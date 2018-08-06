---
title: Развертывание службы "Функции Azure" с помощью Azure IoT Edge | Документация Майкрософт
description: В этом руководстве выполняется развертывание службы "Функции Azure" в виде модуля на граничном устройстве.
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/26/2018
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: d37e08f58986a1318e6b379d2efeb71bc58d4583
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39413751"
---
# <a name="tutorial-deploy-azure-functions-as-iot-edge-modules-preview"></a>Руководство по развертыванию службы "Функции Azure" в качестве модуля IoT Edge (предварительная версия)

Службу "Функции Azure" можно использовать для развертывания кода, который реализует нужную бизнес-логику, непосредственно на устройствах Azure IoT Edge. В этом руководстве рассматривается создание и развертывание службы "Функции Azure", которая фильтрует данные датчика на имитированном устройстве IoT Edge. Вы используете имитированное устройство IoT Edge, созданное при работе с руководством по развертыванию Azure IoT Edge, на имитированном устройстве в [Windows][lnk-tutorial1-win] или [Linux][lnk-tutorial1-lin]. Из этого руководства вы узнаете, как выполнять следующие задачи:     

> [!div class="checklist"]
> * Использовать Visual Studio Code для создания функции Azure.
> * Использовать VS Code и Docker для создания образа Docker и его публикации в реестре контейнеров.
> * Развертывать модуль из реестра контейнеров на устройстве IoT Edge.
> * Просматривать отфильтрованные данные.

>[!NOTE]
>Модули функций Azure, используемые в Azure IoT Edge, находятся в [общедоступной предварительной версии](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

Функция Azure, создаваемая в этом руководстве, фильтрует данные температуры, созданные устройством. Она отправляет сообщения в вышестоящий Центр Интернета вещей Azure, когда температура превышает заданное пороговое значение. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Предварительные требования

Устройство Azure IoT Edge:

* В качестве устройства Azure IoT Edge можно использовать компьютер, на котором ведется разработка, или виртуальную машину. Для этого выполните действия, описанные в кратком руководстве для устройств [Linux](quickstart-linux.md) или [Windows](quickstart.md).

Облачные ресурсы:

* [Центр Интернета вещей](../iot-hub/iot-hub-create-through-portal.md) цен. категории "Стандартный" в Azure. 

Ресурсы разработки:

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

2. Введите имя реестра и выберите подписку.
3. Для группы ресурсов рекомендуется использовать имя группы ресурсов, которое содержит Центр Интернета вещей. Если поместить все ресурсы в одну группу, то ими можно совместно управлять. Например, удаление группы ресурсов, используемой для тестирования, приведет к удалению всех ее ресурсов. 
4. Установите для номера SKU значение **Базовый**, а для параметра **Администратор** установите значение **Включить**. 
5. Нажмите кнопку **Создать**.
6. После создания реестра контейнеров перейдите к нему, а затем выберите **Ключи доступа**. 
7. Скопируйте значения **Сервер входа**, **Имя пользователя** и **Пароль**. Эти значения при работе с этим руководством понадобятся позже. 

## <a name="create-a-function-project"></a>Создание проекта функции
В следующих шагах создается функция IoT Edge с помощью Visual Studio Code и расширения Azure IoT Edge.

1. Откройте Visual Studio Code.
2. Откройте интегрированный терминал VS Code, выберите **Вид** > **Интегрированный терминал**. 
2. Выберите **Представление** > **Палитра команд** для открытия палитры команд VS Code.
3. В палитре команд введите и выполните команду **Azure: Вход**. Следуйте инструкциям, чтобы войти в свою учетную запись Azure. Если вход был выполнен, то этот шаг можно пропустить.
3. В палитре команд введите и выполните команду **Azure IoT Edge: New IoT Edge solution**. В палитре команд укажите следующие сведения для создания решения: 

   1. Выберите папку, где требуется создать решение. 
   2. Введите имя своего решения или примите имя по умолчанию **EdgeSolution**.
   3. Выберите **Azure Functions — C#** (Функции Azure — C#) как шаблон модуля. 
   4. Назовите модуль **CSharpFunction**. 
   5. В качестве репозитория образов первого модуля необходимо указать реестр контейнеров Azure, который был создан в предыдущем разделе. Замените **localhost:5000** скопированным значением имени входа на сервер. Последняя строка выглядит следующим образом: \<имя реестра\>.azurecr.io/csharpfunction.

4. В окне VS Code будет загружена рабочая область решения IoT Edge, которая состоит из папки \.vscode, папки модулей, файла шаблона манифеста развертывания и файла \.env. В обозревателе VS Code откройте **Модули** > **CSharpFunction** > **EdgeHubTrigger Csharp** > **run.csx**.

5. Замените содержимое файла на код, приведенный ниже.

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

1. Войдите в Docker, введя следующую команду в окне интегрированного терминала Visual Studio Code. Затем образ модуля можно отправить в реестр контейнеров Azure. 
     
    ```csh/sh
    docker login -u <ACR username> <ACR login server>
    ```
    Укажите имя пользователя и сервер входа, которые были скопированы из реестра контейнеров Azure ранее. Появится запрос на ввод пароля. Вставьте пароль в строку и нажмите клавишу **ВВОД**.

    ```csh/sh
    Password: <paste in the ACR password and press enter>
    Login Succeeded
    ```

2. В обозревателе VS Code откройте файл deployment.template.json в рабочей области IoT Edge. Этот файл сообщает среде выполнения IoT Edge, какие модули должны быть развернуты на устройстве. Общие сведения о манифестах развертывания см. в статье [Сведения об использовании, настройке и повторном использовании модулей Azure IoT Edge (предварительная версия)](module-composition.md).

3. Найдите раздел **registryCredentials** в манифесте развертывания. Замените **имя пользователя**, **пароль** и **адрес** учетными данными из своего реестра контейнеров. В этом разделе среде выполнения IoT Edge на вашем устройстве предоставляется разрешение извлечь образы контейнеров, хранящиеся в частном реестре. Фактическое имя пользователя хранится в файле ENV, который Git игнорирует.

5. Сохраните этот файл.

6. В обозревателе VS Code щелкните правой кнопкой мыши файл deployment.template.json и выберите **Build IoT Edge solution** (Создать решение IoT Edge). 

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

6. Щелкните правой кнопкой мыши имя устройства IoT Edge, а затем выберите **Create Deployment for IoT Edge device** (Создание развертывания для устройства IoT Edge). 

7. Перейдите в папку решения, содержащую **CSharpFunction**. Откройте папку конфигурации, выберите файл deployment.json и щелкните **Select Edge Deployment Manifest** (Выбрать манифест развертывания Edge).

8. Обновите раздел **Azure IoT Hub Devices** (Устройства Центра Интернета вещей Azure). Вы должны увидеть новый модуль **CSharpFunction**, работающий вместе с модулем **TempSensor**, а также **$edgeAgent** и **$edgeHub**. 

   ![Просмотр развернутых модулей в VS Code](./media/tutorial-deploy-function/view-modules.png)

## <a name="view-generated-data"></a>Просмотр сформированных данных

Чтобы отобразить все сообщения, поступающие в Центр Интернета вещей, в палитре команд выполните команду**Azure IoT Hub: Start Monitoring D2C Message**.

Также можно отфильтровать представления, чтобы увидеть все сообщения, поступающие в Центр Интернета вещей с определенного устройства. Щелкните устройство правой кнопкой мыши в разделе **Azure IoT Hub Devices** (Устройства Центра Интернета вещей Azure) и выберите **Start Monitoring D2C Messages** (Начать мониторинг сообщений D2C).

Чтобы перестать отслеживать сообщения, используйте команду **Azure IoT Hub: Stop monitoring D2C message** в палитре команд. 


## <a name="clean-up-resources"></a>Очистка ресурсов

[!INCLUDE [iot-edge-quickstarts-clean-up-resources](../../includes/iot-edge-quickstarts-clean-up-resources.md)]

Удалите среду выполнения службы IoT Edge, основанную на платформе устройств IoT (ОС Linux или Windows).

#### <a name="windows"></a>Windows

Удалите среду выполнения IoT Edge.

```Powershell
stop-service iotedge -NoWait
sleep 5
sc.exe delete iotedge
```

Удалите контейнеры, созданные на своем устройстве. 

```Powershell
docker rm -f $(docker ps -a --no-trunc --filter "name=edge" --filter "name=tempSensor" --filter "name=CSharpFunction")
```

#### <a name="linux"></a>Linux

Удалите среду выполнения IoT Edge.

```bash
sudo apt-get remove --purge iotedge
```

Удалите контейнеры, созданные на своем устройстве. 

```bash
sudo docker rm -f $(sudo docker ps -a --no-trunc --filter "name=edge" --filter "name=tempSensor" --filter "name=CSharpFunction")
```

Удалите среду выполнения контейнера.

```bash
sudo apt-get remove --purge moby
```



## <a name="next-steps"></a>Дополнительная информация

Из этого руководства вы узнали, как создать модуль функции Azure, содержащий код для фильтрации необработанных данных, которые были созданы при помощи устройства IoT Edge. Когда будете готовы создавать собственные модули, обратитесь к статье [Использование Visual Studio Code для разработки и отладки решения "Функции Azure" для Azure IoT Edge](how-to-develop-csharp-function.md). 

Чтобы узнать о других способах, с помощью которых Azure IoT Edge может помочь превратить данные пограничного устройства в бизнес-аналитику, перейдите к следующим руководствам.

> [!div class="nextstepaction"]
> [Руководство по развертыванию Azure Stream Analytics в качестве модуля IoT Edge (предварительная версия)](tutorial-deploy-stream-analytics.md)

<!--Links-->
[lnk-tutorial1-win]: quickstart.md
[lnk-tutorial1-lin]: quickstart-linux.md
