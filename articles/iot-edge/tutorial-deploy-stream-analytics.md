---
title: Руководство по развертывание заданий ASA на устройствах Azure IoT Edge | Документация Майкрософт
description: Развертывание Azure Stream Analytics в качестве модуля на устройстве IoT Edge
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/25/2018
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: ad2895a457a20632823260f2429ac95fad82089c
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37060201"
---
# <a name="tutorial-deploy-azure-stream-analytics-as-an-iot-edge-module---preview"></a>Руководство по развертыванию Azure Stream Analytics в качестве модуля IoT Edge (предварительная версия)

Многие решения Интернета вещей используют службы аналитики, чтобы получать аналитические сведения о данных по мере того, как они попадают в облако с устройств Интернета вещей. С помощью Azure IoT Edge можно переместить логику [Azure Stream Analytics][azure-stream] на само устройство. Обрабатывая потоки данных телеметрии в Edge, можно сократить объем отправленных данных и время, необходимое для реагирования на полезные аналитические сведения.

Azure IoT Edge и Azure Stream Analytics интегрированы, поэтому вы можете создать задание Azure Stream Analytics на портале Azure и затем развернуть его в качестве модуля IoT Edge без дополнительного кода.  

Azure Stream Analytics предоставляет расширенный синтаксис структурированных запросов для анализа данных как в облаке, так и на устройствах IoT Edge. Дополнительные сведения об Azure Stream Analytics в IoT Edge см. в статье [Azure Stream Analytics в IoT Edge (предварительная версия)](../stream-analytics/stream-analytics-edge.md).

В этом руководстве модуль Stream Analytics вычисляет среднюю температуру в течение скользящего 30-секундного окна. Когда это значение достигает 70, модуль отправляет предупреждение, чтобы устройство выполнило действие. В этом случае действие заключается в сбросе имитируемого датчика температуры. В рабочей среде вы можете использовать эту функцию для выключения компьютера или принятия профилактических мер, когда температура достигает опасных уровней. 

Из этого руководства вы узнаете, как выполнять такие задачи:

> [!div class="checklist"]
> * Создание задания Azure Stream Analytics для обработки пограничных данных.
> * Подключение нового задания Azure Stream Analytics с другими модулями IoT Edge.
> * Развертывание задания Azure Stream Analytics на устройстве IoT Edge с портала Azure.

>[!NOTE]
>Модули Azure Stream Analytics для IoT Edge находятся в [общедоступной предварительной версии](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>предварительным требованиям

* Центр Интернета вещей
* Устройство Azure IoT Edge, которое вы создали и настроили при работе с кратким руководством для [Windows][lnk-quickstart-win] или [Linux][lnk-quickstart-lin]. 

## <a name="create-an-azure-stream-analytics-job"></a>Создание задания Azure Stream Analytics

В этом разделе создается задание Azure Stream Analytics для получения данных из вашего Центра Интернета вещей, запроса данных телеметрии, отправленных с вашего устройства, и передачи результатов в контейнер хранилища BLOB-объектов Azure. Дополнительные сведения см. в разделе "Обзор" [документации по Azure Stream Analytics][azure-stream]. 

### <a name="create-a-storage-account"></a>Создание учетной записи хранения

Для задач Azure Stream Analytics требуется учетная запись службы хранилища Azure, которая выступает в качестве конечной точки для выходных данных задания. В примере в этом разделе используется тип хранилища BLOB-объектов. Дополнительные сведения см. в разделе "BLOB-объекты" [документации по службе хранилища Azure][azure-storage].

1. На портале Azure выберите **Создать ресурс**, введите **Учетная запись хранения** в поле поиска, а затем выберите **Учетная запись хранения — BLOB-объект, файл, таблица, очередь**.

2. В области **Создать учетную запись хранения** введите имя для учетной записи хранения, выберите расположение, где хранится ваш Центр Интернета вещей, выберите ту же группу ресурсов, что и для Центра Интернета вещей, а затем щелкните **Создать**. Запишите имя для использования в дальнейшем.

    ![Создание учетной записи хранения][1]


### <a name="create-a-stream-analytics-job"></a>Создание задания Stream Analytics

1. На портале Azure выберите **Создать ресурс** > **Интернет вещей**, а затем — **Задание Stream Analytics**.

2. В области **Новое задание Stream Analytics** выполните следующие действия.

   1. В поле **Job name** (Имя задания) введите имя задания.
   
   2. Используйте ту же **группу ресурсов** и **расположение**, что и для Центра Интернета вещей. 

      > [!NOTE]
      > Сейчас задания Azure Stream Analytics в IoT Edge не поддерживаются в регионе "Западная часть США 2". 

   3. В разделе **Среда размещения**выберите **Edge**.
    
3. Нажмите кнопку **Создать**.

4. В созданном задании в разделе **Топология задания** откройте **Входные данные**.

   ![Входные данные Azure Stream Analytics](./media/tutorial-deploy-stream-analytics/asa_input.png)

5. Выберите **Добавить потоковый вход**, а затем выберите **Центр Edge**.

6. На панели **Новые входные данные** введите **temperature** как псевдоним входных данных. 

7. Щелкните **Сохранить**.

8. В разделе **Топология задания** щелкните **Выходные данные**.

   ![Выходные данные Azure Stream Analytics](./media/tutorial-deploy-stream-analytics/asa_output.png)

9. Щелкните **Добавить**, а затем выберите **Центр Edge**.

10. На панели **Новые выходные данные** введите **alert** как псевдоним выходных данных. 

11. Щелкните **Сохранить**.

12. В разделе **Топология задания** выберите **Запрос**, а затем замените текст по умолчанию следующим запросом, который создает оповещение, если средняя температура машины за 30 секунд достигает 70 градусов:

    ```sql
    SELECT  
        'reset' AS command 
    INTO 
       alert 
    FROM 
       temperature TIMESTAMP BY timeCreated 
    GROUP BY TumblingWindow(second,30) 
    HAVING Avg(machine.temperature) > 70
    ```

13. Щелкните **Сохранить**.

14. В разделе **Настройка** выберите **Параметры IoT Edge**.

15. Выберите **Учетная запись хранения** из раскрывающегося списка.

16. Для поля **Контейнер** выберите **Создать новый** и укажите имя для контейнера хранилища. 

17. Щелкните **Сохранить**. 


## <a name="deploy-the-job"></a>Развертывание задания

Теперь вы готовы к развертыванию задания Azure Stream Analytics на своем устройстве IoT Edge.

1. На портале Azure в Центре Интернета вещей перейдите к **IoT Edge**, а затем откройте страницу сведений о своем устройстве IoT Edge.

2. Щелкните **Set modules** (Настроить модули).  

   Если модуль tempSensor на этом устройстве уже развернут, эти данные могут быть заполнены автоматически. Если же он не установлен, добавьте модуль следующим образом.

   1. Щелкните **Добавить** и выберите **Модуль IoT Edge**.
   2. В качестве имени введите **tempSensor**.
   3. В качестве универсального кода ресурса (URI) образа введите **mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0**. 
   4. Оставьте без изменений другие параметры.
   5. Щелкните **Сохранить**.

3. Добавьте задание Azure Stream Analytics Edge, выполнив следующие шаги:

   1. Нажмите кнопку **Добавить** и выберите модуль **Azure Stream Analytics**.
   2. Выберите свою подписку и созданное задание Edge Azure Stream Analytics. 
   3. Щелкните **Сохранить**.

4. Щелкните **Далее**.

5. Замените значение по умолчанию во вкладке **Маршруты** на код, приведенный ниже. Обновите _{moduleName}_ на имя своего модуля Azure Stream Analytics. Модуль должен иметь то же имя, что и задание, из которого он был создан. 

    ```json
    {
        "routes": {
            "telemetryToCloud": "FROM /messages/modules/tempSensor/* INTO $upstream",
            "alertsToCloud": "FROM /messages/modules/{moduleName}/* INTO $upstream",
            "alertsToReset": "FROM /messages/modules/{moduleName}/* INTO BrokeredEndpoint(\"/modules/tempSensor/inputs/control\")",
            "telemetryToAsa": "FROM /messages/modules/tempSensor/* INTO BrokeredEndpoint(\"/modules/{moduleName}/inputs/temperature\")"
        }
    }
    ```

6. Щелкните **Далее**.

7. В окне **Review Deployment** (Проверка развертывания) выберите **Отправить**.

8. Вернитесь на страницу сведений об устройстве и выберите **Refresh** (Обновить).  

    Вы должны увидеть новый модуль Stream Analytics, работающий вместе с модулем агента IoT Edge и концентратором IoT Edge.

    ![Выходные данные модуля][7]

## <a name="view-data"></a>Просмотр данных

Теперь можно перейти к устройству IoT Edge, чтобы проверить взаимодействие между модулями Azure Stream Analytics и tempSensor.

1. Проверьте выполнение всех модулей в Docker.

   ```cmd/sh
   iotedge list  
   ```
<!--
   ![Docker output][8]
-->
2. Просмотрите все системные журналы и данные метрик. Используйте имя модуля Stream Analytics.

   ```cmd/sh
   iotedge logs -f {moduleName}  
   ```

Вы должны видать, как температура компьютера постепенно растет, пока не достигнет 70 градусов на 30 секунд. Затем модуль Stream Analytics активирует сброс, и температура компьютера уменьшается до 21 градуса. 

   ![Журнал Docker][9]

## <a name="clean-up-resources"></a>Очистка ресурсов 

<!--[!INCLUDE [iot-edge-quickstarts-clean-up-resources](../../includes/iot-edge-quickstarts-clean-up-resources.md)] -->

При переходе к следующей рекомендованной статье можно сохранить уже созданные ресурсы и конфигурации и повторно их использовать.

В противном случае можно удалить локальные конфигурации и ресурсы Azure, созданные в рамках этой статьи, чтобы избежать расходов. 

> [!IMPORTANT]
> Удаление ресурсов Azure и группы ресурсов является необратимым. Группа ресурсов и все содержащиеся в ней ресурсы удаляются без возможности восстановления. Будьте внимательны, чтобы случайно не удалить не ту группу ресурсов или не те ресурсы. Если вы создали Центр Интернета вещей в группе ресурсов, содержащей ресурсы, которые нужно сохранить, удалите только ресурс Центра Интернета вещей, не удаляя всю группу ресурсов.
>

Чтобы удалить только Центр Интернета вещей, выполните следующую команду, указав имена центра и группы ресурсов:

```azurecli-interactive
az iot hub delete --name MyIoTHub --resource-group TestResources
```


Чтобы удалить группу ресурсов по имени, выполните следующие действия.

1. Войдите на [портал Azure](https://portal.azure.com) и щелкните **Группы ресурсов**.

2. Введите в текстовое поле **Фильтровать по имени...** имя группы ресурсов, содержащей Центр Интернета вещей. 

3. Справа от своей группы ресурсов в списке результатов щелкните **...**, а затем выберите **Удалить группу ресурсов**.

<!--
   ![Delete](./media/iot-edge-quickstarts-clean-up-resources/iot-edge-delete-resource-group.png)
-->
4. Подтвердите операцию удаления группы ресурсов. Еще раз введите имя группы ресурсов для подтверждения и нажмите кнопку **Удалить**. Через некоторое время группа ресурсов и все ее ресурсы будут удалены.

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве вы настроили задание Azure Streaming Analytics для анализа данных с устройства IoT Edge. Затем вы загрузили этот модуль Azure Stream Analytics на устройство IoT Edge, чтобы обрабатывать данные и реагировать на повышение температуры локально, а также отправлять агрегированный поток данных в облако. Вы можете перейти к изучению других руководств, чтобы узнать, как Azure IoT Edge может создавать решения для вашего бизнеса.

> [!div class="nextstepaction"] 
> [Развертывание Машинного обучения Azure в качестве модуля IoT Edge — предварительная версия][lnk-ml-tutorial]

<!-- Images. -->
[1]: ./media/tutorial-deploy-stream-analytics/storage.png
[4]: ./media/tutorial-deploy-stream-analytics/add_device.png
[5]: ./media/tutorial-deploy-stream-analytics/asa_job.png
[6]: ./media/tutorial-deploy-stream-analytics/set_module.png
[7]: ./media/tutorial-deploy-stream-analytics/module_output2.png
[8]: ./media/tutorial-deploy-stream-analytics/docker_output.png
[9]: ./media/tutorial-deploy-stream-analytics/docker_log.png
[10]: ./media/tutorial-deploy-stream-analytics/storage_settings.png
[11]: ./media/tutorial-deploy-stream-analytics/temp_module.png


<!-- Links -->
[lnk-what-is-iot-edge]: what-is-iot-edge.md
[lnk-module-dev]: module-development.md
[iot-hub-get-started-create-hub]: ../../includes/iot-hub-get-started-create-hub.md
[azure-iot]: https://docs.microsoft.com/azure/iot-hub/
[azure-storage]: https://docs.microsoft.com/azure/storage/
[azure-stream]: https://docs.microsoft.com/azure/stream-analytics/
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-quickstart-win]: quickstart.md
[lnk-quickstart-lin]: quickstart-linux.md
[lnk-module-tutorial]: tutorial-csharp-module.md
[lnk-ml-tutorial]: tutorial-deploy-machine-learning.md

