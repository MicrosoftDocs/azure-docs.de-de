---
title: Office 365-Verwaltungslösung in Azure
description: Dieser Artikel bietet Einzelheiten zur Konfiguration und Verwendung der Office 365-Lösung in Azure.  Er enthält eine ausführliche Beschreibung der Office 365-Datensätze, die in Azure Monitor erstellt werden.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/30/2020
ms.openlocfilehash: eb20bf4164cb2153f6786dbec04f79453554fa25
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95995861"
---
# <a name="office-365-management-solution-in-azure-preview"></a>Office 365-Verwaltungslösung in Azure (Vorschau)

![Office 365-Logo](media/solution-office-365/icon.png)

> [!IMPORTANT]
> ## <a name="solution-update"></a>Lösungsaktualisierung
> Diese Lösung wurde durch die [Office 365](../../sentinel/connect-office-365.md)-Lösung für allgemeine Verfügbarkeit in [Azure Sentinel](../../sentinel/overview.md) und die [Azure AD-Lösung für Berichterstellung und Überwachung](../../active-directory/reports-monitoring/plan-monitoring-and-reporting.md) ersetzt. Gemeinsam bieten diese eine aktualisierte Version der bisherigen Office 365-Lösung in Azure Monitor mit einer verbesserten Konfigurationsumgebung. Die vorhandene Lösung kann bis zum 31. Oktober 2020 weiterhin verwendet werden.
> 
> Azure Sentinel ist eine cloudnative SIEM-Lösung (Security Information & Event Management), die Protokolle erfasst und zusätzliche SIEM-Funktionalität bereitstellt, einschließlich Erkennungen, Untersuchungen, Hunting und Einblicke auf Grundlage von Machine Learning. Mit Azure Sentinel können Sie nun Office 365 SharePoint-Aktivitäts- und Exchange-Verwaltungsprotokolle erfassen.
> 
> Die Azure AD-Berichterstellung bietet eine umfassendere Ansicht von Protokollen aus Azure AD-Aktivitäten in Ihrer Umgebung, die unter anderem Anmeldeereignisse, Überwachungsereignisse und Änderungen an Ihrem Verzeichnis enthalten. Zum Herstellen einer Verbindung mit Azure AD-Protokollen können Sie entweder den [Azure AD-Connector von Azure Sentinel](../../sentinel/connect-azure-active-directory.md) verwenden oder die [Integration von Azure AD-Protokollen in Azure Monitor](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) konfigurieren. 
>
> Die Erfassung von Azure AD-Protokollen unterliegt den Azure Monitor-Tarifen.  Weitere Informationen finden Sie unter [Azure Monitor – Preise](https://azure.microsoft.com/pricing/details/monitor/).
>
> So verwenden Sie die Office 365-Lösung in Azure Sentinel:
> 1. Die Verwendung des Office 365-Connectors in Azure Sentinel wirkt sich auf die Preise für Ihren Arbeitsbereich aus. Weitere Informationen finden Sie unter [Azure Sentinel – Preise](https://azure.microsoft.com/pricing/details/azure-sentinel/).
> 2. Wenn Sie bereits die Office 365-Lösung in Azure Monitor verwenden, müssen Sie diese zunächst mithilfe des Skripts im [Abschnitt „Deinstallieren“ unten](#uninstall) deinstallieren.
> 3. [Aktivieren Sie die Azure Sentinel-Lösung](../../sentinel/quickstart-onboard.md) in Ihrem Arbeitsbereich.
> 4. Navigieren Sie in Azure Sentinel zur Seite **Datenconnectors**, und aktivieren Sie den **Office 365**-Connector.
>
> ## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen
> 
> ### <a name="q-is-it-possible-to-on-board-the-office-365-azure-monitor-solution-between-now-and-october-31"></a>F: Ist es möglich, ein Onboarding der Office 365-Lösung in Azure Monitor vom jetzigen Zeitpunkt bis zum 31. Oktober durchzuführen?
> Nein, die Onboardingskripts für die Office 365-Lösung in Azure Monitor sind nicht mehr verfügbar. Die Lösung wird am 31. Oktober entfernt.
> 
> ### <a name="q-will-the-tables-and-schemas-be-changed"></a>F: Werden die Tabellen und Schemas geändert?
> Der Name und das Schema der Tabelle **OfficeActivity** bleiben gegenüber der aktuellen Lösung unverändert. Sie können in der neuen Lösung weiterhin die gleichen Abfragen verwenden, mit Ausnahme von Abfragen, die auf Azure AD-Daten verweisen.
> 
> Die neuen Protokolle der [Azure AD-Lösung für Berichterstellung und Überwachung](../../active-directory/reports-monitoring/plan-monitoring-and-reporting.md) werden in den Tabellen [SigninLogs](../../active-directory/reports-monitoring/concept-sign-ins.md) and [AuditLogs](../../active-directory/reports-monitoring/concept-audit-logs.md) anstelle der Tabelle **OfficeActivity** erfasst. Weitere Informationen finden Sie unter [Analysieren von Azure AD-Protokollen](../../active-directory/reports-monitoring/howto-analyze-activity-logs-log-analytics.md). Dieses Thema betrifft auch Azure Sentinel- und Azure Monitor-Benutzer.
> 
> Es folgen Beispiele für das Konvertieren von Abfragen aus **OfficeActivity** in **SigninLogs**:
> 
> **Abfrage fehlerhafter Anmeldungen nach Benutzer:**
> 
> ```Kusto
> OfficeActivity
> | where TimeGenerated >= ago(1d) 
> | where OfficeWorkload == "AzureActiveDirectory"                      
> | where Operation == 'UserLoginFailed'
> | summarize count() by UserId    
> ```
> 
> ```Kusto
> SigninLogs
> | where ConditionalAccessStatus == "failure" or ConditionalAccessStatus == "notApplied"
> | summarize count() by UserDisplayName
> ```
> 
> **Anzeigen von Azure AD-Vorgängen:**
> 
> ```Kusto
> OfficeActivity
> | where OfficeWorkload =~ "AzureActiveDirectory"
> | sort by TimeGenerated desc
> | summarize AggregatedValue = count() by Operation
> ```
> 
> ```Kusto
> AuditLogs
> | summarize count() by OperationName
> ```
> 
> ### <a name="q-how-can-i-on-board-azure-sentinel"></a>F: Wie kann ich ein Onboarding für Azure Sentinel ausführen?
> Azure Sentinel ist eine Lösung, die Sie in einem neuen oder einem vorhandenen Log Analytics-Arbeitsbereich aktivieren können. Weitere Informationen finden Sie in der [Dokumentation zum Onboarding für Azure Sentinel](../../sentinel/quickstart-onboard.md).
>
> ### <a name="q-do-i-need-azure-sentinel-to-connect-the-azure-ad-logs"></a>F: Brauche ich Azure Sentinel zum Herstellen einer Verbindung mit den Azure AD-Protokollen?
> Sie können die [Integration von Azure AD-Protokollen in Azure Monitor](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) konfigurieren. Dies steht nicht mit der Azure Sentinel-Lösung in Zusammenhang. Azure Sentinel bietet einen nativen Connector und standardmäßig verfügbare Inhalte für Azure AD-Protokolle. Weitere Informationen finden Sie unter der Frage zu standardmäßig verfügbaren, sicherheitsorientierten Inhalten weiter unten.
>
> ###    <a name="q-what-are-the-differences-when-connecting-azure-ad-logs-from-azure-sentinel-and-azure-monitor"></a>F: Was sind die Unterschiede beim Herstellen einer Verbindung mit Azure AD-Protokollen über Azure Sentinel und Azure Monitor?
> Azure Sentinel und Azure Monitor stellen eine Verbindung mit Azure AD-Protokollen auf Grundlage derselben [Azure AD-Lösung für Berichterstellung und Überwachung](../../active-directory/reports-monitoring/plan-monitoring-and-reporting.md) her. Azure Sentinel bietet einen nativen 1-Klick-Connector, der eine Verbindung mit denselben Daten herstellt und Überwachungsinformationen bereitstellt.
>
> ###    <a name="q-what-do-i-need-to-change-when-moving-to-the-new-azure-ad-reporting-and-monitoring-tables"></a>F: Was muss ich beim Umstieg auf die neuen Azure AD-Tabellen für Berichterstellung und Überwachung ändern?
> Alle Abfragen, die Azure AD-Daten verwenden, einschließlich Abfragen in Warnungen, Dashboards und Inhalten, die Sie mithilfe von Office 365 Azure AD-Daten erstellt haben, müssen mit den neuen Tabellen neu erstellt werden.
>
> Azure Sentinel und Azure AD bieten integrierte Inhalte, die Sie beim Wechsel zur Azure AD-Lösung für Berichterstellung und Überwachung verwenden können. Weitere Informationen finden Sie unter der nächsten Frage zu standardmäßig verfügbaren, sicherheitsorientierten Inhalten und unter [Verwenden von Azure Monitor-Arbeitsmappen für Azure Active Directory-Berichte](../../active-directory/reports-monitoring/howto-use-azure-monitor-workbooks.md). 
>
> ### <a name="q-how-i-can-use-the-azure-sentinel-out-of-the-box-security-oriented-content"></a>F: Wie kann ich die standardmäßig verfügbaren, sicherheitsorientierten Inhalte in Azure Sentinel verwenden?
> Azure Sentinel bietet standardmäßig verfügbare, sicherheitsorientierte Dashboards, benutzerdefinierte Warnungsabfragen, Hunting-Abfragen, Untersuchungen und Funktionen für automatisierte Antworten, die auf den Office 365- und Azure AD-Protokollen basieren. Weitere Informationen finden Sie auf dem Azure Sentinel-GitHub und in den entsprechenden Tutorials:
>
> - [Standardmäßig verfügbare Erkennung von Bedrohungen](../../sentinel/tutorial-detect-threats-built-in.md)
> - [Erstellen benutzerdefinierter Analyseregeln zum Erkennen von verdächtigen Bedrohungen](../../sentinel/tutorial-detect-threats-custom.md)
> - [Überwachen Ihrer Daten](../../sentinel/tutorial-monitor-your-data.md)
> - Lesen Sie [Untersuchen von Incidents mit Azure Sentinel](../../sentinel/tutorial-investigate-cases.md).
> - Machen Sie sich mit dem [Einrichten automatisierter Reaktionen auf Bedrohungen in Azure Sentinel](../../sentinel/tutorial-respond-threats-playbook.md) vertraut.
> - [Azure Sentinel-GitHub-Community](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks)
> 
> ### <a name="q-does-azure-sentinel-provide-additional-connectors-as-part-of-the-solution"></a>F: Stellt Azure Sentinel zusätzliche Connectors als Teil der Lösung bereit?
> Ja. Informationen dazu finden Sie unter [Herstellen einer Verbindung mit Datenquellen in Azure Sentinel](../../sentinel/connect-data-sources.md).
> 
> ###    <a name="q-what-will-happen-on-october-31-do-i-need-to-offboard-beforehand"></a>F: Was geschieht am 31. Oktober? Muss ich vorab ein Offboarding durchführen?
> 
> - Sie können keine Daten mehr von der **Office 365**-Lösung empfangen. Die Lösung wird aus dem Arbeitsbereich entfernt und ist im Marketplace nicht mehr verfügbar.
> - Für Azure Sentinel-Kunden wird die Log Analytics-Arbeitsbereichslösung **Office365** in die Azure Sentinel-Lösung **SecurityInsights** eingeschlossen.
> - Wenn Sie bis zum 31. Oktober kein manuelles Offboarding für die Lösung durchführen, werden Ihre Daten automatisch getrennt, und die Tabelle **OfficeActivity** wird entfernt. Sie können die Tabelle dennoch wiederherstellen, wenn Sie den Office 365-Connector in Azure Sentinel wie unten erläutert aktivieren.
> 
> ### <a name="q-will-my-data-transfer-to-the-new-solution"></a>F: Werden meine Daten in die neue Lösung übertragen?
> Ja. Wenn Sie die **Office 365**-Lösung aus Ihrem Arbeitsbereich entfernen, sind die Daten vorübergehend nicht verfügbar, da das Schema entfernt wird. Sobald Sie den neuen **Office 365**-Connector in Sentinel aktivieren, wird das Schema im Arbeitsbereich wiederhergestellt, und alle bereits erfassten Daten sind wieder verfügbar. 
 

Mit der Office 365-Verwaltungslösung können Sie Ihre Office 365-Umgebung in Azure Monitor überwachen.

- Überwachen Sie Benutzeraktivitäten mit Ihren Office 365-Konten, um die Verwendungsmuster zu analysieren und Trends beim Verhalten zu identifizieren. Beispielsweise können Sie spezielle Verwendungsszenarien extrahieren, wie z.B. Dateien, die außerhalb Ihrer Organisation freigegeben werden, oder die am häufigsten verwendeten SharePoint-Websites.
- Überwachen Sie Administratoraktivitäten, um Konfigurationsänderungen oder Vorgänge mit erhöhten Rechten nachzuverfolgen.
- Ermitteln und untersuchen Sie unerwünschtes Benutzerverhalten. Dies kann an die Anforderungen Ihrer Organisation angepasst werden.
- Demonstrieren Sie Überwachung und Compliance. Beispielsweise können Sie Dateizugriffe auf vertrauliche Dateien überwachen und so den Überwachungs- und Complianceprozess unterstützen.
- Führen Sie eine operative Problembehandlung mithilfe der [Protokollabfragen](../log-query/log-query-overview.md) in den Office 365-Aktivitätsdaten Ihrer Organisation durch.


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
    $platformParameters = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.PlatformParameters" -ArgumentList "Auto"
    $global:authResultARM = $authContext.AcquireTokenAsync($resourceAppIdURIARM, $clientId, $redirectUri, $platformParameters)
    $global:authResultARM.Wait()
    $authHeader = $global:authResultARM.Result.CreateAuthorizationHeader()
    $authHeader
    }
    
    Function Office-UnSubscribe-Call{
    
    #----------------------------------------------------------------------------------------------------------------------------------------------
    $authHeader = $global:authResultARM.Result.CreateAuthorizationHeader()
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

Sie werden zur Eingabe Ihrer Anmeldeinformationen aufgefordert. Geben Sie die Anmeldeinformationen für den Log Analytics-Arbeitsbereich an.

## <a name="data-collection"></a>Datensammlung

Es kann einige Stunden dauern, bis die Daten gesammelt werden. Sobald die Sammlung begonnen hat, sendet Office 365 immer dann, wenn ein Datensatz erstellt wurde, eine [Webhookbenachrichtigung](/office/office-365-management-api/office-365-management-activity-api-reference#receiving-notifications) mit detaillierten Daten an Azure Monitor. Dieser Datensatz ist innerhalb weniger Minuten nach dem Empfang in Azure Monitor verfügbar.

## <a name="using-the-solution"></a>Verwenden der Lösung

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]

Wenn Sie die Office 365-Lösung dem Log Analytics-Arbeitsbereich hinzufügen, wird Ihr Dashboard um die Kachel **Office 365** erweitert. Auf dieser Kachel werden ein Zahlenwert und eine grafische Darstellung der Anzahl von Computern in Ihrer Umgebung und jeweils die Updatekonformität angezeigt.<br><br>
![Kachel mit der Office 365-Zusammenfassung](media/solution-office-365/tile.png)  

Klicken Sie auf die Kachel **Office 365**, um das Dashboard **Office 365** zu öffnen.

![Office 365-Dashboard](media/solution-office-365/dashboard.png)  

Das Dashboard enthält die Spalten, die in der folgenden Tabelle angegeben sind. In jeder Spalte sind die zehn wichtigsten Warnungen nach ihrer Anzahl aufgeführt. Dies richtet sich jeweils nach den Spaltenkriterien für den angegebenen Bereich und Zeitraum. Sie können eine Protokollsuche durchführen, mit der die gesamte Liste ausgegeben wird, indem Sie unten in der Spalte auf „Alle anzeigen“ oder indem Sie auf die Spaltenüberschrift klicken.

| Column | BESCHREIBUNG |
|:--|:--|
| Operationen (Operations) | Bietet Informationen über die aktiven Benutzer aller Ihrer überwachten Office 365-Abonnements. Sie können auch die Anzahl der Aktivitäten sehen, die im Verlauf aufgetreten sind.
| Exchange | Zeigt die Aufschlüsselung der Exchange Server-Aktivitäten, z.B. Berechtigungen zum Hinzufügen oder Festlegen von Postfächern. |
| SharePoint | Zeigt die wichtigsten Aktivitäten, die Benutzer in SharePoint-Dokumenten ausführen. Wenn Sie auf dieser Kachel einen Drilldown ausführen, zeigt die Seite „Suche“ die Details dieser Aktivitäten, z.B. das Zieldokument und den Speicherort dieser Aktivität. Beispiel: Für ein Ereignis „Auf Datei zugegriffen“ werden das Dokument, auf das zugegriffen wurde, der zugeordnete Kontoname und die IP-Adresse angezeigt. |
| Azure Active Directory | Enthält die wichtigsten Benutzeraktivitäten, z.B. Versuche, das Benutzerkennwort zurückzusetzen, und Anmeldeversuche. Wenn Sie einen Drilldown ausführen, können Sie die Details dieser Aktivitäten wie den Ergebnisstatus anzeigen. Dies ist hilfreich, wenn Sie verdächtige Aktivitäten in Azure Active Directory überwachen möchten. |




## <a name="azure-monitor-log-records"></a>Protokolldatensätze in Azure Monitor

Alle im Log Analytics-Arbeitsbereich in Azure Monitor von der Office 365-Lösung erstellten Datensätze weisen **OfficeActivity** als **Typ** auf.  Die Eigenschaft **OfficeWorkload** bestimmt, zu welchem Office 365-Dienst der Datensatz gehört: Exchange, AzureActiveDirectory, SharePoint oder OneDrive.  Die Eigenschaft **RecordType** gibt den Typ des Vorgangs an.  Die Eigenschaften für jeden Vorgangstyp variieren. Sie sind in den folgenden Tabellen dargestellt.

### <a name="common-properties"></a>Allgemeine Eigenschaften

Die folgenden Eigenschaften gelten für alle Office 365-Datensätze.

| Eigenschaft | BESCHREIBUNG |
|:--- |:--- |
| type | *OfficeActivity* |
| ClientIP | Die IP-Adresse des Geräts, das verwendet wurde, als die Aktivität protokolliert wurde. Die IP-Adresse wird im IPv4- oder IPv6-Adressformat angezeigt. |
| OfficeWorkload | Office 365-Dienst, auf den sich der Datensatz bezieht.<br><br>AzureActiveDirectory<br>Exchange<br>SharePoint|
| Vorgang | Der Name der Benutzer- oder Administratoraktivität.  |
| OrganizationId | Die GUID des Office 365-Mandanten Ihrer Organisation. Dieser Wert ist für Ihre Organisation immer gleich, unabhängig vom Office 365-Dienst, in dem er auftritt. |
| RecordType | Typ des ausgeführten Vorgangs. |
| ResultStatus | Gibt an, ob die Aktion (angegeben in der Eigenschaft „Operation“) erfolgreich war oder nicht. Mögliche Werte sind „Succeeded“, „PartiallySucceeded“ oder „Failed“. Bei Exchange-Administratoraktivitäten ist der Wert entweder „True“ oder „False“. |
| UserId | Der Benutzerprinzipalname (User Principal Name, UPN) des Benutzers, der die Aktion ausgeführt hat, die zum Protokollieren des Datensatzes geführt hat, beispielsweise my_name@my_domain_name. Beachten Sie, dass auch Datensätze für die von Systemkonten ausgeführten Aktivitäten (z.B. „SHAREPOINT\system“ oder „NTAUTHORITY\SYSTEM“) enthalten sind. | 
| UserKey | Eine alternative ID für den Benutzer, der in der Eigenschaft „UserId“ identifiziert wird.  Beispiel: Diese Eigenschaft wird für Ereignisse, die von Benutzern in SharePoint, OneDrive for Business und Exchange ausgeführt werden, mit der eindeutigen Passport-ID (PUID) aufgefüllt. Diese Eigenschaft kann für Ereignisse, die in anderen Diensten und Ereignissen von Systemkonten ausgeführt werden, auch den gleichen Wert wie die Eigenschaft „UserID“ angeben.|
| UserType | Der Typ des Benutzers, der den Vorgang ausgeführt hat.<br><br>Admin<br>Application<br>DcAdmin<br>Regulär<br>Reserved<br>ServicePrincipal<br>System |


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
| `OfficeWorkload` | AzureActiveDirectory |
| `RecordType`     | AzureActiveDirectoryAccountLogon |
| `Application` | Die Anwendung, die das Kontoanmeldeereignis auslöst, z.B. Office 15. |
| `Client` | Details zum Clientgerät, Betriebssystem des Geräts und Gerätebrowser, das bzw. der für das Kontoanmeldeereignis verwendet wurde. |
| `LoginStatus` | Diese Eigenschaft stammt direkt von „OrgIdLogon.LoginStatus“. Die Zuordnung von verschiedenen interessanten Anmeldefehlern könnte durch Warnungsalgorithmen erfolgen. |
| `UserDomain` | Die Informationen zur Mandantenidentität (Tenant Identity Information, TII). | 


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
| IntraSystemId |     Die GUID, die von Azure Active Directory zum Nachverfolgen der Aktion generiert wird. |
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
| ElevationRequestId |     Ein eindeutiger Bezeichner für die Anforderung zur Erhöhung der Rechte. |
| ElevationRole | Die Rolle, für die die Erhöhung der Rechte angefordert wurde. |
| ElevationTime | Die Startzeit für die Erhöhung der Rechte. |
| Start_Time | Die Startzeit für die Cmdlet-Ausführung. |


### <a name="exchange-admin"></a>Exchange-Verwaltung

Diese Datensätze werden erstellt, wenn Änderungen an der Exchange-Konfiguration vorgenommen werden.

| Eigenschaft | BESCHREIBUNG |
|:--- |:--- |
| OfficeWorkload | Exchange |
| RecordType     | ExchangeAdmin |
| ExternalAccess |     Gibt an, ob das Cmdlet von einem Benutzer in Ihrer Organisation, von Mitarbeitern eines Microsoft-Rechenzentrums oder vom Dienstkonto eines Rechenzentrums oder von einem delegierten Administrator ausgeführt wurde. Der Wert „False“ gibt an, dass das Cmdlet von einem Benutzer in Ihrer Organisation ausgeführt wurde. Der Wert „True“ gibt an, dass das Cmdlet von Mitarbeitern eines Rechenzentrums, einem Dienstkonto eines Rechenzentrums oder einem delegierten Administrator ausgeführt wurde. |
| ModifiedObjectResolvedName |     Dies ist der benutzerfreundliche Name des Objekts, das vom Cmdlet geändert wurde. Dies wird nur protokolliert, wenn das Cmdlet das Objekt ändert. |
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
| LogonUserDisplayName |     Der benutzerfreundliche Name des Benutzers, der den Vorgang ausgeführt hat. |
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
| Element | Stellt das Element dar, für das der Vorgang ausgeführt wurde. | 
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
| MachineId |     Informationen zu Gerätesynchronisierungsvorgängen. Diese Informationen werden nur gemeldet, wenn sie in der Anforderung vorhanden sind. |
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
| Event_Data |     Optionale Nutzlast für benutzerdefinierte Ereignisse. |
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
| SourceFileName |     Der Name der Datei oder des Ordners, auf die bzw. den der Benutzer zugegriffen hat. |
| SourceRelativeUrl | Die URL des Ordners, der die Datei enthält, auf die der Benutzer zugegriffen hat. Die Kombination der Werte für die Parameter „SiteURL“, „SourceRelativeURL“ und „SourceFileName“ ist identisch mit dem Wert für die Eigenschaft „ObjectID“, die den vollständigen Pfadnamen für die Datei darstellt, auf die der Benutzer zugegriffen hat. |
| UserSharedWith |     Der Benutzer, für den eine Ressource freigegeben wurde. |




## <a name="sample-log-queries"></a>Beispielprotokollabfragen

Die folgende Tabelle enthält Beispielprotokollabfragen für Updatedatensätze, die mit dieser Lösung erfasst werden.

| Abfrage | BESCHREIBUNG |
| --- | --- |
|Anzahl aller Vorgänge in Ihrem Office 365-Abonnement |OfficeActivity &#124; summarize count() by Operation |
|Verwendung von SharePoint-Websites|OfficeActivity &#124; where OfficeWorkload =~ "sharepoint" &#124; summarize count() by SiteUrl \| sort by Count asc|
|Dateizugriffsvorgänge nach Benutzertyp | OfficeActivity &#124; summarize count() by UserType |
|Überwachung externer Aktionen für Exchange|OfficeActivity &#124; where OfficeWorkload =~ "exchange" and ExternalAccess == true|



## <a name="next-steps"></a>Nächste Schritte

* Verwenden Sie [Protokollabfragen in Azure Monitor](../log-query/log-query-overview.md), um ausführliche Aktualisierungsdaten anzuzeigen.
* [Erstellen Sie eigene Dashboards](../learn/tutorial-logs-dashboards.md), um Ihre bevorzugten Office 365-Suchabfragen anzuzeigen.
* [Erstellen Sie Warnungen](../platform/alerts-overview.md), um proaktiv über wichtige Office 365-Aktivitäten benachrichtigt zu werden.  
