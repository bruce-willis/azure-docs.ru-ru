---
title: Перенаправление событий хранилища BLOB-объектов Azure в пользовательскую конечную веб-точку с помощью PowerShell | Документация Майкрософт
description: Используйте службу "Сетка событий Azure" для подписки на события хранилища BLOB-объектов.
services: storage,event-grid
keywords: ''
author: david-stanford
ms.author: dastanfo
ms.date: 05/24/2018
ms.topic: article
ms.service: storage
ms.openlocfilehash: b6764ffa0e7cfbc888f11c22af855d48d8160372
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34650508"
---
# <a name="route-blob-storage-events-to-a-custom-web-endpoint-with-powershell"></a>Перенаправление событий хранилища BLOB-объектов в пользовательскую конечную веб-точку с помощью PowerShell

"Сетка событий Azure" — это служба обработки событий для облака. В этой статье с помощью Azure PowerShell вы создадите подписку на события хранилища BLOB-объектов и активируете событие, чтобы увидеть результат. 

Как правило, события отправляются на конечную точку, которая обрабатывает данные событий и выполняет соответствующие действия. Но в этой статье для простоты события отправляются в веб-приложение, которое собирает и отображает сообщения.

По завершении можно увидеть, что данные событий отправлены в веб-приложение.

![Просмотр результатов](./media/storage-blob-event-quickstart-powershell/view-results.png)

## <a name="setup"></a>Настройка

В этой статье требуется последняя версия Azure PowerShell. Если вам необходимо выполнить установку или обновление, см. статью [об установке модуля Azure PowerShell](/powershell/azure/install-azurerm-ps).

## <a name="log-in-to-azure"></a>Вход в Azure

Чтобы выполнить проверку подлинности, войдите в подписку Azure с помощью команды `Connect-AzureRmAccount` и следуйте инструкциям на экране.

```powershell
Connect-AzureRmAccount
```

> [!NOTE]
> Доступность событий хранилища привязывается к [доступности](../../event-grid/overview.md) службы "Сетка событий". Эта функция скоро станет доступной в других регионах вместе со службой "Сетка событий".

В этом примере используется регион **westus2**, который сохраняется в переменной для повсеместного использования.

```powershell
$location = "westus2"
```

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Темами событий сетки являются ресурсы Azure, которые необходимо поместить в группу ресурсов Azure. Группа ресурсов Azure — это логическая коллекция, в которой выполняется развертывание и администрирование ресурсов Azure.

Создайте группу ресурсов с помощью команды [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup).

В следующем примере создается группа ресурсов с именем **gridResourceGroup** в расположении **westus2**.  

```powershell
$resourceGroup = "gridResourceGroup"
New-AzureRmResourceGroup -Name $resourceGroup -Location $location
```

## <a name="create-a-storage-account"></a>Создание учетной записи хранения

Для использования событий хранилища BLOB-объектов требуется либо [учетная запись хранения BLOB-объектов](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-storage-accounts), либо [учетная запись хранения общего назначения версии 2](../common/storage-account-options.md#general-purpose-v2). Учетные записи **общего назначения версии 2 (GPv2)** являются учетными записями хранения, которые поддерживают все функции для всех служб хранилища, включая большие двоичные объекты, файлы, очереди и таблицы. **Учетные записи хранения BLOB-объектов** — это специализированные учетные записи хранения таких неструктурированных данных, как большие двоичные объекты, в службе хранилища Azure. Учетные записи хранилища BLOB-объектов похожи на учетные записи хранения общего назначения и обладают такими же функциями обеспечения устойчивости, надежности, масштабируемости и производительности, которые вы уже используете, а также отличаются полной согласованностью API в плане блочных BLOB-объектов и добавления больших двоичных объектов. Для приложений, требующих только блокировки или добавления больших двоичных объектов, рекомендуется использовать учетные записи хранения больших двоичных объектов.  

Создайте учетную запись хранения BLOB-объектов с репликацией LRS, используя командлет [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/New-AzureRmStorageAccount), а затем извлеките контекст учетной записи хранения, который определяет используемую учетную запись хранения. Действуя в учетной записи хранения, ссылайтесь на контекст, вместо того чтобы многократно предоставлять учетные данные. В этом примере создается учетная запись хранения **gridstorage** с локально избыточным хранилищем (LRS). 

> [!NOTE]
> Имена учетных записей хранения находятся в глобальном пространстве имен, поэтому в имя, указанное в этом сценарии, необходимо добавить несколько произвольных знаков.

```powershell
$storageName = "gridstorage"
$storageAccount = New-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageName `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind BlobStorage `
  -AccessTier Hot

$ctx = $storageAccount.Context
```

## <a name="create-a-message-endpoint"></a>Создание конечной точки сообщения

Перед подпиской на раздел необходимо создать конечную точку для сообщения о событии. Обычно конечная точка выполняет действия на основе данных событий. Чтобы упростить работу с этим руководством, разверните [готовое веб-приложение](https://github.com/dbarkol/azure-event-grid-viewer), которое отображает сообщения о событиях. Развернутое решение содержит план службы приложений, веб-приложение службы приложений и исходный код из GitHub.

Замените `<your-site-name>` уникальным именем для вашего веб-приложения. Имя веб-приложения должно быть уникальным, так как оно включается в запись DNS.

```powershell
$sitename="<your-site-name>"

New-AzureRmResourceGroupDeployment `
  -ResourceGroupName $resourceGroup `
  -TemplateUri "https://raw.githubusercontent.com/dbarkol/azure-event-grid-viewer/master/azuredeploy.json" `
  -siteName $sitename `
  -hostingPlanName viewerhost
```

Завершение развертывания может занять несколько минут. Когда развертывание успешно завершится, откройте веб-приложение и убедитесь, что оно работает. Откройте браузер и перейдите по адресу `https://<your-site-name>.azurewebsites.net`.

Вы увидите сайт, на котором сейчас не отображаются никакие сообщения.

## <a name="subscribe-to-your-storage-account"></a>Подписка на учетную запись хранения

Подпишитесь на тему, чтобы определить в сетке событий Azure, какие события вам необходимо отслеживать. В следующем примере создается подписка на созданную учетную запись хранения и передается URL-адрес из веб-приложения в качестве конечной точки для уведомления о событии. Конечная точка веб-приложения должна содержать суффикс `/api/updates/`.

```powershell
$storageId = (Get-AzureRmStorageAccount -ResourceGroupName $resourceGroup -AccountName $storageName).Id
$endpoint="https://$sitename.azurewebsites.net/api/updates"

New-AzureRmEventGridSubscription `
  -EventSubscriptionName gridBlobQuickStart `
  -Endpoint $endpoint `
  -ResourceId $storageId
```

Теперь снова откройте веб-приложение и убедитесь, что оно успешно получило отправленное событие подтверждения подписки. Щелкните значок с изображением глаза, чтобы развернуть данные события. Сетка событий отправляет событие подтверждения, чтобы конечная точка могла подтвердить, что она готова получать данные события. Веб-приложение содержит код для проверки подписки.

![Просмотр события подписки](./media/storage-blob-event-quickstart-powershell/view-subscription-event.png)

## <a name="trigger-an-event-from-blob-storage"></a>Активация события из хранилища BLOB-объектов

Теперь необходимо активировать событие, чтобы увидеть, как Сетка событий Azure распределяет сообщение к вашей конечной точке. Сначала давайте создадим контейнер и объект. Затем давайте передадим этот объект в контейнер.

```powershell
$containerName = "gridcontainer"
New-AzureStorageContainer -Name $containerName -Context $ctx

echo $null >> gridTestFile.txt

Set-AzureStorageBlobContent -File gridTestFile.txt -Container $containerName -Context $ctx -Blob gridTestFile.txt
```

Кроме того, вы активировали событие, а служба "Сетка событий" отправила сообщение в конечную точку, настроенную вами при подписке. Откройте веб-приложение и просмотрите в нем отправленные события.

```json
[{
  "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myrg/providers/Microsoft.Storage/storageAccounts/myblobstorageaccount",
  "subject": "/blobServices/default/containers/gridcontainer/blobs/gridTestFile.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-08-16T20:33:51.0595757Z",
  "id": "4d96b1d4-0001-00b3-58ce-16568c064fab",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "Azure-Storage-PowerShell-d65ca2e2-a168-4155-b7a4-2c925c18902f",
    "requestId": "4d96b1d4-0001-00b3-58ce-16568c000000",
    "eTag": "0x8D4E4E61AE038AD",
    "contentType": "application/octet-stream",
    "contentLength": 0,
    "blobType": "BlockBlob",
    "url": "https://myblobstorageaccount.blob.core.windows.net/gridcontainer/gridTestFile.txt",
    "sequencer": "00000000000000EB0000000000046199",
    "storageDiagnostics": {
      "batchId": "dffea416-b46e-4613-ac19-0371c0c5e352"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]

```

## <a name="clean-up-resources"></a>Очистка ресурсов
Если вы планируете продолжать работу с этой учетной записью хранения и подпиской на события, не очищайте ресурсы, созданные при работе с этой статьей. Если вы не планируете продолжать работу, удалите все созданные ресурсы в этой статье.

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Дополнительная информация

Теперь, когда вы знаете, как создавать разделы и подписки на события, ознакомьтесь с дополнительными сведениями о событиях хранилища BLOB-объектов и возможностях службы "Сетка событий":

- [Reacting to Blob storage events (preview)](storage-blob-event-overview.md) (Реагирование на события хранилища BLOB-объектов)
- [An introduction to Azure Event Grid](../../event-grid/overview.md) (Общие сведения о службе "Сетка событий Azure")
