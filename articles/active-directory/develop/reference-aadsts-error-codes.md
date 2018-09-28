---
title: Проверка подлинности Azure Active Directory и коды ошибок авторизации | Документация Майкрософт
description: Сведения о кодах ошибок AADSTS, которые возвращаются службой маркеров безопасности Azure AD.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: hirsin, justhu
ms.custom: aaddev
ms.openlocfilehash: 0643ae39d24f8ef0c1c1c18dcf73cf840c95e277
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46956339"
---
# <a name="authentication-and-authorization-error-codes"></a>Коды ошибок проверки подлинности и авторизации

Вам нужна информация о кодах ошибок AADSTS, которые возвращаются службой маркеров безопасности (STS) Azure Active Directory (Azure AD)? В этом документе приводятся описания ошибок AADSTS, методы их исправления и некоторые рекомендации по обходным путям.

> [!NOTE]
> Эта информация является предварительной и подлежит изменению. У вас есть вопрос или не можете найти нужную информацию? Создайте запрос в GitHub или изучите статью [Возможности получения поддержки и справки для разработчиков](active-directory-develop-help-support.md), чтобы узнать о других способах получения поддержки и справки.

## <a name="aadsts-error-codes"></a>Коды ошибок AADSTS

| Ошибка | ОПИСАНИЕ |
|---|---|
| AADSTS16000 | SelectUserAccount — это прерывание создается Azure AD и вызывает пользовательский интерфейс, в котором пользователь может выбрать один из нескольких допустимых сеансов единого входа. Эта ошибка возникает довольно часто и может возвращаться в приложение, если указан параметр `prompt=none`. |
| AADSTS16001 | UserAccountSelectionInvalid — такую ошибку вы получите, когда пользователь щелкнет плитку, отклоненную логикой выбора сеанса. Когда возникает эта ошибка, пользователю предоставляется возможность выбрать плитку сеанса из обновленного списка или выбрать другую учетную запись. Такие ошибки возникают из-за ошибок в коде или состояния гонки. |
| AADSTS16002 | AppSessionSelectionInvalid — не соблюдено требование приложения к идентификатору безопасности.  |
| AADSTS16003 | SsoUserAccountNotFoundInResourceTenant |
| AADSTS17003 | CredentialKeyProvisioningFailed |
| AADSTS20001 | WsFedSignInResponseError — возникла проблема с федеративным поставщиком удостоверений. Чтобы решить эту проблему, обратитесь к поставщику удостоверений. |
| AADSTS20012 | WsFedMessageInvalid — возникла проблема с федеративным поставщиком удостоверений. Чтобы решить эту проблему, обратитесь к поставщику удостоверений. |
| AADSTS20033 | FedMetadataInvalidTenantName — возникла проблема с федеративным поставщиком удостоверений. Чтобы решить эту проблему, обратитесь к поставщику удостоверений. |
| AADSTS40008 | OAuth2IdPUnretryableServerError — возникла проблема с федеративным поставщиком удостоверений. Чтобы решить эту проблему, обратитесь к поставщику удостоверений. |
| AADSTS40009 | OAuth2IdPRefreshTokenRedemptionUserError — возникла проблема с федеративным поставщиком удостоверений. Чтобы решить эту проблему, обратитесь к поставщику удостоверений. |
| AADSTS40010 | OAuth2IdPRetryableServerError — возникла проблема с федеративным поставщиком удостоверений. Чтобы решить эту проблему, обратитесь к поставщику удостоверений. |
| AADSTS40015 | OAuth2IdPAuthCodeRedemptionUserError — возникла проблема с федеративным поставщиком удостоверений. Чтобы решить эту проблему, обратитесь к поставщику удостоверений. |
| AADSTS50000 | TokenIssuanceError — возникла проблема со службой входа. Чтобы решить эту проблему, [отправьте запрос в службу поддержки](../fundamentals/active-directory-troubleshooting-support-howto.md). |
| AADSTS50001 | InvalidResource — запрошенный ресурс отключен или не существует. Проверьте код приложения и убедитесь, что вы указали правильный URL-адрес ресурса, к которому пытаетесь получить доступ.  |
| AADSTS50002 | NotAllowedTenant — не удалось войти в систему из-за ограничений прокси-доступа в клиенте. Если политикой клиента управляете вы, измените параметры ограничений для клиента, чтобы решить проблему. |
| AADSTS50003 | MissingSigningKey — не удалось войти в систему из-за отсутствия ключа подписывания или сертификата. Возможно, в приложении не настроен ключ подписывания. Ознакомьтесь с решениями, приведенными по адресу [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#certificate-or-key-not-configured](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#certificate-or-key-not-configured). Если проблемы не удалось решить, обратитесь к владельцу или администратору приложения. |
| AADSTS50005 | DevicePolicyError — пользователь пытался войти на устройство с платформы, для которой сейчас не поддерживается политика условного доступа. |
| AADSTS50006 | InvalidSignature — не удалось проверить подпись по причине ее недействительности. |
| AADSTS50007 | PartnerEncryptionCertificateMissing — не найден сертификат шифрования партнера для этого приложения. Чтобы решить эту проблему, [отправьте запрос в службу поддержки](../fundamentals/active-directory-troubleshooting-support-howto.md) Майкрософт. |
| AADSTS50008 | InvalidSamlToken — в маркере отсутствует или неправильно настроено утверждение SAML. Обратитесь к федеративному поставщику. |
| AADSTS50010 | AudienceUriValidationFailed — для приложения не удалось проверить URI аудитории, так как аудитории маркеров не настроены. |
| AADSTS50011 | InvalidReplyTo — обратный адрес отсутствует, неправильно настроен или не соответствует обратным адресам, настроенным для приложения. Попробуйте использовать решение, приведенное по адресу [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application). Если проблемы не удалось решить, обратитесь к владельцу или администратору приложения. |
| AADSTS50012 | AuthenticationFailed — сбой проверки подлинности по одной из следующих причин:<ul><li>не авторизовано имя субъекта сертификата для подписи;</li><li>для авторизованного имени субъекта не найдена соответствующая политика сопоставления доверенного центра сертификации;</li><li>недопустимая цепочка сертификатов;</li><li>недействительный сертификат для подписи;</li><li>в клиенте не настроена политика;</li><li>не авторизован отпечаток сертификата для подписи;</li><li>утверждение клиента содержит недопустимую подпись.</li></ul> |
| AADSTS50013 | InvalidAssertion — утверждение недопустимо по любой из причин: издатель не указал версию API в пределах допустимого диапазона времени; истек срок действия; используется недопустимый формат; маркер обновления в утверждении не является основным. |
| AADSTS50014 | GuestUserInPendingState |
| AADSTS50015 | ViralUserLegalAgeConsentRequiredState |
| AADSTS50017 | CertificateValidationFailed — сбой проверки сертификации по одной из следующих причин:<ul><li>не удалось найти сертификат поставщика в списке доверенных сертификатов;</li><li>не удалось найти ожидаемый сегмент CrlSegment;</li><li>не удалось найти сертификат поставщика в списке доверенных сертификатов;</li><li>точка распространения разностного CRL настроена без соответствующей точки распространения CRL;</li><li>не удалось получить допустимые сегменты CRL из-за проблемы со временем ожидания;</li><li>не удалось скачать CRL.</li></ul>Обратитесь к администратору клиента. |
| AADSTS50020 | UserUnauthorized — пользователи не имеют разрешения для вызова этой конечной точки. |
| AADSTS50027 | InvalidJwtToken — недопустимый маркер JWT. Возможные причины:<ul><li>отсутствует утверждение nonce, вложенное утверждение;</li><li>несовпадение идентификаторов субъектов;</li><li>повторяющееся утверждение в утверждениях idToken;</li><li>непредвиденный издатель;</li><li>непредвиденная аудитория;</li><li>превышен допустимый диапазон времени; </li><li>неправильный формат маркера;</li><li>Внешний идентификатор маркера от издателя не прошел проверку подписи.</li></ul> |
| AADSTS50029 | Недопустимый URI — доменное имя содержит недопустимые символы. Обратитесь к администратору клиента. |
| AADSTS50032 | WeakRsaKey — обозначает попытку пользователя использовать слабый ключ RSA. |
| AADSTS50033 | RetryableError — обозначает временную ошибку, которая не связана с операциями базы данных. |
| AADSTS50034 | UserAccountNotFound — чтобы войти в приложение, учетная запись должна быть добавлена в каталог. |
| AADSTS50042 | UnableToGeneratePairwiseIdentifierWithMissingSalt — в субъекте нет случайных данных, которые требуются для создания парного идентификатора. Обратитесь к администратору клиента. |
| AADSTS50043 | UnableToGeneratePairwiseIdentifierWithMultipleSalts |
| AADSTS50048 | SubjectMismatchesIssuer — субъект не соответствует утверждению издателя в утверждении клиента. Обратитесь к администратору клиента. |
| AADSTS50049 | NoSuchInstanceForDiscovery — неизвестный или недопустимый экземпляр. |
| AADSTS50050 | MalformedDiscoveryRequest — неправильный формат запроса. |
| AADSTS50053 | IdsLocked — учетная запись заблокирована из-за большого количества попыток входа с неправильным идентификатором пользователя или паролем. |
| AADSTS50055 | InvalidPasswordExpiredPassword — истек срок действия пароля. |
| AADSTS50056 | Недопустимый или пустой пароль — в хранилище нет пароля для этого пользователя. |
| AADSTS50057 | UserDisabled — учетная запись пользователя отключена. Учетная запись отключена администратором. |
| AADSTS50058 | UserInformationNotProvided — означает, что пользователь не выполнил вход. Это очень распространенная ошибка, которая должна возникать, когда пользователь не прошел проверку подлинности и не выполнил вход.</br>Если эта ошибка вызывается в контексте единого входа, где пользователь уже выполнил вход, значит сеанс единого входа не найден или является недопустимым.</br>Такая ошибка может возвращаться в приложении, если указан параметр prompt=none. |
| AADSTS50059 | MissingTenantRealmAndNoUserInformationProvided — не найдены идентификационные сведения о клиенте в запросе или в предоставленных учетных данных. Пользователь может обратиться за помощью к администратору клиента. |
| AADSTS50061 | SignoutInvalidRequest — недопустимый запрос на выход. |
| AADSTS50064 | CredentialAuthenticationError |
| AADSTS50068 | SignoutInitiatorNotParticipant |
| AADSTS50070 | SignoutUnknownSessionIdentifier |
| AADSTS50071 | SignoutMessageExpired |
| AADSTS50072 | UserStrongAuthEnrollmentRequiredInterrupt — пользователь должен зарегистрироваться для двухфакторной (интерактивной) проверки подлинности. |
| AADSTS50074 | UserStrongAuthClientAuthNRequiredInterrupt — требуется строгая проверка подлинности, но пользователь не прошел проверку с запросом защиты MFA. |
| AADSTS50076 | UserStrongAuthClientAuthNRequired — пользователь должен использовать многофакторную проверку подлинности для доступа к этому ресурсу. Создайте новый запрос авторизации для доступа к ресурсу. |
| AADSTS50079 | UserStrongAuthEnrollmentRequired — из-за внесенного администратором изменения конфигурации или после перемещения в новое местоположение пользователю необходимо пройти многофакторную проверку подлинности. |
| AADSTS50085 | Для маркера обновления требуется имя входа IDP для социальных сетей. Пользователям следует выполнить повторную попытку входа с помощью имени пользователя и пароля. |
| AADSTS50086 | SasNonRetryableError |
| AADSTS50087 | SasRetryableError |
| AADSTS50089 | Срок действия маркера потока истек — проверка подлинности не пройдена. Пользователям следует выполнить повторную попытку входа с помощью имени пользователя и пароля. |
| AADSTS50097 | DeviceAuthenticationRequired — требуется проверка подлинности устройства. |
| AADSTS50099 | PKeyAuthInvalidJwtUnauthorized — недопустимая подпись JWT. |
| AADSTS50105 | EntitlementGrantsNotFound — выполнившему вход пользователю не назначена роль для приложения, в которое выполнен вход. Назначьте пользователю роль для этого приложения. Дополнительные сведения можно получить здесь: [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#user-not-assigned-a-role](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#user-not-assigned-a-role). |
| AADSTS50107 | InvalidRealmUri — запрошенный объект области федерации не существует. Обратитесь к администратору клиента. |
| AADSTS50120 | ThresholdJwtInvalidJwtFormat — проблема с заголовком JWT. Обратитесь к администратору клиента. |
| AADSTS50124 | ClaimsTransformationInvalidInputParameter — преобразование утверждений содержит недопустимый входной параметр. Обратитесь к администратору клиента, чтобы обновить политику. |
| AADSTS50125 | PasswordResetRegistrationRequiredInterrupt — вход был прерван из-за сброса пароля или записи регистрации пароля. |
| AADSTS50126 | InvalidUserNameOrPassword — ошибка при проверке учетных данных из-за недействительного имени пользователя или пароля. |
| AADSTS50127 | BrokerAppNotInstalled — пользователю нужно установить приложение брокера для получения доступа к этому содержимому. |
| AADSTS50128 | Недопустимое доменное имя: не найдены идентификационные сведения о клиенте в запросе или в предоставленных учетных данных.|
| AADSTS50129 | DeviceIsNotWorkplaceJoined — для регистрации устройства требуется подключение к рабочему месту. |
| AADSTS50131 | ConditionalAccessFailed — обозначает различные ошибки условного доступа, например неверное состояние устройства Windows, блокировку запроса из-за подозрительной активности, решений политики доступа или политики безопасности. |
| AADSTS50132 | SsoArtifactInvalidOrExpired — сеанс не является допустимым из-за истечения срока действия пароля или недавней смены пароля. |
| AADSTS50133 | SsoArtifactRevoked — сеанс не является допустимым из-за истечения срока действия пароля или недавней смены пароля. |
| AADSTS50134 | DeviceFlowAuthorizeWrongDatacenter |
| AADSTS50135 | PasswordChangeCompromisedPassword — требуется смена пароля из-за наличия риска для учетной записи. |
| AADSTS50136 | RedirectMsaSessionToApp — обнаружен один сеанс MSA. |
| AADSTS50139 | SessionMissingMsaOAuth2RefreshToken |
| AADSTS50140 | KmsiInterrupt — эта ошибка происходит из-за прерывания режима "Оставаться в системе" при входе пользователя. Чтобы получить дополнительные сведения, [отправьте запрос в службу поддержки](../fundamentals/active-directory-troubleshooting-support-howto.md), указав в нем идентификатор корреляции, идентификатор запроса и код ошибки. |
| AADSTS50143 | Несоответствие сеанса — сеанс недопустим, так как клиент пользователя не соответствует указанию домена из-за того, что указаны различные ресурсы. Чтобы получить дополнительные сведения, [отправьте запрос в службу поддержки](../fundamentals/active-directory-troubleshooting-support-howto.md), указав в нем идентификатор корреляции, идентификатор запроса и код ошибки. |
| AADSTS50144 | InvalidPasswordExpiredOnPremPassword — истек срок действия пароля Active Directory для пользователя. Создайте для этого пользователя новый пароль или попросите его применить средство самостоятельного сброса пароля. |
| AADSTS50146 | MissingCustomSigningKey — в приложении необходимо настроить ключ подписывания для конкретного приложения. Либо оно не настроено с помощью такого ключа, либо же ключ просрочен или еще не вступил в действие. |
| AADSTS50147 | MissingCodeChallenge |
| AADSTS50155 | DeviceAuthenticationFailed — проверка подлинности устройства для этого пользователя завершилась сбоем. |
| AADSTS50158 | ExternalSecurityChallenge — не пройдена проверка с внешним запросом защиты. |
| AADSTS50161 | InvalidExternalSecurityChallengeConfiguration — недостаточно утверждений, отправленных внешним поставщиком, или отсутствует запрос на утверждение внешнего поставщика. |
| AADSTS50166 | ExternalClaimsProviderThrottled — не удалось отправить запрос поставщику утверждений. |
| AADSTS50168 | ChromeBrowserSsoInterruptRequired — клиент поддерживает получение маркера единого входа через расширение учетных записей Windows 10, но этот маркер отсутствует в запросе или срок действия указанного маркера истек. |
| AADSTS50169 | InvalidRequestBadRealm — область не настроена для текущего пространства имен службы. |
| AADSTS50170 | MissingExternalClaimsProviderMapping |
| AADSTS50177 | ExternalChallengeNotSupportedForPassthroughUsers — внешний запрос не поддерживается для пользователей в сквозном режиме. |
| AADSTS50178 | SessionControlNotSupportedForPassthroughUsers — управление сеансом не поддерживается для пользователей в сквозном режиме. |
| AADSTS50180 | WindowsIntegratedAuthMissing — требуется встроенная проверка подлинности Windows. Включите в клиенте простой единый вход. |
| AADSTS50187 | DeviceInformationNotProvided |
| AADSTS51000 | RequiredFeatureNotEnabled |
| AADSTS51001 | DomainHintMustbePresent — должно присутствовать указание домена с локальным идентификатором безопасности или локальным именем участника-пользователя. |
| AADSTS51004 | UserAccountNotInDirectory — учетная запись пользователя не существует в каталоге. |
| AADSTS51005 | TemporaryRedirect — этот эквивалент HTTP-состояния 307 обозначает, что запрашиваемые данные расположены в URI, указанном в заголовке location. Получив такое состояние, переходите по адресу, указанному в заголовке location ответа. Если для исходного запроса использовался метод POST, перенаправляемый запрос также должен использовать метод POST. |
| AADSTS51006 | ForceReauthDueToInsufficientAuth — требуется встроенная проверка подлинности Windows. Пользователь вошел с маркером сеанса, в котором отсутствуют утверждения для встроенной проверки подлинности Windows. Запросите повторный вход для этого пользователя. |
| AADSTS52004 | DelegationDoesNotExistForLinkedIn — пользователь не предоставил согласие на доступ к ресурсам LinkedIn. |
| AADSTS53000 | DeviceNotCompliant — политики условного доступа требуют наличия соответствующего устройства, а это устройство не соответствует требованиям. Пользователю нужно зарегистрировать свое устройство у утвержденного поставщика MDM, например Intune. |
| AADSTS53001 | DeviceNotDomainJoined — политика условного доступа требует наличия присоединенного к домену устройства, а это устройство не присоединено к домену. Потребуйте, чтобы пользователь использовал присоединенное к домену устройство. |
| AADSTS53002 | ApplicationUsedIsNotAnApprovedApp — используемое приложение не утверждено для использования условного доступа. Чтобы получить доступ, нужно использовать приложение, входящее в список утвержденных. |
| AADSTS53003 | BlockedByConditionalAccess — доступ заблокирован политиками условного доступа. Политика доступа не разрешает выдачу маркеров. |
| AADSTS53004 | ProofUpBlockedDueToRisk — прежде чем обращаться к запрошенному содержимому, пользователю нужно пройти регистрацию для многофакторной проверки подлинности. Пользователю нужно зарегистрироваться для многофакторной проверки подлинности. |
| AADSTS54000 | MinorUserBlockedLegalAgeGroupRule |
| AADSTS65001 | DelegationDoesNotExist — пользователь или администратор не предоставили согласие на использование приложения с идентификатором X. Отправьте интерактивный запрос на проверку подлинности для этого пользователя и ресурса. |
| AADSTS65004 | UserDeclinedConsent — пользователь отказался предоставить согласие на доступ к приложению. Пользователю нужно повторить попытку входа и дать согласие на доступ к приложению.|
| AADSTS65005 | MisconfiguredApplication — список доступа к обязательным ресурсам приложения не содержит приложений, доступных для ресурса, или клиентское приложение запросило доступ к ресурсу, который не был указан в списке доступа к обязательным ресурсам, или служба Graph вернула недопустимый запрос, либо же ресурс не был найден. Если приложение поддерживает SAML, возможно, для приложения указан неправильный идентификатор (сущность). Попробуйте использовать разрешения для SAML, приведенные по следующей ссылке: [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#no-resource-in-requiredresourceaccess-list](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery?/?WT.mc_id=DMC_AAD_Manage_Apps_Troubleshooting_Nav#no-resource-in-requiredresourceaccess-list) |
| AADSTS67003 | ActorNotValidServiceIdentity |
| AADSTS70000 | InvalidGrant — сбой проверки подлинности. Маркер обновления является недопустимым. Эта ошибка может быть вызвана следующими причинами:<ul><li>заголовок привязки в маркере пуст;</li><li>хэш привязки маркера не совпадает с ожидаемым.</li></ul> |
| AADSTS70001 | UnauthorizedClient — приложение отключено. |
| AADSTS70002 | InvalidClient — ошибка при проверке учетных данных. Указанное значение client_secret не соответствует ожидаемому значению для этого клиента. Исправьте значение client_secret и повторите попытку. Дополнительные сведения см. в разделе [Использование кода авторизации для запроса маркера доступа](v1-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token). |
| AADSTS70003 | UnsupportedGrantType — приложение вернуло недопустимый тип предоставления разрешения. |
| AADSTS70004 | InvalidRedirectUri — приложение вернуло недопустимый URI перенаправления. Адрес перенаправления, указанный с помощью клиента, не соответствует ни одному настроенному адресу или же не совпадает ни с одним из адресов в списке адресов, утвержденных OIDC. |
| AADSTS70005 | UnsupportedResponseType — приложение вернуло неподдерживаемый тип ответа. Возможные причины:<ul><li>для приложения не включен тип ответа token;</li><li>для типа ответа id_token требуется область OpenID — содержит неподдерживаемое значение параметра OAuth в кодировке wctx.</li></ul> |
| AADSTS70007 | UnsupportedResponseMode — приложение вернуло неподдерживаемое значение `response_mode` при запросе маркера.  |
| AADSTS70008 | ExpiredOrRevokedGrant — истек срок действия маркера обновления из-за отсутствия активности. Маркер был выдан XXX и не использовался в течение определенного времени. |
| AADSTS70011 | InvalidScope — приложение запрашивает недопустимую область. |
| AADSTS70012 | MsaServerError — при проверке подлинности пользователя (объекта-получателя) MSA произошла ошибка сервера. Повторите попытку. Если по-прежнему происходит сбой, [отправьте запрос в службу поддержки](../fundamentals/active-directory-troubleshooting-support-howto.md). |
| AADSTS70016 | AuthorizationPending — ошибка с расширением OAuth 2.0 Device Flow. Ожидается авторизация. Устройство будет повторять опрашивающие запросы. |
| AADSTS70018 | BadVerificationCode — пользователь ввел неправильный код проверки для последовательности кода авторизации устройства. Авторизация не подтверждена. |
| AADSTS70019 | CodeExpired — истек срок действия кода проверки. Потребуйте, чтобы пользователь повторил попытку входа. |
| AADSTS75001 | BindingSerializationError — ошибка в процессе привязки сообщений SAML. |
| AADSTS75003 | UnsupportedBindingError — приложение вернуло сообщение об ошибке, связанной с неподдерживаемой привязкой (ответ протокола SAML невозможно отправить с помощью привязок, отличных от HTTP POST). |
| AADSTS75005 | Saml2MessageInvalid — Azure AD не поддерживает отправку запросов SAML для единого входа из приложений. |
| AADSTS75008 | RequestDeniedError — запрос от приложения отклонен, так как назначение запроса SAML отличается от ожидаемого. |
| AADSTS75011 | NoMatchedAuthnContextInOutputClaims — метод проверки подлинности, примененный пользователем для службы, не соответствует запрошенному. |
| AADSTS75016 | Saml2AuthenticationRequestInvalidNameIDPolicy — запрос проверки подлинности SAML2 имеет недопустимое значение NameIdPolicy. |
| AADSTS80001 | OnPremiseStoreIsNotAvailable — агенту проверки подлинности не удалось подключиться к Active Directory. Убедитесь, что серверы агентов являются членами того же леса, что и пользователи, чьи пароли должны быть проверены, и могут подключиться к Active Directory. |
| AADSTS80002 | OnPremisePasswordValidatorRequestTimedout — истекло время ожидания для запроса на проверку пароля. Убедитесь, что служба Active Directory доступна и отвечает на запросы агентов. |
| AADSTS80005 | OnPremisePasswordValidatorUnpredictableWebException — неизвестная ошибка при обработке ответа от агента проверки подлинности. Повторите запрос. Если ошибку не удалось устранить, [отправьте запрос в службу поддержки](../fundamentals/active-directory-troubleshooting-support-howto.md), чтобы получить дополнительные сведения об этой ошибке. |
| AADSTS80007 | OnPremisePasswordValidatorErrorOccurredOnPrem — агенту проверки подлинности не удалось проверить пароль пользователя. Найдите дополнительные сведения в журналах агентов и убедитесь, что Active Directory работает должным образом. |
| AADSTS80010 | OnPremisePasswordValidationEncryptionException — агенту проверки подлинности не удалось расшифровать пароль. |
| AADSTS80012 | OnPremisePasswordValidationAccountLogonInvalidHours — пользователи пытались войти в систему вне допустимого диапазона времени (который указан в AD). |
| AADSTS80013 | OnPremisePasswordValidationTimeSkew — не удалось выполнить проверку подлинности из-за сдвига времени между компьютером, на котором запущен агент проверки подлинности, и AD. Устраните проблемы с синхронизацией времени. |
| AADSTS81004 | DesktopSsoIdentityInTicketIsNotAuthenticated — сбой при попытке проверки подлинности Kerberos. |
| AADSTS81005 | DesktopSsoAuthenticationPackageNotSupported — пакет проверки подлинности не поддерживается. |
| AADSTS81006 | DesktopSsoNoAuthorizationHeader — не найден заголовок авторизации. |
| AADSTS81007 | DesktopSsoTenantIsNotOptIn — для клиента не включен простой единый вход. |
| AADSTS81009 | DesktopSsoAuthorizationHeaderValueWithBadFormat — не удалось проверить билет Kerberos пользователя. |
| AADSTS81010 | DesktopSsoAuthTokenInvalid — не удалось выполнить простой единый вход, так как срок действия билета Kerberos пользователя истек или этот билет недопустим. |
| AADSTS81011 | DesktopSsoLookupUserBySidFailed — не удалось найти объект пользователя на основе сведений в билете Kerberos пользователя. |
| AADSTS81012 | DesktopSsoMismatchBetweenTokenUpnAndChosenUpn — пользователь, пытающийся выполнить вход в Azure AD, и пользователь, вошедший на устройство, отличаются. |
| AADSTS90002 | InvalidTenantName — имя клиента не найдено в хранилище данных. Убедитесь, что идентификатор клиента указан правильно. |
| AADSTS90004 | InvalidRequestFormat |
| AADSTS90005 | InvalidRequestWithMultipleRequirements |
| AADSTS90006 | ExternalServerRetryableError |
| AADSTS90007 | InvalidSessionId |
| AADSTS90008 | TokenForItselfRequiresGraphPermission |
| AADSTS90009 | TokenForItselfMissingIdenticalAppIdentifier |
| AADSTS90010 | NotSupported |
| AADSTS90012 | RequestTimeout |
| AADSTS90013 | InvalidUserInput |
| AADSTS90014 | MissingRequiredField — такой код ошибки может возникать в нескольких ситуациях, когда в учетных данных отсутствует ожидаемое поле. |
| AADSTS90015 | QueryStringTooLong |
| AADSTS90016 | MissingRequiredClaim |
| AADSTS90019 | MissingTenantRealm — Azure AD не удалось определить идентификатор клиента из запроса. |
| AADSTS90022 | AuthenticatedInvalidPrincipalNameFormat — неправильный формат имени субъекта или не соблюдается ожидаемый формат `name[/host][@realm]`. Имя субъекта является обязательным параметром, а узел и область — необязательные параметры. Их можно оставить пустыми (NULL). |
| AADSTS90023 | InvalidRequest |
| AADSTS90024 | RequestBudgetExceededError |
| AADSTS90033 | MsodsServiceUnavailable |
| AADSTS90036 | MsodsServiceUnretryableFailure |
| AADSTS90038 | NationalCloudTenantRedirection — указанный клиент "Y" принадлежит национальному облаку "X". Текущий экземпляр облака "Z" не имеет федерации с "X". Возвращается ошибка перенаправления облачной службы. |
| AADSTS90043 | NationalCloudAuthCodeRedirection |
| AADSTS90051 | InvalidNationalCloudId |
| AADSTS90055 | TenantThrottlingError — слишком много входящих запросов. Такое исключение создается для заблокированных клиентов. |
| AADSTS90056 | BadResourceRequest — чтобы получить маркер доступа по коду, приложение должно отправить запрос POST на конечную точку `/token`. Кроме того, перед этим следует предоставить код авторизации и отправить его в запросе POST на конечную точку `/token`. Обзор последовательности кода авторизации OAuth 2.0 вы найдете в этой статье: [https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code). Сначала следует направить пользователя на конечную точку `/authorize`, которая возвращает код авторизации. По запросу на конечную точку `/token` пользователь получает маркер доступа. Войдите на портал Azure и выберите **Регистрация приложений > Конечные точки**, чтобы проверить правильность настройки двух конечных точек. |
| AADSTS90072 | PassThroughUserMfaError |
| AADSTS90081 | OrgIdWsFederationMessageInvalid |
| AADSTS90082 | OrgIdWsFederationNotSupported |
| AADSTS90084 | OrgIdWsFederationGuestNotAllowed |
| AADSTS90085 | OrgIdWsFederationSltRedemptionFailed |
| AADSTS90086 | OrgIdWsTrustDaTokenExpired |
| AADSTS90087 | OrgIdWsFederationMessageCreationFromUriFailed |
| AADSTS90090 | GraphRetryableError |
| AADSTS90091 | GraphServiceUnreachable |
| AADSTS90092 | GraphNonRetryableError |
| AADSTS90093 | GraphUserUnauthorized — возвращается граф с кодом ошибки "Запрещено" для полученного запроса. |
| AADSTS90094 | AdminConsentRequired |
| AADSTS90100 | InvalidRequestParameter |
| AADSTS90101 | InvalidEmailAddress |
| AADSTS90102 | InvalidUriParameter |
| AADSTS90107 | InvalidXml |
| AADSTS90114 | InvalidExpiryDate |
| AADSTS90117 | InvalidRequestInput |
| AADSTS90119 | InvalidUserCode |
| AADSTS90120 | InvalidDeviceFlowRequest |
| AADSTS90121 | InvalidEmptyRequest |
| AADSTS90123 | IdentityProviderAccessDenied |
| AADSTS90124 | V1ResourceV2GlobalEndpointNotSupported |
| AADSTS90125 | DebugModeEnrollTenantNotFound |
| AADSTS90126 | DebugModeEnrollTenantNotInferred |
| AADSTS90130 | NonConvergedAppV2GlobalEndpointNotSupported |
| AADSTS120000 | PasswordChangeIncorrectCurrentPassword |
| AADSTS120002 | PasswordChangeInvalidNewPasswordWeak |
| AADSTS120003 | PasswordChangeInvalidNewPasswordContainsMemberName |
| AADSTS120004 | PasswordChangeOnPremComplexity |
| AADSTS120005 | PasswordChangeOnPremSuccessCloudFail |
| AADSTS120008 | PasswordChangeAsyncJobStateTerminated |
| AADSTS120011 | PasswordChangeAsyncUpnInferenceFailed |
| AADSTS120012 | PasswordChangeNeedsToHappenOnPrem |
| AADSTS120013 | PasswordChangeOnPremisesConnectivityFailure |
| AADSTS120014 | PasswordChangeOnPremUserAccountLockedOutOrDisabled |
| AADSTS120015 | PasswordChangeADAdminActionRequired |
| AADSTS120016 | PasswordChangeUserNotFoundBySspr |
| AADSTS120018 | PasswordChangePasswordDoesnotComplyFuzzyPolicy |
| AADSTS120020 | PasswordChangeFailure |
| AADSTS120021 | PartnerServiceSsprInternalServiceError |
| AADSTS130004 | NgcKeyNotFound |
| AADSTS130005 | NgcInvalidSignature |
| AADSTS130006 | NgcTransportKeyNotFound |
| AADSTS130007 | NgcDeviceIsDisabled |
| AADSTS130008 | NgcDeviceIsNotFound |
| AADSTS135010 | KeyNotFound |
| AADSTS140000 | InvalidRequestNonce |
| AADSTS140001 | InvalidSessionKey |
| AADSTS165000 | InvalidRequestCanary |
| AADSTS165004 | InvalidApiCanary |
| AADSTS165900 | InvalidApiRequest |
| AADSTS220450 | UnsupportedAndroidWebViewVersion |
| AADSTS220501 | InvalidCrlDownload |
| AADSTS221000 | DeviceOnlyTokensNotSupportedByResource — ресурс не настроен на принятие маркеров только для устройств. |
| AADSTS240001 | BulkAADJTokenUnauthorized |
| AADSTS240002 | RequiredClaimIsMissing |
| AADSTS700020 | InteractionRequired |
| AADSTS700022 | InvalidMultipleResroucesScope |
| AADSTS700023 | InvalidResourcelessScope |
| AADSTS900000 | WebWatsonEnvironmentError |
| AADSTS1000000 | UserNotBoundError |
| AADSTS1000002 | BindCompleteInterruptError |

## <a name="next-steps"></a>Дополнительная информация

* У вас есть вопрос или не можете найти нужную информацию? Создайте запрос в GitHub или изучите статью [Возможности получения поддержки и справки для разработчиков](active-directory-develop-help-support.md), чтобы узнать о других способах получения поддержки и справки.