---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
ms.author: jingwang
ms.openlocfilehash: 24bb7a1fcb1569922fb34034fb3c0d003cdd7061
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178558"
---
## <a name="repeatability-during-copy"></a>Wiederholbarkeit beim Kopieren
Beim Kopieren von Daten in Azure SQL/SQL Server aus anderen Datenspeichern muss die Wiederholbarkeit berücksichtigt werden, um unbeabsichtigte Ergebnisse zu vermeiden. 

Beim Kopieren von Daten in eine Azure SQL/SQL Server-Datenbank fügt die Kopieraktivität das Dataset standardmäßig mit APPEND an die Senkentabelle an. Wenn Sie z. B. Daten aus einer CSV-Datei mit zwei Datensätzen in eine Azure SQL-/SQL Server-Datenbank kopieren, sieht die Tabelle wie folgt aus:

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
```

Angenommen, Sie haben Fehler in der Quelldatei gefunden und die Menge von "Down Tube" in der Quelldatei von 2 in 4 geändert. Wenn Sie den Datenslice für diesen Zeitraum wiederholen, finden Sie zwei neue an die Azure SQL-/SQL Server-Datenbank angefügte Datensätze. Nachstehend wird angenommen, dass keine der Spalten der Tabelle die Einschränkung "Primärschlüssel" aufweist.

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

Um dies zu vermeiden, müssen Sie UPSERT-Semantik (zum Aktualisieren und Einfügen) angeben, indem Sie eines der beiden folgenden Verfahren nutzen.

> [!NOTE]
> Ein Slice kann in Azure Data Factory automatisch gemäß der angegebenen Wiederholungsrichtlinie wiederholt werden.
> 
> 

### <a name="mechanism-1"></a>Verfahren 1
Sie können die **SqlWriterCleanupScript** -Eigenschaft verwenden, um zunächst eine Systembereinigungsaktion auszuführen, wenn ein Slice ausgeführt wird. 

```json
"sink":  
{ 
  "type": "SqlSink", 
  "sqlWriterCleanupScript": "$$Text.Format('DELETE FROM table WHERE ModifiedDate >= \\'{0:yyyy-MM-dd HH:mm}\\' AND ModifiedDate < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
}
```

Das Bereinigungsskript wird zuerst während des Kopiervorgangs für einen bestimmten Slice ausgeführt, wobei die Daten aus der SQL-Tabelle gelöscht werden, die diesem Slice entspricht. Die Aktivität fügt anschließend die Daten in die SQL-Tabelle ein. 

Wenn der Slice nun wiederholt wird, erkennen Sie, dass die Menge wie gewünscht aktualisiert wurde.

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

Angenommen, der Datensatz "Flat Washer" wurde aus der ursprünglichen CSV-Datei entfernt. Bei Wiederholen des Slices wird das folgende Ergebnis erzeugt: 

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
7     Down Tube    4            2015-05-01 00:00:00
```
Weitere Schritte müssen nicht erfolgen. Die Kopieraktivität hat das Bereinigungsskript ausgeführt, um die dem Slice entsprechenden Daten zu löschen. Dann wurde die Eingabe aus der CSV-Datei (die jetzt nur noch 1 Datensatz enthielt) gelesen und in die Tabelle eingefügt. 

### <a name="mechanism-2"></a>Verfahren 2
> [!IMPORTANT]
> sliceIdentifierColumnName wird für Azure SQL Data Warehouse derzeit nicht unterstützt. 

Ein anderes Verfahren zum Erreichen von Wiederholbarkeit sieht eine dedizierte Spalte (**SliceIdentifierColumnName**) in der Zieltabelle vor. Diese Spalte wird von Azure Data Factory verwendet, um sicherzustellen, dass Quell- und Zielserver synchron bleiben. Dieser Ansatz funktioniert, wenn das Schema der SQL-Zieltabelle flexibel geändert oder definiert werden kann. 

Diese Spalte wird von Azure Data Factory zum Zweck der Wiederholbarkeit verwendet, wobei Azure Data Factory keine Änderungen am Schema der Tabelle vornimmt. So setzen Sie dieses Verfahren um:

1. Definieren Sie in der SQL-Zieltabelle eine Spalte vom Typ "binary (32)". Für diese Spalte dürfen keine Einschränkungen gelten. Lassen Sie uns diese Spalte für dieses Beispiel "ColumnForADFuseOnly" nennen.
2. Verwenden Sie sie wie folgt in der Kopieraktivität:
   
    ```json
    "sink":  
    { 
   
        "type": "SqlSink", 
        "sliceIdentifierColumnName": "ColumnForADFuseOnly"
    }
    ```

Azure Data Factory füllt diese Spalte den Anforderungen entsprechend auf, um sicherzustellen, dass Quell- und Zielserver synchron bleiben. Die Werte dieser Spalte dürfen außerhalb dieses Kontexts nicht vom Benutzer verwendet werden. 

Ähnlich wie bei Verfahren 1 bereinigt die Kopieraktivität zunächst automatisch die Daten eines bestimmten Slices in der SQL-Zieltabelle. Anschließend wird die Kopieraktivität normal ausgeführt, um für diesen Slice Daten aus der Quelle im Ziel einzufügen. 

