---
title: Mobile App, die Web-APIs aufruft – Übersicht | Microsoft Identity Platform
description: Erfahren Sie, wie Sie eine mobile App erstellen, die Web-APIs aufruft (Übersicht)
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: df5b53ccdcd33e2542834769baff5b759ff7b170
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65962364"
---
# <a name="scenario-mobile-application-that-calls-web-apis"></a>Szenario: Mobile App, die Web-APIs aufruft

Erfahren Sie alles über das Erstellen einer mobilen App, die Web-APIs aufruft.

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Erste Schritte

Erstellen Sie Ihre erste mobile App und probieren Sie einen Schnellstart aus.

> [!div class="nextstepaction"]
> [Schnellstart: Abrufen eines Tokens und Aufrufen der Microsoft Graph-API von einer Android-App aus](./quickstart-v2-android.md)
>
> [Schnellstart: Abrufen eines Tokens und Aufrufen der Microsoft Graph-API von einer iOS-App aus](./quickstart-v2-ios.md)
>
> [Schnellstart: Abrufen eines Tokens und Aufrufen der Microsoft Graph-API von einer Xamarin.iOS- und Xamarin.Android-App aus](https://github.com/Azure-Samples/active-directory-xamarin-native-v2)

## <a name="overview"></a>Übersicht

Eine personalisierte und übersichtliche Benutzeroberfläche ist für mobile Apps äußerst wichtig.  Microsoft Identity Platform ermöglicht Entwicklern von mobilen Apps, diese Oberfläche für iOS- und Android-Benutzer zu schaffen. Ihre Anwendung kann Benutzer von Azure Active Directory (Azure AD), Benutzer eines persönlichen Microsoft-Kontos und Azure AD B2C-Benutzer anmelden und Token abrufen, um eine Web-API in ihrem Auftrag abzurufen. Mithilfe der Microsoft Authentication Library (MSAL), die den [Flow für den OAuth 2.0-Autorisierungscode](v2-oauth2-auth-code-flow.md) nach Branchenstandard implementiert, können Sie diese Flows implementieren.

![Daemon-Apps](./media/scenarios/mobile-app.svg)

Aspekte für mobile Apps:

- **Benutzerfreundlichkeit ist der Schlüssel:** Zeigen Sie Benutzern, noch bevor diese zur Anmeldung aufgefordert werden, welche Vorteile Ihre App bietet, und fordern Sie nur die tatsächlich erforderlichen Berechtigungen an.
- **Unterstützen aller Benutzerkonfigurationen:** Für viele Geschäftsbenutzer mobiler Apps gelten Richtlinien für bedingten Zugriff sowie Gerätekonformitätsrichtlinien. Stellen Sie sicher, dass diese wichtigen Szenarios unterstützt werden.
- **Implementieren von einmaligem Anmelden (SSO):** Mit MSAL und Microsoft Identity Platform ist es einfach, einmaliges Anmelden (SSO) über den Gerätebrowser oder Microsoft Authenticator (und das Intune-Unternehmensportal unter Android) zu aktivieren.

## <a name="specifics"></a>Besonderheiten

Berücksichtigen Sie die folgenden Aspekte, wenn Sie eine mobile App auf Microsoft Identity Platform erstellen:

- Abhängig von der Plattform sind möglicherweise bei der ersten Anmeldung Benutzerinteraktionen erforderlich. Beispielsweise müssen Apps unter iOS eine Benutzeraktion anzeigen, wenn einmaliges Anmelden (Single Sign-On, SSO) zum ersten Mal über Microsoft Authenticator (und das Intune-Unternehmensportal unter Android) verwendet wird.
- Unter iOS und Android verwendet MSAL möglicherweise einen (im Vordergrund Ihrer App angezeigten) externen Browser zum Anmelden von Benutzern. Sie können die Konfiguration anpassen, sodass stattdessen In-App-Webansichten verwendet werden.
- Verwenden Sie niemals einen geheimen Schlüssel in einer mobilen Anwendung, weil alle Benutzer darauf zugreifen könnten.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [App-Registrierung](scenario-mobile-app-registration.md)
