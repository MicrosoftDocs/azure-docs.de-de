---
title: Erstellen von Azure AD-Benutzern mithilfe von Dienstprinzipalen
description: Dieses Tutorial führt Sie durch das Erstellen eines Azure AD-Benutzers mit einer Azure AD-Anwendung (Dienstprinzipale) in Azure SQL-Datenbank und Azure Synapse Analytics.
ms.service: sql-database
ms.subservice: security
ms.custom: azure-synapse
ms.topic: tutorial
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 02/11/2021
ms.openlocfilehash: 13e049d3e7e0c87bd0a214a92491e10d652a3619
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100380609"
---
# <a name="tutorial-create-azure-ad-users-using-azure-ad-applications"></a>Tutorial: Erstellen von Azure AD-Benutzern mithilfe von Azure AD-Anwendungen

[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

> [!NOTE]
> Dieser Artikel ist eine **öffentliche Vorschauversion**. Weitere Informationen finden Sie unter [Azure Active Directory-Dienstprinzipal mit Azure SQL](authentication-aad-service-principal.md). In diesem Artikel wird Azure SQL-Datenbank verwendet, um die erforderlichen Tutorialschritte zu veranschaulichen. Sie können jedoch auch auf [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) angewendet werden.

Dieser Artikel führt Sie durch den Vorgang des Erstellens von Azure AD-Benutzern in Azure SQL-Datenbank unter Verwendung von Azure-Dienstprinzipalen (Azure AD-Anwendungen). Diese Funktionalität ist bereits in Azure SQL Managed Instance vorhanden, sie wird jetzt aber auch in Azure SQL-Datenbank und Azure Synapse Analytics eingeführt. Um dieses Szenario zu unterstützen, muss eine Azure AD-Identität generiert und dem logischen Azure SQL-Server zugewiesen werden.

Weitere Informationen zur Azure AD-Authentifizierung für Azure SQL finden Sie unter [Verwenden von Azure Active Directory-Authentifizierung](authentication-aad-overview.md).

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> - Zuweisen einer Identität zum logischen Azure SQL-Server
> - Zuweisen der Berechtigung „Verzeichnisleser“ zur Identität des logischen SQL-Servers
> - Erstellen eines Dienstprinzipals (einer Azure AD-Anwendung) in Azure AD
> - Erstellen eines Dienstprinzipalbenutzers in Azure SQL-Datenbank
> - Erstellen eines anderen Azure AD-Benutzers in SQL-Datenbank mithilfe eines Azure AD-Dienstprinzipalbenutzers

## <a name="prerequisites"></a>Voraussetzungen

- Eine vorhandene Bereitstellung von [Azure SQL-Datenbank](single-database-create-quickstart.md) oder [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md). Wir gehen davon aus, dass Sie für dieses Tutorial über eine funktionierende SQL-Datenbank verfügen.
- Zugriff auf ein bereits vorhandenes Azure Active Directory.
- Das [Az.Sql 2.9.0](https://www.powershellgallery.com/packages/Az.Sql/2.9.0)-Modul oder höher ist erforderlich, wenn Sie PowerShell verwenden, um eine einzelne Azure AD Anwendung als Azure AD-Administrator für Azure SQL einzurichten. Stellen Sie sicher, dass Sie ein Upgrade auf das neueste Modul ausgeführt haben.

## <a name="assign-an-identity-to-the-azure-sql-logical-server"></a>Zuweisen einer Identität zum logischen Azure SQL-Server

1. Stellen Sie eine Verbindung mit Azure Active Directory her. Sie müssen Ihre Mandanten-ID ermitteln. Sie finden diese, indem Sie zum [Azure-Portal](https://portal.azure.com) navigieren und dann zu Ihrer **Azure Active Directory**-Ressource navigieren. Im Bereich **Übersicht** sollte die **Mandanten-ID** angezeigt werden. Führen Sie den folgenden PowerShell-Befehl aus:

    - Ersetzen Sie `<TenantId>` durch Ihre **Mandanten-ID**.

    ```powershell
    Connect-AzAccount -Tenant <TenantId>
    ```

    Notieren Sie sich den `TenantId`-Wert für die spätere Verwendung in diesem Tutorial.

1. Generieren Sie eine Azure AD-Identität, und weisen Sie sie dem logischen Azure SQL-Server zu. Führen Sie den folgenden PowerShell-Befehl aus:

    - Ersetzen Sie `<resource group>` und `<server name>` durch Ihre Ressourcen. Wenn der Servername `myserver.database.windows.net` ist, ersetzen Sie `<server name>` durch `myserver`.

    ```powershell
    Set-AzSqlServer -ResourceGroupName <resource group> -ServerName <server name> -AssignIdentity
    ```

    Weitere Informationen finden Sie unter dem [AzSqlServer](/powershell/module/az.sql/set-azsqlserver)-Befehl.

    > [!IMPORTANT]
    > Wenn eine Azure AD-Identität für den logischen Azure SQL-Server eingerichtet wird, muss der Identität die Berechtigung [**Verzeichnisleser**](../../active-directory/roles/permissions-reference.md#directory-readers) erteilt werden. Dieser Schritt wird im folgenden Abschnitt ausführlich erläutert. **Überspringen Sie diesen Schritt nicht**, weil die Azure AD-Authentifizierung nicht mehr funktioniert.

    - Wenn Sie in der Vergangenheit den Befehl [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) mit dem Parameter `AssignIdentity` für das Erstellen eines neuen SQL-Servers verwendet haben, müssen Sie anschließend den Befehl [Set-AzSqlServer](/powershell/module/az.sql/set-azsqlserver) als separaten Befehl ausführen, um diese Eigenschaft im Azure-Fabric zu aktivieren.

1. Überprüfen Sie, ob die Serveridentität erfolgreich zugewiesen wurde. Führen Sie den folgenden PowerShell-Befehl aus:

    - Ersetzen Sie `<resource group>` und `<server name>` durch Ihre Ressourcen. Wenn der Servername `myserver.database.windows.net` ist, ersetzen Sie `<server name>` durch `myserver`.
    
    ```powershell
    $xyz = Get-AzSqlServer  -ResourceGroupName <resource group> -ServerName <server name>
    $xyz.identity
    ```

    Ihre Ausgabe sollte Ihnen `PrincipalId`, `Type`und `TenantId`zeigen. Die zugewiesene Identität ist die `PrincipalId`.

1. Sie können die Identität auch überprüfen, indem Sie zum [Azure-Portal](https://portal.azure.com) navigieren.

    - Navigieren Sie unter der **Azure Active Directory**-Ressource zu **Unternehmensanwendungen**. Geben Sie den Namen Ihres logischen SQL-Servers ein. Sie sehen, dass eine **Objekt-ID** an die Ressource angefügt wurde.
    
    :::image type="content" source="media/authentication-aad-service-principals-tutorial/enterprise-applications-object-id.png" alt-text="object-id":::

## <a name="assign-directory-readers-permission-to-the-sql-logical-server-identity"></a>Zuweisen der Berechtigung „Verzeichnisleser“ zur Identität des logischen SQL-Servers

Damit die Azure AD zugewiesene Identität für Azure SQL ordnungsgemäß funktioniert, muss der Serveridentität die Azure AD-Berechtigung `Directory Readers` erteilt werden.

Führen Sie das folgende Skript aus, um diese erforderliche Berechtigung zu erteilen.

> [!NOTE] 
> Dieses Skript muss von einem Azure AD-`Global Administrator` oder einem -`Privileged Roles Administrator` ausgeführt werden.
>
> In der **öffentlichen Vorschau** können Sie die Rolle `Directory Readers` einer Gruppe in Azure AD zuweisen. Die Gruppenbesitzer können dann die verwaltete Identität als Mitglied dieser Gruppe hinzufügen. Hierdurch wird die Notwendigkeit umgangen, dass von `Global Administrator` oder `Privileged Roles Administrator` die Rolle `Directory Readers` gewährt werden muss. Weitere Informationen zu diesem Feature finden Sie unter [Rolle „Verzeichnisleseberechtigte“ in Azure Active Directory für Azure SQL](authentication-aad-directory-readers-role.md).

- Ersetzen Sie `<TenantId>` durch Ihre zuvor ermittelte `TenantId`.
- Ersetzen Sie `<server name>` durch den Namen Ihres logischen SQL-Servers. Wenn der Servername `myserver.database.windows.net` ist, ersetzen Sie `<server name>` durch `myserver`.

```powershell
# This script grants Azure "Directory Readers" permission to a Service Principal representing the Azure SQL logical server
# It can be executed only by a "Global Administrator" or "Privileged Roles Administrator" type of user.
# To check if the "Directory Readers" permission was granted, execute this script again

Import-Module AzureAD
Connect-AzureAD -TenantId "<TenantId>"    #Enter your actual TenantId
$AssignIdentityName = "<server name>"     #Enter Azure SQL logical server name
 
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
$roleMember = Get-AzureADServicePrincipal -SearchString $AssignIdentityName
$roleMember.Count
if ($roleMember -eq $null) {
    Write-Output "Error: No Service Principals with name '$($AssignIdentityName)', make sure that AssignIdentityName parameter was entered correctly."
    exit
}

if (-not ($roleMember.Count -eq 1)) {
    Write-Output "Error: More than one service principal with name pattern '$($AssignIdentityName)'"
    Write-Output "Dumping selected service principals...."
    $roleMember
    exit
}
 
# Check if service principal is already member of readers role
$allDirReaders = Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
$selDirReader = $allDirReaders | where{$_.ObjectId -match $roleMember.ObjectId}
 
if ($selDirReader -eq $null) {
    # Add principal to readers role
    Write-Output "Adding service principal '$($AssignIdentityName)' to 'Directory Readers' role'..."
    Add-AzureADDirectoryRoleMember -ObjectId $role.ObjectId -RefObjectId $roleMember.ObjectId
    Write-Output "'$($AssignIdentityName)' service principal added to 'Directory Readers' role'..."
 
    #Write-Output "Dumping service principal '$($AssignIdentityName)':"
    #$allDirReaders = Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
    #$allDirReaders | where{$_.ObjectId -match $roleMember.ObjectId}
} else {
    Write-Output "Service principal '$($AssignIdentityName)' is already member of 'Directory Readers' role'."
}
```

> [!NOTE]
> Die Ausgabe des Skripts oben gibt an, ob der Identität die Berechtigung „Verzeichnisleser“ erteilt wurde. Wenn Sie sich nicht sicher sind, ob die Berechtigung erteilt wurde, können Sie das Skript erneut ausführen.

Eine ähnliche Vorgehensweise zum Festlegen der Berechtigung **Verzeichnisleser** für SQL Managed Instance finden Sie unter [Bereitstellen des Azure AD-Administrators (SQL Managed Instance)](authentication-aad-configure.md#powershell).

## <a name="create-a-service-principal-an-azure-ad-application-in-azure-ad"></a>Erstellen eines Dienstprinzipals (einer Azure AD-Anwendung) in Azure AD

1. Befolgen Sie hier die Anweisungen, um [Ihre App zu registrieren und Berechtigungen festzulegen](active-directory-interactive-connect-azure-sql-db.md#register-your-app-and-set-permissions).

    Stellen Sie sicher, dass Sie die **Anwendungsberechtigungen** und die **delegierten Berechtigungen** hinzufügen.

    :::image type="content" source="media/authentication-aad-service-principals-tutorial/aad-apps.png" alt-text="Screenshot mit der „App-Registrierungen“-Seite für Azure Active Directory. Eine App mit dem Anzeigenamen „AppSP“ ist hervorgehoben.":::

    :::image type="content" source="media/authentication-aad-service-principals-tutorial/aad-app-registration-api-permissions.png" alt-text="api-permissions":::

2. Außerdem müssen Sie einen geheimen Clientschlüssel für die Anmeldung erstellen. Befolgen Sie hier die Anweisungen, um ein [Zertifikat hochzuladen oder ein Geheimnis für die Anmeldung zu erstellen](../../active-directory/develop/howto-create-service-principal-portal.md#authentication-two-options).

3. Notieren Sie sich die folgenden Informationen aus ihrer Anwendungsregistrierung. Sie sollten im Bereich **Übersicht** verfügbar sein:
    - **Anwendungs-ID**
    - **Mandanten-ID**: Dieser Wert sollte identisch mit dem vorherigen Wert sein.

In diesem Tutorial verwenden wir *AppSP* als Hauptdienstprinzipal und *myapp* als zweiten Dienstprinzipalbenutzer, der in Azure SQL durch *AppSP* erstellt wird. Sie müssen zwei Anwendungen erstellen: *AppSP* und *myapp*.

Weitere Informationen zum Erstellen einer Azure AD-Anwendung finden Sie unter [Vorgehensweise: Erstellen einer Azure AD-Anwendung und eines Dienstprinzipals mit Ressourcenzugriff über das Portal](../../active-directory/develop/howto-create-service-principal-portal.md).

### <a name="permissions-required-to-set-or-unset-the-azure-ad-admin"></a>Erforderliche Berechtigungen zum Festlegen oder zum Aufheben der Festlegung des Azure AD-Administrators

Damit der Dienstprinzipal einen Azure AD-Administrator für Azure SQL festlegen oder dessen Festlegung aufheben kann, ist eine zusätzliche API-Berechtigung erforderlich. Die [Directory.Read.All](/graph/permissions-reference#application-permissions-18)-Berechtigung der Anwendungs-API muss zu Ihrer Anwendung in Azure AD hinzugefügt werden.

:::image type="content" source="media/authentication-aad-service-principals-tutorial/aad-directory-reader-all-permissions.png" alt-text="Directory.Reader.All-Berechtigungen in Azure AD":::

Der Dienstprinzipal benötigt auch die Rolle [**Mitwirkender von SQL Server**](../../role-based-access-control/built-in-roles.md#sql-server-contributor) für SQL-Datenbank oder die Rolle [**Mitwirkender für verwaltete SQL-Instanzen**](../../role-based-access-control/built-in-roles.md#sql-managed-instance-contributor) für SQL Managed Instance.

> [!NOTE]
> Obwohl die Azure AD Graph-API veraltet ist, gilt für dieses Tutorial immer noch die Berechtigung **Directory.Reader.All**. Die Microsoft Graph-API gilt nicht für dieses Tutorial.

## <a name="create-the-service-principal-user-in-azure-sql-database"></a>Erstellen des Dienstprinzipalbenutzers in Azure SQL-Datenbank

Nachdem ein Dienstprinzipal in Azure AD erstellt wurde, erstellen Sie den Benutzer in SQL-Datenbank. Sie müssen eine Verbindung mit Ihrer SQL-Datenbank mit einer gültigen Anmeldung mit Berechtigungen zum Erstellen von Benutzern in der Datenbank herstellen.

1. Erstellen Sie den Benutzer *AppSP* in der SQL-Datenbank, indem Sie den folgenden T-SQL-Befehl verwenden:

    ```sql
    CREATE USER [AppSP] FROM EXTERNAL PROVIDER
    GO
    ```

2. Erteilen Sie die `db_owner`-Berechtigung für *AppSP*, wodurch der Benutzer andere Azure AD-Benutzer in der Datenbank erstellen kann.

    ```sql
    EXEC sp_addrolemember 'db_owner', [AppSP]
    GO
    ```

    Weitere Informationen finden Sie unter [sp_addrolemember](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql).

    Alternativ kann die `ALTER ANY USER`-Berechtigung erteilt werden, anstatt die `db_owner`-Rolle zuzuweisen. Dadurch kann der Dienstprinzipal weitere Azure AD-Benutzer hinzufügen.

    ```sql
    GRANT ALTER ANY USER TO [AppSp]
    GO
    ```

    > [!NOTE]
    > Die oben erwähnte Einstellung ist nicht erforderlich, wenn *AppSP* als Azure AD-Administrator für den Server festgelegt ist. Wenn Sie den Dienstprinzipal als AD-Administrator für den logischen SQL-Server festlegen möchten, können Sie das Azure-Portal, PowerShell oder Azure CLI-Befehle verwenden. Weitere Informationen finden Sie unter [Bereitstellen des Azure AD-Administrators (SQL-Datenbank)](authentication-aad-configure.md?tabs=azure-powershell#powershell-for-sql-database-and-azure-synapse).

## <a name="create-an-azure-ad-user-in-sql-database-using-an-azure-ad-service-principal"></a>Erstellen eines Azure AD-Benutzers in SQL-Datenbank mithilfe eines Azure AD-Dienstprinzipals

> [!IMPORTANT]
> Der Dienstprinzipal, der für die Anmeldung bei SQL-Datenbank verwendet wird, muss einen geheimen Clientschlüssel aufweisen. Wenn dies nicht der Fall ist, führen Sie Schritt 2 unter [Erstellen eines Dienstprinzipals (einer Azure AD-Anwendung) in Azure AD](#create-a-service-principal-an-azure-ad-application-in-azure-ad) aus. Dieser geheime Clientschlüssel muss als Eingabeparameter im folgenden Skript hinzugefügt werden.

1. Verwenden Sie das folgende Skript, um mithilfe des Dienstprinzipals *AppSP* einen Azure AD-Dienstprinzipalbenutzer *myapp* zu erstellen.

    - Ersetzen Sie `<TenantId>` durch Ihre zuvor ermittelte `TenantId`.
    - Ersetzen Sie `<ClientId>` durch den zuvor erfassten `ClientId`-Wert.
    - Ersetzen Sie `<ClientSecret>` durch Ihren geheimen Clientschlüssel, der zuvor erstellt wurde.
    - Ersetzen Sie `<server name>` durch den Namen Ihres logischen SQL-Servers. Wenn der Servername `myserver.database.windows.net` ist, ersetzen Sie `<server name>` durch `myserver`.
    - Ersetzen Sie `<database name>` durch den Namen der SQL-Datenbank.

    ```powershell
    # PowerShell script for creating a new SQL user called myapp using application AppSP with secret
    # AppSP is part of an Azure AD admin for the Azure SQL server below
    
    # Download latest  MSAL  - https://www.powershellgallery.com/packages/MSAL.PS
    Import-Module MSAL.PS
    
    $tenantId = "<TenantId>"   # tenantID (Azure Directory ID) were AppSP resides
    $clientId = "<ClientId>"   # AppID also ClientID for AppSP     
    $clientSecret = "<ClientSecret>"   # Client secret for AppSP 
    $scopes = "https://database.windows.net/.default" # The end-point
    
    $result = Get-MsalToken -RedirectUri $uri -ClientId $clientId -ClientSecret (ConvertTo-SecureString $clientSecret -AsPlainText -Force) -TenantId $tenantId -Scopes $scopes
    
    $Tok = $result.AccessToken
    #Write-host "token"
    $Tok
      
    $SQLServerName = "<server name>"    # Azure SQL logical server name 
    $DatabaseName = "<database name>"     # Azure SQL database name
    
    Write-Host "Create SQL connection string"
    $conn = New-Object System.Data.SqlClient.SQLConnection 
    $conn.ConnectionString = "Data Source=$SQLServerName.database.windows.net;Initial Catalog=$DatabaseName;Connect Timeout=30"
    $conn.AccessToken = $Tok
    
    Write-host "Connect to database and execute SQL script"
    $conn.Open() 
    $ddlstmt = 'CREATE USER [myapp] FROM EXTERNAL PROVIDER;'
    Write-host " "
    Write-host "SQL DDL command"
    $ddlstmt
    $command = New-Object -TypeName System.Data.SqlClient.SqlCommand($ddlstmt, $conn)       
    
    Write-host "results"
    $command.ExecuteNonQuery()
    $conn.Close()
    ``` 

    Alternativ können Sie das Codebeispiel im Blog [Azure AD Service Principal authentication to SQL DB - Code Sample](https://techcommunity.microsoft.com/t5/azure-sql-database/azure-ad-service-principal-authentication-to-sql-db-code-sample/ba-p/481467) (Azure AD-Dienstprinzipalauthentifizierung bei SQL DB: Codebeispiel) verwenden. Ändern Sie das Skript, um eine DDL-Anweisung `CREATE USER [myapp] FROM EXTERNAL PROVIDER` auszuführen. Das gleiche Skript kann verwendet werden, um einen regulären Azure AD-Benutzer einer Gruppe in SQL-Datenbank zu erstellen.

    
2. Überprüfen Sie, ob der Benutzer *myapp* in der Datenbank vorhanden ist, indem Sie den folgenden Befehl ausführen:

    ```sql
    SELECT name, type, type_desc, CAST(CAST(sid as varbinary(16)) as uniqueidentifier) as appId from sys.database_principals WHERE name = 'myapp'
    GO
    ```

    Eine ähnliche Ausgabe wie die folgende sollte angezeigt werden:

    ```output
    name    type    type_desc   appId
    myapp   E   EXTERNAL_USER   6d228f48-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    ```

## <a name="next-steps"></a>Nächste Schritte

- [Azure Active Directory-Dienstprinzipal mit Azure SQL](authentication-aad-service-principal.md)
- [Was sind verwaltete Identitäten für Azure-Ressourcen?](../../active-directory/managed-identities-azure-resources/overview.md)
- [Verwenden verwalteter Identitäten für App Service und Azure Functions](../../app-service/overview-managed-identity.md)
- [Azure AD Service Principal authentication to SQL DB - Code Sample (Azure AD-Dienstprinzipalauthentifizierung bei SQL DB: Codebeispiel)](https://techcommunity.microsoft.com/t5/azure-sql-database/azure-ad-service-principal-authentication-to-sql-db-code-sample/ba-p/481467)
- [Anwendungs- und Dienstprinzipalobjekte in Azure Active Directory](../../active-directory/develop/app-objects-and-service-principals.md)
- [Erstellen eines Azure-Dienstprinzipals mit Azure PowerShell](/powershell/azure/create-azure-service-principal-azureps)
- [Rolle „Verzeichnisleseberechtigte“ in Azure Active Directory für Azure SQL](authentication-aad-directory-readers-role.md)