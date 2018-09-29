---
title: Управление виртуальной машиной Linux в Azure с помощью Ansible
description: Узнайте, как управлять виртуальной машиной Linux в Azure с помощью Ansible
ms.service: ansible
keywords: ansible, azure, devops, bash, cloudshell, сборник тренировочных заданий, bash
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.topic: quickstart
ms.date: 09/27/2018
ms.openlocfilehash: d9ff0387a1d02eb5f4684048aeed8ad0079b28ef
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/28/2018
ms.locfileid: "47434421"
---
# <a name="use-ansible-to-manage-a-linux-virtual-machine-in-azure"></a>Управление виртуальной машиной Linux в Azure с помощью Ansible
Ansible позволяет автоматизировать развертывание и настройку ресурсов в среде. Ansible можно использовать для управления виртуальными машинами Azure так же, как любым другим ресурсом. В этой статье описано, как запустить и остановить виртуальные машины Linux с помощью сборника схем Ansible. 

## <a name="prerequisites"></a>Предварительные требования

- **Подписка Azure**. Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]

## <a name="use-ansible-to-deallocate-stop-an-azure-virtual-machine"></a>Завершение общения (остановка) виртуальной машины Azure с помощью Ansible
В этом разделе описано, как завершить общение виртуальной машины Azure с помощью Ansible.

1.  Войдите на [портале Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1.  Откройте [Cloud Shell](/azure/cloud-shell/overview).

1.  Создайте файл (для хранения сборника схем) под названием `azure-vm-stop.yml` и откройте его в редакторе VI следующим образом:

    ```azurecli-interactive
    vi azure-vm-stop.yml
    ```

1.  Нажмите ключ **I**, чтобы войти в режим вставки.

1.  Вставьте следующий пример кода в редактор.

    ```yaml
    - name: Stop Azure VM
      hosts: localhost
      connection: local
      tasks:
      - name: Deallocate the virtual machine
        azure_rm_virtualmachine:
          resource_group: myResourceGroup
          name: myVM
          allocated: no
    ```

1.  Выйдите из режима вставки, нажав клавишу **Esc**.

1.  Сохраните файл и закройте редактор VI. Для этого введите приведенную ниже команду.

    ```bash
    :wq
    ```

1.  Запустите пример сборника схем Ansible.

    ```bash
    ansible-playbook azure-vm-stop.yml
    ```

1.  Ниже приведен пример выходных данных, в котором показано, что общение виртуальной машины успешно завершено:

    ```bash
    PLAY [Stop Azure VM] ********************************************************

    TASK [Gathering Facts] ******************************************************
    ok: [localhost]

    TASK [Deallocate the Virtual Machine] ***************************************
    changed: [localhost]

    PLAY RECAP ******************************************************************
    localhost                  : ok=2    changed=1    unreachable=0    failed=0
    ```

## <a name="use-ansible-to-start-a-deallocated-stopped-azure-virtual-machine"></a>Запуск виртуальной машины Azure, для которой было остановлено общение, с помощью Ansible
В этом разделе описано, как с помощью Ansible запустить виртуальную машину Azure, для которой было остановлено общение.

1.  Войдите на [портале Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1.  Откройте [Cloud Shell](/azure/cloud-shell/overview).

1.  Создайте файл (для хранения сборника схем) под названием `azure-vm-start.yml` и откройте его в редакторе VI следующим образом:

    ```azurecli-interactive
    vi azure-vm-start.yml
    ```

1.  Нажмите ключ **I**, чтобы войти в режим вставки.

1.  Вставьте следующий пример кода в редактор.

    ```yaml
    - name: Start Azure VM
      hosts: localhost
      connection: local
      tasks:
      - name: Start the virtual machine
        azure_rm_virtualmachine:
          resource_group: myResourceGroup
          name: myVM
    ```

1.  Выйдите из режима вставки, нажав клавишу **Esc**.

1.  Сохраните файл и закройте редактор VI. Для этого введите приведенную ниже команду.

    ```bash
    :wq
    ```

1.  Запустите пример сборника схем Ansible.

    ```bash
    ansible-playbook azure-vm-start.yml
    ```

1.  Ниже приведен пример выходных данных, в котором показано, что запуск виртуальной машины успешно выполнен:

    ```bash
    PLAY [Start Azure VM] ********************************************************

    TASK [Gathering Facts] ******************************************************
    ok: [localhost]

    TASK [Start the Virtual Machine] ********************************************
    changed: [localhost]

    PLAY RECAP ******************************************************************
    localhost                  : ok=2    changed=1    unreachable=0    failed=0
    ```

## <a name="next-steps"></a>Дополнительная информация
> [!div class="nextstepaction"] 
> [Управление динамическими списками Azure с помощью Ansible](/articles/ansible/ansible-manage-azure-dynamic-inventories)