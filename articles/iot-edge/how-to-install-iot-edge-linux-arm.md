---
title: Как установить Azure IoT Edge в Linux | Документация Майкрософт
description: Инструкции по установке Azure IoT Edge в Linux ARM32
author: kgremban
manager: timlt
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 08/14/2018
ms.author: kgremban
ms.openlocfilehash: 7720e0471c6d8f2ba20f28753773829a28f93c7a
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/16/2018
ms.locfileid: "42142937"
---
# <a name="install-azure-iot-edge-runtime-on-linux-arm32v7armhf"></a>Установка среды выполнения Azure IoT Edge в Linux (ARM32v7/armhf)

Среда выполнения Azure IoT Edge развертывается на всех устройствах IoT Edge. Она состоит из трех компонентов. **Управляющая программа безопасности IoT Edge** обеспечивает безопасность и соответствие стандартам безопасности на устройстве Edge. Управляющая программа запускается при каждой загрузке устройства и перезагружает устройство, запуская агент IoT Edge. **Агент IoT Edge** упрощает развертывание и мониторинг модулей на устройстве IoT Edge, в том числе центр IoT Edge. **Центр IoT Edge** управляет взаимодействием между модулями на устройстве IoT Edge, а также между устройством и Центром Интернета вещей.

В этой статье приводятся инструкции по установке среды выполнения Azure IoT Edge на устройстве IoT Edge с Linux ARM32v7/armhf (например, Raspberry Pi).

>[!NOTE]
>К пакетам в репозиториях программного обеспечения Linux применяются условия лицензии, которые можно найти в каждом пакете (/usr/share/doc/*имя-пакета*). Прежде чем использовать пакет, ознакомьтесь с условиями лицензии. Установка и использование пакета свидетельствуют о принятии этих условий. Если вы не согласны с условиями лицензии, не используйте этот пакет.

## <a name="install-the-container-runtime"></a>Установка среды выполнения контейнера

Служба Azure IoT Edge использует среду выполнения контейнера, [совместимую с OCI][lnk-oci]. Для разработки в рабочей среде мы настоятельно рекомендуем использовать платформу [на основе Moby][lnk-moby], описанную ниже. Это единственная платформа контейнеров, которая официально поддерживается с Azure IoT Edge. Образы контейнеров Docker (Community Edition или Enterprise Edition) совместимы со средой выполнения на основе Moby.

Приведенные ниже команды позволяют установить платформу на основе Moby и интерфейс командной строки (CLI). CLI применяется на стадии разработки, но при развертывании в рабочей среде он может не понадобится.

```cmd/sh

# You can copy the entire text from this code block and 
# paste in terminal. The comment lines will be ignored.

# Download and install the moby-engine
curl -L https://aka.ms/moby-engine-armhf-latest -o moby_engine.deb && sudo dpkg -i ./moby_engine.deb

# Download and install the moby-cli
curl -L https://aka.ms/moby-cli-armhf-latest -o moby_cli.deb && sudo dpkg -i ./moby_cli.deb

# Run apt-get fix
sudo apt-get install -f

```

## <a name="install-the-iot-edge-security-daemon"></a>Установка управляющей программы безопасности IoT Edge

```cmd/sh
# You can copy the entire text from this code block and 
# paste in terminal. The comment lines will be ignored.

# Download and install the standard libiothsm implementation
curl -L https://aka.ms/libiothsm-std-linux-armhf-latest -o libiothsm-std.deb && sudo dpkg -i ./libiothsm-std.deb

# Download and install the IoT Edge Security Daemon
curl -L https://aka.ms/iotedged-linux-armhf-latest -o iotedge.deb && sudo dpkg -i ./iotedge.deb

# Run apt-get fix
sudo apt-get install -f
```

## <a name="configure-the-azure-iot-edge-security-daemon"></a>Настройка управляющей программы безопасности Azure IoT Edge


Управляющую программу можно настроить с помощью файла конфигурации `/etc/iotedge/config.yaml`. По умолчанию этот файл защищен от записи, и вам потребуется использовать повышенные разрешения для его изменения.

```bash
sudo nano /etc/iotedge/config.yaml
```

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

Сохраните и закройте файл. 

   `CTRL + X`, `Y`, `Enter`

Когда введете сведения о подготовке в файл конфигурации, перезапустите управляющую программу:

```cmd/sh
sudo systemctl restart iotedge
```

>[!TIP]
>Для запуска команд `iotedge` требуется более высокий уровень привилегий. После установки среды выполнения IoT Edge, выхода из компьютера и обратного входа ваши разрешения обновляются автоматически. До этого момента используйте перед командой префикс **sudo**. 

## <a name="verify-successful-installation"></a>Проверка установки

Если вы выполняли **настройку вручную**, как описано в предыдущем разделе, подготовленная среда выполнения IoT Edge должна работать на вашем устройстве. Если вы выполняли **автоматическую настройку**, вам нужно сделать еще кое-что, чтобы среда выполнения могла зарегистрировать ваше устройство в центре Интернета вещей от вашего имени. Инструкции см. в руководстве по [созданию и подготовке имитированного устройства IoT Edge TPM в Linux](how-to-auto-provision-simulated-device-linux.md#give-iot-edge-access-to-the-tpm).

Вы можете проверить состояние управляющей программы IoT Edge, используя следующий код:

```cmd/sh
systemctl status iotedge
```

Чтобы просмотреть журналы управляющей программы, используйте следующий код:

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

Чтобы получить список запущенных модулей, используйте следующий код.

```cmd/sh
sudo iotedge list
```
>[!NOTE]
>В устройствах с ограниченными ресурсами, например RaspberryPi, настоятельно рекомендуется присвоить переменной среды *OptimizeForPerformance* значение *false* согласно инструкциям, приведенным в [руководстве по устранению неполадок.][lnk-trouble]


## <a name="next-steps"></a>Дополнительная информация

Если вам не удается установить среду выполнения IoT Edge надлежащим образом, см. страницу со сведениями об [устранении неполадок][lnk-trouble].

<!-- Links -->
[lnk-dcs]: how-to-register-device-portal.md
[lnk-dps]: how-to-auto-provision-simulated-device-linux.md
[lnk-trouble]: https://docs.microsoft.com/azure/iot-edge/troubleshoot#stability-issues-on-resource-constrained-devices
[lnk-oci]: https://www.opencontainers.org/
[lnk-moby]: https://mobyproject.org/
