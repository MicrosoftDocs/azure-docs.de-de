---
title: Kennwortlose Anmeldung mit Sicherheitsschlüsseln bei Windows – Azure Active Directory
description: Erfahren Sie, wie Sie die kennwortlose Anmeldung mit FIDO2-Sicherheitsschlüsseln bei Azure Active Directory (Vorschauversion) aktivieren.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/24/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: librown, aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 04a46a691b2f629b64cfe09c22813b05c593af1c
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/06/2020
ms.locfileid: "96743461"
---
# <a name="enable-passwordless-security-key-sign-in-to-windows-10-devices-with-azure-active-directory-preview"></a>Aktivieren der kennwortlosen Anmeldung mit Sicherheitsschlüsseln bei Windows 10-Geräten mit Azure Active Directory (Vorschauversion)

In diesem Dokument liegt der Schwerpunkt auf der Aktivierung der auf FIDO2-Sicherheitsschlüsseln basierenden kennwortlosen Authentifizierung bei Windows 10-Geräten. Am Ende dieses Artikels können Sie sich mit Ihrem Azure AD-Konto mithilfe eines FIDO2-Sicherheitsschlüssels sowohl bei Ihren in Azure AD als auch in Azure AD Hybrid eingebundenen Windows 10-Geräten anmelden.

> [!NOTE]
> FIDO2-Sicherheitsschlüssel sind eine öffentliche Previewfunktion von Azure Active Directory. Weitere Informationen zu Vorschauversionen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="requirements"></a>Requirements (Anforderungen)

| Gerätetyp | In Azure AD eingebunden | Hybrid in Azure AD eingebunden |
| --- | --- | --- |
| [Azure AD Multi-Factor Authentication](howto-mfa-getstarted.md) | X | X |
| [Kombinierte Registrierung von Sicherheitsinformationen (Vorschauversion)](concept-registration-mfa-sspr-combined.md) | X | X |
| Kompatible [FIDO2-Sicherheitsschlüssel](concept-authentication-passwordless.md#fido2-security-keys) | X | X |
| WebAuthN erfordert Windows 10, Version 1903 oder höher | X | X |
| [In Azure AD eingebundene Geräte](../devices/concept-azure-ad-join.md) erfordern Windows 10, Version 1909 oder höher | X |   |
| [In Azure AD Hybrid eingebundene Geräte](../devices/concept-azure-ad-join-hybrid.md) erfordern Windows 10, Version 2004 oder höher |   | X |
| Vollständig gepatchte Windows Server 2016/2019-Domänencontroller. |   | X |
| [Azure AD Connect](../hybrid/how-to-connect-install-roadmap.md#install-azure-ad-connect) Version 1.4.32.0 oder höher |   | X |
| [Microsoft Intune](/intune/fundamentals/what-is-intune) (optional) | X | X |
| Bereitstellungspaket (optional) | X | X |
| Gruppenrichtlinie (optional) |   | X |

### <a name="unsupported-scenarios"></a>Nicht unterstützte Szenarien

Folgende Szenarien werden nicht unterstützt:

- Eine Bereitstellung von Windows Server Active Directory Domain Services (AD DS) für in die Domäne eingebundene (rein lokale) Geräte.
- RDP-, VDI- und Citrix-Szenarien mit einem Sicherheitsschlüssel.
- S/MIME mit einem Sicherheitsschlüssel.
- „Ausführen als“ mit einem Sicherheitsschlüssel.
- Die Anmeldung bei einem Server mithilfe eines Sicherheitsschlüssels.
- Wenn Sie Ihren Sicherheitsschlüssel nicht verwendet haben, um sich online bei Ihrem Gerät anzumelden, können Sie ihn nicht verwenden, um sich offline anzumelden oder die Sperrung aufzuheben.
- Anmelden oder Entsperren eines Windows 10-Geräts mit einem Sicherheitsschlüssel, der mehrere Azure AD-Konten enthält In diesem Szenario wird das letzte Konto verwendet, das dem Sicherheitsschlüssel hinzugefügt wurde. WebAuthN ermöglicht Benutzern die Auswahl des gewünschten Kontos.
- Entsperren eines Geräts unter Windows 10, Version 1809. Hierfür eignet sich besonders Windows 10 (Version 1903 oder höher).

## <a name="prepare-devices-for-preview"></a>Vorbereiten von Geräten für die Vorschauversion

Auf in Azure AD eingebundenen Geräten, auf denen Sie während der Featurevorschau Pilottests ausführen, muss Windows 10, Version 1909 oder höher ausgeführt werden.

Auf in Azure AD Hybrid eingebundenen Geräte muss Windows 10, Version 2004 oder höher, ausgeführt werden.

## <a name="enable-security-keys-for-windows-sign-in"></a>Aktivieren von Sicherheitsschlüsseln für die Windows-Anmeldung

Organisationen können je nach ihren Anforderungen eine oder mehrere der folgenden Methoden zum Aktivieren der Verwendung von Sicherheitsschlüsseln für die Windows-Anmeldung verwenden:

- [Aktivieren mit Intune](#enable-with-intune)
- [Zielgerichtete Intune-Bereitstellung](#targeted-intune-deployment)
- [Aktivieren mit einem Bereitstellungspaket](#enable-with-a-provisioning-package)
- [Aktivieren mit Gruppenrichtlinie (nur in Azure AD Hybrid eingebundene Geräte)](#enable-with-group-policy)

> [!IMPORTANT]
> Organisationen mit **in Azure AD Hybrid eingebundenen Geräten** müssen **auch** die Schritte im Artikel [Aktivieren der kennwortlosen Anmeldung mit Sicherheitsschlüsseln bei lokalen Ressourcen mit Azure Active Directory (Vorschau)](howto-authentication-passwordless-security-key-on-premises.md) ausführen, bevor die Authentifizierung mit Windows 10-FIDO2-Sicherheitsschlüsseln funktioniert.
>
> Organisationen mit **in Azure AD eingebundenen Geräten** müssen dies tun, bevor sich ihre Geräte bei lokalen Ressourcen mit FIDO2-Sicherheitsschlüsseln authentifizieren können.

### <a name="enable-with-intune"></a>Aktivieren mit Intune

Führen Sie die folgenden Schritte aus, um die Verwendung von Sicherheitsschlüsseln mithilfe von Intune zu aktivieren:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Navigieren Sie zu **Microsoft Intune** > **Geräteregistrierung** > **Windows-Registrierung** > **Windows Hello for Business** > **Eigenschaften**.
1. Legen Sie unter **Einstellungen** die Option **Sicherheitsschlüssel zur Anmeldung verwenden** auf **Aktiviert** fest.

Die Konfiguration von Sicherheitsschlüsseln für die Anmeldung ist nicht von der Konfiguration von Windows Hello for Business abhängig.

### <a name="targeted-intune-deployment"></a>Zielgerichtete Intune-Bereitstellung

Verwenden Sie zum Festlegen bestimmter Gerätegruppen als Ziel die folgenden benutzerdefinierten Einstellungen über Intune, um den Anmeldeinformationsanbieter zu aktivieren:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Navigieren Sie zu **Microsoft Intune** > **Gerätekonfiguration** > **Profile** > **Profil erstellen**.
1. Konfigurieren Sie das neue Profil mit den folgenden Einstellungen:
   - Name: Sicherheitsschlüssel für die Windows-Anmeldung
   - Beschreibung: Aktiviert FIDO-Sicherheitsschlüssel für die Windows-Anmeldung
   - Plattform: Windows 10 und höher
   - Profiltyp: Benutzerdefiniert
   - Benutzerdefinierte OMA-URI-Einstellungen:
      - Name: Aktivieren der FIDO-Sicherheitsschlüssel für die Windows-Anmeldung
      - OMA-URI: ./Device/Vendor/MSFT/PassportForWork/SecurityKey/UseSecurityKeyForSignin
      - Datentyp: Integer
      - Wert: 1
1. Diese Richtlinie kann bestimmten Benutzern, Geräten oder Gruppen zugewiesen werden. Weitere Informationen finden Sie unter [Zuweisen von Benutzer- und Geräteprofilen in Microsoft Intune](/intune/device-profile-assign).

![Screenshot: Richtlinienerstellung für die benutzerdefinierte Gerätekonfiguration in Intune](./media/howto-authentication-passwordless-security-key/intune-custom-profile.png)

### <a name="enable-with-a-provisioning-package"></a>Aktivieren mit einem Bereitstellungspaket

Für nicht von Intune verwaltete Geräte können Sie ein Bereitstellungspaket installieren, um die Funktion zu aktivieren. Die Windows Configuration Designer-App kann aus dem [Microsoft Store](https://www.microsoft.com/p/windows-configuration-designer/9nblggh4tx22) heruntergeladen und installiert werden. Führen Sie die folgenden Schritte aus, um ein Bereitstellungspaket zu erstellen:

1. Starten Sie Windows Configuration Designer.
1. Klicken Sie auf **Datei** > **Neues Projekt**.
1. Geben Sie dem Projekt einen Namen, notieren Sie sich den Pfad, in dem Ihr Projekt erstellt wird, und wählen Sie **Weiter** aus.
1. Lassen Sie das *Bereitstellungspaket* als **Selected project workflow** (Ausgewählter Projektworkflow), und klicken Sie auf **Weiter**.
1. Wählen Sie unter **Einstellungen auswählen, die angezeigt und konfiguriert werden sollen** die Option *Alle Windows-Desktopeditionen* und dann **Weiter** aus.
1. Wählen Sie **Fertig stellen** aus.
1. Navigieren Sie in Ihrem neu erstellten Projekt zu **Runtimeeinstellungen** > **Windows Hello For Business** > **Sicherheitsschlüssel** > **Use Security Key For Sign In** (Sicherheitsschlüssel für die Anmeldung verwenden).
1. Legen Sie **Use Security Key For Sign In** (Sicherheitsschlüssel für die Anmeldung verwenden) auf *Aktiviert* fest.
1. Klicken Sie auf **Exportieren** > **Bereitstellungspaket**.
1. Lassen Sie die Standardwerte im Fenster **Erstellen** unter **Beschreibung des Bereitstellungspakets** unverändert, und wählen Sie **Weiter** aus.
1. Lassen Sie die Standardwerte im Fenster **Erstellen** unter **Sicherheitsdetails für das Bereitstellungspaket auswählen** unverändert, und klicken Sie auf **Weiter**.
1. Notieren Sie sich den Pfad oder ändern Sie diesen im Fenster **Erstellen** unter **Speicherort des Bereitstellungspakets auswählen**, und klicken Sie dann auf **Weiter**.
1. Klicken Sie auf der Seite **Bereitstellungspaket erstellen** auf **Erstellen**.
1. Speichern Sie die zwei erstellten Dateien (*ppkg* und *cat*) an einem Speicherort, von dem aus Sie sie später auf Computer anwenden können.
1. Um das von Ihnen erstellte Bereitstellungspaket anzuwenden, befolgen Sie die Anweisungen unter [Anwenden eines Bereitstellungspakets](/windows/configuration/provisioning-packages/provisioning-apply-package).

> [!NOTE]
> Auf Geräten unter Windows 10 (Version 1903) muss auch der Modus für freigegebene PCs (*EnableSharedPCMode*) aktiviert sein. Weitere Informationen zum Aktivieren dieser Funktion finden Sie unter [Einrichten eines freigegebenen oder Gast-PCs unter Windows 10](/windows/configuration/set-up-shared-or-guest-pc).

### <a name="enable-with-group-policy"></a>Aktivieren mit Gruppenrichtlinie

Für **in Azure AD Hybrid eingebundene Geräte** können Organisationen die folgende Gruppenrichtlinieneinstellung konfigurieren, um die Anmeldung mit dem FIDO-Sicherheitsschlüssel zu aktivieren. Die Einstellung finden Sie unter **Computerkonfiguration** > **Administrative Vorlagen** > **System** > **Anmeldung** > **Anmeldung mit Sicherheitsschlüssel aktivieren**:

- Wenn Sie diese Richtlinie auf **Aktiviert** festlegen, können Benutzer sich mit Sicherheitsschlüsseln anmelden.
- Das Festlegen dieser Richtlinie auf **Deaktiviert** oder **Nicht konfiguriert** verhindert, dass Benutzer sich mit Sicherheitsschlüsseln anmelden.

Diese Gruppenrichtlinieneinstellung erfordert eine aktualisierte Version der `CredentialProviders.admx`-Gruppenrichtlinienvorlage. Diese neue Vorlage ist mit der nächsten Version von Windows Server und mit Windows 10 20h1 verfügbar. Diese Einstellung kann mit einem Gerät verwaltet werden, auf dem eine dieser neueren Versionen von Windows ausgeführt wird, oder zentral anhand der Anleitung im Supportthema [Erstellen und Verwalten des zentralen Speichers für administrative Vorlagendateien für Gruppenrichtlinien in Windows](https://support.microsoft.com/help/3087759/how-to-create-and-manage-the-central-store-for-group-policy-administra).

## <a name="sign-in-with-fido2-security-key"></a>Anmelden mit dem FIDO2-Sicherheitsschlüssel

Im nachstehenden Beispiel hat der Benutzer Bala Sandhu den FIDO2-Sicherheitsschlüssel bereits anhand der im vorherigen Artikel [Aktivieren der kennwortlosen Anmeldung mit Sicherheitsschlüsseln](howto-authentication-passwordless-security-key.md#user-registration-and-management-of-fido2-security-keys) aufgeführten Schritte bereitgestellt. Stellen Sie für in Azure AD Hybrid eingebundene Geräte sicher, dass Sie auch die [kennwortlose Anmeldung mit Sicherheitsschlüsseln für lokale Ressourcen](howto-authentication-passwordless-security-key-on-premises.md) aktiviert haben. Bala kann auf dem Windows 10-Sperrbildschirm den Anbieter für Sicherheitsschlüsselanmeldeinformationen auswählen und den Sicherheitsschlüssel für die Anmeldung bei Windows einfügen.

![Anmelden mit Sicherheitsschlüssel auf dem Windows 10-Sperrbildschirm](./media/howto-authentication-passwordless-security-key/fido2-windows-10-1903-sign-in-lock-screen.png)

### <a name="manage-security-key-biometric-pin-or-reset-security-key"></a>Verwalten eines biometrischen Sicherheitsschlüssels bzw. einer PIN oder Zurücksetzen eines Sicherheitsschlüssels

* Windows 10, Version 1903 oder höher
   * Benutzer können die **Windows-Einstellungen** auf ihrem Gerät öffnen und zu **Konten** > **Sicherheitsschlüssel** navigieren.
   * Benutzer können ihre PIN ändern, Biometrie aktualisieren oder ihren Sicherheitsschlüssel zurücksetzen.

## <a name="troubleshooting-and-feedback"></a>Problembehandlung und Feedback

Wenn Sie Feedback geben möchten oder Probleme beim Anzeigen der Vorschau dieses Features auftreten, teilen Sie uns dies in folgenden Schritten über die Windows-Feedback-Hub-App mit:

1. Starten Sie **Feedback-Hub**, und stellen Sie sicher, dass Sie angemeldet sind.
1. Senden Sie Ihr Feedback unter der folgenden Kategorisierung:
   - Kategorie: Sicherheit und Datenschutz
   - Unterkategorie: FIDO
1. Verwenden Sie die Option **Problem reproduzieren**, um Protokolle zu erfassen.

## <a name="next-steps"></a>Nächste Schritte

[Aktivieren des Zugriffs auf lokale Ressourcen für in Azure AD und Azure AD Hybrid eingebundene Geräte](howto-authentication-passwordless-security-key-on-premises.md)

[Erfahren Sie mehr über die Geräteregistrierung](../devices/overview.md)

[Erfahren Sie mehr über Azure AD Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)