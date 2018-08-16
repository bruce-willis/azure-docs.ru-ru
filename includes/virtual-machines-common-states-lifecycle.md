---
title: включение файла
description: включение файла
services: virtual-machines
author: shandilvarun
ms.service: virtual-machines
ms.topic: include
ms.date: 08/09/2018
ms.author: vashan, cynthn, rajsqr
ms.custom: include file
ms.openlocfilehash: 603e7c3a0c30eb42cb75d6a6ff87a96d847b7c9f
ms.sourcegitcommit: a2ae233e20e670e2f9e6b75e83253bd301f5067c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/13/2018
ms.locfileid: "40100644"
---
Виртуальные машины Azure проходят различные состояния, которые могут быть отнесены к состояниям *подготовки* и состояниям *включения*. Цель этой статьи — описать эти состояния и показать, когда клиентам будет выставлен счет, например, за использование экземпляра. 

## <a name="power-states"></a>Состояния включения

Состояние включения представляет собой последнее известное состояние виртуальной машины.

![Схема состояния включения виртуальной машины](./media/virtual-machines-common-states-lifecycle/vm-power-states.png)

<br>
В следующей таблице представлено описание каждого состояния экземпляра и указано, будет ли выставлен счет за его использование.

<table>
<tr>
<th>
Состояние
</th>
<th>
ОПИСАНИЕ
</th>
<th>
Счет за использование экземпляра
</th>
</tr>
<tr>
<td>
<p><b>Запуск</b></p>
</td>
<td>
<p>Выполняется запуск виртуальной машины.</p>
<code>"statuses": [<br>
   {<br>
      "code": "PowerState/starting",<br>
       "level": "Info",<br>
        "displayStatus": "VM starting"<br>
    }<br>
    ]</code><br>
</td>
<td>
<p><b>Счет не выставляется</b></p>
</td>
</tr>
<tr>
<td>
<p><b>Выполнение</b></p>
</td>
<td>
<p>Нормальное рабочее состояние для виртуальной машины</p>
<code>"statuses": [<br>
 {<br>
 "code": "PowerState/running",<br>
 "level": "Info",<br>
 "displayStatus": "VM running"<br>
 }<br>
 ]</code><br>
</td>
<td>
<p><b>Счет выставлен</b></p>
</td>
</tr>
<tr>
<td>
<p><b>Остановка</b></p>
</td>
<td>
<p>Это переходное состояние. По завершении оно будет отображаться как **Остановлено**.</p>
<code>"statuses": [<br>
 {<br>
 "code": "PowerState/stopping",<br>
 "level": "Info",<br>
 "displayStatus": "VM stopping"<br>
 }<br>
 ]</code><br>
</td>
<td>
<p><b>Счет выставлен</b></p>
</td>
</tr>
<tr>
<td>
<p><b>Stopped</b></p>
</td>
<td>
<p>Виртуальная машина была отключена из гостевой ОС или с помощью API PowerOff.</p>
<p>Оборудование по-прежнему выделяется на виртуальную машину и остается на узле. </p>
<code>"statuses": [<br>
 {<br>
 "code": "PowerState/stopped",<br>
 "level": "Info",<br>
 "displayStatus": "VM stopped"<br>
 }<br>
 ]</code><br>
</td>
<td>
<p><b>Не выставлен счет*</b></p>
</td>
</tr>
<tr>
<td>
<p><b>Освобождение</b></p>
</td>
<td>
<p>Переходное состояние. По завершении виртуальная машина будет отображаться как **Освобождена**.</p>
<code>"statuses": [<br>
 {<br>
 "code": "PowerState/deallocating",<br>
 "level": "Info",<br>
 "displayStatus": "VM deallocating"<br>
 }<br>
 ]</code><br>
</td>
<td>
<p><b>Не выставлен счет*</b></p>
</td>
</tr>
<tr>
<td>
<p><b>Освобождено</b></p>
</td>
<td>
<p>Виртуальная машина была успешно остановлена и удалена с узла. </p>
<code>"statuses": [<br>
 {<br>
 "code": "PowerState/deallocated",<br>
 "level": "Info",<br>
 "displayStatus": "VM deallocated"<br>
 }<br>
 ]</code><br>
</td>
<td>
<p><b>Счет не выставляется</b></p>
</td>
</tr>
</tbody>
</table>


*Некоторые ресурсы Azure, такие как диски и сети, несут расходы, независимо от состояния экземпляра. 

## <a name="provisioning-states"></a>Состояния подготовки

Состояние подготовки — это состояние инициированной пользователем операции уровня управления на виртуальной машине. Эти состояния отделены от состояния включения виртуальной машины.

- **Создание**. Создается виртуальная машина.

- **Обновление**. Обновление модели для существующей виртуальной машины. Некоторые немодельные изменения в виртуальной машине, такие как "Запуск/Перезапуск", также подпадают под обновление.

- **Удаление**. Удаление виртуальной машины.

- **Освобождение**. Это место, где виртуальная машина остановлена и удалена с узла. Освобождение виртуальной машины считается обновлением, поэтому она отображает состояния подготовки, связанные с обновлением.



Ниже приведены состояния переходной операции после того, как платформа приняла инициированное пользователем действие.

<br>

<table>
<tbody>
<tr>
<td width="162">
<p><b>Состояния</b></p>
</td>
<td width="366">
<p>ОПИСАНИЕ</p>
</td>
</tr>
<tr>
<td width="162">
<p><b>Создание</b></p>
</td>
<td width="366">
<code>"statuses": [<br>
 {<br>
 "code": "ProvisioningState/creating",<br>
 "level": "Info",<br>
 "displayStatus": "Creating"<br>
 }</code><br>
</td>
</tr>
<tr>
<td width="162">
<p><b>Обновление</b></p>
</td>
<td width="366">
<code>"statuses": [<br>
 {<br>
 "code": "ProvisioningState/updating",<br>
 "level": "Info",<br>
 "displayStatus": "Updating"<br>
 }<br>
 ]</code><br>
</td>
</tr>
<tr>
<td width="162">
<p><b>Удаление</b></p>
</td>
<td width="366">
<code>"statuses": [<br>
 {<br>
 "code": "ProvisioningState/deleting",<br>
 "level": "Info",<br>
 "displayStatus": "Deleting"<br>
 }<br>
 ]</code><br>
</td>
</tr>
<tr>
<td width="162">
<p><b>Состояния подготовки ОС</b></p>
</td>
<td width="366">
<p>Если виртуальная машина создается с помощью образа ОС, а не специализированного образа, то могут наблюдаться следующие подсостояния.</p>
<p>1. <b>OSProvisioningInprogress</b> &ndash; виртуальная машина работает и выполняется установка гостевой ОС. <p /> 
<code> "statuses": [<br>
 {<br>
 "code": "ProvisioningState/creating/OSProvisioningInprogress",<br>
 "level": "Info",<br>
 "displayStatus": "OS Provisioning In progress"<br>
 }<br>
]</code><br>
<p>2. <b>OSProvisioningComplete</b> &ndash; кратковременное состояние. Виртуальная машина быстро переходит к состоянию **Успешно**, если не нужно устанавливать какие-либо расширения. Установка расширений может занять некоторое время. <br />
<code> "statuses": [<br>
 {<br>
 "code": "ProvisioningState/creating/OSProvisioningComplete",<br>
 "level": "Info",<br>
 "displayStatus": "OS Provisioning Complete"<br>
 }<br>
]</code><br>
<p><b>Примечание</b>. Подготовка ОС может перейти в состояние **Сбой**, если произошел сбой ОС или ОС не устанавливается вовремя. Клиентам будет выставлен счет за развернутую виртуальную машину в инфраструктуре.</p>
</td>
</tr>
</table>


После завершения операции виртуальная машина перейдет в одно из следующих состояний.

- **Успешно**. Действия, инициированные пользователем, завершены.

    ```
 "statuses": [ 
 {
     "code": "ProvisioningState/succeeded",
     "level": "Info",
     "displayStatus": "Provisioning succeeded",
     "time": "time"
 }
 ]
    ```

 

- **Сбой**. Представляет собой сбой при выполнении операции. Для получения дополнительных сведений и возможных решений см. коды ошибок.

    ```
 "statuses": [
    {
      "code": "ProvisioningState/failed/InternalOperationError",
      "level": "Error",
      "displayStatus": "Provisioning failed",
      "message": "Operation abandoned due to internal error. Please try again later.",
      "time": "time"
    }
    ]
    ```



## <a name="vm-instance-view"></a>Представление экземпляра виртуальной машины

API представления экземпляра предоставляет информацию о состоянии выполнения виртуальной машины. Дополнительные сведения см. в разделе [Virtual Machines — Instance View](https://docs.microsoft.com/rest/api/compute/virtualmachines/instanceview) (Просмотр экземпляров виртуальных машин в документации по API).

Обозреватель ресурсов Azure предоставляет простой пользовательский интерфейс для просмотра состояния выполнения виртуальной машины: [Resource Explorer] (https://resources.azure.com/).

Состояния подготовки отображаются в свойствах виртуальной машины и в представлении экземпляра. Состояния включения доступно в представлении экземпляра виртуальной машины. 

