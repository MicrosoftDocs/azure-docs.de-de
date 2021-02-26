---
title: Sichere WebHook-Zustellung mit Azure AD in Azure Event Grid
description: Beschreibt, wie Ereignisse mittels Azure Event Grid an HTTPS-Endpunkte zugestellt werden, die von Azure Active Directory geschützt werden.
ms.topic: how-to
ms.date: 02/03/2021
ms.openlocfilehash: b01f0342e185883c4d1552800ab9bdb30cea3040
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99549151"
---
# <a name="publish-events-to-azure-active-directory-protected-endpoints"></a>Veröffentlichen von Ereignissen auf mit Azure Active Directory geschützten Endpunkten

Dieser Artikel beschreibt, wie Sie Azure Active Directory nutzen können, um die Verbindung zwischen Ihrem Ereignisabonnement und Ihrem Webhook-Endpunkt zu sichern. Eine Übersicht über Azure AD-Anwendungen und -Dienstprinzipale finden Sie unter [Microsoft Identity Platform (v2.0): Übersicht](../active-directory/develop/v2-overview.md).

In diesem Artikel wird das Azure-Portal zur Demonstration verwendet. Die Funktion kann jedoch auch über mit der CLI, der PowerShell oder den SDKs aktiviert werden.


## <a name="create-an-azure-ad-application"></a>Erstellen einer Azure AD-Anwendung

Beginnen Sie damit, eine Azure AD-Anwendung für Ihren geschützten Endpunkt zu erstellen. Siehe https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-overview.
    - Konfigurieren Sie Ihre geschützte API so, dass Sie von einer Daemon-App aufgerufen wird.
    
## <a name="enable-event-grid-to-use-your-azure-ad-application"></a>Aktivieren von Event Grid zur Verwendung Ihrer Azure AD-Anwendung
In diesem Abschnitt erfahren Sie, wie Sie Event Grid für die Verwendung Ihrer Azure AD-Anwendung aktivieren. 

> [!NOTE]
> Sie müssen Mitglied der [Rolle „Azure AD-Anwendungsadministrator“](../active-directory/roles/permissions-reference.md#available-roles) sein, um dieses Skript ausführen zu können.

### <a name="connect-to-your-azure-tenant"></a>Herstellen einer Verbindung mit Ihrem Azure-Mandanten
Stellen Sie zunächst mithilfe des `Connect-AzureAD`-Befehls eine Verbindung mit Ihrem Azure-Mandanten her. 

```PowerShell
# This is your Tenant Id. 
$myTenantId = "<the Tenant Id of your Azure AD Application>"
Connect-AzureAD -TenantId $myTenantId
```

### <a name="create-microsofteventgrid-service-principal"></a>Erstellen des Microsoft.EventGrid-Dienstprinzipals
Führen Sie das folgende Skript aus, um den Dienstprinzipal für **Microsoft.EventGrid** zu erstellen, wenn er nicht bereits vorhanden ist. 

```PowerShell
# This is the "Azure Event Grid" Azure Active Directory AppId
$eventGridAppId = "4962773b-9cdb-44cf-a8bf-237846a00ab7"
    
$eventGridSP = Get-AzureADServicePrincipal -Filter ("appId eq '" + $eventGridAppId + "'")

# Create the service principal if it doesn't exist
if ($eventGridSP -match "Microsoft.EventGrid")
{
    Write-Host "The Service principal is already defined.`n"
} else
{
    # Create a service principal for the "Azure Event Grid" Azure AD Application and add it to the role
    $eventGridSP = New-AzureADServicePrincipal -AppId $eventGridAppId
}
```

### <a name="create-a-role-for-your-application"></a>Erstellen einer Rolle für Ihre Anwendung   
Führen Sie das folgende Skript aus, um eine Rolle für die Azure AD-Anwendung zu erstellen. In diesem Beispiel lautet der Rollenname wie folgt: **AzureEventGridSecureWebhook**. Ändern Sie `$myTenantId` des PowerShell-Skripts so, dass Ihre Azure AD-Mandanten-ID und `$myAzureADApplicationObjectId` mit der Objekt-ID Ihrer Azure AD-Anwendung verwendet wird.

```PowerShell
# This is your Azure AD Application's ObjectId. 
$myAzureADApplicationObjectId = "<the Object Id of your Azure AD Application>"
    
# This is the name of the new role we will add to your Azure AD Application
$eventGridRoleName = "AzureEventGridSecureWebhook"
    
# Create an application role of given name and description
Function CreateAppRole([string] $Name, [string] $Description)
{
    $appRole = New-Object Microsoft.Open.AzureAD.Model.AppRole
    $appRole.AllowedMemberTypes = New-Object System.Collections.Generic.List[string]
    $appRole.AllowedMemberTypes.Add("Application");
    $appRole.DisplayName = $Name
    $appRole.Id = New-Guid
    $appRole.IsEnabled = $true
    $appRole.Description = $Description
    $appRole.Value = $Name;
    return $appRole
}

# Get my Azure AD Application, it's roles and service principal
$myApp = Get-AzureADApplication -ObjectId $myAzureADApplicationObjectId
$myAppRoles = $myApp.AppRoles

Write-Host "App Roles before addition of new role.."
Write-Host $myAppRoles
    
# Create the role if it doesn't exist
if ($myAppRoles -match $eventGridRoleName)
{
    Write-Host "The Azure Event Grid role is already defined.`n"
    $myServicePrincipal = Get-AzureADServicePrincipal -Filter ("appId eq '" + $myApp.AppId + "'")
} else
{
    $myServicePrincipal = Get-AzureADServicePrincipal -Filter ("appId eq '" + $myApp.AppId + "'")
    
    # Add our new role to the Azure AD Application
    $newRole = CreateAppRole -Name $eventGridRoleName -Description "Azure Event Grid Role"
    $myAppRoles.Add($newRole)
    Set-AzureADApplication -ObjectId $myApp.ObjectId -AppRoles $myAppRoles
}

# print application's roles
Write-Host "My Azure AD Application's Roles: "
Write-Host $myAppRoles
```

### <a name="add-event-grid-service-principal-to-the-role"></a>Hinzufügen des Event Grid-Dienstprinzipals zur Rolle    
Führen Sie nun den `New-AzureADServiceAppRoleAssignment`-Befehl aus, um der im vorherigen Schritt erstellten Rolle den Event Grid-Dienstprinzipal zuzuweisen. 

```powershell
New-AzureADServiceAppRoleAssignment -Id $myApp.AppRoles[0].Id -ResourceId $myServicePrincipal.ObjectId -ObjectId $eventGridSP.ObjectId -PrincipalId $eventGridSP.ObjectId
```

Führen Sie die folgenden Befehle aus, um Informationen auszugeben, die Sie in den nächsten Schritten verwenden werden. 

```powershell    
Write-Host "My Azure AD Tenant Id: $myTenantId"
Write-Host "My Azure AD Application Id: $($myApp.AppId)"
Write-Host "My Azure AD Application ObjectId: $($myApp.ObjectId)"
```
    
## <a name="configure-the-event-subscription"></a>Konfigurieren des Ereignisabonnements

Wählen Sie im Erstellungsablauf für Ihr Ereignisabonnement den Endpunkttyp „Webhook“ aus. Nachdem Sie Ihren Endpunkt-URI angegeben haben, klicken Sie am oberen Rand des Blatts zum Erstellen von Ereignisabonnements auf die Registerkarte „Zusätzliche Features“.

![Auswählen des Endpunkttyps „Webhook“](./media/secure-webhook-delivery/select-webhook.png)

Aktivieren Sie auf der Registerkarte „Zusätzliche Features“ das Kontrollkästchen für „AAD-Authentifizierung verwenden“, und konfigurieren Sie die Mandanten-ID und die Anwendungs-ID:

* Kopieren Sie die Azure AD-Mandanten-ID aus der Ausgabe des Skripts, und geben Sie sie in das Feld für die AAD-Mandanten-ID ein.
* Kopieren Sie die Azure AD-Anwendungs-ID aus der Ausgabe des Skripts, und geben Sie sie in das Feld für die AAD-Anwendungs-ID ein.

    ![Sichere Webhookaktion](./media/secure-webhook-delivery/aad-configuration.png)

## <a name="next-steps"></a>Nächste Schritte

* Informationen zur Überwachung von Ereignisübermittlungen finden Sie unter [Überwachen der Event Grid-Nachrichtenübermittlung](monitor-event-delivery.md).
* Weitere Informationen zum Authentifizierungsschlüssel finden Sie unter [Event Grid – Sicherheit und Authentifizierung](security-authentication.md).
* Weitere Informationen zum Erstellen eines Azure Event Grid-Abonnements finden Sie unter [Event Grid-Abonnementschema](subscription-creation-schema.md).
