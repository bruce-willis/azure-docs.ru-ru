---
title: Экспорт данных в Azure IoT Central | Документация Майкрософт
description: Как экспортировать данные из приложения Azure IoT Central
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 07/3/2018
ms.topic: article
ms.prod: azure-iot-central
manager: peterpr
ms.openlocfilehash: 5b9564dfe40f292d289ee9ed680e816771d0b0ed
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/27/2018
ms.locfileid: "39282880"
---
# <a name="export-your-data-in-azure-iot-central"></a>Экспорт данных в Azure IoT Central

Используйте Экспорт непрерывных данных для периодического экспорта данных в учетную запись хранилища BLOB-объектов. Для экспорта выберите **измерения**, **устройства** и **шаблоны устройств** в файлах формата [Apache AVRO](https://avro.apache.org/docs/current/index.html). Используйте экспортированные данные для анализа холодного пути, например для обучения моделей в Машинном обучении Azure и долгосрочного анализа тенденций в Power BI.

> [!Note]
> С момента включения Экспорта непрерывных данных вы получаете только поступающие данные. В настоящее время нет способа получить данные, когда Экспорт непрерывных данных был отключен. Включите Экспорт непрерывных данных раньше, чтобы сохранить больше исторических данных.

## <a name="prerequisites"></a>Предварительные требования

- Расширенное 30-дневное пробное или платное приложение
- Учетная запись Azure с подпиской Azure
- Эта же учетная запись Azure должна иметь роль администратора в приложении IoT Central
- Эта же учетная запись Azure имеет разрешения на создание учетной записи хранения или на доступ к имеющейся учетной записи хранения в той же подписке Azure

## <a name="types-of-data-to-export"></a>Тип данных для экспорта

### <a name="measurements"></a>Измерения

Измерения, отправляемые устройствами, экспортируются в вашу учетную запись хранения. Данные измерений экспортируются приблизительно раз в минуту и содержат все новые сообщения, полученные IoT Central со всех устройств в пределах заданного периода времени. Экспортированные файлы AVRO находятся в том же формате, что и файлы сообщений, экспортированные с использованием [маршрутизации сообщений с помощью Центра Интернета вещей](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-csharp-csharp-process-d2c) в хранилище BLOB-объектов.

> [!NOTE]
> Устройства, отправляющие измерения, представлены идентификаторами устройств (см. ниже). Чтобы получить имена устройств, необходимо также экспортировать моментальные снимки устройств. Вы можете коррелировать каждую запись сообщения с использованием идентификатора connectionDeviceId, который совпадает с идентификатором устройства.

Ниже приведен пример записи в декодированном файле AVRO.

```json
{
    "EnqueuedTimeUtc": "2018-06-11T00:00:08.2250000Z",
    "Properties": {},
    "SystemProperties": {
        "connectionDeviceId": "2383d8ba-c98c-403a-b4d5-8963859643bb",
        "connectionAuthMethod": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
        "connectionDeviceGenerationId": "636614021491644195",
        "enqueuedTime": "2018-06-11T00:00:08.2250000Z"
    },
    "Body": "{\"humidity\":80.59100954598546,\"magnetometerX\":0.29451796907056726,\"magnetometerY\":0.5550332126050068,\"magnetometerZ\":-0.04116681874733441,\"connectivity\":\"connected\",\"opened\":\"triggered\"}"
}
```

### <a name="devices"></a>Устройства

При включении Экспорта непрерывных данных экспортируется один моментальный снимок, включающий все устройства. А именно:
- идентификаторы устройств;
- имена устройств;
- идентификаторы шаблонов устройств;
- значения свойств;
- значения параметров.

Примерно раз в минуту записывается новый моментальный снимок отчета, содержащий:

- новые устройства, добавленные с момента создания последнего моментального снимка;
- устройства, значения свойств и параметров которых изменились с момента создания последнего моментального снимка.

> [!NOTE]
> Устройства, удаленные с момента создания последнего моментального снимка, не экспортируются. В моментальных снимках отсутствует индикатор для устройств, удаленных в это время.

> [!NOTE]
> Шаблон устройства, к которому принадлежит каждое устройство, представлен идентификатором шаблона устройства. Чтобы получить имя шаблона устройства, также необходимо экспортировать моментальный снимок шаблона устройства.

Каждая запись в декодированном файле AVRO выглядит следующим образом:

```json
{
    "id": "2383d8ba-c98c-403a-b4d5-8963859643bb",
    "name": "Refrigerator 2",
    "simulated": true,
    "deviceTemplate": {
        "id": "c318d580-39fc-4aca-b995-843719821049",
        "version": "1.0.0"
    },
    "properties": {
        "cloud": {
            "location": "New York",
            "maintCon": true,
            "tempThresh": 20
        },
        "device": {
            "lastReboot": "2018-02-09T22:22:47.156Z"
        }
    },
    "settings": {
        "device": {
            "fanSpeed": 0
        }
    }
}
```

### <a name="device-templates"></a>Шаблоны устройств

При включении Экспорта непрерывных данных экспортируется один моментальный снимок, содержащий все шаблоны устройства. А именно: 
- идентификаторы шаблонов устройств;
- типы данных измерения, минимальные и максимальные значения;
- типы данных свойств и значения по умолчанию;
- типы данных параметров и значения по умолчанию.

Примерно раз в минуту записывается новый моментальный снимок отчета, содержащий:

- новые шаблоны устройства, добавленные с момента создания последнего моментального снимка;
- шаблоны устройств, измерения, свойства и параметры которых изменились с момента создания последнего моментального снимка.

> [!NOTE]
> Шаблоны устройства, удаленные с момента создания последнего моментального снимка, не экспортируются. В моментальных снимках отсутствует индикатор для шаблонов устройств, удаленных в это время.

Каждая запись в декодированном файле AVRO выглядит следующим образом:

```json
{
    "id": "c318d580-39fc-4aca-b995-843719821049",
    "name": "Refrigerated Vending Machine",
    "version": "1.0.0",
    "measurements": {
        "telemetry": {
            "humidity": {
                "dataType": "double",
                "name": "Humidity"
            },
            "magnetometerX": {
                "dataType": "double",
                "name": "Magnetometer X"
            },
            "magnetometerY": {
                "dataType": "double",
                "name": "Magnetometer Y"
            },
            "magnetometerZ": {
                "dataType": "double",
                "name": "Magnetometer Z"
            }
        },
        "states": {
            "connectivity": {
                "dataType": "enum",
                "name": "Connectivity"
            }
        },
        "events": {
            "opened": {
                "name": "Door Opened",
                "category": "informational"
            }
        }
    },
    "settings": {
        "device": {
            "fanSpeed": {
                "dataType": "double",
                "name": "Fan Speed",
                "initialValue": 0
            }
        }
    },
    "properties": {
        "cloud": {
            "location": {
                "dataType": "string",
                "name": "Location",
                "initialValue": "Seattle"
            },
            "maintCon": {
                "dataType": "boolean",
                "name": "Maintenance Contract",
                "initialValue": true
            },
            "tempThresh": {
                "dataType": "double",
                "name": "Temperature Alert Threshold",
                "initialValue": 30
            }
        },
        "device": {
            "lastReboot": {
                "dataType": "dateTime",
                "name": "Last Reboot"
            }
        }
    }
}
```

## <a name="how-to-set-up-data-export"></a>Настройка экспорта данных

1. Создайте учетную запись хранения Azure в **подписке Azure, в которой находится ваше приложение**. [Щелкните здесь](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM), чтобы перейти на портал Azure и создать учетную запись хранения Azure.

    - Выберите тип учетной записи *Общее назначение* или *Хранилище BLOB-объектов*.
    - Выберите подписку, в которой находится приложение IoT Central. Если вы не видите подписку, возможно вам нужно войти в другую учетную запись Azure или запросить доступ к подписке.
    - Выберите имеющуюся группу ресурсов или создайте новую. Узнайте, [как создать учетную запись хранения.](https://aka.ms/blobdocscreatestorageaccount)

2. Создайте контейнер в учетной записи хранения для экспорта данных IoT Central. Перейдите ко вкладке учетной записи хранения, выберите "Обзор BLOB-объектов" и создайте контейнер. ![Создание образа контейнера](media/howto-export-data/createcontainer.png)

3. Войдите в приложение IoT Central, используя эту же учетную запись Azure.
1. Выберите "Управление -> Continuous Data Export" (Экспорт непрерывных данных).
![Экспорт непрерывных данных IoT Central](media/howto-export-data/continuousdataexport.PNG)
1. С помощью раскрывающихся списков выберите свою учетную запись хранения и контейнер. Затем используйте переключатели, чтобы включить или отключить различные типы данных для экспорта.
1. Наконец включите Экспорт непрерывных данных с помощью переключателя и нажмите кнопку "Сохранить".
1. Через несколько минут данные должны появиться в вашей учетной записи хранения. Вы можете перейти к своей учетной записи хранения, выбрать "Обзор BLOB-объектов", затем "Контейнер". Здесь вы увидите три папки. По умолчанию путь для файлов AVRO, содержащих различные типы данных, следующий.
- Для сообщений: **{container}/measurements/{hubname}/{YYYY}/{MM}/{dd}/{hh}/{mm}/00.avro**
- Для устройств: **{container}/devices/{hubname}/{YYYY}/{MM}/{dd}/{hh}/{mm}/00.avro**
- Для шаблонов устройств: **{container}/deviceTemplates/{hubname}/{YYYY}/{MM}/{dd}/{hh}/{mm}/00.avro**

## <a name="how-to-read-exported-avro-files"></a>Считывание экспортированных файлов AVRO

AVRO — формат двоичных данных, поэтому файлы невозможно считать в необработанном состоянии. Файлы можно декодировать в формат JSON. Далее показаны примеры анализа файлов AVRO с измерениями, устройствами и шаблонами устройств с помощью приведенных выше примеров.

## <a name="python"></a>Python

### <a name="install-pandas-and-the-pandaavro-package"></a>Установка пакета Pandas и PandaAvro:

```python
pip install pandas
pip install pandavro
```
### <a name="parse-measurements-avro-file"></a>Анализ измерений файла AVRO

```python
import json
import pandavro as pdx
import pandas as pd

def parse(filePath):
    # Pandavro loads the avro file into a pandas DataFrame where each record is
    # a single row
    measurements = pdx.from_avro(filePath)

    # In this example, we create a new DataFrame and load a series for each
    # column we map into a column in our new DataFrame.
    transformed = pd.DataFrame()

    # The SystemProperties column contains a dictionary with the device id
    # located under the "connectionDeviceId" key.
    transformed["device_id"] = measurements["SystemProperties"].apply(lambda x: x["connectionDeviceId"])

    # The Body column is a series of utf-8 bytes that is stringified and parsed
    # as json. In this example, we pull the "humidity" property off of each one
    # to get the humidity field.
    transformed["humidity"] = measurements["Body"].apply(lambda x: json.loads(bytes(x).decode('utf-8'))["humidity"])

    # Finally, we print the new DataFrame with our device ids and humidities
    print(transformed)

```
### <a name="parse-devices-avro-file"></a>Анализ устройств файла AVRO

```python
import json
import pandavro as pdx
import pandas as pd

def parse(filePath):
    # Pandavro loads the avro file into a pandas DataFrame where each record is
    # a single row
    devices = pdx.from_avro(filePath)

    # In this example, we create a new DataFrame and load a series for each
    # column we map into a column in our new DataFrame.
    transformed = pd.DataFrame()

    # The device id is available directly in the "id" column
    transformed["device_id"] = devices["id"]

    # The template id and version are present in a dictionary under the
    # deviceTemplate column
    transformed["template_id"] = devices["deviceTemplate"].apply(lambda x: x["id"])
    transformed["template_version"] = devices["deviceTemplate"].apply(lambda x: x["version"])

    # The fanSpeed setting value is located in a nested dictionary under the
    # settings column
    transformed["fan_speed"] = devices["settings"].apply(lambda x: x["device"]["fanSpeed"])

    # Finally, we print the new DataFrame with our device and template
    # information, along with the value of the fan speed
    print(transformed)

```

### <a name="parse-device-templates-avro-file"></a>Анализ шаблонов устройств файла AVRO

```python
import json
import pandavro as pdx
import pandas as pd

def parse(filePath):
    # Pandavro loads the avro file into a pandas DataFrame where each record is
    # a single row
    templates = pdx.from_avro(filePath)

    # In this example, we create a new DataFrame and load a series for each
    # column we map into a column in our new DataFrame.
    transformed = pd.DataFrame()

    # The template and version are available directly in the "id" and "version"
    # columns, respectively
    transformed["template_id"] = templates["id"]
    transformed["template_version"] = templates["version"]

    # The fanSpeed setting value is located in a nested dictionary under the
    # settings column
    transformed["fan_speed"] = templates["settings"].apply(lambda x: x["device"]["fanSpeed"])

    # Finally, we print the new DataFrame with our device and template
    # information, along with the value of the fan speed
    print(transformed)
```

## <a name="c"></a>C#

### <a name="install-microsofthadoopavro"></a>Установка Microsoft.Hadoop.Avro

```csharp
Install-Package Microsoft.Hadoop.Avro -Version 1.5.6
```

### <a name="parse-measurements-avro-file"></a>Анализ измерений файла AVRO

```csharp
using Microsoft.Hadoop.Avro;
using Microsoft.Hadoop.Avro.Container;
using Newtonsoft.Json;

public static async Task Run(string filePath)
{
    using (var fileStream = File.OpenRead(filePath))
    {
        using (var reader = AvroContainer.CreateGenericReader(fileStream))
        {
            // For one Avro Container, it may contain multiple blocks
            // Loop through each block within the container
            while (reader.MoveNext())
            {
                // Loop through Avro record inside the block and extract the fields
                foreach (AvroRecord record in reader.Current.Objects)
                {
                    var systemProperties = record.GetField<IDictionary<string, object>>("SystemProperties");
                    var deviceId = systemProperties["connectionDeviceId"] as string;
                    Console.WriteLine("Device ID: {0}", deviceId);

                    using (var stream = new MemoryStream(record.GetField<byte[]>("Body")))
                    {
                        using (var streamReader = new StreamReader(stream, Encoding.UTF8))
                        {
                            var body = JsonSerializer.Create().Deserialize(streamReader, typeof(IDictionary<string, dynamic>)) as IDictionary<string, dynamic>;
                            var humidity = body["humidity"];
                            Console.WriteLine("Humidity: {0}", humidity);
                        }
                    }
                }
            }
        }
    }
}
```

### <a name="parse-devices-avro-file"></a>Анализ устройств файла AVRO

```csharp
using Microsoft.Hadoop.Avro;
using Microsoft.Hadoop.Avro.Container;

public static async Task Run(string filePath)
{
    using (var fileStream = File.OpenRead(filePath))
    {
        using (var reader = AvroContainer.CreateGenericReader(fileStream))
        {
            // For one Avro Container, it may contains multiple blocks
            // Loop through each block within the container
            while (reader.MoveNext())
            {
                // Loop through Avro record inside the block and extract the
                // fields from it
                foreach (AvroRecord record in reader.Current.Objects)
                {
                    // Get the field value directly. You can also yield return
                    // record and make the function IEnumerable<AvroRecord>
                    var deviceId = record.GetField<string>("id");

                    // The device template information is stored in a sub-record
                    // under the "deviceTemplate" field.
                    var deviceTemplateRecord = record.GetField<AvroRecord>("deviceTemplate");
                    var templateId = deviceTemplateRecord.GetField<string>("id");
                    var templateVersion = deviceTemplateRecord.GetField<string>("version");

                    // The settings and properties are nested two levels deep,
                    // with the first level indicating settings or properties
                    // and the second indicating the kind of setting/property
                    var settingsRecord = record.GetField<AvroRecord>("settings");
                    var deviceSettingsRecord = settingsRecord.GetField<IDictionary<string, dynamic>>("device");
                    var fanSpeed = deviceSettingsRecord["fanSpeed"];
                    
                    Console.WriteLine(
                        "ID: {0}, Template ID: {1}, Template Version: {2}, Fan Speed: {3}",
                        deviceId,
                        templateId,
                        templateVersion,
                        fanSpeed
                    );
                }
            }
        }
    }
}

```
### <a name="parse-device-templates-avro-file"></a>Анализ шаблонов устройств файла AVRO

```csharp
using Microsoft.Hadoop.Avro;
using Microsoft.Hadoop.Avro.Container;

public static async Task Run(string filePath)
{
    using (var fileStream = File.OpenRead(filePath))
    {
        using (var reader = AvroContainer.CreateGenericReader(fileStream))
        {
            // For one Avro Container, it may contains multiple blocks
            // Loop through each block within the container
            while (reader.MoveNext())
            {
                // Loop through Avro record inside the block and extract the
                // fields from it
                foreach (AvroRecord record in reader.Current.Objects)
                {
                    // Get the field value directly. You can also yield return
                    // record and make the function IEnumerable<AvroRecord>
                    var id = record.GetField<string>("id");
                    var version = record.GetField<string>("version");

                    // The settings and properties are nested two levels deep,
                    // with the first level indicating settings or properties
                    // and the second indicating the kind of setting/property
                    var settingsRecord = record.GetField<AvroRecord>("settings");
                    var deviceSettingsRecord = settingsRecord.GetField<IDictionary<string, dynamic>>("device");
                    var fanSpeed = deviceSettingsRecord["fanSpeed"];
                    
                    Console.WriteLine(
                        "ID: {1}, Version: {2}, Fan Speed: {3}",
                        id,
                        version,
                        fanSpeed
                    );
                }
            }
        }
    }
}
```

## <a name="javascript"></a>JavaScript

### <a name="install-avsc"></a>Установка avsc

```javascript
npm install avsc
```

### <a name="parse-measurements-avro-file"></a>Анализ измерений файла AVRO

```javascript
const avro = require('avsc');

// Read the avro file and parse the device id and humidity from each record
async function parse(filePath) {
    const records = await load(filePath);
    for (const record of records) {
        // Fetch the device id from the system properties
        const deviceId = record.SystemProperties.connectionDeviceId;

        // Convert the Body from a Buffer to a string and parse it
        const body = JSON.parse(record.Body.toString());

        // Get the humidty property off of the body
        const humidity = body.humidity;

        // Log the device id and humidity we found
        console.log(`Device ID: ${deviceId}`);
        console.log(`Humidity: ${humidity}`);
    }
}

function load(filePath) {
    return new Promise((resolve, reject) => {
        // The file decoder emits each record as a data event on a stream. We
        // collect them up into an array and return them when we get to the end.
        const records = [];
        avro.createFileDecoder(filePath)
            .on('data', record => { records.push(record); })
            .on('end', () => resolve(records))
            .on('error', reject);
    });
}
```

### <a name="parse-devices-avro-file"></a>Анализ устройств файла AVRO

```javascript
const avro = require('avsc');

// Read the avro file and parse the device and template identification info as
// well as the fanSpeed setting for each device record.
async function parse(filePath) {
    const records = await load(filePath);
    for (const record of records) {
        // Fetch the device id from the id property
        const deviceId = record.id;

        // Fetch the tempalte id and version from the deviceTemplate property
        const deviceTemplateId = record.deviceTemplate.id;
        const deviceTemplateVersion = record.deviceTemplate.version;

        // Get the fanSpeed off the nested device settings property
        const fanSpeed = record.settings.device.fanSpeed;

        // Log the device id and humidity we found
        console.log(`ID: ${deviceId}, Template ID: ${deviceTemplateId}, Template Version: ${deviceTemplateVersion}, Fan Speed: ${fanSpeed}`);
    }
}

function load(filePath) {
    return new Promise((resolve, reject) => {
        // The file decoder emits each record as a data event on a stream. We
        // collect them up into an array and return them when we get to the end.
        const records = [];
        avro.createFileDecoder(filePath)
            .on('data', record => { records.push(record); })
            .on('end', () => resolve(records))
            .on('error', reject);
    });
}
```

### <a name="parse-device-templates-avro-file"></a>Анализ шаблонов устройств файла AVRO

```javascript
const avro = require('avsc');

// Read the avro file and parse the device and template identification info as
// well as the fanSpeed setting for each device record.
async function parse(filePath) {
    const records = await load(filePath);
    for (const record of records) {
        // Fetch the template id and version from the id and verison properties
        const templateId = record.id;
        const templateVersion = record.version;

        // Get the fanSpeed off the nested device settings property
        const fanSpeed = record.settings.device.fanSpeed;

        // Log the device id and humidity we found
        console.log(`Template ID: ${templateId}, Template Version: ${templateVersion}, Fan Speed: ${fanSpeed}`);
    }
}

function load(filePath) {
    return new Promise((resolve, reject) => {
        // The file decoder emits each record as a data event on a stream. We
        // collect them up into an array and return them when we get to the end.
        const records = [];
        avro.createFileDecoder(filePath)
            .on('data', record => { records.push(record); })
            .on('end', () => resolve(records))
            .on('error', reject);
    });
}
```

## <a name="next-steps"></a>Дополнительная информация

Теперь, когда вы узнали, как экспортировать данные, предлагаем ознакомиться со статьей

> [!div class="nextstepaction"]
> [Visualize and analyze your Azure IoT Central data in a Power BI dashboard](howto-connect-powerbi.md) (Визуализация и анализ данных Azure IoT Central в Power BI)