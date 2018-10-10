---
title: Azure Front Door Service — поддержка протокола HTTP2 | Документация Майкрософт
description: Эта статья поможет вам узнать о поддержке HTTP/2 в Azure Front Door Service.
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: 4282c9e9b660476992ba6f948bc5e408e9b064a5
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46968617"
---
# <a name="http2-support-in-azure-front-door-service"></a>Поддержка HTTP/2 в службе Azure Front Door Service
HTTP/2 является основной редакцией HTTP/1.1. Этот протокол обеспечивает более высокую производительность, сокращение времени ответа и более удобную среду работы пользователей наряду с привычными методами, кодами состояния и семантикой HTTP. Хотя HTTP/2 предназначен для работы с HTTP и HTTPS, многие клиентские веб-браузеры поддерживают только HTTP/2 через протокол TLS.

### <a name="http2-benefits"></a>Преимущества HTTP/2

Преимущества HTTP/2 перечислены ниже.

*   **Мультиплексирование и параллелизм**

    При использовании HTTP 1.1 для выполнения нескольких запросов к ресурсам требуется несколько TCP-подключений, и с каждым подключением связаны определенные затраты на обеспечение производительности. HTTP/2 позволяет запрашивать несколько ресурсов через одно TCP-подключение.

*   **Сжатие заголовка**

    Благодаря сжатию HTTP-заголовков для обслуживаемых ресурсов значительно уменьшается время в сети.

*   **Зависимости потоков**

    Зависимости потоков позволяют клиенту указывать серверу, какие ресурсы имеют приоритет.


## <a name="http2-browser-support"></a>Поддержка HTTP/2 в браузерах

Во всех текущих версиях основных браузеров реализована поддержка HTTP/2. Неподдерживаемые браузеры будет автоматически использовать HTTP/1.1.

|"Обзор"|Минимальная версия|
|-------------|------------|
|Microsoft Edge| 12|
|Google Chrome| 43|
|Mozilla Firefox| 38|
|Opera| 32|
|Safari| 9|

## <a name="enabling-http2-support-in-azure-front-door-service"></a>Включение поддержки HTTP/2 в Azure Front Door Service

Сейчас HTTP/2 поддерживается во всех конфигурациях Front Door. Никаких действий со стороны пользователей не требуется.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о HTTP/2 см. в следующих ресурсах.

- [Домашняя страница спецификации HTTP/2](https://http2.github.io/)
- [Официальная страница часто задаваемых вопросов об HTTP/2](https://http2.github.io/faq/)
- Дополнительные сведения о [создании Front Door](quickstart-create-front-door.md).
- Дополнительные сведения о том, [как работает Front Door](front-door-routing-architecture.md).
