---
title: Рабочие процессы Hadoop Oozie в присоединенных к домену кластерах HDInsight
description: Используйте Hadoop Oozie в присоединенном к домену корпоративном пакете безопасности HDInsight на базе Linux. Узнайте, как определить рабочий процесс и отправить задание для Oozie.
services: hdinsight
author: omidm1
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: d7603471-5076-43d1-8b9a-dbc4e366ce5d
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/26/2018
ms.author: omidm
ms.openlocfilehash: 928f6adbb348683a110f7da9b20efaae998290ca
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38972219"
---
#<a name="run-apache-oozie-in-domain-joined-hdinsight-hadoop-clusters"></a>Запуск Apache Oozie в присоединенных к домену кластерах HDInsight Hadoop
Oozie — это система рабочих процессов и координации, управляющая заданиями Hadoop. Служба Oozie интегрирована со стеком Hadoop и поддерживает следующие задания:
- Apache MapReduce
- Apache Pig
- Apache Hive
- Apache Sqoop

Вы также можете использовать Oozie для планирования системных заданий, например Java-программ и сценариев оболочки.

##<a name="prerequisites"></a>Предварительные требования:
- Присоединенный к домену кластер HDInsight Hadoop. См. статью [Настройка присоединенных к домену кластеров HDInsight](./apache-domain-joined-configure-using-azure-adds.md)

    > [!NOTE]
    > Подробные инструкции по использованию Oozie в не присоединенных к домену кластерах см. [здесь](../hdinsight-use-oozie-linux-mac.md)

##<a name="connecting-to-domain-joined-cluster"></a>Подключение к присоединенному к домену кластеру
Дополнительные сведения об SSH см. в разделе [Проверка подлинности: присоединенный к домену кластер HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).
- Подключитесь к кластеру HDInsight с помощью протокола SSH:
     ```bash
    ssh [DomainUserName]@<clustername>-ssh.azurehdinsight.net
    ```
Чтобы проверить успешность проверки подлинности Kerberos, используйте команду `klist`. Если она не пройдена, запустите проверку подлинности Kerberos с помощью команды `kinit`.

- Войдите в шлюз HDInsight, чтобы зарегистрировать токен oAuth, необходимый для доступа к Azure Data Lake Store (ADLS)
     ```bash
     curl -I -u [DomainUserName@Domain.com]:[DomainUserPassword] https://<clustername>.azurehdinsight.net
     ```

    Код отклика состояния _200 ОК_ указывает на успешную регистрацию. Проверьте имя пользователя и пароль, если получен отклик о неуспешной авторизации (401).

## <a name="define-the-workflow"></a>Определение рабочего процесса
Определения рабочего процесса Oozie записываются на языке определения процессов Hadoop (hPDL), который является языком определения процессов XML. Для определения рабочего процесса выполните следующие действия.

-   Настройка рабочей области пользователя домена:
 ```bash
hdfs dfs -mkdir /user/<DomainUser>
cd /home/<DomainUserPath>
cp /usr/hdp/<ClusterVersion>/oozie/doc/oozie-examples.tar.gz .
tar -xvf oozie-examples.tar.gz
hdfs dfs -put examples /user/<DomainUser>/
 ```
Замените `DomainUser` именем пользователя домена. Замените `DomainUserPath` путем к домашнему каталогу для пользователя домена. Замените `ClusterVersion` вашей версией кластера HDP.

-   Для создания и открытия файла выполните следующий запрос:
 ```bash
nano workflow.xml
 ```

- Открыв редактор nano, введите следующий код XML в качестве содержимого файла:
 ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <workflow-app xmlns="uri:oozie:workflow:0.4" name="map-reduce-wf">
       <credentials>
          <credential name="metastore_token" type="hcat">
             <property>
                <name>hcat.metastore.uri</name>
                <value>thrift://hn0-<clustername>.<Domain>.com:9083</value>
             </property>
             <property>
                <name>hcat.metastore.principal</name>
                <value>hive/_HOST@<Domain>.COM</value>
             </property>
          </credential>
          <credential name="hs2-creds" type="hive2">
             <property>
                <name>hive2.server.principal</name>
                <value>${jdbcPrincipal}</value>
             </property>
             <property>
                <name>hive2.jdbc.url</name>
                <value>${jdbcURL}</value>
             </property>
          </credential>
       </credentials>
       <start to="mr-test" />
       <action name="mr-test">
          <map-reduce>
             <job-tracker>${jobTracker}</job-tracker>
             <name-node>${nameNode}</name-node>
             <prepare>
                <delete path="${nameNode}/user/${wf:user()}/examples/output-data/mrresult" />
             </prepare>
             <configuration>
                <property>
                   <name>mapred.job.queue.name</name>
                   <value>${queueName}</value>
                </property>
                <property>
                   <name>mapred.mapper.class</name>
                   <value>org.apache.oozie.example.SampleMapper</value>
                </property>
                <property>
                   <name>mapred.reducer.class</name>
                   <value>org.apache.oozie.example.SampleReducer</value>
                </property>
                <property>
                   <name>mapred.map.tasks</name>
                   <value>1</value>
                </property>
                <property>
                   <name>mapred.input.dir</name>
                   <value>/user/${wf:user()}/${examplesRoot}/input-data/text</value>
                </property>
                <property>
                   <name>mapred.output.dir</name>
                   <value>/user/${wf:user()}/${examplesRoot}/output-data/mrresult</value>
                </property>
             </configuration>
          </map-reduce>
          <ok to="myHive2" />
          <error to="fail" />
       </action>
       <action name="myHive2" cred="hs2-creds">
          <hive2 xmlns="uri:oozie:hive2-action:0.2">
             <job-tracker>${jobTracker}</job-tracker>
             <name-node>${nameNode}</name-node>
             <jdbc-url>${jdbcURL}</jdbc-url>
             <script>${hiveScript2}</script>
             <param>hiveOutputDirectory2=${hiveOutputDirectory2}</param>
          </hive2>
          <ok to="myHive" />
          <error to="fail" />
       </action>
       <action name="myHive" cred="metastore_token">
          <hive xmlns="uri:oozie:hive-action:0.2">
             <job-tracker>${jobTracker}</job-tracker>
             <name-node>${nameNode}</name-node>
             <configuration>
                <property>
                   <name>mapred.job.queue.name</name>
                   <value>${queueName}</value>
                </property>
             </configuration>
             <script>${hiveScript1}</script>
             <param>hiveOutputDirectory1=${hiveOutputDirectory1}</param>
          </hive>
          <ok to="end" />
          <error to="fail" />
       </action>
       <kill name="fail">
          <message>Oozie job failed, error message[${wf:errorMessage(wf:lastErrorNode())}]</message>
       </kill>
       <end name="end" />
    </workflow-app>
 ```
Замените `clustername` именем кластера. 

Чтобы сохранить файл, нажмите Ctrl+X, введите `Y`, а затем нажмите клавишу **ВВОД**.

Рабочий процесс состоит из двух частей:
*   Раздел учетных данных. Раздел учетных данных принимает учетные данные, которые будут использоваться для проверки подлинности действий Oozie:

    В этом примере используется проверка подлинности для действий Hive. Дополнительные сведения см. [здесь]( https://oozie.apache.org/docs/4.2.0/DG_ActionAuthentication.html).

    Служба учетных данных позволяет действиям Oozie олицетворять пользователя для доступа к службам Hadoop.

*   Раздел действий. У нас есть три действия: map-reduce, действие сервера Hive 2 и действие сервера Hive 1.

    Действие map-reduce запускает пример из пакета Oozie для выполнения операции map-reduce, в результате которой вы получаете общее количество слов.

    Действия сервера Hive 2 и сервера Hive 1 выполняют простой запрос к таблице Hivesample, поставляемой с кластером HDInsight.

    Действия Hive используют учетные данные, определенные в разделе учетных данных, для проверки подлинности с помощью ключевого слова `cred` в элементе действия

- Выполните следующую команду, чтобы скопировать файл `workflow.xml` в `/user/<domainuser>/examples/apps/map-reduce/workflow.xml`.
     ```bash
    hdfs dfs -put workflow.xml /user/<domainuser>/examples/apps/map-reduce/workflow.xml
     ```

    Замените `domainuser` на свое имя пользователя для домена.

## <a name="define-the-properties-file-for-the-oozie-job"></a>Определение файла свойств для задания Oozie

1.  Для создания и изменения нового файла свойств задания используйте следующий оператор:
     ```bash
    nano job.properties
     ```

2.   Открыв редактор nano, используйте следующий код XML в качестве содержимого файла:

    ```bash
        nameNode=adl://home
        jobTracker=headnodehost:8050
        queueName=default
        examplesRoot=examples
        oozie.wf.application.path=${nameNode}/user/[domainuser]/examples/apps/map-reduce/workflow.xml
        hiveScript1=${nameNode}/user/${user.name}/countrowshive1.hql
        hiveScript2=${nameNode}/user/${user.name}/countrowshive2.hql
        oozie.use.system.libpath=true
        user.name=[domainuser]
        jdbcPrincipal=hive/hn0-<ClusterShortName>.<Domain>.com@<Domain>.COM
        jdbcURL=[jdbcurlvalue]
        hiveOutputDirectory1=${nameNode}/user/${user.name}/hiveresult1
        hiveOutputDirectory2=${nameNode}/user/${user.name}/hiveresult2
    ```
    

   * Замените `domainuser` на свое имя пользователя для домена.
   * Замените `ClusterShortName` коротким именем кластера. Если имя кластера — https://sechadoopcontoso.azurehdisnight.net, `clustershortname` представляет собой первые шесть букв кластера: sechad
   * Замените `jdbcurlvalue` на URL-адрес JDBC из файла конфигурации Hive. Например, jdbc:hive2://headnodehost:10001/;transportMode=http.
    
   * Чтобы сохранить файл, нажмите Ctrl+X, введите `Y`, а затем нажмите клавишу **ВВОД**.

   * Этот файл свойств должен присутствовать локально при выполнении заданий Oozie

## <a name="creating-custom-hive-scripts-for-the-oozie-job"></a>Создание пользовательских скриптов Hive для задания Oozie
Можно создать два скрипта Hive для сервера Hive 1 и сервера Hive 2 следующим образом:
-   Файл сервера Hive 1:
1.  Для создания и изменения файла для действия сервера Hive 1 используйте следующий оператор:
    ```bash
    nano countrowshive1.hql
    ```

2.  Создание скрипта
    ```sql
    INSERT OVERWRITE DIRECTORY '${hiveOutputDirectory1}' 
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' 
    select devicemake from hivesampletable limit 2;
    ```

3.  Сохранение файла в HDFS
    ```bash
    hdfs dfs -put countrowshive1.hql countrowshive1.hql
    ```

-   Файл сервера Hive 2:
1.  Для создания и изменения поля для действия сервера Hive 2 используйте следующий оператор:
    ```bash
    nano countrowshive2.hql
    ```

2.  Создание скрипта
    ```sql
    INSERT OVERWRITE DIRECTORY '${hiveOutputDirectory1}' 
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' 
    select devicemodel from hivesampletable limit 2;
    ```

3.  Сохранение файла в HDFS
    ```bash
    hdfs dfs -put countrowshive2.hql countrowshive2.hql
    ```

## <a name="submission-of-oozie-jobs"></a>Отправка заданий Oozie
Отправка заданий Oozie для присоединенных к домену кластеров аналогична отправке заданий Oozie в кластерах, не присоединенных к домену.

Дополнительные сведения см. в разделе об [отправке и управлении заданиями](../hdinsight-use-oozie-linux-mac.md).

## <a name="results-from-oozie-job-submission"></a>Результаты отправки заданий Oozie
Так как задания Oozie выполняются от имени пользователя, в журналах аудита Yarn и Ranger задания отображаются как выполненные олицетворенным пользователем. Выходные данные интерфейса командной строки для заданий Oozie выглядят следующим образом:


```bash
    Job ID : 0000015-180626011240801-oozie-oozi-W
    ------------------------------------------------------------------------------------------------
    Workflow Name : map-reduce-wf
    App Path      : adl://home/user/alicetest/examples/apps/map-reduce/wf.xml
    Status        : SUCCEEDED
    Run           : 0
    User          : alicetest
    Group         : -
    Created       : 2018-06-26 19:25 GMT
    Started       : 2018-06-26 19:25 GMT
    Last Modified : 2018-06-26 19:30 GMT
    Ended         : 2018-06-26 19:30 GMT
    CoordAction ID: -
    
    Actions
    ------------------------------------------------------------------------------------------------
    ID                      Status  Ext ID          ExtStatus   ErrCode
    ------------------------------------------------------------------------------------------------
    0000015-180626011240801-oozie-oozi-W@:start:    OK  -           OK      -
    ------------------------------------------------------------------------------------------------
    0000015-180626011240801-oozie-oozi-W@mr-test    OK  job_1529975666160_0051  SUCCEEDED   -
    ------------------------------------------------------------------------------------------------
    0000015-180626011240801-oozie-oozi-W@myHive2    OK  job_1529975666160_0053  SUCCEEDED   -
    ------------------------------------------------------------------------------------------------
    0000015-180626011240801-oozie-oozi-W@myHive OK  job_1529975666160_0055  SUCCEEDED   -
    ------------------------------------------------------------------------------------------------
    0000015-180626011240801-oozie-oozi-W@end    OK  -           OK      -
    -----------------------------------------------------------------------------------------------
```

*    Журналы аудита Ranger для действий сервера Hive 2 показывают, что задания Oozie выполняли действие от имени пользователя. Представление Ranger и Yarn доступно только для администратора кластера.

## <a name="configuration-of-user-authorization-in-oozie"></a>Настройка авторизации пользователей в Oozie
В Oozie уже есть конфигурация авторизации пользователей, которая может запретить пользователям останавливать или завершать задания другого пользователя. Для этого параметр `oozie.service.AuthorizationService.security.enabled` должен иметь значение `true`. 

Дополнительные сведения об этом можно найти в документации Oozie в разделе [Настройка авторизации пользователей]( https://oozie.apache.org/docs/3.2.0-incubating/AG_Install.html):

Для таких компонентов, как сервер Hive 1, где подключаемый модуль Ranger недоступен или не поддерживается, возможна только недетализированная авторизация HDFS. Авторизация с высокой степенью детализации доступна только через подключаемые модули Ranger.

## <a name="oozie-web-ui"></a>веб-интерфейс Oozie
Веб-интерфейс Oozie позволяет получить информацию о состоянии задания Oozie в кластере. В присоединенных к домену кластерах вам потребуется:

1. Добавить [граничный узел](../hdinsight-apps-use-edge-node.md) и включить [проверку подлинности SSH Kerberos](../hdinsight-hadoop-linux-use-ssh-unix.md)

2. Следовать инструкциям в [веб-интерфейсе Oozie](../hdinsight-use-oozie-linux-mac.md), чтобы включить туннелирование SSH для граничного узла и получить доступ к пользовательскому веб-интерфейсу.

## <a name="next-steps"></a>Дополнительная информация
* [Использование Oozie с Hadoop для определения и запуска рабочих процессов в Azure HDInsight под управлением Linux](../hdinsight-use-oozie-linux-mac.md)
* [Использование временного координатора Oozie](../hdinsight-use-oozie-coordinator-time.md)
* [Выполнение запросов Hive с помощью SSH в присоединенных к домену кластерах HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).
