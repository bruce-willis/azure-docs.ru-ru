---
title: Создание центра Интернета вещей на портале Azure | Документация Майкрософт
description: Сведения о том, как с помощью портала Azure создавать и удалять Центры Интернета вещей Azure, а также управлять ими. Содержит сведения о ценовых категориях, масштабировании, безопасности, а также о настройке обмена сообщениями.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/06/2018
ms.author: robinsh
ms.openlocfilehash: 8f08141f5c14a734f89ba91045767e2a36a44fd2
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46985611"
---
# <a name="create-an-iot-hub-using-the-azure-portal"></a>Создание Центра Интернета вещей с помощью портала Azure

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

В этой статье описывается создание Центров Интернета вещей и управление ими с помощью [портала Azure](https://portal.azure.com).

Для выполнения шагов в этом руководстве вам потребуется подписка Azure. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="create-an-iot-hub"></a>Создание Центра Интернета вещей

1. Войдите на [портал Azure](https://portal.azure.com). 

2. Выберите **+ Создать ресурс**, а затем — **Интернет вещей**.

3. Нажмите кнопку **Центр Интернета вещей** в списке справа. Появится первый экран для создания Центра Интернета вещей.

   ![Снимок экрана создания концентратора на портале Azure](./media/iot-hub-create-through-portal/iot-hub-create-screen-basics.png)

   Заполните поля.

   **Подписка**. Выберите нужную подписку для Центра Интернета вещей.

   **Группа ресурсов**. Вы можете создать новую группу ресурсов или использовать существующую. Чтобы создать новую, выберите **Создать** и введите нужное имя. Чтобы использовать существующую группу, щелкните **Использовать существующий** и выберите группу ресурсов из раскрывающегося списка.

   **Регион**. В раскрывающемся списке выберите регион, в котором будет расположен Центр Интернета вещей.

   **Имя Центра Интернета вещей**. Введите Имя Центра Интернета вещей. Оно должно быть глобально уникальным. 

   [!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

4. Выберите **Next: Size and scale** (Далее: размер и масштаб), чтобы перейти к следующему экрану.

   ![Снимок экрана, где показаны параметры размера и масштаба для нового Центра Интернета вещей на портале Azure](./media/iot-hub-create-through-portal/iot-hub-create-screen-size-scale.png)

   На этом экране можно оставить значения по умолчанию и просто выбрать **Review + create** (Просмотреть и создать) внизу. При необходимости поля можно заполнить.

   **Ценовая категория и категория масштабирования**. В зависимости от количества необходимых функций и ежедневно отправляемых сообщений с помощью решения можно выбрать несколько уровней. Для тестирования и оценки можно использовать уровень "Бесплатный". Он позволяет подключить к Центру Интернета вещей 500 устройств и отправлять до 8000 сообщений в день. Для каждой подписки Azure можно создать один Центр Интернета вещей на уровне "Бесплатный". 

   **Единицы центра IoT Hub**. Допустимое число сообщений за единицу в сутки зависит от ценовой категории концентратора. Например, если требуется, чтобы Центр Интернета вещей поддерживал 700 000 входящих сообщений, то следует выбрать две единицы уровня S1.

   Дополнительные сведения о других параметрах уровня см. в статье [Масштабирование решения для Центра Интернета вещей](iot-hub-scaling.md).

   **Дополнительные параметры / Разделы "Из устройства в облако"**. Это свойство привязывает сообщения, отправляемые с устройства в облако, к числу одновременно работающих модулей чтения этих сообщений. Для большинства центров Интернета вещей достаточно четырех секций. 

5. Нажмите кнопку **Review + create** (Просмотреть и создать), чтобы просмотреть выбранные параметры. Отобразится примерно следующий экран.

   ![Снимок экрана, где показана информация для создания центра Интернета вещей](./media/iot-hub-create-through-portal/iot-hub-create-review.png)

5. Нажмите кнопку **Создать**, чтобы создать центр Интернета вещей. Создание центра занимает несколько минут.

## <a name="change-the-settings-of-the-iot-hub"></a>Изменение параметров Центра Интернета вещей

Вы можете изменить параметры Центра Интернета вещей после его создания, используя область этого центра.

![Снимок экрана, показывающий параметры для Центра Интернета вещей](./media/iot-hub-create-through-portal/iot-hub-settings-panel.png)

Ниже приведены некоторые свойства, которые можно задать для центра Интернета вещей:

**Цены и масштабирование**. Это свойство можно использовать для миграции на другой уровень или для того, чтобы задать количество единиц Центра Интернета вещей. 

**Мониторинг операций**. Включение и отключение различных категорий мониторинга, таких как ведение журнала событий, связанных с сообщениями, отправляемыми с устройства в облако и принимаемыми из облака на устройство.

**Фильтрация IP-адресов**. Здесь можно указать диапазон IP-адресов, который будет принят или отклонен центром Интернета вещей.

**Свойства**. Содержит список свойств, например идентификатор ресурса, группу ресурсов, расположение и т. д., которые можно скопировать и использовать в другом месте.

### <a name="shared-access-policies"></a>Политики общего доступа

Можно также просмотреть или изменить список политик общего доступа, выбрав **Политики общего доступа** в разделе **Параметры**. Эти политики определяют разрешения для подключения устройств и служб к Центру Интернета вещей. 

Выберите **Добавить**, чтобы открыть колонку **Добавление политики общего доступа**.  Вы можете ввести новое имя политики и указать разрешения, которые нужно связать с ней. Эта колонка показана на следующем рисунке:

![Снимок экрана добавления политики общего доступа](./media/iot-hub-create-through-portal/iot-hub-add-shared-access-policy.png)

* Политики **Чтение реестра** и **Запись в реестр** предоставляют права на чтение и запись в реестре удостоверений. Если вы выберете разрешение на запись, разрешение на чтение добавляется автоматически.

* Политика **Подключение службы** предоставляет разрешение на доступ к конечным точкам службы, например для **передачи данных от устройства в облако**. 

* Политика **Подключение устройства** предоставляет разрешения на отправку и получение сообщений через конечные точки Центра Интернета вещей на стороне устройства.

Чтобы добавить новую политику к списку уже существующих, нажмите кнопку **Создать** .

## <a name="message-routing-for-an-iot-hub"></a>Маршрутизация сообщений для центра Интернета вещей

В разделе **Маршрутизация сообщений** выберите **Обмен сообщениями**, чтобы просмотреть область "Маршрутизация сообщений", где можно определить пути и пользовательские конечные точки для центра. [Маршрутизация сообщений](iot-hub-devguide-messages-d2c.md) дает возможность управлять способом отправки данных с устройств к конечным точкам. Первым шагом является добавление нового маршрута. Затем можно добавить в маршрут существующую конечную точку или создать новую конечную точку одного из поддерживаемых типов, например хранилище BLOB-объектов. 

![Область "Маршрутизация сообщений"](./media/iot-hub-create-through-portal/iot-hub-message-routing.png)

### <a name="routes"></a>Маршруты

"Маршруты" — это первая вкладка в области "Маршрутизация сообщений". Чтобы добавить новый маршрут, щелкните **+ Добавить**. Вы увидите приведенный ниже экран. 

![Снимок экрана добавления нового маршрута](./media/iot-hub-create-through-portal/iot-hub-add-route-storage-endpoint.png)

Укажите имя центра. Имя должно быть уникальным в пределах списка маршрутов для этого центра. 

Для параметра **Конечная точка** можно выбрать существующую конечную точку из раскрывающегося списка или добавить новую. В этом примере учетная запись хранения и контейнер уже доступны. Чтобы добавить их в качестве конечной точки, нажмите кнопку **+ Добавить** рядом с раскрывающимся списком конечной точки и выберите **Хранилище BLOB-объектов**. На следующем экране показано, где заданы учетная запись хранения и контейнер.

![Снимок экрана, на котором показано добавление конечной точки хранилища для правила маршрутизации](./media/iot-hub-create-through-portal/iot-hub-routing-add-storage-endpoint.png)

Щелкните **Выберите контейнер**, чтобы выбрать учетную запись хранения и контейнер. Если выбрать эти поля, произойдет переход в область конечной точки. Используйте значения по умолчанию для остальных полей и нажмите кнопку **Создать**, чтобы создать конечную точку для учетной записи хранения и добавить ее в правила маршрутизации.

Для параметра **Источник данных** выберите "Сообщения телеметрии устройства". 

Затем добавьте запрос на маршрутизацию. В этом примере сообщения, для которых задано свойство приложения `level` со значением `critical`, маршрутизируются в учетную запись хранения.

![Снимок экрана, где показано сохранение нового правила маршрутизации](./media/iot-hub-create-through-portal/iot-hub-add-route.png)

Нажмите кнопку **Сохранить**, чтобы сохранить правило маршрутизации. Вы вернетесь к области "Маршрутизация сообщений", где отображается новое правило маршрутизации.

### <a name="custom-endpoints"></a>Пользовательские конечные точки

Выберите вкладку **Пользовательские конечные точки**. Вы увидите созданные пользовательские конечные точки. На этой странице можно добавить новые конечные точки или удалить существующие. 

> [!NOTE]
> При удалении маршрута присвоенные ему конечные точки не удаляются. Чтобы удалить конечную точку, выберите вкладку "Пользовательские конечные точки", а затем выберите соответствующую конечную точку и нажмите кнопку "Удалить".
>

Дополнительные сведения о пользовательских конечных точках см. в статье [Руководство. Конечные точки Центра Интернета вещей](iot-hub-devguide-endpoints.md).

Для центра Интернета вещей можно определить до 10 пользовательских конечных точек. 

Полный пример использования конечных точек с маршрутизацией см. в статье [Руководство. Настройка маршрутизации сообщений с Центром Интернета вещей](tutorial-routing.md).

## <a name="find-a-specific-iot-hub"></a>Поиск определенного центра Интернета вещей

Ниже приведено два способа поиска определенного центра Интернета вещей в подписке:

1. Если вы знаете группу ресурсов, к которой принадлежит центр Интернета вещей, щелкните **Группы ресурсов**, а затем выберите группу ресурсов из списка. На экране группы ресурсов показаны все ресурсы в этой группе, включая центры Интернета вещей. Щелкните центр, который вы искали.

2. Выберите **Все ресурсы**. В области **Все ресурсы** есть раскрывающийся список, который по умолчанию имеет значение `All types`. Щелкните раскрывающийся список и снимите флажок `Select all`. Найдите `IoT Hub` и установите напротив него флажок. Щелкните раскрывающийся список, чтобы закрыть его. Записи будут отфильтрованы так, чтобы отображались только ваши центры Интернета вещей.

## <a name="delete-the-iot-hub"></a>Удаление Центра Интернета вещей

Чтобы удалить центр Интернета вещей, найдите его, а затем нажмите кнопку **Удалить** под именем центра Интернета вещей.

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения об управлении Центром Интернета вещей в Azure см. по следующим ссылкам:

* [Руководство. Настройка маршрутизации сообщений с Центром Интернета вещей](tutorial-routing.md)
* [Общие сведения о метриках Центра Интернета вещей](iot-hub-metrics.md)
* [Мониторинг операций](iot-hub-operations-monitoring.md)