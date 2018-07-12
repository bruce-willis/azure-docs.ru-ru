---
title: включение файла
description: включение файла
services: functions
author: ggailey777
manager: cfowler
ms.service: functions
ms.topic: include
ms.date: 05/01/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 88c01e8e57d4a92478b8b1ca0689ff0f8e499b39
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38739633"
---
Если узел службы "Функции" запускается в локальной среде, журналы записываются по следующему пути:

```
<DefaultTempDirectory>\LogFiles\Application\Functions
```

В Windows `<DefaultTempDirectory>` — это первое значение, найденное для переменных среды TMP, TEMP, USERPROFILE или каталога Windows.
В MacOS или Linux `<DefaultTempDirectory>` — это переменная среды TMPDIR.

> [!NOTE]
> При запуске узла службы "Функции" существующая структура файлов в каталоге перезаписывается.