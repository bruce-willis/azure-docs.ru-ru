---
title: Шифрование диска в центре безопасности Azure | Документация Майкрософт
description: В этом документе объясняется, как выполнить рекомендацию Центра безопасности Azure по **применению шифрования дисков**.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: 6cc7824a-8d6b-4a5f-ab40-e3bbaebc4a91
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/2017
ms.author: terrylan
ms.openlocfilehash: bcfa6ead0eac4a8a631c0388466534406c7a594b
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/24/2018
ms.locfileid: "42887344"
---
# <a name="apply-disk-encryption-in-azure-security-center"></a>Шифрование диска в центре безопасности Azure
Центр безопасности Azure рекомендует шифровать диски виртуальных машин Windows или Linux, которые не зашифрованы с помощью шифрования дисков Azure. Шифрование дисков позволяет шифровать диски виртуальных машин IaaS Windows и Linux.  Шифровать рекомендуется как том операционной системы, так и том данных на виртуальной машине.

Дисковое шифрование использует стандартные для отрасли функции ([BitLocker](https://technet.microsoft.com/library/cc732774.aspx) в Windows и [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) в Linux). Эти функции обеспечивают шифрование ОС и данных, помогая защитить информацию в соответствии с применимыми требованиями к безопасности и стандартами. Это решение интегрировано с [хранилищем ключей Azure](https://azure.microsoft.com/documentation/services/key-vault/) и позволяет управлять ключами и секретами дискового шифрования через подписку хранилища ключей. Шифрование выполняется для всех данных на дисках виртуальных машин в [хранилище Azure](https://azure.microsoft.com/documentation/services/storage/).

> [!NOTE]
> Шифрование дисков Azure доступно для следующих операционных систем Windows Server: Windows Server 2008 R2, Windows Server 2012 и Windows Server 2012 R2. Шифрование дисков доступно для следующих операционных систем Linux: Ubuntu, CentOS, SUSE и SUSE Linux Enterprise Server (SLES).
>
>

## <a name="implement-the-recommendation"></a>Выполнение рекомендаций
1. В колонке **Рекомендации** выберите **Применить шифрование дисков**.
2. В колонке **Применение шифрования дисков** появится список виртуальных машин, для которых рекомендуется использовать шифрование дисков.
3. Следуйте инструкциям, чтобы применить шифрование дисков к этим виртуальным машинам.

![][1]

Для шифрования виртуальных машин Azure, которые определены центром безопасности как требующие шифрования, мы рекомендуем выполнить следующие действия.

* Установите и настройте Azure PowerShell. Это позволит вам выполнять команды PowerShell, чтобы установить необходимые компоненты для шифрования виртуальных машин Azure.
* Получите и выполните сценарий Azure PowerShell с необходимыми компонентами для шифрования дисков Azure.
* Зашифруйте свои виртуальные машины.

В кратком руководстве [Шифрование виртуальной машины IaaS под управлением Windows с помощью Azure PowerShell](../security/quick-encrypt-vm-powershell.md) подробно описаны эти шаги. В этом разделе предполагается, что в качестве клиентского компьютера, на котором будет настраиваться шифрование дисков, используется компьютер под управлением Windows.

Есть разные подходы, используемые при работе с виртуальными машинами Azure. Если у вас есть опыт работы с Azure PowerShell или интерфейсом командной строки Azure, вы можете использовать альтернативные подходы. См. дополнительные сведения о [дисковом шифровании Azure](../security/azure-security-disk-encryption.md).

## <a name="see-also"></a>См. также
В этом документе объясняется, как выполнить рекомендацию Центра безопасности "Применение шифрования дисков". Дополнительные сведения о шифровании дисков см. по ссылкам ниже.

* [Encryption and key management with Azure Key Vault](https://azure.microsoft.com/documentation/videos/azurecon-2015-encryption-and-key-management-with-azure-key-vault/) (видео, 36 мин 39 с) — узнайте, как использовать управление шифрованием дисков для защиты данных на виртуальных машинах IaaS и в хранилище ключей Azure.
* [Дисковое шифрование Azure для виртуальных машин IaaS под управлением Windows и Linux](../security/azure-security-disk-encryption-overview.md) (документ) — узнайте, как включить шифрование дисков для виртуальных машин Windows и Linux.

Дополнительные сведения о Центре безопасности см. в следующих статьях:

* [Настройка политик безопасности в Центре безопасности Azure](security-center-policies.md) — узнайте, как настраивать политики безопасности.
* [Наблюдение за работоспособностью системы безопасности в Центре безопасности Azure](security-center-monitoring.md) — сведения о мониторинге работоспособности ресурсов Azure.
* [Управление оповещениями безопасности в Центре безопасности Azure и реагирование на них](security-center-managing-and-responding-alerts.md) — узнайте, как управлять оповещениями системы безопасности и реагировать на них.
* [Управление рекомендациями по безопасности в Центре безопасности Azure](security-center-recommendations.md) — узнайте, как рекомендации могут помочь вам защитить ресурсы Azure.
* [Часто задаваемые вопросы о Центре безопасности Azure](security-center-faq.md) — часто задаваемые вопросы об использовании этой службы.
* [Блог по безопасности Azure](http://blogs.msdn.com/b/azuresecurity/) — публикации блога, посвященные безопасности и соответствию требованиям в Azure.

<!--Image references-->
[1]: ./media/security-center-apply-disk-encryption/apply-disk-encryption.png
