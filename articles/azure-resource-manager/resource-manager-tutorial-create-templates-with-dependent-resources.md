---
title: Создание шаблонов Azure Resource Manager с зависимыми ресурсами | Документация Майкрософт
description: Узнайте, как создавать шаблон Azure Resource Manager с несколькими ресурсами, а также, как развертывать его, используя портал Azure
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 08/27/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 7509ed46ba07cd8250f82f8eb258d18e3f4a1ee6
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/27/2018
ms.locfileid: "43107111"
---
# <a name="tutorial-create-azure-resource-manager-templates-with-dependent-resources"></a>Руководство. Создание шаблонов Azure Resource Manager с зависимыми ресурсами

Узнайте, как создать шаблон Azure Resource Manager для развертывания нескольких ресурсов.  После создания шаблона можно развернуть шаблон с помощью Cloud Shell с портала Azure.

Некоторые ресурсы невозможно развернуть до тех пор, пока не будет существовать другой ресурс. Например, невозможно создать виртуальную машину, пока не существуют ее учетная запись хранения и сетевой интерфейс. Эта связь определяется путем пометки зависимости одного ресурса от других. Диспетчер ресурсов оценивает зависимости между ресурсами и развертывает эти ресурсы согласно установленным зависимостям. Если ресурсы не зависят друг от друга, диспетчер ресурсов развертывает их параллельно. Дополнительные сведения см. в статье [Define the order for deploying resources in Azure Resource Manager Templates](./resource-group-define-dependencies.md) (Определение порядка развертывания ресурсов в шаблонах Azure Resource Manager).

> [!div class="checklist"]
> * Открытие шаблона быстрого запуска
> * Обзор шаблона
> * Развертывание шаблона
> * Очистка ресурсов

Инструкции в этом руководстве описывают создание виртуальной машины, виртуальной сети и другие зависимые ресурсы. 

## <a name="prerequisites"></a>Предварительные требования

Для работы с этой статьей необходимо иметь следующее.

* [Visual Studio Code](https://code.visualstudio.com/).
* Расширение средств Resource Manager.  Как установить расширение см. в разделе [Quickstart: create Azure Resource Manager templates by using Visual Studio Code](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites) (Создание шаблонов Azure Resource Manager с помощью Visual Studio Code)

## <a name="open-a-quickstart-template"></a>Открытие шаблона быстрого запуска

Шаблоны быстрого запуска Azure являются репозиторием для шаблонов Resource Manager. Вместо создания шаблона с нуля можно найти пример шаблона и настроить его. Шаблон, используемый в этом руководстве, называется [Deploy a simple Windows VM](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/) (Развертывание простой виртуальной машины Windows).

1. В Visual Studio Code выберите **Файл**>**Открыть файл**.
2. Скопируйте приведенный ниже URL-адрес и вставьте его в поле **Имя файла**.

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```
3. Чтобы открыть файл, выберите **Открыть**.
4. Выберите **Файл**>**Сохранить как**, чтобы сохранить файл на локальный компьютер с именем **azuredeploy.json**.

## <a name="explore-the-template"></a>Обзор шаблона

Рассматривая шаблон в этом разделе, постарайтесь ответить на следующие вопросы:

- Сколько ресурсов Azure определено в этом шаблоне?
- Одним из ресурсов является учетная запись хранения Azure.  Похоже ли это определение на использованное в предыдущем руководстве?
- Можете ли вы найти справочники по шаблонам для ресурсов, определенных в этом шаблоне?
- Можете ли вы найти зависимости ресурсов?

1. В Visual Studio Code сверните элементы, пока не появятся элементы только первого уровня и элементы второго уровня внутри **ресурсов**.

    ![Шаблоны Azure Resource Manager в Visual Studio Code](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code.png)

    Шаблоном определено пять ресурсов.
2. Разверните первый ресурс. Это учетная запись хранения. Определение должно быть идентичным определению, используемому в начале предыдущего руководства.

    ![Определение учетной записи хранения для шаблонов Azure Resource Manager в Visual Studio Code](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-storage-account-definition.png)

3. Разверните второй ресурс. Тип ресурса — **Microsoft.Network/publicIPAddresses**. Чтобы найти справочник по шаблону, перейдите по [этой ссылке](https://docs.microsoft.com/azure/templates/) и в поле **Фильтровать по названию** введите **общедоступный IP-адрес** или **общедоступные IP-адреса**. Сравните определение ресурса с определением в справочнике по шаблону.

    ![Определение общедоступного IP-адреса для шаблонов Azure Resource Manager в Visual Studio Code](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-public-ip-address-definition.png)
4. Повторите последний шаг, чтобы найти справочники по шаблонам для других ресурсов, определенных в этом шаблоне.  Сравните определения ресурсов со справочными экземплярами.
5. Разверните четвертый ресурс:

    ![Шаблоны Azure Resource Manager в Visual Studio Code для элемента dependsOn](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code-dependson.png)

    Элемент dependsOn позволяет определить один ресурс как зависимый от одного или нескольких ресурсов. В этом примере ресурс является networkInterface.  Он зависит от двух других ресурсов:

    * publicIpAddress;
    * virtualNetwork.

6. Разверните пятый ресурс. Этот ресурс — это виртуальная машина. Он зависит от двух других ресурсов:

    * storageAccount
    * networkInterface.

На следующей схеме приведены ресурсы и информация о зависимости для этого шаблона.

![Схема зависимости шаблонов Azure Resource Manager в Visual Studio Code](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code-dependency-diagram.png)

Путем указания зависимостей диспетчер ресурсов позволяет эффективно развертывать решение. Развертывание учетной записи хранения, общедоступного IP-адреса и виртуальной сети происходит в параллельном режиме, так как они не имеют зависимостей. После развертывания общедоступного IP-адреса и виртуальной сети создается сетевой интерфейс. Когда все ресурсы развернуты, диспетчер ресурсов развертывает виртуальную машину.

## <a name="deploy-the-template"></a>Развертывание шаблона

Существует множество методов по развертыванию шаблонов.  В этом руководстве используется Cloud Shell на портале Azure.

1. Войдите на [портал Azure](https://portal.azure.com)
2. В правом верхнем углу выберите **Cloud Shell** (как показано на следующем рисунке).

    ![Cloud Shell на портале Azure](./media/resource-manager-tutorial-create-templates-with-dependent-resources/azure-portal-cloud-shell.png)
3. Выберите **PowerShell** в верхнем левом углу Cloud Shell.  В этом руководстве используется PowerShell.
4. Выберите **Перезапустить**
5. Выберите **Отправить файл** из Cloud Shell.

    ![Файл отправки Cloud Shell портале Azure](./media/resource-manager-tutorial-create-templates-with-dependent-resources/azure-portal-cloud-shell-upload-file.png)
6. Выберите файл, сохраненный ранее в этом руководстве. **azuredeploy.json** — имя по умолчанию.  Если файл с тем же именем уже существует, то он будет перезаписан без уведомления.
7. Чтобы убедиться, что файл загружен успешно, выполните в командной строке Cloud Shell следующую команду. 

    ```shell
    ls
    ```

    ![Файл списка оболочки Cloud портала Azure](./media/resource-manager-tutorial-create-templates-with-dependent-resources/azure-portal-cloud-shell-list-file.png)

    Имя файла, показанное на снимке экрана, — azuredeploy.json.

8. Чтобы проверить содержимое JSON-файла, выполните в командной строке Cloud Shell следующую команду.

    ```shell
    cat azuredeploy.json
    ```
9. Выполните следующие команды PowerShell в командной строке Cloud Shell.

    ```powershell
    $resourceGroupName = "<Enter the resource group name>"
    $location = "<Enter the Azure location>"
    $vmAdmin = "<Enter the admin username>"
    $vmPassword = "<Enter the password>"
    $dnsLabelPrefix = "<Enter the prefix>"

    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
    $vmPW = ConvertTo-SecureString -String $vmPassword -AsPlainText -Force
    New-AzureRmResourceGroupDeployment -Name mydeployment0710 -ResourceGroupName $resourceGroupName `
        -TemplateFile azuredeploy.json -adminUsername $vmAdmin -adminPassword $vmPW `
        -dnsLabelPrefix $dnsLabelPrefix
    ```
    Пример развертывания приведен на снимке экрана.

    ![Шаблон развертывания оболочки Cloud портала Azure](./media/resource-manager-tutorial-create-templates-with-dependent-resources/azure-portal-cloud-shell-deploy-template.png)

    Значения, которые были использованы, приведены на снимке экрана.

    * **$resourceGroupName**: myresourcegroup0710. 
    * **$location**: eastus2
    * **&lt;DeployName>**: mydeployment0710
    * **&lt;TemplateFile>**: azuredeploy.json
    * **Параметры шаблона**:

        * **adminUsername**: JohnDole
        * **adminPassword**: Pass@word123
        * **dnsLabelPrefix**: myvm0710

10. Для отображения вновь созданной виртуальной машины выполните следующую команду PowerShell.

    ```powershell
    Get-AzureRmVM -Name SimpleWinVM -ResourceGroupName <ResourceGroupName>
    ```

    Имя виртуальной машины внутри шаблона жестко закодировано как **SimpleWinVM**.

## <a name="clean-up-resources"></a>Очистка ресурсов

Если ресурсы Azure больше не нужны, их можно удалить. Для этого необходимо удалить группу ресурсов.

1. На портале Azure в меню слева выберите **Группа ресурсов**.
2. В поле **Filter by name** (Фильтровать по имени) введите имя группы ресурсов.
3. Выберите имя группы ресурсов.  В группе ресурсов должно появится шесть ресурсов.
4. В главном меню выберите **Удалить группу ресурсов**.

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве был разработан и развернут шаблон для создания виртуальной машины, виртуальной сети и зависимых ресурсов. Дополнительные сведения о шаблонах см. в статье [Understand the structure and syntax of Azure Resource Manager Templates](./resource-group-authoring-templates.md) (Описание структуры и синтаксиса шаблонов Azure Resource Manager).