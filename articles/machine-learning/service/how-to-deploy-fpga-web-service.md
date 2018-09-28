---
title: Как развернуть модель как веб-службу в FPGA с помощью Машинного обучения Azure
description: Узнайте, как развернуть веб-службу с моделью, работающей на базе FPGA, с использованием Машинного обучения Azure.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: tedway
author: tedway
ms.date: 09/24/2018
ms.openlocfilehash: ee67585a523ab96b1442d9eee3e9dfd55a758d32
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46971490"
---
# <a name="deploy-a-model-as-a-web-service-on-an-fpga-with-azure-machine-learning"></a>Как развернуть модель как веб-службу в FPGA с помощью Машинного обучения Azure

Вы можете развернуть модель как веб-службу в [матрицах FPGA](concept-accelerate-with-fpgas.md).  При использовании FPGA обеспечивается сверхнизкая задержка даже в случае пакетов одного размера.   

## <a name="prerequisites"></a>Предварительные требования

- Подписка Azure. Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начать работу.

- Должны быть установлены рабочая область машинного обучения Azure и пакет SDK машинного обучения Azure для Python. Дополнительные сведения о получении этих необходимых компонентов см. в документе [Настройка среды разработки](how-to-configure-environment.md).
 
  - Ваша рабочая область должна находиться в регионе *Восточная часть США 2*.

  - Установите дополнительные компоненты.

    ```shell
    pip install --upgrade azureml-sdk[contrib]
    ```  

## <a name="create-and-deploy-your-model"></a>Создание и развертывание модели
Создайте конвейер для предварительной обработки входного изображения, определите для него признаки, используя модель ResNet 50 в FPGA, а затем пропустите признаки через классификатор, обученный с помощью набора данных ImageNet.

Следуйте инструкциям по выполнению следующих процедур:

* Определение конвейера модели
* Развертывание модели
* Использовать развернутую модель.
* Удалить развернутые службы.

> [!IMPORTANT]
> Чтобы оптимизировать задержку и пропускную способность, ваш клиент должен находиться в том же регионе Azure, что и конечная точка.  В настоящее время API создаются в регионе Azure "Восточная часть США".

### <a name="get-the-notebook"></a>Получение записной книжки

Для удобства это руководство доступно в виде Jupyter Notebook. Используйте любой из приведенных далее методов для запуска записной книжки `project-brainwave/project-brainwave-quickstart.ipynb`.

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-in-azure-notebook.md)]

### <a name="preprocess-image"></a>Предварительная обработка изображения
Первый этап конвейера — предварительная обработка изображений.

```python
import os
import tensorflow as tf

# Input images as a two-dimensional tensor containing an arbitrary number of images represented a strings
import azureml.contrib.brainwave.models.utils as utils
in_images = tf.placeholder(tf.string)
image_tensors = utils.preprocess_array(in_images)
print(image_tensors.shape)
```
### <a name="add-featurizer"></a>Добавление характеризатора
Инициализируйте модель и загрузите контрольную точку TensorFlow квантованной версии ResNet50 для использования в качестве характеризатора.

```python
from azureml.contrib.brainwave.models import QuantizedResnet50, Resnet50
model_path = os.path.expanduser('~/models')
model = QuantizedResnet50(model_path, is_frozen = True)
feature_tensor = model.import_graph_def(image_tensors)
print(model.version)
print(feature_tensor.name)
print(feature_tensor.shape)
```

### <a name="add-classifier"></a>Добавление классификатора
Этот классификатор обучен с помощью набора данных ImageNet.

```python
classifier_input, classifier_output = Resnet50.get_default_classifier(feature_tensor, model_path)
```

### <a name="create-service-definition"></a>Создание определения службы
Определив предварительную обработку изображений, характеризатор и классификатор, который выполняется в службе, можно создать определение службы. Определение службы — это набор файлов, созданных на основе модели, которая развертывается в службе FPGA. Определение службы состоит из конвейера. Конвейер — это последовательность этапов, которые выполняются по порядку.  Поддерживаются этапы TensorFlow, Keras и BrainWave.  Этапы выполняются в службе по порядку: выходные данные каждого этапа используются как входные данные для следующего этапа.

Для создания этапа TensorFlow укажите сеанс, содержащий граф (в данном случае используется граф по умолчанию), и входные и выходные тензоры для этого этапа.  Эти данные используются для сохранения графа, чтобы его можно было выполнить в службе.

```python
from azureml.contrib.brainwave.pipeline import ModelDefinition, TensorflowStage, BrainWaveStage

save_path = os.path.expanduser('~/models/save')
model_def_path = os.path.join(save_path, 'service_def.zip')

model_def = ModelDefinition()
with tf.Session() as sess:
    model_def.pipeline.append(TensorflowStage(sess, in_images, image_tensors))
    model_def.pipeline.append(BrainWaveStage(sess, model))
    model_def.pipeline.append(TensorflowStage(sess, classifier_input, classifier_output))
    model_def.save(model_def_path)
    print(model_def_path)
```

### <a name="deploy-model"></a>Развертывание модели
Создайте службу на основе определения службы.  Ваша рабочая область должна находиться в регионе "Восточная часть США 2".

```python
from azureml.core import Workspace

ws = Workspace.from_config()
print(ws.name, ws.resource_group, ws.location, ws.subscription_id, sep = '\n')

from azureml.core.model import Model
model_name = "resnet-50-rtai"
registered_model = Model.register(ws, model_def_path, model_name)

from azureml.core.webservice import Webservice
from azureml.exceptions import WebserviceException
from azureml.contrib.brainwave import BrainwaveWebservice, BrainwaveImage
service_name = "imagenet-infer"
service = None
try:
    service = Webservice(ws, service_name)
except WebserviceException:
    image_config = BrainwaveImage.image_configuration()
    deployment_config = BrainwaveWebservice.deploy_configuration()
    service = Webservice.deploy_from_model(ws, service_name, [registered_model], image_config, deployment_config)
    service.wait_for_deployment(true)
```

### <a name="test-the-service"></a>Тестирование службы
Чтобы отправить изображение в API и проверить ответ, добавьте сопоставление из идентификатора выходного класса в имя класса ImageNet.

```python
import requests
classes_entries = requests.get("https://raw.githubusercontent.com/Lasagne/Recipes/master/examples/resnet50/imagenet_classes.txt").text.splitlines()
```

Вызовите службу и замените имя файла your-image.jpg ниже изображением с компьютера. 

```python
with open('your-image.jpg') as f:
    results = service.run(f)
# map results [class_id] => [confidence]
results = enumerate(results)
# sort results by confidence
sorted_results = sorted(results, key=lambda x: x[1], reverse=True)
# print top 5 results
for top in sorted_results[:5]:
    print(classes_entries[top[0]], 'confidence:', top[1])
``` 

### <a name="clean-up-service"></a>Удаление службы
Удалите службу.

```python
service.delete()
    
registered_model.delete()
```

## <a name="secure-fpga-web-services"></a>Защита веб-служб FPGA

Модели машинного обучения Azure, выполняющиеся в FPGA, обеспечивают поддержку SSL и аутентификацию на основе ключей. Это позволит вам ограничивать доступ к вашей службе и защищать данные, предоставляемые клиентами.

> [!IMPORTANT]
> Аутентификация разрешена только для служб, которые предоставили SSL-сертификат и ключ. 
>
> Если вы не включите SSL, любой пользователь в Интернете сможет совершать вызовы в службу.
>
> Если вы включите SSL, при доступе к службе потребуется ключ аутентификации.

SSL шифрует данные, отправляемые между клиентом и службой. Он также используется клиентом для проверки удостоверения сервера.

Вы можете развернуть службу с включенным протоколом SSL или обновить уже развернутую службу, чтобы включить его. Выполняемые действия идентичны.

1. Получите имя домена.

2. Получите SSL-сертификат.

3. Разверните или обновите службу с включенным протоколом SSL.

4. Обновите DNS, чтобы указать службу.

### <a name="acquire-a-domain-name"></a>Получите имя домена.

Если у вас еще нет имени домена, вы можете приобрести его у __регистратора доменных имен__. Процесс различается у разных регистраторов, как и стоимость. Регистратор также предоставляет вам инструменты для управления доменным именем. Эти инструменты используются для сопоставления полного доменного имени (например, www.contoso.com) с IP-адресом, по которому размещается ваша служба.

### <a name="acquire-an-ssl-certificate"></a>Получение SSL-сертификата

Существует множество способов получить SSL-сертификат. Наиболее распространенным является покупка в одном из __центров сертификации__. Независимо от того, где вы получаете сертификат, вам нужны следующие файлы:

* __Сертификат__. Сертификат должен содержать полную цепочку сертификатов и должен быть в кодировке PEM.
* __Ключ__. Ключ должен быть в кодировке PEM.

> [!TIP]
> Если центр сертификации не может предоставить сертификат и ключ в виде файлов в кодировке PEM, вы можете использовать такую служебную программу, как [OpenSSL](https://www.openssl.org/), чтобы изменить формат.

> [!IMPORTANT]
> Эти самозаверяющие сертификаты следует использовать только для разработки. Они не должны использоваться в рабочей среде.
>
> Если вы используете самозаверяющий сертификат, см. инструкции в разделе [Использование служб с помощью самозаверяющих сертификатов](#self-signed).

> [!WARNING]
> При запросе сертификата вы должны предоставить полное доменное имя (FQDN) адреса, который вы планируете использовать для этой службы. Например, www.contoso.com. Адрес, указанный в сертификате, и адрес, используемый клиентами, сравниваются при проверке удостоверения службы.
>
> Если адреса не совпадают, клиенты получат сообщение об ошибке. 

### <a name="deploy-or-update-the-service-with-ssl-enabled"></a>Развертывание или обновление службы с включенным протоколом SSL

Чтобы развернуть службу с включенным SSL, установите для параметра `ssl_enabled` значение `True`. Установите для параметра `ssl_certificate` значение файла __сертификата__, а для параметра `ssl_key` значение __ключа__. В следующем примере показано развертывание службы с включенным SSL:

```python
from amlrealtimeai import DeploymentClient

subscription_id = "<Your Azure Subscription ID>"
resource_group = "<Your Azure Resource Group Name>"
model_management_account = "<Your AzureML Model Management Account Name>"
location = "eastus2"

model_name = "resnet50-model"
service_name = "quickstart-service"

deployment_client = DeploymentClient(subscription_id, resource_group, model_management_account, location)

with open('cert.pem','r') as cert_file:
    with open('key.pem','r') as key_file:
        cert = cert_file.read()
        key = key_file.read()
        service = deployment_client.create_service(service_name, model_id, ssl_enabled=True, ssl_certificate=cert, ssl_key=key)
```

Ответ операции `create_service` содержит IP-адрес службы. IP-адрес используется при сопоставлении имени DNS с IP-адресом службы.

Ответ также содержит __первичный__ и __вторичный ключ__, используемые для применения службы.

### <a name="update-your-dns-to-point-to-the-service"></a>Обновите DNS, чтобы указать службу.

Используйте инструменты, предоставленные вашим регистратором доменных имен, чтобы обновить запись DNS для вашего доменного имени. Запись должна указывать на IP-адрес службы.

> [!NOTE]
> В зависимости от регистратора и срока жизни, настроенного для имени домена, может потребоваться от нескольких минут до нескольких часов, прежде чем клиенты смогут разрешить имя домена.

### <a name="consume-authenticated-services"></a>Использование аутентифицированных служб

В следующих примерах показано, как использовать аутентифицированную службу с помощью Python и C #:

> [!NOTE]
> Замените `authkey` первичным или вторичным ключом, возвращаемым при создании службы.

```python
from amlrealtimeai import PredictionClient
client = PredictionClient(service.ipAddress, service.port, use_ssl=True, access_token="authKey")
image_file = R'C:\path_to_file\image.jpg'
results = client.score_image(image_file)
```

```csharp
var client = new ScoringClient(host, 50051, useSSL, "authKey");
float[,] result;
using (var content = File.OpenRead(image))
    {
        IScoringRequest request = new ImageRequest(content);
        result = client.Score<float[,]>(request);
    }
```

Другие клиенты gRPC могут аутентифицировать запросы, установив заголовок авторизации. Общий подход заключается в создании объекта `ChannelCredentials`, который объединяет `SslCredentials` с `CallCredentials`. Это добавляется в заголовок авторизации запроса. Дополнительные сведения о реализации поддержки конкретных заголовков см. на странице [https://grpc.io/docs/guides/auth.html](https://grpc.io/docs/guides/auth.html).

В следующих примерах показано, как установить заголовок для C# и Go:

```csharp
creds = ChannelCredentials.Create(baseCreds, CallCredentials.FromInterceptor(
                      async (context, metadata) =>
                      {
                          metadata.Add(new Metadata.Entry("authorization", "authKey"));
                          await Task.CompletedTask;
                      }));

```

```go
conn, err := grpc.Dial(serverAddr, 
    grpc.WithTransportCredentials(credentials.NewClientTLSFromCert(nil, "")),
    grpc.WithPerRPCCredentials(&authCreds{
    Key: "authKey"}))

type authCreds struct {
    Key string
}

func (c *authCreds) GetRequestMetadata(context.Context, uri ...string) (map[string]string, error) {
    return map[string]string{
        "authorization": c.Key,
    }, nil
}

func (c *authCreds) RequireTransportSecurity() bool {
    return true
}
```

### <a id="self-signed"></a>Использование служб с помощью самозаверяющих сертификатов

Существует два способа, позволяющих клиенту выполнить аутентификацию на сервере, защищенном с использованием самозаверяющего сертификата:

* В системе клиента установите переменную среды `GRPC_DEFAULT_SSL_ROOTS_FILE_PATH`, чтобы указать файл сертификата.

* При создании объекта `SslCredentials` передайте содержимое файла сертификата в конструктор.

Использование любого из методов приведет к тому, что gRPC будет использовать сертификат в качестве корневого сертификата.

> [!IMPORTANT]
> gRPC не принимает ненадежные сертификаты. Использование ненадежного сертификата завершится ошибкой с кодом состояния `Unavailable`. Подробные сведения об ошибке будут содержать сообщение `Connection Failed`.
