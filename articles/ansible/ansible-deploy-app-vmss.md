---
title: Развертывание приложений в масштабируемых наборах виртуальных машин в Azure c помощью Ansible
description: Узнайте, как настроить масштабируемый набор виртуальных машин и развернуть в нем приложение в Azure
ms.service: ansible
keywords: ansible, azure, devops, bash, playbook, virtual machine, virtual machine scale set, vmss
author: tomarcher
manager: jpconnock
editor: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.date: 07/11/2018
ms.author: tarcher
ms.openlocfilehash: b9c8058606e13c0db4908530e98cddb69d2caf50
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/13/2018
ms.locfileid: "39011502"
---
# <a name="deploy-applications-to-virtual-machine-scale-sets-in-azure-using-ansible"></a>Развертывание приложений в масштабируемых наборах виртуальных машин в Azure c помощью Ansible
Ansible позволяет автоматизировать развертывание и настройку ресурсов в среде. Вы можете использовать Ansible для развертывания приложений в Azure. В этой статье показано, как развернуть приложение Java в масштабируемом наборе виртуальных машин (VMSS) Azure.  

## <a name="prerequisites"></a>Предварительные требования
- **Подписка Azure.** Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio), прежде чем начинать работу.
- **Настройка Ansible** - [Создание учетных данных Azure](../virtual-machines/linux/ansible-install-configure.md#create-azure-credentials).
- **Модули Ansible и пакета SDK для Python в Azure** 
  - [CentOS 7.4](../virtual-machines/linux/ansible-install-configure.md#centos-74)
  - [Ubuntu 16.04 LTS](../virtual-machines/linux/ansible-install-configure.md#ubuntu-1604-lts)
  - [SLES 12 SP2](../virtual-machines/linux/ansible-install-configure.md#sles-12-sp2)
- **Масштабируемый набор виртуальных машин**. Если у вас еще нет масштабируемого набора виртуальных машин, вы можете [создать его с помощью Ansible](ansible-create-configure-vmss.md). 
- **git** - [git](https://git-scm.com) используется для скачивания примеров Java, которые есть в этом руководстве.
- **Пакет SDK для Java SE (JDK)**. JDK используется для создания примера проекта Java.
- **Средства сборки Apache Maven**[Средства сборки Apache Maven](https://maven.apache.org/download.cgi) используются для создания примера проекта Java.

> [!Note]
> Для выполнения примеров сборников схем в этом руководстве требуется Ansible 2.6. 

## <a name="get-host-information"></a>Получение сведений об узле

В этом разделе показано, как использовать Ansible для получения сведений об узле для группы виртуальных машин Azure. Ниже приведен пример сборника схем Ansible. Код получает общедоступные IP-адреса и подсистему балансировки нагрузки в указанной группе ресурсов и создает группу узлов с именем **saclesethosts** в списке. 

Сохраните следующий пример сборника схем как `get-hosts-tasks.yml`: 

  ```yaml
  - name: Get facts for all Public IPs within a resource groups
    azure_rm_publicipaddress_facts:
      resource_group: "{{ resource_group }}"
    register: output_ip_address

  - name: Get loadbalancer info
    azure_rm_loadbalancer_facts:
      resource_group: "{{ resource_group }}"
      name: "{{ loadbalancer_name }}"
    register: output

  - name: Add all hosts
    add_host:
      groups: scalesethosts
      hostname: "{{ output_ip_address.ansible_facts.azure_publicipaddresses[0].properties.ipAddress }}_{{ item.properties.frontendPort }}"
      ansible_host: "{{ output_ip_address.ansible_facts.azure_publicipaddresses[0].properties.ipAddress }}"
      ansible_port: "{{ item.properties.frontendPort }}"
      ansible_ssh_user: "{{ admin_username }}"
      ansible_ssh_pass: "{{ admin_password }}"
    with_items:
      - "{{ output.ansible_facts.azure_loadbalancers[0].properties.inboundNatRules }}"
  ```

## <a name="prepare-an-application-for-deployment"></a>Подготовка приложения для развертывания  

В этом разделе вы используете git для клонирования примера проекта Java из GitHub и выполните сборку проекта. Сохраните следующий сборник схем как `app.yml`:

  ```yaml
  - hosts: localhost
    vars:
      repo_url: https://github.com/spring-guides/gs-spring-boot.git
      workspace: ~/src/helloworld

    tasks: 
    - name: Git Clone sample app
      git:
        repo: "{{ repo_url }}"
        dest: "{{ workspace }}"

    - name: Build sample app
      shell: mvn package chdir="{{ workspace }}/complete"
  ```

Запустите пример сборника схем Ansible, выполнив следующую команду:

  ```bash
  ansible-playbook app.yml
  ```

Вывод команды ansible-playbook примерно такой, как показано ниже (создан пример приложения, клонированный с GitHub):

  ```bash
  PLAY [localhost] **********************************************************

  TASK [Gathering Facts] ****************************************************
  ok: [localhost]

  TASK [Git Clone sample app] ***************************************************************************
  changed: [localhost]

  TASK [Build sample app] ***************************************************
  changed: [localhost]

  PLAY RECAP ***************************************************************************
  localhost                  : ok=3    changed=2    unreachable=0    failed=0

  ```

## <a name="deploy-the-application-to-vmss"></a>Развертывание приложения в VMSS

В следующем разделе сборника схем Ansible устанавливается JRE (Java Runtime Environment) в группе узлов с именем **saclesethosts** и развертывается приложение Java в группе узлов с именем **saclesethosts**: 

(Измените `admin_password` на свой пароль).

  ```yaml
  - hosts: localhost
    vars:
      resource_group: myResourceGroup
      scaleset_name: myVMSS
      loadbalancer_name: myVMSSlb
      admin_username: azureuser
      admin_password: "your_password"
    tasks:   
    - include: get-hosts-tasks.yml

  - name: Install JRE on VMSS
    hosts: scalesethosts
    become: yes
    vars:
      workspace: ~/src/helloworld
      admin_username: azureuser

    tasks:
    - name: Install JRE
      apt:
        name: default-jre
        update_cache: yes

    - name: Copy app to Azure VM
      copy:
        src: "{{ workspace }}/complete/target/gs-spring-boot-0.1.0.jar"
        dest: "/home/{{ admin_username }}/helloworld.jar"
        force: yes
        mode: 0755

    - name: Start the application
      shell: java -jar "/home/{{ admin_username }}/helloworld.jar" >/dev/null 2>&1 &
      async: 5000
      poll: 0
  ```

Вы можете сохранить предыдущий пример сборника схем Ansible как `vmss-setup-deploy.yml` или [загрузить весь пример сборника схем](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss). 

Чтобы использовать тип SSH-подключения с паролями, необходимо установить программу sshpass. 
  - Для Ubunto версии 16.04 выполните команду `apt-get install sshpass`.
  - Для CentOS версии 7.4 выполните команду `yum install sshpass`.

Могут появиться следующие сообщения об ошибке. **Использование пароля SSH вместо ключа невозможно, так как проверка ключа узла включена и sshpass не поддерживает ее.  Добавьте отпечаток узла в файл known_hosts для управления этим узлом.** Если вы видите эту ошибку, можно отключить проверку ключа узла, добавив следующую строку в файл `/etc/ansible/ansible.cfg` или `~/.ansible.cfg`:
  ```bash
  [defaults]
  host_key_checking = False
  ```

Выполните следующую команду, чтобы запустить сборник схем:

  ```bash
  ansible-playbook vmss-setup-deploy.yml
  ```

Результат выполнения команды ansible-playbook указывает, что пример приложения Java установлен в группу узлов масштабируемого набора виртуальных машин:

  ```bash
  PLAY [localhost] **********************************************************

  TASK [Gathering Facts] ****************************************************
  ok: [localhost]

  TASK [Get facts for all Public IPs within a resource groups] **********************************************
  ok: [localhost]

  TASK [Get loadbalancer info] ****************************************************************************
  ok: [localhost]

  TASK [Add all hosts] *****************************************************************************
  changed: [localhost] ...

  PLAY [Install JRE on VMSS] *****************************************************************************

  TASK [Gathering Facts] *****************************************************************************
  ok: [40.114.30.145_50000]
  ok: [40.114.30.145_50003]

  TASK [Copy app to Azure VM] *****************************************************************************
  changed: [40.114.30.145_50003]
  changed: [40.114.30.145_50000]

  TASK [Start the application] ********************************************************************
  changed: [40.114.30.145_50000]
  changed: [40.114.30.145_50003]

  PLAY RECAP ************************************************************************************************
  40.114.30.145_50000        : ok=4    changed=3    unreachable=0    failed=0
  40.114.30.145_50003        : ok=4    changed=3    unreachable=0    failed=0
  localhost                  : ok=4    changed=1    unreachable=0    failed=0
  ```

Поздравляем! Теперь приложение работает в Azure. Теперь можно перейти по URL-адресу подсистемы балансировки нагрузки для масштабируемого набора виртуальных машин:

![Приложение Java, работающее в масштабируемом наборе виртуальных машин на портале Azure.](media/ansible-deploy-app-vmss/ansible-deploy-app-vmss.png)

## <a name="next-steps"></a>Дополнительная информация
> [!div class="nextstepaction"] 
> [Пример сборника схем Ansible для масштабируемого набора виртуальных машин](https://github.com/Azure-Samples/ansible-playbooks/tree/master/vmss)