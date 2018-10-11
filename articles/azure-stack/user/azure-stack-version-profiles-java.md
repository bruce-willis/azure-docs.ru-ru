---
title: Использование профилей версий API с помощью Java в Azure Stack | Документация Майкрософт
description: Сведения об использовании профилей версий API с помощью Java в Azure Stack.
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
ms.date: 09/28/2018
ms.author: sethm
ms.reviewer: sijuman
ms.openlocfilehash: 5a97a683e7f25029199ba68ce3d5cee410c3cf29
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2018
ms.locfileid: "48886830"
---
# <a name="use-api-version-profiles-with-java-in-azure-stack"></a>Использование профилей версий API с помощью Java в Azure Stack

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

Пакет SDK для Resource Manager Azure Stack для Java предоставляет средства для создания и администрирования инфраструктуры. В этом пакете SDK представлены поставщики ресурсов вычислений, сети, хранилища, служб приложений и [KeyVault](../../key-vault/key-vault-whatis.md). Пакет SDK для Java добавляет профили API, включая зависимости в файл Pom.xml, который загружает правильные модули в JAVA-файле. Но вы можете указать в качестве зависимостей несколько профилей Azure, например **2018-03-01-hybrid** или **latest**. Такие зависимости загружают правильный модуль, чтобы при создании типа ресурса вы могли выбирать из этих профилей версию API, которую хотите использовать. Это позволяет использовать в Azure последние стабильные версии, а для разработки — самые свежие версии API для Azure Stack. Пакет SDK для Java позволяет создать полноценную гибридную облачную среду. Профили API в пакете SDK для Java позволяют выполнять разработку гибридных облачных приложений, легко переключаясь между глобальными ресурсами Azure и ресурсами в Azure Stack.

## <a name="java-and-api-version-profiles"></a>Использование профилей версии API с помощью Java

Профиль API определяет поставщик ресурсов и версии API. С помощью профиля API можно получить последнюю и наиболее стабильную версию ресурса любого типа из представленных в пакете поставщика ресурсов.

- Чтобы получить последние версии всех служб, укажите профиль **Latest** в качестве зависимости.
    
   - Чтобы использовать самый новый профиль, укажите зависимость **com.microsoft.azure**.

   - Чтобы использовать службы, совместимые с Azure Stack, укажите профиль **com.microsoft.azure.profile\_2018\_03\_01\_hybrid**.
    
      - Это значение нужно указать в качестве зависимости в файле Pom.xml, чтобы модули загружались автоматически при выборе нужного класса из раскрывающегося списка, как в .NET.
        
      - Верхняя часть каждого модуля выглядит следующим образом:         
           `Import com.microsoft.azure.management.resources.v2018_03_01.ResourceGroup`
             

  - Зависимости оформляются так:
     ```xml
     <dependency>
     <groupId>com.microsoft.azure.profile_2018_03_01_hybrid</groupId>
     <artifactId>azure</artifactId>
     <version>1.0.0-beta</version>
     </dependency>
     ```

  - Чтобы использовать конкретные версии API для определенного типа ресурса от конкретного поставщика, выберите соответствующую версию API, определенную с помощью IntelliSense.

Обратите внимание, что все описанные выше варианты можно сочетать в одном приложении.

## <a name="install-the-azure-java-sdk"></a>Установка пакета Azure SDK для Java

Чтобы установить пакет SDK для Java, выполните следующие действия.

1.  Выполните официальные инструкции по установке Git. Инструкции см. в разделе по [установке Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).

2.  Выполните официальные инструкции по установке [пакета SDK для Java](http://zulu.org/download/) и [Maven](https://maven.apache.org/). Следует использовать Java Developer Kit версии 8. Следует использовать Apache Maven версии 3.0 или более поздней версии. Для выполнения инструкций из этого краткого руководства в переменной среды JAVA_HOME нужно сохранить расположение установки JDK. Дополнительные сведения см. в статье [Создание первой функции с помощью Java и Maven](../../azure-functions/functions-create-first-java-maven.md).

3.  Чтобы установить правильные пакеты зависимостей, откройте файл Pom.xml в приложении Java. Добавьте в него зависимость, как показано в следующем коде:

   ```xml  
   <dependency>
   <groupId>com.microsoft.azure.profile_2018_03_01_hybrid</groupId>
   <artifactId>azure</artifactId>
   <version>1.0.0-beta</version>
   </dependency>
   ```

4.  Набор устанавливаемых пакетов зависит от версии профиля, который вам нужен. Имена пакетов для версий профилей:
    
   - **com.microsoft.azure.profile\_2018\_03\_01\_hybrid**
   - **com.microsoft.azure**
      - **Актуальная**

5.  При необходимости создайте подписку и сохраните ее идентификатор, чтобы использовать его позднее. Инструкции по созданию подписки см. в статье [Создание подписок для предложений в Azure Stack](../azure-stack-subscribe-plan-provision-vm.md).

6.  Создайте субъект-службу и сохраните идентификатор клиента и секрет клиента. Инструкции по созданию субъекта-службы для Azure Stack см. в статье [Предоставление приложениям доступа к Azure Stack](../azure-stack-create-service-principals.md). Обратите внимание, что идентификатор клиента при создании субъекта-службы называется идентификатором приложения.

7.  Убедитесь, что субъект-служба имеет роль участника или владельца в вашей подписке. Сведения о том, как назначить роль субъекту-службе, см. в статье [Предоставление приложениям доступа к Azure Stack](../azure-stack-create-service-principals.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы использовать пакет SDK Azure для Java и Azure Stack, укажите следующие значения и задайте значения для переменных среды. Чтобы настроить переменные среды, воспользуйтесь инструкциями, которые указаны после таблицы для соответствующей операционной системы.

| Значение                     | Переменные среды | ОПИСАНИЕ                                                                                                                                                                                                          |
| ------------------------- | --------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Tenant ID                 | AZURE_TENANT_ID            | Значение [<span class="underline">идентификатора клиента</span>](../azure-stack-identity-overview.md) Azure Stack.                                                          |
| Идентификатор клиента                 | AZURE_CLIENT_ID             | Идентификатор приложения субъекта-службы, сохраненный во время создания субъекта-службы (см. выше).                                                                                              |
| Идентификатор подписки           | AZURE_SUBSCRIPTION_ID      | [<span class="underline">Идентификатор подписки</span>](../azure-stack-plan-offer-quota-overview.md#subscriptions) для доступа к предложениям в Azure Stack.                |
| Секрет клиента             | AZURE_CLIENT_SECRET        | Секрет приложения субъекта-службы, сохраненный во время создания субъекта-службы (см. выше).                                                                                                                                   |
| Конечная точка Resource Manager | ARM_ENDPOINT              | См. дополнительные сведения о [<span class="underline">конечной точке Resource Manager для Azure Stack</span>](../user/azure-stack-version-profiles-ruby.md#the-azure-stack-resource-manager-endpoint). |
| Расположение                  | RESOURCE_LOCATION    | Локальное значение для Azure Stack                                                                                                                                                                                                |

Чтобы узнать идентификатор клиента для Azure Stack, выполните приведенные [здесь](../azure-stack-csp-ref-operations.md) инструкции. Чтобы настроить переменные среды, сделайте следующее:

### <a name="microsoft-windows"></a>Microsoft Windows

Используйте следующие команды, чтобы настроить переменные среды в командной строке Windows:

```shell
Set AZURE_TENANT_ID=<Your_Tenant_ID>
```

### <a name="macos-linux-and-unix-based-systems"></a>MacOS, Linux и системы на основе Unix

В системах на основе Unix можно использовать такую команду:

```shell
Export AZURE_TENANT_ID=<Your_Tenant_ID>
```

### <a name="the-azure-stack-resource-manager-endpoint"></a>Конечная точка Resource Manager для Azure Stack

Microsoft Azure Resource Manager — это платформа управления, которая позволяет администраторам развертывать, администрировать и отслеживать ресурсы Azure. Azure Resource Manager может обрабатывать эти задачи в рамках одной операции как группы, а не по отдельности.

Вы можете получить метаданные из конечной точки Resource Manager. Конечная точка возвращает JSON-файл со сведениями, необходимыми для запуска кода.

Обратите внимание на следующие моменты:

- В Пакете средств разработки Azure Stack (ASDK) **ResourceManagerUrl**: https://management.local.azurestack.external/.

- В интегрированных системах **ResourceManagerUrl**: `https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/`.

Чтобы получить необходимые метаданные, используйте `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`.

Пример JSON-файла:

```json
{ 
   "galleryEndpoint": "https://portal.local.azurestack.external:30015/",
   "graphEndpoint": "https://graph.windows.net/",
   "portal Endpoint": "https://portal.local.azurestack.external/",
   "authentication": 
      {
      "loginEndpoint": "https://login.windows.net/",
      "audiences": ["https://management.<yourtenant>.onmicrosoft.com/3cc5febd-e4b7-4a85-a2ed-1d730e2f5928"]
      }
}
```

## <a name="existing-api-profiles"></a>Существующие профили API

1.  **com.microsoft.azure.profile\_2018\_03\_01\_hybrid**: актуальный профиль для Azure Stack. Используйте этот профиль для служб, которым нужна максимальная совместимость с Azure Stack с меткой 1808 или более новой.

2.  **com.microsoft.azure**: профиль с новейшими версиями всех служб. Используйте последние версии всех служб.

См. дополнительные сведения о [профилях API и Azure Stack](../user/azure-stack-version-profiles.md#summary-of-api-profiles).

## <a name="azure-java-sdk-api-profile-usage"></a>Использование профиля API пакета SDK Azure для Java

Следующий код выполняет аутентификацию субъекта-службы в Azure Stack. Он создает маркер, используя идентификатор клиента и базу аутентификации для Azure Stack.

```java
AzureTokenCredentials credentials = new ApplicationTokenCredentials(client, tenant, key, AZURE_STACK)
                    .withDefaultSubscriptionId(subscriptionId);
Azure azureStack = Azure.configure()
                    .withLogLevel(com.microsoft.rest.LogLevel.BASIC)
                    .authenticate(credentials, credentials.defaultSubscriptionId());
```

Так вы сможете использовать зависимости профиля API для успешного развертывания приложения в Azure Stack.

## <a name="define-azure-stack-environment-setting-functions"></a>Определение функций параметров среды Azure Stack

Чтобы зарегистрировать облако Azure Stack с правильными конечными точками, используйте следующий код:

```java
AzureEnvironment AZURE_STACK = new AzureEnvironment(new HashMap<String, String>() {
                {
                    put("managementEndpointUrl", settings.get("audience"));
                    put("resourceManagerEndpointUrl", armEndpoint);
                    put("galleryEndpointUrl", settings.get("galleryEndpoint"));
                    put("activeDirectoryEndpointUrl", settings.get("login_endpoint"));
                    put("activeDirectoryResourceId", settings.get("audience"));
                    put("activeDirectoryGraphResourceId", settings.get("graphEndpoint"));
                    put("storageEndpointSuffix", armEndpoint.substring(armEndpoint.indexOf('.')));
                    put("keyVaultDnsSuffix", ".vault" + armEndpoint.substring(armEndpoint.indexOf('.')));
                }
            });
```

Вызов `getActiveDirectorySettings` в этом коде извлекает конечные точки из конечных точек метаданных. Он использует переменные среды из выполняемого вызова:

```java
public static HashMap<String, String>
getActiveDirectorySettings(String armEndpoint) {

HashMap<String, String> adSettings = new HashMap<String, String>();

try {

// create HTTP Client
HttpClient httpClient = HttpClientBuilder.create().build();

// Create new getRequest with below mentioned URL
HttpGet getRequest = new
HttpGet(String.format("%s/metadata/endpoints?api-version=1.0",
armEndpoint));

// Add additional header to getRequest which accepts application/xml data
getRequest.addHeader("accept", "application/xml");

// Execute request and catch response
HttpResponse response = httpClient.execute(getRequest);
```

## <a name="samples-using-api-profiles"></a>Примеры с профилями API

Вы можете использовать примеры из репозитория GitHub в качестве рекомендаций при создании решений с профилями API Azure Stack и .NET:

  - [Управление группами ресурсов](https://github.com/Azure-Samples/Hybrid-resources-java-manage-resource-group)

  - [Управление учетными записями хранения](https://github.com/Azure-Samples/hybrid-storage-java-manage-storage-accounts)

  - [Управление виртуальной машиной](https://github.com/Azure-Samples/hybrid-compute-java-manage-vm)

### <a name="sample-unit-test-project"></a>Пример проекта модульного теста 

1.  Клонируйте репозиторий, используя следующую команду:
    
    `git clone https://github.com/Azure-Samples/Hybrid-resources-java-manage-resource-group.git`

2.  Создайте субъект-службу Azure и назначьте роль для доступа к подписке. См. дополнительные сведения о [создании субъекта-службы с сертификатом с помощью Azure PowerShell](../azure-stack-create-service-principals.md).

3.  Получите следующие обязательные значения переменных среды:
    
    -  AZURE_TENANT_ID
    -  AZURE_CLIENT_ID
    -  AZURE_CLIENT_SECRET
    -  AZURE_SUBSCRIPTION_ID
    -  ARM_ENDPOINT
    -  RESOURCE_LOCATION

4.  С помощью командной строки настройте следующие переменные среды, используя данные, полученные от созданного субъекта-службы.
    
    - export AZURE_TENANT_ID={your tenant id}
    - export AZURE_CLIENT_ID={your client id}
    - export AZURE_CLIENT_SECRET={your client secret}
    - export AZURE_SUBSCRIPTION_ID={your subscription id}
    - export ARM_ENDPOINT={URL-адрес диспетчера ресурсов Azure Stack}
    - export RESOURCE_LOCATION={расположение Azure Stack}

   В Windows используйте **set** вместо **export**.

5.  Используйте код `getactivedirectorysettings`, чтобы получить конечную точку метаданных arm, а затем HTTP-клиент, чтобы задать сведения о конечной точке.

   ```java
   public static HashMap<String, String> getActiveDirectorySettings(String armEndpoint) {
   HashMap<String, String> adSettings = new HashMap<String,> String>();

   try {

   // create HTTP Client
   HttpClient httpClient = HttpClientBuilder.create().build();

   // Create new getRequest with below mentioned URL
   HttpGet getRequest = new
   HttpGet(String.format("%s/metadata/endpoints?api-version=1.0", armEndpoint));

   // Add additional header to getRequest which accepts application/xml data
   getRequest.addHeader("accept", "application/xml");

   // Execute request and catch response
   HttpResponse response = httpClient.execute(getRequest);
   ```

6.  В файл pom.xml добавьте зависимости, приведенные ниже, чтобы использовать профиль 2018-03-01-hybrid для Azure Stack. Эта зависимость установит все связанные с этим профилем модули поставщиков ресурсов вычислений, сети, хранилища, хранилища ключей и службы приложений.
      
   ```xml
   <dependency>
   <groupId>com.microsoft.azure.profile_2018_03_01_hybrid</groupId>
   <artifactId>azure</artifactId>
   <version>1.0.0-beta</version>
   </dependency>
   ```

8.  В окне командной строки, которое вы ранее открыли для настройки переменных среды, введите следующую строку:
    
   ```shell
   mvn clean compile exec:java
   ```

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о профилях API:

- [Manage API version profiles in Azure Stack](azure-stack-version-profiles.md) (Управление профилями версий API в Azure Stack).
- [Версии API поставщика ресурсов, поддерживаемые профилями в Azure Stack](azure-stack-profiles-azure-resource-manager-versions.md)
