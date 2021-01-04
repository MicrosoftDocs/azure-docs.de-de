---
title: Häufig gestellte Fragen zu Enterprise State Roaming – Azure Active Directory
description: Häufig gestellte Fragen zu ESR
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 02/12/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: na
ms.collection: M365-identity-device-management
ms.openlocfilehash: cb889298a09c30a629c69442ebf31bc735af31d1
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96173123"
---
# <a name="settings-and-data-roaming-faq"></a>Roaming von Einstellungen und Daten – Häufig gestellte Fragen

Dieser Artikel enthält Antworten auf Fragen zur Synchronisierung von Einstellungen und App-Daten, die von IT-Administratoren häufig gestellt werden.

## <a name="what-data-roams"></a>Für welche Daten wird das Roaming durchgeführt?

**Windows-Einstellungen**: Die PC-Einstellungen, die in das Windows-Betriebssystem integriert sind. Im Allgemeinen sind dies Einstellungen, mit denen Ihr PC personalisiert wird. Sie lassen sich in die folgenden Kategorien unterteilen:

* *Design*: beispielsweise Desktopdesign und Taskleisteneinstellungen.
* *Internet Explorer-Einstellungen:* : zuletzt geöffnete Registerkarten, Favoriten usw.
* *Einstellungen des Microsoft Edge-Browsers:* beispielsweise Favoriten und Leseliste.
* *Kennwörter:* : Internet-Kennwörter, WLAN-Profile usw.
* *Spracheinstellungen*: Einstellungen für Tastaturlayouts, Systemsprache, Datum und Uhrzeit usw.
* *Funktionen für die erleichterte Bedienung:* : Design mit hohem Kontrast, Sprachausgabe, Bildschirmlupe usw.
* *Andere Windows-Einstellungen*, z.B. Mauseinstellungen.

> [!NOTE]
> Dieser Artikel bezieht sich auf den älteren HTML-basierten Microsoft Edge-Browser, der im Juli 2015 mit Windows 10 veröffentlicht wurde. Der Artikel gilt nicht für den neuen Chromium-basierten Microsoft Edge-Browser, der am 15. Januar 2020 veröffentlicht wurde. Weitere Informationen zum Synchronisierungsverhalten des neuen Microsoft Edge finden Sie im Artikel [Microsoft Edge-Synchronisierung](/deployedge/microsoft-edge-enterprise-sync).

**Anwendungsdaten:** Universelle Windows-Apps können Einstellungsdaten in einen Roamingordner schreiben. Alle Daten, die in diesen Ordner geschrieben werden, werden automatisch synchronisiert. App-Entwickler können eine App entwerfen, um diese Funktion zu nutzen. Weitere Informationen zum Entwickeln einer universellen Windows-App, die Roaming verwendet, finden Sie unter [Speichern und Abrufen von Einstellungen und anderen App-Daten](/windows/uwp/design/app-settings/store-and-retrieve-app-data) und im [Entwicklerblog zum Roaming von Windows 8-App-Daten](https://blogs.windows.com/windowsdeveloper/2016/05/04/roaming-app-data-and-the-user-experience/).

## <a name="what-account-is-used-for-settings-sync"></a>Welches Konto wird für die Einstellungssynchronisierung verwendet?

In Windows 8.1 wurden für die Einstellungssynchronisierung immer Microsoft-Benutzerkonten verwendet. Unternehmensbenutzer hatten die Möglichkeit, ein Microsoft-Konto mit ihrem Active Directory-Domänenkonto zu verbinden, um Zugriff auf die Einstellungssynchronisierung zu erhalten. In Windows 10 wurde diese Funktion des verbundenen Microsoft-Kontos durch ein Framework mit primärem/sekundärem Konto ersetzt.

Das primäre Konto ist als das Konto definiert, das zum Anmelden bei Windows verwendet wird. Dies kann ein Microsoft-Konto sein, ein Azure Active Directory-Konto (Azure AD), ein lokales Active Directory-Konto oder ein lokales Konto. Zusätzlich zum primären Konto können Windows 10-Benutzer ihrem Gerät ein oder mehrere sekundäre Cloudkonten hinzufügen. Bei einem sekundären Konto handelt es sich im Allgemeinen um ein Microsoft-Konto, ein Azure AD-Konto oder ein anderes Konto, z.B. Gmail oder Facebook. Diese sekundären Konten ermöglichen den Zugriff auf weitere Dienste, z.B. einmaliges Anmelden und den Windows Store, aber sie unterstützen keine Einstellungssynchronisierung.

Unter Windows 10 kann für die Einstellungssynchronisierung nur das primäre Konto für das Gerät verwendet werden. (Weitere Informationen finden Sie unter [Gewusst wie: Aktualisieren von Microsoft-Konto-Einstellungssynchronisierung unter Windows 8 auf Azure AD-Einstellungssynchronisierung unter Windows 10](enterprise-state-roaming-faqs.md#how-do-i-upgrade-from-microsoft-account-settings-sync-in-windows-8-to-azure-ad-settings-sync-in-windows-10).)

Daten werden in Bezug auf die verschiedenen Benutzerkonten niemals gemischt. Es gibt zwei Regeln für die Synchronisierung von Einstellungen:

* Für Windows-Einstellungen wird das Roaming immer mit dem primären Konto durchgeführt.
* App-Daten werden mit dem Konto verknüpft, das zum Beschaffen der App verwendet wird. Es werden nur Apps synchronisiert, die mit dem primären Konto verknüpft („getaggt“) sind. Die App-Besitzmarkierung wird bestimmt, wenn eine App über den Windows Store oder die Verwaltung mobiler Geräte (Mobile Device Management, MDM) quergeladen wird.

Falls der Besitzer einer App nicht identifiziert werden kann, wird das Roaming basierend auf dem primären Konto durchgeführt. Wenn ein Gerät von Windows 8 oder Windows 8.1 auf Windows 10 aktualisiert wird, werden alle Apps als „mit diesem Microsoft-Konto erworben“ markiert. Dies liegt daran, dass die meisten Benutzer Apps über den Windows Store abrufen und es vor Windows 10 noch keine Windows Store-Unterstützung für Azure AD-Konten gab. Wenn eine App per Offlinelizenz installiert wird, wird die App mit dem primären Konto auf dem Gerät verknüpft.

> [!NOTE]
> Microsoft-Konten auf Windows 10-Geräten, die sich im Besitz von Unternehmen befinden und mit Azure AD verbunden sind, können nicht mehr mit einem Domänenkonto verbunden werden. Die Möglichkeit zum Verbinden eines Microsoft-Kontos mit einem Domänenkonto und zum Synchronisieren aller Daten des Benutzers mit dem Microsoft-Konto (also das Microsoft-Konto-Roaming über die Funktion „Verbundenes Microsoft-Konto und Active Directory”) wird von Windows 10-Geräten entfernt, die in eine verbundene Active Directory- oder Azure AD-Umgebung eingebunden werden.

## <a name="how-do-i-upgrade-from-microsoft-account-settings-sync-in-windows-8-to-azure-ad-settings-sync-in-windows-10"></a>Gewusst wie: Aktualisieren von Microsoft-Konto-Einstellungssynchronisierung unter Windows 8 auf Azure AD-Einstellungssynchronisierung unter Windows 10

Wenn Sie der Active Directory-Domäne beigetreten sind und Windows 8.1 mit einem verbundenen Microsoft-Konto ausführen, werden Ihre Einstellungen über Ihr Microsoft-Konto synchronisiert. Nach der Aktualisierung auf Windows 10 werden Ihre Benutzereinstellungen weiterhin über das Microsoft-Konto synchronisiert, solange Sie der Domäne beigetreten sind und die Active Directory-Domäne keine Verbindung mit Azure AD herstellt.

Falls die lokale Active Directory-Domäne eine Verbindung mit Azure AD herstellt, versucht Ihr das Gerät, die Einstellungen über das verbundene Azure AD-Konto zu synchronisieren. Wenn das Enterprise State Roaming vom Azure AD-Administrator nicht aktiviert wurde, beendet Ihr verbundenes Azure AD-Konto die Synchronisierung von Einstellungen. Wenn Sie Windows 10-Benutzer sind und sich mit einer Azure AD-Identität anmelden, beginnt die Synchronisierung von Windows-Einstellungen, sobald Ihr Administrator die Einstellungssynchronisierung über Azure AD aktiviert.

Wenn Sie persönliche Daten auf Ihren Unternehmensgeräten gespeichert haben, sollten Sie sich darüber im Klaren sein, dass für die Windows-Betriebssystem- und Anwendungsdaten die Synchronisierung mit Azure AD durchgeführt wird. Dies hat folgende Auswirkungen:

* Die Einstellungen Ihres persönlichen Microsoft-Kontos werden von den Einstellungen Ihres Azure AD-Geschäfts-, Schul- oder Unikontos abweichen. Dies liegt daran, dass die Microsoft-Konto- und Azure AD-Einstellungen jetzt über getrennte Konten synchronisiert werden.
* Persönliche Daten wie WLAN-Kennwörter, Webanmeldeinformationen und Internet Explorer-Favoriten, die bislang über das verbundene Microsoft-Konto synchronisiert wurden, werden nun per Azure AD synchronisiert.

## <a name="how-do-microsoft-account-and-azure-ad-enterprise-state-roaming-interoperability-work"></a>Wie funktioniert die Enterprise State Roaming-Interoperabilität zwischen Microsoft-Konto und Azure AD?

In den Windows 10-Versionen ab November 2015 wird Enterprise State Roaming nur für jeweils ein Konto unterstützt. Wenn Sie sich mit einem Azure AD-Geschäfts-, Schul- oder Unikonto bei Windows anmelden, werden alle Daten über Azure AD synchronisiert. Wenn Sie sich mit einem persönlichen Microsoft-Konto bei Windows anmelden, werden alle Daten über das Microsoft-Konto synchronisiert. Für universelle App-Daten wird das Roaming nur mit dem primären Anmeldekonto auf dem Gerät durchgeführt – und auch nur dann, wenn sich die Lizenz der App im Besitz des primären Kontos befindet. Universelle App-Daten für Apps, die sich im Besitz von sekundären Konten befinden, werden nicht synchronisiert.

## <a name="do-settings-sync-for-azure-ad-accounts-from-multiple-tenants"></a>Können Einstellungen für Azure AD-Konten mehrerer Mandanten synchronisiert werden?

Wenn sich mehrere Azure AD-Konten von verschiedenen Azure AD-Mandanten auf demselben Gerät befinden, müssen Sie die Registrierung des Geräts aktualisieren, um mit dem Azure Rights Management-Dienst für jeden Azure AD-Mandanten zu kommunizieren.  

1. Suchen Sie die GUID für jeden Azure AD-Mandanten. Öffnen Sie das Azure-Portal, und wählen Sie einen Azure AD-Mandanten aus. Die GUID für den Mandanten befindet sich auf der Seite „Eigenschaften“ für den ausgewählten Mandanten (https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties), mit der Bezeichnung **Verzeichnis-ID**. 
2. Wenn Sie die GUID ermittelt haben, müssen Sie den Registrierungsschlüssel **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\SettingSync\WinMSIPC\<tenant ID GUID>** hinzufügen.
   Erstellen Sie im Schlüssel **Mandanten-ID-GUID** einen neuen mehrteiligen Zeichenfolgenwert (REG-MULTI-SZ) namens **AllowedRMSServerUrls**. Geben Sie als Daten die URLs der Lizenzverteilungspunkte der anderen Azure-Mandanten an, auf die das Gerät zugreift.
3. Sie können die Lizenzverteilungspunkt-URLs ermitteln, indem Sie das Cmdlet **Get-AadrmConfiguration** aus dem AADRM-Modul ausführen. Falls sich die Werte für **LicensingIntranetDistributionPointUrl** und **LicensingExtranetDistributionPointUrl** unterscheiden, müssen Sie beide Werte angeben. Wenn die Werte gleich sind, müssen Sie den Wert nur einmal angeben.

## <a name="what-are-the-roaming-settings-options-for-existing-windows-desktop-applications"></a>Welche Optionen stehen für das Roaming von Einstellungen für bereits vorhandene Windows-Desktopanwendungen zur Verfügung?

Roaming kann nur für universelle Windows-Apps verwendet werden. Das Roaming für eine bereits vorhandene Windows-Desktopanwendung kann auf zwei Arten aktiviert werden:

* Mithilfe der [Desktop-Brücke](/windows/msix/desktop/source-code-overview) können Sie bereits vorhandene Windows-Desktop-Apps zur universellen Windows-Plattform migrieren. Anschließend sind nur minimale Codeänderungen erforderlich, um das Roaming von Azure AD-App-Daten zu nutzen. Die Desktop-Brücke versieht Ihre Apps mit einer App-Identität. Diese wird benötigt, um das App-Datenroaming für bereits vorhandene Desktop-Apps zu ermöglichen.
* [User Experience Virtualization (UE-V)](/previous-versions//dn458947(v=vs.85)) können Sie eine Vorlage mit benutzerdefinierten Einstellungen für bereits vorhandene Windows-Desktop-Apps erstellen und das Roaming für Win32-Apps ermöglichen. Bei dieser Option sind keinerlei Änderungen am App-Code erforderlich. UE-V ist auf lokales Active Directory-Roaming für Kunden beschränkt, die das Microsoft Desktop Optimization Pack erworben haben.

Administratoren können UE-V so konfigurieren, dass das Roaming nur für Daten von Windows-Desktop-Apps stattfindet, indem sie mithilfe von [UE-V-Gruppenrichtlinien](/microsoft-desktop-optimization-pack/uev-v2/configuring-ue-v-2x-with-group-policy-objects-both-uevv2) das Roaming von Windows-Betriebssystemeinstellungen und von Daten universeller Apps deaktivieren. Dies betrifft folgende Einstellungen und Daten:

* Gruppenrichtlinie „Roaming von Windows-Einstellungen“
* Gruppenrichtlinie „Windows-Apps nicht synchronisieren“
* Internet Explorer-Roaming im Abschnitt „Anwendungen“

Es ist möglich, dass Microsoft in Zukunft nach Wegen suchen wird, wie UE-V fest in Windows integriert und erweitert werden kann, um das Roaming von Einstellungen über die Azure AD-Cloud zu ermöglichen.

## <a name="can-i-store-synced-settings-and-data-on-premises"></a>Kann ich synchronisierte Einstellungen und Daten lokal speichern?

Enterprise State Roaming speichert alle synchronisierten Daten in der Microsoft-Cloud. UE-V bietet eine Lösung für lokales Roaming.

## <a name="who-owns-the-data-thats-being-roamed"></a>Wem gehören die Daten, für die das Roaming durchgeführt wird?

Daten, für die Enterprise State Roaming verwendet wird, gehören den Unternehmen. Die Daten werden in einem Azure-Datencenter gespeichert. Alle Benutzerdaten werden sowohl bei der Übertragung als auch der Speicherung in der Cloud mithilfe des Azure Rights Management-Diensts von Azure Information Protection verschlüsselt. Dies ist eine Verbesserung im Vergleich zur Einstellungssynchronisierung basierend auf Microsoft-Konten, da dabei nur bestimmte vertrauliche Daten, z.B. Anmeldeinformationen von Benutzern, verschlüsselt werden, bevor sie das Gerät verlassen.

Microsoft liegt der Schutz von Kundendaten am Herzen. Die Einstellungsdaten eines Unternehmensbenutzers werden automatisch mithilfe des Azure Rights Management-Diensts verschlüsselt, bevor sie ein Windows 10-Gerät verlassen, damit kein anderer Benutzer diese Daten lesen kann. Wenn Ihr Unternehmen über ein kostenpflichtiges Abonnement für den Azure Rights Management-Dienst verfügt, können Sie weitere Schutzfunktionen nutzen, z. B. Nachverfolgen und Widerrufen von Dokumenten, automatisches Schützen von E-Mails mit vertraulichen Informationen und Verwalten Ihrer eigenen Schlüssel (Lösung „Bring Your Own Key“ (BYOK)). Unter [Was ist Azure Rights Management?](/azure/information-protection/what-is-information-protection) finden Sie weitere Informationen zu diesen Features und zur Funktionsweise dieses Schutzdiensts.

## <a name="can-i-manage-sync-for-a-specific-app-or-setting"></a>Kann ich die Synchronisierung für eine bestimmte Anwendung oder Einstellung verwalten?

Unter Windows 10 gibt es keine MDM- oder Gruppenrichtlinien-Einstellung, mit der das Roaming für eine einzelne Anwendung deaktiviert werden kann. Mandantenadministratoren können die Synchronisierung von App-Daten für alle Apps auf einem verwalteten Gerät deaktivieren, es gibt jedoch keine präzisere Steuerung auf App-Ebene oder innerhalb der Apps.

## <a name="how-can-i-enable-or-disable-roaming"></a>Wie kann ich das Roaming aktivieren oder deaktivieren?

Navigieren Sie in der App **Einstellungen** zu **Konten** > **Einstellungen synchronisieren**. Auf dieser Seite sehen Sie, welches Konto für das Roaming von Einstellungen verwendet wird, und Sie können einzelne Gruppen mit Einstellungen für das Roaming aktivieren oder deaktivieren.

## <a name="what-is-microsofts-recommendation-for-enabling-roaming-in-windows-10"></a>Was empfiehlt Microsoft in Bezug auf die Aktivierung des Roamings in Windows 10?

Microsoft bietet verschiedene Lösungen für das Roaming von Einstellungen. Hierzu zählen etwa Roamingbenutzerprofile, UE-V und Enterprise State Roaming.  In zukünftigen Versionen von Windows setzt Microsoft verstärkt auf Enterprise State Roaming. Wenn Ihre Organisation noch nicht für das Verschieben von Daten in die Cloud bereit ist oder noch Bedenken bestehen, empfiehlt Microsoft die Verwendung von UE-V als primäre Roamingtechnologie. Falls Ihr Unternehmen Roamingunterstützung für bereits vorhandene Windows-Desktopanwendungen benötigt, aber an einer schnellen Nutzung der Cloud interessiert ist, empfiehlt Microsoft die parallele Verwendung von Enterprise State Roaming und UE-V. UE-V und Enterprise State Roaming sind zwar sehr ähnliche Technologien, schließen sich aber nicht gegenseitig aus. Sie ergänzen sich, sodass Sie sicherstellen können, dass Ihre Organisation genau die Roamingdienste bereitstellt, die Ihre Benutzer benötigen.  

Bei der parallelen Nutzung der Synchronisierung von Unternehmenseinstellungen von Enterprise State Roaming und UE-V gelten folgende Regeln:

* Enterprise State Roaming ist der primäre Roaming-Agent auf dem Gerät. UE-V wird zum Schließen der „Win32-Lücke“ verwendet.
* Das UE-V-Roaming für Windows-Einstellungen und Daten aus modernen UWP-Apps sollte deaktiviert werden, wenn Sie die UE-V-Gruppenrichtlinien verwenden. Dies wird bereits durch Enterprise Status Roaming durchgeführt.

## <a name="how-does-enterprise-state-roaming-support-virtual-desktop-infrastructure-vdi"></a>Wie unterstützt Enterprise State Roaming die virtuelle Desktopinfrastruktur (Virtual Desktop Infrastructure, VDI)?

Enterprise State Roaming wird nur für Windows 10-Client-SKUs unterstützt, nicht für Server-SKUs. Wenn ein virtueller Clientcomputer auf einem Hypervisorcomputer gehostet wird und Sie sich remote bei dem virtuellen Computer anmelden, wird das Roaming für Ihre Benutzerdaten durchgeführt. Wenn mehrere Benutzer das gleiche Betriebssystem verwenden und sich remote bei einem Server anmelden, um uneingeschränkte Desktopfeatures zu nutzen, findet möglicherweise kein Roaming statt. Dieses sitzungsbasierte Szenario wird offiziell nicht unterstützt.

## <a name="what-happens-when-my-organization-purchases-a-subscription-that-includes-azure-rights-management-after-using-roaming"></a>Was geschieht, wenn meine Organisation Roaming verwendet und anschließend ein Abonnement erwirbt, das Azure Rights Management enthält?

Falls Ihre Organisation das Roaming unter Windows 10 bereits über das kostenlose, eingeschränkte Azure Rights Management-Abonnement nutzt, hat der Erwerb eines [kostenpflichtigen Abonnements](https://azure.microsoft.com/pricing/details/information-protection/), das den Azure Rights Management-Schutzdienst enthält, keinerlei Auswirkungen auf die Funktionen des Roamingfeatures, und Ihr IT-Administrator muss keine Änderungen an der Konfiguration vornehmen.

## <a name="known-issues"></a>Bekannte Probleme

In der Dokumentation im Abschnitt [Problembehandlung](enterprise-state-roaming-troubleshooting.md) finden Sie eine Liste der bekannten Probleme. 

## <a name="next-steps"></a>Nächste Schritte 

Sehen Sie sich die Übersicht zum [Enterprise State Roaming](enterprise-state-roaming-overview.md) an.