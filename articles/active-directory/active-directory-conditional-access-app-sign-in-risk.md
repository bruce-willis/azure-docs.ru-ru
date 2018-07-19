---
title: Краткое руководство. Блокировка доступа в случае обнаружения угроз сеанса при помощи условного доступа Azure Active Directory | Документация Майкрософт
description: В этом руководстве описано, как настроить политику условного доступа Azure Active Directory (Azure AD) для блокировки входов в систему, связанных с рисками сеанса.
services: active-directory
keywords: условный доступ к приложениям, условный доступ посредством Azure Active Directory, безопасный доступ к ресурсам организации, политики условного доступа
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: protection
ms.topic: article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/03/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: 98e2f88c85d21cde2ecc7196f93d531a80b14d13
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/03/2018
ms.locfileid: "37449399"
---
# <a name="quickstart-block-access-when-a-session-risk-is-detected-with-azure-active-directory-conditional-access"></a>Краткое руководство. Блокировка доступа в случае обнаружения угроз сеанса при помощи условного доступа Azure Active Directory  

Чтобы обеспечить защиту окружения, может потребоваться блокировка подозрительных пользователей от входа в систему. [Azure Active Directory (Azure AD) Identity Protection](active-directory-identityprotection.md) анализирует каждый вход в систему и вычисляет вероятность того, что попытка входа не была выполнена законным владельцем учетной записи пользователя. Вероятность (низкая, средняя, высокая) указывается в виде вычисляемого значения, называемого [уровни риска при входе](active-directory-conditional-access-conditions.md#sign-in-risk). Задавая условие риска при входе, можно настроить политику условного доступа так, что она будет реагировать на определенные уровни риска при входе в систему. 

В этом руководстве показано, как настроить [политику условного доступа](active-directory-conditional-access-azure-portal.md), чтобы блокировать вход в систему при обнаружении настроенного уровня риска при входе в систему. 

![Создание политики](./media/active-directory-conditional-access-app-sign-in-risk/1000.png)


Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.



## <a name="prerequisites"></a>предварительным требованиям 

Для выполнения сценария в этом руководстве вам понадобится следующее.

- **Доступ к Azure AD Premium P2**. Хотя в Azure AD Premium P1 доступна функция условного доступа, все же вам необходим выпуск P2, так как для сценария, описанного в этом руководстве, требуется защита идентификации. 

- **Защита идентификации**. Сценарий в этом руководстве требует включенной функции защиты идентификации. Если вы не знаете, как включить защиту идентификации Azure, см. раздел [Включение защиты идентификации Azure Active Directory](active-directory-identityprotection-enable.md).

- **Обозреватель Tor**. [Обозреватель Tor](https://www.torproject.org/projects/torbrowser.html.en) предназначен для сохранения конфиденциальности в Интернете. Служба защиты идентификации воспринимает вход в систему с помощью обозревателя Tor как **входы с анонимных IP-адресов**, которые имеют средний уровень риска. Дополнительные сведения см. в статье [События риска Azure Active Directory](active-directory-reporting-risk-events.md).  

- **Тестовая учетная запись Alain Charon**. Сведения о создании тестовой учетной записи см. в разделе о [Add new users to Azure Active Directory](fundamentals/add-users-azure-active-directory.md#add-cloud-based-users) (Добавление новых пользователей в Azure Active Directory).


## <a name="test-your-sign-in"></a>Проверка входа 

Цель этого шага — убедиться, что тестовая учетная запись имеет доступ к клиенту с использованием обозревателя Tor.

**Чтобы проверить вход в систему, выполните следующие действия.**

1. Выполните вход в [портал Azure](https://portal.azure.com) как **Alain Charon**.

2. Выполните выход. 


## <a name="create-your-conditional-access-policy"></a>Создание политики условного доступа 

В этом сценарии используется вход в систему с помощью обозревателя Tor для создания обнаруженных событий риска — **входы с анонимных IP-адресов**. Уровень риска этого события средний.   

В этом разделе показано, как создать необходимую политику условного доступа. В политике задайте следующие значения.

|Параметр |Значение|
|---     | --- |
| Пользователи и группы | Alain Charon  |
| Облачные приложения | Все облачные приложения |
| Предоставление доступа | Заблокировать доступ |
 

![Создание политики](./media/active-directory-conditional-access-app-sign-in-risk/115.png)

 


**Чтобы настроить политику условного доступа, выполните следующие действия.**

1. Войдите на [портал Azure](https://portal.azure.com) с правами глобального администратора, администратора безопасности или администратора условного доступа.

2. На портале Azure на панели навигации слева щелкните **Azure Active Directory**. 

    ![Azure Active Directory](./media/active-directory-conditional-access-app-sign-in-risk/02.png)

3. На странице **Azure Active Directory** в разделе **Управление** щелкните **Условный доступ**.

    ![Условный доступ](./media/active-directory-conditional-access-app-sign-in-risk/03.png)
 
4. На странице **Условный доступ** на панели инструментов сверху нажмите кнопку **Добавить**.

    ![ИМЯ](./media/active-directory-conditional-access-app-sign-in-risk/108.png)

5. На странице **Создать** в поле **Имя** введите **Блокировать доступ для среднего уровня риска**.

    ![ИМЯ](./media/active-directory-conditional-access-app-sign-in-risk/104.png)

6. В разделе **Назначение** щелкните **Пользователи и группы**.

    ![Пользователи и группы](./media/active-directory-conditional-access-app-sign-in-risk/06.png)

7. На странице **Пользователи и группы** выполните следующие действия.

    ![Условный доступ](./media/active-directory-conditional-access-app-sign-in-risk/107.png)

    a. Щелкните **Выбор пользователей и групп**, а затем выберите **Пользователи и группы**.

    Б. Нажмите кнопку **Выбрать**.

    c. На странице **Выбор** выберите **Alain Charon** и нажмите кнопку **Выбрать**.

    d. На странице **Пользователи и группы** нажмите кнопку **Готово**.

8. Щелкните **Облачные приложения**.

    ![Облачные приложения](./media/active-directory-conditional-access-app-sign-in-risk/08.png)

9. На странице **Облачные приложения** выполните следующие действия.

    ![Условный доступ](./media/active-directory-conditional-access-app-sign-in-risk/109.png)

    a. Нажмите **Все облачные приложения**.

    Б. Нажмите кнопку **Done**(Готово).

10. В разделе **Элементы управления доступом** щелкните **Предоставить**.

    ![Элементы управления доступом](./media/active-directory-conditional-access-app-sign-in-risk/10.png)

11. На странице **Предоставление** выполните следующие действия.

    ![Условный доступ](./media/active-directory-conditional-access-app-sign-in-risk/105.png)

    a. Выберите **Заблокировать доступ**.

    Б. Нажмите кнопку **Выбрать**.

12. В разделе **Включение политики** щелкните **Вкл**.

    ![Включение политики](./media/active-directory-conditional-access-app-sign-in-risk/18.png)

13. Нажмите кнопку **Создать**.


## <a name="evaluate-a-simulated-sign-in"></a>Оценка смоделированного входа

Теперь, когда вы настроили политику условного доступа, вероятно, вы захотите узнать, работает ли она так, как ожидалось. В качестве первого шага используйте **средство политики "Что — если"** условного доступа для имитации входа в систему тестового пользователя. При имитации оценивается влияние входа на политики и создается отчет об имитации.  

При запуске **средства политики "Что — если"** для этого сценария в список **Применяемые политики** должен быть внесен пункт **Блокировать доступ для среднего уровня риска**. 

![Пользователь](./media/active-directory-conditional-access-app-sign-in-risk/117.png)


**Чтобы оценить политику условного доступа, выполните следующие действия.**

1. На странице [Условный доступ — политики](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) в меню в верхней части нажмите кнопку **What If**.  
 
    ![What If](./media/active-directory-conditional-access-app-sign-in-risk/14.png)

2. Нажмите кнопку **Пользователь** на странице **Пользователи**, выберите **Alan Charon** и нажмите **Выбрать**.

    ![Пользователь](./media/active-directory-conditional-access-app-sign-in-risk/116.png)

3. В поле **риск при входе** выберите **Средний**.

    ![Пользователь](./media/active-directory-conditional-access-app-sign-in-risk/119.png)


3. Щелкните **What If**.


## <a name="test-your-conditional-access-policy"></a>Проверка политики условного доступа

В предыдущем разделе вы узнали, как оценить смоделированный вход. Помимо имитации необходимо проверить политику условного доступа, чтобы убедиться, что она работает должным образом. 

Чтобы протестировать политику, попробуйте войти в [портал Azure](https://portal.azure.com) как **Alan Charon** при помощи обозревателя Tor. Попытка входа должна быть заблокирована политикой условного доступа.

![Многофакторная Идентификация](./media/active-directory-conditional-access-app-sign-in-risk/118.png)


## <a name="clean-up-resources"></a>Очистка ресурсов

Если тестовый пользователь, обозреватель Tor и политика условного доступа больше не нужны, удалите их.

- Сведения об удалении пользователя Azure AD см. в разделе об [удалении пользователей из Azure AD](fundamentals/add-users-azure-active-directory.md#delete-users-from-azure-ad).

- Чтобы удалить политику, выберите ее и нажмите кнопку **Удалить** на панели инструментов быстрого доступа.

    ![Многофакторная Идентификация](./media/active-directory-conditional-access-app-sign-in-risk/33.png)

- Инструкции по удалению обозревателя Tor см. в разделе [Uninstalling](https://tb-manual.torproject.org/en-US/uninstalling.html) (Удаление).

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Обеспечение принятия условий использования](./active-directory-conditional-access-tou.md)
> [Require MFA for specific apps with Azure Active Directory conditional access](./active-directory-conditional-access-app-based-mfa.md) (Требование MFA для определенных приложений с использованием условного доступа Azure Active Directory)

