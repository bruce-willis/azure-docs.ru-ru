---
title: Тестирование реальных данных в локальной среде с помощью инструментов Azure Stream Analytics для Visual Studio (предварительная версия)
description: Узнайте, как протестировать задание Azure Stream Analytics в локальной среде с помощью потоковой трансляции данных.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: f0a8978a9c2e0538a2e7bc4eab202604913e700b
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46984166"
---
# <a name="test-live-data-locally-using-azure-stream-analytics-tools-for-visual-studio-preview"></a>Тестирование реальных данных в локальной среде с помощью инструментов Azure Stream Analytics для Visual Studio (предварительная версия)

С помощью инструментов Azure Stream Analytics для Visual Studio можно тестировать задания в локальной среде из интегрированной среды разработки с помощью потоковой трансляции событий из концентратора событий Azure, Центра Интернета вещей и хранилища BLOB-объектов. Локальное тестирование реальных данных не заменяет [тестирование производительности и масштабируемости](stream-analytics-streaming-unit-consumption.md), которое можно выполнять в облаке, но вы можете сэкономить время во время функционального тестирования, не отправляя данные в облако каждый раз, когда требуется проверить в задание Stream Analytics. Эта функция находится на этапе предварительной версии и не должна использоваться для производственных рабочих нагрузок.

## <a name="testing-options"></a>Параметры тестирования

Поддерживаются следующие параметры тестирования:

|**Входные данные**  |**Выходные данные**  |**Тип задания**  |
|---------|---------|---------|
|Локальные статические данные   |  Локальные статические данные   |   Облако или Edge |
|Реальные входные данные   |  Локальные статические данные   |   Облако |
|Реальные входные данные   |  Реальные выходные данные   |   Облако |

## <a name="local-testing-with-live-data"></a>Локальное тестирование с помощью реальных данных

1. После создания [облачного проекта Azure Stream Analytics в Visual Studio](stream-analytics-quick-create-vs.md) откройте файл **script.asaql**. При локальном тестировании по умолчанию используются локальные входные и выходные данные.

   ![Локальное тестирование Azure Stream Analytics в Visual Studio: локальные входные и выходные данные](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-local-input-output.png)

2. Чтобы протестировать реальные данные, из раскрывающегося списка выберите **Use Cloud Input** (Использовать облачные входные данные).

   ![Локальное тестирование Azure Stream Analytics в Visual Studio: реальные облачные входные данные](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-input.png)


3. Задайте **время начала**, чтобы определить, когда задание начнет обработку входных данных. Заданию может потребоваться считать входные данные заранее, чтобы обеспечить точные результаты. Время по умолчанию составляет 30 минут с текущего момента.

   ![Локальное тестирование Azure Stream Analytics в Visual Studio с помощью реальных входных данных: время начала](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-input-start-time.png)

4. Щелкните **Run Locally** (Запустить локально). Появится окно консоли, отображающее ход выполнения и метрики задания. Если вы хотите остановить процесс, это можно сделать вручную. 

   ![Локальное тестирование Azure Stream Analytics в Visual Studio с помощью реальных входных данных: окно обработки](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-input-process-window.png)

   Выходные данные обновляются каждые три секунды. В окне результатов локального выполнения отображаются первые 500 выходных строк, а выходные файлы помещаются в папку **ASALocalRun**, размещенную по пути к вашему проекту. Выходные файлы можно также открыть, нажав кнопку **Открыть папку результатов** в окне результатов локального выполнения.

   ![Локальное тестирование Azure Stream Analytics в Visual Studio с помощью реальных входных данных: открытие папки результатов](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-input-open-results-folder.png)

5. Если вы хотите вывести результаты в облачные приемники выходных данных, выберите **Output to Cloud** (Вывод в облако) из второго раскрывающегося списка. Power BI и Azure Data Lake Storage не поддерживаются в качестве приемников выходных данных.

   ![Локальное тестирование Azure Stream Analytics в Visual Studio с передачей реальных выходных данных в облако](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-output.png)
 
## <a name="limitations"></a>Ограничения

* Power BI и Azure Data Lake Storage не поддерживаются в качестве приемников выходных данных из-за ограничений модели аутентификации.

* Для облачных входных данных поддерживаются [политики времени](stream-analytics-out-of-order-and-late-events.md), а для локальных входных данных — нет.

## <a name="next-steps"></a>Дополнительная информация

* [Создание задания Stream Analytics с использованием инструментов Azure Stream Analytics для Visual Studio](stream-analytics-quick-create-vs.md)
* [Установка инструментов Azure Stream Analytics для Visual Studio](stream-analytics-tools-for-visual-studio-install.md)
* [Локальное тестирование запросов Stream Analytics с помощью Visual Studio](stream-analytics-vs-tools-local-run.md)
* [Просмотр заданий Azure Stream Analytics с помощью Visual Studio](stream-analytics-vs-tools.md)