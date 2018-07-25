---
title: Архитектура присоединенного к домену кластера Azure HDInsight | Документация Майкрософт
description: Сведения о планировании архитектуры присоединенного к домену кластера HDInsight.
services: hdinsight
documentationcenter: ''
author: omidm1
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 7dc6847d-10d4-4b5c-9c83-cc513cf91965
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: ''
ms.topic: conceptual
ms.date: 05/30/2018
ms.author: omidm
ms.openlocfilehash: 1f51a1fbb38bc27d15b7a45ca4783508d863fee5
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/18/2018
ms.locfileid: "39112633"
---
# <a name="plan-azure-domain-joined-hadoop-clusters-in-hdinsight"></a>Планирование архитектуры присоединенных к домену кластеров Hadoop в Azure HDInsight

Стандартный кластер Azure HDInsight рассчитан на одного пользователя. Он подходит для большинства организаций с небольшими отделами по работе с приложениями, создающими объемные рабочие нагрузки данных. Для каждого пользователя по запросу может быть создан выделенный кластер, который может быть удален, когда перестанет быть нужен. 

Многие организации перешли на новую модель. В ней команды ИТ-специалистов управляют кластерами, которые совместно используются несколькими командами по разработке приложений. Таким крупным предприятиям требуется многопользовательский доступ к кластерам в Azure HDInsight.

HDInsight использует наиболее популярный поставщик удостоверений — управляемую службу Active Directory. Благодаря интеграции HDInsight с [доменными службами Azure Active Directory (Azure AD DS)](../../active-directory-domain-services/active-directory-ds-overview.md) доступ к кластерам можно получить с помощью учетных данных домена. 

Виртуальные машины в HDInsight присоединены к указанному домену. Поэтому все службы, запущенные в HDInsight (Ambari, сервер Hive, Ranger, сервер Spark Thrift и т. д.), работают прозрачно для аутентифицированного пользователя. Администраторы могут настраивать строгие политики авторизации на основе Apache Ranger, чтобы применять управление доступом на основе ролей для ресурсов в кластере.


## <a name="integrate-hdinsight-with-active-directory"></a>Интеграция HDInsight с Active Directory

Hadoop с открытым кодом использует протокол Kerberos для аутентификации и обеспечения безопасности. Таким образом, узлы кластера HDInsight присоединены к домену под управлением Azure AD DS. Для включенных в кластер компонентов Hadoop настраиваются параметры безопасности Kerberos. 

Для каждого компонента Hadoop автоматически создается субъект-служба. Кроме того, соответствующий субъект компьютера создается для каждого компьютера, присоединенного к домену. Для хранения этих субъектов служб и компьютеров необходимо предоставить подразделение в контроллере домена (Azure AD DS), в котором эти субъекты размещены. 

Кратко перечислим, что вам нужно создать в сетевом окружении:

- Домен Active Directory (под управлением Azure AD DS).
- В Azure AD DS должен быть включен протокол LDAPS.
- Соответствующее сетевое подключение виртуальной сети HDInsight к виртуальной сети Azure AD DS, если вы выбрали для них отдельные виртуальные сети. Виртуальная машина в виртуальной сети HDInsight должна иметь возможность напрямую обращаться к Azure AD DS через пиринг виртуальных сетей. Если HDInsight и Azure AD DS развернуты в одной виртуальной сети, подключение предоставляется автоматически и никаких дальнейших действий не требуется.
- [В Azure AD DS должно быть создано](../../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md) подразделение.
- учетную запись службы со следующими разрешениями:
    - на создание субъектов-служб в подразделении;
    - на присоединение компьютеров к домену и создание субъектов компьютеров в подразделении.

На приведенном ниже снимке экрана представлено такое подразделение в домене contoso.com. На нем также показаны некоторые из субъектов-служб и субъектов компьютеров.

![Подразделение для присоединенных к домену кластеров HDInsight](./media/apache-domain-joined-architecture/hdinsight-domain-joined-ou.png).

## <a name="set-up-different-domain-controllers"></a>Настройка разных контроллеров домена
HDInsight в настоящее время поддерживает только Azure AD DS в качестве основного контроллера домена, используемого кластером для передачи данных по протоколу Kerberos. Но возможны и другие сложные конфигурации Active Directory, при условии, что они обеспечивают доступ к HDInsight для Azure AD DS.

### <a name="azure-active-directory-domain-services"></a>Доменные службы Azure Active Directory
[Azure AD DS](../../active-directory-domain-services/active-directory-ds-overview.md) предоставляет управляемый домен, который полностью совместим с Windows Server Active Directory. Корпорация Майкрософт управляет доменом, применяет к нему исправления и осуществляет его мониторинг в конфигурации высокой доступности. Вы можете развернуть свой кластер, не беспокоясь о поддержке контроллеров домена. 

Пользователи, группы и пароли синхронизируются из Azure Active Directory (Azure AD). Однонаправленная синхронизация из экземпляра Azure AD в Azure AD DS позволяет пользователям входить в кластер, используя те же учетные данные организации. 

Дополнительные сведения см. в разделе [Настройка присоединенных к домену кластеров HDInsight с помощью доменных служб Azure Active Directory](./apache-domain-joined-configure-using-azure-adds.md).

### <a name="on-premises-active-directory-or-active-directory-on-iaas-vms"></a>Локальная служба Active Directory или служба Active Directory на виртуальных машинах IaaS

Если у вас используется локальный экземпляр Active Directory или более сложная конфигурация Active Directory для домена, то вы можете синхронизировать их удостоверения в Azure AD с помощью Azure AD Connect. Затем вы можете включить Azure AD DS на этом клиенте Active Directory. 

Так как в протоколе Kerberos используются хэши паролей, необходимо будет [включить синхронизацию хэша пароля в Azure AD DS](../../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md). Если вы используете федерацию на основе служб федерации Active Directory (AD FS), то у вас есть возможность настроить синхронизацию паролей для дополнительной защиты на случай сбоя в инфраструктуре AD FS. Дополнительные сведения см. в статье [Реализация синхронизации хэшированных паролей в службе синхронизации Azure AD Connect](../../active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md). 

Использование только локальной службы Active Directory или Active Directory на виртуальных машинах IaaS, без Azure AD и Azure AD DS, не поддерживается для присоединенных к домену кластеров HDInsight.

## <a name="next-steps"></a>Дополнительная информация
* [Настройка присоединенных к домену кластеров HDInsight с помощью доменных служб Azure Active Directory](apache-domain-joined-configure-using-azure-adds.md)
* [Настройка политик Hive в присоединенном к домену кластере HDInsight](apache-domain-joined-run-hive.md)
* [Управление присоединенными к домену кластерами HDInsight](apache-domain-joined-manage.md) 
