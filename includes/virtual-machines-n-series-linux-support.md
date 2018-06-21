---
title: включение файла
description: включение файла
services: virtual-machines-linux
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 05/29/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 0264f92fa10bd503a2811ce40ee0b8d4edd5f3b1
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34669838"
---
## <a name="supported-distributions-and-drivers"></a>Поддерживаемые дистрибутивы и драйверы

### <a name="nvidia-cuda-drivers"></a>Драйверы NVIDIA CUDA

Драйверы NVIDIA CUDA для виртуальных машин серий NC, NCv2, NCv3 и ND (необязательно для серии NV) поддерживаются только в дистрибутивах Linux, перечисленных в следующей таблице. Приведенные сведения о драйверах CUDA актуальны на время публикации. Последние версии драйверов CUDA можно получить на веб-сайте [NVIDIA](https://developer.nvidia.com/cuda-zone). Убедитесь, что вы установили последнюю версию драйверов CUDA для своего дистрибутива или выполнили обновление до этой версии. 

> [!TIP]
> Вместо ручной установки драйвера CUDA на виртуальной машине Linux можно развернуть образ [виртуальной машины для обработки и анализа данных](../articles/machine-learning/data-science-virtual-machine/overview.md) Azure. Выпуски DSVM Ubuntu 16.04 LTS или CentOS 7.4 предварительно устанавливают драйверы NVIDIA CUDA, библиотеку глубокой нейронной сети CUDA и другие средства.

| Дистрибутив | Драйвер |
| --- | -- | 
| Ubuntu 16.04 LTS<br/><br/> Red Hat Enterprise Linux 7.3 или 7.4<br/><br/> Версия 7.3 или 7.4 на платформе CentOS, HPC на платформе CentOS 7.4 | NVIDIA CUDA 9.1, драйвер серии R390 |

### <a name="nvidia-grid-drivers"></a>Драйверы NVIDIA GRID

Корпорация Майкрософт распространяет установщики драйверов NVIDIA GRID для виртуальных машин серии NV, используемых в качестве виртуальных рабочих станциях или виртуальных приложений. Эти драйверы GRID следует устанавливать только на виртуальные машины Azure серии NV под управлением дистрибутивов, перечисленных в следующей таблице. Эти драйверы содержат лицензии на ПО виртуального графического процессора GRID в Azure.

| Дистрибутив | Драйвер |
| --- | -- |
| Ubuntu 16.04 LTS<br/><br/>Red Hat Enterprise Linux 7.3 или 7.4<br/><br/>Версия 7.3 или 7.4 на платформе CentOS | NVIDIA GRID 6.0, ветвь драйверов R390|



> [!WARNING] 
> Установка стороннего программного обеспечения на продуктах Red Hat может нарушать условия технической поддержки Red Hat. Ознакомьтесь со [статьей из базы знаний Red Hat](https://access.redhat.com/articles/1067).
>
