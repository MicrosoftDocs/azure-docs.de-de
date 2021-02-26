---
title: 'Tutorial: Erstellen des Vorhersagemodells mit einem Notebook (Teil 1 von 2)'
titleSuffix: Azure Machine Learning
description: Es wird beschrieben, wie Sie ein Machine Learning-Modell erstellen und bereitstellen, indem Sie Code in einem Jupyter Notebook verwenden. Erstellen Sie außerdem ein Bewertungsskript, das die Eingabe und Ausgabe für eine einfache Integration in Microsoft Power BI definiert.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: samkemp
author: samuel100
ms.reviewer: sdgilley
ms.date: 12/11/2020
ms.openlocfilehash: 00d5fa43245fb25b8ee99a0523d680bef891b71e
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100387001"
---
# <a name="tutorial-power-bi-integration---create-the-predictive-model-with-a-jupyter-notebook-part-1-of-2"></a>Tutorial: Power BI-Integration: Erstellen des Vorhersagemodells mit einer Jupyter Notebook-Instanz (Teil 1 von 2)

In Teil 1 dieses Tutorials wird ein Vorhersagemodell für maschinelles Lernen mithilfe von Code in einem Jupyter Notebook trainiert und bereitgestellt. Außerdem erstellen Sie ein Bewertungsskript, um das Eingabe- und Ausgabeschema des Modells für die Integration in Power BI zu definieren.  In Teil 2 verwenden Sie das Modell, um Ergebnisse in Microsoft Power BI vorherzusagen.

In diesem Tutorial führen Sie Folgendes durch:

> [!div class="checklist"]
> * Erstellen eines Notebooks für Jupyter Notebook
> * Erstellen einer Compute-Instanz von Azure Machine Learning
> * Trainieren eines Regressionsmodells mithilfe von scikit-learn
> * Schreiben Sie ein Bewertungsskript, das die Eingabe und Ausgabe für eine einfache Integration in Microsoft Power BI definiert.
> * Bereitstellen des Modells auf einem Echtzeit-Bewertungsendpunkt

Es gibt drei Möglichkeiten, wie Sie das zu verwendende Modell in Power BI erstellen und bereitstellen können.  In diesem Artikel wird Option A behandelt: Trainieren und Bereitstellen mithilfe von Notebooks.  Bei dieser Option handelt es sich um eine codebasierte Erstellung. Es werden in Azure Machine Learning Studio gehostete Jupyter Notebooks verwendet. 

Stattdessen können Sie aber auch eine der anderen Optionen nutzen:

* [Option B: Trainieren und Bereitstellen von Modellen – Verwenden des Designers](tutorial-power-bi-designer-model.md). Bei dieser Erstellung mit geringem Codeaufwand wird eine Drag & Drop-Benutzeroberfläche verwendet.
* [Option C: Trainieren und Bereitstellen von Modellen mit automatisiertem maschinellem Lernen](tutorial-power-bi-automated-model.md). Bei dieser Erstellung ohne Code werden die Datenaufbereitung und das Modelltraining vollständig automatisiert.


## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Abonnement. Falls Sie noch nicht über ein Abonnement verfügen, können Sie eine [kostenlose Testversion](https://aka.ms/AMLFree) nutzen. 
- Ein Azure Machine Learning-Arbeitsbereich. Falls Sie noch nicht über einen Arbeitsbereich verfügen, helfen Ihnen die Informationen unter [Erstellen und Verwalten von Azure Machine Learning-Arbeitsbereichen](./how-to-manage-workspace.md#create-a-workspace) weiter.
- Kenntnisse der Python-Sprache und der Machine Learning-Workflows auf Einstiegsniveau.

## <a name="create-a-notebook-and-compute"></a>Erstellen eines Notebooks und einer Compute-Instanz

Wählen Sie auf der Startseite von [**Azure Machine Learning Studio**](https://ml.azure.com) die Option **Neu erstellen** > **Notebook** aus:

:::image type="content" source="media/tutorial-power-bi/create-new-notebook.png" alt-text="Screenshot: Erstellen eines Notebooks":::
 
Gehen Sie auf der Seite **Neue Datei erstellen** wie folgt vor:

1. Geben Sie Ihrem Notebook einen Namen (z. B. *my_model_notebook*).
1. Ändern Sie den **Dateityp** in **Notebook**.
1. Klicken Sie auf **Erstellen**. 
 
Erstellen Sie als Nächstes zum Ausführen von Codezellen eine Compute-Instanz, und fügen Sie sie an Ihr Notebook an. Wählen Sie hierfür zunächst oben im Notebook das Pluszeichen-Symbol aus:

:::image type="content" source="media/tutorial-power-bi/create-compute.png" alt-text="Screenshot: Erstellen einer Compute-Instanz":::

Gehen Sie auf der Seite **Compute-Instanz erstellen** wie folgt vor:

1. Wählen Sie eine CPU-VM-Größe aus. Für dieses Tutorial können Sie **Standard_D11_v2** mit zwei Kernen und 14 GB RAM auswählen.
1. Wählen Sie **Weiter** aus. 
1. Geben Sie auf der Seite **Einstellungen konfigurieren** unter **Computename** einen gültigen Namen an. Gültige Zeichen sind Groß- und Kleinbuchstaben, Ziffern und Bindestriche (-).
1. Klicken Sie auf **Erstellen**.

Sie können erkennen, dass im Notebook der Kreis neben **Compute** nun in der Farbe Blaugrün angezeigt wird. Mit dieser farblichen Änderung wird angegeben, dass die Compute-Instanz erstellt wird:

:::image type="content" source="media/tutorial-power-bi/creating.png" alt-text="Screenshot: Erstellung einer Compute-Instanz":::

> [!NOTE]
> Die Bereitstellung der Compute-Instanz kann zwei bis vier Minuten dauern.

Nachdem die Compute-Instanz bereitgestellt wurde, können Sie das Notebook verwenden, um Codezellen auszuführen. In der Zelle können Sie beispielsweise den folgenden Code eingeben:

```python
import numpy as np

np.sin(3)
```

Drücken Sie anschließend UMSCHALT+EINGABE (oder STRG+EINGABE, oder wählen Sie die **Wiedergabeschaltfläche** neben der Zelle aus). Die folgende Ausgabe sollte angezeigt werden:

:::image type="content" source="media/tutorial-power-bi/simple-sin.png" alt-text="Screenshot: Ausgabe einer Zelle":::

Sie können nun ein Machine Learning-Modell erstellen.

## <a name="build-a-model-by-using-scikit-learn"></a>Erstellen eines Modells mithilfe von scikit-learn

In diesem Tutorial verwenden Sie das [Diabetes-Dataset](https://www4.stat.ncsu.edu/~boos/var.select/diabetes.html). Dieses Dataset ist in [Azure Open Datasets](https://azure.microsoft.com/services/open-datasets/) verfügbar.


### <a name="import-data"></a>Daten importieren

Kopieren Sie den folgenden Code, und fügen Sie ihn in eine neue *Codezelle* Ihres Notebooks ein, um Ihre Daten zu importieren.

```python
from azureml.opendatasets import Diabetes

diabetes = Diabetes.get_tabular_dataset()
X = diabetes.drop_columns("Y")
y = diabetes.keep_columns("Y")
X_df = X.to_pandas_dataframe()
y_df = y.to_pandas_dataframe()
X_df.info()
```

Der Pandas-Datenrahmen `X_df` enthält zehn grundlegende Eingabevariablen. Beispiele für diese Variablen sind Alter, Geschlecht, BMI, durchschnittliche Blutdruckwerte und sechs auf das Blutserum bezogene Werte. Der Pandas-Datenrahmen `y_df` ist die Zielvariable. Sie enthält ein quantitatives Maß für den Fortschritt der Diabetes-Erkrankung ein Jahr nach der Ermittlung der Grundwerte. Der Datenrahmen enthält 442 Datensätze.

### <a name="train-the-model"></a>Trainieren des Modells

Erstellen Sie eine neue *Codezelle* in Ihrem Notebook. Kopieren Sie anschließend den folgenden Code, und fügen Sie ihn in die Zelle ein. Mit diesem Codeausschnitt wird ein Ridge-Regressionsmodell erstellt und serialisiert, indem das Python-Pickle-Format verwendet wird.

```python
import joblib
from sklearn.linear_model import Ridge

model = Ridge().fit(X_df,y_df)
joblib.dump(model, 'sklearn_regression_model.pkl')
```

### <a name="register-the-model"></a>Registrieren des Modells

Zusätzlich zum Inhalt der Modelldatei selbst werden von Ihrem registrierten Modell auch Metadaten gespeichert. Die Metadaten enthalten die Modellbeschreibung, Tags und Frameworkinformationen. 

Metadaten sind hilfreich beim Verwalten und Bereitstellen von Modellen in Ihrem Arbeitsbereich. Mithilfe von Tags können Sie Ihre Modelle beispielsweise in Kategorien einteilen und beim Auflisten von Modellen in Ihrem Arbeitsbereich Filter anwenden. Wenn Sie dieses Modell mit dem scikit-learn-Framework markieren, vereinfachen Sie die Bereitstellung als Webdienst.

Kopieren Sie den folgenden Code, und fügen Sie ihn dann in eine neue *Codezelle* Ihres Notebooks ein.

```python
import sklearn

from azureml.core import Workspace
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

ws = Workspace.from_config()

model = Model.register(workspace=ws,
                       model_name='my-sklearn-model',                # Name of the registered model in your workspace.
                       model_path='./sklearn_regression_model.pkl',  # Local file to upload and register as a model.
                       model_framework=Model.Framework.SCIKITLEARN,  # Framework used to create the model.
                       model_framework_version=sklearn.__version__,  # Version of scikit-learn used to create the model.
                       sample_input_dataset=X,
                       sample_output_dataset=y,
                       resource_configuration=ResourceConfiguration(cpu=2, memory_in_gb=4),
                       description='Ridge regression model to predict diabetes progression.',
                       tags={'area': 'diabetes', 'type': 'regression'})

print('Name:', model.name)
print('Version:', model.version)
```

Sie können das Modell auch in Azure Machine Learning Studio anzeigen. Wählen Sie im Menü auf der linken Seite die Option **Modelle** aus:

:::image type="content" source="media/tutorial-power-bi/model.png" alt-text="Screenshot: Anzeigen eines Modells":::

## <a name="define-the-scoring-script"></a>Definieren des Bewertungsskripts

Wenn Sie ein Modell bereitstellen, das in Power BI integriert werden soll, müssen Sie ein Python-*Bewertungsskript* und eine benutzerdefinierte Umgebung definieren. Das Bewertungsskript enthält zwei Funktionen:

- Die Funktion `init()` wird ausgeführt, wenn der Dienst gestartet wird. Sie bewirkt, dass das Modell geladen (automatischer Download aus der Modellregistrierung) und deserialisiert wird.
- Die Funktion `run(data)` wird ausgeführt, wenn ein Aufruf des Diensts Eingabedaten enthält, die bewertet werden müssen. 

>[!NOTE]
> Die Decorator-Elemente von Python im folgenden Code definieren das Schema der Eingabe- und Ausgabedaten. Dies ist für die Integration in Power BI wichtig.

Kopieren Sie den folgenden Code, und fügen Sie ihn in eine neue *Codezelle* Ihres Notebooks ein. Der folgende Codeausschnitt enthält Zellen-Magic-Befehle, mit denen der Code in eine Datei mit dem Namen *score.py* geschrieben wird.

```python
%%writefile score.py

import json
import pickle
import numpy as np
import pandas as pd
import os
import joblib
from azureml.core.model import Model

from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType
from inference_schema.parameter_types.pandas_parameter_type import PandasParameterType


def init():
    global model
    # Replace filename if needed.
    path = os.getenv('AZUREML_MODEL_DIR') 
    model_path = os.path.join(path, 'sklearn_regression_model.pkl')
    # Deserialize the model file back into a sklearn model.
    model = joblib.load(model_path)


input_sample = pd.DataFrame(data=[{
    "AGE": 5,
    "SEX": 2,
    "BMI": 3.1,
    "BP": 3.1,
    "S1": 3.1,
    "S2": 3.1,
    "S3": 3.1,
    "S4": 3.1,
    "S5": 3.1,
    "S6": 3.1
}])

# This is an integer type sample. Use the data type that reflects the expected result.
output_sample = np.array([0])

# To indicate that we support a variable length of data input,
# set enforce_shape=False
@input_schema('data', PandasParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(data):
    try:
        print("input_data....")
        print(data.columns)
        print(type(data))
        result = model.predict(data)
        print("result.....")
        print(result)
    # You can return any data type, as long as it can be serialized by JSON.
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

### <a name="define-the-custom-environment"></a>Definieren der benutzerdefinierten Umgebung

Definieren Sie als Nächstes die Umgebung zum Bewerten des Modells. Definieren Sie in der Umgebung die Python-Pakete, z. B. „pandas“ und „scikit-learn“, die für das Bewertungsskript (*score.py*) erforderlich sind.

Kopieren Sie den folgenden Code, und fügen Sie ihn in eine neue *Codezelle* Ihres Notebooks ein, um die Umgebung zu definieren.

```python
from azureml.core.model import InferenceConfig
from azureml.core import Environment
from azureml.core.conda_dependencies import CondaDependencies

environment = Environment('my-sklearn-environment')
environment.python.conda_dependencies = CondaDependencies.create(pip_packages=[
    'azureml-defaults',
    'inference-schema[numpy-support]',
    'joblib',
    'numpy',
    'pandas',
    'scikit-learn=={}'.format(sklearn.__version__)
])

inference_config = InferenceConfig(entry_script='./score.py',environment=environment)
```

### <a name="deploy-the-model"></a>Bereitstellen des Modells

Kopieren Sie den folgenden Code, und fügen Sie ihn in eine neue *Codezelle* Ihres Notebooks ein, um das Modell bereitzustellen:

```python
service_name = 'my-diabetes-model'

service = Model.deploy(ws, service_name, [model], inference_config, overwrite=True)
service.wait_for_deployment(show_output=True)
```

>[!NOTE]
> Die Bereitstellung des Diensts kann zwei bis vier Minuten dauern.

Wenn die Bereitstellung des Diensts erfolgreich war, sollte die folgende Ausgabe angezeigt werden:

```txt
Tips: You can try get_logs(): https://aka.ms/debugimage#dockerlog or local deployment: https://aka.ms/debugimage#debug-locally to debug if deployment takes longer than 10 minutes.
Running......................................................................................
Succeeded
ACI service creation operation finished, operation "Succeeded"
```

Sie können den Dienst auch in Azure Machine Learning Studio anzeigen. Wählen Sie im Menü auf der linken Seite die Option **Endpunkte** aus:

:::image type="content" source="media/tutorial-power-bi/endpoint.png" alt-text="Screenshot: Anzeigen des Diensts":::

Wir empfehlen Ihnen, den Webdienst zu testen, um sicherzustellen, dass er wie erwartet funktioniert. Wählen Sie zum Zurückgeben Ihres Notebooks in Azure Machine Learning Studio im Menü auf der linken Seite die Option **Notebooks** aus. Kopieren Sie den folgenden Code, und fügen Sie ihn in eine neue *Codezelle* Ihres Notebooks ein, um den Dienst zu testen.

```python
import json

input_payload = json.dumps({
    'data': X_df[0:2].values.tolist()
})

output = service.run(input_payload)

print(output)
```

Die Ausgabe sollte wie die folgende JSON-Struktur aussehen: `{'predict': [[205.59], [68.84]]}`.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial wurde beschrieben, wie Sie ein Modell erstellen und bereitstellen, damit es von Power BI verwendet werden kann. Im nächsten Teil erfahren Sie, wie Sie dieses Modell in einem Power BI-Bericht nutzen.

> [!div class="nextstepaction"]
> [Tutorial: Verwenden von Azure Machine Learning-Modellen in Power BI](/power-bi/connect-data/service-aml-integrate?context=azure/machine-learning/context/ml-context)
