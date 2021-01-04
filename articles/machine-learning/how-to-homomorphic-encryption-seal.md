---
title: Bereitstellen eines Diensts für verschlüsselte Rückschlüsse (Vorschauversion)
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie mit Microsoft SEAL einen verschlüsselten Vorhersagedienst für die Bildklassifizierung bereitzustellen.
author: luisquintanilla
ms.author: luquinta
ms.date: 07/09/2020
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, devx-track-python, deploy
ms.openlocfilehash: 3509530994b07a16fb1f2780fffc6fd27cf8aa7c
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93325516"
---
# <a name="how-to-deploy-an-encrypted-inferencing-web-service-preview"></a>Bereitstellen eines Webdiensts für verschlüsselte Rückschlüsse (Vorschauversion)

Erfahren Sie, wie Sie ein Bildklassifizierungsmodell als Webdienst für verschlüsselte Rückschlüsse in [Azure Container Instances](../container-instances/index.yml) (ACI) bereitstellen können. Der Webdienst ist ein Docker-Containerimage, das das Modell und die Bewertungslogik enthält.

In diesem Leitfaden verwenden Sie Azure Machine Learning Service für Folgendes:

> [!div class="checklist"]
> * Konfigurieren Ihrer Umgebungen
> * Bereitstellen eines Webdiensts für verschlüsselte Rückschlüsse
> * Vorbereiten von Testdaten
> * Erstellen verschlüsselter Vorhersagen
> * Bereinigen von Ressourcen

ACI ist eine hervorragende Lösung für das Testen und Verstehen des Workflows für die Modellimplementierung. Für skalierbare Produktionsbereitstellungen sollten Sie Azure Kubernetes Service verwenden. Weitere Informationen finden Sie unter [Bereitstellen von Modellen mit dem Azure Machine Learning Service](./how-to-deploy-and-where.md).

Die in diesem Beispiel verwendete Verschlüsselungsmethode ist die [homomorphe Verschlüsselung](https://github.com/Microsoft/SEAL#homomorphic-encryption). Die homomorphe Verschlüsselung ermöglicht es, Berechnungen mit verschlüsselten Daten durchzuführen, ohne Zugriff auf einen geheimen Schlüssel (Entschlüsselung) zu benötigen. Die Ergebnisse der Berechnungen sind verschlüsselt und können nur vom Besitzer des geheimen Schlüssels offengelegt werden. 

## <a name="prerequisites"></a>Voraussetzungen

Dieser Leitfaden geht davon aus, dass Sie über ein Bildklassifizierungsmodell verfügen, das bei Azure Machine Learning registriert ist. Wenn dies nicht der Fall ist, registrieren Sie das Modell mit einem [vortrainierten Modell](https://github.com/Azure/MachineLearningNotebooks/raw/master/tutorials/image-classification-mnist-data/sklearn_mnist_model.pkl) oder erstellen Sie Ihr eigenes, indem Sie das [Tutorial zum Trainieren eines Bildklassifizierungsmodells mit Azure Machine Learning](tutorial-train-models-with-aml.md) abschließen.

## <a name="configure-local-environment"></a>Konfigurieren der lokalen Umgebung

In einem Jupyter-Notebook

1. Importieren Sie die für dieses Beispiel erforderlichen Python-Pakete.

    ```python
    %matplotlib inline
    import numpy as np
    import matplotlib.pyplot as plt

    import azureml.core

    # display the core SDK version number
    print("Azure ML SDK Version: ", azureml.core.VERSION)
    ```

2. Installieren Sie die Bibliothek für die homomorphe Verschlüsselung für sichere Rückschlüsse.

    > [!NOTE]
    > Das `encrypted-inference`-Paket befindet sich derzeit in der Vorschauversion.

    [`encrypted-inference`](https://pypi.org/project/encrypted-inference) ist eine Bibliothek, die Bindungen für verschlüsselte Rückschlüsse basierend auf [Microsoft SEAL](https://github.com/Microsoft/SEAL) enthält.

    ```python
    !pip install encrypted-inference==0.9
    ```

## <a name="configure-the-inferencing-environment"></a>Konfigurieren der Rückschlussumgebung

Erstellen Sie eine Umgebung für Rückschlüsse, und fügen Sie das `encrypted-inference`-Paket als Conda-Abhängigkeit hinzu.

```python
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies

# to install required packages
env = Environment('tutorial-env')
cd = CondaDependencies.create(pip_packages=['azureml-dataprep[pandas,fuse]>=1.1.14', 'azureml-defaults', 'azure-storage-blob', 'encrypted-inference==0.9'], conda_packages = ['scikit-learn==0.22.1'])

env.python.conda_dependencies = cd

# Register environment to re-use later
env.register(workspace = ws)
```

## <a name="deploy-encrypted-inferencing-web-service"></a>Bereitstellen eines Webdiensts für verschlüsselte Rückschlüsse

Stellen Sie das Modell als in ACI gehosteten Webdienst bereit.

Zum Erstellen der richtigen Umgebung für ACI ist folgendes erforderlich:

* Ein Bewertungsskript zur Veranschaulichung der Verwendung des Modells
* Eine Konfigurationsdatei zum Erstellen der ACI
* Ein trainiertes Modell

### <a name="create-scoring-script"></a>Erstellen des Bewertungsskripts

Erstellen Sie das Bewertungsskript `score.py`, das vom Webdienst für Rückschlüsse verwendet wird.

Sie müssen zwei erforderliche Funktionen in das Bewertungsskript einschließen:

* Die `init()`-Funktion, die das Modell in der Regel in ein globales Objekt lädt. Diese Funktion wird nur dann ausgeführt, wenn der Docker-Container gestartet wird.
* Die `run(input_data)`-Funktion verwendet das Modell zur Vorhersage eines Werts, der auf den Eingabedaten basiert. Ein- und Ausgaben zur Ausführung verwenden in der Regel JSON für die Serialisierung und Deserialisierung, allerdings werden auch andere Formate unterstützt. Die Funktion ruft auf homomorpher Verschlüsselung basierende öffentliche Schlüssel ab, die vom Dienstanbieter hochgeladen werden.

```python
%%writefile score.py
import json
import os
import pickle
import joblib
from azure.storage.blob import BlobServiceClient, BlobClient, ContainerClient, PublicAccess
from encrypted.inference.eiserver import EIServer

def init():
    global model
    # AZUREML_MODEL_DIR is an environment variable created during deployment.
    # It is the path to the model folder (./azureml-models/$MODEL_NAME/$VERSION)
    # For multiple models, it points to the folder containing all deployed models (./azureml-models)
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_mnist_model.pkl')
    model = joblib.load(model_path)

    global server
    server = EIServer(model.coef_, model.intercept_, verbose=True)

def run(raw_data):

    json_properties = json.loads(raw_data)

    key_id = json_properties['key_id']
    conn_str = json_properties['conn_str']
    container = json_properties['container']
    data = json_properties['data']

    # download the public keys from blob storage
    blob_service_client = BlobServiceClient.from_connection_string(conn_str=conn_str)
    blob_client = blob_service_client.get_blob_client(container=container, blob=key_id)
    public_keys = blob_client.download_blob().readall()

    result = {}
    # make prediction
    result = server.predict(data, public_keys)

    # you can return any data type as long as it is JSON-serializable
    return result
```

### <a name="create-configuration-file"></a>Erstellen einer Konfigurationsdatei

Erstellen Sie eine Konfigurationsdatei für die Bereitstellung und geben Sie die Anzahl der CPUs und Gigabyte-RAM an, die für Ihren ACI-Container benötigt werden. Obwohl die Anforderungen von Ihrem Modell abhängen, ist der Standard von 1 Kern und 1 Gigabyte-RAM für viele Modelle in der Regel ausreichend. Wenn Sie das Gefühl haben, dass Sie später mehr brauchen, müssen Sie das Image neu erstellen und den Dienst neu bereitstellen.

```python
from azureml.core.webservice import AciWebservice

aciconfig = AciWebservice.deploy_configuration(cpu_cores=1, 
                                               memory_gb=1, 
                                               tags={"data": "MNIST",  "method" : "sklearn"}, 
                                               description='Encrypted Predict MNIST with sklearn + SEAL')
```

### <a name="deploy-to-azure-container-instances"></a>Bereitstellen in Azure Container Instances

Geschätzter Zeitaufwand: **Ca. 2-5 Minuten**

Konfigurieren Sie das Image und stellen Sie es bereit. Der folgende Code durchläuft diese Schritte:

1. Erstellen Sie mit der Umgebungsdatei (`myenv.yml`) ein Umgebungsobjekt, das die vom Modell benötigten Abhängigkeiten enthält.
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

myenv = Environment.get(workspace=ws, name="tutorial-env")
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)

service = Model.deploy(workspace=ws,
                       name='sklearn-encrypted-mnist-svc',
                       models=[model],
                       inference_config=inference_config,
                       deployment_config=aciconfig)

service.wait_for_deployment(show_output=True)
```

Rufen Sie den Bewertungswebdienst des HTTP-Endpunkts ab, der REST-Clientaufrufe akzeptiert. Dieser Endpunkt kann von jedem genutzt werden, der den Webdienst testen oder in eine Anwendung integrieren möchte.

```python
print(service.scoring_uri)
```

## <a name="prepare-test-data"></a>Vorbereiten von Testdaten

1. Laden Sie die Testdaten herunter. In diesem Fall werden sie in einem Verzeichnis namens *data* gespeichert.

    ```python
    import os
    from azureml.core import Dataset
    from azureml.opendatasets import MNIST
    
    data_folder = os.path.join(os.getcwd(), 'data')
    os.makedirs(data_folder, exist_ok=True)
    
    mnist_file_dataset = MNIST.get_file_dataset()
    mnist_file_dataset.download(data_folder, overwrite=True)
    ```

1. Laden Sie die Testdaten aus dem Verzeichnis *data*.

    ```python
    from utils import load_data
    import os
    import glob
    
    data_folder = os.path.join(os.getcwd(), 'data')
    # note we also shrink the intensity values (X) from 0-255 to 0-1. This helps the neural network converge faster
    X_test = load_data(glob.glob(os.path.join(data_folder,"**/t10k-images-idx3-ubyte.gz"), recursive=True)[0], False) / 255.0
    y_test = load_data(glob.glob(os.path.join(data_folder,"**/t10k-labels-idx1-ubyte.gz"), recursive=True)[0], True).reshape(-1)
    ```

## <a name="make-encrypted-predictions"></a>Erstellen verschlüsselter Vorhersagen

Verwenden Sie das Testdataset mit dem Modell, um Vorhersagen zu erhalten.

So erstellen Sie verschlüsselte Vorhersagen

1. Erstellen Sie einen neuen `EILinearRegressionClient`, einem auf homomorpher Verschlüsselung basierenden Client, und öffentliche Schlüssel.

    ```python
    from encrypted.inference.eiclient import EILinearRegressionClient

    # Create a new Encrypted inference client and a new secret key.
    edp = EILinearRegressionClient(verbose=True)

    public_keys_blob, public_keys_data = edp.get_public_keys()
    ```

1. Laden Sie durch homomorphe Verschlüsselung generierte öffentliche Schlüssel in den standardmäßigen Blobspeicher des Arbeitsbereichs hoch. Auf diese Weise können Sie die Schlüssel für den Rückschlussserver freigeben.

    ```python
    import azureml.core
    from azureml.core import Workspace, Datastore
    import os

    ws = Workspace.from_config()

    datastore = ws.get_default_datastore()
    container_name=datastore.container_name

    # Create a local file and write the keys to it
    public_keys = open(public_keys_blob, "wb")
    public_keys.write(public_keys_data)
    public_keys.close()

    # Upload the file to blob store
    datastore.upload_files([public_keys_blob])

    # Delete the local file
    os.remove(public_keys_blob)
    ```

1. Verschlüsseln der Testdaten

    ```python
    #choose any one sample from the test data 
    sample_index = 1

    #encrypt the data
    raw_data = edp.encrypt(X_test[sample_index])

    ```

1. Verwenden Sie die `run`-API des SDK zum Aufrufen des Diensts und zum Bereitstellen des Testdatasets für das Modell, um Vorhersagen zu erhalten. Wir müssen die Verbindungszeichenfolge an den Blobspeicher senden, in den die öffentlichen Schlüssel hochgeladen wurden.

    ```python
    import json
    from azureml.core import Webservice

    service = Webservice(ws, 'sklearn-encrypted-mnist-svc')

    #pass the connection string for blob storage to give the server access to the uploaded public keys 
    conn_str_template = 'DefaultEndpointsProtocol={};AccountName={};AccountKey={};EndpointSuffix=core.windows.net'
    conn_str = conn_str_template.format(datastore.protocol, datastore.account_name, datastore.account_key)

    #build the json 
    data = json.dumps({"data": raw_data, "key_id" : public_keys_blob, "conn_str" : conn_str, "container" : container_name })
    data = bytes(data, encoding='ASCII')

    print ('Making an encrypted inference web service call ')
    eresult = service.run(input_data=data)

    print ('Received encrypted inference results')
    ```

1. Verwenden Sie den Client, um die Ergebnisse zu entschlüsseln.

    ```python
    import numpy as np

    results = edp.decrypt(eresult)

    print ('Decrypted the results ', results)

    #Apply argmax to identify the prediction result
    prediction = np.argmax(results)

    print ( ' Prediction : ', prediction)
    print ( ' Actual Label : ', y_test[sample_index])
    ```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Löschen Sie den in diesem Beispiel erstellten Webdienst:

```python
service.delete()
```

Wenn Sie die von Ihnen erstellten Azure-Ressourcen nicht mehr verwenden möchten, löschen Sie sie. Dadurch wird verhindert, dass Ihnen nicht genutzte Ressourcen, die noch aktiv sind, in Rechnung gestellt werden. Weitere Informationen zum [Bereinigen von Ressourcen](how-to-manage-workspace.md#clean-up-resources) finden Sie in diesem Leitfaden.