---
title: JavaScript-SPA-Szenarioübersicht – Microsoft Identity Platform
description: Erfahren Sie, wie Sie eine Single-Page-Webanwendung (Szenarioübersicht) erstellen, die Microsoft Identity Platform integriert.
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 07a21e83f304f3e1acc0ed4033d832dd8e901ac9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65080298"
---
# <a name="scenario-single-page-application"></a>Szenario: Einseitige Anwendung

Erfahren Sie, wie Sie eine Single-Page-Webanwendung (SPA) erstellen.

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Erste Schritte

Sie können Ihre erste Anwendung erstellen, indem Sie die Schritte des JavaScript SPA-Schnellstarts ausführen:

> [!div class="nextstepaction"]
> [Schnellstart: Single-Page-Webanwendung](./quickstart-v2-javascript.md)

## <a name="overview"></a>Übersicht

Viele moderne Webanwendungen werden als clientseitige Single-Page-Webanwendung erstellt, die mithilfe von JavaScript oder SPA-Frameworks wie Angular, Vue.js oder React.js geschrieben werden. Diese Anwendungen werden in einem Webbrowser ausgeführt und haben andere Authentifizierungsmerkmale als herkömmliche serverseitige Webanwendungen. Microsoft Identity Platform ermöglicht Single-Page-Webanwendungen mithilfe des [impliziten OAuth 2.0-Flusses](./v2-oauth2-implicit-grant-flow.md) das Anmelden von Benutzern und Abrufen von Token für den Zugriff auf Back-End-Dienste oder Web-APIs. Durch den impliziten Flow wird es der Anwendung ermöglicht, ID-Token zum Darstellen des authentifizierten Benutzers und für den Aufruf von geschützten APIs erforderliche Zugriffstoken abzurufen.

![Single-Page-Webanwendungen](./media/scenarios/spa-app.svg)

Dieser Authentifizierungsablauf umfasst keine Anwendungsszenarios, die plattformübergreifende JavaScript-Frameworks wie beispielsweise Electron, React-Native usw. verwenden. Für die Interaktion mit den nativen Plattformen benötigen diese weitere Funktionen.

## <a name="specifics"></a>Besonderheiten

Die folgenden Aspekte sind erforderlich, um dieses Szenario für Ihre Anwendung zu aktivieren:

* Die Anwendungsregistrierung bei Azure AD umfasst das Aktivieren des impliziten Flows und Festlegen einer Umleitungs-URL, an die Token zurückgegeben werden.
* Anwendungskonfiguration mit den registrierten Anwendungseigenschaften, wie z. B. der Anwendungs-ID.
* Verwenden der MSAL-Bibliothek zum Anmelden des Authentifizierungsflusses und Abrufen von Token.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [App-Registrierung](scenario-spa-app-registration.md)
