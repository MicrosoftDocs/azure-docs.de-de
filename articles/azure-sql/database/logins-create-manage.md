---
title: Autorisieren des Server- und Datenbankzugriffs mithilfe von Anmeldungen und Benutzerkonten
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
description: Erfahren Sie, wie Azure SQL-Datenbank, SQL Managed Instance und Azure Synapse Benutzer mithilfe von Anmeldungen und Benutzerkonten für den Zugriff authentifizieren. Außerdem erfahren Sie, wie Sie Datenbankrollen und explizite Berechtigungen zuweisen, um Anmeldungen und Benutzer zum Durchführen von Aktionen und Abfragen von Daten autorisieren.
keywords: Sicherheit für SQL-Datenbank,Datenbanksicherheitsverwaltung,Anmeldesicherheit,Datenbanksicherheit,Datenbankzugriff
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: sqldbrb=3
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: sstein
ms.date: 03/23/2020
ms.openlocfilehash: d03bce1566d4f56a576c980723571f587296236f
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96452433"
---
# <a name="authorize-database-access-to-sql-database-sql-managed-instance-and-azure-synapse-analytics"></a>Autorisieren des Datenbankzugriffs für Azure SQL-Datenbank, SQL Managed Instance und Azure Synapse Analytics
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

In diesem Artikel lernen Sie Folgendes:

- Optionen zum Konfigurieren von Azure SQL-Datenbank, Azure SQL Managed Instance und Azure Synapse Analytics, um Benutzern das Durchführen administrativer Aufgaben und den Zugriff auf die in diesen Datenbanken gespeicherten Daten zu ermöglichen
- Konfiguration von Zugriff und Autorisierung nach der ersten Erstellung eines neuen Servers
- Hinzufügen von Anmeldungen und Benutzerkonten in der Masterdatenbank und Gewähren von Administratorberechtigungen für diese Konten
- Hinzufügen von Benutzerkonten zu Benutzerdatenbanken im Zusammenhang mit Anmeldungen oder als eigenständige Benutzerkonten
- Konfigurieren von Benutzerkonten mit Berechtigungen in Benutzerdatenbanken mithilfe von Datenbankrollen und expliziten Berechtigungen

> [!IMPORTANT]
> Datenbanken in Azure SQL-Datenbank, Azure SQL Managed Instance und Azure Synapse werden im restlichen Teil dieses Artikels gemeinsam als Datenbanken bezeichnet. Server bezieht sich auf den [Server](logical-servers.md), der Datenbanken für Azure SQL-Datenbank und Azure Synapse verwaltet.

## <a name="authentication-and-authorization"></a>Authentifizierung und Autorisierung

Die [**Authentifizierung**](security-overview.md#authentication) ist der Prozess, in dem die Identität des Benutzers bestätigt wird. Ein Benutzer stellt mithilfe eines Benutzerkontos eine Verbindung zu einer Datenbank her.
Wenn ein Benutzer versucht, eine Verbindung mit einer Datenbank herzustellen, gibt er ein Benutzerkonto und Authentifizierungsinformationen an. Der Benutzer wird mit einer der folgenden zwei Authentifizierungsmethoden authentifiziert:

- [SQL-Authentifizierung](/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-sql-server-authentication)

  Bei dieser Authentifizierungsmethode übergibt der Benutzer einen Benutzerkontonamen und das zugehörige Kennwort, um eine Verbindung herzustellen. Dieses Kennwort wird in der Masterdatenbank für Benutzerkonten, die mit einer Anmeldung verknüpft sind, oder in der Datenbank mit den Benutzerkonten gespeichert, die *nicht* mit einer Anmeldung verknüpft sind.
- [Azure Active Directory-Authentifizierung](authentication-aad-overview.md)

  Bei dieser Authentifizierungsmethode gibt der Benutzer einen Benutzerkontonamen an und fordert an, dass der Dienst die Anmeldeinformationen verwendet, die in Azure Active Directory (Azure AD) gespeichert sind.

**Anmeldungen und Benutzer:** Ein Benutzerkonto in einer Datenbank kann einer Anmeldung zugeordnet werden, die in der Masterdatenbank gespeichert wird oder ein Benutzername ist, der in einer individuellen Datenbank gespeichert wird.

- Eine **Anmeldung** ist ein individuelles Konto in der Masterdatenbank, mit dem ein Benutzerkonto in mehreren Datenbanken verknüpft werden kann. Die Anmeldeinformationen für das Benutzerkonto werden zusammen mit der Anmeldung gespeichert.
- Ein **Benutzerkonto** ist ein individuelles Konto in einer Datenbank, das mit einer Anmeldung verknüpft sein kann. Dies ist jedoch nicht erforderlich. Bei Benutzerkonten, die nicht mit einer Anmeldung verknüpft sind, werden die Anmeldeinformationen mit dem Benutzerkonto gespeichert.

Die [**Autorisierung**](security-overview.md#authorization) für den Zugriff auf Daten und das Durchführen verschiedener Aktionen wird mithilfe von Datenbankrollen und expliziten Berechtigungen verwaltet. Der Begriff „Autorisierung“ bezieht sich auf Berechtigungen, die einem Benutzer zugewiesen werden. Die Autorisierung wird durch die [Rollenmitgliedschaften](/sql/relational-databases/security/authentication-access/database-level-roles) und [Berechtigungen auf Objektebene](/sql/relational-databases/security/permissions-database-engine) der Datenbank Ihres Benutzerkontos gesteuert. Als bewährte Methode sollten Sie Benutzern nur die minimal erforderlichen Berechtigungen erteilen.

## <a name="existing-logins-and-user-accounts-after-creating-a-new-database"></a>Vorhandene Anmeldungen und Benutzerkonten nach Erstellung einer Datenbank

Wenn Sie Azure SQL erstmals bereitstellen, legen Sie eine Administratoranmeldung und ein zugehöriges Kennwort für diese Anmeldung fest. Dieses administrative Konto wird als **Serveradministrator** bezeichnet. Die folgende Konfiguration der Anmeldungen und Benutzer in den Master- und Benutzerdatenbank erfolgt während der Bereitstellung:

- Eine SQL-Anmeldung mit Administratorberechtigungen wird mit dem von Ihnen angegebenen Anmeldenamen erstellt. Eine [Anmeldung](/sql/relational-databases/security/authentication-access/principals-database-engine#sa-login) ist ein einzelnes Benutzerkonto für die Anmeldung bei SQL-Datenbank, SQL Managed Instance und Azure Synapse.
- Dieser Anmeldung werden vollständige Administratorberechtigungen für alle Datenbanken als [Serverebenenprinzipal](/sql/relational-databases/security/authentication-access/principals-database-engine) gewährt. Die Anmeldung verfügt über alle verfügbaren Berechtigungen und kann nicht eingeschränkt werden. In einer SQL Managed Instance wird diese Anmeldung zur [festen Serverrolle „sysadmin“](/sql/relational-databases/security/authentication-access/server-level-roles) hinzugefügt (diese Rolle ist in Azure SQL-Datenbank nicht vorhanden).
- Ein [Benutzerkonto](/sql/relational-databases/security/authentication-access/getting-started-with-database-engine-permissions#database-users) namens `dbo` wird in allen Benutzerdatenbank für diese Anmeldung erstellt. Der Benutzer [dbo](/sql/relational-databases/security/authentication-access/principals-database-engine) verfügt über alle Datenbankberechtigungen in der Datenbank und ihm wird die feste Datenbankrolle `db_owner` zugeordnet. Im späteren Verlauf dieses Artikels werden weitere feste Datenbankrollen behandelt.

Öffnen Sie das Azure-Portal, und navigieren Sie zur Registerkarte **Eigenschaften** Ihres Servers oder Ihrer verwalteten Instanz, um die Administratorkonten für eine Datenbank zu identifizieren.

![SQL Server-Administratoren](./media/logins-create-manage/sql-admins.png)

![Screenshot: Hervorhebung der Menüoption „Eigenschaften“](./media/logins-create-manage/sql-admins2.png)

> [!IMPORTANT]
> Der Anmeldename des Administrators kann nach der Erstellung nicht geändert werden. Um das Kennwort für den Serveradministrator zurückzusetzen, klicken Sie im [Azure-Portal](https://portal.azure.com) auf **SQL Server**, wählen Sie in der Liste den Server aus, und klicken Sie dann auf **Kennwort zurücksetzen**. Rufen Sie das Azure-Portal auf, klicken Sie auf die Instanz, und wählen Sie dann **Kennwort zurücksetzen** aus, um das Kennwort für die SQL Managed Instance zurückzusetzen. Sie können auch PowerShell oder die Azure CLI verwenden.

## <a name="create-additional-logins-and-users-having-administrative-permissions"></a>Erstellen weiterer Anmeldungen und Benutzer mit Administratorberechtigungen

An diesem Punkt ist Ihr Server oder Ihre verwaltete Instanz lediglich für den Zugriff mit einer einzigen SQL-Anmeldung und einem Benutzerkonto konfiguriert. Zum Erstellen weiterer Anmeldungen mit vollständigen oder partiellen Administratorberechtigungen stehen Ihnen die folgenden Optionen zur Auswahl (je nach Bereitstellungsmodus):

- **Erstellen eines Azure Active Directory-Administratorkontos mit vollständigen Administratorberechtigungen**

  Aktivieren Sie die Azure Active Directory-Authentifizierung, und erstellen Sie eine Azure AD-Administratoranmeldung. Ein Azure Active Directory-Konto kann als Administrator der Azure SQL-Bereitstellung mit vollständigen Administratorberechtigungen konfiguriert werden. Bei diesem Konto kann es sich um ein einzelnes Konto oder ein Sicherheitsgruppenkonto handeln. Ein Azure AD-Administrator **muss** konfiguriert werden, wenn Sie Azure AD-Konten zum Herstellen einer Verbindung mit Azure SQL-Datenbank,SQL Managed Instance oder Azure Synapse verwenden möchten. Ausführliche Informationen zum Aktivieren der Azure AD-Authentifizierung für alle Azure SQL-Bereitstellungstypen finden Sie in den folgenden Artikeln:

  - [Verwenden der Azure Active Directory-Authentifizierung für die Authentifizierung mit SQL](authentication-aad-overview.md)
  - [Konfigurieren und Verwalten der Azure Active Directory-Authentifizierung mit SQL](authentication-aad-configure.md)

- **Erstellen von SQL-Anmeldungen mit vollständigen Administratorberechtigungen in SQL Managed Instance**

  - Erstellen Sie eine weitere SQL-Anmeldung in der Masterdatenbank.
  - Fügen Sie die Anmeldung mit der Anweisung [ALTER SERVER ROLE](/sql/t-sql/statements/alter-server-role-transact-sql) zur [festen Serverrolle „sysadmin“](/sql/relational-databases/security/authentication-access/server-level-roles) hinzu. Diese Anmeldung verfügt über vollständige Administratorberechtigungen.
  - Alternativ können Sie eine [Azure AD-Anmeldung](authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance) mithilfe der Syntax [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) erstellen.

- **Erstellen von SQL-Anmeldungen mit eingeschränkten Administratorberechtigungen in Azure SQL-Datenbank**

  - Erstellen Sie eine weitere SQL-Anmeldung in der Masterdatenbank.
  - Erstellen Sie ein Benutzerkonto in der Masterdatenbank, das dieser neuen Anmeldung zugeordnet ist.
  - Fügen Sie das Benutzerkonto mithilfe der Anweisung [ALTER ROLE](/sql/t-sql/statements/alter-role-transact-sql) der Rolle `dbmanager`, `loginmanager` oder beiden in der `master`-Datenbank hinzu (verwenden Sie für Azure Synapse die Anweisung [sp_addrolemember](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql)).

  > [!NOTE]
  > Die Rollen `dbmanager` und `loginmanager` gelten **nicht** für SQL Managed Instance-Bereitstellungen.

  Mitglieder dieser [speziellen Masterdatenbankrollen](/sql/relational-databases/security/authentication-access/database-level-roles#special-roles-for--and-) für Azure SQL-Datenbank verfügen über Berechtigungen zum Erstellen und Verwalten von Datenbanken oder Anmeldungen. In Datenbanken, die von einem Benutzer erstellt wurden, der Mitglied der `dbmanager`-Rolle ist, wird das Mitglied der festen Datenbankrolle `db_owner` zugeordnet, sodass es sich mit dem Benutzerkonto `dbo` bei der Datenbank anmelden kann, um sie zu verwalten. Diese Rollen verfügen außerhalb der Masterdatenbank über keine expliziten Berechtigungen.

  > [!IMPORTANT]
  > Sie können keine zusätzliche SQL-Anmeldung mit vollständigen Administratorberechtigungen in Azure SQL-Datenbank erstellen.

## <a name="create-accounts-for-non-administrator-users"></a>Erstellen von Konten für Benutzer ohne Administratorberechtigungen

Sie können Konten für Benutzer ohne Administratorberechtigungen mithilfe einer der folgenden zwei Methoden erstellen:

- **Erstellen einer Anmeldung**

  Erstellen Sie eine SQL-Anmeldung in der Masterdatenbank. Erstellen Sie dann ein Benutzerkonto in allen Datenbanken, auf die der Benutzer zugreifen können muss, und verknüpfen Sie das Benutzerkonto mit der Anmeldung. Dieser Ansatz wird bevorzugt, wenn der Benutzer auf mehrere Datenbanken zugreifen muss und die Kennwörter synchronisiert bleiben sollen. Bei diesem Ansatz kommt es bei Verwendung mit Georeplikation zu Komplexitäten, da die Anmeldung sowohl auf dem primären Server als auch auf den sekundären Servern erstellt werden muss. Weitere Informationen finden Sie unter [Konfigurieren und Verwalten der Sicherheit von Azure SQL-Datenbank für die Geowiederherstellung oder den Failover](active-geo-replication-security-configure.md).
- **Erstellen eines Benutzerkontos**

  Erstellen Sie ein Benutzerkonto in der Datenbank, auf die ein Benutzer zugreifen muss (dieser wird auch als [eigenständiger Benutzer](/sql/relational-databases/security/contained-database-users-making-your-database-portable) bezeichnet).

  - Bei Azure SQL-Datenbank können Sie diese Art von Benutzerkonto immer erstellen.
  - Wenn SQL Managed Instance [Azure AD-Serverprinzipale](authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities) unterstützt, können Sie Benutzerkonten für die Authentifizierung bei der verwalteten SQL-Instanz erstellen, ohne dass Datenbankbenutzer als eigenständige Datenbankbenutzer erstellt werden müssen.

  Bei diesem Ansatz werden die Benutzerauthentifizierungsinformationen in den einzelnen Datenbanken gespeichert und automatisch in georeplizierten Datenbanken repliziert. Wenn jedoch dasselbe Konto in mehreren Datenbanken vorhanden ist und Sie die Azure SQL-Authentifizierung verwenden, müssen Sie die Kennwörter manuell synchronisieren. Wenn ein Benutzer über ein Konto in verschiedenen Datenbanken mit unterschiedlichen Kennwörtern verfügt, kann das Merken dieser Kennwörter ebenfalls zu einem Problem werden.

> [!IMPORTANT]
> Sie müssen mit einem Azure AD-Konto angemeldet sein, das über Administratorberechtigungen in der Datenbank in Azure SQL-Datenbank verfügt, um eigenständige Benutzer zu erstellen, die Azure AD-Identitäten zugeordnet sind. In SQL Managed Instance kann auch eine SQL-Anmeldung mit `sysadmin`-Berechtigungen eine Azure AD-Anmeldung oder einen Azure AD-Benutzer erstellen.

Beispiele zum Erstellen von Anmeldungen und Benutzern finden Sie unter:

- [Erstellen einer Anmeldung für Azure SQL-Datenbank](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-current#examples-1)
- [Erstellen einer Anmeldung für Azure SQL Managed Instance](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current#examples-2)
- [Erstellen einer Anmeldung für Azure Synapse](/sql/t-sql/statements/create-login-transact-sql?view=azure-sqldw-latest#examples-3)
- [Benutzer erstellen](/sql/t-sql/statements/create-user-transact-sql#examples)
- [Erstellen eigenständiger Azure AD-Benutzer](authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities)

> [!TIP]
> Ein Sicherheitslernprogramm, in dem das Erstellen von Benutzern in Azure SQL-Datenbank behandelt wird, finden Sie unter [Tutorial: Schützen von Azure SQL-Datenbank](secure-database-tutorial.md).

## <a name="using-fixed-and-custom-database-roles"></a>Verwenden fester und benutzerdefinierter Datenbankrollen

Nachdem Sie ein Benutzerkonto basierend auf einer Anmeldung oder als eigenständigen Benutzer in einer Datenbank erstellt haben, können Sie diesen Benutzer zum Durchführen verschiedener Aktionen und für den Zugriff auf Daten in einer bestimmten Datenbank autorisieren. Sie können die folgenden Methoden zum Autorisieren des Zugriffs verwenden:

- **Feste Datenbankrollen**

  Fügen Sie das Benutzerkonto zu einer [festen Datenbankrolle](/sql/relational-databases/security/authentication-access/database-level-roles) hinzu. Es gibt 9 feste Datenbankrollen, die jeweils über definierte Berechtigungen verfügen. Die folgenden festen Datenbankrollen werden am häufigsten verwendet: **db_owner**, **db_ddladmin**, **db_datawriter**, **db_datareader**, **db_denydatawriter** und **db_denydatareader**. **db_owner** wird häufig verwendet, um nur einigen Benutzern uneingeschränkte Berechtigungen zu erteilen. Die anderen festen Datenbankrollen sind hilfreich, um bei der Entwicklung schnell eine einfache Datenbank zu erhalten, aber sie sind auch für die meisten Produktionsdatenbanken zu empfehlen. Die feste Datenbankrolle **db_datareader** gewährt beispielsweise Lesezugriff auf alle Tabellen in der Datenbank. Dies ist im nicht unbedingt erforderlich.

  - So fügen Sie einen Benutzer zu einer festen Datenbankrolle hinzu:

    - Verwenden Sie die Anweisung [ALTER ROLE](/sql/t-sql/statements/alter-role-transact-sql) in Azure SQL-Datenbank. Beispiele finden Sie unter [Beispiele zu ALTER ROLE](/sql/t-sql/statements/alter-role-transact-sql#examples).
    - Verwenden Sie die Anweisung [sp_addrolemember](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql) in Azure Synapse. Beispiele finden Sie unter [Beispiele zu sp_addrolemember](/sql/t-sql/statements/alter-role-transact-sql).

- **Benutzerdefinierte Datenbankrolle**

  Erstellen Sie mit der Anweisung [CREATE ROLE](/sql/t-sql/statements/create-role-transact-sql) eine benutzerdefinierte Datenbankrolle. Mit einer benutzerdefinierten Rolle können Sie Ihre eigenen benutzerdefinierte Datenbankrollen erstellen und jeder Rolle sorgfältig die mindestens erforderlichen Berechtigungen für die geschäftlichen Aufgaben zuweisen. Anschließend können Sie Benutzer zur benutzerdefinierten Rolle hinzufügen. Wenn ein Benutzer Mitglied mehrerer Rollen ist, verfügt er über die zusammengefassten Berechtigungen all dieser Rollen.
- **Direktes Zuweisen von Berechtigungen**

  Erteilen Sie die [Berechtigungen](/sql/relational-databases/security/permissions-database-engine) direkt dem Benutzerkonto. Es gibt mehr als 100 Berechtigungen, die in SQL-Datenbank individuell gewährt oder verweigert werden können. Viele dieser Berechtigungen sind geschachtelt. Die `UPDATE`-Berechtigung für ein Schema enthält beispielsweise für jede Tabelle des Schemas die `UPDATE`-Berechtigung. Wie bei den meisten Berechtigungssystemen wird eine Gewährung durch die Verweigerung einer Berechtigung außer Kraft gesetzt. Aufgrund der Schachtelung und der Anzahl von Berechtigungen muss ein geeignetes Berechtigungssystem sorgfältig entworfen werden, um für Ihre Datenbank den richtigen Schutz sicherzustellen. Beginnen Sie mit der Liste der Berechtigungen unter [Berechtigungen (Datenbank-Engine)](/sql/relational-databases/security/permissions-database-engine), und sehen Sie sich die [Grafik in Postergröße](/sql/relational-databases/security/media/database-engine-permissions.png) mit den Berechtigungen an.

## <a name="using-groups"></a>Verwenden von Gruppen

Bei der effizienten Zugriffsverwaltung werden Berechtigungen verwendet, die Active Directory-Sicherheitsgruppen sowie festen oder benutzerdefinierten Rollen anstelle einzelner Benutzer zugewiesen werden.

- Wenn Sie die Active Directory-Authentifizierung verwenden, fügen Sie Azure Active Directory-Benutzer in Azure Active Directory-Sicherheitsgruppen ein. Erstellen Sie einen eigenständigen Datenbankbenutzer für die Gruppe. Fügen Sie mindestens einen Datenbankbenutzer als Mitglied zu benutzerdefinierten oder integrierten Datenbankrollen mit den spezifischen Berechtigungen hinzu, die für diese Benutzergruppe geeignet sind.

- Wenn Sie die SQL-Authentifizierung verwenden, erstellen Sie eigenständige Datenbankbenutzer in der Datenbank. Weisen Sie mindestens einem Datenbankbenutzer eine benutzerdefinierte Datenbankrolle mit spezifischen Berechtigungen zu, die der jeweiligen Benutzergruppe entspricht.

  > [!NOTE]
  > Sie können auch nicht eigenständige Datenbankbenutzer gruppieren.

Machen Sie sich mit den folgenden Features zum Einschränken oder Erweitern von Berechtigungen vertraut:

- Mithilfe von [Identitätswechsel](/dotnet/framework/data/adonet/sql/customizing-permissions-with-impersonation-in-sql-server) und [Modulsignierung](/dotnet/framework/data/adonet/sql/signing-stored-procedures-in-sql-server) können Berechtigungen ohne Sicherheitsbedenken vorübergehend erhöht werden.
- [Sicherheit auf Zeilenebene](/sql/relational-databases/security/row-level-security) lassen sich die Zeilen beschränken, auf die ein Benutzer Zugriff hat.
- [Daten Masking](dynamic-data-masking-overview.md) kann zum Schutz vor unautorisierter Offenlegung von sensiblen Daten verwendet werden.
- [Gespeicherten Prozeduren](/sql/relational-databases/stored-procedures/stored-procedures-database-engine) können verwendet werden, um die Aktionen zu begrenzen, die in der Datenbank ausgeführt werden können.

## <a name="next-steps"></a>Nächste Schritte

Eine Übersicht über alle Sicherheitsfunktionen von Azure SQL-Datenbank und SQL Managed Instance finden Sie unter [Sicherheit – Überblick](security-overview.md).