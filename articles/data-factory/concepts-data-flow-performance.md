---
title: Anleitung zur Leistung und Optimierung von Mapping Data Flow in Azure Data Factory | Microsoft-Dokumentation
description: Erfahren Sie, welche Faktoren sich entscheidend auf die Leistung von Datenflüssen in Azure Data Factory auswirken, wenn Sie die Mapping Data Flow-Funktion verwenden.
author: kromerm
ms.topic: conceptual
ms.author: makromer
ms.service: data-factory
ms.date: 05/16/2019
ms.openlocfilehash: 7fca586083f70e0b0f7e593d5203392260cd2136
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66172441"
---
# <a name="mapping-data-flows-performance-and-tuning-guide"></a>Anleitung zur Leistung und Optimierung der Mapping Data Flow-Funktion

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Azure Data Factory Mapping Data Flow bietet eine codefreie Browserschnittstelle zum Entwerfen, Bereitstellen und Orchestrieren von Datentransformationen in beliebigen Größenordnungen.

> [!NOTE]
> Falls Sie mit dem grundlegenden Konzept von ADF Mapping Data Flow noch nicht vertraut sind, lesen Sie unter [Datenflüsse – Übersicht](concepts-data-flow-overview.md) nach, bevor Sie sich mit diesem Artikel beschäftigen.
>

> [!NOTE]
> Stellen Sie beim Entwerfen und Testen von Datenflüssen auf der Benutzeroberfläche von ADF sicher, dass das Debuggen aktiviert ist, damit Sie Ihre Datenflüsse in Echtzeit ausführen können, ohne auf die Aktivierung eines Clusters warten zu müssen.
>

![Schaltfläche „Debuggen“](media/data-flow/debugb1.png "Debuggen")

## <a name="optimizing-for-azure-sql-database"></a>Optimieren für Azure SQL-Datenbank

![Bereich für die Quelle](media/data-flow/sourcepart2.png "Bereich für die Quelle")

### <a name="you-can-match-spark-data-partitioning-to-your-source-database-partitioning-based-on-a-database-table-column-key-in-the-source-transformation"></a>Sie können die Partitionierung Ihrer Quelldatenbank an die Datenpartitionierung von Spark anpassen, indem Sie den Schlüssel der Spalte in der Datenbanktabelle in der Quelltransformation verwenden.

* Navigieren Sie zu „Optimieren“, und wählen Sie „Quelle“ aus. Legen Sie eine bestimmte Spalte oder einen Typ in einer Abfrage fest.
* Wenn Sie „Spalte“ ausgewählt haben, wählen Sie noch die Partitionsspalte aus.
* Legen Sie auch die maximale Anzahl von Verbindungen mit Ihrer Azure SQL-Datenbank-Instanz fest. Sie können eine höhere Einstellung testen, um parallele Verbindungen mit Ihrer Datenbank nutzen zu können. In einigen Fällen erreichen Sie jedoch mit einer begrenzten Anzahl von Verbindungen eine höhere Leistung.

### <a name="set-batch-size-and-query-on-source"></a>Festlegen der Batchgröße und Abfragen der Quelle

![Quelle](media/data-flow/source4.png "Quelle")

* Durch das Festlegen der Batchgröße weisen Sie ADF an, Daten im Speicher und nicht zeilenweise zu speichern. Dies ist eine optionale Einstellung. Sie könnte dazu führen, dass Sie nicht genügend Ressourcen auf den Serverknoten haben, wenn diese nicht ordnungsgemäß dimensioniert sind.
* Das Festlegen einer Abfrage ermöglicht Ihnen das Filtern der Zeilen direkt in der Quelle – bevor Sie überhaupt den Datenfluss für die Verarbeitung erreichen. Dies kann die anfängliche Datenerfassung beschleunigen.
* Wenn Sie eine Abfrage verwenden, können Sie optional Abfragehinweise für Ihre Azure SQL-Datenbank-Instanz hinzufügen, d. h. READ UNCOMMITTED.

### <a name="set-sink-batch-size"></a>Festlegen der Batchgröße für die Senke

![Senke](media/data-flow/sink4.png "Senke")

* Um die zeilenweise Verarbeitung Ihrer Datenflüsse zu vermeiden, legen Sie die „Batchgröße“ in den Senkeneinstellungen für Azure SQL-Datenbank fest. Dadurch wird ADF angewiesen, Schreibvorgänge in der Datenbank in Batches basierend auf der angegebenen Größe zu verarbeiten.

### <a name="set-partitioning-options-on-your-sink"></a>Festlegen von Partitionierungsoptionen für die Senke

* Auch wenn Sie Ihre Daten in den Azure SQL-Datenbank-Zieltabellen nicht partitionieren, sollten Sie die Partitionierung auf der Registerkarte „Optimieren“ festlegen.
* Häufig führt schon die Festlegung der Roundrobin-Partitionierung in ADF dazu, dass Ladevorgänge in den Spark-Ausführungsclustern sehr viel schneller ablaufen, ohne dass alle Verbindungen von einem einzelnen Knoten bzw. einer Partition stammen müssen.

### <a name="increase-size-of-your-compute-engine-in-azure-integration-runtime"></a>Vergrößern der Compute-Engine in Azure Integration Runtime

![Neue IR](media/data-flow/ir-new.png "Neue IR")

* Erhöhen Sie die Anzahl der Kerne. Damit erhöhen Sie auch die Anzahl der Knoten, sodass Ihnen mehr Verarbeitungsleistung zum Abfragen und Schreiben in Ihrer Azure SQL-Datenbank-Instanz zur Verfügung steht.
* Versuchen Sie auch, die Optionen „Compute-optimiert“ und „Arbeitsspeicheroptimiert“ auf zusätzliche Ressourcen für Ihre Serverknoten anzuwenden.

### <a name="disable-indexes-on-write"></a>Deaktivieren von Indizes beim Schreiben
* Verwenden Sie vor der Datenflussaktivität eine Aktivität mit einer gespeicherten Prozedur in Ihrer ADF-Pipeline, die Indizes in Ihren Zieltabellen deaktiviert, in die von der Senke aus geschrieben wird.
* Fügen Sie nach der Datenflussaktivität eine weitere Aktivität mit einer gespeicherten Prozedur hinzu, die diese Indizes aktiviert.

### <a name="increase-the-size-of-your-azure-sql-db"></a>Erhöhen der Größe Ihrer Azure SQL-Datenbank-Instanz
* Planen Sie die Anpassung der Größe Ihrer Quelle und Senke in Azure SQL-Datenbank vor der Ausführung Ihrer Pipeline, um den Durchsatz zu erhöhen und die Drosselung durch Azure beim Erreichen der DTU-Grenzwerte zu minimieren.
* Nach Abschluss der Pipelineausführung können Sie die Größe Ihrer Datenbanken für die normale Ausführung wiederherstellen.

## <a name="next-steps"></a>Nächste Schritte
Lesen Sie die folgenden Artikel zu Datenflüssen:

- [Übersicht über Datenflüsse](concepts-data-flow-overview.md)
- [Datenflussaktivität](control-flow-execute-data-flow-activity.md)

