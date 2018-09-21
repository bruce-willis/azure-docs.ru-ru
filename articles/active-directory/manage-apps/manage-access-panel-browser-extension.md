---
title: Устранение неполадок, связанных с расширением панели доступа Azure для Internet Explorer | Документация Майкрософт
description: Как применить групповую политику для развертывания надстройки Internet Explorer для работы с порталом «Мои приложения».
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/11/2018
ms.author: barbkess
ms.reviewer: asteen
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9ebd949460f826c9529b9f392bc4a7f4918ee170
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/12/2018
ms.locfileid: "44715046"
---
# <a name="troubleshooting-the-access-panel-extension-for-internet-explorer"></a>Устранение неполадок, связанных с расширением панели доступа для Internet Explorer
Эта статья поможет устранить следующие проблемы.

* Не удается получить доступ к приложениям через портал «Мои приложения» при использовании Internet Explorer.
* Отображается сообщение «Установка программного обеспечения», хотя программное обеспечение уже установлено.

Если вы являетесь администратором, также прочтите статью [Развертывание расширения панели доступа для Internet Explorer с помощью групповой политики](deploy-access-panel-browser-extension.md)

## <a name="run-the-diagnostic-tool"></a>Запуск средства диагностики
Для диагностики проблем с установкой расширения панели доступа загрузите и запустите средство диагностики панели доступа.

1. [Щелкните здесь, чтобы загрузить средство диагностики.](https://account.activedirectory.windowsazure.com/applications/AccessPanelExtensionDiagnosticTool/AccessPanelExtensionDiagnosticTool.zip)
2. Откройте файл и нажмите кнопку **Извлечь все** .
   
    ![Нажмите кнопку «Извлечь все».](./media/manage-access-panel-browser-extension/extract1.png)
3. Нажмите кнопку **Извлечь** , чтобы продолжить.
   
    ![Нажмите кнопку «Извлечь».](./media/manage-access-panel-browser-extension/extract2.png)
4. Чтобы запустить инструмент, щелкните правой кнопкой мыши файл **AccessPanelExtensionDiagnosticTool**, а затем выберите **Открыть с помощью > Microsoft Windows Based Script Host** (Сервер сценариев на базе Microsoft Windows).
   
    ![Открыть с помощью > Сервер сценариев на базе Microsoft Windows](./media/manage-access-panel-browser-extension/open_tool.png)
5. Откроется следующее окно диагностики, описывающее возможные проблемы с вашей установкой.
   
    ![Образец окна диагностики](./media/manage-access-panel-browser-extension/tool_preview.png)
6. Нажмите кнопку**ДА**, чтобы программа могла решить обнаруженные проблемы.
7. Чтобы сохранить эти изменения, закройте все окна Internet Explorer и откройте браузер снова.<br />Если приложения по-прежнему недоступны, попробуйте выполнить описанные ниже действия.

## <a name="check-that-the-access-panel-extension-is-enabled"></a>Убедитесь, что расширение панели доступа включено.
Чтобы убедиться, что расширение панели доступа в Internet Explorer включено, выполните описанные ниже действия.

1. В правом верхнем углу Internet Explorer щелкните **значок шестеренки**. Выберите **Свойства браузера**.<br />(В более ранних версиях Internet Explorer они находятся в разделе **Сервис > Параметры Интернета**).
   
    ![Выберите пункты Сервис > Параметры Интернета.](./media/manage-access-panel-browser-extension/internetoptions.png)
2. Откройте вкладку **Программы** и нажмите кнопку **Настроить надстройки**.
   
    ![Щелкните «Управление надстройками».](./media/manage-access-panel-browser-extension/internetoptions_programs.png)
3. В этом диалоговом окне выберите **Access Panel Extension** (Расширение панели доступа) и нажмите кнопку **Включить**.
   
    ![Щелкните «Включить».](./media/manage-access-panel-browser-extension/enableaddon.png)
4. Чтобы сохранить эти изменения, закройте все окна Internet Explorer и откройте браузер снова.

## <a name="enable-extensions-for-inprivate-browsing"></a>Включение расширений для просмотра InPrivate
Если используется режим просмотра InPrivate, выполните следующие действия.

1. В правом верхнем углу Internet Explorer щелкните **значок шестеренки**. Выберите **Свойства браузера**.<br />(В более ранних версиях Internet Explorer они находятся в разделе **Сервис > Параметры Интернета**).
   
    ![Образец окна диагностики](./media/manage-access-panel-browser-extension/inprivateoptions.png)
2. Откройте вкладку **Конфиденциальность**, **снимите** флажок **Отключать панели инструментов и расширения в режиме InPrivate**.</p>
   
    ![Снимите флажок «Отключить панели инструментов и расширения при запуске просмотра InPrivate».](./media/manage-access-panel-browser-extension/enabletoolbars.png)
3. Чтобы сохранить эти изменения, закройте все окна Internet Explorer и откройте браузер снова.

## <a name="uninstall-the-access-panel-extension"></a>Удаление расширения панели доступа
Чтобы удалить расширение панели доступа с компьютера, выполните следующие действия.

1. Нажмите на клавиатуре **клавишу Windows** , чтобы открыть меню «Пуск». Открыв меню, введите параметры поиска. Введите «Панель управления», а затем откройте **Панель управления** , когда она появится в результатах поиска.
   
    ![Поиск панели управления](./media/manage-access-panel-browser-extension/search_sm.png)
2. В правом верхнем углу панели управления выберите для параметра **Просмотр** значение **Крупные значки**. Затем найдите и нажмите кнопку **Программы и компоненты**.
   
    ![Измените представление для отображения крупных значков.](./media/manage-access-panel-browser-extension/control_panel.png)
3. В списке выберите **Access Panel Extension** (Расширение панели доступа) и нажмите кнопку **Удалить**.
   
    ![Нажмите кнопку удаления.](./media/manage-access-panel-browser-extension/uninstall.png)
4. Затем попробуйте установить расширение еще раз, чтобы проверить, удалось ли решить проблему.

Если при удалении расширения возникнут проблемы, удалите его с помощью инструмента [Microsoft Fix It](https://go.microsoft.com/?linkid=9779673) .

## <a name="related-articles"></a>Связанные статьи
* [Доступ к приложениям и единый вход с помощью Azure Active Directory](what-is-single-sign-on.md)
* [Развертывание расширения панели доступа для Internet Explorer с помощью групповой политики](deploy-access-panel-browser-extension.md)

