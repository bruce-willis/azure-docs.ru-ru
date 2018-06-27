---
title: Безопасное развертывание Интернета вещей | Документация Майкрософт
description: В этой статье описано, как обеспечить безопасность развертывания IoT.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 01/17/2018
ms.author: dobett
ms.openlocfilehash: b90d916514e7b5ce566d4823b44d4b4ef575224d
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/18/2018
ms.locfileid: "35774002"
---
[!INCLUDE [iot-secure-your-deployment](../../includes/iot-secure-your-deployment.md)]

## <a name="iot-solution-accelerator-cipher-suites"></a>Комплекты шифров для акселератора решений Интернета вещей

Акселераторы решений Интернета вещей поддерживают следующие комплекты шифров в указанном порядке.

| Комплект шифров | Длина |
| --- | --- |
| TLS\_ECDHE\_RSA\_WITH\_AES\_256\_CBC\_SHA384 (0xc028) ECDH secp384r1 (эквивалент 7680-разрядного алгоритма RSA) FS |256 |
| TLS\_ECDHE\_RSA\_WITH\_AES\_128\_CBC\_SHA256 (0xc027) ECDH secp256r1 (эквивалент 3072-разрядного алгоритма RSA) FS |128 |
| TLS\_ECDHE\_RSA\_WITH\_AES\_256\_CBC\_SHA (0xc014) ECDH secp384r1 (эквивалент 7680-разрядного алгоритма RSA) FS |256 |
| TLS\_ECDHE\_RSA\_WITH\_AES\_128\_CBC\_SHA (0xc013) ECDH secp256r1 (эквивалент 3072-разрядного алгоритма RSA) FS |128 |
| TLS\_RSA\_WITH\_AES\_256\_GCM\_SHA384 (0x9d) |256 |
| TLS\_RSA\_WITH\_AES\_128\_GCM\_SHA256 (0x9c) |128 |
| TLS\_RSA\_WITH\_AES\_256\_CBC\_SHA256 (0x3d) |256 |
| TLS\_RSA\_WITH\_AES\_128\_CBC\_SHA256 (0x3c) |128 |
| TLS\_RSA\_WITH\_AES\_256\_CBC\_SHA (0x35) |256 |
| TLS\_RSA\_WITH\_AES\_128\_CBC\_SHA (0x2f) |128 |
| TLS\_RSA\_WITH\_3DES\_EDE\_CBC\_SHA (0xa) |112 |

## <a name="see-also"></a>См. также

Сведения о безопасности Центра Интернета вещей см. в статье [Управление доступом к Центру Интернета вещей][lnk-devguide-security] в руководстве для разработчиков.

[lnk-devguide-security]: /azure/iot-hub/iot-hub-devguide-security.md
