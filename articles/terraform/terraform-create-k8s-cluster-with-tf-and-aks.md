---
title: Создание кластера Kubernetes с помощью службы Azure Kubernetes (AKS) и Terraform
description: В этом руководстве показано, как создать кластер Kubernetes с помощью службы Azure Kubernetes и Terraform
services: terraform
ms.service: terraform
keywords: terraform, devops, virtual machine, azure, kubernetes
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 09/06/2018
ms.openlocfilehash: cd1219fda7821fdc99e334de58826317113415d4
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/07/2018
ms.locfileid: "44053647"
---
# <a name="create-a-kubernetes-cluster-with-azure-kubernetes-service-and-terraform"></a>Создание кластера Kubernetes с помощью службы Azure Kubernetes и Terraform
[Служба Azure Kubernetes (AKS)](/azure/aks/) управляет размещенной средой Kubernetes, позволяя быстро и легко развертывать контейнерные приложения и управлять ими, даже если вы никогда не оркестрировали контейнеры. Также вам не нужно выполнять текущие операции и обслуживание, так как эта служба подготавливает, обновляет и масштабирует ресурсы по требованию, не отключая приложения от сети.

В этом руководстве показано, как выполнять следующие задачи при создании кластера [Kubernetes](https://www.redhat.com/en/topics/containers/what-is-kubernetes) с использованием [Terraform](http://terraform.io) и AKS:

> [!div class="checklist"]
> * определение кластера Kubernetes с помощью языка HCL;
> * создание кластера Kubernetes с помощью AKS и Terraform;
> * проверка доступности кластера Kubernetes с помощью средства kubectl.

## <a name="prerequisites"></a>Предварительные требования

- **Подписка Azure**. Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio), прежде чем начинать работу.

- **Настройка Terraform.** Следуйте указаниям из статьи [Terraform и настройка доступа к Azure](/azure/virtual-machines/linux/terraform-install-configure).

- **Субъект-служба Azure.** Следуйте указаниям, приведенным в разделе **Создание субъекта-службы** статьи [Создание субъекта-службы Azure с помощью Azure CLI 2.0](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest#create-the-service-principal). Запишите значения appId (идентификатор приложения), displayName (отображаемое имя), password (пароль) и tenant (клиент).

## <a name="create-the-directory-structure"></a>Создание структуры каталога
Первый шаг — создание каталога, содержащего файлы конфигурации Terraform для упражнения.

1. Перейдите на [портал Azure](http://portal.azure.com).

1. Откройте [Azure Cloud Shell](/azure/cloud-shell/overview). Если ранее среда не была выбрана, то в качестве среды необходимо выбрать **Bash**.

    ![Командная строка Cloud Shell](./media/terraform-create-k8s-cluster-with-tf-and-aks/azure-portal-cloud-shell-button-min.png)

1. Перейдите в каталог `clouddrive`.

    ```bash
    cd clouddrive
    ```

1. Создайте каталог с именем `terraform-aks-k8s`.

    ```bash
    mkdir terraform-aks-k8s
    ```

1. Перейдите в новый каталог:

    ```bash
    cd terraform-aks-k8s
    ```

## <a name="declare-the-azure-provider"></a>Объявление поставщика Azure
Создайте файл конфигурации Terraform, который объявляет поставщик Azure.

1. В Cloud Shell создайте файл с именем `main.tf`.

    ```bash
    vi main.tf
    ```

1. Нажмите клавишу I, чтобы войти в режим вставки.

1. Скопируйте приведенный ниже код и вставьте его в редактор.

    ```JSON
    provider "azurerm" {
        version = "~>1.5"
    }

    terraform {
        backend "azurerm" {}
    }

    ```

1. Выйдите из режима вставки, нажав клавишу **Esc**.

1. Сохраните файл и закройте редактор VI. Для этого введите приведенную ниже команду.

    ```bash
    :wq
    ```

## <a name="define-a-kubernetes-cluster"></a>Определение кластера Kubernetes
Создайте файл конфигурации Terraform, который объявляет ресурсы для кластера Kubernetes.

1. В Cloud Shell создайте файл с именем `k8s.tf`.

    ```bash
    vi k8s.tf
    ```

1. Нажмите клавишу I, чтобы войти в режим вставки.

1. Скопируйте приведенный ниже код и вставьте его в редактор.

    ```JSON
    resource "azurerm_resource_group" "k8s" {
        name     = "${var.resource_group_name}"
        location = "${var.location}"
    }

    resource "azurerm_kubernetes_cluster" "k8s" {
        name                = "${var.cluster_name}"
        location            = "${azurerm_resource_group.k8s.location}"
        resource_group_name = "${azurerm_resource_group.k8s.name}"
        dns_prefix          = "${var.dns_prefix}"

        linux_profile {
            admin_username = "ubuntu"

            ssh_key {
            key_data = "${file("${var.ssh_public_key}")}"
            }
        }

        agent_pool_profile {
            name            = "default"
            count           = "${var.agent_count}"
            vm_size         = "Standard_DS2_v2"
            os_type         = "Linux"
            os_disk_size_gb = 30
        }

        service_principal {
            client_id     = "${var.client_id}"
            client_secret = "${var.client_secret}"
        }

        tags {
            Environment = "Development"
        }
    }
    ```

    Предыдущий код задает имя кластера (name), расположение (location) и имя группы ресурсов (resource_group_name). Кроме того, задается значение dns_prefix (префикс DNS), которое составляет часть полного доменного имени (FQDN), используемого для доступа к кластеру.

    Запись **linux_profile** позволяет настроить параметры, разрешающие вход на рабочие узлы с использованием SSH.

    С AKS вы платите только за рабочие узлы. Запись **agent_pool_profile** настраивает сведения этих рабочих узлов. **Запись agent_pool_profile** включает в себя количество и тип создаваемых рабочих узлов. Если в будущем потребуется увеличить или уменьшить масштаб кластера, следует изменить значение **count** в этой записи.

1. Выйдите из режима вставки, нажав клавишу **Esc**.

1. Сохраните файл и закройте редактор VI. Для этого введите приведенную ниже команду.

    ```bash
    :wq
    ```

## <a name="declare-the-variables"></a>Объявление переменных

1. В Cloud Shell создайте файл с именем `variables.tf`.

    ```bash
    vi variables.tf
    ```

1. Нажмите клавишу I, чтобы войти в режим вставки.

1. Скопируйте приведенный ниже код и вставьте его в редактор.

    ```JSON
    variable "client_id" {}
    variable "client_secret" {}

    variable "agent_count" {
        default = 3
    }

    variable "ssh_public_key" {
        default = "~/.ssh/id_rsa.pub"
    }

    variable "dns_prefix" {
        default = "k8stest"
    }

    variable cluster_name {
        default = "k8stest"
    }

    variable resource_group_name {
        default = "azure-k8stest"
    }

    variable location {
        default = "Central US"
    }
    ```

1. Выйдите из режима вставки, нажав клавишу **Esc**.

1. Сохраните файл и закройте редактор VI. Для этого введите приведенную ниже команду.

    ```bash
    :wq
    ```

## <a name="create-a-terraform-output-file"></a>Создание выходного файла Terraform
[Выходные данные Terraform](https://www.terraform.io/docs/configuration/outputs.html) позволяют определить значения, которые будут выделяться для пользователя при применении плана Terraform, и их можно запрашивать с помощью команды `terraform output`. В этом разделе создается выходной файл, который обеспечивает доступ к кластеру с помощью [kubectl](https://kubernetes.io/docs/reference/kubectl/overview/).

1. В Cloud Shell создайте файл с именем `output.tf`.

    ```bash
    vi output.tf
    ```

1. Нажмите клавишу I, чтобы войти в режим вставки.

1. Скопируйте приведенный ниже код и вставьте его в редактор.

    ```JSON
    output "client_key" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config.0.client_key}"
    }

    output "client_certificate" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config.0.client_certificate}"
    }

    output "cluster_ca_certificate" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config.0.cluster_ca_certificate}"
    }

    output "cluster_username" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config.0.username}"
    }

    output "cluster_password" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config.0.password}"
    }

    output "kube_config" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config_raw}"
    }

    output "host" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config.0.host}"
    }
    ```

1. Выйдите из режима вставки, нажав клавишу **Esc**.

1. Сохраните файл и закройте редактор VI. Для этого введите приведенную ниже команду.

    ```bash
    :wq
    ```

## <a name="set-up-azure-storage-to-store-terraform-state"></a>Настройка хранилища Azure для хранения данных о состоянии Terraform
Terraform отслеживает состояние локально через файл `terraform.tfstate`. Эта схема хорошо работает в среде с одним пользователем. Однако в более практичной многопользовательской среде необходимо отслеживать состояние на сервере с использованием [хранилища Azure](/azure/storage/). В этом разделе вы получите необходимые сведения об учетной записи хранения (имя и ключ учетной записи) и создадите контейнер хранилища, в котором будут храниться сведения о состоянии Terraform.

1. На портале Azure выберите **Все службы** в меню слева.

1. Выберите **Учетные записи хранения**.

1. На вкладке **Учетные записи хранения** выберите имя учетной записи хранения, в которой будет сохраняться состояние Terraform. Например, можно использовать учетную запись хранения, созданную при открытии Cloud Shell в первый раз.  Имя учетной записи хранения, созданной с помощью Cloud Shell, обычно начинается с `cs`, после чего следует случайная строка из цифр и букв. **Запомните или запишите выбранное имя учетной записи хранения, так как оно понадобится в дальнейшем.**

1. На вкладке учетной записи хранения выберите **Ключи доступа**.

    ![Меню учетной записи хранения](./media/terraform-create-k8s-cluster-with-tf-and-aks/storage-account.png)

1. Запишите значение ключа **key1** **key**. (Если щелкнуть значок справа от ключа, значение будет скопировано в буфер обмена.)

    ![Ключи доступа к учетной записи хранения](./media/terraform-create-k8s-cluster-with-tf-and-aks/storage-account-access-key.png)

1. В Cloud Shell создайте контейнер в учетной записи хранения Azure (замените заполнители &lt;YourAzureStorageAccountName> и &lt;YourAzureStorageAccountAccessKey> соответствующими значениями для учетной записи хранения Azure).

    ```bash
    az storage container create -n tfstate --account-name <YourAzureStorageAccountName> --account-key <YourAzureStorageAccountKey>
    ```

## <a name="create-the-kubernetes-cluster"></a>Создание кластера Kubernetes
В этом разделе описывается использование команды `terraform init` для создания ресурсов, определяемых файлами конфигурации, которые вы создали в предыдущих разделах.

1. В Cloud Shell инициализируйте Terraform (замените заполнители &lt;YourAzureStorageAccountName> и &lt;YourAzureStorageAccountAccessKey> соответствующими значениями для учетной записи хранения Azure).

    ```bash
    terraform init -backend-config="storage_account_name=<YourAzureStorageAccountName>" -backend-config="container_name=tfstate" -backend-config="access_key=<YourStorageAccountAccessKey>" -backend-config="key=codelab.microsoft.tfstate" 
    ```
    
    Команда `terraform init` отображает успешный результат инициализации внутренней части и подключаемого модуля поставщика:

    ![Пример результатов выполнения команды terraform init](./media/terraform-create-k8s-cluster-with-tf-and-aks/terraform-init-complete.png)

1. Выполните команду `terraform plan`, чтобы создать план Terraform, определяющий элементы инфраструктуры. Команда запросит два значения: **var.client_id** и **var.client_secret**. Для переменной **var.client_id** введите значение **appId**, связанное с субъектом-службой. Для переменной **var.client_secret** введите значение **password**, связанное с субъектом-службой.

    ```bash
    terraform plan -out out.plan
    ```

    Команда `terraform plan` отображает ресурсы, которые будут созданы при выполнении команды `terraform apply`:

    ![Пример результатов выполнения команды terraform plan](./media/terraform-create-k8s-cluster-with-tf-and-aks/terraform-plan-complete.png)

1. Выполните команду `terraform apply`, чтобы применить план для создания кластера Kubernetes. Процесс создания кластера Kubernetes может занять несколько минут. В результате может быть превышено время ожидания сеанса Cloud Shell. Если время ожидания сеанса Cloud Shell истечет, можно выполнить действия из раздела [Восстановление после истечения времени ожидания Cloud Shell](#recover-from-a-dloud-shell-timeout), что позволит завершить работу с руководством.

    ```bash
    terraform apply out.plan
    ```

    Команда `terraform apply` отображает результаты создания ресурсов, определенных в файлах конфигурации:

    ![Пример результатов выполнения команды terraform apply](./media/terraform-create-k8s-cluster-with-tf-and-aks/terraform-apply-complete.png)

1. На портале Azure выберите **Все службы** в меню слева, чтобы просмотреть ресурсы, созданные для нового кластера Kubernetes.

    ![Командная строка Cloud Shell](./media/terraform-create-k8s-cluster-with-tf-and-aks/k8s-resources-created.png)

## <a name="recover-from-a-cloud-shell-timeout"></a>Восстановление после истечения времени ожидания Cloud Shell
Если время ожидания сеанса Cloud Shell истечет, можно выполнить следующие действия для восстановления.

1. Запустите сеанс Cloud Shell.

1. Перейдите в каталог, содержащий файлы конфигурации Terraform.

    ```bash
    cd /clouddrive/terraform-aks-k8s
    ```

1. Выполните следующую команду:

    ```bash
    export KUBECONFIG=./azurek8s
    ```
    
## <a name="test-the-kubernetes-cluster"></a>Проверка кластера Kubernetes
Средства Kubernetes позволяют проверить только что созданный кластер.

1. Получите конфигурацию Kubernetes из данных о состоянии Terraform и сохраните ее в файле, который может прочитать средство kubectl.

    ```bash
    echo "$(terraform output kube_config)" > ./azurek8s
    ```

1. Задайте переменную среды, позволяющую kubectl подобрать правильную конфигурацию.

    ```bash
    export KUBECONFIG=./azurek8s
    ```

1. Проверьте работоспособность кластера.

    ```bash
    kubectl get nodes
    ```

    Должны отобразиться сведения о рабочих узлах. У всех узлов должно быть состояние **Готово**, как показано на следующем рисунке:

    ![Средство kubectl позволяет проверить работоспособность кластера Kubernetes](./media/terraform-create-k8s-cluster-with-tf-and-aks/kubectl-get-nodes.png)

## <a name="next-steps"></a>Дополнительная информация
Из этой статьи вы узнали, как использовать Terraform и AKS для создания кластера Kubernetes. Ниже приведены ресурсы, содержащие дополнительные сведения о Terraform в Azure. 

 [Terraform в документации по Azure](https://docs.microsoft.com/azure/terraform/)  
 [Документация по поставщику для Terraform Azure](http://aka.ms/terraform)  
 [Документация по разработке поставщика Terraform Azure](http://aka.ms/tfgit)  
 [Модули Terraform Azure](http://aka.ms/tfmodules)