---
title: Übersicht über Microsoft Identity Platform – Azure
titleSuffix: Microsoft identity platform
description: Erfahren Sie mehr über die Komponenten der Microsoft Identity Platform und darüber, wie Sie mithilfe dieser Komponenten Unterstützung für die Identitäts- und Zugriffsverwaltung (Identity and Access Management, IAM) in Ihre Anwendungen integrieren.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: overview
ms.workload: identity
ms.date: 07/09/2020
ms.author: ryanwi
ms.reviewer: agirling, saeeda, benv
ms.custom: identityplatformtop40, contperf-fy21q2
ms.openlocfilehash: e5a9292097933ff35eb8b77445f07f10def6e5c3
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97029616"
---
# <a name="what-is-the-microsoft-identity-platform"></a>Was ist die Microsoft Identity Platform?

Die Microsoft Identity Platform unterstützt Sie beim Erstellen von Anwendungen, bei denen sich Ihre Benutzer und Kunden mit ihren Microsoft-Identitäten oder Social Media-Konten anmelden können. Außerdem können Sie autorisierten Zugriff auf Ihre eigenen APIs oder Microsoft-APIs wie Microsoft Graph gewähren.

Die Microsoft Identity Platform besteht aus mehreren Komponenten:

- **Standardkonformer OAuth 2.0- und OpenID Connect-Authentifizierungsdienst**, der Entwicklern die Authentifizierung mehrerer Identitätstypen ermöglicht, einschließlich:
  - Über Azure AD bereitgestellte Geschäfts-, Schul- oder Unikonten
  - Persönliche Microsoft-Konten wie z. B. Skype, Xbox und Outlook.com
  - Social Media- oder lokale Konten bei Verwendung von Azure AD B2C
- **Open-Source-Bibliotheken**: Microsoft Authentication Libraries (MSAL) und Unterstützung für andere standardkonforme Bibliotheken
- **Verwaltungsportal für Anwendungen**: Eine Registrierungs- und Konfigurationsumgebung im Azure-Portal, die zusammen mit den anderen Azure-Verwaltungsfunktionen genutzt werden kann.
- **Anwendungskonfigurations-API und PowerShell**: Programmgesteuerte Konfiguration Ihrer Anwendungen über die Microsoft Graph-API und PowerShell, um Ihre DevOps-Aufgaben automatisieren zu können.
- **Entwicklerinhalte**: Technische Dokumentation einschließlich Schnellstarts, Tutorials, Schrittanleitungen und Codebeispielen.

Für Entwickler bietet die Microsoft Identity Platform die Integration moderner Innovationen in den Identitäts- und Sicherheitsbereich. Beispiele dafür sind kennwortlose Authentifizierung, Step-up-Authentifizierung und bedingter Zugriff. Sie müssen diese Funktionalität nicht selbst implementieren: In Microsoft Identity Platform integrierte Anwendungen nutzen solche Innovationen nativ.

Dank der Microsoft Identity Platform müssen Sie Code nur einmal schreiben, um alle Benutzer zu erreichen. Sie können eine App einmalig erstellen und sie plattformübergreifend nutzen lassen, oder eine App erstellen, die sowohl als Client als auch als Ressourcenanwendung (API) fungiert.

## <a name="getting-started"></a>Erste Schritte

Wählen Sie das gewünschte [Anwendungsszenario](authentication-flows-app-scenarios.md) aus. Jeder Szenariopfad beginnt mit einer Übersicht und enthält Links zu einem Schnellstart, um Ihnen den Einstieg zu erleichtern:

- [Single-Page-Webanwendung (Single-Page-App, SPA)](scenario-spa-overview.md)
- [Web-App, die Benutzer anmeldet](scenario-web-app-sign-user-overview.md)
- [Web-App, die Web-APIs aufruft](scenario-web-app-call-api-overview.md)
- [Geschützte Web-API](scenario-protected-web-api-overview.md)
- [Web-API, die Web-APIs aufruft](scenario-web-api-call-api-overview.md)
- [Desktop-App](scenario-desktop-overview.md)
- [Daemon-App](scenario-daemon-overview.md)
- [Mobile App](scenario-mobile-overview.md)

Wenn Sie mit der Microsoft Identity Platform arbeiten, um Authentifizierungs- und Autorisierungsfunktionen in Ihre Apps zu integrieren, können Sie die folgende Abbildung als Referenz verwenden. Darin sind die gängigsten App-Szenarien und deren Identitätskomponenten dargestellt. Wählen Sie das Bild aus, um es in voller Größe anzuzeigen.

[![Metro-Karte mit mehreren Anwendungsszenarien in Microsoft Identity Platform](./media/v2-overview/application-scenarios-identity-platform.png)](./media/v2-overview/application-scenarios-identity-platform.svg#lightbox)

## <a name="learn-authentication-concepts"></a>Informationen zu Authentifizierungskonzepten

In den folgenden empfohlenen Artikeln erfahren Sie, wie die Kernauthentifizierungs- und Azure AD-Konzepte Anwendung auf die Microsoft Identity Platform finden:

- [Authentifizierungsszenarien für Azure AD](./authentication-vs-authorization.md)
- [Anwendungs- und Dienstprinzipale](app-objects-and-service-principals.md)
- [Zielgruppen](v2-supported-account-types.md)
- [Berechtigungen und Einwilligung](v2-permissions-and-consent.md)
- [ID-Token](id-tokens.md)
- [Zugriffstoken](access-tokens.md)
- [Authentifizierungsflows und Anwendungsszenarien](authentication-flows-app-scenarios.md)

## <a name="more-identity-and-access-management-options"></a>Weitere Identitäts- und Zugriffsverwaltungsoptionen

[Azure AD B2C](../../active-directory-b2c/overview.md): Erstellen Sie kundenorientierte Anwendungen, bei denen sich Ihre Benutzer mit ihren Konten sozialer Netzwerke wie Facebook oder Google oder mit einer E-Mail-Adresse und einem Kennwort anmelden können.

[Azure AD B2B](../external-identities/what-is-b2b.md): Laden Sie externe Benutzer als „Gastbenutzer“ in Ihren Azure AD-Mandanten ein, und weisen Sie ihnen Berechtigungen für die Autorisierung zu, während sie für die Authentifizierung ihre vorhandenen Anmeldeinformationen verwenden können.

[Azure Active Directory für Entwickler (v1.0)](../azuread-dev/v1-overview.md): Ist hier für Entwickler mit vorhandenen Apps aufgeführt, für die der ältere v1.0-Endpunkt genutzt wird. Verwenden Sie v1.0 **nicht** für neue Projekte.

## <a name="next-steps"></a>Nächste Schritte

Wenn Sie über ein Azure-Konto verfügen, haben Sie bereits Zugriff auf einen Azure Active Directory-Mandanten. Die meisten Microsoft Identity Platform-Entwickler benötigen jedoch einen eigenen Azure AD-Mandanten, einen Entwicklungsmandanten, den sie beim Entwickeln von Anwendungen verwenden können.

Erfahren Sie, wie Sie Ihren eigenen Mandanten zur Verwendung beim Erstellen von Anwendungen erstellen:

[Schnellstart: Einrichten eines Azure AD-Mandanten](quickstart-create-new-tenant.md)
