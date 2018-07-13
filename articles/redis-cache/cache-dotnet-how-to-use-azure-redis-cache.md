---
title: Краткое руководство по использованию кэша Redis для Azure с приложениями .NET | Документация Майкрософт
description: В этом кратком руководстве объясняется, как обращаться к кэшу Redis для Azure из приложений .NET.
services: redis-cache,app-service
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: c502f74c-44de-4087-8303-1b1f43da12d5
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 05/18/2018
ms.author: wesmc
ms.custom: mvc
ms.openlocfilehash: 31d93fc8b2034152e61d24a789bba62bfd3b7892
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38635444"
---
# <a name="quickstart-use-azure-redis-cache-with-a-net-application"></a>Краткое руководство. Использование кэша Redis для Azure с приложением .NET



В этом кратком руководстве объясняется, как начать работу с кэшем Redis для Microsoft Azure на платформе .NET. Кэш Redis для Microsoft Azure основан на популярном продукте с открытым кодом — кэше Redis. Он дает доступ к защищенному выделенному кэшу Redis, управляемому Майкрософт. Кэш, созданный с использованием кэша Azure Redis, доступен из любого приложения в Microsoft Azure.

С помощью этого краткого руководства вы запустите клиент [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) с помощью кода C\# в консольном приложении. Затем вы создадите кэш и настроите клиентское приложение .NET. После этого вы добавите в кэш объекты и обновите их. 

![Готовое консольное приложение](./media/cache-dotnet-how-to-use-azure-redis-cache/cache-console-app-complete.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>предварительным требованиям

* [Visual Studio](https://www.visualstudio.com/downloads/)
* Для клиента StackExchange.Redis требуется [.NET Framework 4 или более новая версия](https://www.microsoft.com/net/download/dotnet-framework-runtime).

## <a name="create-a-cache"></a>Создание кэша
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-access-keys](../../includes/redis-cache-access-keys.md)]

Создайте на компьютере файл с именем *CacheSecrets.config* и поместите его в расположение, которое не будет записано после изменения с исходным кодом примера приложения. В этот кратком руководстве файл *CacheSecrets.config* расположен в папке *C:\AppSecrets\CacheSecrets.config*.

Измените файл *CacheSecrets.config* и добавьте содержимое, приведенное ниже.

```xml
<appSettings>
    <add key="CacheConnection" value="<cache-name>.redis.cache.windows.net,abortConnect=false,ssl=true,password=<access-key>"/>
</appSettings>
```

Замените `<cache-name>` на имя узла кэша.

Замените `<access-key>` первичным ключом для кэша.


## <a name="create-a-console-app"></a>Создание консольного приложения

В Visual Studio выберите **Файл** > **Создать** > **Проект**.

В разделе **Visual C#** щелкните **Классическое приложение Windows**, а затем — **Консольное приложение** и **ОК**, чтобы создать новое консольное приложение.


<a name="configure-the-cache-clients"></a>

## <a name="configure-the-cache-client"></a>Настройка клиента кэша

В этом разделе вы настроите в консольном приложении использование клиента [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) для .NET.

В Visual Studio последовательно выберите **Инструменты** > **Диспетчер пакетов NuGet** > **Консоль диспетчера пакетов** и выполните следующую команду в открывшемся окне консоли менеджера пакетов.

```powershell
Install-Package StackExchange.Redis
```

Когда установка завершится, клиент кэша *StackExchange.Redis* станет доступным для использования в проекте.


## <a name="connect-to-the-cache"></a>Подключение к кэшу

В Visual Studio откройте файл *App.config* и включите в него атрибут `appSettings` `file`, который ссылается на файл *CacheSecrets.config*.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
    <startup> 
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.7.1" />
    </startup>

    <appSettings file="C:\AppSecrets\CacheSecrets.config"></appSettings>  

</configuration>
```

В обозревателе решений щелкните правой кнопкой мыши **Ссылки**, а затем выберите **Добавить ссылку**. Добавьте ссылку на сборку **System.Configuration**.

Добавьте следующие инструкции `using` в файл *Program.cs*:

```csharp
using StackExchange.Redis;
using System.Configuration;
```

Подключение к кэшу Redis для Azure выполняется с помощью класса `ConnectionMultiplexer` . Этот класс нужно сделать общим и повторно использовать в коде клиентского приложения. Не создавайте новое подключение для каждой операции. 

Никогда не храните учетные данные в исходном коде. Для простоты в этом примере используется только внешний файл конфигурации с секретными данными. Гораздо лучше было бы применить [Azure Key Vault с сертификатами](https://docs.microsoft.com/rest/api/keyvault/certificate-scenarios).

В файле *Program.cs* добавьте следующие члены в класс `Program` консольного приложения:

```csharp
        private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
        {
            string cacheConnection = ConfigurationManager.AppSettings["CacheConnection"].ToString();
            return ConnectionMultiplexer.Connect(cacheConnection);
        });

        public static ConnectionMultiplexer Connection
        {
            get
            {
                return lazyConnection.Value;
            }
        }
```


При таком подходе к совместному использованию экземпляра `ConnectionMultiplexer` в приложении нужно создать статическое свойство, которое возвращает подключенный экземпляр. Такой код потокобезопасно инициализирует только один подключенный экземпляр `ConnectionMultiplexer`. Для параметра `abortConnect` задано значение false. Это означает, что вызов завершается успешно, даже если подключение к кэшу Redis для Azure не установлено. Одна из ключевых особенностей `ConnectionMultiplexer` заключается в том, что этот параметр автоматически восстанавливает соединение с кэшем, когда устраняется проблема с сетью или другие проблемы.

Значение параметра *CacheConnection* в appSetting позволяет указать ссылку на строку подключения к кэшу через портал Azure в качестве параметра пароля.

## <a name="executing-cache-commands"></a>Выполнение команд кэша

Добавьте следующий код в процедуру `Main` класса `Program` для консольного приложения:

```csharp
        static void Main(string[] args)
        {
            // Connection refers to a property that returns a ConnectionMultiplexer
            // as shown in the previous example.
            IDatabase cache = lazyConnection.Value.GetDatabase();

            // Perform cache operations using the cache object...

            // Simple PING command
            string cacheCommand = "PING";
            Console.WriteLine("\nCache command  : " + cacheCommand);
            Console.WriteLine("Cache response : " + cache.Execute(cacheCommand).ToString());

            // Simple get and put of integral data types into the cache
            cacheCommand = "GET Message";
            Console.WriteLine("\nCache command  : " + cacheCommand + " or StringGet()");
            Console.WriteLine("Cache response : " + cache.StringGet("Message").ToString());

            cacheCommand = "SET Message \"Hello! The cache is working from a .NET console app!\"";
            Console.WriteLine("\nCache command  : " + cacheCommand + " or StringSet()");
            Console.WriteLine("Cache response : " + cache.StringSet("Message", "Hello! The cache is working from a .NET console app!").ToString());

            // Demostrate "SET Message" executed as expected...
            cacheCommand = "GET Message";
            Console.WriteLine("\nCache command  : " + cacheCommand + " or StringGet()");
            Console.WriteLine("Cache response : " + cache.StringGet("Message").ToString());

            // Get the client list, useful to see if connection list is growing...
            cacheCommand = "CLIENT LIST";
            Console.WriteLine("\nCache command  : " + cacheCommand);
            Console.WriteLine("Cache response : \n" + cache.Execute("CLIENT", "LIST").ToString().Replace("id=", "id="));

            lazyConnection.Value.Dispose();
        }
```

Кэши Redis для Azure могут использовать до 16 баз данных (их количество можно указать), чтобы обеспечить логическое разделение данных в кэше Redis. Этот код подключается к стандартной базе данных DB 0. Дополнительные сведения см. в разделах [What are Redis databases?](cache-configure.md#default-redis-server-configuration) (Что такое базы данных Redis) и [Конфигурация сервера Redis по умолчанию](cache-faq.md#what-are-redis-databases).

Элементы можно сохранять в кэше и извлекать из него с помощью методов `StringSet` и `StringGet`.

Redis хранит большинство данных в строках Redis, но эти строки могут содержать данные многих типов, включая сериализованные двоичные данные, которые можно использовать при сохранении объектов .NET в кэше.

Нажмите сочетание клавиш **CTRL+F5**, чтобы скомпилировать и запустить консольное приложение.

В приведенном ниже примере видно, что ключ `Message` ранее содержал кэшированное значение, установленное через консоль Redis на портале Azure. Приложение обновило кэшированное значение. Кроме того, оно выполнило команды `PING` и `CLIENT LIST`.

![Частичное консольное приложение](./media/cache-dotnet-how-to-use-azure-redis-cache/cache-console-app-partial.png)


## <a name="work-with-net-objects-in-the-cache"></a>Работа с объектами .NET в кэше

Кэш Redis для Azure может кэшировать объекты .NET и примитивные типы данных, но прежде чем объект .NET можно будет сохранить в кэше, он должен быть сериализован. За сериализацию объекта .NET отвечает разработчик приложения, что дает ему свободу в выборе сериализатора.

Простой способ сериализовать объекты — использовать методы сериализации `JsonConvert` из [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/) и выполнять сериализацию в нотацию JSON и из нее. В этом разделе вы добавите в кэш объект .NET.

В Visual Studio последовательно выберите **Инструменты** > **Диспетчер пакетов NuGet** > **Консоль диспетчера пакетов** и выполните следующую команду в открывшемся окне консоли менеджера пакетов.

```powershell
Install-Package Newtonsoft.Json
```

Добавьте следующую инструкцию `using` в начало файла *Program.cs*:

```charp
using Newtonsoft.Json;
```

Добавьте следующее определение класса `Employee` в файл *Program.cs*:

```csharp
        class Employee
        {
            public string Id { get; set; }
            public string Name { get; set; }
            public int Age { get; set; }

            public Employee(string EmployeeId, string Name, int Age)
            {
                this.Id = EmployeeId;
                this.Name = Name;
                this.Age = Age;
            }
        }
```

В нижней части процедуры `Main()` в файле *Program.cs* прямо перед вызовом `Dispose()` добавьте следующие строки кода, которые сохраняют и извлекают сериализованный объект .NET:

```csharp
            // Store .NET object to cache
            Employee e007 = new Employee("007", "Davide Columbo", 100);
            Console.WriteLine("Cache response from storing Employee .NET object : " + 
                cache.StringSet("e007", JsonConvert.SerializeObject(e007)));

            // Retrieve .NET object from cache
            Employee e007FromCache = JsonConvert.DeserializeObject<Employee>(cache.StringGet("e007"));
            Console.WriteLine("Deserialized Employee .NET object :\n");
            Console.WriteLine("\tEmployee.Name : " + e007FromCache.Name);
            Console.WriteLine("\tEmployee.Id   : " + e007FromCache.Id);
            Console.WriteLine("\tEmployee.Age  : " + e007FromCache.Age + "\n");
```

Нажмите сочетание клавиш **Ctrl+F5**, чтобы скомпилировать и запустить консольное приложение для проверки сериализации объектов .NET. 

![Готовое консольное приложение](./media/cache-dotnet-how-to-use-azure-redis-cache/cache-console-app-complete.png)


## <a name="clean-up-resources"></a>Очистка ресурсов

При переходе к следующему руководству в нем можно использовать ресурсы, созданные в этом руководстве.

В противном случае, если вы закончите работу с примером приложения из краткого руководства, вы можете удалить ресурсы Azure, созданные в текущем руководстве, чтобы избежать ненужных расходов. 

> [!IMPORTANT]
> Удаление группы ресурсов — необратимая операция, и все соответствующие ресурсы удаляются окончательно. Будьте внимательны, чтобы случайно не удалить не ту группу ресурсов или не те ресурсы. Если ресурсы для размещения этого примера созданы в имеющейся группе ресурсов, содержащей ресурсы, которые следует сохранить, можно удалить каждый ресурс отдельно в соответствующих колонках вместо удаления группы ресурсов.
>

Войдите на [портал Azure](https://portal.azure.com) и щелкните **Группы ресурсов**.

Введите имя группы ресурсов в текстовое поле **Фильтровать по имени...**. В инструкциях в этой статье использовалась группа ресурсов с именем *TestResources*. В своей группе ресурсов в списке результатов щелкните **...**, а затем **Удалить группу ресурсов**.

![Delete](./media/cache-dotnet-how-to-use-azure-redis-cache/cache-delete-resource-group.png)

Подтвердите операцию удаления группы ресурсов. Введите имя группы ресурсов и нажмите кнопку **Удалить**.

Через некоторое время группа ресурсов и все ее ресурсы будут удалены.



<a name="next-steps"></a>

## <a name="next-steps"></a>Дополнительная информация

Из этого краткого руководства вы узнали, как использовать кэш Redis для Azure из приложения .NET. Переходите к следующему краткому руководству по использованию кэша Redis с веб-приложением ASP.NET.

> [!div class="nextstepaction"]
> [Создание веб-приложения ASP.NET, использующего кэш Redis для Azure.](./cache-web-app-howto.md)


