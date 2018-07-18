---
title: включение файла
description: включение файла
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/08/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: ac2cf4d688b1bdc54ed2d7341f0e195d3b2fe42d
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/08/2018
ms.locfileid: "35236475"
---
## <a name="what-happens-to-my-app-during-deployment"></a>Что происходит с моим приложением во время развертывания?

Все официально поддерживаемые методы развертывания объединяет одно: они вносят изменения в файлы в папке `/site/home/wwwroot` приложения. Именно эти файлы выполняются в рабочей среде. Поэтому развертывание может завершиться сбоем из-за заблокированных файлов или приложение в рабочей среде может вести себя непредсказуемо во время развертывания, так как не все файлы обновляются одновременно. Избежать этих проблем можно несколькими способами.

- Остановите приложение или включите для него автономный режим на время развертывания. Дополнительные сведения см. в разделе [Dealing with locked files during deployment](https://github.com/projectkudu/kudu/wiki/Dealing-with-locked-files-during-deployment) (Устранение проблем из-за заблокированных файлов при развертывании).
- Выполняйте развертывание в [промежуточный слот](../articles/app-service/web-sites-staged-publishing.md) с включенным [автоматическим переключением](../articles/app-service/web-sites-staged-publishing.md#configure-auto-swap). 
- Используйте [Run-from-Zip](https://github.com/Azure/app-service-announcements/issues/84).
