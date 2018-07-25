---
title: Работа с несколькими модулями Azure IoT Edge в VS Code | Документация Майкрософт
description: Использование расширения Интернета вещей в Visual Studio Code для одновременной разработки нескольких модулей Azure IoT Edge
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 06/27/2018
ms.topic: conceptual
ms.service: iot-edge
ms.openlocfilehash: 31fe210b87a052438956d813db0d104e0f2cdb6e
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/14/2018
ms.locfileid: "39041263"
---
# <a name="develop-an-iot-edge-solution-with-multiple-modules-in-visual-studio-code"></a>Разработка решения IoT Edge с несколькими модулями в Visual Studio Code

Visual Studio Code можно использовать для разработки решения Azure IoT Edge с несколькими модулями. В этой статье показано, как с помощью VS Code создать, обновить и развернуть решение IoT Edge для передачи данных с датчиков на имитированное устройство IoT Edge. 

## <a name="prerequisites"></a>Предварительные требования

Чтобы выполнить действия, описанные в этой статье, необходимо следующее:

- [Visual Studio Code](https://code.visualstudio.com/)
- [расширение Azure IoT Edge для Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge);
- [расширение C# для Visual Studio Code (на платформе OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp);
- [Docker](https://docs.docker.com/engine/installation/)
- [пакет SDK для .NET Core 2.1](https://www.microsoft.com/net/download);
- активный Центр Интернета вещей с устройством IoT Edge (как минимум).

Вам также понадобится средство [Docker для VS Code](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker), интегрированное с обозревателем устройств Azure IoT Hub для управления образами и контейнерами.

## <a name="create-your-iot-edge-solution"></a>Создание решения IoT Edge

1. В Visual Studio Code откройте интегрированный терминал, последовательно выбрав элементы **Вид** > **Интегрированный терминал**. 

1. В **палитре команд** VS Code введите и выполните команду **Azure IoT Edge: New IoT Edge solution**. Выберите папку рабочей области и укажите имя решения (по умолчанию используется EdgeSolution). Создайте модуль C# с именем **PipeModule**, который станет первым пользовательским модулем в этом решении. Шаблон модуля C# по умолчанию — это модуль канала, который передает сообщения непосредственно из вышестоящего объекта в подчиненный объект. Кроме того, для первого модуля следует указать репозиторий образов Docker. По умолчанию используется репозиторий образов на базе локального реестра Docker (**localhost:5000/<first module name>**). Вместо него можно использовать реестр контейнеров Azure или Docker Hub. 

2. Окно VS Code загружает рабочую область решения IoT Edge. Корневая папка содержит папку **modules**, папку **.vscode** и файл шаблона с манифестом развертывания. Конфигурации отладки хранятся в папке .vscode. Весь код пользовательских модулей находится во вложенных папках модулей. Файл deployment.template.json представляет собой шаблон манифеста развертывания. Для некоторых параметров в этом файле значения извлекаются из файла module.json, который есть в папке каждого модуля.

3. Добавьте второй модуль в этот проект решения. Существует несколько способов добавления нового модуля в используемое решение. Введите и выполните команду **Azure IoT Edge: Add IoT Edge module**. Выберите файл шаблона развертывания, который вы намерены обновить. Также можно щелкнуть правой кнопкой мыши папку модулей или файл deployment.template.json и выбрать пункт **Добавить модуль IoT Edge**. После этого в раскрывающемся списке можно выбрать тип модуля. Выберите модуль типа **Azure Functions - C#** (Функции Azure — C#) с названием **PipeFunction** и соответствующий репозиторий образов Docker. Шаблон модуля функций C# по умолчанию — это модуль канала, который передает сообщения непосредственно из вышестоящего объекта в подчиненный объект.

4. Откройте файл deployment.template.json. Убедитесь, что файл объявляет три модуля и среду выполнения. Сообщение создается из модуля tempSensor. Сообщение передается по конвейеру напрямую в модули SampleModule и SampleFunction, а затем отправляется в Центр Интернета вещей. 

5. Измените маршруты для этих модулей, используя приведенное ниже содержимое.

   ```json
        "routes": {
          "SensorToPipeModule": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/PipeModule/inputs/input1\")",
          "PipeModuleToPipeFunction": "FROM /messages/modules/PipeModule/outputs/output1 INTO BrokeredEndpoint(\"/modules/PipeFunction/inputs/input1\")",
          "PipeFunctionToIoTHub": "FROM /messages/modules/PipeFunction/outputs/output1 INTO $upstream"
        },
   ```

5. Сохраните этот файл.

## <a name="build-and-deploy-your-iot-edge-solution"></a>Сборка и развертывание решения IoT Edge

1. В **палитре команд** VS Code введите и выполните команду **Azure IoT Edge: Build IoT Edge solution**. Используя файлы module.json в каждой папке модуля, эта команда выполняет сборку, контейнеризацию и отправку образов Docker для каждого модуля. Затем команда передает требуемое значение в файл deployment.template.json и создает файл deployment.json с информацией из папки config. Во встроенном терминале VS Code можно отслеживать ход сборки. 

2. В **обозревателе устройств** Центра Интернета вещей Azure щелкните правой кнопкой мыши идентификатор устройства IoT Edge и выберите **Create deployment for Edge device** (Создать развертывание для устройства Edge). Выберите файл deployment.json в папке config. Встроенный терминал в VS Code отображает успешное завершение развертывания и идентификатор этого развертывания.

3. Если вы создаете имитацию устройства IoT Edge на компьютере разработки, вы увидите, как через несколько минут на нем будут запущены контейнеры образов модулей.

## <a name="view-the-generated-data"></a>Просмотр созданных данных

1. Для просмотра данных, поступающих в Центр Интернета вещей, выберите **Представление** > **Палитра команд**. Затем выберите команду **IoT: Start monitoring D2C message**. 
2. Чтобы прекратить мониторинг данных, используйте команду **IoT: Stop monitoring D2C message** на **палитре команд**. 

## <a name="next-steps"></a>Дополнительная информация

Изучите другие сценарии разработки для Azure IoT Edge в VS Code:

* Разработка модулей в VS Code с помощью [C#](how-to-develop-csharp-module.md) или [Node.js](how-to-develop-node-module.md).
* Разработка Функций Azure в VS Code с помощью [C#](how-to-develop-csharp-function.md).

Чтобы разрабатывать модули для устройств IoT Edge, см. раздел [Понимание и использование пакетов SDK для Центра Интернета вещей Azure](../iot-hub/iot-hub-devguide-sdks.md).