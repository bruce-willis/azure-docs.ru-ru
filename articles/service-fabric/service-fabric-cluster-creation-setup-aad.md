---
title: Настройка Azure Active Directory для проверки подлинности клиента Service Fabric | Документация Майкрософт
description: Сведения о настройке Azure Active Directory (Azure AD) для проверки подлинности клиентов для кластеров Service Fabric.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: chackdan
ms.assetid: 15d0ab67-fc66-4108-8038-3584eeebabaa
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/15/2018
ms.author: aljo
ms.openlocfilehash: 9b00f6e5c4dbd2fe2c6aab4c62cecc2f2e2640f0
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/20/2018
ms.locfileid: "42143597"
---
# <a name="set-up-azure-active-directory-for-client-authentication"></a>Настройка Azure Active Directory для проверки подлинности клиента

Для кластеров, работающих в Azure, рекомендуется использовать Azure Active Directory (Azure AD), чтобы обеспечить безопасность доступа к конечным точкам управления.  В этой статье описывается, как настроить Azure AD для проверки подлинности клиентов для кластера Service Fabric и что нужно сделать перед [созданием кластера](service-fabric-cluster-creation-via-arm.md).  Azure AD позволяет организациям (известным как клиенты) управлять доступом пользователей к приложениям. Приложения можно разделить на две группы: те, в которых есть пользовательский веб-интерфейс входа в систему, и те, в которых вход выполняется с помощью собственного клиентского приложения. В этой статье предполагается, что клиент уже создан. Если это не так, обратитесь к статье [Как получить клиент Azure Active Directory][active-directory-howto-tenant].

## <a name="create-azure-ad-applications"></a>Создание приложений Azure AD
Кластеры Service Fabric предлагают несколько точек входа для управления функциями кластеров, включая веб-интерфейс [Service Fabric Explorer][service-fabric-visualizing-your-cluster] и [Visual Studio][service-fabric-manage-application-in-visual-studio]. В итоге вы создаете два приложения Azure AD для управления доступом к кластеру: одно веб-приложение и одно собственное приложение.  Создав приложения, сопоставьте пользователей с ролями, разрешающими доступ только для чтения и администрирования.

Чтобы упростить некоторые шаги по настройке Azure AD с кластером Service Fabric, мы создали набор сценариев Windows PowerShell.

> [!NOTE]
> Перед созданием кластера необходимо выполнить следующие действия. Поскольку в сценариях предварительно заданы имена кластеров и конечные точки, эти имена нужно выбрать заблаговременно, при этом они должны отличаться от созданных ранее имен.

1. [Скачайте сценарии][sf-aad-ps-script-download] на компьютер.
2. Щелкните правой кнопкой мыши ZIP-файл, выберите **Свойства**, установите флажок **Разблокировать** и нажмите кнопку **Применить**.
3. Извлеките ZIP-файл.
4. Запустите `SetupApplications.ps1` и укажите TenantId (идентификатор клиента), ClusterName (имя кластера) и WebApplicationReplyUrl (URL-адрес ответа веб-приложения) в качестве параметров. Например: 

```PowerShell
.\SetupApplications.ps1 -TenantId '690ec069-8200-4068-9d01-5aaf188e557a' -ClusterName 'mycluster' -WebApplicationReplyUrl 'https://mycluster.westus.cloudapp.azure.com:19080/Explorer/index.html'
```

> [!NOTE]
> Для национальных облаков (Azure для государственных организаций, Azure для Китая и Azure для Германии) также необходимо указать параметр `-Location`.

Чтобы узнать TenantId, можно выполнить команду PowerShell `Get-AzureSubscription`. После выполнения этой команды для каждой подписки отображается TenantId.

Значение ClusterName используется в качестве префикса для приложений Azure AD, создаваемых сценарием. Точное совпадение с именем реального кластера не требуется. Оно предназначено только для упрощения сопоставления артефактов Azure AD с кластером Service Fabric, с которым они используются.

WebApplicationReplyUrl является конечной точкой по умолчанию, которую Azure AD возвращает пользователям после завершения ими входа. Эту конечную точку следует назначить конечной точкой Service Fabric Explorer для кластера, по умолчанию это:

https://&lt;cluster_domain&gt;:19080/Explorer

Вам будет предложено войти в учетную запись с правами администратора для клиента Azure AD. После входа сценарий начнет создавать веб-приложение и собственное приложение для представления кластера Service Fabric. Если посмотреть на список приложений клиента на [портале Azure][azure-portal], вы увидите две новых записи:

   * *имя_кластера*\_Кластер
   * *имя_кластера*\_Клиент

Этот скрипт выводит код JSON для шаблона Azure Resource Manager, который вы будете использовать в следующем разделе для создания кластера. Рекомендуем не закрывать пока окно PowerShell.

```json
"azureActiveDirectory": {
  "tenantId":"<guid>",
  "clusterApplication":"<guid>",
  "clientApplication":"<guid>"
},
```

<a name="assign-roles"></a>

## <a name="assign-users-to-roles"></a>Назначение пользователей ролям
Создав приложения, представляющие кластер, сопоставьте пользователей с ролями, поддерживаемыми Service Fabric и разрешающими доступ на чтение и администрирование. Роли можно назначить с помощью [портала Azure][azure-portal].

1. На портале Azure выберите клиент в правом верхнем углу.

    ![Кнопка выбора клиента][select-tenant-button]
2. Выберите **Azure Active Directory** на левой вкладке, а затем выберите "Корпоративные приложения".
3. Выберите "Все приложения", найдите и выберите веб-приложение с именем наподобие `myTestCluster_Cluster`.
4. Откройте вкладку **Пользователи и группы**.

    ![Вкладка "Пользователи и группы"][users-and-groups-tab]
5. На новой странице нажмите кнопку **Добавить пользователя**, выберите пользователя и роль, которые нужно назначить, и нажмите кнопку **Выбрать** в нижней части страницы.

    ![Страница назначения ролей пользователям][assign-users-to-roles-page]
6. Нажмите кнопку **Назначить** в нижней части страницы.

    ![Подтверждение о добавлении назначения][assign-users-to-roles-confirm]

> [!NOTE]
> Дополнительные сведения о ролях в Service Fabric см. в статье [Контроль доступа на основе ролей для клиентов Service Fabric](service-fabric-cluster-security-roles.md).
>
>

## <a name="troubleshooting-help-in-setting-up-azure-active-directory"></a>Устранение неполадок при настройке Azure Active Directory
Настройка и использование Azure AD может оказаться сложной задачей, поэтому воспользуйтесь следующими советами для отладки проблемы.

### <a name="service-fabric-explorer-prompts-you-to-select-a-certificate"></a>Service Fabric Explorer предлагает выбрать сертификат
#### <a name="problem"></a>Проблема
После успешного входа в Azure AD в Service Fabric Explorer браузер отображает домашнюю страницу, но на экране отображается предложение выбрать сертификат.

![Диалоговое окно сертификата SFX][sfx-select-certificate-dialog]

#### <a name="reason"></a>Причина
Пользователю не назначена роль в приложении кластера Azure AD. Таким образом, аутентификация Azure AD в кластере Service Fabric завершается ошибкой. Service Fabric Explorer воспользуется проверкой подлинности на основе сертификата.

#### <a name="solution"></a>Решение
Следуйте инструкциям по настройке Azure AD и назначению ролей пользователей. Кроме того, рекомендуется включить параметр "Для доступа к приложению требуется назначение пользователей", как делает сценарий `SetupApplications.ps1`.

### <a name="connection-with-powershell-fails-with-an-error-the-specified-credentials-are-invalid"></a>Подключение с помощью PowerShell завершается ошибкой: "Указанные учетные данные недействительны".
#### <a name="problem"></a>Проблема
После успешного входа в Azure AD при использовании PowerShell для подключения к кластеру с помощью режима безопасности AzureActiveDirectory возникла ошибка, подключение завершается с ошибкой: "Указанные учетные данные недействительны".

#### <a name="solution"></a>Решение
Решение этой проблемы такое же, как и в предыдущем случае.

### <a name="service-fabric-explorer-returns-a-failure-when-you-sign-in-aadsts50011"></a>Service Fabric Explorer при входе возвращает ошибку "AADSTS50011"
#### <a name="problem"></a>Проблема
При попытке входа на страницу Azure AD в Service Fabric Explorer выдается ошибка: "AADSTS50011: адрес ответа &lt;URL-адрес&gt; не совпадает с адресами ответа, настроенными для приложения: &lt;guid&gt;".

![Несовпадение адреса ответа в Service Fabric Explorer][sfx-reply-address-not-match]

#### <a name="reason"></a>Причина
Приложение кластера (веб-приложение), представляющее Service Fabric Explorer, пытается выполнить аутентификацию в Azure AD и в составе запроса предоставляет URL-адрес ответа для перенаправления. URL-адрес не указан в списке **URL-АДРЕС ОТВЕТА** приложения Azure AD.

#### <a name="solution"></a>Решение
Щелкните "Регистрация приложений" на странице AAD, выберите приложение кластера и нажмите кнопку **URL-адреса ответа**. На странице "URL-адреса ответа" добавьте URL-адрес Service Fabric Explorer в список или замените один из элементов в списке. После завершения сохраните изменения.

![URL-адрес ответа веб-приложения][web-application-reply-url]

### <a name="connect-the-cluster-by-using-azure-ad-authentication-via-powershell"></a>Подключение к кластеру с помощью аутентификации Azure AD с использованием PowerShell
Чтобы подключить кластер Service Fabric, воспользуйтесь следующим примером команды PowerShell:

```PowerShell
Connect-ServiceFabricCluster -ConnectionEndpoint <endpoint> -KeepAliveIntervalInSec 10 -AzureActiveDirectory -ServerCertThumbprint <thumbprint>
```

Дополнительные сведения о командлете Connect-ServiceFabricCluster см. в [этой статье](https://docs.microsoft.com/powershell/module/servicefabric/connect-servicefabriccluster).

### <a name="can-i-reuse-the-same-azure-ad-tenant-in-multiple-clusters"></a>Можно ли повторно использовать один и тот же клиент Azure AD в нескольких кластерах?
Да. Не забудьте добавить URL-адрес Service Fabric Explorer в (веб-)приложение кластера. В противном случае — Service Fabric Explorer не работает.

### <a name="why-do-i-still-need-a-server-certificate-while-azure-ad-is-enabled"></a>Почему мне все еще нужен сертификат сервера при включенном Azure AD?
FabricClient и FabricGateway выполняют взаимную аутентификацию. Во время аутентификации Azure AD интеграция Azure AD предоставляет удостоверение клиента серверу и для аутентификации сервера используется сертификат сервера. Дополнительные сведения о сертификатах Service Fabric см. в разделе [Сертификаты X.509 и Service Fabric][x509-certificates-and-service-fabric].

## <a name="next-steps"></a>Дополнительная информация
После настройки приложений Azure Active Directory и ролей для пользователей [настройте и разверните кластер](service-fabric-cluster-creation-via-arm.md).


<!-- Links -->
[azure-CLI]:https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest
[azure-portal]: https://portal.azure.com/
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[active-directory-howto-tenant]:../active-directory/develop/quickstart-create-new-tenant.md
[service-fabric-visualizing-your-cluster]: service-fabric-visualizing-your-cluster.md
[service-fabric-manage-application-in-visual-studio]: service-fabric-manage-application-in-visual-studio.md
[sf-aad-ps-script-download]:http://servicefabricsdkstorage.blob.core.windows.net/publicrelease/MicrosoftAzureServiceFabric-AADHelpers.zip
[x509-certificates-and-service-fabric]: service-fabric-cluster-security.md#x509-certificates-and-service-fabric

<!-- Images -->
[select-tenant-button]: ./media/service-fabric-cluster-creation-setup-aad/select-tenant-button.png
[users-and-groups-tab]: ./media/service-fabric-cluster-creation-setup-aad/users-and-groups-tab.png
[assign-users-to-roles-page]: ./media/service-fabric-cluster-creation-setup-aad/assign-users-to-roles-page.png
[assign-users-to-roles-confirm]: ./media/service-fabric-cluster-creation-setup-aad/assign-users-to-roles-confirm.png
[sfx-select-certificate-dialog]: ./media/service-fabric-cluster-creation-setup-aad/sfx-select-certificate-dialog.png
[sfx-reply-address-not-match]: ./media/service-fabric-cluster-creation-setup-aad/sfx-reply-address-not-match.png
[web-application-reply-url]: ./media/service-fabric-cluster-creation-setup-aad/web-application-reply-url.png
