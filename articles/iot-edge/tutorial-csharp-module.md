---
title: Руководство по использованию Azure IoT Edge с кодом C# | Документация Майкрософт
description: В этом руководстве показано, как создать модуль IoT Edge c кодом C# и развернуть его на граничном устройстве.
services: iot-edge
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/27/2018
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 2293390684a8dcdf5f32bbae8f04fe7317d389e2
ms.sourcegitcommit: c2c64fc9c24a1f7bd7c6c91be4ba9d64b1543231
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/26/2018
ms.locfileid: "39258971"
---
# <a name="tutorial-develop-a-c-iot-edge-module-and-deploy-to-your-simulated-device"></a>Руководство по разработке модуля IoT Edge с кодом C# и его развертывание на имитированном устройстве

Вы можете использовать модули Azure IoT Edge для развертывания кода, который реализует бизнес-логику непосредственно на устройствах IoT Edge. В этом руководстве рассматриваются создание и развертывание модуля IoT Edge, который фильтрует данные датчика. Вы используете имитированное устройство IoT Edge, созданное при работе с руководством по развертыванию Azure IoT Edge, на имитированном устройстве в [Windows][lnk-tutorial1-win] или [Linux][lnk-tutorial1-lin]. Из этого руководства вы узнаете, как выполнять следующие задачи:    

> [!div class="checklist"]
> * Использовать Visual Studio Code для создания модуля IoT Edge на основе пакета SDK .NET Core 2.0.
> * Использовать Visual Studio Code и Docker для создания образа Docker и его публикации в реестре.
> * Развертывать модуль на устройстве IoT Edge.
> * Просматривать сформированные данные.


Модуль IoT Edge, создаваемый в этом руководстве, фильтрует данные температуры, которые создаются устройством. Оно отправляет сообщения, только если температура превышает заданное пороговое значение. Такой тип пограничного анализа удобен для сокращения объема данных, передаваемых в облако и сохраняемых в нем. 

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free), прежде чем начинать работу.


## <a name="prerequisites"></a>Предварительные требования

* Устройство Azure IoT Edge, которое вы создали при работе с кратким руководством для устройств [Linux](quickstart-linux.md) или [Windows](quickstart.md).
* Строка подключения первичного ключа для устройства IoT Edge.  
* [Visual Studio Code](https://code.visualstudio.com/). 
* [C# для расширения Visual Studio Code (на платформе OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).
* [Расширение Azure IoT Edge для Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge). 
* [Пакет SDK для .NET Core 2.1](https://www.microsoft.com/net/download).
* [Docker CE](https://docs.docker.com/install/) на компьютере, на котором ведется разработка. 


## <a name="create-a-container-registry"></a>Создание реестра контейнеров
В этом руководстве вы используете расширение Azure IoT Edge для Visual Studio Code, чтобы создать модуль, и создадите **образ контейнера** из файлов. Затем вы отправите этот образ в **реестр**, содержащий ваши образы и управляющий ими. Наконец, вы развернете свой образ из реестра для выполнения на устройстве IoT Edge.  

Для работы с этим руководством можно использовать любые реестры, совместимые с Docker. Две популярные службы реестров Docker, доступные в облаке — [Реестр контейнеров Azure](https://docs.microsoft.com/azure/container-registry/) и [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). В этом руководстве используется реестр контейнеров Azure. 

1. На [портале Azure](https://portal.azure.com) выберите **Создать ресурс** > **Контейнеры** > **Реестр контейнеров Azure**.
2. Укажите имя реестра, выберите подписку, выберите группу ресурсов и задайте номер SKU **Базовый**. 
3. Нажмите кнопку **Создать**.
4. После создания реестра контейнеров просмотрите его и выберите **Ключи доступа**. 
5. **Включите** параметр **Пользователь-администратор**.
6. Скопируйте значения **Сервер входа**, **Имя пользователя** и **Пароль**. Эти значения будут использоваться далее в руководстве при публикации образа Docker в реестре и добавлении учетных данных реестра в среду выполнения Azure IoT Edge. 

## <a name="create-an-iot-edge-module-project"></a>Создание проекта модуля IoT Edge
На следующих этапах показано, как создать проект модуля IoT Edge на основе .NET Сore 2.0 с использованием Visual Studio Code и расширения Azure IoT Edge.

1. В Visual Studio Code выберите **Представление** > **Палитра команд** для открытия палитры команд VS Code. 
2. В палитре команд введите и выполните команду **Azure: Sign in** (Azure: Вход) и следуйте инструкциям, чтобы войти в свою учетную запись Azure. Если вход был выполнен, то этот шаг можно пропустить.
3. В палитре команд введите и выполните команду **Azure IoT Edge: New IoT Edge solution**. В палитре команд укажите следующие сведения для создания решения: 

   1. Выберите папку, где требуется создать решение. 
   2. Введите имя своего решения или примите имя по умолчанию **EdgeSolution**.
   3. Выберите **C# Module** (Модуль C#) в качестве шаблона модуля. 
   4. Замените имя модуля по умолчанию модуль на **CSharpModule**. 
   5. В качестве репозитория образов первого модуля необходимо указать реестр контейнеров Azure, который был создан в предыдущем разделе. Замените **localhost:5000** скопированным значением имени входа на сервер. Окончательная строка выглядит так: \<registry name\>.azurecr.io/csharpmodule.

4.  Окно VS Code загружает рабочую область решения IoT Edge: папка для модулей,\.файл шаблона манифеста развертывания и \.env-файл. В обозревателе VS Code выберите **modules** > **NodeModule** > **app.js**.

5. В верхней части пространства имен **CSharpModule** добавьте три **инструкции** для типов, которые будут использоваться позже.

    ```csharp
    using System.Collections.Generic;     // For KeyValuePair<>
    using Microsoft.Azure.Devices.Shared; // For TwinCollection
    using Newtonsoft.Json;                // For JsonConvert
    ```

6. Добавьте переменную **temperatureThreshold** к классу **Program**. Эта переменная устанавливает значение, которое должно быть измеренной температурой, чтобы данные были отправлены в Центр Интернета вещей. 

    ```csharp
    static int temperatureThreshold { get; set; } = 25;
    ```

7. Добавьте **MessageBody**, **Machine** и **Ambient** к классу **Program**. Эти классы определяют ожидаемую схему текста входящего сообщения.

    ```csharp
    class MessageBody
    {
        public Machine machine {get;set;}
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

8. Метод **Init** объявляет для модуля протокол связи. Замените параметры MQTT параметрами AMPQ. 

   ```csharp
   // MqttTransportSettings mqttSetting = new MqttTransportSettings(TransportType.Mqtt_Tcp_Only);
   // ITransportSettings[] settings = { mqttSetting };

   AmqpTransportSettings amqpSetting = new AmqpTransportSettings(TransportType.Amqp_Tcp_Only);
   ITransportSettings[] settings = {amqpSetting};
   ```

8. В методе **Init** код создает и настраивает объект **ModuleClient**. Этот объект позволяет модулю подключаться к локальной среде выполнения Azure IoT Edge для отправки и получения сообщений. Строка подключения, используемая в методе **Init**, предоставляется модулю средой выполнения IoT Edge. После создания **ModuleClient** код считывает значение **temperatureThreshold** из требуемых свойств двойника модуля. Этот код регистрирует обратный вызов для получения сообщений из концентратора IoT Edge через **input1** конечной точки. Замените метод  **SetInputMessageHandlerAsync** новым и добавьте метод **SetDesiredPropertyUpdateCallbackAsync** для обновления нужного свойства. Чтобы внести это изменение, замените последнюю строку метода **Init** следующим кодом:

    ```csharp
    // Register a callback for messages that are received by the module.
    // await ioTHubModuleClient.SetImputMessageHandlerAsync("input1", PipeMessage, iotHubModuleClient);

    // Read the TemperatureThreshold value from the module twin's desired properties
    var moduleTwin = await ioTHubModuleClient.GetTwinAsync();
    var moduleTwinCollection = moduleTwin.Properties.Desired;
    try {
        temperatureThreshold = moduleTwinCollection["TemperatureThreshold"];
    } catch(ArgumentOutOfRangeException e) {
        Console.WriteLine($"Property TemperatureThreshold not exist: {e.Message}"); 
    }

    // Attach a callback for updates to the module twin's desired properties.
    await ioTHubModuleClient.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertiesUpdate, null);

    // Register a callback for messages that are received by the module.
    await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FilterMessages, ioTHubModuleClient);
    ```

9. Добавьте метод **onDesiredPropertiesUpdate** к классу **Program**. Этот метод принимает изменения требуемых свойств из двойника модуля и соответствующим образом изменяет переменную **temperatureThreshold**. У каждого модуля есть собственный модуль-двойник, что позволяет настроить код, выполняемый в модуле, непосредственно из облака.

    ```csharp
    static Task OnDesiredPropertiesUpdate(TwinCollection desiredProperties, object userContext)
    {
        try
        {
            Console.WriteLine("Desired property change:");
            Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));

            if (desiredProperties["TemperatureThreshold"]!=null)
                temperatureThreshold = desiredProperties["TemperatureThreshold"];

        }
        catch (AggregateException ex)
        {
            foreach (Exception exception in ex.InnerExceptions)
            {
                Console.WriteLine();
                Console.WriteLine("Error when receiving desired property: {0}", exception);
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error when receiving desired property: {0}", ex.Message);
        }
        return Task.CompletedTask;
    }
    ```

10. Замените метод **PipeMessage** методом **FilterMessages**. Этот метод вызывается каждый раз, когда модуль получает сообщение из центра IoT Edge. Он отфильтровывает сообщения о температуре ниже порогового значения, настроенного с помощью двойника модуля. Он также добавляет свойство **MessageType** в сообщение со значением **Alert**. 

    ```csharp
    static async Task<MessageResponse> FilterMessages(Message message, object userContext)
    {
        var counterValue = Interlocked.Increment(ref counter);
        try
        {
            ModuleClient moduleClient = (ModuleClient)userContext;
            var messageBytes = message.GetBytes();
            var messageString = Encoding.UTF8.GetString(messageBytes);
            Console.WriteLine($"Received message {counterValue}: [{messageString}]");

            // Get the message body.
            var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

            if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
            {
                Console.WriteLine($"Machine temperature {messageBody.machine.temperature} " +
                    $"exceeds threshold {temperatureThreshold}");
                var filteredMessage = new Message(messageBytes);
                foreach (KeyValuePair<string, string> prop in message.Properties)
                {
                    filteredMessage.Properties.Add(prop.Key, prop.Value);
                }

                filteredMessage.Properties.Add("MessageType", "Alert");
                await moduleClient.SendEventAsync("output1", filteredMessage);
            }

            // Indicate that the message treatment is completed.
            return MessageResponse.Completed;
        }
        catch (AggregateException ex)
        {
            foreach (Exception exception in ex.InnerExceptions)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", exception);
            }
            // Indicate that the message treatment is not completed.
            var moduleClient = (ModuleClient)userContext;
            return MessageResponse.Abandoned;
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
            // Indicate that the message treatment is not completed.
            ModuleClient moduleClient = (ModuleClient)userContext;
            return MessageResponse.Abandoned;
        }
    }
    ```

11. Сохраните этот файл.

## <a name="build-your-iot-edge-solution"></a>Сборка решения IoT Edge

В предыдущем разделе вы создали решение IoT Edge и добавили код **CSharpModule**, который будет отфильтровывать сообщения, где указанная температура компьютера ниже допустимого порогового значения. Теперь необходимо создать решение в качестве образа контейнера и передать его в реестр контейнеров. 

1. Войдите в Docker, введя следующую команду в окне интегрированного терминала Visual Studio Code. Затем можно отправить образ модуля в Реестр контейнеров Azure.
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```
   Укажите имя пользователя, пароль и сервер входа, скопированные из Реестра контейнеров Azure при его создании. Вы также можете получить эти значения из раздела **Ключи доступа** в разделе реестра на портале Azure.

2. В обозревателе VS Code откройте файл deployment.template.json в рабочей области IoT Edge. Этот файл указывает **$edgeAgent** развернуть два модуля: **tempSensor** и **CSharpModule**. Значение **CSharpModule.image** установлено на версию образа amd64 для Linux. 

   Убедитесь, что шаблон содержит правильное имя модуля, а не имя **SampleModule** по умолчанию, которое можно изменить при создании решения IoT Edge.

   Общие сведения о манифестах развертывания см. в статье [Сведения об использовании, настройке и повторном использовании модулей Azure IoT Edge (предварительная версия)](module-composition.md).

3. В файле deployment.template.json есть раздел **registryCredentials**, который хранит учетные данные реестра Docker. Фактическое имя пользователя хранится в файле ENV, который Git игнорирует.  

4. Добавьте двойник модуля **CSharpModule** в манифест развертывания. Вставьте следующее содержимое JSON в нижней части раздела **moduleContent** после двойника модуля **$edgeHub**: 
    ```json
        "CSharpModule": {
            "properties.desired":{
                "TemperatureThreshold":25
            }
        }
    ```

4. Сохраните этот файл.

5. В обозревателе VS Code щелкните правой кнопкой мыши файл deployment.template.json и выберите **Build IoT Edge solution** (Создать решение IoT Edge). 

После указания службе Visual Studio Code создать решение, сначала она запишет данные в шаблон развертывания, а затем в новой папке **config** создаст файл deployment.json. Затем выполняются две команды в интегрированном терминале: `docker build` и `docker push`. Они создают код, упаковывают его в контейнер CSharpModule.dll и передают в реестр контейнера, который был указан при инициализации решения. 

Полный адрес образа контейнера с тегом можно увидеть в окне интегрированного терминала VS Code. Адрес образа создается на основе информации из файла module.json в формате \<repository\>:\<version\>-\<platform\>. В этом руководстве он должен выглядеть так: registryname.azurecr.io/csharpmodule:0.0.1-amd64.

## <a name="deploy-and-run-the-solution"></a>Развертывание и запуск решения

1. Настройте расширение Azure IoT Toolkit со строкой подключения для Центра Интернета вещей. 

    1. Откройте обозреватель VS Code, выбрав **Вид** > **Обозреватель**.

    1. В обозревателе выберите **устройства Центра Интернета вещей Azure**, нажмите кнопку с многоточием (**...**), а затем выберите раздел **Выбрать центр Интернета вещей**. Следуйте инструкциям для входа в учетную запись Azure и выберите свой Центр Интернета вещей. 

       > [!Note]
       > Можно также завершить настройку выбрав **Установка строки подключения Центра Интернета вещей**. Введите во всплывающем окне строку подключения Центра Интернета вещей, к которому подключается ваше устройство IoT Edge.

2. В обозревателе устройств Центра Интернета вещей щелкните правой кнопкой мыши на устройство IoT Edge и выберите **Create deployment for IoT Edge device** (Создать развертывание для устройства IoT Edge). Выберите файл deployment.json в папке config и щелкните **Select Edge Deployment Manifest**(Выбрать манифест развертывания Edge).

3. Обновите раздел **Azure IoT Hub Devices** (Устройства Центра Интернета вещей Azure). Вы должны увидеть новый модуль **CSharpModule**, работающий вместе с модулем **TempSensor**, а также **$edgeAgent** и **$edgeHub**. 

## <a name="view-generated-data"></a>Просмотр сформированных данных

1. Для отслеживания данных, поступающих в Центр Интернета вещей, нажмите кнопку с многоточием (**...**), а затем выберите **Start Monitoring D2C Messages** (Начать мониторинг сообщений D2C).
2. Для отслеживания сообщений D2C для конкретного устройства щелкните его правой кнопкой мыши в списке и выберите **Start Monitoring D2C Messages** (Начать мониторинг сообщений D2C).
3. Чтобы перестать отслеживать данные, выполните команду **Azure IoT Hub: Stop monitoring D2C message** (Центр Интернета вещей Azure: остановить мониторинг сообщений D2C) в палитре команд. 
4. Для просмотра или обновления двойника модуля щелкните модуль правой кнопкой мыши в списке и выберите **Edit module twin** (Редактирование двойника модуля). Чтобы обновить двойник модуля, сохраните двойник файла JSON, затем щелкните правой кнопкой мыши область редактора и выберите **Update Module Twin** (Обновить двойник модуля).
5. Чтобы просмотреть журналы Docker, установите [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) для VS Code. Локально запущенные модули можно найти в обозревателе Docker. В контекстном меню выберите пункт **Показать журналы** для просмотра в интегрированном терминале.
 
## <a name="clean-up-resources"></a>Очистка ресурсов 

<!--[!INCLUDE [iot-edge-quickstarts-clean-up-resources](../../includes/iot-edge-quickstarts-clean-up-resources.md)] -->

Если вы планируете перейти к следующей рекомендуемой статье, можно сохранить созданные и повторно используемые ресурсы и конфигурации.

В противном случае чтобы избежать расходов, можно удалить локальные конфигурации и ресурсы Azure, созданные в рамках этой статьи. 

> [!IMPORTANT]
> Удаление ресурсов Azure и групп ресурсов является необратимым. Когда эти элементы удаляются, группа ресурсов и все содержащиеся в ней ресурсы удаляются без возможности восстановления. Будьте внимательны, чтобы случайно не удалить не ту группу ресурсов или не те ресурсы. Если вы создали Центр Интернета вещей в группе ресурсов, содержащей ресурсы, которые нужно сохранить, удалите только ресурс Центра Интернета вещей, не удаляя всю группу ресурсов.
>

Чтобы удалить только Центр Интернета вещей, выполните следующую команду, используя имена центра и группы ресурсов.

```azurecli-interactive
az iot hub delete --name MyIoTHub --resource-group TestResources
```


Чтобы удалить группу ресурсов по имени, выполните следующие действия.

1. Войдите в портал [Azure](https://portal.azure.com) и выберите**Группы ресурсов**.

2. Введите в текстовое поле **Фильтровать по имени** имя группы ресурсов, содержащей Центр Интернета вещей. 

3. Справа от своей группы ресурсов в списке результатов щелкните многоточие (**...**), а затем выберите **Удалить группу ресурсов**.

4. Подтвердите операцию удаления группы ресурсов. Повторно введите имя группы ресурсов, которую необходимо удалить, и щелкните **Удалить**. Через некоторое время группа ресурсов и все ее ресурсы будут удалены.



## <a name="next-steps"></a>Дополнительная информация

В этом руководстве вы создали модуль IoT Edge с кодом для фильтрации необработанных данных, созданных устройством IoT Edge. Когда вы будете готовы создавать собственные модули, больше об этом см. в разделе [Использование Visual Studio Code для разработки и отладки модулей C# для Azure IoT Edge](how-to-develop-csharp-module.md). Перейдите к следующим руководствам, чтобы узнать о других способах, с помощью которых Azure IoT Edge может помочь вам превратить данные в бизнес-аналитику на пограничном устройстве.

> [!div class="nextstepaction"]
> [Руководство по хранению данных в пограничной системе с помощью баз данных SQL Server](tutorial-store-data-sql-server.md)

<!-- Links -->
[lnk-tutorial1-win]: quickstart.md
[lnk-tutorial1-lin]: quickstart-linux.md

<!-- Images -->
[1]: ./media/tutorial-csharp-module/programcs.png
[2]: ./media/tutorial-csharp-module/build-module.png
[3]: ./media/tutorial-csharp-module/docker-os.png
