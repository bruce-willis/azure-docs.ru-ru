---
title: Настройка удостоверения гибридного облака в приложениях Azure и Azure Stack | Документация Майкрософт
description: Узнайте, как настроить удостоверение гибридного облака в приложениях Azure и Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/22/2018
ms.author: mabrigg
ms.reviewer: Anjay.Ajodha
ms.openlocfilehash: a57afb4a90da5877879afddc35545e0bfef622a7
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/06/2018
ms.locfileid: "34808168"
---
# <a name="tutorial-configure-hybrid-cloud-identity-for-azure-and-azure-stack-applications"></a>Руководство по настройке идентификатора гибридного облака для приложений Azure и Azure Stack

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

Узнайте, как настроить идентификатор гибридного облака для приложений Azure и Azure Stack.

Платформа Azure вообще и Azure Stack поддерживают два способа доступа к приложениям.

 * Если в Azure Stack есть постоянное подключение к Интернету, можно использовать Azure Active Directory (Azure AD).
 * Если Azure Stack не имеет подключения к Интернету, используйте службы федерации Active Directory (AD FS).

Субъекты-службы позволяют предоставлять доступ к приложениям Azure Stack для их развертывания или настройки с помощью Azure Resource Manager в Azure Stack.

В рамках этого руководства вы создадите пример среды и выполните в ней следующие действия:

> [!div class="checklist"]
> * создадите гибридное удостоверение для глобальной платформы Azure и Azure Stack;
> * Получите маркер для доступа к API Azure Stack.

Для выполнения указанных в этом руководстве действий необходимо иметь разрешения оператора Azure Stack.

## <a name="create-a-service-principal-for-azure-ad-in-the-portal"></a>Создание субъекта-службы с доступом для Azure AD с помощью портала

Если Azure Stack развернут с использованием Azure AD в качестве хранилища идентификаторов, создание субъекта-службы выполняется точно так же, как для Azure. В статье [Создание субъектов-служб](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-create-service-principals#create-service-principal-for-azure-ad) рассказывается, как выполнить эти шаги с использованием портала. Прежде чем начать, проверьте [необходимые разрешения Azure AD](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#required-permissions).

## <a name="create-a-service-principal-for-ad-fs-using-powershell"></a>Создание субъекта-службы для AD FS с помощью PowerShell

Когда вы развернете Azure Stack с использованием AD FS, для создания субъекта-службы, назначения роли для доступа и входа с этим идентификатором можно использовать PowerShell. В разделе [Создание субъекта-службы для AD FS](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-create-service-principals#create-service-principal-for-ad-fs) показано, как выполнить необходимые шаги с помощью PowerShell.

## <a name="using-the-azure-stack-api"></a>Использование API Azure Stack

В руководстве [Azure Stack API](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-rest-api-use) приведены шаги по получению маркера для доступа к Azure Stack API.

## <a name="connect-to-azure-stack-using-powershell"></a>Подключение к Azure Stack с помощью PowerShell

Краткое руководство [Начало работы с PowerShell в Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-configure-quickstart) поможет выполнить действия, необходимые для установки Azure PowerShell, и подключиться к среде Azure Stack.

### <a name="prerequisites"></a>предварительным требованиям

Установка Azure Stack с подключением к Azure Active Directory и использованием доступной подписки. Если вы не установили Azure Stack, воспользуйтесь инструкциями по установке [Пакета средств разработки Azure Stack](https://docs.microsoft.com/azure/azure-stack/asdk/asdk-deploy).

#### <a name="connect-to-azure-stack-using-code"></a>Подключение к Azure Stack из кода программы

Чтобы подключиться к Azure Stack из кода, через API конечных точек Azure Resource Manager получите конечную точку аутентификации и конечную точку Microsoft Graph для Azure Stack, а затем выполните аутентификацию с помощью запросов REST. Образец клиентского приложения можно найти на [GitHub](https://github.com/shriramnat/HybridARMApplication).

>[!Note]
>Если пакет Azure SDK для используемого языка не поддерживает профили API Azure, вы не можете использовать его для работы с Azure Stack. Дополнительные сведения о профилях API Azure см. в статье [Управление профилями версий API](https://docs.microsoft.com/da-dk/azure/azure-stack/user/azure-stack-version-profiles).

## <a name="next-steps"></a>Дополнительная информация

 - Дополнительные сведения о работе с удостоверениями в Azure Stack см. в статье [Архитектура удостоверений для Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-identity-architecture).
 - Дополнительные сведения о шаблонах облака Azure см. в статье [Cloud Design Patterns](https://docs.microsoft.com/azure/architecture/patterns) (Конструктивные шаблоны облачных решений).
