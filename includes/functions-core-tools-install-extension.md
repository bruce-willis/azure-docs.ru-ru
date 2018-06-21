---
title: включение файла
description: включение файла
services: functions
author: ggailey777
ms.service: functions
ms.topic: include
ms.date: 04/06/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 6fb497a5b6da00dece43c7f41ea3c411f385a2ba
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34726893"
---
Если вы разрабатываете функции локально, можете установить необходимые расширения из терминала или командной строки с помощью основных инструментов службы "Функции Azure". 

После обновления файла *function.json* для включения всех привязок, необходимых для функции, выполните команду `func extensions install` в папке проекта. Эта команда считывает файл *function.json*, чтобы определить, какие пакеты требуются, а затем устанавливает их.

Если вы хотите установить определенную версию пакета или установить пакеты до изменения файла *function.json*, используйте команду `func extensions install`, указав имя пакета, как показано в следующем примере.

```
func extensions install --package Microsoft.Azure.WebJobs.Extensions.CosmosDB --version <target_version>
```

Замените `<target_version>` определенной версией пакета. Допустимые версии перечислены на страницах отдельных пакетов на сайте [NuGet.org](https://nuget.org).
