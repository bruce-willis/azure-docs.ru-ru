---
title: Распространенные вопросы о службе "Сетка Azure Service Fabric" | Документация Майкрософт
description: Изучите ответы на распространенные вопросы о службе "Сетка Azure Service Fabric".
services: service-fabric-mesh
keywords: ''
author: chackdan
ms.author: chackdan
ms.date: 06/25/2018
ms.topic: troubleshooting
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: 07c0347793f6541a3e047f3f357d0d1b05dc3bca
ms.sourcegitcommit: dc646da9fbefcc06c0e11c6a358724b42abb1438
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/18/2018
ms.locfileid: "39136187"
---
# <a name="commonly-asked-service-fabric-mesh-questions"></a>Распространенные вопросы о службе "Сетка Service Fabric"
Сетка Azure Service Fabric — это полностью управляемая служба, которая позволяет разработчикам развертывать приложения для микрослужб без управления виртуальными машинами, хранилищем или сетями. Эта статья содержит ответы на часто задаваемые вопросы.

## <a name="how-do-i-report-an-issue-or-ask-a-question"></a>Как сообщить о проблеме или задать вопрос?

Можно задать вопросы, получить ответы от инженеров Майкрософт и сообщить о проблемах в [репозитории GitHub service-fabric-mesh-preview](https://aka.ms/sfmeshissues).

## <a name="quota-and-cost"></a>Квоты и стоимость

**Какова стоимость использования предварительной версии?**

Плата за развертывание приложений или контейнеров к предварительной версии службы "Сетка" не взимается. Тем не менее рекомендуется удалять развернутые ресурсы и не оставлять их работающими, если только они не используются для тестирования.

**Существует ли предел квоты числа ядер и объема ОЗУ?**

Да, ниже приведены соответствующие квоты.

- Число приложений: 5. 
- Число ядер на приложение: 12. 
- Общий объем оперативной памяти на приложение: 48 ГБ. 
- Число конечных точек сети и входящего трафика: 5.  
- Число томов Azure, которые можно подключить: 10. 
- Число реплик службы: 3. 
- Наибольший контейнер, который вы можете развернуть, может использовать 4 ядра и 16 ГБ ОЗУ.
- Вы можете выделять для контейнеров частичные ядра с шагом в 0,5 ядра, но не более 6 ядер.

**Можно оставить мои приложения работать на ночь?**

Да, это возможно. Тем не менее рекомендуется удалять развернутые ресурсы и не оставлять их работающими, если только они не используются для тестирования. В будущем эта политика может измениться, и ресурсы могут быть удалены, если они используются неправильно.

## <a name="supported-container-os-images"></a>Поддерживаемые образы ОС контейнера
Ниже приведены образы ОС контейнера, которые можно использовать при развертывании служб.

- Windows: windowsservercore и nanoserver
    - Windows Server 2016
    - Windows Server, версия 1709
- Linux
    - Известные ограничения отсутствуют

## <a name="features-gaps-and-known-issues"></a>Недостатки функций и известные проблемы

**После развертывания приложения сетевой ресурс, связанный с ним, не имеет IP-адреса**

На сегодня известна проблема с IP-адресом, который назначается с задержкой. Проверьте состояние сетевого ресурса через несколько минут, чтобы просмотреть связанный IP-адрес.

**Мое приложение не может получить доступ к соответствующему сетевому ресурсу или ресурсу тома**

В модели приложения необходимо использовать полный идентификатор ресурса для сетей и томов, чтобы иметь к ним доступ. Ниже приведен пример из краткого руководства.

```json
"networkRefs": [
    {
    "name":  "[resourceId('Microsoft.ServiceFabric/networks', 'SbzVotingNetwork')]" 
    }
]
```

**Я не вижу текущую модель приложения, поддерживающую способ шифрования моих секретов**

Да, шифрование секретов в текущей закрытой предварительной версии не поддерживается. 

**DNS работает не так, как в кластере разработки Service Fabric и службе "Сетка"**

Известна проблема, из-за которой могут по-разному указываться ссылки на службы в локальном кластере разработки и службе "Сетка Azure". В локальном кластере разработки используйте формат {имя_службы}. {имя_приложения}. В службе "Сетка Azure Service Fabric" используйте формат {имя_службы}. Сейчас служба "Сетка Azure" не поддерживает разрешение DNS между приложениями.

Другие известные проблемы с DNS при запуске кластера разработки Service Fabric в Windows 10 описаны здесь: [Практическое руководство. Отладка контейнеров Windows в Azure Service Fabric с помощью Visual Studio 2017](/azure/service-fabric/service-fabric-how-to-debug-windows-containers).

**При использовании модуля интерфейса командной строки _ImportError возникает ошибка: не удается импортировать имя "sdk_no_wait"**

Если вы используете версию интерфейса командной строки, предшествующую версии 2.0.30, может произойти приведенная ниже ошибка.

```
cannot import name 'sdk_no_wait'
Traceback (most recent call last):
File "C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\knack\cli.py", line 193, in invoke cmd_result = self.invocation.execute(args)
File "C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\azure\cli\core\commands_init_.py", line 241, in execute self.commands_loader.load_arguments(command)
File "C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\azure\cli\core_init_.py", line 201, in load_arguments self.command_table[command].load_arguments() # this loads the arguments via reflection
File "C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\azure\cli\core\commands_init_.py", line 142, in load_arguments super(AzCliCommand, self).load_arguments()
File "C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\knack\commands.py", line 76, in load_arguments cmd_args = self.arguments_loader()
File "C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\azure\cli\core_init_.py", line 332, in default_arguments_loader op = handler or self.get_op_handler(operation)
File "C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\azure\cli\core_init_.py", line 375, in get_op_handler op = import_module(mod_to_import)
File "C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\importlib_init_.py", line 126, in import_module return _bootstrap._gcd_import(name[level:], package, level)
File "", line 978, in _gcd_import
File "", line 961, in _find_and_load
File "", line 950, in _find_and_load_unlocked
File "", line 655, in _load_unlocked
File "", line 678, in exec_module
File "", line 205, in _call_with_frames_removed
File "C:\Users\annayak.azure\cliextensions\azure-cli-sbz\azext_sbz\custom.py", line 18, in 
from azure.cli.core.util import get_file_json, shell_safe_json_parse, sdk_no_wait
ImportError: cannot import name 'sdk_no_wait'.
```

**При установке пакета расширения интерфейса командной строки возникает ошибка несоответствия имени дистрибутива**

Это не означает, что расширение не установлено. Вы сможете использовать команды интерфейса командной строки без каких-либо проблем.

**При развертывании я вижу, что затрагиваются все контейнеры, включая работающие**

Это ошибка, которая должна быть устранена в следующем обновлении для среды выполнения.

## <a name="next-steps"></a>Дополнительная информация

Чтобы узнать больше о службе "Сетка Service Fabric", прочитайте этот [обзор](service-fabric-mesh-overview.md).
