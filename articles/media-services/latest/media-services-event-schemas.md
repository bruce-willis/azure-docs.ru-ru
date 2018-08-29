---
title: Схемы службы "Сетка событий Azure" для событий Служб мультимедиа
description: В этой статье приведены свойства событий Служб мультимедиа, используемых со службой "Сетка событий Azure".
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: reference
ms.date: 08/17/2018
ms.author: juliako
ms.openlocfilehash: a6a6c459e170627d26aa1445f4f4dd193965fe70
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/17/2018
ms.locfileid: "42143065"
---
# <a name="azure-event-grid-schemas-for-media-services-events"></a>Схемы службы "Сетка событий Azure" для событий Служб мультимедиа

В этой статье приведены схемы и свойства событий Служб мультимедиа.

Список примеров сценариев и руководства см. в статье [Источники событий в службе "Сетка событий Azure"](../../event-grid/event-sources.md#azure-subscriptions).

## <a name="available-event-types"></a>Доступные типы событий

Службы мультимедиа выдают следующие типы событий:

| Тип события | ОПИСАНИЕ |
| ---------- | ----------- |
| Microsoft.Media.JobStateChange| Состояние задания изменяется. |
| Microsoft.Media.LiveEventConnectionRejected | Попытка подключения кодировщика отклоняется. |
| Microsoft.Media.LiveEventEncoderConnected | Кодировщик устанавливает подключение с событием прямой трансляции. |
| Microsoft.Media.LiveEventEncoderDisconnected | Отключение кодировщика. |
| Microsoft.Media.LiveEventIncomingDataChunkDropped | Сервер мультимедиа удаляет блок данных, потому что он устарел или имеет перекрывающуюся метку времени (метка времени нового блока данных меньше времени окончания предыдущего блока данных). |
| Microsoft.Media.LiveEventIncomingStreamReceived | Сервер мультимедиа получает первый блок данных для каждой дорожки в потоковой передаче или подключении. |
| Microsoft.Media.LiveEventIncomingStreamsOutOfSync | Сервер мультимедиа обнаруживает, что аудио- и видеопотоки не синхронизированы. Используется как предупреждение, так как не влияет на взаимодействие с пользователем. |
| Microsoft.Media.LiveEventIncomingVideoStreamsOutOfSync | Сервер мультимедиа обнаруживает, что любой из двух видеопотоков, поступающих из внешнего кодировщика, не синхронизирован. Используется как предупреждение, так как не влияет на взаимодействие с пользователем. |
| Microsoft.Media.LiveEventIngestHeartbeat | Публикуется каждые 20 секунд для каждой дорожки, когда выполняется событие прямой трансляции. Предоставляет сводку работоспособности приема. |
| Microsoft.Media.LiveEventTrackDiscontinuityDetected | Сервер мультимедиа обнаруживает разрыв во входящей дорожке. |

Существуют две категории для событий **прямой трансляции**: события уровня потока и события уровня дорожки. 

События уровня потока вызываются в потоке или подключении. Каждое событие имеет параметр `StreamId`, который идентифицирует подключение или поток. Каждый поток или подключение имеет одну или несколько дорожек разных типов. Например, одно подключение от кодировщика может иметь одну аудиодорожку и четыре видеодорожки. К типам событий потока относятся следующие:

* LiveEventConnectionRejected;
* LiveEventEncoderConnected;
* LiveEventEncoderDisconnected.

События уровня дорожки вызываются в каждой дорожке. К типам событий дорожки относятся следующие:

* LiveEventIncomingDataChunkDropped;
* LiveEventIncomingStreamReceived;
* LiveEventIncomingStreamsOutOfSync;
* LiveEventIncomingVideoStreamsOutOfSync;
* LiveEventIngestHeartbeat;
* LiveEventTrackDiscontinuityDetected.

## <a name="jobstatechange"></a>JobStateChange

В примере ниже показана схема события **JobStateChange**. 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
    "eventType": "Microsoft.Media.JobStateChange",
    "eventTime": "2018-04-20T21:26:13.8978772",
    "id": "b9d38923-9210-4c2b-958f-0054467d4dd7",
    "data": {
      "previousState": "Processing",
      "state": "Finished"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Объект данных имеет следующие свойства:

| Свойство | type | ОПИСАНИЕ |
| -------- | ---- | ----------- |
| previousState | строка | Состояние задания перед событием. |
| state | строка | Новое состояние задания в этом событии. Пример: "Queued: The Job is awaiting resources" (В очереди: задание ожидает ресурсы) или "Scheduled: The job is ready to start" (Запланировано: задание готово к запуску).|

Возможные значения состояния задания: *В очереди*, *Запланировано*, *Обработка*, *Завершено*, *Ошибка*, *Отменено*, *Отмена*.

## <a name="liveeventconnectionrejected"></a>LiveEventConnectionRejected

Следующий пример демонстрирует схему события **LiveEventConnectionRejected**: 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaServices/<account-name>",
    "subject": "/LiveEvents/MyLiveEvent1",
    "eventType": "Microsoft.Media.LiveEventConnectionRejected",
    "eventTime": "2018-01-16T01:57:26.005121Z",
    "id": "b303db59-d5c1-47eb-927a-3650875fded1",
    "data": { 
      "StreamId":"Mystream1",
      "IngestUrl": "http://abc.ingest.isml",
      "EncoderIp": "118.238.251.xxx",
      "EncoderPort": 52859,
      "ResultCode": "MPE_INGEST_CODEC_NOT_SUPPORTED"
    },
    "dataVersion": "1.0"
  }
]
```

Объект данных имеет следующие свойства:

| Свойство | type | ОПИСАНИЕ |
| -------- | ---- | ----------- |
| StreamId | строка | Идентификатор потока или подключения. Кодировщик или клиент несет ответственность за добавление этого идентификатора в URL-адрес приема. |  
| IngestUrl | строка | URL-адрес приема, предоставленный событием прямой трансляции. |  
| EncoderIp | строка | IP-адрес кодировщика. |
| EncoderPort | строка | Порт кодировщика из источника этого потока. |
| ResultCode | строка | Причина, по которой подключение было отклонено. Коды результатов перечислены в следующей таблице. |

Коды результатов следующие:

| Код результата | ОПИСАНИЕ |
| ----------- | ----------- |
| MPE_RTMP_APPID_AUTH_FAILURE | Неправильный URL-адрес приема. |
| MPE_INGEST_ENCODER_CONNECTION_DENIED | IP-адрес кодировщика отсутствует в настроенном списке разрешенных IP-адресов. |
| MPE_INGEST_RTMP_SETDATAFRAME_NOT_RECEIVED | Кодировщик не отправил метаданные о потоке. |
| MPE_INGEST_CODEC_NOT_SUPPORTED | Указанный кодек не поддерживается. |
| MPE_INGEST_DESCRIPTION_INFO_NOT_RECEIVED | Получен фрагмент перед получением и заголовком для этого потока. |
| MPE_INGEST_MEDIA_QUALITIES_EXCEEDED | Количество указанных качеств превышает допустимый максимальный предел. |
| MPE_INGEST_BITRATE_AGGREGATED_EXCEEDED | Совокупная скорость превышает максимально допустимый предел. |
| MPE_RTMP_FLV_TAG_TIMESTAMP_INVALID | Метка времени из кодировщика RTMP для FLVTag видео или аудио недействительна. |

## <a name="liveeventencoderconnected"></a>LiveEventEncoderConnected

Следующий пример демонстрирует схему события **LiveEventEncoderConnected**: 

```json
[
  { 
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventEncoderConnected",
    "eventTime": "2018-08-07T23:08:09.1710643",
    "id": "<id>",
    "data": {
      "ingestUrl": "http://mle1-amsts03mediaacctgndos-ts031.channel.media.azure-test.net:80/ingest.isml",
      "streamId": "15864-stream0",
      "encoderIp": "131.107.147.xxx",
      "encoderPort": "27485"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Объект данных имеет следующие свойства:

| Свойство | type | ОПИСАНИЕ |
| -------- | ---- | ----------- |
| StreamId | строка | Идентификатор потока или подключения. Кодировщик или клиент несет ответственность за указание этого идентификатора в URL-адресе приема. |
| IngestUrl | строка | URL-адрес приема, предоставленный событием прямой трансляции. |
| EncoderIp | строка | IP-адрес кодировщика. |
| EncoderPort | строка | Порт кодировщика из источника этого потока. |

## <a name="liveeventencoderdisconnected"></a>LiveEventEncoderDisconnected.

Следующий пример демонстрирует схему события **LiveEventEncoderDisconnected**: 

```json
[
  { 
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventEncoderDisconnected",
    "eventTime": "2018-08-07T23:08:09.1710872",
    "id": "<id>",
    "data": {
      "ingestUrl": "http://mle1-amsts03mediaacctgndos-ts031.channel.media.azure-test.net:80/ingest.isml",
      "streamId": "15864-stream0",
      "encoderIp": "131.107.147.xxx",
      "encoderPort": "27485",
      "resultCode": "S_OK"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Объект данных имеет следующие свойства:

| Свойство | type | ОПИСАНИЕ |
| -------- | ---- | ----------- |
| StreamId | строка | Идентификатор потока или подключения. Кодировщик или клиент несет ответственность за добавление этого идентификатора в URL-адрес приема. |  
| IngestUrl | строка | URL-адрес приема, предоставленный событием прямой трансляции. |  
| EncoderIp | строка | IP-адрес кодировщика. |
| EncoderPort | строка | Порт кодировщика из источника этого потока. |
| ResultCode | строка | Причина отключения кодировщика. Это может быть нормальное отключение или ошибка. Коды результатов перечислены в следующей таблице. |

Коды результатов ошибок следующие:

| Код результата | ОПИСАНИЕ |
| ----------- | ----------- |
| MPE_RTMP_SESSION_IDLE_TIMEOUT | Превышено время ожидания сеанса RTMP после простоя в течение допустимого ограничения времени. |
| MPE_RTMP_FLV_TAG_TIMESTAMP_INVALID | Метка времени из кодировщика RTMP для FLVTag видео или аудио недействительна. |
| MPE_CAPACITY_LIMIT_REACHED | Кодировщик отправляет данные слишком быстро. |
| Коды неизвестных ошибок | Эти коды ошибок могут варьироваться от ошибки памяти до дублирования записей в карте хэшей. |

Коды результатов нормального отключения:

| Код результата | ОПИСАНИЕ |
| ----------- | ----------- |
| S_OK | Кодировщик отключен успешно. |
| MPE_CLIENT_TERMINATED_SESSION | Кодировщик отключен (RTMP). |
| MPE_CLIENT_DISCONNECTED | Кодировщик отключен (FMP4). |
| MPI_REST_API_CHANNEL_RESET | Получена команда сброса канала. |
| MPI_REST_API_CHANNEL_STOP | Получена команда остановки канала. |
| MPI_REST_API_CHANNEL_STOP | Канал находится в режиме обслуживания. |
| MPI_STREAM_HIT_EOF | Поток конца файла отправлен кодировщиком. |

## <a name="liveeventincomingdatachunkdropped"></a>LiveEventIncomingDataChunkDropped

Следующий пример демонстрирует схему события **LiveEventIncomingDataChunkDropped**: 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaServices/<account-name>",
    "subject": "/LiveEvents/MyLiveEvent1",
    "eventType": "Microsoft.Media.LiveEventIncomingDataChunkDropped",
    "eventTime": "2018-01-16T01:57:26.005121Z",
    "id": "03da9c10-fde7-48e1-80d8-49936f2c3e7d",
    "data": { 
      "TrackType": "Video",
      "TrackName": "Video",
      "Bitrate": 300000,
      "Timestamp": 36656620000,
      "Timescale": 10000000,
      "ResultCode": "FragmentDrop_OverlapTimestamp"
    },
    "dataVersion": "1.0"
  }
]
```

Объект данных имеет следующие свойства:

| Свойство | type | ОПИСАНИЕ |
| -------- | ---- | ----------- |
| TrackType | строка | Тип дорожки (аудио/видео). |
| TrackName | строка | Имя дорожки. |
| Bitrate | целое число | Скорость дорожки. |
| Timestamp | строка | Удалена метка времени блока данных. |
| Шкала времени | строка | Шкала времени метки времени. |
| ResultCode | строка | Причина удаления блока данных. **FragmentDrop_OverlapTimestamp** или **FragmentDrop_NonIncreasingTimestamp**. |

## <a name="liveeventincomingstreamreceived"></a>LiveEventIncomingStreamReceived

Следующий пример демонстрирует схему события **LiveEventIncomingStreamReceived**: 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventIncomingStreamReceived",
    "eventTime": "2018-08-07T23:08:10.5069288Z",
    "id": "7f939a08-320c-47e7-8250-43dcfc04ab4d",
    "data": {
      "ingestUrl": "http://mle1-amsts03mediaacctgndos-ts031.channel.media.azure-test.net:80/ingest.isml/Streams(15864-stream0)15864-stream0",
      "trackType": "video",
      "trackName": "video",
      "bitrate": 2962000,
      "encoderIp": "131.107.147.xxx",
      "encoderPort": "27485",
      "timestamp": "15336831655032322",
      "duration": "20000000",
      "timescale": "10000000"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Объект данных имеет следующие свойства:

| Свойство | type | ОПИСАНИЕ |
| -------- | ---- | ----------- |
| TrackType | строка | Тип дорожки (аудио/видео). |
| TrackName | строка | Название дорожки (предоставляется кодировщиком или, в случае RTMP, создается сервером в формате *TrackType_Bitrate*). |
| Bitrate | целое число | Скорость дорожки. |
| IngestUrl | строка | URL-адрес приема, предоставленный событием прямой трансляции. |
| EncoderIp | строка  | IP-адрес кодировщика. |
| EncoderPort | строка | Порт кодировщика из источника этого потока. |
| Timestamp | строка | Получена первая метка времени. |
| Шкала времени | строка | Шкала времени, в которой представлена метка времени. |

## <a name="liveeventincomingstreamsoutofsync"></a>LiveEventIncomingStreamsOutOfSync

Следующий пример демонстрирует схему события **LiveEventIncomingStreamsOutOfSync**: 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventIncomingStreamsOutOfSync",
    "eventTime": "2018-08-10T02:26:20.6269183Z",
    "id": "b9d38923-9210-4c2b-958f-0054467d4dd7",
    "data": {
      "minLastTimestamp": "319996",
      "typeOfStreamWithMinLastTimestamp": "Audio",
      "maxLastTimestamp": "366000",
      "typeOfStreamWithMaxLastTimestamp": "Video"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Объект данных имеет следующие свойства:

| Свойство | type | ОПИСАНИЕ |
| -------- | ---- | ----------- |
| MinLastTimestamp | строка | Минимальная из последних меток времени среди всех дорожек (аудио или видео). |
| TypeOfTrackWithMinLastTimestamp | строка | Тип дорожки (аудио или видео) с минимальной последней меткой времени. |
| MaxLastTimestamp | строка | Максимальная из всех меток времени среди всех дорожек (аудио или видео). |
| TypeOfTrackWithMaxLastTimestamp | строка | Тип дорожки (аудио или видео) с максимальной последней меткой времени. |

## <a name="liveeventincomingvideostreamsoutofsync"></a>LiveEventIncomingVideoStreamsOutOfSync

Следующий пример демонстрирует схему события **LiveEventIncomingVideoStreamsOutOfSync**: 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaServices/<account-name>",
    "subject": "/LiveEvents/LiveEvent1",
    "eventType": "Microsoft.Media.LiveEventIncomingVideoStreamsOutOfSync",
    "eventTime": "2018-01-16T01:57:26.005121Z",
    "id": "6dd4d862-d442-40a0-b9f3-fc14bcf6d750",
    "data": {
      "FirstTimestamp": "2162058216",
      "FirstDuration": "2000",
      "SecondTimestamp": "2162057216",
      "SecondDuration": "2000"
    },
    "dataVersion": "1.0"
  }
]
```

Объект данных имеет следующие свойства:

| Свойство | type | ОПИСАНИЕ |
| -------- | ---- | ----------- |
| FirstTimestamp | строка | Метка времени, полученная для одной из дорожек или уровней качества типа видео. |
| FirstDuration | строка | Длительность блока данных с первой меткой времени. |
| SecondTimestamp | строка  | Метка времени, полученная для другого уровня дорожки или уровня качества типа видео. |
| SecondDuration | строка | Длительность блока данных со второй меткой времени. |

## <a name="liveeventingestheartbeat"></a>LiveEventIngestHeartbeat

Следующий пример демонстрирует схему события **LiveEventIngestHeartbeat**: 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventIngestHeartbeat",
    "eventTime": "2018-08-07T23:17:57.4610506",
    "id": "7f450938-491f-41e1-b06f-c6cd3965d786",
    "data": {
      "trackType": "audio",
      "trackName": "audio",
      "bitrate": 160000,
      "incomingBitrate": 155903,
      "lastTimestamp": "15336837535253637",
      "timescale": "10000000",
      "overlapCount": 0,
      "discontinuityCount": 0,
      "nonincreasingCount": 0,
      "unexpectedBitrate": false,
      "state": "Running",
      "healthy": true
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Объект данных имеет следующие свойства:

| Свойство | type | ОПИСАНИЕ |
| -------- | ---- | ----------- |
| TrackType | строка | Тип дорожки (аудио/видео). |
| TrackName | строка | Название дорожки (предоставляется кодировщиком или, в случае RTMP, создается сервером в формате *TrackType_Bitrate*). |
| Bitrate | целое число | Скорость дорожки. |
| IncomingBitrate | целое число | Расчетная скорость на основе блоков данных, поступающих из кодировщика. |
| LastTimestamp | строка | Последняя метка времени, полученная для дорожки за последние 20 секунд. |
| Шкала времени | строка | Шкала времени, в которой выражены метки времени. |
| OverlapCount | целое число | Количество блоков данных с перекрывающимися метками времени за последние 20 секунд. |
| DiscontinuityCount | целое число | Количество прерываний, наблюдаемое за последние 20 секунд. |
| NonincreasingCount | целое число | Количество блоков данных с метками времени в прошлом, полученных за последние 20 секунд. |
| UnexpectedBitrate | bool | Если ожидаемые и фактические скорости отличаются более чем на допустимый предел за последние 20 секунд. Это верно, только если IncomingBitrate> = 2 * скорость, IncomingBitrate <= скорость/2 ИЛИ IncomingBitrate = 0. |
| Состояние | строка | Состояние события прямой трансляции. |
| Healthy | bool | Указывает состояние работоспособности приема на основе счетчиков и ​​флагов. Работоспособно, если OverlapCount = 0 && DiscontinuityCount = 0 && NonIncreasingCount = 0 && UnexpectedBitrate = false. |

## <a name="liveeventtrackdiscontinuitydetected"></a>LiveEventTrackDiscontinuityDetected

Следующий пример демонстрирует схему события **LiveEventTrackDiscontinuityDetected**: 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventTrackDiscontinuityDetected",
    "eventTime": "2018-08-07T23:18:06.1270405Z",
    "id": "5f4c510d-5be7-4bef-baf0-64b828be9c9b",
    "data": {
      "trackName": "video",
      "previousTimestamp": "15336837615032322",
      "trackType": "video",
      "bitrate": 2962000,
      "newTimestamp": "15336837619774273",
      "discontinuityGap": "575284",
      "timescale": "10000000"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Объект данных имеет следующие свойства:

| Свойство | type | ОПИСАНИЕ |
| -------- | ---- | ----------- |
| TrackType | строка | Тип дорожки (аудио/видео). |
| TrackName | строка | Название дорожки (предоставляется кодировщиком или, в случае RTMP, создается сервером в формате *TrackType_Bitrate*). |
| Bitrate | целое число | Скорость дорожки. |
| PreviousTimestamp | строка | Метка времени предыдущего фрагмента. |
| NewTimestamp | строка | Метка времени текущего фрагмента. |
| DiscontinuityGap | строка | Разрыв между двумя метками времени выше. |
| Шкала времени | строка | Шкала времени, в которой представлены метка времени и разрыв. |

## <a name="common-event-properties"></a>Общие свойства событий

Событие содержит следующие высокоуровневые данные:

| Свойство | type | ОПИСАНИЕ |
| -------- | ---- | ----------- |
| Раздел | строка | Раздел "Сетка событий". Это свойство имеет идентификатор ресурса для учетной записи Служб мультимедиа. |
| subject | строка | Путь ресурсов для канала Служб мультимедиа в учетной записи Служб мультимедиа. Объединение темы и объекта предоставляет вам идентификатор ресурса для задания. |
| eventType | строка | Один из зарегистрированных типов событий для этого источника событий. Пример: Microsoft.Media.JobStateChange. |
| eventTime | строка | Время создания события с учетом времени поставщика в формате UTC. |
| id | строка | Уникальный идентификатор события. |
| data | object | Данные события Служб мультимедиа. |
| dataVersion | строка | Версия схемы для объекта данных. Версию схемы определяет издатель. |
| metadataVersion | строка | Версия схемы для метаданных события. Служба "Сетка событий" определяет схему свойств верхнего уровня. Это значение предоставляет служба "Сетка событий". |

## <a name="next-steps"></a>Дополнительная информация

[Зарегистрируйтесь на получение событий изменения состояния задания](job-state-events-cli-how-to.md).