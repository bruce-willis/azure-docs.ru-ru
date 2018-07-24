---
title: Краткое руководство по использованию Azure IoT Edge в ОС Windows | Документация Майкрософт
description: Первое знакомство с Azure IoT Edge и выполнение анализа на имитированном пограничном устройстве
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/24/2018
ms.topic: quickstart
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 19fd671514da0dbfb1704c37d4347e870763d41b
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/17/2018
ms.locfileid: "39091819"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-from-the-azure-portal-to-a-windows-device---preview"></a>Краткое руководство по развертыванию первого простого модуля IoT Edge на устройстве с Windows при помощи портала Azure (предварительная версия)

В этом кратком руководстве вы примените облачный интерфейс Azure IoT Edge для удаленного развертывания готового кода на устройстве IoT Edge. Чтобы выполнить эту задачу, сначала создайте виртуальное устройство IoT Edge на устройстве с Windows,а затем разверните на нем нужный модуль.

Из этого краткого руководства вы узнаете, как выполнять следующие задачи:

1. Создайте Центр Интернета вещей.
2. Регистрация устройства IoT Edge в Центре Интернета вещей.
3. Установка и запуск среды выполнения IoT Edge на устройстве.
4. Удаленное развертывание модуля на устройстве IoT Edge и отправка телеметрии в Центр Интернета вещей.

![Архитектура, используемая в руководстве][2]

Модуль, который вы развернете в рамках этого краткого руководства, представляет собой имитированный датчик, генерирующий данные температуры, влажности и давления. В других руководствах по Azure IoT Edge используются наработки из этой статьи: развернутые модули, которые анализируют смоделированные данные для бизнес-аналитики. 

>[!NOTE]
>Среда выполнения IoT Edge в ОС Windows доступна в режиме [общедоступной предварительной версии](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Если у вас еще нет подписки Azure, перед началом работы [создайте бесплатную учетную запись Azure][lnk-account].

## <a name="prerequisites"></a>Предварительные требования

В рамках этого краткого руководства компьютер или виртуальная машина Windows используются в качестве устройства IoT Edge. Если вы работаете на виртуальной машине с Windows, включите [вложенную виртуализацию][lnk-nested] и выделите не менее 2 ГБ памяти. 

На компьютере, используемом для устройства IoT Edge, подготовьте следующие необходимые компоненты:

1. Убедитесь, что используется поддерживаемая версия Windows:
   * Windows 10 или более поздней версии;
   * Windows Server 2016 или поздней версии.
2. Установите среду [Docker для Windows][lnk-docker] и убедитесь, что она выполняется.
3. Настройте Docker для использования [контейнеров Linux](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).

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

1. В Azure Cloud Shell создайте группу ресурсов. Следующий пример кода создает группу ресурсов с именем **IoTEdgeResources** в регионе **Западная часть США**. Поместив в группу все ресурсы, используемые для кратких руководств и инструкций, вы можете управлять ими совместно. 

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus
   ```

1. Создайте в новой группе ресурсов Центр Интернета вещей. При помощи следующего кода создается бесплатный центр **F1** в группе ресурсов **IoTEdgeResources**. Замените *{hub_name}* уникальным именем для вашего Центра Интернета вещей.

   ```azurecli-interactive
   az iot hub create --resource-group IoTEdgeResources --name {hub_name} --sku F1 
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
   Invoke-WebRequest https://aka.ms/iotedged-windows-latest -o .\iotedged-windows.zip
   Expand-Archive .\iotedged-windows.zip C:\ProgramData\iotedge -f
   Move-Item c:\ProgramData\iotedge\iotedged-windows\* C:\ProgramData\iotedge\ -Force
   rmdir C:\ProgramData\iotedge\iotedged-windows
   $sysenv = "HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment"
   $path = (Get-ItemProperty -Path $sysenv -Name Path).Path + ";C:\ProgramData\iotedge"
   Set-ItemProperty -Path $sysenv -Name Path -Value $path
   ```

3. Установите vcruntime.

  ```powershell
  Invoke-WebRequest -useb https://download.microsoft.com/download/0/6/4/064F84EA-D1DB-4EAA-9A5C-CC2F0FF6A638/vc_redist.x64.exe -o vc_redist.exe
  .\vc_redist.exe /quiet /norestart
  ```

4. Установите и запустите службу IoT Edge.

   ```powershell
   New-Service -Name "iotedge" -BinaryPathName "C:\ProgramData\iotedge\iotedged.exe -c C:\ProgramData\iotedge\config.yaml"
   Start-Service iotedge
   ```

5. Добавьте в исключения брандмауэра порты, используемые службой IoT Edge.

   ```powershell
   New-NetFirewallRule -DisplayName "iotedged allow inbound 15580,15581" -Direction Inbound -Action Allow -Protocol TCP -LocalPort 15580-15581 -Program "C:\programdata\iotedge\iotedged.exe" -InterfaceType Any
   ```

6. Создайте файл с именем **iotedge.reg** и откройте его в текстовом редакторе. 

7. Добавьте в этот файл следующее содержимое и сохраните файл. 

   ```input
   Windows Registry Editor Version 5.00
   [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\EventLog\Application\iotedged]
   "CustomSource"=dword:00000001
   "EventMessageFile"="C:\\ProgramData\\iotedge\\iotedged.exe"
   "TypesSupported"=dword:00000007
   ```

8. В проводнике перейдите к файлу и дважды щелкните его, чтобы импортировать изменения в реестр Windows. 

### <a name="configure-the-iot-edge-runtime"></a>Настройка среды выполнения IoT Edge 

Настройте среду выполнения, используя строку подключения устройства IoT Edge, скопированную при регистрации нового устройства. Затем настройте сеть среды выполнения. 

1. Откройте файл конфигурации IoT Edge, расположенный по адресу `C:\ProgramData\iotedge\config.yaml`. Этот файл защищен, поэтому запустите текстовый редактор, например Блокнот, от имени администратора, а затем откройте файл в этом редакторе. 

2. Найдите раздел **provisioning** и вместо значения для **device_connection_string** укажите строку, скопированную на странице сведений о своем устройстве IoT Edge. 

3. В окне администратора PowerShell получите имя узла для устройства IoT Edge и скопируйте выходные данные. 

   ```powershell
   hostname
   ```

4. В файле конфигурации найдите раздел **Edge device hostname** (Имя устройства Edge). Замените значение для **hostname** именем узла, скопированным в окне PowerShell.

3. В окне администратора PowerShell получите IP-адрес для устройства IoT Edge. 

   ```powershell
   ipconfig
   ```

4. Скопируйте значение **IPv4-адреса** в разделе выходных данных **vEthernet (DockerNAT)**. 

5. Создайте переменную среды с именем **IOTEDGE_HOST** и замените *\<ip_address\>* IP-адресом для устройства IoT Edge. 

   ```powershell
   [Environment]::SetEnvironmentVariable("IOTEDGE_HOST", "http://<ip_address>:15580")
   ```

6. В файле `config.yaml` найдите раздел с **параметрами подключения**. Для параметров **management_uri** и **workload_uri** укажите значение своего IP-адреса и порты, открытые в рамках предыдущего раздела. Замените **\<GATEWAY_ADDRESS\>** скопированным IP-адресом DockerNAT. 

   ```yaml
   connect: 
     management_uri: "http://<GATEWAY_ADDRESS>:15580"
     workload_uri: "http://<GATEWAY_ADDRESS>:15581"
   ```

7. Найдите раздел **Listen settings** (Параметры ожидания передачи данных) и добавьте те же значения для **management_uri** и **workload_uri**. 

   ```yaml
   listen:
     management_uri: "http://<GATEWAY_ADDRESS>:15580"
     workload_uri: "http://<GATEWAY_ADDRESS>:15581"
   ```

8. Найдите раздел **параметров среды выполнения контейнера Moby** и убедитесь, что значение для **сети** равно `nat`.

9. Сохраните файл конфигурации. 

10. Перезапустите службу IoT Edge в PowerShell.

   ```powershell
   Stop-Service iotedge -NoWait
   sleep 5
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
    select TimeCreated, Message |
    sort-object @{Expression="TimeCreated";Descending=$false}
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

Вы также можете просматривать сообщения, которые получает Центр Интернета вещей, используя средство [Обозреватель Центра Интернета вещей][lnk-iothub-explorer] или [расширение Azure IoT Toolkit для Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit). 

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы хотите продолжить ознакомление с руководствами по IoT Edge, используйте устройство, зарегистрированное и настроенное в рамках этого краткого руководства. В противном случае можно удалить с устройства созданные ресурсы Azure и среду выполнения IoT Edge. 

### <a name="delete-azure-resources"></a>Удаление ресурсов Azure

Если вы создали виртуальную машину и Центр Интернета вещей в новой группе ресурсов, можно удалить эту группу и все связанные с ней ресурсы. Если в группе ресурсов, которую необходимо удалить, имеются важные данные, можно удалить только ненужные ресурсы. 

Удалите группу **IoTEdgeResources**. 

   ```azurecli-interactive
   az group delete --name IoTEdgeResources 
   ```

### <a name="remove-the-iot-edge-runtime"></a>Удаление среды выполнения IoT Edge

Если вы планируете использовать устройство IoT Edge для тестирования в будущем, но хотите, чтобы неиспользуемый модуль tempSensor перестал отправлять данные в Центр Интернета вещей, остановите работу службы IoT Edge, используя приведенную ниже команду. 

   ```powershell
   Stop-Service iotedge -NoWait
   ```

Когда вы будете готовы к тестированию, вы сможете перезапустить службы.

   ```powershell
   Start-Service iotedge
   ```

Чтобы удалить файлы установки с устройства, используйте приведенные ниже команды.  

Удалите среду выполнения IoT Edge.

   ```powershell
   cmd /c sc delete iotedge
   rm -r c:\programdata\iotedge
   ```

При удалении среды выполнения IoT Edge созданные с ее помощью контейнеры остановятся, но будут по-прежнему храниться на устройстве. Просмотрите все контейнеры.

   ```powershell
   docker ps -a
   ```

Удалите контейнеры, созданные на устройстве средой выполнения IoT Edge. Измените имя контейнера tempSensor, если вы указали для него другое имя. 

   ```powershell
   docker rm -f tempSensor
   docker rm -f edgeHub
   docker rm -f edgeAgent
   ```
   
## <a name="next-steps"></a>Дополнительная информация

При работе с этим кратким руководством вы создали устройство IoT Edge и с помощью облачного интерфейса Azure IoT Edge развернули код на устройстве. В итоге вы получили устройство для тестирования, генерирующее необработанные данные о своей среде. 

Вы можете перейти к любому из оставшихся руководств, чтобы узнать, как Azure IoT Edge может помочь вам превратить данные в бизнес-аналитику на пограничном устройстве.

> [!div class="nextstepaction"]
> [Фильтрация данных датчика с помощью Функций Azure](tutorial-deploy-function.md)


<!-- Images -->
[1]: ./media/quickstart/cloud-shell.png
[2]: ./media/quickstart/install-edge-full.png
[3]: ./media/quickstart/create-iot-hub.png
[4]: ./media/quickstart/register-device.png
[5]: ./media/quickstart/start-runtime.png
[6]: ./media/quickstart/deploy-module.png


<!-- Links -->
[lnk-docker]: https://docs.docker.com/docker-for-windows/install/ 
[lnk-iothub-explorer]: https://github.com/azure/iothub-explorer
[lnk-account]: https://azure.microsoft.com/free
[lnk-portal]: https://portal.azure.com
[lnk-nested]: https://docs.microsoft.com/virtualization/hyper-v-on-windows/user-guide/nested-virtualization
[lnk-delete]: https://docs.microsoft.com/cli/azure/iot/hub?view=azure-cli-latest#az_iot_hub_delete

<!-- Anchor links -->
[anchor-register]: #register-an-iot-edge-device
