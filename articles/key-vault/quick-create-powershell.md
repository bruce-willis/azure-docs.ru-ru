---
title: Краткое руководство Azure. Настройка и получение секрета из Key Vault с помощью PowerShell | Документация Майкрософт
description: ''
services: key-vault
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 1126f665-2e6c-4cca-897e-7d61842e8334
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.date: 08/28/2018
ms.author: barclayn
ms.openlocfilehash: 50448691fb136278cf7fdf3687ffb3b13fbb54ca
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/28/2018
ms.locfileid: "43122269"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-powershell"></a>Краткое руководство. Настройка и получение секрета из Azure Key Vault с помощью PowerShell

Azure Key Vault — это облачная служба, которая работает как защищенное хранилище секретов. Вы можете безопасно хранить ключи, пароли, сертификаты и другие секреты. Дополнительные сведения о хранилище ключей см. в статье [обзора](key-vault-overview.md). В этом кратком руководстве для создания хранилища ключей вы используете PowerShell. Затем вы сохраните секрет в только что созданном хранилище.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Чтобы установить и использовать PowerShell локально для работы с этим руководством, вам понадобится модуль Azure PowerShell 5.1.1 или более поздней версии. Чтобы узнать версию, выполните команду `Get-Module -ListAvailable AzureRM`. Если вам необходимо выполнить обновление, ознакомьтесь со статьей, посвященной [установке модуля Azure PowerShell](/powershell/azure/install-azurerm-ps). Если модуль PowerShell запущен локально, необходимо также выполнить командлет `Login-AzureRmAccount`, чтобы создать подключение к Azure.

```azurepowershell-interactive
Login-AzureRmAccount
```

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Создайте группу ресурсов Azure с помощью командлета [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Группа ресурсов — это логический контейнер, в котором происходит развертывание ресурсов Azure и управление ими. 

```azurepowershell-interactive
New-AzureRmResourceGroup -Name ContosoResourceGroup -Location EastUS
```

## <a name="create-a-key-vault"></a>создать хранилище ключей;

Далее вы создадите Key Vault. При выполнении этого шага вам понадобятся некоторые сведения:

Хотя мы используем Contoso KeyVault2 как имя для Key Vault в рамках работы с этим руководством, вы должны использовать уникальное имя.

- **Имя хранилища**: Contoso-Vault2.
- **Имя группы ресурсов**: ContosoResourceGroup.
- **Расположение**: восточная часть США.

```azurepowershell-interactive
New-AzureRmKeyVault -VaultName 'Contoso-Vault2' -ResourceGroupName 'ContosoResourceGroup' -Location 'East US'
```

В выходных данных командлета будут показаны свойства созданного Key Vault. Запишите значения двух указанных ниже свойств.

* **Имя хранилища** — в нашем примере это **Contoso-Vault2**. Вы будете использовать это имя для выполнения других командлетов хранилища ключей;
* **Универсальный код ресурса (URI) хранилища** — в нашем примере это https://contosokeyvault.vault.azure.net/. Необходимо, чтобы приложения, использующие ваше хранилище через REST API, использовали этот URI.

После создания хранилища ваша учетная запись Azure будет единственной учетной записью, с которой можно выполнять любые действия в новом хранилище.

![Выходные данные после выполнения команды, создающей хранилище ключей](./media/quick-create-powershell/output-after-creating-keyvault.png)

## <a name="adding-a-secret-to-key-vault"></a>Добавление секрета в Key Vault

Чтобы добавить секрет в хранилище, вам просто нужно выполнить несколько шагов. В этом случае вы добавите пароль, который может быть использован приложением. Пароль называется **ExamplePassword** и в нем хранится значение '''**Pa$$w0rd**'''.

Сначала преобразуйте значение Pa$$w0rd в безопасную строку, выполнив команду ниже:

```azurepowershell-interactive
$secretvalue = ConvertTo-SecureString 'Pa$$w0rd' -AsPlainText -Force
```

Затем введите команды PowerShell ниже, чтобы создать секрет в Key Vault с именем **ExamplePassword** и значением **Pa$$w0rd**:

```azurepowershell-interactive
$secret = Set-AzureKeyVaultSecret -VaultName 'ContosoKeyVault' -Name 'ExamplePassword' -SecretValue $secretvalue
```

Чтобы просмотреть содержащееся в секрете значение в виде обычного текста, введите следующее:

```azurepowershell-interactive
(Get-AzureKeyVaultSecret -vaultName "Contosokeyvault" -name "ExamplePassword").SecretValueText
```

Вы создали Key Vault, сохранили в нем секрет и извлекли его.

## <a name="clean-up-resources"></a>Очистка ресурсов

 Другие руководства в этой серии созданы на основе этого документа. Если вы планируете продолжить работу с другими краткими руководствами и статьями, эти ресурсы можно не удалять.

Вы можете удалить ставшие ненужными группу ресурсов, Key Vault и все связанные с ним ресурсы, выполнив команду [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup).

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name ContosoResourceGroup
```

## <a name="next-steps"></a>Дополнительная информация

В этом кратком руководстве вы создали Key Vault и сохранили в нем ключ ПО. Чтобы получить дополнительные сведения о Key Vault и о том, как вы можете использовать его в своих приложениях, ознакомьтесь с руководством по веб-приложениям, работающим с Key Vault.

> [!div class="nextstepaction"]
> Чтобы узнать, как считать секрет из Key Vault с помощью веб-приложения, используя удостоверения управляемой службы, перейдите к [следующему руководству](quick-create-net.md).
