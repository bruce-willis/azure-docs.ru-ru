---
title: включение файла
description: включение файла
services: iot-accelerators
author: dominicbetts
ms.service: iot-accelerators
ms.topic: include
ms.date: 09/28/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 5eb3c08792b760bf66e443f79762d91210706c92
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/28/2018
ms.locfileid: "47435118"
---
В первом сценарии вы добавляете новый тип телеметрии к существующему типу устройства **охладителя** компании Contoso.

Во втором сценарии компания Contoso планирует протестировать новое интеллектуальное устройство управления освещением. Чтобы выполнить тесты, необходимо создать новое виртуальное устройство со следующими характеристиками:

*Свойства*

| ИМЯ                     | Значения                      |
| ------------------------ | --------------------------- |
| Цвет                    | Белый, красный, синий            |
| Яркость               | 0–100                    |
| Ожидаемое время работы | Обратный отсчет, начиная с 10 000 часов |

*Телеметрия*

В таблице ниже приведены данные, которые система освещения потоком передает в облако.

| ИМЯ   | Значения      |
| ------ | ----------- |
| Status | on, off |
| температура; | Градусы по Фаренгейту |
| в сети | true, false |

> [!NOTE]
> Значение телеметрии **в сети** является обязательным для всех типов имитации.

*Методы*

В таблице ниже приведены действия, поддерживаемые новым устройством.

| ИМЯ        |
| ----------- |
| Включение   |
| Выключение  |

*Начальное состояние*

В таблице ниже представлены начальные показатели устройства.

| ИМЯ                     | Значения |
| ------------------------ | -------|
| Начальный цвет            | Белый  |
| Начальная яркость       | 75     |
| Начальное ожидаемое время работы   | 10 000 |
| Начальное состояние передачи данных телеметрии | on   |
| Начальная температура телеметрии | 200   |

Чтобы выполнить действия, описанные в этом руководстве, вам потребуется активная подписка Azure.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

[!INCLUDE [cloud-shell-try-it.md](cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Предварительные требования

Чтобы следовать этому пошаговому руководству, вам необходимы следующие компоненты.

* Visual Studio Code. Вы можете [скачать Visual Studio Code для Mac, Linux и Windows](https://code.visualstudio.com/download).
* .NET Core. Вы можете скачать [.NET Core для Mac, Linux и Windows](https://www.microsoft.com/net/download).
* [C# для Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp);
* Postman. Вы можете скачать [Postman для Mac, Windows или Linux](https://www.getpostman.com/apps).
* [Центр Интернета вещей для вашей подписки Azure](../articles/iot-hub/iot-hub-create-through-portal.md). Для выполнения шагов этого руководства вам понадобится строка подключения Центра Интернета вещей. Вы можете получить строку подключения с портала Azure.
* База данных Cosmos DB, которая использует SQL API и которая настроена на [строгую согласованность](../articles/cosmos-db/manage-account.md). Для выполнения шагов этого руководства вам понадобится строка подключения базы данных Cosmos DB. Вы можете получить строку подключения с портала Azure.

## <a name="prepare-your-development-environment"></a>Подготовка среды разработки

Для подготовки среды разработки выполните следующие действия:

* загрузите исходный код для микрослужбы моделирования устройств;
* загрузите исходный код для микрослужбы адаптера хранилища;
* запустите микрослужбу адаптера хранилища локально.

В инструкциях этой статьи предполагается, что вы используете Windows. Если вы используете другую операционную систему, может потребоваться изменить некоторые пути к файлам и команды в соответствии с требованиями вашей среды.

### <a name="download-the-microservices"></a>Загрузка микрослужб

Скачайте и распакуйте [микрослужбы мониторинга Azure](https://github.com/Azure/remote-monitoring-services-dotnet/archive/master.zip) из GitHub в подходящее место на локальном компьютере. В этой статье предполагается, что эта папка называется **remote-monitoring-services-dotnet-master**.

Скачайте и распакуйте [микрослужбу моделирования устройств](https://github.com/Azure/device-simulation-dotnet/archive/master.zip) из GitHub в подходящее место на вашем локальном компьютере. В этой статье предполагается, что эта папка называется **device-simulation-dotnet-master**.

### <a name="run-the-storage-adapter-microservice"></a>Запуск микрослужбы адаптера хранилища

Откройте папку **remote-monitoring-services-dotnet-master\storage-adapter** в Visual Studio Code. Чтобы исправить любые нерешенные зависимости, щелкните любую кнопку **Восстановить**.

Откройте файл **.vscode/launch.json** и назначьте строку подключения Cosmos DB для переменной среды **PCS_STORAGEADAPTER_DOCUMENTDB_CONNSTRING**.

<!-- Open the **WebService/appsettings.ini** file and assign your Cosmos DB connection string to the **documentdb_connstring** configuration setting.-->

Чтобы запустить микрослужбу локально, нажмите **Отладка > Начать отладку**.

Окно **Терминал** в Visual Studio Code отображает выходные данные из работающей микрослужбы, включая URL-адрес проверки работоспособности веб-службы: [http://127.0.0.1:9022/v1/status](http://127.0.0.1:9022/v1/status). При переходе на этот адрес, состояние должно быть "OK: живой и отлично".

Оставьте микрослужбу адаптера хранилища запущенной в данном экземпляре Visual Studio Code, пока вы выполняете следующие действия.

## <a name="modify-the-chiller"></a>Изменения охладителя

В этом разделе вы добавите новый тип телеметрии **Внутренняя температура** для существующего типа устройства **охладителя**.

1. Создайте новую папку **C:\temp\devicemodels** на локальном компьютере.

1. Скопируйте следующие файлы в новую папку, из загруженной копии микрослужбы моделирования устройств.

    | Источник | Место назначения |
    | ------ | ----------- |
    | Services\data\devicemodels\chiller-01.json | C:\temp\devicemodels\chiller-01.json |
    | Services\data\devicemodels\scripts\chiller-01-state.js | C:\temp\devicemodels\scripts\chiller-01-state.js |
    | Services\data\devicemodels\scripts\Reboot-method.js | C:\temp\devicemodels\scripts\Reboot-method.js |
    | Services\data\devicemodels\scripts\FirmwareUpdate-method.js | C:\temp\devicemodels\scripts\FirmwareUpdate-method.js |
    | Services\data\devicemodels\scripts\EmergencyValveRelease-method.js | C:\temp\devicemodels\scripts\EmergencyValveRelease-method.js |
    | Services\data\devicemodels\scripts\IncreasePressure-method.js | C:\temp\devicemodels\scripts\IncreasePressure-method.js |

1. Откройте файл **C:\temp\devicemodels\chiller-01.json**.

1. В разделе **InitialState** добавьте два следующих определения:

    ```json
    "internal_temperature": 65.0,
    "internal_temperature_unit": "F",
    ```

1. В массиве **Telemetry** добавьте следующее определение:

    ```json
    {
      "Interval": "00:00:05",
      "MessageTemplate": "{\"internal_temperature\":${internal_temperature},\"internal_temperature_unit\":\"${internal_temperature_unit}\"}",
      "MessageSchema": {
        "Name": "chiller-internal-temperature;v1",
        "Format": "JSON",
        "Fields": {
          "temperature": "double",
          "temperature_unit": "text"
        }
      }
    },
    ```

1. Сохраните файл **C:\temp\devicemodels\chiller-01.json**.

1. Откройте файл **C:\temp\devicemodels\scripts\chiller-01-state.js**.

1. Добавьте в переменную **state** следующие поля:

    ```js
    internal_temperature: 65.0,
    internal_temperature_unit: "F",
    ```

1. Обновите функцию **main** следующим образом.

    ```js
    function main(context, previousState, previousProperties) {

        // Restore the global state before generating the new telemetry, so that
        // the telemetry can apply changes using the previous function state.
        restoreSimulation(previousState, previousProperties);

        // 75F +/- 5%,  Min 25F, Max 100F
        state.temperature = vary(75, 5, 25, 100);

        // 70% +/- 5%,  Min 2%, Max 99%
        state.humidity = vary(70, 5, 2, 99);

        // 65F +/- 2%,  Min 15F, Max 125F
        state.internal_temperature = vary(65, 2, 15, 125);

        log("Simulation state: " + state.simulation_state);
        if (state.simulation_state === "high_pressure") {
            // 250 psig +/- 25%,  Min 50 psig, Max 300 psig
            state.pressure = vary(250, 25, 50, 300);
        } else {
            // 150 psig +/- 10%,  Min 50 psig, Max 300 psig
            state.pressure = vary(150, 10, 50, 300);
        }

        updateState(state);
        return state;
    }
    ```

1. Сохраните файл **C:\temp\devicemodels\scripts\chiller-01-state.js**.

## <a name="create-the-lightbulb"></a>Создание лампочки

В этом разделе вы определите новый тип устройства **лампочка**.

1. Создайте файл **C:\temp\devicemodels\lightbulb-01.json** и добавьте следующее содержимое.

    ```json
    {
      "SchemaVersion": "1.0.0",
      "Id": "lightbulb-01",
      "Version": "0.0.1",
      "Name": "Lightbulb",
      "Description": "Smart lightbulb device.",
      "Protocol": "MQTT",
      "Simulation": {
        "InitialState": {
          "online": true,
          "temperature": 200.0,
          "temperature_unit": "F",
          "status": "on"
        },
        "Interval": "00:00:20",
        "Scripts": [
          {
            "Type": "javascript",
            "Path": "lightbulb-01-state.js"
          }
        ]
      },
      "Properties": {
        "Type": "Lightbulb",
        "Color": "White",
        "Brightness": 75,
        "EstimatedRemainingLife": 10000
      },
      "Tags": {
        "Location": "Building 2",
        "Floor": "2",
        "Campus": "Redmond"
      },
      "Telemetry": [
        {
          "Interval": "00:00:20",
          "MessageTemplate": "{\"temperature\":${temperature},\"temperature_unit\":\"${temperature_unit}\",\"status\":\"${status}\"}",
          "MessageSchema": {
            "Name": "lightbulb-status;v1",
            "Format": "JSON",
            "Fields": {
              "temperature": "double",
              "temperature_unit": "text",
              "status": "text"
            }
          }
        }
      ],
      "CloudToDeviceMethods": {
        "SwitchOn": {
          "Type": "javascript",
          "Path": "SwitchOn-method.js"
        },
        "SwitchOff": {
          "Type": "javascript",
          "Path": "SwitchOff-method.js"
        }
      }
    }
    ```

    Сохраните изменения в **C:\temp\devicemodels\lightbulb-01.json**.

1. Создайте файл **C:\temp\devicemodels\scripts\lightbulb-01-state.js** и добавьте следующее содержимое.

    ```javascript
    "use strict";

    // Default state
    var state = {
      online: true,
      temperature: 200.0,
      temperature_unit: "F",
      status: "on"
    };

    // Default device properties
    var properties = {};

    /**
     * Restore the global state using data from the previous iteration.
     *
     * @param previousState device state from the previous iteration
     * @param previousProperties device properties from the previous iteration
     */
    function restoreSimulation(previousState, previousProperties) {
      // If the previous state is null, force a default state
      if (previousState) {
        state = previousState;
      } else {
        log("Using default state");
      }

      if (previousProperties) {
        properties = previousProperties;
      } else {
        log("Using default properties");
      }
    }

    /**
     * Simple formula generating a random value around the average
     * in between min and max
     *
     * @returns random value with given parameters
     */
    function vary(avg, percentage, min, max) {
      var value = avg * (1 + ((percentage / 100) * (2 * Math.random() - 1)));
      value = Math.max(value, min);
      value = Math.min(value, max);
      return value;
    }

    /**
     * Simple formula that sometimes flips the status of the lightbulb
     */
    function flip(value) {
      if (Math.random() < 0.2) {
        return (value == "on") ? "off" : "on"
      }
      return value;
    }

    /**
     * Entry point function called by the simulation engine.
     * Returns updated simulation state.
     * Device property updates must call updateProperties() to persist.
     *
     * @param context             The context contains current time, device model and id
     * @param previousState       The device state since the last iteration
     * @param previousProperties  The device properties since the last iteration
     */
    function main(context, previousState, previousProperties) {

      // Restore the global device properties and the global state before
      // generating the new telemetry, so that the telemetry can apply changes
      // using the previous function state.
      restoreSimulation(previousState, previousProperties);

      state.temperature = vary(200, 5, 150, 250);

      // Make this flip every so often
      state.status = flip(state.status);

      updateState(state);

      return state;
    }
    ```

    Сохраните изменения в **C:\temp\devicemodels\scripts\lightbulb-01-state.js**.

1. Создайте файл **C:\temp\devicemodels\scripts\SwitchOn-method.js** и добавьте следующее содержимое.

    ```javascript
    "use strict";

    // Default state
    var state = {
      status: "on"
    };

    /**
     * Entry point function called by the method.
     *
     * @param context        The context contains current time, device model and id
     * @param previousState  The device state since the last iteration
     * @param previousProperties  The device properties since the last iteration
     */
    function main(context, previousState) {
      log("Executing lightbulb Switch On method.");
      state.status = "on";
      updateState(state);
    }
    ```

    Сохраните изменения в **C:\temp\devicemodels\scripts\SwitchOn-method.js**.

1. Создайте файл **C:\temp\devicemodels\scripts\SwitchOff-method.js** и добавьте следующее содержимое.

    ```javascript
    "use strict";

    // Default state
    var state = {
      status: "on"
    };

    /**
     * Entry point function called by the method.
     *
     * @param context        The context contains current time, device model and id
     * @param previousState  The device state since the last iteration
     * @param previousProperties  The device properties since the last iteration
     */
    function main(context, previousState) {
      log("Executing lightbulb Switch Off method.");
      state.status = "off";
      updateState(state);
    }
    ```

    Сохраните изменения в **C:\temp\devicemodels\scripts\SwitchOff-method.js**.

Теперь вы создали настроенную версию типа устройства **охладителя** и создали новый тип устройства **лампочка**.

## <a name="test-the-devices"></a>Тестирование устройств

В этом разделе вы тестируете типы устройств, локально созданные в предыдущих разделах.

### <a name="run-the-device-simulation-microservice"></a>Запуск микрослужбы моделирования устройств

Откройте папку **​​device-simulation-dotnet-master**, загруженную с GitHub, в новом экземпляре Visual Studio Code. Чтобы исправить любые нерешенные зависимости, щелкните любую кнопку **Восстановить**.

Откройте файл **.vscode/launch.json** и назначьте строку подключения Центра Интернета вещей для переменной среды **PCS_IOTHUB_CONNSTRING**. В том же файле назначьте строку подключения к базе данных Cosmos DB и добавьте переменную среды **PCS_STORAGEADAPTER_DOCUMENTDB_CONNSTRING**.

Откройте файл **WebService/Properties/launchSettings.json** и назначьте строку подключения Центра Интернета вещей для переменной среды **PCS_IOTHUB_CONNSTRING**.

Откройте файл **WebService/appsettings.ini** и измените настройки следующим образом.

```ini
device_models_folder = C:\temp\devicemodels\

device_models_scripts_folder = C:\temp\devicemodels\scripts\
```

Чтобы запустить микрослужбу локально, нажмите **Отладка > Начать отладку**.

Окно **Терминал** в Visual Studio Code отображает выходные данные из работающей микрослужбы.

Оставьте микрослужбу моделирования устройств запущенной в данном экземпляре Visual Studio Code, пока вы выполняете следующие действия.

### <a name="set-up-a-monitor-for-device-events"></a>Настройка монитора для событий устройства

В этом разделе вы используете Azure CLI, чтобы настроить монитор событий для просмотра телеметрии, отправленной с устройств, подключенных к Центру Интернета вещей.

В следующем сценарии предполагается, что имя вашего Центра Интернета вещей — **device-simulation-test**.

```azurecli-interactive
# Install the IoT extension if it's not already installed
az extension add --name azure-cli-iot-ext

# Monitor telemetry sent to your hub
az iot hub monitor-events --hub-name device-simulation-test
```

Оставьте монитор событий включенным во время тестирования имитируемых устройств.

### <a name="create-a-simulation-with-the-updated-chiller-device-type"></a>Создание имитации с обновленным типом устройства охладителя

В этом разделе вы используете средство Postman для запроса микрослужбы моделирования устройства, чтобы запустить имитацию с использованием обновленного типа устройства охладителя. Postman — это средство, которое позволяет отправлять запросы REST на веб-службу. Необходимые файлы конфигурации Postman находятся в вашей локальной копии репозитория **device-simulation-dotnet**.

Действия для настройки Postman.

1. Откройте Postman на локальном компьютере.

1. Нажмите **Файл> Импорт**. Затем нажмите **Выбор файлов**.

1. Перейдите в папку **device-simulation-dotnet-master/docs/postman**. Выберите **Решение имитации устройств Azure IoT accelerator.postman_collection** и **Решение имитации устройств Azure IoT accelerator.postman_environment** и нажмите **Открыть**.

1. Разверните **Акселератор решений имитации устройств Azure loT** для запросов, которые вы можете отправить.

1. Нажмите **No Environment** (Без среды) и выберите **Акселератор решений имитации устройств Azure IoT**.

Теперь у вас есть коллекция и среда, загруженные в рабочее пространство Postman, которые вы можете использовать для взаимодействия с микрослужбой моделирования устройств.

Настройка и запуск имитации.

1. В коллекции Postman выберите **Создать имитацию модифицированного охладителя** и нажмите **Отправить**. Этот запрос создает четыре экземпляра имитации типа устройства охладителя.

1. Выход монитора событий в окне Azure CLI показывает телеметрию с имитируемых устройств, включая новые значения **internal_temperature**.

Чтобы остановить имитацию, выберите запрос в Postman **Остановить имитацию** и нажмите **Отправка**.

### <a name="create-a-simulation-with-the-lightbulb-device-type"></a>Создание имитации с типом устройства "лампочка"

В этом разделе вы используете средство Postman для запроса микрослужбы моделирования устройства, чтобы запустить имитацию с использованием типа устройства "лампочка". Postman — это средство, которое позволяет отправлять запросы REST на веб-службу.

Настройка и запуск имитации.

1. В коллекции Postman выберите  **Создать имитацию лампочки** и нажмите **Отправить**. Этот запрос создает два экземпляра имитации устройства типа "лампочка".

1. Выход монитора событий в окне Azure CLI показывает телеметрию из имитируемых лампочек.

Чтобы остановить имитацию, выберите запрос в Postman **Остановить имитацию** и нажмите **Отправка**.

## <a name="clean-up-resources"></a>Очистка ресурсов

Вы можете остановить две локально работающих микрослужбы в своих экземплярах Visual Studio Code (**Отладка > Остановить отладку**).

Если вы больше не нуждаетесь в экземплярах Центра Интернета вещей и Cosmos DB, удалите их из подписки Azure, чтобы избежать ненужных расходов.