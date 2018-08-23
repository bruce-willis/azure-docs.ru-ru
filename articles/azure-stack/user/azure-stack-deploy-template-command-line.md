---
title: Развертывание шаблонов в Azure Stack с помощью командной строки | Документация Майкрософт
description: Узнайте, как использовать кроссплатформенный интерфейс командной строки (CLI) для развертывания шаблонов в Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 9584177f-4af3-4834-864d-930b09ae0995
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2018
ms.author: sethm
ms.reviewer: ''
ms.openlocfilehash: 7814552256f17c5265bbeb4ce8c069dd8dca1bb2
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/18/2018
ms.locfileid: "41946496"
---
# <a name="deploy-templates-in-azure-stack-using-the-command-line"></a>Развертывание шаблонов в Azure Stack с помощью командной строки

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

С помощью командной строки можно развертывать шаблоны Azure Resource Manager в Пакет средств разработки Azure Stack. Используя шаблоны Azure Resource Manager, можно развернуть и подготовить все ресурсы для приложения в рамках одной скоординированной операции.

## <a name="before-you-begin"></a>Перед началом работы
 - [Выполните установку и подключение](azure-stack-version-profiles-azurecli2.md) к Azure Stack с помощью Azure CLI.
 - Скачайте файлы *azuredeploy.json* и *azuredeploy.parameters.json* из [примера шаблона создания учетной записи хранения](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-create-storage-account).
 
## <a name="deploy-template"></a>Развертывание шаблона
Перейдите к папке, в которую скачаны эти файлы, и выполните следующую команду для развертывания шаблона:

    azure group create "cliRG" "local" –f azuredeploy.json –d "testDeploy" –e azuredeploy.parameters.json

Эта команда развертывает шаблон в группу ресурсов **cliRG** в стандартном расположении подтверждения концепции Azure Stack.

## <a name="validate-template-deployment"></a>Проверка развертывания шаблона
Чтобы просмотреть эту группу ресурсов и учетную запись хранения, используйте следующие команды.

    azure group list

    azure storage account list




