---
title: Включение защищенного протокола LDAP (LDAPS) в доменных службах Azure AD | Документация Майкрософт
description: Включение защищенного протокола LDAP (LDAPS) для управляемого домена доменных служб Azure AD
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: c6da94b6-4328-4230-801a-4b646055d4d7
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/01/2018
ms.author: maheshu
ms.openlocfilehash: befab32a9daf5a22a326396c84223e07d401f72b
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/03/2018
ms.locfileid: "39502800"
---
# <a name="enable-secure-ldap-ldaps-for-an-azure-ad-domain-services-managed-domain"></a>Включение защищенного протокола LDAP (LDAPS) для управляемого домена доменных служб Azure AD

## <a name="before-you-begin"></a>Перед началом работы
Выполните инструкции из раздела [Задача 2. Экспорт сертификата защищенного протокола LDAP в PFX-файл](active-directory-ds-admin-guide-configure-secure-ldap-export-pfx.md).


## <a name="task-3-enable-secure-ldap-for-the-managed-domain-using-the-azure-portal"></a>Задача 3. Включение защищенного протокола LDAP для управляемого домена с помощью портала Azure
Выполните следующие действия по настройке, чтобы включить защищенный протокол LDAP.

1. Перейдите на **[портал Azure](https://portal.azure.com)**.

2. В поле **Поиск ресурсов** введите "доменные службы". В списке результатов выберите **Доменные службы Azure AD**. На странице **Доменные службы Azure AD** будет указан ваш управляемый домен.

    ![Поиск подготавливаемого управляемого домена](./media/getting-started/domain-services-provisioning-state-find-resource.png)

2. Щелкните имя управляемого домена (например, "contoso100.com") для получения дополнительных сведений о домене.

    ![Доменные службы — состояние подготовки](./media/getting-started/domain-services-provisioning-state.png)

3. Щелкните **Защищенный протокол LDAP** на панели навигации.

    ![Доменные службы — страница "Защищенный протокол LDAP"](./media/active-directory-domain-services-admin-guide/secure-ldap-blade.png)

4. По умолчанию защищенный доступ LDAP к управляемому домену отключен. Измените значение параметра **Защищенный протокол LDAP** на **Включено**.

    ![Включение защищенного протокола LDAP](./media/active-directory-domain-services-admin-guide/secure-ldap-blade-configure.png)
5. По умолчанию защищенный доступ LDAP к управляемому домену через Интернет отключен. При необходимости измените значение параметра **Разрешить доступ по защищенному протоколу LDAP через Интернет** на **Включить**.

    > [!WARNING]
    > При включении доступа по защищенному протоколу LDAP через Интернет ваш домен уязвим к атакам методом подбора пароля через Интернет. Поэтому рекомендуется настроить группу безопасности сети для блокировки доступа к необходимым диапазонам исходных IP-адресов. Инструкции см. в разделе о [блокировке доступа через LDAPS к управляемому домену через Интернет](#task-5---lock-down-secure-ldap-access-to-your-managed-domain-over-the-internet).
    >

6. Щелкните значок папки рядом с полем **PFX-файл с защищенным сертификатом LDAP**. Укажите путь к PFX-файлу сертификата для доступа управляемому домену через защищенный протокол LDAP.

7. Укажите **Пароль для расшифровки PFX-файла**. Укажите тот же пароль, который использовался при экспорте сертификата в PFX-файл.

8. Когда все будет готово, нажмите кнопку **Сохранить**.

9. Вы увидите уведомление о том, что настройка защищенного протокола LDAP для управляемого домена выполняется. До завершения этой операции вы не сможете изменить другие параметры домена.

    ![Настройка защищенного протокола LDAP для управляемого домена](./media/active-directory-domain-services-admin-guide/secure-ldap-blade-configuring.png)

> [!NOTE]
> Включение защищенного LDAP для управляемого домена займет примерно 10–15 минут. Если предоставленный защищенный сертификат LDAP не соответствует необходимым требованиям, то защищенный протокол LDAP не будет включен для каталога и произойдет ошибка. Например, отобразится сообщение о том, что указано неправильное доменное имя, срок действия сертификата истек или истекает в ближайшее время. В этом случае повторите попытку, указав действительный сертификат.
>
>

## <a name="next-step"></a>Дальнейшие действия
[Задача 4. Настройка DNS для доступа к управляемому домену через Интернет](active-directory-ds-ldaps-configure-dns.md)
