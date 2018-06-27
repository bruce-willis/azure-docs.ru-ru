---
title: Миграция в базу данных Azure для PostgreSQL с минимальным временем простоя
description: В этой статье объясняется, как с помощью Azure Database Migration Service перенести базу данных PostgreSQL в Базу данных Azure для PostgreSQL с минимальным временем простоя.
services: postgresql
author: HJToland3
ms.author: jtoland
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 06/21/2018
ms.openlocfilehash: 9ab5d4615a8baf763d0b7ee47bf0890124f8665c
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/20/2018
ms.locfileid: "36292548"
---
# <a name="minimal-downtime-migration-to-azure-database-for-postgresql"></a>Миграция в базу данных Azure для PostgreSQL с минимальным временем простоя
Благодаря новой возможности **непрерывной синхронизации**, реализованной в службе [Azure Database Migration Service](https://aka.ms/get-dms) (DMS), вы можете переносить базы данных PostgreSQL в Базу данных Azure для PostgreSQL с минимальным временем простоя. Эта функция позволяет сократить время простоя при работе приложения.

## <a name="overview"></a>Обзор
DMS выполняет начальную загрузку локальных данных в Базу данных Azure для PostgreSQL и непрерывно синхронизирует все новые транзакции с Azure. Все это время приложение продолжает работать. Когда данные поступят в целевую службу Azure, остановите приложение на короткое время (минимальное время простоя), подождите, пока последний пакет данных (с момента остановки приложения до его фактической недоступности для принятия нового трафика) будет передан в целевую службу, а затем обновите строку подключения, чтобы она указывала на Azure. По завершении приложение станет доступно в Azure.

![Непрерывная синхронизация с Azure Database Migration Service](./media/howto-migrate-online/ContinuousSync.png)

Сейчас функция миграции источников PostgreSQL в DMS доступна в режиме предварительной версии. Если вы хотите попробовать перенести рабочие нагрузки PostgreSQL с помощью службы, зарегистрируйтесь на странице [предварительной версии](https://aka.ms/dms-preview) Azure DMS. Ваши отзывы очень важны для нас. Они помогают нам улучшать службу.

## <a name="next-steps"></a>Дополнительная информация
- Посмотрите видео [App Modernization with Microsoft Azure](https://medius.studios.ms/Embed/Video/BRK2102?sid=BRK2102) (Усовершенствование приложений с помощью Microsoft Azure), в котором показано, как переносить приложения PostgreSQL в Базу данных Azure для PostgreSQL.
- Зарегистрируйтесь, чтобы использовать ограниченную [предварительную версию](https://aka.ms/dms-preview) функции миграции приложений PostgreSQL в Базу данных Azure для PostgreSQL с минимальным временем простоя.
