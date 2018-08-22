---
title: Развертывание в службе Azure Kubernetes (AKS) с помощью Jenkins и сине-зеленого шаблона развертывания
description: Сведения о развертывании в службе Azure Kubernetes (AKS) с использованием Jenkins и сине-зеленого шаблона развертывания.
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
ms.openlocfilehash: 384681ae0ba212b485022ac81743528f96075ec8
ms.sourcegitcommit: d16b7d22dddef6da8b6cfdf412b1a668ab436c1f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/08/2018
ms.locfileid: "39716466"
---
# <a name="deploy-to-azure-kubernetes-service-aks-by-using-jenkins-and-the-bluegreen-deployment-pattern"></a>Развертывание в службе Azure Kubernetes (AKS) с помощью Jenkins и сине-зеленого шаблона развертывания

Служба Azure Kubernetes (AKS) управляет размещенной средой Kubernetes, позволяя быстро и легко развертывать контейнерные приложения и управлять ими. Вам не обязательно быть экспертом в оркестрации контейнеров. AKS также устраняет нагрузку на текущие операции и техническое обслуживание за счет подготовки, обновления и масштабирования ресурсов по требованию. Нет необходимости в отключении ваших приложений. Дополнительные сведения об AKS см. в разделе [Служба Azure Kubernetes (AKS)](/azure/aks/).

Сине-зеленое развертывание — это шаблон непрерывной поставки Azure DevOps, который предполагает сохранение существующей (синей) версии при развертывании новой (зеленой). Как правило, этот шаблон использует балансировку нагрузки для прямого увеличения объема трафика в зеленом развертывании. Если во время мониторинга обнаруживается ошибка, трафик перенаправляется на оставшееся синее развертывание. Дополнительные сведения о непрерывной поставке см. в статье [Что такое непрерывная поставка](/azure/devops/what-is-continuous-delivery).

Из этого руководства вы узнаете, как выполнять такие задачи:

> [!div class="checklist"]
> * Знакомство с шаблоном сине-зеленого развертывания
> * Создание управляемого кластера Kubernetes.
> * Запуск примера скрипта для настройки кластера Kubernetes
> * Настройка кластера Kubernetes вручную
> * Создание и запуск задания Jenkins

## <a name="prerequisites"></a>Предварительные требования
- [Учетная запись GitHub](https://github.com). Вам понадобится учетная запись GitHub, чтобы клонировать пример из репозитория.
- [Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest). Для создания кластера Kubernetes используйте Azure CLI 2.0.
- [Chocolatey](https://chocolatey.org). Это диспетчер пакетов, используемый для установки kubectl.
- [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/). Интерфейс командной строки, используемый для выполнения команд кластеров Kubernetes.
- [jq](https://stedolan.github.io/jq/download/). Небольшой процессор командной строки JSON.

## <a name="clone-the-sample-app-from-github"></a>Клонирование примера приложения из GitHub

В репозитории Майкрософт в GitHub вы можете найти пример приложения, демонстрирующий развертывание в AKS с помощью Jenkins и сине-зеленого шаблона. В этом разделе вы создадите вилку этого репозитория в GitHub, и клонируете приложения в свою локальную систему.

1. В репозитории GitHub найдите пример приложения [todo-app-java-on-azure](https://github.com/microsoft/todo-app-java-on-azure.git).

    ![Снимок экрана примера приложения в репозитории Майкрософт в GitHub](./media/jenkins-aks-blue-green-deployment/github-sample-msft.png)

1. Чтобы создать вилку репозитория в вашей учетной записи GitHub, выберите **Fork** (Вилка) в верхнем правом углу страницы, и следуйте инструкциям.

    ![Снимок экрана с командой создания вилки в GitHub](./media/jenkins-aks-blue-green-deployment/github-sample-msft-fork.png)

1. После ветвления репозитория можно увидеть, что имя учетной записи меняется на имя вашей учетной записи, а заметка указывает точку ветвления репозитория (Майкрософт).

    ![Снимок экрана с именем учетной записи GitHub и заметкой](./media/jenkins-aks-blue-green-deployment/github-sample-msft-forked.png)

1. Выберите **Clone or download**(Клонировать или скачать).

    ![Снимок экрана с командой клонирования или скачивания репозитория в GitHub](./media/jenkins-aks-blue-green-deployment/github-sample-clone.png)

1. В окне **Clone with HTTPS** (Клонирование с использованием HTTPS) щелкните значок **копирования**.

    ![Снимок экрана с параметром копирования URL-адреса клона в буфер обмена в GitHub](./media/jenkins-aks-blue-green-deployment/github-sample-copy.png)

1. Откройте терминал или окно Git Bash.

1. Выберите каталог, где вы хотите сохранить локальную копию (клон) репозитория.

1. С помощью команды `git clone` клонируйте URL-адрес, скопированный ранее.

    ![Снимок экрана с командой Git Bash — git clone](./media/jenkins-aks-blue-green-deployment/git-clone-command.png)

1. Нажмите клавишу ВВОД, чтобы начать процесс клонирования.

    ![Снимок экрана с результатами команды Git Bash — git clone](./media/jenkins-aks-blue-green-deployment/git-clone-results.png)

1. Перейдите в только что созданный каталог, содержащий клон исходного кода приложения.

## <a name="create-and-configure-a-managed-kubernetes-cluster"></a>Создание и настройка управляемого кластера Kubernetes

В этом разделе описывается выполнение следующих шагов:

- Использование Azure CLI 2.0 для создания управляемого кластера Kubernetes.
- Узнайте, как настроить кластер с помощью скрипта установки или вручную.
- Создайте экземпляр службы "Реестр контейнеров Azure".

> [!NOTE]   
> Сейчас служба AKS доступна в предварительной версии. Сведения о включении предварительной версии для подписки Azure см. в статье [Краткое руководство по развертыванию кластера службы Azure Kubernetes (AKS)](/azure/aks/kubernetes-walkthrough#enabling-aks-preview-for-your-azure-subscription).

### <a name="use-the-azure-cli-20-to-create-a-managed-kubernetes-cluster"></a>Использование Azure CLI 2.0 для создания управляемого кластера Kubernetes
Для создания управляемого кластера Kubernetes с [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) убедитесь, что вы используете Azure CLI версии 2.0.25 или более поздней версии.

1. Войдите в учетную запись Azure. После ввода следующей команды вы получите инструкции, в которых объясняется, как завершить вход. 
    
    ```bash
    az login
    ```

1. При выполнении команды `az login` на предыдущем шаге появляется список всех ваших подписок Azure (вместе с идентификаторами подписки). На этом шаге вы по умолчанию задаете подписку Azure. Замените местозаполнитель &lt;your-subscription-id> на нужный идентификатор подписки Azure. 

    ```bash
    az account set -s <your-subscription-id>
    ```

1. Создайте группу ресурсов. Замените местозаполнитель &lt;your-resource-group-name> именем вашей новой группы ресурсов, а &lt;your-location> — расположением. Команда `az account list-locations` отображает все расположениях Azure. Во время предварительной версии AKS доступны не все расположения. Если ввести расположение, которое является недопустимым в настоящее время, в сообщении об ошибке отобразятся доступные расположения.

    ```bash
    az group create -n <your-resource-group-name> -l <your-location>
    ```

1. Создание кластера Kubernetes. Замените &lt;your-resource-group-name> именем группы ресурсов, созданной на предыдущем шаге, а &lt;your-kubernetes-cluster-name> — именем своего нового кластера. (Процесс может занять несколько минут).

    ```bash
    az aks create -g <your-resource-group-name> -n <your-kubernetes-cluster-name> --generate-ssh-keys --node-count 2
    ```

### <a name="set-up-the-kubernetes-cluster"></a>Настройка кластера Kubernetes

Вы можете настроить сине-зеленое развертывание в AKS вручную или с помощью сценария установки, ранее предоставленного в примере клонирования. В этом разделе вы узнаете, как использовать оба этих способа.

#### <a name="set-up-the-kubernetes-cluster-via-the-sample-setup-script"></a>Настройка кластера Kubernetes с помощью примера сценария установки
1. Измените файл **deploy/aks/setup/setup.sh**, заменив следующие местозаполнители соответствующими значениями для своей среды: 

    - **&lt;название группы ресурсов>**
    - **&lt;имя кластера kubernetes>**
    - **&lt;расположение>**
    - **&lt;суффикс имени dns>**

    ![Снимок экрана со сценарием setup.sh в bash с несколькими выделенными заполнителями](./media/jenkins-aks-blue-green-deployment/edit-setup-script.png)

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

1. Обновление DNS-имени для общедоступной и тестовой конечных точек. При создании кластера Kubernetes также создается [дополнительная группа ресурсов](https://github.com/Azure/AKS/issues/3) по такому шаблону: **MC_&lt;имя_вашей_группы_ресурсов>_&lt;имя_кластера_kubernetes>_&lt;ваше_расположение>**.

    Найдите общедоступные IP-адреса в группе ресурсов.

    ![Снимок экрана с общедоступными IP-адресами в группе ресурсов](./media/jenkins-aks-blue-green-deployment/publicip.png)

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

    DNS-имя должно быть уникальным для каждой подписки. Для обеспечения уникальности имени вы можете использовать `<your-dns-name-suffix>`.

### <a name="create-an-instance-of-container-registry"></a>Создание экземпляра Реестра контейнеров

1. Чтобы создать экземпляр Реестра контейнеров, выполните команду `az acr create`. В следующем разделе можно использовать `login server` как URL-адрес реестра Docker.

    ```bash
    az acr create -n <your-registry-name> -g <your-resource-group-name>
    ```

1. Выполните команду `az acr credential`, чтобы отобразить учетные данные вашего Реестра контейнеров. Запомните имя пользователя и пароль реестра Docker, так как они вам понадобятся в следующем разделе.

    ```bash
    az acr credential show -n <your-registry-name>
    ```

## <a name="prepare-the-jenkins-server"></a>Подготовка сервера Jenkins

В этом разделе вы узнаете, как подготовить сервер Jenkins для выполнения сборки, удобной для тестирования. Тем не менее следует использовать [агент виртуальной машины Azure](https://plugins.jenkins.io/azure-vm-agents) или [агент контейнеров Azure](https://plugins.jenkins.io/azure-container-agents) для выполнения развертывания агента и запуска сборки. Дополнительные сведения см. в статье [Влияние безопасности построения на образец](https://wiki.jenkins.io/display/JENKINS/Security+implication+of+building+on+master) о Jenkins.

1. Разверните [Jenkins Master в Azure](https://aka.ms/jenkins-on-azure).

1. Подключение к серверу с помощью протокола SSH и установка средств сборки на сервере, где выполняется сборка.
   
   ```bash
   sudo apt-get install git maven 
   ```
   
1. [Установите Docker](https://docs.docker.com/install/linux/docker-ce/ubuntu/#install-docker-ce). Убедитесь, что пользователь `jenkins` имеет разрешение на выполнение команд `docker`.

1. [Установите kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/).

1. [загрузите jq](https://stedolan.github.io/jq/download/).

1. Установите jq с помощью следующей команды:

   ```bash
   sudo apt-get install jq
   ```
   
1. Установите подключаемые модули Jenkins, выполнив на панели мониторинга Jenkins следующие действия:

    1. Выберите **Управление Jenkins > Управление подключаемыми модулями > Доступно**.
    1. Выполните поиск и установите подключаемый модуль службы контейнеров Azure.

1. Добавьте учетные данные для управления ресурсами в Azure. Если у вас еще нет подключаемого модуля, установите подключаемый модуль **Учетные данные Azure**.

1. Добавьте учетные данные субъекта-службы Azure в качестве типа **субъекта-службы Microsoft Azure**.

1. Добавьте имя пользователя и пароль реестра Docker в Azure (согласно данным, полученным в разделе "Создание экземпляра Реестра контейнеров") в качестве типа **имени пользователя с паролем**.

## <a name="edit-the-jenkinsfile"></a>Изменение Jenkinsfile

1. В репозитории перейдите к `/deploy/aks/` и откройте `Jenkinsfile`.

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
    
    Обновите идентификатор учетных данных Реестра контейнеров:
    
    ```groovy
    def dockerCredentialId = '<your-acr-credential-id>'
    ```

## <a name="create-the-job"></a>Создание задания
1. Добавьте новое задание в типе **Pipeline** (Конвейер).

1. Выберите **Pipeline** > **Definition** > **Pipeline script from SCM** (Конвейер > Определение > Сценарий конвейера из SCM).

1. Введите URL-адрес репозитория SCM с &lt;вашим разветвлением репозитория>.

1. Введите следующий путь к сценарию: `deploy/aks/Jenkinsfile`.

## <a name="run-the-job"></a>Выполнение задания

1. Убедитесь, что ваш проект успешно выполнен в локальной среде. Вот инструкции [выполнения проекта на локальном компьютере](https://github.com/Microsoft/todo-app-java-on-azure/blob/master/README.md#run-it).

1. Выполните задания Jenkins При выполнении задания в первый раз Jenkins развертывает приложение списка задач в синей среде, которая является неактивной по умолчанию. 

1. Чтобы убедиться, что задание выполнено, перейдите по URL-адресам:
    - Общедоступная конечная точка: `http://aks-todoapp<your-dns-name-suffix>.<your-location>.cloudapp.azure.com`
    - Синяя конечная точка: `http://aks-todoapp-blue<your-dns-name-suffix>.<your-location>.cloudapp.azure.com`
    - Зеленая конечная точка: `http://aks-todoapp-green<your-dns-name-suffix>.<your-location>.cloudapp.azure.com`

Общедоступная и синяя тестовая конечные точки имеют одно и то же обновление, а зеленая конечная точка по умолчанию показывает изображение tomcat. 

Если сборка выполняется более одного раза, она циклически проходит через сине-зеленое развертывание. Другими словами, если текущая среда синяя, задание развертывает и проверяет зеленую среду. Затем, если при тестировании не обнаружено ошибок, задание обновляет общедоступную конечную точку приложения для направления трафика в зеленую среду.

## <a name="additional-information"></a>Дополнительная информация

Дополнительные сведения о развертывании без простоя см. в [шаблоне быстрого запуска](https://github.com/Azure/azure-quickstart-templates/tree/master/301-jenkins-aks-zero-downtime-deployment). 

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вам больше не нужны ресурсы, созданные для этого руководства, вы можете удалить их.

```bash
az group delete -y --no-wait -n <your-resource-group-name>
```

## <a name="troubleshooting"></a>Устранение неполадок

Если вы столкнулись с ошибками, которые касаются подключаемых модулей Jenkins, сообщите о проблеме конкретного компонента в [JENKS JIRA](https://issues.jenkins-ci.org/).

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве вы узнали, как развертывать AKS с использованием Jenkins и сине-зеленого шаблона развертывания. Дополнительные сведения о поставщике Jenkins в Azure, см. в разделе Jenkins на сайте Azure.

> [!div class="nextstepaction"]
> [Jenkins в Azure](/azure/jenkins/)