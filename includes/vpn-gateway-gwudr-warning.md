---
title: включение файла
description: включение файла
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 06/04/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 244d6be318662be794cac58aaa8350b433b6cb37
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/06/2018
ms.locfileid: "34825465"
---
Не связывайте таблицу маршрутов, которая включает маршрут с пунктом назначения 0.0.0.0/0 в подсеть шлюза. Это приводит к неправильной работе шлюза.