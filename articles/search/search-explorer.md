---
title: Обозреватель поиска для обращения к индексам в службе "Поиск Azure" | Документация Майкрософт
description: Узнайте, как использовать обозреватель поиска для обращения к индексам в службе "Поиск Azure".
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.topic: conceptual
ms.date: 07/10/2018
ms.author: heidist
ms.openlocfilehash: 520d9e7b1899c54d922ff6fb77e0901f9609b029
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/13/2018
ms.locfileid: "39004139"
---
# <a name="how-to-use-search-explorer-to-query-indexes-in-azure-search"></a>Как использовать обозреватель поиска для обращения к индексам в службе "Поиск Azure" 

В этой статье показано, как отправлять запросы в существующий индекс службы "Поиск Azure" с помощью **обозревателя поиска** на портале Azure. Используйте обозреватель поиска, чтобы отправить простые строки запросов или расширенные строки запросов Lucene в любой имеющийся индекс в службе.

## <a name="open-the-service-dashboard"></a>Открытие панели мониторинга службы
1. Щелкните **Все ресурсы** на панели переходов слева на [портале Azure](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices).
2. Выберите службу поиска Azure.

## <a name="select-an-index"></a>Выбор индекса

Выберите индекс, в котором нужно выполнить поиск, на плитке **Индексы**.

   ![](./media/search-explorer/pick-index.png)

## <a name="open-search-explorer"></a>Открытие обозревателя поиска

Щелкните плитку обозревателя поиска, чтобы открыть панель поиска и область результатов.

   ![](./media/search-explorer/search-explorer-tile.png)

## <a name="start-searching"></a>Начните поиск.

При использовании обозревателя поиска можно указать [параметры запроса](https://docs.microsoft.com/rest/api/searchservice/Search-Documents), чтобы сформировать запрос.

1. В **строке запроса** введите запрос и нажмите кнопку **Поиск**. 

   Строка запроса автоматически преобразуется в правильный URL-адрес запроса для отправки HTTP-запроса в REST API службы поиска Azure.   
   
   Используйте любой допустимый простой синтаксис запросов или синтаксис расширенных запросов Lucene, чтобы создать запрос. Знак `*` эквивалентен пустому полю поиска или неуказанным условиям поиска, который возвращает все документы в произвольном порядке.

2. В области **результатов** результаты запроса будут представлены в необработанном формате JSON аналогично полезным данным, возвращенным в тексте HTTP-ответа при программной отправке запросов.

   ![](./media/search-explorer/search-bar.png)

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о синтаксисе запросов и примеры см. в следующих ресурсах.

 + [Простой синтаксис запросов](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) 
 + [Синтаксис запросов Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) 
 + [Примеры синтаксиса запросов Lucene для создания запросов в службе поиска Azure](https://docs.microsoft.com/azure/search/search-query-lucene-examples) 
 + [OData Expression Syntax for Azure Search](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) (Синтаксис выражений OData для службы поиска Azure) 