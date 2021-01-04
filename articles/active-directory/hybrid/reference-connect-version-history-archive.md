---
title: 'Azure AD Connect: Archiv des Versionsfreigabeverlaufs | Microsoft-Dokumentation'
description: In diesem Artikel sind alle archivierten Versionen von Azure AD Connect und Azure AD Sync aufgeführt.
services: active-directory
author: billmath
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.date: 07/23/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 859a4f199e65dd0c3aee9424029f6060683d5fbd
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94836086"
---
# <a name="azure-ad-connect-version-release-history-archive"></a>Azure AD Connect: Archiv des Versionsfreigabeverlaufs

Das Azure Active Directory-Team (Azure AD) aktualisiert Azure AD Connect regelmäßig mit neuen Features und Funktionen. Nicht alle Erweiterungen gelten für alle Benutzergruppen.

>[!NOTE] 
> Dieser Artikel enthält Versionsreferenzinformationen zu allen archivierten Versionen (1.3.20.0 und ältere Versionen) von Azure AD.  Informationen zu aktuellen Releases finden Sie unter [Azure AD Connect: Verlauf der Versionsveröffentlichungen](reference-connect-version-history.md).

## <a name="13200"></a>1.3.20.0 

### <a name="release-status"></a>Releasestatus 

24.04.2019: Für den Download veröffentlicht

### <a name="new-features-and-improvements"></a>Neue Features und Verbesserungen 

- Die Unterstützung der Domänenaktualisierung wurde hinzugefügt. 
- Das Feature „Öffentliche Exchange-E-Mail-Ordner“ wird allgemein verfügbar. 
- Der Assistent für die Fehlerbehandlung bei Dienstfehlern wurde verbessert. 
- Auf der Benutzeroberfläche von Synchronization Service Manager wurde auf der Eigenschaftenseite für den Connector ein Warnungslink hinzugefügt. 
- Das Feature für ein einheitliches Gruppenrückschreiben ist jetzt allgemein verfügbar. 
- Die SSPR Fehlermeldung beim Fehlen eines LDAP-Steuerelements für den Domänencontroller wurde verbessert. 
- Eine Diagnose für DCOM-Registrierungsfehler während der Installation wurde hinzugefügt.  
- Die Verfolgung von RPC-Fehlern bei der Kennworthashsynchronisierung wurde verbessert. 
- EA-Anmeldeinformationen sind jetzt von einer untergeordneten Domäne zulässig. 
- Der Datenbankname (Standardname „ADSync“) kann jetzt während der Installation eingegeben werden.
- Es wurde ein Upgrade auf ADAL 3.19.8 durchgeführt, um eine WS-Trust-Lösung für Ping-Signale auszuwählen und Unterstützung für neue Azure-Instanzen hinzuzufügen. 
- Die Gruppensynchronisierungsregeln wurden geändert, um „SamAccountName“, „DomainNetbios“ und „DomainFQDN“ an die Cloud zu übergeben – erforderlich für Ansprüche 
- Die Standardbehandlung von Synchronisierungsregeln wurde geändert. [Hier](how-to-connect-fix-default-rules.md) erfahren Sie mehr.
- Ein neuer Agent wurde hinzugefügt, der als Windows-Dienst ausgeführt wird. Dieser Agent namens „Admin-Agent“ ermöglicht eine umfassendere Remotediagnose des Azure AD Connect-Servers, um Microsoft-Techniker bei der Problembehandlung zu unterstützen, wenn Sie eine Supportanfrage öffnen. Dieser Agent ist standardmäßig nicht installiert und aktiviert.  Weitere Informationen zum Installieren und Aktivieren des Agents finden Sie unter [Was ist der Azure AD Connect-Administrator-Agent?](whatis-aadc-admin-agent.md) 
- Die Software-Lizenzbedingungen wurden aktualisiert. 
- Die automatische Upgrade-Unterstützung für Bereitstellungen, die AD FS als Anmeldetyp verwenden, wurde hinzugefügt.  Dadurch entfällt auch die Notwendigkeit, im Rahmen des Upgradeprozesses die Azure AD-Vertrauensstellung der Active Directory Federation Services (AD FS) zu aktualisieren. 
- Eine Verwaltungsaufgabe für Azure AD-Vertrauensstellung wurde hinzugefügt, die zwei Optionen bereitstellt: Vertrauensstellung analysieren/aktualisieren und Vertrauensstellung zurücksetzen. 
- Das Azure AD-Vertrauensstellungsverhalten von AD FS wurde so geändert, dass jetzt stets der Switch „-SupportMultipleDomain“ verwendet wird (enthält Vertrauensstellungs- und Azure AD-Domänenupdates). 
- Die Installation des neuen AD FS-Farmverhaltens wurde so geändert, dass jetzt ein PFX-Zertifikat erforderlich ist. Dadurch entfällt die Option für die Verwendung eines vorinstallierten Zertifikats.
- Die Installation des neuen AD FS-Farmworkflows wurde so aktualisiert, dass nur die Bereitstellung von einem (1) AD FS- und einem (1) WAP-Server zulässig ist.  Alle weiteren Server folgen nach der Erstinstallation. 

### <a name="fixed-issues"></a>Behobene Probleme 

- Die Probleme bei der SQL-Logik für die erneute Verbindungsherstellung für den ADSync-Dienst wurden behoben. 
- Der Fehler bei Neuinstallationen mit einer leeren SQL AOA-DB wurde behoben. 
- Das PS-Berechtigungsskript zum Verfeinern von GWB-Berechtigungen wurde korrigiert. 
- VSS-Fehler mit LocalDB wurden behoben.  
- Die irreführende Fehlermeldung, die angezeigt wurde, wenn sich der Objekttyp nicht im Bereich befand, wurde korrigiert. 
- Das Problem, bei dem die Installation von Azure AD PowerShell auf einem Server unter Umständen einen Assemblykonflikt mit Azure AD Connect verursachte, wurde behoben. 
- Der PHS-Fehler auf dem Stagingserver beim Aktualisieren der Connectoranmeldeinformationen auf der Benutzeroberfläche von Synchronization Service Manager wurde behoben. 
- Einige Speicherverluste wurden behoben. 
- Korrektur sonstiger Autoupgrade-Fehler 
- Verschiedene Fehler bei der Export- und unbestätigten Importverarbeitung wurden behoben. 
- Das Problem bei der Behandlung des umgekehrten Schrägstrichs in den Filtern für Domänen und Organisationseinheiten wurde behoben. 
- Das Problem des ADSync-Diensts, der mehr als zwei Minuten zum Beenden benötigte und dadurch ein Problem beim Upgrade verursachte, wurde behoben. 




## <a name="12700"></a>1.2.70.0

### <a name="release-status"></a>Releasestatus

18.12.2018: Für den Download veröffentlicht

### <a name="fixed-issues"></a>Behobene Probleme

Dieser Build aktualisiert die nicht standardmäßigen Connectors (z. B. den generischen LDAP-Connector und den generischen SQL-Connector), die im Lieferumfang von Azure AD Connect enthalten sind. Weitere Informationen zu den entsprechenden Connectors finden Sie unter Version 1.1.911.0 in [Connector – Versionsveröffentlichungsverlauf](/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-version-history).


## <a name="12690"></a>1.2.69.0

### <a name="release-status"></a>Releasestatus
11.12.2018: Für den Download veröffentlicht

### <a name="fixed-issues"></a>Behobene Probleme
Dieser Hotfixbuild ermöglicht es dem Benutzer, eine Zieldomäne in der angegebenen Gesamtstruktur für den Container „RegisteredDevices“ auszuwählen, wenn er das Geräterückschreiben aktiviert.  In früheren Versionen (1.1.819.0 bis 1.2.68.0), die die neue Funktion „Geräteoptionen“ enthalten, ist der Speicherort des Containers „RegisteredDevices“ auf den Gesamtstrukturstamm beschränkt und lässt keine untergeordneten Domänen zu.  Diese Einschränkung manifestierte sich nur in neuen Bereitstellungen, direkte Upgrades waren davon nicht betroffen.  

Wenn ein Build mit der aktualisierten Funktion „Geräteoptionen“ auf einem neuen Server bereitgestellt wurde und das Geräterückschreiben aktiviert ist, müssen Sie den Speicherort des Containers manuell angeben, wenn er sich nicht im Gesamtstrukturstamm befinden soll.  Hierzu müssen Sie das Geräterückschreiben deaktivieren und erneut aktivieren. Dann können Sie den Speicherort des Containers auf der Seite „Gesamtstruktur für Rückschreiben“ angeben.



## <a name="12680"></a>1.2.68.0

### <a name="release-status"></a>Releasestatus 

30.11.2018:  Für den Download veröffentlicht

### <a name="fixed-issues"></a>Behobene Probleme

Dieser Hotfixbuild behebt einen Konflikt, bei dem ein Authentifizierungsfehler aufgrund des unabhängigen Vorhandenseins des Microsoft Online PowerShell-Katalogmoduls auf dem Synchronisierungsserver auftreten kann.



## <a name="12670"></a>1.2.67.0

### <a name="release-status"></a>Releasestatus 

19.11.2018:  Für den Download veröffentlicht

### <a name="fixed-issues"></a>Behobene Probleme

Dieser Hotfixbuild behebt eine Regression im vorherigen Build, bei der das Kennwortrückschreiben fehlschlägt, wenn ein ADDS-Domänencontroller unter Windows Server 2008/R2 verwendet wird.

## <a name="12650"></a>1.2.65.0 

### <a name="release-status"></a>Releasestatus 

25.10.2018: für den Download veröffentlicht


### <a name="new-features-and-improvements"></a>Neue Features und Verbesserungen 


- Die Funktionalität des Attributzurückschreibens wurde geändert, um sicherzustellen, dass gehostete Voicemail wie erwartet funktioniert.  In bestimmten Szenarien hat Azure AD das Attribut „msExchUcVoicemailSettings“ während des Zurückschreibens mit einem Nullwert überschrieben.  Azure AD löscht jetzt nicht mehr den lokalen Wert dieses Attributs, wenn der Cloudwert nicht festgelegt ist.
- Im Azure AD Connect-Assistenten wurde eine Diagnose zur Untersuchung und Identifizierung von Konnektivitätsproblemen mit Azure AD hinzugefügt. Dieselben Diagnosen können auch direkt über PowerShell ausgeführt werden, wobei das Cmdlet „Test-AdSyncAzureServiceConnectivity“ verwendet wird. 
- Im Azure AD Connect-Assistenten wurde eine Diagnose zur Untersuchung und Identifizierung von Konnektivitätsproblemen mit AD hinzugefügt. Dieselben Diagnosen können auch direkt über PowerShell ausgeführt werden, wobei die Funktion „Start-ConnectivityValidation“ im PowerShell-Modul „ADConnectivityTools“ verwendet wird.  Weitere Informationen finden Sie unter [Was ist das PowerShell-Modul „ADConnectivityTool“?](how-to-connect-adconnectivitytools.md).
- Eine Vorüberprüfung der AD-Schemaversion für Hybrid Azure Active Directory-Beitritt und -Gerätezurückschreiben wurde hinzugefügt. 
- Die Attributsuche auf der Seite „Verzeichniserweiterung“ wurde so geändert, dass Groß-/Kleinschreibung nicht berücksichtigt wird.
-   Vollständige Unterstützung für TLS 1.2 wurde hinzugefügt. Dieser Release unterstützt, dass alle anderen Protokolle deaktiviert und nur TLS 1.2 auf dem Computer aktiviert wird, auf dem Azure AD Connect installiert ist.  Weitere Informationen finden Sie unter [Erzwingen von TLS 1.2 für Azure AD Connect](reference-connect-tls-enforcement.md).



### <a name="fixed-issues"></a>Behobene Probleme   

- Korrektur eines Fehlers, bei dem ein Azure AD Connect-Upgrade fehlschlug, wenn SQL Always On verwendet wurde. 
- Korrektur eines Fehlers, damit OE-Namen, die einen Schrägstrich enthalten, ordnungsgemäß analysiert werden. 
- Korrektur eines Problems, bei dem Passthrough-Authentifizierung für eine Neuinstallation im Stagingmodus deaktiviert wurde. 
- Korrektur eines Fehlers, der verhindert hat, dass das PowerShell-Modul geladen wurde, wenn die Tools zur Problembehandlung ausgeführt werden. 
- Korrektur eines Fehlers, der Kunden an der Verwendung numerischer Werte als erstes Zeichen eines Hostnamens gehindert hat. 
- Korrektur eines Fehlers, bei dem Azure AD Connect ungültige Partitionen und Containerauswahl zugelassen hat. 
- Die Fehlermeldung „Ungültiges Kennwort“, wenn Desktop-SSO aktiviert ist, wurde behoben. 
- Verschiedene Fehlerkorrekturen für AD FS-Vertrauensstellungsverwaltung.  
- Beim Konfigurieren des Gerätezurückschreibens – die Schemaüberprüfung wurde dahingehend korrigiert, dass sie nach der Objektklasse „msDs-DeviceContainer“ sucht (eingeführt in WS2012 R2).


## <a name="118820"></a>1.1.882.0  

7\.9.2018: als Download veröffentlicht; kein Release für automatisches Upgrade 

### <a name="fixed-issues"></a>Behobene Probleme  

Beim Azure AD Connect-Upgrade tritt ein Fehler auf, wenn für die ADSync-Datenbank SQL Always On-Verfügbarkeit konfiguriert ist. Dieser Hotfix behebt dieses Problem und ermöglicht erfolgreiche Upgrades. 

## <a name="118800"></a>1.1.880.0

### <a name="release-status"></a>Releasestatus

21.8.2018: Für den Download und für automatisches Upgrade veröffentlicht. 

### <a name="new-features-and-improvements"></a>Neue Features und Verbesserungen

- Die Ping-Federate-Integration in Azure AD Connect ist jetzt allgemein verfügbar. [Erfahren Sie mehr über den Verbund von Azure AD mit Ping Federate](./plan-connect-user-signin.md#federation-with-pingfederate)
- Azure AD Connect erstellt jetzt bei jedem Update die Sicherung der Azure AD-Vertrauensstellung in AD FS und speichert diese zum einfachen Wiederherstellen in einer separaten Datei. [Erfahren Sie mehr über die neuen Funktionen und die Verwaltung der Azure AD-Vertrauensstellung in Azure AD Connect](./how-to-connect-azure-ad-trust.md).
- Neues Tool für Problembehandlung vereinfacht die Problembehandlung durch das Ändern der primären E-Mail-Adresse und das Ausblenden des Kontos von der globalen Adressliste.
- Azure AD Connect wurde aktualisiert und enthält nun die neueste Version von SQL Server 2012 Native Client
- Wenn Sie in der Aufgabe „Benutzeranmeldung ändern“ die Benutzeranmeldung auf die Kennworthashsynchronisierung oder Passthrough-Authentifizierung umstellen, ist das Kontrollkästchen für nahtloses einmaliges Anmelden standardmäßig aktiviert.
- Unterstützung für Windows Server Essentials 2019 hinzugefügt
- Der Azure AD Connect Health Agent wurde auf die neueste Version 3.1.7.0 aktualisiert.
- Wenn das Installationsprogramm während eines Upgrades Änderungen an den Standardregeln für die Synchronisierung erkennt, erhält der Administrator vor dem Überschreiben der geänderten Regeln eine Warnung. Dadurch kann der Benutzer Korrekturmaßnahmen ergreifen und den Vorgang später fortsetzen. Altes Verhalten: Bei Änderungen vordefinierter Regeln wurden diese Regeln durch das manuelle Upgrade ohne Warnung des Benutzers überschrieben, und der Synchronisierungsplaner wurde deaktiviert, ohne den Benutzer darüber zu informieren. Neues Verhalten: Der Benutzer erhält vor dem Überschreiben von vordefinierten Synchronisierungsregeln eine Warnung. Benutzer können den Upgradevorgang beenden und nach dem Ergreifen von Korrekturmaßnahmen fortsetzen.
- Bessere Verarbeitung von FIPS-Kompatibilitätsproblemen mit Bereitstellen einer Fehlermeldung für die MD5-Hash-Generierung in einer FIPS-kompatiblen Umgebung und eines Links zur Dokumentation, in der Sie erfahren, wie Sie das Problem umgehen.
- Update der Benutzeroberfläche zur Verbesserung der Verbundaufgaben im Assistenten, die jetzt eine separate Untergruppe für den Verbund bilden. 
- Alle zusätzlichen Verbundaufgaben werden jetzt zur einfachen Verwendung in einem einzelnen Untermenü gruppiert.
- Ein neues, umgestaltetes ADSyncConfig Posh-Modul (AdSyncConfig.psm1) mit neuen AD-Berechtigungsfunktionen, die aus dem alten ADSyncPrep.psm1 verschoben wurden (in Kürze möglicherweise veraltet)

### <a name="fixed-issues"></a>Behobene Probleme 

- Fehler behoben, aufgrund dessen der Azure AD Connect-Server nach dem Upgrade auf .NET 4.7.2 eine hohe CPU-Auslastung angezeigt hat
- Fehler behoben, aufgrund dessen gelegentlich eine Fehlermeldung für ein automatisch gelöstes SQL-Deadlock-Problem ausgegeben wurde
- Barrierefreiheitsprobleme im Synchronisierungsregel-Editor und im Synchronization Service Manager behoben  
- Fehler behoben, aufgrund dessen Azure AD Connect keine Einstellungsinformationen in der Registrierung abrufen konnte
- Fehler beim Vor-/Zurückspringen im Assistenten behoben
- Fehler behoben, der aufgrund einer falschen Multi-Thread Behandlung im Assistenten aufgetreten ist
- Wenn auf der Seite zum Filtern der Gruppensynchronisierung beim Auflösen von Sicherheitsgruppen ein LDAP-Fehler auftritt, gibt Azure AD Connect die Ausnahme nun zuverlässig zurück.  Die Grundursache für die Verweisausnahme ist noch unbekannt und wird in einem anderen Fehler behoben.
-  Fehler behoben, aufgrund dessen Berechtigungen für STK- und NGC-Schlüssel (ms-DS-KeyCredentialLink-Attribut für Benutzer/Gerätobjekte für WHfB) nicht ordnungsgemäß festgelegt wurden.     
- Fehler behoben, aufgrund dessen 'Set-ADSyncRestrictedPermissions' nicht ordnungsgemäß aufgerufen wurde
-  Berechtigungen für das Gruppenrückschreiben im Azure AD Connect-Installationsassistenten werden jetzt unterstützt.
- Beim Ändern der Anmeldemethode von der Kennworthashsynchronisierung zu AD FS wurde die Kennworthashsynchronisierung nicht deaktiviert.
- Überprüfung von IPv6-Adressen in AD FS-Konfiguration hinzugefügt
- Die Benachrichtigungsmeldung wurde aktualisiert und informiert nun, dass eine vorhandene Konfiguration vorhanden ist.
- Das Geräterückschreiben erkennt in nicht vertrauenswürdigen Gesamtstrukturen keine Container. Dies wurde aktualisiert, um eine bessere Fehlermeldung sowie einen Link zur entsprechenden Dokumentation bereitzustellen.
- Beim Deaktivieren einer Organisationseinheit und anschließendem Synchronisieren/Rückschreiben in Verbindung mit dieser Organisationseinheit tritt ein generischer Synchronisierungsfehler auf. Dies wurde geändert, um eine besser verständliche Fehlermeldung auszugeben.

## <a name="118190"></a>1.1.819.0

### <a name="release-status"></a>Releasestatus

14.05.2018: Für automatisches Upgrade und für den Download veröffentlicht.

### <a name="new-features-and-improvements"></a>Neue Features und Verbesserungen

Neue Features und Verbesserungen

- Diese Version enthält die Public Preview der PingFederate-Integration in Azure AD Connect. Mit dieser Version können Kunden ihre Azure Active Directory-Umgebung einfach und zuverlässig mit PingFederate als Verbundanbieter konfigurieren. Weitere Informationen zur Verwendung dieses neuen Features finden Sie in unserer [Onlinedokumentation](plan-connect-user-signin.md#federation-with-pingfederate). 
- Das Hilfsprogramm zur Problembehandlung des Azure AD Connect-Assistenten wurde aktualisiert, um mehr Fehlerszenarien wie etwa verknüpfte Postfächer und dynamische AD-Gruppen zu analysieren. Weitere Informationen zum Hilfsprogramm für die Problembehandlung finden Sie [hier](tshoot-connect-objectsync.md).
- Die Konfiguration des Geräterückschreibens wird nun ausschließlich über den Azure AD Connect-Assistenten verwaltet.
- Ein neues PowerShell-Modul namens „ADSyncTools.psm1“ zur Behandlung von SQL-Konnektivitätsproblemen und verschiedene andere Problembehandlungstools wurden hinzugefügt. Weitere Informationen zum ADSyncTools-Modul finden Sie [hier](tshoot-connect-tshoot-sql-connectivity.md). 
- Eine neue Zusatzaufgabe namens „Geräteoptionen konfigurieren“ wurde hinzugefügt. Mit dieser Aufgabe lassen sich die beiden folgenden Vorgänge konfigurieren: 
  - **Azure AD-Hybrideinbindung**: Wenn Ihre Umgebung über einen lokalen AD-Fußabdruck verfügt und Sie zudem die Funktionen von Azure Active Directory nutzen möchten, können Sie in Azure AD eingebundene Hybridgeräte implementieren. Hierbei handelt es sich um Geräte, die sowohl in Ihr lokales Active Directory als auch in Ihr Azure Active Directory eingebunden sind.
  - **Geräterückschreiben**: Das Geräterückschreiben ermöglicht den bedingten gerätebasierten Zugriff auf mit AD FS (2012 R2 oder höher) geschützte Geräte.

    >[!NOTE] 
    > - Die Option zum Aktivieren des Geräterückschreibens über „Synchronisierungsoptionen anpassen“ wird ausgegraut. 
    > -  Das PowerShell-Modul für ADPrep ist ab dieser Version veraltet.


### <a name="fixed-issues"></a>Behobene Probleme 

- Mit dieser Version wird die SQL Server Express-Installation auf SQL Server 2012 SP4 aktualisiert, die u.a. Fehlerbehebungen für mehrere Sicherheitsrisiken bereitstellt.  [Hier](https://support.microsoft.com/help/4018073/sql-server-2012-service-pack-4-release-information) finden Sie weitere Informationen zu SQL Server 2012 SP4.
- Verarbeitung von Synchronisierungsregeln: Synchronisierungsregeln für ausgehende Verknüpfungen ohne Verknüpfungsbedingung sollten nicht mehr angewendet werden, wenn die übergeordnete Synchronisierungsregel nicht mehr relevant ist.
- Mehrere Fehlerbehebungen für Bedienungshilfen wurden auf die Synchronization Service Manager-Benutzeroberfläche und den Synchronisierungsregel-Editor angewendet.
- Azure AD Connect-Assistent: Fehler beim Erstellen des AD Connector-Kontos, wenn sich Azure AD Connect in einer Arbeitsgruppe befindet
- Azure AD Connect-Assistent: Anzeige des Kontrollkästchens für die Überprüfung auf der Azure AD-Anmeldeseite, wenn AD-Domänen und überprüfte Azure AD-Domänen nicht übereinstimmen
- Korrektur des automatischen PowerShell-Upgrades zur korrekten Festlegung des Status des automatischen Upgrades in bestimmten Fällen nach versuchtem automatischen Upgrade
- Azure AD Connect-Assistent: Aktualisierung von Telemetriedaten zur Erfassung zuvor fehlender Informationen
- Azure AD Connect-Assistent: Die folgenden Änderungen wurden vorgenommen, wenn Sie die Aufgabe **Benutzeranmeldung ändern** zum Wechseln von AD FS zur Passthrough-Authentifizierung verwenden:
    - Der Passthrough-Authentifizierungs-Agent ist auf dem Azure AD Connect-Server installiert, und die Passthrough-Authentifizierungsfunktion ist aktiviert, bevor wir Verbunddomänen in verwaltete Domänen konvertieren.
    - Verbundbenutzer werden nicht mehr in verwaltete Benutzer konvertiert. Nur Domänen werden konvertiert.
- Azure AD Connect-Assistent: Inkorrekter regulärer Ausdruck für mehrere AD FS-Domänen, wenn der Benutzer-UPN das Sonderzeichen „'“ enthält – Aktualisierung des regulären Ausdrucks zur Unterstützung von Sonderzeichen
- Azure AD Connect-Assistent: Entfernung der falschen Meldung zur Konfiguration des Quellankerattributs, wenn keine Änderung stattgefunden hat 
- Azure AD Connect-Assistent: AD FS-Unterstützung des dualen Verbundszenarios
- Azure AD Connect-Assistent: Keine Aktualisierung der AD FS-Ansprüche für die hinzugefügte Domäne beim Konvertieren einer verwalteten Domäne in eine Verbunddomäne
- Azure AD Connect-Assistent: Bei der Ermittlung der installierten Pakete werden veraltete Produkte für DirSync/Azure AD Sync/Azure AD Connect gefunden. Es wird nun versucht, die veralteten Produkte zu deinstallieren.
- Azure AD Connect-Assistent: Korrektur der Fehlermeldungszuordnung, wenn bei der Installation des Agents für die Passthrough-Authentifizierung ein Fehler auftritt
- Azure AD Connect-Assistent: Entfernung des Containers „Konfiguration“ von der Seite zum Filtern von Domänen und Organisationseinheiten
- Installation der Synchronisierungsengine: Entfernung von unnötiger Legacylogik mit gelegentlichem Fehler aus der MSI-Datei für die Synchronisierungsengine
- Azure AD Connect-Assistent: Korrektur des eingeblendeten Hilfetexts auf der Seite „Optionale Features“ für die Kennworthashsynchronisierung
- Laufzeit des Synchronisierungsmoduls: Korrektur des Szenarios, in dem ein CS-Objekt über einen importierten Löschvorgang verfügt und aufgrund von Synchronisierungsregeln versucht wird, das Objekt erneut bereitzustellen
- Laufzeit des Synchronisierungsmoduls: Hinzufügung eines Hilfelinks für den Leitfaden zur Behandlung von Onlinekonnektivitätsproblemen im Ereignisprotokoll für einen Importfehler
- Laufzeit des Synchronisierungsmoduls: Geringere Arbeitsspeicherauslastung des Synchronisierungsplaners beim Aufzählen von Connectors
- Azure AD Connect-Assistent: Korrektur eines Problems beim Auflösen eines benutzerdefinierten Synchronisierungsdienstkontos ohne AD-Leserechte
- Azure AD Connect-Assistent: Verbesserung der Protokollierung der Auswahl für die Filterung von Domänen und Organisationseinheiten
- Azure AD Connect-Assistent: Hinzufügung von AD FS-Standardansprüchen zur erstellten Verbundvertrauensstellung für das MFA-Szenario
- Azure AD Connect-Assistent: AD FS-WAP-Bereitstellung: Keine Verwendung des neuen Zertifikats beim Hinzufügen eines Servers
- Azure AD Connect-Assistent: DSSO-Ausnahme, wenn „onPremCredentials“ für eine Domäne nicht initialisiert wurde 
- Bevorzugter Fluss des AD-Attributs „distinguishedName“ aus dem aktiven Benutzerobjekt
- Korrektur eines kosmetischen Fehlers, der dazu führte, dass die Rangfolge der ersten OOB-Synchronisierungsregel nicht auf 100, sondern auf 99 festgelegt wurde



## <a name="117510"></a>1.1.751.0
Status: 12.04.2018: Nur für den Download veröffentlicht

>[!NOTE]
>Dieses Release ist ein Hotfix für Azure AD Connect.
### <a name="azure-ad-connect-sync"></a>Azure AD Connect-Synchronisierung
#### <a name="fixed-issues"></a>Behobene Probleme
Es wurde ein Problem korrigiert, bei dem die automatische Ermittlung von Azure-Instanzen für Mandanten in China gelegentlich fehlschlug.  

### <a name="ad-fs-management"></a>AD FS-Verwaltung
#### <a name="fixed-issues"></a>Behobene Probleme

Es lag ein Problem in der Konfiguration der Wiederholungslogik vor, das zu einer ArgumentException führen würde, die Folgendes besagt: „Es wurde bereits ein Element mit dem gleichen Schlüssel hinzugefügt.“  Dies würde dazu führen, dass alle Wiederholungsversuche fehlschlagen.

## <a name="117500"></a>1.1.750.0
Status: 22.03.2018: Für automatisches Upgrade und für den Download veröffentlicht.
>[!NOTE]
>Wenn das Upgrade auf diese neue Version abgeschlossen ist, wird automatisch ein vollständiger Synchronisierungs- und Importvorgang für den Azure AD-Connector und ein vollständiger Synchronisierungsvorgang für den AD-Connector ausgelöst. Da dies je nach Größe Ihrer Azure AD Connect-Umgebung einen längeren Zeitraum in Anspruch nehmen kann, sollten Sie sicherstellen, dass Sie die notwendigen Schritte zur Unterstützung dieses Vorgangs ausgeführt haben. Führen Sie das Upgrade andernfalls zu einem passenderen Zeitpunkt durch.
>[!NOTE]
>Die AutoUpgrade-Funktion wurde fälschlicherweise für einige Mandanten deaktiviert, die Builds mit einer höheren Version als 1.1.524.0 bereitgestellt haben. Führen Sie das folgende PowerShell-Cmdlet aus, um sicherzustellen, dass Ihre Azure AD Connect-Instanz weiterhin automatische Upgrades erhält: „Set-ADSyncAutoUpgrade -AutoupGradeState Enabled“

### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issues"></a>Behobene Probleme

* Mit dem Set-ADSyncAutoUpgrade-Cmdlet wurde Autoupgrade bisher blockiert, wenn der Status des automatischen Upgrades „Angehalten“ lautete. Dies Funktion wurde geändert, sodass AutoUpgrade für zukünftige Builds nicht blockiert wird.
* Auf der Seite **Benutzeranmeldung** wurde die Option „Kennwortsynchronisierung“ in „Kennworthashsynchronisierung“ geändert.  Die Option wurde angepasst, da Azure AD Connect keine Kennwörter synchronisiert, sondern Kennworthashes.  Weitere Informationen finden Sie unter [Implementieren der Kennworthashsynchronisierung mit der Azure AD Connect-Synchronisierung](how-to-connect-password-hash-synchronization.md).

## <a name="117490"></a>1.1.749.0
Status: Für ausgewählte Kunden veröffentlicht

>[!NOTE]
>Wenn das Upgrade auf diese neue Version abgeschlossen ist, wird automatisch ein vollständiger Synchronisierungs- und Importvorgang für den Azure AD-Connector und ein vollständiger Synchronisierungsvorgang für den AD-Connector ausgelöst. Da dies je nach Größe Ihrer Azure AD Connect-Umgebung einen längeren Zeitraum in Anspruch nehmen kann, stellen Sie sicher, dass Sie die notwendigen Schritte unternommen haben, um diesen Vorgang zu unterstützen. Nehmen Sie das Upgrade andernfalls zu einem passenderen Zeitpunkt vor.
### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issues"></a>Behobene Probleme
* Es wurde ein Problem mit dem Zeitfenster für Hintergrundaufgaben auf der Seite „Partitionsfilterung“ behoben, das beim Wechsel auf die nächste Seite entstand.

* Es wurde ein Problem behoben, das während einer benutzerdefinierten ConfigDB-Aktion zu einer Zugriffsverletzung führte.

* Es wurde ein Problem mit der Wiederherstellung nach einem SQL-Verbindungstimeout behoben.

* Es wurde ein Problem behoben, bei dem Zertifikate mit SAN-Platzhaltern eine Voraussetzungsprüfung nicht bestanden haben.

* Es wurde ein Problem behoben, das zu einem Absturz von „miiserver.exe“ während eines Azure AD-Connectorexports führte.

* Es wurde ein Problem mit der versuchten Eingabe von ungültigen Kennwörtern behoben, die beim Ausführen des Azure AD Connect-Assistenten zum Ändern der Konfiguration auf dem Domänencontroller protokolliert wurden.


#### <a name="new-features-and-improvements"></a>Neue Features und Verbesserungen

* Es wurden Datenschutzeinstellungen für die Datenschutz-Grundverordnung (DSGVO) hinzugefügt.  Weitere Informationen finden Sie [hier](reference-connect-user-privacy.md) im Artikel.

[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]  

* Anwendungstelemetrie: Administratoren können diese Datenklasse nach Bedarf aktivieren/deaktivieren

* Azure AD Health-Daten: Administratoren müssen das Health-Portal öffnen, um die zugehörigen Einstellungen zu konfigurieren.
   Sobald die Dienstrichtlinie geändert wurde, wird sie von den Agents gelesen und erzwungen.

* Zusätzliche Aktionen zum Konfigurieren des Geräterückschreibens und eine Statusanzeige für die Seiteninitialisierung.

* Verbesserte allgemeine Diagnose mit HTML-Berichten und vollständiger Datenerfassung in einem ZIP-Text- bzw. HTML-Bericht.

* Die Zuverlässigkeit des automatischen Upgrades wurde verbessert, und es wurden zusätzliche Telemetriedaten hinzugefügt, um sicherzustellen, dass die Integrität des Servers ermittelt werden kann.

* Eingeschränkte Berechtigungen für privilegierte Konten im AD Connector-Konto.

  * Bei Neuinstallationen schränkt der Assistent die Berechtigungen ein, über die privilegierte Konten im MSOL-Konto verfügen, nachdem das MSOL-Konto erstellt wurde.

Die Änderungen gelten für Folgendes:
1. Express-Installationen
2. Benutzerdefinierte Installationen mit Konto für die automatische Erstellung
3. Das Installationsprogramm wurde geändert, sodass bei einer Neuinstallation von Azure AD Connect keine SA-Berechtigung mehr erforderlich ist.

* Ein neues Hilfsprogramm wurde hinzugefügt, um Synchronisierungsprobleme für ein bestimmtes Objekt zu beheben. Es steht in den zusätzlichen Problembehebungsaufgaben des Azure AD Connect-Assistenten unter der Option zum Beheben von Problemen bei der Objektsynchronisierung zur Verfügung. Zurzeit überprüft das Hilfsprogramm Folgendes:

  * Diskrepanz beim UserPrincipalName zwischen dem synchronisierten Benutzerobjekt und dem Benutzerkonto im Azure AD-Mandanten.
  * Ausschluss des Objekts von der Synchronisierung aufgrund der Domänenfilterung.
  * Ausschluss des Objekts von der Synchronisierung aufgrund der Filterung nach Organisationseinheit.

* Ein neues Hilfsprogramm wurde hinzugefügt, um den aktuellen Kennworthash zu synchronisieren, der im lokalen Active Directory-Verzeichnis für ein bestimmtes Benutzerkonto gespeichert ist.

Das Hilfsprogramm erfordert keine Kennwortänderung. Es steht in den zusätzlichen Problembehebungsaufgaben des Azure AD Connect-Assistenten unter der Option zum Beheben von Problemen bei der Kennworthashsynchronisierung zur Verfügung.






## <a name="116540"></a>1.1.654.0
Status: 12. Dezember 2017

>[!NOTE]
>Dieses Release ist ein sicherheitsrelevanter Hotfix für Azure AD Connect.
### <a name="azure-ad-connect"></a>Azure AD Connect
Eine Verbesserung wurde in Azure AD Connect Version 1.1.654.0 (und höher) hinzugefügt, die sicherstellt, dass die im Abschnitt [Sperren des Zugriffs auf das AD DS-Konto](#lock) beschriebenen empfohlenen Berechtigungsänderungen automatisch angewendet werden, wenn Azure AD Connect das AD DS-Konto erstellt. 

- Beim Einrichten von Azure AD Connect kann der installierende Administrator ein vorhandenes AD DS-Konto angeben oder Azure AD Connect das Konto automatisch erstellen lassen. Die Berechtigungsänderungen werden automatisch auf das AD DS-Konto angewendet, das während des Setups von Azure AD Connect erstellt wird. Sie werden nicht auf ein vorhandenes AD DS-Konto angewendet, das ggf. vom installierenden Administrator bereitgestellt wird.
- Für Kunden, die ein Upgrade von einer früheren Version von Azure AD Connect auf 1.1.654.0 (oder höher) durchgeführt haben, werden die Berechtigungsänderungen nicht rückwirkend auf vorhandene AD DS-Konten angewendet, die vor dem Upgrade erstellt wurden. Sie werden nur auf neue AD DS-Konten angewendet, die nach dem Upgrade erstellt werden. Dies erfolgt beim Hinzufügen von neuen AD-Gesamtstrukturen, die mit Azure AD synchronisiert werden sollen.

>[!NOTE]
>In diesem Release wird das Sicherheitsrisiko für neue Installationen von Azure AD Connect behoben, wenn das Dienstkonto während der Installation erstellt wird. Für vorhandene Installationen oder in Fällen, in denen Sie das Konto selbst bereitstellen, sollten Sie sicherstellen, dass dieses Sicherheitsrisiko nicht besteht.
#### <a name="lock-down-access-to-the-ad-ds-account"></a><a name="lock"></a> Sperren des Zugriffs auf das AD DS-Konto
Sie sperren den Zugriff auf das AD DS-Konto durch die Implementierung der folgenden Berechtigungsänderungen im lokalen AD:  

*   Deaktivieren der Vererbung für das angegebene Objekt
*   Entfernen aller ACEs für das angegebene Objekt, mit Ausnahme von ACEs für SELF Wir möchten die Standardberechtigungen für SELF beibehalten.
*   Weisen Sie diese speziellen Berechtigungen zu:

type     | Name                          | Zugriff               | Gilt für
---------|-------------------------------|----------------------|--------------|
Allow    | SYSTEM                        | Vollzugriff         | Dieses Objekt  |
Allow    | Organisationsadministratoren             | Vollzugriff         | Dieses Objekt  |
Allow    | Domänenadministratoren                 | Vollzugriff         | Dieses Objekt  |
Allow    | Administratoren                | Vollzugriff         | Dieses Objekt  |
Allow    | Domänencontroller des Unternehmens | Inhalt auflisten        | Dieses Objekt  |
Allow    | Domänencontroller des Unternehmens | Alle Eigenschaften lesen  | Dieses Objekt  |
Allow    | Domänencontroller des Unternehmens | Leseberechtigungen     | Dieses Objekt  |
Allow    | Authentifizierte Benutzer           | Inhalt auflisten        | Dieses Objekt  |
Allow    | Authentifizierte Benutzer           | Alle Eigenschaften lesen  | Dieses Objekt  |
Allow    | Authentifizierte Benutzer           | Leseberechtigungen     | Dieses Objekt  |

#### <a name="powershell-script-to-tighten-a-pre-existing-service-account"></a>PowerShell-Skript zum Stärken eines bereits vorhandenen Dienstkontos

Um mit dem PowerShell-Skript diese Einstellungen auf ein bereits vorhandenes AD DS-Konto anzuwenden (von Ihrer Organisation bereitgestellt oder bei einer vorherigen Installation von Azure AD Connect erstellt), laden Sie das Skript über den oben bereitgestellten Link herunter.

##### <a name="usage"></a>Syntax:

```powershell
Set-ADSyncRestrictedPermissions -ObjectDN <$ObjectDN> -Credential <$Credential>
```

Hierbei gilt: 

**$ObjectDN** – Active Directory-Konto, dessen Berechtigungen verstärkt werden sollen.

**$Credential** – Anmeldeinformationen des Administrators, der über die erforderlichen Berechtigungen zum Einschränken des Kontos $ObjectDN verfügt. In der Regel hat der Unternehmens- oder Domänenadministrator diese Berechtigungen. Verwenden Sie den vollqualifizierten Domänennamen des Administratorkontos, um Fehler bei der Kontosuche zu vermeiden. Beispiel: contoso.com\admin.

>[!NOTE] 
>„$credential.UserName“ muss im Format „vollqualifizierter Domänenname\Benutzername“ angegeben werden. Beispiel: contoso.com\admin 
##### <a name="example"></a>Beispiel:

```powershell
Set-ADSyncRestrictedPermissions -ObjectDN "CN=TestAccount1,CN=Users,DC=bvtadwbackdc,DC=com" -Credential $credential 
```
### <a name="was-this-vulnerability-used-to-gain-unauthorized-access"></a>Wurde diese Sicherheitslücke verwendet, um nicht autorisierten Zugriff zu erhalten?

Um festzustellen, ob diese Sicherheitslücke verwendet wurde, um Ihre Azure AD Connect-Konfiguration zu manipulieren, sollten Sie das Datum des letzten Zurücksetzens des Kennworts für das Dienstkonto überprüfen.  Wenn der Zeitstempel nicht dem erwarteten Wert entspricht, führen Sie über das Ereignisprotokoll für dieses Ereignis der Kennwortzurücksetzung weitere Untersuchungen durch.

Weitere Informationen finden Sie in der [Microsoft-Sicherheitsempfehlung 4056318](/security-updates/securityadvisories/2017/4056318).

## <a name="116490"></a>1.1.649.0
Status: 27. Oktober 2017

>[!NOTE]
>Dieser Build steht Kunden nicht über das Feature zum automatischen Upgrade von Azure AD Connect zur Verfügung.
### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issue"></a>Behobenes Problem
* Ein Problem mit der Versionskompatibilität zwischen Azure AD Connect und dem Azure AD Connect Health-Agent (für die Synchronisierung) wurde behoben. Dieses Problem betrifft Kunden, die ein direktes Upgrade von Azure AD Connect auf Version 1.1.647.0 durchführen, aber derzeit die Health-Agent Version 3.0.127.0 verwenden. Nach dem Upgrade kann der Health-Agent keine Integritätsdaten mehr über den Azure AD Connect-Synchronisierungsdienst an den Azure AD-Integritätsdienst senden. Behoben wird dieses Problem, indem Version 3.0.129.0 des Health-Agents während des direkten Upgrades von Azure AD Connect installiert wird. Zwischen Version 3.0.129.0 des Health-Agents und Version 1.1.649.0 von Azure AD Connect bestehen keine Kompatibilitätsprobleme.


## <a name="116470"></a>1.1.647.0
Status: 19. Oktober 2017

> [!IMPORTANT]
> Es besteht ein bekanntes Kompatibilitätsproblem zwischen Azure AD Connect Version 1.1.647.0 und Azure AD Connect Health-Agent (für die Synchronisierung) Version 3.0.127.0. Durch dieses Problem wird verhindert, dass der Health-Agent Integritätsdaten zum Azure AD Connect-Synchronisierungsdienst (einschließlich Objektsynchronisierungsfehlern und Ausführungsverlaufsdaten) an den Azure AD Health-Dienst sendet. Überprüfen Sie die aktuelle Version des auf Ihrem Azure AD Connect-Server installierten Azure AD Connect Health-Agents, bevor Sie Ihre Bereitstellung von Azure AD Connect manuell auf Version 1.1.647.0 aktualisieren. Hierzu können Sie zu *Systemsteuerung → Software* wechseln und die Anwendung *Microsoft Azure AD Connect Health-Agent für die Synchronisierung* suchen. Wenn Version 3.0.127.0 installiert ist, wird empfohlen, dass Sie auf die nächste verfügbare Version von Azure AD Connect warten, bevor Sie das Upgrade durchführen. Wenn eine andere Version des Health-Agents als 3.0.127.0 installiert ist, können Sie mit dem manuellen direkten Upgrade fortfahren. Beachten Sie, dass sich dieses Problem nicht auf das Swing-Upgrade oder auf Kunden auswirkt, die eine neue Installation von Azure AD Connect durchführen.
>
>
### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issues"></a>Behobene Probleme
* Es wurde ein Problem mit der Aufgabe *Benutzeranmeldung ändern* im Azure AD Connect-Assistenten behoben:

  * Das Problem tritt auf, wenn in einer vorhandenen Bereitstellung von Azure AD Connect die Kennwortsynchronisierung **aktiviert** ist und Sie versuchen, die Benutzeranmeldemethode als *Pass-Through-Authentifizierung* festzulegen. Vor dem Anwenden der Änderung wird im Assistenten fälschlicherweise die Aufforderung *Disable Password Synchronization* (Kennwortsynchronisierung deaktivieren) angezeigt. Die Kennwortsynchronisierung bleibt jedoch nach Anwendung der Änderung aktiviert. Durch diese Fehlerbehebung wird die Aufforderung im Assistenten nicht mehr angezeigt.

  * Standardmäßig wird die Kennwortsynchronisierung im Assistenten nicht deaktiviert, wenn Sie die Benutzeranmeldemethode über die Aufgabe *Benutzeranmeldung ändern* aktualisieren. Dadurch werden Unterbrechungen für Kunden vermieden, die die Kennwortsynchronisierung beibehalten möchten, obwohl sie die Pass-Through-Authentifizierung oder den Verbund als primäre Benutzeranmeldemethode aktivieren.

  * Wenn Sie die Kennwortsynchronisierung nach dem Aktualisieren der Benutzeranmeldemethode deaktivieren möchten, müssen Sie die Aufgabe *Synchronisierungskonfiguration anpassen* im Assistenten ausführen. Deaktivieren Sie beim Navigieren zur Seite *Optionale Features* die Option *Kennwortsynchronisierung*.

  * Beachten Sie, dass das gleiche Problem auch auftritt, wenn Sie das nahtlose einmalige Anmelden aktivieren oder deaktivieren. Dies ist insbesondere der Fall, wenn Sie über eine vorhandene Bereitstellung von Azure AD Connect mit aktivierter Kennwortsynchronisierung verfügen und die Benutzeranmeldemethode bereits als *Pass-Through-Authentifizierung* konfiguriert ist. Über die Aufgabe *Benutzeranmeldung ändern* versuchen Sie, die Option *Enable Seamless Single Sign-On* (Nahtloses einmaliges Anmelden aktivieren) zu aktivieren oder zu deaktivieren, während die Benutzeranmeldemethode als „Pass-Through-Authentifizierung“ konfiguriert bleibt. Vor dem Anwenden der Änderung wird im Assistenten fälschlicherweise die Aufforderung *Disable Password Synchronization* (Kennwortsynchronisierung deaktivieren) angezeigt. Die Kennwortsynchronisierung bleibt jedoch nach Anwendung der Änderung aktiviert. Durch diese Fehlerbehebung wird die Aufforderung im Assistenten nicht mehr angezeigt.

* Es wurde ein Problem mit der Aufgabe *Benutzeranmeldung ändern* im Azure AD Connect-Assistenten behoben:

  * Das Problem tritt auf, wenn in einer vorhandenen Bereitstellung von Azure AD Connect die Kennwortsynchronisierung **deaktiviert** ist und Sie versuchen, die Benutzeranmeldemethode als *Pass-Through-Authentifizierung* festzulegen. Bei Anwendung der Änderung wird im Assistenten sowohl die Pass-Through-Authentifizierung als auch die Kennwortsynchronisierung aktiviert. Durch diese Fehlerbehebung wird im Assistenten die Kennwortsynchronisierung nicht mehr aktiviert.

  * Bisher war die Kennwortsynchronisierung eine Voraussetzung für die Aktivierung der Pass-Through-Authentifizierung. Beim Festlegen der *Pass-Through-Authentifizierung* als Benutzeranmeldemethode wurde im Assistenten sowohl die Pass-Through-Authentifizierung als auch die Kennwortsynchronisierung aktiviert. Vor Kurzem wurde die Kennwortsynchronisierung als Voraussetzung entfernt. Im Rahmen der Azure AD Connect Version 1.1.557.0 wurde eine Änderung an Azure AD Connect vorgenommen, sodass die Kennwortsynchronisierung nicht aktiviert wird, wenn Sie die Benutzeranmeldemethode als *Pass-Through-Authentifizierung* festlegen. Die Änderung wurde jedoch nur auf die Azure AD Connect-Installation angewendet. Durch diese Fehlerbehebung wird diese Änderung auch auf die Aufgabe *Benutzeranmeldung ändern* angewendet.

  * Beachten Sie, dass das gleiche Problem auch auftritt, wenn Sie das nahtlose einmalige Anmelden aktivieren oder deaktivieren. Dies ist insbesondere der Fall, wenn Sie über eine vorhandene Bereitstellung von Azure AD Connect mit deaktivierter Kennwortsynchronisierung verfügen und die Benutzeranmeldemethode bereits als *Pass-Through-Authentifizierung* konfiguriert ist. Über die Aufgabe *Benutzeranmeldung ändern* versuchen Sie, die Option *Enable Seamless Single Sign-On* (Nahtloses einmaliges Anmelden aktivieren) zu aktivieren oder zu deaktivieren, während die Benutzeranmeldemethode als „Pass-Through-Authentifizierung“ konfiguriert bleibt. Bei Anwendung der Änderung wird im Assistenten die Kennwortsynchronisierung aktiviert. Durch diese Fehlerbehebung wird im Assistenten die Kennwortsynchronisierung nicht mehr aktiviert. 

* Es wurde ein Problem behoben, durch das beim Upgrade von Azure AD Connect ein Fehler mit der Fehlermeldung *Unable to upgrade the Synchronization Service* (Upgrade des Synchronisierungsdiensts nicht möglich) verursacht wurde. Darüber hinaus kann der Synchronisierungsdienst aufgrund des Ereignisfehlers *The service was unable to start because the version of the database is newer than the version of the binaries installed* (Der Dienst kann nicht gestartet werden, da die Version der Datenbank neuer ist als die Version der installierten Binärdateien.) nicht mehr gestartet werden. Das Problem tritt auf, wenn der Administrator, der das Upgrade durchführt, nicht über die Berechtigung „sysadmin“ für den SQL Server verfügt, der von Azure AD Connect verwendet wird. Durch diese Fehlerbehebung muss der Administrator in Azure AD Connect für das Upgrade nur über die Berechtigung „db_owner“ für die ADSync-Datenbank verfügen.

* Es wurde ein Problem mit dem Azure AD Connect-Upgrade behoben, das Kunden betraf, die [Nahtloses einmaliges Anmelden](./how-to-connect-sso.md) aktiviert haben. Nach dem Upgrade von Azure AD Connect wird das nahtlose einmalige Anmelden im Azure AD Connect-Assistenten fälschlicherweise als deaktiviert angezeigt, obwohl das Feature aktiviert und voll funktionsfähig bleibt. Durch diese Fehlerbehebung wird das Feature im Assistenten jetzt korrekt als aktiviert angezeigt.

* Es wurde ein Problem behoben, das dazu führte, dass im Azure AD Connect-Assistenten auf der Seite *Bereit zur Konfiguration* immer die Aufforderung *Quellanker konfigurieren* angezeigt wurde, auch dann, wenn keine Änderungen am Quellanker vorgenommen wurden.

* Beim Durchführen eines manuellen direkten Upgrades von Azure AD Connect muss der Kunde die Anmeldeinformationen des globalen Administrators des entsprechenden Azure AD-Mandanten angeben. Bisher konnten Upgrades auch durchgeführt werden, wenn die Anmeldeinformationen des globalen Administrators zu einem anderen Azure AD-Mandanten gehörten. Obwohl das Upgrade scheinbar erfolgreich abgeschlossen wird, werden bestimmte Konfigurationen mit dem Upgrade nicht korrekt beibehalten. Durch diese Änderung verhindert der Assistent die Fortsetzung des Upgrades, wenn die angegebenen Anmeldeinformationen nicht mit dem Azure AD-Mandanten übereinstimmen.

* Es wurde redundante Logik entfernt, über die der Azure AD Connect Health-Dienst zu Beginn eines manuellen Upgrades unnötigerweise neu gestartet wurde.


#### <a name="new-features-and-improvements"></a>Neue Features und Verbesserungen
* Es wurde Logik hinzugefügt, um die erforderlichen Schritte zum Einrichten von Azure AD Connect mit der Cloud von Microsoft Deutschland zu vereinfachen. Bisher mussten Sie bestimmte Registrierungsschlüssel auf dem Azure AD Connect-Server aktualisieren, damit Azure AD Connect ordnungsgemäß mit der Cloud von Microsoft Deutschland ausgeführt wurde (wie in diesem Artikel beschrieben). Azure AD Connect kann jetzt anhand der beim Setup angegebenen Anmeldeinformationen des globalen Administrators automatisch erkennen, wenn sich Ihr Mandant in der Cloud von Microsoft Deutschland befindet.

### <a name="azure-ad-connect-sync"></a>Azure AD Connect-Synchronisierung
> [!NOTE]
> Hinweis: Der Synchronisierungsdienst verfügt über eine WMI-Schnittstelle, über die Sie Ihren eigenen benutzerdefinierten Scheduler entwickeln können. Diese Schnittstelle ist inzwischen veraltet und wird in zukünftigen Versionen von Azure AD Connect, die nach dem 30. Juni 2018 ausgeliefert werden, entfernt. Kunden, die den Zeitplan für die Synchronisierung anpassen möchten, sollten den [integrierten Scheduler](./how-to-connect-sync-feature-scheduler.md) verwenden.
#### <a name="fixed-issues"></a>Behobene Probleme
* Wenn der Azure AD Connect-Assistent das erforderliche AD Connector-Konto zum Synchronisieren von Änderungen aus dem lokalen Active Directory erstellt, wird dem Konto die zum Lesen von PublicFolder-Objekten erforderliche Berechtigung nicht ordnungsgemäß zugewiesen. Dieses Problem betrifft die Expressinstallation und die benutzerdefinierte Installation. Das Problem wird mit dieser Änderung behoben.

* Es wurde ein Problem behoben, das dazu führte, dass die Problembehandlungsseite des Azure AD Connect-Assistenten für Administratoren, die Windows Server 2016 ausführen, nicht korrekt dargestellt wurde.

#### <a name="new-features-and-improvements"></a>Neue Features und Verbesserungen
* Bei der Problembehandlung der Kennwortsynchronisierung über die Problembehandlungsseite des Azure AD Connect-Assistenten werden jetzt domänenspezifische Statuswerte zurückgegeben.

* Wenn Sie bisher versucht haben, die Kennworthashsynchronisierung zu aktivieren, wurde in Azure AD Connect nicht überprüft, ob das AD Connector-Konto über die erforderlichen Berechtigungen zum Synchronisieren von Kennworthashes aus dem lokalen AD verfügt. Dies wird jetzt im Azure AD Connect-Assistenten überprüft, und Sie werden gewarnt, wenn das AD-Connector-Konto nicht über ausreichende Berechtigungen verfügt.

### <a name="ad-fs-management"></a>AD FS-Verwaltung
#### <a name="fixed-issue"></a>Behobenes Problem
* Es wurde ein Problem bei der Verwendung des Features [ms-DS-ConsistencyGuid als Quellanker](./plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor) behoben. Dieses Problem betrifft Kunden, die *Verbund mit AD FS* als Benutzeranmeldemethode konfiguriert haben. Beim Ausführen der Aufgabe *Quellanker konfigurieren* im Assistenten wird in Azure AD Connect „*ms-DS-ConsistencyGuid“ als Quellattribut für „immutableId“ verwendet. Als Teil dieser Änderung wird in Azure AD Connect versucht, die Anspruchsregeln für „ImmutableId“ in AD FS zu aktualisieren. Bei diesem Schritt traten jedoch Fehler auf, da Azure AD Connect nicht über die erforderlichen Administratoranmeldeinformationen zum Konfigurieren von AD FS verfügte. Durch diese Fehlerbehebung werden Sie in Azure AD Connect jetzt aufgefordert, die Administratoranmeldeinformationen für AD FS einzugeben, wenn Sie die Aufgabe *Quellanker konfigurieren* ausführen.



## <a name="116140"></a>1.1.614.0
Status: 05. September 2017

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="known-issues"></a>Bekannte Probleme
* Es besteht ein bekanntes Problem, durch das beim Upgrade von Azure AD Connect ein Fehler mit der Fehlermeldung *Unable to upgrade the Synchronization Service* (Upgrade des Synchronisierungsdiensts nicht möglich) verursacht wird. Darüber hinaus kann der Synchronisierungsdienst aufgrund des Ereignisfehlers *The service was unable to start because the version of the database is newer than the version of the binaries installed* (Der Dienst kann nicht gestartet werden, da die Version der Datenbank neuer ist als die Version der installierten Binärdateien.) nicht mehr gestartet werden. Das Problem tritt auf, wenn der Administrator, der das Upgrade durchführt, nicht über die Berechtigung „sysadmin“ für den SQL Server verfügt, der von Azure AD Connect verwendet wird. dbo-Berechtigungen sind nicht ausreichend.

* Es besteht ein bekanntes Problem mit dem Azure AD Connect-Upgrade, das Kunden betrifft, die [Nahtloses einmaliges Anmelden](how-to-connect-sso.md) aktiviert haben. Nach dem Aktualisieren von Azure AD Connect wird das Feature im Assistenten als deaktiviert angezeigt, obwohl das Feature aktiviert bleibt. In einem zukünftigen Release wird dieses Problem behoben. Kunden, die wg. dieses Anzeigeproblems besorgt sind, können es manuell beheben, indem sie „Nahtloses einmaliges Anmelden“ im Assistenten aktivieren.

#### <a name="fixed-issues"></a>Behobene Probleme
* Das Problem wurde behoben, aufgrund dessen verhindert wurde, dass Azure AD Connect die Anspruchsregeln im lokalen AD FS aktualisiert, wenn das Feature [ms-DS-ConsistencyGuid als Quellanker](./plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor) aktiviert wird. Dieses Problem tritt auf, wenn Sie versuchen, die Funktion für eine vorhandene Azure AD Connect-Bereitstellung zu aktivieren, bei der AD FS als Anmeldemethode konfiguriert ist. Die Ursache des Problems liegt darin, dass der Assistent nicht zur Eingabe der AD FS-Anmeldeinformationen auffordert, bevor versucht wird, die Anspruchsregeln in AD FS zu aktualisieren.
* Das Problem wurde behoben, aufgrund dessen bei der Azure AD Connect-Installation ein Fehler auftrat, wenn in der lokalen AD-Gesamtstruktur NTLM deaktiviert war. Ursache des Problems ist, dass der Azure AD Connect-Assistent bei der Erstellung der für die Kerberos-Authentifizierung erforderlichen Sicherheitskontexte nicht die vollqualifizierten Anmeldeinformationen angibt. Dies bewirkt, dass bei der Kerberos-Authentifizierung ein Fehler auftritt, und der Azure AD Connect-Assistent auf die Verwendung von NTLM zurückgeht.

### <a name="azure-ad-connect-sync"></a>Azure AD Connect-Synchronisierung
#### <a name="fixed-issues"></a>Behobene Probleme
* Das Problem wurde behoben, dass die neue Synchronisierungsregel nicht erstellt werden kann, wenn das Tag-Attribut nicht gefüllt wird.
* Das Problem wurde behoben, aufgrund dessen Azure AD Connect zur Kennwortsynchronisierung eine Verbindung mit dem lokalen AD unter Verwendung von NTLM herstellte, obwohl Kerberos verfügbar war. Dieses Problem tritt auf, wenn die lokale AD-Topologie über mindestens einen Domänencontroller verfügt, der aus einer Sicherung wiederhergestellt wurde.
* Das Problem wurde behoben, aufgrund dessen die Schritte der vollständigen Synchronisierung unnötigerweise nach dem Upgrade auftraten. Im Allgemeinen ist die Ausführung der Schritte der vollständigen Synchronisierung nach dem Upgrade erforderlich, wenn Änderungen der vordefinierten Synchronisierungsregeln vorliegen. Das Problem trat aufgrund eines Fehlers im Änderungserkennungscode auf, der fälschlicherweise eine Änderung erkannte, wenn er auf einen Synchronisierungsregelausdruck traf, der Zeilenumbruchzeichen enthielt. Zeilenumbruchzeichen werden zur besseren Lesbarkeit in Synchronisierungsregelausdrücke eingefügt.
* Das Problem wurde behoben, aufgrund dessen die Azure AD Connect-Serverinstanz nach dem automatischen Upgrade möglicherweise nicht richtig funktioniert. Dieses Problem betrifft Azure AD Connect-Serverinstanzen mit Version 1.1.443.0 (oder früher). Weitere Informationen zu diesem Problem finden Sie im Artikel [Azure AD Connect is not working correctly after an automatic upgrade](https://support.microsoft.com/help/4038479/azure-ad-connect-is-not-working-correctly-after-an-automatic-upgrade) (Azure AD Connect funktioniert nach einem automatischen Upgrade nicht ordnungsgemäß).
* Das Problem wurde behoben, aufgrund dessen die Ausführung des automatischen Upgrades möglicherweise alle 5 Minuten neu versucht wird, wenn Fehler auftreten. Mit der Behebung wird die Ausführung des automatischen Upgrades mit exponentiellem Backoff neu versucht, wenn Fehler auftreten.
* Ein Problem wurde behoben, bei dem das Kennwortsynchronisierungsereignis 611 im Windows-Anwendungsereignisprotokoll fälschlicherweise als **Informativ** anstatt als **Fehler** angezeigt wurde. Ereignis 611 wird generiert, wenn bei der Kennwortsynchronisierung ein Problem festgestellt wird. 
* Ein Problem im Azure AD Connect-Assistenten wurde behoben, das die Aktivierung der Gruppenrückschreibfunktion ohne Auswahl einer für das Gruppenrückschreiben erforderlichen Organisationseinheit ermöglichte.

#### <a name="new-features-and-improvements"></a>Neue Features und Verbesserungen
* Eine Problembehandlungsaufgabe wurde dem Azure AD Connect-Assistenten unter „Weitere Aufgaben“ hinzugefügt. Kunden können diese Aufgabe zum Behandeln von Problemen im Zusammenhang mit der Synchronisierung von Kennwörtern und dem Erfassen der allgemeinen Diagnose nutzen. In der Zukunft wird die Aufgabe zur Problembehandlung erweitert, sodass sie auch andere, mit der Verzeichnissynchronisierung verbundene Probleme umfasst.
* Azure AD Connect unterstützt jetzt einen neuen, als **Vorhandene Datenbank verwenden** bezeichneten Installationsmodus. Dieser Installationsmodus ermöglicht Kunden, Azure AD Connect zu installieren, das eine vorhandene ADSync-Datenbank angibt. Weitere Informationen zu diesem Feature finden Sie im Artikel [Install Azure AD Connect using an existing ADSync database](how-to-connect-install-existing-database.md) (Installieren von Azure AD Connect mithilfe einer vorhandenen ADSync-Datenbank).
* Zur Sicherheitsverbesserung verwendet Azure AD Connect jetzt standardmäßig TLS 1.2 für die Herstellung der Verbindung mit Azure AD für die Verzeichnissynchronisierung. Bisher war der Standard TLS1.0.
* Wenn der Kennwortsynchronisierungs-Agent von Azure AD Connect startet, versucht er, eine Verbindung mit dem bekannten Azure AD-Endpunkt für die Synchronisierung von Kennwörtern herzustellen. Bei erfolgreicher Verbindung wird er zu einem regionsspezifischen Endpunkt umgeleitet. Vorher speicherte der Kennwortsynchronisierungs-Agent den regionsspezifischen Endpunkt zwischen, bis er neu gestartet wurde. Jetzt löscht der Agent den Cache und unternimmt einen erneuten Versuch mit dem bekannten Endpunkt, falls er ein Verbindungsproblem bei dem regionsspezifischen Endpunkt erkennt. Diese Änderung stellt sicher, dass die Synchronisierung von Kennwörtern ein Failover zu einem anderen regionsspezifischen Endpunkt ausführen kann, wenn der zwischengespeicherte regionsspezifische Endpunkt nicht mehr verfügbar ist.
* Zum Synchronisieren der Änderungen von einer lokalen AD-Gesamtstruktur ist ein AD DS-Konto erforderlich. Sie können entweder (i) das AD DS-Konto selbst erstellen und Azure AD Connect die Anmeldeinformationen angeben, oder (ii) eine Enterprise-Administratoranmeldeinfo eingeben und Azure AD Connect das AD DS-Konto für Sie erstellen lassen. Früher war (i) die Standardoption im Azure AD Connect-Assistenten. Jetzt ist (ii) die Standardoption.

### <a name="azure-ad-connect-health"></a>Azure AD Connect Health

#### <a name="new-features-and-improvements"></a>Neue Features und Verbesserungen
* Unterstützung für Microsoft Azure Government-Cloud und Microsoft Cloud Deutschland wurde hinzugefügt.

### <a name="ad-fs-management"></a>AD FS-Verwaltung
#### <a name="fixed-issues"></a>Behobene Probleme
* Das Cmdlet „Initialize-ADSyncNGCKeysWriteBack“ im AD-Vorbereitungs-PowerShell-Modul wandte die Zugriffsteuerungsliste falsch auf den Geräteregistrierungscontainer an und erbte daher nur vorhandene Berechtigungen.  Dies wurde aktualisiert, sodass das Synchronisierungsdienstkonto über die richtigen Berechtigungen verfügt.

#### <a name="new-features-and-improvements"></a>Neue Features und Verbesserungen
* Die Azure AD Connect-Aufgabe für die Überprüfung der ADFS-Anmeldung wurde aktualisiert, sodass sie Anmeldungen anhand von Microsoft Online überprüft und nicht einfach einen Tokenabruf in ADFS durchführt.
* Beim Einrichten einer neuen ADFS-Farm mit Azure AD Connect wurde die Seite mit der Aufforderung zur Eingabe der ADFS-Anmeldeinformationen verschoben, sodass sie jetzt angezeigt wird, bevor der Benutzer zur Angabe des ADFS- und des WAP-Servers aufgefordert wird.  So kann Azure AD Connect überprüfen, ob das angegebene Konto über die richtigen Berechtigungen verfügt.
* Während des Azure AD Connect-Upgrades tritt kein Upgradefehler mehr auf, wenn ein Fehler bei der Aktualisierung der ADFS-Azure AD-Vertrauensstellung auftritt.  Ist dies der Fall, erhält der Benutzer eine entsprechende Warnmeldung und sollte mit dem Zurücksetzen der Vertrauensstellung über die zusätzliche Azure AD Connect-Aufgabe fortfahren.

### <a name="seamless-single-sign-on"></a>Nahtloses einmaliges Anmelden
#### <a name="fixed-issues"></a>Behobene Probleme
* Ein Problem wurde behoben, aufgrund dessen der Azure AD Connect-Assistent zu einem Fehler zurückkehrte, wenn Sie versuchten, das [Nahtlose einmalige Anmelden](how-to-connect-sso.md) zu aktivieren. Die Fehlermeldung lautet *„Fehler bei Konfiguration des Microsoft Azure AD Connect-Authentifizierungs-Agent“* . Dieses Problem betrifft bestehende Kunden, die manuell die Vorschauversion der Authentifizierung-Agents für [Passthrough-Authentifizierung](how-to-connect-sso.md) anhand der in diesem [Artikel](how-to-connect-pta-upgrade-preview-authentication-agents.md) beschriebenen Schritte aktualisiert hatten.


## <a name="115610"></a>1.1.561.0
Status: 23. Juli 2017

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="fixed-issue"></a>Behobenes Problem

* Es wurde ein Problem behoben, das die Out-of-Box-Synchronisierungsregel „Ausgehend nach AD - User ImmutableId“ entfernt hat:

  * Dieses Problem tritt auf, wenn Azure AD Connect aktualisiert wird oder wenn die Registerkartenoption *Update Synchronization Configuration* (Synchronisierungskonfiguration aktualisieren) im Assistenten von Azure AD Connect verwendet wird, um die Synchronisierungskonfiguration für Azure AD Connect zu aktualisieren.

  * Diese Synchronisierungsregel gilt für Kunden, die das Feature [ms-DS-ConsistencyGuid als Quellanker](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor) aktiviert haben. Dieses Feature wurde mit Version 1.1.524.0 und höher eingeführt. Wenn diese Synchronisierungsregel entfernt wird, kann Azure AD Connect das lokale Attribut „AD ms-DS-ConsistencyGuid“ nicht mehr mit dem Attributwert „ObjectGuid“ füllen. Es verhindert nicht, dass neue Benutzer in Azure AD bereitgestellt werden.

  * Diese Lösung gewährleistet, dass die Synchronisierungsregel während des Upgrades oder einer Konfigurationsänderung nicht mehr entfernt wird, sofern das Feature aktiviert ist. Für bestehende Kunden, die von diesem Problem betroffen waren, gewährleistet die Lösung ebenfalls, dass die Synchronisierungsregel nach der Aktualisierung auf diese Version von Azure AD Connect wieder hinzugefügt wird.

* Es wurde ein Problem behoben, bei dem Out-of-Box-Synchronisierungsregeln einen Rangfolgenwert von weniger als 100 besitzen:

  * Die Rangfolgenwerte von 0 bis 99 sind im Allgemeinen für benutzerdefinierte Synchronisierungsregeln reserviert. Während des Upgrades werden die Rangfolgenwerte für die Out-of-Box-Synchronisierungsregeln aktualisiert, um Änderungen an den Synchronisierungsregeln zu ermöglichen. Aufgrund des Problems konnte Out-of-Box-Synchronisierungsregeln ein Rangfolgenwert von weniger als 100 zugewiesen werden.

  * Die Lösung verhindert, dass das Problem während des Upgrades auftritt. Der Rangfolgenwert für bestehende Kunden, die von diesem Problem betroffen waren, wird jedoch nicht wiederhergestellt. Eine separate Lösung zur Unterstützung bei der Wiederherstellung wird in Zukunft noch bereitgestellt.

* Es wurde ein Problem behoben, bei dem der [Domain and OU Filtering screen (Bildschirm zum Filtern von Domänen und Organisationseinheiten (OEs))](how-to-connect-install-custom.md#domain-and-ou-filtering) im Assistenten für Azure AD Connect die Option *Sync all domains and OUs* (Alle Domänen und Organisationseinheiten synchronisieren) als ausgewählt anzeigt, obwohl das OE-basierte Filtern aktiviert ist.

*   Es wurde ein Problem behoben, bei dem der [Configure Directory Partitions screen (Bildschirm zum Konfigurieren von Verzeichnispartitionen)](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering) im Synchronization Service Manager einen Fehler zurückgibt, wenn die Schaltfläche *Refresh* (Erneuern) angeklickt wird. Die Fehlermeldung lautet *„Beim Aktualisieren der Domänen ist ein Fehler aufgetreten: Objekt des Typs ‚System.Collections.ArrayList‘ konnte nicht in den Typ ‚Microsoft.DirectoryServices.MetadirectoryServices.UI.PropertySheetBase.MaPropertyPages.PartitionObject‘ umgewandelt werden.“* Dieser Fehler tritt auf, wenn eine neue AD-Domäne zu einer bestehenden AD-Gesamtstruktur hinzugefügt wurde und Sie versuchen, Azure AD Connect mit der Schaltfläche „Erneuern“ zu aktualisieren.

#### <a name="new-features-and-improvements"></a>Neue Features und Verbesserungen

* Das [Automatic Upgrade feature (Feature für automatische Upgrades)](how-to-connect-install-automatic-upgrade.md) wurde erweitert, um Kunden zu unterstützen, die folgende Konfigurationen verwenden:
  * Sie haben das Feature „Geräterückschreiben“ aktiviert.
  * Sie haben das Feature „Gruppenrückschreiben“ aktiviert.
  * Die Installation ist keine Express-Einstellung und kein DirSync-Upgrade.
  * Es sind mehr als 100.000 Objekte im Metaverse enthalten.
  * Sie stellen Verbindungen mit mehr als einer Gesamtstruktur her. Beim Express-Setup wird nur eine Verbindung mit einer Gesamtstruktur hergestellt.
  * Das AD-Connector-Konto ist nicht mehr das Standardkonto vom Typ „MSOL_“.
  * Der Server ist auf den Stagingmodus festgelegt.
  * Sie haben das Feature „Benutzerrückschreiben“ aktiviert.

  >[!NOTE]
  >Die Bereichserweiterung des Features für automatische Upgrades betrifft Kunden mit Azure AD Connect Build 1.1.105.0 und höher. Wenn Sie nicht möchten, dass Ihr Azure AD Connect-Server automatisch aktualisiert wird, müssen Sie auf diesem folgendes Cmdlet ausführen: `Set-ADSyncAutoUpgrade -AutoUpgradeState disabled`. Weitere Informationen zum Aktivieren und Deaktivieren von automatischen Upgrades finden Sie im Artikel [Azure AD Connect: Automatisches Upgrade](how-to-connect-install-automatic-upgrade.md).
## <a name="115580"></a>1.1.558.0
Status: Wird nicht veröffentlicht. Änderungen in diesem Build sind in Version 1.1.561.0 enthalten.

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="fixed-issue"></a>Behobenes Problem

* Es wurde ein Problem behoben, das die Out-of-Box-Synchronisierungsregel „Ausgehend nach AD - User ImmutableId“ entfernt hat, wenn die Konfiguration für das OE-basierte Filtern aktualisiert wird. Diese Synchronisierungsregel wird für das Feature [ms-DS-ConsistencyGuid als Quellanker](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor) benötigt.

* Es wurde ein Problem behoben, bei dem der [Domain and OU Filtering screen (Bildschirm zum Filtern von Domänen und Organisationseinheiten (OEs))](how-to-connect-install-custom.md#domain-and-ou-filtering) im Assistenten für Azure AD Connect die Option *Sync all domains and OUs* (Alle Domänen und Organisationseinheiten synchronisieren) als ausgewählt anzeigt, obwohl das OE-basierte Filtern aktiviert ist.

*   Es wurde ein Problem behoben, bei dem der [Configure Directory Partitions screen (Bildschirm zum Konfigurieren von Verzeichnispartitionen)](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering) im Synchronization Service Manager einen Fehler zurückgibt, wenn die Schaltfläche *Refresh* (Erneuern) angeklickt wird. Die Fehlermeldung lautet *„Beim Aktualisieren der Domänen ist ein Fehler aufgetreten: Objekt des Typs ‚System.Collections.ArrayList‘ konnte nicht in den Typ ‚Microsoft.DirectoryServices.MetadirectoryServices.UI.PropertySheetBase.MaPropertyPages.PartitionObject‘ umgewandelt werden.“* Dieser Fehler tritt auf, wenn eine neue AD-Domäne zu einer bestehenden AD-Gesamtstruktur hinzugefügt wurde und Sie versuchen, Azure AD Connect mit der Schaltfläche „Erneuern“ zu aktualisieren.

#### <a name="new-features-and-improvements"></a>Neue Features und Verbesserungen

* Das [Automatic Upgrade feature (Feature für automatische Upgrades)](how-to-connect-install-automatic-upgrade.md) wurde erweitert, um Kunden zu unterstützen, die folgende Konfigurationen verwenden:
  * Sie haben das Feature „Geräterückschreiben“ aktiviert.
  * Sie haben das Feature „Gruppenrückschreiben“ aktiviert.
  * Die Installation ist keine Express-Einstellung und kein DirSync-Upgrade.
  * Es sind mehr als 100.000 Objekte im Metaverse enthalten.
  * Sie stellen Verbindungen mit mehr als einer Gesamtstruktur her. Beim Express-Setup wird nur eine Verbindung mit einer Gesamtstruktur hergestellt.
  * Das AD-Connector-Konto ist nicht mehr das Standardkonto vom Typ „MSOL_“.
  * Der Server ist auf den Stagingmodus festgelegt.
  * Sie haben das Feature „Benutzerrückschreiben“ aktiviert.

  >[!NOTE]
  >Die Bereichserweiterung des Features für automatische Upgrades betrifft Kunden mit Azure AD Connect Build 1.1.105.0 und höher. Wenn Sie nicht möchten, dass Ihr Azure AD Connect-Server automatisch aktualisiert wird, müssen Sie auf diesem folgendes Cmdlet ausführen: `Set-ADSyncAutoUpgrade -AutoUpgradeState disabled`. Weitere Informationen zum Aktivieren und Deaktivieren von automatischen Upgrades finden Sie im Artikel [Azure AD Connect: Automatisches Upgrade](how-to-connect-install-automatic-upgrade.md).
## <a name="115570"></a>1.1.557.0
Status: Juli 2017

>[!NOTE]
>Dieser Build steht Kunden nicht über das Feature zum automatischen Upgrade von Azure AD Connect zur Verfügung.
### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="fixed-issue"></a>Behobenes Problem
* Es wurde ein Problem mit dem Cmdlet „Initialize-ADSyncDomainJoinedComputerSync“ behoben, durch das sich die überprüfte Domäne geändert hat, die auf dem bestehenden Dienstverbindungspunkt-Objekt konfiguriert wurde, obwohl die Domäne noch gültig war. Dieses Problem tritt auf, wenn Ihr Azure AD-Mandant über mehr als eine überprüfte Domäne verfügt, die für die Konfiguration des Dienstverbindungspunkts verwendet werden kann.

#### <a name="new-features-and-improvements"></a>Neue Features und Verbesserungen
* Das Rückschreiben von Kennwörtern ist nun für die Vorschauversion von Microsoft Azure Government-Cloud und Microsoft Cloud Germany verfügbar. Weitere Informationen zur Unterstützung der verschiedenen Dienstinstanzen in Azure AD Connect finden Sie im Artikel [Azure AD Connect: Besondere Überlegungen zu Instanzen](reference-connect-instances.md).

* Das Cmdlet „Initialize-ADSyncDomainJoinedComputerSync“ besitzt jetzt den neuen optionalen Parameter „AzureADDomain“. Durch diesen Parameter können Sie angeben, welche überprüfte Domäne zum Konfigurieren des Dienstverbindungspunkts verwendet werden soll.

### <a name="pass-through-authentication"></a>Passthrough-Authentifizierung

#### <a name="new-features-and-improvements"></a>Neue Features und Verbesserungen
* Der Name des Agenten, der für die Passthrough-Authentifizierung benötigt wird, wurde von *Microsoft Azure AD Application Proxy Connector (Microsoft Azure AD-Anwendungsproxyconnector)* zu *Microsoft Azure AD Connect Authentication Agent (Microsoft Azure AD Connect-Authentifizierungs-Agent)* geändert.

* Das Aktivieren der Passthrough-Authentifizierung aktiviert nicht mehr standardmäßig die Kennworthashsynchronisierung.


## <a name="115530"></a>1.1.553.0
Status: Juni 2017

> [!IMPORTANT]
> Bei diesem Build wurden Änderungen an Schemas und Synchronisierungsregeln vorgenommen. Der Azure AD Connect-Synchronisierungsdienst löst nach dem Upgrade die Schritte „Vollständiger Import“ und „Vollständige Synchronisierung“ aus. Details zu den Änderungen werden nachfolgend beschrieben. Informationen zum vorübergehenden Verzögern der Schritte „Vollständiger Import“ und „Vollständige Synchronisierung“ finden Sie im Artikel [How to defer full synchronization after upgrade (Vorgehensweise: Verzögern der vollständigen Synchronisierung nach einem Upgrade)](how-to-upgrade-previous-version.md#how-to-defer-full-synchronization-after-upgrade).
>
>
### <a name="azure-ad-connect-sync"></a>Azure AD Connect-Synchronisierung

#### <a name="known-issue"></a>Bekanntes Problem
* Es besteht ein Problem, das Kunden betrifft, die [OU-based filtering (OE-basiertes Filtern)](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering) mit der Azure AD Connect-Synchronisierung verwenden. Wenn Sie zu der [Domain and OU Filtering page (Seite zum Filtern von Domänen und Organisationseinheiten)](how-to-connect-install-custom.md#domain-and-ou-filtering) im Assistenten für Azure AD Connect navigieren, wird das folgende Verhalten erwartet:
  * Wenn OE-basiertes Filtern aktiviert ist, wird die Option **Sync selected domains and OUs** („Ausgewählte Domänen und Organisationseinheiten synchronisieren“) ausgewählt.
  * Andernfalls wird die Option **Sync all domains and OUs** („Alle Domänen und Organisationseinheiten synchronisieren“) ausgewählt.

Dabei entsteht das Problem, dass die **Sync all domains and OUs option** (Option „Alle Domänen und Organisationseinheiten synchronisieren“) immer ausgewählt ist, wenn Sie den Assistenten starten.  Dieses Problem tritt auch dann auf, wenn das OE-basierte Filtern zuvor konfiguriert wurde. Stellen Sie vor dem Speichern von Änderungen an der Azure AD Connect-Konfiguration sicher, dass **die Option „Ausgewählte Domänen und Organisationseinheiten synchronisieren“ aktiviert ist** und dass alle Organisationseinheiten, die synchronisiert werden müssen, wieder aktiviert sind. Andernfalls wird das OE-basierte Filtern deaktiviert.

#### <a name="fixed-issues"></a>Behobene Probleme

* Es wurde ein Problem mit dem Rückschreiben von Kennwörtern behoben, das es dem Azure AD-Administrator ermöglichte, das Kennwort eines privilegierten lokalen AD-Benutzerkontos zurückzusetzen. Das Problem tritt auf, wenn Azure AD Connect die Berechtigung „Kennwort zurücksetzen“ durch das privilegierte Konto gewährt wird. Das Problem wird in dieser Version von Azure AD Connect behoben, indem es einem Azure AD-Administrator nicht gestattet wird, das Kennwort eines lokalen privilegierten AD-Benutzerkontos zurückzusetzen, wenn der Administrator nicht der Besitzer dieses Kontos ist. Weitere Informationen finden Sie in der [Security Advisory 4033453 (Sicherheitsempfehlung 4033453)](/security-updates/SecurityAdvisories/2017/4033453).

* Es wurde ein Problem mit dem Feature [ms-DS-ConsistencyGuid als Quellanker](./plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor) behoben, bei dem Azure AD Connect keine Rückschreibung auf das lokale Attribut „ms-DS-ConsistencyGuid“ durchführt. Das Problem tritt auf, wenn Azure AD Connect mehrere lokale AD-Gesamtstrukturen hinzugefügt wurden und die *User identities exist across multiple directories option* (Option „Benutzeridentitäten sind in mehreren Verzeichnissen vorhanden“) ausgewählt ist. Wenn diese Konfiguration verwendet wird, füllen die resultierenden Synchronisierungsregeln das Attribut „sourceAnchorBinary“ nicht in der Metaverse auf. Das Attribut „sourceAnchorBinary“ wird als Quellattribut für das Attribut „ms-DS-ConsistencyGuid“ verwendet. Dadurch wird keine Rückschreibung in das Attribut „msDS-ConsistencyGuid“ ausgeführt. Um das Problem zu beheben wurden die folgenden Synchronisierungsregeln aktualisiert, um zu gewährleisten, dass das Attribut „sourceAnchorBinary“ in der Metaverse immer aufgefüllt ist:
  * Eingehend aus AD: InetOrgPerson AccountEnabled.xml
  * Eingehend aus AD: InetOrgPerson Common.xml
  * Eingehend aus AD: User AccountEnabled.xml
  * Eingehend aus AD: User Common.xml
  * Eingehend aus AD: User Join SOAInAAD.xml

* Zuvor wurde die Synchronisierungsregel „Ausgehend nach AD: User ImmutableId“ dennoch zu Azure AD Connect hinzugefügt, auch wenn das Feature [ms-DS-ConsistencyGuid als Quellanker](./plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor) deaktiviert war. Die Auswirkungen sind unkritisch und verursachen lediglich, dass keine Rückschreibung auf das Attribut „ms-DS-ConsistencyGuid“ erfolgen kann. Es wurde zum Vermeiden von Verwirrung eine Logik hinzugefügt, um zu gewährleisten, dass die Synchronisierungsregel nur hinzugefügt wird, wenn das Feature aktiviert ist.

* Es wurde ein Problem behoben, bei dem die Kennworthashsynchronisierung mit dem Fehlerereignis 611 fehlgeschlagen ist. Dieses Problem tritt auf, wenn mindestens ein Domänencontroller aus dem lokalen AD entfernt wurde. Am Ende jedes Kennwortsynchronisierungszyklus enthält das Cookie zur Synchronisierung, das vom lokalen AD verwendet wird, die Aufruferkennungen der entfernten Domänencontroller mit einem USN-Wert (Updatesequenznummern-Wert) von 0. Der Manager für Kennwortsynchronisierung kann das Cookie zur Synchronisierung, das den USN-Wert von 0 enthält, nicht speichern und schlägt deshalb mit der Fehlermeldung 611 fehl. Während des nächsten Synchronisierungszyklus verwendet der Manager für Kennwortsynchronisierung das letzte gespeicherte Cookie zur Synchronisierung, das keinen USN-Wert von 0 enthält. Dadurch werden dieselben Kennwortänderungen erneut synchronisiert. Durch diese Lösung speichert der Manager für Kennwortsynchronisierung das Cookie zur Synchronisierung korrekt.

* Zuvor hat der Vorgang für automatische Upgrades weiterhin regelmäßig auf Upgrades überprüft, auch wenn automatische Upgrades durch das Cmdlet „Set-ADSyncAutoUpgrade“ deaktiviert wurden und die Berücksichtigung der Deaktivierung basierte auf dem heruntergeladenen Installationsprogramm. Durch diese Lösung überprüft der Vorgang für automatische Upgrades nicht mehr regelmäßig auf Upgrades. Die Lösung wird automatisch angewendet, wenn das Installationsprogramm für Upgrades für diese Version von Azure AD Connect einmal ausgeführt wurde.

#### <a name="new-features-and-improvements"></a>Neue Features und Verbesserungen

* Zuvor war das Feature [ms-DS-ConsistencyGuid als Quellanker](./plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor) nur für neue Bereitstellungen verfügbar. Nun ist es auch für vorhandene Bereitstellungen verfügbar. Dies gilt insbesondere in folgenden Fällen:
  * Starten Sie den Assistenten für Azure AD Connect und klicken Sie die Option *Update Source Anchor* (Quellanker aktualisieren) an, um auf dieses Feature zuzugreifen.
  * Diese Option wird nur in bestehenden Bereitstellungen angezeigt, die „objectGuid“ als Attribut für den Quellanker verwenden.
  * Wenn Sie diese Option konfigurieren, überprüft der Assistent den Status des Attributs „ms-DS-ConsistencyGuid“ in Ihrem lokalen Active Directory. Wenn das Attribut in keinem Benutzerobjekt im Verzeichnis konfiguriert ist, verwendet der Assistent „ms-DS-ConsistencyGuid“ als „sourceAnchor“-Attribut. Wenn das Attribut in einem oder in mehreren Objekten oder Benutzern im Verzeichnis konfiguriert ist, folgert der Assistent daraus, dass das Attribut von anderen Anwendungen verwendet wird und nicht als „sourceAnchor“-Attribut infrage kommt. Deshalb gestattet der Assistent nicht, dass die Änderung des Quellankers fortgesetzt wird. Wenn Sie sicher sind, dass das Attribut nicht von vorhandenen Anwendungen verwendet wird, wenden Sie sich an den Support, um Informationen zum Unterdrücken der Fehlermeldung zu erhalten.

* Speziell für das Attribut **userCertificate** der Geräteobjekte sucht Azure AD Connect nun nach den Zertifikatwerten, die für das [Connecting domain-joined devices to Azure AD for Windows 10 experience (Verbinden von in Domänen eingebunden Geräten mit Azure AD für Windows 10)](../devices/hybrid-azuread-join-plan.md) benötigt werden und filtert den Rest heraus, bevor mit Azure AD synchronisiert wird. Um dieses Verhalten zu ermöglichen, wurde die Out-of-Box-Synchronisierungsregel „Ausgehend nach AAD: Gerät Join SOAInAD“ aktualisiert.

* Azure AD Connect unterstützt jetzt die Rückschreibung des Exchange Online-Attributs **cloudPublicDelegates** auf das lokale AD-Attribut **publicDelegates**. Dadurch wird einem Exchange Online-Postfach ermöglicht, dass Benutzern mit lokalem Exchange-Postfach SendOnBehalfTo-Rechte erteilt werden. Zur Unterstützung dieses Features wurde die neue Out-of-Box-Synchronisierungsregel „Ausgehend nach AD: User Exchange Hybrid PublicDelegates writeback“ hinzugefügt. Diese Synchronisierungsregel wird nur zu Azure AD Connect hinzugefügt, wenn das Feature „Exchange Hybrid“ aktiviert ist.

* Azure AD Connect unterstützt nun die Synchronisierung des Attributs **altRecipient** von Azure AD. Um diese Änderung zu unterstützen wurden folgende Out-of-Box-Synchronisierungsregeln aktualisiert, um den erforderlichen Attributfluss einzuschließen:
  * Ein von AD – Benutzer Exchange
  * Ausgehend nach AAD: User ExchangeOnline

* Das Attribut **cloudSOAExchMailbox** in der Metaverse gibt an, ob ein bestimmter Benutzer über ein Exchange Online-Postfach verfügt oder nicht. Die Definition wurde aktualisiert, sodass zusätzlich „RecipientDisplayTypes“ für Exchange Online hinzugefügt wurde und dadurch auch Postfächer für Anlagen und Konferenzräume. Um diese Änderungen vorzunehmen wurde die Definition des Attributs „cloudSOAExchMailbox“, das unter der Out-of-Box-Synchronisierungsregel „Eingehend von AAD: User Exchange Hybrid“ gefunden werden kann, von Folgendem aktualisiert:

    ```
    CBool(IIF(IsNullOrEmpty([cloudMSExchRecipientDisplayType]),NULL,BitAnd([cloudMSExchRecipientDisplayType],&amp;HFF) = 0))
    ```

    ...auf das Folgende:

    ```
    CBool(
      IIF(IsPresent([cloudMSExchRecipientDisplayType]),(
        IIF([cloudMSExchRecipientDisplayType]=0,True,(
          IIF([cloudMSExchRecipientDisplayType]=2,True,(
            IIF([cloudMSExchRecipientDisplayType]=7,True,(
              IIF([cloudMSExchRecipientDisplayType]=8,True,(
                IIF([cloudMSExchRecipientDisplayType]=10,True,(
                  IIF([cloudMSExchRecipientDisplayType]=16,True,(
                    IIF([cloudMSExchRecipientDisplayType]=17,True,(
                      IIF([cloudMSExchRecipientDisplayType]=18,True,(
                        IIF([cloudMSExchRecipientDisplayType]=1073741824,True,(
                          IF([cloudMSExchRecipientDisplayType]=1073741840,True,False)))))))))))))))))))),False))
    ```

* Der folgende Satz X509Certificate2-kompatibler Funktionen wurde hinzugefügt, damit Ausdrücke für Synchronisierungsregeln erstellt werden können, um die Zertifikatwerte im Attribut „userCertificate“ zu verarbeiten:
  * CertSubject 
  * CertIssuer
  * CertKeyAlgorithm
  * CertSubjectNameDN
  * CertIssuerOid
  * CertNameInfo
  * CertSubjectNameOid
  * CertIssuerDN
  * IsCert
  * CertFriendlyName
  * CertThumbprint
  * CertExtensionOids
  * CertFormat
  * CertNotAfter
  * CertPublicKeyOid 
  * CertSerialNumber
  * CertNotBefore
  * CertPublicKeyParametersOid
  * CertVersion
  * CertSignatureAlgorithmOid
  * Select
  * CertKeyAlgorithmParams
  * CertHashString
  * Hierbei gilt:
  * With

* Folgende Schemaänderungen wurden eingeführt, um es Kunden zu ermöglichen, benutzerdefinierte Synchronisierungsregeln zu erstellen, um „sAMAccountName“, „domainNetBios“ und „domainFQDN“ an Gruppenobjekte und „distinguishedName“ an Benutzerobjekte zu übergeben:

  * Folgende Attribute wurden dem MV-Schema hinzugefügt:
    * Gruppe: AccountName
    * Gruppe: domainNetBios
    * Gruppe: domainFQDN
    * Person: distinguishedName

  * Folgende Attribute wurden dem Azure AD-Connector-Schema hinzugefügt:
    * Gruppe: OnPremisesSamAccountName
    * Gruppe: NetBiosName
    * Gruppe: DnsDomainName
    * User: OnPremisesDistinguishedName

* Das Cmdlet-Skript „ADSyncDomainJoinedComputerSync“ besitzt jetzt den neuen optionalen Parameter „AzureEnvironment“. Der Parameter wird verwendet, um anzugeben, in welcher Region der entsprechende Azure Active Directory-Mandant gehostet wird. Gültige Werte:
  * AzureCloud (Standard)
  * AzureChinaCloud
  * AzureGermanyCloud
  * USGovernment

* Der aktualisierte Synchronisierungsregel-Editor, um „Join“ (statt „Provision“) als Standardwert für den Verknüpfungstyp während der Erstellung von Synchronisierungsregeln zu verwenden.

### <a name="ad-fs-management"></a>AD FS-Verwaltung

#### <a name="issues-fixed"></a>Behobene Fehler

* Folgende URLs sind neue Endpunkte für den WS-Verbund und werden von Azure AD eingeführt, um die Resilienz gegen Authentifizierungsausfälle zu verbessern. Sie werden zur lokalen AD FS-Konfiguration für die Vertrauensstellung der vertrauenden Seite hinzugefügt:
  * https:\//ests.login.microsoftonline.com/login.srf
  * https:\//stamp2.login.microsoftonline.com/login.srf
  * https://ccs.login.microsoftonline.com/login.srf
  * https://ccs-sdf.login.microsoftonline.com/login.srf

* Es wurde ein Fehler behoben, bei dem AD FS einen falschen Anspruchswert für IssuerID generiert hat. Das Problem tritt auf, wenn mehrere überprüfte Domänen im Azure Ad-Mandanten vorhanden sind und die Domänenendung des Attributs „userPrincipalName“, das verwendet wird, um den Anspruch „IssuerID“ zu generieren, mindestens drei Ebenen tief ist (beispielsweise johndoe@us.contoso.com). Das Problem wird behoben, indem der reguläre Ausdruck aktualisiert wird, der von den Anspruchsregeln verwendet wird.

#### <a name="new-features-and-improvements"></a>Neue Features und Verbesserungen
* Zuvor konnte das Feature „ADFS-Zertifikatverwaltung“, das von Azure AD Connect bereitgestellt wird, nur mit ADFS-Farmen verwendet werden, die über Azure AD Connect verwaltet werden. Nun können Sie das Feature auch mit ADFS-Farmen verwenden, die nicht mithilfe von Azure AD Connect verwaltet werden.

## <a name="115240"></a>1.1.524.0
Veröffentlichung: Mai 2017

> [!IMPORTANT]
> Bei diesem Build wurden Änderungen an Schemas und Synchronisierungsregeln vorgenommen. Der Azure AD Connect-Synchronisierungsdienst löst nach dem Upgrade die Schritte „Vollständiger Import“ und „Vollständige Synchronisierung“ aus. Details zu den Änderungen werden nachfolgend beschrieben.
>
>
**Behobene Probleme:**

Azure AD Connect-Synchronisierung

* Das Problem, dass das automatische Upgrade auf dem Azure AD Connect-Server ausgeführt wird, selbst wenn der Kunde die Funktion mit dem Cmdlet „Set-ADSyncAutoUpgrade“ deaktiviert hat, wurde behoben. Durch diese Fehlerbehebung wird im Rahmen des Vorgangs zum automatischen Upgrade auf dem Server weiterhin in regelmäßigen Abständen nach Upgrades gesucht, das heruntergeladene Installationsprogramm berücksichtigt jedoch die Konfiguration des automatischen Upgrades.
* Während des direkten Upgrades von DirSync erstellt Azure AD Connect ein Azure AD-Dienstkonto, das vom Azure AD-Connector zur Synchronisierung mit Azure AD verwendet werden soll. Nachdem das Konto erstellt wurde, wird Azure AD Connect anhand des Kontos bei Azure AD authentifiziert. In einigen Fällen schlägt die Authentifizierung aufgrund vorübergehender Probleme fehl, wodurch wiederum das direkte Upgrade von DirSync mit folgender Fehlermeldung fehlschlägt: *„Bei der Ausführung der Aufgabe AADSTS50034 zur Konfiguration der AAD-Synchronisierung ist ein Fehler aufgetreten: Um sich bei dieser Anwendung anmelden zu können, muss das Konto zum Verzeichnis „xxx.onmicrosoft.com“ hinzugefügt werden.“* Um die Stabilität beim Upgrade von DirSync zu verbessern, wiederholt Azure AD Connect nun den Authentifizierungsschritt.
* Bei Build 443 ist ein Problem aufgetreten, bei dem das direkte Upgrade von DirSync zwar erfolgreich ausgeführt wurde, jedoch die erforderlichen Ausführungsprofile für die Verzeichnissynchronisierung nicht erstellt wurden. Dieser Build von Azure AD Connect enthält eine Logik zur Systemreparatur. Wenn Kunden ein Upgrade auf diesen Build durchführen, erkennt und erstellt Azure AD Connect fehlende Ausführungsprofile.
* Das Problem, dass der Vorgang zur Kennwortsynchronisierung aufgrund der Ereignis-ID 6900 und der Fehlermeldung *„Ein Element mit demselben Schlüssel wurde bereits hinzugefügt“* nicht gestartet werden kann, wurde behoben. Dieses Problem tritt auf, wenn Sie beim Aktualisieren der Konfiguration der OE-Filterung die AD-Konfigurationspartition einschließen. Zur Behebung dieses Problems werden beim Vorgang zur Kennwortsynchronisierung nur noch Kennwortänderungen bei AD-Domänenpartitionen synchronisiert. Partitionen, die nicht zu Domänen gehören (z.B. Konfigurationspartitionen), werden übersprungen.
* Während der Express-Installation erstellt Azure AD Connect ein lokales AD DS-Konto, das vom AD-Connector zur Kommunikation mit dem lokalen AD verwendet werden soll. Vorher wurde das Konto mit dem für das Benutzerkontosteuerungs-Attribut festgelegte Flag „PASSWD_NOTREQD“ erstellt, und ein zufälliges Kennwort wurde für das Konto festgelegt. Nun entfernt Azure AD Connect explizit das Flag „PASSWD_NOTREQD“, nachdem das Kennwort im Konto festgelegt wurde.
* Das Problem, dass das Upgrade von DirSync mit der Fehlermeldung *„Deadlock beim Versuch, eine Anwendungssperre abzurufen“* , wenn das Attribut „mailNickname“ im lokalen AD-Schema gefunden, aber nicht an die Objektklasse „AD-Benutzer“ gebunden wird, wurde behoben.
* Das Problem, dass die Funktion zum Geräterückschreiben automatisch deaktiviert wird, wenn ein Administrator die Konfiguration der Azure AD Connect-Synchronisierung mithilfe des Azure AD Connect-Assistenten aktualisiert, wurde behoben. Dieses Problem tritt auf, wenn der Assistent die Voraussetzungen für die vorhandene Konfiguration des Geräterückschreibens im lokalen AD durchführt und die Überprüfung fehlschlägt. Das Problem lässt sich beheben, indem die Überprüfung übersprungen wird, wenn das Geräterückschreiben vorher bereits aktiviert wurde.
* Um die OE-Filterung zu konfigurieren, können Sie entweder den Azure AD Connect-Assistenten oder den Synchronization Service Manager verwenden. Wenn Sie vorher die OE-Filterung mithilfe des Azure AD Connect-Assistenten konfiguriert haben, wurden neue erstellte Organisationseinheiten (OEs) zur Verzeichnissynchronisierung im Anschluss eingeschlossen. Wenn neue Organisationseinheiten nicht eingeschlossen werden sollten, musste die OE-Filterung mithilfe des Synchronization Service Manager konfiguriert werden. Nun können Sie dasselbe Verhalten mit dem Azure AD Connect-Assistenten erzielen.
* Das Problem, dass gespeicherte, von Azure AD Connect erforderliche Verfahren anstelle des dbo-Schemas unter dem Schema des Administrators, der die Installation durchführt, erstellt werden, wurde behoben.
* Das Problem, dass das von Azure AD zurückgegebene Attribut „TrackingId“ in den Azure AD Connect-Serverereignisprotokollen ausgelassen wird, wurde behoben. Das Problem tritt auf, wenn Azure AD Connect eine Umleitungsmeldung von Azure AD erhält und keine Verbindung zum bereitgestellten Endpunkt herstellen kann. Das Attribut „TrackingId“ wird von Supporttechnikern zur Korrelation mit serverseitigen Protokollen bei der Problembehandlung verwendet.
* Wenn von Azure AD der Fehler „LargeObject“ an Azure AD Connect ausgegeben wird, generiert Azure AD Connect ein Ereignis mit der EventID 6941 und der Meldung *„Das bereitgestellte Objekt ist zu groß. Verringern Sie die Anzahl der Attributwerte für dieses Objekt.“* . Gleichzeitig generiert Azure AD Connect auch ein irreführendes Ereignis mit der Ereignis-ID 6900 und der Meldung *„Microsoft.Online.Coexistence.ProvisionRetryException: Kommunikation mit dem Microsoft Azure Active Directory-Dienst nicht möglich.“* Um Verwirrungen zu vermeiden, generiert Azure AD Connect nicht mehr das letztere Ereignis, wenn die Fehlermeldung „LargeObject“ empfangen wird.
* Das Problem, dass der Synchronization Service Manager bei dem Versuch, die Konfiguration des generischen LDAP-Connectors zu aktualisieren, nicht mehr reagiert, wurde behoben.

**Neue Features/Verbesserungen:**

Azure AD Connect-Synchronisierung
* Änderungen an Synchronisierungsregeln – Folgende Änderungen an Synchronisierungsregeln wurden implementiert:
  * Der standardmäßige Synchronisierungsregelsatz, der festlegt, dass die Attribute **userCertificate** und **userSMIMECertificate** bei mehr als 15 Werten nicht exportiert werden, wurde aktualisiert.
  * Die AD-Attribute **employeeID** und **msExchBypassModerationLink** sind nun im standardmäßigen Synchronisierungsregelsatz enthalten.
  * Das AD-Attribut **photo** wurde aus dem standardmäßigen Synchronisierungsregelsatz entfernt.
  * Das Attribut **preferredDataLocation** wurde zum Metaverse- und Azure AD-Connectorschema hinzugefügt. Kunden, die beide Attribute in Azure AD aktualisieren möchten, können hierfür benutzerdefinierte Synchronisierungsregeln implementieren. 
  * Das Attribut **userType** wurde zum Metaverse- und Azure AD-Connectorschema hinzugefügt. Kunden, die beide Attribute in Azure AD aktualisieren möchten, können hierfür benutzerdefinierte Synchronisierungsregeln implementieren.

* Azure AD Connect ermöglicht jetzt automatisch die Verwendung des Attributs „ConsistencyGuid“ als Quellankerattribut für lokale AD-Objekte. Weiterhin füllt Azure AD Connect das Attribut „ConsistencyGuid“ mit dem Wert des Attributs „objectGuid“ auf, wenn es leer ist. Diese Funktion gilt nur für neue Bereitstellungen. Weitere Informationen zu dieser Funktion finden Sie im Artikelabschnitt [Azure AD Connect: Entwurfskonzepte – Verwendung von „ms-DS-ConsistencyGuid“ als „sourceAnchor“](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor).
* Um Probleme in Bezug auf die Kennworthashsynchronisierung zu diagnostizieren, wurde das neue Problembehandlungs-Cmdlet „Invoke-ADSyncDiagnostics“ hinzugefügt. Informationen zur Verwendung des Cmdlets finden Sie im Artikel [Troubleshoot password hash synchronization with Azure AD Connect sync](tshoot-connect-password-hash-synchronization.md) (Problembehandlung: Kennworthashsynchronisierung mit der Azure AD Connect-Synchronisierung).
* Azure AD Connect unterstützt nun die Synchronisierung von Objekten von E-Mail-aktivierten öffentlichen Ordnern aus dem lokalen AD mit Azure AD. Die Funktion kann mit dem Azure AD Connect-Assistenten unter „Optionale Funktionen“ aktiviert werden. Weitere Informationen zu diesem Feature finden Sie im Artikel [Office 365 Directory Based Edge Blocking support for on-premises Mail Enabled Public Folders (Unterstützung für verzeichnisbasierte Edge-Blockierung in Office 365 für lokale, E-Mail-aktivierte öffentliche Ordner)](https://techcommunity.microsoft.com/t5/exchange/office-365-directory-based-edge-blocking-support-for-on-premises/m-p/74218).
* Azure AD Connect erfordert ein AD DS-Konto, um lokal von AD zu synchronisieren. Zuvor konnten Sie bei der Installation von Azure AD Connect im Express-Modus die Anmeldeinformationen eines Unternehmensadministratorkontos angeben, mit denen Azure AD Connect dann das erforderliche AD DS-Konto erstellt hat. Allerdings mussten Sie für eine benutzerdefinierte Installationen und beim Hinzufügen von Gesamtstrukturen zu einer vorhandenen Bereitstellung stattdessen das AD DS-Konto bereitstellen. Nun haben Sie die Möglichkeit, die Anmeldeinformationen für das Konto eines Unternehmensadministrators auch während einer benutzerdefinierten Installation bereitzustellen und Azure AD Connect die Erstellung des erforderlichen AD DS-Kontos zu überlassen.
* Azure AD Connect unterstützt nun SQL AOA. Vor der Installation von Azure AD Connect müssen Sie SQL AOA aktivieren. Während der Installation erkennt Azure AD Connect, ob die bereitgestellte SQL-Instanz für SQL AOA aktiviert ist. Wenn SQL AOA aktiviert ist, prüft Azure AD Connect darüber hinaus, ob SQL AOA für die Verwendung synchroner oder asynchroner Replikationen konfiguriert ist. Bei der Einrichtung des Verfügbarkeitsgruppenlisteners wird empfohlen, die Eigenschaft „RegisterAllProvidersIP“ auf 0 festzulegen. Dies wird empfohlen, weil Azure AD Connect Verbindungen zu SQL derzeit über den SQL Native Client herstellt und der SQL Native Client die Verwendung der Eigenschaft „MultiSubNetFailover“ nicht unterstützt.
* Wenn Sie „LocalDB“ als Datenbank für den Azure AD Connect-Server verwenden und das Größenlimit von 10 GB erreicht haben, wird der Synchronisierungsdienst nicht mehr gestartet. Vorher mussten Sie den Vorgang „ShrinkDatabase“ in LocalDB durchführen, um genügend Datenbankspeicherplatz zum Starten des Synchronisierungsdiensts freizugeben. Anschließend konnten Sie mithilfe des Synchronization Service Manager den Ausführungsverlauf löschen, um weiteren Datenbankspeicherplatz freizugeben. Nun können Sie das Cmdlet „Start-ADSyncPurgeRunHistory“ verwenden, um zur Freigabe von Datenbankspeicherplatz Daten zum Ausführungsverlauf aus LocalDB zu löschen. Dieses Cmdlet unterstützt darüber hinaus einen Offlinemodus (durch Festlegen des Parameters „-offline“), der verwendet werden kann, wenn der Synchronisierungsdienst nicht ausgeführt wird. Hinweis: Der Offlinemodus kann nur verwendet werden, wenn der Synchronisierungsdienst nicht ausgeführt und die Datenbank „LocalDB“ verwendet wird.
* Um den erforderlichen Speicherplatzbedarf zu reduzieren, komprimiert Azure AD Connect nun Details zu Synchronisierungsfehlern, bevor diese in LocalDB-/SQL-Datenbanken gespeichert werden. Bei der Durchführung eines Upgrades von einer älteren Azure AD Connect-Version auf diese Version führt Azure AD Connect eine einmalige Komprimierung von Details zu bestehenden Synchronisierungsfehlern durch.
* Vorher mussten Sie nach der Aktualisierung der OE-Filterung den Schritt „Vollständiger Import“ manuell ausführen, um sicherzustellen, dass vorhandene Objekte ordnungsgemäß bei aus der Verzeichnissynchronisierung ein- bzw. ausgeschlossen werden. Azure AD Connect löst den Schritt „Vollständiger Import“ nun automatisch während des nächsten Synchronisierungszyklus aus. Darüber hinaus wird „Vollständiger Import“ nur auf die von dem Update betroffenen AD-Connectors angewendet. Hinweis: Diese Verbesserung gilt nur für Updates zur OE-Filterung, die mit dem Azure AD Connect-Assistenten durchgeführt werden. Sie gilt nicht für Updates zur OE-Filterung, die mit dem Synchronization Service Manager durchgeführt wurden.
* Vorher unterstützte die gruppenbasierte Filterung nur Benutzer-, Gruppen- und Kontaktobjekte. Nun unterstützt die gruppenbasierte Filterung auch Computerobjekte.
* Vorher konnten Sie Daten aus dem Connectorbereich löschen, ohne den Azure AD Connect-Synchronisierungsplaner deaktivieren zu müssen. Nun blockiert der Synchronization Service Manager die Löschung von Daten aus dem Connectorbereich, wenn er erkennt, dass der Planer aktiviert ist. Darüber hinaus wird eine Warnung zurückgegeben, um Kunden über einen potenziellen Datenverlust zu informieren, wenn Daten aus dem Connectorbereich gelöscht werden.
* Vorher mussten Sie die PowerShell-Aufzeichnung deaktivieren, damit der Azure AD Connect-Assistent korrekt ausgeführt wird. Dieses Problem wurde teilweise behoben. Sie können die PowerShell-Aufzeichnung aktivieren, wenn Sie den Azure AD Connect-Assistenten zur Verwaltung der Synchronisierungskonfiguration verwenden. Sie müssen die PowerShell-Aufzeichnung deaktivieren, wenn Sie den Azure AD Connect-Assistenten zur Verwaltung der AD FS-Konfiguration verwenden.



## <a name="114860"></a>1.1.486.0
Veröffentlichung: April 2017

**Behobene Probleme:**
* Das Problem, dass Azure AD Connect in einer lokalisierten Version von Windows Server nicht erfolgreich installiert wird, wurde behoben.

## <a name="114840"></a>1.1.484.0
Veröffentlichung: April 2017

**Bekannte Probleme:**

* Diese Version von Azure AD Connect wird nicht erfolgreich installiert, wenn alle der folgenden Bedingungen zutreffen:
   1. Sie führen ein direktes Upgrade von DirSync oder eine Neuinstallation von Azure AD Connect aus.
   2. Sie verwenden eine lokalisierte Version von Windows Server, wobei der Name der integrierten Administratorgruppe auf dem Server nicht „Administratoren“ lautet.
   3. Sie verwenden die mit Azure AD Connect installierte SQL Server 2012 Express LocalDB-Standardinstanz, statt eine eigene vollständige Version von SQL Server bereitzustellen.

**Behobene Probleme:**

Azure AD Connect-Synchronisierung
* Das Problem, dass der Synchronisierungsplaner den gesamten Synchronisierungsschritt überspringt, wenn bei einem oder mehreren Connectors das Ausführungsprofil für diesen Synchronisierungsschritt fehlt, wurde behoben. Das Problem kann beispielsweise auftreten, wenn Sie mit Synchronization Service Manager manuell einen Connector hinzugefügt haben, ohne für diesen das Ausführungsprofil „Deltaimport“ zu erstellen. Durch diese Korrektur wird sichergestellt, dass der Synchronisierungsplaner „Deltaimport“ für andere Connectors weiterhin ausführt.
* Das Problem, dass der Synchronisierungsdienst die Verarbeitung eines Ausführungsprofils sofort beendet, wenn bei einem der Ausführungsschritte ein Problem auftritt, wurde behoben. Mit dieser Korrektur wird sichergestellt, dass der Synchronisierungsdienst diesen Ausführungsschritt überspringt und mit der Verarbeitung der restlichen Schritte fortfährt. Beispiel: Sie verfügen für den AD-Connector über das Ausführungsprofil „Deltaimport“ mit mehreren Ausführungsschritten (einer für jede lokale AD-Domäne). Der Synchronisierungsdienst führt „Deltaimport“ für die anderen AD-Domänen aus, selbst wenn bei einer von ihnen Netzwerkverbindungsprobleme auftreten.
* Das Problem, aufgrund dessen das Update des Azure AD-Connectors während des automatischen Upgrades übersprungen wird, wurde behoben.
* Das Problem, aufgrund dessen Azure AD Connect während des Setups falsch bestimmt, ob der Server ein Domänencontroller ist, sodass das DirSync-Upgrade fehlschlägt, wurde behoben.
* Das Problem, aufgrund dessen beim direkten DirSync-Upgrade kein Ausführungsprofil für den Azure AD-Connector erstellt wird, wurde behoben.
* Das Problem, dass beim Konfigurieren des generischen LDAP-Connectors die Benutzeroberfläche von Synchronization Service Manager nicht mehr reagiert, wurde behoben.

AD FS-Verwaltung
* Das Problem, dass der Azure AD Connect-Assistent nach dem Verschieben des primären AD FS-Knotens auf einen anderen Server fehlschlägt, wurde behoben.

Desktop-SSO
* Das Problem beim Azure AD Connect-Assistenten, dass Desktop-SSO im Anmeldebildschirm nicht aktiviert werden kann, wenn während der Neuinstallation „Kennwortsynchronisierung“ als Anmeldeoption gewählt wurde, wurde behoben.

**Neue Features/Verbesserungen:**

Azure AD Connect-Synchronisierung
* Die Azure AD Connect-Synchronisierung unterstützt jetzt die Verwendung von virtuellen Dienstkonten, verwalteten Dienstkonten und gruppenverwalteten Dienstkonten. Dies gilt nur für die Neuinstallation von Azure AD Connect. Beim Installieren von Azure AD Connect:
    * Der Azure AD Connect-Assistent erstellt standardmäßig ein virtuelles Dienstkonto und verwendet dieses als Dienstkonto.
    * Bei der Installation auf einem Domänencontroller greift Azure AD Connect auf das frühere Verhalten zurück. Das heißt, es wird ein Domänenbenutzerkonto erstellt, und dieses wird als Dienstkonto verwendet.
    * Sie können das Standardverhalten überschreiben, indem Sie eines der folgenden Konten angeben:
      * Ein gruppenverwaltetes Dienstkonto
      * Ein verwaltetes Dienstkonto
      * Ein Domänenbenutzerkonto
      * Ein lokales Benutzerkonto
* Wenn früher ein Upgrade auf einen neuen Build von Azure AD Connect mit Updateänderungen für Connectors oder mit Änderungen von Synchronisierungsregeln durchgeführt wurde, wurde von Azure AD Connect ein vollständiger Synchronisierungszyklus ausgelöst. Jetzt löst Azure AD Connect den Schritt „Vollständiger Import“ nur für Connectors mit Updateänderungen und den Schritt „Vollständige Synchronisierung“ nur für Connectors mit Änderungen von Synchronisierungsregeln aus.
* Früher galt der Exportlöschschwellenwert nur für Exporte, die über den Synchronisierungsplaner ausgelöst werden. Jetzt umfasst das Feature auch Exporte, die vom Kunden mithilfe von Synchronization Service Manager manuell ausgelöst werden.
* Eine Dienstkonfiguration im Azure AD-Mandanten gibt an, ob die Kennwortsynchronisierung für den Mandanten aktiviert ist. Früher konnte es leicht zu einer falschen Dienstkonfiguration durch Azure AD Connect kommen, wenn ein aktiver Server und ein Stagingserver vorhanden waren. Jetzt versucht Azure AD Connect, die Dienstkonfiguration nur entsprechend dem aktiven Azure AD Connect-Server vorzunehmen.
* Der Azure AD Connect-Assistent erkennt jetzt, wenn im lokalen AD der AD-Papierkorb nicht aktiviert ist, und gibt eine entsprechende Warnung aus.
* Früher trat beim Export nach Azure AD eine Zeitüberschreitung auf, und der Export schlug fehl, wenn die Gesamtgröße der Objekte im Batch einen bestimmten Schwellenwert überschritt. Jetzt versucht der Synchronisierungsdienst, die Objekte in einzelnen kleineren Batches erneut zu senden, wenn das Problem auftritt.
* Die Anwendung Synchronization Service Key Management wurde aus dem Windows-Startmenü entfernt. Die Verwaltung der Verschlüsselungsschlüssel wird weiterhin über die Befehlszeilenschnittstelle mithilfe von „miiskmu.exe“ unterstützt. Informationen zum Verwalten von Verschlüsselungsschlüsseln finden Sie im Artikel [Abandoning the Azure AD Connect Sync encryption key](./how-to-connect-sync-change-serviceacct-pass.md#abandoning-the-adsync-service-account-encryption-key) (Verwerfen des Verschlüsselungsschlüssels der Azure AD Connect-Synchronisierung) (in englischer Sprache).
* Wenn Sie früher das Kennwort für das Dienstkonto der Azure AD Connect-Synchronisierung geändert heben, konnte der Synchronisierungsdienst nicht ordnungsgemäß gestartet werden, bis der Verschlüsselungsschlüssel verworfen und das Kennwort für das Dienstkonto der Azure AD Connect-Synchronisierung erneut initialisiert wurde. Jetzt ist dieser Prozess nicht mehr erforderlich.

Desktop-SSO

* Für den Azure AD Connect-Assistenten muss beim Konfigurieren von Passthrough-Authentifizierung und Desktop-SSO nicht mehr Port 9090 im Netzwerk geöffnet werden. Es ist lediglich Port 443 erforderlich.

## <a name="114430"></a>1.1.443.0
Veröffentlichung: 17. März 2017

**Behobene Probleme:**

Azure AD Connect-Synchronisierung
* Ein Problem wurde behoben, durch das der Azure AD Connect-Assistent fehlschlägt, wenn der Anzeigename des Azure AD-Connectors nicht die anfängliche dem Azure AD-Mandanten zugewiesene onmicrosoft.com-Domäne enthält.
* Ein Problem wurde behoben, durch das der Azure AD Connect-Assistent beim Herstellen einer Verbindung mit der SQL-Datenbank fehlschlägt, wenn das Kennwort des Sync-Dienstkonto Sonderzeichen wie z. B. Apostrophe, Doppelpunkte und Leerzeichen enthält.
* Ein Problem wurde behoben, durch das der Fehler „The image has an anchor that is different than the image“ (Das Image weist einen Anker auf, der vom Image abweicht) auf einem Azure AD Connect-Server im Stagingmodus ausgegeben wird, wenn ein lokales AD-Objekt temporär vom Synchronisieren ausgeschlossen und dann wieder in die Synchronisierung eingeschlossen wird.
* Ein Problem wurde behoben, durch das der Fehler „The object located by DN is a phantom“ (Das Objekt in DN ist ein Phantom) auf einem Azure AD Connect-Server im Stagingmodus ausgegeben wird, wenn ein lokales AD-Objekt temporär vom Synchronisieren ausgeschlossen und dann wieder in die Synchronisierung eingeschlossen wird.

AD FS-Verwaltung
* Ein Problem wurde behoben, durch das der Azure AD Connect-Assistent die AD FS-Konfiguration nicht aktualisiert und nicht die richtigen Ansprüche auf die Vertrauensstellung der vertrauenden Seite festlegt, nachdem die alternative Anmelde-ID konfiguriert wurde.
* Ein Problem wurde behoben, durch das der Azure AD Connect-Assistent AD FS-Server nicht ordnungsgemäß verarbeiten kann, wenn deren Dienstkonten das Format „userPrincipalName“ statt „sAMAccountName“ verwenden.

Passthrough-Authentifizierung
* Ein Problem wurde behoben, durch das der Azure AD Connect-Assistent fehlschlägt, wenn die Pass-Through-Authentifizierung ausgewählt wird, aber die Registrierung des Connectors fehlschlägt.
* Ein Problem wurde behoben, durch das der Azure AD Connect-Assistent Überprüfungen der gewählten Anmeldemethode umgeht, wenn Desktop SSO aktiviert ist.

Kennwortzurücksetzung
* Ein Fehler wurde behoben, der dazu führte, dass der Azure AD Connect-Server nicht versuchte, die Verbindung wiederherzustellen, wenn die Verbindung durch eine Firewall oder einen Proxy beendet wurde.

**Neue Features/Verbesserungen:**

Azure AD Connect-Synchronisierung
* Das Get-ADSyncScheduler-Cmdlet gibt jetzt eine neue boolesche Eigenschaft namens „SyncCycleInProgress“ zurück. Lautet der zurückgegebene Wert „true“, wird ein geplanter Synchronisierungszyklus bearbeitet.
* Der Zielordner zum Speichern der Installations- und Setupprotokolle von Azure AD Connect wurde von %localappdata%\AADConnect zu %programdata%\AADConnect verschoben, um den Zugriff auf die Protokolldateien zu vereinfachen.

AD FS-Verwaltung
* Die Aktualisierung des TLS/SSL-Zertifikats für AD FS-Farm wird jetzt unterstützt.
* Die Unterstützung für die Verwaltung von AD FS 2016 wird jetzt unterstützt.
* Sie können jetzt bei der Installation von AD FS vorhandene gruppenverwaltete Dienstkonten angeben.
* Sie können jetzt SHA-256 als Signaturhashalgorithmus für die Azure AD-Vertrauensstellung der vertrauenden Seite konfigurieren.

Kennwortzurücksetzung
* Es wurden Verbesserungen vorgenommen, damit das Produkt auch in Umgebungen mit strengeren Firewallregeln ausgeführt werden kann.
* Verbesserte Zuverlässigkeit der Verbindung mit Azure Service Bus

## <a name="113800"></a>1.1.380.0
Veröffentlichung: Dezember 2016

**Behobenes Problem:**

* Das Problem der fehlenden Anspruchsregel „issuerid“ für Active Directory-Verbunddienste (AD FS) in diesem Build wurde behoben.

>[!NOTE]
>Dieser Build steht Kunden nicht über das Feature zum automatischen Upgrade von Azure AD Connect zur Verfügung.
## <a name="113710"></a>1.1.371.0
Veröffentlichung: Dezember 2016

**Bekanntes Problem:**

* Die Anspruchsregel „issuerid“ für AD FS fehlt in diesem Build. Die Anspruchsregel „issuerid“ist erforderlich, wenn Sie mehrere Domänen mit Azure Active Directory (Azure AD) in einem Verbund zusammenfassen. Wenn Sie Azure AD Connect zum Verwalten Ihrer lokalen AD FS-Bereitstellung verwenden, wird durch ein Upgrade auf diesen Build die vorhandene Anspruchsregel „issuerid“ aus Ihrer AD FS-Konfiguration entfernt. Sie können dieses Problem umgehen, indem Sie die Anspruchsregel „issuerid“ nach der Installation bzw. dem Upgrade hinzufügen. Informationen zum Hinzufügen der Anspruchsregel „issuerid“ finden Sie in diesem Artikel unter [Unterstützung mehrerer Domänen für den Verbund mit Azure AD](how-to-connect-install-multiple-domains.md).

**Behobenes Problem:**

* Bei der Installation oder Aktualisierung von Azure AD Connect tritt ein Fehler auf, wenn Port 9090 nicht für ausgehende Verbindungen geöffnet ist.

>[!NOTE]
>Dieser Build steht Kunden nicht über das Feature zum automatischen Upgrade von Azure AD Connect zur Verfügung.
## <a name="113700"></a>1.1.370.0
Veröffentlichung: Dezember 2016

**Bekannte Probleme:**

* Die Anspruchsregel „issuerid“ für AD FS fehlt in diesem Build. Die Anspruchsregel issuerid ist erforderlich, wenn Sie mehrere Domänen mit Azure AD in einem Verbund zusammenfassen. Wenn Sie Azure AD Connect zum Verwalten Ihrer lokalen AD FS-Bereitstellung verwenden, wird durch ein Upgrade auf diesen Build die vorhandene Anspruchsregel „issuerid“ aus Ihrer AD FS-Konfiguration entfernt. Sie können dieses Problem umgehen, indem Sie die Anspruchsregel „issuerid“ nach der Installation bzw. dem Upgrade hinzufügen. Informationen zum Hinzufügen der Anspruchsregel issuerid finden Sie in diesem Artikel unter [Unterstützung mehrerer Domänen für den Verbund mit Azure AD](how-to-connect-install-multiple-domains.md).
* Port 9090 muss für ausgehende Verbindungen geöffnet werden, um die Installation abzuschließen.

**Neue Features:**

* Passthrough-Authentifizierung (Vorschau)

>[!NOTE]
>Dieser Build steht Kunden nicht über das Feature zum automatischen Upgrade von Azure AD Connect zur Verfügung.
## <a name="113430"></a>1.1.343.0
Veröffentlichung: November 2016

**Bekanntes Problem:**

* Die Anspruchsregel „issuerid“ für AD FS fehlt in diesem Build. Die Anspruchsregel issuerid ist erforderlich, wenn Sie mehrere Domänen mit Azure AD in einem Verbund zusammenfassen. Wenn Sie Azure AD Connect zum Verwalten Ihrer lokalen AD FS-Bereitstellung verwenden, wird durch ein Upgrade auf diesen Build die vorhandene Anspruchsregel „issuerid“ aus Ihrer AD FS-Konfiguration entfernt. Sie können dieses Problem umgehen, indem Sie die Anspruchsregel „issuerid“ nach der Installation bzw. dem Upgrade hinzufügen. Informationen zum Hinzufügen der Anspruchsregel issuerid finden Sie in diesem Artikel unter [Unterstützung mehrerer Domänen für den Verbund mit Azure AD](how-to-connect-install-multiple-domains.md).

**Behobene Probleme:**

* Manchmal tritt beim Installieren von Azure AD Connect ein Fehler auf, da kein lokales Dienstkonto erstellt werden kann, dessen Kennwort die von der Kennwortrichtlinie der Organisation angegebenen Komplexitätsanforderungen erfüllt.
* Behebung eines Fehlers, bei dem Verknüpfungsregeln nicht neu ausgewertet werden, wenn sich ein Objekt im Connectorbereich gleichzeitig für eine Verknüpfungsregel im Bereich und für eine andere Regel außerhalb des Bereichs befindet. Dies kann geschehen, wenn Sie über zwei oder mehr Verknüpfungsregeln verfügen, deren Verknüpfungsbedingungen sich gegenseitig ausschließen.
* Behebung eines Problems, bei dem Synchronisierungsregeln für eingehenden Datenverkehr (von Azure AD), die keine Verknüpfungsregeln enthalten, nicht verarbeitet werden, wenn sie niedrigere Rangfolgenwerte aufweisen als Regeln mit Verknüpfungsregeln.

**Verbesserungen:**

* Unterstützung für die Installation von Azure AD Connect unter Windows Server 2016 Standard oder höher wurde hinzugefügt.
* Unterstützung für die Verwendung von SQL Server 2016 als Remotedatenbank für Azure AD Connect wurde hinzugefügt.

## <a name="112810"></a>1.1.281.0
Veröffentlichung: August 2016

**Behobene Probleme:**

* Änderungen am Synchronisierungsintervall werden erst nach Abschluss des nächsten Synchronisierungszyklus wirksam.
* Der Azure AD Connect-Assistent akzeptiert keine Azure AD-Konten, deren Benutzernamen mit einem Unterstrich (\_) beginnen.
* Der Azure AD Connect-Assistent kann das angegebene Azure AD-Konto nicht authentifizieren, wenn das Kontokennwort zu viele Sonderzeichen enthält. Die Fehlermeldung „Die Anmeldeinformationen konnten nicht gefunden werden. Ein unerwarteter Fehler ist aufgetreten.“ wird zurückgegeben.
* Durch Deinstallieren des Stagingservers wird die Kennwortsynchronisierung im Azure AD-Mandanten deaktiviert, sodass bei der Kennwortsynchronisierung mit dem aktiven Server ein Fehler auftritt.
* Die Kennwortsynchronisierung ist in seltenen Fällen nicht erfolgreich, wenn für den Benutzer kein Kennworthash gespeichert ist.
* Wenn der Azure AD Connect-Server für den Stagingmodus aktiviert wird, wird das Kennwortrückschreiben nicht vorübergehend deaktiviert.
* Wenn sich der Server im Stagingmodus befindet, zeigt der Azure AD Connect-Assistent nicht die tatsächliche Konfiguration von Kennwortsynchronisierung und Kennwortrückschreiben. Die Konfiguration wird immer als deaktiviert angezeigt.
* Wenn sich der Server im Stagingmodus befindet, werden Änderungen an der Konfiguration von Kennwortsynchronisierung und Kennwortrückschreiben vom Azure AD Connect-Assistenten nicht beibehalten.

**Verbesserungen:**

* Das Cmdlet „Start-ADSyncSyncCycle“ wurde aktualisiert und gibt nun an, ob ein neuer Synchronisierungszyklus gestartet werden kann.
* Mit dem hinzugefügten Cmdlet „Stop-ADSyncSyncCycle“ können der derzeit ausgeführte Synchronisierungszyklus und -vorgang beendet werden.
* Mit dem hinzugefügten Cmdlet „Stop-ADSyncScheduler“ kann der derzeit ausgeführte Synchronisierungszyklus bzw. -vorgang beendet werden.
* Im Azure AD Connect-Assistenten kann nun beim Konfigurieren von [Verzeichniserweiterungen](how-to-connect-sync-feature-directory-extensions.md) das Azure AD-Attribut des Typs „Teletex-Zeichenfolge“ ausgewählt werden.

## <a name="111890"></a>1.1.189.0
Veröffentlichung: Juni 2016

**Behobene Probleme und Verbesserungen:**

* Azure AD Connect kann jetzt auf einem FIPS-kompatiblen Server installiert werden.
  * Informationen zur Kennwortsynchronisierung finden Sie unter [Password hash synchronization and FIPS](how-to-connect-password-hash-synchronization.md#password-hash-synchronization-and-fips) (Kennworthashsynchronisierung und FIPS).
* Ein Problem, bei dem ein NetBIOS-Name im FQDN im Active Directory Connector nicht aufgelöst werden konnte, wurde behoben.

## <a name="111800"></a>1.1.180.0
Veröffentlichung: Mai 2016

**Neue Features:**

* Warnungen und Unterstützung beim Überprüfen von Domänen, wenn Sie in Azure AD Connect noch keine Überprüfung durchgeführt haben.
* Unterstützung für [Microsoft Cloud Deutschland](reference-connect-instances.md#microsoft-cloud-germany)wurde hinzugefügt.
* Unterstützung für die neueste Infrastruktur der [Microsoft Azure Government-Cloud](reference-connect-instances.md#microsoft-azure-government) mit neuen URL-Anforderungen wurde hinzugefügt.

**Behobene Probleme und Verbesserungen:**

* Eine neue Filterfunktion im Editor für Synchronisierungsregeln erleichtert das Auffinden von Synchronisierungsregeln.
* Verbesserte Leistung beim Löschen eines Connectorbereichs.
* Beseitigung eines Problems, bei dem in einem Vorgang dasselbe Objekt sowohl gelöscht als auch hinzugefügt wurde (Löschen/Hinzufügen).
* Eine deaktivierte Synchronisierungsregel führt bei einem Upgrade oder einer Aktualisierung des Verzeichnisschemas nicht mehr dazu, dass eingeschlossene Objekte und Attribute erneut aktiviert werden.

## <a name="111300"></a>1.1.130.0
Veröffentlichung: April 2016

**Neue Features:**

* Unterstützung für mehrwertige Attribute in [Verzeichniserweiterungen](how-to-connect-sync-feature-directory-extensions.md) wurde hinzugefügt.
* Unterstützung für weitere Konfigurationsvarianten für das [automatische Upgrade](how-to-connect-install-automatic-upgrade.md) wurde hinzugefügt, damit diese für ein Upgrade infrage kommen.
* Einige Cmdlets für den [benutzerdefinierten Scheduler](how-to-connect-sync-feature-scheduler.md#custom-scheduler)wurden hinzugefügt.

## <a name="111190"></a>1.1.119.0
Veröffentlichung: März 2016

**Behobene Probleme:**

* Es wurde sichergestellt, dass die Express-Installation unter Windows Server 2008 (vor R2) nicht verwendet werden kann, da die Kennwortsynchronisierung unter diesem Betriebssystem nicht unterstützt wird.
* Upgrade von DirSync mit einer benutzerdefinierten Filterkonfiguration funktionierte nicht wie erwartet.
* Beim Upgrade auf eine neuere Version ohne Änderungen an der Konfiguration sollte kein vollständiger Import-/Synchronisierungsvorgang geplant werden.

## <a name="111100"></a>1.1.110.0
Veröffentlichung: Februar 2016

**Behobene Probleme:**

* Ein Upgrade von früheren Versionen kann nicht ausgeführt werden, wenn sich die Installation nicht im Standardordner „C:\Programme“ befindet.
* Wenn Sie bei der Installation am Ende des Installations-Assistenten die Option zum **Starten des Synchronisierungsvorgangs** deaktivieren, wird der Scheduler durch erneutes Ausführen des Installations-Assistenten nicht aktiviert.
* Auf Servern, auf denen das Datums-/Uhrzeitformat nicht dem US-englischen Format entspricht, wird der Scheduler nicht wie erwartet ausgeführt. Außerdem wird verhindert, dass `Get-ADSyncScheduler` korrekte Zeitangaben zurückgibt.
* Wenn Sie eine frühere Version von Azure AD Connect mit AD FS als Anmeldeoption und Upgrade installiert haben, können Sie den Installations-Assistenten nicht erneut ausführen.

## <a name="111050"></a>1.1.105.0
Veröffentlichung: Februar 2016

**Neue Features:**

* [Automatic upgrade](how-to-connect-install-automatic-upgrade.md) für Kunden mit Expresseinstellungen.
* Unterstützung für den globalen Administrator durch Verwenden von Azure AD Multi-Factor Authentication und Privileged Identity Management im Installations-Assistenten.
  * Sie müssen für Ihren Proxy auch Datenverkehr an https://secure.aadcdn.microsoftonline-p.com zulassen, wenn Sie die Multi-Factor Authentication verwenden.
  * Sie müssen https://secure.aadcdn.microsoftonline-p.com der Liste mit den vertrauenswürdigen Sites hinzufügen, damit die Multi-Factor Authentication richtig funktioniert.
* Das Ändern der Anmeldemethode des Benutzers nach der Erstinstallation ist zulässig.
* Lassen Sie im Installations-Assistenten das [Filtern von Domänen und Organisationseinheiten](how-to-connect-install-custom.md#domain-and-ou-filtering) zu. Dadurch wird außerdem das Herstellen einer Verbindung mit Gesamtstrukturen ermöglicht, in denen nicht alle Domänen verfügbar sind.
* [Scheduler](how-to-connect-sync-feature-scheduler.md) ist in das Synchronisierungsmodul integriert.

**Features, die von der Vorschau auf die allgemeine Verfügbarkeit hochgestuft wurden:**

* [Geräterückschreiben](how-to-connect-device-writeback.md).
* [Verzeichniserweiterungen](how-to-connect-sync-feature-directory-extensions.md).

**Neue Vorschaufeatures:**

* Das neue Standardintervall für den Synchronisierungszyklus beträgt 30 Minuten. In allen früheren Versionen wurde ein Intervall von drei Stunden verwendet. Unterstützung zum Ändern des [Scheduler](how-to-connect-sync-feature-scheduler.md) -Verhaltens wurde hinzugefügt.

**Behobene Probleme:**

* Von der Seite zum Überprüfen von DNS-Domänen wurden die Domänen nicht immer erkannt.
* Anmeldeinformationen von Domänenadministratoren werden beim Konfigurieren von AD FS abgefragt.
* Die lokalen AD-Konten werden vom Installations-Assistenten nicht erkannt, wenn sie sich in einer Domäne befinden, die eine andere DNS-Struktur als die Stammdomäne aufweist.

## <a name="1091310"></a>1.0.9131.0
Veröffentlichung: Dezember 2015

**Behobene Probleme:**

* Möglicherweise funktioniert die Kennwortsynchronisierung in Active Directory Domain Services (AD DS) nicht beim Ändern von Kennwörtern, jedoch beim Festlegen eines Kennworts.
* Bei Verwenden eines Proxyservers schlägt die Azure AD-Authentifizierung während der Installation möglicherweise fehl, oder ein Upgrade wird auf der Konfigurationsseite abgebrochen.
* Das Aktualisieren einer vorherigen Version von Azure AD Connect mit einer vollständigen SQL Server-Instanz schlägt fehl, wenn Sie kein SQL Server-Systemadministrator (sa) sind.
* Beim Aktualisieren einer vorherigen Version von Azure AD Connect über einen Remotecomputer mit SQL Server wird die Fehlermeldung „Kein Zugriff auf die ADSync SQL-Datenbank“ angezeigt.

## <a name="1091250"></a>1.0.9125.0
Veröffentlichung: November 2015

**Neue Features:**

* Die Vertrauensstellung zwischen AD FS und Azure AD kann neu konfiguriert werden.
* Das Active Directory-Schema kann aktualisiert und die Synchronisierungsregeln können neu generiert werden.
* Synchronisierungsregeln können deaktiviert werden.
* In einer Synchronisierungsregel kann „AuthoritativeNull“ als neues Literal definiert werden.

**Neue Vorschaufeatures:**

* [Azure AD Connect Health für die Synchronisierung](how-to-connect-health-sync.md)
* Unterstützung für die Kennwortsynchronisierung mit [Azure AD-Domänendiensten](../user-help/active-directory-passwords-update-your-own-password.md)

**Neues unterstütztes Szenario:**

* Es werden mehrere lokale Exchange-Organisationen unterstützt. Weitere Informationen finden Sie unter [Hybrid-Bereitstellungen mit mehreren Active Directory-Gesamtstrukturen](/previous-versions/exchange-server/exchange-150/jj873754(v=exchg.150)).

**Behobene Probleme:**

* Probleme bei der Kennwortsynchronisierung:
  * Für ein Objekt, das sich bisher außerhalb des Bereichs befand und das dann in den Bereich verschoben wurde, wird das Kennwort nicht synchronisiert. Dies schließt die Organisationseinheit und Attributfilterung ein.
  * Wenn eine neue Organisationseinheit in die Synchronisierung einbezogen wird, ist keine vollständige Kennwortsynchronisierung erforderlich.
  * Nach dem Aktivieren eines bisher deaktivierten Benutzers wird das Kennwort nicht synchronisiert.
  * Für die Warteschlange mit Kennwortwiederholungsversuchen gilt kein Limit, der bisherige Grenzwert von 5.000 Objekten bis zum Außerkraftsetzen wurde entfernt.
* Mit der Gesamtstrukturfunktionsebene von Windows Server 2016 kann keine Verbindung mit Active Directory hergestellt werden.
* Nach der Erstinstallation kann die für das gruppenspezifische Filtern verwendete Gruppe nicht geändert werden.
* Wenn Benutzer, für die das Kennwortrückschreiben aktiviert ist, ihr Kennwort ändern, wird auf dem Azure AD Connect-Server kein neues Benutzerprofil mehr angelegt.
* In den Geltungsbereichen der Synchronisierungsregeln können keine langen Ganzzahlen als Werte verwendet werden.
* Das Kontrollkästchen „Geräterückschreiben“ bleibt deaktiviert, wenn nicht erreichbare Domänencontroller vorhanden sind.

## <a name="1086670"></a>1.0.8667.0
Veröffentlichung: August 2015

**Neue Features:**

* Der Azure AD Connect-Installations-Assistent ist jetzt für alle Windows Server-Sprachen lokalisiert.
* Erweiterte Unterstützung für die Kontoentsperrung bei Verwendung der Azure AD-Kennwortverwaltung.

**Behobene Probleme:**

* Azure AD-Connect-Installations-Assistent stürzt ab, wenn ein anderer Benutzer als die Person, die die Installation gestartet hat, die Installation fortsetzt.
* Wenn eine frühere Deinstallation von Azure AD Connect die Azure AD Connect-Synchronisierung nicht sauber deinstalliert, ist keine Neuinstallation möglich.
* Azure AD Connect kann nicht per Expressinstallation installiert werden, wenn der Benutzer nicht zur Stammdomäne der Gesamtstruktur gehört, oder wenn eine nichtenglische Version von Active Directory verwendet wird.
* Wenn der FQDN des Active Directory-Benutzerkontos nicht aufgelöst werden kann, wird eine irreführende Fehlermeldung "Schreiben des Schemas fehlgeschlagen" angezeigt.
* Wenn das auf dem Active Directory Connector verwendete Konto außerhalb des Assistenten geändert wird, schlägt die Ausführung des Assistenten bei nachfolgenden Versuchen fehl.
* Die Installation von Azure AD Connect auf einem Domänencontroller schlägt manchmal fehl.
* Aktivieren und Deaktivieren des "Stagingmodus" ist nicht möglich, wenn Erweiterungsattribute hinzugefügt wurden.
* Das Rückschreiben von Kennwörtern schlägt in einigen Konfigurationen aufgrund eines falschen Kennworts für den Active Directory Connector fehl.
* DirSync kann nicht aktualisiert werden, wenn ein definierter Name (DN) bei der Attributfilterung verwendet wird.
* Beim Verwenden der Kennwortzurücksetzung wird die CPU übermäßig ausgelastet.

**Entfernte Vorschaufunktionen:**

* Die Vorschaufunktion [Benutzerrückschreiben](how-to-connect-preview.md#user-writeback) wurde aufgrund von Rückmeldungen unserer Vorschaukunden vorübergehend entfernt. Sie wird später erneut hinzugefügt werden, nachdem das gemeldete Feedback umgesetzt wurde.

## <a name="1086410"></a>1.0.8641.0
Veröffentlichung: Juni 2015

**Erste Version von Azure AD Connect.**

Änderung des Namens von Azure AD Sync zu Azure AD Connect.

**Neue Features:**

* [Installation mit Express-Einstellungen](how-to-connect-install-express.md)
* [Konfigurieren von AD FS](how-to-connect-install-custom.md#configuring-federation-with-ad-fs) möglich
* [Upgrade von DirSync](how-to-dirsync-upgrade-get-started.md) möglich
* [Verhindern von versehentlichen Löschungen](how-to-connect-sync-feature-prevent-accidental-deletes.md)
* Neuer [Stagingmodus](how-to-connect-sync-staging-server.md)

**Neue Vorschaufeatures:**

* [Rückschreiben von Benutzern](how-to-connect-preview.md#user-writeback)
* [Geräterückschreiben](how-to-connect-device-writeback.md)
* [Verzeichniserweiterungen](how-to-connect-preview.md)

## <a name="104940501"></a>1.0.494.0501
Veröffentlichung: Mai 2015

**Neue Voraussetzung:**

* Azure AD Sync erfordert jetzt die Installation der .NET Framework-Version 4.5.1.

**Behobene Probleme:**

* Das Rückschreiben von Kennwörtern aus Azure AD schlägt mit einem Azure-Service Bus-Konnektivitätsfehler fehl.

## <a name="104910413"></a>1.0.491.0413
Veröffentlichung: April 2015

**Behobene Probleme und Verbesserungen:**

* Der Active Directory Connector verarbeitet Löschvorgänge nicht ordnungsgemäß, wenn der Papierkorb aktiviert ist, und mehrere Domänen in der Gesamtstruktur vorhanden sind.
* Die Leistung bei Importvorgängen wurde für den Azure Active Directory Connector verbessert.
* Wenn eine Gruppe die Mitgliedergrenze überschritten hatte (standardmäßig ist der Grenzwert auf 50.000 Objekte festgelegt), wurde die Gruppe in Azure Active Directory gelöscht. Beim neuen Verhalten wird die Gruppe nicht gelöscht. Jedoch wird ein Fehler ausgelöst, und neue Mitgliedschaftsänderungen werden nicht exportiert.
* Ein neues Objekt kann nicht bereitgestellt werden, wenn eine gestaffelte Löschung mit dem gleichen DN bereits im Connectorbereich vorhanden ist.
* Einige Objekte werden für die Synchronisierung während einer Deltasynchronisierung markiert, obwohl keine Änderung für das Objekt bereitgestellt ist.
* Bei Erzwingen einer Kennwortsynchronisierung wird auch die Liste der bevorzugten Domänencontroller entfernt.
* CSExportAnalyzer hat Probleme mit einigen Objektstatus.

**Neue Features:**

* Mit einer Verknüpfung ist jetzt eine Verbindung zum Objekttyp "ANY" im Metaverse möglich.

## <a name="104850222"></a>1.0.485.0222
Veröffentlichung: Februar 2015

**Verbesserungen:**

* Verbesserte Importleistung.

**Behobene Probleme:**

* Die Kennwortsynchronisierung berücksichtigt das zur Attributfilterung verwendete Attribut „cloudFiltered“. Gefilterte Objekte fallen nicht mehr in den Bereich der Synchronisierung von Kennwörtern.
* In seltenen Fällen, in denen die Topologie viele Domänencontroller aufweist, funktioniert die Synchronisierung von Kennwörtern nicht.
* "Stopped-server" beim Importieren vom Azure AD-Connector in die Geräteverwaltung wurde in Azure AD/Intune aktiviert.
* Verknüpfen der fremden Sicherheitsprinzipale (FSPs) aus mehreren Domänen in der gleichen Gesamtstruktur verursacht den Fehler mehrdeutiger Verknüpfungen.

## <a name="104751202"></a>1.0.475.1202
Veröffentlichung: Dezember 2014

**Neue Features:**

* Die Synchronisierung von Kennwörtern mit attributbasierter Filterung wird jetzt unterstützt. Weitere Informationen finden Sie unter [Synchronisierung von Kennwörtern mit Filterung](how-to-connect-sync-configure-filtering.md).
* Das Attribut „ms-DS-ExternalDirectoryObjectID“ wird in Active Directory zurückgeschrieben. Mit diesem Feature wird Unterstützung für Microsoft 365-Anwendungen hinzugefügt. Es verwendet OAuth2, um auf Online- und lokale Postfächer in einer hybriden Exchange-Bereitstellung zuzugreifen.

**Behobene Upgrade-Probleme:**

* Eine neuere Version des Anmelde-Assistenten ist auf dem Server verfügbar.
* Ein benutzerdefinierter Installationspfad wurde verwendet, um Azure AD Sync zu installieren.
* Ein ungültiges benutzerdefiniertes Verknüpfungskriterium blockiert das Upgrade.

**Weitere Problembehebungen:**

* Probleme mit den Vorlagen für Office Pro Plus wurden behoben.
* Installationsprobleme, die durch Benutzernamen verursacht wurden, die mit einem Gedankenstrich beginnen, wurden behoben.
* Das Problem, dass die Einstellung sourceAnchor bei der zweiten Ausführung des Installations-Assistenten verloren geht, wurde behoben.
* Probleme mit der ETW zur Kennwortsynchronisierung wurden behoben.

## <a name="104701023"></a>1.0.470.1023
Veröffentlichung: Oktober 2014

**Neue Features:**

* Synchronisierung von Kennwörtern mehrerer lokaler Active Directory-Instanzen mit Azure AD.
* Lokalisierte Benutzeroberfläche für die Installation für alle Windows Server-Sprachen.

**Upgrade von AADSync 1.0 GA**

Wenn Sie Azure AD Sync bereits installiert haben, müssen Sie einen zusätzlichen Schritt ausführen, falls Sie eine der standardmäßigen Synchronisierungsregeln geändert haben. Nach dem Upgrade auf die Version 1.0.470.1023 sind die Synchronisierungsregeln, die Sie geändert haben, dupliziert. Führen Sie für jede geänderte Synchronisierungsregel die folgenden Schritte aus:

1. Suchen Sie die Synchronisierungsregel, die Sie geändert haben, und notieren Sie sich die Änderungen.
1. Löschen Sie die Synchronisierungsregel.
1. Suchen Sie die neue von Azure AD Sync erstellte Synchronisierungsregel, und wenden Sie die Änderungen erneut an.

**Berechtigungen für das Active Directory-Konto**

Dem Active Directory-Konto müssen zusätzliche Berechtigungen erteilt werden, um die Kennworthashes aus Active Directory zu lesen. Die zu erteilenden Berechtigungen heißen „Verzeichnisänderungen replizieren“ und „Alle Verzeichnisänderungen replizieren“. Beide Berechtigungen sind zum Lesen von Kennworthashes erforderlich.

## <a name="104190911"></a>1.0.419.0911
Veröffentlichung: September 2014

**Erste Version von Azure AD Sync**

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zum [Integrieren lokaler Identitäten in Azure Active Directory](whatis-hybrid-identity.md).