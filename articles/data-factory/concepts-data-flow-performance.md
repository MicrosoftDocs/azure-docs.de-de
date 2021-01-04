---
title: Anleitung zur Leistung und Optimierung des Zuordnungsdatenflusses
description: Erfahren Sie, welche Faktoren sich entscheidend auf die Leistung der Mapping Data Flow-Funktion in Azure Data Factory auswirken.
author: kromerm
ms.topic: conceptual
ms.author: makromer
ms.service: data-factory
ms.custom: seo-lt-2019
ms.date: 11/24/2020
ms.openlocfilehash: cc06f12317f5e30721452e07bd4dc5f50dfdb7ec
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96022359"
---
# <a name="mapping-data-flows-performance-and-tuning-guide"></a>Anleitung zur Leistung und Optimierung der Mapping Data Flow-Funktion

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Die Funktion „Zuordnungsdatenfluss“ (Mapping Data Flow) in Azure Data Factory verfügt über eine codefreie Benutzeroberfläche zum Entwerfen und Ausführen von Datentransformationen im großen Stil. Wenn Sie mit der Mapping Data Flow-Funktion nicht vertraut sind, finden Sie weitere Informationen in der [Übersicht über Mapping Data Flow](concepts-data-flow-overview.md). In diesem Artikel werden verschiedene Möglichkeiten beschrieben, wie Sie Ihre Datenflüsse so anpassen und optimieren können, dass sie Ihre Leistungsbenchmarks erreichen.

Sehen Sie sich das folgende Video an, das einige Beispiele für die erforderliche Dauer beim Transformieren von Daten mit Datenflüssen enthält:

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4rNxM]

## <a name="testing-data-flow-logic"></a>Testen der Datenflusslogik

Beim Entwerfen und Testen von Datenflüssen über die ADF-Benutzeroberfläche können Sie im Debugmodus einen interaktiven Test für einen Spark-Livecluster durchführen. Dies ermöglicht Ihnen das Anzeigen einer Vorschau für die Daten und das Ausführen Ihrer Datenflüsse, ohne auf das „Aufwärmen“ eines Clusters warten zu müssen. Weitere Informationen finden Sie unter [Debugmodus](concepts-data-flow-debug-mode.md).

## <a name="monitoring-data-flow-performance"></a>Überwachen der Datenflussleistung

Nachdem Sie Ihre Transformationslogik im Debugmodus überprüft haben, sollten Sie Ihren Datenfluss per End-to-End-Vorgang als Aktivität in einer Pipeline ausführen. Datenflüsse werden in einer Pipeline mit der [Aktivität zum Ausführen eines Datenflusses](control-flow-execute-data-flow-activity.md) operationalisiert. Die Datenflussaktivität verfügt im Gegensatz zu anderen Azure Data Factory-Aktivitäten über eine einzigartige Überwachungsoberfläche, auf der ein ausführlicher Ausführungsplan und das Leistungsprofil der Transformationslogik angezeigt werden. Klicken Sie in der Ausgabe der Aktivitätsausführung einer Pipeline auf das Brillensymbol, um für den Datenfluss ausführliche Überwachungsinformationen anzuzeigen. Weitere Informationen finden Sie unter [Überwachen von Datenflüssen](concepts-data-flow-monitoring.md).

![Datenflussüberwachung](media/data-flow/monitoring-details.png "Datenflussüberwachung 2")

Beim Überwachen der Leistung von Datenflüssen gibt es vier mögliche Engpässe, auf die Sie achten sollten:

* Startzeit des Clusters
* Lesen von einer Quelle
* Transformationszeit
* Schreiben in eine Senke 

![Datenflussüberwachung](media/data-flow/monitoring-performance.png "Datenflussüberwachung 3")

Die Startzeit des Clusters ist die benötigte Dauer für den Start eines Apache Spark-Clusters. Dieser Wert befindet sich oben rechts auf dem Überwachungsbildschirm. Datenflüsse basieren auf einem Just-In-Time-Modell, bei dem für jeden Auftrag ein isolierter Cluster verwendet wird. Diese Startzeit hat normalerweise eine Dauer von drei bis fünf Minuten. Für sequenzielle Aufträge kann dies reduziert werden, indem ein Wert für die Gültigkeitsdauer aktiviert wird. Weitere Informationen finden Sie unter [Optimieren der Azure Integration Runtime](#ir).

Für Datenflüsse wird ein Spark-Optimierer genutzt, mit dem Ihre Geschäftslogik in „Stufen“ neu sortiert und ausgeführt wird, damit die Dauer möglichst kurz ist. Für jede Senke, in die Ihr Datenfluss geschrieben wird, sind in der Ausgabe der Überwachung die Dauer der einzelnen Transformationsstufen sowie die Zeiten enthalten, die zum Schreiben der Daten in die Senke benötigt werden. Anhand der längsten Dauer können Sie wahrscheinlich den Engpass für Ihren Datenfluss ermitteln. Falls die Transformationsstufe mit der längsten Dauer eine Quelle enthält, sollten Sie überlegen, ob Sie Ihre Lesedauer weiter optimieren können. Wenn eine Transformation lange dauert, müssen Sie ggf. neu partitionieren oder die Größe für Ihre Integration Runtime erhöhen. Bei einer langen Dauer der Senkenverarbeitung müssen Sie unter Umständen Ihre Datenbank hochskalieren oder sich vergewissern, dass die Ausgabe nicht nur in eine einzelne Datei erfolgt.

Nachdem Sie den Engpass für Ihren Datenfluss identifiziert haben, sollten Sie die unten angegebenen Strategien nutzen, um die Leistung zu verbessern.

## <a name="optimize-tab"></a>Registerkarte „Optimieren“

Die Registerkarte **Optimieren** enthält Einstellungen zum Konfigurieren des Partitionsschemas für den Spark-Cluster. Diese Registerkarte ist für jede Transformation des Datenflusses vorhanden und gibt an, ob Sie die Daten neu partitionieren sollten, **nachdem** die Transformation abgeschlossen ist. Das Anpassen der Partitionierung bietet Kontrolle über die Verteilung Ihrer Daten auf Computeknoten und Datenstandortoptimierungen, die sowohl positive als auch negative Auswirkungen auf die gesamte Datenflussleistung haben können.

![Screenshot der Registerkarte „Optimieren“ mit der Option „Partition“, dem Partitionstyp und der Anzahl von Partitionen](media/data-flow/optimize.png)

Standardmäßig ist die Option *Aktuelle Partitionierung verwenden* ausgewählt, bei der Azure Data Factory angewiesen wird, die aktuelle Ausgabepartitionierung der Transformation beizubehalten. Da die erneute Partitionierung einige Zeit in Anspruch nimmt, ist die Verwendung der Option *Aktuelle Partitionierung verwenden* in den meisten Szenarien zu empfehlen. Szenarien, in denen Sie Ihre Daten ggf. neu partitionieren sollten, sind beispielsweise Aggregations- und Joinvorgänge, bei denen es für Ihre Daten zu einer erheblichen Datenschiefe kommt, oder bei Verwendung einer Quellpartitionierung auf einer SQL-Datenbank-Instanz.

Wenn Sie die Partitionierung für eine Transformation ändern möchten, wählen Sie die Registerkarte **Optimieren** und das Optionsfeld **Partitionierung festlegen** aus. Ihnen werden mehrere Optionen für die Partitionierung angezeigt. Die beste Methode der Partitionierung richtet sich jeweils nach den Datenmengen, Kandidatenschlüsseln, NULL-Werten und der Kardinalität. 

> [!IMPORTANT]
> Bei nur einer Partition werden darin alle verteilten Daten kombiniert. Hierbei handelt es sich um einen sehr langsamen Vorgang, bei dem auch alle nachgeschalteten Transformationen und Schreibvorgänge erheblich beeinträchtigt werden. Für Azure Data Factory wird von dieser Option dringend abgeraten. Ihre Verwendung wird nur empfohlen, wenn es einen triftigen geschäftlichen Grund gibt.

Die folgenden Partitionierungsoptionen sind in jeder Transformation verfügbar:

### <a name="round-robin"></a>Roundrobin 

Per Roundrobin werden Daten gleichmäßig auf die Partitionen verteilt. Verwenden Sie die Option „Roundrobin“, wenn Sie nicht über gute Kandidaten verfügen, um eine solide, intelligente Partitionierungsstrategie zu implementieren. Sie können die Anzahl der physischen Partitionen festlegen.

### <a name="hash"></a>Hash

Azure Data Factory erzeugt einen Hash von Spalten, um einheitliche Partitionen zu erstellen, sodass Zeilen mit ähnlichen Werten in die gleiche Partition fallen. Führen Sie bei Verwendung der Option „Hash“ einen Test auf mögliche Partitionsungleichmäßigkeiten durch. Sie können die Anzahl der physischen Partitionen festlegen.

### <a name="dynamic-range"></a>Dynamischer Bereich

Bei dieser Option werden die dynamischen Spark-Bereiche basierend auf den von Ihnen angegebenen Spalten oder Ausdrücken verwendet. Sie können die Anzahl der physischen Partitionen festlegen. 

### <a name="fixed-range"></a>Fester Bereich

Erstellen Sie einen Ausdruck, der einen festen Bereich für Werte in Ihren partitionierten Datenspalten bereitstellt. Sie sollten über fundiertes Wissen über Ihre Daten verfügen, bevor Sie diese Option verwenden, um Partitionsungleichmäßigkeiten zu vermeiden. Die von Ihnen für den Ausdruck eingegebenen Werte werden als Teil einer Partitionsfunktion verwendet. Sie können die Anzahl der physischen Partitionen festlegen.

### <a name="key"></a>Schlüssel

Wenn Sie gut mit der Kardinalität Ihrer Daten vertraut sind, kann die Schlüsselpartitionierung eine gute Strategie darstellen. Die Schlüsselpartitionierung erstellt Partitionen für jeden eindeutigen Wert in der Spalte. Sie können die Anzahl der Partitionen nicht festlegen, weil die Anzahl auf den eindeutigen Werten in den Daten basiert.

> [!TIP]
> Beim manuellen Festlegen des Partitionierungsschemas werden die Daten neu angeordnet, sodass die Vorteile des Spark-Optimierers ggf. nicht voll zur Geltung kommen. Eine bewährte Methode besteht darin, die Partitionierung nicht manuell festzulegen, sofern dies nicht unbedingt erforderlich ist.

## <a name="logging-level"></a>Protokolliergrad

Wenn Sie nicht voraussetzen, dass jede Pipelineausführung Ihrer Datenflussaktivitäten alle ausführlichen Telemetrieprotokolle vollständig protokolliert, können Sie den Protokolliergrad optional auf „Standard“ oder „Kein“ festlegen. Wenn Sie Ihre Datenflüsse im Modus „Ausführlich“ (Standard) ausführen, fordern Sie an, dass ADF die Aktivität während der Datentransformation auf den einzelnen Partitionsebenen vollständig protokolliert. Da dies ein kostspieliger Vorgang sein kann, kann nur die ausschließliche Aktivierung von „Ausführlich“ bei der Problembehandlung den gesamten Datenfluss und die Pipelineleistung verbessern. Der Modus „Standard“ protokolliert nur die Transformationszeitspannen, während „Kein“ nur eine Zusammenfassung der Zeitspannen bietet.

![Protokolliergrad](media/data-flow/logging.png "Festlegen des Protokolliergrads")

## <a name="optimizing-the-azure-integration-runtime"></a><a name="ir"></a> Optimieren der Azure Integration Runtime

Datenflüsse werden in Spark-Clustern ausgeführt, die zur Laufzeit gestartet werden. Die Konfiguration für den verwendeten Cluster wird in der Integration Runtime (IR) der Aktivität definiert. Beim Definieren Ihrer Integration Runtime müssen drei Leistungsaspekte berücksichtigt werden: Clustertyp, Clustergröße und Gültigkeitsdauer.

Weitere Informationen zum Erstellen einer Integration Runtime finden Sie unter [Integrationslaufzeit in Azure Data Factory](concepts-integration-runtime.md).

### <a name="cluster-type"></a>Clustertyp

In Bezug auf den Typ des gestarteten Spark-Clusters gibt es drei verfügbare Optionen: „Universell“, „Arbeitsspeicheroptimiert“ und „Für Compute optimiert“.

Standardmäßig werden Cluster vom Typ **Universell** verwendet. Sie sind für die meisten Datenflussworkloads am besten geeignet. Bei dieser Option erhalten Sie normalerweise die beste Mischung aus Leistung und Kostenaufwand.

Falls Ihr Datenfluss über viele Joins und Suchvorgänge verfügt, sollten Sie einen Cluster vom Typ **Arbeitsspeicheroptimiert** verwenden. Bei Clustern vom Typ „Arbeitsspeicheroptimiert“ können mehr Daten im Arbeitsspeicher gespeichert werden, und die Menge an Fehlern der Art „Nicht genügend Arbeitsspeicher“ wird stark reduziert. Die Arbeitsspeicheroptimierung ist mit dem höchsten Preis pro Kern verbunden, aber sie führt auch zu erfolgreicheren Pipelines. Wenn beim Ausführen der Datenflüsse Fehler aufgrund von unzureichendem Arbeitsspeicher auftreten, sollten Sie zu einer arbeitsspeicheroptimierten Azure IR-Konfiguration wechseln. 

Der Typ **Für Compute optimiert** ist für ETL-Workflows nicht ideal und wird vom Azure Data Factory-Team für die meisten Produktionsworkloads nicht empfohlen. Für einfachere Datentransformationen, die nicht arbeitsspeicherintensiv sind, z. B. Filtern von Daten oder Hinzufügen von abgeleiteten Spalten, können Cluster vom Typ „Für Compute optimiert“ zu einem günstigeren Preis pro Kern verwendet werden.

### <a name="cluster-size"></a>Clustergröße

Bei Datenflüssen wird die Datenverarbeitung auf unterschiedliche Knoten eines Spark-Clusters verteilt, damit Vorgänge parallel durchgeführt werden können. Bei Verwendung eines Spark-Clusters mit mehr Kernen erhöht sich die Anzahl von Knoten in der Compute-Umgebung. Eine größere Anzahl von Knoten führt zu einer Erhöhung der Verarbeitungsleistung des Datenflusses. Die Erhöhung der Clustergröße ist häufig eine einfache Möglichkeit, um die Verarbeitungszeit zu verkürzen.

Die Standardclustergröße beträgt vier Treiberknoten und vier Workerknoten.  Je mehr Daten Sie verarbeiten, desto größere Cluster sollten Sie verwenden. Unten sind die möglichen Optionen für die Größenanpassung angegeben:

| Workerkerne | Treiberkerne | Total cores (Kerne gesamt) | Notizen |
| ------------ | ------------ | ----------- | ----- |
| 4 | 4 | 8 | Nicht verfügbar für die Option „Für Compute optimiert“ |
| 8 | 8 | 16 | |
| 16 | 16 | 32 | |
| 32 | 16 | 48 | |
| 64 | 16 | 80 | |
| 128 | 16 | 144 | |
| 256 | 16 | 272 | |

Die Preise von Datenflüssen werden nach „Stunden der virtuellen Kerne“ berechnet. Dies bedeutet, dass sowohl die Clustergröße als auch die Ausführungszeit einfließen. Beim Hochskalieren erhöhen sich Ihre Clusterkosten pro Minute, aber die Gesamtdauer verringert sich.

> [!TIP]
> Für die Auswirkung der Größe eines Clusters auf die Leistung eines Datenflusses gibt es eine Obergrenze. Je nach Umfang Ihrer Daten gibt es einen bestimmten Punkt, an dem die Erhöhung der Größe eines Clusters nicht mehr zu einer Verbesserung der Leistung führt. Wenn Sie beispielsweise über mehr Knoten als Partitionen mit Daten verfügen, ist das Hinzufügen weiterer Knoten nicht hilfreich. Eine bewährte Methode besteht darin, klein anzufangen und dann hochzuskalieren, um Ihre Leistungsanforderungen zu erfüllen. 

### <a name="time-to-live"></a>Gültigkeitsdauer

Standardmäßig startet jede Datenflussaktivität basierend auf der IR-Konfiguration einen neuen Cluster. Das Starten des Clusters dauert einige Minuten, und die Datenverarbeitung kann erst gestartet werden, nachdem dieser Vorgang abgeschlossen ist. Wenn Ihre Pipelines mehrere **sequenzielle** Datenflüsse enthalten, können Sie einen Wert für die Gültigkeitsdauer aktivieren. Bei Angabe eines Werts für die Gültigkeitsdauer bleibt ein Cluster nach Abschluss der Ausführung noch eine bestimmte Zeit aktiv. Falls die IR während der Gültigkeitsdauer von einem neuen Auftrag genutzt wird, wird der vorhandene Cluster wiederverwendet und die Startzeit beträchtlich verkürzt. Nachdem der zweite Auftrag abgeschlossen ist, bleibt der Cluster erneut so lange aktiv, wie dies durch die Gültigkeitsdauer vorgegeben ist.

In einem Cluster kann jeweils nur ein Auftrag ausgeführt werden. Wenn ein Cluster verfügbar ist, aber zwei Datenflüsse gestartet werden, wird er nur für einen davon verwendet. Für den zweiten Auftrag wird ein eigener isolierter Cluster gestartet.

Falls die meisten Datenflüsse parallel ausgeführt werden, ist die Aktivierung der Gültigkeitsdauer nicht zu empfehlen. 

> [!NOTE]
> Die Gültigkeitsdauer ist nicht verfügbar, wenn Sie die Integration Runtime mit automatischer Auflösung verwenden.

## <a name="optimizing-sources"></a>Optimieren von Quellen

Für jede Quelle – mit Ausnahme von Azure SQL-Datenbank – lautet die Empfehlung, die Option **Aktuelle Partitionierung verwenden** ausgewählt zu lassen. Beim Lesen von allen anderen Quellsystemen werden Daten von Datenflüssen je nach Größe automatisch gleichmäßig partitioniert. Für ca. 128 MB an Daten wird jeweils eine neue Partition erstellt. Wenn die Datengröße zunimmt, steigt die Anzahl von Partitionen.

Alle benutzerdefinierten Partitionierungen werden durchgeführt, *nachdem* die Daten von Spark eingelesen wurden. Dies wirkt sich negativ auf die Leistung Ihres Datenflusses aus. Dies ist nicht zu empfehlen, da die Daten beim Lesen gleichmäßig partitioniert sind. 

> [!NOTE]
> Die Lesegeschwindigkeiten können aufgrund des Durchsatzes Ihres Quellsystems begrenzt sein.

### <a name="azure-sql-database-sources"></a>Azure SQL-Datenbank-Quellen

Azure SQL-Datenbank verfügt über eine eindeutige Partitionierungsoption, die die Bezeichnung „Quellpartitionierung“ hat. Mit dem Aktivieren der Quellpartitionierung können Sie Ihre Lesedauern für Azure SQL-Datenbank verbessern, indem Sie auf dem Quellsystem parallele Verbindungen aktivieren. Geben Sie die Anzahl von Partitionen und die Partitionierung Ihrer Daten an. Verwenden Sie eine Partitionsspalte mit hoher Kardinalität. Sie können auch eine Abfrage eingeben, die zum Partitionierungsschema Ihrer Quelltabelle passt.

> [!TIP]
> Bei der Quellpartitionierung ist der E/A-Vorgang von SQL Server der Engpass. Das Hinzufügen von zu vielen Partitionen kann dazu führen, dass die Quelldatenbank vollständig ausgelastet ist. Im Allgemeinen sind bei Verwendung dieser Option vier oder fünf Partitionen ideal.

![Quellpartitionierung](media/data-flow/sourcepart3.png "Quellpartitionierung")

#### <a name="isolation-level"></a>Isolationsstufe

Die Isolationsstufe des Lesevorgangs auf einem Azure SQL-Quellsystem hat eine Auswirkung auf die Leistung. Wenn Sie die Option „Lesen ohne Commit“ auswählen, ergibt sich die schnellste Leistung, und Datenbanksperren werden verhindert. Weitere Informationen zu SQL-Isolationsstufen finden Sie unter [Grundlegendes zu Isolationsstufen](https://docs.microsoft.com/sql/connect/jdbc/understanding-isolation-levels).

#### <a name="read-using-query"></a>Lesen per Abfrage

Sie können Lesevorgänge aus Azure SQL-Datenbank durchführen, indem Sie eine Tabelle oder SQL-Abfrage verwenden. Wenn Sie eine SQL-Abfrage ausführen, muss diese beendet werden, bevor die Transformation gestartet werden kann. SQL-Abfragen können nützlich sein, um einen Pushdown für Vorgänge durchzuführen, damit diese dann ggf. schneller ausgeführt werden, und um die Menge der von einer SQL Server-Instanz gelesenen Daten zu reduzieren, z. B. SELECT-, WHERE- und JOIN-Anweisungen. Bei einem Pushdown von Vorgängen ist es nicht mehr möglich, die Herkunft und Leistung der Transformationen nachzuverfolgen, bevor die Daten in den Datenfluss gelangen.

### <a name="azure-synapse-analytics-sources"></a>Azure Synapse Analytics-Quellen

Bei Verwendung von Azure Synapse Analytics ist in den Quelloptionen die Einstellung **Staging aktivieren** vorhanden. Dies ermöglicht ADF das Lesen aus Synapse über ```Polybase```. Bei dieser Vorgehensweise ergibt sich eine starke Verbesserung der Leseleistung. Beim Aktivieren von ```Polybase``` müssen Sie in den Einstellungen für Datenflussaktivitäten einen Azure Blob Storage- oder Azure Data Lake Storage Gen2-Stagingspeicherort angeben.

![Staging aktivieren](media/data-flow/enable-staging.png "Staging aktivieren")

### <a name="file-based-sources"></a>Dateibasierte Quellen

Für Datenflüsse werden zwar verschiedene Dateitypen unterstützt, aber für Azure Data Factory wird empfohlen, zur Erzielung von optimalen Lese- und Schreibdauern das native Spark-Format „Parquet“ zu verwenden.

Wenn Sie den gleichen Datenfluss für mehrere Dateien ausführen, empfehlen wir Ihnen, die Daten aus einem Ordner zu lesen, Platzhalterpfade zu verwenden oder die Daten aus einer Liste mit Dateien zu lesen. Mit nur einer Datenfluss-Aktivitätsausführung können Ihre gesamten Dateien als Batch verarbeitet werden. Weitere Informationen zum Festlegen dieser Einstellungen finden Sie in der Dokumentation zu Connectors, z. B. [Azure Blob Storage](connector-azure-blob-storage.md#source-transformation).

Vermeiden Sie nach Möglichkeit die Verwendung der For-Each-Aktivität, um Datenflüsse für mehrere Dateien auszuführen. Dies führt dazu, dass bei jeder Iteration des For-Each-Vorgangs ein eigener Spark-Cluster gestartet wird. Häufig ist dies nicht nötig, und diese Vorgehensweise kann teuer werden. 

## <a name="optimizing-sinks"></a>Optimieren von Senken

Wenn Datenflüsse in Senken schreiben, werden alle benutzerdefinierten Partitionierungen direkt vor dem Schreibvorgang durchgeführt. Wie auch bei der Quelle, ist es in den meisten Fällen zu empfehlen, die Auswahl der Partitionierungsoption **Aktuelle Partitionierung verwenden** beizubehalten. Partitionierte Daten werden deutlich schneller als nicht partitionierte Daten geschrieben. Dies gilt auch, wenn Ihr Ziel nicht partitioniert ist. Unten sind die einzelnen Aspekte für die verschiedenen Senkentypen angegeben. 

### <a name="azure-sql-database-sinks"></a>Azure SQL-Datenbank-Senken

Bei Azure SQL-Datenbank sollte die Standardpartitionierung in den meisten Fällen funktionieren. Es besteht die Möglichkeit, dass Ihre Senke über zu viele Partitionen verfügt, sodass sie von Ihrer SQL-Datenbank-Instanz nicht mehr verarbeitet werden können. Reduzieren Sie in diesem Fall die Anzahl von Partitionen, die von Ihrer SQL-Datenbank-Senke ausgegeben werden.

#### <a name="impact-of-error-row-handling-to-performance"></a>Auswirkung der Fehlerzeilenbehandlung auf die Leistung

Wenn Sie die Fehlerzeilenbehandlung („Bei Fehler fortsetzen“) in der Senkentransformation aktivieren, führt ADF einen zusätzlichen Schritt aus, bevor die kompatiblen Zeilen in die Zieltabelle geschrieben werden. Dieser zusätzliche Schritt führt zu einer geringen Leistungseinbuße, die für diesen Schritt mit etwa 5 % veranschlagt werden kann. Eine zusätzliche kleine Leistungseinbuße tritt auf, wenn Sie auch die Option für inkompatible Zeilen für eine Protokolldatei festlegen.

#### <a name="disabling-indexes-using-a-sql-script"></a>Deaktivieren von Indizes per SQL-Skript

Durch das Deaktivieren von Indizes vor dem Laden in eine SQL-Datenbank kann die Leistung beim Schreiben in die Tabelle erheblich verbessert werden. Führen Sie den unten angegebenen Befehl aus, bevor Sie in Ihre SQL-Senke schreiben.

`ALTER INDEX ALL ON dbo.[Table Name] DISABLE`

Nachdem der Schreibvorgang abgeschlossen ist, sollten Sie die Indizes mit dem folgenden Befehl neu erstellen:

`ALTER INDEX ALL ON dbo.[Table Name] REBUILD`

Dies kann sowohl nativ mit Pre- und Post-SQL-Skripts auf einer Azure SQL-Datenbank-Instanz als auch über eine Synapse-Senke in Zuordnungsdatenflüssen durchgeführt werden.

![Indizes deaktivieren](media/data-flow/disable-indexes-sql.png "Indizes deaktivieren")

> [!WARNING]
> Beim Deaktivieren von Indizes übernimmt der Datenfluss quasi die Kontrolle über eine Datenbank, und die Durchführung von Abfragen ist dann wahrscheinlich nicht erfolgreich. Aus diesem Grund werden viele ETL-Aufträge nachts ausgelöst, um Konflikte dieser Art zu vermeiden. Weitere Informationen finden Sie im Artikel [Deaktivieren von Indizes und Einschränkungen](https://docs.microsoft.com/sql/relational-databases/indexes/disable-indexes-and-constraints).

#### <a name="scaling-up-your-database"></a>Hochskalieren Ihrer Datenbank

Planen Sie die Anpassung der Größe Ihrer Quelle und Senke in Azure SQL-Datenbank und Azure SQL Data Warehouse vor der Pipelineausführung, um den Durchsatz zu erhöhen und die Drosselung durch Azure beim Erreichen der DTU-Grenzwerte zu minimieren. Stellen Sie nach Abschluss der Pipelineausführung die Größe Ihrer Datenbanken für die normale Ausführung wieder her.

### <a name="azure-synapse-analytics-sinks"></a>Azure Synapse Analytics-Senken

Stellen Sie beim Schreiben in Azure Synapse Analytics sicher, dass die Option **Staging aktivieren** aktiviert ist. Hierdurch wird für ADF das Schreiben mit [PolyBase](/sql/relational-databases/polybase/polybase-guide) ermöglicht, sodass für die Daten quasi ein Massenladevorgang erfolgt. Bei Verwendung von PolyBase müssen Sie auf ein Azure Data Lake Storage Gen2- oder Azure Blob Storage-Konto für das Staging der Daten verweisen.

Mit Ausnahme von PolyBase gelten für Azure Synapse Analytics die gleichen bewährten Methoden wie für Azure SQL-Datenbank.

### <a name="file-based-sinks"></a>Dateibasierte Senken 

Für Datenflüsse werden zwar verschiedene Dateitypen unterstützt, aber für Azure Data Factory wird empfohlen, zur Erzielung von optimalen Lese- und Schreibdauern das native Spark-Format „Parquet“ zu verwenden.

Wenn die Daten gleichmäßig verteilt sind, ist **Aktuelle Partitionierung verwenden** die schnellste Partitionierungsoption für das Schreiben von Dateien.

#### <a name="file-name-options"></a>Dateinamenoptionen

Beim Schreiben von Dateien können Sie zwischen verschiedenen Benennungsoptionen wählen, die sich jeweils auf die Leistung auswirken.

![Senkenoptionen](media/data-flow/file-sink-settings.png "Senkenoptionen")

Wenn Sie die Option **Standard** auswählen, ist der Schreibvorgang am schnellsten. Jede Partition entspricht einer Datei mit dem Standardnamen von Spark. Dies ist hilfreich, wenn Sie nur aus dem Ordner mit den Daten lesen.

Wenn Sie ein **Muster** für die Benennung auswählen, wird jede Partitionsdatei mit einem benutzerfreundlicheren Namen benannt. Dieser Vorgang erfolgt nach dem Schreibvorgang und ist etwas langsamer als bei der Option „Standard“. Es gibt auch eine Option, bei der Sie jede einzelne Partition manuell benennen können.

Falls eine Spalte bereits die gewünschte Form für die Ausgabe der Daten aufweist, können Sie die Option **Wie Daten in Spalte** auswählen. Die Daten werden dann neu angeordnet und können die Leistung beeinträchtigen, falls die Spalten nicht gleichmäßig verteilt sind.

Bei **Ausgabe in eine einzelne Datei** werden alle Daten in einer Partition kombiniert. Dies führt besonders für große Datasets zu langen Schreibzeiten. Die dringende Empfehlung des Azure Data Factory-Teams lautet, diese Option **nicht** auszuwählen. Ihre Verwendung wir nur empfohlen, wenn es einen triftigen geschäftlichen Grund gibt.

### <a name="cosmosdb-sinks"></a>Cosmos DB-Senken

Beim Schreiben in Cosmos DB kann die Leistung verbessert werden, indem der Durchsatz und die Batchgröße während der Ausführung des Datenflusses geändert werden. Diese Änderungen sind nur während der Datenfluss-Aktivitätsausführung wirksam und werden nach Abschluss des Vorgangs auf die ursprünglichen Sammlungseinstellungen zurückgesetzt. 

**Batchgröße:** Berechnen Sie die ungefähre Zeilengröße der Daten, und stellen Sie sicher, dass die Multiplikation von Zeilengröße und Batchgröße einen Wert von weniger als 2 Millionen ergibt. Erhöhen Sie andernfalls die Batchgröße, um einen besseren Durchsatz zu erzielen.

**Durchsatz:** Legen Sie hier einen höheren Durchsatz fest, damit Dokumente schneller in Cosmos DB geschrieben werden können. Beachten Sie die höheren RU-Kosten bei einer höheren Durchsatzeinstellung.

**Write Throughput Budget** (Budget für Schreibdurchsatz): Verwenden Sie einen Wert, der kleiner als die Gesamtanzahl der RUs pro Minute ist. Wenn Ihr Datenfluss eine hohe Anzahl von Spark-Partitionen enthält, können Sie durch das Festlegen eines Durchsatzbudgets eine bessere Balance zwischen diesen Partitionen erzielen.

## <a name="optimizing-transformations"></a>Optimieren von Transformationen

### <a name="optimizing-joins-exists-and-lookups"></a>Optimieren von Joins, Exists-Transformationen und Suchvorgängen

#### <a name="broadcasting"></a>Übertragung

Wenn bei Joins, Suchvorgängen und Exists-Transformationen der Arbeitsspeicher des Workerknotens groß genug für einen oder beide Datenströme ist, können Sie die Leistung optimieren, indem Sie die **Übertragung** aktivieren. Bei einer Übertragung senden Sie kleine Datenrahmen an alle Knoten im Cluster. Dies ermöglicht für die Spark-Engine die Durchführung eines Joinvorgangs, ohne dass die Daten im großen Datenstrom neu angeordnet werden. Standardmäßig entscheidet die Spark-Engine automatisch, ob eine Seite eines Joins übertragen werden soll. Wenn Sie mit Ihren eingehenden Daten vertraut sind und wissen, dass ein Datenstrom erheblich kleiner als der andere ist, können Sie für die Übertragung die Option **Feststehend** auswählen. Bei der feststehenden Übertragung wird Spark gezwungen, den ausgewählten Datenstrom zu übertragen. 

Wenn die Größe der übertragenen Daten für den Spark-Knoten zu hoch ist, tritt ggf. ein Fehler vom Typ „Nicht genügend Arbeitsspeicher“ auf. Verwenden Sie Cluster vom Typ **Arbeitsspeicheroptimiert**, um Fehler vom Typ „Nicht genügend Arbeitsspeicher“ zu vermeiden. Wenn bei den Datenflussausführungen Übertragungstimeouts auftreten, können Sie die Broadcastoptimierung deaktivieren. Dies führt jedoch zu Datenflüssen mit geringerer Leistung.

![Optimieren der Join-Transformation](media/data-flow/joinoptimize.png "Join-Optimierung")

#### <a name="cross-joins"></a>Kreuzprodukte

Wenn Sie Literalwerte in Ihren Verknüpfungsbedingungen verwenden oder auf beiden Seiten eines Joins über mehrere Übereinstimmungen verfügen, führt Spark den Joinvorgang als „Kreuzprodukt“ aus. Ein Kreuzprodukt ist ein vollständiges kartesisches Produkt, mit dem dann die verknüpften Werte herausgefiltert werden. Dieser Vorgang ist deutlich langsamer als andere Jointypen. Stellen Sie sicher, dass Sie auf beiden Seiten Ihrer Verknüpfungsbedingungen über Spaltenverweise verfügen, um eine Beeinträchtigung der Leistung zu vermeiden.

#### <a name="sorting-before-joins"></a>Sortieren vor Joinvorgängen

Im Gegensatz zu „Merge Join“ in Tools wie SSIS ist die Join-Transformation kein obligatorischer Vorgang vom Typ „Merge Join“. Für die Joinschlüssel ist vor der Transformation kein Sortiervorgang erforderlich. Das Azure Data Factory-Team rät von der Verwendung von Transformationen für die Sortierung in Zuordnungsdatenflüssen ab.

### <a name="window-transformation-performance"></a>Leistung der Fenstertransformation

Die [Fenstertransformation](data-flow-window.md) partitioniert die Daten nach Wert in Spalten, die Sie als Teil der ```over()```-Klausel in den Transformationseinstellungen auswählen. Es gibt eine Reihe sehr gängiger Aggregat- und Analysefunktionen, die in der Fenstertransformation bereitgestellt werden. Wenn Ihr Anwendungsfall aber darin besteht, ein Fenster für das gesamte Dataset für die Rangfolge ```rank()``` oder die Zeilennummer ```rowNumber()``` zu generieren, empfiehlt es sich, stattdessen die [Rangtransformation](data-flow-rank.md) und die [Transformation für Ersatzschlüssel](data-flow-surrogate-key.md) zu verwenden. Die Transformation bietet bei Verwendung dieser Funktionen eine bessere Leistung bei Vorgängen für das gesamte Dataset.

### <a name="repartitioning-skewed-data"></a>Erneutes Partitionieren bei Datenschiefe

Bei bestimmten Transformationen, z. B. Joins und Aggregatvorgängen, werden Ihre Datenpartitionen neu angeordnet und können gelegentlich zu Datenschiefe führen. Datenschiefe bedeutet, dass Daten nicht gleichmäßig auf die Partitionen verteilt werden. Bei hoher Datenschiefe kann es zu einer Verlangsamung von nachgeschalteten Transformationen und Senkenschreibvorgängen kommen. Sie können die Datenschiefe bei Ihnen während einer Datenflussausführung jederzeit überprüfen, indem Sie in der Überwachungsanzeige auf die Transformation klicken.

![Schiefe und Wölbung](media/data-flow/skewness-kurtosis.png "Schiefe und Wölbung")

In der Überwachungsanzeige wird angegeben, wie die Daten auf die einzelnen Partitionen verteilt sind, und die beiden Metriken für „Schiefe“ und „Wölbung“ werden angezeigt. Die **Schiefe** ist ein Maß dafür, wie asymmetrisch die Daten sind. Der Wert kann „Positiv“, „Null“, „Negativ“ oder „Nicht definiert“ lauten. Bei einer negativen Datenschiefe ist der linke Ausläufer länger als der rechte Ausläufer. Die **Wölbung** ist das Maß dafür, ob die Daten eine Verteilung mit schweren oder leichten Rändern aufweisen. Hohe Wölbungswerte sind nicht wünschenswert. Ideale Bereiche für die Datenschiefe liegen zwischen -3 und 3, und bei der Wölbung werden Werte angestrebt, die unter 10 liegen. Eine einfache Möglichkeit zur Interpretation dieser Zahlen besteht darin, im Partitionsdiagramm zu überprüfen, ob ein Balken deutlich größer als die anderen ist.

Falls Ihre Daten nach einer Transformation nicht gleichmäßig partitioniert sind, können Sie die [Registerkarte „Optimieren“](#optimize-tab) verwenden, um sie neu zu partitionieren. Die Neuanordnung von Daten nimmt Zeit in Anspruch und führt ggf. nicht zu einer Verbesserung der Datenflussleistung.

> [!TIP]
> Wenn Sie Ihre Daten neu partitionieren, aber die Daten bei Ihnen von nachgeschalteten Transformationen dann neu angeordnet werden, sollten Sie die Hashpartitionierung für eine Spalte verwenden, die als Joinschlüssel genutzt wird.

## <a name="using-data-flows-in-pipelines"></a>Verwenden von Datenflüssen in Pipelines 

Wenn Sie komplexe Pipelines mit mehreren Datenflüssen erstellen, kann der logische Datenfluss große Auswirkungen auf Zeit und Kosten haben. In diesem Abschnitt werden die Auswirkungen verschiedener Architekturstrategien behandelt.

### <a name="executing-data-flows-in-parallel"></a>Paralleles Ausführen von Datenflüssen

Wenn Sie mehrere Datenflüsse parallel ausführen, richtet ADF separate Spark-Cluster für jede Aktivität ein. Dadurch kann jeder Auftrag isoliert und parallel ausgeführt werden, führt aber dazu, dass mehrere Cluster gleichzeitig ausgeführt werden.

Bei paralleler Ausführung Ihrer Datenflüsse wird empfohlen, die Eigenschaft für die Gültigkeitsdauer der Azure IR nicht zu aktivieren, da diese zu mehreren nicht verwendeten aktiven Pools führt.

> [!TIP]
> Anstatt denselben Datenfluss mehrmals für jede Aktivität auszuführen, können Sie die Daten in einem Data Lake bereitstellen und Platzhalterpfade verwenden, um die Daten in einem einzelnen Datenfluss zu verarbeiten.

### <a name="execute-data-flows-sequentially"></a>Sequenzielles Ausführen von Datenflüssen

Wenn Sie Ihre Datenflussaktivitäten nacheinander ausführen, empfiehlt es sich, in der Azure IR-Konfiguration eine Gültigkeitsdauer festzulegen. Die Computeressourcen werden von ADF wiederverwendet, was zu einer schnelleren Startzeit des Clusters führt. Jede Aktivität wird weiterhin isoliert und erhält einen neuen Spark-Kontext für jede Ausführung.

Die sequenzielle Ausführung von Aufträgen dauert insgesamt wahrscheinlich am längsten, bietet jedoch eine saubere Trennung logischer Vorgänge.

### <a name="overloading-a-single-data-flow"></a>Überladen eines einzelnen Datenflusses

Wenn Sie die gesamte Logik innerhalb eines einzelnen Datenflusses anordnen, führt ADF den gesamten Auftrag auf einer einzelnen Spark-Instanz aus. Dies mag zwar als eine Möglichkeit zur Kostenreduzierung erscheinen, doch werden unterschiedliche logische Datenflüsse kombiniert und das Überwachen und Debuggen kann schwierig sein. Wenn eine Komponente ausfällt, können auch alle anderen Teile des Auftrags nicht ausgeführt werden. Das Azure Data Factory-Team empfiehlt, Datenflüsse anhand unabhängiger Flüsse der Geschäftslogik zu organisieren. Wenn der Datenfluss zu groß wird, werden Überwachung und Debugging durch Aufteilen in separate Komponenten erleichtert. Es gibt zwar kein festes Limit für die Anzahl von Transformationen in einem Datenfluss, doch wird der Auftrag bei zu vielen Transformationen sehr komplex.

## <a name="next-steps"></a>Nächste Schritte

Lesen Sie die folgenden Artikel zu Datenflüssen in Bezug auf die Leistung:

- [Datenflussaktivität](control-flow-execute-data-flow-activity.md)
- [Überwachen der Datenflussleistung](concepts-data-flow-monitoring.md)
