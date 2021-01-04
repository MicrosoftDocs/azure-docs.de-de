---
title: Erstellen automatisierter ML-Experimente
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie Datenquellen-, Compute- und Konfigurationseinstellungen für Ihre Experimente für automatisiertes maschinelles Lernen definieren.
author: cartacioS
ms.author: sacartac
ms.reviewer: nibaccam
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.date: 09/29/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python,contperf-fy21q1, automl
ms.openlocfilehash: 6aa54f65b504e61a5e74ed584c5dad51e49eb087
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97031452"
---
# <a name="configure-automated-ml-experiments-in-python"></a>Konfigurieren automatisierter ML-Experimente in Python


In diesem Leitfaden erfahren Sie, wie Sie verschiedene Konfigurationseinstellungen der automatisierten Machine Learning-Experimente mit dem [Azure Machine Learning-SDK](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py) definieren. Das automatisierte Machine Learning wählt einen Algorithmus und Hyperparameter für Sie aus und generiert ein Modell, das bereitgestellt werden kann. Es gibt verschiedene Optionen, mit denen Sie automatisierte Machine Learning-Experimente konfigurieren können.

Beispiele für automatisierte Machine-Learning-Experimente finden Sie im [Tutorial: Trainieren eines Klassifizierungsmodells mit automatisiertem maschinellem Lernen](tutorial-auto-train-models.md) und zum [Trainieren von Modellen mit automatisiertem maschinellem Lernen in der Cloud](how-to-auto-train-remote.md).

Für das automatisierte maschinelle Lernen sind folgende Konfigurationsoptionen verfügbar:

* Wählen Sie die Experimentart aus: Klassifizierung, Regression oder Zeitreihenvorhersage
* Datenquelle, Datenformate und Abrufen von Daten
* Wählen Sie das Computeziel aus: lokal oder remote.
* Einstellungen für automatisierte Machine Learning-Experimente
* Ausführen eines automatisierten Machine Learning-Experiments
* Untersuchen von Modellmetriken
* Registrieren und Bereitstellen von Modellen

Wenn Sie lieber ohne Code arbeiten, informieren Sie sich unter [Erstellen, Untersuchen und Bereitstellen automatisierter Experimente mit maschinellem Lernen in Azure Machine Learning-Studio](how-to-use-automated-ml-for-ml-models.md).

## <a name="prerequisites"></a>Voraussetzungen

Für diesen Artikel ist Folgendes erforderlich: 
* Ein Azure Machine Learning-Arbeitsbereich. Informationen zum Erstellen des Arbeitsbereichs finden Sie unter [Erstellen eines Azure Machine Learning-Arbeitsbereichs](how-to-manage-workspace.md).

* Installation des Azure Machine Learning Python SDK,
    zum Installieren des SDK können Sie wie folgt vorgehen: 
    * Erstellen Sie eine Compute-Instanz, wodurch das SDK automatisch installiert und für ML-Workflows vorkonfiguriert wird. Weitere Informationen hierzu finden Sie unter [Erstellen und Verwalten einer Azure Machine Learning-Computeinstanz](how-to-create-manage-compute-instance.md). 

    * [Installieren Sie das Paket `automl` selbst](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/README.md#setup-using-a-local-conda-environment). Es enthält die [Standardinstallation](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py#default-install) des SDK.

## <a name="select-your-experiment-type"></a>Auswählen der Experimentart

Legen Sie vor Experimentbeginn fest, welche Art von Problem des maschinellen Lernens Sie lösen möchten. Das automatisierte maschinelle Lernen unterstützt die Aufgabentypen `classification`, `regression` und `forecasting`. Weitere Informationen zu [Aufgabentypen](concept-automated-ml.md#when-to-use-automl-classify-regression--forecast)

Der folgende Code nutzt den Parameter `task` im Konstruktor `AutoMLConfig`, um den Experimenttyp `classification` anzugeben.

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task = "classification")
```

## <a name="data-source-and-format"></a>Datenquelle und Datenformat

Das automatisierte Machine Learning unterstützt Daten, die sich auf dem lokalen Desktop oder in der Cloud (z. B. in Azure Blob Storage) befinden. Die Daten können in einen **Pandas DataFrame** oder ein **Azure Machine Learning-TabularDataset** eingelesen werden. [Erfahren Sie mehr über Datasets](how-to-create-register-datasets.md).

Anforderungen für Trainingsdaten:
- Die Daten müssen in Tabellenform vorliegen.
- Der Wert, der vorhergesagt werden soll (Zielspalte), muss in den Daten vorhanden sein.

**Für Remoteexperimente** müssen Trainingsdaten über die Remotecomputeressource zugänglich sein. AutoML akzeptiert nur [TabularDatasets von Azure Machine Learning](/python/api/azureml-core/azureml.data.tabulardataset?preserve-view=true&view=azure-ml-py), wenn Sie auf einer Remotecomputeressource arbeiten. 

Azure Machine Learning-Datasets stellen Funktionen für folgende Zwecke bereit:

* Einfacher Datentransfer von statischen Dateien oder URL-Quellen in Ihren Arbeitsbereich
* Bereitstellen Ihrer Daten für Trainingsskripts bei Ausführung auf Cloudcomputeressourcen (Ein Beispiel für die Verwendung der `Dataset`-Klasse zum Einbinden von Daten in Ihr Remotecomputeziel finden Sie unter [Trainieren mit Datasets](how-to-train-with-datasets.md#mount-files-to-remote-compute-targets).)

Mit dem folgenden Code wird ein TabularDataset-Element aus einer Web-URL erstellt. Codebeispiele zum Erstellen von Datasets aus anderen Quellen wie lokalen Dateien und Datenspeichern finden Sie unter [Erstellen eines TabularDatasets-Element](how-to-create-register-datasets.md#create-a-tabulardataset).

```python
from azureml.core.dataset import Dataset
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"
dataset = Dataset.Tabular.from_delimited_files(data)
  ```
**Für lokale Computeexperimente** werden DataFrames-Objekte von Pandas für schnellere Verarbeitungszeiten empfohlen.

  ```python
  import pandas as pd
  from sklearn.model_selection import train_test_split

  df = pd.read_csv("your-local-file.csv")
  train_data, test_data = train_test_split(df, test_size=0.1, random_state=42)
  label = "label-col-name"
  ```

## <a name="training-validation-and-test-data"></a>Trainieren, Überprüfen und Testen von Daten

Sie können separate **Datasets für das Training und die Überprüfung** direkt im `AutoMLConfig`-Konstruktor angeben. Erfahren Sie mehr über das [Konfigurieren von Datenaufteilungen und Kreuzvalidierung](how-to-configure-cross-validation-data-splits.md) für Ihre AutoML-Experimente. 

Wenn Sie keinen `validation_data`- oder `n_cross_validation`-Parameter explizit angeben, wendet AutoML Standardverfahren an, um zu bestimmen, wie die Überprüfung durchgeführt wird. Diese Bestimmung hängt von der Anzahl der Zeilen im Dataset ab, das Ihrem `training_data`-Parameter zugewiesen wird. 

|Größe der Trainings&nbsp;daten&nbsp;| Validierungsverfahren |
|---|-----|
|**Größer&nbsp;als&nbsp;20.000&nbsp;Zeilen**| Es wird eine Aufteilung in Trainings- und Validierungsdaten vorgenommen. Standardmäßig werden 10 % des ursprünglichen Trainingsdatasets als Validierungsset verwendet. Dieses Validierungsset wird seinerseits für die Metrikberechnung verwendet.
|**Kleiner&nbsp;als&nbsp;20.000&nbsp;Zeilen**| Der Kreuzvalidierungsansatz wird angewendet. Die Standardanzahl der Faltungen (Folds) hängt von der Zeilenanzahl ab. <br> **Wenn das Dataset weniger als 1.000 Zeilen aufweist**, werden 10 Faltungen verwendet. <br> **Wenn die Anzahl der Zeilen zwischen 1.000 und 20.000 liegt**, werden drei Faltungen verwendet.

Zu diesem Zeitpunkt müssen Sie Ihre eigenen **Testdaten** für die Modellauswertung angeben. Ein Codebeispiel, in dem Sie Ihre eigenen Testdaten für die Modellauswertung verwenden, finden Sie im Abschnitt **Testen** von [diesem Jupyter Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-credit-card-fraud/auto-ml-classification-credit-card-fraud.ipynb).

## <a name="compute-to-run-experiment"></a>Computeziel zum Ausführen des Experiments

Legen Sie als Nächstes die Instanz fest, auf der das Modell trainiert werden soll. Ein Trainingsexperiment für automatisiertes maschinelles Lernen kann unter folgenden Computeoptionen ausgeführt werden: Informieren Sie sich über die [Vor- und Nachteile der lokalen und Remotecomputeoptionen](concept-automated-ml.md#local-remote). 

* Ihr **lokaler** Computer (z. B. lokaler Desktop oder Laptop): Diese Option wird in der Regel für kleine Datasets und während der Untersuchungsphase verwendet. Ein Beispiel mit einem lokalen Computeziel finden Sie in [diesem Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/local-run-classification-credit-card-fraud/auto-ml-classification-credit-card-fraud-local.ipynb). 
 
* Ein **Remotecomputer** in der Cloud: [Azure Machine Learning Managed Compute](concept-compute-target.md#amlcompute) ist ein verwalteter Dienst, mit dem Machine Learning-Modelle in Clustern virtueller Azure-Computer trainiert werden können. 

    Ein Remotebeispiel mit Azure Machine Learning Managed Compute finden Sie in [diesem Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb). 

* Ein **Azure Databricks-Cluster** im Azure-Abonnement. Weitere Informationen finden Sie unter [Einrichten eines Azure Databricks-Clusters für automatisiertes maschinelles Lernen](how-to-configure-databricks-automl-environment.md). Beispielnotebooks mit Azure Databricks finden Sie auf der [GitHub-Website](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/azure-databricks/automl).

<a name='configure-experiment'></a>

## <a name="configure-your-experiment-settings"></a>Konfigurieren der Experimenteinstellungen

Es gibt verschiedene Optionen für das Konfigurieren Ihrer automatisierten Machine Learning-Experimente. Diese Parameter werden beim Instanziieren eines `AutoMLConfig`-Objekts festgelegt. Unter [AutoMLConfig-Klasse](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) finden Sie eine vollständige Liste der Parameter.

Beispiele hierfür sind:

1. Ein Klassifizierungsexperiment, in dem AUC gewichtet als primäre Metrik verwendet wird, wobei das Experimentzeitlimit auf 30 Minuten festgelegt ist und 2 Kreuzvalidierungfolds verwendet werden.

   ```python
       automl_classifier=AutoMLConfig(task='classification',
                                      primary_metric='AUC_weighted',
                                      experiment_timeout_minutes=30,
                                      blocked_models=['XGBoostClassifier'],
                                      training_data=train_data,
                                      label_column_name=label,
                                      n_cross_validations=2)
   ```
1. Beim folgenden Beispiel handelt es sich um ein Regressionsexperiment, das nach 60 Minuten mit fünf Kreuzvalidierungsfolds endet.

   ```python
      automl_regressor = AutoMLConfig(task='regression',
                                      experiment_timeout_minutes=60,
                                      allowed_models=['KNN'],
                                      primary_metric='r2_score',
                                      training_data=train_data,
                                      label_column_name=label,
                                      n_cross_validations=5)
   ```


1. Für Prognoseaufgaben ist ein zusätzliches Setup erforderlich. Weitere Informationen finden Sie im Artikel [Automatisches Trainieren eines Modells für die Zeitreihenprognose](how-to-auto-train-forecast.md). 

    ```python
    time_series_settings = {
        'time_column_name': time_column_name,
        'time_series_id_column_names': time_series_id_column_names,
        'drop_column_names': ['logQuantity'],
        'forecast_horizon': n_test_periods
    }
    
    automl_config = AutoMLConfig(task = 'forecasting',
                                 debug_log='automl_oj_sales_errors.log',
                                 primary_metric='normalized_root_mean_squared_error',
                                 experiment_timeout_minutes=20,
                                 training_data=train_data,
                                 label_column_name=label,
                                 n_cross_validations=5,
                                 path=project_folder,
                                 verbosity=logging.INFO,
                                 **time_series_settings)
    ```
    
### <a name="supported-models"></a>Unterstützte Modelle

Beim automatisierten maschinellen Lernen werden während des Automatisierungs- und Optimierungsprozesses verschiedene Modelle und Algorithmen getestet. Als Benutzer müssen Sie den Algorithmus nicht angeben. 

Die drei verschiedenen Werte des Parameters `task` bestimmen die Liste der anzuwendenden Algorithmen oder Modelle. Verwenden Sie die Parameter `allowed_models` oder `blocked_models`, um Iterationen mit den verfügbaren Modellen, die eingeschlossen oder ausgeschlossen werden sollen, weiter zu ändern. 

In der folgenden Tabelle werden die unterstützten Modelle anhand des Tasktyps zusammengefasst. 

> [!NOTE]
> Wenn Sie vorhaben, Ihre mit ML automatisch erstellten Modelle in ein [ONNX-Modell](concept-onnx.md) zu exportieren, beachten Sie, dass nur Algorithmen, die mit einem Sternchen (*) angegeben sind, in das ONNX-Format konvertiert werden können. Erfahren Sie mehr über das [Konvertieren von Modellen zu ONNX](concept-automated-ml.md#use-with-onnx). <br> <br> Beachten Sie auch, dass ONNX zurzeit nur Klassifizierungs- und Regressionsaufgaben unterstützt. 

Klassifizierung | Regression | Zeitreihe und Vorhersage
|-- |-- |--
[Logistische Regression](https://scikit-learn.org/stable/modules/linear_model.html#logistic-regression)* | [Elastisches Netz](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)* | [Elastisches Netz](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)
[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)* |[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)*|[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)
[Gradient Boosting](https://scikit-learn.org/stable/modules/ensemble.html#classification)* |[Gradient Boosting](https://scikit-learn.org/stable/modules/ensemble.html#regression)* |[Gradient Boosting](https://scikit-learn.org/stable/modules/ensemble.html#regression)
[Entscheidungsstruktur](https://scikit-learn.org/stable/modules/tree.html#decision-trees)* |[Entscheidungsstruktur](https://scikit-learn.org/stable/modules/tree.html#regression)* |[Entscheidungsstruktur](https://scikit-learn.org/stable/modules/tree.html#regression)
[K nächste Nachbarn](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)* |[K nächste Nachbarn](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)* |[K nächste Nachbarn](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)
[Lineare SVC](https://scikit-learn.org/stable/modules/svm.html#classification)* |[LARS-Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)* |[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)
[Support Vector Classification (SVC)](https://scikit-learn.org/stable/modules/svm.html#classification)* |[Stochastisches Gradientenabstiegsverfahren (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)* |[Stochastisches Gradientenabstiegsverfahren (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)
[Zufällige Gesamtstruktur](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)* |[Zufällige Gesamtstruktur](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)* |[Random Forest](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)
[Extremely Randomized Trees](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)* |[Extremely Randomized Trees](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)* |[Extremely Randomized Trees](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)
[XGBoost](https://xgboost.readthedocs.io/en/latest/parameter.html)* |[XGBoost](https://xgboost.readthedocs.io/en/latest/parameter.html)* | [Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)
[Averaged Perceptron Classifier](/python/api/nimbusml/nimbusml.linear_model.averagedperceptronbinaryclassifier?preserve-view=true&view=nimbusml-py-latest)|[Online Gradient Descent Regressor](/python/api/nimbusml/nimbusml.linear_model.onlinegradientdescentregressor?preserve-view=true&view=nimbusml-py-latest) |[Auto-ARIMA](https://www.alkaline-ml.com/pmdarima/modules/generated/pmdarima.arima.auto_arima.html#pmdarima.arima.auto_arima)
[Naive Bayes](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)* |[Schneller linearer Regressor](/python/api/nimbusml/nimbusml.linear_model.fastlinearregressor?preserve-view=true&view=nimbusml-py-latest)|[Prophet](https://facebook.github.io/prophet/docs/quick_start.html)
[Stochastisches Gradientenabstiegsverfahren (SGD)](https://scikit-learn.org/stable/modules/sgd.html#sgd)* ||ForecastTCN
|[Linearer SVM-Klassifizierer](/python/api/nimbusml/nimbusml.linear_model.linearsvmbinaryclassifier?preserve-view=true&view=nimbusml-py-latest)*||

### <a name="primary-metric"></a>Primäre Metrik
Der Parameter `primary metric` bestimmt die Metrik, die während des Modelltrainings für die Optimierung verwendet werden soll. Die verfügbaren Metriken, die Sie auswählen können, werden vom ausgewählten Tasktyp bestimmt. In der folgenden Tabelle werden gültige primäre Metriken für jeden Tasktyp aufgeführt.

Informationen zu den speziellen Definitionen dieser Metriken finden Sie unter [Grundlagen von Ergebnissen des automatisierten maschinellen Lernens](how-to-understand-automated-ml.md).

|Klassifizierung | Regression | Zeitreihe und Vorhersage
|--|--|--
|accuracy| spearman_correlation | spearman_correlation
|AUC_weighted | normalized_root_mean_squared_error | normalized_root_mean_squared_error
|average_precision_score_weighted | r2_score | r2_score
|norm_macro_recall | normalized_mean_absolute_error | normalized_mean_absolute_error
|precision_score_weighted |

### <a name="data-featurization"></a>Merkmalerstellung für Daten

In jedem automatisierten Machine Learning-Experiment werden Ihre Daten automatisch skaliert und normalisiert, um *bestimmte* Algorithmen zu unterstützen, die auf Funktionen mit unterschiedlichen Skalierungen sensibel reagieren. Diese Skalierung und Normalisierung wird als Featurisierung bezeichnet. Weitere Informationen und Codebeispiele finden Sie unter [Featurisierung in AutoML](how-to-configure-auto-features.md#). 

Beim Konfigurieren Ihrer Experimente im Objekt `AutoMLConfig` können Sie die Einstellung `featurization` aktivieren/deaktivieren. In der folgenden Tabelle werden die akzeptierten Einstellungen für die Featurisierung im [AutoMLConfig-Objekt](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) aufgeführt. 

|Konfiguration der Featurebereitstellung | BESCHREIBUNG |
| ------------- | ------------- |
|`"featurization": 'auto'`| Gibt an, dass im Rahmen der Vorverarbeitung die folgenden [Schritte für Datenschutzmaßnahmen und Featurebereitstellung](how-to-configure-auto-features.md#featurization) automatisch durchgeführt werden. **Standardeinstellung**.|
|`"featurization": 'off'`| Gibt an, dass der Featurisierungsschritt nicht automatisch durchgeführt werden soll|
|`"featurization":`&nbsp;`'FeaturizationConfig'`| Gibt an, dass ein angepasster Featurebereitstellungsschritt verwendet werden soll. [Erfahren Sie, wie Sie die Featurebereitstellung anpassen](how-to-configure-auto-features.md#customize-featurization).|

> [!NOTE]
> Die Schritte zur Featurebereitstellung bei automatisiertem maschinellen Lernen (Featurenormalisierung, Behandlung fehlender Daten, Umwandlung von Text in numerische Daten usw.) werden Teil des zugrunde liegenden Modells. Bei Verwendung des Modells für Vorhersagen werden die während des Trainings angewendeten Schritte zur Featurebereitstellung automatisch auf Ihre Eingabedaten angewendet.

<a name="ensemble"></a>

### <a name="ensemble-configuration"></a> Ensemble-Konfiguration

Ensemble-Modelle sind standardmäßig aktiviert und treten als abschließende Ausführungsiterationen in einer AutoML-Ausführung auf. Derzeit werden **VotingEnsemble** und **StackEnsemble** unterstützt. 

Mit VotingEnsemble wird soft-voting implementiert, wobei gewichtete Durchschnittswerte verwendet werden. Bei der StackEnsemble-Implementierung wird eine Implementierung auf zwei Ebenen verwendet, bei der die erste Ebene dieselben Modelle wie VotingEnsemble besitzt, und das Modell der zweiten Ebene verwendet wird, um die optimale Kombination der Modelle aus der ersten Ebene zu ermitteln. 

Wenn Sie ONNX-Modelle verwenden **oder** die Modellerklärung aktiviert haben, wird StackEnsemble deaktiviert und nur VotingEnsemble wird verwendet.

Das Ensemble-Training kann mithilfe der booleschen Parameter `enable_voting_ensemble` und `enable_stack_ensemble`deaktiviert werden.

```python
automl_classifier = AutoMLConfig(
        task='classification',
        primary_metric='AUC_weighted',
        experiment_timeout_minutes=30,
        training_data=data_train,
        label_column_name=label,
        n_cross_validations=5,
        enable_voting_ensemble=False,
        enable_stack_ensemble=False
        )
```

Es gibt mehrere Standardargumente, die als `kwargs` in einem `AutoMLConfig`-Objekt bereitgestellt werden können, um das Ensemble-Standardverhalten zu verändern.

> [!IMPORTANT]
>  Die folgenden Parameter sind keine expliziten Parameter der AutoMLConfig-Klasse. 

* `ensemble_download_models_timeout_sec`: Während der **VotingEnsemble**- und **StackEnsemble**-Modellgenerierung werden mehrere angepasste Modelle aus den vorherigen Ausführungen untergeordneter Elemente heruntergeladen. Wenn der folgende Fehler angezeigt wird, müssen Sie möglicherweise mehr Zeit für das Herunterladen der Modelle einplanen: `AutoMLEnsembleException: Could not find any models for running ensembling`. Der Standardwert beträgt 300 Sekunden für das parallele Herunterladen dieser Modelle, und es gibt kein maximales Timeoutlimit. Konfigurieren Sie diesen Parameter mit einem höheren Wert als 300 Sekunden, wenn mehr Zeit erforderlich ist. 

  > [!NOTE]
  >  Wenn das Zeitlimit erreicht ist und Modelle heruntergeladen wurden, wird das Ensembling mit so vielen Modellen fortgesetzt, wie es heruntergeladen hat. Es ist nicht erforderlich, dass alle Modelle heruntergeladen werden müssen, um innerhalb dieses Zeitlimits fertig zu werden.

Die folgenden Parameter gelten nur für **StackEnsemble**-Modelle: 

* `stack_meta_learner_type`: Der meta-learner ist ein mit der Ausgabe der einzelnen heterogenen Modelle trainiertes Modell. Standard-meta-learner sind `LogisticRegression` für Klassifizierungsaufgaben (oder `LogisticRegressionCV`, wenn die Kreuzvalidierung aktiviert ist) und `ElasticNet` für Regressions-/Vorhersage-Aufgaben (oder `ElasticNetCV`, wenn die Kreuzvalidierung aktiviert ist). Dieser Parameter kann ein der folgenden Zeichenfolgen sein: `LogisticRegression`, `LogisticRegressionCV`, `LightGBMClassifier`, `ElasticNet`, `ElasticNetCV`, `LightGBMRegressor` oder `LinearRegression`.

* `stack_meta_learner_train_percentage`: gibt den Teil des Trainingssatzes an (bei Auswahl des Trainings- und Validierungstyps des Trainings), der für das Training des meta-learners reserviert werden soll. Der Standardwert ist `0.2`. 

* `stack_meta_learner_kwargs`: optionale Parameter, die an den Initialisierer des meta-learners übergeben werden sollen. Diese Parameter und Parametertypen spiegeln die Parameter und Parametertypen des entsprechenden Modellkonstruktors wider und werden an den Modellkonstruktor weitergeleitet.

Der folgende Code zeigt ein Beispiel für das Angeben des benutzerdefinierten Ensemble-Verhaltens in einem `AutoMLConfig`-Objekt.

```python
ensemble_settings = {
    "ensemble_download_models_timeout_sec": 600
    "stack_meta_learner_type": "LogisticRegressionCV",
    "stack_meta_learner_train_percentage": 0.3,
    "stack_meta_learner_kwargs": {
        "refit": True,
        "fit_intercept": False,
        "class_weight": "balanced",
        "multi_class": "auto",
        "n_jobs": -1
    }
}

automl_classifier = AutoMLConfig(
        task='classification',
        primary_metric='AUC_weighted',
        experiment_timeout_minutes=30,
        training_data=train_data,
        label_column_name=label,
        n_cross_validations=5,
        **ensemble_settings
        )
```

<a name="exit"></a> 

### <a name="exit-criteria"></a>Exit Criteria (Beendigungskriterien)

Es gibt einige Optionen, die Sie in Ihrer AutoMLConfig definieren können, um Ihr Experiment zu beenden.

|Kriterien| description
|----|----
No&nbsp;criteria (Keine Kriterien) | Wenn Sie keine Beendigungsparameter definieren, wird das Experiment fortgesetzt, bis kein weiterer Fortschritt bei Ihrer primären Metrik erzielt wird.
After&nbsp;a&nbsp;length&nbsp;of&nbsp;time (Nach einem Zeitraum)| Verwenden Sie `experiment_timeout_minutes` in Ihren Einstellungen, um zu definieren, wie lange Ihr Experiment in Minuten weiterhin ausgeführt werden soll. <br><br> Es liegt ein Mindestwert von 15 Minuten vor (oder 60 Minuten, wenn die Zeile nach Spaltengröße 10 Millionen überschreitet), um Fehler aufgrund von Zeitüberschreitungen beim Experiment zu vermeiden.
A&nbsp;score&nbsp;has&nbsp;been&nbsp;reached (Eine Bewertung wurde erzielt)| Wenn Sie `experiment_exit_score` verwenden, wird das Experiment abgeschlossen, nachdem der angegebene primäre metrischer Bewertungswert erreicht wurde.

## <a name="run-experiment"></a>Ausführen des Experiments

Für automatisierte ML-Experimente erstellen Sie ein `Experiment`-Objekt, wobei es sich um ein benanntes Objekt in einem `Workspace` handelt, das zur Ausführung von Experimenten verwendet wird.

```python
from azureml.core.experiment import Experiment

ws = Workspace.from_config()

# Choose a name for the experiment and specify the project folder.
experiment_name = 'automl-classification'
project_folder = './sample_projects/automl-classification'

experiment = Experiment(ws, experiment_name)
```

Übermitteln Sie das auszuführende Experiment, um ein Modell zu generieren. Übergeben Sie die Methode `AutoMLConfig` an die `submit`-Methode, um das Modell zu generieren.

```python
run = experiment.submit(automl_config, show_output=True)
```

>[!NOTE]
>Abhängigkeiten werden zunächst auf einem neuen Computer installiert.  Es dauert bis zu 10 Minuten, bevor die Ausgabe angezeigt wird.
>Wenn Sie `show_output` auf `True` festlegen, wird die Ausgabe auf der Konsole angezeigt.

### <a name="multiple-child-runs-on-clusters"></a>Mehrere untergeordnete Ausführungen auf Clustern

Untergeordnete Ausführungen von automatisierten ML-Experimenten können auf einem Cluster durchgeführt werden, auf dem bereits ein anderes Experiment ausgeführt wird. Das Timing hängt jedoch davon ab, über wie viele Knoten der Cluster verfügt und ob diese Knoten zur Ausführung eines anderen Experiments zur Verfügung stehen.

Jeder Knoten im Cluster fungiert als einzelner virtueller Computer (VM), der eine einzelne Trainingsausführung durchführen kann. Für automatisiertes ML bedeutet dies eine untergeordnete Ausführung. Wenn alle Knoten ausgelastet sind, wird das neue Experiment in die Warteschlange gestellt. Wenn es jedoch freie Knoten gibt, führt das neue Experiment untergeordnete Ausführungen von automatisiertem ML parallel auf den verfügbaren Knoten/VMs aus.

Um die Verwaltung von untergeordneten Ausführungen und deren Zeitpunkt der Durchführung zu erleichtern, empfehlen wir Ihnen, einen dedizierten Cluster pro Experiment zu erstellen und die Anzahl von `max_concurrent_iterations` Ihres Experiments an die Anzahl der Knoten im Cluster anzupassen. Auf diese Weise verwenden Sie alle Clusterknoten gleichzeitig mit der von Ihnen gewünschten Anzahl von gleichzeitigen untergeordneten Ausführungen/Iterationen.

Konfigurieren Sie `max_concurrent_iterations` in Ihrem `AutoMLConfig`-Objekt. Wenn es nicht konfiguriert ist, ist standardmäßig nur eine gleichzeitige untergeordnete Ausführung/Iteration pro Experiment zulässig.  

## <a name="explore-models-and-metrics"></a>Untersuchen von Modellen und Metriken

Sie können Ihre Trainingsergebnisse in einem Widget oder in der Inlineansicht anzeigen, wenn Sie ein Notebook verwenden. Weitere Details finden Sie unter [Verfolgen und Auswerten von Modellen](how-to-monitor-view-training-logs.md#monitor-automated-machine-learning-runs).

Definitionen und Beispiele zu den Leistungsdiagrammen und -metriken, die für jede Ausführung angegeben werden, finden Sie unter [Auswerten der Ergebnisse von Experimenten des automatisierten maschinellen Lernens](how-to-understand-automated-ml.md). 

Eine Zusammenfassung der Featurisierung und Informationen zu den Features, die zu einem bestimmten Modell hinzugefügt wurden, finden Sie unter [Transparenz der Featurisierung](how-to-configure-auto-features.md#featurization-transparency). 

> [!NOTE]
> Die Algorithmen, die beim automatisierten maschinellen Lernen eingesetzt werden, weisen eine inhärente Zufälligkeit auf, die zu geringfügigen Abweichungen in der abschließenden metrischen Bewertung eines empfohlenen Modells führen kann, z. B. bei der Genauigkeit. Automatisiertes maschinelles Lernen führt bei Bedarf auch Vorgänge an Daten wie Training-Test-Aufteilung, Training-Validierung-Aufteilung oder Kreuzvalidierung durch. Wenn Sie also ein Experiment mit denselben Konfigurationseinstellungen und derselben primären Metrik mehrmals durchführen, werden Sie aufgrund dieser Faktoren wahrscheinlich bei jedem Experiment eine Abweichung in der abschließenden metrischen Bewertung sehen. 

## <a name="register-and-deploy-models"></a>Registrieren und Bereitstellen von Modellen

Details zum Herunterladen oder Registrieren eines Modells für die Bereitstellung in einem Webdienst finden Sie unter [Wie und wo Modelle bereitgestellt werden](how-to-deploy-and-where.md).

<a name="explain"></a>

## <a name="model-interpretability"></a>Interpretierbarkeit von Modellen

Die Interpretierbarkeit von Modellen ermöglicht es Ihnen, zu verstehen, warum Ihre Modelle Vorhersagen erstellt haben, und die zugrunde liegenden Featurewichtigkeitswerte zu verstehen. Das SDK enthält verschiedene Pakete zum Aktivieren von Modellinterpretierbarkeitsfeatures, sowohl für die Trainings- als auch die Rückschlusszeit, für lokale und bereitgestellte Modelle.

Codebeispiele dazu, wie Interpretierbarkeitsfeatures insbesondere in Experimenten zum automatisierten maschinellen Lernen aktiviert werden, finden Sie unter [Interpretierbarkeit von Modellen für automatisiertes ML](how-to-machine-learning-interpretability-automl.md).

Allgemeine Informationen dazu, wie Modellerklärungen und Featurewichtigkeit in anderen Bereichen des SDK außerhalb des automatisierten maschinellen Lernens aktiviert werden können, finden Sie im Artikel [Modellinterpretierbarkeit mit Azure Machine Learning Service](how-to-machine-learning-interpretability.md).

> [!NOTE]
> Das ForecastTCN-Modell wird aktuell vom Erklärungsclient nicht unterstützt. Dieses Modell gibt kein Erklärungsdashboard zurück, wenn es als bestes Modell zurückgegeben wird, und es unterstützt keine Erklärungsläufe auf Anforderung.

## <a name="next-steps"></a>Nächste Schritte

+ Lernen Sie, [wie und wo Sie Modelle bereitstellen](how-to-deploy-and-where.md) können.

+ Erfahren Sie mehr über das [Trainieren eines Regressionsmodells mit automatisiertem maschinellem Lernen](tutorial-auto-train-models.md) und das [Trainieren mit automatisiertem maschinellem Lernen auf einer Remoteressource](how-to-auto-train-remote.md).

+ Hier erfahren Sie, wie Sie mehrere Modelle mit AutoML im [Many Models Solution Accelerator](https://aka.ms/many-models) trainieren.