---
title: 'Tutorial: Aktivieren der Authentifizierung in einer Single-Page-Webanwendung – Azure Active Directory B2C | Microsoft-Dokumentation'
description: Tutorial zur Verwendung von Azure Active Directory B2C zum Bereitstellen einer Benutzeranmeldung für eine einseitige Anwendung (JavaScript)
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 6e4be3a14a6cfba6b32bea8a77975e3e34ea012d
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66507816"
---
# <a name="tutorial-enable-authentication-in-a-single-page-application-using-azure-active-directory-b2c"></a>Tutorial: Aktivieren der Authentifizierung in einer Single-Page-Webanwendung mit Azure Active Directory B2C

In diesem Tutorial erfahren Sie, wie Sie Azure Active Directory (Azure AD) B2C für die Anmeldung und Registrierung von Benutzern in einer Single-Page-Webanwendung (Single-Page Application, SPA) verwenden. Mit Azure AD B2C können sich Ihre Anwendungen über offene Standardprotokolle bei Konten für soziale Netzwerke sowie bei Unternehmenskonten und Azure Active Directory-Konten authentifizieren.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Aktualisieren der Anwendung in Azure AD B2C
> * Konfigurieren des Beispiels für die Verwendung der Anwendung
> * Anmelden über den Benutzerflow

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

* [Erstellen Sie Benutzerflows](tutorial-create-user-flows.md), um Benutzererfahrungen in Ihrer Anwendung zu aktivieren. 
* Installieren Sie [Visual Studio 2019](https://www.visualstudio.com/downloads/) mit der Workload **ASP.NET und Webentwicklung**.
* Installieren Sie [.NET Core 2.0.0 SDK](https://www.microsoft.com/net/core) oder höher
* Installieren von [Node.js](https://nodejs.org/en/download/)

## <a name="update-the-application"></a>Aktualisieren der Anwendung

In dem Tutorial, das Sie zur Vorbereitung absolviert haben, wurde eine Webanwendung in Azure AD B2C hinzugefügt. Um die Kommunikation mit dem Beispiel im Tutorial zu aktivieren, müssen Sie der Anwendung in Azure AD B2C einen Umleitungs-URI hinzufügen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält, indem Sie im oberen Menü auf den **Verzeichnis- und Abonnementfilter** klicken und das entsprechende Verzeichnis auswählen.
3. Wählen Sie links oben im Azure-Portal die Option **Alle Dienste** aus, suchen Sie nach **Azure AD B2C**, und wählen Sie dann diese Option aus.
4. Wählen Sie **Anwendungen** und anschließend die Anwendung *webapp1* aus.
5. Fügen Sie unter **Antwort-URL** Folgendes hinzu: `http://localhost:6420`.
6. Wählen Sie **Speichern** aus.
7. Notieren Sie sich auf der Eigenschaftenseite die Anwendungs-ID, die Sie beim Konfigurieren der Webanwendung verwenden.
8. Wählen Sie **Schlüssel** > **Schlüssel generieren** > **Speichern** aus. Notieren Sie sich den Schlüssel. Er wird beim Konfigurieren der Webanwendung verwendet.

## <a name="configure-the-sample"></a>Das Beispiel konfigurieren

In diesem Tutorial konfigurieren Sie ein Beispiel, das Sie von GitHub herunterladen können. Das Beispiel veranschaulicht, wie eine Single-Page-Webanwendung Azure AD B2C für die Benutzerregistrierung und -anmeldung sowie zum Aufrufen einer geschützten Web-API verwenden kann.

[Laden Sie eine ZIP-Datei herunter](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip), oder klonen Sie das Beispiel aus GitHub.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

So ändern Sie die Einstellungen:

1. Öffnen Sie die Datei `index.html` im Beispiel.
2. Konfigurieren Sie das Beispiel mit der Anwendungs-ID und dem Schlüssel, die Sie zuvor notiert haben. Ändern Sie die folgenden Codezeilen. Ersetzen Sie dabei die Werte durch die Namen Ihres Verzeichnisses und Ihre APIs:

    ```javascript
    // The current application coordinates were pre-registered in a B2C directory.
    var applicationConfig = {
        clientID: '<Application ID>',
        authority: "https://contoso.b2clogin.com/tfp/contoso.onmicrosoft.com/B2C_1_signupsignin1",
        b2cScopes: ["https://contoso.onmicrosoft.com/demoapi/demo.read"],
        webApi: 'https://contosohello.azurewebsites.net/hello',
    };
    ```

    Der Name des in diesem Tutorial verwendeten Benutzerflows lautet **B2C_1_signupsignin1**. Verwenden Sie bei einem anderen Benutzerflownamen den Benutzerflownamen im Wert `authority`.

## <a name="run-the-sample"></a>Ausführen des Beispiels

1. Starten Sie eine Node.js-Eingabeaufforderung.
2. Wechseln Sie zum Verzeichnis mit dem Node.js-Beispiel. Beispiel: `cd c:\active-directory-b2c-javascript-msal-singlepageapp`
3. Führen Sie die folgenden Befehle aus:

    ```
    npm install && npm update
    node server.js
    ```

    Im Konsolenfenster wird die Portnummer angezeigt, unter der die App gehostet wird.
    
    ```
    Listening on port 6420...
    ```

4. Navigieren Sie in einem Browser zur Adresse `http://localhost:6420`, um die Anwendung anzuzeigen.

Das Beispiel unterstützt Registrierung, Anmeldung, Profilbearbeitung und Kennwortzurücksetzung. In diesem Tutorial wird beschrieben, wie sich ein Benutzer mit einer E-Mail-Adresse anmeldet.

### <a name="sign-up-using-an-email-address"></a>Registrieren mit einer E-Mail-Adresse

1. Klicken Sie auf **Anmelden**, um sich als Benutzer der Anwendung zu registrieren. Hierbei wird der zuvor definierte Benutzerflow **B2C_1_signupsignin1** verwendet.
2. Azure AD B2C zeigt eine Anmeldeseite mit einem Registrierungslink an. Da Sie noch nicht über ein Konto verfügen, klicken Sie auf den Link **Jetzt registrieren**. 
3. Der Registrierungsworkflow zeigt eine Seite an, über die die Identität des Benutzers mithilfe einer E-Mail-Adresse erfasst und überprüft wird. Darüber hinaus werden im Rahmen des Registrierungsworkflows auch das Kennwort des Benutzers sowie die angeforderten Attribute erfasst, die im Benutzerflow definiert wurden.

    Verwenden Sie eine gültige E-Mail-Adresse, und bestätigen Sie sie mithilfe des Prüfcodes. Legen Sie ein Kennwort fest. Geben Sie Werte für die angeforderten Attribute ein. 

    ![Registrierungsworkflow](media/active-directory-b2c-tutorials-desktop-app/sign-up-workflow.png)

4. Klicken Sie auf **Erstellen**, um im Azure AD B2C-Verzeichnis ein lokales Konto zu erstellen.

Nun kann sich der Benutzer mithilfe einer E-Mail-Adresse anmelden und die SPA-App verwenden.

> [!NOTE]
> Nach der Anmeldung wird von der App der Fehler „Nicht genügend Berechtigungen“ angezeigt. Der Fehler wird angezeigt, da Sie versuchen, über das Demoverzeichnis auf eine Ressource zuzugreifen. Da Ihr Zugriffstoken nur für Ihr Azure AD-Verzeichnis gilt, ist der API-Aufruf nicht autorisiert. Fahren Sie mit dem nächsten Tutorial fort, um eine geschützte Web-API für Ihr Verzeichnis zu erstellen.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Aktualisieren der Anwendung in Azure AD B2C
> * Konfigurieren des Beispiels für die Verwendung der Anwendung
> * Anmelden über den Benutzerflow

> [!div class="nextstepaction"]
> [Tutorial: Gewähren des Zugriffs auf eine ASP.NET Core-Web-API über eine einseitige App mithilfe von Azure Active Directory B2C](active-directory-b2c-tutorials-spa-webapi.md)
