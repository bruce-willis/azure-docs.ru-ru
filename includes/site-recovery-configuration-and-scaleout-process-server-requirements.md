---
title: включение файла
description: включение файла
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: include
ms.date: 06/10/2018
ms.author: raynew
ms.custom: include file
ms.openlocfilehash: 669966ce21c5c6c2d0653eb51c81fe78aa0b3a12
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/14/2018
ms.locfileid: "39057316"
---
**Требования к серверам конфигурации и обработки**

**Компонент** | **Требование** 
--- | ---
**Параметры оборудования** | 
Ядра ЦП | 8 
ОЗУ | 16 ГБ
Количество дисков | 3 (диск ОС, диск кэша сервера обработки, диск хранения (для восстановления размещения)) 
Свободное место на диске (кэш сервера обработки) | 600 ГБ
Свободное место на диске (диск хранения) | 600 ГБ
 | 
**Параметры программного обеспечения** | 
Операционная система | Windows Server 2012 R2 <br> Windows Server 2016
Язык операционной системы | Английский (en-us)
Роли Windows Server | Не включайте эти роли: <br> — доменные службы Active Directory; <br>— службы IIS; <br> — Hyper-V. 
Групповые политики | Не включать эти групповые политики: <br> — запрет на использование командной строки; <br> — запрет на использование инструментов редактирования реестра; <br> — логика доверия для вложенных файлов; <br> — включение выполнения скриптов. <br> [Подробнее](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)
IIS | — должен отсутствовать предварительно созданный веб-сайт по умолчанию; <br> — должен отсутствовать предварительно созданный веб-сайт или приложение, ожидающее передачи данных через порт 443. <br>— включите [анонимную аутентификацию](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx); <br> — включите параметр [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx); 
| 
**Параметры сети** | 
Тип IP-адреса | Статическое 
Доступ к Интернету | Сервер должен иметь доступ к этим URL-адресам (напрямую или через прокси-сервер) <br> - \*.accesscontrol.windows.net<br> - \*.backup.windowsazure.com <br>- \*.store.core.windows.net<br> - \*.blob.core.windows.net<br> - \*.hypervrecoverymanager.windowsazure.com  <br> - https://management.azure.com <br> - *.services.visualstudio.com <br> time.nist.gov <br> time.windows.com <br> OVF также необходим доступ к следующим URL-адресам <br> - https://login.microsoftonline.com <br> - https://secure.aadcdn.microsoftonline-p.com <br> - https://login.live.com  <br> - https://auth.gfx.ms <br> - https://graph.windows.net <br> - https://login.windows.net <br> - https://www.live.com <br> - https://www.microsoft.com <br> - https://dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi 
порты; | 443 (оркестрация канала управления)<br>9443 (передача данных) 
Тип сетевой карты | VMXNET3 (если сервером конфигурации является виртуальная машина VMware)
 | 
**Устанавливаемое программное обеспечение** | 
VMware vSphere PowerCLI | Если сервер конфигурации работает на виртуальной машине VMware, должен быть установлен компонент [PowerCLI версии 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1).
MySQL | Должен быть установлен компонент MySQL. Его можно установить вручную, или его может установить Site Recovery.

**Требования к размеру серверов конфигурации и обработки**

**ЦП** | **Память** | **Диск кэша** | **Частота изменения данных** | **Реплицируемые компьютеры**
--- | --- | --- | --- | ---
8 виртуальных ЦП<br/><br/> 2 сокета по 4 ядра с частотой \@ 2,5 ГГц | 16 ГБ | 300 ГБ | 500 ГБ или менее | < 100 компьютеров
12 виртуальных ЦП<br/><br/> 2 сокета по 6 ядер с частотой \@ 2,5 ГГц | 18 ГБ | 600 ГБ | 500 ГБ — 1 ТБ | От 100 до 150 компьютеров
16 виртуальных ЦП<br/><br/> 2 сокета по 8 ядер с частотой \@ 2,5 ГГц | 32 ГБ | 1 TБ | 1-2 TБ | От 150 до 200 компьютеров

