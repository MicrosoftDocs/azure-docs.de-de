---
title: Self-Service-Kennwortzurücksetzung für Windows-Geräte – Azure Active Directory
description: Hier erfahren Sie, wie Sie die Self-Service-Kennwortzurücksetzung für Azure Active Directory auf dem Windows-Anmeldebildschirm aktivieren.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/17/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 478ae6146caeb8a27cdaf13b7f33e421b8121afc
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/06/2020
ms.locfileid: "96741489"
---
# <a name="enable-azure-active-directory-self-service-password-reset-at-the-windows-sign-in-screen"></a>Aktivieren der Self-Service-Kennwortzurücksetzung für Azure Active Directory auf dem Windows-Anmeldebildschirm

Mit der Self-Service-Kennwortzurücksetzung (Self-Service Password Reset, SSPR) in Azure Active Directory (Azure AD) können Benutzer ihr Kennwort ohne Beteiligung eines Administrators oder des Helpdesks ändern oder zurücksetzen. Normalerweise öffnen Benutzer einen Webbrowser auf einem anderen Gerät, um auf das [SSPR-Portal](https://aka.ms/sspr) zuzugreifen. Sie können Benutzern das Zurücksetzen ihres Kennworts über den Windows-Anmeldebildschirm ermöglichen, um diesen Vorgang auf Computern mit Windows 7, Windows 8, Windows 8.1 oder Windows 10 zu vereinfachen.

![Beispiel für Windows 7- und Windows 10-Anmeldebildschirm mit angezeigtem SSPR-Link](./media/howto-sspr-windows/windows-reset-password.png)

> [!IMPORTANT]
> In diesem Tutorial wird erläutert, wie Administratoren die SSPR für Windows-Geräte in einem Unternehmen aktivieren.
>
> Wenn Ihr IT-Team die SSPR nicht auf Ihrem Windows-Gerät verwenden kann oder Sie Probleme mit der Anmeldung haben, sollten Sie sich an Ihren Helpdesk wenden.

## <a name="general-limitations"></a>Allgemeine Einschränkungen

Die folgenden Einschränkungen gelten für die Verwendung der SSPR auf dem Windows-Anmeldebildschirm:

- Die Kennwortzurücksetzung wird für Remotedesktopsitzungen oder erweiterte Hyper-V-Sitzungen derzeit nicht unterstützt.
- Einige Anmeldeinformationsanbieter (Drittanbieter) haben bekanntermaßen Probleme mit diesem Feature.
- Das Deaktivieren der Benutzerkontensteuerung durch die Änderung des [Registrierungsschlüssels „EnableLUA“](/openspecs/windows_protocols/ms-gpsb/958053ae-5397-4f96-977f-b7700ee461ec) verursacht bekanntermaßen Probleme.
- Dieses Feature funktioniert nicht bei Netzwerken mit 802.1x-Netzwerkauthentifizierung und der Option „Unmittelbar vor der Benutzeranmeldung ausführen“. In Netzwerken mit 802.1x-Netzwerkauthentifizierung empfiehlt es sich, die Computerauthentifizierung zu verwenden, um dieses Feature zu aktivieren.
- In Hybrid Azure AD eingebundene Computer müssen über eine direkte Netzwerkverbindung mit einem Domänencontroller verfügen, um das neue Kennwort verwenden und zwischengespeicherte Anmeldeinformationen aktualisieren zu können. Das heißt, die Geräte müssen sich im internen Unternehmensnetzwerk oder in einem VPN mit Netzwerkzugriff auf einen lokalen Domänencontroller befinden.
- Wenn Sie ein Image verwenden, stellen Sie vor dem Ausführen von Sysprep sicher, dass der Webcache für den integrierten Administrator vor der Durchführung des CopyProfile-Schritts gelöscht ist. Weitere Informationen zu diesem Schritt finden Sie im Supportartikel [Schlechte Leistung bei Verwendung eines benutzerdefinierten Standardbenutzerprofils](https://support.microsoft.com/help/4056823/performance-issue-with-custom-default-user-profile).
- Bei den folgenden Einstellungen ist bekannt, dass sie das Verwenden und Zurücksetzen von Kennwörtern auf Windows 10-Geräten beeinträchtigen:
    - Wenn eine Richtlinie in Windows 10-Versionen vor 1909 das Drücken von STRG+ALT+ENTF vorschreibt, funktioniert **Kennwort zurücksetzen** nicht.
    - Wenn Benachrichtigungen bei gesperrtem Bildschirm deaktiviert sind, funktioniert **Kennwort zurücksetzen** nicht.
    - *HideFastUserSwitching* ist auf „Aktiviert“ oder „1“ festgelegt.
    - *DontDisplayLastUserName* ist auf „Aktiviert“ oder auf „1“ festgelegt.
    - *NoLockScreen* ist auf „Aktiviert“ oder „1“ festgelegt.
    - *EnableLostMode* ist auf dem Gerät festgelegt.
    - „Explorer.exe“ wird durch eine benutzerdefinierte Shell ersetzt.
- Die Kombination der folgenden drei Einstellungen kann dazu führen, dass dieses Feature fehlschlägt.
    - Interaktive Anmeldung: Kein STRG+ALT+ENTF erforderlich = Deaktiviert
    - *DisableLockScreenAppNotifications* ist auf 1 oder „Aktiviert“ festgelegt.
    - Bei der Windows-SKU handelt es sich nicht um die Home oder Professional Edition.

## <a name="windows-10-password-reset"></a>Windows 10-Kennwortzurücksetzung

Folgende Voraussetzungen und Konfigurationsschritte sind nötig, um ein Windows 10-Gerät auf dem Anmeldebildschirm für die SSPR zu konfigurieren.

### <a name="windows-10-prerequisites"></a>Voraussetzungen für Windows 10

- Ein Administrator [muss die Self-Service-Kennwortzurücksetzung in Azure AD über das Azure-Portal](tutorial-enable-sspr.md) aktivieren.
- Benutzer müssen sich unter [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup) für die SSPR registrieren, bevor sie dieses Feature verwenden können.
    - Alle Benutzer müssen die Kontaktinformationen für die Authentifizierung angeben, bevor sie ihr Kennwort zurücksetzen können (gilt nicht ausschließlich für die SSPR auf dem Windows-Anmeldebildschirm).
- Anforderungen an Netzwerkproxy:
    - Port 443 für `passwordreset.microsoftonline.com` und `ajax.aspnetcdn.com`
    - Windows 10-Geräte unterstützen nur die Proxykonfiguration auf Computerebene.
- Es muss mindestens Windows 10 mit dem Update vom April 2018 (v1803) ausgeführt werden, und die Geräte müssen eine der folgenden Voraussetzungen erfüllen:
    - In Azure AD eingebunden
    - Hybrid in Azure AD eingebunden

### <a name="enable-for-windows-10-using-intune"></a>Aktivieren für Windows 10 mithilfe von Intune

Die flexibelste Methode besteht darin, die Konfigurationsänderung für die Aktivierung der SSPR mithilfe von Intune über den Anmeldebildschirm bereitzustellen. Mit Intune können Sie die Konfigurationsänderung für eine bestimmte Gruppe von Computern bereitstellen, die Sie definieren. Diese Methode erfordert die Registrierung des Gerätes über Intune.

#### <a name="create-a-device-configuration-policy-in-intune"></a>Erstellen einer Richtlinie für die Gerätekonfiguration in Intune

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und wählen Sie **Intune** aus.
1. Erstellen Sie ein neues Profil für die Gerätekonfiguration, indem Sie zu **Gerätekonfiguration** > **Profile** navigieren und dann auf **+ Profil erstellen** klicken.
   - Wählen Sie unter **Plattform** die Option *Windows 10 und höher* aus.
   - Wählen Sie den **Profiltyp** *Benutzerdefiniert* aus.
1. Klicken Sie auf **Erstellen**, und geben Sie dem Profil einen aussagekräftigen Namen wie *SSPR auf Windows 10-Anmeldebildschirm*.

    Geben Sie optional eine aussagekräftige Beschreibung des Profils an, und klicken Sie auf **Weiter**.
1. Klicken Sie unter *Konfigurationseinstellungen* auf **Hinzufügen**, und geben Sie folgende OMA-URI-Einstellung an, um den Link für die Kennwortzurücksetzung zu aktivieren:
      - Geben Sie einen aussagekräftigen Namen wie *SSPR-Link hinzufügen* an, um den Zweck der Einstellung zu verdeutlichen.
      - Geben Sie optional eine aussagekräftige Beschreibung der Einstellung an.
      - Festlegung von **OMA-URI** auf `./Vendor/MSFT/Policy/Config/Authentication/AllowAadPasswordReset`
      - Festlegung von **Data type** auf **Integer**
      - Festlegung von **Value** auf **1**

    Klicken Sie auf **Hinzufügen** und dann auf **Weiter**.
1. Die Richtlinie kann bestimmten Benutzern, Geräten oder Gruppen zugewiesen werden. Weisen Sie das Profil Ihrer gewünschten Umgebung zu, idealerweise zunächst einer Testgerätegruppe, und klicken Sie dann auf **Weiter**.

    Weitere Informationen finden Sie unter [Zuweisen von Benutzer- und Geräteprofilen in Microsoft Intune](/mem/intune/configuration/device-profile-assign).

1. Konfigurieren Sie die Anwendbarkeitsregeln wie für Ihre Umgebung gewünscht, zum Beispiel *Assign profile if OS edition is Windows 10 Enterprise* (Profil nur zuweisen, wenn das Betriebssystem Windows 10 Enterprise ist), und klicken Sie dann auf **Weiter**.
1. Überprüfen Sie Ihr Profil, und klicken Sie anschließend auf **Erstellen**.

### <a name="enable-for-windows-10-using-the-registry"></a>Aktivieren für Windows 10 mithilfe der Registrierung

Befolgen Sie diese Schritte, um die SSPR auf dem Anmeldebildschirm mithilfe eines Registrierungsschlüssels zu aktivieren:

1. Melden Sie sich mit Administratoranmeldeinformationen beim Windows-PC an.
1. Drücken Sie **Windows-Taste** + **R**, um das Dialogfeld *Ausführen* zu öffnen, und führen Sie dann **regedit** als Administrator aus.
1. Legen Sie den folgenden Registrierungsschlüssel fest:

    ```cmd
    HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\AzureADAccount
       "AllowPasswordReset"=dword:00000001
    ```

#### <a name="troubleshooting-windows-10-password-reset"></a>Behandeln von Problemen bei der Windows 10-Kennwortzurücksetzung

Wenn bei der Verwendung der SSPR über den Windows-Anmeldebildschirm Probleme auftreten, finden Sie im Azure AD-Überwachungsprotokoll Informationen zur IP-Adresse und zum *Clienttyp*, auf der bzw. dem die Kennwortzurücksetzung versucht wurde. Das wird in der folgenden Beispielausgabe veranschaulicht:

![Beispiel: Kennwortzurücksetzung unter Windows 7 im Azure AD-Überwachungsprotokoll](media/howto-sspr-windows/windows-7-sspr-azure-ad-audit-log.png)

Wenn Benutzer ihr Kennwort über den Anmeldebildschirm eines Windows 10-Geräts zurücksetzen, wird das temporäre Konto `defaultuser1` mit niedrigen Berechtigungen erstellt. Mit diesem Konto wird der Vorgang zur Kennwortzurücksetzung geschützt.

Für das Konto selbst wird ein zufällig generiertes Kennwort festgelegt. Das Konto wird außerdem nicht bei der Geräteanmeldung angezeigt und wird nach dem Zurücksetzen des Kennworts durch den Benutzer automatisch entfernt. Unter Umständen sind mehrere Profile vom Typ `defaultuser` vorhanden, sie können jedoch ignoriert werden.

## <a name="windows-7-8-and-81-password-reset"></a>Kennwortzurücksetzung für Windows 7, 8 und 8.1

Folgende Voraussetzungen und Konfigurationsschritte sind nötig, um ein Windows 7-, Windows 8- oder Windows 8.1-Gerät auf dem Anmeldebildschirm für die SSPR zu konfigurieren.

### <a name="windows-7-8-and-81-prerequisites"></a>Voraussetzungen für Windows 7, 8 und 8.1

- Ein Administrator [muss die Self-Service-Kennwortzurücksetzung in Azure AD über das Azure-Portal](tutorial-enable-sspr.md) aktivieren.
- Benutzer müssen sich unter [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup) für die SSPR registrieren, bevor sie dieses Feature verwenden können.
    - Alle Benutzer müssen die Kontaktinformationen für die Authentifizierung angeben, bevor sie ihr Kennwort zurücksetzen können (gilt nicht ausschließlich für die SSPR auf dem Windows-Anmeldebildschirm).
- Anforderungen an Netzwerkproxy:
    - Port 443 für `passwordreset.microsoftonline.com`
- Gepatchtes Windows 7- oder Windows 8.1-Betriebssystem.
- TLS 1.2 wurde gemäß der Anleitung unter [Registrierungseinstellungen für Transport Layer Security (TLS)](/windows-server/security/tls/tls-registry-settings#tls-12) aktiviert.
- Wenn auf Ihrem Computer mehrere Drittanbieter von Anmeldeinformationen aktiviert sind, werden Benutzern mehrere Benutzerprofile auf dem Anmeldebildschirm angezeigt.

> [!WARNING]
> TLS 1.2 muss aktiviert sein, nicht nur auf „Automatische Aushandlung“ festgelegt.

### <a name="install"></a>Installieren

Bei Windows 7, Windows 8 und Windows 8.1 muss eine kleine Komponente auf dem Computer installiert werden, um die SSPR auf dem Anmeldebildschirm zu aktivieren. Befolgen Sie diese Schritte, um die SSPR-Komponente zu installieren:

1. Laden Sie das Installationsprogramm für die Windows-Version herunter, die Sie aktivieren möchten.

    Der Softwareinstaller ist im Microsoft Download Center unter [https://aka.ms/sspraddin](https://aka.ms/sspraddin) verfügbar.
1. Melden Sie sich auf dem Computer an, auf dem Sie installieren möchten, und führen Sie das Installationsprogramm aus.
1. Ein Neustart nach der Installation wird dringend empfohlen.
1. Wählen Sie nach dem Neustart auf dem Anmeldebildschirm einen Benutzer aus, und klicken Sie auf „Kennwort vergessen?“, um den Workflow zur Kennwortzurücksetzung zu initiieren.
1. Schließen Sie den Workflow mit den folgenden Schritten auf dem Bildschirm ab, um Ihr Kennwort zurückzusetzen.

![Beispiel: Windows 7 mit angeklickter Option „Kennwort vergessen?“ SSPR-Flow](media/howto-sspr-windows/windows-7-sspr.png)

#### <a name="silent-installation"></a>Automatische Installation

Die SSPR-Komponente kann ohne Eingabeaufforderungen installiert oder deinstalliert werden, indem die folgenden Befehle verwendet werden:

- Verwenden Sie für die automatische Installation den Befehl „msiexec /i SsprWindowsLogon.PROD.msi /qn“.
- Verwenden Sie für die automatische Deinstallation den Befehl „msiexec /x SsprWindowsLogon.PROD.msi /qn“.

#### <a name="troubleshooting-windows-7-8-and-81-password-reset"></a>Behandeln von Problemen bei der Kennwortzurücksetzung für Windows 7, 8 und 8.1

Wenn bei der Verwendung der SSPR über den Windows-Anmeldebildschirm Probleme auftreten, werden die Ereignisse auf dem Computer und in Azure AD protokolliert. Azure AD-Ereignisse enthalten Informationen zur IP-Adresse und zum Clienttyp, für die bzw. den das Kennwort zurückgesetzt wurde. Das wird in der folgenden Beispielausgabe veranschaulicht:

![Beispiel: Kennwortzurücksetzung unter Windows 7 im Azure AD-Überwachungsprotokoll](media/howto-sspr-windows/windows-7-sspr-azure-ad-audit-log.png)

Wenn zusätzliche Protokollierung erforderlich ist, kann ein Registrierungsschlüssel auf dem Computer geändert werden, um die ausführliche Protokollierung zu aktivieren. Aktivieren Sie die ausführliche Protokollierung nur für die Problembehandlung, indem Sie den folgenden Registrierungsschlüsselwert verwenden:

```cmd
HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Authentication\Credential Providers\{86D2F0AC-2171-46CF-9998-4E33B3D7FD4F}
```

- Zum Aktivieren der ausführlichen Protokollierung erstellen Sie den Eintrag `REG_DWORD: "EnableLogging"`, und legen Sie ihn auf 1 fest.
- Um die ausführliche Protokollierung zu deaktivieren, ändern Sie `REG_DWORD: "EnableLogging"` in 0.

## <a name="what-do-users-see"></a>Anzeige für Benutzer

Was ändert sich für Benutzer, nachdem die SSPR für Ihre Windows-Geräte konfiguriert wurde? Wie erfahren sie, dass sie ihr Kennwort über den Anmeldebildschirm zurücksetzen können? Auf den folgenden Beispielscreenshots werden die Optionen veranschaulicht, die Benutzern zusätzlich für die SSPR zur Verfügung stehen:

![Beispiel für Windows 7- und Windows 10-Anmeldebildschirm mit angezeigtem SSPR-Link](./media/howto-sspr-windows/windows-reset-password.png)

Wenn Benutzer versuchen, sich anzumelden, wird der Link **Kennwort zurücksetzen** oder **Kennwort vergessen** angezeigt. Mit diesen Links wird die Self-Service-Kennwortzurücksetzung auf dem Anmeldebildschirm geöffnet. Mit dieser Funktion können Benutzer ihr Kennwort zurücksetzen, ohne ein anderes Gerät für den Zugriff auf einen Webbrowser verwenden zu müssen.

Benutzer, die dieses Feature verwenden, finden weitere Informationen unter [Zurücksetzen des Kennworts für Ihr Geschäfts-, Schul- oder Unikonto mithilfe von Sicherheitsinformationen](../user-help/active-directory-passwords-update-your-own-password.md).

## <a name="next-steps"></a>Nächste Schritte

Sie können die Benutzerregistrierung vereinfachen, indem Sie die [SSPR-Kontaktinformationen für die Benutzerauthentifizierung vorab auffüllen](howto-sspr-authenticationdata.md).