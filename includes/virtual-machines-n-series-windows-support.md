---
title: включение файла
description: включение файла
services: virtual-machines-windows
author: dlepow
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 09/24/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 4da90cf636ab2010d7c369f4c13e45190dc6b2db
ms.sourcegitcommit: 7bc4a872c170e3416052c87287391bc7adbf84ff
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/02/2018
ms.locfileid: "48020795"
---
## <a name="supported-operating-systems-and-drivers"></a>Поддерживаемые операционные системы и драйверы

### <a name="nvidia-tesla-cuda-drivers"></a>Драйверы NVIDIA Tesla (CUDA)

Драйверы NVIDIA Tesla (CUDA) для виртуальных машин серий NC, NCv2, NCv3 и ND (необязательно для серии NV) поддерживаются только в операционных системах, перечисленных в следующей таблице. Ссылки для скачивания драйверов актуальны на момент публикации. Последние версии драйверов можно получить на веб-сайте [NVIDIA](http://www.nvidia.com/).

> [!TIP]
> Вместо ручной установки драйвера CUDA на виртуальной машине Windows Server можно развернуть образ [виртуальной машины для обработки и анализа данных](../articles/machine-learning/data-science-virtual-machine/overview.md) Azure. Выпуски DSVM Windows Server 2016 предварительно устанавливают драйверы NVIDIA CUDA, библиотеку глубокой нейронной сети CUDA и другие средства.


| ОС | Драйвер |
| -------- |------------- |
| Windows Server 2016 | [398.75](http://us.download.nvidia.com/Windows/Quadro_Certified/398.75/398.75-tesla-desktop-winserver2016-international.exe) (EXE-файл) |
| Windows Server 2012 R2 | [398.75](http://us.download.nvidia.com/Windows/Quadro_Certified/398.75/398.75-tesla-desktop-winserver2008-2012r2-64bit-international.exe) (EXE-файл) |


### <a name="nvidia-grid-drivers"></a>Драйверы NVIDIA GRID

Корпорация Майкрософт распространяет установщики драйверов NVIDIA GRID для виртуальных машин серии NV и NVv2, используемых в качестве виртуальных рабочих станций или виртуальных приложений. Эти драйверы GRID следует устанавливать только на виртуальные машины Azure серии NV под управлением операционных систем, перечисленных в следующей таблице. Эти драйверы содержат лицензии на ПО виртуального графического процессора GRID в Azure.

| ОС | Драйвер |
| -------- |------------- |
| Windows Server 2016<br/><br/>Windows 10 | [GRID 6.2 (391.81)](https://go.microsoft.com/fwlink/?linkid=874181) (EXE-файл) |
| Windows Server 2012 R2 | [GRID 6.2 (391.81)](https://go.microsoft.com/fwlink/?linkid=874184) (EXE-файл)  |