---
title: Управление сервером конфигурации для аварийного восстановления VMware с помощью Azure Site Recovery | Документация Майкрософт
description: В этой статье описывается, как управлять сервером конфигурации для аварийного восстановления VMware в Azure с помощью Azure Site Recovery.
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: raynew
ms.openlocfilehash: df5b2ecce2a5c9d7c263ee0acc3a49b859b93f7f
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/30/2018
ms.locfileid: "39346126"
---
# <a name="manage-the-configuration-server-for-vmware-vms"></a>Управление сервером конфигурации для виртуальных машин VMware

При использовании [Azure Site Recovery](site-recovery-overview.md) для аварийного восстановления виртуальных машин VMware и физических серверов в Azure настраивается локальный сервер конфигурации. Сервер конфигурации используется для управления обменом данными между локальной средой VMware и Azure, а также репликацией данных. В этой статье перечислены распространенные задачи управления сервером конфигурации после его развертывания.



## <a name="modify-vmware-settings"></a>Изменение параметров VMware

Доступ к серверу конфигурации можно получить следующими способами:
    - Войдя на виртуальную машину, на которой он развернут, и запустив диспетчер конфигурации Azure Site Recovery, щелкнув соответствующий ярлык на рабочем столе.
    - Кроме того, к нему можно получить доступ по адресу **https://*ConfigurationServerName*/:44315/**. Для входа необходимы учетные данные администратора.
   
### <a name="modify-vmware-server-settings"></a>Изменение параметров сервера VMware

1. Чтобы привязать другой сервер VMware к серверу конфигурации, выберите **Добавление сервера vCenter Server или ESXi vSphere** после входа.
2. Введите сведения и нажмите кнопку **ОК**.


### <a name="modify-credentials-for-automatic-discovery"></a>Изменение учетных данных для автоматического обнаружения

1. Чтобы обновить учетные данные, используемые для подключения к серверу VMware для автоматического обнаружения виртуальных машин VMware, выберите **Изменить** после входа.
2. Введите новые учетные данные и нажмите кнопку **ОК**.

    ![Изменение VMware](./media/vmware-azure-manage-configuration-server/modify-vmware-server.png)


## <a name="modify-credentials-for-mobility-service-installation"></a>Изменение учетных данных для установки службы Mobility Service

Измените учетные данные, используемые для автоматической установки службы Mobility Service на виртуальных машинах VMware, предназначенных для репликации.

1. После входа выберите **Управление учетными данными виртуальной машины**
2. Введите новые учетные данные и нажмите кнопку **ОК**.

    ![Изменение учетных данных службы Mobility Service](./media/vmware-azure-manage-configuration-server/modify-mobility-credentials.png)

## <a name="modify-proxy-settings"></a>Изменение параметров прокси-сервера

Измените параметры прокси-сервера, используемые на компьютере сервера конфигурации для доступа к Azure через Интернет. При наличии компьютера сервера обработки помимо сервера обработки по умолчанию, выполняемого на сервере конфигурации, измените параметры на обоих компьютерах.

1. После входа на сервер конфигурации выберите **Управление взаимодействием**.
2. Обновление значения прокси-сервера. Затем выберите **Сохранить**, чтобы обновить параметры.

## <a name="add-a-network-adapter"></a>Добавление сетевого адаптера

Шаблон в формате OVF развертывает виртуальную машину сервера конфигурации с одним сетевым адаптером.

- Вы можете [добавить дополнительный сетевой адаптер в эту виртуальную машину](vmware-azure-deploy-configuration-server.md#add-an-additional-adapter), но это необходимо сделать до регистрации сервера конфигурации в хранилище.
- Если необходимо добавить адаптер после того, как сервер конфигурации был зарегистрирован в хранилище, необходимо добавить адаптер в свойствах виртуальной машины. Затем сервер необходимо зарегистрировать в хранилище.


## <a name="reregister-a-configuration-server-in-the-same-vault"></a>Повторная регистрация сервера конфигурации в том же хранилище

Можно повторно зарегистрировать сервер конфигурации в том же хранилище, если необходимо. При наличии дополнительного компьютера сервера обработки помимо сервера обработки по умолчанию, выполняемого на сервере конфигурации, следует повторно зарегистрировать оба эти компьютера.


  1. В хранилище выберите **Управление** > **Site Recovery Infrastructure (Инфраструктура Site Recovery)** > **Серверы конфигурации**.
  2. В списке **Серверы** выберите **Скачать ключ регистрации**, чтобы загрузить файл учетных данных хранилища.
  3. Войдите на компьютер сервера конфигурации.
  4. В папке **%ProgramData%\ASR\home\svsystems\bin** запустите файл **cspsconfigtool.exe**.
  5. На вкладке **Vault Registration** (Регистрация в хранилище) выберите **Обзор** и найдите скачанный файл с учетными данными хранилища.
  6. При необходимости укажите данные прокси-сервера. Выберите **Зарегистрировать**.
  7. Откройте командную строку PowerShell с правами администратора и выполните следующую команду:
   ```
      $pwd = ConvertTo-SecureString -String MyProxyUserPassword
      Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber – ProxyUserName domain\username -ProxyPassword $pwd
   ```

      >[!NOTE] 
      >Чтобы **извлечь новейшие сертификаты** с сервера конфигурации для горизонтального масштабирования сервера обработки, выполните команду *“<Installation Drive\Microsoft Azure Site Recovery\agent\cdpcli.exe>" --registermt*.

  8. И, наконец, перезапустите службу OBEngine, выполнив приведенную ниже команду.
  ```
          net stop obengine
          net start obengine

## Upgrade the configuration server

You run update rollups to update the configuration server. Updates can be applied for up to N-4 versions. For example:

- If you run 9.7, 9.8, 9.9, or 9.10, you can upgrade directly to 9.11.
- If you run 9.6 or earlier and you want to upgrade to 9.11, you must first upgrade to version 9.7. before 9.11.

Links to update rollups for upgrading to all versions of the configuration server are available in the [wiki updates page](https://social.technet.microsoft.com/wiki/contents/articles/38544.azure-site-recovery-service-updates.aspx).

Upgrade the server as follows:

1. In the vault, go to **Manage** > **Site Recovery Infrastructure** > **Configuration Servers**.
2. If an update is available, a link appears in the **Agent Version** > column.
    ![Update](./media/vmware-azure-manage-configuration-server/update2.png)
3. Download the update installer file to the configuration server.

    ![Update](./media/vmware-azure-manage-configuration-server/update1.png)

4. Double-click to run the installer.
5. The installer detects the current version running on the machine. Click **Yes** to start the upgrade.
6. When the upgrade completes the server configuration validates.

    ![Update](./media/vmware-azure-manage-configuration-server/update3.png)
    
7. Click **Finish** to close the installer.

## Delete or unregister a configuration server

1. [Disable protection](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) for all VMs under the configuration server.
2. [Disassociate](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) and [delete](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) all replication policies from the configuration server.
3. [Delete](vmware-azure-manage-vcenter.md#delete-a-vcenter-server) all vCenter servers/vSphere hosts that are associated with the configuration server.
4. In the vault, open **Site Recovery Infrastructure** > **Configuration Servers**.
5. Select the configuration server that you want to remove. Then, on the **Details** page, select **Delete**.

    ![Delete configuration server](./media/vmware-azure-manage-configuration-server/delete-configuration-server.png)
   

### Delete with PowerShell

You can optionally delete the configuration server by using PowerShell.

1. [Install](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.4.0) the Azure PowerShell module.
2. Sign in to your Azure account by using this command:
    
    `Connect-AzureRmAccount`
3. Select the vault subscription.

     `Get-AzureRmSubscription –SubscriptionName <your subscription name> | Select-AzureRmSubscription`
3.  Set the vault context.
    
    ```
    $vault = Get-AzureRmRecoveryServicesVault -Name <name of your vault> Set-AzureRmSiteRecoveryVaultSettings -ARSVault $vault
    ```
4. Retrieve the configuration server.

    `$fabric = Get-AzureRmSiteRecoveryFabric -FriendlyName <name of your configuration server>`
6. Delete the configuration server.

    `Remove-AzureRmSiteRecoveryFabric -Fabric $fabric [-Force] `

> [!NOTE]
> You can use the **-Force** option in Remove-AzureRmSiteRecoveryFabric for forced deletion of the configuration server.
 
## Generate configuration server Passphrase

1. Sign in to your configuration server, and then open a command prompt window as an administrator.
2. To change the directory to the bin folder, execute the command **cd %ProgramData%\ASR\home\svsystems\bin**
3. To generate the passphrase file, execute **genpassphrase.exe -v > MobSvc.passphrase**.
4. Your passphrase will be stored in the file located at **%ProgramData%\ASR\home\svsystems\bin\MobSvc.passphrase**.

## Renew SSL certificates

The configuration server has an inbuilt web server, which orchestrates activities of the Mobility Service, process servers, and master target servers connected to it. The web server uses an SSL certificate to authenticate clients. The certificate expires after three years and can be renewed at any time.

### Check expiry

For configuration server deployments before May 2016, certificate expiry was set to one year. If you have a certificate that is going to expire, the following occurs:

- When the expiry date is two months or less, the service starts sending notifications in the portal, and by email (if you subscribed to Site Recovery notifications).
- A notification banner appears on the vault resource page. For more information, select the banner.
- If you see an **Upgrade Now** button, it indicates that some components in your environment haven't been upgraded to 9.4.xxxx.x or higher versions. Upgrade the components before you renew the certificate. You can't renew on older versions.

### Renew the certificate

1. In the vault, open **Site Recovery Infrastructure** > **Configuration Server**. Select the required configuration server.
2. The expiry date appears under **Configuration Server health**.
3. Select **Renew Certificates**. 


## Next steps

Review the tutorials for setting up disaster recovery of [VMware VMs](vmware-azure-tutorial.md) to Azure.
