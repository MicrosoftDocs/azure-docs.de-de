---
title: Erstellen einer Web-API, die Web-APIs aufruft – Microsoft Identity Platform | Azure
description: Erfahren Sie, wie Sie eine Web-API erstellen, die Downstream-Web-APIs aufruft (Übersicht).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: ee9d879849a94ac255a0967ad4fbc762417f8cd4
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94442649"
---
# <a name="scenario-a-web-api-that-calls-web-apis"></a>Szenario: Web-API, die Web-APIs aufruft

Hier erfahren Sie, wie Sie eine Web-API erstellen, die Web-APIs aufruft.

## <a name="prerequisites"></a>Voraussetzungen

Dieses Szenario, in dem eine geschützte Web-API andere Web-APIs aufruft, baut auf dem [Szenario: Geschützte Web-API](scenario-protected-web-api-overview.md) auf.

## <a name="overview"></a>Übersicht

- Ein Web-, Desktop-, Mobil- oder SPA-Client (Single-Page Application, Single-Page-Webanwendung), der nicht im zugehörigen Diagramm dargestellt ist, ruft eine geschützte Web-API auf und stellt in seinem HTTP-Header „Authorization“ (Autorisierung) ein JWT-Bearertoken (JSON Web Token) bereit.
- Die geschützte Web-API überprüft das Token und verwendet die Methode `AcquireTokenOnBehalfOf` der Microsoft-Authentifizierungsbibliothek (Microsoft Authentication Library, MSAL), um ein weiteres Token von Azure Active Directory (Azure AD) anzufordern, sodass die geschützte Web-API im Auftrag des Benutzers eine zweite Web-API oder eine Downstream-Web-API aufrufen kann.
- Die geschützte Web-API kann zudem später `AcquireTokenSilent`aufrufen, um im Auftrag des gleichen Benutzers Token für andere Downstream-APIs anzufordern. Durch `AcquireTokenSilent` wird das Token bei Bedarf aktualisiert.

![Diagramm einer Web-API, die eine Web-API aufruft](media/scenarios/web-api.svg)

## <a name="specifics"></a>Besonderheiten

Der auf die API-Berechtigungen bezogene Teil der App-Registrierung ist klassisch. Die App-Konfiguration beinhaltet die Verwendung des OBO-Flusses (On-Behalf-Of) von OAuth 2.0, um das JWT-Bearertoken gegen ein Token für eine Downstream-API auszutauschen. Dieses Token wird zum Tokencache hinzugefügt, wo es in den Controllern der Web-API verfügbar ist, und kann dann automatisch ein Token abrufen, um Downstream-APIs aufzurufen.

## <a name="next-steps"></a>Nächste Schritte

Fahren Sie mit dem nächsten Artikel in diesem Szenario fort: [App-Registrierung](scenario-web-api-call-api-app-registration.md).
