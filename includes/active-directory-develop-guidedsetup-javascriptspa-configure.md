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
ms.date: 09/17/2018
ms.author: nacanuma
ms.custom: include file
ms.openlocfilehash: 387adcdf8bdabf90bc1e691a7a8ec9ae0a8e90dc
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66121698"
---
## <a name="register-your-application"></a>Anwendung registrieren

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an, um eine Anwendung zu registrieren.
1. Wenn Sie mit Ihrem Konto auf mehrere Mandanten zugreifen können, klicken Sie rechts oben auf Ihr Konto, und legen Sie Ihre Portalsitzung auf den gewünschten Azure AD-Mandanten fest.
1. Navigieren Sie zur Seite [App-Registrierungen](https://go.microsoft.com/fwlink/?linkid=2083908) von Microsoft Identity Platform für Entwickler.
1. Geben Sie auf der daraufhin angezeigten Seite **Anwendung registrieren** einen Namen für Ihre Anwendung ein.
1. Wählen Sie unter **Unterstützte Kontotypen** **Konten in allen Organisationsverzeichnissen und persönliche Microsoft-Konten** aus.
1. Wählen Sie im Abschnitt **Umleitungs-URI** die Plattform **Web** aus, und setzen Sie den Wert auf die URL der Anwendung basierend auf Ihrem Webserver. Informationen über Anweisungen zum Festlegen und Abrufen der Umleitungs-URL in Visual Studio und Node finden Sie in den folgenden Abschnitten.
1. Wenn Sie so weit sind, klicken Sie auf **Registrieren**.
1. Notieren Sie sich auf der Seite **Übersicht** den Wert von **Anwendungsclient-ID**.
1. Für diesen Schnellstart muss der [Flow zur impliziten Genehmigung](../articles/active-directory/develop/v2-oauth2-implicit-grant-flow.md) aktiviert werden. Wählen Sie im linken Navigationsbereich der registrierten Anwendung **Authentifizierung** aus.
1. Aktivieren Sie unter **Erweiterte Einstellungen** und **Implizite Gewährung** die Kontrollkästchen **ID-Token** und **Zugriffstoken**. ID-Token und Zugriffstoken sind erforderlich, da diese App Benutzer anmelden und eine API aufrufen muss.
1. Wählen Sie **Speichern** aus.

> #### <a name="setting-the-redirect-url-for-node"></a>Festlegen der Umleitungs-URL für Node
> Für Node.js können Sie den Webserverport in der Datei *server.js* festlegen. In diesem Tutorial wird der Port 30662 als Referenz genutzt, aber Sie können auch einen beliebigen anderen Port verwenden, der verfügbar ist. Befolgen Sie die unten angegebene Anleitung zum Einrichten einer Umleitungs-URL in den Registrierungsinformationen der Anwendung:<br/>
> - Wechseln Sie zurück zu *App-Registrierung*, und legen Sie `http://localhost:30662/` als `Redirect URL` fest, oder verwenden Sie `http://localhost:[port]/`, wenn Sie einen benutzerdefinierten TCP-Port einsetzen (wobei *[port]* die benutzerdefinierte TCP-Portnummer ist).

<p>

> #### <a name="visual-studio-instructions-for-obtaining-the-redirect-url"></a>Visual Studio-Anleitung zum Abrufen der Umleitungs-URL
> Führen Sie diese Schritte aus, um die Umleitungs-URL zu erhalten:
> 1. Wählen Sie im **Projektmappen-Explorer** das Projekt aus, und sehen Sie sich das **Eigenschaftenfenster** an. Wenn kein **Eigenschaftenfenster** angezeigt wird, können Sie **F4** drücken.
> 2. Kopieren Sie den Wert aus **URL** in die Zwischenablage:<br/> ![Projekteigenschaften](media/active-directory-develop-guidedsetup-javascriptspa-configure/vs-project-properties-screenshot.png)<br />
> 3. Wechseln Sie zurück zu *App-Registrierung*, und legen Sie den Wert als **Umleitungs-URL** fest.

#### <a name="configure-your-javascript-spa"></a>Konfigurieren Ihrer JavaScript-SPA

1. Fügen Sie in der Datei `index.html`, die beim Einrichten des Projekts erstellt wurde, die Informationen für die Anwendungsregistrierung hinzu. Fügen Sie in den Tags `<script></script>` oben im Text der Datei `index.html` den folgenden Code hinzu:

    ```javascript
    var msalConfig = {
        auth: {
            clientId: "Enter_the_Application_Id_here",
            authority: "https://login.microsoftonline.com/Enter_the_Tenant_Info_Here"
        },
        cache: {
            cacheLocation: "localStorage",
            storeAuthStateInCookie: true
        }
    };
    ```

    Hinweis:
    - `Enter_the_Application_Id_here` ist die **Anwendungs-ID (Client)** für die von Ihnen registrierte Anwendung.
    - `Enter_the_Tenant_Info_Here` wird auf eine der folgenden Optionen festgelegt:
       - Unterstützt Ihre Anwendung **nur Konten in diesem Organisationsverzeichnis**, ersetzen Sie diesen Wert durch die **Mandanten-ID** oder den **Mandantennamen** (z.B. contoso.microsoft.com).
       - Unterstützt Ihre Anwendung **Konten in einem beliebigen Organisationsverzeichnis**, ersetzen Sie diesen Wert durch `organizations`.
       - Unterstützt Ihre Anwendung **Konten in allen Organisationsverzeichnissen und persönliche Microsoft-Konten**, ersetzen Sie diesen Wert durch `common`. Wenn Sie die Unterstützung *ausschließlich auf persönliche Microsoft-Konten* beschränken möchten, ersetzen Sie diesen Wert durch `consumers`.
