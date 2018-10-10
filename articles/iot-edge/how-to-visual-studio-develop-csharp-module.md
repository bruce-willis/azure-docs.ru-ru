---
title: Разработка и отладка модулей C# для Azure IoT Edge в Visual Studio 2017 | Документация Майкрософт
description: Сведения об использовании Visual Studio 2017 для разработки и отладки модулей C# для Azure IoT Edge
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 09/24/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 9cc8e1db577859ad7637902a5ccd5a044efcd033
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46978528"
---
# <a name="use-visual-studio-2017-to-develop-and-debug-c-modules-for-azure-iot-edge-preview"></a>Сведения об использовании Visual Studio 2017 для разработки и отладки модулей C# для Azure IoT Edge (предварительная версия)

Вы можете превратить бизнес-логику в модули для Azure IoT Edge. В этой статье описывается, как использовать Visual Studio 2017 в качестве основного средства разработки и отладки модулей C#.

Средства Azure IoT Edge для Visual Studio обеспечивают следующие преимущества:

- создание, изменение, сборку, запуск и отладку решений и модулей Azure IoT Edge на локальном компьютере для разработки;
- развертывание решения Azure IoT Edge на устройстве Azure IoT Edge через Центр Интернета вещей;
- программирование модулей Интернета вещей Azure на C#, сохраняя при этом все преимущества разработки в Visual Studio;
- управление устройствами и модулями Azure IoT Edge с помощью пользовательского интерфейса. 

В этой статье объясняется, как использовать средства Azure IoT Edge для Visual Studio 2017 для разработки модулей IoT Edge на C#. Вы также узнаете, как развернуть свой проект на устройство Azure IoT Edge.

## <a name="prerequisites"></a>Предварительные требования
В этой статье предполагается, что для разработки вы используете компьютер или виртуальную машину под управлением Windows. Устройство IoT Edge может быть другим физическим устройством.

Так как в этой статье в качестве основного средства разработки используется Visual Studio 2017, установите Visual Studio. Включите **рабочую нагрузку разработки Azure** в своей установке Visual Studio 2017. Вы можете [изменить Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/modify-visual-studio?view=vs-2017) и добавить рабочую нагрузку разработки Azure.

После подготовки Visual Studio 2017 вам понадобится:

- Скачайте и установите [расширение Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsc-iot.azureiotedgetools) из Visual Studio Marketplace для создания проекта IoT Edge в Visual Studio 2017.
- [Docker Community Edition](https://docs.docker.com/install/) на компьютере разработки для сборки и запуска образов модуля. Вам необходимо правильно настроить Docker CE в режиме контейнера в Linux или Windows.
- Чтобы настроить локальную среду разработки для отладки, запуска и тестирования решения IoT Edge, требуется [средство разработки Azure IoT EdgeHub](https://pypi.org/project/iotedgehubdev/). Установите [Python (2.7/3.6) и Pip](https://www.python.org/). Затем установите **iotedgehubdev**, выполнив следующую команду в окне терминала. Используйте версию средства разработки Azure IoT EdgeHub выше версии 0.3.0.

   ```cmd
   pip install --upgrade iotedgehubdev
   ```

- [Реестр контейнеров Azure](https://docs.microsoft.com/azure/container-registry/) или [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags).
   - Вместо облачного реестра можно использовать локальный реестр Docker для создания прототипов и тестирования. 

- Для тестирования модуля требуется активный Центр Интернета вещей по крайней мере с одним созданным идентификатором устройства IoT Edge. Если вы запустили управляющую программу безопасности IoT Edge на компьютере разработки, может потребоваться остановить EdgeHub и EdgeAgent, прежде чем запустить разработку в Visual Studio. 

### <a name="check-your-tools-version"></a>Проверка версии инструментов

1. В меню **Сервис** выберите пункт **Расширения и обновления**. Разверните **Установленные > Средства** и найдите **Azure IoT Edge** и **Cloud Explorer**.

2. Запишите номер установленной версии. Вы можете сравнить эту версию с последней версией в Visual Studio Marketplace ([Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS), [Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsc-iot.azureiotedgetools)).

3. Если вы используете более раннюю версию, обновите инструменты в Visual Studio, как показано в следующем разделе.

### <a name="update-your-tools"></a>Обновление инструментов

1. В диалоговом окне **Расширения и обновления** последовательно выберите **Обновления > Visual Studio Marketplace**, **Azure IoT Edge** или **Cloud Explorer**, а затем щелкните **Обновить**.

2. Скачав обновления для инструментов, закройте Visual Studio, чтобы активировать обновления с помощью установщика VSIX.

3. В установщике нажмите кнопку **ОК**, чтобы начать запуск, и выберите "Изменить" для обновления инструментов.

4. Когда обновление завершится, нажмите кнопку "Закрыть" и перезапустите Visual Studio.

### <a name="create-an-azure-iot-edge-project"></a>Создание проекта Azure IoT Edge

Шаблон проекта Azure IoT Edge в Visual Studio создает проект, который можно развернуть на устройства Azure IoT Edge в Центре Интернета вещей. Сначала нужно создать решение Azure IoT Edge, а затем сформировать в нем первый модуль C#. Каждое решение IoT Edge может содержать несколько модулей. 

1. В Visual Studio в меню **Файл** выберите **Создать** > **Проект**.

2. В диалоговом окне **Новый проект** выберите **Установленные**, разверните узел **Visual C# > Облако**, выберите **Azure IoT Edge**, укажите **имя** проекта и расположение, а затем нажмите кнопку **ОК**. Имя проекта по умолчанию — **AzureIoTEdgeApp1**. 

   ![Новый проект](./media/how-to-visual-studio-develop-csharp-module/create-new.jpg)

3. В окне **конфигурации модуля IoT Edge** выберите тип **модуля C#** и укажите имя модуля и репозиторий образа модуля.  VS автоматически заполняет имя модуля значением **localhost:5000**. Замените его собственными данными реестра. Если для тестирования вы используете локальный реестр Docker, вполне подойдет значение localhost. Если используется Реестр контейнеров Azure, укажите сервер входа, заданный в параметрах реестра. Значение для сервера входа выглядит так: **<registry name>.azurecr.io**. Замените только часть строки localhost, не удаляйте имя модуля. Имя модуля по умолчанию — **IoTEdgeModule1**.

4. Нажмите кнопку **ОК**, чтобы создать проект Azure IoT Edge с использованием модуля C#.

Теперь в решении есть проекты **AzureIoTEdgeApp1** и **IoTEdgeModule1**. Проект **AzureIoTEdgeApp1** включает файл **deployment.template.json**. Этот файл определяет модули, которые вы хотите создать и развернуть для решения IoT Edge, и также маршруты между ними. Решение по умолчанию включает модули **tempSensor** и **IoTEdgeModule1**. Модуль **tempSensor** генерирует смоделированные данные в модуль **IoTEdgeModule1**, а код по умолчанию в модуле **IoTEdgeModule1** представляет собой модуль сообщений канала, который напрямую передает полученные сообщения в Центр Интернета вещей.

Проект **IoTEdgeModule1** представляет собой консольное приложение .Net Core 2.1. Он содержит обязательные файлы **Dockerfile**, необходимые для вашего устройства IoT Edge, работающего с контейнером Windows или Linux. В файле **module.json** описываются метаданные модуля. Файл **program.cs** — это фактический код модуля, который использует пакет SDK для устройств Azure IoT в качестве зависимости.

## <a name="develop-your-module"></a>Разработка модуля

Код модуля C# по умолчанию, который поставляется вместе с решением, находится здесь: **IoTEdgeModule1** > **Program.cs**. Модуль и файл deployment.template.json настраиваются так, чтобы можно было собрать решение, передать его в реестр контейнеров и развернуть на устройстве, чтобы начать тестирование, не меняя код. Модуль просто принимает входные данные из источника (в данном случае модуль tempSensor имитирует данные) и передает их в Центр Интернета вещей. 

Когда вы будете готовы настроить шаблон C# с добавлением собственного кода, используйте [пакеты SDK для Центра Интернета вещей Azure](../iot-hub/iot-hub-devguide-sdks.md), чтобы создать модули, которые удовлетворяют ключевые потребности решений Интернета вещей, такие как безопасность, управление устройствами и надежность. 

## <a name="build-and-debug-single-c-module"></a>Создание и отладка единого модуля C#

Как правило, мы хотим выполнить тестирование и отладку каждого модуля, прежде чем запустить его в рамках целого решения с несколькими модулями.

1. Выберите **IoTEdgeModule1** в качестве запускаемого проекта в контекстном меню.

   ![Задание запускаемого проекта](./media/how-to-visual-studio-develop-csharp-module/module-start-up-project.png)

2. Нажмите клавишу **F5** или кнопку ниже, чтобы запустить модуль. Это может занять от 10 до 20 секунд в первый раз.

   ![Запуск модуля](./media/how-to-visual-studio-develop-csharp-module/run-module.png)


3. В случае успешной инициализации модуля вы должны увидеть запущенное консольное приложение .Net Core.

   ![Запущенный модуль](./media/how-to-visual-studio-develop-csharp-module/single-module-run.png)

4. Теперь вы можете установить точку останова в **PipeMessage** в **Program.cs**, а затем отправить сообщение, выполнив следующую команду в **Git Bash** или **WSL Bash** (не запускайте ее в CMD или Powershell). (Вы также можете найти эту команду в окне вывода VS.)

    ```cmd
    curl --header "Content-Type: application/json" --request POST --data '{"inputName": "input1","data":"hello world"}' http://localhost:53000/api/v1/messages
    ```

   ![Отладка одного модуля](./media/how-to-visual-studio-develop-csharp-module/debug-single-module.png)

    Точка останова должна быть активирована. Вы можете просмотреть переменные в окне локальных элементов Visual Studio.

   > [!TIP]
   > Вместо `curl` для отправки сообщений можно использовать [PostMan](https://www.getpostman.com/) или другие средства API.

5. Нажмите клавиши **CTRL + F5** или кнопку "Остановить", чтобы остановить отладку.

## <a name="build-and-debug-iot-edge-solution-with-multiple-modules"></a>Создание и отладка решения IoT Edge с несколькими модулями

После разработки единого модуля далее нужно запустить и отладить все решение с несколькими модулями.

1. Добавьте свой второй модуль C# в решение. Щелкните правой кнопкой мыши **AzureIoTEdgeApp1** и выберите **Добавить** -> **New IoT Edge Module** (Новый модуль IoT Edge). Стандартное имя второго модуля — **IoTEdgeModule2**, и он по-прежнему является модулем канала.

2. Перейдите в файл **deployment.template.json**. Вы увидите модуль **IoTEdgeModule2**, добавленный в раздел **модулей**. Замените раздел **routes** следующим кодом: Если вы настроили имена модулей, обновите их в коде ниже после замены.

    ```json
        "routes": {
          "IoTEdgeModule1ToIoTHub": "FROM /messages/modules/IoTEdgeModule1/outputs/* INTO $upstream",
          "sensorToIoTEdgeModule1": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/IoTEdgeModule1/inputs/input1\")",
          "IoTEdgeModule2ToIoTHub": "FROM /messages/modules/IoTEdgeModule2/outputs/* INTO $upstream",
          "sensorToIoTEdgeModule2": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/IoTEdgeModule2/inputs/input1\")"
        },
    ```

3. Задайте проект **AzureIoTEdgeApp1** как запускаемый проект в контекстном меню.

4. Установите точки останова и нажмите клавишу F5. Затем вы можете запускать и отлаживать несколько модулей одновременно. Вы должны увидеть несколько окон консольного приложения .Net Core, каждое окно указывает модуль C#. 

   ![Отладка нескольких модулей](./media/how-to-visual-studio-develop-csharp-module/debug-multiple-modules.png)

5. Нажмите клавиши **CTRL + F5** или кнопку "Остановить", чтобы остановить отладку.

## <a name="build-and-push-images"></a>Создание и отправка образов

1. Задайте **AzureIoTEdgeApp1** как запускаемый проект. Выберите конфигурацию **отладки** или **выпуска** для образов модуля, которые нужно создать.

    > [!NOTE]
    > В случае выбора **отладки** VS будет использовать `Dockerfile.debug` для сборки образов Docker. Это включает отладчик командной строки .Net Core VSDBG в образе контейнера при его создании. Мы рекомендуем выбрать конфигурацию **выпуска**, которая использует `Dockerfile` без VSDBG для модулей IoT Edge, готовых для рабочей среды.

2. Если вы используете частный реестр, например Реестр контейнеров Azure, выполните вход в Docker, запустив следующую команду в терминале. Если вы используете локальный реестр, вы можете выполнить команду [запуска локального реестра](https://docs.docker.com/registry/deploying/#run-a-local-registry).

    ```cmd
    docker login -u <ACR username> -p <ACR password> <ACR login server> 
    ```

3. Если вы используете частный реестр, например Реестр контейнеров Azure, вам нужно указать имя пользователя и пароль реестра в файле `deployment.template.json` в параметрах среды выполнения вместе со следующим содержимым. Замените заполнитель фактическим именем пользователя и паролем администратора.

    ```json
          "settings": {
            "minDockerVersion": "v1.25",
            "loggingOptions": "",
            "registryCredentials": {
              "registry1": {
                "username": "<username>",
                "password": "<password>",
                "address": "<registry name>.azurecr.io"
              }
            }
          }
    ```

4. Щелкните правой кнопкой мыши **AzureIoTEdgeApp1** и выберите пункт контекстного меню **Build and Push Edge Solution** (Создать и отправить решение Edge). В результате для каждого модуля будет создан и отправлен образ Docker.

   ![Создание и отправка образов](./media/how-to-visual-studio-develop-csharp-module/build-and-push.png)


## <a name="deploy-the-solution"></a>Развертывание решения

При использовании руководства по настройке устройства IoT Edge модуль был развернут с помощью портала Azure. Вы также можете развернуть модули с помощью Cloud Explorer для Visual Studio. У вас уже есть манифест развертывания, подготовленный для вашего сценария, — это файл `deployment.json`. Теперь вам осталось выбрать устройство для получения развертывания.

1. Откройте **Cloud Explorer**, щелкнув **Просмотр** > **Cloud Explorer**. Войдите в Visual Studio 2017 с использованием своей учетной записи.

2. В **Cloud Explorer** разверните подписку, найдите Центр Интернета вещей Azure и устройство Azure IoT Edge, которое нужно развернуть.

3. Щелкните правой кнопкой мыши устройство IoT Edge, чтобы создать для него развертывание. Выберите файл манифеста развертывания: `$AzureIoTEdgeApp1\config\Debug|Release\deployment.json`.

4. Нажмите кнопку "Обновить". Появятся новые модули, работающие вместе с модулем **TempSensor**, а также **$edgeAgent** и **$edgeHub**.

## <a name="view-generated-data"></a>Просмотр сформированных данных

1. Для отслеживания сообщений D2C для конкретного устройства щелкните его кнопкой мыши в списке и выберите **Start Monitoring D2C Messages** (Начать мониторинг сообщений D2C) в окне **Действие**.

2. Чтобы завершить отслеживание данных, щелкните устройство в списке и выберите **Stop Monitoring D2C Messages** (Завершить мониторинг сообщений D2C) в окне **Действие**.

## <a name="next-steps"></a>Дополнительная информация

Чтобы разрабатывать модули для устройств IoT Edge, см. раздел [Понимание и использование пакетов SDK для Центра Интернета вещей Azure](../iot-hub/iot-hub-devguide-sdks.md).
