---
title: Миграция в службу "База данных Azure для MySQL" с минимальным простоем
description: В этой статье объясняется, как с помощью Azure Database Migration Service перенести базу данных MySQL в Базу данных Azure для MySQL с минимальным временем простоя.
services: mysql
author: HJToland3
ms.author: jtoland
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 06/21/2018
ms.openlocfilehash: ecbd35bd45bd11292bbe4a032329d704858d4c77
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/20/2018
ms.locfileid: "36293927"
---
# <a name="minimal-downtime-migration-to-azure-database-for-mysql"></a>Миграция в службу "База данных Azure для MySQL" с минимальным простоем
Благодаря новой возможности **непрерывной синхронизации** в [Azure Database Migration Service](https://aka.ms/get-dms) (DMS) вы можете переносить базы данных MySQL в Базу данных Azure для MySQL с минимальным временем простоя. Эта функция позволяет сократить время простоя при работе приложения.

## <a name="overview"></a>Обзор
DMS выполняет начальную загрузку локальных баз данных в Базу данных Azure для MySQL и непрерывно синхронизирует все новые транзакции с Azure во время работы приложения. Когда данные поступят в целевую службу Azure, остановите приложение на короткое время (минимальное время простоя), подождите, пока последний пакет данных (с момента остановки приложения до его фактической недоступности для принятия нового трафика) будет передан в целевую службу, а затем обновите строку подключения, чтобы она указывала на Azure. По завершении приложение станет доступно в Azure.

![Непрерывная синхронизация с Azure Database Migration Service](./media/howto-migrate-online/ContinuousSync.png)

Сейчас в DMS миграция источников MySQL находится на этапе предварительной версии. Если вы хотите попробовать перенести рабочие нагрузки MySQL с помощью службы, зарегистрируйтесь на странице [предварительной версии](https://aka.ms/dms-preview) Azure DMS, чтобы сообщить о своей заинтересованности. Ваши отзывы очень важны для нас. Они помогают нам улучшать службу.

## <a name="next-steps"></a>Дополнительная информация
- Просмотрите видео [Easily migrate MySQL/PostgreSQL apps to Azure managed service](https://medius.studios.ms/Embed/Video/THR2201?sid=THR2201) (Простой перенос приложений MySQL и PostgreSQL в управляемую службу Azure), в котором демонстрируется миграция приложений MySQL в Базу данных Azure для MySQL.
- Зарегистрируйтесь, чтобы использовать ограниченную предварительную версию для миграции приложений MySQL в Базу данных Azure для MySQL с минимальным временем простоя, на [этой странице](https://aka.ms/dms-preview) Azure DMS.
