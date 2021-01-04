---
title: Konvertieren von Notebook-Code in Python-Skripts
titleSuffix: Azure Machine Learning
description: Konvertieren Sie Ihre experimentellen Notebooks für maschinelles Lernen (ML) mit der MLOpsPython-Codevorlage in produktionsreifen Code. Anschließend können Sie diesen Code testen, bereitstellen und automatisieren.
author: bjcmit
ms.author: brysmith
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 04/30/2020
ms.custom: devx-track-python
ms.openlocfilehash: 2f7746f079e740493348731376d0a5a7b1a9e954
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93317855"
---
# <a name="tutorial-convert-ml-experiments-to-production-python-code"></a>Tutorial: Konvertieren von ML-Experimenten in Python-Produktionscode

In diesem Tutorial erfahren Sie, wie Sie Jupyter-Notebooks mit der MLOpsPython-Codevorlage und Azure Machine Learning in Python-Skripts konvertieren, damit sie leichter getestet und automatisiert werden können. In der Regel wird dieser Prozess verwendet, um Experiment-/Trainingscode aus einem Jupyter-Notebook in Python-Skripts zu konvertieren. Diese Skripts können dann für Tests und zur CI/CD-Automatisierung in Ihrer Produktionsumgebung verwendet werden. 

Für ein Projekt für maschinelles Lernen (Machine Learning) sind Experimente erforderlich, bei denen Hypothesen mit flexiblen Tools wie Jupyter Notebook und realen Datasets überprüft werden. Wenn das Modell für die Produktion bereit ist, sollte der Modellcode in einem Repository für Produktionscode platziert werden. In einigen Fällen muss der Modellcode in Python-Skripts konvertiert werden, damit er in ein Repository für Produktionscode eingefügt werden kann. In diesem Tutorial geht es um einen empfohlenen Ansatz für den Export von Experimentcode in Python-Skripts.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Bereinigen von unwichtigem Code
> * Umgestalten von Jupyter Notebook-Code in Funktionen
> * Erstellen von Python-Skripts für verwandte Aufgaben
> * Erstellen von Komponententests

## <a name="prerequisites"></a>Voraussetzungen

- Generieren Sie die [MLOpsPython-Vorlage](https://github.com/microsoft/MLOpsPython/generate), und verwenden Sie die Notebooks `experimentation/Diabetes Ridge Regression Training.ipynb` und `experimentation/Diabetes Ridge Regression Scoring.ipynb`. Diese Notebooks werden als Beispiel für die Konvertierung vom Experiment in die Produktion verwendet. Sie finden diese Notebooks unter [https://github.com/microsoft/MLOpsPython/tree/master/experimentation](https://github.com/microsoft/MLOpsPython/tree/master/experimentation).
- Installieren von `nbconvert`. Befolgen Sie die Installationsanleitung auf der Seite [Installation](https://nbconvert.readthedocs.io/en/latest/install.html) im Abschnitt __Installing nbconvert__ (nbconvert installieren).

## <a name="remove-all-nonessential-code"></a>Entfernen des unwichtigen Codes

Code, der im Rahmen der Experimente geschrieben wird, dient nur explorativen Zwecken. Daher ist der erste Schritt beim Konvertieren von Experimentcode in Produktionscode das Entfernen dieses unwichtigen Codes. Nach dem Entfernen des unwichtigen Codes kann der restliche Code zudem besser verwaltet werden. In diesem Abschnitt entfernen Sie Code aus dem Notebook `experimentation/Diabetes Ridge Regression Training.ipynb`. Die Anweisungen zum Ausgeben der Form von `X` und `y` und die Zelle zum Aufrufen von `features.describe` dienen nur der Untersuchung von Daten und können entfernt werden. Nach dem Entfernen von unwichtigem Code sollte `experimentation/Diabetes Ridge Regression Training.ipynb` wie der folgende Code ohne Markdown aussehen:

```python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
import joblib
import pandas as pd

sample_data = load_diabetes()

df = pd.DataFrame(
    data=sample_data.data,
    columns=sample_data.feature_names)
df['Y'] = sample_data.target

X = df.drop('Y', axis=1).values
y = df['Y'].values

X_train, X_test, y_train, y_test = train_test_split(
    X, y, test_size=0.2, random_state=0)
data = {"train": {"X": X_train, "y": y_train},
        "test": {"X": X_test, "y": y_test}}

args = {
    "alpha": 0.5
}

reg_model = Ridge(**args)
reg_model.fit(data["train"]["X"], data["train"]["y"])

preds = reg_model.predict(data["test"]["X"])
mse = mean_squared_error(preds, y_test)
metrics = {"mse": mse}
print(metrics)

model_name = "sklearn_regression_model.pkl"
joblib.dump(value=reg, filename=model_name)
```

## <a name="refactor-code-into-functions"></a>Umgestalten von Code in Funktionen

Als zweite Maßnahme muss der Jupyter-Code in Funktionen umgestaltet werden. Die Umgestaltung von Code in Funktionen vereinfacht die Komponententests und verbessert die Verwaltbarkeit des Codes. In diesem Abschnitt gestalten Sie Folgendes um:

- Notebook „Diabetes Ridge Regression Training“ (`experimentation/Diabetes Ridge Regression Training.ipynb`)
- Notebook „Diabetes Ridge Regression Scoring“ (`experimentation/Diabetes Ridge Regression Scoring.ipynb`)

### <a name="refactor-diabetes-ridge-regression-training-notebook-into-functions"></a>Umgestalten des Notebooks „Diabetes Ridge Regression Training“ in Funktionen

Führen Sie unter `experimentation/Diabetes Ridge Regression Training.ipynb` die folgenden Schritte aus:

1. Erstellen Sie eine Funktion `split_data`, um den Datenrahmen in Test- und Trainingsdaten zu unterteilen. Die Funktion sollte den Datenrahmen `df` als Parameter verwenden und ein Verzeichnis mit den Schlüsseln `train` und `test` zurückgeben.

    Verschieben Sie den Code unter der Überschrift *Aufteilen der Daten in Trainings-/Validierungssätze* in die Funktion `split_data`, und ändern Sie ihn so, dass das Objekt `data` zurückgegeben wird.

1. Erstellen Sie eine Funktion `train_model`, bei der die Parameter `data` und `args` verwendet werden und ein trainiertes Modell zurückgegeben wird.

    Verschieben Sie den Code unter der Überschrift *Modell mit Trainingssatz trainieren* in die Funktion `train_model`, und ändern Sie ihn so, dass das Objekt `reg_model` zurückgegeben wird. Entfernen Sie das Verzeichnis `args`. Die Werte stammen vom Parameter `args`.

1. Erstellen Sie eine Funktion`get_model_metrics`, bei der die Parameter `reg_model` und `data` verwendet werden und das Modell ausgewertet wird. Anschließend wird ein Verzeichnis mit Metriken für das trainierte Modell zurückgegeben.

    Verschieben Sie den Code unter der Überschrift *Modell mit Validierungssatz überprüfen* in die Funktion `get_model_metrics`, und ändern Sie ihn so, dass das Objekt `metrics` zurückgegeben wird.

Die drei Funktionen sollten wie folgt lauten:

```python
# Split the dataframe into test and train data
def split_data(df):
    X = df.drop('Y', axis=1).values
    y = df['Y'].values

    X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
    data = {"train": {"X": X_train, "y": y_train},
            "test": {"X": X_test, "y": y_test}}
    return data


# Train the model, return the model
def train_model(data, args):
    reg_model = Ridge(**args)
    reg_model.fit(data["train"]["X"], data["train"]["y"])
    return reg_model


# Evaluate the metrics for the model
def get_model_metrics(reg_model, data):
    preds = reg_model.predict(data["test"]["X"])
    mse = mean_squared_error(preds, data["test"]["y"])
    metrics = {"mse": mse}
    return metrics
```

Führen Sie in `experimentation/Diabetes Ridge Regression Training.ipynb` die folgenden Schritte aus:

1. Erstellen Sie eine neue Funktion mit dem Namen `main`, für die keine Parameter verwendet werden und nichts zurückgegeben wird.
1. Verschieben Sie den Code unter der Überschrift „Daten laden“ in die Funktion `main`.
1. Fügen Sie der Funktion `main` Aufrufe für die neu geschriebenen Funktionen hinzu:
    ```python
    # Split Data into Training and Validation Sets
    data = split_data(df)
    ```

    ```python
    # Train Model on Training Set
    args = {
        "alpha": 0.5
    }
    reg = train_model(data, args)
    ```

    ```python
    # Validate Model on Validation Set
    metrics = get_model_metrics(reg, data)
    ```
1. Verschieben Sie den Code unter der Überschrift „Modell speichern“ in die Funktion `main`.

Die Funktion `main` sollte wie der folgende Code aussehen:

```python
def main():
    # Load Data
    sample_data = load_diabetes()

    df = pd.DataFrame(
        data=sample_data.data,
        columns=sample_data.feature_names)
    df['Y'] = sample_data.target

    # Split Data into Training and Validation Sets
    data = split_data(df)

    # Train Model on Training Set
    args = {
        "alpha": 0.5
    }
    reg = train_model(data, args)

    # Validate Model on Validation Set
    metrics = get_model_metrics(reg, data)

    # Save Model
    model_name = "sklearn_regression_model.pkl"

    joblib.dump(value=reg, filename=model_name)
```

Zu diesem Zeitpunkt sollte das Notebook keinen Code mehr enthalten, der nicht in einer Funktion enthalten ist (mit Ausnahme von Importanweisungen in der ersten Zelle).

Fügen Sie eine Anweisung hinzu, die die Funktion `main` aufruft.

```python
main()
```

Nach der Umgestaltung sollte `experimentation/Diabetes Ridge Regression Training.ipynb` wie der folgende Code ohne Markdown aussehen:

```python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
import pandas as pd
import joblib


# Split the dataframe into test and train data
def split_data(df):
    X = df.drop('Y', axis=1).values
    y = df['Y'].values

    X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
    data = {"train": {"X": X_train, "y": y_train},
            "test": {"X": X_test, "y": y_test}}
    return data


# Train the model, return the model
def train_model(data, args):
    reg_model = Ridge(**args)
    reg_model.fit(data["train"]["X"], data["train"]["y"])
    return reg_model


# Evaluate the metrics for the model
def get_model_metrics(reg_model, data):
    preds = reg_model.predict(data["test"]["X"])
    mse = mean_squared_error(preds, data["test"]["y"])
    metrics = {"mse": mse}
    return metrics


def main():
    # Load Data
    sample_data = load_diabetes()

    df = pd.DataFrame(
        data=sample_data.data,
        columns=sample_data.feature_names)
    df['Y'] = sample_data.target

    # Split Data into Training and Validation Sets
    data = split_data(df)

    # Train Model on Training Set
    args = {
        "alpha": 0.5
    }
    reg = train_model(data, args)

    # Validate Model on Validation Set
    metrics = get_model_metrics(reg, data)

    # Save Model
    model_name = "sklearn_regression_model.pkl"

    joblib.dump(value=reg, filename=model_name)

main()
```

### <a name="refactor-diabetes-ridge-regression-scoring-notebook-into-functions"></a>Umgestalten des Notebooks „Diabetes Ridge Regression Scoring“ in Funktionen

Führen Sie unter `experimentation/Diabetes Ridge Regression Scoring.ipynb` die folgenden Schritte aus:

1. Erstellen Sie eine neue Funktion mit dem Namen `init`, für die keine Parameter verwendet werden und nichts zurückgegeben wird.
1. Kopieren Sie den Code unter der Überschrift „Load Model“ (Modell laden) in die Funktion `init`.

Die Funktion `init` sollte wie der folgende Code aussehen:

```python
def init():
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)
```

Ersetzen Sie nach der Erstellung der Funktion `init` den gesamten Code unter der Überschrift „Load Model“ (Modell laden) wie folgt durch einen Einzelaufruf von `init`:

```python
init()
```

Führen Sie unter `experimentation/Diabetes Ridge Regression Scoring.ipynb` die folgenden Schritte aus:

1. Erstellen Sie wie folgt eine neue Funktion mit dem Namen `run`, bei der `raw_data` und `request_headers` als Parameter verwendet werden und ein Wörterbuch mit Ergebnissen zurückgegeben wird:

    ```python
    {"result": result.tolist()}
    ```

1. Kopieren Sie den Code unter den Überschriften „Prepare Data“ (Daten aufbereiten) und „Score Data“ (Daten bewerten) in die Funktion `run`.

    Die Funktion `run` sollte wie der folgende Code aussehen (denken Sie an das Entfernen der Anweisungen zum Festlegen der Variablen `raw_data` und `request_headers`, die später beim Aufrufen der Funktion `run` verwendet werden):

    ```python
    def run(raw_data, request_headers):
        data = json.loads(raw_data)["data"]
        data = numpy.array(data)
        result = model.predict(data)

        return {"result": result.tolist()}
    ```

Ersetzen Sie nach der Erstellung der Funktion `run` den gesamten Code unter den Überschriften „Prepare Data“ (Daten aufbereiten) und „Score Data“ (Daten bewerten) durch den folgenden Code:

```python
raw_data = '{"data":[[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}'
request_header = {}
prediction = run(raw_data, request_header)
print("Test result: ", prediction)
```

Mit dem obigen Code werden die Variablen `raw_data` und `request_header` festgelegt, die Funktion `run` mit `raw_data` und `request_header` wird aufgerufen, und die Vorhersagen werden ausgegeben.

Nach der Umgestaltung sollte `experimentation/Diabetes Ridge Regression Scoring.ipynb` wie der folgende Code ohne Markdown aussehen:

```python
import json
import numpy
from azureml.core.model import Model
import joblib

def init():
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)

def run(raw_data, request_headers):
    data = json.loads(raw_data)["data"]
    data = numpy.array(data)
    result = model.predict(data)

    return {"result": result.tolist()}

init()
test_row = '{"data":[[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}'
request_header = {}
prediction = run(test_row, {})
print("Test result: ", prediction)
```

## <a name="combine-related-functions-in-python-files"></a>Kombinieren verwandter Funktionen in Python-Dateien

Als dritte Maßnahme müssen die verwandten Funktionen in Python-Dateien zusammengeführt werden, um die Wiederverwendung von Code zu vereinfachen. In diesem Abschnitt erstellen Sie Python-Dateien für die folgenden Notebooks:

- Notebook „Diabetes Ridge Regression Training“ (`experimentation/Diabetes Ridge Regression Training.ipynb`)
- Notebook „Diabetes Ridge Regression Scoring“ (`experimentation/Diabetes Ridge Regression Scoring.ipynb`)

### <a name="create-python-file-for-the-diabetes-ridge-regression-training-notebook"></a>Erstellen einer Python-Datei für das Notebook „Diabetes Ridge Regression Training“

Konvertieren Sie Ihr Notebook in ein ausführbares Skript, indem Sie an einer Eingabeaufforderung die folgende Anweisung ausführen, für die das Paket `nbconvert`und der Pfad `experimentation/Diabetes Ridge Regression Training.ipynb` verwendet werden:

```
jupyter nbconvert -- to script "Diabetes Ridge Regression Training.ipynb" –output train
```

Entfernen Sie alle unerwünschten Kommentare, nachdem das Notebook in `train.py` konvertiert wurde. Ersetzen Sie den Aufruf von `main()` am Ende der Datei durch einen bedingten Aufruf wie den folgenden Code:

```python
if __name__ == '__main__':
    main()
```

Ihre Datei `train.py` sollte wie im folgenden Code aussehen:

```python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
import pandas as pd
import joblib


# Split the dataframe into test and train data
def split_data(df):
    X = df.drop('Y', axis=1).values
    y = df['Y'].values

    X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
    data = {"train": {"X": X_train, "y": y_train},
            "test": {"X": X_test, "y": y_test}}
    return data


# Train the model, return the model
def train_model(data, args):
    reg_model = Ridge(**args)
    reg_model.fit(data["train"]["X"], data["train"]["y"])
    return reg_model


# Evaluate the metrics for the model
def get_model_metrics(reg_model, data):
    preds = reg_model.predict(data["test"]["X"])
    mse = mean_squared_error(preds, data["test"]["y"])
    metrics = {"mse": mse}
    return metrics


def main():
    # Load Data
    sample_data = load_diabetes()

    df = pd.DataFrame(
        data=sample_data.data,
        columns=sample_data.feature_names)
    df['Y'] = sample_data.target

    # Split Data into Training and Validation Sets
    data = split_data(df)

    # Train Model on Training Set
    args = {
        "alpha": 0.5
    }
    reg = train_model(data, args)

    # Validate Model on Validation Set
    metrics = get_model_metrics(reg, data)

    # Save Model
    model_name = "sklearn_regression_model.pkl"

    joblib.dump(value=reg, filename=model_name)

if __name__ == '__main__':
    main()
```

`train.py` kann nun über ein Terminal aufgerufen werden, indem `python train.py` ausgeführt wird.
Die Funktionen aus `train.py` können auch aus anderen Dateien aufgerufen werden.

Die Datei `train_aml.py` aus dem Verzeichnis `diabetes_regression/training` im MLOpsPython-Repository ruft die in `train.py` definierten Funktionen im Kontext einer Azure Machine Learning-Experimentausführung auf. Die Funktionen können auch in Komponententests aufgerufen werden. Dieses Thema wird später in dieser Anleitung behandelt.

### <a name="create-python-file-for-the-diabetes-ridge-regression-scoring-notebook"></a>Erstellen einer Python-Datei für das Notebook „Diabetes Ridge Regression Scoring“

Konvertieren Sie Ihr Notebook in ein ausführbares Skript, indem Sie an einer Eingabeaufforderung die folgende Anweisung ausführen, für die das Paket `nbconvert` und der Pfad `experimentation/Diabetes Ridge Regression Scoring.ipynb` verwendet werden:

```
jupyter nbconvert -- to script "Diabetes Ridge Regression Scoring.ipynb" –output score
```

Entfernen Sie alle unerwünschten Kommentare, nachdem das Notebook in `score.py` konvertiert wurde. Ihre Datei `score.py` sollte wie im folgenden Code aussehen:

```python
import json
import numpy
from azureml.core.model import Model
import joblib

def init():
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)

def run(raw_data, request_headers):
    data = json.loads(raw_data)["data"]
    data = numpy.array(data)
    result = model.predict(data)

    return {"result": result.tolist()}

init()
test_row = '{"data":[[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}'
request_header = {}
prediction = run(test_row, request_header)
print("Test result: ", prediction)
```

`model` muss eine globale Variable sein, damit sie im gesamten Skript sichtbar ist. Fügen Sie am Anfang der `init`-Funktion die folgende Anweisung hinzu:

```python
global model
```

Nachdem Sie die obige Anweisung hinzugefügt haben, sollte die `init`-Funktion wie der folgende Code aussehen:

```python
def init():
    global model

    # load the model from file into a global object
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)
```

## <a name="create-unit-tests-for-each-python-file"></a>Erstellen von Komponententests für jede Python-Datei

Als vierte Maßnahme erstellen Sie Komponententests für Ihre Python-Funktionen. Komponententests schützen Code vor funktionalen Regressionen und vereinfachen die Verwaltung. In diesem Abschnitt erstellen Sie Komponententests für die Funktionen in `train.py`.

`train.py` enthält mehrere Funktionen. Wir erstellen jedoch nur einen Komponententest für die Funktion `train_model`. Dazu verwenden wir das Pytest-Framework in diesem Tutorial. Pytest ist nicht das einzige Python-Framework für Komponententests, aber eines der am häufigsten verwendeten. Weitere Informationen finden Sie unter [Pytest](https://pytest.org).

Ein Komponententest enthält normalerweise drei Hauptaktionen:

- Objekt anordnen: Erstellen und Einrichten der benötigten Objekte
- Aktion für ein Objekt durchführen
- Erwartetes Ergebnis bestätigen

Der Komponententest ruft `train_model` mit hartcodierten Daten und Argumenten auf und überprüft, ob `train_model` das erwartete Verhalten aufweist. Dazu wird das resultierende trainierte Modell verwendet, um eine Vorhersage zu treffen und diese Vorhersage mit einem erwarteten Wert zu vergleichen.

```python
import numpy as np
from code.training.train import train_model


def test_train_model():
    # Arrange
    X_train = np.array([1, 2, 3, 4, 5, 6]).reshape(-1, 1)
    y_train = np.array([10, 9, 8, 8, 6, 5])
    data = {"train": {"X": X_train, "y": y_train}}

    # Act
    reg_model = train_model(data, {"alpha": 1.2})

    # Assert
    preds = reg_model.predict([[1], [2]])
    np.testing.assert_almost_equal(preds, [9.93939393939394, 9.03030303030303])
```

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie nun wissen, wie ein Experiment in Produktionscode konvertiert wird, können Sie über die folgenden Links auf weitere Informationen und die nächsten Schritte zugreifen:

+ [MLOpsPython](https://github.com/microsoft/MLOpsPython/blob/master/docs/custom_model.md): Erstellen einer CI/CD-Pipeline zum Trainieren, Auswerten und Bereitstellen eines eigenen Modells mithilfe von Azure Pipelines und Azure Machine Learning
+ [Überwachen von Azure ML-Experimentausführungen und -metriken](./how-to-track-experiments.md)
+ [Überwachen und Erfassen von Daten von ML-Webdienst-Endpunkten](./how-to-enable-app-insights.md)