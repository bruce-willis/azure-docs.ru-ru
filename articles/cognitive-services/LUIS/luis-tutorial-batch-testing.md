---
title: Улучшение прогнозирований LUIS с помощью пакетного тестирования | Документы Майкрософт
titleSuffix: Azure
description: Выполняйте пакетное тестирование, просматривайте результаты и улучшайте прогнозирования LUIS с помощью изменений.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/19/2018
ms.author: v-geberr
ms.openlocfilehash: 5788f17f2724a0354a1db506971c2343c1800f01
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36266402"
---
# <a name="use-batch-testing-to-find-prediction-accuracy-issues"></a>Использование пакетного тестирования для поиска ошибок точности прогнозирования

В этом руководстве содержатся сведения по использованию пакетного тестирования для поиска проблем прогнозирования.  

Из этого руководства вы узнаете, как выполнять такие задачи:

> [!div class="checklist"]
* Создание файла пакетного теста 
* Выполнение пакетного теста
* Просмотр результатов теста
* Исправление ошибок для намерений
* Повторное тестирование пакета

## <a name="prerequisites"></a>предварительным требованиям

> [!div class="checklist"]
> * Для работы с этой статьей требуется бесплатная учетная запись [LUIS][LUIS], чтобы создать приложение LUIS.

> [!Tip]
> Если у вас еще нет подписки, вы можете зарегистрироваться для получения [бесплатной учетной записи](https://azure.microsoft.com/free/).

## <a name="create-new-app"></a>Создание приложения
В этой статье используется готовая предметная область HomeAutomation. В ней есть намерения, сущности и высказывания для управления устройствами HomeAutomation, такими как осветительные приборы. Создайте приложение, добавьте предметную область, обучите и опубликуйте приложение.

1. На веб-сайте [LUIS] создайте приложение, выбрав **Создать приложение** на странице **MyApps**. 

    ![Создание приложения](./media/luis-tutorial-batch-testing/create-app-1.png)

2. В диалоговом окне введите имя `Batchtest-HomeAutomation`.

    ![Ввод имени приложения](./media/luis-tutorial-batch-testing/create-app-2.png)

3. В левом нижнем углу щелкните **Prebuilt Domains** (Готовые предметные области). 

    ![Выбор готовой предметной области](./media/luis-tutorial-batch-testing/prebuilt-domain-1.png)

4. Нажмите кнопку **Add Domain** (Добавить предметную область) для HomeAutomation.

    ![Добавление предметной области HomeAutomation](./media/luis-tutorial-batch-testing/prebuilt-domain-2.png)

5. Выберите **Train** (Обучить) на правой верхней панели навигации.

    ![Выбор кнопки обучения](./media/luis-tutorial-batch-testing/train-button.png)

## <a name="batch-test-criteria"></a>Условия пакетного теста
При пакетном тестировании можно проверять до 1000 высказываний одновременно. Пакет не должен иметь дубликатов. [Экспортируйте](create-new-app.md#export-app) приложение, чтобы просмотреть список текущих высказываний.  

В стратегии тестирования LUIS используется три отдельных набора данных: высказывания модели, высказывания пакетного теста и высказывания конечной точки. При работе с этим руководством убедитесь, что вы не используете высказывания из высказываний модели (добавленные в намерение) или высказываний конечной точки. 

Не используйте высказывания, которые уже имеются в приложении для пакетного тестирования:

```
'breezeway on please',
'change temperature to seventy two degrees',
'coffee bar on please',
'decrease temperature for me please',
'dim kitchen lights to 25 .',
'fish pond off please',
'fish pond on please',
'illuminate please',
'living room lamp on please',
'living room lamps off please',
'lock the doors for me please',
'lower your volume',
'make camera 1 off please',
'make some coffee',
'play dvd',
'set lights bright',
'set lights concentrate',
'set lights out bedroom',
'shut down my work computer',
'silence the phone',
'snap switch fan fifty percent',
'start master bedroom light .',
'theater on please',
'turn dimmer off',
'turn off ac please',
'turn off foyer lights',
'turn off living room light',
'turn off staircase',
'turn off venice lamp',
'turn on bathroom heater',
'turn on external speaker',
'turn on my bedroom lights .',
'turn on the furnace room lights',
'turn on the internet in my bedroom please',
'turn on thermostat please',
'turn the fan to high',
'turn thermostat on 70 .' 
```

## <a name="create-a-batch-to-test-intent-prediction-accuracy"></a>Создание пакета для тестирования точности прогнозирования намерения
1. Создайте `homeauto-batch-1.json` в текстовом редакторе, таком как [VSCode](https://code.visualstudio.com/). 

2. Добавьте высказывания с **намерением**, которое должно быть спрогнозировано в тесте. Чтобы упростить выполнение действий в этом руководстве, возьмите высказывания в `HomeAutomation.TurnOn` и `HomeAutomation.TurnOff` и установите переключатели `on` и `off` для текста в высказываниях. Для намерения `None` добавьте несколько высказываний, которые не входят в [предметную](luis-glossary.md#domain) область (тему). 

    Чтобы понять, как результаты пакетного тестирования коррелируются с JSON-файлом пакета, добавьте только шесть намерений.

    ```JSON
    [
        {
          "text": "lobby on please",
          "intent": "HomeAutomation.TurnOn",
          "entities": []
        },
        {
          "text": "change temperature to seventy one degrees",
          "intent": "HomeAutomation.TurnOn",
          "entities": []
        },
        {
          "text": "where is my pizza",
          "intent": "None",
          "entities": []
        },
        {
          "text": "help",
          "intent": "None",
          "entities": []
        },
        {
          "text": "breezeway off please",
          "intent": "HomeAutomation.TurnOff",
          "entities": []
        },
        {
          "text": "coffee bar off please",
          "intent": "HomeAutomation.TurnOff",
          "entities": []
        }
    ]
    ```

## <a name="run-the-batch"></a>Запуск пакетного теста
1. Нажмите кнопку **Test** (Тестировать) в верхней панели навигации. 

    ![Кнопка "Test" (Тестировать) на панели навигации](./media/luis-tutorial-batch-testing/test-1.png)

2. На правой панели щелкните ссылку **Batch testing panel** (Панель пакетного тестирования). 

    ![Выбор панели пакетного тестирования](./media/luis-tutorial-batch-testing/test-2.png)

3. Нажмите кнопку **Import dataset** (Импортировать набор данных).

    ![Кнопка "Import dataset" (Импортировать набор данных)](./media/luis-tutorial-batch-testing/test-3.png)

4. Выберите расположение файловой системы для файла `homeauto-batch-1.json`.

5. Задайте набору данных имя `set 1`.

    ![Выбор файла](./media/luis-tutorial-batch-testing/test-4.png)

6. Нажмите кнопку **Запустить**. Дождитесь завершения теста.

    ![Кнопка запуска](./media/luis-tutorial-batch-testing/test-5.png)

7. Щелкните **See results** (Просмотреть результаты).

    ![Просмотр результатов](./media/luis-tutorial-batch-testing/test-6.png)

8. Просмотрите результаты на диаграмме и в условных обозначениях.

    ![Результаты пакетного тестирования](./media/luis-tutorial-batch-testing/batch-result-1.png)

## <a name="review-batch-results"></a>Просмотр результатов пакетного тестирования
Результаты приведены в двух разделах. В верхнем разделе находятся диаграмма и условные обозначения. В нижнем разделе находятся высказывания, отображаемые при выборе имени области на диаграмме.

Все ошибки обозначаются красным цветом. Диаграмма состоит из четырех частей, две из которых выделены красным цветом. **Это разделы, требующие внимания**. 

Верхний правый раздел указывает, что тест неправильно спрогнозировал существование намерения или сущности. Нижний левый раздел указывает, что тест неправильно спрогнозировал отсутствие намерения или сущности.

### <a name="homeautomationturnoff-test-results"></a>Результаты теста HomeAutomation.TurnOff
В условных обозначениях выберите намерение `HomeAutomation.TurnOff`. В условных обозначениях его можно найти по зеленому значку успешного выполнения слева от имени. Для этого намерения ошибок не обнаружено. 

![Результаты пакетного тестирования](./media/luis-tutorial-batch-testing/batch-result-1.png)

### <a name="homeautomationturnon-and-none-intents-have-errors"></a>Ошибки в намерениях HomeAutomation.TurnOn и None
У двух других намерений есть ошибки, что означает, что тестовые прогнозирования не соответствуют ожиданиям пакетного файла. В условных обозначениях выберите намерение `None`, чтобы просмотреть первую ошибку. 

![Намерение None](./media/luis-tutorial-batch-testing/none-intent-failures.png)

Ошибки отображаются на диаграмме в красных разделах: **False Positive** (Ложный положительный результат) и **False Negative** (Ложный отрицательный результат). Выберите имя раздела **False Negative** (Ложный отрицательный результат), чтобы увидеть высказывания с ошибками под диаграммой. 

![Ложные отрицательные ошибки](./media/luis-tutorial-batch-testing/none-intent-false-negative.png)

Высказывание с ошибкой `help` ожидалось в качестве намерения `None`, но тест спрогнозировал намерение `HomeAutomation.TurnOn`.  

Существует две ошибки — одна в HomeAutomation.TurnOn и одна в None. Их причиной является высказывание `help`, так как не удалось удовлетворить условиям ожидания в None и возникло неожиданное совпадение для намерения HomeAutomation.TurnOn. 

Чтобы определить причину возникновения ошибок в высказываниях `None`, просмотрите высказывания, находящиеся в текущий момент в намерении `None`. 

## <a name="review-none-intents-utterances"></a>Просмотр высказываний в намерении None

1. Закройте панель **Test** (Тестирование), нажав кнопку **Test** (Тестировать) на верхней панели навигации. 

2. Нажмите кнопку **Build** (Создать) на верхней панели навигации. 

3. В списке намерения выберите намерение **None**.

4. Нажмите сочетание клавиш CRTL+E, чтобы открыть представление токенов для высказываний. 
    
    |Высказывания намерения None|Оценка прогнозирования|
    |--|--|
    |"decrease temperature for me please"|0,44|
    |"dim kitchen lights to 25."|0,43|
    |"lower your volume"|0,46|
    |"turn on the internet in my bedroom please"|0,28|

## <a name="fix-none-intents-utterances"></a>Исправление высказываний намерения None
    
Все высказывания в `None` не должны входить в предметную область приложения. Эти высказывания относятся к HomeAutomation, поэтому они находятся в неправильном намерении. 

LUIS также дает высказываниям оценку прогнозирования меньше 50 % (< 0,50). Если взглянуть на высказывания в двух других намерениях, можно увидеть гораздо более высокие оценки прогнозирования. LUIS дает низкие оценки примерам высказываний, это свидетельствует о том, что высказывания ставят LUIS в затруднительное положение относительно текущего намерения и других намерений. 

Чтобы исправить приложение, необходимо переместить выражения, находящиеся в настоящее время в намерении `None`, в правильное намерение и назначить намерению `None` новые соответствующие намерения. 

Три высказывания в намерении `None` предназначены для снижения настроек устройств автоматизации. В них используются такие слова, как `dim`, `lower` или `decrease`. В четвертом высказывании содержится просьба на включение Интернета. Поскольку все четыре высказывания касаются включения или изменения уровня мощности устройства, их следует переместить в намерение `HomeAutomation.TurnOn`. 

Это лишь одно из решений. Можно также создать намерение `ChangeSetting` и переместить в него выражения со словами dim, lower и decrease. 

## <a name="fix-the-app-based-on-batch-results"></a>Исправление приложения на основе результатов пакетного тестирования
Переместите четыре высказывания в намерение `HomeAutomation.TurnOn`. 

1. Установите флажок над списком высказываний, чтобы выбрать все высказывания. 

2. В раскрывающемся списке **Reassign intent** (Переназначить намерение) выберите `HomeAutomation.TurnOn`. 

    ![Перемещение высказываний](./media/luis-tutorial-batch-testing/move-utterances.png)

    После переназначения четырех высказываний список для намерения `None` будет пуст.

3. Добавьте четыре новых намерения для намерения None.

    ```
    "fish"
    "dogs"
    "beer"
    "pizza"
    ```

    Эти высказывания точно не входят в предметную область HomeAutomation. По мере ввода каждого высказывания отслеживайте его оценку. Оценка может быть низкой или даже очень низкой (с красным прямоугольником вокруг значения). После обучения приложения на шаге 8 оценка будет гораздо выше. 

7. Удалите все пометки, щелкнув синюю метку в высказывании и выбрав **Remove label** (Удалить метку).

8. Выберите **Train** (Обучить) на правой верхней панели навигации. Теперь оценка каждого высказывания гораздо выше. Сейчас все оценки для намерения `None` должны быть выше 0,80. 

## <a name="verify-the-fix-worked"></a>Проверка применения исправления
Чтобы проверить правильность прогнозирования высказываний для намерения **None**, запустите пакетный тест еще раз.

1. Нажмите кнопку **Test** (Тестировать) в верхней панели навигации. 

2. На правой панели щелкните ссылку **Batch testing panel** (Панель пакетного тестирования). 

3. Нажмите кнопку с многоточием (...) справа от имени пакета и выберите пункт **Run Dataset** (Запустить набор данных). Дождитесь завершения пакетного теста.

    ![Запуск набора данных](./media/luis-tutorial-batch-testing/run-dataset.png)

4. Щелкните **See results** (Просмотреть результаты). У всех намерений должны быть зеленые значки слева от имен. Задав для фильтра в качестве условия намерение `HomeAutomation.Turnoff`, на верхней правой панели выберите зеленую точку, ближайшую к середине диаграммы. Имя высказывания появится в таблице под диаграммой. `breezeway off please` имеет очень низкую оценку. Необязательное действие: чтобы повысить эту оценку, добавьте дополнительные высказывания в намерение. 

    ![Запуск набора данных](./media/luis-tutorial-batch-testing/turnoff-low-score.png)

<!--
    The Entities section of the legend may have errors. That is the next thing to fix.

## Create a batch to test entity detection
1. Create `homeauto-batch-2.json` in a text editor such as [VSCode](https://code.visualstudio.com/). 

2. Utterances have entities identified with `startPos` and `endPost`. These two elements identify the entity before [tokenization](luis-glossary.md#token), which happens in some [cultures](luis-supported-languages.md#tokenization) in LUIS. If you plan to batch test in a tokenized culture, learn how to [extract](luis-concept-data-extraction.md#tokenized-entity-returned) the non-tokenized entities.

    Copy the following JSON into the file:

    ```JSON
    [
        {
          "text": "lobby on please",
          "intent": "HomeAutomation.TurnOn",
          "entities": [
            {
              "entity": "HomeAutomation.Room",
              "startPos": 0,
              "endPos": 4
            }
          ]
        },
        {
          "text": "change temperature to seventy one degrees",
          "intent": "HomeAutomation.TurnOn",
          "entities": [
            {
              "entity": "HomeAutomation.Operation",
              "startPos": 7,
              "endPos": 17
            }
          ]
        },
        {
          "text": "where is my pizza",
          "intent": "None",
          "entities": []
        },
        {
          "text": "help",
          "intent": "None",
          "entities": []
        },
        {
          "text": "breezeway off please",
          "intent": "HomeAutomation.TurnOff",
          "entities": [
            {
              "entity": "HomeAutomation.Room",
              "startPos": 0,
              "endPos": 9
            }
          ]
        },
        {
          "text": "coffee bar off please",
          "intent": "HomeAutomation.TurnOff",
          "entities": [
            {
              "entity": "HomeAutomation.Device",
              "startPos": 0,
              "endPos": 10
            }
          ]
        }
      ]
    ```

3. Import the batch file, following the [same instructions](#run-the-batch) as the first import, and name the dataset `set 2`. Run the test.

## Possible entity errors
Since the intents in the right-side filter of the test panel still pass the test, this section focuses on correct entity identification. 

Entity testing is diferrent than intents. An utterance will have only one top scoring intent, but it may have several entities. An utterance's entity may be correctly identified, may be incorrectly identified as an entity other than the one in the batch test, may overlap with other entities, or not identified at all. 

## Review entity errors
1. Select `HomeAutomation.Device` in the filter panel. The chart changes to show a single false positive and several true negatives. 

2. Select the False positive section name. The utterance for this chart point is displayed below the chart. The labeled intent and the predicted intent are the same, which is consistent with the test -- the intent prediction is correct. 

    The issue is that the HomeAutomation.Device was detected but the batch expected HomeAutomation.Room for the utterance "coffee bar off please". `Coffee bar` could be a room or a device, depending on the environment and context. As the model designer, you can either enforce the selection as `HomeAutomation.Room` or change the batch file to use `HomeAutomation.Device`. 

    If you want to reinforce that coffee bar is a room, you nee to add an utterances to LUIS that help LUIS decide a coffee bar is a room. 

    The most direct route is to add the utterance to the intent but that to add the utterance for every entity detection error is not the machine-learned solution. Another fix would be to add an utterance with `coffee bar`.

## Add utterance to help extract entity
1. Select the **Test** button on the top navigation to close the batch test panel.

2. On the `HomeAutomation.TurnOn` intent, add the utterance, `turn coffee bar on please`. The uttterance should have all three entities detected after you select enter. 

3. Select **Train** on the top navigation panel. Wait until training completes successfully.

3. Select **Test** on the top navigation panel to open the Batch testing pane again. 

4. If the list of datasets is not visible, select **Back to list**. Select the three dots (...) at the end of `Set 2` and select `Run Dataset`. Wait for the test to complete.

5. Select **See results** to review the test results.

6. 
-->
## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Узнайте больше о примерах высказываний](luis-how-to-add-example-utterances.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions