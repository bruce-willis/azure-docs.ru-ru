---
title: Ввод в эксплуатацию служб машинного обучения в Azure HDInsight
description: Сведения о вводе в эксплуатацию служб машинного обучения в Azure HDInsight.
services: hdinsight
ms.service: hdinsight
author: jasonwhowell
ms.author: jasonh
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/27/2018
ms.openlocfilehash: 0a275405530c60c5e2f08a2af120382f81c2b6f8
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/27/2018
ms.locfileid: "43041377"
---
# <a name="operationalize-ml-services-cluster-on-azure-hdinsight"></a>Ввод в эксплуатацию кластера служб машинного обучения в Azure HDInsight

После того как вы выполните моделирование данных на кластере служб машинного обучения в HDInsight, полученную модель можно ввести в эксплуатацию для предоставления прогнозов. В этой статье описано выполнение этой задачи.

## <a name="prerequisites"></a>Предварительные требования

* **Кластер служб машинного обучения в HDInsight**. Инструкции см. в статье [Начало работы со службами машинного обучения в HDInsight](r-server-get-started.md).

* **Клиент Secure Shell (SSH)**. Клиент SSH используется для удаленного подключения к кластеру HDInsight и выполнения команд непосредственно в кластере. Дополнительные сведения см. в статье [Использование SSH с Hadoop на основе Linux в HDInsight из Linux, Unix или OS X](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="operationalize-ml-services-cluster-with-one-box-configuration"></a>Ввод в эксплуатацию кластера служб машинного обучения в универсальной конфигурации

> [!NOTE]
> Приведенные ниже действия применимы к R Server 9.0 и Machine Learning Server (ML Server) 9.1. Действия для ML Server 9.3 см. в статье [Launch the administration tool/CLI to manage the operationalization configuration](https://docs.microsoft.com/machine-learning-server/operationalize/configure-admin-cli-launch) (Запуск средства администрирования или CLI для управления конфигурацией ввода в эксплуатацию).

1. Подключитесь к граничному узлу по протоколу SSH.

        ssh USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

    Инструкции по использованию SSH с Azure HDInsight см. в статье [Подключение к HDInsight (Hadoop) с помощью SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

1. Перейдите в каталог для используемой версии и запустите DLL-файл для .NET с помощью команды sudo. 

    - Для Microsoft ML Server 9.1:

            cd /usr/lib64/microsoft-r/rserver/o16n/9.1.0
            sudo dotnet Microsoft.RServer.Utils.AdminUtil/Microsoft.RServer.Utils.AdminUtil.dll

    - Для Microsoft R Server 9.0:

            cd /usr/lib64/microsoft-deployr/9.0.1
            sudo dotnet Microsoft.DeployR.Utils.AdminUtil/Microsoft.DeployR.Utils.AdminUtil.dll

1. У вас есть несколько вариантов на выбор. Выберите первый вариант, как показано на следующем снимке экрана, чтобы **настроить ML Server для ввода в эксплуатацию**.

    ![Универсальная конфигурация](./media/r-server-operationalize/admin-util-one-box-1.png)

1. Теперь вам предлагаются доступные методы для ввода в эксплуатацию ML Server. Выберите первый из представленных вариантов, введя символ **A**.

    ![Универсальная конфигурация](./media/r-server-operationalize/admin-util-one-box-2.png)

1. В ответ на соответствующий запрос дважды введите пароль пользователя с правами локального администратора.

1. Выходные данные сообщат, что операция выполнена успешно. Также вам будет предложено еще одно меню для выбора параметров. Выберите E, чтобы вернуться к главному меню.

    ![Универсальная конфигурация](./media/r-server-operationalize/admin-util-one-box-3.png)

1. По желанию вы можете запустить диагностический тест, как показано ниже.

    a. В главном меню выберите **6**, чтобы выполнить диагностические тесты.

    ![Универсальная конфигурация](./media/r-server-operationalize/diagnostic-1.png)

    b. В меню диагностических тестов выберите вариант **A**. Когда появится запрос, введите пароль пользователя с правами локального администратора.

    ![Универсальная конфигурация](./media/r-server-operationalize/diagnostic-2.png)

    c. Убедитесь, что выходные данные подтверждают работоспособность системы.

    ![Универсальная конфигурация](./media/r-server-operationalize/diagnostic-3.png)

    d. Из предложенного меню выберите вариант **E**, чтобы вернуться к главному меню, а затем введите **8** для выхода из служебной программы администрирования.

### <a name="long-delays-when-consuming-web-service-on-spark"></a>Длительные задержки при использовании веб-службы на Spark

Если при попытке использовать веб-службы, созданные с использованием функций mrsdeploy, в вычисленном контексте Spark возникают значительные задержки, может потребоваться добавить некоторые отсутствующие папки. Приложение Spark принадлежит пользователю с именем *rserve2* при вызове из веб-службы с помощью функций mrsdeploy. Для обхода этой проблемы:

    # Create these required folders for user 'rserve2' in local and hdfs:

    hadoop fs -mkdir /user/RevoShare/rserve2
    hadoop fs -chmod 777 /user/RevoShare/rserve2

    mkdir /var/RevoShare/rserve2
    chmod 777 /var/RevoShare/rserve2


    # Next, create a new Spark compute context:
 
    rxSparkConnect(reset = TRUE)


Итак, вы завершили настройку для практического использования. Теперь с помощью пакета `mrsdeploy` на RClient вы можете подключаться к введенному в эксплуатацию решению на граничном узле и применять разные функции, например [удаленное выполнение](https://docs.microsoft.com/machine-learning-server/r/how-to-execute-code-remotely) и [веб-службы](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services). В зависимости от того, настроен ли кластер в виртуальной сети, может потребоваться настроить туннелирование с перенаправлением портов через сеанс SSH. В следующих разделах описано, как настроить этот туннель.

### <a name="ml-services-cluster-on-virtual-network"></a>Кластер служб машинного обучения в виртуальной сети

Убедитесь, что трафик через порт 12800 направляется на граничный узел. Это позволит использовать граничный узел для подключения к средству операционализации.


    library(mrsdeploy)

    remoteLogin(
        deployr_endpoint = "http://[your-cluster-name]-ed-ssh.azurehdinsight.net:12800",
        username = "admin",
        password = "xxxxxxx"
    )


Если `remoteLogin()` не может подключиться к граничному узлу, но вам удается подключиться к нему по протоколу SSH, проверьте наличие и правильность настройки правила, разрешающего трафик через порт 12800. Если и после этого проблема не исчезнет, в качестве решения можно настроить туннелирование с перенаправлением портов через SSH. Инструкции см. в следующем разделе.

### <a name="ml-services-cluster-not-set-up-on-virtual-network"></a>Кластер служб машинного обучения не настроен в виртуальной сети

Если ваш кластер не настроен в виртуальной сети или с подключением через виртуальную сеть возникают проблемы, можно использовать туннелирование с перенаправлением портов через SSH:

    ssh -L localhost:12800:localhost:12800 USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

Теперь при открытии сеанса SSH трафик от порта 12800 на локальном компьютере будет передаваться на порт 12800 граничного узла через сеанс SSH. Убедитесь, что вы используете `127.0.0.1:12800` в методе `remoteLogin()`. Это позволит входить в средство операционализации на граничном узле при использовании перенаправления портов.


    library(mrsdeploy)

    remoteLogin(
        deployr_endpoint = "http://127.0.0.1:12800",
        username = "admin",
        password = "xxxxxxx"
    )


## <a name="scale-operationalized-compute-nodes-on-hdinsight-worker-nodes"></a>Масштабирование вычислительных узлов, введенных в эксплуатацию на рабочих узлах HDInsight

Чтобы масштабировать вычислительные узлы, сначала нужно вывести рабочие узлы из эксплуатации, а затем настроить вычислительные узлы на выведенных из эксплуатации рабочих узлах.

### <a name="step-1-decommission-the-worker-nodes"></a>Шаг 1. Вывод рабочих узлов из эксплуатации

Управление кластером служб машинного обучения не осуществляется через YARN. Если вы не выведете из эксплуатации рабочие узлы, диспетчер ресурсов YARN будет действовать неправильно из-за отсутствия информации об использовании ресурсов сервером. Чтобы этого избежать, мы рекомендуем вывести из эксплуатации рабочие узлы, прежде чем масштабировать вычислительные узлы.

Выполните следующие действия, чтобы вывести рабочие узлы из эксплуатации.

1. Войдите в консоль Ambari для кластера и щелкните вкладку **Hosts** (Узлы).

1. Выберите рабочие узлы, которые вы намерены вывести из эксплуатации.

1. Последовательно выберите **Actions** (Действия)  > **Selected Hosts** (Выбранные узлы)  > **Hosts** (Узлы)  > **Turn ON Maintenance Mode** (Включить режим обслуживания). Например, на следующем рисунке для вывода из эксплуатации выбраны узлы wn3 и wn4.  

   ![Вывод рабочих узлов из эксплуатации](./media/r-server-operationalize/get-started-operationalization.png)  

* Выберите **Actions** (Действия) > **Selected Hosts** (Выбранные узлы) > **DataNodes** (Узлы данных) и щелкните **Decommission** (Вывести из эксплуатации).
* Выберите **Actions** (Действия) > **Selected Hosts** (Выбранные узлы) > **NodeManagers** (Диспетчеры узлов) и щелкните **Decommission** (Вывести из эксплуатации).
* Выберите **Actions** (Действия) > **Selected Hosts** (Выбранные узлы) > **DataNodes** (Узлы данных) и щелкните **Stop** (Остановить).
* Выберите **Actions** (Действия) > **Selected Hosts** (Выбранные узлы) > **NodeManagers** (Диспетчеры узлов) и щелкните **Stop** (Остановить).
* Выберите **Actions** (Действия) > **Selected Hosts** (Выбранные узлы) > **Hosts** (Узлы) и щелкните **Stop All Components** (Остановить все компоненты).
* Отмените выбор рабочих узлов и выберите головные узлы.
* Выберите **Actions** (Действия) > **Selected Hosts** (Выбранные узлы) > **Hosts** (Узлы) > **Restart All Components** (Перезапустить все компоненты).

### <a name="step-2-configure-compute-nodes-on-each-decommissioned-worker-nodes"></a>Шаг 2. Настройка вычислительных узлов на каждом из рабочих узлов, выведенных из эксплуатации

1. Поочередно откройте сеанс SSH для каждого рабочего узла, выведенного из эксплуатации.

1. Запустите служебную программу администрирования через подходящую библиотеку DLL для используемого кластера служб машинного обучения. В ML Server версии 9.1 выполните следующий код:

        dotnet /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Utils.AdminUtil/Microsoft.DeployR.Utils.AdminUtil.dll

1. Введите **1**, чтобы выбрать параметр **Configure ML Server for Operationalization** (Настройка ML Server для ввода в эксплуатацию).

1. Введите **C**, чтобы выбрать пункт `C. Compute node`. Это настраивает вычислительный узел на рабочем узле.

1. Выйдите из служебной программы администрирования.

### <a name="step-3-add-compute-nodes-details-on-web-node"></a>Шаг 3. Добавление сведений о вычислительных узлах на веб-узел

Когда вы настроите функции вычислительных узлов на всех рабочих узлах, выведенных из эксплуатации, вернитесь на граничный узел и добавьте IP-адреса этих рабочих узлов в конфигурацию веб-узла ML Server.

1. Подключитесь к граничному узлу по протоколу SSH.

1. Запустите `vi /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Server.WebAPI/appsettings.json`.

1. Найдите раздел URI и добавьте в него информацию об IP-адресах и портах рабочих узлов.

       "Uris": {
         "Description": "Update 'Values' section to point to your backend machines. Using HTTPS is highly recommended",
         "Values": [
           "http://localhost:12805", "http://[worker-node1-ip]:12805", "http://[workder-node2-ip]:12805"
         ]
       }

## <a name="next-steps"></a>Дополнительная информация

* [Управление кластером служб машинного обучения в HDInsight](r-server-hdinsight-manage.md)
* [Варианты контекста вычислений для кластера служб машинного обучения в HDInsight](r-server-compute-contexts.md)
* [Решения службы хранилища Azure для служб машинного обучения в Azure HDInsight](r-server-storage.md)
