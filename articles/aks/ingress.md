---
title: Настройка входящего трафика с помощью кластера Службы Azure Kubernetes (AKS)
description: Узнайте как установить и настроить контроллер входящего трафика NGINX, который использует Let's Encrypt для автоматического создания SSL-сертификата в кластере Службы Azure Kubernetes (AKS).
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 07/17/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: d31a3e62aaabf7a865078aa2e7c6d1585466b379
ms.sourcegitcommit: b9786bd755c68d602525f75109bbe6521ee06587
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/18/2018
ms.locfileid: "39126683"
---
# <a name="deploy-an-https-ingress-controller-on-azure-kubernetes-service-aks"></a>Развертывание контроллера входящего трафика HTTPS в Службе Azure Kubernetes (AKS)

Контроллер входящего трафика является программным компонентом, предоставляющим для служб Kubernetes обратный прокси-сервер, настраиваемую маршрутизацию трафика и обработку подключений TLS для последующей передачи. Ресурсы входящего трафика Kubernetes используются при настройке правил входящего трафика и маршрутов для отдельных служб Kubernetes. Применяя контроллер и правила входящего трафика, для маршрутизации трафика в несколько служб в кластере Kubernetes можно использовать один внешний адрес.

В этой статье описывается, как установить и настроить [контроллер входящего трафика NGINX][nginx-ingress] в кластере Службы Azure Kubernetes (AKS). Для автоматического создания и настройки сертификатов [Let's Encrypt][lets-encrypt] используется проект [cert-manager][cert-manager]. Наконец, в кластере AKS запущено несколько приложений, каждое из которых доступно по одному адресу.

## <a name="before-you-begin"></a>Перед началом работы

В этой статье для установки контроллера входящего трафика NGINX cert-manager и примера веб-приложения используется Helm. Поэтому необходимо инициализировать Helm в кластере AKS и использовать учетную запись службы для Tiller. Дополнительную информацию о настройке и использовании Helm см. в статье [Использование Helm со службой Azure Kubernetes][use-helm].

Для этой статьи требуется Azure CLI версии 2.0.41 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0][azure-cli-install].

## <a name="install-an-ingress-controller"></a>Установка контроллера входящего трафика

Используйте Helm для установки контроллера входящего трафика NGINX. Дополнительные сведения см. в [документации по контроллеру входящего трафика NGINX][nginx-ingress].

В следующем примере контроллер устанавливается в пространстве имен `kube-system`. Вы можете указать другое пространство имен для своей среды. Если в кластере AKS не включено RBAC, добавьте `--set rbac.create=false` в команду.

```console
helm install stable/nginx-ingress --namespace kube-system
```

Во время установки для контроллера входящего трафика создается общедоступный IP-адрес Azure. Чтобы получить общедоступный IP-адрес, используйте команду `kubectl get service`. Назначение службе IP-адреса занимает несколько минут.

```
$ kubectl get service -l app=nginx-ingress --namespace kube-system

NAME                                       TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)                      AGE
eager-crab-nginx-ingress-controller        LoadBalancer   10.0.182.160   51.145.155.210  80:30920/TCP,443:30426/TCP   20m
eager-crab-nginx-ingress-default-backend   ClusterIP      10.0.255.77    <none>          80/TCP                       20m
```

Правила доступа еще не созданы. Если вы перейдете на общедоступный IP-адрес, для контроллера входящего трафика NGINX по умолчанию отобразится страница 404, как показано в следующем примере:

![Сервер NGINX по умолчанию](media/ingress/default-back-end.png)

## <a name="configure-a-dns-name"></a>Настройка DNS-имени

Чтобы сертификаты HTTPS работали правильно, настройте для IP-адреса контроллера входящего трафика полное доменное имя. Укажите в этом скрипте IP-адрес контроллера входящего трафика и уникальное имя, которое вы хотите использовать в качестве полного доменного имени.

```console
#!/bin/bash

# Public IP address of your ingress controller
IP="51.145.155.210"

# Name to associate with public IP address
DNSNAME="demo-aks-ingress"

# Get the resource-id of the public ip
PUBLICIPID=$(az network public-ip list --query "[?ipAddress!=null]|[?contains(ipAddress, '$IP')].[id]" --output tsv)

# Update public ip address with DNS name
az network public-ip update --ids $PUBLICIPID --dns-name $DNSNAME
```

Теперь к контроллеру входящего трафика можно получить доступ по полному доменному имени.

## <a name="install-cert-manager"></a>Установка cert-manager

Контроллер входящего трафика NGINX поддерживает обработку подключений TLS для последующей передачи. Получить и настроить сертификаты для HTTPS можно несколькими способами. В этой статье демонстрируется использование проекта [cert-manager][cert-manager], который обеспечивает автоматические функции создания сертификатов [Lets Encrypt][lets-encrypt] и управления ими.

> [!NOTE]
> В этой статье используется среда для Let's Encrypt `staging`. В производственных развертываниях используйте `letsencrypt-prod` и `https://acme-v02.api.letsencrypt.org/directory` в определениях ресурсов и при установке диаграммы Helm.

Чтобы установить контроллер cert-manager в кластере с поддержкой RBAC, используйте следующую команду `helm install`:

```console
helm install stable/cert-manager --set ingressShim.defaultIssuerName=letsencrypt-staging --set ingressShim.defaultIssuerKind=ClusterIssuer
```

Если кластер не поддерживает RBAC, используйте следующую команду:

```console
helm install stable/cert-manager \
  --set ingressShim.defaultIssuerName=letsencrypt-staging \
  --set ingressShim.defaultIssuerKind=ClusterIssuer \
  --set rbac.create=false \
  --set serviceAccount.create=false
```

Дополнительные сведения о конфигурации cert-manager см. в описании [проекта cert-manager][cert-manager].

## <a name="create-a-ca-cluster-issuer"></a>Создание издателя кластера ЦС

Для выдачи сертификатов средству cert-manager нужен ресурс [Issuer][cert-manager-issuer] или [ClusterIssuer][cert-manager-cluster-issuer]. Эти ресурсы Kubernetes имеют аналогичную функциональность, однако `Issuer` работает в отдельном пространстве имен, а `ClusterIssuer` — во всех. Дополнительные сведения см. в документации по [издателю cert-manager][cert-manager-issuer].

Создайте издатель кластера, например `cluster-issuer.yaml`, используя приведенный ниже пример манифеста. Измените адрес электронной почты на действительный адрес вашей организации:

```yaml
apiVersion: certmanager.k8s.io/v1alpha1
kind: ClusterIssuer
metadata:
  name: letsencrypt-staging
spec:
  acme:
    server: https://acme-staging-v02.api.letsencrypt.org/directory
    email: user@contoso.com
    privateKeySecretRef:
      name: letsencrypt-staging
    http01: {}
```

Чтобы создать издатель, используйте команду `kubectl create -f cluster-issuer.yaml`.

```
$ kubectl create -f cluster-issuer.yaml

clusterissuer.certmanager.k8s.io/letsencrypt-prod created
```

## <a name="create-a-certificate-object"></a>Создание объекта сертификата

Далее нужно создать ресурс сертификата. Этот ресурс сертификата определяет необходимый сертификат X.509. Дополнительные сведения см. в описании [сертификатов cert-manager][cert-manager-certificates].

Создайте ресурс сертификата, например `certificates.yaml`, используя приведенный ниже пример манифеста. Обновите *dnsNames* и *домены*, указав для них DNS-имя, которое вы создали на предыдущем шаге.

```yaml
apiVersion: certmanager.k8s.io/v1alpha1
kind: Certificate
metadata:
  name: tls-secret
spec:
  secretName: tls-secret
  dnsNames:
  - demo-aks-ingress.eastus.cloudapp.azure.com
  acme:
    config:
    - http01:
        ingressClass: nginx
      domains:
      - demo-aks-ingress.eastus.cloudapp.azure.com
  issuerRef:
    name: letsencrypt-staging
    kind: ClusterIssuer
```

Чтобы создать ресурс сертификата, используйте команду `kubectl create -f certificates.yaml`.

```
$ kubectl create -f certificates.yaml

certificate.certmanager.k8s.io/tls-secret created
```

## <a name="run-demo-applications"></a>Запуск демонстрационных версий приложений

Контроллер входящего трафика и решение по управлению сертификатами настроены. Теперь запустите две демонстрационные версии приложения в своем кластере AKS. В этом примере для развертывания двух экземпляров простого приложения Hello World применяется Helm.

Чтобы установить примеры диаграмм Helm, добавьте репозиторий примеров Azure в свою среду Helm таким образом:

```console
helm repo add azure-samples https://azure-samples.github.io/helm-charts/
```

Создайте первую демонстрационную версию приложения из диаграммы Helm с помощью такой команды:

```console
helm install azure-samples/aks-helloworld
```

Установите второй экземпляр демонстрационной версии приложения. Укажите новый заголовок для второго экземпляра, чтобы оба приложения визуально отличались. Также задайте уникальное имя службы:

```console
helm install azure-samples/aks-helloworld --set title="AKS Ingress Demo" --set serviceName="ingress-demo"
```

## <a name="create-an-ingress-route"></a>Создание маршрута для входящего трафика

Теперь оба приложения запущены на кластере Kubernetes, несмотря на то что они настроены с помощью службы типа `ClusterIP`. Таким образом приложения не доступны через Интернет. Чтобы сделать их доступными, создайте ресурс входящего трафика Kubernetes. Ресурс входящего трафика настраивает правила, по которым осуществляется маршрутизация трафика к одному из двух приложений.

В следующем примере трафик по адресу `https://demo-aks-ingress.eastus.cloudapp.azure.com/` направляется в службу `aks-helloworld`. Трафик по адресу `https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two` направляется в службу `ingress-demo`. Обновите *узлы* и *узел*, указав DNS-имя, которое вы создали на предыдущем шаге.

Создайте файл `hello-world-ingress.yaml` и скопируйте в него следующий пример кода YAML:

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  annotations:
    kubernetes.io/ingress.class: nginx
    certmanager.k8s.io/cluster-issuer: letsencrypt-staging
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  tls:
  - hosts:
    - demo-aks-ingress.eastus.cloudapp.azure.com
    secretName: tls-secret
  rules:
  - host: demo-aks-ingress.eastus.cloudapp.azure.com
    http:
      paths:
      - path: /
        backend:
          serviceName: aks-helloworld
          servicePort: 80
      - path: /hello-world-two
        backend:
          serviceName: ingress-demo
          servicePort: 80
```

Создайте ресурс входящего трафика, используя команду `kubectl create -f hello-world-ingress.yaml`.

```
$ kubectl create -f hello-world-ingress.yaml

ingress.extensions/hello-world-ingress created
```

## <a name="test-the-ingress-configuration"></a>Проверка конфигурации входящего трафика

Откройте в браузере адрес с полным доменным именем вашего контроллера входящего трафика Kubernetes, например *https://demo-aks-ingress.eastus.cloudapp.azure.com*.

Поскольку в этих примерах используется `letsencrypt-staging`, браузер не доверяет выданному сертификату SSL. Чтобы продолжить работу с приложением, примите предупреждение. Информация о сертификате свидетельствует о том, что этот сертификат *Fake LE Intermediate X1* выдан Let's Encrypt. Этот поддельный сертификат указывает на то, что `cert-manager` правильно обработал запрос и получил сертификат от поставщика.

![Промежуточный сертификат Let's Encrypt](media/ingress/staging-certificate.png)

Когда вы меняете настройки Let's Encrypt, чтобы использовать `prod` вместо `staging`, применяется доверенный сертификат, выданный Let's Encrypt, как показано в следующем примере:

![Сертификат Let's Encrypt](media/ingress/certificate.png)

В браузере отображается демонстрационная версия приложения:

![Первый пример приложения](media/ingress/app-one.png)

Теперь добавьте к FQDN путь */hello-world-two*, например *https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two*. Отобразится второе демонстрационное приложение с пользовательским заголовком:

![Второй пример приложения](media/ingress/app-two.png)

## <a name="next-steps"></a>Дополнительная информация

В данной статье упоминаются некоторые внешние компоненты для AKS. Чтобы узнать больше об этих компонентах, см. следующие страницы проекта:

- [Интерфейс командной строки Helm][helm-cli]
- [Контроллер входящего трафика NGINX][nginx-ingress]
- [cert-manager][cert-manager]

<!-- LINKS - external -->
[helm-cli]: https://docs.microsoft.com/azure/aks/kubernetes-helm#install-helm-cli
[cert-manager]: https://github.com/jetstack/cert-manager
[cert-manager-certificates]: https://cert-manager.readthedocs.io/en/latest/reference/certificates.html
[cert-manager-cluster-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/clusterissuers.html
[cert-manager-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/issuers.html
[lets-encrypt]: https://letsencrypt.org/
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx

<!-- LINKS - internal -->
[use-helm]: kubernetes-helm.md
[azure-cli-install]: /cli/azure/install-azure-cli