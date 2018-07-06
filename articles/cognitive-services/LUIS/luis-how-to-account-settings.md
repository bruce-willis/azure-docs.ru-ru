---
title: Управление параметрами учетной записи в LUIS | Документация Майкрософт
description: Веб-сайт службы LUIS можно использовать для управления параметрами учетной записи.
titleSuffix: Azure
services: cognitive-services
author: v-geberr
manager: Kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/04/2018
ms.author: v-geberr
ms.openlocfilehash: 1f22112a38bf32af03ffaf0493db16839b3fe794
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/25/2018
ms.locfileid: "36749969"
---
# <a name="manage-your-luis-account"></a>Управление учетной записью LUIS
Чтобы использовать учетную запись LUIS необходимо иметь в наличии два таких ключевых элемента, как учетная запись пользователя и ключ разработки. Сведения для входа управляются с сайта [account.microsoft.com](https://account.microsoft.com). Ключ разработки управляется из [LUIS] веб-сайта [LUIS] страницы **Параметры**. 

## <a name="authoring-key"></a>Ключ разработки

Этот единый ключ разработки для конкретного региона на странице **Параметры**, который позволяет разрабатывать все приложения [LUIS] на веб-сайте [LUIS], также как на веб-сайте по [разработке API](https://aka.ms/luis-authoring-api). Для удобства ключу разработки разрешается запрашивать конечные точки на протяжении месяца [ограниченное](luis-boundaries.md) количество раз. 

![Страница "Параметры LUIS"](./media/luis-how-to-account-settings/account-settings.png)

Ключ разработки используется для любых приложений, которыми вы владеете, так и для приложений, в которых вы отображены как сотрудник.

## <a name="authoring-key-regions"></a>Регионы ключа разработки
Ключ разработки соответствует [региону разработки](luis-reference-regions.md#publishing-regions). Для каждого региона существует свой уникальный ключ. 

## <a name="reset-authoring-key"></a>Сброс ключа разработки
Если ключ разработки скомпрометирован, его необходимо сбросить. На веб-сайте [LUIS] можно сбросить ключ для всех приложений. При создании приложения через веб-сайт разработки API необходимо изменить значение `Ocp-Apim-Subscription-Key` на новый ключ. 

## <a name="delete-account"></a>Удаление учетной записи
Дополнительные сведения о том, какие данные удаляются при удалении учетной записи см. статью [Data storage and removal](luis-concept-data-storage.md#accounts) (Хранение и удаление данных). 

## <a name="azure-active-directory-tenant-user"></a>Пользователь клиента Azure Active Directory
Служба LUIS использует стандартный поток согласия Azure Active Directory (Azure AD). 

Администратор клиента должен работать непосредственно с пользователем, которому нужно предоставить доступ на использование службы LUIS в Azure AD. 

Сначала пользователю необходимо зарегистрироваться в службе LUIS. При этом он увидит всплывающее диалоговое окно, требующее подтверждения администратора. Перед тем как продолжить, пользователю необходимо обратится к администратору клиента. 

Затем администратор клиента войдет в службу LUIS и увидит всплывающее окно. Чтобы предоставить разрешение пользователю, во всплывающем окне администратор должен дать разрешение. Пользователь сможет продолжить работу со службой LUIS после подтверждения администратора.

Если администратор клиента не войдет в службу LUIS, пользователь может получить согласие к LUIS на [веб-сайте доступа](https://account.activedirectory.windowsazure.com/r#/applications). 

![Разрешение Azure Active Directory на веб-сайте приложения](./media/luis-how-to-account-settings/tenant-permissions.png)

Если администратор клиента хочет, чтобы определенные пользователи использовали службу LUIS, обратитесь к этому [идентификатору блога](https://blogs.technet.microsoft.com/tfg/2017/10/15/english-tips-to-manage-azure-ad-users-consent-to-applications-using-azure-ad-graph-api/).

### <a name="user-accounts-with-multiple-emails-for-collaborators"></a>Учетные записи пользователей с несколькими адресами электронной почты для сотрудников
Если в приложение LUIS будут добавлены участники для совместной работы, необходимо указать их точные адреса электронной почты, используемые в LUIS. В то время как Azure Active Directory (Azure AD) позволяет одному пользователю иметь несколько учетных записей электронной почты, которые являются взаимозаменяемыми, служба LUIS требует, чтобы пользователь для входа в систему использовал адрес электронной почты, указанный в списке участников для совместной работы. 


## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения см. в разделе [Keys in LUIS](luis-concept-keys.md#authoring-key) (Ключи в службе LUIS). 

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions
