---
title: включение файла
description: включение файла
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: nacanuma
ms.custom: include file
ms.openlocfilehash: eead4c6a66a317c7404205415cbf04c442ffe8d1
ms.sourcegitcommit: 715813af8cde40407bd3332dd922a918de46a91a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47060743"
---
## <a name="add-the-applications-registration-information-to-your-app"></a>Добавление в приложение сведений о его регистрации

На этом шаге необходимо настроить URL-адрес перенаправления сведений о регистрации приложения, а затем добавить идентификатор приложения в одностраничное приложение JavaScript.

### <a name="configure-redirect-url"></a>Настройка URL-адреса перенаправления

Настройте поле `Redirect URL`, используя URL-адрес страницы index.html на основе веб-сервера, а затем нажмите кнопку *Обновить*.


> #### <a name="visual-studio-instructions-for-obtaining-the-redirect-url"></a>Инструкции для Visual Studio по получению URL-адреса перенаправления
> Чтобы получить URL-адрес перенаправления, выполните следующие действия:
> 1.    Выберите проект в **обозревателе решений** и просмотрите сведения в окне **Свойства**. Если окно **Свойства** не отображается, нажмите клавишу **F4**.
> 2.    Скопируйте значение **URL-адреса** в буфер обмена:<br/> ![Свойства проекта](media/active-directory-develop-guidedsetup-javascriptspa-configure/vs-project-properties-screenshot.png)<br />
> 3.    Также вставьте это значение в качестве **URL-адреса перенаправления** в верхней части этой страницы, а затем нажмите кнопку **Обновить**.

<p/>

> #### <a name="setting-redirect-url-for-node"></a>Настройка URL-адреса перенаправления для Node.js
> При использовании Node.js порт веб-сервера можно задать в файле *server.js*. В этом руководстве в качестве примера использован порт 30662, но можно применить любой другой доступный порт. Выполните инструкции ниже, чтобы указать URL-адрес перенаправления в сведениях о регистрации приложения.<br/>
> В верхней части этой страницы укажите `http://localhost:30662/` в качестве **URL-адреса перенаправления** или воспользуйтесь `http://localhost:[port]/`, если используете настраиваемый TCP-порт (где *[port]* — это номер настраиваемого TCP-порта), а затем нажмите кнопку **Обновить**.

### <a name="configure-your-javascript-spa-application"></a>Настройка одностраничного приложения JavaScript

1.  Внесите сведения о регистрации приложения в файл `index.html`, созданный во время настройки проекта. В начале вашего файла `index.html` между тегами `<script></script>` вставьте следующий код:

```javascript
var applicationConfig = {
    clientID: "[Enter the application Id here]",
    graphScopes: ["user.read"],
    graphEndpoint: "https://graph.microsoft.com/v1.0/me"
};
```
