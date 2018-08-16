---
title: Анализ данных JSON и AVRO в Azure Stream Analytics
description: В этой статье описывается, как работать со сложными типами данных, такими как массивы, данные в формате JSON и CSV.
services: stream-analytics
ms.service: stream-analytics
author: jasonwhowell
ms.author: jasonh
manager: kfile
ms.topic: conceptual
ms.date: 08/03/2018
ms.openlocfilehash: 208dfd1d78437e4ae8270d599f6365ad70a708c7
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/06/2018
ms.locfileid: "39580905"
---
# <a name="parse-json-and-avro-data-in-azure-stream-analytics"></a>Анализ данных JSON и AVRO в Azure Stream Analytics

Azure Stream Analytics поддерживает обработку событий в форматах CSV, JSON и Avro. Данные JSON и Avro могут содержать сложные типы, такие как вложенные объекты (записи) и массивы. 
  
## <a name="array-data-types"></a>Тип данных "массив"  
Тип данных "массив" представляет собой упорядоченную коллекцию значений. Ниже приведены типичные операции со значениями массивов. В этих примерах предполагается, что входные события имеют свойство с именем "arrayField", который принадлежит к типу данных "массив".

В этих примерах используются функции [GetArrayElement](https://msdn.microsoft.com/azure/stream-analytics/reference/getarrayelement-azure-stream-analytics), [GetArrayElements](https://msdn.microsoft.com/azure/stream-analytics/reference/getarrayelements-azure-stream-analytics), [GetArrayLength](https://msdn.microsoft.com/azure/stream-analytics/reference/getarraylength-azure-stream-analytics) и оператор [APPLY](https://msdn.microsoft.com/azure/stream-analytics/reference/apply-azure-stream-analytics).

## <a name="examples"></a>Примеры  
 Выберите элемент массива по указанному индексу (первый элемент массива):  
  
```SQL 
SELECT   
    GetArrayElement(arrayField, 0) AS firstElement  
FROM input  
```  
  
 Выберите длину массива:  
  
```SQL  
SELECT   
    GetArrayLength(arrayField) AS arrayLength  
FROM input  
```  
  
Выберите все элементы массива как отдельные события. Оператор [APPLY](https://msdn.microsoft.com/azure/stream-analytics/reference/apply-azure-stream-analytics) вместе со встроенной функцией [GetArrayElements](https://msdn.microsoft.com/azure/stream-analytics/reference/getarrayelements-azure-stream-analytics) извлекает все элементы массива как отдельные события:  
  
```SQL  
SELECT   
    arrayElement.ArrayIndex,  
    arrayElement.ArrayValue  
FROM input as event  
CROSS APPLY GetArrayElements(event.arrayField) AS arrayElement  
```  
  
## <a name="record-data-types"></a>Тип данных "запись"  
Тип данных "запись" используется для представления массивов JSON и Avro, когда соответствующие форматы используются во входных потоках данных. Эти примеры демонстрируют пример датчика, который считывает входные события в формате JSON. Ниже приведен пример одного события:
  
```json  
{  
    "DeviceId" : "12345",  
    "Location" : 
    {
        "Lat": 47,
        "Long": 122 
    },  
    "SensorReadings" :  
    {  
        "Temperature" : 80,  
        "Humidity" : 70,  
        "CustomSensor01" : 5,  
        "CustomSensor02" : 99  
    }  
}  
```  
  
## <a name="examples"></a>Примеры  
Используйте точечную нотацию (.) для доступа к вложенным полям. Например, этот запрос выбирает координаты широты и долготы в свойстве Location из предыдущего фрагмента данных JSON: 
  
```SQL  
SELECT  
    DeviceID,  
    Location.Lat,  
    Location.Long  
FROM input  
```  

Используйте функцию [GetRecordPropertyValue](https://msdn.microsoft.com/azure/stream-analytics/reference/getrecordpropertyvalue-azure-stream-analytics), если имя свойства неизвестно. Например, представьте пример потока данных, который нужно соединить с эталонными данными, содержащими пороговые значения для каждого датчика устройства:  

```json  
{  
    "DeviceId" : "12345",  
    "SensorName" :  "Temperature",
    "Value" : 75
}  
```  
  
```SQL  
SELECT  
    input.DeviceID,  
    thresholds.SensorName  
FROM input  
JOIN thresholds  
ON  
    input.DeviceId = thresholds.DeviceId  
WHERE  
    GetRecordPropertyValue(input.SensorReadings, thresholds.SensorName) > thresholds.Value  
```  
  
Чтобы преобразовать поля записей в отдельные события, используйте оператор [APPLY](https://msdn.microsoft.com/azure/stream-analytics/reference/apply-azure-stream-analytics) вместе с функцией [GetRecordProperties](https://msdn.microsoft.com/azure/stream-analytics/reference/getrecordproperties-azure-stream-analytics). Например, чтобы преобразовать пример потока в поток событий с отдельными показаниями датчика, можно использовать этот запрос:  
  
```SQL  
SELECT   
    event.DeviceID,  
    sensorReading.PropertyName,  
    sensorReading.PropertyValue  
FROM input as event  
CROSS APPLY GetRecordProperties(event.SensorReadings) AS sensorReading  
```  

Все свойства вложенной записи можно выбрать с помощью подстановочного знака "*". Рассмотрим следующий пример.  

```SQL  
SELECT input.SensorReadings.*  
FROM input  
```  

Результат:  

```json  
{  
    "Temperature" : 80,  
    "Humidity" : 70,  
    "CustomSensor01" : 5,  
    "CustomSensor022" : 99  
}  
```  
  
## <a name="see-also"></a>См. также  
 [Типы данных в Azure Stream Analytics](https://msdn.microsoft.com/azure/stream-analytics/reference/data-types-azure-stream-analytics)  
