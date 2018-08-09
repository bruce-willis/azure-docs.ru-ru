---
title: Настройка именованных расположений в Azure Active Directory | Документация Майкрософт
description: Узнайте, как настроить именованные расположения.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.component: protection
ms.assetid: f56e042a-78d5-4ea3-be33-94004f2a0fc3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 62a55672a4326df585fc84699dfd72424be362dc
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/08/2018
ms.locfileid: "39627497"
---
# <a name="configure-named-locations-in-azure-active-directory"></a>Настройка именованных расположений в Azure Active Directory

С помощью именованных расположений можно пометить доверенные диапазоны IP-адресов в вашей организации. Именованные расположения в Azure Active Directory используются в следующих контекстах:

- обнаружение [событий риска](reports-monitoring/concept-risk-events.md) для уменьшения количества ложных положительные результатов;  

- [условный доступ на основе расположения](conditional-access/location-condition.md).


В этой статье объясняется, как настроить именованные расположения в конкретной среде.


## <a name="entry-points"></a>Точки входа

Чтобы открыть страницу настройки именованных расположений в разделе **Безопасность** на странице Azure Active Directory, щелкните следующие элементы:

![Точки входа](./media/active-directory-named-locations/34.png)

- **Условный доступ:**

    - В разделе **Управление** щелкните **Именованные расположения**.
    
        ![Команда "Именованные расположения"](./media/active-directory-named-locations/06.png)

- **Рискованные входы в систему:**

    - На панели инструментов в верхней части страницы щелкните **Добавить известные диапазоны IP-адресов**.

       ![Команда "Именованные расположения"](./media/active-directory-named-locations/35.png)



## <a name="configuration-example"></a>Пример конфигурации

**Чтобы настроить именованное расположение:**

1. Войдите на [портал Azure](https://portal.azure.com) как глобальный администратор.

2. В левой панели щелкните **Azure Active Directory**.

    ![Ссылка на Azure Active Directory на левой панели](./media/active-directory-named-locations/01.png)

3. На странице **Azure Active Directory** в разделе **Безопасность** щелкните **Условный доступ**.

    ![Команда "Условный доступ"](./media/active-directory-named-locations/05.png)


4. На странице **Условный доступ** в разделе **Управление** щелкните **Именованные расположения**.

    ![Команда "Именованные расположения"](./media/active-directory-named-locations/06.png)


5. На странице **Именованные расположения** щелкните **Новое расположение**.

    ![Команда "Создать расположение"](./media/active-directory-named-locations/07.png)


6. На странице **Создать** сделайте следующее:

    ![Колонка "Создать"](./media/active-directory-named-locations/61.png)

    a. В поле **Имя** введите имя именованного расположения.

    b. В поле **Диапазоны IP-адресов** введите диапазон IP-адресов. Диапазон IP-адресов должен иметь формат *бесклассовой междоменной маршрутизации* (CIDR).  

    c. Нажмите кнопку **Создать**.



## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения можно найти в разделе 

- [Условный доступ в Azure Active Directory](active-directory-conditional-access-azure-portal.md)

- [Условия расположения в условном доступе Azure Active Directory](conditional-access/location-condition.md)

- [События риска Azure Active Directory](reports-monitoring/concept-risk-events.md)

- [Отчет о событиях входа, представляющих риск, на портале Azure Active Directory](reports-monitoring/concept-risky-sign-ins.md)  
