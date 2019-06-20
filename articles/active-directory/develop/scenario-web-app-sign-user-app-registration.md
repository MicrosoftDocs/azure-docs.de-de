---
title: Web-App für Benutzeranmeldungen (App-Registrierung) – Microsoft Identity Platform
description: Erfahren Sie, wie Sie eine Web-App erstellen, die Benutzer anmeldet (App-Registrierung)
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
ms.openlocfilehash: e6f512911811535818f4ad857c5c3b956870f619
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65080056"
---
# <a name="web-app-that-signs-in-users---app-registration"></a>Web-App für Benutzeranmeldungen – App-Registrierung

Auf dieser Seite werden die Besonderheiten der App-Registrierung für eine Web-App erläutert, mit der Benutzer angemeldet werden.

Für das Registrieren Ihrer Anwendung können Sie Folgendes verwenden:

- Die [Web-App-Schnellstarts](#register-an-app-using-the-quickstarts) – Schnellstarts im Azure-Portal vermitteln nicht nur erste Erfahrungen beim Erstellen einer Anwendung, sondern umfassen auch die Schaltfläche **Make this change for me** (Diese Änderung für mich durchführen). Sie können diese Schaltfläche verwenden, um die benötigten Eigenschaften festzulegen. Dies gilt auch für eine bereits vorhandene App. Sie müssen die Werte dieser Eigenschaften an Ihren konkreten Fall anpassen. Insbesondere wird sich die Web-API-URL für Ihre App wahrscheinlich vom vorgeschlagenen Standardwert unterscheiden, was auch Auswirkungen auf den Abmelde-URI hat.
- Das Azure-Portal für die [manuelle Registrierung Ihrer Anwendung](#register-an-app-using-azure-portal)
- PowerShell und Befehlszeilentools

## <a name="register-an-app-using-the-quickstarts"></a>Registrieren einer App mithilfe der Schnellstarts

Wenn Sie zu diesem Link navigieren, können Sie einen Bootstrap zur Erstellung Ihrer Web-App durchführen:

- [ASP.NET Core](https://aka.ms/aspnetcore2-1-aad-quickstart-v2)
- [ASP.NET](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs)

### <a name="register-an-app-using-azure-portal"></a>Registrieren einer App mit dem Azure-Portal

> [!NOTE]
> Welches Portal Sie verwenden müssen, hängt davon ab, ob Ihre Anwendung in der öffentlichen Cloud von Microsoft Azure oder in einer nationalen oder Sovereign Cloud ausgeführt wird. Weitere Informationen finden Sie unter [Nationale Clouds](./authentication-national-cloud.md#app-registration-endpoints).

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim [Azure-Portal](https://portal.azure.com) an. Melden Sie sich alternativ am Azure-Portal der nationalen Cloud Ihrer Wahl an.
1. Wenn Sie mit Ihrem Konto auf mehrere Mandanten zugreifen können, klicken Sie rechts oben auf Ihr Konto, und legen Sie Ihre Portalsitzung auf den gewünschten Azure AD-Mandanten fest.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** und anschließend **App-Registrierungen** > **Neue Registrierung** aus.
1. Geben Sie auf der daraufhin angezeigten Seite **Anwendung registrieren** die Registrierungsinformationen für Ihre Anwendung ein:
   - Wählen Sie die unterstützten Kontotypen für Ihre Anwendung aus (siehe [Supported Account types (Unterstützte Kontotypen)](./v2-supported-account-types.md)).
   - Geben Sie im Abschnitt **Name** einen aussagekräftigen Anwendungsnamen ein, der den Benutzern der App angezeigt wird (beispielsweise `AspNetCore-WebApp`).
   - Fügen Sie unter **Antwort-URL** die Antwort-URL für Ihre App hinzu, z. B. `https://localhost:44321/`, und wählen Sie **Registrieren** aus.
1. Wählen Sie das Menü **Authentifizierung** aus, und fügen Sie dann die folgenden Informationen hinzu:
- Fügen Sie `https://localhost:44321/signin-oidc` unter **Antwort-URL** hinzu, und wählen Sie **Registrieren** aus.
- Legen Sie im Abschnitt **Erweiterte Einstellungen** die Option **Abmelde-URL** auf `https://localhost:44321/signout-oidc` fest.
- Aktivieren Sie unter **Implizite Genehmigung** die Option **ID-Token**.
- Wählen Sie **Speichern** aus.

### <a name="register-an-app-using-powershell"></a>Registrieren einer App mit PowerShell

> [!NOTE]
> Derzeit erstellt Azure AD PowerShell nur Anwendungen mit den folgenden unterstützten Kontotypen:
>
> - MyOrg (nur Konten in diesem Organisationsverzeichnis)
> - AnyOrg (Konten in einem beliebigen Organisationsverzeichnis)
>
> Wenn Sie eine Anwendung erstellen möchten, die Benutzer mit ihren persönlichen Microsoft-Konten (z. B. Skype, XBox, Outlook.com) anmeldet, können Sie zunächst eine Anwendung mit mehreren Mandanten erstellen (unterstützte Kontotypen = Konten in einem beliebigen Organisationsverzeichnis) und anschließend die `signInAudience`-Eigenschaft in das Anwendungsmanifest aus dem Azure-Portal ändern. Dies wird in Schritt [1.3](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-3-AnyOrgOrPersonal#step-1-register-the-sample-with-your-azure-ad-tenant) des ASP.NET Core-Tutorials ausführlich erläutert (und kann allgemein auf Web-Apps in einer beliebigen Sprache übertragen werden).

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Codekonfiguration der App](scenario-web-app-sign-user-app-configuration.md)
