---
title: Получение текущего списка точек POP Verizon для Azure CDN | Документы Майкрософт
description: Сведения о получении текущего списка точек POP Verizon с помощью REST API.
services: cdn
documentationcenter: ''
author: dksimpson
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2018
ms.author: v-deasim
ms.custom: ''
ms.openlocfilehash: 9605b352755933b37819527cecbc4e1ccc30e8aa
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/14/2018
ms.locfileid: "45579077"
---
# <a name="retrieve-the-current-verizon-pop-list-for-azure-cdn"></a>Получение текущего списка точек POP Verizon для Azure CDN

С помощью REST API можно получить набор IP-адресов для серверов точек подключения (POP) Verizon. Эти серверы POP отправляют запросы к серверам-источникам, которые связаны с конечными точками сети доставки содержимого (CDN) Azure в профиле Verizon (**Azure CDN уровня "Стандартный" от Verizon**  или **Azure CDN уровня "Премиум" от Verizon**). Обратите внимание, что этот набор IP-адресов отличается от IP-адресов, которые будет видеть клиент при выполнении запросов к точкам POP. 

Синтаксис операции REST API для получения списка точек POP см. в статье [Edge Nodes - List](https://docs.microsoft.com/rest/api/cdn/edgenodes/list) (Пограничные узлы — список).

## <a name="typical-use-case"></a>Типичный случай использования

В целях безопасности этот список IP-адресов можно использовать для принудительного выполнения запросов к серверу-источнику только с допустимой точки POP Verizon. Например, если кто-то узнает имя узла или IP-адрес сервера-источника конечной точки CDN, он сможет отправлять запросы напрямую на сервер-источник, обходя, таким образом, функции масштабирования и безопасности, предоставляемые Azure CDN. Чтобы избежать такой ситуации, IP-адреса в возвращаемом списке следует задать только как разрешенные на сервере-источнике. Чтобы иметь в распоряжении актуальный список точек POP, получайте его по меньшей мере один раз в день. 

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о REST API см. в статье [Azure CDN REST API](https://docs.microsoft.com/rest/api/cdn/) (REST API сети доставки содержимого Azure).