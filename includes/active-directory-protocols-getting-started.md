---
title: Обзор протокола .NET Azure AD | Документация Майкрософт
description: Использование HTTP-сообщений для авторизации доступа к веб-приложениям и веб-API в клиенте с использованием Azure AD.
services: active-directory
documentationcenter: .net
author: priyamohanram
manager: mbaldwin
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 04/18/2018
ms.author: priyamo
ms.openlocfilehash: cb31bb91c80e4d5dd032b009b40d8e3fc435e0c8
ms.sourcegitcommit: 99a6a439886568c7ff65b9f73245d96a80a26d68
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/31/2018
ms.locfileid: "39359465"
---
## <a name="register-your-application-with-your-ad-tenant"></a>Регистрация приложения в клиенте AD
Для начала необходимо зарегистрировать приложение в клиенте Azure Active Directory (Azure AD). После этого вашему приложению будет присвоен идентификатор, и оно сможет получать маркеры.

* Войдите на [портале Azure](https://portal.azure.com).
* Выберите клиент Azure AD. Для этого щелкните учетную запись в правом верхнем углу страницы, а затем щелкните панель навигации **Переключение каталога** и выберите соответствующий клиент. 
  * Пропустите этот шаг, если в вашей учетной записи только один клиент Azure AD или если вы уже выбрали соответствующий клиент Azure AD.
* В области навигации слева щелкните **Azure Active Directory**.
* Выберите **Регистрация приложений** и щелкните **Регистрация нового приложения**.
* Следуйте инструкциям на экране, а затем создайте новое приложение. Для данного руководства не имеет значения, будет это веб-приложение или собственное приложение, но если вам нужны конкретные примеры веб-приложений или собственных приложений, ознакомьтесь с нашими [краткими руководствами](../articles/active-directory/develop/active-directory-developers-guide.md).
  * Для веб-приложений укажите **URL-адрес входа**, который является базовым URL-адресом вашего приложения и через который пользователи могут входить в приложение, например `http://localhost:12345`.
<!--TODO: add once App ID URI is configurable: The **App ID URI** is a unique identifier for your application. The convention is to use `https://<tenant-domain>/<app-name>`, e.g. `https://contoso.onmicrosoft.com/my-first-aad-app`-->
  * Для собственных приложений укажите **URI перенаправления**, который Azure AD будет использовать для возврата ответов маркеров. Укажите значение, специфичное для вашего приложения, например `http://MyFirstAADApp`
* По завершении регистрации система Azure AD присвоит приложению уникальный идентификатор клиента — **идентификатор приложения**. Это значение вам понадобится в следующих разделах, поэтому не забудьте скопировать его на странице приложения.
* Чтобы найти приложение на портале Azure, щелкните **Регистрация приложений** и выберите **Показать все приложения**.
