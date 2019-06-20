---
title: Schreiben von Abfragen für den Azure-Daten-Explorer
description: In dieser Anleitung erfahren Sie, wie Sie grundlegende und komplexere Abfragen für den Azure-Daten-Explorer durchführen.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/07/2019
ms.openlocfilehash: b1a7e64cf6b85b517bc027d6541d63c9be729734
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60773977"
---
# <a name="write-queries-for-azure-data-explorer"></a>Schreiben von Abfragen für den Azure-Daten-Explorer

In diesem Artikel erfahren Sie, wie Sie mithilfe der Abfragesprache in Azure-Daten-Explorer grundlegende Abfragen mit den gängigsten Operatoren ausführen. Sie lernen auch einige der komplexeren Features der Sprache kennen.

## <a name="prerequisites"></a>Voraussetzungen

Sie können die Abfragen in diesem Artikel auf zwei Arten ausführen:

- Im *Hilfecluster* für Azure-Daten-Explorer, den wir eingerichtet haben, um den Lernprozess zu unterstützen.
    [Melden Sie sich bei dem Cluster](https://dataexplorer.azure.com/clusters/help/databases/samples) mit einem Organisations-E-Mail-Konto an, das Mitglied von Azure Active Directory ist.

- In Ihrem eigenen Cluster, der die StormEvents-Beispieldaten enthält. Weitere Informationen finden Sie unter [Schnellstart: Erstellen eines Azure Data Explorer-Clusters und einer Datenbank](create-cluster-database-portal.md) und [Erfassen von Beispieldaten in Azure Data Explorer](ingest-sample-data.md).

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

## <a name="overview-of-the-query-language"></a>Übersicht der Abfragesprache

Eine Abfrage in Azure-Daten-Explorer ist eine schreibgeschützte Anforderung, Daten zu verarbeiten und Ergebnisse zurückzugeben. Die Anforderung wird in unformatiertem Text formuliert, wobei ein Datenflussmodell verwendet wird, das darauf ausgelegt ist, die Lesbarkeit, das Verfassen und die Automatisierung der Syntax zu vereinfachen. Die Abfrage verwendet Schemaentitäten, die in einer SQL-ähnlichen Hierarchie organisiert sind: Datenbanken, Tabellen und Spalten.

Die Abfrage besteht aus einer Abfolge von Abfrageanweisungen, getrennt durch ein Semikolon (`;`), mit mindestens einer tabellarischen Ausdrucksanweisung, bei der es sich um eine Anweisung handelt, die Daten in einem tabellenähnlichen Raster aus Spalten und Zeilen erzeugt. Die tabellarischen Ausdrucksanweisungen der Abfrage erzeugen die Ergebnisse der Abfrage.

Die Syntax der tabellarischen Ausdrucksanweisung besteht aus einem tabellarischen Datenfluss von einem tabellarischen Abfrageoperator zum nächsten, beginnend mit der Datenquelle (z. B. eine Tabelle in einer Datenbank oder ein Operator, der Daten erzeugt), worauf der Fluss eine Reihe von Datentransformationsoperatoren durchläuft, die unter Verwendung des Pipe-Trennzeichens (`|`) miteinander verbunden sind.

Beispielsweise enthält die folgende Abfrage eine einzelne Anweisung, die eine tabellarische Ausdrucksanweisung ist. Die Anweisung beginnt mit einem Verweis auf eine Tabelle namens `StormEvents` (die Datenbank, die diese Tabelle hostet, ist hier impliziert und Teil der Verbindungsinformationen). Die Daten (Zeilen) für diese Tabelle werden dann zuerst nach dem Wert der Spalte `StartTime` gefiltert und anschließend nach dem Wert der Spalte `State`. Die Abfrage gibt dann die Anzahl der "verbleibenden" Zeilen zurück.

**\[** [**Zum Ausführen der Abfrage klicken**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjPSC1KVQguSSwqCcnMTVWws1VISSxJLQGyNYwMDMx1DQ11DQw1FRLzUpBU2aArMgIpQjGvJFXB1lZByc3HP8jTxVFJQQEkm5xfmlcCAHoR9euCAAAA) **\]**

```Kusto
StormEvents
| where StartTime >= datetime(2007-11-01) and StartTime < datetime(2007-12-01)
| where State == "FLORIDA"  
| count
```

In diesem Fall ist das Ergebnis:

|Count|
|-----|
|   23|
| |

Weitere Informationen finden Sie in der [Referenz zur Abfragesprache](https://aka.ms/kustolangref).

## <a name="most-common-operators"></a>Die gängigsten Operatoren

Die in diesem Abschnitt behandelten Operatoren sind die Bausteine zum Verständnis von Abfragen im Azure-Daten-Explorer. Die meisten Abfragen, die Sie schreiben, werden mehrere dieser Operatoren enthalten.

Um Abfragen im Hilfecluster auszuführen, wählen Sie **Zum Ausführen der Abfrage klicken** über jeder Abfrage aus.

So führen Sie Abfragen in Ihrem eigenen Cluster aus

1. Kopieren Sie jede Abfrage in die webbasierte Abfrageanwendung, und wählen Sie dann entweder die Abfrage aus, oder platzieren Sie den Cursor in der Abfrage.

1. Wählen Sie dann am oberen Rand der Abfrage **Ausführen** aus.

### <a name="count"></a>count

[**count**](https://docs.microsoft.com/azure/kusto/query/countoperator): Gibt die Anzahl von Zeilen in der Tabelle zurück.

Die folgende Abfrage gibt die Anzahl der Zeilen in der Tabelle „StormEvents“ zurück.

**\[** [**Zum Ausführen der Abfrage klicken**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSspVqhRSM4vzSsBALU2eHsTAAAA) **\]**

```Kusto
StormEvents | count
```

### <a name="take"></a>take

[**take**](https://docs.microsoft.com/azure/kusto/query/takeoperator): Gibt maximal die angegebene Anzahl von Zeilen zurück.

Die folgende Abfrage gibt fünf Zeilen aus der Tabelle „StormEvents“ zurück. Das Schlüsselwort *limit* ist ein Alias für *take*.

**\[** [**Zum Ausführen der Abfrage klicken**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSspVqhRKEnMTlUwBQDEz2b8FAAAAA%3d%3d) **\]**

```Kusto
StormEvents | take 5
```

> [!TIP]
> Es gibt keine Garantie dafür, welche Datensätze zurückgegeben werden, außer wenn die Daten sortiert sind.

### <a name="project"></a>project

[**project**](https://docs.microsoft.com/azure/kusto/query/projectoperator): Wählt eine Teilmenge von Spalten aus.

Die folgende Abfrage gibt eine bestimmte Gruppe von Spalten zurück.

**\[** [**Zum Ausführen der Abfrage klicken**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUShJzE5VMAWxCorys1KTSxSCSxKLSkIyc1N1FFzzUiAMoFgJiA%2fSFlJZAGS6JOYmpqcGFOUXpBaVVAKlCjKL81NS%2fRKLihJLMstSAY%2buIINnAAAA) **\]**

```Kusto
StormEvents
| take 5
| project StartTime, EndTime, State, EventType, DamageProperty, EpisodeNarrative
```

### <a name="where"></a>Hierbei gilt:

[**where**](https://docs.microsoft.com/azure/kusto/query/whereoperator): Filtert eine Tabelle auf die Teilmenge der Zeilen, die ein Prädikat erfüllen.

Die folgende Abfrage filtert die Daten nach `EventType` und `State`.

**\[** [**Zum Ausführen der Abfrage klicken**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAEWMPQvCMBCGd8H%2fcFuWro4dBOvHkgoJOB%2fm0KjJhetRKfjjNe3g9n49r1OW1I2UdVivPvC%2bkxDM3k%2bFoG3B7F%2fMwQDmAE5Rl%2fCydceTPfjemsopPgk2VRXhB121TkV9TNRAl8MiZrz53zeww4Q3OgsXEp1%2bVYkDB7IoghpH%2bgI9OH8WnwAAAA%3d%3d) **\]**

```Kusto
StormEvents
| where EventType == 'Flood' and State == 'WASHINGTON'
| take 5
| project StartTime, EndTime, State, EventType, DamageProperty, EpisodeNarrative
```

### <a name="sort"></a>sort

[**sort**](https://docs.microsoft.com/azure/kusto/query/sortoperator): Sortiert die Zeilen der Eingabetabelle in Reihenfolge nach einer oder mehreren Spalten.

Die folgende Abfrage sortiert die Daten in absteigender Reihenfolge nach `DamageProperty`.

**\[** [**Zum Ausführen der Abfrage klicken**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAF2NPQvCMBCGd8H%2fcFuXrI4dBOvHEoUGnM%2fm0KjphctRKfjjNe0guL0fvM%2fbKktsBuo1LxdveN1ICCbvxkRQ11Btn8y%2bAuw9tIo6h%2bd1uz%2fYnTvaquwyi8JlhA1GvNJJOJHoCJ5yV2rFB8GqqCR8p04LSdSFSAaa3s9iopvfu%2fnDfasUMnuyKIIaBvoAtvGMsb4AAAA%3d) **\]**

```Kusto
StormEvents
| where EventType == 'Flood' and State == 'WASHINGTON'
| sort by DamageProperty desc
| take 5
| project StartTime, EndTime, State, EventType, DamageProperty, EpisodeNarrative
```

> [!NOTE]
> Die Reihenfolge von Vorgängen ist wichtig. Versuchen Sie, `take 5` vor `sort by` zu setzen. Erhalten Sie unterschiedliche Ergebnisse?

### <a name="top"></a>top

[**top**](https://docs.microsoft.com/azure/kusto/query/topoperator): Gibt die ersten  *N*  Datensätze nach den angegebenen Spalten sortiert zurück.

Die folgende Abfrage gibt dieselben Ergebnisse wie oben mit einem Operator weniger zurück.

**\[** [**Zum Ausführen der Abfrage klicken**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAF2NOwvCMBSFd8H%2fcLcsWR07CNbHkgoJOMfmohGTG24vlYA%2fXtsOgtt5cL5jhTi1I2YZ1qs3vO7ICLN3tSA0Daj9kygo8DmAFS9LeNna48kcXGfUtBMqsIFrhZ1P%2foZnpoIsFQIO%2fdQXpgf2MgFYXEyooc1hETNU%2f071H%2bRblThQQOOZvcQRP1rSng21AAAA) **\]**

```Kusto
StormEvents
| where EventType == 'Flood' and State == 'WASHINGTON'
| top 5 by DamageProperty desc
| project StartTime, EndTime, State, EventType, DamageProperty, EpisodeNarrative
```

### <a name="extend"></a>extend

[**extend**](https://docs.microsoft.com/azure/kusto/query/extendoperator): Berechnet abgeleitete Spalten.

Die folgende Abfrage erstellt eine neue Spalte, indem ein Wert in jeder Zeile berechnet wird.

**\[** [**Zum Ausführen der Abfrage klicken**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAF2OvQ7CMAyEdyTewVuWMDJ2QGr5WQJSKzGHxoIiEkeuKVTi4WmooBKbfXeffaUQ%2b6LDIO189oLHBRnhs1d9RMgyUOsbkVNgg4NSrIzicVVud2ZT7Y1KnFCEJZx6yK23ZzwwRWTpwWFbJx%2bfggOf39lKQwEyKIKrGo%2bwSEdZ0pyCkemKtUyi%2fib1j9ZjDz311H9%2fBys2LTk0lhPT4RvwA3pn6AAAAA%3d%3d) **\]**

```Kusto
StormEvents
| where EventType == 'Flood' and State == 'WASHINGTON'
| top 5 by DamageProperty desc
| extend Duration = EndTime - StartTime
| project StartTime, EndTime, Duration, State, EventType, DamageProperty, EpisodeNarrative
```

Ausdrücke können alle üblichen Operatoren (+, -, *, /, %) enthalten. Zudem gibt es zahlreiche nützliche Funktionen, die Sie aufrufen können.

### <a name="summarize"></a>summarize

[**summarize**](https://docs.microsoft.com/azure/kusto/query/summarizeoperator): Aggregiert Gruppen von Zeilen.

Die folgende Abfrage gibt die Anzahl der Ereignisse nach `State` zurück.

**\[** [**Zum Ausführen der Abfrage klicken**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlVIBYnFJ%2beX5pUo2CqAaQ1NhaRKheCSxJJUAB%2fedDI3AAAA) **\]**

```Kusto
StormEvents
| summarize event_count = count() by State
```

Der **summarize**-Operator gruppiert Zeilen, die denselben Wert in der **by**-Klausel haben, und verwendet dann die Aggregationsfunktion (z. B. **count**), um jeder Gruppe zu einer einzelnen Zeile zu kombinieren. In diesem Fall gibt es also für jeden Bundesstaat eine Zeile und eine Spalte für die Anzahl der Zeilen in diesem Bundesstaat.

Es gibt eine Reihe von Aggregationsfunktionen, und Sie können mehrere davon in einem **summarize**-Operator verwenden, um mehrere berechnete Spalten zu erzeugen. Beispielsweise könnten Sie die Anzahl der Stürme in jedem Bundesstaat abrufen sowie die eindeutige Anzahl der Stürme pro Bundesstaat und anschließend **top** verwenden, um die am meisten von Stürmen betroffenen Bundesstaaten zu erhalten.

**\[** [**Zum Ausführen der Abfrage klicken**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlUIBkk455fmlSjYKiSDaA1NHYWQyoJU%2fzSwXDFQPAUiAdYPktJUSKoE6kwsSQUZVpJfoGAKEYGblZJanAwAgbFb73QAAAA%3d) **\]**

```Kusto
StormEvents
| summarize StormCount = count(), TypeOfStorms = dcount(EventType) by State
| top 5 by StormCount desc
```

Das Ergebnis eines **summarize**-Vorgangs enthält:

- jede in **by** genannte Spalte

- eine Spalte für jeden berechneten Ausdruck

- eine Zeile für jede Kombination von by-Werten

### <a name="render"></a>render

[**render**](https://docs.microsoft.com/azure/kusto/query/renderoperator): Gibt Ergebnisse als grafische Ausgabe wieder.

Die folgende Abfrage zeigt ein Säulendiagramm an.

**\[** [**Zum Ausführen der Abfrage klicken**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAFWMsQ7CQAxDdyT%2bIWMrdSgbSxmQ2Nj6Aei4Ru0hkqA0VwTi49uUBRZL9rPdmiidJmQbt5sPjJkoaHojoGeXKJmtWbUoK6DUQQNh6osj9onPwUq4vqC1YLjORc2Dpef2OaD%2bPcEBdvu6dvZQuWG077b6LTlV5A4VotwzcRyC2gxU6ktSqQAAAA%3d%3d) **\]**

```Kusto
StormEvents
| summarize event_count=count(), mid = avg(BeginLat) by State
| sort by mid
| where event_count > 1800
| project State, event_count
| render columnchart
```

Die folgende Abfrage zeigt ein einfaches Zeitdiagramm an.

**\[** [**Zum Ausführen der Abfrage klicken**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlVIBYnFJ%2beX5pXYgkkNTYWkSoWkzDyN4JLEopKQzNxUHQXDFE2QtqLUvJTUIoUSoFhyBlASAAyXWQJWAAAA) **\]**

```Kusto
StormEvents
| summarize event_count=count() by bin(StartTime, 1d)
| render timechart
```

Die folgende Abfrage zählt Ereignisse nach der Zeit Modulo 1 Tag, gruppiert in Stunden (bins), und zeigt ein Zeitdiagramm an.

**\[** [**Zum Ausführen der Abfrage klicken**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEADWNQQqDMBRE90LvMBtBwY0HcNkT2L2k8UuEJh9%2bfqSWHt4k4GZghpk3s7L450FB46P5g75KYYXjJJiwfZilm9WIvnZPaDGuGDC6vnRj8t7I%2fiNQ2S%2bWU9CpatfjfVZKLbLo7WGiLZnkGxJoxlqX%2bRf81ZbyiAAAAA%3d%3d) **\]**

```Kusto
StormEvents
| extend hour = floor(StartTime % 1d , 1h)
| summarize event_count=count() by hour
| sort by hour asc
| render timechart
```

Die folgende Abfrage vergleicht mehrere tägliche Reihen in einem Zeitdiagramm.

**\[** [**Zum Ausführen der Abfrage klicken**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEACWPSwvCMBCE74L%2fYSgIFXrpD%2bihaKzxkUBTXyeputKCbSCmvvDHm9TL7gwzsN8qq03DHtTa%2b3DwBb0stRdUujMJrjetTQhlS2OLuiGMEF8QIa7GvvusyJBPLaFuEQbZZjWDnGHN9nwigyhYp1wwt7c8z7jgqZM7riZSKC6cFjIv5pimS1n4SLAdFixX7OCMzFkmRdAfundNU5r6QyAPejzrrrVJP8MxTu8eN%2fqT%2bL5xL5CBdcjnyrH%2fALPTSKnkAAAA) **\]**

```Kusto
StormEvents
| extend hour= floor( StartTime % 1d , 1h)
| where State in ("GULF OF MEXICO","MAINE","VIRGINIA","WISCONSIN","NORTH DAKOTA","NEW JERSEY","OREGON")
| summarize event_count=count() by hour, State
| render timechart
```

> [!NOTE]
> Der **render**-Operator ist eher eine clientseitige Funktion als Teil der Engine. Er wurde aus Gründen der Bedienfreundlichkeit in die Sprache integriert. Die Webanwendung unterstützt die folgenden Optionen: Balkendiagramm, Säulendiagramm, Kreisdiagramm, Zeitdiagramm und Liniendiagramm. 

## <a name="scalar-operators"></a>Skalar-Operatoren

In diesem Abschnitt werden einige der wichtigsten Skalar-Operatoren behandelt.

### <a name="bin"></a>bin()

[**bin()** ](https://docs.microsoft.com/azure/kusto/query/binfunction): Rundet Werte auf eine ganze Zahl ab, die ein Vielfaches der angegebenen bin-Größe ist.

Die folgende Abfrage berechnet die Anzahl mit einer Bucketgröße von einem Tag.

**\[** [**Zum Ausführen der Abfrage klicken**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjPSC1KVQguSSwqCcnMTVWwU0hJLEktATI1jAwMzHUNjHQNTTQVEvNSkBTZYCoyMtQEGVdcmpubWJRZlaqQCrIiPjm%2fNK9EwVYBTGtoKiRVKiRl5mnAjdJRMEzRBABIhjnmkwAAAA%3d%3d) **\]**

```Kusto
StormEvents
| where StartTime > datetime(2007-02-14) and StartTime < datetime(2007-02-21)
| summarize event_count = count() by bin(StartTime, 1d)
```

### <a name="case"></a>case()

[**case()** ](https://docs.microsoft.com/azure/kusto/query/casefunction): Wertet eine Liste von Prädikaten aus und gibt den ersten Ergebnisausdruck zurück, dessen Prädikat erfüllt ist, oder den endgültigen Ausdruck **else**. Sie können mit diesem Operator Daten kategorisieren oder gruppieren:

Die folgende Abfrage gibt eine neue Spalte `deaths_bucket` zurück und gruppiert die Todesfälle nach Anzahl.

**\[** [**Zum Ausführen der Abfrage klicken**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAGWOwQrCQAxE74X%2bQ9hTCwX14FFBaK9e%2bgGS7gZdbFrYZEXFj7dbqgfNbfJmhml1DNzcaFDJsxdIZMbgnwSOUC8Cu%2fQq6lnUPpDVEroHtIpKKUB3pcEt7lMX7ZV0ClkUgiLPYLqlaQ%2fbdQWmx3AmU%2f2gTUJMzkf%2bYwkJY99%2fiDmuDqac545Bv3MAxb4Bic1Oy88AAAA%3d) **\]**

```Kusto
StormEvents
| summarize deaths = sum(DeathsDirect) by State
| extend deaths_bucket = case (
    deaths > 50, "large",
    deaths > 10, "medium",
    deaths > 0, "small",
    "N/A")
| sort by State asc
```

### <a name="extract"></a>extract()

[**extract()** ](https://docs.microsoft.com/azure/kusto/query/extractfunction): Ruft eine Übereinstimmung für einen regulären Ausdruck aus einer Textzeichenfolge ab.

Die folgende Abfrage extrahiert bestimmte Attributwerte aus einer Ablaufverfolgung.

**\[** [**Zum Ausführen der Abfrage klicken**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAE2OwQrCMBBE74X%2bw9BTojHYagSVHJRevXkrHqJdpVBbSVew4McbFYungeXtvKmJsetzxw4WZQh2x5og9t6daIWOfdVcJIpkY1OFrc0U8rt3XLWNTbOZnhultU4UfoD5A4zRmVkovInDOo6%2bojh6gh5MTTmQwR0uQckiGb5FMZ0s9WEsQ3uo%2fixSccT9jdqz8ORqKTECV1cSaSdfq2k6L8oAAAA%3d) **\]**

```Kusto
let MyData = datatable (Trace: string) ["A=1, B=2, Duration=123.45,...", "A=1, B=5, Duration=55.256, ..."];
MyData
| extend Duration = extract("Duration=([0-9.]+)", 1, Trace, typeof(real)) * time(1s)
```

Diese Abfrage verwendet eine **let**-Anweisung, die einen Namen (in diesem Fall `MyData`) an einen Ausdruck bindet. Im restlichen Gültigkeitsbereichs, in dem die **let**-Anweisung vorkommt (globaler Gültigkeitsbereich oder im Gültigkeitsbereich eines Funktionskörpers), kann der Name verwendet werden, um auf seine gebundenen Wert zu verweisen.

### <a name="parsejson"></a>parse_json()

[**parse_json()** ](https://docs.microsoft.com/azure/kusto/query/parsejsonfunction): Interpretiert eine Zeichenfolge als JSON-Wert und gibt den Wert als dynamischen Datentyp zurück. Diese ist der Nutzung der **extractjson()** -Funktion vorzuziehen, wenn Sie mehrere Elemente eines zusammengesetzten JSON-Objekts extrahieren müssen.

Die folgende Abfrage extrahiert die JSON-Elemente aus einem Array.

**\[** [**Zum Ausführen der Abfrage klicken**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAHWPQQuCQBCF74L%2fYdmLBSJ6EGKjU17r1E0kJh1C2XZlHc0w%2f3ur1s1O896bB%2fONRGKnVwIE7MAKOwhuEtnmYiBHwRoypbpvXSf1Bl60BqjUiot04B3IFrmIol0Q%2bpPLdauIi3iyj9KWojCcNfRWx7NuqEiw48KaMRu9bO86y3HXeTPsCVXBzvg8amlpajANXqtGq4VmO5VqoyvM6dsKfkhpmAUzkf9nM9OtLi3reg79ar788AEVX8GkOAEAAA%3d%3d) **\]**

```Kusto
let MyData = datatable (Trace: string)
['{"duration":[{"value":118.0,"valcount":5.0,"min":100.0,"max":150.0,"stdDev":0.0}]}'];
MyData
| extend NewCol = parse_json(Trace)
| project NewCol.duration[0].value, NewCol.duration[0].valcount, NewCol.duration[0].min, NewCol.duration[0].max, NewCol.duration[0].stdDev
```

Die folgende Abfrage extrahiert die JSON-Elemente.

**\[** [**Zum Ausführen der Abfrage klicken**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAE2OwQqCQBCG74LvsOzFBBE9CLHRKa916hYRkw6RbLuyO5pRvXvrGtZpvn9m4P8kEts%2bSiBga1a7QXCWyBZ7AxUKZslc1SVmh%2bjJe5AdcpHnyzRLxlTpThEXxRhvV%2bVOWeYZBseFZ0t1iT0XLryj4yoMprIweDEcCFXNdnjfaOnaWzAWT43VamqPx6fW6AYr%2bn6l3iH5S95hXjiLH8Mw82TxAQvJEB%2fsAAAA) **\]**

```Kusto
let MyData = datatable (Trace: string) ['{"value":118.0,"valcount":5.0,"min":100.0,"max":150.0,"stdDev":0.0}'];
MyData
| extend NewCol = parse_json(Trace)
| project NewCol.value, NewCol.valcount, NewCol.min, NewCol.max, NewCol.stdDev
```

Die folgende Abfrage extrahiert die JSON-Elemente mit einem dynamischen Datentyp.

**\[** [**Zum Ausführen der Abfrage klicken**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAD2NMQvCMBBG90D%2bw5GphVLSoSARt65ubuJwJjdU0lZiWlrU%2f25MotO9x8H7LHk4bh16hAOYcDxeLUFxcqhJgdlGHHpdcnbOWDzFgnYmoZpmV8tK6GkePTmh2q8N%2fRg%2bUkbGNXAb%2beFNR4tQQd7lZc9ZGuXsBXc33Uh7iJN1jFdZcvunIf5HXCvOEqf2BwXmDCnKAAAA) **\]**

```Kusto
let MyData = datatable (Trace: dynamic)
[dynamic({"value":118.0,"counter":5.0,"min":100.0,"max":150.0,"stdDev":0.0})];
MyData
| project Trace.value, Trace.counter, Trace.min, Trace.max, Trace.stdDev
```

### <a name="ago"></a>ago()

[**ago()** ](https://docs.microsoft.com/azure/kusto/query/agofunction): Subtrahiert den angegebenen Zeitraum von der aktuellen UTC-Uhrzeit.

Die folgende Abfrage gibt Daten für die letzten 12 Stunden zurück.

**\[** [**Zum Ausführen der Abfrage klicken**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA1WOQQ6CQAxF9yTc4S8hQcmQuNSNR4ALTKQyJDAlnSIuPLwzJGrctM3v+7+t684R7qMEhW6MafQUMJAnsUoIdl4mQm/VVrC+h0Z6shFOINZAIc/qOql24KIEL8nIAuWYohC6sfQB9yjtPtPA8SrhmGeLjF7RjTO1Gu+cIdYPVHjeisOpLyukKTbjYml5piuvXknwIU1lGlPm2Qvzg55L+u+b9udIyOZI6LfHZf/YNK58Ay2HrbAEAQAA) **\]**

```Kusto
//The first two lines generate sample data, and the last line uses
//the ago() operator to get records for last 12 hours.
print TimeStamp= range(now(-5d), now(), 1h), SomeCounter = range(1,121)
| mv-expand TimeStamp, SomeCounter
| where TimeStamp > ago(12h)
```

### <a name="startofweek"></a>startofweek()

[**startofweek()** ](https://docs.microsoft.com/azure/kusto/query/startofweekfunction): Gibt den Beginn der Woche zurück, einschließlich Datum, verschoben um einen Offset, falls angegeben.

Die folgende Abfrage gibt den Beginn der Woche mit verschiedenen Offsets zurück.

**\[** [**Zum Ausführen der Abfrage klicken**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEACtKzEtPVchPSytOLVFIK8rPVdA1VCjJVzBUKC5JLVAw5OWqUSgoys9KTS5RKE9NzQ4uSSwqUbAFygLp%2fDSQkEZefrmGpg7UEE0dCA0AdE3lv1kAAAA%3d) **\]**

```Kusto
range offset from -1 to 1 step 1
| project weekStart = startofweek(now(), offset),offset
```

Diese Abfrage verwendet den **range**-Operator, der eine einspaltige Tabelle mit Werten erzeugt. Siehe auch: [**startofday()** ](https://docs.microsoft.com/azure/kusto/query/startofdayfunction), [**startofweek()** ](https://docs.microsoft.com/azure/kusto/query/startofweekfunction), [**startofyear()** ](https://docs.microsoft.com/azure/kusto/query/startofyearfunction)), [**startofmonth()** ](https://docs.microsoft.com/azure/kusto/query/startofmonthfunction), [**endofday()** ](https://docs.microsoft.com/azure/kusto/query/endofdayfunction), [**endofweek()** ](https://docs.microsoft.com/azure/kusto/query/endofweekfunction), [**endofmonth()** ](https://docs.microsoft.com/azure/kusto/query/endofmonthfunction), and [**endofyear()** ](https://docs.microsoft.com/azure/kusto/query/endofyearfunction).

### <a name="between"></a>between()

[**between()** ](https://docs.microsoft.com/azure/kusto/query/betweenoperator): Gleicht die Eingabe ab, die innerhalb des inklusiven Bereichs liegt.

Die folgende Abfrage filtert die Daten nach einem angegebenen Datumsbereich.

**\[** [**Zum Ausführen der Abfrage klicken**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjPSC1KVQguSSwqCcnMTVVISi0pT03NU9BISSxJLQGKaBgZGJjrApGRuaaCnp4ChrixgaYmyKTk%2fNK8EgBluyagXgAAAA%3d%3d) **\]**

```Kusto
StormEvents
| where StartTime between (datetime(2007-07-27) .. datetime(2007-07-30))
| count
```

Die folgende Abfrage filtert die Daten nach einem angegebenen Datumsbereich, mit der kleinen Abweichung von drei Tagen (`3d`) gegenüber dem Startdatum.

**\[** [**Zum Ausführen der Abfrage klicken**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjPSC1KVQguSSwqCcnMTVVISi0pT03NU9BISSxJLQGKaBgZGJjrApGRuaaCnp6CcYomSF9yfmleCQCGAqjRTAAAAA%3d%3d) **\]**

```Kusto
StormEvents
| where StartTime between (datetime(2007-07-27) .. 3d)
| count
```

## <a name="tabular-operators"></a>Tabellarische Operatoren

Kusto verfügt über viele tabellarische Operatoren, von denen einige in anderen Abschnitten dieses Artikels behandelt werden. Hier konzentrieren wir uns auf **parse**. 

### <a name="parse"></a>parse

[**parse**](https://docs.microsoft.com/azure/kusto/query/parseoperator): Wertet einen Zeichenfolgenausdruck aus und analysiert dessen Wert in eine oder mehrere berechnete Spalten. Es gibt drei Arten der Analyse: „simple“ (der Standard), „regex“ und „relaxed“.

Die folgende Abfrage analysiert eine Ablaufverfolgung und extrahiert die relevanten Werte unter Verwendung der Standardanalysemethode „simple“. Der Ausdruck (als „StringConstant“ bezeichnet) ist ein regulärer Zeichenfolgenwert, und die Übereinstimmung ist „strict“: Erweiterte Spalten müssen den erforderlichen Typen entsprechen.

**\[** [**Zum Ausführen der Abfrage klicken**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAN2UTU%2fDMAyG75X6H6xcxlCkpRlsUNQjN6gQ2wnEoevMFsiaKk2HJvHjabqvlI91l11QLrH12vETW5Zo4H411kmKEME0MdWZSISz2yVmpvaHhdEim3V979n3OrU%2fhFgZ8boaSZHiI0pMiipEY6FKnWKcLDB6EDlKkeEoneO0lKgpGGUSWYcUER9SKOw1LhcT1BHvU5AqfR%2bLKpbxXjDscRYMgF2FFyxkwRMFvX7ngCLXuBSqLO5%2bT9S%2ftrJuh54OI7g8iMFaMdhxGOy0GJz9i25w%2fjdG0IoRHNWNNe1ph2pwEKNlqI7HsEPley83vrfZCL73CXmiq%2fr32wA%2bhJnDOZAGEQHXBNIEIq4VSpXNbAIXkbjAO8UOmuz4bWoXlrhWWO0vqyA2%2bAcw2f7B1rORd60calat3jA1TRbq1A6NxsC%2bLdCoCuj3p74AKTs4pmcFAAA%3d) **\]**

```Kusto
let MyTrace = datatable (EventTrace:string)
[
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=23, lockTime=02/17/2016 08:40:01Z, releaseTime=02/17/2016 08:40:01Z, previousLockTime=02/17/2016 08:39:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=15, lockTime=02/17/2016 08:40:00Z, releaseTime=02/17/2016 08:40:00Z, previousLockTime=02/17/2016 08:39:00Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=20, lockTime=02/17/2016 08:40:01Z, releaseTime=02/17/2016 08:40:01Z, previousLockTime=02/17/2016 08:39:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=22, lockTime=02/17/2016 08:41:01Z, releaseTime=02/17/2016 08:41:00Z, previousLockTime=02/17/2016 08:40:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=16, lockTime=02/17/2016 08:41:00Z, releaseTime=02/17/2016 08:41:00Z, previousLockTime=02/17/2016 08:40:00Z)'
];
MyTrace
| parse EventTrace with * "resourceName=" resourceName ", totalSlices=" totalSlices:long * "sliceNumber=" sliceNumber:long * "lockTime=" lockTime ", releaseTime=" releaseTime:date "," * "previousLockTime=" previouLockTime:date ")" *  
| project resourceName ,totalSlices , sliceNumber , lockTime , releaseTime , previouLockTime
```

Die folgende Abfrage analysiert eine Ablaufverfolgung und extrahiert die relevanten Werte unter Verwendung von `kind = regex`. Die „StringConstant“ kann ein regulärer Ausdruck sein.

**\[** [**Zum Ausführen der Abfrage klicken**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAN2UQU%2fCQBCF7036HyZ7gWKRbVHQmgY9eNPGCCcoh9KOsLK0ZLtFMf54l6LQBgUuXEyTTbP7pt3vvclwlPC47IkgRHAhCqR6Rhyher%2fAWOb7TioFi8eGrg10rZLvO%2bAlkr0su5yF%2bIwcg1SVCEyTTIToBTN0n9gcOYuxG04wyjgKE2QiA56XpK7dNiFdvXrZbITCtZsm8CSc9piqpXbDajdsarWAXjkX1KFW3wSx%2fs8exVzggiVZ%2bvD7h5rXK5lRMU%2bHYV3uxaAHMehxGPS0GDb9F2nY9t8Y1kEM66g01rSnbarWXowDTXU8xqqpdG14o2vfE0HXPmEeCHX%2fKYsjNR8EjvEdtqMB3picAKme1zrGIKh%2f3NX7w5pLoEgLt6SM56c1PzpTq6oqYpIitMOTeAxAlKb6c3Wjs3GBbAzJJUV8UjQjP91BJztuOGryKbHvGwQgxxbJK4ayTFKKBbahQCkA2DX7C29veJJmBQAA) **\]**

```Kusto
let MyTrace = datatable (EventTrace:string)
[
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=23, lockTime=02/17/2016 08:40:01Z, releaseTime=02/17/2016 08:40:01Z, previousLockTime=02/17/2016 08:39:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=15, lockTime=02/17/2016 08:40:00Z, releaseTime=02/17/2016 08:40:00Z, previousLockTime=02/17/2016 08:39:00Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=20, lockTime=02/17/2016 08:40:01Z, releaseTime=02/17/2016 08:40:01Z, previousLockTime=02/17/2016 08:39:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=22, lockTime=02/17/2016 08:41:01Z, releaseTime=02/17/2016 08:41:00Z, previousLockTime=02/17/2016 08:40:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=16, lockTime=02/17/2016 08:41:00Z, releaseTime=02/17/2016 08:41:00Z, previousLockTime=02/17/2016 08:40:00Z)'
];
MyTrace
| parse kind = regex EventTrace with "(.*?)[a-zA-Z]*=" resourceName @", totalSlices=\s*\d+\s*.*?sliceNumber=" sliceNumber:long  ".*?(previous)?lockTime=" lockTime ".*?releaseTime=" releaseTime ".*?previousLockTime=" previousLockTime:date "\\)"  
| project resourceName , sliceNumber , lockTime , releaseTime , previousLockTime
```

Die folgende Abfrage analysiert eine Ablaufverfolgung und extrahiert die relevanten Werte unter Verwendung von `kind = relaxed`. Die „StringConstant“ ist ein regulärer Zeichenfolgenwert, und die Übereinstimmung ist „relaxed“: Erweiterte Spalten können den erforderlichen Typen teilweise entsprechen.

**\[** [**Zum Ausführen der Abfrage klicken**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAN2US0%2fCQBDH7036HSZ7wZpN2BYFrenRGzZG4KLxUNoRVpYu2W5REj%2b83fKw9QE1kYvppTOZx%2f%2b3MxmBGm5WQxXFCAEkkS6%2bsUA4uV5iqku%2fn2nF04ljWw%2b21Sr9PoRS86fVQPAY71BglBUpCjOZqxjDaI7BLV%2bg4CkO4ikmuUBFQUsdiTIlC7wehcz8hvl8jCrwOhSEjGdDXuQyr%2b322h5zu8Au%2fDPmM%2feeglr32ROxULjkMs%2f63xfqXJowp0WPh%2bGe78VgBzFYMwx2XAyP%2fYtpeN7PGO5BDLfRNNa0x12q7l6MA0vVHMMslW09XtnW5iLY1hssIlXon%2fE0CYom0SsmQP6IMxz1%2b7%2b7AnXQdX6TNXMIvHA9hVMgNYEEqiaQuj5StXwh04kpUNVLqup3ETsCsoMxpavSSdXyi7NrIohJ%2foJDtoRbzybcMeFQjkjJZ4x1nYVWtEPtleHjjaGmCujnVu%2fWU75tHgYAAA%3d%3d) **\]**

```Kusto
let MyTrace = datatable (EventTrace:string)
[
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=23, lockTime=02/17/2016 08:40:01Z, releaseTime=02/17/2016 08:40:01Z, previousLockTime=02/17/2016 08:39:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=15, lockTime=02/17/2016 08:40:00Z, releaseTime=02/17/2016 08:40:00Z, previousLockTime=02/17/2016 08:39:00Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=20, lockTime=02/17/2016 08:40:01Z, releaseTime=02/17/2016 08:40:01Z, previousLockTime=02/17/2016 08:39:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=22, lockTime=02/17/2016 08:41:01Z, releaseTime=02/17/2016 08:41:00Z, previousLockTime=02/17/2016 08:40:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=16, lockTime=02/17/2016 08:41:00Z, releaseTime=02/17/2016 08:41:00Z, previousLockTime=02/17/2016 08:40:00Z)'
];
MyTrace
| parse kind=relaxed "Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=NULL, sliceNumber=23, lockTime=02/17/2016 08:40:01, releaseTime=NULL, previousLockTime=02/17/2016 08:39:01)" with * "resourceName=" resourceName ", totalSlices=" totalSlices:long * "sliceNumber=" sliceNumber:long * "lockTime=" lockTime ", releaseTime=" releaseTime:date "," * "previousLockTime=" previousLockTime:date ")" *  
| project resourceName ,totalSlices , sliceNumber , lockTime , releaseTime , previousLockTime
```

## <a name="time-series-analysis"></a>Zeitreihenanalyse

### <a name="make-series"></a>make-series

[**make-series**](https://docs.microsoft.com/azure/kusto/query/make-seriesoperator): Aggregiert Gruppen von Zeilen wie [summarize](https://docs.microsoft.com/azure/kusto/query/summarizeoperator), generiert aber einen (Zeit-)Reihenvektor für jede Kombination von Werten.

Die folgende Abfrage gibt eine Gruppe von Zeitreihen für die Anzahl der Sturmereignisse pro Tag zurück. Die Abfrage deckt einen Zeitraum von drei Monaten für jeden Bundesstaat ab, wobei fehlende Klassen (bins) mit der Konstanten 0 aufgefüllt werden:

**\[** [**Zum Ausführen der Abfrage klicken**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUchNzE7VLU4tykwtVsizTc4vzSvR0FRISU1LLM0psTVQyM9TCC5JLCoJycxNVcjMUyhKzEtP1UhJLEktAYpoGBkYmOsaGAKRpo4CmqixrjFI1DBFUyGpEmRKSSoAazsM0n0AAAA%3d) **\]**

```Kusto
StormEvents
| make-series n=count() default=0 on StartTime in range(datetime(2007-01-01), datetime(2007-03-31), 1d) by State
```

Nachdem Sie eine Gruppe von (Zeit-)Reihen erstellt haben, können Sie Reihenfunktionen anwenden, um anomale Formen, saisonale Muster und vieles mehr zu erkennen.

Die folgende Abfrage extrahiert die obersten drei Bundesstaaten, bei denen die meisten Ereignisse an einem bestimmten Tag auftraten:

**\[** [**Zum Ausführen der Abfrage klicken**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAF2OsQoCMRBEe8F%2f2DIBAzmvsLrSLzj7EC%2brBs3mSPbkBD%2feLDYibPVmZmdGziUdn0hct5s3JH9HU7FErEDDlBdipSHgxS8PHixkgpF94VNMCJGgeLqiCp6RG1F7aw%2fGdu30Dv5ob3qhXdBwfskXRmnElZECfDtdbbgq0qJwnqEX76%2fmyCW%2ftkV1Ek9pWSwgNdOt7foAJIuybs8AAAA%3d) **\]**

```Kusto
StormEvents
| make-series n=count() default=0 on StartTime in range(datetime(2007-01-01), datetime(2007-03-31), 1d) by State
| extend series_stats(n)
| top 3 by series_stats_n_max desc
| render timechart
```

Weitere Informationen finden Sie in der vollständigen Liste der [Reihenfunktionen](https://docs.microsoft.com/azure/kusto/query/scalarfunctions#series-processing-functions).

## <a name="advanced-aggregations"></a>Erweiterte Aggregationen

Wir haben einfache Aggregationen wie **count** und **summarize** weiter oben in diesem Artikel behandelt. Dieser Abschnitt führt komplexere Optionen ein.

### <a name="top-nested"></a>top-nested

[**top-nested**](https://docs.microsoft.com/azure/kusto/query/topnestedoperator): Erzeugt oberste, hierarchische Ergebnisse, wobei jede Ebene einen Drilldown, basierend auf Werten der vorherigen Ebene, darstellt.

Dieser Operator ist nützlich für Dashboardvisualisierungsszenarien oder wenn es erforderlich ist, eine Frage wie die folgende zu beantworten: „Finden Sie die Top-N-Werte von K1 (mithilfe einer Aggregation); für jeden dieser Werte finden Sie die Top-M-Werte von K2 (mithilfe einer anderen Aggregation); ...“

Die folgende Abfrage gibt eine hierarchische Tabelle mit `State` auf der obersten Ebene, gefolgt von `Sources`, zurück.

**\[** [**Zum Ausführen der Abfrage klicken**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjJL9DNSy0uSU1RMFLIT1MILkksSVVIqlQoLs3VcEpNz8zzSSzR1OHlQlJoDFaYX1qUTEilIUila16KT35yYklmfh6GcgDrXwk5jgAAAA%3d%3d) **\]**

```Kusto
StormEvents
| top-nested 2 of State by sum(BeginLat),
top-nested 3 of Source by sum(BeginLat),
top-nested 1 of EndLocation by sum(BeginLat)
```

### <a name="pivot-plugin"></a>pivot()-Plug-In

[**pivot()-Plug-In**](https://docs.microsoft.com/azure/kusto/query/pivotplugin): Dreht eine Tabelle durch Umwandeln der eindeutigen Werte aus einer Spalte in der Eingabetabelle in mehrere Spalten in der Ausgabetabelle. Der Operator führt bei allen verbleibenden Spaltenwerten in der endgültigen Ausgabe Aggregationen durch, wenn sie erforderlich sind.

Die folgende Abfrage wendet einen Filter an und pivotiert die Zeilen in Spalten.

**\[** [**Zum Ausführen der Abfrage klicken**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSgoys9KTS5RCC5JLEnVUQBLhFQWpILkyjNSi1IhMgrFJYlFJcXlmSUZCkqOPkoIabgOhYzEYgWl8My8FLBsalliTilIZ0FmWX6JBtgUTQDlv21NfQAAAA%3d%3d) **\]**

```Kusto
StormEvents
| project State, EventType
| where State startswith "AL"
| where EventType has "Wind"
| evaluate pivot(State)
```

### <a name="dcount"></a>dcount()

[**dcount()** ](https://docs.microsoft.com/azure/kusto/query/dcount-aggfunction): Gibt eine Schätzung der Anzahl unterschiedlicher Werte eines Ausdrucks in der Gruppe zurück. Sie können [ **count()** ](https://docs.microsoft.com/azure/kusto/query/countoperator) verwenden, um alle Werte zu zählen.

Die folgende Abfrage zählt unterschiedliche `Source` nach `State`.

**\[** [**Zum Ausführen der Abfrage klicken**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlUIzi8tSk4tVrBVSEnOL80r0YAIaCokVSoElySWpAIAFKgSBDoAAAA%3d) **\]**

```Kusto
StormEvents
| summarize Sources = dcount(Source) by State
```

### <a name="dcountif"></a>dcountif()

[**dcountif()** ](https://docs.microsoft.com/azure/kusto/query/dcountif-aggfunction): Gibt eine Schätzung der Anzahl unterschiedlicher Werte des Ausdrucks für Zeilen zurück, bei denen das Prädikat zu „wahr“ ausgewertet wird.

Die folgende Abfrage zählt die unterschiedliche Werte von `Source`, mit `DamageProperty < 5000`.

**\[** [**Zum Ausführen der Abfrage klicken**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSspVqhRKEnMTlUwNDDg5apRKC7NzU0syqxKVQjOLy1KTi1WsFVISc4vzSvJTNOACOkouCTmJqanBhTlF6QWlVQq2CiYGhgYaCokVSoElySWpAIAuk%2fTX14AAAA%3d) **\]**

```Kusto
StormEvents 
| take 100
| summarize Sources = dcountif(Source, DamageProperty < 5000) by State
```

### <a name="dcounthll"></a>dcount_hll()

[**dcount_hll()** ](https://docs.microsoft.com/azure/kusto/query/dcount-hllfunction): Berechnet  **dcount**  aus HyperLogLog-Ergebnissen (generiert mit  [**hll**](https://docs.microsoft.com/azure/kusto/query/hll-aggfunction)  oder  [**hll_merge**](https://docs.microsoft.com/azure/kusto/query/hll-merge-aggfunction)).

Die folgende Abfrage verwendet den HLL-Algorithmus, um die Anzahl zu generieren.

**\[** [**Zum Ausführen der Abfrage klicken**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlXIyMkJSi1WsAUxNFwScxPTUwOK8gtSi0oqNRWSKhWSMvM0gksSi0pCMnNTdQwNcjUx9PumFqWnpkCMiM8FcTQgpoKVFhTlZ6UmlyikJOeX5pXEg6yB69EEAKm9wyCXAAAA) **\]**

```Kusto
StormEvents
| summarize hllRes = hll(DamageProperty) by bin(StartTime,10m)
| summarize hllMerged = hll_merge(hllRes)
| project dcount_hll(hllMerged)
```

### <a name="argmax"></a>arg_max()

[**arg_max()** ](https://docs.microsoft.com/azure/kusto/query/arg-max-aggfunction): Findet eine Zeile in der Gruppe, die einen Ausdruck maximiert, und gibt den Wert eines anderen Ausdrucks zurück (oder „*“, um die gesamte Zeile zurückzugeben).

Die folgende Abfrage gibt die Zeit des letzten Flutberichts in jedem Bundesstaat zurück.

**\[** [**Zum Ausführen der Abfrage klicken**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjPSC1KVQDzQyoLUhVsbRWU3HLy81OUQLLFpbm5iUWZVakKiUXp8bmJFRrBJYlFJSGZuak6ClqaCkmVCkCBklSQ2oKi%2fKzU5BKIgI4CkkLXvBQoA2YNAHO1S0OFAAAA) **\]**

```Kusto
StormEvents
| where EventType == "Flood"
| summarize arg_max(StartTime, *) by State
| project State, StartTime, EndTime, EventType
```

### <a name="makeset"></a>makeset()

[**makeset()** ](https://docs.microsoft.com/azure/kusto/query/makeset-aggfunction): Gibt ein dynamisches(JSON) Array der Gruppe unterschiedlicher Werte zurück, die ein Ausdruck in der Gruppe annimmt.

Die folgende Abfrage gibt alle Zeiten zurück, zu denen eine Flut von jedem Bundesstaat gemeldet wurde, und erstellt ein Array aus der Gruppe der unterschiedlichen Werte.

**\[** [**Zum Ausführen der Abfrage klicken**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAFWLQQ6CQBAE7yb8ocNJE76wR3mA8IEFOxF1mM3siIHweAVPHqsq1bianCeOnovDiveNRuzczokIAWX9VL2WW80vkWjDQuzuwqTmGQESH8z0Y%2bPRvB2EJ3QzvuTcvmR6Z%2b8%2fUf3NH6ZkMFeAAAAA) **\]**

```Kusto
StormEvents
| where EventType == "Flood"
| summarize FloodReports = makeset(StartTime) by State
| project State, FloodReports
```

### <a name="mv-expand"></a>mv-expand

[**mv-expand**](https://docs.microsoft.com/azure/kusto/query/mvexpandoperator): Erweitert mehrwertige Sammlungen aus einer Spalte des Typs „dynamisch“, sodass jeder Wert in der Sammlung eine gesonderte Zeile erhält. Alle anderen Spalten in einer erweiterten Zeile werden dupliziert. Ist das Gegenteil von makelist.

Die folgende Abfrage generiert die Beispieldaten, indem ein Satz erstellt wird, der dann zur Veranschaulichung der **mv-expand**-Funktionen verwendet wird.

**\[** [**Zum Ausführen der Abfrage klicken**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAFWOQQ6CQAxF9yTcoWGliTcws1MPIFygyk9EKTPpVBTj4Z2BjSz%2f738v7WF06r1vD2xcp%2bCoNq9yHDFYLIsvvW5Q0JybKYCco2omqnyNTxHW7oPFckbwajFZhB%2bIsE1trNZ0gi1dpuRmQ%2baC%2bjuuthS7Fbwvi%2f%2bP8lpGvAMP7Wr3A6BceSu7AAAA) **\]**

```Kusto
let FloodDataSet = StormEvents
| where EventType == "Flood"
| summarize FloodReports = makeset(StartTime) by State
| project State, FloodReports;
FloodDataSet
| mv-expand FloodReports
```

### <a name="percentiles"></a>percentiles()

[**percentiles()** ](https://docs.microsoft.com/azure/kusto/query/percentiles-aggfunction): Gibt eine Schätzung für den angegebenen  [**nächsten Perzentilrang**](https://docs.microsoft.com/azure/kusto/query/percentiles-aggfunction)  der durch einen Ausdruck definierten Grundgesamtheit. Die Genauigkeit hängt von der Bevölkerungsdichte in der Region des Perzentils ab. Kann nur im Kontext der Aggregation in  [**summarize**](https://docs.microsoft.com/azure/kusto/query/summarizeoperator) verwendet werden.

Die folgende Abfrage berechnet Perzentile für die Sturmdauer.

**\[** [**Zum Ausführen der Abfrage klicken**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUUitKEnNS1FIKS1KLMnMz1OwVXDNSwnJzE1V0FUILkksKgGxQQrLM1KLUhHq7BQMirEI2ygYZ4CEi0tzcxOLMqtSFQpSi5KBlmXmpBZrwJTpKJjqKBgZACkgtgBiS1NNAEC7XiaYAAAA) **\]**

```Kusto
StormEvents
| extend duration = EndTime - StartTime
| where duration > 0s
| where duration < 3h
| summarize percentiles(duration, 5, 20, 50, 80, 95)
```

Die folgende Abfrage berechnet Perzentile für die Sturmdauer nach Bundesstaat und normalisiert die Daten auf 5-Minuten-Klassen (`5m`).

**\[** [**Zum Ausführen der Abfrage klicken**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAG1NSwrCMBTcC95hli1EKEpBQd31BHUvafOgAZNI8uIPD28SEBVcDDMM8%2bnZedNdyHKYz56gG5NVUNFL1s5ih86qgzaEBXqWnrPOwetEnj65PZrwx95iNWU7RGOk1w8C5avj6KLlNF64qjHcMWhbvXsCralFPmT6rZ%2fJj2lAnyh8pwWWTaKEdcKmLYul%2fgLODFs%2b4AAAAA%3d%3d) **\]**

```Kusto
StormEvents
| extend duration = EndTime - StartTime
| where duration > 0s
| where duration < 3h
| summarize event_count = count() by bin(duration, 5m), State
| summarize percentiles(duration, 5, 20, 50, 80, 95) by State
```

### <a name="cross-dataset"></a>Datasetübergreifend

In diesem Abschnitt werden Elemente behandelt, mit denen Sie komplexere Abfragen erstellen, Daten zwischen Tabellen verknüpfen und Abfragen über Datenbanken und Cluster hinweg ausführen können.

### <a name="let"></a>let

[**let**](https://docs.microsoft.com/azure/kusto/query/letstatement): Verbessert die Modularität und Wiederverwendbarkeit. Die **let**-Anweisung gestattet es Ihnen, einen potenziell komplexen Ausdruck in mehrere Teile aufzuteilen, von denen jeder an einen Namen gebunden ist, und diese Teilen dann zu kombinieren. Eine **let**-Anweisung kann auch verwendet werden, um benutzerdefinierte Funktionen und Sichten (Ausdrücke über Tabellen, deren Ergebnisse wie eine neue Tabelle aussehen) zu erstellen. Durch eine **let**-Anweisung gebundene Ausdrücke können vom Typ „Skalar“, „Tabelle“ oder benutzerdefinierte Funktionen (Lambdas) sein.

Im folgenden Beispiel wird eine Variable vom Typ „Tabelle“ erstellt, die diese dann in einem nachfolgenden Ausdruck verwendet.

**\[** [**Zum Ausführen der Abfrage klicken**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAMtJLVHwyUzPKMnLzEsPLskvyi1WsOXlArNcy1LzSop5uWoUyjNSi1IVwPyQyoJUBVtbBSW4LiVrXq4coDGOZYk5iXnJGakkGQPXBTIGzSUgPVn5mXkKGmhmayrk5ykElySWpIKUpGQWl2TmJZdARACul3kY0gAAAA%3d%3d) **\]**

```Kusto
let LightningStorms =
StormEvents
| where EventType == "Lightning";
let AvalancheStorms =
StormEvents
| where EventType == "Avalanche";
LightningStorms
| join (AvalancheStorms) on State
| distinct State
```

### <a name="join"></a>join

[**join**](https://docs.microsoft.com/azure/kusto/query/joinoperator): Führt die Zeilen zweier Tabellen zusammen, um eine neue Tabelle zu erzeugen, indem Werte aus den angegebenen Spalten aus beiden Tabellen zugeordnet werden. Kusto unterstützt eine umfangreiche Zahl von Join-Typen (Verknüpfungen): **fullouter**, **inner**, **innerunique**, **leftanti**, **leftantisemi**, **leftouter**, **leftsemi**, **rightanti**, **rightantisemi**, **rightouter**, **rightsemi**.

Im folgenden Beispiel werden zwei Tabellen mit einem inneren Join verknüpft.

**\[** [**Zum Ausführen der Abfrage klicken**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA8tJLVGIULBVSEksAcKknFQN79RKq+KSosy8dB2FsMSc0lRDq5z8vHRNXq5oXi4FIFBPVNcx1IGyk9R1jJDYxjB2srqOCS9XrDUvVw7Qhkj8Nhih2wA0ydAAySgjZI4xnJMCtMQAYkuEQo1CVn5mnkJ2Zl6KbWZeXmoR0Nb8PAWgZQAFPLdO5AAAAA==) **\]**

```Kusto
let X = datatable(Key:string, Value1:long)
[
    'a',1,
    'b',2,
    'b',3,
    'c',4
];
let Y = datatable(Key:string, Value2:long)
[
    'b',10,
    'c',20,
    'c',30,
    'd',40
];
X 
| join kind=inner Y on Key
```

> [!TIP]
> Verwenden Sie die Operatoren **where** und **project**, um die Anzahl von Zeilen und Spalten in den Eingabetabellen vor dem Join zu verringern. Wenn eine Tabelle immer kleiner als die andere ist, verwenden Sie diese als die linke (weitergeleitete) Seite der Verknüpfung. Die Spalten für die Verknüpfungsübereinstimmung müssen den gleichen Namen haben. Verwenden Sie ggf. den Operator **project**, um eine Spalte in einer der Tabellen umzubenennen.

### <a name="serialize"></a>serialize

[**serialize**](https://docs.microsoft.com/azure/kusto/query/serializeoperator): Serialisiert das Rowset, sodass Sie Funktionen verwenden können, die serialisierte Daten benötigen, z. B. **row_number()** .

Die folgende Abfrage ist erfolgreich, weil die Daten serialisiert werden.

**\[** [**Zum Ausführen der Abfrage klicken**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlVIzi%2fNK9HQVEiqVAguSSxJBcumFmUm5gBlQZzUipLUvBSFovzy%2bLzS3KTUIgVbJI6GJgB4pV4NWgAAAA%3d%3d) **\]**

```Kusto
StormEvents
| summarize count() by State
| serialize
| extend row_number = row_number()
```

Das Rowset gilt auch als serialisiert, wenn es ein Ergebnis der folgenden Operatoren ist: **sort**, **top** oder **range**, optional gefolgt von den Operatoren **project**, **project-away**, **extend**, **where**, **parse**, **mv-expand** oder **take**.

**\[** [**Zum Ausführen der Abfrage klicken**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlVIzi%2fNK9HQVEiqVAguSSxJBcvmF5XABRQSi5NBgqkVJal5KQpF%2beXxeaW5SalFCrZIHA1NAEGimf5iAAAA) **\]**

```Kusto
StormEvents
| summarize count() by State
| sort by State asc
| extend row_number = row_number()
```

### <a name="cross-database-and-cross-cluster-queries"></a>Datenbankübergreifende und clusterübergreifende Abfragen

[Datenbankübergreifende und clusterübergreifende Abfragen](https://docs.microsoft.com/azure/kusto/query/cross-cluster-or-database-queries): Sie können eine Datenbank in demselben Cluster abfragen, indem Sie auf sie als `database("MyDatabase").MyTable` verweisen. Sie können eine Datenbank in einem Remotecluster abfragen, indem Sie auf sie als `cluster("MyCluster").database("MyDatabase").MyTable` verweisen.

Die folgende Abfrage wird aus einem Cluster aufgerufen und fragt Daten von dem Cluster `MyCluster` ab. Um diese Abfrage auszuführen, verwenden Sie den Namen Ihres eigenen Clusters und den Ihrer Datenbank.

```Kusto
cluster("MyCluster").database("Wiki").PageViews
| where Views < 2000
| take 1000;
```

### <a name="user-analytics"></a>Benutzeranalyse

Dieser Abschnitt enthält die Elemente und Abfragen, die veranschaulichen, wie einfach es ist, Benutzerverhalten in Kusto zu analysieren.

### <a name="activitycountsmetrics-plugin"></a>activity_counts_metrics-Plug-In

[**activity_counts_metrics-Plug-In**](https://docs.microsoft.com/azure/kusto/query/activity-counts-metrics-plugin): Berechnet nützliche Aktivitätsmetriken (Gesamtzahl von Werten, Anzahl unterschiedlicher Werte, Anzahl unterschiedlicher neuer Werte und aggregierte Anzahl unterschiedlicher Werte). Metriken werden für jedes Zeitfenster berechnet, werden dann verglichen und zu sowie mit allen vorherigen Zeitfenstern aggregiert.

Die folgende Abfrage analysiert die Benutzerakzeptanz, indem die tägliche Anzahl von Aktivitäten berechnet wird.

**\[** [**Zum Ausführen der Abfrage klicken**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAJXSPQvCMBAG4L3Q%2f5CtFlLoFyiVDn4M6mqdREpsggTaKs1VEfzxXm0LDiEimcJz3CW8VwogClgDKWcgQFZiEvrB1PNnnh%2b4c9sqsUDUXMPxyA9Z8%2bsjDfhwz0hKsBzPuRSTgxLNlicKGllfKMmwBw6sbsnY0bWto205C4cS3Rso2tpgO4MtDbbSWvixzGD6eb1ttBYZev42%2fbzI8L%2fe9n9b3NkJQ8xs60XEnZUt1hBWgLxLeObFta1B5ZXAKAs1BPuVKO03iXb7gp36tXDfExVB%2f2ICAAA%3d) **\]**

```Kusto
let start=datetime(2017-08-01);
let end=datetime(2017-08-04);
let window=1d;
let T = datatable(UserId:string, Timestamp:datetime)
[
'A', datetime(2017-08-01),
'D', datetime(2017-08-01),
'J', datetime(2017-08-01),
'B', datetime(2017-08-01),
'C', datetime(2017-08-02),
'T', datetime(2017-08-02),
'J', datetime(2017-08-02),
'H', datetime(2017-08-03),
'T', datetime(2017-08-03),
'T', datetime(2017-08-03),
'J', datetime(2017-08-03),
'B', datetime(2017-08-03),
'S', datetime(2017-08-03),
'S', datetime(2017-08-04),
];
T
| evaluate activity_counts_metrics(UserId, Timestamp, start, end,
window)
```

### <a name="activityengagement-plugin"></a>activity_engagement-Plug-In

[**activity_engagement-Plug-In**](https://docs.microsoft.com/azure/kusto/query/activity-engagement-plugin): Berechnet das Aktivitäts-Beteiligungs-Verhältnis auf Grundlage der ID-Spalte über ein gleitendes Zeitachsenfenster. **activity_engagement-Plug-In** kann verwendet werden, um die Werte DAU, WAU und MAU (tägliche, wöchentliche und monatliche aktive Benutzer) zu berechnen.

Die folgende Abfrage gibt das Verhältnis der Gesamtzahl der unterschiedlichen Benutzer, die eine Anwendung täglich verwenden, zu der Gesamtzahl der unterschiedlichen Benutzer, die die Anwendung wöchentlich verwenden, zurück, bezogen auf ein gleitendes Sieben-Tage-Fenster.

**\[** [**Zum Ausführen der Abfrage klicken**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAG1RQWrDMBC8G%2fyHvUVOHGy1lByKD6GBviDkUIoR1tpVsS0jr0MCeXxXiigpVAiBVjOzM6uigHcc0SlCcGrUdgCtSIFtYZnRgWrInA0ZnNOkR4J6JuUIKo9CMgOKp1LutqXknb1GDI76P8RzQHCXDqHW6gqt43ZRkeydNxNOIHWa3AAv5Ctei2xvx06IQNtGTlZInT0AHQN9BpFt5EO59kHmKvQVUUivX8q1y3L4c9%2fIks%2bt5LoMwsMZLxMrgtHVXcb7pOuEthWemEFvBkPARL%2fSpCjgTfXN0vuBHvbH4rQ%2fsikyNjg6q37xL3GsV47cqQ4HHEl8rIxefeZhNHmMmIehsB2dp8nunnZy9hsbiriDWuqTWqpfxdBsLb2ZGzhm8y%2f6b2i%2bWO8HLFcMGe8BAAA%3d) **\]**

```Kusto
// Generate random data of user activities
let _start = datetime(2017-01-01);
let _end = datetime(2017-01-31);
range _day from _start to _end step 1d
| extend d = tolong((_day - _start)/1d)
| extend r = rand()+1
| extend _users=range(tolong(d*50*r), tolong(d*50*r+100*r-1), 1)
| mv-expand id=_users to typeof(long) limit 1000000
// Calculate DAU/WAU ratio
| evaluate activity_engagement(['id'], _day, _start, _end, 1d, 7d)
| project _day, Dau_Wau=activity_ratio*100
| render timechart
```

> [!TIP]
> Bei der Berechnung von DAU/MAU ändern Sie die Enddaten und den Zeitraum für das gleitende Fenster (OuterActivityWindow).

### <a name="activitymetrics-plugin"></a>activity_metrics-Plug-In

[**activity_metrics-Plug-In**](https://docs.microsoft.com/azure/kusto/query/activity-metrics-plugin): Berechnet nützliche Aktivitätsmetriken (Anzahl unterschiedlicher Werte, Anzahl unterschiedlicher neuer Werte, Retentionsrate und Änderungsrate) basierend auf dem aktuellen Zeitfenster im Vergleich zu dem vorherigen Zeitfenster.

Die folgende Abfrage berechnet die Änderungs- und Retentionsrate für ein angegebenes Dataset.

**\[** [**Zum Ausführen der Abfrage klicken**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAG2SwW7CMAyG70i8g2%2bk0KoNE%2bIwscsOe4hpqqLGQFjaVKkLVNrDLw7RxjRyqBTr%2fz%2f3t1OW8IYdekUIXnXataAVKXB7GAf0oBoyZ0MGh%2fnMIkE9kPIEO1YhmRbFupLbopJFtc6ekwY7%2fV%2bxKZ4kK0KXA0Kt1QR7H9olIrmbbyDsQer57AvwSlxhFjnruoMQ0VYkT1ZKnd0JfRByBpGt5F255iDDLvYVCaSXm2rpsxz%2b3FfrKnwLGeoygtszXvtABKN3Nwz%2fJ009ur1gYwbWtIZAVvGw53JEn%2fK9PJwSi3rvTthQlOWBPp%2bVJbwq24yWN3FB%2fLQTeAwByLgOeD8x0lnZkRVpL1PdInnTDOJ9YfTiI0%2fE24DyONIctvpB0x94zfBlSJBDcxz97509PgDCM%2bAMzTEgvwEO44wSMAIAAA%3d%3d) **\]**

```Kusto
// Generate random data of user activities
let _start = datetime(2017-01-02);
let _end = datetime(2017-05-31);
range _day from _start to _end step 1d
| extend d = tolong((_day - _start)/1d)
| extend r = rand()+1
| extend _users=range(tolong(d*50*r), tolong(d*50*r+200*r-1), 1)
| mv-expand id=_users to typeof(long) limit 1000000
| where _day > datetime(2017-01-02)
| project _day, id
// Calculate weekly retention rate
| evaluate activity_metrics(['id'], _day, _start, _end, 7d)
| project _day, retention_rate*100, churn_rate*100
| render timechart
```

### <a name="newactivitymetrics-plugin"></a>new_activity_metrics-Plug-In

[**new_activity_metrics-Plug-In**](https://docs.microsoft.com/azure/kusto/query/new-activity-metrics-plugin): Berechnet nützliche Aktivitätsmetriken (Anzahl unterschiedlicher Werte, Anzahl unterschiedlicher neuer Werte, Retentionsrate und Änderungsrate) für die Kohorte der neuen Benutzer. Das Konzept dieses Plug-Ins ähnelt dem  [**activity_metrics-Plug-In**](https://docs.microsoft.com/azure/kusto/query/activity-metrics-plugin), konzentriert sich aber auf neue Benutzer.

Die folgende Abfrage berechnet eine Vermerk- und Änderungsrate mit einem Woche-zu-Woche-Fenster für die Kohorte neuer Benutzer (Benutzer, die in der ersten Woche angekommen sind).

**\[** [**Zum Ausführen der Abfrage klicken**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAG1Ry27DIBC8W%2fI%2f7C04wbJJFeVQ5VapP9BbVVnIrGMaGyy8eVjqxxcwh1QqBx7LzCwzVBW8o0EnCcFJo%2bwISpIE28F1RgeyJX3TpHHOswEJmpmkIzgFFJIeke1rcSzrQ1mL4jVh0Kj%2fEC8R4bucEd7kAp3z3ZIg2ZU2E04gVJ79AD4oVIIU2cGaM2OBVSZKUQlVPOGcxwUHrNiJp3ITbMyn2JUlHbU91FtXcPhz3u1rP5fC10UUHm%2f4mLwiaHVaZcIzaZnQdiwQCxj0qAlEHUeeVRV8yAuCNcMC1CN02s0Ed8QLtLa33igbpK9M0skRCd3q4CaHa%2fgBg%2fcmJb40%2ft7pdmafG602XzxExpN3HsPicFQ8z1IcQWhy9htbisk2EU92XZ1vZkhb04Sv5tD2V7fufwFYtolnAgIAAA%3d%3d) **\]**

```Kusto
// Generate random data of user activities
let _start = datetime(2017-05-01);
let _end = datetime(2017-05-31);
range Day from _start to _end step 1d
| extend d = tolong((Day - _start)/1d)
| extend r = rand()+1
| extend _users=range(tolong(d*50*r), tolong(d*50*r+200*r-1), 1)
| mv-expand id=_users to typeof(long) limit 1000000
// Take only the first week cohort (last parameter)
| evaluate new_activity_metrics(['id'], Day, _start, _end, 7d, _start)
| project from_Day, to_Day, retention_rate, churn_rate
```

### <a name="sessioncount-plugin"></a>session_count-Plug-In

[**session_count-Plug-In**](https://docs.microsoft.com/azure/kusto/query/session-count-plugin): Berechnet die Anzahl der Sitzungen auf Grundlage der ID-Spalte über eine Zeitachse.

Die folgende Abfrage gibt die Anzahl der Sitzungen zurück. Eine Sitzung gilt als aktiv, wenn eine Benutzer-ID mindestens einmal in einem Zeitraum von 100 Zeitfenstern vorkommt, während das Sitzungsrückschaufenster 41 Zeitfenster groß ist.

**\[** [**Zum Ausführen der Abfrage klicken**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAFWPQYvCQAyF74X%2bh3dZUCjYgfUgMkcP3r2XoZPqaM3INK4u7I%2ffzOwiNQRC8pKPl5EEnXfiYJEcHwmHcKUxMGFI8QoDidhoYBK6wdTVD%2bgpxB5dd6FvPSuzcwyMS2BvAzMlLP5gez%2fDrNt%2fCN4Z1iwRua5Kk2GPE6WZkY%2bMsRZt1m4pnqmXl9qouK2r1Qo75cUB5RlPQ%2bAgoWDzpPj%2bcuPdCWGiaVKp6%2bOdZbH3zYxmNFuNUhp8mmU%2bTWpWv8or%2fckl%2bQXutT48NwEAAA%3d%3d) **\]**

```Kusto
let _data = range Timeline from 1 to 9999 step 1
| extend __key = 1
| join kind=inner (range Id from 1 to 50 step 1 | extend __key=1) on __key
| where Timeline % Id == 0
| project Timeline, Id;
// End of data definition
_data
| evaluate session_count(Id, Timeline, 1, 10000, 100, 41)
| render linechart
```

### <a name="funnelsequence-plugin"></a>funnel_sequence-Plug-In

[**funnel_sequence-Plug-In**](https://docs.microsoft.com/azure/kusto/query/funnel-sequence-plugin): Berechnet die Anzahl unterschiedlicher Benutzer, die eine Abfolge von Zuständen eingenommen haben, zeigt die Verteilung der vorherigen und nächsten Zustände an, die zu der Abfolge geführt haben bzw. von dieser Abfolge gefolgt wurden.

Die folgende Abfrage zeigt an, welches Ereignis vor und nach allen Tornado-Ereignissen im Jahr 2007 eintritt.

**\[** [**Zum Ausführen der Abfrage klicken**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAGWOPYvCQBCG%2b0D%2bw3QmEEmieIqNVQrBRgxYiMhcdqKLyWzcnQiCP95V70DuYIrh5Xk%2f0hRWxpw1H8EwbMTYtrgSiwMnKNqJrtw8DNIU1vkcticUOGHXETv4ptpYgtJYRmWAnrbFGx39QbEWsv%2fIj7YwuHsZmx6FoO6ZqTk4uvTEFUVFp51RtFSJH4hWSt1SAsqj4r9olGXTYZb7i5Mw%2bJRnvzLkKhl%2fTXzAq668dc%2bAG2Orq2g3%2bBk22MfxA23MLGQQAQAA) **\]**

```Kusto
// Looking on StormEvents statistics:
// Q1: What happens before Tornado event?
// Q2: What happens after Tornado event?
StormEvents
| evaluate funnel_sequence(EpisodeId, StartTime, datetime(2007-01-01), datetime(2008-01-01), 1d,365d, EventType, dynamic(['Tornado']))
```

### <a name="funnelsequencecompletion-plugin"></a>funnel_sequence_completion-Plug-In

[**funnel_sequence_completion-Plug-In**](https://docs.microsoft.com/azure/kusto/query/funnel-sequence-completion-plugin): Berechnet den Trichter der abgeschlossenen Abfolgeschritte innerhalb verschiedener Zeiträume.

Die folgende Abfrage überprüft den Abschlusstrichter der Abfolge:  `Hail -> Tornado -> Thunderstorm -> Wind` in „allen“ Zeiten von einer Stunde, vier Stunden und einem Tag (`[1h, 4h, 1d]`).

**\[** [**Zum Ausführen der Abfrage klicken**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA12QTYvCMBCG74L/YW6tkIV2XT9g8SjsnlvwICKhM9JAOqlJqrj4402CW0RIIB/PPLwzmjwcnZfWwwZQevKqo/yzKFYfRRnW7Hs60ZEhxjdi/UZcFaO5VuqPAjhfLvD/w9F5IG7iM95YdqrJ99mPVDoTkNXGskSTju3ASNZ5Y7t43wVhdhj9PVll0L1aylbAV9glJqyKldsLsXfTyR3oIvUQAsNpYCY95jg2puuDUhnOt71yBukXBVRxCnVoTjwnIlLX4rUzAUlf3/pEPYViDDd7AOyqowFQAQAA) **\]**

```Kusto
let _start = datetime(2007-01-01);
let _end = datetime(2008-01-01);
let _windowSize = 365d;
let _sequence = dynamic(['Hail', 'Tornado', 'Thunderstorm', 'Wind']);
let _periods = dynamic([1h, 4h, 1d]);
StormEvents
| evaluate funnel_sequence_completion(EpisodeId, StartTime, _start, _end, _windowSize, EventType, _sequence, _periods)
```

## <a name="functions"></a>Functions

Dieser Abschnitt behandelt [ **Funktionen**](https://docs.microsoft.com/azure/kusto/query/functions): wiederverwendbare Abfragen, die auf dem Server gespeichert sind. Funktionen können von Abfragen und andere Funktionen aufgerufen werden (rekursive Funktionen werden nicht unterstützt).

> [!NOTE]
> Sie können keine Funktionen im Hilfecluster erstellen, der schreibgeschützt ist. Verwenden Sie Ihren eigenen Testcluster für diesen Teil.

Im folgenden Beispiel wird eine Funktion erstellt, die den Namen eines Bundesstaats (`MyState`) als Argument nimmt.

```Kusto
.create function with (folder="Demo")
MyFunction (MyState: string)
{
StormEvents
| where State =~ MyState
}
```

Im folgenden Beispiel wird eine Funktion aufgerufen, die Daten für den Bundesstaat Texas abruft.

```Kusto
MyFunction ("Texas")
| summarize count()
```

Im folgenden Beispiel wird die Funktion gelöscht, die im ersten Schritt erstellt wurde.

```Kusto
.drop function MyFunction
```

## <a name="next-steps"></a>Nächste Schritte

[Referenz zur Abfragesprache von Kusto](https://aka.ms/kustolangref)
