---
title: Migrieren vorhandener Datenbanken für die Aufskalierung
description: Erstellen eines Shardzuordnungs-Managers, um Sharddatenbanken für die Verwendung der Tools für elastische Datenbanken umzuwandeln
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: c6ad8b4c80f4b9c2fdb3c1a14209dcf0febc89e9
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92787141"
---
# <a name="migrate-existing-databases-to-scale-out"></a>Migrieren vorhandener Datenbanken für die Aufskalierung
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Verwalten Sie Ihre vorhandenen horizontal skalierten Sharddatenbanken mithilfe von Tools (wie z. B. der [Clientbibliothek für elastische Datenbanken](elastic-database-client-library.md)). Konvertieren Sie zunächst eine vorhandene Gruppe von Datenbanken für die Verwendung des [Shardzuordnungs-Managers](elastic-scale-shard-map-management.md).

## <a name="overview"></a>Übersicht

So migrieren Sie eine vorhandene Sharddatenbank:

1. Bereiten Sie die [Shardzuordnungs-Manager-Datenbank](elastic-scale-shard-map-management.md)vor.
2. Erstellen Sie die Shardzuordnung.
3. Bereiten Sie die einzelnen Shards vor.  
4. Fügen Sie Zuordnungen zur Shardzuordnung hinzu.

Diese Schritte können entweder unter Verwendung der [.NET Framework-Clientbibliothek](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/) oder der PowerShell-Skripts ausgeführt werden, die unter [Azure SQL Database – Elastic Database tools scripts](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db) (Azure SQL-Datenbank – Skripts für Tools für elastische Datenbanken) verfügbar sind. Für die hier beschriebenen Beispiele werden die PowerShell-Skripts verwendet.

Weitere Informationen zum Shardzuordnungs-Manager finden Sie unter [Shard-Zuordnungsverwaltung](elastic-scale-shard-map-management.md). Eine Übersicht der Tools für elastische Datenbanken finden Sie unter [Übersicht über Features für elastische Datenbanken](elastic-scale-introduction.md).

## <a name="prepare-the-shard-map-manager-database"></a>Vorbereiten der Shardzuordnungs-Manager-Datenbank

Der Shardzuordnungs-Manager ist eine spezielle Datenbank, die Daten zur Verwaltung von horizontal skalierten Datenbanken enthält. Sie können eine vorhandene Datenbank verwenden oder eine neue Datenbank erstellen. Eine als Shardzuordnungs-Manager eingesetzten Datenbank sollte nicht gleichzeitig eine Datenbank sein, die als Shard definiert ist. Das PowerShell-Skript erstellt die Datenbank nicht für Sie.

## <a name="step-1-create-a-shard-map-manager"></a>Schritt 1: Erstellen eines Shardzuordnungs-Managers

```powershell
# Create a shard map manager
New-ShardMapManager -UserName '<user_name>' -Password '<password>' -SqlServerName '<server_name>' -SqlDatabaseName '<smm_db_name>'
#<server_name> and <smm_db_name> are the server name and database name
# for the new or existing database that should be used for storing
# tenant-database mapping information.
```

### <a name="to-retrieve-the-shard-map-manager"></a>So rufen Sie den Shardzuordnungs-Manager ab

Nach dem Erstellen können Sie den Shardzuordnungs-Manager mit diesem Cmdlet abrufen. Dieser Schritt muss jedes Mal ausgeführt werden, wenn Sie das ShardMapManager-Objekt verwenden müssen.

```powershell
# Try to get a reference to the Shard Map Manager  
$ShardMapManager = Get-ShardMapManager -UserName '<user_name>' -Password '<password>' -SqlServerName '<server_name>' -SqlDatabaseName '<smm_db_name>'
```

## <a name="step-2-create-the-shard-map"></a>Schritt 2: Erstellen der Shardzuordnung

Wählen Sie den Typ der zu erstellenden Shardzuordnung aus. Die Auswahl hängt von der Architektur der Datenbank ab:

1. Ein Mandant pro Datenbank (Informationen finden Sie im [Glossar](elastic-scale-glossary.md)).
2. Mehrere Mandanten pro Datenbank (zwei Typen):
   1. Listenzuordnung
   2. Bereichszuordnung

Erstellen Sie für ein Modell mit einem einzelnen Mandanten eine **Listenzuordnungs** -Shardzuordnung. Beim Modell mit einem Mandanten wird eine Datenbank pro Mandant zugewiesen. Dies ist ein effektives Modell für SaaS-Entwickler, da es die Verwaltung vereinfacht.

![Listenzuordnung][1]

Beim Modell mit mehreren Mandanten werden einer einzelnen Datenbank mehrere Mandanten zugewiesen. Außerdem können Sie Mandantengruppen auf verschiedene Datenbanken verteilen. Verwenden Sie dieses Modell, wenn Sie erwarten, dass die einzelnen Mandanten geringe Datenanforderungen haben. In diesem Modell wird einer Datenbank mithilfe der **Bereichszuordnung** ein Bereich von Mandanten zugewiesen.

![Bereichszuordnung][2]

Wenn Sie einer einzelnen Datenbank mehrere Mandanten zuweisen möchten, kann das Datenbankmodell mit mehreren Mandanten auch unter Verwendung einer *Listenzuordnung* implementiert werden. Beispiel: DB1 wird zum Speichern von Informationen zu den Mandanten-IDs 1 und 5, DB2 zum Speichern von Daten für die Mandanten 7 und 10 verwendet.

![Einzeldatenbank mit mehreren Mandanten][3]

**Basierend auf Ihrer Entscheidung wählen Sie eine der folgenden Optionen aus:**

### <a name="option-1-create-a-shard-map-for-a-list-mapping"></a>Option 1: Erstellen einer Shardzuordnung für eine Listenzuordnung

Erstellen Sie mithilfe des ShardMapManager-Objekts eine Shardzuordnung.

```powershell
# $ShardMapManager is the shard map manager object
$ShardMap = New-ListShardMap -KeyType $([int]) -ListShardMapName 'ListShardMap' -ShardMapManager $ShardMapManager
```

### <a name="option-2-create-a-shard-map-for-a-range-mapping"></a>Option 2: Erstellen einer Shardzuordnung für eine Bereichszuordnung

Bei Verwendung dieses Zuordnungsmusters müssen die Mandanten-IDs fortlaufende Bereiche sein. Dabei können Bereiche unterbrochen werden, indem Sie einen Bereich beim Erstellen der Datenbanken überspringen.

```powershell
# $ShardMapManager is the shard map manager object
# 'RangeShardMap' is the unique identifier for the range shard map.  
$ShardMap = New-RangeShardMap -KeyType $([int]) -RangeShardMapName 'RangeShardMap' -ShardMapManager $ShardMapManager
```

### <a name="option-3-list-mappings-on-an-individual-database"></a>Option 3: Listenzuordnungen für eine einzelne Datenbank

Für die Einrichtung dieses Musters muss auch eine Listenzuordnung erstellt werden, wie in Schritt 2, Option 1 gezeigt.

## <a name="step-3-prepare-individual-shards"></a>Schritt 3: Vorbereiten der einzelnen Shards

Fügen Sie die einzelnen Shards (Datenbanken) dem Shardzuordnungs-Manager hinzu. Dadurch werden die einzelnen Datenbanken für das Speichern von Zuordnungsinformationen vorbereitet. Führen Sie diese Schritte für jeden Shard aus.

```powershell
Add-Shard -ShardMap $ShardMap -SqlServerName '<shard_server_name>' -SqlDatabaseName '<shard_database_name>'
# The $ShardMap is the shard map created in step 2.
```

## <a name="step-4-add-mappings"></a>Schritt 4: Hinzufügen von Zuordnungen

Die Schritte zum Hinzufügen von Zuordnungen hängen von der Art der Shardzuordnung ab, die Sie erstellt haben. Wenn Sie eine Listenzuordnung erstellt haben, fügen Sie Listenzuordnungen hinzu. Wenn Sie eine Bereichszuordnung erstellt haben, fügen Sie Bereichszuordnungen hinzu.

### <a name="option-1-map-the-data-for-a-list-mapping"></a>Option 1: Zuordnen der Daten für eine Listenzuordnung

Ordnen Sie die Daten zu, indem Sie eine Listenzuordnung für jeden Mandanten hinzufügen.  

```powershell
# Create the mappings and associate it with the new shards
Add-ListMapping -KeyType $([int]) -ListPoint '<tenant_id>' -ListShardMap $ShardMap -SqlServerName '<shard_server_name>' -SqlDatabaseName '<shard_database_name>'
```

### <a name="option-2-map-the-data-for-a-range-mapping"></a>Option 2: Zuordnen der Daten für eine Bereichszuordnung

Fügen Sie die Bereichszuordnungen für alle Zuordnungen zwischen Mandanten-ID-Bereichen und der Datenbank hinzu:

```powershell
# Create the mappings and associate it with the new shards
Add-RangeMapping -KeyType $([int]) -RangeHigh '5' -RangeLow '1' -RangeShardMap $ShardMap -SqlServerName '<shard_server_name>' -SqlDatabaseName '<shard_database_name>'
```

### <a name="step-4-option-3-map-the-data-for-multiple-tenants-on-an-individual-database"></a>Schritt 4, Option 3: Zuordnen der Daten für mehrere Mandanten zu einer einzelnen Datenbank

Führen Sie für jeden Mandanten Add-ListMapping aus (Option 1).

## <a name="checking-the-mappings"></a>Überprüfen der Zuordnungen

Informationen zu vorhandenen Shards und den zugehörigen Zuordnungen können über die folgenden Befehle abgefragt werden:  

```powershell
# List the shards and mappings
Get-Shards -ShardMap $ShardMap
Get-Mappings -ShardMap $ShardMap
```

## <a name="summary"></a>Zusammenfassung

Nach der Einrichtung können Sie die Clientbibliothek für elastische Datenbanken verwenden. Außerdem können Sie das [datenabhängige Routing](elastic-scale-data-dependent-routing.md) und das [Abfragen mehrerer Shards](elastic-scale-multishard-querying.md) nutzen.

## <a name="next-steps"></a>Nächste Schritte

Laden Sie die PowerShell-Skripts auf der Seite [Azure SQL Database – Elastic Database tools scripts](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db) (Azure SQL-Datenbank – Skripts für Tools für elastische Datenbanken) herunter.

Die Tools finden Sie auch auf GitHub: [Azure/elastic-db-tools](https://github.com/Azure/elastic-db-tools).

Verschieben Sie Daten mithilfe des Split-Merge-Tools in das Modell mit mehreren Mandanten bzw. aus diesem Modell in ein Modell mit einem einzelnen Mandanten. Siehe [Split-Merge-Tool](elastic-scale-get-started.md).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

Informationen zu gängigen Datenarchitekturmustern von mehrinstanzenfähigen SaaS-Datenbankanwendungen (Software as a Service) finden Sie unter [Entwurfsmuster für mehrinstanzenfähige SaaS-Anwendungen und Azure SQL-Datenbank](saas-tenancy-app-design-patterns.md).

## <a name="questions-and-feature-requests"></a>Fragen und Featureanfragen

Bei Fragen nutzen Sie die [Microsoft F&A-Seite für SQL-Datenbank](/answers/topics/azure-sql-database.html), Featureanforderungen können Sie im [Feedbackforum für SQL-Datenbank](https://feedback.azure.com/forums/217321-sql-database/) einreichen.

<!--Image references-->
[1]: ./media/elastic-convert-to-use-elastic-tools/listmapping.png
[2]: ./media/elastic-convert-to-use-elastic-tools/rangemapping.png
[3]: ./media/elastic-convert-to-use-elastic-tools/multipleonsingledb.png