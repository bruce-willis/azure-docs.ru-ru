---
title: Тестирование определения пользовательского интерфейса для Управляемых приложений Azure | Документация Майкрософт
description: В этой статье приводятся сведения о тестировании пользовательского интерфейса для создания Управляемого приложения Azure с помощью портала.
services: managed-applications
documentationcenter: na
author: tfitzmac
ms.service: managed-applications
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/10/2018
ms.author: tomfitz
ms.openlocfilehash: 2c313538e297c5781b48fcfe9d0d5390f94c97f5
ms.sourcegitcommit: 17fe5fe119bdd82e011f8235283e599931fa671a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/11/2018
ms.locfileid: "40043546"
---
# <a name="test-azure-portal-interface-for-your-managed-application"></a>Тестирование интерфейса портала Azure для управляемого приложения
После создания файла [createUiDefinition.json](create-uidefinition-overview.md) для Управляемого приложения Azure необходимо протестировать пользовательский интерфейс. Чтобы упростить тестирование, используйте сценарий, который загружает файл на портале. Вам не нужно развертывать управляемое приложение.

## <a name="prerequisites"></a>Предварительные требования

* Файл **createUiDefinition.json**. Если у вас его нет, скопируйте [пример файла](https://github.com/Azure/azure-quickstart-templates/blob/master/test/template-validation-tests/sample-template/createUIDefinition.json) и сохраните его локально.

* Подписка Azure. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="download-test-script"></a>Скачивание сценария теста

Чтобы протестировать свой интерфейс на портале, скопируйте один из следующих сценариев на локальный компьютер:

* [PowerShell side-load script](https://github.com/Azure/azure-quickstart-templates/blob/master/SideLoad-CreateUIDefinition.ps1)
* [Azure CLI side-load script](https://github.com/Azure/azure-quickstart-templates/blob/master/sideload-createuidef.sh)

## <a name="run-script"></a>Запуск сценария

Чтобы просмотреть файл интерфейса на портале, запустите загруженный сценарий. Сценарий создаст учетную запись хранения в вашей подписке Azure и загрузит файл createUiDefinition.json в эту учетную запись хранения. Затем он откроет портал и загрузит файл из учетной записи хранения.

Укажите расположение учетной записи хранения и папку, в которой содержится файл createUiDefinition.json. Расположение учетной записи хранения нужно указывать только при первом запуске сценария или в случае удаления учетной записи хранения.

Для PowerShell используйте команду:

```powershell
.\SideLoad-CreateUIDefinition.ps1 `
  -StorageResourceGroupLocation southcentralus `
  -ArtifactsStagingDirectory <path-to-folder-with-createuidefinition>
```

Для интерфейса командной строки Azure:

```azurecli
./sideload-createuidef.sh \
  -l southcentralus \
  -a <path-to-folder-with-createuidefinition>
```

## <a name="test-your-interface"></a>Тестирование интерфейса

Сценарий откроет новую вкладку в браузере. В ней отобразится портал с интерфейсом для создания управляемого приложения.

![Просмотр портала](./media/test-createuidefinition/view-portal.png)

Прежде чем приступить к заполнению полей, откройте инструменты разработчика Web в браузере. В **консоли** отображаются важные сообщения об интерфейсе.

![Выбор консоли](./media/test-createuidefinition/select-console.png)

Если определение интерфейса содержит ошибку, ее описание будет размещено в консоли.

![Отображение ошибки](./media/test-createuidefinition/show-error.png)

Укажите значения для полей. По завершении вы увидите значения, переданные в шаблон.

![Отображение значений](./media/test-createuidefinition/show-json.png)

## <a name="test-your-solution-files"></a>Тестирование файлов решения

Теперь, когда вы проверили, что ваш интерфейс портала работает так, как ожидается, пришло время проверить, что ваш файл createUiDefinition правильно интегрирован с файлом mainTemplate.json. Можно запустить тестирование сценария проверки для проверки содержимого файлов решения, включая файл createUiDefinition. Сценарий проверяет синтаксис JSON, регулярные выражения в текстовых полях и обеспечивает соответствие выходных значений интерфейса портала параметрам шаблона. Со сведениями о запуске этого сценария можно ознакомиться в статье, посвященной [запуску статических проверок шаблонов](https://github.com/Azure/azure-quickstart-templates/tree/master/test/template-validation-tests).

## <a name="next-steps"></a>Дополнительная информация

После проверки интерфейса портала узнайте, как [обеспечить доступность управляемых приложений Azure в Marketplace](publish-marketplace-app.md).
