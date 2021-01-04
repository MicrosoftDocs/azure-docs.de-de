---
title: Transaktionsreplikation
titleSuffix: Azure SQL Managed Instance
description: Erfahren Sie mehr über die Verwendung der SQL Server-Transaktionsreplikation mit Azure SQL Managed Instance (Vorschau).
services: sql-database
ms.service: sql-managed-instance
ms.subservice: data-movement
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sstein
ms.date: 04/20/2020
ms.openlocfilehash: 76bb4ffb4ebeb01baf8236d6be84c900b23ffbc0
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92790813"
---
# <a name="transactional-replication-with-azure-sql-managed-instance-preview"></a>Transaktionsreplikation mit Azure SQL Managed Instance (Vorschau)
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Transaktionsreplikation ist ein Feature von Azure SQL Managed Instance und SQL Server, das Ihnen die Replikation von Daten aus einer Tabelle in Azure SQL Managed Instance oder einer SQL Server-Instanz in Tabellen ermöglicht, die in Remotedatenbanken abgelegt sind. Mit diesem Feature können Sie mehrere Tabellen in unterschiedlichen Datenbanken synchronisieren. 

Die Transaktionsreplikation ist zurzeit als öffentliche Vorschau für SQL Managed Instance verfügbar. 

## <a name="overview"></a>Übersicht

Sie können Transaktionsreplikation auch verwenden, um Änderungen an Azure SQL Managed Instance mithilfe von Push an folgende Ziele zu übertragen:

- Eine SQL Server-Datenbank, entweder lokal oder in Azure-VM
- Eine Datenbank in Azure SQL-Datenbank
- Eine Instanzdatenbank in Azure SQL Managed Instance

  > [!NOTE]
  > Um alle Features von Azure SQL Managed Instance verwenden zu können, müssen Sie die neuesten Versionen von [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) und [SQL Server Data Tools (SSDT)](/sql/ssdt/download-sql-server-data-tools-ssdt) verwenden.

### <a name="components"></a>Komponenten

Die wichtigsten Komponenten der Transaktionsreplikation ( **Verleger** , **Verteiler** und **Abonnent** ) sind in der folgenden Abbildung dargestellt:  

![Replikation zu SQL-Datenbank](./media/replication-transactional-overview/replication-to-sql-database.png)

| Role | Azure SQL-Datenbank | Verwaltete Azure SQL-Instanz |
| :----| :------------- | :--------------- |
| **Herausgeber** | Nein | Ja |
| **Verteiler** | Nein | Ja|
| **Pull-Abonnent** | Nein | Ja|
| **Push-Abonnent**| Ja | Ja|
| &nbsp; | &nbsp; | &nbsp; |

Der **Verleger** veröffentlicht an Tabellen (Artikeln) erfolgte Änderungen, indem die Aktualisierungen zum Verteiler gesendet werden. Der Verleger kann Azure SQL Managed Instance oder eine SQL Server-Instanz sein.

Der **Verteiler** sammelt Änderungen an den Artikeln von einem Verleger und verteilt sie an die Abonnenten. Der Verteiler kann entweder eine Azure SQL Managed Instance oder eine SQL Server-Instanz (beliebige Version, sofern nicht älter als die Verlegerversion) sein.

Der **Abonnent** empfängt auf dem Verleger erfolgte Änderungen. Eine SQL Server-Instanz und Azure SQL Managed Instance können sowohl Push- als auch Pullabonnenten sein. Ein Pullabonnement wird jedoch nicht unterstützt, wenn der Verteiler eine Azure SQL Managed Instance ist und der Abonnent nicht. Eine Datenbank in Azure SQL-Datenbank kann nur ein Pushabonnent sein.

Azure SQL Managed Instance kann als Abonnent der folgenden Versionen von SQL Server fungieren:

- SQL Server 2016 und höher
- SQL Server 2014 [RTM CU10 (12.0.4427.24)](https://support.microsoft.com/help/3094220/cumulative-update-10-for-sql-server-2014) oder [SP1 CU3 (12.0.2556.4)](https://support.microsoft.com/help/3094221/cumulative-update-3-for-sql-server-2014-service-pack-1)
- SQL Server 2012 [SP2 CU8 (11.0.5634.1)](https://support.microsoft.com/help/3082561/cumulative-update-8-for-sql-server-2012-sp2) oder [SP3 (11.0.6020.0)](https://www.microsoft.com/download/details.aspx?id=49996)

   > [!NOTE]
   >
   > - Für andere Versionen von SQL Server, die keine Veröffentlichung in Objekten in Azure unterstützen, kann die Methode der [erneuten Veröffentlichung von Daten](/sql/relational-databases/replication/republish-data) verwendet werden, um Daten in neuere Versionen von SQL Server verschieben.
   > - Der Versuch, Replikationen mit einer älteren Version zu konfigurieren, kann zu dem Fehler mit der Nummer MSSQL_REPL20084 (Der Prozess konnte keine Verbindung mit dem Abonnenten herstellen.) oder MSSQ_REPL40532 (Der von der Anmeldung angeforderte Server \<name> kann nicht geöffnet werden. Die Anmeldung ist fehlgeschlagen.)

### <a name="types-of-replication"></a>Replikationstypen

Es gibt verschiedene [Replikationstypen](/sql/relational-databases/replication/types-of-replication):

| Replikation | Azure SQL-Datenbank | Verwaltete Azure SQL-Instanz |
| :----| :------------- | :--------------- |
| [**Transaktionsreplikation**](/sql/relational-databases/replication/transactional/transactional-replication) | Ja (nur als Abonnent) | Ja |
| [**Momentaufnahme**](/sql/relational-databases/replication/snapshot-replication) | Ja (nur als Abonnent) | Ja|
| [**Mergereplikation**](/sql/relational-databases/replication/merge/merge-replication) | Nein | Nein|
| [**Peer-to-Peer**](/sql/relational-databases/replication/transactional/peer-to-peer-transactional-replication) | Nein | Nein|
| [**Bidirektional**](/sql/relational-databases/replication/transactional/bidirectional-transactional-replication) | Nein | Ja|
| [**Aktualisierbare Abonnements**](/sql/relational-databases/replication/transactional/updatable-subscriptions-for-transactional-replication) | Nein | Nein|
| &nbsp; | &nbsp; | &nbsp; |

### <a name="supportability-matrix"></a>Unterstützungsmatrix

  Die Unterstützungsmatrix für die Transaktionsreplikation für Azure SQL Managed Instance ist identisch mit der für SQL Server.
  
| **Herausgeber**   | **Verteiler** | **Abonnent** |
| :------------   | :-------------- | :------------- |
| SQL Server 2019 | SQL Server 2019 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/>  |
| SQL Server 2017 | SQL Server 2019 <br/>SQL Server 2017 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 |
| SQL Server 2016 | SQL Server 2019 <br/>SQL Server 2017 <br/> SQL Server 2016 | SQL Server 2019 <br/> SQL Server 2017 <br/>SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 |
| SQL Server 2014 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>| SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 |
| SQL Server 2012 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>SQL Server 2012 <br/> | SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 |
| SQL Server 2008 R2 <br/> SQL Server 2008 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 |  SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 <br/>  |
| &nbsp; | &nbsp; | &nbsp; |

## <a name="when-to-use"></a>Verwendung

Die Transaktionsreplikation ist in den folgenden Szenarien nützlich:

- Veröffentlichen von Änderungen an Tabellen in einer Datenbank, die an eine oder mehrere Datenbanken in einer SQL Server-Instanz oder eine Azure SQL-Datenbank-Instanz verteilt werden, die die Änderungen abonniert haben
- Synchronisierthalten mehrerer verteilter Datenbanken
- Migrieren von Datenbanken aus einer SQL Server-Instanz oder Azure SQL Managed Instance in eine andere Datenbank durch fortlaufendes Veröffentlichen der Änderungen

### <a name="compare-data-sync-with-transactional-replication"></a>Vergleichen von Datensynchronisierung und Transaktionsreplikation

| Category | Datensynchronisierung | Transaktionsreplikation |
|---|---|---|
| Vorteile | – Aktiv/Aktiv-Unterstützung<br/>– Bidirektional zwischen lokaler und Azure SQL-Datenbank | – Niedrigere Latenzzeiten<br/>– Transaktionskonsistenz<br/>– Wiederverwendung vorhandener Topologie nach der Migration |
| Nachteile | – Latenzzeiten von 5 Minuten und mehr<br/>– Keine Transaktionskonsistenz<br/>– Größere Auswirkung auf die Leistung | – Keine Veröffentlichung aus Azure SQL-Datenbank <br/>– Hohe Wartungskosten |

## <a name="common-configurations"></a>Häufig verwendete Konfigurationen

Im Allgemeinen müssen sich Verleger und Verteiler gemeinsam entweder in der Cloud oder an einem lokalen Standort befinden. Die folgenden Konfigurationen werden unterstützt:

### <a name="publisher-with-local-distributor-on-sql-managed-instance"></a>Verleger mit lokalem Verteiler in SQL Managed Instance

![Einzelinstanz als Verleger und Verteiler](./media/replication-transactional-overview/01-single-instance-asdbmi-pubdist.png)

Verleger und Verteiler sind in einer einzelnen SQL Managed Instance konfiguriert und verteilen Änderungen an eine andere SQL Managed Instance, SQL-Datenbank- oder SQL Server-Instanz.

### <a name="publisher-with-remote-distributor-on-sql-managed-instance"></a>Verleger mit Remoteverteiler in SQL Managed Instance

Bei dieser Konfiguration veröffentlicht eine verwaltete Instanz Änderungen in einem Verteiler in einer anderen SQL Managed Instance, die zahlreichen SQL Managed Instances als Quelle dienen und Änderungen an ein oder mehrere Ziele in Azure SQL-Datenbank, Azure SQL Managed Instance oder SQL Server verteilen kann.

![Separate Instanzen als Verleger und Verteiler](./media/replication-transactional-overview/02-separate-instances-asdbmi-pubdist.png)

Verleger und Verteiler werden in zwei verwalteten Instanzen konfiguriert. Bei dieser Konfiguration gibt es einige Einschränkungen:

- Beide verwaltete Instanzen befinden sich im gleichen VNET.
- Die beiden verwalteten Instanzen befinden sich am gleichen Standort.

### <a name="on-premises-publisherdistributor-with-remote-subscriber"></a>Lokaler Verleger/Verteiler mit Remoteabonnent

![Azure SQL-Datenbank als Abonnent](./media/replication-transactional-overview/03-azure-sql-db-subscriber.png)

Bei dieser Konfiguration ist eine Datenbank in Azure SQL-Datenbank oder Azure SQL Managed Instance ein Abonnent. Diese Konfiguration unterstützt die Migration vom lokalen Standort zu Azure. Für einen Abonnenten in einer Datenbank in Azure SQL-Datenbank muss der Pushmodus aktiviert sein.  

## <a name="requirements"></a>Requirements (Anforderungen)

- Für die Verbindung zwischen Teilnehmern an der Replikation wird SQL-Authentifizierung verwendet.
- Nutzen Sie für das von der Replikation verwendete Arbeitsverzeichnis eine Azure Storage-Kontofreigabe.
- Öffnen Sie den ausgehenden TCP-Port 445 in den Sicherheitsregeln des Subnetzes, um auf die Azure-Dateifreigabe zuzugreifen.
- Öffnen Sie den ausgehenden TCP-Port 1433, wenn sich der Verleger/Verteiler in SQL Managed Instance befinden und der Abonnent nicht. Möglicherweise müssen Sie auch die Sicherheitsregel für ausgehenden Datenverkehr der Netzwerksicherheitsgruppe von SQL Managed Instance für `allow_linkedserver_outbound` für das **Zieldiensttag** von Port 1433 von `virtualnetwork` in `internet` ändern.
- Platzieren Sie sowohl den Verleger als auch den VerteVerlegeriler in der Cloud oder beide lokal.
- Konfigurieren Sie VPN-Peering zwischen den virtuellen Netzwerken der Replikationsteilnehmer, sofern die virtuellen Netzwerke unterschiedlich sind.

> [!NOTE]
> Möglicherweise tritt beim Herstellen einer Verbindung mit einer Azure Storage-Datei der Fehler 53 auf, wenn Port 445 (ausgehend) der Netzwerksicherheitsgruppe gesperrt ist und der Verteiler eine Azure SQL Managed Instance-Datenbank und der Abonnent ein lokales System ist. [Aktualisieren Sie die vNet-Netzwerksicherheitsgruppe](../../storage/files/storage-troubleshoot-windows-file-connection-problems.md), um dieses Problem zu beheben.

## <a name="with-failover-groups"></a>Mit Failovergruppen

[Aktive Georeplikation](../database/active-geo-replication-overview.md) wird bei einer SQL Managed Instance, die Transaktionsreplikation nutzt, nicht unterstützt. Verwenden Sie anstelle der aktiven Georeplikation [Autofailover-Gruppen](../database/auto-failover-group-overview.md). Beachten Sie jedoch, dass die Veröffentlichung aus der primären verwalteten Instanz [manuell gelöscht](transact-sql-tsql-differences-sql-server.md#replication) und nach dem Failover in der sekundären SQL Managed Instance neu erstellt werden muss.

Wenn Georeplikation für eine als **Verleger** oder **Verteiler** fungierende SQL Managed Instance in einer [Failovergruppe](../database/auto-failover-group-overview.md) aktiviert ist, muss der SQL Managed Instance-Administrator alle Veröffentlichungen für die alte primäre Instanz bereinigen und nach einem Failover für die neue primäre Instanz erneut konfigurieren. Die folgenden Aktivitäten sind in diesem Szenario erforderlich:

1. Beenden aller Replikationsaufträge, die für die Datenbank ausgeführt werden, sofern vorhanden.
1. Löschen der Abonnementmetadaten vom Verleger, indem das folgende Skript für die Verlegerdatenbank ausgeführt wird:

   ```sql
   EXEC sp_dropsubscription @publication='<name of publication>', @article='all',@subscriber='<name of subscriber>'
   ```

1. Löschen von Abonnementmetadaten aus dem Abonnenten. Wenden Sie das folgende Skript auf die Abonnementdatenbank der SQL Managed Instance des Abonnenten an:

   ```sql
   EXEC sp_subscription_cleanup
      @publisher = N'<full DNS of publisher, e.g. example.ac2d23028af5.database.windows.net>',
      @publisher_db = N'<publisher database>',
      @publication = N'<name of publication>';
   ```

1. Erzwingen Sie das Löschen aller Replikationsobjekte aus dem Verleger, indem Sie das folgende Skript in der veröffentlichten Datenbank ausführen:

   ```sql
   EXEC sp_removedbreplication
   ```

1. Erzwingen Sie das Löschen des alten Verteilers aus der ursprünglichen primären SQL Managed Instance (bei einem Failback auf eine alte primäre Instanz, die einen Verteiler hatte). Wenden Sie das folgende Skript auf die Masterdatenbank in der alten als Verteiler fungierenden SQL Managed Instance an:

   ```sql
   EXEC sp_dropdistributor 1,1
   ```

Wenn Georeplikation für eine **Abonnenteninstanz** in einer Failovergruppe aktiviert ist, sollte die Veröffentlichung so konfiguriert werden, dass eine Verbindung mit dem Failovergruppen-Listenerendpunkt für die von einem Abonnenten verwaltete Instanz hergestellt wird. Im Fall eines Failovers hängt die nachfolgende Aktion durch den Administrator der verwalteten Instanz vom Failovertyp ab, der aufgetreten ist:

- Bei einem Failover ohne Datenverlust wird die Replikation nach einem Failover fortgesetzt.
- Bei einem Failover mit Datenverlust funktioniert die Replikation ebenfalls. Die verlorenen Änderungen werden dann erneut repliziert.
- Für ein Failover mit Datenverlust, bei dem der Datenverlust aber außerhalb des Aufbewahrungszeitraums der Verteilungsdatenbank liegt, muss der Administrator der SQL Managed Instance die Abonnementdatenbank erneut initialisieren.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Konfigurieren von Transaktionsreplikation finden Sie in den folgenden Tutorials:

- [Konfigurieren der Replikation zwischen einem SQL Managed Instance-Verleger und -Abonnenten](../managed-instance/replication-between-two-instances-configure-tutorial.md)
- [Konfigurieren der Replikation zwischen einem SQL Managed Instance-Verleger, SQL Managed Instance-Verteiler und SQL Server-Abonnenten](../managed-instance/replication-two-instances-and-sql-server-configure-tutorial.md)
- [Erstellen Sie eine Veröffentlichung](/sql/relational-databases/replication/publish/create-a-publication).
- [Erstellen Sie ein Pushabonnement](/sql/relational-databases/replication/create-a-push-subscription) mit dem Servernamen als Abonnent (z. B. `N'azuresqldbdns.database.windows.net` und dem Namen der Datenbank in Azure SQL-Datenbank als Zieldatenbank, z. B. **AdventureWorks** . )

## <a name="see-also"></a>Weitere Informationen  

- [Replikation mit einer SQL Managed Instance und einer Failovergruppe](transact-sql-tsql-differences-sql-server.md#replication)
- [Replikation zu SQL-Datenbank-Einzeldatenbanken und in einem Pool zusammengefassten Datenbanken](../database/replication-to-sql-database.md)
- [Konfigurieren der Replikation in einer verwalteten Azure SQL-Datenbank-Instanzdatenbank](../managed-instance/replication-between-two-instances-configure-tutorial.md)
- [Erstellen einer Veröffentlichung](/sql/relational-databases/replication/publish/create-a-publication)
- [Erstellen eines Pushabonnements](/sql/relational-databases/replication/create-a-push-subscription/)
- [Replikationstypen](/sql/relational-databases/replication/types-of-replication)
- [Überwachen (Replikation)](/sql/relational-databases/replication/monitor/monitoring-replication)
- [Initialisieren eines Abonnements](/sql/relational-databases/replication/initialize-a-subscription)