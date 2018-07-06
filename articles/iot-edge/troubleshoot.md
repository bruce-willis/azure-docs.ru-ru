---
title: Устранение неполадок в Azure IoT Edge | Документация Майкрософт
description: Решения распространенных проблем и получение навыков по устранению неполадок для Azure IoT Edge
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/26/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: efe3e31a1a92e21f2c3a3461deba248d2a8c97fa
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37029447"
---
# <a name="common-issues-and-resolutions-for-azure-iot-edge"></a>Распространенные проблемы и их решения для Azure IoT Edge

Если при запуске Azure IoT Edge в вашей среде возникают проблемы, используйте эту статью как руководство для устранения неполадок. 

## <a name="standard-diagnostic-steps"></a>Стандартные действия диагностики 

При возникновении проблемы необходимо узнать о состоянии устройства IoT Edge. Для этого можно просмотреть журналы контейнера и сообщения, переданные на устройство и из него. Для сбора сведений используйте команды и инструменты, приведенные в этом разделе. 

### <a name="check-the-status-of-the-iot-edge-security-manager-and-its-logs"></a>Проверка состояния диспетчера безопасности IoT Edge и его журналов

В Linux
- Чтобы просмотреть состояние диспетчера безопасности IoT Edge, используйте следующую команду:

   ```bash
   sudo systemctl status iotedge
   ```

- Чтобы просмотреть журналы диспетчера безопасности IoT Edge, используйте следующую команду:

    ```bash
    sudo journalctl -u iotedge -f
    ```

- Чтобы просмотреть более подробные сведения журналов диспетчера безопасности IoT Edge, сделайте следующее.

   - Измените параметры управляющей программы IoT Edge:

      ```bash
      sudo systemctl edit iotedge.service
      ```
   
   - Обновите следующие строки:
    
      ```
      [Service]
      Environment=IOTEDGE_LOG=edgelet=debug
      ```
    
   - Перезапустите управляющую программу безопасности IoT Edge:
    
      ```bash
      sudo systemctl cat iotedge.service
      sudo systemctl daemon-reload
      sudo systemctl restart iotedge
      ```

Действия для ОС Windows.
- Чтобы просмотреть состояние диспетчера безопасности IoT Edge, используйте следующую команду:

   ```powershell
   Get-Service iotedge
   ```

- Чтобы просмотреть журналы диспетчера безопасности IoT Edge, используйте следующую команду:

   ```powershell
   # Displays logs from today, newest at the bottom.
 
   Get-WinEvent -ea SilentlyContinue `
   -FilterHashtable @{ProviderName= "iotedged";
     LogName = "application"; StartTime = [datetime]::Today} |
   select TimeCreated, Message |
   sort-object @{Expression="TimeCreated";Descending=$false}
   ```

### <a name="if-the-iot-edge-security-manager-is-not-running-verify-your-yaml-configuration-file"></a>Если диспетчер безопасности IoT Edge не запущен, проверьте YAML-файл конфигурации.

> [!WARNING]
> YAML-файлы не могут содержать отступы в виде табуляции. Вместо этого используйте двойные пробелы.

В Linux

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

Действия для ОС Windows.

   ```cmd
   notepad C:\ProgramData\iotedge\config.yaml
   ```

### <a name="check-container-logs-for-issues"></a>Проверка журналов контейнеров на наличие ошибок

После запуска управляющей программы безопасности IoT Edge проверьте журналы контейнеров на наличие ошибок. Начните с развернутых контейнеров, а затем просмотрите контейнеры, которые составляют среду выполнения IoT Edge: агент и центр Edge. В журналах агента Edge обычно содержатся сведения о жизненном цикле каждого контейнера, а в журналах центра Edge — об обмене сообщениями и маршрутизации. 

   ```cmd
   iotedge logs <container name>
   ```

### <a name="view-the-messages-going-through-the-edge-hub"></a>Просмотр сообщений, отправляемых через концентратор Edge

Просмотрите сообщения, отправляемые через концентратор Edge, и соберите сведения об обновлениях свойств устройства из подробных журналов контейнеров в средах выполнения edgeAgent и edgeHub. Чтобы включить ведение подробных журналов в этих контейнерах, задайте значение для переменной `RuntimeLogLevel` среды. 

В Linux
    
   ```cmd
   export RuntimeLogLevel="debug"
   ```
    
Действия для ОС Windows.
    
   ```powershell
   [Environment]::SetEnvironmentVariable("RuntimeLogLevel", "debug")
   ```

Можно также проверить сообщения, отправленные между Центром Интернета вещей и устройствами IoT Edge. Просмотрите эти сообщения с помощью расширения [Azure IoT Toolkit](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) для Visual Studio Code. Дополнительные сведения см. в записи блога об [удобном средстве при разработке с помощью Центра Интернета вещей Azure](https://blogs.msdn.microsoft.com/iotdev/2017/09/01/handy-tool-when-you-develop-with-azure-iot/).

### <a name="restart-containers"></a>Перезапуск контейнеров
После изучения журналов и сообщений можно попытаться перезапустить контейнеры:

```
iotedge restart <container name>
```

Перезапустите контейнеры среды выполнения IoT Edge:

```
iotedge restart edgeAgent && iotedge restart edgeHub
```

### <a name="restart-the-iot-edge-security-manager"></a>Перезапустите диспетчер безопасности IoT Edge:

Если проблема не исчезла, попытайтесь перезапустить диспетчер безопасности IoT Edge.

В Linux

   ```cmd
   sudo systemctl restart iotedge
   ```

Действия для ОС Windows.

   ```powershell
   Stop-Service iotedge -NoWait
   sleep 5
   Start-Service iotedge
   ```

## <a name="edge-agent-stops-after-about-a-minute"></a>Прекращение работы агента Edge в течение минуты

После запуска агент Edge работает в течение минуты и останавливается. В журналах указывается, что агент Edge пытается установить подключение к Центру Интернета вещей через расширенный протокол управления очередью сообщений (AMQP), а затем по истечении приблизительно 30 секунд пытается выполнить подключение с помощью того же расширенного протокола AMQP через WebSocket. Когда это не удается выполнить, агент Edge завершает свою работу. 

Примеры журналов агента Edge:

```output
2017-11-28 18:46:19 [INF] - Starting module management agent. 
2017-11-28 18:46:19 [INF] - Version - 1.0.7516610 (03c94f85d0833a861a43c669842f0817924911d5) 
2017-11-28 18:46:19 [INF] - Edge agent attempting to connect to IoT Hub via AMQP... 
2017-11-28 18:46:49 [INF] - Edge agent attempting to connect to IoT Hub via AMQP over WebSocket... 
```

### <a name="root-cause"></a>Первопричина
Сетевая конфигурация сети узла препятствует получению доступа к сети агентом Edge. Сначала агент пытается подключиться через AMQP (порт 5671). Если это не удается, он пытается подключиться через WebSocket (порт 443).

Среда выполнения IoT Edge настраивает сеть для всех модулей, с которыми нужно взаимодействовать. В Linux этой сетью является мостовая сеть. В Windows используется NAT. Эта проблема чаще всего встречается на устройствах с Windows, использующих контейнеры Windows с сетью NAT. 

### <a name="resolution"></a>Способы устранения:
Должен быть маршрут к Интернету для IP-адресов, назначенных этой мостовой сети или сети NAT. Иногда конфигурация VPN на узле переопределяет сеть IoT Edge. 

## <a name="edge-hub-fails-to-start"></a>Сбой запуска Центра Edge

Когда не удается запустить Центр Edge, в журналах отображается следующее сообщение: 

```output
One or more errors occurred. 
(Docker API responded with status code=InternalServerError, response=
{\"message\":\"driver failed programming external connectivity on endpoint edgeHub (6a82e5e994bab5187939049684fb64efe07606d2bb8a4cc5655b2a9bad5f8c80): 
Error starting userland proxy: Bind for 0.0.0.0:443 failed: port is already allocated\"}\n) 
```

### <a name="root-cause"></a>Первопричина
На сервере связанный порт 443 используется для другого процесса. Центр Edge сопоставляет порты 5671 и 443 для использования в шлюзах в различных условиях. Если с этим портом связан другой процесс, такое сопоставление портов завершается сбоем. 

### <a name="resolution"></a>Способы устранения:
Найдите и остановите процесс, который использует порт 443. Таким процессом обычно является веб-сервер.

## <a name="edge-agent-cant-access-a-modules-image-403"></a>Агент Edge не может получить доступ к образу модуля (403)
Запуск контейнера завершается сбоем, и в журналах агента Edge отображается ошибка 403. 

### <a name="root-cause"></a>Первопричина
У агента Edge нет разрешений на доступ к образу модуля. 

### <a name="resolution"></a>Способы устранения:
Убедитесь, что в манифесте развертывания правильно указаны учетные данные реестра.

## <a name="iot-edge-security-daemon-fails-with-an-invalid-hostname"></a>Сбой управляющей программы безопасности IoT Edge с недопустимым именем узла

Команда `sudo journalctl -u iotedge` завершается ошибкой и выводит следующее сообщение: 

```output
Error parsing user input data: invalid hostname. Hostname cannot be empty or greater than 64 characters
```

### <a name="root-cause"></a>Первопричина
Среда выполнения IoT Edge поддерживает только имена узлов, которые короче 64 символов. Обычно это не является проблемой для физических компьютеров, но может наблюдаться при установке среды выполнения на виртуальной машине. Автоматически создаваемые имена узлов для виртуальных машин Windows, размещенных в Azure, часто бывают длинными. 

### <a name="resolution"></a>Способы устранения:
Эту ошибку можно устранить, настроив DNS-имя виртуальной машины, а затем задав DNS-имя в качестве имени узла в команде установки.

1. На портале Azure перейдите к странице обзора виртуальной машины. 
2. Выберите **Настроить** под DNS-именем. Если для виртуальной машины уже настроено DNS-имя, настраивать новое не нужно. 

   ![Настройка DNS-имени](./media/troubleshoot/configure-dns.png)

3. Укажите значение в поле **Метка DNS-имени** и выберите **Сохранить**.
4. Скопируйте новое DNS-имя, которое должно быть в формате **\<DNSnamelabel\>.\<vmlocation\>.cloudapp.azure.com**.
5. В виртуальной машине используйте следующую команду для настройки среды выполнения IoT Edge с DNS-именем:

   - В Linux

      ```bash
      sudo nano /etc/iotedge/config.yaml
      ```

   - Действия для ОС Windows.

      ```cmd
      notepad C:\ProgramData\iotedge\config.yaml
      ```

## <a name="next-steps"></a>Дополнительная информация
Считаете, что обнаружили ошибку в платформе IoT Edge? [Отправьте запрос](https://github.com/Azure/iot-edge/issues), чтобы мы как можно скорее устранили неисправность. 
