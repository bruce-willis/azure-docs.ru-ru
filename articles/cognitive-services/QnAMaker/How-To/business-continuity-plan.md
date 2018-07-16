---
title: Создание плана по обеспечению непрерывности бизнеса для службы QnA Maker. Microsoft Cognitive Services | Документация Майкрософт
titleSuffix: Azure
description: Как создать план по обеспечению непрерывности бизнес-процессов для службы QnA Maker
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/07/2018
ms.author: saneppal
ms.openlocfilehash: ca6e54b8a8ca8b38e8ef6b1a148f8b2c54bd43da
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35381748"
---
# <a name="create-a-business-continuity-plan-for-your-qna-maker-service"></a>Создание плана по обеспечению непрерывности бизнес-процессов для службы QnA Maker

Основная цель плана обеспечения непрерывности бизнес-процессов — создание отказоустойчивой конечной точки базы знаний, которая обеспечит отсутствие простоев бота или приложения, которые его используют.

![План по обеспечению непрерывности бизнес-процессов для QnA Maker](../media/qnamaker-how-to-bcp-plan/qnamaker-bcp-plan.png)

В общих чертах для воплощения представленной выше концепции нужно сделать следующее:

1. Настройте две параллельные [службы QnA Maker](../How-To/set-up-qnamaker-service-azure.md) в [сопряженных регионах Azure](https://docs.microsoft.com/en-us/azure/best-practices-availability-paired-regions).

2. Поддерживайте синхронизацию основного и дополнительного индексов поиска Azure. Просмотрите приведенный [здесь](https://github.com/pchoudhari/QnAMakerBackupRestore) пример на GitHub, чтобы узнать, как создавать резервные копии индексов Azure и восстанавливать их.

3. Создайте резервную копию Application Insights с помощью [непрерывного экспорта](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-export-telemetry).

4. После настройки первичного и вторичного стеков воспользуйтесь [диспетчером трафика](https://docs.microsoft.com/en-us/azure/traffic-manager/) для настройки двух конечных точек и метода маршрутизации.

5. Для конечной точки диспетчера трафика потребуется создать SSL-сертификат. [Привяжите SSL-сертификат](https://docs.microsoft.com/en-us/azure/app-service/app-service-web-tutorial-custom-ssl) к службам приложений.

6. Наконец, используйте конечную точку диспетчера трафика в боте или приложении.

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Выбор ресурсов для развертывания QnA Maker](../Tutorials/choosing-capacity-qnamaker-deployment.md)
