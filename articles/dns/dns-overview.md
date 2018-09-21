---
title: Что такое Azure DNS?
description: Обзор служб размещения DNS в Microsoft Azure. Размещение домена в Microsoft Azure.
author: vhorne
manager: jeconnoc
ms.service: dns
ms.topic: overview
ms.date: 6/7/2018
ms.author: victorh
ms.openlocfilehash: 0812f5e3e23fbf49afec91524b4de2ff0eb7cd9a
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/14/2018
ms.locfileid: "45574187"
---
# <a name="what-is-azure-dns"></a>Что такое Azure DNS?

Azure DNS является службой размещения для доменов DNS, предоставляющей разрешение имен с помощью инфраструктуры Microsoft Azure. Размещая домены в Azure, вы можете управлять своими записями DNS с помощью тех же учетных данных, API и инструментов и оплачивать использование, как и другие службы Azure.

Невозможно использовать Azure DNS для приобретения имени домена. Уплатив ежегодный сбор, можно приобрести имя домена, используя [Веб-приложения Azure](https://docs.microsoft.com/azure/app-service/custom-dns-web-site-buydomains-web-app#buy-the-domain) или регистратор сторонних доменных имен. Затем можно разместить домены в Azure DNS, чтобы управлять записями. Дополнительные сведения см. в статье [Делегирование домена в Azure DNS](dns-domain-delegation.md).

Azure DNS включает следующие функции.

## <a name="reliability-and-performance"></a>Надежность и производительность

Домены DNS в Azure DNS размещаются в глобальной сети DNS-серверов Azure. Azure DNS использует адресацию любому устройству сети, чтобы на каждый запрос DNS отвечал ближайший доступный DNS-сервер. Это обеспечивает высокую производительность и высокий уровень доступности для вашего домена.

## <a name="security"></a>Безопасность

Служба Azure DNS работает на основе Azure Resource Manager. Таким образом, диспетчер ресурсов получает следующие возможности.

* [Управление доступом на основе ролей.](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#access-control) Позволяет управлять доступом к определенным действиям для организации.

* [Журналы действий.](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#activity-logs) Используются для наблюдения за тем, как пользователь организации изменяет ресурс, а также для поиска ошибки при устранении неполадок.

* [Блокировка ресурсов.](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources) Позволяет заблокировать подписку, ресурс или группу ресурсов, чтобы другие пользователи в организации не могли случайно удалить или изменить критически важные ресурсы.

Дополнительные сведения см. в разделе [Как защитить зоны и записи DNS](dns-protect-zones-recordsets.md). 


## <a name="ease-of-use"></a>Простота использования

Служба Azure DNS может управлять DNS-записями служб Azure, а также предоставлять DNS для внешних ресурсов. Azure DNS интегрирована в портал Azure и использует те же учетные данные, данные для выставления счетов и контракты поддержки, что и другие службы Azure. 

Выставление счета за использование DNS выполняется на основании количества зон DNS, размещенных в Azure, и количества запросов DNS. Дополнительные сведения о ценах см. в статье [Цены на Azure DNS](https://azure.microsoft.com/pricing/details/dns/).

Доменами и записями можно управлять с помощью портала Azure, командлетов Azure PowerShell и кроссплатформенного Azure CLI. Приложения, для которых необходимо автоматическое управление DNS, можно интегрировать со службой с помощью REST API и пакетов SDK.

## <a name="customizable-virtual-networks-with-private-domains"></a>Настраиваемые виртуальные сети с частными доменами

Azure DNS также поддерживает частные DNS-домены, которые сейчас находятся в общедоступной предварительной версии. Это позволяет использовать пользовательские доменные имена в частных виртуальных сетях, а не только имена, предоставленные Azure, как ранее.

Дополнительные сведения см. в статье [Использование Azure DNS для частных доменов](private-dns-overview.md).


## <a name="next-steps"></a>Дополнительная информация

* Дополнительные сведения о записях и зонах DNS см. в разделе [Обзор зон и записей DNS](dns-zones-records.md).

* Дополнительные сведения о том, как создать зону DNS в Azure DNS, см. в разделе [Приступая к работе со службой DNS Azure с помощью портала Azure](./dns-getstarted-create-dnszone-portal.md).

* Часто задаваемые вопросы о DNS см. в статье [Вопросы и ответы о Azure DNS](dns-faq.md).

