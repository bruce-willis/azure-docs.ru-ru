---
title: Аутентификация заданий Azure Stream Analytics с помощью управляемых удостоверений для вывода данных в Azure Data Lake Storage 1-го поколения (предварительная версия)
description: ''
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 09/27/2018
ms.openlocfilehash: b79d529822f2b1acca9c8a120202b4ce4010949e
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/27/2018
ms.locfileid: "47413196"
---
# <a name="use-managed-identities-to-authenticate-azure-stream-analytics-jobs-to-azure-data-lake-storage-gen1-output-preview"></a>Аутентификация заданий Azure Stream Analytics с помощью управляемых удостоверений для вывода данных в Azure Data Lake Storage 1-го поколения (предварительная версия)

Azure Stream Analytics поддерживает аутентификацию с помощью управляемого удостоверения для вывода данных в Azure Data Lake Storage (ADLS) 1-го поколения. Удостоверение — это зарегистрированное в Azure Active Directory управляемое приложение, представляющее данное задание Stream Analytics и используемое для аутентификации в целевом ресурсе. Управляемые удостоверения устраняют ограничения пользовательских методов аутентификации, такие как необходимость повторной аутентификации из-за изменения пароля или после истечения срока действия пользовательских токенов (каждые 90 дней). Кроме того, управляемые удостоверения позволяют автоматизировать развертывания заданий Stream Analytics, которые выводят данные в Azure Data Lake Storage 1-го поколения.

Ознакомьтесь с записью блога о [восьми новых возможностях Azure Stream Analytics](https://azure.microsoft.com/en-us/blog/eight-new-features-in-azure-stream-analytics/), чтобы зарегистрироваться для использования этой предварительной версии и получить подробные сведения о новых возможностях.

В этой статье показано два способа включения управляемого удостоверения для задания Azure Stream Analytics, которое выводит данные в Azure Data Lake Storage 1-го поколения: через портал Azure и путем развертывания шаблона Azure Resource Manager.

## <a name="enable-managed-identity-with-azure-portal"></a>Включение управляемого удостоверения через портал Azure

1. Начните с создания задания Stream Analytics или открытия имеющегося задания на портале Azure. В строке меню, расположенной в левой части экрана, выберите **Managed Identity (preview)** (Управляемое удостоверение (предварительная версия)) в разделе **Настройка**.

   ![Настройка управляемого удостоверения Stream Analytics предварительной версии](./media/stream-analytics-managed-identities-adls/stream-analytics-managed-identity-preview.png)

2. В окне, которое отобразится справа, выберите **Use System-assigned Managed Identity (preview)** (Использовать назначаемое системой управляемое удостоверение (предварительная версия)). Нажмите кнопку **Сохранить**, чтобы создать субъект-службу для идентификации задания Stream Analytics в Azure Active Directory. Жизненным циклом нового удостоверения будет управлять Azure. При удалении задания Stream Analytics Azure автоматически удаляет связанное удостоверение (то есть субъект-службу).

   Если конфигурация сохраняется, идентификатор объекта (OID) субъекта-службы отображается в качестве идентификатора субъекта, как показано ниже:

   ![Идентификатор субъекта Stream Analytics](./media/stream-analytics-managed-identities-adls/stream-analytics-principal-id.png)
 
   Субъект-служба имеет то же имя, что и задание Stream Analytics. Например, если имя задания — **MyASAJob**, имя созданного субъекта-службы будет также **MyASAJob**.

3. В окне свойств выходных данных в приемнике выходных данных ADLS 1-го поколения щелкните раскрывающийся список режима аутентификации, а затем выберите **Managed Identity (preview)** (Управляемое удостоверение (предварительная версия)).

4. Укажите остальные свойства. Дополнительные сведения о создании выходных данных для ADLS см. в статье [Потоковая передача данных из большого двоичного объекта службы хранилища Azure в Data Lake Storage 1-го поколения с помощью Azure Stream Analytics](../data-lake-store/data-lake-store-stream-analytics.md). Закончив, нажмите кнопку **Сохранить**.

   ![Настройка Azure Data Lake Storage](./media/stream-analytics-managed-identities-adls/stream-analytics-configure-adls.png)
 
5. Перейдите к странице обзора ADLS 1-го поколения и щелкните **Обозреватель данных**.

   ![Обзор настройки Data Lake Storage](./media/stream-analytics-managed-identities-adls/stream-analytics-adls-overview.png)

6. На панели обозревателя данных выберите **Доступ** и щелкните **Добавить** в области доступа.

   ![Настройка доступа к Data Lake Storage](./media/stream-analytics-managed-identities-adls/stream-analytics-adls-access.png)

7. В текстовом поле в области **Select user or group** (Выбор пользователя или группы) укажите имя субъекта-службы. Не забывайте, что имя субъекта-службы совпадает с именем соответствующего задания Stream Analytics. Как только вы начнете вводить имя субъекта, оно отобразится под текстовым полем. Выберите необходимое имя субъекта-службы, а затем щелкните **Выбрать**.

   ![Выбор имени субъекта-службы](./media/stream-analytics-managed-identities-adls/stream-analytics-service-principal-name.png)
 
8. В области **Разрешения** выберите разрешения **Запись** и **Выполнить** и установите переключатель **К этой папке и всем вложенным элементам**. Нажмите кнопку **ОК**.

   ![Выбор разрешения](./media/stream-analytics-managed-identities-adls/stream-analytics-select-permissions.png)
 
9. Субъект-служба находится в списке **Назначенные разрешения** в области **Доступ**, как показано ниже. Теперь вы можете вернуться назад и приступить к заданию Stream Analytics.

   ![Список доступа](./media/stream-analytics-managed-identities-adls/stream-analytics-access-list.png)

   Дополнительные сведения о разрешениях файловой системы Azure Data Lake Storage 1-го поколения см. в статье [Контроль доступа в Azure Data Lake Storage 1-го поколения](../data-lake-store/data-lake-store-access-control.md).

## <a name="resource-manager-template-deployment"></a>Развертывание шаблонов Resource Manager

1. Вы можете создать ресурс *Microsoft.StreamAnalytics/streamingjobs* с использованием управляемого удостоверения, включив в раздел ресурсов шаблона Resource Manager следующее свойство:

   ```json
   "Identity": {
   "Type": "SystemAssigned",
   },
   ```

   Это свойство указывает Azure Resource Manager, что требуется создать удостоверение для задания Azure Stream Analytics и управлять им.

   **Пример задания**

   ```json
   { 
   "Name": "AsaJobWithIdentity", 
   "Type": "Microsoft.StreamAnalytics/streamingjobs", 
   "Location": "West US",
   "Identity": {
     "Type": "SystemAssigned", 
     }, 
   "properties": {
      "sku": {
       "name": "standard"
       },
   "outputs": [
         {
           "name": "string",
           "properties": {
             "datasource": {        
   "type": "Microsoft.DataLake/Accounts",
                "properties": {     
                  "accountName": “myDataLakeAccountName",
              "filePathPrefix": “cluster1/logs/{date}/{time}",
              "dateFormat": "YYYY/MM/DD",
              "timeFormat": "HH",
          "authenticationMode": "Msi"
          }
       }
   }
   ```
  
   **Пример ответа задания**

   ```json
   { 
   "Name": "mySAJob", 
   "Type": "Microsoft.StreamAnalytics/streamingjobs", 
   "Location": "West US",
   "Identity": {
   "Type": "SystemAssigned",
    "principalId": "GUID", 
    "tenantId": "GUID", 
   }, 
   "properties": {
           "sku": {
             "name": "standard"
           },
   }
   ```

   Запишите идентификатор субъекта (principalId) из ответа задания для предоставления доступа к необходимому ресурсу ADLS.

   **Идентификатор клиента** (tenantId) — это идентификатор клиента Azure Active Directory, где создан субъект-служба. Субъект-служба создается в клиенте Azure, который является доверенным для этой подписки.

   **Type** указывает тип управляемого удостоверения, как описано в типах управляемых удостоверений. Поддерживается только тип удостоверения, назначаемого системой.

2. Предоставьте доступ субъекту-службе с помощью PowerShell. Чтобы предоставить доступ субъекту-службе с помощью PowerShell, выполните следующую команду:

   ```powershell
   Set-AzureRmDataLakeStoreItemAclEntry -AccountName <accountName> -Path <Path> -AceType User -Id <PrinicpalId> -Permissions <Permissions>
   ```

   **PrincipalId** — идентификатор объекта субъекта-службы. Он отображается на экране портала после создания субъекта-службы. В случае создания задания путем развертывания шаблона Resource Manager идентификатор объекта отображается в свойстве идентификаторов в ответе задания.

   **Пример**

   ```powershell
   PS > Set-AzureRmDataLakeStoreItemAclEntry -AccountName "adlsmsidemo" -Path / -AceType
   User -Id 14c6fd67-d9f5-4680-a394-cd7df1f9bacf -Permissions WriteExecute
   ```

   Чтобы узнать больше о вышеуказанной команде PowerShell, ознакомьтесь с [этой документацией](https://docs.microsoft.com/powershell/module/azurerm.datalakestore/set-azurermdatalakestoreitemaclentry?view=azurermps-6.8.1&viewFallbackFrom=azurermps-4.2.0#optional-parameters).

## <a name="next-steps"></a>Дополнительная информация

* [Создание выходных данных Data Lake Store с помощью Stream Analytics](../data-lake-store/data-lake-store-stream-analytics.md)