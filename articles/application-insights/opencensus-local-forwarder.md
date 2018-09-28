---
title: Локальный сервер пересылки распределенной трассировки OpenCensus в Azure Application Insights | Документация Майкрософт
description: Пересылка распределенных трассировок и диапазонов OpenCensus из таких языков, как Python и Go, в Azure Application Insights
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: conceptual
ms.date: 09/18/2018
ms.reviewer: nimolnar
ms.author: mbullwin
ms.openlocfilehash: ddfcb90090d82d8fe947292737163a81c715b32d
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46972833"
---
# <a name="local-forwarder"></a>Локальный сервер пересылки

Локальный сервер пересылки — это агент, который собирает данные телеметрии Application Insights или [OpenCensus](https://opencensus.io/) из различных пакетов SDK и направляет их в Application Insights. Он может работать под управлением Windows и Linux. Также есть возможность запускать его в macOS, но официально в настоящее время это не поддерживается.

## <a name="running-local-forwarder"></a>Запуск локального сервера пересылки

Локальный сервер пересылки является [проектом с открытым кодом на GitHub](https://github.com/Microsoft/ApplicationInsights-LocalForwarder/releases). Есть много способов запуска локального сервера пересылки на нескольких платформах.

### <a name="windows"></a>Windows

#### <a name="windows-service"></a>Служба Windows

Самый простой способ запустить локальный сервер пересылки в Windows — установить его в качестве службы Windows. Выпуск поставляется с исполняемым файлом службы Windows (*WindowsServiceHost/Microsoft.LocalForwarder.WindowsServiceHost.exe*), который можно легко зарегистрировать в операционной системе.

> [!NOTE]
> Для службы локального сервера пересылки требуется как минимум .NET Framework 4.7. При отсутствии .NET Framework 4.7 служба установится, но не запустится. Чтобы получить доступ к последней версии платформы .NET Framework, **[откройте страницу загрузки .NET Framework](
https://www.microsoft.com/net/download/dotnet-framework-runtime/net472?utm_source=getdotnet&utm_medium=referral)**.

1. Скачайте файл LF.WindowsServiceHost.zip со [страницы выпуска локального сервера пересылки](https://github.com/Microsoft/ApplicationInsights-LocalForwarder/releases) на сайте GitHub.

    ![Снимок экрана страницы загрузки выпуска локального сервера пересылки](.\media\opencensus-local-forwarder\001-local-forwarder-windows-service-host-zip.png)

2. В этом примере для удобства демонстрации мы просто извлечем ZIP-файл в папку `C:\LF-WindowsServiceHost`.

    Чтобы зарегистрировать службу и настроить ее для запуска при загрузке системы, выполните следующую команду из командной строки от имени администратора:

    ```
    sc create "Local Forwarder" binpath="C:\LF-WindowsServiceHost\Microsoft.LocalForwarder.WindowsServiceHost.exe" start=auto
    ```
    
    Вы должны получить следующий ответ:
    
    `[SC] CreateService SUCCESS`
    
    Чтобы проверить новую службу с помощью графического интерфейса служб, введите команду ``services.msc``
        
     ![Снимок экрана службы локального сервера пересылки](.\media\opencensus-local-forwarder\002-services.png)

3. **Щелкните правой кнопкой мыши** новый локальный сервер пересылки и выберите **Запустить**. Служба перейдет в состояние выполнения.

4. По умолчанию служба создается без каких-либо действий для восстановления. Можно **щелкнуть правой кнопкой мыши** и последовательно выбрать **Свойства** > **Восстановление**, чтобы настроить автоматические реакции на сбой службы.

    Или, если вы предпочитаете задать параметры автоматического восстановления при сбое программным путем, можно воспользоваться следующей командой:

    ```
    sc failure "Local Forwarder" reset= 432000 actions= restart/1000/restart/1000/restart/1000
    ```

5. В том же расположении, где находится файл ``Microsoft.LocalForwarder.WindowsServiceHost.exe`` (в этом примере ``C:\LF-WindowsServiceHost``), содержится файл с именем ``LocalForwarder.config``. Это файл на основе XML, который позволяет настроить конфигурацию локального сервера пересылки и указать ключ инструментирования ресурса Application Insights, данные распределенной трассировки которого требуется пересылать. 

    После редактирования файла ``LocalForwarder.config`` для добавления ключа инструментирования не забудьте перезапустить **службу локального сервера пересылки**, чтобы изменения вступили в силу.
    
6. Чтобы убедиться, что требуемые настройки активированы и локальный сервер пересылки ожидает передачи данных трассировки надлежащим образом, проверьте файл ``LocalForwarder.log``. В нижней части файла должны отобразиться результаты, подобные приведенным ниже:

    ![Снимок экрана файла LocalForwarder.log](.\media\opencensus-local-forwarder\003-log-file.png)

#### <a name="console-application"></a>Консольное приложение

Для определенных вариантов использования, возможно, будет полезно запускать локальный сервер пересылки в качестве консольного приложения. Выпуск поставляется со следующими версиями исполняемого файла узла консоли:
* Зависящий от платформы .NET Core двоичный файл */ConsoleHost/publish/Microsoft.LocalForwarder.ConsoleHost.dll*. Для запуска этого двоичного файла нужно установить среду выполнения .NET Core (дополнительные сведения см. на [странице](https://www.microsoft.com/net/download/dotnet-core/2.1) скачивания).
```batchfile
E:\uncdrop\ConsoleHost\publish>dotnet Microsoft.LocalForwarder.ConsoleHost.dll
```
* Автономный набор двоичных файлов .NET Core для платформ x86 и x64. Для их запуска не требуется среда выполнения .NET Core. */ConsoleHost/win-x86/publish/Microsoft.LocalForwarder.ConsoleHost.exe*, */ConsoleHost/win-x64/publish/Microsoft.LocalForwarder.ConsoleHost.exe*.
```batchfile
E:\uncdrop\ConsoleHost\win-x86\publish>Microsoft.LocalForwarder.ConsoleHost.exe
E:\uncdrop\ConsoleHost\win-x64\publish>Microsoft.LocalForwarder.ConsoleHost.exe
```

### <a name="linux"></a>Linux

Как и для Windows, выпуск поставляется со следующими версиями исполняемого файла узла консоли:
* Зависящий от платформы .NET Core двоичный файл */ConsoleHost/publish/Microsoft.LocalForwarder.ConsoleHost.dll*. Для запуска этого двоичного файла нужно установить среду выполнения .NET Core (дополнительные сведения см. на [странице](https://www.microsoft.com/net/download/dotnet-core/2.1) скачивания).

```batchfile
dotnet Microsoft.LocalForwarder.ConsoleHost.dll
```

* Автономный набор двоичных файлов .NET Core для linux-64. Для его запуска не требуется среда выполнения .NET Core. */ConsoleHost/linux-x64/publish/Microsoft.LocalForwarder.ConsoleHost*.

```batchfile
user@machine:~/ConsoleHost/linux-x64/publish$ sudo chmod +x Microsoft.LocalForwarder.ConsoleHost
user@machine:~/ConsoleHost/linux-x64/publish$ ./Microsoft.LocalForwarder.ConsoleHost
```

Многим пользователям Linux, возможно, потребуется запускать локальный сервер пересылки в качестве управляющей программы. Системы Linux поставляются с различными решениями для управления службами, например Upstart, sysv или systemd. Независимо от конкретной версии с ее помощью можно запускать локальный сервер пересылки тем способом, который лучше всего подойдет для вашего сценария.

Например, давайте создадим службу управляющей программы с помощью systemd. Мы будем использовать версию, зависимую от платформы, но то же самое можно сделать и с помощью автономной версии.

* Создайте указанный ниже файл службы с именем *localforwarder.service* и поместите его в */lib/systemd/system*.
В этом примере предполагается, что ваше имя пользователя SAMPLE_USER и вы скопировали зависимые от платформы двоичные файлы с локального сервера пересылки (из */ConsoleHost/publish*) в */home/SAMPLE_USER/LOCALFORWARDER_DIR*.

```
# localforwarder.service
# Place this file into /lib/systemd/system/
# Use 'systemctl enable localforwarder' to start the service automatically on each boot
# Use 'systemctl start localforwarder' to start the service immediately

[Unit]
Description=Local Forwarder service
After=network.target
StartLimitIntervalSec=0

[Service]
Type=simple
Restart=always
RestartSec=1
User=SAMPLE_USER
WorkingDirectory=/home/SAMPLE_USER/LOCALFORWARDER_DIR
ExecStart=/usr/bin/env dotnet /home/SAMPLE_USER/LOCALFORWARDER_DIR/Microsoft.LocalForwarder.ConsoleHost.dll noninteractive

[Install]
WantedBy=multi-user.target
```

* Выполните следующую команду, чтобы дать указание systemd запускать локальный сервер пересылки при каждой загрузке:

```
systemctl enable localforwarder
```

* Выполните следующую команду, чтобы дать указание systemd запустить локальный сервер пересылки немедленно:

```
systemctl start localforwarder
```

* Отслеживайте службу, проверяя файлы **.log* в каталоге /home/SAMPLE_USER/LOCALFORWARDER_DIR.

### <a name="mac"></a>Mac
Локальный сервер пересылки может работать под управлением macOS, но сейчас это не поддерживается официально.

### <a name="self-hosting"></a>Автономное размещение
Локальный сервер пересылки также распространяется в виде пакета .NET Standard NuGet, что позволяет разместить его внутри вашего приложения .NET.

```C#
using Library;
...
Host host = new Host();

// see section below on configuring local forwarder
string configuration = ...;
    
host.Run(config, TimeSpan.FromSeconds(5));
...
host.Stop();
```

## <a name="configuring-local-forwarder"></a>Настройка локального сервера пересылки

* При выполнении одного из собственных узлов локального сервера пересылки (узла консоли или узла службы Windows) рядом с двоичным файлом можно найти файл **LocalForwarder.config**.
* При резидентном размещении локального сервера пересылки NuGet необходимо указать в коде конфигурацию такого же формата (см. раздел о размещении на собственном сервере). Синтаксис конфигурации см. в файле [LocalForwarder.config](https://github.com/Microsoft/ApplicationInsights-LocalForwarder/blob/master/src/ConsoleHost/LocalForwarder.config) в репозитории GitHub. 

> [!NOTE]
> Конфигурация может изменяться в зависимости от выпуска, поэтому обратите внимание на версию, которую используете.

## <a name="monitoring-local-forwarder"></a>Мониторинг локального сервера пересылки

Трассировки записываются в файловую систему рядом с исполняемым файлом, который запускает локальный сервер пересылки (это файлы **.log*). Можно разместить файл с именем *NLog.config* рядом с исполняемым файлом, чтобы указать собственную конфигурацию вместо заданной по умолчанию. Описание формата см. в [документации](https://github.com/NLog/NLog/wiki/Configuration-file#configuration-file-format).

Если файл конфигурации не указан (что является настройкой по умолчанию), локальный сервер пересылки будет использовать конфигурацию по умолчанию, которую можно найти [здесь](https://github.com/Microsoft/ApplicationInsights-LocalForwarder/blob/master/src/Common/NLog.config).

## <a name="next-steps"></a>Дополнительная информация

* [Open Census](https://opencensus.io/)
