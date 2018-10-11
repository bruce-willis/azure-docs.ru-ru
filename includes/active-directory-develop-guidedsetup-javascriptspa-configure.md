---
title: включение файла
description: включение файла
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: nacanuma
ms.custom: include file
ms.openlocfilehash: 038ea48bedeb31416627f99b38ebb083846747e4
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/05/2018
ms.locfileid: "48843163"
---
## <a name="register-your-application"></a>Регистрация приложения

Есть несколько способов создавать приложения. Выберите один из них:

### <a name="option-1-register-your-application-express-mode"></a>Вариант 1. Регистрация приложения (экспресс-режим).
Теперь вам необходимо зарегистрировать приложение на *портале регистрации приложений Майкрософт*:

1.  Зарегистрируйте свое приложение на [портале регистрации приложений Майкрософт](https://apps.dev.microsoft.com/portal/register-app?appType=singlePageApp&appTech=javascriptSpa&step=configure).
2.  Введите название приложения и адрес электронной почты.
3.  Выберите параметр **Пошаговая установка**.
4.  Следуйте инструкциям, чтобы получить идентификатор приложения. Затем вставьте его в свой код.

### <a name="option-2-register-your-application-advanced-mode"></a>Вариант 2. Регистрация приложения (расширенный режим).

1. Перейдите на [портал регистрации приложений Майкрософт](https://apps.dev.microsoft.com/portal/register-app) для регистрации приложения.
2. Введите название приложения и адрес электронной почты.
3. Убедитесь, что параметр **Пошаговая установка** не выбран.
4.  Щелкните **Добавление платформы** и выберите **Веб**.
5. Добавьте **URL-адрес перенаправления**, соответствующий URL-адресу приложения, в зависимости от веб-сервера. Инструкции по определению и получению URL-адреса перенаправления в Visual Studio и Node см. в разделах ниже.
6. Щелкните **Сохранить**.

> #### <a name="setting-redirect-url-for-node"></a>Настройка URL-адреса перенаправления для Node
> При использовании Node.js порт веб-сервера можно задать в файле *server.js*. В этом руководстве в качестве примера использован порт 30662, но можно применить любой другой доступный порт. Как бы то ни было, выполните инструкции ниже, чтобы настроить URL-адрес перенаправления в сведениях о регистрации приложения.<br/>
> - Вернитесь на *портал регистрации приложения* и укажите для `http://localhost:30662/` значение `Redirect URL`. Или укажите `http://localhost:[port]/`, если вы используете другой номер TCP-порта (где *[port]*  — это номер пользовательского TCP-порта). Затем нажмите кнопку "Сохранить".

<p/>

> #### <a name="visual-studio-instructions-for-obtaining-the-redirect-url"></a>Инструкции для Visual Studio по получению URL-адреса перенаправления
> Чтобы получить URL-адрес перенаправления, выполните следующие действия:
> 1.    Выберите проект в **обозревателе решений** и просмотрите сведения в окне **Свойства**. Если окно **Свойства** не отображается, нажмите клавишу **F4**.
> 2.    Скопируйте значение **URL-адреса** в буфер обмена:<br/> ![Свойства проекта](media/active-directory-develop-guidedsetup-javascriptspa-configure/vs-project-properties-screenshot.png)<br />
> 3.    Вернитесь на *портал регистрации приложения* и вставьте это значение как **URL-адрес перенаправления**, а затем нажмите кнопку **Сохранить**.


#### <a name="configure-your-javascript-spa"></a>Настройка одностраничного приложения JavaScript

1.  Внесите сведения о регистрации приложения в файл `index.html`, созданный во время настройки проекта. В начале вашего файла `index.html` между тегами `<script></script>` вставьте следующий код:

```javascript
var applicationConfig = {
    clientID: "[Enter the application Id here]",
    graphScopes: ["user.read"],
    graphEndpoint: "https://graph.microsoft.com/v1.0/me"
};
```
<ol start="3">
<li>
Замените <code>Enter the application Id here</code> зарегистрированным идентификатором приложения.
</li>
</ol>
