---
title: Основные понятия высокого уровня доступности в базе данных Azure для MySQL
description: Этот раздел содержит сведения о высоком уровне доступности при использовании базы данных Azure для MySQL.
services: mysql
author: jasonwhowell
ms.author: jasonh
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 90dc603c0ee520774bd22531c7136e0949f6cf90
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35264186"
---
# <a name="high-availability-concepts-in-azure-database-for-mysql"></a>Основные понятия высокого уровня доступности в базе данных Azure для MySQL
Служба "База данных Azure для службы MySQL" обеспечивает гарантированный высокий уровень доступности. Платные соглашения об уровне обслуживания обеспечивают доступность 99,99% в общедоступной версии. При использовании этой службы время простоя приложений практически отсутствует.

## <a name="high-availability"></a>высокую доступность;
Модель высокого уровня доступности (HA) основана на встроенных механизмах отработки отказа, срабатывающих при прерывании работы на уровне узла. Прерывание работы на уровне узла может произойти из-за сбоя оборудования или в ответ на развертывание службы.

Изменения, вносимые в сервер базы данных Azure для MySQL, происходят в контексте транзакции на постоянной основе. Изменения синхронно записываются в службу хранилища Azure, когда транзакция фиксируется. В случае прерывания работы на уровне узла сервер базы данных автоматически создает новый узел и подключает к нему хранилище данных. Все активные подключения прерываются, а все текущие транзакции не фиксируются.

## <a name="application-retry-logic-is-essential"></a>Важность логики повтора в приложении
Очень важно, чтобы приложения базы данных MySQL могли обнаруживать и восстанавливать прерванные подключения и повторять поврежденные транзакции. Когда приложение повторяет попытку операции, его подключение прозрачно перенаправляется к вновь созданному экземпляру, который заменяет поврежденный экземпляр.

Внутри платформы Azure для перенаправления подключений к новому экземпляру используется шлюз. После прерывания весь процесс отработки отказа обычно занимает десятки секунд. Так как перенаправление внутренне обрабатывается шлюзом, внешняя строка подключения остается неизменной для клиентских приложений.

## <a name="scaling-up-or-down"></a>Увеличение или уменьшение масштаба
Как и в модели высокого уровня доступности, при масштабировании базы данных Azure для MySQL создается новый экземпляр сервера с указанным размером. Существующее хранилище данных отключается от исходного экземпляра и подключается к новому экземпляру.

Во время операции масштабирования происходит прерывание подключений к базе данных. Клиентские приложения отключаются, а открытые незафиксированные транзакции отменяются. Когда клиентское приложение предпринимает попытку восстановить подключение или устанавливает новое подключение, шлюз направляет это подключение к экземпляру с новым размером. 

## <a name="next-steps"></a>Дополнительная информация
- Обзор службы содержится в статье [What is Azure Database for MySQL? Service Introduction](overview.md) (Что такое база данных Azure для MySQL? Общие сведения о службе).
