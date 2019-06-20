---
title: Indizieren eines großen Datasets mithilfe integrierter Indexer – Azure Search
description: Lernen Sie Strategien für die Indizierung umfangreicher Daten oder rechenintensive Indizierung über den Batchmodus, Ressourcenerstellung und Techniken für die geplante, parallele und verteilte Indizierung.
services: search
author: HeidiSteen
manager: cgronlun
ms.service: search
ms.topic: conceptual
ms.date: 12/19/2018
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 8923c94409dcf079179ed0464046e39ef7654c4c
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65949830"
---
# <a name="how-to-index-large-data-sets-in-azure-search"></a>Indizieren großer Datasets in Azure Search

Wenn das Datenvolumen zunimmt oder die Verarbeitung geändert werden muss, stellen Sie möglicherweise fest, dass standardmäßige Indizierungsstrategien nicht ausreichend sind. Für Azure Search gibt es verschiedene Ansätze für die Aufnahme von größeren Datasets, angefangen bei der Art, wie Sie eine Uploadanforderung strukturieren, bis zur Verwendung eines quellenspezifischen Indexers für geplante und verteilte Workloads.

Die Techniken für große Datenmengen gelten auch für lang andauernde Prozesse. Insbesondere die unter [Parallelindizierung](#parallel-indexing) beschriebenen Schritte sind hilfreich für die Ausführung rechenintensiver Indizierung, z.B. Bildanalyse oder Verarbeitung natürlicher Sprache in [kognitiven Suchpipelines](cognitive-search-concept-intro.md).

## <a name="batch-indexing"></a>Batchindizierung

Eines der einfachsten Verfahren für die Indizierung eines größeren Datasets ist das Senden mehrerer Dokumente oder Datensätze in einer einzelnen Anforderung. Solange die gesamte Nutzlast kleiner als 16MB ist, kann eine Anforderung bis zu 1.000 Dokumente in einem Uploadmassenvorgang verarbeiten. Das [Add or Update Documents-REST-API](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) vorausgesetzt, würden Sie 1.000 Dokumente in den Hauptteil der Anforderung packen.

Die Batchindizierung wird für einzelne Anforderungen mit REST oder .NET oder über Indexer implementiert. Ein paar Indexer werden unter verschiedenen Einschränkungen eingesetzt. Insbesondere wird bei der Azure-Blobindizierung eine Batchgröße von 10 Dokumenten hinsichtlich der größeren durchschnittlichen Dokumentgröße festgelegt. Für Indexer basierend auf der [Create Indexer-REST-API](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer ) können Sie das `BatchSize`-Argument zum Anpassen dieser Einstellung festlegen, damit die Eigenschaften besser Ihren Daten entsprechen. 

> [!NOTE]
> Um die Dokumentgröße niedrig zu halten, achten Sie darauf, nicht abfragbare Daten von der Anforderung auszuschließen. Bilder und andere binäre Daten können nicht direkt durchsucht werden und sollten nicht im Index gespeichert werden. Um nicht abfragbare Daten in Suchergebnisse zu integrieren, sollten Sie ein nicht durchsuchbares Feld definieren, in dem ein URL-Verweis auf die Ressource gespeichert wird.

## <a name="add-resources"></a>Hinzufügen von Ressourcen

Bei Diensten, die zu einem der [Standardtarife](search-sku-tier.md) bereitgestellt werden, ist die Kapazität für Speicherung und Workloads (Abfragen oder Indizierung) oft nicht ausgelastet, sodass [die Erhöhung der Partitions- und Replikateanzahl](search-capacity-planning.md) eine nahe liegende Lösung für die Unterbringung größerer Datasets ist. Für optimale Ergebnisse benötigen Sie beide Ressourcen: Partitionen zur Speicherung und Replikate für die Datenerfassung.

Replikate und Partitionen sind zunehmend abzurechnende Ereignisse, die Ihre Kosten erhöhen, doch wenn Sie nicht ständig unter maximaler Auslastung indizieren, können Sie Skalierung für die Dauer des Indizierungsprozesses hinzufügen und dann nach Abschluss der Indizierung Ressourcenebenen nach unten anpassen.

## <a name="use-indexers"></a>Verwenden von Indexern

Mit [Indexern](search-indexer-overview.md) werden externe Datenquellen nach durchsuchbaren Inhalten durchforstet. Mehrere Indexerfunktionen sind zwar nicht speziell für die Indizierung in großem Rahmen vorgesehen, jedoch besonders nützlich zur Aufnahme größerer Datasets:

+ Mit Schedulern können Sie die Indizierung in regelmäßige Intervalle gliedern, sodass Sie sie im Laufe der Zeit verteilen können.
+ Geplante Indizierung kann am letzten bekannten Haltepunkt fortgesetzt werden. Wenn eine Datenquelle innerhalb eines 24-Stunden-Zeitfensters nicht vollständig durchsucht wird, setzt der Indexer die Indizierung am nächsten Tag fort, unabhängig davon, wo er aufgehört hat.
+ Partitionieren von Daten in kleinere einzelne Datenquellen ermöglicht die parallele Verarbeitung. Sie können ein großes Dataset in kleinere Datasets aufteilen und dann mehrere Datenquellendefinitionen erstellen, die gleichzeitig indiziert werden können.

> [!NOTE]
> Indexer sind datenquellenspezifisch, also eignet sich ein Indexeransatz nur für ausgewählte Datenquellen in Azure: [SQL-Datenbank](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md), [Blobspeicher](search-howto-indexing-azure-blob-storage.md), [Tabellenspeicher](search-howto-indexing-azure-tables.md), [Cosmos DB](search-howto-index-cosmosdb.md).

## <a name="scheduled-indexing"></a>Geplante Indizierung

Indexerzeitpläne sind ein wichtiger Mechanismus zum Verarbeiten von großen Datasets und für lang andauernde Prozesse wie Bildanalyse in einer Pipeline für die kognitive Suche. Die Indexerverarbeitung wird innerhalb von 24 Stunden ausgeführt. Wenn die Verarbeitung nicht innerhalb von 24 Stunden abgeschlossen ist, können Sie die Verhaltensweisen für die Indexerzeitplanung zu Ihrem Vorteil nutzen. 

Standardmäßig startet die geplante Indizierung zu bestimmten Zeitintervallen. Dabei wird ein Vorgang in der Regel abgeschlossen, bevor die Indizierung zum nächsten geplanten Intervall fortgesetzt wird. Wenn der Vorgang jedoch nicht innerhalb des Intervalls abgeschlossen wird, wird der Indexer beendet, da ein Timeout auftritt. Beim nächsten Intervall wird die Verarbeitung an der Stelle fortgesetzt, an der sie während des letzten Intervalls unterbrochen wurde. Dabei verfolgt das System nach, an welcher Stelle dies der Fall ist. 

Das heißt in der Praxis, dass Sie für Indexladungen, die sich über mehrere Tage erstrecken, einen 24-Stunden-Zeitplan für den Indexer festlegen können. Wenn eine Indizierung für den nächsten 24-Stunden-Zyklus fortgesetzt wird, startet diese bei dem letzten erfolgreich verarbeiteten Dokument neu. Auf diese Weise kann sich ein Indexer über mehrere Tage hinweg durch das Dokumentbacklog durcharbeiten, bis alle nicht verarbeitete Dokumente verarbeitet wurden. Weitere Informationen zu diesem Ansatz finden Sie unter [Indizieren großer Datasets](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets). Weitere Informationen zum Festlegen der Zeitpläne im Allgemeinen finden Sie unter [Anforderungssyntax](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer#request-syntax).

<a name="parallel-indexing"></a>

## <a name="parallel-indexing"></a>Parallele Indizierung

Eine parallele Indizierungsstrategie basiert auf der gemeinsamen Indizierung mehrerer Datenquellen, wobei jede Datenquellendefinition eine Teilmenge der Daten angibt. 

Für nicht routinemäßige, rechenintensive Indizierungsanforderungen – wie z.B. OCR bei gescannten Dokumenten in einer kognitiven Suchpipeline, bei der Bildanalyse oder Verarbeitung natürlicher Sprache – ist eine parallele Indizierungsstrategie häufig der richtige Ansatz für den Abschluss eines lang andauernden Prozesses in kürzestmöglicher Zeit. Wenn Sie Abfrageanforderungen vermeiden oder einschränken können, ist die parallele Indizierung für einen Dienst, der simultan keine Anforderungen verarbeitet, Ihre beste Strategie für das Durcharbeiten einer großen Inhaltsmenge, die langsam verarbeitet wird. 

Parallele Verarbeitung verfügt über die folgenden Elemente:

+ Verteilen Sie Ihre Quelldaten auf mehrere Container oder mehrere virtuelle Ordner innerhalb desselben Containers. 
+ Ordnen Sie jedes noch so kleine Dataset einer eigenen [Datenquelle](https://docs.microsoft.com/rest/api/searchservice/create-data-source) zu, die an einen eignen [Indexer](https://docs.microsoft.com/rest/api/searchservice/create-indexer) gekoppelt ist.
+ Verweisen Sie für die kognitive Suche in jeder Indexerdefinition auf dieselbe [Fähigkeitsgruppe](https://docs.microsoft.com/rest/api/searchservice/create-skillset).
+ Schreiben Sie in denselben Suchindex für das Ziel. 
+ Legen Sie für die Ausführung aller Indexer denselben Zeitpunkt fest.

> [!NOTE]
> Azure Search unterstützt das Dedizieren von Replikaten oder Partitionen auf bestimmte Workloads nicht. Es besteht ein erhöhtes Risiko, dass die gleichzeitige Indizierung Ihr System so belastet, dass die Abfrageleistung beeinträchtigt wird. Wenn Sie über eine Testumgebung verfügen, implementieren Sie die parallele Indizierung dort zuerst, um die Vor- und Nachteile nachvollziehen zu können.

### <a name="how-to-configure-parallel-indexing"></a>Konfigurieren der parallelen Indizierung

Für Indexer basiert die Verarbeitung der Kapazität grob auf einem Indexersubsystem für jede Diensteinheit, die von Ihrem Suchdienst verwendet wird. Für Azure Search-Dienste, die für die Tarife Basic und Standard verfügbar sind, sind mehrere gleichzeitige Indexer möglich, die mindestens zwei Replikate aufweisen. 

1. Prüfen Sie im [Azure-Portal](https://portal.azure.com) auf der Dashboardseite **Übersicht** des Search-Diensts den **Tarif**, um festzustellen, ob die parallele Indizierung notwendig ist. Sowohl der Basic- als auch der Standard-Tarif umfassen mehrere Replikate.

2. Erhöhen Sie die [Anzahl der Replikate](search-capacity-planning.md) unter **Einstellungen** > **Staffelung** um ein zusätzliches Replikat für jede Indexerworkload. Geben Sie eine ausreichende Zahl für bestehende Abfragevolumen an. Es sollten keine Abfrageworkloads für die Indizierung geopfert werden.

3. Teilen Sie Daten auf mehrere Container auf. Tun Sie dies auf einer Ebene, die von den Azure Search-Indexern erreicht werden kann. Also z.B. auf mehrere Tabellen in Azure SQL-Datenbank, auf mehrere Container im Azure Blob-Speicher oder auf mehrere Sammlungen. Definieren Sie ein Datenquellobjekt pro Tabelle oder Container.

4. Erstellen Sie mehrere Indexer, und planen Sie eine parallele Ausführung:

   + Gehen Sie von einem Dienst mit sechs Replikaten aus. Konfigurieren Sie sechs Indizes, wobei jeder einer Datenquelle zugeordnet ist, die ein Sechstel des Datasets enthält, sodass Sie das gesamte Dataset auf sechs Teile aufteilen können. 

   + Richten Sie alle Indexer auf einen Index aus. Richten Sie für kognitive Suchworkloads alle Indexer auf eine Fähigkeitsgruppe aus.

   + Legen Sie innerhalb sämtlicher Indexerdefinitionen denselben Zeitplan für das Ausführungsmuster für die Runtime fest. Beispielsweise erstellt `"schedule" : { "interval" : "PT8H", "startTime" : "2018-05-15T00:00:00Z" }` für den 15.5.2018 einen Zeitplan für alle Indexer, die in Intervallen von acht Stunden ausgeführt werden.

Zum festgelegten Zeitpunkt beginnen sämtliche Indexer mit der Ausführung, dem Laden von Daten, der Anwendung von Anreicherungen (wenn Sie eine Pipeline für die kognitive Suche konfiguriert haben) und dem Schreiben in den Index. Azure Search sperrt den Index für Updates nicht. Gleichzeitige Schreibvorgänge werden verwaltet und wiederholt, wenn ein bestimmter Schreibvorgang beim ersten Mal nicht erfolgreich ausgeführt werden kann.

> [!Note]
> Wenn Sie die Anzahl der Replikate erhöhen, sollten Sie auch die Anzahl der Partitionen erhöhen, wenn zu erwarten ist, dass der Index deutlich vergrößert wird. Partitionen speichern einzelne Segmente des indizierten Inhalts. Je mehr Partitionen Sie besitzen, desto kleiner sind die einzelnen Segmente, die darin gespeichert werden müssen.

## <a name="see-also"></a>Weitere Informationen

+ [Indexer (Übersicht)](search-indexer-overview.md)
+ [Indizieren im Portal](search-import-data-portal.md)
+ [Azure SQL-Datenbank-Indexer](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Azure Cosmos DB-Indexer](search-howto-index-cosmosdb.md)
+ [Azure Blob Storage-Indexer](search-howto-indexing-azure-blob-storage.md)
+ [Azure Table Storage-Indexer](search-howto-indexing-azure-tables.md)
+ [Sicherheit in Azure Search](search-security-overview.md)
