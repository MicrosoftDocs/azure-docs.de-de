---
title: Entwerfen von Azure-Speichertabellen zur Datenänderung | Microsoft-Dokumentation
description: Entwerfen von Tabellen zur Datenänderung in Azure Table Storage.
services: storage
author: MarkMcGeeAtAquent
ms.service: storage
ms.topic: article
ms.date: 04/23/2018
ms.author: sngun
ms.subservice: tables
ms.openlocfilehash: e993d169025f9b76c5e813bae31ca6cb2a39ba71
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60325883"
---
# <a name="design-for-data-modification"></a>Entwurf für die Datenänderung
Dieser Artikel konzentriert sich auf Entwurfsansätze zum Optimieren von Einfügungen, Aktualisierungen und Löschungen. In einigen Fällen müssen Sie einen Kompromiss finden zwischen Entwürfen, die nach Abfragen optimiert sind und Entwürfen, die nach Datenänderung optimiert sind, so wie sie dies auch bei Entwürfen für relationale Datenbanken vornehmen (obwohl die Techniken für die Verwaltung von Entwurfskompromissen für eine relationale Datenbank unterschiedlich sind). Der Abschnitt „Entwurfsmuster für die Tabelle“ beschreibt einige detaillierte Entwurfsmuster für den Tabellenspeicherdienst und stellt einige dieser Kompromisse heraus. In der Praxis werden Sie feststellen, dass viele für Abfragen von Entitäten optimierte Entwürfe sich auch zum Ändern von Entitäten eignen.  

## <a name="optimize-the-performance-of-insert-update-and-delete-operations"></a>Leistungsoptimierung bei Einfüge-, Aktualisierungs- und Löschvorgängen
Um eine Entität zu aktualisieren oder zu löschen, müssen Sie in der Lage sein, diese durch Verwendung der **PartitionKey**- und **RowKey**-Werte zu identifizieren. In diesem Zusammenhang sollte die Wahl von **PartitionKey** und **RowKey** für das Ändern von Entitäten ähnlichen Kriterien wie bei Ihrer Wahl für Punktabfragen folgen, da Sie Entitäten so effizient wie möglich identifizieren möchten. Es ist nicht empfehlenswert, einen ineffizienten Partitions- oder Tabellenscan zu verwenden, um eine Entität zu suchen und so die **PartitionKey**- und **RowKey**-Werte zu ermitteln, die Sie zum Aktualisieren oder Löschen benötigen.  

Die folgenden Muster im Abschnitt „Entwurfsmuster für die Tabelle“ befassen sich mit der Optimierung der Leistung oder den Einfüge-, Update- und Löschvorgängen:  

* [Muster für umfangreiche Löschvorgänge](table-storage-design-patterns.md#high-volume-delete-pattern) – Aktivieren Sie das Löschen einer großen Anzahl von Entitäten, indem Sie alle Entitäten zum gleichzeitigen Löschen in einer eigenen separaten Tabelle speichern. Die Entitäten werden dann durch Löschen der Tabelle gelöscht.  
* [Datenreihenmuster](table-storage-design-patterns.md#data-series-pattern) – Speichern Sie vollständige Datenreihen in einer einzelnen Entität, um die Anzahl der Anforderungen zu minimieren, die Sie vornehmen.  
* [Breite Entitätenmuster](table-storage-design-patterns.md#wide-entities-pattern) – Verwenden Sie mehrere physische Entitäten zum Speichern von logischen Entitäten mit mehr als 252 Eigenschaften.  
* [Muster für große Entitäten](table-storage-design-patterns.md#large-entities-pattern) – Verwenden Sie Blob-Speicher zum Speichern großer Eigenschaftswerte.  

## <a name="ensure-consistency-in-your-stored-entities"></a>Gewährleisten der Konsistenz in den gespeicherten Entitäten
Der andere wichtige Faktor, der Ihrer Schlüsselauswahl für die Optimierung von Datenänderungen beeinflusst ist, wie Konsistenz mit atomischen Transaktionen sichergestellt werden. Sie können nur eine EGT verwenden, um mit Entitäten zu arbeiten, die in derselben Partition gespeichert ist.  

Die folgenden Muster im Artikel [Entwurfsmuster für die Tabelle](table-storage-design-patterns.md) befassen sich mit der Konsistenzverwaltung:  

* [Sekundäres Indexmuster für Intra-Partition](table-storage-design-patterns.md#intra-partition-secondary-index-pattern) – Speichern mehrerer Kopien jeder Entität mit unterschiedlichen **RowKey**-Werten (in der gleichen Partition) zur Ermöglichung schneller und effizienter Suchvorgänge und alternativer Sortierreihenfolgen mit unterschiedlichen **RowKey**-Werten.  
* [Sekundäres Indexmuster für Inter-Partition](table-storage-design-patterns.md#inter-partition-secondary-index-pattern) – Speichern mehrerer Kopien der einzelnen Entitäten mithilfe verschiedener RowKey-Werte in separaten Partitionen oder in separaten Tabellen ermöglichen schnelle und effiziente Suchvorgänge und alternative Sortierreihenfolgen mit anderen **RowKey** -Werten.  
* [Eventual Consistency-Transaktionsmuster](table-storage-design-patterns.md#eventually-consistent-transactions-pattern) – Aktivieren Sie "Eventual Consistency" über Partitions- oder Speichersystemgrenzen hinweg, indem Sie Azure-Warteschlangen verwenden.
* [Indexmuster für Entitäten](table-storage-design-patterns.md#index-entities-pattern) – Verwalten von Indexentitäten, um effiziente Suchvorgänge zu ermöglichen, die Listen mit Entitäten zurückgeben.  
* [Denormalisierungsmuster](table-storage-design-patterns.md#denormalization-pattern) – Fassen Sie verknüpfte Daten in einer einzelnen Entität zusammen, damit Sie alle benötigten Daten mit einer einzelnen Punktabfrage abrufen können.  
* [Datenreihenmuster](table-storage-design-patterns.md#data-series-pattern) – Speichern Sie vollständige Datenreihen in einer einzelnen Entität, um die Anzahl der Anforderungen zu minimieren, die Sie vornehmen.  

Informationen zu Entitätsgruppentransaktionen (EGTs) finden Sie im Abschnitt [Entitätsgruppentransaktionen](table-storage-design.md#entity-group-transactions).  

## <a name="ensure-your-design-for-efficient-modifications-facilitates-efficient-queries"></a>Sicherstellen des Entwurfs für effiziente Änderungen erleichtert das effiziente Abfragen
In vielen Fällen ergibt ein Entwurf für effiziente Abfragen effiziente Änderungen, aber Sie sollten immer bewerten, ob dies auch bei Ihrem speziellen Szenario der Fall ist. Einige der Muster im Artikel [Entwurfsmuster für die Tabelle](table-storage-design-patterns.md) bewerten ausschließlich Kompromisse zwischen Abfragen und der Änderung von Entitäten, und Sie sollten immer die Anzahl der einzelnen Vorgänge berücksichtigen.  

Die folgenden Muster im Artikel [Entwurfsmuster für die Tabelle](table-storage-design-patterns.md) befassen sich mit den Kompromissen zwischen einem Entwurf für effiziente Abfragen und einem Entwurf für effiziente Datenänderung:  

* [Zusammengesetzte Schlüsselmuster](table-storage-design-patterns.md#compound-key-pattern) – Verwenden Sie zusammengesetzte **RowKey** -Werte, damit der Client in der Lage ist, mit einer einzelnen Punktabfrage nach verknüpften Daten zu suchen.  
* [Log Tail-Muster](table-storage-design-patterns.md#log-tail-pattern) – Abrufen der *n* Entitäten, die zuletzt einer Partition hinzugefügt wurden, indem Sie einen **RowKey** -Wert verwenden, mit dem nach Datum und Uhrzeit in umgekehrter Reihenfolge sortiert wird.  
