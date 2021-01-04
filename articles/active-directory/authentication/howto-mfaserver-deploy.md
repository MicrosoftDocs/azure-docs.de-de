---
title: Erste Schritte mit Azure MFA-Server – Azure Active Directory
description: Erste Schritte mit lokalen Azure MFA-Servern
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: d7bd9913cccbe077a4deed9a7c5bfdc601f3dd5e
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/06/2020
ms.locfileid: "96742339"
---
# <a name="getting-started-with-the-azure-multi-factor-authentication-server"></a>Erste Schritte mit Azure Multi-Factor Authentication-Server

<center>

![Erste Schritte mit einem lokalen MFA-Server](./media/howto-mfaserver-deploy/server2.png)</center>

Auf dieser Seite werden eine Neuinstallation des Servers und die Einrichtung mit einer lokalen Active Directory-Instanz behandelt. Falls Sie den MFA-Server bereits installiert haben und ein Upgrade vornehmen möchten, lesen Sie unter [Upgrade to the latest Azure Multi-Factor Authentication Server](howto-mfaserver-deploy-upgrade.md) (Upgraden auf den neuesten Azure Multi-Factor Authentication-Server) weiter. Falls Sie nur den Webdienst installieren möchten, lesen Sie unter [Aktivieren der Mobile App-Authentifizierung mit dem Azure Multi-Factor Authentication-Server](howto-mfaserver-deploy-mobileapp.md) weiter.

> [!IMPORTANT]
> Seit dem 1. Juli 2019 bietet Microsoft für neue Bereitstellungen keine MFA-Server mehr an. Neue Kunden, die bei Benutzeranmeldeereignissen eine mehrstufige Authentifizierung (Multi-Factor Authentication, MFA) verlangen möchten, sollten cloudbasierte Azure AD Multi-Factor Authentication verwenden.
>
> Informationen zu den ersten Schritten mit der cloudbasierten MFA finden Sie im [Tutorial: Schützen von Benutzeranmeldeereignissen mit Azure AD Multi-Factor Authentication](tutorial-enable-azure-mfa.md).
>
> Bestandskunden, die ihren MFA-Server vor dem 1. Juli 2019 aktiviert haben, können weiterhin die neuesten Versionen und zukünftige Updates herunterladen sowie Anmeldedaten zur Aktivierung generieren.

## <a name="plan-your-deployment"></a>Planen der Bereitstellung

Bevor Sie den Azure Multi-Factor Authentication-Server herunterladen, sollten Sie überlegen, wie Ihre Last- und Hochverfügbarkeitsanforderungen sind. Verwenden Sie diese Informationen, um zu entscheiden, wie und wo Sie bereitstellen möchten.

Ein guter Anhaltspunkt für den benötigten Speicherplatz ist die Anzahl der Benutzer, von denen Sie erwarten, dass sie sich regelmäßig authentifizieren.

| Benutzer | RAM |
| ----- | --- |
| 1-10,000 | 4 GB |
| 10,001-50,000 | 8 GB |
| 50,001-100,000 | 12 GB |
| 100,000-200,001 | 16 GB |
| 200,001+ | 32 GB |

Müssen Sie mehrere Server für Hochverfügbarkeit oder Lastenausgleich einrichten? Es gibt zahlreiche Möglichkeiten, um diese Konfiguration mit Azure MFA-Server einzurichten. Wenn Sie Ihren ersten Azure MFA-Server installieren, wird dieser der Master. Alle weiteren Server werden untergeordnet und synchronisieren Benutzer und die Konfiguration automatisch mit dem Master. Anschließend können Sie einen primären Server konfigurieren und die übrigen als Sicherung verwenden, oder Lastenausgleich zwischen allen Servern einrichten.

Wenn ein Azure MFA-Masterserver offline geschaltet wird, können die untergeordneten Server weiterhin Anforderungen für die zweistufige Überprüfung verarbeiten. Sie können jedoch keine neuen Benutzer hinzufügen, und vorhandene Benutzer können ihre Einstellungen nicht aktualisieren, bis der Master wieder online geschaltet oder ein untergeordneter Server höher gestuft wird.

### <a name="prepare-your-environment"></a>Vorbereiten der Umgebung

Stellen Sie sicher, dass der Server, den Sie für Azure Multi-Factor Authentication verwenden, die folgenden Anforderungen erfüllt:

| Anforderungen an den Azure Multi-Factor Authentication-Server | BESCHREIBUNG |
|:--- |:--- |
| Hardware |<li>200 MB Festplattenspeicher</li><li>x32- oder x64-fähiger Prozessor</li><li>1 GB oder mehr RAM</li> |
| Software |<li>Windows Server 2016</li><li>Windows Server 2012 R2</li><li>Windows Server 2012</li><li>Windows Server 2008/R2 (nur mit [ESU](/lifecycle/faq/extended-security-updates))</li><li>Windows 10</li><li>Windows 8.1, alle Editionen</li><li>Windows 8, alle Editionen</li><li>Windows 7, alle Editionen (nur mit [ESU](/lifecycle/faq/extended-security-updates) )</li><li>Microsoft .NET 4.0 Framework</li><li>IIS 7.0 oder höher bei Installation des Benutzerportals oder des Webdienst-SDK</li> |
| Berechtigungen | Domänenadministrator- oder Unternehmensadministratorkonto zum Registrieren in Active Directory |

### <a name="azure-mfa-server-components"></a>Komponenten des Azure MFA-Servers

Der Azure MFA-Server setzt sich aus drei Webkomponenten zusammen:

* Webdienst-SDK: Ermöglicht die Kommunikation mit den anderen Komponenten und wird auf dem Azure MFA-Anwendungsserver installiert.
* Benutzerportal: Eine IIS-Website, die es Benutzern ermöglicht, sich für Azure Multi-Factor Authentication (MFA) zu registrieren und ihre Konten zu verwalten.
* Webdienst der mobilen App: Ermöglicht die Verwendung einer mobilen App wie Microsoft Authenticator für die zweistufige Überprüfung.

Alle drei Komponenten können auf dem gleichen Server installiert werden, sofern dieser über Internetzugriff verfügt. Wenn die Komponenten aufgeteilt werden, wird das Webdienst-SDK auf dem Azure MFA-Anwendungsserver installiert, während das Benutzerportal und der Webdienst der mobilen App auf einem Server mit Internetzugriff installiert werden.

### <a name="azure-multi-factor-authentication-server-firewall-requirements"></a>Anforderungen an die Azure Multi-Factor Authentication-Server-Firewall

Für jeden MFA-Server muss die Kommunikation über den ausgehenden Port 443 zu folgenden Adressen möglich sein:

* https://pfd.phonefactor.net
* https://pfd2.phonefactor.net
* https://css.phonefactor.net

Öffnen Sie die folgenden IP-Adressbereiche, wenn Firewalls auf den ausgehenden Port 443 beschränkt sind:

| IP-Subnetz | Netzmaske | IP-Bereich |
|:---: |:---: |:---: |
| 134.170.116.0/25 |255.255.255.128 |134.170.116.1–134.170.116.126 |
| 134.170.165.0/25 |255.255.255.128 |134.170.165.1–134.170.165.126 |
| 70.37.154.128/25 |255.255.255.128 |70.37.154.129–70.37.154.254   |
| 52.251.8.48/28   | 255.255.255.240 | 52.251.8.48 - 52.251.8.63     |
| 52.247.73.160/28 | 255.255.255.240 | 52.247.73.160 - 52.247.73.175 |
| 52.159.5.240/28  | 255.255.255.240 | 52.159.5.240 - 52.159.5.255   |
| 52.159.7.16/28   | 255.255.255.240 | 52.159.7.16 - 52.159.7.31     |
| 52.250.84.176/28 | 255.255.255.240 | 52.250.84.176 - 52.250.84.191 |
| 52.250.85.96/28  | 255.255.255.240 | 52.250.85.96 - 52.250.85.111  |

Wenn Sie das Ereignisbestätigungsfeature nicht verwenden und Ihre Benutzer keine mobilen Apps nutzen, um eine Verifizierung über Geräte im Unternehmensnetzwerk durchzuführen, benötigen Sie lediglich folgende Bereiche:

| IP-Subnetz | Netzmaske | IP-Bereich |
|:---: |:---: |:---: |
| 134.170.116.72/29 |255.255.255.248 |134.170.116.72–134.170.116.79|
| 134.170.165.72/29 |255.255.255.248 |134.170.165.72–134.170.165.79|
| 70.37.154.200/29 |255.255.255.248  |70.37.154.201–70.37.154.206  |
| 52.251.8.48/28   | 255.255.255.240 | 52.251.8.48 - 52.251.8.63     |
| 52.247.73.160/28 | 255.255.255.240 | 52.247.73.160 - 52.247.73.175 |
| 52.159.5.240/28  | 255.255.255.240 | 52.159.5.240 - 52.159.5.255   |
| 52.159.7.16/28   | 255.255.255.240 | 52.159.7.16 - 52.159.7.31     |
| 52.250.84.176/28 | 255.255.255.240 | 52.250.84.176 - 52.250.84.191 |
| 52.250.85.96/28  | 255.255.255.240 | 52.250.85.96 - 52.250.85.111  |

## <a name="download-the-mfa-server"></a>Herunterladen des MFA-Servers

Führen Sie die folgenden Schritte aus, um den Azure Multi-Factor Authentication-Server im Azure-Portal herunterzuladen:

> [!IMPORTANT]
> Seit dem 1. Juli 2019 bietet Microsoft für neue Bereitstellungen keine MFA-Server mehr an. Neue Kunden, die eine mehrstufige Authentifizierung für ihre Benutzer einrichten möchten, können die cloudbasierte Azure AD Multi-Factor Authentication verwenden.
>
> Informationen zu den ersten Schritten mit der cloudbasierten MFA finden Sie im [Tutorial: Schützen von Benutzeranmeldeereignissen mit Azure AD Multi-Factor Authentication](tutorial-enable-azure-mfa.md).
>
> Bestandskunden, die ihren MFA-Server vor dem 1. Juli 2019 aktiviert haben, können weiterhin die neuesten Versionen und zukünftige Updates herunterladen sowie Anmeldedaten zur Aktivierung generieren. Die folgenden Schritte können nur bei vorhandenen MFA Server-Kunden angewandt werden.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) als Administrator an.
2. Suchen Sie nach *Azure Active Directory*, und wählen Sie diese Option aus. Wählen Sie **Sicherheit** > **MFA** aus.
3. Wählen Sie unter **MFA-Server verwalten** die Option **Servereinstellungen** aus.
4. Wählen Sie **Herunterladen** aus, und befolgen Sie die Anweisungen auf der Downloadseite, um das Installationsprogramm zu speichern. 

   ![Herunterladen von MFA-Server aus dem Azure-Portal](./media/howto-mfaserver-deploy/downloadportal.png)

5. Lassen Sie diese Seite geöffnet. Sie wird nach dem Ausführen des Installationsprogramms erneut benötigt.

## <a name="install-and-configure-the-mfa-server"></a>Installieren und Konfigurieren des MFA-Servers

Nachdem Sie den Server heruntergeladen haben, können Sie ihn installieren und konfigurieren. Achten Sie darauf, dass der Computer, auf dem Sie den Server installieren möchten, die im Abschnitt „Planung“ aufgeführten Anforderungen erfüllt.

1. Doppelklicken Sie auf die ausführbare Datei.
2. Vergewissern Sie sich, dass auf dem Bildschirm „Installationsordner auswählen“ der richtige Ordner ausgewählt ist, und klicken Sie auf **Weiter**.
3. Klicken Sie nach Abschluss der Installation auf **Fertig stellen**. Der Konfigurations-Assistent wird gestartet.
4. Aktivieren Sie auf der Willkommensseite des Konfigurations-Assistenten das Kontrollkästchen **Verwendung des Authentifizierungskonfigurations-Assistenten überspringen**, und klicken Sie auf **Weiter**. Der Assistent wird geschlossen, und der Server startet.

   ![Verwendung des Authentifizierungskonfigurations-Assistenten überspringen](./media/howto-mfaserver-deploy/skip2.png)

5. Wechseln Sie zurück zu der Seite, von der Sie den Server heruntergeladen haben, und klicken Sie auf die Schaltfläche **Anmeldeinformationen für Aktivierung generieren**. Kopieren Sie diese Informationen auf dem Azure MFA-Server in die angezeigten Felder, und klicken Sie auf **Aktivieren**.

> [!NOTE]
> Nur globale Administratoren können Aktivierungsanmeldeinformationen im Azure-Portal generieren.

## <a name="send-users-an-email"></a>Senden einer E-Mail an Benutzer

Erlauben Sie dem MFA-Server die Kommunikation mit Ihren Benutzern, um das Rollout zu vereinfachen. Der MFA-Server kann die Benutzer per E-Mail darüber informieren, dass sie für die zweistufige Überprüfung registriert wurden.

Die gesendete E-Mail sollte sich danach richten, wie Sie die Benutzer für die zweistufige Überprüfung konfigurieren. Falls Sie also beispielsweise die Telefonnummern aus dem Telefonverzeichnis Ihres Unternehmens importieren können, sollte die E-Mail die Standardnummern enthalten, damit die Benutzer wissen, was sie erwartet. Falls Sie keine Telefonnummern importieren oder Ihre Benutzer die mobile App verwenden, senden Sie ihnen eine E-Mail, in der sie aufgefordert werden, ihre Kontoregistrierung abzuschließen. Schließen Sie in die E-Mail einen Link zum Benutzerportal für Azure Multi-Factor Authentication ein.

Der Inhalt der E-Mail variiert auch abhängig von der Überprüfungsmethode, die für den Benutzer festgelegt wurde (Telefonanruf, SMS oder mobile App). Wenn der Benutzer beispielsweise bei der Authentifizierung eine PIN verwenden muss, wird er per E-Mail informiert, wie seine anfängliche PIN festgelegt wurde. Benutzer müssen ihre PIN während der ersten Überprüfung ändern.

### <a name="configure-email-and-email-templates"></a>Konfigurieren von E-Mails und E-Mail-Vorlagen

Klicken Sie auf das E-Mail-Symbol auf der linken Seite, um die Einstellungen zum Senden dieser E-Mails einzurichten. Auf dieser Seite können Sie die SMTP-Informationen Ihres E-Mailservers eingeben und E-Mails senden, indem Sie das Kontrollkästchen **E-Mails an Benutzer senden** aktivieren.

![MFA-Server – E-Mail-Konfiguration](./media/howto-mfaserver-deploy/email1.png)

Auf der Registerkarte „E-Mail-Inhalt“ werden alle E-Mail-Vorlagen angezeigt, die Sie auswählen können. Je nachdem, wie Sie Ihre Benutzer für die zweistufige Überprüfung konfiguriert haben, können Sie also die am besten geeignete Vorlage auswählen.

![MFA-Server: E-Mail-Vorlagen in der Konsole](./media/howto-mfaserver-deploy/email2.png)

## <a name="import-users-from-active-directory"></a>Importieren von Benutzern aus Active Directory

Nach Abschluss der Serverinstallation können Sie Benutzer hinzufügen. Sie können die Benutzer entweder manuell erstellen, aus Active Directory importieren oder eine automatische Synchronisierung mit Active Directory konfigurieren.

### <a name="manual-import-from-active-directory"></a>Manuelles Importieren aus Active Directory

1. Wählen Sie auf dem Azure MFA-Server auf der linken Seite **Benutzer** aus.
2. Wählen Sie unten **Aus Active Directory importieren** aus.
3. Sie können jetzt entweder nach einzelnen Benutzern suchen oder im AD-Verzeichnis nach Organisationseinheiten suchen, die die gewünschten Benutzer enthalten. In diesem Beispiel wird die Benutzer-Organisationseinheit angegeben.
4. Markieren Sie alle Benutzer auf der rechten Seite, und klicken Sie auf **Importieren**. Es wird ein Popupfenster angezeigt, wenn der Vorgang erfolgreich ausgeführt wurde. Schließen Sie das Fenster "Importieren".

   ![MFA-Server: Benutzerimport aus Active Directory](./media/howto-mfaserver-deploy/import2.png)

### <a name="automated-synchronization-with-active-directory"></a>Automatische Synchronisierung mit Active Directory

1. Wählen Sie auf dem Azure MFA-Server im linken Bereich die Option **Verzeichnisintegration** aus.
2. Navigieren Sie zur Registerkarte **Synchronisierung**.
3. Wählen Sie am unteren Rand die Option **Hinzufügen** aus.
4. Wählen Sie im daraufhin angezeigten Feld **Synchronisierungselement hinzufügen** die Domäne, Organisationseinheit **oder** Sicherheitsgruppe, Einstellungen, Methodenstandardeinstellungen und Sprachstandardeinstellungen für die Synchronisierungsaufgabe aus, und klicken Sie anschließend auf **Hinzufügen**.
5. Aktivieren Sie das Kontrollkästchen **Synchronisierung mit Active Directory aktivieren**, und wählen Sie ein **Synchronisierungsintervall** zwischen einer Minute und 24 Stunden aus.

## <a name="how-the-azure-multi-factor-authentication-server-handles-user-data"></a>Verarbeitung von Benutzerdaten durch den Azure Multi-Factor Authentication-Server

Bei lokaler Verwendung des Multi-Factor Authentication (MFA)-Servers werden die Daten eines Benutzers auf den lokalen Servern gespeichert. Daten werden nicht dauerhaft in der Cloud gespeichert. Wenn der Benutzer eine zweistufige Überprüfung durchführt, sendet der MFA-Server Daten an den Azure MFA-Clouddienst, um die Überprüfung durchzuführen. Wenn diese Authentifizierungsanforderungen an den Clouddienst gesendet werden, werden in der Anforderung und den Protokollen die folgenden Felder gesendet, damit sie in den Authentifizierungs-/Verwendungsberichten des Kunden verfügbar sind. Einige Felder sind optional und können für den Multi-Factor Authentication-Server aktiviert oder deaktiviert werden. Für die Kommunikation zwischen MFA-Server und MFA-Clouddienst wird SSL/TLS über den ausgehenden Port 443 verwendet. Die Felder lauten:

* Eindeutige ID: Benutzername oder interne ID des MFA-Servers
* Vor- und Nachname (optional)
* E-Mail-Adresse (optional)
* Rufnummer: für eine Anruf- oder SMS-Authentifizierung
* Gerätetoken: für die Authentifizierung einer mobilen App
* Authentifizierungsmodus
* Authentifizierungsergebnis
* Name des MFA-Servers
* MFA-Server-IP
* Client-IP – falls verfügbar

Zusätzlich zu den obigen Feldern werden auch das Überprüfungsergebnis (Erfolg/Ablehnung) und der Grund für etwaige Ablehnungen mit den Authentifizierungsdaten gespeichert. Sie stehen in den Authentifizierungs- bzw. Verwendungsberichten zur Verfügung.

> [!IMPORTANT]
> Ab März 2019 sind die Telefonanrufoptionen für MFA Server-Benutzer in kostenlosen bzw. Testversionen von Azure AD-Mandanten nicht mehr verfügbar. SMS-Nachrichten sind von dieser Änderung nicht betroffen. Für Benutzer in kostenpflichtigen Azure AD-Mandanten ist die Telefonanrufoption weiterhin verfügbar. Diese Änderung wirkt sich nur auf kostenlose bzw. Testversionen von Azure AD-Mandanten aus.

## <a name="back-up-and-restore-azure-mfa-server"></a>Sichern und Wiederherstellen des Azure MFA-Servers

Die Erstellung angemessener Sicherungen ist ein wichtiger Aspekt jedes Systems.

Vergewissern Sie sich beim Sichern des Azure MFA-Servers, dass Sie über eine Kopie des Ordners **C:\Programme\Multi-Factor Authentication Server\Data** einschließlich der Datei **PhoneFactor.pfdata** verfügen. 

Sollte eine Wiederherstellung erforderlich sein, gehen Sie wie folgt vor:

1. Installieren Sie den Azure MFA-Server auf einem neuen Server.
2. Aktivieren Sie den neuen Azure MFA-Server.
3. Beenden Sie den Dienst **MultiFactorAuth**.
4. Überschreiben Sie die Datei **PhoneFactor.pfdata** mit der gesicherten Kopie.
5. Starten Sie den Dienst **MultiFactorAuth**.

Der neue Server ist nun aktiv und wird mit den ursprünglichen Konfigurations- und Benutzerdaten aus der Sicherung ausgeführt.

## <a name="managing-the-tlsssl-protocols-and-cipher-suites"></a>Verwalten von TLS/SSL-Protokollen und Verschlüsselungssammlungen

Nachdem Sie MFA Server Version 8.x oder höher installiert bzw. das Upgrade darauf durchgeführt haben, ist es ratsam, ältere und weniger leistungsstarke Verschlüsselungssammlungen zu deaktivieren oder zu entfernen, sofern diese nicht von Ihrer Organisation benötigt werden. Informationen zur Durchführung dieser Aufgabe finden Sie im Artikel [Verwalten von SSL/TLS-Protokollen und Verschlüsselungssammlungen für AD FS](/windows-server/identity/ad-fs/operations/manage-ssl-protocols-in-ad-fs).

## <a name="next-steps"></a>Nächste Schritte

- Einrichten und Konfigurieren des [Benutzerportals](howto-mfaserver-deploy-userportal.md) für Self-Service
- Einrichten und Konfigurieren des Azure MFA-Servers mit [Active Directory-Verbunddienste](multi-factor-authentication-get-started-adfs.md), [RADIUS-Authentifizierung](howto-mfaserver-dir-radius.md) oder [LDAP-Authentifizierung](howto-mfaserver-dir-ldap.md)
- Einrichten und Konfigurieren von [Remotedesktopgateway und Azure Multi-Factor Authentication-Server mithilfe von RADIUS](howto-mfaserver-nps-rdg.md)
- [Bereitstellen des mobilen App-Webdiensts für den Azure Multi-Factor Authentication-Server](howto-mfaserver-deploy-mobileapp.md)
- [Erweiterte Szenarien mit Azure Multi-Factor Authentication und Drittanbieter-VPNs](howto-mfaserver-nps-vpn.md)