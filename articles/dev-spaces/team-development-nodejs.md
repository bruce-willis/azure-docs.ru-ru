---
title: Коллективная разработка с помощью Azure Dev Spaces и VS Code | Документация Майкрософт
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 07/09/2018
ms.topic: tutorial
description: Быстрая разработка в Kubernetes с использованием контейнеров и микрослужб в Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers
manager: douge
ms.openlocfilehash: 6993712e6de3eb932c4d1e3331ff6028463ad0ec
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/15/2018
ms.locfileid: "41920590"
---
# <a name="team-development-with-azure-dev-spaces"></a>Коллективная разработка с помощью Azure Dev Spaces

В этом руководстве описано, как использовать несколько пространств разработки для параллельной работы в разных средах разработки, когда отдельные проекты выполняются в отдельных пространствах в пределах одного кластера.

## <a name="call-a-service-running-in-a-separate-container"></a>Вызов службы, запущенной в отдельном контейнере

В этом разделе вы создадите вторую службу, `mywebapi`, к которой будет обращаться `webfrontend`. Каждая служба будет выполняться в отдельных контейнерах. Затем вы выполните отладку в обоих контейнерах.

![](media/common/multi-container.png)

### <a name="open-sample-code-for-mywebapi"></a>Открытие примера кода для *mywebapi*.
У вас уже должен быть пример кода для `mywebapi` в рамках этого руководства. Он находится в папке `samples` (в противном случае перейдите по адресу https://github.com/Azure/dev-spaces и выберите **Clone or Download** (Клонировать или загрузить), чтобы загрузить репозиторий GitHub). Код для этого раздела находится в папке `samples/nodejs/getting-started/mywebapi`.

### <a name="run-mywebapi"></a>Запуск *mywebapi*
1. Откройте папку `mywebapi` в *отдельном окне VS Code*.
1. Откройте **палитру команд** (с помощью меню **Вид | Палитра команд**), включите автоматическое завершение ввода и выберите эту команду: `Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces`. 
1. Нажмите клавишу F5 и подождите, пока выполнится сборка и развертывание службы. Когда все будет готово, откроется панель отладки VS Code.
1. Запишите URL-адрес конечной точки. Он будет иметь приблизительно такой вид: http://localhost:\<portnumber\>. **Подсказка. В строке состояния VS Code отобразится URL-адрес, который можно щелкнуть.** Может показаться, что контейнер выполняется локально, но фактически он выполняется в вашей среде разработки в Azure. В адресе указано localhost, потому что для `mywebapi` не определены общедоступные конечные точки и доступ осуществляется только в пределах экземпляра Kubernetes. Для вашего удобства и упрощения взаимодействия с закрытой службой на локальном компьютере служба Azure Dev Spaces создает временный туннель SSH для контейнера, запущенного в Azure.
1. Когда `mywebapi` будет готово, откройте в браузере адрес localhost. Вы увидите ответ от службы `mywebapi` ("Hello from mywebapi").


### <a name="make-a-request-from-webfrontend-to-mywebapi"></a>Запрос из *webfrontend* к *mywebapi*
Давайте теперь напишем код в `webfrontend`, который отправляет запрос к `mywebapi`.
1. Перейдите в окно VS Code для `webfrontend`.
1. Добавьте следующие строки кода в верхней части `server.js`:
    ```javascript
    var request = require('request');
    ```

3. *Замените* код для обработчика GET `/api`. При обработке запроса он, в свою очередь, обращается к `mywebapi`, а затем возвращает результаты из обеих служб.

    ```javascript
    app.get('/api', function (req, res) {
       request({
          uri: 'http://mywebapi',
          headers: {
             /* propagate the dev space routing header */
             'azds-route-as': req.headers['azds-route-as']
          }
       }, function (error, response, body) {
           res.send('Hello from webfrontend and ' + body);
       });
    });
    ```

В предыдущем примере код перенаправляет заголовок `azds-route-as` из входящего запроса в исходящий. Позже вы увидите, как это помогает повысить производительность при коллективной разработке.

### <a name="debug-across-multiple-services"></a>Отладка в нескольких службах
1. На этом этапе служба `mywebapi` по-прежнему должна выполняться с подключенным отладчиком. Если это не так, нажмите клавишу F5 в проекте `mywebapi`.
1. Установите точку останова в обработчике GET `/` по умолчанию.
1. В проекте `webfrontend` установите точку останова до отправки запроса GET к `http://mywebapi`.
1. Нажмите клавишу F5 в проекте `webfrontend`.
1. Откройте веб-приложение и выполните пошаговое прохождение кода в обеих службах. В веб-приложении должно отобразиться объединенное сообщение от двух служб: "Hello from webfrontend and Hello from mywebapi".

Все готово! Теперь у вас есть многоконтейнерное приложение, где каждый контейнер можно разрабатывать и развертывать отдельно.

## <a name="learn-about-team-development"></a>Сведения о коллективной разработке

[!INCLUDE[](includes/team-development-1.md)]

Давайте посмотрим на это в действии.
1. Перейдите в окно VS Code для `mywebapi` и измените код в обработчике GET `/` по умолчанию, например:

    ```javascript
    app.get('/', function (req, res) {
        res.send('mywebapi now says something new');
    });
    ```

[!INCLUDE[](includes/team-development-2.md)]

[!INCLUDE[](includes/well-done.md)]

[!INCLUDE[](includes/clean-up.md)]




