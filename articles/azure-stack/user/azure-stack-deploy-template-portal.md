---
title: Развертывание шаблонов в Azure Stack с помощью портала | Документация Майкрософт
description: Узнайте, как использовать портал Azure Stack для развертывания шаблонов.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: eafa60f2-16c9-4ef1-b724-47709e9ea29e
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/24/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: eea4f568f21693764222c8fdbe3316bf6008cc05
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34604239"
---
# <a name="deploy-templates-using-the-azure-stack-portal"></a>Развертывание шаблонов с помощью портала Azure Stack

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

С помощью портала можно развертывать шаблоны Azure Resource Manager в Azure Stack.

## <a name="to-deploy-a-template"></a>Развертывание шаблона

1. Войдите на портал, выберите **Создать** и выберите **Настраиваемый**.
2. Выберите **Развертывание шаблона**.
3. Щелкните **Изменить шаблон** и вставьте код JSON шаблона в окно кода. Щелкните **Сохранить**.
4. Выберите **Изменить параметры**, укажите значения параметров, которые отображаются, а затем нажмите кнопку **ОК**.
5. Выберите **Подписка**. Выберите подписку, которую нужно использовать, и нажмите кнопку **ОК**.
6. Выберите **Группа ресурсов**. Выберите имеющуюся группу ресурсов или создайте новую. Затем нажмите кнопку **ОК**.
7. Нажмите кнопку **Создать**. Новый элемент на панели мониторинга отслеживает ход выполнения развертывания вашего шаблона.

## <a name="next-steps"></a>Дополнительная информация

* [Развертывание шаблонов с помощью PowerShell](azure-stack-deploy-template-powershell.md)
