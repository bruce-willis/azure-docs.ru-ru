---
title: Использование Draft с AKS и реестром контейнеров Azure
description: Использование Draft с AKS и реестром контейнеров Azure
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 08/15/2018
ms.author: iainfou
ms.openlocfilehash: 15fbf254eb479f0935e154806795ebd00cff6adf
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/11/2018
ms.locfileid: "44345953"
---
# <a name="use-draft-with-azure-kubernetes-service-aks"></a>Использование Draft со службой Azure Kubernetes (AKS)

Draft — это инструмент с открытым кодом, помогающий упаковывать и развертывать контейнеры приложений в кластере Kubernetes, что позволяет больше сосредоточиться на цикле разработки, то есть на "внутреннем цикле" усиленной разработки. Draft работает по мере разработки кода, но перед фиксацией в системе управления версиями. С помощью Draft можно быстро повторно развернуть приложение в Kubernetes при изменении кода. Дополнительные сведения о Draft см. в [документации по Draft на сайте GitHub][draft-documentation].

В этой статье показано, как использовать Draft в кластере Kubernetes в AKS.

## <a name="prerequisites"></a>Предварительные требования

В действиях, описанных в этой статье, предполагается, что создан кластер AKS и с ним установлено подключение `kubectl`. Если вам требуются эти компоненты, прочитайте статью [Развертывание кластера Службы контейнеров Azure (AKS)][aks-quickstart].

Вам необходим частный реестр Docker в Реестре контейнеров Azure (ACR). Инструкции по созданию экземпляра ACR см. в статье [Краткое руководство. Создание реестра контейнеров с использованием Azure CLI][acr-quickstart].

Также в кластере AKS нужно установить Helm. Сведения об установке и настройке Helm см. в статье [Установка приложения с помощью Helm в службе Azure Kubernetes][aks-helm].

Наконец, необходимо установить [Docker](https://www.docker.com).

## <a name="install-draft"></a>Установка Draft

CLI Draft — это клиент, который выполняется в системе разработки и позволяет развертывать код в кластер Kubernetes. Чтобы установить интерфейс командной строки Draft на компьютере Mac, выполните команду `brew`. Дополнительные параметры установки см. в [руководстве по установке Draft][draft-documentation].

> [!NOTE]
> При установке Draft до версии 0.12 сначала удалите Draft из кластера с помощью команды `helm delete --purge draft`, а затем удалить локальную конфигурацию, выполнив команду `rm -rf ~/.draft`. В MacOS выполните команду `brew upgrade draft`.

```console
brew tap azure/draft
brew install draft
```

Теперь инициализируйте Draft с помощью команды `draft init`:

```console
draft init
```

## <a name="configure-draft"></a>Настройка Draft

Draft создает образы контейнеров локально, а затем развертывает их из локального реестра (например, Minikube) или использует указанный вами реестр образов. В этой статье используется Реестр контейнеров Azure (ACR), поэтому необходимо установить отношение доверия между кластером AKS и реестром ACR, а затем настроить Draft для отправки образов контейнера в ACR.

### <a name="create-trust-between-aks-cluster-and-acr"></a>Создание отношения доверия между кластером AKS и ACR

Чтобы установить отношения доверия между кластером AKS и реестром ACR, предоставьте субъекту-службе Azure Active Directory, используемому кластером AKS, разрешение на доступ к реестру ACR. В следующих командах укажите `<resourceGroupName>`, заменив `<aksName>` именем своего кластера AKS, а `<acrName>` — именем реестра ACR:

```azurecli
# Get the service principal ID of your AKS cluster
AKS_SP_ID=$(az aks show --resource-group <resourceGroupName> --name <aksName> --query "servicePrincipalProfile.clientId" -o tsv)

# Get the resource ID of your ACR instance
ACR_RESOURCE_ID=$(az acr show --resource-group <resourceGroupName> --name <acrName> --query "id" -o tsv)

# Create a role assignment for your AKS cluster to access the ACR instance
az role assignment create --assignee $AKS_SP_ID --scope $ACR_RESOURCE_ID --role contributor
```

Дополнительные сведения о получении доступа к ACR см. в разделе [Аутентификация с помощью реестра контейнеров Azure из Службы Azure Kubernetes](../container-registry/container-registry-auth-aks.md).

### <a name="configure-draft-to-push-to-and-deploy-from-acr"></a>Настройка Draft для отправки и развертывания из ACR

Теперь, когда установлено отношение доверия между AKS и ACR, настройте использование ACR из кластера AKS.

1. Задайте значение *registry* в файле конфигурации Draft. В следующих командах замените `<acrName>` именем своего реестра ACR:

    ```console
    draft config set registry <acrName>.azurecr.io
    ```

1. Войдите в реестр ACR, выполнив команду [az acr login][az-acr-login]:

    ```azurecli
    az acr login --name <acrName>
    ```

Так как между AKS и ACR были созданы отношения доверия, для отправки в реестр ACR или получения из него пароли или секреты не требуются. Аутентификация выполняется на уровне Azure Resource Manager с помощью Azure Active Directory.

## <a name="run-an-application"></a>Запуск приложения

Чтобы увидеть Draft в действии, давайте развернем пример приложения из [репозитория Draft][draft-repo]. Сначала клонируйте репозиторий:

```console
git clone https://github.com/Azure/draft
```

Перейдите в каталог примеров Java:

```console
cd draft/examples/example-java/
```

Чтобы запустить процесс, используйте команду `draft create`. Эта команда создает артефакты, которые используются для запуска приложения в кластере Kubernetes. Эти элементы включают файл Dockerfile, диаграмму Helm и файл *draft.toml*, который является файлом конфигурации Draft.

```
$ draft create

--> Draft detected Java (92.205567%)
--> Ready to sail
```

Чтобы запустить пример приложения в кластере AKS, используйте команду `draft up`. Эта команда создает файл Docker, чтобы создать образ контейнера, отправляет образ в ACR и, наконец, устанавливает диаграмму Helm для запуска приложения в AKS.

При первом выполнении этой команды отправка и получение образа контейнера может занять некоторое время. После кэширования базовых уровней время, необходимое для развертывания приложения, значительно сократится.

```
$ draft up

Draft Up Started: 'example-java': 01CMZAR1F4T1TJZ8SWJQ70HCNH
example-java: Building Docker Image: SUCCESS ⚓  (73.0720s)
example-java: Pushing Docker Image: SUCCESS ⚓  (19.5727s)
example-java: Releasing Application: SUCCESS ⚓  (4.6979s)
Inspect the logs with `draft logs 01CMZAR1F4T1TJZ8SWJQ70HCNH`
```

Если у вас возникли проблемы с отправкой образа Docker, войдите в реестр ACR с помощью команды [az acr login][az-acr-login], а затем снова выполните команду `draft up`.

## <a name="test-the-application-locally"></a>Локальное тестирование приложения

Чтобы проверить приложение, используйте команду `draft connect`. Эта команда устанавливает безопасное прокси-подключение к модулю pod Kubernetes. По завершении доступ к приложению можно получить через предоставленный URL-адрес.

> [!NOTE]
> Скачивание образа контейнера и запуск приложения могут занять несколько минут. Если при доступе к приложению появляется ошибка, повторите попытку подключения.

```
$ draft connect

Connect to java:4567 on localhost:49804
[java]: SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
[java]: SLF4J: Defaulting to no-operation (NOP) logger implementation
[java]: SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
[java]: == Spark has ignited ...
[java]: >> Listening on 0.0.0.0:4567
```

Чтобы получить доступ к своему приложению, откройте веб-браузер по адресу и порту, указанному в выходных данных `draft connect`, например *http://localhost:49804*. 

![Пример приложения Java, выполняемый с помощью Draft](media/kubernetes-draft/sample-app.png)

Нажмите клавиши `Control+C`, чтобы остановить прокси-подключение.

> [!NOTE]
> Можно также использовать команду `draft up --auto-connect`, чтобы создать и развернуть приложение, а затем немедленно подключиться к первому запущенному контейнеру.

## <a name="access-the-application-on-the-internet"></a>Доступ к приложению через Интернет

На предыдущем шаге было создано прокси-подключение к модулю pod приложения в кластере AKS. При разработке и тестировании приложения можно предоставить доступ к приложению через Интернет. Для предоставления доступа к приложению через Интернет создайте службу Kubernetes с типом [LoadBalancer][kubernetes-service-loadbalancer] или же создайте [контроллер входящего трафика][kubernetes-ingress]. Давайте создадим службу *LoadBalancer*.

Во-первых, обновите пакет Draft *values.yaml*, чтобы указать создание службы с типом *LoadBalancer*:

```console
vi charts/java/values.yaml
```

Найдите свойство *service.type* и обновите значение из *ClusterIP* до *LoadBalancer*, как показано в следующем кратком примере:

```yaml
[...]
service:
  name: java
  type: LoadBalancer
  externalPort: 80
  internalPort: 4567
[...]
```

Сохраните и закройте файл, а затем повторно запустите приложение, выполнив команду `draft up`:

```console
draft up
```

Возвращение общедоступного IP-адреса службы займет несколько минут. Чтобы отследить ход выполнения, используйте команду `kubectl get service` с параметром *watch*:

```console
kubectl get service --watch
```

Изначально для параметра *EXTERNAL-IP* службы отображается состояние *pending* (ожидание):

```
NAME                TYPE          CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
example-java-java   LoadBalancer  10.0.141.72   <pending>     80:32150/TCP   2m
```

Как только значение в столбце EXTERNAL-IP изменится с состояния *ожидания* на IP-адрес, используйте команду `Control+C`, чтобы остановить процесс отслеживания `kubectl`:

```
NAME                TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)        AGE
example-java-java   LoadBalancer   10.0.141.72   52.175.224.118  80:32150/TCP   7m
```

Чтобы увидеть приложение, перейдите по внешнему IP-адресу подсистемы балансировки нагрузки с помощью команды `curl`:

```
$ curl 52.175.224.118

Hello World, I'm Java
```

## <a name="iterate-on-the-application"></a>Выполнение итерации приложения

Теперь, когда Draft настроен, а приложение выполняется в Kubernetes, можно настроить итерацию кода. Каждый раз, когда необходимо протестировать обновленный код, выполните команду `draft up`, чтобы обновить выполняющееся приложение.

В этом примере обновите пример приложения Java, чтобы изменить отображаемый текст. Откройте файл *Hello.java*:

```console
vi src/main/java/helloworld/Hello.java
```

Измените выходной текст на *Hello World, I'm Java in AKS!*:

```java
package helloworld;

import static spark.Spark.*;

public class Hello {
    public static void main(String[] args) {
        get("/", (req, res) -> "Hello World, I'm Java in AKS!");
    }
}
```

Выполните команду `draft up` для повторного развертывания приложения:

```console
$ draft up

Draft Up Started: 'example-java': 01CMZC9RF0TZT7XPWGFCJE15X4
example-java: Building Docker Image: SUCCESS ⚓  (25.0202s)
example-java: Pushing Docker Image: SUCCESS ⚓  (7.1457s)
example-java: Releasing Application: SUCCESS ⚓  (3.5773s)
Inspect the logs with `draft logs 01CMZC9RF0TZT7XPWGFCJE15X4`
```

Чтобы просмотреть обновленное приложение, снова перейдите по IP-адресу своей подсистемы балансировки нагрузки:

```
$ curl 52.175.224.118

Hello World, I'm Java in AKS!
```

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о Draft см. в документации Draft на GitHub.

> [!div class="nextstepaction"]
> [Документация по Draft][draft-documentation]

<!-- LINKS - external -->
[draft-documentation]: https://github.com/Azure/draft/tree/master/docs
[kubernetes-service-loadbalancer]: https://kubernetes.io/docs/concepts/services-networking/service/#type-loadbalancer
[draft-repo]: https://github.com/Azure/draft

<!-- LINKS - internal -->
[acr-quickstart]: ../container-registry/container-registry-get-started-azure-cli.md
[aks-helm]: ./kubernetes-helm.md
[kubernetes-ingress]: ./ingress-basic.md
[aks-quickstart]: ./kubernetes-walkthrough.md
[az-acr-login]: /cli/azure/acr#az-acr-login
