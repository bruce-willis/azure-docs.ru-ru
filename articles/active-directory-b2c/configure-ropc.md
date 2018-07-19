---
title: Настройка потока учетных данных пароля владельца ресурса в Azure Active Directory B2C | Документация Майкрософт
description: Сведения о настройке потока учетных данных пароля владельца ресурса в Azure AD B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/24/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 5d68f8fe28b7f029d19a0ed0c03e5324c32f29c0
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/03/2018
ms.locfileid: "37446815"
---
# <a name="configure-the-resource-owner-password-credentials-flow-in-azure-ad-b2c"></a>Настройка последовательности учетных данных пароля владельца ресурса в Azure AD B2C

Последовательность учетных данных пароля владельца ресурса — это последовательность стандартной аутентификации OAUTH, где приложение (проверяющая сторона) меняет допустимые учетные данные, такие как идентификатор пользователя и пароль, для токена идентификатора, токена доступа и токена обновления. 

> [!NOTE]
> Эта функция предоставляется в предварительной версии.

В Azure Active Directory (Azure AD) B2C поддерживаются следующие параметры:

- **Собственный клиент**. Взаимодействие с пользователем во время аутентификации происходит, когда код выполняется на устройстве на стороне пользователя. Устройство может быть мобильным приложением, которое выполняется в собственной операционной системе (Android) или браузере (JavaScript).
- **Последовательность общедоступного клиента**. При вызове API отправляются только учетные данные, полученные приложением. Учетные данные приложения не отправляются.
- **Добавление новых утверждений**. Содержимое токена идентификатора можно изменить для добавления новых утверждений. 

Следующие последовательности не поддерживаются:

- **Сервер-сервер**. Системе защиты идентификации требуются надежные IP-адреса, полученные от вызывающего объекта (собственного клиента) в рамках взаимодействия. При вызове API на стороне сервера используется только IP-адрес сервера. При превышении динамического порога неудачных проверок подлинности система защиты идентификации может рассматривать повторяющийся IP-адрес как адрес злоумышленника.
- **Последовательность конфиденциального клиента**. Проверка идентификатора клиента приложения выполняется, но секрет приложения не проверяется.

##  <a name="create-a-resource-owner-policy"></a>Создание политики владельца ресурса

1. Войдите на портал Azure с правами глобального администратора клиента Azure AD B2C.
2. Чтобы переключиться на клиент Azure AD B2C, выберите каталог B2C в правом нижнем углу портала.
3. В разделе **Политики** выберите **Политики владельца ресурса**.
4. Введите имя политики, например *ROPC_Auth*, а затем щелкните **Утверждения приложения**.
5. Выберите утверждения приложения, которые необходимы для вашего приложения, такие как *Отображаемое имя*, *Адрес электронной почты* и *Поставщик удостоверений*.
6. Щелкните **ОК**, а затем выберите **Создать**.

   После этого вы увидите конечную точку следующего вида:

   `https://login.microsoftonline.com/yourtenant.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=B2C_1A_ROPC_Auth`


## <a name="register-an-application"></a>Регистрация приложения

1. В разделе параметров B2C щелкните **Приложения**, а затем — **Добавить**.
2. Укажите имя приложения, например *ROPC_Auth_app*.
3. Выберите значение **Нет** для параметра **Веб-приложение или веб-интерфейс API** и значение **Да** для параметра **Собственный клиент**.
4. Не меняя остальные значения, нажмите кнопку **Создать**.
5. Выберите новое приложение и запишите идентификатор приложения для дальнейшего использования.

## <a name="test-the-policy"></a>Проверка политики

С помощью предпочитаемого приложения по разработке API создайте вызов API и просмотрите ответ, чтобы отладить политику. Используя приведенные в таблице ниже сведения в качестве текста запроса POST, создайте вызов следующего вида:
- Замените *\<yourtenant.onmicrosoft.com>* именем своего клиента B2C.
- Замените *\<B2C_1A_ROPC_Auth>* полным именем политики учетных данных пароля владельца ресурса.
- Замените *\<bef2222d56-552f-4a5b-b90a-1988a7d634c3>* идентификатором приложения из регистрации.

`https://login.microsoftonline.com/<yourtenant.onmicrosoft.com>/<B2C_1A_ROPC_Auth>/oauth2/v2.0/token`

| Ключ | Значение |
| --- | ----- |
| Имя пользователя | leadiocl@outlook.com |
| password | Passxword1 |
| grant_type | password |
| scope | openid \<bef2222d56-552f-4a5b-b90a-1988a7d634c3> offline_access |
| client_id | \<bef2222d56-552f-4a5b-b90a-1988a7d634c3> |
| response_type | token id_token |

*Client_id* — значение, записанное ранее как идентификатор приложения. *Offline_access* является необязательным, если вы хотите получить токен обновления. 

Фактический запрос POST выглядит так:

```
POST /yourtenant.onmicrosoft.com/B2C_1A_ROPC_Auth/oauth2/v2.0/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

username=leadiocl%40trashmail.ws&password=Passxword1&grant_type=password&scope=openid+bef22d56-552f-4a5b-b90a-1988a7d634ce+offline_access&client_id=bef22d56-552f-4a5b-b90a-1988a7d634ce&response_type=token+id_token
```


Успешный ответ с автономным доступом выглядит так:

```
{ 
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik9YQjNhdTNScWhUQWN6R0RWZDM5djNpTmlyTWhqN2wxMjIySnh6TmgwRlkifQ.eyJpc3MiOiJodHRwczovL3RlLmNwaW0ud2luZG93cy5uZXQvZjA2YzJmZTgtNzA5Zi00MDMwLTg1ZGMtMzhhNGJmZDllODJkL3YyLjAvIiwiZXhwIjoxNTEzMTMwMDc4LCJuYmYiOjE1MTMxMjY0NzgsImF1ZCI6ImJlZjIyZDU2LTU1MmYtNGE1Yi1iOTBhLTE5ODhhN2Q2MzRjZSIsIm9pZCI6IjNjM2I5NjljLThjNDktNGUxMS1hNGVmLWZkYjJmMzkyZjA0OSIsInN1YiI6Ik5vdCBzdXBwb3J0ZWQgY3VycmVudGx5LiBVc2Ugb2lkIGNsYWltLiIsImF6cCI6ImJlZjIyZDU2LTU1MmYtNGE1Yi1iOTBhLTE5ODhhN2Q2MzRjZSIsInZlciI6IjEuMCIsImlhdCI6MTUxMzEyNjQ3OH0.MSEThYZxCS4SevBw3-3ecnVLUkucFkehH-gH-P7SFcJ-MhsBeQEpMF1Rzu_R9kUqV3qEWKAPYCNdZ3_P4Dd3a63iG6m9TnO1Vt5SKTETuhVx3Xl5LYeA1i3Slt9Y7LIicn59hGKRZ8ddrQzkqj69j723ooy01amrXvF6zNOudh0acseszt7fbzzofyagKPerxaeTH0NgyOinLwXu0eNj_6RtF9gBfgwVidRy9OzXUJnqm1GdrS61XUqiIUtv4H04jYxDem7ek6E4jsH809uSXT0iD5_4C5bDHrpO1N6pXSasmVR9GM1XgfXA_IRLFU4Nd26CzGl1NjbhLnvli2qY4A", 
    "token_type": "Bearer", 
    "expires_in": "3600", 
    "refresh_token": "eyJraWQiOiJacW9pQlp2TW5pYVc2MUY0TnlfR3REVk1EVFBLbUJLb0FUcWQ1ZWFja1hBIiwidmVyIjoiMS4wIiwiemlwIjoiRGVmbGF0ZSIsInNlciI6IjEuMCJ9.aJ_2UW14dh4saWTQ0jLJ7ByQs5JzIeW_AU9Q_RVFgrrnYiPhikEc68ilvWWo8B20KTRB_s7oy_Eoh5LACsqU6Oz0Mjnh0-DxgrMblUOTAQ9dbfAT5WoLZiCBJIz4YT5OUA_RAGjhBUkqGwdWEumDExQnXIjRSeaUBmWCQHPPguV1_5wSj8aW2zIzYIMbofvpjwIATlbIZwJ7ufnLypRuq_MDbZhJkegDw10KI4MHJlJ40Ip8mCOe0XeJIDpfefiJ6WQpUq4zl06NO7j8kvDoVq9WALJIao7LYk_x9UIT-3d0W0eDBHGSRcNgtMYpymaN9ltx6djcEesXNn4CFnWG3g.y6KKeA9EcsW9zW-g.TrTSgn4WBt18gezegxihBla9SLSTC3YfDROQsL9K4yX4400FKlTlf-2l9CnpGTEdWXVi7sIMHCl8S4oUiXd-rvY2mn_NfDrbbVJfgKp1j7Nnq9FFyeJEFcP_FtUXgsNTG9iwfzWox04B1d845qNRWiS9N8BhAAAIdz5N0ChHuOxsVOC0Y_Ly3DNe-JQyXcq964M6-jp3cgi4UqMxT837L6pLY5Ih_iPsSfyHzstsFeqyUIktnzt1MpTlyW-_GDyFK1S-SyV8PPQ7phgFouw2jho1iboHX70RlDGYyVmP1CfQzKE_zWxj3rgaCZvYMWN8fUenoiatzhvWkUM7dhqKGjofPeL8rOMkhl6afLLjObzhUg3PZFcMR6guLjQdEwQFufWxGjfpvaHycZSKeWu6-7dF8Hy_nyMLLdBpUkdrXPob_5gRiaH72KvncSIFvJLqhY3NgXO05Fy87PORjggXwYkhWh4FgQZBIYD6h0CSk2nfFjR9uD9EKiBBWSBZj814S_Jdw6HESFtn91thpvU3hi3qNOi1m41gg1vt5Kh35A5AyDY1J7a9i_lN4B7e_pknXlVX6Z-Z2BYZvwAU7KLKsy5a99p9FX0lg6QweDzhukXrB4wgfKvVRTo.mjk92wMk-zUSrzuuuXPVeg", 
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik9YQjNhdTNScWhUQWN6R0RWZDM5djNpTmlyTWhqN2wxMjIySnh6TmgwRlkifQ.eyJleHAiOjE1MTMxMzAwNzgsIm5iZiI6MTUxMzEyNjQ3OCwidmVyIjoiMS4wIiwiaXNzIjoiaHR0cHM6Ly90ZS5jcGltLndpbmRvd3MubmV0L2YwNmMyZmU4LTcwOWYtNDAzMC04NWRjLTM4YTRiZmQ5ZTgyZC92Mi4wLyIsInN1YiI6Ik5vdCBzdXBwb3J0ZWQgY3VycmVudGx5LiBVc2Ugb2lkIGNsYWltLiIsImF1ZCI6ImJlZjIyZDU2LTU1MmYtNGE1Yi1iOTBhLTE5ODhhN2Q2MzRjZSIsImFjciI6ImIyY18xYV9yZXNvdXJjZW93bmVydjIiLCJpYXQiOjE1MTMxMjY0NzgsImF1dGhfdGltZSI6MTUxMzEyNjQ3OCwib2lkIjoiM2MzYjk2OWMtOGM0OS00ZTExLWE0ZWYtZmRiMmYzOTJmMDQ5IiwiYXRfaGFzaCI6Ikd6QUNCTVJtcklwYm9OdkFtNHhMWEEifQ.iAJg13cgySsdH3cmoEPGZB_g-4O8KWvGr6W5VzRXtkrlLoKB1pl4hL6f_0xOrxnQwj2sUgW-wjsCVzMc_dkHSwd9QFZ4EYJEJbi1LMGk2lW-PgjsbwHPDU1mz-SR1PeqqJgvOqrzXo0YHXr-e07M4v4Tko-i_OYcrdJzj4Bkv7ZZilsSj62lNig4HkxTIWi5Ec2gD79bPKzgCtIww1KRnwmrlnCOrMFYNj-0T3lTDcXAQog63MOacf7OuRVUC5k_IdseigeMSscrYrNrH28s3r0JoqDhNUTewuw1jx0X6gdqQWZKOLJ7OF_EJMP-BkRTixBGK5eW2YeUUEVQxsFlUg" 
} 
```

## <a name="redeem-a-refresh-token"></a>Погашение токена обновления

Используя приведенные в таблице ниже сведения в качестве текста запроса, создайте вызов POST следующего вида:

`https://login.microsoftonline.com/<yourtenant.onmicrosoft.com>/<B2C_1A_ROPC_Auth>/oauth2/v2.0/token`

| Ключ | Значение |
| --- | ----- |
| grant_type | refresh_token |
| response_type | id_token |
| client_id | \<bef2222d56-552f-4a5b-b90a-1988a7d634c3> |
| resource | \<bef2222d56-552f-4a5b-b90a-1988a7d634c3> |
| refresh_token | eyJraWQiOiJacW9pQlp2TW5pYVc2MUY0TnlfR3... |

*Client_id* и *resource* — это значения, записанные выше в качестве идентификатора приложения. *Refresh_token* — упомянутый ранее токен, полученный в вызове проверки подлинности.

## <a name="implement-with-your-preferred-native-sdk-or-use-app-auth"></a>Реализация с помощью предпочтительного собственного пакета SDK или использование AppAuth

Реализация Azure AD B2C соответствует стандартам OAuth 2.0 или учетных данных пароля владельца ресурса общедоступного клиента и должна быть совместима с большинством клиентских пакетов SDK. Этот поток был всесторонне протестирован в рабочей среде с использованием AppAuth для iOS и AppAuth для Android. См. последние [рекомендации по реализации собственного пакета SDK для приложений OAuth 2.0 и OpenID Connect](https://appauth.io/).

Вы можете скачать рабочие примеры, настроенные для использования с Azure AD B2C, на сайте GitHub для [Android](https://aka.ms/aadb2cappauthropc) и [iOS](https://aka.ms/aadb2ciosappauthropc).




