---
title: Активация ролей ресурсов Azure в PIM | Документация Майкрософт
description: Узнайте, как активировать роли ресурсов в Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: pim
ms.date: 08/31/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 59bce2c61db5838bb21a29757d4e354311ecffd5
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/04/2018
ms.locfileid: "43666253"
---
# <a name="activate-my-azure-resource-roles-in-pim"></a>Активация ролей ресурсов Azure в PIM

С помощью Azure AD Privileged Identity Management (PIM) подходящие участники роли для ресурсов Azure могут запланировать активацию на дату и время в будущем. Кроме того, они могут выбирать конкретную продолжительность активации в пределах максимума (заданного администраторами).

Эта статья предназначена для участников, которым нужно активировать свои роли ресурсов Azure в PIM.

## <a name="activate-a-role"></a>Активация роли

Когда вам потребуется какая-либо роль ресурса Azure, вы можете запросить ее активацию с помощью параметра **Мои роли** в области навигации в PIM.

1. Войдите на [портале Azure](https://portal.azure.com/).

1. Откройте страницу **Azure AD Privileged Identity Management**. Сведения о том, как добавить плитку PIM на панель мониторинга, см. в статье [Начало работы с PIM](pim-getting-started.md).

1. Щелкните **Мои роли**, чтобы просмотреть список доступных ролей каталога Azure AD и ролей ресурсов Azure.

    ![Роли каталога Azure AD и роли ресурсов Azure в области "Мои роли"](./media/pim-resource-roles-activate-your-roles/resources-my-roles.png)

1. В списке **Роли службы ресурсов Azure** найдите роль, которую необходимо активировать.

    ![Роли ресурсов Azure — список "Мои роли"](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate.png)

1. Щелкните **Активировать**, чтобы открыть область активации.

1. Если ваша роль требует многофакторной проверки подлинности (MFA), щелкните **Чтобы продолжить, подтвердите свою личность**. Проверка подлинности выполняется один раз за сеанс.

    ![Проверка с помощью функции MFA перед активацией роли](./media/pim-resource-roles-activate-your-roles/resources-my-roles-mfa.png)

1. Щелкните **Подтверждение вашей личности** и следуйте инструкциям для настройки дополнительной проверки безопасности.

    ![Дополнительная проверка безопасности](./media/pim-resource-roles-activate-your-roles/resources-mfa-enter-code.png)

1. Если вы хотите указать меньшую область, щелкните **Область**, чтобы открыть область "Фильтр ресурсов".

    Рекомендуется запрашивать доступ только к тем ресурсам, которые вам нужны. В области "Фильтр ресурсов" можно указать группы ресурсов или ресурсы, доступ к которым вам нужен.

    ![Активация. Фильтр ресурсов](./media/pim-resource-roles-activate-your-roles/resources-my-roles-resource-filter.png)

1. При необходимости укажите время начала настраиваемой активации. Участник будет активирован по прошествии установленного времени.

1. В поле **Причина** введите основание для запроса активации.

    ![Область завершения активации](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-done.png)

1. Щелкните **Активировать**.

    Если роль не требует утверждения, она будет сразу же активирована и появится в списке активных ролей. Если [роль требует утверждения](pim-resource-roles-approval-workflow.md) для активации, в правом верхнем углу браузера появится уведомление о том, что запрос ожидает утверждения.

    ![Уведомление о запросе, ожидающем утверждения](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-notification.png)

## <a name="view-the-status-of-your-requests"></a>Просмотр состояния запросов

Вы можете просмотреть состояние запросов, ожидающих активации.

1. Откройте страницу Azure AD Privileged Identity Management.

1. Щелкните **Мои запросы**, чтобы просмотреть список запросов ролей каталога Azure AD и ролей ресурсов Azure.

    ![Роли каталога Azure AD и роли ресурсов Azure в области "Мои запросы"](./media/pim-resource-roles-activate-your-roles/resources-my-requests.png)

1. Прокрутите вправо, чтобы увидеть столбец **Состояние запроса**.

## <a name="use-a-role-immediately-after-activation"></a>Использование роли сразу после активации

Из-за кэширования активация не происходит на портале Azure сразу, если его не обновить. Если требуется снизить вероятность задержек после активации роли, можно использовать страницу **Доступ к приложениям** на портале. Приложения, доступные на этой странице, немедленно проверяют новые назначения ролей.

1. Откройте страницу Azure AD Privileged Identity Management.

1. Перейдите на страницу **​​Доступ к приложениям**.

    ![Снимок экрана страницы доступа к приложениям PIM](./media/pim-resource-roles-activate-your-roles/pim-application-access.png)

1. Щелкните вкладку **Ресурсы Azure**, чтобы открыть на портале страницу **Все ресурсы**.

    После перехода по этой ссылке страница принудительно обновится и будет выполнена проверка на наличие новых назначений ролей ресурсов Azure.

## <a name="cancel-a-pending-request"></a>Отмена ожидающего запроса

Если вы не активируете роль, требующую утверждения, вы можете в любой момент отменить запрос, ожидающий утверждения.

1. Откройте страницу Azure AD Privileged Identity Management.

1. Щелкните **Мои запросы**.

1. Щелкните ссылку **Отмена** для роли, которую хотите отменить.

    После нажатия кнопки "Отмена" запрос будет отменен. Чтобы активировать роль снова, необходимо будет отправить новый запрос на активацию.

   ![Отмена ожидающего запроса](./media/pim-resource-roles-activate-your-roles/resources-my-requests-cancel.png)

## <a name="next-steps"></a>Дополнительная информация

- [Расширение или возобновление ролей ресурсов Azure в PIM](pim-resource-roles-renew-extend.md)
- [Активация ролей каталога Azure AD в PIM](pim-how-to-activate-role.md)