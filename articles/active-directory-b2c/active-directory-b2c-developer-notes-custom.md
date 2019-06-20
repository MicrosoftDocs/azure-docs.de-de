---
title: Entwicklerhinweise zu benutzerdefinierten Richtlinien (Azure Active Directory B2C) | Microsoft-Dokumentation
description: Dieser Artikel enthält Hinweise für Entwickler, die das Konfigurieren und Verwalten von Azure AD B2C mit benutzerdefinierten Richtlinien betreffen.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 1d0be4ec2ed8feb308839377e0494ef2f4b78368
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66510209"
---
# <a name="developer-notes-for-custom-policies-in-azure-active-directory-b2c"></a>Entwicklerhinweise zu benutzerdefinierten Richtlinien in Azure Active Directory B2C

Die benutzerdefinierte Konfiguration in Azure Active Directory B2C ist jetzt allgemein verfügbar. Dieser Konfigurationsmethode ist für fortgeschrittene Identitätsentwickler bestimmt, die komplexe Identitätslösungen erstellen. Durch benutzerdefinierte Richtlinien kann die Leistungsfähigkeit des Identity Experience Framework in Azure AD B2C-Mandanten genutzt werden. Fortgeschrittene Identitätsentwickler, die benutzerdefinierte Richtlinien verwenden, sollten Zeit für das Durcharbeiten von exemplarischen Vorgehensweisen und für das Lesen von Referenzdokumenten einplanen.

Die meisten Optionen für benutzerdefinierte Richtlinien sind zwar nun allgemein verfügbar, aber einige zugrunde liegende Funktionen wie verschiedene technische Profile und APIs für die Inhaltsdefinition befinden sich noch in anderen Phasen des Softwarelebenszyklus. Es folgen noch viele weitere Funktionen. In der folgenden Tabelle erhalten Sie einen genaueren Überblick über den Verfügbarkeitsgrad.  

## <a name="features-that-are-generally-available"></a>Allgemein verfügbare Features

- Erstellen und Hochladen von benutzerdefinierten User Journeys für die Authentifizierung mit benutzerdefinierten Richtlinien  
    - Beschreiben von User Journeys als Austauschvorgänge zwischen Anspruchsanbietern (Schritt für Schritt)  
    - Definieren der bedingten Verzweigung in User Journeys  
- Interagieren mit REST-API-fähigen Diensten in User Journeys für die benutzerdefinierte Authentifizierung  
- Einrichten von Verbünden mit Identitätsanbietern, die mit dem OpenID Connect-Protokoll konform sind  
- Einrichten von Verbünden mit Identitätsanbietern, die auf dem SAML 2.0-Protokoll basieren   

## <a name="responsibilities-of-custom-policy-feature-set-developers"></a>Zuständigkeiten für Entwickler von Featuregruppen für benutzerdefinierte Richtlinien

Die manuelle Richtlinienkonfiguration gewährt eingehenderen Zugriff auf die zugrunde liegende Plattform von Azure AD B2C und führt zur Erstellung eines eindeutigen Vertrauensframeworks. Für die möglichen Permutationen von benutzerdefinierten Identitätsanbietern, Vertrauensstellungen, Integrationen in externe Dienste und ausführlichen Workflows ist ein methodischer Ansatz beim Entwurf und bei der Konfiguration nötig. 

Entwickler, die das Feature für benutzerdefinierte Richtlinien verwenden, sollten folgende Leitlinien einhalten:

- Machen Sie sich mit der Konfigurationssprache der benutzerdefinierten Richtlinien und der Verwaltung von Schlüsseln und Geheimnissen vertraut. Weitere Informationen finden Sie unter [TrustFrameworkPolicy](trustframeworkpolicy.md). 
- Übernehmen Sie den Besitz für Szenarien und benutzerdefinierte Integrationen. Dokumentieren Sie Ihre Arbeit, und halten Sie das Organisationsteam für Ihre Livewebsite auf dem Laufenden.  
- Führen Sie methodische Szenariotests durch. 
- Nutzen Sie die bewährten Methoden für die Softwareentwicklung und das Staging, und verwenden Sie mindestens eine Entwicklungs-/Testumgebung und eine Produktionsumgebung. 
- Informieren Sie sich regelmäßig über neue Entwicklungen bei den von Ihnen integrierten Identitätsanbietern und -diensten. Achten Sie beispielsweise auf Änderungen an Geheimnissen sowie auf geplante und ungeplante Änderungen am Dienst. 
- Richten Sie eine aktive Überwachung ein, und überwachen Sie die Reaktionsfähigkeit von Produktionsumgebungen. Weitere Informationen zur Integration mit Application Insights finden Sie unter [Nachverfolgen des Benutzerverhaltens in Azure Active Directory B2C mithilfe von Application Insights](active-directory-b2c-custom-guide-eventlogger-appins.md). 
- Halten Sie die E-Mail-Adressen der Kontaktpersonen im Azure-Abonnement aktuell, und sorgen Sie dafür, dass auf die E-Mails des Microsoft-Livewebsite-Teams schnell reagiert wird. 
- Ergreifen Sie ohne Verzögerung Maßnahmen, wenn Sie vom Microsoft-Livewebsite-Team dazu aufgefordert werden.

## <a name="terms-for-features-in-public-preview"></a>Nutzungsbedingungen für Features in der öffentlichen Vorschauversion

- Die Nutzung der neuen Features der öffentlichen Vorschauversion sollte nur zu Evaluierungszwecken erfolgen. 
- Vereinbarungen zum Servicelevel (Service Level Agreements, SLAs) gelten nicht für die Features der öffentlichen Vorschauversion.
- Supportanfragen für Features der öffentlichen Vorschauversion können über die üblichen Supportkanäle gesendet werden. 

## <a name="features-by-stage-and-known-issues"></a>Features nach Phase und bekannte Probleme

Funktionen für benutzerdefinierte Richtlinien und das Identity Experience Framework werden laufend und schnell weiterentwickelt. Die folgende Tabelle dient als Index für die Verfügbarkeit von Features und Komponenten.

### <a name="identity-providers-tokens-protocols"></a>Identitätsanbieter, Token, Protokolle

| Feature | Entwicklung | Vorschau | Allgemein verfügbar | Notizen |
|-------- | ----------- | ------- | -- | ----- |
| IDP-OpenIDConnect |  |  | X | z.B. Google+  |
| IDP-OAUTH2 |  |  | X | z.B. Facebook  |
| IDP-OAUTH1 (Twitter) |  | X |  | z.B. Twitter |
| IDP-OAUTH1 (ehemals Twitter) |  |  |  | Nicht unterstützt |
| IDP-SAML |  |   | X | z.B. Salesforce, ADFS |
| IDP-WSFED | X |  |  |  |
| OAUTH1 der vertrauenden Seite |  |  |  | Nicht unterstützt. |
| OAUTH2 der vertrauenden Seite |  |  | X |  |
| OIDC der vertrauenden Seite |  |  | X |  |
| SAML der vertrauenden Seite | X |  |  |  |
| WSFED der vertrauenden Seite | X |  |  |  |
| REST-API mit Standard- und Zertifikatauthentifizierung |  |  | X | z.B. Azure Logic Apps |

### <a name="component-support"></a>Unterstützung für Komponenten

| Feature | Entwicklung | Vorschau | Allgemein verfügbar | Notizen |
| ------- | ----------- | ------- | -- | ----- |
| Azure Multi-Factor Authentication |  |  | X |  |
| Azure Active Directory als lokales Verzeichnis |  |  | X |  |
| E-Mail-Subsystem in Azure für die Verifizierung per E-Mail |  |  | X |  |
| Unterstützung für mehrere Sprachen|  |  | X |  |
| Prädikatüberprüfungen |  |  | X | z.B. Kennwortkomplexität |
| Verwendung von E-Mail-Diensten von Drittanbietern | X |  |  |  |

### <a name="content-definition"></a>Inhaltsdefinition

| Feature | Entwicklung | Vorschau | Allgemein verfügbar | Notizen |
| ------- | ----------- | ------- | -- | ----- |
| Fehlerseite, api.error |  |  | X |  |
| IDP-Auswahlseite, api.idpselections |  |  | X |  |
| IDP-Auswahl zur Registrierung, api.idpselections.signup |  |  | X |  |
| Kennwort vergessen, api.localaccountpasswordreset |  |  | X |  |
| Anmeldung mit lokalem Konto, api.localaccountsignin |  |  | X |  |
| Registrierung mit lokalem Konto, api.localaccountsignup |  |  | X |  |
| MFA-Seite, api.phonefactor |  |  | X |  |
| Selbstbestätigte Registrierung über ein Konto eines sozialen Netzwerks, api.selfasserted |  |  | X |  |
| Selbstbestätigte Profilaktualisierung, api.selfasserted.profileupdate |  |  | X |  |
| Einheitliche Seite für Registrierung oder Anmeldung, api.signuporsignin, mit dem Parameter „disableSignup“ |  |  | X |  |
| JavaScript/Seitenvertrag |  | X |  |  |

### <a name="app-ief-integration"></a>App-IEF-Integration

| Feature | Entwicklung | Vorschau | Allgemein verfügbar | Notizen |
| ------- | ----------- | ------- | -- | ----- |
| Abfragezeichenfolgen-Parameter „domain_hint“ |  |  | X | als Anspruch verfügbar, kann an IDP übergeben werden |
| Abfragezeichenfolgen-Parameter „login_hint“ |  |  | X | als Anspruch verfügbar, kann an IDP übergeben werden |
| Einfügen von JSON in UserJourney über „client_assertion“ | X |  |  | wird eingestellt |
| Einfügen von JSON in UserJourney als „id_token_hint“ |  | X |  | Go-Forward-Ansatz zum Übergeben von JSON |
| Übergeben von IDP-Tokens an die Anwendung |  | X |  | z.B. von Facebook in eine App |

### <a name="session-management"></a>Sitzungsverwaltung

| Feature | Entwicklung | Vorschau | Allgemein verfügbar | Notizen |
| ------- | ----------- | ------- | -- | ----- |
| SSO-Sitzungsanbieter |  |  | X |  |
| Sitzungsanbieter mit externer Anmeldung |  |  | X |  |
| SAML SSO-Sitzungsanbieter |  |  | X |  |
| SSO-Standardsitzungsanbieter |  |  | X |  |

### <a name="security"></a>Sicherheit

| Feature | Entwicklung | Vorschau | Allgemein verfügbar | Notizen |
|-------- | ----------- | ------- | -- | ----- |
| Richtlinienschlüssel – Generieren, Manuell, Hochladen |  |  | X |  |
| Richtlinienschlüssel – RSA/Zertifikate, Geheimnisse |  |  | X |  |
| Hochladen von Richtlinien |  |  | X |  |

### <a name="developer-interface"></a>Entwicklerschnittstelle

| Feature | Entwicklung | Vorschau | Allgemein verfügbar | Notizen |
| ------- | ----------- | ------- | -- | ----- |
| Azure-Portal – IEF UX |  |  | X |  |
| Application Insights, UserJourney-Protokolle |  | X |  | für die Problembehandlung bei der Entwicklung  |
| Application Insights-Ereignisprotokolle (über Orchestrierungsschritte) |  | X |  | für die Überwachung von Benutzerabläufen in der Produktion |

## <a name="next-steps"></a>Nächste Schritte
[Benutzerdefinierte Richtlinien in Azure Active Directory B2C](active-directory-b2c-overview-custom.md)
