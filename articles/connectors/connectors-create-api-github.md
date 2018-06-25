---
title: Подключение к GitHub при помощи Azure Logic Apps | Документация Майкрософт
description: Отслеживайте события GitHub с помощью интерфейсов REST API GitHub и Azure Logic Apps.
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.date: 03/02/2018
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: ce567dc631c3a147b795eb2355a4961faa8881d6
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35295818"
---
# <a name="connect-to-github"></a>Подключение к GitHub

GitHub — это веб-служба, где размещен репозиторий Git. Она обеспечивает все возможности распределенного управления версиями и исходным кодом (SCM) в Git, а также другие функции.

Чтобы приступить к работе с соединителем GitHub, [сначала создайте приложение логики](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-github"></a>Создание подключения к GitHub

Чтобы использовать в приложении логики соединитель GitHub, сначала создайте *подключение*, а затем введите сведения для следующих свойств: 

| Свойство | Обязательно | ОПИСАНИЕ | 
| -------- | -------- | ----------- | 
| по маркеру | Yes | Укажите свои учетные данные GitHub. |

Создав подключение, вы можете выполнять действия и ожидать передачи данных от триггеров, описанных в этой статье.

> [!INCLUDE [Steps to create a connection to GitHub](../../includes/connectors-create-api-github.md)]
> 

## <a name="connector-specific-details"></a>Сведения о соединителях

Сведения о триггерах, действиях, определенных в Swagger, и всех ограничениях см. в статье о [соединителях](/connectors/github/).

## <a name="find-more-connectors"></a>Другие соединители

* Просмотрите [список соединителей](apis-list.md).