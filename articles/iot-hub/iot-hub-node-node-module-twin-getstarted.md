---
title: Приступая к работе с удостоверением и двойником модуля в Центре Интернета вещей Azure (Node.js) | Документация Майкрософт
description: Сведения о создании удостоверения модуля и обновлении двойника модуля с помощью пакетов SDK для Центра Интернета вещей для Node.js.
author: chrissie926
manager: ''
ms.service: iot-hub
services: iot-hub
ms.devlang: node
ms.topic: conceptual
ms.date: 04/26/2018
ms.author: menchi
ms.openlocfilehash: fa77e117b8045be4ef0566e388c4e8df08c95fe2
ms.sourcegitcommit: 7b845d3b9a5a4487d5df89906cc5d5bbdb0507c8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/14/2018
ms.locfileid: "42146942"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-using-nodejs-back-end-and-nodejs-device"></a>Приступая к работе с удостоверением и двойником модуля в Центре Интернета вещей с использованием серверной части и устройства Node.js

> [!NOTE]
> [Удостоверение и двойник модуля](iot-hub-devguide-module-twins.md) аналогичны удостоверению и двойнику устройства Центра Интернета вещей Azure, однако они предоставляют более высокую степень детализации. В то время как удостоверение и двойник устройства Центра Интернета вещей Azure позволяют серверному приложению настраивать устройство и отображать состояние устройства, удостоверение и двойник модуля предоставляют эти возможности для отдельных компонентов устройства. Благодаря этому можно изолировать конфигурацию и условия для каждого компонента на совместимых устройствах с несколькими компонентами, например устройствах на основе операционной системы или устройствах со встроенным ПО.

По завершении работы с этим руководством у вас будет два приложения Node.js:

* **CreateIdentities** — создает удостоверение устройства и модуля, а также соответствующий ключ безопасности для подключения к клиентам устройства и модуля.
* **UpdateModuleTwinReportedProperties** — отправляет обновленные сообщаемые свойства двойника модуля в Центр Интернета вещей.

> [!NOTE]
> Статья о [пакетах SDK для Центра Интернета вещей Azure][lnk-hub-sdks] содержит сведения о различных пакетах SDK, которые можно использовать для создания приложений, которые будут работать на устройствах и в серверной части решения.

Для работы с этим учебником требуется:

* Активная учетная запись Azure. Если ее нет, можно создать [бесплатную учетную запись][lnk-free-trial] всего за несколько минут.
* Центр Интернета вещей.
* Установка последней версии [пакета SDK для Node.js](https://github.com/Azure/azure-iot-sdk-node).


Теперь у вас есть Центр Интернета вещей, а также имя узла и строка подключения Центра Интернета вещей, необходимые для завершения работы с этим руководством.

## <a name="create-a-device-identity-and-a-module-identity-in-iot-hub"></a>Создание удостоверения устройства и удостоверения модуля в Центре Интернета вещей

В этом разделе объясняется, как создать приложение Node.js, создающее удостоверение устройства и удостоверение модуля в реестре удостоверений Центра Интернета вещей. Устройства и модули не могут подключаться к Центру Интернета вещей, если в реестре удостоверений для них нет соответствующей записи. Дополнительные сведения см. в разделе, посвященном реестру удостоверений, в [руководстве разработчика для Центра Интернета вещей Azure][lnk-devguide-identity]. Запущенное консольное приложение создает уникальные идентификаторы и ключи одновременно для устройства и для модуля. Устройство и модуль применяют эти значения для идентификации при отправке сообщений с устройства в облако в Центр Интернета вещей. В идентификаторах учитывается регистр символов.

1.  Создайте каталог для размещения файлов с кодом.
2. Выполните в этом каталоге команду **npm init -y**, чтобы создать пустой файл package.json со значениями по умолчанию. Это файл проекта, в котором будет содержаться код.
3. Выполните **npm install -S azure-iothub@modules-preview**, чтобы установить пакет SDK службы в подкаталоге **node_modules**. 

    > [!NOTE] 
    > Слово module в имени подкаталога node_modules обозначает "библиотеку узла". Он не имеет никакого отношения к модулям Центра Интернета вещей.

4. Создайте в этом каталоге приведенный ниже JS-файл. Присвойте ему имя **add.js**. Скопируйте имя Центра Интернета вещей и строку подключения к нему, затем вставьте их в соответствующие места.

    ```javascript
    var Registry = require('azure-iothub').Registry;
    var uuid = require('uuid');
    // Copy/paste your connection string and hub name here
    var serviceConnectionString = '<hub connection string from portal>';
    var hubName = '<hub name>.azure-devices.net';
    // Create an instance of the IoTHub registry
    var registry = Registry.fromConnectionString(serviceConnectionString);
    // Insert your device ID and moduleId here.
    var deviceId = 'myFirstDevice';
    var moduleId = 'myFirstModule';
    // Create your device as a SAS authentication device
    var primaryKey = new Buffer(uuid.v4()).toString('base64');
    var secondaryKey = new Buffer(uuid.v4()).toString('base64');
    var deviceDescription = {
      deviceId: deviceId,
      status: 'enabled',
      authentication: {
        type: 'sas',
        symmetricKey: {
          primaryKey: primaryKey,
          secondaryKey: secondaryKey
        }
      }
    };

    // First, create a device identity
    registry.create(deviceDescription, function(err) {
      if (err) {
        console.log('Error creating device identity: ' + err);
        process.exit(1);
      }
      console.log('device connection string = "HostName=' + hubName + ';DeviceId=' + deviceId + ';SharedAccessKey=' + primaryKey + '"');

    // Then add a module to that device
      registry.addModule({ deviceId: deviceId, moduleId: moduleId }, function(err) {
        if (err) {
          console.log('Error creating module identity: ' + err);
          process.exit(1);
        }

    // Finally, retrieve the module details from the hub so we can construct the connection string
        registry.getModule(deviceId, moduleId, function(err, foundModule) {
          if (err) {
            console.log('Error getting module back from hub: ' + err);
            process.exit(1);
          }
          console.log('module connection string = "HostName=' + hubName + ';DeviceId=' + foundModule.deviceId + ';ModuleId='+foundModule.moduleId+';SharedAccessKey=' + foundModule.authentication.symmetricKey.primaryKey + '"');
          process.exit(0);
        });
      });
    });

    ```

Это приложение создает удостоверение устройства с идентификатором **myFirstDevice** и удостоверение модуля с идентификатором **myFirstModule** на устройстве **myFirstDevice**. (Если такой идентификатор модуля уже существует в реестре удостоверений, код просто извлекает сведения о существующем модуле.) Затем приложение отображает первичный ключ для данного удостоверения. Этот ключ будет использоваться в приложении виртуального модуля для подключения к Центру Интернета вещей.

5. Выполните его для узла add.js. Так вы получите строку подключения для удостоверения устройства и еще одну для удостоверения модуля.

    > [!NOTE]
    > В реестре удостоверений в Центре Интернета вещей хранятся только удостоверения устройств и модулей, необходимые для защиты доступа к Центру Интернета вещей. Реестр удостоверений хранит идентификаторы устройств и ключи, используемые в качестве учетных данных безопасности. Реестр удостоверений также хранит флаги включения и отключения для каждого устройства. Эти флаги можно использовать для отключения доступа для этого устройства. Если в приложении необходимо хранить другие метаданные для конкретного устройства, следует использовать хранилище конкретного приложения. У вас нет возможности включать и отключать удостоверения модулей. Дополнительные сведения см. в [руководстве разработчика для Центра Интернета вещей][lnk-devguide-identity].

## <a name="update-the-module-twin-using-nodejs-device-sdk"></a>Обновление двойника модуля с помощью пакета SDK для устройства на Node.js

В этом разделе объясняется, как на имитированном устройстве создать приложение Node.js, которое обновляет сообщаемые свойства двойника модуля.

1. **Получите строку подключения модуля**. Войдите на [портал Azure][lnk-portal]. Перейдите к Центру Интернета вещей и щелкните "Устройства IoT". Найдите устройство myFirstDevice, откройте его, и вы увидите, что модуль myFirstModule успешно создан. Скопируйте строку подключения модуля. Она понадобится на следующем шаге.

    ![Сведения о модуле на портале Azure][15]

2. Как и на предыдущем шаге, создайте каталог для файлов с кодом устройства, с помощью NPM инициализируйте его и установите пакет SDK для устройства (**npm install -S azure-iot-device-amqp@modules-preview**).

    > [!NOTE]
    > Команда npm install может показаться довольно медленной. Не спешите, ведь ей нужно извлечь большой объем кода из репозитория пакетов.

    > [!NOTE] 
    > Если вы увидите ошибку "npm ERR! registry error parsing json" (Ошибка реестра при синтаксическом анализе JSON), можете проигнорировать ее. Если вы увидите ошибку "npm ERR! registry error parsing json" (Ошибка реестра при синтаксическом анализе JSON), можете проигнорировать ее.

3. Создайте файл с именем twin.js. Скопируйте и вставьте в него строку удостоверения устройства.

    ```javascript
    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-amqp').Amqp;
    // Copy/paste your module connection string here.
    var connectionString = '<insert module connection string here>';
    // Create a client using the Amqp protocol.
    var client = Client.fromConnectionString(connectionString, Protocol);
    client.on('error', function (err) {
      console.error(err.message);
    });
    // connect to the hub
    client.open(function(err) {
      if (err) {
        console.error('error connecting to hub: ' + err);
        process.exit(1);
      }
      console.log('client opened');
    // Create device Twin
      client.getTwin(function(err, twin) {
        if (err) {
          console.error('error getting twin: ' + err);
          process.exit(1);
        }
    // Output the current properties
        console.log('twin contents:');
        console.log(twin.properties);
    // Add a handler for desired property changes
        twin.on('properties.desired', function(delta) {
            console.log('new desired properties received:');
            console.log(JSON.stringify(delta));
        });
    // create a patch to send to the hub
        var patch = {
          updateTime: new Date().toString(),
          firmwareVersion:'1.2.1',
          weather:{
            temperature: 72,
            humidity: 17
          }
        };
    // send the patch
        twin.properties.reported.update(patch, function(err) {
          if (err) throw err;
          console.log('twin state reported');
        });
      });
    });
    ```

2. Теперь запустите этот файл командой **node twin.js**.

    ```
    F:\temp\module_twin>node twin.js
    client opened
    twin contents:
    { reported: { update: [Function: update], '$version': 1 },
      desired: { '$version': 1 } }
    new desired properties received:
    {"$version":1}
    twin state reported
    ```

## <a name="next-steps"></a>Дополнительная информация

Чтобы продолжить знакомство с Центром Интернета вещей и изучить другие сценарии Интернета вещей, см. следующие ресурсы:

* [How to get started with device management (Node)][lnk-device-management] (Начало работы с управлением устройствами (Node))
* [Explore Azure IoT Edge architecture on Linux][lnk-iot-edge] (Приступая к работе с архитектурой Azure IoT Edge в Linux)


<!-- Images. -->
[15]: ./media\iot-hub-csharp-csharp-module-twin-getstarted/module-detail.JPG
<!-- Links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-iot-edge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-devguide-identity]: iot-hub-devguide-identity-registry.md
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/