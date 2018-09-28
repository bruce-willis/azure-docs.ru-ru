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
ms.openlocfilehash: efb3ffeb2e5fdde79fe3741377b8a06074671fea
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46951140"
---
# <a name="azure-front-door-service---http-headers-protocol-support"></a>Служба Azure Front Door — поддержка протокола HTTP-заголовки
В этом документе описывается протокол, поддерживаемый службой Azure Front Door и различные фрагменты пути к вызовам, как показано на следующей схеме. В следующем разделе приводятся более подробные сведения об HTTP-заголовках, которые поддерживает Front Door.

![Служба Azure Front Door — протокол HTTP-заголовков][1]

>[!WARNING]
>Служба Azure Front Door не дает гарантий в отношении HTTP-заголовков, которые здесь не рассматриваются.

## <a name="1-client-to-front-door"></a>1. Клиент — Front Door

Front Door анализирует следующие заголовки. Любые другие заголовки Front Door пропускает и отправляет в серверную систему приложения.

| Заголовок  | Пример и описание |
| ------------- | ------------- |
| X-MSEdge-IG  | *X-MSEdge-IG: 2BC2B67F49ED47DFA47FBE2E962AC81F* </br> GUID, который определяет один или несколько HTTP-запросов, отправленных для обработки одной веб-страницы. Front Door будет пересылать GUID в серверную систему, используя заголовок X-FD-ImpressionGuid. </br> Если клиент не отправляет этот заголовок, Front Door автоматически создает новый GUID и отправляет его в серверную систему. |

## <a name="2-front-door-to-backend"></a>2. Front Door — серверная система

Front Door отправляет в серверную систему приложения следующие заголовки.

| Заголовок  | Пример и описание |
| ------------- | ------------- |
| X-MS-Ref |  *X-FD-Ref: 0WrHgWgAAAACFupORp/8MS6vxhG/WUvawV1NURURHRTAzMjEARWRnZQ==* </br> Идентификатор запроса, передаваемого Front Door. Значение этого заголовка содержит наиболее важную информацию для устранения неполадок. Данный идентификатор можно использовать для поиска по журналам доступа. Front Door отправляет один и тот же заголовок и в клиент, и в серверную систему. |
| X-FD-ClientHttpVersion | *X-FD-ClientHttpVersion: 1.1* </br>Версия протокола HTTP между клиентом и Front Door. Возможные значения: 1.1, 2.0 и т. д. |
| X-FD-ClientIP | *X-FD-ClientIP: 131.107.192.35* </br>IP-адрес клиента, связанный с обрабатываемым запросом. |
| X-FD-EdgeEnvironment | *X-FD-EdgeEnvironment: Edge-Prod-WSTr3* </br>Конкретный узел Front Door, обработавший запрос. Этот заголовок может регистрироваться серверной системой для использования в процессе отладки. При этом никакие зависимости от соглашений Front Door об именовании, распределении трафика между узлами Front Door или доступность определенного узла не фиксируются. |
| X-FD-EventID | *X-FD-EventId: FB805145C0124400915BE0E180F3A159* </br>Уникальный идентификатор (GUID), созданный в начале обработки запроса. Все журналы доступа, созданные во время обработки этого запроса, будут связаны с одним и тем же GUID. |
| X-FD-ImpressionGuid | *X-FD-ImpressionGuid: 2BC2B67F49ED47DFA47FBE2E962AC81F* </br>GUID, который определяет один или несколько HTTP-запросов, отправленных для обработки одной веб-страницы. На стороне клиента этот GUID можно указать следующим образом: </br>— Отправить в заголовке запроса *X-MSEdge-IG*. Например: X-MSEdge-IG: 2BC2B67F49ED47DFA47FBE2E962AC81F </br>— Отправить в заголовке запроса "ig". Например: *?ig=2BC2B67F49ED47DFA47FBE2E962AC81F*. Если клиент не отправляет GUID, Front Door создает новый. |
| X-FD-OriginalURL | *X-FD-OriginalURL: http://www.contoso.com:80/* </br> URL-адрес исходного запроса, отправленного клиентом. |
| X-FD-Partner | *X-FD-Partner: ARM-contoso.azurefd.net_Default* </br>Идентификатор профиля Front Door, для которого обрабатывается запрос. Серверные системы, в которых размещаются несколько приложений для различных экземпляров Front Door, могут использовать этот идентификатор для того, чтобы определять, для какой конфигурации Front Door они обслуживают текущий запрос. |
| X-FD-RequestHadClientId | Reserved |
| X-FD-ResponseHasClientId | Reserved |
| X-FD-SocketIP | *X-FD-SocketIP: 131.107.192.35* </br>То же самое, что и указанный выше X-FD-ClientIP. |

## <a name="3-front-door-to-client"></a>3. Front Door — клиент

Ниже перечислены заголовки, которые отправляются из Front Door клиентам. Все заголовки, отправляемые в службу Front Door из серверной части, также передаются клиенту.

| Заголовок  | Пример |
| ------------- | ------------- |
| X-MS-Ref  | *X-FD-Ref: 0WrHgWgAAAACFupORp/8MS6vxhG/WUvawV1NURURHRTAzMjEARWRnZQ==* </br>Идентификатор запроса, передаваемого Front Door. Значение этого заголовка содержит наиболее важную информацию для устранения неполадок. Данный идентификатор можно использовать для поиска по журналам доступа. Front Door отправляет один и тот же заголовок и в клиент, и в серверную систему. |

## <a name="next-steps"></a>Дополнительная информация

- Дополнительные сведения о [создании Front Door](quickstart-create-front-door.md).
- Дополнительные сведения о том, [как работает Front Door](front-door-routing-architecture.md).

<!--Image references-->
[1]: ./media/front-door-http-headers-protocol/front-door-protocol-summary.png