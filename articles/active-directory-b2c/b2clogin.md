---
title: Использование b2clogin.com | Документация Майкрософт
description: Сведения об использовании b2clogin.com вместо login.microsoftonline.com.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/29/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 1d42d9a97244eeff501b9d02b0f143d6ef0c91b2
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/03/2018
ms.locfileid: "37440636"
---
# <a name="using-b2clogincom"></a>С помощью b2clogin.com

>[!IMPORTANT]
>Эта функция является общедоступной 
>

Теперь у вас есть возможность использовать службу Azure AD B2C `<YourTenantName>.b2clogin.com` вместо `login.microsoftonline.com`.  Вы получаете множество преимуществ:
* Вы не будете разделять такое же ограничение размера заголовка файла cookie, что и у других продуктов Майкрософт.
* Вы можете удалить все ссылки на сайты корпорации Майкрософт в своем URL-адресе (вы можете заменить `<YourTenantName>.onmicrosoft.com` своим идентификатором клиента). Например, `https://<tenantname>.b2clogin.com/tfp/<tenantname>/<policyname>/v2.0/.well-known/openid-configuration`.

 Чтобы воспользоваться преимуществами b2clogin.com, необходимо настроить следующее:

1. Убедитесь, что для **конечной точки немедленного выполнения** используется `<YourTenantName>.b2clogin.com` вместо `login.microsoftonline.com`.
2. Если вы используете MSAL, вам нужно установить `validateauthority=false`.  Это происходит потому, что поставщик маркера — `<YourTenantName>.b2clogin.com`.