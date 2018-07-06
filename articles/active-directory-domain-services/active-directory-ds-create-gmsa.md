---
title: Доменные службы Azure Active Directory. Создание групповой управляемой учетной записи службы | Документация Майкрософт
description: Администрирование управляемых доменов доменных служб Azure Active Directory
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: e6faeddd-ef9e-4e23-84d6-c9b3f7d16567
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/26/2018
ms.author: maheshu
ms.openlocfilehash: 4b4ce876760d024170020ae3faf618c7e9e76773
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37035293"
---
# <a name="create-a-group-managed-service-account-gmsa-on-an-azure-ad-domain-services-managed-domain"></a>Создание групповой управляемой учетной записи службы в управляемом домене доменных служб AAD
В этой статье показано, как создать управляемые учетные записи службы в управляемом домене доменных служб AAD.

## <a name="managed-service-accounts"></a>Управляемые учетные записи службы
Управляемая учетная запись службы (MSA) называется изолированной (sMSA), если для нее выполняется автоматическое управление паролем. Это упрощает управление именем субъекта-службы и позволяет делегировать управление другим администраторам. Такой тип управляемой учетной записи службы реализован в ОС Windows Server 2008 R2 и Windows 7.

Групповая управляемая учетная запись службы (gMSA) предоставляет такие же возможности для многих серверов в домене. Все экземпляры службы, размещенные в кластере серверов, должны использовать одну и ту же субъект-службу, чтобы работал протокол взаимной проверки подлинности. Если в качестве субъекта-службы используется gMSA, паролем учетной записи управляет не администратор, а сама операционная система Windows.

**Дополнительные сведения**:
- [Group Managed Service Accounts Overview](https://docs.microsoft.com/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview) (Обзор групповых управляемых учетных записей служб);
- [Getting Started with Group Managed Service Accounts](https://docs.microsoft.com/windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts) (Начало работы с групповыми управляемыми учетными записями служб).


## <a name="using-service-accounts-in-azure-ad-domain-services"></a>Использование учетных записей служб в Доменных службах AAD
Корпорация Майкрософт блокирует управляемые домены Доменных служб AAD и управляет ими самостоятельно. При использовании учетных записей служб в Доменных службах Azure AD следует учесть несколько важных аспектов.

### <a name="create-service-accounts-within-custom-organizational-units-ou-on-the-managed-domain"></a>Создание учетных записей служб в настраиваемых организационных единицах управляемого домена
Вы не можете создать учетную запись службы для встроенных подразделений "AADDC Users" и "AADDC Computers". [Создайте пользовательское подразделение](active-directory-ds-admin-guide-create-ou.md) в своем управляемом домене, а затем создайте в нем нужные учетные записи службы.

### <a name="the-key-distribution-services-kds-root-key-is-already-pre-created"></a>Корневой ключ для служб распространения ключей (KDS) создается заранее
Корневой ключ для служб распространения ключей (KDS) создается заранее в управляемом домене Доменных служб Azure AD. Вам не нужно создавать этот корневой ключ. Кроме того, у вас нет разрешений на это. Вы не можете просматривать корневой ключ для служб распространения ключей (KDS), созданный для управляемого домена.

## <a name="sample---create-a-gmsa-using-powershell"></a>Пример создания gMSA с помощью PowerShell
Следующий пример демонстрирует создание пользовательского подразделения с помощью PowerShell. В этом подразделении вы можете создать gMSA, используя параметр ```-Path```, чтобы указать подразделение.

```powershell
# Create a new custom OU on the managed domain
New-ADOrganizationalUnit -Name "MyNewOU" -Path "DC=CONTOSO100,DC=COM"

# Create a service account 'WebFarmSvc' within the custom OU.
New-ADServiceAccount -Name WebFarmSvc  `
-DNSHostName ` WebFarmSvc.contoso100.com  `
-Path "OU=MYNEWOU,DC=CONTOSO100,DC=com"  `
-KerberosEncryptionType AES128, AES256  ` -ManagedPasswordIntervalInDays 30  `
-ServicePrincipalNames http/WebFarmSvc.contoso100.com/contoso100.com, `
http/WebFarmSvc.contoso100.com/contoso100,  `
http/WebFarmSvc/contoso100.com, http/WebFarmSvc/contoso100  `
-PrincipalsAllowedToRetrieveManagedPassword CONTOSO-SERVER$
```

**Документация по командлетам PowerShell:**
- [командлет New-ADOrganizationalUnit](https://docs.microsoft.com/powershell/module/addsadministration/new-adorganizationalunit);
- [командлет New-ADServiceAccount](https://docs.microsoft.com/powershell/module/addsadministration/New-ADServiceAccount).


## <a name="next-steps"></a>Дополнительная информация
- [Создание подразделения в управляемом домене доменных служб Azure AD](active-directory-ds-admin-guide-create-ou.md)
- [Group Managed Service Accounts Overview](https://docs.microsoft.com/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview) (Обзор групповых управляемых учетных записей служб);
- [Getting Started with Group Managed Service Accounts](https://docs.microsoft.com/windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts) (Начало работы с групповыми управляемыми учетными записями служб).
