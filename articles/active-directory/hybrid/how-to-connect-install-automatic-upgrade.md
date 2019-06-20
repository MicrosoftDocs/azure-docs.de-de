---
title: 'Azure AD Connect: Automatisches Upgrade | Microsoft-Dokumentation'
description: Dieses Thema beschreibt das integrierte automatische Upgradefeature in der Azure AD Connect-Synchronisierung.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 6b395e8f-fa3c-4e55-be54-392dd303c472
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/26/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: bfd61b78ca3027ade1f2f48dec33e0a8ed508d3d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60349823"
---
# <a name="azure-ad-connect-automatic-upgrade"></a>Azure AD Connect: automatische Upgrade
Dieses Feature wurde mit [Build 1.1.105.0 (veröffentlicht im Februar 2016)](reference-connect-version-history.md#111050) eingeführt.  Dieses Feature wurde in [Build 1.1.561](reference-connect-version-history.md#115610) aktualisiert und unterstützt nun zusätzliche Szenarios, die zuvor nicht unterstützt wurden.

## <a name="overview"></a>Übersicht
Noch nie konnten Sie so leicht sicherstellen, dass Ihre Azure AD Connect-Installation immer auf dem neuesten Stand ist  – mit dem **automatischen Upgradefeature** . Dieses Feature ist standardmäßig für Express-Installationen und DirSync-Upgrades aktiviert. Wenn eine neue Version veröffentlicht wird, erfolgt ein automatisches Upgrade Ihrer Installation.
Das automatische Upgrade ist in folgenden Fällen standardmäßig aktiviert:

* Installationen mit Express-Einstellungen und DirSync-Upgrades.
* Verwendung von SQL Express LocalDB (wird bei Express-Einstellungen immer verwendet). DirSync mit SQL Express verwendet ebenfalls LocalDB.
* Das AD-Konto ist das MSOL_-Standardkonto, das mit den Express-Einstellungen und DirSync erstellt wird
* Es sind weniger als 100.000 Objekte im Metaverse enthalten.

Der aktuelle Zustand des automatischen Upgrades kann über das PowerShell-Cmdlet `Get-ADSyncAutoUpgrade`angezeigt werden. Folgende Zustände sind möglich:

| Zustand | Kommentar |
| --- | --- |
| Aktiviert |Das automatische Upgrade ist aktiviert. |
| Ausgesetzt |Nur vom System festgelegt. Das System ist **derzeit nicht** zum Erhalten automatischer Upgrades berechtigt. |
| Deaktiviert |Das automatische Upgrade ist deaktiviert. |

Mit `Set-ADSyncAutoUpgrade` können Sie zwischen **aktiviert** und **deaktiviert** wechseln. Nur das System sollte den Zustand **Ausgesetzt**festlegen.  Vor 1.1.750.0 hat das Cmdlet „Set-ADSyncAutoUpgrade“ das automatische Upgrade blockiert, wenn der auto-upgrade-Status auf „Angehalten“ festgelegt wurde. Diese Funktion wurde geändert, sodass das automatische Upgrade nicht blockiert wird.

Das automatische Upgrade verwendet Azure AD Connect Health als Upgrade-Infrastruktur. Damit das automatische Upgrade funktioniert, müssen die URLs im Proxy für **Azure AD Connect Health** wie unter [URLs und IP-Adressbereiche von Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)beschrieben geöffnet sein.


Wenn auf dem Server die **Synchronization Service Manager** -Benutzeroberfläche geöffnet ist, wird das Upgrade angehalten, bis die Benutzeroberfläche geschlossen wird.

## <a name="troubleshooting"></a>Problembehandlung
Wenn das automatische Upgrade Ihrer Connect-Installation nicht wie erwartet funktioniert, befolgen Sie diese Schritte, um herauszufinden, wo der Fehler liegen könnte.

Ein automatisches Upgrade wird voraussichtlich nicht direkt am Erscheinungstag einer neuen Version versucht. Upgrades unterliegen einer beabsichtigten Zufälligkeit – Sie müssen sich also keine Sorgen machen, wenn Ihre Installation nicht sofort aktualisiert wird.

Wenn Sie der Meinung sind, dass ein Fehler vorliegt, führen Sie zuerst `Get-ADSyncAutoUpgrade` aus, um sicherzustellen, dass das automatische Upgrade aktiviert ist.

Stellen Sie dann sicher, dass Sie die erforderlichen URLs in Ihrem Proxy oder in der Firewall geöffnet haben. Die automatische Aktualisierung verwendet Azure AD Connect Health wie in der [Übersicht](#overview)beschrieben. Wenn Sie einen Proxy verwenden, muss Health für die Verwendung eines [Proxyservers](how-to-connect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy)konfiguriert sein. Prüfen Sie auch die [Verbindung von Health](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service) zu Azure AD.

Wenn Sie die Verbindung mit Azure AD überprüft haben, prüfen Sie die Ereignisprotokolle. Starten Sie die Ereignisanzeige, und sehen Sie sich das Ereignisprotokoll für die **Anwendung** an. Fügen Sie einen Ereignisprotokollfilter für die Quelle **Azure AD Connect Upgrade** und den Ereignis-ID-Bereich **300-399** hinzu.  
![Ereignisprotokollfilter für das automatische Upgrade](./media/how-to-connect-install-automatic-upgrade/eventlogfilter.png)  

So können Sie alle Ereignisprotokolle anzeigen, die dem Status für das automatische Upgrade zugeordnet sind.  
![Ereignisprotokollfilter für das automatische Upgrade](./media/how-to-connect-install-automatic-upgrade/eventlogresult.png)  

Der Ergebniscode hat ein Präfix mit einer Übersicht über den Status.

| Ergebniscodepräfix | BESCHREIBUNG |
| --- | --- |
| Erfolgreich |Das Upgrade der Installation wurde erfolgreich durchgeführt. |
| UpgradeAborted |Das Upgrade wurde durch ein temporäres Problem angehalten. Das Upgrade wird erneut ausgeführt, und es wird davon ausgegangen, dass es später erfolgreich durchgeführt werden kann. |
| UpgradeNotSupported |Die Konfiguration des Systems blockiert das automatische Upgrade. Das Upgrade wird erneut versucht, um zu ermitteln, ob sich der Status ändert. Es ist jedoch damit zu rechnen, dass das Upgrade für das System manuell durchgeführt werden muss. |

Im Folgenden finden Sie eine Liste der Meldungen, die am häufigsten angezeigt werden. Es werden nicht alle Meldungen aufgeführt, aber aus den Ergebnismeldungen wird das jeweilige Problem klar.

| Ergebnismeldung | BESCHREIBUNG |
| --- | --- |
| **UpgradeAborted** | |
| UpgradeAbortedCouldNotSetUpgradeMarker |Es konnte nicht in die Registrierung geschrieben werden. |
| UpgradeAbortedInsufficientDatabasePermissions |Die integrierte Administratorengruppe verfügt nicht über die erforderlichen Berechtigungen für die Datenbank. Führen Sie ein manuelles Upgrade auf die aktuelle Version von Azure AD Connect aus, um dieses Problem zu beheben. |
| UpgradeAbortedInsufficientDiskSpace |Es ist nicht genügend Speicherplatz vorhanden, um ein Upgrade zu unterstützen. |
| UpgradeAbortedSecurityGroupsNotPresent |Es konnten nicht alle Sicherheitsgruppen gefunden und aufgelöst werden, die vom Synchronisierungsmodul verwendet werden. |
| UpgradeAbortedServiceCanNotBeStarted |Der NT-Dienst **Microsoft Azure AD Sync** konnte nicht gestartet werden. |
| UpgradeAbortedServiceCanNotBeStopped |Der NT-Dienst **Microsoft Azure AD Sync** konnte nicht angehalten werden. |
| UpgradeAbortedServiceIsNotRunning |Der NT-Dienst **Microsoft Azure AD Sync** wird nicht ausgeführt. |
| UpgradeAbortedSyncCycleDisabled |Die SyncCycle-Option im [Scheduler](how-to-connect-sync-feature-scheduler.md) wurde deaktiviert. |
| UpgradeAbortedSyncExeInUse |Die [Synchronization Service Manager-Benutzeroberfläche](how-to-connect-sync-service-manager-ui.md) ist auf dem Server geöffnet. |
| UpgradeAbortedSyncOrConfigurationInProgress |Der Installations-Assistent wird ausgeführt, oder es wurde eine Synchronisierung außerhalb des Schedulers geplant. |
| **UpgradeNotSupported** | |
| UpgradeNotSupportedAdfsSignInMethod | Sie haben AD FS als Anmeldemethode ausgewählt. |
| UpgradeNotSupportedCustomizedSyncRules |Sie haben der Konfiguration eigene benutzerdefinierte Regeln hinzugefügt. |
| UpgradeNotSupportedDeviceWritebackEnabled |Sie haben das Feature [Geräterückschreiben](how-to-connect-device-writeback.md) aktiviert. |
| UpgradeNotSupportedGroupWritebackEnabled |Sie haben das Feature [Gruppenrückschreiben](how-to-connect-preview.md#group-writeback) aktiviert. |
| UpgradeNotSupportedInvalidPersistedState |Die Installation ist keine Express-Einstellung und kein DirSync-Upgrade. |
| UpgradeNotSupportedMetaverseSizeExceeeded |Es sind mehr als 100.000 Objekte im Metaverse enthalten. |
| UpgradeNotSupportedMultiForestSetup |Sie stellen Verbindungen mit mehr als einer Gesamtstruktur her. Beim Express-Setup wird nur eine Verbindung mit einer Gesamtstruktur hergestellt. |
| UpgradeNotSupportedNonLocalDbInstall |Sie verwenden keine SQL Server Express LocalDB-Datenbank. |
| UpgradeNotSupportedNonMsolAccount |Das [AD DS-Connector-Konto](reference-connect-accounts-permissions.md#ad-ds-connector-account) ist nicht mehr das Standardkonto vom Typ „MSOL_“. |
| UpgradeNotSupportedNotConfiguredSignInMethod | Beim Einrichten von AAD Connect haben Sie *Nicht konfigurieren* beim Auswählen der Anmeldemethode gewählt. |
| UpgradeNotSupportedPtaSignInMethod | Sie haben Pass-Through-Authentifizierung als Anmeldemethode ausgewählt. |
| UpgradeNotSupportedStagingModeEnabled |Der Server ist auf den [Stagingmodus](how-to-connect-sync-staging-server.md)festgelegt. |
| UpgradeNotSupportedUserWritebackEnabled |Sie haben das Feature [Benutzerrückschreiben](how-to-connect-preview.md#user-writeback) aktiviert. |

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zum [Integrieren lokaler Identitäten in Azure Active Directory](whatis-hybrid-identity.md).
