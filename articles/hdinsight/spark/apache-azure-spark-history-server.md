---
title: Отладка и диагностика приложений Spark с использованием расширенного сервера журнала Spark в Azure HDInsight
description: 'Отладка и диагностика приложений Spark с использованием расширенного сервера журнала Spark: Azure HDInsight'
services: hdinsight
ms.service: hdinsight
author: jejiang
ms.author: jejiang
ms.reviewer: jasonh
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 07/12/2018
ms.openlocfilehash: b514f23f2e8a43f99fd5bf5c3afb5ed625ad4472
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/27/2018
ms.locfileid: "43046581"
---
# <a name="use-extended-spark-history-server-to-debug-and-diagnose-spark-applications"></a>Отладка и диагностика приложений Spark с использованием расширенного сервера журнала Spark

Эта статья содержит рекомендации по использованию расширенного сервера журнала Spark для отладки и диагностики готовых и запущенных приложений Spark. Сейчас расширение включает вкладку "Данные" и вкладку "Граф". На вкладке "Данные" пользователи могут проверить входные и выходные данные задания Spark. На вкладке "Граф" пользователи могут проверить поток данных и воспроизвести граф задания.

## <a name="open-the-spark-history-server"></a>Открытие сервера журнала Spark

Сервер журнала Spark — это пользовательский веб-интерфейс для готовых и запущенных приложений Spark. 

### <a name="to-open-the-spark-history-server-web-ui-from-azure-portal"></a>Открытие пользовательского веб-интерфейса сервера журнала Spark на портале Azure

1. Откройте кластер Spark на [портале Azure](https://portal.azure.com/). Дополнительные сведения см. в разделе [Отображение кластеров](../hdinsight-administer-use-portal-linux.md#list-and-show-clusters).
2. В разделе **Быстрые ссылки** щелкните **Панель мониторинга кластера**, а затем — **Сервер журнала Spark**. При появлении запроса введите учетные данные администратора для кластера Spark. 

    ![Сервер журнала Spark](./media/apache-azure-spark-history-server/launch-history-server.png "Сервер журнала Spark")

### <a name="to-open-the-spark-history-server-web-ui-by-url"></a>Открытие пользовательского веб-интерфейса сервера журнала Spark по URL-адресу
Откройте сервер журнала Spark, перейдя по следующему URL-адресу, и замените <ClusterName> именем кластера Spark клиента.

   ```
   https://<ClusterName>.azurehdinsight.net/sparkhistory
   ```

Пользовательский веб-интерфейс сервера журнала Spark выглядит примерно так:

![Сервер журнала HDInsight Spark](./media/apache-azure-spark-history-server/hdinsight-spark-history-server.png)


## <a name="open-the-data-tab-from-spark-history-server"></a>Открытие вкладки "Данные" на сервере журнала Spark
Выберите идентификатор задания, а затем щелкните **Данные** в меню средств, чтобы получить представление данных.

+ Просмотрите вкладки **Входные данные**, **Выходные данные** и **Операции с таблицей**, выбрав их по отдельности.

    ![Вкладки данных](./media/apache-azure-spark-history-server/sparkui-data-tabs.png)

+ Скопируйте все строки, нажав кнопку **Копировать**.

    ![Копирование данных](./media/apache-azure-spark-history-server/sparkui-data-copy.png)

+ Сохраните все данные в виде CSV-файла, нажав кнопку **csv**.

    ![Сохранение данных](./media/apache-azure-spark-history-server/sparkui-data-save.png)

+ Выполните поиск, введя ключевые слова в поле **Поиск**. Результат поиска сразу же отобразится.

    ![Поиск данных](./media/apache-azure-spark-history-server/sparkui-data-search.png)

+ Щелкните заголовок столбца, чтобы отсортировать таблицу, щелкните знак "плюс", чтобы развернуть строку для отображения дополнительных сведений, или щелкните знак "минус", чтобы свернуть строку.

    ![Таблица данных](./media/apache-azure-spark-history-server/sparkui-data-table.png)

+ Загрузите один файл, нажав кнопку **Частичная загрузка**, которая находится справа. После этого выбранный файл загрузится локально. Если файл больше не существует, откроется новая вкладка с сообщениями об ошибке.

    ![Строка загрузки данных](./media/apache-azure-spark-history-server/sparkui-data-download-row.png)

+ Скопируйте полный или относительный путь, выбрав пункт **Копировать полный путь** или **Копировать относительный путь**, которые развертываются из меню загрузки. Для файлов Data Lake Storage Azure выберите **Open in Azure Storage Explorer** (Открыть в Обозревателе службы хранилища Azure), чтобы запустить Обозреватель службы хранилища Azure, и найдите папку после входа в систему.

    ![Путь копирования данных](./media/apache-azure-spark-history-server/sparkui-data-copy-path.png)

+ Щелкните число, указанное под таблицей, чтобы переходить между страницами, если на одной странице отображается слишком много строк. 

    ![Страница данных](./media/apache-azure-spark-history-server/sparkui-data-page.png)

+ Наведите указатель мыши на вопросительный знак рядом с заголовком "Данные", чтобы увидеть подсказку, или щелкните вопросительный знак, чтобы получить дополнительные сведения.

    ![Дополнительные сведения о данных](./media/apache-azure-spark-history-server/sparkui-data-more-info.png)

+ Отправьте отзыв о проблемах, щелкнув **Provide us feedback** (Оставьте отзыв).

    ![отзыв о графе](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)


## <a name="open-the-graph-tab-from-spark-history-server"></a>Открытие вкладки "Граф" на сервере журнала Spark
Выберите идентификатор задания, а затем щелкните **Граф** в меню средств, чтобы получить представление графа задания.

+ Просмотрите сведения о задании путем создания графа задания. 

+ По умолчанию отображаются все задания. Их можно отфильтровать по **идентификатору задания**.

    ![идентификатор задания графа](./media/apache-azure-spark-history-server/sparkui-graph-jobid.png)

+ По умолчанию выбрано значение **Выполнение**. Пользователь может проверить поток данных, выбрав **Read/Written** (Прочитано и записано) в раскрывающемся списке **Отображать**.

    ![отображение графа](./media/apache-azure-spark-history-server/sparkui-graph-display.png)

    Узел графа отображается цветом, который показывает тепловую карту.

    ![тепловая карта графа](./media/apache-azure-spark-history-server/sparkui-graph-heatmap.png)

+ Воспроизведите задание, нажав кнопку **Воспроизведение**. Вы можете его остановить в любое время, нажав кнопку "Остановить". Задача отображается в определенном цвете для отображения разных состояний при воспроизведении:

    + Зеленый при успешном выполнении: задание выполнено успешно.
    + Оранжевый при повторе: экземпляры задач, которые не удалось выполнить, но они не влияют на результат задания. Эти задачи дублируют или повторно выполняют экземпляры, которые могут быть успешно выполнены позже.
    + Красный при сбое: не удалось выполнить задачу.
    + Синий при выполнении: задача выполняется.
    + Белый при пропуске или ожидании: задача ожидает выполнения или пропущен этап.

    ![пример цвета графа, выполняется](./media/apache-azure-spark-history-server/sparkui-graph-color-running.png)
 
    ![пример цвета графа, сбой](./media/apache-azure-spark-history-server/sparkui-graph-color-failed.png)
 
    > [!NOTE]
    > Допускается воспроизведение для каждого задания. Если задание не состоит из этапов или еще не завершено, воспроизведение не поддерживается.


+ Прокручивайте колесико мыши, чтобы увеличить или уменьшить граф задания, или нажмите кнопку **Масштабировать по размеру**, чтобы масштабировать по размеру экрана.
 
    ![масштабировать по размеру экрана графа](./media/apache-azure-spark-history-server/sparkui-graph-zoom2fit.png)

+ Наведите указатель мыши на узел графа, чтобы увидеть подсказку при наличии невыполненных задач, и щелкните этап, чтобы открыть страницу этапа.

    ![подсказка графа](./media/apache-azure-spark-history-server/sparkui-graph-tooltip.png)

+ На узле графа задания будут отображаться следующие сведения о каждом этапе:
    + Идентификатор.
    + Имя или описание.
    + Общее количество задач.
    + Чтение данных: сумма размера входных данных и размер данных чтения в случайном порядке.
    + Запись данных: сумма размера выходных данных и размер данных записи в случайном порядке.
    + Время выполнения: время между временем начала первой попытки и временем завершения последней попытки.
    + Количество строк: сумма входных записей, выходных записей, операций чтения и записи в случайном порядке.
    + Ход выполнения.

    > [!NOTE]
    > По умолчанию на узле графа задания отображаются данные о последней попытке каждого этапа (за исключением времени выполнения этапа), но во время воспроизведения на узле графа отображаются сведения о каждой попытке.

    > [!NOTE]
    > Для размера данных чтения и записи мы используем 1 МБ = 1000 КБ = 1000 * 1000 байт.

+ Отправьте отзыв о проблемах, щелкнув **Provide us feedback** (Оставьте отзыв).

    ![отзыв о графе](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)


## <a name="faq"></a>Часто задаваемые вопросы

### <a name="1-revert-to-community-version"></a>1. Возврат к версии для сообщества

Чтобы вернуться к версии для сообщества, сделайте следующее:

1. Откройте кластер в Ambari. Нажмите кнопку **Spark2** на левой панели.
2. Щелкните вкладку **Configs** (Конфигурации).
3. Разверните группу **Custom spark2-defaults**.
4. Щелкните **Add Property** (Добавить свойство), добавьте **spark.ui.enhancement.enabled=false** и сохраните.
5. Это свойство задает значение **false**.
6. Чтобы сохранить конфигурацию, нажмите кнопку **Сохранить**.

    ![отключение функции](./media/apache-azure-spark-history-server/sparkui-turn-off.png)

7. Щелкните **Spark2** на левой панели, на вкладке **Summary** (Сводка) щелкните **Spark2 History Server** (Сервер журнала Spark2).

    ![перезапуск сервера1](./media/apache-azure-spark-history-server/sparkui-restart-1.png) 

8. Перезапустите сервер журнала, выбрав пункт **перезапуска** для **сервера журнала Spark2**.

    ![перезапуск сервера2](./media/apache-azure-spark-history-server/sparkui-restart-2.png)  

9. Обновите веб-интерфейс сервера журнала Spark. Он будет возвращен к версии для сообщества.

### <a name="2-upload-history-server-event"></a>2. Отправка события сервера журнала

Если возникла ошибка сервера журнала, выполните шаги, чтобы указать событие.
1. Загрузите событие, щелкнув **Загрузить** в веб-интерфейсе сервера журнала.

    ![загрузка события](./media/apache-azure-spark-history-server/sparkui-download-event.png)

2. Нажмите кнопку **Provide us feedback** (Оставьте отзыв) на вкладке данных или графа.

    ![отзыв о графе](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)

3. Укажите название и описание ошибки, перетащите ZIP-файл в поле редактирования, а затем нажмите кнопку **Submit new issue** (Отправить новую проблему).

    ![сообщить о проблеме](./media/apache-azure-spark-history-server/sparkui-file-issue.png)


### <a name="3-upgrade-jar-file-for-hotfix-scenario"></a>3. Обновления JAR-файла для сценария исправления

Если требуется выполнить обновление с применением исправления, используйте приведенный ниже скрипт, который обновит файл spark-enhancement.jar*.

**upgrade_spark_enhancement.sh**:

   ```bash
    #!/usr/bin/env bash

    # Copyright (C) Microsoft Corporation. All rights reserved.

    # Arguments:
    # $1 Enhancement jar path

    if [ "$#" -ne 1 ]; then
        >&2 echo "Please provide the upgrade jar path."
        exit 1
    fi

    install_jar() {
        tmp_jar_path="/tmp/spark-enhancement-hotfix-$( date +%s )"

        if wget -O "$tmp_jar_path" "$2"; then
            for FILE in "$1"/spark-enhancement*.jar
            do
                back_up_path="$FILE.original.$( date +%s )"
                echo "Back up $FILE to $back_up_path"
                mv "$FILE" "$back_up_path"
                echo "Copy the hotfix jar file from $tmp_jar_path   to $FILE"
                cp "$tmp_jar_path" "$FILE"

                "Hotfix done."
                break
            done
        else    
            >&2 echo "Download jar file failed."
            exit 1
        fi
    }

    jars_folder="/usr/hdp/current/spark2-client/jars"
    jar_path=$1

    if ls ${jars_folder}/spark-enhancement*.jar 1>/dev/null 2>&1;   then
        install_jar "$jars_folder" "$jar_path"
    else
        >&2 echo "There is no target jar on this node. Exit with no action."
        exit 0
    fi
   ```

**Использование**: 

`upgrade_spark_enhancement.sh https://${jar_path}`

**Пример**:

`upgrade_spark_enhancement.sh https://${account_name}.blob.core.windows.net/packages/jars/spark-enhancement-${version}.tgz` 

**Использование bash-файла на портале Azure**

1. Запустите [портал Azure](https://ms.portal.azure.com) и выберите свой кластер.
2. Щелкните **Действия скрипта**, затем — **Отправить новое**. Заполните форму **Отправка действия скрипта**, а затем нажмите кнопку **Создать**.
    
    + **Тип скрипта**: выберите **Пользовательский**.
    + **Имя**: укажите имя скрипта.
    + **URI bash-скрипта**: отправьте bash-файл в частный кластер, а затем скопируйте URL-адрес здесь. Или используйте указанный URI.
    
   ```upgrade_spark_enhancement
    https://hdinsighttoolingstorage.blob.core.windows.net/shsscriptactions/upgrade_spark_enhancement.sh
   ```

    + Установите флажки **Головной** и **Рабочий**.
    + **Параметры**: задайте параметры согласно использованию в bash.

    ![отправка журнала или обновление исправлений](./media/apache-azure-spark-history-server/sparkui-upload2.png)


## <a name="known-issue"></a>Известная проблема

1.  Сейчас это работает только для кластера Spark 2.3.

2.  Входные и выходные данные с использованием устойчивого распределенного набора данных не будут отображаться на вкладке "Данные".

## <a name="next-steps"></a>Дополнительная информация

* [Управление ресурсами для кластера Apache Spark в Azure HDInsight](apache-spark-resource-manager.md)
* [Настройка параметров Spark](apache-spark-settings.md)


## <a name="contact-us"></a>Свяжитесь с нами

С любыми отзывами, а также в случае возникновения проблем при работе с этим инструментом обращайтесь по электронному адресу ([hdivstool@microsoft.com](mailto:hdivstool@microsoft.com)).
