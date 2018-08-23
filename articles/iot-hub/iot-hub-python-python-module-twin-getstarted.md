---
title: Приступая к работе с удостоверением и двойником модуля Центра Интернета вещей Azure (Python) | Документация Майкрософт
description: Узнайте, как создать удостоверение модуля и обновить двойник модуля с помощью пакетов SDK Центра Интернета вещей для Python.
author: chrissie926
manager: ''
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 04/26/2018
ms.author: menchi
ms.openlocfilehash: 5a4d9debfcc48279bbb56df076a77a5c8b44e231
ms.sourcegitcommit: 7b845d3b9a5a4487d5df89906cc5d5bbdb0507c8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/14/2018
ms.locfileid: "42145604"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-using-python-back-end-and-python-device"></a>Приступая к работе с удостоверением и двойником модуля Центра Интернета вещей с использованием серверной части и устройства Python

> [!NOTE]
> [Удостоверение и двойник модуля](iot-hub-devguide-module-twins.md) аналогичны удостоверению и двойнику устройства Центра Интернета вещей Azure, однако они предоставляют более высокую степень детализации. В то время как удостоверение и двойник устройства Центра Интернета вещей Azure позволяют серверному приложению настраивать устройство и отображать состояние устройства, удостоверение и двойник модуля предоставляют эти возможности для отдельных компонентов устройства. Благодаря этому можно изолировать конфигурацию и условия для каждого компонента на совместимых устройствах с несколькими компонентами, например устройствах на основе операционной системы или устройствах со встроенным ПО.

По завершении работы с этим руководством у вас будет два приложения Python.

* **CreateIdentities** — создает удостоверение устройства и модуля, а также соответствующий ключ безопасности для подключения к клиентам устройства и модуля.
* **UpdateModuleTwinReportedProperties** — отправляет обновленные сообщаемые свойства двойника модуля в Центр Интернета вещей.

> [!NOTE]
> Статья о [пакетах SDK для Центра Интернета вещей Azure][lnk-hub-sdks] содержит сведения о различных пакетах SDK, которые можно использовать для создания приложений, которые будут работать на устройствах и в серверной части решения.

Для работы с этим учебником требуется:

* Активная учетная запись Azure. Если ее нет, можно создать [бесплатную учетную запись][lnk-free-trial] всего за несколько минут.
* Центр Интернета вещей.
* Установка последней версии [пакета SDK для Python](https://github.com/Azure/azure-iot-sdk-python).


Теперь у вас есть Центр Интернета вещей, а также имя узла и строка подключения Центра Интернета вещей, необходимые для завершения работы с этим руководством.

## <a name="create-a-device-identity-and-a-module-identity-in-iot-hub"></a>Создание удостоверения устройства и удостоверения модуля в Центре Интернета вещей

В этом разделе объясняется, как создать приложение Python, создающее удостоверение устройства и удостоверение модуля в реестре удостоверений Центра Интернета вещей. Устройства и модули не могут подключаться к Центру Интернета вещей, если в реестре удостоверений для них нет соответствующей записи. Дополнительные сведения см. в разделе, посвященном реестру удостоверений, в [руководстве разработчика для Центра Интернета вещей Azure][lnk-devguide-identity]. Запущенное консольное приложение создает уникальные идентификаторы и ключи одновременно для устройства и для модуля. Устройство и модуль применяют эти значения для идентификации при отправке сообщений с устройства в облако в Центр Интернета вещей. В идентификаторах учитывается регистр символов.

Добавьте в файл Python следующий код:

```Python
import sys
import iothub_service_client
from iothub_service_client import IoTHubRegistryManager, IoTHubRegistryManagerAuthMethod, IoTHubError

CONNECTION_STRING = "YourConnString"
DEVICE_ID = "myFirstDevice"
MODULE_ID = "myFirstModule"

try:
    # RegistryManager
    iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

    # CreateDevice
    primary_key = ""
    secondary_key = ""
    auth_method = IoTHubRegistryManagerAuthMethod.SHARED_PRIVATE_KEY
    new_device = iothub_registry_manager.create_device(DEVICE_ID, primary_key, secondary_key, auth_method)
    print("new_device <" + DEVICE_ID + "> has primary key = " + new_device.primaryKey)

    # CreateModule
    new_module = iothub_registry_manager.create_module(DEVICE_ID, primary_key, secondary_key, MODULE_ID, auth_method)
    print("device/new_module <" + DEVICE_ID + "/" + MODULE_ID + "> has primary key = " + new_module.primaryKey)

except IoTHubError as iothub_error:
    print ( "Unexpected error {0}".format(iothub_error) )
except KeyboardInterrupt:
    print ( "IoTHubRegistryManager sample stopped" )
```

Это приложение создает удостоверение устройства с идентификатором **myFirstDevice** и удостоверение модуля с идентификатором **myFirstModule** на устройстве **myFirstDevice**. (Если такой идентификатор модуля уже существует в реестре удостоверений, код просто извлекает сведения о существующем модуле.) Затем приложение отображает первичный ключ для данного удостоверения. Этот ключ будет использоваться в приложении виртуального модуля для подключения к Центру Интернета вещей.

> [!NOTE]
> В реестре удостоверений в Центре Интернета вещей хранятся только удостоверения устройств и модулей, необходимые для защиты доступа к Центру Интернета вещей. Реестр удостоверений хранит идентификаторы устройств и ключи, используемые в качестве учетных данных безопасности. Реестр удостоверений также хранит флаги включения и отключения для каждого устройства. Эти флаги можно использовать для отключения доступа для этого устройства. Если в приложении необходимо хранить другие метаданные для конкретного устройства, следует использовать хранилище конкретного приложения. У вас нет возможности включать и отключать удостоверения модулей. Дополнительные сведения см. в [руководстве разработчика для Центра Интернета вещей][lnk-devguide-identity].

## <a name="update-the-module-twin-using-python-device-sdk"></a>Обновление двойника модуля с помощью пакета SDK для устройства Python

В этом разделе объясняется, как создать приложение Python на имитированном устройстве, которое обновляет сообщаемые свойства двойника модуля.

1. **Получите строку подключения модуля**. Войдите на [портал Azure][lnk-portal]. Перейдите к Центру Интернета вещей и щелкните "Устройства IoT". Найдите устройство myFirstDevice, откройте его, и вы увидите, что модуль myFirstModule успешно создан. Скопируйте строку подключения модуля. Она понадобится на следующем шаге.

    ![Сведения о модуле на портале Azure][15]

2. **Создайте приложение UpdateModuleTwinReportedProperties**. В начало файла **Program.cs** добавьте следующие инструкции `using`:

    ```Python
    import sys
    import iothub_service_client
    from iothub_service_client import IoTHubRegistryManager, IoTHubRegistryManagerAuthMethod, IoTHubDeviceTwin, IoTHubError

    CONNECTION_STRING = "FILL IN CONNECTION STRING"
    DEVICE_ID = "MyFirstDevice"
    MODULE_ID = "MyFirstModule"

    UPDATE_JSON = "{\"properties\":{\"desired\":{\"telemetryInterval\":122}}}"

    try:
        iothub_twin = IoTHubDeviceTwin(CONNECTION_STRING)

        twin_info = iothub_twin.get_twin(DEVICE_ID, MODULE_ID)
        print ( "" )
        print ( "Twin before update    :" )
        print ( "{0}".format(twin_info) )

        twin_info_updated = iothub_twin.update_twin(DEVICE_ID, MODULE_ID, UPDATE_JSON)
        print ( "" )
        print ( "Twin after update     :" )
        print ( "{0}".format(twin_info_updated) )

    except IoTHubError as iothub_error:
        print ( "Unexpected error {0}".format(iothub_error) )
    except KeyboardInterrupt:
        print ( "IoTHubRegistryManager sample stopped" )
    ```

В этом примере кода показано, как извлечь двойник модуля и обновить сообщаемые свойства с помощью протокола AMQP. 

## <a name="get-updates-on-the-device-side"></a>Получение обновлений на стороне устройства
Помимо приведенного выше кода, вы можете добавить следующий блок кода, чтобы получать сообщения об обновлении двойника на устройстве.

```Python
import random
import time
import sys
import iothub_client
from iothub_client import IoTHubModuleClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult

PROTOCOL = IoTHubTransportProvider.AMQP
CONNECTION_STRING = ""

def module_twin_callback(update_state, payload, user_context):
    print ("")
    print ("Twin callback called with:")
    print ("updateStatus: %s" % update_state )
    print ("context: %s" % user_context )
    print ("payload: %s" % payload )

try:
    module_client = IoTHubModuleClient(CONNECTION_STRING, PROTOCOL)
    module_client.set_module_twin_callback(module_twin_callback, 1234)

    print ("Waiting for incoming twin messages.  Hit Control-C to exit.")
    while True:

        time.sleep(1000000)

except IoTHubError as iothub_error:
    print ( "Unexpected error {0}".format(iothub_error) )
except KeyboardInterrupt:
    print ( "module client sample stopped" )
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