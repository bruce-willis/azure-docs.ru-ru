---
title: XML-сообщения для интеграции с предприятием B2B в Azure Logic Apps | Документация Майкрософт
description: Обработка, проверка, преобразование и добавление возможностей XML-сообщений для решений B2B в Azure Logic Apps с помощью Пакета интеграции Enterprise
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: 47672dc4-1caa-44e5-b8cb-68ec3a76b7dc
ms.date: 02/27/2017
ms.openlocfilehash: 5f804bf656b423d6dbe8f5c2ed73ba54d8361000
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/28/2018
ms.locfileid: "43124541"
---
# <a name="xml-messages-and-flat-files-in-azure-logic-apps-with-enterprise-integration-pack"></a>XML-сообщения и неструктурированные файлы в Azure Logic Apps с Пакетом интеграции Enterprise

С помощью приложений логики можно обрабатывать отправляемые и получаемые сообщения XML. Эта функция в состав Пакета интеграции Enterprise. Для пользователей, работавших с BizTalk Server, Пакет интеграции Enterprise обеспечит аналогичные возможности по преобразованию и проверке сообщений, работе с неструктурированными файлами и даже использованию XPath для обогащения сообщений или извлечения из них свойств. 

Пользователи, не знакомые с этой областью, с помощью данных функций смогут расширить возможности обработки сообщений в рабочем процессе. Например, если в сценарии B2B вы работаете с конкретными схемами XML, то можете использовать Пакет интеграции Enterprise для улучшения обработки этих сообщений в организации. 

Пакет интеграции Enterprise включает в себя следующее: 

* [Проверка XML](logic-apps-enterprise-integration-xml-validation.md "Узнайте о проверке сообщений XML") позволяет проверить входящее или исходящее сообщение XML на соответствие указанной схеме.
* [Преобразование XML](../logic-apps/logic-apps-enterprise-integration-transform.md "Узнайте о преобразованиях сообщений XML и сопоставлениях") позволяет преобразовать или настроить сообщение XML в соответствии с вашими требованиями или требованиями партнера.
* [Кодирование и декодирование неструктурированных файлов](logic-apps-enterprise-integration-flatfile.md "См. дополнительные сведения о кодировании и декодировании неструктурированных файлов") позволяет кодировать и декодировать неструктурированный файл. Например, SAP принимает и отправляет IDOC-файлы в виде неструктурированных файлов. Многие платформы интеграции создают сообщения XML, включая Logic Apps. Поэтому можно создать приложение логики, которое использует кодировщик неструктурированных файлов для преобразования XML-файлов в неструктурированные файлы. 
* [XPath](https://msdn.microsoft.com/library/mt643789.aspx) позволяет дополнить сообщение и извлечь из него определенные свойства. Эти извлеченные свойства можно затем использовать для перенаправления сообщения в целевую или промежуточную конечную точку.

## <a name="try-it-out"></a>Попробуйте сейчас
[Разверните полностью работоспособное приложение логики](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-veter-pipeline) (пример с GitHub) с помощью функций XML в Azure Logic Apps.

## <a name="learn-more"></a>Подробнее
[Обзор пакета интеграции Enterprise](../logic-apps/logic-apps-enterprise-integration-overview.md "Обзор пакета интеграции Enterprise")
