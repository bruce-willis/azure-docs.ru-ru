---
title: Создание и настройка кластеров службы Azure Kubernetes в Azure с помощью Ansible
description: Узнайте, как использовать Ansible для создания кластеров службы Azure Kubernetes и управления ими в Azure.
ms.service: ansible
keywords: ansible, azure, devops, bash, cloudshell, сборник схем, aks, контейнер, Kubernetes
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 08/21/2018
ms.openlocfilehash: de692b29902145e44a055680d662c16ed90c56c2
ms.sourcegitcommit: a62cbb539c056fe9fcd5108d0b63487bd149d5c3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/22/2018
ms.locfileid: "42617181"
---
# <a name="create-and-configure-azure-kubernetes-service-clusters-in-azure-using-ansible"></a>Создание и настройка кластеров службы Azure Kubernetes в Azure с помощью Ansible
Ansible позволяет автоматизировать развертывание и настройку ресурсов в среде. Ansible можно использовать для управления службой Azure Kubernetes (AKS). В этой статье приводятся сведения об использовании Ansible для создания и настройки кластера службы Azure Kubernetes.

## <a name="prerequisites"></a>Предварительные требования
- **Подписка Azure.** Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio), прежде чем начинать работу.
- **Субъект-служба Azure**. При [создании субъекта-службы](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest#create-the-service-principal) обратите внимание на следующие значения: **appId**, **displayName**, **password** и **tenant**.

- **Настройте Azure Cloud Shell** или **установите и настройте Ansible на виртуальной машине Linux**

  **Настройте Azure Cloud Shell**

  1. **Настройте Azure Cloud Shell**. Если вы еще не работали с Azure Cloud Shell, см. [краткое руководство по Bash в Azure Cloud Shell](/azure/cloud-shell/quickstart), в котором объясняется, как запустить и настроить Cloud Shell. 

  **--или--**

  **Установите и настройте Ansible на виртуальной машине Linux**

  1. **Установите Ansible.** Установите Ansible на [поддерживаемую платформу Linux](/azure/virtual-machines/linux/ansible-install-configure#install-ansible-on-an-azure-linux-virtual-machine).

  1. **Настройка Ansible** - [Создание учетных данных Azure](/azure/virtual-machines/linux/ansible-install-configure#create-azure-credentials).

> [!Note]
> Для выполнения примеров сборников схем в этом руководстве требуется Ansible 2.6. 

## <a name="create-a-managed-aks-cluster"></a>Создание управляемого кластера AKS
В следующем примере сборника схем Ansible создается группа ресурсов и кластер AKS, который находится в группе ресурсов:

  ```yaml
  - name: Create Azure Kubernetes Service
    hosts: localhost
    connection: local
    vars:
      resource_group: myResourceGroup
      location: eastus
      aks_name: myAKSCluster
      username: azureuser
      ssh_key: "your_ssh_key"
      client_id: "your_client_id"
      client_secret: "your_client_secret"
    tasks:
    - name: Create resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"
    - name: Create a managed Azure Container Services (AKS) cluster
      azure_rm_aks:
        name: "{{ aks_name }}"
        location: "{{ location }}"
        resource_group: "{{ resource_group }}"
        dns_prefix: "{{ aks_name }}"
        linux_profile:
          admin_username: "{{ username }}"
          ssh_key: "{{ ssh_key }}"
        service_principal:
          client_id: "{{ client_id }}"
          client_secret: "{{ client_secret }}"
        agent_pool_profiles:
          - name: default
            count: 2
            vm_size: Standard_D2_v2
        tags:
          Environment: Production
  ```

Следующие маркеры поясняют предыдущий код сборника схем Ansible:
- В первом разделе в **tasks** определяется группа ресурсов с именем **myResourceGroup** в расположении **eastus**. 
- Во втором разделе в **tasks** определяется кластер AKS с именем **myAKSCluster** в группе ресурсов **myResourceGroup**. 

Чтобы создать кластер AKS с помощью Ansible, сохраните предыдущий пример сборника схем как `azure_create_aks.yml` и запустите сборник схем, выполнив следующую команду:

  ```bash
  ansible-playbook azure_create_aks.yml
  ```

Результат выполнения команды **ansible-playbook* будет аналогичен приведенному ниже — в нем отображается успешное создание кластера AKS:

  ```bash
  PLAY [Create AKS] ****************************************************************************************

  TASK [Gathering Facts] ********************************************************************************************
  ok: [localhost]

  TASK [Create resource group] **************************************************************************************
  changed: [localhost]

  TASK [Create a Azure Container Services (AKS) cluster] ***************************************************
  changed: [localhost]

  PLAY RECAP *********************************************************************************************************
  localhost                  : ok=3    changed=2    unreachable=0    failed=0
  ```

## <a name="scale-aks-nodes"></a>Масштабирование узлов AKS

Сборник схем из предыдущего раздела определяет два узла. Если вам требуется больше или меньше рабочих нагрузок контейнеров в кластере, вы можете легко изменить количество узлов. В этом разделе тот же сборник схем увеличивает количество узлов с двух до трех. Изменение количества узлов выполняется путем изменения значения **count** в блоке **agent_pool_profiles**. 

В блоке **service_principal** введите собственные значения `ssh_key`, `client_id` и `client_secret`:

```yaml
- name: Scale AKS cluster
  hosts: localhost
  connection: local
  vars:
    resource_group: myResourceGroup
    location: eastus
    aks_name: myAKSCluster
    username: azureuser
    ssh_key: "your_ssh_key"
    client_id: "your_client_id"
    client_secret: "your_client_secret"
  tasks:
  - name: Scaling an existed AKS cluster
    azure_rm_aks:
        name: "{{ aks_name }}"    
        location: "{{ location }}"
        resource_group: "{{ resource_group }}" 
        dns_prefix: "{{ aks_name }}" 
        linux_profile:
          admin_username: "{{ username }}"
          ssh_key: "{{ ssh_key }}"
        service_principal:
          client_id: "{{ client_id }}"
          client_secret: "{{ client_secret }}"
        agent_pool_profiles:
          - name: default
            count: 3
            vm_size: Standard_D2_v2
```

Для масштабирования кластера службы Azure Kubernetes с помощью Ansible сохраните предыдущий сборник схем как *azure_configure_aks.yml* и запустите сборник схем:

  ```bash
  ansible-playbook azure_configure_aks.yml
  ```

В следующих выходных данных демонстрируется успешное создание кластера AKS:

  ```bash
  PLAY [Scale AKS cluster] ***************************************************************

  TASK [Gathering Facts] ******************************************************************
  ok: [localhost]

  TASK [Scaling an existed AKS cluster] **************************************************
  changed: [localhost]

  PLAY RECAP ******************************************************************************
  localhost                  : ok=2    changed=1    unreachable=0    failed=0
  ```
## <a name="delete-a-managed-aks-cluster"></a>Удаление управляемого кластера AKS

В следующем разделе примера сборника схем Ansible описано, как удалить кластер AKS:

  ```yaml
  - name: Delete a managed Azure Container Services (AKS) cluster
    hosts: localhost
    connection: local
    vars:
      resource_group: myResourceGroup
      aks_name: myAKSCluster
    tasks:
    - name: 
      azure_rm_aks:
        name: "{{ aks_name }}"
        resource_group: "{{ resource_group }}"
        state: absent
   ```

Чтобы удалить кластер Службы Azure Kubernetes с помощью Ansible, сохраните предыдущий сборник схем как файл *azure_configure_aks.yml* и запустите его:

  ```bash
  ansible-playbook azure_delete_aks.yml
  ```

В следующих выходных данных показано успешное удаление кластера AKS:
  ```bash
PLAY [Delete a managed Azure Container Services (AKS) cluster] ****************************

TASK [Gathering Facts] ********************************************************************
ok: [localhost]

TASK [azure_rm_aks] *********************************************************************

PLAY RECAP *********************************************************************
localhost                  : ok=2    changed=1    unreachable=0    failed=0
  ```
  
## <a name="next-steps"></a>Дополнительная информация
> [!div class="nextstepaction"] 
> [Руководство. Масштабирование приложения в службе Azure Kubernetes (AKS)](https://docs.microsoft.com/azure/aks/tutorial-kubernetes-scale)
