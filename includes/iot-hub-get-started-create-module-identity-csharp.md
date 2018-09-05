---
title: включение файла
description: включение файла
services: iot-hub
author: chrissie926
ms.service: iot-hub
ms.topic: include
ms.date: 04/26/2018
ms.author: menchi
ms.custom: include file
ms.openlocfilehash: 603a4892cc19991532c2858edd8d4ca90f850d1f
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/27/2018
ms.locfileid: "43086399"
---
## <a name="create-a-module-identity"></a>Создание удостоверения модуля

В этом разделе объясняется, как создать консольное приложение .NET, создающее удостоверение устройства и удостоверение модуля в реестре удостоверений Центра Интернета вещей. Устройства и модули не могут подключаться к Центру Интернета вещей, если в реестре удостоверений для них нет соответствующей записи. Дополнительные сведения см. в статье [Общие сведения о реестре удостоверений в Центре Интернета вещей](../articles/iot-hub/iot-hub-devguide-identity-registry.md). Запущенное консольное приложение создает уникальные идентификаторы и ключи одновременно для устройства и для модуля. Устройство и модуль применяют эти значения для идентификации при отправке сообщений с устройства в облако в Центр Интернета вещей. В идентификаторах учитывается регистр символов.


1. **Создание проекта Visual Studio**. В Visual Studio откройте новое решение и добавьте в него проект классического приложения Windows на языке Visual C#, используя шаблон проекта **Консольное приложение (.NET Framework)**. Убедитесь, что указана версия платформы .NET 4.6.1 или более поздняя версия. Присвойте проекту имя **CreateIdentities**, а решению — **IoTHubGetStarted**.

    ![Создание решения Visual Studio](./media/iot-hub-get-started-create-module-identity-csharp/create-identities-csharp1.JPG)

2. **Установка пакета SDK версии 1.16.0-preview-001 .NET для Центра Интернета вещей Azure**. Удостоверение модуля и двойник модуля предоставляются в общедоступной предварительной версии. Они доступны только через пакеты SDK предварительного выпуска для Центра Интернета вещей. В Visual Studio выберите для решения элементы "Сервис > Диспетчер пакетов NuGet > Управление пакетами NuGet". Выполните поиск по фразе Microsoft.Azure.Devices. Обязательно установите флажок "Включить предварительные выпуски". Выберите версию 1.16.0-preview-001 и установите ее. Теперь вы имеете доступ ко всем возможностям этого модуля. 

    ![Установка пакета SDK версии 1.16.0-preview-001 .NET для Центра Интернета вещей Azure](./media/iot-hub-get-started-create-module-identity-csharp/install-sdk.png)

3. Добавьте следующие инструкции `using` в начало файла **Program.cs** :

   ```csharp
   using Microsoft.Azure.Devices;
   using Microsoft.Azure.Devices.Common.Exceptions;
   ```

4. Добавьте следующие поля в класс **Program** . Замените значение заполнителя строкой подключения к Центру Интернета вещей, созданному в предыдущем разделе.

   ```csharp
   const string connectionString = "<replace_with_iothub_connection_string>";
   const string deviceID = "myFirstDevice";
   const string moduleID = "myFirstModule";
   ```

5. Добавьте следующий код в класс **Main**.
   
   ```csharp
   static void Main(string[] args)
   {
       AddDeviceAsync().Wait();
       AddModuleAsync().Wait();
   }
   ```

6. Добавьте следующие методы в класс **Program**.

    ```csharp
    private static async Task AddDeviceAsync()
    {
       RegistryManager registryManager = 
         RegistryManager.CreateFromConnectionString(connectionString);
       Device device;

       try
       {
           device = await registryManager.AddDeviceAsync(new Device(deviceID));
       }
       catch (DeviceAlreadyExistsException)
        {
            device = await registryManager.GetDeviceAsync(deviceID);
        }

        Console.WriteLine("Generated device key: {0}", 
          device.Authentication.SymmetricKey.PrimaryKey);
    }

    private static async Task AddModuleAsync()
    {
        RegistryManager registryManager = 
          RegistryManager.CreateFromConnectionString(connectionString);
        Module module;

        try
        {
            module = 
              await registryManager.AddModuleAsync(new Module(deviceID, moduleID));
        }
        catch (ModuleAlreadyExistsException)
        {
            module = await registryManager.GetModuleAsync(deviceID, moduleID);
        }

        Console.WriteLine("Generated module key: {0}", module.Authentication.SymmetricKey.PrimaryKey);
    }
    ```

    Метод AddDeviceAsync() создает удостоверение устройства с идентификатором **myFirstDevice**. Если этот идентификатор устройства уже существует в реестре, код просто извлекает сведения о существующем устройстве. Затем приложение отображает первичный ключ для данного удостоверения. Этот ключ будет использоваться в приложении виртуального устройства для подключения к Центру Интернета вещей.

    Метод AddModuleAsync() создает удостоверение модуля с идентификатором **myFirstModule** для устройства **myFirstDevice**. (Если такой идентификатор модуля уже существует в реестре удостоверений, код просто извлекает сведения о существующем модуле.) Затем приложение отображает первичный ключ для данного удостоверения. Этот ключ будет использоваться в приложении виртуального модуля для подключения к Центру Интернета вещей.

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

7. Запустите это приложение и запишите ключ устройства и ключ модуля.

> [!NOTE]
> В реестре удостоверений в Центре Интернета вещей хранятся только удостоверения устройств и модулей, необходимые для защиты доступа к Центру Интернета вещей. Реестр удостоверений хранит идентификаторы устройств и ключи, используемые в качестве учетных данных безопасности. Реестр удостоверений также хранит флаги включения и отключения для каждого устройства. Эти флаги можно использовать для отключения доступа для этого устройства. Если в приложении необходимо хранить другие метаданные для конкретного устройства, следует использовать хранилище конкретного приложения. У вас нет возможности включать и отключать удостоверения модулей. Дополнительные сведения см. в разделе [Руководство разработчика для Центра Интернета вещей](../articles/iot-hub/iot-hub-devguide-identity-registry.md).
