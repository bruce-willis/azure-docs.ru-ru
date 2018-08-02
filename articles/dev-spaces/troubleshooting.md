---
title: Устранение неполадок | Документация Майкрософт
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: article
description: Быстрая разработка в Kubernetes с использованием контейнеров и микрослужб в Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers
manager: douge
ms.openlocfilehash: b2ef450a429b26843cf770a6243c6f4de932de43
ms.sourcegitcommit: 156364c3363f651509a17d1d61cf8480aaf72d1a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/25/2018
ms.locfileid: "39247334"
---
# <a name="troubleshooting-guide"></a>Руководство по устранению неполадок

Это руководство содержит сведения о распространенных проблемах, которые могут возникнуть при использовании Azure Dev Spaces.

## <a name="error-failed-to-create-azure-dev-spaces-controller"></a>Ошибка создания контроллера Azure Dev Spaces

Вы можете увидеть эту ошибку, если что-то пойдет не так с созданием контроллера. Если это временная ошибка, ее можно устранить, удалив контроллер, а затем повторно создав его.

### <a name="try"></a>Попробуйте выполнить следующее.

Чтобы удалить контроллер, используйте интерфейс командной строки Azure Dev Spaces. Это невозможно сделать в Visual Studio или Cloud Shell. Чтобы установить CLI AZDS, сначала установите Azure CLI, а затем выполните эту команду:

```cmd
az aks use-dev-spaces -g <resource group name> -n <cluster name>
```

Чтобы удалить контроллер, выполните эту команду:

```cmd
azds remove -g <resource group name> -n <cluster name>
```

Чтобы повторно создать контроллер, можно использовать CLI или Visual Studio. Следуйте инструкциям в руководствах, если вы ранее не выполняли эти задачи.


## <a name="error-service-cannot-be-started"></a>Ошибка Service cannot be started (Запуск службы невозможен).

Эта ошибка может возникать, когда не удается запустить код службы. Чаще всего причина в пользовательском коде. Чтобы получить дополнительные диагностические сведения, внесите следующие изменения в команды и параметры:

### <a name="try"></a>Попробуйте выполнить следующее.

В командной строке:

При использовании _azds.exe_ используйте параметр командной строки --verbose и параметр командной строки --output, чтобы указать формат выходных данных.
 
    ```cmd
    azds up --verbose --output json
    ```

В Visual Studio:

1. Откройте меню **Сервис > Параметры** и в разделе **Проекты и решения** выберите **Сборка и запуск**.
2. Измените значение параметра **Степень подробности сообщений при сборке проекта MSBuild** на **Подробно** или **Диагностика**.

    ![Снимок экрана диалогового окна "Сервис > Параметры"](media/common/VerbositySetting.PNG)

## <a name="error-required-tools-and-configurations-are-missing"></a>Ошибка Required tools and configurations are missing (Отсутствуют необходимые средства и конфигурации)

Эта ошибка может возникать при запуске VS Code: "[Azure Dev Spaces] Отсутствуют необходимые средства и конфигурации для сборки и отладки "[имя проекта]"".
Ошибка означает, что это средство azds.exe не находится в переменной среды PATH, как показано в VS Code.

### <a name="try"></a>Попробуйте выполнить следующее.

Запустите VS Code из командной строки, где переменная среды PATH задана должным образом.

## <a name="error-azds-is-not-recognized-as-an-internal-or-external-command-operable-program-or-batch-file"></a>Ошибка azds is not recognized as an internal or external command, operable program, or batch file (azds не распознано как внутренняя или внешняя команда, исполняемая программа или пакетный файл)
 
Эта ошибка может возникать, если средство azds.exe неправильно установлено или настроено.

### <a name="try"></a>Попробуйте выполнить следующее.

1. Попробуйте найти azds.exe в расположении %ProgramFiles%/Microsoft SDKs\Azure\Azure Dev Spaces CLI (Preview). Если файл есть в этой папке, добавьте это расположение в переменную среды PATH.
2. Если средство azds.exe не установлено, выполните следующую команду:

    ```cmd
    az aks use-dev-spaces -n <cluster-name> -g <resource-group>
    ```

## <a name="error-upstream-connect-error-or-disconnectreset-before-headers"></a>Ошибка "upstream connect error or disconnect/reset before headers" (ошибка вышестоящего подключения или отключение либо сброс до прохождения заголовков)
Вы можете увидеть эту ошибку при попытке получить доступ к службе. Например, при переходе по URL-адресу службы в браузере. 

### <a name="reason"></a>Причина 
Порт контейнера недоступен. Эта проблема может возникать по следующим причинам: 
* Процесс сборки или развертывания контейнера еще не завершен. Это может случиться, если вы запускаете `azds up` или отладчик, а затем пытаетесь получить доступ к контейнеру до его успешного развертывания.
* Конфигурация порта не согласуется в _Dockerfile_, диаграмме Helm и серверном коде, который открывает порт.

### <a name="try"></a>Попробуйте выполнить следующее.
1. Если процесс сборки или развертывания еще не завершен, вы можете подождать 2–3 секунды и повторить попытку доступа к службе. 
1. Проверьте конфигурацию порта. Указанные номера портов должны **совпадать** во всех следующих ресурсах:
    * **Dockerfile:** задается инструкцией `EXPOSE`.
    * **[Диаграмма Helm](https://docs.helm.sh):** задается значениями `externalPort` и `internalPort` для службы (часто находится в файле `values.yml`).
    * Все порты, открытые в коде приложения, например в Node.js: `var server = app.listen(80, function () {...}`


## <a name="config-file-not-found"></a>Файл конфигурации не найден
Вы запускаете `azds up`, и появляется следующая ошибка: `Config file not found: .../azds.yaml`

### <a name="reason"></a>Причина
Необходимо запустить `azds up` из корневого каталога кода, который вы хотите выполнить, и инициализировать папку с кодом для запуска с Azure Dev Spaces.

### <a name="try"></a>Попробуйте выполнить следующее.
1. Измените текущий каталог на корневую папку, содержащую код службы. 
1. Если у вас нет файла _azds.yaml_ в папке кода, запустите `azds prep` для создания ресурсов Docker, Kubernetes и Azure Dev Spaces.

## <a name="error-the-pipe-program-azds-exited-unexpectedly-with-code-126"></a>Ошибка "The pipe program 'azds' exited unexpectedly with code 126" (Программа канала AZDS неожиданно завершила работу с кодом 126)
Запуск отладчика VS Code иногда может привести к этой ошибке. Это известная проблема.

### <a name="try"></a>Попробуйте выполнить следующее.
1. Закройте и снова откройте VS Code.
2. Нажмите клавишу F5 еще раз.


## <a name="debugging-error-configured-debug-type-coreclr-is-not-supported"></a>Ошибка отладки "Configured debug type 'coreclr' is not supported" (Настроенный тип отладки "coreclr" не поддерживается)
При запуске отладчика VS Code возникает ошибка: `Configured debug type 'coreclr' is not supported.`

### <a name="reason"></a>Причина
На компьютере, на котором ведется разработка, не установлено расширения VS Code для Azure Dev Spaces.

### <a name="try"></a>Попробуйте выполнить следующее.
Установите [расширение VS Code для Azure Dev Spaces](get-started-netcore.md).

## <a name="the-type-or-namespace-name-mylibrary-could-not-be-found"></a>Не удалось найти имя типа или пространства имен "MyLibrary"

### <a name="reason"></a>Причина 
Контекст сборки находится на уровне проекта или службы по умолчанию, поэтому используемый проект библиотеки невозможно найти.

### <a name="try"></a>Попробуйте выполнить следующее.
Что необходимо сделать:
1. В файле _azds.yaml_ задайте для контекста сборки уровень решения.
2. Измените файлы _Dockerfile_ и _Dockerfile.develop_ для корректного обращения к _CSPROJ_-файлам проекта в зависимости от нового контекста сборки.
3. Поместите _DOCKERIGNORE_-файл рядом с SLN-файлом и внесите необходимые изменения.

Вы можете найти пример по ссылке https://github.com/sgreenmsft/buildcontextsample.

## <a name="microsoftdevspacesregisteraction-authorization-error"></a>Ошибка авторизации "Microsoft.DevSpaces/register/action"
Вы можете увидеть следующую ошибку при управлении Azure Dev Space, когда вы работаете в подписке Azure, для которой у вас нет доступа с правами владельца или участника.
`The client '<User email/Id>' with object id '<Guid>' does not have authorization to perform action 'Microsoft.DevSpaces/register/action' over scope '/subscriptions/<Subscription Id>'.`

### <a name="reason"></a>Причина
Выбранная подписка Azure не зарегистрировала пространство имен `Microsoft.DevSpaces`.

### <a name="try"></a>Попробуйте выполнить следующее.
Пользователь с правами владельца или участника в подписке Azure может запустить следующую команду Azure CLI для регистрации пространства имен `Microsoft.DevSpaces` вручную:

```cmd
az provider register --namespace Microsoft.DevSpaces
```

## <a name="azure-dev-spaces-doesnt-seem-to-use-my-existing-dockerfile-to-build-a-container"></a>Azure Dev Spaces не использует существующий файл Dockerfile для сборки контейнера 

### <a name="reason"></a>Причина
Настройте службу Azure Dev Spaces так, чтобы она указывала на конкретный файл _Dockerfile_ в вашем проекте. Если Azure Dev Spaces не использует файл _Dockerfile_, который вы хотели использовать для сборки контейнеров, то можно явно указать Azure Dev Spaces, где он находится. 

### <a name="try"></a>Попробуйте выполнить следующее.
Откройте файл _azds.yaml_, созданный Azure Dev Spaces в проекте. Используйте директиву `configurations->develop->build->dockerfile` для указания файла Dockerfile, который вы хотите использовать:

```
...
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
```