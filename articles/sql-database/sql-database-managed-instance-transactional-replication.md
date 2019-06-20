---
title: Transaktionsreplikation mit Azure SQL-Datenbank | Microsoft-Dokumentation
description: Erfahren Sie mehr über die Verwendung der SQL Server-Transaktionsreplikation mit Einzeldatenbanken, Pooldatenbanken und Instanzdatenbanken in Azure SQL-Datenbank.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
manager: craigg
ms.date: 02/08/2019
ms.openlocfilehash: c8d5df0f83a7ae37b9f06a5e255e9809288b6d67
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64917031"
---
# <a name="transactional-replication-with-single-pooled-and-instance-databases-in-azure-sql-database"></a>Transaktionsreplikation mit Einzeldatenbanken, Pooldatenbanken und Instanzdatenbanken in Azure SQL-Datenbank

Die Transaktionsreplikation ist ein Feature von Azure SQL-Datenbank und SQL Server, die Ihnen die Replikation von Daten aus einer Tabelle in Azure SQL-Datenbank oder einem SQL-Server zu den in Remotedatenbanken platzierten Tabellen ermöglicht. Mit diesem Feature können Sie mehrere Tabellen in unterschiedlichen Datenbanken synchronisieren.

## <a name="when-to-use-transactional-replication"></a>Wann ist die Transaktionsreplikation zu verwenden?

Die Transaktionsreplikation ist in den folgenden Szenarien nützlich:
- Veröffentlichen von Änderungen an Tabellen in einer Datenbank, die an SQL Server-Instanzen oder Azure SQL-Datenbanken verteilt werden, die die Änderungen abonniert haben
- Synchronisierthalten mehrerer verteilter Datenbanken
- Migrieren von Datenbanken aus einer SQL Server- oder einer verwalteten Instanz zu einer anderen Datenbank durch fortlaufendes Veröffentlichen der Änderungen

## <a name="overview"></a>Übersicht

Die wichtigsten Komponenten der Transaktionsreplikation sind in der folgenden Abbildung dargestellt:  

![Replikation zu SQL-Datenbank](media/replication-to-sql-database/replication-to-sql-database.png)

Der **Herausgeber** ist eine Instanz oder ein Server, die bzw. der Änderungen an Tabellen (Artikeln) veröffentlicht, indem die Aktualisierungen an den Verteiler gesendet werden. Das Veröffentlichen in einer Azure SQL-Datenbank von einem lokalen SQL Server wird durch die folgenden Versionen von SQL Server unterstützt:

- SQL Server 2019 (Vorschauversion)
- SQL Server 2016 bis SQL 2017
- SQL Server 2014 SP1 CU3 oder höher (12.00.4427)
- SQL Server 2014 RTM CU10 (12.00.2556)
- SQL Server 2012 SP3 oder höher (11.0.6020)
- SQL Server 2012 SP2 CU8 (11.0.5634.0)
- Für andere Versionen von SQL Server, die keine Veröffentlichung in Objekten in Azure unterstützen, kann die Methode der [erneuten Veröffentlichung von Daten](https://docs.microsoft.com/sql/relational-databases/replication/republish-data) verwendet werden, um Daten in neuere Versionen von SQL Server verschieben. 

Der **Verteiler** ist eine Instanz oder ein Server, die bzw. der Änderungen an den Artikeln von einem Herausgeber erfasst und an die Abonnenten verteilt. Der Verteiler kann entweder eine verwaltete Azure SQL-Datenbank-Instanz oder eine SQL Server-Instanz (beliebige Version, sofern nicht älter als die Herausgeber-Version) sein. 

Der **Abonnent** ist eine Instanz oder ein Server, die bzw. der die auf dem Herausgeber vorgenommenen Änderungen erhält. Abonnenten können Einzeldatenbanken, Pooldatenbanken und Instanzdatenbanken in Azure SQL-Datenbank- oder SQL Server-Datenbanken sein. Ein Abonnent einer Einzel- oder Pooldatenbank muss als Push-Abonnent konfiguriert sein. 

| Rolle | Einzel- und Pooldatenbanken | Instanzdatenbanken |
| :----| :------------- | :--------------- |
| **Herausgeber** | Nein | Ja | 
| **Verteiler** | Nein | Ja|
| **Pull-Abonnent** | Nein | Ja|
| **Push-Abonnent**| Ja | Ja|
| &nbsp; | &nbsp; | &nbsp; |

  >[!NOTE]
  > Ein Pull-Abonnement wird nicht unterstützt, wenn der Verteiler eine Instanzdatenbank und der Abonnent keine Instanzdatenbank ist. 

Es gibt verschiedene [Replikationstypen](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication):


| Replikation | Einzel- und Pooldatenbanken | Instanzdatenbanken|
| :----| :------------- | :--------------- |
| [**Transaktion**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/transactional-replication) | Ja (nur als Abonnent) | Ja | 
| [**Momentaufnahme**](https://docs.microsoft.com/sql/relational-databases/replication/snapshot-replication) | Ja (nur als Abonnent) | Ja|
| [**Mergereplikation**](https://docs.microsoft.com/sql/relational-databases/replication/merge/merge-replication) | Nein | Nein|
| [**Peer-to-Peer**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/peer-to-peer-transactional-replication) | Nein | Nein|
| **Unidirektional** | Ja | Ja|
| [**Bidirektional**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/bidirectional-transactional-replication) | Nein | Ja|
| [**Aktualisierbare Abonnements**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/updatable-subscriptions-for-transactional-replication) | Nein | Nein|
| &nbsp; | &nbsp; | &nbsp; |

  >[!NOTE]
  > - Der Versuch, Replikationen mit einer älteren Version zu konfigurieren, kann zu dem Fehler mit der Nummer MSSQL_REPL20084 (Der Prozess konnte keine Verbindung mit dem Abonnenten herstellen.) oder MSSQ_REPL40532 (Der von der Anmeldung angeforderte Server \<Name> kann nicht geöffnet werden) führen. Die Anmeldung ist fehlgeschlagen.)
  > - Um alle Features von Azure SQL-Datenbank verwenden zu können, müssen Sie die neuesten Versionen von [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) und [SQL Server Data Tools (SSDT)](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt) verwenden.
  
  ### <a name="supportability-matrix-for-instance-databases-and-on-premises-systems"></a>Matrix für die Unterstützbarkeit von Instanzdatenbanken und lokalen Systemen
  Die Unterstützungsmatrix für die Replikation für Instanzdatenbanken ist identisch mit der für eine lokale SQL Server-Instanz. 
  
  | **Herausgeber**   | **Verteiler** | **Abonnent** |
| :------------   | :-------------- | :------------- |
| SQL Server 2017 | SQL Server 2017 | SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 |
| SQL Server 2016 | SQL Server 2017 <br/> SQL Server 2016 | SQL Server 2017 <br/>SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 |
| SQL Server 2014 | SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>| SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 |
| SQL Server 2012 | SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>SQL Server 2012 <br/> | SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 | 
| SQL Server 2008 R2 <br/> SQL Server 2008 | SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 | SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 <br/>  |
| &nbsp; | &nbsp; | &nbsp; |

## <a name="requirements"></a>Requirements (Anforderungen)

- Für die Verbindung zwischen den Teilnehmern der Replikation wird SQL-Authentifizierung verwendet. 
- Ein Azure-Speicherkonto für das von der Replikation verwendete Arbeitsverzeichnis. 
- Port 445 (TCP ausgehend) muss in den Sicherheitsregeln des Subnetzes der verwalteten Instanz geöffnet sein, um auf die Azure-Dateifreigabe zugreifen zu können. 
- Port 1433 (TCP ausgehend) muss geöffnet werden, wenn sich der Herausgeber/Verteiler auf einer verwalteten Instanz und der Abonnent in der lokalen Umgebung befindet.

  >[!NOTE]
  > Möglicherweise tritt beim Herstellen einer Verbindung zu einer Azure Storage-Datei der Fehler 53 auf, wenn Port 445 (ausgehend) der Netzwerksicherheitsgruppe gesperrt ist und der Verteiler eine Instanzdatenbank und der Abonnent ein lokales System ist. [Aktualisieren Sie die vNet-Netzwerksicherheitsgruppe](/azure/storage/files/storage-troubleshoot-windows-file-connection-problems), um dieses Problem zu beheben. 

### <a name="compare-data-sync-with-transactional-replication"></a>Vergleichen von Datensynchronisierung und Transaktionsreplikation

| | Datensynchronisierung | Transaktionsreplikation |
|---|---|---|
| Vorteile | – Aktiv/Aktiv-Unterstützung<br/>– Bidirektional zwischen lokaler und Azure SQL-Datenbank | – Niedrigere Latenzzeiten<br/>– Transaktionskonsistenz<br/>– Wiederverwendung vorhandener Topologie nach der Migration |
| Nachteile | – Latenzzeiten von 5 Minuten und mehr<br/>– Keine Transaktionskonsistenz<br/>– Größere Auswirkung auf die Leistung | – Keine Veröffentlichung über eine Einzel- oder Pooldatenbank in Azure SQL-Datenbank<br/>– Hohe Wartungskosten |
| | | |

## <a name="common-configurations"></a>Häufig verwendete Konfigurationen

Im Allgemeinen müssen sich Herausgeber und Verteiler gemeinsam entweder in der Cloud oder an einem lokalen Standort befinden. Die folgenden Konfigurationen werden unterstützt: 

### <a name="publisher-with-local-distributor-on-a-managed-instance"></a>Herausgeber mit lokalem Verteiler in verwalteter Instanz

![Einzelinstanz als Herausgeber und Verteiler](media/replication-with-sql-database-managed-instance/01-single-instance-asdbmi-pubdist.png)

Herausgeber und Verteiler sind in einer einzelnen verwalteten Instanz konfiguriert und verteilen Änderungen an andere verwaltete Instanzen, Einzeldatenbanken, Pooldatenbanken oder lokale SQL Server-Instanzen. In dieser Konfiguration kann die verwaltete Herausgeber-/Verteilerinstanz nicht mit [Georeplikation und Auto-Failovergruppen](sql-database-auto-failover-group.md) konfiguriert werden.

### <a name="publisher-with-remote-distributor-on-a-managed-instance"></a>Herausgeber mit Remoteverteiler in verwalteter Instanz

In dieser Konfiguration veröffentlicht eine verwaltete Instanz Änderungen auf dem Verteiler in einer anderen verwalteten Instanz, die zahlreiche verwaltete Quellinstanzen bedienen und Änderungen an ein oder mehrere Ziele in verwalteten Instanzen, Einzeldatenbanken, Pooldatenbanken oder SQL Server-Instanzen verteilen kann.

![Separate Instanzen als Herausgeber und Verteiler](media/replication-with-sql-database-managed-instance/02-separate-instances-asdbmi-pubdist.png)

Herausgeber und Verteiler werden in zwei verwalteten Instanzen konfiguriert. Bei dieser Konfiguration gilt Folgendes:

- Beide verwaltete Instanzen befinden sich im gleichen VNET.
- Beide verwaltete Instanzen befinden sich am gleichen Standort.
- Verwaltete Instanzen, die veröffentlichte Datenbanken und Verteilerdatenbanken hosten, können nicht [mithilfe von Auto-Failovergruppen georepliziert werden](sql-database-auto-failover-group.md).

### <a name="publisher-and-distributor-on-premises-with-a-subscriber-on-a-single-pooled-and-instance-database"></a>Herausgeber und Verteiler am lokalen Standort mit einem Abonnenten eines Singletons, einer in einem Pool zusammengefassten Datenbank oder einer Instanzdatenbank 

![Azure SQL-DB als Abonnent](media/replication-with-sql-database-managed-instance/03-azure-sql-db-subscriber.png)
 
In dieser Konfiguration ist eine Azure SQL-Datenbank (Singleton, in einem Pool zusammengefasste und Instanzdatenbank) ein Abonnent. Diese Konfiguration unterstützt die Migration vom lokalen Standort zu Azure. Für einen Abonnenten in einer Einzel- oder Pooldatenbank muss dafür der Pushmodus aktiviert sein.  


## <a name="next-steps"></a>Nächste Schritte

1. [Konfigurieren Sie Transaktionsreplikation für eine verwaltete Instanz](replication-with-sql-database-managed-instance.md). 
1. [Erstellen Sie eine Veröffentlichung](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication).
1. [Erstellen Sie ein Push-Abonnement](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription) mit dem Servernamen von Azure SQL-Datenbank als Abonnent (z. B. `N'azuresqldbdns.database.windows.net`) und dem Namen der Azure SQL-Datenbank als Zieldatenbank (z. B. **AdventureWorks**). )



## <a name="see-also"></a>Siehe auch  

- [Replikation zu SQL-Datenbank-Einzeldatenbanken und in einem Pool zusammengefassten Datenbanken](replication-to-sql-database.md)
- [Replikation zu einer verwalteten Instanz](replication-with-sql-database-managed-instance.md)
- [Erstellen einer Veröffentlichung](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [Erstellen eines Pushabonnements](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/)
- [Replikationstypen](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication)
- [Überwachen (Replikation)](https://docs.microsoft.com/sql/relational-databases/replication/monitor/monitoring-replication)
- [Initialisieren eines Abonnements](https://docs.microsoft.com/sql/relational-databases/replication/initialize-a-subscription)  
