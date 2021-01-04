---
title: Wiederholbare Kopiervorgänge in Azure Data Factory
description: Erfahren Sie, wie Duplikate vermieden werden, obwohl ein Slice, der Daten kopiert, mehrfach ausgeführt wird.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: cd0f389615c95ef9b9bc8280b6486740ddba4fb4
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96456828"
---
# <a name="repeatable-copy-in-azure-data-factory"></a>Wiederholbare Kopiervorgänge in Azure Data Factory

## <a name="repeatable-read-from-relational-sources"></a>Wiederholbare Lesevorgänge aus relationalen Quellen
Beim Kopieren von Daten aus relationalen Datenspeichern müssen Sie die Wiederholbarkeit berücksichtigen, um unbeabsichtigte Ergebnisse zu vermeiden. Sie können einen Slice in Azure Data Factory manuell erneut ausführen. Sie können auch eine Wiederholungsrichtlinie für ein Dataset konfigurieren, sodass ein Slice erneut ausgeführt wird, wenn ein Fehler auftritt. Wenn ein Slice erneut ausgeführt wird, müssen Sie sicherstellen, dass dieselben Daten gelesen werden – egal wie oft ein Slice ausgeführt wird.  
 
> [!NOTE]
> Die folgenden Beispiele beziehen sich auf Azure SQL, sie gelten aber auch für beliebige Datenspeicher, die rechteckige Datasets unterstützen. Sie müssen möglicherweise den **Typ** der Quelle und die **query**-Eigenschaft (z.B. „query“ anstelle von „sqlReaderQuery“) für den Datenspeicher anpassen.   

Generell möchten Sie beim Lesen aus relationalen Speichern nur die Daten lesen, die dem Slice entsprechen. Sie können dies beispielsweise erreichen, indem Sie die Systemvariablen "WindowStart" und "WindowEnd" verwenden, die in Azure Data Factory verfügbar sind. Im Artikel [Azure Data Factory – Funktionen und Systemvariablen](data-factory-functions-variables.md) erfahren Sie mehr über die Variablen und Funktionen in Azure Data Factory. Beispiel: 

```json
"source": {
    "type": "SqlSource",
    "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm\\'', WindowStart, WindowEnd)"
},
```
Diese Abfrage liest Daten aus der Tabelle „MyTable“, die in den Dauerbereich (WindowStart > WindowEnd) des Slices fallen. Bei einer erneuten Ausführung dieses Slices wäre auch immer sichergestellt, dass die gleichen Daten gelesen werden. 

In anderen Fällen kann es sinnvoll sein, die gesamte Tabelle zu lesen; dann könnten Sie "sqlReaderQuery" wie folgt definieren:

```json
"source": 
{            
    "type": "SqlSource",
    "sqlReaderQuery": "select * from MyTable"
},
```

## <a name="repeatable-write-to-sqlsink"></a>Wiederholbare Schreibvorgänge nach SqlSink
Beim Kopieren von Daten aus anderen Datenspeichern in **Azure SQL/SQL Server** muss die Wiederholbarkeit berücksichtigt werden, um unbeabsichtigte Ergebnisse zu vermeiden. 

Beim Kopieren von Daten in Azure SQL/SQL Server-Datenbank fügt die Kopieraktivität standardmäßig Daten an die Senkentabelle an. Angenommen, Sie kopieren Daten aus einer CSV-Datei (Datei mit durch Trennzeichen getrennten Werten), die zwei Datensätze enthält, in die folgende Tabelle in einer Azure SQL/SQL Server-Datenbank. Wenn ein Slice ausgeführt wird, werden die beiden Datensätze in die SQL-Tabelle kopiert. 

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
```

Angenommen, Sie haben Fehler in der Quelldatei gefunden und die Menge von „Down Tube“ von 2 in 4 geändert. Wenn Sie den Datenslice für diesen Zeitraum manuell erneut ausführen, finden Sie zwei neue an die Azure SQL/SQL Server-Datenbank angefügte Datensätze. In diesem Beispiel wird angenommen, dass keine der Spalten in der Tabelle die Primärschlüsseleinschränkung aufweist.

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

Um dieses Verhalten zu vermeiden, müssen Sie mit einem der beiden folgenden Verfahren UPSERT-Semantik angeben:

### <a name="mechanism-1-using-sqlwritercleanupscript"></a>Verfahren 1: mithilfe von sqlWriterCleanupScript
Sie können die **SqlWriterCleanupScript**-Eigenschaft zum Bereinigen von Daten aus der Senkentabelle verwenden, bevor bei der Sliceausführung die Daten eingefügt werden. 

```json
"sink":  
{ 
  "type": "SqlSink", 
  "sqlWriterCleanupScript": "$$Text.Format('DELETE FROM table WHERE ModifiedDate >= \\'{0:yyyy-MM-dd HH:mm}\\' AND ModifiedDate < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
}
```

Wenn ein Slice ausgeführt wird, wird das Bereinigungsskript zuerst ausgeführt, um Daten zu löschen, die dem Slice aus der SQL-Tabelle entsprechen. Anschließend fügt die Kopieraktivität Daten in die SQL-Tabelle ein. Wenn der Slice erneut ausgeführt wird, wird die Menge wie gewünscht aktualisiert.

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

Angenommen, der Datensatz "Flat Washer" wurde aus der ursprünglichen CSV-Datei entfernt. Dann würde die erneutes Ausführung des Slices das folgende Ergebnis hervorbringen: 

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
7     Down Tube    4            2015-05-01 00:00:00
```

Die Kopieraktivität hat das Bereinigungsskript ausgeführt, um die dem Slice entsprechenden Daten zu löschen. Dann wurde die Eingabe aus der CSV-Datei (die jetzt nur noch einen Datensatz enthielt) gelesen und in die Tabelle eingefügt. 

### <a name="mechanism-2-using-sliceidentifiercolumnname"></a>Verfahren 2: mithilfe von sliceIdentifierColumnName
> [!IMPORTANT]
> sliceIdentifierColumnName wird für Azure Synapse Analytics derzeit nicht unterstützt. 

Das zweite Verfahren zum Erreichen von Wiederholbarkeit besteht in der Verwendung einer dedizierten Spalte (sliceIdentifierColumnName) in der Zieltabelle. Diese Spalte wird von Azure Data Factory verwendet, um sicherzustellen, dass Quell- und Zielserver synchron bleiben. Dieser Ansatz funktioniert, wenn das Schema der SQL-Zieltabelle flexibel geändert oder definiert werden kann. 

Diese Spalte wird von Azure Data Factory zum Zweck der Wiederholbarkeit verwendet, wobei Azure Data Factory keine Änderungen am Schema der Tabelle vornimmt. So setzen Sie dieses Verfahren um:

1. Definieren Sie in der SQL-Zieltabelle eine Spalte vom Typ **binary (32)** . Für diese Spalte dürfen keine Einschränkungen gelten. Benennen wir für die Zwecke dieses Beispiels die Spalte „AdfSliceIdentifier“.


    Quelltabelle:

    ```sql
    CREATE TABLE [dbo].[Student](
       [Id] [varchar](32) NOT NULL,
       [Name] [nvarchar](256) NOT NULL
    )
    ```

    Zieltabelle: 

    ```sql
    CREATE TABLE [dbo].[Student](
       [Id] [varchar](32) NOT NULL,
       [Name] [nvarchar](256) NOT NULL,
       [AdfSliceIdentifier] [binary](32) NULL
    )
    ```

1. Verwenden Sie sie wie folgt in der Kopieraktivität:
   
    ```json
    "sink":  
    { 
   
        "type": "SqlSink", 
        "sliceIdentifierColumnName": "AdfSliceIdentifier"
    }
    ```

Azure Data Factory füllt diese Spalte den Anforderungen entsprechend auf, um sicherzustellen, dass Quell- und Zielserver synchron bleiben. Die Werte dieser Spalte dürfen außerhalb dieses Kontexts nicht verwendet werden. 

Ähnlich wie bei Verfahren 1 bereinigt die Kopieraktivität automatisch die Daten für den angegebenen Slice aus der SQL-Zieltabelle. Anschließend werden Daten aus der Quelle in die Zieltabelle eingefügt. 

## <a name="next-steps"></a>Nächste Schritte
Gehen Sie die folgenden Connector-Artikel durch, um vollständige JSON-Beispiele zu finden: 

- [Azure SQL-Datenbank](data-factory-azure-sql-connector.md)
- [Azure Synapse Analytics](data-factory-azure-sql-data-warehouse-connector.md)
- [SQL Server](data-factory-sqlserver-connector.md)
