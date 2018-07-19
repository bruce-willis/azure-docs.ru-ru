---
title: Добавление условных операторов с помощью Azure Logic Apps | Документация Майкрософт
description: Создание условий, которые управляют действиями в рабочих процессах Azure Logic Apps
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
manager: jeconnoc
ms.date: 03/05/2018
ms.topic: article
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: d4e69d33e07f484b4ccc5343786865230368c7ca
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37096382"
---
# <a name="create-conditional-statements-that-control-workflow-actions-in-azure-logic-apps"></a>Создание условных операторов, которые управляют действиями в рабочих процессах Azure Logic Apps

Чтобы выполнить определенные действия в приложении логики только после соблюдения указанного условия, добавьте *условный оператор*. Эта структура сравнивает данные в рабочем процессе с конкретными значениями или полями. Затем вы можете определить различные действия для запуска в зависимости от того, соответствуют ли данные условию. Вы можете вложить условия друг в друга.

Например, предположим, что у вас есть приложение логики, которое отправляет слишком много электронных писем, когда в RSS-канале веб-сайта появляются новые элементы. Вы можете добавить условный оператор для отправки электронных писем только в том случае, если новый элемент содержит определенную строку. 

> [!TIP]
> Чтобы выполнять различные шаги на основе определенных значений, используйте [*оператор switch*](../logic-apps/logic-apps-control-flow-switch-statement.md).

## <a name="prerequisites"></a>предварительным требованиям

* Подписка Azure. Если у вас нет ее, вы можете [зарегистрироваться для получения бесплатной учетной записи Azure](https://azure.microsoft.com/free/).

* Базовые знания [создания приложений логики](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Чтобы выполнить действия, указанные в данной статье, [создайте этот пример приложения логики](../logic-apps/quickstart-create-first-logic-app-workflow.md), используя учетную запись Outlook.com или Office 365 Outlook.

## <a name="add-a-condition"></a>Добавить условие

1. На <a href="https://portal.azure.com" target="_blank">портале Azure</a> откройте приложение логики в конструкторе приложений логики.

2. Добавьте условие в нужное место. 

   Чтобы добавить условие между шагами, наведите указатель мыши на стрелку, где его нужно добавить. Щелкните появившийся **знак "плюс"** (**+**), а затем выберите **Add a condition** (Добавить условие). Например: 

   ![Добавление условий между шагами](./media/logic-apps-control-flow-conditional-statement/add-condition.png)

   Если необходимо добавить условие в конце рабочего процесса, перейдите в нижнюю часть приложения логики и выберите **+ Новый шаг** > **Add a condition** (Добавить условие).

3. Создайте условие в разделе **Условие**. 

   1. В текстовом поле слева укажите данные или поле, которое вы хотите сравнить.

      Если щелкнуть в поле слева, отобразится список динамического содержимого, с помощью которого можно выбрать выходные данные, полученные на предыдущих шагах. 
      В этом примере выберите пункт RSS feed summary (Сводка по RSS-каналу).

      ![Создание условия](./media/logic-apps-control-flow-conditional-statement/edit-condition.png)

   2. В среднем списке выберите операцию для выполнения. 
   В этом примере выберите **содержит**. 

   3. В текстовом поле справа укажите в качестве критерия значение или поле. 
   В этом примере укажите следующую строку: **Microsoft**.

   Ниже приведено полное условие:

   ![Полное условие](./media/logic-apps-control-flow-conditional-statement/edit-condition-2.png)

5. В разделе **Если истинно** и **Если ложно** добавьте шаги, которые будут выполнены в зависимости от того, удовлетворяется ли условие. Например: 

   ![Условие с путями "Если истинно" и "Если ложно"](./media/logic-apps-control-flow-conditional-statement/condition-yes-no-path.png)

   > [!TIP]
   > Существующие действия можно перетаскивать в пути **Если истинно** и **Если ложно**.

6. Сохраните приложение логики.

Теперь это приложение логики отправляет сообщение только в том случае, если новые элементы в RSS-канале соответствуют вашему условию.

## <a name="json-definition"></a>Определение JSON

Теперь, когда приложение логики создано с помощью условного оператора, можно рассмотреть определение программного высокоуровневого кода условного оператора.

``` json
"actions": {
  "Condition": {
    "type": "If",
    "actions": {
      "Send_an_email": {
        "inputs": {},
        "runAfter": {}
    },
    "expression": {
      "and": [ 
        { 
          "contains": [ 
            "@triggerBody()?['summary']", "Microsoft"
          ]
        } 
      ]
    },
    "runAfter": {}
  }
},
```

## <a name="get-support"></a>Получение поддержки

* Если у вас возникли вопросы, то посетите [форум Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Оставить предложения по функциям или проголосовать за них вы можете на [сайте отзывов пользователей Azure Logic Apps](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Дополнительная информация

* [Операторы switch. Выполнение различных шагов на основе различных значений в приложениях логики](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Loops: Process arrays or repeat actions until a condition is met](../logic-apps/logic-apps-control-flow-loops.md) (Циклы. Обработка массивов или повторение действий до выполнения условия)
* [Create or join parallel branches in your logic app](../logic-apps/logic-apps-control-flow-branches.md) (Создание или присоединение параллельных ветвей в приложении логики)
* [Области. Выполнение шагов на основе состояния группы в приложениях логики](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
