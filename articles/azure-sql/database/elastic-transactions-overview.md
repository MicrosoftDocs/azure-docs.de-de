---
title: Verteilte Transaktionen über Clouddatenbanken (Vorschau)
description: Übersicht über Transaktionen für elastische Datenbanken mit Azure SQL-Datenbank und Azure SQL Managed Instance.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: 5504b9bc87f78682ff584006255d4e75e5e69fa7
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92793346"
---
# <a name="distributed-transactions-across-cloud-databases-preview"></a>Verteilte Transaktionen über Clouddatenbanken (Vorschau)
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Mithilfe elastischer Datenbanktransaktionen für Azure SQL-Datenbank und Azure SQL Managed Instance können Sie Transaktionen ausführen, die sich über mehrere Datenbanken erstrecken. Elastische Datenbanktransaktionen stehen für .NET-Anwendungen über ADO.NET zur Verfügung und lassen sich mithilfe der [System.Transaction](/dotnet/api/system.transactions)-Klassen in die vertraute Programmierumgebung integrieren. Informationen zum Abrufen der Bibliothek finden Sie unter [.NET Framework 4.6.1 (Webinstaller)](https://www.microsoft.com/download/details.aspx?id=49981).
Darüber hinaus sind verteilte Transaktionen für verwaltete Instanz in [Transact-SQL](/sql/t-sql/language-elements/begin-distributed-transaction-transact-sql) verfügbar.

Lokal muss für ein solches Szenario normalerweise MSDTC (Microsoft Distributed Transaction Coordinator) ausgeführt werden. Da MSDTC in Azure nicht als Platform-as-a-Service-Anwendung verfügbar ist, wurde die Koordinierung verteilter Transaktionen nun direkt in Azure SQL-Datenbank oder verwaltete SQL-Datenbank-Instanz integriert. Anwendungen können eine Verbindung mit einer beliebigen Datenbank herstellen, um verteilte Transaktionen zu starten, und eine der Datenbanken oder der Server koordiniert auf transparente Weise die verteilte Transaktion, wie in der folgenden Abbildung zu sehen.

In diesem Dokument werden die Begriffe „verteilte Transaktionen“ und „elastische Datenbanktransaktionen“ als Synonyme angesehen und austauschbar verwendet.

  ![Verteilte Transaktionen mit Azure SQL-Datenbank unter Verwendung elastischer Datenbanktransaktionen ][1]

## <a name="common-scenarios"></a>Häufige Szenarios

Mit elastischen Datenbanktransaktionen können Anwendungen unteilbare Änderungen an Daten vornehmen, die in mehreren verschiedenen Datenbanken gespeichert sind. Die Vorschau konzentriert sich auf clientseitige Entwicklungsoptionen in C# und .NET. Eine serverseitige Entwicklungsoption (Code, der in gespeicherten Prozeduren oder serverseitigen Skripts geschrieben ist), für die [Transact-SQL](/sql/t-sql/language-elements/begin-distributed-transaction-transact-sql) verwendet wird, ist nur für verwaltete SQL-Datenbank-Instanz verfügbar.
> [!IMPORTANT]
> In der Vorschau wird das Ausführen elastischer Datenbanktransaktionen zwischen Azure SQL-Datenbank und Azure SQL Managed Instance derzeit nicht unterstützt. Eine elastische Datenbanktransaktion kann sich nur über eine Gruppe von SQL-Datenbanken oder verwalteten SQL-Datenbank-Instanzen erstrecken.

Elastische Datenbanktransaktionen eignen sich für folgende Szenarien:

* Multidatenbankanwendungen in Azure: In diesem Szenario werden die Daten vertikal so auf mehrere Datenbanken in Azure SQL-Datenbank oder verwaltete SQL-Datenbank-Instanz verteilt, dass sich unterschiedliche Arten von Daten in unterschiedlichen Datenbanken befinden. Einige Vorgänge erfordern Änderungen an Daten in mehreren Datenbanken. Die Anwendung verwendet elastische Datenbanktransaktionen, um die datenbankübergreifenden Änderungen zu koordinieren und die Unteilbarkeit sicherzustellen.
* Partitionierte Datenbankanwendungen in Azure: In diesem Szenario werden die Daten mithilfe der [Clientbibliothek für elastische Datenbanken](elastic-database-client-library.md) oder mittels Self-Sharding horizontal über mehrere Datenbanken in Azure SQL-Datenbank oder verwaltete SQL-Datenbank-Instanz hinweg partitioniert. Ein naheliegender Anwendungsfall ist das Durchführen unteilbarer Änderungen für eine mehrinstanzenfähige Sharding-Anwendung, wenn die Änderungen mehrere Mandanten betreffen. Stellen Sie sich beispielsweise eine Übertragung zwischen zwei Mandanten in unterschiedlichen Datenbanken vor. Ein zweiter Fall wäre differenziertes Sharding zur Erfüllung der Kapazitätsanforderungen eines großen Mandanten. Dies impliziert in der Regel, dass sich einige unteilbare Vorgänge über mehrere, für den gleichen Mandanten verwendete Datenbanken erstrecken. Ein dritter Fall wären unteilbare Aktualisierungen für Referenzdaten mit datenbankübergreifender Replizierung. Unteilbare (transaktionsorientierte) Vorgänge dieser Art können jetzt mithilfe der Vorschau über mehrere Datenbanken hinweg koordiniert werden.
  Elastische Datenbanktransaktionen verwenden ein Zweiphasencommit, um die datenbankübergreifende Unteilbarkeit von Transaktionen zu gewährleisten. Dies eignet sich gut für Transaktionen mit einer Beteiligung von weniger als 100 Datenbanken pro einzelner Transaktion. Diese Limits werden nicht erzwungen. Eine Überschreitung beeinträchtigt jedoch die Leistung und Erfolgsraten von elastischen Datenbanktransaktionen.

## <a name="installation-and-migration"></a>Installation und Migration

Die Funktionen für elastische Datenbanktransaktionen werden mittels Aktualisierung der .NET-Bibliotheken „System.Data.dll“ und „System.Transactions.dll“ bereitgestellt. Die DLLs gewährleisten, dass bei Bedarf das Zweiphasencommit verwendet wird, um die Unteilbarkeit sicherzustellen. Installieren Sie [.NET 4.6.1](https://www.microsoft.com/download/details.aspx?id=49981) oder eine höhere Version von .NET Framework, um mit dem Entwickeln von Anwendungen mit elastischen Datenbanktransaktionen zu beginnen. Bei der Ausführung unter einer älteren Version von .NET Framework lassen sich Transaktionen nicht zu einer verteilten Transaktion höher stufen, was eine Ausnahme zur Folge hat.

Nach der Installation können Sie die APIs für verteilte Transaktionen in „System.Transactions“ für Verbindungen mit Azure SQL-Datenbank und verwaltete SQL-Datenbank-Instanz verwenden. Wenn Sie über MSDTC-Anwendungen verfügen, die diese APIs verwenden, erstellen Sie nach der Installation von .NET Framework 4.6.1 Ihre vorhandenen Anwendungen für .NET Framework 4.6 neu. Falls Ihre Projekte auf .NET Framework 4.6 ausgerichtet sind, verwenden sie automatisch die aktualisierten DLLs der neuen .NET Framework-Version. Aufrufe der API für verteilte Transaktion können in Kombination mit Verbindungen zu Azure SQL-Datenbank oder verwaltete SQL-Datenbank-Instanz erfolgreich ausgeführt werden.

Beachten Sie, dass für elastische Datenbanktransaktionen keine Installation von MSDTC erforderlich ist. Elastische Datenbanktransaktionen werden stattdessen direkt durch den und im Dienst verwaltet. Dies vereinfacht Cloudszenarien erheblich, da die Verwendung verteilter Transaktionen mit Azure SQL-Datenbank oder verwaltete SQL-Datenbank-Instanz keine MSDTC-Bereitstellung erfordert. Ausführlichere Informationen zum gemeinsamen Bereitstellen von elastischen Datenbanktransaktionen, .NET Framework und Ihren Cloudanwendungen in Azure finden Sie in Abschnitt 4.

## <a name="net-installation-for-azure-cloud-services"></a>Installation von .NET für Azure Cloud Services

Azure stellt verschiedene Angebote zum Hosten von .NET-Anwendungen bereit. Einen Vergleich der verschiedenen Angebote finden Sie unter [Azure App Service, Cloud Services und Virtual Machines im Vergleich](/azure/architecture/guide/technology-choices/compute-decision-tree). Wenn das Gastbetriebssystem des Angebots älter als .NET 4.6.1 ist (das für elastische Transaktionen erforderlich ist), müssen Sie das Gastbetriebssystem auf 4.6.1 aktualisieren.

Für Azure App Service werden Upgrades des Gastbetriebssystems derzeit nicht unterstützt. Melden Sie sich für Azure Virtual Machines einfach auf dem virtuellen Computer an, und führen Sie das Installationsprogramm für das neueste .NET Framework aus. Für Azure Cloud Services müssen Sie die Installation einer neueren Version von .NET in die Startaufgaben der Bereitstellung einschließen. Informationen zu den Konzepten und Schritten finden Sie unter [Installieren von .NET in einer Clouddienstrolle](../../cloud-services/cloud-services-dotnet-install-dotnet.md).  

Beachten Sie, dass das Installationsprogramm für .NET 4.6.1 während des Bootstrappingprozesses für Azure Cloud Services möglicherweise mehr temporären Speicherplatz benötigt als das für .NET 4.6. Um eine erfolgreiche Installation sicherzustellen, erhöhen Sie den temporären Speicher für Ihren Azure-Clouddienst in der Datei "ServiceDefinition.csdef" im Abschnitt "LocalResources". Ändern Sie außerdem die Umgebungseinstellungen der Startaufgabe wie im folgenden Beispiel gezeigt:

```xml
    <LocalResources>
    ...
        <LocalStorage name="TEMP" sizeInMB="5000" cleanOnRoleRecycle="false" />
        <LocalStorage name="TMP" sizeInMB="5000" cleanOnRoleRecycle="false" />
    </LocalResources>
    <Startup>
        <Task commandLine="install.cmd" executionContext="elevated" taskType="simple">
            <Environment>
        ...
                <Variable name="TEMP">
                    <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='TEMP']/@path" />
                </Variable>
                <Variable name="TMP">
                    <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='TMP']/@path" />
                </Variable>
            </Environment>
        </Task>
    </Startup>
```

## <a name="net-development-experience"></a>.NET-Entwicklung

### <a name="multi-database-applications"></a>Multidatenbankanwendungen

Der folgende Beispielcode verwendet die vertraute Programmierung mit „System.Transactions“ aus .NET. Die TransactionScope-Klasse erstellt eine Ambient-Transaktion in .NET. (Eine Ambient-Transaktion ist eine Transaktion aus dem aktuellen Thread.) Alle hergestellten Verbindungen innerhalb des Transaktionsbereichs sind an der Transaktion beteiligt. Bei Beteiligung verschiedener Datenbanken wird die Transaktion automatisch zu einer verteilten Transaktion höher gestuft. Zum Steuern des Transaktionsergebnisses wird der Bereich auf „Complete“ festgelegt, um einen Commit anzugeben.

```csharp
    using (var scope = new TransactionScope())
    {
        using (var conn1 = new SqlConnection(connStrDb1))
        {
            conn1.Open();
            SqlCommand cmd1 = conn1.CreateCommand();
            cmd1.CommandText = string.Format("insert into T1 values(1)");
            cmd1.ExecuteNonQuery();
        }

        using (var conn2 = new SqlConnection(connStrDb2))
        {
            conn2.Open();
            var cmd2 = conn2.CreateCommand();
            cmd2.CommandText = string.Format("insert into T2 values(2)");
            cmd2.ExecuteNonQuery();
        }

        scope.Complete();
    }
```

### <a name="sharded-database-applications"></a>Sharding-Datenbankanwendungen

Elastische Datenbanktransaktionen für Azure SQL-Datenbank und verwaltete SQL-Datenbank-Instanz unterstützen auch die Koordinierung verteilter Transaktionen, bei denen Sie mithilfe der OpenConnectionForKey-Methode der Clientbibliothek für elastische Datenbanken Verbindungen für eine horizontal hochskalierte Datenschicht herstellen. Beispiele wären etwa Fälle, in denen Sie bei Änderungen Transaktionskonsistenz über verschiedene Sharding-Schlüsselwerte hinweg sicherstellen müssen. Verbindungen mit den Shards, die die verschiedenen Sharding-Schlüsselwerte hosten, werden mithilfe von „OpenConnectionForKey“ vermittelt. Grundsätzlich können die Verbindungen mit unterschiedlichen Shards hergestellt werden, sodass zur Gewährleistung von Transaktionsgarantien eine verteilte Transaktion erforderlich ist.
Das folgende Codebeispiel veranschaulicht diese Vorgehensweise. Hierbei wird davon ausgegangen, dass eine Variable namens „shardmap“ eine Shardzuordnung aus der Clientbibliothek für elastische Datenbanken dargestellt:

```csharp
    using (var scope = new TransactionScope())
    {
        using (var conn1 = shardmap.OpenConnectionForKey(tenantId1, credentialsStr))
        {
            SqlCommand cmd1 = conn1.CreateCommand();
            cmd1.CommandText = string.Format("insert into T1 values(1)");
            cmd1.ExecuteNonQuery();
        }

        using (var conn2 = shardmap.OpenConnectionForKey(tenantId2, credentialsStr))
        {
            var cmd2 = conn2.CreateCommand();
            cmd2.CommandText = string.Format("insert into T1 values(2)");
            cmd2.ExecuteNonQuery();
        }

        scope.Complete();
    }
```

## <a name="transact-sql-development-experience"></a>Transact-SQL-Entwicklung

Serverseitige verteilte Transaktionen, in denen Transact-SQL verwendet wird, sind nur für Azure SQL Managed Instance verfügbar. Verteilte Transaktionen können nur zwischen verwalteten SQL-Datenbank-Instanzen ausgeführt werden, die zur selben [Serververtrauensstellungsgruppe](../managed-instance/server-trust-group-overview.md) gehören. In diesem Szenario müssen für verwaltete SQL-Datenbank-Instanzen [Verbindungsserver](/sql/relational-databases/linked-servers/create-linked-servers-sql-server-database-engine#TsqlProcedure) verwendet werden, um wechselseitig auf sich zu verweisen.

Im folgenden Transact-SQL-Beispielcode wird [BEGIN DISTRIBUTED TRANSACTION](/sql/t-sql/language-elements/begin-distributed-transaction-transact-sql) verwendet, um eine verteilte Transaktion zu starten.

```Transact-SQL

    -- Configure the Linked Server
    -- Add one Azure SQL Managed Instance as Linked Server
    EXEC sp_addlinkedserver
        @server='RemoteServer', -- Linked server name
        @srvproduct='',
        @provider='sqlncli', -- SQL Server Native Client
        @datasrc='managed-instance-server.46e7afd5bc81.database.windows.net' -- Managed Instance endpoint

    -- Add credentials and options to this Linked Server
    EXEC sp_addlinkedsrvlogin
        @rmtsrvname = 'RemoteServer', -- Linked server name
        @useself = 'false',
        @rmtuser = '<login_name>',         -- login
        @rmtpassword = '<secure_password>' -- password

    USE AdventureWorks2012;
    GO
    SET XACT_ABORT ON;
    GO
    BEGIN DISTRIBUTED TRANSACTION;
    -- Delete candidate from local instance.
    DELETE AdventureWorks2012.HumanResources.JobCandidate
        WHERE JobCandidateID = 13;
    -- Delete candidate from remote instance.
    DELETE RemoteServer.AdventureWorks2012.HumanResources.JobCandidate
        WHERE JobCandidateID = 13;
    COMMIT TRANSACTION;
    GO
```

## <a name="combining-net-and-transact-sql-development-experience"></a>Kombinieren von .NET- und Transact-SQL-Entwicklung

In .NET-Anwendungen, in denen System.Transaction-Klassen verwendet werden, kann die TransactionScope-Klasse mit der Transact-SQL-Anweisung BEGIN DISTRIBUTED TRANSACTION kombiniert werden. In TransactionScope wird eine interne Transaktion, in der BEGIN DITRIBUTED TRANSACTION ausgeführt wird, explizit zu einer verteilten Transaktion hochgestuft. Auch wenn die zweite Verbindung mit SqlConnection in der TransactionScope-Instanz geöffnet wird, wird diese implizit zu einer verteilten Transaktion hochgestuft. Sobald die verteilte Transaktion gestartet wurde, werden alle nachfolgenden Transaktionen, unabhängig davon, ob sie von .NET oder Transact-SQL stammen, mit der übergeordneten verteilten Transaktion verknüpft. Dies hat zur Folge, dass alle von der BEGIN-Anweisung initiierten geschachtelten Transaktionsbereiche in derselben Transaktion enden und COMMIT/ROLLBACK-Anweisungen sich wie folgt auf das Gesamtergebnis auswirken:
 * Eine COMMIT-Anweisung hat keine Auswirkung auf den Transaktionsbereich, der von einer BEGIN-Anweisung initiiert wurde, d. h., es werden keine Ergebnisse committet, bevor die Complete()-Methode für das TransactionScope-Objekt aufgerufen wurde. Wird das TransactionScope-Objekt zerstört, bevor es abgeschlossen war, wird für alle im Bereich vorgenommenen Änderungen ein Rollback ausgeführt.
 * Eine ROLLBACK-Anweisung bewirkt, dass für das gesamte TransactionScope-Objekt ein Rollback ausgeführt wird. Jeder Versuch, neue Transaktionen in das TransactionScope-Objekt einzutragen, schlägt danach ebenso fehl wie ein Versuch, Complete() für das TransactionScope-Objekt aufzurufen.

Es folgt ein Beispiel, in dem die Transaktion mit Transact-SQL explizit zu einer verteilten Transaktion hochgestuft wird.

```csharp
    using (TransactionScope s = new TransactionScope())
    {
        using (SqlConnection conn = new SqlConnection(DB0_ConnectionString)
        {
            conn.Open();
        
            // Transaction is here promoted to distributed by BEGIN statement
            //
            Helper.ExecuteNonQueryOnOpenConnection(conn, "BEGIN DISTRIBUTED TRAN");
            // ...
        }
     
        using (SqlConnection conn2 = new SqlConnection(DB1_ConnectionString)
        {
            conn2.Open();
            // ...
        }
        
        s.Complete();
    }
```

Das folgende Beispiel zeigt eine Transaktion, die implizit zu einer verteilten Transaktion hochgestuft wird, nachdem die zweite SqlConnection-Verbindung im TransactionScope-Objekt gestartet wurde.

```csharp
    using (TransactionScope s = new TransactionScope())
    {
        using (SqlConnection conn = new SqlConnection(DB0_ConnectionString)
        {
            conn.Open();
            // ...
        }
        
        using (SqlConnection conn = new SqlConnection(DB1_ConnectionString)
        {
            // Because this is second SqlConnection within TransactionScope transaction is here implicitly promoted distributed.
            //
            conn.Open(); 
            Helper.ExecuteNonQueryOnOpenConnection(conn, "BEGIN DISTRIBUTED TRAN");
            Helper.ExecuteNonQueryOnOpenConnection(conn, lsQuery);
            // ...
        }
        
        s.Complete();
    }
```

## <a name="transactions-across-multiple-servers-for-azure-sql-database"></a>Transaktionen über mehrere Server für Azure SQL-Datenbank

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Das PowerShell Azure Resource Manager-Modul wird von Azure SQL-Datenbank weiterhin unterstützt, aber alle zukünftigen Entwicklungen erfolgen für das Az.Sql-Modul. Informationen zu diesen Cmdlets finden Sie unter [AzureRM.Sql](/powershell/module/AzureRM.Sql/). Die Argumente für die Befehle im Az-Modul und den AzureRm-Modulen sind im Wesentlichen identisch.

Elastische Datenbanktransaktionen werden auf verschiedenen Servern in Azure SQL-Datenbank unterstützt. Bei Transaktionen über die Grenzen Server hinweg müssen die beteiligten Server zuerst in eine Beziehung mit gegenseitiger Kommunikation gebracht werden. Sobald die Kommunikationsbeziehung eingerichtet wurde, kann eine Datenbank in einem der beiden Server an elastischen Transaktionen mit Datenbanken auf dem anderen Server teilnehmen. Mit Transaktionen über mehr als zwei Server hinweg muss eine Kommunikationsbeziehung für jedes Serverpaar vorhanden sein.

Verwenden Sie die folgenden PowerShell-Cmdlets, um serverübergreifende Kommunikationsbeziehungen für elastische Transaktionen zu verwalten:

* **New-AzSqlServerCommunicationLink:** Mit diesem Cmdlet können Sie eine neue Kommunikationsbeziehung zwischen zwei Servern in Azure SQL-Datenbank erstellen. Die Beziehung ist symmetrisch, sodass beide Server Transaktionen mit dem jeweils anderen Server initiieren können.
* **Get-AzSqlServerCommunicationLink:** Mit diesem Cmdlet können Sie vorhandene Kommunikationsbeziehungen und die zugehörigen Eigenschaften abrufen.
* **Remove-AzSqlServerCommunicationLink:** Mit diesem Cmdlet können Sie eine vorhandene Kommunikationsbeziehung entfernen.

## <a name="transactions-across-multiple-servers-for-azure-sql-managed-instance"></a>Transaktionen über mehrere Server für Azure SQL Managed Instance

Verteilte Transaktionen werden auf verschiedenen Servern in Azure SQL Managed Instance unterstützt. Überschreiten Transaktionen Azure SQL Managed Instance-Grenzen, müssen die beteiligten Instanzen zunächst in eine gegenseitige Sicherheits- und Kommunikationsbeziehung gebracht werden. Dies erfolgt, indem im Azure-Portal eine [Serververtrauensstellungsgruppe](../managed-instance/server-trust-group-overview.md) erstellt wird. Wenn sich verwaltete Instanzen nicht im selben virtuellen Netzwerk befinden, muss [Peering virtueller Netzwerke](../../virtual-network/virtual-network-peering-overview.md) eingerichtet werden, und in den Eingangs- und Ausgangsregeln der Netzwerksicherheitsgruppe müssen die Ports 5024 und 11000 bis 12000 in allen beteiligten virtuellen Netzwerken geöffnet sein.

  ![Serververtrauensstellungsgruppe im Azure-Portal][3]

Das folgende Diagramm zeigt die Serververtrauensstellungsgruppe mit verwalteten Instanzen, die verteilte Transaktionen über .NET oder Transact-SQL ausführen können.

  ![Verteilte Transaktionen mit Azure SQL Managed Instance, wozu elastische Transaktionen verwendet werden][2]

## <a name="monitoring-transaction-status"></a>Überwachen des Transaktionsstatus

Verwenden Sie DMVs (Dynamic Management Views), um Status und Fortschritt laufender elastischer Datenbanktransaktionen zu überwachen. Für verteilte Transaktionen in Azure SQL-Datenbank und Azure SQL Managed Instance sind alle transaktionsbezogenen DMVs relevant. Die entsprechende DMV-Liste finden Sie hier: [Dynamische Verwaltungssichten für Transaktionen und Funktionen (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/transaction-related-dynamic-management-views-and-functions-transact-sql).

Folgende DMVs sind besonders hilfreich:

* **sys.dm\_tran\_active\_transactions** : Führt die derzeit aktiven Transaktionen und deren Status auf. Die UOW-Spalte (Arbeitseinheit) gibt die verschiedenen untergeordneten Transaktionen an, die zur gleichen verteilten Transaktion gehören. Alle Transaktionen innerhalb einer verteilten Transaktion haben den gleichen UOW-Wert. Weitere Informationen finden Sie in der [DMV-Dokumentation](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-active-transactions-transact-sql).
* **sys.dm\_tran\_database\_transactions** : Liefert zusätzliche Informationen zu Transaktionen (wie etwa die Position der Transaktion im Protokoll). Weitere Informationen finden Sie in der [DMV-Dokumentation](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-database-transactions-transact-sql).
* **sys.dm\_tran\_locks** : Enthält Informationen zu den Sperren, die derzeit für laufende Transaktionen aktiv sind. Weitere Informationen finden Sie in der [DMV-Dokumentation](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-locks-transact-sql).

## <a name="limitations"></a>Einschränkungen

Derzeit gelten für elastische Datenbanktransaktionen in Azure SQL-Datenbank folgende Einschränkungen:

* Es werden nur datenbankübergreifende Transaktionen in Azure SQL-Datenbank unterstützt. Andere Ressourcenanbieter für [X/Open XA](https://en.wikipedia.org/wiki/X/Open_XA) sowie Datenbanken außerhalb von Azure SQL-Datenbank können nicht an elastischen Datenbanktransaktionen beteiligt werden. Das bedeutet, dass sich elastische Datenbanktransaktionen nicht über lokale SQL Server- und Azure SQL-Datenbanken erstrecken können. Für lokale verteilte Transaktionen muss weiterhin MSDTC verwendet werden.
* Nur clientkoordinierte Transaktionen einer .NET-Anwendung werden unterstützt. Die serverseitige Unterstützung für T-SQL (etwa „BEGIN DISTRIBUTED TRANSACTION“) ist zwar geplant, aber noch nicht verfügbar.
* Transaktionen über WCF-Dienste werden nicht unterstützt. Nehmen wir beispielsweise eine WCF-Dienstmethode, die eine Transaktion ausführt. Das Umschließen des Aufrufs innerhalb eines Transaktionsbereichs schlägt als [System.ServiceModel.ProtocolException](/dotnet/api/system.servicemodel.protocolexception)fehl.

Derzeit gelten für verteilte Transaktionen in Azure SQL Managed Instance die folgenden Einschränkungen:

* Datenbankübergreifende Transaktionen werden nur in Azure SQL Managed Instance unterstützt. Andere [X/Open XA](https://en.wikipedia.org/wiki/X/Open_XA)-Ressourcenanbieter und -Datenbanken außerhalb von Azure SQL Managed Instance können nicht an verteilten Transaktionen beteiligt werden. Das bedeutet, dass sich verteilte Transaktionen nicht über lokale SQL Server-Datenbanken und Azure SQL Managed Instance erstrecken können. Für lokale verteilte Transaktionen muss weiterhin MSDTC verwendet werden.
* Transaktionen über WCF-Dienste werden nicht unterstützt. Nehmen wir beispielsweise eine WCF-Dienstmethode, die eine Transaktion ausführt. Das Umschließen des Aufrufs innerhalb eines Transaktionsbereichs schlägt als [System.ServiceModel.ProtocolException](/dotnet/api/system.servicemodel.protocolexception)fehl.
* Azure SQL Managed Instance muss Bestandteil einer [Serververtrauensstellungsgruppe](../managed-instance/server-trust-group-overview.md) sein, um an einer verteilten Transaktion teilnehmen zu können.
* Einschränkungen von [Serververtrauensstellungsgruppen](../managed-instance/server-trust-group-overview.md) wirken sich auf verteilte Transaktionen aus.
* Azure SQL Managed Instances, die an verteilten Transaktionen teilnehmen, müssen über private Endpunkte (mit einer privaten IP-Adresse des virtuellen Netzwerks, in dem sie bereitgestellt werden) verbunden sein und müssen über private vollqualifizierte Domänennamen aufeinander verweisen. Clientanwendungen können verteilte Transaktionen auf privaten Endpunkten verwenden. Außerdem können Clientanwendungen auch verteilte Transaktionen auf öffentlichen Endpunkten verwenden, wenn in Transact-SQL Verbindungsserver genutzt werden, die auf private Endpunkte verweisen. Diese Einschränkung ist im folgenden Diagramm erläutert.
  ![Verbindungseinschränkung für private Endpunkte][4]
## <a name="next-steps"></a>Nächste Schritte

* Fragen können Sie über die [Microsoft F&A-Seite für SQL-Datenbank](/answers/topics/azure-sql-database.html) an uns richten.
* Wenn Sie Featurevorschläge haben, fügen Sie diese im [SQL-Datenbank-Feedbackforum](https://feedback.azure.com/forums/217321-sql-database/) oder im [SQL Managed Instance-Forum](https://feedback.azure.com/forums/915676-sql-managed-instance) hinzu.



<!--Image references-->
[1]: ./media/elastic-transactions-overview/distributed-transactions.png
[2]: ./media/elastic-transactions-overview/sql-mi-distributed-transactions.png
[3]: ./media/elastic-transactions-overview/server-trust-groups-azure-portal.png
[4]: ./media/elastic-transactions-overview/managed-instance-distributed-transactions-private-endpoint-limitations.png
