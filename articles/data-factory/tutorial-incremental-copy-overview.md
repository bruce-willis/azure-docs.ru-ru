---
title: Пошаговое копирование данных с помощью фабрики данных Azure | Документация Майкрософт
description: В этих руководствах описывается добавочное копирование данных из исходного в целевое хранилище данных. В первом руководстве данные копируются из одной таблицы.
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/22/2018
ms.author: yexu
ms.openlocfilehash: 5151ef834a35f410d8caf9904cd3c7567a8958ad
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/27/2018
ms.locfileid: "43088782"
---
# <a name="incrementally-load-data-from-a-source-data-store-to-a-destination-data-store"></a>Добавочная загрузка данных из исходного хранилища данных в целевое

В решениях для интеграции данных добавочная (разностная) загрузка данных после начальной загрузки данных является широко используемым сценарием. В руководствах этого раздела показаны различные способы пошаговой загрузки данных с помощью Фабрики данных Azure.

## <a name="delta-data-loading-by-using-a-watermark"></a>Разностная загрузка данных c использованием предела
В этом случае следует определить предел в базе данных-источнике. Предел представляет собой столбец, содержащий метку времени последнего обновления или добавочный ключ. Решение разностной загрузки загружает измененные данные между значениями старого и нового пределов. Рабочий процесс для этого подхода показан на следующей схеме: 

![Рабочий процесс использования предела](media/tutorial-incremental-copy-overview/workflow-using-watermark.png)

Пошаговые инструкции представлены в следующих статьях: 

- [Пошаговая загрузка данных из базы данных SQL Azure в хранилище BLOB-объектов Azure](tutorial-incremental-copy-powershell.md)
- [Добавочная загрузка данных из нескольких таблиц в SQL Server в базу данных SQL Azure](tutorial-incremental-copy-multiple-tables-powershell.md)

## <a name="delta-data-loading-by-using-the-change-tracking-technology"></a>Разностная загрузка данных путем использования технологии отслеживания изменений
Технология отслеживания изменений — это упрощенное решение в SQL Server и Базе данных SQL Azure, которое предоставляет эффективный механизм отслеживания изменений для приложений. Эта технология позволяет приложению легко идентифицировать вставленные, обновленные или удаленные данные. 

Рабочий процесс для этого подхода показан на следующей схеме:

![Рабочий процесс для использования отслеживания изменений](media/tutorial-incremental-copy-overview/workflow-using-change-tracking.png)

Пошаговые инструкции представлены в следующем руководстве: <br/>
[Добавочная загрузка данных из базы данных SQL Azure в хранилище BLOB-объектов Azure с использованием сведений об отслеживании изменений](tutorial-incremental-copy-change-tracking-feature-powershell.md)

## <a name="next-steps"></a>Дополнительная информация
Перейдите к следующему руководству: 

> [!div class="nextstepaction"]
>[Пошаговая загрузка данных из базы данных SQL Azure в хранилище BLOB-объектов Azure](tutorial-incremental-copy-powershell.md)
