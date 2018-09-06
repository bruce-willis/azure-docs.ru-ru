---
title: Добавление собственных атрибутов в настраиваемые политики в Azure Active Directory B2C | Документация Майкрософт
description: Пошаговое руководство по использованию свойств расширения и настраиваемых атрибутов, а также по их добавлению в пользовательский интерфейс.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/04/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 5513e0ff434862ea7eee42cb94ff2a0f67f6d390
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/31/2018
ms.locfileid: "43338750"
---
# <a name="azure-active-directory-b2c-use-custom-attributes-in-a-custom-profile-edit-policy"></a>Azure Active Directory B2C. Использование настраиваемых атрибутов в пользовательской политике изменения профиля

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

В этой статье описано создание настраиваемого атрибута в каталоге Azure Active Directory (Azure AD) B2C. Этот новый атрибут будет использоваться в качестве пользовательского утверждения в редактировании профиля пути взаимодействия пользователя.

## <a name="prerequisites"></a>Предварительные требования

Выполните действия, описанные в статье [Azure Active Directory B2C: Начало работы с пользовательскими политиками](active-directory-b2c-get-started-custom.md).

## <a name="use-custom-attributes-to-collect-information-about-your-customers-in-azure-ad-b2c-by-using-custom-policies"></a>Использование настраиваемых атрибутов для сбора данных о клиентах в Azure AD B2C с помощью пользовательских политик
Каталог Azure AD B2C прилагается к встроенному набору атрибутов. Примерами являются **Имя**, **Фамилия**, **Город**, **Почтовый индекс** и **userPrincipalName**. Часто требуется создавать собственные атрибуты, как в этих примерах.
* Клиентскому приложению требуется сохранить атрибут **LoyaltyNumber**.
* Поставщик удостоверений имеет уникальный идентификатор пользователя, который необходимо сохранить, например **uniqueUserGUID**.
* В настраиваемом пути взаимодействия пользователя нужно сохранить состояние, например **migrationStatus**.

Azure AD B2C расширяет набор атрибутов, хранящихся в каждой учетной записи пользователя. Можно также читать и записывать эти атрибуты с помощью [API Graph Azure AD](active-directory-b2c-devquickstarts-graph-dotnet.md).

Свойства расширения расширяют схему объектов пользователей в каталоге. В контексте этой статьи *свойство расширения*, *настраиваемый атрибут* и *пользовательское утверждение* подразумевают одно и то же. Имя изменяется в зависимости от контекста, например приложения, объекта, политики.

Свойства расширения можно зарегистрировать только в объекте приложения, даже если они могут содержать данные для пользователя. Свойство присоединяется к приложению. Объекту приложения нужно иметь доступ на запись для регистрации свойства расширения. В один объект можно записать не более сотни значений расширений для всех типов и приложений. Свойства расширения добавляются к типу целевого каталога и сразу становятся доступными в клиенте каталога Azure AD B2C.
При удалении приложения эти свойства расширения и все данные, содержащиеся в них, для всех пользователей также удаляются. Если свойство расширения удаляется приложением, оно удаляется в объектах целевого каталога вместе со значениями.

Свойства расширения существуют только в контексте зарегистрированного приложения в клиенте. Идентификатор объекта приложения должен содержаться в параметре **TechnicalProfile**, который он использует.

>[!NOTE]
>Каталог Azure AD B2C обычно включает веб-приложение с именем `b2c-extensions-app`. Это приложение главным образом используется встроенными политиками B2C для пользовательских утверждений, созданных с помощью портала Azure. Регистрировать расширения для пользовательских политик B2C с помощью этого приложения рекомендуется для опытных пользователей.  
Инструкции содержатся в разделе **Дальнейшие действия** этой статьи.


## <a name="create-a-new-application-to-store-the-extension-properties"></a>Создайте новое приложение для хранения свойств расширения

1. Откройте браузер и перейдите на [портал Azure](https://portal.azure.com). Войдите в систему с учетными данными администратора каталога B2C, который необходимо настроить.
2. В области навигации слева выберите **Active Directory**. Найти эту службу можно, выбрав пункт **Больше служб**.
3. Щелкните **Регистрация приложений**. Выберите **Регистрация нового приложения**.
4. Укажите следующие записи.
    * Имя для веб-приложения: **WebApp-GraphAPI-DirectoryExtensions**.
    * Тип приложения: **веб-приложение или API**.
    * URL-адрес входа: **https://{tenantName}.onmicrosoft.com/WebApp-GraphAPI-DirectoryExtensions**.
5. Нажмите кнопку **Создать**.
6. Выберите созданное веб-приложение.
7. Выберите **Параметры** > **Необходимые разрешения**.
8. Выберите API **Microsoft Azure Active Directory**.
9. Установите флажки в разделе "Разрешения приложения": **Чтение и запись данных каталога**. Затем нажмите кнопку **Save** (Сохранить).
10. Выберите команду **Предоставить разрешения** и нажмите кнопку **Да**.
11. Скопируйте следующие идентификаторы в буфер обмена и сохраните их.
    * **Идентификатор приложения**. Пример: `103ee0e6-f92d-4183-b576-8c3739027780`.
    * **Идентификатор объекта**. Пример: `80d8296a-da0a-49ee-b6ab-fd232aa45201`.



## <a name="modify-your-custom-policy-to-add-the-applicationobjectid"></a>Изменение пользовательской политики для добавления **ApplicationObjectId**

Выполнив действия, описанные в статье [Azure Active Directory B2C: Начало работы с пользовательскими политиками](active-directory-b2c-get-started-custom.md), вы скачаете и измените [примеры файлов](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) с именем **TrustFrameworkBase.xml**, **TrustFrameworkExtensions.xml**, **SignUpOrSignin.xml**, **ProfileEdit.xml** и **PasswordReset.xml**. На этом этапе необходимо внести дополнительные изменения в эти файлы.

* Откройте файл **TrustFrameworkBase.xml** и добавьте раздел `Metadata`, как показано в следующем примере: Вставьте идентификатор объекта, записанный ранее для значения `ApplicationObjectId`, и идентификатор приложения, записанный для значения `ClientId`. 

    ```xml
    <ClaimsProviders>
        <ClaimsProvider>
          <DisplayName>Azure Active Directory</DisplayName>
            <TechnicalProfile Id="AAD-Common">
          <DisplayName>Azure Active Directory</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
              
          <!-- Provide objectId and appId before using extension properties. -->
          <Metadata>
            <Item Key="ApplicationObjectId">insert objectId here</Item>
            <Item Key="ClientId">insert appId here</Item>
          </Metadata>
          <!-- End of changes -->
              
          <CryptographicKeys>
            <Key Id="issuer_secret" StorageReferenceId="TokenSigningKeyContainer" />
          </CryptographicKeys>
          <IncludeInSso>false</IncludeInSso>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
        </TechnicalProfile>
        </ClaimsProvider>
    </ClaimsProviders>
    ```

> [!NOTE]
> При первой записи **TechnicalProfile** в недавно созданное свойство расширения может произойти одноразовая ошибка. Свойство расширения создается при первом использовании.  

## <a name="use-the-new-extension-property-or-custom-attribute-in-a-user-journey"></a>Используйте новое свойство расширения или настраиваемого атрибута в пути взаимодействия пользователя

1. Откройте файл **ProfileEdit.xml**.
2. Добавьте пользовательское утверждение `loyaltyId`. Добавленное в элемент `<RelyingParty>` пользовательское утверждение будет включено в токен для приложения.
    
    ```xml
    <RelyingParty>
      <DefaultUserJourney ReferenceId="ProfileEdit" />
      <TechnicalProfile Id="PolicyProfile">
        <DisplayName>PolicyProfile</DisplayName>
        <Protocol Name="OpenIdConnect" />
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
          <OutputClaim ClaimTypeReferenceId="city" />

          <!-- Provide the custom claim identifier -->
          <OutputClaim ClaimTypeReferenceId="extension_loyaltyId" />
          <!-- End of changes -->
        </OutputClaims>
        <SubjectNamingInfo ClaimType="sub" />
      </TechnicalProfile>
    </RelyingParty>
    ```

3. Откройте файл **TrustFrameworkExtensions.xml** и добавьте элемент `<ClaimsSchema>` и его дочерний элемент в элемент `BuildingBlocks`:

    ```xml
    <BuildingBlocks>
      <ClaimsSchema> 
        <ClaimType Id="extension_loyaltyId"> 
          <DisplayName>Loyalty Identification Tag</DisplayName> 
          <DataType>string</DataType> 
          <UserHelpText>Your loyalty number from your membership card</UserHelpText> 
          <UserInputType>TextBox</UserInputType> 
        </ClaimType> 
      </ClaimsSchema>
    </BuildingBlocks>
    ```

4. Добавьте это же определение `ClaimType` в файл **TrustFrameworkBase.xml**. Нет необходимости добавлять определение `ClaimType` как в базу, так и в файлы расширений. Однако следующие шаги добавляют `extension_loyaltyId` к **TechnicalProfiles** в базовый файл. Таким образом, проверяющий политики отклоняет передачу базового файла без него. Отследите выполнение пути взаимодействия пользователя **ProfileEdit** в файле **TrustFrameworkBase.xml**. Найдите путь взаимодействия пользователя с тем же именем в редакторе. Обратите внимание, что на шаге 5 оркестрации вызывается **TechnicalProfileReferenceID="SelfAsserted-ProfileUpdate**. Найдите и проверьте этот **TechnicalProfile**, чтобы ознакомиться с рабочим процессом.

5. Откройте файл **TrustFrameworkBase.xml** и добавьте `loyaltyId` в качестве входного и выходного утверждения в элемент **TechnicalProfile SelfAsserted-ProfileUpdate**.

    ```xml
    <TechnicalProfile Id="SelfAsserted-ProfileUpdate">
      <DisplayName>User ID signup</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ContentDefinitionReferenceId">api.selfasserted.profileupdate</Item>
      </Metadata>
      <IncludeInSso>false</IncludeInSso>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="alternativeSecurityId" />
        <InputClaim ClaimTypeReferenceId="userPrincipalName" />
        <InputClaim ClaimTypeReferenceId="givenName" />
            <InputClaim ClaimTypeReferenceId="surname" />

        <!-- Add the loyalty identifier -->
        <InputClaim ClaimTypeReferenceId="extension_loyaltyId"/>
        <!-- End of changes -->
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" DefaultValue="true" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        
        <!-- Add the loyalty identifier -->
        <OutputClaim ClaimTypeReferenceId="extension_loyaltyId"/>
        <!-- End of changes -->

      </OutputClaims>
      <ValidationTechnicalProfiles>
        <ValidationTechnicalProfile ReferenceId="AAD-UserWriteProfileUsingObjectId" />
      </ValidationTechnicalProfiles>
    </TechnicalProfile>
    ```

6. В файле **TrustFrameworkBase.xml** добавьте утверждение `loyaltyId` в элемент **TechnicalProfile AAD-UserWriteProfileUsingObjectId**. Это добавление сохраняет значение утверждения в свойстве расширения для текущего пользователя в каталоге.

    ```xml
    <TechnicalProfile Id="AAD-UserWriteProfileUsingObjectId">
      <Metadata>
        <Item Key="Operation">Write</Item>
        <Item Key="RaiseErrorIfClaimsPrincipalAlreadyExists">false</Item>
        <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
      </Metadata>
      <IncludeInSso>false</IncludeInSso>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
      </InputClaims>
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="objectId" />
        <PersistedClaim ClaimTypeReferenceId="givenName" />
        <PersistedClaim ClaimTypeReferenceId="surname" />

        <!-- Add the loyalty identifier -->
        <PersistedClaim ClaimTypeReferenceId="extension_loyaltyId" />
        <!-- End of changes -->

      </PersistedClaims>
      <IncludeTechnicalProfile ReferenceId="AAD-Common" />
    </TechnicalProfile>
    ```

7. В файле **TrustFrameworkBase.xml** добавьте утверждение `loyaltyId` в элемент **TechnicalProfile AAD-UserReadUsingObjectId**, чтобы считывать значение атрибута расширения при каждом входе пользователя в систему. До сих пор **TechnicalProfiles** изменялся только при работе с локальными учетными записями. Если необходимо использовать новый атрибут для учетной записи социальной сети или федеративной учетной записи, нужно изменить другой набор **TechnicalProfiles**. См. раздел **Дальнейшие действия**.

    ```xml
    <TechnicalProfile Id="AAD-UserReadUsingObjectId">
      <Metadata>
        <Item Key="Operation">Read</Item>
        <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
      </Metadata>
      <IncludeInSso>false</IncludeInSso>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="otherMails" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />

        <!-- Add the loyalty identifier -->
        <OutputClaim ClaimTypeReferenceId="extension_loyaltyId" />
        <!-- End of changes -->

      </OutputClaims>
      <IncludeTechnicalProfile ReferenceId="AAD-Common" />
    </TechnicalProfile>
    ```

## <a name="test-the-custom-policy"></a>Проверка пользовательской политики

1. Откройте колонку Azure AD B2C и последовательно выберите **Identity Experience Framework** > **Настраиваемые политики**.
1. Выберите отправленную пользовательскую политику. Выберите **Запустить сейчас**.
1. Регистрация с помощью адреса электронной почты.

Идентификатор токена, отправляемый в приложение, содержит новое свойство расширения в виде пользовательского утверждения, перед которым будет указано **extension_loyaltyId**. Как в этом примере:

```json
{
  "exp": 1493585187,
  "nbf": 1493581587,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "sub": "a58e7c6c-7535-4074-93da-b0023fbaf3ac",
  "aud": "4e87c1dd-e5f5-4ac8-8368-bc6a98751b8b",
  "acr": "b2c_1a_trustframeworkprofileedit",
  "nonce": "defaultNonce",
  "iat": 1493581587,
  "auth_time": 1493581587,
  "extension_loyaltyId": "abc",
  "city": "Redmond"
}
```

## <a name="next-steps"></a>Дополнительная информация

1. Добавьте новое утверждение в рабочий процесс для входа с использованием учетных записей социальных сетей, изменив указанные ниже **TechnicalProfiles**. Социальные и федеративные учетные записи используют эти два **TechnicalProfiles** для входа в систему. Они записывают и читают данные пользователя, используя **alternativeSecurityId** как указатель объекта пользователя.

  ```xml
    <TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">

    <TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
  ```

2. Используйте одинаковые атрибуты расширения во встроенных и пользовательских политиках. При добавлении атрибутов расширения или пользовательских атрибутов на портал они регистрируются с помощью приложения **b2c-extensions-app**, содержащегося в каждом клиенте B2C. Чтобы использовать эти атрибуты расширения в пользовательской политике, выполните следующие действия.

  a. В клиенте B2C на сайте portal.azure.com перейдите к **Azure Active Directory** и выберите **Регистрация приложений**.  
  b. Найдите свое приложение **b2c-extensions-app** и выберите его.  
  c. В разделе **Основные компоненты** введите **Идентификатор приложения** и **Идентификатор объекта**.  
  d. Добавьте их в метаданные технического профиля **AAD-Common**.  

  ```xml
      <ClaimsProviders>
        <ClaimsProvider>
              <DisplayName>Azure Active Directory</DisplayName>
            <TechnicalProfile Id="AAD-Common">
                <DisplayName>Azure Active Directory</DisplayName>
                <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
                <!-- Provide objectId and appId before using extension properties. -->
                <Metadata>
                  <Item Key="ApplicationObjectId">insert objectId here</Item> <!-- This is the "Object ID" from the "b2c-extensions-app"-->
                  <Item Key="ClientId">insert appId here</Item> <!--This is the "Application ID" from the "b2c-extensions-app"-->
                </Metadata>
  ```

3. Следите за взаимодействием с порталом. Создайте эти атрибуты с помощью пользовательского интерфейса портала, прежде чем использовать их в пользовательских политиках. При создании атрибута **ActivationStatus** на портале необходимо обратиться к нему следующим образом.

  ```
  extension_ActivationStatus in the custom policy.
  extension_<app-guid>_ActivationStatus via Graph API.
  ```


## <a name="reference"></a>Справочные материалы

Дополнительные сведения о свойствах расширения см. в статье [Directory schema extensions | Graph API concepts](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions) (Расширения схемы каталогов | Концепции API Graph).

> [!NOTE]
> * **TechnicalProfile** — это тип элемента или функция, определяющая имя, метаданные и протокол конечной точки. **TechnicalProfile** детализирует обмен утверждениями, которые выполняет платформа Identity Experience Framework. При вызове этой функции на шаге оркестрации или из другого **TechnicalProfile** вызывающий объект указывает **InputClaims** и **OutputClaims** в качестве параметров.  
> * Атрибуты расширений в API Graph именуются по соглашению `extension_ApplicationObjectID_attributename`.  
> * Пользовательские политики относятся к атрибутам расширения как **extension_attributename**. Эта ссылка исключает **ApplicationObjectId** на языке XML.
