---
title: Как установить Azure IoT Edge в Linux | Документация Майкрософт
description: Инструкции по установке Azure IoT Edge в Linux ARM32
author: kgremban
manager: timlt
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: kgremban
ms.openlocfilehash: ad70fcc6b9779cb33772a3fce2fb11b4cec804ee
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37062604"
---
# <a name="install-azure-iot-edge-runtime-on-linux-arm32v7armhf"></a>Установка среды выполнения Azure IoT Edge в Linux (ARM32v7/armhf)

Среда выполнения Azure IoT Edge развертывается на всех устройствах IoT Edge. Она состоит из трех компонентов. **Управляющая программа безопасности IoT Edge** обеспечивает безопасность и соответствие стандартам безопасности на устройстве IoT Edge. Управляющая программа запускается при каждой загрузке устройства и перезагружает устройство, запустив агент IoT Edge. **Агент IoT Edge** упрощает развертывание и мониторинг модулей на устройстве IoT Edge, включая центр IoT Edge. **Центр IoT Edge** управляет взаимодействием между модулями на устройстве IoT Edge, а также между устройством и Центром Интернета вещей.

В этой статье приводятся инструкции по установке среды выполнения Azure IoT Edge на устройстве IoT Edge с Linux ARM32v7/armhf (например, Raspberry Pi).

>[!NOTE]
>К пакетам в репозиториях программного обеспечения Linux применяются условия лицензии, которые можно найти в каждом пакете (/usr/share/doc/*имя-пакета*). Прежде чем использовать пакет, ознакомьтесь с условиями лицензии. Установка и использование пакета свидетельствуют о принятии этих условий. Если вы не согласны с условиями лицензии, не используйте этот пакет.

## <a name="install-the-container-runtime"></a>Установка среды выполнения контейнера

Служба Azure IoT Edge основана на среде выполнения контейнера [совместимого с OCI][lnk-oci] (например, Docker). Если вы установили Docker (Community Edition или Enterprise Edition) на устройстве IoT Edge, можете использовать Docker с Azure IoT Edge для разработки и тестирования. 

Для разработки в рабочей среде мы настоятельно рекомендуем использовать платформу [на основе Moby][lnk-moby], описанную ниже. Это единственная платформа контейнеров, которая официально поддерживается с Azure IoT Edge. Образы контейнеров Docker (Community Edition или Enterprise Edition) полностью совместимы со средой выполнения Moby.

Приведенные ниже команды позволяют установить платформу Moby и интерфейс командной строки (CLI). CLI применяется на стадии разработки, но при развертывании в рабочей среде он может не понадобится.

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

Управляющую программу можно настроить с помощью файла конфигурации (`/etc/iotedge/config.yaml`). Устройство IoT Edge можно настроить вручную <!--[automatically via Device Provisioning Service][lnk-dps] or-->, используя [строку подключения устройства][lnk-dcs].

Чтобы настроить устройство вручную, в раздел **provisioning** в файле **config.yaml** вставьте строку подключения для устройства.

```yaml
provisioning:
  source: "manual"
  device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
```

*По умолчанию этот файл защищен от записи, и вам не требуется использовать `sudo` для его изменения. Пример: `sudo nano /etc/iotedge/config.yaml`*

Когда введете сведения о подготовке в файл конфигурации, перезапустите управляющую программу:

```cmd/sh
sudo systemctl restart iotedge
```

## <a name="verify-successful-installation"></a>Проверка успешной установки

Вы можете проверить состояние управляющей программы IoT Edge, используя следующий код:

```cmd/sh
systemctl status iotedge
```

Чтобы просмотреть журналы управляющей программы, используйте следующий код:

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

Чтобы получить список запущенных модулей, используйте следующий код:

```cmd/sh
iotedge list
```

## <a name="next-steps"></a>Дополнительная информация

Если вам не удается установить среду выполнения IoT Edge надлежащим образом, см. страницу со сведениями об [устранении неполадок][lnk-trouble].

<!-- Links -->
[lnk-dcs]: ../iot-hub/quickstart-send-telemetry-dotnet.md#register-a-device
[lnk-dps]: how-to-simulate-dps-tpm.md
[lnk-oci]: https://www.opencontainers.org/
[lnk-moby]: https://mobyproject.org/
[lnk-trouble]: troubleshoot.md