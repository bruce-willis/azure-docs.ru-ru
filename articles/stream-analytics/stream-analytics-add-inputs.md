---
title: Общие сведения о входных данных Azure Stream Analytics
description: В этой статье описываются концепции входных данных в задании Azure Stream Analytics, сравнение потоковых и справочных входных данных.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/25/2018
ms.openlocfilehash: 4a84e8f7460d3a339be783be6a12353770ced1b8
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/04/2018
ms.locfileid: "43665829"
---
# <a name="understand-inputs-for-azure-stream-analytics"></a>Общие сведения о входных данных Azure Stream Analytics

Задания Azure Stream Analytics используют несколько видов входных данных. Все входные данные определяют подключение к имеющемуся источнику данных. Stream Analytics принимает входящие данные от нескольких типов источников событий, включая Центры событий, Центр Интернета вещей и хранилище BLOB-объектов. Входные данные подаются в имени потокового SQL-запроса, которое записывается для каждого задания. В запросе вы можете объединить несколько входных данных для смешивания данных или сравнения потоковых данных с помощью уточняющего запроса ссылочных данных и передачи результатов в выходные данные. 

Stream Analytics полностью интегрируется с тремя типами ресурсов, используемых в качестве входных данных.
- [Центры событий Azure](https://azure.microsoft.com/services/event-hubs/)
- [Центр Интернета вещей Azure](https://azure.microsoft.com/services/iot-hub/) 
- [хранилище BLOB-объектов Azure](https://azure.microsoft.com/services/storage/blobs/) 

Эти ресурсы входных данных могут существовать в той же подписке Azure, что и задание Stream Analytics, или быть из другой подписки.

Для создания, изменения и проверки входных данных задания Stream Analytics можно использовать [портал Azure](stream-analytics-quick-create-portal.md#configure-input-to-the-job), [Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/New-AzureRmStreamAnalyticsInput), [.Net API](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.inputsoperationsextensions), [REST API](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-input) и [Visual Studio](stream-analytics-tools-for-visual-studio-install.md).

## <a name="stream-and-reference-inputs"></a>Потоковые и справочные входные данные
Данные, отправляемые в источник данных, принимаются заданием Stream Analytics и обрабатываются в режиме реального времени. Входные данные делятся на два типа: входные потоковые данные и входные справочные данные.

### <a name="data-stream-input"></a>Входные потоковые данные
Поток данных — это несвязанная последовательность событий в динамике по времени. Задания Stream Analytics должны включать хотя бы один входной поток данных. В качестве источников входных потоковых данных могут выступать хранилища больших двоичных объектов, Центры событий и центры Интернета вещей. Центры событий используются для сбора потоков событий из нескольких устройств и служб. Это могут быть ленты новостей социальных сетей, сведения о торговле акциями или данные датчиков. Центры Интернета вещей оптимизированы для сбора данных с подключенных устройств в сценариях Интернета вещей.  Хранилище больших двоичных объектов может использоваться в качестве источника входных данных при сборе массовых данных в виде потока, например файлов журналов.  

Дополнительные сведения о входных потоковых данных см. в статье [Подключение данных: узнайте о потоках входных данных из событий в Stream Analytics](stream-analytics-define-inputs.md).

### <a name="reference-data-input"></a>Входные справочные данные
Stream Analytics также поддерживает входные данные, называемые *ссылочными данными*. Справочные данные являются полностью статическими и изменяются крайне редко. Они обычно используются для осуществления корреляции и поисков. Например, можно соединить входные потоковые данные со ссылочными данными так же, как вы бы выполнили соединение SQL для поиска статических значений. Хранилище больших двоичных объектов Azure — единственный поддерживаемый источник входных эталонных данных. Максимальный размер больших двоичных объектов в источнике ссылочных данных — 100 МБ.

Дополнительные сведения о входных справочных данных см. в статье [Использование эталонных данных для уточняющих запросов в Stream Analytics](stream-analytics-use-reference-data.md).

Эта статья представляет собой шаг [схемы обучения Stream Analytics](https://azure.microsoft.com/documentation/learning-paths/stream-analytics/).

## <a name="next-steps"></a>Дополнительная информация
> [!div class="nextstepaction"]
> [Краткое руководство по созданию задания Stream Analytics с помощью портала Azure](stream-analytics-quick-create-portal.md)