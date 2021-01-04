---
title: Authentifizierungsmethoden und -funktionen – Azure Active Directory
description: Erfahren Sie mehr über die verschiedenen in Azure Active Directory verfügbaren Authentifizierungsmethoden und -funktionen, um Anmeldeereignisse zu verbessern und sicher zu gestalten
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 09/15/2020
ms.author: justinha
author: justinha
manager: daveba
ms.collection: M365-identity-device-management
ms.custom: contperfq4
ms.openlocfilehash: c5b2003a290152533a0a8c0f88fe8eb1a410ccf9
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/06/2020
ms.locfileid: "96744328"
---
# <a name="what-authentication-and-verification-methods-are-available-in-azure-active-directory"></a>Welche Authentifizierungs- und Prüfmethoden stehen in Azure Active Directory zur Verfügung?

Bei der Anmeldung bei Konten in Azure Active Directory (Azure AD) hat ein Benutzer verschiedene Möglichkeiten, sich zu authentifizieren. Benutzername und Kennwort sind die gängigste Art und Weise, in der ein Benutzer bisher seine Anmeldeinformationen bereitgestellt hat. Dank moderner Authentifizierungs- und Sicherheitsfunktionen in Azure AD sollte dieses Basiskennwort durch sicherere Authentifizierungsmethoden ergänzt oder ersetzt werden.

![Tabelle mit den bevorzugten Authentifizierungsmethoden in Azure AD und deren Stärken](media/concept-authentication-methods/authentication-methods.png)

Die sichersten Anmeldeereignisse erzielen Sie mit kennwortlosen Authentifizierungsmethoden wie Windows Hello, FIDO2-Sicherheitsschlüssel und Microsoft Authenticator-App.

Bei der Benutzeranmeldung bietet Azure AD Multi-Factor Authentication (MFA) zusätzliche Sicherheit im Vergleich zur alleinigen Verwendung eines Kennworts. Der Benutzer kann aufgefordert werden, sich zusätzlich in einer anderen Form zu authentifizieren, z. B. durch die Antwort auf eine Pushbenachrichtigung, die Eingabe eines Codes von einem Software- oder Hardwaretoken oder die Beantwortung einer SMS oder eines Telefonanrufs.

Um die Umgebung für das Onboarding von Benutzern zu vereinfachen und diese sowohl für MFA als auch für SSPR zu registrieren, empfiehlt es sich, [die kombinierte Registrierung von Sicherheitsinformationen zu aktivieren](howto-registration-mfa-sspr-combined.md). Aus Resilienzgründen ist es empfehlenswert, Benutzer aufzufordern, mehrere Authentifizierungsmethoden zu registrieren. Wenn eine Methode bei der Anmeldung oder bei SSPR für einen Benutzer nicht verfügbar ist, kann er sich wahlweise mit einer anderen Methode authentifizieren. Weitere Informationen finden Sie unter [Erstellen einer robusten Verwaltungsstrategie für die Zugriffssteuerung (Azure AD)](concept-resilient-controls.md).

## <a name="authentication-method-strength-and-security"></a>Stärke und Sicherheit von Authentifizierungsmethoden

Wenn Sie in Ihrer Organisation Features wie Azure AD Multi-Factor Authentication bereitstellen, überprüfen Sie die verfügbaren Authentifizierungsmethoden. Wählen Sie die Methoden aus, die Ihre Anforderungen im Hinblick auf Sicherheit, Nutzbarkeit und Verfügbarkeit erfüllen oder darüber hinausgehen. Verwenden Sie nach Möglichkeit Authentifizierungsmethoden mit der höchsten Sicherheitsstufe.

In der folgenden Tabelle sind die Sicherheitsaspekte der verfügbaren Authentifizierungsmethoden aufgeführt. Verfügbarkeit ist ein Hinweis darauf, dass der Benutzer die Authentifizierungsmethode verwenden kann, nicht auf die Dienstverfügbarkeit in Azure AD:

| Authentifizierungsmethode          | Sicherheit | Benutzerfreundlichkeit | Verfügbarkeit |
|--------------------------------|:--------:|:---------:|:------------:|
| Windows Hello for Business     | High     | High      | High         |
| Microsoft Authenticator-App    | High     | High      | High         |
| FIDO2-Sicherheitsschlüssel (Vorschau)   | High     | High      | High         |
| OATH-Hardwaretoken (Vorschau) | Medium   | Medium    | High         |
| OATH-Softwaretoken           | Medium   | Medium    | High         |
| SMS                            | Medium   | High      | Medium       |
| Sprache                          | Medium   | Medium    | Medium       |
| Kennwort                       | Niedrig      | High      | High         |

Weitere Informationen zur Sicherheit finden Sie unter [Authentifizierungsrisiken und Angriffsvektoren](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/all-your-creds-are-belong-to-us/ba-p/855124).

> [!TIP]
> Aus Gründen der Flexibilität und Benutzerfreundlichkeit empfehlen wir, die Microsoft Authenticator-App zu verwenden. Diese Authentifizierungsmethode bietet die bestmögliche Benutzererfahrung und verfügt über mehrere Modi, z. B. kennwortlose Authentifizierung, MFA-Pushbenachrichtigungen und OATH-Codes.

## <a name="how-each-authentication-method-works"></a>Funktionsweise der einzelnen Authentifizierungsmethoden

Einige Authentifizierungsmethoden können als primärer Faktor verwendet werden, wenn Sie sich bei einer Anwendung oder einem Gerät anmelden, z. B. mit einem FIDO2-Sicherheitsschlüssel oder einem Kennwort. Andere Authentifizierungsmethoden sind nur als sekundärer Faktor verfügbar, wenn Sie Azure AD Multi-Factor Authentication oder SSPR verwenden.

Aus der folgenden Tabelle geht hervor, wann eine Authentifizierungsmethode bei einem Anmeldeereignis verwendet werden kann:

| Methode                         | Primäre Authentifizierung | Sekundäre Authentifizierung  |
|--------------------------------|:----------------------:|:-------------------------:|
| Windows Hello for Business     | Ja                    | MFA                       |
| Microsoft Authenticator-App    | Ja (Vorschau)          | MFA und SSPR              |
| FIDO2-Sicherheitsschlüssel (Vorschau)   | Ja                    | MFA                       |
| OATH-Hardwaretoken (Vorschau) | Nein                     | MFA                       |
| OATH-Softwaretoken           | Nein                     | MFA                       |
| SMS                            | Ja (Vorschau)          | MFA und SSPR              |
| Anruf                     | Nein                     | MFA und SSPR              |
| Kennwort                       | Ja                    |                           |

All diese Authentifizierungsmethoden können im Azure-Portal und zunehmend auch mithilfe der [Microsoft Graph-REST-API (Betaversion)](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta) konfiguriert werden.

Weitere Informationen zur Funktionsweise der einzelnen Authentifizierungsmethoden finden Sie in den folgenden einzelnen konzeptionellen Artikeln:

* [Windows Hello for Business](/windows/security/identity-protection/hello-for-business/hello-overview)
* [Microsoft Authenticator-App](concept-authentication-authenticator-app.md)
* [FIDO2-Sicherheitsschlüssel (Vorschau)](concept-authentication-passwordless.md#fido2-security-keys)
* [OATH-Hardwaretoken (Vorschau)](concept-authentication-oath-tokens.md#oath-hardware-tokens-preview)
* [Oath-Softwaretoken](concept-authentication-oath-tokens.md#oath-software-tokens)
* [Anmeldung (Vorschau)](howto-authentication-sms-signin.md) und [Überprüfung](concept-authentication-phone-options.md#mobile-phone-verification) mit SMS
* [Überprüfung mit Sprachanruf](concept-authentication-phone-options.md)
* Kennwort

> [!NOTE]
> In Azure AD wird häufig ein Kennwort als primäre Authentifizierungsmethode verwendet. Die Authentifizierungsmethode über Kennwort kann nicht deaktiviert werden. Wenn Sie ein Kennwort als primären Authentifizierungsfaktor verwenden, sollten Sie die Sicherheit von Anmeldeereignissen mithilfe von Azure AD Multi-Factor Authentication erhöhen.

In bestimmten Szenarien können die folgenden zusätzlichen Überprüfungsmethoden verwendet werden:

* [App-Kennwörter](howto-mfa-app-passwords.md) – Werden für alte Anwendungen verwendet, die keine moderne Authentifizierung unterstützen. Können für die benutzerbezogene Azure AD Multi-Factor Authentication konfiguriert werden.
* [Sicherheitsfragen](concept-authentication-security-questions.md): Werden nur für SSPR verwendet.
* [E-Mail-Adresse](concept-sspr-howitworks.md#authentication-methods): Wird nur für SSPR verwendet.

## <a name="next-steps"></a>Nächste Schritte

Informationen zum Einstieg finden Sie im Tutorial zur [Self-Service-Kennwortzurücksetzung (SSPR)][tutorial-sspr] und im Tutorial zu [Azure AD Multi-Factor Authentication][tutorial-azure-mfa].

Weitere Informationen zu SSPR-Konzepten finden Sie unter [So funktioniert's: Self-Service-Kennwortzurücksetzung in Azure AD][concept-sspr].

Weitere Informationen zu MFA-Konzepten finden Sie unter [Funktionsweise von Azure AD Multi-Factor Authentication][concept-mfa].

Im Artikel [API-Übersicht zu Azure AD-Authentifizierungsmethoden](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta) finden Sie weitere Informationen zur Betaversion der REST-API für Microsoft Graph.

Weitere Informationen zu den verwendeten Authentifizierungsmethoden finden Sie unter [Azure AD Multi-Factor Authentication authentication method analysis with PowerShell](/samples/azure-samples/azure-mfa-authentication-method-analysis/azure-mfa-authentication-method-analysis/) (Authentifizierungsmethode von Azure AD Multi-Factor Authentication mit PowerShell).

<!-- INTERNAL LINKS -->
[tutorial-sspr]: tutorial-enable-sspr.md
[tutorial-azure-mfa]: tutorial-enable-azure-mfa.md
[concept-sspr]: concept-sspr-howitworks.md
[concept-mfa]: concept-mfa-howitworks.md
