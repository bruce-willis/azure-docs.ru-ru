---
title: Пакетное тестирование приложения LUIS в Azure | Документы Майкрософт
description: Используйте пакетное тестирование LUIS для поиска высказываний с неправильными намерениями и сущностями.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/14/2018
ms.author: v-geberr
ms.openlocfilehash: 822fb1e2d5b13941527d242e8501b423bd6b81cb
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36265519"
---
# <a name="batch-testing"></a>Пакетное тестирование
 Пакетное тестирование представляет собой комплексный тест текущей обученной модели для оценки ее производительности в LUIS. 

<a name="batch-testing"></a>
## <a name="import-a-dataset-file-for-batch-testing"></a>Импорт файла набора данных для пакетного тестирования

1. В верхней строке нажмите кнопку **Test** (Тестировать), а затем щелкните ссылку **Batch testing panel** (Панель пакетного тестирования).

    ![Ссылка на пакетное тестирование](./media/luis-how-to-batch-test/batch-testing-link.png)

2. Нажмите кнопку **Import dataset** (Импортировать набор данных). Откроется диалоговое окно **Import new dataset** (Импорт нового набора данных). Нажмите кнопку **Choose File** (Выбрать файл) и найдите [JSON](luis-concept-batch-test.md#batch-file-format)-файл, содержащий *не более 1000* высказываний для тестирования.

    ![Импорт файла набора данных](./media/luis-how-to-batch-test/batchtest-importset.png)

    Ошибки импорта выводятся в красной строке уведомлений в верхней части браузера. Если при импорте возникают ошибки, набор данных не создается. Дополнительные сведения см. в разделе об [общих ошибках](luis-concept-batch-test.md#common-errors-importing-a-batch).

3. В поле **Dataset Name** (Имя набора данных) введите имя для набора данных. Файл набора данных содержит **массив высказываний**, включая *помеченное намерение* и *сущности*. Просмотрите синтаксис [примера пакетного файла](luis-concept-batch-test.md#batch-file-format). 

4. Нажмите кнопку **Готово**. Будет добавлен файл набора данных.

## <a name="run-rename-export-or-delete-dataset"></a>Запуск, переименование, экспорт и удаление набора данных
Чтобы запустить, переименовать, экспортировать или удалить набор данных, используйте кнопку с многоточием (**...** ) в конце строки набора данных.

![Действия с набором данных](./media/luis-how-to-batch-test/batch-testing-options.png)

## <a name="run-a-batch-test-on-your-trained-app"></a>Запуск пакетного теста в обученном приложении

Чтобы запустить тест, выберите имя набора данных. После завершения теста в этой строке отображается результат теста набора данных.

![Результат пакетного теста](./media/luis-how-to-batch-test/run-test.png)

Скачиваемый набор данных — это тот же файл, который был отправлен для пакетного тестирования.

|Состояние|Значение|
|--|--|
|![Зеленый круглый значок успешного выполнения теста](./media/luis-how-to-batch-test/batch-test-result-green.png)|Все высказывания успешно протестированы.|
|![Красный значок x неудачно выполненного теста](./media/luis-how-to-batch-test/batch-test-result-red.png)|По меньшей мере одно намерение высказывания не соответствует прогнозированию.|
|![Значок готовности к тестированию](./media/luis-how-to-batch-test/batch-test-result-blue.png)|Тест готов к запуску.|

<a name="access-batch-test-result-details-in-a-visualized-view"></a>
## <a name="view-batch-test-results"></a>Просмотр результатов пакетного тестирования 
Чтобы просмотреть результаты пакетного тестирования, щелкните **See results** (Просмотреть результаты).

![Результаты пакетного тестирования](./media/luis-how-to-batch-test/run-test-results.png)

<!--
 Select the **See results** link that appears after you run the test. A scatter graph known as an error matrix displays. The data points represent the utterances in the dataset. 

Green points indicate correct prediction, and red ones indicate incorrect prediction.

The filtering panel on the right side of the screen displays a list of all intents and entities in the app, with a green point for intents/entities that were predicted correctly in all dataset utterances, and a red point for those items with errors. Also, for each intent/entity, you can see the number of correct predictions out of the total utterances.

-->


<a name="filter-chart-results-by-intent-or-entity"></a> ## Фильтрация результатов на диаграмме

Чтобы отфильтровать диаграмму по определенному намерению или сущности, выберите намерение или сущность на панели фильтрации справа. Точки данных и их распределение на диаграмме обновляются в зависимости от выбора. 
 
![Визуализированный результат пакетного тестирования](./media/luis-how-to-batch-test/filter-by-entity.png) 

<!--
## Investigate false sections
Data points on the **[False Positive][false-positive]** and **[False Negative][false-negative]** sections indicate errors, which should be investigated. If all data points are on the **[True Positive][true-positive]** and **[True Negative][true-negative]** sections, then your application's performance is perfect on this dataset.


The graph indicates [F-measure][f-measure], [recall][recall], and [precision][precision].  
-->
## <a name="view-single-point-utterance-data"></a>Просмотр данных высказывания для одной точки
На диаграмме наведите указатель мыши на точку данных, чтобы увидеть оценку достоверности ее прогнозирования. Выберите точку данных для извлечения ее соответствующего высказывания из списка высказываний в нижней части страницы. 

![Выбранное высказывание](./media/luis-how-to-batch-test/selected-utterance.png)


<a name="relabel-utterances-and-retrain"></a>
<a name="false-test-results"></a>
## <a name="view-section-data"></a>Просмотр данных раздела
В диаграмме из четырех частей выберите имя раздела, например **False Positive** (Ложноположительный результат), в правой верхней части диаграммы. Под диаграммой в списке отображаются все высказывания в указанном разделе. 

![Высказывания, выбранные по разделу](./media/luis-how-to-batch-test/selected-utterances-by-section.png)

На предыдущем рисунке высказывание `switch on` помечено с намерением TurnAllOn, но получило прогнозирование намерения None. Это указывает на то, что для выполнения ожидаемого прогнозирования намерению TurnAllOn необходимы дополнительные высказывания. 

Два красных раздела на диаграмме указывают на высказывания, которые не соответствуют ожидаемому прогнозированию. Это значит, что LUIS требуется дополнительное обучение. 

Два зеленых раздела на диаграмме означают соответствие ожидаемому прогнозированию.

## <a name="next-steps"></a>Дополнительная информация

Если тестирование показывает, что приложение LUIS не распознает правильные намерения и сущности, вы можете повысить производительность приложения LUIS, пометив дополнительные высказывания или добавив компоненты. 

* я[Label suggested utterances with LUIS](Label-Suggested-Utterances.md) (Пометка предлагаемых высказываний в LUIS) 
* [Use features to improve your LUIS app's performance](luis-how-to-add-features.md) (Использование функций для повышения производительности приложения LUIS) 
* [Understand batch testing with this tutorial](luis-tutorial-batch-testing.md) (Общие сведения о пакетном тестировании в этом руководстве)
* [Learn batch testing concepts](luis-concept-batch-test.md) (Общие сведения о принципах пакетного тестирования)

[true-positive]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-glossary#true-positive
[true-negative]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-glossary#true-negative
[false-positive]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-glossary#false-positive
[false-negative]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-glossary#false-negative
[f-measure]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-glossary#f-measure
[recall]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-glossary#recall
[precision]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-glossary#precision

