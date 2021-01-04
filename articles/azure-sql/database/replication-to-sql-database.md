---
title: Azure SQL Server-Replikation zu Azure SQL-Datenbank
description: Sie können eine Datenbank in Azure SQL-Datenbank als Pushabonnenten in einer unidirektionalen Transaktionsreplikationstopologie oder Momentaufnahme-Replikationstopologie konfigurieren.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: mathoma
ms.date: 04/28/2020
ms.openlocfilehash: 6ff1d485ab4c0662ae8a9d754ce67b1446b76fcc
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92780953"
---
# <a name="replication-to-azure-sql-database"></a>Replikation zu Azure SQL-Datenbank
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Sie können eine Azure SQL-Datenbank als Pushabonnent in einer unidirektionalen Transaktions- oder Momentaufnahmereplikationstopologie konfigurieren.

> [!NOTE]
> In diesem Artikel wird die Verwendung der [Transaktionsreplikation](/sql/relational-databases/replication/transactional/transactional-replication) in Azure SQL-Datenbank beschrieben. Sie steht nicht im Zusammenhang mit der [aktiven Georeplikation](./active-geo-replication-overview.md), einer Azure SQL-Datenbank-Funktion, mit der Sie umfassende lesbare Replikate einzelner Datenbanken erstellen können.

## <a name="supported-configurations"></a>Unterstützte Konfigurationen
  
- Azure SQL-Datenbank kann nur Pushabonnent eines SQL Server-Verlegers oder -Verteilers sein.  
- Die SQL Server-Instanz, die als Verleger und/oder Verteiler fungiert, kann eine [lokal ausgeführte SQL Server-Instanz](https://www.microsoft.com/sql-server/sql-server-downloads), eine [Instanz von Azure SQL Managed Instance](../managed-instance/instance-create-quickstart.md) oder eine [auf einem virtuellen Azure-Computer in der Cloud ausgeführte SQL Server-Instanz](../virtual-machines/windows/sql-vm-create-portal-quickstart.md) sein. 
- Die Verteilungsdatenbank und die Replikations-Agents können sich nicht in einer Datenbank in Azure SQL-Datenbank befinden.  
- [Momentaufnahmereplikation](/sql/relational-databases/replication/snapshot-replication) und [unidirektionale Transaktionsreplikation](/sql/relational-databases/replication/transactional/transactional-replication) werden unterstützt. Nicht unterstützt werden Peer-zu-Peer-Transaktions- und Mergereplikationen.

### <a name="versions"></a>Versionen  

Für eine erfolgreiche Replikation in eine Datenbank in Azure SQL-Datenbank müssen die SQL Server-Verleger und -Verteiler (mindestens) eine der folgenden Versionen verwenden:

Das Veröffentlichen in einer Azure SQL-Datenbank von einer SQL Server-Datenbank wird durch die folgenden Versionen von SQL Server unterstützt:

- SQL Server 2016 und höher
- SQL Server 2014 [RTM CU10 (12.0.4427.24)](https://support.microsoft.com/help/3094220/cumulative-update-10-for-sql-server-2014) oder [SP1 CU3 (12.0.2556.4)](https://support.microsoft.com/help/3094221/cumulative-update-3-for-sql-server-2014-service-pack-1)
- SQL Server 2012 [SP2 CU8 (11.0.5634.1)](https://support.microsoft.com/help/3082561/cumulative-update-8-for-sql-server-2012-sp2) oder [SP3 (11.0.6020.0)](https://www.microsoft.com/download/details.aspx?id=49996)

> [!NOTE]
> Der Versuch, Replikationen mit einer nicht unterstützten Version zu konfigurieren, kann zu dem Fehler mit der Nummer MSSQL_REPL20084 (Der Prozess konnte keine Verbindung mit dem Abonnenten herstellen.) oder MSSQL_REPL40532 (Der von der Anmeldung angeforderte Server \<name> kann nicht geöffnet werden. Fehler bei der Anmeldung.) führen.  

Um alle Features von Azure SQL-Datenbank verwenden zu können, müssen Sie die neuesten Versionen von [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) und [SQL Server Data Tools](/sql/ssdt/download-sql-server-data-tools-ssdt) verwenden.  

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

  
## <a name="remarks"></a>Bemerkungen

- In Azure SQL-Datenbank werden nur Pushabonnements unterstützt.  
- Replikationen können mithilfe von [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) oder durch Ausführen von Transact-SQL-Anweisungen im Verleger konfiguriert werden. Replikation können nicht mit dem Azure-Portal konfiguriert werden.  
- Bei Replikationen kann eine Verbindung mit Azure SQL-Datenbank nur über SQL Server-Authentifizierungsanmeldungen hergestellt werden.
- Replizierte Tabellen müssen einen Primärschlüssel aufweisen.  
- Sie benötigen ein Azure-Abonnement.  
- Der Azure SQL-Datenbank-Abonnent kann sich in einer beliebigen Region befinden.  
- Eine einzelne Veröffentlichung in SQL Server kann Azure SQL-Datenbank- und SQL Server-Abonnenten (lokal und SQL Server auf einem virtuellen Azure-Computer) unterstützen.  
- Die Verwaltung, Überwachung und Problembehandlung bei Replikationen müssen über SQL Server und nicht über Azure SQL-Datenbank ausgeführt werden.  
- Nur `@subscriber_type = 0` wird in **sp_addsubscription** für SQL-Datenbank unterstützt.  
- Azure SQL-Datenbank unterstützt keine bidirektionalen, sofortigen, aktualisierbaren oder Peer-zu-Peer-Replikationen.

## <a name="replication-architecture"></a>Architektur der Replikation  

![Diagramm: Replikationsarchitektur mit Azure SQL-Datenbank und mehreren Abonnentenclustern in verschiedenen Regionen sowie mit lokalen virtuellen Azure-Computer mit einem Verleger, der Datei „Logread.exe“ und den Dateien „distributor.exe“, die mit Remoteclustern verbunden sind](./media/replication-to-sql-database/replication-to-sql-database.png)  

## <a name="scenarios"></a>Szenarien  

### <a name="typical-replication-scenario"></a>Typisches Replikationsszenario  

1. Erstellen Sie eine Transaktionsreplikationsveröffentlichung in einer SQL Server-Datenbank.  
2. Erstellen Sie im SQL Server mithilfe des **Assistenten für neue Abonnements** oder mithilfe von Transact-SQL-Anweisungen ein Pushabonnement in Azure SQL-Datenbank.  
3. Bei Verwendung von Einzel- und Pooldatenbanken in Azure SQL-Datenbank handelt es sich beim ersten Dataset um eine Momentaufnahme, die vom Momentaufnahmen-Agent erstellt und vom Verteilungs-Agent verteilt und angewendet wird. Bei Verwendung der verwalteten SQL-Instanz als Verleger können Sie auch eine Datenbanksicherung als Seed für den Azure SQL-Datenbank-Abonnenten verwenden.

### <a name="data-migration-scenario"></a>Datenmigrationsszenario  

1. Replizieren Sie Daten über Transaktionsreplikationen von einer SQL Server-Datenbank-Instanz zu Azure SQL-Datenbank.  
2. Leiten Sie die Client- oder Middle-Tier-Anwendung um, um die Datenbankkopie zu aktualisieren.  
3. Beenden Sie die Aktualisierung der SQL Server-Version der Tabelle, und entfernen Sie die Veröffentlichung.  

## <a name="limitations"></a>Einschränkungen

Die folgenden Optionen werden für Azure SQL-Datenbank-Abonnements nicht unterstützt:

- Kopieren einer Dateigruppenzuordnung  
- Kopieren von Tabellenpartitionierungsschemas  
- Kopieren von Indexpartitionierungsschemas  
- Kopieren von benutzerdefinierten Statistiken  
- Kopieren von Standardbindungen  
- Kopieren von Regelbindungen  
- Kopieren von Volltextindizes  
- Kopieren einer XML-XSD  
- Kopieren von XML-Indizes  
- Kopieren von Berechtigungen  
- Kopieren von räumlichen Indizes  
- Kopieren von gefilterten Indizes  
- Kopieren von Datenkomprimierungsattributen  
- Kopieren von Sparsespaltenattributen  
- Konvertieren von filestream in MAX-Datentypen  
- Konvertieren von hierarchyid in MAX-Datentypen  
- Konvertieren von spatial in MAX-Datentypen  
- Kopieren von erweiterten Eigenschaften  

### <a name="limitations-to-be-determined"></a>Zu bestimmende Einschränkungen

- Kopieren einer Sortierung  
- Ausführung in einer serialisierten Transaktion der gespeicherten Prozedur  

## <a name="examples"></a>Beispiele

Erstellen Sie eine Veröffentlichung und ein Pushabonnement. Weitere Informationen finden Sie unter
  
- [Erstellen einer Veröffentlichung](/sql/relational-databases/replication/publish/create-a-publication)
- [Erstellen eines Pushabonnements](/sql/relational-databases/replication/create-a-push-subscription/) mit dem Namen des Servers als Abonnent (z. B. **N'azuresqldbdns.database.windows.net'** ) und dem Namen von Azure SQL-Datenbank als Zieldatenbank (z. B. **AdventureWorks** ).  

## <a name="see-also"></a>Weitere Informationen  

- [Transaktionsreplikation](../managed-instance/replication-transactional-overview.md)
- [Erstellen einer Veröffentlichung](/sql/relational-databases/replication/publish/create-a-publication)
- [Erstellen eines Pushabonnements](/sql/relational-databases/replication/create-a-push-subscription/)
- [Replikationstypen](/sql/relational-databases/replication/types-of-replication)
- [Überwachen (Replikation)](/sql/relational-databases/replication/monitor/monitoring-replication)
- [Initialisieren eines Abonnements](/sql/relational-databases/replication/initialize-a-subscription)