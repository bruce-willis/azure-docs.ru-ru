---
title: Как удалить кластер HDInsight в Azure
description: Сведения о различных способах удаления кластера HDInsight.
services: hdinsight
author: jasonwhowell
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/22/2018
ms.author: jasonh
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: 39404ff74552b11e982cf5968c0eb131ea642e27
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46979460"
---
# <a name="delete-an-hdinsight-cluster-using-your-browser-powershell-or-the-azure-classic-cli"></a>Удаление кластера HDInsight с помощью браузера, PowerShell или классического интерфейса Azure CLI

Начисление оплаты начинается после создания кластера HDInsight и прекращается только после его удаления. Кластеры оплачиваются поминутно, поэтому всегда следует удалять кластер, когда он больше не нужен. В этом документе вы узнаете, как удалить кластер с помощью портала Azure, Azure PowerShell и классического интерфейса Azure CLI.

> [!IMPORTANT]
> При удалении кластера HDInsight не происходит удаление связанных с ним учетных записей хранения Azure или Data Lake Store. Данные, хранящиеся в этих службах, можно повторно использовать в будущем.

## <a name="azure-portal"></a>Портал Azure

1. Войдите на [портал Azure](https://portal.azure.com) и выберите свой кластер HDInsight. Если кластер HDInsight не закреплен на панели мониторинга, его можно найти по имени с помощью поля поиска.
   
    ![поиск по порталу](./media/hdinsight-delete-cluster/navbar.png)

2. В параметрах кластера выберите значок **Удалить**. При появлении запроса выберите **Да**, чтобы удалить кластер.
   
    ![значок удаления](./media/hdinsight-delete-cluster/deletecluster.png)

## <a name="azure-powershell"></a>Azure PowerShell

Введите следующую команду в командной строке PowerShell, чтобы удалить кластер.

    Remove-AzureRmHDInsightCluster -ClusterName CLUSTERNAME

Замените **CLUSTERNAME** именем кластера HDInsight.

## <a name="azure-classic-cli"></a>Классический Azure CLI

[!INCLUDE [classic-cli-warning](../../includes/requires-classic-cli.md)]

Введите следующую команду в командной строке, чтобы удалить кластер.

    azure hdinsight cluster delete CLUSTERNAME

Замените **CLUSTERNAME** именем кластера HDInsight.
