---
title: Includedatei
description: Includedatei
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/11/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 86113246fab399e8364653198e9a6971317e3f2c
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66121679"
---
# <a name="add-sign-in-with-microsoft-to-an-aspnet-web-app"></a>Hinzufügen von „Mit Microsoft anmelden“ zu einer ASP.NET-Web-App

Diese Anleitung veranschaulicht das Implementieren von „Mit Microsoft anmelden“ mithilfe einer ASP.NET MVC-Projektmappe mit einer herkömmlichen browserbasierten Anwendung mittels OpenID Connect.

Am Ende dieser Anleitung kann Ihre Anwendung Anmeldungen sowohl von persönlichen Konten (z.B. outlook.com, live.com u.a.) als auch von Geschäfts-, Schul- und Unikonten von Unternehmen oder Organisationen akzeptieren, die in Azure Active Directory integriert wurden.

> Für diesen Leitfaden wird Visual Studio 2017 benötigt.  Sie haben beides nicht?  [Laden Sie Visual Studio 2017 kostenlos herunter](https://www.visualstudio.com/downloads/)

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Funktionsweise der über diesen Leitfaden generierten Beispiel-App

![Zeigt, wie die in diesen Tutorials generierte Beispiel-App funktioniert](media/active-directory-develop-guidedsetup-aspnetwebapp-intro/aspnetbrowsergeneral.svg)

Die mithilfe dieser Anleitung erstellte Beispielanwendung basiert auf dem Szenario, in dem ein Benutzer den Browser für den Zugriff auf eine ASP.NET-Website verwendet und aufgefordert wird, sich über die Schaltfläche „Anmelden“ anzumelden. In diesem Szenario wird ein Großteil der Arbeit zum Rendern der Webseite auf dem Server erledigt.

## <a name="libraries"></a>Bibliotheken

In dieser Anleitung werden die folgenden Bibliotheken verwendet:

|Bibliothek|BESCHREIBUNG|
|---|---|
|[Microsoft.Owin.Security.OpenIdConnect](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/)|Middleware, die einer Anwendung das Verwenden von OpenIDConnect für die Authentifizierung ermöglicht|
|[Microsoft.Owin.Security.Cookies](https://www.nuget.org/packages/Microsoft.Owin.Security.Cookies)|Middleware, die einer Anwendung das Beibehalten der Benutzersitzung mithilfe von Cookies ermöglicht|
|[Microsoft.Owin.Host.SystemWeb](https://www.nuget.org/packages/Microsoft.Owin.Host.SystemWeb)|Ermöglicht OWIN-basierten Anwendungen die Ausführung in IIS mithilfe der ASP.NET-Anforderungspipeline|
