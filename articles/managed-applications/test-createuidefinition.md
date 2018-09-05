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
ms.date: 08/22/2018
ms.author: tomfitz
ms.openlocfilehash: c88bdce64e88f8639da2c4ebb01f4594fccff8a0
ms.sourcegitcommit: b5ac31eeb7c4f9be584bb0f7d55c5654b74404ff
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/23/2018
ms.locfileid: "42747094"
---
# <a name="test-azure-portal-interface-for-your-managed-application"></a>Тестирование интерфейса портала Azure для управляемого приложения
После создания файла [createUiDefinition.json](create-uidefinition-overview.md) для Управляемого приложения Azure необходимо протестировать пользовательский интерфейс. Чтобы упростить тестирование, используйте сценарий, который загружает файл на портале. Вам не нужно развертывать управляемое приложение.

## <a name="prerequisites"></a>Предварительные требования

* Файл **createUiDefinition.json**. Если у вас его нет, скопируйте [пример файла](https://github.com/Azure/azure-quickstart-templates/blob/master/100-marketplace-sample/createUiDefinition.json) и сохраните его локально.

* Подписка Azure. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="download-test-script"></a>Скачивание сценария теста

Чтобы протестировать свой интерфейс на портале, скопируйте один из следующих сценариев на локальный компьютер:

* [PowerShell side-load script](https://github.com/Azure/azure-quickstart-templates/blob/master/SideLoad-CreateUIDefinition.ps1)
* [Azure CLI side-load script](https://github.com/Azure/azure-quickstart-templates/blob/master/sideload-createuidef.sh)

## <a name="run-script"></a>Запуск сценария

Чтобы просмотреть файл интерфейса на портале, запустите загруженный сценарий. Сценарий создаст учетную запись хранения в вашей подписке Azure и загрузит файл createUiDefinition.json в эту учетную запись хранения. Учетная запись хранения создается при первом запуске скрипта или в случае удаления учетной записи хранения. Если учетная запись хранения уже существует в подписке Azure, скрипт повторно использует ее. Затем он откроет портал и загрузит файл из учетной записи хранения.

Укажите расположение учетной записи хранения и папку, в которой содержится файл createUiDefinition.json.

Для PowerShell используйте команду:

```powershell
.\SideLoad-CreateUIDefinition.ps1 `
  -StorageResourceGroupLocation southcentralus `
  -ArtifactsStagingDirectory .\100-Marketplace-Sample
```

Для интерфейса командной строки Azure:

```azurecli
./sideload-createuidef.sh \
  -l southcentralus \
  -a .\100-Marketplace-Sample
```

Если файл createUiDefinition.json находится в той же папке, что и скрипт, и вы уже создали учетную запись хранения, указывать эти параметры не нужно.

Для PowerShell используйте команду:

```powershell
.\SideLoad-CreateUIDefinition.ps1
```

Для интерфейса командной строки Azure:

```azurecli
./sideload-createuidef.sh
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

Эти значения можно использовать как файл параметров для тестирования шаблона развертывания.

## <a name="troubleshooting-the-interface"></a>Устранение неполадок интерфейса

К распространенным ошибкам, с которыми можно столкнуться, относятся:

* Портал не загружает интерфейс. Вместо этого отображается значок облака с каплей. Как правило, этот значок отображается при наличии синтаксической ошибки в файле. Откройте файл в VS Code (или другом редакторе JSON с проверкой схемы) и найдите синтаксические ошибки.

* Портал зависает на странице сводки. Как правило, это прерывание происходит при наличии ошибки в разделе выходных данных. Например, возможно, вы ссылаетесь на несуществующий элемент управления.

* Параметр в выходных данных пустой. Возможно, параметр ссылается на несуществующее свойство. Например, ссылка на элемент управления является допустимой, а ссылка на свойство — нет.

## <a name="test-your-solution-files"></a>Тестирование файлов решения

Теперь, когда вы проверили, что ваш интерфейс портала работает так, как ожидается, пришло время проверить, что ваш файл createUiDefinition правильно интегрирован с файлом mainTemplate.json. Можно запустить тестирование сценария проверки для проверки содержимого файлов решения, включая файл createUiDefinition. Сценарий проверяет синтаксис JSON, регулярные выражения в текстовых полях и обеспечивает соответствие выходных значений интерфейса портала параметрам шаблона. Со сведениями о запуске этого сценария можно ознакомиться в статье, посвященной [запуску статических проверок шаблонов](https://github.com/Azure/azure-quickstart-templates/tree/master/test/template-validation-tests).

## <a name="next-steps"></a>Дополнительная информация

После проверки интерфейса портала узнайте, как [обеспечить доступность управляемых приложений Azure в Marketplace](publish-marketplace-app.md).
