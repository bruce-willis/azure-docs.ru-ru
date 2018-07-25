---
title: Использование Azure Key Vault с управляемыми приложениями | Документы Майкрософт
description: Демонстрация использования секретов доступа в Azure Key Vault при развертывании управляемых приложений
services: managed-applications
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 07/11/2018
ms.author: tomfitz
ms.openlocfilehash: f091ba44a3170dcc4141829f2f4105d6e7993cdf
ms.sourcegitcommit: 04fc1781fe897ed1c21765865b73f941287e222f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/13/2018
ms.locfileid: "39035295"
---
# <a name="access-key-vault-secret-when-deploying-azure-managed-applications"></a>Доступ к секрету Key Vault при развертывании Управляемых приложений Azure

Если необходимо передать защищенное значение (например, пароль) в качестве параметра во время развертывания, можно извлечь его из [Azure Key Vault](../key-vault/key-vault-whatis.md). Чтобы получить доступ к Key Vault при развертывании управляемых приложений, нужно предоставить доступ к субъекту-службе **Поставщик ресурсов устройств**. Эта статья описывает настройку Key Vault для работы с управляемыми приложениями.

## <a name="enable-template-deployment"></a>Включение развертывания шаблона

1. На портале выберите Key Vault.

1. Выберите **Политики доступа**.   

   ![Выбор политик доступа](./media/key-vault-access/select-access-policies.png)

1. Выберите **Щелкните, чтобы показать политики расширенного доступа**.

   ![Отображение политик расширенного доступа](./media/key-vault-access/advanced.png)

1. Выберите **Включить доступ к Azure Resource Manager для развертывания шаблонов**. Затем нажмите кнопку **Сохранить**.

   ![Включение развертывания шаблона](./media/key-vault-access/enable-template.png)

## <a name="add-service-as-contributor"></a>Добавление службы в качестве участника

1. Выберите **Управление доступом (IAM)**.

   ![Выбор управления доступом](./media/key-vault-access/access-control.png)

1. Выберите **Добавить**.

   ![Выбор элемента "Добавить"](./media/key-vault-access/add-access-control.png)

1. Выберите **Участник** для роли. Найдите **Поставщик ресурсов устройств** и выберите его в списке доступных вариантов.

   ![Поиск поставщика](./media/key-vault-access/search-provider.png)

1. Щелкните **Сохранить**.

## <a name="reference-key-vault-secret"></a>Ссылка на секрет Key Vault

Чтобы передать секрет из Key Vault в шаблон в управляемом приложении, нужно использовать [связанный шаблон](../azure-resource-manager/resource-group-linked-templates.md) и ссылку на Key Vault в параметрах для этого связанного шаблона. Укажите идентификатор ресурса Key Vault и имя секрета.

```json
"resources": [{
  "apiVersion": "2015-01-01",
  "name": "linkedTemplate",
  "type": "Microsoft.Resources/deployments",
  "properties": {
    "mode": "incremental",
    "templateLink": {
      "uri": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/keyvaultparameter/sqlserver.json",
      "contentVersion": "1.0.0.0"
    },
    "parameters": {
      "adminPassword": {
        "reference": {
          "keyVault": {
            "id": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.KeyVault/vaults/<key-vault-name>"
          },
          "secretName": "<secret-name>"
        }
      },
      "adminLogin": { "value": "[parameters('adminLogin')]" },
      "sqlServerName": {"value": "[parameters('sqlServerName')]"}
    }
  }
}],
```

## <a name="next-steps"></a>Дополнительная информация

Вы настроили доступ к Key Vault во время развертывания управляемого приложения.

* Сведения о передаче значения из Key Vault в виде параметра шаблона см. в статье [Использование Azure Key Vault для передачи защищенного значения параметра во время развертывания](../azure-resource-manager/resource-manager-keyvault-parameter.md).
* См. дополнительные сведения о [примерах проектов для управляемых приложений Azure](sample-projects.md).
* Чтобы узнать, как создать файл определения пользовательского интерфейса для управляемого приложения, изучите статью [Начало работы с CreateUiDefinition](create-uidefinition-overview.md).