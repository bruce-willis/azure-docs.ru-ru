---
title: Подключение к Project Online из Azure Logic Apps | Документация Майкрософт
description: Автоматизация рабочих процессов, которые отслеживают, создают проекты, задачи и ресурсы Project Online и управляют ими с помощью Azure Logic Apps.
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.suite: integration
ms.topic: article
ms.assetid: 40ce621e-4925-4653-93bb-71ab9abcbdf1
tags: connectors
ms.date: 08/24/2018
ms.openlocfilehash: cfcb53b6e95250a1ccbebfdfcfbff5ec8479504b
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/24/2018
ms.locfileid: "42886959"
---
# <a name="manage-project-online-projects-tasks-and-resources-by-using-azure-logic-apps"></a>Управление проектами, задачами и ресурсами Project Online с помощью Azure Logic Apps

С помощью Azure Logic Apps и соединителя Project Online вы можете создавать автоматизированные задачи и рабочие процессы для своих проектов, задач и ресурсов в Project Online через Office 365. Рабочие процессы могут выполнять эти и другие действия, например:

* отслеживать создание проектов, задач или ресурсов, а также публикацию новых проектов;
* создавать проекты, задачи или ресурсы;
* получать список имеющихся проектов или задач;
* получать проекты для изменения, записывать их после изменения или публиковать.

Project Online помогает вам планировать, определять приоритеты и управлять проектами и инвестициями портфеля проектов практически из любого места на любом устройстве, предоставляя мощные возможности управления проектами. Вы можете использовать триггеры Project Online, которые получают ответы от Project Online и обеспечивают доступность выходных данных для других действий. Вы можете использовать действия в своих приложениях логики для выполнения различных задач в Project Online. Если вы не знакомы с приложениями логики, ознакомьтесь со статьей [Что такое Azure Logic Apps](../logic-apps/logic-apps-overview.md).

## <a name="prerequisites"></a>Предварительные требования

* Подписка Azure. Если у вас еще нет подписки Azure, <a href="https://azure.microsoft.com/free/" target="_blank">зарегистрируйтесь для получения бесплатной учетной записи Azure</a>. 

* Project Online, доступная через [учетную запись Office 365](https://www.office.com/). 

* Базовые знания [создания приложений логики](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Приложение логики, из которого необходимо получить доступ к данным Project Online. Чтобы начать работу с триггером Project Online, [создайте пустое приложение логики](../logic-apps/quickstart-create-first-logic-app-workflow.md). Чтобы использовать действия Project Online, запустите свое приложение логики с помощью другого триггера, например триггера **повторения**.

## <a name="connect-to-project-online"></a>Подключение к Project Online

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Войдите на [портал Azure](https://portal.azure.com) и откройте свое приложение логики в конструкторе приложений логики, если оно еще не открыто.

1. Выберите путь: 

   * Для пустых приложений логики в поле поиска в качестве фильтра введите "Project Online". 
   В списке триггеров выберите нужный триггер. 

     -или-

   * В имеющихся приложениях логики на шаге, в котором необходимо добавить действие, выберите **Новый шаг**. В поле поиска в качестве фильтра введите "Project Online". В списке действий выберите любое необходимое действие.

1. Если появится запрос войти в Project Online, выполните вход.

   Ваши учетные данные авторизуют приложение логики, чтобы оно могло создать подключение к Project Online и получить доступ к данным.

1. Укажите необходимые сведения для выбранного триггера или действия и продолжайте создание рабочего процесса приложения логики.

## <a name="connector-reference"></a>Справочник по соединителям

Дополнительные технические сведения о триггерах, действиях и ограничениях, которые приведены в описании OpenAPI соединителя (прежнее название — Swagger), можно найти на [странице справки](/connectors/projectonline/) соединителя.

## <a name="get-support"></a>Получение поддержки

* Если у вас возникли вопросы, то посетите [форум Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Отправить идею по поводу возможности или проголосовать за нее вы можете на [сайте отзывов пользователей Logic Apps](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Дополнительная информация

* См. дополнительные сведения о других [соединителях Logic Apps](../connectors/apis-list.md).