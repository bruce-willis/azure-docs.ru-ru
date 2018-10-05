---
title: Управление ключами разработки и конечной точки в LUIS
titleSuffix: Azure Cognitive Services
description: После создания на портале Azure ключа конечной точки LUIS присвойте приложению LUIS ключ и получите правильный URL-адрес конечной точки. Используйте этот URL-адрес конечной точки, чтобы получать прогнозирование LUIS.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 62081f96e2081833eb705992914899a6764bd792
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47033215"
---
# <a name="add-an-azure-luis-resource-to-app"></a>Добавление в приложение ресурса LUIS Azure

После создания на портале Azure ресурса LUIS назначьте приложению LUIS ресурс и получите правильный URL-адрес конечной точки. Используйте этот URL-адрес конечной точки, чтобы получать прогнозирование LUIS.

<a name="programmatic-key" ></a>
<a name="authoring-key" ></a>
<a name="endpoint-key" ></a>
<a name="use-endpoint-key-in-query" ></a>
<a name="api-usage-of-ocp-apim-subscription-key" ></a>
<a name="key-limits" ></a>
<a name="key-limit-errors" ></a>
<a name="key-concepts"></a>
<a name="authoring-key"></a>
<a name="create-and-use-an-endpoint-key"></a>
<a name="assign-endpoint-key"></a>

## <a name="assign-resource"></a>Присвоение ресурса

1. Создайте ключ LUIS на [портале Azure](https://portal.azure.com). Дальнейшие инструкции см. в разделе [Управление ключами подписки конечной точки Azure](luis-how-to-azure-subscription.md).
 
2. Выберите **Управление**в верхнем правом меню, а затем выберите **Keys and endpoints** (Ключи и конечные точки).

    [ ![Страница Keys and endpoints](./media/luis-manage-keys/keys-and-endpoints.png) ](./media/luis-manage-keys/keys-and-endpoints.png#lightbox) (Ключи и конечные точки)

3. Чтобы добавить LUIS, выберите **Assign Resource +** (Присвоить ресурс +).

    ![Присвоение ресурса приложению](./media/luis-manage-keys/assign-key.png)

4. В диалоговом окне выберите клиент, связанный с адресом электронной почты, с помощью которой вы входите на веб-сайт LUIS.  

5. Выберите **Имя подписки**, связанное с ресурсом Azure, который вы хотите добавить.

6. Выберите **LUIS resource name** (Имя ресурса LUIS). 

7. Выберите **Assign resource** (Присвоить ресурс). 

8. Найдите в таблице новую строку и скопируйте URL-адрес конечной точки. Он построен правильно для того, чтобы отправлять запрос HTTP GET в конечную точку LUIS для прогнозирования. 

<!-- content moved to luis-reference-regions.md, need replacement links-->
<a name="regions-and-keys"></a>
<a name="publishing-to-europe"></a>
<a name="publishing-to-australia"></a>

## <a name="unassign-resource"></a>Отменить присвоение ресурса
Когда вы отмените назначение ключа конечной точки, он не удалится из Azure. Он только потеряет связь из LUIS. 

Если ключ конечной точки не назначен или не присвоен приложению, то любой запрос к URL-адресу конечной точки возвращает ошибку: `401 This application cannot be accessed with the current subscription`. 

## <a name="include-all-predicted-intent-scores"></a>Включение всех прогнозируемых оценок намерения
Флажок **Включить все прогнозируемые оценки намерения** позволяет ответить на запрос конечной точки, чтобы включить оценку прогноза для каждого намерения. 

Этот параметр позволяет вашему чат-боту или приложению LUIS принимать программное решение, основанное на оценках возвращенных намерений. Как правило, два верхние намерения являются наиболее интересными. Если верхний показатель намерения — None, ваш чат-бот может задать вопрос, который сделает окончательным выбор между намерением None и другим намерением с высокой оценкой. 

Намерения и их оценки также включены в журналы конечных точек. Вы можете [экспортировать](luis-how-to-start-new-app.md#export-app) эти журналы и проанализировать оценки. 

```JSON
{
  "query": "book a flight to Cairo",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.5223427
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.5223427
    },
    {
      "intent": "BookFlight",
      "score": 0.372391433
    }
  ],
  "entities": []
}
```

## <a name="enable-bing-spell-checker"></a>Включение проверки орфографии Bing 
Флажок **Проверка орфографии Bing** в **настройках URL-адреса конечной точки** позволяет LUIS проверять орфографию перед прогнозированием. Создайте **[ключ API проверки орфографии Bing](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api)**. 

Добавьте параметры строки запроса **spellCheck=true** и **bing-spell-check-subscription-key={YOUR_BING_KEY_HERE}**. Замените `{YOUR_BING_KEY_HERE}` своим ключом проверки правописания Bing.

```JSON
{
  "query": "Book a flite to London?",
  "alteredQuery": "Book a flight to London?",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 0.780123
  },
  "entities": []
}
```


## <a name="publishing-regions"></a>Регионы публикации

Ознакомьтесь с дополнительными сведениями о [регионах](luis-reference-regions.md) публикации, включая публикацию в [Европе](luis-reference-regions.md#publishing-to-europe) и [Австралии](luis-reference-regions.md#publishing-to-australia). Регионы публикации отличаются от регионов разработки. Убедитесь, что вы разрабатываете приложение в регионе разработки, что соответствует региону публикации конечной точки запроса.

## <a name="next-steps"></a>Дополнительная информация

Используйте ключ для публикации приложения на странице **Публикация приложения**. Инструкции по публикации см. в разделе [Публикация приложения](luis-how-to-publish-app.md).

Сведения о разработке приложений LUIS и работе с конечными точками приведены в разделе [Ключи в LUIS](luis-concept-keys.md).