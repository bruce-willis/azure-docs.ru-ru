---
title: Краткое руководство. Настройка и получение секрета из Azure Key Vault с помощью веб-приложения Node | Документация Майкрософт
description: Краткое руководство. Настройка и получение секрета из Azure Key Vault с помощью веб-приложения Node
services: key-vault
documentationcenter: ''
author: prashanthyv
manager: sumedhb
ms.service: key-vault
ms.workload: identity
ms.topic: quickstart
ms.date: 08/08/2018
ms.author: barclayn
ms.custom: mvc
ms.openlocfilehash: 4592b256dfda75e81a94034545cd54dbf0d71532
ms.sourcegitcommit: 0fcd6e1d03e1df505cf6cb9e6069dc674e1de0be
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/14/2018
ms.locfileid: "42022546"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-a-node-web-app"></a>Краткое руководство. Настройка и получение секрета из Azure Key Vault с помощью веб-приложения Node 

В этом кратком руководстве описывается, как хранить секрет в Key Vault и как его получать с помощью веб-приложения. Чтобы просмотреть значение секрета, необходимо выполнить это действие в Azure. Это краткое руководство использует Node.js и Управляемые удостоверения служб (MSI)

> [!div class="checklist"]
> * создание Key Vault;
> * сохранение секрета в Key Vault;
> * получение секрета из Key Vault;
> * создание веб-приложения Azure;
> * [включение удостоверений управляемой службы;](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview)
> * предоставление разрешений, необходимых веб-приложению для чтения данных из Key Vault.

Прежде чем продолжить, убедитесь, что изучены [основные понятия](key-vault-whatis.md#basic-concepts).

>[!NOTE]
Чтобы понять, почему в этой статье приводятся именно такие рекомендации, необходимо знать несколько основных понятий. Key Vault — это центральный репозиторий для хранения секретов программным способом. Но, чтобы воспользоваться возможностями Key Vault, приложения или пользователи должны сначала пройти в нем аутентификацию, т. е. предоставить секрет. Следуя рекомендациям по безопасности, первый секрет должен также периодически меняться. Но благодаря [Управляемому удостоверению службы](../active-directory/managed-service-identity/overview.md) приложения, работающие в Azure, получают удостоверение, которое автоматически управляется службой Azure. Это помогает устранить **проблему введения секрета**, чтобы пользователи и приложения могли следовать рекомендациям и не беспокоиться об изменении первого секрета.

## <a name="prerequisites"></a>Предварительные требования

* [Node.js](https://nodejs.org/en/)
* [Git](https://www.git-scm.com/)
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 2.0.4 или более поздней версии
* Подписка Azure. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="login-to-azure"></a>Вход в Azure

Чтобы войти в Azure с помощью CLI, введите следующее.

```azurecli
az login
```

## <a name="create-resource-group"></a>Создать группу ресурсов

Создайте группу ресурсов с помощью команды [az group create](/cli/azure/group#az-group-create). Группа ресурсов Azure является логическим контейнером, в котором происходит развертывание ресурсов Azure и управление ими.

Выберите имя группы ресурсов для заполнителя.
В следующем примере создается группа ресурсов с именем *<YourResourceGroupName>* в расположении *eastus*.

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "East US"
```

Созданная вами группа ресурсов будет использоваться далее в этом руководстве.

## <a name="create-an-azure-key-vault"></a>Создание Azure Key Vault

Теперь создайте Key Vault в группе ресурсов, созданной на предыдущем шаге. Хотя в этом руководстве для Key Vault используется имя ContosoKeyVault, вам необходимо использовать уникальное имя. Введите следующие сведения:

* Имя хранилища: **Выберите имя Key Vault**.
* Имя группы ресурсов: **Выберите имя группы ресурсов**.
* Расположение: **восточная часть США**.

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "East US"
```

На этом этапе любые операции в этом хранилище ключей может выполнять только учетная запись Azure.

## <a name="add-a-secret-to-key-vault"></a>Добавление секрета в хранилище ключей

Теперь мы добавим секрет, чтобы продемонстрировать этот процесс. Вы может хранить здесь строки подключения SQL и прочие сведения, которые должны храниться безопасно, но быть доступны для приложения. В этом руководстве мы сохраним пароль с именем **AppSecret** и значением **MySecret**.

Введите указанные ниже команды, чтобы создать в Key Vault секрет с именем **AppSecret** и значением **MySecret**.

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

Чтобы просмотреть содержащееся в секрете значение в виде обычного текста, введите следующее:

```azurecli
az keyvault secret show --name "AppSecret" --vault-name "<YourKeyVaultName>"
```

Эта команда отображает секретные сведения, включая URI. Выполнив эти действия, вы сохраните в Azure Key Vault секрет со значением URI. Запишите эти сведения. Они потребуются вам на следующих шагах.

## <a name="clone-the-repo"></a>Клонирование репозитория

Чтобы сделать локальную копию для изменения источника, клонируйте репозиторий, выполнив следующую команду.

```
git clone https://github.com/Azure-Samples/key-vault-node-quickstart.git
```

## <a name="install-dependencies"></a>Установка зависимостей

Здесь устанавливаются зависимости. Выполните следующие команды cd key-vault-node-quickstart и npm install

В этом проекте используются 2 узла.

* [ms-rest-azure](https://www.npmjs.com/package/ms-rest-azure) 
* [azure-keyvault](https://www.npmjs.com/package/azure-keyvault)

## <a name="publish-the-web-application-to-azure"></a>Публикация веб-приложения в Azure

Ниже приведены несколько шагов, которые необходимо выполнить.

- Первый шаг — создание плана [Службы приложений Azure](https://azure.microsoft.com/services/app-service/). В этом плане можно хранить несколько веб-приложений.

    ```
    az appservice plan create --name myAppServicePlan --resource-group myResourceGroup
    ```
- Затем создается веб-приложение. В следующем примере замените <app_name> глобальным уникальным именем приложения (допустимые символы: a-z, 0-9 и -). Для среды выполнения установлено значение NODE|6.9. Чтобы просмотреть все поддерживаемые среды выполнения, выполните команду az webapp list-runtimes
    ```
    # Bash
    az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name> --runtime "NODE|6.9" --deployment-local-git
    ```
    Когда веб-приложение будет создано, в Azure CLI отобразится примерно следующее:
    ```
    {
      "availabilityState": "Normal",
      "clientAffinityEnabled": true,
      "clientCertEnabled": false,
      "cloningInfo": null,
      "containerSize": 0,
      "dailyMemoryTimeQuota": 0,
      "defaultHostName": "<app_name>.azurewebsites.net",
      "enabled": true,
      "deploymentLocalGitUrl": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git"
      < JSON data removed for brevity. >
    }
    ```
    Перейдите к только что созданным веб-приложениям, и увидите действующее веб-приложение. Замените <app_name> уникальным именем приложения.

    ```
    http://<app name>.azurewebsites.net
    ```
    Приведенная выше команда также создает приложение с поддержкой Git, которое позволяет развертывать Azure с локального репозитория Git. 
    Локальный репозиторий Git настроен с URL-адресом 'https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git'

- Создание пользователя развертывания. После завершения предыдущей команды можно добавить удаленную службу приложений Azure в локальный репозиторий Git. Замените <url> на URL-адрес удаленного репозитория Git, который был получен при включении использования репозитория Git для приложения.

    ```
    git remote add azure <url>
    ```

## <a name="enable-managed-service-identity"></a>Включение удостоверения управляемой службы

Azure Key Vault позволяет обеспечить безопасное хранение учетных данных, а также других ключей и секретов, но для их получения код должен выполнять аутентификацию в Key Vault. Управляемое удостоверение службы (MSI) упрощает решение этой задачи, предоставляя службам Azure автоматически управляемое удостоверение в Azure Active Directory (Azure AD). Это удостоверение можно использовать для аутентификации в любой службе, которая поддерживает аутентификацию Azure AD, включая Key Vault, не храня какие-либо учетные данные в коде.

Выполните команду assign-identity, чтобы создать удостоверение для этого приложения.

```azurecli
az webapp identity assign --name <app_name> --resource-group "<YourResourceGroupName>"
```

Эта команда выполняет те же действия, что и **включение** параметра **Удостоверение управляемой службы** в свойствах веб-приложения на портале.

### <a name="assign-permissions-to-your-application-to-read-secrets-from-key-vault"></a>Назначение разрешений приложению для чтения секретов из Key Vault

Запишите или скопируйте выходные данные команды, приведенной выше. Они должны быть представлены в следующем формате.
        
        {
          "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "type": "SystemAssigned"
        }
        
Затем выполните следующую команду, используя имя вашего Key Vault и значение PrincipalId, скопированные выше.

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <PrincipalId> --secret-permissions get
```

## <a name="deploy-the-node-app-to-azure-and-retrieve-the-secret-value"></a>Развертывание приложения Node.js в Azure и получение секретного значения

Теперь все установлено. Чтобы развернуть приложение в Azure, выполните следующую команду.

```
git push azure master
```

После этого при просмотре https://<app_name>.azurewebsites.net отобразится секретное значение.
Убедитесь, что вы заменили имя <YourKeyVaultName> именем своего хранилища

## <a name="next-steps"></a>Дополнительная информация

* [Azure Key Vault Home Page](https://azure.microsoft.com/services/key-vault/) (Домашняя страница Azure Key Vault)
* [Документация по хранилищу ключей](https://docs.microsoft.com/azure/key-vault/)
* [Пакет SDK Azure для Node.js](https://docs.microsoft.com/javascript/api/overview/azure/key-vault)
* [Справочник по REST API Azure](https://docs.microsoft.com/rest/api/keyvault/)
