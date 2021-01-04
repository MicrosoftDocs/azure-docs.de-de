---
title: Unterschiede bei T-SQL zwischen SQL Server und Azure SQL Managed Instance
description: In diesem Artikel werden die T-SQL-Unterschiede (Transact-SQL) zwischen Azure SQL Managed Instance und SQL Server beschrieben.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.devlang: ''
ms.topic: reference
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, bonova, danil
ms.date: 11/10/2020
ms.custom: seoapril2019, sqldbrb=1
ms.openlocfilehash: 610ab649d64351b0897ef7358cdaf9280fe3ba55
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94684917"
---
# <a name="t-sql-differences-between-sql-server--azure-sql-managed-instance"></a>Unterschiede bei T-SQL zwischen SQL Server und Azure SQL Managed Instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

In diesem Artikel werden die Unterschiede in der Syntax und dem Verhalten zwischen Azure SQL Managed Instance und SQL Server zusammengefasst und erläutert. 


SQL Managed Instance bietet hohe Kompatibilität mit der SQL Server-Datenbank-Engine, und die meisten Features werden in SQL Managed Instance unterstützt.

![Einfache Migration von SQL Server](./media/transact-sql-tsql-differences-sql-server/migration.png)

Es gibt einige PaaS-Einschränkungen, die in SQL Managed Instance eingeführt werden, und einige Verhaltensänderungen im Vergleich zu SQL Server. Die Unterschiede sind in die folgenden Kategorien unterteilt: <a name="Differences"></a>

- [Verfügbarkeit](#availability) umfasst die Unterschiede in [Always On-Verfügbarkeitsgruppen](#always-on-availability-groups) und [Sicherungen](#backup).
- [Sicherheit](#security), einschließlich der Unterschiede bei [Überwachung](#auditing), [Zertifikaten](#certificates), [Anmeldeinformationen](#credential), [Kryptografieanbietern](#cryptographic-providers), [Anmeldungen und Benutzern](#logins-and-users) sowie [Dienstschlüsseln und Diensthauptschlüsseln](#service-key-and-service-master-key)
- [Konfiguration](#configuration), einschließlich der Unterschiede bei [Pufferpoolerweiterung](#buffer-pool-extension), [Sortierung](#collation), [Kompatibilitätsgraden](#compatibility-levels),[Datenbankspiegelung](#database-mirroring), [Datenbankoptionen](#database-options), [SQL Server-Agent](#sql-server-agent) und [Tabellenoptionen](#tables)
- [Funktionen](#functionalities), einschließlich [BULK INSERT/OPENROWSET](#bulk-insert--openrowset), [CLR](#clr), [DBCC](#dbcc), [verteilter Transaktionen](#distributed-transactions), [erweiterter Ereignisse](#extended-events), [externer Bibliotheken](#external-libraries), [Filestream und Dateitabelle](#filestream-and-filetable), [semantischer Volltextsuche](#full-text-semantic-search), [Verbindungsserver](#linked-servers), [PolyBase](#polybase), [Replikation](#replication), [RESTORE](#restore-statement), [Service Broker](#service-broker) sowie [gespeicherter Prozeduren, Funktionen und Trigger](#stored-procedures-functions-and-triggers).
- [Umgebungseinstellungen](#Environment) wie VNets und Subnetzkonfigurationen.

Die meisten dieser Features sind architekturbezogene Einschränkungen und stellen Dienstfeatures dar.

Temporäre bekannte Probleme, die in SQL Managed Instance erkannt wurden und in Zukunft behoben werden, sind in den [Versionshinweisen](../database/doc-changes-updates-release-notes.md) beschrieben.

## <a name="availability"></a>Verfügbarkeit

### <a name="always-on-availability-groups"></a><a name="always-on-availability-groups"></a>Always On-Verfügbarkeitsgruppen

[Hochverfügbarkeit](../database/high-availability-sla.md) ist in SQL Managed Instance integriert und kann von Benutzern nicht gesteuert werden. Folgende Anweisungen werden nicht unterstützt:

- [CREATE ENDPOINT … FOR DATABASE_MIRRORING](/sql/t-sql/statements/create-endpoint-transact-sql)
- [CREATE AVAILABILITY GROUP](/sql/t-sql/statements/create-availability-group-transact-sql)
- [ALTER AVAILABILITY GROUP](/sql/t-sql/statements/alter-availability-group-transact-sql)
- [DROP AVAILABILITY GROUP](/sql/t-sql/statements/drop-availability-group-transact-sql)
- Die Klausel [SET HADR](/sql/t-sql/statements/alter-database-transact-sql-set-hadr) der Anweisung [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql)

### <a name="backup"></a>Backup

In SQL Managed Instance werden automatische Sicherungen durchgeführt, sodass Benutzer vollständige `COPY_ONLY`-Sicherungen für Datenbanken erstellen können. Differenzielle, Protokoll- und Dateimomentaufnahme-Sicherungen werden nicht unterstützt.

- Bei SQL Managed Instance können Sie eine Instanzdatenbank nur in einem Azure Blob Storage-Konto sichern:
  - Nur `BACKUP TO URL` wird unterstützt.
  - `FILE`, `TAPE` und Sicherungsmedien werden nicht unterstützt.
- Die meisten allgemeinen `WITH`-Optionen werden unterstützt.
  - `COPY_ONLY` ist obligatorisch.
  - `FILE_SNAPSHOT` wird nicht unterstützt.
  - Bandoptionen: `REWIND`, `NOREWIND`, `UNLOAD` und `NOUNLOAD` werden nicht unterstützt.
  - Protokollspezifische Optionen: `NORECOVERY`, `STANDBY` und `NO_TRUNCATE` werden nicht unterstützt.

Einschränkungen: 

- Bei SQL Managed Instance können Sie eine Instanzdatenbank in einer Sicherung mit bis zu 32 Stripes sichern. Dies ist ausreichend für Datenbanken mit bis zu 4 TB, wenn die Sicherungskomprimierung verwendet wird.
- Sie können `BACKUP DATABASE ... WITH COPY_ONLY` nicht in einer Datenbank ausführen, die mit vom Dienst verwalteter Transparent Data Encryption (TDE) verschlüsselt ist. Die vom Dienst verwaltete TDE erzwingt die Verschlüsselung von Sicherungen mit einem internen TDE-Schlüssel. Der Schlüssel kann nicht exportiert werden, daher können Sie die Sicherung nicht wiederherstellen. Verwenden Sie automatische Sicherungen und die Point-in-Time-Wiederherstellung, oder verwenden Sie stattdessen [vom Kunden verwaltete Transparent Data Encryption – BYOK (Bring Your Own Key)](../database/transparent-data-encryption-tde-overview.md#customer-managed-transparent-data-encryption---bring-your-own-key). Sie können die Verschlüsselung für die Datenbank auch deaktivieren.
- Die maximale Stripegröße für Sicherungen mit dem Befehl `BACKUP` in SQL Managed Instance beträgt 195 GB. Dies ist die maximale Blobgröße. Erhöhen Sie die Streifenanzahl im Backup-Befehl, um die einzelne Streifengröße zu reduzieren und innerhalb dieser Einschränkungen zu bleiben.

    > [!TIP]
    > Um diese Einschränkung beim Sichern einer Datenbank von SQL Server in einer lokalen Umgebung oder auf einem virtuellen Computer zu umgehen, haben Sie folgende Möglichkeiten:
    >
    > - Führen Sie die Sicherung mit `DISK` anstatt mit `URL` aus.
    > - Laden Sie die Sicherungsdateien in Blob Storage hoch.
    > - Stellen Sie die Daten in SQL Managed Instance wieder her.
    >
    > Der Befehl `Restore` in SQL Managed Instance unterstützt höhere Blobgrößen in den Sicherungsdateien, da für die Speicherung der hochgeladenen Sicherungsdateien ein anderer Blobtyp verwendet wird.

Informationen zu Sicherungen mithilfe von T-SQL finden Sie unter [BACKUP](/sql/t-sql/statements/backup-transact-sql).

## <a name="security"></a>Sicherheit

### <a name="auditing"></a>Überwachung

Die wichtigsten Unterschiede zwischen der Überwachung in Microsoft Azure SQL-Datenbank und SQL Server sind:

- Bei SQL Managed Instance erfolgt die Überwachung auf Serverebene. Die `.xel`-Protokolldateien werden in Azure Blob Storage gespeichert.
- In Azure SQL-Datenbank erfolgt die Überwachung auf Datenbankebene. Die `.xel`-Protokolldateien werden in Azure Blob Storage gespeichert.
- Bei SQL Server-Instanz erfolgt die Überwachung lokaler oder virtueller Computer auf Serverebene. Ereignisse werden in Dateisystem- oder Windows-Ereignisprotokollen gespeichert.
 
Die XEvent-Überwachung in SQL Managed Instance unterstützt Azure Blob Storage-Ziele. Dateiprotokolle und Windows-Protokolle werden nicht unterstützt.

Wichtigste Unterschiede in der Syntax von `CREATE AUDIT` zur Überwachung in Azure Blob Storage:

- Mit der neuen Syntax `TO URL` können Sie die URL des Azure Blob Storage-Containers angeben, in dem die `.xel`-Dateien gespeichert werden.
- Die Syntax `TO FILE` wird nicht unterstützt, da SQL Managed Instance nicht auf Windows-Dateifreigaben zugreifen kann.

Weitere Informationen finden Sie unter 

- [CREATE SERVER AUDIT](/sql/t-sql/statements/create-server-audit-transact-sql) 
- [ALTER SERVER AUDIT](/sql/t-sql/statements/alter-server-audit-transact-sql)
- [Überwachung](/sql/relational-databases/security/auditing/sql-server-audit-database-engine)

### <a name="certificates"></a>Zertifikate

SQL Managed Instance kann nicht auf Dateifreigaben und Windows-Ordner zugreifen. Daher gelten folgende Einschränkungen:

- Die `CREATE FROM`/`BACKUP TO`-Datei wird für Zertifikate nicht unterstützt.
- Das `CREATE`/`BACKUP`-Zertifikat aus `FILE`/`ASSEMBLY` wird nicht unterstützt. Dateien mit privaten Schlüsseln können nicht verwendet werden. 

Siehe [CREATE CERTIFICATE](/sql/t-sql/statements/create-certificate-transact-sql) und [BACKUP CERTIFICATE](/sql/t-sql/statements/backup-certificate-transact-sql). 
 
**Problemumgehung**: Anstatt eine Sicherung des Zertifikats zu erstellen und die Sicherung wiederherzustellen, [rufen Sie den binären Inhalt des Zertifikats und den privaten Schlüssel ab, speichern Sie sie als SQL-Datei, und erstellen Sie Folgendes aus den Binärdaten](/sql/t-sql/functions/certencoded-transact-sql#b-copying-a-certificate-to-another-database):

```sql
CREATE CERTIFICATE  
   FROM BINARY = asn_encoded_certificate
WITH PRIVATE KEY (<private_key_options>)
```

### <a name="credential"></a>Anmeldeinformationen

Nur Azure Key Vault- und `SHARED ACCESS SIGNATURE`-Identitäten werden unterstützt. Windows-Benutzer werden nicht unterstützt.

Siehe [CREATE CREDENTIAL](/sql/t-sql/statements/create-credential-transact-sql) und [ALTER CREDENTIAL](/sql/t-sql/statements/alter-credential-transact-sql).

### <a name="cryptographic-providers"></a>Kryptografieanbieter

SQL Managed Instance kann nicht auf Dateien zugreifen. Daher können keine Kryptografieanbieter erstellt werden:

- `CREATE CRYPTOGRAPHIC PROVIDER` wird nicht unterstützt. Siehe [CREATE CRYPTOGRAPHIC PROVIDER](/sql/t-sql/statements/create-cryptographic-provider-transact-sql).
- `ALTER CRYPTOGRAPHIC PROVIDER` wird nicht unterstützt. Siehe [ALTER CRYPTOGRAPHIC PROVIDER](/sql/t-sql/statements/alter-cryptographic-provider-transact-sql).

### <a name="logins-and-users"></a>Anmeldungen und Benutzer

- Mithilfe von `FROM CERTIFICATE`, `FROM ASYMMETRIC KEY` und `FROM SID` erstellte SQL-Anmeldungen werden unterstützt. Siehe [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql).
- Azure Active Directory-Dienstprinzipale (Anmeldungen), die mit der Syntax [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) oder der Syntax [CREATE USER FROM LOGIN [Azure AD-Anmeldung]](/sql/t-sql/statements/create-user-transact-sql?view=azuresqldb-mi-current) erstellt wurden, werden unterstützt. Diese Anmeldungen werden auf Serverebene erstellt.

    SQL Managed Instance unterstützt Azure AD-Datenbankprinzipale mit der Syntax `CREATE USER [AADUser/AAD group] FROM EXTERNAL PROVIDER`. Dieses Feature wird auch als Azure AD-Benutzer für eigenständige Datenbanken bezeichnet.

- Windows-Anmeldungen, die mit der Syntax `CREATE LOGIN ... FROM WINDOWS` erstellt wurden, werden nicht unterstützt. Verwenden Sie Azure Active Directory-Anmeldungen und -Benutzer.
- Der Azure AD-Benutzer, der die Instanz erstellt hat, verfügt über [uneingeschränkte Administratorrechte](../database/logins-create-manage.md).
- Azure AD-Benutzer auf Datenbankebene ohne Administratorrechte können mit der Syntax `CREATE USER ... FROM EXTERNAL PROVIDER` erstellt werden. Siehe [CREATE USER ... FROM EXTERNAL PROVIDER](../database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities).
- Azure AD-Serverprinzipale (Anmeldungen) unterstützen SQL-Features nur innerhalb einer Instanz von SQL Managed Instance. Funktionen, die eine instanzübergreifende Interaktion erfordern – unabhängig davon, ob innerhalb desselben Azure AD-Mandanten oder in verschiedenen Mandanten –, werden für Azure AD-Benutzer nicht unterstützt. Beispiele für solche Funktionen:

  - SQL-Transaktionsreplikation
  - Linkserver

- Das Festlegen einer Azure AD-Anmeldung, die einer Azure AD-Gruppe zugeordnet ist, als Besitzer der Datenbank wird nicht unterstützt.
- Identitätswechsel von Azure AD-Prinzipalen auf Serverebene unter Verwendung anderer Azure AD-Prinzipale werden unterstützt, z.B. in der [EXECUTE AS](/sql/t-sql/statements/execute-as-transact-sql)-Klausel. Einschränkung für EXECUTE AS:

  - EXECUTE AS USER wird für Azure AD-Benutzer nicht unterstützt, wenn der Name sich vom Anmeldenamen unterscheidet. Beispiel: Der Benutzer wird über die Syntax CREATE USER [meinAADBenutzer] FROM LOGIN [john@contoso.com] erstellt, und es wird ein Identitätswechsel über EXEC AS USER = _meinAADBenutzer_ versucht. Wenn Sie einen Benutzer (**USER**) auf der Grundlage eines Azure AD-Serverprinzipals (Anmeldung) erstellen, müssen Sie als Benutzernamen den gleichen Anmeldenamen angeben wie in der Anmeldung (**LOGIN**).
  - Die folgenden Vorgänge für Azure AD-Prinzipale können nur von Prinzipalen (Anmeldungen) auf SQL Server-Ebene ausgeführt werden, die der Rolle `sysadmin` angehören:

    - EXECUTE AS USER
    - EXECUTE AS LOGIN

  - Um die Identität eines Benutzers mit einer EXECUTE AS-Anweisung anzunehmen, muss der Benutzer dem Azure AD-Serverprinzipal (Anmeldung) direkt zugeordnet werden. Die Identität von Benutzern, die Mitglieder von Azure AD-Gruppen sind, die Azure AD-Serverprinzipalen zugeordnet sind, kann mit der EXECUTE AS-Anweisung nicht effektiv angenommen werden. Dies gilt selbst dann, wenn der Aufrufer über IMPERSONATE-Berechtigungen für den angegebenen Benutzernamen verfügt.

- Das Exportieren/Importieren von Datenbanken mithilfe von BACPAC-Dateien wird für Azure AD-Benutzer in SQL Managed Instance per [SSMS V18.4 oder höher](/sql/ssms/download-sql-server-management-studio-ssms) oder [SQLPackage.exe](/sql/tools/sqlpackage-download) unterstützt.
  - Die folgenden Konfigurationen werden mit einer BACPAC-Datei für die Datenbank unterstützt: 
    - Exportieren/Importieren einer Datenbank zwischen verschiedenen verwalteten Instanzen in derselben Azure AD-Domäne
    - Exportieren einer Datenbank aus SQL Managed Instance und Importieren in SQL-Datenbank innerhalb derselben Azure AD-Domäne 
    - Exportieren einer Datenbank aus SQL-Datenbank und Importieren in SQL Managed Instance innerhalb derselben Azure AD-Domäne
    - Exportieren einer Datenbank aus SQL Managed Instance und Importieren in SQL Server (ab Version 2012).
      - Bei dieser Konfiguration werden alle Azure AD Benutzer als SQL Server-Datenbankprinzipale (Benutzer) ohne Anmeldungen erstellt. Der Benutzertyp wird als `SQL` aufgeführt und in sys.database_principals als `SQL_USER` angezeigt. Ihre Berechtigungen und Rollen verbleiben in den Metadaten der SQL Server-Datenbank und können für Identitätswechsel verwendet werden. Sie können jedoch nicht für den Zugriff und die Anmeldung bei SQL Server mithilfe der Anmeldeinformationen verwendet werden.

- Nur die Prinzipalanmeldung auf Serverebene, die vom Bereitstellungsprozess von SQL Managed Instance, von Mitgliedern der Serverrollen (z. B. `securityadmin` oder `sysadmin`) oder von anderen Anmeldungen mit der Berechtigung ALTER ANY LOGIN auf Serverebene erstellt wurde, kann Azure AD-Serverprinzipale (Anmeldungen) in der Masterdatenbank für SQL Managed Instance erstellen.
- Wenn es sich bei der Anmeldung um einen SQL-Prinzipal handelt, können nur Anmeldungen, die der Rolle `sysadmin` angehören, den Befehl „create“ verwenden, um Anmeldungen für ein Azure AD-Konto zu erstellen.
- Die Azure AD-Anmeldung muss Mitglied einer Azure AD-Instanz im selben Verzeichnis sein, das auch für Azure SQL Managed Instance verwendet wird.
- Azure AD-Serverprinzipale (Anmeldungen) werden ab SQL Server Management Studio 18.0 Preview 5 im Objekt-Explorer angezeigt.
- Das Überlappen von Azure AD-Serverprinzipalen (Anmeldungen) mit einem Azure AD-Administratorkonto ist zulässig. Azure AD-Serverprinzipale (Anmeldungen) haben beim Auflösen des Prinzipals und beim Anwenden der Berechtigungen auf SQL Managed Instance Vorrang vor Azure AD-Administratoren.
- Während der Authentifizierung wird die folgende Reihenfolge angewendet, um den authentifizierenden Prinzipal aufzulösen:

    1. Wenn das Azure AD-Konto direkt dem Azure AD-Serverprinzipal (Anmeldung) zugeordnet ist, der in „sys.server_principals“ als Typ „E“ vorhanden ist, wird der Zugriff gewährt, und die Berechtigungen des Azure AD-Serverprinzipals (Anmeldung) werden angewendet.
    2. Wenn das Azure AD-Konto Mitglied einer Azure AD-Gruppe ist, die dem Azure AD-Serverprinzipal (Anmeldung) zugeordnet ist – in „sys.server_principals“ als Typ „X“ vorhanden –, wird der Zugriff gewährt, und die Berechtigungen der Azure AD-Gruppenanmeldung werden angewendet.
    3. Wenn das Azure AD-Konto ein spezieller, im Portal konfigurierter Azure AD-Administrator für SQL Managed Instance ist, der in den Systemsichten von SQL Managed Instance nicht vorhanden ist, werden spezielle feste Berechtigungen des Azure AD-Administrators für SQL Managed Instance angewandt (Legacymodus).
    4. Wenn das Azure AD-Konto direkt einem Azure AD-Benutzer in einer Datenbank zugeordnet ist, die in „sys.database_principals“ als Typ „E“ vorhanden ist, wird der Zugriff gewährt, und die Berechtigungen des Azure AD-Datenbankbenutzers werden angewendet.
    5. Wenn das Azure AD-Konto Mitglied einer Azure AD-Gruppe ist, die einem Azure AD-Benutzer in einer Datenbank zugeordnet ist – in „sys.database_principals“ als Typ „X“ vorhanden –, wird der Zugriff gewährt, und die Berechtigungen der Azure AD-Gruppenanmeldung werden angewendet.
    6. Wenn eine Azure AD-Anmeldung einem Azure AD-Benutzerkonto oder einem Azure AD-Gruppenkonto zugeordnet ist, das zu dem authentifizierenden Benutzer aufgelöst werden kann, werden alle Berechtigungen dieser Azure AD-Anmeldung angewendet.

### <a name="service-key-and-service-master-key"></a>Dienstschlüssel und Diensthauptschlüssel

- Die [Sicherung des Hauptschlüssels](/sql/t-sql/statements/backup-master-key-transact-sql) wird nicht unterstützt (wird durch den SQL-Datenbank-Dienst verwaltet).
- Die [Wiederherstellung des Hauptschlüssels](/sql/t-sql/statements/restore-master-key-transact-sql) wird nicht unterstützt (wird durch den SQL-Datenbank-Dienst verwaltet).
- Die [Sicherung des Diensthauptschlüssels](/sql/t-sql/statements/backup-service-master-key-transact-sql) wird nicht unterstützt (wird durch den SQL-Datenbank-Dienst verwaltet).
- Die [Wiederherstellung des Diensthauptschlüssels](/sql/t-sql/statements/restore-service-master-key-transact-sql) wird nicht unterstützt (wird durch den SQL-Datenbank-Dienst verwaltet).

## <a name="configuration"></a>Konfiguration

### <a name="buffer-pool-extension"></a>Pufferpoolerweiterung

- Die [Pufferpoolerweiterung](/sql/database-engine/configure-windows/buffer-pool-extension) wird nicht unterstützt.
- `ALTER SERVER CONFIGURATION SET BUFFER POOL EXTENSION` wird nicht unterstützt. Siehe [ALTER SERVER CONFIGURATION](/sql/t-sql/statements/alter-server-configuration-transact-sql).

### <a name="collation"></a>Sortierung

Die standardmäßige Instanzsortierung ist `SQL_Latin1_General_CP1_CI_AS`, sie kann als Erstellungsparameter angegeben werden. Siehe [Sortierungen](/sql/t-sql/statements/collations).

### <a name="compatibility-levels"></a>Kompatibilitätsgrade

- Folgende Kompatibilitätsgrade werden unterstützt: 100, 110, 120, 130, 140 und 150.
- Kompatibilitätsgrade unter 100 werden nicht unterstützt.
- Der standardmäßige Kompatibilitätsgrad für neue Datenbanken ist 140. Bei wiederhergestellten Datenbanken bleibt der Kompatibilitätsgrad unverändert, wenn er zuvor bei 100 und höher lag.

Siehe [ALTER DATABASE-Kompatibilitätsgrad](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level).

### <a name="database-mirroring"></a>Datenbankspiegelung

Die Datenbankspiegelung wird nicht unterstützt.

- Die Optionen `ALTER DATABASE SET PARTNER` und `SET WITNESS` werden nicht unterstützt.
- `CREATE ENDPOINT … FOR DATABASE_MIRRORING` wird nicht unterstützt.

Weitere Informationen finden Sie unter [ALTER DATABASE SET PARTNER und SET WITNESS](/sql/t-sql/statements/alter-database-transact-sql-database-mirroring) und [CREATE ENDPOINT … FOR DATABASE_MIRRORING](/sql/t-sql/statements/create-endpoint-transact-sql).

### <a name="database-options"></a>Datenbankoptionen

- Mehrere Protokolldateien werden nicht unterstützt.
- In-Memory-Objekte werden in der Dienstebene „Universell“ nicht unterstützt. 
- Es gilt ein Grenzwert von 280 Dateien pro universeller Instanz, d.h. maximal 280 Dateien pro Datenbank. Auf der Dienstebene „Universell“ zählen sowohl Datendateien als auch Protokolldateien für diesen Grenzwert. [Die Dienstebene „Unternehmenskritisch“ unterstützt 32.767 Dateien pro Datenbank](./resource-limits.md#service-tier-characteristics).
- Eine Datenbank darf keine Dateigruppen mit Filestreamdaten enthalten. Es kann keine Wiederherstellung durchgeführt werden, wenn die BAK-Datei `FILESTREAM`-Daten enthält. 
- Jede Datei wird in Azure Blob Storage gespeichert. E/A und Durchsatz pro Datei hängen von der Größe der jeweiligen Datei ab.

#### <a name="create-database-statement"></a>CREATE DATABASE-Anweisung

Für `CREATE DATABASE` gelten die folgenden Einschränkungen:

- Dateien und Dateigruppen können nicht definiert werden. 
- Die `CONTAINMENT`-Option wird nicht unterstützt. 
- `WITH`-Optionen werden nicht unterstützt. 
   > [!TIP]
   > Als Problemumgehung können Sie `ALTER DATABASE` nach `CREATE DATABASE` verwenden, um Datenbankoptionen zum Hinzufügen von Dateien oder Festlegen der Eigenständigkeit festzulegen. 

- Die `FOR ATTACH`-Option wird nicht unterstützt.
- Die `AS SNAPSHOT OF`-Option wird nicht unterstützt.

Weitere Informationen finden Sie unter [CREATE DATABASE](/sql/t-sql/statements/create-database-sql-server-transact-sql).

#### <a name="alter-database-statement"></a>ALTER DATABASE-Anweisung

Einige Dateieigenschaften können nicht festgelegt oder geändert werden:

- Der Dateipfad kann nicht in der T-SQL-Anweisung `ALTER DATABASE ADD FILE (FILENAME='path')` angegeben werden. Entfernen Sie `FILENAME` aus dem Skript, da SQL Managed Instance die Dateien automatisch speichert. 
- Der Dateiname kann nicht mithilfe der Anweisung `ALTER DATABASE` geändert werden.

Die folgenden Optionen werden standardmäßig festgelegt und können nicht geändert werden:

- `MULTI_USER`
- `ENABLE_BROKER ON`
- `AUTO_CLOSE OFF`

Die folgenden Optionen können nicht geändert werden:

- `AUTO_CLOSE`
- `AUTOMATIC_TUNING(CREATE_INDEX=ON|OFF)`
- `AUTOMATIC_TUNING(DROP_INDEX=ON|OFF)`
- `DISABLE_BROKER`
- `EMERGENCY`
- `ENABLE_BROKER`
- `FILESTREAM`
- `HADR`
- `NEW_BROKER`
- `OFFLINE`
- `PAGE_VERIFY`
- `PARTNER`
- `READ_ONLY`
- `RECOVERY BULK_LOGGED`
- `RECOVERY_SIMPLE`
- `REMOTE_DATA_ARCHIVE` 
- `RESTRICTED_USER`
- `SINGLE_USER`
- `WITNESS`

Weitere Informationen finden Sie unter [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql-file-and-filegroup-options).

### <a name="sql-server-agent"></a>SQL Server-Agent

- Das Aktivieren und Deaktivieren des SQL Server-Agents wird derzeit in SQL Managed Instance nicht unterstützt. Der SQL-Agent wird immer ausgeführt.
- SQL Server-Agent-Einstellungen sind schreibgeschützt. Die Prozedur `sp_set_agent_properties` wird in SQL Managed Instance nicht unterstützt. 
- Aufträge
  - T-SQL-Auftragsschritte werden unterstützt.
  - Die folgenden Replikationsaufträge werden unterstützt:
    - Transaktionsprotokollleser
    - Momentaufnahme
    - Verteiler
  - SSIS-Auftragsschritte werden unterstützt.
  - Andere Arten von Auftragsschritten werden derzeit nicht unterstützt:
    - Der Auftragsschritt für die Mergereplikation wird nicht unterstützt. 
    - Der Warteschlangenleser wird nicht unterstützt. 
    - Die Befehlsshell wird noch nicht unterstützt.
  - SQL Managed Instance kann nicht auf externe Ressourcen zugreifen – z. B. auf Netzwerkfreigaben über Robocopy. 
  - SQL Server Analysis Services wird nicht unterstützt.
- Benachrichtigungen werden teilweise unterstützt.
- Die E-Mail-Benachrichtigung wird unterstützt. Dazu muss allerdings ein Datenbank-E-Mail-Profil konfiguriert werden. Der SQL Server-Agent kann nur ein Datenbank-E-Mail-Profil verwenden, für das der Name `AzureManagedInstance_dbmail_profile` angegeben werden muss. 
  - Pager wird nicht unterstützt.
  - NetSend wird nicht unterstützt.
  - Warnungen werden noch nicht unterstützt.
  - Proxys werden nicht unterstützt.
- EventLog wird nicht unterstützt.
- Der Benutzer muss dem Azure AD-Serverprinzipal (Anmeldung) direkt zugeordnet sein, um SQL-Agent-Aufträge zu erstellen, zu ändern oder auszuführen. Benutzer, die nicht direkt zugeordnet sind, z. B. Benutzer, die einer Azure AD-Gruppe angehören, die über die Berechtigungen zum Erstellen, Ändern oder Ausführen von SQL-Agent-Aufträgen verfügt, können diese Aktionen nicht effektiv ausführen. Dies liegt an Einschränkungen beim Identitätswechsel für verwaltete Instanzen und bei [EXECUTE AS](#logins-and-users).

Die folgenden SQL-Agent-Funktionen werden derzeit nicht unterstützt:

- Proxys
- Planen von Aufträgen für eine CPU im Leerlauf
- Aktivieren oder Deaktivieren eines Agents
- Alerts

Weitere Informationen zum SQL Server-Agent finden Sie unter [SQL Server-Agent](/sql/ssms/agent/sql-server-agent).

### <a name="tables"></a>Tabellen

Die folgenden Tabellentypen werden nicht unterstützt:

- [FILESTREAM](/sql/relational-databases/blob/filestream-sql-server)
- [FILETABLE](/sql/relational-databases/blob/filetables-sql-server)
- [EXTERNAL TABLE](/sql/t-sql/statements/create-external-table-transact-sql) (Polybase)
- [MEMORY_OPTIMIZED](/sql/relational-databases/in-memory-oltp/introduction-to-memory-optimized-tables) (wird nur im universellen Tarif unterstützt)

Informationen zum Erstellen und Ändern von Tabellen finden Sie unter [CREATE TABLE](/sql/t-sql/statements/create-table-transact-sql) und [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql).

## <a name="functionalities"></a>Funktionen

### <a name="bulk-insert--openrowset"></a>Bulk insert/OPENROWSET

SQL Managed Instance kann nicht auf Dateifreigaben und Windows-Ordner zugreifen. Daher müssen die Dateien aus Azure Blob Storage importiert werden:

- `DATASOURCE` ist beim Importieren von Dateien aus Azure Blob Storage im Befehl `BULK INSERT` erforderlich. Siehe [BULK INSERT](/sql/t-sql/statements/bulk-insert-transact-sql).
- `DATASOURCE` ist beim Lesen von Inhalten einer Datei aus Azure Blob Storage in der Funktion `OPENROWSET` erforderlich. Siehe [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql).
- `OPENROWSET` kann zum Lesen von Daten aus Instanzen von Azure SQL-Datenbank, Azure SQL Managed Instance oder SQL Server verwendet werden. Andere Quellen wie Oracle-Datenbanken oder Excel-Dateien werden nicht unterstützt.

### <a name="clr"></a>CLR

SQL Managed Instance kann nicht auf Dateifreigaben und Windows-Ordner zugreifen. Daher gelten folgende Einschränkungen:

- Nur `CREATE ASSEMBLY FROM BINARY` wird unterstützt. Siehe [CREATE ASSEMBLY FROM BINARY](/sql/t-sql/statements/create-assembly-transact-sql). 
- `CREATE ASSEMBLY FROM FILE` wird nicht unterstützt. Siehe [CREATE ASSEMBLY FROM FILE](/sql/t-sql/statements/create-assembly-transact-sql).
- `ALTER ASSEMBLY` kann nicht auf Dateien verweisen. Siehe [ALTER ASSEMBLY](/sql/t-sql/statements/alter-assembly-transact-sql).

### <a name="database-mail-db_mail"></a>Datenbank-E-Mail – (db_mail)
 - `sp_send_dbmail` kann keine Anlagen mithilfe des Parameters @file_attachments senden. Aus dieser Prozedur kann auf das lokale Dateisystem und externe Freigaben oder Azure Blob Storage nicht zugegriffen werden.
 - Informieren Sie sich zu den bekannten Problemen im Zusammenhang mit dem Parameter `@query` und Authentifizierung.
 
### <a name="dbcc"></a>DBCC

Nicht dokumentierte DBCC-Anweisungen, die in SQL Server aktiviert sind, werden in SQL Managed Instance nicht unterstützt.

- Es wird nur eine begrenzte Anzahl von globalen Ablaufverfolgungsflags unterstützt. `Trace flags` auf Sitzungsebene werden nicht unterstützt. Siehe [Ablaufverfolgungsflags](/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql).
- [DBCC TRACEOFF](/sql/t-sql/database-console-commands/dbcc-traceoff-transact-sql) und [DBCC TRACEON](/sql/t-sql/database-console-commands/dbcc-traceon-transact-sql) funktionieren mit einer begrenzten Anzahl globaler Ablaufverfolgungsflags.
- [DBCC CHECKDB](/sql/t-sql/database-console-commands/dbcc-checkdb-transact-sql) mit den Optionen REPAIR_ALLOW_DATA_LOSS, REPAIR_FAST und REPAIR_REBUILD kann nicht verwendet werden, weil die Datenbank im Modus `SINGLE_USER` nicht festgelegt werden kann. Siehe [ALTER DATABASE-Unterschiede.](#alter-database-statement) Potenzielle Datenbankbeschädigungen werden vom Azure-Supportteam behandelt. Wenden Sie sich an den Azure-Support, wenn es Anzeichen für eine Beschädigung der Datenbank gibt.

### <a name="distributed-transactions"></a>Verteilte Transaktionen

Derzeit werden [verteilte Transaktionen](../database/elastic-transactions-overview.md) teilweise in der öffentlichen Vorschau unterstützt. Folgende Szenarios werden unterstützt:
* Transaktionen, an denen nur Azure SQL Managed Instances beteiligt sind, die zur [Serververtrauensgruppe](./server-trust-group-overview.md) gehören.
* Von .NET (TransactionScope-Klasse) und Transact-SQL initiierte Transaktionen.

Azure SQL Managed Instance unterstützt derzeit keine anderen Szenarios, die regelmäßig von MSDTC lokal oder in Azure Virtual Machines unterstützt werden.

### <a name="extended-events"></a>Erweiterte Ereignisse

Einige Windows-spezifische Ziele für XEvents (Erweiterte Ereignisse) werden nicht unterstützt:

- Das Ziel `etw_classic_sync` wird nicht unterstützt. Speichern Sie `.xel`-Dateien in Azure Blob Storage. Siehe [etw_classic_sync-Ziel](/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#etw_classic_sync_target-target).
- Das Ziel `event_file` wird nicht unterstützt. Speichern Sie `.xel`-Dateien in Azure Blob Storage. Siehe [event_file-Ziel](/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#event_file-target).

### <a name="external-libraries"></a>Externe Bibliotheken

Externe datenbankinterne R- und Python-Bibliotheken werden in einer begrenzten öffentlichen Vorschau unterstützt. Weitere Informationen finden Sie unter [Machine Learning Services in Azure SQL Managed Instance (Vorschauversion)](machine-learning-services-overview.md)

### <a name="filestream-and-filetable"></a>Filestream und Dateitabelle

- Filestreamdaten werden nicht unterstützt.
- Die Datenbank darf keine Dateigruppen mit `FILESTREAM`-Daten enthalten.
- `FILETABLE` wird nicht unterstützt.
- Tabellen dürfen keine `FILESTREAM`-Typen enthalten.
- Die folgenden Funktionen werden nicht unterstützt:
  - `GetPathLocator()`
  - `GET_FILESTREAM_TRANSACTION_CONTEXT()`
  - `PathName()`
  - `GetFileNamespacePat)`
  - `FileTableRootPath()`

Weitere Informationen finden Sie unter [FILESTREAM](/sql/relational-databases/blob/filestream-sql-server) und [FileTables](/sql/relational-databases/blob/filetables-sql-server).

### <a name="full-text-semantic-search"></a>Semantische Volltextsuche

Die [semantische Suche](/sql/relational-databases/search/semantic-search-sql-server) wird nicht unterstützt.

### <a name="linked-servers"></a>Verbindungsserver

Verbindungsserver in SQL Managed Instance unterstützen eine begrenzte Anzahl von Zielen:

- Unterstützte Ziele sind Instanzen von SQL Managed Instance, SQL-Datenbank, Azure Synapse SQL und SQL Server. 
- Verbindungsserver unterstützen keine verteilten beschreibbaren Transaktionen (MS DTC).
- Nicht unterstützte Ziele sind Dateien, Analysis Services und andere RDBMS. Verwenden Sie einen nativen CSV-Import aus Azure Blob Storage mithilfe von `BULK INSERT` oder `OPENROWSET` als Alternative zum Dateiimport.

Vorgänge: 

- Instanzübergreifende Schreibtransaktionen werden nicht unterstützt.
- `sp_dropserver` wird zum Löschen eines Verbindungsservers unterstützt. Siehe [sp_dropserver](/sql/relational-databases/system-stored-procedures/sp-dropserver-transact-sql).
- Die `OPENROWSET`-Funktion kann verwendet werden, um Abfragen nur auf SQL Server-Instanzen auszuführen. Diese Instanzen können verwaltet sein oder sich auf lokalen oder virtuellen Computern befinden. Siehe [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql).
- Die `OPENDATASOURCE`-Funktion kann verwendet werden, um Abfragen nur auf SQL Server-Instanzen auszuführen. Diese Instanzen können verwaltet sein oder sich auf lokalen oder virtuellen Computern befinden. Als Anbieter werden nur die Werte `SQLNCLI`, `SQLNCLI11` und `SQLOLEDB` unterstützt. z. B. `SELECT * FROM OPENDATASOURCE('SQLNCLI', '...').AdventureWorks2012.HumanResources.Employee`. Siehe [OPENDATASOURCE](/sql/t-sql/functions/opendatasource-transact-sql).
- Verbindungsserver können nicht zum Lesen von Dateien (Excel, CSV) aus den Netzwerkfreigaben verwendet werden. Versuchen Sie es mit [BULK INSERT](/sql/t-sql/statements/bulk-insert-transact-sql#e-importing-data-from-a-csv-file) oder [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql#g-accessing-data-from-a-csv-file-with-a-format-file), welche CSV-Dateien aus Azure Blob Storage lesen. Verfolgen Sie diese Anforderungen im [Feedback zu SQL Managed Instance](https://feedback.azure.com/forums/915676-sql-managed-instance/suggestions/35657887-linked-server-to-non-sql-sources)|.

### <a name="polybase"></a>PolyBase

Der einzige unterstützte Typ externer Quellen für Azure SQL-Datenbank und andere Azure SQL Managed Instance-Instanzen ist RDBMS. Informationen zu PolyBase finden Sie unter [PolyBase](/sql/relational-databases/polybase/polybase-guide).

### <a name="replication"></a>Replikation

- Momentaufnahmen und bidirektionale Replikationstypen werden unterstützt. Mergereplikation, Peer-zu-Peer-Replikation und aktualisierbare Abonnements werden nicht unterstützt.
- Die [Transaktionsreplikation](replication-transactional-overview.md) ist mit einigen Einschränkungen für die Public Preview von SQL Managed Instance verfügbar:
    - Alle Replikationsteilnehmertypen (Herausgeber, Verteiler, Pullabonnent und Pushabonnent) können in SQL Managed Instance platziert werden. Der Herausgeber und der Verteiler müssen dabei entweder beide in der Cloud oder beide lokal platziert sein.
    - SQL Managed Instance kann mit der neuesten SQL Server-Version kommunizieren. Weitere Informationen finden Sie in der [Matrix der unterstützten Versionen](replication-transactional-overview.md#supportability-matrix).
    - Für die Transaktionsreplikation gibt es einige [zusätzliche Netzwerkanforderungen](replication-transactional-overview.md#requirements).

Weitere Informationen zum Konfigurieren von Transaktionsreplikation finden Sie in den folgenden Tutorials:
- [Replikation zwischen einem SQL-MI-Herausgeber und einem SQL-MI-Abonnenten](replication-between-two-instances-configure-tutorial.md)
- [Replikation zwischen einem SQL-MI-Herausgeber, einem SQL-MI-Verteiler und einem SQL Server-Abonnenten](replication-two-instances-and-sql-server-configure-tutorial.md)

### <a name="restore-statement"></a>RESTORE-Anweisung 

- Unterstützte Syntax:
  - `RESTORE DATABASE`
  - `RESTORE FILELISTONLY ONLY`
  - `RESTORE HEADER ONLY`
  - `RESTORE LABELONLY ONLY`
  - `RESTORE VERIFYONLY ONLY`
- Nicht unterstützte Syntax:
  - `RESTORE LOG ONLY`
  - `RESTORE REWINDONLY ONLY`
- Quelle: 
  - `FROM URL` (Azure Blob Storage) ist die einzige unterstützte Option.
  - `FROM DISK`/`TAPE`/Sicherungsmedium wird nicht unterstützt.
  - Sicherungssätze werden nicht unterstützt.
- `WITH`-Optionen werden nicht unterstützt. Wiederherstellungsversuche mit `WITH` wie `DIFFERENTIAL`, `STATS`, `REPLACE` usw. sind nicht erfolgreich.
- `ASYNC RESTORE`: Die Wiederherstellung wird auch bei einer Unterbrechung der Clientverbindung fortgesetzt. Wenn die Verbindung ausfällt, können Sie sich in der Ansicht `sys.dm_operation_status` über den Status eines Wiederherstellungsvorgangs sowie über CREATE- und DROP-Vorgänge für die Datenbank informieren. Siehe [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database). 

Die folgenden Datenbankoptionen werden festgelegt oder überschrieben und können später nicht geändert werden: 

- `NEW_BROKER`, wenn der Broker in der BAK-Datei nicht aktiviert ist. 
- `ENABLE_BROKER`, wenn der Broker in der BAK-Datei nicht aktiviert ist. 
- `AUTO_CLOSE=OFF`, wenn eine Datenbank in der BAK-Datei `AUTO_CLOSE=ON` enthält. 
- `RECOVERY FULL`, wenn eine Datenbank in der BAK-Datei den Wiederherstellungsmodus `SIMPLE` oder `BULK_LOGGED` enthält.
- Eine speicheroptimierte Dateigruppe wird hinzugefügt und erhält den Namen „XTP“, wenn sie in der BAK-Quelldatei nicht vorhanden war. 
- Alle vorhandenen speicheroptimierten Dateigruppen werden in „XTP“ umbenannt. 
- Die Optionen `SINGLE_USER` und `RESTRICTED_USER` werden zu `MULTI_USER` konvertiert.

Einschränkungen: 

- Sicherungen der beschädigten Datenbanken werden je nach Art der Beschädigung möglicherweise wiederhergestellt, automatisierte Sicherungen werden jedoch erst ausgeführt, nachdem die Beschädigung behoben wurde. Stellen Sie sicher, dass Sie `DBCC CHECKDB` für die Quellinstanz von SQL Managed Instance ausführen und für die Sicherung `WITH CHECKSUM` verwenden, um dieses Problem zu vermeiden.
- Die Wiederherstellung der `.BAK`-Datei einer Datenbank, die eine in diesem Dokument beschriebene Einschränkung enthält (z. B. `FILESTREAM`- oder `FILETABLE`-Objekte), kann nicht in SQL Managed Instance durchgeführt werden.
- `.BAK`-Dateien mit mehreren Sicherungssätzen können nicht wiederhergestellt werden. 
- `.BAK`-Dateien mit mehreren Protokolldateien können nicht wiederhergestellt werden.
- Sicherungen, die Datenbanken von mehr als 8 TB, aktive In-Memory-OLTP-Objekte oder eine Reihe von Dateien enthalten, die das Limit von 280 Dateien pro Instanz überschreiten würden, können auf einer universellen Instanz nicht wiederhergestellt werden. 
- Sicherungen, die Datenbanken mit mehr als 4 TB oder In-Memory-OLTP-Objekte enthalten, deren Gesamtgröße größer ist als die unter [Ressourcenlimits](resource-limits.md) beschriebene Größe, können auf der unternehmenskritischen Instanz nicht wiederhergestellt werden.
Weitere Informationen zu Anweisungen für Wiederherstellungen finden Sie unter [RESTORE-Anweisungen](/sql/t-sql/statements/restore-statements-transact-sql).

 > [!IMPORTANT]
 > Die gleichen Einschränkungen gelten für den integrierten Vorgang der Point-in-Time-Wiederherstellung. So kann beispielsweise eine universelle Datenbank, die größer als 4 TB ist, auf einer unternehmenskritischen Instanz nicht wiederhergestellt werden. Eine unternehmenskritische Datenbank mit In-Memory-OLTP-Dateien oder mehr als 280 Dateien kann auf einer universellen Instanz nicht wiederhergestellt werden.

### <a name="service-broker"></a>Service Broker

Der instanzübergreifende Service Broker wird nicht unterstützt:

- `sys.routes`: Als Voraussetzung müssen Sie die Adresse aus „sys.routes“ auswählen. Die Adresse muss für jede Route auf LOCAL festgelegt sein. Informationen hierzu finden Sie unter [sys.routes](/sql/relational-databases/system-catalog-views/sys-routes-transact-sql).
- `CREATE ROUTE`: Sie können `CREATE ROUTE` nicht mit einer anderen `ADDRESS` als `LOCAL` verwenden. Informationen hierzu finden Sie unter [CREATE ROUTE](/sql/t-sql/statements/create-route-transact-sql).
- `ALTER ROUTE`: Sie können `ALTER ROUTE` nicht mit einer anderen `ADDRESS` als `LOCAL` verwenden. Informationen hierzu finden Sie unter [ALTER ROUTE](/sql/t-sql/statements/alter-route-transact-sql). 

### <a name="stored-procedures-functions-and-triggers"></a>Gespeicherte Prozeduren, Funktionen und Trigger

- `NATIVE_COMPILATION` wird in der Dienstebene „Universell“ nicht unterstützt.
- Die folgenden [sp_configure](/sql/relational-databases/system-stored-procedures/sp-configure-transact-sql)-Optionen werden nicht unterstützt: 
  - `allow polybase export`
  - `allow updates`
  - `filestream_access_level`
  - `remote access`
  - `remote data archive`
  - `remote proc trans`
- `sp_execute_external_scripts` wird nicht unterstützt. Siehe [sp_execute_external_scripts](/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql#examples).
- `xp_cmdshell` wird nicht unterstützt. Siehe [xp_cmdshell](/sql/relational-databases/system-stored-procedures/xp-cmdshell-transact-sql).
- `Extended stored procedures` werden nicht unterstützt. Hierzu gehören `sp_addextendedproc` und `sp_dropextendedproc`. Siehe [Erweiterte gespeicherte Prozeduren](/sql/relational-databases/system-stored-procedures/general-extended-stored-procedures-transact-sql).
- `sp_attach_db`, `sp_attach_single_file_db` und `sp_detach_db` werden nicht unterstützt. Siehe [sp_attach_db](/sql/relational-databases/system-stored-procedures/sp-attach-db-transact-sql), [sp_attach_single_file_db](/sql/relational-databases/system-stored-procedures/sp-attach-single-file-db-transact-sql) und [sp_detach_db](/sql/relational-databases/system-stored-procedures/sp-detach-db-transact-sql).

### <a name="system-functions-and-variables"></a>Systemfunktionen und Variablen

Die folgenden Variablen, Funktionen und Sichten geben abweichende Ergebnisse zurück:

- `SERVERPROPERTY('EngineEdition')` gibt den Wert 8 zurück. Durch diese Eigenschaft wird eine Instanz von SQL Managed Instance eindeutig identifiziert. Siehe [SERVERPROPERTY](/sql/t-sql/functions/serverproperty-transact-sql).
- `SERVERPROPERTY('InstanceName')` gibt NULL zurück, da das für SQL Server bestehende Konzept der Instanz für SQL Managed Instance nicht gilt. Siehe [SERVERPROPERTY('InstanceName')](/sql/t-sql/functions/serverproperty-transact-sql).
- `@@SERVERNAME` gibt den vollständigen „verbindungsfähigen“ DNS-Namen zurück, z.B. „meine-verwaltete-instanz.wcus17662feb9ce98.database.windows.net“. Siehe [@@SERVERNAME](/sql/t-sql/functions/servername-transact-sql). 
- `SYS.SERVERS` gibt den vollständigen „verbindungsfähigen“ DNS-Namen zurück, z.B. `myinstance.domain.database.windows.net` für die Eigenschaften „name“ und „data_source“. Siehe [SYS.SERVERS](/sql/relational-databases/system-catalog-views/sys-servers-transact-sql).
- `@@SERVICENAME` gibt NULL zurück, da das für SQL Server bestehende Konzept des Diensts für SQL Managed Instance nicht gilt. Siehe [@@SERVICENAME](/sql/t-sql/functions/servicename-transact-sql).
- `SUSER_ID` wird unterstützt. Gibt NULL zurück, wenn die Azure AD-Anmeldung in „sys.syslogins“ nicht vorhanden ist. Siehe [SUSER_ID](/sql/t-sql/functions/suser-id-transact-sql). 
- `SUSER_SID` wird nicht unterstützt. Es werden falsche Daten zurückgegeben. Dies ist ein bekanntes vorübergehendes Problem. Siehe [SUSER_SID](/sql/t-sql/functions/suser-sid-transact-sql). 

## <a name="environment-constraints"></a><a name="Environment"></a>Umgebungseinschränkungen

### <a name="subnet"></a>Subnet
-  Sie können keine anderen Ressourcen (z. B. virtuelle Computer) in dem Subnetz platzieren, in dem Sie SQL Managed Instance bereitgestellt haben. Stellen Sie diese Ressourcen in einem anderen Subnetz bereit.
- Das Subnetz muss eine ausreichende Anzahl verfügbarer [IP-Adressen](connectivity-architecture-overview.md#network-requirements) aufweisen. Der Mindestwert ist 16, wir empfehlen jedoch, mindestens 32 IP-Adressen im Subnetz vorzusehen.
- [Dem Subnetz von SQL Managed Instance können keine Dienstendpunkte zugeordnet werden](connectivity-architecture-overview.md#network-requirements). Wenn Sie das virtuelle Netzwerk erstellen, überprüfen Sie, ob die Option „Dienstendpunkte“ auf „Deaktiviert“ festgelegt ist.
- Für die Anzahl von virtuellen Kernen und die Typen der Instanzen, die Sie in einer Region platzieren können, gibt es einige [Einschränkungen und Grenzwerte](resource-limits.md#regional-resource-limitations).
- Es gibt eine Reihe von [Sicherheitsregeln, die auf das Subnetz angewendet werden müssen](connectivity-architecture-overview.md#network-requirements).

### <a name="vnet"></a>VNET
- VNet kann mithilfe des Ressourcenmodells bereitgestellt werden – das klassische Modell wird für VNets nicht unterstützt.
- Nachdem eine Instanz von SQL Managed Instance erstellt wurde, wird das Verschieben dieser Instanz oder des VNET in eine andere Ressourcengruppe oder ein anderes Abonnement nicht unterstützt.
- Einige Dienste, wie App Service-Umgebungen, Logik-Apps und SQL Managed Instance (die für Georeplikation, Transaktionsreplikation oder über Verbindungsserver verwendet werden), können nicht auf SQL Managed Instance in anderen Regionen zugreifen, wenn ihre VNETs mithilfe von [globalem Peering](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) verbunden sind. Sie können sich mit diesen Ressourcen über ExpressRoute oder VNET-zu-VNET über VNet-Gateways verbinden.

### <a name="failover-groups"></a>Failovergruppen
Systemdatenbanken werden nicht auf die sekundäre Instanz in einer Failovergruppe repliziert. Daher sind Szenarien, die von Objekten aus den Systemdatenbanken abhängen, auf der sekundären Instanz nicht möglich, es sei denn, die Objekte werden manuell auf der sekundären Instanz erstellt.

### <a name="tempdb"></a>TEMPDB
- Die maximale Dateigröße von `tempdb` darf in der Dienstebene „Universell“ 24 GB pro Kern nicht überschreiten. Die maximale Größe von `tempdb` ist in der Dienstebene „Unternehmenskritisch“ auf die Speichergröße von SQL Managed Instance begrenzt. Die Größe der Protokolldatei `Tempdb` ist bei der Dienstebene „Universell“ auf 120 GB begrenzt. Einige Abfragen geben möglicherweise einen Fehler zurück, wenn für sie mehr als 24 GB pro Kern in `tempdb` erforderlich sind oder die erstellten Protokolldaten mehr als 120 GB benötigen.
- `Tempdb` wird immer in 12 Datendateien aufgeteilt: 1 primäre Datendatei (auch als Masterdatei bezeichnet) und 11 nicht primäre Datendateien. Die Dateistruktur kann nicht geändert werden, und es können auch keine neuen Dateien zu `tempdb` hinzugefügt werden. 
- [Speicheroptimierte `tempdb`-Metadaten](/sql/relational-databases/databases/tempdb-database?view=sql-server-ver15#memory-optimized-tempdb-metadata), ein neues In-Memory Database-Feature von SQL Server 2019, wird nicht unterstützt.
- In der Modelldatenbank erstellte Objekte können in `tempdb` nach einem Neustart oder Failover nicht automatisch erstellt werden, weil `tempdb` die anfängliche Objektliste nicht aus der Modelldatenbank abruft. Sie müssen Objekte in `tempdb` nach jedem Neustart oder Failover manuell erstellen.

### <a name="msdb"></a>MSDB

Die folgenden MSDB-Schemas in SQL Managed Instance müssen ihren jeweiligen vordefinierten Rollen gehören:

- Allgemeine Rollen
  - TargetServersRole
- [Feste Datenbankrollen](/sql/ssms/agent/sql-server-agent-fixed-database-roles?view=sql-server-ver15)
  - SQLAgentUserRole
  - SQLAgentReaderRole
  - SQLAgentOperatorRole
- [DatabaseMail-Rollen](/sql/relational-databases/database-mail/database-mail-configuration-objects?view=sql-server-ver15#DBProfile):
  - DatabaseMailUserRole
- [Integration Services-Rollen](/sql/integration-services/security/integration-services-roles-ssis-service?view=sql-server-ver15):
  - db_ssisadmin
  - db_ssisltduser
  - db_ssisoperator
  
> [!IMPORTANT]
> Das Ändern der vordefinierten Rollennamen, Schemanamen und Schemabesitzer durch Kunden wirkt sich auf den normalen Betrieb des Diensts aus. Alle Änderungen, die an diesen Angaben vorgenommen werden, werden auf die vordefinierten Werte zurückgesetzt, sobald sie erkannt werden, oder spätestens beim nächsten Dienstupdate, um den normalen Dienstbetrieb sicherzustellen.

### <a name="error-logs"></a>Fehlerprotokolle

SQL Managed Instance stellt ausführliche Informationen in Fehlerprotokollen zur Verfügung. Es gibt viele interne Systemereignisse, die im Fehlerprotokoll protokolliert werden. Verwenden Sie zum Lesen von Fehlerprotokollen eine benutzerdefinierte Prozedur, die einige nicht relevante Einträge herausfiltert. Weitere Informationen finden Sie unter [SQL Managed Instance: sp_readmierrorlog ](/archive/blogs/sqlcat/azure-sql-db-managed-instance-sp_readmierrorlog) oder [SQL Managed Instance-Erweiterung (Vorschauversion)](/sql/azure-data-studio/azure-sql-managed-instance-extension#logs) für Azure Data Studio.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu SQL Managed Instance finden Sie unter [Was ist SQL Managed Instance?](sql-managed-instance-paas-overview.md).
- Informationen zu den Features und eine Vergleichsliste finden Sie unter [Funktionsvergleich: Azure SQL Managed Instance](../database/features-comparison.md).
- Informationen zu Releaseupdates und bekannten Problemen finden Sie in den [Versionshinweisen zu SQL Managed Instance](../database/doc-changes-updates-release-notes.md).
- Einen Schnellstart zum Erstellen einer neuen Instanz von SQL Managed Instance finden Sie unter [Erstellen einer Instanz von SQL Managed Instance](instance-create-quickstart.md).
