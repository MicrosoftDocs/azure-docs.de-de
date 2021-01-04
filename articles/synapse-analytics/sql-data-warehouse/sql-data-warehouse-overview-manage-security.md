---
title: Sichern eines dedizierten SQL-Pools (früher SQL DW)
description: Tipps zum Sichern eines dedizierten SQL-Pools (früher SQL DW) und zum Entwickeln von Lösungen in Azure Synapse Analytics.
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019
tags: azure-synapse
ms.openlocfilehash: ce09488e2323aada5f99494ef3920681b685ec0b
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96453631"
---
# <a name="secure-a-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Sichern eines dedizierten SQL-Pools (früher SQL DW) in Azure Synapse Analytics

> [!div class="op_single_selector"]
>
> * [Sicherheitsübersicht](sql-data-warehouse-overview-manage-security.md)
> * [Authentifizierung](sql-data-warehouse-authentication.md)
> * [Verschlüsselung (Portal)](sql-data-warehouse-encryption-tde.md)
> * [Verschlüsselung (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)

In diesem Artikel werden die Grundlagen zum Sichern Ihres dedizierten SQL-Pools (früher SQL DW) erläutert. Insbesondere erhalten Sie in diesem Artikel erste Informationen über Ressourcen zum Einschränken des Zugriffs, Schützen von Daten und Überwachen von Aktivitäten mithilfe eines dedizierten SQL-Pools (früher SQL DW).

## <a name="connection-security"></a>Verbindungssicherheit

Verbindungssicherheit bezieht sich darauf, auf welche Weise Sie die Verbindungen zu Ihrer Datenbank mithilfe von Firewall-Regeln und Verbindungsverschlüsselung einschränken und sichern.

Firewallregeln werden vom [logischen SQL-Server](../../azure-sql/database/logical-servers.md) und den zugehörigen Datenbanken verwendet, um Verbindungsversuche von IP-Adressen abzuwehren, die nicht explizit genehmigt wurden. Damit von Ihrer Anwendung oder von der öffentlichen IP-Adresse Ihres Clientcomputers aus Verbindungen hergestellt werden können, müssen Sie zuerst über das Azure-Portal, über REST-API oder über PowerShell eine Firewallregel auf Serverebene erstellen.

Eine bewährte Methode besteht darin, die von der Firewall auf Serverebene zugelassenen IP-Adressbereiche so weit wie möglich einzuschränken.  Wenn Sie von Ihrem lokalen Computer aus auf Ihren dedizierten SQL-Pool (früher SQL DW) zugreifen möchten, stellen Sie sicher, dass die Firewall in Ihrem Netzwerk und auf dem lokalen Computer eine ausgehende Kommunikation an TCP-Port 1433 zulässt.  

Der dedizierte SQL-Pool (früher SQL DW) verwendet IP-Firewallregeln auf Serverebene. IP-Firewallregeln auf Datenbankebene werden nicht unterstützt. Weitere Informationen finden Sie unter [Firewallregeln für Azure SQL-Datenbank](../../azure-sql/database/firewall-configure.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

Verbindungen mit Ihrem dedizierten SQL-Pool (früher SQL DW) werden standardmäßig verschlüsselt.  Das Ändern von Verbindungseinstellungen zum Deaktivieren der Verschlüsselung wird ignoriert.

## <a name="authentication"></a>Authentifizierung

Authentifizierung bezieht sich darauf, auf welche Weise Sie Ihre Identität beim Herstellen der Verbindung mit der Datenbank nachweisen. Der dedizierte SQL-Pool (früher SQL DW) unterstützt zurzeit die SQL Server-Authentifizierung mit einem Benutzernamen und Kennwort sowie mit Azure Active Directory.

Bei der Erstellung des Servers für die Datenbank haben Sie eine Serveradministratoranmeldung mit Benutzername und Kennwort angegeben. Mit diesen Anmeldeinformationen können Sie sich mittels SQL Server-Authentifizierung bei jeder Datenbank auf diesem Server als Datenbankbesitzer bzw. „dbo“ (database owner) authentifizieren.

Allerdings sollten die Benutzer in Ihrer Organisation als bewährte Methode ein anderes Konto für die Authentifizierung verwenden. Auf diese Weise können Sie die Berechtigungen für die Anwendung beschränken und die Risiken schädlicher Aktivitäten reduzieren, falls Ihr Anwendungscode für einen SQL-Injection-Angriff anfällig ist.

Um einen authentifizierten SQL Server-Benutzer zu erstellen, stellen Sie mit den Anmeldeinformationen des Serveradministrators eine Verbindung mit der **Masterdatenbank** auf dem Server her, und erstellen Sie eine neue Serveranmeldung.  Es ist eine gute Idee, auch einen Benutzer in der Masterdatenbank zu erstellen. Das Erstellen eines Benutzers in der Masterdatenbank ermöglicht einem Benutzer, sich mit Tools wie SSMS ohne Angabe eines Datenbanknamens anzumelden.  Außerdem kann er mit dem Objekt-Explorer alle Datenbanken auf einem Server anzeigen.

```sql
-- Connect to master database and create a login
CREATE LOGIN ApplicationLogin WITH PASSWORD = 'Str0ng_password';
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

Stellen Sie dann mit den Anmeldedaten für den Serveradministrator eine Verbindung mit Ihrem **dedizierten SQL-Pool (früher SQL DW)** her, und erstellen Sie einen Datenbankbenutzer basierend auf der erstellten Serveranmeldung.

```sql
-- Connect to the database and create a database user
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

Um einem Benutzer die Berechtigung zu erteilen, zusätzliche Vorgänge wie das Erstellen von Anmeldedaten oder neuer Datenbanken auszuführen, weisen Sie dem Benutzer die Rollen `Loginmanager` und `dbmanager` in der Masterdatenbank zu.

Weitere Informationen zu diesen zusätzlichen Rollen sowie zur Authentifizierung bei einer SQL-Datenbank finden Sie unter [Verwalten von Datenbanken und Anmeldungen in der Azure SQL-Datenbank](../../azure-sql/database/logins-create-manage.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).  Weitere Informationen zum Herstellen einer Verbindung mithilfe von Azure Active Directory finden Sie unter [Herstellen einer Verbindung mithilfe der Azure Active Directory-Authentifizierung](sql-data-warehouse-authentication.md).

## <a name="authorization"></a>Authorization

Die Autorisierung bezieht sich darauf, was Sie innerhalb einer Datenbank tun können, sobald Sie authentifiziert und verbunden sind. Autorisierungsrechte werden anhand von Rollenmitgliedschaften und -berechtigungen festgelegt. Als bewährte Methode sollten Sie Benutzern nur die minimal erforderlichen Berechtigungen erteilen. Um Rollen zu verwalten, können Sie die folgenden gespeicherten Prozeduren verwenden:

```sql
EXEC sp_addrolemember 'db_datareader', 'ApplicationUser'; -- allows ApplicationUser to read data
EXEC sp_addrolemember 'db_datawriter', 'ApplicationUser'; -- allows ApplicationUser to write data
```

Das Server-Admin-Konto, mit dem Sie eine Verbindung herstellen, ist Mitglied von db_owner und verfügt daher über alle Berechtigungen in der Datenbank. Speichern Sie dieses Konto für die Bereitstellung von Schemaänderungen und andere Verwaltungsvorgänge. Verwenden Sie das Konto "ApplicationUser" mit eingeschränkteren Berechtigungen, um eine Verbindung von Ihrer Anwendung zur Datenbank mit den geringsten Berechtigungen herzustellen, die von Ihrer Anwendung benötigt werden.

Es gibt Möglichkeiten, Benutzerberechtigungen für die Datenbank noch weiter einzuschränken:

* Mithilfe von granularen [Berechtigungen](/sql/relational-databases/security/permissions-database-engine?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) können Sie steuern, welche Vorgänge in einzelnen Spalten, Tabellen, Sichten, Schemas, Prozeduren und anderen Objekten in der Datenbank ausgeführt werden dürfen. Verwenden Sie präzise Berechtigungen, um die größtmögliche Kontrolle zu haben und die geringstmöglichen Berechtigungen zu erteilen.
* [Datenbankrollen](/sql/relational-databases/security/authentication-access/database-level-roles?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) können, mit Ausnahme von „db_datareader“ und „db_datawriter“, dazu verwendet werden, Anwendungsbenutzerkonten mit mehr Berechtigungen oder Verwaltungskonten mit weniger Berechtigungen zu erstellen. Die integrierten festen Datenbankrollen bieten eine einfache Möglichkeit zum Erteilen von Berechtigungen, können jedoch auch dazu führen, dass mehr Berechtigungen als nötig erteilt werden.
* [Gespeicherten Prozeduren](/sql/relational-databases/stored-procedures/stored-procedures-database-engine?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) können verwendet werden, um die Aktionen zu begrenzen, die in der Datenbank ausgeführt werden können.

Im folgenden Beispiel wird Lesezugriff für ein benutzerdefiniertes Schema erteilt.

```sql
--CREATE SCHEMA Test
GRANT SELECT ON SCHEMA::Test to ApplicationUser
```

Die Verwaltung von Datenbanken und Servern über das Azure-Portal oder mit der Azure Resource Manager-API wird durch die Rollenzuweisungen Ihres Portal-Benutzerkontos gesteuert. Weitere Informationen finden Sie unter [Hinzufügen oder Entfernen von Rollenzuweisungen mithilfe des Azure-Portals](../../role-based-access-control/role-assignments-portal.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

## <a name="encryption"></a>Verschlüsselung

Transparent Data Encryption (TDE) ist ein zusätzlicher Schutz vor der Bedrohung durch schädliche Aktivitäten. Hierzu wird die Verschlüsselung und Entschlüsselung Ihrer ruhenden Daten ausgeführt. Wenn Sie Ihre Datenbank verschlüsseln, werden zugehörige Sicherungen und Transaktionsprotokolle verschlüsselt, ohne dass Änderungen an Ihren Anwendungen erforderlich sind. TDE verschlüsselt die Speicherung einer gesamten Datenbank, indem ein symmetrischer Schlüssel verwendet wird, der als Datenbankverschlüsselungsschlüssel bezeichnet wird.

In SQL-Datenbank wird der Datenbankverschlüsselungsschlüssel mit einem integrierten Serverzertifikat geschützt. Das integrierte Serverzertifikat ist für jeden Server eindeutig. Microsoft tauscht diese Zertifikate mindestens alle 90 Tage automatisch untereinander. Der verwendete Verschlüsselungsalgorithmus ist AES-256. Eine allgemeine Beschreibung von TDE finden Sie unter [Transparent Data Encryption (Transparente Datenverschlüsselung, TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

Sie können Ihre Datenbank mit dem [Azure-Portal](sql-data-warehouse-encryption-tde.md) oder mit [T-SQL](sql-data-warehouse-encryption-tde-tsql.md) verschlüsseln.

## <a name="next-steps"></a>Nächste Schritte

Informationen und Beispiele zum Herstellen einer Verbindung mit Ihrem Warehouse mithilfe verschiedener Protokolle finden Sie unter [Herstellen einer Verbindung mit Ihrem dedizierten SQL-Pool (früher SQL DW)](sql-data-warehouse-connect-overview.md).
