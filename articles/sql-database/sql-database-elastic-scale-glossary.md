---
title: Tools für elastische Datenbanken – Glossar | Microsoft Docs
description: Erläuterung von Begriffen, die für die Tools für flexible Datenbanken verwendet werden
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 12/04/2018
ms.openlocfilehash: 446203b45744a95c32cd41d9ded26fd960ac8a22
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60585580"
---
# <a name="elastic-database-tools-glossary"></a>Tools für elastische Datenbanken – Glossar

Die folgenden Begriffe werden für die [Tools für elastische Datenbanken](sql-database-elastic-scale-introduction.md), ein Feature von Azure SQL-Datenbank, definiert. Die Tools werden zur Verwaltung von [Shardzuordnungen](sql-database-elastic-scale-shard-map-management.md) verwendet. Sie beinhalten die [Clientbibliothek](sql-database-elastic-database-client-library.md), das [Split-Merge-Tool](sql-database-elastic-scale-overview-split-and-merge.md), [Pools für elastische Datenbanken](sql-database-elastic-pool.md) und [Abfragen](sql-database-elastic-query-overview.md). 

Diese Begriffe werden in [Hinzufügen eines Shards mithilfe der Tools für elastische Datenbanken](sql-database-elastic-scale-add-a-shard.md) und [Beheben von Problemen bei der Shardzuordnung mithilfe der RecoveryManager-Klasse](sql-database-elastic-database-recovery-manager.md) verwendet.

![Begriffe zur elastischen Skalierung][1]

**Datenbank**: Eine Azure SQL-Datenbank. 

**Datenabhängiges Routing**: Die Funktion, die es einer Anwendung ermöglicht, unter Verwendung eines bestimmten Shardschlüssels eine Verbindung mit einem Shard herzustellen. Siehe [Datenabhängiges Routing](sql-database-elastic-scale-data-dependent-routing.md). Vgl. **[Abfragen mehrerer Shards](sql-database-elastic-scale-multishard-querying.md)** .

**Globale Shardzuordnung**: Die Zuordnung zwischen Shardschlüsseln und den zugehörigen Shards in einer **Shardgruppe**. Die globale Shardzuordnung wird im **Shardzuordnungs-Manager** gespeichert. Vgl. **lokale Shardzuordnung**.

**Listenshardzuordnung**: Eine Shardzuordnung, in der die Shardschlüssel einzeln zugeordnet sind. Vgl. **Bereichsshardzuordnung**.   

**Lokale Shardzuordnung**: Wird in einem Shard gespeichert und enthält die Zuordnungen für die Shardlets, die sich in diesem Shard befinden.

**Abfragen von mehreren Shards**: Die Möglichkeit, eine Abfrage für mehrere Shards durchzuführen. Die Resultsets werden über die UNION ALL-Semantik zurückgegeben (auch als „Auffächerungsabfrage“ bezeichnet). Vgl. **Datenabhängiges Routing**.

**Mehrinstanzenfähig** und **Einzelmandant**: Die folgende Abbildung zeigt eine Einzelmandantendatenbank und eine mehrinstanzenfähige Datenbank:

![Einzelmandanten- und mehrinstanzenfähige Datenbanken](./media/sql-database-elastic-scale-glossary/multi-single-simple.png)

Hier ist eine Darstellung von Einzelmandanten- und mehrinstanzenfähigen **Sharddatenbanken** . 

![Einzelmandanten- und mehrinstanzenfähige Datenbanken](./media/sql-database-elastic-scale-glossary/shards-single-multi.png)

**Bereichsshardzuordnung**: Eine Shardzuordnung, in der die Strategie der Shardverteilung auf mehreren Bereichen zusammenhängender Werte basiert. 

**Verweistabellen**: Tabellen, die nicht partitioniert sind, aber über mehrere Shards hinweg repliziert werden. Postleitzahlen können z. B. in einer Verweistabelle gespeichert werden. 

**Shard**: Eine Azure SQL-Datenbank, in der die Daten aus einem horizontal partitionierten Dataset gespeichert werden. 

**Shardelastizität**: Die Fähigkeit, sowohl **horizontal** als auch **vertikal** zu skalieren.

**Shardtabellen**: Tabellen, die horizontal partitioniert sind, deren Daten also anhand ihrer Shardschlüsselwerte auf Shards verteilt werden. 

**Shardschlüssel**: Ein Spaltenwert, der bestimmt, wie Daten auf Shards verteilt werden. Folgende Werttypen sind zulässig: **int**, **bigint**, **varbinary** oder **uniqueidentifier**. 

**Shardgruppe**: Die Sammlung von Shards, die zur gleichen Shardzuordnung im Shardzuordnungs-Manager gehören.  

**Shardlet**: Alle Daten, die mit einem einzelnen Wert eines Shardschlüssels in einem Shard verknüpft sind. Ein Shardlet ist die kleinste Einheit der Datenverschiebung, die bei der erneuten Verteilung von Shard-Tabellen möglich ist. 

**Shardzuordnung**: Der Satz von Zuordnungen zwischen Shardschlüsseln und den zugehörigen Shards.

**Shardzuordnungs-Manager**: Ein Verwaltungsobjekt und Datenspeicher mit den Shardzuordnungen, Shardspeicherorten und Zuordnungen für eine oder mehrere Shardgruppen.

![Zuordnungen][2]

## <a name="verbs"></a>Verben
**Horizontale Skalierung**: Das Hoch- oder Herunterskalieren einer Sammlung von Shards durch Hinzufügen oder Entfernen von Shards zu bzw. aus einer Shardzuordnung, wie unten dargestellt.

![Horizontale und vertikale Skalierung][3]

**Zusammenführen**: Das Verschieben von Shardlets aus zwei Shards in einen Shard und das entsprechende Aktualisieren der Shardzuordnung.

**Shardletverschiebung**: Das Verschieben eines einzelnen Shardlets in einen anderen Shard. 

**Shard**: Das horizontale Partitionieren identisch strukturierter Daten über mehrere Datenbanken hinweg anhand eines Shardschlüssels.

**Teilen**: Das Verschieben mehrerer Shardlets aus einem Shard in einen anderen (in der Regel neuen) Shard. Als Aufteilungspunkt wird ein Sharding-Schlüssel vom Benutzer bereitgestellt.

**Vertikale Skalierung**: Das Herauf- oder Herunterskalieren der Computegröße eines einzelnen Shards. Dies erfolgt z. B. durch Ändern eines Shards von Standard in Premium (wodurch mehr Computerressourcen zur Verfügung stehen). 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-glossary/glossary.png
[2]: ./media/sql-database-elastic-scale-glossary/mappings.png
[3]: ./media/sql-database-elastic-scale-glossary/h_versus_vert.png

