---
title: Отправка событий в Центры событий Azure с помощью Go | Документация Майкрософт
description: Приступая к отправке событий в Центры событий с помощью Go
services: event-hubs
author: ShubhaVijayasarathy
manager: kamalb
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 07/23/2018
ms.author: shvija
ms.openlocfilehash: 40b3aa82c3e9e8ab9a30362c0a41998877655725
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/09/2018
ms.locfileid: "40005603"
---
# <a name="send-events-to-event-hubs-using-go"></a>Отправка событий в Центры событий с помощью Go

Концентраторы событий Azure — это высокомасштабируемая система управления событиями, которая может принимать миллионы событий в секунду, позволяя приложениям обрабатывать и анализировать большие объемы данных, сформированных подключенными устройствами и другими системами. После сбора событий в концентратор событий вы можете получать и обрабатывать события с помощью внутрипроцессных обработчиков или перенаправлять их в другие аналитические системы.

Дополнительные сведения о концентраторах событий см. в статье [Что такое концентраторы событий Azure?][Event Hubs overview].

В этом руководстве представлены инструкции по отправке событий в концентратор событий из приложения, написанного на Go. Для получения событий используйте пакет**Go eph** (Узел обработчика событий), как описано в [соответствующей статье о получении](event-hubs-go-get-started-receive-eph.md).

Из [этих примеров GitHub](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/eventhubs) в руководство взят код, который можно проверить, чтобы увидеть полное рабочее приложение, включая инструкции импорта и объявления переменных.

Другие примеры доступны в [репозитории пакетов концентраторов событий](https://github.com/Azure/azure-event-hubs-go/tree/master/_examples).

## <a name="prerequisites"></a>Предварительные требования

Для работы с данным руководством вам потребуется:

* Локально установленный Go. При необходимости выполните [следующие инструкции](https://golang.org/doc/install).
* Активная учетная запись Azure. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure][], прежде чем начинать работу.
* Существующее пространство имен Центров событий и концентратор событий. Эти сущности можно создать, следуя инструкциям в [этой статье](event-hubs-create.md).

## <a name="install-go-package"></a>Установка пакета Go

Получить пакет Go для Центров событий с помощью `go get` или `dep`. Например: 

```bash
go get -u github.com/Azure/azure-event-hubs-go
go get -u github.com/Azure/azure-amqp-common-go/...

# or

dep ensure -add github.com/Azure/azure-event-hubs-go
dep ensure -add github.com/Azure/azure-amqp-common-go
```

## <a name="import-packages-in-your-code-file"></a>Импорт пакетов в файл кода

Для импорта пакетов Go используйте следующий пример кода:

```go
import (
    aad "github.com/Azure/azure-amqp-common-go/aad"
    eventhubs "github.com/Azure/azure-event-hubs-go"
)
```

## <a name="create-service-principal"></a>Создание субъекта-службы

Создайте субъект-службу, следуя инструкциям в статье [Создание субъекта-службы Azure с помощью Azure CLI 2.0](/cli/azure/create-an-azure-service-principal-azure-cli). Сохраните предоставленные учетные данные в своей среде со следующими именами. Пакеты Azure SDK для Go и Центров событий предварительно настроенные для поиска этих имен переменных:

```bash
export AZURE_CLIENT_ID=
export AZURE_CLIENT_SECRET=
export AZURE_TENANT_ID=
export AZURE_SUBSCRIPTION_ID= 
```

Теперь создайте поставщик авторизации для клиента Центров событий, использующего эти учетные данные:

```go
tokenProvider, err := aad.NewJWTProvider(aad.JWTProviderWithEnvironmentVars())
if err != nil {
    log.Fatalf("failed to configure AAD JWT provider: %s\n", err)
}
```

## <a name="create-event-hubs-client"></a>Создание клиента Центров событий

В следующем коде создается клиент Центров событий:

```go
hub, err := eventhubs.NewHub("namespaceName", "hubName", tokenProvider)
ctx := context.WithTimeout(context.Background(), 10 * time.Second)
defer hub.Close(ctx)
if err != nil {
    log.Fatalf("failed to get hub %s\n", err)
}
```

## <a name="send-messages"></a>Отправка сообщений

В следующем фрагменте кода используйте (1) для отправки сообщений из терминала в интерактивном режиме или (2) для отправки сообщений в рамках программы:

```go
// 1. send messages at the terminal
ctx = context.Background()
reader := bufio.NewReader(os.Stdin)
for {
    fmt.Printf("Input a message to send: ")
    text, _ := reader.ReadString('\n')
    hub.Send(ctx, eventhubs.NewEventFromString(text))
}

// 2. send messages within program
ctx = context.Background()
hub.Send(ctx, eventhubs.NewEventFromString("hello Azure!")
```

## <a name="extras"></a>Дополнительно

Получите идентификаторы каждой секции в концентраторе событий:

```go
info, err := hub.GetRuntimeInformation(ctx)
if err != nil {
    log.Fatalf("failed to get runtime info: %s\n", err)
}
log.Printf("got partition IDs: %s\n, info.PartitionIDs)
```

## <a name="next-steps"></a>Дополнительная информация

Чтобы узнать больше о Центрах событий, посетите следующие страницы:

* [Получение событий из концентраторов событий с помощью EventProcessorHost](event-hubs-go-get-started-receive-eph.md)
* [Обзор концентраторов событий Azure][Event Hubs overview].
* [Создание концентратора событий](event-hubs-create.md)
* [Часто задаваемые вопросы о концентраторах событий](event-hubs-faq.md)

<!-- Links -->
[Event Hubs overview]: event-hubs-about.md
[создайте бесплатную учетную запись Azure]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
