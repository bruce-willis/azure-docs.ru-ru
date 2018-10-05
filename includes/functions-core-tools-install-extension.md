---
title: включение файла
description: включение файла
services: functions
author: ggailey777
ms.service: functions
ms.topic: include
ms.date: 09/21/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: f1b53c53b1e5fb089eb9b8a9b816b11a1eea126d
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47044515"
---
Если вы разрабатываете функции локально, можете установить необходимые расширения из терминала или командной строки с помощью основных инструментов службы "Функции Azure".

После обновления файла *function.json* для включения всех привязок, необходимых для функции, выполните следующую команду в папке проекта.

```bash
func extensions install
```

Эта команда считывает файл *function.json*, чтобы определить, какие пакеты требуются, а затем устанавливает их и перестраивает проект расширений. Затем добавляются все новые привязки в текущей версии, но существующие привязки не обновляются. Используйте параметр `--force`, чтобы обновить существующие привязки до последней версии при установке новых пакетов.

Если вы хотите установить определенную версию пакета или установить пакеты до изменения файла *function.json*, используйте команду `func extensions install`, указав имя пакета, как показано в следующем примере.

```bash
func extensions install --package Microsoft.Azure.WebJobs.ServiceBus --version <target_version>
```

Замените `<target_version>` определенной версией пакета, например `3.0.0-beta5`. Допустимые версии перечислены на страницах отдельных пакетов на сайте [NuGet.org](https://nuget.org).
