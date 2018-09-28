---
title: Создание автоматизированных рабочих процессов и управление ими в Visual Studio Code с помощью Azure Logic Apps | Документы Майкрософт
description: Создание приложений логики и управление ими в Visual Studio Code (VS Code)
services: logic-apps
ms.service: logic-apps
ms.workload: azure-vs
author: ecfan
ms.author: estfan
ms.topic: article
ms.reviewer: klam, deli, LADocs
ms.suite: integration
ms.date: 09/24/2018
ms.openlocfilehash: fd92b093b70dba2df3ec645b3ecb77233d3ebd5c
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46958362"
---
# <a name="create-and-manage-automated-logic-app-workflows---visual-studio-code"></a>Создание автоматизированных рабочих процессов приложений логики и управление ими в Visual Studio Code

С помощью [Azure Logic Apps](../logic-apps/logic-apps-overview.md) и Visual Studio Code можно создать приложения логики, которые автоматизируют задачи, рабочие процессы и процессы для интеграции приложений, данных, систем и служб по предприятиям и организациям, и управлять ими. В этой статье содержатся сведения о создании и изменении определений рабочих процессов приложений логики при работе в среде на основе кода. Можно также работать с приложениями логики, уже развернутыми в <a href="https://docs.microsoft.com/azure/guides/developer/azure-developer-guide" target="_blank">Azure</a> в облаке. 

Несмотря на то, что эти задачи можно выполнять на <a href="https://portal.azure.com" target="_blank">портале Azure</a> и в Visual Studio, Visual Studio Code позволяет быстрее приступить к работе непосредственно в коде. Например, вы также можете отключать, включать, удалять и обновлять уже созданные приложения логики.

В этой статье приводятся инструкции по созданию такого же приложения логики, что и в [кратком руководстве по созданию приложения логики на портале Azure](../logic-apps/quickstart-create-first-logic-app-workflow.md), в котором основное внимание уделяется базовым понятиям. В Visual Studio Code приложение логики выглядит так, как показано в следующем примере.

![Готовое приложение логики](./media/create-logic-apps-visual-studio-code/overview.png)

## <a name="prerequisites"></a>Предварительные требования

Прежде чем начать, убедитесь, что вы выполняете следующие условия.

* Если у вас еще нет подписки Azure, <a href="https://azure.microsoft.com/free/" target="_blank">зарегистрируйтесь для получения бесплатной учетной записи Azure</a>.

* Наличие базовых знаний об [определениях рабочих процессов](../logic-apps/logic-apps-workflow-definition-language.md) приложений логики и структуре этих определений, которая использует нотацию объектов JavaScript (JSON).

* Доступ к Интернету для входа в Azure и в подписку Azure.

* Скачайте и установите эти средства, если вы еще этого не сделали: 

  * бесплатная <a href="https://code.visualstudio.com/" target="_blank">версия Visual Studio Code 1.25.1 или более поздняя версия</a>;

  * расширение Visual Studio Code для Azure Logic Apps.

    Это расширение можно установить из Visual Studio Code. 
    Перезапустите Visual Studio Code после установки. 

    ![Поиск расширения Visual Studio Code для Azure Logic Apps](./media/create-logic-apps-visual-studio-code/find-install-logic-apps-extension.png)

    Значок Azure на панели инструментов Visual Studio Code означает, что расширение установлено правильно. 

    ![Установленное расширение](./media/create-logic-apps-visual-studio-code/installed-extension.png)

    Дополнительные сведения см. на странице <a href="https://code.visualstudio.com/docs/editor/extension-gallery" target="_blank">Extension Marketplace</a> (Расширения в Marketplace). 

<a name="sign-in-azure"></a>

## <a name="sign-in-to-azure"></a>Вход в Azure

1. Откройте Visual Studio Code. На панели инструментов Visual Studio Code щелкните значок Azure. 

   ![Выбор значка Azure](./media/create-logic-apps-visual-studio-code/open-extension.png)

1. В окне Azure в разделе **Logic Apps** выберите пункт **Sign in to Azure** (Войти в Azure). 

   ![Выбор пункта "Sign in to Azure" (Войти в Azure).](./media/create-logic-apps-visual-studio-code/sign-in-azure.png)

   Вам будет предложено выполнить вход с помощью предоставленного кода проверки подлинности. 

1. Скопируйте код проверки подлинности, а затем нажмите кнопку **Копировать и открыть**, после чего откроется новое окно браузера.

   ![Запрос на вход](./media/create-logic-apps-visual-studio-code/sign-in-prompt.png)

1. Введите код проверки подлинности. При появлении запроса нажмите кнопку **Продолжить**.

   ![Ввод кода](./media/create-logic-apps-visual-studio-code/authentication-code.png)

1. Выберите учетную запись Azure. После входа можно закрыть браузер и вернуться в Visual Studio Code.

   Теперь в окне Azure в разделах Logic Apps и учетных записей интеграции отображаются подписки Azure в вашей учетной записи. 

   ![Выберите подписку.](./media/create-logic-apps-visual-studio-code/select-azure-subscription.png)

   Если нужные подписки отсутствуют, рядом с названием **Logic Apps** щелкните **Select Subscriptions** (Выбрать подписки) (значок фильтра). Найдите и выберите нужные подписки.

1. Чтобы просмотреть существующие приложения логики или учетные записи интеграции в подписке Azure, разверните подписку.

   ![Просмотр приложений логики и учетных записей интеграции](./media/create-logic-apps-visual-studio-code/existing-logic-apps.png)

<a name="create-logic-app"></a>

## <a name="create-logic-app"></a>Создание приложения логики

1. Если вы еще не вошли подписку Azure в Visual Studio Code, выполните описанные в этой статье действия по [входу](#sign-in-azure).

1. В контекстном меню подписки выберите команду **Создать**.

   ![Выбор команды "Создать"](./media/create-logic-apps-visual-studio-code/create-logic-app.png)

1. В списке групп ресурсов Azure в подписке выберите имеющуюся группу ресурсов или **создайте группу ресурсов**. 

   В этом примере показано создание группы ресурсов.

   ![Создание группы ресурсов](./media/create-logic-apps-visual-studio-code/select-or-create-azure-resource-group.png)

1. Укажите имя для группы ресурсов Azure и нажмите клавишу ВВОД.

   ![Указание имени для группы ресурсов](./media/create-logic-apps-visual-studio-code/enter-name-resource-group.png)

1. Выберите расположение центра обработки данных для сохранения метаданных приложения логики.

   ![Выбор расположения](./media/create-logic-apps-visual-studio-code/select-location.png)

1. Укажите имя для приложения логики, а затем нажмите клавишу ВВОД.

   ![Указание имени для приложения логики](./media/create-logic-apps-visual-studio-code/enter-name-logic-app.png)

   Новое приложение логики появится в окне Azure под подпиской Azure. Теперь можно приступить к созданию определения рабочего процесса приложения логики.

1. В контекстном меню приложения логики выберите **Открыть в редакторе**. 

   ![Открытие приложения логики в редакторе](./media/create-logic-apps-visual-studio-code/open-new-logic-app.png)

   В Visual Studio Code откроется шаблон определения рабочего процесса приложения логики (файл logicapp.json), и вы сможете приступить к созданию рабочего процесса приложения логики.

   ![Новое определение рабочего процесса приложения логики](./media/create-logic-apps-visual-studio-code/blank-logic-app-workflow-definition.png)

1. В файле шаблона определения рабочего процесса приложения логики начните создавать определение рабочего процесса приложения логики. Технические справочные сведения см. в статье [Сведения о схеме языка определения рабочих процессов в Azure Logic Apps](../logic-apps/logic-apps-workflow-definition-language.md).

   Ниже приведен пример определения логики. Как правило, элементы JSON отображаются в каждом разделе в алфавитном порядке, но в этом примере элементы показаны в порядке появления действий приложения логики в конструкторе.

   ```json
   {
      "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
         "$connections": {
            "defaultValue": {},
            "type": "Object"
         }
      },
      "triggers": {
         "When_a_feed_item_is_published": {
            "recurrence": {
               "frequency": "Minute",
               "interval": 1
            },
            "splitOn": "@triggerBody()?['value']",
            "type": "ApiConnection",
            "inputs": {
               "host": {
                  "connection": {
                     "name": "@parameters('$connections')['rss']['connectionId']"
                  }
               },
               "method": "get",
               "path": "/OnNewFeed",
               "queries": {
                  "feedUrl": "http://feeds.reuters.com/reuters/topNews"
               }
            }
         }
      },
      "actions": {
         "Send_an_email": {
            "runAfter": {},
            "type": "ApiConnection",
            "inputs": {
               "body": {
                  "Body": "Title: @{triggerBody()?['title']}\n\nDate published: @{triggerBody()?['publishDate']}\n\nLink: @{triggerBody()?['primaryLink']}",
                  "Subject": "New RSS item: @{triggerBody()?['title']}",
                  "To": "Sophie.Owen@contoso.com"
               },
               "host": {
                  "connection": {
                     "name": "@parameters('$connections')['outlook']['connectionId']"
                  }
               },
               "method": "post",
               "path": "/Mail"
            }
         }
      },
      "outputs": {}
   }   
   ```

1. Закончив, сохраните файл определения приложения логики. Когда в Visual Studio Code появится запрос на подтверждение отправки определения приложения логики в подписку Azure, нажмите кнопку **Отправить**.

   ![Отправка нового приложения логики](./media/create-logic-apps-visual-studio-code/upload-new-logic-app.png)

   После того как Visual Studio Code опубликует приложение логики в Azure, приложение будет доступно и запущено на портале Azure. 

   ![Опубликованное приложение логики на портале Azure](./media/create-logic-apps-visual-studio-code/published-logic-app.png)

<a name="edit-logic-app"></a>

## <a name="edit-logic-app"></a>Изменение приложения логики

Для работы с приложением логики, уже развернутым в Azure, откройте файл определения рабочего процесса этого приложения в Visual Studio Code.

1. Если вы еще не вошли подписку Azure в Visual Studio Code, выполните описанные в этой статье действия по [входу](#sign-in-azure).

1. В окне Azure в разделе **Logic Apps** разверните подписку Azure и выберите нужное приложение логики. 

1. В меню приложения логики выберите пункт **Открыть в редакторе**. Или рядом с именем приложения логики щелкните значок редактирования.

   ![Открытие редактора для имеющегося приложения логики](./media/create-logic-apps-visual-studio-code/open-editor-existing-logic-app.png)

   В Visual Studio Code откроется файл logicapp.json для определения рабочего процесса приложения логики.

   ![Открытое определение рабочего процесса приложения логики](./media/create-logic-apps-visual-studio-code/edit-logic-app-workflow-definition-file.png)

1. Внесите необходимые изменения в определение приложения логики.

1. Сохраните изменения, когда закончите.

1. Когда в Visual Studio Code появится запрос на обновление определения приложения логики в подписке Azure, нажмите кнопку **Отправить**. 

   ![Отправка изменений](./media/create-logic-apps-visual-studio-code/upload-logic-app-changes.png)

## <a name="get-support"></a>Получение поддержки

* Если у вас возникли вопросы, то посетите <a href="https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps" target="_blank">форум Azure Logic Apps</a>.
* Отправить идею по поводу возможности или проголосовать за нее вы можете на <a href="http://aka.ms/logicapps-wish" target="_blank">сайте отзывов пользователей Logic Apps</a>.

