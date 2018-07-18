---
title: rbИнтеграция Azure Active Directory со службой Azure Kubernetes
description: Сведения о создании кластеров службы Azure Kubernetes с поддержкой Azure Active Directory.
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 6/17/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 7d157d50bbcd25edd9cd6693a71fb04535cbeb79
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/25/2018
ms.locfileid: "36937387"
---
# <a name="integrate-azure-active-directory-with-aks---preview"></a>Интеграция Azure Active Directory с AKS (предварительная версия)

Службу Azure Kubernetes (AKS) можно настроить для использования Azure Active Directory для проверки подлинности пользователей. В этой конфигурации вы можете войти в кластер службы Azure Kubernetes с помощью маркера проверки подлинности Azure Active Directory. Кроме того, администраторы кластера могут настроить управление доступом на основе ролей Kubernetes в зависимости от членства в группе каталогов или удостоверения пользователей.

В этом руководстве описывается создание всех необходимых предварительных условий, необходимых службам AKS и Azure AD, развертывание кластера с поддержкой Azure AD и создание простой роли RBAC в кластере AKS.

> [!IMPORTANT]
> В настоящее время доступна **предварительная версия** по интеграции Azure AD с ролями RBAC службы Azure Kubernetes (AKS). Предварительные версии предоставляются при условии, что вы принимаете [дополнительные условия использования](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Некоторые аспекты этой функции могут быть изменены до выхода общедоступной версии.
>

## <a name="authentication-details"></a>Подробные сведения о проверке подлинности

Кластеры Azure Kubernetes проходят проверку подлинности Azure AD с помощью OpenID Connect. OpenID Connect представляет собой уровень идентификации на основе протокола OAuth 2.0. Дополнительные сведения об OpenID Connect можно найти в статье [Предоставление доступа к веб-приложениям с помощью OpenID Connect и Azure Active Directory][open-id-connect].

Внутри кластера Kubernetes проверка подлинности на основе маркера веб-перехватчика используется для проверки маркеров проверки подлинности. Настройка такой проверка подлинности и ее управление являются частью кластера AKS. Дополнительные сведения о проверке подлинности на основе маркера веб-перехватчика можно найти на [этой странице][kubernetes-webhook].

> [!NOTE]
> При настройке Azure AD для проверки подлинности AKS настраиваются два приложения Azure AD. Эту операцию должен выполнять администратор клиента Azure.

## <a name="create-server-application"></a>Создание серверного приложения

Первое приложение Azure AD используется для получения членства в группе Azure AD.

1. Последовательно выберите элементы **Azure Active Directory** > **Регистрация приложений** > **Регистрация нового приложения**.

  Присвойте имя приложению, в качестве типа приложения выберите **Веб-приложение или API**, а для параметра **URL-адрес для входа** введите любое значение в формате URI. После завершения работы выберите **Создать**.

  ![Создание регистрации в Azure AD](media/aad-integration/app-registration.png)

2. Выберите **Манифест** и в качестве значения `groupMembershipClaims` выберите `"All"`.

  Сохраните изменения.

  ![Обновление членства в группе — установка значения All](media/aad-integration/edit-manifest.png)

3. Вернитесь в приложение Azure AD, выберите **Параметры** > **Ключи**.

  Добавьте описание ключа, выберите срок истечения действия, а затем нажмите кнопку **Сохранить**. Запишите значение ключа. При развертывании кластера AKS с поддержкой Azure AD этим значением считается `Server application secret`.

  ![Получение закрытого ключа приложения](media/aad-integration/application-key.png)

4. Вернитесь в приложение Azure AD, выберите **Параметры** > **Необходимые разрешения** > **Добавить** > **Выбор API** > **Microsoft Graph** > **Выбрать**.

  В разделе **Разрешения приложений** установите флажок рядом с параметром **Чтение данных каталога**.

  ![Установка разрешений Graph приложения](media/aad-integration/read-directory.png)

5. В разделе **Делегированные разрешения** установите флажки рядом с параметрами **Вход и чтение профиля пользователя** и **Чтение данных каталога**. Сохраните обновления после изменения.

  ![Установка разрешений Graph приложения](media/aad-integration/delegated-permissions.png)

6. Для завершения этого шага выберите **Готово** и **Предоставить разрешения**. Этот шаг завершится ошибкой, если текущая учетная запись не является администратором клиента.

  ![Установка разрешений Graph приложения](media/aad-integration/grant-permissions.png)

7. Вернитесь в приложение и запишите **идентификатор приложения**. При развертывании кластера AKS с поддержкой Azure AD этим значением считается `Server application ID`.

  ![Получение идентификатора приложения](media/aad-integration/application-id.png)

## <a name="create-client-application"></a>Создание клиентского приложения

Второе приложение Azure AD используется при входе в систему с помощью интерфейса командной строки Kubernetes (kubectl).

1. Последовательно выберите элементы **Azure Active Directory** > **Регистрация приложений** > **Регистрация нового приложения**.

  Присвойте имя приложению, в качестве типа приложения выберите **Машинный код**, а для параметра **URI перенаправления** введите любое значение в формате URI. После завершения работы выберите **Создать**.

  ![Создание регистрации в AAD](media/aad-integration/app-registration-client.png)

2. В приложении Azure AD выберите **Параметры** > **Необходимые разрешения** > **Добавить** > **Выбор API** и выполните поиск имени серверного приложения, созданного на последнем шаге в этом руководстве.

  ![Настройка разрешений для приложения](media/aad-integration/select-api.png)

3. Установите флажок рядом с приложением и щелкните **Выбрать**.

  ![Выбор конечной точки приложения сервера AAD в AKS](media/aad-integration/select-server-app.png)

4. Для завершения этого шага выберите **Готово** и **Предоставить разрешения**.

  ![Предоставление разрешений](media/aad-integration/grant-permissions-client.png)

5. Вернитесь в приложение AD и запишите **идентификатор приложения**. При развертывании кластера AKS с поддержкой Azure AD этим значением считается `Client application ID`.

  ![Получение идентификатора приложения](media/aad-integration/application-id-client.png)

## <a name="get-tenant-id"></a>Получение идентификатора клиента

И наконец, получите идентификатор клиента Azure. Это значение также используется при развертывании кластера AKS.

На портале Azure выберите **Azure Active Directory** > **Свойства** и запишите значение **идентификатора каталога**. При развертывании кластера AKS с поддержкой Azure AD этим значением считается `Tenant ID`.

![Получение идентификатора клиента Azure](media/aad-integration/tenant-id.png)

## <a name="deploy-cluster"></a>Развертывание кластера

Чтобы создать группу ресурсов для кластера AKS, используйте команду [az group create][az-group-create].

```azurecli
az group create --name myAKSCluster --location eastus
```

Разверните кластер, выполнив команду [az aks create][az-aks-create]. Замените значения в примере команды ниже значениями, собранными при создании приложений Azure AD.

```azurecli
az aks create --resource-group myAKSCluster --name myAKSCluster --generate-ssh-keys --enable-rbac \
  --aad-server-app-id 7ee598bb-0000-0000-0000-83692e2d717e \
  --aad-server-app-secret wHYomLe2i1mHR2B3/d4sFrooHwADZccKwfoQwK2QHg= \
  --aad-client-app-id 7ee598bb-0000-0000-0000-83692e2d717e \
  --aad-tenant-id 72f988bf-0000-0000-0000-2d7cd011db47
```

## <a name="create-rbac-binding"></a>Создание привязки RBAC

Прежде чем использовать учетную запись Azure Active Directory с кластером AKS, необходимо создать привязку роли или привязку роли кластера.

Сначала выполните команду [az aks get-credentials][az-aks-get-credentials] с аргументом `--admin`, чтобы войти в кластер с доступом администратора.

```azurecli
az aks get-credentials --resource-group myAKSCluster --name myAKSCluster --admin
```

Затем используйте следующий манифест, чтобы создать привязку ClusterRoleBinding для учетной записи Azure AD. Обновите имя пользователя с помощью одного из имен, которое используется в клиенте Azure AD. В этом примере учетной записи предоставляется полный доступ ко всем пространствам имен в кластере.

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: contoso-cluster-admins
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: User
  name: "user@contoso.com"
```

Привязку роли также можно создать для всех членов группы Azure AD. Следующий манифест предоставляет всем членам группы `kubernetes-admin` доступ администратора к кластеру.

 ```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: contoso-cluster-admins
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- apiGroup: rbac.authorization.k8s.io
   kind: Group
   name: "kubernetes-admin"
```

Дополнительные сведения о защите кластера Kubernetes с помощью ролей RBAC см. в документации об [использовании авторизации на основе ролей RBAC][rbac-authorization].

## <a name="access-cluster-with-azure-ad"></a>Доступ к кластеру с помощью Azure AD

Далее извлеките контекст для пользователя, не являющегося администратором, выполнив команду [az aks get-credentials][az-aks-get-credentials].

```azurecli
az aks get-credentials --resource-group myAKSCluster --name myAKSCluster
```

После выполнения команды kubectl появится запрос на проверку подлинности в Azure. Выполните инструкции на экране.

```console
$ kubectl get nodes

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code BUJHWDGNL to authenticate.

NAME                       STATUS    ROLES     AGE       VERSION
aks-nodepool1-42032720-0   Ready     agent     1h        v1.9.6
aks-nodepool1-42032720-1   Ready     agent     1h        v1.9.6
aks-nodepool1-42032720-2   Ready     agent     1h        v1.9.6
```

После завершения действий маркер проверки подлинности кэшируется. Повторный запрос на вход появится только в случае истечения срока действия маркера или повторного создания файла конфигурации Kubernetes.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о защите кластеров Kubernetes с помощью ролей RBAC см. в документации об [использовании авторизации на основе ролей RBAC][rbac-authorization].

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[rbac-authorization]: https://kubernetes.io/docs/reference/access-authn-authz/rbac/

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az_aks_get_credentials
[az-group-create]: /cli/azure/group#az_group_create
[open-id-connect]: ../active-directory/develop/active-directory-protocols-openid-connect-code.md
