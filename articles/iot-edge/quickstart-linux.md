---
title: Краткое руководство по использованию Azure IoT Edge и Linux | Документация Майкрософт
description: Из этого краткого руководства вы узнаете, как удаленно развертывать готовый код на устройстве IoT Edge.
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/27/2018
ms.topic: quickstart
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 27e5b7fed227248d9d60c8ede460c9ecc65ca52d
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37096280"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-to-a-linux-x64-device"></a>Краткое руководство. Развертывание первого модуля IoT Edge на устройстве под управлением 64-разрядной ОС Linux

Azure IoT Edge переносит мощь облака на ваши устройства Интернета вещей. Из этого краткого руководства вы узнаете, как использовать облачный интерфейс для удаленного развертывания готового кода на устройстве IoT Edge.

Из этого краткого руководства вы узнаете, как выполнять следующие задачи:

1. Создайте Центр Интернета вещей.
2. Регистрация устройства IoT Edge в Центре Интернета вещей.
3. Установка и запуск среды выполнения IoT Edge на устройстве.
4. Удаленное развертывание модуля на устройстве IoT Edge.

![Архитектура, используемая при работе с этим кратким руководством][2]

В рамках этого краткого руководства компьютер или виртуальная машина Linux используются в качестве устройства IoT Edge. Вы можете развернуть модуль на портале Azure для устройства. Модуль, который вы развернете в рамках этого краткого руководства, представляет собой имитированный датчик, генерирующий данные температуры, влажности и давления. В других руководствах по Azure IoT Edge используются наработки из этой статьи: развернутые модули, которые анализируют смоделированные данные для бизнес-аналитики. 

Если у вас еще нет подписки Azure, перед началом работы [создайте бесплатную учетную запись Azure][lnk-account].


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Для выполнения многих действий, описанных в этом кратком руководстве, используется Azure CLI, а также расширение Интернета вещей Azure для предоставления дополнительных функций. 

Добавьте расширение Интернета вещей Azure в экземпляр Cloud Shell.

   ```azurecli-interactive
   az extension add --name azure-cli-iot-ext
   ```

## <a name="create-an-iot-hub"></a>Создание Центра Интернета вещей

Начните с создания Центра Интернета вещей на портале Azure.
![Создание Центра Интернета вещей][3]

Для целей этого руководства можно использовать бесплатный уровень. Если вы уже использовали бесплатный Центр Интернета вещей и он у вас сохранился, можете использовать его. В подписке может быть только один бесплатный Центр Интернета вещей. 

1. В Azure Cloud Shell создайте группу ресурсов. С помощью следующего примера кода создается группа ресурсов с именем **TestResources** в регионе **Западная часть США**. Поместив в группу все ресурсы, используемые для кратких руководств и инструкций, вы можете управлять ими совместно. 

   ```azurecli-interactive
   az group create --name TestResources --location westus
   ```

1. Создайте в новой группе ресурсов Центр Интернета вещей. При помощи следующего кода создается бесплатный центр **F1** в группе ресурсов **TestResources**. Замените *{hub_name}* уникальным именем для вашего Центра Интернета вещей.

   ```azurecli-interactive
   az iot hub create --resource-group TestResources --name {hub_name} --sku F1 
   ```

## <a name="register-an-iot-edge-device"></a>Регистрация устройства IoT Edge

Зарегистрируйте устройство IoT Edge в только что созданном Центре Интернета вещей.
![Регистрация устройства][4]

Создайте удостоверение для своего имитированного устройства, чтобы оно могло обмениваться данными с Центром Интернета вещей. Так как устройства IoT Edge отличаются от стандартных устройств Интернета вещей поведением и управлением, объявите свое устройство устройством IoT Edge с самого начала. 

1. Чтобы создать устройство с именем **myEdgeDevice** в Центре Интернета вещей, введите следующую команду в Azure Cloud Shell.

   ```azurecli-interactive
   az iot hub device-identity create --device-id myEdgeDevice --hub-name {hub_name} --edge-enabled
   ```

1. Получите строку подключения для устройства, которая связывает физическое устройство с его идентификатором в Центре Интернета вещей. 

   ```azurecli-interactive
   az iot hub device-identity show-connection-string --device-id myEdgeDevice --hub-name {hub_name}
   ```

1. Скопируйте строку подключения и сохраните ее. Это значение потребуется для настройки среды выполнения IoT Edge в следующем разделе. 


## <a name="install-and-start-the-iot-edge-runtime"></a>Установка и запуск среды выполнения IoT Edge

Установите и запустите среду выполнения Azure IoT Edge на устройстве. 
![Регистрация устройства][5]

Среда выполнения IoT Edge развертывается на всех устройствах IoT Edge. Она состоит из трех компонентов. **Управляющая программа безопасности IoT Edge** запускается при каждой загрузке устройства Edge и перезагружает устройство, запустив агент IoT Edge. **Агент IoT Edge** упрощает развертывание и мониторинг модулей на устройстве IoT Edge, включая центр IoT Edge. **Центр IoT Edge** управляет взаимодействием между модулями на устройстве IoT Edge, а также между устройством и Центром Интернета вещей. 

### <a name="register-your-device-to-use-the-software-repository"></a>Регистрация устройства для использования репозитория программного обеспечения

Пакеты, которые необходимо запускать в среде выполнения IoT Edge, управляются в репозитории программного обеспечения. Настройте устройство IoT Edge для доступа к этому репозиторию. 

Все действия, описанные в этом разделе, предназначены для устройств под управлением **Ubuntu 16.04**. Сведения о том, как получить доступ к репозиторию программного обеспечения в других версиях Linux, см. в статьях по [установке среды выполнения Azure IoT Edge в Linux (x64)](how-to-install-iot-edge-linux.md) или [установке среды выполнения Azure IoT Edge в Linux (ARM32v7/armhf)](how-to-install-iot-edge-linux-arm.md).

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

Среда выполнения IoT Edge — это набор контейнеров и логическое приложение, которое развертывается на устройстве IoT Edge и упаковано в контейнеры. Подготовьте устройство для этих компонентов, установив среду выполнения контейнера.

Обновите **apt-get**.

   ```bash
   sudo apt-get update
   ```

Установите Moby (среду выполнения контейнера) с командами CLI. 

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

3. Добавьте строку подключения устройства IoT Edge, скопированную при регистрации устройства. Замените значение переменной **device_connection_string** значением, скопированным ранее в этом кратком руководстве.

4. Перезапустите управляющую программу безопасности Edge:

   ```bash
   sudo systemctl restart iotedge
   ```

5. Убедитесь, что управляющая программа безопасности Edge выполняется как системная служба:

   ```bash
   sudo systemctl status iotedge
   ```

   ![Проверка того, выполняется ли управляющая программа Edge как системная служба](./media/quickstart-linux/iotedged-running.png)

   Можно также просмотреть журналы управляющей программы безопасности Edge, выполнив следующую команду:

   ```bash
   journalctl -u iotedge
   ```

6. Просмотрите модули, запущенные на устройстве: 

   ```bash
   sudo iotedge list
   ```

   Когда вы выйдете из системы и войдете в нее, *sudo* больше не потребуется для приведенной выше команды.

   ![Просмотр данных об одном модуле на устройстве](./media/quickstart-linux/iotedge-list-1.png)

## <a name="deploy-a-module"></a>Развертывание модуля

Управляя устройством Azure IoT Edge из облака, разверните модуль, который будет передавать данные телеметрии в Центр Интернета вещей.
![Регистрация устройства][6]

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>Просмотр сформированных данных

В этом руководстве мы создали новое устройство IoT Edge и установили на нем среду выполнения IoT Edge. Затем с помощью портала Azure мы отправили на устройство и запустили модуль IoT Edge, не изменяя на устройстве никаких настроек. В этом примере отправленный модуль создает данные среды, которые можно использовать для работы с руководствами. 

Еще раз откройте командную строку на компьютере, на котором работает имитированное устройство. Убедитесь, что развернутый из облака модуль работает на вашем устройстве IoT Edge.

   ```bash
   sudo iotedge list
   ```
   Когда вы выйдете из системы и войдете в нее, *sudo* больше не потребуется для приведенной выше команды.

   ![Просмотр трех модулей на устройстве](./media/quickstart-linux/iotedge-list-2.png)

Убедитесь, что сообщения отправляются из модуля tempSensor.

   ```bash
   sudo iotedge logs tempSensor -f 
   ```
Когда вы выйдете из системы и войдете в нее, *sudo* больше не потребуется для приведенной выше команды.

![Просмотр получаемых с модуля данных](./media/quickstart-linux/iotedge-logs.png)

Модуль датчика температуры может ожидать подключения к центру Edge, если последней строкой в журнале оказывается `Using transport Mqtt_Tcp_Only`. Попробуйте завершить модуль, и пусть агент Edge перезапустит его. Для завершения можно воспользоваться командой `sudo docker stop tempSensor`.

Данные телеметрии, которые передает устройство, можно также просматривать, используя [средство "Обозреватель Центра Интернета вещей"][lnk-iothub-explorer] или [расширение Azure IoT Toolkit для Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit). 


## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы хотите продолжить ознакомление с руководствами по IoT Edge, используйте устройство, зарегистрированное и настроенное в рамках этого краткого руководства. Чтобы удалить файлы установки с устройства, используйте приведенные ниже команды.  

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

Если созданные ресурсы Azure вам больше не нужны, воспользуйтесь следующей командой, чтобы удалить созданную группу ресурсов и все ресурсы, связанные с ней:

   ```azurecli-interactive
   az group delete --name TestResources
   ```

## <a name="next-steps"></a>Дополнительная информация

Изучение этого этим краткого руководства обязательно для дальнейшей работы со всеми остальными руководствами по IoT Edge. Вы можете перейти к любому из оставшихся руководств, чтобы узнать, как Azure IoT Edge может помочь вам превратить данные в бизнес-аналитику на пограничном устройстве.

> [!div class="nextstepaction"]
> [Фильтрация данных датчика с помощью Функций Azure](tutorial-deploy-function.md)



<!-- Images -->
[0]: ./media/quickstart-linux/cloud-shell.png
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
[lnk-account]: https://azure.microsoft.com/free
[lnk-portal]: https://portal.azure.com
[lnk-delete]: https://docs.microsoft.com/cli/azure/iot/hub?view=azure-cli-latest#az_iot_hub_delete

<!-- Anchor links -->
[anchor-register]: #register-an-iot-edge-device
