---
title: Руководство по API распознавания лиц в Java для Android | Документация Майкрософт
titleSuffix: Microsoft Cognitive Services
description: Создание простого приложения Android, которое использует API распознавания лиц Microsoft Cognitive Services для обнаружения и выделения человеческих лиц на изображении.
services: cognitive-services
author: SteveMSFT
manager: corncar
ms.service: cognitive-services
ms.component: face-api
ms.topic: article
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: 5164a261d482d0cca3842a973d2109b17999bd25
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35382549"
---
# <a name="getting-started-with-face-api-in-java-for-android-tutorial"></a>Руководство по началу работы с API распознавания лиц в Java для Android

С помощью этого руководства вы научитесь создавать и разрабатывать простое приложение Android, которое вызывает API распознавания лиц для обнаружения человеческих лиц на изображении. После обработки изображения приложением все обнаруженные лица заключаются в рамки.

![GettingStartedAndroid](../Images/android_getstarted2.1.PNG)

## <a name="preparation"></a> Подготовка

Для работы с руководством вам потребуется следующее:

- Android Studio и пакет SDK;
- устройство Android для тестирования (необязательно).

## <a name="step1"></a>Шаг 1. Подписка на API распознавания лиц и получение ключа подписки

Прежде чем использовать API распознавания лиц, необходимо зарегистрироваться для получения подписки на этот API на портале Microsoft Cognitive Services. Ознакомьтесь со страницей [подписок](https://azure.microsoft.com/try/cognitive-services/). В этом руководстве можно использовать как первичный, так и вторичный ключ.

## <a name="step2"></a>Шаг 2. Создание платформы для приложения

На этом этапе вы создадите проект приложения Android для реализации простого интерфейса, в котором можно выбрать и отобразить изображение. Выполните приведенные далее инструкции. 

1. Откройте Android Studio.
2. В меню "Файл" выберите **Новый проект...**.
3. Присвойте приложению имя **MyFirstApp** и щелкните "Далее". 

    ![GettingStartAndroidNewProject](../Images/AndroidNewProject.png)

4. Выберите нужную целевую платформу и щелкните "Далее". 

    ![GettingStartAndroidNewProject2](../Images/AndroidNewProject2.png)

5. Выберите **Basic Activity** (Базовое действие) и щелкните "Далее".
6. Присвойте этому действию указанное ниже имя и щелкните "Готово". 

    ![GettingStartAndroidNewProject4](../Images/AndroidNewProject4.png)

7. Щелкните файл **activity_main.xml**, чтобы открыть это действие в редакторе макета.
8. Просмотрите исходный текстовый файл и измените макет действия следующим образом:

    ```xml
    <RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:tools="http://schemas.android.com/tools" android:layout_width="match_parent"
        android:layout_height="match_parent" android:paddingLeft="@dimen/activity_horizontal_margin"
        android:paddingRight="@dimen/activity_horizontal_margin"
        android:paddingTop="@dimen/activity_vertical_margin"
        android:paddingBottom="@dimen/activity_vertical_margin" tools:context=".MainActivity">
     
        <ImageView
            android:layout_width="match_parent"
            android:layout_height="fill_parent"
            android:id="@+id/imageView1"
            android:layout_above="@+id/button1" />
    
        <Button
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:text="Browse"
            android:id="@+id/button1"
            android:layout_alignParentBottom="true" />
    </RelativeLayout>
    ```  

9. Откройте **MainActivity.java** и вставьте в начало файла следующие директивы импорта:

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
    ```
      
    Затем измените этот класс следующим образом:  
    
    ```java
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
                Intent gallIntent = new Intent(Intent.ACTION_GET_CONTENT);
                gallIntent.setType("image/*");
                startActivityForResult(Intent.createChooser(gallIntent, "Select Picture"), PICK_IMAGE);
            }
        });
         
        detectionProgressDialog = new ProgressDialog(this);
    }
    
    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        super.onActivityResult(requestCode, resultCode, data);
        if (requestCode == PICK_IMAGE && resultCode == RESULT_OK && data != null && data.getData() != null) {
            Uri uri = data.getData();
            try {
                Bitmap bitmap = MediaStore.Images.Media.getBitmap(getContentResolver(), uri);
                ImageView imageView = (ImageView) findViewById(R.id.imageView1);
                imageView.setImageBitmap(bitmap);
                } catch (IOException e) {
                e.printStackTrace();
                }
        }
    }
    ```

Теперь приложение сможет выбирать фотографию из коллекции и отображать ее в окне, как показано на рисунке ниже:

![GettingStartAndroidUI](../Images/android_getstarted1.1.PNG)

## <a name="step3"></a>Шаг 3. Настройка клиентской библиотеки API распознавания лиц

API распознавания лиц представляет собой облачный API-интерфейс, к которому вы можете обращаться с помощью HTTPS-запросов. Кроме того, для удобства использования API распознавания лиц в приложениях платформы .NET предоставляется клиентская библиотека, позволяющая инкапсулировать веб-запросы. В этом примере для упрощения работы мы используем клиентскую библиотеку. 

Выполните следующие действия, чтобы настроить клиентскую библиотеку: 

1. Найдите в своем проекте файл **build.gradle** верхнего уровня, используя панель Project (Проект), как показано в нашем примере. Обратите внимание, что в дереве проекта есть еще несколько файлов **build.gradle**, но сейчас вам нужен только файл **build.gradle** верхнего уровня.
2. Добавьте **mavenCentral()** в список репозиториев проекта. Также можно использовать jcenter(), являющийся репозиторием по умолчанию для Android Studio, так как jcenter() является супермножеством mavenCentral().  

```
    allprojects {
        repositories {
            ...
            mavenCentral()
        }
    }
```

3. Откройте файл **build.gradle** в проекте приложения.
4. Добавьте зависимость для нужной клиентской библиотеки, которая хранится в репозитории Maven Central:

```
    dependencies {  
        ...  
        implementation 'com.microsoft.projectoxford:face:1.4.3'  
    }
```

5. Откройте **MainActivity.java** в проекте приложения и вставьте в него следующие директивы импорта: 
    
    ```java
    import com.microsoft.projectoxford.face.*;  
    import com.microsoft.projectoxford.face.contract.*;  
    ```
    
   Теперь вставьте в класс следующий код:

    ```java
    private FaceServiceClient faceServiceClient = new FaceServiceRestClient("your API endpoint", "<Subscription Key>");
    ```

   В приведенном выше фрагменте кода замените первый параметр адресом конечной точки API, который назначен для вашего ключа на шаге 1. Например: 
   
        https://eastus2.api.cognitive.microsoft.com/face/v1.0
   
   Замените второй параметр ключом подписки, который вы получили на шаге 1.
   
6. Откройте файл **AndroidManifest.xml** в проекте приложения. Добавьте следующий элемент в качестве дочернего элемента в элемент **manifest**:  

    ```xml
    <uses-permission android:name="android.permission.INTERNET" />  
    ```

7. Теперь все готово для вызова API распознавания лиц из приложения. 

## <a name="step4"></a>Шаг 4. Отправка изображений для обнаружения лиц

Самый простой способ обнаружения лиц — вызвать функцию [обнаружения API распознавания лиц](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), отправив файл изображения напрямую. При использовании клиентской библиотеки это можно сделать с помощью асинхронного метода **DetectAsync** из класса **FaceServiceClient**. Каждое возвращенное лицо выделяется прямоугольником, указывающим его расположение, и дополняется рядом необязательных атрибутов лица. В этом примере нам достаточно узнать расположение лица. Для этого мы добавим новый метод в класс **MainActivity** для обнаружения лиц: 

```java

    // Detect faces by uploading face images
    // Frame faces after detection
    
    private void detectAndFrame(final Bitmap imageBitmap)
    {
        ByteArrayOutputStream outputStream = new ByteArrayOutputStream();
        imageBitmap.compress(Bitmap.CompressFormat.JPEG, 100, outputStream);
        ByteArrayInputStream inputStream = 
            new ByteArrayInputStream(outputStream.toByteArray());
        AsyncTask<InputStream, String, Face[]> detectTask =
            new AsyncTask<InputStream, String, Face[]>() {
                @Override
                protected Face[] doInBackground(InputStream... params) {
                    try {
                        publishProgress("Detecting...");
                        Face[] result = faceServiceClient.detect(
                                params[0], 
                                true,         // returnFaceId
                                false,        // returnFaceLandmarks
                                null           // returnFaceAttributes: a string like "age, gender"
                /* If you want value of FaceAttributes, try adding 4th argument like below.
                            new FaceServiceClient.FaceAttributeType[] {
                    FaceServiceClient.FaceAttributeType.Age,
                    FaceServiceClient.FaceAttributeType.Gender }
                */              
                        );
                        if (result == null)
                        {
                            publishProgress("Detection Finished. Nothing detected");
                            return null;
                        }
                        publishProgress(
                                String.format("Detection Finished. %d face(s) detected",
                                        result.length));
                        return result;
                    } catch (Exception e) {
                        publishProgress("Detection failed");
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
```

## <a name="step5"></a>Шаг 5. Выделение лиц на изображении

На последнем этапе мы объединим все перечисленные выше шаги и отметим обнаруженные лица рамками на изображении. Сначала откройте **MainActivity.java** и вставьте в него вспомогательный метод для рисования прямоугольников: 

```java
    private static Bitmap drawFaceRectanglesOnBitmap(Bitmap originalBitmap, Face[] faces) {
        Bitmap bitmap = originalBitmap.copy(Bitmap.Config.ARGB_8888, true);
        Canvas canvas = new Canvas(bitmap);
        Paint paint = new Paint();
        paint.setAntiAlias(true);
        paint.setStyle(Paint.Style.STROKE);
        paint.setColor(Color.RED);
        int stokeWidth = 2;
        paint.setStrokeWidth(stokeWidth);
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

Теперь завершите элементы TODO в методе **detectAndFrame**, чтобы выделять лица и получать информацию о состоянии.

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
        if (result == null) return;
        ImageView imageView = (ImageView)findViewById(R.id.imageView1);
        imageView.setImageBitmap(drawFaceRectanglesOnBitmap(imageBitmap, result));
        imageBitmap.recycle();
    }
```
 
И наконец, добавьте вызов метода **detectAndFrame** из метода **onActivityResult**, как показано ниже. 

```java
    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        super.onActivityResult(requestCode, resultCode, data);
        if (requestCode == PICK_IMAGE && resultCode == RESULT_OK && data != null && data.getData() != null) {
            Uri uri = data.getData();
            try {
                Bitmap bitmap = MediaStore.Images.Media.getBitmap(getContentResolver(), uri);
                ImageView imageView = (ImageView) findViewById(R.id.imageView1);
                imageView.setImageBitmap(bitmap);
     
                // This is the new addition.
                // detectAndFrame(bitmap);
     
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
```

Запустите приложение и найдите изображение, содержащее лицо. На получение ответа от облачного API потребуется несколько секунд. После этого вы получите примерно такой результат: 

![GettingStartAndroid](../Images/android_getstarted2.1.PNG)

## <a name="summary"></a> Сводка

С помощью этого руководства вы изучили базовый процесс использования API распознавания лиц и создали приложение для отображения меток лиц на изображениях. Дополнительные сведения об API распознавания лиц вы можете найти в практическом руководстве и в [разделе справки по API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236). 

## <a name="related"></a> Связанные руководства

- [Руководство по началу работы с API распознавания лиц в CSharp](FaceAPIinCSharpTutorial.md)
- [Руководство по началу работы с API распознавания лиц в Python](FaceAPIinPythonTutorial.md)
