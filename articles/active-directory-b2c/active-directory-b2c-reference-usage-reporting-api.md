---
title: 'Verwendungsberichts-API: Beispiele und Definitionen in Azure Active Directory B2C | Microsoft-Dokumentationen'
description: Leitfaden und Beispiele zum Abrufen von Berichten zu Azure AD B2C-Mandantenbenutzern, Authentifizierungen und mehrstufigen Authentifizierungen.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 08/04/2017
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: fe7dd90bdec816ee433310a803d85c57f4892f8c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66508715"
---
# <a name="accessing-usage-reports-in-azure-ad-b2c-via-the-reporting-api"></a>Zugriff auf Verwendungsberichte in Azure AD B2C über die Berichterstattungs-API

Azure Active Directory B2C (Azure AD B2C) ermöglicht die Authentifizierung basierend auf Benutzeranmeldungen und Azure Multi-Factor Authentication. Die Authentifizierung wird für die Endbenutzer Ihrer Anwendungsfamilie über Identitätsanbieter bereitgestellt. Wenn die Anzahl der im Mandanten registrierten Benutzer, die Anbieter, die sie zur Registrierung verwendet haben, und die Anzahl der Authentifizierungen nach Typ bekannt sind, können Fragen wie die folgenden beantwortet werden:
* Wie viele Benutzer jedes Identitätsanbietertyps (z.B. ein Microsoft- oder LinkedIn-Account) haben sich in den letzten 10 Tagen registriert?
* Wie viele mehrstufige-Authentifizierungen wurden im vergangenen Monat erfolgreich abgeschlossen?
* Wie viele anmeldebasierten Authentifizierungen wurden in diesem Monat abgeschlossen? Pro Tag? Pro Anwendung?
* Wie kann ich die erwarteten monatlichen Kosten für meine Azure AD B2C-Mandantenaktivitäten ermitteln?

Dieser Artikel konzentriert sich auf Berichte, die an Abrechnungsaktivität, Benutzerzahl, Anzahl abrechenbarer, anmeldebasierter Authentifizierungen und Anzahl mehrstufiger Authentifizierungen gebunden sind.


## <a name="prerequisites"></a>Voraussetzungen
Bevor Sie beginnen, müssen Sie die [Voraussetzungen zum Zugriff auf die Azure AD-Berichterstellungs-APIs](https://azure.microsoft.com/documentation/articles/active-directory-reporting-api-getting-started/) erfüllen. Erstellen Sie eine Anwendung, beschaffen Sie sich einen geheimen Schlüssel für sie, und gewähren Sie ihr Zugriffsrechte für die Berichte Ihres Azure AD B2C-Mandanten. *Bash-Skript*- und *Python-Skript*-Beispiele werden auch hier zur Verfügung gestellt. 

## <a name="powershell-script"></a>PowerShell-Skript
Dieses Skript veranschaulicht die Erstellung von vier Nutzungsberichten mithilfe des `TimeStamp`-Parameters und des `ApplicationId`-Filters.

```powershell
# This script will require the Web Application and permissions setup in Azure Active Directory

# Constants
$ClientID      = "your-client-application-id-here"  
$ClientSecret  = "your-client-application-secret-here"
$loginURL      = "https://login.microsoftonline.com"
$tenantdomain  = "your-b2c-tenant-domain.onmicrosoft.com"  
# Get an Oauth 2 access token based on client id, secret and tenant domain
$body          = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
$oauth         = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body
if ($oauth.access_token -ne $null) {
    $headerParams  = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}

    Write-host Data from the tenantUserCount report
    Write-host ====================================================
     # Returns a JSON document for the report
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/tenantUserCount?api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the tenantUserCount report with datetime filter
    Write-host ====================================================
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/tenantUserCount?%24filter=TimeStamp+gt+2016-10-15&api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cAuthenticationCountSummary report
    Write-host ====================================================
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cAuthenticationCountSummary?api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cAuthenticationCount report with datetime filter
    Write-host ====================================================
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cAuthenticationCount?%24filter=TimeStamp+gt+2016-09-20+and+TimeStamp+lt+2016-10-03&api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cAuthenticationCount report with ApplicationId filter
    Write-host ====================================================
    # Returns a JSON document for the " " report
        $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cAuthenticationCount?%24filter=ApplicationId+eq+ada78934-a6da-4e69-b816-10de0d79db1d&api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cMfaRequestCountSummary
    Write-host ====================================================
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cMfaRequestCountSummary?api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cMfaRequestCount report with datetime filter
    Write-host ====================================================
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cMfaRequestCount?%24filter=TimeStamp+gt+2016-09-10+and+TimeStamp+lt+2016-10-04&api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cMfaRequestCount report with ApplicationId filter
    Write-host ====================================================
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cMfaRequestCountSummary?%24filter=ApplicationId+eq+ada78934-a6da-4e69-b816-10de0d79db1d&api-version=beta")
     Write-host $myReport.Content

} else {
    Write-Host "ERROR: No Access Token"
    }
```


## <a name="usage-report-definitions"></a>Definitionen von Verwendungsberichten
* **tenantUserCount**: Die Anzahl der Benutzer im Mandanten nach Typ des Identitätsanbieters pro Tag in den letzten 30 Tagen. (Optional liefert ein `TimeStamp`-Filter Benutzerzahlen ab einem angegebenen Datum bis zum aktuellen Datum.) Der Bericht enthält:
  * **TotalUserCount**: Die Anzahl aller Benutzerobjekte.
  * **OtherUserCount**: Die Anzahl von Azure Active Directory-Benutzern (nicht Azure AD B2C-Benutzer).
  * **LocalUserCount**: Die Anzahl von Azure AD B2C-Benutzerkonten, die mit Anmeldeinformationen lokal auf dem Azure AD B2C-Mandanten erstellt wurden.

* **AlternateIdUserCount**: Die Anzahl von Azure AD B2C-Benutzern, die über externe Identitätsanbieter registriert sind (z.B. Facebook, ein Microsoft-Konto oder einen anderen Azure Active Directory-Mandanten, auch bezeichnet als `OrgId`).

* **b2cAuthenticationCountSummary**: Zusammenfassung der täglichen Anzahl abrechenbarer Authentifizierungen in den letzten 30 Tagen nach Tag und nach Typ der Authentifizierung

* **b2cAuthenticationCount**: Die Anzahl von Authentifizierungen innerhalb eines Zeitraums. Der Standardwert sind die letzten 30 Tage.  (Optional: Die `TimeStamp`-Parameter am Anfang und am Ende legen einen bestimmten Zeitraum fest.) Die Ausgabe umfasst `StartTimeStamp` (frühestes Datum der Aktivität für diesen Mandanten) und `EndTimeStamp` (neuestes Update).

* **b2cMfaRequestCountSummary**: Zusammenfassung der täglichen Anzahl von mehrstufigen Authentifizierungen nach Tag und nach Typ (SMS oder Spracheingabe)


## <a name="limitations"></a>Einschränkungen
Die Benutzeranzahl betreffende Daten werden alle 24 bis 48 Stunden aktualisiert. Authentifizierungen werden mehrmals täglich aktualisiert. Bei Verwendung des `ApplicationId`-Filters kann eine leere Berichtsantwort auf eine der folgenden Bedingungen zurückzuführen sein:
  * Die Anwendungs-ID ist im Mandanten nicht vorhanden. Stellen Sie sicher, dass sie korrekt ist.
  * Die Anwendungs-ID ist zwar vorhanden, aber im Berichtszeitraum wurden keine Daten gefunden. Überprüfen Sie die Datum-Uhrzeit-Parameter.


## <a name="next-steps"></a>Nächste Schritte
### <a name="monthly-bill-estimates-for-azure-ad"></a>Schätzung der monatlichen Rechnung für Azure AD
In Kombination mit [den aktuellsten verfügbaren Azure AD B2C-Preisen](https://azure.microsoft.com/pricing/details/active-directory-b2c/) können Sie tägliche, wöchentliche und monatliche Azure-Nutzung schätzen.  Eine Schätzung ist besonders hilfreich, wenn Sie Änderungen im Mandantenverhalten planen, die sich möglicherweise auf die Gesamtkosten auswirken können. Sie können die tatsächlichen Kosten in Ihrem [verknüpften Azure-Abonnement](active-directory-b2c-how-to-enable-billing.md) überprüfen.

### <a name="options-for-other-output-formats"></a>Optionen für andere Ausgabeformate
Der folgende Code zeigt Beispiele für das Senden von Ausgaben an JSON, eine Name-Wert-Liste und XML:
```powershell
# to output to JSON use following line in the PowerShell sample
$myReport.Content | Out-File -FilePath name-your-file.json -Force

# to output the content to a name value list
($myReport.Content | ConvertFrom-Json).value | Out-File -FilePath name-your-file.txt -Force

# to output the content in XML use the following line
(($myReport.Content | ConvertFrom-Json).value | ConvertTo-Xml).InnerXml | Out-File -FilePath name-your-file.xml -Force
```
