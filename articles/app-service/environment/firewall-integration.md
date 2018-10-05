---
title: Блокировка исходящего трафика из среды Службы приложений Azure
description: Руководство по интеграции с брандмауэром Azure для защиты исходящего трафика
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 955a4d84-94ca-418d-aa79-b57a5eb8cb85
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/24/2018
ms.author: ccompy
ms.openlocfilehash: bcd5cb15c003ea146263a6e71a280f7da42ba830
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2018
ms.locfileid: "47167824"
---
# <a name="app-service-environment-certificates-overview"></a>Обзор сертификатов среды Службы приложений 

Среда Службы приложений (ССП) имеет ряд внешних зависимостей, доступ к которым необходим для правильной работы. ССП находится в пользовательской виртуальной сети Azure (VNet). Пользователи должны разрешить трафик зависимостей ССП, что является проблемой для тех пользователей, которым нужно заблокировать все выходы из своей виртуальной сети.

ССП имеет ряд входящих зависимостей. Входящий трафик управления невозможно отправлять через устройство брандмауэра. Исходные адреса этого трафика известны и опубликованы в документе [Адреса управления среды Службы приложений](https://docs.microsoft.com/azure/app-service/environment/management-addresses). Используя эту информацию можно создать группу безопасности сети, которая защитит входящий трафик.

Исходящие зависимости СПП почти полностью определены с помощью FQDN, которые не содержат статических адресов. Отсутствие статических адресов означает, что для блокировки исходящего из СПП трафика нельзя использовать группу безопасности сети. Адреса часто меняются, поэтому создать правила, основанные на текущем разрешении, и использовать их для создания NSG невозможно. 

Решение для защиты исходящих адресов заключается в использовании устройства брандмауэра, которое может контролировать исходящий трафик на основе доменных имен. Команда Azure Networking добавила в предварительную версию новое сетевое устройство под названием "Брандмауэр Azure". Брандмауэр Azure способен ограничивать исходящий трафик протоколов HTTP и HTTPS на основе имени DNS массива назначения.  

## <a name="configuring-azure-firewall-with-your-ase"></a>Настройка брандмауэра Azure с помощью СПП 

Шаги по блокировке исходящих из СПП данных с помощью брандмауэра Azure:

1. Создайте брандмауэр Azure в VNet, где есть или будет находиться ваша СПП. [Документация по брандмауэру Azure](https://docs.microsoft.com/azure/firewall/)
2. В пользовательском интерфейсе брандмауэра Azure выберите тег "FQDN среды Службы приложений"
3. Создайте таблицу маршрутов с помощью адресов управления из документации [Адреса управления среды Службы приложений]( https://docs.microsoft.com/azure/app-service/environment/management-addresses) со следующим прыжком Интернета. Записи таблицы маршрутов необходимы для предотвращения проблем с асимметричной маршрутизацией. 
4. Добавьте маршруты для зависимостей IP-адресов, указанных ниже в зависимостях IP-адреса, со следующим прыжком Интернета. 
5. Добавьте маршрут к таблице маршрутов для 0.0.0.0/0, при этом следующий прыжок должен быть вашим сетевым устройством брандмауэра Azure
6. Создайте в Azure SQL и службе хранилища Azure конечные точки службы для подсети СПП
7. Назначьте таблицу маршрутов, созданную в вашей подсети СПП  

## <a name="application-traffic"></a>Трафик приложения 

Вышеупомянутые шаги позволят вашей СПП работать без проблем. Чтобы удовлетворить потребности приложения, вам все же необходимо выполнить некоторые настройки. С приложениями в СПП, настроенными с помощью брандмауэра Azure, возникают две проблемы.  

- В брандмауэр Azure или таблицу маршрутов необходимо добавить FQDN зависимости приложения
- Маршруты должны создаваться для тех адресов, из которых будет выходить трафик, чтобы избежать проблемы асимметричной маршрутизации

Если ваше приложение имеет зависимости, то их необходимо добавить в брандмауэр Azure. Создайте правила приложения, чтобы разрешать трафик протокола HTTP/HTTPS, и правила сети для всего остального. 

Если вам известен диапазон адресов, из которых будет поступать трафик запроса к приложению, то вы можете добавить его в таблицу маршрутов, назначенную вашей подсети СПП. Если диапазон адресов большой или не задан, то вы можете использовать сетевое устройство, такое как приложение Gateway, которое даст вам один адрес, который необходимо добавить в таблицу маршрутов. Дополнительные сведения о настройке шлюза приложений с подсистемой балансировки нагрузки СПП см. в статье [Интеграция среды Службы приложений с внутренней подсистемой балансировки нагрузки со шлюзом приложений Azure](https://docs.microsoft.com/azure/app-service/environment/integrate-with-application-gateway)


## <a name="dependencies"></a>Зависимости

Служба приложений Azure имеет ряд внешних зависимостей. Их можно разделить по категориям на несколько основных областей:

- Сервисные службы конечной точки — это конечные точки службы, которые, при желании, можно настроить на блокировку исходящего сетевого трафика.
- Конечные точки IP-адреса не обрабатываются с использованием доменного имени. Это может быть проблемой для устройств брандмауэра, которые предполагают, что весь трафик протокола HTTPS будет использовать доменные имена. Конечные точки IP-адреса необходимо добавить в таблицу маршрутов, которая задается в подсети СПП.
- В устройство брандмауэра можно добавить FQDN конечных точек для протокола HTTP/HTTPS.
- Произвольные конечные точки для HTTP/HTTPS — это зависимости, которые могут меняться вместе со средой СПП в зависимости от количества квалификаторов. 
- Зависимости Linux становятся предметом беспокойства только при развертывании Linux-приложений в СПП. Если вы не развертываете Linux-приложения в СПП, то вам не обязательно добавлять эти адреса в брандмауэр. 


#### <a name="service-endpoint-capable-dependencies"></a>Зависимости конечной точки службы 

| Конечная точка |
|----------|
| Azure SQL |
| Хранилище Azure |
| Azure KeyVault |


#### <a name="ip-address-dependencies"></a>Зависимости IP-адреса 

| Конечная точка |
|----------|
| 40.77.24.27:443 |
| 13.82.184.151:443 |
| 13.68.109.212:443 |
| 13.90.249.229:443 |
| 13.91.102.27:443 |
| 104.45.230.69:443 |
| 168.62.226.198:12000 |


#### <a name="fqdn-httphttps-dependencies"></a>Зависимости FQDN протокола HTTP/HTTPS 

| Конечная точка |
|----------|
|graph.windows.net:443 |
|login.live.com:443 |
|login.windows.com:443 |
|login.windows.net:443 |
|login.microsoftonline.com:443 |
|client.wns.windows.com:443 |
|definitionupdates.microsoft.com:443 |
|go.microsoft.com:80 |
|go.microsoft.com:443 |
|www.microsoft.com:80 |
|www.microsoft.com:443 |
|wdcpalt.microsoft.com:443 |
|wdcp.microsoft.com:443 |
|ocsp.msocsp.com:443 |
|mscrl.microsoft.com:443 |
|mscrl.microsoft.com:80 |
|crl.microsoft.com:443 |
|crl.microsoft.com:80 |
|www.thawte.com:443 |
|crl3.digicert.com:80 |
|ocsp.digicert.com:80 |
|csc3-2009-2.crl.verisign.com:80 |
|crl.verisign.com:80 |
|ocsp.verisign.com:80 |
|azperfcounters1.blob.core.windows.net:443 |
|azurewatsonanalysis-prod.core.windows.net:443 |
|global.metrics.nsatc.net:80   |
|az-prod.metrics.nsatc.net:443 |
|antares.metrics.nsatc.net:443 |
|azglobal-black.azglobal.metrics.nsatc.net:443 |
|azglobal-red.azglobal.metrics.nsatc.net:443 |
|antares-black.antares.metrics.nsatc.net:443 |
|antares-red.antares.metrics.nsatc.net:443 |
|maupdateaccount.blob.core.windows.net:443 |
|clientconfig.passport.net:443 |
|packages.microsoft.com:443 |
|schemas.microsoft.com:80 |
|schemas.microsoft.com:443 |
|management.core.windows.net:443 |
|management.core.windows.net:80 |
|www.msftconnecttest.com:80 |
|shavamanifestcdnprod1.azureedge.net:443 |
|validation-v2.sls.microsoft.com:443 |
|flighting.cp.wd.microsoft.com:443 |
|dmd.metaservices.microsoft.com:80 |
|admin.core.windows.net:443 |
|azureprofileruploads.blob.core.windows.net:443 |
|azureprofileruploads2.blob.core.windows.net:443 |
|azureprofileruploads3.blob.core.windows.net:443 |
|azureprofileruploads4.blob.core.windows.net:443 |
|azureprofileruploads5.blob.core.windows.net:443 |

#### <a name="wildcard-httphttps-dependencies"></a>Зависимости подстановочного знака протокола HTTP/HTTPS 

| Конечная точка |
|----------|
|gr-Prod-\*.cloudapp.net:443 |
| \*.management.azure.com:443 |
| \*.update.microsoft.com:443 |
| \*.windowsupdate.microsoft.com:443 |
|grmdsprod\*mini\*.servicebus.windows.net:443 |
|grmdsprod\*lini\*.servicebus.windows.net:443 |
|grsecprod\*mini\*.servicebus.windows.net:443 |
|grsecprod\*lini\*.servicebus.windows.net:443 |
|graudprod\*mini\*.servicebus.windows.net:443 |
|graudprod\*lini\*.servicebus.windows.net:443 |

#### <a name="linux-dependencies"></a>Зависимости Linux 

| Конечная точка |
|----------|
|wawsinfraprodbay063.blob.core.windows.net:443 |
|registry-1.docker.io:443 |
|auth.docker.io:443 |
|production.cloudflare.docker.com:443 |
|download.docker.com:443 |
|us.archive.ubuntu.com:80 |
|download.mono-project.com:80 |
|packages.treasuredata.com:80|
|security.ubuntu.com:80 |

