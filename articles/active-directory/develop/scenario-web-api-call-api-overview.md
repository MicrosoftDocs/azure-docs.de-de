---
title: Web-API, die Downstream-Web-APIs aufruft (Übersicht) – Microsoft Identity Platform
description: Erfahren Sie, wie Sie eine Web-API erstellen, die Downstream-Web-APIs aufruft (Übersicht).
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
ms.openlocfilehash: 497134b7f3cc535f7b3f180db13cd04ef56787db
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65080170"
---
# <a name="scenario-web-api-that-calls-web-apis"></a>Szenario: Web-API, die Web-APIs aufruft

Erfahren Sie, wie Sie eine Web-API erstellen können, die Web-APIs aufruft.

## <a name="prerequisites"></a>Voraussetzungen

Dieses Szenario – eine geschützte Web-API, die Web-APIs aufruft – baut auf dem Szenario zum Schützen einer Web-API auf. Weitere Informationen zu diesem grundlegenden Szenario finden Sie unter [Scenario: Protected web API (Szenario: Geschützte Web-API)](scenario-protected-web-api-overview.md).

## <a name="overview"></a>Übersicht

- Ein Client (Web-, Desktop-, Mobil- oder Single-Page-Webanwendung), der nicht auf dem nachfolgenden Diagramm dargestellt ist, ruft eine geschützte Web-API auf und stellt in seinem HTTP-Header „Authorization“ (Autorisierung) ein JWT-Bearertoken bereit.
- Die geschützte Web-API überprüft das Token und verwendet die MSAL-Methode `AcquireTokenOnBehalfOf`, um (von Azure AD) ein weiteres Token anzufordern, damit dieses anstelle des Benutzers selbst eine zweite Web-API (die Downstream-Web-API genannt) aufrufen kann.
- Die geschützte Web-API verwendet dieses Token, um eine Downstream-API aufzurufen. Sie kann zudem später `AcquireTokenSilent` aufrufen, um Token für andere Downstream-APIs anzufordern (jedoch immer noch anstelle des gleichen Benutzers). Durch `AcquireTokenSilent` wird das Token bei Bedarf aktualisiert.

![Web-API, die eine Web-API aufruft](media/scenarios/web-api.svg)

## <a name="specifics"></a>Besonderheiten

Der auf die API-Berechtigungen bezogene Teil der App-Registrierung ist klassisch. Die Anwendungskonfiguration beinhaltet die Verwendung des OBO-Flusses (On-Behalf-Of) von OAuth 2.0, um das JWT-Bearertoken gegen ein Token für eine Downstream-API auszutauschen. Dieses Token wird zum Tokencache hinzugefügt, wo es in den Controllern der Web-API verfügbar ist, und kann automatisch ein Token abrufen, um Downstream-APIs aufzurufen.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [App-Registrierung](scenario-web-api-call-api-app-registration.md)
