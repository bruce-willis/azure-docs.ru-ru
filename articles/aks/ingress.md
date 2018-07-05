---
title: Настройка входящего трафика с помощью кластера Службы Azure Kubernetes (AKS)
description: Установка и настройка контроллера входящего трафика NGINX в кластере Службы Azure Kubernetes (AKS).
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 06/25/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: fcf0b6f3b7f6d75006d8c10aab041c25fc0d8c39
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/25/2018
ms.locfileid: "36751291"
---
# <a name="https-ingress-on-azure-kubernetes-service-aks"></a>Входящий трафик HTTPS в Службе Azure Kubernetes (AKS)

Контроллер входящего трафика является программным компонентом, предоставляющим для служб Kubernetes обратный прокси-сервер, настраиваемую маршрутизацию трафика и обработку подключений TLS для последующей передачи. Ресурсы входящего трафика Kubernetes используются при настройке правил входящего трафика и маршрутов для отдельных служб Kubernetes. Применяя контроллер и правила входящего трафика, для маршрутизации трафика в несколько служб в кластере Kubernetes можно использовать один внешний адрес.

В этом документе описывается пример развертывания [контроллера входящего трафика NGINX][nginx-ingress] в кластере Службы Azure Kubernetes (AKS). Кроме того, проект [cert-manager][cert-manager] используется для автоматического создания и настройки сертификатов [Let's Encrypt][lets-encrypt]. Наконец, в кластере AKS запущено несколько приложений, каждое из которых доступно по одному адресу.

## <a name="install-an-ingress-controller"></a>Установка контроллера входящего трафика

Используйте Helm для установки контроллера входящего трафика NGINX. Дополнительные сведения см. в [документации по контроллеру входящего трафика NGINX][nginx-ingress].

В этом примере контроллер устанавливается в пространстве имен `kube-system`. Вы можете изменить его на свое пространство имен. Если в кластере AKS не включено RBAC, добавьте `--set rbac.create=false` в команду. Дополнительные сведения см. на [диаграмме nginx-ingress][nginx-ingress].

```bash
helm install stable/nginx-ingress --namespace kube-system
```

Во время установки для контроллера входящего трафика создается общедоступный IP-адрес Azure. Чтобы получить общедоступный IP-адрес, используйте команду kubectl get service. Процесс назначения службе IP-адреса может занять некоторое время.

```console
$ kubectl get service -l app=nginx-ingress --namespace kube-system

NAME                                       TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)                      AGE
eager-crab-nginx-ingress-controller        LoadBalancer   10.0.182.160   51.145.155.210  80:30920/TCP,443:30426/TCP   20m
eager-crab-nginx-ingress-default-backend   ClusterIP      10.0.255.77    <none>          80/TCP                       20m
```

Так как правила входящего трафика не созданы, при переходе к общедоступному IP-адресу по умолчанию будет отображаться страница с ошибкой "404 — страница не найдена" для контролеров входящего трафика NGINX.

![Сервер NGINX по умолчанию](media/ingress/default-back-end.png)

## <a name="configure-dns-name"></a>Настройка DNS-имени

Так как используются сертификаты HTTPS, для IP-адреса контроллеров входящего трафика необходимо настроить полное доменное имя. В этом примере полное доменное имя в Azure создается с помощью Azure CLI. Укажите в скрипте IP-адрес контроллера входящего трафика и имя, которое вы хотите использовать в качестве полного доменного имени.

```bash
#!/bin/bash

# Public IP address
IP="51.145.155.210"

# Name to associate with public IP address
DNSNAME="demo-aks-ingress"

# Get the resource-id of the public ip
PUBLICIPID=$(az network public-ip list --query "[?ipAddress!=null]|[?contains(ipAddress, '$IP')].[id]" --output tsv)

# Update public ip address with dns name
az network public-ip update --ids $PUBLICIPID --dns-name $DNSNAME
```

Теперь к контроллеру входящего трафика можно получить доступ по полному доменному имени.

## <a name="install-cert-manager"></a>Установка cert-manager

Контроллер входящего трафика NGINX поддерживает обработку подключений TLS для последующей передачи. Существует несколько способов получения и настройки сертификатов для использования протокола HTTPS. В этом документе демонстрируется способ с использованием [cert-manager][cert-manager], который обеспечивает автоматические возможности создания сертификата [Lets Encrypt][lets-encrypt] и управления им.

Чтобы установить контроллер cert-manager, используйте приведенную ниже команду установки Helm.

```bash
helm install stable/cert-manager --set ingressShim.defaultIssuerName=letsencrypt-prod --set ingressShim.defaultIssuerKind=ClusterIssuer
```

Если в кластере не включено RBAC, используйте эту команду.

```bash
helm install stable/cert-manager \
  --set ingressShim.defaultIssuerName=letsencrypt-prod \
  --set ingressShim.defaultIssuerKind=ClusterIssuer \
  --set rbac.create=false \
  --set serviceAccount.create=false
```

Дополнительные сведения о конфигурации cert-manager см. в описании [проекта cert-manager][cert-manager].

## <a name="create-ca-cluster-issuer"></a>Создание издателя кластера ЦС

Для выдачи сертификатов средству cert-manager нужен ресурс [Issuer][cert-manager-issuer] или [ClusterIssuer][cert-manager-cluster-issuer]. Эти ресурсы имеют аналогичную функциональность, однако `Issuer` работает в отдельном пространстве имен, а `ClusterIssuer` — во всех. Дополнительные сведения см. в документации по [издателю cert-manager][cert-manager-issuer].

Создайте издатель кластера, используя приведенный ниже манифест. Измените адрес электронной почты на действительный адрес вашей организации.

```yaml
apiVersion: certmanager.k8s.io/v1alpha1
kind: ClusterIssuer
metadata:
  name: letsencrypt-prod
spec:
  acme:
    server: https://acme-v02.api.letsencrypt.org/directory
    email: user@contoso.com
    privateKeySecretRef:
      name: letsencrypt-prod
    http01: {}
```

## <a name="create-certificate-object"></a>Создание объекта сертификата

Далее нужно создать ресурс сертификата. Этот ресурс сертификата определяет необходимый сертификат X.509. Дополнительные сведения см. в описании [сертификатов cert-manager][cert-manager-certificates].

Создайте ресурс сертификата, используя приведенный ниже манифест.

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
    name: letsencrypt-prod
    kind: ClusterIssuer
```

## <a name="run-application"></a>Запуск приложения

На этом этапе уже настроены контроллер входящего трафика и решение по управлению сертификатами. Теперь запустите несколько приложений в кластере AKS.

В этом примере Helm применяется для запуска нескольких экземпляров простого приложения Hello World.

Перед запуском приложения добавьте репозиторий Helm образцов Azure в систему разработки.

```bash
helm repo add azure-samples https://azure-samples.github.io/helm-charts/
```

Запустите диаграмму AKS для приложения Hello World, используя следующую команду.

```bash
helm install azure-samples/aks-helloworld
```

Теперь установите второй экземпляр приложения Hello World.

Укажите новый заголовок для второго экземпляра, чтобы оба приложения визуально отличались. Вам также необходимо задать уникальное имя службы. Эти конфигурации видны в следующей команде.

```bash
helm install azure-samples/aks-helloworld --set title="AKS Ingress Demo" --set serviceName="ingress-demo"
```

## <a name="create-ingress-route"></a>Настройка маршрутизации входящего трафика

Оба приложения запущены на кластере Kubernetes, несмотря на то что были настроены с помощью службы типа `ClusterIP`. Таким образом приложения не доступны через Интернет. Чтобы сделать их доступными, создайте ресурс входящего трафика Kubernetes. Ресурс входящего трафика настраивает правила, по которым осуществляется маршрутизация трафика к одному из двух приложений.

Создайте файл `hello-world-ingress.yaml` и скопируйте в него следующий код YAML.

Обратите внимание, что трафик по адресу `https://demo-aks-ingress.eastus.cloudapp.azure.com/` направляется в службу `aks-helloworld`. Трафик по адресу `https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two` направляется в службу `ingress-demo`.

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  annotations:
    kubernetes.io/ingress.class: nginx
    certmanager.k8s.io/cluster-issuer: letsencrypt-prod
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

Создайте ресурс входящего трафика с помощью команды `kubectl apply`.

```console
kubectl apply -f hello-world-ingress.yaml
```

## <a name="test-the-ingress-configuration"></a>Проверка конфигурации входящего трафика

Перейдите к контроллеру входящего трафика Kubernetes по полному доменному имени. Вы увидите приложение Hello World.

![Первый пример приложения](media/ingress/app-one.png)

Теперь перейдите к контроллеру входящего трафика по полному доменному имени, используя путь `/hello-world-two`. Вы увидите приложение Hello World с пользовательским заголовком.

![Второй пример приложения](media/ingress/app-two.png)

Обратите также внимание на то, что соединение зашифровано и используется сертификат, полученный через Let's Encrypt.

![Сертификат Let's Encrypt](media/ingress/certificate.png)

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о программном обеспечении, рассматриваемом в этом документе:

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
