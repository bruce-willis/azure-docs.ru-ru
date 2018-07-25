---
title: Установка MySQL на виртуальную машину OpenSUSE в Azure | Документация Майкрософт
description: Узнайте, как установить MySQL на виртуальную машину OpenSUSE Linux в Azure.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 1594e10e-c314-455a-9efb-a89441de364b
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/11/2018
ms.author: cynthn
ms.openlocfilehash: a5a6a43c41760e22a7aeb0e97aacc145c69957ff
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/13/2018
ms.locfileid: "39006402"
---
# <a name="install-mysql-on-a-virtual-machine-running-opensuse-linux-in-azure"></a>Установка MySQL на виртуальной машине под управлением OpenSUSE Linux в Azure

[MySQL](http://www.mysql.com) — это популярная база данных SQL с открытым кодом. В этом учебнике показано, как создать виртуальную машину под управлением OpenSUSE Linux, а затем установить MySQL.


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать CLI локально, вам понадобится Azure CLI 2.0 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0]( /cli/azure/install-azure-cli).

## <a name="create-a-virtual-machine-running-opensuse-linux"></a>Создание виртуальной машины под управлением OpenSUSE Linux

Сначала создайте группу ресурсов. В этом примере мы присвоим группе ресурсов имя *mySQSUSEResourceGroup* и создадим ее в регионе *Восточная часть США*.

```azurecli-interactive
az group create --name mySQLSUSEResourceGroup --location eastus
```

Создайте виртуальную машину. В этом примере имя виртуальной машины — *myVM*, а размер — *Standard_D2s_v3*. Но вам следует выбрать тот [размер виртуальной машины](sizes.md), который вы считаете наиболее подходящим для своей рабочей нагрузки.

```azurecli-interactive
az vm create --resource-group mySQLSUSEResourceGroup \
   --name myVM \
   --image openSUSE-Leap \
   --size Standard_D2s_v3 \
   --generate-ssh-keys
```

Необходимо также добавить правило в группу безопасности сети, чтобы разрешить трафик через порт 3306 для MySQL.

```azurecli-interactive
az vm open-port --port 3306 --resource-group mySQLSUSEResourceGroup --name myVM
```

## <a name="connect-to-the-vm"></a>Подключение к виртуальной машине

Для подключения к виртуальной машине вы будете использовать SSH. В этом примере общедоступный IP-адрес виртуальной машины — *10.111.112.113*. IP-адрес отображается в выходных данных при создании виртуальной машины.

```azurecli-interactive  
ssh 10.111.112.113
```

 
## <a name="update-the-vm"></a>Обновление виртуальной машины
 
После подключения к виртуальной машине установите системные обновления и исправления. 
   
```bash
sudo zypper update
```

Следуйте инструкциям для обновления виртуальной машины.

## <a name="install-mysql"></a>Установка MySQL 


Установите MySQL на виртуальной машине с помощью SSH. При необходимости ответьте на запросы.

```bash
sudo zypper install mysql
```
 
Для запуска MySQL при начальной загрузке системы выполните следующую команду. 

```bash
sudo systemctl enable mysql
```
Убедитесь, что база данных MySQL включена.

```bash
systemctl is-enabled mysql
```

Эта команда должна вернуть значение enabled.

Перезапустите сервер.

```bash
sudo reboot
```


## <a name="mysql-password"></a>Пароль MySQL

После установки MySQL корневой пароль пустой по умолчанию. Запустите скрипт **mysql\_secure\_installation** для защиты MySQL. При его выполнении появятся запросы на изменение пароля привилегированного пользователя для MySQL, удаление анонимных учетных записей, отключение удаленного входа с учетными данными привилегированного пользователя, удаление тестовых баз данных и перезагрузку таблицы привилегий. 

После перезагрузки сервера повторно подключитесь к виртуальной машине по протоколу SSH.

```azurecli-interactive  
ssh 10.111.112.113
```



```bash
mysql_secure_installation
```

## <a name="sign-in-to-mysql"></a>Вход в MySQL

Теперь можно войти в систему и ввести запрос для перехода в командную строку MySQL.

```bash  
mysql -u root -p
```
После этого вы перейдете в командную строку MySQL, в которой можно вводить инструкции SQL для взаимодействия с базой данных.

Теперь создайте пользователя MySQL.

```   
CREATE USER 'mysqluser'@'localhost' IDENTIFIED BY 'password';
```
   
Точка с запятой (;) в конце строки является символом завершения команды.


## <a name="create-a-database"></a>Создание базы данных


Создайте базу данных и предоставьте разрешения пользователю `mysqluser`.

```   
CREATE DATABASE testdatabase;
GRANT ALL ON testdatabase.* TO 'mysqluser'@'localhost' IDENTIFIED BY 'password';
```
   
Имена пользователей и пароли для базы данных используются только в скриптах, подключающихся к базе данных.  Имена учетных записей базы данных могут не совпадать с учетными записями системы.

Разрешите вход с другого компьютера. В этом примере IP-адрес компьютера, с которого нужно разрешить вход в систему, — *10.112.113.114*.

```   
GRANT ALL ON testdatabase.* TO 'mysqluser'@'10.112.113.114' IDENTIFIED BY 'password';
```
   
Чтобы выйти из программы администрирования базы данных MySQL, введите следующую команду:

```    
quit
```


## <a name="next-steps"></a>Дополнительная информация
Дополнительные сведения о MySQL см. в [этой документации](http://dev.mysql.com/doc/index-topic.html).




