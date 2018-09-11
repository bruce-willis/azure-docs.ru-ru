---
title: Краткое руководство по вызову приложения службы "Распознавание речи" (LUIS) с использованием Python | Документация Майкрософт
description: Из этого краткого руководства вы узнаете, как вызвать приложение LUIS с помощью Python.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 06/27/2018
ms.author: diberry
ms.openlocfilehash: bc7ae912d762a98c34b9a1b2d6a82d5630c4794b
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/24/2018
ms.locfileid: "43771754"
---
# <a name="quickstart-call-a-luis-endpoint-using-python"></a>Краткое руководство по вызову конечной точки LUIS с помощью Python
Из этой статьи вы узнаете, как передать фразы в конечную точку LUIS и получить намерение и сущности.

<!-- green checkmark -->
<!--
> [!div class="checklist"]
> * Create LUIS subscription and copy key value for later use
> * View LUIS endpoint results from browser to public sample IoT app
> * Create Visual Studio C# console app to make HTTPS call to LUIS endpoint
-->

Для работы с этой статьей требуется бесплатная учетная запись [LUIS](luis-reference-regions.md#luis-website), в которой вы создадите приложение LUIS.

<a name="create-luis-subscription-key"></a>
## <a name="create-luis-endpoint-key"></a>Создание ключа конечной точки LUIS
Ключ API Cognitive Services нужен для отправки вызовов к примеру приложения LUIS, используемому в этом пошаговом руководстве. 

Чтобы получить ключ API, сделайте следующее: 

1. Сначала необходимо создать [учетную запись API-интерфейсов Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) на портале Azure. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

2. Войдите на портал Azure по адресу https://portal.azure.com. 

3. Получите ключ, следуя указаниям из статьи о [создании ключей конечных точек с помощью Azure](./luis-how-to-azure-subscription.md).

4. Вернитесь на веб-сайт [LUIS](luis-reference-regions.md) и выполните вход, используя свою учетную запись Azure. 

    [![](media/luis-get-started-node-get-intent/app-list.png "Снимок экрана со списком приложений")](media/luis-get-started-node-get-intent/app-list.png)

## <a name="understand-what-luis-returns"></a>Возвращаемые LUIS результаты

Чтобы понять, что возвращает приложение LUIS, можно вставить URL-адрес примера приложения LUIS в окне браузера. Пример приложения — это приложение Интернета, которое определяет, что хочет сделать пользователь: включить или выключить освещение.

1. Конечная точка примера приложения имеет следующий формат: `https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?subscription-key=<YOUR_API_KEY>&verbose=false&q=turn%20on%20the%20bedroom%20light`. Скопируйте URL-адрес и замените значение ключа конечной точки значением поля `subscription-key`.
2. Вставьте URL-адрес в окне браузера и нажмите клавишу ВВОД. В браузере отображается результат JSON, который указывает, что LUIS определяет намерение `HomeAutomation.TurnOn` и сущность `HomeAutomation.Room` со значением `bedroom`.

    ![Результат JSON, указывающий, что определено намерение включить освещение](./media/luis-get-started-node-get-intent/turn-on-bedroom.png)
3. Измените значение параметра `q=` в URL-адресе на `turn off the living room light` и нажмите клавишу ВВОД. Теперь результат указывает, что приложение LUIS определило намерение `HomeAutomation.TurnOff` и сущность `HomeAutomation.Room` со значением `living room`. 

    ![Результат JSON, указывающий, что определено намерение выключить освещение](./media/luis-get-started-node-get-intent/turn-off-living-room.png)


## <a name="consume-a-luis-result-using-the-endpoint-api-with-python"></a>Получение результата LUIS с помощью API конечной точки с использованием Python

С помощью Python можно получить доступ к тем же результатам, которые вы уже видели в окне браузера на предыдущем шаге.

1. Скопируйте приведенный ниже фрагмент кода в файл с именем `quickstart-call-endpoint.py`.

   [!code-python[Console app code that calls a LUIS endpoint for Python 2.7](~/samples-luis/documentation-samples/quickstarts/analyze-text/python/2.x/quickstart-call-endpoint-2-7.py)]

   [!code-python[Console app code that calls a LUIS endpoint for Python 3.6](~/samples-luis/documentation-samples/quickstarts/analyze-text/python/3.x/quickstart-call-endpoint-3-6.py)]

2. Замените значение поля `Ocp-Apim-Subscription-Key` своим ключом к конечной точке LUIS.

3. Установите зависимости с помощью команды `pip install requests`.

4. Запустите скрипт с помощью команды `python ./quickstart-call-endpoint.py`. Отобразится тот же результат JSON, который вы видели ранее в окне браузера.
<!-- 
![Console window displays JSON result from LUIS](./media/luis-get-started-python-get-intent/console-turn-on.png)
-->

## <a name="clean-up-resources"></a>Очистка ресурсов
В этом руководстве мы создали два ресурса: ключ конечной точки LUIS и проект Python. Удалите ключ конечной точки LUIS с портала Azure. Закройте проект Visual Studio и удалите каталог из файловой системы. 

## <a name="next-steps"></a>Дополнительная информация
> [!div class="nextstepaction"]
> [Добавление высказываний](luis-get-started-python-add-utterance.md)