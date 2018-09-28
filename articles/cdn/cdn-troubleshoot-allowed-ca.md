---
title: Разрешенные центры сертификации для включения настраиваемого HTTPS в Azure CDN | Документация Майкрософт
description: Если вы используете свой собственный сертификат для включения HTTPS в личном домене, вам нужен разрешенный центр сертификации (CA) для его создания.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2018
ms.author: magattus
ms.custom: mvc
ms.openlocfilehash: e9d9f5b053537b3d6881763bcb39d7894ba78869
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46969516"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-cdn"></a>Разрешенные центры сертификации для включения настраиваемого HTTPS в Azure CDN

Для личного домена сети доставки содержимого Azure (CDN) в конечной точке **Azure CDN уровня "Стандартный" от Майкрософт**, когда вы [включите функцию HTTPS, используя свой собственный сертификат](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#ssl-certificates), нужно создать SSL-сертификат с помощью разрешенного центра сертификации (ЦС). В противном случае при использовании недопустимого ЦС или самозаверяющего сертификата ваш запрос будет отклонен.

> [!NOTE]
> Возможность использования собственного сертификата для включения пользовательского HTTPS доступна только с профилем **Azure CDN уровня "Стандартный" от Майкрософт**. 
>

При создании собственного сертификата разрешены следующие ЦС:

- AddTrust External CA Root
- Корневой ЦС AlphaSSL
- ЦС 01 AME Infra
- ЦС 02 AME Infra
- Ameroot
- AP Root CA
- AP Root Certificate Authority 2013
- AP Root Certificate Authority 2014
- APCA-DM3P
- Autopilot Root CA
- Baltimore CyberTrust Root
- Class 3 Public Primary Certification Authority
- COMODO RSA Certification Authority
- COMODO RSA Domain Validation Secure Server CA
- D-TRUST Root Class 3 CA 2 2009
- DigiCert Cloud Services CA-1
- DigiCert Global Root CA
- DigiCert High Assurance CA-3
- DigiCert High Assurance EV Root CA
- ЦС расширенной проверки сервера DigiCert SHA2
- DigiCert SHA2 High Assurance Server CA
- DigiCert SHA2 Secure Server CA
- Корневой ЦС X3 DST
- Корневой ЦС 2 класса 3 D-trust 2009
- ЦС Encryption Everywhere DV TLS
- Корневой ЦС Entrust
- Корневой ЦС Entrust — G2
- ЦС Entrust.net (2048)
- Глобальный ЦС GeoTrust
- Основной ЦС GeoTrust
- Основной ЦС GeoTrust — G2
- ЦС Geotrust RSA 2018
- GlobalSign;
- GlobalSign Extended Validation CA - SHA256 - G2
- GlobalSign Organization Validation CA - G2
- GlobalSign Root CA
- Корневой ЦС Go Daddy — G2
- Защищенный ЦС Go Daddy — G2
- Microsoft Authenticode(tm) Root Authority
- Microsoft Exchange Services CA 2015
- Microsoft Internal Corporate Root
- Microsoft IT ITO SSL CA 1
- Microsoft IT SSL SHA1
- Microsoft IT SSL SHA2
- Microsoft IT TLS CA 1
- Microsoft IT TLS CA 2
- Microsoft IT TLS CA 4
- Microsoft IT TLS CA 5
- Microsoft Root Authority
- Microsoft Root Certificate Authority
- Microsoft Root Certificate Authority 2010
- Microsoft Root Certificate Authority 2011
- Microsoft Secure Server CA 2011
- Microsoft Services Partner Root
- Microsoft Time Stamping Service Root
- Microsoft Windows Hardware Compatibility
- MSIT CA Z2
- MSIT Enterprise CA 1
- MSIT Enterprise CA 3
- ЦС RSA RapidSSL 2018
- Root Agency
- Symantec Class 3 EV SSL CA - G3
- Symantec Class 3 Secure Server CA - G4
- Symantec Enterprise Mobile Root for Microsoft
- Основной корневой ЦС Thawte
- Основной корневой ЦС Thawte — G2
- Основной корневой ЦС Thawte — G3
- ЦС Thawte RSA 2018
- Thawte Timestamping CA
- ЦС TrustAsia TLS RSA
- UTN-USERFirst-Object
- VeriSign Class 3 Extended Validation SSL CA
- VeriSign Class 3 Extended Validation SSL SGC CA
- VeriSign Class 3 Public Primary Certification Authority - G5
- VeriSign International Server CA - Class 3
- VeriSign Time Stamping Service Root
- VeriSign Universal Root Certification Authority
- WMSvc-SHA2-DALEDGE1008

