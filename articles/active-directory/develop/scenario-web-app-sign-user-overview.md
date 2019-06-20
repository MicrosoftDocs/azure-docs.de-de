---
title: Web-App, die Benutzer anmeldet (Übersicht) – Microsoft Identity Platform
description: Erfahren Sie, wie Sie eine Web-App erstellen, die Benutzer anmeldet (Übersicht).
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6ce534c6eeecba220fd829be829caa679df52055
ms.sourcegitcommit: 3ced637c8f1f24256dd6ac8e180fff62a444b03c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65833096"
---
# <a name="scenario-web-app-that-signs-in-users"></a>Szenario: Web-App, die Benutzer anmeldet

Erfahren Sie, wie Sie eine Web-App erstellen, die Benutzer mit Microsoft Identity Plattform anmeldet.

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Erste Schritte

Wenn Sie Ihre ersten portablen ASP.NET Core-Web-Apps erstellen möchten, die Benutzer anmelden, führen Sie die Schritte im folgenden Schnellstart aus:

> [!div class="nextstepaction"]
> [Schnellstart: ASP.NET Core-Web-App, die Benutzer anmeldet](quickstart-v2-aspnet-core-webapp.md)

Wenn Sie stattdessen ASP.NET verwenden möchten, können Sie das folgende Tutorial ausprobieren:

> [!div class="nextstepaction"]
> [Schnellstart: ASP.NET-Web-App für die Benutzeranmeldung](quickstart-v2-aspnet-webapp.md)

## <a name="overview"></a>Übersicht

Wenn Sie für Ihre Web-App eine Authentifizierung einsetzen, können Benutzer bei der App angemeldet werden. Dadurch kann Ihre App auf eingeschränkte Profilinformationen zugreifen und beispielsweise Funktionen für bestimmte Benutzer anpassen. Web-Apps authentifizieren Benutzer in einem Webbrowser. In diesem Szenario weist die Webanwendung den Browser des Benutzers an, diesen in Azure AD anzumelden. Azure AD gibt über den Browser des Benutzers eine Anmeldeantwort mit Benutzeransprüchen in einem Sicherheitstoken zurück. Bei der Anmeldung von Benutzern wird das Standardprotokoll [OpenID Connect](./v2-protocols-oidc.md) genutzt. Der Anmeldevorgang wird durch die Verwendung von [Middlewarebibliotheken](scenario-web-app-sign-user-app-configuration.md#libraries-used-to-protect-web-apps) noch vereinfacht.

![Web-App, die Benutzer anmeldet](./media/scenario-webapp/scenario-webapp-signs-in-users.svg)

In einem zweiten Schritt können Sie die Anwendung so konfigurieren, dass sie für den angemeldeten Benutzer Web-APIs aufruft. Bei diesem nächsten Schritt handelt es sich um ein anderes Szenario, das unter [Web-App ruft Web-APIs auf](scenario-web-app-call-api-overview.md) beschrieben wird.

> [!NOTE]
> Wenn Sie Ihre Web-App so konfigurieren, dass eine Anmeldung erforderlich ist, schützten Sie sie auf diese Weise. Außerdem wird ein Benutzertoken mithilfe von **Middlewarebilbliotheken** überprüft. Für dieses Szenario sind noch keine Microsoft Authentication Libraries (MSAL) erforderlich, mit denen ein Token zum Aufruf geschützter APIs abgerufen werden kann. Die Authentifizierungsbibliotheken werden erst im nächsten Szenario eingeführt, wenn die Web-App Web-APIs aufrufen muss.

## <a name="specifics"></a>Besonderheiten

- Bei der Anwendungsregistrierung müssen Sie eine oder – wenn die App an mehreren Standorten bereitgestellt wird – mehrere Antwort-URIs angeben. In einigen Fällen (etwa bei ASP.NET oder ASP.NET Core) müssen Sie „IDToken“ aktivieren. Abschließend sollten Sie einen Abmelde-URI einrichten, damit Ihre Anwendung entsprechend reagiert, wenn sich ein Benutzer abmeldet.
- Im Code für Ihre Anwendung müssen Sie die Autorität angeben, an die Ihre Web-App die Anmeldung delegiert. Sie sollten außerdem die Tokenüberprüfung (vor allem in ISV-Szenarios) anpassen.
- Webanwendungen unterstützen alle Kontotypen. Weitere Informationen finden Sie unter [Supported account types (Unterstützte Kontotypen)](v2-supported-account-types.md).

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [App-Registrierung](scenario-web-app-sign-user-app-registration.md)
