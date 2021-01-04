---
title: Analysieren und Überwachen von Datasets auf Datendrift (Vorschau)
titleSuffix: Azure Machine Learning
description: Erstellen Sie Monitore für Azure Machine Learning-Datasets (Vorschau), überwachen Sie die Datasets auf Datendrift, und richten Sie Warnungen ein.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: sgilley
ms.author: copeters
author: lostmygithubaccount
ms.date: 06/25/2020
ms.topic: conceptual
ms.custom: how-to, data4ml
ms.openlocfilehash: 04882c71a2d80e01029dd0a8b476f21a658e632b
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93359594"
---
# <a name="detect-data-drift-preview-on-datasets"></a>Erkennen von Datendrift (Vorschau) in Datasets


> [!IMPORTANT]
> Das Erkennen von Datendrift in Datasets befindet sich derzeit in der öffentlichen Vorschauphase.
> Die Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Hier erfahren Sie, wie Sie Datendrift überwachen und Warnungen für starke Drift festlegen.  

Azure Machine Learning-Datasetmonitore (Vorschau) ermöglichen Folgendes:
* **Analysieren der Drift in Ihren Daten** , um zu verstehen, wie diese sich im Lauf der Zeit ändern.
* **Überwachen von Modelldaten** auf Änderungen zwischen Trainings- und Nutzungsdatasets.  Beginnen Sie mit dem [Sammeln von Daten für Modelle in der Produktion](how-to-enable-data-collection.md).
* **Überwachen von neuen Daten** auf Änderungen zwischen Baseline- und Zieldatasets.
* **Erstellen von Profilen für Features in Daten** , um nachzuverfolgen, wie sich statistische Eigenschaften im Lauf der Zeit ändern.
* **Einrichten von Warnungen zur Datendrift** , um bei potenziellen Problemen frühzeitig eine Benachrichtigung zu erhalten. 

Für die Monitorerstellung wird ein [Azure Machine Learning-Dataset](how-to-create-register-datasets.md) verwendet. Das Dataset muss eine Zeitstempelspalte enthalten.

Datendriftmetriken können mit dem Python SDK oder in Azure Machine Learning Studio angezeigt werden.  Andere Metriken und Erkenntnisse sind über die Ressource [Azure Application Insights](../azure-monitor/app/app-insights-overview.md) verfügbar, die mit dem Azure Machine Learning-Arbeitsbereich verknüpft ist.

## <a name="prerequisites"></a>Voraussetzungen

Um Datasetmonitore zu erstellen und zu nutzen, benötigen Sie Folgendes:
* Ein Azure-Abonnement. Wenn Sie nicht über ein Azure-Abonnement verfügen, können Sie ein kostenloses Konto erstellen, bevor Sie beginnen. Probieren Sie die [kostenlose oder kostenpflichtige Version von Azure Machine Learning](https://aka.ms/AMLFree) noch heute aus.
* Ein [Azure Machine Learning-Arbeitsbereich](how-to-manage-workspace.md).
* Eine [Installation des Azure Machine Learning-SDK für Python](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py), in dem das Paket „azureml-datasets“ enthalten ist.
* Strukturierte (tabellarische) Daten mit einem Zeitstempel im Dateipfad, im Dateinamen oder in einer Spalte in den Daten.

## <a name="what-is-data-drift"></a>Was sind Datenabweichungen?

Datendrift ist einer der Hauptgründe für nachlassende Modellgenauigkeit.  Für Machine Learning-Modelle ist Datendrift die Veränderung von Modelleingabedaten, die eine Verschlechterung der Modellleistung zur Folge hat.  Die Datendriftüberwachung trägt zur Erkennung solcher Modellleistungsprobleme bei.

Folgende Aspekte zählen zu den Gründen für Datendrift:

- Vorgelagerte Prozessänderungen, z. B. durch einen ausgetauschten Sensor, der die Maßeinheiten von Zoll zu Zentimeter ändert. 
- Probleme mit der Datenqualität, z. B. durch einen defekten Sensor, der immer den Messwert 0 liefert.
- Natürliche Abweichungen in den Daten, z. B. durch Änderungen der mittleren Temperatur in den Jahreszeiten.
- Änderungen in der Beziehung zwischen Features oder Kovariantenabweichungen. 

Azure Machine Learning vereinfacht die Drifterkennung durch die Berechnung einer einzelnen Metrik und die Abstrahierung der Komplexität der verglichenen Datasets.  Diese Datasets können hunderte Features und mehrere zehntausend Zeilen umfassen. Wurde eine Drift erkannt, können Sie mittels Drilldown ermitteln, auf welche Features sie zurückzuführen ist.  Anschließend können Sie Metriken auf der Featureebene untersuchen, um die Grundursache für die Drift zu debuggen und zu isolieren.

Dieser Top-Down-Ansatz ermöglicht im Gegensatz zu herkömmlichen regelbasierten Techniken eine unkomplizierte Datenüberwachung. Regelbasierte Techniken wie zulässige Datenbereiche oder zulässige eindeutige Werte können zeitaufwändig und fehleranfällig sein.

In Azure Machine Learning werden Datasetmonitore zur Erkennung von Datendrift sowie zur Ausgabe entsprechender Warnungen verwendet.
  
### <a name="dataset-monitors"></a>Datasetmonitore 

Ein Datasetmonitor ermöglicht Folgendes:

* Erkennen von Datendrift bei neuen Daten in einem Dataset und Ausgeben entsprechender Warnungen
* Analysieren historischer Daten auf Drift
* Erstellen eines Profils für neue Daten im Zeitverlauf

Der Datendriftalgorithmus misst Änderungen von Daten allgemein und bietet Hinweise darauf, welche Features die Ursache sind, damit diese genauer untersucht werden können. Datasetmonitore generieren eine Reihe weiterer Metriken, indem für neue Daten im Dataset `timeseries` ein Profil erstellt wird. 

Über [Azure Application Insights](../azure-monitor/app/app-insights-overview.md) können benutzerdefinierte Warnungen für alle vom Monitor generierten Metriken eingerichtet werden. Datasetmonitore können verwendet werden, um Datenprobleme schnell zu erfassen und den Zeitraum bis zum Beheben des Problems zu verkürzen, indem wahrscheinliche Ursachen ermittelt werden.  

Aus konzeptioneller Sicht gibt es drei primäre Szenarien für die Einrichtung von Datasetmonitoren in Azure Machine Learning.

Szenario | Beschreibung
---|---
Überwachen der Nutzungsdaten eines Modells auf Drift gegenüber den Trainingsdaten | Angesichts der Tatsache, dass die Modellgenauigkeit abnimmt, wenn die Nutzungsdaten von den Trainingsdaten abweichen, können die Ergebnisse dieses Szenarios als Überwachung eines Proxys für die Modellgenauigkeit interpretiert werden.
Überwachen eines Zeitreihendatasets auf Drift gegenüber einem vorherigen Zeitraum | Dieses Szenario ist allgemeiner und kann zum Überwachen von Datasets verwendet werden, die an Prozessen vor oder nach der Modellerstellung beteiligt sind.  Das Zieldataset muss über eine Zeitstempelspalte verfügen. Das Baselinedataset kann ein beliebiges tabellarische Dataset sein, das über gemeinsame Features mit dem Zieldataset verfügt.
Analysieren älterer Daten | Dieses Szenario hilft dabei, frühere Daten zu verstehen und fundierte Entscheidungen für die Einstellungen von Datasetmonitoren zu treffen.

Datasetmonitore sind von folgenden Azure-Diensten abhängig:

|Azure-Dienst  |Beschreibung  |
|---------|---------|
| *Dataset* | Drift verwendet Machine Learning-Datasets, um Trainingsdaten abzurufen und Daten für das Modelltraining zu vergleichen.  Die Datenprofilgenerierung wird verwendet, um einige der gemeldeten Metriken (Mindestwerte, Maximalwerte, unterschiedliche Werte, Anzahl unterschiedlicher Werte) zu generieren. |
| *azureml-Pipeline und Compute* | Der Driftberechnungsauftrag wird in der azureml-Pipeline gehostet.  Der Auftrag wird bei Bedarf oder zeitplangesteuert ausgelöst und auf einer Computeressource ausgeführt, die bei der Erstellung der Driftüberwachung konfiguriert wurde.
| *Application Insights*| Drift gibt Metriken an Application Insights aus, die zum Machine Learning-Arbeitsbereich gehören.
| *Azure Blob Storage*| Drift gibt Metriken im JSON-Format an Azure Blob Storage aus.

## <a name="how-dataset-monitors-data"></a>Datenüberwachung durch das Dataset

Verwenden Sie Machine Learning-Datasets für die Überwachung auf Datendrift. Geben Sie ein Baselinedataset an (üblicherweise das Trainingsdataset für ein Modell). Ein Zieldataset – in der Regel Modelleingabedaten – wird im Lauf der Zeit mit dem Baselinedataset verglichen. Dieser Vergleich bedeutet, dass im Zieldataset eine Zeitstempelspalte angegeben sein muss.

## <a name="create-target-dataset"></a>Erstellen des Zieldatasets

Für das Zieldataset muss das Merkmal `timeseries`festgelegt sein. Hierzu muss die Zeitstempelspalte angegeben werden – entweder auf der Grundlage einer Spalte in den Daten oder auf der Grundlage einer aus dem Pfadmuster der Dateien abgeleiteten virtuellen Spalte. Erstellen Sie das Dataset mit einem Zeitstempel per [Python SDK](#sdk-dataset) oder in [Azure Machine Learning Studio](#studio-dataset). Eine Spalte, die einen Zeitstempel darstellt, muss angegeben werden, um dem Dataset das Merkmal `timeseries` hinzuzufügen. Wenn Ihre Daten in einer Ordnerstruktur mit Zeitinformationen partitioniert sind (beispielsweise „{JJJJ/MM/TT}“), können Sie über die Einstellung für das Pfadmuster eine virtuelle Spalte erstellen und als Partitionszeitstempel festlegen, um die Bedeutung der Zeitreihenfunktion zu erhöhen.

### <a name="python-sdk"></a><a name="sdk-dataset"></a>Python SDK

Die Methode [`with_timestamp_columns()`](/python/api/azureml-core/azureml.data.tabulardataset?preserve-view=true&view=azure-ml-py#&preserve-view=truewith-timestamp-columns-timestamp-none--partition-timestamp-none--validate-false----kwargs-) der Klasse [`Dataset`](/python/api/azureml-core/azureml.data.tabulardataset?preserve-view=true&view=azure-ml-py#&preserve-view=truewith-timestamp-columns-timestamp-none--partition-timestamp-none--validate-false----kwargs-) dient zum Definieren der Zeitstempelspalte für das Dataset.

```python 
from azureml.core import Workspace, Dataset, Datastore

# get workspace object
ws = Workspace.from_config()

# get datastore object 
dstore = Datastore.get(ws, 'your datastore name')

# specify datastore paths
dstore_paths = [(dstore, 'weather/*/*/*/*/data.parquet')]

# specify partition format
partition_format = 'weather/{state}/{date:yyyy/MM/dd}/data.parquet'

# create the Tabular dataset with 'state' and 'date' as virtual columns 
dset = Dataset.Tabular.from_parquet_files(path=dstore_paths, partition_format=partition_format)

# assign the timestamp attribute to a real or virtual column in the dataset
dset = dset.with_timestamp_columns('date')

# register the dataset as the target dataset
dset = dset.register(ws, 'target')
```

Ein vollständiges Beispiel für die Verwendung des `timeseries`-Merkmals von Datasets finden Sie im [Beispiel-Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/timeseries-datasets/tabular-timeseries-dataset-filtering.ipynb) oder in der [Dokumentation zum SDK für das Dataset](/python/api/azureml-core/azureml.data.tabulardataset?preserve-view=true&view=azure-ml-py#&preserve-view=truewith-timestamp-columns-timestamp-none--partition-timestamp-none--validate-false----kwargs-).

### <a name="azure-machine-learning-studio"></a><a name="studio-dataset"></a>Azure Machine Learning Studio

Wenn Sie Ihr Dataset mithilfe des Azure Machine Learning-Studios erstellen, stellen Sie sicher, dass der Pfad zu Ihren Daten Zeitstempelinformationen enthält, schließen Sie alle Unterordner mit Daten ein, und legen Sie das Partitionsformat fest.

Im folgenden Beispiel werden alle Daten im Unterordner *NoaaIsdFlorida/2019* einbezogen, und das Partitionsformat gibt Jahr, Monat und Tag des Zeitstempels an.

[![Partitionsformat](./media/how-to-monitor-datasets/partition-format.png)](media/how-to-monitor-datasets/partition-format-expand.png)

Geben Sie in den Einstellungen für das **Schema** die Zeitstempelspalte anhand einer virtuellen oder realen Spalte im angegebenen Dataset an:

:::image type="content" source="media/how-to-monitor-datasets/timestamp.png" alt-text="Festlegen des Zeitstempels":::

Wenn Ihre Daten wie hier nach Datum partitioniert sind, können Sie auch den Zeitstempel der Partition angeben.  Dies ermöglicht eine effizientere Datumsverarbeitung.

:::image type="content" source="media/how-to-monitor-datasets/timeseries-partitiontimestamp.png" alt-text="Zeitstempel der Partition":::


## <a name="create-dataset-monitors"></a>Erstellen von Datasetmonitoren

Erstellen Sie Datasetmonitore, um Datendrift bei einem neuen Dataset zu erkennen und eine entsprechende Warnung auszugeben.  Verwenden Sie entweder das [Python SDK](#sdk-monitor) oder [Azure Machine Learning Studio](#studio-monitor).

### <a name="python-sdk"></a><a name="sdk-monitor"></a>Python SDK

Vollständige Informationen finden Sie in der [Python SDK-Referenzdokumentation zur Datendrift](/python/api/azureml-datadrift/azureml.datadrift). 

Im folgenden Beispiel wird gezeigt, wie ein Datasetmonitor mit dem Python SDK erstellt wird.

```python
from azureml.core import Workspace, Dataset
from azureml.datadrift import DataDriftDetector
from datetime import datetime

# get the workspace object
ws = Workspace.from_config()

# get the target dataset
dset = Dataset.get_by_name(ws, 'target')

# set the baseline dataset
baseline = target.time_before(datetime(2019, 2, 1))

# set up feature list
features = ['latitude', 'longitude', 'elevation', 'windAngle', 'windSpeed', 'temperature', 'snowDepth', 'stationName', 'countryOrRegion']

# set up data drift detector
monitor = DataDriftDetector.create_from_datasets(ws, 'drift-monitor', baseline, target, 
                                                      compute_target='cpu-cluster', 
                                                      frequency='Week', 
                                                      feature_list=None, 
                                                      drift_threshold=.6, 
                                                      latency=24)

# get data drift detector by name
monitor = DataDriftDetector.get_by_name(ws, 'drift-monitor')

# update data drift detector
monitor = monitor.update(feature_list=features)

# run a backfill for January through May
backfill1 = monitor.backfill(datetime(2019, 1, 1), datetime(2019, 5, 1))

# run a backfill for May through today
backfill1 = monitor.backfill(datetime(2019, 5, 1), datetime.today())

# disable the pipeline schedule for the data drift detector
monitor = monitor.disable_schedule()

# enable the pipeline schedule for the data drift detector
monitor = monitor.enable_schedule()
```

Ein vollständiges Beispiel für die Einrichtung eines `timeseries`-Datasets und einer Datendrifterkennung finden Sie in unserem [Beispiel-Notebook](https://aka.ms/datadrift-notebook).

### <a name="azure-machine-learning-studio"></a><a name="studio-monitor"></a> Azure Machine Learning Studio

1. Navigieren Sie zur [Studio-Homepage](https://ml.azure.com).
1. Wählen Sie die Registerkarte **Datasets** auf der linken Seite aus. 
1. Wählen Sie **Datasetmonitore** aus.
   ![Monitorliste](./media/how-to-monitor-datasets/monitor-list.png)

1. Klicken Sie auf die Schaltfläche **+ Monitor erstellen** , und fahren Sie mit dem Assistenten fort, indem Sie auf **Weiter** klicken.  

:::image type="content" source="media/how-to-monitor-datasets/wizard.png" alt-text="Assistent zum Erstellen von Monitoren":::

* **Zieldataset auswählen** :  Das Zieldataset ist ein tabellarisches Dataset mit angegebener Zeitstempelspalte, das auf Datendrift hin analysiert wird. Das Zieldataset muss gemeinsame Features mit dem Baselinedataset aufweisen, und es sollte sich um ein `timeseries`-Dataset handeln, an das neue Daten angefügt werden. Es können Daten der Vergangenheit im Zieldataset analysiert oder neue Daten überwacht werden.

* **Baselinedataset auswählen** :  Wählen Sie das tabellarische Dataset aus, das als Baseline für den Vergleich mit dem Zieldataset im Zeitverlauf verwendet werden soll.  Das Baselinedataset muss gemeinsame Features mit dem Zieldataset aufweisen.  Wählen Sie einen Zeitbereich aus, um ein Segment des Zieldatasets zu verwenden, oder geben Sie ein separates Dataset an, das als Baseline verwendet werden soll.

* **Monitoreinstellungen** :  Diese Einstellungen gelten für die geplante Datasetmonitor-Pipeline, die erstellt wird. 

    | Einstellung | Beschreibung | Tipps | Veränderlich | 
    | ------- | ----------- | ---- | ------- |
    | Name | Name des Datasetmonitors. | | Nein |
    | Features | Liste der Features, die im Hinblick auf Datendrift im Lauf der Zeit analysiert werden. | Legen Sie diese Einstellung auf die Ausgabefeatures eines Modells fest, um eine konzeptionelle Drift zu messen. Schließen Sie keine Features ein, für die im Laufe der Zeit eine natürliche Drift auftritt (Monat, Jahr, Index usw.). Nach dem Anpassen der Featureliste können Sie einen Abgleich für alle vorhandenen Datendriftmonitore durchführen. | Ja | 
    | Computeziel | Azure Machine Learning-Computeziel zur Ausführung der Aufträge des Datasetmonitors. | | Ja | 
    | Aktivieren | Aktivieren oder Deaktivieren des Zeitplans in der Pipeline des Datasetmonitors. | Deaktivieren Sie den Zeitplan, um Daten der Vergangenheit mithilfe der Abgleicheinstellung zu analysieren. Die Einstellung kann nach dem Erstellen des Datasetmonitors aktiviert werden. | Ja | 
    | Häufigkeit | Die Häufigkeit, die zum Planen des Pipelineauftrags und zum Analysieren von Daten der Vergangenheit verwendet wird, wenn ein Abgleich durchgeführt wird. Verfügbare Optionen sind „täglich“, „wöchentlich“ oder „monatlich“. | Bei jeder Ausführung werden Daten im Zieldataset entsprechend der Häufigkeit verglichen: <li>Täglich: Der letzte vollständige Tag im Zieldataset wird mit der Baseline verglichen. <li>Wöchentlich: Die letzte vollständige Woche (Montag bis Sonntag) im Zieldataset wird mit der Baseline verglichen. <li>Monatlich: Der letzte vollständige Monat im Zieldataset wird mit der Baseline verglichen. | Nein | 
    | Latency | Der Zeitraum in Stunden, der verstreicht, bis Daten im Dataset eintreffen. Wenn es z. B. drei Tage dauert, bis Daten in der SQL-Datenbank eintreffen, die vom Dataset gekapselt wird, legen Sie die Latenz auf den Wert 72 fest. | Die Einstellung kann nach dem Erstellen des Datasetmonitors nicht geändert werden. | Nein | 
    | E-Mail-Adressen | E-Mail-Adressen, an die Warnungen gesendet werden, wenn eine Verletzung des Schwellenwerts für den Prozentsatz der Datendrift ermittelt wird. | Das Senden der E-Mails erfolgt über Azure Monitor. | Ja | 
    | Schwellenwert | Schwellenwert für den Prozentsatz der Datendrift für Warnungen per E-Mail. | Weitere Warnungen und Ereignisse können in vielen weiteren Metriken in der dem Arbeitsbereich zugeordneten Application Insights-Ressource festgelegt werden. | Ja |

Nach Abschluss des Assistenten wird der resultierende Datasetmonitor in der Liste angezeigt. Wählen Sie diesen Eintrag aus, um zur Detailseite für den Monitor zu gelangen.

## <a name="understand-data-drift-results"></a>Verstehen der Datendriftergebnisse

In diesem Abschnitt finden Sie die Ergebnisse der Überwachung eines Datasets, die in Azure Studio unter **Datasets** / **Dataset-Monitore** bereitgestellt werden.  Auf dieser Seite können Sie die Einstellungen aktualisieren sowie bereits vorhandene Daten für einen bestimmten Zeitraum analysieren.  

Beginnen Sie mit den ersten Erkenntnissen hinsichtlich der Größenordnung der Datendrift und einer Übersicht über die wichtigsten Features, die weiter untersucht werden sollten.

:::image type="content" source="media/how-to-monitor-datasets/drift-overview.png" alt-text="Driftübersicht":::


| Metrik | Beschreibung | 
| ------ | ----------- | 
| Größenordnung der Datendrift | Ein Driftprozentsatz zwischen Baseline- und Zieldataset im Zeitverlauf. Der Wert liegt zwischen 0 und 100, wobei 0 für identische Datasets steht und 100 bedeutet, dass die beiden Datasets durch das Datendriftmodell von Azure Machine Learning vollständig voneinander unterschieden werden können. Aufgrund der Machine Learning-Techniken, die zum Generieren dieser Größenordnung verwenden werden, ist ein gewisses Maß an Ungenauigkeit beim gemessenen Prozentsatz zu erwarten. | 
| Wichtigste Features mit Drift | Zeigt die Features aus dem Dataset an, die den größten Driftumfang aufweisen und daher am meisten zur Driftumfangsmetrik beitragen. Aufgrund von Kovariantenabweichungen muss sich die zugrunde liegende Verteilung eines Features nicht notwendigerweise ändern, um eine relativ hohe Featurerelevanz aufzuweisen. |
| Schwellenwert | Wenn die Datendriftgröße den festgelegten Schwellenwert übersteigt, werden Warnungen ausgelöst. Dies kann in den Überwachungseinstellungen konfiguriert werden. | 

### <a name="drift-magnitude-trend"></a>Trend des Driftumfangs

Hier sehen Sie, wie sehr sich das Dataset im angegebenen Zeitraum vom Zieldataset unterscheidet.  Je näher der Wert bei 100 Prozent liegt, desto stärker unterscheiden sich die beiden Datasets.

:::image type="content" source="media/how-to-monitor-datasets/drift-magnitude.png" alt-text="Trend des Driftumfangs":::

### <a name="drift-magnitude-by-features"></a>Driftausmaß nach Features

Dieser Abschnitt enthält Erkenntnisse auf Featureebene zu Änderungen an der Verteilung des ausgewählten Features sowie weitere statistische Daten im Zeitverlauf.

Für das Zieldataset wird auch ein Profil im Zeitverlauf erstellt. Der statistische Abstand zwischen der Baselineverteilung der einzelnen Features wird mit dem zeitlichen Verlauf des Zieldatasets verglichen.  Dies entspricht konzeptionell der Datendriftgröße.  Der statistische Abstand gilt jedoch für ein einzelnes Feature, nicht für alle Features. Mindest-, Maximal- und Mittelwerte sind ebenfalls verfügbar.

Klicken Sie in Azure Machine Learning Studio auf einen Balken des Diagramms, um die Details auf der Featureebene für das entsprechende Datum anzuzeigen. Standardmäßig werden die Verteilung des Baselinedatasets und die Verteilung der letzten Ausführung für dieses Feature angezeigt.

:::image type="content" source="media/how-to-monitor-datasets/drift-by-feature.gif" alt-text="Driftausmaß nach Features":::

Diese Metriken können auch im Python SDK mithilfe der `get_metrics()`-Methode in einem `DataDriftDetector`-Objekt abgerufen werden.

### <a name="feature-details"></a>Featuredetails

Scrollen Sie abschließend nach unten, um Details zu den einzelnen Features anzuzeigen.  Verwenden Sie die Dropdownlisten über dem Diagramm, um das Feature auszuwählen, und wählen Sie außerdem die Metrik aus, die Sie anzeigen möchten.

:::image type="content" source="media/how-to-monitor-datasets/numeric-feature.gif" alt-text="Numerisches Featurediagramm und Vergleich":::

Die Metriken im Diagramm hängen von der Art des Features ab.

* Numerische Features

    | Metrik | Beschreibung |  
    | ------ | ----------- |  
    | Wasserstein-Distanz | Der Mindestarbeitsaufwand, der für die Transformation der Baselineverteilung in die Zielverteilung erforderlich ist. |
    | Mittelwert | Durchschnittlicher Wert des Features. |
    | Mindestwert | Minimaler Wert des Features. |
    | Maximalwert | Maximaler Wert des Features. |

* Kategorische Features
    
    | Metrik | Beschreibung |  
    | ------ | ----------- |  
    | Euklidischer Abstand     |  Berechnet für Kategoriespalten.  Der euklidische Abstand wird für zwei Vektoren berechnet, die auf der Grundlage der empirischen Verteilung der gleichen Kategoriespalte aus zwei Datasets generiert wurden.  „0“ gibt an, dass es keinen Unterschied bei den empirischen Verteilungen gibt.    Je weiter der Wert von „0“ entfernt ist, desto stärker ist der Drift der Spalte.  Trends können anhand eines Zeitreihendiagramm dieser Metrik ermittelt werden und bei der Identifizierung eines Features mit Drift hilfreich sein.  |
    | Eindeutige Werte | Anzahl eindeutiger Werte (Kardinalität) des Features. |

Wählen Sie in diesem Diagramm ein einzelnes Datum aus, um die Featureverteilung zwischen dem Ziel und diesem Datum für das angezeigte Feature zu vergleichen. Für numerische Features werden zwei Wahrscheinlichkeitsverteilungen angezeigt.  Ist das Feature numerisch, wird ein Balkendiagramm angezeigt.

:::image type="content" source="media/how-to-monitor-datasets/select-date-to-compare.gif" alt-text="Auswählen eines Datums für den Vergleich mit dem Ziel":::

## <a name="metrics-alerts-and-events"></a>Metriken, Warnungen und Ereignisse

Metriken können in der [Azure Application Insights](../azure-monitor/app/app-insights-overview.md)-Ressource abgefragt werden, die Ihrem Machine Learning-Arbeitsbereich zugeordnet ist. Sie haben Zugriff auf alle Features von Application Insights, z. B. auch zur Einrichtung von benutzerdefinierten Warnungsregeln und Aktionsgruppen zum Auslösen einer Aktion wie E-Mail/SMS/Pushübertragung/Sprachnachricht oder einer Azure-Funktion. Ausführlichere Informationen hierzu finden Sie in der vollständigen Application Insights-Dokumentation. 

Navigieren Sie zunächst zum [Azure-Portal](https://portal.azure.com), und wählen Sie die Seite **Übersicht** Ihres Arbeitsbereichs aus.  Die zugeordnete Application Insights-Ressource befindet sich ganz rechts:

[![Azure-Portal – Übersicht](./media/how-to-monitor-datasets/ap-overview.png)](media/how-to-monitor-datasets/ap-overview-expanded.png)

Wählen Sie im linken Bereich unter „Überwachung“ die Option „Protokolle (Analytics)“ aus:

![Application Insights-Übersicht](./media/how-to-monitor-datasets/ai-overview.png)

Die Metriken der Datasetüberwachung werden als `customMetrics` gespeichert. Sie können eine Abfrage schreiben und ausführen, nachdem Sie eine Überwachung per Datasetmonitor eingerichtet haben, um die Metriken anzuzeigen:

[![Log Analytics-Abfrage](./media/how-to-monitor-datasets/simple-query.png)](media/how-to-monitor-datasets/simple-query-expanded.png)

Erstellen Sie eine neue Warnungsregel, nachdem Sie die Metriken für die Einrichtung von Warnungsregeln identifiziert haben:

![Neue Warnungsregel](./media/how-to-monitor-datasets/alert-rule.png)

Sie können eine vorhandene oder eine neue Aktionsgruppe verwenden, um die Aktion zu definieren, die durchgeführt werden soll, wenn die festgelegten Bedingungen erfüllt sind:

![Neue Aktionsgruppe](./media/how-to-monitor-datasets/action-group.png)

## <a name="next-steps"></a>Nächste Schritte

* Fahren Sie mit dem [Azure Machine Learning-Studio](https://ml.azure.com) oder dem [Python-Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datadrift-tutorial/datadrift-tutorial.ipynb) fort, um einen Datasetmonitor zu erstellen.
* Informieren Sie sich, wie Sie die Datendrift für [in Azure Kubernetes Service bereitgestellte Modelle](./how-to-enable-data-collection.md) einrichten.
* Richten Sie Datendriftüberwachungen mit [Ereignisraster](how-to-use-event-grid.md) ein. 
* Sollten Probleme auftreten, sehen Sie sich die allgemeinen [Tipps zur Problembehandlung](resource-known-issues.md#data-drift) an.