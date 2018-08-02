---
title: Общие сведения о пакете SDK графического модуля runbook службы автоматизации Azure
description: В этой статье описывается использование пакета SDK графического модуля runbook службы автоматизации Azure
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 07/20/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 702af8311887afc94e7127704d3377e944503324
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/25/2018
ms.locfileid: "39240585"
---
# <a name="use-the-azure-automation-graphical-runbook-sdk"></a>Использование пакета SDK графического модуля runbook службы автоматизации Azure

[Графические модули runbook](automation-graphical-authoring-intro.md) — это модули runbook, которые позволяют выполнять сложные задачи с базовым кодом Windows PowerShell или рабочего процесса PowerShell. Пакет SDK для графической разработки службы автоматизации Microsoft Azure позволяет разработчикам создавать и изменять графические модули runbook для использования со службой автоматизации Azure. В следующих фрагментах кода показан базовый поток создания графического модуля runbook из кода.

## <a name="pre-requisites"></a>Предварительные требования

Для начала импортируйте пакет `Microsoft.Azure.Automation.GraphicalRunbook.Model` в проект.

## <a name="create-a-runbook-object-instance"></a>Создание экземпляра объекта runbook

Укажите ссылку на сборку `Orchestrator.GraphRunbook.Model` и создайте экземпляр класса `Orchestrator.GraphRunbook.Model.GraphRunbook`:

```csharp
using Orchestrator.GraphRunbook.Model;
using Orchestrator.GraphRunbook.Model.ExecutableView;

var runbook = new GraphRunbook();
```

## <a name="add-runbook-parameters"></a>Добавление параметров runbook

Создайте экземпляры объектов `Orchestrator.GraphRunbook.Model.Parameter` и добавьте их в runbook:

```csharp
runbook.AddParameter(
 new Parameter("YourName") {
  TypeName = typeof(string).FullName,
   DefaultValue = "World",
   Optional = true
 });

runbook.AddParameter(
 new Parameter("AnotherParameter") {
  TypeName = typeof(int).FullName, ...
 });
```

## <a name="add-activities-and-links"></a>Добавление действий и ссылок

Создайте экземпляры действий соответствующих типов и добавьте их в runbook:

```csharp
var writeOutputActivityType = new CommandActivityType {
 CommandName = "Write-Output",
  ModuleName = "Microsoft.PowerShell.Utility",
 InputParameterSets = new [] {
  new ParameterSet {
   Name = "Default",
    new [] {
     new Parameter("InputObject"), ...
    }
  },
  ...
 }
};

var outputName = runbook.AddActivity(
 new CommandActivity("Output name", writeOutputActivityType) {
  ParameterSetName = "Default",
   Parameters = new ActivityParameters {
    {
     "InputObject",
     new RunbookParameterValueDescriptor("YourName")
    }
   },
   PositionX = 0,
   PositionY = 0
 });

var initializeRunbookVariable = runbook.AddActivity(
 new WorkflowScriptActivity("Initialize variable") {
  Process = "$a = 0",
   PositionX = 0,
   PositionY = 100
 });
```

Действия реализуются с помощью следующих классов в пространстве имен `Orchestrator.GraphRunbook.Model`:

|Класс  |Действие  |
|---------|---------|
|CommandActivity     | Вызывает команду PowerShell (командлет, функция и т. д.).        |
|InvokeRunbookActivity     | Вызывает другой runbook изнутри.        |
|JunctionActivity     | Ожидает завершения всех входящих ветвей.        |
|WorkflowScriptActivity     | Выполняет блок кода PowerShell или рабочего процесса PowerShell (в зависимости от типа runbook) в контексте runbook. Это эффективное средство, но не злоупотребляйте им. В пользовательском интерфейсе этот блок сценария будет отображаться как текст. Подсистема выполнения будет обрабатывать этот предоставленный блок по принципу "черного ящика" и не предпримет попыток проанализировать его содержимое за исключением базовой проверки синтаксиса. Если необходимо просто вызвать одну команду PowerShell, воспользуйтесь CommandActivity.        |

> [!NOTE]
> Не извлекайте свои собственные действия из предоставленных классов. Служба автоматизации Azure не сможет использовать runbook с помощью настраиваемых типов действий.

Параметры CommandActivity и InvokeRunbookActivity необходимо указать в качестве дескрипторов значений, а не прямых значений. Дескрипторы значений указывают, каким образом должны создаваться фактические значения параметров. Сейчас указаны следующие дескрипторы значений:


|Дескриптор  |Определение  |
|---------|---------|
|ConstantValueDescriptor     | Ссылается на жестко заданное постоянное значение.        |
|RunbookParameterValueDescriptor     | Ссылается на параметр runbook по имени.        |
|ActivityOutputValueDescriptor     | Ссылается на вышестоящие выходные данные действия, позволяя одному действию "подписаться" на данные, создаваемые другим действием.        |
|AutomationVariableValueDescriptor     | Ссылается на ресурс переменной службы автоматизации по имени.         |
|AutomationCredentialValueDescriptor     | Ссылается на ресурс сертификата службы автоматизации по имени.        |
|AutomationConnectionValueDescriptor     | Ссылается на ресурс подключения службы автоматизации по имени.        |
|PowerShellExpressionValueDescriptor     | Указывает выражение PowerShell свободной формы, которое будет вычисляться непосредственно перед вызовом действия.  <br/>Это эффективное средство, но не злоупотребляйте им. В пользовательском интерфейсе это выражение будет отображаться как текст. Подсистема выполнения будет обрабатывать этот предоставленный блок по принципу "черного ящика" и не предпримет попыток проанализировать его содержимое за исключением базовой проверки синтаксиса. По возможности отдавайте предпочтение более конкретным дескрипторам значений.      |

> [!NOTE]
> Не извлекайте свои собственные дескрипторы значений из предоставленных классов. Служба автоматизации Azure не сможет использовать runbook с помощью настраиваемых типов дескрипторов значений.

Создайте экземпляры связей, соединяющих действия, и добавьте их в runbook:

```csharp
runbook.AddLink(new Link(activityA, activityB, LinkType.Sequence));

runbook.AddLink(
 new Link(activityB, activityC, LinkType.Pipeline) {
  Condition = string.Format("$ActivityOutput['{0}'] -gt 0", activityB.Name)
 });
```

## <a name="save-the-runbook-to-a-file"></a>Сохранение runbook в файл

Сериализуйте runbook в строку с помощью `Orchestrator.GraphRunbook.Model.Serialization.RunbookSerializer`:

```csharp
var serialized = RunbookSerializer.Serialize(runbook);
```

Эту строку можно сохранить в файл с расширением **.graphrunbook** и этот файл можно импортировать в службу автоматизации Azure.
Сериализованный формат может измениться в будущих версиях `Orchestrator.GraphRunbook.Model.dll`. Мы гарантируем обратную совместимость: любой runbook, сериализованный с использованием более старой версии `Orchestrator.GraphRunbook.Model.dll`, может быть десериализован с использованием любой более поздней версии. Прямая совместимость не гарантируется: runbook, сериализованный с использованием более поздней версии, не может быть десериализован с использованием старых версий.

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о графических модулях runbook в службе автоматизации Azure см. в статье [Графическая разработка в службе автоматизации Azure](automation-graphical-authoring-intro.md).
