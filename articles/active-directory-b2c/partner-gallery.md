---
title: ISV-Partnerkatalog für Azure AD B2C
titleSuffix: Azure AD B2C
description: Erfahren Sie, wie Sie mit unseren ISV-Partnern zusammenarbeiten können, um die Endbenutzererfahrung auf Ihre Anforderungen abzustimmen. Unser Partnernetzwerk erweitert unsere Lösungskapazitäten, um MFA, sichere Kundenauthentifizierung und rollenbasierte Zugriffssteuerung zu aktivieren sowie Betrug durch Identitätsüberprüfung und -nachweis zu bekämpfen.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/11/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 5012e90aae316897454ffc7933efc88d83afdf6e
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98893377"
---
# <a name="azure-active-directory-b2c-isv-partners"></a>Azure Active Directory B2C-ISV-Partner

Unser ISV-Partnernetzwerk erweitert unsere Lösungsmöglichkeiten, damit Sie nahtlose Endbenutzererfahrungen schaffen können. Mit Azure AD B2C können Sie ISV-Partner integrieren, um MFA-Methoden (Multi-Factor Authentication, mehrstufige Authentifizierung), die rollenbasierte Zugriffssteuerung und Identitätsüberprüfungen und -nachweise zu aktivieren, die Sicherheit durch Boterkennung und Betrugsschutz zu verbessern und die Anforderungen von PSD2 (Payment Services Directive 2) und SCA (Secure Customer Authentication) einzuhalten. Mithilfe unserer detaillierten exemplarischen Vorgehensweisen erfahren Sie, wie Sie Apps mit den Lösungen von ISV-Partnern integrieren können.

>[!NOTE]
>Auf der [Website der Azure Active Directory B2C-Community auf GitHub](https://azure-ad-b2c.github.io/azureadb2ccommunity.io/) finden Sie auch Beispiele benutzerdefinierter Richtlinien aus der Community.

## <a name="identity-verification-and-proofing"></a>Identitätsüberprüfung und -nachweis

Über Azure AD B2C-Partner können Kunden Identitätsüberprüfungen und -nachweise für ihre Endbenutzer aktivieren, bevor diesen die Kontoregistrierung oder der Zugriff erlaubt wird. Für die Überprüfung und den Nachweis der Identität können Dokumente, Informationen und Livetests geprüft werden.

Dieses allgemeine Architekturdiagramm veranschaulicht den Flow.

![Diagramm des Flows für Identitätsüberprüfungen](./media/partner-gallery/third-party-identity-proofing.png)

Microsoft arbeitet für Identitätsüberprüfungen und -nachweise mit den folgenden ISVs zusammen.

| ISV-Partner | Beschreibung und exemplarische Vorgehensweisen für die Integration |
|:-------------------------|:--------------|
|![Screenshot eines Experian-Logos.](./media/partner-gallery/experian-logo.png) | [Experian](./partner-experian.md) ist ein Identitätsüberprüfungs- und -nachweisanbieter, der Risikobewertungen basierend auf Benutzerattributen durchführt, um Betrug zu verhindern. |
|![Screenshot eines IDology-Logos.](./media/partner-gallery/idology-logo.png) | [IDology](./partner-idology.md) ist ein Anbieter von Lösungen für Identitätsprüfung und -nachweis mit Lösungen für u. a. ID-Verifizierung, Betrugsprävention und Compliance.|
|![Screenshot eines Jumio-Logos.](./media/partner-gallery/jumio-logo.png) | [Jumio](./partner-jumio.md) ist ein ID-Überprüfungsdienst, der eine automatische ID-Überprüfung in Echtzeit und den Schutz von Kundendaten ermöglicht. |
| ![Screenshot eines LexisNexis-Logos.](./media/partner-gallery/lexisnexis-logo.png) | [LexisNexis](./partner-lexisnexis.md) ist ein Profilerstellungs- und Identitätsüberprüfungsanbieter, der die Benutzeridentifikation überprüft und eine umfassende Risikobewertung basierend auf dem Gerät des Benutzers bereitstellt. |
| ![Screenshot eines Onfido-Logos](./media/partner-gallery/onfido-logo.png) | [Onfido](./partner-onfido.md) ist eine Lösung zur Überprüfung von Dokument-ID und Gesichtsbiometrie, mit der Unternehmen Ihre Anforderungen hinsichtlich *Know Your Customer* und Identitätsprüfung in Echtzeit erfüllen können.  |

## <a name="mfa-and-passwordless-authentication"></a>MFA und kennwortlose Authentifizierung

Microsoft arbeitet für MFA und kennwortlose Authentifizierung mit den folgenden ISVs zusammen.

| ISV-Partner | Beschreibung und exemplarische Vorgehensweisen für die Integration |
|:-------------------------|:--------------|
| ![Screenshot eines HYPR-Logos](./media/partner-gallery/hypr-logo.png) | [HYPR](./partner-hypr.md) ist ein Anbieter für die kennwortlose Authentifizierung, bei der Kennwörter durch eine Verschlüsselung mit öffentlichen Schlüsseln ersetzt werden. Dadurch lassen sich Betrug, Phishing und die Wiederverwendung von Anmeldeinformationen vermeiden. |
| ![Screenshot eines itsme-Logos](./media/partner-gallery/itsme-logo.png) | [itsme](./partner-itsme.md) ist eine eiDAS-konforme digitale ID-Lösung (Electronic Identification, Authentication and Trust Services), die es Benutzern ermöglicht, sich ohne Kartenleser, Kennwörter, zweistufige Authentifizierung und mehrere PIN-Codes sicher anzumelden. |
|![Screenshot eines Keyless-Logos.](./media/partner-gallery/keyless-logo.png) | [Keyless](./partner-keyless.md) ist ein Anbieter der kennwortlosen Authentifizierung, der die Authentifizierung in Form eines biometrischen Gesichtsscans ermöglicht, um Betrug, Phishing und die Wiederverwendung von Anmeldeinformationen zu vermeiden.
| ![Screenshot eines Nevis-Logos](./media/partner-gallery/nevis-logo.png) | [Nevis](./partner-nevis.md) ermöglicht die kennwortlose Authentifizierung und bietet mit der Nevis Access-App eine vollständig markentreue Endbenutzerumgebung an, die auf Mobilgeräte abzielt. So wird eine starke Kundenauthentifizierung bereitgestellt, die den Transaktionsanforderungen der Zahlungsdiensterichtlinie 2 (Payment Services Directive 2, PSD2) entspricht. |
| ![Screenshot eines Trusona-Logos](./media/partner-gallery/trusona-logo.png) | Die Integration von [Trusona](./partner-trusona.md) hilft Ihnen bei der sicheren Anmeldung und ermöglicht kennwortlose Authentifizierung, mehrstufige Authentifizierung (MFA) und das Scannen digitaler Lizenzen. |
| ![Screenshot eines Twilio-Logos](./media/partner-gallery/twilio-logo.png) | Die [Twilio Verify-App](./partner-twilio.md) bietet mehrere Lösungen, um die mehrstufige Authentifizierung (MFA) durch SMS OTP (One-Time-Password, Einmalkennwort), TOTP (Time Based One-Time-Password, zeitbasiertes Einmalkennwort) und Push-Benachrichtigungen zu aktivieren und die SCA-Anforderungen der PSD2 zu erfüllen. |
| ![Screenshot eines TypingDNA-Logos](./media/partner-gallery/typingdna-logo.png) | [TypingDNA](./partner-typingdna.md) ermöglicht eine strenge Kundenauthentifizierung durch das Analysieren der Tastatureingabemuster eines Benutzers. So können Unternehmen eine „unsichtbare“ MFA implementieren und die SCA-Anforderungen der PSD2 einhalten. |
| ![Screenshot eines WhoIAm-Logos](./media/partner-gallery/whoiam-logo.png) | [WhoIAM](./partner-whoiam.md) ist eine BRIMS-Anwendung (Branded Identity Management System), mit der Organisationen ihre Benutzerbasis per Sprachanruf, SMS und E-Mail überprüfen können. |

## <a name="role-based-access-control"></a>Rollenbasierte Zugriffssteuerung 
 
Microsoft arbeitet für die rollenbasierte Zugriffssteuerung mit den folgenden ISVs zusammen.

| ISV-Partner | Beschreibung und exemplarische Vorgehensweisen für die Integration |
|:-------------------------|:--------------|
| ![Screenshot eines N8Identity-Logos](./media/partner-gallery/n8identity-logo.png) | [N8Identity](./partner-n8identity.md) ist eine Governanceplattform vom Typ Identity-as-a-Service, die eine Lösung für die Migration von Kundenkonten und die Verwaltung von Kundendienstanforderungen (Customer Service Requests, CSR) in Microsoft Azure bietet. |
| ![Screenshot eines Saviynt-Logos](./media/partner-gallery/saviynt-logo.png) | Die cloudnative Plattform [Saviynt](./partner-Saviynt.md) verwendet intelligente Analysen und ermöglicht eine anwendungsübergreifende Integration, um die IT-Modernisierung zu optimieren und Sicherheit, Governance und Compliance zu verbessern. |

## <a name="security"></a>Sicherheit

Microsoft arbeitet für die Sicherheit mit den folgenden ISVs zusammen.

| ISV-Partner | Beschreibung und exemplarische Vorgehensweisen für die Integration |
|:-------------------------|:--------------|
| ![Screenshot eines Arkose Labs-Logos](./media/partner-gallery/arkose-logo.png) | [Arkose Labs](./partner-arkose-labs.md) ist ein Anbieter von Lösungen zur Betrugsabwehr, der Organisationen beim Schutz gegen Botangriffe, Angriffe zur Kontoübernahme und betrügerische Kontoeröffnungen hilft. |
| ![Screenshot eines Ping-Logos](./media/partner-gallery/ping-logo.png) | [Ping Identity](./partner-ping-identity.md) ermöglicht einen sicheren Hybridzugriff auf lokale Legacyanwendungen über mehrere Clouds hinweg. |
| ![Screenshot eines Strata-Logos](./media/partner-gallery/strata-logo.png) | [Strata](./partner-strata.md) bietet sicheren Hybridzugriff auf lokale Anwendungen, indem einheitliche Zugriffsrichtlinien erzwungen und Identitäten synchron gehalten werden und der Umstieg von Anwendungen von älteren Identitätsanbietern auf die standardbasierte Authentifizierung und Zugriffssteuerung von Azure AD B2C vereinfacht wird. |
| ![Screenshot eines Zscaler-Logos](./media/partner-gallery/zscaler-logo.png) | [Zscaler](./partner-zscaler.md) ermöglicht einen richtlinienbasierten, sicheren Zugriff auf private Anwendungen und Ressourcen ohne die Kosten, den Aufwand oder die Sicherheitsrisiken eines VPN. |

## <a name="additional-information"></a>Zusätzliche Informationen

- [Benutzerdefinierte Richtlinien in Azure AD B2C](./custom-policy-overview.md)

- [Erste Schritte mit benutzerdefinierten Richtlinien in Azure AD B2C](./custom-policy-get-started.md?tabs=applications)

## <a name="next-steps"></a>Nächste Schritte

Wählen Sie in den Tabellen einen Partner aus, um zu erfahren, wie seine Lösung und Azure AD B2C integriert werden können.
