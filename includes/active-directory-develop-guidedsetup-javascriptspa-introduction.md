---
title: Includedatei
description: Includedatei
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/11/2019
ms.author: nacanuma
ms.custom: include file
ms.openlocfilehash: 8c2dc41fde9387da291b6e4a6c8a6220ae62b514
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66121782"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-api-from-a-javascript-single-page-application-spa"></a>Anmelden von Benutzern und Aufrufen der Microsoft Graph-API aus einer JavaScript-SPA (Single-Page-Webanwendung)

Diese Anleitung veranschaulicht, wie eine JavaScript-Single-Page-Webanwendung eine Anmeldung für private Konten und Geschäfts-, Schul- oder Unikonten durchführen, ein Zugriffstoken abrufen und die Microsoft Graph-API oder andere APIs aufrufen kann, die Zugriffstoken von einem Microsoft Identity Platform-Endpunkt benötigen.

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Funktionsweise der über diesen Leitfaden generierten Beispiel-App

![Zeigt, wie die in diesen Tutorials generierte Beispiel-App funktioniert](media/active-directory-develop-guidedsetup-javascriptspa-introduction/javascriptspa-intro.svg)

<!--start-collapse-->
### <a name="more-information"></a>Weitere Informationen

Die über diesen Leitfaden erstellte Beispiel-App ermöglicht eine JavaScript-Single-Page-Webanwendung das Abfragen der Microsoft Graph-API oder einer Web-API, die Token von einem Microsoft Identity Platform-Endpunkt akzeptiert. In diesem Szenario wird nach der Benutzeranmeldung ein Zugriffstoken angefordert und über den Autorisierungsheader den HTTP-Anforderungen hinzugefügt. Tokenabruf und -verlängerung werden von der Microsoft Authentication Library (MSAL) verarbeitet.

<!--end-collapse-->

<!--start-collapse-->
### <a name="libraries"></a>Bibliotheken

In diesem Leitfaden werden die folgenden Bibliotheken verwendet:

|Bibliothek|BESCHREIBUNG|
|---|---|
|[msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)|Microsoft Authentication Library für JavaScript-Vorschau|

> [!NOTE]
> *msal.js* hat als Ziel den *Microsoft Identity Platform-Endpunkt*, der privaten und Geschäfts-, Schul- oder Unikonten das Anmelden sowie das Abrufen von Token ermöglicht. Der *Microsoft Identity Platform-Endpunkt* weist [einige Einschränkungen](../articles/active-directory/develop/active-directory-v2-limitations.md) auf.
> Die Unterschiede zwischen v1.0- und v2.0-Endpunkten werden unter [Endpunktvergleich](../articles/active-directory/develop/azure-ad-endpoint-comparison.md) erläutert.

<!--end-collapse-->
