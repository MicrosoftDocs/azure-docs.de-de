---
title: VPN mit Azure AD MFA unter Verwendung der NPS-Erweiterung – Azure Active Directory
description: Integrieren Sie Ihre VPN-Infrastruktur mithilfe der Netzwerkrichtlinienserver-Erweiterung für Microsoft Azure in Azure AD MFA.
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
ms.openlocfilehash: 1c4c5c4e632943ebbe68003f663aebbeaab9ebaf
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/06/2020
ms.locfileid: "96743444"
---
# <a name="integrate-your-vpn-infrastructure-with-azure-ad-mfa-by-using-the-network-policy-server-extension-for-azure"></a>Integrieren Ihrer VPN-Infrastruktur in Azure AD MFA mithilfe der Netzwerkrichtlinienserver-Erweiterung für Azure

Die Netzwerkrichtlinienserver-Erweiterung (Network Policy Server, NPS) für Azure ermöglicht Organisationen, die RADIUS-Clientauthentifizierung (Remote Authentication Dial-in User Service) mit cloudbasierter [Azure AD Multi-Factor Authentication (MFA)](howto-mfaserver-nps-rdg.md) zu schützen, die eine zweistufige Überprüfung bietet.

Dieser Artikel enthält Anweisungen zum Integrieren der NPS-Infrastruktur in MFA mithilfe der NPS-Erweiterung für Azure. Dieser Vorgang ermöglicht eine sichere zweistufige Überprüfung für Benutzer, die versuchen, über ein VPN eine Verbindung mit Ihrem Netzwerk herzustellen.

Die Netzwerkrichtlinien- und Zugriffsdienste bieten Organisationen folgende Möglichkeiten:

* Zuweisen eines zentralen Orts für die Verwaltung und Steuerung von Netzwerkanforderungen, um Folgendes anzugeben:

  * Wer kann eine Verbindung herstellen

  * Zu welchen Tageszeiten sind Verbindungen zulässig

  * Dauer der Verbindungen

  * Sicherheitsstufe, die Clients für die Verbindungsherstellung verwenden müssen

    Anstatt Richtlinien in jedem VPN oder auf jedem Remotedesktopgateway-Server anzugeben, kann dies erfolgen, nachdem diese einem zentralen Ort zugewiesen wurden. Das RADIUS-Protokoll wird verwendet, um die zentralisierte Authentifizierung, Autorisierung und Ressourcenerfassung (Authentication, Authorization, Accounting – AAA) bereitzustellen.

* Richten Sie Netzwerkzugriffsschutz-Clientintegritätsrichtlinien (Network Access Protection, NAP) ein, die bestimmen, ob Geräten uneingeschränkter oder eingeschränkter Zugriff auf Netzwerkressourcen gewährt wird, und erzwingen Sie deren Durchsetzung.

* Möglichkeit zum Erzwingen der Authentifizierung und Autorisierung für den Zugriff auf 802.1X-fähige Funkzugriffspunkte und Ethernet-Switches
  Weitere Informationen finden Sie unter [Netzwerkrichtlinienserver (NPS)](/windows-server/networking/technologies/nps/nps-top).

Um die Sicherheit zu erhöhen und ein hohes Maß an Kompatibilität zu bieten, können Organisationen NPS in Azure AD Multi-Factor Authentication integrieren, um sicherzustellen, dass Benutzer die zweistufige Überprüfung verwenden, um eine Verbindung mit dem virtuellen Port auf dem VPN-Server herzustellen. Damit Benutzern Zugriff gewährt wird, müssen sie die von ihnen festgelegte Kombination aus Benutzername und Kennwort und andere Informationen angeben. Diese Informationen müssen vertrauenswürdig und dürfen nicht problemlos duplizierbar sein. Dazu gehören z.B. eine Mobiltelefonnummer, eine Festnetznummer oder eine Anwendung auf einem mobilen Gerät.

Vor der Verfügbarkeit der NPS-Erweiterung für Azure mussten Kunden, die die zweistufige Überprüfung für integrierte NPS- und MFA-Umgebungen implementieren wollten, einen separaten MFA-Server in einer lokalen Umgebung konfigurieren und verwalten. Dieser Authentifizierungstyp wird vom Remotedesktopgateway- und Azure Multi-Factor Authentication-Server mithilfe von RADIUS zur Verfügung gestellt.

Mit der NPS-Erweiterung für Azure können Organisationen entweder eine lokal basierte oder eine cloudbasierte MFA-Lösung zum Schützen der RADIUS-Clientauthentifizierung bereitstellen.

## <a name="authentication-flow"></a>Authentifizierungsfluss

Wenn Benutzer eine Verbindung mit einem virtuellen Port auf einem VPN-Server herstellen, müssen sie sich zunächst mit verschiedensten Protokollen authentifizieren. Die Protokolle ermöglichen die Verwendung einer Kombination aus Benutzername und Kennwort sowie zertifikatbasierte Authentifizierungsmethoden.

Zusätzlich zur Authentifizierung und Identitätsüberprüfung müssen Benutzer über die entsprechenden Einwahlberechtigungen verfügen. In einfachen Implementierungen werden Einwahlberechtigungen, die Zugriff gewährleisten, direkt in den Active Directory-Benutzerobjekten festgelegt.

![Registerkarte „Einwählen“ in den Benutzereigenschaften von „Active Directory-Benutzer und-Computer“](./media/howto-mfa-nps-extension-vpn/image1.png)

In einfachen Implementierungen gewährt oder verweigert jeder VPN-Server den Zugriff basierend auf Richtlinien, die auf jedem lokalen VPN-Server definiert werden.

In größeren und besser skalierbaren Implementierungen befinden sich die Richtlinien, die VPN-Zugriff gewähren oder verweigern, zentral auf RADIUS-Servern. In diesen Fällen fungiert der VPN-Server als Zugriffsserver (RADIUS-Client), der Verbindungsanforderungen und Ressourcenerfassungsnachrichten an einen RADIUS-Server weiterleitet. Um eine Verbindung mit dem virtuellen Port auf dem VPN-Server herzustellen, müssen Benutzer authentifiziert werden und die zentral auf RADIUS-Servern definierten Bedingungen erfüllen.

Wenn die NPS-Erweiterung für Azure im NPS integriert ist, gestaltet sich ein erfolgreicher Authentifizierungsablauf wie folgt:

1. Der VPN-Server empfängt von einem VPN-Benutzer eine Authentifizierungsanforderung, die den Benutzernamen und das Kennwort zum Herstellen der Verbindung mit einer Ressource (z.B. einer Remotedesktopsitzung) enthält.
2. Als RADIUS-Client fungierend konvertiert der VPN-Server die Anforderung in eine RADIUS-*Zugriffsanforderungsnachricht* und sendet diese (mit einem verschlüsselten Kennwort) an den RADIUS-Server, auf dem die NPS-Erweiterung installiert ist.
3. Die Kombination aus Benutzername und Kennwort wird in Active Directory überprüft. Wenn der Benutzername oder das Kennwort falsch ist, sendet der RADIUS-Server eine *Zugriffsverweigerungsnachricht*.
4. Wenn alle Bedingungen entsprechend den Angaben in der NPS-Verbindungsanforderung und den Netzwerkrichtlinien erfüllt sind (z. B. Uhrzeit- oder Gruppenmitgliedschaftseinschränkungen), löst die NPS-Erweiterung eine Anforderung der sekundären Authentifizierung mit Azure AD Multi-Factor Authentication aus.
5. Azure AD Multi-Factor Authentication kommuniziert mit Azure Active Directory, ruft die Details des Benutzers ab und führt die sekundäre Authentifizierung mit der vom Benutzer konfigurierten Methode (Anruf auf dem Mobiltelefon, Textnachricht oder mobile App) aus.
6. Bei erfolgreicher MFA-Überprüfung übermittelt Azure AD Multi-Factor Authentication das Ergebnis an die NPS-Erweiterung.
7. Nach Authentifizierung und Autorisierung des Verbindungsversuchs sendet der Netzwerkrichtlinienserver, auf dem die Erweiterung installiert ist, eine RADIUS-*Zugriffsakzeptierungsnachricht* an den VPN-Server (RADIUS-Client).
8. Der Benutzer erhält Zugriff auf den virtuellen Port des VPN-Servers und richtet einen verschlüsselten VPN-Tunnel ein.

## <a name="prerequisites"></a>Voraussetzungen

In diesem Abschnitt werden die erforderlichen Voraussetzungen zur Integration von MFA in das VPN ausführlich beschrieben. Bevor Sie beginnen, müssen folgende Voraussetzungen erfüllt und eingerichtet sein:

* VPN-Infrastruktur
* Rolle „Netzwerkrichtlinien- und Zugriffsdienste“
* Azure AD Multi-Factor Authentication-Lizenz
* Windows Server-Software
* Bibliotheken
* Azure Active Directory (Azure AD) synchronisiert mit der lokalen Active Directory-Instanz
* GUID-ID von Azure Active Directory

### <a name="vpn-infrastructure"></a>VPN-Infrastruktur

In diesem Artikel wird davon ausgegangen, dass Sie über eine funktionierende VPN-Infrastruktur mit Microsoft Windows Server 2016 verfügen und dass der VPN-Server aktuell nicht zum Weiterleiten von Verbindungsanforderungen an einen RADIUS-Server konfiguriert ist. In diesem Artikel konfigurieren Sie die VPN-Infrastruktur zur Verwendung eines zentralen RADIUS-Servers.

Wenn Sie nicht über eine funktionierende VPN-Infrastruktur verfügen, können Sie sie schnell anhand der Anleitungen in zahlreichen Tutorials zum VPN-Setup erstellen, die Sie auf den Websites von Microsoft und Drittanbietern finden.

### <a name="the-network-policy-and-access-services-role"></a>Rolle „Netzwerkrichtlinien- und Zugriffsdienste“

Die Rolle „Netzwerkrichtlinien- und Zugriffsdienste“ bietet die RADIUS-Funktionen für Server und Client. In diesem Artikel wird davon ausgegangen, dass Sie die Rolle „Netzwerkrichtlinien- und Zugriffsdienste“ auf einem Mitgliedsserver oder Domänencontroller in Ihrer Umgebung installiert haben. In dieser Anleitung konfigurieren Sie RADIUS für eine VPN-Konfiguration. Installieren Sie die Rolle „Netzwerkrichtlinien- und Zugriffsdienste“ auf einem *anderen* Server als Ihrem VPN-Server.

Informationen zum Installieren des Rollendiensts „Netzwerkrichtlinien- und Zugriffsdienste“ für Windows Server 2012 oder älter finden Sie unter [Install a NAP Health Policy Server](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd296890(v=ws.10)) (Installieren eines NAP-Integritätsrichtlinienservers). Der Netzwerkzugriffsschutz (NAP) ist in Windows Server 2016 veraltet. Eine Beschreibung der bewährten Methoden für NPS einschließlich der Empfehlung zum Installieren von NPS auf einem Domänencontroller finden Sie unter [Best Practices for NPS](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc771746(v=ws.10)) (Bewährte Methoden für NPS).

### <a name="azure-ad-mfa-license"></a>Azure AD MFA-Lizenz

Für Azure AD Multi-Factor Authentication ist eine Lizenz erforderlich. Diese ist über eine Azure AD Premium-, eine Enterprise Mobility + Security- oder eine eigenständige Multi-Factor Authentication-Lizenz verfügbar. Verbrauchsbasierte Lizenzen für Azure AD MFA, z. B. pro Benutzer oder pro Authentifizierungslizenz, sind mit der NPS-Erweiterung nicht kompatibel. Weitere Informationen finden Sie unter [Erwerben von Azure AD Multi-Factor Authentication](concept-mfa-licensing.md). Zu Testzwecken können Sie ein Testabonnement verwenden.

### <a name="windows-server-software"></a>Windows Server-Software

Die NPS-Erweiterung erfordert Windows Server 2008 R2 SP1 oder höher mit installierter Rolle „Netzwerkrichtlinien- und Zugriffsdienste“. Alle Schritte in dieser Anleitung wurden unter Windows Server 2016 ausgeführt.

### <a name="libraries"></a>Bibliotheken

Die folgenden Bibliotheken werden automatisch mit der NPS-Erweiterung installiert:

-    [Visual C++ Redistributable Packages für Visual Studio 2013 (x64)](https://www.microsoft.com/download/details.aspx?id=40784)
-    [Microsoft Azure Active Directory-Modul für Windows PowerShell, Version 1.1.166.0](https://connect.microsoft.com/site1164/Downloads/DownloadDetails.aspx?DownloadID=59185)

Sofern das Microsoft Azure Active Directory PowerShell-Modul nicht bereits vorhanden ist, wird es mit einem Konfigurationsskript installiert, das Sie als Teil des Installationsvorgangs ausführen. Es ist nicht erforderlich, das Modul vorab zu installieren, wenn es nicht bereits installiert ist.

### <a name="azure-active-directory-synced-with-on-premises-active-directory"></a>Azure Active Directory synchronisiert mit der lokalen Active Directory-Instanz

Um die NPS-Erweiterung zu verwenden, müssen lokale Benutzer mit Azure Active Directory synchronisiert und für MFA aktiviert werden. In dieser Anleitung wird vorausgesetzt, dass lokale Benutzer über AD Connect mit Azure Active Directory synchronisiert werden. Anweisungen zum Aktivieren von Benutzern für MFA siehe unten.

Informationen zu Azure AD Connect finden Sie unter [Integrieren Ihrer lokalen Verzeichnisse in Azure Active Directory](../hybrid/whatis-hybrid-identity.md).

### <a name="azure-active-directory-guid-id"></a>GUID-ID von Azure Active Directory

Um die NPS-Erweiterung zu installieren, müssen Sie die GUID von Azure Active Directory kennen. Anweisungen zum Ermitteln der GUID von Azure Active Directory finden Sie im nächsten Abschnitt.

## <a name="configure-radius-for-vpn-connections"></a>Konfigurieren von RADIUS für VPN-Verbindungen

Wenn Sie die NPS-Rolle auf einem Mitgliedsserver installiert haben, müssen Sie sie zum Authentifizieren und Autorisieren des VPN-Clients, der VPN-Verbindungen anfordert, konfigurieren. 

In diesem Abschnitt wird davon ausgegangen, dass Sie die Rolle „Netzwerkrichtlinien- und Zugriffsdienste“ installiert, aber noch nicht für die Verwendung in Ihrer Infrastruktur konfiguriert haben.

> [!NOTE]
> Wenn Sie bereits über einen funktionierenden VPN-Server verfügen, der einen zentralisierten RADIUS-Server für die Authentifizierung verwendet, können Sie diesen Abschnitt überspringen.
>

### <a name="register-server-in-active-directory"></a>Registrieren des Servers in Active Directory

Die ordnungsgemäße Funktionsweise des NPS-Servers in diesem Szenario setzt seine Registrierung in Active Directory voraus.

1. Öffnen Sie den Server-Manager.

2. Wählen Sie im Server-Manager **Extras** und dann **Netzwerkrichtlinienserver** aus.

3. Klicken Sie in der Netzwerkrichtlinienserver-Konsole mit der rechten Maustaste auf **NPS (Lokal)** , und wählen Sie dann **Server in Active Directory registrieren** aus. Wählen Sie zweimal **OK** aus.

    ![Menüoption „Server in Active Directory registrieren“](./media/howto-mfa-nps-extension-vpn/image2.png)

4. Lassen Sie die Konsole für den nächsten Vorgang geöffnet.

### <a name="use-wizard-to-configure-the-radius-server"></a>Verwenden des Assistenten zum Konfigurieren des RADIUS-Servers

Sie können eine standardmäßige (assistentenbasierte) oder die erweiterte Konfigurationsoption zum Konfigurieren des RADIUS-Servers verwenden. In diesem Abschnitt wird davon ausgegangen, dass Sie die Option zur assistentenbasierten Standardkonfiguration verwenden.

1. Wählen Sie in der Netzwerkrichtlinienserver-Konsole die Option **NPS (Lokal)** aus.

2. Wählen Sie unter **Standardkonfiguration** die Option **RADIUS-Server für Einwähl- oder VPN-Verbindungen** und dann **VPN oder DFÜ konfigurieren** aus.

    ![RADIUS-Server für Einwähl- oder VPN-Verbindungen konfigurieren](./media/howto-mfa-nps-extension-vpn/image3.png)

3. Wählen Sie im Fenster **Auswählen des Einwähl- oder VPN-Verbindungstyps** die Option **Verbindungen für virtuelles privates Netzwerk (VPN)** und dann **Weiter** aus.

    ![Verbindungen für virtuelles privates Netzwerk konfigurieren](./media/howto-mfa-nps-extension-vpn/image4.png)

4. Wählen Sie im Fenster **Angeben des Einwähl- oder VPN-Servers** die Option **Hinzufügen** aus.

5. Geben Sie im Fenster **Neuer RADIUS-Client** einen Anzeigenamen an, und geben Sie den auflösbaren Namen oder die IP-Adresse des VPN-Servers und dann ein gemeinsames geheimes Kennwort ein. Das gemeinsame geheime Kennwort sollte lang und komplex sein. Notieren Sie es, da Sie es im nächsten Abschnitt benötigen.

    ![Fenster „Neuer RADIUS-Client“](./media/howto-mfa-nps-extension-vpn/image5.png)

6. Wählen Sie **OK** und anschließend **Weiter** aus.

7. Akzeptieren Sie im Fenster **Authentifizierungsmethoden konfigurieren** die Standardauswahl **Microsoft-verschlüsselte Authentifizierung, Version 2 (MS-CHAPv2)** , oder wählen Sie eine andere Option aus. Wählen Sie dann **Weiter** aus.

    > [!NOTE]
    > Wenn Sie das Extensible Authentication-Protokoll (EAP) konfigurieren, müssen Sie entweder das Microsoft Challenge Handshake Authentication-Protokoll (CHAPv2) oder das Protected Extensible Authentication-Protokoll (PEAP) verwenden. Kein anderes EAP wird unterstützt.

8. Wählen Sie im Fenster **Benutzergruppen angeben** die Option **Hinzufügen** und anschließend eine entsprechende Gruppe aus. Wenn keine Gruppe vorhanden ist, lassen Sie die Auswahl leer, um allen Benutzern Zugriff zu gewähren.

    ![Fenster „Benutzergruppen angeben“ zum Zulassen oder Verweigern des Zugriffs](./media/howto-mfa-nps-extension-vpn/image7.png)

9. Wählen Sie **Weiter** aus.

10. Wählen Sie im Fenster **Angeben von IP-Filtern** die Option **Weiter** aus.

11. Akzeptieren Sie im Fenster **Angeben von Verschlüsselungseinstellungen** die Standardeinstellungen, und wählen Sie dann **Weiter** aus.

    ![Fenster „Angeben von Verschlüsselungseinstellungen“](./media/howto-mfa-nps-extension-vpn/image8.png)

12. Lassen Sie im Fenster **Bereichsname angeben** den Bereichsnamen leer, akzeptieren Sie die Standardeinstellung, und wählen Sie **Weiter** aus.

    ![Fenster „Bereichsname angeben“](./media/howto-mfa-nps-extension-vpn/image9.png)

13. Wählen Sie im Fenster **Abschließen der neuen Einwähl- oder VPN-Verbindungen und RADIUS-Clients** die Option **Fertig stellen** aus.

    ![Fenster der abgeschlossenen Konfiguration](./media/howto-mfa-nps-extension-vpn/image10.png)

### <a name="verify-the-radius-configuration"></a>Überprüfen der RADIUS-Konfiguration

In diesem Abschnitt wird die Konfiguration erörtert, die Sie mithilfe des Assistenten erstellt haben.

1. Erweitern Sie auf dem Netzwerkrichtlinienserver in der Konsole „NPS (Lokal)“ den Eintrag **RADIUS-Clients**, und wählen Sie **RADIUS-Clients** aus.

2. Klicken Sie im Detailbereich mit der rechten Maustaste auf den RADIUS-Client, den Sie erstellt haben, und wählen Sie dann **Eigenschaften** aus. Die Eigenschaften des RADIUS-Clients (der VPN-Server) sollten denen hier dargestellten entsprechen:

    ![VPN-Eigenschaften und -Konfiguration überprüfen](./media/howto-mfa-nps-extension-vpn/image11.png)

3. Wählen Sie **Abbrechen** aus.

4. Erweitern Sie auf dem Netzwerkrichtlinienserver in der Konsole „NPS (Lokal)“ den Eintrag **Richtlinien**, und wählen Sie **Verbindungsanforderungsrichtlinien** aus. Die Richtlinie für VPN-Verbindungen wird angezeigt, wie in der folgenden Abbildung dargestellt:

    ![Fenster „Verbindungsanforderungsrichtlinien“ mit der Richtlinie für VPN-Verbindungen](./media/howto-mfa-nps-extension-vpn/image12.png)

5. Wählen Sie unter **Richtlinien** die Option **Netzwerkrichtlinien** aus. Eine Richtlinie für VPN-Verbindungen sollte angezeigt werden und der Richtlinie in der folgenden Abbildung ähneln:

    ![Fenster „Netzwerkrichtlinien“ mit der Richtlinie für VPN-Verbindungen](./media/howto-mfa-nps-extension-vpn/image13.png)

## <a name="configure-your-vpn-server-to-use-radius-authentication"></a>Konfigurieren des VPN-Servers zur Verwendung der RADIUS-Authentifizierung

In diesem Abschnitt konfigurieren Sie Ihren VPN-Server zur Verwendung der RADIUS-Authentifizierung. Bei den Anweisungen wird vorausgesetzt, dass Sie über eine funktionsfähige Konfiguration eines VPN-Servers verfügen, ihn jedoch nicht zur Verwendung der RADIUS-Authentifizierung konfiguriert haben. Prüfen Sie nach dem Konfigurieren des VPN-Servers, ob die Konfiguration wie erwartet funktioniert.

> [!NOTE]
> Wenn Sie bereits über eine funktionierende VPN-Serverkonfiguration verfügen, die die RADIUS-Authentifizierung verwendet, können Sie diesen Abschnitt überspringen.
>

### <a name="configure-authentication-provider"></a>Konfigurieren des Authentifizierungsanbieters

1. Öffnen Sie auf dem VPN-Server den Server-Manager.

2. Wählen Sie im Server-Manager die Option **Tools** und dann **Routing und RAS** aus.

3. Klicken Sie im Fenster **Routing und Remotezugriff** mit der rechten Maustaste auf **\<server name> (lokal)** , und wählen Sie dann **Eigenschaften** aus.

4. Wählen Sie im Fenster **\<server name> (lokal) – Eigenschaften** die Registerkarte **Sicherheit** aus.

5. Wählen Sie auf der Registerkarte **Sicherheit** unter **Authentifizierungsanbieter** die Option **RADIUS-Authentifizierung** und dann **Konfigurieren** aus.

    ![RADIUS-Authentifizierungsanbieter konfigurieren](./media/howto-mfa-nps-extension-vpn/image15.png)

6. Wählen Sie im Fenster **RADIUS-Authentifizierung** die Option **Hinzufügen** aus.

7. Gehen Sie im Fenster **RADIUS-Server hinzufügen** folgendermaßen vor:

    a. Geben Sie im Feld **Servername** den Namen oder die IP-Adresse des RADIUS-Servers ein, den Sie im vorherigen Abschnitt konfiguriert haben.

    b. Wählen Sie für **Gemeinsamer geheimer Schlüssel** die Schaltfläche **Ändern** aus, und geben Sie das gemeinsame geheime Kennwort ein, das Sie zuvor erstellt und notiert haben.

    c. Geben Sie im Feld **Timeout (Sekunden)** den Wert **30** ein.  
    Der Timeoutwert ist erforderlich, damit genügend Zeit für den Abschluss des zweiten Authentifizierungsfaktors bleibt. Bei einigen VPNs oder Regionen sind Timeouteinstellungen von mehr als 30 Sekunden erforderlich, um zu verhindern, dass Benutzer mehrere Anrufe empfangen. Wenn dieses Problem entsteht, erhöhen Sie den Wert für **Timeout (Sekunden)** in Inkrementen von 30 Sekunden, bis das Problem nicht wieder auftritt.

    ![Fenster „RADIUS-Server hinzufügen“, in dem der Timeoutwert ausgewählt wird](./media/howto-mfa-nps-extension-vpn/image16.png) 

8. Klicken Sie auf **OK**.

### <a name="test-vpn-connectivity"></a>Testen der VPN-Konnektivität

In diesem Abschnitt prüfen Sie, ob der VPN-Client vom RADIUS-Server authentifiziert und autorisiert wird, wenn Sie versuchen, eine Verbindung mit dem virtuellen VPN-Port herzustellen. Bei den Anweisungen wird davon ausgegangen, dass Sie Windows 10 als VPN-Client verwenden.

> [!NOTE]
> Wenn Sie bereits einen VPN-Client zum Herstellen einer Verbindung mit dem VPN-Server konfiguriert und die Einstellungen gespeichert haben, können Sie die Schritte zum Konfigurieren und Speichern eines VPN-Verbindungsobjekts überspringen.
>

1. Wählen Sie auf Ihrem VPN-Clientcomputer die Schaltfläche **Start** und dann die Schaltfläche **Einstellungen** aus.

2. Wählen Sie im Fenster **Windows-Einstellungen** die Option **Netzwerk und Internet** aus.

3. Wählen Sie **VPN** aus.

4. Wählen Sie **VPN-Verbindung hinzufügen** aus.

5. Wählen Sie im Fenster **VPN-Verbindung hinzufügen** im Feld **VPN-Anbieter** die Option **Windows (integriert)** aus, füllen Sie die verbleibenden Felder nach Bedarf aus, und wählen Sie dann **Speichern** aus.

    ![Fenster „VPN-Verbindung hinzufügen“](./media/howto-mfa-nps-extension-vpn/image17.png)

6. Wechseln Sie zur **Systemsteuerung**, und wählen Sie **Netzwerk- und Freigabecenter** aus.

7. Wählen Sie **Adaptereinstellungen ändern** aus.

    ![Netzwerk- und Freigabecenter: Adaptereinstellungen ändern](./media/howto-mfa-nps-extension-vpn/image18.png)

8. Klicken Sie mit der rechten Maustaste auf die VPN-Netzwerkverbindung, und wählen Sie **Eigenschaften** aus.

9. Wählen Sie im Fenster der VPN-Eigenschaften die Registerkarte **Sicherheit** aus.

10. Stellen Sie auf der Registerkarte **Sicherheit** sicher, dass nur **Microsoft CHAP, Version 2 (MS-CHAP v2)** ausgewählt ist, und wählen Sie dann **OK** aus.

    ![Option „Folgende Protokolle zulassen“](./media/howto-mfa-nps-extension-vpn/image20.png)

11. Klicken Sie mit der rechten Maustaste auf die VPN-Verbindung, und wählen Sie **Verbinden** aus.

12. Wählen Sie im Fenster **Einstellungen** die Option **Verbinden** aus.  
    Eine erfolgreiche Verbindung wird im Sicherheitsprotokoll auf dem RADIUS-Server als Ereignis-ID 6272 angezeigt, wie hier dargestellt:

    ![Fenster „Ereigniseigenschaften“, in dem eine erfolgreiche Verbindung angezeigt wird](./media/howto-mfa-nps-extension-vpn/image21.png)

## <a name="troubleshooting-radius"></a>Problembehandlung für RADIUS

Angenommen, Ihre VPN-Konfiguration funktionierte vor der Konfiguration des VPN-Servers zur Verwendung eines zentralisierten RADIUS-Servers für die Authentifizierung und Autorisierung ordnungsgemäß. Wenn die Konfiguration ordnungsgemäß funktioniert, wird das Problem wahrscheinlich durch eine fehlerhafte Konfiguration des RADIUS-Servers oder die Verwendung eines ungültigen Benutzernamens oder Kennworts verursacht. Wenn Sie beispielsweise das alternative UPN-Suffix im Benutzernamen verwenden, kann beim Anmeldeversuch ein Fehler auftreten. Verwenden Sie den gleichen Kontonamen, um beste Ergebnisse zu erzielen.

Um diese Probleme zu beheben, ist die Untersuchung der Sicherheitsereignisprotokolle auf dem RADIUS-Server ein idealer Ausgangspunkt. Um bei der Suche nach Ereignissen Zeit zu sparen, können Sie, wie hier gezeigt, die rollenbasierte benutzerdefinierte Netzwerkrichtlinien- und Zugriffsserveransicht in der Ereignisanzeige verwenden. Ereignis-ID 6273 zeigt Ereignisse an, bei denen der Netzwerkrichtlinienserver einem Benutzer den Zugriff verweigert hat.

![Ereignisanzeige mit NPAS-Ereignissen](./media/howto-mfa-nps-extension-vpn/image22.png)

## <a name="configure-multi-factor-authentication"></a>Konfigurieren der Multi-Factor Authentication

Unterstützung beim Konfigurieren von Benutzern für Multi-Factor Authentication finden Sie in den Artikeln [Planen einer cloudbasierten Bereitstellung von Azure AD Multi-Factor Authentication](howto-mfa-getstarted.md#create-conditional-access-policy) und [Einrichten meines Kontos für die zweistufige Überprüfung](../user-help/multi-factor-authentication-end-user-first-time.md).

## <a name="install-and-configure-the-nps-extension"></a>Installieren und Konfigurieren der NPS-Erweiterung

Dieser Abschnitt enthält Anweisungen zum Konfigurieren von VPN zur Verwendung der MFA für die Clientauthentifizierung mit dem VPN-Server.

> [!NOTE]
> Beim Registrierungsschlüssel REQUIRE_USER_MATCH wird die Groß-/Kleinschreibung beachtet. Alle Werte müssen in GROSSBUCHSTABEN angegeben werden.
>

Nachdem Sie die NPS-Erweiterung installiert und konfiguriert haben, muss bei jeder von diesem Server verarbeiteten RADIUS-basierten Clientauthentifizierung MFA verwendet werden. Wenn nicht alle VPN-Benutzer bei Azure AD Multi-Factor Authentication registriert sind, haben Sie folgende Möglichkeiten:

* Einrichten eines anderen RADIUS-Servers zum Authentifizieren von Benutzern, die nicht zur Verwendung von MFA konfiguriert sind

* Erstellen Sie einen Registrierungseintrag, der den Benutzern ermöglicht, einen zweiten Authentifizierungsfaktor anzugeben, wenn sie bei Azure AD Multi-Factor Authentication registriert sind.

Erstellen Sie einen neuen Zeichenfolgenwert namens _REQUIRE_USER_MATCH in HKLM\SOFTWARE\Microsoft\AzureMfa_, und legen Sie den Wert auf *TRUE* oder *FALSE* fest.

![Einstellung „Benutzerabgleich erfordern“](./media/howto-mfa-nps-extension-vpn/image34.png)

Wenn der Wert auf *TRUE* festgelegt oder leer ist, unterliegen alle Authentifizierungsanforderungen einer MFA-Überprüfung. Wenn der Wert auf *FALSE* festgelegt ist, werden MFA-Überprüfungen nur für Benutzer ausgegeben, die bei Azure AD Multi-Factor Authentication registriert sind. Verwenden Sie die Einstellung *FALSE* nur zu Testzwecken oder in Produktionsumgebungen während des Onboardingzeitraums.



### <a name="obtain-the-azure-active-directory-tenant-id"></a>Abrufen der Azure Active Directory-Mandanten-ID

Im Rahmen der Konfiguration der NPS-Erweiterung müssen Sie Administratoranmeldeinformationen und die ID Ihres Azure AD-Mandanten angeben. Führen Sie die folgenden Schritte aus, um die Mandanten-ID abzurufen:

1. Melden Sie sich im [Azure-Portal](https://portal.azure.com) als globaler Administrator des Azure-Mandanten an.
1. Wählen Sie im Menü des Azure-Portals die Option **Azure Active Directory** aus. Sie können auch auf einer beliebigen Seite nach **Azure Active Directory** suchen und diese Option auswählen.
1. Auf der Seite **Übersicht** werden die *Mandanteninformationen* angezeigt. Wählen Sie neben der *Mandanten-ID* das Symbol **Kopieren** aus, wie im folgenden Beispielscreenshot gezeigt:

   ![Abrufen der Mandanten-ID aus dem Azure-Portal](./media/howto-mfa-nps-extension-vpn/azure-active-directory-tenant-id-portal.png)

### <a name="install-the-nps-extension"></a>Installieren der NPS-Erweiterung

Die NPS-Erweiterung muss auf einem Server installiert sein, auf dem die Rolle „Netzwerkrichtlinien- und Zugriffsdienste“ installiert ist und der in Ihrem Entwurf als RADIUS-Server dient. Installieren Sie die NPS-Erweiterung *nicht* auf Ihrem VPN-Server.

1. Laden Sie die NPS-Erweiterung aus dem [Microsoft Download Center](https://aka.ms/npsmfa) herunter.

2. Kopieren Sie die ausführbare Setupdatei (*NpsExtnForAzureMfaInstaller.exe*) auf den NPS-Server.

3. Doppelklicken Sie auf dem NPS-Server auf **NpsExtnForAzureMfaInstaller.exe**, und wählen Sie nach Aufforderung die Option **Ausführen** aus.

4. Überprüfen Sie im Fenster **NPS-Erweiterung für Azure AD MFA-Setup** die Softwarelizenzbedingungen, aktivieren Sie das Kontrollkästchen **Ich stimme den Lizenzbedingungen zu**, und wählen Sie dann **Installieren** aus.

    ![Fenster „NPS-Erweiterung für Azure AD MFA-Setup“](./media/howto-mfa-nps-extension-vpn/image36.png)

5. Wählen Sie im Fenster **NPS-Erweiterung für Azure AD MFA-Setup** die Option **Schließen** aus.  

    ![Bestätigungsfenster „Das Setup war erfolgreich.“](./media/howto-mfa-nps-extension-vpn/image37.png)

### <a name="configure-certificates-for-use-with-the-nps-extension-by-using-a-powershell-script"></a>Konfigurieren von Zertifikaten für die Verwendung mit der NPS-Erweiterung mithilfe eines PowerShell-Skripts

Um die sichere Kommunikation zu gewährleisten, konfigurieren Sie Zertifikate für die Verwendung durch die NPS-Erweiterung. Die NPS-Komponenten umfassen ein Windows PowerShell-Skript, das ein selbstsigniertes Zertifikat zur Verwendung mit NPS konfiguriert.

Dieses Skript führt folgende Aktionen aus:

* Erstellen eines selbstsignierten Zertifikats
* Zuordnen des öffentlichen Schlüssels des Zertifikats zum Dienstprinzipal in Azure AD
* Speichern des Zertifikats im Speicher des lokalen Computers
* Gewähren des Zugriffs auf den privaten Schlüssel des Zertifikats für den Netzwerkbenutzer
* Neustarten des NPS-Diensts

Wenn Sie Ihre eigenen Zertifikate verwenden möchten, müssen Sie den öffentlichen Schlüssel Ihres Zertifikats dem Dienstprinzipal in Azure AD zuordnen usw.

Um das Skript zu verwenden, geben Sie die Erweiterung mit Ihren Azure Active Directory-Administratoranmeldeinformationen und die Azure Active Directory-Mandanten-ID ein, die Sie zuvor kopiert haben. Das Konto muss demselben Azure AD-Mandanten angehören, für den Sie die Erweiterung aktivieren möchten. Führen Sie das Skript auf jedem NPS-Server aus, auf dem Sie die NPS-Erweiterung installieren.

1. Führen Sie Windows PowerShell als Administrator aus.

2. Geben Sie an der PowerShell-Eingabeaufforderung **cd "c:\Program Files\Microsoft\AzureMfa\Config"** ein, und drücken Sie die EINGABETASTE.

3. Geben Sie an der nächsten Eingabeaufforderung **.\AzureMfaNpsExtnConfigSetup.ps1** ein, und drücken Sie die EINGABETASTE. Das Skript überprüft, ob das Azure AD PowerShell-Modul installiert ist. Wenn es nicht installiert ist, installiert das Skript das Modul für Sie.

    ![Ausführung des Konfigurationsskripts „AzureMfsNpsExtnConfigSetup.ps1“](./media/howto-mfa-nps-extension-vpn/image38.png)

    Wenn aufgrund von TLS ein Sicherheitsfehler auftritt, aktivieren Sie TLS 1.2 mithilfe des Befehls `[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12` in Ihrer PowerShell-Eingabeaufforderung.
    
    Nachdem das Skript die Installation des PowerShell-Moduls überprüft hat, wird das Anmeldefenster des Azure Active Directory PowerShell-Moduls angezeigt.

4. Geben Sie Ihre Azure AD-Administratoranmeldeinformationen und das entsprechende Kennwort ein, und wählen Sie **Anmelden** aus.

    ![Authentifizierung bei Azure AD PowerShell](./media/howto-mfa-nps-extension-vpn/image39.png)

5. Fügen Sie an der Eingabeaufforderung die zuvor kopierte Mandanten-ID ein, und drücken Sie die EINGABETASTE.

    ![Eingabe der zuvor kopierten Azure AD-Mandanten-ID](./media/howto-mfa-nps-extension-vpn/image40.png)

    Das Skript erstellt ein selbstsigniertes Zertifikat und führt andere Änderungen an der Konfiguration durch. Die Ausgabe ähnelt der in der folgenden Abbildung:

    ![PowerShell-Fenster mit selbstsigniertem Zertifikat](./media/howto-mfa-nps-extension-vpn/image41.png)

6. Starten Sie den Server neu.

### <a name="verify-the-configuration"></a>Überprüfen der Konfiguration

Um die Konfiguration zu überprüfen, müssen Sie eine neue VPN-Verbindung mit dem VPN-Server herstellen. Nach erfolgreicher Eingabe Ihrer Anmeldeinformationen zur primären Authentifizierung wartet die VPN-Verbindung auf die erfolgreiche sekundäre Authentifizierung, bevor die Verbindung hergestellt wird, wie unten dargestellt.

![Fenster „Windows-Einstellungen“ für VPN](./media/howto-mfa-nps-extension-vpn/image42.png)

Nach Ihrer erfolgreichen Authentifizierung mit der sekundären Überprüfungsmethode, die Sie zuvor in Azure AD MFA konfiguriert haben, wird eine Verbindung mit der Ressource hergestellt. Wenn die sekundäre Authentifizierung jedoch nicht erfolgreich ist, wird Ihnen der Zugriff auf die Ressource verweigert.

Im folgenden Beispiel wird die sekundäre Authentifizierung über die Microsoft Authenticator-App auf einem Windows Phone bereitgestellt:

![MFA-Beispieleingabeaufforderung auf einem Windows Phone](./media/howto-mfa-nps-extension-vpn/image43.png)

Nachdem Sie mit der zweiten Methode erfolgreich authentifiziert wurden, erhalten Sie Zugriff auf den virtuellen Port des VPN-Servers. Da Sie aufgefordert wurden, eine sekundäre Authentifizierungsmethode mit einer mobilen App auf einem vertrauenswürdigen Gerät zu verwenden, ist der Anmeldevorgang sicherer als nur bei Verwendung einer Kombination aus Benutzernamen und Kennwort.

### <a name="view-event-viewer-logs-for-successful-sign-in-events"></a>Anzeigen der Protokolle der Ereignisanzeige für erfolgreiche Anmeldeereignisse

Um die erfolgreichen Anmeldeereignisse in den Protokollen der Windows-Ereignisanzeige anzuzeigen, können Sie den folgenden PowerShell-Befehl zum Abfragen des Windows-Sicherheitsprotokolls auf dem NPS-Server eingeben:

```powershell
Get-WinEvent -Logname Security | where {$_.ID -eq '6272'} | FL
```

![PowerShell-Sicherheitsereignisanzeige](./media/howto-mfa-nps-extension-vpn/image44.png)

Sie können auch das Sicherheitsprotokoll oder die benutzerdefinierte Ansicht der Netzwerkrichtlinien- und Zugriffsdienste anzeigen, wie hier dargestellt:

![Beispielprotokoll des Netzwerkrichtlinienservers](./media/howto-mfa-nps-extension-vpn/image45.png)

Auf dem Server, auf dem Sie die NPS-Erweiterung für Azure AD Multi-Factor Authentication installiert haben, finden Sie im Verzeichnis *Anwendungs- und Dienstprotokolle\Microsoft\AzureMfa* spezifische Ereignisanzeige-Anwendungsprotokolle für die Erweiterung.

```powershell
Get-WinEvent -Logname Security | where {$_.ID -eq '6272'} | FL
```

![Ereignisanzeige mit einem Beispiel für den Bereich für AuthZ-Protokolle](./media/howto-mfa-nps-extension-vpn/image46.png)

## <a name="troubleshooting-guide"></a>Handbuch zur Problembehandlung

Wenn die Konfiguration nicht wie erwartet funktioniert, sollten Sie die Problembehandlung mit der Überprüfung beginnen, ob der Benutzer zur Verwendung von MFA konfiguriert ist. Lassen Sie den Benutzer eine Verbindung mit dem [Azure-Portal](https://portal.azure.com) herstellen. Wenn der Benutzer zur sekundären Authentifizierung aufgefordert wird und sich erfolgreich authentifizieren kann, können Sie eine fehlerhafte MFA-Konfiguration ausschließen.

Wenn MFA für den Benutzer funktioniert, prüfen Sie die relevanten Protokolle der Ereignisanzeige. Dazu gehören die Sicherheitsereignis-, Gatewaybetriebs- und Azure AD Multi-Factor Authentication-Protokolle, die im vorherigen Abschnitt erläutert wurden.

Es folgt ein Beispiel für ein Sicherheitsprotokoll mit einem fehlerhaften Anmeldeereignis (Ereignis-ID 6273):

![Sicherheitsprotokoll mit einem fehlerhaften Anmeldeereignis](./media/howto-mfa-nps-extension-vpn/image47.png)

Nachfolgend sehen Sie ein zugehöriges Ereignis aus dem Azure AD Multi-Factor Authentication-Protokoll:

![Azure AD Multi-Factor Authentication-Protokolle](./media/howto-mfa-nps-extension-vpn/image48.png)

Zur erweiterten Problembehandlung nutzen Sie die NPS-Datenbankformat-Protokolldateien dort, wo der NPS-Dienst installiert ist. Die Protokolldateien werden im Ordner _%SystemRoot%\System32\Logs_ als durch Trennzeichen getrennte Textdateien erstellt. Eine Beschreibung der Protokolldateien finden Sie unter [Interpret NPS Database Format Log Files](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc771748(v=ws.10)) (Interpretieren von NPS-Datenbankformat-Protokolldateien).

Die Einträge in diesen Protokolldateien sind schwierig zu interpretieren, es sei denn, Sie importieren sie in eine Tabelle oder Datenbank. Sie finden online viele IAS-Analysetools (Internet Authentication Service), die Sie zur Interpretation der Protokolldateien verwenden können. Hier ist die Ausgabe einer solchen herunterladbaren [Sharewareanwendung](https://www.deepsoftware.com/iasviewer) abgebildet:

![Beispiel für eine IAS-Analyse einer Shareware-App](./media/howto-mfa-nps-extension-vpn/image49.png)

Zur weiteren Problembehandlung können Sie ein Protokollanalyseprogramm wie Wireshark oder [Microsoft Message Analyzer](/message-analyzer/microsoft-message-analyzer-operating-guide) verwenden. Die folgende Abbildung aus Wireshark zeigt die zwischen dem VPN-Server und Netzwerkrichtlinienserver gesendeten RADIUS-Nachrichten.

![Gefilterter Datenverkehr in Microsoft Message Analyzer](./media/howto-mfa-nps-extension-vpn/image50.png)

Weitere Informationen finden Sie unter [Integrieren Ihrer vorhandenen NPS-Infrastruktur in Azure AD Multi-Factor Authentication](howto-mfa-nps-extension.md).

## <a name="next-steps"></a>Nächste Schritte

[Erwerben von Azure AD Multi-Factor Authentication](concept-mfa-licensing.md)

[Remotedesktop-Gateway und Azure Multi-Factor Authentication-Server mithilfe von RADIUS](howto-mfaserver-nps-rdg.md)

[Integrieren Ihrer lokalen Verzeichnisse in Azure Active Directory](../hybrid/whatis-hybrid-identity.md)