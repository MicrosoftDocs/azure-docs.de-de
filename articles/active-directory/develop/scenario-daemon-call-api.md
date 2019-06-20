---
title: Web-APIs aufrufende Daemon-App (Aufrufen von Web-APIs) – Microsoft Identity Platform
description: Erstellen einer Daemon-App, die Web-APIs aufruft (Aufrufen von Web-APIs)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
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
ms.openlocfilehash: aff375f996126d9e8b64361fc0e5673c25d30c19
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65080286"
---
# <a name="daemon-app-that-calls-web-apis---call-a-web-api-from-the-app"></a>Daemon-App, die Web-APIs aufruft – Aufrufen einer Web-API aus der App

Eine Daemon-App kann eine Web-API aus einer .NET-Daemon-Anwendung oder mehrere vorab genehmigte Web-APIs aufrufen.

## <a name="calling-a-web-api-from-a-net-daemon-application"></a>Aufrufen einer Web-API aus einer .NET-Daemon-Anwendung

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

<!--
More includes will come later for Python and Java
-->

## <a name="calling-several-apis"></a>Aufrufen mehrerer APIs

Für Daemon-Apps müssen die von Ihnen aufgerufenen Web-APIs vorab genehmigt werden. Für Daemon-Apps gibt es keine inkrementelle Einwilligung (keine Benutzerinteraktion). Der Administrator des Mandanten muss vorab die Berechtigungen für die Anwendung und alle APIs erteilen. Wenn Sie mehrere APIs aufrufen möchten, müssen Sie für jede Ressource ein Token abrufen, indem Sie `AcquireTokenForClient` aufrufen. MSAL verwendet den Tokencache der Anwendung, um unnötige Dienstaufrufe zu vermeiden.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Daemon app - move to production (Daemon-App – Übergang in die Produktion)](./scenario-daemon-production.md)