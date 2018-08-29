---
title: Регистрация личного устройства в сети организации — Azure Active Directory | Документация Майкрософт
description: Из этой статьи можно узнать, как зарегистрировать персональное устройство в сети организации и как с его помощью получить доступ к ее защищенным ресурсам.
services: active-directory
author: eross-msft
manager: mtillman
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.component: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 08/03/2018
ms.author: lizross
ms.reviewer: jairoc
ms.openlocfilehash: 7126a47bd90168c7d86fe9fcc05fab0a60955063
ms.sourcegitcommit: 1aedb52f221fb2a6e7ad0b0930b4c74db354a569
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/17/2018
ms.locfileid: "40180690"
---
# <a name="register-your-personal-device-on-your-organizations-network"></a>Регистрация персонального устройства в сети организации
Регистрация персонального устройства (в роли которого может выступать телефон или планшет) в сети организации. После регистрации устройства с него можно получать доступ к ограниченным ресурсам организации.

>[!Note]
>В целях демонстрации в этой статье используется устройство Windows. Но это не мешает регистрировать устройства под управлением iOS, Android и macOS.

## <a name="what-happens-when-you-register-your-device"></a>Действия, которые происходят при регистрации устройства
Во время регистрации устройства в сети организации будут выполнены следующие действия.

- ОС Windows зарегистрирует устройство в сети организации.

- В зависимости от выбранных организацией параметров может появиться запрос на настройку двухэтапной проверки подлинности с применением [Многофакторной идентификации](multi-factor-authentication-end-user-first-time.md) или метода на основе [сведений для защиты](user-help-security-info-overview.md).

- В зависимости от выбранных организацией параметров вы можете быть автоматически зарегистрированы в службе управления мобильными устройствами, например в Microsoft Intune. Дополнительные сведения о регистрации в Microsoft Intune см. в статье [Регистрация устройства в Intune](https://docs.microsoft.com/intune-user-help/enroll-your-device-in-intune-all).

- Чтобы завершить процесс входа, понадобятся имя и пароль к личной учетной записи Майкрософт.

## <a name="to-register-your-windows-device"></a>Регистрация устройства под управлением ОС Windows

Чтобы зарегистрировать персональное устройство в сети, выполните следующие действия.

1. Откройте **Параметры** и выберите **Учетные записи**.

    ![Пункт "Учетные записи" в окне параметров](./media/user-help-register-device-on-network/register-device-settings-accounts.png)

2. Последовательно выберите **Email & accounts** (Электронная почта и учетные записи) и **Join a Microsoft account** (Присоединиться к учетной записи Майкрософт).

    ![Электронная почта и учетные записи и Присоединиться к учетной записи Майкрософт](./media/user-help-register-device-on-network/register-device-email-and-accounts.png)

3. В окне **Добавление учетной записи Майкрософт** введите адрес электронной почты личной учетной записи Майкрософт.

    ![Окно добавления адреса электронной почты в учетную запись Майкрософт](./media/user-help-register-device-on-network/register-device-add-accounts.png)

4. В окне **Ввод пароля** введите пароль личной учетной записи Майкрософт и нажмите кнопку **Вход**.

    ![Окно ввода пароля](./media/user-help-register-device-on-network/register-device-enter-password.png)

5. Завершите оставшуюся часть процесса регистрации, включая утверждение запроса подтверждения личности (если была использована двухфакторная проверка подлинности) и настройку Windows Hello (при необходимости).

## <a name="to-make-sure-youre-registered"></a>Проверка состояния регистрации
Если взглянуть на параметры, то можно убедиться, что регистрация прошла успешно.

1. Откройте **Параметры** и выберите **Учетные записи**.

    ![Пункт "Учетные записи" в окне параметров](./media/user-help-register-device-on-network/register-device-settings-accounts.png)

2. Выберите **Email & accounts** (Электронная почта и учетные записи) и убедитесь, что личная учетная запись Майкрософт отображается.

    ![Окно доступа к учетной записи места работы или учебного заведения с подключенной учетной записью Contoso](./media/user-help-register-device-on-network/register-device-verify-account.png)

## <a name="next-steps"></a>Дополнительная информация
После регистрации персонального устройства в сети организации доступ к большинству ресурсов будет открыт.

- Если организации требуется, чтобы вы подключили рабочее устройство, см. статью [Join your work device to your organization's network](user-help-join-device-on-network.md) (Подключение рабочего устройства к сети организации).



