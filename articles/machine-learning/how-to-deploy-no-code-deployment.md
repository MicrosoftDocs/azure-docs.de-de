---
title: Bereitstellung ohne Code (Vorschauversion)
titleSuffix: Azure Machine Learning
description: Dank einer Bereitstellung ohne Code können Sie ein Modell als Webdienst bereitstellen, ohne dass Sie ein Eingabeskript manuell erstellen müssen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.date: 07/31/2020
ms.topic: conceptual
ms.custom: deploy
ms.reviewer: larryfr
ms.openlocfilehash: a17126695aa5138d1df7fd17cfaa2f5f75ad1004
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93324922"
---
# <a name="preview-no-code-model-deployment"></a>(Vorschau) Modellimplementierung ohne Code

Die Modellimplementierung ohne Code ist derzeit in der Vorschauphase und unterstützt die folgenden Frameworks für maschinelles Lernen:

## <a name="tensorflow-savedmodel-format"></a>TensorFlow SavedModel-Format
TensorFlow-Modelle müssen im **SavedModel-Format** registriert werden, um mit der Modellimplementierung ohne Code zu funktionieren.

Informationen zum Erstellen von „SavedModel“ finden Sie unter [diesem Link](https://www.tensorflow.org/guide/saved_model).

Wir unterstützen jede TensorFlow Version, die unter „Tags“ im [TensorFlow Serving DockerHub](https://registry.hub.docker.com/r/tensorflow/serving/tags) aufgelistet ist.

```python
from azureml.core import Model

model = Model.register(workspace=ws,
                       model_name='flowers',                        # Name of the registered model in your workspace.
                       model_path='./flowers_model',                # Local Tensorflow SavedModel folder to upload and register as a model.
                       model_framework=Model.Framework.TENSORFLOW,  # Framework used to create the model.
                       model_framework_version='1.14.0',            # Version of Tensorflow used to create the model.
                       description='Flowers model')

service_name = 'tensorflow-flower-service'
service = Model.deploy(ws, service_name, [model])
```

## <a name="onnx-models"></a>ONNX-Modelle

Die Registrierung und Bereitstellung von ONNX-Modellen wird für alle ONNX-Rückschlussgraphen unterstützt. Schritte zur Vor- und Nachverarbeitung werden derzeit nicht unterstützt.

Nachfolgend sehen Sie ein Beispiel der Registrierung und Implementierung eines MNIST ONNX-Modells:

```python
from azureml.core import Model

model = Model.register(workspace=ws,
                       model_name='mnist-sample',                  # Name of the registered model in your workspace.
                       model_path='mnist-model.onnx',              # Local ONNX model to upload and register as a model.
                       model_framework=Model.Framework.ONNX ,      # Framework used to create the model.
                       model_framework_version='1.3',              # Version of ONNX used to create the model.
                       description='Onnx MNIST model')

service_name = 'onnx-mnist-service'
service = Model.deploy(ws, service_name, [model])
```

Informationen zum Bewerten eines Modells finden Sie unter [Nutzen eines als Webdienst bereitgestellten Azure Machine Learning-Modells](./how-to-consume-web-service.md). Viele ONNX-Projekte verwenden protobuf-Dateien, um Trainings- und Validierungsdaten kompakt zu speichern. Dadurch kann es schwierig sein, das vom Dienst erwartete Datenformat zu erkennen. Als Modellentwickler sollten Sie Folgendes für Ihre Entwickler dokumentieren:

* Eingabeformat (JSON- oder Binärformat)
* Eingabedatenform und -typ (z. B. ein Array von Gleitkommazahlen in der Form [100,100,3])
* Domäneninformationen (z. B. bei einem Bild der Farbraum, die Komponentenreihenfolge und die Angabe, ob die Werte normalisiert sind)

Wenn Sie Pytorch verwenden, enthält der [Export von Modellen aus PyTorch zu ONNX](https://github.com/onnx/tutorials/blob/master/tutorials/PytorchOnnxExport.ipynb) die Details zur Konvertierung und zu den Einschränkungen. 

## <a name="scikit-learn-models"></a>Scikit-learn-Modelle

Für alle integrierten Scikit-learn-Modelltypen wird keine Modellimplementierung ohne Code unterstützt.

Nachfolgend sehen Sie ein Beispiel der Registrierung und Implementierung eines Scikit-learn-Modells ohne zusätzlichen Code:

```python
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

model = Model.register(workspace=ws,
                       model_name='my-sklearn-model',                # Name of the registered model in your workspace.
                       model_path='./sklearn_regression_model.pkl',  # Local file to upload and register as a model.
                       model_framework=Model.Framework.SCIKITLEARN,  # Framework used to create the model.
                       model_framework_version='0.19.1',             # Version of scikit-learn used to create the model.
                       resource_configuration=ResourceConfiguration(cpu=1, memory_in_gb=0.5),
                       description='Ridge regression model to predict diabetes progression.',
                       tags={'area': 'diabetes', 'type': 'regression'})
                       
service_name = 'my-sklearn-service'
service = Model.deploy(ws, service_name, [model])
```

> [!NOTE]
> Modelle, die predict_proba unterstützen, verwenden diese Methode standardmäßig. Sie können den POST-Text wie unten beschrieben ändern, um dies für die Verwendung von Vorhersagen außer Kraft zu setzen:

```python
import json


input_payload = json.dumps({
    'data': [
        [ 0.03807591,  0.05068012,  0.06169621, 0.02187235, -0.0442235,
         -0.03482076, -0.04340085, -0.00259226, 0.01990842, -0.01764613]
    ],
    'method': 'predict'  # If you have a classification model, the default behavior is to run 'predict_proba'.
})

output = service.run(input_payload)

print(output)
```

> [!NOTE]
> Diese Abhängigkeiten sind im vordefinieren Scikit-learn-Container für Rückschlüsse enthalten:

```yaml
    - dill
    - azureml-defaults
    - inference-schema[numpy-support]
    - scikit-learn
    - numpy
    - joblib
    - pandas
    - scipy
    - sklearn_pandas
```
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