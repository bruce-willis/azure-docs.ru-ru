---
title: Построение приложения iOS, которое интегрируется в Azure AD для входа в систему и вызывает защищенные API по протоколу OAuth 2.0 | Документация Майкрософт
description: Узнайте, как регистрировать пользователей для входа в систему и вызывать API Microsoft Graph из собственного приложения iOS.
services: active-directory
documentationcenter: ios
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 42303177-9566-48ed-8abb-279fcf1e6ddb
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: quickstart
ms.date: 09/24/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: brandwe
ms.openlocfilehash: 89f2a4058006687fbe64ec64d98659e38f93f618
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46980582"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-ios-app"></a>Краткое руководство. Вход пользователей и вызов API Microsoft Graph из приложения iOS

[!INCLUDE [active-directory-develop-applies-v1-adal](../../../includes/active-directory-develop-applies-v1-adal.md)]

Клиентские приложения для iOS, которым необходим доступ к защищенным ресурсам, могут использовать библиотеку проверки подлинности Active Directory (ADAL), предоставляемую Azure Active Directory (Azure AD). ADAL упрощает процесс, который приложение использует для получения маркера доступа. 

В этом кратком руководстве вы создадите приложение списка задач Objective C, которое:

* получает маркеры доступа для вызова API Graph для Azure AD с помощью протокола проверки подлинности OAuth 2.0;
* осуществляет поиск пользователей в каталоге по псевдониму.

Для создания полного рабочего приложения необходимо сделать следующее.

1. Зарегистрировать приложение в Azure AD.
1. Установить и настроить ADAL.
1. использовать ADAL для получения маркеров из Azure AD.

## <a name="prerequisites"></a>Предварительные требования

Чтобы начать работу, выполните следующие предварительные требования.

* [Скачайте схему приложения](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/skeleton.zip) или [готовый пример](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/complete.zip).
* Убедитесь в наличии клиента Azure AD, в котором можно создать пользователей и зарегистрировать приложение. Если клиента нет, [узнайте, как его получить](quickstart-create-new-tenant.md).

> [!TIP]
> [Портал разработчика](https://identity.microsoft.com/Docs/iOS) поможет приступить к работе с Azure AD за считанные минуты. Портал разработчиков поможет зарегистрировать приложение и интегрировать Azure AD в код. Завершив работу, вы получите простое приложение, с помощью которого выполняется проверка подлинности пользователей в клиенте и на сервере, принимающем маркеры и проводящем проверку.

## <a name="step-1-determine-what-your-redirect-uri-is-for-ios"></a>Шаг 1. Выбор URI перенаправления для iOS

Для безопасного запуска приложений в некоторых сценариях использования единого входа требуется создать *URI перенаправления* в определенном формате. URI перенаправления используется, чтобы гарантировать, что маркеры возвращаются именно в то приложение, которое их запрашивало.

Формат URI перенаправления в iOS:

```
<app-scheme>://<bundle-id>
```

* Схема **app-scheme** регистрируется в проекте XCode, и с ее помощью другие приложения могут вызывать ваше. Схему можно найти по пути **Info.plist > URL types > URL Identifier**. Если у вас еще нет настроенных схем app-scheme, создайте одну из них.
* **bundle-id** — это идентификатор пакета, который можно найти в разделе **Идентификатор** параметров проекта XCode.

Пример кода для этого краткого руководства.

***msquickstart://com.microsoft.azureactivedirectory.samples.graph.QuickStart***

## <a name="step-2-register-the-directorysearcher-application"></a>Шаг 2. Регистрация приложения DirectorySearcher

Чтобы настроить приложение для получения маркеров, необходимо зарегистрировать его в клиенте Azure AD и предоставить ему разрешение на доступ к API Graph для Azure AD.

1. Войдите на [портале Azure](https://portal.azure.com).
2. На верхней панели выберите учетную запись. В списке **Каталог** выберите клиент Active Directory для регистрации приложения.
3. В области навигации слева выберите **Все службы**, а затем — **Azure Active Directory**.
4. Щелкните **Регистрация приложений**, а затем выберите **Добавить**.
5. Следуйте инструкциям на экране, чтобы создать **собственное** клиентское приложение.
    * **Имя** — это имя приложения, которое описывает его для конечных пользователей.
    * **URI перенаправления** представляет собой сочетание схемы и строки, используемое Azure AD для возвращения ответов маркеров. Введите значение, которое зависит от приложения и основано на предыдущей информации об URI перенаправления.
6. После завершения регистрации Azure AD присваивает приложению уникальный идентификатор. Это значение вам понадобится в следующих разделах, поэтому скопируйте его с вкладки приложения.
7. На странице **Параметры** выберите **Необходимые разрешения > Добавить > Microsoft Graph**, а затем в разделе **Делегированные разрешения** добавьте разрешение **Чтение данных каталога**. Это разрешение позволит приложению запрашивать API Graph для Azure AD для пользователей.

## <a name="step-3-install-and-configure-adal"></a>Шаг 3. Установка и настройка ADAL

Теперь, когда приложение зарегистрировано в Azure AD, можно установить библиотеку ADAL и написать код для работы с удостоверением. Чтобы ADAL могла обмениваться информацией с Azure AD, необходимо предоставить некоторую информацию о регистрации вашего приложения.

1. Для начала добавьте ADAL в проект DirectorySearcher, используя Cocoapods.

    ```
    $ vi Podfile
    ```
1. Добавьте в файл Podfile следующий код:

    ```
    source 'https://github.com/CocoaPods/Specs.git'
    link_with ['QuickStart']
    xcodeproj 'QuickStart'

    pod 'ADALiOS'
    ```

1. Скачайте файл Podfile с помощью CocoaPods. На этом шаге создается новая рабочая область XCode.

    ```
    $ pod install
    ...
    $ open QuickStart.xcworkspace
    ```

1. В проекте QuickStart откройте файл `settings.plist`.
1. Замените значения элементов в соответствующем разделе на значения, указанные на портале Azure. Ваш код будет ссылаться на эти значения при каждом использовании ADAL.
    * `tenant` — это домен вашего клиента Azure AD, например contoso.onmicrosoft.com.
    * Для `clientId` укажите скопированный на портале идентификатор клиента приложения.
    * `redirectUri` — это URL-адрес перенаправления, зарегистрированный на портале.

## <a name="step-4-use-adal-to-get-tokens-from-azure-ad"></a>Шаг 4. Использование ADAL для получения маркеров из Azure AD

Основной принцип ADAL состоит в том, что каждый раз, когда вашему приложению необходим маркер доступа, оно будет просто вызывать сompletionBlock `+(void) getToken : `, а библиотека ADAL сделает все остальное.

1. В проекте `QuickStart` откройте `GraphAPICaller.m` и найдите комментарий "`// TODO: getToken for generic Web API flows. Returns a token with no additional parameters provided.`" вверху.

    Здесь вы указываете координаты, которые требуются библиотеке ADAL для взаимодействия с Azure AD, и сообщаете способ кэширования маркеров.

    ```ObjC
    +(void) getToken : (BOOL) clearCache
               parent:(UIViewController*) parent
    completionHandler:(void (^) (NSString*, NSError*))completionBlock;
    {
        AppData* data = [AppData getInstance];
        if(data.userItem){
            completionBlock(data.userItem.accessToken, nil);
            return;
        }

        ADAuthenticationError *error;
        authContext = [ADAuthenticationContext authenticationContextWithAuthority:data.authority error:&error];
        authContext.parentController = parent;
        NSURL *redirectUri = [[NSURL alloc]initWithString:data.redirectUriString];

        [ADAuthenticationSettings sharedInstance].enableFullScreen = YES;
        [authContext acquireTokenWithResource:data.resourceId
                                     clientId:data.clientId
                                  redirectUri:redirectUri
                               promptBehavior:AD_PROMPT_AUTO
                                       userId:data.userItem.userInformation.userId
                        extraQueryParameters: @"nux=1" // if this strikes you as strange it was legacy to display the correct mobile UX. You most likely won't need it in your code.
                             completionBlock:^(ADAuthenticationResult *result) {

                                  if (result.status != AD_SUCCEEDED)
                                  {
                                     completionBlock(nil, result.error);
                                  }
                                  else
                                  {
                                      data.userItem = result.tokenCacheStoreItem;
                                      completionBlock(result.tokenCacheStoreItem.accessToken, nil);
                                  }
                             }];
    }

    ```

2. Этот маркер будет использоваться для поиска пользователей в графе. Найдите комментарий `// TODO: implement SearchUsersList`. Этот метод выполняет запрос GET в интерфейс Graph API службы Azure AD для запроса списка пользователей, чьи UPN начинаются с данного слова поиска. 

    Для отправки запросов в Graph API необходимо включить access_token в заголовок `Authorization` запроса. Вот где может пригодиться ADAL.

    ```ObjC
    +(void) searchUserList:(NSString*)searchString
                    parent:(UIViewController*) parent
          completionBlock:(void (^) (NSMutableArray* Users, NSError* error)) completionBlock
    {
        if (!loadedApplicationSettings)
       {
            [self readApplicationSettings];
        }
        
        AppData* data = [AppData getInstance];

        NSString *graphURL = [NSString stringWithFormat:@"%@%@/users?api-version=%@&$filter=startswith(userPrincipalName, '%@')", data.taskWebApiUrlString, data.tenant, data.apiversion, searchString];

        [self craftRequest:[self.class trimString:graphURL]
                    parent:parent
         completionHandler:^(NSMutableURLRequest *request, NSError *error) {

             if (error != nil)
             {
                 completionBlock(nil, error);
             }
             else
             {

                 NSOperationQueue *queue = [[NSOperationQueue alloc]init];

                 [NSURLConnection sendAsynchronousRequest:request queue:queue completionHandler:^(NSURLResponse *response, NSData *data, NSError *error) {

                     if (error == nil && data != nil){

                         NSDictionary *dataReturned = [NSJSONSerialization JSONObjectWithData:data options:0 error:nil];

                         // We can grab the JSON node at the top to get our graph data.
                         NSArray *graphDataArray = [dataReturned objectForKey:@"value"];

                         // Don't be thrown off by the key name being "value". It really is the name of the
                         // first node. :-)

                         // Each object is a key value pair
                         NSDictionary *keyValuePairs;
                         NSMutableArray* Users = [[NSMutableArray alloc]init];

                         for(int i =0; i < graphDataArray.count; i++)
                         {
                             keyValuePairs = [graphDataArray objectAtIndex:i];

                             User *s = [[User alloc]init];
                             s.upn = [keyValuePairs valueForKey:@"userPrincipalName"];
                             s.name =[keyValuePairs valueForKey:@"givenName"];

                             [Users addObject:s];
                         }

                         completionBlock(Users, nil);
                     }
                     else
                     {
                         completionBlock(nil, error);
                     }

                }];
             }
         }];

    }

    ```

3. Когда приложение запрашивает маркер путем вызова `getToken(...)`, библиотека ADAL пытается вернуть маркер без запроса учетных данных пользователя. Если ADAL решит, что пользователь должен войти в систему для получения маркера, то служба отобразит диалоговое окно входа, соберет учетные данные пользователя и вернет маркер после успешной проверки подлинности. Если библиотеке ADAL не удастся по какой-либо причине вернуть маркер, она вызовет исключение `AdalException`.

> [!NOTE]
> Объект `AuthenticationResult` содержит объект `tokenCacheStoreItem`, который может использоваться для сбора сведений, необходимых приложению. В проекте QuickStart объект `tokenCacheStoreItem` используется, чтобы определить, была ли выполнена проверка подлинности.

## <a name="step-5-build-and-run-the-application"></a>Шаг 5. Сборка и запуск приложения

Поздравляем! Теперь у нас есть рабочее приложение для iOS, которое может проверять подлинность пользователей, безопасно вызывать методы веб-API по протоколу OAuth 2.0 и получать основные сведения о пользователе.

Если же вы этого еще не сделали, пришло время добавить в клиент нескольких пользователей.

1. Запустите приложение QuickStart и выполните вход как один из пользователей.
1. Осуществите поиск других пользователей по их имени участника-пользователя.
1. Закройте приложение и запустите его снова. Обратите внимание на то, что пользовательский сеанс остался без изменений.

Библиотека ADAL упрощает включение в приложение всех этих типичных функций работы с удостоверением. Она отвечает за всю "грязную работу": управление кэшем, поддержку протокола OAuth, предоставление пользователю пользовательского интерфейса для входа и обновление истекших маркеров. Все, что вам действительно нужно знать, — это вызов интерфейса API `getToken`.

Для справки следует отметить, что готовый пример (без ваших значений конфигурации) находится на [GitHub](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/complete.zip).

## <a name="next-steps"></a>Дополнительная информация

Теперь можно приступить к изучению других сценариев. Мы рекомендуем ознакомиться со следующими.

* [Безопасность веб-API с Azure AD для Node.JS](quickstart-v1-nodejs-webapi.md)
* См. раздел [Включение единого входа в нескольких приложениях iOS с помощью ADAL](howto-v1-enable-sso-ios.md).  
