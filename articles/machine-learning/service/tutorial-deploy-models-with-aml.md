---
title: 'Tutorial zur Bildklassifizierung: Bereitstellen von Modellen'
titleSuffix: Azure Machine Learning service
description: Dieses Tutorial zeigt, wie Sie mit Azure Machine Learning Service ein Bildklassifizierungsmodell mit scikit-learn in einem Python Jupyter Notebook bereitstellen. Dieses Tutorial ist der zweite Teil einer zweiteiligen Reihe.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: sdgilley
ms.author: sgilley
ms.date: 05/14/2019
ms.custom: seodec18
ms.openlocfilehash: f2984ed9a7edd36b166c0dff9aa87d86c6d2ab45
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65800733"
---
# <a name="tutorial-deploy-an-image-classification-model-in-azure-container-instances"></a>Tutorial: Bereitstellen eines Bildklassifizierungsmodells in Azure Container Instances

Dieses Tutorial ist der **zweite Teil einer zweiteiligen Reihe**. Im [vorherigen Tutorial](tutorial-train-models-with-aml.md) haben Sie Machine Learning-Modelle trainiert und anschließend ein Modell in Ihrem Arbeitsbereich in der Cloud registriert.  

Nun können Sie das Modell als Webdienst in [Azure Container Instances](https://docs.microsoft.com/azure/container-instances/) bereitstellen. Ein Webdienst ist ein Image (in diesem Fall: ein Docker-Image). Es kapselt die Bewertungslogik und das eigentliche Modell. 

In diesem Teil des Tutorials verwenden Sie den Azure Machine Learning-Dienst für Folgendes:

> [!div class="checklist"]
> * Einrichten Ihrer Testumgebung
> * Abrufen des Modells aus Ihrem Arbeitsbereich
> * Lokales Testen des Modells
> * Bereitstellen eines Modells in Container Instances
> * Testen des bereitgestellten Modells

Container Instances ist eine hervorragende Lösung für das Testen und Verstehen des Workflows. Für skalierbare Produktionsbereitstellungen sollten Sie Azure Kubernetes Service verwenden. Weitere Informationen finden Sie unter [Bereitstellen von Modellen mit dem Azure Machine Learning Service](how-to-deploy-and-where.md).

>[!NOTE]
> Der Code in diesem Artikel wurde mit Version 1.0.8 des Azure Machine Learning SDK getestet.

## <a name="prerequisites"></a>Voraussetzungen
Wechseln Sie zu [Festlegen der Entwicklungsumgebung](#start), um die Notebookschritte durchzulesen.  

Schließen Sie zum Ausführen des Notebooks zuerst das Modelltraining in [Tutorial (Teil 1): Trainieren eines Bildklassifizierungsmodells mit dem Azure Machine Learning Service](tutorial-train-models-with-aml.md).   Führen Sie dann das Notebook **tutorials/img-classification-part2-deploy.ipynb** auf demselben Notebookserver aus.


## <a name="start"></a>Einrichten der Umgebung

Zunächst richten Sie eine Testumgebung ein.

### <a name="import-packages"></a>Importieren von Paketen

Importieren Sie die für dieses Tutorial erforderlichen Python-Pakete:

```python
%matplotlib inline
import numpy as np
import matplotlib
import matplotlib.pyplot as plt
 
import azureml
from azureml.core import Workspace, Run

# display the core SDK version number
print("Azure ML SDK Version: ", azureml.core.VERSION)
```

### <a name="retrieve-the-model"></a>Abrufen des Modells

Im vorherigen Tutorial haben Sie ein Modell in Ihrem Arbeitsbereich registriert. Laden Sie nun diesen Arbeitsbereich, und laden Sie das Modell in Ihr lokales Verzeichnis herunter:


```python
from azureml.core import Workspace
from azureml.core.model import Model
import os 
ws = Workspace.from_config()
model=Model(ws, 'sklearn_mnist')

model.download(target_dir=os.getcwd(), exist_ok=True)

# verify the downloaded model file
file_path = os.path.join(os.getcwd(), "sklearn_mnist_model.pkl")

os.stat(file_path)
```

## <a name="test-the-model-locally"></a>Lokales Testen des Modells

Vergewissern Sie sich vor der Bereitstellung, dass Ihr Modell lokal funktioniert:
* Laden Sie Testdaten.
* Verwenden Sie die Testdaten für Vorhersagen.
* Überprüfen Sie die Konfusionsmatrix.

### <a name="load-test-data"></a>Laden von Testdaten

Laden Sie die Testdaten aus dem Verzeichnis **./data/**, das im Rahmen des Trainingstutorials erstellt wurde:

```python
from utils import load_data
import os

data_folder = os.path.join(os.getcwd(), 'data')
# note we also shrink the intensity values (X) from 0-255 to 0-1. This helps the neural network converge faster
X_test = load_data(os.path.join(data_folder, 'test-images.gz'), False) / 255.0
y_test = load_data(os.path.join(data_folder, 'test-labels.gz'), True).reshape(-1)
```

### <a name="predict-test-data"></a>Vorhersagen von Testdaten

Laden Sie das Testdataset in das Modell, um Vorhersagen zu erhalten:

```python
import pickle
from sklearn.externals import joblib

clf = joblib.load( os.path.join(os.getcwd(), 'sklearn_mnist_model.pkl'))
y_hat = clf.predict(X_test)
```

###  <a name="examine-the-confusion-matrix"></a>Überprüfen der Konfusionsmatrix

Erzeugen Sie eine Konfusionsmatrix, um zu sehen, wie viele Stichproben aus dem Testsatz korrekt klassifiziert sind. Beachten Sie den falsch klassifizierten Wert für die falschen Vorhersagen: 

```python
from sklearn.metrics import confusion_matrix

conf_mx = confusion_matrix(y_test, y_hat)
print(conf_mx)
print('Overall accuracy:', np.average(y_hat == y_test))
```

Die Ausgabe zeigt die Konfusionsmatrix:

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
   

Verwenden Sie `matplotlib`, um die Konfusionsmatrix als Diagramm anzuzeigen. In diesem Diagramm wird die X-Achse für die Ist-Werte und die Y-Achse für die vorhergesagten Werte verwendet: Die Farbe in den einzelnen Rastern gibt jeweils die Fehlerrate an. Je heller die Farbe, desto höher ist die Fehlerrate. Beispielsweise sind viele 5en falsch als 3en klassifiziert. Dies führt zu einem hellen Raster bei (5,3):

```python
# normalize the diagonal cells so that they don't overpower the rest of the cells when visualized
row_sums = conf_mx.sum(axis=1, keepdims=True)
norm_conf_mx = conf_mx / row_sums
np.fill_diagonal(norm_conf_mx, 0)

fig = plt.figure(figsize=(8,5))
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

## <a name="deploy-as-a-web-service"></a>Bereitstellen als Webdienst

Nachdem Sie das Modell getestet haben und mit den Ergebnissen zufrieden sind, stellen Sie das Modell als in Container Instances gehosteten Webdienst bereit. 

Für die Erstellung der korrekten Umgebung für Container Instances sind folgende Komponenten erforderlich:
* Ein Bewertungsskript zur Veranschaulichung der Verwendung des Modells
* Eine Umgebungsdatei, um anzugeben, welche Pakete installiert werden müssen
* Eine Konfigurationsdatei zum Erstellen der Containerinstanz
* Das zuvor trainierte Modell

<a name="make-script"></a>

### <a name="create-scoring-script"></a>Erstellen des Bewertungsskripts

Erstellen Sie das Bewertungsskript (**score.py**). Der Aufruf des Webdiensts verwendet dieses Skript, um zu zeigen, wie das Modell verwendet wird.

Schließen Sie die beiden folgenden erforderlichen Funktionen in das Bewertungsskript ein:
* Die `init()`-Funktion, die das Modell in der Regel in ein globales Objekt lädt. Diese Funktion wird nur dann ausgeführt, wenn der Docker-Container gestartet wird. 

* Die `run(input_data)`-Funktion verwendet das Modell zur Vorhersage eines Werts, der auf den Eingabedaten basiert. Ein- und Ausgaben zur Ausführung verwenden in der Regel JSON für die Serialisierung und Deserialisierung, allerdings werden auch andere Formate unterstützt.

```python
%%writefile score.py
import json
import numpy as np
import os
import pickle
from sklearn.externals import joblib
from sklearn.linear_model import LogisticRegression

from azureml.core.model import Model

def init():
    global model
    # retrieve the path to the model file using the model name
    model_path = Model.get_model_path('sklearn_mnist')
    model = joblib.load(model_path)

def run(raw_data):
    data = np.array(json.loads(raw_data)['data'])
    # make prediction
    y_hat = model.predict(data)
    # you can return any data type as long as it is JSON-serializable
    return y_hat.tolist()
```

<a name="make-myenv"></a>

### <a name="create-environment-file"></a>Erstellen einer Umgebungsdatei

Erstellen Sie als Nächstes eine Umgebungsdatei namens **myenv.yml**, die alle Paketabhängigkeiten des Skripts angibt. Diese Datei wird verwendet, um sicherzustellen, dass alle diese Abhängigkeiten im Docker-Image installiert sind. Dieses Modell benötigt `scikit-learn` und `azureml-sdk`:

```python
from azureml.core.conda_dependencies import CondaDependencies 

myenv = CondaDependencies()
myenv.add_conda_package("scikit-learn")

with open("myenv.yml","w") as f:
    f.write(myenv.serialize_to_string())
```
Überprüfen Sie den Inhalt der Datei `myenv.yml`:

```python
with open("myenv.yml","r") as f:
    print(f.read())
```

### <a name="create-a-configuration-file"></a>Erstellen einer Konfigurationsdatei

Erstellen Sie eine Bereitstellungskonfigurationsdatei. Geben Sie an, wie viele CPUs und wie viele Gigabyte Arbeitsspeicher für Ihren Container Instances-Container benötigt werden. Die Anforderungen hängen zwar von Ihrem Modell ab, die Standardwerte von einem Kern und einem Gigabyte Arbeitsspeicher sind jedoch für viele Modelle ausreichend. Sollten Sie später höhere Werte benötigen, erstellen Sie das Image neu, und stellen Sie den Dienst erneut bereit.

```python
from azureml.core.webservice import AciWebservice

aciconfig = AciWebservice.deploy_configuration(cpu_cores=1, 
                                               memory_gb=1, 
                                               tags={"data": "MNIST",  "method" : "sklearn"}, 
                                               description='Predict MNIST with sklearn')
```

### <a name="deploy-in-container-instances"></a>Bereitstellen in Container Instances
Der Bereitstellungsvorgang dauert voraussichtlich **sieben bis acht Minuten**.

Konfigurieren Sie das Image und stellen Sie es bereit. Der folgende Code durchläuft diese Schritte:

1. Erstellen Sie ein Image mit folgenden Dateien:
   * Bewertungsdatei: `score.py`
   * Umgebungsdatei: `myenv.yml`
   * Der Modelldatei.
1. Registrieren Sie das Image im Arbeitsbereich. 
1. Senden Sie das Bild an den Container Instances-Container.
1. Starten Sie einen Container in Container Instances mithilfe des Bilds.
1. Rufen Sie den Webdienst-HTTP-Endpunkt ab.


```python
%%time
from azureml.core.webservice import Webservice
from azureml.core.image import ContainerImage

# configure the image
image_config = ContainerImage.image_configuration(execution_script="score.py", 
                                                  runtime="python", 
                                                  conda_file="myenv.yml")

service = Webservice.deploy_from_model(workspace=ws,
                                       name='sklearn-mnist-svc',
                                       deployment_config=aciconfig,
                                       models=[model],
                                       image_config=image_config)

service.wait_for_deployment(show_output=True)
```

Rufen Sie den Bewertungswebdienst des HTTP-Endpunkts ab, der REST-Clientaufrufe akzeptiert. Sie können diesen Endpunkt für jeden freigeben, der den Webdienst testen oder in eine Anwendung integrieren möchte: 

```python
print(service.scoring_uri)
```


## <a name="test-the-deployed-service"></a>Testen des bereitgestellten Diensts

Zuvor haben Sie alle Testdaten mit der lokalen Version des Modells bewertet. Sie können nun das bereitgestellte Modell mit einer zufälligen Stichprobe von 30 Bildern aus den Testdaten testen.  

Der folgende Code durchläuft diese Schritte:
1. Senden Sie die Daten als JSON-Array an den in Container Instances gehosteten Webdienst. 

1. Verwenden Sie die `run`-API des SDK, um den Dienst aufzurufen. Sie können auch unformatierte Daten mit einem beliebigen HTTP-Tool (beispielsweise **curl**) aufrufen.

1. Drucken Sie die zurückgegebenen Vorhersagen und stellen Sie sie zusammen mit den eingehenden Bildern grafisch dar. Rote Schrift und ein inverses Bild (weiß auf schwarz) heben falsch klassifizierte Stichproben hervor. 

Da die Modellgenauigkeit hoch ist, müssen Sie möglicherweise den folgenden Code einige Male ausführen, bevor Sie eine falsch klassifizierte Stichprobe sehen:

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
    
    plt.text(x=10, y =-10, s=result[i], fontsize=18, color=font_color)
    plt.imshow(X_test[s].reshape(28, 28), cmap=clr_map)
    
    i = i + 1
plt.show()
```

Das folgende Ergebnis stammt aus einer zufälligen Stichprobe an Testbildern:

![Grafik mit Ergebnissen](./media/tutorial-deploy-models-with-aml/results.png)

Sie können auch eine unformatierte HTTP-Anforderung zum Testen des Webdiensts senden:

```python
import requests

# send a random row from the test set to score
random_index = np.random.randint(0, len(X_test)-1)
input_data = "{\"data\": [" + str(list(X_test[random_index])) + "]}"

headers = {'Content-Type':'application/json'}

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

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]


## <a name="next-steps"></a>Nächste Schritte

+ Weitere Informationen zu den Bereitstellungsoptionen für den Azure Machine Learning-Dienst finden Sie [hier](how-to-deploy-and-where.md).
+ Erfahren Sie, wie [Clients für den Webdienst erstellt werden](how-to-consume-web-service.md).
+  [Treffen Sie asynchron Vorhersagen für große Datenmengen](how-to-run-batch-predictions.md).
+ [Überwachen Sie Ihre Azure Machine Learning-Modelle mit Application Insights](how-to-enable-app-insights.md).
+ Probieren Sie das Tutorial [Erstellen Ihres Regressionsmodells mit automatisiertem Machine Learning](tutorial-auto-train-models.md) aus. 
