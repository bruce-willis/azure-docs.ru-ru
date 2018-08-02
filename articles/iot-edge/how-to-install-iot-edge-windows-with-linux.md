---
title: Установка Azure IoT Edge в ОС Windows с контейнерами Linux | Документация Майкрософт
description: Инструкции по установке Azure IoT Edge в ОС Windows с контейнерами Linux
author: kgremban
manager: timlt
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: kgremban
ms.openlocfilehash: a56b2b12143a29637196d2239f648b78f1f8e763
ms.sourcegitcommit: cfff72e240193b5a802532de12651162c31778b6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/27/2018
ms.locfileid: "39307871"
---
# <a name="install-azure-iot-edge-runtime-on-windows-to-use-with-linux-containers"></a>Установка среды выполнения Azure IoT Edge в ОС Windows для использования с контейнерами Linux

Среда выполнения Azure IoT Edge развертывается на всех устройствах IoT Edge. Она состоит из трех компонентов. **Управляющая программа безопасности IoT Edge** обеспечивает безопасность и соответствие стандартам безопасности на устройстве Edge. Управляющая программа запускается при каждой загрузке устройства и перезагружает устройство, запуская агент IoT Edge. **Агент IoT Edge** упрощает развертывание и мониторинг модулей на устройстве IoT Edge, в том числе центр IoT Edge. **Центр IoT Edge** управляет взаимодействием между модулями на устройстве IoT Edge, а также между устройством и Центром Интернета вещей.

В этой статье описаны этапы установки среды выполнения Azure IoT Edge на компьютере под управлением 64-разрядной ОС Windows (на базе процессора AMD или Intel). Сейчас поддержка ОС Windows доступна в предварительной версии.

>[!NOTE]
Использование контейнеров Linux в системах Windows не является рекомендуемым или поддерживаемым производственной конфигурацией для Azure IoT Edge. Однако его можно использовать для целей разработки и тестирования.

## <a name="supported-windows-versions"></a>Поддерживаемые версии Windows
Azure IoT Edge может использоваться для разработки и тестирования в следующих версиях Windows при использовании контейнеров Linux.
  * Операционные системы Windows 10 или более поздней версии.
  * Операционные системы сервера Windows Server 2016 или новее.

## <a name="install-the-container-runtime"></a>Установка среды выполнения контейнера 

Служба Azure IoT Edge основана на среде выполнения контейнера [совместимого с OCI][lnk-oci] (например, Docker). 

Для разработки и тестирования можно использовать [Docker для Windows][lnk-docker-for-windows]. Убедитесь, что клиент Docker для Windows [настроен для использования контейнеров Linux][lnk-docker-config]

## <a name="install-the-azure-iot-edge-security-daemon"></a>Установка управляющей программы безопасности Azure IoT Edge

>[!NOTE]
>Использование программных пакетов Azure IoT Edge регулируется условиями лицензии, содержащейся в самих пакетах (в каталоге LICENSE). Прежде чем использовать пакет, ознакомьтесь с условиями лицензии. Установка и использование пакета означают, что вы принимаете эти условия. Если вы не согласны с условиями лицензии, не используйте пакет.

### <a name="download-the-edge-daemon-package-and-install"></a>Скачивание и установка пакета управляющей программы IoT Edge

В окне "Администратор PowerShell" выполните следующие команды.

```powershell
Invoke-WebRequest https://aka.ms/iotedged-windows-latest -o .\iotedged-windows.zip
Expand-Archive .\iotedged-windows.zip C:\ProgramData\iotedge -f
Move-Item c:\ProgramData\iotedge\iotedged-windows\* C:\ProgramData\iotedge\ -Force
rmdir C:\ProgramData\iotedge\iotedged-windows
$sysenv = "HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment"
$path = (Get-ItemProperty -Path $sysenv -Name Path).Path + ";C:\ProgramData\iotedge"
Set-ItemProperty -Path $sysenv -Name Path -Value $path
```

Установите vcruntime с помощью следующей команды.

```powershell
Invoke-WebRequest -useb https://download.microsoft.com/download/0/6/4/064F84EA-D1DB-4EAA-9A5C-CC2F0FF6A638/vc_redist.x64.exe -o vc_redist.exe
.\vc_redist.exe /quiet /norestart
 ```

Установите и запустите службу *IoT Edge*.

```powershell
New-Service -Name "iotedge" -BinaryPathName "C:\ProgramData\iotedge\iotedged.exe -c C:\ProgramData\iotedge\config.yaml"
Start-Service iotedge
```

Добавьте исключения брандмауэра для портов, используемых службой.

```powershell
New-NetFirewallRule -DisplayName "iotedged allow inbound 15580,15581" -Direction Inbound -Action Allow -Protocol TCP -LocalPort 15580-15581 -Program "C:\programdata\iotedge\iotedged.exe" -InterfaceType Any
```

Создайте файл **iotedge.reg** с указанным ниже содержимым и импортируйте его в реестр Windows. Для этого дважды щелкните этот файл или используйте команду `reg import iotedge.reg`.

```
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\EventLog\Application\iotedged]
"CustomSource"=dword:00000001
"EventMessageFile"="C:\\ProgramData\\iotedge\\iotedged.exe"
"TypesSupported"=dword:00000007
```

## <a name="configure-the-azure-iot-edge-security-daemon"></a>Настройка управляющей программы безопасности Azure IoT Edge

Управляющую программу можно настроить с помощью файла конфигурации `C:\ProgramData\iotedge\config.yaml`.

Устройство IoT Edge можно настроить вручную, используя [строку подключения устройства][lnk-dcs], или автоматически, используя [Службы подготовки устройств к добавлению в Центр Интернета вещей][lnk-dps].

* Для настройки вручную раскомментируйте режим подготовки **manual**. Замените значение **device_connection_string** строкой подключения для устройства IoT Edge.

   ```yaml
   provisioning:
     source: "manual"
     device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
  
   # provisioning: 
   #   source: "dps"
   #   global_endpoint: "https://global.azure-devices-provisioning.net"
   #   scope_id: "{scope_id}"
   #   registration_id: "{registration_id}"
   ```

* Для автоматической настройки раскомментируйте режим подготовки **dps**. Замените значения **scope_id** и **registration_id** значениями для вашего экземпляра Службы подготовки устройств к добавлению в Центр Интернета вещей и устройства IoT Edge с доверенным платформенным модулем. 

   ```yaml
   # provisioning:
   #   source: "manual"
   #   device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
  
   provisioning: 
     source: "dps"
     global_endpoint: "https://global.azure-devices-provisioning.net"
     scope_id: "{scope_id}"
     registration_id: "{registration_id}"
   ```

Получите имя устройства Edge с помощью команды `hostname` в PowerShell и укажите его в качестве значения в разделе **hostname:** в YAML-файле конфигурации. Например: 

```yaml
  ###############################################################################
  # Edge device hostname
  ###############################################################################
  #
  # Configures the environment variable 'IOTEDGE_GATEWAYHOSTNAME' injected into
  # modules.
  #
  ###############################################################################

  hostname: "edgedevice-1"
```

Затем укажите IP-адрес и порт для параметров **workload_uri** и **management_uri** в разделах конфигурации **connect:** и **listen:**.

Чтобы получить IP-адрес, введите `ipconfig` в окне PowerShell. Скопируйте IP-адрес интерфейса **vEthernet (DockerNAT)**, как показано в следующем примере (IP-адрес вашего компьютера может отличаться):

![DockerNat][img-docker-nat]

Обновите параметры **workload_uri** и **management_uri** в разделе **connect:** файла конфигурации. Замените **\<GATEWAY_ADDRESS\>** скопированным IP-адресом DockerNAT. 

```yaml
connect:
  management_uri: "http://<GATEWAY_ADDRESS>:15580"
  workload_uri: "http://<GATEWAY_ADDRESS>:15581"
```

В разделе **listen:** введите те же адреса.

```yaml
listen:
  management_uri: "http://<GATEWAY_ADDRESS>:15580"
  workload_uri: "http://<GATEWAY_ADDRESS>:15581"
```

В окне PowerShell создайте переменную среды **IOTEDGE_HOST** с адресом **management_uri**.

```powershell
[Environment]::SetEnvironmentVariable("IOTEDGE_HOST", "http://<GATEWAY_ADDRESS>:15580")
```

Сохраняйте переменную среды между перезагрузками.

```powershell
SETX /M IOTEDGE_HOST "http://<GATEWAY_ADDRESS>:15580"
```

Наконец, убедитесь, что параметр **network:** в разделе **moby_runtime:** раскомментирован и для него указано значение **azure-iot-edge**

```yaml
moby_runtime:
  docker_uri: "npipe://./pipe/docker_engine"
  network: "azure-iot-edge"
```

Сохраните файл конфигурации и перезапустите службу.

```powershell
Stop-Service iotedge -NoWait
sleep 5
Start-Service iotedge
```

## <a name="verify-successful-installation"></a>Проверка установки

Если вы выполняли **настройку вручную**, как описано в предыдущем разделе, подготовленная среда выполнения IoT Edge должна работать на вашем устройстве. Если вы выполняли **автоматическую настройку**, вам нужно сделать еще кое-что, чтобы среда выполнения могла зарегистрировать ваше устройство в центре Интернета вещей от вашего имени. Дальнейшие инструкции см. в руководстве по [созданию и подготовке имитированного устройства IoT Edge TPM в Windows](how-to-auto-provision-simulated-device-windows.md#create-a-tpm-environment-variable).


Чтобы проверить состояние службы IoT Edge, используйте следующий код. 

```powershell
Get-Service iotedge
```

Чтобы просмотреть журналы службы за последние пять минут, используйте следующий код.

```powershell

# Displays logs from last 5 min, newest at the bottom.

Get-WinEvent -ea SilentlyContinue `
  -FilterHashtable @{ProviderName= "iotedged";
    LogName = "application"; StartTime = [datetime]::Now.AddMinutes(-5)} |
  select TimeCreated, Message |
  sort-object @{Expression="TimeCreated";Descending=$false} |
  format-table -autosize -wrap
```

Чтобы получить список запущенных модулей, используйте следующий код.

```powershell
iotedge list
```

## <a name="next-steps"></a>Дополнительная информация

Если вам не удается установить среду выполнения IoT Edge надлежащим образом, см. страницу со сведениями об [устранении неполадок][lnk-trouble].


<!-- Images -->
[img-docker-nat]: ./media/how-to-install-iot-edge-windows-with-linux/dockernat.png

<!-- Links -->
[lnk-docker-config]: https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers
[lnk-dcs]: how-to-register-device-portal.md
[lnk-dps]: how-to-auto-provision-simulated-device-windows.md
[lnk-oci]: https://www.opencontainers.org/
[lnk-moby]: https://mobyproject.org/
[lnk-trouble]: troubleshoot.md
[lnk-docker-for-windows]: https://www.docker.com/docker-windows

