---
title: Überführen einer Single-Page-App in die Produktion – Microsoft Identity Platform | Azure
description: Erfahren Sie, wie Sie eine Single-Page-Webanwendung (Übergang in die Produktion) erstellen.
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 0a51442870fb72e2b3cd93d9f03736d2c679ed06
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94442819"
---
# <a name="single-page-application-move-to-production"></a>Single-Page-Webanwendung: Überführen in die Produktion

Nachdem Sie nun wissen, wie Sie ein Token abrufen, um Web-APIs aufzurufen, erfahren Sie in diesem Artikel, wie Ihnen der Übergang in die Produktion gelingt.

## <a name="improve-your-app"></a>Verbessern Ihrer App

[Aktivieren Sie die Protokollierung](msal-logging.md), damit Ihre App für die Produktion bereit ist.

## <a name="test-your-integration"></a>Testen Ihrer Integration

Testen Sie Ihre Integration anhand der [Checkliste für die Integration von Microsoft Identity Platform](identity-platform-integration-checklist.md).

## <a name="deploy-your-app"></a>Bereitstellen Ihrer App

Sehen Sie sich ein [Beispiel für eine Bereitstellung](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnet-webapi-multitenant/tree/master/Chapter3) an, um zu erfahren, wie Sie Ihre SPA- und Web-API-Projekte mit Azure Storage bzw. Azure App Services bereitstellen. 

## <a name="next-steps"></a>Nächste Schritte

- Ausführliche Behandlung des Schnellstartbeispiels, wobei der Code zum Anmelden von Benutzern und Abrufen eines Zugriffstokens zum Aufrufen der **Microsoft Graph-API** mit **MSAL.js** beschrieben wird [JavaScript-SPA-Tutorial](./tutorial-v2-javascript-spa.md).

- Ein Beispiel, in dem veranschaulicht wird, wie Sie mit **MSAL.js** Token für Ihre eigene Back-End-Web-API (ASP.NET Core) abrufen können: [Single-Page-App (SPA) mit einem ASP.NET-Back-End](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnetcore-webapi).

- Ein Beispiel, in dem veranschaulicht wird, wie Sie mit **passport-azure-ad** Zugriffstoken für Ihre Back-End-Web-API (Node.js) überprüfen: [Node.js-Web-API (Azure AD)](https://github.com/Azure-Samples/active-directory-javascript-nodejs-webapi-v2).

- Ein Beispiel für die Verwendung von **MSAL.js** zum Anmelden von Benutzern bei einer App, die bei **Azure Active Directory B2C** (Azure AD B2C) registriert ist: [Single-Page-App (SPA) mit Azure AD B2C](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp).

- Ein Beispiel, in dem veranschaulicht wird, wie Sie mit **passport-azure-ad** Zugriffstoken für Apps überprüfen, die mit **Azure Active Directory B2C** (Azure AD B2C) registriert wurden: [Node.js-Web-API (Azure AD B2C)](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi).
