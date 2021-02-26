---
title: Verschieben einer Single-Page-App in die Produktion
titleSuffix: Microsoft identity platform
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
ms.openlocfilehash: 9656da8be086724482f129efab323e02b73e117e
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98954958"
---
# <a name="single-page-application-move-to-production"></a>Single-Page-Webanwendung: Überführen in die Produktion

Nachdem Sie nun wissen, wie Sie ein Token zum Aufrufen von Web-APIs abrufen, sollten Sie die folgenden Punkte beachten, wenn Sie Ihre Anwendung in die Produktion verschieben.

[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="deploy-your-app"></a>Bereitstellen Ihrer App

Sehen Sie sich ein [Beispiel für eine Bereitstellung](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnet-webapi-multitenant/tree/master/Chapter3) an, um zu erfahren, wie Sie Ihre SPA- und Web-API-Projekte mit Azure Storage bzw. Azure App Services bereitstellen. 

## <a name="code-samples"></a>Codebeispiele

Diese Codebeispiele veranschaulichen verschiedene wichtige Vorgänge bei einer Single-Page-App.
- [Single-Page-Webanwendung (SPA) mit einem ASP.NET-Back-End:](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnetcore-webapi) Abrufen von Token für Ihre eigene Back-End-Web-API (ASP.NET Core) mithilfe von **MSAL.js**

- [Node.js-Web-API (Azure AD):](https://github.com/Azure-Samples/active-directory-javascript-nodejs-webapi-v2) Überprüfen von Zugriffstoken für Ihre Back-End-Web-API (Node.js) mithilfe von **passport-azure-ad**

- [Single-Page-Webanwendung (SPA) mit Azure AD B2C:](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp) Verwenden von **MSAL.js** zum Anmelden von Benutzern bei einer App, die bei **Azure Active Directory B2C** (Azure AD B2C) registriert ist

- [Node.js-Web-API (Azure AD B2C):](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi) Verwenden von **passport-azure-ad** zum Überprüfen von Zugriffstoken für Apps, die mit **Azure Active Directory B2C** (Azure AD B2C) registriert wurden

## <a name="next-steps"></a>Nächste Schritte

- [JavaScript-SPA-Tutorial:](./tutorial-v2-javascript-spa.md) Ausführliche Informationen zum Anmelden von Benutzern und Abrufen eines Zugriffstokens zum Aufrufen der **Microsoft Graph-API** mit **MSAL.js**