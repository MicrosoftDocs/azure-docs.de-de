---
title: Azure AD-Pass-Through-Authentifizierung – Schnellstart | Microsoft-Dokumentation
description: In diesem Artikel wird beschrieben, wie Sie die ersten Schritte für die Azure AD-Passthrough-Authentifizierung (Azure Active Directory) ausführen.
services: active-directory
keywords: Passthrough-Authentifizierung mit Azure AD Connect, Active Directory installieren, erforderliche Komponenten für Azure AD, SSO, einmaliges Anmelden
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 04/13/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8bdfb1ca21860f1dc338f85a82caf643f9f7be6d
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95973200"
---
# <a name="azure-active-directory-pass-through-authentication-quickstart"></a>Azure Active Directory-Passthrough-Authentifizierung: Schnellstart

## <a name="deploy-azure-ad-pass-through-authentication"></a>Bereitstellen der Azure AD-Passthrough-Authentifizierung

Mit der Azure Active Directory-Passthrough-Authentifizierung (Azure AD) können sich Benutzer mit denselben Kennwörtern sowohl bei lokalen als auch bei cloudbasierten Anwendungen anmelden. Benutzer werden bei der Passthrough-Authentifizierung angemeldet, indem sie ihre Kennwörter direkt für Ihre lokale Active Directory-Instanz angeben.

>[!IMPORTANT]
>Wenn Sie von AD FS (oder andere Verbundtechnologien nutzen) zur Passthrough-Authentifizierung migrieren, wird dringend empfohlen, dem ausführlichen [Leitfaden zur Bereitstellung](https://aka.ms/adfstoPTADPDownload) zu folgen.

>[!NOTE]
>Wenn Sie die Pass-Through-Authentifizierung in der Azure Government-Cloud bereitstellen, lesen Sie die [Überlegungen zur Hybrididentität für Azure Government](./reference-connect-government-cloud.md).

Befolgen Sie diese Anweisungen, um die Passthrough-Authentifizierung bereitzustellen:

## <a name="step-1-check-the-prerequisites"></a>Schritt 1: Überprüfen der Voraussetzungen

Stellen Sie sicher, dass die folgenden Voraussetzungen erfüllt werden:

>[!IMPORTANT]
>Vom Sicherheitsstandpunkt aus sollten Administratoren den Server, auf dem der PTA-Agent ausgeführt wird, so behandeln, als sei er ein Domänencontroller.  Die PTA-Agent-Server sollten wie in [Sichern von Domänencontrollern gegen Angriffe](/windows-server/identity/ad-ds/plan/security-best-practices/securing-domain-controllers-against-attack) beschrieben geschützt werden.

### <a name="in-the-azure-active-directory-admin-center"></a>Im Azure Active Directory Admin Center

1. Erstellen Sie in Ihrem Azure AD-Mandanten ein auf die Cloud beschränktes globales Administratorkonto. Auf diese Weise können Sie die Konfiguration Ihres Mandanten verwalten, falls Ihre lokalen Dienste ausfallen oder nicht verfügbar sind. Erfahren Sie, wie Sie ein [rein cloudbasiertes Konto für den globalen Administrator hinzufügen](../fundamentals/add-users-azure-active-directory.md). Die Ausführung dieses Schritts ist sehr wichtig, damit sichergestellt ist, dass Sie für Ihren Mandanten nicht gesperrt werden.
2. Fügen Sie Ihrem Azure AD-Mandanten mindestens einen [benutzerdefinierten Domänennamen](../fundamentals/add-custom-domain.md) hinzu. Ihre Benutzer können sich mit einem dieser Domänennamen anmelden.

### <a name="in-your-on-premises-environment"></a>In Ihrer lokalen Umgebung

1. Identifizieren Sie einen Server mit Windows Server 2012 R2 oder höher, auf dem Azure AD Connect ausgeführt werden soll. Wenn nicht bereits aktiviert, [aktivieren Sie TLS 1.2 auf dem Server](./how-to-connect-install-prerequisites.md#enable-tls-12-for-azure-ad-connect). Fügen Sie den Server derselben Active Directory-Gesamtstruktur wie die Benutzer hinzu, deren Kennwörter überprüft werden müssen.
2. Installieren Sie die [aktuelle Version von Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) auf dem Server, den Sie im vorherigen Schritt ausgewählt haben. Wenn Azure AD Connect bereits ausgeführt wird, vergewissern Sie sich, dass die Version 1.1.750.0 oder höher lautet.

    >[!NOTE]
    >Die Azure AD Connect-Versionen 1.1.557.0, 1.1.558.0, 1.1.561.0 und 1.1.614.0 weisen ein Problem in Bezug auf die Kennworthashsynchronisierung auf. Wenn Sie die Kennworthashsynchronisierung _nicht_ zusammen mit der Passthrough-Authentifizierung verwenden möchten, finden Sie Informationen dazu in den [Versionshinweisen zu Azure AD Connect](./reference-connect-version-history.md).

3. Identifizieren Sie mindestens einen weiteren Server (mit Windows Server 2012 R2 oder höher, mit aktivierter TLS 1.2), auf dem eigenständige Authentifizierungs-Agents ausgeführt werden können. Diese zusätzlichen Server werden benötigt, um die Hochverfügbarkeit von Anmeldeanforderungen sicherzustellen. Fügen Sie die Server derselben Active Directory-Gesamtstruktur wie die Benutzer hinzu, deren Kennwörter überprüft werden müssen.

    >[!IMPORTANT]
    >Für Produktionsumgebungen wird empfohlen, dass Sie mindestens drei Authentifizierungs-Agents auf Ihrem Mandanten ausführen. In einem System können maximal 40 Authentifizierungs-Agents pro Mandant installiert werden. Eine bewährte Methode ist die Behandlung aller Server, auf denen Authentifizierungs-Agents ausgeführt werden, als Ebene-0-Systeme (siehe [Referenz](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)).

4. Wenn zwischen Ihren Servern und Azure AD eine Firewall eingerichtet wurde, konfigurieren Sie die folgenden Elemente:
   - Stellen Sie sicher, dass Authentifizierung-Agents *ausgehende* Anforderungen an Azure AD über die folgenden Ports senden können:

     | Portnummer | Wie diese verwendet wird |
     | --- | --- |
     | **80** | Herunterladen der Zertifikatsperrlisten (Certificate Revocation Lists, CRLs) bei der Überprüfung des TLS/SSL-Zertifikats |
     | **443** | Verarbeitung der gesamten ausgehende Kommunikation mit dem Dienst |
     | **8080** (optional) | Authentifizierungs-Agents melden ihren Status alle zehn Minuten über Port 8080, wenn Port 443 verfügbar ist. Dieser Status wird im Azure AD-Portal angezeigt. Port 8080 wird _nicht_ für Benutzeranmeldungen verwendet. |
     
     Wenn Ihre Firewall Regeln gemäß Ursprungsbenutzern erzwingt, öffnen Sie diese Ports für den Datenverkehr aus Windows-Diensten, die als Netzwerkdienst ausgeführt werden.
   - Wenn Ihre Firewall oder Ihr Proxy DNS-Whitelisting zulässt, fügen Sie Verbindungen mit **\*.msappproxy.net** und **\*.servicebus.windows.net** hinzu. Aktivieren Sie andernfalls den Zugriff auf die [IP-Adressbereiche für das Azure-Rechenzentrum](https://www.microsoft.com/download/details.aspx?id=41653), die wöchentlich aktualisiert werden.
   - Ihre Authentifizierungs-Agents benötigen für den anfänglichen Registrierungsprozess Zugriff auf **login.windows.net** und **login.microsoftonline.com**. Öffnen Sie Ihre Firewall auch für diese URLs.
    - Geben Sie zur Zertifikatüberprüfung die folgenden URLs frei: **crl3.digicert.com:80**, **crl4.digicert.com:80**, **ocsp.digicert.com:80**, **www\.d-trust.net:80**, **root-c3-ca2-2009.ocsp.d-trust.net:80**, **crl.microsoft.com:80**, **oneocsp.microsoft.com:80** und **ocsp.msocsp.com:80**. Da diese URLs für die Überprüfung des Zertifikats in Verbindung mit anderen Microsoft-Produkten verwendet werden, haben Sie diese möglicherweise bereits freigegeben.

### <a name="azure-government-cloud-prerequisite"></a>Voraussetzungen für die Azure Government-Cloud
Laden Sie vor dem Aktivieren der Pass-Through-Authentifizierung über Azure AD Connect in Schritt 2 das neueste Release des PTA-Agents im Azure-Portal herunter.  Sie müssen sicherstellen, dass die Agent-Version **1.5.1742.0** lautet oder höher.  Informationen zum Überprüfen des Agents finden Sie unter [Upgrade von Authentifizierungs-Agents](how-to-connect-pta-upgrade-preview-authentication-agents.md).

Nachdem Sie das neueste Release des Agents heruntergeladen haben, fahren Sie anhand der folgenden Anweisungen fort, um die Pass-Through-Authentifizierung über Azure AD Connect zu konfigurieren.

## <a name="step-2-enable-the-feature"></a>Schritt 2: Aktivieren des Features

Aktivieren Sie die Passthrough-Authentifizierung über [Azure AD Connect](whatis-hybrid-identity.md).

>[!IMPORTANT]
>Sie können die Passthrough-Authentifizierung auf dem primären Server oder dem Stagingserver von Azure AD Connect aktivieren. Es wird dringend empfohlen, die Aktivierung über den primären Server durchzuführen. Wenn Sie in Zukunft einen Azure AD Connect-Stagingserver einrichten, **müssen** Sie weiterhin die Pass-Through-Authentifizierung als Anmeldeoption auswählen. Wenn Sie eine andere Option auswählen, wird die Pass-Through-Authentifizierung auf dem Mandanten **deaktiviert** und die Einstellung auf dem primären Server überschrieben.

Wählen Sie den [benutzerdefinierten Installationspfad](how-to-connect-install-custom.md), wenn Sie Azure AD Connect zum ersten Mal installieren. Wählen Sie auf der Seite **Benutzeranmeldung** die Option **Passthrough-Authentifizierung** als **Anmeldemethode** aus. Nach erfolgreicher Durchführung wird auf demselben Server wie Azure AD Connect ein Passthrough-Authentifizierungs-Agent installiert. Darüber hinaus wird die Passthrough-Authentifizierungsfunktion auf Ihrem Mandanten aktiviert.

![Azure AD Connect: Benutzeranmeldung](./media/how-to-connect-pta-quick-start/sso3.png)

Wenn Sie Azure AD Connect bereits installiert haben (per [Expressinstallation](how-to-connect-install-express.md) oder [benutzerdefinierter Installation](how-to-connect-install-custom.md)), wählen Sie in Azure AD Connect die Aufgabe **Benutzeranmeldung ändern** aus, und klicken Sie auf **Weiter**. Wählen Sie anschließend **Passthrough-Authentifizierung** als Anmeldemethode aus. Nach erfolgreicher Durchführung wird auf demselben Server wie Azure AD Connect ein Passthrough-Authentifizierungs-Agent installiert, und das Feature wird auf Ihrem Mandanten aktiviert.

![Azure AD Connect: Benutzeranmeldung ändern](./media/how-to-connect-pta-quick-start/changeusersignin.png)

>[!IMPORTANT]
>Die Passthrough-Authentifizierung ist ein Feature auf Mandantenebene. Wenn Sie es aktivieren, wirkt sich dies auf die Anmeldung der Benutzer in _allen_ verwalteten Domänen Ihres Mandanten aus. Wenn Sie von Active Directory-Verbunddienste (AD FS) zur Passthrough-Authentifizierung wechseln, sollten Sie mindestens zwölf Stunden warten, bevor Sie Ihre AD FS-Infrastruktur herunterfahren. Diese Wartezeit soll sicherstellen, dass Benutzer sich während des Übergangs weiterhin bei Exchange ActiveSync anmelden können. Weitere Informationen zur Migration von AD FS zur Passthrough-Authentifizierung finden Sie [hier](https://aka.ms/adfstoptadpdownload) im ausführlichen Bereitstellungsplan.

## <a name="step-3-test-the-feature"></a>Schritt 3: Testen des Features

Befolgen Sie diese Anweisungen, um zu überprüfen, ob die Passthrough-Authentifizierung ordnungsgemäß aktiviert ist:

1. Melden Sie sich mit den Anmeldeinformationen des globalen Administrators für Ihren Mandanten beim [Azure Active Directory Admin Center](https://aad.portal.azure.com) an.
2. Wählen Sie im linken Bereich die Option **Azure Active Directory** aus.
3. Wählen Sie **Azure AD Connect** aus.
4. Überprüfen Sie, ob das Feature **Passthrough-Authentifizierung** als **aktiviert** angezeigt wird.
5. Wählen Sie **Passthrough-Authentifizierung** aus. Im Bereich **Passthrough-Authentifizierung** werden die Server aufgelistet, auf denen Ihre Authentifizierungs-Agents installiert sind.

![Azure Active Directory Admin Center: Bereich „Azure AD Connect“](./media/how-to-connect-pta-quick-start/pta7.png)

![Azure Active Directory Admin Center: Bereich „Passthrough-Authentifizierung“](./media/how-to-connect-pta-quick-start/pta8.png)

Jetzt können sich Benutzer aus allen verwalteten Domänen Ihres Mandanten mit der Passthrough-Authentifizierung anmelden. Benutzer von Verbunddomänen melden sich aber weiterhin mithilfe der AD FS oder einem anderen Verbundanbieter an, den Sie zuvor konfiguriert haben. Wenn Sie eine Verbunddomäne in eine verwaltete Domäne konvertieren, melden sich alle Benutzer dieser Domäne automatisch mit der Passthrough-Authentifizierung an. Benutzer, die auf die Cloud beschränkt sind, sind von der Passthrough-Authentifizierungsfunktion nicht betroffen.

## <a name="step-4-ensure-high-availability"></a>Schritt 4: Sicherstellen der Hochverfügbarkeit

Wenn Sie die Bereitstellung der Passthrough-Authentifizierung in einer Produktionsumgebung planen, sollten Sie weitere eigenständige Authentifizierungs-Agents installieren. Installieren Sie Authentifizierungs-Agents auf _anderen_ Servern als dem, auf dem Azure AD Connect ausgeführt wird. Mit dieser Einrichtung erzielen Sie Hochverfügbarkeit für Anforderungen zur Benutzeranmeldung.

>[!IMPORTANT]
>Für Produktionsumgebungen wird empfohlen, dass Sie mindestens drei Authentifizierungs-Agents auf Ihrem Mandanten ausführen. In einem System können maximal 40 Authentifizierungs-Agents pro Mandant installiert werden. Eine bewährte Methode ist die Behandlung aller Server, auf denen Authentifizierungs-Agents ausgeführt werden, als Ebene-0-Systeme (siehe [Referenz](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)).

Durch die Installation von mehreren Passthrough-Authentifizierungs-Agents wird zwar Hochverfügbarkeit, jedoch kein deterministischer Lastenausgleich zwischen den Authentifizierungs-Agents sichergestellt. Berücksichtigen Sie beim Ermitteln der Anzahl der für Ihren Mandanten erforderlichen Authentifizierungs-Agents die Spitzenlast und die durchschnittliche Last in Bezug auf die Anmeldeanforderungen, die Sie für Ihren Mandanten erwarten. Als Richtwert gilt, dass ein einzelner Authentifizierungs-Agent auf einem Standardserver mit einer CPU mit vier Kernen und 16 GB RAM pro Sekunde 300 bis 400 Authentifizierungen verarbeiten kann.

Um den Netzwerkverkehr abzuschätzen, verwenden Sie die folgende Anleitung zur Skalierung:
- Jede Anforderung hat eine Nutzlastgröße von (0,5 K + 1 K * Anz_Agents) Byte, d. h. Daten von Azure AD zum Authentifizierungs-Agent. Hier gibt „anz_agents“ die Anzahl der für Ihren Mandanten registrierten Authentifizierung-Agents an.
- Jede Antwort hat eine Nutzlastgröße von 1 K Byte, d. h. Daten vom Authentifizierungs-Agent zu Azure AD.

Bei den meisten Kunden reichen insgesamt drei Authentifizierungs-Agents aus, um Hochverfügbarkeit und genügend Kapazität bereitzustellen. Es wird empfohlen, die Authentifizierungs-Agents in der Nähe Ihres Domänencontrollers zu installieren, um die Anmeldungslatenz zu verbessern.

Befolgen Sie zunächst die folgenden Anweisungen zum Herunterladen der Authentifizierungs-Agent-Software:

1. Zum Herunterladen der neuesten Version des Authentifizierungs-Agents (Version 1.5.193.0 oder höher) melden Sie sich mit den Anmeldeinformationen des globalen Administrators für Ihren Mandanten beim [Azure Active Directory-Admin Center](https://aad.portal.azure.com) an.
2. Wählen Sie im linken Bereich die Option **Azure Active Directory** aus.
3. Klicken Sie auf **Azure AD Connect**, **Passthrough-Authentifizierung** und dann auf **Agent herunterladen**.
4. Klicken Sie auf die Schaltfläche **Bedingungen akzeptieren und herunterladen**.

![Azure Active Directory Admin Center: Schaltfläche „Authentifizierungs-Agent herunterladen“](./media/how-to-connect-pta-quick-start/pta9.png)

![Azure Active Directory Admin Center: Bereich für das Herunterladen des Agents](./media/how-to-connect-pta-quick-start/pta10.png)

>[!NOTE]
>Sie können die [Authentifizierungs-Agent-Software auch direkt herunterladen](https://aka.ms/getauthagent). Lesen und akzeptieren Sie die [Nutzungsbedingungen](https://aka.ms/authagenteula) für den Authentifizierungs-Agent, _bevor_ Sie ihn installieren.

Es gibt zwei Methoden zum Bereitstellen eines eigenständigen Authentifizierungs-Agents:

Die erste Methode ist die interaktive Bereitstellung, indem einfach die heruntergeladene ausführbare Authentifizierungs-Agent-Datei ausgeführt wird, und Sie bei Aufforderung die Anmeldeinformationen des globalen Administrators Ihres Mandanten angeben.

Zweitens können Sie auch ein unbeaufsichtigtes Bereitstellungsskript erstellen und ausführen. Dies ist hilfreich, wenn Sie mehrere Authentifizierungs-Agents gleichzeitig bereitstellen oder Authentifizierungs-Agents auf Windows-Servern installieren möchten, auf denen keine Benutzeroberfläche aktiviert ist bzw. auf die Sie nicht mit Remotedesktop zugreifen können. Hier finden Sie die Anweisungen für diesen Ansatz:

1. Führen Sie den folgenden Befehl aus, um einen Authentifizierungs-Agent zu installieren: `AADConnectAuthAgentSetup.exe REGISTERCONNECTOR="false" /q`.
2. Sie können den Authentifizierungs-Agent mithilfe von Windows PowerShell bei unserem Dienst registrieren. Erstellen Sie ein PowerShell-Anmeldeinformationsobjekt (`$cred`) mit einem globalen Administratorbenutzernamen und -kennwort für Ihren Mandanten. Führen Sie den folgenden Befehl aus, und ersetzen Sie dabei *\<username\>* und *\<password\>* :

  ```powershell
  $User = "<username>"
  $PlainPassword = '<password>'
  $SecurePassword = $PlainPassword | ConvertTo-SecureString -AsPlainText -Force
  $cred = New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $User, $SecurePassword
  ```
3. Wechseln Sie zu **C:\Programme\Microsoft Azure AD Connect Authentication Agent**, und führen Sie das folgende Skript unter Verwendung des zuvor erstellten Objekts `$cred` aus:

  ```powershell
  RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft Azure AD Connect Authentication Agent\Modules\" -moduleName "PassthroughAuthPSModule" -Authenticationmode Credentials -Usercredentials $cred -Feature PassthroughAuthentication
  ```

>[!IMPORTANT]
>Wenn ein Authentifizierungs-Agent auf einem virtuellen Computer installiert ist, können Sie den virtuellen Computer nicht klonen, um einen weiteren Authentifizierungs-Agent einzurichten. Diese Methode wird **nicht unterstützt**.

## <a name="step-5-configure-smart-lockout-capability"></a>Schritt 5: Konfigurieren der Smart Lockout-Funktion

Smart Lockout unterstützt Sie beim Sperren von Angreifern, die versuchen, Benutzerkennwörter zu erraten oder mithilfe von Brute-Force-Methoden in Ihr System einzudringen. Durch Konfigurieren der Smart Lockout-Einstellungen in Azure AD und/oder entsprechender Sperrungseinstellungen in einer lokalen Active Directory-Instanz können Angriffe herausgefiltert werden, bevor sie Active Directory erreichen. Lesen Sie [diesen Artikel](../authentication/howto-password-smart-lockout.md), um mehr darüber zu erfahren, wie Sie die Smart Lockout-Einstellungen für Ihren Mandanten zum Schutz Ihrer Benutzerkonten konfigurieren können.

## <a name="next-steps"></a>Nächste Schritte
- [Migrieren von AD FS zur Passthrough-Authentifizierung](https://aka.ms/adfstoptadp): Ein detaillierter Leitfaden zur Migration von AD FS (oder anderen Verbundtechnologien) zur Passthrough-Authentifizierung
- [Smart Lockout](../authentication/howto-password-smart-lockout.md): Erfahren Sie, wie Sie die Smart Lockout-Funktion für Ihren Mandanten zum Schutz von Benutzerkonten konfigurieren.
- [Aktuelle Einschränkungen](how-to-connect-pta-current-limitations.md): Hier finden Sie Informationen zu den unterstützten und nicht unterstützten Szenarien für die Passthrough-Authentifizierung.
- [Technische Einzelheiten](how-to-connect-pta-how-it-works.md): Hier finden Sie Informationen zur Funktionsweise der Passthrough-Authentifizierung.
- [Häufig gestellte Fragen](how-to-connect-pta-faq.md): Finden Sie Antworten auf häufig gestellte Fragen.
- [Problembehandlung](tshoot-connect-pass-through-authentication.md): Hier finden Sie Informationen zum Beheben von allgemeinen Problemen, die bei der Passthrough-Authentifizierung auftreten können.
- [Ausführliche Informationen zur Sicherheit](how-to-connect-pta-security-deep-dive.md): Hier erhalten Sie technische Informationen zur Passthrough-Authentifizierung.
- [Nahtloses einmaliges Anmelden mit Azure AD:](how-to-connect-sso.md) Erfahren Sie mehr über diese Ergänzungsfunktion.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): Fordern Sie neue Features über das Azure Active Directory-Forum an.
