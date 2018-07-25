---
title: Настройка автоматизации виртуальной глобальной сети Azure для партнеров виртуальной глобальной сети | Документация Майкрософт
description: Эта статья поможет партнерам по решениям для программно-определяемых подключений настроить автоматизацию Виртуальной глобальной сети Azure.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 07/10/2018
ms.author: cherylmc
Customer intent: As a Virtual WAN software-defined connectivity provider, I want to set up a provisioning environment.
ms.openlocfilehash: a1ff4364e394b3807cf767722ee934ae024399b0
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/18/2018
ms.locfileid: "39114350"
---
# <a name="configure-virtual-wan-automation---for-virtual-wan-partners-preview"></a>Настройка автоматизации Виртуальной глобальной сети для партнеров Виртуальной глобальной сети (предварительная версия)

Эта статья поможет понять, как настроить среду службы автоматизации для подключения и настроить устройство филиала (на локальном VPN-устройстве клиента или SDWAN) Виртуальной глобальной сети Azure. Если вы являетесь поставщиком, предоставляющим устройства для филиалов, обеспечивающие работу VPN-подключения через IPsec/IKEv2, эта статья предназначена для вас.

Программно-определяемые решения обычно используют контроллер или центр подготовки устройств для управления своими устройствами для филиалов. Контроллер может использовать API-интерфейсы Azure для автоматизации подключения к виртуальной глобальной сети Azure. Для этого типа подключения требуется локальное VPN-устройство или SDWAN, которому назначен внешний общедоступный IP-адрес.

##  <a name="access"></a>Контроль доступа

Клиенты должны иметь возможность установить соответствующее управление доступом для Виртуальной глобальной сети в пользовательском интерфейсе устройства. Этот вариант рекомендуется с помощью субъекта-службы Azure. Доступ на основе субъекта-службы обеспечивает правильную аутентификацию контроллера устройства для загрузки информации о филиале.

##  <a name="site"></a>Отправка информации о филиале

Создайте пользовательский интерфейс для загрузки информации о филиале (локальном сайте) в Azure. Интерфейсы [REST API](https://docs.microsoft.com/rest/api/virtualwan/vpnsites) для **VPNSite** могут использоваться для создания данных сайта в виртуальной глобальной сети. Вы можете выбрать все VPN-устройства или SDWAN для филиалов или выбрать соответствующие настройки устройств.

##  <a name="hub"></a>Центр и службы

После загрузки устройства филиала в Azure клиент, как правило, осуществляет выбор региона или служб узла на портале Azure, что вызывает выполнение ряда операций для создания центра виртуальной сети с конечной точкой VPN внутри. VPN-шлюз — это масштабируемый шлюз, размер которого зависит от пропускной способности и потребностей подключения.

## <a name="device"></a>Конфигурация устройства

На этом этапе клиент, который не использует поставщик, может вручную загрузить конфигурацию Azure и применить ее к своему локальному VPN-устройству или SDWAN. Вам как поставщику следует автоматизировать этот этап. Контроллер может вызывать REST API **GetVpnConfiguration**, чтобы загрузить конфигурацию Azure, которая обычно выглядит так, как в следующем файле.

**Примечания к конфигурации**

  * Если виртуальные сети Azure присоединяются к виртуальному центру, они отображаются как ConnectedSubnets.
  * VPN-подключение использует конфигурации на основе маршрута и IKEv2.

### <a name="understanding-the-device-configuration-file"></a>Основные сведения о файле конфигурации устройства

Файл конфигурации устройства содержит параметры, которые необходимо использовать при настройке локального VPN-устройства. При просмотре этого файла обратите внимание на следующие сведения:

* **vpnSiteConfiguration**. В этом разделе указаны сведения об устройстве, настроенные как сайт, подключенный к виртуальной глобальной сети. Они содержат имя и общедоступный IP-адрес устройства филиала.
* **vpnSiteConnections**. В этом разделе представлены следующие сведения:

    * **Адресное пространство** виртуальной сети виртуальных концентраторов.<br>Пример:
 
        ```
        "AddressSpace":"10.1.0.0/24"
        ```
    * **Адресное пространство** виртуальных сетей, подключенных к концентратору.<br>Пример:

         ```
        "ConnectedSubnets":["10.2.0.0/16","10.30.0.0/16"]
         ```
    * **IP-адреса** VPN-шлюзов виртуального концентратора. Так как каждое подключение VPN-шлюза состоит из 2 туннелей в конфигурации "активный — активный", в этом файле будут указаны оба IP-адреса. В данном примере вы видите Instance0 и Instance1 для каждого сайта.<br>Пример:

        ``` 
        "Instance0":"104.45.18.186"
        "Instance1":"104.45.13.195"
        ```
    * **Сведения о конфигурации подключения к Vpngateway**, такие как протокол BGP, общий ключ и т. д. PSK — это автоматически созданный общий ключ. Вы всегда можете изменить подключение на странице обзора для настраиваемого ключа PSK.
  
### <a name="example-device-configuration-file"></a>Пример файла конфигурации устройства

  ```
  { 
      "configurationVersion":{ 
         "LastUpdatedTime":"2018-07-03T18:29:49.8405161Z",
         "Version":"r403583d-9c82-4cb8-8570-1cbbcd9983b5"
      },
      "vpnSiteConfiguration":{ 
         "Name":"testsite1",
         "IPAddress":"73.239.3.208"
      },
      "vpnSiteConnections":[ 
         { 
            "hubConfiguration":{ 
               "AddressSpace":"10.1.0.0/24",
               "Region":"West Europe",
               "ConnectedSubnets":[ 
                  "10.2.0.0/16",
                  "10.30.0.0/16"
               ]
            },
            "gatewayConfiguration":{ 
               "IpAddresses":{ 
                  "Instance0":"104.45.18.186",
                  "Instance1":"104.45.13.195"
               }
            },
            "connectionConfiguration":{ 
               "IsBgpEnabled":false,
               "PSK":"bkOWe5dPPqkx0DfFE3tyuP7y3oYqAEbI",
               "IPsecParameters":{ 
                  "SADataSizeInKilobytes":102400000,
                  "SALifeTimeInSeconds":3600
               }
            }
         }
      ]
   },
   { 
      "configurationVersion":{ 
         "LastUpdatedTime":"2018-07-03T18:29:49.8405161Z",
         "Version":"1f33f891-e1ab-42b8-8d8c-c024d337bcac"
      },
      "vpnSiteConfiguration":{ 
         "Name":" testsite2",
         "IPAddress":"66.193.205.122"
      },
      "vpnSiteConnections":[ 
         { 
            "hubConfiguration":{ 
               "AddressSpace":"10.1.0.0/24",
               "Region":"West Europe"
            },
            "gatewayConfiguration":{ 
               "IpAddresses":{ 
                  "Instance0":"104.45.18.187",
                  "Instance1":"104.45.13.195"
               }
            },
            "connectionConfiguration":{ 
               "IsBgpEnabled":false,
               "PSK":"XzODPyAYQqFs4ai9WzrJour0qLzeg7Qg",
               "IPsecParameters":{ 
                  "SADataSizeInKilobytes":102400000,
                  "SALifeTimeInSeconds":3600
               }
            }
         }
      ]
   },
   { 
      "configurationVersion":{ 
         "LastUpdatedTime":"2018-07-03T18:29:49.8405161Z",
         "Version":"cd1e4a23-96bd-43a9-93b5-b51c2a945c7"
      },
      "vpnSiteConfiguration":{ 
         "Name":" testsite3",
         "IPAddress":"182.71.123.228"
      },
      "vpnSiteConnections":[ 
         { 
            "hubConfiguration":{ 
               "AddressSpace":"10.1.0.0/24",
               "Region":"West Europe"
            },
            "gatewayConfiguration":{ 
               "IpAddresses":{ 
                  "Instance0":"104.45.18.187",
                  "Instance1":"104.45.13.195"
               }
            },
            "connectionConfiguration":{ 
               "IsBgpEnabled":false,
               "PSK":"YLkSdSYd4wjjEThR3aIxaXaqNdxUwSo9",
               "IPsecParameters":{ 
                  "SADataSizeInKilobytes":102400000,
                  "SALifeTimeInSeconds":3600
               }
            }
         }
      ]
   }
  ```

## <a name="default"></a>Политики по умолчанию

### <a name="initiator"></a>Инициатор

В следующих разделах перечислены сочетания поддерживаемой политики, когда Azure является инициатором для туннеля.

**Этап 1**

* AES_256, SHA1, DH_GROUP_2
* AES_256, SHA_256, DH_GROUP_2
* AES_128, SHA1, DH_GROUP_2
* AES_128, SHA_256, DH_GROUP_2
* 3DES, SHA1, DH_GROUP_2
* 3DES, SHA_256, DH_GROUP_2

**Этап 2**

* GCM_AES_256, GCM_AES_256, PFS_NONE
* AES_256, SHA_1, PFS_NONE
* CBC_3DES, SHA_1, PFS_NONE
* AES_256, SHA_256, PFS_NONE
* AES_128, SHA_1, PFS_NONE
* CBC_3DES, SHA_256, PFS_NONE


### <a name="responder"></a>Отвечающее устройство

В следующих разделах перечислены сочетания поддерживаемой политики, когда Azure является отвечающим устройством для туннеля.

**Этап 1**

* AES_256, SHA1, DH_GROUP_2
* AES_256, SHA_256, DH_GROUP_2
* AES_128, SHA1, DH_GROUP_2
* AES_128, SHA_256, DH_GROUP_2
* 3DES, SHA1, DH_GROUP_2
* 3DES, SHA_256, DH_GROUP_2

**Этап 2**

* GCM_AES_256, GCM_AES_256, PFS_NONE
* AES_256, SHA_1, PFS_NONE
* CBC_3DES, SHA_1, PFS_NONE
* AES_256, SHA_256, PFS_NONE
* AES_128, SHA_1, PFS_NONE
* CBC_3DES, SHA_256, PFS_NONE
* CBC_DES, SHA_1, PFS_NONE 
* AES_256, SHA_1, PFS_1
* AES_256, SHA_1, PFS_2
* AES_256, SHA_1, PFS_14
* AES_128, SHA_1, PFS_1
* AES_128, SHA_1, PFS_2
* AES_128, SHA_1, PFS_14
* CBC_3DES, SHA_1, PFS_1
* CBC_3DES, SHA_1, PFS_2
* CBC_3DES, SHA_256, PFS_2
* AES_256, SHA_256, PFS_1
* AES_256, SHA_256, PFS_2
* AES_256, SHA_256, PFS_14
* AES_256, SHA_1, PFS_24
* AES_256, SHA_256, PFS_24
* AES_128, SHA_256, PFS_NONE
* AES_128, SHA_256, PFS_1
* AES_128, SHA_256, PFS_2
* AES_128, SHA_256, PFS_14
* CBC_3DES, SHA_1, PFS_14

### <a name="does-everything-need-to-match-between-the-virtual-hub-vpngateway-policy-and-my-on-premises-sdwanvpn-device-or-sd-wan-configuration"></a>Должны ли политика шлюза виртуального центра и конфигурация локального VPN-устройства / SDWAN или конфигурация SD-WAN полностью совпадать?

Ваша конфигурация локальных VPN-устройств / SDWAN или SD-WAN должна совпадать со следующими алгоритмами и параметрами, указанными в политике Azure IPsec/IKE, или содержать их. Время существования SA указывается исключительно в локальных спецификациях и эти значения не обязательно должны совпадать.

* Алгоритм шифрования IKE
* Алгоритм проверки целостности IKE
* Группа DH
* Алгоритм шифрования IPsec
* Алгоритм проверки целостности IPsec
* Группа PFS

## <a name="feedback"></a>Отзывы о предварительной версии

Ваши отзывы важны для нас! Отправьте электронное письмо на адрес <azurevirtualwan@microsoft.com>, чтобы сообщить о любых проблемах или предоставить отзыв (положительный или отрицательный) о Виртуальной глобальной сети. В строке темы заключите название компании в скобки []. Если вы сообщаете о проблеме, укажите идентификатор подписки.

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о Виртуальной глобальной сети см. в разделе [Сведения о Виртуальной глобальной сети Azure](virtual-wan-about.md) и [Часто задаваемые вопросы о Виртуальной глобальной сети Azure (WAN)](virtual-wan-faq.md).
