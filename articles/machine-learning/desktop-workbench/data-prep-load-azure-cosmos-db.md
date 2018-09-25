---
title: Подключение Azure Cosmos DB в качестве источника данных в Azure Machine Learning Workbench | Документация Майкрософт
description: В этом документе содержатся примеры подключения к Azure Cosmos DB через Azure Machine Learning Workbench.
services: machine-learning
author: cforbe
ms.author: cforbe
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: ''
ms.devlang: ''
ms.topic: article
ms.date: 09/11/2017
ROBOTS: NOINDEX
ms.openlocfilehash: 9c4ea529e8ca6dbb9b7321dc24468fad93435705
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46948202"
---
# <a name="connecting-to-azure-cosmos-db-as-a-data-source"></a>Подключение к Azure Cosmos DB в качестве источника данных

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 


В этой статье содержатся примеры Python, позволяющие подключаться к Cosmos DB в Azure Machine Learning Workbench.

## <a name="load-azure-cosmos-db-data-into-data-preparation"></a>Загрузка данных Azure Cosmos DB в средство подготовки данных

Создайте поток данных на основе скрипта и загрузите данные из Azure Cosmos DB с помощью следующего скрипта: 

```python
import pydocumentdb
import pydocumentdb.document_client as document_client

import pandas as pd

config = { 
    'ENDPOINT': '<Endpoint>',
    'MASTERKEY': '<Key>',
    'DOCUMENTDB_DATABASE': '<DBName>',
    'DOCUMENTDB_COLLECTION': '<collectionname>'
};

# Initialize the Python DocumentDB client.
client = document_client.DocumentClient(config['ENDPOINT'], {'masterKey': config['MASTERKEY']})

# Read databases and take first since id should not be duplicated.
db = next((data for data in client.ReadDatabases() if data['id'] == config['DOCUMENTDB_DATABASE']))

# Read collections and take first since id should not be duplicated.
coll = next((coll for coll in client.ReadCollections(db['_self']) if coll['id'] == config['DOCUMENTDB_COLLECTION']))

docs = client.ReadDocuments(coll['_self'])

df = pd.DataFrame(list(docs))
```

## <a name="other-data-source-connections"></a>Другие соединения с источниками данных
Другие примеры дополнительных подключений к исходным данным см. [здесь](data-prep-appendix8-sample-source-connections-python.md).
