---
title: Подключение к MailChimp с помощью Azure Logic Apps | Документация Майкрософт
description: Создавайте маркетинговые сообщения и управляйте ими с помощью интерфейсов REST API MailChimp и Azure Logic Apps.
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.date: 08/18/2016
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: c9bcf48254d05ccb18abc55e49ea44b714b727cd
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35295260"
---
# <a name="get-started-with-the-mailchimp-connector"></a>Начало работы с соединителем MailChimp
MailChimp — это служба SaaS, которая позволяет компаниям управлять маркетинговыми мероприятиями по электронной почте, включая отправку маркетинговых сообщений электронной почты, автоматических сообщений и целевых кампаний, и автоматизировать их.

Для начала можно создать приложение логики, как описано [здесь](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-mailchimp"></a>Создание подключения к MailChimp
Для создания приложений логики с помощью MailChimp необходимо создать **подключение**, а затем указать данные для следующих свойств.

| Свойство | Обязательно | ОПИСАНИЕ |
| --- | --- | --- |
| по маркеру |Yes |Укажите учетные данные MailChimp |

> [!INCLUDE [Steps to create a connection to MailChimp](../../includes/connectors-create-api-mailchimp.md)]
> 


## <a name="connector-specific-details"></a>Сведения о соединителях

Информацию о существующих ограничениях, а также о триггерах и действиях, определенных в Swagger, см. в статье со [сведениями о соединителях](/connectors/mailchimp/).

## <a name="more-connectors"></a>Дополнительные сведения о соединителях
Вы можете вернуться к [списку интерфейсов API](apis-list.md).