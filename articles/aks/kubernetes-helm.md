---
title: Развертывание контейнеров с помощью Helm в Kubernetes в Azure
description: Использование средства упаковки Helm для развертывания контейнеров в кластере службы Azure Kubernetes
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 07/13/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: dd2deba25615373765dd3492d03c1ba547c8ba8c
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/14/2018
ms.locfileid: "39055140"
---
# <a name="install-applications-with-helm-in-azure-kubernetes-service-aks"></a>Установка приложения с помощью Helm в службе Azure Kubernetes

[Helm][helm] — это средство упаковки с открытым кодом, которое помогает установить приложения Kubernetes и управлять их жизненным циклом. Аналогично диспетчерам пакетов Linux, таких как *APT* и *Yum*, Helm используется для управления чартами Kubernetes, представляющими собой пакеты предварительно настроенных ресурсов Kubernetes.

В этой статье показано, как настроить и использовать Helm в кластере Kubernetes в AKS.

## <a name="before-you-begin"></a>Перед началом работы

В действиях, описанных в этом документе, предполагается, что кластер AKS создан и к нему установлено подключение `kubectl`. Если вам требуются эти компоненты, см. статью [Развертывание кластера Службы контейнеров Azure (AKS)][aks-quickstart].

## <a name="install-helm-cli"></a>Установка интерфейса командной строки Helm

Интерфейс командной строки Helm — это клиент, который выполняется в системе разработки и позволяет запускать, останавливать и администрировать приложения с помощью Helm.

Если вы используете Azure Cloud Shell, интерфейс командной строки Helm уже установлен. Чтобы установить интерфейс командной строки Helm на компьютере Mac, выполните команду `brew`. Дополнительные параметры установки см. в статье об [установке Helm][helm-install-options].

```console
brew install kubernetes-helm
```

Выходные данные:

```
==> Downloading https://homebrew.bintray.com/bottles/kubernetes-helm-2.9.1.high_sierra.bottle.tar.gz
######################################################################## 100.0%
==> Pouring kubernetes-helm-2.9.1.high_sierra.bottle.tar.gz
==> Caveats
Bash completion has been installed to:
  /usr/local/etc/bash_completion.d
==> Summary
🍺  /usr/local/Cellar/kubernetes-helm/2.9.1: 50 files, 66.2MB
```

## <a name="create-a-service-account"></a>Создание учетной записи службы

Перед развертыванием Helm в кластере с поддержкой RBAC необходимо создать учетную запись службы и привязку роли для службы Tiller. Дополнительные сведения о защите Helm и Tiller в кластере с поддержкой RBAC см. в руководстве по [использованию Tiller, пространств имен и RBAC][tiller-rbac]. Если в кластере не включена поддержка RBAC, пропустите этот шаг.

Создайте файл `helm-rbac.yaml` и скопируйте в него следующий код YAML:

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: tiller
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRoleBinding
metadata:
  name: tiller
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
  - kind: ServiceAccount
    name: tiller
    namespace: kube-system
```

Создайте учетную запись службы и привязку роли с помощью команды `kubectl create`:

```console
kubectl create -f helm-rbac.yaml
```

## <a name="secure-tiller-and-helm"></a>Защита Tiller и Helm

Клиент Helm и служба Tiller проверяют подлинность и взаимодействуют друг с другом с помощью протокола TLS/SSL. Этот метод проверки подлинности позволяет обеспечить безопасность кластера Kubernetes и развертываемых служб. Для повышения безопасности можно создавать собственные подписанные сертификаты. Каждый пользователь Helm получит собственный сертификат клиента, и инициализация Tiller в кластере Kubernetes будет выполняться с помощью сертификатов. Дополнительные сведения см. в статье [Using TLS/SSL between Helm and Tiller][helm-ssl].

Благодаря кластеру Kubernetes с поддержкой RBAC вы можете контролировать уровень доступа Tiller в кластере. Можно определить пространство имен Kubernetes, в котором развертывается Tiller, и ограничить пространства имен, в которых Tiller может развертывать ресурсы. Такой подход позволяет создавать экземпляры Tiller в разных пространствах имен и определять границы развертывания, а также ограничивать область пользователей клиента Helm определенными пространствами имен. Дополнительные сведения см. в статье об [управлении доступом на основе ролей Helm][helm-rbac].

## <a name="configure-helm"></a>Настройка Helm

Чтобы развернуть базовую службу Tiller в кластере AKS, используйте команду [helm init][helm-init]. Если в кластере не включена поддержка RBAC, удалите значение и аргумент `--service-account`. Если вы настроили TLS/SSL для Tiller и Helm, пропустите эту процедуру базовой инициализации и укажите необходимый `--tiller-tls-`, как показано в следующем примере.

```console
helm init --service-account tiller
```

Если вы настроили подключение TLS/SSL между Helm и Tiller, укажите параметры `--tiller-tls-` и имена собственных сертификатов, как показано в следующем примере:

```console
helm init \
    --tiller-tls \
    --tiller-tls-cert tiller.cert.pem \
    --tiller-tls-key tiller.key.pem \
    --tiller-tls-verify \
    --tls-ca-cert ca.cert.pem \
    --service-account tiller
```

## <a name="find-helm-charts"></a>Поиск чартов Helm

Чарты Helm используются для развертывания приложений в кластере Kubernetes. Чтобы найти предварительно созданные чарты Helm, используйте команду [helm search][helm-search].

```console
helm search
```

В следующем кратком примере выходные данные содержат некоторые из доступных для использования чартов Helm:

```
$ helm search

NAME                           CHART VERSION    APP VERSION  DESCRIPTION
stable/acs-engine-autoscaler   2.2.0            2.1.1        Scales worker nodes within agent pools
stable/aerospike               0.1.7            v3.14.1.2    A Helm chart for Aerospike in Kubernetes
stable/anchore-engine          0.1.7            0.1.10       Anchore container analysis and policy evaluatio...
stable/apm-server              0.1.0            6.2.4        The server receives data from the Elastic APM a...
stable/ark                     1.0.1            0.8.2        A Helm chart for ark
stable/artifactory             7.2.1            6.0.0        Universal Repository Manager supporting all maj...
stable/artifactory-ha          0.2.1            6.0.0        Universal Repository Manager supporting all maj...
stable/auditbeat               0.1.0            6.2.4        A lightweight shipper to audit the activities o...
stable/aws-cluster-autoscaler  0.3.3                         Scales worker nodes within autoscaling groups.
stable/bitcoind                0.1.3            0.15.1       Bitcoin is an innovative payment network and a ...
stable/buildkite               0.2.3            3            Agent for Buildkite
stable/burrow                  0.4.4            0.17.1       Burrow is a permissionable smart contract machine
stable/centrifugo              2.0.1            1.7.3        Centrifugo is a real-time messaging server.
stable/cerebro                 0.1.0            0.7.3        A Helm chart for Cerebro - a web admin tool tha...
stable/cert-manager            v0.3.3           v0.3.1       A Helm chart for cert-manager
stable/chaoskube               0.7.0            0.8.0        Chaoskube periodically kills random pods in you...
stable/chartmuseum             1.5.0            0.7.0        Helm Chart Repository with support for Amazon S...
stable/chronograf              0.4.5            1.3          Open-source web application written in Go and R...
stable/cluster-autoscaler      0.6.4            1.2.2        Scales worker nodes within autoscaling groups.
stable/cockroachdb             1.1.1            2.0.0        CockroachDB is a scalable, survivable, strongly...
stable/concourse               1.10.1           3.14.1       Concourse is a simple and scalable CI system.
stable/consul                  3.2.0            1.0.0        Highly available and distributed service discov...
stable/coredns                 0.9.0            1.0.6        CoreDNS is a DNS server that chains plugins and...
stable/coscale                 0.2.1            3.9.1        CoScale Agent
stable/dask                    1.0.4            0.17.4       Distributed computation in Python with task sch...
stable/dask-distributed        2.0.2                         DEPRECATED: Distributed computation in Python
stable/datadog                 0.18.0           6.3.0        DataDog Agent
...
```

Чтобы обновить список чартов, используйте команду [helm repo update][helm-repo-update]. Ниже приведен пример успешного обновления репозитория:

```console
$ helm repo update

Hang tight while we grab the latest from your chart repositories...
...Skip local chart repository
...Successfully got an update from the "stable" chart repository
Update Complete. ⎈ Happy Helming!⎈
```

## <a name="run-helm-charts"></a>Выполнение чартов Helm

Чтобы установить чарты с помощью Helm, используйте команду [helm install][helm-install] и укажите имя чарта для установки. Чтобы увидеть это в действии, мы установим базовое развертывание Wordpress с помощью чарта Helm. Если вы настроили TLS/SSL, добавьте параметр `--tls`, чтобы использовать сертификат клиента Helm.

```console
helm install stable/wordpress
```

В следующем кратком примере выходные данные содержат состояние развертывания ресурсов Kubernetes, созданное чартом Helm:

```
$ helm install stable/wordpress

NAME:   wishful-mastiff
LAST DEPLOYED: Thu Jul 12 15:53:56 2018
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1beta1/Deployment
NAME                       DESIRED  CURRENT  UP-TO-DATE  AVAILABLE  AGE
wishful-mastiff-wordpress  1        1        1           0          1s

==> v1beta1/StatefulSet
NAME                     DESIRED  CURRENT  AGE
wishful-mastiff-mariadb  1        1        1s

==> v1/Pod(related)
NAME                                        READY  STATUS   RESTARTS  AGE
wishful-mastiff-wordpress-6f96f8fdf9-q84sz  0/1    Pending  0         1s
wishful-mastiff-mariadb-0                   0/1    Pending  0         1s

==> v1/Secret
NAME                       TYPE    DATA  AGE
wishful-mastiff-mariadb    Opaque  2     2s
wishful-mastiff-wordpress  Opaque  2     2s

==> v1/ConfigMap
NAME                           DATA  AGE
wishful-mastiff-mariadb        1     2s
wishful-mastiff-mariadb-tests  1     2s

==> v1/PersistentVolumeClaim
NAME                       STATUS   VOLUME   CAPACITY  ACCESS MODES  STORAGECLASS  AGE
wishful-mastiff-wordpress  Pending  default  2s

==> v1/Service
NAME                       TYPE          CLUSTER-IP   EXTERNAL-IP  PORT(S)                     AGE
wishful-mastiff-mariadb    ClusterIP     10.1.116.54  <none>       3306/TCP                    2s
wishful-mastiff-wordpress  LoadBalancer  10.1.217.64  <pending>    80:31751/TCP,443:31264/TCP  2s
...
```

Заполнение адреса *EXTERNAL-IP* службы Wordpress занимает несколько минут, после чего вы можете получить к ней доступ с помощью веб-браузера.

## <a name="list-helm-releases"></a>Список выпусков Helm

Чтобы просмотреть список выпусков, установленных в кластере, используйте команду [helm list][helm-list]. В следующем примере показан выпуск Wordpress, развернутый на предыдущем шаге. Если вы настроили TLS/SSL, добавьте параметр `--tls`, чтобы использовать сертификат клиента Helm.

```console
$ helm list

NAME             REVISION    UPDATED                     STATUS      CHART              NAMESPACE
wishful-mastiff  1           Thu Jul 12 15:53:56 2018    DEPLOYED    wordpress-2.1.3  default
```

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения об управлении развертываниями приложений Kubernetes с помощью Helm см. в документации по Helm.

> [!div class="nextstepaction"]
> [Документация по Helm][helm-documentation]

<!-- LINKS - external -->
[helm]: https://github.com/kubernetes/helm/
[helm-documentation]: https://docs.helm.sh/
[helm-init]: https://docs.helm.sh/helm/#helm-init
[helm-install]: https://docs.helm.sh/helm/#helm-install
[helm-install-options]: https://github.com/kubernetes/helm/blob/master/docs/install.md
[helm-list]: https://docs.helm.sh/helm/#helm-list
[helm-rbac]: https://docs.helm.sh/using_helm/#role-based-access-control
[helm-repo-update]: https://docs.helm.sh/helm/#helm-repo-update
[helm-search]: https://docs.helm.sh/helm/#helm-search
[tiller-rbac]: https://docs.helm.sh/using_helm/#tiller-namespaces-and-rbac
[helm-ssl]: https://docs.helm.sh/using_helm/#using-ssl-between-helm-and-tiller

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
