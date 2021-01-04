---
title: Telefonauthentifizierungsmethoden – Azure Active Directory
description: Erfahren Sie mehr über die Verwendung von Telefonauthentifizierungsmethoden in Azure Active Directory zum Verbessern und Sichern von Anmeldeereignissen.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/18/2020
ms.author: justinha
author: justinha
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: c9d3a00491bc0628244a5a7907f0dee03a5f8d3c
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/06/2020
ms.locfileid: "96744192"
---
# <a name="authentication-methods-in-azure-active-directory---phone-options"></a>Authentifizierungsmethoden in Azure Active Directory – Telefonoptionen

Informationen zur direkten Authentifizierung mit Textnachrichten finden Sie unter [Konfigurieren und Aktivieren der SMS-basierten Authentifizierung von Benutzern (Vorschau)](howto-authentication-sms-signin.md). Die SMS-basierte Anmeldung eignet sich hervorragend für Außendienstmitarbeiter. Bei der SMS-basierten Anmeldung brauchen sich Benutzer keine Benutzernamen und Kennwörter zu merken, um auf Anwendungen und Dienste zuzugreifen. Der Benutzer gibt stattdessen seine registrierte Mobiltelefonnummer ein, empfängt eine Textnachricht mit einem Prüfcode und gibt diesen Code im Anmeldebildschirm ein.

Benutzer können bei Azure AD Multi-Factor Authentication oder der Self-Service-Kennwortzurücksetzung (SSPR) als zweite Authentifizierungsmethode auch eine Mobiltelefonnummer oder eine geschäftliche Telefonnummer verwenden.

Für eine ordnungsgemäße Funktion müssen Telefonnummern im Format *+Landesvorwahl Telefonnummer* vorliegen (Beispiel: *+1 4251234567*).

> [!NOTE]
> Zwischen Landes-/Ortsvorwahl und Telefonnummer muss sich ein Leerzeichen befinden.
>
> Bei der Kennwortzurücksetzung werden Nebenstellen nicht unterstützt. Selbst bei der Angabe im Format *+1 4251234567X12345* werden Nebenstellen vor dem Anruf entfernt.

## <a name="mobile-phone-verification"></a>Überprüfung mit Mobiltelefonnummer

Bei Azure AD Multi-Factor Authentication oder SSPR stehen Benutzern zwei Optionen zur Auswahl: Empfang einer SMS mit einem Prüfcode, der im Anmeldebildschirm eingegeben wird, oder Empfang eines Telefonanrufs.

Wenn ein Benutzer nicht möchte, dass seine Mobiltelefonnummer im Verzeichnis sichtbar ist, sie aber dennoch für die Kennwortzurücksetzung verwenden möchte, sollten Administratoren die Telefonnummer nicht in das Verzeichnis übernehmen. Der Benutzer sollte stattdessen über die kombinierte Registrierung von Sicherheitsinformationen auf [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo) das Attribut **Authentifizierungstelefon** für sich eingeben. Administratoren können diese Informationen im Profil des Benutzers anzeigen, sie werden jedoch nicht an anderer Stelle veröffentlicht.

:::image type="content" source="media/concept-authentication-methods/user-authentication-methods.png" alt-text="Screenshot des Azure-Portals: Authentifizierungsmethoden mit übernommener Telefonnummer":::

Microsoft kann keine Bereitstellung konsistenter SMS- oder Sprachaufforderungen für Azure AD Multi-Factor Authentication durch dieselbe Nummer garantieren. Im Interesse unserer Benutzer können wir jederzeit Kurzcodes hinzuzufügen oder entfernen, wenn wir Routenanpassungen zur Verbesserung der Zustellbarkeit von SMS vornehmen. Microsoft unterstützt keine Kurzcodes für Länder/Regionen außerhalb der USA und Kanadas.

### <a name="text-message-verification"></a>Überprüfung mit Textnachricht

Bei der Überprüfung mit Textnachricht wird bei Azure AD Multi-Factor Authentication oder der Self-Service-Kennwortzurücksetzung eine SMS mit einem Prüfcode an die Mobiltelefonnummer gesendet. Zum Abschließen des Anmeldevorgangs muss der bereitgestellte Prüfcode im Anmeldebildschirm eingegeben werden.

### <a name="phone-call-verification"></a>Überprüfung mit Telefonanruf

Bei der Überprüfung mit Telefonanruf erfolgt bei Azure AD Multi-Factor Authentication oder der Self-Service-Kennwortzurücksetzung ein automatischer Sprachanruf an die vom Benutzer registrierte Telefonnummer. Zum Abschließen des Anmeldevorgangs wird der Benutzer aufgefordert, die Taste „#“ auf der Tastatur zu drücken.

## <a name="office-phone-verification"></a>Überprüfung mit geschäftlicher Rufnummer

Bei der Überprüfung mit Telefonanruf erfolgt bei Azure AD Multi-Factor Authentication oder der Self-Service-Kennwortzurücksetzung ein automatischer Sprachanruf an die vom Benutzer registrierte Telefonnummer. Zum Abschließen des Anmeldevorgangs wird der Benutzer aufgefordert, die Taste „#“ auf der Tastatur zu drücken.

## <a name="troubleshooting-phone-options"></a>Problembehandlung bei Telefonoptionen

Wenn bei der telefonischen Authentifizierung für Azure AD Probleme auftreten, überprüfen Sie die folgenden Schritte zur Problembehebung:

* Fehlermeldungen „Sie haben das Limit für Verifizierungsanrufe erreicht“ oder „Sie haben das Limit für Textprüfcodes erreicht“ während Anmeldungen
   * Microsoft begrenzt möglicherweise wiederholte Authentifizierungsversuche vom selben Benutzer innerhalb eines kurzen Zeitraums. Diese Einschränkung gilt nicht für die Verifizierung per Microsoft Authenticator oder Prüfcode. Wenn diese Grenzwerte erreicht sind, können Sie die Authenticator-App oder einen Prüfcode verwenden oder nach einigen Minuten erneut versuchen sich anzumelden.
* Gesperrte Anrufer-ID auf einem einzelnen Gerät.
   * Überprüfen Sie alle gesperrten Telefonnummern, die auf dem Gerät konfiguriert sind.
* Falsche Telefonnummer oder falsche Landes-/Ortsvorwahl oder Verwechslung von persönlicher und geschäftlicher Telefonnummer.
   * Beheben Sie das Problem in Bezug auf das Benutzerobjekt und die konfigurierten Authentifizierungsmethoden. Stellen Sie sicher, dass die richtigen Telefonnummern registriert sind.
* Falsche PIN eingegeben.
   * Vergewissern Sie sich, dass der Benutzer die richtige PIN, die für sein Konto registriert ist, verwendet hat (nur MFA-Server-Benutzer).
* Anruf an Voicemail weitergeleitet.
   * Stellen Sie sicher, dass der Benutzer sein Telefon eingeschaltet hat und dieser Dienst in seiner Region verfügbar ist, oder verwenden Sie die alternative Methode.
* Benutzer ist gesperrt
   * Veranlassen Sie, dass ein Azure AD-Administrator den Benutzer im Azure-Portal entsperrt.
* SMS ist auf dem Gerät nicht abonniert.
   * Lassen Sie den Benutzer die Methoden ändern oder SMS auf dem Gerät aktivieren.
* Fehler beim Telekommunikationsanbieter (z. B. Telefoneingabe nicht erkannt, Problem mit fehlenden MFV-Tönen, gesperrte Anrufer-ID auf mehreren Geräten oder SMS auf mehreren Geräten gesperrt).
   * Microsoft verwendet mehrere Telekommunikationsanbieter, um Telefonanrufe und SMS-Nachrichten für die Authentifizierung weiterzuleiten. Wenn eines der oben genannten Probleme auftritt, bitten Sie den Benutzer, die Methode innerhalb von 5 Minuten mindestens fünfmal zu verwenden, und halten Sie die Informationen dieses Benutzers bereit, wenn Sie sich an den Microsoft-Support wenden.

## <a name="next-steps"></a>Nächste Schritte

Informationen zum Einstieg finden Sie im Tutorial zur [Self-Service-Kennwortzurücksetzung (SSPR)][tutorial-sspr] und im Tutorial zu [Azure AD Multi-Factor Authentication][tutorial-azure-mfa].

Weitere Informationen zu SSPR-Konzepten finden Sie unter [So funktioniert's: Self-Service-Kennwortzurücksetzung in Azure AD][concept-sspr].

Weitere Informationen zu MFA-Konzepten finden Sie unter [Funktionsweise von Azure AD Multi-Factor Authentication][concept-mfa].

Im Artikel [API-Übersicht zu Azure AD-Authentifizierungsmethoden](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta&preserve-view=true) finden Sie weitere Informationen zur Betaversion der REST-API für Microsoft Graph.

<!-- INTERNAL LINKS -->
[tutorial-sspr]: tutorial-enable-sspr.md
[tutorial-azure-mfa]: tutorial-enable-azure-mfa.md
[concept-sspr]: concept-sspr-howitworks.md
[concept-mfa]: concept-mfa-howitworks.md
