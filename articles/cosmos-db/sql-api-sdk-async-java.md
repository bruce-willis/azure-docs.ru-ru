---
title: Azure Cosmos DB. Интерфейс API, пакет SDK и ресурсы для SQL Async Java | Документация Майкрософт
description: Сведения о пакете SDK и API-интерфейсе SQL Async Java, включая даты выхода и прекращения использования, а также изменения, внесенные в каждую версию пакета SDK SQL Async Java для Azure Cosmos DB.
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/16/2018
ms.author: sngun
ms.openlocfilehash: 9852edfca26e78ad1b373418970dfe9c7ab477d8
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/18/2018
ms.locfileid: "42142503"
---
# <a name="azure-cosmos-db-async-java-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB: заметки о выпуске и материалы по пакету SDK Async Java для API-интерфейса SQL
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [Веб-канал изменений в .NET](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Async Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [Поставщик ресурсов REST](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)
> * [BulkExecutor — .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [BulkExecutor — Java](sql-api-sdk-bulk-executor-java.md)

Пакет SDK Async Java для API-интерфейса SQL отличается от пакета SDK Java для API-интерфейса SQL возможностью выполнять асинхронные операции с поддержкой [библиотеки Netty](http://netty.io/). Существующий [пакет SDK Java для API-интерфейса SQL](sql-api-sdk-java.md) не поддерживает асинхронные операции. 

<table>

<tr><td>**Скачивание пакета SDK**</td><td>[Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb)</td></tr>

<tr><td>**Документация по API**</td><td>[Справочная документация по API Java](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx._async_document_client?view=azure-java-stable)</td></tr>

<tr><td>**Участие в разработке пакета SDK**</td><td>[GitHub](https://github.com/Azure/azure-cosmosdb-java)</td></tr>

<tr><td>**Начало работы**</td><td>[Начало работы с пакетом SDK для Async Java](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started)</td></tr>

<tr><td>**Пример кода**</td><td>[Github](https://github.com/Azure/azure-cosmosdb-java#usage-code-sample)</td></tr>

<tr><td>**Советы по улучшению производительности**</td><td>[Файл сведений Github](https://github.com/Azure/azure-cosmosdb-java#guide-for-prod)</td></tr>

<tr><td>**Минимальная поддерживаемая среда выполнения**</td><td>[JDK 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)</td></tr>
</table></br>

## <a name="release-notes"></a>Заметки о выпуске

### <a name="a-name201201"></a><a name="2.0.1"/>2.0.1
* Исправлена ошибка для не английских языковых стандартов ([github #51](https://github.com/Azure/azure-cosmosdb-java/issues/51)).
* Добавлены вспомогательные методы в раздел "Конфликт ресурсов".

### <a name="a-name200200"></a><a name="2.0.0"/>2.0.0
* Заменена зависимость org.json от jackson из соображений производительности и лицензирования ([github #29](https://github.com/Azure/azure-cosmosdb-java/issues/29)).
* Удален нерекомендуемый класс OfferV2.
* Добавлен метод доступа в класс Offer для материала о пропускной способности.
* Все методы в Document/Resource, возвращающие типы org.json, изменены для возвращения типа объекта jackson.
* Метод getObject(.) классов, расширяющих JsonSerializable, изменен для возвращения типа объекта ObjectNode jackson.
* Метод getCollection(.) изменен для возвращения коллекции ObjectNode.
* Удалены конструкторы подклассов JsonSerializable с аргументом org.json.JSONObject.
* JsonSerializable.toJson (SerializationFormattingPolicy.Indented) теперь использует двойной пробел для отступа.
  
### <a name="a-name102102"></a><a name="1.0.2"/>1.0.2
* Добавлена поддержка политики уникальных индексов.
* Добавлена поддержка ограничения размера маркера продолжения ответа в параметрах веб-канала.
* Добавлена поддержка разбиения секций в запросах между секциями.
* Исправлена ошибка в сериализации метки времени JSON ([№32 на сайте GitHub](https://github.com/Azure/azure-cosmosdb-java/issues/32)).
* Исправлена ошибка в сериализации перечисления JSON.
* Исправлена ошибка в управлении документами размером 2 МБ ([№33 на сайте GitHub](https://github.com/Azure/azure-cosmosdb-java/issues/33)).
* Зависимость com.fasterxml.jackson.core:jackson-databind обновлена до версии 2.9.5 из-за ошибки ([jackson-databind: №1599 на сайте GitHub](https://github.com/FasterXML/jackson-databind/issues/1599)).
* Зависимость от rxjava-extras обновлена до версии 0.8.0.17 из-за ошибки ([rxjava-extras: №30 на сайте GitHub](https://github.com/davidmoten/rxjava-extras/issues/30)).
* Описание метаданных в POM-файле обновлено в соответствии с остальной документацией.
* Улучшение синтаксиса ([№41 на сайте GitHub ](https://github.com/Azure/azure-cosmosdb-java/issues/41)), ([№40 на сайте GitHub](https://github.com/Azure/azure-cosmosdb-java/issues/40)).

### <a name="a-name101101"></a><a name="1.0.1"/>1.0.1
* В запросах добавлена поддержка обратной реакции.
* В запросах добавлена поддержка идентификатора диапазона для ключей секции.
* Внесено исправление, позволяющее использовать больший маркер продолжения в заголовке запроса (исправление ошибки 24 в GitHub).
* Зависимость Netty обновлена до версии 4.1.22.Final, чтобы обеспечить завершение работы виртуальной машины Java после выполнения основного потока.
* Внесено исправление, позволяющее избежать передачи маркера сеанса при считывании ресурсов главной ветви.
* Добавлены другие примеры.
* Добавлены другие сценарии тестирования производительности.
* Исправлены файлы заголовков Java для правильного создания документа Java.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* Пакет SDK в общедоступной версии с комплексной поддержкой неблокирующих операций ввода-вывода и [библиотеки Netty](http://netty.io/) в режиме шлюза. 

## <a name="release-and-retirement-dates"></a>Даты выпуска и выбытия
Корпорация Майкрософт отправит уведомление минимум за **12 месяцев** до вывода пакета SDK из эксплуатации, чтобы обеспечить более плавный переход на новую или поддерживаемую версию.

Новые компоненты, функции и средства оптимизации добавлены только в текущий пакет SDK. Поэтому рекомендуем как можно раньше обновлять систему до последней версии пакета SDK.

Любые запросы к Cosmos DB с помощью выведенного из эксплуатации пакета SDK будут отклонены службой.

<br/>

| Version (версия) | Дата выпуска | Дата вывода |
| --- | --- | --- |
| [2.0.1](#2.0.1) |16 августа 2018 г.|--- |
| [2.0.0](#2.0.0) |20 июня 2018 г.|--- |
| [1.0.2](#1.0.2) |18 мая 2018 г.|--- |
| [1.0.1](#1.0.1) |20 апреля 2018 г.|--- |
| [1.0.0](#1.0.0) |27 февраля 2018 г|--- |

## <a name="faq"></a>Часто задаваемые вопросы
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>См. также
Дополнительные сведения о Cosmos DB см. на странице службы [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

