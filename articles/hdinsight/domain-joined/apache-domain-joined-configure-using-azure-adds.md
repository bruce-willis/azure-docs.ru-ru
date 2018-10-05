---
title: Настройка кластера HDInsight с корпоративным пакетом безопасности с помощью Azure AD-DS
description: Узнайте, как установить и настроить кластер HDInsight с Корпоративным пакетом безопасности с помощью доменных служб Azure Active Directory.
services: hdinsight
ms.service: hdinsight
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: eb24aa0471604696de99f4878baef764cfef0a8b
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/27/2018
ms.locfileid: "47408360"
---
# <a name="configure-a-hdinsight-cluster-with-enterprise-security-package-by-using-azure-active-directory-domain-services"></a>Настройка кластера HDInsight с корпоративным пакетом безопасности с помощью доменных служб Azure Active Directory

Кластеры Azure HDInsight с корпоративным пакетом безопасности предоставляют многопользовательский режим доступа. Кластеры HDInsight с корпоративным пакетом безопасности подключаются к доменам. Таким образом, пользователи домена могут использовать свои учетные данные домена для проверки подлинности в кластерах и выполнения заданий по обработке больших объемов данных. 

Из этой статьи вы узнаете, как настроить присоединенный к домену кластер HDInsight с корпоративным пакетом безопасности с помощью доменных служб Azure Active Directory (Azure AD-DS).

>[!NOTE]
>Корпоративный пакет безопасности доступен в общедоступной версии в HDI 3.6 для Spark, Interactive и Hadoop. Корпоративный пакет безопасности для типов кластеров HBase и Kafka находится на этапе предварительной версии.

## <a name="enable-azure-ad-ds"></a>Включение Azure AD-DS

Включение Azure AD-DS является необходимым предварительным условием, которое нужно выполнить, прежде чем вы сможете создать кластер HDInsight с корпоративным пакетом безопасности. Дополнительные сведения см. в разделе [Включение доменных служб Azure Active Directory с помощью портала Azure](../../active-directory-domain-services/active-directory-ds-getting-started.md). 

Если доменные службы Azure AD включены, все пользователи и объекты начинают синхронизацию из Azure Active Directory (AAD) в Azure AD DS по умолчанию. Продолжительность операции синхронизации зависит от числа объектов в AAD. Синхронизация сотен тысяч объектов может занять несколько дней. 

Пользователи могут выбрать для синхронизации только группы, которым требуется доступ к кластерам HDInsight. Этот вариант синхронизации только определенных групп называется *синхронизацией определенных объектов*. Инструкции см. в статье [Configure Scoped Synchronization from Azure AD to your managed domain](https://docs.microsoft.com/en-us/azure/active-directory-domain-services/active-directory-ds-scoped-synchronization) (Настройка синхронизации определенных объектов из Azure AD в управляемый домен).

> [!NOTE]
> Создать экземпляр Azure AD-DS могут только администраторы клиента. Многофакторная аутентификация должна быть отключена только для пользователей, которые получат доступ к кластеру.

При включении защищенных протоколов LDAP укажите в сертификате доменное имя в качестве имени субъекта или альтернативного имени субъекта. Например, если ваше доменное имя — *contoso.com*, убедитесь, что в сертификате существует такое же имя субъекта или альтернативное имя субъекта. Дополнительные сведения см. в разделе [Настройка защищенного протокола LDAP для управляемого домена Azure AD-DS](../../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md).

## <a name="check-aad-ds-health-status"></a>Проверка состояния работоспособности доменных служб AAD

Просмотрите состояние работоспособности доменных служб Active Directory Azure, выбрав **Работоспособность** в категории **Управление**. Убедитесь, что состояние доменных служб AAD отображается с зеленым значком (выполняется) и синхронизация завершена.

![Работоспособность доменных служб Azure Active Directory](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-health.png)

## <a name="add-managed-identity"></a>Добавление управляемого удостоверения

После включения Azure AD-DS создайте назначаемое пользователем управляемое удостоверение и присвойте его роли **Участник доменных служб HDInsight** в службе контроля доступа Azure AD-DS.

![Служба контроля доступа доменных служб Azure Active Directory](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-configure-managed-identity.png)

Назначение управляемого удостоверения для роли **Участник доменных служб HDInsight** гарантирует, что у удостоверения есть необходимый уровень доступа для выполнения определенных операций доменных служб в домене AAD-DS. Дополнительные сведения см. в разделе [Что такое управляемые удостоверения для ресурсов Azure](../../active-directory/managed-identities-azure-resources/overview.md).

## <a name="create-a-hdinsight-cluster-with-esp"></a>Создание кластера HDInsight с корпоративным пакетом безопасности

Следующий шаг — создать кластер HDInsight с корпоративным пакетом безопасности с помощью Azure AD-DS.

Проще разместить экземпляр Azure AD-DS и кластер HDInsight в одной и той же виртуальной сети Azure. Если вы решили поместить их в разные виртуальные сети, необходимо установить пиринг между этими виртуальными сетями, чтобы виртуальные машины HDI могли напрямую обращаться к контроллеру домена для присоединения виртуальных машин. Дополнительные сведения см. в статье [Пиринг между виртуальными сетями](../../virtual-network/virtual-network-peering-overview.md). Чтобы проверить правильность установки пиринга, присоедините виртуальную машину к виртуальной сети или подсети HDInsight и проверьте связь доменного имени или запустите **ldp.exe** для доступа к домену AAD-DS.

При создании кластера HDInsight вы можете включить Корпоративный пакет безопасности на пользовательской вкладке. 

![Безопасность и сеть Azure HDInsight](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-create-cluster-security-networking.png)

После включения корпоративного пакета безопасности будет выполняться автоматическое обнаружение и проверка распространенных неправильных настроек, связанных с Azure AD-DS.

![Проверка домена Azure HDInsight с корпоративным пакетом безопасности](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-create-cluster-esp-domain-validate.png)

Чем раньше будут обнаружены ошибки, тем больше времени вы сэкономите, исправив их до создания кластера.

![Непройденные проверки домена в Azure HDInsight с корпоративным пакетом безопасности](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-create-cluster-esp-domain-validate-failed.png)

При создании кластера HDInsight с корпоративным пакетом безопасности необходимо указать следующие параметры.

- **Администратор кластера**: выберите администратора кластера в синхронизированных службах Azure AD-DS. Эта учетная запись должна быть синхронизирована и доступна в доменных службах AAD.

- **Cluster access groups** (Группы доступа к кластеру): группы безопасности, пользователей которых нужно синхронизировать с кластером, должны быть доступны в Azure AD-DS. Например, группа HiveUsers. Дополнительные сведения см. в статье [Создание группы и добавление в нее пользователей в Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

- **URL-адрес LDAPS**. Например, ldaps://contoso.com:636.

На следующем снимке экрана показаны успешные конфигурации на портале Azure:

![Конфигурация доменных служб Active Directory в Azure HDInsight с корпоративным пакетом безопасности](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-domain-joined-configuration-azure-aads-portal.png).

Созданное управляемое удостоверение можно выбрать из раскрывающегося списка назначаемых пользователем управляемых удостоверений при создании нового кластера.

![Конфигурация доменных служб Active Directory в Azure HDInsight с корпоративным пакетом безопасности](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-identity-managed-identity.png).


## <a name="next-steps"></a>Дополнительная информация
* Сведения о настройке политик Hive и выполнении запросов Hive см. в разделе [Настройка политик Hive в кластерах HDInsight с корпоративным пакетом безопасности](apache-domain-joined-run-hive.md).
* Сведения о подключении к кластерам HDInsight с корпоративным пакетом безопасности с использованием SSH см. в разделе [Использование SSH с Hadoop на основе Linux в HDInsight из Linux, Unix или OS X](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).