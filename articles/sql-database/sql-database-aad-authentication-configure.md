---
title: Konfigurieren der Azure Active Directory-Authentifizierung – SQL| Microsoft-Dokumentation
description: Es wird beschrieben, wie Sie unter Verwendung der Azure Active Directory-Authentifizierung eine Verbindung mit SQL-Datenbank, der verwalteten SQL-Datenbank-Instanz und SQL Data Warehouse herstellen – nachdem Sie Azure AD konfiguriert haben.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: data warehouse
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto, carlrab
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: abb4a43176026fca5a80409ade13af1f8f96d9f1
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60390484"
---
# <a name="configure-and-manage-azure-active-directory-authentication-with-sql"></a>Konfigurieren und Verwalten der Azure Active Directory-Authentifizierung mit SQL

In diesem Artikel erfahren Sie, wie Sie ein Azure AD erstellen und auffüllen und dann dieses Azure AD mit Azure [SQL-Datenbank](sql-database-technical-overview.md), einer [verwalteten Instanz](sql-database-managed-instance.md) und [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) verwenden. Eine Übersicht finden Sie unter [Authentifizierung über Azure Active Directory](sql-database-aad-authentication.md).

> [!NOTE]
> Dieser Artikel gilt für den Azure SQL-Datenbankserver sowie für Datenbanken von SQL-Datenbank und SQL Data Warehouse, die auf dem Azure SQL-Datenbankserver erstellt werden. Der Einfachheit halber wird nur SQL-Datenbank verwendet, wenn sowohl SQL-Datenbank als auch SQL Data Warehouse gemeint sind.
> [!IMPORTANT]  
> Das Herstellen einer Verbindung mit einer SQL Server-Instanz, die auf einer Azure-VM ausgeführt wird, wird für ein Azure Active Directory-Konto nicht unterstützt. Verwenden Sie stattdessen ein Active Directory-Domänenkonto.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Das PowerShell Azure Resource Manager-Modul wird von der Azure SQL-Datenbank weiterhin unterstützt, aber alle zukünftigen Entwicklungen erfolgen für das Az.Sql-Modul. Informationen zu diesen Cmdlets finden Sie unter [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Die Argumente für die Befehle im Az- und den AzureRm-Modulen sind im Wesentlichen identisch.

## <a name="create-and-populate-an-azure-ad"></a>Erstellen und Auffüllen von Azure AD

Erstellen Sie ein Azure Active Directory-Verzeichnis, und füllen Sie es mit Benutzern und Gruppen. Azure AD kann die verwaltete Azure AD-Anfangsdomäne sein. Azure AD kann auch ein lokaler Active Directory Domain Services im Verbund mit Azure AD sein.

Weitere Informationen finden Sie unter [Integrieren Ihrer lokalen Identitäten in Azure Active Directory](../active-directory/hybrid/whatis-hybrid-identity.md), [Hinzufügen eines benutzerdefinierten Domänennamens zu Azure AD](../active-directory/active-directory-domains-add-azure-portal.md), [Microsoft Azure unterstützt jetzt den Verbund mit Windows Server Active Directory](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/), [Verwalten Ihres Azure AD-Verzeichnisses](../active-directory/fundamentals/active-directory-administer.md)[ sowie unter Verwalten von Azure AD mit Windows PowerShell](/powershell/azure/overview) und [erforderliche Ports und Protokolle für die Hybrid-Identität](../active-directory/hybrid/reference-connect-ports.md).

## <a name="associate-or-add-an-azure-subscription-to-azure-active-directory"></a>Zuweisen oder Hinzufügen eines Azure-Abonnements zu Azure Active Directory

1. Ordnen Sie Ihr Azure-Abonnement Azure Active Directory zu, indem Sie das Verzeichnis für das Azure-Abonnement, das die Datenbank hostet, als vertrauenswürdiges Verzeichnis kennzeichnen. Weitere Informationen finden Sie unter [Beziehung zwischen Azure-Abonnements und Azure Active Directory](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).
2. Verwenden Sie den Verzeichnisumschalter im Azure-Portal, um zu dem der Domäne zugeordneten Abonnement zu wechseln.

   **Weitere Informationen:** Jedes Azure-Abonnement weist eine Vertrauensstellung mit einer Azure AD-Instanz auf. Dies bedeutet, dass es diesem Verzeichnis bei der Authentifizierung von Benutzern, Diensten und Geräten vertraut. Mehrere Abonnements können dem gleichen Verzeichnis vertrauen, ein Abonnement vertraut jedoch nur einem Verzeichnis. Diese Vertrauensstellung, die ein Abonnement mit einem Verzeichnis aufweist, unterscheidet sich von der Beziehung, die ein Abonnement mit allen anderen Ressourcen in Azure (Websites, Datenbanken usw.) hat. Diese ähneln eher den untergeordneten Ressourcen eines Abonnements. Wenn ein Abonnement abläuft, wird der Zugriff auf die anderen Ressourcen, die dem Abonnement zugeordnet sind, ebenfalls beendet. Das Verzeichnis verbleibt jedoch in Azure, und Sie können ihm ein anderes Abonnement zuordnen und weiterhin die Benutzer des Verzeichnisses verwalten. Weitere Informationen zu Ressourcen finden Sie unter [Grundlegendes zur Zugriffssteuerung in Azure sowie zur Integration in Azure Active Directory](../active-directory/active-directory-b2b-admin-add-users.md). Weitere Informationen zu dieser Vertrauensstellung finden Sie unter [Zuweisen oder Hinzufügen eines Azure-Abonnements zu Azure Active Directory](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="create-an-azure-ad-administrator-for-azure-sql-server"></a>Erstellen eines Azure AD-Administrators für die Azure SQL Server-Instanz

Jede Azure SQL Server-Instanz (mit Hosten von SQL-Datenbank oder SQL Data Warehouse) wird zunächst mit einem einzelnen Serveradministratorkonto konfiguriert, das als Administrator für die gesamte Azure SQL Server-Instanz fungiert. Anschließend muss ein zweiter SQL Server-Administrator erstellt werden, bei dem es sich um ein Azure AD-Konto handelt. Dieser Prinzipal wird als eigenständiger Datenbankbenutzer in der Masterdatenbank erstellt. Als Administratoren sind die Serveradministratorkonten Mitglieder der Rolle **db_owner** in jeder Benutzerdatenbank. Der Zugriff auf alle Benutzerdatenbanken erfolgt als Benutzer **dbo**. Weitere Informationen zu den Serveradministratorkonten finden Sie unter [Verwalten von Datenbanken und Anmeldungen in Azure SQL-Datenbank](sql-database-manage-logins.md).

Bei Verwendung von Azure Active Directory mit Georeplikation muss der Azure Active Directory-Administrator sowohl für den primären als auch für die sekundären Server konfiguriert werden. Wenn ein Server über keinen Azure Active Directory-Administrator verfügt, erhalten Benutzer bei der Azure Active Directory-Anmeldung die Fehlermeldung „Keine Verbindung“.

> [!NOTE]
> Benutzer, die nicht auf einem Azure AD-Konto basieren (hierzu gehört auch das Administratorkonto für die Azure SQL Server-Instanz) können keine Azure AD-basierten Benutzer erstellen, da sie keine Berechtigung zum Überprüfen der vorgeschlagenen Datenbankbenutzer mit Azure AD besitzen.

## <a name="provision-an-azure-active-directory-administrator-for-your-managed-instance"></a>Bereitstellen eines Azure Active Directory-Administrators für Ihre verwaltete SQL-Datenbank-Instanz

> [!IMPORTANT]
> Führen Sie diese Schritte nur aus, wenn Sie eine verwaltete SQL-Datenbank-Instanz bereitstellen. Dieser Vorgang kann nur von einem globalen bzw. Unternehmensadministrator in Azure AD durchgeführt werden. Mit den folgenden Schritten wird der Prozess zum Gewähren von Berechtigungen für Benutzer mit unterschiedlichen Berechtigungen für das Verzeichnis beschrieben.

Für Ihre verwaltete SQL-Datenbank-Instanz sind Leseberechtigungen für Azure AD erforderlich, damit Aufgaben wie die Authentifizierung von Benutzern über die Mitgliedschaft in Sicherheitsgruppen oder die Erstellung neuer Benutzer erfolgreich durchgeführt werden können. Damit dies funktioniert, müssen Sie für die verwaltete SQL-Datenbank-Instanz Leseberechtigungen für Azure AD gewähren. Hierfür gibt es zwei Möglichkeiten: das Portal und PowerShell. Die folgenden Schritte gelten für beide Methoden.

1. Wählen Sie im Azure-Portal in der oberen rechten Ecke Ihre Verbindung aus, um eine Dropdownliste mit möglichen Active Directory-Verzeichnissen zu öffnen.
2. Wählen Sie das richtige Active Directory-Verzeichnis als Standardeinstellung für Azure AD aus.

   In diesem Schritt wird das Abonnement, das Active Directory zugeordnet ist, mit der verwalteten SQL-Datenbank-Instanz verknüpft. So wird sichergestellt, dass dasselbe Abonnement sowohl für Azure AD als auch für die verwaltete SQL-Datenbank-Instanz verwendet wird.
3. Navigieren Sie zu „Verwaltete Instanz“, und wählen Sie die gewünschte Instanz aus, die Sie für die Azure AD-Integration verwenden möchten.

   ![aad](./media/sql-database-aad-authentication/aad.png)

4. Wählen Sie oben auf der Seite „Active Directory-Administrator“ das Banner aus, und erteilen Sie dem aktuellen Benutzer die Berechtigung. Wenn Sie an Azure AD als globaler bzw. Unternehmensadministrator angemeldet sind, können Sie dies über das Azure-Portal durchführen oder PowerShell mit dem nachfolgenden Skript verwenden.

    ![Erteilen von Berechtigungen – Portal](./media/sql-database-aad-authentication/grant-permissions.png)

    ```powershell
    # Gives Azure Active Directory read permission to a Service Principal representing the Managed Instance.
    # Can be executed only by a "Company Administrator" or "Global Administrator" type of user.

    $aadTenant = "<YourTenantId>" # Enter your tenant ID
    $managedInstanceName = "MyManagedInstance"

    # Get Azure AD role "Directory Users" and create if it doesn't exist
    $roleName = "Directory Readers"
    $role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq $roleName}
    if ($role -eq $null) {
        # Instantiate an instance of the role template
        $roleTemplate = Get-AzureADDirectoryRoleTemplate | Where-Object {$_.displayName -eq $roleName}
        Enable-AzureADDirectoryRole -RoleTemplateId $roleTemplate.ObjectId
        $role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq $roleName}
    }

    # Get service principal for managed instance
    $roleMember = Get-AzureADServicePrincipal -SearchString $managedInstanceName
    $roleMember.Count
    if ($roleMember -eq $null)
    {
        Write-Output "Error: No Service Principals with name '$    ($managedInstanceName)', make sure that managedInstanceName parameter was     entered correctly."
        exit
    }
    if (-not ($roleMember.Count -eq 1))
    {
        Write-Output "Error: More than one service principal with name pattern '$    ($managedInstanceName)'"
        Write-Output "Dumping selected service principals...."
        $roleMember
        exit
    }

    # Check if service principal is already member of readers role
    $allDirReaders = Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
    $selDirReader = $allDirReaders | where{$_.ObjectId -match     $roleMember.ObjectId}

    if ($selDirReader -eq $null)
    {
        # Add principal to readers role
        Write-Output "Adding service principal '$($managedInstanceName)' to     'Directory Readers' role'..."
        Add-AzureADDirectoryRoleMember -ObjectId $role.ObjectId -RefObjectId     $roleMember.ObjectId
        Write-Output "'$($managedInstanceName)' service principal added to     'Directory Readers' role'..."

        #Write-Output "Dumping service principal '$($managedInstanceName)':"
        #$allDirReaders = Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
        #$allDirReaders | where{$_.ObjectId -match $roleMember.ObjectId}
    }
    else
    {
        Write-Output "Service principal '$($managedInstanceName)' is already     member of 'Directory Readers' role'."
    }
    ```

5. Nachdem der Vorgang erfolgreich abgeschlossen wurde, wird in der oberen rechten Ecke die folgende Benachrichtigung angezeigt:

    ![Erfolg](./media/sql-database-aad-authentication/success.png)

6. Jetzt können Sie Ihren Azure AD-Administrator für die verwaltete Instanz auswählen. Wählen Sie hierzu auf der Seite „Active Directory-Administrator“ **Administrator festlegen** aus.

    ![Administrator festlegen](./media/sql-database-aad-authentication/set-admin.png)

7. Suchen Sie auf der Azure AD-Administratorseite nach einem Benutzer, wählen Sie den Benutzer oder die Gruppe aus, den bzw. die Sie als Administrator festlegen möchten, und wählen Sie dann **Auswählen** aus.

   Auf der Seite „Active Directory-Administrator“ werden alle Mitglieder und Gruppen in Ihrem Active Directory-Verzeichnis angezeigt. Abgeblendete Benutzer oder Gruppen können nicht ausgewählt werden, da sie nicht als Azure AD-Administratoren unterstützt werden. Eine Liste mit den unterstützten Administratoren finden Sie unter [Features und Einschränkungen von Azure AD](sql-database-aad-authentication.md#azure-ad-features-and-limitations). Die rollenbasierte Zugriffskontrolle (Role-Based Access Control, RBAC) gilt nur für das Azure-Portal und wird nicht an SQL Server weitergegeben.

    ![Administrator hinzufügen](./media/sql-database-aad-authentication/add-admin.png)

8. Wählen Sie oben auf der Seite „Active Directory-Administrator“ **Speichern** aus.

    ![Speichern](./media/sql-database-aad-authentication/save.png)

    Der Vorgang zum Ändern des Administrators kann einige Minuten in Anspruch nehmen. Anschließend wird der neue Administrator im Feld „Active Directory-Administrator“ angezeigt.

Nach der Bereitstellung eines Azure AD-Administrators für die verwaltete Instanz können Sie mit dem Erstellen von Azure AD-Serverprinzipalen (Anmeldungen) (**öffentliche Vorschau**) anhand der <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">CREATE LOGIN</a>-Syntax beginnen. Weitere Informationen finden Sie in der [Übersicht über verwaltete Instanzen](sql-database-managed-instance.md#azure-active-directory-integration).

> [!TIP]
> Wenn Sie später einen Administrator entfernen möchten, wählen Sie oben auf der Seite „Active Directory-Administrator“ **Administrator entfernen** und anschließend **Speichern** aus.

## <a name="provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server"></a>Bereitstellen eines Azure Active Directory-Administrators für Ihren Azure SQL-Datenbank-Server

> [!IMPORTANT]
> Führen Sie die folgenden Schritte nur aus, wenn Sie einen Azure SQL-Datenbank-Server oder eine Data Warehouse-Instanz bereitstellen.

Die beiden folgenden Verfahren zeigen Ihnen, wie Sie einen Azure Active Directory-Administrator für Ihre Azure SQL Server-Instanz im Azure-Portal und mithilfe von PowerShell bereitstellen.

### <a name="azure-portal"></a>Azure-Portal

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) in der oberen rechten Ecke Ihre Verbindung aus, um eine Dropdownliste mit möglichen Active Directory-Verzeichnissen zu öffnen. Wählen Sie das richtige Active Directory-Verzeichnis als Standardeinstellung für Azure AD aus. Durch diesen Schritt wird die mit dem Abonnement verbundene Active Directory-Instanz mit der Azure SQL Server-Instanz verknüpft. So wird sichergestellt, dass das gleiche Abonnement sowohl für Azure AD als auch für SQL Server verwendet wird. (Die Azure SQL Server-Instanz kann entweder Azure SQL-Datenbank oder Azure SQL Data Warehouse hosten.) ![choose-ad][8]

2. Wählen Sie in dem linken Banner **Alle Dienste** aus, und geben Sie im Filtertyp **SQL Server** ein. Wählen Sie **SQL Server** aus.

    ![sqlservers.png](media/sql-database-aad-authentication/sqlservers.png)

    >[!NOTE]
    > Auf dieser Seite können Sie vor der Auswahl von **SQL Server** den **Stern** neben dem Namen auswählen, um die Kategorie den *Favoriten* hinzuzufügen, und **SQL Server** der linken Navigationsleiste hinzufügen.

3. Wählen Sie auf der Seite **SQL Server** die Option **Active Directory-Administrator** aus.
4. Wählen Sie auf der Seite **Active Directory-Administrator** die Option **Administrator festlegen** aus.  ![select active directory](./media/sql-database-aad-authentication/select-active-directory.png)  

5. Suchen Sie auf der Seite **Administrator hinzufügen** nach einem Benutzer, wählen Sie den Benutzer oder die Gruppe aus, den bzw. die Sie als Administrator festlegen möchten, und wählen Sie dann **Auswählen** aus. Auf der Seite „Active Directory-Administrator“ werden alle Mitglieder und Gruppen in Ihrem Active Directory-Verzeichnis angezeigt. (Auf dem Blatt „Active Directory-Administrator“ werden alle Mitglieder und Gruppen in Ihrem Active Directory-Verzeichnis angezeigt. (Siehe Liste mit unterstützten Administratoren im Abschnitt **Funktionen und Einschränkungen von Azure AD** von [Verwenden der Azure Active Directory-Authentifizierung für die Authentifizierung mit SQL-Datenbank oder SQL Data Warehouse](sql-database-aad-authentication.md).) Die rollenbasierte Zugriffskontrolle (Role-based Access Control, RBAC) gilt nur für das Portal und wird nicht an SQL Server weitergegeben.
    ![Administrator auswählen](./media/sql-database-aad-authentication/select-admin.png)  

6. Wählen Sie oben auf der Seite **Active Directory-Administrator** **SPEICHERN** aus.
    ![Administrator speichern](./media/sql-database-aad-authentication/save-admin.png)

Der Vorgang zum Ändern des Administrators kann einige Minuten in Anspruch nehmen. Anschließend wird der neue Administrator im Feld **Active Directory-Administrator** angezeigt.

   > [!NOTE]
   > Beim Einrichten des Azure AD-Administrators darf der Name des neuen Administrators (Benutzer oder Gruppe) in der virtuellen Masterdatenbank noch nicht als SQL Server-Authentifizierungsbenutzer vorhanden sein. Wenn er vorhanden ist, schlägt die Einrichtung des Azure AD-Administrators fehl, wobei die Erstellung rückgängig gemacht und angegeben wird, dass ein solcher Administrator (Name) bereits vorhanden ist. Da dieser SQL Server-Authentifizierungsbenutzer nicht Teil von Azure AD ist, sind alle Versuche erfolglos, unter der Azure AD-Authentifizierung eine Verbindung mit dem Server herzustellen.

Wenn Sie später einen Administrator entfernen möchten, wählen Sie oben auf der Seite **Active Directory-Administrator** **Administrator entfernen** und anschließend **Speichern** aus.

### <a name="powershell"></a>PowerShell

Zum Ausführen von PowerShell-Cmdlets muss Azure PowerShell installiert sein und ausgeführt werden. Weitere Informationen finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](/powershell/azure/overview). Um einen Azure AD-Administrator bereitzustellen, führen Sie die folgenden Azure PowerShell-Befehle aus:

- Connect-AzAccount
- Select-AzSubscription

Verwenden Sie folgende Cmdlets zum Bereitstellen und Verwalten des Azure AD-Administrators:

| Cmdlet-Name | BESCHREIBUNG |
| --- | --- |
| [Set-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/set-azsqlserveractivedirectoryadministrator) |Stellt einen Azure Active Directory-Administrator für die Azure SQL Server-Instanz oder Azure SQL Data Warehouse bereit. (Muss aus dem aktuellen Abonnement stammen.) |
| [Remove-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/remove-azsqlserveractivedirectoryadministrator) |Entfernt einen Azure Active Directory-Administrator für die Azure SQL Server-Instanz oder Azure SQL Data Warehouse. |
| [Get-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/get-azsqlserveractivedirectoryadministrator) |Gibt Informationen zu dem Azure Active Directory-Administrator zurück, der aktuell für die Azure SQL Server-Instanz oder Azure SQL Data Warehouse konfiguriert ist. |

Verwenden Sie den PowerShell-Befehl „get-help“, um weitere Informationen zu diesen Befehlen anzuzeigen, z.B. ``get-help Set-AzSqlServerActiveDirectoryAdministrator``.

Das folgende Skript stellt eine Azure AD-Administratorengruppe namens **DBA_Group** (Objekt-ID `40b79501-b343-44ed-9ce7-da4c8cc7353f`) für den Server **demo_server** in einer Ressourcengruppe mit dem Namen **Group-23** bereit:

```powershell
Set-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23"
-ServerName "demo_server" -DisplayName "DBA_Group"
```

Der Eingabeparameter **DisplayName** akzeptiert entweder den Azure AD-Anzeigenamen oder den Benutzerprinzipalnamen (UPN). Beispiel: ``DisplayName="John Smith"`` und ``DisplayName="johns@contoso.com"``. Für Azure AD-Gruppen wird nur der Azure AD-Anzeigename unterstützt.

> [!NOTE]
> Der Azure PowerShell-Befehl ```Set-AzSqlServerActiveDirectoryAdministrator``` hindert Sie nicht daran, nicht unterstützte Benutzer als Azure AD-Administratoren bereitzustellen. Ein nicht unterstützter Benutzer kann bereitgestellt werden, jedoch keine Verbindung mit einer Datenbank herstellen.

Im folgenden Beispiel wird der optionale Wert für **ObjectID**verwendet:

```powershell
Set-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23"
-ServerName "demo_server" -DisplayName "DBA_Group" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353f"
```

> [!NOTE]
> Ein Wert für **ObjectID** ist in Azure AD erforderlich, wenn **DisplayName**nicht eindeutig ist. Verwenden Sie den Active Directory-Abschnitt im klassischen Azure-Portal, um die Werte für **ObjectID** und **DisplayName** abzurufen um die Eigenschaften für einen Benutzer oder eine Gruppe anzuzeigen.

Das folgende Beispiel gibt Informationen zum aktuellen Azure AD-Administrator für die Azure SQL Server-Instanz zurück:

```powershell
Get-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" | Format-List
```

Im folgenden Beispiel wird ein Azure AD-Administrator entfernt:

```powershell
Remove-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server"
```

Sie können auch einen Azure Active Directory-Administrator mithilfe der REST-APIs bereitstellen. Weitere Informationen finden Sie unter [Referenz zur REST-API der Dienstverwaltung und Vorgänge für Azure SQL-Datenbank](https://docs.microsoft.com/rest/api/sql/).

### <a name="cli"></a>Befehlszeilenschnittstelle (CLI)  

Sie können auch einen Azure AD-Administrator bereitstellen, indem Sie die folgenden CLI-Befehle aufrufen:

| Get-Help | BESCHREIBUNG |
| --- | --- |
|[az sql server ad-admin create](https://docs.microsoft.com/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-create) |Stellt einen Azure Active Directory-Administrator für die Azure SQL Server-Instanz oder Azure SQL Data Warehouse bereit. (Muss aus dem aktuellen Abonnement stammen.) |
|[az sql server ad-admin delete](https://docs.microsoft.com/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-delete) |Entfernt einen Azure Active Directory-Administrator für die Azure SQL Server-Instanz oder Azure SQL Data Warehouse. |
|[az sql server ad-admin list](https://docs.microsoft.com/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-list) |Gibt Informationen zu dem Azure Active Directory-Administrator zurück, der aktuell für die Azure SQL Server-Instanz oder Azure SQL Data Warehouse konfiguriert ist. |
|[az sql server ad-admin update](https://docs.microsoft.com/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-update) |Aktualisiert den Active Directory-Administrator für einen Azure SQL-Server oder Azure SQL Data Warehouse. |

Weitere Informationen zu CLI-Befehlen finden Sie unter [SQL – az sql](https://docs.microsoft.com/cli/azure/sql/server).  

## <a name="configure-your-client-computers"></a>Konfigurieren der Clientcomputer

Auf allen Clientcomputern, über die Ihre Anwendungen oder Benutzer unter Verwendung von Azure AD-Identitäten eine Verbindung mit der Azure SQL-Datenbank oder mit Azure SQL Data Warehouse herstellen, muss die folgende Software installiert werden:

- .NET Framework 4.6 oder höher aus [https://msdn.microsoft.com/library/5a4x27ek.aspx](https://msdn.microsoft.com/library/5a4x27ek.aspx).
- Die Azure Active Directory-Authentifizierungsbibliothek für SQL Server (**ADALSQL.DLL**) steht in verschiedenen Sprachen (sowohl x86 als auch amd64) im Downloadcenter unter [Microsoft Active Directory-Authentifizierungsbibliothek für Microsoft SQL Server](https://www.microsoft.com/download/details.aspx?id=48742)bereit.

Sie können diese Anforderungen folgendermaßen erfüllen:

- Durch das Installieren von [SQL Server 2016 Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) oder [SQL Server Data Tools für Visual Studio 2015](https://msdn.microsoft.com/library/mt204009.aspx) wird die Anforderung erfüllt, dass .NET Framework 4.6 vorhanden sein muss.
- SSMS installiert die x86-Version von **ADALSQL.DLL**.
- SSDT installiert die amd64-Version von **ADALSQL.DLL**.
- Die neueste Visual Studio-Version unter [Visual Studio-Downloads](https://www.visualstudio.com/downloads/download-visual-studio-vs) erfüllt die .NET Framework 4.6-Anforderung, die erforderliche amd64-Version von **ADALSQL.DLL**wird jedoch nicht installiert.

## <a name="create-contained-database-users-in-your-database-mapped-to-azure-ad-identities"></a>Erstellen eigenständiger Datenbankbenutzer in der Datenbank, die Azure AD-Identitäten zugeordnet sind

>[!IMPORTANT]
>Verwaltete Instanzen unterstützen jetzt Azure AD-Serverprinzipale (Anmeldungen) (**öffentliche Vorschau**), wodurch Sie Anmeldungen für Azure AD-Benutzer, Gruppen oder Anwendungen erstellen können. Mit Azure AD-Serverprinzipalen (Anmeldungen) ist eine Authentifizierung bei der verwalteten Instanz möglich, ohne dass Datenbankbenutzer als eigenständige Datenbankbenutzer erstellt werden müssen. Weitere Informationen finden Sie in der [Übersicht über verwaltete Instanzen](sql-database-managed-instance.md#azure-active-directory-integration). Die Syntax zum Erstellen von Azure AD-Serverprinzipalen (Anmeldungen) finden Sie unter <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">CREATE LOGIN</a>.

Für die Azure Active Directory-Authentifizierung ist es erforderlich, dass Datenbankbenutzer als eigenständige Datenbankbenutzer erstellt werden. Ein eigenständiger Datenbankbenutzer basierend auf einer Azure AD-Identität ist ein Datenbankbenutzer, der über keine Anmeldung für die Masterdatenbank verfügt, und der einer Identität im Azure AD-Verzeichnis zugeordnet ist, das mit der Datenbank verknüpft ist. Bei der Azure AD-Identität kann es sich entweder um ein einzelnes Benutzerkonto oder um eine Gruppe handeln. Weitere Informationen zu eigenständigen Datenbankbenutzern finden Sie unter [Eigenständige Datenbankbenutzer – machen Sie Ihre Datenbank portabel](https://msdn.microsoft.com/library/ff929188.aspx).

> [!NOTE]
> Datenbankbenutzer (mit Ausnahme von Administratoren) können nicht im Azure-Portal erstellt werden. RBAC-Rollen werden nicht auf SQL Server, SQL-Datenbank oder SQL Data Warehouse übertragen. Azure RBAC-Rollen dienen zum Verwalten von Azure-Ressourcen und gelten nicht für Datenbankberechtigungen. Beispielsweise gewährt die Rolle **SQL Server-Mitwirkender** keinen Zugriff zum Herstellen einer Verbindung mit der SQL-Datenbank oder SQL Data Warehouse. Die Zugriffsberechtigung muss direkt in der Datenbank mithilfe von Transact-SQL-Anweisungen erteilt werden.
> [!WARNING]
> Sonderzeichen wie Doppelpunkt `:` oder das Und-Zeichen `&`, die als Benutzernamen in den Anweisungen „T-SQL CREATE LOGIN“ und „CREATE USER“ enthalten sind, werden nicht unterstützt.

Um einen Azure AD-basierten eigenständigen Datenbankbenutzer zu erstellen (bei dem es sich nicht um den Serveradministrator handelt, der Besitzer der Datenbank ist), stellen Sie mit einer Azure AD-Identität, die mindestens über die Berechtigung **BELIEBIGEN BENUTZER ÄNDERN** verfügt, eine Verbindung mit der Datenbank her. Verwenden Sie anschließend die folgende Transact-SQL-Syntax:

```sql
CREATE USER <Azure_AD_principal_name> FROM EXTERNAL PROVIDER;
```

*Azure_AD_principal_name* kann der Benutzerprinzipalname (UPN) eines Azure AD-Benutzers oder der Anzeigename einer Azure AD-Gruppe sein.

**Beispiele:** So erstellen Sie einen eigenständigen Datenbankbenutzer, der einen Benutzer der Azure AD-Verbunddomäne oder einen Benutzer der verwalteten Azure AD-Domäne repräsentiert:

```sql
CREATE USER [bob@contoso.com] FROM EXTERNAL PROVIDER;
CREATE USER [alice@fabrikam.onmicrosoft.com] FROM EXTERNAL PROVIDER;
```

Um einen eigenständigen Datenbankbenutzer zu erstellen, der eine Azure AD-Gruppe oder eine Gruppe der Verbunddomäne repräsentiert, geben Sie den Anzeigenamen einer Sicherheitsgruppe an:

```sql
CREATE USER [ICU Nurses] FROM EXTERNAL PROVIDER;
```

So erstellen Sie einen eigenständigen Datenbankbenutzer, der eine Anwendung darstellt, die eine Verbindung mit einem Azure AD-Token herstellt:

```sql
CREATE USER [appName] FROM EXTERNAL PROVIDER;
```

> [!TIP]
> Sie können einen Benutzer nur direkt aus der Azure Active Directory-Instanz erstellen, die Ihrem Azure-Abonnement zugeordnet ist. Mitglieder anderer Active Directory-Instanzen, die importierte Benutzer der zugeordneten Active Directory-Instanz sind (bekannt als externe Benutzer) können jedoch einer Active Directory-Gruppe des Active Directory-Mandanten hinzugefügt werden. Indem Sie einen eigenständigen Datenbankbenutzer für diese AD-Gruppe erstellen, können die Benutzer aus der externen Active Directory-Instanz auf die SQL-Datenbank zugreifen.

Weitere Informationen zum Erstellen eigenständiger Datenbankbenutzer basierend auf Azure Active Directory-Identitäten finden Sie unter [CREATE USER (Transact-SQL)](https://msdn.microsoft.com/library/ms173463.aspx).

> [!NOTE]
> Wenn der Azure Active Directory-Administrator für die Azure SQL Server-Instanz entfernt wird, können Azure AD-Authentifizierungsbenutzer keine Verbindung mehr mit dem Server herstellen. Nicht mehr verwendbare Azure AD-Benutzer können bei Bedarf manuell von einem SQL-Datenbankadministrator gelöscht werden.
> [!NOTE]
> Wenn Sie die Benachrichtigung **Das Verbindungstimeout ist abgelaufen.** erhalten, müssen Sie möglicherweise den Parameter `TransparentNetworkIPResolution` der Verbindungszeichenfolge auf FALSE festlegen. Weitere Informationen finden Sie unter [Connection timeout issue with .NET Framework 4.6.1 – TransparentNetworkIPResolution](https://blogs.msdn.microsoft.com/dataaccesstechnologies/20../../connection-timeout-issue-with-net-framework-4-6-1-transparentnetworkipresolution/) (Verbindungstimeout-Problem mit .NET Framework 4.6.1 – TransparentNetworkIPResolution).

Wenn Sie einen Datenbankbenutzer erstellen, erhält dieser Benutzer die Berechtigung **VERBINDEN** und kann sich als Mitglied der Rolle **ÖFFENTLICH** mit dieser Datenbank verbinden. Anfänglich stehen dem Benutzer nur die Berechtigungen zur Verfügung, die der Rolle **ÖFFENTLICH** gewährt wurden, sowie sämtliche Berechtigungen, die Azure AD-Gruppen erteilt wurden, denen der Benutzer angehört. Nachdem Sie einen Azure AD-basierten eigenständigen Datenbankbenutzer bereitgestellt haben, können Sie dem Benutzer zusätzliche Berechtigungen gewähren. Die Vorgehensweise ist hierbei mit der Berechtigungszuweisung für alle weiteren Benutzertypen identisch. Typischerweise gewähren Sie Datenbankrollen die gewünschten Berechtigungen, und weisen diesen Rollen anschließend Benutzer zu. Weitere Informationen finden Sie unter [Erste Schritte mit Berechtigungen für die Datenbank-Engine](https://social.technet.microsoft.com/wiki/contents/articles/4433.database-engine-permission-basics.aspx). Weitere Informationen zu besonderen SQL-Datenbankrollen finden Sie unter [Verwalten von Datenbanken und Anmeldungen in Azure SQL-Datenbank](sql-database-manage-logins.md).
Ein Verbunddomänen-Benutzerkonto, das als externer Benutzer in eine verwaltete Domäne importiert wird, muss die Identität der verwalteten Domäne verwenden.

> [!NOTE]
> Azure AD-Benutzer werden in den Datenbankmetadaten mit dem Typ E (EXTERNAL_USER) gekennzeichnet, Gruppen mit dem Typ X (EXTERNAL_GROUPS). Weitere Informationen finden Sie unter [sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx).
>

## <a name="connect-to-the-user-database-or-data-warehouse-by-using-ssms-or-ssdt"></a>Herstellen einer Verbindung mit der Benutzerdatenbank oder dem Data Warehouse mithilfe von SSMS oder SSDT  

Um zu bestätigen, dass der Azure AD-Administrator ordnungsgemäß eingerichtet ist, stellen Sie mit dem Azure AD-Administratorkonto eine Verbindung mit der Datenbank **master** her.
Um einen Azure AD-basierten eigenständigen Datenbankbenutzer bereitzustellen (bei dem es sich nicht um den Serveradministrator handelt, der Besitzer der Datenbank ist), stellen Sie mithilfe einer Azure AD-Identität, die Zugriff auf die Datenbank hat, eine Verbindung mit der Datenbank her.

> [!IMPORTANT]
> Unterstützung für die Azure Active Directory-Authentifizierung wird über [SQL Server 2016 Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) und [SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx) in Visual Studio 2015 bereitgestellt. Die SSMS-Version von August 2016 bietet auch Unterstützung für die universelle Active Directory-Authentifizierung, die es Administratoren ermöglicht, die Multi-Factor Authentication per Telefonanruf, SMS, Smartcards mit PIN oder Benachrichtigung in einer mobilen App anzufordern.

## <a name="using-an-azure-ad-identity-to-connect-using-ssms-or-ssdt"></a>Verwenden einer Azure AD-Identität zum Herstellen einer Verbindung mithilfe von SSMS oder SSDT

Nachfolgend wird das Herstellen einer Verbindung mit einer SQL-­Datenbank mithilfe einer Azure AD-Identität und unter Verwendung von SQL Server Management Studio oder SQL Server Data Tools erläutert.

### <a name="active-directory-integrated-authentication"></a>Integrierte Active Directory-Authentifizierung

Verwenden Sie diese Methode, wenn Sie mit Ihren Azure Active Directory-Anmeldeinformationen aus einer Verbunddomäne bei Windows angemeldet sind.

1. Starten Sie Management Studio oder Data Tools, und wählen Sie im Dialogfeld **Verbindung mit dem Server herstellen** (oder **Verbindung mit Datenbank-Engine herstellen**) im Feld **Authentifizierung** die Einstellung **Active Directory: integriert** aus. Es ist kein Kennwort erforderlich bzw. es kann kein Kennwort eingegeben werden, weil Ihre vorhandenen Anmeldeinformationen zur Verbindungsherstellung verwendet werden.

    ![Integrierte AD-Authentifizierung auswählen][11]
2. Wählen Sie die Schaltfläche **Optionen** aus, und geben Sie auf der Seite **Verbindungseigenschaften** im Feld **Mit Datenbank verbinden** den Namen der Benutzerdatenbank ein, mit der die Verbindung hergestellt werden soll. (Die Option **AD-Domänenname oder Mandanten-ID** wird nur für Optionen der Reihe **universell mit MFA-Unterstützung** unterstützt, andernfalls wird sie abgeblendet dargestellt.)  

    ![Datenbanknamen auswählen][13]

## <a name="active-directory-password-authentication"></a>Active Directory-Kennwortauthentifizierung

Verwenden Sie diese Methode, wenn Sie unter Verwendung der verwalteten Azure AD-Domäne und mit einem Azure AD-Prinzipalnamen eine Verbindung herstellen. Sie können diese Authentifizierungsmethode auch für ein Verbundkonto ohne Zugriff auf die Domäne verwenden, beispielsweise wenn Sie an einem Remotestandort arbeiten.

Verwenden Sie diese Methode, um sich bei SQL DB/DW mit Azure AD für native oder verbundene Azure AD-Benutzer zu authentifizieren. Ein nativer Benutzer ist ein explizit in Azure AD erstellter Benutzer, der mit Benutzername und Kennwort authentifiziert wird, während es sich bei einem Verbundbenutzer um einen Windows-Benutzer handelt, dessen Domäne mit Azure AD verbunden ist. Letztere Methode (mit Benutzer und Kennwort) kann verwendet werden, wenn ein Benutzer seine Windows-Anmeldeinformationen verwenden möchte, aber sein lokaler Computer nicht mit der Domäne verbunden ist (z.B. über einen Remotezugriff). In diesem Fall kann ein Windows-Benutzer sein Domänenkonto mit Kennwort angeben und sich bei SQL DB/DW mithilfe von Anmeldeinformationen als Verbundbenutzer authentifizieren.

1. Starten Sie Management Studio oder Data Tools, und wählen Sie im Dialogfeld **Verbindung mit dem Server herstellen** (oder **Verbindung mit Datenbank-Engine herstellen**) im Feld **Authentifizierung** die Einstellung **Active Directory: Kennwort** aus.
2. Geben Sie im Feld **Benutzername** Ihren Azure Active Directory-Benutzernamen im Format **Benutzername\@domain.com** ein. Bei Benutzernamen muss sich um ein Konto aus Azure Active Directory oder um ein Konto aus einer Domäne handeln, die im Verbund mit Azure Active Directory konfiguriert ist.
3. Geben Sie im Feld **Kennwort** Ihr Benutzerkennwort für das Azure Active Directory-Konto oder das Verbunddomänenkonto ein.

    ![AD-Kennwortauthentifizierung auswählen][12]
4. Wählen Sie die Schaltfläche **Optionen** aus, und geben Sie auf der Seite **Verbindungseigenschaften** im Feld **Mit Datenbank verbinden** den Namen der Benutzerdatenbank ein, mit der die Verbindung hergestellt werden soll. (Siehe Grafik in der vorherigen Option.)

## <a name="using-an-azure-ad-identity-to-connect-from-a-client-application"></a>Verwenden einer Azure AD-Identität, um von einer Clientanwendung aus eine Verbindung herzustellen

Die folgenden Verfahren zeigen Ihnen, wie Sie mit einer Azure AD-Identität von einer Clientanwendung aus eine Verbindung mit einer SQL-Datenbank herstellen.

### <a name="active-directory-integrated-authentication"></a>Integrierte Active Directory-Authentifizierung

Um die integrierte Windows-Authentifizierung zu verwenden, muss das Active Directory Ihrer Domäne mit Azure Active Directory verbunden sein. Ihre Client-Anwendung (oder ein Dienst), die eine Verbindung mit der Datenbank herstellt, muss unter den Domänenanmeldeinformationen eines Benutzers auf einem Computer ausgeführt werden, der der Domäne beigetreten ist.

Um mithilfe der integrierten Authentifizierung und einer Azure AD-Identität eine Verbindung mit einer Datenbank herzustellen, muss das Authentifizierungsschlüsselwort in der Verbindungszeichenfolge für die Datenbank auf "Active Directory Integrated" festgelegt sein. Im folgenden C#-Codebeispiel wird ADO .NET verwendet.

```C#
string ConnectionString =
@"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Integrated; Initial Catalog=testdb;";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

Das Schlüsselwort ``Integrated Security=True`` in der Verbindungszeichenfolge wird nicht für die Verbindungsherstellung mit Azure SQL-Datenbank unterstützt. Beim Herstellen einer ODBC-Verbindung müssen Sie Leerzeichen entfernen und Authentifizierung auf ActiveDirectoryIntegrated festlegen.

### <a name="active-directory-password-authentication"></a>Active Directory-Kennwortauthentifizierung

Um mithilfe der integrierten Authentifizierung und einer Azure AD-Identität eine Verbindung mit einer Datenbank herzustellen, muss als Authentifizierungsschlüsselwort das Kennwort für Active Directory festgelegt sein. Die Verbindungszeichenfolge muss Benutzer-ID/UID und Kennwort/PWD-Schlüsselwörter enthalten. Im folgenden C#-Codebeispiel wird ADO .NET verwendet.

```C#
string ConnectionString =
@"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Password; Initial Catalog=testdb;  UID=bob@contoso.onmicrosoft.com; PWD=MyPassWord!";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

Informieren Sie sich über Azure AD-Authentifizierungsmethoden, indem Sie die Demo-Codebeispiele unter [Azure AD Authentication GitHub Demo](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth)(GitHub-Demo zur Azure AD-Authentifizierung) verwenden.

## <a name="azure-ad-token"></a>Azure AD-Token

Diese Authentifizierungsmethode ermöglicht Diensten der mittleren Ebene, die Verbindung mit der Azure SQL-Datenbank oder Azure SQL Data Warehouse durch Abrufen eines Tokens von Azure Active Directory (AAD) herzustellen. So sind komplexe Szenarien einschließlich der zertifikatbasierten Authentifizierung möglich. Sie müssen vier grundlegende Schritte zum Verwenden der Azure AD-Token-Authentifizierung ausführen:

1. Registrieren Sie Ihre Anwendung in Azure Active Directory, und rufen Sie die Client-ID für den Code ab.
2. Erstellen Sie einen Datenbankbenutzer, der die Anwendung darstellt. (Bereits in Schritt 6 abgeschlossen.)
3. Erstellen Sie ein Zertifikat auf dem Clientcomputer, auf dem die Anwendung ausgeführt wird.
4. Fügen Sie das Zertifikat als Schlüssel für die Anwendung hinzu.

Beispiel-Verbindungszeichenfolge:

```c#
string ConnectionString =@"Data Source=n9lxnyuzhv.database.windows.net; Initial Catalog=testdb;"
SqlConnection conn = new SqlConnection(ConnectionString);
conn.AccessToken = "Your JWT token"
conn.Open();
```

Weitere Informationen finden Sie im [SQL Server Security Blog](https://blogs.msdn.microsoft.com/sqlsecurity/20../../token-based-authentication-support-for-azure-sql-db-using-azure-ad-auth/)(Blog zur Sicherheit von SQL Server). Informationen zum Hinzufügen eines Zertifikats finden Sie unter [Erste Schritte mit zertifikatbasierter Authentifizierung in Azure Active Directory](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md).

### <a name="sqlcmd"></a>sqlcmd

Die folgenden Anweisungen stellen eine Verbindung mithilfe von Version 13.1 von SQLCMD her, die im [Download Center](https://go.microsoft.com/fwlink/?LinkID=825643)verfügbar ist.

```cmd
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net  -G  
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net -U bob@contoso.com -P MyAADPassword -G -l 30
```

## <a name="next-steps"></a>Nächste Schritte

- Eine Übersicht über den Zugriff und die Steuerung in SQL-Datenbank finden Sie unter [Azure SQL Database access control](sql-database-control-access.md) (SQL-Datenbank – Zugriffssteuerung).
- Eine Übersicht über Anmeldungen, Benutzer und Datenbankrollen in SQL-Datenbank finden Sie unter [SQL-Datenbank-Authentifizierung und -Autorisierung: Gewähren von Zugriff](sql-database-manage-logins.md).
- Weitere Informationen zu Datenbankprinzipalen finden Sie unter [Prinzipale](https://msdn.microsoft.com/library/ms181127.aspx).
- Weitere Informationen zu Datenbankrollen finden Sie unter [Datenbankrollen](https://msdn.microsoft.com/library/ms189121.aspx).
- Weitere Informationen zu Firewallregeln in SQL-Datenbank finden Sie unter [Übersicht über Firewallregeln für Azure SQL-Datenbank](sql-database-firewall-configure.md).

<!--Image references-->

[1]: ./media/sql-database-aad-authentication/1aad-auth-diagram.png
[2]: ./media/sql-database-aad-authentication/2subscription-relationship.png
[3]: ./media/sql-database-aad-authentication/3admin-structure.png
[4]: ./media/sql-database-aad-authentication/4select-subscription.png
[5]: ./media/sql-database-aad-authentication/5ad-settings-portal.png
[6]: ./media/sql-database-aad-authentication/6edit-directory-select.png
[7]: ./media/sql-database-aad-authentication/7edit-directory-confirm.png
[8]: ./media/sql-database-aad-authentication/8choose-ad.png
[10]: ./media/sql-database-aad-authentication/10choose-admin.png
[11]: ./media/sql-database-aad-authentication/active-directory-integrated.png
[12]: ./media/sql-database-aad-authentication/12connect-using-pw-auth2.png
[13]: ./media/sql-database-aad-authentication/13connect-to-db2.png
