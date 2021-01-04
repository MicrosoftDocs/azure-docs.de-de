---
title: 'Tutorial: Gewähren des Zugriffs auf eine ASP.NET-Web-API'
titleSuffix: Azure AD B2C
description: In diesem Tutorial erfahren Sie, wie Sie mit Active Directory B2C eine ASP.NET-Web-API schützen und sie über eine ASP.NET-Webanwendung aufrufen.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 10/14/2019
ms.custom: devx-track-csharp, mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: ada4226500437e7733605a29988ee7cacae40761
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95994311"
---
# <a name="tutorial-grant-access-to-an-aspnet-web-api-using-azure-active-directory-b2c"></a>Tutorial: Gewähren des Zugriffs auf eine ASP.NET-Web-API unter Verwendung von Azure Active Directory B2C

In diesem Tutorial erfahren Sie, wie Sie eine geschützte Web-API-Ressource in Azure Active Directory B2C (Azure AD B2C) über eine ASP.NET-Webanwendung aufrufen.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Hinzufügen einer Web-API-Anwendung
> * Konfigurieren von Bereichen für eine Web-API
> * Gewähren von Berechtigungen für die Web-API
> * Konfigurieren des Beispiels für die Verwendung der Anwendung

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

Führen Sie die erforderlichen Schritte unter [Tutorial: Aktivieren der Authentifizierung in einer Webanwendung mit Azure Active Directory B2C](tutorial-web-app-dotnet.md) aus.

## <a name="add-a-web-api-application"></a>Hinzufügen einer Web-API-Anwendung

Web-API-Ressourcen müssen bei Ihrem Mandanten registriert werden, damit sie geschützte Ressourcenanforderungen von Clientanwendungen, die ein Zugriffstoken bereitstellen, akzeptieren und darauf reagieren können.

Zum Registrieren einer Anwendung in Ihrem Azure AD B2C-Mandanten können Sie unsere neue einheitliche Benutzeroberfläche **App-Registrierungen** oder unsere alte Benutzeroberfläche **Anwendungen (Legacy)** verwenden. [Weitere Informationen zur neuen Oberfläche](./app-registrations-training-guide.md)

#### <a name="app-registrations"></a>[App-Registrierungen](#tab/app-reg-ga/)

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie im oberen Menü den Filter **Verzeichnis und Abonnement** aus, und wählen Sie dann das Verzeichnis aus, das Ihren Azure AD B2C-Mandanten enthält.
1. Wählen Sie im linken Menü die Option **Azure AD B2C** aus. Oder wählen Sie **Alle Dienste** aus, suchen Sie nach dem Eintrag **Azure AD B2C**, und wählen Sie ihn aus.
1. Wählen Sie **App-Registrierungen** aus, und wählen Sie dann **Registrierung einer neuen Anwendung** aus.
1. Geben Sie unter **Name** einen Namen für die Anwendung ein. Beispiel: *webapi1*.
1. Wählen Sie unter **Umleitungs-URI** die Option **Web** aus, und geben Sie dann einen Endpunkt ein, an den Azure AD B2C von Ihrer Anwendung angeforderte Token zurückgeben soll. Das Beispiel in diesem Tutorial wird lokal ausgeführt und lauscht an `https://localhost:44332`.
1. Wählen Sie **Registrieren**.
1. Notieren Sie sich die **Anwendungs-ID (Client)** zur Verwendung in einem späteren Schritt.

#### <a name="applications-legacy"></a>[Anwendungen (Legacy)](#tab/applications-legacy/)

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält, indem Sie im oberen Menü auf den **Verzeichnis- und Abonnementfilter** klicken und das entsprechende Verzeichnis auswählen.
3. Wählen Sie links oben im Azure-Portal die Option **Alle Dienste** aus, suchen Sie nach **Azure AD B2C**, und wählen Sie dann diese Option aus.
4. Wählen Sie **Anwendungen (Legacy)** und dann **Hinzufügen** aus.
5. Geben Sie einen Namen für die Anwendung ein. Beispiel: *webapi1*.
6. Wählen Sie für **Web-App/Web-API einschließen** die Option **Ja** aus.
7. Geben Sie für **Antwort-URLs** einen Endpunkt ein, an den Azure AD B2C von Ihrer App angeforderte Token zurückgibt. Das Beispiel in diesem Tutorial wird lokal ausgeführt und lauscht an `https://localhost:44332`.
8. Geben Sie für **App-ID-URI** den für Ihre Web-API verwendeten Bezeichner ein. Der vollständige Bezeichner-URI (einschließlich der Domäne) wird für Sie generiert. Beispiel: `https://contosotenant.onmicrosoft.com/api`.
9. Klicken Sie auf **Erstellen**.
10. Notieren Sie sich auf der Eigenschaftenseite die Anwendungs-ID, die Sie beim Konfigurieren der Webanwendung verwenden.

* * *

## <a name="configure-scopes"></a>Konfigurieren von Bereichen

Bereiche ermöglichen die Steuerung des Zugriffs auf geschützte Ressourcen. Bereiche werden von der Web-API verwendet, um eine bereichsbasierte Zugriffssteuerung zu implementieren. Benutzer der Web-API können beispielsweise über Lese- und Schreibzugriff oder nur über Lesezugriff verfügen. In diesem Tutorial verwenden Sie Bereiche zum Definieren von Lese- und Schreibberechtigungen für die Web-API.

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

## <a name="grant-permissions"></a>Erteilen von Berechtigungen

Wenn Sie über eine Anwendung eine geschützte Web-API aufrufen möchten, müssen Sie Ihrer Anwendung Berechtigungen für die API erteilen. Im vorbereitenden Tutorial haben Sie in Azure AD B2C eine Webanwendung namens *webapp1* erstellt. Sie verwenden diese Anwendung für den Aufruf der Web-API.

[!INCLUDE [active-directory-b2c-permissions-api](../../includes/active-directory-b2c-permissions-api.md)]

Ihre Anwendung ist für den Aufruf der geschützten Web-API registriert. Ein Benutzer authentifiziert sich mit Azure AD B2C, um die Anwendung zu verwenden. Die Anwendung bezieht eine Autorisierungsgewährung von Azure AD B2C, um auf die geschützte Web-API zuzugreifen.

## <a name="configure-the-sample"></a>Das Beispiel konfigurieren

Nach dem Registrieren der Web-API und dem Definieren von Bereichen konfigurieren Sie die Web-API für die Verwendung Ihres Azure AD B2C-Mandanten. In diesem Tutorial konfigurieren Sie eine Beispiel-Web-API. Die Beispiel-Web-API ist in dem Projekt enthalten, das Sie im vorbereitenden Tutorial heruntergeladen haben:

Die Beispielprojektmappe enthält zwei Projekte:

* **TaskWebApp:** Dient zum Erstellen und Bearbeiten einer Aufgabenliste. In dem Beispiel wird der **Registrierungs- oder Anmeldebenutzerflow** für die Registrierung oder Anmeldung von Benutzern verwendet.
* **TaskService:** Unterstützt die Funktionen zum Erstellen, Lesen, Aktualisieren und Löschen der Aufgabenliste. Die API wird durch Azure AD B2C geschützt und von „TaskWebApp“ aufgerufen.

### <a name="configure-the-web-application"></a>Konfigurieren der Webanwendung

1. Öffnen Sie die Projektmappe **B2C-WebAPI-DotNet** in Visual Studio.
1. Öffnen Sie **Web.config** im Projekt **TaskWebApp**.
1. Verwenden Sie zum lokalen Ausführen der API die localhost-Einstellung für **api:TaskServiceUrl**. Nehmen Sie in der Datei „Web.config“ folgende Änderungen vor:

    ```csharp
    <add key="api:TaskServiceUrl" value="https://localhost:44332/"/>
    ```

1. Konfigurieren Sie den URI der API. Dieser URI wird von der Webanwendung für die API-Anforderung verwendet. Konfigurieren Sie außerdem die angeforderten Berechtigungen.

    ```csharp
    <add key="api:ApiIdentifier" value="https://<Your tenant name>.onmicrosoft.com/api/" />
    <add key="api:ReadScope" value="demo.read" />
    <add key="api:WriteScope" value="demo.write" />
    ```

### <a name="configure-the-web-api"></a>Konfigurieren der Web-API

1. Öffnen Sie **Web.config** im Projekt **TaskService**.
1. Konfigurieren Sie die API für die Verwendung Ihres Mandanten.

    ```csharp
    <add key="ida:AadInstance" value="https://<Your tenant name>.b2clogin.com/{0}/{1}/v2.0/.well-known/openid-configuration" />
    <add key="ida:Tenant" value="<Your tenant name>.onmicrosoft.com" />
    ```

1. Legen Sie die Client-ID auf die Anwendungs-ID Ihrer registrierten Web-API-Anwendung (*webapi1*) fest.

    ```csharp
    <add key="ida:ClientId" value="<application-ID>"/>
    ```

1. Aktualisieren Sie die Benutzerfloweinstellung mit dem Namen des Registrierungs- und Anmeldebenutzerflows (*B2C_1_signupsignin1*).

    ```csharp
    <add key="ida:SignUpSignInPolicyId" value="B2C_1_signupsignin1" />
    ```

1. Konfigurieren Sie die Bereichseinstellung so, dass sie dem entspricht, was Sie im Portal erstellt haben.

    ```csharp
    <add key="api:ReadScope" value="demo.read" />
    <add key="api:WriteScope" value="demo.write" />
    ```

## <a name="run-the-sample"></a>Ausführen des Beispiels

Führen Sie sowohl das Projekt **TaskWebApp** als auch das Projekt **TaskService** aus.

1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf Ihre Projektmappe, und wählen Sie **Startprojekte festlegen...** aus.
1. Wählen Sie **Mehrere Startprojekte** aus.
1. Ändern Sie die **Aktion** für beide Projekte in **Start**.
1. Klicken Sie auf **OK**, um die Konfiguration zu speichern.
1. Drücken Sie **F5**, um beide Anwendungen auszuführen. Jede Anwendung wird in einem eigenen Browserfenster geöffnet.
    * `https://localhost:44316/` ist die Webanwendung.
    * `https://localhost:44332/` ist die Web-API.

1. Wählen Sie in der Webanwendung **sign-up / sign-in** (Registrierung/Anmeldung) aus, um sich bei der Webanwendung anzumelden. Verwenden Sie das Konto, das Sie zuvor erstellt haben.
1. Wählen Sie nach der Anmeldung die **Aufgabenliste** aus, und erstellen Sie ein Aufgabenlistenelement.

Wenn Sie ein Aufgabenlistenelement erstellen, richtet die Webanwendung eine Anforderung an die Web-API, um das Element zu generieren. Ihre geschützte Webanwendung ruft die von Azure AD B2C geschützte Web-API auf.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Hinzufügen einer Web-API-Anwendung
> * Konfigurieren von Bereichen für eine Web-API
> * Gewähren von Berechtigungen für die Web-API
> * Konfigurieren des Beispiels für die Verwendung der Anwendung

> [!div class="nextstepaction"]
> [Tutorial: Hinzufügen von Identitätsanbietern zu Ihren Anwendungen in Azure Active Directory B2C](tutorial-add-identity-providers.md)