---
title: 'Bereitstellen von Azure verwalteter Arbeitsstationen: Azure Active Directory'
description: Informationen zum Bereitstellen sicherer von Azure verwalteter Arbeitsstationen, um das Risiko einer Sicherheitsverletzung aufgrund einer Fehlkonfiguration oder Gefährdung zu verringern.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: frasim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 242926c0821e4951d2a2bd2f858f63691baf1017
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66307555"
---
# <a name="deploy-a-secure-workstation"></a>Bereitstellen einer sicheren Arbeitsstation

Da Sie nun wissen, [warum das Schützen der Arbeitsstation wichtig ist](concept-azure-managed-workstation.md), können Sie unter Verwendung der verfügbaren Tools mit der Bereitstellung beginnen. In dieser Anleitung werden die definierten Profile verwendet, um eine Arbeitsstation zu erstellen, die von Anfang an sicherer ist.

![Bereitstellen einer sicheren Arbeitsstation](./media/howto-azure-managed-workstation/deploying-secure-workstations.png)

Vor dem Bereitstellen der Lösung müssen Sie wählen, welches Profil Sie verwenden möchten. Beachten Sie unbedingt, dass Sie ein beliebiges der ausgewählten Profile verwenden und zu einem anderen wechseln können, indem Sie das Profil in Intune gemäß Ihren Anforderungen zuweisen. Mehrere Profile können gleichzeitig in einer Bereitstellung verwendet und mithilfe von Tags oder Gruppenzuweisungen zugewiesen werden.

| Profil | Niedrig | Verbessert | Hoch | Spezialisiert | Geschützt | Isolated |
| :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Benutzer in Azure AD | Ja | Ja | Ja | Ja | Ja | Ja |
| Von Intune verwaltet | Ja | Ja | Ja | Ja | Ja | Ja |
| Gerät bei Azure AD registriert | Ja |  |  |  |  | |   |
| Gerät in Azure AD eingebunden |   | Ja | Ja | Ja | Ja | Ja |
| Intune-Sicherheitsbaseline angewandt |   | Ja <br> (Erweitert) | Ja <br> (HighSecurity) | Ja <br> (NCSC) | Ja <br> (Geschützt) |  Nicht verfügbar |
| Hardware erfüllt sichere Windows 10-Standards |   | Ja | Ja | Ja | Ja | Ja |
| Microsoft Defender ATP aktiviert |   | Ja  | Ja | Ja | Ja | Ja |
| Entfernen von Administratorrechten |   |   | Ja  | Ja | Ja | Ja |
| Bereitstellung mithilfe von Microsoft Autopilot |   |   | Ja  | Ja | Ja | Ja |
| Apps nur von Intune installiert |   |   |   | Ja | Ja |Ja |
| URLs auf genehmigte Liste beschränkt |   |   |   | Ja | Ja |Ja |
| Internet (eingehend/ausgehend blockiert) |   |   |   |  |  |Ja |

## <a name="license-requirements"></a>Lizenzanforderungen

Die in diesem Handbuch erörterten Konzepte setzen Microsoft 365 Enterprise E5 oder eine entsprechende SKU voraus. Einige der Empfehlungen in diesem Handbuch können mit niedrigeren SKUs implementiert werden. Weitere Informationen finden Sie unter [Was ist Microsoft 365?](https://www.microsoft.com/licensing/product-licensing/microsoft-365-enterprise).

Um Lizenzen automatisch bereitzustellen, können Sie die [gruppenbasierte Lizenzierung](https://docs.microsoft.com/azure/active-directory/users-groups-roles/licensing-groups-assign) für Ihre Benutzer konfigurieren.

## <a name="azure-active-directory-configuration"></a>Azure Active Directory-Konfiguration

Das Konfigurieren Ihres Azure Active Directory-Verzeichnisses (Azure AD), das Ihre Benutzer, Gruppen und Geräte für Ihre Administratorarbeitsstationen verwaltet, erfordert das Aktivieren der Identitätsdienste und Features mit einem [Administratorkonto](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles).

Wenn Sie das geschützte Arbeitsstations-Administratorkonto erstellen, machen Sie das Konto für Ihre aktuelle Arbeitsstation verfügbar. Sie sollten diese anfängliche Konfiguration und die gesamte globale Konfiguration auf einem bekannten, sicheren Gerät ausführen. Sie können die Anleitung zum [Verhindern von Schadsoftwareinfektionen](https://docs.microsoft.com/windows/security/threat-protection/intelligence/prevent-malware-infection) nutzen, um das Risiko der Anfälligkeit für Angriffe zu verringern.

Organisationen sollten zumindest für ihre Administratoren die mehrstufige Authentifizierung voraussetzen. Einen Implementierungsleitfaden finden Sie unter [Planen einer cloudbasierten Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted).

### <a name="azure-ad-users-and-groups"></a>Azure AD-Benutzer und -Gruppen

Navigieren Sie im Azure-Portal zu **Azure Active Directory** > **Benutzer** > **Neuer Benutzer**. [Erstellen Sie den Benutzer Ihrer sicheren Arbeitsstation](https://docs.microsoft.com/Intune/quickstart-create-user), der Ihr Geräteadministrator sein soll.

* **Name**: Administrator der sicheren Arbeitsstation
* **Benutzername**: secure-ws-admin@identityitpro.com
* **Verzeichnisrolle** - **Eingeschränkter Administrator**, und wählen Sie die folgende administrative Rolle aus
   * **Intune-Administrator**
* **Erstellen**

Wir erstellen zwei Gruppen, eine für Benutzer der Arbeitsstationen und eine für die Arbeitsstationen selbst. Navigieren Sie im Azure-Portal zu **Azure Active Directory** > **Gruppen** > **Neue Gruppe**.

Erste Gruppe für Benutzer von Arbeitsstationen. Um Lizenzen für Benutzer automatisch bereitzustellen, können Sie die [gruppenbasierte Lizenzierung](https://docs.microsoft.com/azure/active-directory/users-groups-roles/licensing-groups-assign) für die Benutzer konfigurieren.

* **Gruppentyp**: Sicherheit
* **Gruppenname**: Benutzer der sicheren Arbeitsstation
* **Mitgliedschaftstyp**: Zugewiesen
* Fügen Sie Ihren Administratorbenutzer der sicheren Arbeitsstation der Gruppe hinzu
   * secure-ws-admin@identityitpro.com
* Sie können beliebige andere Benutzer, die sichere Arbeitsstationen verwalten, der Gruppe hinzufügen
* **Erstellen**

Zweite Gruppe für Arbeitsstationsgeräte.

* **Gruppentyp**: Sicherheit
* **Gruppenname**: Sichere Arbeitsstationen
* **Mitgliedschaftstyp**: Zugewiesen
* **Erstellen**

### <a name="azure-ad-device-configuration"></a>Azure AD-Gerätekonfiguration

#### <a name="specify-who-can-join-devices-to-azure-ad"></a>Angeben, wer Geräte in Azure AD einbinden darf

Konfigurieren Sie Ihre Geräteeinstellungen in Active Directory, um Ihrer administrativen Sicherheitsgruppe zu erlauben, in Ihre Domäne Geräte einzubinden. Um diese Einstellung im Azure-Portal zu konfigurieren, navigieren Sie zu **Azure Active Directory** > **Geräte** > **Geräteeinstellungen**. Wählen Sie **Ausgewählte** unter **Benutzer dürfen Geräte in Azure AD einbinden** und dann die Gruppe „Benutzer der sicheren Arbeitsstation“ aus.

#### <a name="removal-of-local-admin-rights"></a>Entfernen von lokalen Administratorrechten

Im Rahmen des Rollouts haben Arbeitsstationsbenutzer der VIP- und DevOps-Arbeitsstationen sowie der Arbeitsstationen auf sicherer Ebene keine Administratorrechte auf ihren Computern. Um diese Einstellung im Azure-Portal zu konfigurieren, navigieren Sie zu **Azure Active Directory** > **Geräte** > **Geräteeinstellungen**. Wählen Sie unter **Weitere lokale Administratoren für in Azure AD eingebundene Geräte** die Option **Keine** aus.

#### <a name="require-multi-factor-authentication-to-join-devices"></a>Voraussetzen der Multi-Factor Authentication für das Einbinden von Geräten

Um den Prozess des Einbindens von Geräten in Azure AD weiter zu stärken, navigieren Sie zu **Azure Active Directory** > **Geräte** > **Geräteeinstellungen**, wählen Sie **Ja** unter **Multi-factor Auth zum Hinzufügen von Geräten erforderlich** und dann **Speichern** aus.

#### <a name="configure-mdm"></a>Konfigurieren von MDM

Navigieren Sie im Azure-Portal zu **Azure Active Directory** > **Mobilität (MDM und MAM)**  > **Microsoft Intune**. Ändern Sie die Einstellung **MDM-Benutzerbereich** zu **Alle**, und wählen Sie **Speichern** aus, da in diesem Szenario zugelassen wird, dass jedes Gerät von Intune verwaltet wird. Weitere Informationen finden Sie im Artikel [Schnellstart: Einrichten der automatischen Registrierung für Windows 10-Geräte](https://docs.microsoft.com/Intune/quickstart-setup-auto-enrollment). In einem späteren Schritt erstellen wir Intune-Konfiguration und Compliancerichtlinien.

#### <a name="azure-ad-conditional-access"></a>Bedingter Zugriff auf Azure AD

Bedingter Zugriff auf Azure AD kann dazu beitragen, diese privilegierten administrativen Aufgaben auf konformen Geräten beizubehalten. Benutzer, die wir als Mitglieder der Gruppe **Benutzer der sicheren Arbeitsstation** definiert haben, müssen zur Anmeldung bei Cloudanwendungen die mehrstufige Authentifizierung durchführen. Wir befolgen die Anleitungen zu bewährten Methoden und schließen unsere Notfallzugriffskonten von der Richtlinie aus. Weitere Informationen finden Sie im Artikel [Verwalten von Konten für den Notfallzugriff in Azure AD](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-emergency-access).

Um den bedingten Zugriff über das Azure-Portal zu konfigurieren, navigieren Sie zu **Azure Active Directory** > **Bedingter Zugriff** > **Neue Richtlinie**.

* **Namen**: Erforderliche Richtlinie für sicheres Gerät
* Arbeitsaufträge
   * **Benutzer und Gruppen**
      * Einschließen: **Benutzer und Gruppen**: Wählen Sie die zuvor erstellte Gruppe **Benutzer der sicheren Arbeitsstation** aus
      * Ausschließen: **Benutzer und Gruppen**: Wählen Sie die Notfallzugriffskonten Ihrer Organisation aus
   * **Cloud-Apps**
      * Einschließen: **Alle Cloud-Apps**
* Zugriffssteuerung
   * **Erteilen**: Wählen Sie das Optionsfeld **Zugriff erteilen** aus
      * **Erfordern von Multi-Factor Authentication**
      * **Markieren des Geräts als kompatibel erforderlich**
      * Für mehrere Steuerelemente: **Alle ausgewählten Steuerungen anfordern**
* Richtlinie aktivieren: **Ein**

Organisationen können optional Richtlinien erstellen, um Länder zu blockieren, wo Benutzer nicht auf Unternehmensressourcen zugreifen würden. Weitere Informationen zu Richtlinien für bedingten Zugriff auf IP-Standortbasis finden Sie im Artikel [Was sind Standortbedingungen beim bedingten Zugriff in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/location-condition).

## <a name="intune-configuration"></a>Intune-Konfiguration

### <a name="configure-enrollment-status"></a>Konfigurieren des Registrierungsstatus

Wie im Supply Chain Management beschrieben, sollten um sicherzustellen, dass die sichere Arbeitsstation ein vertrauenswürdiges, reines Gerät ist, neue Geräte beim Kauf werksseitig auf [Windows 10 Pro im S-Modus](https://docs.microsoft.com/Windows/deployment/Windows-10-pro-in-s-mode) eingestellt sein; dies schränkt Anfälligkeit und Sicherheitsrisiken während des Supply Chain Managements ein. Sobald Sie ein Gerät von Ihrem Lieferanten erhalten, wird der S-Modus des Geräts mithilfe von Autopilot aufgehoben. Die folgende Anleitung enthält Informationen zum Anwenden des Transformationsprozesses.

Wir möchten sicherstellen, dass Geräte vor der Verwendung vollständig konfiguriert sind. Intune bietet die Option **Geräteverwendung blockieren, bis alle Apps und Profile installiert sind**. 

1. Navigieren Sie im **Azure-Portal** zu:
1. **Microsoft Intune** > **Geräteregistrierung** > **Windows-Registrierung** > **Seite zum Registrierungsstatus (Vorschau)**  > **Standard** > **Einstellungen**.
1. Setzen Sie **Installationsfortschritt für Apps und Profile anzeigen** auf **Ja**.
1. Setzen Sie **Geräteverwendung blockieren, bis alle Apps und Profile installiert sind** auf **Ja**.

### <a name="create-an-autopilot-deployment-profile"></a>Erstellen eines Autopilot-Bereitstellungsprofils

Nach dem Erstellen einer Gerätegruppe müssen Sie ein Bereitstellungsprofil erstellen, sodass Sie die Autopilot-Geräte konfigurieren können.

1. Wählen Sie in Intune im Azure-Portal **Geräteregistrierung** > **Windows-Registrierung** > **Bereitstellungsprofile** > **Profil erstellen** aus.
1. Geben Sie für „Name“ **Bereitstellungsprofil für sichere Arbeitsstation** ein. Geben Sie für „Beschreibung“ **Bereitstellung von sicheren Arbeitsstationen** ein.
1. Setzen Sie „Alle Zielgeräte in Autopilot-Geräte konvertieren“ auf „Ja“. Diese Einstellung stellt sicher, dass alle Geräte in der Liste mit dem Autopilot-Bereitstellungsdienst registriert werden.  Lassen Sie 48 Stunden zur Verarbeitung der Registrierung verstreichen.
1. Wählen Sie für „Bereitstellungsmodus“ **Selbstbereitstellung (Vorschau)** . Geräte mit diesem Profil sind dem Benutzer zugeordnet, der das Gerät registriert. Anmeldeinformationen sind erforderlich, um das Gerät zu registrieren.
1. Im Feld „Azure AD beitreten als“ sollte **In Azure AD eingebunden** ausgewählt und abgeblendet sein.
1. Wählen Sie „Windows-Willkommensseite“ aus, konfigurieren Sie die folgende Option, behalten Sie bei anderen den Standardwert bei, und wählen Sie dann **OK** aus:
   1. Benutzerkontotyp: **Standard**
1. Wählen Sie **Erstellen**, um das Profil zu erstellen. Das Autopilot-Bereitstellungsprofil steht nun für die Zuweisung von Geräten zur Verfügung.
1. Wählen Sie **Zuweisungen** > **Zuweisen zu** > **Ausgewählte Gruppen** aus
   1. **Wählen Sie die Gruppen aus, die eingeschlossen werden sollen**: Benutzer der sicheren Arbeitsstation

### <a name="configure-windows-update"></a>Konfigurieren des Windows-Updates

Eine der wichtigsten Aufgaben einer Organisation ist, Windows 10 auf dem neuesten Stand zu halten. Um Windows 10 in einem sicheren Zustand zu halten, werden wir einen Updatering bereitstellen, um die Geschwindigkeit zu verwalten, mit der Updates auf Arbeitsstationen angewendet werden. Diese Konfiguration finden Sie unter **Azure-Portal** > **Microsoft Intune** > **Softwareupdates** > **Windows 10-Updateringe**.

Wir **erstellen** einen neuen Updatering mit den folgenden Einstellungen unter Änderung der Standardwerte.

* Name: **Updates für verwaltete Azure-Arbeitsstationen**
* Wartungskanal: **Windows-Insider: schnell**
* Rückstellungszeitraum für Qualitätsupdates (Tage): **3**
* Rückstellungszeitraum für Funktionsupdates (Tage): **3**
* Automatisches Updateverhalten: **Ohne Endbenutzersteuerung automatisch installieren und neu starten**
* Anhalten von Windows-Updates durch Benutzer blockieren: **Blockieren**
* Genehmigung des Benutzers für Neustart außerhalb der Arbeitszeiten erforderlich: **Erforderlich**
* Benutzer (erzwungenen) Neustart ermöglichen: **Erforderlich**
   * Übergang von Benutzern nach einem automatischen Neustart zum erzwungenen Neustart (Tage) : **3**
   * Erinnerung zu engagiertem Neustart zurückstellen (Tage): **3**
   * Stichtag für ausstehende Neustarts festlegen (Tage): **3**

Klicken Sie auf **Erstellen**, und fügen Sie dann auf der Registerkarte **Zuweisungen** die Gruppe **Sichere Arbeitsstationen** als eingeschlossene Gruppe hinzu.

Weitere Informationen zu Windows-Update-Richtlinien finden Sie unter [Richtlinie CSP – Update](https://docs.microsoft.com/windows/client-management/mdm/policy-csp-update)

### <a name="windows-defender-atp-intune-integration"></a>Integration von Windows Defender ATP in Intune

Windows Defender ATP und Microsoft Intune verhindern gemeinsam Sicherheitsverletzungen und begrenzen deren Auswirkungen. Die Funktionen bieten Echtzeiterkennung. ATP ermöglicht auch unsere Bereitstellung umfangreicher Überprüfung und Protokollierung für Endpunktgeräte.

Navigieren Sie zum Konfigurieren der Integration von Windows Defender ATP in Intune im Azure-Portal zu **Microsoft Intune** > **Gerätekonformität** > **Windows Defender ATP**.

1. Klicken Sie in Schritt 1 unter **Konfigurieren von Windows Defender ATP** auf **Verbindungsherstellung zwischen Windows Defender ATP und Microsoft Intune über Windows Defender Security Center**.
1. In Windows Defender Security Center:
   1. Wählen Sie **Einstellungen** > **Erweiterte Features** aus
   1. Wählen Sie für **Microsoft Intune-Verbindung** die Option **Ein** aus
   1. Wählen Sie **Einstellungen speichern** aus
1. Nachdem eine Verbindung hergestellt ist, kehren Sie zu Intune zurück, und klicken Sie oben auf **Aktualisieren**.
1. Setzen Sie **Verbindung von Windows-Geräten mit Version 10.0.15063 und höher mit Windows Defender ATP herstellen** auf **Ein**.
1. **Speichern**

Weitere Informationen finden Sie im Artikel [Microsoft Defender Advanced Threat Protection](https://docs.microsoft.com/Windows/security/threat-protection/windows-defender-atp/windows-defender-advanced-threat-protection).

### <a name="completing-hardening-of-the-workstation-profile"></a>Abschließen des Härtens des Arbeitsstationsprofils

Um das Härten der Lösung erfolgreich abzuschließen, laden Sie das auf der gewünschten **Profilebene** basierende Skript aus dem folgenden Diagramm herunter und führen es aus.

| Profil | Downloadspeicherort | Dateiname |
| --- | --- | --- |
| Niedrige Sicherheit | – |  – |
| Erweiterte Sicherheit | https://aka.ms/securedworkstationgit | Enhanced-Workstation-Windows10-(1809).ps1 |
| Hohe Sicherheit  | https://aka.ms/securedworkstationgit | HighSecurityWorkstation-Windows10-(1809).ps1 |
| Spezialisiert | https://github.com/pelarsen/IntunePowerShellAutomation | DeviceConfiguration_NCSC – Windows10 (1803) SecurityBaseline.ps1 |
| Spezielle Compliance * | https://aka.ms/securedworkstationgit | DeviceCompliance_NCSC-Windows10(1803).ps1 |
| Geschützt | https://aka.ms/securedworkstationgit | Secure-Workstation-Windows10-(1809)-SecurityBaseline.ps1 |

Spezielle Compliance* ist ein Skript, das die in der NCSC Windows 10 SecurityBaseline bereitgestellte spezielle Konfiguration erzwingt.

Wenn das ausgewählte Skript erfolgreich ausgeführt wird, können Updates für Profile und Richtlinien in Microsoft Intune vorgenommen werden. Die Skripts für erweiterte und sichere Profile erstellen Richtlinien und Profile für Sie, aber Sie müssen die Richtlinie Ihrer Gruppe **Sichere Arbeitsstationen** zuweisen.

* Von den Skripts erstellte Intune-Gerätekonfigurationsprofile finden Sie unter **Azure-Portal** > **Microsoft Intune** > **Gerätekonfiguration** > **Profile**.
* Von den Skripts erstellte Compliancerichtlinien finden Sie unter **Azure-Portal** > **Microsoft Intune** > **Gerätekonformität** > **Profile**.

Um die Änderungen zu überprüfen, können Sie auch die Profile exportieren und Änderungen der Exportdatei wie in der SECCON-Dokumentation beschrieben basierend auf einer zusätzlich erforderlichen Härtung übernehmen.

Die Ausführung des Intune-Datenexportskripts `DeviceConfiguration_Export.ps1` aus dem [DeviceConfiguration GitHub-Repository](https://github.com/microsoftgraph/powershell-intune-samples/tree/master/DeviceConfiguration) stellt den aktuellen Export aller vorhandenen Intune-Profile bereit.

## <a name="additional-configurations-and-hardening-to-consider"></a>Zu berücksichtigende zusätzliche Konfigurationen und Härtung

Die vorliegende Anleitung hat eine geschützte Arbeitsstation ermöglicht; weitere Steuerungen sollten auch berücksichtigt werden, wie z.B. alternativer Browserzugriff, Zulassung ausgehenden HTTP-Datenverkehrs und blockierte Websites sowie die Möglichkeit, benutzerdefinierte PowerShell-Skripts auszuführen.

### <a name="restrictive-inbound-and-outbound-rules-in-firewall-configuration-service-provider-csp"></a>Einschränkende Eingangs- und Ausgangsregeln im Firewall-Konfigurationsdienstanbieter (Configuration Service Provider, CSP)

Zusätzliche Verwaltung von Eingangs- und Ausgangsregeln kann aktualisiert werden, um Ihre zulässigen und blockierten Endpunkte widerzuspiegeln. Indem wir das Härten der sicheren Arbeitsstation fortsetzen, erweitern wir die Einschränkung auf die standardmäßige Verweigerung jeglichen Ein- und Ausgangs und fügen zulässige Websites für den Ausgang hinzu, um gängige und vertrauenswürdige Websites widerzuspiegeln. Die zusätzlichen Konfigurationsinformationen für den Firewall-Konfigurationsdienstanbieter finden Sie im Artikel [Firewall-CSP](https://docs.microsoft.com/Windows/client-management/mdm/firewall-csp).

Standardempfehlungen zu Einschränkungen sind:

* Allen Eingang verweigern
* Allen Ausgang verweigern

Das Spamhaus Project verwaltet eine [The Domain Block List (DBL)](https://www.spamhaus.org/dbl/) (Domänenblockierliste) genannte Liste, die Organisationen als Ausgangspunkt für das Blockieren von Websites verwenden können.

### <a name="managing-local-applications"></a>Verwalten von lokalen Anwendungen

Das Entfernen von lokalen Anwendungen einschließlich Produktivitätsanwendungen versetzt die sichere Arbeitsstation in einen wirklich gehärteten Zustand. In unserem Beispiel fügen wir Chrome als Standardbrowser hinzu und beschränken mithilfe von Intune die Möglichkeit, den Browser zu ändern, Plug-Ins eingeschlossen.

#### <a name="deploy-applications-using-intune"></a>Bereitstellen von Anwendungen mit Intune

In einigen Fällen sind Anwendungen wie der Google Chrome-Browser auf der geschützten Arbeitsstation erforderlich. Das folgende Beispiel enthält Anweisungen zum Installieren von Chrome auf Geräten in der zuvor erstellten Sicherheitsgruppe **Sichere Arbeitsstationen**.

1. Laden Sie das Offlineinstallationsprogramm [Chrome-Paket für Windows-64-Bit](https://cloud.google.com/chrome-enterprise/browser/download/) herunter.
1. Extrahieren Sie die Dateien, und notieren Sie sich den Speicherort von `GoogleChromeStandaloneEnterprise64.msi` zum Installieren mit Intune.
1. Navigieren Sie im **Azure-Portal** zu **Microsoft Intune** > **Clientanwendungen** > **Apps** > **Hinzufügen**.
1. Wählen Sie unter **App-Typ** die Option **Branchenspezifisch** aus.
1. Wählen Sie unter **App-Paketdatei** `GoogleChromeStandaloneEnterprise64.msi` am extrahierten Speicherort aus, und klicken Sie auf **OK**.
1. Geben Sie unter **App-Informationen** eine Beschreibung und einen Herausgeber an, und wählen Sie **OK** aus.
1. Klicken Sie auf **Hinzufügen**.
1. Wählen Sie unter **Zuweisungen** **Für registrierte Geräte verfügbar** unter **Zuweisungstyp** aus.
1. Fügen Sie unter **Eingeschlossene Gruppen** die zuvor erstellte Gruppe **Sichere Arbeitsstationen** hinzu.
1. Klicken Sie auf **OK** und dann auf **Speichern**.

Weitere Anleitungen zum Konfigurieren der Chrome-Einstellungen finden Sie im Supportartikel [Manage Chrome Browser with Microsoft Intune](https://support.google.com/chrome/a/answer/9102677) (Verwalten des Chrome-Browsers mit Microsoft Intune).

#### <a name="configuring-the-company-portal-for-custom-apps"></a>Konfigurieren des Unternehmensportals für benutzerdefinierte Apps

Im sicheren Modus ist die Installation von Anwendungen auf das Intune-Unternehmensportal beschränkt. Das Installieren des Portals erfordert jedoch Zugriff auf den Microsoft Store. In unserer sicheren Lösung werden wir das Portal mit einem Offlinemodus des Unternehmensportals für alle Geräte verfügbar machen.

Das Installieren einer von Intune verwalteten Kopie des [Unternehmensportals](https://docs.microsoft.com/Intune/store-apps-company-portal-app) gestattet, bei Bedarf zusätzliche Tools an Benutzer geschützter Arbeitsstationen zu pushen.

Einige Organisationen müssen möglicherweise Windows-32-Bit-Apps installieren oder Apps, deren Bereitstellung weitere Vorbereitungen erfordert. Für diese Anwendungen stellt das [Microsoft Win32 Content Prep Tool](https://github.com/Microsoft/Microsoft-Win32-Content-Prep-Tool) (Vorbereitungstool für Microsoft-Win32-Inhalt) eine einsatzbereite `.intunewin`-Formatdatei für die Installation bereit.

### <a name="setting-up-custom-settings-using-powershell"></a>Einrichten von benutzerdefinierten Einstellungen mithilfe von PowerShell

Wir zeigen außerdem ein Beispiel für die Verwendung von PowerShell zum Ermöglichen von Erweiterbarkeit bei der Verwaltung des Hosts. Das Skript richtet einen Standardhintergrund auf dem Host ein. Diese Funktion ist auch in den Profilen verfügbar und wird nur verwendet, um die Funktion zu veranschaulichen.

In der Lösung müssen möglicherweise einige benutzerdefinierte Steuerelemente und Einstellungen auf geschützten Arbeitsstationen eingerichtet werden. In unserem Beispiel ändern wir den Hintergrund der Arbeitsstation mithilfe von PowerShell, um das Gerät mühelos als eine verwendungsbereite geschützte Arbeitsstation identifizieren zu können. In unserem Beispiel wird zwar PowerShell verwendet, um diese Aufgabe abzuschließen, doch ist dies auch im Azure-Portal möglich.

In unserem Beispiel verwenden wir das folgende [kostenlose generische Hintergrundbild](https://i.imgur.com/OAJ28zO.png) und die Datei [SetDesktopBackground.ps1](https://gallery.technet.microsoft.com/scriptcenter/Set-Desktop-Image-using-5430c9fb/) aus dem Microsoft Scripting Center, um Windows das Laden des Hintergrunds beim Start zu ermöglichen.

1. Laden Sie das Skript auf ein lokales Gerät herunter.
1. Aktualisieren Sie „customerXXXX“ und den Downloadspeicherort des Hintergrunds, den Sie im Skript verwenden möchten, gemäß der Hintergrunddatei und des Ordners, den die Bereitstellung verwenden soll. In unserem Beispiel ersetzen wir „customerXXXX“ mit „Hintergründe“.  
1. Navigieren Sie zu **Azure-Portal** > **Microsoft Intune** > **Gerätekonfiguration** > **PowerShell-Skripts** > **Hinzufügen**.
1. Geben Sie einen **Namen** für das Skript ein, und geben Sie den **Skriptspeicherort** an, an den es heruntergeladen werden soll.
1. Wählen Sie **Konfigurieren** aus.
   1. Setzen Sie **Dieses Skript mit den Anmeldeinformationen des angemeldeten Benutzers ausführen** auf **Ja**.
   1. Klicken Sie auf **OK**
1. Klicken Sie auf **Erstellen**
1. Wählen Sie **Zuweisungen** > **Gruppen auswählen** aus.
   1. Fügen Sie die zuvor erstellte Sicherheitsgruppe **Sichere Arbeitsstationen** hinzu, und klicken Sie auf **Speichern**.

### <a name="using-the-preview-mdm-security-baseline-for-october-2018"></a>Verwendung der Vorschau: MDM-Sicherheitsbaseline für Oktober 2018

Microsoft Intune hat das Sicherheitsbaselinemanagement-Feature eingeführt, dass Administratoren ermöglicht, mühelos einen gemeinsamen Baselinesicherheitsstatus zu erzwingen. Die Baseline bietet ein ähnliches Mittel, um eine Arbeitsstation mit erweitertem Profil zu sperren.

Für die geschützte Workstation wird die Implementierung dieser Baseline nicht verwendet, weil ein Konflikt mit der Bereitstellung der sicheren Konfiguration entstehen würde.

|   |   |   |
| :---: | :---: | :---: |
| Sperrbildschirm | Geräteinstallation | Remotedesktopdienste |
| App-Runtime | Gerätesperre | Remoteverwaltung |
| Anwendungsverwaltung | Ereignisprotokolldienst | Remoteprozeduraufruf |
| Automatische Wiedergabe | Benutzererfahrung | Suchen, |
| BitLocker | Exploit Guard | SmartScreen |
| "Browser" | Datei-Explorer | Systemanforderungen|
| Konnektivität | Internet Explorer | WLAN |
| Delegierung von Anmeldeinformationen | Sicherheitsoptionen für lokale Richtlinien | Windows Connection Manager |
| Benutzeroberfläche für Anmeldeinformationen | MS Security Guide | Windows Defender|
| Datenschutz | MSS Legacy | Windows Ink-Arbeitsbereich |
| Device Guard | Potenz | Windows PowerShell |

Weitere Informationen zu dieser Previewfunktion finden Sie im Artikel [Windows-Sicherheitsbaselineeinstellungen für Intune](https://docs.microsoft.com/Intune/security-baseline-settings-windows).

## <a name="enroll-and-validate-your-first-device"></a>Registrieren und Überprüfen Ihres ersten Geräts

1. Zum Registrieren Ihres Geräts benötigen Sie die folgenden Informationen:
   * **Seriennummer**: befindet sich am Gehäuse des Geräts.
   * **Windows-Produkt-ID**: unter **System** > **Info** im Menü „Windows-Einstellungen“.
   * Bei Ausführung von [Get-WindowsAutoPilotInfo](https://aka.ms/Autopilotshell) wird eine CSV-Hash-Datei für die Geräteregistrierung mit allen erforderlichen Informationen generiert. 
      * Führen Sie `Get-WindowsAutoPilotInfo – outputfile device1.csv` aus, um die Informationen als CSV-Datei auszugeben, die in Intune importiert werden kann.

   > [!NOTE]
   > Das Skript erfordert erhöhte Rechte und wird als remote signiert ausgeführt. Sie können den folgenden Befehl verwenden, damit das Skript ordnungsgemäß ausgeführt wird. `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned`

   *  Sie können diese Informationen sammeln, indem Sie sich bei einem Gerät für Windows 10 Version 1809 oder höher anmelden, um die Informationen zu sammeln, oder Ihr Hardwarehändler kann Ihnen diese Informationen bei der Bestellung neuer Geräte geben.
1. Sammeln Sie die erforderlichen Informationen, und kehren Sie zum **Azure-Portal** zurück. Navigieren Sie zu **Microsoft Intune** > **Geräteregistrierung** > **Windows-Registrierung** > **Geräte: Windows AutoPilot-Geräte verwalten**, wählen Sie **Import** aus und dann die CSV-Datei, die Sie erstellt haben oder die bereitgestellt wurde.
1. Fügen Sie das jetzt registrierte Gerät der zuvor erstellten Sicherheitsgruppe **Sichere Arbeitsstationen** hinzu.
1. Navigieren Sie auf dem Windows 10-Gerät, das Sie konfigurieren möchten, zu **Windows-Einstellungen** > **Update und Sicherheit** > **Wiederherstellung**. Wählen Sie **Erste Schritte** unter **Diesen PC zurücksetzen** aus, und befolgen Sie die Eingabeaufforderungen zum Zurücksetzen und erneuten Konfigurieren des Geräts mithilfe der konfigurierten Profil- und Konformitätsrichtlinien.

Nachdem das Gerät konfiguriert wurde, überprüfen Sie die Konfiguration. Vergewissern Sie sich, dass das erste Gerät ordnungsgemäß konfiguriert ist, bevor Sie Ihre Bereitstellung fortsetzen.

## <a name="assignment-and-monitoring"></a>Zuweisung und Überwachung

Zuweisen von Geräten und Benutzern erfordert die Zuordnung der [ausgewählten Profile](https://docs.microsoft.com/intune/device-profile-assign) zu Ihrer Sicherheitsgruppe, und alle neuen Benutzer, die Berechtigungen für den Dienst erhalten, müssen auch der Sicherheitsgruppe hinzugefügt werden.

Das Überwachen der Profile kann mithilfe der Überwachung [Microsoft Intune-Profile](https://docs.microsoft.com/intune/device-profile-monitor) erfolgen.

## <a name="next-steps"></a>Nächste Schritte

[Microsoft Intune](https://docs.microsoft.com/intune/index)-Dokumentation

[Azure AD](https://docs.microsoft.com/azure/active-directory/index)-Dokumentation