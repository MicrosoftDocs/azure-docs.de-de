---
title: 'Tutorial: Gewähren des Zugriffs auf eine Node.js-Web-API über eine Desktop-App – Azure Active Directory B2C | Microsoft-Dokumentation'
description: In diesem Tutorial erfahren Sie, wie Sie mit Active Directory B2C eine Node.js-Web-API schützen und sie über eine .NET-Desktop-App aufrufen.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: b4b70ddfe6ea2baf42227cc83ed0cc14969b3e92
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66508099"
---
# <a name="tutorial-grant-access-to-a-nodejs-web-api-from-a-desktop-app-using-azure-active-directory-b2c"></a>Tutorial: Gewähren des Zugriffs auf eine Node.js-Web-API über eine Desktop-App unter Verwendung von Azure Active Directory B2C

In diesem Tutorial erfahren Sie, wie Sie eine durch Azure Active Directory (Azure AD) B2C geschützte Node.js-Web-API-Ressource über eine WPF-Desktop-App (Windows Presentation Foundation) aufrufen.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Hinzufügen einer Web-API-Anwendung
> * Konfigurieren von Bereichen für eine Web-API
> * Gewähren von Berechtigungen für die Web-API
> * Aktualisieren des Beispiels für die Verwendung der Anwendung

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

Führen Sie die erforderlichen Schritte unter [Tutorial: Aktivieren der Authentifizierung von Desktop-Apps mit Konten unter Verwendung von Azure Active Directory B2C](active-directory-b2c-tutorials-desktop-app.md) aus.

## <a name="add-a-web-api-application"></a>Hinzufügen einer Web-API-Anwendung

Web-API-Ressourcen müssen bei Ihrem Mandanten registriert werden, damit sie geschützte Ressourcenanforderungen von Clientanwendungen, die ein Zugriffstoken bereitstellen, akzeptieren und darauf reagieren können. 

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält, indem Sie im oberen Menü auf den **Verzeichnis- und Abonnementfilter** klicken und das entsprechende Verzeichnis auswählen.
3. Wählen Sie links oben im Azure-Portal die Option **Alle Dienste** aus, suchen Sie nach **Azure AD B2C**, und wählen Sie dann diese Option aus.
4. Wählen Sie **Anwendungen** und dann **Hinzufügen** aus.
5. Geben Sie einen Namen für die Anwendung ein. Beispiel: *webapi1*.
6. Wählen Sie für **Web-App/Web-API einschließen** und n**Impliziten Ablauf zulassen** die Option **Ja**.
7. Geben Sie für **Antwort-URLs** einen Endpunkt ein, an den Azure AD B2C von Ihrer App angeforderte Token zurückgibt. Das Beispiel in diesem Tutorial wird lokal ausgeführt und lauscht an `https://localhost:5000`.
8. Geben Sie für **App-ID-URI** den für Ihre Web-API verwendeten Bezeichner ein. Der vollständige Bezeichner-URI (einschließlich der Domäne) wird für Sie generiert. Beispiel: `https://contosotenant.onmicrosoft.com/api`.
9. Klicken Sie auf **Create**.
10. Notieren Sie sich auf der Eigenschaftenseite die Anwendungs-ID, die Sie beim Konfigurieren der Webanwendung verwenden.

## <a name="configure-scopes"></a>Konfigurieren von Bereichen

Bereiche ermöglichen die Steuerung des Zugriffs auf geschützte Ressourcen. Bereiche werden von der Web-API verwendet, um eine bereichsbasierte Zugriffssteuerung zu implementieren. So können beispielsweise einige Benutzer Lese- und Schreibzugriff haben, während andere Benutzer nur über Leseberechtigungen verfügen. In diesem Tutorial definieren Sie Leseberechtigungen für die Web-API.

1. Wählen Sie **Anwendungen** und dann *webapi1* aus.
2. Wählen Sie **Veröffentlichte Bereiche** aus.
3. Geben Sie unter **Bereich** den Bereich `Hello.Read` und als Beschreibung `Read access to hello` ein.
4. Geben Sie unter **Bereich** den Bereich `Hello.Write` und als Beschreibung `Write access to hello` ein.
5. Klicken Sie auf **Speichern**.

Mit den veröffentlichten Bereichen können einer Client-App Berechtigungen für die Web-API gewährt werden.

## <a name="grant-permissions"></a>Erteilen von Berechtigungen

Wenn Sie über eine Anwendung eine geschützte Web-API aufrufen möchten, müssen Sie Ihrer Anwendung Berechtigungen für die API erteilen. Im vorbereitenden Tutorial haben Sie in Azure AD B2C eine Webanwendung namens *app1* erstellt. Sie verwenden diese Anwendung für den Aufruf der Web-API.

1. Wählen Sie **Anwendungen** und dann *nativeapp1* aus.
2. Wählen Sie **API-Zugriff** und dann **Hinzufügen** aus.
3. Wählen Sie in der Dropdownliste **API auswählen** die Web-API *webapi1* aus.
4. Wählen Sie in der Dropdownliste **Bereiche auswählen** die Bereiche **Hello.Read** und **Hello.Write** aus, die Sie zuvor festgelegt haben.
5. Klicken Sie auf **OK**.

Ein Benutzer authentifiziert sich mit Azure AD B2C, um die WPF-Desktopanwendung zu verwenden. Die Desktopanwendung bezieht eine Autorisierungsgewährung von Azure AD B2C, um auf die geschützte Web-API zuzugreifen.

## <a name="configure-the-sample"></a>Das Beispiel konfigurieren

Nach dem Registrieren der Web-API und dem Definieren von Bereichen konfigurieren Sie den Web-API-Code für die Verwendung Ihres Azure AD B2C-Mandanten. In diesem Tutorial konfigurieren Sie ein Beispiel für eine Node.js-Webanwendung, die Sie von GitHub herunterladen können. 

[Laden Sie eine ZIP-Datei herunter](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi/archive/master.zip), oder klonen Sie die Beispiel-Web-App aus GitHub.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```
Das Node.js-Web-API-Beispiel verwendet die Bibliothek „Passport.js“, um Azure AD B2C zum Schützen von API-Aufrufen zu aktivieren. 

1. Öffnen Sie die Datei `index.js` .
2. Konfigurieren Sie das Beispiel mit den Registrierungsinformationen für den Azure AD B2C-Mandanten. Ändern Sie die folgenden Codezeilen:

    ```nodejs
    var tenantID = "<your-tenant-name>.onmicrosoft.com";
    var clientID = "<application-ID>";
    var policyName = "B2C_1_signupsignin1";
    ```

## <a name="run-the-sample"></a>Ausführen des Beispiels

1. Starten Sie eine Node.js-Eingabeaufforderung.
2. Wechseln Sie zum Verzeichnis mit dem Node.js-Beispiel. Beispiel: `cd c:\active-directory-b2c-javascript-nodejs-webapi`
3. Führen Sie die folgenden Befehle aus:
    ```
    npm install && npm update
    ```
    ```
    node index.js
    ```

### <a name="run-the-desktop-application"></a>Ausführen der Desktopanwendung

1. Öffnen Sie die Projektmappe **active-directory-b2c-wpf** in Visual Studio.
2. Drücken Sie **F5**, um die Desktop-App auszuführen.
3. Melden Sie sich mit der E-Mail-Adresse und dem Kennwort an, die Sie im Tutorial [Aktivieren der Authentifizierung von Desktop-Apps mit Konten unter Verwendung von Azure Active Directory B2C](active-directory-b2c-tutorials-desktop-app.md) verwendet haben.
4. Klicken Sie auf die Schaltfläche **API aufrufen**. 

Die Desktopanwendung sendet eine Anforderung an die Web-API und erhält eine Antwort mit dem Anzeigenamen des angemeldeten Benutzers. Ihre geschützte Desktopanwendung ruft die geschützte Web-API in Ihrem Azure AD B2C-Mandanten auf.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Hinzufügen einer Web-API-Anwendung
> * Konfigurieren von Bereichen für eine Web-API
> * Gewähren von Berechtigungen für die Web-API
> * Aktualisieren des Beispiels für die Verwendung der Anwendung

> [!div class="nextstepaction"]
> [Tutorial: Hinzufügen von Identitätsanbietern zu Ihren Anwendungen in Azure Active Directory B2C](tutorial-add-identity-providers.md)
