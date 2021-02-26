---
title: Experimentnachverfolgung und Bereitstellen von Modellen
titleSuffix: Azure Data Science Virtual Machine
description: Erfahren Sie, wie Sie Experimente von der Data Science Virtual Machine mit Azure Machine Learning und/oder MLFlow nachverfolgen und protokollieren können.
services: machine-learning
ms.service: data-science-vm
author: samkemp
ms.author: samkemp
ms.topic: conceptual
ms.date: 07/17/2020
ms.openlocfilehash: 3795d531c5c4c543587ab817c05cd1cfeea6be06
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100518537"
---
# <a name="track-experiments-and-deploy-models-in-azure-machine-learning"></a>Nachverfolgen von Experimenten und Bereitstellen von Modellen in Azure Machine Learning

Verbessern Sie den Modellerstellungsvorgang, indem Sie Ihre Experimente nachverfolgen und Ausführungsmetriken überwachen. In diesem Artikel erfahren Sie, wie Sie Ihrem Trainingsskript mit der [MLflow](https://mlflow.org/)-API Protokollierungscode hinzufügen und das Experiment in Azure Machine Learning nachverfolgen können.

Im folgenden Diagramm ist dargestellt, wie Sie mit MLflow-Tracking die Ausführungsmetriken eines Experiments nachverfolgen und Modellartefakte in Ihrem Azure Machine Learning-Arbeitsbereich speichern.

![Nachverfolgen von Experimenten](./media/how-to-track-experiments/mlflow-diagram-track.png)

## <a name="prerequisites"></a>Voraussetzungen

* Sie müssen [einen Azure Machine Learning-Arbeitsbereich bereitstellen](../how-to-manage-workspace.md#create-a-workspace).

## <a name="create-a-new-notebook"></a>Erstellen eines neuen Notebooks

Das Azure Machine Learning und das MLFlow SDK sind auf der Data Science-VM vorinstalliert und sind in der **azureml_py36_\*** -Conda-Umgebung zugänglich. Klicken Sie in JupyterLab auf das Startprogramm, und wählen Sie den folgenden Kernel aus:

![Kernelauswahl](./media/how-to-track-experiments/experiment-tracking-1.png)

## <a name="set-up-the-workspace"></a>Arbeitsbereich einrichten

Navigieren Sie zum [Azure-Portal](https://portal.azure.com), und wählen Sie den Arbeitsbereich aus, den Sie als Teil der Voraussetzungen bereitgestellt haben. Es wird __„config.json“ herunterladen__ angezeigt (siehe unten): Laden Sie die Konfiguration herunter, und stellen Sie sicher, dass sie in Ihrem Arbeitsverzeichnis auf der DSVM gespeichert ist.

![Abrufen der Konfigurationsdatei](./media/how-to-track-experiments/experiment-tracking-2.png)

Die Konfiguration enthält Informationen wie z. B. den Namen des Arbeitsbereichs, das Abonnement usw. Dies bedeutet, dass Sie diese Parameter nicht hartcodieren müssen.

## <a name="track-dsvm-runs"></a>Nachverfolgen von DSVM-Ausführungen

Fügen Sie dem Notebook (oder Skript) den folgenden Code hinzu, um das AzureML-Arbeitsbereichsobjekt festzulegen.

```Python
import mlflow
from azureml.core import Workspace

ws = Workspace.from_config()

mlflow.set_tracking_uri(ws.get_mlflow_tracking_uri())
```

>[!NOTE]
Der Tracking-URI ist maximal eine Stunde lang gültig. Wenn Sie Ihr Skript nach einer Leerlaufzeit neu starten, sollten Sie die get_mlflow_tracking_uri-API verwenden, um einen neuen URI abzurufen.

### <a name="load-the-data"></a>Laden der Daten

In diesem Beispiel wird das Diabetes-Dataset verwendet – ein bekanntes kleines Dataset, das in Scikit-learn enthalten ist. Durch diese Zelle wird das Dataset geladen und nach dem Zufallsprinzip in Trainings- und Testsätze aufgeteilt.

```python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
from sklearn.externals import joblib

X, y = load_diabetes(return_X_y = True)
columns = ['age', 'gender', 'bmi', 'bp', 's1', 's2', 's3', 's4', 's5', 's6']
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=0)
data = {
    "train":{"X": X_train, "y": y_train},
    "test":{"X": X_test, "y": y_test}
}

print ("Data contains", len(data['train']['X']), "training samples and",len(data['test']['X']), "test samples")
```

### <a name="add-tracking"></a>Hinzufügen der Nachverfolgung

Fügen Sie die Experimentnachverfolgung mit dem Azure Machine Learning SDK hinzu und laden Sie ein persistentes Modell in die Ausführungsaufzeichnung des Experiments hoch. Der folgende Code fügt Protokolle hinzu und lädt eine Modelldatei in die Ausführung des Experiments hoch. Das Modell wird auch in der Azure Machine Learning-Modellregistrierung registriert.

```python
# Get an experiment object from Azure Machine Learning
from azureml.mlflow import register_model
experiment_name = 'experiment_with_mlflow'
mlflow.set_experiment(experiment_name)

with mlflow.start_run():
    # Log the algorithm parameter alpha to the run
    mlflow.log_param('alpha', 0.03)

    # Create, fit, and test the scikit-learn Ridge regression model
    regression_model = Ridge(alpha=0.03)
    regression_model.fit(data['train']['X'], data['train']['y'])
    preds = regression_model.predict(data['test']['X'])

    # Output the Mean Squared Error to the notebook and to the run
    print('Mean Squared Error is', mean_squared_error(data['test']['y'], preds))
    mlflow.log_metric('mse', mean_squared_error(data['test']['y'], preds))

    # Save the model
    model_file_name = 'model.pkl'
    joblib.dump(value = regression_model, filename = model_file_name)

    # upload the model file explicitly into artifacts
    mlflow.log_artifact(model_file_name)
    # register the model
    register_model(mlflow.active_run(), 'diabetes_model', 'model.pkl', model_framework="ScikitLearn")
```

### <a name="view-runs-in-azure-machine-learning"></a>Anzeigen von Ausführungen in Azure Machine Learning

Sie können die Ausführung des Experiments in [Azure Machine Learning Studio](https://ml.azure.com) anzeigen. Klicken Sie im Menü links auf __Experimente__ und wählen Sie „experiment_with_mlflow“ (wenn Sie sich entschieden haben, Ihr Experiment im Codeausschnitt oben anders zu benennen, klicken Sie auf den verwendeten Namen):

![Auswählen des Experiments](./media/how-to-track-experiments/mlflow-experiments.png)

Die protokollierte mittlere quadratische Abweichung (MSE) sollte angezeigt werden:

![Mittlere quadratische Abweichung (MSE)](./media/how-to-track-experiments/mlflow-experiments-2.png)

Wenn Sie auf die Ausführung klicken, werden weitere Details und auch das Pickle-Modell unter __Ausgaben und Protokolle__ angezeigt.

## <a name="deploy-model-in-azure-machine-learning"></a>Bereitstellen des Modells in Azure Machine Learning

In diesem Abschnitt zeigen wir, wie Modelle, die auf einer DSVM trainiert wurden, in Azure Machine Learning bereitgestellt werden können.

### <a name="step-1-create-inference-compute"></a>Schritt 1: Erstellen von Rückschlusscompute

Klicken Sie im Menü auf der linken Seite in [AzureML Studio](https://ml.azure.com) auf __Compute__ und dann auf die Registerkarte __Rückschlusscluster__. Klicken Sie als nächstes auf __+ Neu__, wie unten besprochen:

![Erstellen von Rückschlusscompute](./media/how-to-track-experiments/mlflow-experiments-6.png)

Geben Sie im Bereich __Neuer Rückschlusscluster__ Details für Folgendes ein:

* Computename
* Kubernetes Service. Wählen Sie „Neu erstellen“ aus.
* Wählen Sie die Region aus.
* Wählen Sie die VM-Größe aus (für die Zwecke dieses Tutorials ist die Standardgröße Standard_D3_v2 ausreichend).
* Clusterzweck: Wählen Sie __Dev-test__ aus.
* Die Anzahl der Knoten sollte gleich __1__ sein.
* Netzwerkkonfiguration: Standard

Klicken Sie dann auf __Erstellen__.

![Computedetails](./media/how-to-track-experiments/mlflow-experiments-7.png)

### <a name="step-2-deploy-no-code-inference-service"></a>Schritt 2: Bereitstellen eines Rückschlussdiensts ohne Code

Beim Registrieren des Modells in unserem Code mithilfe von `register_model` haben wir das Framework als sklearn angegeben. Azure Machine Learning unterstützt Bereitstellungen ohne Code für die folgenden Frameworks:

* scikit-learn
* TensorFlow SaveModel-Format
* ONNX-Modellformat

Eine Bereitstellung ohne Code bedeutet, dass Sie direkt aus dem Modellartefakt bereitstellen können, ohne ein bestimmtes Bewertungsskript angeben zu müssen.

Um das Diabetes-Modell bereitzustellen, navigieren Sie in [Azure Machine Learning Studio](https://ml.azure.com) zum linken Menü und wählen __Modelle__ aus. Klicken Sie anschließend auf das registrierte diabetes_model:

![Auswählen des Modells](./media/how-to-track-experiments/mlflow-experiments-3.png)

Klicken Sie anschließend im Modelldetailbereich auf die Schaltfläche __Bereitstellen__:

![Bereitstellen](./media/how-to-track-experiments/mlflow-experiments-4.png)

Wir stellen das Modell für den Rückschlusscluster (Azure Kubernetes Service) bereit, den wir in Schritt 1 erstellt haben. Geben Sie die folgenden Details ein, indem Sie einen Namen für den Dienst und den Namen des AKS-Computeclusters (in Schritt 1 erstellt) angeben. Außerdem wird empfohlen, die __CPU-Reservekapazität__ auf 1 (von 0,1) und die __Speicherreservekapazität__ auf 1 (von 0,5) zu erhöhen. Sie können dies durch Klicken auf __Erweitert__ und Ausfüllen der Details erhöhen. Klicken Sie auf __Bereitstellen__.

![Details zur Bereitstellung](./media/how-to-track-experiments/mlflow-experiments-5.png)

### <a name="step-3-consume"></a>Schritt 3: Nutzen

Wenn das Modell erfolgreich bereitgestellt wurde, sollten Sie Folgendes sehen (um zu dieser Seite zu gelangen, klicken Sie im linken Menü auf „Endpunkte“ und dann auf den Namen des bereitgestellten Diensts):

![Nutzen des Modells](./media/how-to-track-experiments/mlflow-experiments-8.png)

Sie sollten erkennen, dass sich der Bereitstellungszustand aus __Im Übergang__ in __Fehlerfrei__ ändert. Darüber hinaus stellt dieser Detailabschnitt den REST-Endpunkt und Swagger-URLs bereit, mit denen ein Anwendungsentwickler Ihr ML-Modell in seine Apps integrieren kann.

Sie können den Endpunkt mithilfe von [Postman](https://www.postman.com/) testen. Alternativ können Sie das AzureML SDK verwenden:

```python
from azureml.core import Webservice
import json

# if you called your service differently then change the name below
service = Webservice(ws, name="diabetes-service")

input_payload = json.dumps({
    'data': X_test[0:2].tolist(),
    'method': 'predict'  # If you have a classification model, you can get probabilities by changing this to 'predict_proba'.
})

output = service.run(input_payload)

print(output)
```

### <a name="step-4-clean-up"></a>Schritt 4: Bereinigung

Löschen Sie die in Schritt 1 erstellten Rückschlusscomputeressourcen, damit keine laufenden Computegebühren anfallen. Klicken Sie im Menü auf der linken Seite von Azure Machine Learning Studio auf „Compute > Rückschlusscluster“. Wählen Sie dann „Compute > Löschen“ aus.

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zum [Bereitstellen von Modellen in AzureML](../how-to-deploy-and-where.md)
