---
title: Was ist automatisiertes maschinelles Lernen? AutoML
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Azure Machine Learning automatisch mithilfe der bereitgestellten Parameter und Kriterien ein Modell generieren kann.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: cartacioS
ms.author: sacartac
ms.date: 10/27/2020
ms.custom: automl
ms.openlocfilehash: ca0cfd7c38dde5e7307c31989791aa3906b9cbe5
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93357282"
---
# <a name="what-is-automated-machine-learning-automl"></a>Was ist automatisiertes maschinelles Lernen (AutoML)?

Automatisiertes maschinelles Lernen, auch als automatisiertes ML oder AutoML bezeichnet, ist der Prozess des Automatisierens der zeitaufwändigen, iterativen Aufgaben der Entwicklung eines Machine Learning-Modells. Es versetzt Data Scientists, Analysten und Entwickler in die Lage, ML-Modelle mit hoher Skalierbarkeit, Effizienz und Produktivität zu erstellen und gleichzeitig die Modellqualität zu erhalten. Automatisiertes Machine Learning basiert auf einem Durchbruch der [Microsoft Research-Abteilung](https://www.microsoft.com/research/project/automl/).

Die Entwicklung traditioneller Machine Learning-Modelle ist ressourcenintensiv und erfordert viel Fachwissen und Zeit, um Dutzende von Modellen zu erstellen und zu vergleichen. Mit automatisiertem maschinellem Lernen verkürzen Sie die Zeit, die benötigt wird, um produktionsbereite ML-Modelle mit großer Leichtigkeit und Effizienz zu erhalten.

## <a name="when-to-use-automl-classify-regression--forecast"></a>Verwendung von AutoML: Klassifizieren, Regression und Prognose

Sie arbeiten mit automatisiertem ML, wenn Sie möchten, dass Azure Machine Learning mit der von Ihnen angegebenen Zielmetrik ein Modell für Sie trainiert und optimiert. Automatisiertes ML demokratisiert den Entwicklungsprozess eines Machine Learning-Modells und befähigt seine Benutzer, unabhängig von deren Data Science-Kenntnissen, eine durchgängige Machine Learning-Pipeline für jedes Problem zu bestimmen.

Data Scientists, Analysten und Entwickler aus den verschiedensten Branchen können automatisiertes ML für Folgendes verwenden:
+ Implementieren von ML-Lösungen ohne umfangreiche Programmierkenntnisse
+ Sparen von Zeit und Ressourcen
+ Nutzen von bewährten Methoden aus der Data Science
+ Bereitstellen flexibler Problemlösungen

### <a name="classification"></a>Klassifizierung

Die Klassifizierung ist ein gängiger Machine Learning-Task. Klassifizierung ist eine Art des überwachten Lernens, bei der Modelle anhand von Trainingsdaten lernen und diese Erkenntnisse auf neue Daten anwenden. Azure Machine Learning bietet Featurebereitstellungen speziell für diese Aufgaben, z. B. Textfeaturizer für Deep Neural Network zur Klassifizierung. Erfahren Sie mehr über die [Optionen für die Featurebereitstellung](how-to-configure-auto-features.md#featurization). 

Das Hauptziel von Klassifizierungsmodellen besteht darin, auf der Grundlage der Erkenntnisse aus den Trainingsdaten vorherzusagen, in welche Kategorien neue Daten fallen werden. Zu den gängigen Klassifizierungsbeispielen gehören Betrugserkennung, Handschrifterkennung und Objekterkennung. Weitere Informationen und ein Beispiel finden Sie unter [Erstellen eines Klassifizierungsmodells mit automatisiertem maschinellem Lernen](tutorial-first-experiment-automated-ml.md).

Weitere Beispiele für Klassifizierung und automatisiertes Machine Learning finden Sie in den folgenden Python-Notebooks: [Fraud Detection](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-credit-card-fraud/auto-ml-classification-credit-card-fraud.ipynb) (Betrugserkennung), [Marketing Prediction](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb) (Marketingprognose) und [Newsgroup Data Classification](https://towardsdatascience.com/automated-text-classification-using-machine-learning-3df4f4f9570b) (Klassifizierung von Diskussionsgruppendaten)

### <a name="regression"></a>Regression

Ähnlich der Klassifizierung sind Regressionsaufgaben auch ein gängiger überwachter Lerntask. Azure Machine Learning bietet [spezielle Featurisierungen für diesen Task](how-to-configure-auto-features.md#featurization).

Anders als bei der Klassifizierung, bei der die vorhergesagten Ausgabewerte kategorisch sind, sagen Regressionsmodelle numerische Ausgabewerte auf der Grundlage unabhängiger Vorhersagefaktoren voraus. Bei der Regression besteht das Ziel darin, die Beziehung zwischen diesen unabhängigen Vorhersagevariablen herzustellen, indem geschätzt wird, wie eine Variable die anderen beeinflusst. Beispiel: Der Fahrzeugpreis basierend auf Merkmalen wie Kraftstoffverbrauch, Sicherheitseinstufung, usw. Hier erhalten Sie weitere Informationen und ein Beispiel für die [Regression mit automatisiertem maschinellen Lernen](tutorial-auto-train-models.md).

Weitere Beispiele für Regression und automatisiertes Machine Learning für Vorhersagen finden Sie in den folgenden Python-Notebooks: [CPU-Leistungsvorhersage](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/regression-explanation-featurization/auto-ml-regression-explanation-featurization.ipynb), 

### <a name="time-series-forecasting"></a>Zeitreihenvorhersagen

Die Erstellung von Vorhersagen ist ein integraler Bestandteil jedes Unternehmens, unabhängig davon, ob es sich um Einnahmen, Lagerbestände, Umsätze oder Kundennachfrage handelt. Sie können automatisiertes maschinelles Lernen verwenden, um verschiedene Techniken und Ansätze zu kombinieren. Außerdem erhalten Sie dabei eine beliebte und hochwertige Zeitreihenprognose. Hier erhalten Sie weitere Informationen zur Vorgehensweise: [Automatisches Trainieren eines Modells für die Zeitreihenprognose](how-to-auto-train-forecast.md). 

Automatisierte Zeitreihenexperimente werden als multivariate Regressionsprobleme behandelt. Zeitreihenwerte aus der Vergangenheit werden „pivotiert“ und dienen so zusammen mit anderen Vorhersageelementen als zusätzliche Dimensionen für den Regressor. Dieser Ansatz hat im Gegensatz zu klassischen Zeitreihenmethoden den Vorteil, dass mehrere kontextbezogene Variablen und deren Beziehungen zueinander beim Training auf natürliche Weise integriert werden. Beim automatisierten maschinellen Lernen wird ein zwar einfaches, aber häufig in interne Verzweigungen unterteiltes Modell für alle Elemente im Dataset und in den Vorhersagehorizonten erlernt. Dadurch sind mehr Daten verfügbar, um Modellparameter zu schätzen, und die Generalisierung von unbekannten Reihen wird möglich.

Die erweiterte Vorhersagekonfiguration umfasst Folgendes:
* Feiertagserkennung und Erstellen zusätzlicher Merkmale (Featurization)
* Zeitreihen und DNN-Lernmodule (Auto-ARIMA, Prophet, ForecastTCN)
* Unterstützung vieler Modelle mithilfe von Gruppierungen
* Kreuzvalidierung mit rollierendem Ursprung (Rolling Origin Validation)
* Konfigurierbare Verzögerungen (Lags)
* Aggregierte Zeitfenstermerkmale (Rolling Window Features)


Weitere Beispiele für Regression und automatisiertes Machine Learning für Vorhersagen finden Sie in den folgenden Python-Notebooks: [Sales Forecasting](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-orange-juice-sales/auto-ml-forecasting-orange-juice-sales.ipynb) (Verkaufsprognose), [Demand Forecasting](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb) (Nachfrageprognose) und [Beverage Production Forecast](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb) (Getränkeproduktionsprognose).

## <a name="how-automl-works"></a>Funktionsweise von AutoML

Während des Trainings erstellt Azure Machine Learning parallel eine Reihe von Pipelines, die unterschiedliche Algorithmen und Parameter für Sie ausprobieren. Der Dienst durchläuft die ML-Algorithmen iterativ im Zusammenspiel mit der jeweiligen Featureauswahl, wobei für jede Iteration ein Modell mit einer Trainingsbewertung erzeugt wird. Je höher die Bewertung ist, desto besser wird das Modell als „passend“ für Ihre Daten angesehen.  Die Ausführung wird beendet, sobald die im Experiment definierten Beendigungskriterien erreicht werden. 

Mithilfe von **Azure Machine Learning** können Sie automatisierte ML-Trainingsexperimente mit den folgenden Schritten entwerfen und ausführen:

1. **Identifizieren des ML-Problems**, das gelöst werden soll: Klassifizierung, Vorhersage oder Regression.

1. **Wählen Sie, ob Sie das Python SDK oder die Studioweboberfläche verwenden möchten**: Erfahren Sie mehr über die Parität zwischen dem [Python SDK und der Studioweboberfläche](#parity).

   * Wenn Sie nur über eingeschränkte oder gar keine Erfahrung mit Code verfügen, testen Sie die Studioweboberfläche von Azure Machine Learning unter [https://ml.azure.com](https://ml.azure.com/).  
   * Informieren Sie sich als Python-Entwickler über das [Python SDK von Azure Machine Learning](how-to-configure-auto-train.md). 
    
1. **Angeben der Quelle und des Formats der bezeichneten Trainingsdaten**: NumPy-Arrays oder Pandas-Datenrahmen.

1. **Konfigurieren des Computeziels für das Modelltraining**, z.B. [lokaler Computer, Azure Machine Learning Computes, Remote-VMs oder Azure Databricks](how-to-set-up-training-targets.md).  Weitere Informationen zu automatisiertem Training [für eine Remoteressource](how-to-auto-train-remote.md).

1. **Konfigurieren der automatisierten Machine Learning-Parameter**, die die Anzahl der Iterationen über verschiedene Modelle, die Hyperparametereinstellungen, erweiterte Vorverarbeitung/Featurebereitstellung und die Metriken bestimmen, die bei der Ermittlung des besten Modells zu berücksichtigen sind.  
1. **Übermitteln der Trainingsausführung.**

1. **Überprüfen der Ergebnisse** 

Dieser Prozess wird anhand des folgenden Diagramms veranschaulicht. 
![Automatisiertes maschinelles Lernen](./media/concept-automated-ml/automl-concept-diagram2.png)


Sie können die protokollierten Ausführungsinformationen auch untersuchen. Sie enthalten die während der Ausführung [erfassten Metriken](how-to-understand-automated-ml.md). Bei der Trainingsausführung wird ein serialisiertes Python-Objekt (`.pkl`-Datei) generiert, das die Vorabverarbeitung des Modells und der Daten enthält.

Obwohl die Modellerstellung automatisiert ist, können Sie auch [ermitteln, wie wichtig oder relevant Features für die generierten Modelle sind](how-to-configure-auto-train.md#explain).

Erfahren Sie, wie Sie ein [Remotecomputeziel](how-to-auto-train-remote.md) verwenden.



> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2Xc9t]


## <a name="feature-engineering"></a>Featureentwicklung

Beim Feature Engineering werden Domänenkenntnisse der Daten zum Erstellen von Features verwendet, mit denen ML-Algorithmen besser lernen können. In Azure Machine Learning werden für das Feature Engineering Skalierungs- und Normalisierungstechniken angewendet. Zusammen werden diese Techniken und das Feature Engineering als Featurisierung bezeichnet.

Bei automatisierten Machine Learning-Experimenten wird die Featurisierung automatisch angewendet, sie kann aber auch basierend auf Ihren Daten angepasst werden. [Weitere Informationen zur enthaltenen Featurebereitstellung](how-to-configure-auto-features.md#featurization).  

> [!NOTE]
> Die Schritte zur Featurebereitstellung bei automatisiertem maschinellen Lernen (Featurenormalisierung, Behandlung fehlender Daten, Umwandlung von Text in numerische Daten usw.) werden Teil des zugrunde liegenden Modells. Bei Verwendung des Modells für Vorhersagen werden die während des Trainings angewendeten Schritte zur Featurebereitstellung automatisch auf Ihre Eingabedaten angewendet.

### <a name="automatic-featurization-standard"></a>Automatische Featurisierung (Standard)

In jedem automatisierten Machine Learning-Experiment werden Ihre Daten automatisch skaliert oder normalisiert, damit die Algorithmen gut funktionieren. Während des Modelltrainings wird eine der folgenden Skalierungs- oder Normalisierungstechniken auf jedes Modell angewendet. Erfahren Sie, wie AutoML dazu beiträgt, die [Überanpassung und Unausgeglichenheit von Daten](concept-manage-ml-pitfalls.md) in Ihren Modellen zu vermeiden.

|Skalierung&nbsp;&&nbsp;Normalisierung| BESCHREIBUNG |
| ------------- | ------------- |
| [StandardScaleWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.StandardScaler.html)  | Standardisieren von Features durch Entfernen des Mittelwerts und Skalierung auf Einheitenvarianz.  |
| [MinMaxScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MinMaxScaler.html)  | Transformieren von Features durch Skalieren jedes Features anhand des Mindest- und Maximalwerts dieser Spalte.  |
| [MaxAbsScaler](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MaxAbsScaler.html#sklearn.preprocessing.MaxAbsScaler) |Skalieren jedes Features anhand seines maximalen Absolutwerts. |
| [RobustScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.RobustScaler.html) |Diese Skalarfunktionen skaliert Features nach ihren Quantilbereich. |
| [PCA](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.PCA.html) |Lineare Dimensionalitätsreduzierung unter Verwendung von Singulärwertzerlegung der Daten, um sie auf einen niedrigeren Dimensionsbereich zu projizieren. |
| [TruncatedSVDWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.TruncatedSVD.html) |Diese Transformation führt Dimensionalitätsreduzierung durch abgeschnittene Singulärwertzerlegung (SVD) durch. Im Gegensatz zu PCA zentriert diese Schätzfunktion die Daten nicht vor der Berechnung der Singulärwertzerlegung, d. h., dass scipy.sparse-Matrizen effizient verwendet werden können. |
| [SparseNormalizer](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.Normalizer.html) | Jede Stichprobe (d.h. jede Zeile der Datenmatrix) mit mindestens einer Komponente ungleich Null wird unabhängig von anderen Stichproben neu skaliert, sodass ihre Norm (l1 oder l2) gleich eins ist. |

### <a name="customize-featurization"></a>Anpassen der Featurisierung

Es stehen auch weitere Feature Engineering-Techniken wie etwa Codierung und Transformationen zur Verfügung. 

Diese Einstellung kann aktiviert werden über:

+ Azure Machine Learning Studio: Aktivieren Sie die **Automatische Merkmalserstellung** im Abschnitt **Konfigurationsausführung** [mit diesen Schritten](how-to-use-automated-ml-for-ml-models.md#customize-featurization).

+ Python SDK: Geben Sie `"feauturization": 'auto' / 'off' / 'FeaturizationConfig'` in Ihrem Objekt vom Typ [AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) an. Weitere Informationen zur [Aktivierung der Featurisierung](how-to-configure-auto-features.md). 

## <a name="ensemble-models"></a><a name="ensemble"></a> Ensemblemodelle

Automatisiertes Machine Learning unterstützt Ensemblemodelle, die standardmäßig aktiviert sind. Das Lernen mit Ensembles verbessert die Ergebnisse des maschinellen Lernens und die Vorhersageleistung, da nicht einzelne Modelle verwendet, sondern mehrere Modelle kombiniert werden. Die Ensemble-Iterationen erfolgen als abschließende Iterationen Ihrer Ausführung. Automatisiertes Machine Learning verwendet die beiden Ensemble-Methoden „voting“ (Abstimmen) und „stacking“ (Stapeln) gemeinsam, um Modelle zu kombinieren:

* **Voting**: Trifft Vorhersagen auf Grundlage des gewichteten Durchschnitts der vorhergesagten Klassenwahrscheinlichkeiten (für Klassifizierungsaufgaben) oder auf Grundlage der vorhergesagten Regressionsziele (für Regressionsaufgaben).
* **Stacking**: Stacking kombiniert heterogene Modelle und trainiert ein Metamodell, basierend auf der Ausgabe der einzelnen Modelle. Die aktuellen Standardmetamodelle sind LogisticRegression für Klassifizierungsaufgaben und ElasticNet für Regressions-/Vorhersageaufgaben.

Der [Caruana-Algorithmus für die Ensembleauswahl](http://www.niculescu-mizil.org/papers/shotgun.icml04.revised.rev2.pdf) mit sortierter Ensembleinitialisierung wird verwendet, um zu entscheiden, welche Modell innerhalb des Ensembles verwendet werden sollen. Generell initialisiert dieser Algorithmus das Ensemble mit bis zu fünf Modellen mit den besten Einzelbewertungen und überprüft, ob diese Modelle innerhalb des 5 %-Schwellenwerts der besten Bewertung liegen, um ein schlechtes Ausgangsensemble zu vermeiden. Dann wird für jede Ensemble-Iteration ein neues Modell zum vorhandenen Ensemble hinzugefügt, und die resultierende Bewertung wird berechnet. Wenn ein neues Modell die vorhandene Ensemblebewertung verbessert hat, wird das Ensemble so aktualisiert, dass es das neue Modell aufnimmt.

Informationen zum Ändern der Standard-Ensembleeinstellungen beim automatisierten Machine Learning finden Sie unter [Gewusst wie:](how-to-configure-auto-train.md#ensemble).

## <a name="guidance-on-local-vs-remote-managed-ml-compute-targets"></a><a name="local-remote"></a>Leitfaden zu lokal verwalteten ML-Computezielen im Vergleich zu remote verwalteten ML-Computezielen

Die Weboberfläche für automatisiertes ML verwendet immer ein [Remotecomputeziel](concept-compute-target.md).  Wenn Sie jedoch das Python SDK verwenden, wählen Sie entweder ein lokales Computeziel oder ein Remotecomputeziel für automatisiertes ML-Training aus.

* **Lokales Computeziel:** Das Training erfolgt auf Ihrem lokalen Computer oder VM-Compute. 
* **Remotecomputeziel:** Das Training erfolgt auf Azure Machine Learning-Computeclustern.  

### <a name="choose-compute-target"></a>Auswählen eines Computeziels
Berücksichtigen Sie die folgenden Faktoren bei der Auswahl Ihres Computeziels:

 * **Wählen Sie ein lokales Computeziel aus:** Wenn Ihr Szenario anfängliche Untersuchungen oder Demos mithilfe weniger Daten und kurzen Trainingsprozessen (d. h. Sekunden oder wenige Minuten pro untergeordnete Ausführung) umfasst, kann sich das Training auf Ihrem lokalen Computer als besser erweisen.  Es gibt keine Einrichtungszeit, die Infrastrukturressourcen (Ihr Computer oder Ihre VM) sind sofort verfügbar.
 * **Wählen Sie ein Remote-ML-Computecluster aus**: Wenn Sie wie beim Erstellen von Modellen für Produktionstraining Training mit größeren Datasets durchführen, die längere Trainingszeiträume erfordern, bietet ein Remotecomputeziel eine bessere Gesamtdauer, da `AutoML` Trainingsprozesse über Clusterknoten hinweg parallelisiert. Auf einem Remotecomputeziel werden etwa 1,5 Minuten pro untergeordnete Ausführung durch die Startzeit für die interne Infrastruktur hinzugefügt. Außerdem werden weitere Minuten für die Clusterinfrastruktur addiert, wenn die VMs noch nicht ausgeführt werden.

### <a name="pros-and-cons"></a>Vor- und Nachteile
Wägen Sie die folgenden Vor- und Nachteile gegeneinander ab, wenn Sie zwischen einem lokalen und einem Remotecomputeziel entscheiden.

|  | Vorteile  |Nachteile  |
|---------|---------|---------|---------|
|**Lokales Computeziel** |  <li> Keine Startzeit für die Umgebung   | <li>  Teilmenge der Features<li>  Keine Parallelisierung der Ausführungen <li> Schlechter für große Datenmengen <li>Kein Datenstreaming während des Trainings <li>  Keine DNN-basierte Featurisierung <li> Nur Python SDK |
|**Remote-ML-Computecluster**|  <li> Alle Features <li> Parallelisierung untergeordneter Ausführungen <li>   Unterstützung großer Datasets<li>  DNN-basierte Featurisierung <li>  Dynamische Skalierbarkeit von Computeclustern nach Bedarf <li> Webbenutzeroberfläche zur Verwendung ohne Programmiererfahrung  |  <li> Startzeit für Clusterknoten <li> Startzeit für jede untergeordnete Ausführung    |

### <a name="feature-availability"></a>Verfügbarkeit von Funktionen 

 Wie in der folgenden Tabelle gezeigt stehen Ihnen mehr Features zur Verfügung, wenn Sie Remotecompute verwenden. 

| Funktion                                                    | Remote | Lokal | 
|------------------------------------------------------------|--------|-------|
| Datenstreaming (Unterstützung großer Datenmengen, bis zu 100 GB)          | ✓      |       | 
| DNN-BERT-basierte Textfeaturisierung und -training             | ✓      |       |
| Direkt einsatzbereite GPU-Unterstützung (Training und Rückschlüsse)        | ✓      |       |
| Unterstützung von Bildklassifizierung und Bezeichnungen                  | ✓      |       |
| Auto-ARIMA-, Prophet- und ForecastTCN-Modelle für Vorhersagen | ✓      |       | 
| Mehrere parallele Ausführungen/Iterationen                       | ✓      |       |
| Erstellen von Modellen mit Interpretierbarkeit in der AutoML Studio-Webbenutzeroberfläche      | ✓      |       |
| Anpassung des Feature Engineering in der AutoML Studio-Webbenutzeroberfläche| ✓      |       |
| Azure ML-Hyperparameteroptimierung                             | ✓      |       |
| Unterstützung von Azure ML-Pipelineworkflows                         | ✓      |       |
| Fortsetzen einer Ausführung                                             | ✓      |       |
| Vorhersagen                                                | ✓      | ✓     |
| Erstellen und Ausführen von Experimenten in Notebooks                    | ✓      | ✓     |
| Registrieren und Visualisieren der Informationen und Metriken von Experimenten auf der Benutzeroberfläche | ✓      | ✓     |
| Schutzmaßnahmen für Daten                                            | ✓      | ✓     |

## <a name="many-models"></a>Viele Modelle 

Der [Many Models Solution Accelerator](https://aka.ms/many-models) (Preview) (Projektmappenbeschleuniger für viele Modelle (Vorschau)) baut auf Azure Machine Learning auf und ermöglicht Ihnen die Verwendung des automatisierten maschinellen Lernens für Training, Betrieb und Verwaltung von hunderten oder sogar tausenden von Machine Learning-Modellen.

Die Erstellung eines Modells __für jede Instanz oder jede Einzelperson__ kann in den folgenden Szenarien z. B. zu verbesserten Ergebnissen führen:

* Vorhersage der Umsätze für jedes einzelne Geschäft
* Vorausschauende Wartung für Hunderte von Ölquellen
* Anpassen einer Erfahrung für einzelne Benutzer

## <a name="automl-in-azure-machine-learning"></a>AutoML in Azure Machine Learning

Azure Machine Learning bietet zwei Möglichkeiten für die Arbeit mit automatisiertem maschinellen Lernen:

* Für Kunden mit Codierungserfahrung, [Azure Machine Learning Python SDK](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py) 

* Für Kunden mit begrenzten oder keinen Codierungserfahrungen, Azure Machine Learning Studio unter [https://ml.azure.com](https://ml.azure.com/)  

<a name="parity"></a>

### <a name="experiment-settings"></a>Einstellungen für das Experiment 

Mit den folgenden Einstellungen können Sie Ihr Experiment für automatisiertes maschinelles Lernen konfigurieren. 

| |Python SDK|Die Studioweboberfläche|
----|:----:|:----:
|**Aufteilen der Daten in Trainings-/Validierungssätze**| ✓|✓
|**Unterstützt Aufgaben für maschinelles Lernen: Klassifizierung, Regression und Vorhersage**| ✓| ✓
|**Optimiert auf Grundlage der primären Metrik**| ✓| ✓
|**Unterstützt Azure ML-Compute als Computeziel** | ✓|✓
|**Konfigurieren des Vorhersagehorizonts, der Zielverzögerungen und des rollierenden Fensters**|✓|✓
|**Festlegen der Beendigungskriterien** |✓|✓ 
|**Festlegen gleichzeitiger Iterationen**| ✓|✓
|**Löschen von Spalten**| ✓|✓
|**Blockieren von Algorithmen**|✓|✓
|**Kreuzvalidierung im Vergleich** |✓|✓
|**Unterstützt das Training für Azure Databricks-Cluster**| ✓|
|**Anzeigen der Namen der technischen Features**|✓|
|**Featurezusammenfassung**| ✓|
|**Featurisierung für Feiertage**|✓|
|**Ausführlichkeitsgrade der Protokolldatei**| ✓|

### <a name="model-settings"></a>Modelleinstellungen

Diese Einstellungen können auf das beste Modell als Ergebnis Ihres Experiments für das automatisierte maschinelle Lernen angewendet werden.

| |Python SDK|Die Studioweboberfläche|
|----|:----:|:----:|
|**Registrierung, Bereitstellung, Erklärbarkeit des besten Modells**| ✓|✓|
|**Aktivieren von Abstimmungsensemble- und Stapelensemble-Modellen**| ✓|✓|
|**Anzeigen des besten Modells auf der Basis von nicht primärer Metrik**|✓||
|**Aktivieren/Deaktivieren der Kompatibilität des ONNX-Modells**|✓||
|**Testen des Modells** | ✓| |

### <a name="run-control-settings"></a>Ausführen der Steuerelementeinstellungen

Mit diesen Einstellungen können Sie Ihre Experimentausführungen und die Ausführungen der ihnen untergeordneten Elemente überprüfen und steuern. 

| |Python SDK|Die Studioweboberfläche|
|----|:----:|:----:|
|**Ausführen der Zusammenfassungstabelle**| ✓|✓|
|**Abbrechen von Ausführungen und untergeordneten Ausführungen**| ✓|✓|
|**Abrufen von Schutzmaßnahmen**| ✓|✓|
|**Anhalten und Fortsetzen von Ausführungen**| ✓| |

<a name="use-with-onnx"></a>

## <a name="automl--onnx"></a>AutoML und ONNX

Mit Azure Machine Learning können Sie automatisiertes ML verwenden, um ein Python-Modell zu erstellen und in das ONNX-Format zu konvertieren. Sobald die Modelle im ONNX-Format vorliegen, können sie auf einer Vielzahl von Plattformen und Geräten ausgeführt werden. Erfahren Sie mehr über das [Beschleunigen von ML-Modellen mit ONNX](concept-onnx.md).

Informationen zum Konvertieren in das ONNX-Format finden Sie in [diesem Jupyter Notebook-Beispiel](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb). Erfahren Sie, welche [Algorithmen in ONNX unterstützt werden](how-to-configure-auto-train.md#select-your-experiment-type).

Die ONNX-Runtime unterstützt auch C#, sodass Sie das erstellte Modell automatisch in Ihren C#-Apps verwenden können, ohne es neu codieren oder die Netzwerklatenzen in Kauf nehmen zu müssen, die REST-Endpunkte mit sich bringen. Erfahren Sie mehr über die [Verwendung eines AutoML ONNX-Modells in einer .NET-Anwendung mit ML.NET](./how-to-use-automl-onnx-model-dotnet.md) und das [Rückschließen von ONNX-Modellen mit der C#-API für die ONNX-Runtime](https://github.com/Microsoft/onnxruntime/blob/master/docs/CSharp_API.md). 

## <a name="next-steps"></a>Nächste Schritte

Es gibt mehrere Ressourcen, um Sie mit AutoML vertraut zu machen. 

### <a name="tutorials-how-tos"></a>Tutorials/Anleitungen
Tutorials sind einführende End-to-End-Beispiele für AutoML-Szenarien.
+ **Für ein Code First-Konzept** folgen Sie dem [Tutorial: Automatisches Trainieren eines Regressionsmodell mithilfe von Azure Machine Learning Python SDK](tutorial-auto-train-models.md).

 + **Für ein Konzept mit wenig Code oder ohne Code:** folgen Sie dem [Tutorial: Erstellen von automatisierten ML-Klassifizierungsmodellen mit Azure Machine Learning Studio](tutorial-first-experiment-automated-ml.md).

Anleitungsartikel bieten zusätzliche Informationen zu den Funktionen von AutoML. Beispiel: 

+ Konfigurieren der Einstellungen für automatische Trainingsexperimente
    + Verwenden Sie in Azure Machine Learning Studio [diese Schritte](how-to-use-automated-ml-for-ml-models.md). 
    + Mit dem Python SDK [verwenden Sie diese Schritte](how-to-configure-auto-train.md).

+  Erfahren Sie, wie automatisches Training mithilfe von Zeitreihendaten funktioniert, indem Sie [diese Schritte verwenden](how-to-auto-train-forecast.md).

### <a name="jupyter-notebook-samples"></a>Jupyter Notebook-Beispiele 

Überprüfen Sie detaillierte Codebeispiele und Anwendungsfälle im [GitHub-Notebook-Repository für Beispiele zum automatisierten maschinellen Lernen](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/).

### <a name="python-sdk-reference"></a>Referenz zum Python SDK

Vertiefen Sie Ihre Kenntnisse über SDK-Entwurfsmuster und Klassenspezifikationen mit der [AutoML-Klassenreferenzdokumentation](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig?preserve-view=true&view=azure-ml-py). 

> [!Note]
> Die Funktionen des automatisierten maschinellen Lernens sind auch in anderen Lösungen von Microsoft verfügbar: [ML.NET](/dotnet/machine-learning/automl-overview), [HDInsight](../hdinsight/spark/apache-spark-run-machine-learning-automl.md), [Power BI](/power-bi/service-machine-learning-automated) und [SQL Server](https://cloudblogs.microsoft.com/sqlserver/2019/01/09/how-to-automate-machine-learning-on-sql-server-2019-big-data-clusters/)