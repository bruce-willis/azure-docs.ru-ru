---
title: Создание учетной записи NetApp для доступа к Azure NetApp Files | Документация Майкрософт
description: Описание способа получения доступа к Azure NetApp Files и создания учетной записи NetApp, чтобы иметь возможность настраивать пул емкости и создавать том.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/28/2018
ms.author: b-juche
ms.openlocfilehash: ad8cc550ce69e4dc4c19a569718fa873a65b3620
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/13/2018
ms.locfileid: "39010350"
---
# <a name="create-a-netapp-account"></a>Создание учетной записи NetApp
Создание учетной записи NetApp предоставляет возможность настройки пула емкости и впоследствии создания тома. Чтобы создать новую учетную запись NetApp, нужно использовать колонку Azure NetApp Files.

## <a name="before-you-begin"></a>Перед началом работы
Необходимо быть добавленным в список разрешений для доступа к поставщику ресурсов Microsoft.NetApp Azure и настроенным для использования службы Azure NetApp Files.  

[Azure NetApp Files Public Preview](https://aka.ms/nfspublicpreview) (Подписка на общедоступную предварительную версию Azure NetApp Files). 

## <a name="steps"></a>Действия 

1. Найдите URL-адрес предварительной версии портала Azure из приглашения ко входу в предварительную версию и выполните вход на портал. 
2.  Подключитесь к колонке Azure NetApp Files, используя один из следующих способов.  
  * Искать **Azure NetApp Files** в поле поиска на портале Azure.  
  * Нажать кнопку **Все службы** в навигации, а затем отфильтровать для Azure NetApp Files.  

  Колонку Azure NetApp Files можно добавить в избранное, щелкнув значок звездочки рядом с ней. 

3. Нажмите кнопку **+ Добавить** для создания новой учетной записи NetApp.  
  Появится окно новой учетной записи NetApp.  

4. Укажите следующие сведения для учетной записи NetApp. 
  * **Имя учетной записи**  
    Укажите уникальное имя для подписки.
  *  **Подписка**  
    Выберите подписку из существующих подписок.
  * **Группа ресурсов**   
    Используйте имеющуюся группу ресурсов или создайте новую.
  * **Местоположение.**  
    Выберите регион, где нужно разместить учетную запись и ее дочерние ресурсы.  
    В настоящее время служба Azure NetApp Files поддерживается только в восточной части США.  

    ![Новая учетная запись NetApp](../media/azure-netapp-files/azure-netapp-files-new-netapp-account.png)


5. Нажмите кнопку **Создать**.     
  Созданная учетная запись NetApp теперь отображается в колонке Azure NetApp Files. 

## <a name="next-steps"></a>Дополнительная информация  

1. [Настройка пула емкости](azure-netapp-files-set-up-capacity-pool.md)
2. [Создание тома для Azure NetApp Files](azure-netapp-files-create-volumes.md)
3. [Настройка политики экспорта для тома (необязательно)](azure-netapp-files-configure-export-policy.md)
