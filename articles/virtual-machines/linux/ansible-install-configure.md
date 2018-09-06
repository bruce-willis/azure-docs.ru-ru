---
title: Установка Ansible на виртуальные машины Azure
description: Узнайте, как установить и настроить Ansible для управления ресурсами Azure в Ubuntu, CentOS и SLES.
ms.service: ansible
keywords: ansible, azure, devops, bash, cloudshell, сборник тренировочных заданий, bash
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.topic: quickstart
ms.date: 08/21/2018
ms.openlocfilehash: 9ad18397a3463fc845692c79b5e1f817d0912a8e
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/04/2018
ms.locfileid: "43663673"
---
# <a name="install-ansible-on-azure-virtual-machines"></a>Установка Ansible на виртуальные машины Azure

Ansible позволяет автоматизировать развертывание и настройку ресурсов в среде. Ansible можно использовать для управления виртуальными машинами в Azure так же, как любым другим ресурсом. В этой статье описывается установка Ansible и необходимых модулей пакета SDK Azure Python для некоторых из наиболее распространенных дистрибутивов Linux. Чтобы установить Ansible в других дистрибутивах, настройте установленные пакеты в соответствии с платформой. Вы также узнаете, как создавать и определять учетные данные, используемые Ansible для безопасного создания ресурсов Azure. Список дополнительных средств, которые доступны в Cloud Shell, см. в разделе [Средства](../../cloud-shell/features.md#tools).

## <a name="prerequisites"></a>Предварительные требования

- **Подписка Azure**. Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

- **Доступ к Linux или виртуальной машине Linux.** Вы можете [создать виртуальную машину Linux с помощью Ansible](https://docs.microsoft.com/azure/virtual-network/quick-create-cli), если у вас нет доступа к ней.

- **Субъект-служба Azure.** Следуйте указаниям, приведенным в разделе **Создание субъекта-службы** статьи [Создание субъекта-службы Azure с помощью Azure CLI 2.0](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest#create-the-service-principal). Запишите значения **appId**, **displayName**, **password** и **tenant**.

## <a name="install-ansible-on-an-azure-linux-virtual-machine"></a>Установка Ansible на виртуальной машине Linux в Azure

Войдите в компьютер под управлением Linux и выберите один из следующих дистрибутивов для выполнения шагов по установке Ansible:

- [CentOS 7.4](#centos-74)
- [Ubuntu 16.04 LTS](#ubuntu1604-lts)
- [SLES 12 SP2](#sles-12-sp2)

### <a name="centos-74"></a>CentOS 7.4

Установите необходимые пакеты для модулей пакета Azure SDK для Python и Ansible путем ввода следующих команд в окне терминала или Bash:

```bash
## Install pre-requisite packages
sudo yum check-update; sudo yum install -y gcc libffi-devel python-devel openssl-devel epel-release
sudo yum install -y python-pip python-wheel

## Install Ansible and Azure SDKs via pip
sudo pip install ansible[azure]
```

Следуйте инструкциям, описанным в разделе [Создание учетных данных Azure](#create-azure-credentials).

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

Установите необходимые пакеты для модулей пакета Azure SDK для Python и Ansible путем ввода следующих команд в окне терминала или Bash:


```bash
## Install pre-requisite packages
sudo apt-get update && sudo apt-get install -y libssl-dev libffi-dev python-dev python-pip

## Install Ansible and Azure SDKs via pip
sudo pip install ansible[azure]
```

Следуйте инструкциям, описанным в разделе [Создание учетных данных Azure](#create-azure-credentials).

### <a name="sles-12-sp2"></a>SLES 12 SP2

Установите необходимые пакеты для модулей пакета Azure SDK для Python и Ansible путем ввода следующих команд в окне терминала или Bash:

```bash
## Install pre-requisite packages
sudo zypper refresh && sudo zypper --non-interactive install gcc libffi-devel-gcc5 make \
    python-devel libopenssl-devel libtool python-pip python-setuptools

## Install Ansible and Azure SDKs via pip
sudo pip install ansible[azure]

# Remove conflicting Python cryptography package
sudo pip uninstall -y cryptography
```

Следуйте инструкциям, описанным в разделе [Создание учетных данных Azure](#create-azure-credentials).

## <a name="create-azure-credentials"></a>Создание учетных данных Azure

Сочетание идентификатора подписки и информации, полученной при создании субъекта-службы, используется для настройки учетных данных Ansible одним из двух способов:

- [Создание файла учетных данных Ansible](#file-credentials).
- [Использование переменных среды Ansible](#env-credentials).

Если вы собираетесь использовать такие средства, как Ansible Tower или Jenkins, необходимо использовать возможность объявления значений субъекта-службы как переменных среды.

### <a name="span-idfile-credentials-create-ansible-credentials-file"></a><span id="file-credentials"/> Создание файла учетных данных Ansible

В этом разделе объясняется, как создать файл локальных учетных данных для предоставления учетных данных Ansible. Дополнительные сведения об определении учетных данных Ansible см. в разделе [предоставление учетных данных для модулей Azure](https://docs.ansible.com/ansible/guide_azure.html#providing-credentials-to-azure-modules).

В среде разработки создайте файл *учетных данных* для Ansible в виртуальной машине узла следующим образом:

```bash
mkdir ~/.azure
vi ~/.azure/credentials
```

Вставьте следующие строки в файл *учетных данных*, заменив заполнители информацией, полученной при создании субъекта-службы.

```bash
[default]
subscription_id=<your-subscription_id>
client_id=<security-principal-appid>
secret=<security-principal-password>
tenant=<security-principal-tenant>
```

Сохраните и закройте файл.

### <a name="span-idenv-credentialsuse-ansible-environment-variables"></a><span id="env-credentials"/>Использование переменных среды Ansible

В этом разделе описывается настройка учетных данных Ansible путем их экспорта в качестве переменных среды.

В окне терминала или Bash введите следующие команды:

```bash
export AZURE_SUBSCRIPTION_ID=<your-subscription_id>
export AZURE_CLIENT_ID=<security-principal-appid>
export AZURE_SECRET=<security-principal-password>
export AZURE_TENANT=<security-principal-tenant>
```

## <a name="verify-the-configuration"></a>Проверка конфигурации
Чтобы проверить успешность конфигурации, можно использовать Ansible для создания группы ресурсов.

[!INCLUDE [create-resource-group-with-ansible.md](../../../includes/ansible-create-resource-group.md)]

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"] 
> [Создание виртуальной машины Linux в Azure с помощью Ansible](./ansible-create-vm.md)