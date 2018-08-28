---
title: Управление виртуальной машиной Linux в Azure с помощью Ansible
description: Узнайте, как управлять виртуальной машиной Linux в Azure с помощью Ansible
ms.service: ansible
keywords: ansible, azure, devops, bash, cloudshell, сборник тренировочных заданий, bash
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.topic: quickstart
ms.date: 08/21/2018
ms.openlocfilehash: 66106346b298fae22cce47081916a6c8eec8fd40
ms.sourcegitcommit: 76797c962fa04d8af9a7b9153eaa042cf74b2699
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/21/2018
ms.locfileid: "40250675"
---
# <a name="use-ansible-to-manage-a-linux-virtual-machine-in-azure"></a>Управление виртуальной машиной Linux в Azure с помощью Ansible
Ansible позволяет автоматизировать развертывание и настройку ресурсов в среде. Ansible можно использовать для управления виртуальными машинами Azure так же, как любым другим ресурсом. В этой статье описано, как запустить и остановить виртуальные машины Linux с помощью сборника схем Ansible. 

## <a name="prerequisites"></a>Предварительные требования

- **Подписка Azure.** Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

- **Настройте Azure Cloud Shell** или **установите и настройте Ansible на виртуальной машине Linux**

  **Настройте Azure Cloud Shell**

  1. **Настройте Azure Cloud Shell.** Если вы еще не работали с Azure Cloud Shell, см. статью [Краткое руководство по Bash в Azure Cloud Shell](/azure/cloud-shell/quickstart), в которой объясняется, как запустить и настроить Cloud Shell. 

  1. **Виртуальная машина Linux.** Вы можете [создать виртуальную машину Linux с помощью Ansible](ansible-create-vm.md), если у вас нет доступа к ней.

  **--ИЛИ--**

  **Установите и настройте Ansible на виртуальной машине Linux**

  1. **Установите Ansible.** Установите Ansible на [поддерживаемую платформу Linux](/azure/virtual-machines/linux/ansible-install-configure#install-ansible-on-an-azure-linux-virtual-machine).

  1. **Настройка Ansible** - [Создание учетных данных Azure](/azure/virtual-machines/linux/ansible-install-configure#create-azure-credentials).

## <a name="use-ansible-to-deallocate-stop-an-azure-virtual-machine"></a>Завершение общения (остановка) виртуальной машины Azure с помощью Ansible
В этом разделе описано, как завершить общение виртуальной машины Azure с помощью Ansible.

1. Войдите на [портале Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Откройте [Cloud Shell](/azure/cloud-shell/overview).

1. Создайте файл (для хранения сборника схем) под названием `azure_vm_stop.yml` и откройте его в редакторе VI следующим образом:

  ```azurecli-interactive
  vi azure_vm_stop.yml
  ```

1. Нажмите ключ **I**, чтобы войти в режим вставки.

1. Вставьте следующий пример кода в редактор.

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

1. Выйдите из режима вставки, нажав клавишу **Esc**.

1. Сохраните файл и закройте редактор VI. Для этого введите приведенную ниже команду.

    ```bash
    :wq
    ```

1. Запустите пример сборника схем Ansible.

  ```bash
  ansible-playbook azure_vm_stop.yml
  ```

1. Ниже приведен пример выходных данных, в котором показано, что общение виртуальной машины успешно завершено:

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

1. Войдите на [портале Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Откройте [Cloud Shell](/azure/cloud-shell/overview).

1. Создайте файл (для хранения сборника схем) под названием `azure_vm_start.yml` и откройте его в редакторе VI следующим образом:

  ```azurecli-interactive
  vi azure_vm_start.yml
  ```

1. Нажмите ключ **I**, чтобы войти в режим вставки.

1. Вставьте следующий пример кода в редактор.

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

1. Выйдите из режима вставки, нажав клавишу **Esc**.

1. Сохраните файл и закройте редактор VI. Для этого введите приведенную ниже команду.

    ```bash
    :wq
    ```

1. Запустите пример сборника схем Ansible.

  ```bash
  ansible-playbook azure_vm_start.yml
  ```

1. Ниже приведен пример выходных данных, в котором показано, что запуск виртуальной машины успешно выполнен:

    Ниже приведен пример выходных данных, в котором показано, что запуск виртуальной машины успешно выполнен:

    ```bash
    PLAY [Stop Azure VM] ********************************************************

    TASK [Gathering Facts] ******************************************************
    ok: [localhost]

    TASK [Start the Virtual Machine] ********************************************
    changed: [localhost]

    PLAY RECAP ******************************************************************
    localhost                  : ok=2    changed=1    unreachable=0    failed=0
    ```

## <a name="next-steps"></a>Дополнительная информация
> [!div class="nextstepaction"] 
> [Управление динамическими списками Azure с помощью Ansible](../../ansible/ansible-manage-azure-dynamic-inventories.md)