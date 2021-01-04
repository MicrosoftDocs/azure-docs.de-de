---
title: Konfigurieren von Kreuzvalidierung und Datenaufteilung in automatisierten Experimenten mit maschinellem Lernen
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie die Kreuzvalidierung und die Aufteilung von Datasets für automatisierte Experimente mit maschinellem Lernen konfigurieren.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, automl
ms.author: cesardl
author: CESARDELATORRE
ms.reviewer: nibaccam
ms.date: 06/16/2020
ms.openlocfilehash: c29c8ab31507c0ec904a7534e50ef6523e1aab96
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93360104"
---
# <a name="configure-data-splits-and-cross-validation-in-automated-machine-learning"></a>Konfigurieren von Datenaufteilung und Kreuzvalidierung im automatisierten maschinellen Lernen

In diesem Artikel lernen Sie die verschiedenen Optionen zum Konfigurieren der Aufteilung von Trainings- und Validierungsdaten und der Kreuzvalidierung für Ihre automatisierten Experimente mit maschinellem Lernen, AutoML, kennen.

Wenn Sie in Azure Machine Learning AutoML zum Erstellen mehrerer ML-Modelle verwenden, muss jede untergeordnete Ausführung das zugehörige Modell durch Berechnen der Qualitätsmetriken für das Modell validieren, etwa „Genauigkeit“ oder „AUC gewichtet“. Diese Metriken werden durch Vergleichen der mit jedem Modell gemachten Vorhersagen mit realen Bezeichnungen aus Beobachtungen in der Vergangenheit in den Validierungsdaten berechnet. 

AutoML-Experimente führen die Modellvalidierung automatisch durch. In den folgenden Abschnitten wird beschrieben, wie Sie die Validierungseinstellungen mit dem [Azure Machine Learning Python SDK](/python/api/overview/azure/ml/?preserve-view=true&view=azure-ml-py) weiter anpassen können. 

Anweisungen zum Arbeiten mit wenig Code oder ohne Code finden Sie unter [Erstellen automatisierter Experimente mit maschinellem Lernen in Azure Machine Learning-Studio](how-to-use-automated-ml-for-ml-models.md). 

> [!NOTE]
> Im Studio werden aktuell Datenaufteilung für Training/Validierung und Optionen zur Kreuzvalidierung unterstützt, nicht jedoch das Angeben einzelner Datendateien für Ihr Validierungsset. 

## <a name="prerequisites"></a>Voraussetzungen

Für diesen Artikel ist Folgendes erforderlich:

* Ein Azure Machine Learning-Arbeitsbereich. Informationen zum Erstellen des Arbeitsbereichs finden Sie unter [Erstellen eines Azure Machine Learning-Arbeitsbereichs](how-to-manage-workspace.md).

* Grundkenntnisse in der Einrichtung eines automatisierten Experiments mit maschinellem Lernen mit dem Azure Machine Learning SDK. Machen Sie sich anhand des [Tutorials](tutorial-auto-train-models.md) oder der [Anleitung](how-to-configure-auto-train.md) mit den grundlegenden Entwurfsmustern für automatisierte ML-Experimente vertraut.

* Ein grundlegendes Verständnis von Kreuzvalidierung und Datenaufteilung für Training/Validierung als ML-Konzepte. Eine allgemeine Erklärung finden Sie unter

    * [About Train, Validation and Test Sets in Machine Learning](https://towardsdatascience.com/train-validation-and-test-sets-72cb40cba9e7) `(Informationen über Trainings-, Validierungs- und Testsets im maschinellen Lernen)

    * [Understanding Cross Validation](https://towardsdatascience.com/understanding-cross-validation-419dbd47e9bd) (Grundlegendes zur Kreuzvalidierung)

## <a name="default--data-splits-and-cross-validation"></a>Standard-Datenaufteilung und Kreuzvalidierung

Verwenden Sie das [AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig?preserve-view=true&view=azure-ml-py)-Objekt zum Definieren Ihrer Experiment- und Trainingseinstellungen. Beachten Sie im folgenden Codeausschnitt, dass nur die erforderlichen Parameter definiert sind, d. h., die Parameter für `n_cross_validation` oder `validation_ data` sind **nicht** enthalten.

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = dataset,
                             label_column_name = 'Class'
                            )
```

Wenn Sie nicht explizit einen `validation_data`- oder `n_cross_validation`-Parameter angeben, wendet AutoML Standardverfahren an, abhängig von der Anzahl der Zeilen im übergebenen einzelnen Dataset `training_data`:

|Größe der Trainings&nbsp;daten&nbsp;| Validierungsverfahren |
|---|-----|
|**Größer&nbsp;als&nbsp;20.000&nbsp;Zeilen**| Es wird eine Aufteilung in Trainings- und Validierungsdaten vorgenommen. Standardmäßig werden 10 % des ursprünglichen Trainingsdatasets als Validierungsset verwendet. Dieses Validierungsset wird seinerseits für die Metrikberechnung verwendet.
|**Kleiner&nbsp;als&nbsp;20.000&nbsp;Zeilen**| Der Kreuzvalidierungsansatz wird angewendet. Die Standardanzahl der Faltungen (Folds) hängt von der Zeilenanzahl ab. <br> **Wenn das Dataset weniger als 1.000 Zeilen aufweist** , werden 10 Faltungen verwendet. <br> **Wenn die Anzahl der Zeilen zwischen 1.000 und 20.000 liegt** , werden drei Faltungen verwendet.

## <a name="provide-validation-data"></a>Bereitstellen von Validierungsdaten

In diesem Fall können Sie entweder von einer einzelnen Datendatei ausgehen, die Sie in Trainings- und Validierungssets aufteilen, oder Sie können für das Validierungsset eine separate Datendatei bereitstellen. In beiden Fällen weist der Parameter `validation_data` in Ihrem `AutoMLConfig`-Objekt die Daten zu, die als Validierungsset verwendet werden. Dieser Parameter akzeptiert nur Datasets in der Form eines [Azure Machine Learning-Datasets](how-to-create-register-datasets.md) oder eines Pandas-Dataframes.   

Im folgenden Codebeispiel ist explizit definiert, welcher Teil der bereitgestellten Daten in `dataset` zum Training und welcher zur Validierung verwendet wird.

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

training_data, validation_data = dataset.random_split(percentage=0.8, seed=1)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = training_data,
                             validation_data = validation_data,
                             label_column_name = 'Class'
                            )
```

## <a name="provide-validation-set-size"></a>Angeben der Größe des Validierungssets

In diesem Fall wird nur ein einzelnes Dataset für das Experiment bereitgestellt. Das heißt, der `validation_data`-Parameter wird **nicht** angegeben, und das bereitgestellte Dataset wird dem Parameter `training_data` zugewiesen.  In Ihrem `AutoMLConfig`-Objekt können Sie den Parameter `validation_size` so festlegen, dass er einen Teil der Trainingsdaten für die Validierung reserviert. Das bedeutet, dass das Validierungsset von AutoML aus den ursprünglich bereitgestellten `training_data` abgeteilt wird. Dieser Wert sollte zwischen 0,0 und 1.0 ausschließlich betragen (beispielsweise bedeutet 0,2, dass 20 % der Daten als Validierungsdaten zurückgehalten werden).

Betrachten Sie das folgende Codebeispiel:

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = dataset,
                             validation_size = 0.2,
                             label_column_name = 'Class'
                            )
```

## <a name="set-the-number-of-cross-validations"></a>Festlegen der Anzahl der Kreuzvalidierungen

Um Kreuzvalidierung durchzuführen, schließen Sie den Parameter `n_cross_validations` ein, und legen Sie ihn auf einen Wert fest. Dieser Parameter legt fest, wie viele Kreuzvalidierungen durchgeführt werden, ausgehend von der gleichen Anzahl der Faltungen.

Im folgenden Code werden fünf Faltungen für die Kreuzvalidierung definiert. Es ergeben sich also fünf verschiedene Trainings, von denen jedes 4/5 der Daten und jede Validierung 1/5 der Daten mit einer pro Durchgang verschiedenen Faltung der zurückgehaltenen Daten verwendet.

Als Ergebnis werden Metriken mit dem Durchschnitt der 5 Validierungsmetriken berechnet.

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = dataset,
                             n_cross_validations = 5
                             label_column_name = 'Class'
                            )
```

## <a name="specify-custom-cross-validation-data-folds"></a>Angeben benutzerdefinierter Faltungen für die Kreuzvalidierung

Sie können außerdem eigene Datenfaltungen für die Kreuzvalidierung (Cross-Validation, CV) angeben. Dies wird als fortgeschrittenes Szenario angesehen, da Sie angeben, welche Spalten aufgeteilt und zur Validierung verwendet werden sollen.  Nehmen Sie benutzerdefinierte aufgeteilte CV-Spalten in Ihre Trainingsdaten auf, und geben Sie die Spalten an, indem Sie die Spaltennamen in den `cv_split_column_names`-Parameter einsetzen. Jede Spalte stellt eine Kreuzvalidierungsaufteilung dar und enthält die ganzzahligen Werte 1 oder 0 – hier gibt 1 an, dass die Zeile zum Training, 0, dass die Zeile zur Validierung verwendet werden soll.

Der folgende Codeausschnitt enthält Marketingdaten einer Bank mit zwei CV-Aufteilungsspalten ‚cv1‘ und ‚cv2‘.

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_with_cv.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = dataset,
                             label_column_name = 'y',
                             cv_split_column_names = ['cv1', 'cv2']
                            )
```

> [!NOTE]
> Um `cv_split_column_names` mit `training_data` und `label_column_name` zu verwenden, führen Sie ein Upgrade Ihres Azure Machine Learning Python SDK auf Version 1.6.0 oder höher durch. Informieren Sie sich für frühere SDK-Versionen über die Verwendung von `cv_splits_indices`, beachten Sie aber, dass dies nur in Verbindung mit der `X`- und `y`-Eingabe von Datasets verwendet wird. 

## <a name="next-steps"></a>Nächste Schritte

* [Vermeiden von unausgeglichenen Daten und Überanpassung](concept-manage-ml-pitfalls.md).
* [Tutorial: Vorhersagen von Preisen für Taxifahrten mit automatisiertem maschinellen Lernen – -Abschnitt zur Datenaufteilung](tutorial-auto-train-models.md#split-the-data-into-train-and-test-sets).
* Anleitung: [Automatisches Trainieren eines Modells für die Zeitreihenprognose](how-to-auto-train-forecast.md).