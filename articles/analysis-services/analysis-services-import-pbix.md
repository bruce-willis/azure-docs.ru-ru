---
title: Импорт файла Power BI Desktop в Azure Analysis Services | Документация Майкрософт
description: Из этой статьи вы узнаете, как импортировать файл Power BI Desktop (PBIX-файл) с помощью портала Azure.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 08/16/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: a2855ca5dbb76d3fcc30c4b1007c20bb48c91c9b
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/17/2018
ms.locfileid: "42146834"
---
# <a name="import-a-power-bi-desktop-file"></a>Импорт файла Power BI Desktop

Вы можете импортировать модель данных в файл Power BI Desktop в Azure Analysis Services. Импортируются метаданные модели, кэшированные данные и подключения к источникам данных. Отчеты и визуализации не импортируются. Импортированные модели данных из Power BI Desktop имеют уровень совместимости 1400.

**Ограничения**   

- Импорт из pbix-файла использует функцию веб-дизайнера на портале, которая является **предварительной версией**. Функциональность ограничена. Для разработки и тестирования более сложных моделей лучше использовать Visual Studio (SSDT) и SQL Server Management Studio (SSMS).
- Необходимо иметь разрешения администратора сервера для импорта из pbix-файлов.
- PBIX-модель может подключаться только к таким источникам данных, как **База данных SQL Azure** и **Хранилище данных SQL Azure**.
- Для PBIX-модели не должны быть установлены активные подключения или подключения DirectQuery. 
- Если PBIX-модель данных содержит метаданные, которые не поддерживаются в Analysis Services, импорт может завершиться ошибкой.


## <a name="to-import-from-pbix"></a>Импорт из PBIX-файла

1. На сервере последовательно выберите **Обзор** > **Дизайнер веб-страниц** и щелкните **Открыть**.

    ![Создание модели на портале Azure](./media/analysis-services-create-model-portal/aas-create-portal-overview-wd.png)

2. Выбрав **Web designer** (Конструктор веб-приложений)  >  **Модели**, нажмите кнопку **+ Добавить**.

    ![Создание модели на портале Azure](./media/analysis-services-create-model-portal/aas-create-portal-models.png)

3. В диалоговом окне **Новая модель** введите имя модели и выберите файл Power BI Desktop.

    ![Диалоговое окно "Новая модель" на портале Azure](./media/analysis-services-import-pbix/aas-import-pbix-new-model.png)

4. В окне **Импорт** найдите и выберите нужный файл.

     ![Диалоговое окно "Подключение" на портале Azure](./media/analysis-services-import-pbix/aas-import-pbix-select-file.png)

## <a name="change-credentials"></a>Изменение учетных данных

При импорте модели данных pbix-файла учетные данные, используемые для подключения к источнику данных, по умолчанию устанавливаются в ServiceAccount. После того как модель была импортирована из pbix, вы можете изменить учетные данные, используя следующие методы.

- Для редактирования учетных данных используйте SSMS от июля 2018 года (версия 17.8.1) или более поздней версии. Это самый простой способ.
- Используйте [команду Alter](https://docs.microsoft.com/sql/analysis-services/tabular-models-scripting-language-commands/alter-command-tmsl) TMSL в [объекте DataSources](https://docs.microsoft.com/sql/analysis-services/tabular-models-scripting-language-objects/datasources-object-tmsl), чтобы изменить свойство строки подключения. 
- Откройте модель в Visual Studio, отредактируйте учетные данные для подключения к источнику данных и затем повторно разверните модель.

Чтобы изменить учетные данные с помощью среды SSMS, выполните следующие действия. 

1. В среде SSMS разверните базу данных > **Подключения**. 
2. Щелкните правой кнопкой мыши на соединение с базой данных и нажмите **Обновить учетные данные**. 

    ![Обновление учетных данных](./media/analysis-services-import-pbix/aas-import-pbix-creds.png)

3. В диалоговом окне учетных данных выберите тип учетных данных и введите учетные данные. Для проверки подлинности SQL выберите "База данных". Для учетной записи организации (OAuth) выберите учетную запись Майкрософт.
    ![Изменение учетных данных](./media/analysis-services-import-pbix/aas-import-pbix-edit-creds.png)

Версия Power BI Desktop с июля 2018 года включает новую функцию для изменения разрешений источника данных. На вкладке **Главная** щелкните **Изменить запросы**  > **Параметры источника данных**. Выберите подключение источника данных и нажмите кнопку **Изменить разрешения**.


## <a name="see-also"></a>См. также

[Создание модели на портале Azure](analysis-services-create-model-portal.md)   
[Подключение к службам Azure Analysis Services](analysis-services-connect.md)  
