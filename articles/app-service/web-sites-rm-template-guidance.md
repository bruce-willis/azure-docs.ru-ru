---
title: Руководство по развертыванию веб-приложений Azure с помощью шаблонов | Документация Майкрософт
description: Рекомендации по созданию шаблонов Azure Resource Manager для развертывания веб-приложений.
services: app-service
documentationcenter: app-service
author: tfitzmac
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/09/2018
ms.author: tomfitz
ms.openlocfilehash: c2f600d86965e1115d4be1370da8f7c8e1b67f05
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/10/2018
ms.locfileid: "37927678"
---
# <a name="guidance-on-deploying-web-apps-by-using-azure-resource-manager-templates"></a>Руководство по развертыванию веб-приложений с помощью шаблонов Azure Resource Manager

В этой статье содержатся рекомендации по созданию шаблонов Azure Resource Manager для развертывания решений службы приложений Azure. Используя эти рекомендации, можно избежать возникновения распространенных проблем.

## <a name="define-dependencies"></a>Определение зависимостей

Определение зависимостей веб-приложений требует понимания того, как ресурсы взаимодействуют в пределах веб-приложения. Если указать зависимости в неверном порядке, это может привести к возникновению ошибок при развертывании или к наличию состояния гонки, что, в свою очередь, приводит к зависанию развертывания.

> [!WARNING]
> Если вы включили в шаблон расширение сайта MSDeploy, необходимо установить все ресурсы настройки как зависимые от ресурса MSDeploy. Изменение конфигурации приводит к асинхронной перезагрузке сайта. Если сделать ресурсы настройки зависимыми от MSDeploy, работа MSDeploy будет завершаться до перезагрузки сайта. Без этих зависимостей сайт может перезагрузиться в процессе развертывания MSDeploy. Пример шаблона WordPress с зависимостью веб-развертывания см. в [этом](https://github.com/davidebbo/AzureWebsitesSamples/blob/master/ARMTemplates/WordpressTemplateWebDeployDependency.json) разделе.

На следующем изображении показан порядок зависимостей для различных ресурсов службы приложений:

![Зависимости службы приложений](media/web-sites-rm-template-guidance/web-dependencies.png)

Выполните развертывание ресурсов в следующем порядке.

**Уровень 1**
* План службы приложений.
* Все другие связанные ресурсы (например, базы данных или учетные записи хранения).

**Уровень 2**
* Веб-приложение — зависит от плана службы приложений.
* Экземпляр Azure Application Insights, предназначенный для фермы серверов, — зависит от плана службы приложений.

**Уровень 3**
* Система управления версиями — зависит от веб-приложения.
* Расширение сайта MSDeploy — зависит от веб-приложения.
* Экземпляр Application Insights, предназначенный для фермы серверов, — зависит от веб-приложения.

**Уровень 4**
* Сертификат службы приложений — зависит от системы управления версиями или MSDeploy (если не имеется одной из этих служб). В противном случае зависит от веб-приложения.
* Параметры конфигурации (строки подключения, значения в файле web.config, параметры приложения) — зависят от системы управления версиями или MSDeploy (при их наличии). В противном случае зависит от веб-приложения.

**Уровень 5**
* Привязки имени узла — зависят от сертификата (если он присутствует). В противном случае зависят от ресурса более высокого уровня.
* Расширения сайта — зависят от параметров конфигурации (если они присутствуют). В противном случае зависят от ресурса более высокого уровня.

Как правило, решение содержит только некоторые из этих ресурсов и уровней. Для отсутствующих уровней сопоставьте ресурсы более низкого уровня с ресурсами следующего уровня.

В приведенном ниже примере представлена часть шаблона. Значение конфигурации строки подключения зависит от расширения MSDeploy. Расширение MSDeploy зависит от веб-приложения и базы данных. 

```json
{
    "name": "[parameters('appName')]",
    "type": "Microsoft.Web/Sites",
    ...
    "resources": [
      {
          "name": "MSDeploy",
          "type": "Extensions",
          "dependsOn": [
            "[concat('Microsoft.Web/Sites/', parameters('appName'))]",
            "[concat('Microsoft.Sql/servers/', parameters('dbServerName'), '/databases/', parameters('dbName'))]",
          ],
          ...
      },
      {
          "name": "connectionstrings",
          "type": "config",
          "dependsOn": [
            "[concat('Microsoft.Web/Sites/', parameters('appName'), '/Extensions/MSDeploy')]"
          ],
          ...
      }
    ]
}
```

Готовый пример с показанным выше кодом см. [здесь](https://github.com/Azure/azure-quickstart-templates/tree/master/umbraco-webapp-simple).

## <a name="find-information-about-msdeploy-errors"></a>Поиск сведений об ошибках MSDeploy

Если шаблон Resource Manager использует MSDeploy, сообщения об ошибках развертывания могут быть трудными для понимания. Чтобы получить дополнительные сведения об ошибках в случае неудачного развертывания, сделайте следующее.

1. Перейдите на [консоль Kudu](https://github.com/projectkudu/kudu/wiki/Kudu-console) сайта.
2. Перейдите в папку по следующему пути: D:\home\LogFiles\SiteExtensions\MSDeploy.
3. Найдите файлы appManagerStatus.xml и appManagerLog.xml. В первом файле указано состояние. Во втором файле содержатся сведения об ошибке. Если ошибка недостаточно информативна, укажите ее в публикации на форуме.

## <a name="choose-a-unique-web-app-name"></a>Выбор уникального имени веб-приложения

Имя веб-приложение должно быть глобально уникальным. Вы можете использовать соглашение об именовании, которое будет уникальным, или применить [функцию uniqueString](../azure-resource-manager/resource-group-template-functions-string.md#uniquestring), чтобы получить уникальное имя.

```json
{
  "apiVersion": "2016-08-01",
  "name": "[concat(parameters('siteNamePrefix'), uniqueString(resourceGroup().id))]",
  "type": "Microsoft.Web/sites",
  ...
}
```

## <a name="deploy-web-app-certificate-from-key-vault"></a>Развертывание сертификата веб-приложения из Key Vault

Если шаблон содержит ресурс [Microsoft.Web/certificates](/azure/templates/microsoft.web/certificates) для SSL-привязки и сертификат хранится в Key Vault, необходимо убедиться в том, что удостоверение службы приложений имеет доступ к сертификату.

В глобальной среде Azure субъект-служба службы приложений имеет идентификатор **abfa0a7c-a6b6-4736-8310-5855508787cd**. Чтобы предоставить доступ к Key Vault для субъекта-службы службы приложений, используйте:

```azurepowershell-interactive
Set-AzureRmKeyVaultAccessPolicy `
  -VaultName KEY_VAULT_NAME `
  -ServicePrincipalName abfa0a7c-a6b6-4736-8310-5855508787cd `
  -PermissionsToSecrets get `
  -PermissionsToCertificates get
```

В Azure для государственных организаций субъект-служба службы приложений имеет идентификатор **6a02c803-dafd-4136-b4c3-5a6f318b4714**. Используйте этот идентификатор в предыдущем примере.

В хранилище Key Vault выберите **Сертификаты** и нажмите **Создать или импортировать**, чтобы отправить сертификат.

![Импорт сертификата](media/web-sites-rm-template-guidance/import-certificate.png)

В шаблоне укажите имя сертификата для `keyVaultSecretName`.

Пример шаблона см. в разделе [Развертывание сертификата веб-приложения из секрета Key Vault и его использование для создания SSL-привязки](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-certificate-from-key-vault).

## <a name="next-steps"></a>Дополнительная информация

* Руководство по развертыванию веб-приложений с помощью шаблона см. [здесь](app-service-deploy-complex-application-predictably.md).