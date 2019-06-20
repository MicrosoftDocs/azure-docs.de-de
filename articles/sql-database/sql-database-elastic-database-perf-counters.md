---
title: Erstellen von Leistungsindikatoren zum Nachverfolgen der Leistung des Shardzuordnungs-Managers
description: ShardMapManager-Klasse und datenabhängiges Routing – Leistungsindikatoren
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: seoapril2019
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 02/07/2019
ms.openlocfilehash: 5c6c923c86ea0c5968079188c87ec3988ec30142
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61475693"
---
# <a name="create-performance-counters-to-track-performance-of-shard-map-manager"></a>Erstellen von Leistungsindikatoren zum Nachverfolgen der Leistung des Shardzuordnungs-Managers

Mit Leistungsindikatoren wird die Leistung der Vorgänge bei [datenabhängigem Routing](sql-database-elastic-scale-data-dependent-routing.md) nachverfolgt. Auf diese Leistungsindikatoren kann im Systemmonitor in der Kategorie „Elastic Database: Shard Management“ (Elastische Datenbank: Shardverwaltung) zugegriffen werden.

Sie können die Leistung eines [Shardzuordnungs-Managers](sql-database-elastic-scale-shard-map-management.md) insbesondere bei der Verwendung des [datenabhängigen Routings](sql-database-elastic-scale-data-dependent-routing.md) erfassen. Leistungsindikatoren werden mit Methoden der Klasse „Microsoft.Azure.SqlDatabase.ElasticScale.Client“ erstellt.  


**Aktuelle Version:** Besuchen Sie [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/). Siehe auch [Upgrade einer App auf die neueste Clientbibliothek für elastische Datenbanken](sql-database-elastic-scale-upgrade-client-library.md).

## <a name="prerequisites"></a>Voraussetzungen

* Damit der Benutzer die Leistungskategorie und -indikatoren erstellen kann, muss er Mitglied der lokalen Gruppe **Administratoren** auf dem Computer mit der Anwendung sein.  
* Damit der Benutzer eine Leistungsindikatorinstanz erstellen und die Leistungsindikatoren aktualisieren kann, muss er entweder Mitglied der Gruppe **Administratoren** oder Mitglied der Gruppe **Leistungsüberwachungsbenutzer** sein.

## <a name="create-performance-category-and-counters"></a>Erstellen der Leistungskategorie und Leistungsindikatoren

Rufen Sie zum Erstellen der Leistungsindikatoren die CreatePerformanceCategoryAndCounters-Methode der [ShardMapManagementFactory](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory)-Klasse auf. Nur ein Administrator kann die Methode ausführen:

    ShardMapManagerFactory.CreatePerformanceCategoryAndCounters()  

Sie können auch [dieses](https://gallery.technet.microsoft.com/scriptcenter/Elastic-DB-Tools-for-Azure-17e3d283) PowerShell-Skript zum Ausführen der Methode verwenden.
Die Methode erstellt die folgenden Leistungsindikatoren:  

* **Cached mappings** (Zwischengespeicherte Zuordnungen): Anzahl der Zuordnungen, die für die Shardzuordnung zwischengespeichert werden.
* **DDR operations/sec** (DDR-Vorgänge/s): Rate der Vorgänge für datenabhängiges Routing für die Shardzuordnung. Dieser Leistungsindikator wird aktualisiert, wenn ein Aufruf von [OpenConnectionForKey()](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey) zu einer erfolgreichen Verbindung mit dem Zielshard führt.
* **Mapping lookup cache hits/sec** (Cachetreffer bei der Zuordnungssuche/s): Rate der erfolgreichen Cachesuchvorgänge für Zuordnungen in der Shardzuordnung.
* **Mapping lookup cache misses/sec** (Cachefehler bei der Zuordnungssuche/s): Rate der fehlerhaften Cachsuchvorgänge für Zuordnungen in der Shardzuordnung.
* **Mappings added or updated in cache/sec** (Im Cache hinzugefügte oder aktualisierte Zuordnungen/s): Rate, mit der Zuordnungen im Cache für die Shardzuordnung hinzugefügt oder aktualisiert werden.
* **Mappings removed from cache/sec** (Aus dem Cache entfernte Zuordnungen/s): Rate, mit der Zuordnungen aus dem Cache für die Shardzuordnung entfernt werden.

Leistungsindikatoren werden für jede zwischengespeicherte Shardzuordnung pro Prozess erstellt.  

## <a name="notes"></a>Notizen

Die folgenden Ereignisse lösen die Erstellung der Leistungsindikatoren aus:  

* Initialisierung von [ShardMapManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager) mit [Eager Loading](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerloadpolicy), wenn der ShardMapManager Shardzuordnungen enthält. Dazu gehören die Methoden [GetSqlShardMapManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager) und [TryGetSqlShardMapManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.trygetsqlshardmapmanager).
* Erfolgreiches Nachschlagen einer Shardzuordnung (unter Verwendung von [GetShardMap()](https://msdn.microsoft.com/library/azure/dn824215.aspx), [GetListShardMap()](https://msdn.microsoft.com/library/azure/dn824212.aspx) oder [GetRangeShardMap()](https://msdn.microsoft.com/library/azure/dn824173.aspx)).
* Erfolgreiche Erstellung einer Shardzuordnung mit „CreateShardMap()“.

Die Leistungsindikatoren werden von allen Cachevorgängen aktualisiert, die für die Shardzuordnung und Zuordnungen ausgeführt werden. Wird die Shardzuordnung mit DeleteShardMap() erfolgreich entfernt, führt dies zu einer Löschung der Leistungsindikatorinstanz.  

## <a name="best-practices"></a>Bewährte Methoden

* Die Erstellung der Leistungskategorie und -indikatoren sollte nur einmal vor der Erstellung des ShardMapManager-Objekts erfolgen. Jede Ausführung des Befehls „CreatePerformanceCategoryAndCounters()“ löscht die vorherigen Leistungsindikatoren (von allen Instanzen werden Datenverluste gemeldet) und erstellt neue.  
* Leistungsindikatorinstanzen werden pro Prozess erstellt. Jeder Anwendungsabsturz oder jede Entfernung einer Shardzuordnung aus dem Cache führt dazu, dass die Leistungsindikatorinstanzen gelöscht werden.  

### <a name="see-also"></a>Siehe auch

[Übersicht über Features für elastische Datenbanken](sql-database-elastic-scale-introduction.md)  

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
