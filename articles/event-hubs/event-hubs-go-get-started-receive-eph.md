---
title: Получение событий от концентраторов событий Azure с помощью Go | Документация Майкрософт
description: Узнайте основные сведения о получении событий из концентраторов событий с помощью Go
services: event-hubs
author: ShubhaVijayasarathy
manager: kamalb
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 07/23/2018
ms.author: shvija
ms.openlocfilehash: eaea6adbaef7baf9bb1e617ba0a709cf14edf781
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/09/2018
ms.locfileid: "40005584"
---
# <a name="receive-events-from-event-hubs-using-go"></a>Получение событий из концентраторов событий с помощью Go

Концентраторы событий Azure — это высокомасштабируемая система управления событиями, которая может принимать миллионы событий в секунду, позволяя приложениям обрабатывать и анализировать большие объемы данных, сформированных подключенными устройствами и другими системами. После сбора событий в концентратор событий вы можете получать и обрабатывать события с помощью внутрипроцессных обработчиков или перенаправлять их в другие аналитические системы.

Дополнительные сведения о концентраторах событий см. в статье [Что такое концентраторы событий Azure?][Event Hubs overview].

Это руководство содержит инструкции по получению событий из концентратора событий в приложение Go. Сведения об отправке событий см. в статье [Отправка событий в концентраторы событий с помощью Go](event-hubs-go-get-started-send.md).

Код в этом руководстве взят из [этих примеров в GitHub](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/eventhubs). Вы можете просмотреть эти примеры, чтобы увидеть полное рабочее приложение, включая инструкции импорта и объявления переменных.

Другие примеры доступны в [репозитории пакетов концентраторов событий](https://github.com/Azure/azure-event-hubs-go/tree/master/_examples).

## <a name="prerequisites"></a>Предварительные требования

Для работы с данным руководством вам потребуется:

* Локально установленный Go. При необходимости выполните [следующие инструкции](https://golang.org/doc/install).
* Активная учетная запись Azure. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure][], прежде чем начинать работу.
* В целевом концентраторе событий должны быть сообщения. Только так вы сможете получать сообщения. Узнайте, как отправлять сообщения, из [руководства по отправке](event-hubs-go-get-started-send.md).
* Существующий концентратор событий (см. следующий раздел).
* Существующая учетная запись хранения и контейнер (см. раздел после следующего).

### <a name="create-an-event-hub"></a>Создание концентратора событий

В этом руководстве предполагается наличие существующего пространства имен концентраторов событий и концентратора событий. Эти сущности можно создать, следуя инструкциям в [этой статье](event-hubs-create.md).

### <a name="create-a-storage-account-and-container"></a>Создание учетной записи хранения и контейнера

Такие состояния, как аренда разделов и контрольные точки в потоке событий, совместно используются получателями через контейнер службы хранилища Azure. Вы можете создать учетную запись хранения и контейнер с помощью пакета SDK для Go, но их также можно создать, следуя инструкциям в статье [Об учетных записях хранения Azure](../storage/common/storage-create-storage-account.md).

Примеры для создания артефактов хранилища с помощью пакета SDK для Go доступны в [репозитории примеров Go](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/storage) и в примере, прилагаемом к этому руководству.

## <a name="receive-messages"></a>Получение сообщений

Чтобы получать сообщения, получите пакеты Go для концентраторов событий с помощью `go get` или `dep`:

```bash
go get -u github.com/Azure/azure-event-hubs-go/...
go get -u github.com/Azure/azure-amqp-common-go/...
go get -u github.com/Azure/go-autorest/...

# or

dep ensure -add github.com/Azure/azure-event-hubs-go
dep ensure -add github.com/Azure/azure-amqp-common-go
dep ensure -add github.com/Azure/go-autorest
```

## <a name="import-packages-in-your-code-file"></a>Импорт пакетов в файл кода

Для импорта пакетов Go используйте следующий пример кода:

```go
import (
    aad "github.com/Azure/azure-amqp-common-go/aad"
    eventhubs "github.com/Azure/azure-event-hubs-go"
    eph "github.com/Azure/azure-event-hubs-go/eph"
    storageLeaser "github.com/Azure/azure-event-hubs-go/storage"
    azure "github.com/Azure/go-autorest/autorest/azure"
)
```

## <a name="create-service-principal"></a>Создание субъекта-службы

Создайте субъект-службу, следуя инструкциям в статье [Создание субъекта-службы Azure с помощью Azure CLI 2.0](/cli/azure/create-an-azure-service-principal-azure-cli). Сохраните предоставленные учетные данные в своей среде со следующими именами. Пакет Azure SDK для Go и пакет концентраторов событий предварительно настроены для поиска этих имен переменных.

```bash
export AZURE_CLIENT_ID=
export AZURE_CLIENT_SECRET=
export AZURE_TENANT_ID=
export AZURE_SUBSCRIPTION_ID= 
```

Затем создайте поставщика авторизации для вашего клиента концентраторов событий, который использует эти учетные данные:

```go
tokenProvider, err := aad.NewJWTProvider(aad.JWTProviderWithEnvironmentVars())
if err != nil {
    log.Fatalf("failed to configure AAD JWT provider: %s\n", err)
}
```

## <a name="get-metadata-struct"></a>Получение структуры метаданных

Получите структуру с метаданными о вашей среде Azure, используя пакет Azure SDK для Go. Последующие операции используют эту структуру для поиска нужных конечных точек.

```go
azureEnv, err := azure.EnvironmentFromName("AzurePublicCloud")
if err != nil {
    log.Fatalf("could not get azure.Environment struct: %s\n", err)
}
```

## <a name="create-credential-helper"></a>Создание вспомогательного приложения для учетных данных 

Создайте вспомогательное приложение, которое использует предыдущие учетные данные Azure Active Directory (AAD) для создания подписанного URL-адреса (SAS) для службы хранилища. Последний параметр предписывает конструктору использовать такие же переменные среды, как и ранее:

```go
cred, err := storageLeaser.NewAADSASCredential(
    subscriptionID,
    resourceGroupName,
    storageAccountName,
    storageContainerName,
    storageLeaser.AADSASCredentialWithEnvironmentVars())
if err != nil {
    log.Fatalf("could not prepare a storage credential: %s\n", err)
}
```

## <a name="create-checkpointer-and-leaser"></a>Создание средства создания конечных точек и выдачи аренд 

Создайте **средство выдачи аренд** (Leaser), ответственное за присвоение аренды на раздел для конкретного получателя, и **средство создания конечных точек** (Checkpointer), ответственное за запись контрольных точек для потока сообщений, чтобы другие получатели могли начать чтение из правильной позиции смещения.

В настоящее время доступно одно **StorageLeaserCheckpointer**. Оно использует один контейнер хранилища для управления арендой и контрольными точками. Помимо имен учетной записи и контейнера, средству **StorageLeaserCheckpointer** требуются учетные данные, созданные на предыдущем шаге, и структура среды Azure для правильного доступа к контейнеру.

```go
leaserCheckpointer, err := storageLeaser.NewStorageLeaserCheckpointer(
    cred,
    storageAccountName,
    storageContainerName,
    azureEnv)
if err != nil {
    log.Fatalf("could not prepare a storage leaserCheckpointer: %s\n", err)
}
```

## <a name="construct-event-processor-host"></a>Создание узла обработчика событий

Теперь у вас есть компоненты для создания узла обработчика событий следующим образом. Тот же **StorageLeaserCheckpointer** используется и в качестве средства выдачи аренд, и в качестве средства создания контрольных точек, как описано ранее:

```go
ctx := context.Background()
p, err := eph.New(
    ctx,
    nsName,
    hubName,
    tokenProvider,
    leaserCheckpointer,
    leaserCheckpointer)
if err != nil {
    log.Fatalf("failed to create EPH: %s\n", err)
}
defer p.Close(context.Background())
```

## <a name="create-handler"></a>Создание обработчика 

Теперь создайте обработчик и зарегистрируйте его в узле обработчика событий. Когда узел запущен, он применяет его и любые другие указанные обработчики ко входящим сообщениям:

```go
handler := func(ctx context.Context, event *eventhubs.Event) error {
    fmt.Printf("received: %s\n", string(event.Data))
    return nil
}

// register the handler with the EPH
_, err := p.RegisterHandler(ctx, handler)
if err != nil {
    log.Fatalf("failed to register handler: %s\n", err)
}
```

## <a name="receive-messages"></a>Получение сообщений

Когда все будет настроено, можно запустить узел обработчика событий с `Start(context)`, чтобы он постоянно работал, или с `StartNonBlocking(context)` для работы только до тех пор, пока доступны сообщения.

В этом руководстве запуск и выполнение происходит следующим образом. (Ознакомьтесь с примером использования `StartNonBlocking` на GitHub).

```go
ctx := context.Background()
err = p.Start()
if err != nil {
    log.Fatalf("failed to start EPH: %s\n", err)
}
```

## <a name="notes"></a>Примечания

В этом руководстве используется один экземпляр **EventProcessorHost**. Чтобы увеличить пропускную способность и надежность, следует запустить несколько экземпляров **узла обработчика событий** в различных системах. Система выдачи аренд гарантирует, что только один получатель связан и получает сообщения из указанного раздела в указанное время.

## <a name="next-steps"></a>Дополнительная информация

Посетите эти страницы, чтобы узнать больше о концентраторах событий:

* [Отправка событий в концентраторы событий с помощью Go](event-hubs-go-get-started-send.md)
* 
  [Общие сведения о Центрах событий](event-hubs-about.md)
* [Создание концентратора событий](event-hubs-create.md)
* [Часто задаваемые вопросы о концентраторах событий](event-hubs-faq.md)

<!-- Links -->
[Event Hubs overview]: event-hubs-about.md
[создайте бесплатную учетную запись Azure]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
