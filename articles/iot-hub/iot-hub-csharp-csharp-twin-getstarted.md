---
title: Начало работы с двойниками устройств Центра Интернета вещей Azure (.NET/.NET) | Документы Майкрософт
description: Добавление тегов и последующее использование запроса Центра Интернета вещей с помощью двойников устройств Центра Интернета вещей. Используйте пакет SDK для устройств Azure IoT для .NET, чтобы реализовать приложение имитации устройства, и пакет SDK для служб Azure IoT для .NET, чтобы реализовать приложение-службу, которое добавит теги и выполнит запрос к Центру Интернета вещей.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 05/15/2017
ms.author: dobett
ms.openlocfilehash: 340453448d38db66558e59edb845f2caf4454cf9
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/27/2018
ms.locfileid: "43104156"
---
# <a name="get-started-with-device-twins-netnet"></a>Начало работы с двойниками устройств (.NET/.NET)
[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

Выполнив это руководство, вы создадите следующие консольные приложения .NET:

* **CreateDeviceIdentity** — приложение .NET, создающее удостоверение устройства и соответствующий ключ безопасности для подключения к приложению виртуального устройства.

* **AddTagsAndQuery** — внутреннее приложение .NET, которое добавляет теги и выполняет запросы к двойникам устройств.

* **ReportConnectivity** — приложение .NET, которое имитирует устройство, подключающееся к Центру Интернета вещей с использованием удостоверения устройства, созданного ранее, и передает условия его подключения.

> [!NOTE]
> Статья [Общие сведения о пакетах SDK для Azure IoT и их использование](iot-hub-devguide-sdks.md) содержит сведения о разных пакетах SDK для Интернета вещей Azure, с помощью которых можно создать приложения для устройств и внутренние приложения.
> 

Для работы с этим учебником требуется:

* Visual Studio 2017.
* Активная учетная запись Azure. Если ее нет, можно создать [бесплатную учетную запись](http://azure.microsoft.com/pricing/free-trial/) всего за несколько минут.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

## <a name="create-the-service-app"></a>Создание приложения службы

В этом разделе рассказывается о том, как создать консольное приложение .NET (с помощью C#), которое добавляет метаданные расположения в двойник устройства, связанный с **myDeviceId**. После этого оно запрашивает данные двойников устройств, хранящихся в Центре Интернета вещей, выбирает устройства, находящиеся в США, а затем те, которые сообщили о подключении по сети мобильной связи.

1. В Visual Studio добавьте в текущее решение проект классического приложения Windows на языке Visual C# с помощью шаблона проекта **консольного приложения** . Назовите проект **AddTagsAndQuery**.
   
    ![Новый проект классического приложения Windows на языке Visual C#](./media/iot-hub-csharp-csharp-twin-getstarted/createnetapp.png)

2. В обозревателе решений щелкните правой кнопкой мыши проект **AddTagsAndQuery**, а затем **Управление пакетами Nuget...**

3. В окне **Диспетчер пакетов NuGet** выберите **Обзор** и найдите **Microsoft.Azure.Devices**. Выберите **Установить**, чтобы установить пакет **Microsoft.Azure.Devices**, и примите условия использования. В результате выполняется скачивание и установка пакета NuGet [SDK для служб Azure IoT](https://www.nuget.org/packages/Microsoft.Azure.Devices/) и его зависимостей, а также добавляется соответствующая ссылка.
   
    ![Окно "Диспетчер пакетов NuGet"](./media/iot-hub-csharp-csharp-twin-getstarted/servicesdknuget.png)

4. Добавьте следующие инструкции `using` в начало файла **Program.cs** :

    ```csharp  
    using Microsoft.Azure.Devices;
    ```

5. Добавьте следующие поля в класс **Program** . Замените значение заполнителя строкой подключения к Центру Интернета вещей, созданному в предыдущем разделе.

    ```csharp  
    static RegistryManager registryManager;
    static string connectionString = "{iot hub connection string}";
    ```

6. Добавьте следующий метод в класс **Program** .

    ```csharp  
    public static async Task AddTagsAndQuery()
    {
        var twin = await registryManager.GetTwinAsync("myDeviceId");
        var patch =
            @"{
                tags: {
                    location: {
                        region: 'US',
                        plant: 'Redmond43'
                    }
                }
            }";
        await registryManager.UpdateTwinAsync(twin.DeviceId, patch, twin.ETag);
   
        var query = registryManager.CreateQuery(
          "SELECT * FROM devices WHERE tags.location.plant = 'Redmond43'", 100);
        var twinsInRedmond43 = await query.GetNextAsTwinAsync();
        Console.WriteLine("Devices in Redmond43: {0}", 
          string.Join(", ", twinsInRedmond43.Select(t => t.DeviceId)));
   
        query = registryManager.CreateQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43' AND properties.reported.connectivity.type = 'cellular'", 100);
        var twinsInRedmond43UsingCellular = await query.GetNextAsTwinAsync();
        Console.WriteLine("Devices in Redmond43 using cellular network: {0}", 
          string.Join(", ", twinsInRedmond43UsingCellular.Select(t => t.DeviceId)));
    }
    ```
   
    Класс **RegistryManager** предоставляет все методы, необходимые для взаимодействия с двойниками устройства из службы. Предыдущий код сначала инициализирует объект **RegistryManager**, затем извлекает двойник устройства для **MyDeviceId** и обновляет его теги, используя сведения о требуемом расположении.
   
    После обновления он выполняет два запроса: первый выбирает только двойники устройств, расположенные на фабрике **Redmond43**, а второй уточняет условия первого запроса и выбирает устройства, подключенные по сети мобильной связи.
   
    Обратите внимание, что при создании объекта **query** предыдущий код указывает максимальное количество возвращаемых документов. Объект **Query** содержит логическое свойство **HasMoreResults**, которое можно использовать для вызова методов **GetNextAsTwinAsync** несколько раз, чтобы получить все результаты. Метод **GetNextAsJson** доступен для результатов, которые не являются двойниками устройств, например результатов статистических запросов.

7. Наконец, добавьте следующие строки в метод **Main** :

    ```csharp  
    registryManager = RegistryManager.CreateFromConnectionString(connectionString);
    AddTagsAndQuery().Wait();
    Console.WriteLine("Press Enter to exit.");
    Console.ReadLine();
    ```

8. В обозревателе решений выберите **Задать автозагружаемые проекты...** и убедитесь, что для параметра **Действие** проекта **AddTagsAndQuery** задано значение **Запустить**. Выполните сборку решения.

9. Запустите это приложение, щелкнув правой кнопкой мыши проект **AddTagsAndQuery** и выбрав **Отладка**, а затем — **Запустить новый экземпляр**. В результатах запроса на все устройства, расположенные на фабрике **Redmond43**, отобразится одно устройство, а для запроса на ограничение результатов устройствами, использующими сеть мобильной связи, не отобразится ни одного устройства.
   
    ![Результаты запроса в окне](./media/iot-hub-csharp-csharp-twin-getstarted/addtagapp.png)

В следующем разделе рассказывается о том, как создать приложение устройства, которое сообщает сведения о подключении и изменяет результат запроса, описанного в предыдущем разделе.

## <a name="create-the-device-app"></a>Создание приложения устройства

В этом разделе вы создадите консольное приложение .NET, которое подключается к центру как **myDeviceId** и обновляет передаваемые свойства, добавив в них сведения о подключении по сети мобильной связи.

1. В Visual Studio добавьте в текущее решение проект классического приложения Windows на языке Visual C# с помощью шаблона проекта **консольного приложения** . Назовите проект **ReportConnectivity**.
   
    ![Новое классическое приложение устройства Windows на языке Visual C#](./media/iot-hub-csharp-csharp-twin-getstarted/createdeviceapp.png)
    
2. В обозревателе решений щелкните правой кнопкой мыши проект **ReportConnectivity**, а затем **Управление пакетами NuGet...**

3. В окне **Диспетчер пакетов NuGet** выберите **Обзор** и найдите **Microsoft.Azure.Devices.Client**. Выберите **Установить**, чтобы установить пакет **Microsoft.Azure.Devices.Client**, и примите условия использования. В результате выполняется скачивание и установка пакета NuGet [SDK для устройств Azure IoT](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/) и его зависимостей, а также добавляется соответствующая ссылка.
   
    ![Клиентское приложение в окне "Диспетчер пакетов NuGet"](./media/iot-hub-csharp-csharp-twin-getstarted/clientsdknuget.png)

4. Добавьте следующие инструкции `using` в начало файла **Program.cs** :

    ```csharp  
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using Newtonsoft.Json;
    ```

5. Добавьте следующие поля в класс **Program** . Замените значение заполнителя строкой подключения устройства, записанной в предыдущем разделе.

    ```csharp  
    static string DeviceConnectionString = "HostName=<yourIotHubName>.azure-devices.net;
      DeviceId=<yourIotDeviceName>;SharedAccessKey=<yourIotDeviceAccessKey>";
    static DeviceClient Client = null;
    ```

6. Добавьте следующий метод в класс **Program** .

    ```csharp
    public static async void InitClient()
    {
        try
        {
            Console.WriteLine("Connecting to hub");
            Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, 
              TransportType.Mqtt);
            Console.WriteLine("Retrieving twin");
            await Client.GetTwinAsync();
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
        }
    }
    ```

    Объект **Client** позволяет получить все методы, необходимые для взаимодействия с двойниками устройства с устройства. Приведенный выше код инициализирует объект **Client**, а затем получает двойник устройства для **myDeviceId**.

7. Добавьте следующий метод в класс **Program** .

    ```csharp  
    public static async void ReportConnectivity()
    {
        try
        {
            Console.WriteLine("Sending connectivity data as reported property");
            
            TwinCollection reportedProperties, connectivity;
            reportedProperties = new TwinCollection();
            connectivity = new TwinCollection();
            connectivity["type"] = "cellular";
            reportedProperties["connectivity"] = connectivity;
            await Client.UpdateReportedPropertiesAsync(reportedProperties);
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
        }
    }
    ```

   Вышеприведенный код изменяет передаваемое свойство **myDeviceId** с помощью сведений о подключении.

8. Наконец, добавьте следующие строки в метод **Main** :

    ```csharp
    try
    {
        InitClient();
        ReportConnectivity();
    }
    catch (Exception ex)
    {
        Console.WriteLine();
        Console.WriteLine("Error in sample: {0}", ex.Message);
    }
    Console.WriteLine("Press Enter to exit.");
    Console.ReadLine();
    ```

9. В обозревателе решений выберите **Задать автозагружаемые проекты...** и убедитесь, что для параметра **Действие** проекта **ReportConnectivity** задано значение **Запустить**. Выполните сборку решения.

10. Запустите это приложение, щелкнув правой кнопкой мыши проект **ReportConnectivity** и выбрав **Отладка**, а затем — **Запустить новый экземпляр**. Программа получит сведения о двойнике, а затем отправит данные подключения как *переданное свойство*.
   
    ![Запуск приложения устройства для передачи данных подключения](./media/iot-hub-csharp-csharp-twin-getstarted/rundeviceapp.png)
       
11. Теперь, когда устройство сообщило сведения о подключении, оно должно появиться в обоих запросах. Запустите приложение .NET **AddTagsAndQuery**, чтобы возобновить выполнение запросов. На этот раз **myDeviceId** должен появиться в результатах обоих запросов.
   
    ![Сведения о подключении устройства успешно переданы](./media/iot-hub-csharp-csharp-twin-getstarted/tagappsuccess.png)

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве мы настроили новый Центр Интернета вещей на портале Azure и создали удостоверение устройства в реестре удостоверений Центра Интернета вещей. Вы добавили метаданные устройства в качестве тегов из внутреннего приложения и написали код приложения имитации устройства, чтобы сообщить сведения о подключении в двойнике устройства. Вы также узнали, как запрашивать эти сведения, используя похожий на SQL язык запросов Центра Интернета вещей.

Ознакомьтесь со следующими материалами, чтобы узнать как:

* отправить данные телеметрии с устройств ([Краткое руководство. Отправка данных телеметрии с устройства в Центр Интернета вещей и чтение данных телеметрии из центра с помощью внутреннего приложения (C#)](quickstart-send-telemetry-dotnet.md));

* настроить устройства с помощью требуемых свойств двойника устройства ([Руководство. Настройка устройств из внутренней службы](tutorial-device-twins.md));

* управлять устройствами в интерактивном режиме, например включить вентилятор из управляемого пользователем приложения ([Краткое руководство по управлению подключенным к Центру Интернета вещей устройством (Node.js)](quickstart-control-device-node.md)).
