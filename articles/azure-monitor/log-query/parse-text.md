---
title: Analysieren von Textdaten in Azure Monitor-Protokollen | Microsoft-Dokumentation
description: Beschreibt verschiedene Optionen zum Analysieren von Protokolldaten in Azure Monitor-Datensätzen, wenn die Daten erfasst sowie in einer Abfrage abgerufen werden, wobei jeweils ein Vergleich der relativen Vorteile erfolgt.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/04/2018
ms.author: bwren
ms.openlocfilehash: ad4839a1b9e951a2bb206518254826a066330000
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61426730"
---
# <a name="parse-text-data-in-azure-monitor-logs"></a>Analysieren von Textdaten in Azure Monitor-Protokollen
Einige von Azure Monitor gesammelte Protokolldaten umfassen mehrere Arten von Informationen in einer einzelnen Eigenschaft. Das Analysieren dieser Daten in mehrere Eigenschaften erleichtert die Verwendung in Abfragen. Ein gängiges Beispiel ist ein [benutzerdefiniertes Protokoll](../../log-analytics/log-analytics-data-sources-custom-logs.md), das einen kompletten Protokolleintrag mit mehreren Werten in einer einzelnen Eigenschaft sammelt. Durch die Erstellung separater Eigenschaften für die verschiedenen Werte können Sie nach den einzelnen Eigenschaften suchen und sie aggregieren.

In diesem Artikel werden verschiedene Optionen zum Analysieren von Protokolldaten in Azure Monitor beschrieben, wenn die Daten erfasst sowie in einer Abfrage abgerufen werden, wobei jeweils ein Vergleich der relativen Vorteile erfolgt.


## <a name="parsing-methods"></a>Analysemethoden
Sie können Daten entweder zum Erfassungs- oder zum Abfragezeitpunkt analysieren, wenn die Daten mit einer Abfrage analysiert werden. Jede Strategie hat spezifische Vorteile, wie im Folgenden beschrieben.

### <a name="parse-data-at-collection-time"></a>Analysieren von Daten zum Sammlungszeitpunkt
Wenn Sie Daten zum Sammlungszeitpunkt analysieren, konfigurieren Sie [Benutzerdefinierte Felder](../../log-analytics/log-analytics-custom-fields.md), die neue Eigenschaften in der Tabelle erstellen. Abfragen müssen keine Analyselogik enthalten und sie verwenden diese Eigenschaften einfach wie jedes andere Feld in der Tabelle.

Vorteile dieser Methode:

- Die Abfrage der gesammelten Daten ist einfacher, da Sie keine Analysebefehle in die Abfrage einbeziehen müssen.
- Bessere Abfrageleistung, da die Abfrage nicht analysiert werden muss.
 
Nachteile dieser Methode:

- Die Definition muss im Voraus erfolgen. Bereits gesammelte Daten können nicht einbezogen werden.
- Wenn Sie die Analyselogik ändern, gilt sie nur für neue Daten.
- Es stehen weniger Analyseoptionen zur Verfügung als in Abfragen.
- Erhöht die Wartezeit für die Datensammlung.
- Fehler sind möglicherweise schwer zu beheben.


### <a name="parse-data-at-query-time"></a>Analysieren von Daten zum Abfragezeitpunkt
Wenn Sie Daten zur Abfragezeit analysieren, beziehen Sie die Logik in Ihre Abfrage ein, um Daten in mehreren Feldern zu analysieren. Die eigentliche Tabelle wird nicht geändert.

Vorteile dieser Methode:

- Gilt für beliebige Daten, einschließlich der bereits gesammelten Daten.
- Änderungen an der Logik können sofort auf alle Daten angewendet werden.
- Flexible Analyseoptionen einschließlich vordefinierter Logik für bestimmte Datenstrukturen.
 
Nachteile dieser Methode:

- Erfordert komplexere Abfragen. Dies kann mithilfe von [Funktionen zur Simulation einer Tabelle](#use-function-to-simulate-a-table) gemindert werden.
- Die Analyselogik muss in mehreren Abfragen repliziert werden. Ein Teil der Logik kann durch Funktionen freigegeben werden.
- Bei der Ausführung komplexer Logik für eine sehr große Anzahl von Datensätzen (Milliarden von Datensätzen) kann sich ein Mehraufwand ergeben.

## <a name="parse-data-as-its-collected"></a>Analysieren von Daten beim Sammeln
Weitere Informationen zum Analysieren von Daten beim Sammeln finden Sie unter [Erstellen benutzerdefinierter Felder in Azure Monitor](../platform/custom-fields.md). Dadurch werden benutzerdefinierte Eigenschaften in der Tabelle erstellt, die wie jede andere Eigenschaft von Abfragen verwendet werden können.

## <a name="parse-data-in-query-using-patterns"></a>Analysieren von Daten in Abfragen mithilfe von Mustern
Wenn die zu analysierenden Daten durch ein Muster identifiziert werden können, das sich über Datensätze hinweg wiederholt, können Sie verschiedene Operatoren in der [Abfragesprache Kusto](/azure/kusto/query/) verwenden, um den bestimmten Teil der Daten in eine oder mehrere neue Eigenschaften zu extrahieren.

### <a name="simple-text-patterns"></a>Einfache Textmuster

Verwenden Sie den [parse](/azure/kusto/query/parseoperator)-Operator in Ihrer Abfrage, um eine oder mehrere benutzerdefinierte Eigenschaften zu erstellen, die aus einem Zeichenfolgenausdruck extrahiert werden können. Sie geben das zu identifizierende Muster und die Namen der zu erstellenden Eigenschaften an. Dies ist insbesondere für Daten mit Schlüsselwertzeichenfolgen nützlich, die eine Form wie _Schlüssel=Wert_ aufweisen.

Stellen Sie sich ein benutzerdefiniertes Protokoll mit Daten im folgenden Format vor.

```
Time=2018-03-10 01:34:36 Event Code=207 Status=Success Message=Client 05a26a97-272a-4bc9-8f64-269d154b0e39 connected
Time=2018-03-10 01:33:33 Event Code=208 Status=Warning Message=Client ec53d95c-1c88-41ae-8174-92104212de5d disconnected
Time=2018-03-10 01:35:44 Event Code=209 Status=Success Message=Transaction 10d65890-b003-48f8-9cfc-9c74b51189c8 succeeded
Time=2018-03-10 01:38:22 Event Code=302 Status=Error Message=Application could not connect to database
Time=2018-03-10 01:31:34 Event Code=303 Status=Error Message=Application lost connection to database
```

Die folgende Abfrage würde diese Daten als einzelne Eigenschaften analysieren. Die Zeile mit _project_ wird hinzugefügt, um nur die berechneten Eigenschaften und nicht _RawData_ zurückzugeben, bei dem es sich um die einzelne Eigenschaft handelt, die den gesamten Eintrag aus dem benutzerdefinierten Protokoll enthält.

```Kusto
MyCustomLog_CL
| parse RawData with * "Time=" EventTime " Event Code=" Code " Status=" Status " Message=" Message
| project EventTime, Code, Status, Message
```

Nachfolgend finden Sie ein weiteres Beispiel, das den Benutzernamen eines Benutzerprinzipalnamens in der Tabelle _AzureActivity_ ausgibt.

```Kusto
AzureActivity
| parse  Caller with UPNUserPart "@" * 
| where UPNUserPart != "" //Remove non UPN callers (apps, SPNs, etc)
| distinct UPNUserPart, Caller
```


### <a name="regular-expressions"></a>Reguläre Ausdrücke
Wenn Ihre Daten durch einen regulären Ausdruck identifiziert werden können, können Sie mithilfe von [Funktionen, die reguläre Ausdrücke verwenden](/azure/kusto/query/re2), einzelne Werte extrahieren. Das folgende Beispiel verwendet [extract](/azure/kusto/query/extractfunction), um das Feld _UPN_ aus _AzureActivity_-Datensätzen herauszufiltern und dann verschiedene Benutzer zurückzugeben.

```Kusto
AzureActivity
| extend UPNUserPart = extract("([a-z.]*)@", 1, Caller) 
| distinct UPNUserPart, Caller
```

Um eine effiziente Analyse im großen Umfang zu ermöglichen, verwendet Azure Monitor die re2-Version von Regular Expressions, die mit einigen der anderen Varianten von Regular Expressions ähnlich, aber nicht identisch ist. Weitere Informationen finden Sie in der [re2-Ausdrucksyntax](https://aka.ms/kql_re2syntax).


## <a name="parse-delimited-data-in-a-query"></a>Analysieren von durch Trennzeichen getrennten Daten in einer Abfrage
Durch Trennzeichen getrennte Daten trennen Felder mit einem allgemeinen Zeichen (z. B. ein Komma) in einer CSV-Datei. Verwenden Sie die [split](/azure/kusto/query/splitfunction)-Funktion, um durch Trennzeichen getrennte Daten zu analysieren, die ein von Ihnen angegebenes Trennzeichen verwenden. Sie können dies mit dem Operator [extend](/azure/kusto/query/extendoperator) verwenden, um alle Felder in den Daten zurückzugeben oder um einzelne Felder anzugeben, die in die Ausgabe einbezogen werden sollen.

> [!NOTE]
> Da „split“ ein dynamisches Objekt zurückgibt, müssen die Ergebnisse möglicherweise explizit in Datentypen wie Zeichenfolgen umgewandelt werden, die in Operatoren und Filtern verwendet werden.

Stellen Sie sich ein benutzerdefiniertes Protokoll mit Daten im folgenden CSV-Format vor.

```
2018-03-10 01:34:36, 207,Success,Client 05a26a97-272a-4bc9-8f64-269d154b0e39 connected
2018-03-10 01:33:33, 208,Warning,Client ec53d95c-1c88-41ae-8174-92104212de5d disconnected
2018-03-10 01:35:44, 209,Success,Transaction 10d65890-b003-48f8-9cfc-9c74b51189c8 succeeded
2018-03-10 01:38:22, 302,Error,Application could not connect to database
2018-03-10 01:31:34, 303,Error,Application lost connection to database
```

Die folgende Abfrage würde diese Daten analysieren und für zwei der berechneten Eigenschaften zusammenfassen. Die erste Zeile unterteilt die _RawData_-Eigenschaft in ein Zeichenfolgenarray. Jede der folgenden Zeilen weist den einzelnen Eigenschaften einen Namen zu und fügt sie der Ausgabe mithilfe von Funktionen hinzu, um sie in den entsprechenden Datentyp zu konvertieren.

```Kusto
MyCustomCSVLog_CL
| extend CSVFields  = split(RawData, ',')
| extend EventTime  = todatetime(CSVFields[0])
| extend Code       = toint(CSVFields[1]) 
| extend Status     = tostring(CSVFields[2]) 
| extend Message    = tostring(CSVFields[3]) 
| where getyear(EventTime) == 2018
| summarize count() by Status,Code
```

## <a name="parse-predefined-structures-in-a-query"></a>Analysieren vordefinierter Strukturen in einer Abfrage
Wenn Ihre Daten in einer bekannten Struktur formatiert sind, können Sie möglicherweise eine der Funktionen in der [Abfragesprache Kusto](/azure/kusto/query/) zum Analysieren vordefinierter Strukturen verwenden:

- [JSON](/azure/kusto/query/parsejsonfunction)
- [XML](/azure/kusto/query/parse-xmlfunction)
- [IPv4](/azure/kusto/query/parse-ipv4function)
- [URL](/azure/kusto/query/parseurlfunction)
- [URL-Abfrage](/azure/kusto/query/parseurlqueryfunction)
- [Dateipfad](/azure/kusto/query/parsepathfunction)
- [Benutzer-Agent](/azure/kusto/query/parse-useragentfunction)
- [Versionszeichenfolge](/azure/kusto/query/parse-versionfunction)

Die folgende Beispielabfrage analysiert das Feld _Eigenschaften_ der Tabelle _AzureActivity_, die im JSON-Format strukturiert ist. Sie speichert die Ergebnisse in einer dynamischen Eigenschaft namens _parsedProp_, die den individuell benannten Wert im JSON-Format enthält. Diese Werte werden verwendet, um die Abfrageergebnisse zu filtern und zusammenzufassen.

```Kusto
AzureActivity
| extend parsedProp = parse_json(Properties) 
| where parsedProp.isComplianceCheck == "True" 
| summarize count() by ResourceGroup, tostring(parsedProp.tags.businessowner)
```

Diese Analysefunktionen können prozessorintensiv sein, sodass sie nur verwendet werden sollten, wenn Ihre Abfrage mehrere Eigenschaften aus den formatierten Daten verwendet. Andernfalls ist die einfache Musterabgleichsverarbeitung schneller.

Das folgende Beispiel zeigt die Aufschlüsselung des Domänencontrollers vom Typ „TGT Preauth“. Der Typ ist nur im Feld „EventData“ vorhanden, das eine XML-Zeichenfolge darstellt, aber es sind keine weiteren Daten aus diesem Feld erforderlich. In diesem Fall wird [parse](/azure/kusto/query/parseoperator) verwendet, um die erforderlichen Daten auszuwählen.

```Kusto
SecurityEvent
| where EventID == 4768
| parse EventData with * 'PreAuthType">' PreAuthType '</Data>' * 
| summarize count() by PreAuthType
```

## <a name="use-function-to-simulate-a-table"></a>Verwenden einer Funktion zum Simulieren einer Tabelle
Sie verfügen möglicherweise über mehrere Abfragen, die eine bestimmte Tabelle auf dieselbe Weise analysieren. In diesem Fall [erstellen Sie eine Funktion](functions.md), die die analysierten Daten zurückgibt, anstatt die Analyselogik in den einzelnen Abfragen zu replizieren. Sie können dann in anderen Abfragen den Aliasnamen der Funktion anstelle der ursprünglichen Tabelle verwenden.

Betrachten Sie das obige Beispiel für das benutzerdefinierte Protokoll, das Kommas als Trennzeichen verwendet. Damit die analysierten Daten in mehreren Abfragen verwendet werden können, erstellen Sie eine Funktion mit der folgenden Abfrage, und speichern Sie sie mit dem Aliasnamen _MyCustomCSVLog_.

```Kusto
MyCustomCSVLog_CL
| extend CSVFields = split(RawData, ',')
| extend DateTime  = tostring(CSVFields[0])
| extend Code      = toint(CSVFields[1]) 
| extend Status    = tostring(CSVFields[2]) 
| extend Message   = tostring(CSVFields[3]) 
```

Sie können jetzt den Aliasnamen _MyCustomCSVLog_ anstelle des eigentlichen Tabellennamens in Abfragen wie den folgenden verwenden.

```Kusto
MyCustomCSVLog
| summarize count() by Status,Code
```


## <a name="next-steps"></a>Nächste Schritte
* Erfahren Sie mehr über [Protokollabfragen](log-query-overview.md) zum Analysieren der aus Datenquellen und Lösungen gesammelten Daten.