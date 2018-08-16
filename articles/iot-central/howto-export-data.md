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
ms.openlocfilehash: 3ca2bc56c03e5bbabbd9b2f17edc621bdd94b02f
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/08/2018
ms.locfileid: "39622489"
---
# <a name="export-your-data-in-azure-iot-central"></a>Экспорт данных в Azure IoT Central

В этой статье описывается, как использовать возможность экспорта непрерывных данных в Azure IoT Central для периодического экспорта данных в учетную запись хранилища BLOB-объектов Azure. Вы можете экспортировать **измерения**, **устройства** и **шаблоны устройств** в файлы формата [Apache AVRO](https://avro.apache.org/docs/current/index.html). Экспортированные данные можно использовать для анализа холодного пути, например для обучения моделей в службе "Машинное обучение Azure" и долгосрочного анализа тенденций в Microsoft Power BI.

> [!Note]
> С момента включения экспорта непрерывных данных вы получаете только поступающие данные. Сейчас невозможно получить данные за то время, когда непрерывный экспорт данных был отключен. Чтобы сохранить больше исторических данных, включите экспорт непрерывных данных раньше.

## <a name="prerequisites"></a>Предварительные требования

- Расширенная 30-дневная пробная версия приложения IoT Central или платное приложение.
- Учетная запись Azure с подпиской Azure.
- Эта же учетная запись Azure должна иметь роль администратора в приложении IoT Central.
- Эта же учетная запись Azure должна иметь разрешения на создание учетной записи хранения или на доступ к имеющейся учетной записи хранения в той же подписке Azure.

## <a name="types-of-data-to-export"></a>Тип данных для экспорта

### <a name="measurements"></a>Измерения

Измерения, отправляемые устройствами, экспортируются в вашу учетную запись хранения один раз в минуту. В данных содержатся все новые сообщения, полученные IoT Central со всех устройств в течение этого времени. Экспортированные файлы AVRO используют тот же формат, что и файлы сообщений, экспортированные с использованием [маршрутизации сообщений с помощью Центра Интернета вещей](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-csharp-csharp-process-d2c) в хранилище BLOB-объектов.

> [!NOTE]
> Устройства, отправляющие измерения, представлены идентификаторами устройств (см. следующие разделы). Чтобы получить имена устройств, экспортируйте моментальные снимки устройств. Коррелируйте каждую запись сообщения с использованием идентификатора **connectionDeviceId**, который совпадает с идентификатором устройства.

В следующем примере показана запись в декодированный файл AVRO:

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

При первом включении экспорта непрерывных данных экспортируется один моментальный снимок со всеми устройствами. Моментальный снимок содержит:
- идентификаторы устройств;
- имена устройств;
- идентификаторы шаблонов устройств;
- значения свойств;
- значения параметров.

Новый моментальный снимок записывается один раз в минуту. Моментальный снимок содержит:

- новые устройства, добавленные с момента создания последнего моментального снимка;
- устройства с измененными значениями свойств и параметров с момента создания последнего моментального снимка.

> [!NOTE]
> Устройства, удаленные с момента создания последнего моментального снимка, не экспортируются. Сейчас в моментальных снимках нет индикаторов удаленных устройств.
>
> Шаблон устройства, к которому принадлежит каждое устройство, представлен идентификатором шаблона устройства. Чтобы получить имя шаблона устройства, экспортируйте моментальные снимки шаблонов устройств.

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

При первом включении экспорта непрерывных данных экспортируется один моментальный снимок со всеми шаблонами устройств. Моментальный снимок содержит: 
- идентификаторы шаблонов устройств;
- типы данных измерения, минимальные и максимальные значения;
- типы данных свойств и значения по умолчанию;
- типы данных параметров и значения по умолчанию.

Новый моментальный снимок записывается один раз в минуту. Моментальный снимок содержит:

- новые шаблоны устройств, добавленные с момента создания последнего моментального снимка;
- шаблоны устройств с измененными измерениями, свойствами и параметрами с момента создания последнего моментального снимка.

> [!NOTE]
> Шаблоны устройств, удаленные с момента создания последнего моментального снимка, не экспортируются. Сейчас в моментальных снимках нет индикаторов удаленных шаблонов устройств.

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

## <a name="set-up-continuous-data-export"></a>Настройка экспорта непрерывных данных

1. Если у вас нет учетной записи хранения Azure, [создайте ее](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM) на портале Azure. Создайте учетную запись хранения **в подписке Azure, в которой находится ваше приложение IoT Central**.
    - Выберите тип учетной записи **Общее назначение** или **Хранилище BLOB-объектов**.
    - Выберите подписку, в которой находится приложение IoT Central. Если вы не видите подписку, возможно, вам нужно войти в другую учетную запись Azure или запросить доступ к подписке.
    - Выберите имеющуюся группу ресурсов или создайте новую. Узнайте, [как создать учетную запись хранения.](https://aka.ms/blobdocscreatestorageaccount)

2. Создайте контейнер в учетной записи хранения для экспорта данных IoT Central. Войдите в свою учетную запись хранения. Выберите **Обзор BLOB-объектов** в разделе **Служба BLOB-объектов**. Выберите **Контейнер** для создания контейнера.

   ![Создание контейнера](media/howto-export-data/createcontainer.png)

3. Войдите в приложение IoT Central, используя эту же учетную запись Azure.

4. В разделе **Администрирование** выберите **Экспорт данных**.

   ![Настройка экспорта непрерывных данных](media/howto-export-data/continuousdataexport.PNG)

5. В раскрывающемся списке **Учетная запись хранения** выберите имя учетной записи хранения. В раскрывающемся списке **Контейнер** выберите контейнер. В разделе **Data to export** (Данные для экспорта) укажите каждый тип данных для экспорта, задав для типа значение **Включено**.

6. Чтобы включить экспорт непрерывных данных, задайте для параметра **Экспорт данных** значение **Включено**. Щелкните **Сохранить**.

7. Через несколько минут данные появятся в вашей учетной записи хранения. Перейдите в свою учетную запись хранения. Выберите **Обзор BLOB-объектов**, а затем свой контейнер. Отображаются три папки для экспорта данных. Пути по умолчанию для файлов AVRO с данными экспорта следующие:
    - Для сообщений: {контейнер}/measurements/{имя центра}/{ГГГГ}/{ММ}/{дд}/{чч}/{мм}/00.avro
    - Для устройств: {контейнер}/devices/{имя центра}/{ГГГГ}/{ММ}/{дд}/{чч}/{мм}/00.avro
    - Для шаблонов устройств: {контейнер}/deviceTemplates/{имя центра}/{ГГГГ}/{ММ}/{дд}/{чч}/00.avro

## <a name="read-exported-avro-files"></a>Чтение экспортированных файлов AVRO

AVRO — формат двоичных данных, поэтому файлы невозможно считать в необработанном состоянии. Файлы можно декодировать в формат JSON. Далее показаны примеры анализа файлов AVRO с измерениями, устройствами и шаблонами устройств. Примеры соответствуют примерам, описанным в предыдущем разделе.

### <a name="read-avro-files-by-using-python"></a>Чтение файлов AVRO с помощью Python

#### <a name="install-pandas-and-the-pandavro-package"></a>Установка пакета pandas и pandaavro

```python
pip install pandas
pip install pandavro
```

#### <a name="parse-a-measurements-avro-file"></a>Анализ файла AVRO с измерениями

```python
import json
import pandavro as pdx
import pandas as pd

def parse(filePath):
    # Pandavro loads the AVRO file into a pandas DataFrame
    # where each record is a single row.
    measurements = pdx.from_avro(filePath)

    # This example creates a new DataFrame and loads a series
    # for each column that's mapped into a column in our new DataFrame.
    transformed = pd.DataFrame()

    # The SystemProperties column contains a dictionary
    # with the device ID located under the connectionDeviceId key.
    transformed["device_id"] = measurements["SystemProperties"].apply(lambda x: x["connectionDeviceId"])

    # The Body column is a series of UTF-8 bytes that is stringified
    # and parsed as JSON. This example pulls the humidity property
    # from each column to get the humidity field.
    transformed["humidity"] = measurements["Body"].apply(lambda x: json.loads(bytes(x).decode('utf-8'))["humidity"])

    # Finally, print the new DataFrame with our device IDs and humidities.
    print(transformed)

```

#### <a name="parse-a-devices-avro-file"></a>Анализ файла AVRO с устройствами

```python
import json
import pandavro as pdx
import pandas as pd

def parse(filePath):
    # Pandavro loads the AVRO file into a pandas DataFrame
    # where each record is a single row.
    devices = pdx.from_avro(filePath)

    # This example creates a new DataFrame and loads a series
    # for each column that's mapped into a column in our new DataFrame.
    transformed = pd.DataFrame()

    # The device ID is available in the id column.
    transformed["device_id"] = devices["id"]

    # The template ID and version are present in a dictionary under
    # the deviceTemplate column.
    transformed["template_id"] = devices["deviceTemplate"].apply(lambda x: x["id"])
    transformed["template_version"] = devices["deviceTemplate"].apply(lambda x: x["version"])

    # The fanSpeed setting value is located in a nested dictionary
    # under the settings column.
    transformed["fan_speed"] = devices["settings"].apply(lambda x: x["device"]["fanSpeed"])

    # Finally, print the new DataFrame with our device and template
    # information, along with the value of the fan speed.
    print(transformed)

```

#### <a name="parse-a-device-templates-avro-file"></a>Анализ файла AVRO с шаблонами устройств

```python
import json
import pandavro as pdx
import pandas as pd

def parse(filePath):
    # Pandavro loads the AVRO file into a pandas DataFrame
    # where each record is a single row.
    templates = pdx.from_avro(filePath)

    # This example creates a new DataFrame and loads a series
    # for each column that's mapped into a column in our new DataFrame.
    transformed = pd.DataFrame()

    # The template and version are available in the id and version columns.
    transformed["template_id"] = templates["id"]
    transformed["template_version"] = templates["version"]

    # The fanSpeed setting value is located in a nested dictionary
    # under the settings column.
    transformed["fan_speed"] = templates["settings"].apply(lambda x: x["device"]["fanSpeed"])

    # Finally, print the new DataFrame with our device and template
    # information, along with the value of the fan speed.
    print(transformed)
```

### <a name="read-avro-files-by-using-c"></a>Чтение файлов AVRO с помощью C#

#### <a name="install-the-microsofthadoopavro-package"></a>Установка пакета Microsoft.Hadoop.Avro

```csharp
Install-Package Microsoft.Hadoop.Avro -Version 1.5.6
```

#### <a name="parse-a-measurements-avro-file"></a>Анализ файла AVRO с измерениями

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
            // For one AVRO container, where a container can contain multiple blocks,
            // loop through each block in the container.
            while (reader.MoveNext())
            {
                // Loop through the AVRO records in the block and extract the fields.
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

#### <a name="parse-a-devices-avro-file"></a>Анализ файла AVRO с устройствами

```csharp
using Microsoft.Hadoop.Avro;
using Microsoft.Hadoop.Avro.Container;

public static async Task Run(string filePath)
{
    using (var fileStream = File.OpenRead(filePath))
    {
        using (var reader = AvroContainer.CreateGenericReader(fileStream))
        {
            // For one AVRO container, where a container can contain multiple blocks,
            // loop through each block in the container.
            while (reader.MoveNext())
            {
                // Loop through the AVRO records in the block and extract the fields.
                foreach (AvroRecord record in reader.Current.Objects)
                {
                    // Get the field value directly. You can also yield return
                    // records and make the function IEnumerable<AvroRecord>.
                    var deviceId = record.GetField<string>("id");

                    // The device template information is stored in a sub-record
                    // under the deviceTemplate field.
                    var deviceTemplateRecord = record.GetField<AvroRecord>("deviceTemplate");
                    var templateId = deviceTemplateRecord.GetField<string>("id");
                    var templateVersion = deviceTemplateRecord.GetField<string>("version");

                    // The settings and properties are nested two levels deep.
                    // The first level indicates settings or properties.
                    // The second level indicates the type of setting or property.
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

#### <a name="parse-a-device-templates-avro-file"></a>Анализ файла AVRO с шаблонами устройств

```csharp
using Microsoft.Hadoop.Avro;
using Microsoft.Hadoop.Avro.Container;

public static async Task Run(string filePath)
{
    using (var fileStream = File.OpenRead(filePath))
    {
        using (var reader = AvroContainer.CreateGenericReader(fileStream))
        {
            // For one AVRO container, where a container can contain multiple blocks,
            // loop through each block in the container.
            while (reader.MoveNext())
            {
                // Loop through the AVRO records in the block and extract the fields.
                foreach (AvroRecord record in reader.Current.Objects)
                {
                    // Get the field value directly. You can also yield return
                    // records and make the function IEnumerable<AvroRecord>.
                    var id = record.GetField<string>("id");
                    var version = record.GetField<string>("version");

                    // The settings and properties are nested two levels deep.
                    // The first level indicates settings or properties.
                    // The second level indicates the type of setting or property.
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

### <a name="read-avro-files-by-using-javascript"></a>Чтение файлов AVRO с помощью JavaScript

#### <a name="install-the-avsc-package"></a>Установка пакета avsc

```javascript
npm install avsc
```

#### <a name="parse-a-measurements-avro-file"></a>Анализ файла AVRO с измерениями

```javascript
const avro = require('avsc');

// Read the AVRO file. Parse the device ID and humidity from each record.
async function parse(filePath) {
    const records = await load(filePath);
    for (const record of records) {
        // Fetch the device ID from the system properties.
        const deviceId = record.SystemProperties.connectionDeviceId;

        // Convert the body from a buffer to a string and parse it.
        const body = JSON.parse(record.Body.toString());

        // Get the humidty property from the body.
        const humidity = body.humidity;

        // Log the retrieved device ID and humidity.
        console.log(`Device ID: ${deviceId}`);
        console.log(`Humidity: ${humidity}`);
    }
}

function load(filePath) {
    return new Promise((resolve, reject) => {
        // The file decoder emits each record as a data event on a stream.
        // Collect the records into an array and return them at the end.
        const records = [];
        avro.createFileDecoder(filePath)
            .on('data', record => { records.push(record); })
            .on('end', () => resolve(records))
            .on('error', reject);
    });
}
```

#### <a name="parse-a-devices-avro-file"></a>Анализ файла AVRO с устройствами

```javascript
const avro = require('avsc');

// Read the AVRO file. Parse the device and template identification
// information and the fanSpeed setting for each device record.
async function parse(filePath) {
    const records = await load(filePath);
    for (const record of records) {
        // Fetch the device ID from the id property.
        const deviceId = record.id;

        // Fetch the template ID and version from the deviceTemplate property.
        const deviceTemplateId = record.deviceTemplate.id;
        const deviceTemplateVersion = record.deviceTemplate.version;

        // Get the fanSpeed from the nested device settings property.
        const fanSpeed = record.settings.device.fanSpeed;

        // Log the retrieved device ID and humidity.
        console.log(`ID: ${deviceId}, Template ID: ${deviceTemplateId}, Template Version: ${deviceTemplateVersion}, Fan Speed: ${fanSpeed}`);
    }
}

function load(filePath) {
    return new Promise((resolve, reject) => {
        // The file decoder emits each record as a data event on a stream.
        // Collect the records into an array and return them at the end.
        const records = [];
        avro.createFileDecoder(filePath)
            .on('data', record => { records.push(record); })
            .on('end', () => resolve(records))
            .on('error', reject);
    });
}
```

#### <a name="parse-a-device-templates-avro-file"></a>Анализ файла AVRO с шаблонами устройств

```javascript
const avro = require('avsc');

// Read the AVRO file. Parse the device and template identification
// information and the fanSpeed setting for each device record.
async function parse(filePath) {
    const records = await load(filePath);
    for (const record of records) {
        // Fetch the template ID and version from the id and verison properties.
        const templateId = record.id;
        const templateVersion = record.version;

        // Get the fanSpeed from the nested device settings property.
        const fanSpeed = record.settings.device.fanSpeed;

        // Log the retrieved device id and humidity.
        console.log(`Template ID: ${templateId}, Template Version: ${templateVersion}, Fan Speed: ${fanSpeed}`);
    }
}

function load(filePath) {
    return new Promise((resolve, reject) => {
        // The file decoder emits each record as a data event on a stream.
        // Collect the records into an array and return them at the end.
        const records = [];
        avro.createFileDecoder(filePath)
            .on('data', record => { records.push(record); })
            .on('end', () => resolve(records))
            .on('error', reject);
    });
}
```

## <a name="next-steps"></a>Дополнительная информация

Теперь, когда вы знаете, как экспортировать данные, перейдите к следующему шагу:

> [!div class="nextstepaction"]
> [Visualize and analyze your Azure IoT Central data in a Power BI dashboard](howto-connect-powerbi.md) (Визуализация и анализ данных Azure IoT Central в Power BI)
