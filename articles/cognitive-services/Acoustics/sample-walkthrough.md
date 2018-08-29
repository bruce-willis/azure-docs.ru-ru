---
title: Пошаговое руководство по примеру Project Acoustics — Cognitive Services
description: В этом пошаговом руководстве описывается пример сцены Unity для Project Acoustics, включая развертывание на рабочем столе и в виртуальную реальность.
services: cognitive-services
author: kegodin
manager: noelc
ms.service: cognitive-services
ms.component: acoustics
ms.topic: article
ms.date: 08/17/2018
ms.author: kegodin
ms.openlocfilehash: b738cc2fc7db6987b8f4ad54a2c53cc9e69989b3
ms.sourcegitcommit: 1aedb52f221fb2a6e7ad0b0930b4c74db354a569
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/17/2018
ms.locfileid: "40181337"
---
# <a name="unity-sample-walkthrough"></a>Пример пошагового руководства Unity
Это пошаговое руководство по примеру Project Acoustics. Общие сведения о Project Acoustics см. в [этой статье](what-is-acoustics.md). Чтобы получить справку по добавлению пакета Project Acoustics в существующий проект Unity, перейдите к статье о [начале работы с Project Acoustics](getting-started.md).

## <a name="requirements-for-running-the-sample-project"></a>Требования для запуска примера проекта
* Unity 2018.2 +, использующий версию среды выполнения сценариев .NET 4.x.
* 64-разрядная версия Unity Editor для Windows.
* Пример, который поддерживает Windows Desktop, универсальную платформу Windows и Android, включая шлемы виртуальной реальности (HMD).
* Подписка пакетной службы Azure, требуемая для создания сцены.

## <a name="sample-project-setup"></a>Установка примера проекта
Загрузите и импортируйте пакет **MicrosoftAcoustics.Sample.unitypackage**. При импорте параметры проекта, в том числе **Spatializer**(Модуль пространственного звука) и **Scripting Runtime Version** (Версия среды выполнения сценариев), обновляются в соответствии с требованиями подключаемого модуля. По завершении импорта в консоли Unity вы увидите ошибку из **AcousticsGeometry.cs** об изменении версии среды выполнения сценариев на **.NET 4.x Equivalent**. Это изменение настроек выполняется как часть импорта пакета, но чтобы изменения вступили в силу, нужно перезагрузить Unity. Перезагрузите Unity сейчас.

## <a name="running-the-sample"></a>Выполнение примера
В пример входит демонстрационная сцена **Assets/AcousticsDemo/ProjectAcousticsDemo.unity**. Эта сцена имеет единый пространственный аудиоисточник, который воспроизводится из плавающего куба (названного **AudioHolder** в представлении **Hierarchy** (Иерархия)). Для формирования общего сценария навигации Main Camera выступает дочерним элементом объекта CameraHolder. 

![Представление иерархии](media/SampleHierarchyView.png)

Сцена уже создана и имеет ACE-файл, связанный с готовым шаблоном **MicrosoftAcoustics** в представлении **Hierarchy** (Иерархия). 

Послушайте, как звучит сцена, нажав кнопку воспроизведения в редакторе Unity. Используйте клавиши W, A, S, D и мышь, чтобы перемещаться. Для сравнения звучания сцены с акустикой и без нее щелкните левой кнопкой мыши или нажмите кнопку основного контроллера. Чтобы переключать различные источники звука, щелкните правой кнопкой мыши или нажмите кнопку "Назад" на контроллере.

## <a name="targeting-other-platforms"></a>Использование других платформ
Этот пример содержит параметры для запуска в Windows Desktop, на универсальной платформе Windows, в смешанной реальности Windows, Android и Oculus Go. По умолчанию проект настроен для Windows Desktop. Чтобы использовать платформу виртуальной реальности, перейдите к параметрам проигрывателя (**Edit > Project Settings > Player** (Изменить > Параметры проекта > Проигрыватель)), найдите **XR Settings** (Параметры XR) и установите флажок **Virtual Reality Supported**(Поддержка виртуальной реальности).

![Включение виртуальной реальности](media/VRSupport.png)  

Подключите гарнитуру виртуальной реальности к вашему ПК. Выберите **File > Build Settings** (Файл > Параметры сборки) и щелкните**Build and Run** (Сборка и запуск), чтобы развернуть пример для вашей гарнитуры виртуальной реальности. Перемещайтесь по сцене с помощью контроллеров движения вашей гарнитуры или попробуйте использовать клавиши W, A, S, D.    
Чтобы использовать Android и Oculus Go, выберите Android в меню **Build Settings** (Параметры сборки). Выберите **Switch Target** (Переключить целевую платформу), а затем — **Build and Run** (Сборка и запуск). Это развернет пример сцены на подключенном устройстве Android. Сведения о разработках Unity для Android см. в разделе о [начале работы с разработкой для Android](https://docs.unity3d.com/Manual/android-GettingStarted.html).

![Использование платформы Android](media/TargetAndroid.png)  

## <a name="next-steps"></a>Дополнительная информация
* [Создайте учетную запись Azure](create-azure-account.md) для собственных сборок сцены.
* Изучите [процесс проектирования](design-process.md).

