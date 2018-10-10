---
title: Управление Службой подготовки устройств Центра Интернета вещей с помощью Azure CLI и расширения Интернета вещей | Документация Майкрософт
description: Узнайте, как управлять Службой подготовки устройств Центра Интернета вещей с помощью Azure CLI и расширения Интернета вещей
author: chrissie926
ms.author: menchi
ms.date: 01/17/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: briz
ms.openlocfilehash: cfbebf8570ee044698b0f4e0abdd58370b04f759
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46992870"
---
# <a name="how-to-use-azure-cli-and-the-iot-extension-to-manage-the-iot-hub-device-provisioning-service"></a>Как управлять Службой подготовки устройств Центра Интернета вещей с помощью Azure CLI и расширения Интернета вещей

[Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) — это кроссплатформенная программа командной строки с открытым кодом для управления ресурсами Azure (например, IoT Edge). Интерфейс Azure CLI доступен для операционных систем Windows, Linux и MacOS. Azure CLI позволяет управлять ресурсами Центра Интернета вещей Azure, экземплярами службы подготовки устройств и связанными концентраторами без дополнительной настройки.

Расширение Интернета вещей расширяет функции Azure CLI (например, функция управления устройствами) и добавляет возможности IoT Edge.

В этом руководстве описаны настройки Azure CLI и расширения Интернета вещей. Затем вы узнаете, как запускать команды CLI для выполнения основных операций службы подготовки устройств. 

## <a name="installation"></a>Установка 

### <a name="step-1---install-python"></a>Шаг 1. Установка Python

Необходимы [Python 2.7x или Python 3.x](https://www.python.org/downloads/).

### <a name="step-2---install-the-azure-cli"></a>Шаг 2. Установка Azure CLI

Выполните [инструкции по установке](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest), чтобы настроить Azure CLI в своей среде. Вам понадобится как минимум Azure CLI версии 2.0.24 или более поздней. Для проверки используйте `az –version`. Эта версия поддерживает команды расширения az и представляет собой платформу команд Knack. Простой способ установки в Windows — скачать и установить [MSI](https://aka.ms/InstallAzureCliWindows).

### <a name="step-3---install-iot-extension"></a>Шаг 3. Установка расширения Интернета вещей

В [файле сведений расширения Интернета вещей](https://github.com/Azure/azure-iot-cli-extension) описывается несколько способов установки расширения. Проще всего запустить `az extension add --name azure-cli-iot-ext`. После установки можно использовать `az extension list`, чтобы проверить установленные расширения, или `az extension show --name azure-cli-iot-ext` для просмотра сведений о расширении Интернета вещей. Чтобы удалить расширение, можно использовать `az extension remove --name azure-cli-iot-ext`.


## <a name="basic-device-provisioning-service-operations"></a>Основные операции службы подготовки устройств
В примере показано, как войти в учетную запись Azure, создать группу ресурсов Azure (контейнер, содержащий связанные ресурсы для решения Azure), создать Центр Интернета вещей, службу подготовки устройств, список имеющихся служб подготовки устройств и связанный Центр Интернета вещей с командами CLI. 

Перед началом работы выполните описанные выше шаги по установке. Если у вас нет учетной записи Azure, вы можете создать [бесплатную учетную запись](https://azure.microsoft.com/free/?v=17.39a). 


### <a name="1-log-in-to-the-azure-account"></a>1. Войдите в учетную запись Azure
  
    az login

![вход][1]

### <a name="2-create-a-resource-group-iothubblogdemo-in-eastus"></a>2. Создайте группу ресурсов IoTHubBlogDemo в регионе eastus

    az group create -l eastus -n IoTHubBlogDemo

![Создать группу ресурсов][2]


### <a name="3-create-two-device-provisioning-services"></a>3. Создайте две службы подготовки устройств

    az iot dps create --resource-group IoTHubBlogDemo --name demodps

![Создание службы подготовки устройств][3]

    az iot dps create --resource-group IoTHubBlogDemo --name demodps2

### <a name="4-list-all-the-existing-device-provisioning-services-under-this-resource-group"></a>4. Создайте список всех имеющихся служб подготовки устройств в этой группе ресурсов

    az iot dps list --resource-group IoTHubBlogDemo

![Создание списка службы подготовки устройств][4]


### <a name="5-create-an-iot-hub-blogdemohub-under-the-newly-created-resource-group"></a>5. Создайте Центр Интернета вещей blogDemoHub в созданной группе ресурсов.

    az iot hub create --name blogDemoHub --resource-group IoTHubBlogDemo

![Создание Центра Интернета вещей][5]

### <a name="6-link-one-existing-iot-hub-to-a-device-provisioning-service"></a>6. Свяжите один имеющийся Центр Интернета вещей со службой подготовки устройств

    az iot dps linked-hub create --resource-group IoTHubBlogDemo --dps-name demodps --connection-string <connection string> -l westus

![Связывание Центра Интернета вещей][5]

<!-- Images -->
[1]: ./media/how-to-manage-dps-with-cli/login.jpg
[2]: ./media/how-to-manage-dps-with-cli/create-resource-group.jpg
[3]: ./media/how-to-manage-dps-with-cli/create-dps.jpg
[4]: ./media/how-to-manage-dps-with-cli/list-dps.jpg
[5]: ./media/how-to-manage-dps-with-cli/create-hub.jpg
[6]: ./media/how-to-manage-dps-with-cli/link-hub.jpg


## <a name="next-steps"></a>Дополнительная информация
Из этого руководства вы узнали, как выполнить следующие задачи:

> [!div class="checklist"]
> * Регистрация устройства
> * запуск устройства;
> * проверка регистрации устройства.

Перейдите к следующему руководству, чтобы узнать, как подготовить несколько устройств через концентраторы с балансировкой нагрузки. 

> [!div class="nextstepaction"]
> [Подготовка устройств в Центрах Интернета вещей с балансировкой нагрузки](./tutorial-provision-multiple-hubs.md)
