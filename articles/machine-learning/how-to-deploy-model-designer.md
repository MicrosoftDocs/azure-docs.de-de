---
title: Verwenden von Studio zum Bereitstellen von Modellen, die im Designer trainiert wurden
titleSuffix: Azure Machine Learning
description: Verwenden Sie Azure Machine Learning Studio, um Machine Learning-Modelle bereitzustellen, ohne eine einzige Codezeile schreiben zu müssen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: keli19
author: likebupt
ms.reviewer: peterlu
ms.date: 10/29/2020
ms.topic: conceptual
ms.custom: how-to, deploy, studio
ms.openlocfilehash: 23c6417741d0753fcdaaf30c89c8f51348cc5dc5
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94554681"
---
# <a name="use-the-studio-to-deploy-models-trained-in-the-designer"></a>Verwenden von Studio zum Bereitstellen von Modellen, die im Designer trainiert wurden

In diesem Artikel erfahren Sie, wie Sie ein trainiertes Modell aus dem Designer als Echtzeitendpunkt in Azure Machine Learning Studio bereitstellen.

Die Bereitstellung in Studio umfasst die folgenden Schritte:

1. Registrieren des trainierten Modells
1. Herunterladen des Eingabeskripts und der Conda-Abhängigkeitendatei für das Modell
1. (Optional) Konfigurieren Sie das Eingabeskript.
1. Bereitstellen des Modells auf einem Computeziel

Sie können Modelle auch direkt im Designer bereitstellen, um die Schritte zur Modellregistrierung und zum Herunterladen der Dateien zu überspringen. Dies kann für eine schnelle Bereitstellung nützlich sein. Weitere Informationen finden Sie unter [Bereitstellen eines Modells mit dem Designer](tutorial-designer-automobile-price-deploy.md).

Im Designer trainierte Modelle können auch über das SDK oder über die Befehlszeilenschnittstelle (CLI) bereitgestellt werden. Weitere Informationen finden Sie unter [Bereitstellen eines vorhandenen Modells mit Azure Machine Learning](how-to-deploy-existing-model.md).

## <a name="prerequisites"></a>Voraussetzungen

* [Ein Azure Machine Learning-Arbeitsbereich](how-to-manage-workspace.md)

* Eine abgeschlossene Trainingspipeline mit einem der folgenden Module:
    - [Train Model-Modul](./algorithm-module-reference/train-model.md)
    - [Modul „Trainieren eines Anomalieerkennungsmodells“](./algorithm-module-reference/train-anomaly-detection-model.md)
    - [Modul „Train Clustering Model“ (Trainieren eines Clusteringmodells)](./algorithm-module-reference/train-clustering-model.md)
    - [Modul „Train Pytorch Model“ (PyTorch-Modell trainieren)](./algorithm-module-reference/train-pytorch-model.md)
    - [Modul „Train SVD Recommender“ (SVD-Empfehlung trainieren)](./algorithm-module-reference/train-svd-recommender.md)
    - [Modul „Train Vowpal Wabbit Model“ (Vowpal Wabbit-Modell trainieren)](./algorithm-module-reference/train-vowpal-wabbit-model.md)
    - [Modul „Train Wide & Deep Model“ (Wide & Deep-Modell trainieren)](./algorithm-module-reference/train-wide-and-deep-recommender.md)

## <a name="register-the-model"></a>Registrieren des Modells

Registrieren Sie nach Anschluss der Trainingspipeline das trainierte Modell in Ihrem Azure Machine Learning-Arbeitsbereich, um in anderen Projekten auf das Modell zugreifen zu können.

1. Wählen Sie das Modul [Train Model](./algorithm-module-reference/train-model.md) aus.
1. Wählen Sie im rechten Bereich die Registerkarte **Ausgaben + Protokolle** aus.
1. Wählen Sie das Symbol **Modell registrieren** aus. ![Screenshot des Zahnradsymbols](./media/how-to-deploy-model-designer/register-model-icon.png).

    ![Screenshot des rechten Bereichs des Moduls „Train Model“](./media/how-to-deploy-model-designer/train-model-right-pane.png)

1. Geben Sie einen Namen für das Modell ein, und wählen Sie dann **Speichern** aus.

Nachdem Sie das Modell registriert haben, finden Sie es in Studio auf der Ressourcenseite **Modelle**.
    
![Screenshot des registrierten Modells auf der Ressourcenseite „Modelle“](./media/how-to-deploy-model-designer/models-asset-page.png)


## <a name="download-the-entry-script-file-and-conda-dependencies-file"></a>Herunterladen des Eingabeskripts und der Conda-Abhängigkeitendatei

Zum Bereitstellen eines Modells in Azure Machine Learning Studio benötigen Sie die folgenden Dateien:

- **Eingabeskriptdatei:** lädt das trainierte Modell, verarbeitet Eingabedaten von Anforderungen, führt Echtzeitrückschlüsse durch und gibt das Ergebnis zurück. Wenn das Modul **Train Model** abgeschlossen ist, generiert der Designer automatisch die Eingabeskriptdatei `score.py`.

- **Conda-Abhängigkeitendatei:** gibt an, von welchen PIP- und Conda-Paketen Ihr Webdienst abhängig ist. Wenn das Modul **Train Model** abgeschlossen ist, erstellt der Designer automatisch die Datei `conda_env.yaml`.

Sie können diese beiden Dateien im rechten Bereich des Moduls **Train Model** herunterladen:

1. Wählen Sie das Modul **Train Model** (Modell trainieren) aus.
1. Wählen Sie auf der Registerkarte **Ausgaben + Protokolle** den Ordner `trained_model_outputs` aus.
1. Laden Sie die Dateien `conda_env.yaml` und `score.py` herunter.

    ![Screenshot des Herunterladens von Dateien für die Bereitstellung im rechten Bereich](./media/how-to-deploy-model-designer/download-artifacts-in-right-pane.png)

Alternativ können Sie die Dateien auf der Ressourcenseite **Modelle** herunterladen, nachdem Sie das Modell registriert haben:

1. Navigieren Sie zur Ressourcenseite **Modelle**.
1. Wählen Sie das Modell aus, das Sie bereitstellen möchten.
1. Wählen Sie die Registerkarte **Artefakte** aus.
1. Wählen Sie den Ordner `trained_model_outputs` aus.
1. Laden Sie die Dateien `conda_env.yaml` und `score.py` herunter.  

    ![Screenshot des Herunterladens von Dateien für die Bereitstellung auf der Detailseite „Modelle“](./media/how-to-deploy-model-designer/download-artifacts-in-models-page.png)

> [!NOTE]
> Die Datei `score.py` bietet nahezu die gleiche Funktionalität wie die **Score Model**-Module. Einige Module wie [Score SVD Recommender](./algorithm-module-reference/score-svd-recommender.md), [Score Wide and Deep Recommender](./algorithm-module-reference/score-wide-and-deep-recommender.md) und [Score Vowpal Wabbit Model](./algorithm-module-reference/score-vowpal-wabbit-model.md) weisen jedoch Parameter für unterschiedliche Bewertungsmodi auf. Sie können diese Parameter auch im Eingabeskript ändern.
>
>Weitere Informationen zum Festlegen von Parametern in der Datei `score.py` finden Sie im Abschnitt [Konfigurieren des Eingabeskripts](#configure-the-entry-script).

## <a name="deploy-the-model"></a>Bereitstellen des Modells

Nachdem Sie die erforderlichen Dateien heruntergeladen haben, können Sie das Modell bereitstellen.

1. Wählen Sie auf der Ressourcenseite **Modelle** das registrierte Modell aus.
1. Wählen Sie die Schaltfläche **Bereitstellen** aus.
1. Geben Sie im Konfigurationsmenü die folgenden Informationen ein:

    - Geben Sie einen Namen für den Endpunkt ein.
    - Wählen Sie aus, ob das Modell in [Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md) oder [Azure Container Instance](how-to-deploy-azure-container-instance.md) bereitgestellt werden soll.
    - Laden Sie `score.py` als **Eingabeskriptdatei** hoch.
    - Laden Sie `conda_env.yml` als **Conda-Abhängigkeitendatei** hoch. 

    >[!TIP]
    > In den **erweiterten** Einstellungen können Sie die CPU-/Arbeitsspeicherkapazität und andere Parameter für die Bereitstellung festlegen. Diese Einstellungen sind für bestimmte Modelle wichtig, z. B. PyTorch-Modelle, die eine große Menge Speicher (ca. 4 GB) verbrauchen.

1. Wählen Sie **Bereitstellen** aus, um das Modell als Echtzeitendpunkt bereitzustellen.

    ![Screenshot der Bereitstellung eines Modells auf der Ressourcenseite „Modell“](./media/how-to-deploy-model-designer/deploy-model.png)

## <a name="consume-the-real-time-endpoint"></a>Verwenden des Echtzeitendpunkts

Nach erfolgreicher Bereitstellung finden Sie den Echtzeitendpunkt auf der Ressourcenseite **Endpunkte**. Dort finden Sie einen REST-Endpunkt, den Clients zum Übermitteln von Anforderungen an den Echtzeitendpunkt verwenden können. 

> [!NOTE]
> Der Designer generiert auch eine JSON-Datei mit Beispieldaten zum Testen. Sie können `_samples.json` im Ordner **trained_model_outputs** herunterladen.

Verwenden Sie das folgende Codebeispiel, um einen Echtzeitendpunkt zu nutzen.

```python

import json
from pathlib import Path
from azureml.core.workspace import Workspace, Webservice
 
service_name = 'YOUR_SERVICE_NAME'
ws = Workspace.get(
    name='WORKSPACE_NAME',
    subscription_id='SUBSCRIPTION_ID',
    resource_group='RESOURCEGROUP_NAME'
)
service = Webservice(ws, service_name)
sample_file_path = '_samples.json'
 
with open(sample_file_path, 'r') as f:
    sample_data = json.load(f)
score_result = service.run(json.dumps(sample_data))
print(f'Inference result = {score_result}')
```

### <a name="consume-computer-vision-related-real-time-endpoints"></a>Verwenden von auf maschinelles Sehen bezogenen Echtzeitendpunkten

Wenn Sie auf maschinelles Sehen bezogene Echtzeitendpunkte verwenden, müssen Sie Bilder in Bytes konvertieren, da der Webdienst nur Zeichenfolgen als Eingabe akzeptiert. Es folgt der Beispielcode:

```python
import base64
import json
from copy import deepcopy
from pathlib import Path
from azureml.studio.core.io.image_directory import (IMG_EXTS, image_from_file, image_to_bytes)
from azureml.studio.core.io.transformation_directory import ImageTransformationDirectory

# image path
image_path = Path('YOUR_IMAGE_FILE_PATH')

# provide the same parameter setting as in the training pipeline. Just an example here.
image_transform = [
    # format: (op, args). {} means using default parameter values of torchvision.transforms.
    # See https://pytorch.org/docs/stable/torchvision/transforms.html
    ('Resize', 256),
    ('CenterCrop', 224),
    # ('Pad', 0),
    # ('ColorJitter', {}),
    # ('Grayscale', {}),
    # ('RandomResizedCrop', 256),
    # ('RandomCrop', 224),
    # ('RandomHorizontalFlip', {}),
    # ('RandomVerticalFlip', {}),
    # ('RandomRotation', 0),
    # ('RandomAffine', 0),
    # ('RandomGrayscale', {}),
    # ('RandomPerspective', {}),
]
transform = ImageTransformationDirectory.create(transforms=image_transform).torch_transform

# download _samples.json file under Outputs+logs tab in the right pane of Train Pytorch Model module
sample_file_path = '_samples.json'
with open(sample_file_path, 'r') as f:
    sample_data = json.load(f)

# use first sample item as the default value
default_data = sample_data[0]
data_list = []
for p in image_path.iterdir():
    if p.suffix.lower() in IMG_EXTS:
        data = deepcopy(default_data)
        # convert image to bytes
        data['image'] = base64.b64encode(image_to_bytes(transform(image_from_file(p)))).decode()
        data_list.append(data)

# use data.json as input of consuming the endpoint
data_file_path = 'data.json'
with open(data_file_path, 'w') as f:
    json.dump(data_list, f)
```

## <a name="configure-the-entry-script"></a>Konfigurieren des Eingabeskripts

Einige Module im Designer wie [Score SVD Recommender](./algorithm-module-reference/score-svd-recommender.md), [Score Wide and Deep Recommender](./algorithm-module-reference/score-wide-and-deep-recommender.md) und [Score Vowpal Wabbit Model](./algorithm-module-reference/score-vowpal-wabbit-model.md) weisen Parameter für unterschiedliche Bewertungsmodi auf. 

In diesem Abschnitt erfahren Sie, wie Sie diese Parameter auch in der Eingabeskriptdatei aktualisieren.

Im folgenden Beispiel wird das Standardverhalten für ein trainiertes **Wide & Deep recommender**-Modell aktualisiert. Standardmäßig weist die Datei `score.py` den Webdienst an, Bewertungen zwischen Benutzern und Elementen vorherzusagen. 

Sie können die Eingabeskriptdatei so ändern, dass Elementempfehlungen abgegeben und empfohlene Elemente zurückgegeben werden, indem Sie den Parameter `recommender_prediction_kind` ändern.


```python
import os
import json
from pathlib import Path
from collections import defaultdict
from azureml.studio.core.io.model_directory import ModelDirectory
from azureml.designer.modules.recommendation.dnn.wide_and_deep.score. \
    score_wide_and_deep_recommender import ScoreWideAndDeepRecommenderModule
from azureml.designer.serving.dagengine.utils import decode_nan
from azureml.designer.serving.dagengine.converter import create_dfd_from_dict

model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'trained_model_outputs')
schema_file_path = Path(model_path) / '_schema.json'
with open(schema_file_path) as fp:
    schema_data = json.load(fp)


def init():
    global model
    model = ModelDirectory.load(load_from_dir=model_path)


def run(data):
    data = json.loads(data)
    input_entry = defaultdict(list)
    for row in data:
        for key, val in row.items():
            input_entry[key].append(decode_nan(val))

    data_frame_directory = create_dfd_from_dict(input_entry, schema_data)

    # The parameter names can be inferred from Score Wide and Deep Recommender module parameters:
    # convert the letters to lower cases and replace whitespaces to underscores.
    score_params = dict(
        trained_wide_and_deep_recommendation_model=model,
        dataset_to_score=data_frame_directory,
        training_data=None,
        user_features=None,
        item_features=None,
        ################### Note #################
        # Set 'Recommender prediction kind' parameter to enable item recommendation model
        recommender_prediction_kind='Item Recommendation',
        recommended_item_selection='From All Items',
        maximum_number_of_items_to_recommend_to_a_user=5,
        whether_to_return_the_predicted_ratings_of_the_items_along_with_the_labels='True')
    result_dfd, = ScoreWideAndDeepRecommenderModule().run(**score_params)
    result_df = result_dfd.data
    return json.dumps(result_df.to_dict("list"))
```

Für die Modelle **Wide & Deep recommender** und **Vowpal Wabbit** können Sie den Parameter für den Bewertungsmodus mithilfe der folgenden Methoden konfigurieren:

- Die Parameternamen bestehen aus Kombinationen aus Kleinbuchstaben und Unterstrichen von Parameternamen für [Score Vowpal Wabbit Model](./algorithm-module-reference/score-vowpal-wabbit-model.md) und [Score Wide and Deep Recommender](./algorithm-module-reference/score-wide-and-deep-recommender.md).
- Die Parameterwerte für den Modustyp sind Zeichenfolgen der entsprechenden Optionsnamen. In den obigen Codebeispielen kann **Recommender prediction kind** als Beispiel dienen: Der Wert hierbei kann `'Rating Prediction'` oder `'Item Recommendation'` sein. Andere Werte sind nicht zulässig.

Für trainierte **SVD recommender**-Modelle sind die Parameternamen und -Werte weniger offensichtlich, und Sie können die Parameter für die Festlegung in den folgenden Tabellen nachschlagen.

| Parametername in [Score SVD Recommender](./algorithm-module-reference/score-svd-recommender.md)                           | Parametername in der Eingabeskriptdatei |
| ------------------------------------------------------------ | --------------------------------------- |
| Recommender prediction kind                                  | prediction_kind                         |
| Empfohlene Elementauswahl                                   | recommended_item_selection              |
| Minimale Größe des Empfehlungspools für einen einzelnen Benutzer    | min_recommendation_pool_size            |
| Maximale Anzahl von Elementen, die einem Benutzer empfohlen werden soll               | max_recommended_item_count              |
| Gibt an, ob die vorhergesagten Bewertungen der Elemente zusammen mit den Bezeichnungen zurückgegeben werden sollen | return_ratings                          |

Der folgende Code zeigt, wie Parameter für ein SVD recommender-Modul festgelegt werden, wobei alle sechs Parameter verwendet werden, um bewertete Elemente mit angefügten vorhergesagten Bewertungen zu empfehlen.

```python
score_params = dict(
        learner=model,
        test_data=DataTable.from_dfd(data_frame_directory),
        training_data=None,
        # RecommenderPredictionKind has 2 members, 'RatingPrediction' and 'ItemRecommendation'. You
        # can specify prediction_kind parameter with one of them.
        prediction_kind=RecommenderPredictionKind.ItemRecommendation,
        # RecommendedItemSelection has 3 members, 'FromAllItems', 'FromRatedItems', 'FromUndatedItems'.
        # You can specify recommended_item_selection parameter with one of them.
        recommended_item_selection=RecommendedItemSelection.FromRatedItems,
        min_recommendation_pool_size=1,
        max_recommended_item_count=3,
        return_ratings=True,
    )
```


## <a name="next-steps"></a>Nächste Schritte

* [Trainieren eines Modells im Designer](tutorial-designer-automobile-price-train-score.md)
* [Bereitstellen von Modellen mit dem Azure Machine Learning SDK](how-to-deploy-and-where.md)
* [Problembehandlung bei einer fehlerhaften Bereitstellung](how-to-troubleshoot-deployment.md)
* [Bereitstellen für Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md)
* [Erstellen von Clientanwendungen zum Nutzen von Webdiensten](how-to-consume-web-service.md)
* [Aktualisieren des Webdiensts](how-to-deploy-update-web-service.md)
