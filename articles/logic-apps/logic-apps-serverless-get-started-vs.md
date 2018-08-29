---
title: Создание бессерверного приложения с помощью Visual Studio | Документация Майкрософт
description: Сведения о том, как в Visual Studio создать, развернуть и администрировать свое первое простое бессерверное приложение на основе Azure Logic Apps и Функций Azure
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
manager: jeconnoc
ms.reviewer: jehollan, LADocs
ms.suite: integration
ms.custom: vs-azure
ms.topic: article
ms.date: 08/01/2018
ms.assetid: d565873c-6b1b-4057-9250-cf81a96180ae
ms.openlocfilehash: ed56e5f2a4a0d9e3a5e8d8c80e0fd20dbfb098bb
ms.sourcegitcommit: fab878ff9aaf4efb3eaff6b7656184b0bafba13b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/22/2018
ms.locfileid: "42444792"
---
# <a name="build-your-first-serverless-app-with-azure-logic-apps-and-azure-functions---visual-studio"></a>Создание первого бессерверного приложения на основе Azure Logic Apps и Функций Azure (Visual Studio)

Вы можете быстро разработать и развернуть облачное приложение с помощью бессерверных средств и возможностей в Azure, таких как [Azure Logic Apps](../logic-apps/logic-apps-overview.md) и [Функции Azure](../azure-functions/functions-overview.md). В этой статье показано, как в Visual Studio приступить к созданию бессерверного приложения на основе приложения логики, которое вызывает функцию Azure. Дополнительные сведения о бессерверных решениях в Azure см. в статье [Обзор бессерверных компонентов Azure с функциями и Logic Apps](../logic-apps/logic-apps-serverless-overview.md).

## <a name="prerequisites"></a>Предварительные требования

Для создания бессерверного приложения в Visual Studio вам потребуется следующее:

* Подписка Azure. Если у вас еще нет подписки Azure, [зарегистрируйтесь для получения бесплатной учетной записи Azure](https://azure.microsoft.com/free/).

* [Visual Studio 2017](https://www.visualstudio.com/vs/) или Visual Studio 2015 — выпуски Community, Professional или Enterprise

* [Пакет Microsoft Azure SDK](https://azure.microsoft.com/downloads/) (2.9.1 или более поздней версии).

* [Azure PowerShell](https://github.com/Azure/azure-powershell#installation)

* [Средства Azure Logic Apps для Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=VinaySinghMSFT.AzureLogicAppsToolsforVisualStudio-18551) или [Visual Studio 2015](https://marketplace.visualstudio.com/items?itemName=VinaySinghMSFT.AzureLogicAppsToolsforVisualStudio).

  Вы можете скачать и установить средства Azure Logic Apps напрямую из Visual Studio Marketplace или узнать, [как установить это расширение из Visual Studio](https://docs.microsoft.com/visualstudio/ide/finding-and-using-visual-studio-extensions). 
  После завершения установки перезагрузите Visual Studio. 

* [Основные инструменты службы "Функции Azure"](https://www.npmjs.com/package/azure-functions-core-tools) для локальной отладки функций.

* Доступ к Интернету для использования конструктора приложений логики, встроенного в Visual Studio.

  Конструктору требуется подключение к Интернету, чтобы создать ресурсы в Azure и считать свойства и данные из соединителей в приложении логики. 
  Например, если вы используете соединитель Dynamics CRM Online, конструктор проверяет экземпляр CRM и получает информацию для отображения доступных свойств по умолчанию и пользовательских свойств.

## <a name="create-resource-group-project"></a>Создание проекта группы ресурсов

Чтобы начать работу, создайте для бессерверного приложения [проект группы ресурсов Azure](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md). Ресурсы в Azure создаются в группе ресурсов, которая представляет собой логическую коллекцию для организации, развертывания ресурсов приложения и управления ими как единым ресурсом. Группа ресурсов для бессерверного приложения в Azure содержит ресурсы для Azure Logic Apps и службы "Функции Azure". Узнайте больше о [группах ресурсов Azure и ресурсах](../azure-resource-manager/resource-group-overview.md).

1. Запустите Visual Studio и войдите в учетную запись Azure. 

1. В меню **Файл** выберите **Создать** > **Проект**. 

   ![Создание проекта в Visual Studio](./media/logic-apps-serverless-get-started-vs/create-new-project-visual-studio.png)

1. В разделе **Установленные** выберите **Visual C#** или **Visual Basic**. Выберите **Облако** > **Группа ресурсов Azure**.

   Если не существует категория **Cloud** или проект **Группа ресурсов Azure**, убедитесь, что у вас установлен пакет Azure SDK для Visual Studio.

1. Укажите имя и расположение проекта, а затем щелкните **ОК**. 

   Visual Studio предложит выбрать шаблон. 
   Вы можете начать с пустого приложения логики или любого существующего шаблона. В нашем примере используется шаблон быстрого запуска Azure для создания бессерверного приложения с приложением логики и вызовом функции Azure.

   Чтобы создать в Visual Studio только приложение логики, выберите шаблон **Приложение логики**. Этот шаблон позволяет создать пустое приложение логики, которое открывается в конструкторе приложений, не требуя предварительно развертывать решение в группе ресурсов Azure.

1. В разделе **Показывать шаблоны из этого расположения** выберите **Azure Quickstart (github/Azure/azure-quickstart-templates)** (Быстрый запуск Azure (github/Azure/azure-quickstart-templates)). 

1. В поле поиска введите выражение фильтра "logic-app", выберите этот шаблон быстрого запуска для бессерверного приложения и щелкните **ОК**: **101-logic-app-and-function-app**.

   ![Выбор шаблона быстрого запуска Azure](./media/logic-apps-serverless-get-started-vs/select-template.png)

   Visual Studio создаст и откроет решение для проекта группы ресурсов. 
   Выбранный шаблон быстрого запуска позволяет создать шаблон развертывания с именем `azuredeploy.json` в проекте группы ресурсов. 
   Этот шаблон развертывания включает определение простого приложения логики, которое запускает HTTP-запрос, вызывает функцию Azure и возвращает результат в виде HTTP-ответа. 
   
   ![Новое бессерверное решение](./media/logic-apps-serverless-get-started-vs/create-serverless-solution.png)

1. Теперь это решение следует развернуть в Azure, и тогда вы сможете открыть шаблон развертывания и просмотреть ресурсы для бессерверного приложения. 

## <a name="deploy-your-solution"></a>Развертывание решения

Чтобы открыть приложение логики в соответствующем конструкторе в Visual Studio, нужно развернуть в Azure группу ресурсов. Это позволит конструктору создавать подключения к ресурсам и службам из приложения логики. Для этого разверните решение с помощью Visual Studio на портал Azure.

1. В обозревателе решений откройте контекстное меню проекта и выберите **Развернуть** > **Создать**.

   ![Создание развертывания для группы ресурсов](./media/logic-apps-serverless-get-started-vs/deploy.png)

1. Выберите подписку Azure, если она еще не выбрана, и группу ресурсов для предстоящего развертывания. Выберите **Развернуть**.

   ![Параметры развертывания](./media/logic-apps-serverless-get-started-vs/deploy-to-resource-group.png)

1. Если появится диалоговое окно **Изменение параметров**, укажите имя ресурса для приложения логики и приложение-функцию Azure для этого развертывания, а затем сохраните введенные параметры. Используйте для приложения-функции глобально уникальное имя.

   ![Ввод имен для приложения логики и приложения-функции](./media/logic-apps-serverless-get-started-vs/logic-function-app-name-parameters.png)

   Когда Visual Studio запускает развертывание решения в указанной группе ресурсов, состояние этого развертывания отображается в **окне вывода** Visual Studio. 
   После завершения развертывания приложение логики активируется на портале Azure.

## <a name="edit-logic-app-in-visual-studio"></a>Изменение приложения логики в Visual Studio

Теперь, когда решение развернуто в группе ресурсов, откройте приложение логики в конструкторе приложений логики, который позволяет редактировать и изменять это приложение.

1. В обозревателе решений откройте контекстное меню файла `azuredeploy.json` и выберите **Open With Logic App Designer** (Открыть в конструкторе приложений логики).

   ![Открытие файла azuredeploy.json в конструкторе приложений логики](./media/logic-apps-serverless-get-started-vs/open-logic-app-designer.png)

1. Когда появится окно **свойств приложения логики**, выберите нужную подписку Azure в разделе **Подписки**, если она еще не выбрана. В разделе **Группа ресурсов** выберите группу ресурсов и расположение, в котором развернуто решение, и щелкните **ОК**.

   ![Свойства приложения логики](./media/logic-apps-serverless-get-started-vs/logic-app-properties.png)

   В открывшемся окне конструктора приложений логики вы можете добавить новые действия или изменить рабочий процесс, а затем сохранить изменения.

   ![Открытое приложение логики в конструкторе приложений логики](./media/logic-apps-serverless-get-started-vs/opened-logic-app.png)

## <a name="create-azure-functions-project"></a>Создание проекта Функций Azure

Чтобы создать проект Функций Azure и функцию с помощью JavaScript, Python, F#, PowerShell, пакетной службы или Bash, выполните действия, описанные в статье [Запуск основных инструментов службы "Функции Azure"](../azure-functions/functions-run-local.md). Чтобы создать в этом решении функцию Azure на языке C#, можно применить библиотеку классов C# с помощью процедуры, описанной в записи блога [о публикации библиотеки классов .NET в качестве приложения-функции](https://blogs.msdn.microsoft.com/appserviceteam/2017/03/16/publishing-a-net-class-library-as-a-function-app/).

## <a name="deploy-functions-from-visual-studio"></a>Развертывание функций из Visual Studio

Шаблон развертывания позволяет развернуть все включенные в решение функции Azure из репозитория Git, который определяется переменными в файле `azuredeploy.json`. Если вы проектируете и создаете в вашем решении проект Функций Azure, его можно передать в систему управления версиями Git, например GitHub или Visual Studio Team Services, а затем указать значение переменной `repo` таким образом, чтобы с этим шаблоном развертывалась нужная функция Azure.

## <a name="manage-logic-apps-and-view-run-history"></a>Управление приложениями логики и просмотр журнала выполнения

Если приложение логики уже развернуто в Azure, вы по-прежнему можете изменять его, управлять им, просматривать журнал выполнения и отключать приложение с помощью Visual Studio. 

1. Откройте меню **Представление** в Visual Studio и выберите **Cloud Explorer**. 

1. В разделе **Все подписки** выберите подписку Azure, связанную с нужным приложением логики, и щелкните **Применить**.

1. В разделе **Приложения логики** выберите это приложение логики. В контекстном меню для приложения логики выберите **Open with Logic App Editor** (Открыть в редакторе приложений логики). 

Теперь опубликованное приложение логики можно скачать в проект группы ресурсов. Таким образом, даже созданное на портале Azure приложение логики можно импортировать, чтобы управлять им с помощью Visual Studio. Дополнительные сведения см. в статье [Управление приложениями логики в Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md).

## <a name="next-steps"></a>Дополнительная информация

* [Создание панели мониторинга потоковой передачи сведений о клиентах с помощью Функций Azure и Azure Logic Apps](logic-apps-scenario-social-serverless.md)
* [Управление приложениями логики в Visual Studio](manage-logic-apps-with-visual-studio.md)
* [Язык определения рабочего процесса приложений логики](logic-apps-workflow-definition-language.md)