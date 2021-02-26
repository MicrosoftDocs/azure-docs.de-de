---
title: Zuweisen der Rolle „Verzeichnisleseberechtigte“ zu einer Azure AD-Gruppe und Verwalten von Rollenzuweisungen
description: In diesem Artikel erfahren Sie Schritt für Schritt, wie Sie die Rolle „Verzeichnisleseberechtigte“ aktivieren und Azure AD-Gruppen verwenden, um Azure AD-Rollenzuweisungen mit Azure SQL-Datenbank, Azure SQL Managed Instance und Azure Synapse Analytics zu verwalten.
ms.service: sql-db-mi
ms.subservice: security
ms.custom: azure-synapse
ms.topic: tutorial
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 08/14/2020
ms.openlocfilehash: bc809cf02b827b7498890cb7d929c44bd360ab53
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/30/2021
ms.locfileid: "99094708"
---
# <a name="tutorial-assign-directory-readers-role-to-an-azure-ad-group-and-manage-role-assignments"></a>Tutorial: Zuweisen der Rolle „Verzeichnisleseberechtigte“ zu einer Azure AD-Gruppe und Verwalten von Rollenzuweisungen

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

> [!NOTE]
> Die in diesem Artikel verwendete Zuweisung der Rolle **Verzeichnisleseberechtigte** zu einer Gruppe befindet sich in der **Public Preview**-Phase. 

In diesem Artikel erfahren Sie, wie Sie eine Gruppe in Azure Active Directory (Azure AD) erstellen und dieser Gruppe die Rolle [**Verzeichnisleseberechtigte**](../../active-directory/roles/permissions-reference.md#directory-readers) zuweisen. Mit den Berechtigungen vom Typ „Verzeichnisleseberechtigte“ können die Gruppenbesitzer weitere Mitglieder zur Gruppe hinzufügen – beispielsweise eine [verwaltete Identität](../../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) von [Azure SQL-Datenbank](sql-database-paas-overview.md), [Azure SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md) und [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md). Das bedeutet, die Rolle „Verzeichnisleseberechtigte“ muss nicht mehr von einem Benutzer vom Typ [Globaler Administrator](../../active-directory/roles/permissions-reference.md#global-administrator) oder [Administrator für privilegierte Rollen](../../active-directory/roles/permissions-reference.md#privileged-role-administrator) direkt für jede Identität des logischen Azure SQL-Servers im Mandanten zugewiesen werden.

In diesem Tutorial wird das unter [Verwenden von Cloudgruppen zum Verwalten von Rollenzuweisungen in Azure Active Directory (Vorschau)](../../active-directory/roles/groups-concept.md) vorgestellte Feature verwendet. 

Weitere Informationen zu den Vorteilen, die das Zuweisen der Rolle „Verzeichnisleseberechtigte“ zu einer Azure AD-Gruppe für Azure SQL mit sich bringt, finden Sie unter [Rolle „Verzeichnisleseberechtigte“ in Azure Active Directory für Azure SQL](authentication-aad-directory-readers-role.md).

## <a name="prerequisites"></a>Voraussetzungen

- Eine Azure AD-Instanz. Weitere Informationen finden Sie unter [Konfigurieren und Verwalten der Azure Active Directory-Authentifizierung mit Azure SQL](authentication-aad-configure.md).
- Eine Instanz von SQL-Datenbank, SQL Managed Instance oder Azure Synapse.

## <a name="directory-readers-role-assignment-using-the-azure-portal"></a>Zuweisen der Rolle „Verzeichnisleseberechtigte“ über das Azure-Portal

### <a name="create-a-new-group-and-assign-owners-and-role"></a>Erstellen einer neuen Gruppe und Zuweisen von Besitzern und Rolle

1. Für diesen ersten Einrichtungsschritt ist ein Benutzer mit Berechtigungen vom Typ [Globaler Administrator](../../active-directory/roles/permissions-reference.md#global-administrator) oder [Administrator für privilegierte Rollen](../../active-directory/roles/permissions-reference.md#privileged-role-administrator) erforderlich.
1. Bitten Sie den privilegierten Benutzer, sich beim [Azure-Portal](https://portal.azure.com) anzumelden.
1. Navigieren Sie zur Ressource **Azure Active Directory**. Navigieren Sie unter **Verwalten** zu **Gruppen**. Wählen Sie **Neue Gruppe** aus, um eine neue Gruppe zu erstellen.
1. Wählen Sie als Gruppentyp die Option **Sicherheit** aus, und füllen Sie die restlichen Felder aus. Achten Sie darauf, dass die Einstellung **Azure AD-Rollen können der Gruppe zugewiesen werden (Vorschau)** auf **Ja** festgelegt ist. Weisen Sie der Gruppe als Nächstes die Azure AD-Rolle **Verzeichnisleseberechtigte** zu.
1. Weisen Sie der erstellten Gruppe Azure AD-Benutzer als Besitzer zu. Ein Gruppenbesitzer kann ein regulärer AD-Benutzer ohne Azure AD-Administratorrolle sein. Der Besitzer sollte ein Benutzer sein, der SQL-Datenbank, SQL Managed Instance oder Azure Synapse verwaltet.

   :::image type="content" source="media/authentication-aad-directory-readers-role/new-group.png" alt-text="aad-new-group":::

1. Klicken Sie auf **Erstellen**

### <a name="checking-the-group-that-was-created"></a>Überprüfen der erstellten Gruppe

> [!NOTE]
> Vergewissern Sie sich, dass der **Gruppentyp** auf **Sicherheit** festgelegt ist. Gruppen vom Typ *Microsoft 365* werden für Azure SQL nicht unterstützt.

Navigieren Sie zum Überprüfen und Verwalten der erstellten Gruppe im Azure-Portal wieder zum Bereich **Gruppen**, und suchen Sie nach dem Gruppennamen. Weitere Besitzer und Mitglieder können nach dem Auswählen der Gruppe unter der Einstellung **Verwalten** über das Menü **Besitzer** bzw. **Mitglieder** hinzugefügt werden. Unter **Zugewiesene Rollen** können Sie außerdem die zugewiesenen Rollen der Gruppe überprüfen.

:::image type="content" source="media/authentication-aad-directory-readers-role/azure-ad-group-created.png" alt-text="Screenshot: Gruppenbereich mit den Links, über die die Menüs „Einstellungen“ für die hervorgehobenen Rollen „Mitglieder“, „Besitzer“ und „Zugewiesene Rollen (Vorschau)“ geöffnet werden":::

### <a name="add-azure-sql-managed-identity-to-the-group"></a>Hinzufügen einer verwalteten Azure SQL-Identität zur Gruppe

> [!NOTE]
> In diesem Beispiel wird SQL Managed Instance verwendet. Für SQL-Datenbank oder Azure Synapse können jedoch mit ähnlichen Schritten die gleichen Ergebnisse erzielt werden.

Für die folgenden Schritte wird der Benutzer vom Typ „Globaler Administrator“ oder „Administrator für privilegierte Rollen“ nicht mehr benötigt.

1. Melden Sie sich beim Azure-Portal als Benutzer an, der SQL Managed Instance verwaltet und einer der Besitzer der zuvor erstellten Gruppe ist.

1. Suchen Sie im Azure-Portal nach dem Namen Ihrer Ressource vom Typ **SQL Managed Instance**.

   :::image type="content" source="media/authentication-aad-directory-readers-role/azure-ad-managed-instance.png" alt-text="Screenshot: Bildschirm der verwalteten SQL-Instanzen, auf dem der Name „ssomitest“ der SQL-Instanz und der Subnetzname „ManagedInstance“ hervorgehoben sind":::

   Im Rahmen der Erstellung Ihrer SQL Managed Instance-Instanz wurde eine Azure-Identität für Ihre Instanz erstellt. Die erstellte Identität hat den gleichen Namen wie das Präfix Ihres SQL Managed Instance-Namens. Den Dienstprinzipal für Ihre SQL Managed Instance-Identität, die als Azure AD-Anwendung erstellt wurde, finden Sie wie folgt:

    - Navigieren Sie zur Ressource **Azure Active Directory**. Wählen Sie unter der Einstellung **Verwalten** die Option **Unternehmensanwendungen** aus. Die **Objekt-ID** ist die Identität der Instanz.
    
    :::image type="content" source="media/authentication-aad-directory-readers-role/azure-ad-managed-instance-service-principal.png" alt-text="Screenshot: Seite „Unternehmensanwendungen“ für eine Azure Active Directory-Ressource mit hervorgehobener Objekt-ID der verwalteten SQL-Instanz":::

1. Navigieren Sie zur Ressource **Azure Active Directory**. Navigieren Sie unter **Verwalten** zu **Gruppen**. Wählen Sie die von Ihnen erstellte Gruppe aus. Wählen Sie unter der Einstellung **Verwalten** Ihrer Gruppe die Option **Mitglieder** aus. Wählen Sie **Mitglieder hinzufügen** aus, und fügen Sie Ihren SQL Managed Instance-Dienstprinzipal als Gruppenmitglied hinzu, indem Sie nach dem oben gefundenen Namen suchen.

   :::image type="content" source="media/authentication-aad-directory-readers-role/azure-ad-add-managed-instance-service-principal.png" alt-text="Screenshot: Seite „Mitglieder“ für eine Azure Active Directory-Ressource mit hervorgehobenen Optionen zum Hinzufügen einer verwalteten SQL-Instanz als neues Mitglied":::

> [!NOTE]
> Es kann einige Minuten dauern, bis die Berechtigungen des Dienstprinzipals im gesamten Azure-System verteilt wurden und auf die Azure AD Graph-API zugegriffen werden kann. Warten Sie ggf. einige Minuten, bevor Sie einen Azure AD-Administrator für SQL Managed Instance bereitstellen.

### <a name="remarks"></a>Hinweise

Bei SQL-Datenbank und Azure Synapse kann die Serveridentität im Rahmen der Erstellung des logischen Azure SQL-Servers oder nach der Erstellung des Servers erstellt werden. Weitere Informationen zum Erstellen oder Festlegen der Serveridentität in SQL-Datenbank oder Azure Synapse finden Sie unter [Aktivieren von Dienstprinzipalen zum Erstellen von Azure AD-Benutzern](authentication-aad-service-principal.md#enable-service-principals-to-create-azure-ad-users).

Bei SQL Managed Instance muss die Rolle **Verzeichnisleseberechtigte** der Identität der verwalteten Instanz zugewiesen werden, damit Sie [einen Azure AD-Administrator für die verwaltete Instanz einrichten](authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance) können.

Bei SQL-Datenbank und Azure Synapse ist die Zuweisung der Rolle **Verzeichnisleseberechtigte** zur Serveridentität nicht erforderlich, wenn Sie einen Azure AD-Administrator für den logischen Server einrichten. Wenn Sie allerdings die Erstellung eines Azure AD-Objekts in SQL-Datenbank oder Azure Synapse im Auftrag einer Azure AD-Anwendung ermöglichen möchten, ist die Rolle **Verzeichnisleseberechtigte** erforderlich. Ist die Rolle nicht der Identität des logischen SQL-Servers zugewiesen, tritt beim Erstellen von Azure AD-Benutzern in Azure SQL ein Fehler auf. Weitere Informationen finden Sie unter [Azure Active Directory-Dienstprinzipal mit Azure SQL](authentication-aad-service-principal.md).

## <a name="directory-readers-role-assignment-using-powershell"></a>Zuweisen der Rolle „Verzeichnisleseberechtigte“ mithilfe von PowerShell

> [!IMPORTANT]
> Diese ersten Schritte müssen von einem Benutzer vom Typ [Globaler Administrator](../../active-directory/roles/permissions-reference.md#global-administrator) oder [Administrator für privilegierte Rollen](../../active-directory/roles/permissions-reference.md#privileged-role-administrator) ausgeführt werden. Neben PowerShell bietet Azure AD die Microsoft Graph-API zum [Erstellen einer Gruppe, der Rollen zugewiesen werden können, in Azure AD](../../active-directory/roles/groups-create-eligible.md#using-microsoft-graph-api).

1. Laden Sie das Azure AD PowerShell-Vorschaumodul mithilfe der folgenden Befehle herunter. PowerShell muss ggf. mit Administratorrechten ausgeführt werden.

    ```powershell
    Install-Module azureadpreview
    Import-Module azureadpreview
    #To verify that the module is ready to use, use the following command:
    Get-Module azureadpreview
    ```

1. Stellen Sie eine Verbindung mit Ihrem Azure AD-Mandanten her.

    ```powershell
    Connect-AzureAD
    ```

1. Erstellen Sie eine Sicherheitsgruppe, um die Rolle **Verzeichnisleseberechtigte** zuzuweisen.

    - `DirectoryReaderGroup`, `Directory Reader Group` und `DirRead` können nach Belieben geändert werden.

    ```powershell
    $group = New-AzureADMSGroup -DisplayName "DirectoryReaderGroup" -Description "Directory Reader Group" -MailEnabled $False -SecurityEnabled $true -MailNickName "DirRead" -IsAssignableToRole $true
    $group
    ```

1. Weisen Sie der Gruppe die Rolle **Verzeichnisleseberechtigte** zu.

    ```powershell
    # Displays the Directory Readers role information
    $roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Directory Readers'" 
    $roleDefinition
    ```

    ```powershell
    # Assigns the Directory Readers role to the group
    $roleAssignment = New-AzureADMSRoleAssignment -ResourceScope '/' -RoleDefinitionId $roleDefinition.Id -PrincipalId $group.Id
    $roleAssignment
    ```

1. Weisen Sie der Gruppe Besitzer zu.

    - Ersetzen Sie `<username>` durch den Benutzer, der als Besitzer dieser Gruppe fungieren soll. Falls Sie mehrere Besitzer hinzufügen möchten, können Sie diese Schritte wiederholen.

    ```powershell
    $RefObjectID = Get-AzureADUser -ObjectId "<username>"
    $RefObjectID
    ```

    ```powershell
    $GrOwner = Add-AzureADGroupOwner -ObjectId $group.ID -RefObjectId $RefObjectID.ObjectID
    ```

    Überprüfen Sie die Besitzer der Gruppe mithilfe des folgenden Befehls:

    ```powershell
    Get-AzureADGroupOwner -ObjectId $group.ID
    ```

    Die Besitzer der Gruppe können auch im [Azure-Portal](https://portal.azure.com) überprüft werden. Gehen Sie dazu wie unter [Überprüfen der erstellten Gruppe](#checking-the-group-that-was-created) beschrieben vor.

### <a name="assigning-the-service-principal-as-a-member-of-the-group"></a>Zuweisen des Dienstprinzipals als Mitglied der Gruppe

Für die folgenden Schritte wird der Benutzer vom Typ „Globaler Administrator“ oder „Administrator für privilegierte Rollen“ nicht mehr benötigt.

1. Führen Sie als Besitzer der Gruppe, der auch die Azure SQL-Ressource verwaltet, den folgenden Befehl aus, um eine Verbindung mit Ihrer Azure AD-Instanz herzustellen:

    ```powershell
    Connect-AzureAD
    ```

1. Weisen Sie den Dienstprinzipal als Mitglied der erstellten Gruppe zu.

    - Ersetzen Sie `<ServerName>` durch den Namen Ihres logischen Azure SQL-Servers oder durch den Namen Ihrer verwalteten Instanz. Weitere Informationen finden Sie im Abschnitt [Hinzufügen einer verwalteten Azure SQL-Identität zur Gruppe](#add-azure-sql-managed-identity-to-the-group).

    ```powershell
    # Returns the service principal of your Azure SQL resource
    $miIdentity = Get-AzureADServicePrincipal -SearchString "<ServerName>"
    $miIdentity
    ```

    ```powershell
    # Adds the service principal to the group as a member
    Add-AzureADGroupMember -ObjectId $group.ID -RefObjectId $miIdentity.ObjectId 
    ```

    Der folgende Befehl gibt die Objekt-ID des Dienstprinzipals zurück, was bedeutet, dass er der Gruppe hinzugefügt wurde:

    ```powershell
    Add-AzureADGroupMember -ObjectId $group.ID -RefObjectId $miIdentity.ObjectId
    ```

## <a name="next-steps"></a>Nächste Schritte

- [Rolle „Verzeichnisleseberechtigte“ in Azure Active Directory für Azure SQL](authentication-aad-directory-readers-role.md)
- [Tutorial: Erstellen von Azure AD-Benutzern mithilfe von Azure AD-Anwendungen](authentication-aad-service-principal-tutorial.md)
- [Konfigurieren und Verwalten der Azure Active Directory-Authentifizierung mit Azure SQL](authentication-aad-configure.md)