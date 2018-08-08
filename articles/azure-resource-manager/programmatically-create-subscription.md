---
title: Программное создание подписок Azure Enterprise | Документация Майкрософт
description: Узнайте, как программно создавать дополнительные подписки Azure Enterprise или Azure "Enterprise — разработка и тестирование".
services: azure-resource-manager
author: adpick
manager: adpick
editor: ''
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/05/2018
ms.author: adpick
ms.openlocfilehash: 2bfa9944d85fde65ad8dbd73ddda11fa405df2f8
ms.sourcegitcommit: 99a6a439886568c7ff65b9f73245d96a80a26d68
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/31/2018
ms.locfileid: "39358365"
---
# <a name="programmatically-create-azure-enterprise-subscriptions-preview"></a>Программное создание подписок Azure Enterprise (предварительная версия)

Как пользователь Azure, подписавший [Соглашение Enterprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/), вы можете программно создавать подписки EA (MS-AZR-0017P) и EA "Разработка и тестирование"(MS-AZR-0148P). В этой статье Вы узнаете как создавать подписки программно с помощью Azure Resource Manager.

Когда Вы создаете подписки Azure, создаваемые с помощью этого API, где подписка регулируются соглашением, в соответствии с которым вы получили службы Microsoft Azure от корпорации Майкрософт или уполномоченного торгового посредника. Дополнительные сведения см. на странице [Юридическая информация Службы Microsoft Azure](https://azure.microsoft.com/support/legal/).

## <a name="prerequisites"></a>Предварительные требования

У вас должна быть роль владельца или участника в учетной записи регистрации, которая будет создана в рамках подписки. Получить эти роли можно двумя способами:

* Администратор регистрации [может назначить роль владельца учетной записи](https://ea.azure.com/helpdocs/addNewAccount) (требуется вход в систему обязательно), что сделает вас владельцем учетной записи регистрации. Следуйте инструкциям из приглашения, полученного по электронной почте, чтобы вручную создать начальную подписку. Подтвердите владение учетной записью и вручную создайте начальную подписку EA, прежде чем переходить к следующему шагу. Просто добавить учетную запись в регистрацию недостаточно.

* Текущий владелец учетной записи регистрации может [предоставить вам права доступа](grant-access-to-create-subscription.md). Аналогично, если вы хотите использовать субъект-службу для создания подписки EA, необходимо [предоставить возможность создания подписки на нее](grant-access-to-create-subscription.md).

## <a name="find-accounts-you-have-access-to"></a>Поиск учетных записей, к которым у вас есть доступ

После того, как вы будете добавлены в регистрацию Azure EA в качестве владельца учетной записи, платформа Azure использует связь между регистрацией и учетной записью, чтобы определить цель для выставления счетов за подписки. Все подписки, созданные с учетной записью, оплачиваются в рамках регистрации EA, к которой относится эта учетная запись. Чтобы создать подписки, необходимо в значениях передать о регистрации учетной записи и субъектов-пользователя — владельца подписки. 

Чтобы выполнить следующие команды, войдите в *домашний каталог* владельца учетной записи, в котором по умолчанию создаются подписки.

# <a name="resttabrest"></a>[REST](#tab/rest)

Запрос на вывод списка всех учетных записей регистрации.

```json
GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts?api-version=2018-03-01-preview
```

Платформа Azure выводит список всех учетных записей регистрации, к которым у вас есть доступ.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Billing/enrollmentAccounts",
      "properties": {
        "principalName": "SignUpEngineering@contoso.com"
      }
    },
    {
      "id": "/providers/Microsoft.Billing/enrollmentAccounts/4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Billing/enrollmentAccounts",
      "properties": {
        "principalName": "BillingPlatformTeam@contoso.com"
      }
    }
  ]
}
```

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Используйте [команду Get-AzureRmEnrollmentAccount](/powershell/module/azurerm.billing/get-azurermenrollmentaccount), чтобы вывести список всех учетных записей регистрации, к которым у вас есть доступ.

```azurepowershell-interactive
Get-AzureRmEnrollmentAccount
```

Платформа Azure выводит список идентификаторов объектов и адресов электронной почты учетных записей.

```azurepowershell
ObjectId                               | PrincipalName
747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | SignUpEngineering@contoso.com
4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | BillingPlatformTeam@contoso.com
```

# <a name="azure-clitabazure-cli"></a>[интерфейс командной строки Azure](#tab/azure-cli)

Используйте [команду Get-EnrollmentAccount](https://aka.ms/EASubCreationPublicPreviewCLI), чтобы вывести список всех учетных записей регистрации, к которым у вас есть доступ.

```azurecli-interactive 
az billing enrollment-account list
```

Платформа Azure выводит список идентификаторов объектов и адресов электронной почты учетных записей.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Billing/enrollmentAccounts",
      "properties": {
        "principalName": "SignUpEngineering@contoso.com"
      }
    },
    {
      "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Billing/enrollmentAccounts",
      "properties": {
        "principalName": "BillingPlatformTeam@contoso.com"
      }
    }
  ]
}
```

---

Используйте свойство `principalName`, чтобы указать учетную запись для оплаты подписок. Укажите `id` в качестве значения `enrollmentAccount`, используемого для создания подписки на следующем шаге.

## <a name="create-subscriptions-under-a-specific-enrollment-account"></a>Создание подписок с помощью определенной учетной записи регистрации 

В следующем примере создается запрос на создание подписки *Dev Team Subscription* и предложения подписки *MS-AZR-0017P* (обычная подписка EA). Используется учетная запись регистрации `747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx` (значение заполнителя, GUID), которая является учетной записью регистрации для SignUpEngineering@contoso.com. Этот пример также при необходимости добавляет двух пользователей в качестве владельцев (RBAC) для подписки.

# <a name="resttabrest"></a>[REST](#tab/rest)

Используйте `id` из `enrollmentAccount` в пути запроса для создания подписки.

```json
POST https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx/providers/Microsoft.Subscription/createSubscription?api-version=2018-03-01-preview

{
  "displayName": "Dev Team Subscription",
  "offerType": "MS-AZR-0017P",
  "owners": [
    {
      "objectId": "<userObjectId>"
    },
    {
      "objectId": "<servicePrincipalObjectId>"
    }
  ]
}
```

| Имя элемента  | Обязательно | type   | ОПИСАНИЕ                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | Нет       | Строка | Отображаемое имя подписки. Если нет — ему присваивается имя предложения, например "Microsoft Azure Enterprise".                                 |
| `offerType`   | Yes      | Строка | Предложение подписки. Доступны два варианта предложения EA: [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (для использования в рабочей среде) и [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (для разработки и тестирования, должен быть [включен с помощью портала EA](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `owners`      | Нет        | Строка | Идентификатор объекта любого пользователя, которого вы хотите добавить в качестве владельца RBAC для подписки при ее создании.  |

В ответе возвращается объект `subscriptionOperation` для отслеживания. После завершения создания подписки объект `subscriptionOperation` вернет объект `subscriptionLink` с идентификатором подписки.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Чтобы использовать эту предварительную версию модуля, сначала установите ее с помощью командлета `Install-Module AzureRM.Subscription -AllowPrerelease`. Чтобы убедиться в том, что `-AllowPrerelease` работает, установите последнюю версию PowerShellGet из статьи [Получение модуля PowerShellGet](/powershell/gallery/installing-psget).

Используйте командлет [New-AzureRmSubscription](/powershell/module/azurerm.subscription), указав идентификатор объекта `enrollmentAccount` в качестве параметра `EnrollmentAccountObjectId`, чтобы создать подписку. 

```azurepowershell-interactive
New-AzureRmSubscription -OfferType MS-AZR-0017P -Name "Dev Team Subscription" -EnrollmentAccountObjectId 747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx -OwnerObjectId <userObjectId>,<servicePrincipalObjectId>
```

| Имя элемента  | Обязательно | type   | ОПИСАНИЕ                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `Name` | Нет       | Строка | Отображаемое имя подписки. Если нет — ему присваивается имя предложения, например "Microsoft Azure Enterprise".                                 |
| `OfferType`   | Yes      | Строка | Предложение подписки. Доступны два варианта предложения EA: [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (для использования в рабочей среде) и [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (для разработки и тестирования, должен быть [включен с помощью портала EA](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `EnrollmentAccountObjectId`      | Yes       | Строка | Идентификатор объекта учетной записи для регистрации, в которой создается подписка и для которой выставляются счета. Это значение GUID, которое вы получили с помощью `Get-AzureRmEnrollmentAccount`. |
| `OwnerObjectId`      | Нет        | Строка | Идентификатор объекта любого пользователя, которого вы хотите добавить в качестве владельца RBAC для подписки при ее создании.  |
| `OwnerSignInName`    | Нет        | Строка | Адрес электронной почты любого пользователя, которого вы хотите добавить в качестве владельца RBAC для подписки при ее создании. Можно использовать этот параметр вместо `OwnerObjectId`.|
| `OwnerApplicationId` | Нет        | Строка | Идентификатор приложения любого субъекта-службы, которого вы хотите добавить в качестве владельца RBAC для подписки при ее создании. Можно использовать этот параметр вместо `OwnerObjectId`.| 

Полный список параметров приведен в разделе о [New-AzureRmSubscription](/powershell/module/azurerm.subscription.preview).

# <a name="azure-clitabazure-cli"></a>[интерфейс командной строки Azure](#tab/azure-cli)

Чтобы использовать эту предварительную версию расширения, сначала установите ее с помощью команды `az extension add --name subscription`.

Используйте команду [az account create](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create), указав идентификатор объекта `enrollmentAccount` в качестве параметра `enrollment-account-object-id`, чтобы создать подписку.

```azurecli-interactive 
az account create --offer-type "MS-AZR-0017P" --display-name "Dev Team Subscription" --enrollment-account-object-id "747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx" --owner-object-id "<userObjectId>","<servicePrincipalObjectId>"
```

| Имя элемента  | Обязательно | type   | ОПИСАНИЕ                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `display-name` | Нет       | Строка | Отображаемое имя подписки. Если нет — ему присваивается имя предложения, например "Microsoft Azure Enterprise".                                 |
| `offer-type`   | Yes      | Строка | Предложение подписки. Доступны два варианта предложения EA: [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (для использования в рабочей среде) и [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (для разработки и тестирования, должен быть [включен с помощью портала EA](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `enrollment-account-object-id`      | Yes       | Строка | Идентификатор объекта учетной записи для регистрации, в которой создается подписка и для которой выставляются счета. Это значение GUID, которое вы получили с помощью `az billing enrollment-account list`. |
| `owner-object-id`      | Нет        | Строка | Идентификатор объекта любого пользователя, которого вы хотите добавить в качестве владельца RBAC для подписки при ее создании.  |
| `owner-upn`    | Нет        | Строка | Адрес электронной почты любого пользователя, которого вы хотите добавить в качестве владельца RBAC для подписки при ее создании. Можно использовать этот параметр вместо `owner-object-id`.|
| `owner-spn` | Нет        | Строка | Идентификатор приложения любого субъекта-службы, которого вы хотите добавить в качестве владельца RBAC для подписки при ее создании. Можно использовать этот параметр вместо `owner-object-id`.| 

Полный список параметров приведен в разделе о команде [az account create](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create).

----

## <a name="limitations-of-azure-enterprise-subscription-creation-api"></a>Ограничения API создания подписок Azure Enterprise

- С помощью этого API могут быть созданы только подписки Azure Enterprise.
- Действует ограничение в 50 подписок на учетную запись. После этого подписки могут создаваться только с помощью центра учетных записей.
- В учетной записи должна быть по крайней мере одна подписка EA или подписка EA "Разработка и тестирование". Это означает, что владелец учетной записи по крайней мере один раз выполнил регистрацию вручную.
- Пользователи, которые не являются владельцами учетной записи, но были добавлены в учетную запись регистрации с помощью RBAC, не могут создавать подписки, используя центр учетных записей.
- Невозможно выбрать клиент для создания подписки. Подписка всегда создается в домашнем клиенте владельца учетной записи. Чтобы переместить подписку в другой клиент, ознакомьтесь с [изменением подписки клиента](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="next-steps"></a>Дополнительная информация

* Создание подписок с помощью .NET: [пример кода на сайте GitHub](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core).
* После создания подписки можно предоставить эту возможность для других пользователей и субъектов-служб. Дополнительные сведения см. статье [Предоставление доступа к созданию подписок Azure Enterprise (предварительная версия)](grant-access-to-create-subscription.md).
* Дополнительные сведения о переносе большого числа подписок с помощью групп управления см. в руководстве по [упорядочиванию ресурсов с помощью групп управления Azure](management-groups-overview.md).
