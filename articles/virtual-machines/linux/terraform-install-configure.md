---
title: Установка и настройка Terraform для использования с Azure | Документация Майкрософт
description: Узнайте, как установить и настроить Terraform для создания ресурсов Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: echuvyrov
manager: jeconnoc
editor: na
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/19/2018
ms.author: echuvyrov
ms.openlocfilehash: 0943bd1bffb3df7beda97ea0619f1aced4ca3a41
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46946788"
---
# <a name="install-and-configure-terraform-to-provision-vms-and-other-infrastructure-into-azure"></a>Установка и настройка Terraform для подготовки виртуальных машин и другой инфраструктуры в Azure
 
Terraform предоставляет простой способ определить, просмотреть и развернуть облачную инфраструктуру, используя [простой настраиваемый язык шаблонов](https://www.terraform.io/docs/configuration/syntax.html). В этой статье описываются шаги, необходимые для подготовки ресурсов Azure с помощью Terraform.

Дополнительные сведения об использовании Terraform с Azure см. в [центре документации по Terraform](/azure/terraform).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Средство Terraform установлено в [Cloud Shell](/azure/terraform/terraform-cloud-shell) по умолчанию. Если вы хотите установить Terraform локально, выполните следующий шаг, в противном случае перейдите к разделу [Настройка доступа Terraform в Azure](#set-up-terraform-access-to-azure).

## <a name="install-terraform"></a>Установка Terraform

Чтобы установить Terraform, [загрузите](https://www.terraform.io/downloads.html) пакет, соответствующий вашей операционной системе, в отдельный каталог установки. Этот пакет содержит один исполняемый файл, для которого также необходимо задать глобальный путь. Инструкции по настройке пути в операционных системах Linux и Mac можно найти на [этой веб-странице](https://stackoverflow.com/questions/14637979/how-to-permanently-set-path-on-linux). Инструкции по настройке пути в Windows можно найти на [этой веб-странице](https://stackoverflow.com/questions/1618280/where-can-i-set-path-to-make-exe-on-windows).

Проверьте конфигурацию пути, выполнив команду `terraform`. Должен отобразиться список доступных параметров Terraform, как в следующем примере выходных данных.

```bash
azureuser@Azure:~$ terraform
Usage: terraform [--version] [--help] <command> [args]
```

## <a name="set-up-terraform-access-to-azure"></a>Настройка доступа Terraform в Azure

Создайте [субъект-службу Azure AD](/cli/azure/create-an-azure-service-principal-azure-cli), чтобы предоставить Terraform возможность подготовить ресурсы в Azure. Субъект-служба предоставляет сценарии Terraform для подготовки ресурсов в подписке Azure.

Если у вас есть несколько подписок Azure, сначала запросите свою учетную запись с помощью команды[az account show](/cli/azure/account#az-account-show), чтобы получить список значений идентификатора подписки и идентификатора клиента:

```azurecli-interactive
az account show --query "{subscriptionId:id, tenantId:tenantId}"
```

Чтобы использовать выбранную подписку, настройте подписку для этого сеанса с помощью команды [az account set](/cli/azure/account#az-account-set). Настройте переменную среды `SUBSCRIPTION_ID`, чтобы она хранила значение возвращаемого поля `id` из подписки, которую необходимо использовать.

```azurecli-interactive
az account set --subscription="${SUBSCRIPTION_ID}"
```

Теперь вы можете создать субъект-службу для использования с Terraform. Используйте [az ad sp create-for-rbac]/cli/azure/ad/sp#az-ad-sp-create-for-rbac) и установите *область* для подписки следующим образом:

```azurecli-interactive
az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/${SUBSCRIPTION_ID}"
```

Будут возвращены `appId`, `password`, `sp_name` и `tenant`. Запишите `appId` и `password`.

## <a name="configure-terraform-environment-variables"></a>Настройка переменных среды Terraform

Чтобы настроить Terraform для использования субъекта-службы Azure AD, задайте следующие переменные среды, которые затем используются [модулями Azure Terraform](https://registry.terraform.io/modules/Azure). Можно также настроить среду, если используется другое облако Azure, а не с общедоступное облако Azure.

- `ARM_SUBSCRIPTION_ID`
- `ARM_CLIENT_ID`
- `ARM_CLIENT_SECRET`
- `ARM_TENANT_ID`
- `ARM_ENVIRONMENT`

Вы можете использовать этот пример сценария оболочки для установки этих переменных:

```bash
#!/bin/sh
echo "Setting environment variables for Terraform"
export ARM_SUBSCRIPTION_ID=your_subscription_id
export ARM_CLIENT_ID=your_appId
export ARM_CLIENT_SECRET=your_password
export ARM_TENANT_ID=your_tenant_id

# Not needed for public, required for usgovernment, german, china
export ARM_ENVIRONMENT=public
```

## <a name="run-a-sample-script"></a>Запуск образца сценария

Создайте файл `test.tf` в пустом каталоге и вставьте следующий сценарий.

```tf
provider "azurerm" {
}
resource "azurerm_resource_group" "rg" {
        name = "testResourceGroup"
        location = "westus"
}
```

Сохраните файл, а затем инициализируйте развертывание Terraform. На этом шаге загружаются модули Azure, необходимые для создания группы ресурсов Azure.

```bash
terraform init
```

Вы должны увидеть результат, аналогичный приведенному ниже.

```bash
* provider.azurerm: version = "~> 0.3"

Terraform has been successfully initialized!
```

Вы можете просмотреть действия, которые должны быть выполнены с помощью сценария Terraform, с помощью `terraform plan`. Когда вы будете готовы создать группу ресурсов, примените план Terraform следующим образом:

```bash
terraform apply
```

Вы должны увидеть результат, аналогичный приведенному ниже.

```bash
An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  + azurerm_resource_group.rg
      id:       <computed>
      location: "westus"
      name:     "testResourceGroup"
      tags.%:   <computed>

azurerm_resource_group.rg: Creating...
  location: "" => "westus"
  name:     "" => "testResourceGroup"
  tags.%:   "" => "<computed>"
azurerm_resource_group.rg: Creation complete after 1s
```

## <a name="next-steps"></a>Дополнительная информация

В этой статье вы установили Terraform или использовали Cloud Shell, чтобы настроить учетные данные Azure и начать создание ресурсов в подписке Azure. Чтобы создать полное развертывание Terraform в Azure, перейдите к следующей статье:

> [!div class="nextstepaction"]
> [Создание готовой инфраструктуры виртуальных машин Linux в Azure с помощью Terraform](terraform-create-complete-vm.md)
