---
title: Как использовать службу управления API Azure в виртуальных сетях со шлюзом приложений | Документация Майкрософт
description: Узнайте, как установить и настроить службу управления API Azure во внутренней виртуальной сети с интерфейсным шлюзом приложений (WAF)
services: api-management
documentationcenter: ''
author: solankisamir
manager: kjoshi
editor: vlvinogr
ms.assetid: a8c982b2-bca5-4312-9367-4a0bbc1082b1
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/26/2018
ms.author: sasolank
ms.openlocfilehash: ce4fd27c89f529b9c12999689152c3025648d2ce
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/10/2018
ms.locfileid: "40038399"
---
# <a name="integrate-api-management-in-an-internal-vnet-with-application-gateway"></a>Интеграция службы управления API во внутреннюю сеть со шлюзом приложений

##<a name="overview"> </a> Обзор

Если настроить службу управления API для работы в виртуальной сети в режиме внутренней сети, она будет доступна только из этой виртуальной сети. Шлюз приложений Azure — это служба PaaS, выполняющая функции подсистемы балансировки нагрузки на сетевом уровне 7. Это служба обратного прокси-сервера, которая содержит также брандмауэр веб-приложения (WAF).

Объединяя возможности службы управления API, работающей во внутренней виртуальной сети, и интерфейса шлюза приложений, вы можете реализовать следующие сценарии.

* Использовать один ресурс управления API одновременно и для внешних, и для внутренних потребителей.
* Использовать один ресурс управления API, определив для него в службе управления API подмножество API-интерфейсов, доступных для внешних потребителей.
* Создать простой способ включать и отключать доступ из Интернета к управлению API.

## <a name="prerequisites"></a>Предварительные требования

Чтобы выполнить действия, описанные в этой статье, необходимо следующее:

* Активная подписка Azure.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

* Сертификаты — PFX- и CER-файл для имени узла API и PFX-файл для имени узла на портале разработчика.

##<a name="scenario"> </a> Сценарий

В этой статье объясняется, как можно использовать одну службу управления API одновременно для внутренних и внешних потребителей, а также как сделать ее единым интерфейсным компонентом для локальных и облачных API. Также вы узнаете, как предоставить некоторое подмножество этих API-интерфейсов (в нашем примере они выделены зеленым цветом) для внешнего использования, применив функцию маршрутизации, предоставляемую Шлюзом приложений.

В первом примере конфигурации все API-интерфейсы управляются только из виртуальной сети. Внутренние потребители (выделены оранжевым цветом) могут обращаться ко всем внутренним и внешним API. Так трафик никогда не выходит в Интернет, а благодаря каналам Express Route достигается высокая производительность.

![Маршрут URL-адреса](./media/api-management-howto-integrate-internal-vnet-appgateway/api-management-howto-integrate-internal-vnet-appgateway.png)

## <a name="before-you-begin"> </a> Перед началом работы

* Убедитесь, что у вас установлена последняя версия Azure PowerShell. Дополнительные сведения см. в статье [Использование Windows PowerShell с диспетчером ресурсов](https://docs.microsoft.com/azure/azure-resource-manager/powershell-azure-resource-manager).

## <a name="what-is-required-to-create-an-integration-between-api-management-and-application-gateway"></a>Что нужно для того, чтобы интегрировать управление API со шлюзом приложений?

* **Пул тыловых серверов**. Это внутренний виртуальный IP-адрес службы управления API.
* **Параметры внутреннего пула серверов**. Каждый пул имеет такие параметры, как порт, протокол и сходство на основе файлов cookie. Эти параметры применяются ко всем серверам в этом пуле.
* **Внешний порт**. Общедоступный порт, открытый в шлюзе приложений. Трафик, поступающий на этот порт, перенаправляется на один из тыловых серверов.
* **Прослушиватель**. У прослушивателя есть интерфейсный порт, протокол (Http или Https — с учетом регистра) и имя SSL-сертификата (в случае настройки разгрузки SSL).
* **Правило**. Это правило связывает прослушиватель с пулом тыловых серверов.
* **Пользовательские проверки работоспособности**. Шлюз приложений по умолчанию использует проверки на основе IP-адреса, чтобы найти активные серверы в пуле BackendAddressPool. Служба управления API отвечает только на те запросы, которые имеют правильный заголовок узла, поэтому стандартные проверки завершаются ошибкой. Вам следует определить пользовательскую проверку работоспособности, чтобы шлюз приложений мог определять работоспособность службы и передавать в нее запросы.
* **Сертификат для личного домена**. Чтобы осуществлять доступ из Интернета в службу управления API, создайте сопоставление CNAME, связывающее имя узла с DNS-именем внешнего интерфейса Шлюза приложений. Это позволит службе управления API распознавать допустимость заголовка hostname и сертификата, который передан в шлюз приложений и пересылается в управление API. В этом примере мы будем использовать два сертификата — для внутренней службы и портала разработчика.  

## <a name="overview-steps"> </a> Действия по интеграции управления API со шлюзом приложений

1. Создание группы ресурсов для диспетчера ресурсов.
2. Создание виртуальной сети, подсети и общедоступного IP-адреса для шлюза приложений. Создайте еще одну подсеть для управления API.
3. Создайте службу управления API в той подсети виртуальной сети, которую вы создали ранее. Должен использоваться режим внутренней сети.
4. Настройте имя личного домена в службе управления API.
5. Создайте объект конфигурации шлюза приложений.
6. Создайте ресурс шлюза приложений.
7. Создайте сопоставление CNAME, связывающее DNS-имя шлюза приложений с именем узла прокси-сервера управления API.

## <a name="exposing-the-developer-portal-externally-through-application-gateway"></a>Внешнее предоставление портала разработчика через Шлюз приложений

В этом руководстве мы также предоставим **портал разработчика** для внешней аудитории через Шлюз приложений. Для этого нужно создать прослушиватель, пробу, параметры и правила портала разработчика. Все подробности предоставляются на соответствующих этапах.

> [!WARNING]
> В описанной настройке портала разработчика, доступ к которому осуществляется через Шлюз приложений, могут возникнуть проблемы с проверкой подлинности с использованием AAD или сторонних решений.

## <a name="create-a-resource-group-for-resource-manager"></a>Создание группы ресурсов для диспетчера ресурсов.

### <a name="step-1"></a>Шаг 1

Вход в Azure

```powershell
Login-AzureRmAccount
```

Выполните аутентификацию со своими учетными данными.

### <a name="step-2"></a>Шаг 2

Выберите нужную подписку.

```powershell
$subscriptionId = "00000000-0000-0000-0000-000000000000" # GUID of your Azure subscription
Get-AzureRmSubscription -Subscriptionid $subscriptionId | Select-AzureRmSubscription
```

### <a name="step-3"></a>Шаг 3.

Создайте группу ресурсов. Если вы используете существующую группу, пропустите этот шаг.

```powershell
$resGroupName = "apim-appGw-RG" # resource group name
$location = "West US"           # Azure region
New-AzureRmResourceGroup -Name $resGroupName -Location $location
```

Диспетчер ресурсов Azure требует, чтобы все группы ресурсов указывали расположение. Оно используется в качестве расположения по умолчанию для всех ресурсов данной группы. Убедитесь, что во всех командах для создания шлюза приложений используется одна группа ресурсов.

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Создание виртуальной сети и подсети для шлюза приложений

В следующем примере показано создание виртуальной сети с помощью Resource Manager.

### <a name="step-1"></a>Шаг 1

Создайте переменную подсети с диапазоном адресов 10.0.0.0/24, которая будет использоваться для шлюза приложений при создании виртуальной сети.

```powershell
$appgatewaysubnet = New-AzureRmVirtualNetworkSubnetConfig -Name "apim01" -AddressPrefix "10.0.0.0/24"
```

### <a name="step-2"></a>Шаг 2

Создайте переменную подсети с диапазоном адресов 10.0.1.0/24, которая будет использоваться для управления API при создании виртуальной сети.

```powershell
$apimsubnet = New-AzureRmVirtualNetworkSubnetConfig -Name "apim02" -AddressPrefix "10.0.1.0/24"
```

### <a name="step-3"></a>Шаг 3.

Создайте виртуальную сеть с именем **appgwvnet** в группе ресурсов **apim-appGw-RG** для региона "Западная часть США", назначив ей префикс 10.0.0.0/16. Создайте в ней подсети 10.0.0.0/24 и 10.0.1.0/24.

```powershell
$vnet = New-AzureRmVirtualNetwork -Name "appgwvnet" -ResourceGroupName $resGroupName -Location $location -AddressPrefix "10.0.0.0/16" -Subnet $appgatewaysubnet,$apimsubnet
```

### <a name="step-4"></a>Шаг 4.

Назначьте переменную для подсети, которая будет использоваться далее.

```powershell
$appgatewaysubnetdata = $vnet.Subnets[0]
$apimsubnetdata = $vnet.Subnets[1]
```

## <a name="create-an-api-management-service-inside-a-vnet-configured-in-internal-mode"></a>Создание службы управления API в виртуальной сети, настроенной в режиме внутренней сети

В следующем примере демонстрируется создание службы управления API в виртуальной сети, настроенной только для внутреннего доступа.

### <a name="step-1"></a>Шаг 1

Создайте объект виртуальной сети для управления API, используя созданную выше переменную подсети $apimsubnetdata.

```powershell
$apimVirtualNetwork = New-AzureRmApiManagementVirtualNetwork -Location $location -SubnetResourceId $apimsubnetdata.Id
```

### <a name="step-2"></a>Шаг 2

Создайте службу управления API в виртуальной сети.

```powershell
$apimServiceName = "ContosoApi"       # API Management service instance name
$apimOrganization = "Contoso"         # organization name
$apimAdminEmail = "admin@contoso.com" # administrator's email address
$apimService = New-AzureRmApiManagement -ResourceGroupName $resGroupName -Location $location -Name $apimServiceName -Organization $apimOrganization -AdminEmail $apimAdminEmail -VirtualNetwork $apimVirtualNetwork -VpnType "Internal" -Sku "Developer"
```

Когда завершится выполнение предыдущей команды, выполните инструкции из раздела [DNS Configuration ](api-management-using-with-internal-vnet.md#apim-dns-configuration) (Настройка DNS) для доступа к службе управления API во внутренней виртуальной сети. Этот шаг может занять более получаса.

## <a name="set-up-a-custom-domain-name-in-api-management"></a>Настройка пользовательского доменного имени в службе управления API

### <a name="step-1"></a>Шаг 1

Отправьте сертификаты с закрытыми ключами для доменов. В этом примере используется `api.contoso.net` и `portal.contoso.net`.  

```powershell
$gatewayHostname = "api.contoso.net"                 # API gateway host
$portalHostname = "portal.contoso.net"               # API developer portal host
$gatewayCertCerPath = "C:\Users\Contoso\gateway.cer" # full path to api.contoso.net .cer file
$gatewayCertPfxPath = "C:\Users\Contoso\gateway.pfx" # full path to api.contoso.net .pfx file
$portalCertPfxPath = "C:\Users\Contoso\portal.pfx"   # full path to portal.contoso.net .pfx file
$gatewayCertPfxPassword = "certificatePassword123"   # password for api.contoso.net pfx certificate
$portalCertPfxPassword = "certificatePassword123"    # password for portal.contoso.net pfx certificate

$certUploadResult = Import-AzureRmApiManagementHostnameCertificate -ResourceGroupName $resGroupName -Name $apimServiceName -HostnameType "Proxy" -PfxPath $gatewayCertPfxPath -PfxPassword $gatewayCertPfxPassword -PassThru
$certPortalUploadResult = Import-AzureRmApiManagementHostnameCertificate -ResourceGroupName $resGroupName -Name $apimServiceName -HostnameType "Proxy" -PfxPath $portalCertPfxPath -PfxPassword $portalCertPfxPassword -PassThru
```

### <a name="step-2"></a>Шаг 2

После отправки сертификатов создайте объекты конфигурации имени узла для прокси-сервера и портала.  

```powershell
$proxyHostnameConfig = New-AzureRmApiManagementHostnameConfiguration -CertificateThumbprint $certUploadResult.Thumbprint -Hostname $gatewayHostname
$portalHostnameConfig = New-AzureRmApiManagementHostnameConfiguration -CertificateThumbprint $certPortalUploadResult.Thumbprint -Hostname $portalHostname
$result = Set-AzureRmApiManagementHostnames -Name $apimServiceName -ResourceGroupName $resGroupName –PortalHostnameConfiguration $portalHostnameConfig -ProxyHostnameConfiguration $proxyHostnameConfig
```

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Создание общедоступного IP-адреса для конфигурации интерфейсной части

Создайте общедоступный IP-ресурс **publicIP01** в группе ресурсов.

```powershell
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName $resGroupName -name "publicIP01" -location $location -AllocationMethod Dynamic
```

IP-адрес назначается шлюзу приложений при запуске службы.

## <a name="create-application-gateway-configuration"></a>Создание конфигурации шлюза приложений

Перед созданием шлюза приложений необходимо настроить все элементы конфигурации. В ходе следующих шагов создаются необходимые элементы конфигурации для ресурса шлюза приложений.

### <a name="step-1"></a>Шаг 1

Создайте конфигурацию IP-адресов шлюза приложений с именем **gatewayIP01**. При запуске шлюз приложений получает IP-адрес из настроенной подсети. Затем шлюз маршрутизирует сетевой трафик на IP-адреса из пула внутренних IP-адресов. Помните, что для каждого экземпляра требуется отдельный IP-адрес.

```powershell
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name "gatewayIP01" -Subnet $appgatewaysubnetdata
```

### <a name="step-2"></a>Шаг 2

Настройте интерфейсный порт IP для конечной точки с общедоступным IP-адресом. Это порт, к которому подключаются пользователи.

```powershell
$fp01 = New-AzureRmApplicationGatewayFrontendPort -Name "port01"  -Port 443
```

### <a name="step-3"></a>Шаг 3.

Настройте внешний IP-адрес, используя конечную точку с общедоступным IP-адресом.

```powershell
$fipconfig01 = New-AzureRmApplicationGatewayFrontendIPConfig -Name "frontend1" -PublicIPAddress $publicip
```

### <a name="step-4"></a>Шаг 4.

Настройте в Шлюзе приложений сертификаты для повторного шифрования и расшифровки проходящего трафика.

```powershell
$certPwd = ConvertTo-SecureString $gatewayCertPfxPassword -AsPlainText -Force
$cert = New-AzureRmApplicationGatewaySslCertificate -Name "cert01" -CertificateFile $gatewayCertPfxPath -Password $certPwd
$certPortalPwd = ConvertTo-SecureString $portalCertPfxPassword -AsPlainText -Force
$certPortal = New-AzureRmApplicationGatewaySslCertificate -Name "cert02" -CertificateFile $portalCertPfxPath -Password $certPortalPwd
```

### <a name="step-5"></a>Шаг 5

Создайте прослушиватели HTTP для Шлюза приложений. Назначьте им IP-конфигурацию внешнего интерфейса, порт и SSL-сертификаты.

```powershell
$listener = New-AzureRmApplicationGatewayHttpListener -Name "listener01" -Protocol "Https" -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -SslCertificate $cert -HostName $gatewayHostname -RequireServerNameIndication true
$portalListener = New-AzureRmApplicationGatewayHttpListener -Name "listener02" -Protocol "Https" -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -SslCertificate $certPortal -HostName $portalHostname -RequireServerNameIndication true
```

### <a name="step-6"></a>Шаг 6

Создайте пользовательские пробы для конечной точки домена прокси-сервера `ContosoApi` службы управления API. По умолчанию на всех службах управления API для конечной точки проверки работоспособности используется путь `/status-0123456789abcdef`. Укажите `api.contoso.net` в качестве имени узла пользовательской пробы, чтобы защитить его с помощью SSL-сертификата.

> [!NOTE]
> Когда в общедоступной службе Azure создается служба с именем `contosoapi.azure-api.net`, для ее прокси-сервера по умолчанию назначается имя узла `contosoapi`.
>

```powershell
$apimprobe = New-AzureRmApplicationGatewayProbeConfig -Name "apimproxyprobe" -Protocol "Https" -HostName $gatewayHostname -Path "/status-0123456789abcdef" -Interval 30 -Timeout 120 -UnhealthyThreshold 8
$apimPortalProbe = New-AzureRmApplicationGatewayProbeConfig -Name "apimportalprobe" -Protocol "Https" -HostName $portalHostname -Path "/signin" -Interval 60 -Timeout 300 -UnhealthyThreshold 8
```

### <a name="step-7"></a>Шаг 7

Передайте сертификат, который будет использоваться для ресурсов внутреннего пула, поддерживающих протокол SSL. Это тот же сертификат, который вы указали выше на шаге 4.

```powershell
$authcert = New-AzureRmApplicationGatewayAuthenticationCertificate -Name "whitelistcert1" -CertificateFile $gatewayCertCerPath
```

### <a name="step-8"></a>Шаг 8

Настройте HTTP для внутреннего пула шлюза приложений. К настройкам относится и предел времени ожидания, по истечении которого запрос к внутренним серверам отменяется. Это значение отличается от времени ожидания проверки работоспособности.

```powershell
$apimPoolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name "apimPoolSetting" -Port 443 -Protocol "Https" -CookieBasedAffinity "Disabled" -Probe $apimprobe -AuthenticationCertificates $authcert -RequestTimeout 180
$apimPoolPortalSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name "apimPoolPortalSetting" -Port 443 -Protocol "Https" -CookieBasedAffinity "Disabled" -Probe $apimPortalProbe -AuthenticationCertificates $authcert -RequestTimeout 180
```

### <a name="step-9"></a>Шаг 9.

Настройте пул IP-адресов серверной части с именем **apimbackend**, указав для него внутренний виртуальный IP-адрес, созданный ранее для службы управления API.

```powershell
$apimProxyBackendPool = New-AzureRmApplicationGatewayBackendAddressPool -Name "apimbackend" -BackendIPAddresses $apimService.PrivateIPAddresses[0]
```

### <a name="step-10"></a>Шаг 10

Создайте правила, чтобы Шлюз приложений использовал базовую маршрутизацию.

```powershell
$rule01 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "rule1" -RuleType Basic -HttpListener $listener -BackendAddressPool $apimProxyBackendPool -BackendHttpSettings $apimPoolSetting
$rule02 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "rule2" -RuleType Basic -HttpListener $portalListener -BackendAddressPool $apimProxyBackendPool -BackendHttpSettings $apimPoolPortalSetting
```

> [!TIP]
> Измените -RuleType и маршрутизацию, чтобы ограничить доступ к определенным страницам портала разработчика.

### <a name="step-11"></a>Шаг 11

Настройте число экземпляров и размер шлюза приложений. Здесь мы используем [WAF SKU](../application-gateway/application-gateway-webapplicationfirewall-overview.md) для повышения уровня безопасности ресурса управления API.

```powershell
$sku = New-AzureRmApplicationGatewaySku -Name "WAF_Medium" -Tier "WAF" -Capacity 2
```

### <a name="step-12"></a>Шаг 12

Настройте WAF в режиме предотвращения.

```powershell
$config = New-AzureRmApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode "Prevention"
```

## <a name="create-application-gateway"></a>Создание шлюза приложений

Создайте шлюз приложений, используя все созданные ранее объекты конфигурации.

```powershell
$appgwName = "apim-app-gw"
$appgw = New-AzureRmApplicationGateway -Name $appgwName -ResourceGroupName $resGroupName -Location $location -BackendAddressPools $apimProxyBackendPool -BackendHttpSettingsCollection $apimPoolSetting, $apimPoolPortalSetting  -FrontendIpConfigurations $fipconfig01 -GatewayIpConfigurations $gipconfig -FrontendPorts $fp01 -HttpListeners $listener, $portalListener -RequestRoutingRules $rule01, $rule02 -Sku $sku -WebApplicationFirewallConfig $config -SslCertificates $cert, $certPortal -AuthenticationCertificates $authcert -Probes $apimprobe, $apimPortalProbe
```

## <a name="cname-the-api-management-proxy-hostname-to-the-public-dns-name-of-the-application-gateway-resource"></a>Создание сопоставления CNAME для связи имени узла прокси-сервера управления API с общедоступным DNS-именем ресурса шлюза приложений

После создания шлюза следует настроить внешний интерфейс для обмена данными. Если вы используете общедоступный IP-адрес, шлюзу приложений требуется динамически назначаемое имя DNS, которое может быть неудобным для использования.

Для DNS-имени шлюза приложений следует создать запись CNAME, которая будет связывать имя узла прокси-сервера (в примере выше это `api.contoso.net`) с этим DNS-именем. Чтобы настроить запись CNAME интерфейсного IP-адреса, получите сведения о шлюзе приложений и соответствующее IP- или DNS-имя с помощью элемента PublicIPAddress. Мы не рекомендуем использовать записи типа A, так как виртуальный IP-адрес может измениться после перезапуска шлюза.

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName $resGroupName -Name "publicIP01"
```

##<a name="summary"> </a> Сводка
Служба управления API Azure, настроенная в виртуальной сети, предоставляет свой шлюз в качестве единого интерфейса для всех настроенных API-интерфейсов, как локальных, так и облачных. Интеграция шлюза приложений с управлением API дает вам дополнительную гибкость, позволяя избирательно предоставлять доступ к API-интерфейсам через Интернет. Также вы можете использовать брандмауэр веб-приложения в качестве интерфейсного компонента для экземпляра службы управления API.

##<a name="next-steps"> </a> Дальнейшие действия
* Дополнительные сведения о шлюзе приложений Azure:
  * [Обзор шлюза приложений](../application-gateway/application-gateway-introduction.md)
  * [Application Gateway Web Application Firewall (preview)](../application-gateway/application-gateway-webapplicationfirewall-overview.md) (Брандмауэр веб-приложения шлюза приложений (предварительная версия))
  * [Создание шлюза приложений с помощью маршрутизации на основе пути](../application-gateway/application-gateway-create-url-route-arm-ps.md)
* См. дополнительные сведения о службе управлении API и виртуальных сетях
  * [Использование службы управления API Azure совместно с внутренней виртуальной сетью](api-management-using-with-internal-vnet.md)
  * [How to use Azure API Management with virtual networks](api-management-using-with-vnet.md) (Использование управления API Azure в виртуальных сетях)
