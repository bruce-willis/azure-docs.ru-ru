---
title: Руководство. Размещение домена и дочернего домена в Azure DNS
description: В этом руководстве показано, как настроить Azure DNS для размещения зон DNS.
services: dns
author: vhorne
manager: jeconnoc
ms.service: dns
ms.topic: tutorial
ms.date: 6/13/2018
ms.author: victorh
ms.openlocfilehash: ea0dc257d691326bc073b4cbff37e847a6990f02
ms.sourcegitcommit: f31bfb398430ed7d66a85c7ca1f1cc9943656678
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/28/2018
ms.locfileid: "47452308"
---
# <a name="tutorial-host-your-domain-in-azure-dns"></a>Руководство. Размещение домена в Azure DNS

Azure DNS позволяет размещать домен DNS и управлять записями DNS. Размещая домены в Azure, вы можете управлять своими записями DNS с помощью тех же учетных данных, API и инструментов и оплачивать использование, как и другие службы Azure. 

Предположим, что вы приобрели домен contoso.net у регистратора доменных имен и создали зону с именем contoso.net в Azure DNS. Регистратор предоставляет вам как владельцу домена возможность настройки записей сервера имен (NS) для домена. Регистратор будет хранить эти записи сервера имен в родительском домене NET. Пользователи Интернета по всему миру будут направляться в ваш домен в зоне Azure DNS при попытке разрешить записи DNS в contoso.net.


Из этого руководства вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Создание зоны DNS
> * Получение списка серверов имен
> * Делегирование домена
> * Проверка работы делегирования имен


Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="create-a-dns-zone"></a>Создание зоны DNS

1. Войдите на портал Azure.
1. В левом верхнем углу выберите **Создать ресурс** > **Сеть** > **Зона DNS**, чтобы открыть страницу **Создание зоны DNS**.

   ![Зона DNS](./media/dns-delegate-domain-azure-dns/openzone650.png)

1. На странице **Создание зоны DNS** введите следующие значения, а затем выберите **Создать**.

   | **Параметр** | **Значение** | **Дополнительные сведения** |
   |---|---|---|
   |**Имя**|[ваше доменное имя] |Имя домена, который был приобретен. В этом руководстве используется contoso.net в качестве примера.|
   |**Подписка**|[Ваша подписка]|Выберите подписку для создания зоны.|
   |**Группа ресурсов**|**Создать:** contosoRG|Создайте группу ресурсов. Имя группы ресурсов должно быть уникальным в пределах выбранной подписки. |
   |**Местоположение.**|Восточная часть США||

> [!NOTE]
> Расположение группы ресурсов никак не влияет на зону DNS. Расположение зоны DNS всегда является глобальным и не отображается.

## <a name="retrieve-name-servers"></a>Получение серверов имен

Прежде чем делегировать зоны DNS в службу DNS Azure, необходимо знать имена серверов зоны. Когда создается зона, служба DNS Azure выделяет серверы имен из пула.

1. Создав зону DNS, на портале Azure на панели **Избранное** выберите **Все ресурсы**. На странице **Все ресурсы** выберите зону DNS. Если выбранная подписка имеет несколько ресурсов, введите доменное имя в поле **Фильтровать по имени** для получения доступа к шлюзу приложений. 

1. Получите серверы имен на странице "Зона DNS". В этом примере зоне contoso.net назначены серверы имен *ns1-01.azure-dns.com*, *ns2-01.azure-dns.net*, *ns3-01.azure-dns.org* и *ns4-01.azure-dns.info*.

   ![Список серверов имен](./media/dns-delegate-domain-azure-dns/viewzonens500.png)

Служба Azure DNS автоматически создает в зоне заслуживающие доверия записи NS для назначенных серверов имен.


## <a name="delegate-the-domain"></a>Делегирование домена

Теперь, когда зона DNS создана и у вас есть серверы имен, необходимо обновить родительский домен с серверами имен Azure DNS. У каждого регистратора есть собственные средства управления DNS для изменения записей серверов имен домена. На странице управления DNS регистратора замените записи NS на серверы имен Azure DNS.

При делегировании домена в Azure DNS вам необходимо использовать имена серверов доменных имен, предоставленные службой Azure DNS. Рекомендуем всегда использовать все четыре сервера имен независимо от имени домена. Для делегирования домена не требуется, чтобы в сервере имен и вашем домене содержался один и тот же домен верхнего уровня.

> [!NOTE]
> При копировании адресов серверов имен обязательно копируйте точку в конце каждого адреса. Эта точка указывает конец полного доменного имени. Некоторые регистраторы могут добавлять точку, если имя NS не завершается ею. Но в соответствии с DNS RFC следует включать точку в конце адреса, так как не стоит рассчитывать на то, что каждый регистратор будет добавлять ее автоматически.

Делегирование с использованием серверов имен в собственной зоне (также известны как *серверы личных имен*) в настоящее время не поддерживается в Azure DNS.

## <a name="verify-that-the-delegation-is-working"></a>Проверка работы делегирования имен

По завершении делегирования можно проверить, работает ли разрешение имен. Это можно сделать с помощью такого средства, как *nslookup*, запросив запись SOA (начальная запись зоны) для зоны. Запись SOA автоматически создается при создании зоны. После завершения делегирования, возможно, придется подождать 10 минут или дольше, прежде чем можно будет убедиться, что оно успешно работает. Для изменений в системе DNS может потребоваться некоторое время.

Нет необходимости указывать серверы имен Azure DNS. Если делегирование настроено правильно, в ходе обычного процесса разрешения DNS серверы имен находятся автоматически.

В командной строке введите команду nslookup, аналогичную следующей.

```
nslookup -type=SOA contoso.net
```

Ниже приведен пример ответа из предыдущей команды.

```
Server: ns1-04.azure-dns.com
Address: 208.76.47.4

contoso.net
primary name server = ns1-04.azure-dns.com
responsible mail addr = msnhst.microsoft.com
serial = 1
refresh = 900 (15 mins)
retry = 300 (5 mins)
expire = 604800 (7 days)
default TTL = 300 (5 mins)
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Можно сохранить группу ресурсов **contosoRG**, если вы намерены изучать следующее руководство. В противном случае удалите группу ресурсов **contosoRG**, чтобы удалить ресурсы, созданные в этом руководстве. Чтобы сделать это, щелкните группу ресурсов **contosoRG**, а затем нажмите кнопку **Удалить группу ресурсов**. 

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве была создана зона DNS для домена и делегирована в Azure DNS. Чтобы узнать об Azure DNS и веб-приложениях, перейдите к руководству для веб-приложений.

> [!div class="nextstepaction"]
> [Создание записей DNS для веб-приложения в пользовательском домене](./dns-web-sites-custom-domain.md)
