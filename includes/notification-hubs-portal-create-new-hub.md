---
title: включение файла
description: включение файла
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 03/28/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 19ea9c749b58f6f81dc2087caa77573062d883b5
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39485960"
---
1. Войдите на [портале Azure](https://portal.azure.com).

1. Выберите **Создать ресурс** > **Мобильные устройства** > **Концентратор уведомлений**.
   
      ![Портал Azure: создание центра уведомлений](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-create.png)
      
1. В поле **Центр уведомлений** введите уникальное имя. Выберите **регион**, **подписку** и **группу ресурсов** (если она уже создана). 
   
      Если у вас нет пространства имен служебной шины, можно использовать имя по умолчанию. Оно создается на основе имени концентратора (если имя пространства имен доступно).
    
      Если у вас уже есть пространство имен служебной шины, в котором требуется создать концентратор, сделайте следующее:

    a. В области **Пространство имен** выберите ссылку **Выбрать существующее**. 
   
    b. Нажмите кнопку **Создать**.
   
      ![Портал Azure: настройка свойств концентратора уведомлений](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-settings.png)

1. Последовательно выберите **Уведомления** (значок колокольчика) и **Go to resource** (Перейти к ресурсу). 

      ![Портал Azure — "Уведомления" -> Go to resource (Перейти к ресурсу)](./media/notification-hubs-portal-create-new-hub/notification-go-to-resource.png)    
1. Выберите **Политики доступа** в списке. Запишите две строки подключения, которые отобразятся. Они требуются для дальнейшей обработки push-уведомлений.

      >[!IMPORTANT]
      >**НЕ** используйте в приложении DefaultFullSharedAccessSignature. Этот параметр можно использовать только в серверной части.
      >
   
      ![Портал Azure: строки подключения к концентратору уведомлений](./media/notification-hubs-portal-create-new-hub/notification-hubs-connection-strings-portal.png)

