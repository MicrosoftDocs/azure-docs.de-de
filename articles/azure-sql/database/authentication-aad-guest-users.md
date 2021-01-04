---
title: Erstellen von Azure AD-Gastbenutzern
description: Erfahren Sie, wie Sie Azure AD-Gastbenutzer erstellen und diese als Azure AD-Administrator festlegen, ohne Azure AD-Gruppen in Azure SQL-Datenbank, Azure SQL Managed Instance und Azure Synapse Analytics zu verwenden.
ms.service: sql-db-mi
ms.subservice: security
ms.custom: azure-synapse
ms.topic: how-to
author: shohamMSFT
ms.author: shohamd
ms.reviewer: vanto
ms.date: 07/27/2020
ms.openlocfilehash: 7a4d9fb9f803a497e84fa189d9a89c2d9097bb70
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92675056"
---
# <a name="create-azure-ad-guest-users-and-set-as-an-azure-ad-admin"></a>Erstellen von Azure AD-Gastbenutzern und Festlegen als Azure AD-Administrator

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

> [!NOTE]
> Dieser Artikel ist eine **Public Preview** .

Gastbenutzer in Azure Active Directory (Azure AD) sind Benutzer, die aus anderen Azure Active Directory-Verzeichnissen oder von außerhalb in das aktuelle Azure AD importiert wurden. Gastbenutzer können z. B. Benutzer aus anderen Azure Active Directory-Verzeichnissen oder Konten wie *\@outlook.com* , *\@hotmail.com* , *\@live.com* oder *\@gmail.com* einschließen. In diesem Artikel wird veranschaulicht, wie ein Azure AD-Gastbenutzer erstellt und als Azure AD-Administrator für den logischen Azure SQL-Server festgelegt wird, ohne dass dieser Gastbenutzer einer Gruppe in Azure AD angehören muss.

## <a name="feature-description"></a>Featurebeschreibung

Dieses Feature hebt die aktuelle Einschränkung auf, durch die Gastbenutzern das Herstellen einer Verbindung mit Azure SQL-Datenbank, SQL Managed Instance oder Azure Synapse Analytics nur dann ermöglicht wird, wenn sie Mitglied einer Gruppe sind, die in Azure AD erstellt wurde. Die Gruppe muss manuell mithilfe der Anweisung [CREATE USER (Transact-SQL)](/sql/t-sql/statements/create-user-transact-sql) einem Benutzer in einer bestimmten Datenbank zugeordnet werden. Nachdem ein Datenbankbenutzer für die Azure AD-Gruppe erstellt wurde, die den Gastbenutzer enthält, kann sich der Gastbenutzer mit Azure Active Directory und MFA-Authentifizierung bei der Datenbank anmelden. Im Rahmen dieser **Public Preview** können Gastbenutzer erstellt werden, die dann direkt eine Verbindung mit SQL-Datenbank, SQL Managed Instance oder Azure Synapse herstellen können, ohne dass sie zuvor einer Azure AD-Gruppe hinzugefügt werden müssen und dann ein Datenbankbenutzer für diese Azure AD-Gruppe erstellt werden muss.

Als Teil dieses Features haben Sie auch die Möglichkeit, den Azure AD-Gastbenutzer direkt als AD-Administrator für den logischen Azure SQL-Server festzulegen. Die vorhandene Funktionalität, bei der der Gastbenutzer Teil einer Azure AD-Gruppe sein können und diese Gruppe dann als Azure AD-Administrator für den logischen Azure SQL-Server festgelegt werden kann, wird hiervon nicht beeinflusst. Gastbenutzer in der Datenbank, die einer Azure AD-Gruppe angehören, sind von dieser Änderung ebenfalls nicht betroffen.

Weitere Informationen zur bestehenden Unterstützung für Gastbenutzer mithilfe von Azure AD-Gruppen finden Sie unter [Verwenden der mehrstufigen Azure Active Directory-Authentifizierung](authentication-mfa-ssms-overview.md).

## <a name="prerequisite"></a>Voraussetzung

- Das Modul [Az.Sql 2.9.0](https://www.powershellgallery.com/packages/Az.Sql/2.9.0) oder höher ist erforderlich, wenn Sie PowerShell verwenden, um einen Gastbenutzer als Azure AD-Administrator für den logischen Azure SQL-Server einzurichten.

## <a name="create-database-user-for-azure-ad-guest-user"></a>Erstellen von Datenbankbenutzern für Azure AD-Gastbenutzer 

Führen Sie die folgenden Schritte aus, um einen Datenbankbenutzer mit einem Azure AD-Gastbenutzer zu erstellen.

### <a name="create-guest-user-in-sql-database-and-azure-synapse"></a>Erstellen von Gastbenutzern in SQL-Datenbank und Azure Synapse

1. Stellen Sie sicher, dass der Gastbenutzer (z. B. `user1@gmail.com`) bereits in Azure AD hinzugefügt und ein Azure AD-Administrator für den Datenbankserver festgelegt wurde. Ein Azure AD-Administrator ist für die Azure Active Directory-Authentifizierung erforderlich.

1. Stellen Sie als Azure AD-Administrator oder Azure AD-Benutzer mit ausreichenden SQL-Berechtigungen zum Erstellen von Benutzern eine Verbindung mit SQL-Datenbank her, und führen Sie den folgenden Befehl für die Datenbank aus, in der der Gastbenutzer hinzugefügt werden soll:

    ```sql
    CREATE USER [user1@gmail.com] FROM EXTERNAL PROVIDER
    ```

1. Es sollte jetzt ein Datenbankbenutzer für den Gastbenutzer `user1@gmail.com` erstellt worden sein.

1. Führen Sie den folgenden Befehl aus, um zu überprüfen, ob der Datenbankbenutzer erfolgreich erstellt wurde:

    ```sql
    SELECT * FROM sys.database_principals
    ```

1. Trennen Sie die Verbindung mit der Datenbank, und melden Sie sich bei der Datenbank als Gastbenutzer `user1@gmail.com` über [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) mithilfe der Authentifizierungsmethode **Azure Active Directory: universell mit MFA** an. Weitere Informationen finden Sie unter [Verwenden der mehrstufigen Azure Active Directory-Authentifizierung](authentication-mfa-ssms-overview.md).

### <a name="create-guest-user-in-sql-managed-instance"></a>Erstellen von Gastbenutzern in SQL Managed Instance

> [!NOTE]
> SQL Managed Instance unterstützt Anmeldungen von Azure AD-Benutzern sowie von Benutzern eigenständiger Azure AD-Datenbanken. In den folgenden Schritten wird gezeigt, wie Sie eine Anmeldung und einen Benutzer für einen Azure AD-Gastbenutzer in SQL Managed Instance erstellen. Sie können auch einen [Benutzer einer eigenständigen Datenbank](/sql/relational-databases/security/contained-database-users-making-your-database-portable) in SQL Managed Instance erstellen, indem Sie die Methode im Abschnitt [Erstellen von Gastbenutzern in SQL-Datenbank und Azure Synapse](#create-guest-user-in-sql-database-and-azure-synapse) verwenden.

1. Stellen Sie sicher, dass der Gastbenutzer (z. B. `user1@gmail.com`) bereits in Azure AD hinzugefügt und ein Azure AD-Administrator für den SQL Managed Instance-Server festgelegt wurde. Ein Azure AD-Administrator ist für die Azure Active Directory-Authentifizierung erforderlich.

1. Stellen Sie als Azure AD-Administrator oder Azure AD-Benutzer mit ausreichenden SQL-Berechtigungen zum Erstellen von Benutzern eine Verbindung mit dem SQL Managed Instance-Server her, und führen Sie den folgenden Befehl für die Datenbank `master` aus, um eine Anmeldung für den Gastbenutzer zu erstellen:

    ```sql
    CREATE LOGIN [user1@gmail.com] FROM EXTERNAL PROVIDER
    ```

1. Es sollte jetzt eine Anmeldung für den Gastbenutzer `user1@gmail.com` in der Datenbank `master` erstellt worden sein.

1. Führen Sie den folgenden Befehl aus, um zu überprüfen, ob die Anmeldung erfolgreich erstellt wurde:

    ```sql
    SELECT * FROM sys.server_principals
    ```

1. Führen Sie den folgenden Befehl für die Datenbank aus, in der der Gastbenutzer hinzugefügt werden soll: 

    ```sql
    CREATE USER [user1@gmail.com] FROM LOGIN [user1@gmail.com]
    ```

1. Es sollte jetzt ein Datenbankbenutzer für den Gastbenutzer `user1@gmail.com` erstellt worden sein.

1. Trennen Sie die Verbindung mit der Datenbank, und melden Sie sich bei der Datenbank als Gastbenutzer `user1@gmail.com` über [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) mithilfe der Authentifizierungsmethode **Azure Active Directory: universell mit MFA** an. Weitere Informationen finden Sie unter [Verwenden der mehrstufigen Azure Active Directory-Authentifizierung](authentication-mfa-ssms-overview.md).

## <a name="setting-a-guest-user-as-an-azure-ad-admin"></a>Festlegen eines Gastbenutzers als Azure AD-Administrator

Führen Sie die folgenden Schritte aus, um einen Azure AD-Gastbenutzer als Azure AD-Administrator für den logischen SQL-Server festzulegen.

### <a name="set-azure-ad-admin-for-sql-database-and-azure-synapse"></a>Festlegen eines Azure AD-Administrators für SQL-Datenbank und Azure Synapse

1. Vergewissern Sie sich, dass der Gastbenutzer (z. B. `user1@gmail.com`) bereits in Azure AD hinzugefügt wurde.

1. Führen Sie den folgenden PowerShell-Befehl aus, um den Gastbenutzer als Azure AD-Administrator für Ihren logischen Azure SQL-Server hinzuzufügen:

    - Ersetzen Sie `<ResourceGroupName>` durch den Namen Ihrer Azure-Ressourcengruppe, die den logischen Azure SQL-Server enthält.
    - Ersetzen Sie `<ServerName>` durch den Namen Ihres logischen Azure SQL-Servers. Wenn der Servername `myserver.database.windows.net` ist, ersetzen Sie `<Server Name>` durch `myserver`.
    - Ersetzen Sie `<DisplayNameOfGuestUser>` durch den Gastbenutzernamen.

    ```powershell
    Set-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName <ResourceGroupName> -ServerName <ServerName> -DisplayName <DisplayNameOfGuestUser>
    ```

    Sie können auch den Azure CLI-Befehl [az sql server ad-admin](/cli/azure/sql/server/ad-admin) verwenden, um den Gastbenutzer als Azure AD-Administrator für Ihren logischen Azure SQL-Server festzulegen.

### <a name="set-azure-ad-admin-for-sql-managed-instance"></a>Festlegen eines Azure AD-Administrators für SQL Managed Instance

1. Vergewissern Sie sich, dass der Gastbenutzer (z. B. `user1@gmail.com`) bereits in Azure AD hinzugefügt wurde.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und navigieren Sie zu Ihrer **Azure Active Directory** -Ressource. Navigieren Sie unter **Verwalten** zum Bereich **Benutzer** . Wählen Sie den Gastbenutzer aus, und notieren Sie die `Object ID`. 

1. Führen Sie den folgenden PowerShell-Befehl aus, um den Gastbenutzer als Azure AD-Administrator für SQL Managed Instance hinzuzufügen:

    - Ersetzen Sie `<ResourceGroupName>` durch den Namen Ihrer Azure-Ressourcengruppe, die SQL Managed Instance enthält.
    - Ersetzen Sie `<ManagedInstanceName>` durch den Namen Ihrer Instanz von SQL Managed Instance.
    - Ersetzen Sie `<DisplayNameOfGuestUser>` durch den Gastbenutzernamen.
    - Ersetzen Sie `<AADObjectIDOfGuestUser>` durch die zuvor notierte `Object ID`.

    ```powershell
    Set-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName <ResourceGroupName> -InstanceName "<ManagedInstanceName>" -DisplayName <DisplayNameOfGuestUser> -ObjectId <AADObjectIDOfGuestUser>
    ```

    Sie können auch den Azure CLI-Befehl [az sql mi ad-admin](/cli/azure/sql/mi/ad-admin) verwenden, um den Gastbenutzer als Azure AD-Administrator für SQL Managed Instance festzulegen.

## <a name="limitations"></a>Einschränkungen

Es gibt eine Einschränkung im Azure-Portal, die verhindert, dass ein Azure AD-Gastbenutzer als Azure AD-Administrator für SQL Managed Instance ausgewählt wird. Für Gastkonten außerhalb Ihres Azure AD-Verzeichnisses wie *\@outlook.com* , *\@hotmail.com* , *\@live.com* oder *\@gmail.com* werden diese Konten zwar im Selektor für den AD-Administrator angezeigt, sie sind jedoch abgeblendet und können nicht ausgewählt werden. Verwenden Sie die oben aufgeführten [PowerShell- oder CLI-Befehle](#setting-a-guest-user-as-an-azure-ad-admin), um den Azure AD-Administrator festzulegen. Alternativ kann auch eine Azure AD-Gruppe, die den Gastbenutzer enthält, als Azure AD-Administrator für die SQL Managed Instance festgelegt werden.

Diese Funktion wird für SQL Managed Instance aktiviert, bevor dieses Feature allgemein verfügbar wird.

## <a name="next-steps"></a>Nächste Schritte

- [Konfigurieren und Verwalten der Azure Active Directory-Authentifizierung mit Azure SQL](authentication-aad-configure.md)
- [Verwenden der mehrstufigen Azure Active Directory-Authentifizierung](authentication-mfa-ssms-overview.md)
- [CREATE USER (Transact-SQL)](/sql/t-sql/statements/create-user-transact-sql)