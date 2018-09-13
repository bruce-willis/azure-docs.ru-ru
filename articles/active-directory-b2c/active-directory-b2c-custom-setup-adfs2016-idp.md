---
title: Добавление ADFS в качестве поставщика удостоверений SAML с помощью пользовательских политик в Azure Active Directory B2C | Документация Майкрософт
description: Настройка ADFS 2016 с помощью протокола SAML и пользовательских политик в Azure Active Directory B2C
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/31/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 2c2e6861fda42a9e8c1aabcba303bfede47ac3c1
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/04/2018
ms.locfileid: "43669232"
---
# <a name="add-adfs-as-a-saml-identity-provider-using-custom-policies-in-azure-active-directory-b2c"></a>Добавление ADFS в качестве поставщика удостоверений SAML с помощью пользовательских политик в Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

В этой статье описывается включение входа учетных записей пользователей ADFS с помощью [пользовательских политик](active-directory-b2c-overview-custom.md) в Azure Active Directory (Azure AD) B2C.

## <a name="prerequisites"></a>Предварительные требования

Выполните шаги, описанные в статье [Azure Active Directory B2C. Приступая к работе с настраиваемыми политиками](active-directory-b2c-get-started-custom.md).

## <a name="add-the-adfs-account-application-key-to-azure-ad-b2c"></a>Добавление ключа приложения учетной записи ADFS в Azure AD B2C

Для федерации с учетной записью ADFS требуется секрет клиента учетной записи ADFS, чтобы доверять Azure AD B2C от имени приложения. Вам необходимо сохранить сертификат ADFS в клиенте Azure AD B2C. 

1. Войдите на [портал Azure](https://portal.azure.com/).
2. Убедитесь, что используется каталог с вашим клиентом Azure AD B2C, переключившись на него в правом верхнем углу окна портала Azure. Выберите **Переключение каталога**, а затем — каталог, содержащий созданный клиент. В этом руководстве используется каталог *contoso* с именем клиента *contoso0522Tenant.onmicrosoft.com*.

    ![Переключение каталогов](./media/active-directory-b2c-custom-setup-adfs2016-idp/switch-directories.png)

3. Выберите **Все службы** в левом верхнем углу окна портала Azure, найдите службу **Azure AD B2C** и выберите ее. Теперь вы должны использовать свой клиент.
4. На странице "Обзор" выберите **Identity Experience Framework**.
5. Выберите **Ключи политики**, чтобы просмотреть доступные в клиенте ключи, а затем щелкните **Добавить**.
6. В качестве параметра выберите **Отправить**.
7. В поле имени введите `ADFSSamlCert`. Префикс `B2C_1A_` может быть добавлен автоматически.
8. Найдите и выберите PFX-файл сертификата с закрытым ключом. Это должен быть сертификат с закрытым ключом, выданный и использованный для проверяющей стороны ADFS.
9. Выберите **Создать** и проверьте, создан ли ключ `B2C_1A_ADFSSamlCert`.

## <a name="add-a-claims-provider-in-your-extension-policy"></a>Добавление поставщика утверждений в политику расширения

Чтобы пользователи выполняли вход с помощью учетной записи ADFS, необходимо определить учетную запись в качестве поставщика утверждений. Для этого следует указать конечную точку, с которой взаимодействует Azure AD B2C. Конечная точка предоставляет набор утверждений, используемых Azure AD B2C, чтобы проверить, была ли выполнена проверка подлинности определенного пользователя.

Определите ADFS в качестве поставщика утверждений, добавив элемент **ClaimsProvider** в файл политики расширения.

1. В рабочей папке откройте файл политики *TrustFrameworkExtensions.xml*. Если вам нужен редактор XML, [попробуйте Visual Studio Code](https://code.visualstudio.com/download) — упрощенный кроссплатформенный редактор.
2. Добавьте приведенный ниже код XML под элементом **ClaimsProviders** и замените заполнитель **your-ADFS-domain** собственным именем домена. Кроме того, замените значение исходящего утверждения **identityProvider** на DNS (произвольное значение, указывающее домен) и сохраните файл. 

    ```xml
    <ClaimsProvider>
      <Domain>contoso.com</Domain>
      <DisplayName>Contoso ADFS</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Contoso-SAML2">
          <DisplayName>Contoso ADFS</DisplayName>
          <Description>Login with your Contoso account</Description>
          <Protocol Name="SAML2"/>
          <Metadata>
            <Item Key="RequestsSigned">false</Item>
            <Item Key="WantsEncryptedAssertions">false</Item>
            <Item Key="PartnerEntity">https://your-ADFS-domain/federationmetadata/2007-06/federationmetadata.xml</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="SamlAssertionSigning" StorageReferenceId="B2C_1A_ADFSSamlCert"/>
            <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_ADFSSamlCert"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="userPrincipalName" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name"/>
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name"/>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email"/>
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name"/>
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="contoso.com" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication"/>
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

## <a name="register-the-claims-provider-for-sign-up-and-sign-in"></a>Регистрация поставщика утверждений для регистрации и входа в систему

Чтобы сделать поставщика удостоверений учетной записи ADFS доступным на страницах регистрации и входа, необходимо добавить его к пути взаимодействия пользователя **SignUpOrSignIn**. 

Создайте копию имеющегося шаблона пути взаимодействия пользователя, а затем измените его таким образом, чтобы он содержал поставщик удостоверений ADFS.

>[!NOTE]
>Если ранее вы скопировали элемент **UserJourneys** из основного файла политики в файл расширения (*TrustFrameworkExtensions.xml*), можно пропустить этот раздел.

1. Откройте базовый файл политики. Например, *TrustFrameworkBase.xml*.
2. Скопируйте все содержимое элемента **UserJourneys**.
3. Откройте файл расширения (*TrustFrameworkExtensions.xml*) и вставьте все содержимое элемента **UserJourneys**, скопированного из этого файла.

### <a name="display-the-button"></a>Отображение кнопки

Элемент **ClaimsProviderSelections** определяет список параметров выбора поставщика утверждений и их порядок.  Кроме того, элемент **ClaimsProviderSelection** является аналогом кнопки поставщика удостоверений на странице регистрации и входа. Если вы добавили элемент **ClaimsProviderSelection** для учетной записи ADFS, при просмотре пользователем страницы на ней будет отображаться новая кнопка. Для этого:

1. В элементе **UserJourney** с идентификатором `SignUpOrSignIn` скопированного пути взаимодействия пользователя найдите элемент **OrchestrationStep** со значением `Order="1"`.
2. Добавьте следующий элемент **ClaimsProviderSelection** под элементом **ClaimsProviderSelections**.

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Связывание кнопки с действием

Теперь, когда у вас есть кнопка, вам необходимо связать ее с действием. В этом случае действие — это возможность взаимодействия Azure AD B2C с учетной записью ADFS для получения маркера. Свяжите кнопку с действием, связав технический профиль для поставщика утверждений учетной записи ADFS.

1. Найдите **OrchestrationStep** со значением `Order="2"` под элементом **UserJourney**.
2. Добавьте указанный ниже элемент **ClaimsExchange** под элементом **ClaimsExchanges**.

    ```xml
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-SAML2" />
    ```

> [!NOTE]
> * Убедитесь, что `Id` имеет то же значение, что и `TargetClaimsExchangeId` в предыдущем разделе.
> * Убедитесь, что для `TechnicalProfileReferenceId` задан ранее созданный технический профиль (Contoso-SAML2).


## <a name="optional-register-the-claims-provider-for-profile-edit"></a>Регистрация поставщика утверждений для изменения профиля [необязательно]

Вы также можете добавить поставщик удостоверений учетной записи ADFS к пути взаимодействия пользователя для изменения профиля.

### <a name="display-the-button"></a>Отображение кнопки

1. Откройте файл расширения политики. Например, *TrustFrameworkExtensions.xml*.
2. В элементе **UserJourney** с идентификатором `ProfileEdit` в скопированном пути взаимодействия пользователя найдите элемент **OrchestrationStep** со значением `Order="1"`.
3. Добавьте указанный ниже элемент **ClaimsProviderSelection** под элементом **ClaimsProviderSelections**.

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Связывание кнопки с действием

1. Найдите **OrchestrationStep** со значением `Order="2"` под элементом **UserJourney**.
2. Добавьте указанный ниже элемент **ClaimsExchange** под элементом **ClaimsExchanges**.

    ```xml
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-SAML2" />
    ```

## <a name="upload-the-policy-to-your-tenant"></a>Отправка политики в клиент

1. На портале Azure выберите **Все политики**.
2. Щелкните **Отправить политику**.
3. Установите флажок **Перезаписать политику, если она существует**.
4. Найдите и выберите файл политики *TrustFrameworkExtensions.xml*, а затем щелкните **Отправить**. Убедитесь, что проверка выполнена успешно.


## <a name="configure-an-adfs-relying-party-trust"></a>Настройка отношения доверия с проверяющей стороной ADFS

Чтобы использовать ADFS в качестве поставщика удостоверений в Azure AD B2C, необходимо сначала создать отношение доверия с проверяющей стороной ADFS с использованием метаданных SAML в Azure AD B2C. В указанном ниже примере показан URL-адрес метаданных SAML, связанных с техническим профилем Azure AD B2C.

```
https://login.microsoftonline.com/te/your-tenant/your-policy/samlp/metadata?idptp=your-technical-profile
```

Замените следующие значения:

- **your-tenant** — именем собственного клиента, например your-tenant.onmicrosoft.com.
- **your-policy** — именем собственной политики. Используйте политику с настроенным техническим профилем поставщика SAML или политику, которая от нее наследуется.
- **your-technical-profile** — именем технического профиля поставщика удостоверений SAML.
 
Откройте браузер и перейдите по URL-адресу. Убедитесь, что ввели правильный URL-адрес и что имеете доступ к XML-файлу метаданных.

Чтобы добавить новое отношение доверия с проверяющей стороной с помощью оснастки управления AD FS и вручную настроить параметры, выполните указанную ниже процедуру на сервере федерации. Для этого необходимо по крайней мере входить в группу **Администраторы** или аналогичную группу на локальном компьютере. Дополнительные сведения об использовании соответствующих учетных записей и членстве в группах см. в статье [Local and Domain Default Groups](http://go.microsoft.com/fwlink/?LinkId=83477) (Локальные и доменные группы по умолчанию).

1. В диспетчере сервера выберите **Средства**, а затем — **ADFS Management** (Управление ADFS).
2. Выберите **Добавить отношение доверия с проверяющей стороной**.
3. На странице **приветствия** выберите **Поддерживающие утверждения** и нажмите кнопку **Запустить**.
4. На странице **выбора источника данных** выберите параметр **импорта данных о проверяющей стороне, опубликованных в Интернете или локальной сети**, укажите URL-адрес метаданных Azure AD B2C и нажмите кнопку **Далее**.
5. На странице **Указание отображаемого имени** введите **отображаемое имя**. В поле **Примечания** введите описание для этого отношения доверия с проверяющей стороной и нажмите кнопку **Далее**.
6. На странице **Выбрать политику управления доступом** выберите политику и нажмите кнопку **Далее**.
7. На странице **Ready to Add Trust** (Готовность для добавления отношения доверия) проверьте параметры и нажмите кнопку **Далее**, чтобы сохранить сведения об отношении доверия с проверяющей стороной.
8. На странице **Готово** щелкните **Закрыть**. При этом автоматически откроется диалоговое окно **Edit Claim Rules** (Изменение правил утверждений).
9. Выберите **Добавить правило**.  
10. В разделе **Claim rule template** (Шаблон правила утверждения) выберите **Send LDAP attributes as claims** (Отправка атрибутов LDAP как утверждений).
11. Укажите **имя правила утверждения**. В разделе **Хранилище атрибутов** выберите **Active Directory**, добавьте следующие утверждения, а затем нажмите кнопку **Готово** и **ОК**.

    ![Настройка свойств правила](./media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-claims-3.png)

12.  В зависимости от типа сертификата может потребоваться задать хэш-алгоритм. В окне свойств отношения доверия с проверяющей стороной (демоверсия B2C) перейдите на вкладку **Дополнительно**, измените **алгоритм SHA** на `SHA-1` или `SHA-256` и нажмите кнопку **ОК**.  

### <a name="update-the-relying-party-metadata"></a>Обновление метаданных проверяющей стороны

Для изменения технического профиля SAML необходимо обновить ADFS с обновленной версией метаданных. Вам не нужно обновлять метаданные при создании приложения проверяющей стороны. Однако при изменении вы обновляете метаданные в ADFS.

1. В диспетчере сервера выберите **Средства**, а затем — **ADFS Management** (Управление ADFS).
2. Выберите созданное отношение доверия проверяющей стороны, щелкните **Update from Federation Metadata** (Обновить с метаданных федерации), а затем выберите **Обновить**. 

### <a name="test-the-policy-by-using-run-now"></a>Тестирование политики с помощью команды "Запустить сейчас"

1.  Откройте **Параметры Azure AD B2C** и перейдите к элементу **Инфраструктура процедур идентификации**.
2.  Откройте **B2C_1A_ProfileEdit**, отправленную пользовательскую политику проверяющей стороны. Выберите **Запустить сейчас**. Вы можете войти с использованием учетной записи ADFS.

## <a name="download-the-complete-policy-files"></a>Загрузка завершенных файлов политики

Необязательно. Вы можете создать свой сценарий, используя собственные файлы пользовательской политики, после того как вы выполните шаги статьи [Azure Active Directory B2C: начало работы с настраиваемыми политиками](active-directory-b2c-get-started-custom.md). Примеры файлов см. [на этой странице](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-setup-adfs2016-app).
