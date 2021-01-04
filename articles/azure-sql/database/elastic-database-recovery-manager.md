---
title: Recovery Manager zur Behebung von Problemen mit der Shard-Zuordnung
description: Verwenden der RecoveryManager-Klasse zum Beheben von Problemen mit Shardzuordnungen
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/03/2019
ms.openlocfilehash: 91bcd998849c619a328a198c97bb8c977b9d8232
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92792224"
---
# <a name="using-the-recoverymanager-class-to-fix-shard-map-problems"></a>Verwenden der RecoveryManager-Klasse zur Behebung von Problemen mit der Shard-Zuordnung
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Die [RecoveryManager](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager)-Klasse bietet ADO.NET-Anwendungen die Möglichkeit, Inkonsistenzen zwischen der globalen Shardzuordnung (GSM) und der lokalen Shardzuordnung (LSM) in einer horizontal partitionierten Datenbank einfach zu erkennen und zu beheben.

Die GSM und die LSM verfolgen die Zuordnung der einzelnen Datenbanken in einer horizontal partitionierten Umgebung. Gelegentlich tritt eine Unterbrechung zwischen GSM und LSM auf. In diesem Fall verwenden Sie die RecoveryManager-Klasse zum Erkennen und Reparieren der Unterbrechung.

Die RecoveryManager-Klasse ist Teil der [Clientbibliothek für elastische Datenbanken](elastic-database-client-library.md).

![Shardzuordnung][1]

Begriffsdefinitionen finden Sie unter [Tools für elastische Datenbanken – Glossar](elastic-scale-glossary.md). Eine Erläuterung dazu, wie der **ShardMapManager** zum Verwalten von Daten in einer Shardlösung eingesetzt wird, finden Sie unter [Shard-Zuordnungsverwaltung](elastic-scale-shard-map-management.md).

## <a name="why-use-the-recovery-manager"></a>Gründe für die Verwendung von Recovery Manager

In einer Umgebung mit Sharddatenbanken gibt es einen Mandanten pro Datenbank und viele Datenbanken pro Server. Es können auch viele Server in der Umgebung vorhanden sein. Jede Datenbank muss in der Shardzuordnung zugeordnet werden, sodass Aufrufe an den richtigen Server und die richtige Datenbank weitergeleitet werden können. Datenbanken werden anhand eines **Shardingschlüssels** nachverfolgt, und jedem Shard wird ein **Bereich von Schlüsselwerten** zugewiesen. Beispielsweise kann ein Shardingschlüssel die Kundennamen "D" bis "F" darstellen. Die Zuordnung aller Shards (d. h. Datenbanken) und ihre Zuordnungsbereiche sind in der **globalen Shardzuordnung (Global Shard Map, GSM)** enthalten. Jede Datenbank enthält außerdem eine Zuordnung der auf dem Shard enthaltenen Bereiche, die als **lokale Shardzuordnung (Local Shard Map, LSM)** bezeichnet wird. Wenn eine App eine Verbindung mit einem Shard herstellt, wird die Zuordnung für den schnellen Abruf mit der App zwischengespeichert. Die LSM dient zum Überprüfen zwischengespeicherter Daten.

Die GSM und die LSM können aus den folgenden Gründen nicht mehr synchron sein:

1. Ein Shard, dessen Bereich anscheinend nicht länger genutzt wird, wurde gelöscht, oder ein Shard wurde umbenannt. Das Löschen eines Shards führt zu einer **verwaisten Shardzuordnung** . Eine umbenannte Datenbank kann auf ähnliche Weise zu einer verwaisten Shardzuordnung führen. Abhängig davon, was geändert werden soll, muss der Shard möglicherweise entfernt oder der Shardspeicherort aktualisiert werden. Informationen zum Wiederherstellen einer gelöschten Datenbank finden Sie unter [Wiederherstellen einer gelöschten Datenbank](recovery-using-backups.md).
2. Ein Geofailoverereignis tritt ein. Um fortzufahren, müssen der Servername und der Datenbankname des Shardzuordnungs-Managers in der Anwendung und anschließend noch die Shardzuordnungsdetails für sämtliche Shards in einer Shardzuordnung aktualisiert werden. Bei einem Geofailover sollte eine solche Wiederherstellungslogik innerhalb des Failoverworkflows automatisiert werden. Die Automatisierung von Wiederherstellungsaktionen gewährleistet eine reibungslose Verwaltbarkeit für geofähige Datenbanken und vermeidet manuelle, von Personen durchzuführende Aktionen. Weitere Informationen zu Optionen zum Wiederherstellen einer Datenbank bei einem Rechenzentrumsausfall finden Sie unter [Geschäftskontinuität](business-continuity-high-availability-disaster-recover-hadr-overview.md) und [Notfallwiederherstellung](disaster-recovery-guidance.md).
3. Entweder ein Shard oder die ShardMapManager-Datenbank wird auf einen früheren Zeitpunkt wiederhergestellt. Weitere Informationen zur Zeitpunktwiederherstellung mithilfe von Sicherungen finden Sie unter [Wiederherstellen einer Azure SQL-Datenbank mit automatisierten Datenbanksicherungen](recovery-using-backups.md).

Weitere Informationen zu den Azure SQL-Datenbanktools für elastische Datenbanken und zur Georeplikation und Wiederherstellung finden Sie in folgenden Artikeln:

* [Übersicht: Geschäftskontinuität für die Cloud und Notfallwiederherstellung für Datenbanken mit SQL-Datenbank](business-continuity-high-availability-disaster-recover-hadr-overview.md)
* [Erste Schritte mit Tools für elastische Datenbanken](elastic-scale-get-started.md)  
* [Shardzuordnungsverwaltung](elastic-scale-shard-map-management.md)

## <a name="retrieving-recoverymanager-from-a-shardmapmanager"></a>Abrufen von RecoveryManager aus einem ShardMapManager

Der erste Schritt ist die Erstellung eine RecoveryManager-Instanz. Die [GetRecoveryManager-Methode](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getrecoverymanager) gibt den Recovery Manager für die aktuelle [ShardMapManager](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager)-Instanz zurück. Zur Behebung von Inkonsistenzen in der Shardzuordnung müssen Sie zuerst den RecoveryManager für die jeweilige Shardzuordnung abrufen.

   ```java
    ShardMapManager smm = ShardMapManagerFactory.GetSqlShardMapManager(smmConnectionString,  
             ShardMapManagerLoadPolicy.Lazy);
             RecoveryManager rm = smm.GetRecoveryManager();
   ```

In diesem Beispiel wird der RecoveryManager vom ShardMapManager aus initialisiert. Der ShardMapManager, der eine ShardMap enthält, ist ebenfalls bereits initialisiert.

Da dieser Anwendungscode die Shardzuordnung selbst verändert, sollten die in der Factorymethode verwendeten Anmeldeinformationen (im vorherigen Beispiel „smmConnectionString“) nur Lese-/Schreibberechtigungen für die GSM-Datenbank besitzen, auf die durch die Verbindungszeichenfolge verwiesen wird. Diese Anmeldeinformationen unterscheiden sich in der Regel von Anmeldeinformationen, die für das Öffnen von Verbindungen zum datenabhängigen Routing verwendet werden. Weitere Informationen hierzu finden Sie unter [Verwalten von Anmeldeinformationen in der Clientbibliothek für elastische Datenbanken](elastic-scale-manage-credentials.md).

## <a name="removing-a-shard-from-the-shardmap-after-a-shard-is-deleted"></a>Entfernen eines gelöschten Shards aus der ShardMap

Die [DetachShard-Methode](/previous-versions/azure/dn842083(v=azure.100)) trennt den angegebenen Shard aus der Shardzuordnung und löscht die Zuordnungen, die dem Shard zugeordnet sind.  

* Der location-Parameter ist der Speicherort des Shards, der getrennt wird, insbesondere dessen Servername und Datenbankname.
* Der shardMapName-Parameter ist der Shardzuordnungsname. Dieser ist nur erforderlich, wenn mehrere Shardzuordnungen von demselben Shardzuordnungs-Manager verwaltet werden. Optional.

> [!IMPORTANT]
> Verwenden Sie dieses Verfahren nur, wenn Sie sicher sind, dass der Bereich für die aktualisierte Zuordnung leer ist. Durch die oben genannten Methoden werden keine Daten für den verschobenen Bereich überprüft. Deshalb empfiehlt es sich, Prüfroutinen im Code zu implementieren.

Dieses Beispiel entfernt die Shards aus der Shardzuordnung.

   ```java
   rm.DetachShard(s.Location, customerMap);
   ```

Die Shardzuordnung entspricht dem Shardspeicherort in der GSM vor dem Löschen des Shards. Da der Shard gelöscht wurde, wird davon ausgegangen, dass dies absichtlich geschehen ist und der Shardingschlüsselbereich nicht mehr verwendet wird. Wenn dies nicht der Fall ist, können Sie die Point-in-Time-Wiederherstellung ausführen, um den Shard von einem früheren Zeitpunkt wiederherzustellen. (Lesen Sie in diesem Fall den folgenden Abschnitt, um Shardinkonsistenzen zu ermitteln.) Informationen zum Wiederherstellen finden Sie unter [Zeitpunktwiederherstellung](recovery-using-backups.md).

Da davon ausgegangen wird, dass die Datenbank absichtlich gelöscht wurde, besteht die letzte administrative Bereinigungsaktion darin, den Eintrag für den Shard im Shardzuordnungs-Manager zu löschen. Dies verhindert, dass die Anwendung versehentlich Informationen in einen unerwarteten Bereich schreibt.

## <a name="to-detect-mapping-differences"></a>So erkennen Sie Zuordnungsunterschiede

Die [DetectMappingDifferences-Methode](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.detectmappingdifferences) wählt eine der Shardzuordnungen (entweder lokal oder global) aus, gibt sie als gültige Quelle zurück und stimmt Zuordnungen in beiden Shardzuordnungen (GSM und LSM) darauf ab.

   ```java
   rm.DetectMappingDifferences(location, shardMapName);
   ```

* Der *Speicherort* gibt den Servernamen und den Datenbanknamen an.
* Der *shardMapName* -Parameter ist der Shardzuordnungsname. Dieser ist nur erforderlich, wenn mehrere Shardzuordnungen von demselben Shardzuordnungs-Manager verwaltet werden. Optional.

## <a name="to-resolve-mapping-differences"></a>So lösen Sie Zuordnungsunterschiede auf

Die [ResolveMappingDifferences-Methode](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.resolvemappingdifferences) wählt eine der Shardzuordnungen (entweder lokal oder global) als gültige Quelle aus und stimmt Zuordnungen in beiden Shardzuordnungen (GSM und LSM) darauf ab.

   ```java
   ResolveMappingDifferences (RecoveryToken, MappingDifferenceResolution.KeepShardMapping);
   ```

* Der *RecoveryToken* -Parameter listet die Unterschiede in den Zuordnungen zwischen GSM und LSM für den jeweiligen Shard auf.
* Mit der [MappingDifferenceResolution-Enumeration](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.mappingdifferenceresolution) wird die Methode angegeben, die zum Beheben des Unterschieds zwischen den Shardzuordnungen verwendet werden soll.
* **MappingDifferenceResolution.KeepShardMapping** wird für den Fall empfohlen, dass die LSM die genaue Zuordnung enthält und daher die Zuordnung im Shard verwendet werden soll. Dies ist normalerweise bei einem Failover der Fall: Der Shard befindet sich jetzt auf einem neuen Server. Da der Shard zuerst (über die RecoveryManager.DetachShard-Methode) aus der GSM entfernt werden muss, ist auf der GSM keine Zuordnung mehr vorhanden. Aus diesem Grund muss die LSM verwendet werden, um die Shardzuordnung wiederherzustellen.

## <a name="attach-a-shard-to-the-shardmap-after-a-shard-is-restored"></a>Anfügen eines Shards an die ShardMap nach dem Wiederherstellen eines Shards

Die [AttachShard-Methode](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.attachshard) fügt den angegebenen Shard an die Shardzuordnung an. Anschließend erkennt es eventuelle Inkonsistenzen in der Shardzuordnung und aktualisiert die Zuordnungen so, dass sie dem Shard zum Zeitpunkt der Wiederherstellung entsprechen. Es wird davon ausgegangen, dass die Datenbank in den ursprünglichen Datenbanknamen (vor der Wiederherstellung des Shards) umbenannt wird, da die Point-in-Time-Wiederherstellung standardmäßig eine neue Datenbank mit angefügtem Zeitstempel verwendet.

   ```java
   rm.AttachShard(location, shardMapName)
   ```

* Der *location* -Parameter entspricht dem Servernamen und dem Datenbanknamen des angefügten Shards.
* Der *shardMapName* -Parameter ist der Shardzuordnungsname. Dieser ist nur erforderlich, wenn mehrere Shardzuordnungen von demselben Shardzuordnungs-Manager verwaltet werden. Optional.

In diesem Beispiel wird der Shardzuordnung ein Shard hinzugefügt, der vor kurzem von einem früheren Zeitpunkt wiederhergestellt wurde. Da der Shard (d. h. die Zuordnung für den Shard in der LSM) wiederhergestellt wurde, ist er möglicherweise inkonsistent mit dem Shardeintrag in der GSM. Außerhalb dieses Beispielcodes wurde der Shard wiederhergestellt und auf den ursprünglichen Namen der Datenbank umbenannt. Da er wiederhergestellt wurde, wird davon ausgegangen, dass die Zuordnung in der LSM die vertrauenswürdige Zuordnung ist.

   ```java
   rm.AttachShard(s.Location, customerMap);
   var gs = rm.DetectMappingDifferences(s.Location);
      foreach (RecoveryToken g in gs)
       {
       rm.ResolveMappingDifferences(g, MappingDifferenceResolution.KeepShardMapping);
       }
   ```

## <a name="updating-shard-locations-after-a-geo-failover-restore-of-the-shards"></a>Aktualisieren von Shardspeicherorten nach einem Geofailover (Wiederherstellung) der Shards

Bei einem Geofailover wird die sekundäre Datenbank für den Schreibzugriff freigegeben und als neue primäre Datenbank festgelegt. Der Name des Servers und möglicherweise der der Datenbank (je nach Konfiguration) können von den ursprünglichen primären Namen abweichen. Daher müssen die Zuordnungseinträge für den Shard in der GSM und in der LSM korrigiert werden. Wenn die Datenbank auf einen anderen Namen oder Speicherort oder auf einen früheren Zeitpunkt wiederhergestellt wird, kann dies ebenso zu Inkonsistenzen in den Shardzuordnungen führen. Der Shardzuordnungs-Manager verarbeitet die Verteilung der geöffneten Verbindungen an die richtige Datenbank. Die Verteilung basiert auf den Daten in der Shardzuordnung und auf dem Wert des Shardingschlüssels, an den die Anwendungsanforderung gerichtet ist. Nach einem Geofailover müssen diese Informationen auf den richtigen Servernamen, den richtigen Datenbanknamen und die richtige Shardzuordnung der wiederhergestellten Datenbank aktualisiert werden.

## <a name="best-practices"></a>Bewährte Methoden

Geofailover und -wiederherstellung sind Vorgänge, die in der Regel von einem Cloudadministrator der Anwendung verwaltet werden, der bewusst die Geschäftskontinuitätsfunktionen von Azure SQL-Datenbank einsetzt. Die Planung der Geschäftskontinuität erfordert Prozesse, Verfahren und Maßnahmen, um zu gewährleisten, dass der Geschäftsbetrieb ohne Unterbrechung fortgesetzt werden kann. Die in der RecoveryManager-Klasse verfügbaren Methoden sollten in diesem Workflow verwendet werden, um sicherzustellen, dass die GSM und die LSM basierend auf der durchgeführten Wiederherstellungsaktion auf dem neuesten Stand gehalten werden. Es gibt fünf grundlegende Schritte, um ordnungsgemäß sicherzustellen, dass die GSM und die LSM nach einem Failoverereignis die richtigen Informationen enthalten. Der Anwendungscode zum Ausführen dieser Schritte kann in vorhandene Tools und Workflows integriert werden.

1. Rufen Sie den RecoveryManager aus dem ShardMapManager ab.
2. Trennen Sie den alten Shard von der Shardzuordnung.
3. Fügen Sie den neuen Shard an die Shardzuordnung an, einschließlich des neuen Shardspeicherorts.
4. Ermitteln Sie Inkonsistenzen in der Zuordnung zwischen GSM und LSM.
5. Lösen Sie Unterschiede zwischen der GSM und der LSM auf, indem Sie die LSM als vertrauenswürdig einstufen.

In diesem Beispiel werden die folgenden Schritte ausgeführt:

1. Entfernen von Shards aus der Shardzuordnung, die Shardspeicherorte von vor dem Failover angeben.
2. Anfügen von Shards an die Shardzuordnung, die die neuen Shardspeicherorte angeben. (Der Parameter „Configuration.SecondaryServer“ ist der neue Servername, aber derselbe Datenbankname.)
3. Abrufen der Wiederherstellungstoken durch das Erkennen von Zuordnungsunterschieden zwischen der GSM und der LSM für die einzelnen Shards.
4. Auflösen der Inkonsistenzen durch Verwenden der LSM-Zuordnung als vertrauenswürdige Zuordnung für die einzelnen Shards.

   ```java
   var shards = smm.GetShards();
   foreach (shard s in shards)
   {
     if (s.Location.Server == Configuration.PrimaryServer)
         {
          ShardLocation slNew = new ShardLocation(Configuration.SecondaryServer, s.Location.Database);
          rm.DetachShard(s.Location);
          rm.AttachShard(slNew);
          var gs = rm.DetectMappingDifferences(slNew);
          foreach (RecoveryToken g in gs)
            {
               rm.ResolveMappingDifferences(g, MappingDifferenceResolution.KeepShardMapping);
            }
        }
    }
   ```

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/elastic-database-recovery-manager/recovery-manager.png