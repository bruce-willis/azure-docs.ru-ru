---
title: Отчет о проверке для Azure Stack | Документация Майкрософт
description: Узнайте результаты проверки готовности Azure Stack с помощью отчета.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/08/2018
ms.author: sethm
ms.reviewer: ''
ms.openlocfilehash: 156b84e4941363716721b5cee6c19333ffe7594c
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/10/2018
ms.locfileid: "49079452"
---
# <a name="azure-stack-validation-report"></a>Отчет о проверке Azure Stack
Используйте средство проверки готовности Azure Stack, чтобы определить, выполнены ли условия, необходимые для поддержки развертывания и обслуживания окружения Azure Stack. Результаты сохраняются в JSON-файле отчета. В отчете отображаются подробные и сводные данные о состоянии компонентов, необходимых для развертывания Azure Stack. Этот отчет также содержит сведения о смене секретов для существующих развертываний Azure Stack.  

 ## <a name="where-to-find-the-report"></a>Где найти отчет
При работе средства результаты сохраняются в файле **AzsReadinessCheckerReport.json**. Также создается файл журнала с именем **AzsReadinessChecker.log**. Расположение этих файлов указывается в PowerShell вместе с результатами проверки.

![Запуск проверки](./media/azure-stack-validation-report/validation.png)

В обоих файлах сохраняются результаты всех последовательных проверок на этом компьютере.  Например, вы можете запустить средство только для проверки сертификатов, затем снова запустить его для проверки удостоверений Azure, а в третий раз — для проверки регистрации. Результаты всех трех проверок будут доступны в JSON-файле отчета.  

По умолчанию оба файла сохраняются в расположении *C:\Users\<имя_пользователя>\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json*.  
- Чтобы задать другое расположение отчетов, при запуске проверки можно указать в конце командной строки параметр **-OutputPath** ***&lt;путь&gt;***.   
- Укажите параметр **-CleanReport** в конце команды, чтобы удалить из файла *AzsReadinessCheckerReport.json* сведения. о предыдущих запусках средства.

## <a name="view-the-report"></a>Просмотр отчета
Чтобы просмотреть отчет в PowerShell, укажите путь к отчету в параметре **-ReportPath**. С помощью этой команды выводится содержимое отчета и перечисляются проверки, для которых еще нет доступных результатов.

Например, следующая команда позволяет просмотреть из командной строки PowerShell отчет, расположенный в текущем каталоге: 
   > `Start-AzsReadinessChecker -ReportPath .\AzsReadinessReport.json` 

Результат выглядит примерно так:

![Просмотр отчета](./media/azure-stack-validation-report/view-report.png)

## <a name="view-the-report-summary"></a>Просмотр сводного отчета
Чтобы просмотреть сводную информацию из отчета, добавьте параметр **-Summary** в конец командной строки PowerShell. Например:  
 > `Start-AzsReadinessChecker -ReportPath .\AzsReadinessReport.json -summary`  

Сводные данные содержат список проверок, по которым пока нет данных, а также сведения об успешных или неудачных результатах проверок. Результат выглядит примерно так:

![Сводка по отчету](./media/azure-stack-validation-report/report-summary.png)


## <a name="view-a-filtered-report"></a>Просмотр отчета с фильтром
Чтобы просмотреть отчет, отфильтрованный проверкам определенного типа, укажите параметр **-ReportSections** с одним из следующих значений:
- Сертификат
- AzureRegistration;
- AzureIdentity;
- Задания   
- Все  

Например, для извлечения из отчета сводной информации только о сертификатах выполните следующую команду PowerShell: 
 > `Start-AzsReadinessChecker -ReportPath .\AzsReadinessReport.json -ReportSections Certificate – Summary`


## <a name="see-also"></a>См. также
[Start-AzsReadinessChecker cmdlet reference](azure-stack-azsreadiness-cmdlet.md) (Справочник по командлету Start-AzsReadinessChecker)
