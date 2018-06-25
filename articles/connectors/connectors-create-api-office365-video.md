---
title: Подключение к Office 365 Видео с помощью Azure Logic Apps | Документация Майкрософт
description: Управляйте видео с помощью интерфейсов REST API Office 365 Видео и Azure Logic Apps.
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.date: 05/18/2016
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 44152101f1a2be6268d4e27430fdf60ef0131fae
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35295784"
---
# <a name="get-started-with-the-office365-video-connector"></a>Начало работы с соединителем Office 365 Видео
Подключившись к Office 365 Видео, вы сможете получать информацию об Office 365 Видео, список видео и многое другое. С помощью Office 365 Видео вы можете:

* формировать бизнес-процессы на основе данных, получаемых из Office 365 Видео; 
* использовать действия для проверки состояния видеопортала, получения списка всех видео, имеющихся в канале, и т. д. Эти действия получают ответ и делают выходные данные доступными для использования другими действиями. Например, можно воспользоваться соединителем Поиска Bing для поиска видео в Office 365, а затем соединителем Office 365 Видео для получения сведений об этом видео. Если видео соответствует вашим требованиям, его можно опубликовать в Facebook. 

Для начала можно создать приложение логики, как описано [здесь](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-office365-video-connector"></a>Создание подключения к соединителю Office 365 Видео
При добавлении этого соединителя в приложения логики необходимо войти в учетную запись Office 365 Видео и разрешить приложениям логики подключаться к вашей учетной записи.

> [!INCLUDE [Steps to create a connection to Office 365 Video](../../includes/connectors-create-api-office365video.md)]
> 
> 

После создания подключения укажите свойства Office 365 Видео, такие как имя клиента или идентификатор канала. 


## <a name="connector-specific-details"></a>Сведения о соединителях

Информацию о существующих ограничениях, а также о триггерах и действиях, определенных в Swagger, см. в статье со [сведениями о соединителях](/connectors/office365videoconnector/).

## <a name="more-connectors"></a>Дополнительные сведения о соединителях
Вы можете вернуться к [списку интерфейсов API](apis-list.md).