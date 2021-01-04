---
title: Datenabhängiges Routing
description: Erfahren Sie, wie Sie die ShardMapManager-Klasse in .NET-Apps für datenabhängiges Routing, ein Feature von Sharddatenbanken in Azure SQL-Datenbank, verwenden.
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
ms.openlocfilehash: 60e8b4b21a9e62279cd0eccfabbbed680183e2a9
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92787022"
---
# <a name="use-data-dependent-routing-to-route-a-query-to-an-appropriate-database"></a>Verwenden des datenabhängigen Routings für das Weiterleiten einer Abfrage an die richtige Datenbank
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

**Datenabhängiges Routing** bezeichnet die Möglichkeit, die Daten in einer Abfrage dazu zu verwenden, die Anforderung an eine entsprechende Datenbank weiterzuleiten. Datenabhängiges Routing stellt ein grundlegendes Muster bei der Arbeit mit horizontal partitionierten Datenbanken (Sharding) dar. Der Anforderungskontext kann auch zur Weiterleitung der Anforderung verwendet werden, insbesondere dann, wenn der Shardingschlüssel nicht Teil der Abfrage ist. Jede spezifische Abfrage oder Transaktion in einer Anwendung, die datenabhängiges Routing verwendet, ist auf den Zugriff auf eine Datenbank pro Anforderung beschränkt. Für die Tools für elastische Azure SQL-Datenbanken erfolgt dieses Routing mithilfe der Klasse **ShardMapManager** ( [Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager)).

Es ist nicht erforderlich, dass die Anwendung die einzelnen Verbindungszeichenfolgen oder Datenbankspeicherorte verfolgt, die den Datenslices in der Shardingumgebung zugeordnet sind. Stattdessen öffnet bei Bedarf der [Shardzuordnungs-Manager](elastic-scale-shard-map-management.md) basierend auf den Daten in der Shardzuordnung und dem Wert des Shardingschlüssels, der das Ziel der Anwendungsanforderung ist, Verbindungen zu den richtigen Datenbanken. Bei diesem Schlüssel handelt es sich in der Regel um *customer_id* , *tenant_id* , *date_key* oder einen anderen spezifischen Bezeichner, der ein grundlegender Parameter der Datenbankanforderung ist.

Weitere Informationen finden Sie unter [Horizontales Hochskalieren von SQL Server mit datenabhängigem Routing](/previous-versions/sql/sql-server-2005/administrator/cc966448(v=technet.10)).

## <a name="download-the-client-library"></a>Herunterladen der Clientbibliothek

Downloads:

* Die Java-Version der Bibliothek finden Sie im [Maven Central Repository](https://search.maven.org/#search%7Cga%7C1%7Celastic-db-tools).
* Die .NET-Version der Bibliothek finden Sie unter [NuGet](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/).

## <a name="using-a-shardmapmanager-in-a-data-dependent-routing-application"></a>Verwenden eines ShardMapManager in einer datenabhängigen Routing-Anwendung

Anwendungen sollten **ShardMapManager** während der Initialisierung instanziieren und dazu den Factoryaufruf **GetSQLShardMapManager** ( [Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory.getsqlshardmapmanager), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager)) verwenden. In diesem Beispiel werden sowohl ein **ShardMapManager** als auch eine spezifische darin enthaltene **ShardMap** initialisiert. Dieses Beispiel zeigt die Methoden „GetSqlShardMapManager“ und „GetRangeShardMap“ ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.getrangeshardmap), [.NET](/previous-versions/azure/dn824173(v=azure.100))).

```Java
ShardMapManager smm = ShardMapManagerFactory.getSqlShardMapManager(connectionString, ShardMapManagerLoadPolicy.Lazy);
RangeShardMap<int> rangeShardMap = smm.getRangeShardMap(Configuration.getRangeShardMapName(), ShardKeyType.Int32);
```

```csharp
ShardMapManager smm = ShardMapManagerFactory.GetSqlShardMapManager(smmConnectionString, ShardMapManagerLoadPolicy.Lazy);
RangeShardMap<int> customerShardMap = smm.GetRangeShardMap<int>("customerMap"); 
```

### <a name="use-lowest-privilege-credentials-possible-for-getting-the-shard-map"></a>Verwenden der Anmeldeinformationen mit den niedrigsten möglichen Rechten zum Abrufen der Shardzuordnung

Wenn eine Anwendung die Shardzuordnung selbst nicht ändert, sollten die in der Factorymethode verwendeten Anmeldeinformationen Leseberechtigungen für die Datenbank mit der **globalen Shardzuordnung** bereitstellen. Diese Anmeldeinformationen unterscheiden sich in der Regel von Anmeldeinformationen, die für offene Verbindungen zum Shard-Zuordnungs-Manager verwendet werden. Lesen Sie dazu auch [Anmeldeinformationen für den Zugriff auf die Clientbibliothek für elastische Datenbanken](elastic-scale-manage-credentials.md).

## <a name="call-the-openconnectionforkey-method"></a>Aufrufen der OpenConnectionForKey-Methode

Die **ShardMap.OpenConnectionForKey-Methode** ( [Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper.listshardmapper.openconnectionforkey), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey)) gibt eine Verbindung zurück, die für die Befehlsausgabe an die entsprechende Datenbank basierend auf dem Wert des **key** -Parameters vorbereitet ist. Shardinginformationen werden in der Anwendung vom **ShardMapManager** zwischengespeichert. Daher erfordern diese Anforderungen i.d.R. keine Datenbanksuche in der Datenbank mit der **globalen Shardzuordnung** .

```Java
// Syntax:
public Connection openConnectionForKey(Object key, String connectionString, ConnectionOptions options)
```

```csharp
// Syntax:
public SqlConnection OpenConnectionForKey<TKey>(TKey key, string connectionString, ConnectionOptions options)
```

* Der **key** -Parameter wird als Suchschlüssel in der Shard-Zuordnung verwendet, um die passende Datenbank für die Anforderung zu bestimmen.
* **connectionString** wird verwendet, um nur die Benutzeranmeldeinformationen für die gewünschte Verbindung zu übergeben. In diesem *connectionString* -Element ist kein Datenbankname oder Servername enthalten, da die Datenbank und der Server von der Methode anhand von **ShardMap** bestimmt werden.
* **connectionOptions** ( [Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper.connectionoptions), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.connectionoptions)) sollte auf **ConnectionOptions.Validate** festgelegt werden, wenn in der Umgebung die Shardzuordnungen geändert und Zeilen aufgrund von Teilungen oder Zusammenführungen in andere Datenbanken verschoben werden können. Diese Validierung umfasst eine kurze Abfrage der lokalen Shardzuordnung in der Zieldatenbank (nicht der globalen Shardzuordnung), bevor die Verbindung der Anwendung bereitgestellt wird.

Wenn die Validierung der lokalen Shard-Zuordnung fehlschlägt (was angibt, dass der Zwischenspeicher nicht korrekt ist), fragt der Shard-Zuordnungs-Manager die globale Shard-Zuordnung ab, um den neuen, richtigen Wert für die Suche zu erhalten, den Zwischenspeicher zu aktualisieren und die passende Datenbankverbindung zu erhalten und zurückzugeben.

Verwenden Sie **ConnectionOptions.None** nur, wenn keine Shardzuordnungsänderungen erwartet werden, während eine Anwendung online ist. In diesem Fall wird davon ausgegangen, dass die zwischengespeicherten Werte immer korrekt sind, und der zusätzliche Lauf des Validierungsaufrufs der Zieldatenbank kann problemlos übersprungen werden. Dies reduziert den Datenverkehr der Datenbank. Die **connectionOptions** können auch über einen Wert in einer Konfigurationsdatei festgelegt werden, um anzugeben, ob während eines Zeitraums Sharding-Änderungen erwartet werden.  

In diesem Beispiel wird der Wert des Ganzzahlschlüssels **CustomerID** zusammen mit dem **ShardMap** -Objekt **customerShardMap** verwendet.  

```Java
int customerId = 12345;
int productId = 4321;
// Looks up the key in the shard map and opens a connection to the shard
try (Connection conn = shardMap.openConnectionForKey(customerId, Configuration.getCredentialsConnectionString())) {
    // Create a simple command that will insert or update the customer information
    PreparedStatement ps = conn.prepareStatement("UPDATE Sales.Customer SET PersonID = ? WHERE CustomerID = ?");

    ps.setInt(1, productId);
    ps.setInt(2, customerId);
    ps.executeUpdate();
} catch (SQLException e) {
    e.printStackTrace();
}
```

```csharp
int customerId = 12345;
int newPersonId = 4321;

// Connect to the shard for that customer ID. No need to call a SqlConnection
// constructor followed by the Open method.
using (SqlConnection conn = customerShardMap.OpenConnectionForKey(customerId, Configuration.GetCredentialsConnectionString(), ConnectionOptions.Validate))
{
    // Execute a simple command.
    SqlCommand cmd = conn.CreateCommand();
    cmd.CommandText = @"UPDATE Sales.Customer
                        SET PersonID = @newPersonID WHERE CustomerID = @customerID";

    cmd.Parameters.AddWithValue("@customerID", customerId);cmd.Parameters.AddWithValue("@newPersonID", newPersonId);
    cmd.ExecuteNonQuery();
}  
```

Die **OpenConnectionForKey** -Methode gibt eine neue, bereits offene Verbindung mit der richtigen Datenbank zurück. Verbindungen, die auf diese Weise verwendet werden, nutzen weiterhin alle Vorteile des Verbindungspooling.

Die **OpenConnectionForKeyAsync-Methode** ( [Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper.listshardmapper.openconnectionforkeyasync), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkeyasync)) ist auch verfügbar, wenn die Anwendung die asynchrone Programmierung mit verwendet.

## <a name="integrating-with-transient-fault-handling"></a>Integration in die Behandlung vorübergehender Fehler

Eine bewährte Methode bei der Entwicklung von Anwendungen für den Datenzugriff in der Cloud besteht darin, sicherzustellen, dass vorübergehende Fehler von der App abgefangen werden und dass die Vorgänge mehrmals wiederholt werden, bevor ein Fehler ausgelöst wird. Die Behandlung vorübergehender Fehler in Cloudanwendungen wird in „Transient Fault Handling“ (Behandeln vorübergehender Fehler) ([Java](/java/api/com.microsoft.azure.elasticdb.core.commons.transientfaulthandling), [.NET](/previous-versions/msp-n-p/dn440719(v=pandp.60))) besprochen.

Die Behandlung vorübergehender Fehler kann natürlich zusammen mit dem datenabhängigen Routingmuster verwendet werden. Die wichtigste Anforderung besteht in einer Wiederholung der gesamten Datenzugriffsanforderung, darunter des **using** -Blocks, der die datenabhängige Routingverbindung abgerufen hat. Das vorherige Beispiel könnte wie folgt umgeschrieben werden.

### <a name="example---data-dependent-routing-with-transient-fault-handling"></a>Beispiel – Datenabhängiges Routing mit Behandlung vorübergehender Fehler

```Java
int customerId = 12345;
int productId = 4321;
try {
    SqlDatabaseUtils.getSqlRetryPolicy().executeAction(() -> {
        // Looks up the key in the shard map and opens a connection to the shard
        try (Connection conn = shardMap.openConnectionForKey(customerId, Configuration.getCredentialsConnectionString())) {
            // Create a simple command that will insert or update the customer information
            PreparedStatement ps = conn.prepareStatement("UPDATE Sales.Customer SET PersonID = ? WHERE CustomerID = ?");

            ps.setInt(1, productId);
            ps.setInt(2, customerId);
            ps.executeUpdate();
        } catch (SQLException e) {
            e.printStackTrace();
        }
    });
} catch (Exception e) {
    throw new StoreException(e.getMessage(), e);
}
```

```csharp
int customerId = 12345;
int newPersonId = 4321;

Configuration.SqlRetryPolicy.ExecuteAction(() -> {

    // Connect to the shard for a customer ID.
    using (SqlConnection conn = customerShardMap.OpenConnectionForKey(customerId, Configuration.GetCredentialsConnectionString(), ConnectionOptions.Validate))
    {
        // Execute a simple command
        SqlCommand cmd = conn.CreateCommand();

        cmd.CommandText = @"UPDATE Sales.Customer
                            SET PersonID = @newPersonID
                            WHERE CustomerID = @customerID";

        cmd.Parameters.AddWithValue("@customerID", customerId);
        cmd.Parameters.AddWithValue("@newPersonID", newPersonId);
        cmd.ExecuteNonQuery();

        Console.WriteLine("Update completed");
    }
});
```

Pakete, die zum Implementieren der Behandlung vorübergehender Fehler erforderlich sind, werden automatisch heruntergeladen, wenn Sie die Beispielanwendung für elastische Datenbanken erstellen.

## <a name="transactional-consistency"></a>Transaktionskonsistenz

Transaktionseigenschaften werden für alle Vorgänge lokal auf einem Shard garantiert. So werden z. B. Transaktionen, die über datenabhängiges Routing gesandt wurden, innerhalb des Bereichs des Ziel-Shards für die Verbindung ausgeführt. Zu diesem Zeitpunkt sind keine Funktionen für mehrere Verbindungen in einer Transaktion vorgesehen. Daher besteht keine Transaktionsgarantie für Vorgänge, die über mehrere Shards hinweg ausgeführt werden.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Trennen oder erneuten Anfügen eines Shards finden Sie unter [Verwenden der RecoveryManager-Klasse zur Behebung von Problemen mit der Shardzuordnung](elastic-database-recovery-manager.md).

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]