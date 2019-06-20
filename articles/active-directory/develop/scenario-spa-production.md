---
title: Single-Page-Webanwendung (Übergang in die Produktion) – Microsoft Identity Platform
description: Erfahren Sie, wie Sie eine Single-Page-Webanwendung (Übergang in die Produktion) erstellen.
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
ms.openlocfilehash: e6a04f5d62ec750cfbe44765e833552bd694654d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65080118"
---
# <a name="single-page-application---move-to-production"></a>Single-Page-Webanwendung – Übergang in die Produktion

Nachdem Sie nun wissen, wie Sie ein Token abrufen, um Web-APIs aufzurufen, erfahren Sie in diesem Artikel, wie Ihnen der Übergang in die Produktion gelingt.

## <a name="improve-your-app"></a>Verbessern Ihrer App

Führen Sie die erforderlichen Schritte zur Vorbereitung Ihrer App auf die Produktion durch.

- [Aktivieren Sie die Protokollierung](msal-logging.md) in Ihrer Anwendung.

## <a name="test-your-integration"></a>Testen Ihrer Integration

- Testen Sie Ihre Integration anhand der [Checkliste für die Integration von Microsoft Identity Platform](identity-platform-integration-checklist.md).

## <a name="next-steps"></a>Nächste Schritte

Hier sind weitere Beispiele und Tutorials aufgeführt:

- Ausführliche Behandlung des Schnellstartbeispiels, wobei der Code zum Anmelden von Benutzern und Abrufen eines Zugriffstokens zum Aufrufen der Microsoft Graph-API mit „MSAL.js“ beschrieben wird.

    > [!div class="nextstepaction"]
    > [JavaScript-SPA-Tutorial](./tutorial-v2-javascript-spa.md)

- Ein Beispiel, in dem veranschaulicht wird, wie Sie Token mit „MSAL.js“ für Ihre eigene Back-End-Web-API abrufen können.

     > [!div class="nextstepaction"]
     > [SPA mit einem ASP.NET-Back-End](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2)

- Ein Beispiel, in dem veranschaulicht wird, wie Sie „MSAL.js“ verwenden können, um Benutzer bei einer App anmelden zu können, die bei Azure AD B2C registriert wurde.

    > [!div class="nextstepaction"]
    > [SPA mit Azure AD B2C](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp)
