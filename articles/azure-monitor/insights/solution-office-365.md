---
title: Office 365-Verwaltungslösung in Azure | Microsoft-Dokumentation
description: Dieser Artikel bietet Einzelheiten zur Konfiguration und Verwendung der Office 365-Lösung in Azure.  Er enthält eine ausführliche Beschreibung der Office 365-Datensätze, die in Azure Monitor erstellt werden.
services: operations-management-suite
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: operations-management-suite
ms.workload: tbd
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 05/29/2019
ms.author: bwren
ms.openlocfilehash: 4c7e1225a8da1e20bc90986d1530b781f7f2c11a
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66357576"
---
# <a name="office-365-management-solution-in-azure-preview"></a>Office 365-Verwaltungslösung in Azure (Vorschau)

![Office 365-Logo](media/solution-office-365/icon.png)


> [!NOTE]
> Die empfohlene Methode zum Installieren und Konfigurieren der Office 365-Lösung ist das Aktivieren des [Office 365-Connectors](../../sentinel/connect-office-365.md) in [Azure Sentinel](../../sentinel/overview.md), statt die Schritte in diesem Artikel auszuführen. Dies ist eine aktualisierte Version der Office 365-Lösung mit einer verbesserten Konfigurationsumgebung. Verwenden Sie zum Herstellen einer Verbindung mit Azure AD-Protokollen den [Azure AD-Connector von Azure Sentinel](../../sentinel/connect-azure-active-directory.md), mit dem umfangreichere Protokolldaten als bei den Office 365-Verwaltungsprotokollen bereitgestellt werden. 
>
> Beim [Integrieren von Azure Sentinel](../../sentinel/quickstart-onboard.md) geben Sie den Log Analytics-Arbeitsbereich an, in dem die Office 365-Lösung installiert werden soll. Nachdem Sie den Connector aktiviert haben, ist die Lösung im Arbeitsbereich verfügbar und wird auf genau die gleiche Weise wie alle anderen installierten Überwachungslösungen verwendet.
>
> Benutzer der Azure Government-Cloud müssen Office 365 anhand der Schritte in diesem Artikel installieren, da Azure Sentinel in der Government-Cloud noch nicht verfügbar ist.

Mit der Office 365-Verwaltungslösung können Sie Ihre Office 365-Umgebung in Azure Monitor überwachen.

- Überwachen Sie Benutzeraktivitäten mit Ihren Office 365-Konten, um die Verwendungsmuster zu analysieren und Trends beim Verhalten zu identifizieren. Beispielsweise können Sie spezielle Verwendungsszenarien extrahieren, wie z.B. Dateien, die außerhalb Ihrer Organisation freigegeben werden, oder die am häufigsten verwendeten SharePoint-Websites.
- Überwachen Sie Administratoraktivitäten, um Konfigurationsänderungen oder Vorgänge mit erhöhten Rechten nachzuverfolgen.
- Ermitteln und untersuchen Sie unerwünschtes Benutzerverhalten. Dies kann an die Anforderungen Ihrer Organisation angepasst werden.
- Demonstrieren Sie Überwachung und Compliance. Beispielsweise können Sie Dateizugriffe auf vertrauliche Dateien überwachen und so den Überwachungs- und Complianceprozess unterstützen.
- Führen Sie eine operative Problembehandlung mithilfe der [Protokollabfragen](../log-query/log-query-overview.md) in den Office 365-Aktivitätsdaten Ihrer Organisation durch.


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Voraussetzungen

Folgendes ist erforderlich, bevor diese Lösung installiert und konfiguriert wird.

- Office 365-Organisationsabonnement.
- Anmeldeinformationen für ein Benutzerkonto, das ein globaler Administrator ist.
- Um Überwachungsdaten zu erhalten, müssen Sie [die Überwachung](https://support.office.com/article/Search-the-audit-log-in-the-Office-365-Security-Compliance-Center-0d4d0f35-390b-4518-800e-0c7ec95e946c?ui=en-US&rs=en-US&ad=US#PickTab=Before_you_begin) in Ihrem Office 365-Abonnement konfigurieren.  Beachten Sie, dass die [Postfachüberwachung](https://technet.microsoft.com/library/dn879651.aspx) separat konfiguriert wird.  Sie können die Lösung auch installieren und andere Daten sammeln, wenn die Überwachung nicht konfiguriert ist.
 

## <a name="management-packs"></a>Management Packs

Bei dieser Lösung werden keine Management Packs in [verbundenen Verwaltungsgruppen](../platform/om-agents.md) installiert.
  

## <a name="install-and-configure"></a>Installieren und konfigurieren

Starten Sie durch Hinzufügen der [Office 365-Lösung zu Ihrem Abonnement](solutions.md#install-a-monitoring-solution). Führen Sie nach dem Hinzufügen die Konfigurationsschritte in diesem Abschnitt aus, um den Zugriff auf Ihr Office 365-Abonnement zu ermöglichen.

### <a name="required-information"></a>Erforderliche Informationen

Bevor Sie diese Prozedur starten, sammeln Sie die folgenden Informationen.

Aus Ihrem Log Analytics-Arbeitsbereich:

- Arbeitsbereichsname: Der Arbeitsbereich, in dem die Office 365-Daten gesammelt werden
- Ressourcengruppenname: Die Ressourcengruppe, die den Arbeitsbereich enthält
- Azure-Abonnement-ID: Das Abonnement, das den Arbeitsbereich enthält

Aus Ihrem Office 365-Abonnement:

- Benutzername: Die E-Mail-Adresse eines Administratorkontos
- Mandanten-ID: Die eindeutige ID für das Office 365-Abonnement
- Client-ID: Eine aus 16 Zeichen bestehende Zeichenfolge, die den Office 365-Client darstellt
- Geheimer Clientschlüssel: Eine für die Authentifizierung erforderliche verschlüsselte Zeichenfolge

### <a name="create-an-office-365-application-in-azure-active-directory"></a>Erstellen einer Office 365-Anwendung in Azure Active Directory

Im ersten Schritt muss in Azure Active Directory eine Anwendung erstellt werden, mit der die Verwaltungslösung auf Ihre Office 365-Lösung zugreift.

1. Melden Sie sich unter [https://portal.azure.com](https://portal.azure.com/) beim Azure-Portal an.
1. Wählen Sie **Azure Active Directory** und dann **App-Registrierungen** aus.
1. Klicken Sie auf **Registrierung einer neuen Anwendung**.

    ![Hinzufügen einer App-Registrierung](media/solution-office-365/add-app-registration.png)
1. Geben Sie einen **Anwendungsnamen** und Ihre **Anmelde-URL** ein.  Der Name sollte aussagekräftig sein.  Verwenden Sie für die URL `http://localhost`, und behalten Sie _Web-App/API_ als **Anwendungstyp** bei.
    
    ![Erstellen einer Anwendung](media/solution-office-365/create-application.png)
1. Klicken Sie auf **Erstellen** und überprüfen Sie die Anwendungsinformationen.

    ![Registrierte App](media/solution-office-365/registered-app.png)

### <a name="configure-application-for-office-365"></a>Konfigurieren einer Anwendung für Office 365

1. Klicken Sie auf **Einstellungen**, um das Menü **Einstellungen** zu öffnen.
1. Wählen Sie **Eigenschaften** aus. Ändern Sie **Mehrinstanzenfähig** in _Ja_.

    ![Einstellungen „Mehrinstanzenfähig“](media/solution-office-365/settings-multitenant.png)

1. Wählen Sie **Erforderliche Berechtigungen** im Menü **Einstellungen**, und klicken Sie auf **Hinzufügen**.
1. Klicken Sie auf **Eine API auswählen** und dann auf **Office 365-Verwaltungs-APIs**. Klicken Sie auf **Office 365-Verwaltungs-APIs**. Klicken Sie auf **Auswählen**.

    ![Auswählen der API](media/solution-office-365/select-api.png)

1. Wählen Sie unter **Berechtigungen auswählen** die folgenden Optionen für sowohl **Anwendungsberechtigungen** als auch **Delegierte Berechtigungen**:
   - Dienstintegritätsinformationen für Ihre Organisation lesen
   - Aktivitätsdaten für Ihre Organisation lesen
   - Aktivitätsberichte für Ihre Organisation lesen

     ![Auswählen der API](media/solution-office-365/select-permissions.png)

1. Klicken Sie auf **Auswählen** und dann auf **Fertig**.
1. Klicken Sie auf **Berechtigungen erteilen** und dann auf **Ja**, wenn Sie um Überprüfung gebeten werden.

    ![Erteilen von Berechtigungen](media/solution-office-365/grant-permissions.png)

### <a name="add-a-key-for-the-application"></a>Hinzufügen eines Schlüssels für die Anwendung

1. Wählen Sie **Schlüssel** im Menü **Einstellungen**.
1. Geben Sie eine **Beschreibung** und die **Dauer** für den neuen Schlüssel ein.
1. Klicken Sie auf **Speichern**, und kopieren Sie dann den generierten **Wert**.

    ![Schlüssel](media/solution-office-365/keys.png)

### <a name="add-admin-consent"></a>Hinzufügen der Administratorzustimmung

Um das Administratorkonto zum ersten Mal zu aktivieren, müssen Sie die Administratorzustimmung für die Anwendung geben. Hierfür können Sie ein PowerShell-Skript verwenden. 

1. Speichern Sie das folgende Skript als *office365_consent.ps1*.

    ```powershell
    param (
        [Parameter(Mandatory=$True)][string]$WorkspaceName,     
        [Parameter(Mandatory=$True)][string]$ResourceGroupName,
        [Parameter(Mandatory=$True)][string]$SubscriptionId
    )
    
    $option = [System.StringSplitOptions]::RemoveEmptyEntries 
    
    IF ($Subscription -eq $null)
        {Login-AzAccount -ErrorAction Stop}
    $Subscription = (Select-AzSubscription -SubscriptionId $($SubscriptionId) -ErrorAction Stop)
    $Subscription
    $Workspace = (Set-AzOperationalInsightsWorkspace -Name $($WorkspaceName) -ResourceGroupName $($ResourceGroupName) -ErrorAction Stop)
    $WorkspaceLocation= $Workspace.Location
    $WorkspaceLocation
    
    Function AdminConsent{
    
    $domain='login.microsoftonline.com'
    switch ($WorkspaceLocation.Replace(" ","").ToLower()) {
           "eastus"   {$OfficeAppClientId="d7eb65b0-8167-4b5d-b371-719a2e5e30cc"; break}
           "westeurope"   {$OfficeAppClientId="c9005da2-023d-40f1-a17a-2b7d91af4ede"; break}
           "southeastasia"   {$OfficeAppClientId="09c5b521-648d-4e29-81ff-7f3a71b27270"; break}
           "australiasoutheast"  {$OfficeAppClientId="f553e464-612b-480f-adb9-14fd8b6cbff8"; break}   
           "westcentralus"  {$OfficeAppClientId="98a2a546-84b4-49c0-88b8-11b011dc8c4e"; break}
           "japaneast"   {$OfficeAppClientId="b07d97d3-731b-4247-93d1-755b5dae91cb"; break}
           "uksouth"   {$OfficeAppClientId="f232cf9b-e7a9-4ebb-a143-be00850cd22a"; break}
           "centralindia"   {$OfficeAppClientId="ffbd6cf4-cba8-4bea-8b08-4fb5ee2a60bd"; break}
           "canadacentral"  {$OfficeAppClientId="c2d686db-f759-43c9-ade5-9d7aeec19455"; break}
           "eastus2"  {$OfficeAppClientId="7eb65b0-8167-4b5d-b371-719a2e5e30cc"; break}
           "westus2"  {$OfficeAppClientId="98a2a546-84b4-49c0-88b8-11b011dc8c4e"; break} #Need to check
           "usgovvirginia" {$OfficeAppClientId="c8b41a87-f8c5-4d10-98a4-f8c11c3933fe"; 
                             $domain='login.microsoftonline.us'; break}
           default {$OfficeAppClientId="55b65fb5-b825-43b5-8972-c8b6875867c1";
                    $domain='login.windows-ppe.net'; break} #Int
        }
    
        $domain
        Start-Process -FilePath  "https://$($domain)/common/adminconsent?client_id=$($OfficeAppClientId)&state=12345"
    }
    
    AdminConsent -ErrorAction Stop
    ```

2. Führen Sie das Skript mit dem folgenden Befehl aus: Sie werden zweimal zur Eingabe von Anmeldeinformationen aufgefordert. Geben Sie zuerst die Anmeldeinformationen für Ihren Log Analytics-Arbeitsbereich und dann die globalen Administratoranmeldeinformationen für Ihren Office 365-Mandanten ein.

    ```
    .\office365_consent.ps1 -WorkspaceName <Workspace name> -ResourceGroupName <Resource group name> -SubscriptionId <Subscription ID>
    ```

    Beispiel:

    ```
    .\office365_consent.ps1 -WorkspaceName MyWorkspace -ResourceGroupName MyResourceGroup -SubscriptionId '60b79d74-f4e4-4867-b631- yyyyyyyyyyyy'
    ```

1. Es wird ein ähnliches Fenster wie unten angezeigt. Klicken Sie auf **Annehmen**.
    
    ![Administratorzustimmung](media/solution-office-365/admin-consent.png)

### <a name="subscribe-to-log-analytics-workspace"></a>Abonnieren des Log Analytics-Arbeitsbereichs

Als letzten Schritt müssen Sie die Anwendung für Ihren Log Analytics-Arbeitsbereich abonnieren. Dafür verwenden Sie ebenfalls ein PowerShell-Skript.

1. Speichern Sie das folgende Skript als *office365_subscription.ps1*.

    ```powershell
    param (
        [Parameter(Mandatory=$True)][string]$WorkspaceName,
        [Parameter(Mandatory=$True)][string]$ResourceGroupName,
        [Parameter(Mandatory=$True)][string]$SubscriptionId,
        [Parameter(Mandatory=$True)][string]$OfficeUsername,
        [Parameter(Mandatory=$True)][string]$OfficeTennantId,
        [Parameter(Mandatory=$True)][string]$OfficeClientId,
        [Parameter(Mandatory=$True)][string]$OfficeClientSecret
    )
    $line='#-------------------------------------------------------------------------------------------------------------------------------------------------------------------------'
    $line
    IF ($Subscription -eq $null)
        {Login-AzAccount -ErrorAction Stop}
    $Subscription = (Select-AzSubscription -SubscriptionId $($SubscriptionId) -ErrorAction Stop)
    $Subscription
    $option = [System.StringSplitOptions]::RemoveEmptyEntries 
    $Workspace = (Set-AzOperationalInsightsWorkspace -Name $($WorkspaceName) -ResourceGroupName $($ResourceGroupName) -ErrorAction Stop)
    $Workspace
    $WorkspaceLocation= $Workspace.Location
    $OfficeClientSecret =[uri]::EscapeDataString($OfficeClientSecret)
    
    # Client ID for Azure PowerShell
    $clientId = "1950a258-227b-4e31-a9cf-717495945fc2"
    # Set redirect URI for Azure PowerShell
    $redirectUri = "urn:ietf:wg:oauth:2.0:oob"
    $domain='login.microsoftonline.com'
    $adTenant = $Subscription[0].Tenant.Id
    $authority = "https://login.windows.net/$adTenant";
    $ARMResource ="https://management.azure.com/";
    $xms_client_tenant_Id ='55b65fb5-b825-43b5-8972-c8b6875867c1'
    
    switch ($WorkspaceLocation) {
           "USGov Virginia" { 
                             $domain='login.microsoftonline.us';
                              $authority = "https://login.microsoftonline.us/$adTenant";
                              $ARMResource ="https://management.usgovcloudapi.net/"; break} # US Gov Virginia
           default {
                    $domain='login.microsoftonline.com'; 
                    $authority = "https://login.windows.net/$adTenant";
                    $ARMResource ="https://management.azure.com/";break} 
                    }
    
    Function RESTAPI-Auth { 
    
    $global:SubscriptionID = $Subscription.SubscriptionId
    # Set Resource URI to Azure Service Management API
    $resourceAppIdURIARM=$ARMResource;
    # Authenticate and Acquire Token 
    # Create Authentication Context tied to Azure AD Tenant
    $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList $authority
    # Acquire token
    $global:authResultARM = $authContext.AcquireToken($resourceAppIdURIARM, $clientId, $redirectUri, "Auto")
    $authHeader = $global:authResultARM.CreateAuthorizationHeader()
    $authHeader
    }
    
    Function Failure {
    $line
    $formatstring = "{0} : {1}`n{2}`n" +
                    "    + CategoryInfo          : {3}`n" +
                    "    + FullyQualifiedErrorId : {4}`n"
    $fields = $_.InvocationInfo.MyCommand.Name,
              $_.ErrorDetails.Message,
              $_.InvocationInfo.PositionMessage,
              $_.CategoryInfo.ToString(),
              $_.FullyQualifiedErrorId
    
    $formatstring -f $fields
    $_.Exception.Response
    
    $line
    break
    }
    
    Function Connection-API
    {
    $authHeader = $global:authResultARM.CreateAuthorizationHeader()
    $ResourceName = "https://manage.office.com"
    $SubscriptionId   =  $Subscription[0].Subscription.Id
    
    $line
    $connectionAPIUrl = $ARMResource + 'subscriptions/' + $SubscriptionId + '/resourceGroups/' + $ResourceGroupName + '/providers/Microsoft.OperationalInsights/workspaces/' + $WorkspaceName + '/connections/office365connection_' + $SubscriptionId + $OfficeTennantId + '?api-version=2017-04-26-preview'
    $connectionAPIUrl
    $line
    
    $xms_client_tenant_Id ='1da8f770-27f4-4351-8cb3-43ee54f14759'
    
    $BodyString = "{
                    'properties': {
                                    'AuthProvider':'Office365',
                                    'clientId': '" + $OfficeClientId + "',
                                    'clientSecret': '" + $OfficeClientSecret + "',
                                    'Username': '" + $OfficeUsername   + "',
                                    'Url': 'https://$($domain)/" + $OfficeTennantId + "/oauth2/token',
                                  },
                    'etag': '*',
                    'kind': 'Connection',
                    'solution': 'Connection',
                   }"
    
    $params = @{
        ContentType = 'application/json'
        Headers = @{
        'Authorization'="$($authHeader)"
        'x-ms-client-tenant-id'=$xms_client_tenant_Id #Prod-'1da8f770-27f4-4351-8cb3-43ee54f14759'
        'Content-Type' = 'application/json'
        }
        Body = $BodyString
        Method = 'Put'
        URI = $connectionAPIUrl
    }
    $response = Invoke-WebRequest @params 
    $response
    $line
    
    }
    
    Function Office-Subscribe-Call{
    try{
    #----------------------------------------------------------------------------------------------------------------------------------------------
    $authHeader = $global:authResultARM.CreateAuthorizationHeader()
    $SubscriptionId   =  $Subscription[0].Subscription.Id
    $OfficeAPIUrl = $ARMResource + 'subscriptions/' + $SubscriptionId + '/resourceGroups/' + $ResourceGroupName + '/providers/Microsoft.OperationalInsights/workspaces/' + $WorkspaceName + '/datasources/office365datasources_' + $SubscriptionId + $OfficeTennantId + '?api-version=2015-11-01-preview'
    
    $OfficeBodyString = "{
                    'properties': {
                                    'AuthProvider':'Office365',
                                    'office365TenantID': '" + $OfficeTennantId + "',
                                    'connectionID': 'office365connection_" + $SubscriptionId + $OfficeTennantId + "',
                                    'office365AdminUsername': '" + $OfficeUsername + "',
                                    'contentTypes':'Audit.Exchange,Audit.AzureActiveDirectory,Audit.SharePoint'
                                  },
                    'etag': '*',
                    'kind': 'Office365',
                    'solution': 'Office365',
                   }"
    
    $Officeparams = @{
        ContentType = 'application/json'
        Headers = @{
        'Authorization'="$($authHeader)"
        'x-ms-client-tenant-id'=$xms_client_tenant_Id
        'Content-Type' = 'application/json'
        }
        Body = $OfficeBodyString
        Method = 'Put'
        URI = $OfficeAPIUrl
      }
    
    $officeresponse = Invoke-WebRequest @Officeparams 
    $officeresponse
    }
    catch{ Failure }
    }
    
    #GetDetails 
    RESTAPI-Auth -ErrorAction Stop
    Connection-API -ErrorAction Stop
    Office-Subscribe-Call -ErrorAction Stop
    ```

2. Führen Sie das Skript mit dem folgenden Befehl aus:

    ```
    .\office365_subscription.ps1 -WorkspaceName <Log Analytics workspace name> -ResourceGroupName <Resource Group name> -SubscriptionId <Subscription ID> -OfficeUsername <OfficeUsername> -OfficeTennantID <Tenant ID> -OfficeClientId <Client ID> -OfficeClientSecret <Client secret>
    ```

    Beispiel:

    ```powershell
    .\office365_subscription.ps1 -WorkspaceName MyWorkspace -ResourceGroupName MyResourceGroup -SubscriptionId '60b79d74-f4e4-4867-b631-yyyyyyyyyyyy' -OfficeUsername 'admin@contoso.com' -OfficeTennantID 'ce4464f8-a172-4dcf-b675-xxxxxxxxxxxx' -OfficeClientId 'f8f14c50-5438-4c51-8956-zzzzzzzzzzzz' -OfficeClientSecret 'y5Lrwthu6n5QgLOWlqhvKqtVUZXX0exrA2KRHmtHgQb='
    ```

### <a name="troubleshooting"></a>Problembehandlung

Möglicherweise wird die folgende Fehlermeldung angezeigt, wenn Ihre Anwendung bereits für diesen Arbeitsbereich abonniert ist, oder wenn dieser Mandant ein Abonnement bei einem anderen Arbeitsbereich hat.

```Output
Invoke-WebRequest : {"Message":"An error has occurred."}
At C:\Users\v-tanmah\Desktop\ps scripts\office365_subscription.ps1:161 char:19
+ $officeresponse = Invoke-WebRequest @Officeparams
+                   ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : InvalidOperation: (System.Net.HttpWebRequest:HttpWebRequest) [Invoke-WebRequest], WebException
    + FullyQualifiedErrorId : WebCmdletWebResponseException,Microsoft.PowerShell.Commands.InvokeWebRequestCommand 
```

Wenn ungültige Parameterwerte angegeben werden, wird möglicherweise der folgende Fehler angezeigt.

```Output
Select-AzSubscription : Please provide a valid tenant or a valid subscription.
At line:12 char:18
+ ... cription = (Select-AzSubscription -SubscriptionId $($Subscriptio ...
+                 ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzContext], ArgumentException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Profile.SetAzContextCommand

```

## <a name="uninstall"></a>Deinstallieren

Sie können die Office 365-Verwaltungslösung mithilfe des in [Entfernen einer Verwaltungslösung](solutions.md#remove-a-monitoring-solution) beschriebenen Prozesses entfernen. Dadurch wird das Sammeln von Daten aus Office 365 in Azure Monitor jedoch nicht beendet. Gehen Sie wie folgt vor, um das Abonnement von Office 365 zu kündigen und die Datensammlung zu beenden.

1. Speichern Sie das folgende Skript als *office365_unsubscribe.ps1*.

    ```powershell
    param (
        [Parameter(Mandatory=$True)][string]$WorkspaceName,
        [Parameter(Mandatory=$True)][string]$ResourceGroupName,
        [Parameter(Mandatory=$True)][string]$SubscriptionId,
        [Parameter(Mandatory=$True)][string]$OfficeTennantId
    )
    $line='#-------------------------------------------------------------------------------------------------------------------------------------------------------------------------'
    
    $line
    IF ($Subscription -eq $null)
        {Login-AzAccount -ErrorAction Stop}
    $Subscription = (Select-AzSubscription -SubscriptionId $($SubscriptionId) -ErrorAction Stop)
    $Subscription
    $option = [System.StringSplitOptions]::RemoveEmptyEntries 
    $Workspace = (Set-AzOperationalInsightsWorkspace -Name $($WorkspaceName) -ResourceGroupName $($ResourceGroupName) -ErrorAction Stop)
    $Workspace
    $WorkspaceLocation= $Workspace.Location
    
    # Client ID for Azure PowerShell
    $clientId = "1950a258-227b-4e31-a9cf-717495945fc2"
    # Set redirect URI for Azure PowerShell
    $redirectUri = "urn:ietf:wg:oauth:2.0:oob"
    $domain='login.microsoftonline.com'
    $adTenant =  $Subscription[0].Tenant.Id
    $authority = "https://login.windows.net/$adTenant";
    $ARMResource ="https://management.azure.com/";
    $xms_client_tenant_Id ='55b65fb5-b825-43b5-8972-c8b6875867c1'
    
    switch ($WorkspaceLocation) {
           "USGov Virginia" { 
                             $domain='login.microsoftonline.us';
                              $authority = "https://login.microsoftonline.us/$adTenant";
                              $ARMResource ="https://management.usgovcloudapi.net/"; break} # US Gov Virginia
           default {
                    $domain='login.microsoftonline.com'; 
                    $authority = "https://login.windows.net/$adTenant";
                    $ARMResource ="https://management.azure.com/";break} 
                    }
    
    Function RESTAPI-Auth { 
    
    $global:SubscriptionID = $Subscription.SubscriptionId
    # Set Resource URI to Azure Service Management API
    $resourceAppIdURIARM=$ARMResource;
    # Authenticate and Acquire Token 
    # Create Authentication Context tied to Azure AD Tenant
    $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList $authority
    # Acquire token
    $global:authResultARM = $authContext.AcquireToken($resourceAppIdURIARM, $clientId, $redirectUri, "Auto")
    $authHeader = $global:authResultARM.CreateAuthorizationHeader()
    $authHeader
    }
    
    Function Office-UnSubscribe-Call{
    
    #----------------------------------------------------------------------------------------------------------------------------------------------
    $authHeader = $global:authResultARM.CreateAuthorizationHeader()
    $ResourceName = "https://manage.office.com"
    $SubscriptionId   = $Subscription[0].Subscription.Id
    $OfficeAPIUrl = $ARMResource + 'subscriptions/' + $SubscriptionId + '/resourceGroups/' + $ResourceGroupName + '/providers/Microsoft.OperationalInsights/workspaces/' + $WorkspaceName + '/datasources/office365datasources_'  + $SubscriptionId + $OfficeTennantId + '?api-version=2015-11-01-preview'
    
    $Officeparams = @{
        ContentType = 'application/json'
        Headers = @{
        'Authorization'="$($authHeader)"
        'x-ms-client-tenant-id'=$xms_client_tenant_Id
        'Content-Type' = 'application/json'
        }
        Method = 'Delete'
        URI = $OfficeAPIUrl
      }
    
    $officeresponse = Invoke-WebRequest @Officeparams 
    $officeresponse
    
    }
    
    #GetDetails 
    RESTAPI-Auth -ErrorAction Stop
    Office-UnSubscribe-Call -ErrorAction Stop
    ```

2. Führen Sie das Skript mit dem folgenden Befehl aus:

    ```
    .\office365_unsubscribe.ps1 -WorkspaceName <Log Analytics workspace name> -ResourceGroupName <Resource Group name> -SubscriptionId <Subscription ID> -OfficeTennantID <Tenant ID> 
    ```

    Beispiel:

    ```powershell
    .\office365_unsubscribe.ps1 -WorkspaceName MyWorkspace -ResourceGroupName MyResourceGroup -SubscriptionId '60b79d74-f4e4-4867-b631-yyyyyyyyyyyy' -OfficeTennantID 'ce4464f8-a172-4dcf-b675-xxxxxxxxxxxx'
    ```

## <a name="data-collection"></a>Datensammlung

### <a name="supported-agents"></a>Unterstützte Agents

Die Office 365-Lösung ruft keine Daten von [Log Analytics-Agents](../platform/agent-data-sources.md) ab.  Sie ruft Daten direkt aus Office 365 ab.

### <a name="collection-frequency"></a>Sammlungshäufigkeit

Es kann einige Stunden dauern, bis die Daten gesammelt werden. Sobald die Sammlung begonnen hat, sendet Office 365 immer dann, wenn ein Datensatz erstellt wurde, eine [Webhookbenachrichtigung](https://msdn.microsoft.com/office-365/office-365-management-activity-api-reference#receiving-notifications) mit detaillierten Daten an Azure Monitor. Dieser Datensatz ist innerhalb weniger Minuten nach dem Empfang in Azure Monitor verfügbar.

## <a name="using-the-solution"></a>Verwenden der Lösung

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]

Wenn Sie die Office 365-Lösung dem Log Analytics-Arbeitsbereich hinzufügen, wird Ihr Dashboard um die Kachel **Office 365** erweitert. Auf dieser Kachel werden ein Zahlenwert und eine grafische Darstellung der Anzahl von Computern in Ihrer Umgebung und jeweils die Updatekonformität angezeigt.<br><br>
![Kachel mit der Office 365-Zusammenfassung](media/solution-office-365/tile.png)  

Klicken Sie auf die Kachel **Office 365**, um das Dashboard **Office 365** zu öffnen.

![Office 365-Dashboard](media/solution-office-365/dashboard.png)  

Das Dashboard enthält die Spalten, die in der folgenden Tabelle angegeben sind. In jeder Spalte sind die zehn wichtigsten Warnungen nach ihrer Anzahl aufgeführt. Dies richtet sich jeweils nach den Spaltenkriterien für den angegebenen Bereich und Zeitraum. Sie können eine Protokollsuche durchführen, mit der die gesamte Liste ausgegeben wird, indem Sie unten in der Spalte auf „Alle anzeigen“ oder indem Sie auf die Spaltenüberschrift klicken.

| Column | BESCHREIBUNG |
|:--|:--|
| Vorgänge | Bietet Informationen über die aktiven Benutzer aller Ihrer überwachten Office 365-Abonnements. Sie können auch die Anzahl der Aktivitäten sehen, die im Verlauf aufgetreten sind.
| Exchange | Zeigt die Aufschlüsselung der Exchange Server-Aktivitäten, z.B. Berechtigungen zum Hinzufügen oder Festlegen von Postfächern. |
| SharePoint | Zeigt die wichtigsten Aktivitäten, die Benutzer in SharePoint-Dokumenten ausführen. Wenn Sie auf dieser Kachel einen Drilldown ausführen, zeigt die Seite „Suche“ die Details dieser Aktivitäten, z.B. das Zieldokument und den Speicherort dieser Aktivität. Beispiel: Für ein Ereignis „Auf Datei zugegriffen“ werden das Dokument, auf das zugegriffen wurde, der zugeordnete Kontoname und die IP-Adresse angezeigt. |
| Azure Active Directory | Enthält die wichtigsten Benutzeraktivitäten, z.B. Versuche, das Benutzerkennwort zurückzusetzen, und Anmeldeversuche. Wenn Sie einen Drilldown ausführen, können Sie die Details dieser Aktivitäten wie den Ergebnisstatus anzeigen. Dies ist hilfreich, wenn Sie verdächtige Aktivitäten in Azure Active Directory überwachen möchten. |




## <a name="azure-monitor-log-records"></a>Protokolldatensätze in Azure Monitor

Alle im Log Analytics-Arbeitsbereich in Azure Monitor von der Office 365-Lösung erstellten Datensätze weisen **OfficeActivity** als **Typ** auf.  Die Eigenschaft **OfficeWorkload** bestimmt, zu welchem Office 365-Dienst der Datensatz gehört: Exchange, AzureActiveDirectory, SharePoint oder OneDrive.  Die Eigenschaft **RecordType** gibt den Typ des Vorgangs an.  Die Eigenschaften für jeden Vorgangstyp variieren. Sie sind in den folgenden Tabellen dargestellt.

### <a name="common-properties"></a>Allgemeine Eigenschaften

Die folgenden Eigenschaften gelten für alle Office 365-Datensätze.

| Eigenschaft | Description |
|:--- |:--- |
| Type | *OfficeActivity* |
| ClientIP | Die IP-Adresse des Geräts, das verwendet wurde, als die Aktivität protokolliert wurde. Die IP-Adresse wird im IPv4- oder IPv6-Adressformat angezeigt. |
| OfficeWorkload | Office 365-Dienst, auf den sich der Datensatz bezieht.<br><br>AzureActiveDirectory<br>Exchange<br>SharePoint|
| Vorgang | Der Name der Benutzer- oder Administratoraktivität.  |
| OrganizationId | Die GUID des Office 365-Mandanten Ihrer Organisation. Dieser Wert ist für Ihre Organisation immer gleich, unabhängig vom Office 365-Dienst, in dem er auftritt. |
| RecordType | Typ des ausgeführten Vorgangs. |
| ResultStatus | Gibt an, ob die Aktion (angegeben in der Eigenschaft „Operation“) erfolgreich war oder nicht. Mögliche Werte sind „Succeeded“, „PartiallySucceeded“ oder „Failed“. Bei Exchange-Administratoraktivitäten ist der Wert entweder „True“ oder „False“. |
| UserId | Der Benutzerprinzipalname (User Principal Name, UPN) des Benutzers, der die Aktion ausgeführt hat, die zum Protokollieren des Datensatzes geführt hat, beispielsweise my_name@my_domain_name. Beachten Sie, dass auch Datensätze für die von Systemkonten ausgeführten Aktivitäten (z.B. „SHAREPOINT\system“ oder „NTAUTHORITY\SYSTEM“) enthalten sind. | 
| UserKey | Eine alternative ID für den Benutzer, der in der Eigenschaft „UserId“ identifiziert wird.  Beispiel: Diese Eigenschaft wird für Ereignisse, die von Benutzern in SharePoint, OneDrive for Business und Exchange ausgeführt werden, mit der eindeutigen Passport-ID (PUID) aufgefüllt. Diese Eigenschaft kann für Ereignisse, die in anderen Diensten und Ereignissen von Systemkonten ausgeführt werden, auch den gleichen Wert wie die Eigenschaft „UserID“ angeben.|
| UserType | Der Typ des Benutzers, der den Vorgang ausgeführt hat.<br><br>Administrator<br>Anwendung<br>DcAdmin<br>Regulär<br>Reserved<br>ServicePrincipal<br>System |


### <a name="azure-active-directory-base"></a>Azure Active Directory-Basis

Die folgenden Eigenschaften gelten für alle Azure Active Directory-Datensätze.

| Eigenschaft | BESCHREIBUNG |
|:--- |:--- |
| OfficeWorkload | AzureActiveDirectory |
| RecordType     | AzureActiveDirectory |
| AzureActiveDirectory_EventType | Der Typ des Azure AD-Ereignisses. |
| ExtendedProperties | Die erweiterten Eigenschaften des Azure AD-Ereignisses. |


### <a name="azure-active-directory-account-logon"></a>Azure Active Directory-Kontoanmeldung

Diese Datensätze werden erstellt, wenn ein Active Directory-Benutzer versucht, sich anzumelden.

| Eigenschaft | BESCHREIBUNG |
|:--- |:--- |
| OfficeWorkload | AzureActiveDirectory |
| RecordType     | AzureActiveDirectoryAccountLogon |
| Anwendung | Die Anwendung, die das Kontoanmeldeereignis auslöst, z.B. Office 15. |
| Client | Details zum Clientgerät, Betriebssystem des Geräts und Gerätebrowser, das bzw. der für das Kontoanmeldeereignis verwendet wurde. |
| LoginStatus | Diese Eigenschaft stammt direkt von „OrgIdLogon.LoginStatus“. Die Zuordnung von verschiedenen interessanten Anmeldefehlern könnte durch Warnungsalgorithmen erfolgen. |
| UserDomain | Die Informationen zur Mandantenidentität (Tenant Identity Information, TII). | 


### <a name="azure-active-directory"></a>Azure Active Directory

Diese Datensätze werden erstellt, wenn Änderungen oder Erweiterungen an Azure Active Directory-Objekten vorgenommen werden.

| Eigenschaft | BESCHREIBUNG |
|:--- |:--- |
| OfficeWorkload | AzureActiveDirectory |
| RecordType     | AzureActiveDirectory |
| AADTarget | Der Benutzer, von dem die Aktion (identifiziert durch die Eigenschaft „Operation“) ausgeführt wurde. |
| Akteur | Der Benutzer oder Dienstprinzipal, der die Aktion ausgeführt hat. |
| ActorContextId | Die GUID der Organisation, zu der der Akteur gehört. |
| ActorIpAddress | Der IP-Adresse des Akteurs im IPV4- oder IPV6-Adressformat. |
| InterSystemsId | Die GUID, die die Aktionen für Komponenten im Office 365-Dienst überwacht. |
| IntraSystemId |   Die GUID, die von Azure Active Directory zum Nachverfolgen der Aktion generiert wird. |
| SupportTicketId | Die Kundensupportticket-ID für die Aktion in Vertretungssituationen. |
| TargetContextId | Die GUID der Organisation, zu der der Zielbenutzer gehört. |


### <a name="data-center-security"></a>Sicherheit von Rechenzentren

Diese Datensätze werden aus Überwachungsdaten für die Sicherheit von Rechenzentren erstellt.  

| Eigenschaft | BESCHREIBUNG |
|:--- |:--- |
| EffectiveOrganization | Der Name des Mandanten, der Ziel der Erhöhung der Rechte/des Cmdlets war. |
| ElevationApprovedTime | Der Zeitstempel des Zeitpunkts, als die Erhöhung der Rechte genehmigt wurde. |
| ElevationApprover | Der Name eines Microsoft-Managers. |
| ElevationDuration | Die Dauer, für die die Erhöhung der Rechte aktiv war. |
| ElevationRequestId |  Ein eindeutiger Bezeichner für die Anforderung zur Erhöhung der Rechte. |
| ElevationRole | Die Rolle, für die die Erhöhung der Rechte angefordert wurde. |
| ElevationTime | Die Startzeit für die Erhöhung der Rechte. |
| Start_Time | Die Startzeit für die Cmdlet-Ausführung. |


### <a name="exchange-admin"></a>Exchange-Verwaltung

Diese Datensätze werden erstellt, wenn Änderungen an der Exchange-Konfiguration vorgenommen werden.

| Eigenschaft | BESCHREIBUNG |
|:--- |:--- |
| OfficeWorkload | Exchange |
| RecordType     | ExchangeAdmin |
| ExternalAccess |  Gibt an, ob das Cmdlet von einem Benutzer in Ihrer Organisation, von Mitarbeitern eines Microsoft-Rechenzentrums oder vom Dienstkonto eines Rechenzentrums oder von einem delegierten Administrator ausgeführt wurde. Der Wert „False“ gibt an, dass das Cmdlet von einem Benutzer in Ihrer Organisation ausgeführt wurde. Der Wert „True“ gibt an, dass das Cmdlet von Mitarbeitern eines Rechenzentrums, einem Dienstkonto eines Rechenzentrums oder einem delegierten Administrator ausgeführt wurde. |
| ModifiedObjectResolvedName |  Dies ist der benutzerfreundliche Name des Objekts, das vom Cmdlet geändert wurde. Dies wird nur protokolliert, wenn das Cmdlet das Objekt ändert. |
| OrganizationName | Der Name des Mandanten. |
| OriginatingServer | Der Name des Servers, über den das Cmdlet ausgeführt wurde. |
| Parameter | Der Name und der Wert für alle Parameter, die mit dem Cmdlet verwendet wurden, das in der Eigenschaft „Operations“ identifiziert wird. |


### <a name="exchange-mailbox"></a>Exchange-Postfach

Diese Datensätze werden erstellt, wenn Änderungen oder Erweiterungen an Exchange-Postfächern vorgenommen werden.

| Eigenschaft | BESCHREIBUNG |
|:--- |:--- |
| OfficeWorkload | Exchange |
| RecordType     | ExchangeItem |
| ClientInfoString | Informationen zum E-Mail-Client, der zum Ausführen des Vorgangs verwendet wurde, z.B. eine Browserversion, eine Outlook-Version und Informationen zu mobilen Geräten. |
| Client_IPAddress | Die IP-Adresse des Geräts, das verwendet wurde, als der Vorgang protokolliert wurde. Die IP-Adresse wird im IPv4- oder IPv6-Adressformat angezeigt. |
| ClientMachineName | Der Computername, der den Outlook-Client hostet. |
| ClientProcessName | Der E-Mail-Client, der verwendet wurde, um auf das Postfach zuzugreifen. |
| ClientVersion | Die Version des E-Mail-Clients. |
| InternalLogonType | Für die interne Verwendung reserviert. |
| Logon_Type | Gibt den Typ des Benutzers an, der auf das Postfach zugegriffen und den Vorgang ausgeführt hat, der protokolliert wurde. |
| LogonUserDisplayName |    Der benutzerfreundliche Name des Benutzers, der den Vorgang ausgeführt hat. |
| LogonUserSid | Die SID des Benutzers, der den Vorgang ausgeführt hat. |
| MailboxGuid | Die Exchange-GUID des Postfachs, auf das zugegriffen wurde. |
| MailboxOwnerMasterAccountSid | Die Hauptkonto-SID des Kontos des Postfachbesitzers. |
| MailboxOwnerSid | Die SID des Postfachbesitzers. |
| MailboxOwnerUPN | Die E-Mail-Adresse der Person, die das Postfach besitzt, auf das zugegriffen wurde. |


### <a name="exchange-mailbox-audit"></a>Exchange-Postfachüberwachung

Diese Datensätze werden erstellt, wenn ein Eintrag zur Postfachüberwachung erstellt wird.

| Eigenschaft | BESCHREIBUNG |
|:--- |:--- |
| OfficeWorkload | Exchange |
| RecordType     | ExchangeItem |
| Item | Stellt das Element dar, für das der Vorgang ausgeführt wurde. | 
| SendAsUserMailboxGuid | Die Exchange-GUID des Postfachs, auf das zugegriffen wurde, um die E-Mail zu senden. |
| SendAsUserSmtp | Die SMTP-Adresse des Benutzers, dessen Identität angenommen wird. |
| SendonBehalfOfUserMailboxGuid | Die Exchange-GUID des Postfachs, auf das zugegriffen wurde, um die E-Mail im Namen eines anderen Benutzers zu senden. |
| SendOnBehalfOfUserSmtp | Die SMTP-Adresse des Benutzers, in dessen Namen die E-Mail gesendet wird. |


### <a name="exchange-mailbox-audit-group"></a>Exchange-Postfachüberwachung – Gruppen

Diese Datensätze werden erstellt, wenn Änderungen oder Erweiterungen an Exchange-Gruppen vorgenommen werden.

| Eigenschaft | BESCHREIBUNG |
|:--- |:--- |
| OfficeWorkload | Exchange |
| OfficeWorkload | ExchangeItemGroup |
| AffectedItems | Informationen über alle Elemente in der Gruppe. |
| CrossMailboxOperations | Gibt an, ob mehr als ein Postfach am Vorgang beteiligt war. |
| DestMailboxId | Wird nur festgelegt, wenn der Parameter „CrossMailboxOperations“ auf „True“ festgelegt ist. Gibt die Zielpostfach-GUID an. |
| DestMailboxOwnerMasterAccountSid | Wird nur festgelegt, wenn der Parameter „CrossMailboxOperations“ auf „True“ festgelegt ist. Gibt die SID für die Hauptkonto-SID des Ziel-Postfachbesitzers an. |
| DestMailboxOwnerSid | Wird nur festgelegt, wenn der Parameter „CrossMailboxOperations“ auf „True“ festgelegt ist. Gibt die SID des Zielpostfachs an. |
| DestMailboxOwnerUPN | Wird nur festgelegt, wenn der Parameter „CrossMailboxOperations“ auf „True“ festgelegt ist. Gibt den UPN des Besitzers des Zielpostfachs an. |
| DestFolder | Der Zielordner für Vorgänge wie „Verschieben“. |
| Ordner | Der Ordner, in dem sich eine Gruppe von Elementen befindet. |
| Ordner |     Informationen zu den Quellordnern, die an einem Vorgang beteiligt waren, z.B. ob Ordner ausgewählt und dann gelöscht wurden. |


### <a name="sharepoint-base"></a>SharePoint-Basis

Diese Eigenschaften gelten für alle SharePoint-Datensätze.

| Eigenschaft | BESCHREIBUNG |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePoint |
| EventSource | Gibt an, dass in SharePoint ein Ereignis aufgetreten ist. Mögliche Werte sind „SharePoint“ oder „ObjectModel“. |
| ItemType | Der Typ des Objekts, auf das zugegriffen wurde oder das geändert wurde. Details zu den Typen von Objekten finden Sie in der Tabelle zu „ItemType“. |
| MachineDomainInfo | Informationen zu Gerätesynchronisierungsvorgängen. Diese Informationen werden nur gemeldet, wenn sie in der Anforderung vorhanden sind. |
| MachineId |   Informationen zu Gerätesynchronisierungsvorgängen. Diese Informationen werden nur gemeldet, wenn sie in der Anforderung vorhanden sind. |
| Site_ | Die GUID der Website, in der sich die Datei oder der Ordner befindet, auf die bzw. den der Benutzer zugegriffen hat. |
| Source_Name | Die Entität, die den überwachten Vorgang ausgelöst hat. Mögliche Werte sind „SharePoint“ oder „ObjectModel“. |
| UserAgent | Informationen zum Client oder Browser des Benutzers. Diese Informationen werden vom Client oder Browser bereitgestellt. |


### <a name="sharepoint-schema"></a>SharePoint-Schema

Diese Datensätze werden erstellt, wenn Konfigurationsänderungen an SharePoint vorgenommen werden.

| Eigenschaft | BESCHREIBUNG |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePoint |
| CustomEvent | Optionale Zeichenfolge für benutzerdefinierte Ereignisse. |
| Event_Data |  Optionale Nutzlast für benutzerdefinierte Ereignisse. |
| ModifiedProperties | Die Eigenschaft bezieht sich auf Verwaltungsereignisse, z.B. das Hinzufügen eines Benutzers als Mitglied einer Website oder der Administratorgruppe einer Websitesammlung. Die Eigenschaft enthält den Namen der Eigenschaft, die geändert wurde (z.B. die Gruppe der Websiteadministratoren), den neuen Wert der geänderten Eigenschaft (z.B. den Benutzer, der als Websiteadministrator hinzugefügt wurde) und den vorherigen Wert des geänderten Objekts. |


### <a name="sharepoint-file-operations"></a>SharePoint-Dateivorgänge

Diese Datensätze werden als Reaktion auf Dateivorgänge in SharePoint erstellt.

| Eigenschaft | BESCHREIBUNG |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePointFileOperation |
| DestinationFileExtension | Die Dateierweiterung einer Datei, die kopiert oder verschoben wird. Diese Eigenschaft wird nur für die Ereignisse „FileCopied“ und „FileMoved“ angezeigt. |
| DestinationFileName | Der Name der Datei, die kopiert oder verschoben wird. Diese Eigenschaft wird nur für die Ereignisse „FileCopied“ und „FileMoved“ angezeigt. |
| DestinationRelativeUrl | Die URL des Zielordners, in den eine Datei kopiert oder verschoben wird. Die Kombination der Werte für die Parameter „SiteURL“, „DestinationRelativeURL“ und „DestinationFileName“ ist identisch mit dem Wert für die Eigenschaft „ObjectID“, die den vollständigen Pfadnamen für die kopierte Datei darstellt. Diese Eigenschaft wird nur für die Ereignisse „FileCopied“ und „FileMoved“ angezeigt. |
| SharingType | Der Typ der Freigabeberechtigungen, die dem Benutzer zugewiesen wurden, für den die Ressource freigegeben wurde. Dieser Benutzer wird durch den Parameter „UserSharedWith“ identifiziert. |
| Site_Url | Die URL der Website, in der sich die Datei oder der Ordner befindet, auf die bzw. den der Benutzer zugegriffen hat. |
| SourceFileExtension | Die Dateierweiterung der Datei, auf die der Benutzer zugegriffen hat. Diese Eigenschaft ist leer, wenn das Objekt, auf das zugegriffen wurde, ein Ordner ist. |
| SourceFileName |  Der Name der Datei oder des Ordners, auf die bzw. den der Benutzer zugegriffen hat. |
| SourceRelativeUrl | Die URL des Ordners, der die Datei enthält, auf die der Benutzer zugegriffen hat. Die Kombination der Werte für die Parameter „SiteURL“, „SourceRelativeURL“ und „SourceFileName“ ist identisch mit dem Wert für die Eigenschaft „ObjectID“, die den vollständigen Pfadnamen für die Datei darstellt, auf die der Benutzer zugegriffen hat. |
| UserSharedWith |  Der Benutzer, für den eine Ressource freigegeben wurde. |




## <a name="sample-log-searches"></a>Beispiele für Protokollsuchen

Die folgende Tabelle enthält Beispiele für Protokollsuchen für Updatedatensätze, die mit dieser Lösung erfasst wurden.

| Abfragen | BESCHREIBUNG |
| --- | --- |
|Anzahl aller Vorgänge in Ihrem Office 365-Abonnement |OfficeActivity &#124; summarize count() by Operation |
|Verwendung von SharePoint-Websites|OfficeActivity &#124; where OfficeWorkload =~ "sharepoint" &#124; summarize count() by SiteUrl \| sort by Count asc|
|Dateizugriffsvorgänge nach Benutzertyp|search in (OfficeActivity) OfficeWorkload =~ "azureactivedirectory" and "MyTest"|
|Suche mit einem bestimmten Schlüsselwort|Type=OfficeActivity OfficeWorkload=azureactivedirectory "MyTest"|
|Überwachung externer Aktionen für Exchange|OfficeActivity &#124; where OfficeWorkload =~ "exchange" and ExternalAccess == true|



## <a name="next-steps"></a>Nächste Schritte

* Verwenden Sie [Protokollabfragen in Azure Monitor](../log-query/log-query-overview.md), um ausführliche Aktualisierungsdaten anzuzeigen.
* [Erstellen Sie eigene Dashboards](../learn/tutorial-logs-dashboards.md), um Ihre bevorzugten Office 365-Suchabfragen anzuzeigen.
* [Erstellen Sie Warnungen](../platform/alerts-overview.md), um proaktiv über wichtige Office 365-Aktivitäten benachrichtigt zu werden.  