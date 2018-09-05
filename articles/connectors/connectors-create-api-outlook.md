---
title: Подключение к Outlook.com. Azure Logic Apps | Документация Майкрософт
description: Управление сообщениями электронной почты, календарями и контактами с помощью REST API Outlook.com и Azure Logic Apps
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
manager: jeconnoc
ms.assetid: 87113c85-d158-4dd5-9ed5-5748130003d6
ms.topic: article
ms.date: 08/18/2016
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: 8030ab9d317c1deefaf441008b9022c4a26bb17c
ms.sourcegitcommit: b5ac31eeb7c4f9be584bb0f7d55c5654b74404ff
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/23/2018
ms.locfileid: "42746168"
---
# <a name="manage-email-calendars-and-contacts-in-outlookcom-with-azure-logic-apps"></a>Управление сообщениями электронной почты, календарями и контактами в Outlook.com с помощью Azure Logic Apps

В этой статье показано, как вы можете создать учетную запись Outlook.com и управлять ею внутри приложения логики с помощью соединителя Box. Таким образом можно создавать приложения логики, которые автоматизируют задачи и рабочие процессы для учетной записи Outlook.com, например:

* отправка сообщения электронной почты; 
* планирование встреч;
* добавление контактов. 

Если вы не знакомы с приложениями логики, ознакомьтесь со статьей [Что такое Azure Logic Apps](../logic-apps/logic-apps-overview.md).

## <a name="prerequisites"></a>Предварительные требования

* [Учетная запись Outlook.com.](https://outlook.live.com/owa/)

* Подписка Azure. Если у вас еще нет подписки Azure, <a href="https://azure.microsoft.com/free/" target="_blank">зарегистрируйтесь для получения бесплатной учетной записи Azure</a>. 

* Приложение логики, из которого необходимо получить доступ к учетной записи Outlook.com. Чтобы запустить приложение логики с помощью триггера Outlook, требуется [пустое приложение логики](../logic-apps/quickstart-create-first-logic-app-workflow.md). 

* Базовые знания о [создании приложений логики](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="connect-to-outlookcom"></a>Подключение к Outlook.com

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

[!INCLUDE [Connect to Outlook.com](../../includes/connectors-create-api-outlook.md)]

## <a name="connector-reference"></a>Справочник по соединителям

См. дополнительные технические сведения о [триггерах, действиях и ограничениях, описываемых файлом Swagger соединителя](/connectors/outlook/). 

## <a name="get-support"></a>Получение поддержки

* Если у вас возникли вопросы, то посетите [форум Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Отправить идею по поводу возможности или проголосовать за нее вы можете на [сайте отзывов пользователей Logic Apps](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Дополнительная информация

* См. дополнительные сведения о других [соединителях Logic Apps](../connectors/apis-list.md).