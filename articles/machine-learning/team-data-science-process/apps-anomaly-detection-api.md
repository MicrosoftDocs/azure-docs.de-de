---
title: 'Machine Learning-App: Anomaly Detection-API: Team Data Science-Prozess'
description: Die Anomaly Detection-API ist ein mit Microsoft Azure Machine Learning erstelltes Beispiel, das Anomalien in Zeitreihendaten erkennt, wenn die numerischen Daten zeitlich gleich verteilt sind.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=alokkirpal, previous-ms.author=alok
ms.openlocfilehash: e210c1683d5f14181bc0549e73a892eb91d2e746
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93305695"
---
# <a name="machine-learning-anomaly-detection-api"></a>Machine Learning Anomaly Detection-API

> [!NOTE]
> Dieses Element wird zurzeit gewartet. Wir empfehlen Ihnen, den [API-Dienst zur Anomalieerkennung](https://azure.microsoft.com/services/cognitive-services/anomaly-detector/) zu verwenden, der von einem Katalog von Machine Learning-Algorithmen unter Azure Cognitive Services unterstützt wird, um Anomalien in Unternehmens-, operative und IoT-Metriken zu erkennen.

## <a name="overview"></a>Übersicht
Die [Anomaly Detection-API](https://gallery.azure.ai/MachineLearningAPI/Anomaly-Detection-2) ist ein mit Azure Machine Learning erstelltes Beispiel, das Anomalien in Zeitreihendaten erkennt, wenn die numerischen Daten zeitlich gleich verteilt sind.

Diese API kann die folgenden Arten anomaler Muster in Zeitreihendaten erkennen:

* **Positive und negative Trends** : Bei der Überwachung der Arbeitsspeichernutzung kann ein Aufwärtstrend in Bezug auf das Computing interessant sein, da dies ein Hinweis auf einen Speicherverlust sein kann.
* **Änderungen in den dynamischen Wertebereich** : Bei der Überwachung der durch einen Clouddienst ausgelösten Ausnahmen können Änderungen im dynamischen Wertebereich z.B. auf eine Instabilität der Dienstintegrität hinweisen.
* **Spitzen und Abfälle** : Beim Überwachen der Anzahl von fehlerhaften Anmeldeversuchen bei einem Dienst oder der Anzahl von Auscheckvorgängen in einer E-Commerce-Website könnten Spitzen oder Abfälle z.B. auf anormales Verhalten hinweisen.

Diese Machine Learning-Erkennungselemente verfolgen diese Art von Wertänderungen in Abhängigkeit der Zeit und melden fortlaufende Änderungen der Werte als Anomaliebewertungen. Sie benötigen keine Ad-hoc-Schwellenwertanpassung, und ihre Bewertungen können verwendet werden, um falsch positive Raten zu steuern. Die Anomaly Detection-API ist beispielsweise in den folgenden Szenarios nützlich: Dienstüberwachung durch Nachverfolgen von KPIs in Abhängigkeit der Zeit, Nutzungsüberwachung anhand von Metriken wie der Anzahl von Suchvorgängen oder der Anzahl von Klicks, Leistungsüberwachung anhand von Indikatoren wie Arbeitsspeicher, CPU, Dateilesevorgänge usw. in Abhängigkeit der Zeit.

Das Anomaly Detection-Angebot verfügt über nützliche Tools für die ersten Schritte.

* Mit der [Webanwendung](https://anomalydetection-aml.azurewebsites.net/) können Sie die Ergebnisse von Anomaly Detection-APIs für Ihre Daten auswerten und visualisieren.

> [!NOTE]
> Probieren Sie die **IT Anomaly Insights-Lösung** , die von [dieser API](https://gallery.azure.ai/MachineLearningAPI/Anomaly-Detection-2) unterstützt wird.
>
<!-- This Solution is no longer available
> To get this end to end solution deployed to your Azure subscription <a href="https://gallery.cortanaintelligence.com/Solution/Anomaly-Detection-Pre-Configured-Solution-1" target="_blank">**Start here >**</a>
-->

## <a name="api-deployment"></a>API-Bereitstellung
Um die API zu verwenden, müssen Sie sie für Ihr Azure-Abonnement bereitstellen, wo sie als Azure Machine Learning-Webdienst gehostet wird.  Sie können dies über den [Azure KI-Katalog](https://gallery.azure.ai/MachineLearningAPI/Anomaly-Detection-2) vornehmen.  Hiermit werden Ihrem Azure-Abonnement zwei (klassische) Azure Machine Learning Studio-Webdienste (und die mit ihnen verknüpften Ressourcen) bereitgestellt – einer für die Erkennung von Anomalien mit Saisonabhängigkeitserkennung und ein weiterer ohne Saisonabhängigkeitserkennung.  Sobald die Bereitstellung abgeschlossen ist, können Sie die APIs auf der Seite [ (klassische) Azure Machine Learning Studio-Webdienste](https://services.azureml.net/webservices/) verwalten.  Von dieser Seite aus finden Sie Ihre Endpunkt-Speicherorte und API-Schlüssel sowie Beispielcodes für den API-Aufruf.  Ausführlichere Anweisungen finden Sie [hier](../classic/manage-new-webservice.md).

## <a name="scaling-the-api"></a>Skalieren der API
Standardmäßig enthält der kostenlose Dev/Test-Abrechnungsplan Ihrer Bereitstellung 1.000 Transaktionen/Monat und zwei Computestunden/Monat.  Sie können nach Ihren Bedürfnissen auf einen anderen Plan aktualisieren.  Informationen zu den Preisen verschiedener Pläne finden Sie [hier](https://azure.microsoft.com/pricing/details/machine-learning/) unter „Produktions-Web-API-Preise“.

## <a name="managing-aml-plans"></a>Verwalten von AML-Plänen
Sie können Ihren Abrechnungsplan [hier](https://services.azureml.net/plans/) verwalten.  Der Name des Plans basiert auf dem Namen der Ressourcengruppe, die Sie bei Bereitstellung der API ausgewählt haben, plus einer Zeichenfolge, die für Ihr Abonnement eindeutig ist.  Anweisungen zum Aktualisieren Ihres Plans stehen [hier](../classic/manage-new-webservice.md) im Abschnitt „Managing billing plans“ (Verwalten von Abrechnungsplänen) zur Verfügung.

## <a name="api-definition"></a>API-Definition
Der Webdienst stellt eine REST-basierte API über HTTPS bereit, die auf unterschiedliche Weise genutzt werden kann, beispielsweise durch eine Web- oder mobile Anwendung, R, Python, Excel usw.  Sie senden Zeitreihendaten über einen REST-API-Aufruf an diesen Dienst, und dann wird eine Kombination der unten beschriebenen drei Anomaliearten ausgeführt.

## <a name="calling-the-api"></a>Aufrufen der API
Um die API aufrufen zu können, müssen Sie die Endpunktposition und den API-Schlüssel kennen.  Diese beiden Anforderungen sind zusammen mit dem Beispielcode für das Aufrufen der API auf der Seite der [Azure Machine Learning Studio-Webdienste (Classic)](https://services.azureml.net/webservices/) verfügbar.  Navigieren Sie zu der gewünschten API, und klicken Sie dann auf die Registerkarte „Nutzen“, um sie zu finden.  Sie können die API als Swagger-API (d. h. mit dem URL-Parameter `format=swagger`) oder als Nicht-Swagger-API (d. h. ohne `format`-URL-Parameter) aufrufen.  Im Beispielcode wird das Swagger-Format verwendet.  Im Folgenden finden Sie ein Beispiel für eine Anforderung und Antwort im Nicht-Swagger-Format.  Diese Beispiele gelten für den Saisonabhängigkeits-Endpunkt.  Der Nicht-Saisonabhängigkeits-Endpunkt ist ähnlich.

### <a name="sample-request-body"></a>Inhalt der Beispielanforderung
Die Anforderung enthält zwei Objekte: `Inputs` und `GlobalParameters`.  In der folgenden Beispielanforderung werden einige Parameter explizit gesendet, andere hingegen nicht (scrollen Sie nach unten, um eine vollständige Liste von Parametern für jeden Endpunkt anzuzeigen).  Parameter, die nicht explizit in der Anforderung gesendet werden, verwenden die unten angegebenen Standardwerte.

```json
{
            "Inputs": {
                    "input1": {
                            "ColumnNames": ["Time", "Data"],
                            "Values": [
                                    ["5/30/2010 18:07:00", "1"],
                                    ["5/30/2010 18:08:00", "1.4"],
                                    ["5/30/2010 18:09:00", "1.1"]
                            ]
                    }
            },
    "GlobalParameters": {
        "tspikedetector.sensitivity": "3",
        "zspikedetector.sensitivity": "3",
        "bileveldetector.sensitivity": "3.25",
        "detectors.spikesdips": "Both"
    }
}
```

### <a name="sample-response"></a>Beispiel für eine Antwort
Um das `ColumnNames`-Feld zu sehen, müssen Sie `details=true` als URL-Parameter in Ihre Anforderung einbeziehen.  In den folgenden Tabellen werden die Bedeutungen dieser Felder aufgeführt.

```json
{
    "Results": {
        "output1": {
            "type": "table",
            "value": {
                "Values": [
                    ["5/30/2010 6:07:00 PM", "1", "1", "0", "0", "-0.687952590518378", "0", "-0.687952590518378", "0", "-0.687952590518378", "0"],
                    ["5/30/2010 6:08:00 PM", "1.4", "1.4", "0", "0", "-1.07030497733224", "0", "-0.884548154298423", "0", "-1.07030497733224", "0"],
                    ["5/30/2010 6:09:00 PM", "1.1", "1.1", "0", "0", "-1.30229513613974", "0", "-1.173800281031", "0", "-1.30229513613974", "0"]
                ],
                "ColumnNames": ["Time", "OriginalData", "ProcessedData", "TSpike", "ZSpike", "BiLevelChangeScore", "BiLevelChangeAlert", "PosTrendScore", "PosTrendAlert", "NegTrendScore", "NegTrendAlert"],
                "ColumnTypes": ["DateTime", "Double", "Double", "Double", "Double", "Double", "Int32", "Double", "Int32", "Double", "Int32"]
            }
        }
    }
}
```


## <a name="score-api"></a>Score-API
Die Score-API wird zum Ausführen der Anomaly Detection (Anomalieerkennung) für nicht saisonabhängige Zeitreihendaten verwendet. Die API führt mehrere Anomalieerkennungsmodule für die Daten aus und gibt jeweils die Anomaliebewertungen zurück.
Die folgende Abbildung enthält ein Beispiel für Anomalien, die von der Score-API erkannt werden können. Diese Zeitreihe weist zwei unterschiedliche Pegeländerungen und drei Spitzen auf. Die roten Punkte zeigen die Zeit an, zu der die Pegeländerung erkannt wird, während die schwarzen Punkte die erkannten Spitzen anzeigen.
![Score-API][1]

### <a name="detectors"></a>Erkennungsmodule
Die Anomaly Detection-API unterstützt Erkennungsmodule in drei allgemeinen Kategorien. Details zu bestimmten Eingabeparametern und Ausgaben für jedes Erkennungsmodul finden Sie in der folgenden Tabelle.

| Kategorie des Erkennungsmoduls | Erkennungsmodul | BESCHREIBUNG | Eingabeparameter | Ausgaben |
| --- | --- | --- | --- | --- |
| Spitzenerkennungsmodule |TSpike-Erkennungsmodul |Erkennen von Spitzen und Abfällen basierend auf der Entfernung der Werte vom ersten und dritten Quartil |*tspikedetector.Sensitivity:* nimmt einen Integerwert im Bereich von 1 bis 10 an, Standardwert: 3. Höhere Werte erfassen extremere Werte und führen zu weniger Empfindlichkeit. |TSpike: Binärwerte – „1“, wenn eine Spitze oder ein Abfall erkannt wird, andernfalls „0“. |
| Spitzenerkennungsmodule | ZSpike-Erkennungsmodul |Erkennen von Spitzen und Abfällen basierend auf der Entfernung der Datenpunkte von ihrem Mittelwert |*zspikedetector.Sensitivity:* nimmt einen Integerwert im Bereich von 1 bis 10 an, Standardwert: 3. Höhere Werte erfassen extremere Werte und führen zu weniger Empfindlichkeit. |ZSpike: Binärwerte – „1“, wenn eine Spitze oder ein Abfall erkannt wird, andernfalls „0“. |
| Erkennungsmodul für langsame Trends |Erkennungsmodul für langsame Trends |Erkennen von langsamen positiven Trends anhand der festgelegten Empfindlichkeit |*trenddetector.Sensitivity:* Schwellenwert für den Erkennungswert (Standardwert: 3,25. 3,25 bis 5 ist ein geeigneter Bereich für die Auswahl. Je höher der Wert ist, desto geringer ist die Empfindlichkeit). |tscore: Gleitzahl, die für die Anomaliebewertung des Trends steht. |
| Erkennungsmodule für Pegeländerungen | Erkennungsmodul für bidirektionale Pegeländerungen |Erkennen von Pegeländerungen in Aufwärts- und Abwärtsrichtung anhand der festgelegten Empfindlichkeit |*bileveldetector.Sensitivity:* Schwellenwert für den Erkennungswert (Standardwert: 3,25. 3,25 bis 5 ist ein geeigneter Bereich für die Auswahl. Je höher der Wert ist, desto geringer ist die Empfindlichkeit). |rpscore: Gleitzahl, die für die Anomaliebewertung von Pegeländerungen in Aufwärts- und Abwärtsrichtung steht. |

### <a name="parameters"></a>Parameter
Weitere ausführliche Informationen zu diesen Eingabeparametern sind in der folgenden Tabelle aufgeführt:

| Eingabeparameter | BESCHREIBUNG | Standardeinstellung | type | Gültiger Bereich | Vorgeschlagener Bereich |
| --- | --- | --- | --- | --- | --- |
| detectors.historywindow |Verwendeter Verlauf (in Anzahl von Datenpunkten) für die Anomalieberechnung |500 |integer |10 - 2.000 |Von Zeitreihe abhängig |
| detectors.spikesdips | Ob nur Spikes, nur Dips oder beides erkannt werden soll |Beide |enumerated |Both, Spikes, Dips |Beide |
| bileveldetector.sensitivity |Empfindlichkeit für Erkennungsmodul für bidirektionale Pegeländerungen. |3.25 |double |Keine |3,25 - 5 (niedrigerer Wert = höhere Empfindlichkeit) |
| trenddetector.sensitivity |Empfindlichkeit für Erkennungsmodul für positive Trends |3.25 |double |Keine |3,25 - 5 (niedrigerer Wert = höhere Empfindlichkeit) |
| tspikedetector.sensitivity |Empfindlichkeit für TSpike-Erkennungsmodul |3 |integer |1 - 10 |3 - 5 (niedrigerer Wert = höhere Empfindlichkeit) |
| zspikedetector.sensitivity |Empfindlichkeit für ZSpike-Erkennungsmodul |3 |integer |1 - 10 |3 - 5 (niedrigerer Wert = höhere Empfindlichkeit) |
| postprocess.tailRows |Anzahl von aktuellen Datenpunkten, die in den Ausgabeergebnissen beibehalten werden sollen |0 |integer |0 (alle Datenpunkte beibehalten), oder geben Sie die Anzahl von Punkten an, die in den Ergebnissen beibehalten werden sollen |– |

### <a name="output"></a>Output
Die API führt alle Erkennungsmodule für Ihre Zeitreihendaten aus und gibt für jeden Zeitpunkt Anomaliebewertungen und binäre Spitzenindikatoren zurück. In der Tabelle unten sind die Ausgaben der API aufgeführt.

| Ausgaben | BESCHREIBUNG |
| --- | --- |
| Time |Zeitstempel aus Rohdaten oder aggregierte (und/oder) zugeordnete Daten, wenn Aggregation (und/oder) die Zuordnung fehlender Daten angewendet wird |
| Daten |Werte aus Rohdaten, oder aggregierte (und/oder) zugeordnete Daten, wenn Aggregation (und/oder) die Zuordnung fehlender Daten angewendet wird |
| TSpike |Binärer Indikator, um anzugeben, ob eine Spitze vom TSpike-Erkennungsmodul erkannt wird |
| ZSpike |Binärer Indikator, um anzugeben, ob eine Spitze vom ZSpike-Erkennungsmodul erkannt wird |
| rpscore |Gleitzahl, die für die Anomaliebewertung von bidirektionalen Pegeländerungen steht |
| rpalert |1/0-Wert, der basierend auf der Eingabeempfindlichkeit angibt, dass eine bidirektionale Pegeländerung vorhanden ist |
| tscore |Gleitzahl, die für die Anomaliebewertung positiver Trends steht |
| talert |1/0-Wert, der basierend auf der Eingabeempfindlichkeit angibt, dass eine positive Trendanomalie vorhanden ist |

## <a name="scorewithseasonality-api"></a>ScoreWithSeasonality-API
Die ScoreWithSeasonality-API wird zum Durchführen der Anomalieerkennung für Zeitreihen verwendet, die über saisonale Muster verfügen. Diese API ist nützlich, um Abweichungen in saisonalen Mustern zu erkennen.
Die folgende Abbildung enthält ein Beispiel für Anomalien, die in einer saisonalen Zeitreihe erkannt wurden. Die Zeitreihe weist eine Spitze (erster schwarzer Punkt), zwei Abfälle (zweiter schwarzer Punkt und ein Punkt am Ende) und eine Pegeländerung (roter Punkt) auf. Sowohl der Abfall in der Mitte der Zeitreihe als auch die Pegeländerung sind erst erkennbar, nachdem die saisonalen Komponenten aus der Zeitreihe entfernt wurden.
![Saisonabhängigkeits-API][2]

### <a name="detectors"></a>Erkennungsmodule
Die Detektoren im Saisonabhängigkeits-Endpunkt ähneln denen im Nicht-Saisonabhängigkeits-Endpunkt, jedoch mit etwas anderen Parameternamen (siehe unten).

### <a name="parameters"></a>Parameter

Weitere ausführliche Informationen zu diesen Eingabeparametern sind in der folgenden Tabelle aufgeführt:

| Eingabeparameter | BESCHREIBUNG | Standardeinstellung | type | Gültiger Bereich | Vorgeschlagener Bereich |
| --- | --- | --- | --- | --- | --- |
| preprocess.aggregationInterval |Aggregationsintervall in Sekunden zum Aggregieren von Eingabezeitreihen |0 (keine Aggregation) |integer |0: Aggregation überspringen, andernfalls > 0 |5 Minuten bis 1 Tag, von Zeitreihe abhängig |
| preprocess.aggregationFunc |Funktion zum Aggregieren von Daten im angegebenen AggregationInterval |mean |enumerated |mean, sum, length |– |
| preprocess.replaceMissing |Werte zum Zuordnen fehlender Daten |lkv (last known value) |enumerated |zero, lkv, mean |– |
| detectors.historywindow |Verwendeter Verlauf (in Anzahl von Datenpunkten) für die Anomalieberechnung |500 |integer |10 - 2.000 |Von Zeitreihe abhängig |
| detectors.spikesdips | Ob nur Spikes, nur Dips oder beides erkannt werden soll |Beide |enumerated |Both, Spikes, Dips |Beide |
| bileveldetector.sensitivity |Empfindlichkeit für Erkennungsmodul für bidirektionale Pegeländerungen. |3.25 |double |Keine |3,25 - 5 (niedrigerer Wert = höhere Empfindlichkeit) |
| postrenddetector.sensitivity |Empfindlichkeit für Erkennungsmodul für positive Trends |3.25 |double |Keine |3,25 - 5 (niedrigerer Wert = höhere Empfindlichkeit) |
| negtrenddetector.sensitivity |Empfindlichkeit für Erkennungsmodul für negative Trends |3.25 |double |Keine |3,25 - 5 (niedrigerer Wert = höhere Empfindlichkeit) |
| tspikedetector.sensitivity |Empfindlichkeit für TSpike-Erkennungsmodul |3 |integer |1 - 10 |3 - 5 (niedrigerer Wert = höhere Empfindlichkeit) |
| zspikedetector.sensitivity |Empfindlichkeit für ZSpike-Erkennungsmodul |3 |integer |1 - 10 |3 - 5 (niedrigerer Wert = höhere Empfindlichkeit) |
| seasonality.enable |Gibt an, ob eine saisonabhängige Analyse durchgeführt wird. |true |boolean |true, false |Von Zeitreihe abhängig |
| seasonality.numSeasonality |Maximale Anzahl von zu erkennenden periodischen Zyklen |1 |integer |1, 2 |1 - 2 |
| seasonality.transform |Gibt an, ob saisonale (und) Trendkomponenten entfernt werden sollen, bevor die Anomalieerkennung angewendet wird. |deseason |enumerated |none, deseason, deseasontrend |– |
| postprocess.tailRows |Anzahl von aktuellen Datenpunkten, die in den Ausgabeergebnissen beibehalten werden sollen |0 |integer |0 (alle Datenpunkte beibehalten), oder geben Sie die Anzahl von Punkten an, die in den Ergebnissen beibehalten werden sollen |– |

### <a name="output"></a>Output
Die API führt alle Erkennungsmodule für Ihre Zeitreihendaten aus und gibt für jeden Zeitpunkt Anomaliebewertungen und binäre Spitzenindikatoren zurück. In der Tabelle unten sind die Ausgaben der API aufgeführt.

| Ausgaben | BESCHREIBUNG |
| --- | --- |
| Time |Zeitstempel aus Rohdaten oder aggregierte (und/oder) zugeordnete Daten, wenn Aggregation (und/oder) die Zuordnung fehlender Daten angewendet wird |
| OriginalData |Werte aus Rohdaten, oder aggregierte (und/oder) zugeordnete Daten, wenn Aggregation (und/oder) die Zuordnung fehlender Daten angewendet wird |
| ProcessedData |Eine der folgenden Optionen: <ul><li>Saisonabhängig angepasste Zeitreihen, wenn eine signifikante Saisonabhängigkeit erkannt und die Option „deseason“ gewählt wurde.</li><li>Saisonabhängig angepasste und trendbereinigte Zeitreihen, wenn eine signifikante Saisonabhängigkeit erkannt und die Option „deseasontrend“ gewählt wurde</li><li>Andernfalls ist diese Option identisch mit „OriginalData“</li> |
| TSpike |Binärer Indikator, um anzugeben, ob eine Spitze vom TSpike-Erkennungsmodul erkannt wird |
| ZSpike |Binärer Indikator, um anzugeben, ob eine Spitze vom ZSpike-Erkennungsmodul erkannt wird |
| BiLevelChangeScore |Gleitzahl, die für die Anomaliebewertung von Pegeländerungen steht |
| BiLevelChangeAlert |1/0-Wert, der basierend auf der Eingabeempfindlichkeit angibt, dass eine Pegeländerungsanomalie vorhanden ist |
| PosTrendScore |Gleitzahl, die für die Anomaliebewertung positiver Trends steht |
| PosTrendAlert |1/0-Wert, der basierend auf der Eingabeempfindlichkeit angibt, dass eine positive Trendanomalie vorhanden ist |
| NegTrendScore |Gleitzahl, die für die Anomaliebewertung negativer Trends steht |
| NegTrendAlert |1/0-Wert, der basierend auf der Eingabeempfindlichkeit angibt, dass eine negative Trendanomalie vorhanden ist |

[1]: ./media/apps-anomaly-detection-api/anomaly-detection-score.png
[2]: ./media/apps-anomaly-detection-api/anomaly-detection-seasonal.png