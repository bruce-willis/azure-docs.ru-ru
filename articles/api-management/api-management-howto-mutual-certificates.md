---
title: Защита внутренних служб с помощью проверки подлинности на основе сертификата клиента в службе управления API Azure | Документация Майкрософт
description: Узнайте, как защитить фоновые службы посредством проверки подлинности с помощью сертификата клиента в службе Azure API Management
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2018
ms.author: apimpm
ms.openlocfilehash: 2b0db6f9a1e9cd660ce1b1e7af3e7e1c85815c16
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/27/2018
ms.locfileid: "43045254"
---
# <a name="how-to-secure-back-end-services-using-client-certificate-authentication-in-azure-api-management"></a>Защита фоновых служб посредством проверки подлинности с помощью сертификата клиента в службе Azure API Management

Служба управления API позволяет защитить доступ ко внутренним службам API с помощью сертификатов клиента. В этом руководстве описывается, как на портале Azure управлять сертификатами в экземпляре службы управления API Azure. Также объясняется, как настроить API для доступа к внутренней службе с помощью сертификата.

Сведения об управлении сертификатами с помощью REST API службы управления API см. в статье, посвященной <a href="https://docs.microsoft.com/en-us/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-certificate-entity">сущности сертификата REST API службы управления API Azure</a>.

## <a name="prerequisites"> </a>Предварительные требования

В этом руководстве описано, как настроить для экземпляра службы API Management проверку подлинности с помощью сертификата клиента при доступе из API к фоновой службе. Прежде чем выполнить инструкции из этой статьи, нужно настроить свою внутреннюю службу для аутентификации на основе сертификата клиента. [Сведения о настройке такой аутентификации в веб-сайтах Azure см. в этой статье][to configure certificate authentication in Azure WebSites refer to this article]. Вам потребуется доступ к сертификату и паролю, чтобы передать их в службу управления API.

## <a name="step1"> </a>Отправка сертификата клиента

![Добавление сертификатов клиента](media/api-management-howto-mutual-certificates/apim-client-cert.png)

Чтобы передать новый сертификат клиента, выполните действия ниже. Если экземпляр службы управления API еще не создан, выполните инструкции из руководства [Создание экземпляра службы управления API Azure][Create an API Management service instance].

1. На портале Azure перейдите к экземпляру службы управления API Azure.
2. В меню выберите **Сертификаты клиента**.
3. Щелкните **+ Добавить**.  
    ![Добавление сертификатов клиента](media/api-management-howto-mutual-certificates/apim-client-cert-add.png)  
4. Выберите сертификат и укажите его идентификатор и пароль.  
5. Нажмите кнопку **Создать**.

> [!NOTE]
> Сертификат должен быть в формате **PFX** . Разрешено использовать самозаверяющие сертификаты.

Когда сертификат будет передан, он появится на вкладке **Сертификаты клиента**.  Если у вас несколько сертификатов, сохраните отпечаток необходимого сертификата, чтобы [настроить API для аутентификации шлюза на основе сертификата клиента][Configure an API to use a client certificate for gateway authentication].

> [!NOTE]
> Чтобы отключить проверку цепочки сертификатов при использовании, например, самозаверяющего сертификата, выполните действия, описанные в [этом разделе](api-management-faq.md#can-i-use-a-self-signed-ssl-certificate-for-a-back-end) часто задаваемых вопросов.

## <a name="step1a"> </a>Удаление сертификата клиента

Чтобы удалить сертификат, щелкните контекстное меню **...** и выберите **Удалить** рядом с именем сертификата.

![Удаление сертификатов клиента](media/api-management-howto-mutual-certificates/apim-client-cert-delete.png)

Если сертификат используется интерфейсом API, появляется предупреждение. Перед удалением сертификата нужно сначала удалить его из всех API, которые настроены на его использование.

![Ошибка при удалении сертификата клиента](media/api-management-howto-mutual-certificates/apim-client-cert-delete-failure.png)

## <a name="step2"> </a>Настройка API для проверки подлинности шлюза с помощью сертификата клиента

1. Щелкните **Интерфейсы API** в меню **Управление API** слева и перейдите к API.  
    ![Включение сертификатов клиента](media/api-management-howto-mutual-certificates/apim-client-cert-enable.png)

2. На вкладке **Конструктор** щелкните значок карандаша в разделе **Внутренняя служба**. 
3. Измените значение параметра **Учетные данные шлюза** на **Сертификат клиента** и выберите свой сертификат в раскрывающемся списке.  
    ![Включение сертификатов клиента](media/api-management-howto-mutual-certificates/apim-client-cert-enable-select.png)

4. Выберите команду **Сохранить**. 

> [!WARNING]
> Изменение вступает в силу немедленно, и для проверки подлинности на фоновом сервере при вызове операций этого API теперь будет использоваться сертификат.


> [!TIP]
> Если сертификат настроен для проверки подлинности шлюза при доступе к серверной службе API, он становится частью политики для этого API, и его можно увидеть в редакторе политики.

## <a name="self-signed-certificates"></a>Самозаверяющие сертификаты

Если вы используете самозаверяющие сертификаты, вам потребуется отключить проверку цепочки сертификатов, чтобы служба управления API могла взаимодействовать с серверной системой. В противном случае будет возвращена ошибка с кодом 500. Для этого используйте командлеты PowerShell [`New-AzureRmApiManagementBackend`](https://docs.microsoft.com/powershell/module/azurerm.apimanagement/new-azurermapimanagementbackend) (для новой серверной части) или [`Set-AzureRmApiManagementBackend`](https://docs.microsoft.com/powershell/module/azurerm.apimanagement/set-azurermapimanagementbackend) (для существующей серверной части) и задайте для параметра `-SkipCertificateChainValidation` значение `True`.

```
$context = New-AzureRmApiManagementContext -resourcegroup 'ContosoResourceGroup' -servicename 'ContosoAPIMService'
New-AzureRmApiManagementBackend -Context  $context -Url 'https://contoso.com/myapi' -Protocol http -SkipCertificateChainValidation $true
```

[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: ../api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md
[API Management policy reference]: api-management-policy-reference.md
[Caching policies]: api-management-policy-reference.md#caching-policies
[Create an API Management service instance]: get-started-create-service-instance.md

[Azure API Management REST API Certificate entity]: http://msdn.microsoft.com/library/azure/dn783483.aspx
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[to configure certificate authentication in Azure WebSites refer to this article]: ../app-service/app-service-web-configure-tls-mutual-auth.md

[Prerequisites]: #prerequisites
[Upload a client certificate]: #step1
[Delete a client certificate]: #step1a
[Configure an API to use a client certificate for gateway authentication]: #step2
[Test the configuration by calling an operation in the Developer Portal]: #step3
[Next steps]: #next-steps
