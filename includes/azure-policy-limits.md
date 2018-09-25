---
title: включение файла
description: включение файла
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/18/2018
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: c3365450c90c4fda37884e8998fad70f5d164244
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47006527"
---
Ниже приведено максимальное количество для каждого типа объекта для службы "Политика Azure". Запись _Область_ означает либо подписку, либо [группу управления](../articles/governance/management-groups/overview.md).

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
