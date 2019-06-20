---
title: Einrichten einer Quelltransformation in der Mapping Data Flow-Funktion von Azure Data Factory
description: Erfahren Sie, wie Sie eine Quelltransformation in Mapping Data Flow einrichten.
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: 51c1ea7b554178f7fb3f264bf731ffd5872ceea2
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66234549"
---
# <a name="source-transformation-for-mapping-data-flow"></a>Quelltransformation für Mapping Data Flow 

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Mit einer Quelltransformation wird die Datenquelle für den Datenfluss konfiguriert. Ein Datenfluss kann mehrere Quelltransformationen enthalten. Beginnen Sie beim Entwerfen von Datenflüssen immer mit einer Quelltransformation.

Jeder Datenfluss erfordert mindestens eine Quelltransformation. Fügen Sie so viele Quellen hinzu, wie Sie benötigen, um Ihre Datentransformationen abzuschließen. Sie können diese Quellen zusammen mit einer Join- oder Union-Transformation verwenden.

> [!NOTE]
> Beim Debuggen des Datenflusses werden Daten mithilfe der Samplingeinstellung oder der Debugquellenlimits aus der Quelle gelesen. Um Daten in eine Senke zu schreiben, müssen Sie Ihren Datenfluss aus einer Datenflussaktivität in der Pipeline ausführen. 

![Optionen der Quelltransformation auf der Registerkarte „Quelleinstellungen“](media/data-flow/source.png "Quelle")

Ordnen Sie Ihre Datenfluss-Quelltransformation genau einem Data Factory-Dataset zu. Das Dataset definiert die Form und Position der Daten, in die geschrieben oder aus denen gelesen werden soll. Sie können Platzhalter und Dateilisten in Ihrer Quelle verwenden, um mit mehreren Dateien gleichzeitig zu arbeiten.

## <a name="data-flow-staging-areas"></a>Datenfluss-Stagingbereiche

Datenflüsse arbeiten mit *Stagingdatasets*, die sich alle in Azure befinden. Verwenden Sie diese Datasets für den Stagingprozess, wenn Sie Ihre Daten transformieren. 

Data Factory hat Zugriff auf nahezu 80 native Connectors. Um Daten aus diesen anderen Quellen in Ihren Datenfluss einzubeziehen, verwenden Sie die Kopieraktivität, um Daten in einen dieser Stagingbereiche des Datenflussdatasets aufzunehmen.

## <a name="options"></a>Optionen

Wählen Sie das Schema und die Samplingoptionen für Ihre Daten aus.

### <a name="allow-schema-drift"></a>Schemaabweichung zulassen
Wählen Sie **Schemaabweichung zulassen** aus, wenn die Quellspalten häufig geändert werden. Diese Einstellung ermöglicht es, dass alle eingehenden Quellfelder durch die Transformationen zur Senke fließen.

### <a name="validate-schema"></a>Überprüfen des Schemas

Wenn die eingehende Version der Quelldaten nicht mit dem definierten Schema übereinstimmt, führt das Ausführen des Datenflusses zu einem Fehler.

![Einstellungen für die öffentliche Quelle mit den Optionen „Schema überprüfen“, „Schemaabweichung zulassen“ und „Sampling“](media/data-flow/source1.png "Öffentliche Quelle 1")

### <a name="sample-the-data"></a>Sampling der Daten
Aktivieren Sie **Sampling**, um die Anzahl der Zeilen aus der Quelle zu beschränken. Verwenden Sie diese Einstellung, wenn Sie für das Debuggen Stichproben der Daten an der Quelle erstellen möchten.

## <a name="define-schema"></a>Definieren des Schemas

Wenn Ihre Quelldateitypen nicht stark typisiert sind (z. B. Flatfile-Dateien im Gegensatz zu Parquet-Dateien), sollten Sie hier in der Quelltransformation die Datentypen für jedes Feld definieren.  

![Einstellungen der Quelltransformation auf die Registerkarte „Schema definieren“](media/data-flow/source2.png "Quelle 2")

Sie können die Spaltennamen später in einer select-Transformation ändern. Verwenden Sie eine Transformation für abgeleitete Spalten, um die Datentypen zu ändern. Für stark typisierte Quellen können Sie die Datentypen in einer späteren select-Transformation ändern. 

![Datentypen in einer select-Transformation](media/data-flow/source003.png "Datentypen")

### <a name="optimize-the-source-transformation"></a>Optimieren der Quelltransformation

Auf der Registerkarte **Optimieren** für die Quelltransformation wird möglicherweise der Partitionstyp **Quelle** angezeigt. Diese Option ist nur verfügbar, wenn die Quelle eine Azure SQL-Datenbank ist. Dies liegt daran, dass Data Factory versucht, parallele Verbindungen herzustellen, um umfangreiche Abfragen für Ihre SQL-Datenbankquelle auszuführen.

![Einstellungen der Quellpartition](media/data-flow/sourcepart2.png "Partitionierung")

Sie verfügen nicht über Partitionsdaten in Ihrer SQL-Datenbank-Quelle, Partitionen sind aber besonders hilfreich für große Abfragen. Sie können Ihre Partitionierung auf eine Spalte oder einer Abfrage basieren.

### <a name="use-a-column-to-partition-data"></a>Verwenden einer Spalte zum Partitionieren von Daten

Wählen Sie in Ihrer Quelltabelle eine Spalte aus, nach der partitioniert werden soll. Legen Sie auch die maximale Anzahl von Verbindungen fest.

### <a name="use-a-query-to-partition-data"></a>Verwenden einer Abfrage zum Partitionieren von Daten

Sie können die Verbindungen basierend auf einer Abfrage partitionieren. Geben Sie einfach den Inhalt eines WHERE-Prädikats ein. Geben Sie beispielsweise „year > 1980“ ein.

## <a name="source-file-management"></a>Quelldateiverwaltung

Wählen Sie die Einstellungen zum Verwalten von Dateien in Ihrer Quelle aus. 

![Einstellungen für eine neue Quelle](media/data-flow/source2.png "Einstellungen für neue Quelle")

* **Platzhalterpfad:** Wählen Sie in Ihrem Quellordner eine Reihe von Dateien aus, die einem Muster entsprechen. Diese Einstellung überschreibt alle Dateien in Ihrer Datasetdefinition.
* **Liste der Dateien:** Dies ist eine Dateigruppe. Erstellen Sie eine Textdatei mit einer Liste der relativen Pfade der zu verarbeitenden Dateien. Verweisen Sie auf diese Textdatei.
* **Spalte für die Speicherung im Dateinamen:** Speichern Sie den Namen der Quelldatei in einer Spalte in den Daten. Geben Sie hier einen neuen Namen ein, um die Zeichenfolge für den Dateinamen zu speichern.
* **Nach der Fertigstellung:** Wählen Sie aus, ob Sie nach dem Ausführen des Datenflusses nichts mit der Quelldatei anstellen, die Quelldatei löschen oder die Quelldateien verschieben möchten. Die Pfade für das Verschieben sind relative Pfade.

### <a name="sql-datasets"></a>SQL-Datasets

Wenn sich Ihre Quelle in SQL-Datenbank oder SQL Data Warehouse befindet, stehen Ihnen zusätzliche Optionen für die Verwaltung von Quelldateien zur Verfügung.

* **Query** (Abfrage): Geben Sie eine SQL-Abfrage für die Quelle ein. Diese Einstellung überschreibt jede Tabelle, die Sie im Dataset ausgewählt haben. Beachten Sie, dass **Order By**-Klauseln hier nicht unterstützt werden. Sie können aber eine vollständige SELECT FROM-Anweisung festlegen. Sie können auch benutzerdefinierte Tabellenfunktionen verwenden. **select * from udfGetData()** ist eine benutzerdefinierte Funktion in SQL, die eine Tabelle zurückgibt. Diese Abfrage generiert eine Quelltabelle, die Sie in Ihrem Datenfluss verwenden können.
* **Batchgröße**: Geben Sie eine Batchgröße ein, um große Datenmengen in Leseblöcke zu segmentieren.

> [!NOTE]
> Die Dateivorgänge werden nur ausgeführt, wenn der Datenfluss anhand der Aktivität zum Ausführen des Datenflusses in einer Pipeline über eine Pipelineausführung ausgeführt wird (Debuggen der Pipeline oder Ausführung). Dateivorgänge werden *nicht* im Datenfluss-Debugmodus ausgeführt.

### <a name="projection"></a>Projektion

Wie Schemas in Datasets definiert die Projektion in einer Quelle die Datenspalten, Datentypen und Datenformate aus den Quelldaten. 

![Einstellungen auf der Registerkarte „Projektion“](media/data-flow/source3.png "Projektion")

Wenn in Ihrer Textdatei kein Schema definiert ist, wählen Sie **Datentyp erkennen** aus, damit Data Factory Stichproben erstellt und die Datentypen ableitet. Wählen Sie **Standarddatenformat** aus, um die Standarddatenformate automatisch zu ermitteln. 

Sie können die Spaltendatentypen in einer späteren Transformation für abgeleitete Spalten ändern. Verwenden Sie eine Transformation, um die Spaltennamen zu ändern.

![Einstellungen für Standarddatenformate](media/data-flow/source2.png "Standardformate")

## <a name="next-steps"></a>Nächste Schritte

Beginnen Sie mit dem Erstellen einer [Transformation für abgeleitete Spalten](data-flow-derived-column.md) und einer [select-Transformation](data-flow-select.md).
