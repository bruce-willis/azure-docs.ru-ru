---
title: Предоставление разрешений для специально разработанного приложения | Документы Майкрософт
description: Предоставление разрешений для специально разработанного приложения с помощью портала Azure AD или параметра URL-адреса
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: barbkess
ms.openlocfilehash: 75beeb35b740bb126fff905f4cfa5a0b455e025e
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/31/2018
ms.locfileid: "39365249"
---
# <a name="how-to-grant-permissions-to-a-custom-developed-application"></a>Как предоставить разрешения для специально разработанного приложения

Если требуется дать согласие в приложении заранее или если возникло сообщение об ошибке в связи с тем, что не предоставлено согласие для приложения, попробуйте выполнить следующие действия.

## <a name="how-to-perform-admin-consent-for-your-application"></a>Предоставление согласия администратора для вашего приложения

Это приводит к тому, что согласие предоставляется для всех пользователей в организации.

1. Перейдите к колонке **Регистрация приложений** в качестве **глобального администратора**, а затем выберите приложение.

2. Выберите **Необходимые разрешения** и, наконец, нажмите кнопку **Предоставить разрешения** в верхней части колонки.

Кроме того, можно создать запрос на адрес *login.microsoftonline.com*, указав конфигурацию приложения и добавив *&prompt=admin\_consent*. После входа с учетными данными администратора приложению были предоставлены разрешения для всех пользователей.

## <a name="how-to-force-user-consent-for-your-application"></a>Принудительное предоставление согласия пользователя для приложения

* Добавьте *&prompt=consent* к запросу на аутентификацию, при этом пользователю необходимо будет давать согласие всякий раз, как он проходит аутентификацию.

## <a name="next-steps"></a>Дополнительная информация

[Согласие и интеграция приложений с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)

[Согласие и разрешения для конвергированных приложений в Azure AD версии 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)<br>

[StackOverflow в AzureAD](http://stackoverflow.com/questions/tagged/azure-active-directory)
