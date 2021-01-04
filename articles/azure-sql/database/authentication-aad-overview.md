---
title: Authentifizierung über Azure Active Directory
description: Erfahren Sie, wie Sie Azure Active Directory für die Authentifizierung bei Azure SQL-Datenbank, Azure SQL Managed Instance und Synapse SQL in Azure Synapse Analytics verwenden.
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: azure-synapse, sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto, sstein
ms.date: 04/23/2020
ms.openlocfilehash: a636c0e2a41b636f30ada14d4f16a022f2890b71
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96454290"
---
# <a name="use-azure-active-directory-authentication"></a>Verwenden der Azure Active Directory-Authentifizierung

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Die Azure Active Directory-Authentifizierung (Azure AD) ist ein Mechanismus zum Herstellen einer Verbindung mit [Azure SQL-Datenbank](sql-database-paas-overview.md), [Azure SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md) und [Synapse SQL in Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) unter Verwendung von Identitäten in Azure AD.

> [!NOTE]
> Dieser Artikel gilt für Azure SQL-Datenbank, SQL Managed Instance und Azure Synapse Analytics.

Mithilfe der Azure AD-Authentifizierung können Sie die Identitäten von Datenbankbenutzern und anderen Microsoft-Diensten an einer zentralen Stelle verwalten. Die zentrale ID-Verwaltung ermöglicht eine einheitliche Verwaltung von Datenbankbenutzern und vereinfacht die Berechtigungsverwaltung. Daraus ergeben sich u. a. die folgenden Vorteile:

- Es wird eine Alternative zur SQL Server-Authentifizierung bereitgestellt.
- Die unkontrollierte Ausbreitung von Benutzeridentitäten über Server hinweg wird vermieden.
- Die Kennwortrotation wird über eine zentrale Stelle ermöglicht.
- Kunden können Datenbankberechtigungen mithilfe von externen Gruppen (Azure AD) verwalten.
- Durch das Aktivieren der integrierten Windows-Authentifizierung und andere von Azure Active Directory unterstützte Authentifizierungsformen wird das Speichern von Kennwörtern überflüssig.
- Die Azure Active Directory-Authentifizierung verwendet eigenständige Datenbankbenutzer zum Authentifizieren von Identitäten auf Datenbankebene.
- Azure AD unterstützt die tokenbasierte Authentifizierung für Anwendungen, die Verbindungen mit SQL-Datenbank und SQL Managed Instance herstellen.
- Azure AD-Authentifizierung unterstützt Folgendes:
  - Reine Azure AD-Cloudidentitäten
  - Azure AD-Hybrididentitäten, die Folgendes unterstützen:
    - Cloudauthentifizierung mit zwei Optionen gekoppelt mit nahtlosem SSO (einmaliges Anmelden) **Passthrough**-Authentifizierung und **Kennworthashauthentifizierung**
    - Verbundauthentifizierung
  - Weitere Informationen zu Azure AD-Authentifizierungsmethoden sowie zur Auswahl der passenden Methode finden Sie im folgenden Artikel:
    - [Wählen der richtigen Authentifizierungsmethode für Ihre Azure Active Directory-Hybrididentitätslösung](../../active-directory/hybrid/choose-ad-authn.md)

- Azure AD unterstützt Verbindungen von SQL Server Management Studio, bei denen universelle Active Directory-Authentifizierungsverfahren verwendet werden, zu denen auch die mehrstufige Authentifizierung gehört. Die mehrstufige Authentifizierung bietet eine sichere Authentifizierung über unterschiedliche einfache Überprüfungsoptionen – Telefonanruf, SMS, Smartcards mit PIN oder Benachrichtigung in einer mobilen App. Weitere Informationen finden Sie unter [SSMS-Unterstützung für Azure AD Multi-Factor Authentication mit Azure SQL-Datenbank, SQL Managed Instance und Azure Synapse](authentication-mfa-ssms-overview.md).

- Azure AD unterstützt ähnliche Verbindungen aus SQL Server Data Tools (SSDT), die die interaktive Active Directory-Authentifizierung verwenden. Weitere Informationen finden Sie unter [Azure Active Directory-Unterstützung in SQL Server Data Tools (SSDT)](/sql/ssdt/azure-active-directory).

> [!NOTE]  
> Das Herstellen einer Verbindung mit einer SQL Server-Instanz, die auf einer Azure-VM ausgeführt wird, wird für ein Azure Active Directory-Konto nicht unterstützt. Verwenden Sie stattdessen ein Active Directory-Domänenkonto.  

Die Konfigurationsschritte schließen die folgenden Verfahren zum Konfigurieren und Verwenden der Azure Active Directory-Authentifizierung ein:

1. Erstellen und Auffüllen von Azure AD
2. Optional: Zuordnen oder Ändern des aktiven Verzeichnisses für Ihr Azure-Abonnement
3. Erstellen eines Azure Active Directory-Administrators
4. Konfigurieren der Clientcomputer
5. Erstellen eigenständiger Datenbankbenutzer in der Datenbank, die Azure AD-Identitäten zugeordnet sind
6. Herstellen einer Verbindung mit Ihrer Datenbank unter Verwendung von Azure AD-Identitäten

> [!NOTE]
> Informationen zum Erstellen und Auffüllen von Azure AD und zum anschließenden Konfigurieren von Azure AD mit Azure SQL-Datenbank, SQL Managed Instance und Synapse SQL in Azure Synapse Analytics finden Sie unter [Konfigurieren von Azure AD mit Azure SQL-Datenbank](authentication-aad-configure.md).

## <a name="trust-architecture"></a>Architektur von Vertrauensstellungen

- Bei der Unterstützung des nativen Benutzerkennworts von Azure AD wird nur der Cloudanteil von Azure AD, SQL-Datenbank, SQL Managed Instance und Azure Synapse berücksichtigt.
- Um Windows-SSO-Anmeldeinformationen (oder Benutzer/Kennwort für Windows-Anmeldeinformationen) zu unterstützen, verwenden Sie Azure Active Directory-Anmeldeinformationen aus einer Verbunddomäne oder einer verwalteten Domäne, die für nahtloses einmaliges Anmelden für Passthrough- und Kennworthashauthentifizierung konfiguriert ist. Weitere Informationen finden Sie unter [Nahtlose einmalige Anmeldung mit Azure Active Directory](../../active-directory/hybrid/how-to-connect-sso.md).
- Zur Unterstützung der Verbundauthentifizierung (oder von Benutzername und Kennwort für Windows-Anmeldeinformationen) ist die Kommunikation mit dem AD FS-Block erforderlich.

Weitere Informationen zu Azure AD-Hybrididentitäten, zur Einrichtung und Synchronisierung finden Sie in den folgenden Artikeln:

- Kennworthashauthentifizierung: [Implementieren von Kennworthashsynchronisierung mit Azure AD Connect-Synchronisierung](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md)
- Passthrough-Authentifizierung: [Azure Active Directory-Passthrough-Authentifizierung](../../active-directory/hybrid/how-to-connect-pta-quick-start.md)
- Verbundauthentifizierung: [Bereitstellung von Active Directory-Verbunddiensten in Azure](/windows-server/identity/ad-fs/deployment/how-to-connect-fed-azure-adfs) und [Azure AD Connect und Verbund](../../active-directory/hybrid/how-to-connect-fed-whatis.md)

Ein Beispiel für Verbundauthentifizierung mit der AD FS-Infrastruktur (oder Benutzer/Kennwort für Windows-Anmeldeinformationen) finden Sie in der folgenden Abbildung. Die Pfeile zeigen die Kommunikationswege.

![Diagramm zur AAD-Authentifizierung][1]

Das folgende Diagramm zeigt die Verbund-, Vertrauensstellungs- und Hostbeziehungen, die einem Client durch Übermittlung eines Tokens die Verbindungsherstellung mit einer Datenbank ermöglichen. Das Token wird von einem Azure Active Directory authentifiziert, und die Datenbank vertraut dem Token. Bei „Customer1“ kann es sich um ein Azure Active Directory mit nativen Benutzern oder um ein Azure AD mit Verbundbenutzern handeln. „Customer2“ stellt eine mögliche Lösung mit importierten Benutzern dar. In diesem Beispiel stammen diese aus Azure Active Directory im Verbund, wobei ADFS mit Azure Active Directory synchronisiert wird. Wichtig: Für den Zugriff auf eine Datenbank mithilfe der Azure AD-Authentifizierung muss das Abonnement, das als Host fungiert, dem Azure AD zugeordnet sein. Dasselbe Abonnement muss auch verwendet werden, um die Ressourcen für Azure SQL-Datenbank, SQL Managed Instance oder Azure Synapse zu erstellen.

![Abonnementbeziehung][2]

## <a name="administrator-structure"></a>Administratorstruktur

Bei Verwendung der Azure AD-Authentifizierung gibt es zwei Administratorkonten: das ursprüngliche Konto für den Azure SQL-Datenbank-Administrator und das Konto für den Azure AD-Administrator. Die gleichen Konzepte gelten für Azure Synapse. Nur der auf einem Azure AD-Konto basierende Administrator kann den ersten eigenständigen Azure AD-Datenbankbenutzer in einer Benutzerdatenbank erstellen. Das Konto für die Azure AD-Administratoranmeldung kann ein Azure AD-Benutzer oder eine Azure AD-Gruppe sein. Wenn es sich bei dem Administrator um ein Gruppenkonto handelt, kann es von einem beliebigen Gruppenmitglied verwendet werden, sodass mehrere Azure AD-Administratoren den Server verwalten können. Die Verwendung eines Gruppenkontos für den Administrator erleichtert die Verwaltung und ermöglicht es Ihnen, Gruppenmitglieder in Azure AD zentral hinzuzufügen und zu entfernen, ohne die Benutzer oder Berechtigungen in SQL-Datenbank oder Azure Synapse zu ändern. Es kann jeweils nur ein Azure AD-Administrator (ein Benutzer oder eine Gruppe) konfiguriert werden.

![Administratorstruktur][3]

## <a name="permissions"></a>Berechtigungen

Um neue Benutzer zu erstellen, müssen Sie über die Berechtigung `ALTER ANY USER` in der Datenbank verfügen. Die Berechtigung `ALTER ANY USER` kann jedem Datenbankbenutzer gewährt werden. Die Berechtigung `ALTER ANY USER` haben auch Serveradministratorkonten inne, ebenso wie Datenbankbenutzer mit der Berechtigung `CONTROL ON DATABASE` oder `ALTER ON DATABASE` für diese Datenbank sowie Mitglieder der Datenbankrolle `db_owner`.

Zum Erstellen eines Benutzers einer eigenständigen Datenbank in Azure SQL-Datenbank, SQL Managed Instance oder Azure Synapse müssen Sie unter Verwendung einer Azure AD-Identität eine Verbindung mit der Datenbank oder Instanz herstellen. Um den ersten eigenständigen Datenbankbenutzer zu erstellen, müssen Sie unter Verwendung eines Azure AD-Administrators (dieser ist der Besitzer der Datenbank) eine Verbindung mit der Datenbank herstellen. Dies wird unter [Konfigurieren und Verwalten der Azure Active Directory-Authentifizierung mit SQL-Datenbank oder Azure Synapse](authentication-aad-configure.md) erläutert. Eine Azure AD-Authentifizierung ist nur möglich, wenn der Azure AD-Administrator für Azure SQL-Datenbank, SQL Managed Instance oder Azure Synapse erstellt wurde. Falls der Azure Active Directory-Administrator vom Server entfernt wurde, können vorhandene Azure Active Directory-Benutzer, die zuvor in SQL Server erstellt wurden, nicht mehr mithilfe ihrer Azure Active Directory-Anmeldeinformationen auf die Datenbank zugreifen.

## <a name="azure-ad-features-and-limitations"></a>Funktionen und Einschränkungen von Azure AD

- Die folgenden Azure AD-Mitglieder können für Azure SQL-Datenbank bereitgestellt werden:

  - Native Elemente: In Azure AD erstellte Mitglieder in der verwalteten Domäne oder einer Kundendomäne. Weitere Informationen finden Sie unter [Verwenden von benutzerdefinierten Domänennamen zum Vereinfachen des Anmeldevorgangs für Benutzer](../../active-directory/fundamentals/add-custom-domain.md).
  - Mitglieder einer Active Directory Domäne im Verbund mit Azure Active Directory in einer verwalteten Domäne, die für nahtloses einmaliges Anmelden (SSO) mit Passthrough- oder Kennworthashauthentifizierung konfiguriert ist. Weitere Informationen finden Sie unter [Microsoft Azure unterstützt jetzt den Verbund mit Windows Server Active Directory](https://azure.microsoft.com/blog/windows-azure-now-supports-federation-with-windows-server-active-directory//) und [Azure Active Directory: Nahtloses einmaliges Anmelden (SSO)](../../active-directory/hybrid/how-to-connect-sso.md).
  - Aus anderen Azure AD-Instanzen importierte Mitglieder, die native Mitglieder oder Mitglieder der Verbunddomäne sind.
  - Active Directory-Gruppen, die als Sicherheitsgruppen erstellt wurden.

- Azure AD-Benutzer, die einer Gruppe mit der Serverrolle `db_owner` angehören, können die Syntax **[CREATE DATABASE SCOPED CREDENTIAL](/sql/t-sql/statements/create-database-scoped-credential-transact-sql)** für Azure SQL-Datenbank und Azure Synapse nicht verwenden. Der folgende Fehler wird angezeigt:

    `SQL Error [2760] [S0001]: The specified schema name 'user@mydomain.com' either does not exist or you do not have permission to use it.`

    Erteilen Sie die Rolle `db_owner` direkt dem einzelnen Azure AD-Benutzer, um das Problem **CREATE DATABASE SCOPED CREDENTIAL** zu verringern.

- Diese Systemfunktionen geben bei der Ausführung unter Azure AD-Prinzipalen NULL-Werte zurück:

  - `SUSER_ID()`
  - `SUSER_NAME(<admin ID>)`
  - `SUSER_SNAME(<admin SID>)`
  - `SUSER_ID(<admin name>)`
  - `SUSER_SID(<admin name>)`

### <a name="sql-managed-instance"></a>Verwaltete SQL-Instanz

- Azure AD-Serverprinzipale (Anmeldungen) und -Benutzer werden für [SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md) unterstützt.
- Das Festlegen von Azure AD-Serverprinzipalen (Anmeldungen), die einer Azure AD-Gruppe als Datenbankbesitzer zugeordnet sind, wird in [SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md) nicht unterstützt.
  - Eine Erweiterung davon sieht folgendermaßen aus: Wenn eine Gruppe als Teil der Serverrolle `dbcreator` hinzugefügt wird, können Benutzer aus dieser Gruppe eine Verbindung mit SQL Managed Instance herstellen und neue Datenbanken erstellen, aber nicht auf die Datenbank zugreifen. Der Grund: Der neue Datenbankbesitzer ist die Sicherheitszuordnung und nicht der Azure AD-Benutzer. Dieses Problem wird nicht sichtbar, wenn der einzelne Benutzer der Serverrolle `dbcreator` hinzugefügt wird.
- Für Azure AD-Serverprinzipale (Anmeldungen) werden die SQL-Agent-Verwaltung und die Auftragsausführung unterstützt.
- Vorgänge für die Datenbanksicherung und -wiederherstellung können von Azure AD-Serverprinzipalen (Anmeldungen) ausgeführt werden.
- Die Überwachung aller Anweisungen in Verbindung mit Azure AD-Serverprinzipalen (Anmeldungen) und Authentifizierungsereignissen wird unterstützt.
- Eine dedizierte Administratorverbindung für Azure AD-Serverprinzipale (Anmeldungen), die der Serverrolle „sysadmin“ angehören, wird unterstützt.
  - Unterstützt über das SQLCMD-Hilfsprogramm und SQL Server Management Studio.
- Anmeldetrigger werden für Anmeldeereignisse unterstützt, die von Azure AD-Serverprinzipalen (Anmeldungen) stammen.
- Service Broker und Datenbank-E-Mails können unter Verwendung eines Azure AD-Serverprinzipals (Anmeldungen) eingerichtet werden.

## <a name="connect-by-using-azure-ad-identities"></a>Herstellen einer Verbindung mit Azure AD-Identitäten

Die Azure Active Directory-Authentifizierung unterstützt die folgenden Methoden der Verbindungsherstellung mit einer Datenbank unter Verwendung von Azure AD-Identitäten:

- Azure Active Directory Password
- Azure Active Directory Integrated
- Azure Active Directory – universell mit Multi-Factor Authentication
- Anwendungstokenauthentifizierung

Die folgenden Authentifizierungsmethoden werden für Azure AD-Serverprinzipale (Anmeldungen) unterstützt:

- Azure Active Directory Password
- Azure Active Directory Integrated
- Azure Active Directory – universell mit Multi-Factor Authentication

### <a name="additional-considerations"></a>Weitere Überlegungen

- Um die Verwaltungsmöglichkeiten zu verbessern, wird empfohlen, eine dedizierte Azure AD-Gruppe als Administrator bereitzustellen.
- Es kann immer nur ein einzelner Azure AD-Administrator (Benutzer oder Gruppe) für einen Server in SQL-Datenbank oder Azure Synapse konfiguriert werden.
  - Das Hinzufügen von Azure AD-Serverprinzipalen (Anmeldungen) für SQL Managed Instance ermöglicht es, mehrere Azure AD-Serverprinzipale (Anmeldungen) zu erstellen, die der Rolle `sysadmin` hinzugefügt werden können.
- Nur ein Azure AD-Administrator für den Server kann unter Verwendung eines Azure Active Directory-Kontos anfangs eine Verbindung mit dem Server oder der verwalteten Instanz herstellen. Der Active Directory-Administrator kann weitere Azure AD-Datenbankbenutzer konfigurieren.
- Es wird empfohlen, das Verbindungstimeout auf 30 Sekunden festzulegen.
- SQL Server 2016 Management Studio und SQL Server Data Tools für Visual Studio 2015 (ab Version 14.0.60311.1April 2016) unterstützen die Azure Active Directory-Authentifizierung. (Die Azure AD-Authentifizierung wird vom **.NET Framework-Datenanbieter für SqlServer** ab .NET Framework 4.6 unterstützt.) Daher können die neuesten Versionen dieser Tools und Datenebenenanwendungen (DAC und BACPAC) die Azure AD-Authentifizierung verwenden.
- Ab Version 15.0.1 unterstützen das [SQLCMD-Hilfsprogramm](/sql/tools/sqlcmd-utility) und das [BCP-Hilfsprogramm](/sql/tools/bcp-utility) die interaktive Active Directory-Authentifizierung mit Multi-Factor Authentication.
- SQL Server Data Tools für Visual Studio 2015 erfordert mindestens die Data Tools-Version von April 2016 (Version 14.0.60311.1). Azure AD-Benutzer werden derzeit nicht im SSDT-Objekt-Explorer angezeigt. Sie können die Benutzer in [sys.database_principals](/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql) anzeigen, um dieses Problem zu umgehen.
- [Microsoft JDBC-Treiber 6.0 für SQL Server](https://www.microsoft.com/download/details.aspx?id=11774) unterstützt die Azure AD-Authentifizierung. Siehe auch [Einstellen der Verbindungseigenschaften](/sql/connect/jdbc/setting-the-connection-properties).
- PolyBase kann sich nicht per Azure AD-Authentifizierung authentifizieren.
- Die Azure AD-Authentifizierung wird für Azure SQL-Datenbank und Azure Synapse auf den Blättern **Datenbank importieren** und **Datenbank exportieren** im Azure-Portal unterstützt. Import- und Exportvorgänge mit Azure AD-Authentifizierung werden auch per PowerShell-Befehl unterstützt.
- Die Azure AD-Authentifizierung wird für SQL-Datenbank, SQL Managed Instance und Azure Synapse mithilfe der CLI unterstützt. Weitere Informationen finden Sie unter [Konfigurieren und Verwalten der Azure AD-Authentifizierung mit SQL-Datenbank oder Azure Synapse](authentication-aad-configure.md) und [SQL Server – az sql server](/cli/azure/sql/server).

## <a name="next-steps"></a>Nächste Schritte

- Informationen zum Erstellen und Auffüllen einer Azure AD-Instanz und zum anschließenden Konfigurieren der Instanz mit Azure SQL-Datenbank, SQL Managed Instance oder Azure Synapse finden Sie unter [Konfigurieren und Verwalten der Azure Active Directory-Authentifizierung mit SQL-Datenbank, SQL Managed Instance oder Azure Synapse](authentication-aad-configure.md).
- Ein Tutorial zur Verwendung von Azure AD-Serverprinzipalen (Anmeldungen) mit SQL Managed Instance finden Sie unter [Sicherheit für verwaltete Azure SQL-Instanz durch Azure AD-Serverprinzipale (Anmeldungen)](../managed-instance/aad-security-configure-tutorial.md).
- Eine Übersicht über Anmeldungen, Benutzer, Datenbankrollen und Berechtigungen in SQL-Datenbank finden Sie unter [Anmeldungen, Benutzer, Datenbankrollen und Berechtigungen](logins-create-manage.md).
- Weitere Informationen zu Datenbankprinzipalen finden Sie unter [Prinzipale](/sql/relational-databases/security/authentication-access/principals-database-engine).
- Weitere Informationen zu Datenbankrollen finden Sie unter [Datenbankrollen](/sql/relational-databases/security/authentication-access/database-level-roles).
- Die Syntax zum Erstellen von Azure AD-Serverprinzipalen (Anmeldungen) für SQL Managed Instance finden Sie unter [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current&preserve-view=true).
- Weitere Informationen zu Firewallregeln in SQL-Datenbank finden Sie unter [Übersicht über Firewallregeln für Azure SQL-Datenbank](firewall-configure.md).

<!--Image references-->
[1]: ./media/authentication-aad-overview/1aad-auth-diagram.png
[2]: ./media/authentication-aad-overview/2subscription-relationship.png
[3]: ./media/authentication-aad-overview/3admin-structure.png
