---
title: Обучение и тестирование приложения LUIS в Azure | Документы Майкрософт
description: Используйте службу Language Understanding (LUIS) для постоянной работы над приложением, чтобы усовершенствовать его и улучшить его функции распознавания речи.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2018
ms.author: v-geberr
ms.openlocfilehash: 927d95f6b6fac922d2c3701b8a5124efb18825f6
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/10/2018
ms.locfileid: "37951243"
---
# <a name="test-your-luis-app"></a>Тестирование приложения LUIS
<a name="train-your-app"></a>
[Обучение](luis-how-to-train.md) и [тестирование](luis-concept-test.md) приложения — это итеративный процесс. После обучения приложения LUIS протестируйте его с примерами высказываний на предмет правильного распознавания намерений и сущностей. Если распознавание выполняется неправильно, внесите изменения в приложение, обучите его и протестируйте еще раз. 

<!-- anchors for H2 name changes -->
<a name="test-your-app"></a>
<a name="access-the-test-page"></a>
<a name="interactive-testing"></a>
## <a name="test-an-utterance"></a>Тестирование высказывания

1. Откройте приложение, выбрав его имя на странице **Мои приложения**. 

2. Чтобы открыть выдвигающуюся панель **Test** (Тестирование), щелкните **Test** (Тестировать) на верхней панели приложения.

    ![Страница обучения и тестирования приложения](./media/luis-how-to-interactive-test/test.png)

3. Введите высказывание в текстовом поле и нажмите клавишу ВВОД. На панели **Test** (Тестирование) можно вводить неограниченное количество тестовых высказываний, но только одно высказывание за раз.

4. Высказывание, его основное намерение и оценка добавляются в список высказываний под текстовым полем.

    ![Интерактивное тестирование определяет неправильное намерение](./media/luis-how-to-interactive-test/test-weather-1.png)

## <a name="clear-test-panel"></a>Очистка панели тестирования
Чтобы очистить все введенные тестовые высказывания и их результаты из консоли тестирования, выберите **Start over** (Начать сначала) в левом верхнем углу **панели тестирования**. 

## <a name="close-test-panel"></a>Закрытие панели тестирования
Чтобы закрыть панель **Test** (Тестирование), еще раз нажмите кнопку **Test** (Тестировать).

## <a name="inspect-score"></a>Проверка оценки
Проверка сведений в результате тестирования выполняется на панели **Inspect** (Проверка). 
 
1. На открытой выдвигающейся панели **Test** (Тестирование) нажмите кнопку **Inspect** (Проверить) рядом с высказыванием, которое нужно сравнить. 

    ![Кнопка "Inspect" (Проверить)](./media/luis-how-to-interactive-test/inspect.png)

2. Появится панель **Inspection** (Проверка). На панели находится намерение с высокой оценкой, а также любые идентифицированные сущности. На панели отображается результат выбранного высказывания.

    ![Кнопка "Inspect" (Проверить)](./media/luis-how-to-interactive-test/inspect-panel.png)

## <a name="correct-top-scoring-intent"></a>Исправление намерения с высокой оценкой

1. Если намерение с высокой оценкой неправильное, нажмите кнопку **Изменить**.

2.  В раскрывающемся списке выберите правильное намерение для высказывания.

    ![Выбор правильного намерения](./media/luis-how-to-interactive-test/intent-select.png)

## <a name="view-sentiment-results"></a>Просмотр результатов тональности

Если на странице **[Publish](luis-how-to-publish-app.md#enable-sentiment-analysis)** (Публикация) настроен **анализ тональности**, результаты теста содержат тональность, найденную в высказывании. 

![Изображение панели тестирования с анализом тональности](./media/luis-how-to-interactive-test/sentiment.png)

## <a name="correct-matched-patterns-intent"></a>Исправление соответствующего намерения шаблона
Если при использовании [шаблонов](luis-concept-patterns.md) высказывание совпало с шаблоном, но было спрогнозировано неправильное намерение, щелкните ссылку **Изменить** в шаблоне, а затем выберите правильное намерение.

## <a name="compare-with-published-version"></a>Сравнение с опубликованной версией
Можно протестировать активную версию приложения с опубликованной версией [конечной точки](luis-glossary.md#endpoint). На панели **Inspect** (Проверка) выберите **Compare with published** (Сравнить с опубликованной). Средства на тестирование на соответствие опубликованной модели вычитаются из суммы квоты на подписку Azure. 

![Сравнение с опубликованной версией](./media/luis-how-to-interactive-test/inspect-panel-compare.png)

## <a name="view-endpoint-json-in-test-panel"></a>Просмотр JSON конечной точки на панели тестирования
Чтобы просмотреть JSON конечной точки, возвращенный для сравнения, выберите **Show JSON view** (Показать представление JSON).

![Опубликованный ответ JSON](./media/luis-how-to-interactive-test/inspect-panel-compare-json.png)

<!--Service name is 'Bing Spell Check v7 API' in the portal-->
## <a name="additional-settings-in-test-panel"></a>Дополнительные параметры на панели тестирования

### <a name="luis-endpoint"></a>Конечная точка LUIS
При наличии нескольких конечных точек LUIS щелкните ссылку **Дополнительные параметры** на панели "Published" (Опубликовано), чтобы изменить конечную точку, используемую для тестирования. Если вы не уверены, какую конечную точку выбрать, выберите значение по умолчанию **Starter_Key**. 

![Панель тестирования с выделенной ссылкой "Дополнительные параметры"](./media/luis-how-to-interactive-test/interactive-with-spell-check-service-key.png)


### <a name="view-bing-spell-check-corrections-in-test-panel"></a>Просмотр проверки орфографии Bing на панели тестирования
Требования к просмотру проверки орфографии: 

* опубликованное приложение;
* [ключ службы](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api) проверки орфографии Bing. Ключ службы не сохраняется и должен быть сброшен для каждого сеанса браузера. 

Чтобы включить службу [проверки орфографии Bing версии 7](https://azure.microsoft.com/services/cognitive-services/spell-check/) в результаты на панели тестирования, выполните следующую процедуру. 

1. На панели **Test** (Тестирование) введите высказывание. После прогнозирования высказывания нажмите кнопку **[Inspect](#inspect-score)** (Проверить) под введенным высказыванием. 

2. На открывшейся панели **Inspect** (Проверка) выберите **[Compare with published](#compare-with-published-version)** (Сравнить с опубликованной). 

3. На открывшейся панели **Published** (Опубликовано) щелкните ссылку **[Дополнительные параметры](#additional-settings-in-test-panel)**.

4. Во всплывающем диалоговом окне введите ключ службы **проверки орфографии Bing**. 
    ![Ввод ключа службы проверки орфографии Bing](./media/luis-how-to-interactive-test/interactive-with-spell-check-service-key.png)

5. Введите запрос с орфографическими ошибками, например `book flite to seattle`, и нажмите клавишу ВВОД. Неправильно написанное слово `flite` заменяется в запросе, отправляемом в LUIS, и в итоговом JSON отображается исходный запрос в виде `query` и исправленное написание в запросе в виде `alteredQuery`.

    ![JSON с исправленными орфографическими ошибками](./media/luis-how-to-interactive-test/interactive-with-spell-check-results.png)

<a name="json-file-with-no-duplicates"></a>
<a name="import-a-dataset-file-for-batch-testing"></a>
<a name="export-rename-delete-or-download-dataset"></a>
<a name="run-a-batch-test-on-your-trained-app"></a>
<a name="access-batch-test-result-details-in-a-visualized-view"></a>
<a name="filter-chart-results-by-intent-or-entity"></a>
<a name="investigate-false-sections"></a>
<a name="view single-point utterance data"></a>
<a name="relabel-utterances-and-retrain"></a>
<a name="false-test-results"></a>
## <a name="batch-testing"></a>Пакетное тестирование
Ознакомьтесь с [принципами](luis-concept-batch-test.md) пакетного тестирования и узнайте, [как](luis-how-to-batch-test.md) протестировать пакет высказываний.

## <a name="next-steps"></a>Дополнительная информация

Если тестирование показывает, что приложение LUIS не распознает правильные намерения и сущности, вы можете повысить точность приложения LUIS путем пометки дополнительных высказываний или добавления функций. 

* [Label suggested utterances with LUIS](luis-how-to-review-endoint-utt.md) (Пометка предлагаемых высказываний в LUIS) 
* [Use features to improve your LUIS app's performance](luis-how-to-add-features.md) (Использование функций для повышения производительности приложения LUIS) 
