---
title: Основные сведения о манифесте приложения Azure Active Directory | Документация Майкрософт
description: Подробное описание манифеста приложения Azure Active Directory, который представляет конфигурацию удостоверений приложения в клиенте Azure AD и используется для упрощения авторизации OAuth, предоставления согласия и многого другого.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 4804f3d4-0ff1-4280-b663-f8f10d54d184
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/27/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: sureshja, justhu
ms.openlocfilehash: f336771da334ffd964ecd032654b8549b7a5e847
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/28/2018
ms.locfileid: "43127026"
---
# <a name="azure-active-directory-app-manifest"></a>Манифест приложения Azure Active Directory

Приложения, которые интегрируются с Azure Active Directory (Azure AD), необходимо зарегистрировать в клиенте Azure AD. Вы можете настроить приложение на [портале Azure](https://portal.azure.com), выбрав **Azure Active Directory** и приложение, которое необходимо настроить, а затем щелкнув **Манифест**.

## <a name="manifest-reference"></a>Справка по манифесту

> [!NOTE]
> Если вы не видите описания, увеличьте окно браузера или прокрутите список, чтобы увидеть описания.

>[!div class="mx-tdBreakAll"]
>[!div class="mx-tdCol2BreakAll"]

| Ключ  | Тип значения | Пример значения | ОПИСАНИЕ  |
|---------|---------|---------|---------|
| `appID` | Строка идентификатора | `"601790de-b632-4f57-9523-ee7cb6ceba95"` | Указывает уникальный идентификатор приложения, который назначается приложению в Azure AD. |
| `appRoles` | Тип массива | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"allowedMemberTypes": [<br>&emsp;&nbsp;&nbsp;&nbsp;"User"<br>&nbsp;&nbsp;&nbsp;],<br>&nbsp;&nbsp;&nbsp;"description":"Read-only access to device information",<br>&nbsp;&nbsp;&nbsp;"displayName":"Read Only",<br>&nbsp;&nbsp;&nbsp;"id":guid,<br>&nbsp;&nbsp;&nbsp;"isEnabled":true,<br>&nbsp;&nbsp;&nbsp;"value":"ReadOnly"<br>&nbsp;&nbsp;}<br>]</code> | Указывает коллекцию ролей, которые может объявить приложение. Эти роли могут назначаться пользователям, группам или субъектам-службам. |
| `availableToOtherTenants`| Логическое | `true` | Если установлено значение true, приложение доступно для других клиентов. Если установлено значение false, приложение будет доступно только клиенту, в котором оно зарегистрировано. Дополнительные сведения см. в статье [Как реализовать вход любого пользователя Azure Active Directory с помощью шаблона мультитенантного приложения](howto-convert-app-to-be-multi-tenant.md). |
| `displayName` | строка | `MyRegisteredApp` | Отображаемое имя приложения. |
| `errorURL` | строка | `http://MyRegisteredAppError` | URL-адрес для ошибок, возникающих в приложении. |
| `groupMembershipClaims` | строка | `1` | Битовая маска, настраивающая утверждение `groups`, выданное в маркере пользователя или маркере доступа OAuth 2.0, которого ожидает приложение. Доступны следующие значения битовой маски:<br>0 — нет.<br>1 — группы безопасности и роли Azure AD.<br>2 — зарезервировано.<br>4 — зарезервировано.<br>Если задать битовой маске значение 7, это предоставит все группы безопасности, группы рассылки и роли каталога Azure AD, членом которых является выполнивший вход пользователь. |
| `optionalClaims` | строка | `null` | Необязательные утверждения, возвращаемые в маркере службой токенов безопасности для этого конкретного приложения. Дополнительные сведения см. в статье [Необязательные утверждения в Azure AD (предварительная версия)](active-directory-optional-claims.md). |
| `acceptMappedClaims` | Логическое | `true` | Если установлено значение `true`, приложение может использовать сопоставление утверждений без указания пользовательского ключа подписи. |
| `homepage` | строка | `http://MyRegisteredApp` | Указывает URL-адрес домашней страницы приложения. |
| `informationalUrls` | строка | <code>{<br>&nbsp;&nbsp;&nbsp;"privacy":"http://MyRegisteredApp/privacystatement",<br>&nbsp;&nbsp;&nbsp;"termsOfService":"http://MyRegisteredApp/termsofservice"<br>}</code> | Указывает ссылки на условия предоставления услуг и заявление о конфиденциальности приложения. Условия обслуживания и заявление о конфиденциальности отображаются в окне запроса согласия пользователя. Дополнительные сведения см. в статье [Условия обслуживания и заявление о конфиденциальности для зарегистрированных приложений Azure Active Directory](howto-add-terms-of-service-privacy-statement.md). |
| `identifierUris` | Массив строк | `http://MyRegistererdApp` | Определяемые пользователем URI, которые уникальным образом идентифицируют веб-приложение в клиенте Azure AD или в проверенном личном домене, если приложение является мультитенантным. |
| `keyCredentials` | Тип массива | <code>[<br>&nbsp;{<br>&nbsp;&nbsp;&nbsp;"customKeyIdentifier":null,<br>&nbsp;&nbsp;&nbsp;"endDate":"2018-09-13T00:00:00Z",<br>&nbsp;&nbsp;&nbsp;"keyId":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"startDate":"2017-09-12T00:00:00Z",<br>&nbsp;&nbsp;&nbsp;"type":"AsymmetricX509Cert",<br>&nbsp;&nbsp;&nbsp;"usage":"Verify",<br>&nbsp;&nbsp;&nbsp;"value":null<br>&nbsp;&nbsp;}<br>]</code> | Содержит ссылки на учетные данные, назначенные приложению, строковые общие секреты и сертификаты X.509. Эти учетные данные используются при запросе маркеров доступа (когда приложение работает в качестве клиента, а не ресурса). |
| `knownClientApplications` | Тип массива | `[GUID]` | Используется для объединения согласия, если у вас есть решение, которое содержит две части: клиентское приложение и пользовательское приложение веб-API. Если в это значение ввести appID клиентского приложения, пользователю нужно будет только единожды предоставить согласие на использование клиентского приложения. Azure AD будет знать, что предоставление клиенту согласия подразумевает неявное одобрение веб-API, и автоматически предоставит субъекты-службы для клиентского приложения и для веб-API одновременно. И клиентское приложение, и веб-API должны быть зарегистрированы в одном и том же клиенте. |
| `logoutUrl` | строка | `http://MyRegisteredAppLogout` | URL-адрес для выхода из приложения. |
| `oauth2AllowImplicitFlow` | Логическое | `false` | Определяет, может ли это веб-приложение запрашивать маркеры неявного потока OAuth 2.0. Его значение по умолчанию — false. Этот флаг используется для браузерных приложений, таких как одностраничные приложения JavaScript. |
| `oauth2AllowUrlPathMatching` | Логическое | `false` | Указывает, будет ли Azure AD в рамках запросов маркеров OAuth 2.0 разрешать сопоставление путей URI перенаправления с replyUrls приложения. Его значение по умолчанию — false. |
| `oauth2Permissions` | Тип массива | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"adminConsentDescription":"Allow the app to access resources on behalf of the signed-in user.",<br>&nbsp;&nbsp;&nbsp;"adminConsentDisplayName":"Access resource1",<br>&nbsp;&nbsp;&nbsp;"id":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"isEnabled":true,<br>&nbsp;&nbsp;&nbsp;"type":"User",<br>&nbsp;&nbsp;&nbsp;"userConsentDescription":"Allow the app to access resource1 on your behalf.",<br>&nbsp;&nbsp;&nbsp;"userConsentDisplayName":"Access resources",<br>&nbsp;&nbsp;&nbsp;"value":"user_impersonation"<br>&nbsp;&nbsp;}<br>]</code> | Указывает коллекцию областей разрешений доступа OAuth 2.0, которые веб-API (ресурс) предоставляет клиентским приложениям. Эти области действия разрешений могут быть назначены клиентским приложениям во время предоставления согласия. |
| `oauth2RequiredPostResponse` | Логическое | `false` | Указывает, будет ли Azure AD в рамках запросов маркеров OAuth 2.0 разрешать запросы POST в отличие от запросов GET. Значение по умолчанию — false. В таком случае будут разрешены только запросы GET. |
| `objectId` | Строка идентификатора | `"f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd"` | Уникальный идентификатор для приложения в каталоге. Это не тот идентификатор, который используется для идентификации приложения в транзакциях протоколов. Он используется для ссылки на объект в запросах к каталогу. |
| `parentalControlSettings` | строка | <code>{<br>&nbsp;&nbsp;&nbsp;"countriesBlockedForMinors":[],<br>&nbsp;&nbsp;&nbsp;"legalAgeGroupRule":"Allow"<br>} </code> | `countriesBlockedForMinors` указывает страны, в которых приложение заблокировано для несовершеннолетних.<br>`legalAgeGroupRule` определяет правило возрастной группы, применяемое к пользователям приложения. Можно установить `Allow`, `RequireConsentForPrivacyServices`, `RequireConsentForMinors`, `RequireConsentForKids` или `BlockMinors`.  |
| `passwordCredentials` | Тип массива | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"customKeyIdentifier":null,<br>&nbsp;&nbsp;&nbsp;"endDate":"2018-10-19T17:59:59.6521653Z",<br>&nbsp;&nbsp;&nbsp;"keyId":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"startDate":"2016-10-19T17:59:59.6521653Z",<br>&nbsp;&nbsp;&nbsp;"value":null<br>&nbsp;&nbsp;&nbsp;}<br>] </code> | См. описание свойства `keyCredentials`. |
| `publicClient` | Логическое | `false` | Указывает, является ли это приложение общедоступным клиентом (например, установленным приложением, которое работает на мобильном устройстве). По умолчанию для этого параметра используется значение false. |
| `replyUrls` | Массив строк | `"http://localhost"` | Это свойство с несколькими значениями хранит список зарегистрированных значений redirect_uri, которые Azure AD будет принимать в качестве назначений при возвращении маркеров. |
| `requiredResourceAccess` | Тип массива | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"resourceAppId":"00000002-0000-0000-c000-000000000000",<br>&nbsp;&nbsp;&nbsp;"resourceAccess":[<br>&nbsp;&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"id":"311a71cc-e848-46a1-bdf8-97ff7156d8e6",<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"type":"Scope"<br>&nbsp;&nbsp;&nbsp;}<br>&nbsp;&nbsp;]<br>&nbsp;}<br>] </code> | При динамическом согласии `requiredResourceAccess` обеспечивает процедуру использования согласия администратора и согласия пользователя для пользователей, которые используют статическое согласие. Однако не предоставляет процедуру использования согласия пользователя для общего случая.<br>`resourceAppId` — уникальный идентификатор для ресурса, доступ к которому нужен приложению. Это значение должно соответствовать appId, который был объявлен в целевом приложении ресурса.<br>`resourceAccess` — массив, в котором перечислены области разрешений OAuth 2.0 и роли приложений, которые требуются приложению из указанного ресурса. Содержит значения `id` и `type` указанных ресурсов. |
| `samlMetadataUrl` | строка | `http://MyRegisteredAppSAMLMetadata` | URL-адрес метаданных SAML для приложения. |

## <a name="next-steps"></a>Дополнительная информация

* Дополнительные сведения о связи между объектами приложения и субъекта-службы см. в статье [Объекты приложения и субъекта-службы в Azure Active Directory (Azure AD)](app-objects-and-service-principals.md).
* Определения некоторых основных понятий для разработчиков Azure Active Directory (AD) приведены в статье [Глоссарий по Azure Active Directory для разработчика](developer-glossary.md).

Оставляйте свои замечания и пожелания в разделе ниже. Ваши отзывы помогают нам улучшать содержимое веб-сайта.

<!--article references -->
[AAD-APP-OBJECTS]:app-objects-and-service-principals.md
[AAD-DEVELOPER-GLOSSARY]:developer-glossary.md
[AAD-GROUPS-FOR-AUTHORIZATION]: http://www.dushyantgill.com/blog/2014/12/10/authorization-cloud-applications-using-ad-groups/
[ADD-UPD-RMV-APP]:quickstart-v1-integrate-apps-with-azure-ad.md
[APPLICATION-ENTITY]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[APPLICATION-ENTITY-APP-ROLE]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#approle-type
[APPLICATION-ENTITY-OAUTH2-PERMISSION]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permission-type
[AZURE-PORTAL]: https://portal.azure.com
[DEV-GUIDE-TO-AUTH-WITH-ARM]: http://www.dushyantgill.com/blog/2015/05/23/developers-guide-to-auth-with-azure-resource-manager-api/
[GRAPH-API]: active-directory-graph-api.md
[IMPLICIT-GRANT]:v1-oauth2-implicit-grant-flow.md
[INTEGRATING-APPLICATIONS-AAD]: https://azure.microsoft.com/documentation/articles/active-directory-integrating-applications/
[O365-PERM-DETAILS]: https://msdn.microsoft.com/office/office365/HowTo/application-manifest
[O365-SERVICE-DAEMON-APPS]: https://msdn.microsoft.com/office/office365/howto/building-service-apps-in-office-365
[RBAC-CLOUD-APPS-AZUREAD]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/

