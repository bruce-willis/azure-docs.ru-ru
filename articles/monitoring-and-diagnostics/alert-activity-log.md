---
title: Создание, просмотр и управление оповещениями журнала действий в Azure Monitor
description: Создание оповещений журнала действий с помощью портала Azure, шаблона ресурсов и PowerShell.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/15/2018
ms.author: vinagara
ms.component: alerts
ms.openlocfilehash: 526c50fa4d261a30738c3f24d537fe5e0d765f6d
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46951310"
---
# <a name="create-view-and-manage-activity-log-alerts-using-azure-monitor"></a>Создание, просмотр и управление оповещениями журнала действий с помощью Azure Monitor  

## <a name="overview"></a>Обзор
Оповещения журнала действий создаются в тех случаях, когда происходит новое событие, записываемое в журнал действий, которое соответствует определенным условиям для оповещения.

Эти оповещения предназначены для ресурсов Azure, их можно создать с помощью шаблона Azure Resource Manager. Их также можно создать, обновить или удалить на портале Azure. Обычно вы создаете оповещения журнала действия, чтобы получать уведомления при внесении определенных изменений в ресурсы в вашей подписки Azure. Как правило, это конкретные группы ресурсов или ресурсы. Например, можно получать уведомления при удалении любой виртуальной машины в **myProductionResourceGroup** (пример группы ресурсов). Или может потребоваться получать уведомления при назначении каких-либо новых ролей пользователю в вашей подписке.

> [!IMPORTANT]
> Оповещения уведомлений о работоспособности службы невозможно создавать через интерфейс для создания оповещений журнала действий. Дополнительные сведения о создании и использовании уведомлений о работоспособности службы см. в статье [Создание оповещений журнала действий для уведомлений службы](monitoring-activity-log-alerts-on-service-notifications.md).

## <a name="manage-alert-rules-for-activity-log-using-azure-portal"></a>Управление правилами генерации оповещений для журнала действий с помощью портала Azure

> [!NOTE]

>  При создании правил генерации оповещений проверьте следующее.

> - Подписка в области ничем не отличается от подписки, в которой создается оповещение.
- Условия должны соответствовать уровню, состоянию, вызывающему, группе ресурсов, идентификатору ресурса, типу ресурса или категории события, на основе которых настраивается оповещение.
- Условие anyOf и вложенные условия отсутствуют в JSON-файле конфигурации оповещений (обычно разрешается только одно условие allOf без allOf и anyOf).
- Для категории "административная". В оповещении необходимо указать хотя бы одно из описанных выше условий. Вы не можете создать оповещение, которое активируется каждый раз при создании события в журнале действий.

### <a name="create-an-alert-rule-for-an-activity-log-using-azure-portal"></a>Создание правила генерации оповещений журнала действий с помощью портала Azure

Выполните перечисленные ниже действия.

1. На портале Azure выберите **Монитор** > **Оповещения**.
2. Выберите **Новое правило генерации оповещений** в верхней части окна **Оповещения**.

     ![Новое правило генерации оповещений](./media/monitor-alerts-unified/AlertsPreviewOption.png)

     Откроется окно **создания правила**.

      ![Параметры нового правила генерации оповещений](./media/monitoring-activity-log-alerts-new-experience/create-new-alert-rule-options.png)

3. В разделе **Define Alert condition** (Определите условия оповещения) введите указанные ниже сведения и выберите **Готово**.

    - **Alert Target** (Целевой объект оповещения). Чтобы просмотреть и выбрать целевой объект нового оповещения, воспользуйтесь параметром **Фильтр по подписке** / **Фильтр по типу ресурсов** и выберите ресурс или группу ресурсов из отображаемого списка.

    > [!NOTE]

    > Вы можете выбрать ресурс, группу ресурсов или целую подписку в качестве сигнала журнала действий.

    **Пример представления целевого объекта оповещения** ![Выбор целевого объекта](./media/monitoring-activity-log-alerts-new-experience/select-target.png)

    - В разделе **Целевые условия** щелкните **Добавить условия**. Отобразятся все доступные сигналы целевого объекта, в том числе из разных категорий **журнала действий**, с именем категории, добавленным в имя **Мониторинга службы**.

    - Выберите сигнал из отображенного списка различных операций, возможных для типа **журнал действий**.

    Вы можете выбрать временную шкалу истории журнала и соответствующую логику оповещений для целевого сигнала.

    **Снимок экрана: (Add criteria) (Добавить критерии)**

    ![Добавить критерии](./media/monitoring-activity-log-alerts-new-experience/add-criteria.png)

    **Время журнала**. События, доступные для выбранной операции, могут быть отображены в графике за последние 6/12/24 часа или за последнюю неделю.

    **Логика оповещений.**

     - **Уровень события**. Уровень серьезности события. _Командный_, _Информационный_, _Предупреждение_, _Ошибка_ или _Критический_.
     - **Состояние**. Состояние события. _Запущено_, _Сбой_ или _Успешно выполнено_.
     - **Инициатор события.** Также называется вызывающей стороной. Здесь указывается адрес электронной почты или идентификатор Azure Active Directory пользователя, выполнившего операцию.

        Пример диаграммы сигнала с применением логики предупреждений:

        ![ выбранные критерии](./media/monitoring-activity-log-alerts-new-experience/criteria-selected.png)

4. В разделе **define alert rules details** (Определение сведений о правилах генерации оповещений) укажите следующие сведения.

    - **Имя правила генерации оповещений** — имя нового правила генерации оповещений.
    - **Описание** — описание нового рабочего правила генерации оповещений.
    - **Save alert to resource group** (Сохранить оповещение в группе ресурсов) — выберите группу ресурсов, в которой вы хотите сохранить новое правило.

5. В разделе **Группа действий** в раскрывающемся меню укажите группу действий, которую нужно назначить новому правилу генерации оповещений. Кроме того, [создайте группу действий](monitoring-action-groups.md) и назначьте ее новому правилу. Чтобы создать группу, выберите **+ Создать группу**.

6. Чтобы включить правило после ее создания, выберите **Да** для параметра **Включить правило при создании**.
7. Выберите **Создать правило генерации оповещений**.

    Будет создано правило генерации оповещений для журнала действий и отобразится сообщение с подтверждением в правом верхнем углу окна.

    Вы можете включить, отключить, изменить или удалить правило. [Узнайте больше](#view-and-manage-activity-log-alert-rules-in-azure-portal) об управлении правилами журнала действий.


Кроме того, в качестве простой аналогии для понимания условий, на которых могут создаваться правила генерации оповещений для журнала действий, можно просмотреть или отфильтровать события с помощью [журнала действий на портале Azure](monitoring-overview-activity-logs.md#query-the-activity-log-in-the-azure-portal). В Azure Monitor журнал действий можно отфильтровать или найти необходимые события и затем создать оповещение с помощью кнопки **Добавить оповещение журнала действий**, а затем приступить к руководству, описанному выше, начиная с 4-го шага.
    
 ![ Добавление оповещения из журнала действий](./media/monitoring-activity-log-alerts-new-experience/add-activity-log.png)
    

### <a name="view-and-manage-activity-log-alert-rules-in-azure-portal"></a>Просмотр и управление правилами генерации оповещений журнала действий на портале Azure

1. На портале Azure выберите **Монитор** > **Оповещения** и щелкните **Управление правилами** в верхнем левом углу окна.

    ![ управление правилами генерации оповещений](./media/monitoring-activity-log-alerts-new-experience/manage-alert-rules.png)

    Откроется список доступных правил.

2. Выполните поиск правила журнала действий, которое необходимо изменить.

    ![ Поиск правил генерации оповещений журнала действий](./media/monitoring-activity-log-alerts-new-experience/searth-activity-log-rule-to-edit.png)

    Вы можете использовать доступные фильтры: _Подписка_, _Группа ресурсов_, _Ресурс_, _Тип сигнала_ или _Состояние_, чтобы найти действующее правило, которое необходимо изменить.

    > [!NOTE]

    > Изменить можно только **описание**, **целевые критерии** и **группы действий**.

3.  Выберите правило и дважды щелкните его, чтобы изменить параметры. Внесите необходимые изменения и нажмите кнопку **Сохранить**.

    ![ управление правилами генерации оповещений](./media/monitoring-activity-log-alerts-new-experience/activity-log-rule-edit-page.png)

4.  Вы можете отключить, включить или удалить правило. Выберите соответствующий параметр в верхней части окна после выбора правила, как описано на шаге 2.


## <a name="manage-alert-rules-for-activity-log-using-azure-resource-template"></a>Управление правилами генерации оповещений для журнала действий с помощью шаблона ресурсов Azure
Чтобы создать оповещение журнала действий с помощью шаблона Resource Manager, создайте ресурс типа `microsoft.insights/activityLogAlerts`. Затем следует заполнить все связанные свойства. Вот шаблон, создающий оповещение журнала действий.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "activityLogAlertName": {
      "type": "string",
      "metadata": {
        "description": "Unique name (within the Resource Group) for the Activity log alert."
      }
    },
    "activityLogAlertEnabled": {
      "type": "bool",
      "defaultValue": true,
      "metadata": {
        "description": "Indicates whether or not the alert is enabled."
      }
    },
    "actionGroupResourceId": {
      "type": "string",
      "metadata": {
        "description": "Resource Id for the Action group."
      }
    }
  },
  "resources": [   
    {
      "type": "Microsoft.Insights/activityLogAlerts",
      "apiVersion": "2017-04-01",
      "name": "[parameters('activityLogAlertName')]",      
      "location": "Global",
      "properties": {
        "enabled": "[parameters('activityLogAlertEnabled')]",
        "scopes": [
            "[subscription().id]"
        ],        
        "condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "Administrative"
            },
            {
              "field": "operationName",
              "equals": "Microsoft.Resources/deployments/write"
            },
            {
              "field": "resourceType",
              "equals": "Microsoft.Resources/deployments"
            }
          ]
        },
        "actions": {
          "actionGroups":
          [
            {
              "actionGroupId": "[parameters('actionGroupResourceId')]"
            }
          ]
        }
      }
    }
  ]
}
```
Приведенный выше пример JSON можно сохранить как sampleActivityLogAlert.json, чтобы использовать его в данном пошаговом руководстве. Его можно развернуть с помощью [Azure Resource Manager на портале Azure](../azure-resource-manager/resource-group-template-deploy-portal.md).

> [!NOTE]
> Перед тем как правило генерации оповещений журнала действий станет активным, может пройти до 5 минут.

## <a name="manage-alert-rules-for-activity-log-using-powershell-cli-or-api"></a>Управление правилами генерации оповещений журнала действий с помощью PowerShell, CLI или API
[API оповещений журнала действий Azure Monitor](https://docs.microsoft.com/rest/api/monitor/activitylogalerts) — это интерфейс REST API, который полностью совместим с REST API Azure Resource Manager. Поэтому он может использоваться в PowerShell с помощью командлета Resource Manager, а также в Azure CLI.

Ниже показано использование командлета PowerShell для Azure Resource Manager в примере шаблона ресурсов, приведенного выше (sampleActivityLogAlert.json) в [разделе шаблонов ресурсов](#manage-alert-rules-for-activity-log-using-azure-resource-template).
```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile sampleActivityLogAlert.json -TemplateParameterFile sampleActivityLogAlert.parameters.json
```
Причем в sampleActivityLogAlert.parameters.json указаны значения для параметров, необходимых для создания правила генерации оповещений.

Ниже показано использование команды Azure CLI для Azure Resource Manager в примере шаблона ресурсов, приведенного выше (sampleActivityLogAlert.json) в [разделе шаблонов ресурсов](#manage-alert-rules-for-activity-log-using-azure-resource-template).

```azurecli
az group deployment create --resource-group myRG --template-file sampleActivityLogAlert.json --parameters @sampleActivityLogAlert.parameters.json
```
Причем в sampleActivityLogAlert.parameters.json указаны значения для параметров, необходимых для создания правила генерации оповещений.


## <a name="next-steps"></a>Дополнительная информация

- [Веб-перехватчики для оповещений журнала действий Azure](monitoring-activity-log-alerts-webhook.md)
- [Создание оповещений журнала действий (классических)](monitoring-activity-log-alerts.md) 
- Дополнительные сведения о группах действий см. в статье [Создание групп действий и управление ими на портале Azure](monitoring-action-groups.md).  
- Дополнительные сведения об уведомлениях о работоспособности службы см. в [этой статье](monitoring-service-notifications.md).
