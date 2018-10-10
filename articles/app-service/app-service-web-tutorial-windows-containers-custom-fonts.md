---
title: Перенос приложения ASP.NET в Службу приложений Azure с помощью контейнера Windows (предварительная версия) | Документация Майкрософт
description: Сведения о том, как развернуть пользовательский контейнер Windows в службе приложений Azure.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: jeconnoc
editor: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 09/17/2018
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 8f6268a345a861ae65a10c3220d1992ba2d45928
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46980344"
---
# <a name="migrate-an-aspnet-app-to-azure-app-service-using-a-windows-container-preview"></a>Перенос приложения ASP.NET в Службу приложений Azure с помощью контейнера Windows (предварительная версия)

[Служба приложений Azure](app-service-web-overview.md) предоставляет предопределенные стеки приложений на платформе Windows, например ASP.NET или Node.js, выполняющиеся в IIS. Предварительно настроенная среда Windows блокирует в операционной системе возможность административного доступа, установки программного обеспечения, изменений в глобальном кэше сборок и т. д. (см. раздел [Функциональные возможности операционной системы для службы приложений Azure](web-sites-available-operating-system-functionality.md)). Тем не менее с помощью пользовательского контейнера Windows в службе приложений можно вносить в ОС изменения, необходимые для вашего приложения. Поэтому легко перенести локальное приложение, которому требуется пользовательская ОС и конфигурация программного обеспечения. В этом руководстве показано, как перенести в службу приложений приложение ASP.NET, которое использует пользовательские шрифты, установленные в библиотеке шрифтов Windows. Вы развернете пользовательский образ Windows из Visual Studio в [Реестр контейнеров Azure](https://docs.microsoft.com/azure/container-registry/), а затем запустите его в службе приложений.

![](media/app-service-web-tutorial-windows-containers-custom-fonts/app-running.png)

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим руководством:

- <a href="https://hub.docker.com/" target="_blank">Зарегистрируйте учетную запись центра Docker</a>.
- <a href="https://docs.docker.com/docker-for-windows/install/" target="_blank">Установите Docker для ОС Windows</a>.
- <a href="https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10#2-switch-to-windows-containers" target="_blank">Переключите Docker для запуска контейнеров Windows</a>.
- <a href="https://www.visualstudio.com/downloads/" target="_blank">Установите Visual Studio 2017</a>, а также следующие рабочие нагрузки: **ASP.NET и веб-разработка** и **разработка Azure**. Если у вас уже установлена версия Visual Studio 2017, сделайте следующее.
    - Установите последние обновления для Visual Studio, выбрав **Справка** > **Проверить наличие обновлений**.
    - Добавьте рабочие нагрузки в Visual Studio, выбрав **Инструменты** > **Get Tools and Features** (Получить инструменты и компоненты).

## <a name="set-up-the-app-locally"></a>Настройка приложения в локальной среде

### <a name="download-the-sample"></a>Скачивание примера приложения

На этом шаге вы настроите локальный проект .NET.

- [Загрузите пример проекта](https://github.com/Azure-Samples/custom-font-win-container/archive/master.zip).
- Извлеките (распакуйте) содержимое файла *custom-font-win-container.zip*.

Пример проекта содержит простое приложение ASP.NET, которое использует пользовательский шрифт, устанавливаемый в библиотеку шрифтов Windows. Устанавливать шрифты не обязательно, но это пример приложения, интегрированного с базовой ОС. Чтобы перенести такое приложение в службу приложений, переработайте код, чтобы удалить интеграцию, либо перенесите его "как есть" в пользовательском контейнере Windows.

### <a name="install-the-font"></a>Установка шрифта

В проводнике Windows перейдите в папку _custom-font-win контейнер master/CustomFontSample_, щелкните правой кнопкой мыши файл _FrederickatheGreat Regular.ttf_ и выберите **Установить**.

Этот шрифт находится в свободном доступе на веб-сайте [Google Fonts](https://fonts.google.com/specimen/Fredericka+the+Great).

### <a name="run-the-app"></a>Запуск приложения

Откройте файл *custom-font-win-container/CustomFontSample.sln* в Visual Studio. 

Введите `Ctrl+F5`, чтобы запустить приложение без отладки. Это приложение откроется в браузере по умолчанию. 

![Диалоговое окно "Новый проект ASP.NET"](media/app-service-web-tutorial-windows-containers-custom-fonts/local-app-in-browser.png)

Так как приложение использует установленный шрифт, оно не может работать в песочнице службы приложений. Тем не менее его можно развернуть с помощью контейнера Windows, так как шрифт можно установить в контейнер Windows.

### <a name="configure-windows-container"></a>Настройка контейнера Windows

В обозревателе решений щелкните правой кнопкой мыши проект **CustomFontSample** и выберите **Добавить** > **Container Orchestration Support** (Поддержка оркестрации контейнеров).

![Диалоговое окно "Новый проект ASP.NET"](media/app-service-web-tutorial-windows-containers-custom-fonts/enable-container-orchestration.png)

Выберите **Docker Compose** > **ОК**.

Теперь проект настроен для запуска в контейнере Windows. Объект _Dockerfile_ добавлен в проект **CustomFontSample**, а проект **docker-compose** добавлен в решение. 

В обозревателе решений откройте **Dockerfile**.

Необходимо использовать [поддерживаемый родительский образ](app-service-web-get-started-windows-container.md#use-a-different-parent-image). Измените родительский образ, заменив строку `FROM` приведенным ниже кодом.

```Dockerfile
FROM microsoft/aspnet:4.7.1
```

В конце файла добавьте приведенную ниже строку, после чего сохраните файл.

```Dockerfile
RUN ${source:-obj/Docker/publish/InstallFont.ps1}
```

Файл _InstallFont.ps1_ можно найти в проекте **CustomFontSample**. Это простой сценарий, который устанавливает шрифт. Более сложную версию этого сценария можно найти в [Центре сценариев](https://gallery.technet.microsoft.com/scriptcenter/fb742f92-e594-4d0c-8b79-27564c575133).

## <a name="publish-to-azure-container-registry"></a>Публикация в Реестре контейнеров Azure

В [Реестре контейнеров Azure](https://docs.microsoft.com/azure/container-registry/) можно хранить образы для развертывания контейнеров. Можно настроить службу приложений для использования образов, размещенных в Реестре контейнеров Azure.

### <a name="open-publish-wizard"></a>Открытие мастера публикации

Щелкните правой кнопкой мыши проект **CustomFontSample** в обозревателе решений и выберите **Опубликовать**.

![Диалоговое окно "Новый проект ASP.NET"](media/app-service-web-tutorial-windows-containers-custom-fonts/open-publish-wizard.png)

### <a name="create-registry-and-publish"></a>Создание реестра и публикация

В мастере публикации выберите **Реестр контейнеров** > **Создать реестр контейнеров Azure** > **Опубликовать**.

![Диалоговое окно "Новый проект ASP.NET"](media/app-service-web-tutorial-windows-containers-custom-fonts/create-registry.png)

### <a name="sign-in-with-azure-account"></a>Вход с учетной записью Azure

В диалоговом окне **Создать реестр контейнеров Azure** выберите **Добавить учетную запись** и выполните вход в подписку Azure. Если вы уже вошли в систему, выберите учетную запись, содержащую необходимую подписку, в раскрывающемся меню.

![Вход в Azure](./media/app-service-web-tutorial-windows-containers-custom-fonts/add-an-account.png)

### <a name="configure-the-registry"></a>Настройка реестра

Настройте новый реестр контейнеров в соответствии с предлагаемыми значениями в следующей таблице. По завершении нажмите кнопку **Создать**.

| Параметр  | Рекомендуемое значение | Дополнительные сведения |
| ----------------- | ------------ | ----|
|**DNS-префикс**| Оставьте имя созданного реестра или измените его, указав другое уникальное имя. |  |
|**Группа ресурсов**| Щелкните **Создать**, введите имя **myResourceGroup** и нажмите кнопку **ОК**. |  |
|**SKU**| базовая; | [Ценовые категории](https://azure.microsoft.com/pricing/details/container-registry/)|
|**Расположение реестра**| Западная Европа | |

![Настройка Реестра контейнеров Azure](./media/app-service-web-tutorial-windows-containers-custom-fonts/configure-registry.png)

Откроется окно терминала, в котором отображается ход развертывания образа. Дождитесь завершения развертывания.

## <a name="sign-in-to-azure"></a>Вход в Azure

Войдите на портал Azure по адресу https://portal.azure.com.

## <a name="create-a-web-app"></a>Создание веб-приложения

В меню слева выберите **Создать ресурс** > **Веб** > **Веб-приложение для контейнеров**.

### <a name="configure-the-new-web-app"></a>Настройка нового веб-приложения

В пользовательском интерфейсе создания настройте параметры в соответствии со следующей таблицей.

| Параметр  | Рекомендуемое значение | Дополнительные сведения |
| ----------------- | ------------ | ----|
|**Имя приложения**| Введите уникальное имя. | URL-адрес веб-приложения: `http://<app_name>.azurewebsites.net`, где `<app_name>` — имя приложения. |
|**Группа ресурсов**| Щелкните **Использовать существующую** и введите **myResourceGroup**. |  |
|**ОС**| Windows (предварительная версия) | |

### <a name="configure-app-service-plan"></a>Настройка плана службы приложений

Щелкните **Расположение или план службы приложений** > **Создать**. Укажите имя нового плана, выберите для него расположение **Западная Европа** и нажмите кнопку **ОК**.

![](media/app-service-web-tutorial-windows-containers-custom-fonts/configure-app-service-plan.png)

### <a name="configure-container"></a>Настройка контейнера

Выберите **Настроить контейнер** > **Реестр контейнеров Azure**. Выберите реестр, образ и тег, созданные ранее при [публикации в Реестре контейнеров Azure](#publish-to-azure-container-registry), затем нажмите кнопку **ОК**.

![](media/app-service-web-tutorial-windows-containers-custom-fonts/configure-app-container.png)

### <a name="complete-app-creation"></a>Завершение создания приложения

Нажмите кнопку **Создать** и подождите, пока Azure создаст необходимые ресурсы.

## <a name="browse-to-the-web-app"></a>Переход к веб-приложению

По завершении операции Azure отображается окно уведомления.

![](media/app-service-web-tutorial-windows-containers-custom-fonts/portal-create-finished.png)

1. Щелкните **Перейти к ресурсу**.

2. На странице приложения щелкните ссылку в разделе **URL-адрес**.

В браузере откроется следующая страница:

![](media/app-service-web-tutorial-windows-containers-custom-fonts/app-starting.png)

Подождите несколько минут и попробуйте еще раз, пока не откроется домашняя страница с тем самым красивым шрифтом.

![](media/app-service-web-tutorial-windows-containers-custom-fonts/app-running.png)

**Поздравляем!** Вы перенесли приложение ASP.NET в Службу приложений Azure в контейнере Windows.

## <a name="see-container-start-up-logs"></a>Просмотр журналов запуска контейнера

Загрузка контейнера Windows может занять некоторое время. Чтобы просмотреть ход выполнения, перейдите по следующему URL-адресу, указав вместо *\<app_name >* имя приложения.
```
https://<app_name>.scm.azurewebsites.net/api/logstream
```

Потоковые журналы выглядят следующим образом:

```
14/09/2018 23:16:19.889 INFO - Site: fonts-win-container - Creating container for image: customfontsample20180914115836.azurecr.io/customfontsample:latest.
14/09/2018 23:16:19.928 INFO - Site: fonts-win-container - Create container for image: customfontsample20180914115836.azurecr.io/customfontsample:latest succeeded. Container Id 329ecfedbe370f1d99857da7352a7633366b878607994ff1334461e44e6f5418
14/09/2018 23:17:23.405 INFO - Site: fonts-win-container - Start container succeeded. Container: 329ecfedbe370f1d99857da7352a7633366b878607994ff1334461e44e6f5418
14/09/2018 23:17:28.637 INFO - Site: fonts-win-container - Container ready
14/09/2018 23:17:28.637 INFO - Site: fonts-win-container - Configuring container
14/09/2018 23:18:03.823 INFO - Site: fonts-win-container - Container ready
14/09/2018 23:18:03.823 INFO - Site: fonts-win-container - Container start-up and configuration completed successfully
```

