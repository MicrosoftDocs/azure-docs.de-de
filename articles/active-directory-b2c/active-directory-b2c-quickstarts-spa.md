---
title: Schnellstart – Einrichten der Anmeldung für eine Single-Page-Webanwendung mit Azure Active Directory B2C | Microsoft-Dokumentation
description: Führen Sie eine Single-Page-Beispielwebanwendung aus, bei der die Kontoanmeldung über Azure Active Directory B2C erfolgt.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: quickstart
ms.date: 10/24/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: dff1716e8886d1ef72f4d2ad4fbca4bcab86a348
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66509789"
---
# <a name="quickstart-set-up-sign-in-for-a-single-page-app-using-azure-active-directory-b2c"></a>Schnellstart: Einrichten der Anmeldung für eine einseitige App mit Azure Active Directory B2C

Azure Active Directory (Azure AD) B2C ermöglicht die Cloudidentitätsverwaltung zum Schützen Ihrer Anwendung, Ihres Unternehmens und Ihrer Kunden. Mit Azure AD B2C können sich Ihre Anwendungen über offene Standardprotokolle bei Konten für soziale Netzwerke und bei Unternehmenskonten authentifizieren. In dieser Schnellstartanleitung verwenden Sie eine Single-Page-Anwendung, um sich unter Verwendung eines sozialen Netzwerks als Identitätsanbieter anzumelden und eine per Azure AD B2C geschützte Web-API aufzurufen.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

- [Visual Studio 2019](https://www.visualstudio.com/downloads/) mit der Workload **ASP.NET und Webentwicklung**
- Installieren von [Node.js](https://nodejs.org/en/download/)
- Sie besitzen ein Konto bei einem sozialen Netzwerk, d.h. bei Facebook, Google, Microsoft oder Twitter.
- [Laden Sie eine ZIP-Datei herunter](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip), oder klonen Sie die Beispiel-Web-App aus GitHub.

    ```
    git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
    ```

## <a name="run-the-application"></a>Ausführen der Anwendung

1. Starten Sie den Server, indem Sie die folgenden Befehle über die Node.js-Eingabeaufforderung ausführen: 

    ```
    cd active-directory-b2c-javascript-msal-singlepageapp
    npm install && npm update
    node server.js
    ```

    „Server.js“ gibt die Portnummer aus, über die unter „localhost“ gelauscht wird.

    ```
    Listening on port 6420...
    ```

2. Navigieren Sie zur URL der Anwendung. Beispiel: `http://localhost:6420`.

## <a name="sign-in-using-your-account"></a>Anmelden mit Ihrem Konto

1. Klicken Sie auf **Anmelden**, um den Workflow zu starten.

    ![Beispielanwendung im Browser](media/active-directory-b2c-quickstarts-spa/sample-app-spa.png)

    In der Beispielanwendung werden mehrere Registrierungsoptionen unterstützt, z.B. die Nutzung eines sozialen Netzwerks als Identitätsanbieter oder die Erstellung eines lokalen Kontos per E-Mail-Adresse. Verwenden Sie für diese Schnellstartanleitung ein Konto bei einem sozialen Netzwerk als Identitätsanbieter (etwa Facebook, Google, Microsoft oder Twitter). 

2. Azure AD B2C zeigt für die Beispiel-Web-App eine benutzerdefinierte Anmeldeseite für die fiktive Marke „Wingtip Toys“ an. Um sich mit einem Social Media-Konto als Identitätsanbieter zu registrieren, klicken Sie auf die Schaltfläche des Identitätsanbieters, den Sie verwenden möchten.

    ![Anbieter für Registrierung oder Anmeldung](media/active-directory-b2c-quickstarts-spa/sign-in-or-sign-up-spa.png)

    Sie authentifizieren sich mit den Anmeldeinformationen Ihres Social Media-Kontos und autorisieren die Anwendung dazu, Informationen aus Ihrem Social Media-Konto zu lesen. Wenn Sie der Anwendung Zugriff auf diese gewähren, kann die Anwendung Profilinformationen aus dem Social Media-Konto abrufen, z.B. Ihren Namen und Ihre Stadt. 

3. Schließen Sie den Anmeldevorgang für den Identitätsanbieter ab.

## <a name="access-a-protected-api-resource"></a>Zugreifen auf eine geschützte API-Ressource

Klicken Sie auf **Call Web API** (Web-API aufrufen), um Ihren Anzeigenamen des Web-API-Aufrufs als JSON-Objekt zurückzugeben. 

![Web-API-Antwort](media/active-directory-b2c-quickstarts-spa/call-api-spa.png)

Die Single-Page-Beispielanwendung enthält ein Zugriffstoken in der Anforderung, die an die geschützte Web-API-Ressource gesendet wird.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Sie können Ihren Azure AD B2C-Mandanten für weitere Azure AD B2C-Schnellstarts oder -Tutorials verwenden. Wenn Sie ihn nicht mehr benötigt, können Sie [Ihren Azure AD B2C-Mandanten löschen](active-directory-b2c-faqs.md#how-do-i-delete-my-azure-ad-b2c-tenant).

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie mithilfe eines Beispiels für eine Single-Page-Webanwendung folgende Aktionen durchgeführt:

* Anmelden mit einer benutzerdefinierten Anmeldeseite
* Anmelden mit einem sozialen Netzwerk als Identitätsanbieter
* Erstellen eines Azure AD B2C-Kontos
* Aufrufen einer durch Azure AD B2C geschützten Web-API

Machen Sie sich als Nächstes mit der Erstellung Ihres eigenen Azure AD B2C-Mandanten vertraut.

> [!div class="nextstepaction"]
> [Erstellen eines Azure Active Directory B2C-Mandanten im Azure-Portal](tutorial-create-tenant.md)
