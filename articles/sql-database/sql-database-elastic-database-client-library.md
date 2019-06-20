---
title: Erstellen skalierbarer Clouddatenbanken | Microsoft Docs
description: Erstellen skalierbarer .NET-Datenbank-Apps mit der Clientbibliothek für elastische Datenbanken
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
ms.date: 09/25/2018
ms.openlocfilehash: 1c6e77f3afc90a8c018296db80253d8b9a22159e
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66234104"
---
# <a name="building-scalable-cloud-databases"></a>Erstellen skalierbarer Clouddatenbanken

Das horizontale Skalieren von Datenbanken kann problemlos mithilfe von skalierbaren Tools und Features für die Azure SQL-Datenbank ausgeführt werden. Insbesondere können Sie die **Clientbibliothek für elastische Datenbanken** verwenden, um horizontal skalierte Datenbanken zu erstellen und zu verwalten. Mit diesem Feature können Sie ganz einfach Shardanwendungen mithilfe von Hunderten – oder sogar Tausenden – von Azure SQL-Datenbanken entwickeln.

Downloads:

* Die Java-Version der Bibliothek finden Sie im [Maven Central Repository](https://search.maven.org/#search%7Cga%7C1%7Celastic-db-tools).
* Die .NET-Version der Bibliothek finden Sie unter [NuGet](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/).

## <a name="documentation"></a>Dokumentation

1. [Erste Schritte mit Tools für elastische Datenbanken](sql-database-elastic-scale-get-started.md)
2. [Features für elastische Datenbanken](sql-database-elastic-scale-introduction.md)
3. [Shard-Zuordnungsverwaltung](sql-database-elastic-scale-shard-map-management.md)
4. [Migrieren von vorhandenen Datenbanken für die horizontale Hochskalierung](sql-database-elastic-convert-to-use-elastic-tools.md)
5. [Datenabhängiges Routing](sql-database-elastic-scale-data-dependent-routing.md)
6. [Multishardabfragen](sql-database-elastic-scale-multishard-querying.md)
7. [Hinzufügen eines Shards mithilfe der Tools für elastische Datenbanken](sql-database-elastic-scale-add-a-shard.md)
8. [Mehrinstanzenfähige Anwendungen mit elastischen Datenbanktools und zeilenbasierter Sicherheit](sql-database-elastic-tools-multi-tenant-row-level-security.md)
9. [Aktualisieren von Clientbibliothek-Apps](sql-database-elastic-scale-upgrade-client-library.md) 
10. [Übersicht über elastische Abfragen](sql-database-elastic-query-overview.md)
11. [Tools für elastische Datenbanken – Glossar](sql-database-elastic-scale-glossary.md)
12. [Clientbibliothek für elastische Datenbanken mit Entity Framework](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md)
13. [Clientbibliothek für elastische Datenbanken mit Dapper](sql-database-elastic-scale-working-with-dapper.md)
14. [Split-Merge-Tool](sql-database-elastic-scale-overview-split-and-merge.md)
15. [Leistungsindikatoren für den Shardzuordnungs-Manager](sql-database-elastic-database-client-library.md) 
16. [Häufig gestellte Fragen zu Tools für elastische Datenbanken](sql-database-elastic-scale-faq.md)

## <a name="client-capabilities"></a>Clientfunktionen

Horizontales Hochskalieren von Anwendungen mit *Sharding* konfrontiert Entwickler und Administrator gleichermaßen mit Herausforderungen. Die Clientbibliothek vereinfacht die Verwaltungsaufgaben durch Tools, mit denen sowohl Entwickler als auch Administratoren horizontal skalierte Datenbanken verwalten können. In einem typischen Beispiel gibt es viele Datenbanken zu verwalten, die als „Shards“ bezeichnet werden. Kunden werden in der gleichen Datenbank zusammengestellt, und es gibt eine Datenbank pro Kunde (ein Einzelmandantenschema). Die Clientbibliothek enthält die folgenden Features:

- **Shard-Zuordnungsverwaltung**: Eine spezielle Datenbank wird erstellt, der so genannte „Shardzuordnungs-Manager“. Die Shardzuordnungsverwaltung gibt einer Anwendung die Möglichkeit, verschiedene Metadaten über die Shards zu verwalten. Entwickler können diese Funktion verwenden, um Datenbanken als Shards zu registrieren, Zuordnungen einzelner Sharding-Schlüssel oder -Schlüsselbereiche zu diesen Datenbanken zu beschreiben und diese Metadaten zu verwalten, während sich die Anzahl und Zusammensetzung der Datenbanken gemäß den Kapazitätsänderungen weiter entwickelt. Ohne die Clientbibliothek für elastische Datenbanken ist das Schreiben des Verwaltungscodes beim Implementieren von Sharding sehr zeitaufwendig. Details finden Sie unter [Shard-Zuordnungsverwaltung](sql-database-elastic-scale-shard-map-management.md).

- **Datenabhängiges Routing**: Angenommen, eine Anforderung geht bei der Anwendung ein. Anhand des Shardschlüsselwerts der Anforderung muss die Anwendung die richtige Datenbank basierend auf dem Schlüsselwert ermitteln. Anschließend öffnet sie eine Verbindung mit der Datenbank, um die Anforderung zu verarbeiten. Das datenabhängige Routing bietet die Möglichkeit, Verbindungen durch einen einfachen Aufruf über die Shard-Zuordnung der Anwendung zu öffnen. Das datenabhängige Routing ist weiterer Bereich des Infrastrukturcodes, der jetzt durch Funktionen der Clientbibliothek für elastische Datenbanken abgedeckt wird. Details finden Sie unter [Datenabhängiges Routing](sql-database-elastic-scale-data-dependent-routing.md).
- **Abfragen von mehreren Shards (MSQ)** : Das Abfragen mehrerer Shards erfolgt, wenn eine Anforderung mehrere (oder alle) Shards umfasst. In einer Abfrage mehrerer Shards wird derselbe T-SQL-Code für alle Shards oder eine Gruppe von Shards ausgeführt. Die Ergebnisse der beteiligten Shards werden unter Verwendung der UNION ALL-Semantik in einem Gesamtergebnis zusammengeführt. Die Funktionalität wird über die Clientbibliothek verfügbar gemacht. Sie verarbeitet u.a. Aufgaben wie Verbindungsverwaltung, Threadverwaltung, Fehlerbehandlung und Verarbeiten von Zwischenergebnissen. MSQ kann Hunderte von Shards abfragen. Weitere Einzelheiten finden Sie unter [Abfragen mehrerer Shards](sql-database-elastic-scale-multishard-querying.md).

Im Allgemeinen steht den Endkunden mit den Tools für elastische Datenbanken bei der Übermittlung lokaler Shard-Vorgänge die volle T-SQL-Funktionalität zur Verfügung, anders als bei Shard-übergreifenden Vorgängen, die eine eigene Semantik haben.



## <a name="next-steps"></a>Nächste Schritte

- Clientbibliothek für elastische Datenbanken ([Java](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-elasticdb-tools%22), [.NET](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)) – zum **Herunterladen** der Bibliothek

- [Erste Schritte mit Tools für elastische Datenbanken](sql-database-elastic-scale-get-started.md): Zum Testen der **Beispiel-App**, die Clientfunktionen veranschaulicht.

- GitHub ([Java](https://github.com/Microsoft/elastic-db-tools-for-java/blob/master/README.md), [.NET](https://github.com/Azure/elastic-db-tools)) – für Beiträge zum Code
- [Übersicht über elastische Abfragen in Azure SQL-Datenbank](sql-database-elastic-query-overview.md): Zum Ausführen von elastischen Abfragen.

- [Verschieben von Daten zwischen horizontal skalierten Clouddatenbanken](sql-database-elastic-scale-overview-split-and-merge.md): Für Anweisungen zur Verwendung des **Tools zum Aufteilen bzw. Zusammenführen**.



<!-- Additional resources H2 -->

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]


<!--Anchors-->
<!--Image references-->

[1]: ./media/sql-database-elastic-database-client-library/glossary.png

