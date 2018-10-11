---
title: Подготовка данных с помощью пакета SDK для подготовки данных машинного обучения Azure (Python)°— Azure
description: Узнайте, как использовать пакет SDK для подготовки данных машинного обучения Azure для Python, чтобы загружать данные в различных форматах, преобразовывать их для удобства использования и записывать эти данные в расположения, где они будут доступны вашим моделям.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: cforbe
author: cforbe
manager: cgronlun
ms.reviewer: jmartens
ms.date: 09/24/2018
ms.openlocfilehash: 83373f5d6e4a2227bcafdbc4b25b686b0b8d651d
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/08/2018
ms.locfileid: "48867193"
---
# <a name="prepare-data-for-modeling-with-azure-machine-learning"></a>Подготовка данных для моделирования с помощью службы "Машинное обучение Azure"
 
Подготовка данных является важной частью рабочего процесса машинного обучения. Ваши модели будут более точными и эффективными, если им будут предоставлены достоверные данные в удобном для использования формате. 

Вы можете подготовить данные в Python, используя [пакет SDK для подготовки данных службы "Машинное обучение Azure"](https://docs.microsoft.com/python/api/overview/azure/dataprep?view=azure-dataprep-py). 

## <a name="data-preparation-pipeline"></a>Конвейер подготовки данных

Ниже приведены основные этапы подготовки данных.

1. [Загрузка данных](how-to-load-data.md), которые могут иметь различные форматы.
2. [Преобразование](how-to-transform-data.md) данных в более удобную структуру.
3. [Запись](how-to-write-data.md) данных в доступное для моделей расположение.

![Процесс подготовки данных](./media/concept-data-preparation/data-prep-process.png)

## <a name="next-steps"></a>Дополнительная информация
Просмотрите [примерный журнал](https://github.com/Microsoft/MSDataPrepDocs/blob/master/Scenarios/GettingStarted/getting-started.ipynb) подготовки данных с помощью пакета SDK для подготовки данных машинного обучения Azure.
