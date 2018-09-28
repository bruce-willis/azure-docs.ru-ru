---
title: Примеры кэша Redis для Azure CLI | Документы Майкрософт
description: Примеры Azure CLI для кэша Redis для Azure.
services: redis-cache
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: 8d2de145-50c0-4f76-bf8f-fdf679f03698
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: azurecli
ms.topic: article
ms.date: 04/14/2017
ms.author: wesmc
ms.openlocfilehash: 01773e1ec24e6ab7d1899df6774230b7ce5b5676
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46957903"
---
# <a name="azure-cli-samples-for-azure-redis-cache"></a>Примеры Azure CLI для кэша Redis для Azure

В следующей таблице содержатся ссылки на скрипты Bash, созданные с помощью Azure CLI.

| | |
|---|---|
|**Создание кэша**||
| [Создание кэша](./scripts/create-cache.md) | Создает группу ресурсов и базовый уровень кэша Redis для Azure. |
| [Создание кэша уровня "Премиум" с кластеризацией](./scripts/create-premium-cache-cluster.md) | Создает группу ресурсов и кэш уровня "Премиум" с включенной кластеризацией.|
| [Получение сведений о кэше](./scripts/show-cache.md) | Возвращает сведения об экземпляре кэша Redis для Azure, включая состояние подготовки. |
| [Получение имени узла, портов и ключей](./scripts/cache-keys-ports.md) | Возвращает имя узла, порты и ключи для экземпляра кэша Redis для Azure. |
|**Веб-приложение и кэш**||
| [Подключение веб-приложения к кэшу Redis](./../app-service/scripts/app-service-cli-app-service-redis.md) | Создает веб-приложение Azure и кэш Redis, а затем добавляет сведения о подключении кэша Redis в параметры приложения. |
|**Удаление кэша**||
| [Удаление кэша](./scripts/delete-cache.md) | Удаляет экземпляр кэша Redis для Azure.  |
| | |

Дополнительные сведения об Azure CLI см. в статьях [Установка интерфейса командной строки Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) и [Приступая к работе с Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli).
