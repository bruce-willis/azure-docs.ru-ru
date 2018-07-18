---
title: Руководство по вызову приложения Language Understanding Intelligent Services (LUIS) с использованием Node.js | Документация Майкрософт
description: В этом руководстве вы узнаете, как вызвать приложение LUIS с помощью Node.js.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 12/13/2017
ms.author: v-geberr
ms.openlocfilehash: 5e9df272be3c48602d38f551a791376c043bf409
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36263658"
---
# <a name="tutorial-call-a-luis-endpoint-using-nodejs"></a>Руководство по вызову конечной точки LUIS с помощью Node.js
В этой статье вы узнаете, как передать фразы в конечную точку LUIS и получить намерение и сущности.

<!-- green checkmark -->
> [!div class="checklist"]
> * Создание подписки LUIS и копирование значения ключа для последующего использования.
> * Просмотр результатов конечной точки LUIS в браузере, вставив URL-адрес общедоступного примера приложения Интернета вещей.
> * Создание консольного приложения Visual Studio C#, отправляющего вызов HTTPS к конечной точке LUIS.

Для работы с этой статьей требуется бесплатная учетная запись [LUIS][LUIS], в которой вы разработаете приложение LUIS.

## <a name="create-luis-subscription-key"></a>Создание ключа подписки LUIS
Ключ API Cognitive Services нужен для отправки вызовов к примеру приложения LUIS, используемому в этом пошаговом руководстве. 

Чтобы получить ключ API, сделайте следующее: 

1. Сначала необходимо создать [учетную запись API-интерфейсов Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) на портале Azure. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

2. Войдите на портал Azure по адресу https://portal.azure.com. 

3. Получите ключ, следуя указаниям в статье по [созданию ключей подписки с помощью Azure](./luis-how-to-azure-subscription.md).

4. Вернитесь на веб-сайт [LUIS](luis-reference-regions.md) и выполните вход, используя свою учетную запись Azure. 

    [![](media/luis-get-started-node-get-intent/app-list.png "Снимок экрана со списком приложений")](media/luis-get-started-node-get-intent/app-list.png)

## <a name="understand-what-luis-returns"></a>Возвращаемые LUIS результаты

Чтобы понять, что возвращает приложение LUIS, можно вставить URL-адрес примера приложения LUIS в окне браузера. Пример приложения — это приложение Интернета, которое определяет, что хочет сделать пользователь: включить или выключить освещение.

1. Конечная точка примера приложения имеет следующий формат: `https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?subscription-key=<YOUR_API_KEY>&verbose=false&q=turn%20on%20the%20bedroom%20light`. Скопируйте URL-адрес и замените значение ключа подписки на значение поля `subscription-key`.
2. Вставьте URL-адрес в окне браузера и нажмите клавишу ВВОД. В браузере отображается результат JSON, который указывает, что LUIS определяет намерение `HomeAutomation.TurnOn` и сущность `HomeAutomation.Room` со значением `bedroom`.

    ![Результат JSON, указывающий, что определено намерение включить освещение](./media/luis-get-started-node-get-intent/turn-on-bedroom.png)
3. Измените значение параметра `q=` в URL-адресе на `turn off the living room light` и нажмите клавишу ВВОД. Теперь результат указывает, что приложение LUIS определило намерение `HomeAutomation.TurnOff` и сущность `HomeAutomation.Room` со значением `living room`. 

    ![Результат JSON, указывающий, что определено намерение выключить освещение](./media/luis-get-started-node-get-intent/turn-off-living-room.png)


## <a name="consume-a-luis-result-using-the-endpoint-api-with-nodejs"></a>Получение результата LUIS с помощью API конечной точки с использованием Node.js

С помощью Node.js можно получить доступ к тем же результатам, которые вы уже видели в окне браузера на предыдущем шаге.

1. Скопируйте следующий фрагмент кода.

   [!code-nodejs[Console app code that calls a LUIS endpoint for Node.js](~/samples-luis/documentation-samples/endpoint-api-samples/node/call-endpoint.js)]

2. Задайте переменную среды `LUIS_APP_ID`, как описано в комментариях к коду. 

3. Задайте для переменной среды `LUIS_SUBSCRIPTION_KEY` ключ подписки Cognitive Services.

4. Выполните код. Отобразятся те же значения, которые вы видели ранее в окне браузера.
<!-- 
![Console window displays JSON result from LUIS](./media/luis-get-started-Node.js-get-intent/console-turn-on.png)
-->

## <a name="clean-up-resources"></a>Очистка ресурсов
При работе с этим руководством вы создали два ресурса: ключ подписки LUIS и проект C#. Удалите ключ подписки LUIS на портале Azure. Закройте проект Visual Studio и удалите каталог из файловой системы. 

## <a name="next-steps"></a>Дополнительная информация
> [!div class="nextstepaction"]
> [Добавление высказываний](luis-get-started-node-add-utterance.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website