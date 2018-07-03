---
title: Имитация Azure IoT Edge в Linux | Документация Майкрософт
description: Из этого краткого руководства вы узнаете, как удаленно развертывать готовый код на устройстве IoT Edge.
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/27/2018
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 6b63c10a8c092d6568f8caf9842f007a5dc9c027
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37049168"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-to-a-linux-x64-device"></a>Краткое руководство. Развертывание первого модуля IoT Edge на устройстве под управлением 64-разрядной ОС Linux

Azure IoT Edge позволяет выполнять анализ и обработку данных на устройствах вместо того, чтобы принудительно отправлять все данные в облако. В руководствах по IoT Edge демонстрируется развертывание различных типов модулей, однако сначала вам потребуется устройство для тестирования. 

Из этого краткого руководства вы узнаете, как выполнять следующие задачи:

1. Создайте Центр Интернета вещей.
2. Регистрация устройства IoT Edge в Центре Интернета вещей.
3. Запуск среды выполнения IoT Edge.
4. Удаленное развертывание модуля на устройстве IoT Edge.

![Архитектура, используемая в руководстве][2]

В рамках этого краткого руководства компьютер или виртуальная машина Linux используются в качестве устройства IoT Edge. Вы можете развернуть модуль на портале Azure для устройства. Модуль, который вы развернете в рамках этого краткого руководства, представляет собой имитированный датчик, генерирующий данные температуры, влажности и давления. В других руководствах по Azure IoT Edge используются наработки из этой статьи: развернутые модули, которые анализируют смоделированные данные для бизнес-аналитики. 

Если у вас еще нет подписки Azure, перед началом работы создайте [бесплатную учетную запись][lnk-account].

## <a name="create-an-iot-hub"></a>Создание Центра Интернета вещей

Начните с создания Центра Интернета вещей на портале Azure.
![Создание Центра Интернета вещей][3]

[!INCLUDE [iot-hub-create-hub](../../includes/iot-hub-create-hub.md)]

## <a name="register-an-iot-edge-device"></a>Регистрация устройства IoT Edge

Зарегистрируйте устройство IoT Edge в только что созданном Центре Интернета вещей.
![Регистрация устройства][4]

[!INCLUDE [iot-edge-register-device](../../includes/iot-edge-register-device.md)]


## <a name="install-and-start-the-iot-edge-runtime"></a>Установка и запуск среды выполнения IoT Edge

Установите и запустите среду выполнения Azure IoT Edge на устройстве. 
![Регистрация устройства][5]

Среда выполнения IoT Edge развертывается на всех устройствах IoT Edge. Она состоит из трех компонентов. **Управляющая программа безопасности IoT Edge** запускается при каждой загрузке устройства Edge и перезагружает устройство, запустив агент IoT Edge. **Агент IoT Edge** упрощает развертывание и мониторинг модулей на устройстве IoT Edge, включая центр IoT Edge. **Центр IoT Edge** управляет взаимодействием между модулями на устройстве IoT Edge, а также между устройством и Центром Интернета вещей. 

### <a name="register-your-device-to-use-the-software-repository"></a>Регистрация устройства для использования репозитория программного обеспечения

Пакеты, которые необходимо запускать в среде выполнения IoT Edge, управляются в репозитории программного обеспечения. Настройте устройство IoT Edge для доступа к этому репозиторию. 

Все действия, описанные в этом разделе, предназначены для устройств под управлением **Ubuntu 16.04**. Сведения о том, как получить доступ к репозиторию программного обеспечения на других версиях Linux, см. в статьях об [установке среды выполнения Azure IoT Edge в Linux (x64)](how-to-install-iot-edge-linux.md) или [установке среды выполнения Azure IoT Edge в Linux (ARM32v7/armhf)](how-to-install-iot-edge-linux-arm.md).

1. На компьютере, используемом в качестве устройства IoT Edge, установите конфигурацию репозитория.

   ```bash
   curl https://packages.microsoft.com/config/ubuntu/16.04/prod.list > ./microsoft-prod.list
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

2. Установите открытый ключ для доступа к репозиторию.

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

### <a name="install-a-container-runtime"></a>Установка среды выполнения контейнера

Среда выполнения IoT Edge — это набор контейнеров и логика, развертываемая на устройство IoT Edge и упакованная в контейнер. Подготовьте устройство для этих компонентов, установив среду выполнения контейнера.

Обновите **apt-get**.

   ```bash
   sudo apt-get update
   ```

Установите Moby, среду выполнения контейнера, с командами CLI. 

   ```bash
   sudo apt-get install moby-engine
   sudo apt-get install moby-cli   
   ```

### <a name="install-and-configure-the-iot-edge-security-daemon"></a>Установка и настройка управляющей программы безопасности IoT Edge

Управляющая программа безопасности устанавливается как системная служба, поэтому среда выполнения IoT Edge будет запускаться каждый раз при загрузке устройства. Пакет установки также включает версию **hsmlib**, позволяющую управляющей программе безопасности взаимодействовать с аппаратным модулем безопасности устройства. 

1. Загрузите и установите управляющую программу безопасности IoT Edge. 

   ```bash
   sudo apt-get update
   sudo apt-get install iotedge
   ```

2. Откройте файл конфигурации IoT Edge. Это защищенный файл, поэтому для доступа к нему нужно использовать более высокий уровень привилегий.
   
   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

3. Добавьте строку подключения устройства IoT Edge, скопированную при регистрации устройства. Замените значение переменной **device_connection_string**, скопированное ранее в кратком руководстве.

4. Перезапустите управляющую программу безопасности Edge:

   ```bash
   sudo systemctl restart iotedge
   ```

5. Проверьте, чтобы управляющая программа безопасности Edge выполнялась как системная служба:

   ```bash
   sudo systemctl status iotedge
   ```

   ![Просмотр выполнения управляющей программы Edge как системной службы](./media/quickstart-linux/iotedged-running.png)

   Просмотреть журналы управляющей программы безопасности Edge можно также, выполнив следующую команду:

   ```bash
   journalctl -u iotedge
   ```

6. Просмотрите модули, запущенные на устройстве: 

   ```bash
   iotedge list
   ```

   ![Просмотр данных об одном модуле на устройстве](./media/quickstart-linux/iotedge-list-1.png)

## <a name="deploy-a-module"></a>Развертывание модуля

Управляя устройством Azure IoT Edge из облака, разверните модуль, который будет передавать данные телеметрии в Центр Интернета вещей.
![Регистрация устройства][6]

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]


## <a name="view-generated-data"></a>Просмотр сформированных данных

В этом руководстве мы создали новое устройство IoT Edge и установили на нем среду выполнения IoT Edge. Затем с помощью портала Azure мы отправили на устройство и запустили модуль IoT Edge, не изменяя на устройстве никаких настроек. В этом примере отправленный модуль создает данные среды, которые можно использовать для работы с руководствами. 

Еще раз откройте командную строку на компьютере, на котором работает имитированное устройство. Убедитесь, что развернутый из облака модуль работает на вашем устройстве IoT Edge.

   ```bash
   iotedge list
   ```

   ![Просмотр трех модулей на устройстве](./media/quickstart-linux/iotedge-list-2.png)

Убедитесь, что сообщения отправляются из модуля tempSensor.

   ```bash
   iotedge logs tempSensor -f 
   ```

![Просмотр получаемых с модуля данных](./media/quickstart-linux/iotedge-logs.png)

Модуль датчика температуры может ожидать подключения к центру Edge, если последней строкой в журнале оказывается `Using transport Mqtt_Tcp_Only`. Попробуйте завершить модуль, и пусть агент Edge перезапустит его. Для завершения можно воспользоваться командой `sudo docker stop tempSensor`.

Данные телеметрии, которые передает устройство, можно также просматривать, используя [средство "Обозреватель Центра Интернета вещей"][lnk-iothub-explorer] или [расширение Azure IoT Toolkit для Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit). 

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы хотите продолжить пользоваться руководствами IoT Edge, используйте устройство, зарегистрированное и настроенное в этом кратком руководстве. Если вы хотите удалить установленные компоненты с устройства, используйте следующие команды.  

Удалите среду выполнения IoT Edge.

   ```bash
   sudo apt-get remove --purge iotedge
   ```

Удалите контейнеры, созданные на своем устройстве. 

   ```bash
   sudo docker rm -f $(sudo docker ps -aq)
   ```

Удалите среду выполнения контейнера.

   ```bash
   sudo apt-get remove --purge moby
   ```

Если созданный в этом руководстве Центр Интернета вещей или устройство IoT Edge вам больше не нужны, их можно удалить на портале Azure. Перейдите на страницу сведений о Центре Интернета вещей и щелкните **Удалить**. 

## <a name="next-steps"></a>Дополнительная информация

При работе с этим кратким руководством вы создали устройство IoT Edge и с помощью облачного интерфейса Azure IoT Edge развернули код на устройстве. В итоге мы получили имитированное устройство, генерирующее необработанные данные о своей среде. 

Результаты, полученные в ходе работы с этим руководством, будут использоваться во всех остальных руководствах по IoT Edge. Вы можете перейти к любому из руководств, чтобы узнать, как Azure IoT Edge может помочь вам превратить данные в бизнес-аналитику на пограничном устройстве.

> [!div class="nextstepaction"]
> [Фильтрация данных датчика с помощью Функций Azure](tutorial-deploy-function.md)


<!-- Images -->
[1]: ./media/quickstart-linux/view-module.png
[2]: ./media/quickstart-linux/install-edge-full.png
[3]: ./media/quickstart-linux/create-iot-hub.png
[4]: ./media/quickstart-linux/register-device.png
[5]: ./media/quickstart-linux/start-runtime.png
[6]: ./media/quickstart-linux/deploy-module.png
[7]: ./media/quickstart-linux/iotedged-running.png
[8]: ./media/tutorial-simulate-device-linux/running-modules.png
[9]: ./media/tutorial-simulate-device-linux/sensor-data.png

<!-- Links -->
[lnk-docker-ubuntu]: https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/ 
[lnk-iothub-explorer]: https://github.com/azure/iothub-explorer
