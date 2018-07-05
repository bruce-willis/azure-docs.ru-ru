---
title: Подключение к Azure Analysis Services с помощью Excel | Документы Майкрософт
description: Сведения о подключении к серверу Azure Analysis Services с помощью Excel.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/03/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 807496584acb3f93fccd3495de005792b769b37f
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/03/2018
ms.locfileid: "37443007"
---
# <a name="connect-with-excel"></a>Подключение с помощью Excel

После создания сервера и развертывания на нем табличной модели клиенты смогут подключиться к нему для работы с данными. 

## <a name="before-you-begin"></a>Перед началом работы
Учетная запись, с помощью которой вы вошли в систему, должна принадлежать к роли шаблона базы данных с по крайней мере с правами на чтение. Дополнительные сведения см. в руководстве по [аутентификации и настройке пользовательских разрешений](analysis-services-manage-users.md). 

## <a name="connect-in-excel"></a>Подключение в Excel

Подключиться к серверу из Excel можно с помощью функции "Получить данные" в Excel 2016. Подключение с помощью мастера импорта таблиц в Power Pivot не поддерживается. 

**Подключение в Excel 2016**

1. В Excel 2016 на ленте **Данные** последовательно выберите **Получить внешние данные** > **Из других источников** > **Из служб Analysis Services**.

2. В мастере подключения к данным введите имя сервера, включая протокол и универсальный код ресурса (URI), в поле **Имя сервера**. Например, asazure://westcentralus.asazure.windows.net/advworks. Затем для параметра **Учетные данные** выберите значение **Использовать следующие имя пользователя и пароль**, введите имя пользователя, например nancy@adventureworks.com, и пароль для него.

    > [!IMPORTANT]
    > Если вы входите в систему с учетной записью Майкрософт, Live ID, Yahoo, Gmail, и т. д., или требуется войти с помощью многофакторной проверки подлинности, оставьте поле пароля пустым. Пароль запрашивается после нажатия кнопки "Далее". 

    ![Подключение из Excel: вход](./media/analysis-services-connect-excel/aas-connect-excel-logon.png)

3. В разделе **Выбор базы данных и таблицы** выберите нужную базу данных, а также модель или перспективу, и нажмите кнопку **Готово**.
   
    ![Подключение из Excel: выбор модели](./media/analysis-services-connect-excel/aas-connect-excel-select.png)


## <a name="see-also"></a>См. также
[Клиентские библиотеки](analysis-services-data-providers.md)   
[Управление службами Analysis Services](analysis-services-manage.md)     


