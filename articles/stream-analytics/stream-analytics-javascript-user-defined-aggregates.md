---
title: Benutzerdefinierte JavaScript-Aggregate in Azure Stream Analytics
description: In diesem Artikel wird beschrieben, wie Sie erweiterte Abfragemechanismen mit benutzerdefinierten JavaScript-Aggregaten in Azure Stream Analytics durchführen.
author: rodrigoaatmicrosoft
ms.author: rodrigoa
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 10/28/2017
ms.custom: devx-track-js
ms.openlocfilehash: 8891bb5cd3b8bd8ae9e02c871d6d0ffe42078c0b
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93124763"
---
# <a name="azure-stream-analytics-javascript-user-defined-aggregates"></a>Azure Stream Analytics – benutzerdefinierte JavaScript-Aggregate
 
Azure Stream Analytics unterstützt in JavaScript geschriebene, benutzerdefinierte Aggregate (UDA), mit deren Hilfe Sie komplexe zustandsbehaftete Geschäftslogik implementieren können. Innerhalb von UDA haben Sie die vollständige Kontrolle über die Zustandsdatenstruktur, die Zustandsakkumulation/-dekumulation und die Berechnung des Aggregatergebnisses. Der Artikel beschreibt die beiden unterschiedlichen JavaScript-UDA-Schnittstellen, Schritte zum Erstellen eines UDA sowie die Verwendung von UDA mit Windows-basierten Vorgängen in Stream Analytics-Abfragen.

## <a name="javascript-user-defined-aggregates"></a>Benutzerdefinierte Aggregate in JavaScript

Ein benutzerdefiniertes Aggregat wird im Rahmen einer Zeitfensterspezifikation verwendet, um die Ereignisse in diesem Fenster zu aggregieren und einen einzelnen Ergebniswert zu erzeugen. Es gibt zwei Typen von UDA-Schnittstellen, die zurzeit von Stream Analytics unterstützt werden: AccumulateOnly und AccumulateDeaccumulate. Beide Typen von UDA können mit rollierenden, springenden, gleitenden und Sitzungsfenstern verwendet werden. AccumulateDeaccumulate-UDA bietet für springende, gleitende und Sitzungsfenster eine bessere Leistung als AccumulateOnly-UDA. Sie wählen anhand des verwendeten Algorithmus zwischen den beiden Typen.

### <a name="accumulateonly-aggregates"></a>AccumulateOnly-Aggregate

AccumulateOnly Aggregate können nur neue Ereignisse für den Zustand akkumulieren, eine Dekumulation von Werten lässt der Algorithmus nicht zu. Wählen Sie diesen Aggregattyp aus, wenn eine Dekumulation der Informationen zu einem Ereignis aus dem Zustandswert nicht implementiert werden kann. Im Folgenden finden Sie die JavaScript-Vorlage für AccumulateOnly-Aggregate:

```JavaScript
// Sample UDA which state can only be accumulated.
function main() {
    this.init = function () {
        this.state = 0;
    }

    this.accumulate = function (value, timestamp) {
        this.state += value;
    }

    this.computeResult = function () {
        return this.state;
    }
}
```

### <a name="accumulatedeaccumulate-aggregates"></a>AccumulateDeaccumulate-Aggregate

AccumulateDeaccumulate Aggregate ermöglichen die Dekumulation eines zuvor akkumulierten Werts aus dem Zustand, z.B. das Entfernen eines Schlüssel-Wert-Paars aus einer Liste von Ereigniswerte oder das Subtrahieren eines Werts von einem Zustand der aggregierten Summe. Im Folgenden finden Sie die JavaScript-Vorlage für AccumulateDeaccumulate-Aggregate:

```JavaScript
// Sample UDA which state can be accumulated and deaccumulated.
function main() {
    this.init = function () {
        this.state = 0;
    }

    this.accumulate = function (value, timestamp) {
        this.state += value;
    }

    this.deaccumulate = function (value, timestamp) {
        this.state -= value;
    }

    this.deaccumulateState = function (otherState){
        this.state -= otherState.state;
    }

    this.computeResult = function () {
        return this.state;
    }
}
```

## <a name="uda---javascript-function-declaration"></a>UDA – JavaScript-Funktionsdeklaration

Jedes JavaScript-UDA wird durch eine Funktionsobjektdeklaration definiert. Es folgen die Hauptelemente einer UDA-Definition.

### <a name="function-alias"></a>Funktionsalias

Der Funktionsalias ist der UDA-Bezeichner. Verwenden Sie beim Aufrufen in einer Stream Analytics-Abfrage immer den UDA-Alias zusammen mit „uda.“ als .

### <a name="function-type"></a>Funktionstyp

Für UDA muss der Funktionstyp **Javascript UDA** sein.

### <a name="output-type"></a>Ausgabetyp

Eine bestimmter vom Stream Analytics-Auftrag unterstützter Typ oder „Any“, wenn Sie den Typ in Ihrer Abfrage behandeln möchten.

### <a name="function-name"></a>Funktionsname

Der Name dieses Funktionsobjekts. Der Funktionsname muss mit dem UDA-Alias identisch sein.

### <a name="method---init"></a>Methode: init()

Die init()-Methode initialisiert den Zustand des Aggregats. Diese Methode wird beim Starten des Fensters aufgerufen.

### <a name="method--accumulate"></a>Methode: accumulate()

Die accumulate()-Methode berechnet den UDA-Zustand basierend auf dem vorherigen Zustand und den aktuellen Ereigniswerten. Diese Methode wird aufgerufen, wenn ein Ereignis in ein Zeitfenster (TUMBLINGWINDOW, HOPPINGWINDOW, SLIDINGWINDOW oder SESSIONWINDOW) eintritt.

### <a name="method--deaccumulate"></a>Methode: deaccumulate()

Die deaccumulate()-Methode berechnet den Zustand basierend auf dem vorherigen Zustand und den aktuellen Ereigniswerten. Diese Methode wird aufgerufen, wenn ein Ereignis ein SLIDINGWINDOW oder SESSIONWINDOW verlässt.

### <a name="method--deaccumulatestate"></a>Methode: deaccumulateState()

Die deaccumulateState()-Methode berechnet den Zustand basierend auf dem vorherigen Zustand und dem Zustand eines Hops. Diese Methode wird aufgerufen, wenn ein Satz von Ereignissen ein HOPPINGWINDOW verlässt.

### <a name="method--computeresult"></a>Methode: computeResult()

Die computeResult()-Methode gibt das aggregierte Ergebnis basierend auf dem aktuellen Zustand zurück. Diese Methode wird am Ende eines Zeitfensters (TUMBLINGWINDOW, HOPPINGWINDOW, SLIDINGWINDOW oder SESSIONWINDOW) aufgerufen.

## <a name="javascript-uda-supported-input-and-output-data-types"></a>Von JavaScript-UDA unterstützte Eingabe- und Ausgabedatentypen
Informationen zu JavaScript-UDA-Datentypen finden Sie im Abschnitt **Stream Analytics- und JavaScript-Typkonvertierung** unter [Integrieren von JavaScript-UDFs](stream-analytics-javascript-user-defined-functions.md).

## <a name="adding-a-javascript-uda-from-the-azure-portal"></a>Hinzufügen eines JavaScript-UDA über das Azure-Portal

Im Folgenden finden Sie eine exemplarische Vorgehensweise für die Erstellung eines UDA über das Portal. Im hier verwendeten Beispiel werden die zeitlich gewichteten Durchschnittswerte berechnet.

Nun erstellen wir anhand der folgenden Schritte ein JavaScript-UDA unter einem vorhandenen ASA-Auftrag.

1. Melden Sie sich beim Azure-Portal an, und suchen Sie Ihren vorhandenen Stream Analytics-Auftrag.
1. Klicken Sie auf den Funktionslink unter **AUFTRAGSTOPOLOGIE**.
1. Klicken Sie auf das Symbol **Hinzufügen** , um eine neue Funktion hinzuzufügen.
1. Wählen Sie in der Ansicht „Neue Funktion“ den Funktionstyp **JavaScript-UDA** aus. Daraufhin wird im Editor eine UDA-Standardvorlage angezeigt.
1. Geben Sie „TWA“ als UDA-Alias ein, und ändern Sie die Funktionsimplementierung wie im Folgenden angegeben:

    ```JavaScript
    // Sample UDA which calculate Time-Weighted Average of incoming values.
    function main() {
        this.init = function () {
            this.totalValue = 0.0;
            this.totalWeight = 0.0;
        }

        this.accumulate = function (value, timestamp) {
            this.totalValue += value.level * value.weight;
            this.totalWeight += value.weight;

        }

        // Uncomment below for AccumulateDeaccumulate implementation
        /*
        this.deaccumulate = function (value, timestamp) {
            this.totalValue -= value.level * value.weight;
            this.totalWeight -= value.weight;
        }

        this.deaccumulateState = function (otherState){
            this.state -= otherState.state;
            this.totalValue -= otherState.totalValue;
            this.totalWeight -= otherState.totalWeight;
        }
        */

        this.computeResult = function () {
            if(this.totalValue == 0) {
                result = 0;
            }
            else {
                result = this.totalValue/this.totalWeight;
            }
            return result;
        }
    }
    ```

1. Sobald Sie auf die Schaltfläche „Speichern“ klicken, wird das UDA in der Liste der Funktionen angezeigt.

1. Wenn Sie auf die neue Funktion „TWA“ klicken, können Sie die Funktionsdefinition überprüfen.

## <a name="calling-javascript-uda-in-asa-query"></a>Aufrufen des JavaScript-UDA in einer ASA-Abfrage

Öffnen Sie Ihren Auftrag im Azure-Portal, bearbeiten Sie die Abfrage, und rufen Sie die TWA()-Funktion mit dem obligatorischen Präfix „uda.“ auf. Beispiel:

```SQL
WITH value AS
(
    SELECT
    NoiseLevelDB as level,
    DurationSecond as weight
FROM
    [YourInputAlias] TIMESTAMP BY EntryTime
)
SELECT
    System.Timestamp as ts,
    uda.TWA(value) as NoseDoseTWA
FROM value
GROUP BY TumblingWindow(minute, 5)
```

## <a name="testing-query-with-uda"></a>Testen einer Abfrage mit einem UDA

Erstellen Sie eine lokale JSON-Datei mit dem nachstehenden Inhalt, laden Sie die Datei in einen Stream Analytics-Auftrag hoch, und testen Sie die vorstehende Abfrage.

```JSON
[
  {"EntryTime": "2017-06-10T05:01:00-07:00", "NoiseLevelDB": 80, "DurationSecond": 22.0},
  {"EntryTime": "2017-06-10T05:02:00-07:00", "NoiseLevelDB": 81, "DurationSecond": 37.8},
  {"EntryTime": "2017-06-10T05:02:00-07:00", "NoiseLevelDB": 85, "DurationSecond": 26.3},
  {"EntryTime": "2017-06-10T05:03:00-07:00", "NoiseLevelDB": 95, "DurationSecond": 13.7},
  {"EntryTime": "2017-06-10T05:03:00-07:00", "NoiseLevelDB": 88, "DurationSecond": 10.3},
  {"EntryTime": "2017-06-10T05:05:00-07:00", "NoiseLevelDB": 103, "DurationSecond": 5.5},
  {"EntryTime": "2017-06-10T05:06:00-07:00", "NoiseLevelDB": 99, "DurationSecond": 23.0},
  {"EntryTime": "2017-06-10T05:07:00-07:00", "NoiseLevelDB": 108, "DurationSecond": 1.76},
  {"EntryTime": "2017-06-10T05:07:00-07:00", "NoiseLevelDB": 79, "DurationSecond": 17.9},
  {"EntryTime": "2017-06-10T05:08:00-07:00", "NoiseLevelDB": 83, "DurationSecond": 27.1},
  {"EntryTime": "2017-06-10T05:09:00-07:00", "NoiseLevelDB": 91, "DurationSecond": 17.1},
  {"EntryTime": "2017-06-10T05:09:00-07:00", "NoiseLevelDB": 115, "DurationSecond": 7.9},
  {"EntryTime": "2017-06-10T05:09:00-07:00", "NoiseLevelDB": 80, "DurationSecond": 28.3},
  {"EntryTime": "2017-06-10T05:10:00-07:00", "NoiseLevelDB": 55, "DurationSecond": 18.2},
  {"EntryTime": "2017-06-10T05:10:00-07:00", "NoiseLevelDB": 93, "DurationSecond": 25.8},
  {"EntryTime": "2017-06-10T05:11:00-07:00", "NoiseLevelDB": 83, "DurationSecond": 11.4},
  {"EntryTime": "2017-06-10T05:12:00-07:00", "NoiseLevelDB": 89, "DurationSecond": 7.9},
  {"EntryTime": "2017-06-10T05:15:00-07:00", "NoiseLevelDB": 112, "DurationSecond": 3.7},
  {"EntryTime": "2017-06-10T05:15:00-07:00", "NoiseLevelDB": 93, "DurationSecond": 9.7},
  {"EntryTime": "2017-06-10T05:18:00-07:00", "NoiseLevelDB": 96, "DurationSecond": 3.7},
  {"EntryTime": "2017-06-10T05:20:00-07:00", "NoiseLevelDB": 108, "DurationSecond": 0.99},
  {"EntryTime": "2017-06-10T05:20:00-07:00", "NoiseLevelDB": 113, "DurationSecond": 25.1},
  {"EntryTime": "2017-06-10T05:22:00-07:00", "NoiseLevelDB": 110, "DurationSecond": 5.3}
]
```

## <a name="get-help"></a>Hier erhalten Sie Hilfe

Weitere Hilfe finden Sie auf der [Frageseite von Microsoft Q&A (Fragen und Antworten) zu Azure Stream Analytics](/answers/topics/azure-stream-analytics.html).

## <a name="next-steps"></a>Nächste Schritte

* [Einführung in Azure Stream Analytics](stream-analytics-introduction.md)
* [Erste Schritte mit Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skalieren von Azure Stream Analytics-Aufträgen](stream-analytics-scale-jobs.md)
* [Stream Analytics Query Language Reference](/stream-analytics-query/stream-analytics-query-language-reference) (Referenz zur Stream Analytics-Abfragesprache)
* [Azure Stream Analytics management REST API reference](/rest/api/streamanalytics/) (Referenz zur Azure Stream Analytics-Verwaltungs-REST-API)