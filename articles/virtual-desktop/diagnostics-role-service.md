---
title: Identifizieren von Problemen mit der Diagnosefunktion von Windows Virtual Desktop (Vorschauversion) – Azure
description: Enthält eine Beschreibung der Diagnosefunktion für Windows Virtual Desktop (Vorschauversion) und ihrer Verwendung.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 747e177b0fbbfb9049959c3194ee39c3234bba50
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65234031"
---
# <a name="identify-issues-with-the-diagnostics-feature"></a>Identifizieren von Problemen mit der Diagnosefunktion

Windows Virtual Desktop (Vorschauversion) verfügt über eine Diagnosefunktion, mit der der Administrator Probleme über eine zentrale Benutzeroberfläche identifizieren kann. Die Windows Virtual Desktop-Rollen protokollieren jeweils eine Diagnoseaktivität, wenn ein Benutzer mit dem System interagiert. Jedes Protokoll enthält relevante Informationen, z. B. die an der Transaktion beteiligten Windows Virtual Desktop-Rollen, Fehlermeldungen, Mandanteninformationen und Benutzerinformationen. Diagnoseaktivitäten werden sowohl bei Endbenutzer- als auch bei Administratoraktionen erstellt und können in drei Buckets kategorisiert werden:

* Feedabonnementaktivitäten: Der Endbenutzer löst diese Aktivitäten jeweils aus, wenn er versucht, über Microsoft-Remotedesktopanwendungen eine Verbindung mit seinem Feed herzustellen.
* Verbindungsaktivitäten: Der Endbenutzer löst diese Aktivitäten jeweils aus, wenn er versucht, über Microsoft-Remotedesktopanwendungen eine Verbindung mit einem Desktop oder einer RemoteApp herzustellen.
* Verwaltungsaktivitäten: Der Administrator löst diese Aktivitäten jeweils aus, wenn er im System Verwaltungsvorgänge durchführt, z. B. das Erstellen von Hostpools, Zuweisen von Benutzern zu App-Gruppen und Erstellen von Rollenzuweisungen.
  
Verbindungen, die Windows Virtual Desktop nicht erreichen, werden nicht in den Diagnoseergebnissen angezeigt, da der Diagnoserollendienst selbst Teil von Windows Virtual Desktop ist. Windows Virtual Desktop-Verbindungsprobleme können auftreten, wenn es für den Endbenutzer zu Problemen mit der Netzwerkkonnektivität kommt.

Zunächst müssen Sie das [Windows Virtual Desktop-PowerShell-Modul herunterladen und importieren](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview), um es in Ihrer PowerShell-Sitzung verwenden zu können.

## <a name="diagnose-issues-with-powershell"></a>Diagnostizieren von Problemen mit PowerShell

Für die Windows Virtual Desktop-Diagnose wird nur ein PowerShell-Cmdlet verwendet, aber es sind viele optionale Parameter verfügbar, mit denen Probleme eingegrenzt und isoliert werden können. In den folgenden Abschnitten sind die Cmdlets aufgeführt, die Sie zum Diagnostizieren von Problemen ausführen können. Die meisten Filter können zusammen angewendet werden. Die Werte in Klammern, z. B. `<tenantName>`, sollten durch die Werte ersetzt werden, die für Ihre Situation gelten.

### <a name="retrieve-diagnostic-activities-in-your-tenant"></a>Abrufen von Diagnoseaktivitäten in Ihrem Mandanten

Sie können Diagnoseaktivitäten abrufen, indem Sie das Cmdlet **Get-RdsDiagnosticActivities** eingeben. Mit dem folgenden Cmdlet wird eine Liste mit Diagnoseaktivitäten zurückgegeben, die nach dem Zeitpunkt des Auftretens sortiert ist.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName>
```

Wie bei anderen Windows Virtual Desktop-PowerShell-Cmdlets auch, müssen Sie den Parameter **-TenantName** verwenden, um den Namen des Mandanten anzugeben, den Sie für Ihre Abfrage nutzen möchten. Der Mandantenname gilt für fast alle Abfragen von Diagnoseaktivitäten.

### <a name="retrieve-detailed-diagnostic-activities"></a>Abrufen von ausführlichen Diagnoseaktivitäten

Mit dem Parameter **-Detailed** werden zusätzliche Details für jede zurückgegebene Diagnoseaktivität bereitgestellt. Das Format für jede Aktivität variiert je nach Aktivitätstyp. Der Parameter **-Detailed** kann allen **Get-RdsDiagnosticActivities**-Abfragen hinzugefügt werden. Dies ist im folgenden Beispiel veranschaulicht.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -Detailed
```

### <a name="retrieve-a-specific-diagnostic-activity-by-activity-id"></a>Abrufen einer bestimmten Diagnoseaktivität nach Aktivitäts-ID

Mit dem Parameter **-ActivityId** wird eine bestimmte Diagnoseaktivität zurückgegeben, sofern sie vorhanden ist. Dies ist im folgenden Beispiel-Cmdlet dargestellt.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityId <ActivityIdGuid>
```

### <a name="filter-diagnostic-activities-by-user"></a>Filtern von Diagnoseaktivitäten nach Benutzer

Mit dem Parameter **-UserName** wird eine Liste mit Diagnoseaktivitäten zurückgegeben, die vom angegebenen Benutzer initiiert werden. Dies ist im folgenden Beispiel-Cmdlet dargestellt.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN>
```

Der Parameter **-UserName** kann auch mit anderen optionalen Filterparametern kombiniert werden.

### <a name="filter-diagnostic-activities-by-time"></a>Filtern von Diagnoseaktivitäten nach Zeit

Sie können die Liste mit den zurückgegebenen Diagnoseaktivitäten mit den Parametern **-StartTime** und **-EndTime** filtern. Mit dem Parameter **-StartTime** wird eine Liste mit Diagnoseaktivitäten zurückgegeben, die ab einem bestimmten Datum beginnt. Dies ist im folgenden Beispiel dargestellt.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -StartTime "08/01/2018"
```

Der Parameter **-EndTime** kann einem Cmdlet mit dem Parameter **-StartTime** hinzugefügt werden, um einen bestimmten Zeitraum anzugeben, für den Sie Ergebnisse abrufen möchten. Mit dem folgenden Beispiel-Cmdlet wird eine Liste mit den Diagnoseaktivitäten für den Zeitraum zwischen dem 1. August und dem 10. August zurückgegeben.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -StartTime "08/01/2018" -EndTime "08/10/2018"
```

Die Parameter **-StartTime** und **-EndTime** können auch mit anderen optionalen Filterparametern kombiniert werden.

### <a name="filter-diagnostic-activities-by-activity-type"></a>Filtern von Diagnoseaktivitäten nach Aktivitätstyp

Sie können auch den Parameter **-ActivityType** verwenden, um Diagnoseaktivitäten nach Aktivitätstyp zu filtern. Mit dem folgenden Cmdlet wird eine Liste mit Endbenutzerverbindungen zurückgegeben:

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityType Connection
```

Mit dem folgenden Cmdlet wird eine Liste mit Administratorverwaltungsaufgaben zurückgegeben:

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityType Management
```

Für das Cmdlet **Get-RdsDiagnosticActivities** wird das Angeben des Feeds als ActivityType derzeit nicht unterstützt.

### <a name="filter-diagnostic-activities-by-outcome"></a>Filtern von Diagnoseaktivitäten nach Ergebnis

Sie können die Liste mit den zurückgegebenen Diagnoseaktivitäten mit dem Parameter **-Outcome** nach dem Ergebnis filtern. Mit dem folgenden Beispiel-Cmdlet wird eine Liste mit erfolgreichen Diagnoseaktivitäten zurückgegeben.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -Outcome Success
```

Mit dem folgenden Beispiel-Cmdlet wird eine Liste mit nicht erfolgreichen Diagnoseaktivitäten zurückgegeben.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -Outcome Failure
```

Der Parameter **-Outcome** kann auch mit anderen optionalen Filterparametern kombiniert werden.

## <a name="common-error-scenarios"></a>Allgemeine Fehlerszenarien

Fehlerszenarien werden in die Bereiche „Dienstintern“ und „Außerhalb von Windows Virtual Desktop“ (extern) kategorisiert.

* Internes Problem: Betrifft Szenarien, die nicht vom Mandantenadministrator gelöst werden können und als ein Supportproblem behandelt werden müssen. Wenn Sie Feedback über die [Windows Virtual Desktop Tech Community](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) bereitstellen, geben Sie die Aktivitäts-ID sowie den ungefähren Zeitrahmen an, in dem das Problem aufgetreten ist.
* Externes Problem: Betrifft Szenarien, die vom Systemadministrator gelöst werden können. Dies erfolgt außerhalb von Windows Virtual Desktop.

In der folgenden Tabelle sind häufige Fehler aufgeführt, die für Ihre Administratoren ggf. auftreten können.

>[!NOTE]
>Diese Vorschauversion enthält keine vollständige Kategorisierung der Fehler und wird regelmäßig aktualisiert. Damit Sie immer über die aktuellen Informationen verfügen, ist es ratsam, diesen Artikel mindestens einmal pro Monat aufzurufen.

### <a name="external-management-error-codes"></a>Externe Verwaltung: Fehlercodes

|Numerischer Code|Fehlercode|Vorgeschlagene Lösung|
|---|---|---|
|3|UnauthorizedAccess|Der Benutzer, der das administrative PowerShell-Cmdlet ausführen möchte, verfügt entweder nicht über die erforderlichen Berechtigungen oder hat seinen Benutzernamen falsch eingegeben.|
|1000|TenantNotFound|Der von Ihnen eingegebene Mandantenname stimmt nicht mit vorhandenen Mandanten überein. Überprüfen Sie den Mandantennamen auf Tippfehler, und wiederholen Sie den Vorgang.|
|1006|TenantCannotBeRemovedHasSessionHostPools|Sie können einen Mandanten nicht löschen, solange er Objekte enthält. Löschen Sie zuerst die Sitzungshostpools, und wiederholen Sie dann den Vorgang.|
|2000|HostPoolNotFound|Der von Ihnen eingegebene Hostpoolname stimmt nicht mit vorhandenen Hostpools überein. Überprüfen Sie den Hostpoolnamen auf Tippfehler, und wiederholen Sie den Vorgang.|
|2005|HostPoolCannotBeRemovedHasApplicationGroups|Sie können einen Hostpool nicht löschen, solange er Objekte enthält. Entfernen Sie zuerst alle App-Gruppen im Hostpool.|
|2004|HostPoolCannotBeRemovedHasSessionHosts|Entfernen Sie zuerst alle Sitzungen, bevor Sie den Sitzungshostpool löschen.|
|5001|SessionHostNotFound|Der von Ihnen abgefragte Sitzungshost ist unter Umständen offline. Überprüfen Sie den Status des Hostpools.|
|5008|SessionHostUserSessionsExist |Sie müssen alle Benutzer vom Sitzungshost abmelden, bevor Sie Ihre gewünschte Verwaltungsaktivität ausführen.|
|6000|AppGroupNotFound|Der von Ihnen eingegebene App-Gruppenname stimmt nicht mit vorhandenen App-Gruppen überein. Überprüfen Sie den App-Gruppennamen auf Tippfehler, und wiederholen Sie den Vorgang.|
|6022|RemoteAppNotFound|Der von Ihnen eingegebene RemoteApp-Name stimmt nicht mit vorhandenen RemoteApps überein. Überprüfen Sie den RemoteApp-Namen auf Tippfehler, und wiederholen Sie den Vorgang.|
|6010|PublishedItemsExist|Der Name der Ressource, die Sie veröffentlichen möchten, ist mit dem Namen einer bereits vorhandenen Ressource identisch. Ändern Sie den Namen der Ressource, und wiederholen Sie den Vorgang.|
|7002|NameNotValidWhiteSpace|Verwenden Sie keine Leerzeichen im Namen.|
|8\.000|InvalidAuthorizationRoleScope|Der von Ihnen eingegebene Rollenname stimmt nicht mit vorhandenen Rollennamen überein. Überprüfen Sie den Rollennamen auf Tippfehler, und wiederholen Sie den Vorgang. |
|8001|UserNotFound |Der von Ihnen eingegebene Benutzername stimmt nicht mit vorhandenen Benutzernamen überein. Überprüfen Sie den Namen auf Tippfehler, und wiederholen Sie den Vorgang.|
|8005|UserNotFoundInAAD |Der von Ihnen eingegebene Benutzername stimmt nicht mit vorhandenen Benutzernamen überein. Überprüfen Sie den Namen auf Tippfehler, und wiederholen Sie den Vorgang.|
|8008|TenantConsentRequired|Befolgen Sie [diese Anleitung](tenant-setup-azure-active-directory.md#grant-azure-active-directory-permissions-to-the-windows-virtual-desktop-preview-service), um die Einwilligung für Ihren Mandanten zu erteilen.|

### <a name="external-connection-error-codes"></a>Externe Verbindung: Fehlercodes

|Numerischer Code|Fehlercode|Vorgeschlagene Lösung|
|---|---|---|
|-2147467259|ConnectionFailedAdTrustedRelationshipFailure|Der Sitzungshost ist nicht richtig in Active Directory eingebunden.|
|-2146233088|ConnectionFailedUserHasValidSessionButRdshIsUnhealthy|Bei der Verbindungsherstellung ist ein Fehler aufgetreten, weil der Sitzungshost nicht verfügbar ist. Überprüfen Sie die Integrität des Sitzungshosts.|
|-2146233088|ConnectionFailedClientDisconnect|Wenn dieser Fehler häufig angezeigt wird, sollten Sie sicherstellen, dass der Computer des Benutzers mit dem Netzwerk verbunden ist.|
|-2146233088|ConnectionFailedNoHealthyRdshAvailable|Die Sitzung, mit der der Hostbenutzer eine Verbindung herstellen möchte, ist nicht fehlerfrei. Debuggen Sie den virtuellen Computer.|
|-2146233088|ConnectionFailedUserNotAuthorized|Der Benutzer verfügt nicht über die Berechtigung zum Zugreifen auf die veröffentlichte App oder den Desktop. Der Fehler wird unter Umständen angezeigt, nachdem der Administrator die veröffentlichten Ressourcen entfernt hat. Bitten Sie den Benutzer, den Feed in der Remotedesktopanwendung zu aktualisieren.|
|2|FileNotFound|Die Anwendung, auf die der Benutzer zugreifen möchte, ist entweder falsch installiert oder auf einen falschen Pfad festgelegt.|
|3|InvalidCredentials|Der vom Benutzer eingegebene Benutzername oder das Kennwort stimmt nicht mit vorhandenen Benutzernamen bzw. Kennwörtern überein. Überprüfen Sie die Anmeldeinformationen auf Tippfehler, und wiederholen Sie den Vorgang.|
|8|ConnectionBroken|Die Verbindung zwischen dem Client und dem Gateway oder Server wurde getrennt. Sofern dieser Vorgang nicht unerwartet auftritt, ist keine Aktion erforderlich.|
|14|UnexpectedNetworkDisconnect|Die Verbindung mit dem Netzwerk wurde getrennt. Bitten Sie den Benutzer, die Verbindung wiederherzustellen.|
|24|ReverseConnectFailed|Der virtuelle Hostcomputer verfügt nicht über eine direkte Sichtlinie zum RD-Gateway. Stellen Sie sicher, dass die IP-Adresse des Gateways aufgelöst werden kann.|

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Rollen in Windows Virtual Desktop finden Sie unter [Windows Virtual Desktop-Umgebung (Vorschauversion)](environment-setup.md).

Eine Liste mit verfügbaren PowerShell-Cmdlets für Windows Virtual Desktop finden Sie in der [PowerShell-Referenz](/powershell/windows-virtual-desktop/overview).
