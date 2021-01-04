---
title: Erstellen eines Profils für Modellspeicher und CPU-Nutzung
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie ein Profil für Ihr Modell vor der Bereitstellung erstellen können. Die Profilerstellung bestimmt die Auslastung von Arbeitsspeicher und CPU des Modells.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.date: 07/31/2020
ms.topic: conceptual
zone_pivot_groups: aml-control-methods
ms.reviewer: larryfr
ms.custom: deploy
ms.openlocfilehash: b9ae40b3d2673961f9b84ed702f18b25b79b6d0c
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93320406"
---
# <a name="profile-your-model-to-determine-resource-utilization"></a>Erstellen eines Profils Ihres Modells zur Bestimmung der Ressourcenverwendung

In diesem Artikel wird gezeigt, wie Sie ein Profil für ein Machine Learning-Modell erstellen, um zu bestimmen, wie viel CPU und Arbeitsspeicher für das Modell bei der Bereitstellung als Webdienst zugeordnet werden müssen.

## <a name="prerequisites"></a>Voraussetzungen

In diesem Artikel wird davon ausgegangen, dass Sie ein Modell mit Azure Machine Learning trainiert und registriert haben. Ein Beispiel zum Trainieren und Registrieren eines Scikit-learn-Modells mit Azure Machine Learning finden Sie in [diesem Beispieltutorial](how-to-train-scikit-learn.md).

## <a name="limitations"></a>Einschränkungen

* Die Profilerstellung funktioniert nicht, wenn sich die Azure Container Registry (ACR) für Ihren Arbeitsbereich hinter einem virtuellen Netzwerk befindet.

## <a name="run-the-profiler"></a>Ausführen des Profilers

Nachdem Sie Ihr Modell registriert und die anderen für die Bereitstellung erforderlichen Komponenten vorbereitet haben, können Sie die CPU und den Speicher bestimmen, die der bereitgestellte Dienst benötigt. Die Profilerstellung testet den Dienst, der Ihr Modell ausführt, und gibt Informationen wie CPU-Auslastung, Speicherauslastung und Antwortlatenz zurück. Sie bietet auch eine Empfehlung für die CPU und den Speicher auf der Grundlage der Ressourcenverwendung.

Um ein Profil Ihres Modells erstellen zu können, benötigen Sie Folgendes:
* Ein registriertes Modell.
* Eine Rückschlusskonfiguration basierend auf Ihrem Eingabeskript und der Definition der Rückschlussumgebung.
* Ein einspaltiges Tabellendataset, bei dem jede Zeile eine Zeichenfolge enthält, die Beispielanforderungsdaten darstellt.

> [!IMPORTANT]
> An dieser Stelle unterstützen wir nur die Profilerstellung von Diensten, die erwarten, dass ihre Anforderungsdaten eine Zeichenfolge sind. Beispiel: string serialized json, text, string serialized image, usw. Der Inhalt der einzelnen Zeilen des Datasets (Zeichenfolge) wird in den Hauptteil der HTTP-Anforderung eingefügt und an den Dienst gesendet, der das Modell zur Bewertung kapselt.

> [!IMPORTANT]
> Wir unterstützen nur die Profilerstellung von bis zu 2 CPUs in den Regionen „China, Osten 2“ und „US Gov Arizona“.

Nachfolgend finden Sie ein Beispiel dafür, wie Sie ein Eingabedataset konstruieren können, um ein Profil eines Diensts zu erstellen, der erwartet, dass seine eingehenden Anforderungsdaten serialisierten JSON-Code enthalten. In diesem Fall haben Sie ein Dataset erstellt, das auf 100 Instanzen desselben Anforderungsdateninhalts basiert. In realen Szenarien wird empfohlen, dass Sie größere Datasets mit verschiedenen Eingaben verwenden, insbesondere wenn die Nutzung bzw. das Verhalten Ihrer Modellressourcen von Eingaben abhängig ist.

::: zone pivot="py-sdk"

```python
import json
from azureml.core import Datastore
from azureml.core.dataset import Dataset
from azureml.data import dataset_type_definitions

input_json = {'data': [[1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
                       [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]]}
# create a string that can be utf-8 encoded and
# put in the body of the request
serialized_input_json = json.dumps(input_json)
dataset_content = []
for i in range(100):
    dataset_content.append(serialized_input_json)
dataset_content = '\n'.join(dataset_content)
file_name = 'sample_request_data.txt'
f = open(file_name, 'w')
f.write(dataset_content)
f.close()

# upload the txt file created above to the Datastore and create a dataset from it
data_store = Datastore.get_default(ws)
data_store.upload_files(['./' + file_name], target_path='sample_request_data')
datastore_path = [(data_store, 'sample_request_data' +'/' + file_name)]
sample_request_data = Dataset.Tabular.from_delimited_files(
    datastore_path, separator='\n',
    infer_column_types=True,
    header=dataset_type_definitions.PromoteHeadersBehavior.NO_HEADERS)
sample_request_data = sample_request_data.register(workspace=ws,
                                                   name='sample_request_data',
                                                   create_new_version=True)
```

Sobald Sie das Dataset mit den Beispielanforderungsdaten bereit haben, erstellen Sie eine Rückschlusskonfiguration. Die Rückschlusskonfiguration basiert auf der „score.py“ und der Umgebungsdefinition. Im folgenden Beispiel wird veranschaulicht, wie die Rückschlusskonfiguration erstellt und die Profilerstellung ausgeführt wird:

```python
from azureml.core.model import InferenceConfig, Model
from azureml.core.dataset import Dataset


model = Model(ws, id=model_id)
inference_config = InferenceConfig(entry_script='path-to-score.py',
                                   environment=myenv)
input_dataset = Dataset.get_by_name(workspace=ws, name='sample_request_data')
profile = Model.profile(ws,
            'unique_name',
            [model],
            inference_config,
            input_dataset=input_dataset)

profile.wait_for_completion(True)

# see the result
details = profile.get_details()
```

::: zone-end

::: zone pivot="cli"


Der folgende Befehl veranschaulicht, wie ein Modell mithilfe der CLI profiliert wird:

```azurecli-interactive
az ml model profile -g <resource-group-name> -w <workspace-name> --inference-config-file <path-to-inf-config.json> -m <model-id> --idi <input-dataset-id> -n <unique-name>
```

> [!TIP]
> Um die durch die Profilerstellung zurückgegebenen Informationen beizubehalten, verwenden Sie Tags oder Eigenschaften für das Modell. Die Verwendung von Tags oder Eigenschaften speichert die Daten mit dem Modell in der Modellregistrierung. Die folgenden Beispiele zeigen das Hinzufügen eines neuen Tags, das die Informationen `requestedCpu` und `requestedMemoryInGb` enthält:
>
> ```python
> model.add_tags({'requestedCpu': details['requestedCpu'],
>                 'requestedMemoryInGb': details['requestedMemoryInGb']})
> ```
>
> ```azurecli-interactive
> az ml model profile -g <resource-group-name> -w <workspace-name> --i <model-id> --add-tag requestedCpu=1 --add-tag requestedMemoryInGb=0.5
> ```

::: zone-end

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

