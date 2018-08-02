---
title: Удаленное взаимодействие в службе Service Fabric с помощью C# | Документы Майкрософт
description: Удаленное взаимодействие Service Fabric позволяет осуществлять обмен данными между службами и клиентами и службами C# с помощью удаленного вызова процедур.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: BharatNarasimman
ms.assetid: abfaf430-fea0-4974-afba-cfc9f9f2354b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 09/20/2017
ms.author: vturecek
ms.openlocfilehash: e7652fe1b211e6811a4a3aa61bc2aa9d2f529dda
ms.sourcegitcommit: 30221e77dd199ffe0f2e86f6e762df5a32cdbe5f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/23/2018
ms.locfileid: "39205822"
---
# <a name="service-remoting-in-c-with-reliable-services"></a>Удаленное взаимодействие службы с Reliable Services в C#
> [!div class="op_single_selector"]
> * [C# в Windows](service-fabric-reliable-services-communication-remoting.md)
> * [Java в Linux](service-fabric-reliable-services-communication-remoting-java.md)
>
>

Для служб, которые не привязаны к определенному протоколу обмена данными или стеку, например веб-API, Windows Communication Foundation (WCF) и другим, платформа Reliable Services предоставляет механизм удаленного взаимодействия для быстрой и простой настройки удаленного вызова процедур. В этой статье рассматривается, как настраивать удаленные вызовы процедур для служб на C#.

## <a name="set-up-remoting-on-a-service"></a>Настройка удаленного взаимодействия в службе
Процесс настройки удаленного доступа для службы состоит из двух простых этапов.

1. Создание интерфейса для реализации в службе. Этот интерфейс определяет методы, которые будут доступны для удаленного вызова процедур в службе. Эти методы должны быть асинхронными методами, возвращающими задачи. Интерфейс должен реализовать `Microsoft.ServiceFabric.Services.Remoting.IService` , чтобы показать, что служба имеет интерфейс удаленного взаимодействия.
2. Используйте прослушиватель удаленного взаимодействия в службе. Прослушиватель удаленного взаимодействия — это реализация `ICommunicationListener`, которая предоставляет возможности удаленного взаимодействия. Пространство имен `Microsoft.ServiceFabric.Services.Remoting.Runtime` содержит метод расширения `CreateServiceRemotingListener` для служб без отслеживания и с отслеживанием состояния, который можно использовать, чтобы создать прослушиватель удаленного взаимодействия с использованием транспортного протокола удаленного взаимодействия по умолчанию.

>[!NOTE]
>Пространство имен `Remoting` доступно как отдельный пакет NuGet `Microsoft.ServiceFabric.Services.Remoting`.

Например, приведенная ниже служба без отслеживания состояния предоставляет один метод для получения "Hello World" посредством удаленного вызова процедуры.

```csharp
using Microsoft.ServiceFabric.Services.Communication.Runtime;
using Microsoft.ServiceFabric.Services.Remoting;
using Microsoft.ServiceFabric.Services.Remoting.Runtime;
using Microsoft.ServiceFabric.Services.Runtime;

public interface IMyService : IService
{
    Task<string> HelloWorldAsync();
}

class MyService : StatelessService, IMyService
{
    public MyService(StatelessServiceContext context)
        : base (context)
    {
    }

    public Task<string> HelloWorldAsync()
    {
        return Task.FromResult("Hello!");
    }

    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
     return this.CreateServiceRemotingInstanceListeners();
    }
}
```
> [!NOTE]
> Аргументы и возвращаемые данные в интерфейсе службы могут иметь простые, сложные или настраиваемые типы, однако они должны быть сериализуемыми с помощью объекта .NET [DataContractSerializer](https://msdn.microsoft.com/library/ms731923.aspx).
>
>

## <a name="call-remote-service-methods"></a>Вызов удаленных методов службы
Вызов методов в службе с помощью стека удаленного взаимодействия осуществляется с помощью локального прокси-сервера для службы через класс `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxy` . Метод `ServiceProxy` создает локальный прокси-сервер, используя тот же интерфейс, который реализует служба. С помощью этого прокси-сервера можно удаленно вызвать методы в интерфейсе.

```csharp

IMyService helloWorldClient = ServiceProxy.Create<IMyService>(new Uri("fabric:/MyApplication/MyHelloWorldService"));

string message = await helloWorldClient.HelloWorldAsync();

```

Платформа удаленного взаимодействия распространяет исключения, созданные службой, на клиент. Поэтому при использовании метода `ServiceProxy` клиент отвечает за обработку исключений, которые создает служба.

## <a name="service-proxy-lifetime"></a>Время существования ServiceProxy
Создание ServiceProxy не требует больших ресурсов, поэтому вы можете создавать такие объекты в любых количествах. Экземпляры ServiceProxy можно использовать повторно. Если удаленный вызов процедуры создает исключение, вы по-прежнему можете использовать тот же экземпляр прокси-сервера. Каждый объект ServiceProxy содержит клиент обмена данными, используемый для отправки сообщений по сети. При запуске удаленных вызовов проводятся внутренние проверки для определения работоспособности этого клиента. В зависимости от результата проверки клиент может быть создан повторно. Поэтому, если возникает исключение, вам не нужно заново создавать `ServiceProxy`.

### <a name="serviceproxyfactory-lifetime"></a>Время существования ServiceProxyFactory
[ServiceProxyFactory](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.client.serviceproxyfactory) — это фабрика, которая создает экземпляры прокси-сервера для различных интерфейсов удаленного взаимодействия. Если для создания прокси-сервера вы используете API `ServiceProxy.Create`, платформа создает одноэлементный ServiceProxy.
При необходимости переопределить свойства [IServiceRemotingClientFactory](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v1.client.iserviceremotingclientfactory) имеет смысл создать фабрику вручную.
Создание фабрики — ресурсоемкая операция. ServiceProxyFactory хранит внутренний кэш клиента обмена данными.
Рекомендуется кэшировать ServiceProxyFactory на как можно больший период времени.

## <a name="remoting-exception-handling"></a>Обработка исключений удаленного взаимодействия
Все исключения удаленного взаимодействия, порождаемые API службы, отправляются обратно в клиент как AggregateException. Исключения RemoteException должны быть сериализуемыми в формат DataContract. В противном случае API прокси-сервера получит исключение [ServiceException](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.communication.serviceexception) с ошибкой сериализации.

Объект ServiceProxy обрабатывает все исключения отработки отказа для секции службы, для которого он создан. Он повторно разрешает конечные точки в случае исключений отработки отказа (повторяющихся исключений) и повторяет вызов к правильной конечной точке. Число повторных попыток для исключений отработки отказа не ограничено.
Если порождаются временные исключения, прокси-сервер повторяет вызов.

Параметры повтора по умолчанию определяются [OperationRetrySettings](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.communication.client.operationretrysettings).

Пользователь может настроить эти значения, передав объект OperationRetrySettings в конструктор ServiceProxyFactory.

## <a name="how-to-use-the-remoting-v2-stack"></a>Как использовать стек удаленного взаимодействия версии 2

Пакет NuGet для удаленного взаимодействия версии 2.8 позволяет использовать стек удаленного взаимодействия версии 2. Стек удаленного взаимодействие версии 2 обеспечивает более высокую производительность, а также предоставляет такие функции, как настраиваемая сериализация и дополнительные подключаемые API.
Код шаблона продолжает использовать стек удаленного взаимодействия версии 1.
Стек удаленного взаимодействия версии 2 несовместим со стеком версии 1 (предыдущим стеком удаленного взаимодействия), поэтому выполните инструкции, чтобы [обновить версию 1 до версии 2](#how-to-upgrade-from-remoting-v1-to-remoting-v2) без ущерба для доступности службы.

Можно использовать следующий подход для включения стека версии 2.

### <a name="using-an-assembly-attribute-to-use-the-v2-stack"></a>Использование атрибута сборки для стека версии 2

Эти действия изменяют код шаблона, чтобы можно было использовать стек версии 2 с помощью атрибута сборки.

1. Измените ресурс конечной точки с `"ServiceEndpoint"` на `"ServiceEndpointV2"` в манифесте служб.

  ```xml
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2" />
    </Endpoints>
  </Resources>
  ```

2. Используйте метод расширения `Microsoft.ServiceFabric.Services.Remoting.Runtime.CreateServiceRemotingInstanceListeners`, чтобы создать прослушивателей удаленного взаимодействия (одинаково для версии 1 и 2).

  ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
  ```

3. Пометьте сборку с удаленными интерфейсами атрибутом `FabricTransportServiceRemotingProvider`.

  ```csharp
  [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2, RemotingClientVersion = RemotingClientVersion.V2)]
  ```

Вносить изменения в проект клиента не требуется.
Выполните сборку клиента со сборкой интерфейса, чтобы обеспечить использование приведенного выше атрибута сборки.

### <a name="using-explicit-v2-classes-to-use-the-v2-stack"></a>Использования явных классов версии 2 для использования стека версии 2

Вместо атрибута сборки можно использовать явные классы версии 2, чтобы включить стек версии 2.

Эти действия изменяют код шаблона, чтобы можно было использовать стек версии 2 с помощью явных классов версии 2.

1. Измените ресурс конечной точки с `"ServiceEndpoint"` на `"ServiceEndpointV2"` в манифесте служб.

  ```xml
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2" />
    </Endpoints>
  </Resources>
  ```

2. Используйте [FabricTransportServiceRemotingListener](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.runtime.fabrictransportserviceremotingListener?view=azure-dotnet) из пространства имен `Microsoft.ServiceFabric.Services.Remoting.V2.FabricTransport.Runtime`.

  ```csharp
  protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return new[]
        {
            new ServiceInstanceListener((c) =>
            {
                return new FabricTransportServiceRemotingListener(c, this);

            })
        };
    }
  ```

3. Используйте [FabricTransportServiceRemotingClientFactory ](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.client.fabrictransportserviceremotingclientfactory?view=azure-dotnet) из пространства имен `Microsoft.ServiceFabric.Services.Remoting.V2.FabricTransport.Client`, чтобы создать клиенты.

  ```csharp
  var proxyFactory = new ServiceProxyFactory((c) =>
          {
              return new FabricTransportServiceRemotingClientFactory();
          });
  ```

## <a name="how-to-upgrade-from-remoting-v1-to-remoting-v2"></a>Как выполнить обновление службы удаленного взаимодействия версии 1 до версии 2
Чтобы обновить версию 1 до версии 2, требуются выполнить двухэтапное обновление. Требуется выполнить указанные действия в приведенном порядке.

1. Обновите службу версии 1 до версии 2 с помощью атрибута ниже.
Это изменение гарантирует, что служба будет ожидать передачи данных от прослушивателей версии 1 и 2.

    а) Добавьте ресурс конечной точки, например ServiceEndpointV2, в манифест службы.
      ```xml
      <Resources>
        <Endpoints>
          <Endpoint Name="ServiceEndpointV2" />  
        </Endpoints>
      </Resources>
      ```

    б) Используйте приведенный ниже метод расширения удаленного взаимодействия для создания прослушивателя удаленного взаимодействия.

    ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
    ```

    в) добавьте атрибут сборки в интерфейсы удаленного взаимодействия для использования прослушивателя версии 1 и 2 и клиента версии 2.
    ```csharp
    [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2|RemotingListenerVersion.V1, RemotingClientVersion = RemotingClientVersion.V2)]

      ```
2. Обновите клиент версии 1 до версии 2 с помощью атрибута клиента версии 2.
Это гарантирует, что клиент будет использовать стек версии 2.
Никаких изменений в проект службы или клиента вносить не требуется. Достаточно будет выполнить сборку проектов клиента со обновленной сборкой интерфейса.

3. Этот шаг не является обязательным. Используйте атрибут прослушивателя версии 2 и обновите службу версии 2.
Это гарантирует, что служба будет ожидать передачи данных только от прослушивателя версии 2.

```csharp
[assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2, RemotingClientVersion = RemotingClientVersion.V2)]
```


## <a name="how-to-use-remoting-v2interfacecompatible-stack"></a>Как использовать стек удаленного взаимодействия версии 2 (InterfaceCompatible)
 В стеке удаленного взаимодействия версии 2 (InterfaceCompatible версии 2_1) есть все функции стека удаленного взаимодействия версии 2. Он совместим с интерфейсом стека удаленного взаимодействия версии 1, но не поддерживает обратную совместимость с версией 2 и версией 1. Чтобы выполнить обновление версии 1 до версии 2_1, не влияя на доступность службы, см. действие в разделе [Как обновить версию 1 до версии 2 (InterfaceCompatible)](#how-to-upgrade-from-remoting-v1-to-remoting-v2interfacecompatible) ниже.


### <a name="using-assembly-attribute-to-use-remoting-v2interfacecompatible-stack"></a>Использование атрибута сборки для стека удаленного взаимодействия версии 2 (InterfaceCompatible)

Ниже приведены инструкции по переходу на использование стека версии 2_1.

1. Добавьте ресурс конечной точки, например ServiceEndpointV2_1, в манифест службы.

  ```xml
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2_1" />  
    </Endpoints>
  </Resources>
  ```

2.  Используйте метод расширения удаленного взаимодействия для создания прослушивателя удаленного взаимодействия.

  ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
  ```

3.  Добавьте [атрибут сборки](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.fabrictransport.fabrictransportserviceremotingproviderattribute?view=azure-dotnet) в интерфейсы удаленного взаимодействия.

  ```csharp
    [assembly:  FabricTransportServiceRemotingProvider(RemotingListenerVersion=  RemotingListenerVersion.V2_1, RemotingClientVersion= RemotingClientVersion.V2_1)]

  ```
Вносить изменения проект клиента не требуется.
Выполните сборку клиента со сборкой интерфейса, чтобы обеспечить использование приведенного выше атрибута сборки.

### <a name="using-explicit-remoting-classes-to-create-listener-clientfactory-for-v2interfacecompatible-version"></a>Использование явных классов удаленного взаимодействия для создания прослушивателя и фабрики клиента для версии 2
Ниже приведены инструкции, которые нужно выполнить.
1.  Добавьте ресурс конечной точки, например ServiceEndpointV2_1, в манифест службы.

  ```xml
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2_1" />  
    </Endpoints>
  </Resources>
  ```

2. Используйте [прослушиватель удаленного взаимодействия версии 2](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.runtime.fabrictransportserviceremotinglistener?view=azure-dotnet). Используемое имя ресурса конечной точки службы по умолчанию — ServiceEndpointV2_1. Оно должны быть определено в манифесте службы.

  ```csharp
  protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return new[]
        {
            new ServiceInstanceListener((c) =>
            {
                var settings = new FabricTransportRemotingListenerSettings();
                settings.UseWrappedMessage = true;
                return new FabricTransportServiceRemotingListener(c, this,settings);

            })
        };
    }
  ```

3. Используйте [фабрику клиента](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.client.fabrictransportserviceremotingclientfactory?view=azure-dotnet) версии 2.
  ```csharp
  var proxyFactory = new ServiceProxyFactory((c) =>
          {
            var settings = new FabricTransportRemotingSettings();
            settings.UseWrappedMessage = true;
            return new FabricTransportServiceRemotingClientFactory(settings);
          });
  ```

## <a name="how-to-upgrade-from-remoting-v1-to-remoting-v2interfacecompatible"></a>Как выполнить обновление службы удаленного взаимодействия версии 1 до версии 2 (InterfaceCompatible).
Чтобы обновить версию 1 до версии 2 (InterfaceCompatible 2_1), требуются выполнить двухэтапное обновление. Требуется выполнить указанные действия в приведенном порядке.

1. Обновите службу версии 1 до версии 2_1 с помощью атрибута ниже.
Это изменение гарантирует, что служба будет ожидать передачи данных от прослушивателей версии 1 и 2_1.

    а) добавьте ресурс конечной точки, например ServiceEndpointV2_1, в манифест службы;
      ```xml
      <Resources>
        <Endpoints>
          <Endpoint Name="ServiceEndpointV2_1" />  
        </Endpoints>
      </Resources>
      ```

    б) Используйте приведенный ниже метод расширения удаленного взаимодействия для создания прослушивателя удаленного взаимодействия.

    ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
    ```

    в) добавьте атрибут сборки в интерфейсы удаленного взаимодействия для использования прослушивателя версии 1 и 2_1 и клиента версии 2_1.
    ```csharp
   [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1 | RemotingListenerVersion.V1, RemotingClientVersion = RemotingClientVersion.V2_1)]

      ```
2. Обновите клиент версии 1 до версии 2_1 с помощью атрибута клиента версии 2_1.
Это гарантирует, что клиент будет использовать стек версии 2_1.
Никаких изменений в проект службы или клиента вносить не требуется. Достаточно будет выполнить сборку проектов клиента со обновленной сборкой интерфейса.

3. Этот шаг не является обязательным. Удалите атрибут прослушивателя версии 1 и обновите службу версии 2.
Это гарантирует, что служба будет ожидать передачи данных только от прослушивателя версии 2.

```csharp
[assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1, RemotingClientVersion = RemotingClientVersion.V2_1)]
```

### <a name="how-to-use-custom-serialization-with-remoting-wrapped-message"></a>Как использовать настраиваемую сериализацию с упакованным сообщением удаленного взаимодействия
В упакованном сообщении удаленного взаимодействия мы создаем отдельный упакованный объект со всеми параметрами в качестве содержащегося в нем поля.
Выполните следующие действия.

1. Реализуйте интерфейс IServiceRemotingMessageSerializationProvider для реализации настраиваемой сериализации.
    Ниже приведен фрагмент кода, содержащий данную реализацию.

      ```csharp
      public class ServiceRemotingJsonSerializationProvider : IServiceRemotingMessageSerializationProvider
      {
        public IServiceRemotingMessageBodyFactory CreateMessageBodyFactory()
        {
          return new JsonMessageFactory();
        }

        public IServiceRemotingRequestMessageBodySerializer CreateRequestMessageSerializer(Type serviceInterfaceType, IEnumerable<Type> requestWrappedType, IEnumerable<Type> requestBodyTypes = null)
        {
          return new ServiceRemotingRequestJsonMessageBodySerializer();
        }

        public IServiceRemotingResponseMessageBodySerializer CreateResponseMessageSerializer(Type serviceInterfaceType, IEnumerable<Type> responseWrappedType, IEnumerable<Type> responseBodyTypes = null)
        {
          return new ServiceRemotingResponseJsonMessageBodySerializer();
        }
      }
      ```
      ```csharp
        class JsonMessageFactory : IServiceRemotingMessageBodyFactory
            {

              public IServiceRemotingRequestMessageBody CreateRequest(string interfaceName, string methodName, int numberOfParameters, object wrappedRequestObject)
              {
                return new JsonBody(wrappedRequestObject);
              }

              public IServiceRemotingResponseMessageBody CreateResponse(string interfaceName, string methodName, object wrappedRequestObject)
              {
                return new JsonBody(wrappedRequestObject);
              }
            }
      ```
      ```csharp
      class ServiceRemotingRequestJsonMessageBodySerializer : IServiceRemotingRequestMessageBodySerializer
        {
            private JsonSerializer serializer;

            public ServiceRemotingRequestJsonMessageBodySerializer()
            {
              serializer = JsonSerializer.Create(new JsonSerializerSettings()
              {
                TypeNameHandling = TypeNameHandling.All
                });
              }

              public IOutgoingMessageBody Serialize(IServiceRemotingRequestMessageBody serviceRemotingRequestMessageBody)
             {
               if (serviceRemotingRequestMessageBody == null)
               {
                 return null;
               }          
               using (var writeStream = new MemoryStream())
               {
                 using (var jsonWriter = new JsonTextWriter(new StreamWriter(writeStream)))
                 {
                   serializer.Serialize(jsonWriter, serviceRemotingRequestMessageBody);
                   jsonWriter.Flush();
                   var bytes = writeStream.ToArray();
                   var segment = new ArraySegment<byte>(bytes);
                   var segments = new List<ArraySegment<byte>> { segment };
                   return new OutgoingMessageBody(segments);
                 }
               }
              }

              public IServiceRemotingRequestMessageBody Deserialize(IIncomingMessageBody messageBody)
             {
               using (var sr = new StreamReader(messageBody.GetReceivedBuffer()))
               {
                 using (JsonReader reader = new JsonTextReader(sr))
                 {
                   var ob = serializer.Deserialize<JsonBody>(reader);
                   return ob;
                 }
               }
             }
            }
      ```
      ```csharp
      class ServiceRemotingResponseJsonMessageBodySerializer : IServiceRemotingResponseMessageBodySerializer
       {
         private JsonSerializer serializer;

        public ServiceRemotingResponseJsonMessageBodySerializer()
        {
          serializer = JsonSerializer.Create(new JsonSerializerSettings()
          {
              TypeNameHandling = TypeNameHandling.All
            });
          }

          public IOutgoingMessageBody Serialize(IServiceRemotingResponseMessageBody responseMessageBody)
          {
            if (responseMessageBody == null)
            {
              return null;
            }

            using (var writeStream = new MemoryStream())
            {
              using (var jsonWriter = new JsonTextWriter(new StreamWriter(writeStream)))
              {
                serializer.Serialize(jsonWriter, responseMessageBody);
                jsonWriter.Flush();
                var bytes = writeStream.ToArray();
                var segment = new ArraySegment<byte>(bytes);
                var segments = new List<ArraySegment<byte>> { segment };
                return new OutgoingMessageBody(segments);
              }
            }
          }

          public IServiceRemotingResponseMessageBody Deserialize(IIncomingMessageBody messageBody)
          {

             using (var sr = new StreamReader(messageBody.GetReceivedBuffer()))
             {
               using (var reader = new JsonTextReader(sr))
               {
                 var obj = serializer.Deserialize<JsonBody>(reader);
                 return obj;
               }
             }
           }
       }
    ```
    ```csharp
    class JsonBody : WrappedMessage, IServiceRemotingRequestMessageBody, IServiceRemotingResponseMessageBody
    {
          public JsonBody(object wrapped)
          {
            this.Value = wrapped;
          }

          public void SetParameter(int position, string parameName, object parameter)
          {  //Not Needed if you are using WrappedMessage
            throw new NotImplementedException();
          }

          public object GetParameter(int position, string parameName, Type paramType)
          {
            //Not Needed if you are using WrappedMessage
            throw new NotImplementedException();
          }

          public void Set(object response)
          { //Not Needed if you are using WrappedMessage
            throw new NotImplementedException();
          }

          public object Get(Type paramType)
          {  //Not Needed if you are using WrappedMessage
            throw new NotImplementedException();
          }
    }
    ```

2.    Переопределите поставщик сериализации по умолчанию, указав JsonSerializationProvider для прослушивателя удаленного взаимодействия.

  ```csharp
  protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
   {
       return new[]
       {
           new ServiceInstanceListener((c) =>
           {
               return new FabricTransportServiceRemotingListener(c, this,
                   new ServiceRemotingJsonSerializationProvider());
           })
       };
   }
  ```

3.    Переопределите поставщик сериализации по умолчанию, указав JsonSerializationProvider для фабрики клиента удаленного взаимодействия.

```csharp
var proxyFactory = new ServiceProxyFactory((c) =>
{
    return new FabricTransportServiceRemotingClientFactory(
    serializationProvider: new ServiceRemotingJsonSerializationProvider());
  });
  ```
## <a name="next-steps"></a>Дополнительная информация
* [Веб-API с OWIN в модели Reliable Services](service-fabric-reliable-services-communication-webapi.md)
* [Взаимодействие WCF с Reliable Services](service-fabric-reliable-services-communication-wcf.md)
* [Защита обмена данными для Reliable Services](service-fabric-reliable-services-secure-communication.md)
