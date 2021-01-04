---
title: Integrieren von RDG mit der NPS-Erweiterung für Azure AD MFA – Azure Active Directory
description: Integrieren Ihrer Remotedesktopgateway-Infrastruktur unter Verwendung der Netzwerkrichtlinienserver-Erweiterung für Microsoft Azure in Azure AD MFA
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
ms.openlocfilehash: 20b0150c18f2c007ed104d34daacd49ab03131a7
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/06/2020
ms.locfileid: "96743376"
---
# <a name="integrate-your-remote-desktop-gateway-infrastructure-using-the-network-policy-server-nps-extension-and-azure-ad"></a>Integrieren Sie Ihre Remotedesktopgateway-Infrastruktur mit der Netzwerkrichtlinienserver-Erweiterung (Network Policy Server, NPS) und Azure AD

Dieser Artikel bietet weitere Informationen zur Integration Ihrer Remotedesktopgateway-Infrastruktur mit Azure AD Multi-Factor Authentication (MFA) mit der Netzwerkrichtlinienserver-Erweiterung (Network Policy Server, NPS) für Microsoft Azure.

Die Netzwerkrichtlinienserver-Erweiterung (NPS) für Azure ermöglicht Kunden, ihre RADIUS-Clientauthentifizierung (Remote Authentication Dial-in User Service) mit der cloudbasierten [Multi-Factor Authentication (MFA)](./concept-mfa-howitworks.md) von Azure zu schützen. Diese Lösung ermöglicht die zweistufige Überprüfung, um eine zweite Sicherheitsebene für Benutzeranmeldungen und Transaktionen hinzuzufügen.

Dieser Artikel enthält ausführliche Anleitungen zum Integrieren der NPS-Infrastruktur in Azure AD MFA mithilfe der NPS-Erweiterung für Azure. Dies ermöglicht eine sichere Überprüfung von Benutzern, die sich bei einem Remotedesktopgateway anmelden.

> [!NOTE]
> Dieser Artikel sollte nicht für Bereitstellungen von MFA Server verwendet werden, sondern nur für (cloudbasierte) Azure AD MFA-Bereitstellungen.

Die Netzwerkrichtlinien- und Zugriffsdienste (Network Policy and Access Services, NPS) bieten Organisationen folgende Möglichkeiten:

* Definieren zentraler Orte für die Verwaltung und Steuerung von Netzwerkanforderungen, um Folgendes anzugeben: wer eine Verbindung herstellen kann, zu welchen Tageszeiten Verbindungen zugelassen sind, die Dauer der Verbindungen und die Sicherheitsstufe, die Clients verwenden müssen, um eine Verbindung herstellen zu können usw. Anstatt diese Richtlinien auf jedem VPN oder Remotedesktopgateway-Server (RD) anzugeben, können diese Richtlinien einmal an einem zentralen Ort angegeben werden. Das RADIUS-Protokoll ermöglicht die zentralisierte Authentifizierung, Autorisierung und Ressourcenerfassung (Authentication, Authorization, Accounting – AAA).
* Richten Sie Netzwerkzugriffsschutz-Clientintegritätsrichtlinien (Network Access Protection, NAP) ein, die bestimmen, ob Geräten uneingeschränkter oder eingeschränkter Zugriff auf Netzwerkressourcen gewährt wird, und erzwingen Sie deren Durchsetzung.
* Bieten Sie eine Möglichkeit zum Erzwingen der Authentifizierung und Autorisierung für den Zugriff auf 802.1x-fähige Drahtloszugriffspunkte und Ethernet-Switches.

In der Regel verwenden Organisationen NPS (RADIUS) zur Vereinfachung und Zentralisierung der Verwaltung von VPN-Richtlinien. Viele Organisationen verwenden NPS jedoch auch zur Vereinfachung und Zentralisierung der Verwaltung der RD-Verbindungsautorisierungsrichtlinien (RD-CAPs, Remote Desktop Connection Authorization Policies).

Organisationen können NPS auch mit Azure AD MFA integrieren, um die Sicherheit zu erhöhen und ein hohes Maß an Konformität bereitzustellen. Dadurch wird sichergestellt, dass Benutzer die zweistufige Überprüfung zur Anmeldung beim Remotedesktopgateway einrichten. Damit Benutzern Zugriff gewährt wird, müssen sie die von ihnen festgelegte Kombination aus Benutzername und Kennwort angeben. Diese Informationen müssen vertrauenswürdig und dürfen nicht problemlos duplizierbar sein, z.B. eine Mobiltelefonnummer, Festnetznummer, Anwendung auf einem mobilen Gerät usw. RDG unterstützt derzeit Telefonanruf- und Pushbenachrichtigungen von Microsoft Authenticator-App-Methoden für die zweistufige Authentifizierung (2FA). Weitere Informationen zu unterstützten Authentifizierungsmethoden finden Sie im Abschnitt [Bestimmen Sie die Authentifizierungsmethoden, die Ihre Benutzer verwenden können](howto-mfa-nps-extension.md#determine-which-authentication-methods-your-users-can-use).

Vor der Verfügbarkeit der NPS-Erweiterung für Azure mussten Kunden, die die zweistufige Überprüfung für integrierte NPS- und Azure AD MFA-Umgebungen implementieren wollten, einen separaten MFA-Server in der lokalen Umgebung konfigurieren und verwalten, wie in [Remotedesktopgateway und Azure Multi-Factor Authentication-Server mithilfe von RADIUS](howto-mfaserver-nps-rdg.md) dokumentiert.

Die Verfügbarkeit der NPS-Erweiterung für Azure bietet Organisationen jetzt die Möglichkeit, entweder eine lokal basierte oder eine cloudbasierte MFA-Lösung zum Schützen der RADIUS-Clientauthentifizierung bereitzustellen.

## <a name="authentication-flow"></a>Authentifizierungsablauf

Damit Benutzern der Zugriff auf Netzwerkressourcen über ein Remotedesktopgateway erteilt wird, müssen sie die in einer Remotedesktop-Verbindungsautorisierungsrichtlinie (RD-CAP) und einer Remotedesktop-Ressourcenautorisierungsrichtlinie (RD Resource Authorization Policy, RD-RAP) angegebenen Bedingungen erfüllen. RD-CAPs geben an, wer zum Herstellen einer Verbindung mit RD-Gateways autorisiert ist. RD-RAPs geben die Netzwerkressourcen an, wie z.B. Remotedesktops oder Remote-Apps, mit denen der Benutzer über das RD-Gateway eine Verbindung mit zulässig ist.

Ein RD-Gateway kann konfiguriert werden, um einen zentralen Richtlinienspeicher für RD-CAPs zu verwenden. RD-RAPs können keine zentrale Richtlinie verwenden, da sie auf dem RD-Gateway verarbeitet werden. Ein Beispiel eines RD-Gateways, das zur Verwendung eines zentralen Richtlinienspeichers für RD-CAPs konfiguriert ist, ist ein RADIUS-Client einer anderen NPS-Serverinstanz, die als zentraler Richtlinienspeicher dient.

Wenn die NPS-Erweiterung für Azure in NPS und Remotedesktopgateway integriert ist, lautet der erfolgreiche Authentifizierungsablauf wie folgt:

1. Der Remotedesktopgateway-Server empfängt eine Authentifizierungsanforderung von einem Remotedesktop-Benutzer zum Herstellen der Verbindung mit einer Ressource, z.B. einer Remotedesktopsitzung. Als RADIUS-Client fungierend konvertiert der Remotedesktopgateway-Server die Anforderung in eine RADIUS-Zugriffsanforderungsnachricht und sendet die Nachricht an den RADIUS-Server (NPS), auf dem die NPS-Erweiterung installiert ist.
1. Die Kombination aus Benutzername und Kennwort wird in Active Directory überprüft, und der Benutzer ist authentifiziert.
1. Wenn alle Bedingungen entsprechend den Angaben in der NPS-Verbindungsanforderung und den Netzwerkrichtlinien erfüllt sind (z. B. Uhrzeit- oder Gruppenmitgliedschaftseinschränkungen), löst die NPS-Erweiterung eine Anforderung der sekundären Authentifizierung mit Azure AD MFA aus.
1. Azure AD MFA kommuniziert mit Azure AD, ruft die Details des Benutzers ab und führt die sekundäre Authentifizierung mit unterstützten Methoden aus.
1. Bei erfolgreicher Authentifizierung durch MFA kommuniziert Azure AD MFA das Ergebnis an die NPS-Erweiterung.
1. Der NPS-Server, auf dem die Erweiterung installiert ist, sendet eine RADIUS-Zugriffsakzeptierungsnachricht für die RD-CAP-Richtlinie an den Remotedesktopgateway-Server.
1. Der Benutzer erhält über das RD-Gateway Zugriff auf die angeforderte Netzwerkressource.

## <a name="prerequisites"></a>Voraussetzungen

In diesem Abschnitt werden die erforderlichen Voraussetzungen zur Integration von Azure AD MFA mit dem Remotedesktopgateway erörtert. Bevor Sie beginnen, müssen folgende Voraussetzungen erfüllt sein:  

* Remotedesktopdienste-Infrastruktur (Remote Desktop Services, RDS)
* Azure AD MFA-Lizenz
* Windows Server-Software
* Netzwerkrichtlinien- und Zugriffsdienste-Rolle (Network Policy and Access Services, NPS)
* Azure Active Directory synchronisiert mit der lokalen Active Directory-Instanz
* GUID-ID von Azure Active Directory

### <a name="remote-desktop-services-rds-infrastructure"></a>Remotedesktopdienste-Infrastruktur (Remote Desktop Services, RDS)

Eine funktionsfähige Remotedesktopdienste-Infrastruktur (RDS) muss vorhanden sein. Wenn dies nicht der Fall ist, können Sie diese Infrastruktur mithilfe der folgenden Schnellstartvorlage schnell in Azure erstellen: [Erstellen der Bereitstellung einer Remotedesktopsitzungs-Sammlung](https://github.com/Azure/azure-quickstart-templates/tree/ad20c78b36d8e1246f96bb0e7a8741db481f957f/rds-deployment).

Wenn Sie eine lokale RDS-Infrastruktur schnell manuell zu Testzwecken erstellen möchten, führen Sie die Schritte zu deren Bereitstellung aus.
**Weitere Informationen:** [Nahtlose RDS-Bereitstellung mit ARM und Azure Marketplace](/windows-server/remote/remote-desktop-services/rds-in-azure) und [Bereitstellen Ihrer Remotedesktopumgebung](/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure).

### <a name="azure-ad-mfa-license"></a>Azure AD MFA-Lizenz

Es wird eine Lizenz für Azure AD MFA benötigt, die über Azure AD Premium oder andere Pakete verfügbar ist. Verbrauchsbasierte Lizenzen für Azure AD MFA, z. B. pro Benutzer oder pro Authentifizierungslizenz, sind mit der NPS-Erweiterung nicht kompatibel. Weitere Informationen finden Sie unter [Beziehen von Azure AD Multi-Factor Authentication](concept-mfa-licensing.md). Zu Testzwecken können Sie ein Testabonnement verwenden.

### <a name="windows-server-software"></a>Windows Server-Software

Die NPS-Erweiterung erfordert Windows Server 2008 R2 SP1 oder höher mit installiertem NPS-Rollendienst. Alle Schritte in diesem Abschnitt wurden mit Windows Server 2016 ausgeführt.

### <a name="network-policy-and-access-services-nps-role"></a>Netzwerkrichtlinien- und Zugriffsdienste-Rolle (Network Policy and Access Services, NPS)

Der NPS-Rollendienst bietet sowohl die RADIUS-Server- und -Clientfunktionalität als auch den Netzwerkzugriffsrichtlinien-Integritätsdienst. Diese Rolle muss auf mindestens zwei Computern in Ihrer Infrastruktur installiert werden: Das Remotedesktopgateway und ein weiterer Mitgliedsserver oder Domänencontroller. Standardmäßig ist die Rolle bereits auf dem Computer vorhanden, der als Remotedesktopgateway konfiguriert ist.  Sie müssen auch die NPS-Rolle auf mindestens einem anderen Computer installieren, z.B. einen Domänencontroller oder Mitgliedsserver.

Informationen zum Installieren des NPS-Rollendiensts für Windows Server 2012 oder älter finden Sie unter [Install a NAP Health Policy Server](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd296890(v=ws.10)) (Installieren eines NAP-Integritätsrichtlinienservers). Eine Beschreibung der bewährten Methoden für NPS einschließlich der Empfehlung zum Installieren von NPS auf einem Domänencontroller finden Sie unter [Best Practices for NPS](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc771746(v=ws.10)) (Bewährte Methoden für NPS).

### <a name="azure-active-directory-synched-with-on-premises-active-directory"></a>Azure Active Directory synchronisiert mit der lokalen Active Directory-Instanz

Um die NPS-Erweiterung zu verwenden, müssen lokale Benutzer mit Azure AD synchronisiert und für MFA aktiviert werden. Dieser Abschnitt setzt voraus, dass lokale Benutzer mithilfe von AD Connect mit Azure AD synchronisiert werden. Informationen zu Azure AD Connect finden Sie unter [Integrieren Ihrer lokalen Identitäten in Azure Active Directory](../hybrid/whatis-hybrid-identity.md).

### <a name="azure-active-directory-guid-id"></a>GUID-ID von Azure Active Directory

Um die NPS-Erweiterung zu installieren, müssen Sie die GUID von Azure AD kennen. Nachfolgend sind die Anweisungen für die Suche nach der GUID von Azure AD aufgeführt.

## <a name="configure-multi-factor-authentication"></a>Konfigurieren der Multi-Factor Authentication

Dieser Abschnitt enthält Anweisungen für die Integration von Azure AD MFA mit Remotedesktopgateway. Als Administrator müssen Sie den Azure AD MFA-Dienst konfigurieren, bevor Benutzer selbst ihre Geräte oder Anwendungen für die mehrstufige Authentifizierung registrieren können.

Führen Sie die Schritte in [Erste Schritte mit Azure AD Multi-Factor Authentication in der Cloud](howto-mfa-getstarted.md) aus, um MFA für Ihre Azure AD-Benutzer zu aktivieren.

### <a name="configure-accounts-for-two-step-verification"></a>Konfigurieren von Konten für die zweistufige Überprüfung

Nachdem ein Konto für MFA aktiviert wurde, können Sie sich erst dann bei Ressourcen anmelden, die der MFA-Richtlinie unterliegen, wenn Sie erfolgreich ein vertrauenswürdiges Gerät für den zweiten Authentifizierungsfaktor konfiguriert und die Authentifizierung mit der zweistufigen Überprüfung durchgeführt haben.

Führen Sie die Schritte in [Was ist Azure AD Multi-Factor Authentication?](../user-help/multi-factor-authentication-end-user-first-time.md) aus, um Ihre Geräte ordnungsgemäß mit Ihrem Benutzerkonto für MFA zu konfigurieren.

> [!IMPORTANT]
> Das Anmeldeverhalten für Remotedesktopgateway umfasst keine Option zur Eingabe eines Verifizierungscodes über Azure AD Multi-Factor Authentication. Benutzerkonten müssen für die Telefonüberprüfung oder die Microsoft Authenticator-App über Pushbenachrichtigungen konfiguriert sein.
>
> Wenn eine dieser Authentifizierungsmethoden für einen Benutzer nicht konfiguriert sind, kann Azure AD Multi-Factor Authentication nicht abgeschlossen werden, und der Benutzer kann sich nicht beim Remotedesktopgateway anmelden.

## <a name="install-and-configure-nps-extension"></a>Installieren und Konfigurieren der NPS-Erweiterung

Dieser Abschnitt enthält Anweisungen zum Konfigurieren der RDS-Infrastruktur zur Verwendung von Azure AD MFA für die Clientauthentifizierung mit dem Remotedesktopgateway.

### <a name="acquire-azure-active-directory-tenant-id"></a>Abrufen der Azure Active Directory-Mandanten-ID

Im Rahmen der Konfiguration der NPS-Erweiterung müssen Sie Administratoranmeldeinformationen und Azure AD-ID für Ihren Azure AD-Mandanten angeben. Führen Sie die folgenden Schritte aus, um die Mandanten-ID abzurufen:

1. Melden Sie sich im [Azure-Portal](https://portal.azure.com) als globaler Administrator des Azure-Mandanten an.
1. Wählen Sie im Menü des Azure-Portals die Option **Azure Active Directory** aus. Sie können auch auf einer beliebigen Seite nach **Azure Active Directory** suchen und diese Option auswählen.
1. Auf der Seite **Übersicht** werden die *Mandanteninformationen* angezeigt. Wählen Sie neben der *Mandanten-ID* das Symbol **Kopieren** aus, wie im folgenden Beispielscreenshot gezeigt:

   ![Abrufen der Mandanten-ID aus dem Azure-Portal](./media/howto-mfa-nps-extension-rdg/azure-active-directory-tenant-id-portal.png)

### <a name="install-the-nps-extension"></a>Installieren der NPS-Erweiterung

Installieren Sie die NPS-Erweiterung auf einem Server, auf dem die Netzwerkrichtlinien- und Zugriffsdienste-Rolle (NPS) installiert ist. Diese fungiert als RADIUS-Server für den Entwurf.

> [!IMPORTANT]
> Installieren Sie die NPS-Erweiterung nicht auf Ihrem Remotedesktop-Gatewayserver. Der Remotedesktop-Gatewayserver verwendet mit seinem Client das RADIUS-Protokoll nicht und kann daher die Erweiterung weder interpretieren noch die mehrstufige Authentifizierung (MFA) ausführen.
>
> Auch wenn der Remotedesktop-Gatewayserver und der NPS-Server mit NPS-Erweiterung unterschiedliche Server sind, verwendet der Remotedesktop-Gatewayserver doch intern die Netzwerkrichtlinien- und Zugriffsdienste-Rolle (NPS) für die Kommunikation mit anderen NPS-Servern und RADIUS als Protokoll für die ordnungsgemäße Kommunikation.

1. Laden Sie die [NPS-Erweiterung](https://aka.ms/npsmfa) herunter.
1. Kopieren Sie die ausführbare Setupdatei (NpsExtnForAzureMfaInstaller.exe) auf den NPS-Server.
1. Doppelklicken Sie auf dem NPS-Server auf **NpsExtnForAzureMfaInstaller.exe**. Klicken Sie nach Aufforderung auf **Ausführen**.
1. Überprüfen Sie im Dialogfeld der NPS-Erweiterung für das Azure AD MFA-Setup die Softwarelizenzbedingungen, aktivieren Sie **Ich stimme den Lizenzbedingungen zu**, und klicken Sie auf **Installieren**.
1. Klicken Sie im Dialogfeld der NPS-Erweiterung für das Azure AD MFA-Setup auf **Schließen**.

### <a name="configure-certificates-for-use-with-the-nps-extension-using-a-powershell-script"></a>Konfigurieren von Zertifikaten für die Verwendung mit der NPS-Erweiterung mithilfe eines PowerShell-Skripts

Als Nächstes müssen Sie Zertifikate für die Verwendung durch die NPS-Erweiterung konfigurieren, um die sichere Kommunikation zu gewährleisten. Die NPS-Komponenten umfassen ein Windows PowerShell-Skript, das ein selbstsigniertes Zertifikat zur Verwendung mit NPS konfiguriert.

Dieses Skript führt folgende Aktionen aus:

* Erstellen eines selbstsignierten Zertifikats
* Zuordnen des öffentlichen Schlüssels des Zertifikats zum Dienstprinzipal in Azure AD
* Speichern des Zertifikats im Speicher des lokalen Computers
* Gewähren des Zugriffs auf den privaten Zertifikatschlüssel für den Netzwerkbenutzer
* Neustart des Netzwerkrichtlinienserver-Diensts

Wenn Sie Ihre eigenen Zertifikate verwenden möchten, müssen Sie den öffentlichen Schlüssel Ihres Zertifikats dem Dienstprinzipal in Azure AD zuordnen usw.

Um das Skript zu verwenden, geben Sie die Erweiterung mit Ihren Azure AD-Administratoranmeldeinformationen und die Azure AD-Mandanten-ID ein, die Sie zuvor kopiert haben. Führen Sie das Skript auf jedem NPS-Server aus, auf dem Sie die NPS-Erweiterung installiert haben. Gehen Sie wie folgt vor:

1. Öffnen Sie eine administrative Windows PowerShell-Eingabeaufforderung.
1. Geben Sie an der PowerShell-Eingabeaufforderung `cd 'c:\Program Files\Microsoft\AzureMfa\Config'` ein, und drücken Sie die **EINGABETASTE**.
1. Geben Sie `.\AzureMfaNpsExtnConfigSetup.ps1` ein, und drücken Sie die **EINGABETASTE**. Das Skript überprüft, ob das Azure Active Directory PowerShell-Modul installiert ist. Wenn es nicht installiert ist, installiert das Skript das Modul für Sie.

   ![Ausführen von „AzureMfaNpsExtnConfigSetup.ps1“ in Azure AD PowerShell](./media/howto-mfa-nps-extension-rdg/image4.png)
  
1. Nachdem das Skript die Installation des PowerShell-Moduls überprüft hat, wird das Dialogfeld des Azure Active Directory PowerShell-Moduls angezeigt. Geben Sie im Dialogfeld Ihre Azure AD-Administratoranmeldeinformationen und das Kennwort ein, und klicken Sie auf **Anmelden**.

   ![Authentifizieren bei Azure AD in PowerShell](./media/howto-mfa-nps-extension-rdg/image5.png)

1. Wenn Sie aufgefordert werden, fügen Sie die *Mandanten-ID* ein, die Sie vorher in die Zwischenablage kopiert haben, und drücken Sie die **EINGABETASTE**.

   ![Eingeben der Mandanten-ID in PowerShell](./media/howto-mfa-nps-extension-rdg/image6.png)

1. Das Skript erstellt ein selbstsigniertes Zertifikat und führt andere Änderungen an der Konfiguration durch. Die Ausgabe sollte dem folgenden Bild ähneln.

   ![PowerShell-Ausgabe mit selbstsigniertem Zertifikat](./media/howto-mfa-nps-extension-rdg/image7.png)

## <a name="configure-nps-components-on-remote-desktop-gateway"></a>Konfigurieren von NPS-Komponenten auf dem Remotedesktopgateway

In diesem Abschnitt konfigurieren Sie die Verbindungsautorisierungsrichtlinien für das Remotedesktopgateway und andere RADIUS-Clienteinstellungen.

Der Authentifizierungsablauf erfordert, dass RADIUS-Nachrichten zwischen dem Remotedesktopgateway und dem NPS-Server ausgetauscht werden, auf dem die NPS-Erweiterung installiert ist. Dies bedeutet, dass Sie RADIUS-Clienteinstellungen sowohl auf dem Remotedesktopgateway als auch dem NPS-Server, auf dem die NPS-Erweiterung installiert ist, konfigurieren müssen.

### <a name="configure-remote-desktop-gateway-connection-authorization-policies-to-use-central-store"></a>Konfigurieren von Verbindungsautorisierungsrichtlinien für das Remotedesktopgateway zur Verwendung im zentralen Speicher

Verbindungsautorisierungsrichtlinien für Remotedesktop (RD-CAPs) geben die Anforderungen für das Herstellen einer Verbindung mit einem Remotedesktopgateway-Server an. RD-CAPs können lokal gespeichert werden (Standard) oder in einem zentralen RD-CAP-Speicher, wo NPS ausgeführt wird. Um die Integration von Azure AD MFA mit RDS zu konfigurieren, müssen Sie die Verwendung eines zentralen Speichers angeben.

1. Öffnen Sie auf dem RD-Gatewayserver den **Server-Manager**.
1. Klicken Sie auf der Menüleiste auf **Tools**, zeigen Sie auf **Remotedesktopdienste**, und klicken Sie dann auf **Remotedesktopgateway-Manager**.
1. Klicken Sie im RD-Gateway-Manager mit der rechten Maustaste auf **\[Servername\] (Lokal)** , und klicken Sie auf **Eigenschaften**.
1. Wählen Sie im Dialogfeld „Eigenschaften“ die Registerkarte **RD-CAP-Speicher** aus.
1. Wählen Sie auf der Registerkarte „RD-CAP-Speicher“ die Option **Zentraler NPS-Server**. 
1. Geben Sie in das Feld **Name oder IP-Adresse für den Server mit NPS eingeben:** die IP-Adresse bzw. den Servernamen des Servers ein, auf dem Sie die NPS-Erweiterung installiert haben.

   ![Den Namen oder die IP-Adresse Ihres NPS-Servers eingeben](./media/howto-mfa-nps-extension-rdg/image10.png)
  
1. Klicken Sie auf **Hinzufügen**.
1. Geben Sie im Dialogfeld **Gemeinsamer geheimer Schlüssel** einen gemeinsamen geheimen Schlüssel ein, und klicken Sie dann auf **OK**. Denken Sie daran, diesen gemeinsamen geheimen Schlüssel zu notieren und die Notiz an einem sicheren Ort aufzubewahren.

   >[!NOTE]
   >Mit dem gemeinsamen geheimen Schlüssel wird eine Vertrauensstellung zwischen den RADIUS-Servern und -Clients hergestellt. Erstellen Sie ein langes und komplexes Geheimnis.
   >

   ![Erstellen eines gemeinsamen geheimen Schlüssels zum Herstellen einer Vertrauensstellung](./media/howto-mfa-nps-extension-rdg/image11.png)

1. Klicken Sie auf **OK** , um das Dialogfeld zu schließen.

### <a name="configure-radius-timeout-value-on-remote-desktop-gateway-nps"></a>Konfigurieren des RADIUS-Timeoutwerts für Remotedesktopgateway-NPS

Um sicherzustellen, dass genügend Zeit zum Überprüfen der Anmeldeinformationen des Benutzers, Ausführen der zweistufigen Überprüfung, Empfangen von Antworten und Antworten auf RADIUS-Nachrichten zur Verfügung steht, müssen Sie den RADIUS-Timeoutwert anpassen.

1. Öffnen Sie auf dem RD-Gatewayserver den Server-Manager. Klicken Sie im Menü auf **Extras** und dann auf **Netzwerkrichtlinienserver**.
1. Erweitern Sie in der Konsole **NPS (Lokal)** **RADIUS-Clients und Server**, und wählen Sie **Remote-RADIUS-Server**.

   ![Fenster der Netzwerkrichtlinienserver-Verwaltungskonsole mit dem Bereich für RADIUS-Remoteservergruppen](./media/howto-mfa-nps-extension-rdg/image12.png)

1. Doppelklicken Sie im Detailbereich auf **TS-GATEWAYSERVERGRUPPE**.

   >[!NOTE]
   >Diese RADIUS-Server-Gruppe wurde erstellt, als Sie den zentralen Server für die NPS-Richtlinien konfigurierten. Das RD-Gateway leitet RADIUS-Nachrichten an diesen Server weiter, bzw. an eine Gruppe von Servern, wenn mehrere Server vorhanden sind.
   >

1. Wählen Sie im Dialogfeld **TS-GATEWAYSERVERGRUPPEN-Eigenschaften** die IP-Adresse oder den Namen des NPS-Servers, den Sie zum Speichern von RD-CAPs konfiguriert haben, und klicken Sie dann auf **Bearbeiten**.

   ![Auswählen der IP-Adresse oder des Namens des zuvor konfigurierten NPS-Servers](./media/howto-mfa-nps-extension-rdg/image13.png)

1. Wählen Sie im Dialogfeld **RADIUS-Server bearbeiten** die Registerkarte **Lastenausgleich**.
1. Ändern Sie in der Registerkarte **Lastenausgleich** im Feld **Sekunden ohne Antwort, bevor eine Anforderung als verworfen gilt** den Standardwert von 3 in einen Wert zwischen 30 und 60 Sekunden.
1. Ändern Sie im Feld **Sekunden zwischen Anforderungen, nach denen der Server als nicht verfügbar identifiziert wird** den Standardwert von 30 Sekunden in einen Wert, der mindestens so groß ist wie der Wert, den Sie im vorherigen Schritt angegeben haben.

   ![Bearbeiten der Timeouteinstellungen im Dialogfeld „RADIUS-Server bearbeiten“ auf der Registerkarte „Lastenausgleich“](./media/howto-mfa-nps-extension-rdg/image14.png)

1. Klicken Sie zweimal auf **OK**, um die Dialogfelder zu schließen.

### <a name="verify-connection-request-policies"></a>Überprüfen der Verbindungsanforderungsrichtlinien

Wenn Sie das RD-Gateway zur Verwendung eines zentralen Richtlinienspeichers für Verbindungsautorisierungsrichtlinien konfigurieren, wird standardmäßig das RD-Gateway zum Weiterleiten von CAP-Anforderungen an den NPS-Server konfiguriert. Der NPS-Server, auf dem die Azure AD MFA-Erweiterung installiert ist, verarbeitet die RADIUS-Zugriffsanforderung. In den folgenden Schritten lernen Sie das Überprüfen der Standard-Verbindungsanforderungsrichtlinie.  

1. Erweitern Sie auf dem RD-Gateway in der Konsole „NPS (Lokal)“ **Richtlinien**, und wählen Sie **Verbindungsanforderungsrichtlinien**.
1. Doppelklicken Sie auf **TS-GATEWAYAUTORISIERUNGSRICHTLINIE**.
1. Klicken Sie im Dialogfeld **TS-GATEWAYAUTORISIERUNGSRICHTLINIE-Eigenschaften** auf die Registerkarte **Einstellungen**.
1. Klicken Sie auf der Registerkarte **Einstellungen** unter „Weiterleitungsverbindungsanforderung“ auf **Authentifizierung**. Der RADIUS-Client ist zum Weiterleiten von Anforderungen für die Authentifizierung konfiguriert.

   ![Konfigurieren der Authentifizierungseinstellungen zum Angeben der Servergruppe](./media/howto-mfa-nps-extension-rdg/image15.png)

1. Klicken Sie auf **Abbrechen**.

>[!NOTE]
> Weitere Informationen zum Erstellen einer Verbindungsanforderungsrichtlinie finden im Artikel „Konfigurieren von Verbindungsanforderungsrichtlinien“ unter [Hinzufügen einer Verbindungsanforderungsrichtlinie](/windows-server/networking/technologies/nps/nps-crp-configure#add-a-connection-request-policy). 

## <a name="configure-nps-on-the-server-where-the-nps-extension-is-installed"></a>Konfigurieren von NPS auf dem Server, auf dem die NPS-Erweiterung installiert ist

Der NPS-Server, auf dem die NPS-Erweiterung installiert ist, muss die RADIUS-Nachrichten mit dem NPS-Server auf dem Remotedesktopgateway austauschen können. Um diesen Nachrichtenaustausch zu aktivieren, müssen Sie die NPS-Komponenten auf dem Server konfigurieren, auf dem der NPS-Erweiterungsdienst installiert ist.

### <a name="register-server-in-active-directory"></a>Registrieren des Servers in Active Directory

Die ordnungsgemäße Funktionsweise des NPS-Servers in diesem Szenario setzt seine Registrierung in Active Directory voraus.

1. Öffnen Sie auf dem NPS-Server den **Server-Manager**.
1. Klicken Sie im Server-Manager auf **Tools** und dann auf **Netzwerkrichtlinienserver**.
1. Klicken Sie in der Netzwerkrichtlinienserver-Konsole mit der rechten Maustaste auf **NPS (Lokal)** , und klicken Sie dann auf **Server in Active Directory registrieren**.
1. Klicken Sie zweimal auf **OK**.

   ![Registrieren des NPS-Servers in Active Directory](./media/howto-mfa-nps-extension-rdg/image16.png)

1. Lassen Sie die Konsole für den nächsten Vorgang geöffnet.

### <a name="create-and-configure-radius-client"></a>Erstellen und Konfigurieren des RADIUS-Clients

Das Remotedesktopgateway muss als RADIUS-Client des NPS-Servers konfiguriert werden.

1. Klicken Sie auf dem NPS-Server, wo die NPS-Erweiterung installiert ist, in der **NPS (Lokal)** -Konsole mit der rechten Maustaste auf **RADIUS-Clients**, und klicken Sie auf **Neu**.

   ![Erstellen eines neuen RADIUS-Clients in der NPS-Konsole](./media/howto-mfa-nps-extension-rdg/image17.png)

1. Geben Sie im Dialogfeld **Neuer RADIUS-Client** einen Anzeigenamen wie z.B. _Gateway_ und die IP-Adresse oder den DNS-Namen des Remotedesktopgateway-Servers an.
1. Geben Sie in den Feldern **Gemeinsamer geheimer Schlüssel** und **Gemeinsamen geheimen Schlüssel bestätigen:** den gleichen geheimen Schlüssel ein, den Sie zuvor verwendet haben.

   ![Eingeben eines Anzeigenamens und der IP- oder DNS-Adresse](./media/howto-mfa-nps-extension-rdg/image18.png)

1. Klicken Sie auf **OK**, um das Dialogfeld „Neuer RADIUS-Client“ zu schließen.

### <a name="configure-network-policy"></a>Konfigurieren der Netzwerkrichtlinie

Denken Sie daran, dass der NPS-Server mit der Azure AD MFA-Erweiterung der angegebene zentrale Richtlinienspeicher für die Verbindungsautorisierungsrichtlinie (CAP) ist. Aus diesem Grund müssen Sie auf dem NPS-Server eine CAP zum Autorisieren gültiger Verbindungsanforderungen implementieren.  

1. Öffnen Sie auf dem NPS-Server die Konsole „NPS (Lokal)“, erweitern Sie **Richtlinien**, und klicken Sie auf **Netzwerkrichtlinien**.
1. Klicken Sie mit der rechten Maustaste auf **Verbindungen mit anderen Zugriffsservern**, und klicken Sie auf **Duplizieren der Richtlinie**.

   ![Richtlinie „Verbindungen mit anderen Zugriffsservern“ duplizieren](./media/howto-mfa-nps-extension-rdg/image19.png)

1. Klicken Sie mit der rechten Maustaste auf **Kopie von Verbindungen mit anderen Zugriffsservern**, und klicken Sie auf **Eigenschaften**.
1. Geben Sie im Dialogfeld **Kopie der Verbindungen mit anderen Zugriffsservern** unter **Richtlinienname** einen geeigneten Namen ein, z.B. _RDG_CAP_. Aktivieren Sie **Richtlinie aktiviert**, und wählen Sie **Zugriff gewähren** aus. Wählen Sie optional unter **Typ des Netzwerkzugriffs** die Option  **Remotedesktopgateway** aus, oder behalten Sie **Nicht angegeben** bei.

   ![Richtliniennamen eingeben, „Richtlinie aktiviert“ aktivieren und „Zugriff gewähren“ auswählen](./media/howto-mfa-nps-extension-rdg/image21.png)

1. Klicken Sie auf die Registerkarte **Einschränkungen**, und aktivieren Sie **Clientverbindungen ohne Aushandlung einer Authentifizierungsmethode zulassen**.

   ![Ändern der Authentifizierungsmethoden zum Zulassen von Clientverbindungen](./media/howto-mfa-nps-extension-rdg/image22.png)

1. Klicken Sie optional auf die Registerkarte **Bedingungen**, und fügen Sie Bedingungen hinzu, die für die Autorisierung der Verbindung erfüllt werden müssen, z.B. die Mitgliedschaft in einer bestimmten Windows-Gruppe.

   ![Optional Verbindungsbedingungen angeben](./media/howto-mfa-nps-extension-rdg/image23.png)

1. Klicken Sie auf **OK**. Wenn Sie gefragt werden, ob Sie das entsprechende Hilfethema anzeigen möchten, klicken Sie auf **Nein**.
1. Stellen Sie sicher, dass Ihre neue Richtlinie am oberen Rand der Liste angezeigt wird, dass die Richtlinie aktiviert ist und Zugriff gewährt.

   ![Richtlinie an den Anfang der Liste verschieben](./media/howto-mfa-nps-extension-rdg/image24.png)

## <a name="verify-configuration"></a>Überprüfen der Konfiguration

Um die Konfiguration zu überprüfen, müssen Sie sich mit einem geeigneten RDP-Client beim Remotedesktopgateway anmelden. Achten Sie darauf, ein Konto zu verwenden, das von Ihren Verbindungsautorisierungsrichtlinien zugelassen und für Azure AD MFA aktiviert ist.

Wie im folgenden Bild gezeigt, können Sie die Seite **Web Access für Remotedesktop** verwenden.

![Überprüfung in Web Access für Remotedesktop](./media/howto-mfa-nps-extension-rdg/image25.png)

Nach erfolgreicher Eingabe Ihrer Anmeldeinformationen für die primäre Authentifizierung zeigt das Dialogfeld „Remotedesktopverbindung“ wie unten dargestellt den Status „Remoteverbindung wird initiiert“ an. 

Nach Ihrer erfolgreichen Authentifizierung mit der sekundären Authentifizierungsmethode, die Sie zuvor in Azure AD MFA konfiguriert haben, werden Sie mit der Ressource verbunden. Wenn die sekundäre Authentifizierung jedoch nicht erfolgreich ist, wird Ihnen der Zugriff auf die Ressource verweigert. 

![Remotedesktopverbindung: Remoteverbindung wird initiiert](./media/howto-mfa-nps-extension-rdg/image26.png)

Im folgenden Beispiel wird die Authenticator-App auf einem Windows Phone verwendet, um die sekundäre Authentifizierung bereitzustellen.

![Beispiel: Option „Überprüfen“ in Windows Phone-Authenticator-App](./media/howto-mfa-nps-extension-rdg/image27.png)

Nachdem Sie erfolgreich mit der sekundären Authentifizierungsmethode authentifiziert wurden, werden Sie wie gewohnt beim Remotedesktopgateway angemeldet. Weil Sie eine sekundäre Authentifizierungsmethode mit einer mobilen App auf einem vertrauenswürdigen Gerät verwenden müssen, ist der Anmeldeprozess jedoch sicherer als sonst.

### <a name="view-event-viewer-logs-for-successful-logon-events"></a>Anzeigen der Protokolle der Ereignisanzeige für erfolgreiche Anmeldeereignisse

Um die erfolgreichen Anmeldeereignisse in den Protokollen der Windows-Ereignisanzeige anzuzeigen, können Sie den folgenden Windows PowerShell-Befehl zum Abfragen der Windows-Terminaldienste und Windows-Sicherheitsprotokolle ausgeben.

Verwenden Sie zur Abfrage erfolgreicher Anmeldungsereignisse in den Gatewaybetriebsprotokollen (_Ereignisanzeige\Anwendungs- und Dienstprotokolle\Microsoft\Windows\TerminalServices-Gateway\Operational_) die folgenden PowerShell-Befehle:

* `Get-WinEvent -Logname Microsoft-Windows-TerminalServices-Gateway/Operational | where {$_.ID -eq '300'} | FL`
* Dieser Befehl zeigt Windows-Ereignisse an, die belegen, dass der Benutzer die Anforderungen der Ressourcenautorisierungsrichtlinie (RD-RAP) erfüllt hat und ihm Zugriff erteilt wurde.

![Anzeigen von Ereignissen mithilfe von PowerShell](./media/howto-mfa-nps-extension-rdg/image28.png)

* `Get-WinEvent -Logname Microsoft-Windows-TerminalServices-Gateway/Operational | where {$_.ID -eq '200'} | FL`
* Dieser Befehl zeigt Ereignisse an, die belegen, dass der Benutzer die Anforderungen der Verbindungsautorisierungsrichtlinie erfüllt hat.

![Anzeigen der Verbindungsautorisierungsrichtlinie mithilfe von PowerShell](./media/howto-mfa-nps-extension-rdg/image29.png)

Sie können bei der Anzeige dieses Protokolls auch nach den Ereignis-IDs 300 und 200 filtern. Verwenden Sie zum Abfragen erfolgreicher Anmeldeereignisse in den Protokollen der Sicherheitsereignisanzeige den folgenden Befehl:

* `Get-WinEvent -Logname Security | where {$_.ID -eq '6272'} | FL`
* Dieser Befehl kann entweder auf dem zentralen NPS oder dem RD-Gatewayserver ausgeführt werden.

![Beispiel für erfolgreiche Anmeldeereignisse](./media/howto-mfa-nps-extension-rdg/image30.png)

Sie können auch das Sicherheitsprotokoll oder die benutzerdefinierte Ansicht der Netzwerkrichtlinien- und Zugriffsdienste anzeigen, wie unten dargestellt:

![Ereignisanzeige: Netzwerkrichtlinien- und Zugriffsdienste](./media/howto-mfa-nps-extension-rdg/image31.png)

Auf dem Server, auf dem Sie die NPS-Erweiterung für Azure AD MFA installiert haben, finden Sie spezifische Ereignisanzeige-Anwendungsprotokolle für die Erweiterung unter _Anwendungs- und Dienstprotokolle\Microsoft\AzureMfa_.

![Ereignisanzeige: AuthZ-Anwendungsprotokolle](./media/howto-mfa-nps-extension-rdg/image32.png)

## <a name="troubleshoot-guide"></a>Leitfaden zur Problembehandlung

Wenn die Konfiguration nicht wie erwartet funktioniert, sollten Sie die Problembehandlung idealerweise mit der Überprüfung beginnen, ob der Benutzer für die Verwendung von Azure AD MFA konfiguriert ist. Lassen Sie den Benutzer eine Verbindung mit dem [Azure-Portal](https://portal.azure.com) herstellen. Wenn der Benutzer zur sekundären Überprüfung aufgefordert wird und sich erfolgreich authentifizieren kann, können Sie eine fehlerhafte Azure AD MFA-Konfiguration ausschließen.

Wenn Azure AD MFA für den Benutzer funktioniert, prüfen Sie die relevanten Ereignisprotokolle. Dazu gehören Sicherheitsereignis-, Gatewaybetriebs- und Azure AD MFA-Protokolle, die im vorherigen Abschnitt erläutert wurden.

Es folgt eine Beispielausgabe des Sicherheitsprotokolls mit einem fehlerhaften Anmeldeereignis (Ereignis-ID 6273):

![Beispiel für ein fehlerhaftes Anmeldeereignis](./media/howto-mfa-nps-extension-rdg/image33.png)

Es folgt ein verwandtes Ereignis aus den Azure MFA-Protokollen:

![Beispiel für ein Azure AD MFA-Protokoll in der Ereignisanzeige](./media/howto-mfa-nps-extension-rdg/image34.png)

Zum Ausführen erweiterter Problembehandlungsoptionen nutzen Sie die NPS-Datenbankformat-Protokolldateien dort, wo der NPS-Dienst installiert ist. Diese Protokolldateien werden im Ordner _%SystemRoot%\System32\Logs_ als durch Trennzeichen getrennte Textdateien erstellt.

Eine Beschreibung dieser Protokolldateien finden Sie unter [Interpret NPS Database Format Log Files](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc771748(v=ws.10)) (Interpretieren von NPS-Datenbankformat-Protokolldateien). Die Einträge in diesen Protokolldateien können schwierig zu interpretieren sein, ohne sie in eine Tabelle oder eine Datenbank zu importieren. Sie finden online mehrere IAS-Parser, die Ihnen die Interpretation der Protokolldateien erleichtern.

Das folgende Bild zeigt die Ausgabe einer solchen herunterladbaren [Sharewareanwendung](https://www.deepsoftware.com/iasviewer).

![Beispiel für eine IAS-Analyse einer Shareware-App](./media/howto-mfa-nps-extension-rdg/image35.png)

Schließlich können Sie für zusätzliche Problembehandlungsoptionen ein Protokollanalyseprogramm wie [Microsoft Message Analyzer](/message-analyzer/microsoft-message-analyzer-operating-guide) verwenden.

Die Abbildung unten aus der Microsoft-Nachrichtenanalyse (Microsoft Message Analyzer) zeigt den Netzwerkdatenverkehr, der mit dem RADIUS-Protokoll gefiltert ist, das den Benutzernamen **CONTOSO\AliceC** enthält.

![Gefilterter Datenverkehr in Microsoft Message Analyzer](./media/howto-mfa-nps-extension-rdg/image36.png)

## <a name="next-steps"></a>Nächste Schritte

[Beziehen von Azure AD Multi-Factor Authentication](concept-mfa-licensing.md)

[Remotedesktop-Gateway und Azure Multi-Factor Authentication-Server mithilfe von RADIUS](howto-mfaserver-nps-rdg.md)

[Integrieren Ihrer lokalen Verzeichnisse in Azure Active Directory](../hybrid/whatis-hybrid-identity.md)