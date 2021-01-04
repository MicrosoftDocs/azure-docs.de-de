---
title: Entwerfen von Azure Table Storage für Abfragen | Microsoft-Dokumentation
description: Entwerfen von Tabellen für Abfragen in Azure Table Storage. Wählen Sie einen geeigneten Partitionsschlüssel aus, optimieren Sie Abfragen, und sortieren Sie die Daten für den Tabellenspeicherdienst.
services: storage
author: tamram
ms.author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/23/2018
ms.subservice: tables
ms.openlocfilehash: 43ae21d97bc9d8292270ae62006e649f4bcf540b
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93316146"
---
# <a name="design-for-querying"></a>Entwurf für Abfragen
Anwendungen für einen Tabellenspeicherdienst können intensiv lesen, intensiv schreiben oder beides. Dieser Artikel konzentriert sich auf die Dinge, die Sie beim Entwurf Ihres Tabellenspeicherdienstes beachten sollten, um Lesevorgänge effizient zu unterstützen. In der Regel ist ein Entwurf, der Lesevorgänge effizient unterstützt, auch für Schreibvorgänge effizient. Es gibt jedoch weitere Überlegungen, die beim Entwerfen der Unterstützung von Schreibvorgängen zu beachten sind und im Artikel [Entwurf für die Datenänderung](table-storage-design-for-modification.md) erläutert werden.

Ein guter Ausgangspunkt für den Entwurf Ihrer Lösung für einen Tabellenspeicherdienst mit effizientem Lesen der Daten ist, sich zu fragen: „Welche Abfragen meiner Anwendung müssen ausgeführt werden, um die benötigten Daten abzurufen, die vom Tabellenspeicherdienst benötigt werden?“  

> [!NOTE]
> Für einen Tabellenspeicherdienst ist es wichtig, den Entwurf von Anfang an richtig zu machen, da Änderungen zu einem späteren Zeitpunkt schwierig und teuer sind. Beispielsweise ist es bei einer relationalen Datenbank oft möglich, Leistungsprobleme in Angriff zu nehmen, indem Sie einfach einer vorhandenen Datenbank Indexe hinzufügen. Diese Möglichkeit steht bei einem Tabellenspeicherdienst nicht zur Verfügung.  
> 
> 

Dieser Abschnitt konzentriert sich auf die Hauptprobleme, mit denen Sie sich beim Entwurf der Tabellen für die Abfrage befassen müssen. Die in diesem Abschnitt behandelten Themen umfassen:

* [Auswirkungen der Wahl von PartitionKey und RowKey auf die Abfrageleistung](#how-your-choice-of-partitionkey-and-rowkey-impacts-query-performance)
* [Auswählen eines geeigneten PartitionKey](#choosing-an-appropriate-partitionkey)
* [Optimieren von Abfragen für den Tabellenspeicherdienst](#optimizing-queries-for-the-table-service)
* [Sortieren von Daten im Tabellenspeicherdienst](#sorting-data-in-the-table-service)

## <a name="how-your-choice-of-partitionkey-and-rowkey-impacts-query-performance"></a>Auswirkungen der Wahl von PartitionKey und RowKey auf die Abfrageleistung
In den folgenden Beispielen wird angenommen, dass der Tabellenspeicherdienst Entitäten von Mitarbeitern mit der folgenden Struktur speichert (bei den meisten Beispielen fehlt die Eigenschaft **Zeitstempel** aus Gründen der Übersichtlichkeit):  

| *Spaltenname* | *Datentyp* |
| --- | --- |
| **PartitionKey** (Abteilungsname) |String |
| **RowKey** (Mitarbeiter-ID) |String |
| **Vorname** |String |
| **Nachname** |String |
| **Alter** |Integer |
| **EmailAddress** |String |

Im Artikel [Einführung zu Tabellenspeicher in Azure](table-storage-overview.md) werden einige der wichtigsten Funktionen des Azure-Tabellenspeicherdiensts beschrieben, die direkten Einfluss auf den Entwurf für Abfragen haben. Dadurch ergeben sich die folgenden allgemeinen Richtlinien für den Entwurf von Abfragen für den Tabellenspeicherdienst. Beachten Sie, dass die in den Beispielen unten verwendete Filtersyntax aus dem REST-API-Tabellenspeicherdienst stammt. Weitere Informationen finden Sie unter [Query Entities](/rest/api/storageservices/Query-Entities) (Abfragen von Entitäten).  

* Eine * **Punktabfrage** _ ist die effizienteste Suche und wird bei sehr umfangreichen Suchvorgängen oder für Suchvorgänge empfohlen, die eine sehr niedrige Latenz erfordern. Eine solche Abfrage kann Indizes verwenden, um durch die Angabe der _ *PartitionKey* *- und **RowKey** -Werte eine einzelne Entität sehr effizient zu suchen. Beispiel: $filter=(PartitionKey eq 'Sales') und (RowKey eq '2')  
* Die zweitbeste Lösung ist eine * **Bereichsabfrage** _, die den _ *PartitionKey* * verwendet und einen Bereich von **RowKey** -Werten filtert, um mehrere Entitäten zurückzugeben. Der **PartitionKey** -Wert identifiziert eine bestimmte Partition, die **RowKey** -Werte identifizieren eine Teilmenge der Entitäten in dieser Partition. Beispiel: $filter=PartitionKey eq 'Sales' und RowKey ge 'S' und RowKey lt 'T'  
* Die drittbeste Lösung ist ein * **Partitionsscan** _, der den _ *PartitionKey* * sowie Filter für eine andere schlüsselfremde Eigenschaft verwendet und möglicherweise mehrere Entitäten zurückgibt. Der **PartitionKey** -Wert identifiziert eine bestimmte Partition und die Eigenschaftswerte wählen eine Teilmenge der Entitäten in dieser Partition aus. Beispiel: $filter=PartitionKey eq 'Sales' und LastName eq 'Smith'  
* Ein * **Tabellenscan** _ umfasst keinen _ *PartitionKey* * und ist sehr ineffizient, da er alle Partitionen, aus denen Ihre Tabelle besteht, auf übereinstimmende Entitäten untersucht. Er führt einen Tabellenscan durch, unabhängig davon, ob der Filter **RowKey** verwendet. Beispiel: $filter=LastName eq 'Jones'  
* Abfragen, die mehrere Entitäten zurückgeben, geben diese nach **PartitionKey** und **RowKey** sortiert zurück. Um eine Neusortierung der Entitäten im Client zu vermeiden, müssen Sie einen **RowKey** mit der am häufigsten verwendeten Sortierreihenfolge auswählen.  

Beachten Sie, dass die Verwendung von **or** für die Festlegung eines Filters, der auf **RowKey** -Werten basiert, zu einem Partitionsscan führt und nicht als Bereichsabfrage behandelt wird. Aus diesem Grund sollten Sie Abfragen vermeiden, die z. B. folgende Filter verwenden: $filter=PartitionKey eq 'Sales' und (RowKey eq '121' or RowKey eq '322')  

Beispiele für clientseitigen Code, der die Storage Client Library zur Ausführung effizienter Abfragen verwendet, finden Sie unter:  

* [Ausführen einer Punktabfrage mithilfe der Storage Client Library](table-storage-design-patterns.md#executing-a-point-query-using-the-storage-client-library)
* [Abrufen von mehreren Entitäten mithilfe von LINQ](table-storage-design-patterns.md#retrieving-multiple-entities-using-linq)
* [Serverseitige Projektion](table-storage-design-patterns.md#server-side-projection)  

Beispiele für clientseitigen Code, der mehrere Entitätstypen handhaben kann, die in der gleichen Tabelle gespeichert sind, finden Sie unter:  

* [Arbeiten mit heterogenen Entitätstypen](table-storage-design-patterns.md#working-with-heterogeneous-entity-types)  

## <a name="choosing-an-appropriate-partitionkey"></a>Auswählen eines geeigneten PartitionKey
Bei der Auswahl des **PartitionKey** sollte ein Abgleich zwischen der Notwendigkeit, die Verwendung von EGTs freizugeben (zur Sicherstellung der Konsistenz), und der Anforderung zur Verteilung von Entitäten auf mehrere Partitionen (um eine skalierbare Lösung sicherzustellen) vorgenommen werden.  

Ein Extremfall ist, dass Sie alle Entitäten in einer einzelnen Partition speichern würden. Dies würde aber möglicherweise die Skalierbarkeit Ihrer Lösung begrenzen und verhindern, dass der Tabellenspeicherdienst Anforderungen für den Lastenausgleich vornehmen kann. Im anderen Extremfall könnten Sie eine Entität pro Partition speichern, was höchst skalierbar wäre und es dem Tabellenspeicherdienst ermöglichen würde, einen Lastenausgleich für Anforderungen vorzunehmen, was aber verhindern würde, dass Sie Entitätsgruppentransaktionen verwenden können.  

Ein idealer **PartitionKey** ermöglicht die Verwendung von effizienten Abfragen und verfügt über ausreichende Partitionen, um sicherzustellen, dass Ihre Lösung skalierbar ist. In der Regel werden Sie feststellen, dass Ihre Entitäten über eine geeignete Eigenschaft verfügen, die Ihre Entitäten über ausreichende Partitionen verteilt.

> [!NOTE]
> Beispielsweise kann in einem System, auf dem Informationen über Benutzer und Mitarbeiter gespeichert werden, die Benutzer-ID eine guter PartitionKey sein. Sie haben möglicherweise mehrere Entitäten, für die eine bestimmte Benutzer-ID als Partitionsschlüssel verwendet wird. Jede Entität, die Daten zu einem Benutzer speichert, wird in einer einzigen Partition gruppiert, und somit kann über Entitätsgruppentransaktionen auf diese Entitäten zugegriffen werden, während sie gleichzeitig hoch skalierbar sind.
> 
> 

Bei der Wahl des **PartitionKey** -Werts sind weitere Aspekte zu bedenken, die sich darauf beziehen, wie Sie Entitäten einfügen, aktualisieren und löschen. Weitere Informationen finden Sie unter [Entwurf für Datenänderung](table-storage-design-for-modification.md).  

## <a name="optimizing-queries-for-the-table-service"></a>Optimieren von Abfragen für den Tabellenspeicherdienst
Der Tabellenspeicherdienst indiziert die Entitäten automatisch unter Verwendung von **PartitionKey** - und **RowKey** -Werten in einem einzelnen gruppierten Index. Aus diesem Grund stellen Punktabfragen die effizienteste Lösung dar. Allerdings gibt es mit Ausnahme des gruppierten Index für **PartitionKey** und **RowKey** keine weiteren Indizes.

Viele Entwürfe müssen Anforderungen erfüllen, um die Suche nach Entitäten auf Grundlage mehrerer Kriterien zu ermöglichen. Suchen Sie z. B. Mitarbeiterentitäten auf Grundlage der E-Mail-Adresse, Mitarbeiter-ID oder Nachname. Die in [Entwurfsmuster für die Tabelle](table-storage-design-patterns.md) beschriebenen Muster befassen sich mit diesen Arten von Anforderungen und beschreiben Möglichkeiten zum Umgehen der Tatsache, dass der Tabellenspeicherdienst keine sekundären Indizes zur Verfügung stellt:  

* [Sekundäres Indexmuster für Intra-Partition](table-storage-design-patterns.md#intra-partition-secondary-index-pattern) – Speichern mehrerer Kopien jeder Entität mit unterschiedlichen **RowKey** -Werten (in der gleichen Partition) zur Ermöglichung schneller und effizienter Suchvorgänge und alternativer Sortierreihenfolgen mit unterschiedlichen **RowKey** -Werten.  
* [Sekundäres Indexmuster für Inter-Partition](table-storage-design-patterns.md#inter-partition-secondary-index-pattern) – Speichern mehrerer Kopien der einzelnen Entitäten mithilfe verschiedener **RowKey** -Werte in separaten Partitionen oder in separaten Tabellen zur Aktivierung schneller und effizienter Suchvorgänge und alternativer Sortierreihenfolgen mit unterschiedlichen **RowKey** -Werten.  
* [Indexmuster für Entitäten](table-storage-design-patterns.md#index-entities-pattern) – Verwalten von Indexentitäten, um effiziente Suchvorgänge zu ermöglichen, die Listen mit Entitäten zurückgeben.  

## <a name="sorting-data-in-the-table-service"></a>Sortieren von Daten im Tabellenspeicherdienst
Der Tabellenspeicherdienst gibt Entitäten zurück, die in aufsteigender Reihenfolge nach **PartitionKey** und **RowKey** sortiert sind. Diese Schlüssel sind String-Werte. Um sicherzustellen, dass die numerischen Werte ordnungsgemäß sortieren, sollten sie mit einer festen Länge konvertiert und mit Nullen aufgefüllt werden. Wenn etwa der als **RowKey** verwendete Mitarbeiter-ID-Wert ein ganzzahliger Wert ist, sollten Sie die Mitarbeiter-ID **123** in **00000123** konvertieren.  

Viele Anwendungen verfügen über Anforderungen für die Verwendung von Daten, die in unterschiedlicher Reihenfolge sortiert sind,z. B. bei Sortierung von Mitarbeitern nach Name oder durch das Verknüpfen eines Datums. Die folgenden Muster befassen sich mit der Verwendung alternativer Sortierreihenfolgen für Entitäten:  

* [Sekundäres Indexmuster für Intra-Partition](table-storage-design-patterns.md#intra-partition-secondary-index-pattern) – Speichern mehrerer Kopien jeder Entität mit unterschiedlichen RowKey-Werten (in der gleichen Partition) zur Ermöglichung schneller und effizienter Suchvorgänge und alternativer Sortierreihenfolgen mit unterschiedlichen RowKey-Werten.  
* [Sekundäres Indexmuster für Inter-Partition](table-storage-design-patterns.md#inter-partition-secondary-index-pattern) – Speichern mehrerer Kopien der einzelnen Entitäten mit verschiedenen RowKey-Werten in separaten Partitionen in separaten Tabellen für schnelle und effiziente Suchvorgänge und alternative Sortierreihenfolgen mit verschiedenen RowKey-Werten.
* [Log Tail-Muster](table-storage-design-patterns.md#log-tail-pattern) – Abrufen der *n* Entitäten, die zuletzt einer Partition hinzugefügt wurden, indem Sie einen **RowKey** -Wert verwenden, mit dem nach Datum und Uhrzeit in umgekehrter Reihenfolge sortiert wird.  

## <a name="next-steps"></a>Nächste Schritte

- [Entwurfsmuster für die Tabelle](table-storage-design-patterns.md)
- [Modellieren von Beziehungen](table-storage-design-modeling.md)
- [Verschlüsseln von Tabellendaten](table-storage-design-encrypt-data.md)
- [Entwurf für die Datenänderung](table-storage-design-for-modification.md)