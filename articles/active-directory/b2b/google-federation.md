---
title: Добавление Google в качестве поставщика удостоверений для Azure Active Directory B2B | Документация Майкрософт
description: Установите федерацию с Google, чтобы гостевые пользователи входили в свои приложения Azure AD с помощью учетных записей Gmail.
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 08/20/2018
ms.author: mimart
author: msmimart
manager: mtillman
ms.reviewer: mal
ms.openlocfilehash: d36a4071dbbfb52e22a4e0ecc850da68ebeae6e5
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/24/2018
ms.locfileid: "42888123"
---
# <a name="add-google-as-an-identity-provider-for-b2b-guest-users"></a>Добавление Google в качестве поставщика удостоверений для гостевых пользователей B2B

Настроив федерацию с Google, можно разрешить приглашенным пользователям входить в общие приложения и ресурсы с помощью собственных учетных записей Google без необходимости создавать учетные записи Майкрософт (MSA) или Azure AD.  
> [!NOTE]
> Ваши гостевые пользователи Google должны входить в систему, используя ссылку, которая содержит контекст клиента, например `https://myapps.microsoft.com/?tenantid=<tenant id>`. Прямые ссылки на приложения и ресурсы также работают, если они содержат контекст клиента. В настоящее время пользователи не могут войти в систему, используя конечные точки, которые не имеют контекста клиента. Например, использование `https://myapps.microsoft.com`, `https://portal.azure.com` или общей конечной точки команды приведет к ошибке.
 
## <a name="what-is-the-experience-for-the-google-user"></a>Как эта возможность выглядит для пользователя Google?
Когда вы отправляете приглашение пользователю Google Gmail, гостевой пользователь должен получить доступ к вашим общим приложениям или ресурсам, используя ссылку, которая содержит контекст клиента. Способ взаимодействия с пользователем зависит от того, выполнил ли он вход в Google:
  - Если гостевой пользователь не выполнил вход в Google, ему предлагается выполнить его.
  - Если гостевой пользователь уже зарегистрирован в Google, ему будет предложено выбрать учетную запись, которую он хочет использовать. Он должен выбрать учетную запись, которую вы использовали, чтобы пригласить его.

Если гостевой пользователь видит ошибку, связанную со слишком длинным заголовком, можно попытаться очистить файлы cookie или открыть окно в анонимном режиме и повторить попытку входа.

![Вход с учетными данными Google](media/google-federation/google-sign-in.png)

## <a name="step-1-configure-a-google-developer-project"></a>Шаг 1. Настройка проекта разработчика Google
Сначала создайте новый проект в консоли разработчиков Google, чтобы получить идентификатор и секрет клиента, которые впоследствии можно добавить в Azure AD. 
1. Перейдите к API-интерфейсам Google на странице https://console.developers.google.com и войдите с помощью учетной записи Google. Мы рекомендуем использовать общую учетную запись Google, используемую командой.
2. Создайте новый проект: на панели мониторинга выберите **Создать проект**, а затем нажмите кнопку **Создать**. На странице "Создание проекта" введите **имя проекта**, а затем нажмите кнопку **Создать**.
   
   ![Новый проект Google](media/google-federation/google-new-project.png)

3. Убедитесь, что в меню проектов выбран ваш новый проект. Затем откройте меню в верхнем левом углу и выберите **API и сервисы** > **Учетные данные**.

   ![Учетные данные Google API](media/google-federation/google-api.png)
 
4. Щелкните вкладку **Окно запроса доступа OAuth** и введите значение в поле **Название продукта, которое видят пользователи**. (Оставьте другие параметры без изменений.) Щелкните **Сохранить**.

   ![Google: окно запроса доступа OAuth](media/google-federation/google-oauth-consent-screen.png)

5. Щелкните вкладку **Учетные данные**. В меню **Создать учетные данные** выберите **Идентификатор клиента OAuth**.

   ![Учетные данные Google API](media/google-federation/google-api-credentials.png)

6. В разделе **Тип приложения** выберите **Веб-приложение**, а затем в поле **Разрешенные URI перенаправления** ведите следующие URI:
   - `https://login.microsoftonline.com` 
   - `https://login.microsoftonline.com/te/<directory id>/oauth2/authresp` <br>(где `<directory id>` является идентификатором каталога).
   
    > [!NOTE]
    > Чтобы найти идентификатор каталога, перейдите к https://portal.azure.com, в разделе **Azure Active Directory** выберите **Свойства** и скопируйте **идентификатор каталога**.

   ![Создание идентификатора клиента OAuth](media/google-federation/google-create-oauth-client-id.png)

7. Нажмите кнопку **Создать**. Скопируйте идентификатор и секрет клиента, которые будут использоваться при добавлении поставщика удостоверений на портале Azure AD.

   ![Получение идентификатора клиента и секрета клиента OAuth](media/google-federation/google-auth-client-id-secret.png)

## <a name="step-2-configure-google-federation-in-azure-ad"></a>Шаг 2. Настройка федерации с Google в Azure AD 
Теперь необходимо установить идентификатор и секрет клиента Google, введя их на портале Azure AD или с помощью PowerShell. Не забудьте проверить конфигурацию федерации Google. Для этого пригласите себя, используя адрес электронной почты Gmail, и попробуйте активировать приглашение с помощью учетной записи Google, указанной в приглашении. 

#### <a name="to-configure-google-federation-in-the-azure-ad-portal"></a>Настройка федерации с Google на портале Azure AD 
1. Перейдите на [портал Azure](https://portal.azure.com). В области слева выберите **Azure Active Directory**. 
2. Выберите **Организационные связи**.
3. Выберите **Поставщики удостоверений**, а затем нажмите кнопку **Google**.
4. Введите имя. Затем введите идентификатор и секрет клиента, полученные ранее. Щелкните **Сохранить**. 

   ![Добавление поставщика удостоверений Google](media/google-federation/google-identity-provider.png)

#### <a name="to-configure-google-federation-by-using-powershell"></a>Настройка федерации Google с помощью PowerShell
1. Установите последнюю версию модуля Azure AD PowerShell для Graph ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)).
2. Выполните следующую команду: `Connect-AzureAD`.
3. При появлении запроса на вход войдите с помощью управляемой учетной записи глобального администратора.  
4. Выполните следующую команду: 
   
   `New-AzureADMSIdentityProvider -Type Google -Name Google -ClientId [Client ID] -ClientSecret [Client secret]`
 
   > [!NOTE]
   > Используйте идентификатор и секрет клиента из приложения, созданного в разделе "Шаг 1. Настройка проекта разработчика Google". Дополнительные сведения см. в статье [New-AzureADMSIdentityProvider](https://docs.microsoft.com/en-us/powershell/module/azuread/new-azureadmsidentityprovider?view=azureadps-2.0-preview). 
 
## <a name="how-do-i-remove-google-federation"></a>Как удалить федерацию с Google?
Вы можете удалить настройку федерации с Google. Если вы это сделаете, пользователи Google, которые уже активировали свое приглашение, не смогут войти в систему, но вы можете снова предоставить им доступ к своим ресурсам, удалив их из каталога и повторно пригласив их. 
 
### <a name="to-delete-google-federation-in-the-azure-ad-portal"></a>Чтобы удалить федерацию с Google на портале Azure AD: 
1. Перейдите на [портал Azure](https://portal.azure.com). В области слева выберите **Azure Active Directory**. 
2. Выберите **Организационные связи**.
3. Выберите **Поставщики удостоверений**, а затем нажмите кнопку **Google**.
4. Выберите **Google**, а затем — **Удалить**. 
   
   ![Удаление поставщика удостоверений социальных сетей](media/google-federation/google-social-identity-providers.png)

1. Выберите **Да**, чтобы подтвердить удаление. 

### <a name="to-delete-google-federation-by-using-powershell"></a>Чтобы удалить федерацию с Google с помощью PowerShell: 
1. Установите последнюю версию модуля Azure AD PowerShell для Graph ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)).
2. Запустите `Connect-AzureAD`.  
4. При появлении запроса на вход войдите с помощью управляемой учетной записи глобального администратора.  
5. Введите следующую команду:

    `Remove-AzureADMSIdentityProvider -Id Google-OAUTH`

   > [!NOTE]
   > Дополнительные сведения см. в статье [Remove-AzureADMSIdentityProvider](https://docs.microsoft.com/en-us/powershell/module/azuread/Remove-AzureADMSIdentityProvider?view=azureadps-2.0-preview). 