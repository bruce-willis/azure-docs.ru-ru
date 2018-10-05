---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 09/13/2018
ms.author: wolfma
ms.openlocfilehash: 8ff8e8341b6f39f66c2cc8014d41d3d3a2918d2b
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/28/2018
ms.locfileid: "47454543"
---
1. Запустите Visual Studio 2017.
 
1. Убедитесь, что доступна рабочая нагрузка **.NET desktop environment** (Среда классических приложений .NET). Choose **Tools** \> **Get Tools and Features** (Средства > Получить средства и компоненты) в строке меню Visual Studio, чтобы открыть установщик Visual Studio. Если эта рабочая нагрузка уже включена, закройте диалоговое окно. 

    В противном случае установите флажок рядом с полем **.NET desktop environment** (Среда классических приложений .NET) и нажмите кнопку **Изменить** в правом нижнем углу диалогового окна. Для установки этого нового компонента нужно некоторое время.

    ![Включение рабочей нагрузки "Разработка классического приложения .NET"](~/articles/cognitive-services/speech-service/media/sdk/vs-enable-net-desktop-workload.png)

1. Создайте консольное приложение Visual C#. В диалоговом окне **Создать проект** в левой области разверните элементы **Установлено** \> **Visual C#** \> **Классическое приложение Windows** и выберите **Консольное приложение (.NET Framework)**. В качестве имени проекта введите *helloworld*.

    ![Создание консольного приложения Visual C# (.NET Framework)](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-01-new-console-app.png "Create Visual C# Console App (.NET Framework)")

1. Установите и создайте ссылку на [пакет Speech SDK NuGet](https://aka.ms/csspeech/nuget). В обозревателе решений щелкните правой кнопкой мыши решение и выберите пункт **Управление пакетами NuGet для решения**.

    ![Щелкните правой кнопкой мыши Управление пакетами NuGet для решения](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-02-manage-nuget-packages.png "Управление пакетами NuGet для решения")

1. В верхнем правом углу в поле **Источник пакета** выберите **Nuget.org**. Найдите пакет `Microsoft.CognitiveServices.Speech`, а затем установите его в проект **helloworld**.

    ![Установка пакета NuGet Microsoft.CognitiveServices.Speech](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-03-nuget-install-1.0.0.png "Install NuGet package")

1. Примите условия отображаемой лицензии, чтобы начать установку пакета NuGet.

    ![Принять лицензию](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-04-nuget-license.png "Принять лицензию")

    После установки пакета в консоли диспетчера пакетов появится подтверждение.

1. Создайте конфигурацию платформы, соответствующую архитектуре вашего ПК, с помощью Configuration Manager. Выберите **Создать** > **Диспетчер конфигураций**.

    ![Запуск диспетчера конфигураций](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-05-cfg-manager-click.png "Запуск диспетчера конфигураций")

1. Добавьте новую платформу в диалоговом окне **Диспетчера конфигураций**. В раскрывающемся списке **Платформа активного решения** выберите команду **Создать**.

    ![Добавление новой платформы в окне диспетчера конфигураций](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-06-cfg-manager-new.png "Добавление новой платформы в окне диспетчера конфигураций")

1. В случае использования 64-разрядной версии Windows, создайте новую конфигурацию платформы с именем `x64`. В случае использования 32-разрядной версии Windows, создайте новую конфигурацию платформы с именем `x86`.

    ![Добавление новой платформы с именем "64" в 64-разрядной версии Windows](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-07-cfg-manager-add-x64.png "Добавление 64-разрядной платформы")


