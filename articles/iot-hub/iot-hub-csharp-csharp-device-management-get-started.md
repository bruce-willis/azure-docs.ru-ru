---
title: Приступая к работе с функцией управления устройствами Центра Интернета вещей Azure (.NET и .NET) | Документация Майкрософт
description: Использование функции управления устройствами в Центре Интернета вещей Azure для начала удаленной перезагрузки устройства. Пакет SDK для устройств Azure IoT для .NET позволяет реализовать приложение имитации устройства, которое содержит прямой метод. Пакет SDK для служб Azure IoT для .NET нужен для того, чтобы реализовать приложение-службу, вызывающее этот прямой метод.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 09/15/2017
ms.author: dobett
ms.openlocfilehash: bca25f5e7a6fd7685d20236033a5a225c5183a2d
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/26/2018
ms.locfileid: "47225351"
---
# <a name="get-started-with-device-management-netnet"></a>Приступая к работе с управлением устройствами (.NET и .NET)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

В этом учебнике описаны следующие процедуры.

* Создание экземпляра Центра Интернета вещей на портале Azure и удостоверения устройства в экземпляре Центра Интернета вещей.

* Создание приложения для имитации устройства с прямым методом, который позволяет выполнить перезагрузку устройства. Прямые методы вызываются из облака.

* Создание консольного приложения для .NET, вызывающего прямой метод перезагрузки в приложении имитации устройства с помощью Центра Интернета вещей.

По завершении работы с этим руководством у вас будет два консольных приложения .NET:

* **SimulateManagedDevice**, которое подключается к вашему центру Интернета вещей с ранее созданным идентификатором устройства, получает прямой метод перезагрузки, имитирует физическую перезагрузку и сообщает о времени последней перезагрузки.

* **TriggerReboot**, которое вызывает прямой метод в приложении виртуального устройства, выводит ответ и отображает обновленные сообщаемые свойства.

Для работы с этим учебником требуется:

* Visual Studio 2017.

* Активная учетная запись Azure. Если ее нет, можно создать [бесплатную учетную запись](http://azure.microsoft.com/pricing/free-trial/) всего за несколько минут.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Активация удаленной перезагрузки на устройстве с помощью прямого метода

В этом разделе вы создадите консольное приложение .NET (с помощью C#), которое инициирует удаленное обновление устройства с помощью прямого метода. Приложение использует запросы двойника устройства для определения времени последней перезагрузки этого устройства.

1. В Visual Studio добавьте в новое решение проект классического приложения Windows на языке Visual C# с помощью шаблона проекта **консольного приложения (.NET Framework)**. Убедитесь, что указана версия платформы .NET 4.5.1 или более поздняя версия. Назовите проект **TriggerReboot**.

    ![Новый проект классического приложения Windows на языке Visual C#](./media/iot-hub-csharp-csharp-device-management-get-started/createserviceapp.png)

2. В обозревателе решений щелкните правой кнопкой мыши проект **TriggerReboot** и выберите **Управление пакетами NuGet**.

3. В окне **Диспетчер пакетов NuGet** нажмите кнопку **Обзор**, найдите **microsoft.azure.devices**, щелкните **Установить**, чтобы установить пакет **Microsoft.Azure.Devices**, и примите условия использования. В результате выполняется скачивание и установка пакета NuGet [SDK для служб Azure IoT](https://www.nuget.org/packages/Microsoft.Azure.Devices/) и его зависимостей, а также добавляется соответствующая ссылка.

    ![Окно "Диспетчер пакетов NuGet"](./media/iot-hub-csharp-csharp-device-management-get-started/servicesdknuget.png)

4. Добавьте следующие инструкции `using` в начало файла **Program.cs** :
   
   ```csharp
   using Microsoft.Azure.Devices;
   using Microsoft.Azure.Devices.Shared;
   ```
        
5. Добавьте следующие поля в класс **Program** . Замените значения заполнителей строкой подключения к Центру Интернета вещей, созданному в разделе "Создание Центра Интернета вещей". 
   
   ```csharp
   static RegistryManager registryManager;
   static string connString = "{iot hub connection string}";
   static ServiceClient client;
   static string targetDevice = "myDeviceId";
   ```

6. Добавьте следующий метод в класс **Program**.  Этот код получает двойник устройства для перезагрузки устройства и выводит сообщаемые свойства.
   
   ```csharp
   public static async Task QueryTwinRebootReported()
   {
       Twin twin = await registryManager.GetTwinAsync(targetDevice);
       Console.WriteLine(twin.Properties.Reported.ToJson());
   }
   ```
        
7. Добавьте следующий метод в класс **Program**.  Этот код инициирует удаленную перезагрузку на устройстве с помощью прямого метода.

   ```csharp
   public static async Task StartReboot()
   {
       client = ServiceClient.CreateFromConnectionString(connString);
       CloudToDeviceMethod method = new CloudToDeviceMethod("reboot");
       method.ResponseTimeout = TimeSpan.FromSeconds(30);

       CloudToDeviceMethodResult result = await 
         client.InvokeDeviceMethodAsync(targetDevice, method);

       Console.WriteLine("Invoked firmware update on device.");
   }
   ```

7. Наконец, добавьте следующие строки в метод **Main** :
   
   ```csharp
   registryManager = RegistryManager.CreateFromConnectionString(connString);
   StartReboot().Wait();
   QueryTwinRebootReported().Wait();
   Console.WriteLine("Press ENTER to exit.");
   Console.ReadLine();
   ```

8. Выполните сборку решения.

> [!NOTE]
> В этом руководстве выполняется только один запрос свойств сообщаемого устройства. В рабочем коде мы рекомендуем выполнять опрос для поиска изменений в свойствах отчета.

## <a name="create-a-simulated-device-app"></a>Создание приложения виртуального устройства

В этом разделе вам необходимо выполнить следующие действия.

* Создайте консольное приложение .NET, отвечающее на прямой метод, вызываемый из облака.

* Запустите перезагрузку имитации устройства.

* Используйте сообщаемые свойства в запросах двойника устройства для определения устройств и времени последней перезагрузки.

1. В Visual Studio добавьте в текущее решение проект классического приложения Windows на языке Visual C# с помощью шаблона проекта **консольного приложения** . Назовите проект **SimulateManagedDevice**.
   
    ![Новое классическое приложение устройства Windows на языке Visual C#](./media/iot-hub-csharp-csharp-device-management-get-started/createdeviceapp.png)
    
2. В обозревателе решений щелкните правой кнопкой мыши проект **SimulateManagedDevice** и выберите **Управление пакетами NuGet**.

3. В окне **Диспетчер пакетов NuGet** выберите **Обзор** и найдите **Microsoft.Azure.Devices.Client**. Выберите **Установить**, чтобы установить пакет **Microsoft.Azure.Devices.Client**, и примите условия использования. В результате выполняется скачивание и установка пакета NuGet [SDK для устройств Azure IoT](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/) и его зависимостей, а также добавляется соответствующая ссылка.
   
    ![Клиентское приложение в окне "Диспетчер пакетов NuGet"](./media/iot-hub-csharp-csharp-device-management-get-started/clientsdknuget.png)
    
4. Добавьте следующие инструкции `using` в начало файла **Program.cs** :
   
    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    ```

5. Добавьте следующие поля в класс **Program** . Замените значение заполнителя строкой подключения устройства, записанной в предыдущем разделе.

    ```csharp
    static string DeviceConnectionString = 
      "HostName=<yourIotHubName>.azure-devices.net;DeviceId=<yourIotDeviceName>;SharedAccessKey=<yourIotDeviceAccessKey>";
    static DeviceClient Client = null;
    ```
6. Добавьте следующий код, чтобы реализовать прямой метод на устройстве:

   ```csharp
   static Task<MethodResponse> onReboot(MethodRequest methodRequest, object userContext)
   {
       // In a production device, you would trigger a reboot 
       //   scheduled to start after this method returns.
       // For this sample, we simulate the reboot by writing to the console
       //   and updating the reported properties.
       try
       {
           Console.WriteLine("Rebooting!");

           // Update device twin with reboot time. 
           TwinCollection reportedProperties, reboot, lastReboot;
           lastReboot = new TwinCollection();
           reboot = new TwinCollection();
           reportedProperties = new TwinCollection();
           lastReboot["lastReboot"] = DateTime.Now;
           reboot["reboot"] = lastReboot;
           reportedProperties["iothubDM"] = reboot;
           Client.UpdateReportedPropertiesAsync(reportedProperties).Wait();
       }
       catch (Exception ex)
       {
           Console.WriteLine();
           Console.WriteLine("Error in sample: {0}", ex.Message);
       }

       string result = "'Reboot started.'";
       return Task.FromResult(new MethodResponse(Encoding.UTF8.GetBytes(result), 200));
   }
   ```

7. И наконец, добавьте этот код в метод **Main**, чтобы открыть подключение к Центру Интернета вещей и инициализировать прослушиватель метода:

   ```csharp
   try
   {
       Console.WriteLine("Connecting to hub");
       Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, 
         TransportType.Mqtt);

       // setup callback for "reboot" method
       Client.SetMethodHandlerAsync("reboot", onReboot, null).Wait();
       Console.WriteLine("Waiting for reboot method\n Press enter to exit.");
       Console.ReadLine();

       Console.WriteLine("Exiting...");

       // as a good practice, remove the "reboot" handler
       Client.SetMethodHandlerAsync("reboot", null, null).Wait();
       Client.CloseAsync().Wait();
   }
   catch (Exception ex)
   {
       Console.WriteLine();
       Console.WriteLine("Error in sample: {0}", ex.Message);
   }
   ```
        
8. В обозревателе решений Visual Studio щелкните правой кнопкой мыши решение и выберите пункт **Назначить запускаемые проекты**. Щелкните **Один запускаемый проект**, а затем в раскрывающемся меню выберите проект **SimulateManagedDevice**. Выполните сборку решения.       

> [!NOTE]
> Для простоты в этом руководстве не реализуются политики повтора. В рабочем коде следует реализовать политики повторных попыток (например, с экспоненциальной задержкой), как указано в статье [Обработка временных сбоев](/azure/architecture/best-practices/transient-faults).

## <a name="run-the-apps"></a>Запуск приложений

Теперь все готово к запуску приложений.

1. Запустите приложение для устройства .NET **SimulateManagedDevice**. Щелкните правой кнопкой мыши проект **SimulateManagedDevice**, выберите **Отладка**, а затем щелкните **Запустить новый экземпляр**. Оно будет ожидать вызовы методов от вашего Центра Интернета вещей. 

2. Теперь, когда устройство подключено и ожидает вызовов методов, запустите приложение .NET **TriggerReboot** для вызова метода в приложении виртуального устройства. Для этого щелкните правой кнопкой мыши проект **TriggerReboot**, выберите **Отладка**, а затем щелкните **Запустить новый экземпляр**. Должен вернуться ответ "Перезагрузка" в консоли **SimulatedManagedDevice**, а также сообщаемые свойства устройства, которые включают время последней перезагрузки, записанное в консоли **TriggerReboot**.
   
    ![Запуск приложений служб и устройств](./media/iot-hub-csharp-csharp-device-management-get-started/combinedrun.png)

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]
