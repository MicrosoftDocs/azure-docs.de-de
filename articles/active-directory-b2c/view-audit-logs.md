---
title: Zugreifen auf und Überprüfen von Überwachungsprotokollen
titleSuffix: Azure AD B2C
description: Hier erfahren Sie, wie Sie programmgesteuert und im Azure-Portal auf Azure AD B2C-Überwachungsprotokolle zugreifen.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.date: 02/20/2020
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 9485fb09d3ac7684ba91bcd37720a0e55603404b
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94952725"
---
# <a name="accessing-azure-ad-b2c-audit-logs"></a>Zugriff auf Active Directory B2C-Überwachungsprotokolle

Azure Active Directory B2C (Azure AD B2C) gibt Überwachungsprotokolle aus, die Aktivitätsinformationen über B2C-Ressourcen, ausgestellte Token und Administratorzugriff enthalten. Dieser Artikel bietet eine kurze Übersicht über die in Überwachungsprotokollen verfügbaren Informationen sowie Anweisungen zum Zugreifen auf diese Daten für Ihren Azure AD B2C-Mandanten.

Überwachungsprotokollereignisse werden nur **sieben Tage** lang aufbewahrt. Planen Sie den Download und die Speicherung Ihrer Protokolle mit einer der unten aufgeführten Methoden, wenn eine längere Aufbewahrungsdauer erforderlich ist.

> [!NOTE]
> Im Azure-Portal werden auf der Seite **Azure Active Directory** oder **Azure AD B2C** im Abschnitt **Benutzer** keine Benutzeranmeldungen für einzelne Azure AD B2C-Anwendungen angezeigt. Für die dort aufgeführten Anmeldeereignisse werden zwar Benutzeraktivitäten angezeigt, aber auf die jeweilige B2C-Anwendung, bei der sich der Benutzer angemeldet hat, sind keine Rückschlüsse möglich. Hierfür müssen Sie die Überwachungsprotokolle verwenden. Wie Sie dabei vorgehen, wird in diesem Artikel beschrieben.

## <a name="overview-of-activities-available-in-the-b2c-category-of-audit-logs"></a>Übersicht der verfügbaren Aktivitäten in der B2C-Kategorie von Überwachungsprotokollen

Die **B2C**-Kategorie in Überwachungsprotokollen umfasst die folgenden Aktivitätstypen:

|Aktivitätstyp |BESCHREIBUNG  |
|---------|---------|
|Authorization |Aktivitäten, die sich auf die Autorisierung eines Benutzers für den Zugriff auf B2C-Ressourcen beziehen (z. B. ein Administrator, der auf eine Liste von B2C-Richtlinien zugreift).         |
|Verzeichnis |Aktivitäten im Zusammenhang mit Verzeichnisattributen, die abgerufen werden, wenn sich ein Administrator über das Azure-Portal anmeldet. |
|Application | Erstellungs-, Lese-, Aktualisierungs- und Löschvorgänge (Create, Read, Update, Delete, CRUD) für B2C-Anwendungen. |
|Key |CRUD-Vorgänge für in einem B2C-Schlüsselcontainer gespeicherte Schlüssel. |
|Resource |CRUD-Vorgänge für B2C-Ressourcen. Beispielsweise Richtlinien und Identitätsanbieter.
|Authentication |Überprüfung von Benutzeranmeldeinformationen und Tokenausstellung.|

Informationen zu den CRUD-Aktivitäten des Benutzerobjekts finden Sie in der Kategorie **Hauptverzeichnis**.

## <a name="example-activity"></a>Beispielaktivität

Im folgenden Beispiel aus dem Azure-Portal sehen Sie die Daten, die erfasst werden, wenn sich ein Benutzer bei einem externen Identitätsanbieter (in diesem Fall Facebook) anmeldet:

![Beispiel für die Seite der Aktivitätsdetails des Überwachungsprotokolls im Azure-Portal](./media/view-audit-logs/audit-logs-example.png)

Das Panel „Aktivitätsbereich“ enthält die folgenden wichtigen Informationen:

|`Section`|Feld|BESCHREIBUNG|
|-------|-----|-----------|
| Aktivität | Name | Die ausgeführte Aktivität. Dies kann beispielsweise *Issue an id_token to the application* (ID-Token für die Anwendung ausstellen) sein, wodurch die Benutzeranmeldung abgeschlossen wird. |
| Initiiert von (Akteur) | ObjectID | Die **Objekt-ID** der B2C-Anwendung, bei der sich der Benutzer anmeldet. Dieser Bezeichner ist im Azure-Portal nicht sichtbar, kann aber über die Microsoft Graph-API aufgerufen werden. |
| Initiiert von (Akteur) | Spn | Die **Anwendungs-ID** der B2C-Anwendung, bei der sich der Benutzer anmeldet. |
| Ziel(e) | ObjectID | Die **Objekt-ID** des Benutzers, der sich anmeldet. |
| Weitere Details | TenantId | Die **Mandanten-ID** des Azure AD B2C-Mandanten. |
| Weitere Details | `PolicyId` | Die **Richtlinien-ID** für den Benutzerfluss (Richtlinie), der zur Anmeldung des Benutzers verwendet wird. |
| Weitere Details | ApplicationId | Die **Anwendungs-ID** der B2C-Anwendung, bei der sich der Benutzer anmeldet. |

## <a name="view-audit-logs-in-the-azure-portal"></a>Anzeigen von Überwachungsprotokollen im Azure-Portal

Das Azure-Portal bietet Zugriff auf die Überwachungsprotokollereignisse in Ihrem Azure AD B2C-Mandanten.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com)
1. Wechseln Sie zu dem Verzeichnis, das Ihren Azure AD B2C-Mandanten enthält, und navigieren Sie dann zu **Azure AD B2C**.
1. Wählen Sie im linken Menü unter **Aktivitäten** die Option **Überwachungsprotokolle** aus.

Es wird eine Liste der Aktivitätsereignisse angezeigt, die in den letzten sieben Tagen protokolliert wurden.

![Beispiel für einen Filter mit zwei Aktivitätsereignissen im Azure-Portal](./media/view-audit-logs/audit-logs-example-filter.png)

Es sind mehrere Filteroptionen verfügbar, einschließlich:

* **Aktivitätsressourcentyp**: Filtern Sie nach den Aktivitätstypen, die in der Tabelle im Abschnitt [Übersicht der verfügbaren Aktivitäten in der B2C-Kategorie von Überwachungsprotokollen](#overview-of-activities-available-in-the-b2c-category-of-audit-logs) aufgeführt sind.
* **Datum**: Filtern Sie den Datumsbereich der angezeigten Aktivitäten.

Wenn Sie eine Zeile in der Liste auswählen, werden die Aktivitätsdetails für das Ereignis angezeigt.

Zum Herunterladen der Liste der Aktivitätsereignisse in einer CSV-Datei wählen Sie **Herunterladen** aus.

## <a name="get-audit-logs-with-the-azure-ad-reporting-api"></a>Abrufen von Überwachungsprotokollen mit der Azure AD-Berichterstellungs-API

Überwachungsprotokolle werden in der gleichen Pipeline wie andere Aktivitäten für Azure Active Directory veröffentlicht, sodass auf sie über die [Azure Active Directory-Berichterstellungs-API](/graph/api/directoryaudit-list) zugegriffen werden kann. Weitere Informationen finden Sie unter [Erste Schritte mit der Berichterstellungs-API von Azure Active Directory](../active-directory/reports-monitoring/concept-reporting-api.md).

### <a name="enable-reporting-api-access"></a>Aktivieren des Zugriffs auf die Berichterstellungs-API

Um den skript- oder anwendungsbasierten Zugriff auf die Azure AD-Berichterstellungs-API zu ermöglichen, benötigen Sie eine Anwendung, die in Ihrem Azure AD B2C-Mandanten mit den folgenden API-Berechtigungen registriert ist. Sie können diese Berechtigungen für eine vorhandene Anwendungsregistrierung in Ihrem B2C-Mandanten aktivieren oder eine neue Anwendung erstellen, die speziell für die Verwendung mit der Überwachungsprotokollautomatisierung verwendet wird.

* „Microsoft Graph“ > „Anwendungsberechtigungen“ > „AuditLog“ > „AuditLog.Read.All“

Führen Sie die Schritte des folgenden Artikels aus, um eine Anwendung mit den erforderlichen Berechtigungen zu registrieren:

[Verwalten von Azure AD B2C mit Microsoft Graph](microsoft-graph-get-started.md)

Sehen Sie sich anschließend den Abschnitt „PowerShell-Skript“ weiter unten in diesem Artikel an. Dort wird anhand eines Beispiels gezeigt, wie Aktivitätsereignisse mit einem Skript abgerufen werden können.

### <a name="access-the-api"></a>Zugreifen auf die API

Wenn Sie Azure AD B2C-Überwachungsprotokollereignisse über die API herunterladen möchten, filtern Sie die Protokolle nach der Kategorie `B2C`. Verwenden Sie zum Filtern nach Kategorie den Abfragezeichenfolgenparameter `filter`, wenn Sie den Endpunkt der Azure AD-Berichterstellungs-API aufrufen.

```http
https://graph.microsoft.com/v1.0/auditLogs/directoryAudits?$filter=loggedByService eq 'B2C' and activityDateTime gt 2019-09-10T02:28:17Z
```

### <a name="powershell-script"></a>PowerShell-Skript

Im folgenden PowerShell-Skript sehen Sie ein Beispiel für das Abfragen der Azure AD-Berichterstellungs-API. Nach dem Abfragen der API werden die protokollierten Ereignisse an die Standardausgabe ausgegeben, und dann wird die JSON-Ausgabe in eine Datei geschrieben.

Sie können dieses Skript in der [Azure Cloud Shell](overview.md) ausprobieren. Sie müssen es dann mit Ihrer Anwendungs-ID, dem Clientgeheimnis und dem Namen Ihres Azure AD B2C-Mandanten aktualisieren.

```powershell
# This script requires an application registration that's granted Microsoft Graph API permission
# https://docs.microsoft.com/azure/active-directory-b2c/microsoft-graph-get-started

# Constants
$ClientID       = "your-client-application-id-here"       # Insert your application's client ID, a GUID
$ClientSecret   = "your-client-application-secret-here"   # Insert your application's client secret
$tenantdomain   = "your-b2c-tenant.onmicrosoft.com"       # Insert your Azure AD B2C tenant domain name

$loginURL       = "https://login.microsoftonline.com"
$resource       = "https://graph.microsoft.com"           # Microsoft Graph API resource URI
$7daysago       = "{0:s}" -f (get-date).AddDays(-7) + "Z" # Use 'AddMinutes(-5)' to decrement minutes, for example
Write-Output "Searching for events starting $7daysago"

# Create HTTP header, get an OAuth2 access token based on client id, secret and tenant domain
$body       = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
$oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

# Parse audit report items, save output to file(s): auditX.json, where X = 0 through n for number of nextLink pages
if ($oauth.access_token -ne $null) {
    $i=0
    $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}
    $url = "https://graph.microsoft.com/v1.0/auditLogs/directoryAudits?`$filter=loggedByService eq 'B2C' and activityDateTime gt  " + $7daysago

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

Nachfolgend sehen Sie die JSON-Darstellung des Beispielsaktivitätsereignisses, das weiter oben in diesem Artikel erörtert wurde:

```json
{
    "id": "B2C_DQO3J_4984536",
    "category": "Authentication",
    "correlationId": "00000000-0000-0000-0000-000000000000",
    "result": "success",
    "resultReason": "N/A",
    "activityDisplayName": "Issue an id_token to the application",
    "activityDateTime": "2019-09-14T18:13:17.0618117Z",
    "loggedByService": "B2C",
    "operationType": "",
    "initiatedBy": {
        "user": null,
        "app": {
            "appId": "00000000-0000-0000-0000-000000000000",
            "displayName": null,
            "servicePrincipalId": null,
            "servicePrincipalName": "00000000-0000-0000-0000-000000000000"
        }
    },
    "targetResources": [
        {
            "id": "00000000-0000-0000-0000-000000000000",
            "displayName": null,
            "type": "User",
            "userPrincipalName": null,
            "groupType": null,
            "modifiedProperties": []
        }
    ],
    "additionalDetails": [
        {
            "key": "TenantId",
            "value": "test.onmicrosoft.com"
        },
        {
            "key": "PolicyId",
            "value": "B2C_1A_signup_signin"
        },
        {
            "key": "ApplicationId",
            "value": "00000000-0000-0000-0000-000000000000"
        },
        {
            "key": "Client",
            "value": "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/76.0.3809.132 Safari/537.36"
        },
        {
            "key": "IdentityProviderName",
            "value": "facebook"
        },
        {
            "key": "IdentityProviderApplicationId",
            "value": "0000000000000000"
        },
        {
            "key": "ClientIpAddress",
            "value": "127.0.0.1"
        }
    ]
}
```

## <a name="next-steps"></a>Nächste Schritte

Sie können weitere Verwaltungsaufgaben automatisieren, etwa das [Verwalten von Azure AD B2C-Benutzerkonten mit Microsoft Graph](manage-user-accounts-graph-api.md).