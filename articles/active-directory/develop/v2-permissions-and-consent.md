---
title: Области, разрешения и согласие в Azure Active Directory версии 2.0 | Документация Майкрософт
description: Описание авторизации в конечной точке Azure AD версии 2.0, включая области, разрешение и предоставление согласия.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 8f98cbf0-a71d-4e34-babf-e644ad9ff423
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: hirsin, jesakowi, justhu
ms.custom: aaddev
ms.openlocfilehash: da8eebb2fc6b87b8916e944495679b45aa34dbf2
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46960334"
---
# <a name="permissions-and-consent-in-the-azure-active-directory-v20-endpoint"></a>Разрешения и предоставление согласия в конечной точке Azure Active Directory версии 2.0

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Приложения, которые интегрируются с платформой удостоверений Майкрософт, следуют модели авторизации, при которой пользователи и администраторы контролируют доступ к данным. Реализация этой модели авторизации в конечной точке версии 2.0 была обновлена, в результате чего изменился механизм взаимодействия приложения с платформой удостоверений Майкрософт. В этой статье рассматриваются основные понятия этой модели авторизации, включая области, разрешения и согласие на их предоставление.

> [!NOTE]
> Не все сценарии и компоненты поддерживаются конечной точкой версии 2.0. Чтобы определить, стоит ли вам использовать конечную точку версии 2.0, ознакомьтесь с [ограничениями версии 2.0](active-directory-v2-limitations.md).

## <a name="scopes-and-permissions"></a>Области и разрешения

Платформа удостоверений Майкрософт реализует протокол авторизации [OAuth 2.0](active-directory-v2-protocols.md). OAuth 2.0 — это метод, посредством которого стороннее приложение может обращаться к интернет-ресурсам от имени пользователя. Любой интернет-ресурс, интегрируемый с платформой удостоверений Майкрософт, имеет идентификатор ресурса или *универсальный код ресурса (URI) идентификатора приложения*. К примеру, ниже приведены некоторые из размещенных в Интернете ресурсов Майкрософт:

* Microsoft Graph: `https://graph.microsoft.com`
* API почты Office 365: `https://outlook.office.com`;
* Azure AD Graph: `https://graph.windows.net`.

> [!NOTE]
> Мы настоятельно рекомендуем использовать Microsoft Graph вместо Azure AD Graph, API почты Office 365 и др.

То же касается любых сторонних ресурсов, интегрированных с платформой удостоверений Майкрософт. Любой из этих ресурсов может также определять набор разрешений, с помощью которых можно разделить функции ресурса на меньшие блоки. Например, в [Microsoft Graph](https://graph.microsoft.com) определены разрешения для выполнения таких задач, как:

* чтение календаря пользователя;
* запись в календарь пользователя;
* отправка сообщений в качестве пользователя.

Благодаря определению этих разрешений ресурс получает детализированный контроль над своими данными и предоставлением внешнего доступа к функционалу API. Стороннее приложение может запрашивать эти разрешения у пользователей и администраторов. Они должны подтвердить такой запрос, прежде чем приложение получит доступ к данным или начнет действовать от имени пользователя. Разделяя функции ресурса на меньшие наборы разрешений, приложения сторонних производителей можно настроить таким образом, чтобы они запрашивали только определенные разрешения, необходимые им для выполнения своих задач. Такой подход позволяет пользователям и администраторам точно знать, к каким данным у приложения есть доступ, что дает большую уверенность в отсутствии вредоносных намерений в работе приложения. Разработчики должны всегда придерживаться принципа минимальных прав доступа и запрашивать только те разрешения, которые необходимы для работы приложения.

В OAuth разрешения такого типа называются *областями*. Также их часто называют просто *разрешениями*. На платформе удостоверений Майкрософт они представлены в виде значения строки. Продолжим рассмотрение примера для Microsoft Graph. Значение строки для каждого разрешения следующее:

* чтение календаря пользователя с помощью `Calendars.Read`;
* запись в календарь пользователя с помощью `Calendars.ReadWrite`;
* отправка сообщений в качестве пользователя с помощью `Mail.Send`.

Обычно приложение запрашивает эти разрешения, указав область в запросах к конечной точке авторизации версии 2.0. Но некоторые высокопривилегированные разрешения могут предоставляться только с согласия администратора, а также запрашиваться или предоставляться с помощью [конечной точки предоставления согласия администратора](v2-permissions-and-consent.md#admin-restricted-scopes). Ознакомьтесь с дополнительными сведениями, чтобы узнать больше.

## <a name="permission-types"></a>Типы разрешений

Платформа удостоверений Майкрософт поддерживает два типа разрешений: **делегированное разрешение** и **разрешение приложения**.

- **Делегированные разрешения** используются приложениями с авторизованным пользователем. Для этих приложений пользователь или администратор соглашается на предоставление разрешений, которые запрашивает приложение. Приложение получает разрешение выполнять роль авторизованного пользователя при вызове целевого ресурса. Для некоторых делегированных разрешений могут предоставить согласие пользователи без прав администратора, а для других разрешений, с более высоким уровнем прав, необходимо [согласие администратора](v2-permissions-and-consent.md#admin-restricted-scopes).  

- **Разрешения приложений** используются в приложениях, работающих без авторизованного пользователя (например, приложения, работающие как фоновые службы или управляющие программы).  Для таких разрешений согласие предоставляет [только администратор](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant). 

_Действующие разрешения_ — это разрешения, которые есть у приложений при запросе целевого ресурса. Важно понимать разницу между делегированными разрешениями и разрешениями приложения, которые получает ваше приложение, а также действующими разрешениями при вызове целевого ресурса.

- Для делегированных разрешений _действующие разрешения_ приложения являются минимальным пересечением делегированных разрешений, предоставленных приложению (с согласия пользователя), и привилегий авторизованного пользователя. Приложения не могут иметь больше привилегий, чем авторизованный пользователь. В рамках организаций привилегии авторизованного пользователя могут определяться политикой или членством в одной или нескольких ролях администратора. Дополнительные сведения о ролях администраторов см. в статье [Назначение ролей администратора в Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md).
  Например, предположим, что вашему приложению предоставлено делегированное разрешение _User.ReadWrite.All_. Это разрешение номинально предоставляет вашему приложению разрешение читать и обновлять профиль каждого пользователя в организации. Если авторизованный пользователь является глобальным администратором, ваше приложение сможет обновлять профиль каждого пользователя в организации. Тем не менее, если авторизованный пользователь имеет роль администратора, ваше приложение сможет обновить только профиль авторизованного пользователя. Оно не сможет обновлять профили других пользователей в организации, потому что пользователь, от имени которого это приложение действует, не имеет таких привилегий.
  
- Для разрешений приложений _действующие разрешения_ вашего приложения будут представлять собой полный уровень привилегий, подразумеваемых разрешением. Например, приложение, имеющее разрешение _User.ReadWrite.All_, может обновлять профиль каждого пользователя в организации. 

## <a name="openid-connect-scopes"></a>Области OpenId Connect

Реализация OpenID Connect на основе версии 2.0 содержит несколько четко определенных областей, которые не применяются к какому-либо конкретному ресурсу: `openid`, `email`, `profile` и `offline_access`.

### <a name="openid"></a>OpenId

Если приложение выполняет вход с помощью протокола [OpenID Connect](active-directory-v2-protocols.md), оно должно запросить область `openid`. В рабочей учетной записи область `openid` отображается на странице согласия в виде разрешения "Вход", а в личной учетной записи Майкрософт — в виде разрешения "View your profile and connect to apps and services using your Microsoft account" (Просмотр профиля и подключение к приложениям и службам с помощью учетной записи Майкрософт). Это разрешение позволяет приложению получить уникальный идентификатор для пользователя в виде утверждения `sub`. Оно также предоставляет приложению доступ к конечной точке сведений о пользователе. Область `openid` можно использовать на конечной точке маркеров версии 2.0 для получения маркеров идентификации, с помощью которых можно защитить HTTP-вызовы между разными компонентами приложения.

### <a name="email"></a>email

Область `email` можно использовать вместе с областью `openid` и любыми другими областями. Она предоставляет приложению доступ к основному электронному адресу пользователя в виде утверждения `email`. Утверждение `email` будет включено в маркер, только если электронный адрес связан с учетной записью пользователя (а это не всегда так). При использовании области `email` приложение должно быть готово обрабатывать случаи, когда утверждение `email` в маркере отсутствует.

### <a name="profile"></a>Профиль

Область `profile` можно использовать вместе с областью `openid` и любыми другими областями. Она предоставляет приложению доступ к существенному объему сведений о пользователе. К этим сведениям, в частности, относятся имя и фамилия пользователя, предпочтительное имя пользователя и идентификатор объекта. Полный список утверждений профиля, доступных в параметре id_tokens для конкретного пользователя, представлен в [`id_tokens`этом справочнике](id-tokens.md).

### <a name="offlineaccess"></a>offline_access

[Область `offline_access`](http://openid.net/specs/openid-connect-core-1_0.html#OfflineAccess) предоставляет приложению доступ к ресурсам от имени пользователя на длительное время. На странице согласия рабочей учетной записи эта область отображается в виде разрешения "Access your data anytime" (Доступ к вашим данным в любое время). На странице согласия личной учетной записи Майкрософт она отображается в виде разрешения "Доступ к вашим сведениям в любое время". Когда пользователь утвердит область `offline_access`, приложение сможет получать маркеры обновления от конечной точки маркеров версии 2.0. Маркеры обновления имеют большой срок действия. Приложение может получать новые маркеры доступа после того, как срок действия старых истечет.

Если приложение не запросит область `offline_access`, оно не получит маркеры обновления. Это значит, что при использовании кода авторизации в [потоке кода авторизации OAuth 2.0](active-directory-v2-protocols.md) от конечной точки `/token` вы получите только маркер доступа. Маркер доступа действителен недолго. Как правило, его срок действия истекает через один час. В этот момент приложению будет необходимо перенаправить пользователя обратно к конечной точке `/authorize`, чтобы получить новый код авторизации. При этом, в зависимости от типа приложения, пользователю может потребоваться повторно ввести учетные данные или предоставить разрешения.

Дополнительные сведения о том, как получать и использовать маркеры обновления, можно найти в [справочнике по протоколу версии 2.0](active-directory-v2-protocols.md).

## <a name="requesting-individual-user-consent"></a>Запрос на получение согласия одного пользователя

В запросе авторизации [OpenID Connect или OAuth 2.0](active-directory-v2-protocols.md) приложение может запросить необходимые разрешения с помощью параметра запроса `scope`. Например, при входе пользователя в приложение оно отправит запрос следующего вида (разрывы строк добавлены для удобства чтения).

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&scope=
https%3A%2F%2Fgraph.microsoft.com%2Fcalendars.read%20
https%3A%2F%2Fgraph.microsoft.com%2Fmail.send
&state=12345
```

Параметр `scope` — это запрашиваемый приложением список делегированных разрешений с разделителями-пробелами. Чтобы указать каждое разрешение, его значение добавляется к идентификатору ресурса (универсальному коду ресурса (URI) идентификатора приложения). В приведенном примере запроса приложению требуется разрешение на чтение календаря пользователя и отправку сообщений от его имени.

После того как пользователь введет учетные данные, конечная точка версии 2.0 проверит наличие соответствующей записи *согласия пользователя*. Если пользователь не предоставил согласие на любое из запрошенных разрешений ранее, а администратор не предоставил согласие от имени всей организации, конечная точка версии 2.0 запросит разрешение у пользователя.

![Предоставление разрешений рабочей учетной записью](./media/v2-permissions-and-consent/work_account_consent.png)

После того как пользователь утвердит разрешение, факт согласия будет записан, чтобы пользователю не пришлось повторно давать его при входе в учетную запись приложения в дальнейшем.

## <a name="requesting-consent-for-an-entire-tenant"></a>Запрос на получение согласия для клиента

Часто, когда организация приобретает лицензию или подписку на приложение, планируется установка приложения для использования всеми сотрудниками. В ходе этого процесса администратор может предоставить этому приложению разрешение действовать от имени любого пользователя. Если администратор предоставляет разрешения для всего клиента, то пользователи организации не увидят страницу согласия для приложения.

Чтобы запросить согласие на делегированные разрешения для всех пользователей в клиенте, приложение может использовать конечную точку предоставления согласия администратора.

Кроме того, конечная точка предоставления согласия администратора понадобится для запроса разрешений приложения.

## <a name="admin-restricted-permissions"></a>Разрешения, предназначенные только для администраторов

Некоторые высокопривилегированные разрешения в экосистеме Майкрософт могут быть помечены как *предназначенные только для администраторов*. Ниже приведены примеры подобных разрешений:

* чтение данных полных профилей пользователей с помощью `User.Read.All`;
* запись данных в каталог организации с помощью `Directory.ReadWrite.All`;
* чтение данных всех групп в каталоге организации с помощью `Groups.Read.All`.

Хотя пользователь, являющийся потребителем, и может предоставить приложению доступ к таким данным, корпоративным пользователям нельзя предоставлять доступ к таким же конфиденциальным данным компании. Если приложение запрашивает доступ к одному из этих разрешений у корпоративного пользователя, появится сообщение об ошибке со сведениями о том, что этот пользователь не может предоставить разрешения приложению.

Если вашему приложению требуется доступ к областям только для администраторов организаций, следует запросить их непосредственно у администратора компании, а также с помощью конечной точки предоставления согласия администратора, как описано ниже.

Если приложение запрашивает высокопривилегированные делегированные разрешения и администратор предоставляет эти разрешения через конечную точку предоставления согласия администратора, согласие получат все пользователи в клиенте.

Если приложение запрашивает разрешения приложения и администратор предоставляет эти разрешения через конечную точку предоставления согласия администратора, право предоставляется не конкретному пользователю, а *напрямую* клиентскому приложению. Разрешения такого типа обычно используются только управляющими программами или другими неинтерактивными приложениями, работающими в фоновом режиме.

## <a name="using-the-admin-consent-endpoint"></a>Использование конечной точки предоставления согласия администратора

Когда администратор компании, используя ваше приложение, переходит к конечной точке авторизации, платформа удостоверений Майкрософт определяет роль пользователя и предлагает ему предоставлять согласие на запрошенные вами разрешения от имени всего клиента. Если вы хотите, чтобы администратор предоставлял разрешения от имени всего клиента, можно использовать выделенную конечную точку предоставления согласия администратора. Эта конечная точка также необходима для запроса разрешений приложения (которые невозможно запросить с помощью конечной точки авторизации).

Если вы выполните описанные здесь действия, приложение получит возможность запрашивать разрешения для всех пользователей в клиенте, включая области только для администраторов. Эта операция является высокопривилегированной, поэтому ее следует выполнять, только если это необходимо для вашего сценария.

Пример кода, реализующий эти действия, см. в [примере областей только для администраторов](https://github.com/Azure-Samples/active-directory-dotnet-admin-restricted-scopes-v2).

### <a name="request-the-permissions-in-the-app-registration-portal"></a>Запрос разрешений на портале регистрации приложения

Согласие администратора не принимает параметр области, поэтому любые запрошенные разрешения нужно статически определить при регистрации приложения. Рекомендуем убедиться в том, что статически определенные разрешения для приложения являются супермножеством разрешений, запрашиваемых динамически или поэтапно.

Чтобы настроить список статически запрашиваемых разрешений для приложения, сделайте следующее: 
1. Перейдите к приложению на [портале регистрации приложения](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) или [создайте приложение](quickstart-v2-register-an-app.md), если это еще не сделано.
2. Найдите раздел **Разрешения Microsoft Graph** и добавьте разрешения, необходимые для приложения.
3. **Сохраните** регистрацию приложения.

### <a name="recommended-sign-the-user-in-to-your-app"></a>Выполнение входа пользователя в приложение (рекомендуется)

Как правило, при создании приложения, использующего конечную точку предоставления согласия администратора, для него нужно настроить страницу или представление, в котором администратор может утвердить разрешения приложения. Эта страница может быть частью потока регистрации приложения, параметров приложения или выделенного потока подключения. Во многих случаях разумно отображать это представление подключения в приложении только после того, как пользователь выполнил вход с помощью учебной или рабочей учетной записи Майкрософт.

При входе пользователя в приложение можно определить организацию, к которой принадлежит администратор, прежде чем запрашивать у него утверждение необходимых разрешений. Хотя это не обязательно, таким образом можно сделать пользовательский интерфейс более интуитивно понятным для сотрудников организации. Чтобы выполнить вход пользователя, следуйте нашим [указаниям в учебниках по протоколу версии 2.0](active-directory-v2-protocols.md).

### <a name="request-the-permissions-from-a-directory-admin"></a>Запрос разрешений у администратора каталога

Когда вы будете готовы запросить разрешения у администратора организации, можно будет перенаправить пользователя к *конечной точке предоставления согласия администратора* версии 2.0.

```
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/adminconsent?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&state=12345
&redirect_uri=http://localhost/myapp/permissions
```

```
// Pro tip: Try pasting the below request in a browser!
```

```
https://login.microsoftonline.com/common/adminconsent?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&state=12345&redirect_uri=http://localhost/myapp/permissions
```

| Параметр | Условие | ОПИСАНИЕ |
| --- | --- | --- |
| `tenant` | Обязательно | Клиент каталога, из которого необходимо запросить разрешение. Может быть указан как GUID или в формате понятного имени, а также использоваться в универсальной ссылке с элементом common, как показано в примере. |
| `client_id` | Обязательно | Идентификатор приложения, присвоенный приложению на [портале регистрации приложений](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList). |
| `redirect_uri` | Обязательно |Универсальный код ресурса (URI) перенаправления, на который нужно направлять ответ для обработки в приложении. Он должен в точности соответствовать одному из универсальных кодов ресурсов (URI) перенаправления, зарегистрированных на портале регистрации приложений. |
| `state` | Рекомендуется | Значение, включенное в запрос, которое также возвращается в ответе токена. Это может быть строка любого содержания. Используйте параметр state для кодирования сведений о состоянии пользователя в приложении перед выполнением запроса на аутентификацию. Например, это могут быть сведения об открытой на тот момент странице или представлении. |

На этом этапе Azure AD требует, чтобы администратор клиента вошел в систему, чтобы выполнить запрос. Администратору предлагается утвердить все разрешения, запрошенные для приложения на портале регистрации приложений.

#### <a name="successful-response"></a>Успешный ответ

Если администратор утверждает разрешения для приложения, то успешный ответ будет выглядеть следующим образом.

```
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| Параметр | ОПИСАНИЕ |
| --- | --- | --- |
| `tenant` | Клиент каталога, предоставивший приложению запрошенные разрешения, в виде GUID. |
| `state` | Значение, включенное в запрос, которое также возвращается в ответе маркера. Это может быть строка любого содержания. Параметр state используется для кодирования сведений о состоянии пользователя в приложении перед созданием запроса на аутентификацию, например сведений об открытой на тот момент странице или представлении. |
| `admin_consent` | Будет присвоено значение **true**. |

#### <a name="error-response"></a>Сообщение об ошибке

Если администратор не утверждает разрешения для приложения, то сообщение о неудачном выполнении будет выглядеть следующим образом.

```
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| Параметр | ОПИСАНИЕ |
| --- | --- | --- |
| `error` |Строка кода ошибки, которую можно использовать для классификации типов возникающих ошибок и реагирования на них. |
| `error_description` |Конкретное сообщение об ошибке, с помощью которого разработчик может определить причину возникновения ошибки. |

После получения успешного ответа от конечной точки предоставления согласия администратора приложение получит запрошенные разрешения. Далее вы можете запросить маркер для ресурса, который вам нужен.

## <a name="using-permissions"></a>Использование разрешений

После того как пользователь предоставит разрешения для приложения, оно может получать маркеры доступа, представляющие разрешение приложения на определенный уровень доступа к ресурсу. Маркер доступа можно использовать только для отдельного ресурса, но в этом маркере закодированы все разрешения, предоставленные приложению для данного ресурса. Чтобы получить маркер доступа, приложение может отправить запрос к конечной точке маркеров версии 2.0, как показано ниже.

```
POST common/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/json

{
    "grant_type": "authorization_code",
    "client_id": "6731de76-14a6-49ae-97bc-6eba6914391e",
    "scope": "https://outlook.office.com/mail.read https://outlook.office.com/mail.send",
    "code": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq..."
    "redirect_uri": "https://localhost/myapp",
    "client_secret": "zc53fwe80980293klaj9823"  // NOTE: Only required for web apps
}
```

Полученный маркер доступа можно использовать в HTTP-запросах к ресурсу. Он достоверно указывает ресурсу, что у приложения имеется необходимое разрешение для выполнения определенной задачи. 

Дополнительные сведения о протоколе OAuth 2.0 и способах получения маркеров доступа можно найти в [справочнике по протоколу конечной точки версии 2.0](active-directory-v2-protocols.md).

## <a name="troubleshooting"></a>Устранение неполадок

Если в процессе предоставления согласия для приложения вы или ваши пользователи обнаружили непредвиденные ошибки, обратитесь к этой статье для устранения неполадок: [Непредвиденная ошибка при предоставлении согласия для приложения](../manage-apps/application-sign-in-unexpected-user-consent-error.md).
