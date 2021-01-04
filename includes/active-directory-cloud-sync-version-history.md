---
ms.openlocfilehash: 3fc2475569765116d46a175629f25d9d49634942
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95993947"
---
In diesem Artikel werden die veröffentlichten Versionen und Features des Azure Active Directory Connect-Bereitstellungs-Agent aufgeführt. Das Azure AD-Team aktualisiert den Bereitstellungs-Agent regelmäßig mit neuen Features und Funktionen. Der Bereitstellungs-Agent wird automatisch aktualisiert, wenn eine neue Version veröffentlicht wird. 

Microsoft stellt direkte Unterstützung für die neueste Agent-Version und die unmittelbare Vorgängerversion bereit.

## <a name="112810"></a>1.1.281.0

### <a name="release-status"></a>Releasestatus

23. November 2020: Für den Download veröffentlicht

### <a name="new-features-and-improvements"></a>Neue Features und Verbesserungen

* Unterstützung für [gMSA](../articles/active-directory/cloud-provisioning/how-to-prerequisites.md#group-managed-service-accounts)
* Unterstützung während des inkrementellen oder Delta-Synchronisierungsprozesses für Gruppen, die kleiner als 1500 Mitglieder sind. Dies gilt, wenn der Gruppenbereichsfilter verwendet wird.
* Unterstützung für große Gruppen mit einer Mitgliedsgröße von bis zu 15.000.
* Verbesserungen der anfänglichen Synchronisierung
* Erweiterte ausführliche Protokollierung
* Einführung des [AADCloudSyncTools-PowerShell-Moduls](../articles/active-directory/cloud-provisioning/reference-powershell.md)
* Einschränkungen für die Installation des Agents auf einem nicht englischsprachigen Server wurden korrigiert.
* Unterstützung für PHS-Filterung nur für Objekte im Gültigkeitsbereich (ursprünglich wurden Kennworthashes für alle Objekte synchronisiert)
* Es wurde ein Problem mit dem Arbeitsspeicherleck im Agent behoben.
* Verbesserte Bereitstellungsprotokolle


## <a name="11960"></a>1.1.96.0

### <a name="release-status"></a>Releasestatus

4\. Dezember 2019 Für den Download veröffentlicht

### <a name="new-features-and-improvements"></a>Neue Features und Verbesserungen

* Umfasst die Unterstützung der Synchronisierung von Benutzer-, Kontakt- und Gruppendaten auf dem lokalen Active Directory mit Azure AD für die [Azure AD Connect-Cloudbereitstellung](../articles/active-directory/cloud-provisioning/what-is-cloud-provisioning.md).


## <a name="11670"></a>1.1.67.0

### <a name="release-status"></a>Releasestatus

9\. September 2019: Veröffentlichung für automatisches Update

### <a name="new-features-and-improvements"></a>Neue Features und Verbesserungen

* Möglichkeit zum Konfigurieren zusätzlicher Ablaufverfolgung und Protokollierung für das Debuggen des Bereitstellungs-Agent
* Möglichkeit zum Abrufen nur jener Azure AD-Attribute, die in der Zuordnung konfiguriert sind, um die Synchronisierungsleistung zu verbessern

### <a name="fixed-issues"></a>Behobene Probleme

* Ein Fehler wurde behoben, aufgrund dessen der Agent nicht mehr reagierte, wenn Probleme durch Azure AD-Verbindungsfehler aufgetreten sind.
* Ein Fehler wurde behoben, der beim Lesen von Binärdaten aus Azure Active Directory Probleme verursacht hat.
* Ein Fehler wurde behoben, aufgrund dessen Agent die Vertrauensstellung mit dem Cloud-Hybrididentitätsdienst nicht erneuern konnte.

## <a name="11300"></a>1.1.30.0

### <a name="release-status"></a>Releasestatus

23. Januar 2019: Für den Download veröffentlicht

### <a name="new-features-and-improvements"></a>Neue Features und Verbesserungen

* Bereitstellungs-Agent und Connector-Architektur wurden für bessere Leistung, Stabilität und Zuverlässigkeit überarbeitet 
* Die Konfiguration des Bereitstellungs-Agent wurde durch Verwendung eines Installationsassistenten auf der Benutzeroberfläche vereinfacht 
* Unterstützung für automatische Agent-Updates hinzugefügt


