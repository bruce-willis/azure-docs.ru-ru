---
title: 'Azure Front Door Service: метрики и ведение журнала | Документация Майкрософт'
description: В этой статье содержатся сведения о различных метриках и журналах доступа, поддерживаемых службой Azure Front Door Service.
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/18/2018
ms.author: sharadag
ms.openlocfilehash: 643ae03a9350868b172d99b2af7ce23e34fedf47
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46998004"
---
# <a name="monitoring-metrics-for-front-door"></a>Метрики мониторинга для службы Front Door

С помощью службы Azure Front Door Service можно отслеживать ключевые метрики для проверки конфигурации Front Door, а также сведения об использовании, работоспособности и производительности службы Front Door.

## <a name="metrics"></a>Метрики

Метрики — это функция определенных ресурсов Azure, позволяющая просматривать данные счетчиков производительности на портале. Для службы Front Door доступны следующие метрики.

| Метрика | Отображаемое имя метрики | Единица измерения | Измерения | ОПИСАНИЕ |
| --- | --- | --- | --- | --- |
| RequestCount | Число запросов | Count | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Число клиентских запросов, обслуженных службой Front Door.  |
| RequestSize | Размер запроса | Байты | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Число байт, отправленных в качестве запросов от клиентов в службу Front Door. |
| ResponseSize | Размер ответа | Байты | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Число байт, отправленных клиентам в качестве ответов из службы Front Door. |
| TotalLatency | Total Latency (Общая задержка) | Миллисекунды | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Время с момента получения клиентского запроса службой Front Door до подтверждения клиентом последнего байта ответа от Front Door. |
| BackendRequestCount | Число запросов к серверному компоненту | Count | HttpStatus</br>HttpStatusGroup</br>Серверная часть | Число запросов, отправленных из службы Front Door к серверным частям. |
| BackendRequestLatency | Backend Request Latency (Задержка запросов к серверным частям) | Миллисекунды | Серверная часть | Время с момента отправки запроса службой Front Door к серверной части до получения Front Door последнего байта ответа от серверной части. |
| BackendHealthPercentage | Работоспособность серверного компонента (в процентах) | Процент | Серверная часть</br>BackendPool | Процент успешных проб работоспособности от службы Front Door к серверным частям. |
| WebApplicationFirewallRequestCount | Web Application Firewall Request Count (Число запросов к брандмауэру веб-приложения) | Count | PolicyName</br>RuleName</br>Действие | Количество клиентских запросов, обрабатываемых механизмом безопасности на прикладном уровне службы Front Door. |


## <a name="next-steps"></a>Дополнительная информация

- Дополнительные сведения о [создании Front Door](quickstart-create-front-door.md).
- Дополнительные сведения о том, [как работает Front Door](front-door-routing-architecture.md).
