---
title: Включение пробной версии с помощью Azure AD | Azure
description: Включение типа приложения пробной версии с помощью Azure Active Directory (Azure AD) в Azure Marketplace и AppSource для издателей приложений и служб.
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
ms.openlocfilehash: 4140ba98c0c65c22674c61dc7266818af904e777
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/06/2018
ms.locfileid: "34826273"
---
# <a name="enable-trial-using-azure-ad"></a>Включение пробной версии с помощью Azure AD  
Azure Active Directory (Azure AD) — это облачная служба удостоверений, которая позволяет выполнять аутентификацию с рабочей или учебной учетной записью Майкрософт с использованием стандартных платформ: OAuth и OpenID Connect.  
*   Дополнительные сведения об Azure AD см. по адресу [azure.microsoft.com/services/active-directory](https://azure.microsoft.com/services/active-directory).  

Вы и ваши клиенты проходят аутентификацию в Marketplace с использованием Azure AD. Когда клиент выбирает ваше пробное приложение в Marketplace, он перенаправляется в пробную среду.  В пробной среде вы можете выполнять настройку для клиента напрямую, не требуя дополнительных шагов входа. Токен, получаемый вашим приложением от Azure AD во время аутентификации, содержит ценные сведения о пользователе, которые помогут вам создать его учетную запись в своем приложении или предложении. Вы можете автоматизировать настройки и увеличить вероятность преобразования.  
*   Дополнительные сведения о токене, отправленном из Azure AD во время аутентификации, см. в разделе "Примеры токенов" по адресу [​​docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims#sample-tokens](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims#sample-tokens).

Преимущества использования Azure AD для включения аутентификации одним щелчком в приложении или пробной версии:  
*   Упрощается взаимодействие клиентов с пробной версией в Marketplace.  
*   Сохраняется ощущение использования продукта, даже когда пользователь перенаправляется с Marketplace на ваш домен или в пробную среду.  
*   Сокращается вероятность отказа от перенаправления, так как отсутствует дополнительный шаг выполнения входа.  
*   Уменьшаются ограничения развертывания для большого числа пользователей Azure AD.  

## <a name="verify-your-azure-ad-integration-on-the-marketplace-multitenant-apps"></a>Проверка интеграции Azure AD с Marketplace для мультитенантных приложений  
Ваше решение, использующее Azure AD, должно поддерживать следующие возможности.  
*   Зарегистрируйте свое приложение в магазинах в Marketplace.  
*   Включите функцию поддержки мультитенантности в Azure AD, чтобы получить пробную версию одним щелчком.  
    *   Дополнительные сведения о регистрации приложений см. в статье "Интеграция приложений с Azure Active Directory", расположенной по адресу [​​docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).  

Если вы не использовали федеративный единый вход (SSO) в Azure AD, выполните следующие действия.  
1.  Зарегистрируйте свое приложение в Marketplace. 
2.  Настройте единый вход в Azure AD с помощью OpenID Connect или OAuth 2.0.  
    *   Дополнительные сведения об OAuth 2.0 см. по адресу [docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code).  
    *   Дополнительные сведения об Open ID см. по адресу [docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code).  
3.  Включите функцию поддержки мультитенантности в Azure AD, чтобы предоставить пробную версию одним щелчком.  
    *   Дополнительные сведения см. на странице сертификации AppSource по адресу [docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified). 

## <a name="verify-your-azure-ad-integration-on-the-marketplace-single-tenant-apps"></a>Проверка интеграции с Azure AD в Marketplace для однотенантных приложений  
Ваше однотенантное приложение должно поддерживать одну из следующих возможностей.  
*   Добавьте пользователей в свой каталог в качестве гостевых пользователей, используя Azure AD B2B.  
    *   Дополнительные сведения об Azure AD B2B см. по адресу [docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).
*   Вручную настройте пробную версию для клиентов с использованием функции "Связаться со мной".  
*   Разработайте тестовый выпуск для одного клиента.  
*   Создайте демонстрационное мультитенантное приложение с единым входом.  

## <a name="next-steps"></a>Дополнительная информация
*   Посетите страницу [руководства по публикации в Azure Marketplace и AppSource](./marketplace-publishers-guide.md).  
 
---  

