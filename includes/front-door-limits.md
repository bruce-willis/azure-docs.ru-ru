---
title: включение файла
description: включение файла
services: frontdoor
author: sharad4u
ms.service: frontdoor
ms.topic: include
ms.date: 9/17/2018
ms.author: sharadag
ms.custom: include file
ms.openlocfilehash: f0c2d1501b9aa19dec8c4ad157e004a57e0e5070
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47006537"
---
| Ресурс | Ограничение по умолчанию |
| --- | --- |
| Ресурсы Front Door для подписки | 100 |
| Интерфейсные узлы, включая личные домены, для одного ресурса | 100 |
| Правила маршрутизации для одного ресурса | 100 |
| Внутренние пулы для одного ресурса | 50 |
| Внутренние серверы во внутреннем пуле | 100 |
| Схемы пути для сопоставления с помощью правила маршрутизации | 25 |
| Настраиваемые правила брандмауэра веб-приложения для одной политики | 10 |
| Политики брандмауэра веб-приложения для одного ресурса | 100 |

### <a name="timeout-values"></a>Значения времени ожидания
#### <a name="client-to-front-door"></a>Клиент — Front Door
- Front Door использует время ожидания простоя TCP-подключения, равное 61 секунде.
#### <a name="front-door-to-application-backend"></a>Front Door — внутренний сервер приложения
- В случае получения блочного ответа будет возвращен код состояния 200, если получен первый блок данных.
- После пересылки HTTP-запроса на внутренний сервер Front Door ожидает 30 секунд поступление первого пакета с внутреннего сервера, прежде чем вернуть клиенту сообщение об ошибке 503.
- После получения первого пакета с внутреннего сервера Front Door ожидает 30 секунд (время ожидания простоя), прежде чем вернуть клиенту сообщение об ошибке 503.
- Время ожидания сеанса TCP между Front Door и внутренним сервером составляет 30 минут.

### <a name="upload--download-data-limit"></a>Ограничение передаваемых и загружаемых данных

|  | С кодированием блочной передачи (CTE) | Без блочной передачи HTTP |
| ---- | ------- | ------- |
| **Загрузить** | Размер скачиваемых данных не ограничен. | Размер скачиваемых данных не ограничен. |
| **Передача** |  Ограничения отсутствуют, пока размер передаваемых блоков CTE не превышает 28,6 МБ. | Размер блока не должен превышать 28,6 МБ. |
