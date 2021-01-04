---
title: Entwicklung der Microsoft Identity Platform – Azure
description: Erfahren Sie mehr über Microsoft Identity Platform, eine Weiterentwicklung des Azure Active Directory-Identitätsdiensts (Azure AD) und der zugehörigen Entwicklerplattform.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: overview
ms.workload: identity
ms.date: 12/09/2019
ms.author: ryanwi
ms.reviewer: agirling, saeeda, benv, marsma
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: ab0057f55ddb5bf76f6fa74b0211b5ea5af24288
ms.sourcegitcommit: 4c89d9ea4b834d1963c4818a965eaaaa288194eb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/04/2020
ms.locfileid: "96608213"
---
# <a name="evolution-of-microsoft-identity-platform"></a>Entwicklung der Microsoft Identity Platform

Die [Microsoft Identity Platform](../develop/index.yml) ist eine Weiterentwicklung der Azure AD-Entwicklerplattform (Azure Active Directory). Sie ermöglicht Entwicklern das Erstellen von Anwendungen, mit denen Benutzer angemeldet werden, sowie das Abrufen von Token zum Aufrufen von APIs, etwa Microsoft Graph oder von Entwicklern erstellten APIs. Die Plattform umfasst einen Authentifizierungsdienst, Open-Source-Bibliotheken, Anwendungsregistrierung und -konfiguration (über ein Entwicklerportal und eine Anwendungs-API), eine vollständige Entwicklerdokumentation, Schnellstartbeispiele, Codebeispiele, Tutorials, Schrittanleitungen und weitere Inhalte für Entwickler. Microsoft Identity Platform unterstützt die branchenüblichen Protokolle, z.B. OAuth 2.0 und OpenID Connect.

Viele Entwickler haben bisher mit der Azure AD v1.0-Plattform gearbeitet, um (von Azure AD bereitgestellte) Geschäfts-, Schul- und Unikonten zu authentifizieren, indem sie Token vom Azure AD v1.0-Endpunkt angefordert, die Azure AD-Authentifizierungsbibliothek (Azure AD Authentication Library, ADAL), das Azure-Portal für die Anwendungsregistrierung und -konfiguration und die Microsoft Graph-API für die programmgesteuerte Anwendungskonfiguration verwendet haben.

Dank der einheitlichen Microsoft Identity Platform-Instanz (v2.0) können Sie einmalig Code schreiben und beliebige Microsoft-Identitäten in Ihrer Anwendung authentifizieren. Für verschiedene Plattformen wird die Verwendung der vollständig unterstützten Microsoft-Authentifizierungsbibliothek (Microsoft Authentication Library, MSAL) für die Identity Platform-Endpunkte empfohlen. MSAL ist einfach zu verwenden und bietet Benutzern hervorragende Funktionen für einmaliges Anmelden (SSO). Sie ist auf hohe Zuverlässigkeit und Leistung ausgelegt und wurde unter Verwendung von Microsoft Secure Development Lifecycle (SDL) entwickelt. Sie können Ihre Anwendung so konfigurieren, dass sie beim Aufrufen von APIs die inkrementelle Zustimmung nutzt. Dadurch wird eine Zustimmung für invasivere Geltungsbereiche erst angefordert, wenn dies durch die Anwendungsnutzung zur Laufzeit erforderlich wird.  MSAL unterstützt darüber hinaus Azure Active Directory B2C, sodass Ihre Kunden ihre bevorzugten Identitäten für soziale Netzwerke, Unternehmen oder lokale Konten nutzen können, um mit einmaligem Anmelden Zugriff auf Ihre Anwendungen und APIs zu erhalten.

Mit Microsoft Identity Platform dehnen Sie Ihre Reichweite auf folgende Benutzertypen aus:

- Geschäfts-, Schul- oder Unikonten (von Azure AD bereitgestellte Konten)
- Persönliche Konten (z. B. Outlook.com oder Hotmail.com)
- Kunden mit eigenen E-Mail-Adressen oder Social Media-Konten (z. B. LinkedIn, Facebook, Google) über MSAL und Azure AD B2C

Sie können das Azure-Portal zum Registrieren und Konfigurieren Ihrer Anwendung und die Microsoft Graph-API für die programmgesteuerte Anwendungskonfiguration verwenden.

Aktualisieren Sie Ihre Anwendung in Ihrem eigenen Tempo. Mit ADAL-Bibliotheken erstellte Anwendungen werden weiterhin unterstützt. Gemischte Portfolios aus Anwendungen, die mit ADAL- und mit MSAL-Bibliotheken erstellt wurden, werden ebenfalls unterstützt. So können Anwendungen, die die aktuelle ADAL- und die aktuelle MSAL-Bibliothek nutzen, eine portfolioübergreifende SSO-Funktionalität bieten. Gewährleistet wird dies durch den gemeinsamen Tokencache, den beide Bibliotheken nutzen. Anwendungen, die von ADAL auf MSAL aktualisiert werden, behalten den Benutzeranmeldestatus beim Upgrade bei.

## <a name="microsoft-identity-platform-experience"></a>Benutzeroberfläche von Microsoft Identity Platform

Die folgende Abbildung zeigt eine Übersicht der Microsoft-Identitätsbenutzeroberfläche. Dabei werden die App-Registrierungsbenutzeroberfläche, SDKs, Endpunkte und unterstützte Identitäten berücksichtigt.

![Aktuelle Microsoft Identity Platform](./media/about-microsoft-identity-platform/about-microsoft-identity-platform.svg)

### <a name="app-registration-experience"></a>Umgebung für die App-Registrierung

Die Seite **[App-Registrierungen](https://go.microsoft.com/fwlink/?linkid=2083908)** im Azure-Portal ist die zentrale Portaloberfläche für die Verwaltung aller in Microsoft Identity Platform integrierten Anwendungen. Wenn Sie bisher das App-Registrierungsportal verwendet haben, sollten Sie jetzt auf die App-Registrierungsoberfläche im Azure-Portal umsteigen.

Zur Integration von Azure AD B2C (für die Authentifizierung von Social Media- oder lokalen Identitäten) müssen Sie Ihre Anwendung bei einem Azure AD B2C-Mandanten registrieren. Diese Funktion ist ebenfalls Bestandteil des Microsoft Azure-Portals.

Verwenden Sie die [Anwendungs-API](/graph/api/resources/application), um Ihre in Microsoft Identity Platform integrierten Anwendungen programmgesteuert für die Authentifizierung beliebiger Microsoft-Identitäten zu konfigurieren.

### <a name="msal-libraries"></a>MSAL-Bibliotheken

Sie können die MSAL-Bibliothek zum Erstellen von Anwendungen nutzen, die sämtliche Microsoft-Identitäten authentifizieren. Die MSAL-Bibliotheken in .NET und JavaScript sind allgemein verfügbar. MSAL-Bibliotheken für iOS und Android befinden sich in der Vorschauphase und sind für die Verwendung in einer Produktionsumgebung geeignet. Wir bieten für MSAL-Bibliotheken in der Vorschau denselben professionellen Produktionssupport wie wir für allgemein verfügbare MSAL- und ADAL-Versionen.

Sie können die MSAL-Bibliotheken auch verwenden, um Ihre Anwendung in Azure AD B2C zu integrieren.

### <a name="microsoft-identity-platform-endpoint"></a>Microsoft Identity Platform-Endpunkt

Der Microsoft Identity Platform (v2.0)-Endpunkt ist OIDC-zertifiziert. Er unterstützt Microsoft-Authentifizierungsbibliotheken (MSAL) und andere standardkonforme Bibliotheken. Er implementiert visuell lesbare Geltungsbereiche gemäß Branchenstandards.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr in der [Dokumentation zur Microsoft Identity Platform](../develop/index.yml).
