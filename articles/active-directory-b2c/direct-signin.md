---
title: Настройка прямого входа в систему с помощью Azure Active Directory B2C | Документация Майкрософт
description: Узнайте, как настроить предварительное заполнение имени для входа или перенаправление непосредственно в поставщик удостоверений в социальных сетях.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 06/18/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 62ded87067bf597a2f40ec8e8405142297d4fb78
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/03/2018
ms.locfileid: "37440542"
---
# <a name="set-up-direct-sign-in-using-azure-active-directory-b2c"></a>Настройка прямого входа в систему с помощью Azure Active Directory B2C

При настройке входа для приложения с помощью Azure Active Directory (AD) B2C вы можете настроить предварительное заполнение имени для входа или прямой вход в поставщик удостоверений в определенных социальных сетях, например Facebook или LinkedIn, или учетной записи Майкрософт. 

## <a name="prepopulate-the-sign-in-name"></a>Предварительное заполнение имени входа в систему

Во время входа пользователя в систему приложение проверяющей стороны может предложить определенное имя пользователя или домена. При этом приложение может указать параметр запроса `login_hint` с именем пользователя в запросе на авторизацию. Azure AD B2C автоматически заполняет имя для входа в систему — пользователю нужно ввести только пароль.

![Использование указания имени для входа](./media/direct-signin/login-hint.png) 

Пользователь может изменить значение, отображаемое в текстовом поле, для входа в систему.

Если вы используете пользовательскую политику, переопределите технический профиль `SelfAsserted-LocalAccountSignin-Email`. В разделе `<InputClaims>` задайте для DefaultValue утверждения signInName значение `{OIDC:LoginHint}`. Переменная `{OIDC:LoginHint}` содержит значение параметра `login_hint`. Azure AD B2C считывает значение утверждения signInName и заполняет текстовое поле signInName.

```xml
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
      <InputClaims>
        <!-- Add the login hint value to the sign-in names claim type -->
        <InputClaim ClaimTypeReferenceId="signInName" DefaultValue="{OIDC:LoginHint}" />
      </InputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="redirect-sign-in-to-a-social-provider"></a>Перенаправление операции входа в поставщик социальных сетей

Если вы настроили для приложения возможность входа с использованием учетных записей социальных сетей, таких как Facebook, LinkedIn или Google, вы можете указать параметр `domain_hint`. Этот параметр запроса сообщает Azure AD B2C о поставщике удостоверений социальных сетей, который должен использоваться для входа в систему. Например, если приложение указывает `domain_hint=facebook.com`, вход в систему выполняется непосредственно на странице входа в Facebook.

![Использование указания домена](./media/direct-signin/domain-hint.png) 

Если вы используете пользовательскую политику, вы можете настроить доменное имя с помощью XML-элемента `<Domain>domain name</Domain>` любого `<ClaimsProvider>`. 

```xml
<ClaimsProvider>
    <!-- Add the domain hint value to the claims provider -->
    <Domain>facebook.com</Domain>
    <DisplayName>Facebook</DisplayName>
    <TechnicalProfiles>
    ...
```


