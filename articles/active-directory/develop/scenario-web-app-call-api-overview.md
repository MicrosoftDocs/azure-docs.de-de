---
title: Web-App, die Web-APIs aufruft (Übersicht) – Microsoft Identity Platform
description: Erfahren Sie, wie Sie eine Web-App erstellen, die Web-APIs aufruft (Übersicht)
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
ms.openlocfilehash: ce45f11a697b72ebdd0fe01166a70e7b47aa8e9f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65080290"
---
# <a name="scenario-web-app-that-calls-web-apis"></a>Szenario: Web-App, die Web-APIs aufruft

Erfahren Sie, wie Sie eine Web-App erstellen, die Benutzer bei der Microsoft Identity Platform anmeldet und Web-APIs im Namen des angemeldeten Benutzers aufruft.

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

In diesem Szenario wird angenommen, dass Sie mit dem folgenden Szenario vertraut sind:

> [!div class="nextstepaction"]
> [Web-App, die Benutzer anmeldet](scenario-web-app-sign-user-overview.md)

## <a name="overview"></a>Übersicht

Fügen Sie eine Authentifizierung zu Ihrer Web-App hinzu, die daher Benutzer anmelden und eine Web-API im Namen des angemeldeten Benutzers aufrufen kann.

![Web-App, die Web-APIs aufruft](./media/scenario-webapp/web-app.svg)

Web-Apps, die Web-APIs aufrufen:

- sind vertrauliche Clientanwendungen.
- Dies ist der Grund für die Registrierung eines Geheimnisses (Anwendungskennwort oder -zertifikat) in Azure AD. Dieses Geheimnis wird während des Aufrufs von Azure AD zum Abrufen eines Tokens übergeben.

## <a name="specifics"></a>Besonderheiten

> [!NOTE]
> Beim Hinzufügen von Anmeldedaten zu einer Web-App werden keine MSAL-Bibliotheken verwendet, um die Web-App zu schützen. Bibliotheken werden mithilfe von Bibliotheken namens Middleware geschützt. Dies war das Ziel des vorherigen Szenarios [Anmelden von Benutzern bei einer Web-App](scenario-web-app-sign-user-overview.md).
>
> Beim Aufrufen von Web-APIs über eine Web-App benötigen Sie Zugriffstoken für diese Web-APIs. Sie können MSAL-Bibliotheken zum Abrufen dieser Token verwenden.

Das End-to-End-Szenario hat für Entwickler daher bestimmte Aspekte:

- Während der [Anwendungsregistrierung](scenario-web-app-call-api-app-registration.md) müssen Sie gegebenenfalls mehrere (falls Sie Ihre App in mehreren Orten bereitstellen möchten) Antwort-URLs, Geheimnisse oder Zertifikate bereitstellen, die für Azure AD freigegeben sind.
- Die [Anwendungskonfiguration](scenario-web-app-call-api-app-configuration.md) muss Clientanmeldeinformationen bereitstellen, die wie während der Anwendungsregistrierung für Azure AD freigegeben sind.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [App-Registrierung](scenario-web-app-call-api-app-registration.md)
