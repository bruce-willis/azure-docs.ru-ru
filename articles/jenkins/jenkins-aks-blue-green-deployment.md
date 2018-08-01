---
title: Развертывание в службе Azure Kubernetes (AKS) с использованием Jenkins и сине-зеленого шаблона развертывания
description: Дополнительные сведения о развертывании в службе Azure Kubernetes (AKS) с использованием Jenkins и сине-зеленого шаблона развертывания
services: app-service\web
documentationcenter: ''
author: tomarcher
manager: jpconnock
editor: ''
ms.assetid: ''
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 07/23/2018
ms.author: tarcher
ms.custom: jenkins
ms.openlocfilehash: 472622f78303593b7a4d5d5136aa47f34a1f44b1
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/24/2018
ms.locfileid: "39227931"
---
# <a name="deploy-to-azure-kubernetes-service-aks-using-jenkins-and-bluegreen-deployment-pattern"></a>Развертывание в службе Azure Kubernetes (AKS) с использованием Jenkins и сине-зеленого шаблона развертывания

Служба Azure Kubernetes (AKS) управляет размещенной средой Kubernetes, позволяя быстро и легко развертывать контейнерные приложения и управлять ими, даже если вы никогда не оркестрировали контейнеры. AKS также устраняет нагрузку на текущие операции и техническое обслуживание за счет подготовки, обновления и масштабирования ресурсов по требованию без отключения ваших приложений. Дополнительные сведения о AKS, см. в разделе [Служба Azure Kubernetes (AKS)](/azure/aks/).

Сине-зеленое развертывание — это шаблон непрерывной поставки DevOps, который предполагает сохранение существующей (синей) версии, при развертывании новой (зеленой). Как правило, этот шаблон использует балансировку нагрузки для прямого увеличения объема трафика в зеленом развертывании. Если во время мониторинга обнаруживается ошибка, трафик перенаправляется на оставшееся синее развертывание. Дополнительные сведения о непрерывной поставке см. в статье [What is Continuous Delivery](/azure/devops/what-is-continuous-delivery) (Что такое непрерывная поставка).

В этом руководстве вы узнаете, как выполнять следующие задачи при развертывании AKS с помощью Jenkins и сине-зеленого шаблона развертывания.

> [!div class="checklist"]
> * Знакомство с шаблоном сине-зеленого развертывания
> * Создание управляемого кластера Kubernetes
> * Запуск примера скрипта для настройки кластера Kubernetes
> * Настройка кластера Kubernetes вручную
> * Создание и запуск задания Jenkins

## <a name="prerequisites"></a>Предварительные требования
- [Учетная запись GitHub](https://github.com). Вам понадобится учетная запись GitHub, чтобы клонировать пример из репозитория.
- [Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest). Для создания кластера Kubernetes используется Azure CLI 2.0.
- [Chocolatey](https://chocolatey.org). Это диспетчер пакетов, используемый для установки kubectl.
- [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/). Интерфейс командной строки для выполнения команд кластеров Kubernetes.
- [jq](https://stedolan.github.io/jq/download/). Небольшой процессор командной строки JSON.

## <a name="clone-the-sample-app-from-github"></a>Клонирование примера приложения из GitHub

Пример приложения, демонстрирующий развертывание в AKS с помощью Jenkins и сине-зеленого шаблона, который находится в репозитории Майкрософт в GitHub. В этом разделе вы создадите вилку этого репозитория в GitHub, и клонируете приложения в свою локальную систему.

1. В репозитории GitHub найдите пример приложения [todo-app-java-on-azure](https://github.com/microsoft/todo-app-java-on-azure.git).

    ![Пример приложения в репозитории Майкрософт в GitHub.](./media/jenkins-aks-blue-green-deployment/github-sample-msft.png)

1. Чтобы создать вилку репозитория в вашей учетной записи GitHub, выберите **Fork** (Вилка) в верхнем правом углу страницы, и следуйте инструкциям.

    ![Создание разветвление примера приложения в учетной записи GitHub.](./media/jenkins-aks-blue-green-deployment/github-sample-msft-fork.png)

1. После ветвления репозитория можно увидеть, что имя учетной записи меняется на имя вашей учетной записи, а заметка указывает точку ветвления репозитория (Майкрософт).

    ![Пример приложения после ветвления в другую учетную запись GitHub.](./media/jenkins-aks-blue-green-deployment/github-sample-msft-forked.png)

1. Выберите команду **Clone or download** (Клонировать или загрузить).

    ![GitHub позволяет быстро клонировать или загрузить репозиторий.](./media/jenkins-aks-blue-green-deployment/github-sample-clone.png)

1. В окне **Clone with HTTPS** (Клонирование с использованием HTTPS) щелкните значок копирования.

    ![Скопируйте URL-адрес клона в буфер обмена.](./media/jenkins-aks-blue-green-deployment/github-sample-copy.png)

1. Откройте терминал или окно Bash.

1. Выберите каталог, где вы хотите сохранить локальную копию (клон) репозитория.

1. С помощью команды `git clone` клонируйте URL-адрес, скопированный ранее.

    ![Введите "git clone" и URL-адрес клона, чтобы создать клон репозитория.](./media/jenkins-aks-blue-green-deployment/git-clone-command.png)

1. Выберите ключ &lt;Ввод>, чтобы начать процесс клонирования.

    ![С помощью команды "git clone" создается личная копия репозитория, которую можно протестировать](./media/jenkins-aks-blue-green-deployment/git-clone-results.png)

1. Перейдите в только что созданный каталог, содержащий клон исходного кода приложения.

## <a name="create-and-configure-a-managed-kubernetes-cluster"></a>Создание и настройка управляемого кластера Kubernetes

В этом разделе описывается выполнение следующих шагов:

- Использование Azure CLI 2.0 для создания управляемого кластера Kubernetes.
- Узнайте, как настроить кластер с помощью скрипта установки или вручную.
- Создание Реестра контейнеров Azure.

> [!NOTE]   
> Сейчас служба AKS доступна в предварительной версии. Дополнительный сведения о включении предварительной версии для подписки Azure см. в разделе [Quickstart: Deploy an Azure Kubernetes Service (AKS) cluster ](/azure/aks/kubernetes-walkthrough#enabling-aks-preview-for-your-azure-subscription) (Краткое руководство по развертыванию кластера службы Azure Kubernetes (AKS)).

### <a name="use-the-azure-cli-20-to-create-a-managed-kubernetes-cluster"></a>Использование Azure CLI 2.0 для создания управляемого кластера Kubernetes
Для создания управляемого кластера Kubernetes с [Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest) убедитесь, что вы используете Azure CLI версии 2.0.25 или более поздней версии.

1. Войдите в учетную запись Azure. После ввода команды `az login` вам предоставляются инструкции, в которых объясняется, как завершить вход. 
    
    ```bash
    az login
    ```

1. При выполнении команды `az login` на предыдущем шаге отображается список всех ваших подписок Azure (вместе с идентификаторами подписки). На этом шаге вы по умолчанию задаете подписку Azure. Замените местозаполнитель &lt;your-subscription-id> на нужный идентификатор подписки Azure. 

    ```bash
    az account set -s <your-subscription-id>
    ```

1. Создайте группу ресурсов. Замените местозаполнитель &lt;your-resource-group-name> именем вашей новой группы ресурсов, а &lt;your-location> — расположением. Команда `az account list-locations` отображает все расположениях Azure. Во время предварительной версии AKS доступны не все расположения. Если ввести расположение, которое является недопустимым в настоящее время, в сообщении об ошибке будут отображаться доступные расположения.

    ```bash
    az group create -n <your-resource-group-name> -l <your-location>
    ```

1. Создание кластера Kubernetes. Замените &lt;your-resource-group-name> именем группы ресурсов, созданной на предыдущем шаге, а &lt;you-kubernetes-cluster-name> замените именем своего нового кластера. (Процесс может занять несколько минут).

    ```bash
    az aks create -g <your-resource-group-name> -n <your-kubernetes-cluster-name> --generate-ssh-keys --node-count 2
    ```

### <a name="set-up-the-kubernetes-cluster"></a>Настройка кластера Kubernetes

Настройку сине-зеленого развертывания в AKS можно выполнить либо с помощью сценария установки, ранее предоставленного в примере клонирования, либо вручную. В этом разделе вы узнаете, как использовать оба этих способа.

#### <a name="set-up-the-kubernetes-cluster-via-the-sample-setup-script"></a>Настройка кластера Kubernetes с помощью примера сценария установки
1. Измените файл **deploy/aks/setup/setup.sh**, заменив следующие местозаполнители соответствующими значениями для своей среды: 

    - **&lt;название группы ресурсов>**
    - **&lt;имя кластера kubernetes>**
    - **&lt;расположение>**
    - **&lt;суффикс имени dns>**

    ![Сценарий setup.sh содержит несколько местозаполнителей, которые можно изменить для вашей среды.](./media/jenkins-aks-blue-green-deployment/edit-setup-script.png)

1. Выполните сценарий установки.

    ```bash
    sh setup.sh
    ```

#### <a name="set-up-a-kubernetes-cluster-manually"></a>Настройка кластера Kubernetes вручную 
1. Загрузите конфигурацию Kubernetes в папку профиля.

    ```bash
    az aks get-credentials -g <your-resource-group-name> -n <your-kubernetes-cluster-name> --admin
    ```

1. Перейдите в каталог **deploy/aks/setup**. 

1. Выполните команду **kubectl**, чтобы настроить службы для общедоступной конечной точки и двух тестовых конечных точек.

    ```bash
    kubectl apply -f  service-green.yml
    kubectl apply -f  test-endpoint-blue.yml
    kubectl apply -f  test-endpoint-green.yml
    ```

1. Обновление DNS-имени для общедоступной и тестовой конечных точек. При создании кластера Kubernetes создается [дополнительная группа ресурсов](https://github.com/Azure/AKS/issues/3) по такому шаблону: **MC_&lt;your-resource-group-name>_&lt;your-kubernetes-cluster-name>_&lt;your-location>**.

    Поиск общедоступного IP-адреса в группе ресурсов

    ![Общедоступный IP-адрес в группе ресурсов](./media/jenkins-aks-blue-green-deployment/publicip.png)

    Для каждой из служб найдите внешний IP-адрес, выполнив следующую команду:
    
    ```bash
    kubectl get service todoapp-service
    ```
    
    Обновите DNS-имя для соответствующих IP-адресов с помощью следующей команды:

    ```bash
    az network public-ip update --dns-name aks-todoapp --ids /subscriptions/<your-subscription-id>/resourceGroups/MC_<resourcegroup>_<aks>_<location>/providers/Microsoft.Network/publicIPAddresses/kubernetes-<ip-address>
    ```

    Повторите вызов для `todoapp-test-blue` и `todoapp-test-green`:

    ```bash
    az network public-ip update --dns-name todoapp-blue --ids /subscriptions/<your-subscription-id>/resourceGroups/MC_<resourcegroup>_<aks>_<location>/providers/Microsoft.Network/publicIPAddresses/kubernetes-<ip-address>

    az network public-ip update --dns-name todoapp-green --ids /subscriptions/<your-subscription-id>/resourceGroups/MC_<resourcegroup>_<aks>_<location>/providers/Microsoft.Network/publicIPAddresses/kubernetes-<ip-address>
    ```

    DNS-имя должно быть уникальным для каждой подписки. Команду `<your-dns-name-suffix>` можно использовать для обеспечения уникальности имени.

### <a name="create-azure-container-registry"></a>Создание Реестра контейнеров Azure

1. Чтобы создать Реестр контейнеров Azure, выполните команду `az acr create`. После создания Реестра контейнеров Azure, используйте `login server` в качестве URL-адреса реестра Docker в следующем разделе.

    ```bash
    az acr create -n <your-registry-name> -g <your-resource-group-name>
    ```

1. Выполните команду `az acr credential`, чтобы отобразить учетные данные вашего Реестра контейнеров Azure. Запомните имя пользователя и пароль реестра Docker, так как они понадобятся в следующем разделе.

    ```bash
    az acr credential show -n <your-registry-name>
    ```

## <a name="prepare-the-jenkins-server"></a>Подготовка сервера Jenkins

В этом разделе вы узнаете, как подготовить сервер Jenkins для выполнения сборки, удобной для тестирования. В статье [Security implications of building on master](https://wiki.jenkins.io/display/JENKINS/Security+implication+of+building+on+master) (Влияние безопасности построения на образец) о Jenkins рекомендуется использовать [Агент виртуальной машины Azure](https://plugins.jenkins.io/azure-vm-agents) или [Агент контейнера Azure](https://plugins.jenkins.io/azure-container-agents) для выполнения развертывания агента и запуска сборки. 

1. Разверните [Jenkins Master в Azure](https://aka.ms/jenkins-on-azure).

1. Подключение к серверу с помощью протокола SSH и установка средств сборки на сервере, где выполняется сборка.
   
   ```bash
   sudo apt-get install git maven 
   ```
   
1. [Установите Docker](https://docs.docker.com/install/linux/docker-ce/ubuntu/#install-docker-ce). Убедитесь, что пользователь `jenkins` имеет разрешение на выполнение команды `docker`.

1. [Установите kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/).

1. [загрузите jq](https://stedolan.github.io/jq/download/).

1. Установите jq с помощью следующей команды:

   ```bash
   sudo apt-get install jq
   ```
   
1. Установите подключаемые модули Jenkins, выполнив на панели мониторинга Jenkins следующие действия:

    1. Выберите **Управление Jenkins > Управление подключаемыми модулями > Доступно**.
    1. Выполните поиск и установите подключаемый модуль службы контейнеров Azure.

1. Необходимо добавить учетные данные, которые будут использоваться для управления ресурсами в Azure. Если у вас еще нет подключаемого модуля, установите подключаемый модуль **Учетные данные Azure**.

1. Добавьте учетные данные субъекта-службы Azure в качестве типа или вида **Субъекта-службы Microsoft Azure**.

1. Добавьте имя пользователя и пароль реестра Docker в Azure (согласно данным, полученным в разделе **Создание Реестра контейнеров Azure**) в качестве типа или вида **Имени пользователя с паролем**.

## <a name="edit-the-jenkinsfile"></a>Изменение Jenkinsfile

1. В репозитории перейдите к `/deploy/aks/` и откройте `Jenkinsfile`

2. Обновите файл следующим образом:

    ```groovy
    def servicePrincipalId = '<your-service-principal>'
    def resourceGroup = '<your-resource-group-name>'
    def aks = '<your-kubernetes-cluster-name>'

    def cosmosResourceGroup = '<your-cosmodb-resource-group>'
    def cosmosDbName = '<your-cosmodb-name>'
    def dbName = '<your-dbname>'

    def dockerRegistry = '<your-acr-name>.azurecr.io'
    ```
    
    И обновите идентификатор учетных данных записи контроля доступа:
    
    ```groovy
    def dockerCredentialId = '<your-acr-credential-id>'
    ```

## <a name="create-the-job"></a>Создание задания
1. Добавьте новое задание в типе **Pipeline** (Конвейер).

1. Выберите **Pipeline > Definition > Pipeline script from SCM** (Конвейер > Определение > Сценарий конвейера из SCM).

1. Введите URL-адрес репозитория SCM с &lt;your-forked-repo>

1. Введите следующий путь к сценарию: `deploy/aks/Jenkinsfile`.

## <a name="run-the-job"></a>Выполнение задания

1. Убедитесь, что ваш проект успешно выполнен в локальной среде. [Выполнение проекта на локальном компьютере](https://github.com/Microsoft/todo-app-java-on-azure/blob/master/README.md#run-it)

1. Выполните задания Jenkins При выполнении задания Jenkins в первый раз, Jenkins развернет приложение todo в Синей среде, которая является неактивной по умолчанию. 

1. Чтобы убедиться, что задание выполнено, перейдите на URL-адрес:
    - Общедоступная конечная точка: `http://aks-todoapp<your-dns-name-suffix>.<your-location>.cloudapp.azure.com`
    - Синяя конечная точка: `http://aks-todoapp-blue<your-dns-name-suffix>.<your-location>.cloudapp.azure.com`
    - Зеленая конечная точка: `http://aks-todoapp-green<your-dns-name-suffix>.<your-location>.cloudapp.azure.com`

Общедоступная и синяя тестовая конечные точки имеют одно и тоже обновление, а зеленая конечная точка по умолчанию показывает изображение tomcat. 

Если сборка выполняется более одного раза, она циклически проходит через сине-зеленое развертывание. Другими словами, при текущей синей среде задание будет развертываться и тестироваться в зеленой среде, и обновлять общедоступную конечную точку приложения для направления трафика в зеленую среду, если во время тестировании не обнаружено ошибок.

## <a name="additional-information"></a>Дополнительная информация

За дополнительными сведениями о развертывании без простоя, см. [quickstart template](https://github.com/Azure/azure-quickstart-templates/tree/master/301-jenkins-aks-zero-downtime-deployment) (шаблон быстрого запуска). 

## <a name="clean-up-resources"></a>Очистка ресурсов

Удалите все ресурсы Azure, создание в этом руководстве, если они вам больше не нужны.

```bash
az group delete -y --no-wait -n <your-resource-group-name>
```

## <a name="troubleshooting"></a>Устранение неполадок

Если вы столкнулись с ошибками, которые касаются подключаемых модулей Jenkins, сообщите о проблеме конкретного компонента в [JENKS JIRA](https://issues.jenkins-ci.org/).

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве вы узнали, как развертывать службу Azure Kubernetes (AKS) с помощью Jenkins и сине-зеленого шаблона развертывания. Дополнительные сведения о поставщике Jenkins в Azure, см. в разделе Jenkins на сайте Azure.

> [!div class="nextstepaction"]
> [Jenkins в Azure](/azure/jenkins/)