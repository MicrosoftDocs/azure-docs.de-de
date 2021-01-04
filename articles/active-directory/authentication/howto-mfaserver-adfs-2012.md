---
title: Azure MFA-Server mit AD FS unter Windows Server – Azure Active Directory
description: In diesem Artikel werden die ersten Schritte mit Azure Multi-Factor Authentication und AD FS unter Windows Server 2012 R2 und 2016 beschrieben.
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
ms.openlocfilehash: 6fd1bf4f9c463973d70f5289f7a82f372d0156cb
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/06/2020
ms.locfileid: "96742544"
---
# <a name="configure-azure-multi-factor-authentication-server-to-work-with-ad-fs-in-windows-server"></a>Konfigurieren von Azure Multi-Factor Authentication-Server zur Verwendung mit AD FS unter Windows Server

Wenn Sie Active Directory-Verbunddienste (AD FS) verwenden und Cloudressourcen oder lokale Ressourcen schützen möchten, können Sie Azure Multi-Factor Authentication-Server für AD FS konfigurieren. Mit dieser Konfiguration wird die zweistufige Überprüfung für Endpunkte von hohem Wert ausgelöst.

In diesem Artikel wird die Verwendung von Azure Multi-Factor Authentication-Server mit AD FS unter Windows Server 2012 R2 oder Windows Server 2016 beschrieben. Weitere Informationen finden Sie unter [Sichern von Cloud- und lokalen Ressourcen mithilfe von Azure Multi-Factor Authentication-Server mit AD FS 2.0](howto-mfaserver-adfs-2.md).

> [!IMPORTANT]
> Seit dem 1. Juli 2019 bietet Microsoft für neue Bereitstellungen keine MFA-Server mehr an. Neue Kunden, die für die Anmeldung der Benutzer mehrstufige Authentifizierung anfordern möchten, sollten cloudbasierte Multi-Factor Authentication von Azure AD verwenden.
>
> Informationen zu den ersten Schritten mit der cloudbasierten MFA finden Sie im [Tutorial: Schützen von Benutzeranmeldeereignissen mit Azure Multi-Factor Authentication](tutorial-enable-azure-mfa.md).
>
> Wenn Sie die cloudbasierte MFA verwenden, finden Sie weitere Informationen unter [Sichern von Cloudressourcen mit Azure AD Multi-Factor Authentication und AD FS](howto-mfa-adfs.md).
>
> Bestandskunden, die ihren MFA-Server vor dem 1. Juli 2019 aktiviert haben, können weiterhin die neuesten Versionen und zukünftige Updates herunterladen sowie Anmeldedaten zur Aktivierung generieren.

## <a name="secure-windows-server-ad-fs-with-azure-multi-factor-authentication-server"></a>Schützen von AD FS unter Windows Server mit Azure Multi-Factor Authentication-Server

Bei der Installation von Azure Multi-Factor Authentication-Server haben Sie die folgenden Möglichkeiten:

* Lokale Installation von Azure Multi-Factor Authentication-Server auf demselben Server wie AD FS
* Lokale Installation von Azure Multi-Factor Authentication-Adapter auf dem AD FS-Server und anschließende Installation von Multi-Factor Authentication-Server auf einem anderen Computer

Bevor Sie beginnen, sollten Sie Folgendes beachten:

* Sie müssen Azure Multi-Factor Authentication-Server nicht auf Ihrem AD FS-Server installieren. Aber Sie müssen den Multi-Factor Authentication-Adapter für AD FS auf einem Computer unter Windows Server 2012 R2 oder Windows Server 2016 installieren, auf dem AD FS ausgeführt wird. Sie können den Server auf einem anderen Computer installieren, wenn Sie den AD FS-Adapter separat auf dem AD FS-Verbundserver installieren. In den folgenden Verfahren wird die separate Installation des Adapters beschrieben.
* Wenn Ihre Organisation als Überprüfungsmethode SMS oder mobile Apps verwendet, enthalten die in den Unternehmenseinstellungen festgelegten Zeichenfolgen einen Platzhalter: <$*application_name*$>. In MFA Server v7.1 können Sie einen Anwendungsnamen angeben, der diesen Platzhalter ersetzt. Bis einschließlich Version 7.0 wird dieser Platzhalter nicht automatisch ersetzt, wenn Sie den AD FS-Adapter verwenden. Entfernen Sie bei diesen älteren Versionen den Platzhalter aus den entsprechenden Zeichenfolgen, wenn Sie AD FS schützen.
* Das Konto, das Sie zum Anmelden verwenden, muss über Benutzerrechte zum Erstellen von Sicherheitsgruppen im Active Directory-Dienst verfügen.
* Der Installations-Assistent für den Multi-Factor Authentication-AD FS-Adapter erstellt eine Sicherheitsgruppe mit dem Namen „PhoneFactor Admins“ in Ihrer Instanz von Active Directory. Anschließend wird dieser Gruppe das AD FS-Dienstkonto Ihres Verbunddiensts hinzugefügt. Vergewissern Sie sich, dass die Gruppe „PhoneFactor Admins“ auf Ihrem Domänencontroller wirklich erstellt wurde und dass das AD FS-Dienstkonto dieser Gruppe angehört. Bei Bedarf fügen Sie das AD FS-Dienstkonto der Gruppe „PhoneFactor Admins“ auf dem Domänencontroller manuell hinzu.
* Informationen zum Installieren des Webdienst-SDK mit dem Benutzerportal finden Sie unter [Bereitstellen des Benutzerportals für den Azure Multi-Factor Authentication-Server](howto-mfaserver-deploy-userportal.md).

### <a name="install-azure-multi-factor-authentication-server-locally-on-the-ad-fs-server"></a>Lokale Installation von Azure Multi-Factor Authentication-Server auf dem AD FS-Server

1. Laden Sie Azure Multi-Factor Authentication-Server herunter, und installieren Sie diese Komponente auf dem AD FS-Server. Informationen zur Installation finden Sie unter [Erste Schritte mit Azure Multi-Factor Authentication-Server](howto-mfaserver-deploy.md).
2. Klicken Sie in der Verwaltungskonsole von Azure Multi-Factor Authentication-Server auf das Symbol **AD FS**. Wählen Sie die Optionen **Benutzerregistrierung zulassen** und **Benutzern die Auswahl der Methode ermöglichen** aus.
3. Wählen Sie alle weiteren Optionen aus, die Sie für Ihre Organisation angeben möchten.
4. Klicken Sie auf **AD FS-Adapter installieren**.

   ![Installieren des ADFS-Adapters über die MFA-Serverkonsole](./media/howto-mfaserver-adfs-2012/server.png)

5. Wenn das Active Directory-Fenster angezeigt wird, bedeutet dies zwei Dinge: Ihr Computer ist in eine Domäne eingebunden, und die Active Directory-Konfiguration zum Schützen der Kommunikation zwischen dem AD FS-Adapter und dem Multi-Factor Authentication-Dienst ist unvollständig. Klicken Sie auf **Weiter**, um diese Konfiguration automatisch abzuschließen, oder aktivieren Sie das Kontrollkästchen **Automatische Active Directory-Konfiguration überspringen und Einstellungen manuell konfigurieren**. Klicken Sie auf **Weiter**.
6. Wenn das Fenster „Lokale Gruppe“ angezeigt wird, bedeutet dies zwei Dinge: Ihr Computer ist nicht in eine Domäne eingebunden, und die Konfiguration der lokalen Gruppe zum Schützen der Kommunikation zwischen dem AD FS-Adapter und dem Multi-Factor Authentication-Dienst ist unvollständig. Klicken Sie auf **Weiter**, um diese Konfiguration automatisch abzuschließen, oder aktivieren Sie das Kontrollkästchen **Automatische Konfiguration der lokalen Gruppe überspringen und Einstellungen manuell konfigurieren**. Klicken Sie auf **Weiter**.
7. Klicken Sie im Installations-Assistenten auf **Weiter**. Azure Multi-Factor Authentication-Server erstellt die Gruppe „PhoneFactor Admins“ und fügt das AD FS-Dienstkonto der Gruppe „PhoneFactor Admins“ hinzu.
8. Klicken Sie auf der Seite **Installer starten** auf **Weiter**.
9. Klicken Sie im Installer für den Multi-Factor Authentication AD FS-Adapter auf **Weiter**.
10. Klicken Sie auf **Schließen** , wenn die Installation abgeschlossen ist.
11. Nach der Installation des Adapters müssen Sie ihn bei AD FS registrieren. Öffnen Sie Windows PowerShell, und führen Sie den folgenden Befehl aus:

    `C:\Program Files\Multi-Factor Authentication Server\Register-MultiFactorAuthenticationAdfsAdapter.ps1`

12. Bearbeiten Sie die globale Authentifizierungsrichtlinie in AD FS, damit der neu registrierte Adapter verwendet werden kann. Wechseln Sie in der AD FS-Verwaltungskonsole zum Knoten **Authentifizierungsrichtlinien** . Klicken Sie im Abschnitt **Multi-Factor Authentication** auf den Link **Bearbeiten** neben dem Abschnitt **Globale Einstellungen**. Wählen Sie im Fenster **Globale Authentifizierungsrichtlinie bearbeiten** als zusätzliche Authentifizierungsmethode **Multi-Factor Authentication** aus, und klicken Sie dann auf **OK**. Der Adapter wird als "WindowsAzureMultiFactorAuthentication" registriert. Starten Sie den AD FS-Dienst neu, damit die Registrierung wirksam wird.

![Bearbeiten der globalen Authentifizierungsrichtlinie](./media/howto-mfaserver-adfs-2012/global.png)

An diesem Punkt ist Multi-Factor Authentication-Server als zusätzlicher Authentifizierungsanbieter für die Verwendung mit AD FS eingerichtet.

## <a name="install-a-standalone-instance-of-the-ad-fs-adapter-by-using-the-web-service-sdk"></a>Installieren einer eigenständigen Instanz des AD FS-Adapters mit dem Webdienst-SDK

1. Installieren Sie das Webdienst-SDK auf dem Server, auf dem Multi-Factor Authentication-Server ausgeführt wird.
2. Kopieren Sie die folgenden Dateien aus dem Verzeichnis „\Programme\Multi-Factor Authentication Server“ auf den Server, auf dem Sie den AD FS-Adapter installieren möchten:
   * MultiFactorAuthenticationAdfsAdapterSetup64.msi
   * Register-MultiFactorAuthenticationAdfsAdapter.ps1
   * Unregister-MultiFactorAuthenticationAdfsAdapter.ps1
   * MultiFactorAuthenticationAdfsAdapter.config
3. Führen Sie die Installationsdatei „MultiFactorAuthenticationAdfsAdapterSetup64.msi“ aus.
4. Klicken Sie im Multi-Factor Authentication AD FS-Adapter-Installer auf **Weiter**, um die Installation zu starten.
5. Klicken Sie auf **Schließen** , wenn die Installation abgeschlossen ist.

## <a name="edit-the-multifactorauthenticationadfsadapterconfig-file"></a>Bearbeiten der Datei „MultiFactorAuthenticationAdfsAdapter.config“

Führen Sie die folgenden Schritte aus, um die Datei „MultiFactorAuthenticationAdfsAdapter.config“ zu bearbeiten:

1. Legen Sie den Knoten **UseWebServiceSdk** auf **true** fest.  
2. Legen Sie den Wert für **WebServiceSdkUrl** auf die URL des Multi-Factor Authentication-Webdienst-SDKs fest. Beispiel: *https:\/\/contoso.com/\<certificatename>/MultiFactorAuthWebServiceSdk/PfWsSdk.asmx*, wobei *\<certificatename>* der Name Ihres Zertifikats ist.  
3. Bearbeiten Sie das Skript „Register-MultiFactorAuthenticationAdfsAdapter.ps1“, indem Sie `-ConfigurationFilePath &lt;path&gt;` am Ende des Befehls `Register-AdfsAuthenticationProvider` hinzufügen, wobei *&lt;path&gt;* der vollständige Pfad zur Datei „MultiFactorAuthenticationAdfsAdapter.config“ ist.

### <a name="configure-the-web-service-sdk-with-a-username-and-password"></a>Konfigurieren des Webdienst-SDKs mit einem Benutzernamen und einem Kennwort

Das Webdienst-SDK kann auf zwei Arten konfiguriert werden: mit einem Benutzernamen und einem Kennwort oder mit einem Clientzertifikat. Im Anschluss finden Sie die Schritte für die erste Option. Informationen zur zweiten Option finden Sie weiter unten.  

1. Legen Sie den Wert für **WebServiceSdkUsername** auf ein Konto fest, das Mitglied der Sicherheitsgruppe „PhoneFactor Admins“ ist. Verwenden Sie das Format &lt;Domäne&gt;&#92;&lt;Benutzername&gt;.  
2. Legen Sie den Wert für **WebServiceSdkPassword** auf das entsprechende Kontokennwort fest.

### <a name="configure-the-web-service-sdk-with-a-client-certificate"></a>Konfigurieren des Webdienst-SDKs mit einem Clientzertifikat

Falls Sie keine Kombination aus Benutzername und Kennwort verwenden möchten, gehen Sie wie folgt vor, um das Webdienst-SDK mit einem Clientzertifikat zu konfigurieren.

1. Beziehen Sie für den Server mit dem Webdienst-SDK ein Clientzertifikat von einer Zertifizierungsstelle. Informationen dazu finden Sie [hier](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770328(v=ws.10)).  
2. Importieren Sie das Clientzertifikat auf dem Server mit dem Webdienst-SDK in den persönlichen Zertifikatspeicher des lokalen Computers. Vergewissern Sie sich, dass das öffentliche Zertifikat der Zertifizierungsstelle im Zertifikatspeicher für die vertrauenswürdigen Stammzertifikate enthalten ist.  
3. Exportieren Sie den öffentlichen und privaten Schlüssel des Clientzertifikats in eine PFX-Datei.  
4. Exportieren Sie den öffentlichen Schlüssel im Base64-Format in eine CER-Datei.  
5. Vergewissern Sie sich im Server-Manager, dass das Feature „Webserver (IIS)\Webserver\Sicherheit\Authentifizierung über IIS-Clientzertifikatzuordnung“ installiert ist. Falls nicht, wählen Sie **Rollen und Features hinzufügen** aus, um das Feature hinzuzufügen.  
6. Doppelklicken Sie in IIS-Manager für die Website, die das virtuelle Verzeichnis des Webdienst-SDKs enthält, auf **Konfigurations-Editor** . Achten Sie darauf, die Website auszuwählen, nicht das virtuelle Verzeichnis.  
7. Navigieren Sie zum Abschnitt **system.webServer/security/authentication/iisClientCertificateMappingAuthentication** .  
8. Legen Sie „enabled“ auf **true** fest.  
9. Legen Sie „oneToOneCertificateMappingsEnabled“ auf **true** fest.  
10. Klicken Sie neben „oneToOneMappings“ auf die Schaltfläche **...** und anschließend auf den Link **Hinzufügen**.  
11. Öffnen Sie die zuvor exportierte Datei „Base64.cer“. Entfernen Sie *-----BEGIN CERTIFICATE-----* , *-----END CERTIFICATE-----* sowie alle Zeilenumbrüche. Kopieren Sie die resultierende Zeichenfolge.  
12. Legen Sie „certificate“ auf die im vorherigen Schritt kopierte Zeichenfolge fest.  
13. Legen Sie „enabled“ auf **true** fest.  
14. Legen Sie „userName“ auf ein Konto fest, das Mitglied der Sicherheitsgruppe „PhoneFactor Admins“ ist. Verwenden Sie das Format &lt;Domäne&gt;&#92;&lt;Benutzername&gt;.  
15. Legen Sie das Kennwort auf das entsprechende Kontokennwort fest, und schließen Sie den Konfigurations-Editor.  
16. Klicken Sie auf den Link **Übernehmen** .  
17. Doppelklicken Sie im virtuellen Verzeichnis des Webdienst-SDKs auf **Authentifizierung**.  
18. Vergewissern Sie sich, dass ASP.NET-Identitätswechsel und Standardauthentifizierung **aktiviert** und alle anderen Elemente **deaktiviert** sind.  
19. Doppelklicken Sie im virtuellen Verzeichnis des Webdienst-SDKs auf **SSL-Einstellungen**.  
20. Legen Sie „Clientzertifikate“ auf **Akzeptieren** fest, und klicken Sie auf **Übernehmen**.  
21. Kopieren Sie die zuvor exportierte PFX-Datei auf den Server, auf dem der AD FS-Adapter ausgeführt wird.  
22. Importieren Sie die PFX-Datei in den persönlichen Zertifikatspeicher des lokalen Computers.  
23. Klicken Sie mit der rechten Maustaste, wählen Sie **Private Schlüssel verwalten** aus, und gewähren Sie dem Konto, das Sie für die Anmeldung beim Active Directory-Verbunddienst verwendet haben, Lesezugriff.  
24. Öffnen Sie das Clientzertifikat, und kopieren Sie den Fingerabdruck aus der Registerkarte **Details** .  
25. Legen Sie in der Datei „MultiFactorAuthenticationAdfsAdapter.config“ das Element **WebServiceSdkCertificateThumbprint** auf die im vorherigen Schritt kopierte Zeichenfolge fest.  

Führen Sie schließlich das Skript „\Programme\Multi-Factor Authentication Server\Register-MultiFactorAuthenticationAdfsAdapter.ps1“ in PowerShell aus, um den Adapter zu registrieren. Der Adapter wird als "WindowsAzureMultiFactorAuthentication" registriert. Starten Sie den AD FS-Dienst neu, damit die Registrierung wirksam wird.

## <a name="secure-azure-ad-resources-using-ad-fs"></a>Schützen von Azure AD-Ressourcen mit AD FS

Zum Schützen Ihrer Cloudressource richten Sie eine Anspruchsregel ein, damit Active Directory-Verbunddienste (AD FS)den multipleauthn-Anspruch ausgibt, wenn ein Benutzer die zweistufige Überprüfung erfolgreich durchführt. Dieser Anspruch wird an Azure AD übergeben. Die Schritte werden im folgenden Verfahren veranschaulicht:

1. Öffnen Sie die AD FS-Verwaltung.
2. Wählen Sie auf der linken Seite die Option **Vertrauensstellungen der vertrauenden Seite**.
3. Klicken Sie mit der rechten Maustaste auf **Microsoft Office 365 Identity Platform**, und wählen Sie **Anspruchsregeln bearbeiten**.

   ![Bearbeiten von Anspruchsregeln in der AD FS-Konsole](./media/howto-mfaserver-adfs-2012/trustedip1.png)

4. Klicken Sie unter „Ausstellungstransformationsregeln“ auf **Regel hinzufügen**.

   ![Bearbeiten von Transformationsregeln in der AD FS-Konsole](./media/howto-mfaserver-adfs-2012/trustedip2.png)

5. Wählen Sie im Assistenten zum Hinzufügen von Transformationsanspruchsregeln im Dropdownmenü die Option **Passthrough oder eingehenden Anspruch filtern**, und klicken Sie auf **Weiter**.

   ![Assistent zum Hinzufügen von Transformationsanspruchsregeln](./media/howto-mfaserver-adfs-2012/trustedip3.png)

6. Benennen Sie die Regel.
7. Wählen Sie **Authentifizierungsmethodenreferenzen** als eingehenden Anspruchstyp aus.
8. Wählen Sie **Durchlauf aller Anspruchswerte**.

    ![Assistenten zum Hinzufügen von Transformationsanspruchsregeln hinzufügen](./media/howto-mfaserver-adfs-2012/configurewizard.png)

9. Klicken Sie auf **Fertig stellen**. Schließen Sie die AD FS-Verwaltungskonsole.

## <a name="troubleshooting-logs"></a>Problembehandlungsprotokolle

Wenn Sie Unterstützung beim Behandeln von Problemen mit dem AD FS-Adapter des MFA-Servers benötigen, führen Sie die folgenden Schritte aus, um die zusätzliche Protokollierung zu aktivieren.

1. Öffnen Sie auf der Oberfläche des MFA-Servers den Abschnitt für AD FS, und aktivieren Sie das Kontrollkästchen **Protokollierung aktivieren**.
2. Verwenden Sie auf jedem AD FS-Server **regedit.exe**, um den Registrierungsschlüssel `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Positive Networks\PhoneFactor\InstallPath` mit dem Wert`C:\Program Files\Multi-Factor Authentication Server\` (oder einem anderen Verzeichnis Ihrer Wahl) zu erstellen.  **Der nachgestellte umgekehrte Schrägstrich ist wichtig.**
3. Erstellen Sie das Verzeichnis `C:\Program Files\Multi-Factor Authentication Server\Logs` (oder ein anderes Verzeichnis wie in **Schritt 2** erwähnt).
4. Gewähren Sie dem AD FS-Dienstkonto Änderungszugriff auf das Protokollverzeichnis.
5. Starten Sie den AD FS-Dienst neu.
6. Überprüfen Sie, ob die Datei `MultiFactorAuthAdfsAdapter.log` im Protokollverzeichnis erstellt wurde.

## <a name="related-topics"></a>Verwandte Themen

Hilfe bei der Problembehandlung finden Sie unter [Azure Multi-Factor Authentication – Häufig gestellte Fragen](multi-factor-authentication-faq.md)