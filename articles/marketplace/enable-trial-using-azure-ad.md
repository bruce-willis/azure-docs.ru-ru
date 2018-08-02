---
title: Включение бесплатной пробной версии в Azure Marketplace с помощью Azure Active Directory | Azure
description: Включение типа приложения пробной версии с помощью Azure Active Directory в Azure Marketplace и AppSource для издателей приложений и служб.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: jm-aditi-ms
manager: pabutler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 06/04/2018
ms.author: ellacroi
ms.openlocfilehash: c5b7b4967c1acef733d366e651d50706db42aace
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/19/2018
ms.locfileid: "39160473"
---
# <a name="enable-a-trial-listing-by-using-azure-active-directory"></a>Включение типа приложения пробной версии с помощью Azure Active Directory

Azure Active Directory (Azure AD) — это облачная служба удостоверений, которая позволяет выполнять аутентификацию с рабочей или учебной учетной записью Майкрософт с использованием стандартных платформ. Azure AD поддерживает аутентификацию с помощью OAuth и OpenID Connect. [Azure Marketplace](https://azuremarketplace.microsoft.com) использует Azure AD, чтобы аутентифицировать вас и ваших клиентов.

Дополнительные сведения об Azure AD см. в [этой статье](https://azure.microsoft.com/services/active-directory).

Когда клиент выбирает бесплатную пробную версию в Marketplace, он перенаправляется в пробную среду. В пробной среде вы можете выполнять настройку для клиента напрямую, не требуя дополнительных шагов входа. Приложение или предложение получает маркер из Azure AD во время выполнения аутентификации. Маркер содержит ценные сведения о пользователе, используемые для создания учетной записи пользователя в приложении или предложении. Вы можете автоматизировать настройку клиента и увеличить вероятность преобразования.

Дополнительные сведения о маркере, полученном от Azure AD во время выполнения аутентификации, см. в разделе [Примеры токенов](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims#sample-tokens).

Преимущества использования Azure AD для включения аутентификации одним щелчком в приложении или пробной версии: Azure AD предоставляет следующие преимущества: 
*   Упрощается взаимодействие клиентов с пробной версией в Marketplace.
*   Сохраняется ощущение использования продукта, даже когда пользователь перенаправляется с Marketplace на ваш домен или в пробную среду.
*   Сокращается вероятность отказа от перенаправления, так как отсутствует дополнительный шаг выполнения входа.
*   Уменьшаются ограничения развертывания для большого числа пользователей Azure AD.

## <a name="verify-your-azure-ad-integration-in-the-marketplace-multitenant-apps"></a>Проверка интеграции Azure AD с Marketplace для мультитенантных приложений
Azure AD позволяет использовать следующие возможности для вашего решения:
*   Регистрация приложения в магазинах в Marketplace.
*   Включите функцию поддержки мультитенантности в Azure AD, чтобы получить пробную версию одним щелчком.

Дополнительные сведения о регистрации приложений см. в статье [Интеграция приложений с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).

Если вы не использовали федеративный единый вход (SSO) в Azure AD, выполните следующие действия:
1.  Зарегистрируйте свое приложение в Marketplace. 
2.  Настройте единый вход в Azure AD с помощью OpenID Connect или OAuth 2.0.
    *   Дополнительные сведения об OAuth 2.0 см. в статье [Авторизация доступа к веб-приложениям Azure Active Directory с помощью потока предоставления кода OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code).
    *   Дополнительные сведения об Open ID Connect см. в статье [Предоставление доступа к веб-приложениям с помощью OpenID Connect и Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code).
3.  Включите функцию поддержки мультитенантности в Azure AD, чтобы предоставить пробную версию одним щелчком.
    
    Дополнительные сведения о сертификации AppSource см. в статье [Как получить сертификат AppSource для Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified). 

## <a name="verify-your-azure-ad-integration-in-the-marketplace-single-tenant-apps"></a>Проверка интеграции с Azure AD в Marketplace для однотенантных приложений
Azure AD позволяет использовать один из следующих вариантов для однотенантного решения: 
*   Добавьте пользователей в свой каталог в качестве гостевых пользователей, используя Azure Active Directory B2B (Azure AD B2B).
    
    Дополнительные сведения об Azure AD B2B см. в статье [Что такое служба совместной работы Azure AD B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).
*   Вручную настройте бесплатную пробную версию для клиентов, используя параметр публикации "Свяжитесь со мной".
*   Разработайте тестовый выпуск для одного клиента.
*   Создайте демонстрационное мультитенантное приложение, использующее единый вход.

## <a name="next-steps"></a>Дополнительная информация
*   Ознакомьтесь со статьей [Руководство по публикации в Azure Marketplace и AppSource](./marketplace-publishers-guide.md).
