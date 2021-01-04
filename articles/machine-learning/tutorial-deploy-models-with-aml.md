---
title: 'Tutorial zur Bildklassifizierung: Bereitstellen von Modellen'
titleSuffix: Azure Machine Learning
description: Dieses Tutorial zeigt, wie Sie mit Azure Machine Learning ein Bildklassifizierungsmodell mit scikit-learn in einem Python Jupyter Notebook bereitstellen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: sdgilley
ms.author: sgilley
ms.date: 03/18/2020
ms.custom: seodec18
ms.openlocfilehash: ee6529f36c302cba1dc7b5a4b53aaf96ce58e4a5
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94532782"
---
# <a name="tutorial-deploy-an-image-classification-model-in-azure-container-instances"></a>Tutorial: Bereitstellen eines Bildklassifizierungsmodells in Azure Container Instances


Dieses Tutorial ist der **zweite Teil einer zweiteiligen Reihe**. Im [vorherigen Tutorial](tutorial-train-models-with-aml.md) haben Sie Machine Learning-Modelle trainiert und anschließend ein Modell in Ihrem Arbeitsbereich in der Cloud registriert.  Nun können Sie das Modell als Webdienst bereitstellen. Ein Webdienst ist ein Image (in diesem Fall: ein Docker-Image). Es kapselt die Bewertungslogik und das eigentliche Modell. 

In diesem Teil des Tutorials verwenden Sie Azure Machine Learning für Folgendes:

> [!div class="checklist"]
> * Einrichten Ihrer Testumgebung
> * Abrufen des Modells aus Ihrem Arbeitsbereich
> * Bereitstellen eines Modells in Container Instances
> * Testen des bereitgestellten Modells

Container Instances ist eine hervorragende Lösung für das Testen und Verstehen des Workflows. Für skalierbare Produktionsbereitstellungen sollten Sie Azure Kubernetes Service verwenden. Weitere Informationen finden Sie unter [Bereitstellen von Modellen mit dem Azure Machine Learning Service](how-to-deploy-and-where.md).

>[!NOTE]
> Der Code in diesem Artikel wurde mit Version 1.0.83 des Azure Machine Learning SDK getestet.

## <a name="prerequisites"></a>Voraussetzungen

Schließen Sie zum Ausführen des Notebooks zuerst das Modelltraining in [Tutorial (Teil 1): Trainieren eines Imageklassifizierungsmodells](tutorial-train-models-with-aml.md).   Öffnen Sie anschließend das Notebook *img-classification-part2-deploy.ipynb* in Ihrem geklonten Ordner *tutorials/image-classification-mnist-data*.

Dieses Tutorial ist auch auf [GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/tutorials) verfügbar, falls Sie es in Ihrer eigenen [lokalen Umgebung](how-to-configure-environment.md#local) verwenden möchten.  Stellen Sie sicher, dass `matplotlib` und `scikit-learn` in Ihrer Umgebung installiert sind. 

> [!Important]
> Der Rest dieses Artikels enthält denselben Inhalt, den Sie auch im Notebook sehen.  
>
> Wechseln Sie nun zur Jupyter Notebook-Instanz, wenn Sie während der Ausführung des Codes mitlesen möchten.
> Klicken Sie zum Ausführen einer einzelnen Codezelle in einem Notebook auf die gewünschte Codezelle, und drücken Sie **UMSCHALT+EINGABE**. Oder führen Sie das gesamte Notebook aus, indem Sie auf der oberen Symbolleiste **Alle ausführen** auswählen.

## <a name="set-up-the-environment"></a><a name="start"></a>Einrichten der Umgebung

Zunächst richten Sie eine Testumgebung ein.

### <a name="import-packages"></a>Importieren von Paketen

Importieren Sie die für dieses Tutorial erforderlichen Python-Pakete.


```python
%matplotlib inline
import numpy as np
import matplotlib.pyplot as plt
 
import azureml.core

# Display the core SDK version number
print("Azure ML SDK Version: ", azureml.core.VERSION)
```

## <a name="deploy-as-web-service"></a>Bereitstellen als Webdienst

Stellen Sie das Modell als in ACI gehosteten Webdienst bereit. 

Zum Erstellen der richtigen Umgebung für ACI ist folgendes erforderlich:
* Ein Bewertungsskript zur Veranschaulichung der Verwendung des Modells
* Eine Konfigurationsdatei zum Erstellen der ACI
* Das zuvor trainierte Modell

### <a name="create-scoring-script"></a>Erstellen des Bewertungsskripts

Erstellen Sie das Bewertungsskript score.py, das vom Webdienstaufruf verwendet wird, um zu zeigen, wie das Modell verwendet wird.

Sie müssen zwei erforderliche Funktionen in das Bewertungsskript einschließen:
* Die `init()`-Funktion, die das Modell in der Regel in ein globales Objekt lädt. Diese Funktion wird nur dann ausgeführt, wenn der Docker-Container gestartet wird. 

* Die `run(input_data)`-Funktion verwendet das Modell zur Vorhersage eines Werts, der auf den Eingabedaten basiert. Ein- und Ausgaben zur Ausführung verwenden in der Regel JSON für die Serialisierung und Deserialisierung, allerdings werden auch andere Formate unterstützt.

```python
%%writefile score.py
import json
import numpy as np
import os
import pickle
import joblib

def init():
    global model
    # AZUREML_MODEL_DIR is an environment variable created during deployment.
    # It is the path to the model folder (./azureml-models/$MODEL_NAME/$VERSION)
    # For multiple models, it points to the folder containing all deployed models (./azureml-models)
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_mnist_model.pkl')
    model = joblib.load(model_path)

def run(raw_data):
    data = np.array(json.loads(raw_data)['data'])
    # make prediction
    y_hat = model.predict(data)
    # you can return any data type as long as it is JSON-serializable
    return y_hat.tolist()
```

### <a name="create-configuration-file"></a>Erstellen einer Konfigurationsdatei

Erstellen Sie eine Konfigurationsdatei für die Bereitstellung und geben Sie die Anzahl der CPUs und Gigabyte-RAM an, die für Ihren ACI-Container benötigt werden. Obwohl die Anforderungen von Ihrem Modell abhängen, ist der Standard von 1 Kern und 1 Gigabyte-RAM für viele Modelle in der Regel ausreichend. Wenn Sie das Gefühl haben, dass Sie später mehr brauchen, müssen Sie das Image neu erstellen und den Dienst neu bereitstellen.


```python
from azureml.core.webservice import AciWebservice

aciconfig = AciWebservice.deploy_configuration(cpu_cores=1, 
                                               memory_gb=1, 
                                               tags={"data": "MNIST",  "method" : "sklearn"}, 
                                               description='Predict MNIST with sklearn')
```

### <a name="deploy-in-aci"></a>Bereitstellen in ACI
Geschätzter Zeitaufwand: **Ca. 2-5 Minuten**

Konfigurieren Sie das Image und stellen Sie es bereit. Der folgende Code durchläuft diese Schritte:

1. Erstellen Sie mit der während des Trainings gespeicherten Umgebung (`tutorial-env`) ein Umgebungsobjekt, das die vom Modell benötigten Abhängigkeiten enthält.
1. Erstellen Sie die Rückschlusskonfiguration, die zum Bereitstellen des Modells als Webdienst erforderlich ist. Verwenden Sie dazu Folgendes:
   * Der Bewertungsdatei (`score.py`)
   * Das im vorherigen Schritt erstellte Umgebungsobjekt
1. Stellen Sie das Modell im ACI-Container bereit.
1. Rufen Sie den Webdienst-HTTP-Endpunkt ab.


```python
%%time
from azureml.core.webservice import Webservice
from azureml.core.model import InferenceConfig
from azureml.core.environment import Environment
from azureml.core import Workspace
from azureml.core.model import Model

ws = Workspace.from_config()
model = Model(ws, 'sklearn_mnist')


myenv = Environment.get(workspace=ws, name="tutorial-env", version="1")
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)

service = Model.deploy(workspace=ws, 
                       name='sklearn-mnist-svc3', 
                       models=[model], 
                       inference_config=inference_config, 
                       deployment_config=aciconfig)

service.wait_for_deployment(show_output=True)
```

Rufen Sie den Bewertungswebdienst des HTTP-Endpunkts ab, der REST-Clientaufrufe akzeptiert. Dieser Endpunkt kann von jedem genutzt werden, der den Webdienst testen oder in eine Anwendung integrieren möchte.


```python
print(service.scoring_uri)
```

## <a name="test-the-model"></a>Testen des Modells


### <a name="download-test-data"></a>Herunterladen der Testdaten
Laden Sie die Testdaten in das Verzeichnis **./data/** herunter.


```python
import os
from azureml.core import Dataset
from azureml.opendatasets import MNIST

data_folder = os.path.join(os.getcwd(), 'data')
os.makedirs(data_folder, exist_ok=True)

mnist_file_dataset = MNIST.get_file_dataset()
mnist_file_dataset.download(data_folder, overwrite=True)
```

### <a name="load-test-data"></a>Laden von Testdaten

Laden Sie die Testdaten aus dem Verzeichnis **./data/** , das während des Trainingstutorials erstellt wurde.


```python
from utils import load_data
import os
import glob

data_folder = os.path.join(os.getcwd(), 'data')
# note we also shrink the intensity values (X) from 0-255 to 0-1. This helps the neural network converge faster
X_test = load_data(glob.glob(os.path.join(data_folder,"**/t10k-images-idx3-ubyte.gz"), recursive=True)[0], False) / 255.0
y_test = load_data(glob.glob(os.path.join(data_folder,"**/t10k-labels-idx1-ubyte.gz"), recursive=True)[0], True).reshape(-1)
```

### <a name="predict-test-data"></a>Vorhersagen von Testdaten

Laden Sie das Test-Dataset in das Modell, um Vorhersagen zu erhalten.


Der folgende Code durchläuft diese Schritte:
1. Senden Sie die Daten als JSON-Array an den in ACI gehosteten Webdienst. 

1. Verwenden Sie die `run`-API des SDK, um den Dienst aufzurufen. Sie können auch unformatierte Daten mit einem beliebigen HTTP-Tool, z.B. Curl, aufrufen.


```python
import json
test = json.dumps({"data": X_test.tolist()})
test = bytes(test, encoding='utf8')
y_hat = service.run(input_data=test)
```

###  <a name="examine-the-confusion-matrix"></a>Überprüfen der Konfusionsmatrix

Erzeugen Sie eine Konfusionsmatrix, um zu sehen, wie viele Stichproben aus dem Testsatz korrekt klassifiziert sind. Beachten Sie den falsch klassifizierten Wert für die falsche Vorhersage.


```python
from sklearn.metrics import confusion_matrix

conf_mx = confusion_matrix(y_test, y_hat)
print(conf_mx)
print('Overall accuracy:', np.average(y_hat == y_test))
```

Die Ausgabe zeigt die Konfusionsmatrix:

```output
[[ 960    0    1    2    1    5    6    3    1    1]
 [   0 1112    3    1    0    1    5    1   12    0]
 [   9    8  920   20   10    4   10   11   37    3]
 [   4    0   17  921    2   21    4   12   20    9]
 [   1    2    5    3  915    0   10    2    6   38]
 [  10    2    0   41   10  770   17    7   28    7]
 [   9    3    7    2    6   20  907    1    3    0]
 [   2    7   22    5    8    1    1  950    5   27]
 [  10   15    5   21   15   27    7   11  851   12]
 [   7    8    2   13   32   13    0   24   12  898]]
Overall accuracy: 0.9204
```

Verwenden Sie `matplotlib`, um die Konfusionsmatrix als Diagramm anzuzeigen. In diesem Diagramm stellt die X-Achse die Ist-Werte und die Y-Achse die vorhergesagten Werte dar. Die Farbe in jedem Raster stellt die Fehlerrate dar. Je heller die Farbe, desto höher ist die Fehlerrate. Beispielsweise sind viele 5en falsch als 3en klassifiziert. Dies führt zu einem hellen Raster bei (5,3).

```python
# normalize the diagonal cells so that they don't overpower the rest of the cells when visualized
row_sums = conf_mx.sum(axis=1, keepdims=True)
norm_conf_mx = conf_mx / row_sums
np.fill_diagonal(norm_conf_mx, 0)

fig = plt.figure(figsize=(8, 5))
ax = fig.add_subplot(111)
cax = ax.matshow(norm_conf_mx, cmap=plt.cm.bone)
ticks = np.arange(0, 10, 1)
ax.set_xticks(ticks)
ax.set_yticks(ticks)
ax.set_xticklabels(ticks)
ax.set_yticklabels(ticks)
fig.colorbar(cax)
plt.ylabel('true labels', fontsize=14)
plt.xlabel('predicted values', fontsize=14)
plt.savefig('conf.png')
plt.show()
```

![Diagramm mit Konfusionsmatrix](./media/tutorial-deploy-models-with-aml/confusion.png)


## <a name="show-predictions"></a>Anzeigen von Vorhersagen

Testen Sie das bereitgestellte Modell mit einer zufälligen Stichprobe von 30 Bildern aus den Testdaten.  


1. Drucken Sie die zurückgegebenen Vorhersagen und stellen Sie sie zusammen mit den eingehenden Bildern grafisch dar. Rote Schrift und ein inverses Bild (weiß auf schwarz) heben falsch klassifizierte Stichproben hervor. 

 Da die Modellgenauigkeit hoch ist, müssen Sie möglicherweise den folgenden Code einige Male ausführen, bevor Sie eine falsch klassifizierte Stichprobe sehen.



```python
import json

# find 30 random samples from test set
n = 30
sample_indices = np.random.permutation(X_test.shape[0])[0:n]

test_samples = json.dumps({"data": X_test[sample_indices].tolist()})
test_samples = bytes(test_samples, encoding='utf8')

# predict using the deployed model
result = service.run(input_data=test_samples)

# compare actual value vs. the predicted values:
i = 0
plt.figure(figsize = (20, 1))

for s in sample_indices:
    plt.subplot(1, n, i + 1)
    plt.axhline('')
    plt.axvline('')
    
    # use different color for misclassified sample
    font_color = 'red' if y_test[s] != result[i] else 'black'
    clr_map = plt.cm.gray if y_test[s] != result[i] else plt.cm.Greys
    
    plt.text(x=10, y=-10, s=result[i], fontsize=18, color=font_color)
    plt.imshow(X_test[s].reshape(28, 28), cmap=clr_map)
    
    i = i + 1
plt.show()
```

Sie können auch unformatierte HTTP-Anforderung zum Testen des Webdiensts senden.


```python
import requests

# send a random row from the test set to score
random_index = np.random.randint(0, len(X_test)-1)
input_data = "{\"data\": [" + str(list(X_test[random_index])) + "]}"

headers = {'Content-Type': 'application/json'}

# for AKS deployment you'd need to the service key in the header as well
# api_key = service.get_key()
# headers = {'Content-Type':'application/json',  'Authorization':('Bearer '+ api_key)} 

resp = requests.post(service.scoring_uri, input_data, headers=headers)

print("POST to url", service.scoring_uri)
#print("input data:", input_data)
print("label:", y_test[random_index])
print("prediction:", resp.text)
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Um die Ressourcengruppe und den Arbeitsbereich für andere Tutorials und Untersuchungen zu behalten, können Sie mit diesem API-Aufruf nur die Container Instances-Bereitstellung löschen:

```python
service.delete()
```

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]


## <a name="next-steps"></a>Nächste Schritte

+ Weitere Informationen zu den Bereitstellungsoptionen für Azure Machine Learning finden Sie [hier](how-to-deploy-and-where.md).
+ Erfahren Sie, wie [Clients für den Webdienst erstellt werden](how-to-consume-web-service.md).
+  [Treffen Sie asynchron Vorhersagen für große Datenmengen](./tutorial-pipeline-batch-scoring-classification.md).
+ [Überwachen Sie Ihre Azure Machine Learning-Modelle mit Application Insights](how-to-enable-app-insights.md).
+ Probieren Sie das Tutorial [Erstellen Ihres Regressionsmodells mit automatisiertem Machine Learning](tutorial-auto-train-models.md) aus.