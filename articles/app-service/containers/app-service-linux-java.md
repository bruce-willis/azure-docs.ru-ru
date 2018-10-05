---
title: Поддержка языка Java для Службы приложений Azure в Linux | Документация Майкрософт
description: Руководство разработчика по развертыванию приложений Java с помощью Службы приложений Azure в Linux.
keywords: Служба приложений Azure, веб-приложение, Linux, OSS
services: app-service
author: rloutlaw
manager: angerobe
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 08/29/2018
ms.author: routlaw
ms.openlocfilehash: 2b2256ef5802160dbaa66e2a098a798fcdc653d2
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2018
ms.locfileid: "47064518"
---
# <a name="java-developers-guide-for-app-service-on-linux"></a>Руководство разработчика для Java для службы приложений в Linux

Служба приложений Azure в Linux позволяет разработчикам быстро создавать, развертывать и масштабировать веб-приложения на основе пакетов Tomcat Java или Java Standard Edition (SE) в полностью управляемой службе под управлением Linux. Возможно развертывание приложений с подключаемыми модулями Maven из командной строки или в редакторах, например Visual Studio Code, Eclipse или IntelliJ.

Это руководство содержит основные понятия и инструкции для разработчиков для Java, использующих службу приложений для Linux. Если вы раньше не использовали Службу приложений Azure для Linux, мы рекомендуем сначала ознакомиться с [кратким руководством по Java](quickstart-java.md). Ответы на общие вопросы об использовании службы приложений для Linux, не относящиеся к разработке для Java, можно найти в разделе [вопросов и ответов о службе приложений для Linux](app-service-linux-faq.md).

## <a name="logging-and-debugging-apps"></a>Ведение журнала и отладка приложений

Отчеты о производительности, визуализация трафика и проверка работоспособности доступны на портале Azure для каждого приложения. Прочитайте [обзор диагностики с помощью Службы приложений Azure](/azure/app-service/app-service-diagnostics), чтобы узнать больше о том, как обращаться к этим средствам диагностики и использовать их.

### <a name="ssh-console-access"></a>Доступ к консоли SSH 

Доступно SSH-подключение к среде Linux, в которой выполняется ваше приложение. В разделе [Поддержка SSH для службы приложений Azure в Linux](/azure/app-service/containers/app-service-linux-ssh-support) приведены подробные инструкции по подключению к системе Linux с помощью веб-браузера или локального терминала.

### <a name="streaming-logs"></a>Журналы потоковой передачи.

Для быстрой отладки и устранения неполадок можно осуществлять потоковую передачу журналов в консоль с помощью Azure CLI. Настройте интерфейс командной строки с помощью команды `az webapp log config`, чтобы включить ведение журнала.

```azurecli-interactive
az webapp log config --name ${WEBAPP_NAME} \
 --resource-group ${RESOURCEGROUP_NAME} \
 --web-server-logging filesystem
```

Затем включите потоковую передачу журналов в консоль с помощью команды `az webapp log tail`.

```azurecli-interactive
az webapp log tail --name webappname --resource-group myResourceGroup
```

Дополнительные сведения см. в разделе [Потоковая передача с использованием интерфейса командной строки Azure](/azure/app-service/web-sites-enable-diagnostic-log#streaming-with-azure-command-line-interface).

### <a name="app-logging"></a>Ведение журнала приложений

Включите [ведение журнала приложений](/azure/app-service/web-sites-enable-diagnostic-log#enablediag) с помощью портала Azure или [Azure CLI](/cli/azure/webapp/log#az-webapp-log-config), чтобы настроить службу приложений для записи выходных данных стандартной консоли приложения и потоков ошибок стандартной консоли в локальную файловую систему или хранилище BLOB-объектов Azure. Запись журналов в локальную файловую систему экземпляра службы приложений отключается через 12 часов после настройки ведения журнала. Если необходимо более длительное хранение, настройте приложение для записи выходных данных в контейнер больших двоичных объектов.

Если приложение использует [Logback](https://logback.qos.ch/) или [Log4j](https://logging.apache.org/log4j) для трассировки, то эти данные трассировки можно передать в Azure Application Insights для просмотра, выполнив инструкции по настройке платформы ведения журнала в разделе [Просмотр журналов трассировки Java в Application Insights](/azure/application-insights/app-insights-java-trace-logs). 

## <a name="customization-and-tuning"></a>Настройка

Служба приложений Azure для Linux поддерживает настройку с помощью портала Azure и интерфейса командной строки. Ознакомьтесь со следующими статьями о настройке конкретных веб-приложений не на платформе Java:

- [Настройка параметров службы приложений](/azure/app-service/web-sites-configure?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Настройка личного домена](/azure/app-service/app-service-web-tutorial-custom-domain?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Включение SSL](/azure/app-service/app-service-web-tutorial-custom-ssl?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Добавление CDN](/azure/cdn/cdn-add-to-web-app?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)

### <a name="set-java-runtime-options"></a>Настройка параметров среды выполнения Java

Чтобы настроить выделенную память или другие параметры среды выполнения виртуальной машины Java в средах Java SE и Tomcat, задайте параметр JAVA_OPTS в качестве [параметра приложения](/azure/app-service/web-sites-configure#app-settings), как показано ниже. При запуске служба приложений для Linux передает этот параметр в качестве переменной среды в среду выполнения Java.

На портале Azure в разделе **Параметры приложения** для веб-приложения создайте параметр приложения `JAVA_OPTS`, включающий в себя дополнительные параметры, такие как `$JAVA_OPTS -Xms512m -Xmx1204m`.

Чтобы настроить параметр приложения из подключаемого модуля Maven для Службы приложений Azure для Linux, добавьте теги "параметр/значение" в раздел подключаемого модуля Azure. В следующем примере задаются минимальный и максимальный размеры кучи для Java.

```xml
<appSettings> 
    <property> 
        <name>JAVA_OPTS</name> 
        <value>$JAVA_OPTS -Xms512m -Xmx1204m</value> 
    </property> 
</appSettings> 
```

Разработчики, запускающие отдельное приложение в одном слоте развертывания в плане службы приложений, могут использовать следующие параметры.

- Экземпляры B1 и S1: -Xms1024m -Xmx1024m.
- Экземпляры B2 и S2: -Xms3072m -Xmx3072m.
- Экземпляры B3 и S3: -Xms6144m -Xmx6144m.


При настройке параметров кучи приложения просмотрите сведения о плане службы приложений и примите во внимание, что наличие нескольких приложений и одного слота развертывания требует оптимального выделения памяти.

### <a name="turn-on-web-sockets"></a>Включение веб-сокетов

Включите для приложения поддержку веб-сокетов на портале Azure в разделе **Параметры приложения**. Необходимо будет перезапустить приложение, чтобы этот параметр вступил в силу.

Включите поддержку веб-сокетов с помощью Azure CLI, выполнив следующую команду.

```azurecli-interactive
az webapp config set -n ${WEBAPP_NAME} -g ${WEBAPP_RESOURCEGROUP_NAME} --web-sockets-enabled true 
```

Затем перезапустите приложение.

```azurecli-interactive
az webapp stop -n ${WEBAPP_NAME} -g ${WEBAPP_RESOURCEGROUP_NAME} 
az webapp start -n ${WEBAPP_NAME} -g ${WEBAPP_RESOURCEGROUP_NAME}  
```

### <a name="set-default-character-encoding"></a>Настройка кодировки по умолчанию 

На портале Azure в разделе **Параметры приложения** для веб-приложения создайте параметр приложения `JAVA_OPTS` со значением `$JAVA_OPTS -Dfile.encoding=UTF-8`.

Можно также настроить параметр приложения с помощью подключаемого модуля Maven для службы приложений. Добавьте теги имени и значения параметра в конфигурацию подключаемого модуля. 

```xml
<appSettings> 
    <property> 
        <name>JAVA_OPTS</name> 
        <value>$JAVA_OPTS -Dfile.encoding=UTF-8</value> 
    </property> 
</appSettings> 
```

## <a name="secure-application"></a>Защита приложения

Для приложений Java, работающих в службе приложений для Linux, предлагается тот же набор [рекомендаций по обеспечению безопасности](/azure/security/security-paas-applications-using-app-services), что и для других приложений. 

### <a name="authenticate-users"></a>Проверка подлинности пользователей

Настройте аутентификацию приложения на портале Azure с помощью параметра **Проверка подлинности и авторизация**. Вы можете включить аутентификацию с помощью Azure Active Directory или имен для входа в социальные сети, таких как Facebook, Google или GitHub. На портале Azure можно настроить только один поставщик аутентификации.  Дополнительные сведения приведены в разделе [Настройка приложения службы приложений для использования входа с помощью Azure Active Directory](/azure/app-service/app-service-mobile-how-to-configure-active-directory-authentication) и связанных статьях о других поставщиках удостоверений.

Если необходимо включить несколько поставщиков входа, следуйте инструкциям в статье [Настройка проверки подлинности и авторизации в службе приложений Azure](https://docs.microsoft.com/azure/app-service/app-service-authentication-how-to).

 Разработчики для Spring Boot могут использовать [краткое руководство по использованию Spring Boot и Azure Active Directory](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-azure-active-directory?view=azure-java-stable), чтобы защитить приложения с помощью привычных заметок и интерфейсов API Spring Security.

### <a name="configure-tlsssl"></a>Настройка TLS/SSL

Следуйте инструкциям в разделе [Руководство. Привязывание существующего настраиваемого SSL-сертификата к веб-приложениям Azure](/azure/app-service/app-service-web-tutorial-custom-ssl), чтобы передать существующий SSL-сертификат и привязать его к доменному имени приложения. По умолчанию приложение по-прежнему будет разрешать HTTP-подключения. Выполните соответствующие инструкции в этом руководстве, чтобы принудительно включить SSL и TLS.

## <a name="tomcat"></a>Tomcat 

### <a name="connecting-to-data-sources"></a>Подключение к источникам данных

>[!NOTE]
> Если приложение использует Spring Framework или Spring Boot, можно задать сведения о подключении к базе данных для JPA Spring Data в качестве переменных среды (в файле свойств приложения). Затем с помощью [параметров приложения](/azure/app-service/web-sites-configure#app-settings) можно задать эти значения для приложения, воспользовавшись порталом Azure или интерфейсом командной строки.

Чтобы настроить Tomcat для использования управляемых подключений к базам данных с помощью Java Database Connectivity (JDBC) или Java Persistence API (JPA), сначала настройте считывание переменной среды CATALINA_OPTS в Tomcat при запуску. Задайте эти значения с помощью параметра приложения в подключаемом модуле Maven для службы приложений.

```xml
<appSettings> 
    <property> 
        <name>CATALINA_OPTS</name> 
        <value>"$CATALINA_OPTS -Dmysqluser=${mysqluser} -Dmysqlpass=${mysqlpass} -DmysqlURL=${mysqlURL}"</value> 
    </property> 
</appSettings> 
```

Можно также настроить эквивалентный параметр службы приложений на портале Azure.

Затем определите, должен ли источник данных быть доступным только для одного приложения или для всех приложений, работающих в плане службы приложений.

Для источников данных уровня приложения сделайте следующее. 

1. Добавьте в веб-приложение файл `context.xml` (если он не существует), затем добавьте его в каталог `META-INF` WAR-файла после сборки проекта.

2. Добавьте в этот файл запись пути `Context`, чтобы связать источник данных с адресом JNDI. Атрибут 

    ```xml
    <Context>
        <Resource
            name="jdbc/mysqldb" type="javax.sql.DataSource"
            url="${mysqlURL}"
            driverClassName="com.mysql.jdbc.Driver"
            username="${mysqluser}" password="${mysqlpass}"
        />
    </Context>
    ```

3. Обновите `web.xml` приложения для использования источника данных в этом приложении.

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/mysqldb</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

Для общих ресурсов уровня сервера сделайте следующее.

1. Скопируйте содержимое `/usr/local/tomcat/conf` в папку `/home/tomcat` на экземпляре службы приложений для Linux с помощью SSH, если на нем еще нет конфигурации.

2. Добавьте контекст в `server.xml`.

    ```xml
    <Context>
        <Resource
            name="jdbc/mysqldb" type="javax.sql.DataSource"
            url="${mysqlURL}"
            driverClassName="com.mysql.jdbc.Driver"
            username="${mysqluser}" password="${mysqlpass}"
        />
    </Context>
    ```

3. Обновите `web.xml` приложения для использования источника данных в этом приложении.

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/mysqldb</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

4. Убедитесь, что файлы драйвера JDBC доступны для загрузчика классов Tomcat, разместив их в каталоге `/home/tomcat/lib`. Чтобы передать эти файлы в экземпляр службы приложений, выполните следующие действия.  
    1. Установите расширение webpp для Службы приложения Azure.
      ```azurecli-interactive
      az extension add –name webapp
      ```
    2. Выполните следующую команду интерфейса командной строки, чтобы создать туннель SSH между локальной системой и службой приложений.
      ```azurecli-interactive
      az webapp remote-connection create –g [resource group] -n [app name] -p [local port to open]
      ```
    3. Подключитесь к локальному порту туннелирования с помощью клиента SFTP и передайте эти файлы в папку `/home/tomcat/lib`.

5. Перезапустите приложение службы приложений для Linux. Tomcat сбросит `CATALINA_HOME` до значения `/home/tomcat` и будет использовать обновленную конфигурацию и классы.

## <a name="docker-containers"></a>Контейнеры Docker

Чтобы использовать JDK Zulu с поддержкой Azure, работающий в службе приложений в контейнерах, убедитесь, что в `Dockerfile` приложения используются образы из [репозитория образов Docker для службы приложения для Java](https://github.com/Azure-App-Service/java).

## <a name="runtime-availability-and-statement-of-support"></a>Доступность среды выполнения и заявление о поддержке

Служба приложений для Linux поддерживает две среды выполнения для управляемого размещения веб-приложений Java.

- [Контейнер сервлетов Tomcat](http://tomcat.apache.org/) для запуска приложений, которые упакованы как файлы веб-архива (WAR-файлы). Поддерживаются версии 8.5 и 9.0.
- Среда выполнения Java SE для запуска приложений, которые упакованы как файлы архива Java (JAR-файлы). Сейчас поддерживается только основной номер версии 8.

## <a name="java-runtime-statement-of-support"></a>Заявление о поддержке среды выполнения Java 

### <a name="jdk-versions-and-maintenance"></a>Версии JDK и обслуживание

Azure поддерживает пакет Java Development Kit (JDK) [Zulu](https://www.azul.com/products/zulu-and-zulu-enterprise/), предоставляемый компанией [Azul Systems](https://www.azul.com/).

Обновления для основного номера версии будут предоставляться посредством новых вариантов среды выполнения в Службе приложений Azure для Linux. Пользователи, устанавливающие более новые версии Java посредством настройки развернутой службы приложений, несут ответственность за тестирование выбранных обновлений для основного номера версии и их соответствие своим потребностям.

Каждый квартал в поддерживаемые пакеты JDK автоматически вносятся исправления. Это происходит в январе, апреле, июле и октябре.

### <a name="security-updates"></a>обновления для системы безопасности;

Исправления для устранения серьезных уязвимостей в системе безопасности будут выпускаться по мере выпуска компанией Azul Systems. "Серьезными" считаются уязвимости с базовым индексом не меньше 9.0 в [NIST Common Vulnerability Scoring System версии 2](https://nvd.nist.gov/cvss.cfm).

### <a name="deprecation-and-retirement"></a>Нерекомендуемые версии и прекращение использования

Если планируется прекращение использования какой-либо поддерживаемой среды выполнения Java, то разработчики для Azure, использующие эту среду выполнения, получат соответствующее уведомление по крайней мере за шесть месяцев до прекращения использования.

### <a name="local-development"></a>Локальная разработка

Разработчики могут скачать выпуск Production Edition пакета Azul Zulu Enterprise JDK для локальной разработки с [сайта загрузки Azul](https://www.azul.com/downloads/zulu/).

### <a name="development-support"></a>Поддержка разработки

Техническая поддержка пакета Azul Zulu Enterprise JDK при разработке для Azure или [Azure Stack](https://azure.microsoft.com/overview/azure-stack/) предоставляется при наличии [соответствующего плана поддержки Azure](https://azure.microsoft.com/support/plans/).

### <a name="runtime-support"></a>Поддержка времени выполнения

Разработчики могут [сообщить о проблеме](/azure/azure-supportability/how-to-create-azure-support-request) со средой выполнения Java для службы приложений для Linux в службу поддержки Azure при наличии [соответствующего плана поддержки](https://azure.microsoft.com/support/plans/).

## <a name="next-steps"></a>Дополнительная информация

Посетите центр [Azure для разработчиков Java](/java/azure/), чтобы найти краткие руководства Azure, руководства и справочную документацию по Java.
