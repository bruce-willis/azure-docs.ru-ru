---
title: Модерация контента Facebook с помощью Azure Content Moderator | Документация Майкрософт
description: Модерация страниц Facebook с помощью машинного обучения и Content Moderator.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 09/18/2017
ms.author: sajagtap
ms.openlocfilehash: 316c7212c30e9fe1151cdf5ceabf875439ad4c65
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35380336"
---
# <a name="facebook-content-moderation-with-content-moderator"></a>Модерация контента Facebook с помощью Azure Content Moderator

В этом руководстве вы узнаете, как использовать машинное обучение и Content Moderator для модерации комментариев и записей на Facebook.

В данном руководстве описываются следующие шаги:

1. создание команды Content Moderator;
2. создание приложения-функции Azure, которое ожидает передачи событий HTTP из Content Moderator и Facebook.
3. создание страницы и приложения Facebook и их подключение к Content Moderator.

После этого Facebook будет отправлять содержимое, опубликованное посетителями, в Content Moderator. На основе соответствия порогам рабочие процессы Content Moderator будут публиковать это содержимое или создать проверки в инструменте проверки. 

На следующем рисунке показаны стандартные блоки решения.

![Модерация записей на Facebook](images/tutorial-facebook-moderation.png)

## <a name="create-a-content-moderator-team"></a>Создание команды Content Moderator

Обратитесь к странице [краткого руководства](quick-start.md), чтобы зарегистрироваться в Content Moderator и создать команду.

## <a name="configure-image-moderation-workflow-threshold"></a>Настройка рабочего процесса модерации изображений (порога)

Перейдите на страницу [Workflows](review-tool-user-guide/workflows.md) (Рабочие процессы), чтобы настроить пользовательский рабочий процесс для изображений (порог). Назовите этот рабочий процесс **name**.

## <a name="3-configure-text-moderation-workflow-threshold"></a>3. Настройка рабочего процесса модерации текста (порога)

Выполните аналогичные действия на странице [Workflows](review-tool-user-guide/workflows.md) (Рабочие процессы), чтобы настроить пользовательский порог и рабочий процесс для текста. Назовите этот рабочий процесс **name**.

![Настройка рабочего процесса для текста](images/text-workflow-configure.PNG)

Протестируйте рабочий процесс с помощью кнопки "Execute Workflow" (Выполнить рабочий процесс).

![Тестирование рабочего процесса для текста](images/text-workflow-test.PNG)

## <a name="create-azure-functions"></a>Создание приложения-функции Azure

Войдите в [портал управления Azure](https://portal.azure.com/) для создания приложения-функции Azure. Выполните следующие действия.

1. Создайте приложение-функцию Azure, как показано на странице [Создание приложения-функции на портале Azure](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal).
2. Откройте только что созданное приложение-функцию.
3. В приложении выберите **Функции платформы > Параметры приложения**.
4. Задайте следующие [параметры приложения](https://docs.microsoft.com/azure/azure-functions/functions-how-to-use-azure-function-app-settings#settings).

> [!NOTE]
> Значением **cm:Region** должно быть имя региона (без пробелов).
> Например, **westeurope**, а не Западная Европа, **westcentralus**, а не центрально-западная часть США, и т. д.
>

| Параметр приложения | ОПИСАНИЕ   | 
| -------------------- |-------------|
| cm:TeamId   | Идентификатор команды Content Moderator.  | 
| cm:SubscriptionKey | Ключ подписки Content Moderator (см. раздел [Учетные данные](/review-tool-user-guide/credentials.md)). | 
| cm:Region | Имя региона Content Moderator без пробелов. См. примечание выше. |
| cm:ImageWorkflow | Имя рабочего процесса для изображений. |
| cm:TextWorkflow | Имя рабочего процесса для текста. |
| cm:CallbackEndpoint | URL-адрес приложения-функции CMListener, которое будет создано позже в этом руководстве. |
| fb:VerificationToken | Маркер секрета, также используется для подписки на события веб-канала Facebook. |
| fb:PageAccessToken | Срок действия маркера доступа API Graph Facebook не истекает, что позволяет функции скрывать или удалять записи от вашего имени. |

5. Создайте функцию **HttpTrigger-CSharp** с именем **FBListener**. Эта функция получает события от Facebook. Создайте эту функцию, выполнив следующие действия.

    1. Не закрывайте страницу [Создание приложения-функции на портале Azure](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal), чтобы сверяться с ней.
    2. Щелкните "**+** Добавить", чтобы создать функцию.
    3. Вместо встроенных шаблонов выберите **Get started on your own/custom function** (Приступить к созданию собственной или пользовательской функции).
    4. Щелкните элемент **HttpTrigger-CSharp**.
    5. Введите имя **FBListener**. В поле **Уровень авторизации** нужно указать значение **Функция**.
    6. Нажмите кнопку **Создать**.
    7. Замените содержимое **run.csx** содержимым из [**FbListener/run.csx**](https://github.com/MicrosoftContentModerator/samples-fbPageModeration/blob/master/FbListener/run.csx).

6. Создайте функцию **HttpTrigger-CSharp** с именем **CMListener**. Эта функция получает события от Facebook. Чтобы создать эту функцию, сделайте следующее.

    1. Не закрывайте страницу [Создание приложения-функции на портале Azure](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal), чтобы сверяться с ней.
    2. Щелкните "**+** Добавить", чтобы создать функцию.
    3. Вместо встроенных шаблонов выберите **Get started on your own/custom function** (Приступить к созданию собственной или пользовательской функции).
    4. Щелкните элемент **HttpTrigger-CSharp**.
    5. Введите имя **CMListener**. В поле **Уровень авторизации** нужно указать значение **Функция**.
    6. Нажмите кнопку **Создать**.
    7. Замените содержимое **run.csx** содержимым из [**CMListener/run.csx**](https://github.com/MicrosoftContentModerator/samples-fbPageModeration/blob/master/CmListener/run.csx).

## <a name="configure-the-facebook-page-and-app"></a>Настройка страницы и приложения Facebook
1. Создайте приложение Facebook.

    1. Перейдите на [сайт разработчиков Facebook](https://developers.facebook.com/).
    2. Щелкните **Мои приложения**.
    3. Добавьте новое приложение.
    4. Выберите **Webhooks (Веб-перехватчики) > Get Started (Приступая к работе)**.
    5. Выберите **Page (Страница) > Subscribe to this topic (Подписаться на этот раздел)**.
    6. Укажите **URL-адрес FBListener** в качестве URL-адреса обратного вызова и введите **маркер проверки**, который был настроен в **параметрах функции-приложения**.
    7. Создав подписку, прокрутите страницу вниз до веб-канала и выберите **subscribe** (Подписаться).

2. Создайте страницу Facebook.

    1. Перейдите на сайт [Facebook](https://www.facebook.com/bookmarks/pages) и создайте **страницу Facebook**.
    2. Разрешите приложению Facebook доступ к этой странице, выполнив следующие действия.
        1. Перейдите к [Graph API Explorer](https://developers.facebook.com/tools/explorer/).
        2. Выберите **Приложение**.
        3. Выберите **Page Access Token** (Маркер доступа к странице) и отправьте запрос **Get**.
        4. Щелкните **Page ID** (Идентификатор страницы) в ответе.
        5. Теперь добавьте **/subscribed_apps** в URL-адрес и отправьте запрос **Get** (пустой ответ).
        6. Отправьте запрос **Post**. Вы получите ответ **success: true**.

3. Создайте бессрочный маркер доступа API Graph.

    1. Перейдите к [Graph API Explorer](https://developers.facebook.com/tools/explorer/).
    2. Выберите **Приложение**.
    3. Выберите **Get User Access Token** (Получить маркер доступа пользователя).
    4. В разделе **Select Permissions** (Выбор разрешений) выберите параметры **manage_pages** и **publish_pages**.
    5. Мы будем использовать **маркер доступа** (краткосрочный маркер) на следующем шаге.

4. На нескольких следующих шагах мы используем Postman.

    1. Откройте **Postman** (или получите его [здесь](https://www.getpostman.com/)).
    2. Импортируйте следующие два файла:
        1. [коллекция Postman](https://github.com/MicrosoftContentModerator/samples-fbPageModeration/blob/master/Facebook%20Permanant%20Page%20Access%20Token.postman_collection.json);
        2. [среда Postman](https://github.com/MicrosoftContentModerator/samples-fbPageModeration/blob/master/FB%20Page%20Access%20Token%20Environment.postman_environment.json).       
    3. Обновите следующие переменные среды.
    
    | Ключ | Значение   | 
    | -------------------- |-------------|
    | appId   | Вставьте идентификатор приложения Facebook.  | 
    | appSecret | Вставьте секрет приложения Facebook. | 
    | short_lived_token | Вставьте краткосрочный маркер доступа пользователя, созданный на предыдущем шаге. |
    4. Теперь запустите 3 интерфейса API, перечисленные в коллекции. 
        1. Выберите **Generate Long-Lived Access Token** (Создать долгосрочный маркер доступа) и нажмите кнопку **Отправить**.
        2. Выберите **Get User ID** (Получить идентификатор пользователя) и нажмите кнопку **Отправить**.
        3. Выберите **Get Permanent Page Access Token** (Получить бессрочный маркер доступа к странице) и нажмите кнопку **Отправить**.
    5. Скопируйте значение **access_token** из ответа и присвойте его параметру приложения **fb:PageAccessToken**.

Вот и все!

Решение отправляет все изображения и тексты, опубликованные на странице Facebook, в Content Moderator. При этом вызываются рабочие процессы, которые вы задали ранее. Для содержимого, которое не удовлетворяет критериям, определенным в рабочих процессах, создаются проверки в инструменте проверки. Остальное содержимое публикуется.

## <a name="license"></a>Лицензия

Все пакеты SDK и примеры для Microsoft Cognitive Services регулируются лицензией MIT. Дополнительные сведения см. в разделе о [лицензировании](https://microsoft.mit-license.org/).

## <a name="developer-code-of-conduct"></a>Правила поведения для разработчиков

Ожидается, что разработчики, использующие Cognitive Services, включая эту клиентскую библиотеку и пример, следуют правилам поведения разработчиков для служб Microsoft Cognitive Services, доступным здесь: http://go.microsoft.com/fwlink/?LinkId=698895.

## <a name="next-steps"></a>Дополнительная информация

1. [Просмотрите демонстрационное видео](https://channel9.msdn.com/Events/Build/2017/T6033) о данном решении из Microsoft Build 2017.
1. Изучите [пример Facebook на сайте GitHub](https://github.com/MicrosoftContentModerator/samples-fbPageModeration).
1. https://docs.microsoft.com/azure/azure-functions/functions-create-github-webhook-triggered-function
2. http://ukimiawz.github.io/facebook/2015/08/12/webhook-facebook-subscriptions/
3. http://stackoverflow.com/questions/17197970/facebook-permanent-page-access-token
