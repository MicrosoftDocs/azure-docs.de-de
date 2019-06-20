---
title: Replikation zu Azure SQL-Datenbank | Microsoft-Dokumentation
description: Erfahren Sie mehr über die Verwendung der SQL Server-Replikation mit Azure SQL-Datenbank-Einzeldatenbanken und Datenbanken in Pools für elastische Datenbanken.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: mathoma
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: b9d6569504b5352c6187afe12d903c986019c517
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60646799"
---
# <a name="replication-to-sql-database-single-and-pooled-databases"></a>Replikation zu SQL-Datenbank-Einzeldatenbanken und -Pooldatenbanken

Die SQL Server-Replikation kann für Einzel- und Pooldatenbanken auf einem [SQL Datenbank-Server](sql-database-servers.md) in Azure SQL-Datenbank konfiguriert werden.  

## <a name="supported-configurations"></a>**Unterstützte Konfigurationen**:
  
- SQL Server kann eine SQL Server-Instanz sein, die lokal oder auf einem virtuellen Azure-Computer in der Cloud ausgeführt wird. Weitere Informationen finden Sie unter [Was ist SQL Server auf virtuellen Azure-Computern? (Windows)](https://azure.microsoft.com/documentation/articles/virtual-machines-sql-server-infrastructure-services/).  
- Azure SQL-Datenbank muss ein Pushabonnent eines SQL Server-Verlegers sein.  
- Die Verteilungsdatenbank und die Replikations-Agents können nicht in Azure SQL-Datenbank platziert werden.  
- Momentaufnahmen und die unidirektionale Transaktionsreplikationen werden unterstützt. Nicht unterstützt werden Peer-zu-Peer-Transaktions- und Mergereplikationen.
- Die Replikation ist für die Public Preview auf der verwalteten Azure SQL-Datenbank-Instanz verfügbar. Eine verwaltete Instanz kann Verleger-, Verteiler- und Abonnentendatenbanken hosten. Weitere Informationen finden Sie unter [Replikation mit einer verwalteten SQL-Datenbank-Instanz](replication-with-sql-database-managed-instance.md).

## <a name="versions"></a>Versionen  

- Der Verleger und der Verteiler müssen mindestens eine der folgenden Versionen aufweisen:  
- SQL Server 2017 (14.x)
- SQL Server 2016 (13.x)
- SQL Server 2014 (12.x) SP1 CU3
- SQL Server 2014 (12.x) RTM CU10
- SQL Server 2012 (11.x) SP2 CU8 oder SP3
- Der Versuch, Replikationen mit einer älteren Version zu konfigurieren, kann zu dem Fehler mit der Nummer MSSQL_REPL20084 (Der Prozess konnte keine Verbindung mit dem Abonnenten herstellen.) oder MSSQL_REPL40532 (Der von der Anmeldung angeforderte Server \<Name> kann nicht geöffnet werden. Fehler bei der Anmeldung.) führen.  
- Um alle Features von Azure SQL-Datenbank verwenden zu können, müssen Sie die neuesten Versionen von [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) und [SQL Server Data Tools](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt) verwenden.  
  
## <a name="remarks"></a>Anmerkungen

- Replikationen können mithilfe von [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) oder durch Ausführen von Transact-SQL-Anweisungen im Verleger konfiguriert werden. Replikation können nicht mit dem Azure-Portal konfiguriert werden.  
- Bei Replikationen kann eine Verbindung mit Azure SQL-Datenbank nur über SQL Server-Authentifizierungsanmeldungen hergestellt werden.
- Replizierte Tabellen müssen einen Primärschlüssel aufweisen.  
- Sie benötigen ein Azure-Abonnement.  
- Der Azure SQL-Datenbank-Abonnent kann sich in einer beliebigen Region befinden.  
- Eine einzelne Veröffentlichung in SQL Server kann Azure SQL-Datenbank- und SQL Server-Abonnenten (lokal und SQL Server auf einem virtuellen Azure-Computer) unterstützen.  
- Die Verwaltung, Überwachung und Problembehandlung bei Replikationen müssen über die lokale SQL Server-Instanz ausgeführt werden.  
- In Azure SQL-Datenbank werden nur Pushabonnements unterstützt.  
- Nur `@subscriber_type = 0` wird in **sp_addsubscription** für SQL-Datenbank unterstützt.  
- Azure SQL-Datenbank unterstützt keine bidirektionalen, sofortigen, aktualisierbaren oder Peer-zu-Peer-Replikationen.

## <a name="replication-architecture"></a>Architektur der Replikation  

![replication-to-sql-database](./media/replication-to-sql-database/replication-to-sql-database.png)  

## <a name="scenarios"></a>Szenarien  

### <a name="typical-replication-scenario"></a>Typisches Replikationsszenario  

1. Erstellen Sie eine Transaktionsreplikationsveröffentlichung in einer lokalen SQL Server-Datenbank-Instanz.  
2. Erstellen Sie auf der lokalen SQL Server-Instanz mithilfe des **Assistenten für neue Abonnements** oder mithilfe von Transact-SQL-Anweisungen ein Pushabonnement in Azure SQL-Datenbank.  
3. Bei Verwendung von Einzel- und Pooldatenbanken in Azure SQL-Datenbank handelt es sich beim ersten Dataset um eine Momentaufnahme, die vom Momentaufnahmen-Agent erstellt und vom Verteilungs-Agent verteilt und angewendet wird. Bei einer Datenbank der verwalteten Instanz können Sie auch eine Datenbanksicherung für das Seeding der Abonnentendatenbank verwenden.

### <a name="data-migration-scenario"></a>Datenmigrationsszenario  

1. Replizieren Sie Daten über Transaktionsreplikationen von einer lokalen SQL Server-Datenbank-Instanz zu Azure SQL-Datenbank.  
2. Leiten Sie die Client- oder Middle-Tier-Anwendungen um, um die Azure SQL-Datenbank-Kopie zu aktualisieren.  
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
- Kopieren von Berechtigungen  

### <a name="limitations-to-be-determined"></a>Zu bestimmende Einschränkungen

- Kopieren einer Sortierung  
- Ausführung in einer serialisierten Transaktion der gespeicherten Prozedur  

## <a name="examples"></a>Beispiele

Erstellen Sie eine Veröffentlichung und ein Pushabonnement. Weitere Informationen finden Sie unter
  
- [Erstellen einer Veröffentlichung](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [Erstellen eines Pushabonnements](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/) mit dem Namen des Azure SQL-Datenbank-Servers als Abonnent (z. B. **N'azuresqldbdns.database.windows.net'** ) und dem Namen von Azure SQL-Datenbank als Zieldatenbank (z. B. **AdventureWorks**).  

## <a name="see-also"></a>Siehe auch  

- [Transaktionsreplikation](sql-database-managed-instance-transactional-replication.md)
- [Erstellen einer Veröffentlichung](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [Erstellen eines Pushabonnements](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/)
- [Replikationstypen](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication)
- [Überwachen (Replikation)](https://docs.microsoft.com/sql/relational-databases/replication/monitor/monitoring-replication)
- [Initialisieren eines Abonnements](https://docs.microsoft.com/sql/relational-databases/replication/initialize-a-subscription)  
