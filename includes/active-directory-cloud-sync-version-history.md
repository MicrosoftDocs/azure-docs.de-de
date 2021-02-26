---
ms.openlocfilehash: 69f0da2f1528ad1f45762a8f754cc2020b4cb880
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98900931"
---
In diesem Artikel werden die veröffentlichten Versionen und Features des Azure Active Directory Connect-Bereitstellungs-Agent aufgeführt. Das Azure AD-Team aktualisiert den Bereitstellungs-Agent regelmäßig mit neuen Features und Funktionen. Der Bereitstellungs-Agent wird automatisch aktualisiert, wenn eine neue Version veröffentlicht wird. 

Microsoft stellt direkte Unterstützung für die neueste Agent-Version und die unmittelbare Vorgängerversion bereit.

## <a name="113540"></a>1.1.354.0

20. Januar 2021: Für den Download veröffentlicht

### <a name="new-features-and-improvements"></a>Neue Features und Verbesserungen
- Verbesserung der GMSA-Erfahrung, einschließlich Unterstützung vordefinierter GMSA-Konten
- Unterstützung von [PowerShell-Cmdlets](../articles/active-directory/cloud-sync/how-to-gmsa-cmdlets.md) für das Setup von GMSA
- [CLI-Unterstützung](../articles/active-directory/cloud-sync/how-to-install-pshell.md) für die Agent-Installation (automatische Installation)
- Zusätzliche Diagnose für Quarantäneprobleme von Agent-Quellen
- Fehlerkorrekturen, die u. a. Reduzierung des Speicherbedarfs von Bereichsfiltern für Organisationseinheiten, Ausführen von PHS ausschließlich für Benutzer im Bereich, Behandlung geschachtelter Objekte in Organisationseinheiten bei Begrenzung von Organisationseinheiten auf bestimmte Bereiche usw. umfassen. 


### <a name="fixed-issues"></a>Behobene Probleme
-    Verhindern der Quarantäne, wenn sich die Bereichsgruppe nicht im Bereich befindet
-   Bei Konfiguration von Bereichsfiltern: PHS-Auftrag gilt ausschließlich für Benutzer im Bereich
-   Agent bleibt mitunter während eines Upgrades hängen
-   Anfängliche Synchronisierung für Objekte in geschachtelten Organisationseinheiten bei der Bereichsdefinition von Organisationseinheiten
-   Cmdlet Repair-AADCloudSyncToolsAccount nun stabiler
-   Reduzierung der großen Arbeitsspeicherbelegung von Bereichsfiltern für Organisationseinheiten
-   Die Überprüfung der Rolle „Administrator“ schlägt fehl, wenn die Rollenmitglieder eine Sicherheitsgruppe enthalten
-   Behebung des Problems mit der Berechtigung für den GMSA-Ordner, das das Verlängern des Agent-Zertifikats verhindert







## <a name="112810"></a>1.1.281.0

### <a name="release-status"></a>Releasestatus

23. November 2020: Für den Download veröffentlicht

### <a name="new-features-and-improvements"></a>Neue Features und Verbesserungen

* Unterstützung für [gMSA](../articles/active-directory/cloud-sync/how-to-prerequisites.md#group-managed-service-accounts)
* Unterstützung während des inkrementellen oder Delta-Synchronisierungsprozesses für Gruppen, die kleiner als 1500 Mitglieder sind. Dies gilt, wenn der Gruppenbereichsfilter verwendet wird.
* Unterstützung für große Gruppen mit einer Mitgliedsgröße von bis zu 15.000.
* Verbesserungen der anfänglichen Synchronisierung
* Erweiterte ausführliche Protokollierung
* Einführung des [AADCloudSyncTools-PowerShell-Moduls](../articles/active-directory/cloud-sync/reference-powershell.md)
* Einschränkungen für die Installation des Agents auf einem nicht englischsprachigen Server wurden korrigiert.
* Unterstützung für PHS-Filterung nur für Objekte im Gültigkeitsbereich (ursprünglich wurden Kennworthashes für alle Objekte synchronisiert)
* Es wurde ein Problem mit dem Arbeitsspeicherleck im Agent behoben.
* Verbesserte Bereitstellungsprotokolle
* Unterstützung für das Konfigurieren des [LDAP-Verbindungstimeouts](../articles/active-directory/cloud-sync/how-to-manage-registry-options.md#configure-ldap-connection-timeout) 
* Unterstützung für das Konfigurieren der [Empfehlungsverfolgung](../articles/active-directory/cloud-sync/how-to-manage-registry-options.md#configure-referral-chasing) 


## <a name="11960"></a>1.1.96.0

### <a name="release-status"></a>Releasestatus

4\. Dezember 2019 Für den Download veröffentlicht

### <a name="new-features-and-improvements"></a>Neue Features und Verbesserungen

* Unterstützung der Synchronisierung von Benutzer-, Kontakt- und Gruppendaten zwischen dem lokalen Active Directory und Azure AD für die [Azure AD Connect-Cloudsynchronisierung](../articles/active-directory/cloud-sync/what-is-cloud-sync.md).


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