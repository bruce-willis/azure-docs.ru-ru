---
title: Приступая к работе с API связывания сущностей | Документация Майкрософт
description: Анализ текста и связывание именованных сущностей с соответствующими записями в базе знаний с помощью API связывания сущностей в Cognitive Services.
services: cognitive-services
author: DavidLiCIG
manager: wkwok
ms.service: cognitive-services
ms.component: entity-linking-intelligence
ms.topic: article
ms.date: 07/06/2016
ms.author: davl
ms.openlocfilehash: 54c4a3bbb3637c248bd7705ed291633368b542c9
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35380272"
---
# <a name="get-started-with-entity-linking-api-in-c35"></a>Приступая к работе с API связывания сущностей с помощью C&#35;

API связывания сущностей Майкрософт — это инструмент обработки естественного языка, предназначенный для анализа текста и связывания именованных сущностей с соответствующими записями в базе знаний. 

В этом руководстве рассматривается связывание сущностей с помощью клиентской библиотеки связывания сущностей, представленной в виде пакета NuGet. 

### <a name="Prerequisites">Предварительные требования</a>

- Visual Studio 2015
- Ключ API Microsoft Cognitive Services.
- Получение клиентской библиотеки и примера
- Пакет NuGet связывания сущностей (Майкрософт)

Вы можете скачать клиентскую библиотеку API аналитической службы связывания сущностей с помощью [пакета SDK](https://www.github.com/microsoft/cognitive-entitylinking-windows). Содержимое скачанного ZIP-файла нужно извлечь в папку по своему усмотрению. Многие пользователи выбирают папку Visual Studio 2015.

### <a name="step-1-subscribe-entity-linking-intelligence-service-and-get-your-own-key">Шаг 1. Подписка на Аналитическую службу связывания сущностей и получение ключа</a>
Прежде чем использовать Аналитическую службу связывания сущностей, необходимо зарегистрироваться и получить ключ API. Ознакомьтесь со страницей [подписок](https://www.microsoft.com/cognitive-services/en-us/sign-up). В этом руководстве можно использовать как первичный, так и вторичный ключ.

### <a name="step-2-create-a-new-project-in-visual-studio">Шаг 2. Создание проекта в Visual Studio</a>

Давайте начнем с создания проекта в Visual Studio. Запустите Visual Studio 2015 из меню "Пуск". Затем создайте проект, выбрав **Установленные → Шаблоны → Visual C# → Универсальные приложения Windows → Пустое приложение** для шаблона проекта.

 ![Создание универсального приложения](./Images/CreateUWP.png)

### <a name="step-3-add-the-entity-linking-nuget-package-to-your-project">Шаг 3. Добавление в проект пакета NuGet связывания сущностей</a>

Библиотека связывания сущностей Cognitive Services выпущена в виде пакета NuGet.org и должна быть установлена, прежде чем ее можно будет использовать.
Чтобы добавить его в проект, перейдите на вкладку **Обозреватель решений**, щелкните правой кнопкой мыши проект и выберите **Управление пакетами NuGet**.

В нижнем правом углу окна **Диспетчер пакетов NuGet** выберите Nuget.org в качестве **источника пакетов**. Щелкните **Обзор** в левом верхнем углу и в поле поиска введите "ProjectOxford.EntityLinking". Выберите пакет NuGet **Microsoft.ProjectOxford.EntityLinking** и нажмите кнопку **Установить**.

Затем найдите Newtonsoft.Json и установите его. Если будет предложено просмотреть изменения, нажмите кнопку **ОК**. Если отобразятся условия лицензионного соглашения EntityLinking, нажмите кнопку **Я принимаю**.

Теперь библиотека связывания сущностей установлена в вашем приложении. Это можно подтвердить, проверив, присутствует ссылка на **Microsoft.ProjectOxford.EntityLinking** в проекте в обозревателе решений.

 ![Библиотека NuGet, добавленная в проект](./Images/NugetLibraryInProject.png)
 
### <a name="step-4-add-an-input-and-output-text-block-to-your-apps-xaml">Шаг 4. Добавление текстовых полей ввода и вывода в код XAML приложения</a>
Перейдите к файлу **MainPage.xaml** в **обозревателе решений** и дважды щелкните этот файл, чтобы открыть его в новом окне. Для удобства можно дважды нажать кнопку **XAML** на вкладке **Конструктор**. При этом **визуальный конструктор** будет скрыт, и все пространство будет доступно просмотра кода.

 ![Библиотека NuGet, добавленная в проект](./Images/UWPMainPage.png)
 
Функциональные возможности текстовой службы лучше всего визуализировать, создав текстовые поля ввода и вывода. Для этого добавьте следующий код XAML в **сетку**. Этот код добавляет три компонента: текстовое поле ввода, текстовое поле вывода и кнопку запуска.
 
 ```XAML
 <Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
    <Grid.RowDefinitions>
        <RowDefinition Height="*"/>
        <RowDefinition Height="*" />
        <RowDefinition Height="50" />
    </Grid.RowDefinitions>
    <TextBox x:Name="inputBox" Grid.Row="0" TextWrapping="Wrap" Text="Enter a paragraph" Margin="10" AcceptsReturn="True" />
    <TextBlock x:Name="outputBlock" Grid.Row="1" TextWrapping="Wrap" Text="Result will be here" Margin="10" />
    <Button x:Name="button" Grid.Row="2" Content="Get Result" />
</Grid>
 ```
 
### <a name="step-5-proceed-to-add-entity-linking-intelligence-service">Шаг 5. Добавление Аналитической службы связывания сущностей</a>
 
Пользовательский интерфейс создан. Прежде чем использовать службу связывания сущностей, необходимо добавить обработчик button-Click. Откройте файл **MainPage.xaml** в **обозревателе решений**. Добавьте обработчик button-Click в конец определения кнопки.
 
 ```XAML
 <Button x:Name="button" Grid.Row="2" Content="Get Result" Click="button_Click" />
 ```
 
Обработчик button-Click должен быть реализован в коде. Откройте файл **MainPage.xaml.cs** в **обозревателе решений**, чтобы реализовать button-Click. EntityLinkingServiceClient является программой-оболочкой для получения ответов службы связывания сущностей. Аргументом конструктора EntityLinkingServiceClient является ключ подписки Cognitive Services. Вставьте ключ подписки, полученный на **шаге 1**, для вызова службы связывания сущностей. 

Ниже приведен пример кода, который добавляет wikipediaId в ответ с помощью службы связывания сущностей. 
 
 ```csharp
 private async void button_Click(object sender, RoutedEventArgs e)
{
    var text = this.inputBox.Text;
    var client = new EntityLinkingServiceClient("Your subscription key","https://api.labs.cognitive.microsoft.com");
    var linkResponse = await client.LinkAsync(text);
    var result = string.Join(", ", linkResponse.Select(i => i.WikipediaID).ToList());
    this.outputBlock.Text = result;
}
 ```
 
Теперь все готово для запуска вашего первого приложения для обработки естественного языка на основе службы связывания сущностей. Нажмите клавишу **F5**, чтобы скомпилировать и запустить приложение. Вставьте фрагменты текста или абзацы в поле ввода. Нажмите кнопку "Get Result" (Получить результат) и просмотрите распознанные сущности в поле вывода.
 
 ![Пример результата для UWP](./Images/DemoCodeResult.png)
 
### <a name="summary">Сводка</a>
 
В этом руководстве вы узнали, как создать приложение, использующее клиентскую библиотеку Аналитической службы связывания сущностей, с помощью всего нескольких строк кода C# и XAML. 

