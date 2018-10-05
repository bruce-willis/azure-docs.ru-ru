---
title: Развертывание службы "Функции Azure" с помощью Azure IoT Edge | Документация Майкрософт
description: В этом руководстве выполняется развертывание службы "Функции Azure" в виде модуля на граничном устройстве.
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 09/21/2018
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 66dcb3347741c4826fc62cc258c67b85f3939ff7
ms.sourcegitcommit: 42405ab963df3101ee2a9b26e54240ffa689f140
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/28/2018
ms.locfileid: "47422768"
---
# <a name="tutorial-deploy-azure-functions-as-iot-edge-modules-preview"></a>Руководство по развертыванию службы "Функции Azure" в качестве модуля IoT Edge (предварительная версия)

Службу "Функции Azure" можно использовать для развертывания кода, который реализует нужную бизнес-логику, непосредственно на устройствах Azure IoT Edge. В этом руководстве рассматривается создание и развертывание службы "Функции Azure", которая фильтрует данные датчика на имитированном устройстве IoT Edge. Вы используете имитированное устройство IoT Edge, созданное при работе с руководством по развертыванию Azure IoT Edge, на имитированном устройстве в [Windows][lnk-tutorial1-win] или [Linux][lnk-tutorial1-lin]. Из этого руководства вы узнаете, как выполнять следующие задачи:     

> [!div class="checklist"]
> * Использовать Visual Studio Code для создания функции Azure.
> * Использовать VS Code и Docker для создания образа Docker и его публикации в реестре контейнеров.
> * Развертывать модуль из реестра контейнеров на устройстве IoT Edge.
> * Просматривать отфильтрованные данные.

<center>
![Схема архитектуры, используемая в руководстве](./media/tutorial-deploy-function/FunctionsTutDiagram.png)
</center>

>[!NOTE]
>Модули службы "Функции Azure", используемые в Azure IoT Edge, находятся на стадии [общедоступной предварительной версии](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

Функция Azure, создаваемая в этом руководстве, фильтрует данные температуры, созданные устройством. Она отправляет сообщения в вышестоящий Центр Интернета вещей Azure, когда температура превышает заданное пороговое значение. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Предварительные требования

Устройство Azure IoT Edge.

* В качестве устройства Azure IoT Edge можно использовать компьютер, на котором ведется разработка, или виртуальную машину. Для этого выполните действия, описанные в кратком руководстве для устройств [Linux](quickstart-linux.md) или [Windows](quickstart.md).

Облачные ресурсы.

* [Центр Интернета вещей](../iot-hub/iot-hub-create-through-portal.md) (цен. категории "Бесплатный") в Azure. 

Ресурсы разработки.

* [Visual Studio Code](https://code.visualstudio.com/). 
* [C# для расширения Visual Studio Code (на платформе OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).
* [Расширение Azure IoT Edge для Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge). 
* [пакет SDK для .NET Core 2.1](https://www.microsoft.com/net/download).
* [Docker CE](https://docs.docker.com/install/). 

## <a name="create-a-container-registry"></a>Создание реестра контейнеров

В этом руководстве вы используете расширение Azure IoT Edge для Visual Studio Code, чтобы создать модуль, и создадите **образ контейнера** из файлов. Затем вы отправите этот образ в **реестр**, содержащий ваши образы и управляющий ими. Наконец, вы развернете свой образ из реестра для выполнения на устройстве IoT Edge.  

Для работы с этим руководством можно использовать любые реестры, совместимые с Docker. Две популярные службы реестров Docker, доступные в облаке — [Реестр контейнеров Azure](https://docs.microsoft.com/azure/container-registry/) и [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). В этом руководстве используется реестр контейнеров Azure. 

1. На [портале Azure](https://portal.azure.com) выберите **Создать ресурс** > **Контейнеры** > **Реестр контейнеров**.

    ![Создание реестра контейнеров](./media/tutorial-deploy-function/create-container-registry.png)

2. Предоставьте следующие значения для создания реестра контейнеров:

   | Поле | Значение | 
   | ----- | ----- |
   | Имя реестра | Укажите уникальное имя. |
   | Подписка | Из раскрывающегося списка выберите подписку. |
   | Группа ресурсов | Мы рекомендуем использовать одну группу ресурсов для всех тестовых ресурсов, которые вы создаете во время работы с краткими руководствами по IoT Edge. Например, **IoTEdgeResources**. |
   | Расположение | Выберите расположение рядом с вами. |
   | Пользователь-администратор | Установите значение **Включить**. |
   | SKU | Выберите **Базовый**. | 

5. Нажмите кнопку **Создать**.

6. После создания реестра контейнеров перейдите к нему, а затем выберите **Ключи доступа**. 

7. Скопируйте значения **Сервер входа**, **Имя пользователя** и **Пароль**. Эти значения при работе с этим руководством понадобятся позже. 

## <a name="create-a-function-project"></a>Создание проекта функции

Расширение Azure IoT Edge для Visual Studio Code, установленное в качестве предварительных требований, предоставляет возможности управления, а также некоторые шаблоны кода. В этом разделе Visual Studio Code используется для создания решения IoT Edge, содержащего функцию Azure. 

1. Откройте Visual Studio Code на компьютере для разработки.

2. Выберите **Представление** > **Палитра команд** для открытия палитры команд VS Code.

3. В палитре команд введите и выполните команду **Azure: Вход**. Следуйте инструкциям, чтобы войти в свою учетную запись Azure.

4. В палитре команд введите и выполните команду **Azure IoT Edge: New IoT Edge solution**. Следуйте инструкциям на экране в палитре команд для создания решения.

   1. Выберите папку, где требуется создать решение. 
   2. Введите имя своего решения или примите имя по умолчанию **EdgeSolution**.
   3. Выберите **Azure Functions — C#** (Функции Azure — C#) как шаблон модуля. 
   4. Назовите модуль **CSharpFunction**. 
   5. В качестве репозитория образов первого модуля необходимо указать реестр контейнеров Azure, который был создан в предыдущем разделе. Замените **localhost:5000** скопированным значением имени входа на сервер. Последняя строка выглядит следующим образом: \<имя реестра\>.azurecr.io/csharpfunction.

   ![Выбор репозитория образа Docker](./media/tutorial-deploy-function/repository.png)

4. В окне VS Code будет загружена рабочая область решения IoT Edge, которая состоит из папки \.vscode, папки модулей, файла шаблона манифеста развертывания и файла \.env. В обозревателе VS Code откройте **Модули** > **CSharpFunction** > **EdgeHubTrigger Csharp** > **run.csx**.

5. Замените содержимое файла **run.csx** следующим кодом:

   ```csharp
   #r "Microsoft.Azure.Devices.Client"
   #r "Newtonsoft.Json"

   using System.IO;
   using Microsoft.Azure.Devices.Client;
   using Newtonsoft.Json;

   // Filter messages based on the temperature value in the body of the message and the temperature threshold value.
   public static async Task Run(Message messageReceived, IAsyncCollector<Message> output, TraceWriter log)
   {
        const int temperatureThreshold = 25;
        byte[] messageBytes = messageReceived.GetBytes();
        var messageString = System.Text.Encoding.UTF8.GetString(messageBytes);

        if (!string.IsNullOrEmpty(messageString))
        {
            // Get the body of the message and deserialize it.
            var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

            if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
            {
                // Send the message to the output as the temperature value is greater than the threashold.
                var filteredMessage = new Message(messageBytes);
                // Copy the properties of the original message into the new Message object.
                foreach (KeyValuePair<string, string> prop in messageReceived.Properties)
                {
                    filteredMessage.Properties.Add(prop.Key, prop.Value);                }
                // Add a new property to the message to indicate it is an alert.
                filteredMessage.Properties.Add("MessageType", "Alert");
                // Send the message.       
                await output.AddAsync(filteredMessage);
                log.Info("Received and transferred a message with temperature above the threshold");
            }
        }
    }

    //Define the expected schema for the body of incoming messages.
    class MessageBody
    {
        public Machine machine {get; set;}
        public Ambient ambient {get; set;}
        public string timeCreated {get; set;}
    }
    class Machine
    {
       public double temperature {get; set;}
       public double pressure {get; set;}         
    }
    class Ambient
    {
       public double temperature {get; set;}
       public int humidity {get; set;}         
    }
   ```

6. Сохраните файл.

## <a name="build-your-iot-edge-solution"></a>Сборка решения IoT Edge

В предыдущем разделе было создано решение IoT Edge и добавлен код в **CSharpFunction**, который будет фильтровать сообщения, в которых температура компьютера ниже допустимого порогового значения. Теперь необходимо создать решение в качестве образа контейнера и передать его в реестр контейнеров.

В этом разделе вы дважды предоставляете учетные данные для реестра контейнеров. Первый раз при локальном входе со своего компьютера для разработки, чтобы редактор Visual Studio Code мог отправлять образы в реестр. Второй — в **ENV**-файле решения IoT Edge, за счет чего устройство IoT Edge получает разрешения на извлечение образов из вашего реестра. 

1. Откройте интегрированный терминал VS Code, выбрав **Вид** > **Терминал**. 

1. Войдите в свой реестр контейнеров, введя следующую команду в окне интегрированного терминала. Затем образ модуля можно отправить в реестр контейнеров Azure. 
     
    ```csh/sh
    docker login -u <ACR username> <ACR login server>
    ```
    Укажите имя пользователя и сервер входа, которые были скопированы из реестра контейнеров Azure ранее. Когда будет предложено ввести пароль, вставьте пароль реестра контейнеров и нажмите клавишу **ВВОД**.

    ```csh/sh
    Password: <paste in the ACR password and press enter>
    Login Succeeded
    ```

2. В обозревателе VS Code откройте файл **deployment.template.json** в рабочей области решения IoT Edge. Этот файл сообщает среде выполнения IoT Edge, какие модули должны быть развернуты на устройстве. Обратите внимание, что ваш модуль функции **CSharpFunction** указан вместе с модулем **tempSensor**, предоставляющим тестовые данные. Общие сведения о манифестах развертывания см. в статье [Сведения об использовании, настройке и повторном использовании модулей Azure IoT Edge (предварительная версия)](module-composition.md).

   ![Просмотр модуля в манифесте развертывания](./media/tutorial-deploy-function/deployment-template.png)

3. Откройте **ENV**-файл в рабочей области решения IoT Edge. Этот файл игнорируется git и хранит учетные данные реестра контейнеров, чтобы вам не требовалось помещать их в шаблон манифеста развертывания. Предоставьте **имя пользователя** и **пароль** для реестра контейнеров. 

5. Сохраните этот файл.

6. В обозревателе VS Code щелкните правой кнопкой мыши файл deployment.template.json и выберите **Build and Push IoT Edge solution** (Создать и отправить решение IoT Edge). 

После указания службе Visual Studio Code создать решение, сначала она запишет данные в шаблон развертывания, а затем в новой папке **config** создаст файл deployment.json. Затем выполняются две команды в интегрированном терминале: `docker build` и `docker push`. Они создают код, упаковывают функции в контейнеры, а затем отправляют его в реестр контейнера, который был указан при инициализации решения. 

## <a name="view-your-container-image"></a>Просмотр образа контейнера

Visual Studio Code выводит сообщение об успешном завершении, когда образ контейнера передается в ваш реестр контейнеров. Если требуется лично убедиться в успешности выполнения, можно просмотреть образ в реестре. 

1. Перейдите в реестр контейнеров на портале Azure. 
2. Выберите **Репозитории**.
3. Репозиторий **csharpfunction** отобразится в списке. Чтобы просмотреть дополнительные сведения о репозитории, выберите его.
4. В разделе **Теги** должен отобразиться тег **0.0.1-amd64**. Он указывает версию и платформу созданного образа. Эти значения устанавливаются в файле module.json в папке CSharpFunction. 

## <a name="deploy-and-run-the-solution"></a>Развертывание и запуск решения

Для развертывания модуля функции в устройстве IoT Edge можно использовать портал Azure (как это было показано в кратком руководстве). Также в Visual Studio Code можно развертывать модули и проводить их мониторинг. В следующих разделах используется расширение Azure IoT Edge для VS Code, указанное в предварительных требованиях. Если расширение не было установлено, то теперь это можно сделать. 

1. Выберите **Представление** > **Палитра команд** для открытия палитры команд VS Code.

2. Найдите и выполните команду **Azure: Sign in**. Следуйте инструкциям, чтобы войти в свою учетную запись Azure. 

3. В палитре команд найдите и выполните команду **Azure IoT Hub: Select IoT Hub**. 

4. Последовательно выберите подписку, содержащую Центр Интернета вещей, а затем Центр Интернета вещей, к которому необходимо получить доступ.

5. В обозревателе VS Code разверните раздел **Azure IoT Hub Devices** (Устройства Центра Интернета вещей Azure). 

6. Щелкните имя устройства IoT Edge правой кнопкой мыши, а затем выберите **Create Deployment for Single Device** (Создание развертывания для одного устройства). 

7. Перейдите в папку решения, содержащую **CSharpFunction**. Откройте папку конфигурации, выберите файл deployment.json и щелкните **Select Edge Deployment Manifest** (Выбрать манифест развертывания Edge).

8. Обновите раздел **Azure IoT Hub Devices** (Устройства Центра Интернета вещей Azure). Вы должны увидеть новый модуль **CSharpFunction**, работающий вместе с модулем **TempSensor**, а также **$edgeAgent** и **$edgeHub**. 

   ![Просмотр развернутых модулей в VS Code](./media/tutorial-deploy-function/view-modules.png)

## <a name="view-generated-data"></a>Просмотр сформированных данных

Чтобы отобразить все сообщения, поступающие в Центр Интернета вещей, в палитре команд выполните команду**Azure IoT Hub: Start Monitoring D2C Message**.

Также можно отфильтровать представления, чтобы увидеть все сообщения, поступающие в Центр Интернета вещей с определенного устройства. Щелкните устройство правой кнопкой мыши в разделе **Azure IoT Hub Devices** (Устройства Центра Интернета вещей Azure) и выберите **Start Monitoring D2C Messages** (Начать мониторинг сообщений D2C).

Чтобы перестать отслеживать сообщения, используйте команду **Azure IoT Hub: Stop monitoring D2C message** в палитре команд. 


## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы планируете перейти к следующей рекомендуемой статье, можно сохранить созданные и повторно используемые ресурсы и конфигурации. Это же устройство IoT Edge также можно использовать в качестве тестового устройства. 

В противном случае чтобы избежать расходов, можно удалить локальные конфигурации и ресурсы Azure, созданные в рамках этой статьи. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

[!INCLUDE [iot-edge-clean-up-local-resources](../../includes/iot-edge-clean-up-local-resources.md)]


## <a name="next-steps"></a>Дополнительная информация

Из этого руководства вы узнали, как создать модуль функции Azure, содержащий код для фильтрации необработанных данных, которые были созданы при помощи устройства IoT Edge. Когда будете готовы создавать собственные модули, обратитесь к статье [Использование Visual Studio Code для разработки и отладки решения "Функции Azure" для Azure IoT Edge](how-to-develop-csharp-function.md). 

Чтобы узнать о других способах, с помощью которых Azure IoT Edge может помочь превратить данные пограничного устройства в бизнес-аналитику, перейдите к следующим руководствам.

> [!div class="nextstepaction"]
> [Руководство по развертыванию Azure Stream Analytics в качестве модуля IoT Edge (предварительная версия)](tutorial-deploy-stream-analytics.md)

<!--Links-->
[lnk-tutorial1-win]: quickstart.md
[lnk-tutorial1-lin]: quickstart-linux.md
