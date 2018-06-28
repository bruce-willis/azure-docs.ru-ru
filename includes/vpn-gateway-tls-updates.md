---
title: включение файла
description: включение файла
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 06/05/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 9a17f34333503436d3da340670abdde154e45ef6
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "36336032"
---
Начиная с 1 июля 2018 года прекращается поддержка TLS 1.0 и TLS 1.1 в VPN-шлюзе Azure. VPN-шлюз будет поддерживать только TLS 1.2. Чтобы обеспечить поддержку TLS и работу клиентов подключений "точка — сеть" Windows 7 и Windows 8, использующих TLS, рекомендуется установить следующие обновления:

•   [обновление реализации Microsoft EAP, которая позволяет использовать TLS](https://support.microsoft.com/help/2977292/microsoft-security-advisory-update-for-microsoft-eap-implementation-th);

•   [обновление, позволяющее использовать TLS 1.1 и TLS 1.2 в качестве безопасных протоколов по умолчанию в WinHTTP](https://support.microsoft.com/help/3140245/update-to-enable-tls-1-1-and-tls-1-2-as-a-default-secure-protocols-in).

Кроме того, с 1 июля 2018 года объявляются нерекомендуемыми для TLS приведенные ниже устаревшие алгоритмы:

* RC4 (Rivest Cipher 4);
* DES (Data Encryption Algorithm);
* 3DES (Triple Data Encryption Algorithm);
* MD5 (Message Digest 5);
