---
title: Erteilen des Zugriffs auf Azure SQL-Datenbank und SQL Data Warehouse | Microsoft-Dokumentation
description: Hier erhalten Sie Informationen zum Erteilen des Zugriffs auf Microsoft Azure SQL-Datenbank und SQL Data Warehouse.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sql-data-warehouse
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: carlrab
manager: craigg
ms.date: 05/08/2019
ms.openlocfilehash: 783a8f0bc25717f1c2bf78a9c0d40b209a07939b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65473339"
---
# <a name="azure-sql-database-and-sql-data-warehouse-access-control"></a>Zugriffssteuerung für Azure SQL-Datenbank und SQL Data Warehouse

Zur Gewährleistung der Sicherheit steuern Azure [SQL-Datenbank](sql-database-technical-overview.md) und [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) den Zugriff mit Firewallregeln (schränken die Konnektivität nach IP-Adresse ein), Authentifizierungsmechanismen (erfordern eine Identitätsbestätigung der Benutzer) und Autorisierungsmechanismen (grenzen bestimmte Aktionen und Daten auf Benutzer ein). 

> [!IMPORTANT]
> Eine Übersicht über die Sicherheitsfunktionen von SQL-Datenbank finden Sie unter [Securing your SQL Database](sql-database-security-overview.md) (Schützen Ihrer SQL-Datenbank). Ein Tutorial finden Sie unter [Schützen von Azure SQL-Datenbank](sql-database-security-tutorial.md). Eine Übersicht über die Sicherheitsfunktionen von SQL Data Warehouse finden Sie unter [SQL Data Warehouse – Sicherheitsübersicht](../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md).

## <a name="firewall-and-firewall-rules"></a>Firewall und Firewallregeln

Die Microsoft Azure SQL-Datenbank bietet einen relationalen Datenbankdienst für Azure und andere internetbasierte Anwendungen. Zum Schutz Ihrer Daten verhindern Firewalls jeglichen Zugriff auf Ihren Datenbankserver, bis Sie angeben, welche Computer zugriffsberechtigt sind. Die Firewall gewährt den Datenbankzugriff auf der Grundlage der Ursprungs-IP-Adresse der jeweiligen Anforderung. Weitere Informationen finden Sie unter [Übersicht über Firewallregeln für Azure SQL-Datenbank](sql-database-firewall-configure.md).

Der Azure SQL-Datenbank-Dienst ist nur über TCP-Port 1433 verfügbar. Wenn Sie von Ihrem Computer auf eine SQL-Datenbank zugreifen möchten, sollten Sie sicherstellen, dass die Firewall Ihres Clientcomputers die ausgehende TCP-Kommunikation über TCP-Port 1433 zulässt. Sofern nicht für andere Anwendungen benötigt, blockieren Sie eingehende Verbindungen über TCP-Port 1433. 

Im Rahmen des Verbindungsprozesses werden Verbindungen von virtuellen Azure-Computern an eine andere IP-Adresse und einen anderen Port umgeleitet, wobei diese Angaben für jede Workerrolle jeweils eindeutig sind. Die Portnummer liegt im Bereich von 11000 bis 11999. Weitere Informationen zu TCP-Ports finden Sie unter [Andere Ports als 1433 für ADO.NET 4.5 und SQL Database2](sql-database-develop-direct-route-ports-adonet-v12.md).

## <a name="authentication"></a>Authentication

SQL-Datenbank unterstützt zwei Arten der Authentifizierung:

- **SQL-Authentifizierung**:

  Diese Authentifizierungsmethode verwendet einen Benutzernamen und ein Kennwort. Bei der Erstellung des SQL-Datenbank-Servers für Ihre Datenbank haben Sie eine „server admin“-Anmeldung mit Benutzername und Kennwort angegeben. Mit diesen Anmeldeinformationen können Sie sich bei jeder Datenbank auf diesem Server als Datenbankbesitzer bzw. "dbo" (database owner) authentifizieren. 
- **Azure Active Directory-Authentifizierung**:

  Diese Authentifizierungsmethode verwendet von Azure Active Directory verwaltete Identitäten und wird für verwaltete und integrierte Domänen unterstützt. Wenn Sie die Azure Active Directory-Authentifizierung verwenden möchten, müssen Sie einen weiteren Serveradministrator mit der Bezeichnung "Azure AD Admin" erstellen, der zum Verwalten von Azure Active Directory-Benutzern und -Gruppen berechtigt ist. Dieser Administrator kann außerdem die gleichen Aufgaben wie ein normaler Serveradministrator ausführen. Unter [Herstellen einer Verbindung mit SQL-Datenbank unter Verwendung der Azure Active Directory-Authentifizierung](sql-database-aad-authentication.md) finden Sie eine exemplarische Vorgehensweise zum Erstellen eines Azure AD-Administrators, um die Azure Active Directory-Authentifizierung zu aktivieren.

Die Datenbank-Engine schließt Verbindungen, die sich länger als 30 Minuten im Leerlauf befinden. Für die Verbindung muss dann eine erneute Anmeldung durchgeführt werden, bevor sie verwendet werden kann. Ständig aktive Verbindungen mit SQL-Datenbank erfordern mindestens alle zehn Stunden eine erneute Autorisierung (über die Datenbank-Engine). Die Datenbank-Engine versucht, die Autorisierung erneut durchzuführen, indem das ursprünglich übermittelte Kennwort verwendet wird. Im Erfolgsfall ist dann keine Benutzereingabe erforderlich. Wenn ein Kennwort in SQL-Datenbank zurückgesetzt wird, wird die Verbindung aus Leistungsgründen nicht erneut authentifiziert. Dies gilt auch, wenn die Verbindung aufgrund des Verbindungspoolings zurückgesetzt wird. Dies unterscheidet sich vom Verhalten einer lokalen SQL Server-Instanz. Wenn das Kennwort seit der ersten Autorisierung der Verbindung geändert wurde, muss die Verbindung beendet und mit dem neuen Kennwort eine neue Verbindung hergestellt werden. Ein Benutzer mit der Berechtigung `KILL DATABASE CONNECTION` kann eine Verbindung mit SQL-Datenbank mithilfe des Befehls [KILL](https://docs.microsoft.com/sql/t-sql/language-elements/kill-transact-sql) explizit beenden.

Benutzerkonten können in der Masterdatenbank erstellt werden und Berechtigungen in allen Datenbanken auf dem Server erhalten. Alternativ können sie in der Datenbank selbst erstellt werden (sogenannte eigenständige Benutzer). Informationen zum Erstellen und Verwalten von Anmeldungen finden Sie unter [SQL-Datenbank-Authentifizierung und -Autorisierung: Gewähren von Zugriff](sql-database-manage-logins.md). Verwenden Sie eigenständige Datenbanken, um die Portabilität und Skalierbarkeit zu verbessern. Weitere Informationen zu eigenständigen Benutzern finden Sie unter [Eigenständige Datenbankbenutzer - machen Sie Ihre Datenbank portabel](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable), [CREATE USER (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql) und [Eigenständige Datenbanken](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases).

Es empfiehlt sich, für die Anwendung ein dediziertes Konto für die Authentifizierung zu verwenden. Auf diese Weise können Sie die Berechtigungen für die Anwendung beschränken und die Risiken schädlicher Aktivitäten reduzieren, falls Ihr Anwendungscode für einen Angriff durch Einschleusung von SQL-Befehlen anfällig ist. Der empfohlene Ansatz besteht darin, einen [eigenständigen Datenbankbenutzer](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable) zu erstellen. Dadurch kann sich Ihre App direkt bei der Datenbank authentifizieren. 

## <a name="authorization"></a>Autorisierung

Autorisierung bezieht sich darauf, welche Aufgaben ein Benutzer in einer Azure SQL-Datenbank ausführen kann. Dies wird durch die [Datenbank-Rollenmitgliedschaften](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/database-level-roles) und [Objektebenenberechtigungen](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine) Ihres Benutzerkontos gesteuert. Als bewährte Methode sollten Sie Benutzern nur die minimal erforderlichen Berechtigungen erteilen. Das Server-Admin-Konto, mit dem Sie eine Verbindung herstellen, ist Mitglied von db_owner und verfügt daher über alle Berechtigungen in der Datenbank. Speichern Sie dieses Konto für die Bereitstellung von Schemaänderungen und andere Verwaltungsvorgänge. Verwenden Sie das Konto "ApplicationUser" mit eingeschränkteren Berechtigungen, um eine Verbindung von Ihrer Anwendung zur Datenbank mit den geringsten Berechtigungen herzustellen, die von Ihrer Anwendung benötigt werden. Weitere Informationen finden Sie unter [Verwalten von Anmeldungen](sql-database-manage-logins.md).

In der Regel benötigen nur Administratoren Zugriff auf die `master`-Datenbank. Der routinemäßige Zugriff auf jede Benutzerdatenbank muss über Benutzer für eigenständige Datenbanken geschehen, die in jeder Datenbank erstellt werden und nicht über Administratorrechte verfügen. Bei Verwendung von Benutzern für eine eigenständige Datenbank müssen Sie keine Anmeldungen für die `master`-Datenbank erstellen. Weitere Informationen finden Sie unter [Eigenständige Datenbankbenutzer – machen Sie Ihre Datenbank portabel](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable).

Machen Sie sich mit den folgenden Features zum Einschränken oder Erweitern von Berechtigungen vertraut:

- Mithilfe von [Identitätswechsel](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/customizing-permissions-with-impersonation-in-sql-server) und [Modulsignierung](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/signing-stored-procedures-in-sql-server) können Berechtigungen ohne Sicherheitsbedenken vorübergehend erhöht werden.
- [Sicherheit auf Zeilenebene](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) lassen sich die Zeilen beschränken, auf die ein Benutzer Zugriff hat.
- [Daten Masking](sql-database-dynamic-data-masking-get-started.md) kann zum Schutz vor unautorisierter Offenlegung von sensiblen Daten verwendet werden.
- [Gespeicherten Prozeduren](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) können verwendet werden, um die Aktionen zu begrenzen, die in der Datenbank ausgeführt werden können.

## <a name="next-steps"></a>Nächste Schritte

- Eine Übersicht über die Sicherheitsfunktionen von SQL-Datenbank finden Sie unter [Securing your SQL Database](sql-database-security-overview.md) (Schützen Ihrer SQL-Datenbank).
- Weitere Informationen zu Firewallregeln finden Sie unter [Firewallregeln](sql-database-firewall-configure.md).
- Weitere Informationen zu Benutzern und Anmeldungenen finden Sie unter [Verwalten von Anmeldungen](sql-database-manage-logins.md). 
- Eine Beschreibung der proaktiven Überwachung finden Sie unter [Datenbanküberwachung](sql-database-auditing.md) und [Bedrohungserkennung von SQL-Datenbank](sql-database-threat-detection.md).
- Ein Tutorial finden Sie unter [Schützen von Azure SQL-Datenbank](sql-database-security-tutorial.md).
