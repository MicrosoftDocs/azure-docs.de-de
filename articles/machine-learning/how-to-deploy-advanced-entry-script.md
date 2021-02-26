---
title: Erstellen eines Eingabeskripts für erweiterte Szenarien
titleSuffix: Azure Machine Learning entry script authoring
description: Erfahren Sie, wie Sie Azure Machine Learning-Eingabeskripts für die Vor- und Nachverarbeitung während der Bereitstellung erstellen.
author: gvashishtha
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 09/17/2020
ms.author: gopalv
ms.reviewer: larryfr
ms.custom: deploy
ms.openlocfilehash: de0a62ead6e1f40755daa068bf779b5391cf66a0
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100371973"
---
# <a name="advanced-entry-script-authoring"></a>Erweiterte Eingabeskripterstellung

In diesem Artikel wird gezeigt, wie Sie Eingabeskripts für spezialisierte Anwendungsfälle schreiben.

## <a name="prerequisites"></a>Voraussetzungen

Dieser Artikel setzt voraus, dass Sie bereits über ein trainiertes Machine Learning-Modell verfügen, das Sie mit Azure Machine Learning bereitstellen möchten. Weitere Informationen zur Modellimplementierung finden Sie in [diesem Tutorial](how-to-deploy-and-where.md).

## <a name="automatically-generate-a-swagger-schema"></a>Automatisches Generieren eines Swagger-Schemas

Um automatisch ein Schema für Ihren Webdienst zu generieren, stellen Sie ein Beispiel der Eingabe und/oder Ausgabe im Konstruktor für eines der definierten Typobjekte bereit. Der Typ und das Beispiel werden verwendet, um das Schema automatisch zu erstellen. Azure Machine Learning erstellt dann während der Bereitstellung eine [OpenAPI](https://swagger.io/docs/specification/about/)-Spezifikation (Swagger) für den Webdienst. 

> [!WARNING]
> Sie dürfen keine vertraulichen oder privaten Daten für die Beispielein- oder -ausgabe verwenden. Die Beispieldaten werden auf der Swagger-Seite für von AML gehostete Rückschlüsse verfügbar gemacht. 

Diese Typen werden derzeit unterstützt:

* `pandas`
* `numpy`
* `pyspark`
* Python-Standardobjekt

Um die Schemagenerierung zu verwenden, schließen Sie das Open-Source-Paket `inference-schema` (Version 1.1.0 oder höher) in Ihre Abhängigkeitsdatei ein. Weitere Informationen zu diesem Paket finden Sie unter [https://github.com/Azure/InferenceSchema](https://github.com/Azure/InferenceSchema). Um Swagger für die automatisierte Webdienstnutzung anzupassen, muss die API für die Funktion „run()“ des Bewertungsskripts die folgende Form haben:
* Ein erster Parameter vom Typ StandardPythonParameterType mit dem Namen **Inputs** und geschachtelt.
* Ein optionaler zweiter Parameter vom Typ StandardPythonParameterType mit dem Namen **GlobalParameter**.
* Rückgabe eines Verzeichnisses vom Typ StandardPythonParameterType mit dem Namen **Inputs** und geschachtelt.

Definieren Sie in den Variablen `input_sample` und `output_sample` das Format für das Ein- und Ausgabebeispiel. (Die Variablen stellen das Anforderungs- und das Antwortformat für den Webdienst dar.) Verwenden Sie diese Beispiele in den Decorator-Elementen der Ein- und Ausgabefunktion für die Funktion `run()`. Im folgenden scikit-learn-Beispiel wird Schemagenerierung verwendet.



## <a name="power-bi-compatible-endpoint"></a>Power BI-kompatibler Endpunkt 

Das folgende Beispiel veranschaulicht, wie Sie die API gemäß der obigen Anleitung definieren. Diese Methode wird dazu unterstützt, den bereitgestellten Webdienst aus Power BI zu nutzen. ([Erfahren Sie mehr darüber, wie der Webdienst aus Power BI genutzt werden kann](/power-bi/service-machine-learning-integration).)

```python
import json
import pickle
import numpy as np
import pandas as pd
import azureml.train.automl
from sklearn.externals import joblib
from sklearn.linear_model import Ridge

from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.standard_py_parameter_type import StandardPythonParameterType
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType
from inference_schema.parameter_types.pandas_parameter_type import PandasParameterType


def init():
    global model
    # Replace filename if needed.
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_regression_model.pkl')
    # Deserialize the model file back into a sklearn model.
    model = joblib.load(model_path)


    # providing 3 sample inputs for schema generation
    numpy_sample_input = NumpyParameterType(np.array([[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]],dtype='float64'))
    pandas_sample_input = PandasParameterType(pd.DataFrame({'name': ['Sarah', 'John'], 'age': [25, 26]}))
    standard_sample_input = StandardPythonParameterType(0.0)

    # This is a nested input sample, any item wrapped by `ParameterType` will be described by schema
    sample_input = StandardPythonParameterType({'input1': numpy_sample_input, 
                                            'input2': pandas_sample_input, 
                                            'input3': standard_sample_input})

    sample_global_parameters = StandardPythonParameterType(1.0) # this is optional
    sample_output = StandardPythonParameterType([1.0, 1.0])
    outputs = StandardPythonParameterType({'Results':sample_output}) # 'Results' is case sensitive

    @input_schema('Inputs', sample_input) 
    # 'Inputs' is case sensitive
    
    @input_schema('GlobalParameters', sample_global_parameters) 
    # this is optional, 'GlobalParameters' is case sensitive

    @output_schema(outputs)

def run(Inputs, GlobalParameters): 
    # the parameters here have to match those in decorator, both 'Inputs' and 
    # 'GlobalParameters' here are case sensitive
    try:
        data = Inputs['input1']
        # data will be convert to target format
        assert isinstance(data, np.ndarray)
        result = model.predict(data)
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```


## <a name="binary-ie-image-data"></a><a id="binary-data"></a> Binäre Daten (z. B. Bilder)

Wenn Ihr Modell Binärdaten (beispielsweise ein Bild) akzeptiert, müssen Sie die für Ihre Bereitstellung verwendete Datei `score.py` so ändern, dass sie HTTP-Rohanforderungen akzeptiert. Um Rohdaten zu akzeptieren, verwenden Sie die `AMLRequest`-Klasse in Ihrem Eingangsskript und fügen der `run()`-Funktion den `@rawhttp`-Decorator hinzu.

Hier ist ein Beispiel für eine Datei `score.py`, die Binärdaten akzeptiert:

```python
from azureml.contrib.services.aml_request import AMLRequest, rawhttp
from azureml.contrib.services.aml_response import AMLResponse
from PIL import Image
import json


def init():
    print("This is init()")
    

@rawhttp
def run(request):
    print("This is run()")
    
    if request.method == 'GET':
        # For this example, just return the URL for GETs.
        respBody = str.encode(request.full_path)
        return AMLResponse(respBody, 200)
    elif request.method == 'POST':
        file_bytes = request.files["image"]
        image = Image.open(file_bytes).convert('RGB')
        # For a real-world solution, you would load the data from reqBody
        # and send it to the model. Then return the response.

        # For demonstration purposes, this example just returns the size of the image as the response..
        return AMLResponse(json.dumps(image.size), 200)
    else:
        return AMLResponse("bad request", 500)
```


> [!IMPORTANT]
> Die `AMLRequest`-Klasse befindet sich im `azureml.contrib`-Namespace. Elemente in diesem Namespace ändern sich häufig, während wir daran arbeiten, den Dienst zu verbessern. Alles in diesem Namespace sollte als Vorschau betrachtet werden, die von Microsoft nicht vollständig unterstützt wird.
>
> Wenn Sie dies in Ihrer lokalen Entwicklungsumgebung testen müssen, können Sie die Komponenten mit dem folgenden Befehl installieren:
>
> ```shell
> pip install azureml-contrib-services
> ```

Die Klasse `AMLRequest` ermöglicht Ihnen nur den Zugriff auf die unformatiert bereitgestellten Daten in der „score.py“, es gibt keine clientseitige Komponente. Von einem Client aus werden die Daten ganz normal bereitgestellt. Der folgende Python-Code liest z. B. eine Imagedatei und stellt die Daten bereit:

```python
import requests

uri = service.scoring_uri
image_path = 'test.jpg'
files = {'image': open(image_path, 'rb').read()}
response = requests.post(url, files=files)

print(response.json)
```

<a id="cors"></a>

## <a name="cross-origin-resource-sharing-cors"></a>Ressourcenfreigabe zwischen verschiedenen Ursprüngen (Cross-Origin Resource Sharing, CORS)

Ressourcenfreigabe zwischen verschiedenen Ursprüngen ist eine Möglichkeit, um die Anforderung eingeschränkter Ressourcen auf einer Webseite aus anderen Domänen zuzulassen. CORS funktioniert über HTTP-Header, die mit der Clientanforderung gesendet und mit der Dienstantwort zurückgegeben werden. Weitere Informationen zu CORS und gültigen Headern finden Sie in Wikipedia unter [Cross-origin resource sharing](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing).

Um Ihre Modellimplementierung für die Unterstützung von CORS zu konfigurieren, verwenden Sie die `AMLResponse`-Klasse in Ihrem Eingangsskript. Diese Klasse ermöglicht es Ihnen, die Header für das Antwortobjekt festzulegen.

Im folgenden Beispiel wird der `Access-Control-Allow-Origin`-Header für die Antwort aus dem Eingangsskript festgelegt:

```python
from azureml.contrib.services.aml_request import AMLRequest, rawhttp
from azureml.contrib.services.aml_response import AMLResponse


def init():
    print("This is init()")

@rawhttp
def run(request):
    print("This is run()")
    print("Request: [{0}]".format(request))
    if request.method == 'GET':
        # For this example, just return the URL for GETs.
        respBody = str.encode(request.full_path)
        return AMLResponse(respBody, 200)
    elif request.method == 'POST':
        reqBody = request.get_data(False)
        # For a real-world solution, you would load the data from reqBody
        # and send it to the model. Then return the response.

        # For demonstration purposes, this example
        # adds a header and returns the request body.
        resp = AMLResponse(reqBody, 200)
        resp.headers['Access-Control-Allow-Origin'] = "http://www.example.com"
        return resp
    else:
        return AMLResponse("bad request", 500)
```

> [!IMPORTANT]
> Die `AMLResponse`-Klasse befindet sich im `azureml.contrib`-Namespace. Elemente in diesem Namespace ändern sich häufig, während wir daran arbeiten, den Dienst zu verbessern. Alles in diesem Namespace sollte als Vorschau betrachtet werden, die von Microsoft nicht vollständig unterstützt wird.
>
> Wenn Sie dies in Ihrer lokalen Entwicklungsumgebung testen müssen, können Sie die Komponenten mit dem folgenden Befehl installieren:
>
> ```shell
> pip install azureml-contrib-services
> ```


> [!WARNING]
> Azure Machine Learning leitet nur POST- und GET-Anforderungen an die Container weiter, die den Bewertungsdienst ausführen. Dies kann zu Fehlern durch Browser führen, die OPTIONS-Anforderungen für CORS-Preflightanforderungen verwenden.
> 


## <a name="load-registered-models"></a>Laden von registrierten Modellen

Es gibt zwei Möglichkeiten, in einem Eingabeskript nach Modellen zu suchen:
* `AZUREML_MODEL_DIR`: Eine Umgebungsvariable, die den Pfad zu dem Speicherort des Modells enthält
* `Model.get_model_path`: Eine API, die den registrierten Modellnamen verwendet, um den Pfad zur Modelldatei zurückzugeben

#### <a name="azureml_model_dir"></a>AZUREML_MODEL_DIR

AZUREML_MODEL_DIR ist eine Umgebungsvariable, die während der Dienstbereitstellung erstellt wird. Sie können diese Umgebungsvariable verwenden, um den Speicherort der bereitgestellten Modelle zu ermitteln.

In der folgenden Tabelle ist der Wert von AZUREML_MODEL_DIR in Abhängigkeit von der Anzahl der bereitgestellten Modelle beschrieben:

| Bereitstellung | Wert der Umgebungsvariablen |
| ----- | ----- |
| Einzelnes Modell | Der Pfad zu dem Ordner, der das Modell enthält |
| Mehrere Modelle | Der Pfad zu dem Ordner, der alle Modelle enthält. Die Modelle befinden sich nach Name und Version in diesem Ordner (`$MODEL_NAME/$VERSION`). |

Während der Modellregistrierung und -bereitstellung werden Modelle in den Pfad „AZUREML_MODEL_DIR“ eingefügt, und die ursprünglichen Dateinamen werden beibehalten.

Um den Pfad zu einer Modelldatei in Ihrem Eingabeskript abzurufen, kombinieren Sie die Umgebungsvariable mit dem Dateipfad, nach dem Sie suchen.

**Beispiel für ein einzelnes Modell**
```python
# Example when the model is a file
model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_regression_model.pkl')

# Example when the model is a folder containing a file
file_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'my_model_folder', 'sklearn_regression_model.pkl')
```

**Beispiel für mehrere Modelle**

In diesem Szenario werden zwei Modelle unter dem Arbeitsbereich registriert:

* `my_first_model`: Enthält eine Datei (`my_first_model.pkl`), und es gibt nur eine Version (`1`).
* `my_second_model`: Enthält eine Datei (`my_second_model.pkl`), und es gibt die zwei Versionen `1` und `2`.

Wenn der Dienst bereitgestellt wurde, werden beide Modelle im Bereitstellungsvorgang verfügbar gemacht:

```python
first_model = Model(ws, name="my_first_model", version=1)
second_model = Model(ws, name="my_second_model", version=2)
service = Model.deploy(ws, "myservice", [first_model, second_model], inference_config, deployment_config)
```

In dem Docker-Image, das den Dienst hostet, enthält die `AZUREML_MODEL_DIR`-Umgebungsvariable das Verzeichnis, in dem sich die Modelle befinden.
In diesem Verzeichnis befindet sich jedes Modell im Verzeichnispfad von `MODEL_NAME/VERSION`. Dabei ist `MODEL_NAME` der Name des registrierten Modells, und `VERSION` ist die Version des Modells. Die Dateien, aus denen das registrierte Modell besteht, werden in diesen Verzeichnissen gespeichert.

In diesem Beispiel lauten die Pfade `$AZUREML_MODEL_DIR/my_first_model/1/my_first_model.pkl` und `$AZUREML_MODEL_DIR/my_second_model/2/my_second_model.pkl`.


```python
# Example when the model is a file, and the deployment contains multiple models
first_model_name = 'my_first_model'
first_model_version = '1'
first_model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), first_model_name, first_model_version, 'my_first_model.pkl')
second_model_name = 'my_second_model'
second_model_version = '2'
second_model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), second_model_name, second_model_version, 'my_second_model.pkl')
```

### <a name="get_model_path"></a>get_model_path

Wenn Sie ein Modell registrieren, geben Sie einen Modellnamen ein. Dieser wird dazu verwendet, das Modell in der Registrierung zu verwalten. Sie verwenden diesen Namen mit der [Model.get_model_path()](/python/api/azureml-core/azureml.core.model.model?preserve-view=true&view=azure-ml-py#&preserve-view=trueget-model-path-model-name--version-none---workspace-none-)-Methode, um den Pfad der Modelldatei(en) im lokalen Dateisystem abzurufen. Wenn Sie einen Ordner oder eine Sammlung von Dateien registrieren, gibt diese API den Pfad des Verzeichnisses zurück, das diese Dateien enthält.

Wenn Sie ein Modell registrieren, geben Sie ihm einen Namen. Der Name entspricht dem Ort, an dem sich das Modell befindet (entweder lokal oder während der Dienstbereitstellung).

## <a name="framework-specific-examples"></a>Frameworkspezifische Beispiele

Weitere Beispiele für Eingabeskripts für bestimmte Machine Learning-Anwendungsfälle finden Sie unten:

* [PyTorch](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/pytorch)
* [TensorFlow](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/tensorflow)
* [Keras](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/keras/train-hyperparameter-tune-deploy-with-keras/train-hyperparameter-tune-deploy-with-keras.ipynb)
* [AutoML](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features)
* [ONNX](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx/)

## <a name="next-steps"></a>Nächste Schritte

* [Problembehandlung bei einer fehlerhaften Bereitstellung](how-to-troubleshoot-deployment.md)
* [Bereitstellen für Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md)
* [Erstellen von Clientanwendungen zum Nutzen von Webdiensten](how-to-consume-web-service.md)
* [Aktualisieren des Webdiensts](how-to-deploy-update-web-service.md)
* [Wie man ein Modell mit einem benutzerdefinierten Docker-Image bereitstellt](how-to-deploy-custom-docker-image.md)
* [Verwenden von TLS zum Absichern eines Webdiensts mit Azure Machine Learning](how-to-secure-web-service.md)
* [Überwachen Ihrer Azure Machine Learning-Modelle mit Application Insights](how-to-enable-app-insights.md)
* [Sammeln von Daten für Modelle in der Produktion](how-to-enable-data-collection.md)
* [Erstellen von Ereigniswarnungen und Triggern für Modellbereitstellungen](how-to-use-event-grid.md)