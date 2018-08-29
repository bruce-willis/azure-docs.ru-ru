---
title: Развертывание локального репозитория Git в службе приложений Azure
description: Узнайте, как включить локальное развертывание репозитория Git в службе приложений Azure.
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
ms.assetid: ac50a623-c4b8-4dfd-96b2-a09420770063
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/05/2018
ms.author: dariagrigoriu;cephalin
ms.openlocfilehash: f461a9a7cc900ce5f8fdba7b255417b1790d3f4d
ms.sourcegitcommit: 1aedb52f221fb2a6e7ad0b0930b4c74db354a569
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/17/2018
ms.locfileid: "42142716"
---
# <a name="local-git-deployment-to-azure-app-service"></a>Развертывание локального репозитория Git в службе приложений Azure

В этом руководстве показано, как развернуть в [службе приложений Azure](app-service-web-overview.md) свой код из репозитория Git на локальном компьютере.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Предварительные требования

Выполните следующие шаги для изучения данного руководства.

* [Установка Git](http://www.git-scm.com/downloads).
* Обеспечьте локальный репозиторий Git с кодом, который вы хотите развернуть.

Чтобы в дальнейшем использовать пример репозитория, выполните следующую команду в локальном окне терминала.

```bash
git clone https://github.com/Azure-Samples/nodejs-docs-hello-world.git
```

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-from-local-git-with-kudu-builds"></a>Развертывание из локального Git с помощью сборок Kudu

Включить локальное развертывание Git для вашего приложения с сервера сборки Kudu проще всего с помощью облачной среды.

### <a name="create-a-deployment-user"></a>Создание пользователя развертывания

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="enable-local-git-with-kudu"></a>Включить локальный Git с помощью Kudu

Чтобы включить локальное развертывание Git для вашего приложения с сервера сборки Kudu, выполните [`az webapp deployment source config-local-git`](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-local-git) в Cloud Shell.

```azurecli-interactive
az webapp deployment source config-local-git --name <app_name> --resource-group <group_name>
```

Чтобы вместо этого создать приложение с поддержкой Git, выполните команду [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) в Cloud Shell с параметром `--deployment-local-git`.

```azurecli-interactive
az webapp create --name <app_name> --resource-group <group_name> --plan <plan_name> --deployment-local-git
```

Результат команды `az webapp create` должен выглядеть примерно так:

```json
Local git is configured with url of 'https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

### <a name="deploy-your-project"></a>Развертывание проекта

Вернитесь к _окну терминала (в локальном расположении)_ и добавьте удаленное приложение Azure в локальный репозиторий Git. Замените _\<url>_ URL-адресом удаленного репозитория Git, полученным на шаге [Включение использования репозитория Git для приложения](#enable-git-for-you-app).

```bash
git remote add azure <url>
```

Отправьте код в удаленное приложение Azure, чтобы развернуть приложение. При появлении запроса введите пароль, созданный в разделе на шаге [настройки пользователя развертывания](#configure-a-deployment-user) (а не используемый для входа на портал Azure).

```bash
git push azure master
```

В выходных данных могут отображаться автоматизированные операции времени выполнения, например MSBuild для ASP.NET, `npm install` для Node.js и `pip install` для Python. 

Перейдите к своему приложению, чтобы убедиться, что содержимое развернуто.

## <a name="deploy-from-local-git-with-vsts-builds"></a>Развертывание из локального Git с помощью сборок VSTS

> [!NOTE]
> Чтобы служба приложений создавала необходимые сборки и определяла выпуск в учетной записи VSTS, ваша учетная запись Azure должна иметь роль **владельца** в подписке Azure.
>

Чтобы включить локальное развертывание Git для вашего приложения с сервера сборки с помощью Kudu, перейдите в свое приложение на [портале Azure](https://portal.azure.com).

На левой навигационной панели страницы приложения щелкните **Центр развертывания** > **Локальный Git** > **Продолжить**. 

![](media/app-service-deploy-local-git/portal-enable.png)

Щелкните **Непрерывная поставка VSTS** > **Продолжить**.

![](media/app-service-deploy-local-git/vsts-build-server.png)

На странице **Настройка** настройте новую учетную запись VSTS или укажите существующую учетную запись. По завершении нажмите кнопку **Продолжить**.

> [!NOTE]
> Если хотите использовать существующую учетную запись VSTS, которая не указана в списке, необходимо [связать учетную запись VSTS с подпиской Azure](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App).

На странице **Тест** выберите, следует ли включать тесты нагрузки, затем нажмите **Продолжить**.

В зависимости от [ценовой категории](https://azure.microsoft.com/pricing/details/app-service/plans/) вашего плана App Service также можно увидеть страницу **Разворачивание по этапам**. Выберите, где нужно включить слоты развертывания, затем щелкните **Продолжить**.

На странице **Сводка** проверьте параметры и нажмите **Готово**.

Подготовка учетной записи VSTS занимает несколько минут. Когда она будет готова, скопируйте URL-адрес репозитория Git в центре развертывания.

![](media/app-service-deploy-local-git/vsts-repo-ready.png)

Вернитесь к _окну терминала (в локальном расположении)_ и добавьте удаленное приложение Azure в локальный репозиторий Git. Замените _\<url>_ на URL-адрес, полученный на последнем шаге.

```bash
git remote add vsts <url>
```

Отправьте код в удаленное приложение Azure, чтобы развернуть приложение. При запросе диспетчера ввода учетных данных Git войдите с помощью пользователя visualstudio.com. Дополнительные методы проверки подлинности см. в разделе [Обзор проверки подлинности VSTS](/vsts/git/auth-overview?view=vsts).

```bash
git push vsts master
```

После завершения развертывания можно увидеть выполнение сборки в `https://<vsts_account>.visualstudio.com/<project_name>/_build` и выполнение развертывания в `https://<vsts_account>.visualstudio.com/<project_name>/_release`.

Перейдите к своему приложению, чтобы убедиться, что содержимое развернуто.

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="troubleshooting-kudu-deployment"></a>Устранение неполадок при развертывании Kudu

Ниже перечислены распространенные ошибки или проблемы, возникающие при использовании Git для публикации в приложение службы приложений в Azure.

---
**Симптом**: `Unable to access '[siteURL]': Failed to connect to [scmAddress]`

**Причина**: эта ошибка может возникнуть, если приложение не работает.

**Устранение**: запустите приложение на портале Azure. Если веб-приложение остановлено развертывание Git недоступно.

---
**Симптом**: `Couldn't resolve host 'hostname'`

**Причина**: эта ошибка может возникнуть, если при создании удаленного репозитория azure был введен неправильный адрес.

**Решение.** Используйте команду `git remote -v`, чтобы вывести список всех удаленных репозиториев с соответствующими URL-адресами. Проверьте правильность URL-адреса удаленного репозитория "azure". При необходимости удалите и повторно создайте этот удаленный репозиторий, используя правильный URL-адрес.

---
**Симптом**: `No refs in common and none specified; doing nothing. Perhaps you should specify a branch such as 'master'.`

**Причина**: эта ошибка может возникнуть, если при использовании команды `git push` не указана ветвь или если не задано значение `push.default` в `.gitconfig`.

**Решение**: выполните команду `git push` еще раз, указав в главную ветвь. Например: 

```bash
git push azure master
```

---
**Симптом**: `src refspec [branchname] does not match any.`

**Причина**: эта ошибка может возникнуть при попытке принудительно отправить данные в ветвь, отличную от главной, в удаленном репозитории azure.

**Решение**: выполните команду `git push` еще раз, указав в главную ветвь. Например: 

```bash
git push azure master
```

---
**Симптом**: `RPC failed; result=22, HTTP code = 5xx.`

**Причина**: эта ошибка может возникнуть при попытке отправить большой репозиторий Git по протоколу HTTPS.

**Решение**. Измените конфигурацию Git на локальном компьютере, чтобы увеличить значение postBuffer.

```bash
git config --global http.postBuffer 524288000
```

---
**Симптом**: `Error - Changes committed to remote repository but your web app not updated.`

**Причина**: эта ошибка может возникнуть при развертывании приложения Node.js, содержащего файл _package.json_, в котором указаны дополнительные необходимые модули.

**Решение**: до этой ошибки должны быть зарегистрированы дополнительные сообщения, содержащие "npm ERR!", и они могут предоставлять дополнительный контекст для данного сбоя. Ниже перечислены известные причины этой ошибки и соответствующее сообщение npm ERR!. сообщение:

* **Malformed package.json file**: npm ERR! Couldn't read dependencies (не удалось прочитать зависимости).
* **Собственный модуль, не имеющий двоичного дистрибутива для Windows**:

  * `npm ERR! \cmd "/c" "node-gyp rebuild"\ failed with 1`

      Или
  * `npm ERR! [modulename@version] preinstall: \make || gmake\`

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Документация по проекту Kudu](https://github.com/projectkudu/kudu/wiki)
* [Непрерывное развертывание в службе приложений Azure](app-service-continuous-deployment.md)
* [Пример. Создание веб-приложения и развертывание кода из локального репозитория Git (Azure CLI)](./scripts/app-service-cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json)
* [Пример. Создание веб-приложения и развертывание кода из локального репозитория Git (PowerShell)](./scripts/app-service-powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json)
