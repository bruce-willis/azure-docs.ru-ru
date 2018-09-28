---
title: Запрос маршрутизации сообщений центра Интернета вещей Azure | Документы Майкрософт
description: Руководство разработчика. Синтаксис запросов для маршрутизации сообщений в центре Интернета вещей Azure.
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/13/2018
ms.author: asrastog
ms.openlocfilehash: 8e9321e72727c1a3149ff2e78b8cb1248734cb88
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46978511"
---
# <a name="iot-hub-message-routing-query-syntax"></a>Синтаксис запросов маршрутизации сообщений центра Интернета вещей

Маршрутизация сообщений позволяет пользователям перенаправлять разные типы данных, а именно сообщения телеметрии устройств, события жизненного цикла устройств и события изменения двойников устройств в различные конечные точки. Кроме того, можно применить полнофункциональные запросы к этим данным перед их отправкой, чтобы получить важные сведения. В этой статье описывается язык запросов маршрутизации сообщений центра Интернета вещей, а также приводятся некоторые распространенные шаблоны запросов. 

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Маршрутизация сообщений позволяет запрашивать свойства сообщения, текст сообщения, теги и свойства двойников устройств. Если текст сообщения не в формате JSON, маршрутизация сообщений по-прежнему выполняется, но запросы не могут применяться к тексту сообщения.  Запросы описываются как логические выражения, где логическое true делает запрос успешным, и выполняется маршрутизация всех входящих данных, а логическое значение false вызывает сбой запроса, и данные не маршрутизируются. Если выражение принимает значение null или неопределенное значение, оно рассматривается как false и в случае сбоя в журналах диагностики формируется ошибка. Для сохранения и вычисления маршрута синтаксис запроса должен быть правильным.  

## <a name="message-routing-query-based-on-message-properties"></a>Запрос маршрутизации сообщений на основе свойств сообщения 

Центр Интернета вещей задает [общий формат](../iot-hub/iot-hub-devguide-messages-construct.md) для всех сообщений с устройства в облако для взаимодействия по различным протоколам. Сообщение центра Интернета вещей предполагает следующее представление JSON сообщения. Системные свойства добавляются для всех пользователей и определяют содержимое сообщения. Пользователи могут выборочно добавлять в сообщение свойства приложений. Мы рекомендуем использовать уникальные имена свойств, так как при передаче сообщений с устройства в облако центр Интернета вещей не учитывает регистр. Например, если у вас есть несколько свойств с одним именем, центр Интернета вещей будет отправлять только одно из этих свойств.  

```json
{ 
  "message": { 
    "systemProperties": { 
      "contentType": "application/json", 
      "contentEncoding": "UTF-8", 
      "iothub-message-source": "deviceMessages", 
      "iothub-enqueuedtime": "2017-05-08T18:55:31.8514657Z" 
    }, 
    "appProperties": { 
      "processingPath": "{cold | warm | hot}", 
      "verbose": "{true, false}", 
      "severity": 1-5, 
      "testDevice": "{true | false}" 
    }, 
    "body": "{\"Weather\":{\"Temperature\":50}}" 
  } 
} 
```
### <a name="system-properties"></a>Свойства системы

Системные свойства помогают определить содержимое и источник сообщений. 

| Свойство | type | ОПИСАНИЕ |
| -------- | ---- | ----------- |
| сontentType | строка | Пользователь указывает тип содержимого сообщений. Чтобы разрешить запрос к тексту сообщения, для этого свойства должно быть задано значение application/JSON. |
| contentEncoding | строка | Пользователь указывает тип кодирования сообщений. Допустимые значения: UTF-8, UTF-16, UTF-32, если contentType имеет значение application/JSON. |
| connectionDeviceId | строка | Это значение задается центром Интернета вещей и определяет источник сообщения. Это могут быть сообщения телеметрии устройств, уведомления об изменении двойников устройства или события жизненного цикла устройства. Это значение нельзя запросить. |
| iothub-enqueuedtime | строка | Это значение задается центром Интернета вещей и представляет фактическое время постановки сообщения в очередь в формате UTC. Чтобы запросить, используйте `'enqueuedTime'`. |

Как описано в разделе [Сообщения центра Интернета вещей](iot-hub-devguide-messages-construct.md), существуют дополнительные системные свойства в сообщении. В дополнение к **contentType**, **contentEncoding** и **enqueuedTime** также можно запросить **connectionDeviceId** и  **connectionModuleId**.

### <a name="application-properties"></a>Свойства приложения

Свойства приложения — это определяемые пользователем строки, которые можно добавить в сообщение. Эти поля необязательные.  

### <a name="query-expressions"></a>Выражения запросов

Запрос системных свойств сообщений должен начинаться с символа `'$'`. Обращение к запросам свойств приложения осуществляется по имени: они не должны включать префикс `'$'`. Если имя свойства приложения начинается с `'$'`, центр Интернета вещей будет искать его в системных свойствах, а затем (если найти его не удастся) — в свойствах приложения. Например:  

Запрос системного свойства contentEncoding 

```sql
$contentEncoding = 'UTF-8'
```

Запрос свойства приложения processingPath
```sql
processingPath = 'hot'
```

Чтобы объединить эти запросы, можно использовать логические выражения и функции. 
```sql
$contentEncoding = 'UTF-8' AND processingPath = 'hot'
```

Полный список поддерживаемых операторов и функций см. в разделе [Выражения и условия](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-query-language#expressions-and-conditions
).

## <a name="message-routing-query-based-on-message-body"></a>Запрос маршрутизации сообщений на основе текста сообщения 

Чтобы включить обработку запросов к тексту сообщения, сообщение должно быть в формате JSON в кодировке UTF-8, UTF-16 или UTF-32. Свойству `contentType` должно быть присвоено значение `application/JSON`, а `contentEncoding` — одна из поддерживаемых кодировок UTF в системном свойстве. Если эти свойства не указаны, центр Интернета вещей не будет вычислять выражение запроса к тексту сообщения. 

В следующем примере показано, как создать сообщение с правильно форматированным и закодированным текстом JSON. 

```javascript
var messageBody = JSON.stringify(Object.assign({}, {
    "Weather": {
        "Temperature": 50,
        "Time": "2017-03-09T00:00:00.000Z",
        "PrevTemperatures": [
            20,
            30,
            40
        ],
        "IsEnabled": true,
        "Location": {
            "Street": "One Microsoft Way",
            "City": "Redmond",
            "State": "WA"
        },
        "HistoricalData": [
            {
                "Month": "Feb",
                "Temperature": 40
            },
            {
                "Month": "Jan",
                "Temperature": 30
            }
        ]
    }
}));

// Encode message body using UTF-8  
var messageBytes = Buffer.from(messageBody, "utf8");

var message = new Message(messageBytes);

// Set message body type and content encoding 
message.contentEncoding = "utf-8";
message.contentType = "application/json";

// Add other custom application properties   
message.properties.add("Status", "Active");

deviceClient.sendEvent(message, (err, res) => {
    if (err) console.log('error: ' + err.toString());
    if (res) console.log('status: ' + res.constructor.name);
});
```

### <a name="query-expressions"></a>Выражения запросов

Запрос к тексту сообщения должен начинаться с `$body`. В выражении запроса можно использовать ссылку на текст, ссылку на массив текста или несколько ссылок на текст. Выражение запроса также может объединять ссылку на текст с системными свойствами сообщения и ссылкой на свойства приложения сообщения. Например, все выражения, приведенные ниже, допустимы: 

```sql
$body.Weather.HistoricalData[0].Month = 'Feb' 
```
```sql
$body.Weather.Temperature = 50 AND $body.Weather.IsEnabled 
```
```sql
length($body.Weather.Location.State) = 2 
```
```sql
$body.Weather.Temperature = 50 AND processingPath = 'hot'
```

## <a name="message-routing-query-based-on-device-twin"></a>Запрос маршрутизации сообщений на основе двойника устройства 

Маршрутизация сообщений позволяет запрашивать теги и свойства [двойников устройств](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-device-twins), которые являются объектами JSON. Обратите внимание, что запросы к двойнику модуля не поддерживаются. Ниже приведен пример свойств и тегов двойников устройств.

```JSON
{
    "tags": { 
        "deploymentLocation": { 
            "building": "43", 
            "floor": "1" 
        } 
    }, 
    "properties": { 
        "desired": { 
            "telemetryConfig": { 
                "sendFrequency": "5m" 
            }, 
            "$metadata" : {...}, 
            "$version": 1 
        }, 
        "reported": { 
            "telemetryConfig": { 
                "sendFrequency": "5m", 
                "status": "success" 
            },
            "batteryLevel": 55, 
            "$metadata" : {...}, 
            "$version": 4 
        } 
    } 
} 
```

### <a name="query-expressions"></a>Выражения запросов

Запрос к тексту сообщения должен начинаться с `$twin`. Выражение запроса также может объединять ссылку на свойство или тег двойника со ссылкой на текст, системные свойства сообщения и ссылкой на свойства приложения сообщения. Мы рекомендуем использовать уникальные имена в тегах и свойствах, так как запрос не учитывает регистр. Кроме того, в качестве имен свойств не рекомендуется использовать `twin`, `$twin`, `body` или `$body`. Например, все выражения, приведенные ниже, допустимы: 

```sql
$twin.properties.desired.telemetryConfig.sendFrequency = '5m'
```
```sql
$body.Weather.Temperature = 50 AND $twin.properties.desired.telemetryConfig.sendFrequency = '5m'
```
```sql
$twin.tags.deploymentLocation.floor = 1 
```

## <a name="next-steps"></a>Дополнительная информация

* Дополнительные сведения о [маршрутизации сообщений](iot-hub-devguide-messages-d2c.md)
* [Руководство по маршрутизации сообщений](tutorial-routing.md)