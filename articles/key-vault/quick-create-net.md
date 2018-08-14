---
title: Краткое руководство Azure. Настройка веб-приложения Azure для настройки и получения секрета из Key Vault | Документация Майкрософт
description: Краткое руководство по настройке веб-приложения ASP.NET Core для настройки и получения секрета из Key Vault
services: key-vault
author: prashanthyv
manager: sumedhb
ms.service: key-vault
ms.topic: quickstart
ms.date: 07/24/2018
ms.author: barclayn
ms.custom: mvc
ms.openlocfilehash: 8b5624ae3083d92213b4ee919dc0860bf5ff4ab7
ms.sourcegitcommit: fc5555a0250e3ef4914b077e017d30185b4a27e6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/03/2018
ms.locfileid: "39480208"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-a-net-web-app"></a>Краткое руководство. Настройка и получение секрета из Azure Key Vault с помощью веб-приложения .NET

В этом кратком руководстве описано, как в веб-приложении Azure настроить чтение данных из хранилища ключей с помощью управляемых удостоверений службы. Вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * создание Key Vault;
> * сохранение секрета в Key Vault;
> * получение секрета из Key Vault;
> * создание веб-приложения Azure;
> * [включение удостоверений управляемой службы;](../active-directory/managed-service-identity/overview.md)
> * предоставление разрешений, необходимых веб-приложению для чтения данных из Key Vault.

Прежде чем продолжить, прочитайте [Базовые концепции](key-vault-whatis.md#basic-concepts), особенно [Управляемое удостоверение службы](../active-directory/managed-service-identity/overview.md).

## <a name="prerequisites"></a>Предварительные требования

* Действия для ОС Windows.
  * [Visual Studio 2017 версии 15.7.3 или более поздней](https://www.microsoft.com/net/download/windows) со следующими рабочими нагрузками:
    * ASP.NET и веб-разработка.
    * Кроссплатформенная разработка .NET Core.
  * [Пакет SDK для .NET Core 2.1 или более поздней версии](https://www.microsoft.com/net/download/windows).

* Для компьютеров Mac.
  * https://visualstudio.microsoft.com/vs/mac/

* Для всех платформ.
  * Скачать GIT можно [здесь](https://git-scm.com/downloads).
  * Подписка Azure. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.
  * [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Вам понадобится Azure CLI версии 2.0.4 или более поздней. Он доступен для Windows, Mac и Linux.

## <a name="login-to-azure"></a>Вход в Azure

   Чтобы войти в Azure с помощью CLI, введите следующее:

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Создайте группу ресурсов с помощью команды [az group create](/cli/azure/group#az-group-create). Группа ресурсов Azure является логическим контейнером, в котором происходит развертывание ресурсов Azure и управление ими.

Выберите имя группы ресурсов для заполнителя.
В следующем примере создается группа ресурсов с именем *<YourResourceGroupName>* в расположении *eastus*.

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "East US"
```

Созданная группа ресурсов будет использоваться далее в этой статье.

## <a name="create-an-azure-key-vault"></a>Создание Azure Key Vault

Теперь создайте Key Vault в группе ресурсов, созданной на предыдущем шаге. Введите следующие сведения:

* Имя хранилища: **выберите имя хранилища Vault Name**. Имя хранилища ключей должно быть строкой длиной от 3 до 24 символов, содержащей только цифры (0–9), буквы (a–z, A–Z) и символ "-".
* Имя группы ресурсов: **выберите имя группы ресурсов**.
* Расположение: **восточная часть США**.

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "East US"
```

На этом этапе любые операции в этом хранилище ключей может выполнять только учетная запись Azure.

## <a name="add-a-secret-to-key-vault"></a>Добавление секрета в Key Vault

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
git clone https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart.git
```

## <a name="open-and-edit-the-solution"></a>Открытие и изменение решения

Измените файл program.cs, чтобы запустить образец с конкретным именем Key Vault.

1. Перейдите в папку key-vault-dotnet-core-quickstart.
2. Откройте в Visual Studio 2017 файл key-vault-dotnet-core-quickstart.sln.
3. Откройте файл Program.cs и измените заполнитель <YourKeyVaultName> на имя вашего Key Vault, созданного ранее.

Это решение использует библиотеки NuGet [AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) и [KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault).

## <a name="run-the-app"></a>Запуск приложения

В главном меню Visual Studio 2017 выберите "Отладка > Запуск без отладки". Когда откроется браузер, перейдите в нем к странице About (Сведения). Отображается значение AppSecret.

## <a name="publish-the-web-application-to-azure"></a>Публикация веб-приложения в Azure

Мы публикуем это приложение в Azure, чтобы увидеть его в качестве живого веб-приложения, а также убедиться, что получено значение секрета.

1. В Visual Studio выберите проект **key-vault-dotnet-core-quickstart**.
2. Выберите **Опубликовать**, а затем **Запустить**.
3. Создайте новую **службу приложений** и выберите **Опубликовать**.
4. Измените имя приложения на keyvaultdotnetcorequickstart.
5. Нажмите кнопку **Создать**.

>[!VIDEO https://sec.ch9.ms/ch9/e93d/a6ac417f-2e63-4125-a37a-8f34bf0fe93d/KeyVault_high.mp4]

## <a name="enable-managed-service-identities-msi"></a>Включение удостоверения управляемой службы (MSI)

Azure Key Vault позволяет обеспечить безопасное хранение учетных данных, а также других ключей и секретов, но для их получения коду необходимо пройти аутентификацию в Azure Key Vault. Управляемое удостоверение службы (MSI) упрощает решение этой задачи, предоставляя службам Azure автоматически управляемое удостоверение в Azure Active Directory (Azure AD). Это удостоверение можно использовать для аутентификации в любой службе, которая поддерживает аутентификацию Azure AD, включая Key Vault, не храня какие-либо учетные данные в коде.

1. Вернитесь в окно командной строки Azure.
2. Выполните команду assign-identity, чтобы создать удостоверение для этого приложения.

```azurecli
az webapp identity assign --name "keyvaultdotnetcorequickstart" --resource-group "<YourResourceGroupName>"
```

>[!NOTE]
>Эта команда выполняет те же действия, что и **включение** параметра **Удостоверение управляемой службы** в свойствах веб-приложения на портале.

## <a name="assign-permissions-to-your-application-to-read-secrets-from-key-vault"></a>Назначение разрешений приложению для чтения секретов из Key Vault

Запишите выходные данные при [публикации приложения в Azure] []. Они должны быть представлены в следующем формате.
        
        {
          "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "type": "SystemAssigned"
        }
        
Затем выполните следующую команду, используя имя вашего Key Vault и значение PrincipalId, скопированные выше.

```azurecli

az keyvault set-policy --name '<YourKeyVaultName>' --object-id <PrincipalId> --secret-permissions get

```

## <a name="next-steps"></a>Дополнительная информация

* [Azure Key Vault Home Page](https://azure.microsoft.com/services/key-vault/) (Домашняя страница Azure Key Vault)
* [Документация по хранилищу ключей](https://docs.microsoft.com/azure/key-vault/)
* [Пакет Azure SDK для .NET](https://github.com/Azure/azure-sdk-for-net)
* [Справочник по REST API Azure](https://docs.microsoft.com/rest/api/keyvault/)
