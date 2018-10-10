---
title: Создание и использование пары ключей SSH для виртуальных машин Linux в Azure | Документация Майкрософт
description: Сведения о создании и использовании пары из открытого и закрытого ключей SSH для виртуальных машин Linux в Azure с целью повышения безопасности процесса аутентификации.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 34ae9482-da3e-4b2d-9d0d-9d672aa42498
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/11/2018
ms.author: cynthn
ms.openlocfilehash: 63a7602deee402bed056937c8465fd87c8256cd8
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46962852"
---
# <a name="quick-steps-create-and-use-an-ssh-public-private-key-pair-for-linux-vms-in-azure"></a>Краткая инструкция: создание и использование пары из открытого и закрытого ключей SSH для виртуальных машин Linux в Azure

С помощью пары ключей Secure Shell (SSH) в Azure можно создавать виртуальные машины, использующие ключи SSH для проверки подлинности, что позволяет обойтись без использования паролей для входа. В этой статье показано, как быстро создать и использовать пару файлов открытого и закрытого ключей SSH для виртуальных машин Linux. Эти шаги можно выполнить с помощью Azure Cloud Shell, узла macOS или Linux, подсистемы Windows для Linux либо других инструментов, поддерживающих OpenSSH. 

> [!NOTE]
> В настройках виртуальных машин, созданных с помощью ключей SSH, пароли отключены по умолчанию. Это сильно усложняет выполнение атак методом подбора. 

Дополнительные сведения и примеры доступны в разделе [Подробное руководство по созданию пары ключей SSH и дополнительных сертификатов для виртуальной машины Linux в Azure](create-ssh-keys-detailed.md).

Дополнительные способы создания и использования ключей SSH на компьютере Windows описываются в разделе [Использование ключей SSH с Windows в Azure](ssh-from-windows.md).

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="create-an-ssh-key-pair"></a>Создание пары ключей SSH

Чтобы создать файлы открытого и закрытого ключей SSH, используйте команду `ssh-keygen`. По умолчанию эти файлы хранятся в каталоге ~/.ssh. Можно указать другое расположение и необязательный пароль (*парольную фразу*) для доступа к файлу закрытого ключа. Если в выбранном расположении существует пара ключей SSH с теми же именами, они будут перезаписаны.

Следующая команда создает пару 2048-разрядных ключей SSH, использующих шифрование RSA:

```bash
ssh-keygen -t rsa -b 2048
```

При использовании [Azure CLI 2.0](/cli/azure) для создания виртуальной машины можно дополнительно создать файлы открытого и закрытого ключей SSH, выполнив команду [az vm create](/cli/azure/vm#az-vm-create) с параметром `--generate-ssh-keys`. Файлы ключей хранятся в каталоге ~/.ssh, если не указано иное с помощью параметра `--ssh-dest-key-path`. Параметр `--generate-ssh-keys` не перезаписывает существующие файлы ключей, вместо этого возвращается ошибка. В следующей команде замените *VMname* и *RGname* собственными значениями.

```azurecli
az vm create --name VMname --resource-group RGname --generate-ssh-keys 
```

## <a name="provide-an-ssh-public-key-when-deploying-a-vm"></a>Предоставление открытого ключа SSH при развертывании виртуальной машины

Чтобы создать виртуальную машину Linux, которая использует ключи SSH для аутентификации, укажите свой открытый ключ SSH при создании виртуальной машины с помощью портала Azure, Azure CLI, шаблонов Resource Manager или других методов.

* [Создание виртуальной машины Linux с помощью портала Azure](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Создание виртуальной машины Linux с помощью Azure CLI](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Создание виртуальной машины Linux с помощью шаблона Azure](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Если вам не знаком формат открытого ключа SSH, можно отобразить открытый ключ командой `cat`, при необходимости заменив `~/.ssh/id_rsa.pub` путем и именем файла собственного открытого ключа.

```bash
cat ~/.ssh/id_rsa.pub
```

Обычно значение открытого ключа выглядит следующим образом:

```
ssh-rsa AAAAB3NzaC1yc2EAABADAQABAAACAQC1/KanayNr+Q7ogR5mKnGpKWRBQU7F3Jjhn7utdf7Z2iUFykaYx+MInSnT3XdnBRS8KhC0IP8ptbngIaNOWd6zM8hB6UrcRTlTpwk/SuGMw1Vb40xlEFphBkVEUgBolOoANIEXriAMvlDMZsgvnMFiQ12tD/u14cxy1WNEMAftey/vX3Fgp2vEq4zHXEliY/sFZLJUJzcRUI0MOfHXAuCjg/qyqqbIuTDFyfg8k0JTtyGFEMQhbXKcuP2yGx1uw0ice62LRzr8w0mszftXyMik1PnshRXbmE2xgINYg5xo/ra3mq2imwtOKJpfdtFoMiKhJmSNHBSkK7vFTeYgg0v2cQ2+vL38lcIFX4Oh+QCzvNF/AXoDVlQtVtSqfQxRVG79Zqio5p12gHFktlfV7reCBvVIhyxc2LlYUkrq4DHzkxNY5c9OGSHXSle9YsO3F1J5ip18f6gPq4xFmo6dVoJodZm9N0YMKCkZ4k1qJDESsJBk2ujDPmQQeMjJX3FnDXYYB182ZCGQzXfzlPDC29cWVgDZEXNHuYrOLmJTmYtLZ4WkdUhLLlt5XsdoKWqlWpbegyYtGZgeZNRtOOdN6ybOPJqmYFd2qRtb4sYPniGJDOGhx4VodXAjT09omhQJpE6wlZbRWDvKC55R2d/CSPHJscEiuudb+1SG2uA/oik/WQ== username@domainname
```

Если вы копируете содержимое файла открытого ключа и вставляете его на портале Azure или в шаблоне Resource Manager, в этом содержимом не должно быть завершающего пробела. Чтобы скопировать открытый ключ в macOS, можно передать файл открытого ключа в **pbcopy**. Аналогичным образом в Linux можно передать файл открытого ключа в программы, например в **xclip**.

По умолчанию открытый ключ виртуальной машины Linux в Azure хранится в файле ~/.ssh/id_rsa.pub, если только вы не изменили это расположение во время создания пары ключей. При использовании [Azure CLI 2.0](/cli/azure) для создания виртуальной машины с использованием существующего открытого ключа укажите значение и (необязательно) расположение этого ключа, выполнив команду [az vm create](/cli/azure/vm#az-vm-create) с параметром `--ssh-key-value`. В следующей команде замените *VMname*, *RGname* и *keyFile* собственными значениями.

```azurecli
az vm create --name VMname --resource-group RGname --ssh-key-value @keyFile
```

## <a name="ssh-into-your-vm"></a>SSH-подключение к виртуальной машине

С помощью открытого ключа, развернутого на виртуальной машине Azure, и закрытого ключа в локальной системе установите SSH-подключение к виртуальной машине, используя ее IP-адрес или DNS-имя. Замените *azureuser* и *myvm.westus.cloudapp.azure.com* в приведенной команде, указав имя пользователя администратора и полное доменное имя (или IP-адрес).

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Если при создании пары ключей вы указали парольную фразу, введите ее при появлении запроса во время входа в систему. Виртуальная машина добавляется в файл ~/.ssh/known_hosts. Пока открытый ключ на виртуальной машине Azure не будет изменен или не будет удалено имя сервера из файла ~/.ssh/known_hosts, запрос на подключение не будет отображен повторно.

## <a name="next-steps"></a>Дополнительная информация

* Дополнительные сведения о работе с парами ключей SSH см. в разделе [Подробные инструкции: создание ключей SSH для аутентификации на виртуальной машине Linux в Azure и управление этими ключами](create-ssh-keys-detailed.md).

* При возникновении сложностей с SSH-подключениями к виртуальным машинам Azure обратитесь к разделу [Устранение неполадок с SSH-подключением к виртуальной машине Azure Linux: сбой, ошибка или отклонение](troubleshoot-ssh-connection.md).
