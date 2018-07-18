---
title: включение файла
description: включение файла
services: logic-apps
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.date: 05/09/2018
ms.author: estfan
ms.custom: include file
ms.openlocfilehash: 524bc1d3a19ad8ecfc8d0d210e735d6a9ae0058b
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34678320"
---
### <a name="set-up-pagination"></a>Настройка разбиения на страницы

Результаты некоторых соединителей и их действий, которые извлекают несколько элементов, могут не помещаться на странице по умолчанию для соединителя. В этом случае действие возвращает только первую страницу результатов. Например, размер страницы по умолчанию для действия **SQL Server - Get rows** равен 2048, но его можно изменить с помощью параметров. Чтобы гарантированно получить все записи, включите параметр **Разбивка на страницы** для этого действия. Благодаря этому параметру ваше приложение логики запросит у соединителя оставшиеся записи, но вернет все результаты в одном сообщении по завершении действия. 

Ниже приведены только некоторые соединители, для которых можно включить разбиение на страницы для конкретных действий: 

* <a href="https://docs.microsoft.com/connectors/db2/" target="_blank">DB2</a>
* <a href="https://docs.microsoft.com/connectors/dynamicscrmonline/" target="_blank">Dynamics 365 CRM Online</a>
* <a href="https://docs.microsoft.com/connectors/excel/" target="_blank">Excel</a>
* <a href="https://docs.microsoft.com/connectors/oracle/" target="_blank">База данных Oracle</a>
* <a href="https://docs.microsoft.com/connectors/sharepointonline/" target="_blank">SharePoint</a>
* <a href="https://docs.microsoft.com/connectors/sql/" target="_blank">SQL Server</a> 

Ниже приведен пример для действия **Get rows**.

1. Чтобы определить, поддерживает ли действие разбиение на страницы, откройте его **параметры**. 

   ![Открытие параметров действия](./media/connectors-pagination-bulk-data-transfer/sql-action-settings.png)

2. Если действие поддерживает разбиение на страницы, измените значение параметра **Разбивка на страницы** с **Выключено** на **Включено**. Чтобы действие возвращало минимальный набор результатов, укажите значение для параметра **Ограничение**.

   ![Указание минимального возвращаемого числа результатов для действия](./media/connectors-pagination-bulk-data-transfer/sql-action-settings-pagination.png)

3. Когда вы будете готовы, нажмите кнопку **Готово**.