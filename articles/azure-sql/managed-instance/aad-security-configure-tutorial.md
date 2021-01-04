---
title: Sicherheit für verwaltete SQL-Instanzen mit Azure AD-Serverprinzipalen (Anmeldungen)
description: Hier finden Sie Informationen zu Methoden und Features für den Schutz einer verwalteten Azure SQL-Instanz sowie zur Verwendung von Azure AD-Serverprinzipalen (Anmeldungen).
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: sqldbrb=1
ms.topic: tutorial
author: GitHubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 11/06/2019
ms.openlocfilehash: 9161bf4f99ddfed479451d2091458ab309aa2c17
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92788620"
---
# <a name="tutorial-security-in-azure-sql-managed-instance-using-azure-ad-server-principals-logins"></a>Tutorial: Sicherheit für verwaltete Azure SQL-Instanz durch Azure AD-Serverprinzipale (Anmeldungen)
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Bei einer verwaltete Azure SQL-Instanz stehen nahezu die gleichen Sicherheitsfeatures zur Verfügung wie bei der neuesten Datenbank-Engine einer SQL Server-Instanz (Enterprise Edition):

- Einschränken des Zugriffs in einer isolierten Umgebung
- Verwenden von Authentifizierungsmechanismen mit Identitätsnachweis: Azure Active Directory- (Azure AD-) und SQL-Authentifizierung
- Verwenden einer Autorisierung mit rollenbasierten Mitgliedschaften und Berechtigungen
- Aktivieren der Sicherheitsfeatures

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
>
> - Erstellen eines Azure AD-Serverprinzipals (Anmeldung) für eine verwaltete Instanz
> - Gewähren von Berechtigungen für Azure AD-Serverprinzipale (Anmeldungen) in einer verwalteten Instanz
> - Erstellen von Azure AD-Benutzern auf der Grundlage von Azure AD-Serverprinzipalen (Anmeldungen)
> - Zuweisen von Berechtigungen zu Azure AD-Benutzern und Verwalten der Datenbanksicherheit
> - Verwenden des Identitätswechsels mit Azure AD-Benutzern
> - Verwenden datenbankübergreifender Abfragen mit Azure AD-Benutzern
> - Kennenlernen von Sicherheitsfeatures wie Bedrohungsschutz, Überwachung, Datenmaskierung und Verschlüsselung

Weitere Informationen finden Sie in der [Übersicht über verwaltete Azure SQL-Instanzen](sql-managed-instance-paas-overview.md). 

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial wird Folgendes vorausgesetzt:

- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS)
- Eine verwaltete Instanz
  - Führen Sie die Schritte des folgenden Artikels aus: [Schnellstart: Erstellen einer verwalteten Instanz](instance-create-quickstart.md)
- Zugriff auf Ihre verwaltete Instanz und [Bereitstellung eines Azure AD-Administrators für die verwaltete Instanz](../database/authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance). Weitere Informationen finden Sie unter:
  - [Herstellen einer Verbindung zwischen einer Anwendung und einer verwalteten Azure SQL-Datenbank-Instanz](connect-application-instance.md)
  - [Konnektivitätsarchitektur für eine verwaltete SQL-Instanz in Azure SQL-Datenbank](connectivity-architecture-overview.md)
  - [Konfigurieren und Verwalten der Azure Active Directory-Authentifizierung mit SQL](../database/authentication-aad-configure.md)

## <a name="limit-access"></a>Beschränken des Zugriffs 

Auf verwaltete Instanzen kann über eine private IP-Adresse zugegriffen werden. Anwendungen und Benutzer benötigen ähnlich wie bei einer isolierten SQL Server-Umgebung Zugriff auf das Netzwerk der verwalteten SQL-Instanz (VNET), um eine Verbindung herstellen zu können. Weitere Informationen finden Sie unter [Herstellen einer Verbindung zwischen einer Anwendung und einer verwalteten SQL-Instanz](connect-application-instance.md).

Darüber hinaus kann ein Dienstendpunkt für eine verwaltete Instanz konfiguriert werden, um öffentliche Verbindungen wie bei Azure SQL-Datenbank zu ermöglichen.
Weitere Informationen finden Sie unter [Konfigurieren des öffentlichen Endpunkts in der verwalteten Azure SQL-Instanz](public-endpoint-configure.md).

> [!NOTE]
> Auch bei aktivierten Dienstendpunkten gelten die [Firewallregeln für Azure SQL-Datenbank](../database/firewall-configure.md) nicht. Eine verwaltete Azure SQL-Instanz verfügt über eine eigene [integrierte Firewall](management-endpoint-verify-built-in-firewall.md) zur Verwaltung der Konnektivität.

## <a name="create-an-azure-ad-server-principal-login-using-ssms"></a>Erstellen eines Azure AD-Serverprinzipals (Anmeldung) mithilfe von SSMS

Der erste Azure AD-Serverprinzipal (Anmeldung) kann vom standardmäßigen SQL-Administratorkonto (Nicht-Azure AD), das ein `sysadmin` ist, oder vom Azure AD-Administrator für die verwaltete Instanz erstellt werden, die während des Bereitstellungsprozesses erstellt wurde. Weitere Informationen finden Sie unter [Bereitstellen eines Azure Active Directory-Administrators für die verwaltete SQL-Instanz](../database/authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance).

Beispiele für das Herstellen einer Verbindung mit der verwalteten SQL-Instanz finden Sie in den folgenden Artikeln:

- [Schnellstart: Konfigurieren einer Azure-VM für das Herstellen einer Verbindung mit einer verwalteten SQL-Instanz](connect-vm-instance-configure.md)
- [Schnellstart: Konfigurieren einer Point-to-Site-Verbindung von einem lokalen Computer mit einer verwalteten SQL-Instanz](point-to-site-p2s-configure.md)

1. Melden Sie sich über [SQL Server Management Studio](point-to-site-p2s-configure.md#connect-with-ssms) unter Verwendung eines SQL-Standardanmeldekontos (Nicht-Azure AD), das ein `sysadmin` oder ein Azure AD-Administrator für verwaltete SQL-Instanzen ist, bei Ihrer verwalteten Instanz an.

2. Klicken Sie im **Objekt-Explorer** mit der rechten Maustaste auf den Server, und wählen Sie **Neue Abfrage** aus.

3. Verwenden Sie im Abfragefenster die folgende Syntax, um eine Anmeldung für ein lokales Azure AD-Konto zu erstellen:

    ```sql
    USE master
    GO
    CREATE LOGIN login_name FROM EXTERNAL PROVIDER
    GO
    ```

    In diesem Beispiel wird eine Anmeldung für das Konto nativeuser@aadsqlmi.onmicrosoft.com erstellt.

    ```sql
    USE master
    GO
    CREATE LOGIN [nativeuser@aadsqlmi.onmicrosoft.com] FROM EXTERNAL PROVIDER
    GO
    ```

4. Wählen Sie auf der Symbolleiste die Option **Ausführen** aus, um die Anmeldung zu erstellen.

5. Führen Sie den folgenden T-SQL-Befehl aus, um die neu hinzugefügte Anmeldung zu überprüfen:

    ```sql
    SELECT *  
    FROM sys.server_principals;  
    GO
    ```

    ![Screenshot der Registerkarte „Ergebnisse“ im SSMS-Objekt-Explorer mit „name“, „principal_id“, „sid“, „type“ und „type_desc“ der neu hinzugefügten Anmeldung](./media/aad-security-configure-tutorial/native-login.png)

Weitere Informationen finden Sie unter [CREATE LOGIN (Transact-SQL)](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current).

## <a name="grant-permissions-to-create-logins"></a>Gewähren von Berechtigungen zum Erstellen von Anmeldungen

Um weitere Azure AD-Serverprinzipale (Anmeldungen) erstellen zu können, müssen dem Prinzipal (SQL oder Azure AD) SQL Server-Rollen oder -Berechtigungen zugewiesen werden.

### <a name="sql-authentication"></a>SQL-Authentifizierung

- Wenn es sich bei der Anmeldung um einen SQL-Prinzipal handelt, können nur Anmeldungen, die der Rolle `sysadmin` angehören, den Befehl „create“ verwenden, um Anmeldungen für ein Azure AD-Konto zu erstellen.

### <a name="azure-ad-authentication"></a>Azure AD-Authentifizierung

- Wenn Sie dem neu erstellten Azure AD-Serverprinzipal (Anmeldung) die Erstellung weiterer Anmeldungen für andere Azure AD-Benutzer, -Gruppen oder -Anwendungen ermöglichen möchten, müssen Sie der Anmeldung die Serverrolle `sysadmin` oder `securityadmin` zuweisen.
- Dem Azure AD-Serverprinzipal (Anmeldung) muss mindestens die Berechtigung **ALTER ANY LOGIN** gewährt werden, um weitere Azure AD-Serverprinzipale (Anmeldungen) erstellen zu können.
- Neu erstellten Azure AD-Serverprinzipalen (Anmeldungen) werden im Master standardmäßig folgende Berechtigungen gewährt: **CONNECT SQL** und **VIEW ANY DATABASE** .
- Die Serverrolle `sysadmin` kann innerhalb einer verwalteten Instanz zahlreichen Azure AD-Serverprinzipalen (Anmeldungen) zugewiesen werden.

So fügen Sie die Anmeldung der Serverrolle `sysadmin` hinzu:

1. Melden Sie sich erneut bei der verwalteten Instanz an, oder verwenden Sie die bestehende Verbindung mit dem Azure AD-Administrator oder SQL-Prinzipal vom Typ `sysadmin`.

1. Klicken Sie im **Objekt-Explorer** mit der rechten Maustaste auf den Server, und wählen Sie **Neue Abfrage** aus.

1. Weisen Sie dem Azure AD-Serverprinzipal (Anmeldung) die Serverrolle `sysadmin` zu. Verwenden Sie dazu die folgende T-SQL-Syntax:

    ```sql
    ALTER SERVER ROLE sysadmin ADD MEMBER login_name
    GO
    ```

    Im folgenden Beispiel wird die Serverrolle `sysadmin` der Anmeldung nativeuser@aadsqlmi.onmicrosoft.com zugewiesen:

    ```sql
    ALTER SERVER ROLE sysadmin ADD MEMBER [nativeuser@aadsqlmi.onmicrosoft.com]
    GO
    ```

## <a name="create-additional-azure-ad-server-principals-logins-using-ssms"></a>Erstellen zusätzlicher Azure AD-Serverprinzipale (Anmeldungen) mithilfe von SSMS

Nachdem der Azure AD-Serverprinzipal (Anmeldung) erstellt und mit Berechtigungen vom Typ `sysadmin` bereitgestellt wurde, können mit dieser Anmeldung unter Verwendung von **CREATE LOGIN** und der Klausel **FROM EXTERNAL PROVIDER** weitere Anmeldungen erstellt werden.

1. Stellen Sie über SQL Server Management Studio unter Verwendung des Azure AD-Serverprinzipals (Anmeldung) eine Verbindung mit der verwalteten Instanz her. Geben Sie den Hostnamen Ihrer verwalteten SQL-Instanz ein. Wenn Sie sich mit einem Azure AD-Konto anmelden, stehen für die Authentifizierung in SSMS drei Optionen zur Auswahl:

   - Active Directory: universell mit MFA-Unterstützung
   - Active Directory-Kennwortauthentifizierung
   - Integrierte Active Directory-Authentifizierung </br>

     ![Screenshot des Dialogfelds „Mit Server verbinden“ in SSMS, wobei „Active Directory: universell mit MFA-Unterstützung“ in der Dropdownliste „Authentifizierung“ ausgewählt ist](./media/aad-security-configure-tutorial/ssms-login-prompt.png)

     Weitere Informationen finden Sie unter [Universelle Authentifizierung (SSMS-Unterstützung für Multi-Factor Authentication)](../database/authentication-mfa-ssms-overview.md).

1. Wählen Sie **Active Directory: universell mit MFA-Unterstützung** aus. Daraufhin wird ein Anmeldefenster mit Multi-Factor Authentication angezeigt. Melden Sie sich mit Ihrem Azure AD-Kennwort an.

    ![Screenshot des Anmeldefenster mit Multi-Factor Authentication. Der Cursor befindet sich im Feld „Kennwort eingeben“.](./media/aad-security-configure-tutorial/mfa-login-prompt.png)

1. Klicken Sie im **Objekt-Explorer** von SSMS mit der rechten Maustaste auf den Server, und wählen Sie **Neue Abfrage** aus.
1. Verwenden Sie im Abfragefenster die folgende Syntax, um eine Anmeldung für ein weiteres Azure AD-Konto zu erstellen:

    ```sql
    USE master
    GO
    CREATE LOGIN login_name FROM EXTERNAL PROVIDER
    GO
    ```

    In diesem Beispiel wird eine Anmeldung für den Azure AD-Benutzer bob@aadsqlmi.net erstellt, dessen Domäne „aadsqlmi.net“ einem Verbund mit der Azure AD-Domäne „aadsqlmi.onmicrosoft.com“ angehört.

    Führen Sie den folgenden T-SQL-Befehl aus. Azure AD-Verbundkonten ersetzen bei der verwalteten SQL-Instanz lokale Windows-Anmeldungen und -Benutzer.

    ```sql
    USE master
    GO
    CREATE LOGIN [bob@aadsqlmi.net] FROM EXTERNAL PROVIDER
    GO
    ```

1. Erstellen Sie mithilfe der Syntax für [CREATE DATABASE](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-mi-current) eine Datenbank in der verwalteten Instanz. Diese Datenbank wird im nächsten Abschnitt zum Testen von Benutzeranmeldungen verwendet.
    1. Klicken Sie im **Objekt-Explorer** mit der rechten Maustaste auf den Server, und wählen Sie **Neue Abfrage** aus.
    1. Verwenden Sie im Abfragefenster die folgende Syntax, um eine Datenbank namens **MyMITestDB** zu erstellen:

        ```sql
        CREATE DATABASE MyMITestDB;
        GO
        ```

1. Erstellen Sie für eine Gruppe in Azure AD eine Anmeldung für die verwaltete SQL-Instanz. Die Gruppe muss bereits in Azure AD vorhanden sein, damit Sie der verwalteten SQL-Instanz die Anmeldung hinzufügen können. Weitere Informationen finden Sie unter [Erstellen einer Basisgruppe und Hinzufügen von Mitgliedern mit Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). Erstellen Sie eine Gruppe namens _mygroup_ , und fügen Sie ihr Mitglieder hinzu.

1. Öffnen Sie in SQL Server Management Studio ein neues Abfragefenster.

    In diesem Beispiel wird davon ausgegangen, dass in Azure AD eine Gruppe namens _mygroup_ vorhanden ist. Führen Sie den folgenden Befehl aus:

    ```sql
    USE master
    GO
    CREATE LOGIN [mygroup] FROM EXTERNAL PROVIDER
    GO
    ```

1. Melden Sie sich zu Testzwecken mit der neu erstellten Anmeldung oder Gruppe bei der verwalteten Instanz an. Öffnen Sie eine neue Verbindung mit der verwalteten Instanz, und verwenden Sie bei der Authentifizierung die neue Anmeldung.
1. Klicken Sie im **Objekt-Explorer** mit der rechten Maustaste auf den Server, und wählen Sie für die neue Verbindung **Neue Abfrage** aus.
1. Führen Sie den folgenden Befehl aus, um die Serverberechtigungen für den neu erstellten Azure AD-Serverprinzipal (Anmeldung) zu überprüfen:

      ```sql
      SELECT * FROM sys.fn_my_permissions (NULL, 'DATABASE')
      GO
      ```

> [!NOTE]
> Azure AD-Gastbenutzer werden für Anmeldungen für die verwaltete SQL-Instanz nur unterstützt, wenn sie als Teil einer Azure AD-Gruppe hinzugefügt werden. Bei einem Azure AD-Gastbenutzer handelt es sich um ein Konto, das aus einer anderen Azure AD-Instanz in die Azure AD-Instanz eingeladen wird, der die verwaltete Instanz angehört. So kann beispielweise joe@contoso.com (Azure AD-Konto) oder steve@outlook.com (Microsoft-Konto) einer Gruppe in der Azure AD-Instanz „aadsqlmi“ hinzugefügt werden. Nachdem die Benutzer einer Gruppe hinzugefügt wurden, kann für die Gruppe unter Verwendung der Syntax von **CREATE LOGIN** eine Anmeldung in der **Masterdatenbank** der verwalteten SQL-Instanz erstellt werden. Gastbenutzer, die dieser Gruppe angehören, können ihre aktuellen Anmeldungen (beispielsweise joe@contoso.com oder steve@outlook.com) verwenden, um eine Verbindung mit der verwalteten Instanz herzustellen.

## <a name="create-an-azure-ad-user-from-the-azure-ad-server-principal-login"></a>Erstellen eines Azure AD-Benutzers auf der Grundlage des Azure AD-Serverprinzipals (Anmeldung)

Die Autorisierung für einzelne Datenbanken funktioniert bei der verwalteten SQL-Instanz ganz ähnlich wie bei Datenbanken in SQL Server. Ein Benutzer kann auf der Grundlage einer vorhandenen Anmeldung in einer Datenbank erstellt und mit Berechtigungen für diese Datenbank ausgestattet oder einer Datenbankrolle hinzugefügt werden.

Nachdem wir nun eine Datenbank namens **MyMITestDB** sowie eine Anwendung mit Standardberechtigungen erstellt haben, können wir als Nächstes auf der Grundlage dieser Anmeldung einen Benutzer erstellen. Im Moment kann die Anmeldung zwar eine Verbindung mit der verwalteten Instanz herstellen und alle Datenbanken anzeigen, aber nicht mit den Datenbanken interagieren. Wenn Sie sich mit dem Azure AD-Konto mit den Standardberechtigungen anmelden und versuchen, die neu erstellte Datenbank zu erweitern, wird der folgende Fehler angezeigt:

![Screenshot einer Fehlermeldung vom SSMS-Objekt-Explorer mit dem Hinweis „Auf die Datenbank MyMITestDB kann nicht zugegriffen werden. (ObjectExplorer)“.](./media/aad-security-configure-tutorial/ssms-db-not-accessible.png)

Weitere Informationen zum Gewähren von Datenbankberechtigungen finden Sie unter [Erste Schritte mit Berechtigungen für die Datenbank-Engine](/sql/relational-databases/security/authentication-access/getting-started-with-database-engine-permissions).

### <a name="create-an-azure-ad-user-and-create-a-sample-table"></a>Erstellen eines Azure AD-Benutzers und einer Beispieltabelle

1. Melden Sie sich über SQL Server Management Studio unter Verwendung eines Kontos vom Typ `sysadmin` bei Ihrer verwalteten Instanz an.
1. Klicken Sie im **Objekt-Explorer** mit der rechten Maustaste auf den Server, und wählen Sie **Neue Abfrage** aus.
1. Verwenden Sie im Abfragefenster die folgende Syntax, um einen Azure AD-Benutzer auf der Grundlage eines Azure AD-Serverprinzipals (Anmeldung) zu erstellen:

    ```sql
    USE <Database Name> -- provide your database name
    GO
    CREATE USER user_name FROM LOGIN login_name
    GO
    ```

    Im folgenden Beispiel wird der Benutzer bob@aadsqlmi.net erstellt. Die Grundlage hierfür bildet bob@aadsqlmi.net:

    ```sql
    USE MyMITestDB
    GO
    CREATE USER [bob@aadsqlmi.net] FROM LOGIN [bob@aadsqlmi.net]
    GO
    ```

1. Ein Azure AD-Benutzer kann auch auf der Grundlage eines Azure AD-Serverprinzipals (Anmeldung) erstellt werden, bei dem es sich um eine Gruppe handelt.

    Im folgenden Beispiel wird eine Anmeldung für die Azure AD-Gruppe _mygroup_ erstellt, die in Ihrer Azure AD-Instanz vorhanden ist.

    ```sql
    USE MyMITestDB
    GO
    CREATE USER [mygroup] FROM LOGIN [mygroup]
    GO
    ```

    Alle Benutzer, die *mygroup* angehören, können auf die Datenbank **MyMITestDB** zugreifen.

    > [!IMPORTANT]
    > Wenn Sie einen Benutzer ( **USER** ) auf der Grundlage eines Azure AD-Serverprinzipals (Anmeldung) erstellen, müssen Sie als Benutzername den gleichen Anmeldenamen angeben wie in der Anmeldung ( **LOGIN** ).

    Weitere Informationen finden Sie unter [CREATE USER (Transact-SQL)](/sql/t-sql/statements/create-user-transact-sql?view=azuresqldb-mi-current).

1. Erstellen Sie in einem neuen Abfragefenster mithilfe des folgenden T-SQL-Befehls eine Testtabelle:

    ```sql
    USE MyMITestDB
    GO
    CREATE TABLE TestTable
    (
    AccountNum varchar(10),
    City varchar(255),
    Name varchar(255),
    State varchar(2)
    );
    ```

1. Erstellen Sie in SSMS eine Verbindung mit dem erstellten Benutzer. Sie werden feststellen, dass die zuvor durch den Systemadministrator (`sysadmin`) erstellte Tabelle **TestTable** nicht angezeigt wird. Dem Benutzer müssen Leseberechtigungen für die Datenbankdaten gewährt werden.

1. Die aktuellen Berechtigungen des Benutzers können mithilfe des folgenden Befehls überprüft werden:

    ```sql
    SELECT * FROM sys.fn_my_permissions('MyMITestDB','DATABASE')
    GO
    ```

### <a name="add-users-to-database-level-roles"></a>Hinzufügen von Benutzern zu Datenbankrollen

Damit dem Benutzer Daten in der Datenbank angezeigt werden, können wir ihm [Rollen auf Datenbankebene](/sql/relational-databases/security/authentication-access/database-level-roles) zuweisen.

1. Melden Sie sich über SQL Server Management Studio unter Verwendung eines Kontos vom Typ `sysadmin` bei Ihrer verwalteten Instanz an.

1. Klicken Sie im **Objekt-Explorer** mit der rechten Maustaste auf den Server, und wählen Sie **Neue Abfrage** aus.

1. Weisen Sie dem Azure AD-Benutzer die Datenbankrolle `db_datareader` zu. Verwenden Sie dazu die folgende T-SQL-Syntax:

    ```sql
    Use <Database Name> -- provide your database name
    ALTER ROLE db_datareader ADD MEMBER user_name
    GO
    ```

    Im folgenden Beispiel werden dem Benutzer bob@aadsqlmi.net und der Gruppe _mygroup_ Berechtigungen vom Typ `db_datareader` für die Datenbank **MyMITestDB** zugewiesen:

    ```sql
    USE MyMITestDB
    GO
    ALTER ROLE db_datareader ADD MEMBER [bob@aadsqlmi.net]
    GO
    ALTER ROLE db_datareader ADD MEMBER [mygroup]
    GO
    ```

1. Vergewissern Sie sich mithilfe des folgenden Befehls, dass der in der Datenbank erstellte Azure AD-Benutzer vorhanden ist:

    ```sql
    SELECT * FROM sys.database_principals
    GO
    ```

1. Erstellen Sie unter Verwendung des Benutzers, der der Rolle `db_datareader` hinzugefügt wurde, eine neue Verbindung mit der verwalteten Instanz.
1. Erweitern Sie die Datenbank im **Objekt-Explorer** , um die Tabelle anzuzeigen.

    ![Screenshot vom SSMS-Objekt-Explorer mit der die Ordnerstruktur für die Tabellen in MyMITestDB. Der Ordner „dbo.TestTable“ ist hervorgehoben.](./media/aad-security-configure-tutorial/ssms-test-table.png)

1. Öffnen Sie ein neues Abfragefenster, und führen Sie die folgende SELECT-Anweisung aus:

    ```sql
    SELECT *
    FROM TestTable
    ```

    Werden Daten aus der Tabelle angezeigt? Die Spalten sollten zurückgegeben werden.

    ![Screenshot der Registerkarte „Ergebnisse“ im SSMS-Objekt-Explorer mit den Tabellenspaltenüberschriften „AccountNum“, „City“, „Name“ und „State“](./media/aad-security-configure-tutorial/ssms-test-table-query.png)

## <a name="impersonate-azure-ad-server-level-principals-logins"></a>Annehmen der Identität von Azure AD-Serverebenenprinzipalen (Anmeldungen)

Die verwaltete SQL-Instanz unterstützt das Annehmen der Identität von Azure AD-Serverebenenprinzipalen (Anmeldungen).

### <a name="test-impersonation"></a>Testen des Identitätswechsels

1. Melden Sie sich über SQL Server Management Studio unter Verwendung eines Kontos vom Typ `sysadmin` bei Ihrer verwalteten Instanz an.

1. Klicken Sie im **Objekt-Explorer** mit der rechten Maustaste auf den Server, und wählen Sie **Neue Abfrage** aus.

1. Führen Sie im Abfragefenster den folgenden Befehl aus, um eine neue gespeicherte Prozedur zu erstellen:

    ```sql
    USE MyMITestDB
    GO  
    CREATE PROCEDURE dbo.usp_Demo  
    WITH EXECUTE AS 'bob@aadsqlmi.net'  
    AS  
    SELECT user_name();  
    GO
    ```

1. Verwenden Sie den folgenden Befehl, um sich zu vergewissern, dass es sich bei dem Benutzer, dessen Identität Sie beim Ausführen der gespeicherten Prozedur annehmen, um **bob\@aadsqlmi.net** handelt:

    ```sql
    Exec dbo.usp_Demo
    ```

1. Testen Sie den Identitätswechsel mithilfe der Anweisung „EXECUTE AS LOGIN“:

    ```sql
    EXECUTE AS LOGIN = 'bob@aadsqlmi.net'
    GO
    SELECT SUSER_SNAME()
    REVERT
    GO
    ```

> [!NOTE]
> Die folgenden Vorgänge für Azure AD-Prinzipale können nur von den SQL-Serverebenenprinzipalen (Anmeldungen) ausgeführt werden, die der Rolle `sysadmin` angehören:
>
> - EXECUTE AS USER
> - EXECUTE AS LOGIN

## <a name="use-cross-database-queries"></a>Verwenden datenbankübergreifender Abfragen

Datenbankübergreifende Abfragen werden für Azure AD-Konten mit Azure AD-Serverprinzipalen (Anmeldungen) unterstützt. Um eine datenbankübergreifende Abfrage mit einer Azure AD-Gruppe testen zu können, müssen wir eine weitere Datenbank und Tabelle erstellen. Falls Sie bereits über eine weitere Datenbank und Tabelle verfügen, können Sie die Erstellung überspringen.

1. Melden Sie sich über SQL Server Management Studio unter Verwendung eines Kontos vom Typ `sysadmin` bei Ihrer verwalteten Instanz an.
1. Klicken Sie im **Objekt-Explorer** mit der rechten Maustaste auf den Server, und wählen Sie **Neue Abfrage** aus.
1. Verwenden Sie im Abfragefenster den folgenden Befehl, um eine Datenbank namens **MyMITestDB2** und eine Tabelle namens **TestTable2** zu erstellen:

    ```sql
    CREATE DATABASE MyMITestDB2;
    GO
    USE MyMITestDB2
    GO
    CREATE TABLE TestTable2
    (
    EmpId varchar(10),
    FirstName varchar(255),
    LastName varchar(255),
    Status varchar(10)
    );
    ```

1. Führen Sie in einem neuen Abfragefenster den folgenden Befehl aus, um in der neuen Datenbank **MyMITestDB2** den Benutzer _mygroup_ zu erstellen und _mygroup_ SELECT-Berechtigungen für diese Datenbank zu gewähren:

    ```sql
    USE MyMITestDB2
    GO
    CREATE USER [mygroup] FROM LOGIN [mygroup]
    GO
    GRANT SELECT TO [mygroup]
    GO
    ```

1. Melden Sie sich über SQL Server Management Studio als Mitglied der Azure AD-Gruppe _mygroup_ bei der verwalteten Instanz an. Öffnen Sie ein neues Abfragefenster, und führen Sie die datenbankübergreifende SELECT-Anweisung aus:

    ```sql
    USE MyMITestDB
    SELECT * FROM MyMITestDB2..TestTable2
    GO
    ```

    Daraufhin sollten die Tabellenergebnisse aus **TestTable2** angezeigt werden.

## <a name="additional-supported-scenarios"></a>Weitere unterstützte Szenarien

- Für Azure AD-Serverprinzipale (Anmeldungen) werden SQL-Agent-Verwaltung und Auftragsausführungen unterstützt.
- Vorgänge für die Datenbanksicherung und -wiederherstellung können von Azure AD-Serverprinzipalen (Anmeldungen) ausgeführt werden.
- [Überwachung](auditing-configure.md) aller Anweisungen in Verbindung mit Azure AD-Serverprinzipalen (Anmeldungen) und Authentifizierungsereignissen.
- Dedizierte Administratorverbindung für Azure AD-Serverprinzipale (Anmeldungen), die der Serverrolle `sysadmin` angehören.
- Azure AD-Serverprinzipale (Anmeldungen) können mit dem [sqlcmd-Hilfsprogramm](/sql/tools/sqlcmd-utility) und mit dem Tool [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) verwendet werden.
- Anmeldetrigger werden für Anmeldeereignisse unterstützt, die von Azure AD-Serverprinzipalen (Anmeldungen) stammen.
- Service Broker und Datenbank-E-Mails können unter Verwendung von Azure AD-Serverprinzipalen (Anmeldungen) eingerichtet werden.

## <a name="next-steps"></a>Nächste Schritte

### <a name="enable-security-features"></a>Aktivieren der Sicherheitsfeatures

Unter [Sicherheitsfeatures für verwaltete SQL-Instanzen](sql-managed-instance-paas-overview.md#security-features) finden Sie eine umfassende Liste mit Schutzmöglichkeiten für Ihre Datenbank. Folgende Sicherheitsfeatures werden behandelt:

- [Überwachung verwalteter SQL-Instanzen](auditing-configure.md)
- [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine)
- [Bedrohungserkennung](threat-detection-configure.md)
- [Dynamische Datenmaskierung](/sql/relational-databases/security/dynamic-data-masking)
- [Sicherheit auf Zeilenebene](/sql/relational-databases/security/row-level-security)
- [Transparent Data Encryption (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)

### <a name="sql-managed-instance-capabilities"></a>Funktionen verwalteter SQL-Instanzen

Eine vollständige Übersicht über die Funktionen einer verwalteten SQL-Instanz finden Sie hier:

> [!div class="nextstepaction"]
> [Funktionen verwalteter SQL-Instanzen](sql-managed-instance-paas-overview.md)