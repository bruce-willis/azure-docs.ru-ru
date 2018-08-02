---
title: Хранение секретов в хранилище ключей в Azure DevTest Labs | Документы Майкрософт
description: Сведения о хранении секретов в Azure Key Vault и использовании их при создании виртуальной машины, формулы или среды.
services: devtest-lab
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2018
ms.author: spelluru
ms.openlocfilehash: d87c8a46459a9b4bf80bef895ec97e436d38e699
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/20/2018
ms.locfileid: "39186838"
---
# <a name="store-secrets-in-a-key-vault-in-azure-devtest-labs"></a>Хранение секретов в хранилище ключей в Azure DevTest Labs
Вам может потребоваться ввести сложный секрет при использовании Azure DevTest Labs: пароль для виртуальной машины Windows, открытый ключ SSH для виртуальной машины Linux или личный маркер доступа для клонирования репозитория Git через артефакт. Секреты обычно имеют большую длину и содержат случайные знаки. Поэтому их ввод может вызвать затруднения и неудобства, особенно если вы используете один секрет несколько раз.

Чтобы устранить эту проблему и обеспечить безопасность секретов, DevTest Labs поддерживает хранение секретов в [Azure Key Vault](../key-vault/key-vault-overview.md). Когда пользователь впервые сохраняет секрет, служба DevTest Labs автоматически создает хранилище ключей в той же группе ресурсов, которая содержит лабораторию, и сохраняет секрет в нем. Служба DevTest Labs создает отдельное хранилище ключей для каждого пользователя. 

## <a name="save-a-secret-in-azure-key-vault"></a>Сохранение секрета в Azure Key Vault
Чтобы сохранить секрет в Azure Key Vault, сделайте следующее:

1. В меню слева выберите **Мои секреты**.
2. Введите **имя** секрета. Это имя отображается в раскрывающемся списке при создании виртуальной машины, формулы или среды. 
3. Введите секрет в качестве **значения**.

    ![Сохранение секрета](media/devtest-lab-store-secrets-in-key-vault/store-secret.png)

## <a name="use-a-secret-from-azure-key-vault"></a>Использование секрета из Azure Key Vault
Если вам требуется ввести секрет для создания виртуальной машины, формулы или среды, можно сделать это вручную или выбрать сохраненный секрет из хранилища ключей. Чтобы использовать секрет из хранилища ключей, сделайте следующее:

1. Выберите **Использовать сохраненный секрет**. 
2. Выберите секрет в раскрывающемся списке **Выберите секрет**. 

    ![Использование секрета в виртуальной машине](media/devtest-lab-store-secrets-in-key-vault/secret-store-pick-a-secret.png)

## <a name="use-a-secret-in-an-azure-resource-manager-template"></a>Использование секрета в шаблоне Azure Resource Manager
Вы можете указать имя секрета в шаблоне Azure Resource Manager, используемом для создания виртуальной машины, как показано в следующем примере:

![Использование секрета в формуле или среде](media/devtest-lab-store-secrets-in-key-vault/secret-store-arm-template.png)

## <a name="next-steps"></a>Дополнительная информация

- [Создание виртуальной машины с помощью секрета](devtest-lab-add-vm.md) 
- [Создание формулы с помощью секрета](devtest-lab-manage-formulas.md)
- [Создание среды с помощью секрета](devtest-lab-create-environment-from-arm.md)
