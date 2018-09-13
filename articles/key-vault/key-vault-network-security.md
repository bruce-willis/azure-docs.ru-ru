---
ms.assetid: ''
title: Настройка брандмауэров и виртуальных сетей Azure Key Vault
description: Пошаговые инструкции для настройки брандмауэров и виртуальных сетей Key Vault.
services: key-vault
author: amitbapat
manager: mbaldwin
ms.service: key-vault
ms.topic: conceptual
ms.workload: identity
ms.date: 08/31/2018
ms.author: ambapat
ms.openlocfilehash: 6315434c1e8acc82e02f5c9e5ae8ab2d1cacc887
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/10/2018
ms.locfileid: "44302059"
---
# <a name="configure-azure-key-vault-firewalls-and-virtual-networks"></a>Настройка брандмауэров и виртуальных сетей Azure Key Vault

В этом руководстве описываются пошаговые инструкции по настройке брандмауэров и виртуальных сетей Key Vault для ограничения доступа к хранилищу ключей. [Конечные точки службы виртуальной сети для Key Vault](key-vault-overview-vnet-service-endpoints.md) позволяют предоставить доступ к хранилищу ключей только для указанной виртуальной сети или набора диапазонов адресов IPv4 (протокол IP версии 4).

> [!IMPORTANT]
> Когда правила брандмауэра и виртуальной сети начнут действовать, все операции [плоскости данных](../key-vault/key-vault-secure-your-key-vault.md#data-plane-access-control) Key Vault будут выполняться, только если запросы вызывающей стороны поступают из разрешенных виртуальных сетей или диапазонов IPv4-адресов. Это также относится к получению доступа к хранилищу ключей с портала Azure. Хотя пользователь может перейти в хранилище ключей с портала Azure, он не сможет перечислять ключи, секреты и сертификаты, если их клиентский компьютер не находится в разрешенном списке. Это также влияет на выбор хранилища ключей другими службами Azure. Пользователи могут просматривать список хранилищ ключей, но не список ключей, если правила брандмауэра запрещают их клиентский компьютер.

## <a name="azure-portal"></a>Портал Azure

1. Перейдите к хранилищу ключей, которое нужно защитить.
2. Щелкните **Брандмауэры и виртуальные сети**.
3. Щелкните **Выбранные сети** в разделе **Разрешить доступ из**.
4. Чтобы добавить имеющиеся виртуальные сети в правила брандмауэров и виртуальных сетей, щелкните **+ Добавить существующие виртуальные сети**.
5. В новой открывшейся колонке выберите подписку, виртуальные сети и подсети, которым нужно предоставить доступ к этому хранилищу ключей. Если у виртуальных сетей и подсетей, которые вы выбрали, нет включенных конечных точек службы, отобразится следующее сообщение: The following networks don't have service endpoints enabled (У следующих сетей нет включенных конечных точек службы). Щелкните **Включить**, убедившись, что вам нужно включить конечные точки службы для перечисленных виртуальных сетей и подсетей. Эта настройка вступит в силу в течение 15 минут.
6. Вы также можете добавить новые виртуальные сети и подсети, а потом включить конечные точки службы для них, щелкнув **+ Добавить новую виртуальную сеть** и следуя запросам.
7. В разделе **IP-сети** можно добавить диапазоны IPv4-адресов, введя их с использованием [нотации CIDR](https://tools.ietf.org/html/rfc4632), или указать отдельные IP-адреса.
8. Выберите команду **Сохранить**.

## <a name="azure-cli-20"></a>Azure CLI 2.0

1. [Установите Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) и [выполните вход](https://docs.microsoft.com/cli/azure/authenticate-azure-cli).

2. Выведите список доступных правил виртуальных сетей. Если вы не настроили какие-либо правила для этого хранилища ключей, список будет пустой.
```azurecli
az keyvault network-rule list --resource-group myresourcegroup --name mykeyvault
```

3. Включите конечную точку службы для Key Vault в имеющейся виртуальной сети и подсети.
```azurecli
az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.KeyVault"
```

4. Добавьте правило сети для виртуальной сети и подсети.
```azurecli
subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
az keyvault network-rule add --resource-group "demo9311" --name "demo9311premium" --subnet $subnetid
```

5. Добавьте диапазон IP-адресов, с которых нужно разрешить поступление трафика.
```azurecli
az keyvault network-rule add --resource-group "myresourcegroup" --name "mykeyvault" --ip-address "191.10.18.0/24"
```

6. Если это хранилище ключей должно быть доступно для каких-либо доверенных служб, задайте параметр bypass для AzureServices.
```azurecli
az keyvault update --resource-group "myresourcegroup" --name "mykeyvault" --bypass AzureServices
```

7. Последним и важным шагом является включение правил сети с помощью установки действия Deny (Запретить) по умолчанию.
```azurecli
az keyvault update --resource-group "myresourcegroup" --name "mekeyvault" --default-action Deny
```

## <a name="azure-powershell"></a>Azure PowerShell

1. Установите последнюю версию [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) и [выполните вход](https://docs.microsoft.com/powershell/azure/authenticate-azureps).

2. Выведите список доступных правил виртуальных сетей. Если вы не настроили какие-либо правила для этого хранилища ключей, список будет пустой.
```PowerShell
(Get-AzureRmKeyVault -VaultName "mykeyvault").NetworkAcls
```

3. Включите конечную точку службы для Key Vault в имеющейся виртуальной сети и подсети.
```PowerShell
Get-AzureRmVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzureRmVirtualNetworkSubnetConfig -Name "mysubnet" -AddressPrefix "10.1.1.0/24" -ServiceEndpoint "Microsoft.KeyVault" | Set-AzureRmVirtualNetwork
```

4. Добавьте правило сети для виртуальной сети и подсети.
```PowerShell
$subnet = Get-AzureRmVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzureRmVirtualNetworkSubnetConfig -Name "mysubnet"
Add-AzureRmKeyVaultNetworkRule -VaultName "mykeyvault" -VirtualNetworkResourceId $subnet.Id
```

5. Добавьте диапазон IP-адресов, с которых нужно разрешить поступление трафика.
```PowerShell
Add-AzureRmKeyVaultNetworkRule -VaultName "mykeyvault" -IpAddressRange "16.17.18.0/24"
```

6. Если это хранилище ключей должно быть доступно для каких-либо доверенных служб, задайте параметр bypass для AzureServices.
```PowerShell
Update-AzureRmKeyVaultNetworkRuleSet -VaultName "mykeyvault" -Bypass AzureServices
```

7. Последним и важным шагом является включение правил сети с помощью установки действия Deny (Запретить) по умолчанию.
```PowerShell
Update-AzureRmKeyVaultNetworkRuleSet -VaultName "mykeyvault" -DefaultAction Deny
```

## <a name="references"></a>Ссылки

* Команда Azure CLI 2.0: [az keyvault network-rule](https://docs.microsoft.com/cli/azure/keyvault/network-rule?view=azure-cli-latest).
* Командлеты Azure PowerShell: [Get-AzureRmKeyVault](https://docs.microsoft.com/powershell/module/azurerm.keyvault/get-azurermkeyvault), [Add-AzureRmKeyVaultNetworkRule](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Add-AzureRmKeyVaultNetworkRule), [Remove-AzureRmKeyVaultNetworkRule](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Remove-AzureRmKeyVaultNetworkRule), [Update-AzureRmKeyVaultNetworkRuleSet](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Update-AzureRmKeyVaultNetworkRuleSet).

## <a name="next-steps"></a>Дополнительная информация

* [Конечные точки служб виртуальной сети для Key Vault](key-vault-overview-vnet-service-endpoints.md)
* [Защита хранилища ключей](key-vault-secure-your-key-vault.md)