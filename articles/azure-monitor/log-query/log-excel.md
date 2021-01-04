---
title: Integrieren von Log Analytics und Excel
description: Integrieren Sie eine Log Analytics-Abfrage in Excel, und aktualisieren Sie Ergebnisse in Excel.
ms.subservice: logs
ms.topic: conceptual
author: roygalMS
ms.author: roygal
ms.date: 11/03/2020
ms.openlocfilehash: d903d1bb16ba3576d0092979f1cc6b82fac1c0be
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2020
ms.locfileid: "94507391"
---
# <a name="integrate-log-analytics-and-excel"></a>Integrieren von Log Analytics und Excel

Sie können Azure Monitor Log Analytics und Microsoft Excel mithilfe der M-Abfrage und der Log Analytics-API integrieren.  Mit dieser Integration können Sie 500.000 Datensätze an Excel senden.

> [!NOTE]
> Da es sich bei Excel um eine lokale Clientanwendung handelt, wirken sich lokale Hardware- und Softwareeinschränkungen auf die Leistung und die Fähigkeit aus, große Datenmengen zu verarbeiten.

## <a name="create-your-m-query-in-log-analytics"></a>Erstellen Ihrer M-Abfrage in Log Analytics 

1. **Erstellen und führen Sie Ihre Abfrage** wie gewohnt in Log Analytics aus. Machen Sie sich keine Sorgen, wenn Sie die Einschränkung von 10.000 Datensätzen in der Benutzeroberfläche erreichen  Es wird empfohlen, relative Datumsangaben wie die „ago“-Funktion oder die Zeitauswahl auf der Benutzeroberfläche zu verwenden, damit Excel die richtigen Datensätze aktualisiert.
  
2. **Abfrage exportieren:** Sobald Sie mit der Abfrage und den Ergebnissen zufrieden sind, exportieren Sie die Abfrage über die Log Analytics-Menüauswahl **In Power BI exportieren (M-Abfrage)** im Menü *Exportieren* nach M:

:::image type="content" source="media/log-excel/export-query.png" alt-text="Log Analytics-Abfrage mit der Daten- und Exportoption" border="true":::



Durch Auswahl dieser Option wird eine TXT-Datei herunterladen, die den M-Code enthält, den Sie in Excel verwenden können.

Die oben gezeigte Abfrage exportiert den folgenden M-Code. Nachfolgend finden Sie ein Beispiel des M-Codes, der für die Abfrage in unserem Beispiel exportiert wird:

```m
/*
The exported Power Query Formula Language (M Language ) can be used with Power Query in Excel
and Power BI Desktop.
For Power BI Desktop follow the instructions below: 
1) Download Power BI Desktop from https://powerbi.microsoft.com/desktop/
2) In Power BI Desktop select: 'Get Data' -> 'Blank Query'->'Advanced Query Editor'
3) Paste the M Language script into the Advanced Query Editor and select 'Done'
*/

let AnalyticsQuery =
let Source = Json.Document(Web.Contents("https://api.loganalytics.io/v1/workspaces/ddcfc599-cae0-48ee-9026-fffffffffffff/query", 
[Query=[#"query"="

Heartbeat 
| summarize dcount(ComputerIP) by bin(TimeGenerated, 1h)    
| render timechart
",#"x-ms-app"="OmsAnalyticsPBI",#"timespan"="P1D",#"prefer"="ai.response-thinning=true"],Timeout=#duration(0,0,4,0)])),
TypeMap = #table(
{ "AnalyticsTypes", "Type" }, 
{ 
{ "string",   Text.Type },
{ "int",      Int32.Type },
{ "long",     Int64.Type },
{ "real",     Double.Type },
{ "timespan", Duration.Type },
{ "datetime", DateTimeZone.Type },
{ "bool",     Logical.Type },
{ "guid",     Text.Type },
{ "dynamic",  Text.Type }
}),
DataTable = Source[tables]{0},
Columns = Table.FromRecords(DataTable[columns]),
ColumnsWithType = Table.Join(Columns, {"type"}, TypeMap , {"AnalyticsTypes"}),
Rows = Table.FromRows(DataTable[rows], Columns[name]), 
Table = Table.TransformColumnTypes(Rows, Table.ToList(ColumnsWithType, (c) => { c{0}, c{3}}))
in
Table
in AnalyticsQuery
```

## <a name="connect-query-to-excel"></a>Verknüpfen der Abfrage mit Excel 

So importieren Sie die Abfrage: 

1. Öffnen Sie Microsoft Excel. 
1. Wechseln Sie im Menüband zum Menü **Daten**. Wählen Sie **Daten abrufen** aus. Wählen Sie **Aus anderen Quellen** und dann **Leere Abfrage** aus.
 
   :::image type="content" source="media/log-excel/excel-import-blank-query.png" alt-text="Excel-Option zum Import aus leerer Abfrage" border="true":::

1. Wählen Sie im Power Query-Fenster **Erweiterter Editor** aus:

   :::image type="content" source="media/log-excel/advanced-editor.png" alt-text="Erweiterter Abfrage-Editor in Excel" border="true":::

 
1. Ersetzen Sie den Text im erweiterten Editor durch die aus Log Analytics exportierte Abfrage:

   :::image type="content" source="media/log-excel/advanced-editor-2.png" alt-text="Erstellen einer leeren Abfrage" border="true":::
 
1. Wählen Sie **Fertig** und anschließend **Load and close** (Laden und schließen) aus. Excel führt die Abfrage mithilfe der Log Analytics-API aus, und anschließend wird das Resultset angezeigt.
 

   :::image type="content" source="media/log-excel/excel-query-result.png" alt-text="Abfrageergebnisse in Excel" border="true":::

##  <a name="refreshing--data"></a>Aktualisieren von Daten

Sie können Ihre Daten direkt über Excel aktualisieren. Klicken Sie in der Menügruppe **Daten** im Excel-Menüband auf die Schaltfläche **Aktualisieren**.
 
## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu den Excel-Integrationen mit externen Datenquellen finden Sie unter [Importieren von Daten aus externen Datenquellen (Power Query)](https://support.office.com/article/import-data-from-external-data-sources-power-query-be4330b3-5356-486c-a168-b68e9e616f5a).