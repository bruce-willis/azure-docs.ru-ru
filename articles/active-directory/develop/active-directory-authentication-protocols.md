---
title: Протоколы аутентификации Azure Active Directory | Документация Майкрософт
description: Обзор протоколов проверки подлинности, поддерживаемых в Azure Active Directory (AD)
documentationcenter: dev-center-name
author: CelesteDG
services: active-directory
manager: mtillman
editor: ''
ms.assetid: 7a838ae2-c24c-4304-b6c0-e77fb888e6c0
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/27/2017
ms.author: celested
ms.custom: aaddev
ms.reviewer: hirsin
ms.openlocfilehash: 04cdba261d67e20762fd4bb4835568f763124fef
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/06/2018
ms.locfileid: "39578479"
---
# <a name="azure-active-directory-authentication-protocols"></a>Протоколы аутентификации Azure Active Directory
Служба Azure Active Directory (Azure AD) поддерживает несколько широко используемых протоколов проверки подлинности и авторизации. В перечисленных ниже разделах мы рассказываем о поддерживаемых протоколах и их реализации в Azure AD. Эти разделы содержат обзор поддерживаемых типов утверждений, основные сведения об использовании метаданных федерации, подробную документацию по протоколам OAuth 2.0. и SAML 2.0, а также советы по устранению неполадок.

## <a name="authentication-protocols-articles-and-reference"></a>Статьи и другие справочные материалы по протоколам проверки подлинности
* [Важные сведения об откате ключа подписи в Azure AD](active-directory-signing-key-rollover.md) — дополнительные сведения об откате ключа подписи Azure AD, изменениях, которые можно вносить для автоматического обновления ключа, и порядок обновления самых распространенных сценариев приложений.
* [Справочник по токенам в Azure AD](v1-id-and-access-tokens.md) — сведения об утверждениях в токенах, издаваемых Azure AD.
* [Метаданные федерации](azure-ad-federation-metadata.md) — узнайте, как находить и интерпретировать документы метаданных, создаваемые Azure AD.
* [OAuth 2.0 в Azure AD](v1-protocols-oauth-code.md) — сведения о реализации протокола OAuth 2.0 в Azure AD.
* [OpenID Connect 1.0](v1-protocols-openid-connect-code.md) — сведения об использовании протокола авторизации OAuth 2.0 для проверки подлинности.
* [Служба обслуживания вызовов с помощью учетных данных клиента](v1-oauth2-client-creds-grant-flow.md) — сведения об использовании потока предоставления учетных данных клиента OAuth 2.0 для вызовов между службами.
* [Служба обслуживания вызовов с помощью потока On-Behalf-Of](v1-oauth2-on-behalf-of-flow.md) — сведения об использовании потока On-Behalf-Of OAuth 2.0 для вызовов между службами.
* [Справочник по протоколу SAML](active-directory-saml-protocol-reference.md) — сведения о профилях единого входа и единого выхода SAML в Azure AD.

## <a name="see-also"></a>См. также
[Руководство разработчика по Azure Active Directory](azure-ad-developers-guide.md)

[Примеры кода Active Directory](sample-v1-code.md)
