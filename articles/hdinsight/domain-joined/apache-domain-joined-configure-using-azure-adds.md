---
title: Настройка кластера HDInsight с корпоративным пакетом безопасности с помощью Azure AD-DS
description: Узнайте, как установить и настроить кластер HDInsight с корпоративным пакетом безопасности с помощью доменных служб Azure Active Directory.
services: hdinsight
ms.service: hdinsight
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: a5b377381fd540c2a9f1d85e0cb7edce32c2dae8
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46968379"
---
# <a name="configure-a-hdinsight-cluster-with-enterprise-security-package-by-using-azure-active-directory-domain-services"></a>Настройка кластера HDInsight с корпоративным пакетом безопасности с помощью доменных служб Azure Active Directory

Кластеры Azure HDInsight с корпоративным пакетом безопасности предоставляют многопользовательский режим доступа. Кластеры HDInsight с корпоративным пакетом безопасности подключаются к доменам. Таким образом, пользователи домена могут использовать свои учетные данные домена для проверки подлинности в кластерах и выполнения заданий по обработке больших объемов данных. 

Из этой статьи вы узнаете, как настроить присоединенный к домену кластер HDInsight с корпоративным пакетом безопасности с помощью доменных служб Azure Active Directory (Azure AD-DS).

>[!NOTE]
>Корпоративный пакет безопасности доступен в HDI 3.6 и более поздней версии для Spark, Interactive и Hadoop. Корпоративный пакет безопасности для типов кластеров HBase находится на этапе предварительной версии.


## <a name="enable-azure-ad-ds"></a>Включение Azure AD-DS

Включение Azure AD-DS является необходимым предварительным условием, которое нужно выполнить, прежде чем вы сможете создать кластер HDInsight с корпоративным пакетом безопасности. Дополнительные сведения см. в разделе [Включение доменных служб Azure Active Directory с помощью портала Azure](../../active-directory-domain-services/active-directory-ds-getting-started.md). 

> [!NOTE]
> Создать экземпляр Azure AD-DS могут только администраторы клиента. Если вы используете Azure Data Lake Storage 1-го поколения в качестве хранилища по умолчанию для HDInsight, убедитесь, что используемый по умолчанию клиент Azure AD для Data Lake Storage 1-го поколения совпадает с доменом для кластера HDInsight. Так как в Hadoop используется протокол Kerberos и обычная проверка подлинности, многофакторная проверка подлинности должна быть отключена для пользователей, которые будут иметь доступ к кластеру.

Защищенный протокол LDAP предназначен для управляемого домена Azure AD-DS. При включении защищенных протоколов LDAP укажите в сертификате доменное имя в качестве имени субъекта или альтернативного имени субъекта. Дополнительные сведения см. в разделе [Настройка защищенного протокола LDAP для управляемого домена Azure AD-DS](../../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md).

## <a name="add-managed-identity"></a>Добавление управляемого удостоверения

После включения Azure AD-DS создайте управляемое удостоверение и назначьте его роли **Участник доменных служб HDInsight** в службе контроля доступа Azure AD-DS.

![Служба контроля доступа доменных служб Azure Active Directory](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-configure-managed-identity.png)

Дополнительные сведения см. в разделе [Что такое управляемые удостоверения для ресурсов Azure](../../active-directory/managed-identities-azure-resources/overview.md).

## <a name="create-a-hdinsight-cluster-with-esp"></a>Создание кластера HDInsight с корпоративным пакетом безопасности

Следующий шаг — создать кластер HDInsight с корпоративным пакетом безопасности с помощью Azure AD-DS.

Проще разместить экземпляр Azure AD-DS и кластер HDInsight в одной и той же виртуальной сети Azure. Если вы решили поместить их в разные виртуальные сети, необходимо установить пиринг между этими виртуальными сетями, чтобы виртуальные машины HDI могли напрямую обращаться к контроллеру домена для присоединения виртуальных машин. Дополнительные сведения см. в статье [Пиринг между виртуальными сетями](../../virtual-network/virtual-network-peering-overview.md).

При создании кластера HDInsight вы можете включить корпоративный пакет безопасности для подключения к кластеру с помощью Azure AD-DS. 

![Безопасность и сеть Azure HDInsight](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-create-cluster-security-networking.png)

После включения корпоративного пакета безопасности будет выполняться автоматическое обнаружение и проверка распространенных неправильных настроек, связанных с Azure AD-DS.

![Проверка домена Azure HDInsight с корпоративным пакетом безопасности](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-create-cluster-esp-domain-validate.png)

Чем раньше будут обнаружены ошибки, тем больше времени вы сэкономите, исправив их до создания кластера.

![Непройденные проверки домена в Azure HDInsight с корпоративным пакетом безопасности](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-create-cluster-esp-domain-validate-failed.png)

При создании кластера HDInsight с корпоративным пакетом безопасности необходимо указать следующие параметры.

- **Администратор кластера**: выберите администратора кластера в синхронизированных службах Azure AD-DS.

- **Группы доступа к кластеру**: группы безопасности, пользователей которых вы хотите синхронизировать с кластером, должны быть синхронизированы и доступны в Azure AD-DS. Например, HiveUsers. Чтобы указать несколько групп пользователей, разделяйте их точкой с запятой (;). Группы должны быть в каталоге до того, как вы начнете подготовку. Дополнительные сведения см. в статье [Создание группы и добавление в нее пользователей в Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). Если группа отсутствует, отображается сообщение об ошибке "Group HiveUsers not found in the Active Directory" (Группа HiveUsers не найдена в Active Directory).

- **URL-адрес LDAPS**. Например, ldaps://contoso.onmicrosoft.com:636.

    > [!IMPORTANT]
    > Следует ввести полный URL-адрес, включая ldaps:// и номер порта (:636).

На следующем снимке экрана показаны конфигурации на портале Azure:

   ![Конфигурация доменных служб Active Directory в Azure HDInsight с корпоративным пакетом безопасности](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-domain-joined-configuration-azure-aads-portal.png).


## <a name="next-steps"></a>Дополнительная информация
* Сведения о настройке политик Hive и выполнении запросов Hive см. в разделе [Настройка политик Hive в кластерах HDInsight с корпоративным пакетом безопасности](apache-domain-joined-run-hive.md).
* Сведения о подключении к кластерам HDInsight с корпоративным пакетом безопасности с использованием SSH см. в разделе [Использование SSH с Hadoop на основе Linux в HDInsight из Linux, Unix или OS X](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).

