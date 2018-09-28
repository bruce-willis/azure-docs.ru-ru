---
title: Проверка подлинности с помощью Azure AD в национальных облаках
description: Узнайте о конечных точках для регистрации и проверки подлинности приложений в национальных облаках.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: negoe
ms.reviewer: negoe,andret,saeeda
ms.custom: aaddev
ms.openlocfilehash: 866a86178d66b7b4af069d684e4eb56c12db47ca
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46982007"
---
# <a name="national-clouds"></a>Национальные облака

Национальные облака — это физически изолированные экземпляры Azure. Эти регионы Azure призваны обеспечить соблюдение требований по размещению и независимости данных, а также законодательных требований в пределах географических границ.

Кроме глобального облака, Azure Active Directory развертывается в следующих национальных облаках:  

- Azure для государственных организаций США
- Azure для Германии
- Azure China 21Vianet

## <a name="app-registration-endpoints"></a>Конечные точки регистрации приложений

В следующей таблице перечислены базовые URL-адреса конечных точек Azure Active Directory (Azure AD), используемых для регистрации приложения в каждом из национальных облаков.

| Национальное облако | Конечная точка портала Azure AD
| --- | --- |
| Azure AD для государственных организаций США |https://portal.azure.us
|Azure AD для Германии |https://portal.microsoftazure.de
|Azure AD для Китая через 21Vianet |https://portal.azure.cn
|Azure AD (глобальная служба)|https://portal.azure.com

## <a name="azure-ad-authentication-endpoints"></a>Конечные точки Azure AD для проверки подлинности

В следующей таблице перечислены базовые URL-адреса конечных точек Azure Active Directory (Azure AD) для получения маркеров проверки подлинности, используемых для вызова Microsoft Graph в каждом из национальных облаков.

| Национальное облако | Конечная точка проверки подлинности Azure AD
| --- | --- |
| Azure AD для государственных организаций США |`https://login.microsoftonline.us`
|Azure AD для Германии| `https://login.microsoftonline.de`
|Azure AD для Китая через 21Vianet | `https://login.chinacloudapi.cn`
|Azure AD (глобальная служба)|`https://login.microsoftonline.com`

Запросы к конечным точкам Azure AD для авторизации или получения маркеров проверки подлинности можно составлять, используя базовый URL-адрес соответствующего региона. Например, для Германии:

- Общая конечная точка авторизации — `https://login.microsoftonline.de/common/oauth2/authorize`.
- Общая конечная точка получения маркеров проверки подлинности — `https://login.microsoftonline.de/common/oauth2/token`. 

Для однотенантных приложений замените часть common в указанных выше URL-адресах идентификатором или именем клиента, например `https://login.microsoftonline.de/contoso.com`.

>[!NOTE]
> Конечные точки [Azure AD версии 2.0 для авторизации]( https://docs.microsoft.com/azure/active-directory/develop/active-directory-appmodel-v2-overview) и получения маркеров проверки подлинности доступны только в глобальной службе. Они пока недоступны для служб, развернутых в национальных облаках.

## <a name="next-steps"></a>Дополнительная информация

- Подробнее об [Azure для государственных организаций](https://docs.microsoft.com/azure/azure-government/).
- Подробнее об [Azure для Китая через 21Vianet](https://docs.microsoft.com/azure/china/).
- Подробнее об [Azure для Германии](https://docs.microsoft.com/azure/germany/).
- [Основные сведения о проверке подлинности в Azure AD](authentication-scenarios.md).
- Подробнее о [развертывании Microsoft Graph в национальном облаке](https://developer.microsoft.com/graph/docs/concepts/deployments).