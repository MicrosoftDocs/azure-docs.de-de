---
title: Planen und Ausführen einer Azure Multi-Factor Authentication-Bereitstellung – Azure Active Directory
description: Microsoft Azure Multi-Factor Authentication – Bereitstellungsplanung
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1ca69fc23d580b61e74fe56b3d0c3524fdfad747
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66235530"
---
# <a name="planning-a-cloud-based-azure-multi-factor-authentication-deployment"></a>Planen einer cloudbasierten Azure Multi-Factor Authentication-Bereitstellung

Benutzer stellen in zunehmend komplizierteren Szenarien eine Verbindung mit Unternehmensressourcen her. Benutzer stellen ein- und ausgehende Unternehmensnetzwerk-Verbindungen von im Besitz der Organisation befindlichen, privaten und öffentlichen Smartphones, Tablets, PCs und Laptops aus her, oft auf mehreren Plattformen. In dieser stets verbundenen Welt mit mehreren Geräten und Plattformen ist die Sicherheit von Benutzerkonten wichtiger als je zuvor. Geräte, Netzwerke und Plattformen übergreifend verwendete Kennwörter sind unabhängig von ihrer Komplexität nicht mehr ausreichend, um die Sicherheit des Benutzerkontos sicherzustellen, insbesondere dann, wenn Benutzer dazu neigen, Kennwörter für mehrere Konten wiederzuverwenden. Anspruchsvolles Phishing und andere Social Engineering-Angriffe können dazu führen, dass Benutzernamen und Kennwörter im Darknet angeboten und verkauft werden.

[Azure Multi-Factor Authentication (MFA)](concept-mfa-howitworks.md) trägt zur Sicherung des Zugriffs auf Daten und Anwendungen bei. Sie bietet eine zusätzliche Sicherheitsebene mithilfe einer zweiten Form der Authentifizierung. Organisationen können die Lösung mit dem [bedingten Zugriff](../conditional-access/overview.md) ihren speziellen Bedürfnissen anpassen.

## <a name="prerequisites"></a>Voraussetzungen

Vor dem Starten einer Bereitstellung von Azure Multi-Factor Authentication sind Voraussetzungen zu berücksichtigen.

| Szenario | Voraussetzung |
| --- | --- |
| **Nur Cloud**-Identitätsumgebung mit moderner Authentifizierung | **Keine weiteren erforderlichen Aufgaben** |
| **Hybrididentitätsszenarien** | [Azure AD Connect](../hybrid/whatis-hybrid-identity.md) wird bereitgestellt, und Benutzeridentitäten werden mit den lokalen Active Directory Domain Services mit Azure Active Directory synchronisiert oder verbunden. |
| Für den Cloudzugriff veröffentlichte lokale ältere Anwendungen | Azure AD-[Anwendungsproxy](../manage-apps/application-proxy.md) wird bereitgestellt. |
| Verwenden von Azure MFA mit RADIUS-Authentifizierung | Ein [Netzwerkrichtlinienserver (Network Policy Server, NPS)](howto-mfa-nps-extension.md) wird bereitgestellt. |
| Benutzer haben Microsoft Office 2010 oder niedriger bzw. Apple Mail für iOS 11 oder niedriger | Aktualisieren Sie auf [Microsoft Office 2013 oder höher](https://support.microsoft.com/help/4041439/modern-authentication-configuration-requirements-for-transition-from-o) und Apple Mail für iOS 12 oder höher. Von den älteren Authentifizierungsprotokollen wird der bedingte Zugriff nicht unterstützt. |

## <a name="plan-user-rollout"></a>Planen des Rollouts für Benutzer

Ihr MFA-Rolloutplan sollte eine Pilotbereitstellung enthalten gefolgt von Bereitstellungsphasen, die innerhalb Ihrer Supportkapazität liegen. Beginnen Sie den Rollout durch Anwenden von Richtlinien für den bedingten Zugriff auf eine kleine Gruppe von Pilotbenutzern. Nach dem Evaluieren der Auswirkungen auf die Pilotbenutzer, den verwendeten Prozess und das Registrierungsverhalten können Sie entweder der Richtlinie weitere Gruppen hinzufügen oder vorhandenen Gruppen weitere Benutzer hinzufügen.

### <a name="user-communications"></a>Benutzerkommunikation

Es ist wichtig, Benutzer in geplanter Kommunikation über zukünftige Änderungen, Azure MFA-Registrierungsanforderungen und alle erforderlichen Benutzeraktionen zu informieren. Die Kommunikation sollte zusammen mit Vertretern aus Ihrer Organisation entwickelt werden, z.B. aus den Abteilungen Kommunikation, Change Management und Personalwesen.

Microsoft bietet [Kommunikationsvorlagen](https://aka.ms/mfatemplates) und [Endbenutzerdokumentation](../user-help/security-info-setup-signin.md), womit Sie Ihre Kommunikation entwerfen können. Sie können Benutzer auf [https://myprofile.microsoft.com](https://myprofile.microsoft.com) verweisen, wo sie sich durch Auswahl der **Sicherheitsinformation**-Links auf dieser Seite direkt registrieren können.

## <a name="deployment-considerations"></a>Überlegungen zur Bereitstellung

Azure Multi-Factor Authentication wird durch Erzwingen von Richtlinien mit bedingtem Zugriff bereitgestellt. Eine [Richtlinie für bedingten Zugriff](../conditional-access/overview.md) kann erfordern, dass Benutzer die Multi-Factor Authentication durchführen, wenn bestimmte Kriterien erfüllt sind, z.B.:

* Alle Benutzer, bestimmter Benutzer, Mitglied einer Gruppe oder zugewiesene Rolle
* Bestimmte Cloudanwendung, auf die zugegriffen wird
* Geräteplattform
* Status des Geräts
* Netzwerkadresse oder auf einen geografischen Standort bezogene IP-Adresse
* Clientanwendungen
* Anmelderisiko (erfordert Identity Protection)
* Kompatibles Gerät
* Hybrid in Azure AD eingebundenes Gerät
* Genehmigte Clientanwendung

Verwenden Sie die anpassbaren Poster und E-Mail-Vorlagen im [Multi-Factor Authentication-Rollout-Material](https://www.microsoft.com/download/details.aspx?id=57600&WT.mc_id=rss_alldownloads_all), um die mehrstufige Authentifizierung in Ihrer Organisation einzuführen.

## <a name="enable-multi-factor-authentication-with-conditional-access"></a>Aktivieren von Multi-Factor Authentication mit bedingtem Zugriff

Richtlinien für bedingten Zugriff erzwingen die Registrierung, sodass nicht registrierte Benutzer die Registrierung beim ersten Anmelden durchführen müssen – ein wichtiger Sicherheitsaspekt.

[Azure AD Identity Protection](../identity-protection/howto-configure-risk-policies.md) trägt sowohl eine Registrierungsrichtlinie als auch automatische Risikoerkennungs- und Wartungsrichtlinien zu Azure Multi-Factor Authentication bei. Richtlinien können erstellt werden, um Kennwortänderungen zu erzwingen, wenn eine Bedrohung der kompromittierten Identität besteht, oder MFA erfordern, wenn eine Anmeldung aufgrund folgender [Ereignisse](../reports-monitoring/concept-risk-events.md) als riskant eingestuft wird:

* Kompromittierte Anmeldeinformationen
* Anmeldungen von anonymen IP-Adressen
* Unmöglicher Ortswechsel zu atypischen Orten
* Anmeldungen von unbekannten Standorten
* Anmeldungen von infizierten Geräten
* Anmeldungen von IP-Adressen mit verdächtigen Aktivitäten

Einige der von Azure Active Directory Identity Protection erkannten Risikoereignisse geschehen in Echtzeit, und einige erfordern Offlineverarbeitung. Administratoren können wahlweise Benutzer blockieren, die riskante Verhaltensweisen zeigen, und den Vorgang manuell korrigieren, eine Kennwortänderung anfordern oder eine mehrstufige Authentifizierung im Rahmen ihrer Richtlinien für bedingten Zugriff anfordern.

## <a name="define-network-locations"></a>Definieren von Netzwerkadressen

Organisationen sollten den bedingten Zugriff verwenden, um ihr Netzwerk mithilfe von [benannten Orten](../conditional-access/location-condition.md#named-locations) zu definieren. Wenn Ihre Organisation Identity Protection verwendet, erwägen Sie die Nutzung risikobasierter Richtlinien statt benannter Orte.

### <a name="configuring-a-named-location"></a>Konfigurieren eines benannten Orts

1. Öffnen Sie **Azure Active Directory** im Azure-Portal.
2. Klicken Sie auf **Bedingter Zugriff**.
3. Klicken Sie auf **Benannte Standorte**.
4. Klicken Sie auf **Neuer Standort**.
5. Geben Sie im Feld **Name** einen aussagekräftigen Namen an.
6. Wählen Sie aus, ob Sie den Standort mithilfe von IP-Bereichen oder Ländern/Regionen definieren.
   1. Bei Verwendung der IP-Bereiche
      1. Entscheiden Sie, ob Sie den Standort als vertrauenswürdig markieren. Die Anmeldung von einem vertrauenswürdigen Ort aus senkt das Anmelderisiko von Benutzern. Markieren Sie diesen Standort nur dann als vertrauenswürdig, wenn die eingegebenen IP-Bereiche bekannt sind und in Ihrer Organisation als unbedenklich eingestuft werden.
      2. Angeben der IP-Bereiche
   2. Bei Verwendung von Ländern/Regionen
      1. Erweitern Sie das Dropdownmenü, und wählen Sie die Länder oder Regionen aus, die Sie für diesen benannten Ort definieren möchten.
      2. Entscheiden Sie, ob Sie unbekannte Bereiche einschließen möchten. Unbekannte Bereiche sind IP-Adressen, die keinem Land/keiner Region zugeordnet werden können.
7. Klicken Sie auf **Erstellen**

## <a name="plan-authentication-methods"></a>Planen von Authentifizierungsmethoden

Administratoren können die [Authentifizierungsmethoden](../authentication/concept-authentication-methods.md) auswählen, die sie für Benutzer verfügbar machen möchten. Es ist wichtig, mehrere Authentifizierungsmethoden zuzulassen, damit Benutzern eine Sicherungsmethode zur Verfügung steht für den Fall, dass ihre primäre Methode nicht verfügbar ist. Administratoren können die folgenden Methoden zulassen:

### <a name="notification-through-mobile-app"></a>Benachrichtigung über mobile App

Eine Pushbenachrichtigung wird an die Microsoft Authenticator-App auf Ihrem Mobilgerät gesendet. Der Benutzer zeigt die Benachrichtigung an und wählt **Genehmigen** aus, um die Überprüfung abzuschließen. Pushbenachrichtigungen über eine mobile App bieten Benutzern die am wenigsten intrusive Option. Sie sind auch die zuverlässigste und sicherste Option, da sie eine Daten- anstelle einer Telefonverbindung verwenden.

> [!NOTE]
> Wenn es in Ihrer Organisation Mitarbeiter gibt, die in China arbeiten oder nach China reisen, müssen Sie beachten, dass die Methode **Benachrichtigung über mobile App** auf **Android-Geräten** in diesem Land nicht funktioniert. Daher sollten diesen Benutzern alternative Methoden zur Verfügung gestellt werden.

### <a name="verification-code-from-mobile-app"></a>Überprüfungscode von der mobilen App

Eine mobile App wie die Microsoft Authenticator-App generiert alle 30 Sekunden einen neuen OATH-Überprüfungscode. Der Benutzer gibt den Überprüfungscode auf der Anmeldeoberfläche ein. Die Option „mobile App“ kann unabhängig davon verwendet werden, ob das Telefon ein Daten- oder Mobilfunksignal sendet.

### <a name="call-to-phone"></a>Auf Telefon anrufen

Der Benutzer erhält einen automatisierten Anruf. Der Benutzer nimmt den Anruf an und drückt die **#** -Taste auf der Telefontastatur, um seine Authentifizierung zu bestätigen. Der Telefonanruf ist eine hervorragende Sicherungsmethode für die Benachrichtigung bzw. den Überprüfungscode von einer mobilen App aus.

### <a name="text-message-to-phone"></a>Textnachricht an Telefon

Eine SMS mit dem Überprüfungscode wird an den Benutzer gesendet, und der Benutzer wird aufgefordert, den Überprüfungscode auf der Anmeldeseite einzugeben.

### <a name="choose-verification-options"></a>Auswählen der Überprüfungsoptionen

1. Wechseln Sie zu **Azure Active Directory**, **Benutzer**, **Multi-Factor Authentication**.

   ![Zugreifen auf das Multi-Factor Authentication-Portal über das Blatt „Azure AD-Benutzer“ im Azure-Portal](media/howto-mfa-getstarted/users-mfa.png)

1. Navigieren Sie auf der neuen Registerkarte, die geöffnet wird, zu **Diensteinstellungen**.
1. Aktivieren Sie unter **Überprüfungsoptionen** alle Kontrollkästchen für Methoden, die den Benutzern zur Verfügung stehen sollen.

   ![Konfigurieren von Überprüfungsmethoden, in der Registerkarte „Diensteinstellung“ von Multi-Factor Authentication](media/howto-mfa-getstarted/mfa-servicesettings-verificationoptions.png)

1. Klicken Sie auf **Speichern**.
1. Schließen Sie die Registerkarte **Diensteinstellungen**.

## <a name="plan-registration-policy"></a>Planen der Registrierungsrichtlinie

Administratoren müssen bestimmen, wie Benutzer ihre Methoden registrieren. Organisationen sollten [die neue kombinierte Registrierungsbenutzeroberfläche](howto-registration-mfa-sspr-combined.md) für Azure MFA und Self-Service-Kennwortzurücksetzung (Self-Service Password Reset, SSPR) aktivieren. SSPR ermöglicht Benutzern das sichere Zurücksetzen des Kennworts mithilfe derselben Methoden, die sie für Multi-Factor Authentication verwenden. Diese derzeit in Public Preview befindliche kombinierte Registrierung wird empfohlen, da sie eine hervorragende Benutzerfreundlichkeit bietet, mit der Möglichkeit, sich einmal für beide Dienste zu registrieren. Das Aktivieren der gleichen Methoden für SSPR und Azure MFA ermöglicht Benutzern, zur Verwendung beider Funktionen registriert zu werden.

### <a name="registration-with-identity-protection"></a>Registrierung mit Identity Protection

Wenn Ihre Organisation Azure Active Directory Identity Protection verwendet, [konfigurieren Sie die MFA-Registrierungsrichtlinie](../identity-protection/howto-mfa-policy.md), um Ihre Benutzer aufzufordern, sich bei ihrer nächsten Anmeldung interaktiv zu registrieren.

### <a name="registration-without-identity-protection"></a>Registrierung ohne Identity Protection

Wenn Ihre Organisation nicht über Lizenzen verfügt, die Identity Protection aktivieren, werden Benutzer aufgefordert, sich das nächste Mal zu registrieren, wenn MFA bei der Anmeldung erforderlich ist. Benutzer sind möglicherweise nicht für MFA registriert, wenn sie keine mit MFA geschützten Anwendungen verwenden. Es ist wichtig, dass alle Benutzer sich registrieren, sodass Angreifer nicht das Kennwort eines Benutzers erraten und sich in seinem Namen bei MFA registrieren und so effektiv die Kontrolle über das Konto erhalten können.

#### <a name="enforcing-registration"></a>Erzwingen der Registrierung

Mithilfe der folgenden Schritte kann eine Richtlinie für bedingten Zugriff Benutzer zwingen, sich für die Multi-Factor Authentication zu registrieren.

1. Erstellen Sie eine Gruppe, und fügen Sie alle zurzeit nicht registrierten Benutzer hinzu.
2. Erzwingen Sie mit dem bedingten Zugriff die Multi-Factor Authentication für diese Gruppe für den Zugriff auf alle Ressourcen.
3. Werten Sie in regelmäßigen Abständen erneut die Gruppenmitgliedschaft aus, und entfernen Sie Benutzer, die sich registriert haben, aus der Gruppe.

Sie können registrierte und nicht registrierte Azure MFA-Benutzer mit PowerShell-Befehlen identifizieren, die auf dem [MSOnline-PowerShell-Modul](https://docs.microsoft.com/powershell/azure/active-directory/install-msonlinev1?view=azureadps-1.0) basieren.

#### <a name="identify-registered-users"></a>Identifizieren registrierter Benutzer

```PowerShell
Get-MsolUser -All | where {$_.StrongAuthenticationMethods -ne $null} | Select-Object -Property UserPrincipalName | Sort-Object userprincipalname 
```

#### <a name="identify-non-registered-users"></a>Identifizieren nicht registrierter Benutzer

```PowerShell
Get-MsolUser -All | where {$_.StrongAuthenticationMethods.Count -eq 0} | Select-Object -Property UserPrincipalName | Sort-Object userprincipalname 
```

### <a name="convert-users-from-per-user-mfa-to-conditional-access-based-mfa"></a>Konvertieren von Benutzern von „MFA pro Benutzer“ zu „MFA mit bedingtem Zugriff“

Wenn für Ihre Benutzer die Multi-Factor Authentication pro Benutzer aktiviert und erzwungen wurde, können Sie mithilfe des folgenden PowerShell-Codes eine Konvertierung in die Multi-Factor Authentication mit bedingtem Zugriff vornehmen.

```PowerShell
# Disable MFA for all users, keeping their MFA methods intact
Get-MsolUser -All | Disable-MFA -KeepMethods

# Enforce MFA for all users
Get-MsolUser -All | Set-MfaState -State Enforced

# Wrapper to disable MFA with the option to keep the MFA
# methods (to avoid having to proof-up again later)
function Disable-Mfa {

    [CmdletBinding()]
    param(
        [Parameter(ValueFromPipeline=$True)]
        $User,
        [switch] $KeepMethods
    )

    Process {

        Write-Verbose ("Disabling MFA for user '{0}'" -f $User.UserPrincipalName)
        $User | Set-MfaState -State Disabled

        if ($KeepMethods) {
            # Restore the MFA methods which got cleared when disabling MFA
            Set-MsolUser -ObjectId $User.ObjectId `
                         -StrongAuthenticationMethods $User.StrongAuthenticationMethods
        }
    }
}

# Sets the MFA requirement state
function Set-MfaState {

    [CmdletBinding()]
    param(
        [Parameter(ValueFromPipelineByPropertyName=$True)]
        $ObjectId,
        [Parameter(ValueFromPipelineByPropertyName=$True)]
        $UserPrincipalName,
        [ValidateSet("Disabled","Enabled","Enforced")]
        $State
    )

    Process {
        Write-Verbose ("Setting MFA state for user '{0}' to '{1}'." -f $ObjectId, $State)
        $Requirements = @()
        if ($State -ne "Disabled") {
            $Requirement =
                [Microsoft.Online.Administration.StrongAuthenticationRequirement]::new()
            $Requirement.RelyingParty = "*"
            $Requirement.State = $State
            $Requirements += $Requirement
        }

        Set-MsolUser -ObjectId $ObjectId -UserPrincipalName $UserPrincipalName `
                     -StrongAuthenticationRequirements $Requirements
    }
}

```

## <a name="plan-conditional-access-policies"></a>Planen von Richtlinien für bedingten Zugriff

Informationen zum Planen der Richtlinienstrategie für den bedingten Zugriff, die bestimmt, wann MFA und andere Steuerelemente erforderlich sind, finden Sie unter [Was ist der bedingte Zugriff in Azure Active Directory?](../conditional-access/overview.md).

Es ist wichtig, dass Sie verhindern, versehentlich aus Ihrem Azure AD-Mandanten ausgesperrt zu werden. Sie können die Auswirkungen eines versehentlichen Verlusts des Administratorzugriffs abmildern, indem Sie [mindestens zwei Konten für den Notfallzugriff in Ihrem Mandanten erstellen](../users-groups-roles/directory-emergency-access.md) und sie von Ihrer Richtlinie für bedingten Zugriff ausnehmen.

### <a name="create-conditional-access-policy"></a>Erstellen der Richtlinie für bedingten Zugriff

1. Melden Sie sich mit dem globalen Administratorkonto am [Azure-Portal](https://portal.azure.com) an.
1. Navigieren Sie zu **Azure Active Directory**  > **Bedingter Zugriff**.
1. Wählen Sie **Neue Richtlinie**.
1. Geben Sie einen aussagekräftigen Namen für Ihre Richtlinie an.
1. Unter **Benutzer und Gruppen**:
   * Wählen Sie auf der Registerkarte **Einschließen** das Optionsfeld **Alle Benutzer**.
   * Aktivieren Sie auf der Registerkarte **Ausschließen** das Kontrollkästchen für **Benutzer und Gruppen**, und wählen Sie Ihre Konten für den Notfallzugriff aus.
   * Klicken Sie auf **Fertig**.
1. Aktivieren Sie unter **Cloud-Apps** das Optionsfeld **Alle Cloud-Apps**.
   * OPTIONAL: Wählen Sie auf der Registerkarte **Ausschließen** die Cloud-Apps aus, für die Ihre Organisation keine MFA benötigt.
   * Klicken Sie auf **Fertig**.
1. Im Abschnitt **Bedingungen**:
   * OPTIONAL: Wenn Sie Azure Identity Protection aktiviert haben, können Sie entscheiden, ob das Anmelderisiko im Rahmen der Richtlinie ausgewertet werden soll.
   * OPTIONAL: Wenn Sie vertrauenswürdige Speicherorte oder benannte Orte konfiguriert haben, können Sie angeben, ob diese in die Richtlinie ein- oder davon ausgeschlossen werden.
1. Stellen Sie unter **Gewähren** sicher, dass das Optionsfeld **Zugriff gewähren** aktiviert ist.
    * Aktivieren Sie das Kontrollkästchen **Multi-Factor Authentication erforderlich**.
    * Klicken Sie auf **Auswählen**.
1. Überspringen Sie den Abschnitt **Sitzung**.
1. Legen Sie die Umschaltfläche **Richtlinie aktivieren** auf **Ein** fest.
1. Klicken Sie auf **Erstellen**.

![Erstellen einer Richtlinie für bedingten Zugriff zum Aktivieren von MFA für die Benutzer des Azure-Portals in der Pilotgruppe](media/howto-mfa-getstarted/conditionalaccess-newpolicy.png)

## <a name="plan-integration-with-on-premises-systems"></a>Planen der Integration mit lokalen Systemen

Einige ältere und lokale Anwendungen, die sich nicht direkt bei Azure AD authentifizieren, müssen zusätzliche Schritte zur Verwendung von MFA ausführen, unter anderem:

* Ältere lokale Anwendungen, die den Anwendungsproxy verwenden müssen.
* Lokale RADIUS-Anwendungen, die den MFA-Adapter mit NPS-Server verwenden müssen.
* Lokale AD FS-Anwendungen, die den MFA-Adapter mit AD FS 2016 verwenden müssen.

Anwendungen, die sich direkt bei Azure AD authentifizieren und die moderne Authentifizierung nutzen (WS-Fed, SAML, OAuth, OpenID Connect) können die Richtlinien für bedingten Zugriff direkt verwenden.

### <a name="use-azure-mfa-with-azure-ad-application-proxy"></a>Verwenden von Azure MFA mit dem Azure AD-Anwendungsproxy

Lokale Anwendungen können bei Ihrem Azure AD-Mandanten über den [Azure AD-Anwendungsproxy](../manage-apps/application-proxy.md) veröffentlicht werden und die Azure Multi-Factor Authentication nutzen, wenn sie für die Verwendung der Azure AD-Vorauthentifizierung konfiguriert sind.

Diese Anwendungen unterliegen genau wie jede andere in Azure AD integrierte Anwendung den Richtlinien für bedingten Zugriff, welche die Azure Multi-Factor Authentication erzwingen.

Ebenso werden, wenn Azure Multi-Factor Authentication für alle Benutzeranmeldungen erzwungen wird, mit dem Azure AD-Anwendungsproxy veröffentlichte lokale Anwendungen geschützt.

### <a name="integrating-azure-multi-factor-authentication-with-network-policy-server"></a>Integration von Azure Multi-Factor Authentication in den Netzwerkrichtlinienserver

Die NPS-Erweiterung (Network Policy Server, Netzwerkrichtlinienserver) für Azure MFA fügt Ihrer Authentifizierungsinfrastruktur unter Verwendung Ihrer vorhandenen Server cloudbasierte MFA-Funktionen hinzu. Mit der NPS-Erweiterung können Sie zu Ihrem bestehenden Authentifizierungsablauf eine Überprüfung per Telefonanruf, SMS oder Telefon-App hinzufügen. Für diese Integration gelten die folgenden Einschränkungen:

* Mit dem CHAPv2-Protokoll werden nur Authenticator-App-Pushbenachrichtigungen und Sprachanrufe unterstützt.
* Richtlinien für bedingten Zugriff können nicht angewendet werden.

Die NPS-Erweiterung fungiert als Adapter zwischen RADIUS und cloudbasierter Azure MFA, um eine zweite Stufe der Authentifizierung zum Schutz von [VPN](howto-mfa-nps-extension-vpn.md), [Remotedesktopgateway-Verbindungen](howto-mfa-nps-extension-rdg.md) oder anderen RADIUS-fähigen Anwendungen zu bieten. Benutzer, die sich in dieser Umgebung für Azure MFA registrieren, werden bei allen Authentifizierungsversuchen abgefragt; das Fehlen von Richtlinien für bedingten Zugriff bedeutet, dass MFA immer erforderlich ist.

#### <a name="implementing-your-nps-server"></a>Implementieren Ihres NPS-Servers

Wenn Sie eine NPS-Instanz bereitgestellt haben und sie bereits verwendet wird, lesen Sie [Integrieren Ihrer vorhandenen NPS-Infrastruktur in Azure Multi-Factor Authentication](howto-mfa-nps-extension.md). Wenn Sie den NPS zum ersten Mal einrichten, lesen Sie die Anweisungen in [Der Netzwerkrichtlinienserver (NPS)](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top). Eine Problembehandlungsanleitung finden Sie im Artikel [Auflösen von Fehlermeldungen in der NPS-Erweiterung für Azure Multi-Factor Authentication](howto-mfa-nps-extension-errors.md).

#### <a name="prepare-nps-for-users-that-arent-enrolled-for-mfa"></a>Vorbereiten von NPS für Benutzer, die nicht für MFA registriert sind

Wählen Sie aus, was geschieht, wenn Benutzer, die nicht in MFA registriert sind, versuchen, sich zu authentifizieren. Verwenden Sie die Registrierungseinstellung `REQUIRE_USER_MATCH` im Registrierungspfad `HKLM\Software\Microsoft\AzureMFA`, um das Verhalten des Features zu steuern. Diese Einstellung hat eine einzelne Konfigurationsoption.

| Schlüssel | Wert | Standard |
| --- | --- | --- |
| `REQUIRE_USER_MATCH` | TRUE/FALSE | Nicht festgelegt (gleichwertig mit TRUE) |

Diese Einstellung dient zum Bestimmen, was zu tun, wenn ein Benutzer nicht für MFA registriert ist. Die Auswirkungen der Änderung dieser Einstellung werden in der folgenden Tabelle aufgeführt.

| Einstellungen | Benutzer-MFA-Status | Effekte |
| --- | --- | --- |
| Schlüssel ist nicht vorhanden | Nicht registriert | MFA-Abfrage ist nicht erfolgreich |
| Wert auf TRUE festgelegt / nicht festgelegt | Nicht registriert | MFA-Abfrage ist nicht erfolgreich |
| Schlüssel auf FALSE festgelegt | Nicht registriert | Authentifizierung ohne MFA |
| Schlüssel auf FALSE oder TRUE festgelegt | Registriert | Authentifizierung mit MFA erforderlich |

### <a name="integrate-with-active-directory-federation-services"></a>Integrieren in Active Directory-Verbunddienste (AD FS)

Wenn Ihre Organisation mit Azure AD im Verbund ist, können Sie sowohl lokal als auch in der Cloud mit [Azure Multi-Factor Authentication AD FS-Ressourcen schützen](multi-factor-authentication-get-started-adfs.md). Mit Azure MFA können Sie die Zahl der Kennwörter reduzieren und eine sicherere Methode zur Authentifizierung bieten. Seit Windows Server 2016 können Sie Azure MFA für die primäre Authentifizierung konfigurieren.

Anders als mit AD FS unter Windows Server 2012 R2 wird der AD FS 2016 Azure MFA-Adapter direkt in Azure AD integriert und erfordert keinen lokalen Azure MFA-Server. Der Azure MFA-Adapter ist in Windows Server 2016 integriert, sodass keine zusätzliche Installation notwendig ist.

Wenn die Verwendung von Azure MFA mit AD FS 2016 und der Zielanwendung der Richtlinie für bedingten Zugriff unterliegt, sind zusätzliche Überlegungen erforderlich:

* Bedingter Zugriff ist verfügbar, wenn die Anwendung eine Azure AD vertrauende Seite ist, im Verbund mit AD FS 2016.
* Bedingter Zugriff ist nicht verfügbar, wenn die Anwendung eine AD FS 2016 vertrauende Seite ist und von AD FS 2016 verwaltet wird oder damit im Verbund ist.
* Bedingter Zugriff ist auch nicht verfügbar, wenn AD FS 2016 dafür konfiguriert ist, Azure MFA als primäre Authentifizierungsmethode zu verwenden.

#### <a name="ad-fs-logging"></a>AD FS-Protokollierung

Standardmäßige AD FS 2016-Protokollierung sowohl im Windows-Sicherheitsprotokoll als auch im AD FS-Administratorprotokoll, enthält Informationen zu Authentifizierungsanforderungen sowie deren Erfolg oder Fehler. Ereignisprotokolldaten in diesen Ereignissen geben an, ob Azure MFA verwendet wurde. Beispielsweise kann eine AD FS-Überwachungsereignis-ID 1200 Folgendes enthalten:

```
<MfaPerformed>true</MfaPerformed>
<MfaMethod>MFA</MfaMethod>
```

#### <a name="renew-and-manage-certificates"></a>Verlängern und Verwalten von Zertifikaten

Auf jedem AD FS-Server befindet sich im lokalen Computer My Store ein selbstsigniertes Azure MFA-Zertifikat mit dem Titel „OU=Microsoft AD FS Azure MFA“, das das Ablaufdatum des Zertifikats enthält. Überprüfen Sie die Gültigkeitsdauer dieses Zertifikats auf jedem AD FS-Server, um das Ablaufdatum zu ermitteln.

Wenn die Gültigkeitsdauer Ihrer Zertifikate sich dem Ablauf nähert, [generieren und überprüfen Sie auf jedem AD FS-Server ein neues MFA-Zertifikat](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-and-azure-mfa#configure-the-ad-fs-servers).

Die folgende Anleitung enthält Informationen zum Verwalten von Azure MFA-Zertifikaten auf Ihren AD FS-Servern. Wenn Sie AD FS mit Azure MFA konfigurieren, sind die über das `New-AdfsAzureMfaTenantCertificate`-PowerShell-Cmdlet generierten Zertifikate für 2 Jahre gültig. Verlängern Sie die Zertifikate, und installieren Sie sie vor Ablauf, um Unterbrechungen des MFA-Diensts zu vermeiden.

## <a name="implement-your-plan"></a>Implementieren Ihres Plans

Da Sie nun Ihre Lösung geplant haben, können Sie sie mit den folgenden Schritten implementieren:

1. Erfüllen Sie etwaige notwendige Voraussetzungen.
   1. Stellen Sie [Azure AD Connect](../hybrid/whatis-hybrid-identity.md) für Hybridszenarien bereit.
   1. Stellen Sie den [Azure AD-Anwendungsproxy](../manage-apps/application-proxy.md) für lokale Apps bereit, die für den Zugriff auf die Cloud veröffentlicht werden.
   1. Stellen Sie den [NPS](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top) zur RADIUS-Authentifizierung bereit.
   1. Stellen Sie sicher, dass Benutzer auf unterstützte Versionen von Microsoft Office mit aktivierter moderner Authentifizierung aktualisiert haben.
1. Konfigurieren Sie ausgewählte [Authentifizierungsmethoden](#choose-verification-options).
1. Definieren Sie Ihre [benannten Netzwerkadressen](../conditional-access/location-condition.md#named-locations).
1. Wählen Sie Gruppen aus, um mit der MFA-Einführung zu beginnen.
1. Konfigurieren Sie [Richtlinien für bedingten Zugriff](#create-conditional-access-policy).
1. Konfigurieren Sie Ihre MFA-Registrierungsrichtlinie.
   1. [Kombinierte MFA und SSPR](howto-registration-mfa-sspr-combined.md)
   1. Mit [Identity Protection](../identity-protection/howto-mfa-policy.md)
1. Fordern Sie die Benutzer zur Registrierung unter [https://aka.ms/mfasetup](https://aka.ms/mfasetup) auf.
1. [Nachverfolgen, wer sich registriert hat](#identify-non-registered-users)

## <a name="manage-your-solution"></a>Verwalten Ihrer Lösung

Berichte für Azure MFA

Azure Multi-Factor Authentication bietet Berichte über das Azure-Portal:

| Bericht | Location | BESCHREIBUNG |
| --- | --- | --- |
| Nutzung und Betrugswarnungen | Azure AD > Anmeldungen | Bietet Informationen zur Gesamtnutzung, Übersichts- und Detailinformationen zu Benutzern sowie einen Verlauf von Betrugswarnungen, die im angegebenen Zeitraum gesendet wurden. |

## <a name="troubleshoot-mfa-issues"></a>Behandeln von MFA-Problemen

Lösungen für häufig auftretende Probleme mit Azure MFA finden Sie im Artikel [Problembehandlung bei Azure mehrstufige Authentifizierung](https://support.microsoft.com/help/2937344/troubleshooting-azure-multi-factor-authentication-issues) im Microsoft Support Center.

## <a name="next-steps"></a>Nächste Schritte

* [Authentifizierungsmethoden](concept-authentication-methods.md)
* Lesen Sie außerdem, wie Sie die [zusammengeführte Registrierung für Azure Multi-Factor Authentication und die Self-Service-Kennwortzurücksetzung in Azure AD aktivieren](concept-registration-mfa-sspr-converged.md).
* Warum wurde ein Benutzer aufgefordert oder nicht aufgefordert, MFA auszuführen? Informationen finden Sie im Abschnitt [„Azure AD-Anmeldungenbericht“ im Dokument „Berichte in Azure Multi-Factor Authentication“](howto-mfa-reporting.md#azure-ad-sign-ins-report).
