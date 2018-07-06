---
title: Включение безопасного TLS для клиента службы хранилища Azure | Документация Майкрософт
description: Узнайте, как включить TLS 1.2 в клиенте службы хранилища Azure.
services: storage
documentationcenter: na
author: fhryo-msft
manager: cbrooks
editor: fhryo-msft
ms.assetid: ''
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 06/25/2018
ms.author: fryu
ms.openlocfilehash: 5c21df2b3bdeee6ac7c3956fe1cafa4f947dd6dd
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37035362"
---
# <a name="enable-secure-tls-for-azure-storage-client"></a>Включение безопасного TLS для клиента службы хранилища Azure

Если вам нужно выполнять в службе хранилища Azure аудит соблюдения новых требований к соответствию и безопасности, учитывайте, что протоколы SSL 1.0, 2.0, 3.0 и TLS 1.0, распознаются как несовместимые протоколы взаимодействия.

В протоколах SSL 1.0, 2.0 и 3.0 обнаружены неустранимые уязвимости. Их применение запрещено рекомендациями RFC. Протокол TLS 1.0 становится небезопасным из-за использования небезопасного блочного шифра (DES CBC и RC2 CBC) и поточного шифра (RC4). Также совет PCI предлагает переходить на более поздние версии TLS. Дополнительные сведения можно найти в статье [о протоколе TLS](https://en.wikipedia.org/wiki/Transport_Layer_Security#SSL_1.0.2C_2.0_and_3.0).

В службе хранилища Azure протокол SSL 3.0 не используется с 2015 г. Сейчас в службе используется TLS 1.2 на общедоступных конечных точках HTTPS, но TLS 1.0 и TLS 1.1 пока поддерживаются для обеспечения обратной совместимости.

Чтобы обеспечить безопасность и соответствие требованиям для подключения к службе хранилища Azure, необходимо включить TLS 1.2 на стороне клиента перед отправкой запросов на управление службой хранилища Azure.

## <a name="enable-tls-12-in-net-client"></a>Включение TLS 1.2 в клиенте .NET

Чтобы клиент согласовывал TLS 1.2, операционная система и версия платформы .NET Framework должны поддерживать TLS 1.2. Дополнительные сведения см. в статье [о поддержке TLS 1.2](https://docs.microsoft.com/en-us/dotnet/framework/network-programming/tls#support-for-tls-12).

В следующем примере показано, как включить TLS 1.2 в клиенте .NET.

```csharp

    static void EnableTls12()
    {
        // Enable TLS 1.2 before connecting to Azure Storage
        System.Net.ServicePointManager.SecurityProtocol = System.Net.SecurityProtocolType.Tls12;

        // Connect to Azure Storage
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse("DefaultEndpointsProtocol=https;AccountName={yourstorageaccount};AccountKey={yourstorageaccountkey};EndpointSuffix=core.windows.net");
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

        CloudBlobContainer container = blobClient.GetContainerReference("foo");
        container.CreateIfNotExists();
    }

```

## <a name="enable-tls-12-in-powershell-client"></a>Включение TLS 1.2 в клиенте PowerShell

В следующем примере показано, как включить TLS 1.2 в клиенте PowerShell.

```powershell

# Enable TLS 1.2 before connecting to Azure Storage
[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12;

$resourceGroup = "{YourResourceGropuName}"
$storageAccountName = "{YourStorageAccountNme}"
$prefix = "foo"

# Connect to Azure Storage
$storageAccount = Get-AzureRmStorageAccount -ResourceGroupName $resourceGroup -Name $storageAccountName
$ctx = $storageAccount.Context
$listOfContainers = Get-AzureStorageContainer -Context $ctx -Prefix $prefix
$listOfContainers

```

## <a name="verify-tls-12-connection"></a>Проверка подключения по протоколу TLS 1.2

Средство Fiddler позволяет проверить, используется ли TLS 1.2. Откройте Fiddler, чтобы начать захват сетевого трафика между клиентами, затем выполните приведенный выше пример. Так вы сможете узнать, какую версию TLS использует подключение, созданное в примере.

На следующем снимке экрана представлен пример такой проверки.

![Снимок экрана Fiddler с проверкой версии протокола TLS](./media/storage-security-tls/storage-security-tls-verify-in-fiddler.png)

## <a name="see-also"></a>См. также

* [Протокол TLS](https://en.wikipedia.org/wiki/Transport_Layer_Security#SSL_1.0.2C_2.0_and_3.0)
