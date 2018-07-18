---
title: Как установить Azure IoT Edge в Linux | Документация Майкрософт
description: Инструкции по установке Azure IoT Edge в Linux
author: kgremban
manager: timlt
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: kgremban
ms.openlocfilehash: 43f82341a3cc9d2163afd35e42864aaa7866b1b2
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37035709"
---
# <a name="install-the-azure-iot-edge-runtime-on-linux-x64"></a>Установка среды выполнения Azure IoT Edge в Linux (x64)

Среда выполнения Azure IoT Edge развертывается на всех устройствах IoT Edge. Она состоит из трех компонентов. **Управляющая программа безопасности IoT Edge** обеспечивает безопасность и соответствие стандартам безопасности на устройстве IoT Edge. Управляющая программа запускается при каждой загрузке устройства и перезагружает устройство, запустив агент IoT Edge. **Агент IoT Edge** упрощает развертывание и мониторинг модулей на устройстве IoT Edge, в том числе центра IoT Edge. **Центр IoT Edge** управляет взаимодействием между модулями на устройстве IoT Edge, а также между устройством и Центром Интернета вещей.

В этой статье описаны этапы установки среды выполнения Azure IoT Edge на устройстве IoT Edge под управлением 64-разрядной ОС Linux (на базе процессора AMD или Intel).

>[!NOTE]
>К пакетам в репозиториях программного обеспечения Linux применяются условия лицензии, которые можно найти в каждом пакете (/usr/share/doc/*имя-пакета*). Прежде чем использовать пакет, ознакомьтесь с условиями лицензии. Установка и использование пакета свидетельствуют о принятии этих условий. Если вы не согласны с условиями лицензии, не используйте пакет.

## <a name="register-microsoft-key-and-software-repository-feed"></a>Регистрация канала репозитория программного обеспечения и ключей (Майкрософт)

### <a name="ubuntu-1604"></a>Ubuntu 16.04.

```cmd/sh
# Install repository configuration
curl https://packages.microsoft.com/config/ubuntu/16.04/prod.list > ./microsoft-prod.list
sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/

# Install Microsoft GPG public key
curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
```

### <a name="ubuntu-1804"></a>Ubuntu 18.04

```cmd/sh
# Install repository configuration
curl https://packages.microsoft.com/config/ubuntu/18.04/prod.list > ./microsoft-prod.list
sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/

# Install Microsoft GPG public key
curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
```

### <a name="debian-9"></a>Debian 9

```cmd/sh
# Install repository configuration
curl https://packages.microsoft.com/config/debian/9/prod.list > ./microsoft-prod.list
sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/

# Install Microsoft GPG public key
curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
```

## <a name="install-the-container-runtime"></a>Установка среды выполнения контейнера 

Служба Azure IoT Edge основана на среде выполнения контейнера [совместимого с OCI][lnk-oci] (например, Docker). Если вы установили Docker (Community Edition или Enterprise Edition) на устройстве IoT Edge, можете использовать Docker с Azure IoT Edge для разработки и тестирования. 

Для разработки в рабочей среде мы настоятельно рекомендуем использовать платформу [на основе Moby][lnk-moby], описанную ниже. Это единственная платформа контейнеров, которая официально поддерживается с Azure IoT Edge. Образы контейнеров Docker (Community Edition или Enterprise Edition) полностью совместимы со средой выполнения Moby.

*Приведенные ниже инструкции позволяют установить платформу Moby и интерфейс командной строки (CLI). CLI применяется на стадии разработки, но при развертывании в рабочей среде он не обязателен.*

```cmd/sh
sudo apt-get update
sudo apt-get install moby-engine
sudo apt-get install moby-cli
```

## <a name="install-the-azure-iot-edge-security-daemon"></a>Установка управляющей программы безопасности Azure IoT Edge

Приведенные ниже команды также позволяют установить стандартную версию **iothsmlib**, если она отсутствует.

```cmd/sh
sudo apt-get update
sudo apt-get install iotedge
```

## <a name="configure-the-azure-iot-edge-security-daemon"></a>Настройка управляющей программы безопасности Azure IoT Edge

Управляющую программу можно настроить с помощью файла конфигурации (`/etc/iotedge/config.yaml`). Устройство IoT Edge можно настроить вручную <!--[automatically via Device Provisioning Service][lnk-dps] or-->, используя [строку подключения устройства][lnk-dcs].

Чтобы настроить устройство вручную, в раздел **provisioning** в файле **config.yaml** вставьте строку подключения для устройства.

```yaml
provisioning:
  source: "manual"
  device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
```

*По умолчанию этот файл защищен от записи, и вам потребуется использовать `sudo` для его изменения. Например так: `sudo nano /etc/iotedge/config.yaml`*

Когда введете сведения о подготовке в файл конфигурации, перезапустите управляющую программу:

```cmd/sh
sudo systemctl restart iotedge
```

## <a name="verify-successful-installation"></a>Проверка успешного выполнения установки

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
