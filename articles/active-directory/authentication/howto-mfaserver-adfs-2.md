---
title: Verwenden von Azure MFA Server mit AD FS 2.0 – Azure Active Directory
description: Auf dieser Seite zur Azure Multi-Factor Authentication werden die ersten Schritte mit Azure MFA und AD FS 2.0 beschrieben.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/11/2018
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: d144716dbc5636451405f1124d4c3949f585d6b2
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/06/2020
ms.locfileid: "96742645"
---
# <a name="configure-azure-multi-factor-authentication-server-to-work-with-ad-fs-20"></a>Konfigurieren des Azure Multi-Factor Authentication-Servers zur Verwendung von AD FS 2.0

Dieser Artikel richtet sich an Organisationen, die sind in einem Azure Active Directory-Verbund befinden und lokale Ressourcen oder Ressourcen in der Cloud schützen möchten. Schützen Sie Ihre Ressourcen mithilfe des Azure Multi-Factor Authentication-Servers, und konfigurieren Sie ihn für die Verwendung von AD FS, damit für wichtige Endpunkte die zweistufige Überprüfung ausgelöst wird.

In dieser Dokumentation wird die Verwendung von Azure Multi-Factor Authentication-Server mit AD FS 2.0 behandelt. Weitere Informationen zu AD FS finden Sie unter [Schützen von Cloudressourcen und lokalen Ressourcen mithilfe von Azure Multi-Factor Authentication-Server mit AD FS unter Windows Server 2012 R2](howto-mfaserver-adfs-2012.md).

> [!IMPORTANT]
> Seit dem 1. Juli 2019 bietet Microsoft für neue Bereitstellungen keine MFA-Server mehr an. Neue Kunden, die bei Benutzeranmeldeereignissen eine mehrstufige Authentifizierung (Multi-Factor Authentication, MFA) verlangen möchten, sollten cloudbasierte Azure AD Multi-Factor Authentication verwenden.
>
> Informationen zu den ersten Schritten mit der cloudbasierten MFA finden Sie im [Tutorial: Schützen von Benutzeranmeldeereignissen mit Azure AD Multi-Factor Authentication](tutorial-enable-azure-mfa.md).
>
> Wenn Sie die cloudbasierte MFA verwenden, lesen Sie [Schützen von Cloudressourcen mit Azure AD Multi-Factor Authentication und AD FS](howto-mfa-adfs.md).
>
> Bestandskunden, die ihren MFA-Server vor dem 1. Juli 2019 aktiviert haben, können weiterhin die neuesten Versionen und zukünftige Updates herunterladen sowie Anmeldedaten zur Aktivierung generieren.

## <a name="secure-ad-fs-20-with-a-proxy"></a>Schützen von AD FS 2.0 mit einem Proxy

Wenn Sie AD FS 2.0 mit einem Proxy schützen möchten, müssen Sie den Azure Multi-Factor Authentication-Server auf dem AD FS-Proxyserver installieren.

### <a name="configure-iis-authentication"></a>Konfigurieren der IIS-Authentifizierung

1. Klicken Sie im Azure Multi-Factor Authentication-Server im linken Menü auf das Symbol **IIS-Authentifizierung**.
2. Klicken Sie auf die Registerkarte **Formularbasiert**.
3. Klicken Sie auf **Hinzufügen**.

   ![Fenster „IIS-Authentifizierung“ in MFA Server](./media/howto-mfaserver-adfs-2/setup1.png)

4. Geben Sie zur automatischen Erkennung von Benutzername, Kennwort und Domänenvariablen im Dialogfeld „Formularbasierte Website automatisch konfigurieren“ die Anmelde-URL (beispielsweise `https://sso.contoso.com/adfs/ls`) ein, und klicken Sie auf **OK**.
5. Aktivieren Sie das Kontrollkästchen **Multi-Factor Authentication-Benutzerabgleich erfordern**, wenn alle Benutzer in den Server importiert wurden oder werden und die zweistufige Überprüfung verwendet werden soll. Wenn eine hohe Anzahl von Benutzern noch nicht in den Server importiert wurde und/oder von der zweistufigen Überprüfung ausgenommen werden soll, lassen Sie das Kontrollkästchen deaktiviert.
6. Falls die Seitenvariablen nicht automatisch erkannt werden können, klicken Sie auf die Schaltfläche **Manuell angeben...** (im Dialogfeld „Formularbasierte Website automatisch konfigurieren“).
7. Geben Sie im Dialogfeld „Formularbasierte Website hinzufügen“ im Feld „Sende-URL“ die URL der AD FS-Anmeldeseite (z. B. `https://sso.contoso.com/adfs/ls`) und optional einen Anwendungsnamen ein. Der Anwendungsname wird in Azure Multi-Factor Authentication-Berichten und möglicherweise auch in Authentifizierungsnachrichten in SMS oder der mobilen App angezeigt.
8. Legen Sie das Anforderungsformat auf **POST oder GET** fest.
9. Geben Sie die Username-Variable (ctl00$ContentPlaceHolder1$UsernameTextBox) und die Password-Variable (ctl00$ContentPlaceHolder1$PasswordTextBox) ein. Wenn auf Ihrer formularbasierten Anmeldeseite ein Textfeld für die Domäne angezeigt wird, geben Sie auch die Domain-Variable ein. Navigieren Sie in einem Webbrowser zur Anmeldeseite, klicken Sie mit der rechten Maustaste auf die Seite, und wählen Sie **Quelltext anzeigen** aus, um die Namen der Eingabefelder auf der Anmeldeseite zu ermitteln.
10. Aktivieren Sie das Kontrollkästchen **Multi-Factor Authentication-Benutzerabgleich erfordern**, wenn alle Benutzer in den Server importiert wurden oder werden und die zweistufige Überprüfung verwendet werden soll. Wenn eine hohe Anzahl von Benutzern noch nicht in den Server importiert wurde und/oder von der zweistufigen Überprüfung ausgenommen werden soll, lassen Sie das Kontrollkästchen deaktiviert.

    ![Hinzufügen einer formularbasierten Website zu MFA Server](./media/howto-mfaserver-adfs-2/manual.png)

11. Klicken Sie auf **Erweitert** um die erweiterten Einstellungen anzuzeigen. Hier haben Sie unter anderem folgende Möglichkeiten:

    - Sie können eine Datei für eine benutzerdefinierte Verweigerungsseite auswählen.
    - Sie können erfolgreiche Authentifizierungen bei der Website mithilfe von Cookies speichern.
    - Sie können auswählen, wie die primären Anmeldeinformationen authentifiziert werden sollen.

12. Da der AD FS-Proxyserver wahrscheinlich nicht der Domäne angehört, können Sie LDAP verwenden, um für den Benutzerimport und die Vorauthentifizierung eine Verbindung mit dem Domänencontroller herzustellen. Klicken Sie im Dialogfeld „Erweiterte formularbasierte Website“ auf die Registerkarte **Primäre Authentifizierung**, und wählen Sie als Vorauthentifizierungstyp die Option **LDAP-Bindung** aus.
13. Klicken Sie abschließend auf **OK**, um zum Dialogfeld „Formularbasierte Website hinzufügen“ zurückzukehren.
14. Klicken Sie auf **OK** , um das Dialogfeld zu schließen.
15. Sobald die URL und die Seitenvariablen erkannt bzw. eingegeben wurden, werden die Websitedaten im Bereich „Formularbasiert“ angezeigt.
16. Klicken Sie auf die Registerkarte **Systemeigenes Modul**, und wählen Sie den Server, die Website, unter der der AD FS-Proxy ausgeführt wird (z. B. „Standardwebsite“), oder die AD FS-Proxyanwendung (z. B. „ls“ unter „adfs“) aus, um das IIS-Plug-In auf der gewünschten Ebene zu aktivieren.
17. Aktivieren Sie im oberen Bildschirmbereich das Kontrollkästchen **IIS-Authentifizierung aktivieren**.

Die IIS-Authentifizierung ist jetzt aktiviert.

### <a name="configure-directory-integration"></a>Konfigurieren der Verzeichnisintegration

Sie haben zwar die IIS-Authentifizierung aktiviert, um die Vorauthentifizierung für Active Directory (AD) über LDAP durchzuführen, müssen Sie jedoch noch die LDAP-Verbindung mit dem Domänencontroller konfigurieren.

1. Klicken Sie auf das Symbol **Verzeichnisintegration**.
2. Aktivieren Sie auf der Registerkarte „Einstellungen“ das Optionsfeld **Bestimmte LDAP-Konfiguration verwenden**.

   ![Konfigurieren der LDAP-Einstellungen für bestimmte LDAP-Einstellungen](./media/howto-mfaserver-adfs-2/ldap1.png)

3. Klicken Sie auf **Bearbeiten**.
4. Füllen Sie im Dialogfeld "LDAP-Konfiguration bearbeiten" die Felder mit den Informationen aus, die für die Verbindung mit dem Active Directory-Domänencontroller erforderlich sind. Beschreibungen der Felder sind in der Hilfedatei zum Azure Multi-Factor Authentication-Server enthalten.
5. Testen Sie die LDAP-Verbindung durch Klicken auf die Schaltfläche **Testen**.

   ![Testen der LDAP-Konfiguration in MFA Server](./media/howto-mfaserver-adfs-2/ldap2.png)

6. Wenn der Test der LDAP-Verbindung erfolgreich war, klicken Sie auf **OK**.

### <a name="configure-company-settings"></a>Konfigurieren der Unternehmenseinstellungen

1. Klicken Sie als Nächstes auf das Symbol **Unternehmenseinstellungen**, und wählen Sie die Registerkarte **Benutzernamenauflösung** aus.
2. Wählen Sie das Optionsfeld **Eindeutiges LDAP-Bezeichnerattribut für den Abgleich von Benutzernamen verwenden** aus.
3. Wenn Benutzer ihren Benutzernamen im Format „Domäne\Benutzername“ eingeben, muss der Server beim Erstellen der LDAP-Abfrage die Domäne vom Benutzernamen trennen können. Dazu kann eine Registrierungseinstellung verwendet werden.
4. Öffnen Sie den Registrierungs-Editor auf einem 64-Bit-Server, und navigieren Sie zu "HKEY_LOCAL_MACHINE/SOFTWARE/Wow6432Node/Positive Networks/PhoneFactor". Auf einem 32-Bit-Server fehlt „Wow6432Node“ im Pfad. Erstellen Sie einen DWORD-Registrierungsschlüssel namens „UsernameCxz_stripPrefixDomain“, und legen Sie den Wert auf „1“ fest. Der AD FS-Proxy wird nun durch Azure Multi-Factor Authentication geschützt.

Stellen Sie sicher, dass Benutzer aus Active Directory in den Server importiert wurden. Wenn Sie interne IP-Adressen zulassen möchten, sodass bei einer Websiteanmeldung über diese Adressen keine zweistufige Überprüfung erforderlich ist, lesen Sie den Abschnitt [Vertrauenswürdige IP-Adressen](#trusted-ips).

![Registrierungs-Editor zum Konfigurieren von Unternehmenseinstellungen](./media/howto-mfaserver-adfs-2/reg.png)

## <a name="ad-fs-20-direct-without-a-proxy"></a>AD FS 2.0 – direkt, ohne Proxy

AD FS kann auch ohne Verwendung des AD FS-Proxys geschützt werden. Installieren Sie den Azure Multi-Factor Authentication-Server auf dem AD FS-Server, und konfigurieren Sie ihn wie folgt:

1. Klicken Sie im Azure Multi-Factor Authentication-Server im linken Menü auf das Symbol **IIS-Authentifizierung**.
2. Klicken Sie auf die Registerkarte **HTTP**.
3. Klicken Sie auf **Hinzufügen**.
4. Geben Sie im Dialogfeld „Basis-URL hinzufügen“ im Feld „Basis-URL“ die URL für die AD FS-Website ein, auf der die HTTP-Authentifizierung erfolgt (beispielsweise `https://sso.domain.com/adfs/ls/auth/integrated`). Geben Sie dann einen Anwendungsnamen ein (optional). Der Anwendungsname wird in Azure Multi-Factor Authentication-Berichten und möglicherweise auch in Authentifizierungsnachrichten in SMS oder der mobilen App angezeigt.
5. Passen Sie bei Bedarf die Leerlaufzeitüberschreitung und die maximale Sitzungsdauer an.
6. Aktivieren Sie das Kontrollkästchen **Multi-Factor Authentication-Benutzerabgleich erfordern**, wenn alle Benutzer in den Server importiert wurden oder werden und die zweistufige Überprüfung verwendet werden soll. Wenn eine hohe Anzahl von Benutzern noch nicht in den Server importiert wurde und/oder von der zweistufigen Überprüfung ausgenommen werden soll, lassen Sie das Kontrollkästchen deaktiviert.
7. Aktivieren Sie bei Bedarf das Kontrollkästchen für das Cookie zur Zwischenspeicherung.

   ![AD FS 2.0 – direkt, ohne Proxy](./media/howto-mfaserver-adfs-2/noproxy.png)

8. Klicken Sie auf **OK**.
9. Klicken Sie auf die Registerkarte **Systemeigenes Modul**, und wählen Sie den Server, die Website (z. B. „Standardwebsite“) oder die AD FS-Anwendung (z. B. „ls“ unter „adfs“) aus, um das IIS-Plug-In auf der gewünschten Ebene zu aktivieren.
10. Aktivieren Sie im oberen Bildschirmbereich das Kontrollkästchen **IIS-Authentifizierung aktivieren**.

AD FS wird nun durch Azure Multi-Factor Authentication geschützt.

Stellen Sie sicher, dass Benutzer aus Active Directory in den Server importiert wurden. Wenn Sie interne IP-Adressen zulassen möchten, sodass bei einer Websiteanmeldung über diese Adressen keine zweistufige Überprüfung erforderlich ist, lesen Sie den Abschnitt „Vertrauenswürdige IP-Adressen“.

## <a name="trusted-ips"></a>Vertrauenswürdige IP-Adressen

Über vertrauenswürdige IP-Adressen können Benutzer Azure Multi-Factor Authentication bei Websiteanforderungen von bestimmten IP-Adressen oder Subnetzen umgehen. So können Sie beispielsweise Benutzer, die sich vom Büro aus anmelden, von der zweistufigen Überprüfung ausschließen. Dazu geben Sie das Bürosubnetz als Eintrag unter „Vertrauenswürdige IPs“ an.

### <a name="to-configure-trusted-ips"></a>So konfigurieren Sie vertrauenswürdige IP-Adressen

1. Klicken Sie im Abschnitt „IIS-Authentifizierung“ auf die Registerkarte **Vertrauenswürdige IPs**.
2. Klicken Sie in der Eigenschaft **Hinzufügen…** .
3. Wählen Sie im daraufhin angezeigten Dialogfeld „Vertrauenswürdige IP hinzufügen“ das Optionsfeld **Eine IP**, **IP-Bereich** oder **Subnetz** aus.
4. Geben Sie die IP-Adresse, den IP-Adressbereich oder das Subnetz ein, die zulässig sein sollen. Wählen Sie im Falle eines Subnetzes die entsprechende Netzmaske aus, und klicken Sie auf die Schaltfläche **OK**.

![Konfigurieren vertrauenswürdiger IP-Adressen für MFA Server](./media/howto-mfaserver-adfs-2/trusted.png)
