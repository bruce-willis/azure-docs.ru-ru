---
title: Получение оповещений журнала действий для уведомлений службы Azure
description: Получайте уведомления по SMS, электронной почте или от веб-перехватчика при использовании службы Azure.
author: shawntabrizi
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/09/2018
ms.author: shtabriz
ms.component: alerts
ms.openlocfilehash: 221434a391f963a764ef36b9533cc8cfd0e16c01
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/28/2018
ms.locfileid: "43123454"
---
# <a name="create-activity-log-alerts-on-service-notifications"></a>Создание оповещений журнала действий для уведомлений службы
## <a name="overview"></a>Обзор
В этой статье показано, как настроить оповещения журнала действий для уведомлений о работоспособности службы с помощью портала Azure.  

Оповещения можно получать, когда Azure отправляет уведомления о работоспособности службы в вашу подписку Azure. Оповещения можно настроить на основе следующих данных.

- Класс уведомления о работоспособности службы (проблемы со службой, запланированное обслуживание, рекомендации по работоспособности).
- Затронутая подписка.
- Затронутые службы.
- Затронутые регионы.

> [!NOTE]
> Уведомления о работоспособности службы не содержат предупреждения о событиях работоспособности ресурсов.

Кроме того, можно настроить, кому должны отправляться оповещения:

- Выберите имеющуюся группу действий.
- Создайте группу действий (которую можно будет использовать для будущих оповещений).

Дополнительные сведения о группах действий см. в разделе [Создание групп действий и управление ими на портале Azure](monitoring-action-groups.md).

Дополнительные сведения о том, как настроить уведомления о работоспособности службы с помощью шаблонов Resource Manager Azure, см. в статье [Создание оповещения журнала действий с помощью шаблона Resource Manager](monitoring-create-activity-log-alerts-with-resource-manager-template.md).

## <a name="create-an-alert-on-a-service-health-notification-for-a-new-action-group-by-using-the-azure-portal"></a>Создание оповещения для уведомления о работоспособности службы для новой группы действий с помощью портала Azure
1. На [портале](https://portal.azure.com)выберите **Работоспособность службы**.

    ![Служба "Работоспособность службы"](./media/monitoring-activity-log-alerts-on-service-notifications/home-servicehealth.png)

1. В разделе **Оповещения** выберите **Оповещения о работоспособности**.

    ![Вкладка "Оповещения о работоспособности"](./media/monitoring-activity-log-alerts-on-service-notifications/alerts-blades-sh.png)

1. Выберите **Create service health alert** (Создать оповещение о работоспособности службы) и заполните поля.

    ![Команда Create service health alert (Создать оповещение о работоспособности службы)](./media/monitoring-activity-log-alerts-on-service-notifications/service-health-alert.png)

1. Выберите **Подписки**, **Службы** и **Области**, чтобы получать оповещения.

    ![Диалоговое окно Add activity log alert (Добавить оповещение журнала действий)](./media/monitoring-activity-log-alerts-on-service-notifications/activity-log-alert-new-ux.png)

> [!NOTE]
> Эта подписка используется для сохранения оповещения журнала действий. В эту подписку развертывается ресурс оповещения, а также с ее помощью отслеживаются связанные с ним события в журнале действий.

1. Выберите **Типы событий** для получения оповещений: *Неполадка службы*, *Плановое обслуживание* и *Рекомендации по работоспособности* 

1. Определите подробности своих оповещений, введя **Имя правила оповещения** и его **Описание**.

1. Выберите **группу ресурсов** для хранения оповещений.

1. Чтобы создать новую группу действий, нажмите **Новая группа действий**. Введите имя в поля **Имя группы действий** и **Короткое имя**. Короткое имя используется в уведомлениях, которые отправляются при выводе оповещения.

    ![Создание новой группы действий](./media/monitoring-activity-log-alerts-on-service-notifications/action-group-creation.png)

1. Определите список получателей, указав данные получателя:

    a. **Имя.** Введите имя, псевдоним или идентификатор получателя.

    b. **Тип действия.** Вы можете выбрать SMS, электронную почту, веб-перехватчик, приложение Azure и другие типы.

    c. **Подробные сведения.** Сведения зависят от типа действия. Можно указать номер телефона, адрес электронной почты, универсальный код ресурса (URI) веб-перехватчика и др.

1. Чтобы создать группу действий, выберите **ОК**, а чтобы завершить оповещение — **Создать правило оповещения**.

В течение нескольких минут оповещение активируется на основе указанных во время создания условий.

Узнайте, [как настроить уведомления веб-перехватчика для существующих систем управления проблемами](../service-health/service-health-alert-webhook-guide.md). Сведения о схеме веб-перехватчика для оповещений журнала действий см. в статье [Объекты webhook для оповещений журнала действий Azure](monitoring-activity-log-alerts-webhook.md).

>[!NOTE]
>Группу действий, определенную при выполнении этих шагов, можно использовать повторно в качестве имеющейся группы действий для всех будущих определений оповещений.
>
>

## <a name="create-an-alert-on-a-service-health-notification-for-an-existing-action-group-by-using-the-azure-portal"></a>Создание оповещения для уведомления о работоспособности службы для имеющейся группы действий на портале Azure

1. Выполните шаги 1–7 из предыдущего раздела, чтобы создать уведомление о работоспособности службы. 

1. В разделе **Определение группы действий** нажмите кнопку **Выбрать группу действий** . Выберите необходимую группу действий.

1. Чтобы добавить группу действий, выберите **Добавить**, а чтобы завершить оповещение — **Создать правило генерации оповещений**.

В течение нескольких минут оповещение активируется на основе указанных во время создания условий.

## <a name="create-an-alert-on-a-service-health-notification-for-a-new-action-group-by-using-the-azure-resource-manager-templates"></a>Создание оповещения для уведомления о работоспособности службы для новой группы действий с помощью шаблонов Azure Resource Manager

Ниже приведен пример кода, который создает группу действий с целевым объектом электронной почты и активирует все уведомления о работоспособности службы в целевой подписке.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "actionGroups_name": {
            "defaultValue": "SubHealth",
            "type": "String"
        },
        "activityLogAlerts_name": {
            "defaultValue": "ServiceHealthActivityLogAlert",
            "type": "String"
        },
        "emailAddress":{
            "type":"string"
        }
    },
    "variables": {
        "alertScope":"[concat('/','subscriptions','/',subscription().subscriptionId)]"
    },
    "resources": [
        {
            "comments": "Action Group",
            "type": "microsoft.insights/actionGroups",
            "name": "[parameters('actionGroups_name')]",
            "apiVersion": "2017-04-01",
            "location": "Global",
            "tags": {},
            "scale": null,
            "properties": {
                "groupShortName": "[parameters('actionGroups_name')]",
                "enabled": true,
                "emailReceivers": [
                    {
                        "name": "[parameters('actionGroups_name')]",
                        "emailAddress": "[parameters('emailAddress')]"
                    }
                ],
                "smsReceivers": [],
                "webhookReceivers": []
            },
            "dependsOn": []
        },
        {
            "comments": "Service Health Activity Log Alert",
            "type": "microsoft.insights/activityLogAlerts",
            "name": "[parameters('activityLogAlerts_name')]",
            "apiVersion": "2017-04-01",
            "location": "Global",
            "tags": {},
            "scale": null,
            "properties": {
                "scopes": [
                    "[variables('alertScope')]"
                ],
                "condition": {
                    "allOf": [
                        {
                            "field": "category",
                            "equals": "ServiceHealth"
                        },
                        {
                            "field": "properties.incidentType",
                            "equals": "Incident"
                        }
                    ]
                },
                "actions": {
                    "actionGroups": [
                        {
                            "actionGroupId": "[resourceId('microsoft.insights/actionGroups', parameters('actionGroups_name'))]",
                            "webhookProperties": {}
                        }
                    ]
                },
                "enabled": true,
                "description": ""
            },
            "dependsOn": [
                "[resourceId('microsoft.insights/actionGroups', parameters('actionGroups_name'))]"
            ]
        }
    ]
}
```

## <a name="manage-your-alerts"></a>Управление оповещениями

Созданное оповещение отображается в разделе **Оповещения** в окне **Монитор**. Выберите оповещение, которым нужно управлять, чтобы получить возможность:

* Изменить его.
* Удалить его.
* Отключить и включить его, если нужно временно остановить или возобновить получение уведомлений для этого оповещения.

## <a name="next-steps"></a>Дополнительная информация
- Узнайте, [как настроить уведомления веб-перехватчика для существующих систем управления проблемами](../service-health/service-health-alert-webhook-guide.md).
- Дополнительные сведения об уведомлениях о работоспособности службы см. в [этой статье](monitoring-service-notifications.md).
- Дополнительные сведения об ограничении частоты отправки уведомлений см. в статье [Ограничение частоты отправки для SMS, сообщений электронной почты и вызовов Webhook](monitoring-alerts-rate-limiting.md).
- Просмотрите схему веб-перехватчика оповещений журнала действий в статье [Объекты webhook для оповещений журнала действий Azure](monitoring-activity-log-alerts-webhook.md).
- Изучите [обзор оповещений журнала действий](monitoring-overview-alerts.md) и узнайте, как получать оповещения. 
- Дополнительные сведения о группах действий см. в статье [Создание групп действий и управление ими на портале Azure](monitoring-action-groups.md).
