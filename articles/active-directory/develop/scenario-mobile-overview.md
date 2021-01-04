---
title: Erstellen einer mobilen App, die Web-APIs aufruft | Azure
titleSuffix: Microsoft identity platform | Azure
description: Erfahren Sie, wie Sie eine mobile App erstellen, die Web-APIs aufruft (Übersicht)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviewer: brandwe
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 853ab82ab40cc7ebee1c74feaf6aad87adf39ece
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94443108"
---
# <a name="scenario-mobile-application-that-calls-web-apis"></a>Szenario: Mobile App, die Web-APIs aufruft

Hier erfahren Sie, wie Sie eine mobile App erstellen, die Web-APIs aufruft.

## <a name="getting-started"></a>Erste Schritte

Erstellen Sie, falls noch nicht geschehen, Ihre erste App, indem Sie einen der folgenden Schnellstarts abschließen:

- [Schnellstart: Abrufen eines Tokens und Aufrufen der Microsoft Graph-API von einer Android-App aus](./quickstart-v2-android.md)
- [Schnellstart: Abrufen eines Tokens und Aufrufen der Microsoft Graph-API von einer iOS-App aus](./quickstart-v2-ios.md)
- [Schnellstart: Abrufen eines Tokens und Aufrufen der Microsoft Graph-API von einer Xamarin iOS- und Android-App](https://github.com/Azure-Samples/active-directory-xamarin-native-v2) (GitHub)

## <a name="overview"></a>Übersicht

Eine personalisierte und übersichtliche Benutzeroberfläche ist für mobile Apps äußerst wichtig.  Microsoft Identity Platform ermöglicht Entwicklern von mobilen Apps, diese Oberfläche für iOS- und Android-Benutzer zu schaffen. Ihre Anwendung kann Benutzer von Azure Active Directory (Azure AD), Benutzer eines persönlichen Microsoft-Kontos und Azure AD B2C-Benutzer anmelden. Sie kann außerdem Token erwerben, um in ihrem Auftrag eine Web-API aufzurufen. Zum Implementieren dieser Abläufe verwenden wir die Microsoft-Authentifizierungsbibliothek (Microsoft Authentication Library, MSAL). MSAL implementiert den [OAuth2.0-Autorisierungscodeablauf](v2-oauth2-auth-code-flow.md), den Branchenstandard.

![Daemon-Apps](./media/scenarios/mobile-app.svg)

Aspekte für mobile Apps:

- **Benutzerfreundlichkeit ist der Schlüssel:** Ermöglichen Sie es Benutzern, den Wert Ihrer App zu erkennen, bevor Sie sie auffordern, sich anzumelden. Fordern Sie nur die erforderlichen Berechtigungen an.
- **Unterstützen aller Benutzerkonfigurationen:** Viele geschäftliche Benutzer mobiler Geräte müssen Richtlinien für den bedingten Zugriff und Gerätekonformitätsrichtlinien befolgen. Stellen Sie sicher, dass diese wichtigen Szenarios unterstützt werden.
- **Implementieren von einmaligem Anmelden (SSO):** Mit MSAL und Microsoft Identity Platform können Sie einmaliges Anmelden (SSO) über den Gerätebrowser oder Microsoft Authenticator (und das Intune-Unternehmensportal unter Android) aktivieren.
- **Implementieren des Modus für gemeinsam genutzte Geräte:** Entwerfen Sie Ihre Anwendung so, dass sie in Szenarios mit gemeinsam genutzten Geräten verwendet werden kann, z. B. in Krankenhäusern, in der Fertigung, im Einzelhandel und im Finanzwesen. [Weitere Informationen zum Unterstützen des Modus für gemeinsam genutzte Geräte](msal-shared-devices.md)

## <a name="specifics"></a>Besonderheiten

Berücksichtigen Sie die folgenden Aspekte, wenn Sie eine mobile App auf Microsoft Identity Platform erstellen:

- Abhängig von der Plattform sind möglicherweise bei der ersten Anmeldung Benutzerinteraktionen erforderlich. Beispielsweise müssen Apps unter iOS eine Benutzeraktion anzeigen, wenn sie zum ersten Mal einmaliges Anmelden (Single Sign-On, SSO) über Microsoft Authenticator (und das Intune-Unternehmensportal unter Android) verwenden.
- Unter iOS und Android verwendet MSAL möglicherweise einen externen Browser zum Anmelden von Benutzern. Der externe Browser wird möglicherweise über Ihrer App angezeigt.
- Verwenden Sie niemals einen geheimen Schlüssel in einer mobilen Anwendung. In diesen Anwendungen sind Geheimnisse für alle Benutzer zugänglich.

## <a name="recommended-reading"></a>Empfohlene Literatur

[!INCLUDE [recommended-topics](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="next-steps"></a>Nächste Schritte

Fahren Sie mit dem nächsten Artikel in diesem Szenario fort: [App-Registrierung](scenario-mobile-app-registration.md).
