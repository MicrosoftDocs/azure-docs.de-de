---
title: Erstellen effizienter Protokollabfragen in Azure Monitor | Microsoft-Dokumentation
description: Verweise auf Ressourcen zum Erlernen des Schreibens von Abfragen in Log Analytics
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/17/2019
ms.author: bwren
ms.openlocfilehash: 25d6b582ed4d4e24df3841f4191471296e25abd8
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60519367"
---
# <a name="writing-efficient-log-queries-in-azure-monitor"></a>Erstellen effizienter Protokollabfragen in Azure Monitor
Dieser Artikel enthält Empfehlungen zum Erstellen effizienter Protokollabfragen in Azure Monitor. Mit diesen Strategien stellen Sie sicher, dass Ihre Abfragen schnell und mit minimalem Mehraufwand ausgeführt werden.

## <a name="scope-your-query"></a>Eingrenzen Ihrer Abfrage
Wenn Sie mit Ihrer Abfrage mehr Daten als benötigt generieren, dauert die Abfrage möglicherweise sehr lange, und Sie erhalten zu viele Daten in Ihren Ergebnissen, um diese effektiv zu analysieren. In Extremfällen kann bei der Abfrage eine Zeitüberschreitung und schließlich ein Fehler auftreten.

### <a name="specify-your-data-source"></a>Festlegen Ihrer Datenquelle
Der erste Schritt beim Erstellen einer effizienten Abfrage besteht darin, den Gültigkeitsbereich auf die erforderlichen Datenquellen einzuschränken. Das Angeben einer Tabelle ist dem Ausführen einer umfangreichen Textsuche (z.B. `search *`) immer vorzuziehen. Um eine bestimmte Tabelle abzufragen, starten Sie Ihre Abfrage wie nachfolgend gezeigt mit dem Tabellennamen:

``` Kusto
requests | ...
```

Mit dem [Suchoperator](/azure/kusto/query/searchoperator) können Sie nach einem Wert in mehreren Spalten in bestimmten Tabellen suchen. Verwenden Sie dazu etwa folgende Abfrage:

``` Kusto
search in (exceptions) "The server was not found"

search in (exceptions, customEvents) "timeout"
```

Verwenden Sie den Operator [union](/azure/kusto/query/unionoperator), um Tabellen wie die folgende abzufragen:

``` Kusto
union requests, traces | ...
```

### <a name="specify-a-time-range"></a>Festlegen eines Zeitbereichs
Beschränken Sie Ihre Abfrage auf den Zeitbereich der benötigten Daten. Standardmäßig enthält Ihre Abfrage Daten, die in den letzten 24 Stunden erfasst wurden. Sie können die entsprechende Option in der [Zeitbereichsauswahl](get-started-portal.md#select-a-time-range) ändern oder Ihrer Abfrage explizit hinzufügen. Fügen Sie den Zeitfilter am besten direkt nach dem Tabellennamen hinzu, damit bei der restlichen Abfrage nur Daten innerhalb dieses Bereichs verarbeitet werden:

``` Kusto
requests | where timestamp > ago(1h)

requests | where timestamp between (ago(1h) .. ago(30m))
```
   
### <a name="get-only-the-latest-records"></a>Abrufen der neuesten Datensätze

Wenn Sie nur die neuesten Datensätze zurückgeben möchten, verwenden Sie den Operator *top* wie in der folgenden Abfrage. Darin werden die neuesten 10 Datensätze in der Tabelle *Ablaufverfolgungen* zurückgegeben:

``` Kusto
traces | top 10 by timestamp
```

   
### <a name="filter-records"></a>Filtern von Datensätzen
Um nur Protokolle zu überprüfen, die einer angegebenen Bedingung entsprechen, verwenden Sie den Operator *where* wie in der folgenden Abfrage. Darin werden nur Datensätze zurückgegeben, deren Wert für _severityLevel_ größer als 0 ist:

``` Kusto
traces | where severityLevel > 0
```



## <a name="string-comparisons"></a>Zeichenfolgenvergleiche
Beim [Auswerten von Zeichenfolgen](/azure/kusto/query/datatypes-string-operators) sollten Sie `has` anstelle von `contains` verwenden, wenn Sie vollständige Token suchen. `has` ist effizienter, da hierbei nicht nach untergeordneten Zeichenfolgen gesucht wird.

## <a name="returned-columns"></a>Zurückgegebene Spalten

Verwenden Sie [project](/azure/kusto/query/projectoperator), damit nur die benötigten Spalten verarbeitet werden:

``` Kusto
traces 
| project timestamp, severityLevel, client_City 
| ...
```

Verwenden Sie [extend](/azure/kusto/query/extendoperator), um die Werte zu berechnen und Ihre eigenen Spalten zu erstellen. Das Filter nach einer Tabellenspalte ist in der Regel effizienter.

Beispiel: Die erste Abfrage, die nach _operation\_Name_ filtert, ist effizienter als die zweite, bei der eine neue _subscription_-Spalte erstellt und danach gefiltert wird:

``` Kusto
customEvents 
| where split(operation_Name, "/")[2] == "acb"

customEvents 
| extend subscription = split(operation_Name, "/")[2] 
| where subscription == "acb"
```

## <a name="using-joins"></a>Verwenden von Joins
Wenn Sie den Operator [join](/azure/kusto/query/joinoperator) verwenden, wählen Sie die Tabelle mit weniger Zeilen auf der linken Seite der Abfrage.


## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zu bewährten Methoden für Abfragen finden Sie unter [Query best practices](/azure/kusto/query/best-practices) (Bewährte Methoden für Abfragen).