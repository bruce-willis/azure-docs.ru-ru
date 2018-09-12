---
title: Загрузка приложения прокси приложения занимает слишком много времени | Документы Майкрософт
description: Устранение проблем с производительностью загрузки страницы в прокси приложения Azure AD.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: 0c18647fbfc5e328276eef248f4b6aa1dc7f48a2
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/11/2018
ms.locfileid: "44355553"
---
# <a name="an-application-proxy-application-takes-too-long-to-load"></a>Загрузка приложения прокси приложения занимает слишком много времени

Эта статья поможет вам понять, почему загрузка приложения Azure AD Application Proxy может занимать долгое время. Здесь также объясняется, что можно сделать, чтобы устранить эту проблему.

## <a name="overview"></a>Обзор
Хотя ваши приложения работают, могут возникать большие задержки. Могут возникнуть модификации топологии сети, которые можно внести, чтобы повысить скорость. Оценку различных топологий см. в [документе с рекомендациями по сети](application-proxy-network-topology.md).

Помимо топологии сети в настоящее время не существует дополнительных рекомендаций по настройке производительности. По мере расширения службы прокси приложения она может оказаться ближе к физическому центру обработки данных. Более близкое расположение может помочь с задержкой. Список центров обработки данных Azure см. на [странице тестирования задержки](http://www.azurespeed.com/Azure/Latency). 

Центры обработки данных с прокси-службой приложения можно найти с помощью [средства проверки портов соединителя](https://aadap-portcheck.connectorporttest.msappproxy.net/). 

## <a name="feedback-on-application-proxy-data-center-locations"></a>Отзывы о расположениях центров обработки данных для прокси приложения 
Могут существовать центры обработки данных Azure, которые пока не используют прокси приложения, но способны значительно сократить вашу задержку. Отправьте расположение центра обработки данных в aadapfeedback@microsoft.com. Корпорация Майкрософт использует отзывы в планах по расширению.

Корпорация Майкрософт работает над дополнительными возможностями снижения задержки. Документация будет обновлена, как только эти улучшения станут доступны.

## <a name="next-steps"></a>Дополнительная информация
[Работа с имеющимися локальными прокси-серверами](application-proxy-configure-connectors-with-proxy-servers.md)
