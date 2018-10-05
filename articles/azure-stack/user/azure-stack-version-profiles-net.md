---
title: Использование профилей версий API и SDK .NET в Azure Stack | Документация Майкрософт
description: Сведения об использовании профилей версий API и .NET в Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2018
ms.author: sethm
ms.reviewer: sijuman
ms.openlocfilehash: 35329468ee01d5b70d654c1eb4a908db9d3fcb5d
ms.sourcegitcommit: 5b8d9dc7c50a26d8f085a10c7281683ea2da9c10
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/26/2018
ms.locfileid: "47184405"
---
# <a name="use-api-version-profiles-with-net-in-azure-stack"></a>Использование профилей версий API и .NET в Azure Stack

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

Пакет SDK .NET для Azure Stack Resource Manager предоставляет средства для создания и администрирования инфраструктуры. В этом пакете SDK представлены поставщики ресурсов вычислений, сети, хранилища, служб приложений и [KeyVault](../../key-vault/key-vault-whatis.md). Пакет SDK для .NET включает 14 пакетов NuGet, которые необходимо скачивать в каждое решение проекта, где используются сведения о профилях. Но вы можете явным образом указать поставщик ресурсов, который будет использоваться для профиля 2018-03-01-hybrid или 2017-03-09-profile, чтобы оптимизировать использование памяти в приложении. Каждый пакет содержит поставщик ресурсов, соответствующую версию API и профиль API, к которой она принадлежит. Профили API в пакете SDK .NET упрощают разработку гибридных облачных приложений, помогая переключаться между глобальными ресурсами Azure и ресурсами в Azure Stack.

## <a name="net-and-api-version-profiles"></a>Профили версии API и .NET

Профиль API определяет поставщик ресурсов и версии API. С помощью профиля API можно получить последнюю и наиболее стабильную версию ресурса любого типа из представленных в пакете поставщика ресурсов.

-   Чтобы получить последние версии всех служб, используйте профиль **latest** в пакете. Этот профиль входит в пакет NuGet **Microsoft.Azure.Management**.

-   Чтобы использовать службы, совместимые с Azure Stack, используйте пакеты **Microsoft.Azure.Management.Profiles.hybrid\_2018\_03\_01.*ResourceProvider*.0.9.0-preview.nupkg** или **Microsoft.Azure.Management.Profiles.hybrid\_2017\_03\_09.*ResourceProvider*.0.9.0-preview.nupkg**.

    -   Для каждого поставщика ресурсов в каждом профиле существует два пакета.

    -   Сегмент **ResourceProvider** в имени пакета NuGet всегда должен соответствовать выбранному поставщику.

-   Чтобы использовать последнюю версию API службы, используйте профиль **latest** определенного пакета NuGet. Например, если вы хотите использовать версию **latest-API** службы вычислений, выберите профиль **latest** пакета **compute**. Профиль **latest** входит в пакет NuGet **Microsoft.Azure.Management**.

-   Чтобы использовать конкретные версии API для некоторого типа ресурса от конкретного поставщика, выберите соответствующую версию API в пакете.

Обратите внимание, что все описанные выше варианты можно сочетать в одном приложении.

## <a name="install-the-azure-net-sdk"></a>Установка пакета SDK .NET для Azure

1.  Установите Git. Инструкции см. в разделе по [Getting Started - Installing Git][].

2.  Чтобы установить правильные пакеты NuGet, воспользуйтесь [Finding and installing a package][].

3.  Набор устанавливаемых пакетов зависит от версии профиля, который вам нужен. Имена пакетов для версий профилей:

    1.  **Microsoft.Azure.Management.Profiles.hybrid\_2018\_03\_01.*ResourceProvider*.0.9.0-preview.nupkg**

    2.  **Microsoft.Azure.Management.Profiles.hybrid\_2017\_03\_09.*ResourceProvider*.0.9.0-preview.nupkg**

4.  Чтобы установить правильные пакеты NuGet для Visual Studio Code, скачайте [NuGet Package Manager instructions][].

5.  Создайте подписку, если ее еще нет, и сохраните ее идентификатор для дальнейшего использования. Инструкции по созданию подписки см. в статье [Create subscriptions to offers in Azure Stack][].

6.  Создайте субъект-службу и сохраните идентификатор клиента и секрет клиента. Инструкции по созданию субъекта-службы для Azure Stack см. в статье [Provide applications access to Azure Stack][]. Обратите внимание, что идентификатор клиента при создании субъекта-службы называется идентификатором приложения.

7.  Убедитесь, что субъект-служба имеет роль участника или владельца в вашей подписке. Сведения о том, как назначить роль субъекту-службе, см. в статье [Provide applications access to Azure Stack][].

## <a name="prerequisites"></a>Предварительные требования

Чтобы использовать пакет SDK Azure для .NET и Azure Stack, укажите следующие значения и задайте значения для переменных среды. Чтобы настроить переменные среды, воспользуйтесь инструкциями, которые указаны после таблицы для соответствующей операционной системы.

| Значение                     | Переменные среды   | ОПИСАНИЕ                                                                                                             |
|---------------------------|-------------------------|-------------------------------------------------------------------------------------------------------------------------|
| Tenant ID                 | AZURE_TENANT_ID       | Значение [*идентификатора клиента*][] Azure Stack.                                                                          |
| Идентификатор клиента                 | AZURE_CLIENT_ID       | Идентификатор приложения субъекта-службы, сохраненный во время создания субъекта-службы в предыдущем разделе этой статьи. |
| Идентификатор подписки           | AZURE_SUBSCRIPTION_ID | [*Идентификатор подписки*][] для доступа к предложениям в Azure Stack.                                                      |
| Секрет клиента             | AZURE_CLIENT_SECRET   | Секрет приложения субъекта-службы, сохраненный во время создания субъекта-службы.                                      |
| Конечная точка Resource Manager | ARM_ENDPOINT           | См. дополнительные сведения о [*конечной точке Resource Manager для Azure Stack*][].                                                                    |

Чтобы узнать идентификатор клиента для Azure Stack, выполните приведенные [здесь](../azure-stack-csp-ref-operations.md) инструкции. Чтобы настроить переменные среды, сделайте следующее:

### <a name="microsoft-windows"></a>Microsoft Windows

Используйте следующие команды, чтобы настроить переменные среды в командной строке Windows:

```shell
Set Azure_Tenant_ID=Your_Tenant_ID
```

### <a name="macos-linux-and-unix-based-systems"></a>MacOS, Linux и системы на основе Unix

В системах на основе Unix можно использовать такую команду:

```shell
Export Azure_Tenant_ID=Your_Tenant_ID
```

### <a name="the-azure-stack-resource-manager-endpoint"></a>Конечная точка Resource Manager для Azure Stack

Microsoft Azure Resource Manager — это платформа управления, которая позволяет администраторам развертывать, администрировать и отслеживать ресурсы Azure. Azure Resource Manager может обрабатывать эти задачи в рамках одной операции как группы, а не по отдельности.

Вы можете получить метаданные из конечной точки Resource Manager. Конечная точка возвращает JSON-файл со сведениями, необходимыми для запуска кода.

Обратите внимание на следующие моменты:

- В Пакете средств разработки Azure Stack (ASDK) **ResourceManagerUrl**: https://management.local.azurestack.external/.

- **ResourceManagerUrl** в интегрированных системах: `https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/` Для получения необходимых метаданных: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`

Пример JSON-файла:

```json
{ 
   "galleryEndpoint": "https://portal.local.azurestack.external:30015/",
   "graphEndpoint": "https://graph.windows.net/",
   "portal Endpoint": "https://portal.local.azurestack.external/",
   "authentication": 
      {
      "loginEndpoint": "https://login.windows.net/",
      "audiences": ["https://management.yourtenant.onmicrosoft.com/3cc5febd-e4b7-4a85-a2ed-1d730e2f5928"]
      }
}
```

## <a name="existing-api-profiles"></a>Существующие профили API

1.  **Microsoft.Azure.Management.Profiles.hybrid\_2018\_03\_01.*ResourceProvider*.0.9.0-preview.nupkg** — актуальный профиль для Azure Stack. Используйте этот профиль для служб, которым нужна максимальная совместимость с Azure Stack с меткой 1808 или более новой.

2.  **Microsoft.Azure.Management.Profiles.hybrid\_2017\_03\_09. *ResourceProvider*. 0.9.0-preview.nupkg** — если вы используете метку ниже, чем сборка 1808, выберите этот профиль.

3.  **Latest** — профиль с новейшими версиями всех служб. Используйте последние версии всех служб. Этот профиль входит в пакет NuGet **Microsoft.Azure.Management**.

См. дополнительные сведения о [Summary of API profiles][].

## <a name="azure-net-sdk-api-profile-usage"></a>Использование профиля API пакета Azure SDK .NET

Чтобы создать экземпляр профиля клиента, используйте следующий код. Этот параметр требуется только для Azure Stack и других частных облаков. В глобальной среде Azure эти параметры используются по умолчанию.

Следующий код необходим для проверки подлинности субъекта-службы в Azure Stack. Он создает маркер, используя идентификатор клиента и базу аутентификации для Azure Stack.

```csharp
public class CustomLoginCredentials : ServiceClientCredentials
{
    private string clientId;
    private string clientSecret;
    private string resourceId;
    private string tenantId;

    private const string authenticationBase = "https://login.windows.net/{0}";

    public CustomLoginCredentials(string servicePrincipalId, string servicePrincipalSecret, string azureEnvironmentResourceId, string azureEnvironmentTenandId)
    {
        clientId = servicePrincipalId;
        clientSecret = servicePrincipalSecret;
        resourceId = azureEnvironmentResourceId;
        tenantId = azureEnvironmentTenandId;
    }
```

Так вы сможете использовать пакеты NuGet профиля API для успешного развертывания приложения в Azure Stack.

## <a name="define-azure-stack-environment-setting-functions"></a>Определение функций параметров среды Azure Stack

Для проверки подлинности субъекта-службы в среде Azure Stack используйте следующий код:

```csharp
private string AuthenticationToken { get; set; }
public override void InitializeServiceClient<T>(ServiceClient<T> client)
{
    var authenticationContext = new AuthenticationContext(String.Format(authenticationBase, tenantId));
    var credential = new ClientCredential(clientId, clientSecret);
    var result = authenticationContext.AcquireTokenAsync(resource: resourceId,
    clientCredential: credential).Result;
    if (result == null)
    {
        throw new InvalidOperationException("Failed to obtain the JWT token");
    }
    AuthenticationToken = result.AccessToken;
}
```

Здесь переназначаются настройки клиента инициализации службы, чтобы аутентификация выполнялась через Azure Stack.

## <a name="samples-using-api-profiles"></a>Примеры с профилями API

Вы можете использовать следующие примеры, опубликованные в репозитории GitHub, в качестве рекомендаций при создании решений с профилями API Azure Stack и .NET:

-   [Тестовый проект с виртуальной машиной, виртуальной сетью, группой ресурсов и учетной записью хранения][]
-   Управление виртуальными машинами с помощью .NET

### <a name="sample-unit-test-project"></a>Пример проекта модульного теста 

1.  Клонируйте репозиторий, используя следующую команду:

    `git clone <https://github.com/seyadava/azure-sdk-for-net-samples/tree/master/TestProject>`

2.  Создайте субъект-службу Azure и назначьте роль для доступа к подписке. См. дополнительные сведения о [Use Azure PowerShell to create a service principal with a certificate][].

3.  Получите следующие обязательные значения:

    1.  Tenant ID
    2.  Идентификатор клиента
    3.  Секрет клиента
    4.  Идентификатор подписки
    5.  Конечная точка Resource Manager

4.  С помощью командной строки настройте следующие переменные среды, используя данные, полученные от созданного субъекта-службы.

    1.  export AZURE_TENANT_ID={your tenant id}
    2.  export AZURE_CLIENT_ID={your client id}
    3.  export AZURE_CLIENT_SECRET={your client secret}
    4.  export AZURE_SUBSCRIPTION_ID={your subscription id}
    5.  export ARM_ENDPOINT={URL-адрес диспетчера ресурсов Azure Stack}

   В Windows используйте **set** вместо **export**.

5.  Убедитесь, что в переменной расположения правильно указано расположение Azure Stack. Например, LOCAL = "local".

6.  Задайте пользовательские учетные данные, которые позволят пройти аутентификацию в Azure Stack. Обратите внимание, что эта часть кода в этом примере размещена в папке Authorization.

   ```csharp
   public class CustomLoginCredentials : ServiceClientCredentials
   {
       private string clientId;
       private string clientSecret;
       private string resourceId;
       private string tenantId;
       private const string authenticationBase = "https://login.windows.net/{0}";
       public CustomLoginCredentials(string servicePrincipalId, string servicePrincipalSecret, string azureEnvironmentResourceId, string azureEnvironmentTenandId)
       {
           clientId = servicePrincipalId;
           clientSecret = servicePrincipalSecret;
           resourceId = azureEnvironmentResourceId;
           tenantId = azureEnvironmentTenandId;
       }
   private string AuthenticationToken { get; set; }
   ```

7.  Если вы используете Azure Stack, добавьте следующий код для перенастройки клиента инициализации службы на аутентификацию Azure Stack. Обратите внимание, что часть этого кода в этом примере уже размещена в папке Authorization.

   ```csharp
   public override void InitializeServiceClient<T>(ServiceClient<T> client)
   {
      var authenticationContext = new AuthenticationContext(String.Format(authenticationBase, tenantId));
      var credential = new ClientCredential(clientId, clientSecret);
      var result = authenticationContext.AcquireTokenAsync(resource: resourceId,
                clientCredential: credential).Result;
      if (result == null)
      {
          throw new InvalidOperationException("Failed to obtain the JWT token");
      }
      AuthenticationToken = result.AccessToken;
   }
   ```
 
8.  С помощью диспетчера пакетов NuGet найдите профиль 2018-03-01-hybrid и установите все связанные с ним пакеты поставщиков ресурсов служб вычислений, приложений и хранилища, а также Сети и Key Vault.

2.  В пределах каждой задачи в файле с расширением CS задайте параметры для работы с Azure Stack. Ниже приведен пример для задачи `CreateResourceGroupTest`:

   ```csharp
   var location = Environment.GetEnvironmentVariable("AZURE_LOCATION");
   var baseUriString = Environment.GetEnvironmentVariable("AZURE_BASE_URL");
   var resourceGroupName = Environment.GetEnvironmentVariable("AZURE_RESOURCEGROUP");
   var servicePrincipalId = Environment.GetEnvironmentVariable("AZURE_CLIENT_ID");
   var servicePrincipalSecret = Environment.GetEnvironmentVariable("AZURE_CLIENT_SECRET");
   var azureResourceId = Environment.GetEnvironmentVariable("AZURE_RESOURCE_ID");
   var tenantId = Environment.GetEnvironmentVariable("AZURE_TENANT_ID");
   var subscriptionId = Environment.GetEnvironmentVariable("AZURE_SUBSCRIPTION_ID");
   var credentials = new CustomLoginCredentials(servicePrincipalId, servicePrincipalSecret, azureResourceId, tenantId);
   ```

1.  Поочередно щелкните каждую задачу правой кнопкой мыши и выберите действие **Запустить тест**.

    1.  Зеленые галочки на боковой панели обозначают, что соответствующие задачи успешно созданы с указанными параметрами. Проверьте подписку Azure Stack, чтобы убедиться в успешном создании этих ресурсов.

    2.  См. дополнительные сведения о [Run unit tests with Test Explorer][].

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о профилях API:

- [Manage API version profiles in Azure Stack](azure-stack-version-profiles.md) (Управление профилями версий API в Azure Stack).
- [Версии API поставщика ресурсов, поддерживаемые профилями в Azure Stack](azure-stack-profiles-azure-resource-manager-versions.md)

  [Getting Started - Installing Git]: https://git-scm.com/book/en/v2/Getting-Started-Installing-Git (Приступая к работе — установка Git)
  [Finding and installing a package]: /nuget/tools/package-manager-ui (Поиск и установка пакета)
  [NuGet Package Manager instructions]: https://marketplace.visualstudio.com/items?itemName=jmrog.vscode-nuget-package-manager (Инструкции по диспетчеру пакетов NuGet)
  [Create subscriptions to offers in Azure Stack]: ../azure-stack-subscribe-plan-provision-vm.md (Создание подписок для предложений в Azure Stack)
  [Provide applications access to Azure Stack]: ../azure-stack-create-service-principals.md (Предоставление приложениям доступа к Azure Stack)
  [*tenant ID*]: ../azure-stack-identity-overview.md (Идентификатор клиента)
  [*subscription ID*]: ../azure-stack-plan-offer-quota-overview.md#subscriptions (Идентификатор подписки)
  [*the Azure Stack resource manager endpoint*]: ../user/azure-stack-version-profiles-ruby.md#the-azure-stack-resource-manager-endpoint (Конечная точка Resource Manager для Azure Stack)
  [Summary of API profiles]: ../user/azure-stack-version-profiles.md#summary-of-api-profiles (Сводка по профилям API)
  [Тестовый проект с виртуальной машиной, виртуальной сетью, группой ресурсов и учетной записью хранения]: https://github.com/seyadava/azure-sdk-for-net-samples/tree/master/TestProject
  [Use Azure PowerShell to create a service principal with a certificate]: ../azure-stack-create-service-principals.md (Использование Azure PowerShell для создания субъекта-службы с сертификатом)
  [Run unit tests with Test Explorer]: /visualstudio/test/run-unit-tests-with-test-explorer?view=vs-2017 (Выполнение модульных тестов с помощью обозревателя тестов)
