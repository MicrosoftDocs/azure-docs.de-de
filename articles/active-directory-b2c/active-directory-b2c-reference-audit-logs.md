---
title: Beispiele und Definitionen für Überwachungsprotokolle in Azure Active Directory B2C | Microsoft-Dokumentation
description: Leitfaden und Beispiele für den Zugriff auf die Azure AD B2C-Überwachungsprotokolle
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 08/04/2017
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 27c91185bacea839ec73a3f4bd06f5df43bd4edf
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66509652"
---
# <a name="accessing-azure-ad-b2c-audit-logs"></a>Zugriff auf Active Directory B2C-Überwachungsprotokolle

Azure Active Directory B2C (Azure AD B2C) gibt Überwachungsprotokolle aus, die Aktivitätsinformationen über B2C-Ressourcen, ausgestellte Token und Administratorzugriff enthalten. Dieser Artikel bietet eine kurze Übersicht über die Informationen, die durch Überwachungsprotokolle verfügbar sind, sowie Anweisungen für den Zugriff auf diese Daten für Ihren Active Directory B2C-Mandanten.

> [!IMPORTANT]
> Überwachungsprotokolle werden nur sieben Tage lang aufbewahrt. Planen Sie den Download und die Speicherung Ihrer Protokolle mit einer der unten aufgeführten Methoden, wenn eine längere Aufbewahrungsdauer erforderlich ist.

## <a name="overview-of-activities-available-in-the-b2c-category-of-audit-logs"></a>Übersicht der verfügbaren Aktivitäten in der B2C-Kategorie von Überwachungsprotokollen
Die **B2C**-Kategorie in Überwachungsprotokollen umfasst die folgenden Aktivitätstypen:

|Aktivitätstyp |BESCHREIBUNG  |
|---------|---------|
|Autorisierung |Aktivitäten, die die Autorisierung eines Benutzers für den Zugriff auf B2C-Ressourcen betreffen (z.B. ein Administrator, der auf eine Liste von B2C-Richtlinien zugreift).         |
|Verzeichnis |Aktivitäten im Zusammenhang mit Verzeichnisattributen, die abgerufen werden, wenn sich ein Administrator über das Azure-Portal anmeldet. |
|Anwendung | CRUD-Vorgänge für B2C-Anwendungen |
|Schlüssel |CRUD-Vorgänge für im B2C-Schlüsselcontainer gespeicherte Schlüssel |
|Resource |CRUD-Vorgänge für B2C-Ressourcen (z.B. Richtlinien und Identitätsanbieter)
|Authentication |Überprüfung der Anmeldeinformationen des Benutzers und Tokenausstellung|

> [!NOTE]
> Informationen zu den CRUD-Aktivitäten des Benutzerobjekts finden Sie in der Kategorie **Hauptverzeichnis**.

## <a name="example-activity"></a>Beispielaktivität
Das folgende Beispiel zeigt die erfassten Daten, wenn sich ein Benutzer bei einem externen Identitätsanbieter anmeldet: ![Überwachungsprotokolle – Beispiel](./media/active-directory-b2c-reference-audit-logs/audit-logs-example.png)

## <a name="accessing-audit-logs-through-the-azure-portal"></a>Zugriff auf Überwachungsprotokolle über das Azure-Portal
1. Öffnen Sie das [Azure-Portal](https://portal.azure.com). Stellen Sie sicher, dass Sie sich in Ihrem B2C-Verzeichnis befinden.
2. Klicken Sie links in der Favoritenleiste auf **Azure Active Directory**.
    
    ![Überwachungsprotokolle – AAD-Schaltfläche](./media/active-directory-b2c-reference-audit-logs/audit-logs-portal-aad.png)

1. Klicken Sie unter **Aktivität** auf **Überwachungsprotokolle**.

    ![Überwachungsprotokolle – Abschnitt „Protokolle“](./media/active-directory-b2c-reference-audit-logs/audit-logs-portal-section.png)

2. Wählen Sie im Dropdownfeld **Kategorie** die Option **B2C**.
3. Klicken Sie auf **Anwenden**.

    ![Überwachungsprotokolle – Kategorie](./media/active-directory-b2c-reference-audit-logs/audit-logs-portal-category.png)

Sie sehen eine Liste der Aktivitäten, die in den letzten sieben Tagen protokolliert wurden.
- Verwenden der Dropdownliste **Aktivitätsressourcentyp** zum Filtern nach den oben beschriebenen Aktivitätstypen.
- Verwenden Sie die Dropdownliste **Datumsbereich**, um den Datumsbereich der angezeigten Aktivitäten zu filtern.
- Wenn Sie auf eine bestimmte Zeile in der Liste klicken, zeigt Ihnen ein Kontextfeld auf der rechten Seite zusätzliche Attribute, die mit der Aktivität verbunden sind.
- Klicken Sie auf **Herunterladen**, um die Aktivitäten als CSV-Datei herunterzuladen.

## <a name="accessing-audit-logs-through-the-azure-ad-reporting-api"></a>Zugriff auf Überwachungsprotokolle über die Azure AD-Berichterstellungs-API
Überwachungsprotokolle werden in der gleichen Pipeline wie andere Aktivitäten für Azure Active Directory veröffentlicht, sodass auf sie über die [Azure Active Directory-Berichterstellungs-API](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-audit-reference) zugegriffen werden kann.

### <a name="prerequisites"></a>Voraussetzungen
Um den Azure AD-Berichterstellungs-API zu authentifizieren, müssen Sie zunächst eine Anwendung registrieren. Führen Sie in jedem Fall die Schritte unter [Voraussetzungen zum Zugriff auf die Azure AD-Berichterstellungs-API](https://azure.microsoft.com/documentation/articles/active-directory-reporting-api-getting-started/) aus.

### <a name="accessing-the-api"></a>Zugriff auf die API
Um die Active Directory B2C-Überwachungsprotokolle über die API herunterzuladen, müssen Sie die Protokolle nach der Kategorie **B2C** filtern. Verwenden Sie zum Filtern nach Kategorie den Abfragezeichenfolgen-Parameter, wenn Sie den Azure AD-Berichterstellungs-API-Endpunkt aufrufen, wie unten gezeigt:

`https://graph.windows.net/your-b2c-tentant.onmicrosoft.com/activities/audit?api-version=beta&$filter=category eq 'B2C'`

### <a name="powershell-script"></a>PowerShell-Skript
Das folgende Skript bietet ein Beispiel für die Verwendung von PowerShell zur Abfrage der Azure AD-Berichterstellungs-API und zum Speichern der Ergebnisse als JSON-Datei:

```powershell
# This script will require registration of a Web Application in Azure Active Directory (see https://azure.microsoft.com/documentation/articles/active-directory-reporting-api-getting-started/)

# Constants
$ClientID       = "your-client-application-id-here"       # Insert your application's Client ID, a Globally Unique ID (registered by Global Admin)
$ClientSecret   = "your-client-application-secret-here"   # Insert your application's Client Key/Secret string
$loginURL       = "https://login.microsoftonline.com"
$tenantdomain   = "your-b2c-tenant.onmicrosoft.com"       # AAD B2C Tenant; for example, contoso.onmicrosoft.com
$resource       = "https://graph.windows.net"             # Azure AD Graph API resource URI
$7daysago       = "{0:s}" -f (get-date).AddDays(-7) + "Z" # Use 'AddMinutes(-5)' to decrement minutes, for example
Write-Output "Searching for events starting $7daysago"

# Create HTTP header, get an OAuth2 access token based on client id, secret and tenant domain
$body       = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
$oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

# Parse audit report items, save output to file(s): auditX.json, where X = 0 thru n for number of nextLink pages
if ($oauth.access_token -ne $null) {
    $i=0
    $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}
    $url = 'https://graph.windows.net/' + $tenantdomain + '/activities/audit?api-version=beta&$filter=category eq ''B2C''and activityDate gt ' + $7daysago

    # loop through each query page (1 through n)
    Do {
        # display each event on the console window
        Write-Output "Fetching data using Uri: $url"
        $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)
        foreach ($event in ($myReport.Content | ConvertFrom-Json).value) {
            Write-Output ($event | ConvertTo-Json)
        }

        # save the query page to an output file
        Write-Output "Save the output to a file audit$i.json"
        $myReport.Content | Out-File -FilePath audit$i.json -Force
        $url = ($myReport.Content | ConvertFrom-Json).'@odata.nextLink'
        $i = $i+1
    } while($url -ne $null)
} else {
    Write-Host "ERROR: No Access Token"
}
```
