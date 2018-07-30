---
title: Руководство по API распознавания лиц в Java для Android | Документация Майкрософт
titleSuffix: Microsoft Cognitive Services
description: С помощью этого руководства вы создадите простое приложение Android, которое использует службу распознавания лиц Cognitive Services для обнаружения и выделения лиц на изображении.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: face-api
ms.topic: tutorial
ms.date: 07/12/2018
ms.author: nolachar
ms.openlocfilehash: ad7b85b378db9e9687b5f8081bc9832e91e9ee5e
ms.sourcegitcommit: b9786bd755c68d602525f75109bbe6521ee06587
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/18/2018
ms.locfileid: "39125642"
---
# <a name="tutorial-create-an-android-app-to-detect-and-frame-faces-in-an-image"></a>Руководство. Создание приложения Android для обнаружения и выделения лиц на изображении

С помощью этого руководства вы создадите простое приложение Android, которое использует библиотеку классов Java службы распознавания лиц для обнаружения лиц людей на изображении. Приложение отображает выбранное изображение, на котором каждое обнаруженное лицо выделено прямоугольником. Полный образец кода можно найти в GitHub на странице [Detect and frame faces in an image on Android](https://github.com/Azure-Samples/cognitive-services-face-android-sample) (Обнаружение и выделение лиц на изображении в Android).

![Снимок экрана устройства Android с фотографией, на которой лица выделены красными прямоугольниками](../Images/android_getstarted2.1.PNG)

В этом учебнике описаны следующие процедуры:

> [!div class="checklist"]
> - создание приложения Android;
> - установка клиентской библиотеки службы распознавания лиц;
> - использование клиентской библиотеки для обнаружения лиц на изображении;
> - выделение рамкой каждого обнаруженного лица.

## <a name="prerequisites"></a>Предварительные требования

- Чтобы выполнить пример, нужен ключ подписки. Вы можете получить ключи бесплатной пробной подписки на странице [Пробная версия Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=face-api).
- Интерфейс [Android Studio](https://developer.android.com/studio/) с пакетом SDK версии не ниже 22 (требуется для клиентской библиотеки службы распознавания лиц).
- Клиентская библиотека службы распознавания лиц [com.microsoft.projectoxford:face:1.4.3](http://search.maven.org/#search%7Cga%7C1%7Cg%3A%22com.microsoft.projectoxford%22) из Maven. Скачивать пакет не нужно. Инструкции по установке приведены ниже.

## <a name="create-the-project"></a>Создание проекта

Создайте проект приложения Android, выполнив следующие действия:

1. Откройте Android Studio. В этом руководстве используется Android Studio 3.1.
1. Выберите **Start a new Android Studio project** (Создать проект Android Studio).
1. На экране **Create Android Project** (Создание проекта Android) при необходимости измените значения в полях по умолчанию, а затем нажмите кнопку **Next** (Далее).
1. На экране **Target Android Devices** (Целевые устройства Android) используйте селектор раскрывающегося списка, чтобы выбрать **API 22** или более поздней версии, а затем нажмите кнопку **Next** (Далее).
1. Выберите значение **Empty Activity** (Пустое действие), затем нажмите кнопку **Next** (Далее).
1. Снимите флажок **Backwards Compatibility** (Обратная совместимость), затем нажмите кнопку **Finish** (Готово).

## <a name="create-the-ui-for-selecting-and-displaying-the-image"></a>Создание пользовательского интерфейса для выбора и отображения изображения

Откройте файл *activity_main.xml*. Отобразится редактор макета. Выберите вкладку **Text** (Текст) и замените ее содержимое следующим кодом.

```xml
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <ImageView
        android:layout_width="match_parent"
        android:layout_height="fill_parent"
        android:id="@+id/imageView1"
        android:layout_above="@+id/button1"
        android:contentDescription="Image with faces to analyze"/>

    <Button
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Browse for face image"
        android:id="@+id/button1"
        android:layout_alignParentBottom="true"/>
</RelativeLayout>
```

Откройте файл *MainActivity.java*, затем замените все, кроме первой инструкции `package`, кодом, приведенным ниже.

Код устанавливает обработчик событий на элемент `Button`, который запускает новое действие, позволяя пользователю выбрать изображение. Выбранное изображение отображается в `ImageView`.

```java
import java.io.*;
import android.app.*;
import android.content.*;
import android.net.*;
import android.os.*;
import android.view.*;
import android.graphics.*;
import android.widget.*;
import android.provider.*;

public class MainActivity extends Activity {
    private final int PICK_IMAGE = 1;
    private ProgressDialog detectionProgressDialog;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
            Button button1 = (Button)findViewById(R.id.button1);
            button1.setOnClickListener(new View.OnClickListener() {
                @Override
                public void onClick(View v) {
                Intent intent = new Intent(Intent.ACTION_GET_CONTENT);
                intent.setType("image/*");
                startActivityForResult(Intent.createChooser(
                        intent, "Select Picture"), PICK_IMAGE);
            }
        });

        detectionProgressDialog = new ProgressDialog(this);
    }

    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        super.onActivityResult(requestCode, resultCode, data);
        if (requestCode == PICK_IMAGE && resultCode == RESULT_OK &&
                data != null && data.getData() != null) {
            Uri uri = data.getData();
            try {
                Bitmap bitmap = MediaStore.Images.Media.getBitmap(
                        getContentResolver(), uri);
                ImageView imageView = (ImageView) findViewById(R.id.imageView1);
                imageView.setImageBitmap(bitmap);

                // Uncomment
                //detectAndFrame(bitmap);
                } catch (IOException e) {
                    e.printStackTrace();
                }
        }
    }
}
```

Теперь в приложении можно будет выбрать фотографию, и она отобразится в окне, как показано на иллюстрации ниже.

![Снимок экрана устройства Android с фотографией лиц](../Images/android_getstarted1.1.PNG)

## <a name="configure-the-face-client-library"></a>Настройка клиентской библиотеки службы распознавания лиц

API распознавания лиц представляет собой облачный API-интерфейс, который можно вызывать с помощью HTTPS-запросов. В этом руководстве используется клиентская библиотека службы распознавания лиц, которая содержит эти веб-запросы для упрощения работы.

В области **Project** (Проект) с помощью селектора раскрывающегося списка выберите **Android**. Разверните раздел **Gradle Scripts** (Скрипты Gradle), а затем откройте *build.gradle (Module: app)*.

Добавьте зависимость `com.microsoft.projectoxford:face:1.4.3` для клиентской библиотеки службы распознавания лиц, как показано на следующем снимке экрана, а затем щелкните **Sync Now** (Синхронизировать).

![Снимок экрана Android Studio с изображением файла приложения build.gradle](../Images/face-tut-java-gradle.png)

Откройте файл **MainActivity.java** и добавьте следующие директивы импорта:

```java
import com.microsoft.projectoxford.face.*;
import com.microsoft.projectoxford.face.contract.*;
```

## <a name="add-the-face-client-library-code"></a>Добавление кода клиентской библиотеки службы распознавания лиц

Вставьте следующий код в класс `MainActivity` над методом `onCreate`:

```java
private final String apiEndpoint = "<API endpoint>";
private final String subscriptionKey = "<Subscription Key>";

private final FaceServiceClient faceServiceClient =
        new FaceServiceRestClient(apiEndpoint, subscriptionKey);
```

Замените `<API endpoint>` конечной точкой API, которая была назначена вашему ключу. Ключи бесплатной пробной подписки создаются в регионе **westcentralus**. Поэтому, если вы используете ключ бесплатной пробной подписки, инструкция будет выглядеть так:

```java
apiEndpoint = "https://westcentralus.api.cognitive.microsoft.com/face/v1.0";
```

Замените `<Subscription Key>` ключом своей подписки. Например: 

```java
subscriptionKey = "0123456789abcdef0123456789ABCDEF"
```

В области **Project** (Проект) разверните раздел **app** (приложение), затем **manifests** (манифесты) и откройте файл *AndroidManifest.xml*.

Добавьте следующий элемент в качестве непосредственного дочернего элемента для элемента `manifest`:

```xml
<uses-permission android:name="android.permission.INTERNET" />
```

Выполните сборку проекта, чтобы проверить его на наличие ошибок. Теперь все готово для вызова службы распознавания лиц.

## <a name="upload-an-image-to-detect-faces"></a>Отправка изображения для обнаружения лиц

Самый простой способ обнаружения лиц — вызов метода `FaceServiceClient.detect`. Этот метод создает оболочку для метода API [Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) (Обнаружение) и возвращает массив элементов `Face` (Лицо).

Каждый возвращенный элемент `Face` выделяется прямоугольником, указывающим его расположение, и дополняется рядом необязательных атрибутов лица. В этом примере обязательным является только расположение лиц.

Если возникает ошибка, в окне `AlertDialog` отображается основная причина.

Добавьте следующие методы в класс `MainActivity`.

```java
// Detect faces by uploading a face image.
// Frame faces after detection.
private void detectAndFrame(final Bitmap imageBitmap) {
    ByteArrayOutputStream outputStream = new ByteArrayOutputStream();
    imageBitmap.compress(Bitmap.CompressFormat.JPEG, 100, outputStream);
    ByteArrayInputStream inputStream =
            new ByteArrayInputStream(outputStream.toByteArray());

    AsyncTask<InputStream, String, Face[]> detectTask =
            new AsyncTask<InputStream, String, Face[]>() {
                String exceptionMessage = "";

                @Override
                protected Face[] doInBackground(InputStream... params) {
                    try {
                        publishProgress("Detecting...");
                        Face[] result = faceServiceClient.detect(
                                params[0],
                                true,         // returnFaceId
                                false,        // returnFaceLandmarks
                                null          // returnFaceAttributes:
                                /* new FaceServiceClient.FaceAttributeType[] {
                                    FaceServiceClient.FaceAttributeType.Age,
                                    FaceServiceClient.FaceAttributeType.Gender }
                                */
                        );
                        if (result == null){
                            publishProgress(
                                    "Detection Finished. Nothing detected");
                            return null;
                        }
                        publishProgress(String.format(
                                "Detection Finished. %d face(s) detected",
                                result.length));
                        return result;
                    } catch (Exception e) {
                        exceptionMessage = String.format(
                                "Detection failed: %s", e.getMessage());
                        return null;
                    }
                }

                @Override
                protected void onPreExecute() {
                    //TODO: show progress dialog
                }
                @Override
                protected void onProgressUpdate(String... progress) {
                    //TODO: update progress
                }
                @Override
                protected void onPostExecute(Face[] result) {
                    //TODO: update face frames
                }
            };

    detectTask.execute(inputStream);
}

private void showError(String message) {
    new AlertDialog.Builder(this)
    .setTitle("Error")
    .setMessage(message)
    .setPositiveButton("OK", new DialogInterface.OnClickListener() {
            public void onClick(DialogInterface dialog, int id) {
        }})
    .create().show();
}
```

## <a name="frame-faces-in-the-image"></a>Выделение лиц на изображении

Вставьте следующий вспомогательный метод в класс `MainActivity`. Этот метод создает прямоугольник вокруг каждого обнаруженного лица.

```java
private static Bitmap drawFaceRectanglesOnBitmap(
        Bitmap originalBitmap, Face[] faces) {
    Bitmap bitmap = originalBitmap.copy(Bitmap.Config.ARGB_8888, true);
    Canvas canvas = new Canvas(bitmap);
    Paint paint = new Paint();
    paint.setAntiAlias(true);
    paint.setStyle(Paint.Style.STROKE);
    paint.setColor(Color.RED);
    paint.setStrokeWidth(10);
    if (faces != null) {
        for (Face face : faces) {
            FaceRectangle faceRectangle = face.faceRectangle;
            canvas.drawRect(
                    faceRectangle.left,
                    faceRectangle.top,
                    faceRectangle.left + faceRectangle.width,
                    faceRectangle.top + faceRectangle.height,
                    paint);
        }
    }
    return bitmap;
}
```

Завершите методы `AsyncTask`, обозначенные в комментариях `TODO`, в методе `detectAndFrame`. В случае успешного выполнения на выбранном изображении в `ImageView` лица выделяются рамкой.

```java
@Override
protected void onPreExecute() {
    detectionProgressDialog.show();
}
@Override
protected void onProgressUpdate(String... progress) {
    detectionProgressDialog.setMessage(progress[0]);
}
@Override
protected void onPostExecute(Face[] result) {
    detectionProgressDialog.dismiss();
    if(!exceptionMessage.equals("")){
        showError(exceptionMessage);
    }
    if (result == null) return;
    ImageView imageView = findViewById(R.id.imageView1);
    imageView.setImageBitmap(
            drawFaceRectanglesOnBitmap(imageBitmap, result));
    imageBitmap.recycle();
}
```

Наконец, в методе `onActivityResult` раскомментируйте вызов метода `detectAndFrame`, как показано ниже.

```java
@Override
protected void onActivityResult(int requestCode, int resultCode, Intent data) {
    super.onActivityResult(requestCode, resultCode, data);

    if (requestCode == PICK_IMAGE && resultCode == RESULT_OK &&
                data != null && data.getData() != null) {
        Uri uri = data.getData();
        try {
            Bitmap bitmap = MediaStore.Images.Media.getBitmap(
                    getContentResolver(), uri);
            ImageView imageView = findViewById(R.id.imageView1);
            imageView.setImageBitmap(bitmap);

            // Uncomment
            detectAndFrame(bitmap);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

## <a name="run-the-app"></a>Запуск приложения

Запустите приложение и с помощью обзора выберите изображение, на котором присутствует лицо. Подождите несколько секунд, чтобы служба распознавания лиц смогла ответить. После этого вы получите примерно такой результат:

![GettingStartAndroid](../Images/android_getstarted2.1.PNG)

## <a name="summary"></a>Сводка

С помощью этого руководства вы изучили базовый процесс использования службы распознавания лиц и создали приложение, в котором лица на изображениях выделяются рамкой.

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения об обнаружении лиц и использовании ориентиров для их обнаружения.

> [!div class="nextstepaction"]
> [Как обнаруживать лица на изображении](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)

Ознакомьтесь с API-интерфейсами распознавания лиц, которые используются для обнаружения лиц и их атрибутов, таких как поза, пол, возраст, положение головы, наличие усов, бороды и очков.

> [!div class="nextstepaction"]
> [Справочник по API распознавания лиц](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)