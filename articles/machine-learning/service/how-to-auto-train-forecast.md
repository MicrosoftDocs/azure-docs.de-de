---
title: Automatisches Trainieren eines Modells für die Zeitreihenprognose
titleSuffix: Azure Machine Learning service
description: Hier erfahren Sie, wie Sie mithilfe des Azure Machine Learning-Diensts ein Regressionsmodell für die Zeitreihenprognose mit automatisiertem maschinellem Lernen trainieren.
services: machine-learning
author: trevorbye
ms.author: trbye
ms.service: machine-learning
ms.subservice: core
ms.reviewer: trbye
ms.topic: conceptual
ms.date: 05/02/2019
ms.openlocfilehash: c7f4b6d8aa614a460772fb7af11f9b83dc3fc979
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65800812"
---
# <a name="auto-train-a-time-series-forecast-model"></a>Automatisches Trainieren eines Modells für die Zeitreihenprognose

In diesem Artikel erfahren Sie, wie Sie im Azure Machine Learning-Dienst ein Regressionsmodell für die Zeitreihenprognose mit automatisiertem maschinellem Lernen trainieren. Das Konfigurieren eines Vorhersagemodells ähnelt zwar der Einrichtung eines Standard-Regressionsmodells mit automatisiertem maschinellem Lernen, die Verwendung von Zeitreihendaten erfordert jedoch bestimmte Konfigurationsoptionen und Vorverarbeitungsschritte. In den Beispielen dieses Artikels wird Folgendes gezeigt:

* Vorbereiten von Daten für die Zeitreihenmodellierung
* Konfigurieren spezifischer Zeitreihenparameter in einem Objekt vom Typ [`AutoMLConfig`](/python/api/azureml-train-automl/azureml.train.automl.automlconfig)
* Ausführen von Vorhersagen mit Zeitreihendaten

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2X1GW]

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure Machine Learning-Dienstbereich. Informationen zum Erstellen des Arbeitsbereichs finden Sie unter [Erstellen eines Azure Machine Learning Service-Arbeitsbereichs](setup-create-workspace.md).
* In diesem Artikel werden Grundkenntnisse im Zusammenhang mit der Einrichtung eines Experiments mit automatisiertem maschinellem Lernen vorausgesetzt. Machen Sie sich anhand des [Tutorials](tutorial-auto-train-models.md) oder der [Anleitung](how-to-configure-auto-train.md) mit den grundlegenden Entwurfsmustern vertraut.

## <a name="preparing-data"></a>Vorbereiten der Daten

Der wichtigste Unterschied zwischen einem Regressionsaufgabentyp für die Vorhersage und einem regulären Regressionsaufgabentyp innerhalb des automatisierten maschinellen Lernens liegt in der Einbeziehung eines Features in Ihre Daten, das eine gültige Zeitreihe darstellt. Eine reguläre Zeitreihe besitzt ein klar definiertes und konsistentes Intervall sowie einen Wert an jedem Stichprobenpunkt in einem ununterbrochenen Zeitraum. Betrachten Sie die folgende Momentaufnahme der Datei `sample.csv`:

    day_datetime,store,sales_quantity,week_of_year
    9/3/2018,A,2000,36
    9/3/2018,B,600,36
    9/4/2018,A,2300,36
    9/4/2018,B,550,36
    9/5/2018,A,2100,36
    9/5/2018,B,650,36
    9/6/2018,A,2400,36
    9/6/2018,B,700,36
    9/7/2018,A,2450,36
    9/7/2018,B,650,36

Dieses Dataset ist ein einfaches Beispiel für die täglichen Verkaufsdaten eines Unternehmens mit zwei Filialen: A und B. Darüber hinaus ist ein Feature für `week_of_year` vorhanden, das dem Modell die Erkennung der wöchentlichen Saisonalität ermöglicht. Das Feld `day_datetime` stellt eine bereinigte Zeitreihe mit täglichem Intervall dar. Das Feld `sales_quantity` ist die Zielspalte für laufende Vorhersagen. Lesen Sie die Daten in einen Pandas-Datenrahmen ein, und verwenden Sie anschließend die Funktion `to_datetime`, um sicherzustellen, dass eine Zeitreihe vom Typ `datetime` verwendet wird.

```python
import pandas as pd
data = pd.read_csv("sample.csv")
data["day_datetime"] = pd.to_datetime(data["day_datetime"])
```

In diesem Fall sind die Daten bereits aufsteigend nach dem Zeitfeld `day_datetime` sortiert. Bei der Einrichtung eines Experiments muss jedoch darauf geachtet werden, dass die gewünschte Zeitspalte in aufsteigender Reihenfolge sortiert wird, um eine gültige Zeitreihe zu erstellen. Nehmen Sie an, die Daten umfassen 1.000 Datensätze, und teilen Sie die Daten deterministisch auf, um Trainings- und Testdatasets zu erstellen. Teilen Sie dann das Zielfeld `sales_quantity` auf, um das Trainings- und das Testdataset für die Vorhersage zu erstellen.

```python
X_train = data.iloc[:950]
X_test = data.iloc[-50:]

y_train = X_train.pop("sales_quantity").values
y_test = X_test.pop("sales_quantity").values
```

> [!NOTE]
> Stellen Sie beim Trainieren eines Modells für die Vorhersage zukünftiger Werte sicher, dass alle während des Trainings verwendeten Features beim Ausführen von Vorhersagen für Ihren gewünschten Vorhersagehorizont verwendet werden können. Wenn Sie also beispielsweise eine Nachfrageprognose erstellen, lässt sich die Trainingsgenauigkeit durch die Einbeziehung eines Features für den aktuellen Aktienkurs erheblich verbessern. Wenn Sie bei Ihrer Vorhersage allerdings einen Vorhersagehorizont verwenden, der weit in der Zukunft liegt, lassen sich zukünftige Aktienkurse für zukünftige Zeitreihenpunkte ggf. nicht präzise vorhersagen, was sich nachteilig auf die Modellgenauigkeit auswirken kann.

## <a name="configure-and-run-experiment"></a>Konfigurieren und Ausführen des Experiments

Bei Vorhersageaufgaben nutzt das automatisierte maschinelle Lernen spezifische Vorverarbeitungs- und Schätzschritte für Zeitreihendaten. Folgende Vorverarbeitungsschritte werden ausgeführt:

* Erkennen des Intervalls der Zeitreihenstichprobe (etwa stündlich, täglich, wöchentlich) und Erstellen neuer Datensätze für fehlende Zeitpunkte, um eine ununterbrochene Reihe zu erhalten
* Imputieren fehlender Werte in der Zielspalte (mittels Forward-Fill) und der Featurespalte (mittels Median-Spaltenwerten)
* Erstellen granularitätsbasierter Features, um reihenübergreifend feste Effekte zu ermöglichen
* Erstellen zeitbasierter Features zur Ermittlung saisonaler Muster
* Codieren kategorischer Variablen zu numerischen Mengen

Das Objekt `AutoMLConfig` definiert die erforderlichen Einstellungen und Daten für eine Aufgabe mit automatisiertem maschinellem Lernen. Sie definieren Standardtrainingsparameter wie Aufgabentyp, Iterationsanzahl, Trainingsdaten und Anzahl von Kreuzvalidierungen (ähnlich wie bei einem Regressionsproblem). Bei Vorhersageaufgaben müssen allerdings noch weitere Parameter für das Experiment festgelegt werden. In der folgenden Tabelle werden die einzelnen Parameter sowie deren Verwendung erläutert:

| Parameter | BESCHREIBUNG | Erforderlich |
|-------|-------|-------|
|`time_column_name`|Dient zum Angeben der Datetime-Spalte in den Eingabedaten, die zum Erstellen der Zeitreihe sowie zum Ableiten des Intervalls verwendet wird.|✓|
|`grain_column_names`|Namen zum Definieren individueller Reihengruppen in den Eingabedaten. Ohne definierte Granularität wird bei dem Dataset von einer einzelnen Zeitreihe ausgegangen.||
|`max_horizon`|Maximaler gewünschter Vorhersagehorizont in Einheiten des Zeitreihenintervalls.|✓|
|`target_lags`|*n* Zeiträume zum Weiterleiten von Verzögerungszielwerten vor dem Trainieren des Modells.||
|`target_rolling_window_size`|*n* Historische Zeiträume zum Generieren der vorhergesagten Werte, < = Größe Trainingsmenge. Wenn nicht angegeben, ist *n* die vollständige Trainingsmenge.||

Erstellen Sie die Zeitreiheneinstellungen als Wörterbuchobjekt. Legen Sie `time_column_name` auf das Feld `day_datetime` im Dataset fest. Definieren Sie den Parameter `grain_column_names`, um sicherzustellen, dass für die Daten **zwei separate Zeitreihengruppen** erstellt werden (jeweils eine für die Filialen A und B). Legen Sie abschließend `max_horizon` auf „50“ fest, um Vorhersagen für den gesamten Testsatz zu generieren. Legen Sie ein Vorhersagefenster auf 10 Zeiträume mit `target_rolling_window_size` fest, und verzögern Sie die Zielwerte 2 Zeiträume vorwärts mit dem `target_lags`-Parameter.

```python
time_series_settings = {
    "time_column_name": "day_datetime",
    "grain_column_names": ["store"],
    "max_horizon": 50,
    "target_lags": 2,
    "target_rolling_window_size": 10
}
```

Erstellen Sie als Nächstes ein Standardobjekt vom Typ `AutoMLConfig`, geben Sie den Aufgabentyp `forecasting` an, und übermitteln Sie das Experiment. Rufen Sie nach Fertigstellung des Modells die Iteration mit der besten Ausführung ab.

```python
from azureml.core.workspace import Workspace
from azureml.core.experiment import Experiment
from azureml.train.automl import AutoMLConfig
import logging

automl_config = AutoMLConfig(task='forecasting',
                             primary_metric='normalized_root_mean_squared_error',
                             iterations=10,
                             X=X_train,
                             y=y_train,
                             n_cross_validations=5,
                             enable_ensembling=False,
                             verbosity=logging.INFO,
                             **time_series_settings)

ws = Workspace.from_config()
experiment = Experiment(ws, "forecasting_example")
local_run = experiment.submit(automl_config, show_output=True)
best_run, fitted_model = local_run.get_output()
```

> [!NOTE]
> Für die Kreuzvalidierung (Cross-Validation, CV) können Zeitreihendaten den grundlegenden statistischen Annahmen der kanonischen k-fachen Kreuzvalidierungsstrategie widersprechen. Das automatisierte maschinelle Lernen implementiert daher eine parallele Ursprungsvalidierung, um Teilmengen für die Kreuzvalidierung von Zeitreihendaten zu erstellen. Geben Sie im Objekt `AutoMLConfig` den Parameter `n_cross_validations` an, um dieses Verfahren zu verwenden. Sie können die Validierung umgehen und mit den Parametern `X_valid` und `y_valid` Ihre eigenen Validierungssätze verwenden.

### <a name="view-feature-engineering-summary"></a>Anzeigen der Zusammenfassung der Featureentwicklung

Für Aufgabentypen mit Zeitreihen beim automatisierten maschinellen Lernen können Sie Details aus dem Featureentwicklungsprozess anzeigen. Der folgende Code zeigt jedes Rohfeature sowie die folgenden Attribute:

* Name des Rohfeatures
* Anzahl der entwickelten Features, die aus diesem Rohfeature gebildet wurden
* Erkannter Typ
* Ob das Feature gelöscht wurde
* Liste der Featuretransformationen für dieses Rohfeature

```python
fitted_model.named_steps['timeseriestransformer'].get_featurization_summary()
```

## <a name="forecasting-with-best-model"></a>Vorhersagen mit dem besten Modell

Verwenden Sie die beste Modelliteration, um Werte für das Testdataset vorherzusagen.

```python
y_predict = fitted_model.predict(X_test)
y_actual = y_test.flatten()
```

Alternativ können Sie anstelle von `predict()` die Funktion `forecast()` verwenden, die die Festlegung ermöglicht, wann Vorhersagen beginnen sollen. Im folgenden Beispiel ersetzen Sie zunächst alle Werte in `y_pred` durch `NaN`. Der Ursprung der Vorhersage liegt in diesem Fall am Ende der Trainingsdaten, wie es normalerweise bei der Verwendung von `predict()` der Fall wäre. Wenn Sie jedoch nur die zweite Hälfte von `y_pred` durch `NaN` ersetzen, lässt die Funktion die numerischen Werte in der ersten Hälfte unverändert, sagt aber die `NaN`-Werte in der zweiten Hälfte voraus. Die Funktion gibt sowohl die vorhergesagten Werte als auch die angepassten Features zurück.

Sie können den Parameter `forecast_destination` in der Funktion `forecast()` auch verwenden, um Werte bis zu einem bestimmten Datum vorherzusagen.

```python
y_query = y_test.copy().astype(np.float)
y_query.fill(np.nan)
y_fcst, X_trans = fitted_pipeline.forecast(X_test, y_query, forecast_destination=pd.Timestamp(2019, 1, 8))
```

Berechnen Sie den RMSE-Wert (Root Mean Squared Error) zwischen den tatsächlichen Werten (`y_test`) und den vorhergesagten Werten (`y_pred`).

```python
from sklearn.metrics import mean_squared_error
from math import sqrt

rmse = sqrt(mean_squared_error(y_actual, y_predict))
rmse
```

Nach der Ermittlung der allgemeinen Modellgenauigkeit besteht der nächste Schritt in der Regel darin, mithilfe des Modells unbekannte zukünftige Werte vorherzusagen. Stellen Sie einfach ein Dataset im gleichen Format wie das Testdataset `X_test`, aber mit zukünftigen Datums-/Uhrzeitwerten bereit, um einen Vorhersagesatz mit Vorhersagewerten für die einzelnen Zeitreihenschritte zu erhalten. Angenommen, die letzten Zeitreihendatensätze im Dataset waren für den 31.12.2018. Wenn Sie die Nachfrage für den Folgetag (oder für beliebig viele Vorhersagezeiträume < = `max_horizon`) vorhersagen möchten, erstellen Sie für jede Filiale einen einzelnen Zeitreihendatensatz für den 01.01.2019.

    day_datetime,store,week_of_year
    01/01/2019,A,1
    01/01/2019,A,1

Wiederholen Sie die erforderlichen Schritte, um diese zukünftigen Daten in einen Datenrahmen zu laden, und führen Sie anschließend `best_run.predict(X_test)` aus, um zukünftige Werte vorherzusagen.

> [!NOTE]
> Werte können nur für eine Anzahl von Zeiträumen vorhergesagt werden, die maximal dem Wert von `max_horizon` entspricht. Das Modell muss mit einem weiter in der Zukunft liegenden Vorhersagehorizont neu trainiert werden, um zukünftige Werte vorhersagen können, die über den aktuellen Vorhersagehorizont hinausgehen.

## <a name="next-steps"></a>Nächste Schritte

* Absolvieren Sie das [Tutorial](tutorial-auto-train-models.md), um zu erfahren, wie Sie Experimente mit automatisiertem maschinellem Lernen erstellen.
* Machen Sie sich mit der Referenzdokumentation [Azure Machine Learning SDK für Python](https://aka.ms/aml-sdk) vertraut.
