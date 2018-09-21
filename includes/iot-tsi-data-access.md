---
title: включение файла
description: включение файла
services: time-series-insights
author: ashannon7
ms.service: time-series-insights
ms.topic: include
ms.date: 08/20/2018
ms.author: anshan
ms.custom: include file
ms.openlocfilehash: fb45ea02f365cf4e7b394e249f9b91a784e5469f
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/19/2018
ms.locfileid: "46370067"
---
## <a name="grant-data-access"></a>Предоставление доступа к данным

Выполните следующие действия, чтобы предоставить доступ к данным для субъекта-пользователя.

1. Войдите на [портале Azure](https://portal.azure.com).

2. Найдите среду "Аналитика временных рядов". Введите **Временные ряды** в поле **поиска**. Выберите **Среда временных рядов** в результатах поиска. 

3. Выберите среду Time Series Insights в списке.

4. Выберите **Политики доступа к данным**, а затем нажмите кнопку **+Добавить**.
    ![Управление исходной средой "Аналитика временных рядов"](media/iot-tsi-data-access/getstarted-grant-data-access1.png)

5. Щелкните **Выбор пользователя**.  Выполните поиск по имени пользователя или адресу электронной почты, чтобы найти пользователя, которого следует добавить. Нажмите **Выбрать**, чтобы подтвердить выбор. 

    ![Управление источником Time Series Insights — "Добавить"](media/iot-tsi-data-access/getstarted-grant-data-access2.png)

6. Щелкните **Выбор ролей**. Выберите соответствующую роль доступа для пользователя.
    - Выберите **Участник**, если вы хотите разрешить пользователю изменять ссылочные данные и предоставить другим пользователям среды доступ к сохраненным запросам и перспективам. 
    - В противном случае выберите **Читатель**, чтобы разрешить пользователю обращаться к данным в среде и сохранять в ней личные (не общие) запросы.

    Нажмите кнопку **ОК**, чтобы подтвердить выбор роли.

    ![Управление источником Time Series Insights — "Выбор пользователя"](media/iot-tsi-data-access/getstarted-grant-data-access3.png)

7. На странице **Выбор роли пользователя** нажмите кнопку **ОК**.

    ![Управление источником Time Series Insights — "Выбор ролей"](media/iot-tsi-data-access/getstarted-grant-data-access4.png)

8. На странице **Политики доступа к данным** перечислены пользователи и соответствующие роли.

    ![Управление источником Time Series Insights — результаты](media/iot-tsi-data-access/getstarted-grant-data-access5.png)