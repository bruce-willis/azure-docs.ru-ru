---
title: включение файла
description: включение файла
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 08/16/2018
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: 0e5d1214a8e1af8299cb40d1a3b31ff6eafc8c5c
ms.sourcegitcommit: 1aedb52f221fb2a6e7ad0b0930b4c74db354a569
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/17/2018
ms.locfileid: "40185088"
---
Ниже приведено максимальное количество для каждого типа объекта для службы "Политика Azure". Запись _Область_ означает либо подписку, либо [группу управления](../articles/azure-resource-manager/management-groups-overview.md).

| Where | Что | Максимальное количество |
|---|---|---|
| Область | Определения политик | 250 |
| Область | Определения инициатив | 100 |
| Клиент | Определения инициатив | 1000 |
| Область | Назначения инициатив или политик | 100 |
| Определение политики | Параметры | 20 |
| Определение инициативы | Политики | 100 |
| Определение инициативы | Параметры | 100 |
| Назначения инициатив или политик | Исключения (не области) | 100 |
| Правило политики | Вложенные условные выражения | 512 |
