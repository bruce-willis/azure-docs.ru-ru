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
ms.openlocfilehash: d166a77a0636efea3b63660fde2187e3f2ec15c0
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37063754"
---
Если вы разрабатываете функции локально, можете установить необходимые расширения из терминала или командной строки с помощью основных инструментов службы "Функции Azure". 

После обновления файла *function.json* для включения всех привязок, необходимых для функции, выполните команду `func extensions install` в папке проекта. Эта команда считывает файл *function.json*, чтобы определить, какие пакеты требуются, а затем устанавливает их.

Если вы хотите установить определенную версию пакета или установить пакеты до изменения файла *function.json*, используйте команду `func extensions install`, указав имя пакета, как показано в следующем примере.

```
func extensions install --package Microsoft.Azure.WebJobs.ServiceBus --version <target_version>
```

Замените `<target_version>` определенной версией пакета, например `3.0.0-beta5`. Допустимые версии перечислены на страницах отдельных пакетов на сайте [NuGet.org](https://nuget.org).
