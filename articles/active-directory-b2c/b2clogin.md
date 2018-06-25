---
title: Использование b2clogin.com | Документация Майкрософт
description: Сведения об использовании b2clogin.com вместо login.microsoftonline.com.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 04/29/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: c41c02acaeffa170d55f3c59f34a4b1ecae1c523
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34712459"
---
# <a name="using-b2clogincom"></a>С помощью b2clogin.com

>[!IMPORTANT]
>Эта функция является общедоступной 
>

Теперь у вас есть возможность использовать службу Azure AD B2C `<YourTenantName>.b2clogin.com` вместо `login.microsoftonline.com`.  Вы получаете множество преимуществ:
* Вы больше не будете разделять такое же ограничение размера заголовка файла cookie, что и у других продуктов Майкрософт.
* Вы можете удалить все ссылки на корпорацию Майкрософт в своем URL-адресе (вы можете заменить `<YourTenantName>.onmicrosoft.com` своим идентификатором клиента).

 Чтобы воспользоваться преимуществами b2clogin.com, необходимо настроить следующее:

1. Убедитесь, что для **конечной точки немедленного выполнения** используется `<YourTenantName>.b2clogin.com` вместо `login.microsoftonline.com`.
2. Если вы используете MSAL, вам нужно установить `validateauthority=false`.  Это происходит потому, что поставщик маркера — `<YourTenantName>.b2clogin.com`.