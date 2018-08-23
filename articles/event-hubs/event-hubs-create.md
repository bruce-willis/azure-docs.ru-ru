---
title: Создание концентратора событий Azure | Документация Майкрософт
description: Узнайте, как создать пространство имен Центров событий Azure и концентратор событий с помощью портала Azure.
services: event-hubs
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.date: 08/16/2018
ms.author: shvija
ms.openlocfilehash: 5b468e1758d752cd3001c85b328d064369429499
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/17/2018
ms.locfileid: "42142718"
---
# <a name="create-an-event-hubs-namespace-and-an-event-hub-using-the-azure-portal"></a>Создание пространства имен Центров событий и концентратора событий с помощью портала Azure

## <a name="create-an-event-hubs-namespace"></a>Создание пространства имен в Центрах событий

1. Войдите на [портал Azure][Azure portal] и щелкните **Создать ресурс** в левой верхней части экрана.
2. Последовательно выберите **Интернет вещей** и **Центры событий**.
   
    ![](./media/event-hubs-create/create-event-hub9.png)

3. В разделе **создания пространства имен** укажите имя пространства имен. Система немедленно проверяет, доступно ли оно.  

4. Убедившись, что пространство имен доступно, выберите ценовую категорию: "Базовый" или "Стандартный". Также выберите подписку Azure, группу ресурсов и расположение для создания ресурса.
 
5. Щелкните **Создать** , чтобы создать пространство имен. Полная подготовка ресурсов для системы может занять несколько минут.

    ![](./media/event-hubs-create/create-event-hub1.png)

6. На портале в списке пространств имен щелкните имя только что созданного пространства имен.

7. В колонке **Политики общего доступа** щелкните **RootManageSharedAccessKey**.
    
    ![](./media/event-hubs-create/create-event-hub7.png)

8. Нажмите кнопку копирования, чтобы скопировать строку подключения **RootManageSharedAccessKey** в буфер обмена. Сохраните эту строку подключения во временном расположении папке, например в Блокноте, для последующего использования.
    
    ![](./media/event-hubs-create/create-event-hub8.png)

## <a name="create-an-event-hub"></a>Создание концентратора событий

1. В списке пространств имен Центров событий щелкните созданное пространство имен.      
   
    ![](./media/event-hubs-create/create-event-hub2.png) 

2. В колонке пространства имен щелкните **Центры событий**.
   
    ![](./media/event-hubs-create/create-event-hub3.png)

3. Щелкните **+ Концентратор событий** в верхней части колонки.
   
    ![](./media/event-hubs-create/create-event-hub4.png)
4. Введите имя концентратора событий, а затем щелкните **Создать**. 

Теперь концентратор событий создан, и у вас есть строки подключения, необходимые для отправки и приема событий.

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о Центрах событий см. в следующих статьях:

* [Общие сведения о Центрах событий](event-hubs-what-is-event-hubs.md)
* [Общие сведения об API Центров событий](event-hubs-api-overview.md)

[Azure portal]: https://portal.azure.com/