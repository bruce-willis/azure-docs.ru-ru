---
title: Расширение Chef для виртуальных машин Azure | Документация Майкрософт
description: Сведения о развертывании клиента Chef на виртуальной машине, используя расширение Chef.
services: virtual-machines-linux
documentationcenter: ''
author: roiyz-msft
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/21/2018
ms.author: roiyz
ms.openlocfilehash: 159ce1b565068e2cfdb3cb1cb2e5b5f72ff6848f
ms.sourcegitcommit: f31bfb398430ed7d66a85c7ca1f1cc9943656678
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/28/2018
ms.locfileid: "47451363"
---
# <a name="chef-vm-extension-for-linux-and-windows"></a>Расширение Chef виртуальной машины для Linux и Windows

Chef Software — это платформа автоматизации DevOps для Linux и Windows, которая позволяет управлять конфигурациями как физического, так и виртуального серверов. Chef — это расширение, которое включает среду Chef на виртуальной машине.

## <a name="prerequisites"></a>Предварительные требования

### <a name="operating-system"></a>Операционная система

Chef работает во всех [операционных системах, поддерживаемых расширением](https://support.microsoft.com/help/4078134/azure-extension-supported-operating-systems) в Azure.

### <a name="internet-connectivity"></a>Подключение к Интернету

Чтобы обеспечить правильную работу расширения Chef, целевую виртуальную машину необходимо подключить к Интернету. Это позволит получать полезные данные клиента Chef из сети доставки содержимого (CDN).  

## <a name="extension-schema"></a>Схема расширения

В следующем коде JSON показана схема расширения Chef виртуальной машины. В расширении необходимо как минимум указать URL-адрес сервера Chef, имя клиента для проверки и ключ проверки для сервера Chef. Эти значения можно найти в файле `knife.rb` в наборе starter-kit.zip, который загружается во время установки [Chef Automate](https://azuremarketplace.microsoft.com/marketplace/apps/chef-software.chef-automate) либо отдельного [сервера Chef](https://downloads.chef.io/chef-server). Так как ключ проверки должен рассматриваться в качестве конфиденциальных данных, его нужно настроить под элементом **protectedSettings**, после чего он будет расшифрован только на целевой виртуальной машине.

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "[concat(variables('vmName'),'/', parameters('chef_vm_extension_type'))]",
  "apiVersion": "2017-12-01",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
  ],
  "properties": {
    "publisher": "Chef.Bootstrap.WindowsAzure",
    "type": "[parameters('chef_vm_extension_type')]",
    "typeHandlerVersion": "1210.12",
    "settings": {
      "bootstrap_options": {
        "chef_server_url": "[parameters('chef_server_url')]",
        "validation_client_name": "[parameters('chef_validation_client_name')]"
      },
      "runlist": "[parameters('chef_runlist')]"
    },
    "protectedSettings": {
      "validation_key": "[parameters('chef_validation_key')]"
    }
  }
}  
```

### <a name="core-property-values"></a>Значения свойств ядра

| ИМЯ | Значение и пример | Тип данных
| ---- | ---- | ---- | ----
| версия_API | `2017-12-01` | Строка (дата) |
| publisher | `Chef.Bootstrap.WindowsAzure` | строка |
| Тип | `LinuxChefClient` (Linux), `ChefClient` (Windows) | строка |
| typeHandlerVersion | `1210.12` | Строка (двойная) |

### <a name="settings"></a>Параметры

| ИМЯ | Значение и пример | Тип данных | Обязательный?
| ---- | ---- | ---- | ----
| settings/параметры_начальной_загрузки/URL-адрес_сервера_Chef | `https://api.chef.io/organizations/myorg` | Строка (URL-адрес) | Да |
| settings/параметры_начальной_загрузки/имя_клиента_для_проверки | `myorg-validator` | строка | Да |
| settings/runlist | `recipe[mycookbook::default]` | строка | Да |

### <a name="protected-settings"></a>Защищенные параметры

| ИМЯ | Пример | Тип данных | Обязательный?
| ---- | ---- | ---- | ---- |
| protectedSettings/ключ_проверки | `-----BEGIN RSA PRIVATE KEY-----\nKEYDATA\n-----END RSA PRIVATE KEY-----` | строка | Да |

<!--
### Linux-specific settings

| Name | Value / Example | Data Type |
| ---- | ---- | ---- |

### Windows-specific settings

| Name | Value / Example | Data Type |
| ---- | ---- | ---- |
-->

## <a name="template-deployment"></a>Развертывание шаблона

Расширения виртуальной машины Azure можно развернуть с помощью шаблонов Azure Resource Manager. С помощью шаблонов можно развернуть одну или несколько виртуальных машин, установить клиент Chef, подключиться к серверу Chef и выполнить начальной конфигурации на сервере, как определено в списке [Run-list](https://docs.chef.io/run_lists.html).

Пример шаблона Resource Manager, включающего в себя расширение Chef виртуальной машины, можно найти в [коллекции быстрого запуска Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/chef-json-parameters-linux-vm).

Конфигурацию JSON для расширения виртуальной машины можно вложить в ресурс виртуальной машины или поместить в корень или на верхний уровень JSON-файла шаблона Resource Manager. Размещение конфигурации JSON влияет на значения имени и типа ресурса. Дополнительные сведения см. в разделе [Указание имени и типа дочернего ресурса в шаблоне Resource Manager](../../azure-resource-manager/resource-manager-template-child-resource.md).

## <a name="azure-cli-deployment"></a>Развертывание с помощью Azure CLI

С помощью Azure CLI можно развернуть расширения Chef виртуальной машины на имеющейся виртуальной машине. Замените заполнитель **ключ_проверки** содержимым ключа проверки (это файл с расширением `.pem`).  Замените заполнители **имя_клиента_для_проверки**, **URL-адрес_сервера_Chef** и **список_запуска** значениями из файла `knife.rb` в начальном наборе.

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myExistingVM \
  --name LinuxChefClient \
  --publisher Chef.Bootstrap.WindowsAzure \
  --version 1210.12 --protected-settings '{"validation_key": "<validation_key>"}' \
  --settings '{ "bootstrap_options": { "chef_server_url": "<chef_server_url>", "validation_client_name": "<validation_client_name>" }, "runlist": "<run_list>" }'
```

## <a name="troubleshooting-and-support"></a>Устранение неполадок и поддержка

Данные о состоянии развертывания расширения можно получить на портале Azure, а также использовав Azure CLI. Чтобы просмотреть состояние развертывания расширений для определенной виртуальной машины, выполните следующую команду в Azure CLI.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myExistingVM -o table
```

Выходные данные выполнения расширения регистрируются в следующем файле:

### <a name="linux"></a>Linux

```bash
/var/lib/waagent/Chef.Bootstrap.WindowsAzure.LinuxChefClient
```

### <a name="windows"></a>Windows

```powershell
C:\Packages\Plugins\Chef.Bootstrap.WindowsAzure.ChefClient\
```

### <a name="error-codes-and-their-meanings"></a>Коды ошибок и их описание

| Код ошибки | Значение | Возможное действие |
| :---: | --- | --- |
| 51 | Это расширение не поддерживается в операционной системе виртуальной машины | |

Дополнительные сведения об устранении неполадок см. в [файле сведений о расширении Chef виртуальной машины](https://github.com/chef-partners/azure-chef-extension).

## <a name="next-steps"></a>Дополнительная информация

Если в любой момент при изучении этой статьи вам потребуется дополнительная помощь, вы можете обратиться к экспертам по Azure на [форумах MSDN Azure и Stack Overflow](https://azure.microsoft.com/support/forums/). Кроме того, можно зарегистрировать обращение в службу поддержки Azure. Перейдите на [сайт поддержки Azure](https://azure.microsoft.com/support/options/) и щелкните "Получить поддержку". Дополнительные сведения об использовании службы поддержки Azure см. в статье [Часто задаваемые вопросы о поддержке Microsoft Azure](https://azure.microsoft.com/support/faq/).
