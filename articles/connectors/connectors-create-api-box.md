---
title: Подключение к Box с помощью Azure Logic Apps | Документация Майкрософт
description: Создавайте файлы и управляйте ими с помощью интерфейсов REST API Box и Azure Logic Apps.
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.date: 11/07/2016
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: b5c8c18c6d02710646560f29d4bc7b5784f730a2
ms.sourcegitcommit: b5ac31eeb7c4f9be584bb0f7d55c5654b74404ff
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/23/2018
ms.locfileid: "42746883"
---
# <a name="create-and-manage-files-in-box-with-azure-logic-apps"></a>Создание файлов в Box и управление ими с помощью Azure Logic Apps

В этой статье показано, как вы можете создавать файлы в Box и управлять ими изнутри приложения логики с помощью соединителя Box. Таким образом можно создавать приложения логики, которые автоматизируют задачи и рабочие процессы по управлению файлами, а также позволяют:

* формировать бизнес-процессы на основе данных, получаемых из Box; 

* запускать автоматизированные задачи и рабочие процессы при создании или обновлении файлов;

* запускать действия по копированию, удалению файлов и т. д. 

  Когда эти действия получают ответ, они делают выходные данные доступными для использования другими действиями. 
  Например, при изменении файла в Box можно отправить измененный файл по электронной почте с помощью Office 365.

## <a name="prerequisites"></a>Предварительные требования

* Учетная запись [Box](https://www.box.com/home).

* Подписка Azure. Если у вас еще нет подписки Azure, <a href="https://azure.microsoft.com/free/" target="_blank">зарегистрируйтесь для получения бесплатной учетной записи Azure</a>. 

* Приложение логики, из которого необходимо получить доступ к учетной записи Box. Чтобы запустить приложение логики с помощью триггера Box, требуется [пустое приложение логики](../logic-apps/quickstart-create-first-logic-app-workflow.md). 

* Базовые знания о [создании приложений логики](../logic-apps/quickstart-create-first-logic-app-workflow.md).
Если вы не знакомы с приложениями логики, ознакомьтесь со статьей [Что такое Azure Logic Apps](../logic-apps/logic-apps-overview.md).

## <a name="connector-reference"></a>Справочник по соединителям

См. дополнительные технические сведения о [триггерах, действиях и ограничениях, описываемых файлом Swagger соединителя](/connectors/box/). 

## <a name="get-support"></a>Получение поддержки

* Если у вас возникли вопросы, то посетите [форум Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Отправить идею по поводу возможности или проголосовать за нее вы можете на [сайте отзывов пользователей Logic Apps](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Дополнительная информация

* См. дополнительные сведения о других [соединителях Logic Apps](../connectors/apis-list.md).