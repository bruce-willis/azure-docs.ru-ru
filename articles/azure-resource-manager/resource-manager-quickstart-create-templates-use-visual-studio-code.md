---
title: Создание шаблона Azure Resource Manager c помощью Visual Studio Code | Документация Майкрософт
description: Используйте расширение "Средства Azure Resource Manager" для работы с шаблонами Resource Manager.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 07/17/2018
ms.topic: quickstart
ms.author: jgao
ms.openlocfilehash: bb0e3ee685d5a52a9f372e99d97f3608f67c17c0
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/20/2018
ms.locfileid: "39185403"
---
# <a name="quickstart-create-azure-resource-manager-templates-by-using-visual-studio-code"></a>Краткое руководство. Создание шаблона Azure Resource Manager c помощью Visual Studio Code

Дополнительные сведения о создании шаблонов Azure Resource Manager с помощью Visual Studio Code и расширения средств Azure Resource Manager. Шаблоны Resource Manager в Visual Studio Code можно создавать и без расширения. Но расширение предоставляет варианты автозаполнения, которые упрощают разработку шаблона. Основные понятия, связанные с развертыванием и управлением решений Azure, см. в [обзоре Azure Resource Manager](resource-group-overview.md).

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этой статьей необходимо иметь следующее.

- [Visual Studio Code](https://code.visualstudio.com/).
- Расширение средств Resource Manager. Чтобы их установить, выполните следующие действия.

    1. Откройте Visual Studio Code.
    2. Чтобы открыть панель расширений,нажмите клавиши **CTRL+SHIFT+X**
    3. Выполните поиск по фразе **Средства Azure Resource Manager**, а затем нажмите кнопку **Установить**.
    4. Чтобы завершить установку расширения, щелкните **Перезагрузить**.

## <a name="open-a-quickstart-template"></a>Открытие шаблона быстрого запуска

Чтобы не создавать шаблон с нуля, откройте его в [Шаблонах быстрого запуска Azure](https://azure.microsoft.com/resources/templates/). Шаблоны быстрого запуска Azure являются репозиторием для шаблонов Resource Manager.

Шаблон, используемый в этом кратком руководстве, называется [Create a standard storage account](https://azure.microsoft.com/resources/templates/101-storage-account-create/) (Создание стандартной учетной записи хранения). Шаблон определяет ресурс учетной записи службы хранилища Azure.

1. В Visual Studio Code выберите **Файл**>**Открыть файл**.
2. Скопируйте приведенный ниже URL-адрес и вставьте его в поле **Имя файла**.

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```
3. Чтобы открыть файл, выберите **Открыть**.
4. Выберите **Файл**>**Сохранить как**, чтобы сохранить файл в качестве **azuredeploy.json** на локальном компьютере.

## <a name="edit-the-template"></a>Изменение шаблона

Чтобы узнать, как изменить шаблон с помощью Visual Studio Code, в разделе выходных данных необходимо добавить еще один элемент.

1. Используя Visual Studio Code, добавьте один или несколько элементов выходных данных в экспортированный шаблон.

    ```json
    "storageUri": {
      "type": "string",
      "value": "[reference(variables('storageAccountName')).primaryEndpoints.blob]"
    }
    ```

    После завершения раздел выходных данных будет выглядеть следующим образом.

    ```json
    "outputs": {
      "storageAccountName": {
        "type": "string",
        "value": "[variables('storageAccountName')]"
      },
      "storageUri": {
        "type": "string",
        "value": "[reference(variables('storageAccountName')).primaryEndpoints.blob]"
      }
    }
    ```

    Чтобы проверить возможности расширения средств Resource Manager IntelliSence, после того, как код внутри Visual Studio Code был скопирован и вставлен, попробуйте еще раз ввести **значение** элемента.

    ![IntelliSense в шаблоне Resource Manager Visual Studio Code](./media/resource-manager-quickstart-create-templates-use-visual-studio-code/resource-manager-templates-visual-studio-code-intellisense.png)

2. Чтобы сохранить файл выберите **Файл**>**Сохранить**.

## <a name="deploy-the-template"></a>Развертывание шаблона

Существует множество методов по развертыванию шаблонов.  В этом кратком руководстве используется оболочка Cloud с портала Azure. Оболочка Cloud поддерживает как Azure CLI так и Azure PowerShell. В интерфейсе командной строки используются инструкции приведенные здесь.

1. Войдите на [портал Azure](https://portal.azure.com)
2. В правом верхнем углу выберите **Cloud Shell** (как показано на следующем рисунке).

    ![Cloud Shell на портале Azure](./media/resource-manager-quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell.png)

3. Чтобы переключится из CLI на PowerShell, щелкните стрелку вниз и выберите **Bash**.

    ![Cloud Shell CLI на портале Azure](./media/resource-manager-quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-choose-cli.png)
4. Чтобы перезапустить оболочку, выберите **Перезапуск**.
5. Выберите **Отправка и скачивание файлов**, а затем **Отправить**.

    ![Файл отправки Cloud Shell портале Azure](./media/resource-manager-quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-upload-file.png)
4. Выберите файл в кратком руководстве, сохраненный ранее. **azuredeploy.json** — имя по умолчанию.
5. Чтобы проверить, что файл загружен успешно, в командной строке Cloud Shell необходимо выполнить команду **ls**. Для проверки содержимого шаблона также можно использовать команду **cat**.

    ![Файл списка оболочки Cloud портала Azure](./media/resource-manager-quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-list-file.png)
6. Выполните следующую команду в командной строке оболочки Cloud.

    ```cli
    az group create --name <ResourceGroupName> --location <AzureLocation>

    az group deployment create --name <DeploymentName> --resource-group <ResourceGroupName> --template-file <TemplateFileName>
    ```
    Пример развертывания приведен на снимке экрана.

    ![Шаблон развертывания оболочки Cloud портала Azure](./media/resource-manager-quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-deploy-template.png)

    Значения, которые были использованы, приведены на снимке экрана.

    - **&lt;ResourceGroupName>**: myresourcegroup0709. Существуют два вида отображения параметра.  Убедитесь, что используется одинаковое значение.
    - **&lt;AzureLocation>**: eastus2
    - **&lt;DeployName>**: mydeployment0709
    - **&lt;TemplateFile>**: azuredeploy.json

    Как видно из результата изображенного на снимке экрана, имя учетной записи хранения это — *3tqebj3slyfyestandardsa*. 

7. Чтобы перечислить учетные записи хранения, которые были созданы, выполните следующую команду PowerShell.

    ```cli
    az storage account show --resource-group <ResourceGroupName> --name <StorageAccountName>
    ```

## <a name="clean-up-resources"></a>Очистка ресурсов

Если ресурсы Azure больше не нужны, их можно удалить. Для этого необходимо удалить группу ресурсов.

1. На портале Azure в меню слева выберите **Группа ресурсов**.
2. В поле **Filter by name** (Фильтровать по имени) введите имя группы ресурсов.
3. Выберите имя группы ресурсов.  В группе ресурсов должно появится шесть ресурсов.
4. В главном меню выберите **Удалить группу ресурсов**.

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве описано как создавать шаблоны с помощью Visual Studio Code и как их развертывать в оболочке Cloud портала Azure. В следующем руководстве описано, как разрабатывать шаблоны и использовать ссылки на шаблоны.

> [!div class="nextstepaction"]
> [Создание зашифрованной учетной записи хранения](./resource-manager-tutorial-create-encrypted-storage-accounts.md)
