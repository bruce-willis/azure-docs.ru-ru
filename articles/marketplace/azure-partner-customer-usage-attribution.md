---
title: Определение потребления услуг Azure клиентами партнеров
description: Общие сведения о том, как отслеживать использование решений Azure Marketplace клиентами
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: ellacroi
manager: nunoc
editor: ''
ms.assetid: e8d228c8-f9e8-4a80-9319-7b94d41c43a6
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 07/26/2018
ms.author: ellacroi
ms.openlocfilehash: 46cd5dbc044cbd0b7e38e5f0d0c8aa1916387a2d
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/10/2018
ms.locfileid: "40038479"
---
# <a name="azure-partner-customer-usage-attribution"></a>Определение потребления услуг Azure клиентами партнеров

Так как вы являетесь партнером по разработке ПО для Azure, ваши решения требуют либо компоненты Azure, либо развертывание непосредственно в инфраструктуре Azure.  В настоящее время, когда клиент развертывает решение партнера и подготавливает к работе собственные ресурсы Azure, партнеру сложно получать сведения о состоянии таких развертываний и сложно понять, как они влияют на увеличение ресурсов Azure. Благодаря более высокому уровню видимости партнеры могут согласовать свои знания с командами продаж Майкрософт и получать кредит для партнерских программ Майкрософт.   

Корпорация Майкрософт создает новый метод, чтобы помочь партнерам лучше отслеживать использование Azure в результате развертывания клиентами вашего программного обеспечения в Azure. Этот новый метод основан на использовании Azure Resource Manager для оркестрации развертывания служб Azure.

Являясь партнером корпорации Майкрософт, вы можете связать сведения об использовании Azure с любыми ресурсами Azure, которое вы подготавливаете от имени клиента.  Эту связь можно реализовать с помощью Azure Marketplace, репозитория для быстрой настройки, частных репозиториев GitHub и даже индивидуального вовлечения клиентов.  Для включения отслеживания существуют два подхода.

- Шаблоны Azure Resource Manager. Можно использовать шаблоны Azure Resource Manager или шаблоны решений, чтобы развертывать службы Azure для запуска программного обеспечения партнера. Партнеры могут создать шаблон Azure Resource Manager, который определяет инфраструктуру и конфигурацию вашего решения Azure. Благодаря такому шаблону вы и ваши клиенты сможете развертывать решение на протяжении всего его жизненного цикла и быть уверенными, что ресурсы развертываются в согласованном состоянии. 

- Интерфейсы API Azure Resource Manager. Партнеры могут напрямую вызывать интерфейсы API Azure Resource Manager для развертывания шаблона Azure Resource Manager или создания вызовов API для непосредственной подготовки служб Azure. 

## <a name="method-1-azure-resource-manager-templates"></a>Метод 1. Шаблоны Azure Resource Manager 

На сегодняшний день многие решения партнеров развертываются в подписке клиента с помощью шаблонов Azure Resource Manager.  Если у вас уже есть шаблон Azure Resource Manager в Azure Marketplace, GitHub или в качестве краткого руководства, то процесс изменения этого шаблона для реализации нового метода будет прост.  Если вы не используете шаблон Azure Resource Manager, ниже приведено несколько ссылок, которые помогут лучше понять, что такое шаблоны Azure Resource Manager и как их создавать. 

*   [Создание и развертывание первого шаблона Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template)
*   [Руководство по созданию шаблона решения для Azure Marketplace](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-solution-template-creation)

## <a name="instructions-add-a-guid-to-your-existing-azure-resource-manager-template"></a>Инструкции: добавление GUID в существующий шаблон Azure Resource Manager

Добавление GUID представляет собой однократное изменение основного файла шаблона.
 1. Создайте GUID. Предположим, что вы создали значение eb7927c8-dd66-43e1-b0cf-c346a422063.
 2. Откройте шаблон Azure Resource Manager.
 3. Добавьте новый ресурс в основной файл шаблона. Ресурс должен быть указан только в файле mainTemplate.json или azuredeploy.json, а не в каком-либо вложенном или связанном шаблоне.
 4. Введите идентификатор GUID после префикса pid-, как показано выше.

   Должно отобразиться примерно следующее: `pid-eb7927c8-dd66-43e1-b0cf-c346a422063`.

 5. Проверьте шаблон на наличие ошибок.
 6. Повторно опубликуйте шаблон в соответствующих репозиториях.

## <a name="sample-template-code"></a>Пример кода шаблона

![](https://raw.githubusercontent.com/ellacroi/azure-docs-pr/lu-images-again-dangit-all/articles/marketplace/media/marketplace-publishers-guide/tracking-sample-code-for-lu-1.PNG?token=Ak8ZDB0JzsBdUGlKEIeHNJRS7b0BWn4Gks5bbMwwwA%3D%3D)


## <a name="method-2-azure-resource-manager-apis"></a>Метод 2. Интерфейсы API Azure Resource Manager

В некоторых случаях можно напрямую вызывать интерфейсы REST API Azure Resource Manager для развертывания служб Azure. [Azure поддерживает несколько пакетов SDK](https://docs.microsoft.com/azure/#pivot=sdkstools), обеспечивающих эту возможность.  Можно использовать один из пакетов SDK или вызывать REST API напрямую для развертывания ресурсов.

При использовании шаблона Azure Resource Manager следует пометить тегом решение, следуя инструкциям, приведенным выше.  Если вы не используете шаблон Azure Resource Manager и выполняете прямые вызовы API, вы все равно можете пометить тегом развертывание, чтобы связать с ним сведения об использовании ресурсов Azure. 

**Как добавить тег для развертывания с помощью интерфейсов API Azure Resource Manager**: в случае, если вы используете этот способ, при проектировании вызовы API будут содержать GUID в заголовке запроса агента пользователя. GUID должен быть добавлен для каждого предложения или номера SKU.  Строка должна содержать префикс pid- и созданный партнером GUID.   

![](https://raw.githubusercontent.com/ellacroi/azure-docs-pr/lu-images-again-dangit-all/articles/marketplace/media/marketplace-publishers-guide/tracking-sample-guid-for-lu-2.PNG?token=Ak8ZDDiokRcj4PJj0aMkZmfF8BdOuOTzks5bbM35wA%3D%3D)

>[!Note] 
>Формат GUID для вставки в агент пользователя: pid-eb7927c8-dd66-43e1-b0cf-c346a422063 // Введите свой идентификатор GUID после pid-.

Важно соблюдать формат строки. Если не указать префикс pid-, то запрос данных будет невозможен. Разные пакеты SDK выполняют это по-разному.  Чтобы реализовать этот метод, необходимо будет просмотреть документацию и изучить подход для выбранного пакета Azure SDK. 

**Пример с использованием пакета SDK для Python:** для Python нужно использовать атрибут config. Вы можете добавить только в агент пользователя. Вот пример: 

![](https://raw.githubusercontent.com/ellacroi/azure-docs-pr/lu-images-again-dangit-all/articles/marketplace/media/marketplace-publishers-guide/python-for-lu.PNG?token=Ak8ZDK5Um4J6oY-7x25tuBpa168BEiYMks5bbMuUwA%3D%3D)

>Это нужно сделать для каждого клиента, так как глобальная статическая конфигурация отсутствует. (Вы можете создать фабрику клиентов, чтобы обеспечить правильную настройку всех клиентов.) 
>[Дополнительная справочная информация](https://github.com/Azure/azure-cli/blob/7402fb2c20be2cdbcaa7bdb2eeb72b7461fbcc30/src/azure-cli-core/azure/cli/core/commands/client_factory.py#L70-L79).

Как добавить тег для развертывания с помощью Azure PowerShell или Azure CLI: при развертывании ресурсов с помощью Azure PowerShell для добавления GUID можно использовать следующий метод.

```

[Microsoft.Azure.Common.Authentication.AzureSession]::ClientFactory.AddUserAgent("pid-eb7927c8-dd66-43e1-b0cf-c346a422063")


```

Чтобы добавить GUID при использовании Azure CLI, задайте переменную среды AZURE_HTTP_USER_AGENT.  Переменную можно задать для сценария или глобально, для всей оболочки:

```

export AZURE_HTTP_USER_AGENT='pid-eb7927c8-dd66-43e1-b0cf-c346a422063'


```

## <a name="registering-guidsoffers"></a>Регистрация идентификаторов GUID и предложений

Чтобы GUID можно было использовать для отслеживания, его необходимо зарегистрировать.  

Регистрация всех идентификаторов GUID шаблонов будет выполняться с помощью Портала Cloud Partner (CPP) Azure Marketplace. 

1. Зарегистрируйтесь в [Azure Marketplace](http://aka.ms/listonazuremarketplace) сегодня и получите доступ к Порталу Cloud Partner.

 *  Партнеры должны будут [создать профиль в CPP](https://docs.microsoft.com/azure/marketplace/become-publisher), и им будет предложено опубликовать предложение в Azure Marketplace или AppSource. 
 *  Партнеры смогут зарегистрировать несколько идентификаторов GUID. 
 *  Партнеры также будут иметь возможность зарегистрировать GUID для предложений или шаблонов решения, расположенного не в marketplace.
 
2. Войдите на [портал Cloud Partner](https://cloudpartner.azure.com/).
3. В правом верхнем углу портала щелкните значок своей учетной записи и нажмите кнопку **Publisher profile** (Профиль издателя).

![](https://github.com/ellacroi/azure-docs-pr/blob/more-lu-images/articles/marketplace/media/marketplace-publishers-guide/guid-image-for-lu.png)

4. На странице профиля щелкните **Add Tracking GUID** (Добавить GUID отслеживания).

![](https://github.com/ellacroi/azure-docs-pr/blob/last-lu-images-i-hope/articles/marketplace/media/marketplace-publishers-guide/guid-how-to-add-tracking.png)

5. В развернутом поле **Tracking GUID**  (GUID отслеживания) укажите GUID отслеживания (только GUID, без префикса pid-), а в поле **Custom Description** (Пользовательское описание) введите имя или описание предложения.

![](https://github.com/ellacroi/azure-docs-pr/blob/more-lu-images/articles/marketplace/media/marketplace-publishers-guide/guid-dev-center-login.png)

![](https://github.com/ellacroi/azure-docs-pr/blob/more-lu-images/articles/marketplace/media/marketplace-publishers-guide/guid-dev-center-example.png)

6. Чтобы зарегистрировать более одного идентификатора GUID, повторно щелкните **Add Tracking GUID** (Добавить GUID отслеживания). Откроется другое развернутое поле. 

![](https://github.com/ellacroi/azure-docs-pr/blob/more-lu-images/articles/marketplace/media/marketplace-publishers-guide/guid-dev-center-example-add.png)

![](https://github.com/ellacroi/azure-docs-pr/blob/more-lu-images/articles/marketplace/media/marketplace-publishers-guide/guid-dev-center-example-description.png)

7. Завершив редактирование, щелкните **Save** (Сохранить), чтобы сохранить изменения. 

![](https://github.com/ellacroi/azure-docs-pr/blob/more-lu-images/articles/marketplace/media/marketplace-publishers-guide/guid-dev-center-save.png)



## <a name="verification-of-guid-deployment"></a>Проверка развертывания по GUID 

После изменения шаблона и выполнения тестового развертывания можно использовать следующий сценарий PowerShell, чтобы получать данные о ресурсах, которые были развернуты и помечены тегом. 

Его можно использовать, чтобы проверить, успешно ли добавлен GUID в шаблон Azure Resource Manager. Он не применяется к развертыванию с помощью API Azure Resource Manager.

Перед выполнением сценария войдите в Azure и выберите подписку, содержащую развертывание, которое необходимо проверить. Этот сценарий должен выполняться в контексте подписки развертывания.

GUID и имя группы ресурсов развертывания являются необходимыми параметрами.

Исходный сценарий можно найти [здесь](https://gist.github.com/bmoore-msft/ae6b8226311014d6e7177c5127c7eba1#file-verify-deploymentguid-ps1).

```

Param(
    [GUID][Parameter(Mandatory=$true)]$guid,
    [string][Parameter(Mandatory=$true)]$resourceGroupName'
)

#get the correlationId of the pid deployment

$correlationId = (Get-AzureRmResourceGroupDeployment -ResourceGroupName 
$resourceGroupName -Name "pid-$guid").correlationId

#find all deployments with that correlationId

$deployments = Get-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName | Where-Object{$_.correlationId -eq $correlationId}

#find all deploymentOperations in a deployment by name (since PowerShell does not surface outputResources on the deployment or correlationId on the deploymentOperation)

foreach ($deployment in $deployments){

#get deploymentOperations by deploymentName and then the resourceId for any create operation

($deployment | Get-AzureRmResourceGroupDeploymentOperation | Where-Object{$_.properties.provisioningOperation -eq "Create" -and $_.properties.targetResource.resourceType -ne "Microsoft.Resources/deployments"}).properties.targetResource.id

}


```

## <a name="guidance-on-creating-guids"></a>Руководство по созданию идентификаторов GUID

GUID (глобальный уникальный идентификатор) — это уникальный 32-разрядный шестнадцатеричный ссылочный номер. Чтобы создать идентификаторы GUID для отслеживания, следует использовать генератор GUID.  Для использования доступно несколько [генераторов GUID в сети](https://www.bing.com/search?q=guid%20generator&qs=n&form=QBRE&sp=-1&ghc=2&pq=guid%20g&sc=8-6&sk=&cvid=0BAFAFCD70B34E4296BB97FBFA3E1B4E).

Рекомендуется создавать уникальные GUID для всех предложений и каналов распространения.  Например, если у вас имеются два решения, которые развертываются с помощью шаблона и доступны в Azure Marketplace и на сайте GitHub.  Создайте четыре GUID:

*   для предложения А в Azure Marketplace; 
*   для предложения А на сайте GitHub;
*   для предложения Б в Azure Marketplace; 
*   для предложения Б на сайте GitHub.

Отчеты будут создаваться на основании данных партнера (ИД партнера Майкрософт) и GUID. 

Можно также отслеживать идентификаторы GUID на более детальном уровне, т. е. на уровне номера SKU (где номера SKU представляют собой варианты предложения).

## <a name="guidance-on-privacy-and-data-collection"></a>Рекомендации по конфиденциальности и сбору данных

Партнеры должны сообщать клиентам о том, что развертывания с отслеживанием GUID Azure Resource Manager позволяют корпорации Майкрософт создавать для партнера отчеты об использовании ресурсов Azure, связанных с этими развертываниями.  Ниже приведен пример такого сообщения. Там, где указано "Партнер", нужно указать имя вашей компании. Кроме того, партнерам следует убедиться, что язык соответствует их собственным политикам конфиденциальности и сбора данных, и у клиентов должна быть возможность отключить отслеживание. 

**Для развертываний с помощью шаблонов Azure Resource Manager**

В случае развертывания этого шаблона корпорация Майкрософт сможет выявить установку программного обеспечения партнеров и развернутые ресурсы Azure.  Корпорация Майкрософт будет иметь возможность сопоставлять ресурсы Azure, используемые для поддержки этого программного обеспечения.  Корпорация Майкрософт собирает эти сведения, чтобы максимально повысить качество использования своих продуктов и делового сотрудничества. Эти данные будут собираться и регулироваться политиками конфиденциальности корпорации Майкрософт, которые можно найти по адресу https://www.microsoft.com/trustcenter. 

**Для развертываний с помощью пакета SDK или API**

В случае развертывания программного обеспечения партнеров корпорация Майкрософт сможет выявить установку программного обеспечения партнеров и развернутые ресурсы Azure.  Корпорация Майкрософт будет иметь возможность сопоставлять ресурсы Azure, используемые для поддержки этого программного обеспечения.  Корпорация Майкрософт собирает эти сведения, чтобы максимально повысить качество использования своих продуктов и делового сотрудничества. Эти данные будут собираться и регулироваться политиками конфиденциальности корпорации Майкрософт, которые можно найти по адресу https://www.microsoft.com/trustcenter.

## <a name="support"></a>Поддержка

Чтобы получить помощь, выполните следующие действия.
 1. Посетите страницу службы поддержки: [go.microsoft.com/fwlink/?linkid=844975](https://go.microsoft.com/fwlink/?linkid=844975).
 2. Для устранения проблем с сопоставлением данных об использовании выберите тип проблемы **Marketplace Onboarding** (Подключение к MARKETPLACE) и категорию **Other** (Другое), затем щелкните **Start Request** (Отправить запрос). 

Для проблем с доступом к Порталу Cloud Partner Azure Marketplace выберите тип проблемы **Marketplace Onboarding** (Подключение к MARKETPLACE) и категорию **Access Problem** (Проблема с доступом), затем нажмите кнопку **Start Request** (Отправить запрос).

 ![](https://github.com/ellacroi/azure-docs-pr/blob/last-lu-images-i-hope/articles/marketplace/media/marketplace-publishers-guide/lu-article-incident.png)
 

 3. Заполните обязательные поля на следующей странице и щелкните **Continue** (Продолжить).
 4. Заполните свободные текстовые поля на следующей странице. **Важно!** Укажите название инцидента **ISV Usage Tracking** (Отслеживание использования продукта независимого поставщика программного обеспечения) и подробно опишите свою проблему в большом текстовом поле ниже.  Заполните остальные поля формы и щелкните **Submit** (Отправить). 
 
  ![](https://github.com/qianw211/azure-docs-pr/blob/MyImgAdded-2/articles/marketplace/media/marketplace-publishers-guide/guid-dev-center-help.png)

 

## <a name="faqs"></a>Часто задаваемые вопросы

**В чем заключается преимущество добавления GUID в шаблон?**

Корпорация Майкрософт предоставит партнерам представление клиентских развертываний с помощью их шаблонов, а также важные сведения об оказавшем на них влияние использовании.  Корпорация Майкрософт и партнер также могут использовать эту информацию, чтобы обеспечить более плотное взаимодействие между командами по продажам. Корпорация Майкрософт и партнер также могут использовать ее для получения более целостного представления о влиянии отдельных партнеров на использование ресурсов Azure. 

**Кто может добавить GUID в шаблон?**

Отслеживаемый ресурс должен связать решение партнера с данными об использовании ресурсов Azure клиентами.  Данные об использовании привязаны к удостоверению партнера Microsoft Partner Network (MPN ID), и для партнеров на Портале Cloud Partner (CPP) будут доступны отчеты.  

**Можно ли изменить добавленный GUID?**
 
Да, клиент или партнер по реализации может настроить шаблон и изменить или удалить GUID. Мы рекомендуем партнерам, чтобы они заранее описывали роль ресурса и GUID для своих клиентов и партнеров, чтобы предотвратить удаление или изменение GUID для отслеживания.  Изменение GUID влияет только на новые, а не на существующие развертывания и ресурсы.

**Когда будут доступны отчеты?**

В скором времени будет выпущена бета-версия функции создания отчетов.  Отчеты будут интегрированы в Портал Cloud Partner (CPP).

**Можно ли отслеживать шаблоны, развертываемые из репозитория сторонних производителей, например GitHub?**

Да, если в развертываемом шаблоне указан GUID, данные об использовании будут отслеживаться.  
Партнерам необходим профиль на Портале Cloud Partner для регистрации связанных шаблонов, опубликованных за пределами Azure Marketplace. 

**Есть ли отличия при развертывании шаблона из Azure Marketplace и из других репозиториев, таких как GitHub?**

Да, партнеры, публикующие предложения в Azure Marketplace, могут получать более подробные данные о развертываниях из Azure Marketplace.  Партнеры получат преимущества, предоставляя предложение клиентам на портале Azure Marketplace и на портале управления Azure. Предложения в Azure Marketplace также привлекают интересы для партнера.

**Что будет, если создать пользовательский шаблон для привлечения клиентов?**

Вы все равно можете добавить GUID в шаблон.  Если вы используете существующий GUID, который был зарегистрирован, он будет включен в отчеты.  Если вы создаете новый GUID, его потребуется зарегистрировать, чтобы использовать для отслеживания.

**Получает ли клиент отчеты?**

В настоящее время клиенты могут отслеживать использование отдельных ресурсов или групп ресурсов, определенных клиентом, на портале управления Azure.   

**Похожа ли эта методика отслеживания на зарегистрированный цифровой партнер (DPOR)?**

Это новый метод привязки развертывания и данных об использовании к решению партнера, который дает возможность связать решение партнера с данными об использовании ресурсов Azure.  DPOR предназначен для связывания партнера, предоставляющего услуги консультирования (системный интегратор) или управления (поставщик управляемых служб), с подпиской Azure клиента.   
