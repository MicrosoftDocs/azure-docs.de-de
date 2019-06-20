---
title: Web-App, die Benutzer anmeldet (Übergang in die Produktion) – Microsoft Identity Platform
description: Erfahren Sie, wie Sie eine Web-App erstellen, die Benutzer anmeldet (Übergang in die Produktion).
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
ms.openlocfilehash: d41ad2518f885bbaa02dda3b01f0c02e9fc1d217
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65080078"
---
# <a name="web-app-that-signs-in-users---move-to-production"></a>Web-App, die Benutzer anmeldet – Übergang in die Produktion

Da Sie nun wissen, wie Sie ein Token abrufen, um Web-APIs aufzurufen, erfahren Sie in diesem Artikel, wie Ihnen der Übergang in die Produktion gelingt.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>Nächste Schritte

### <a name="calling-web-apis-scenario"></a>Szenario: Aufrufen von Web-APIs

Sobald Ihre Web-App Benutzer anmeldet, kann sie für diese Web-APIs aufrufen. Genau darum geht es im folgenden Szenario:

> [!div class="nextstepaction"]
> [Web-App, die Web-APIs aufruft](scenario-web-app-call-api-overview.md)

### <a name="deep-dive---web-app-tutorial"></a>Ausführliches Web-App-Tutorial

Lernen Sie im ASP.NET Core-Tutorial [ms-identity-aspnetcore-webapp-tutorial](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial) andere Möglichkeiten kennen, Benutzer anzumelden. Dieses Tutorial richtet sich an fortgeschrittene Leser und enthält Web-App-Code, der in Produktionsumgebungen eingesetzt werden kann. Außerdem wird beschrieben, wie für die App eine Anmeldung konfiguriert wird.

<!--- Removed the diagram as it's already shown in the above link to GitHub

![Tutorial overview](media/scenarios/aspnetcore-webapp-tutorial.svg)

--->
