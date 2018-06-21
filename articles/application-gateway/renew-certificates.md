---
title: Обновление сертификата шлюза приложений Azure
description: Узнайте, как обновить сертификат, связанный с прослушивателем шлюза приложений.
services: application-gateway
author: vhorne
manager: jpconnock
ms.service: application-gateway
ms.topic: article
ms.date: 05/18/2018
ms.author: victorh
ms.openlocfilehash: b44a57fe8ebcc985d3ab66ea04936a1558d00863
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34598272"
---
# <a name="renew-application-gateway-certificates"></a>Обновление сертификатов шлюза приложений

Если вы настроили в шлюзе приложений SSL-шифрование, рано или поздно вам придется обновить сертификаты.

Сертификат, связанный с прослушивателем, можно обновить с помощью портала Azure, Azure PowerShell или Azure CLI:

## <a name="azure-portal"></a>Портал Azure

Чтобы обновить сертификат прослушивателя на портале, перейдите к прослушивателям шлюза приложений. Щелкните прослушиватель, сертификат которого необходимо обновить, и выберите **Продлить или изменить выбранный сертификат**.

![Обновление сертификата](media/renew-certificate/ssl-cert.png)

Передайте новый сертификат в формате PFX, присвойте ему имя, введите пароль и нажмите кнопку **Сохранить**.

## <a name="azure-powershell"></a>Azure PowerShell

Чтобы обновить сертификат с помощью Azure PowerShell, используйте следующий командлет:

```azurepowershell-interactive
$appgw = Get-AzureRmApplicationGateway `
  -ResourceGroupName <ResourceGroup> `
  -Name <AppGatewayName>

$password = ConvertTo-SecureString `
  -String "<password>" `
  -Force `
  -AsPlainText

set-azureRmApplicationGatewaySSLCertificate -Name <oldcertname> `
-ApplicationGateway $appgw -CertificateFile <newcertPath> -Password $password
```
## <a name="azure-cli"></a>Инфраструктура CLI Azure

```azurecli-interactive
az network application-gateway ssl-cert update \
  -n "<CertName>" \
  --gateway-name "<AppGatewayName>" \
  -g "ResourceGroupName>" \
  --cert-file <PathToCerFile> \
  --cert-password "<password>"
```

## <a name="next-steps"></a>Дополнительная информация

Сведения по настройке разгрузки SSL с использованием шлюза приложений Azure см. в статье [Настройка шлюза приложений для разгрузки SSL с помощью портала](application-gateway-ssl-portal.md)
