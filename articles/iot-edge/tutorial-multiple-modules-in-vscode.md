---
title: Управление несколькими модулями Azure IoT Edge в VS Code | Документация Майкрософт
description: Использование Visual Studio Code для разработки решений Azure IoT Edge, использующих несколько модулей.
author: shizn
manager: ''
ms.author: xshi
ms.date: 03/18/2018
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 4a624994f93e7a3cbb04db2a0ec0b2b823a12ee7
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/05/2018
ms.locfileid: "34763040"
---
# <a name="develop-an-iot-edge-solution-with-multiple-modules-in-visual-studio-code-preview"></a>Разработка решения IoT Edge с несколькими модулями в Visual Studio Code (предварительная версия)

Visual Studio Code можно использовать для разработки решения Azure IoT Edge с несколькими модулями. В этой статье показано, как с помощью VS Code создать, обновить и развернуть решение IoT Edge для передачи данных с датчиков на имитированное устройство IoT Edge. 

## <a name="prerequisites"></a>предварительным требованиям

Чтобы выполнить действия, описанные в этой статье, необходимо следующее:

- [Visual Studio Code](https://code.visualstudio.com/)
- [расширение Azure IoT Edge для Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge);
- [расширение C# для Visual Studio Code (на платформе OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp);
- [Docker](https://docs.docker.com/engine/installation/)
- [пакет SDK для .NET Core 2.0](https://www.microsoft.com/net/core#windowscmd);
- шаблон AzureIoTEdgeModule (`dotnet new -i Microsoft.Azure.IoT.Edge.Module`);
- активный Центр Интернета вещей с устройством IoT Edge (как минимум).

Вам также понадобится средство [Docker для VS Code](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker), интегрированное с обозревателем устройств Azure IoT Hub для управления образами и контейнерами.

## <a name="prepare-your-first-iot-edge-solution"></a>Подготовка первого решения IoT Edge

1. В **палитре команд** VS Code введите и выполните команду **Edge: New IoT Edge solution**. Выберите папку рабочей области и укажите имя решения (по умолчанию используется EdgeSolution). Создайте модуль C# с именем **SampleModule**, который будет первым пользовательским модулем в этом решении. Кроме того, для первого модуля следует указать репозиторий образов Docker. По умолчанию используется репозиторий образов на базе локального реестра Docker (**localhost:5000/<first module name>**). Вместо него можно использовать реестр контейнеров Azure или Docker Hub.

   > [!NOTE]
   > Если вы используете локальный реестр Docker, убедитесь, что этот реестр запущен. Введите в окне консоли следующую команду:
   > 
   > `docker run -d -p 5000:5000 --restart=always --name registry registry:2`

2. Окно VS Code загружает рабочую область решения IoT Edge. Корневая папка содержит папку **modules**, папку **.vscode** и файл шаблона с манифестом развертывания. Конфигурации отладки хранятся в папке .vscode. Весь код пользовательских модулей находится во вложенных папках модулей. Файл deployment.template.json представляет собой шаблон манифеста развертывания. Для некоторых параметров в этом файле значения извлекаются из файла module.json, который есть в папке каждого модуля.

3. Добавьте второй модуль в этот проект решения. Введите и выполните команду **Edge: Add IoT Edge module**. Выберите файл шаблона развертывания, который вы намерены обновить. Выберите модуль **Azure Function — C#** (Функция Azure на C#) с именем **SampleFunction**, а также соответствующий репозиторий образов Docker.

4. Откройте файл deployment.template.json. Убедитесь, что файл объявляет три модуля и среду выполнения. Сообщение создается из модуля tempSensor. Сообщение передается по конвейеру напрямую в модули SampleModule и SampleFunction, а затем отправляется в Центр Интернета вещей. 

5. Измените маршруты для этих модулей, используя приведенное ниже содержимое.

   ```json
   "routes": {
      "SensorToPipeModule": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/SampleModule/inputs/input1\")",
      "PipeModuleToPipeFunction": "FROM /messages/modules/SampleModule/outputs/output1 INTO BrokeredEndpoint(\"/modules/SampleFunction/inputs/input1\")",
      "PipeFunctionToIoTHub": "FROM /messages/modules/SampleFunction/outputs/output1 INTO $upstream"
   },
   ```

6. Сохраните этот файл.

## <a name="build-and-deploy-your-iot-edge-solution"></a>Сборка и развертывание решения IoT Edge

1. В **палитре команд** VS Code введите и выполните команду **Edge: Build IoT Edge solution**. Используя файлы module.json в каждой папке модуля, эта команда выполняет сборку, контейнеризацию и отправку образов Docker для каждого модуля. Затем команда передает требуемое значение в файл deployment.template.json и создает файл deployment.json с информацией из папки config. Во встроенном терминале VS Code можно отслеживать ход сборки.

2. В **обозревателе устройств** Центра Интернета вещей Azure щелкните правой кнопкой мыши идентификатор устройства IoT Edge и выберите **Create deployment for Edge device** (Создать развертывание для устройства Edge). Выберите файл deployment.json в папке config. Встроенный терминал в VS Code отображает успешное завершение развертывания и идентификатор этого развертывания.

3. Если вы создаете имитацию устройства IoT Edge на компьютере разработки, через несколько минут на нем будут запущены контейнеры образов модулей.

## <a name="view-the-generated-data"></a>Просмотр созданных данных

1. Для просмотра данных, поступающих в Центр Интернета вещей, выберите **Представление** > **Палитра команд**. Затем выберите команду **IoT: Start monitoring D2C message**. 
2. Чтобы прекратить мониторинг данных, используйте команду **IoT: Stop monitoring D2C message** на **палитре команд**. 

## <a name="next-steps"></a>Дополнительная информация

Изучите другие сценарии разработки для Azure IoT Edge в VS Code:

* [Отладка модуля C# в VS Code](how-to-vscode-debug-csharp-module.md)
* [Отладка функции C# в VS Code](how-to-vscode-debug-azure-function.md)
