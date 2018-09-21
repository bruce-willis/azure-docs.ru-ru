---
title: Как управлять подключениями и надежным обменом сообщениями с помощью пакетов SDK для устройств центра Интернета вещей Azure
description: Узнайте, как улучшить связь с устройствами и обмен сообщениями при использовании пакетов SDK для устройств центра Интернета вещей Azure.
services: iot-hub
keywords: ''
author: yzhong94
ms.author: yizhon
ms.date: 07/07/2018
ms.topic: article
ms.service: iot-hub
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: f6cfb7bb988b080189da444cb77e0c258417b1ac
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/14/2018
ms.locfileid: "45574714"
---
# <a name="how-to-manage-connectivity-and-reliable-messaging-using-azure-iot-hub-device-sdks"></a>Как управлять подключениями и надежным обменом сообщениями с помощью пакетов SDK для устройств центра Интернета вещей Azure

Это руководство содержит общие рекомендации по проектированию устойчивых приложений для устройств за счет преимуществ возможностей подключения и надежного обмена сообщениями, предоставляемых пакетами SDK для устройств Azure IoT. Цель этой статьи — помочь ответить на вопросы и решить следующие задачи:

- управление потерянным сетевым подключением;
- управление переключением между разными сетевыми подключениями;
- управление повторным подключением после ошибок временного подключения службы.

Реализация может быть разной в зависимости от языка. Дополнительные сведения можно найти в связанной документации по API или описании конкретного пакета SDK.

- [Пакет SDK для C, Python, iOS](https://github.com/azure/azure-iot-sdk-c)
- [ПАКЕТ SDK .NET](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/devdoc/requirements/retrypolicy.md)
- [пакет SDK для Java](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/devdoc/requirement_docs/com/microsoft/azure/iothub/retryPolicy.md)
- [Пакет SDK для Node](https://github.com/Azure/azure-iot-sdk-node/wiki/Connectivity-and-Retries#types-of-errors-and-how-to-detect-them)


## <a name="designing-for-resiliency"></a>Проектирование для обеспечения устойчивости

Устройства IoT часто используют непостоянные и (или) нестабильные сетевые подключения, например сети GSM или спутниковую связь. Кроме того, при взаимодействии с облачными службами могут возникать ошибки из-за временных условий, таких как прерывистая доступность служб и сбои уровня инфраструктуры (которые часто называют временными сбоями). Приложение, работающее на устройстве, должно управлять механизмами подключения и переподключения, а также логикой повтора для отправки и получения сообщений. Кроме того, требования стратегии повтора во многом основываются на сценарии Интернета вещей, в котором участвуют устройства, а также контекста и возможностей этих устройств.

Пакеты SDK для устройств центра Интернета вещей Azure предназначены для упрощения подключений и обмена данными между облаком и устройством. Они обеспечивают надежный и универсальный способ подключения и отправки сообщений в Центр Интернета вещей Azure или получения сообщений из него. Разработчики также могут изменить существующую реализацию, чтобы разработать подходящую стратегию повтора для заданного сценария.

Соответствующие возможности пакета SDK, которые поддерживают подключения и надежный обмен сообщениями, рассматриваются в следующих разделах.

## <a name="connection-and-retry"></a>Подключение и повторные попытки

В этом разделе рассматриваются схемы переподключения и повтора, которые можно использовать при управлении подключениями, руководство по реализации использования политики повтора в приложении для устройства и соответствующие интерфейсы API для пакетов SDK для устройств.

### <a name="error-patterns"></a>Шаблоны ошибок
Сбои подключения могут происходить на многих уровнях:

-  ошибки сети, например ошибки отключенных сокетов и ошибки разрешения имен;
- ошибки уровня протокола HTTP, AMQP и MQTT на транспортном уровне, такие как отключение каналов или истечение срока действия сеансов;
- ошибки уровня приложения, которые происходят либо из-за локальных ошибок, например недопустимых учетных данных, либо из-за поведения службы, например превышения квоты или регулирования.

Пакеты SDK для устройств позволяют обнаруживать ошибки на всех трех уровнях.  С помощью пакетов SDK для устройств невозможно обнаружить и обработать ошибки, связанные с ОС и оборудованием.  Они спроектированы на основе [рекомендаций по обработке временных сбоев](https://docs.microsoft.com/azure/architecture/best-practices/transient-faults#general-guidelines) из центра архитектуры Azure.

### <a name="retry-patterns"></a>Шаблоны повтора

Ниже приведен общий процесс повтора при обнаружении ошибок подключения. 
1. Пакет SDK обнаруживает ошибку и соответствующую ошибку в сети, протоколе или приложении.
2. В зависимости от типа ошибки пакет SDK использует фильтр ошибок, чтобы определить, требуется ли выполнить повторную попытку.  Если пакет SDK выявляет **неустранимую ошибку**, операции (подключение, а также отправка и получение) останавливаются и пакет SDK уведомляет пользователя. Неустранимая ошибка — это ошибка, которую пакет SDK может определить, установив, что ее невозможно устранить. Например, это может быть ошибка аутентификации или ошибка из-за неправильный конечной точки.
3. Если обнаружена **устранимая ошибка**, пакет SDK выполняет повторные попытки, используя политику повтора, пока не истечет указанное время ожидания.
4. По истечении указанного времени ожидания пакет SDK прекращает попытки подключения или отправки и уведомляет пользователя.
5.  Пакет SDK позволяет пользователю подключить обратный вызов для получения изменений состояния подключения. 

Предоставляются три политики повтора.
- **Экспоненциальная задержка с дрожанием**: это применяемая по умолчанию политика повтора.  Сначала она действует агрессивно, постепенно замедляется и затем использует максимальную задержку, которая не может быть превышена.  Она спроектирована на основе [рекомендаций по логике повтора из центра архитектуры Azure](https://docs.microsoft.com/azure/architecture/best-practices/retry-service-specific).
- **Пользовательский повтор**: можно реализовать пользовательскую политику повтора и вставить ее в RetryPolicy в зависимости от выбранного языка. Можно спроектировать политику повтора, которая подходит для вашего сценария.  Это недоступно в пакете SDK для C.
- **Без повтора**: имеется возможность задать политику повтора "без повтора", тем самым отключив логику повтора.  Пакет SDK один раз пытается подключиться и, если подключение установлено, один раз пытается отправить сообщение. Эта политика обычно используются, когда существуют проблемы, связанные пропускной способностью или расходами.   Если выбран этот вариант, то сообщения, которые не удается отправить, теряются без возможности восстановления. 

### <a name="retry-policy-apis"></a>Интерфейсы API политики повтора

   | SDK | Метод SetRetryPolicy | Реализации политики | Рекомендации по реализации |
   |-----|----------------------|--|--|
   |  C, Python, iOS  | [IOTHUB_CLIENT_RESULT IoTHubClient_SetRetryPolicy](https://github.com/Azure/azure-iot-sdk-c/blob/2018-05-04/iothub_client/inc/iothub_client.h#L188)        | **По умолчанию**: [IOTHUB_CLIENT_RETRY_EXPONENTIAL_BACKOFF](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-retry-policies).<BR>**Пользовательская политика:** используйте доступную политику [retryPolicy](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-retry-policies).<BR>**Без повтора:** [IOTHUB_CLIENT_RETRY_NONE](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-retry-policies).  | [Реализация на C, Python, iOS](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#)  |
   | Java| [SetRetryPolicy](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device._device_client_config.setretrypolicy?view=azure-java-stable)        | **По умолчанию**: [класс ExponentialBackoffWithJitter](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/NoRetry.java).<BR>**Пользовательская**: реализуйте [интерфейс RetryPolicy](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/RetryPolicy.java).<BR>**Без повтора:** [класс NoRetry](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/NoRetry.java).  | [Реализация для Java](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/devdoc/requirement_docs/com/microsoft/azure/iothub/retryPolicy.md) |[ПАКЕТ SDK .NET](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/devdoc/requirements/retrypolicy.md)
   | .NET| [DeviceClient.SetRetryPolicy](/dotnet/api/microsoft.azure.devices.client.deviceclient.setretrypolicy?view=azure-dotnet#Microsoft_Azure_Devices_Client_DeviceClient_SetRetryPolicy_Microsoft_Azure_Devices_Client_IRetryPolicy) | **По умолчанию**: [класс ExponentialBackoff](/dotnet/api/microsoft.azure.devices.client.exponentialbackoff?view=azure-dotnet).<BR>**Пользовательская**: реализуйте [интерфейс IRetryPolicy](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.iretrypolicy?view=azure-dotnet).<BR>**Без повтора:** [класс NoRetry](/dotnet/api/microsoft.azure.devices.client.noretry?view=azure-dotnet). | [Реализация для C#](https://github.com/Azure/azure-iot-sdk-csharp) |
   | Узел| [setRetryPolicy](/javascript/api/azure-iot-device/client?view=azure-iot-typescript-latest#azure_iot_device_Client_setRetryPolicy) | **По умолчанию**: [класс ExponentialBackoffWithJitter](/javascript/api/azure-iot-common/exponentialbackoffwithjitter?view=azure-iot-typescript-latest).<BR>**Пользовательская**: реализуйте [интерфейс RetryPolicy](/javascript/api/azure-iot-common/retrypolicy?view=azure-iot-typescript-latest).<BR>**Без повтора:** [класс NoRetry](/javascript/api/azure-iot-common/noretry?view=azure-iot-typescript-latest). | [Реализация для Node](https://github.com/Azure/azure-iot-sdk-node/wiki/Connectivity-and-Retries#types-of-errors-and-how-to-detect-them) |
   

Ниже приведены примеры кода, иллюстрирующие этот процесс. 

#### <a name="net-implementation-guidance"></a>Рекомендации по реализации для .NET

В следующем примере кода показано, как определить и задать политику повтора по умолчанию.

   ```csharp
   # define/set default retry policy
   RetryPolicy retryPolicy = new ExponentialBackoff(int.MaxValue, TimeSpan.FromMilliseconds(100), TimeSpan.FromSeconds(10), TimeSpan.FromMilliseconds(100));
   SetRetryPolicy(retryPolicy);
   ```

Во избежание высокой загрузки ЦП повторные попытки регулируются, если выполнение кода мгновенно завершается сбоем (например, когда нет подключения к сети или маршрута к назначению), поэтому минимальное время до выполнения следующей попытки составляет 1 секунду. 

Если служба возвращает ошибку регулирования, значит, политика повтора отличается и ее невозможно изменить с помощью общедоступного API.

   ```csharp
   # throttled retry policy
   RetryPolicy retryPolicy = new ExponentialBackoff(RetryCount, TimeSpan.FromSeconds(10), TimeSpan.FromSeconds(60), TimeSpan.FromSeconds(5));
   SetRetryPolicy(retryPolicy);
   ```

Механизм повтора будет остановлен по истечении интервала `DefaultOperationTimeoutInMilliseconds`, который сейчас составляет 4 минуты.

#### <a name="other-languages-implementation-guidance"></a>Рекомендации по реализации для других языков
Документация по реализации для других языков приведена ниже.  Примеры, демонстрирующие использование интерфейсов API политики повтора, предоставлены в репозитории.
- [Пакет SDK для C, Python, iOS](https://github.com/azure/azure-iot-sdk-c)
- [ПАКЕТ SDK .NET](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/devdoc/requirements/retrypolicy.md)
- [пакет SDK для Java](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/devdoc/requirement_docs/com/microsoft/azure/iothub/retryPolicy.md)
- [Пакет SDK для Node](https://github.com/Azure/azure-iot-sdk-node/wiki/Connectivity-and-Retries#types-of-errors-and-how-to-detect-them)

## <a name="next-steps"></a>Дополнительная информация
- [Использование пакетов SDK для устройств и служб](.\iot-hub-devguide-sdks.md)
- [Использование пакета SDK для устройств Azure IoT для C](.\iot-hub-device-sdk-c-intro.md)
- [Разработка приложений для устройств с ограниченным ресурсами](.\iot-hub-devguide-develop-for-constrained-devices.md)
- [Разработка приложений для мобильных устройств](.\iot-hub-how-to-develop-for-mobile-devices.md)
- [Detect and troubleshoot disconnects with Azure IoT Hub](iot-hub-troubleshoot-connectivity.md) (Обнаружение и устранение неполадок отключения Центра Интернета вещей Azure)
