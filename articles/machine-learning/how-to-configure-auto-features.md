---
title: Featurisierung in AutoML-Experimenten
titleSuffix: Azure Machine Learning
description: Erfahren Sie, welche Featurisierungseinstellungen Azure Machine Learning bietet und wie Feature Engineering in Experimenten mit automatisiertem ML unterstützt wird.
author: nibaccam
ms.author: nibaccam
ms.reviewer: nibaccam
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, automl
ms.date: 05/28/2020
ms.openlocfilehash: 658db1604895515525e5a4826a43c0b21d9698b1
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93359628"
---
# <a name="featurization-in-automated-machine-learning"></a>Featurisierung mit automatisiertem maschinellem Lernen



In diesem Leitfaden wird Folgendes vermittelt:

- Welche Featurisierungseinstellungen Azure Machine Learning bietet
- Wie Sie diese Features für Ihre [automatisierten Machine Learning-Experimente](concept-automated-ml.md) anpassen

Beim *Feature Engineering* werden Domänenkenntnisse der Daten zum Erstellen von Features verwendet, mit denen ML-Algorithmen (Machine Learning) besser lernen können. In Azure Machine Learning wird das Feature Engineering mithilfe von Datenskalierungs- und Normalisierungstechniken vereinfacht. Zusammen werden diese Techniken und das Feature Engineering als *Featurisierung* in Experimenten mit automatisiertem maschinellem Lernen bzw. *AutoML* bezeichnet.

## <a name="prerequisites"></a>Voraussetzungen

In diesem Artikel wird davon ausgegangen, dass Sie bereits mit der Konfiguration eines AutoML-Experiments vertraut sind. Weitere Informationen zur Konfiguration finden Sie in den folgenden Artikeln:

- Für ein Code First-Konzept: [Konfigurieren automatisierter ML-Experimente in Python](how-to-configure-auto-train.md)
- Für ein Konzept mit wenig Code oder ohne Code: [Erstellen, Überprüfen und Bereitstellen von automatisierten Machine Learning-Modellen mit Azure Machine Learning](how-to-use-automated-ml-for-ml-models.md)

## <a name="configure-featurization"></a>Konfigurieren der Featurisierung

Standardmäßig werden in jedem Experiment mit automatisiertem maschinellem Lernen [automatische Skalierungs- und Normalisierungstechniken](#featurization) auf Ihre Daten angewandt. Bei diesen Techniken handelt es sich um Formen der Featurisierung, die für *bestimmte* Algorithmen hilfreich sind, die auf Features unterschiedlichen Größenordnungen reagieren. Sie können jedoch zusätzliche Featurisierungen wie die *Zuschreibung fehlender Werte* , *Codierung* und *Transformationen* aktivieren.

> [!NOTE]
> Die Schritte zur Featurisierung bei automatisiertem maschinellen Lernen (Featurenormalisierung, Behandlung fehlender Daten, Umwandlung von Text in numerische Daten usw.) werden Teil des zugrunde liegenden Modells. Bei Verwendung des Modells für Vorhersagen werden die während des Trainings angewendeten Schritte zur Featurisierung automatisch auf Ihre Eingabedaten angewendet.

Bei Experimenten, die mit dem Python SDK konfiguriert werden, können Sie die Featurisierungseinstellung aktivieren oder deaktivieren und die Featurisierungsschritte für Ihr Experiment angeben. Wenn Sie Azure Machine Learning Studio verwenden, finden Sie unter [Anpassen der Featurisierung](how-to-use-automated-ml-for-ml-models.md#customize-featurization) weitere Informationen.

In der folgenden Tabelle sind die akzeptierten Einstellungen für `featurization` in der [AutoMLConfig-Klasse](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) aufgeführt:

|Konfiguration der Featurisierung | BESCHREIBUNG|
------------- | ------------- |
|`"featurization": 'auto'`| Gibt an, dass im Rahmen der Vorverarbeitung die folgenden [Schritte für Datenschutzmaßnahmen und Featurebereitstellung](#featurization) automatisch durchgeführt werden müssen. Dies ist die Standardeinstellung.|
|`"featurization": 'off'`| Gibt an, dass Featurisierungsschritte nicht automatisch durchgeführt werden müssen.|
|`"featurization":`&nbsp;`'FeaturizationConfig'`| Gibt an, dass benutzerdefinierte Featurisierungsschritte verwendet werden sollen. [Erfahren Sie, wie Sie die Featurebereitstellung anpassen](#customize-featurization).|

<a name="featurization"></a>

## <a name="automatic-featurization"></a>Automatische Featurebereitstellung

In der folgenden Tabelle sind die Verfahren zusammengefasst, die automatisch auf die Daten angewendet werden. Diese Verfahren werden für Experimente angewendet, die mit dem SDK oder Studio konfiguriert werden. Um dieses Verhalten zu deaktivieren, legen Sie `"featurization": 'off'` in Ihrem `AutoMLConfig`-Objekt fest.

> [!NOTE]
> Wenn Sie Ihre mit ML automatisch erstellten Modelle in ein [ONNX-Modell](concept-onnx.md) exportieren möchten, beachten Sie, dass nur die mit einem Sternchen („*“) gekennzeichneten Optionen zur Featurisierung im ONNX-Format unterstützt werden. Erfahren Sie mehr über das [Konvertieren von Modellen zu ONNX](concept-automated-ml.md#use-with-onnx).

|Schritte zur&nbsp;Featurisierung| BESCHREIBUNG |
| ------------- | ------------- |
|**Löschen von Features mit hoher Kardinalität oder ohne Varianz** _ |Löschen Sie diese Features aus Trainings-und Validierungssätzen. Gilt für Features, denen alle Werte fehlen, die denselben Wert für alle Zeilen haben oder die eine sehr hohe Kardinalität (z. B. Hashes, IDs oder GUIDs) aufweisen.|
|_*Imputieren von fehlenden Werten**_ |Bei numerischen Features werden fehlende Werte mit dem Durchschnitt der Werte in der Spalte imputiert.<br/><br/>Bei kategorischen Features werden fehlende Werte mit dem am häufigsten vorkommenden Wert imputiert.|
|_*Generieren zusätzlicher Features**_ |Für DateTime-Funktionen: Jahr, Monat, Tag, Tag der Woche, Tag des Jahres, Quartal, Woche des Jahres, Stunde, Minute, Sekunde.<br><br> _Für Vorhersageaufgaben* werden diese zusätzlichen DateTime-Features erstellt: ISO-Jahr, Halb – Halbjahr, Kalendermonat als Zeichenfolge, Woche, Wochentag als Zeichenfolge, Quartalstag, Tag des Jahres, AM/PM (0, wenn die Stunde vor Mittag ist (12 pm), andernfalls 1), AM/PM als Zeichenfolge, Tageszeit (12-Stunden-Basis)<br/><br/>Für Text-Funktionen: Ausdruckshäufigkeit basierend auf Unigrammen, Bigrammen und Trigrammen. Informieren Sie sich, [wie Sie hierzu BERT verwenden](#bert-integration).|
|**Transformieren und Codieren** _|Numerische Features mit wenigen eindeutigen Werten werden in kategorische Features transformiert.<br/><br/>One-Hot-Codierung wird für kategorische Features mit geringer Kardinalität verwendet. One-Hot-Hashcodierung wird für kategorische Features mit hoher Kardinalität verwendet.|
|_ *Worteinbettungen**|Ein Textfeaturizer konvertiert Vektoren von Texttoken mithilfe eines vortrainierten Modells in Satzvektoren. Der Einbettungsvektor jedes Worts in einem Dokument wird mit dem Rest zusammengefasst und produziert so einen Dokumentenfeaturevektor.|
|**Zielcodierungen**|Bei kategorischen Features wird durch diesen Schritt jede Kategorie einem gemitteltem Zielwert für Regressionsprobleme und der Klassenwahrscheinlichkeit für jede Klasse für Klassifizierungsprobleme zugeordnet. Häufigkeitsbasierte Gewichtung und k-fache Kreuzvalidierung werden angewendet, um die Überanpassung der Zuordnung und das Rauschen durch dünn besetzte Datenkategorien zu verringern.|
|**Textzielcodierung**|Für die Texteingabe wird ein gestapeltes lineares Modell mit Bag-of-Words verwendet, um die Wahrscheinlichkeit der einzelnen Klassen zu generieren.|
|**Gewichtung der Beweise (Weight of Evidence, WoE)**|Berechnet den WoE-Wert als ein Maß für die Korrelation von kategorischen Spalten zur Zielspalte. WoE wird als Protokoll des Verhältnisses von In-Class- zu Out-of-Class-Wahrscheinlichkeiten berechnet. Dieser Schritt gibt eine numerische Featurespalte pro Klasse aus, und fehlende Werte und Ausreißerbehandlung müssen nicht mehr explizit berechnet werden.|
|**Clusterabstand**|Trainiert ein k-Means-Clusteringmodell für alle numerischen Spalten. Gibt *k* neue Features aus (ein neues numerisches Feature pro Cluster), die den Abstand jeder Stichprobe zum Schwerpunkt jedes Clusters enthalten.|

## <a name="data-guardrails"></a>Schutzmaßnahmen für Daten

Der *Datenintegritätsschutz* hilft Ihnen dabei, potenzielle Probleme mit Ihren Daten zu identifizieren (z. B. fehlende Werte oder ein [Klassenungleichgewicht](concept-manage-ml-pitfalls.md#identify-models-with-imbalanced-data)). Sie helfen Ihnen auch, Korrekturmaßnahmen für verbesserte Ergebnisse zu ergreifen.

Der Datenintegritätsschutz wird für Folgendes angewendet:

- **Für SDK-Experimente** : wenn die Parameter `"featurization": 'auto'` oder `validation=auto` in Ihrem `AutoMLConfig`-Objekt angegeben sind
- **Für Studio-Experimente** : wenn die automatische Featurisierung aktiviert ist

Sie können den Datenintegritätsschutz für Ihr Experiment folgendermaßen überprüfen:

- Durch Festlegen von `show_output=True`, wenn Sie ein Experiment mit dem SDK übermitteln

- In Studio auf der Registerkarte **Data guardrails** (Datenintegritätsschutz) Ihrer Ausführung des automatisierten maschinellen Lernens

### <a name="data-guardrail-states"></a>Zustände des Datenintegritätsschutzes

Der Datenintegritätsschutz zeigt einen von drei Zuständen an:

|State| BESCHREIBUNG |
|----|---- |
|**Erfolgreich**| Es wurden keine Datenprobleme erkannt, und es ist keine Aktion von Ihrer Seite erforderlich. |
|**Fertig**| Die Änderungen wurden auf Ihre Daten angewendet. Wir empfehlen Ihnen, die vom automatisierten maschinellen Lernen ergriffenen Korrekturmaßnahmen zu überprüfen, um sicherzustellen, dass die Änderungen mit den erwarteten Ergebnissen übereinstimmen. |
|**Benachrichtigt**| Ein Datenproblem wurde erkannt, konnte aber nicht behoben werden. Wir empfehlen Ihnen, das Problem zu prüfen und zu beheben.|

### <a name="supported-data-guardrails"></a>Unterstützter Datenintegritätsschutz

In der folgenden Tabelle werden der derzeit unterstützte Datenintegritätsschutz und die zugehörigen Statuswerte beschrieben, die Ihnen beim Übermitteln Ihres Experiments unter Umständen angezeigt werden:

Schutzmaßnahme|Status|Bedingung&nbsp;für&nbsp;Auslösung
---|---|---
**Ergänzen fehlender Featurewerte durch Imputation** |Erfolgreich <br><br><br> Vorgehensweise| Es wurden keine fehlenden Featurewerte in Ihren Trainingsdaten festgestellt. Erfahren Sie mehr über die [Imputation fehlender Werte](./how-to-use-automated-ml-for-ml-models.md#customize-featurization). <br><br> Fehlende Featurewerte wurden in Ihren Trainingsdaten festgestellt und imputiert.
**Behandeln von Features mit hoher Kardinalität** |Erfolgreich <br><br><br> Vorgehensweise| Ihre Eingaben wurden analysiert, und es wurden keine Features mit hoher Kardinalität gefunden. <br><br> Features mit hoher Kardinalität wurden in Ihren Eingaben erkannt und behandelt.
**Verarbeitung der Überprüfungsaufteilung** |Vorgehensweise| Die Überprüfungskonfiguration wurde auf `'auto'` festgelegt, und die Trainingsdaten enthielten *mehr als 20.000 Zeilen*. <br> Jede Iteration des trainierten Modells wurde durch Kreuzvalidierung überprüft. Erfahren Sie mehr über [Überprüfungsdaten](./how-to-configure-auto-train.md#training-validation-and-test-data). <br><br> Die Überprüfungskonfiguration wurde auf `'auto'` festgelegt, und die Trainingsdaten enthielten *weniger als 20.000 Zeilen*. <br> Die Eingabedaten wurden zur Überprüfung des Modells in ein Trainingsdataset und ein Validierungsdataset aufgeteilt.
**Ausgewogenheitserkennung für Klassen** |Erfolgreich <br><br><br><br>Benachrichtigt <br><br><br>Vorgehensweise | Ihre Eingaben wurden analysiert, und alle Klassen in Ihren Trainingsdaten sind ausgeglichen. Ein Dataset gilt als ausgewogen, wenn jede Klasse im Datensatz gemessen an Anzahl und Verhältnis der Stichproben gut repräsentiert ist. <br><br> In Ihren Eingaben wurden unausgeglichene Klassen erkannt. Beheben Sie das Ausgleichsproblem, um den Modelltrend zu beheben. Erfahren Sie mehr über [unausgeglichene Daten](./concept-manage-ml-pitfalls.md#identify-models-with-imbalanced-data).<br><br> In Ihren Eingaben wurden unausgeglichene Klassen festgestellt, und die Sweeping-Logik hat beschlossen, einen Ausgleich anzuwenden.
**Erkennung von Arbeitsspeicherproblemen** |Erfolgreich <br><br><br><br> Vorgehensweise |<br> Die ausgewählten Werte (Horizont, Verzögerung und rollierendes Zeitfenster) wurden analysiert, und es wurden keine potenziellen Probleme aufgrund von unzureichendem Speicherplatz erkannt. Erfahren Sie mehr über [Vorhersagekonfigurationen](./how-to-auto-train-forecast.md#configuration-settings) von Zeitreihen. <br><br><br>Die ausgewählten Werte (Horizont, Verzögerung und rollierendes Zeitfenster) wurden analysiert, und für Ihr Experiment steht unter Umständen nicht genügend Speicherplatz zur Verfügung. Die Konfigurationen für Verzögerung oder rollierende Zeitfenster wurden deaktiviert.
**Häufigkeitserkennung** |Erfolgreich <br><br><br><br> Vorgehensweise |<br> Die Zeitreihe wurde analysiert, und alle Datenpunkte entsprechen der erkannten Häufigkeit. <br> <br> Die Zeitreihe wurde analysiert, und es wurden Datenpunkte ermittelt, die nicht mit der erkannten Häufigkeit übereinstimmen. Diese Datenpunkte wurden aus dem Dataset entfernt. Erfahren Sie mehr über die [Datenaufbereitung für die Zeitreihenvorhersage](./how-to-auto-train-forecast.md#preparing-data).

## <a name="customize-featurization"></a>Anpassen der Featurisierung

Sie können die Featurisierungseinstellungen anpassen, um sicherzustellen, dass die Daten und Features zum Trainieren Ihres ML-Modells zu relevanten Vorhersagen führen.

Um die Featurisierung anzupassen, geben Sie `"featurization": FeaturizationConfig` in Ihrem `AutoMLConfig`-Objekt an. Wenn Sie Azure Machine Learning Studio für Ihr Experiment verwenden, finden Sie weitere Informationen in dieser [Anleitung](how-to-use-automated-ml-for-ml-models.md#customize-featurization). Informationen zum Anpassen der Featurisierung für Vorhersagetasktypen finden Sie unter [Vorhersagen](how-to-auto-train-forecast.md#customize-featurization).

Unterstützte Anpassungen umfassen:

|Anpassung|Definition|
|--|--|
|**Aktualisierung des Spaltenzwecks**|Außerkraftsetzen des automatisch erkannten Featuretyps für die angegebene Spalte.|
|**Aktualisierung von Transformationsparametern** |Aktualisieren der Parameter für den angegebenen Transformator. Unterstützt derzeit *Imputer* (Mittelwert, häufigster Wert und medianer Wert) und *HashOneHotEncoder*.|
|**Löschen von Spalten** |Gibt Spalten an, die aus der Featureverwendung gelöscht werden sollen.|
|**Blockieren von Transformatoren**| Gibt Blockiertransformatoren an, die für die Featurebereitstellung verwendet werden.|

Erstellen Sie das `FeaturizationConfig`-Objekt mithilfe von API-Aufrufen:

```python
featurization_config = FeaturizationConfig()
featurization_config.blocked_transformers = ['LabelEncoder']
featurization_config.drop_columns = ['aspiration', 'stroke']
featurization_config.add_column_purpose('engine-size', 'Numeric')
featurization_config.add_column_purpose('body-style', 'CategoricalHash')
#default strategy mean, add transformer param for for 3 columns
featurization_config.add_transformer_params('Imputer', ['engine-size'], {"strategy": "median"})
featurization_config.add_transformer_params('Imputer', ['city-mpg'], {"strategy": "median"})
featurization_config.add_transformer_params('Imputer', ['bore'], {"strategy": "most_frequent"})
featurization_config.add_transformer_params('HashOneHotEncoder', [], {"number_of_bits": 3})
```

## <a name="featurization-transparency"></a>Transparenz der Featurisierung

Auf jedes AutoML-Modell wird automatisch eine Featurisierung angewendet.  Die Featurisierung umfasst automatisiertes Feature Engineering (wenn `"featurization": 'auto'`) sowie Skalierung und Normalisierung, was sich dann auf den ausgewählten Algorithmus und seine Hyperparameterwerte auswirkt. AutoML unterstützt verschiedene Methoden, um sicherzustellen, dass Sie einen Überblick darüber haben, was auf Ihr Modell angewendet wurde.

Betrachten Sie dieses Vorhersagebeispiel:

+ Es gibt vier Eingabemerkmale: A (numerisch), B (numerisch), C (numerisch), D (DateTime).
+ Das numerische Feature C wird gelöscht, da es sich um eine ID-Spalte mit ausschließlich eindeutigen Werten handelt.
+ Die Werte der numerischen Features A und B fehlen und werden daher als Mittelwerte zugerechnet.
+ DateTime-Feature D wird in 11 verschiedenen entwickelten Features bereitgestellt.

Verwenden Sie die `fitted_model`-Ausgabe Ihrer automatisierten ML-Experimentausführung, um diese Informationen zu erhalten.

```python
automl_config = AutoMLConfig(…)
automl_run = experiment.submit(automl_config …)
best_run, fitted_model = automl_run.get_output()
```
### <a name="automated-feature-engineering"></a>Automatisierte Featureentwicklung 
`get_engineered_feature_names()` gibt eine Liste von Namen entwickelter Features zurück.

  >[!Note]
  >Verwenden Sie „timeseriestransformer“ für die Aufgabe „forecasting“ und andernfalls „datatransformer“ für die Aufgaben „regression“ oder „classification“.

  ```python
  fitted_model.named_steps['timeseriestransformer']. get_engineered_feature_names ()
  ```

Diese Liste enthält alle Namen entwickelter Features. 

  ```
  ['A', 'B', 'A_WASNULL', 'B_WASNULL', 'year', 'half', 'quarter', 'month', 'day', 'hour', 'am_pm', 'hour12', 'wday', 'qday', 'week']
  ```

`get_featurization_summary()` erhält eine Zusammenfassung der Featurisierung aller Eingabefeatures.

  ```python
  fitted_model.named_steps['timeseriestransformer'].get_featurization_summary()
  ```

Output

  ```
  [{'RawFeatureName': 'A',
    'TypeDetected': 'Numeric',
    'Dropped': 'No',
    'EngineeredFeatureCount': 2,
    'Tranformations': ['MeanImputer', 'ImputationMarker']},
   {'RawFeatureName': 'B',
    'TypeDetected': 'Numeric',
    'Dropped': 'No',
    'EngineeredFeatureCount': 2,
    'Tranformations': ['MeanImputer', 'ImputationMarker']},
   {'RawFeatureName': 'C',
    'TypeDetected': 'Numeric',
    'Dropped': 'Yes',
    'EngineeredFeatureCount': 0,
    'Tranformations': []},
   {'RawFeatureName': 'D',
    'TypeDetected': 'DateTime',
    'Dropped': 'No',
    'EngineeredFeatureCount': 11,
    'Tranformations': ['DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime']}]
  ```

   |Output|Definition|
   |----|--------|
   |RawFeatureName|Eingabefeature-/Spaltenname aus dem bereitgestellten Dataset.|
   |TypeDetected|Erkannter Datentyp des Eingabefeatures.|
   |Dropped|Gibt an, ob das Eingabefeature gelöscht oder verwendet wurde.|
   |EngineeringFeatureCount|Anzahl von Features, die über automatisierte Featureentwicklungstransformationen generiert wurden.|
   |Transformationen|Liste der Transformationen, die zum Generieren entwickelter Features auf Eingabefeatures angewendet wurden.|

### <a name="scaling-and-normalization"></a>Skalierung und Normalisierung

Verwenden Sie `fitted_model.steps`, um die Skalierung/Normalisierung und den ausgewählten Algorithmus mit seinen Hyperparameterwerten zu verstehen. 

Das folgende Beispiel zeigt die Ausgabe der Ausführung von `fitted_model.steps` für eine ausgewählte Ausführung:

```
[('RobustScaler', 
  RobustScaler(copy=True, 
  quantile_range=[10, 90], 
  with_centering=True, 
  with_scaling=True)), 

  ('LogisticRegression', 
  LogisticRegression(C=0.18420699693267145, class_weight='balanced', 
  dual=False, 
  fit_intercept=True, 
  intercept_scaling=1, 
  max_iter=100, 
  multi_class='multinomial', 
  n_jobs=1, penalty='l2', 
  random_state=None, 
  solver='newton-cg', 
  tol=0.0001, 
  verbose=0, 
  warm_start=False))
```

Verwenden Sie diese Hilfsfunktion, um weitere Details zu erhalten: 

```python
from pprint import pprint

def print_model(model, prefix=""):
    for step in model.steps:
        print(prefix + step[0])
        if hasattr(step[1], 'estimators') and hasattr(step[1], 'weights'):
            pprint({'estimators': list(
                e[0] for e in step[1].estimators), 'weights': step[1].weights})
            print()
            for estimator in step[1].estimators:
                print_model(estimator[1], estimator[0] + ' - ')
        else:
            pprint(step[1].get_params())
            print()

print_model(model)
```

Diese Hilfsfunktion gibt die folgende Ausgabe für eine bestimmte Ausführung zurück, wobei `LogisticRegression with RobustScalar` als spezifischer Algorithmus verwendet wird.

```
RobustScaler
{'copy': True,
'quantile_range': [10, 90],
'with_centering': True,
'with_scaling': True}

LogisticRegression
{'C': 0.18420699693267145,
'class_weight': 'balanced',
'dual': False,
'fit_intercept': True,
'intercept_scaling': 1,
'max_iter': 100,
'multi_class': 'multinomial',
'n_jobs': 1,
'penalty': 'l2',
'random_state': None,
'solver': 'newton-cg',
'tol': 0.0001,
'verbose': 0,
'warm_start': False}
```

### <a name="predict-class-probability"></a>Vorhersage der Klassenwahrscheinlichkeit

Mithilfe von automatisiertem ML erzeugte Modelle verfügen sämtlich über Wrapper-Objekte, die die Funktionalität ihrer Open-Source-Ursprungsklasse widerspiegeln. Die meisten Wrapper-Objekte von Klassifizierungsmodellen, die von automatisiertem ML zurückgegeben werden, implementieren die `predict_proba()`-Funktion, die eine Datenstichprobe Ihrer Features (X-Werte) in einer Array-ähnlichen oder Sparsematrixform akzeptiert und ein n-dimensionales Array jeder Stichprobe und ihrer jeweiligen Klassenwahrscheinlichkeit zurückgibt.

Angenommen, Sie haben mithilfe der oben aufgeführten Aufrufe die beste Ausführung und das beste angepasste Modell abgerufen, dann können Sie `predict_proba()` direkt aus dem angepassten Modell abrufen, wobei Sie eine `X_test`-Stichprobe im für den Modelltyp passenden Format übergeben.

```python
best_run, fitted_model = automl_run.get_output()
class_prob = fitted_model.predict_proba(X_test)
```

Wenn das zugrundeliegende Modell die `predict_proba()`-Funktion nicht unterstützt oder das Format falsch ist, wird eine für die Modellklasse spezifische Ausnahme ausgelöst. Beispiele zur Implementierung dieser Funktion für verschiedene Modelltypen finden Sie in den Referenzdokumenten zu [RandomForestClassifier](https://scikit-learn.org/stable/modules/generated/sklearn.ensemble.RandomForestClassifier.html#sklearn.ensemble.RandomForestClassifier.predict_proba) und [XGBoost](https://xgboost.readthedocs.io/en/latest/python/python_api.html).

## <a name="bert-integration"></a>BERT-Integration

[BERT](https://techcommunity.microsoft.com/t5/azure-ai/how-bert-is-integrated-into-azure-automated-machine-learning/ba-p/1194657) wird auf der Featurisierungsebene von AutoML verwendet. Wenn in dieser Ebene eine Spalte freien Text oder andere Datentypen wie Zeitstempel oder einfache Zahlen enthält, dann wird die Featurisierung entsprechend angewendet.

Für BERT wird das Modell unter Verwendung der von den Anbietern zur Verfügung gestellten Bezeichnungen optimiert und trainiert. Von hier aus werden die Dokumenteinbettungen als Features neben anderen ausgegeben, z. B. zeitstempelbasierte Features, Wochentag. 


### <a name="bert-steps"></a>BERT-Schritte

Um BERT aufzurufen, müssen Sie `enable_dnn: True` in Ihren „automl_settings“ festlegen und eine GPU-Computeressource verwenden (z. B. `vm_size = "STANDARD_NC6"` oder eine höhere GPU). Wenn eine CPU-Computeressource verwendet wird, aktiviert AutoML anstatt BERT den BiLSTM DNN-Featurizer.

AutoML führt die folgenden Schritte für BERT aus. 

1. **Vorverarbeitung und Tokenisierung aller Textspalten**. Beispielsweise ist der „StringCast“-Transformator in der Zusammenfassung der Featurisierung für das endgültige Modell zu finden. Ein Beispiel dafür, wie die Zusammenfassung der Featurisierung des Modells zu erstellen ist, findet sich in [diesem Notebook](https://towardsdatascience.com/automated-text-classification-using-machine-learning-3df4f4f9570b).

2. **Verketten Sie alle Textspalten zu einer einzelnen Textspalte** , daher das `StringConcatTransformer` im endgültigen Modell. 

    Bei unserer Implementierung von BERT ist die Gesamttextlänge eines Trainingsbeispiels auf 128 Token eingeschränkt. Das bedeutet, dass bei der Verkettung alle Textspalten idealerweise höchstens 128 Token aufweisen sollten. Wenn mehrere Spalten vorhanden sind, sollte jede Spalte gekürzt werden, sodass diese Bedingung erfüllt ist. Bei verketteten Spalten mit einer Länge von mehr als 128 Token kürzt die Tokenizerebene von BERT diese Eingabe andernfalls auf 128 Token.

3. **Im Rahmen des Featuresweepings wird BERT von AutoML mit der Baseline (Sammlung von Wortfeatures) für eine Stichprobe der Daten verglichen.** Dieser Vergleich bestimmt, ob BERT die Genauigkeit erhöhen würde. Wenn BERT besser abschneidet als die Baseline, verwendet AutoML dann BERT zur Textfeatureisierung für die gesamten Daten. In diesem Fall wird im endgültigen Modell `PretrainedTextDNNTransformer` angezeigt.

Die Ausführung von BERT dauert im Allgemeinen länger als bei den anderen Featurizern. Für eine bessere Leistung empfehlen wir die Verwendung von „STANDARD_NC24r“ oder „STANDARD_NC24rs_V3“ für die RDMA-Fähigkeiten. 

AutoML verteilt das BERT-Training auf mehrere Knoten, wenn diese verfügbar sind (bis zu maximal acht Knoten). Dies kann in Ihrem `AutoMLConfig`-Objekt erfolgen, indem Sie den `max_concurrent_iterations`-Parameter auf einen höheren Wert als 1 festlegen. 
### <a name="supported-languages"></a>Unterstützte Sprachen

AutoML unterstützt derzeit ungefähr 100 Sprachen und wählt abhängig von der Sprache des Datasets das entsprechende BERT-Modell aus. Für Daten in deutscher Sprache wird das deutsche BERT-Modell verwendet. Für englische Daten wird das englische BERT-Modell verwendet. Für alle anderen Sprachen nutzen wir das mehrsprachige BERT-Modell.

Im folgenden Code wird das deutsche BERT-Modell ausgelöst, da `deu` als Datasetsprache angegeben ist, was gemäß [ISO-Klassifizierung](https://iso639-3.sil.org/code/deu) dem dreibuchstabigen Ländercode für Deutschland entspricht.

```python
from azureml.automl.core.featurization import FeaturizationConfig

featurization_config = FeaturizationConfig(dataset_language='deu')

automl_settings = {
    "experiment_timeout_minutes": 120,
    "primary_metric": 'accuracy', 
# All other settings you want to use 
    "featurization": featurization_config,
    
  "enable_dnn": True, # This enables BERT DNN featurizer
    "enable_voting_ensemble": False,
    "enable_stack_ensemble": False
}
```

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie, wie Sie Ihre Experimente mit automatisiertem maschinellem Lernen einrichten:

    * Für ein Code First-Konzept: [Konfigurieren automatisierter ML-Experimente](how-to-configure-auto-train.md)
    * Für ein Konzept mit wenig Code oder ohne Code: [Erstellen von Experimenten mit automatisiertem maschinellem Lernen in Azure Machine Learning Studio](how-to-use-automated-ml-for-ml-models.md)

* Lernen Sie, [wie und wo Sie Modelle bereitstellen](how-to-deploy-and-where.md) können.

* Erfahren Sie mehr über das [Trainieren eines Regressionsmodells mit automatisiertem maschinellem Lernen](tutorial-auto-train-models.md) und das [Trainieren mit automatisiertem maschinellem Lernen auf einer Remoteressource](how-to-auto-train-remote.md).
