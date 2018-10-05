---
title: Устранение неполадок и описание известных проблем в службе "Машинное обучение Azure"
description: Список известных проблем, их решение, а также шаги устранения неполадок.
services: machine-learning
author: j-martens
ms.author: jmartens
ms.reviewer: mldocs
ms.service: machine-learning
ms.component: core
ms.topic: article
ms.date: 09/24/2018
ms.openlocfilehash: d84040dc440c373ae9bae6dbac7a95109a387ba7
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2018
ms.locfileid: "47162752"
---
# <a name="known-issues-and-troubleshooting-azure-machine-learning-service"></a>Устранение неполадок и описание известных проблем в службе "Машинное обучение Azure"
 
С помощью сведений в этой статье можно найти и исправить ошибки или сбои, обнаруженные при использовании службы "Машинное обучение Azure". 


## <a name="databricks"></a>Databricks

Ниже описаны проблемы, которые могут возникать в службе "Машинное обучение Azure" и Databricks.

1. Рекомендации относительно кластера Databricks:
   
   Создайте кластер Azure Databricks v4.x с помощью Python 3. Мы рекомендуем использовать кластер с высокой степенью параллелизма.
 
1. Сбой установки пакета SDK для службы "Машинное обучение Azure" в Databricks, если установлены дополнительные пакеты.

   Некоторые пакеты, такие как `psutil upgrade libs`, могут приводить к конфликтам. Чтобы избежать ошибок установки, установите пакеты, заморозив версию lib. Эта проблема связана с Databricks, а не с пакетом SDK для службы "Машинное обучение Azure". Пример:
   ```python
   pstuil cryptography==1.5 pyopenssl==16.0.0 ipython=2.2.0
   ```

## <a name="gather-diagnostics-information"></a>Сбор данных диагностики
Иногда при обращении за помощью полезно предоставить диагностические сведения. Вот где расположены файлы журналов:

## <a name="resource-quotas"></a>Квоты ресурсов

Дополнительные сведения о квотах ресурсов в службе "Машинное обучение Azure" см. [здесь](how-to-manage-quotas.md).

## <a name="get-more-support"></a>Получение поддержки

Вы можете отправлять запросы на поддержку, чтобы получить помощь от команды технической поддержки, участников форумов и других пользователей. [Подробнее...](support-for-aml-services.md)
