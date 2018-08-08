---
title: Мониторинг и диагностика приложений Сетки Service Fabric в Azure | Документы Майкрософт
description: Сведения мониторинге и диагностике приложений в Сетке Service Fabric в Azure.
services: service-fabric-mesh
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/22/2018
ms.author: srrengar
ms.custom: mvc, devcenter
ms.openlocfilehash: 9f857c23b5500bc7790a0ff7fcf81eaec51f37c9
ms.sourcegitcommit: 99a6a439886568c7ff65b9f73245d96a80a26d68
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/31/2018
ms.locfileid: "39358329"
---
# <a name="monitoring-and-diagnostics"></a>Мониторинг и диагностика.
Сетка Azure Service Fabric — это полностью управляемая служба, которая позволяет разработчикам развертывать приложения для микрослужб без управления виртуальными машинами, хранилищем или сетями. В мониторинге и диагностике для Сетки Service Fabric выделяют три основных типа диагностических данных.

- Журналы приложений определяются как журналы из контейнерных приложений в зависимости от способа инструментирования приложения (например, журналы Docker).
- События платформы — это события из платформы Сетки, относящиеся к операции контейнера, такой как активация, деактивация и завершение работы контейнера.
- Метрики контейнера — это метрики производительности и использования ресурсов для контейнеров (статистика Docker).

В этой статье рассматриваются варианты мониторинга и диагностики, доступные для последней предварительной версии.

## <a name="application-logs"></a>Журналы приложений

Журналы Docker можно просматривать из развернутых контейнеров или из каждого отдельного контейнера. В модели приложения Сетки Service Fabric каждый контейнер представляет пакет кода в приложении. Чтобы просмотреть связанные журналы с пакетом кода, выполните следующую команду:

```cli
az mesh code-package-log get --resource-group <nameOfRG> --app-name <nameOfApp> --service-name <nameOfService> --replica-name <nameOfReplica> --code-package-name <nameOfCodePackage>
```

> [!NOTE]
> Для получения имени реплики можно использовать команду az mesh service-replica. Имена реплик — это возрастающие числа, начиная с 0.*

Далее приводится пример просмотра журналов из контейнера VotingWeb.Code из приложения для голосования.

```cli
az mesh code-package-log get --resource-group <nameOfRG> --application-name SbzVoting --service-name VotingWeb --replica-name 0 --code-package-name VotingWeb.Code
```

## <a name="next-steps"></a>Дополнительная информация
Чтобы узнать больше о службе "Сетка Service Fabric", прочитайте этот обзор:
- [Обзор службы "Сетка Service Fabric"](service-fabric-mesh-overview.md)
