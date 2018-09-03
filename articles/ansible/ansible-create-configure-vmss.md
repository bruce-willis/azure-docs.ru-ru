---
title: Создание масштабируемых наборов виртуальных машин в Azure с помощью Ansible
description: Сведения об использовании Ansible для создания и настройки масштабируемого набора виртуальных машин в Azure
ms.service: ansible
keywords: ansible, azure, devops, bash, playbook, virtual machine, virtual machine scale set, vmss
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 08/24/2018
ms.openlocfilehash: f3b08c41d3bf083c7cca5897cee11a1a4b9c9092
ms.sourcegitcommit: ebb460ed4f1331feb56052ea84509c2d5e9bd65c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/24/2018
ms.locfileid: "42918581"
---
# <a name="create-virtual-machine-scale-sets-in-azure-using-ansible"></a>Создание масштабируемых наборов виртуальных машин в Azure с помощью Ansible
Ansible позволяет автоматизировать развертывание и настройку ресурсов в среде. Ansible можно использовать для управления масштабируемым набором виртуальных машин (VMSS) в Azure так же, как любым другим ресурсом Azure. В этой статье показано, как создать и развернуть масштабируемый набор виртуальных машин с помощью Ansible. 

## <a name="prerequisites"></a>Предварительные требования
- **Подписка Azure.** Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio), прежде чем начинать работу.
- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]

> [!Note]
> Для выполнения примеров сборников схем в этом руководстве требуется Ansible 2.6. 

## <a name="create-a-vmss"></a>Создание VMSS
В этом разделе представлен пример сборника схем Ansible, который определяет следующие ресурсы:
- **Группа ресурсов**, в которую будут развернуты все ваши ресурсы.
- **Виртуальная сеть** в адресном пространстве 10.0.0.0/16.
- **Подсеть** в виртуальной сети.
- **Общедоступный IP-адрес**, который позволяет получить доступ к ресурсам через Интернет.
- **Группа безопасности сети**, которая контролирует передачу исходящего и входящего трафика масштабируемого набора виртуальных машин.
- **Подсистема балансировки нагрузки**, которая распределяет трафик в наборе определенных виртуальных машин с помощью правил подсистемы балансировки нагрузки.
- **Масштабируемый набор виртуальных машин**, который использует все созданные ресурсы.

Введите свой пароль для значения *admin_password*.

  ```yaml
  - hosts: localhost
    vars:
      resource_group: myResourceGroup
      vmss_name: myVMSS
      vmss_lb_name: myVMSSlb
      location: eastus
      admin_username: azureuser
      admin_password: "your_password"
    tasks:
      - name: Create a resource group
        azure_rm_resourcegroup:
          name: "{{ resource_group }}"
          location: "{{ location }}"
      - name: Create virtual network
        azure_rm_virtualnetwork:
          resource_group: "{{ resource_group }}"
          name: "{{ vmss_name }}"
          address_prefixes: "10.0.0.0/16"
      - name: Add subnet
        azure_rm_subnet:
          resource_group: "{{ resource_group }}"
          name: "{{ vmss_name }}"
          address_prefix: "10.0.1.0/24"
          virtual_network: "{{ vmss_name }}"
      - name: Create public IP address
        azure_rm_publicipaddress:
          resource_group: "{{ resource_group }}"
          allocation_method: Static
          name: "{{ vmss_name }}"
      - name: Create Network Security Group that allows SSH
        azure_rm_securitygroup:
          resource_group: "{{ resource_group }}"
          name: "{{ vmss_name }}"
          rules:
            - name: SSH
              protocol: Tcp
              destination_port_range: 22
              access: Allow
              priority: 1001
              direction: Inbound

      - name: Create a load balancer
        azure_rm_loadbalancer:
          name: "{{ vmss_lb_name }}"
          location: "{{ location }}"
          resource_group: "{{ resource_group }}"
          public_ip: "{{ vmss_name }}"
          probe_protocol: Tcp
          probe_port: 8080
          probe_interval: 10
          probe_fail_count: 3
          protocol: Tcp
          load_distribution: Default
          frontend_port: 80
          backend_port: 8080
          idle_timeout: 4
          natpool_frontend_port_start: 50000
          natpool_frontend_port_end: 50040
          natpool_backend_port: 22
          natpool_protocol: Tcp

      - name: Create VMSS
        azure_rm_virtualmachine_scaleset:
          resource_group: "{{ resource_group }}"
          name: "{{ vmss_name }}"
          vm_size: Standard_DS1_v2
          admin_username: "{{ admin_username }}"
          admin_password: "{{ admin_password }}"
          ssh_password_enabled: true
          capacity: 2
          virtual_network_name: "{{ vmss_name }}"
          subnet_name: "{{ vmss_name }}"
          upgrade_policy: Manual
          tier: Standard
          managed_disk_type: Standard_LRS
          os_disk_caching: ReadWrite
          image:
            offer: UbuntuServer
            publisher: Canonical
            sku: 16.04-LTS
            version: latest
          load_balancer: "{{ vmss_lb_name }}"
          data_disks:
            - lun: 0
              disk_size_gb: 20
              managed_disk_type: Standard_LRS
              caching: ReadOnly
            - lun: 1
              disk_size_gb: 30
              managed_disk_type: Standard_LRS
              caching: ReadOnly
  ```

Чтобы создать среду масштабируемого набора виртуальных машин с помощью Ansible, сохраните сборник схем как `vmss-create.yml` или [загрузите пример сборника схем Ansible](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss/vmss-create.yml).

Чтобы запустить сборник схем Ansible, используйте команду **ansible-playbook** следующим образом:

  ```bash
  ansible-playbook vmss-create.yml
  ```

После запуска сборника схем выходные данные, аналогичные следующему примеру, показывают, что масштабируемый набор виртуальных машин был успешно создан:

  ```bash
  PLAY [localhost] ***********************************************************

  TASK [Gathering Facts] *****************************************************
  ok: [localhost]

  TASK [Create a resource group] ****************************************************************************
  changed: [localhost]

  TASK [Create virtual network] ****************************************************************************
  changed: [localhost]

  TASK [Add subnet] **********************************************************
  changed: [localhost]

  TASK [Create public IP address] ****************************************************************************
  changed: [localhost]

  TASK [Create Network Security Group that allows SSH] ****************************************************************************
  changed: [localhost]

  TASK [Create a load balancer] ****************************************************************************
  changed: [localhost]

  TASK [Create VMSS] *********************************************************
  changed: [localhost]

  PLAY RECAP *****************************************************************
  localhost                  : ok=8    changed=7    unreachable=0    failed=0

  ```

## <a name="scale-out-a-vmss"></a>Развертывание VMSS
Созданный масштабируемый набор виртуальных машин имеет два экземпляра. Если вы перейдете к масштабируемому набору виртуальных машин на портале Azure, вы увидите следующие сведения: **Standard_DS1_v2 (2 instances)** (Standard_DS1_v2 (2 экземпляра)). Вы также можете использовать [Azure Cloud Shell](https://shell.azure.com/), выполнив следующую команду в Cloud Shell:

  ```azurecli-interactive
  az vmss show -n myVMSS -g myResourceGroup --query '{"capacity":sku.capacity}' 
  ```

Отобразятся похожие результаты:

  ```bash
  {
    "capacity": 2,
  }
  ```

Теперь давайте увеличим количество экземпляров с двух до трех. Следующий код сборника схем Ansible извлекает информацию о масштабируемом наборе виртуальных машин и изменяет его емкость с двух до трех. 

  ```yaml
  - hosts: localhost
    vars:
      resource_group: myResourceGroup
      vmss_name: myVMSS
    tasks: 
      - name: Get scaleset info
        azure_rm_virtualmachine_scaleset_facts:
          resource_group: "{{ resource_group }}"
          name: "{{ vmss_name }}"
          format: curated
        register: output_scaleset

      - name: Dump scaleset info
        debug:
          var: output_scaleset

      - name: Modify scaleset (change the capacity to 3)
        set_fact:
          body: "{{ output_scaleset.ansible_facts.azure_vmss[0] | combine({'capacity': 3}, recursive=True) }}"

      - name: Update something in that VMSS
        azure_rm_virtualmachine_scaleset: "{{ body }}"
  ```

Чтобы развернуть созданный масштабируемый набор виртуальных машин, сохраните предыдущий сборник схем как `vmss-scale-out.yml` или [загрузите пример сборника схем Ansible](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss/vmss-scale-out.yml). 

Запустить сборник схем можно с помощью следующей команды:

  ```bash
  ansible-playbook vmss-scale-out.yml
  ```

Выходные данные запуска сборника схем Ansible показывают, что масштабируемый набор виртуальных машин был успешно развернут:

  ```bash
  PLAY [localhost] **********************************************************

  TASK [Gathering Facts] ****************************************************
  ok: [localhost]

  TASK [Get scaleset info] ***************************************************************************
  ok: [localhost]

  TASK [Dump scaleset info] ***************************************************************************
  ok: [localhost] => {
      "output_scaleset": {
          "ansible_facts": {
              "azure_vmss": [
                  {
                      ......
                  }
              ]
          },
          "changed": false,
          "failed": false
      }
  }

  TASK [Modify scaleset (set upgradePolicy to Automatic and capacity to 3)] ***************************************************************************
  ok: [localhost]

  TASK [Update something in that VMSS] ***************************************************************************
  changed: [localhost]

  PLAY RECAP ****************************************************************
  localhost                  : ok=5    changed=1    unreachable=0    failed=0
  ```

При переходе к настроенному масштабируемому набору виртуальных машин на портале Azure вы увидите следующие сведения: **Standard_DS1_v2 (3 instances)** (Standard_DS1_v2 ( 3 экземпляра)). Вы также можете проверить изменение с помощью [Azure Cloud Shell](https://shell.azure.com/), выполнив следующую команду:

  ```azurecli-interactive
  az vmss show -n myVMSS -g myResourceGroup --query '{"capacity":sku.capacity}' 
  ```

В результатах выполнения команды в Cloud Shell показано, что теперь существует три экземпляра. 

  ```bash
  {
    "capacity": 3,
  }
  ```

## <a name="next-steps"></a>Дополнительная информация
> [!div class="nextstepaction"] 
> [Пример сборника схем Ansible для масштабируемого набора виртуальных машин](https://github.com/Azure-Samples/ansible-playbooks/tree/master/vmss)