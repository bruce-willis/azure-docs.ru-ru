---
title: Имитация Azure IoT Edge в Windows | Документация Майкрософт
description: Установка среды выполнения Azure IoT Edge на имитированном устройстве в ОС Windows и развертывание первого модуля
author: kgremban
manager: timlt
ms.author: kgremban
ms.reviewer: elioda
ms.date: 06/08/2018
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 5863a8edbb20b2b0c231834259f1bb7b0423a8f6
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37034022"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-from-the-azure-portal-to-a-windows-device---preview"></a>Краткое руководство по развертыванию первого простого модуля IoT Edge на устройстве с Windows при помощи портала Azure (предварительная версия)

Azure IoT Edge позволяет выполнять анализ и обработку данных на устройствах вместо того, чтобы принудительно отправлять все данные в облако. В руководствах по IoT Edge демонстрируется развертывание различных типов модулей, однако сначала вам потребуется устройство для тестирования. 

Из этого краткого руководства вы узнаете, как выполнять следующие задачи:

1. Создание центра Интернета вещей
2. Регистрация устройства IoT Edge
3. Запуск среды выполнения IoT Edge
4. Развертывание модуля

![Архитектура, используемая в руководстве][2]

Модуль, который вы развернете в рамках этого краткого руководства, представляет собой имитированный датчик, генерирующий данные температуры, влажности и давления. В других руководствах по Azure IoT Edge используются наработки из этой статьи: развернутые модули, которые анализируют смоделированные данные для бизнес-аналитики. 

>[!NOTE]
>Среда выполнения IoT Edge в ОС Windows доступна в режиме [общедоступной предварительной версии](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Если у вас еще нет подписки Azure, перед началом работы [создайте бесплатную учетную запись Azure][lnk-account].

## <a name="prerequisites"></a>предварительным требованиям

В этом кратком руководстве предполагается, что вы используете компьютер или виртуальную машину под управлением Windows для имитации устройства Интернета вещей. Если вы работаете на виртуальной машине с Windows, включите [вложенную виртуализацию][lnk-nested] и выделите не менее 2 ГБ памяти. 

На компьютере, используемом для устройства IoT Edge, подготовьте следующие необходимые компоненты:

1. Убедитесь, что используется поддерживаемая версия Windows:
   * Windows 10 или более поздней версии;
   * Windows Server 2016 или поздней версии.
2. Установите среду [Docker для Windows][lnk-docker] и убедитесь, что она выполняется.
3. Настройте Docker для использования [контейнеров Linux](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).

## <a name="create-an-iot-hub"></a>Создание Центра Интернета вещей

Начните с создания Центра Интернета вещей на портале Azure.
![Создание Центра Интернета вещей][3]

[!INCLUDE [iot-hub-create-hub](../../includes/iot-hub-create-hub.md)]

## <a name="register-an-iot-edge-device"></a>Регистрация устройства IoT Edge

Зарегистрируйте устройство IoT Edge в только что созданном Центре Интернета вещей.
![Регистрация устройства][4]

[!INCLUDE [iot-edge-register-device](../../includes/iot-edge-register-device.md)]

## <a name="install-and-start-the-iot-edge-runtime"></a>Установка и запуск среды выполнения IoT Edge

Установите и запустите среду выполнения Azure IoT Edge на устройстве IoT Edge. 
![Регистрация устройства][5]

Среда выполнения IoT Edge развертывается на всех устройствах IoT Edge. Она состоит из трех компонентов. **Управляющая программа безопасности IoT Edge** запускается при каждой загрузке устройства Edge и перезагружает устройство, запустив агент IoT Edge. **Агент IoT Edge** упрощает развертывание и мониторинг модулей на устройстве IoT Edge, включая центр IoT Edge. **Центр IoT Edge** управляет взаимодействием между модулями на устройстве IoT Edge, а также между устройством и Центром Интернета вещей. 

>[!NOTE]
>Шаги установки, приведенные в этом разделе, выполняются вручную, так как скрипт установки еще разрабатывается. 

Следуя инструкциям в этом разделе, вы настроите среду выполнения IoT Edge с контейнерами Linux. Если нужно использовать контейнеры Windows, см. статью [Install Azure IoT Edge runtime on Windows to use with Windows containers](how-to-install-iot-edge-windows-with-windows.md) (Установка среды выполнения IoT Edge в Windows для использования контейнеров Windows).

### <a name="download-and-install-the-iot-edge-service"></a>Скачивание и установка службы IoT Edge

1. На устройстве IoT Edge запустите PowerShell от имени администратора.

2. Скачайте пакет обновления IoT Edge.

   ```powershell
   Invoke-WebRequest https://conteng.blob.core.windows.net/iotedged/iotedge.zip -o .\iotedge.zip
   Expand-Archive .\iotedge.zip C:\ProgramData\iotedge -f
   $env:Path += ";C:\ProgramData\iotedge"
   SETX /M PATH "$env:Path"
   ```

3. Установите и запустите службу IoT Edge.

   ```powershell
   New-Service -Name "iotedge" -BinaryPathName "C:\ProgramData\iotedge\iotedged.exe -c C:\ProgramData\iotedge\config.yaml"
   Start-Service iotedge
   ```

4. Добавьте в исключения брандмауэра порты, используемые службой IoT Edge.

   ```powershell
   New-NetFirewallRule -DisplayName "iotedged allow inbound 15580,15581" -Direction Inbound -Action Allow -Protocol TCP -LocalPort 15580-15581 -Program "C:\programdata\iotedge\iotedged.exe" -InterfaceType Any
   ```

5. Создайте файл с именем **iotedge.reg** и откройте его в текстовом редакторе. 

6. Добавьте в этот файл следующее содержимое и сохраните файл. 

   ```input
   Windows Registry Editor Version 5.00
   [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\EventLog\Application\iotedged]
   "CustomSource"=dword:00000001
   "EventMessageFile"="C:\\ProgramData\\iotedge\\iotedged.exe"
   "TypesSupported"=dword:00000007
   ```

7. В проводнике перейдите к файлу и дважды щелкните его, чтобы импортировать изменения в реестр Windows. 

### <a name="configure-the-iot-edge-runtime"></a>Настройка среды выполнения IoT Edge 

Настройте среду выполнения, используя строку подключения устройства IoT Edge, скопированную при регистрации нового устройства. Затем настройте сеть среды выполнения. 

1. Откройте файл конфигурации IoT Edge, расположенный по адресу `C:\ProgramData\iotedge\config.yaml`. Этот файл защищен, поэтому запустите текстовый редактор, например Блокнот, от имени администратора, а затем откройте файл в этом редакторе. 

2. Найдите раздел **provisioning** и вместо значения для **device_connection_string** укажите строку, скопированную на странице сведений о своем устройстве IoT Edge. 

3. В окне администратора PowerShell получите имя узла для устройства IoT Edge и скопируйте выходные данные. 

   ```powershell
   hostname
   ```

4. В файле конфигурации найдите раздел **Edge device hostname** (Имя устройства Edge). Замените значение для **hostname** именем узла, скопированным в окне PowerShell.

5. В окне администратора PowerShell получите IP-адрес для устройства IoT Edge. 

   ```powershell
   ipconfig
   ```

6. Скопируйте значение **IPv4-адреса** в разделе выходных данных **vEthernet (DockerNAT)**. 

7. Создайте переменную среды с именем **IOTEDGE_HOST** и замените *\<ip_address\>* IP-адресом для устройства IoT Edge. 

   ```powershell
   [Environment]::SetEnvironmentVariable("IOTEDGE_HOST", "http://<ip_address>:15580")
   ```

8. В файле `config.yaml` найдите раздел **Connect settings** (Параметры подключения). Для параметров **management_uri** и **workload_uri** укажите вместо **\<GATEWAY_ADDRESS\>** значение своего IP-адреса и порты, открытые в рамках предыдущего раздела. 

   ```yaml
   connect: 
     management_uri: "http://<GATEWAY_ADDRESS>:15580"
     workload_uri: "http://<GATEWAY_ADDRESS>:15581"
   ```

9. Найдите раздел **Listen settings** (Параметры ожидания передачи данных) и добавьте те же значения для **management_uri** и **workload_uri**. 

   ```yaml
   listen:
     management_uri: "http://<GATEWAY_ADDRESS>:15580"
     workload_uri: "http://<GATEWAY_ADDRESS>:15581"
   ```

10. Найдите раздел **Moby Container Runtime settings** (Параметры среды выполнения контейнера Moby). Раскомментируйте строку **network** и убедитесь, что задано значение `nat`.

   ```yaml
   moby_runtime:
     uri: "npipe://./pipe/docker_engine"
     network: "nat"
   ```

11. Сохраните файл конфигурации. 

12. Перезапустите службу IoT Edge в PowerShell.

   ```powershell
   Stop-Service iotedge
   Start-Service iotedge
   ```

### <a name="view-the-iot-edge-runtime-status"></a>Просмотр состояния среды выполнения IoT Edge

Убедитесь, что среда выполнения успешно установлена и настроена.

1. Проверьте состояние службы IoT Edge.

   ```powershell
   Get-Service iotedge
   ```

2. Если нужно устранить неполадки со службой, извлеките журналы службы. 

   ```powershell
   # Displays logs from today, newest at the bottom.

   Get-WinEvent -ea SilentlyContinue `
  -FilterHashtable @{ProviderName= "iotedged";
    LogName = "application"; StartTime = [datetime]::Today} |
  select TimeCreated, Message | Sort-Object -Descending
   ```

3. Просмотрите данные обо всех модулях, запущенных на устройстве IoT Edge. Так как служба запущена первый раз, отобразится только запущенный модуль **edgeAgent**. Модуль edgeAgent запускается по умолчанию и позволяет установить и запустить любые дополнительные модули, развертываемые на устройстве. 

   ```powershell
   iotedge list
   ```

   ![Просмотр данных об одном модуле на устройстве](./media/quickstart/iotedge-list-1.png)

## <a name="deploy-a-module"></a>Развертывание модуля

Управляя устройством Azure IoT Edge из облака, разверните модуль, который будет передавать данные телеметрии в Центр Интернета вещей.
![Регистрация устройства][6]

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>Просмотр сформированных данных

В этом руководстве мы создали новое устройство IoT Edge и установили на нем среду выполнения IoT Edge. Затем с помощью портала Azure мы отправили на устройство и запустили модуль IoT Edge, не изменяя на устройстве никаких настроек. В этом примере отправленный модуль создает данные среды, которые можно использовать для работы с руководствами. 

Убедитесь, что развернутый из облака модуль работает на вашем устройстве IoT Edge. 

```powershell
iotedge list
```

   ![Просмотр трех модулей на устройстве](./media/quickstart/iotedge-list-2.png)

Убедитесь, что сообщения отправляются из модуля tempSensor в облако. 

```powershell
iotedge logs tempSensor -f
```

  ![Просмотр получаемых с модуля данных](./media/quickstart/iotedge-logs.png)

Можно также просматривать сообщения, которые получает Центр Интернета вещей, используя средство [Обозреватель Центра Интернета вещей][lnk-iothub-explorer] или [расширение Azure IoT Toolkit для Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit). 

## <a name="clean-up-resources"></a>Очистка ресурсов

Имитированное устройство, настроенное в рамках этого руководства, можно использовать для задач тестирования, приведенных в других руководствах по IoT Edge. Чтобы модуль tempSensor перестал отправлять данные в Центр Интернета вещей, остановите работу службы IoT Edge, используя приведенную ниже команду, и удалите контейнеры, созданные на устройстве. Если вам снова потребуется использовать компьютер как устройство IoT Edge, обязательно запустите службу. 

   ```powershell
   Stop-Service iotedge -NoWait
   docker rm -f $(docker ps -aq)
   ```

Если созданный Центр Интернета вещей вам больше не нужен, удалите этот ресурс и связанные с ним устройства с помощью портала Azure. Перейдите на страницу сведений о Центре Интернета вещей и щелкните **Удалить**. 

## <a name="next-steps"></a>Дополнительная информация

При работе с этим кратким руководством вы создали устройство IoT Edge и с помощью облачного интерфейса Azure IoT Edge развернули код на устройстве. В итоге вы получили устройство для тестирования, генерирующее необработанные данные о своей среде. 

Вы можете перейти к любому из оставшихся руководств, чтобы узнать, как Azure IoT Edge может помочь вам превратить данные в бизнес-аналитику на пограничном устройстве.

> [!div class="nextstepaction"]
> [Фильтрация данных датчика с помощью Функций Azure](tutorial-deploy-function.md)

<!-- Images -->
[2]: ./media/quickstart/install-edge-full.png
[3]: ./media/quickstart/create-iot-hub.png
[4]: ./media/quickstart/register-device.png
[5]: ./media/quickstart/start-runtime.png
[6]: ./media/quickstart/deploy-module.png

<!-- Links -->
[lnk-nested]: https://docs.microsoft.com/virtualization/hyper-v-on-windows/user-guide/nested-virtualization
[lnk-docker]: https://docs.docker.com/docker-for-windows/install/ 
[lnk-python]: https://www.python.org/downloads/
[lnk-docker-containers]: https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10#2-switch-to-windows-containers
[lnk-iothub-explorer]: https://github.com/azure/iothub-explorer
[lnk-install-iotcore]: how-to-install-iot-core.md
[lnk-account]: https://azure.microsoft.com/free
