---
title: Verwenden der Clientbibliothek für elastische Datenbanken mit Dapper
description: Verwenden der Clientbibliothek für elastische Datenbanken mit Dapper
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/04/2018
ms.openlocfilehash: d660e62ea293bd3cc377b95612cfaf41a9f1cd6a
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92793363"
---
# <a name="using-the-elastic-database-client-library-with-dapper"></a>Verwenden der Clientbibliothek für elastische Datenbanken mit Dapper
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Dieses Dokument ist für Entwickler bestimmt, die Anwendungen mithilfe von Dapper erstellen, aber auch mithilfe von [Tools für elastische Datenbanken](elastic-scale-introduction.md) Anwendungen erstellen möchten, die zum Aufskalieren ihrer Datenebene Sharding implementieren.  Dieses Dokument veranschaulicht, welche Änderungen in Dapper-basierten Anwendungen erforderlich sind, um Tools für elastische Datenbanken zu integrieren. Wir konzentrieren uns darauf, die Shardverwaltung für elastische Datenbanken und das datenabhängige Routing in Dapper zu integrieren. 

**Beispielcode:** [Elastic DB Tools for Azure SQL – Dapper Integration](https://code.msdn.microsoft.com/Elastic-Scale-with-Azure-e19fc77f) (Elastische Datenbanktools für Azure SQL – Dapper-Integration).

Die Integration von **Dapper** und **DapperExtensions** in die Clientbibliothek für elastische Datenbanken für Azure SQL-Datenbank ist einfach. Für Ihre Anwendungen können Sie das datenabhängige Routing verwenden, indem Sie das Erstellen und Öffnen neuer [SqlConnection](/dotnet/api/system.data.sqlclient.sqlconnection)-Objekte so ändern, dass der [OpenConnectionForKey](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)-Aufruf aus der [Clientbibliothek](/previous-versions/azure/dn765902(v=azure.100)) verwendet wird. Dadurch werden die Änderungen in der Anwendung auf Situationen beschränkt, in denen neue Verbindungen erstellt und geöffnet werden. 

## <a name="dapper-overview"></a>Übersicht über Dapper
**Dapper** ist ein objektrelationaler Mapper. Er ordnet .NET-Objekte aus Ihrer Anwendung einer relationalen Datenbank (und umgekehrt) zu. Der erste Teil des Beispielcodes veranschaulicht, wie Sie die Clientbibliothek für elastische Datenbanken in Dapper-basierte Anwendungen integrieren können. Im zweiten Teil des Beispielcodes erfahren Sie dann, wie die Integration verläuft, wenn Sie sowohl Dapper als auch DapperExtensions verwenden.  

Die Mapper-Funktion in Dapper stellt Erweiterungsmethoden für Datenbankverbindungen bereit, die die Übermittlung von T-SQL-Anweisungen zur Ausführung oder Abfrage der Datenbank vereinfacht. Dapper erleichtert z.B. die Zuordnung zwischen .NET-Objekten und den SQL-Anweisungsparametern für **Execute** -Aufrufe. Außerdem können Sie **Query** -Aufrufe aus Dapper verwenden, um die Ergebnisse Ihrer SQL-Abfragen in .NET-Objekte zu übernehmen. 

Bei Verwendung von DapperExtensions müssen keine SQL-Anweisungen mehr angegeben werden. Durch die Übermittlung von Erweiterungsmethoden wie **GetList** oder **Insert** über die Datenbankverbindung werden im Hintergrund die SQL-Anweisungen erstellt.

Ein weiterer Vorteil von Dapper und DapperExtensions besteht darin, dass die Anwendung die Herstellung der Datenbankverbindung steuert. Dies erleichtert die Interaktion mit der Clientbibliothek für elastische Datenbanken, die Datenbankverbindungen basierend auf der Zuordnung von Shardlets und Datenbanken vermittelt.

Wie Sie die Dapper-Assemblys erhalten, erfahren Sie unter [Dapper .NET](https://www.nuget.org/packages/Dapper/). Informationen zu Dapper-Erweiterungen finden Sie unter [DapperExtensions](https://www.nuget.org/packages/DapperExtensions).

## <a name="a-quick-look-at-the-elastic-database-client-library"></a>Ein kurzer Blick auf die Clientbibliothek für elastische Datenbanken
Mit der Clientbibliothek für elastische Datenbanken definieren Sie Partitionen Ihrer Anwendungsdaten, sogenannte *Shardlets* , ordnen sie Datenbanken zu und identifizieren sie nach *Shardingschlüsseln* . Sie können über beliebig viele Datenbanken verfügen und die Shardlets auf diese Datenbanken verteilen. Die Zuordnung der Sharding-Schlüsselwerte zu den Datenbanken wird in einer Shard-Zuordnung gespeichert, die durch die Bibliothek-APIs bereitgestellt wird. Diese Funktion wird als **Shard-Zuordnungsverwaltung** bezeichnet. Die Shard-Zuordnung fungiert auch als Broker von Datenbankverbindungen für Anforderungen, die einen Sharding-Schlüssel enthalten. Diese Funktion wird als **datenabhängiges Routing** bezeichnet.

![Shardzuordnungen und datenabhängiges Routing][1]

Der Shard-Zuordnungs-Manager schützt den Benutzer vor inkonsistenten Einblicken in Shardlet-Daten, die auftreten können, wenn gleichzeitige Shardlet-Verwaltungsvorgänge für die Datenbanken ausgeführt werden. Dazu fungieren die Shard-Zuordnungen als Broker der Datenbankverbindungen für eine mit der Bibliothek erstellte Anwendung. Wenn sich Shard-Verwaltungsvorgänge auf das Shardlet auswirken, kann es passieren, dass Datenbankverbindungen durch die Shard Map-Funktionalität automatisch beendet werden. 

Anstatt Dapper-Verbindungen auf die herkömmliche Weise zu erstellen, müssen Sie die [OpenConnectionForKey](/previous-versions/azure/dn824099(v=azure.100))-Methode verwenden. Dadurch wird sichergestellt, dass alle Überprüfungen stattfinden und Verbindungen bei Datenverschiebungen zwischen Shards ordnungsgemäß verwaltet werden.

### <a name="requirements-for-dapper-integration"></a>Anforderungen an die Dapper-Integration
Bei gleichzeitiger Verwendung der Clientbibliothek für elastische Datenbanken und der Dapper-APIs sollten die folgenden Eigenschaften beibehalten werden:

* **Aufskalieren:** Wir möchten Datenbanken werden der Datenschicht der Shardanwendung entsprechend den Kapazitätsanforderungen der Anwendung hinzufügen oder aus dieser entfernen. 
* **Konsistenz:** Da die Anwendung mithilfe von Sharding horizontal hochskaliert wird, müssen Sie datenabhängiges Routing ausführen. Wir möchten zu diesem Zweck das datenabhängige Routing der Bibliothek verwenden. Vor allem sollten Sie die Überprüfungs- und Konsistenzzusicherungen der Verbindungen beibehalten, die über den Shardzuordnungs-Manager vermittelt werden. Dies hilft dabei, Datenverfälschung oder falsche Abfrageergebnisse zu vermeiden. Dadurch wird sichergestellt, dass Verbindungen mit einem bestimmten Shardlet zurückgewiesen oder unterbrochen werden, wenn das Shardlet z. B. gerade mithilfe von Split/Merge-APIs auf einen anderen Shard verschoben wird.
* **Objektzuordnung:** Wir möchten die programmierfreundlichen Dapper-Zuordnungen beibehalten, um Code zwischen Klassen in der Anwendung und den zugrunde liegenden Datenbankstrukturen zu übersetzen. 

Der folgende Abschnitt erläutert, wie Sie diese Anforderungen für Anwendungen auf Grundlage von **Dapper** und **DapperExtensions** erfüllen.

## <a name="technical-guidance"></a>Technischer Leitfaden
### <a name="data-dependent-routing-with-dapper"></a>Datenabhängiges Routing mit Dapper
Bei Dapper ist die Anwendung normalerweise dafür verantwortlich, die Verbindungen mit der zugrunde liegenden Datenbank zu erstellen und zu öffnen. Wenn von der Anwendung ein T-Typ angegeben wird, gibt Dapper Abfrageergebnisse als .NET-Auflistungen des T-Typs zurück. Dapper ordnet die T-SQL-Ergebniszeilen Objekten des T-Typs zu. Bei DML (Data Manipulation Language)-Anweisungen geht Dapper ähnlich vor, indem er .NET-Objekte den SQL-Werten oder -Parametern zuordnet. Dapper stellt diese Funktionalität über Erweiterungsmethoden des herkömmlichen [SqlConnection](/dotnet/api/system.data.sqlclient.sqlconnection)-Objekts aus den ADO .NET SQL-Clientbibliotheken bereit. Die von den Elastic Scale-APIs für DDR zurückgegebene SQL-Verbindung entspricht ebenfalls einem herkömmlichen [SqlConnection](/dotnet/api/system.data.sqlclient.sqlconnection)-Objekt. Dadurch können wir Dapper- Erweiterungen direkt über den von der DDR-API der Clientbibliothek zurückgegebenen Typ verwenden, da es sich ebenfalls um eine einfache SQL-Clientverbindung handelt.

Diese Beobachtungen erleichtern die Verwendung von Verbindungen, die von der Clientbibliothek für elastische Datenbanken für Dapper vermittelt werden.

Dieses Codebeispiel (aus dem zugehörigen Beispiel) veranschaulicht eine Vorgehensweise, bei der der Shardingschlüssel der Bibliothek von der Anwendung bereitgestellt wird, um die Verbindung an den richtigen Shard zu vermitteln.   

```csharp
    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                     key: tenantId1,
                     connectionString: connStrBldr.ConnectionString,
                     options: ConnectionOptions.Validate))
    {
        var blog = new Blog { Name = name };
        sqlconn.Execute(@"
                      INSERT INTO
                            Blog (Name)
                            VALUES (@name)", new { name = blog.Name }
                        );
    }
```

Der Aufruf der [OpenConnectionForKey](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1) -API ersetzt das standardmäßige Erstellen und Öffnen einer SQL-Clientverbindung. Der [OpenConnectionForKey](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)-Aufruf akzeptiert die Argumente, die für das datenabhängige Routing erforderlich sind: 

* Die Shardzuordnung für den Zugriff auf Schnittstellen für datenabhängiges Routing
* Der Shardingschlüssel zum Identifizieren des Shardlets
* Die Anmeldeinformationen (Benutzername und Kennwort) für die Verbindung mit dem Shard

Das Shard Map-Objekt erstellt eine Verbindung mit dem Shard, der das Shardlet für den jeweiligen Shardingschlüssel enthält. Die Verbindung wird von den Client-APIs für elastische Datenbanken außerdem markiert, um die Konsistenzzusicherungen zu implementieren. Da der Aufruf von [OpenConnectionForKey](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1) ein normales SQL-Clientverbindungsobjekt zurückgibt, folgt der nachfolgende Aufruf der **Execute** -Erweiterungsmethode von Dapper der üblichen Verfahrensweise in Dapper.

Abfragen funktionieren ganz ähnlich. Sie öffnen zuerst die Verbindung mithilfe von [OpenConnectionForKey](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1) aus der Client-API. Anschließend verwenden Sie die regulären Dapper-Erweiterungsmethoden, um den .NET-Objekten die Ergebnisse Ihrer SQL-Abfrage zuzuordnen:

```csharp
    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                    key: tenantId1,
                    connectionString: connStrBldr.ConnectionString,
                    options: ConnectionOptions.Validate ))
    {
           // Display all Blogs for tenant 1
           IEnumerable<Blog> result = sqlconn.Query<Blog>(@"
                                SELECT *
                                FROM Blog
                                ORDER BY Name");

           Console.WriteLine("All blogs for tenant id {0}:", tenantId1);
           foreach (var item in result)
           {
                Console.WriteLine(item.Name);
            }
    }
```

Der **using** -Block mit der DDR-Verbindung ordnet alle Datenbankvorgänge innerhalb des Blocks dem Shard zu, in dem tenantId1 gespeichert ist. Die Abfrage gibt nur die im aktuellen Shard gespeicherten Blogs, aber nicht die in anderen Shards gespeicherten Blogs zurück. 

## <a name="data-dependent-routing-with-dapper-and-dapperextensions"></a>Datenabhängiges Routing mit Dapper und DapperExtensions
Dapper wird mit einem ganzen Ökosystem zusätzlicher Erweiterungen bereitgestellt, die eine komfortablere und von der Datenbank abstrahierte Entwicklung von Datenbankanwendungen ermöglichen. DapperExtensions ist ein Beispiel dafür. 

Die Verwendung von DapperExtensions in der Anwendung ändert nichts daran, wie Datenbankverbindungen erstellt und verwaltet werden. Es liegt immer noch in der Verantwortung der Anwendung, Verbindungen zu öffnen, und die Erweiterungsmethoden erwarten reguläre SQL-Clientverbindungsobjekte. Wie oben beschrieben, können wir den [OpenConnectionForKey](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1) verwenden. Wie die folgenden Codebeispiele zeigen, besteht die einzige Änderung darin, dass keine T-SQL-Anweisungen mehr geschrieben werden müssen:

```csharp
    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                    key: tenantId2,
                    connectionString: connStrBldr.ConnectionString,
                    options: ConnectionOptions.Validate))
    {
           var blog = new Blog { Name = name2 };
           sqlconn.Insert(blog);
    }
```

Im Folgenden finden Sie das Codebeispiel für die Abfrage: 

```csharp
    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                    key: tenantId2,
                    connectionString: connStrBldr.ConnectionString,
                    options: ConnectionOptions.Validate))
    {
           // Display all Blogs for tenant 2
           IEnumerable<Blog> result = sqlconn.GetList<Blog>();
           Console.WriteLine("All blogs for tenant id {0}:", tenantId2);
           foreach (var item in result)
           {
               Console.WriteLine(item.Name);
           }
    }
```

### <a name="handling-transient-faults"></a>Behandeln vorübergehender Fehler
Das Microsoft Patterns & Practices-Team hat den [Transient Fault Handling Application Block](/previous-versions/msp-n-p/hh680934(v=pandp.50)) veröffentlicht, um Anwendungsentwicklern zu ermöglichen, häufig bei der Ausführung in der Cloud auftretende vorübergehende Fehler zu verringern. Weitere Informationen finden Sie unter [Hartnäckigkeit, das Geheimnis aller Erfolge: Verwenden des Anwendungsblocks zum Behandeln vorübergehender Fehler](/previous-versions/msp-n-p/dn440719(v=pandp.60)).

Zum Schutz vor vorübergehenden Fehlern verwendet das Codebeispiel die entsprechende TFH (Transient Fault Handling)-Bibliothek. 

```csharp
    SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() =>
    {
       using (SqlConnection sqlconn =
          shardingLayer.ShardMap.OpenConnectionForKey(tenantId2, connStrBldr.ConnectionString, ConnectionOptions.Validate))
          {
              var blog = new Blog { Name = name2 };
              sqlconn.Insert(blog);
          }
    });
```

Im Code oben ist **SqlDatabaseUtils.SqlRetryPolicy** als **SqlDatabaseTransientErrorDetectionStrategy** mit 10 Wiederholungen und einer Wartezeit von 5 Sekunden zwischen den Wiederholungen definiert. Bei Verwendung von Transaktionen sollten Sie sicherstellen, dass Ihr Wiederholungsbereich im Fall eines vorübergehenden Fehlers an den Anfang der Transaktion zurückkehrt.

## <a name="limitations"></a>Einschränkungen
Für die in diesem Dokument beschriebenen Vorgehensweisen gelten einige Einschränkungen:

* Im Beispielcode dieses Dokuments wird nicht auf die die shardübergreifende Schemaverwaltung eingegangen.
* Bei einer Anforderung wird davon ausgegangen, dass die damit verbundene Datenbankverarbeitung innerhalb eines einzelnen Shards erfolgt, der durch den in der Anforderung angegebenen Shardingschlüssel identifiziert wird. Diese Annahme trifft jedoch nicht immer zu, z. B. wenn kein Shardingschlüssel verfügbar gemacht werden kann. Um dieses Problem zu beheben, enthält die Clientbibliothek für elastische Datenbanken die [MultiShardQuery](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multishardexception)-Klasse. Die Klasse implementiert eine Verbindungsabstraktion für Abfragen über mehrere Shards. Die Verwendung von MultiShardQuery in Kombination mit Dapper geht über den Rahmen dieses Dokuments hinaus.

## <a name="conclusion"></a>Zusammenfassung
Anwendungen, die Dapper und DapperExtensions verwenden, können auch leicht von den Tools für elastische Datenbanken für Azure SQL-Datenbank profitieren. Mithilfe der in diesem Dokument beschriebenen Schritte können diese Anwendungen die Funktion des Tools für das datenabhängige Routing verwenden, indem sie das Erstellen und Öffnen neuer [SqlConnection](/dotnet/api/system.data.sqlclient.sqlconnection)-Objekte so ändern, dass der [OpenConnectionForKey](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)-Aufruf der Clientbibliothek für elastische Datenbanken verwendet wird. Dies beschränkt Änderungen in Anwendungen auf Situationen, in denen neue Verbindungen erstellt und geöffnet werden. 

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/elastic-scale-working-with-dapper/dapperimage1.png