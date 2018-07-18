---
title: Развертывание Функции Azure с помощью Azure IoT Edge | Документация Майкрософт
description: Развертывание Функции Azure в виде модуля на граничном устройстве
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/26/2018
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 0445817f9ff403156025e38a1e14a3892a9a292b
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38667028"
---
# <a name="tutorial-deploy-azure-functions-as-iot-edge-modules---preview"></a>Руководство по развертыванию службы "Функции Azure" в качестве модуля IoT Edge (предварительная версия)

Вы можете использовать Функции Azure для развертывания кода, который реализует нужную бизнес-логику, непосредственно на устройствах IoT Edge. В этом руководстве описывается создание и развертывание Функции Azure, которая фильтрует данные датчиков на виртуальном устройстве IoT Edge, созданном по инструкциям в руководствах по развертыванию Azure IoT Edge на виртуальном устройстве в [Windows][lnk-tutorial1-win] или [Linux][lnk-tutorial1-lin]. Из этого руководства вы узнаете, как выполнять такие задачи:     

> [!div class="checklist"]
> * Использование Visual Studio Code для создания функции Azure
> * Использование VS Code и Docker для создания образа Docker и его публикация в реестре контейнеров 
> * Развертывание модуля из реестра контейнеров на устройстве IoT Edge
> * Просмотр отфильтрованных данных

>[!NOTE]
>Модули службы "Функции Azure" в Интернете вещей Azure находятся в [общедоступной предварительной версии](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

Функция Azure, которую вы создадите в этом руководстве, фильтрует сформированные устройством данные о температуре и отправляет сообщения в вышестоящий Центр Интернета вещей Azure, если температура превышает заданный порог. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>предварительным требованиям

Чтобы протестировать модуль службы "Функции", который вы создаете в рамках этого руководства, вам понадобится устройство IoT Edge. Вы можете использовать устройство, настроенное при работе с кратким руководством для [Linux](quickstart-linux.md) или [Windows](quickstart.md).

На компьютере разработки необходимо установить следующее: 
* [Visual Studio Code](https://code.visualstudio.com/). 
* [Расширение C# для Visual Studio Code (на платформе OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) для Visual Studio Code.
* [Расширение Azure IoT Edge для Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge). 
* [Пакет SDK для .NET Core 2.1](https://www.microsoft.com/net/download).
* [Docker CE](https://docs.docker.com/install/) на компьютере, на котором ведется разработка. 

## <a name="create-a-container-registry"></a>Создание реестра контейнеров
В этом руководстве вы используете расширение Azure IoT Edge для Visual Studio Code, чтобы создать модуль, и создадите **образ контейнера** из файлов. Затем вы отправите этот образ в **реестр**, содержащий ваши образы и управляющий ими. Наконец, вы развернете свой образ из реестра для выполнения на устройстве IoT Edge.  

Для работы с этим руководством можно использовать любые реестры, совместимые с Docker. Две популярные службы реестров Docker, доступные в облаке, — [реестр контейнеров Azure](https://docs.microsoft.com/azure/container-registry/) и [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). В этом руководстве используется реестр контейнеров Azure. 

1. На [портале Azure](https://portal.azure.com) выберите **Создать ресурс** > **Контейнеры** > **Реестр контейнеров**.

    ![создание реестра контейнеров](./media/tutorial-deploy-function/create-container-registry.png)

2. Укажите имя реестра и выберите подписку.
3. Для группы ресурсов рекомендуется использовать имя группы ресурсов, которое содержит ваш Центр Интернета вещей. Поместив все ресурсы для приложения в одну группу, вы можете управлять ими совместно. Например, при удалении группы ресурсов для тестирования все содержащиеся в ней ресурсы также удаляются. 
4. Установите для номера SKU значение **Базовый**, а для параметра **Администратор** установите значение **Включить**. 
5. Нажмите кнопку **Создать**.
6. После создания реестра контейнеров перейдите к нему и выберите **Ключи доступа**. 
7. Скопируйте значения **Сервер входа**, **Имя пользователя** и **Пароль**. Эти значения вам понадобятся позже при работе с этим руководством. 

## <a name="create-a-function-project"></a>Создание проекта функции
Следующий процесс позволяет создать модуль IoT Edge с использованием Visual Studio Code и расширения Azure IoT Edge.

1. Откройте Visual Studio Code.
2. Откройте интегрированный терминал VS Code, выберите **Вид** > **Интегрированный терминал**. 
2. Выберите **Представление** > **Палитра команд** для открытия палитры команд VS Code.
3. В палитре команд введите и выполните команду **Azure: Sign in** и следуйте инструкциям для входа в учетную запись Azure. Если вы уже выполняли вход, этот шаг можно пропустить.
3. В палитре команд введите и выполните команду**Azure IoT Edge: New IoT Edge solution**. В палитре команд укажите следующие сведения для создания решения: 

   1. Выберите папку, где требуется создать решение. 
   2. Введите имя своего решения или примите имя по умолчанию **EdgeSolution**.
   3. Выберите **Azure Functions — C#** (Функции Azure — C#) как шаблон модуля. 
   4. Назовите модуль **CSharpFunction**. 
   5. Укажите реестр контейнеров Azure, созданный в предыдущем разделе, как репозиторий образов для первого модуля. Замените **localhost:5000** скопированным значением имени входа на сервер. Окончательная строка выглядит так: **\<registry name\>.azurecr.io/csharpfunction**.

4. Окно VS Code загружает рабочую область решения IoT Edge. Имеется папка с расширением **VSCODE**, папка **modules**, файл с расширением **ENV** и файл шаблона с манифестом развертывания. Откройте **modules** > **CSharpFunction** > **EdgeHubTrigger Csharp** > **run.csx**.

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
            // Get the body of the message and deserialize it
            var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

            if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
            {
                // Send the message to the output as the temperature value is greater than the threashold
                var filteredMessage = new Message(messageBytes);
                // Copy the properties of the original message into the new Message object
                foreach (KeyValuePair<string, string> prop in messageReceived.Properties)
                {
                    filteredMessage.Properties.Add(prop.Key, prop.Value);                }
                // Add a new property to the message to indicate it is an alert
                filteredMessage.Properties.Add("MessageType", "Alert");
                // Send the message        
                await output.AddAsync(filteredMessage);
                log.Info("Received and transferred a message with temperature above the threshold");
            }
        }
    }

    //Define the expected schema for the body of incoming messages
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

В предыдущем разделе вы создали решение IoT Edge и добавили код в CSharpFunction, который будет отфильтровывать сообщения, где указанная температура компьютера ниже допустимого порога. Теперь необходимо создать решение в качестве образа контейнера и передать его в реестр контейнеров.

1. Войдите в Docker, введя следующую команду в окне интегрированного терминала Visual Studio Code, чтобы передать образ модуля в ACR: 
     
    ```csh/sh
    docker login -u <ACR username> <ACR login server>
    ```
    Укажите имя пользователя и сервер входа, скопированные из реестра контейнеров Azure ранее. Появится запрос на ввод пароля. Вставьте пароль в строку и нажмите клавишу **ВВОД**.

    ```csh/sh
    Password: <paste in the ACR password and press enter>
    Login Succeeded
    ```

2. В обозревателе VS Code откройте файл **deployment.template.json** в рабочей области решения IoT Edge. Этот файл сообщает среде выполнения IoT Edge, какие модули должны быть развернуты на устройстве. Общие сведения о манифестах развертывания см. в статье [Сведения об использовании, настройке и повторном использовании модулей Azure IoT Edge (предварительная версия)](module-composition.md).

3. Найдите раздел **registryCredentials** в манифесте развертывания. Замените **имя пользователя**, **пароль** и **адрес** учетными данными из своего реестра контейнеров. В этом разделе среде выполнения IoT Edge на вашем устройстве предоставляется разрешение извлечь образы контейнеров, хранящиеся в частном реестре. Фактические пары имени пользователя и пароля хранятся в ENV-файле, который игнорируется git.

5. Сохраните этот файл.

6. В обозревателе VS Code щелкните правой кнопкой мыши файл **deployment.template.json** и выберите **Build IoT Edge solution** (Создать решение IoT Edge). 

Когда вы указываете службе Visual Studio Code создать решение, она сначала принимает данные в шаблоне развертывания и создает файл `deployment.json` в новой папке **config**. Затем выполняются две команды в интегрированном терминале: `docker build` и `docker push`. Они создают ваш код, упаковывают функции в контейнеры и передают его в реестр контейнера, который вы указали при инициализации решения. 

## <a name="view-your-container-image"></a>Просмотр образа контейнера

Visual Studio Code выводит сообщение об успешном завершении, когда образ контейнера передается в ваш реестр контейнеров. Если требуется лично убедиться в успешности выполнения, можно просмотреть образ в реестре. 

1. На портале перейдите в реестр контейнеров. 
2. Выберите **Репозитории**.
3. Вы увидите **csharpfunction** в списке репозиториев. Выберите репозиторий, чтобы просмотреть дополнительные сведения.
4. В разделе **Теги** вы увидите **0.0.1-amd64**. Этот тег указывает версию и платформу созданного вами образа. Эти значения устанавливаются в файле **module.json** в папке CSharpFunction. 

## <a name="deploy-and-run-the-solution"></a>Развертывание и запуск решения

Вы можете использовать портал Azure для развертывания модуля службы "Функции" на устройстве IoT Edge, как вы делали в кратком руководстве, но можно также развернуть и отслеживать модули из Visual Studio Code. В следующих разделах используется расширение Azure IoT Edge для VS Code, указанное в предварительных требованиях. Установите его сейчас, если вы этого еще не сделали. 

1. Выберите **Представление** > **Палитра команд** для открытия палитры команд VS Code.

2. Найдите и выполните команду **Azure: Sign in**. Следуйте инструкциям, чтобы войти в свою учетную запись Azure. 

3. В палитре команд найдите и выполните команду **Azure IoT Hub: Select IoT Hub**. 

4. Выберите подписку, содержащую ваш Центр Интернета вещей, а затем выберите Центр Интернета вещей, к которому необходимо получить доступ.

5. В обозревателе VS Code разверните раздел **Azure IoT Hub Devices** (Устройства Центра Интернета вещей Azure). 

6. Щелкните имя устройства IoT Edge правой кнопкой мыши, а затем выберите **Create Deployment for IoT Edge device** (Создание развертывания для устройства IoT Edge). 

7. Перейдите в папку решения, которая содержит CSharpFunction. Откройте папку **config** и выберите файл **deployment.json**. Щелкните **Select Edge Deployment Manifest** (Выбрать манифест развертывания Edge).

8. Обновите раздел **Azure IoT Hub Devices** (Устройства Центра Интернета вещей Azure). Вы должны увидеть новый модуль **CSharpFunction**, работающий вместе с модулем **TempSensor**, а также **$edgeAgent** и **$edgeHub**. 

   ![Просмотр развернутых модулей в VS Code](./media/tutorial-deploy-function/view-modules.png)

## <a name="view-generated-data"></a>Просмотр сформированных данных

Вы можете увидеть все сообщения, поступающие в ваш Центр Интернета вещей, выполнив команду **Azure IoT Hub: Start Monitoring D2C Message** в палитре команд.

Вы также можете отфильтровать все сообщения, поступающие в ваш Центр Интернета вещей с определенного устройства. Щелкните устройство правой кнопкой мыши в разделе **Azure IoT Hub Devices** (Устройства Центра Интернета вещей Azure) и выберите **Start Monitoring D2C Messages** (Начать мониторинг сообщений D2C).

Чтобы перестать отслеживать сообщения, используйте команду **Azure IoT Hub: Stop monitoring D2C message** в палитре команд. 


## <a name="clean-up-resources"></a>Очистка ресурсов

[!INCLUDE [iot-edge-quickstarts-clean-up-resources](../../includes/iot-edge-quickstarts-clean-up-resources.md)]

Удалите среду выполнения службы IoT Edge на основе своей платформы устройств Интернета вещей (Linux или Windows).

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

В этом руководстве вы создали модуль службы "Функции Azure", который содержит код для фильтрации необработанных данных, созданных вашим устройством IoT Edge. Когда вы будете готовы создавать собственные модули, вы можете узнать больше о том, как [разрабатывать решения службы "Функции Azure" с помощью Azure IoT Edge для Visual Studio Code](how-to-develop-csharp-function.md). 

Перейдите к следующим руководствам, чтобы узнать о других способах, с помощью которых Azure IoT Edge может помочь вам превратить данные в бизнес-аналитику на пограничном устройстве.

> [!div class="nextstepaction"]
> [Развертывание Azure Stream Analytics в качестве модуля IoT Edge (предварительная версия)](tutorial-deploy-stream-analytics.md)

<!--Links-->
[lnk-tutorial1-win]: quickstart.md
[lnk-tutorial1-lin]: quickstart-linux.md
