---
title: Шифрование дисков Azure для Windows | Документация Майкрософт
description: Развертывание шифрования дисков Azure на виртуальной машине Windows с помощью расширения виртуальной машины.
services: virtual-machines-windows
documentationcenter: ''
author: ejarvi
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 06/12/2018
ms.author: ejarvi
ms.openlocfilehash: 11ec26729b2239279dddc8cd62f6b658a4f7ed20
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39413796"
---
# <a name="azure-disk-encryption-for-windows-microsoftazuresecurityazurediskencryption"></a>Шифрование дисков Azure для Windows (Microsoft.Azure.Security.AzureDiskEncryption)

## <a name="overview"></a>Обзор

При шифровании дисков Azure используется Bitlocker для полного шифрования диска на виртуальных машинах Azure под управлением Windows.  Шифрование дисков Azure интегрировано в Azure Key Vault, что позволяет управлять секретами и ключами шифрования дисков в подписке Key Vault. 

## <a name="prerequisites"></a>Предварительные требования

См. полный список [необходимых компонентов для шифрования дисков Azure](
../../security/azure-security-disk-encryption-prerequisites.md).

### <a name="operating-system"></a>Операционная система

Список текущих поддерживаемых версий Windows см. в разделе с описанием [необходимых компонентов для шифрования дисков Azure](../../security/azure-security-disk-encryption-prerequisites.md).

### <a name="internet-connectivity"></a>Подключение к Интернету

При шифровании дисков Azure требуется подключение к Интернету для доступа к Active Directory, хранилищу Key Vault, хранения и конечным точкам управления пакетами.  Список параметров безопасности сети см. в разделе с описанием [необходимых компонентов для шифрования дисков Azure](
../../security/azure-security-disk-encryption-prerequisites.md).

## <a name="extension-schema"></a>Схема расширения

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2015-06-15",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientSecret": "[aadClientSecret]",
    },
    "publisher": "Microsoft.Azure.Security",
    "settings": {
      "AADClientID": "[aadClientID]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KeyVaultURL": "[keyVaultURL]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    },
    "type": "AzureDiskEncryption",
    "typeHandlerVersion": "[extensionVersion]"
  }
}
```

### <a name="property-values"></a>Значения свойств

| ИМЯ | Значение и пример | Тип данных |
| ---- | ---- | ---- |
| версия_API | 2015-06-15 | дата |
| publisher | Microsoft.Azure.Security | строка |
| Тип | AzureDiskEncryptionForWindows| строка |
| typeHandlerVersion | 1.0, 2.2 (VMSS) | int |
| (Необязательно) AADClientID | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | GUID | 
| (Необязательно) AADClientSecret | password | строка |
| (Необязательно) AADClientCertificate | thumbprint | строка |
| EncryptionOperation | EnableEncryption | строка | 
| KeyEncryptionAlgorithm | RSA-OAEP | строка |
| KeyEncryptionKeyURL | URL-адрес | строка |
| KeyVaultURL | URL-адрес | строка |
| SequenceVersion | uniqueidentifier | строка |
| VolumeType | ОС, данные, все | строка |

## <a name="template-deployment"></a>Развертывание шаблона
Пример шаблона развертывания см. в руководстве по [созданию зашифрованной виртуальной машины Windows из образа коллекции](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image).

## <a name="azure-cli-deployment"></a>Развертывание с помощью Azure CLI

Инструкции см. в последней версии [документации по Azure CLI](/cli/azure/vm/encryption?view=azure-cli-latest). 

## <a name="troubleshoot-and-support"></a>Устранение неполадок и поддержка

### <a name="troubleshoot"></a>Устранение неполадок

Дополнительные сведения см. в [руководстве по устранению неполадок с шифрованием дисков Azure](../../security/azure-security-disk-encryption-tsg.md).

### <a name="support"></a>Поддержка

Если в любой момент при изучении этой статьи вам потребуется дополнительная помощь, вы можете обратиться к экспертам по Azure на [форумах MSDN Azure и Stack Overflow](https://azure.microsoft.com/support/community/). Кроме того, можно зарегистрировать обращение в службу поддержки Azure. Перейдите на [сайт поддержки Azure](https://azure.microsoft.com/support/options/) и щелкните "Получить поддержку". Дополнительные сведения об использовании службы поддержки Azure см. в статье [Часто задаваемые вопросы о поддержке Microsoft Azure](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Дополнительная информация
См. дополнительные сведения о [расширениях и компонентах виртуальных машин Windows](features-windows.md).