---
title: Создание кластера Service Fabric на платформе Linux в Azure | Документация Майкрософт
description: Из этого руководства вы узнаете, как развернуть кластер Service Fabric на платформе Linux в имеющейся виртуальной сети с помощью Azure CLI.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/27/2018
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 27600cd4656f70b4cd01745667c0e0fd2a2f4997
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/27/2018
ms.locfileid: "47405825"
---
# <a name="tutorial-deploy-a-linux-service-fabric-cluster-into-an-azure-virtual-network"></a>Руководство. Развертывание кластера Service Fabric на платформе Linux в виртуальной сети Azure

Это руководство представляет первую часть цикла. Вы узнаете, как развернуть кластер Service Fabric на платформе Linux в [виртуальной сети Azure](../virtual-network/virtual-networks-overview.md) с помощью Azure CLI и шаблона. После окончания этого учебника у вас будет кластер в облаке, в который можно разворачивать приложения. Создание кластера Windows с помощью PowerShell описывается в разделе [Развертывание безопасного кластера Service Fabric на платформе Windows в виртуальной сети Azure](service-fabric-tutorial-create-vnet-and-windows-cluster.md).

Из этого руководства вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * создание виртуальной сети в Azure с помощью Azure CLI;
> * создание защищенного кластера Service Fabric в Azure с помощью Azure CLI;
> * Защита кластера с помощью сертификата X.509
> * подключение к кластеру с помощью интерфейса командной строки Service Fabric;
> * Удаление кластера

Из этого цикла руководств вы узнаете, как выполнять следующие задачи:
> [!div class="checklist"]
> * создание защищенного кластера в Azure;
> * [свертывание и развертывание кластера](service-fabric-tutorial-scale-cluster.md);
> * [Обновление среды выполнения кластера](service-fabric-tutorial-upgrade-cluster.md)
> * [Удаление кластера](service-fabric-tutorial-delete-cluster.md)

## <a name="prerequisites"></a>Предварительные требования

Перед началом работы с этим руководством выполните следующие действия:

* Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Установите [интерфейс командной строки Service Fabric](service-fabric-cli.md).
* Установите [Azure CLI](/cli/azure/install-azure-cli).

Ниже приведены процедуры для создания кластера Service Fabric с пятью узлами. Чтобы рассчитать затраты, связанные с запуском кластера Service Fabric в Azure, используйте [калькулятор цен Azure](https://azure.microsoft.com/pricing/calculator/).

## <a name="key-concepts"></a>Основные понятия

[Кластер Service Fabric](service-fabric-deploy-anywhere.md) — это подключенный к сети набор виртуальных машин или физических компьютеров, в котором вы развертываете микрослужбы и управляете ими. Кластеры можно масштабировать до нескольких тысяч машин. Узлом называется компьютер или виртуальная машина, которая входит в состав кластера. Каждому узлу назначается имя (строка). Узлы имеют свои характеристики, в частности свойства размещения.

Тип узла определяет размер, количество и свойства набора виртуальных машин в кластере. Каждый определенный тип узлов настраивается как [масштабируемый набор виртуальных машин](/azure/virtual-machine-scale-sets/), т. е. как вычислительный ресурс Azure, который можно использовать для развертывания коллекции виртуальных машин и управления ею как набором. Каждый тип узла поддерживает возможность независимого масштабирования, имеет разные наборы открытых портов и собственные метрики емкости. Типы узлов используются для определения ролей набора узлов кластера, например "интерфейсная часть" или "серверная часть".  В кластере могут быть узлы нескольких типов. Тип основных узлов должен включать не менее пяти виртуальных машин для производственных кластеров и не менее трех виртуальных машин для тестовых кластеров.  [Системные службы Service Fabric](service-fabric-technical-overview.md#system-services) размещаются на узлах основного типа.

Кластер защищен сертификатом кластера. Сертификат кластера — это сертификат X.509, используемый для защиты обмена данными между узлами и для аутентификации конечных точек управления кластера в клиенте управления.  Этот сертификат кластера также предоставляет SSL для API управления HTTPS и Service Fabric Explorer по протоколу HTTPS. Самозаверяющие сертификаты используются для тестовых кластеров.  Для рабочих кластеров в качестве сертификата нужно использовать сертификат из центра сертификации (ЦС).

Сертификат кластера должен:

* Содержать закрытый ключ.
* Быть создан для обмена ключами, которые можно экспортировать в файл обмена личной информацией (PFX-файл).
* Иметь имя субъекта, совпадающее с доменным именем, которое используется для обращения к кластеру Service Fabric. Это необходимо, чтобы предоставить SSL-протокол конечным точкам управления HTTPS в кластере и Service Fabric Explorer. Не удается получить SSL-сертификат от центра сертификации (ЦС) в домене .cloudapp.azure.com. Необходимо получить имя личного домена для кластера. При запросе сертификата из ЦС имя субъекта сертификата должно совпадать с именем личного домена, используемого для кластера.

Хранилище ключей Azure используется для управления сертификатами кластеров Service Fabric в Azure.  При развертывании кластера в Azure поставщик ресурсов Azure, ответственный за создание кластеров Service Fabric, извлекает сертификаты из хранилища ключей и устанавливает их на виртуальные машины кластера.

С помощью этого руководства мы развернем кластер с пятью узлами одного типа. Однако при развертывании любого рабочего кластера важно правильно [спланировать его загрузку](service-fabric-cluster-capacity.md). Вот несколько моментов, которые необходимо учесть:

* Количество узлов и их типов для создания кластера.
* Свойства каждого типа узлов (например, размер, основной тип, возможность подключения из Интернета, количество виртуальных машин и др.).
* Надежность и устойчивость характеристик кластера.

## <a name="download-and-explore-the-template"></a>Скачивание и изучение шаблона

Скачайте следующие файлы шаблона Resource Manager:

* [AzureDeploy.json][template]
* [AzureDeploy.Parameters.json][parameters]

Этот шаблон развертывает в виртуальной сети защищенный кластер с пятью виртуальными машинами и одним типом узла.  Другие примеры шаблонов можно найти на сайте [GitHub](https://github.com/Azure-Samples/service-fabric-cluster-templates). Файл [AzureDeploy.json][template] развертывает ряд ресурсов, включая следующие.

### <a name="service-fabric-cluster"></a>Кластер Service Fabric

Кластер Linux развернут со следующими характеристиками:

* один тип узла;
* пять узлов на первичном типе узла (можно настроить в параметрах шаблона);
* ОС: Ubuntu 16.04 LTS (можно настроить в параметрах шаблона);
* защищенный сертификат (можно настроить в параметрах шаблона);
* [служба DNS](service-fabric-dnsservice.md) включена;
* [уровень устойчивости](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) Bronze (можно настроить в параметрах шаблона);
* [уровень надежности](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster) Silver (можно настроить в параметрах шаблона);
* конечная точка подключения клиента: 19000 (можно настроить в параметрах шаблона);
* конечная точка HTTP-шлюза: 19080 (можно настроить в параметрах шаблона).

### <a name="azure-load-balancer"></a>Azure Load Balancer

Подсистема балансировки нагрузки развернута, а проверки и правила настроены для следующих портов:

* конечная точка подключения клиента: 19000;
* конечная точка HTTP шлюза: 19080;
* порт приложения: 80;
* порт приложения: 443;

### <a name="virtual-network-and-subnet"></a>Виртуальная сеть и подсеть

В параметрах шаблона объявляются имена виртуальной сети и подсети.  В параметрах шаблона также объявляются адресные пространства виртуальной сети и подсети:

* адресное пространство виртуальной сети: 10.0.0.0/16;
* адресное пространство подсети Service Fabric: 10.0.2.0/24.

Если нужны другие порты приложений, нужно настроить ресурс Microsoft.Network/loadBalancers, чтобы разрешить входящий трафик.

## <a name="set-template-parameters"></a>Установка параметров шаблона

В файле параметров [AzureDeploy.Parameters][parameters] объявляются многие значения, используемые для развертывания кластера и связанных ресурсов. Далее представлены некоторые параметры, которые может понадобиться изменить для развертывания:

|Параметр|Пример значения|Примечания|
|---|---||
|adminUserName|vmadmin| Имя администратора кластера виртуальных машин. |
|adminPassword|Password#1234| Пароль администратора для кластера виртуальных машин.|
|clusterName|mysfcluster123| Имя кластера. |
|location|southcentralus| Расположение кластера. |
|certificateThumbprint|| <p>Если создается самозаверяющий сертификат или указывается файл сертификата, значение должно быть пустым.</p><p>Если необходимо использовать имеющийся сертификат, который вы ранее передали в хранилище ключей, заполните значение отпечатка SHA1 сертификата. Например, 6190390162C988701DB5676EB81083EA608DCCF3. </p>|
|certificateUrlValue|| <p>Если создается самозаверяющий сертификат или указывается файл сертификата, значение должно быть пустым.</p><p>Если необходимо использовать имеющийся сертификат, который вы ранее передали в хранилище ключей, укажите URL-адрес сертификата. Например, https://mykeyvault.vault.azure.net:443/secrets/mycertificate/02bea722c9ef4009a76c5052bcbf8346.</p>|
|sourceVaultValue||<p>Если создается самозаверяющий сертификат или указывается файл сертификата, значение должно быть пустым.</p><p>Если необходимо использовать имеющийся сертификат, который вы ранее передали в хранилище ключей, укажите сведения об исходном хранилище. Например, /subscriptions/333cc2c84-12fa-5778-bd71-c71c07bf873f/resourceGroups/MyTestRG/providers/Microsoft.KeyVault/vaults/MYKEYVAULT.</p>|

<a id="createvaultandcert" name="createvaultandcert_anchor"></a>

## <a name="deploy-the-virtual-network-and-cluster"></a>Развертывание кластера и виртуальной сети

Настройте топологию сети и разверните кластер Service Fabric. Шаблон Resource Manager [AzureDeploy.json][template] создает виртуальную сеть и подсеть для Service Fabric. Шаблон также развертывает кластер с включенным сертификатом безопасности.  Для рабочих кластеров в качестве сертификата нужно использовать сертификат из центра сертификации (ЦС). Самозаверяющий сертификат можно использовать для защиты тестовых кластеров.

### <a name="create-a-cluster-using-an-existing-certificate"></a>Создание кластера с помощью имеющегося сертификата

В приведенном ниже сценарии используется команда [az sf cluster create](/cli/azure/sf/cluster?view=azure-cli-latest#az_sf_cluster_create) и шаблон, которые позволяют развернуть новый кластер с помощью имеющегося сертификата. Эта команда также создает хранилище ключей в Azure и отправляет сертификат.

```azurecli
ResourceGroupName="sflinuxclustergroup"
Location="southcentralus"
Password="q6D7nN%6ck@6"
VaultName="linuxclusterkeyvault"
VaultGroupName="linuxclusterkeyvaultgroup"
CertPath="C:\MyCertificates\MyCertificate.pem"

# sign in to your Azure account and select your subscription
az login
az account set --subscription <guid>

# Create a new resource group for your deployment and give it a name and a location.
az group create --name $ResourceGroupName --location $Location

# Create the Service Fabric cluster.
az sf cluster create --resource-group $ResourceGroupName --location $Location \
   --certificate-password $Password --certificate-file $CertPath \
   --vault-name $VaultName --vault-resource-group $ResourceGroupName  \
   --template-file AzureDeploy.json --parameter-file AzureDeploy.Parameters.json
```

### <a name="create-a-cluster-using-a-new-self-signed-certificate"></a>Создание кластера с помощью нового самозаверяющего сертификата

В приведенном ниже сценарии используется команда [az sf cluster create](/cli/azure/sf/cluster?view=azure-cli-latest#az_sf_cluster_create) и шаблон, которые позволяют развернуть в Azure новый кластер. Кроме того, при помощи этой команды создается хранилище ключей в Azure, в него добавляется новый самозаверяющий сертификат и локально скачивается файл сертификата.

```azurecli
ResourceGroupName="sflinuxclustergroup"
ClusterName="sflinuxcluster"
Location="southcentralus"
Password="q6D7nN%6ck@6"
VaultName="linuxclusterkeyvault"
VaultGroupName="linuxclusterkeyvaultgroup"
CertPath="C:\MyCertificates"

az sf cluster create --resource-group $ResourceGroupName --location $Location --cluster-name $ClusterName --template-file C:\temp\cluster\AzureDeploy.json --parameter-file C:\temp\cluster\AzureDeploy.Parameters.json --certificate-password $Password --certificate-output-folder $CertPath --certificate-subject-name $ClusterName.$Location.cloudapp.azure.com --vault-name $VaultName --vault-resource-group $ResourceGroupName
```

## <a name="connect-to-the-secure-cluster"></a>Подключение к безопасному кластеру

Подключитесь к кластеру, выполнив в командной строке Service Fabric команду `sfctl cluster select` и указав свой ключ.  Примечание. Используйте параметр **--no-verify** только для самозаверяющего сертификата.

```azurecli
sfctl cluster select --endpoint https://aztestcluster.southcentralus.cloudapp.azure.com:19080 \
--pem ./aztestcluster201709151446.pem --no-verify
```

Выполните команду `sfctl cluster health`, чтобы проверить подключение к кластеру и убедиться, что кластер работает.

```azurecli
sfctl cluster health
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Кластер, который вы только что создали, используется в других статьях этого цикла руководств. Если вы не собираетесь немедленно приступить к следующей статье, то можете [удалить кластер](service-fabric-cluster-delete.md), чтобы за него не взималась плата.

## <a name="next-steps"></a>Дополнительная информация

Из этого руководства вы узнали, как выполнить следующие задачи:

> [!div class="checklist"]
> * создание виртуальной сети в Azure с помощью Azure CLI;
> * создание защищенного кластера Service Fabric в Azure с помощью Azure CLI;
> * Защита кластера с помощью сертификата X.509
> * подключение к кластеру с помощью интерфейса командной строки Service Fabric;
> * Удаление кластера

Теперь перейдите к следующему руководству, чтобы узнать, как масштабировать кластер.
> [!div class="nextstepaction"]
> [Scale a Service Fabric cluster](service-fabric-tutorial-scale-cluster.md) (Масштабирование кластера Service Fabric)

[template]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/5-VM-Ubuntu-1-NodeTypes-Secure/AzureDeploy.json
[parameters]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/5-VM-Ubuntu-1-NodeTypes-Secure/AzureDeploy.Parameters.json
