---
title: включение файла
description: включение файла
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/13/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: 962cd4598ab3329786b2682bd2f97801c7943fe1
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/05/2018
ms.locfileid: "48843528"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-from-an-android-app"></a>Вход пользователей и вызов Microsoft Graph из приложения Android

Из этого руководства вы узнаете, как создать приложение Android и интегрировать его с платформой удостоверений Майкрософт. В частности, приложение авторизует пользователя, получит маркер доступа для вызова API Microsoft Graph и выполнит базовый запрос к API Microsoft Graph.  

Когда вы завершите работу с этим руководством, ваше приложение сможет принимать операции входа с помощью личных учетных записей Майкрософт (включая outlook.com, live.com и другие), а также рабочих или учебных учетных записей из любой компании или организации, в которых используется Azure Active Directory. 

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Как работает пример приложения, созданный в этом руководстве
![Как работает этот пример](media/active-directory-develop-guidedsetup-android-intro/android-intro.png)

В этом примере приложение авторизует пользователя и получает данные от его имени.  Доступ к этим данным будет осуществляться через удаленный API (в данном случае API Microsoft Graph), который требует авторизации и защищен платформой удостоверений Майкрософт. 

В частности: 
* Приложение открывает веб-страницу для входа пользователя.
* Приложение выдает маркер доступа для API Microsoft Graph.
* Этот маркер доступа будет включен в HTTP-запрос к веб-API.
* Затем обрабатывается ответ Microsoft Graph. 

В этом примере используется библиотека аутентификации Майкрософт для Android (MSAL), которая помогает координировать и выполнять аутентификацию. MSAL автоматически обновляет маркеры, обеспечивает единый вход для других приложений на устройстве, управляет учетными записями, а также обрабатывает большинство случаев условного доступа. 

## <a name="prerequisites"></a>Предварительные требования
* Для этой пошаговой установки необходима среда Android Studio 3.0. 
* Android 21 или более поздней версии (рекомендуется 25 и более поздней).
* Для этой версии MSAL для Android требуется Google Chrome или браузер, поддерживающий пользовательские вкладки.

## <a name="library"></a>Библиотека

В этом руководстве используется следующая библиотека аутентификации:

|Библиотека|ОПИСАНИЕ|
|---|---|
|[com.microsoft.identity.client](http://javadoc.io/doc/com.microsoft.identity.client/msal)|Библиотека проверки подлинности Майкрософт (MSAL)|
