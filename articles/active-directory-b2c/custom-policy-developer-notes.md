---
title: Entwicklerhinweise zu benutzerdefinierten Richtlinien
titleSuffix: Azure AD B2C
description: Dieser Artikel enthält Hinweise für Entwickler, die das Konfigurieren und Verwalten von Azure AD B2C mit benutzerdefinierten Richtlinien betreffen.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 05/19/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 18a3216855516156792524dc577ecef725d3119d
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/31/2021
ms.locfileid: "99218773"
---
# <a name="developer-notes-for-custom-policies-in-azure-active-directory-b2c"></a>Entwicklerhinweise zu benutzerdefinierten Richtlinien in Azure Active Directory B2C

Die benutzerdefinierte Konfiguration in Azure Active Directory B2C ist jetzt allgemein verfügbar. Dieser Konfigurationsmethode ist für fortgeschrittene Identitätsentwickler bestimmt, die komplexe Identitätslösungen erstellen. Durch benutzerdefinierte Richtlinien kann die Leistungsfähigkeit des Identity Experience Framework in Azure AD B2C-Mandanten genutzt werden.
Fortgeschrittene Identitätsentwickler, die benutzerdefinierte Richtlinien verwenden, sollten Zeit für das Durcharbeiten von exemplarischen Vorgehensweisen und für das Lesen von Referenzdokumenten einplanen.

Die meisten Optionen für benutzerdefinierte Richtlinien sind zwar nun allgemein verfügbar, aber einige zugrunde liegende Funktionen wie verschiedene technische Profile und APIs für die Inhaltsdefinition befinden sich noch in anderen Phasen des Softwarelebenszyklus. Es folgen noch viele weitere Funktionen. In der folgenden Tabelle erhalten Sie einen genaueren Überblick über den Verfügbarkeitsgrad.

## <a name="features-that-are-generally-available"></a>Allgemein verfügbare Features

- Erstellen und Hochladen von benutzerdefinierten User Journeys für die Authentifizierung mit benutzerdefinierten Richtlinien
    - Beschreiben von User Journeys (Schritt für Schritt) als Austauschvorgänge zwischen Anspruchsanbietern
    - Definieren der bedingten Verzweigung in User Journeys
- Interagieren mit REST-API-fähigen Diensten in User Journeys für die benutzerdefinierte Authentifizierung
- Einrichten von Verbünden mit Identitätsanbietern, die mit dem OpenID Connect-Protokoll konform sind
- Einrichten von Verbünden mit Identitätsanbietern, die auf dem SAML 2.0-Protokoll basieren

## <a name="responsibilities-of-custom-policy-feature-set-developers"></a>Zuständigkeiten für Entwickler von Featuregruppen für benutzerdefinierte Richtlinien

Die manuelle Richtlinienkonfiguration gewährt eingehenderen Zugriff auf die zugrunde liegende Plattform von Azure AD B2C und führt zur Erstellung eines eindeutigen Vertrauensframeworks. Für die vielen möglichen Permutationen von benutzerdefinierten Identitätsanbietern, Vertrauensstellungen, Integrationen in externe Dienste und Schritt-für-Schritt-Workflows ist ein methodischer Ansatz für den Entwurf und die Konfiguration erforderlich.

Entwickler, die das Feature für benutzerdefinierte Richtlinien verwenden, sollten folgende Leitlinien einhalten:

- Machen Sie sich mit der Konfigurationssprache der benutzerdefinierten Richtlinien und der Verwaltung von Schlüsseln und Geheimnissen vertraut. Weitere Informationen finden Sie unter [TrustFrameworkPolicy](trustframeworkpolicy.md).
- Übernehmen Sie den Besitz für Szenarien und benutzerdefinierte Integrationen. Dokumentieren Sie Ihre Arbeit, und halten Sie das Organisationsteam für Ihre Livewebsite auf dem Laufenden.
- Führen Sie methodische Szenariotests durch.
- Berücksichtigen Sie bewährte Methoden für die Softwareentwicklung und das Staging. Die Verwendung von mindestens einer Entwicklungs- und Testumgebung wird empfohlen.
- Informieren Sie sich regelmäßig über neue Entwicklungen bei den von Ihnen integrierten Identitätsanbietern und -diensten. Achten Sie beispielsweise auf Änderungen an Geheimnissen sowie auf geplante und ungeplante Änderungen am Dienst.
- Richten Sie eine aktive Überwachung ein, und überwachen Sie die Reaktionsfähigkeit von Produktionsumgebungen. Weitere Informationen zur Integration mit Application Insights finden Sie unter [Nachverfolgen des Benutzerverhaltens in Azure Active Directory B2C mithilfe von Application Insights](analytics-with-application-insights.md).
- Halten Sie die E-Mail-Adressen der Kontaktpersonen im Azure-Abonnement aktuell, und sorgen Sie dafür, dass auf die E-Mails des Microsoft-Livewebsite-Teams schnell reagiert wird.
- Ergreifen Sie ohne Verzögerung Maßnahmen, wenn Sie vom Microsoft-Livewebsite-Team dazu aufgefordert werden.

## <a name="terms-for-features-in-public-preview"></a>Nutzungsbedingungen für Features in der öffentlichen Vorschauversion

- Die Nutzung der neuen Features der öffentlichen Vorschauversion sollte nur zu Evaluierungszwecken erfolgen.
- Vereinbarungen zum Servicelevel (Service Level Agreements, SLAs) gelten nicht für die Features der öffentlichen Vorschauversion.
- Supportanfragen für Features der öffentlichen Vorschauversion können über die üblichen Supportkanäle gesendet werden.

## <a name="features-by-stage-and-known-issues"></a>Features nach Phase und bekannte Probleme

Die Funktionen für benutzerdefinierte Richtlinien werden ständig weiterentwickelt. Die folgende Tabelle dient als Index für die Verfügbarkeit von Features und Komponenten.


### <a name="protocols-and-authorization-flows"></a>Protokolle und Autorisierungsabläufe

| Funktion | Entwicklung | Vorschau | Allgemein verfügbar | Notizen |
|-------- | :-----------: | :-------: | :--: | ----- |
| [OAuth2-Autorisierungscode](authorization-code-flow.md) |  |  | X |  |
| OAuth2-Autorisierungscode mit PKCE |  |  | X | [Öffentliche Clients und Single-Page-Webanwendungen](authorization-code-flow.md)  |
| [Impliziter OAuth2-Fluss](implicit-flow-single-page-application.md) |  |  | X |  |
| [Kennwortanmeldeinformationen von Ressourcenbesitzern mittels OAuth2](ropc-custom.md) |  | X |  |  |
| [OIDC Connect](openid-connect.md) |  |  | X |  |
| [SAML2](connect-with-saml-service-providers.md)  |  |  |X  | POST- und Umleitungsbindungen |
| OAuth1 |  |  |  | Wird nicht unterstützt. |
| WSFED | X |  |  |  |

### <a name="identify-providers-federation"></a>Identifizieren des Verbunds von Anbietern 

| Funktion | Entwicklung | Vorschau | Allgemein verfügbar | Notizen |
|-------- | :-----------: | :-------: | :--: | ----- |
| [OpenID Connect](openid-connect-technical-profile.md) |  |  | X | z.B. Google+  |
| [OAuth2](oauth2-technical-profile.md) |  |  | X | z.B. Facebook  |
| [OAuth1](oauth1-technical-profile.md) |  | X |  | z.B. Twitter |
| [SAML2](saml-identity-provider-technical-profile.md) |  |   | X | z.B. Salesforce, ADFS |
| WSFED| X |  |  |  |


### <a name="rest-api-integration"></a>REST-API-Integration

| Funktion | Entwicklung | Vorschau | Allgemein verfügbar | Notizen |
|-------- | :-----------: | :-------: | :--: | ----- |
| [REST-API mit Standardauthentifizierung](secure-rest-api.md#http-basic-authentication) |  |  | X |  |
| [REST-API mit Clientzertifikatauthentifizierung](secure-rest-api.md#https-client-certificate-authentication) |  |  | X |  |
| [REST-API mit OAuth2-Bearerauthentifizierung](secure-rest-api.md#oauth2-bearer-authentication) |  | X |  |  |

### <a name="component-support"></a>Unterstützung für Komponenten

| Funktion | Entwicklung | Vorschau | Allgemein verfügbar | Notizen |
| ------- | :-----------: | :-------: | :--: | ----- |
| [PhoneFactor-Authentifizierung](phone-factor-technical-profile.md) |  |  | X |  |
| [Azure AD MFA-Authentifizierung](multi-factor-auth-technical-profile.md) |  | X |  |  |
| [Einmalkennwort](one-time-password-technical-profile.md) |  | X |  |  |
| [Azure Active Directory](active-directory-technical-profile.md) als lokales Verzeichnis |  |  | X |  |
| E-Mail-Subsystem in Azure für die Überprüfung per E-Mail |  |  | X |  |
| [E-Mail-Dienste von Drittanbietern](custom-email-mailjet.md) |  |X  |  |  |
| [Unterstützung für mehrere Sprachen](localization.md)|  |  | X |  |
| [Prädikatüberprüfungen](predicates.md) |  |  | X | z.B. Kennwortkomplexität |
| [Anzeigesteuerelemente](display-controls.md) |  |X  |  |  |


### <a name="app-ief-integration"></a>App-IEF-Integration

| Funktion | Entwicklung | Vorschau | Allgemein verfügbar | Notizen |
| ------- | :-----------: | :-------: | :--: | ----- |
| Abfragezeichenfolgenparameter `domain_hint` |  |  | X | als Anspruch verfügbar, kann an IDP übergeben werden |
| Abfragezeichenfolgenparameter `login_hint` |  |  | X | als Anspruch verfügbar, kann an IDP übergeben werden |
| Einfügen von JSON in User Journey über `client_assertion` | X |  |  | wird eingestellt |
| Einfügen von JSON in User Journey als `id_token_hint` |  | X |  | Go-Forward-Ansatz zum Übergeben von JSON |
| [Übergeben des Tokens eines Identitätsanbieters an die Anwendung](idp-pass-through-user-flow.md) |  | X |  | z.B. von Facebook in eine App |


### <a name="session-management"></a>Sitzungsverwaltung

| Funktion | Entwicklung | Vorschau | Allgemein verfügbar | Notizen |
| ------- | :-----------: | :-------: | :--: | ----- |
| [SSO-Standardsitzungsanbieter](custom-policy-reference-sso.md#defaultssosessionprovider) |  |  | X |  |
| [Sitzungsanbieter mit externer Anmeldung](custom-policy-reference-sso.md#externalloginssosessionprovider) |  |  | X |  |
| [SAML SSO-Sitzungsanbieter](custom-policy-reference-sso.md#samlssosessionprovider) |  |  | X |  |
| [OAuthSSOSessionProvider](custom-policy-reference-sso.md#oauthssosessionprovider)  |  | X |  |  |
| [Einmaliges Abmelden](session-behavior.md#sign-out)  |  | X |  |  |

### <a name="security"></a>Sicherheit

| Funktion | Entwicklung | Vorschau | Allgemein verfügbar | Notizen |
|-------- | :-----------: | :-------: | :--: | ----- |
| Richtlinienschlüssel – Generieren, Manuell, Hochladen |  |  | X |  |
| Richtlinienschlüssel – RSA/Zertifikate, Geheimnisse |  |  | X |  |


### <a name="developer-interface"></a>Entwicklerschnittstelle

| Funktion | Entwicklung | Vorschau | Allgemein verfügbar | Notizen |
| ------- | :-----------: | :-------: | :--: | ----- |
| Azure-Portal – IEF UX |  |  | X |  |
| Hochladen von Richtlinien |  |  | X |  |
| [Application Insights, User Journey-Protokolle](troubleshoot-with-application-insights.md) |  | X |  | für die Problembehandlung bei der Entwicklung  |
| [Application Insights, Ereignisprotokolle](analytics-with-application-insights.md) |  | X |  | für die Überwachung von Benutzerabläufen in der Produktion |


## <a name="next-steps"></a>Nächste Schritte

- Sehen Sie sich die [Microsoft Graph-Vorgänge an, die für Azure AD B2C verfügbar sind](microsoft-graph-operations.md).
- Weitere Informationen finden Sie unter [Benutzerdefinierte Richtlinien in Azure Active Directory B2C](custom-policy-overview.md).
