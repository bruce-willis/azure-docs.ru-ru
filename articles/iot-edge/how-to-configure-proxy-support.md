---
title: Настройка устройства Azure IoT Edge для прокси-серверов сети | Документы Майкрософт
description: Сведения о настройке среды выполнения Azure IoT Edge и модулей IoT Edge с подключением к Интернету для обмена данными через прокси-сервер.
author: kgremban
manager: ''
ms.author: kgremban
ms.date: 09/24/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 6e6a1d2f758cabca41ac405a01de1f0d8bfd0a7b
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47037462"
---
# <a name="configure-an-iot-edge-device-to-communicate-through-a-proxy-server"></a>Настройка устройства IoT Edge для обмена данными через прокси-сервер

Устройства IoT Edge отправляют HTTPS-запросы для обмена данными с центром IoT. Если устройство подключено к сети, которая использует прокси-сервер, необходимо настроить среду выполнения IoT Edge для обмена данными через сервер. Прокси-серверы могут также влиять на отдельные модули IoT Edge, если они отправляют HTTP- или HTTPS-запросы, которые невозможно направить через центр Edge. 

Настройка устройства IoT Edge для работы с прокси-сервером включает следующие основные шаги: 

1. Установка среды выполнения IoT Edge на устройстве. 
2. Настройте управляющие программы Docker и IoT Edge на устройстве таким образом, чтобы они использовали прокси-сервер.
3. Настройте свойства агента Edge в файле config.yaml на своем устройстве.
4. Задайте переменные среды для среды выполнения IoT Edge и других модулей IoT Edge в манифесте развертывания. 

## <a name="install-the-runtime"></a>Установка среды выполнения

При установке среды выполнения IoT Edge на устройстве Linux настройте в диспетчере пакетов прохождение через прокси-сервер для доступа к пакету установки. Например, [настройте apt-get для использования прокси-сервера HTTP](https://help.ubuntu.com/community/AptGet/Howto/#Setting_up_apt-get_to_use_a_http-proxy). Настроив диспетчер пакетов, следуйте инструкциям в статье [Установка среды выполнения Azure IoT Edge в Linux (ARM32v7/armhf)](how-to-install-iot-edge-linux-arm.md) или [Установка среды выполнения Azure IoT Edge в Linux (x64)](how-to-install-iot-edge-linux.md) как обычно. 

При установке среды выполнения IoT Edge на устройстве Windows необходимо пройти через прокси-сервер, чтобы получить доступ к пакету установки. Сведения о прокси-сервере можно настроить в параметрах Windows или включить непосредственно в сценарий установки. Следующий сценарий PowerShell является примером установки в Windows с использованием аргумента `-proxy`:

```powershell
. {Invoke-WebRequest -proxy <proxy URL> -useb aka.ms/iotedge-win} | Invoke-Expression; `
Install-SecurityDaemon -Manual -ContainerOs Windows
```

Дополнительные сведения и описание вариантов установки см. в статье [Установка среды выполнения Azure IoT Edge в Windows для использования с контейнерами Windows](how-to-install-iot-edge-windows-with-windows.md) или [Установка среды выполнения Azure IoT Edge в ОС Windows для использования с контейнерами Linux](how-to-install-iot-edge-windows-with-linux.md).

После установки среды выполнения IoT Edge используйте следующий раздел, чтобы настроить для нее сведения о прокси-сервере. 

## <a name="configure-the-daemons"></a>Настройка управляющих программ

Управляющие программы IoT Edge и Docker, установленные на устройстве IoT Edge, необходимо настроить на использование прокси-сервера. Управляющая программа Docker создает веб-запросы для получения образов контейнеров из реестров контейнеров. Управляющая программа IoT Edge отправляет HTTPS-запросы для обмена данными с центром IoT.

### <a name="docker-daemon"></a>Управляющая программа Docker

Инструкции по настройке управляющей программы Docker с помощью переменных среды см. в документации к Docker. Большинство реестров контейнеров (включая DockerHub и реестры контейнеров Azure) поддерживают HTTPS-запросы, поэтому необходимо задать переменную **HTTPS_PROXY**. Если вы извлекаете образы из реестра, который не поддерживает протокол TLS, можете задать переменную **HTTP_PROXY**. 

Выберите статью, которая относится к вашей версии Docker: 

* [Docker](https://docs.docker.com/config/daemon/systemd/#httphttps-proxy)
* [Docker для Windows](https://docs.docker.com/docker-for-windows/#proxies)

### <a name="iot-edge-daemon"></a>Управляющая программа IoT Edge

Управляющая программа IoT Edge настраивается точно так же, как управляющая программа Docker. Все запросы, которые IoT Edge отправляет в центр IoT, используют HTTPS. Создайте переменную среды для службы, выполнив указанные ниже действия с учетом своей операционной системы. 

#### <a name="linux"></a>Linux

Откройте редактор в терминале, чтобы настроить управляющую программу IoT Edge. 

```bash
sudo systemctl edit iotedge
```

Введите следующий текст, заменив **\<proxy URL>** на адрес и порт прокси-сервера. Сохранитесь и закройте редактор. 

```text
[Service]
Environment="https_proxy=<proxy URL>"
```

Обновите диспетчер служб, чтобы получить новую конфигурацию для IoT Edge.

```bash
sudo systemctl daemon-reload
```

Перезапустите IoT Edge, чтобы изменения вступили в силу.

```bash
sudo systemctl restart iotedge
```

Убедитесь, что переменная среды была создана, а новая конфигурация загружена. 

```bash
systemctl show --property=Environment iotedge
```

#### <a name="windows"></a>Windows

Откройте окно PowerShell от имени администратора и выполните следующую команду, чтобы внести изменения в реестр с помощью новой переменной среды. Замените **\<proxy url>** на адрес и порт прокси-сервера. 

```powershell
reg add HKLM\SYSTEM\CurrentControlSet\Services\iotedge /v Environment /t REG_MULTI_SZ /d https_proxy=<proxy URL>
```

Перезапустите IoT Edge, чтобы изменения вступили в силу.

```powershell
Restart-Service iotedge
```

## <a name="configure-the-edge-agent"></a>Настройка агента Edge

Агент Edge — это первый модуль, который нужно запустить на любом устройстве IoT Edge. Впервые он запускается с учетом данных, указанных в файле IoT Edge config.yaml. Затем агент Edge подключается к центру IoT для получения манифестов развертывания и, таким образом, сообщает, что на устройстве можно развертывать и другие модули.

Откройте файл config.yaml на устройстве IoT Edge. В системах Linux он находится в папке **/etc/iotedge/config.yaml**. В системах Windows он находится в папке **C:\ProgramData\iotedge\config.yaml**. Файл конфигурации защищен, поэтому для доступа к этому файлу требуются права администратора. В системах Linux это означает, что прежде, чем открывать файл в предпочтительном редакторе, нужно выполнить команду `sudo`. В Windows это означает, что сначала нужно запустить текстовый редактор, например Блокнот, от имени администратора, а затем открыть файл. 

В файле config.yaml найдите раздел **Edge Agent module spec**. Определение агента Edge включает параметр **env**, в который можно добавить переменные среды. 

![Определение edgeAgent](./media/how-to-configure-proxy-support/edgeagent-unedited.png)

Удалите фигурные скобки, которые служат заменителями для параметра env, и добавьте новую переменную в новой строке. Помните, что отступы в YAML составляют два пробела. 

```yaml
https_proxy: "<proxy URL>"
```

По умолчанию среда выполнения IoT Edge использует AMQP для обмена данными с центром IoT. Некоторые прокси-серверы блокируют порты AMQP. Если это так, настройте edgeAgent на использование протокола AMQP через WebSocket. Добавьте вторую переменную среды.

```yaml
UpstreamProtocol: "AmqpWs"
```

![Определение edgeAgent с переменными среды](./media/how-to-configure-proxy-support/edgeagent-edited.png)

Сохраните изменения в файле config.yaml и закройте редактор. Перезапустите IoT Edge, чтобы изменения вступили в силу. 

* Linux: 

   ```bash
   sudo systemctl restart iotedge
   ```

* Windows:

   ```powershell
   Restart-Service iotedge
   ```

## <a name="configure-deployment-manifests"></a>Настройка манифестов развертывания  

После того как устройство IoT Edge будет настроено для работы с вашим прокси-сервером, необходимо будет также объявить переменные среды во всех последующих манифестах развертывания. Два модуля среды выполнения, edgeAgent и edgeHub должны всегда иметь прокси-сервер, настроенный для поддержания связи с центром IoT. Любой модуль IoT Edge можно настроить на обмен данными через прокси-сервер, но это не требуется для модулей, которые направляют свои сообщения через edgeHub или могут обмениваться данными только с другими модулями на устройстве. 

Манифесты развертывания можно создать с помощью портала Azure или вручную, отредактировав JSON-файл. 

### <a name="azure-portal"></a>Портал Azure

Когда вы создаете развертывания для устройств IoT Edge с помощью мастера **Настройка модулей**, каждый модуль включает раздел **Переменные среды**, который можно использовать для настройки подключений прокси-сервера. 

Чтобы настроить агент Edge и модули центра Edge, выберите пункт **Настройка дополнительных параметров среды выполнения Edge** в первом шаге мастера. 

![Настройка дополнительных параметров среды выполнения Edge](./media/how-to-configure-proxy-support/configure-runtime.png)

Добавьте переменную среды **https_proxy** и в агент Edge, и в определения модуля центра Edge. Если вы добавили переменную среды **UpstreamProtocol** в файл config.yaml на устройстве IoT Edge, добавьте ее также в определение модуля агента Edge. 

![Настройка переменных среды](./media/how-to-configure-proxy-support/edgehub-environmentvar.png)

Все остальные модули, добавляемые в манифест развертывания, должны соответствовать той же схеме. Раздел переменных среды находится на странице настройки имени и изображения модуля.

### <a name="json-deployment-manifest-files"></a>Файлы манифеста развертывания JSON

Если вы создаете развертывания для устройств IoT Edge с помощью шаблонов в Visual Studio Code или путем создания JSON-файлов вручную, вы можете добавить переменные среды в определение каждого модуля напрямую. 

Используйте следующий формат JSON: 

```json
"env": {
    "https_proxy": {
        "value": "<proxy URL>"
    }
}
```

С переменными среды определение модуля должно выглядеть как в следующем примере центра Edge:

```json
"edgeHub": {
    "type": "docker",
    "settings": {
        "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
        "createOptions": ""
    },
    "env": {
        "https_proxy": {
            "value": "https://proxy.example.com:3128"
        }
    },
    "status": "running",
    "restartPolicy": "always"
}
```

Если вы добавили переменную среды **UpstreamProtocol** в файл config.yaml на устройстве IoT Edge, добавьте ее также в определение модуля агента Edge. 

```json
"env": {
    "https_proxy": {
        "value": "<proxy URL"
    },
    "UpstreamProtocol": {
        "value": "AmqpWs"
    }
}
```

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о ролях см. в статье о [среде выполнения IoT Edge](iot-edge-runtime.md).

Информацию об устранении ошибок при установке и настройке см. в статье [Распространенные проблемы и их решения для Azure IoT Edge](troubleshoot.md).

