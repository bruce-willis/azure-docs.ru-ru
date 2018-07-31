---
title: Создание шаблона Azure Resource Manager для развертывания зашифрованной учетной записи хранения | Документация Майкрософт
description: Используйте Visual Studio Code, чтобы создать шаблон для развертывания зашифрованной учетной записи хранения.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 07/20/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 7c78636a210ae90c5bfe1d0bfd35e4e05633f5cd
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/20/2018
ms.locfileid: "39188205"
---
# <a name="tutorial-create-an-azure-resource-manager-template-for-deploying-an-encrypted-storage-account"></a>Руководство. Создание шаблона Azure Resource Manager для развертывания зашифрованной учетной записи хранения

Узнайте, как найти сведения для заполнения шаблона Azure Resource Manager.

В этом руководстве для создания учетной записи хранения Azure используется базовый шаблон из шаблонов быстрого запуска Azure.  С помощью шаблонов справочной документации можно настроить базовый шаблон, чтобы создать зашифрованную учетную запись хранения.

В рамках этого руководства рассматриваются следующие задачи:

> [!div class="checklist"]
> * Открытие шаблона быстрого запуска
> * Общие сведения о формате шаблона
> * Использование параметров в шаблоне
> * Использование переменных в шаблоне
> * Изменение шаблона
> * Развертывание шаблона

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этой статьей необходимо иметь следующее.

* [Visual Studio Code](https://code.visualstudio.com/).
* Расширение средств Resource Manager. Для установки см. раздел [Создание шаблона Azure Resource Manager c помощью Visual Studio Code](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites).

## <a name="open-a-quickstart-template"></a>Открытие шаблона быстрого запуска

Шаблон, используемый в этом кратком руководстве, называется [Create a standard storage account](https://azure.microsoft.com/resources/templates/101-storage-account-create/) (Создание стандартной учетной записи хранения). Шаблон определяет ресурс учетной записи службы хранилища Azure.

1. В Visual Studio Code выберите **Файл**>**Открыть файл**.
2. Скопируйте приведенный ниже URL-адрес и вставьте его в поле **Имя файла**.

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```
3. Чтобы открыть файл, выберите **Открыть**.
4. Выберите **Файл**>**Сохранить как**, чтобы сохранить файл в качестве **azuredeploy.json** на локальном компьютере.

## <a name="understand-the-format"></a>Общие сведения о формате

Из VS Code сверните шаблон до корневого уровня. Имеется простейшая структура со следующими элементами.

![Простейшая структура шаблона Resource Manager](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-simplest-structure.png)

* Параметр **$schema** указывает расположение файла схемы JSON, который описывает версию языка шаблона.
* Параметр **contentVersion** задает этому элементу любое значение, чтобы документировать важные изменения в шаблоне.
* Параметр **parameters** указывает значения, которые предоставляются при выполнении развертывания для настройки развертывания ресурсов.
* Параметр **variables** указывает значения, используемые в виде фрагментов JSON в шаблоне для упрощения выражений на языке шаблона.
* Параметр **resources** указывает типы ресурсов, которые развертываются или обновляются в группе ресурсов.
* Параметр **outputs** указывает значения, возвращаемые после развертывания.

## <a name="use-parameters-in-template"></a>Использование параметров в шаблоне

Параметры позволяют настраивать развертывание путем предоставления значений, предназначенных для конкретной среды. Параметры, определенные в шаблоне, используются при задании значений для учетной записи хранения.

![Параметры шаблона Azure Resource Manager](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-parameters.png)

В этом шаблоне определены два параметра. Обратите внимание, что в location.defaultValue используется функция шаблона.

```json
"defaultValue": "[resourceGroup().location]",
```

Функция resourceGroup() возвращает объект, представляющий текущую группу ресурсов. Полный список функций шаблонов см. в статье [Функции шаблонов диспетчера ресурсов Azure](./resource-group-template-functions.md).

Использование параметров, определенных в шаблоне.

```json
"location": "[parameters('location')]",
"name": "[parameters('storageAccountType')]"
```

## <a name="use-variables-in-template"></a>Использование переменных в шаблоне

Переменные позволяют создавать значения, которые могут использоваться в разных частях шаблона. Переменные помогают уменьшить сложность шаблонов.

![Переменные шаблона Resource Manager](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-variables.png)

Этот шаблон определяет одну переменную *storageAccountName*. В определении используются две функции шаблона.

- Функция **concat()** объединяет строки. Дополнительные сведения в разделе [concat](./resource-group-template-functions-string.md#concat).
- Функция **uniqueString()** создает детерминированную хэш-строку на основании значений, указанных как параметры. Каждая учетная запись хранения должна иметь уникальное имя в Azure. Эта функция предоставляет уникальную строку. Больше строковых функций в статье [Строковые функции для шаблонов Azure Resource Manager](./resource-group-template-functions-string.md).

Использование переменной, заданной в шаблоне.

```json
"name": "[variables('storageAccountName')]"
```

## <a name="edit-the-template"></a>Изменение шаблона

Чтобы найти связанную с шифрованием настройку учетной записи хранения, используйте справочник по шаблонам учетной записи хранения Azure.

1. Перейдите к [шаблонам Azure](https://docs.microsoft.com/azure/templates/).
2. В оглавлении в левой части выберите **Ссылка**->**Хранилище**->**Учетные записи хранения**. На странице содержится информация об определении сведений об учетной записи хранения.
3. Изучите сведения, связанные с шифрованием.  
4. В элементе свойств для определения ресурсов учетной записи хранения добавьте следующий код json.

    ```json
    "encryption": {
        "keySource": "Microsoft.Storage",
        "services": {
            "blob": {
                "enabled": true
            }
        }
    }
    ```
    Эта часть включает функцию шифрования службы хранилища больших двоичных объектов.

Окончательный элемент ресурсов выглядит следующим образом.

![Зашифрованные ресурсы учетной записи хранения шаблона Resource Manager](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-encrypted-storage-resources.png)

## <a name="deploy-the-template"></a>Развертывание шаблона

Существует множество методов по развертыванию шаблонов.  В этом руководстве используется Cloud Shell на портале Azure. Оболочка Cloud поддерживает как Azure CLI так и Azure PowerShell. В интерфейсе командной строки используются инструкции приведенные здесь.

1. Войдите на [портал Azure](https://portal.azure.com)
2. В правом верхнем углу выберите **Cloud Shell** (как показано на следующем рисунке).

    ![Cloud Shell на портале Azure](./media/resource-manager-tutorial-create-encrypted-storage-accounts/azure-portal-cloud-shell.png)

3. Если Bash не выбран, щелкните стрелку вниз и выберите **Bash**. В этом руководстве понадобится Azure CLI.

    ![Cloud Shell CLI на портале Azure](./media/resource-manager-tutorial-create-encrypted-storage-accounts/azure-portal-cloud-shell-choose-cli.png)
4. Чтобы перезапустить оболочку, выберите **Перезапуск**.
5. Выберите **Отправка и скачивание файлов**, а затем **Отправить**.

    ![Файл отправки Cloud Shell портале Azure](./media/resource-manager-tutorial-create-encrypted-storage-accounts/azure-portal-cloud-shell-upload-file.png)
6. Выберите файл, сохраненный ранее в этом руководстве. **azuredeploy.json** — имя по умолчанию.
7. Чтобы проверить, что файл загружен успешно, в командной строке Cloud Shell необходимо выполнить команду **ls**. Для проверки содержимого шаблона также можно использовать команду **cat**.

    ![Файл списка оболочки Cloud портала Azure](./media/resource-manager-tutorial-create-encrypted-storage-accounts/azure-portal-cloud-shell-list-file.png)
8. Выполните следующую команду в командной строке оболочки Cloud.

    ```cli
    az group create --name <ResourceGroupName> --location <AzureLocation>

    az group deployment create --name <DeploymentName> --resource-group <ResourceGroupName> --template-file azuredeploy.json
    ```
    Пример развертывания приведен на снимке экрана.

    ![Шаблон развертывания оболочки Cloud портала Azure](./media/resource-manager-tutorial-create-encrypted-storage-accounts/azure-portal-cloud-shell-deploy-template.png)

    Значения, которые были использованы, приведены на снимке экрана.

    * **&lt;ResourceGroupName>**: myresourcegroup0719. Существуют два вида отображения параметра.  Убедитесь, что используется одинаковое значение.
    * **&lt;AzureLocation>**: eastus2
    * **&lt;DeployName>**: mydeployment0719
    * **&lt;TemplateFile>**: azuredeploy.json

    Как видно из результата, изображенного на снимке экрана, имя учетной записи хранения — *fhqbfslikdqdsstandardsa*. 

9. Чтобы перечислить учетные записи хранения, которые были созданы, выполните следующую команду PowerShell.

    ```cli
    az storage account show --resource-group <ResourceGroupName> --name <StorageAccountName>
    ```

    Вы увидите результат, аналогичный приведенному ниже, который показывает, что шифрование было включено для хранилища BLOB-объектов.

    ![Зашифрованная учетная запись хранения Azure Resource Manager](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-encrypted-storage-account.png)

## <a name="clean-up-resources"></a>Очистка ресурсов

Если ресурсы Azure больше не нужны, их можно удалить. Для этого необходимо удалить группу ресурсов.

1. На портале Azure в меню слева выберите **Группа ресурсов**.
2. В поле **Filter by name** (Фильтровать по имени) введите имя группы ресурсов.
3. Выберите имя группы ресурсов.  В группе ресурсов должно появится шесть ресурсов.
4. В главном меню выберите **Удалить группу ресурсов**.

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве описано, как использовать справочник по шаблонам для настройки существующего шаблона. Шаблон, использованный в этом руководстве, содержит только один ресурс Azure.  Сведения о том, как разработать шаблон с несколькими ресурсами, можно узнать из следующего руководства.  Некоторые ресурсы обладают зависимыми ресурсами.

> [!div class="nextstepaction"]
> [Tutorial: create Azure Resource Manager templates with dependent resources](./resource-manager-tutorial-create-templates-with-dependent-resources.md) (Руководство. Создание шаблонов Azure Resource Manager с зависимыми ресурсами)
