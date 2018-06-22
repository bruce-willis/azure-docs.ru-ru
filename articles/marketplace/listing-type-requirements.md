---
title: Требования по типам перечислений | Azure
description: В этой статье описываются условия и требования, которые следует принимать во внимание партнерам при публикации приложений в Azure Marketplace.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: jm-aditi-ms
manager: pabutler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 06/05/2018
ms.author: ellacroi
ms.openlocfilehash: dbb14854f00fb133c3604a1dd529d42120371fd2
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/06/2018
ms.locfileid: "34826292"
---
# <a name="requirements-by-listing-type"></a>Требования по типам приложений  
Технические требования и требования к маркетинговому содержимому различаются в зависимости от онлайн-магазина, типа предложения и типа приложения. Ознакомьтесь со следующими характеристиками для проверки соответствия.  
1. Требования онлайн-магазина:  
    *   [AppSource](#storefront-requirements:-appSource)  
    *   [Azure Marketplace](#storefront-requirements:-azure-marketplace)  
2. Требования к типу приложения и типу предложения:  
    *   Дополнительные сведения о типах приложений и типах предложений см. в статье "Определение типа приложения, соответствующего предложению" по адресу [docs.microsoft.com/ru-ru/azure/marketplace/determine-your-listing-type](./determine-your-listing-type.md).  

## <a name="storefront-requirements-appsource"></a>Требования онлайн-магазина: AppSource  
В следующей таблице описаны предварительные требования для публикации на AppSource.  
| Требование | Сведения | Обязательная или рекомендуемая |  
|:--- |:--- |:--- |  
| ***Azure Active Directory (Azure AD).*** | В вашем приложении должен предоставляться федеративный единый вход в Azure Active Directory с включенным запросом на согласие.<ul> <li>Дополнительные сведения о включении федеративного единого входа в Azure AD см. в статье "Настройка единого входа для приложений, которых нет в коллекции приложений Azure Active Directory" по адресу [docs.microsoft.com/ru-ru/azure/active-directory/active-directory-saas-custom-apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps).</li> </ul> | Обязательно |   
| ***Интеграция с облачными службами Майкрософт*** | Ваше приложение должно интегрироваться с другими облачными службами Майкрософт, такими как Microsoft Power BI, Cortana Intelligence, или службами Microsoft Azure.<ul> <li>Пример службы Microsoft Cloud — Интернет вещей.</li> </ul> | Рекомендуется |  
| ***Аудитория*** | Приложение должно быть предназначено для бизнес-пользователей и владельцев предприятий. | Обязательно | 
| ***Приложение программного обеспечения как услуги (SaaS) для бизнеса*** | Приложение должно соответствовать следующим требованиям:<ul> <li>быть бизнес-приложением SaaS;</li> <li>предназначаться для бизнес-процессов;</li> <li>предназначаться для бизнес-клиентов;</li> <li>позволять пользователям входить с использованием рабочих учетных данных, таких как имя пользователя и пароль.</li> </ul> | Обязательно |  
| ***Бесплатный пробный период и пробная версия*** | Приложение должно содержать один из следующих параметров, чтобы клиент мог им пользоваться бесплатно в течение ограниченного времени.<ul> <li>Укажите метод `try`, чтобы клиенты могли запускать пробную версию приложения в AppSource</li> <li>Укажите параметр `request trial` в AppSource, чтобы клиенты могли запросить пробную версию приложения</li> </ul>Предоставляемая вами бесплатная пробная версия должна предусматривать предопределенное количество времени на тестирование приложения клиентом без дополнительных затрат. | Обязательно |  
| ***Легко настраиваемое, готовое к использованию решение*** | Ваше приложение должно быть простым и быстрым в установке и настройке (без необходимости в специальной настройке). | Обязательно |  
| ***Управление потенциальными клиентами*** | Перед получением данных о потенциальных клиентах из Marketplace необходимо включить CRM для приема этих данных.<ul> <li>Примеры CRM — Marketo, Microsoft Dynamics или Salesforce</li> </ul> | Обязательно |  
| ***Политика конфиденциальности и условия использования*** | Приложение должно предоставлять ссылку на страницу политики конфиденциальности с использованием общедоступного URL-адреса. Условия использования должны быть предоставлены во время публикации в текстовом виде. | Обязательно |  
| ***Поддержка*** | Приложение должно предоставлять ссылку на страницу поддержки клиентов с использованием общедоступного URL-адреса. Если для приложения предусмотрена бесплатная пробная версия, поддержка должна предоставляться бесплатно в течение пробного периода. | Обязательно |  

## <a name="storefront-requirements-azure-marketplace"></a>Требования онлайн-магазина: Azure Marketplace  
Ниже приведены предварительные требования для типов приложений в Azure Marketplace.  
| Требование | Сведения | Тип приложения |  
|:--- |:--- |:--- |  
| ***Политики участия*** | Приложение должно соответствовать политикам участия Azure Marketplace.<ul> <li>Дополнительные сведения о политиках участия см. в статье "Политики участия в Microsoft Azure Marketplace" по адресу [azure.microsoft.com/ru-ru/support/legal/marketplace/participation-policies](https://azure.microsoft.com/support/legal/marketplace/participation-policies).</li></ul> | list<br />transact<br />trial |  
| ***Интеграция с Майкрософт*** | Предложения должны использовать или расширять типы служб Microsoft Azure (службы вычисления, сети или хранилища). Ваше предложение должно соответствовать существующей категории Azure Marketplace, такой как базы данных, безопасность или сетевое взаимодействие.<ul> <li>Дополнительные сведения о предложениях Marketplace см. в статье "Приложения Marketplace" по адресу [azuremarketplace.microsoft.com/marketplace/apps](https://azuremarketplace.microsoft.com/marketplace/apps).</li> </ul> | list<br />transact<br />trial |  
| ***Аудитория*** | Ваше предложение должно быть предназначено для ИТ-специалистов, разработчиков облака или других технических специалистов. | list<br />transact<br />trial |  
| ***Управление потенциальными клиентами*** | Перед получением данных о потенциальных клиентах из Marketplace необходимо включить CRM (Marketo, Microsoft Dynamics или Salesforce) для приема этих данных. | list<br />transact<br />trial |  
| ***Политика конфиденциальности и условия использования*** | Приложение должно предоставлять ссылку на страницу политики конфиденциальности с использованием общедоступного URL-адреса. Условия использования должны быть предоставлены во время публикации в текстовом виде. | list<br />transact<br />trial |  
| ***Поддержка*** | Предложение должно предоставлять ссылку на страницу поддержки клиентов с использованием общедоступного URL-адреса. Если для предложения предусмотрена бесплатная пробная версия, поддержка должна предоставляться бесплатно в течение пробного периода. | transact<br />trial |    

## <a name="non-transact-listings"></a>Приложения нетранзакционного типа  
В этом разделе описываются все остальные типы предложений, которые не используют тип приложения Transact. 

### <a name="list"></a>список  
Тип приложения "Список" включает в себя следующие типы предложений из магазинов на рынке.  

| Тип предложения | Онлайн-магазин | Сведения |  
|:---        |:---        |:---     |  
| Консультационные услуги | AppSource | [Требования — AppSource — Список — Консультационные услуги](#requirements:-appsource:-list:-consulting-services) |  
| Консультационные услуги | Azure Marketplace | [Требования — Azure Marketplace — Список — Консультационные услуги](#requirements:-azure-marketplace:-list:-consulting-services) |  
| Свяжитесь со мной | AppSource | [](#) |  
| Свяжитесь со мной | Azure Marketplace | [Требования — AppSource — Список — Свяжитесь со мной](#requirements:-azure-marketplace:-list:-contact-me) |  

#### <a name="requirements-appsource-list-consulting-service"></a>Требования — AppSource — Список — Консультационная услуга  

| Требования | Сведения |  
|:--- |:--- |  
| Характеристики предложения услуг | Консультационная услуга должна соответствовать следующим критериям.<ul> <li>Поставляться в фиксированном объеме, с фиксированной длительностью, по фиксированной цене (или бесплатно).</li> <li>Ориентироваться в первую очередь на предпродажу.</li> <li>Предназначаться для одного клиента.</li> <li>Оказываться на месте.</li> </ul> |  
| Требования к партнерам по разработке консультационных услуг | Вы соответствуете критериям в соответствующей области ваших услуг.<table><tr><th>Область решения</th><th>Критерии</th></tr><tr><td>Dynamics 365 for Customer Engagement</td><td>Наличие компетенции Cloud Customer Relationship Management уровня Silver или Gold.</td></tr><tr><td>Dynamics 365 for Finance and Operations (корпоративный выпуск)</td><td>Наличие компетенции Enterprise Resource Planning уровня Silver или Gold и дохода от облачных операций за последние 12 месяцев размером 25 000 долларов США или более.</td></tr><tr><td>Dynamics 365 for Finance and Operations (выпуск для бизнеса)</td><td>Статус поставщика облачных служб (CSP) или Digital Partner of Record (DPOR) для одного или нескольких клиентов.</td></tr><tr><td>Power BI</td><td>Соответствие критериям партнера по решению.</td></tr><tr><td>PowerApps.</td><td>Наличие решения презентации партнера.</td></tr></table><ul> <li>Дополнительные сведения об управлении взаимоотношениями с клиентами см. в статье "Компетенция Cloud Customer Relationship Management" по адресу [partner.microsoft.com/ru-RU/membership/cloud-customer-relationship-management-competency](https://partner.microsoft.com/membership/cloud-customer-relationship-management-competency).</li> <li>Дополнительные сведения о планировании ресурсов см. в статье "Enterprise Resource Planning" по адресу [partner.microsoft.com/ru-RU/membership/enterprise-resource-planning-competency](https://partner.microsoft.com/membership/enterprise-resource-planning-competency).</li> <li>Дополнительные сведения о CSP см. в статье "Поставщик облачных решений" по адресу [partner.microsoft.com/ru-RU/cloud-solution-provider](https://partner.microsoft.com/cloud-solution-provider).</li> <li>Дополнительные сведения о DPOR см. в статье "Статус Digital Partner of Record и партнерская ассоциация" по адресу [partner.microsoft.com/membership/digital-partner-of-record](https://partner.microsoft.com/membership/digital-partner-of-record).</li> <li>Дополнительные сведения о критериях партнеров по решениям см. в статье "Solution Partner Overview and Incentives" (Обзор и вознаграждение партнеров по решениям) по адресу [www.microsoftpartnerserverandcloud.com/_layouts/download.aspx? SourceUrl=Hosted%20Documents/Power%20BI%20Program%20Overview%20%26%20Incentives.pdf](https://www.microsoftpartnerserverandcloud.com/_layouts/download.aspx?SourceUrl=Hosted%20Documents/Power%20BI%20Program%20Overview%20%26%20Incentives.pdf).</li> <li>Дополнительные сведения о партнерской презентации см. на странице презентаций партнеров по адресу [powerapps.microsoft.com/ru-ru/partner-showcase/](https://powerapps.microsoft.com/partner-showcase).</li> </ul> |  

#### <a name="requirements-azure-marketplace-list-consulting-service"></a>Требования — Azure Marketplace — Список — Консультационная услуга  

| Требования | Сведения |  
|:--- |:--- |  
| Характеристики предложения услуг | Консультационная услуга должна соответствовать следующим критериям.<ul> <li>Поставляться в фиксированном объеме, с фиксированной длительностью, по фиксированной цене (или бесплатно).</li> <li>Ориентироваться в первую очередь на предпродажу.</li> <li>Предназначаться для одного клиента.</li> <li>Оказываться на месте.</li> </ul> |  
| Требования к партнерам по разработке консультационных услуг | Необходимо обладать одной из указанных ниже компетенций уровня Silver или Gold в соответствующей области услуг. <table><tr><th>Область решения</th><th>Компетенция</th></tr><td>Облачная платформа и инфраструктура</td><td>Облачная платформа<br />Центр обработки данных</td><tr><td>Разработка приложений и независимый поставщик программного обеспечения</td><td>Разработка приложений<br />Интеграция приложений<br />DevOps</td></tr><tr><td>Управление данными и аналитика</td><td>Аналитика данных<br />Платформа данных</td></tr></table><ul> <li>Дополнительные сведения о компетенциях см. в статье "Competencies Through Microsoft Partner Network" (Компетенции через сеть партнеров Майкрософт) по адресу [partner.microsoft.com/ru-RU/membership/competencies](https://partner.microsoft.com/membership/competencies).</li> <li>Дополнительные сведения о публикации приложений см. в статье "Консультационные услуги в Azure Marketplace" по адресу [docs.microsoft.com/ru-ru/azure/marketplace/consulting-services](https://docs.microsoft.com/azure/marketplace/consulting-services).</li></ul> |  

<!-- #### Requirements: Azure Marketplace: List: Contact Me -->

---   

### <a name="trial"></a>Пробная версия  

| Тип предложения | Онлайн-магазин | Сведения |  
|:---        |:---        |:---     |  
| Бесплатная или пробная версия SaaS | AppSource | [Требования к типу приложения: Пробная версия](#listing-type-requirements:-trial) |  
| Бесплатная или пробная версия SaaS | Azure Marketplace | [Требования — Azure Marketplace — Пробная версия — Бесплатная пробная версия или пробная версия SaaS](#requirements:-azure-marketplace:-trial:-free-trial-/-saas-trial) |  
| Интерактивная демоверсия | AppSource | [Требования к типу приложения — Пробная версия](#listing-type-requirements:-trial) |  
| Интерактивная демоверсия | Azure Marketplace | [Требования — Azure Marketplace — Пробная версия — Интерактивная демонстрация](#requirements:-azure-marketplace:-trial:-interactive-demo) |  
| Тестовый выпуск | AppSource | [Требования к типу приложения — Пробная версия](#listing-type-requirements:-trial) |  
| Тестовый выпуск | Azure Marketplace | [Требования — Azure Marketplace — Пробная версия — Тестовый выпуск](#requirements:-azure-marketplace:-trial:-test-drive) |  

#### <a name="requirements-azure-marketplace-trial"></a>Требования — Azure Marketplace — Пробная версия  

| Требование | Сведения |  
|:--- |:--- |  
| Бесплатный пробный период и пробная версия | Клиент может использовать ваше приложение бесплатно в течение ограниченного времени.<br /><br />Клиенту не нужно платить за лицензию или подписку на ваше предложение или приложение. Клиенту не нужно платить за собственный базовый продукт или услугу Майкрософт. Все варианты пробных версий развертываются в подписке Azure. У вас имеется единоличный контроль над пробной версией по оптимизации затрат и управлению.<br /><br />Вы можете выбрать бесплатную пробную версию, интерактивную демонстрацию или тестовый выпуск. Независимо от того, что вы выберете, бесплатная пробная версия должна предусматривать предопределенное количество время на тестирование приложения клиентом без дополнительных затрат.<ul> <li>Чтобы создать тестовый выпуск, сначала отправьте сообщение электронной почты по адресу [amp-testdrive@microsoft.com](mailto:amp-testdrive@microsoft.com).</li> </ul>Примечание. Пробные версии SaaS-решений Azure Marketplace должны предусматривать возможность входа клиентов с учетными данными для работы.<ul> <li>Дополнительные сведения см. в разделе "Пробные версии возможностей AppSource" по адресу [ocs.microsoft.com/ru-ru/azure/active-directory/develop/active-directory-devhowto-appsource-certified#appsource-trial-experiences](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified#appsource-trial-experiences).</li> </ul> |  
| Легко настраиваемое, готовое к использованию решение | Ваше приложение должно быть простым и быстрым в установке и настройке. |  
| Доступность и время непрерывной работы | Время непрерывной работы вашего приложение или платформы SaaS должно составлять не менее 99,9 %. |  
| Azure Active Directory | В вашем предложении должен предоставляться федеративный единый вход (SSO) в Azure Active Directory (Azure AD) с включенным запросом на согласие. |  

#### <a name="requirements-azure-marketplace-trial-free-trial--saas-trial"></a>Требования — Azure Marketplace — Пробная версия — Бесплатная пробная версия или пробная версия SaaS  

| Преимущество | Требование |  
|:--- |:--- |  
| Позволяет клиенту опробовать ваш продукт перед покупкой с помощью автоматизированного метода перехода на платную версию. Также дает возможность выполнить процесс подтверждения концепции и взаимодействовать с командами продаж Майкрософт. | Ваше решение — виртуальная машина или шаблон решения.<br /><br />Ваше решение — предложение SaaS с мультитенантным продуктом SaaS.<br /><br />У вас есть компонент для начала работы и быстрой настройки для клиента.<br /><br />Вы используете однотенантное приложение, но добавляете клиентов в качестве гостевых пользователей. |  

#### <a name="requirements-azure-marketplace-trial-interactive-demo"></a>Требования — Azure Marketplace — Пробная версия — Интерактивная демонстрация  

| Преимущество | Требование |  
|:--- |:--- |  
| Позволяет клиентам просматривать ваше решение в действии без сложных настроек. | Ваше решение требует сложных настроек, которые сложно выполнить в течение пробного периода. |  

#### <a name="requirements-azure-marketplace-trial-test-drive"></a>Требования — Azure Marketplace — Пробная версия — Тестовый выпуск  

| Преимущество | Требование |  
|:--- |:--- |  
| Позволяет клиенту опробовать ваш продукт перед покупкой.<br /><br />Предоставляет возможность интерактивного взаимодействия с вашим решением с предварительно настроенными параметрами.<br /><br />Ниже приведены дополнительные преимущества при использовании тестового выпуска.<ul> <li>27 % запросов пользователей в Marketplace уточняются пользователями таким образом, чтобы отображались только предложения с тестовыми выпусками.</li> <li>Предложения с тестовыми выпусками позволяют получить на 38 % больше потенциальных клиентов, чем предложения без них.</li> <li>36 % новых клиентов в Marketplace являются клиентами, которые протестировали предложение.</li> <li>Тестовые выпуски позволяют специалистам по реализации Майкрософт досконально анализировать продукт для внедрения инициатив по совместным продажам.</li> </ul> | Ваше решение — виртуальная машина, шаблон решения, однотенантное приложение SaaS. Ваше решение сложно развернуть. <br /><br />У вас нет способа преобразовать пробную версию в платное предложение. |  

---

## <a name="transact-specific-listings"></a>Приложения для транзакций

### <a name="transact"></a>"Транзакция"  

| Тип предложения | Онлайн-магазин | Сведения |   
|:---        |:---        | :--- |  
| Приложения Azure — Управляемое приложение | Azure Marketplace | [Требования — Azure Marketplace — Транзакция — Приложения Azure — Управляемое приложение](#requirements:-azure-marketplace:-transact:-azure-apps:-managed-app) |  
| Приложения Azure — Шаблон решения | Azure Marketplace | [Требования — Azure Marketplace — Транзакция — Приложения Azure — Шаблон решения](#requirements:-azure-marketplace:-transact:-azure-apps:-solution-template) |  
| Контейнеры | Azure Marketplace | [Требования — Azure Marketplace — Транзакция — Контейнер](#requirements:-azure-marketplace:-transact:-container) |  
| Приложение SaaS  | Azure Marketplace | [Требования — Azure Marketplace — Транзакция — Приложение SaaS](#requirements:-azure-marketplace:-transact:-saas-app) |  
| Виртуальная машина. | Azure Marketplace | [Требования — Azure Marketplace — Транзакция — Виртуальная машина](#requirements:-azure-marketplace:-transact:-virtual-machine) |  

<!-- #### Requirements: Azure Marketplace: Transact: Azure apps: Managed app  

#### Requirements: Azure Marketplace: Transact: Azure apps: Solution template   -->

#### <a name="requirements-azure-marketplace-transact-container"></a>Требования — Azure Marketplace — Транзакция — Контейнер  

| Требование | Сведения |  
|:--- |:--- |  
| Выставление счетов и ценообразование | Поддерживается либо бесплатная, либо модель выставления счетов BYOL. |  
| Образ на основе Docker | Образ контейнера должен быть основан на формате образа Docker и извлечен из реестров контейнеров Azure. |  

#### <a name="requirements-azure-marketplace-transact-saas-app"></a>Требования — Azure Marketplace — Транзакция — Приложение SaaS  

| Требование | Сведения |  
|:--- |:--- |  
| Выставление счетов и ценообразование | Цена вашего предложения определяется помесячной фиксированной ставкой. Возможности оплаты по мере использования и *повышения уровня* на основе использования сейчас не поддерживаются. |  
| Отмена | Ваше предложение может быть отменено клиентом в любое время. |  
| Целевая страница транзакции | Разместите целевую страницу транзакции с совместным брендингом Azure. Целевая страница позволяет клиентам создать учетную запись службы SaaS и управлять ею. |  
| API подписки на SaaS | Предоставляется служба, которая взаимодействует с подпиской SaaS для создания, обновления и удаления учетной записи пользователя и плана обслуживания. Все критические изменения в API должны поддерживаться в течение 24 часов. Любые некритические изменения в API обновляются периодически. |  

#### <a name="requirements-azure-marketplace-transact-virtual-machine"></a>Требования — Azure Marketplace — Транзакция — Виртуальная машина  

| Требование | Сведения |  
|:--- |:--- | 
| Выставление счетов и ценообразование | Виртуальная машина должна поддерживать ежемесячное выставление счетов в режиме BYOL или оплаты по мере использования. |  
| Совместимый с Azure виртуальный жесткий диск | Виртуальные машины должны быть созданы на платформе Windows или Linux.<ul> <li>Дополнительные сведения о создании виртуального жесткого диска Linux см. в разделе "Создание виртуального жесткого диска, совместимого с Azure (для Linux)" по адресу [docs.microsoft.com/ru-ru/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#2-create-an-azure-compatible-vhd-linux-based](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#2-create-an-azure-compatible-vhd-linux-based).</li> <li>Дополнительные сведения о создании виртуального жесткого диска Windows см. в разделе "Создание виртуального жесткого диска, совместимого с Azure (на основе Windows)" по адресу [docs.microsoft.com/ru-ru/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#3-create-an-azure-compatible-vhd-windows-based](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#3-create-an-azure-compatible-vhd-windows-based).</li> </ul> |  

## <a name="next-steps"></a>Дополнительная информация
*   Посетите страницу [Руководство по публикации в Azure Marketplace и AppSource](./marketplace-publishers-guide.md).  
 
---  
