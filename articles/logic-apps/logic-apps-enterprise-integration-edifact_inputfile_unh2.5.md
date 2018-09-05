---
title: Обработка сообщений EDIFACT с помощью сегментов UNH 2.5 в Azure Logic Apps | Документация Майкрософт
description: Сведения о разрешении документов EDIFACT с помощью сегментов UNH 2.5 в Azure Logic Apps с Пакетом интеграции Enterprise.
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: cf44af18-1fe5-41d5-9e06-cc57a968207c
ms.date: 04/27/2017
ms.openlocfilehash: 9c8b8611347840dcf49759dac51fb506815cd782
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/28/2018
ms.locfileid: "43122014"
---
# <a name="handle-edifact-documents-with-unh25-segments-in-azure-logic-apps"></a>Обработка документов EDIFACT с помощью сегментов UNH 2.5 в Azure Logic Apps

При наличии сегмента UNH2.5 в документе EDIFACT он используется для поиска схемы. 

Например, поле UNH представляет **EAN008** в сообщении EDIFACT.  
UNH+SSDD1+ORDERS:D:03B:UN:**EAN008**'  

Чтобы обработать сообщение, сделайте следующее: 
1. Обновите схему.
2. Проверьте параметры соглашения.  

## <a name="update-the-schema"></a>Обновление схемы
Чтобы обработать сообщение, вам необходимо развернуть схему с именем корневого узла UNH2.5.  В этом примере корневое имя схемы будет выглядеть так: **EFACT_D03B_ORDERS_EAN008**  

Для каждого D03B_ORDERS с разными сегментами UNH2.5 вам понадобится развернуть отдельную схему.  

## <a name="add-schema-to-the-edifact-agreement"></a>Добавление схемы в соглашение EDIFACT
### <a name="edifact-decode"></a>Декодирование EDIFACT
Чтобы декодировать входящее сообщение, настройте схему в параметрах получения для соглашения EDIFACT.
1. Добавьте схему в учетную запись интеграции.    
2. Настройте схему в параметрах получения для соглашения EDIFACT. 
3. Выберите соглашение EDIFACT и щелкните **Редактирование в качестве JSON**.  Добавьте значение UNH2.5 в **schemaReferences**
![](./media/logic-apps-enterprise-integration-edifact_inputfile_unh2.5/image1.png) для соглашения получения.

### <a name="edifact-encode"></a>Кодирование EDIFACT
Чтобы выполнить кодирование входящего сообщения, настройте схему в параметрах отправки для соглашения EDIFACT.
1. Добавьте схему в учетную запись интеграции.    
2. Настройте схему в параметрах отправки для соглашения EDIFACT. 
3. Выберите соглашение EDIFACT и щелкните **Редактирование в качестве JSON**.  Добавьте значение UNH2.5 в **schemaReferences**
![](./media/logic-apps-enterprise-integration-edifact_inputfile_unh2.5/image2.png) для соглашения отправки.

## <a name="next-steps"></a>Дальнейшие действия
* [Дополнительные сведения о соглашениях учетных записей интеграции](../logic-apps/logic-apps-enterprise-integration-agreements.md "Дополнительные сведения о корпоративных соглашениях интеграции")  