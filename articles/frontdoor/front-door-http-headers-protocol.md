---
title: Служба Azure Front Door — поддержка протокола HTTP-заголовки | Документы Майкрософт
description: Эта статья поможет вам понять, какие протоколы HTTP-заголовков поддерживает Front Door.
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: 0dcb769627714be9da55faf2a8e82c8750789498
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47038856"
---
# <a name="azure-front-door-service---http-headers-protocol-support"></a>Служба Azure Front Door — поддержка протокола HTTP-заголовки
В этом документе описывается протокол, поддерживаемый службой Azure Front Door и различные фрагменты пути к вызовам, как показано на следующей схеме. В следующем разделе приводятся более подробные сведения об HTTP-заголовках, которые поддерживает Front Door.

![Служба Azure Front Door — протокол HTTP-заголовков][1]

>[!WARNING]
>Служба Azure Front Door не дает гарантий в отношении HTTP-заголовков, которые здесь не рассматриваются.

## <a name="1-client-to-front-door"></a>1. Клиент — Front Door
Front Door принимает большинство заголовков из входящего запроса (не изменяя их), однако существуют некоторые зарезервированные заголовки, которые будут удалены из входящего запроса. К ним относятся заголовки со следующими префиксами:
 - X-FD*
 - X-MS*

## <a name="2-front-door-to-backend"></a>2. Front Door — серверная система

Front Door будет включать заголовки из входящего запроса, если только они не были удалены из-за ограничений, упомянутых выше. Front Door также добавит приведенные ниже заголовки.

| Заголовок  | Пример и описание |
| ------------- | ------------- |
| X-MS-Ref |  *X-MS-Ref: 0WrHgWgAAAACFupORp/8MS6vxhG/WUvawV1NURURHRTAzMjEARWRnZQ==* </br> Это уникальная эталонная строка, которая идентифицирует запрос, обслуженный Front Door. Она крайне важна для устранения неполадок, так как используется для поиска в журналах доступа.|
| X-MS-RequestChain |  *X-MS-RequestChain: hops=1* </br> Это заголовок, который Front Door использует, чтобы обнаруживать циклы запросов, и пользователи не должны использовать его в качестве зависимостей. |
| X-MS-Via |  *X-MS-Via: Azure* </br> Front Door добавляет этот заголовок, чтобы указать, что Azure или Front Door является промежуточным получателем запроса между клиентом и внутренним сервером. |

## <a name="3-front-door-to-client"></a>3. Front Door — клиент

Ниже перечислены заголовки, которые отправляются из Front Door клиентам. Все заголовки, отправляемые в службу Front Door из серверной части, также передаются клиенту.

| Заголовок  | Пример |
| ------------- | ------------- |
| X-MS-Ref |  *X-MS-Ref: 0WrHgWgAAAACFupORp/8MS6vxhG/WUvawV1NURURHRTAzMjEARWRnZQ==* </br> Это уникальная эталонная строка, которая идентифицирует запрос, обслуженный Front Door. Она крайне важна для устранения неполадок, так как используется для поиска в журналах доступа.|

## <a name="next-steps"></a>Дополнительная информация

- Дополнительные сведения о [создании Front Door](quickstart-create-front-door.md).
- Дополнительные сведения о том, [как работает Front Door](front-door-routing-architecture.md).

<!--Image references-->
[1]: ./media/front-door-http-headers-protocol/front-door-protocol-summary.png