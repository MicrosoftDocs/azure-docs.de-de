---
title: Zuordnen von Datenflüssen
description: Übersicht über Mapping Data Flows in Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: references_regions
ms.date: 09/02/2020
ms.openlocfilehash: 32f1602c91d17ef2ad3283d4bc03d00d432641aa
ms.sourcegitcommit: daab0491bbc05c43035a3693a96a451845ff193b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/29/2020
ms.locfileid: "93027600"
---
# <a name="mapping-data-flows-in-azure-data-factory"></a>Zuordnungsdatenflüsse in Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

## <a name="what-are-mapping-data-flows"></a>Was sind Zuordnungsdatenflüsse?

Mapping Data Flows (Zuordnungsdatenflüsse) sind visuell entworfene Datentransformationen in Azure Data Factory. Mit Datenflüssen können Data Engineers eine Datentransformationslogik entwickeln, ohne Code schreiben zu müssen. Die daraus resultierenden Datenflüsse werden als Aktivitäten in Azure Data Factory-Pipelines ausgeführt, für die erweiterte Apache Spark-Cluster verwendet werden. Datenflussaktivitäten können mithilfe vorhandener Planungs-, Steuerungs-, Fluss- und Überwachungsfunktionen in Data Factory operationalisiert werden.

Zuordnungsdatenflüsse bieten eine vollständig visuelle Darstellung, ohne Code geschrieben werden muss. Ihre Datenflüsse werden in ADF-verwalteten Ausführungsclustern für erweiterte Datenverarbeitung ausgeführt. Azure Data Factory übernimmt die gesamte Codeübersetzung, Pfadoptimierung und Ausführung Ihrer Datenflussaufträge.

## <a name="getting-started"></a>Erste Schritte

Datenflüsse werden über den Bereich mit Factory-Ressourcen wie Pipelines und Datasets erstellt. Wählen Sie zum Erstellen eines Datenflusses das Pluszeichen neben **Factory-Ressourcen** und dann die Option **Datenfluss** aus. 

![Neuer Datenfluss](media/data-flow/new-data-flow.png "neuer Datenfluss")

Mit dieser Aktion gelangen Sie zur Datenflusscanvas, auf der Sie Ihre Transformationslogik erstellen können. Wählen Sie **Quelle hinzufügen** aus, um mit der Konfiguration Ihrer Quelltransformation zu beginnen. Weitere Informationen finden Sie im Artikel zur [Quelltransformation](data-flow-source.md).

## <a name="authoring-data-flows"></a>Erstellen von Datenflüssen

Ein Zuordnungsdatenfluss verfügt über einen einzigartigen Erstellungsbereich für das vereinfachte Erstellen von Transformationslogik. Die Datenflusscanvas ist in drei Bereiche unterteilt: die obere Leiste, das Diagramm und den Konfigurationsbereich. 

![Screenshot: Datenflusscanvas mit Beschriftungen für obere Leiste, Graph und Konfigurationsbereich](media/data-flow/canvas1.png "Canvas")

### <a name="graph"></a>Graph

Das Diagramm zeigt den Transformationsdatenstrom. Es zeigt die Herkunft der Quelldaten beim Fließen in eine oder mehrere Senken. Wählen Sie die Option **Quelle hinzufügen** aus, um eine neue Quelle hinzuzufügen. Wählen Sie zum Hinzufügen einer neuen Transformation unten rechts in einer vorhandenen Transformation das Pluszeichen aus. Informieren Sie sich über das [Verwalten des Datenflussdiagramms](concepts-data-flow-manage-graph.md).

![Screenshot: Graphbereich der Canvas mit einem Suchtextfeld](media/data-flow/canvas2.png "Canvas")

### <a name="configuration-panel"></a>Konfigurationsbereich

Im Konfigurationsbereich werden die spezifischen Einstellungen für die derzeit ausgewählte Transformation angezeigt. Wenn keine Transformation ausgewählt ist, wird der Datenfluss angezeigt. In der allgemeinen Datenflusskonfiguration können Sie den Namen und die Beschreibung auf der Registerkarte **Allgemein** bearbeiten oder Parameter über die Registerkarte **Parameter** hinzufügen. Weitere Informationen finden Sie unter [Mapping Data Flow-Parameter](parameters-data-flow.md).

Jede Transformation enthält mindestens vier Registerkarten für die Konfiguration.

#### <a name="transformation-settings"></a>Transformationseinstellungen

Die erste Registerkarte im Konfigurationsbereich jeder Transformation enthält die Einstellungen, die für diese Transformation spezifisch sind. Weitere Informationen finden Sie auf der Dokumentationsseite für diese Transformation.

![Registerkarte „Quelleinstellungen“](media/data-flow/source1.png "Registerkarte „Quelleinstellungen“")

#### <a name="optimize"></a>Optimieren

Die Registerkarte **Optimieren** enthält Einstellungen zum Konfigurieren von Partitionierungsschemas. Weitere Informationen zum Optimieren Ihrer Datenflüsse finden Sie in der [Anleitung zur Leistung des Zuordnungsdatenflusses](concepts-data-flow-performance.md).

![Screenshot der Registerkarte „Optimieren“ mit der Option „Partition“, dem Partitionstyp und der Anzahl von Partitionen](media/data-flow/optimize.png)

#### <a name="inspect"></a>Überprüfen

Die Registerkarte **Überprüfen** bietet einen Einblick in die Metadaten des Datenstroms, den Sie transformieren. Sie können die Spaltenanzahl, geänderte Spalten, hinzugefügte Spalten, Datentypen, die Spaltensortierung und Spaltenverweise sehen. **Überprüfen** ist eine schreibgeschützte Ansicht Ihrer Metadaten. Der Debugmodus muss nicht aktiviert sein, um die Metadaten im Bereich **Überprüfen** anzeigen zu können.

![Überprüfen](media/data-flow/inspect1.png "Überprüfen")

Wenn Sie die Form Ihrer Daten durch Transformationen ändern, wird der Fluss der Metadatenänderungen im Bereich **Überprüfen** angezeigt. Falls in Ihrer Quelltransformation kein definiertes Schema vorhanden ist, werden im Bereich **Überprüfen** keine Metadaten angezeigt. Fehlende Metadaten kommen in Schemaabweichungsszenarien häufiger vor.

#### <a name="data-preview"></a>Datenvorschau

Bei aktiviertem Debugmodus können Sie auf der Registerkarte **Datenvorschau** eine interaktive Momentaufnahme der Daten bei jeder Transformation anzeigen. Weitere Informationen finden Sie unter [Datenvorschau im Debugmodus](concepts-data-flow-debug-mode.md#data-preview).

### <a name="top-bar"></a>Obere Leiste

Die obere Leiste enthält Aktionen, die sich auf den gesamten Datenfluss auswirken, z. B. das Speichern und Überprüfen. Sie können auch den zugrunde liegenden JSON-Code und das Datenflussskript Ihrer Transformationslogik anzeigen. Weitere Informationen finden Sie unter [Datenflussskript](data-flow-script.md).

## <a name="available-transformations"></a>Verfügbare Transformationen

Unter [Zuordnungsdatenfluss – Übersicht über Transformationen](data-flow-transformation-overview.md) finden Sie eine Liste der verfügbaren Transformationen.

## <a name="data-flow-activity"></a>Datenflussaktivität

Zuordnungsdatenflüsse werden innerhalb von ADF-Pipelines mithilfe der [Datenflussaktivität](control-flow-execute-data-flow-activity.md) operationalisiert. Der Benutzer muss lediglich angeben, welche Integration Runtime verwendet werden soll, und Parameterwerte übergeben. Weitere Informationen finden Sie unter [Azure Integration Runtime](concepts-integration-runtime.md#azure-integration-runtime).

## <a name="debug-mode"></a>Debugmodus

Im Debugmodus können Sie die Ergebnisse jedes Transformationsschritts interaktiv anzeigen, während Sie Datenflüsse erstellen und debuggen. Die Debugsitzung kann sowohl beim Erstellen der Datenflusslogik als auch beim Ausführen von Debugläufen für die Pipeline mit Datenflussaktivitäten ausgeführt werden. Weitere Informationen finden Sie in der [Dokumentation zum Debugmodus](concepts-data-flow-debug-mode.md).

## <a name="monitoring-data-flows"></a>Überwachen von Datenflüssen

Der Zuordnungsdatenfluss ist in vorhandene Azure Data Factory-Überwachungsfunktionen integriert. Informationen zum Verständnis der Ausgabe der Datenflussüberwachung finden Sie unter [Überwachen von Zuordnungsdatenflüssen](concepts-data-flow-monitoring.md).

Das Azure Data Factory-Team hat eine [Anleitung zur Leistungsoptimierung](concepts-data-flow-performance.md) erstellt, mit deren Hilfe Sie die Ausführungszeit Ihrer Datenflüsse nach dem Erstellen der Geschäftslogik optimieren können.

## <a name="available-regions"></a>Verfügbare Regionen

Zuordnungsdatenflüsse sind in den folgenden Regionen verfügbar:

| Azure-Region | Datenflüsse in ADF | Datenflüsse in Synapse Studio |
| ------------ | ----------------- | ---------------------------- |
|  Australien, Mitte | | |  
| Australien, Mitte 2 | | |
| Australien (Osten) | ✓ |  ✓ |
| Australien, Südosten   | ✓ | ✓ |
| Brasilien Süd  | ✓ |  |
| Kanada, Mitte | ✓ |  |
| Indien, Mitte | ✓ |   ✓ |
| USA (Mitte)    | ✓ |   ✓ |
| China, Osten |      | ✓ |
| China, Osten 2  |   |    |
| China, landesweit | | |
| China, Norden |     | |
| China, Norden 2 | |  |
| Asien, Osten | ✓ | |
| East US   | ✓ | ✓ |
| USA (Ost) 2 | ✓ | ✓ |
| Frankreich, Mitte | ✓ | ✓ |
| Frankreich, Süden  | | |
| Deutschland, Mitte (Sovereign) | | |
| Deutschland, landesweit (Sovereign) | | |
| Deutschland, Norden (Öffentlich) | | |
| Deutschland, Nordosten (Sovereign) | | |
| Deutschland, Westen-Mitte (Öffentlich) |  | ✓ |
| Japan, Osten | ✓ |  |
| Japan, Westen |  | |
| Korea, Mitte | ✓ |  |
| Korea, Süden | | |
| USA Nord Mitte  | ✓ | ✓ |
| Nordeuropa  | ✓ |    |
| Norwegen, Osten | | |
| Norwegen, Westen | | |
| Südafrika, Norden    | ✓ | |
| Südafrika, Westen |  |    |
| USA Süd Mitte  | | ✓ |
| Indien (Süden) | | |
| Asien, Südosten    | ✓ | ✓ |
| Schweiz, Norden |   |  |
| Schweiz, Westen | | |
| VAE, Mitte | | |
| Vereinigte Arabische Emirate, Norden |  |    |
| UK, Süden  | ✓ |   | ✓ |
| UK, Westen |     | ✓ |
| US DoD, Mitte | |  |
| US DoD, Osten | |  |
| US Gov Arizona |      |  |
| US Gov, landesweit | |  |
| US Gov Texas | |  |
| US Government, Virginia |     |  |
| USA, Westen-Mitte |     | ✓ |
| Europa, Westen   | ✓ |   ✓ |
| Indien, Westen | | |
| USA (Westen)   | ✓ |   |
| USA, Westen 2 | ✓ |   ✓ | 

## <a name="next-steps"></a>Nächste Schritte

* Informieren Sie sich über die Erstellung einer [Quelltransformation](data-flow-source.md).
* Informieren Sie sich darüber, wie Sie Ihre Datenflüsse im [Debugmodus](concepts-data-flow-debug-mode.md) erstellen.
