---
title: Konfigurieren der Azure Active Directory-Authentifizierung
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
description: Erfahren Sie, wie Sie unter Verwendung der Azure Active Directory-Authentifizierung nach der Konfiguration von Azure AD eine Verbindung mit SQL-Datenbank, SQL Managed Instance und Azure Synapse Analytics herstellen.
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: azure-synapse, has-adal-ref, sqldbrb=2, devx-track-azurecli
ms.devlang: ''
ms.topic: how-to
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto, sstein
ms.date: 08/17/2020
ms.openlocfilehash: 5558480f568e802637deb30c9f1b41c00826c9b5
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96454499"
---
# <a name="configure-and-manage-azure-ad-authentication-with-azure-sql"></a>Konfigurieren und Verwalten der Azure Active Directory-Authentifizierung mit Azure SQL

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

In diesem Artikel wird gezeigt, wie Sie eine Azure Active Directory-Instanz (Azure AD) erstellen und mit Daten auffüllen sowie Azure AD anschließend mit [Azure SQL-Datenbank](sql-database-paas-overview.md), [Azure SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md) und [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) verwenden. Eine Übersicht finden Sie im Artikel zur [Azure Active Directory-Authentifizierung](authentication-aad-overview.md).

## <a name="azure-ad-authentication-methods"></a>Azure AD-Authentifizierungsmethoden

Azure AD-Authentifizierung unterstützt die folgenden Authentifizierungsmethoden:

- Reine Azure AD-Cloudidentitäten
- Azure AD-Hybrididentitäten, die Folgendes unterstützen:
  - Cloudauthentifizierung mit zwei Optionen gekoppelt mit nahtlosem SSO (einmaliges Anmelden)
    - Azure AD-Kennworthashauthentifizierung
    - Azure AD-Passthrough-Authentifizierung
  - Verbundauthentifizierung

Ausführliche Informationen zu den Azure AD-Authentifizierungsmethoden und zur Auswahl der geeigneten Methode finden Sie unter [Wählen der richtigen Authentifizierungsmethode für Ihre Azure Active Directory-Hybrididentitätslösung](../../active-directory/hybrid/choose-ad-authn.md).

Weitere Informationen zu Azure AD-Hybrididentitäten, zur Einrichtung und Synchronisierung finden Sie hier:

- Kennworthashauthentifizierung: [Implementieren von Kennworthashsynchronisierung mit Azure AD Connect-Synchronisierung](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md)
- Passthrough-Authentifizierung: [Azure Active Directory-Passthrough-Authentifizierung](../../active-directory/hybrid/how-to-connect-pta-quick-start.md)
- Verbundauthentifizierung: [Bereitstellung von Active Directory-Verbunddiensten in Azure](/windows-server/identity/ad-fs/deployment/how-to-connect-fed-azure-adfs) und [Azure AD Connect und Verbund](../../active-directory/hybrid/how-to-connect-fed-whatis.md)

## <a name="create-and-populate-an-azure-ad-instance"></a>Erstellen und Auffüllen einer Azure AD-Instanz

Erstellen Sie eine Azure AD-Instanz, und füllen Sie sie mit Benutzern und Gruppen. Azure AD kann die verwaltete Azure AD-Anfangsdomäne sein. Azure AD kann auch ein lokaler Active Directory Domain Services im Verbund mit Azure AD sein.

Weitere Informationen finden Sie unter [Integrieren Ihrer lokalen Identitäten in Azure Active Directory](../../active-directory/hybrid/whatis-hybrid-identity.md), [Hinzufügen eines benutzerdefinierten Domänennamens zu Azure AD](../../active-directory/fundamentals/add-custom-domain.md), [Microsoft Azure unterstützt jetzt den Verbund mit Windows Server Active Directory](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/), [Verwalten Ihres Azure AD-Verzeichnisses](../../active-directory/fundamentals/active-directory-whatis.md)[ sowie unter Verwalten von Azure AD mit Windows PowerShell](/powershell/azure/) und [erforderliche Ports und Protokolle für die Hybrid-Identität](../../active-directory/hybrid/reference-connect-ports.md).

## <a name="associate-or-add-an-azure-subscription-to-azure-active-directory"></a>Zuweisen oder Hinzufügen eines Azure-Abonnements zu Azure Active Directory

1. Ordnen Sie Ihr Azure-Abonnement Azure Active Directory zu, indem Sie das Verzeichnis für das Azure-Abonnement, das die Datenbank hostet, als vertrauenswürdiges Verzeichnis kennzeichnen. Ausführliche Informationen finden Sie unter [Zuordnen oder Hinzufügen eines Azure-Abonnements zu Ihrem Azure Active Directory-Mandanten](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

2. Verwenden Sie den Verzeichnisumschalter im Azure-Portal, um zu dem der Domäne zugeordneten Abonnement zu wechseln.

   > [!IMPORTANT]
   > Jedes Azure-Abonnement weist eine Vertrauensstellung mit einer Azure AD-Instanz auf. Dies bedeutet, dass es diesem Verzeichnis bei der Authentifizierung von Benutzern, Diensten und Geräten vertraut. Mehrere Abonnements können dem gleichen Verzeichnis vertrauen, ein Abonnement vertraut jedoch nur einem Verzeichnis. Diese Vertrauensstellung, die ein Abonnement mit einem Verzeichnis aufweist, unterscheidet sich von der Beziehung, die ein Abonnement mit allen anderen Ressourcen in Azure (Websites, Datenbanken usw.) hat. Diese ähneln eher den untergeordneten Ressourcen eines Abonnements. Wenn ein Abonnement abläuft, wird der Zugriff auf die anderen Ressourcen, die dem Abonnement zugeordnet sind, ebenfalls beendet. Das Verzeichnis verbleibt jedoch in Azure, und Sie können ihm ein anderes Abonnement zuordnen und weiterhin die Benutzer des Verzeichnisses verwalten. Weitere Informationen zu Ressourcen finden Sie unter [Grundlegendes zur Zugriffssteuerung in Azure sowie zur Integration in Azure Active Directory](../../active-directory/external-identities/add-users-administrator.md). Weitere Informationen zu dieser Vertrauensstellung finden Sie unter [Zuweisen oder Hinzufügen eines Azure-Abonnements zu Azure Active Directory](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="azure-ad-admin-with-a-server-in-sql-database"></a>Azure AD-Administrator mit einem Server in SQL-Datenbank

Jeder [Server](logical-servers.md) in Azure (der SQL-Datenbank oder Azure Synapse hostet) wird zunächst mit einem einzelnen Serveradministratorkonto konfiguriert, das als Administrator für den gesamten Server fungiert. Erstellen Sie ein zweites Administratorkonto als Azure AD-Konto. Dieser Prinzipal wird als eigenständiger Datenbankbenutzer in der Masterdatenbank des Servers erstellt. Administratorkonten sind Mitglieder der Rolle **db_owner** in jeder Benutzerdatenbank, und der Zugriff auf alle Benutzerdatenbanken erfolgt als Benutzer **dbo**. Weitere Informationen zu Administratorkonten finden Sie unter [Verwalten von Datenbanken und Anmeldungen](logins-create-manage.md).

Bei Verwendung von Azure Active Directory mit Georeplikation muss der Azure Active Directory-Administrator sowohl für den primären als auch für die sekundären Server konfiguriert werden. Wenn ein Server über keinen Azure Active Directory-Administrator verfügt, erhalten Benutzer bei der Azure Active Directory-Anmeldung die Fehlermeldung `Cannot connect`.

> [!NOTE]
> Benutzer, die nicht auf einem Azure AD-Konto basieren (hierzu gehört auch das Serveradministratorkonto) können keine Azure AD-basierten Benutzer erstellen, da sie keine Berechtigung zum Überprüfen der vorgeschlagenen Datenbankbenutzer mit Azure AD besitzen.

## <a name="provision-azure-ad-admin-sql-managed-instance"></a>Bereitstellen eines Azure AD-Administrators (SQL Managed Instance)

> [!IMPORTANT]
> Führen Sie diese Schritte nur aus, wenn Sie SQL Managed Instance bereitstellen. Dieser Vorgang kann nur von einem globalen Administrator/Unternehmensadministrator oder von einem Administrator für privilegierte Rollen in Azure AD durchgeführt werden.
>
> In der **öffentlichen Vorschau** können Sie die Rolle **Verzeichnisleseberechtigte** einer Gruppe in Azure AD zuweisen. Die Gruppenbesitzer können dann die Identität der verwalteten Instanz als Mitglied dieser Gruppe hinzufügen. Dadurch können Sie einen Azure AD-Administrator für die verwaltete SQL-Instanz bereitstellen. Weitere Informationen zu diesem Feature finden Sie unter [Rolle „Verzeichnisleseberechtigte“ in Azure Active Directory für Azure SQL](authentication-aad-directory-readers-role.md).

Für SQL Managed Instance werden Leseberechtigungen für Azure AD benötigt, damit Aufgaben wie die Authentifizierung von Benutzern über die Mitgliedschaft in Sicherheitsgruppen oder die Erstellung neuer Benutzer erfolgreich durchgeführt werden können. Damit dies funktioniert, müssen Sie SQL Managed Instance Leseberechtigungen für Azure AD gewähren. Dies ist mithilfe des Azure-Portals oder mit PowerShell möglich.

### <a name="azure-portal"></a>Azure-Portal

Um Ihrer Instanz von SQL Managed Instance über das Azure-Portal Leseberechtigungen für Azure AD zu erteilen, melden Sie sich als globaler Administrator/Unternehmensadministrator in Azure AD an und führen die folgenden Schritte aus:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com) in der oberen rechten Ecke Ihre Verbindung über die Dropdownliste der möglichen Active Directory-Verzeichnissen aus.

2. Wählen Sie das richtige Active Directory-Verzeichnis als Standardeinstellung für Azure AD aus.

   In diesem Schritt wird das Active Directory zugeordnete Abonnement mit SQL Managed Instance verknüpft. So wird sichergestellt, dass für die Azure AD-Instanz und SQL Managed Instance dasselbe Abonnement verwendet wird.

3. Navigieren Sie zu der Instanz von SQL Managed Instance, die Sie für die Azure AD-Integration verwenden möchten.

   ![Screenshot: Azure-Portal mit der geöffneten Active Directory-Administratorseite für die ausgewählte verwaltete SQL-Instanz](./media/authentication-aad-configure/aad.png)

4. Wählen Sie oben auf der Seite „Active Directory-Administrator“ das Banner aus, und erteilen Sie dem aktuellen Benutzer die Berechtigung.

    ![Screenshot: Dialogfeld zum Erteilen von Berechtigungen für eine verwaltete SQL-Instanz zum Zugreifen auf Active Directory. Die Schaltfläche „Berechtigungen erteilen“ ist ausgewählt.](./media/authentication-aad-configure/grant-permissions.png)

5. Nachdem der Vorgang erfolgreich abgeschlossen wurde, wird in der oberen rechten Ecke die folgende Benachrichtigung angezeigt:

    ![Screenshot: Benachrichtigung, die bestätigt, dass Active Directory-Leseberechtigungen für die verwaltete Instanz erfolgreich aktualisiert wurden](./media/authentication-aad-configure/success.png)

6. Nun können Sie Ihren Azure AD-Administrator für Ihre Instanz von SQL Managed Instance auswählen. Wählen Sie hierzu auf der Seite „Active Directory-Administrator“ **Administrator festlegen** aus.

    ![Screenshot: Hervorgehobene Option „Administrator festlegen“ auf der Active Directory-Administratorseite für die ausgewählte verwaltete SQL-Instanz](./media/authentication-aad-configure/set-admin.png)

7. Suchen Sie auf der Azure AD-Administratorseite nach einem Benutzer, wählen Sie den Benutzer oder die Gruppe aus, den bzw. die Sie als Administrator festlegen möchten, und klicken Sie dann auf **Auswählen**.

   Auf der Seite „Active Directory-Administrator“ werden alle Mitglieder und Gruppen in Ihrem Active Directory-Verzeichnis angezeigt. Abgeblendete Benutzer oder Gruppen können nicht ausgewählt werden, da sie nicht als Azure AD-Administratoren unterstützt werden. Eine Liste mit den unterstützten Administratoren finden Sie unter [Features und Einschränkungen von Azure AD](authentication-aad-overview.md#azure-ad-features-and-limitations). Die rollenbasierte Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC) gilt nur für das Azure-Portal und wird nicht an SQL-Datenbank, SQL Managed Instance oder Azure Synapse weitergegeben.

    ![Hinzufügen eines Azure Active Directory-Administrators](./media/authentication-aad-configure/add-azure-active-directory-admin.png)

8. Wählen Sie oben auf der Seite „Active Directory-Administrator“ **Speichern** aus.

    ![Screenshot: Active Directory-Administratorseite mit der Schaltfläche „Speichern“ in der oberen Zeile neben den Schaltflächen „Administrator festlegen“ und „Administrator entfernen“](./media/authentication-aad-configure/save.png)

    Der Vorgang zum Ändern des Administrators kann einige Minuten in Anspruch nehmen. Anschließend wird der neue Administrator im Feld „Active Directory-Administrator“ angezeigt.

Nachdem Sie einen Azure AD-Administrator für Ihre Instanz von SQL Managed Instance bereitgestellt haben, können Sie unter Verwendung der Syntax <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">CREATE LOGIN</a> mit der Erstellung von Azure AD-Serverprinzipalen (Anmeldungen) beginnen. Weitere Informationen finden Sie in der [Übersicht über SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md#azure-active-directory-integration).

> [!TIP]
> Wenn Sie später einen Administrator entfernen möchten, wählen Sie oben auf der Seite „Active Directory-Administrator“ **Administrator entfernen** und anschließend **Speichern** aus.

### <a name="powershell"></a>PowerShell

Um Ihrer Instanz von SQL Managed Instance über PowerShell Azure AD-Leseberechtigungen zu gewähren, führen Sie dieses Skript aus:

```powershell
# Gives Azure Active Directory read permission to a Service Principal representing the SQL Managed Instance.
# Can be executed only by a "Company Administrator", "Global Administrator", or "Privileged Role Administrator" type of user.

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

# Get service principal for your SQL Managed Instance
$roleMember = Get-AzureADServicePrincipal -SearchString $managedInstanceName
$roleMember.Count
if ($roleMember -eq $null) {
    Write-Output "Error: No Service Principals with name '$    ($managedInstanceName)', make sure that managedInstanceName parameter was     entered correctly."
    exit
}
if (-not ($roleMember.Count -eq 1)) {
    Write-Output "Error: More than one service principal with name pattern '$    ($managedInstanceName)'"
    Write-Output "Dumping selected service principals...."
    $roleMember
    exit
}

# Check if service principal is already member of readers role
$allDirReaders = Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
$selDirReader = $allDirReaders | where{$_.ObjectId -match     $roleMember.ObjectId}

if ($selDirReader -eq $null) {
    # Add principal to readers role
    Write-Output "Adding service principal '$($managedInstanceName)' to     'Directory Readers' role'..."
    Add-AzureADDirectoryRoleMember -ObjectId $role.ObjectId -RefObjectId     $roleMember.ObjectId
    Write-Output "'$($managedInstanceName)' service principal added to     'Directory Readers' role'..."

    #Write-Output "Dumping service principal '$($managedInstanceName)':"
    #$allDirReaders = Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
    #$allDirReaders | where{$_.ObjectId -match $roleMember.ObjectId}
}
else {
    Write-Output "Service principal '$($managedInstanceName)' is already     member of 'Directory Readers' role'."
}
```

### <a name="powershell-for-sql-managed-instance"></a>PowerShell für SQL Managed Instance

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Zum Ausführen von PowerShell-Cmdlets muss Azure PowerShell installiert sein und ausgeführt werden. Weitere Informationen finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](/powershell/azure/).

> [!IMPORTANT]
> Das Azure Resource Manager-Modul von PowerShell wird von Azure SQL Managed Instance weiterhin unterstützt, aber alle zukünftigen Entwicklungen erfolgen für das Az.Sql-Modul. Das AzureRM-Modul erhält mindestens bis Dezember 2020 weiterhin Fehlerbehebungen.  Die Argumente für die Befehle im Az-Modul und den AzureRm-Modulen sind im Wesentlichen identisch. Weitere Informationen zur Kompatibilität finden Sie in der [Einführung in das neue Azure PowerShell Az-Modul](/powershell/azure/new-azureps-module-az).

Um einen Azure AD-Administrator bereitzustellen, führen Sie die folgenden Azure PowerShell-Befehle aus:

- Connect-AzAccount
- Select-AzSubscription

Zum Bereitstellen und Verwalten des Azure AD-Administrators für Ihre Instanz von SQL Managed Instance stehen folgende Cmdlets zur Verfügung:

| Cmdlet-Name | Beschreibung |
| --- | --- |
| [Set-AzSqlInstanceActiveDirectoryAdministrator](/powershell/module/az.sql/set-azsqlinstanceactivedirectoryadministrator) |Stellt einen Azure AD-Administrator für SQL Managed Instance im aktuellen Abonnement bereit. (Muss aus dem aktuellen Abonnement stammen.)|
| [Remove-AzSqlInstanceActiveDirectoryAdministrator](/powershell/module/az.sql/remove-azsqlinstanceactivedirectoryadministrator) |Entfernt einen Azure AD-Administrator für SQL Managed Instance aus dem aktuellen Abonnement. |
| [Get-AzSqlInstanceActiveDirectoryAdministrator](/powershell/module/az.sql/get-azsqlinstanceactivedirectoryadministrator) |Gibt Informationen zu einem Azure AD-Administrator für SQL Managed Instance im aktuellen Abonnement zurück.|

Mit dem folgenden Befehl werden Informationen zu einem Azure AD-Administrator für eine SQL Managed Instance-Instanz namens „ManagedInstance01“ abgerufen, die einer Ressourcengruppe mit dem Namen „ResourceGroup01“ zugeordnet ist.

```powershell
Get-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstance01"
```

Über den folgenden Befehl wird eine Azure AD-Administratorgruppe mit dem Namen „DBAs“ für die SQL Managed Instance-Instanz „ManagedInstance01“ bereitgestellt. Dieser Server ist der Ressourcengruppe „ResourceGroup01“ zugeordnet:

```powershell
Set-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstance01" -DisplayName "DBAs" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353b"
```

Mit dem folgenden Befehl wird der Azure AD-Administrator für die SQL Managed Instance-Instanz „ManagedInstanceName01“ entfernt, die der Ressourcengruppe „ResourceGroup01“ zugeordnet ist.

```powershell
Remove-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstanceName01" -Confirm -PassThru
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Sie können einen Azure AD-Administrator für SQL Managed Instance auch mithilfe der folgenden CLI-Befehle bereitstellen:

| Befehl | Beschreibung |
| --- | --- |
|[az sql mi ad-admin create](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-create) | Stellt einen Azure AD-Administrator für SQL Managed Instance bereit (muss aus dem aktuellen Abonnement stammen). |
|[az sql mi ad-admin delete](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-delete) | Entfernt einen Azure Active Directory-Administrator für SQL Managed Instance. |
|[az sql mi ad-admin list](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-list) | Gibt Informationen zu einem Azure Active Directory-Administrator zurück, der aktuell für SQL Managed Instance konfiguriert ist. |
|[az sql mi ad-admin update](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-update) | Aktualisiert den Azure Active Directory-Administrator für SQL Managed Instance. |

Weitere Informationen zu CLI-Befehlen finden Sie unter [az sql mi](/cli/azure/sql/mi).

* * *

## <a name="provision-azure-ad-admin-sql-database"></a>Bereitstellen eines Azure AD-Administrators (SQL-Datenbank)

> [!IMPORTANT]
> Führen Sie die folgenden Schritte nur aus, wenn Sie einen [Server](logical-servers.md) für Azure SQL-Datenbank oder Azure Synapse Analytics bereitstellen.

Die beiden folgenden Verfahren zeigen, wie Sie einen Azure Active Directory-Administrator für Ihren Server im Azure-Portal und mithilfe von PowerShell bereitstellen.

### <a name="azure-portal"></a>Azure-Portal

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) in der oberen rechten Ecke Ihre Verbindung aus, um eine Dropdownliste mit möglichen Active Directory-Verzeichnissen zu öffnen. Wählen Sie das richtige Active Directory-Verzeichnis als Standardeinstellung für Azure AD aus. Durch diesen Schritt wird die mit dem Abonnement verbundene Active Directory-Instanz mit dem Server verknüpft. So wird sichergestellt, dass für Azure AD und den Server dasselbe Abonnement verwendet wird.

2. Suchen Sie nach **SQL Server**, und wählen Sie diese Option aus.

    ![Suchen nach und Auswählen von „SQL Server“](./media/authentication-aad-configure/search-for-and-select-sql-servers.png)

    >[!NOTE]
    > Auf dieser Seite können Sie vor der Auswahl von **SQL Server** den **Stern** neben dem Namen auswählen, um die Kategorie den *Favoriten* hinzuzufügen, und **SQL Server** der linken Navigationsleiste hinzufügen.

3. Wählen Sie auf der Seite **SQL Server** die Option **Active Directory-Administrator** aus.

4. Wählen Sie auf der Seite **Active Directory-Administrator** die Option **Administrator festlegen** aus.

    ![SQL Server: Festlegen des Active Directory-Administrators](./media/authentication-aad-configure/sql-servers-set-active-directory-admin.png)  

5. Suchen Sie auf der Seite **Administrator hinzufügen** nach einem Benutzer, wählen Sie den Benutzer oder die Gruppe aus, den bzw. die Sie als Administrator festlegen möchten, und wählen Sie dann **Auswählen** aus. Auf der Seite „Active Directory-Administrator“ werden alle Mitglieder und Gruppen in Ihrem Active Directory-Verzeichnis angezeigt. (Auf dem Blatt „Active Directory-Administrator“ werden alle Mitglieder und Gruppen in Ihrem Active Directory-Verzeichnis angezeigt. (Siehe die Liste von unterstützten Administratoren im Abschnitt **Funktionen und Einschränkungen von Azure AD** von [Verwenden der Azure Active Directory-Authentifizierung für die Authentifizierung mit SQL-Datenbank oder Azure Synapse](authentication-aad-overview.md).) Die rollenbasierte Zugriffssteuerung in Azure (Role-based Access Control, RBAC) gilt nur für das Portal und wird nicht an SQL Server weitergegeben.

    ![Auswählen des Azure Active Directory-Administrators](./media/authentication-aad-configure/select-azure-active-directory-admin.png)  

6. Wählen Sie oben auf der Seite **Active Directory-Administrator** **SPEICHERN** aus.

    ![Administrator speichern](./media/authentication-aad-configure/save-admin.png)

Der Vorgang zum Ändern des Administrators kann einige Minuten in Anspruch nehmen. Anschließend wird der neue Administrator im Feld **Active Directory-Administrator** angezeigt.

   > [!NOTE]
   > Beim Einrichten des Azure AD-Administrators darf der Name des neuen Administrators (Benutzer oder Gruppe) in der virtuellen Masterdatenbank noch nicht als Benutzer für die Serverauthentifizierung vorhanden sein. Wenn er vorhanden ist, schlägt die Einrichtung des Azure AD-Administrators fehl, wobei die Erstellung rückgängig gemacht und angegeben wird, dass ein solcher Administrator (Name) bereits vorhanden ist. Da dieser Benutzer für die Serverauthentifizierung nicht in Azure AD enthalten ist, kann über die Azure AD-Authentifizierung keine Verbindung mit dem Server hergestellt werden.

Wenn Sie später einen Administrator entfernen möchten, wählen Sie oben auf der Seite **Active Directory-Administrator** **Administrator entfernen** und anschließend **Speichern** aus.

### <a name="powershell-for-sql-database-and-azure-synapse"></a>PowerShell für SQL-Datenbank und Azure Synapse

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Zum Ausführen von PowerShell-Cmdlets muss Azure PowerShell installiert sein und ausgeführt werden. Weitere Informationen finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](/powershell/azure/). Um einen Azure AD-Administrator bereitzustellen, führen Sie die folgenden Azure PowerShell-Befehle aus:

- Connect-AzAccount
- Select-AzSubscription

Zum Bereitstellen und Verwalten des Azure AD-Administrators für Azure SQL-Datenbank und Azure Synapse stehen folgende Cmdlets zur Verfügung:

| Cmdlet-Name | Beschreibung |
| --- | --- |
| [Set-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/set-azsqlserveractivedirectoryadministrator) |Stellt einen Azure Active Directory-Administrator für den Server bereit, der SQL-Datenbank oder Azure Synapse hostet. (Muss aus dem aktuellen Abonnement stammen.) |
| [Remove-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/remove-azsqlserveractivedirectoryadministrator) |Entfernt einen Azure Active Directory-Administrator für den Server, der SQL-Datenbank oder Azure Synapse hostet.|
| [Get-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/get-azsqlserveractivedirectoryadministrator) |Gibt Informationen zu dem Azure Active Directory-Administrator zurück, der aktuell für den Server zum Hosten von SQL-Datenbank oder Azure Synapse konfiguriert ist. |

Verwenden Sie den PowerShell-Befehl „get-help“, um weitere Informationen zu diesen Befehlen zu erhalten. Beispiel: `get-help Set-AzSqlServerActiveDirectoryAdministrator`.

Das folgende Skript stellt eine Azure AD-Administratorengruppe namens **DBA_Group** (Objekt-ID `40b79501-b343-44ed-9ce7-da4c8cc7353f`) für den Server **demo_server** in einer Ressourcengruppe mit dem Namen **Group-23** bereit:

```powershell
Set-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" -DisplayName "DBA_Group"
```

Der Eingabeparameter **DisplayName** akzeptiert entweder den Azure AD-Anzeigenamen oder den Benutzerprinzipalnamen (UPN). Beispiel: ``DisplayName="John Smith"`` und ``DisplayName="johns@contoso.com"``. Für Azure AD-Gruppen wird nur der Azure AD-Anzeigename unterstützt.

> [!NOTE]
> Der Azure PowerShell-Befehl ```Set-AzSqlServerActiveDirectoryAdministrator``` hindert Sie nicht daran, nicht unterstützte Benutzer als Azure AD-Administratoren bereitzustellen. Ein nicht unterstützter Benutzer kann bereitgestellt werden, jedoch keine Verbindung mit einer Datenbank herstellen.

Im folgenden Beispiel wird der optionale Wert für **ObjectID** verwendet:

```powershell
Set-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" `
    -DisplayName "DBA_Group" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353f"
```

> [!NOTE]
> Ein Wert für **ObjectID** ist in Azure AD erforderlich, wenn **DisplayName** nicht eindeutig ist. Verwenden Sie den Active Directory-Abschnitt im klassischen Azure-Portal, um die Werte für **ObjectID** und **DisplayName** abzurufen um die Eigenschaften für einen Benutzer oder eine Gruppe anzuzeigen.

Das folgende Beispiel gibt Informationen zum aktuellen Azure AD-Administrator für den Server zurück:

```powershell
Get-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" | Format-List
```

Im folgenden Beispiel wird ein Azure AD-Administrator entfernt:

```powershell
Remove-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server"
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Sie können einen Azure AD-Administrator bereitstellen, indem Sie die folgenden CLI-Befehle aufrufen:

| Get-Help | Beschreibung |
| --- | --- |
|[az sql server ad-admin create](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-create) | Stellt einen Azure Active Directory-Administrator für den Server bereit, der SQL-Datenbank oder Azure Synapse hostet. (Muss aus dem aktuellen Abonnement stammen.) |
|[az sql server ad-admin delete](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-delete) | Entfernt einen Azure Active Directory-Administrator für den Server, der SQL-Datenbank oder Azure Synapse hostet. |
|[az sql server ad-admin list](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-list) | Gibt Informationen zu dem Azure Active Directory-Administrator zurück, der aktuell für den Server zum Hosten von SQL-Datenbank oder Azure Synapse konfiguriert ist. |
|[az sql server ad-admin update](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-update) | Aktualisiert den Azure Active Directory-Administrator für den Server, der SQL-Datenbank oder Azure Synapse hostet. |

Weitere Informationen zu CLI-Befehlen finden Sie unter [az sql server](/cli/azure/sql/server).

* * *

> [!NOTE]
> Sie können auch einen Azure Active Directory-Administrator mithilfe der REST-APIs bereitstellen. Weitere Informationen finden Sie unter [Referenz zur REST-API der Dienstverwaltung und Vorgänge für Azure SQL-Datenbank](/rest/api/sql/).

## <a name="configure-your-client-computers"></a>Konfigurieren der Clientcomputer

Auf allen Clientcomputern, über die Ihre Anwendungen oder Benutzer unter Verwendung von Azure AD-Identitäten eine Verbindung mit SQL-Datenbank oder Azure Synapse herstellen, muss die folgende Software installiert werden:

- .NET Framework 4.6 oder höher aus [https://msdn.microsoft.com/library/5a4x27ek.aspx](/dotnet/framework/install/guide-for-developers).
- Azure Active Directory-Authentifizierungsbibliothek für SQL Server (*ADAL.DLL*). Nachstehend finden Sie die Downloadlinks zum Installieren der neuesten SSMS-, ODBC-und OLE DB-Treiber, die die Bibliothek *ADAL.DLL* enthalten.
  - [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)
  - [ODBC Driver 17 for SQL Server](https://www.microsoft.com/download/details.aspx?id=56567)
  - [OLE DB Driver 18 for SQL Server](https://www.microsoft.com/download/details.aspx?id=56730)

Sie können diese Anforderungen folgendermaßen erfüllen:

- Durch das Installieren der aktuellen Version von [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) oder [SQL Server Data Tools](/sql/ssdt/download-sql-server-data-tools-ssdt) wird die Anforderung erfüllt, dass .NET Framework 4.6 vorhanden sein muss.
  - SSMS installiert die x86-Version von *ADAL.DLL*.
  - SSDT installiert die amd64-Version von *ADAL.DLL*.
  - Die neueste Visual Studio-Version unter [Visual Studio-Downloads](https://www.visualstudio.com/downloads/download-visual-studio-vs) erfüllt die .NET Framework 4.6-Anforderung, die erforderliche amd64-Version von *ADAL.DLL* wird jedoch nicht installiert.

## <a name="create-contained-users-mapped-to-azure-ad-identities"></a>Erstellen eigenständiger Benutzer mit Zuordnung zu Azure AD-Identitäten

SQL Managed Instance unterstützt Azure AD-Serverprinzipale (Anmeldungen), deshalb ist die Verwendung eigenständiger Datenbankbenutzer nicht erforderlich. Azure AD-Serverprinzipale (Anmeldungen) ermöglichen es Ihnen, Anmeldungen für Azure AD-Benutzer, -Gruppen oder -Anwendungen zu erstellen. Dies bedeutet, dass Sie sich über die Azure AD-Serveranmeldung bei Ihrer Instanz von SQL Managed Instance authentifizieren können, statt sich über einen eigenständigen Datenbankbenutzer zu authentifizieren. Weitere Informationen finden Sie in der [Übersicht über SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md#azure-active-directory-integration). Die Syntax zum Erstellen von Azure AD-Serverprinzipalen (Anmeldungen) finden Sie unter <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">CREATE LOGIN</a>.

Die Verwendung der Azure Active Directory-Authentifizierung bei SQL-Datenbank und Azure Synapse erfordert jedoch die Verwendung von eigenständigen Datenbankbenutzern auf der Grundlage einer Azure AD-Identität. Ein eigenständiger Datenbankbenutzer umfasst keine Anmeldung in der Masterdatenbank und ist einer Identität in Azure AD zugeordnet, die mit der Datenbank verknüpft ist. Bei der Azure AD-Identität kann es sich entweder um ein einzelnes Benutzerkonto oder um eine Gruppe handeln. Weitere Informationen zu eigenständigen Datenbankbenutzern finden Sie unter [Eigenständige Datenbankbenutzer – machen Sie Ihre Datenbank portabel](/sql/relational-databases/security/contained-database-users-making-your-database-portable).

> [!NOTE]
> Datenbankbenutzer (mit Ausnahme von Administratoren) können nicht im Azure-Portal erstellt werden. Azure-Rollen werden nicht an die Datenbank in SQL-Datenbank, SQL Managed Instance oder Azure Synapse weitergegeben. Azure-Rollen dienen zum Verwalten von Azure-Ressourcen und gelten nicht für Datenbankberechtigungen. Beispielsweise gewährt die Rolle **SQL Server-Mitwirkender** keinen Zugriff zur Verbindungsherstellung mit der Datenbank in SQL-Datenbank, SQL Managed Instance oder Azure Synapse. Die Zugriffsberechtigung muss direkt in der Datenbank mithilfe von Transact-SQL-Anweisungen erteilt werden.

> [!WARNING]
> In den T-SQL-Anweisungen `CREATE LOGIN` und `CREATE USER` werden Sonderzeichen wie Doppelpunkt (`:`) oder kaufmännisches Und-Zeichen (`&`) in Benutzernamen nicht unterstützt.

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

> [!NOTE]
> Dieser Befehl erfordert, dass SQL auf Azure AD (den „externen Anbieter“) im Namen des angemeldeten Benutzers zugreift. Manchmal treten Umstände auf, die bewirken, dass Azure AD eine Ausnahme an SQL zurückgibt. In diesen Fällen wird dem Benutzer der SQL-Fehler 33134 angezeigt, der die Azure AD-spezifische Fehlermeldung enthalten sollte. In den meisten Fällen besagt der Fehler, dass der Zugriff verweigert wird oder dass der Benutzer sich für MFA registrieren muss, um auf die Ressource zuzugreifen, oder dass der Zugriff zwischen Erstanbieteranwendungen über Vorautorisierung verarbeitet werden muss. In den ersten beiden Fällen wird das Problem normalerweise durch Richtlinien für bedingten Zugriff verursacht, die im Azure AD-Mandanten des Benutzers festgelegt werden. Diese verhindern, dass der Benutzer auf den externen Anbieter zugreifen kann. Aktualisieren Sie die Zertifizierungsstellenrichtlinien, um den Zugriff auf die Anwendung „00000002-0000-0000-c000-000000000000“ zu ermöglichen (die Anwendungs-ID der Azure AD-Graph-API). Dadurch sollte das Problem behoben werden. Wenn der Fehler besagt, dass der Zugriff zwischen Erstanbieteranwendungen über Vorautorisierung verarbeitet werden muss, liegt das Problem daran, dass der Benutzer als Dienstprinzipal angemeldet ist. Der Befehl sollte erfolgreich sein, wenn er stattdessen von einem Benutzer ausgeführt wird.

> [!TIP]
> Sie können einen Benutzer nur direkt aus der Azure Active Directory-Instanz erstellen, die Ihrem Azure-Abonnement zugeordnet ist. Mitglieder anderer Active Directory-Instanzen, die importierte Benutzer der zugeordneten Active Directory-Instanz sind (bekannt als externe Benutzer) können jedoch einer Active Directory-Gruppe des Active Directory-Mandanten hinzugefügt werden. Indem Sie einen eigenständigen Datenbankbenutzer für diese AD-Gruppe erstellen, können die Benutzer aus der externen Active Directory-Instanz auf die SQL-Datenbank zugreifen.

Weitere Informationen zum Erstellen eigenständiger Datenbankbenutzer basierend auf Azure Active Directory-Identitäten finden Sie unter [CREATE USER (Transact-SQL)](/sql/t-sql/statements/create-user-transact-sql).

> [!NOTE]
> Wenn der Azure Active Directory-Administrator für den Server entfernt wird, können Azure AD-Authentifizierungsbenutzer nicht länger eine Verbindung mit dem Server herstellen. Nicht mehr verwendbare Azure AD-Benutzer können bei Bedarf manuell von einem SQL-Datenbankadministrator gelöscht werden.

> [!NOTE]
> Wenn Sie die Benachrichtigung **Das Verbindungstimeout ist abgelaufen.** erhalten, müssen Sie möglicherweise den Parameter `TransparentNetworkIPResolution` der Verbindungszeichenfolge auf FALSE festlegen. Weitere Informationen finden Sie unter [Connection timeout issue with .NET Framework 4.6.1 – TransparentNetworkIPResolution](/archive/blogs/dataaccesstechnologies/connection-timeout-issue-with-net-framework-4-6-1-transparentnetworkipresolution) (Verbindungstimeout-Problem mit .NET Framework 4.6.1 – TransparentNetworkIPResolution).

Wenn Sie einen Datenbankbenutzer erstellen, erhält dieser Benutzer die Berechtigung **VERBINDEN** und kann sich als Mitglied der Rolle **ÖFFENTLICH** mit dieser Datenbank verbinden. Anfänglich stehen dem Benutzer nur die Berechtigungen zur Verfügung, die der Rolle **ÖFFENTLICH** gewährt wurden, sowie sämtliche Berechtigungen, die Azure AD-Gruppen erteilt wurden, denen der Benutzer angehört. Nachdem Sie einen Azure AD-basierten eigenständigen Datenbankbenutzer bereitgestellt haben, können Sie dem Benutzer zusätzliche Berechtigungen gewähren. Die Vorgehensweise ist hierbei mit der Berechtigungszuweisung für alle weiteren Benutzertypen identisch. Typischerweise gewähren Sie Datenbankrollen die gewünschten Berechtigungen, und weisen diesen Rollen anschließend Benutzer zu. Weitere Informationen finden Sie unter [Erste Schritte mit Berechtigungen für die Datenbank-Engine](https://social.technet.microsoft.com/wiki/contents/articles/4433.database-engine-permission-basics.aspx). Weitere Informationen zu besonderen SQL-Datenbankrollen finden Sie unter [Verwalten von Datenbanken und Anmeldungen in Azure SQL-Datenbank](logins-create-manage.md).
Ein Verbunddomänen-Benutzerkonto, das als externer Benutzer in eine verwaltete Domäne importiert wird, muss die Identität der verwalteten Domäne verwenden.

> [!NOTE]
> Azure AD-Benutzer werden in den Datenbankmetadaten mit dem Typ E (EXTERNAL_USER) gekennzeichnet, Gruppen mit dem Typ X (EXTERNAL_GROUPS). Weitere Informationen finden Sie unter [sys.database_principals](/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql).

## <a name="connect-to-the-database-using-ssms-or-ssdt"></a>Herstellen einer Datenbankverbindung SSMS oder SSDT  

Um zu bestätigen, dass der Azure AD-Administrator ordnungsgemäß eingerichtet ist, stellen Sie mit dem Azure AD-Administratorkonto eine Verbindung mit der Datenbank **master** her.
Um einen Azure AD-basierten eigenständigen Datenbankbenutzer bereitzustellen (bei dem es sich nicht um den Serveradministrator handelt, der Besitzer der Datenbank ist), stellen Sie mithilfe einer Azure AD-Identität, die Zugriff auf die Datenbank hat, eine Verbindung mit der Datenbank her.

> [!IMPORTANT]
> Unterstützung für die Azure Active Directory-Authentifizierung wird über [SQL Server 2016 Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) und [SQL Server Data Tools](/sql/ssdt/download-sql-server-data-tools-ssdt) in Visual Studio 2015 bereitgestellt. Die SSMS-Version von August 2016 bietet auch Unterstützung für die universelle Active Directory-Authentifizierung, die es Administratoren ermöglicht, die Multi-Factor Authentication per Telefonanruf, SMS, Smartcards mit PIN oder Benachrichtigung in einer mobilen App anzufordern.

## <a name="using-an-azure-ad-identity-to-connect-using-ssms-or-ssdt"></a>Verwenden einer Azure AD-Identität zum Herstellen einer Verbindung mithilfe von SSMS oder SSDT

Das nachstehende Verfahren zeigt, wie Sie mithilfe einer Azure AD-Identität und unter Verwendung von SQL Server Management Studio oder SQL Server Data Tools eine Verbindung mit SQL-­Datenbank herstellen.

### <a name="active-directory-integrated-authentication"></a>Integrierte Active Directory-Authentifizierung

Verwenden Sie diese Methode, wenn Sie bei Windows angemeldet sind. Verwenden Sie dazu Ihre Azure Active Directory-Anmeldeinformationen aus einer Verbunddomäne oder einer verwalteten Domäne, die für nahtloses einmaliges Anmelden für eine Passthrough- und Kennworthashauthentifizierung konfiguriert ist. Weitere Informationen finden Sie unter [Nahtlose einmalige Anmeldung mit Azure Active Directory](../../active-directory/hybrid/how-to-connect-sso.md).

1. Starten Sie Management Studio oder Data Tools, und wählen Sie im Dialogfeld **Verbindung mit dem Server herstellen** (oder **Verbindung mit Datenbank-Engine herstellen**) im Feld **Authentifizierung** die Einstellung **Azure Active Directory: integriert** aus. Es ist kein Kennwort erforderlich bzw. es kann kein Kennwort eingegeben werden, weil Ihre vorhandenen Anmeldeinformationen zur Verbindungsherstellung verwendet werden.

   ![Integrierte AD-Authentifizierung auswählen][11]

2. Wählen Sie die Schaltfläche **Optionen** aus, und geben Sie auf der Seite **Verbindungseigenschaften** im Feld **Mit Datenbank verbinden** den Namen der Benutzerdatenbank ein, mit der die Verbindung hergestellt werden soll. Weitere Informationen finden Sie im Artikel [Mehrstufige AAD-Authentifizierung](authentication-mfa-ssms-overview.md#azure-ad-domain-name-or-tenant-id-parameter), in dem die Unterschiede zwischen den Verbindungseigenschaften für SSMS 17.x und 18.x behandelt werden.

   ![Datenbanknamen auswählen][13]

### <a name="active-directory-password-authentication"></a>Active Directory-Kennwortauthentifizierung

Verwenden Sie diese Methode, wenn Sie unter Verwendung der verwalteten Azure AD-Domäne und mit einem Azure AD-Prinzipalnamen eine Verbindung herstellen. Sie können diese Authentifizierungsmethode auch für ein Verbundkonto ohne Zugriff auf die Domäne verwenden, beispielsweise wenn Sie an einem Remotestandort arbeiten.

Verwenden Sie diese Methode, um sich in SQL-Datenbank oder SQL Managed Instance mit reinen Azure AD-Cloudidentitätsbenutzern oder Benutzern bei der Datenbank zu authentifizieren, die Azure AD-Hybrididentitäten verwenden. Diese Methode unterstützt Benutzer, die ihre Windows-Anmeldeinformationen verwenden möchten, deren lokaler Computer aber nicht mit der Domäne verbunden ist (z. B. über Remotezugriff). In diesem Fall kann ein Windows-Benutzer sein Domänenkonto mit dem zugehörigen Kennwort verwenden, um sich in SQL-Datenbank, SQL Managed Instance oder Azure Synapse bei der Datenbank zu authentifizieren.

1. Starten Sie Management Studio oder Data Tools, und wählen Sie im Dialogfeld **Verbindung mit dem Server herstellen** (oder **Verbindung mit Datenbank-Engine herstellen**) im Feld **Authentifizierung** die Einstellung **Azure Active Directory: Kennwort** aus.

2. Geben Sie im Feld **Benutzername** Ihren Azure Active Directory-Benutzernamen im Format **Benutzername\@domain.com** ein. Bei Benutzernamen muss es sich um ein Konto aus Azure Active Directory oder um ein Konto aus einer verwalteten Domäne oder einer Domäne handeln, die im Verbund mit Azure Active Directory konfiguriert ist.

3. Geben Sie im Feld **Kennwort** Ihr Benutzerkennwort für das Azure Active Directory-Konto oder das Konto der verwalteten Domäne oder Verbunddomäne ein.

    ![AD-Kennwortauthentifizierung auswählen][12]

4. Wählen Sie die Schaltfläche **Optionen** aus, und geben Sie auf der Seite **Verbindungseigenschaften** im Feld **Mit Datenbank verbinden** den Namen der Benutzerdatenbank ein, mit der die Verbindung hergestellt werden soll. (Siehe Grafik in der vorherigen Option.)

### <a name="active-directory-interactive-authentication"></a>Interaktive Active Directory-Authentifizierung

Verwenden Sie diese Methode für die interaktive Authentifizierung mit oder ohne mehrstufige Authentifizierung (Multi-Factor Authentication, MFA), bei der das Kennwort interaktiv angefordert wird. Diese Methode kann verwendet werden, um sich in SQL-Datenbank oder SQL Managed Instance mit reinen Azure AD-Cloudidentitätsbenutzern oder Benutzern bei der Datenbank zu authentifizieren, die Azure AD-Hybrididentitäten verwenden.

Weitere Informationen finden Sie unter [Verwenden der mehrstufiger Azure AD-Authentifizierung mit SQL-Datenbank und Azure Synapse Analytics (SSMS-Unterstützung für MFA)](authentication-mfa-ssms-overview.md).

## <a name="using-an-azure-ad-identity-to-connect-from-a-client-application"></a>Verwenden einer Azure AD-Identität, um von einer Clientanwendung aus eine Verbindung herzustellen

Die folgenden Verfahren zeigen, wie Sie mit einer Azure AD-Identität von einer Clientanwendung aus eine Verbindung mit SQL-Datenbank herstellen.

### <a name="active-directory-integrated-authentication"></a>Integrierte Active Directory-Authentifizierung

Um integrierte Windows-Authentifizierung verwenden zu können, muss das Active Directory der Domäne einen Verbund mit Azure Active Directory bilden oder sollte eine verwaltete Domäne sein, die für nahtloses einmaliges Anmelden (SSO) für Passthrough- oder Kennworthashauthentifizierung konfiguriert ist. Weitere Informationen finden Sie unter [Nahtlose einmalige Anmeldung mit Azure Active Directory](../../active-directory/hybrid/how-to-connect-sso.md).

> [!NOTE]
> [MSAL.NET (Microsoft.Identity.Client)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki#roadmap) für die integrierte Windows-Authentifizierung wird nicht für nahtloses einmaliges Anmelden für die Pass-Through- und Kennworthashauthentifizierung unterstützt.

Ihre Client-Anwendung (oder ein Dienst), die eine Verbindung mit der Datenbank herstellt, muss unter den Domänenanmeldeinformationen eines Benutzers auf einem Computer ausgeführt werden, der der Domäne beigetreten ist.

Um mithilfe der integrierten Authentifizierung und einer Azure AD-Identität eine Verbindung mit einer Datenbank herzustellen, muss das Authentifizierungsschlüsselwort in der Verbindungszeichenfolge für die Datenbank auf `Active Directory Integrated` festgelegt sein. Im folgenden C#-Codebeispiel wird ADO .NET verwendet.

```csharp
string ConnectionString = @"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Integrated; Initial Catalog=testdb;";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

Das Schlüsselwort `Integrated Security=True` in der Verbindungszeichenfolge wird nicht für die Verbindungsherstellung mit Azure SQL-Datenbank unterstützt. Beim Herstellen einer ODBC-Verbindung müssen Sie Leerzeichen entfernen und Authentifizierung auf ActiveDirectoryIntegrated festlegen.

### <a name="active-directory-password-authentication"></a>Active Directory-Kennwortauthentifizierung

Zum Herstellen einer Verbindung mit einer Datenbank mithilfe reiner Azure AD-Cloudidentitäts-Benutzerkonten oder Konten, die Azure AD-Hybrididentitäten verwenden, muss das Authentifizierungsschlüsselwort auf `Active Directory Password`festgelegt werden. Die Verbindungszeichenfolge muss Benutzer-ID/UID und Kennwort/PWD-Schlüsselwörter enthalten. Im folgenden C#-Codebeispiel wird ADO .NET verwendet.

```csharp
string ConnectionString =
@"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Password; Initial Catalog=testdb;  UID=bob@contoso.onmicrosoft.com; PWD=MyPassWord!";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

Informieren Sie sich über Azure AD-Authentifizierungsmethoden, indem Sie die Demo-Codebeispiele unter [Azure AD Authentication GitHub Demo](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth)(GitHub-Demo zur Azure AD-Authentifizierung) verwenden.

## <a name="azure-ad-token"></a>Azure AD-Token

Dank dieser Authentifizierungsmethode können Dienste der mittleren Ebene [JSON Webtoken (JWT)](../../active-directory/develop/id-tokens.md) abrufen, um in SQL-Datenbank, SQL Managed Instance oder Azure Synapse eine Verbindung mit der Datenbank herzustellen, indem sie ein Token aus Azure AD abrufen. Diese Methode ermöglicht verschiedene Anwendungsszenarien einschließlich Dienstidentitäten, Dienstprinzipalen und Anwendungen, die zertifikatbasierte Authentifizierung verwenden. Sie müssen vier grundlegende Schritte zum Verwenden der Azure AD-Token-Authentifizierung ausführen:

1. Registrieren Sie Ihre Anwendung in Azure Active Directory, und rufen Sie die Client-ID für Ihren Code ab.
2. Erstellen Sie einen Datenbankbenutzer, der die Anwendung darstellt. (Bereits in Schritt 6 abgeschlossen.)
3. Erstellen Sie ein Zertifikat auf dem Clientcomputer, auf dem die Anwendung ausgeführt wird.
4. Fügen Sie das Zertifikat als Schlüssel für die Anwendung hinzu.

Beispiel-Verbindungszeichenfolge:

```csharp
string ConnectionString =@"Data Source=n9lxnyuzhv.database.windows.net; Initial Catalog=testdb;"
SqlConnection conn = new SqlConnection(ConnectionString);
conn.AccessToken = "Your JWT token"
conn.Open();
```

Weitere Informationen finden Sie im [SQL Server Security Blog](/archive/blogs/sqlsecurity/token-based-authentication-support-for-azure-sql-db-using-azure-ad-auth)(Blog zur Sicherheit von SQL Server). Informationen zum Hinzufügen eines Zertifikats finden Sie unter [Erste Schritte mit zertifikatbasierter Authentifizierung in Azure Active Directory](../../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md).

### <a name="sqlcmd"></a>sqlcmd

Die folgenden Anweisungen stellen eine Verbindung mithilfe von Version 13.1 von SQLCMD her, die im [Download Center](https://www.microsoft.com/download/details.aspx?id=53591)verfügbar ist.

> [!NOTE]
> `sqlcmd` mit dem `-G`-Befehl funktioniert nicht mit Systemidentitäten und erfordert eine Benutzerprinzipalanmeldung.

```cmd
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net -G  
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net -U bob@contoso.com -P MyAADPassword -G -l 30
```

## <a name="troubleshoot-azure-ad-authentication"></a>Behandeln von Problemen mit der Azure AD-Authentifizierung

Eine Anleitung zur Behandlung von Problemen bei der Azure AD-Authentifizierung finden Sie im folgenden Blog: <https://techcommunity.microsoft.com/t5/azure-sql-database/troubleshooting-problems-related-to-azure-ad-authentication-with/ba-p/1062991>

## <a name="next-steps"></a>Nächste Schritte

- Eine Übersicht über Anmeldungen, Benutzer, Datenbankrollen und Berechtigungen in SQL-Datenbank finden Sie unter [Anmeldungen, Benutzer, Datenbankrollen und Benutzerkonten](logins-create-manage.md).
- Weitere Informationen zu Datenbankprinzipalen finden Sie unter [Prinzipale](/sql/relational-databases/security/authentication-access/principals-database-engine).
- Weitere Informationen zu Datenbankrollen finden Sie unter [Datenbankrollen](/sql/relational-databases/security/authentication-access/database-level-roles).
- Weitere Informationen zu Firewallregeln in SQL-Datenbank finden Sie unter [Übersicht über Firewallregeln für Azure SQL-Datenbank](firewall-configure.md).
- Informationen dazu, wie Sie einen Azure AD-Gastbenutzer als Azure AD-Administrator festlegen, finden Sie unter [Erstellen von Azure AD-Gastbenutzern und Festlegen als Azure AD-Administrator](authentication-aad-guest-users.md).
- Informationen über Dienstprinzipale mit Azure SQL finden Sie unter [Erstellen von Azure AD-Benutzern mithilfe von Azure AD-Anwendungen](authentication-aad-service-principal-tutorial.md).

<!--Image references-->

[11]: ./media/authentication-aad-configure/active-directory-integrated.png
[12]: ./media/authentication-aad-configure/12connect-using-pw-auth2.png
[13]: ./media/authentication-aad-configure/13connect-to-db2.png
