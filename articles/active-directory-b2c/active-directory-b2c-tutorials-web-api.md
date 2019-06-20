---
title: 'Tutorial: Gewähren des Zugriffs auf eine ASP.NET-Web-API – Azure Active Directory B2C | Microsoft-Dokumentation'
description: In diesem Tutorial erfahren Sie, wie Sie mit Active Directory B2C eine ASP.NET-Web-API schützen und sie über eine ASP.NET-Webanwendung aufrufen.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 6b93a7848e5c8516507c825d3064fb61a404e3cf
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66507768"
---
# <a name="tutorial-grant-access-to-an-aspnet-web-api-using-azure-active-directory-b2c"></a>Tutorial: Gewähren des Zugriffs auf eine ASP.NET-Web-API unter Verwendung von Azure Active Directory B2C

In diesem Tutorial erfahren Sie, wie Sie eine geschützte Web-API-Ressource in Azure Active Directory (Azure AD) B2C über eine ASP.NET-Webanwendung aufrufen.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Hinzufügen einer Web-API-Anwendung
> * Konfigurieren von Bereichen für eine Web-API
> * Gewähren von Berechtigungen für die Web-API
> * Konfigurieren des Beispiels für die Verwendung der Anwendung

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

Führen Sie die erforderlichen Schritte unter [Tutorial: Aktivieren der Authentifizierung in einer Webanwendung mit Azure Active Directory B2C](active-directory-b2c-tutorials-web-app.md) aus.

## <a name="add-a-web-api-application"></a>Hinzufügen einer Web-API-Anwendung

Web-API-Ressourcen müssen bei Ihrem Mandanten registriert werden, damit sie geschützte Ressourcenanforderungen von Clientanwendungen, die ein Zugriffstoken bereitstellen, akzeptieren und darauf reagieren können.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält, indem Sie im oberen Menü auf den **Verzeichnis- und Abonnementfilter** klicken und das entsprechende Verzeichnis auswählen.
3. Wählen Sie links oben im Azure-Portal die Option **Alle Dienste** aus, suchen Sie nach **Azure AD B2C**, und wählen Sie dann diese Option aus.
4. Wählen Sie **Anwendungen** und dann **Hinzufügen** aus.
5. Geben Sie einen Namen für die Anwendung ein. Beispiel: *webapi1*.
6. Wählen Sie für **Web-App/Web-API einschließen** und n**Impliziten Ablauf zulassen** die Option **Ja**.
7. Geben Sie für **Antwort-URLs** einen Endpunkt ein, an den Azure AD B2C von Ihrer App angeforderte Token zurückgibt. Das Beispiel in diesem Tutorial wird lokal ausgeführt und lauscht an `https://localhost:44332`.
8. Geben Sie für **App-ID-URI** den für Ihre Web-API verwendeten Bezeichner ein. Der vollständige Bezeichner-URI (einschließlich der Domäne) wird für Sie generiert. Beispiel: `https://contosotenant.onmicrosoft.com/api`.
9. Klicken Sie auf **Create**.
10. Notieren Sie sich auf der Eigenschaftenseite die Anwendungs-ID, die Sie beim Konfigurieren der Webanwendung verwenden.

## <a name="configure-scopes"></a>Konfigurieren von Bereichen

Bereiche ermöglichen die Steuerung des Zugriffs auf geschützte Ressourcen. Bereiche werden von der Web-API verwendet, um eine bereichsbasierte Zugriffssteuerung zu implementieren. Benutzer der Web-API können beispielsweise über Lese- und Schreibzugriff oder nur über Lesezugriff verfügen. In diesem Tutorial verwenden Sie Bereiche zum Definieren von Lese- und Schreibberechtigungen für die Web-API.

1. Wählen Sie **Anwendungen** und dann *webapi1* aus.
2. Wählen Sie **Veröffentlichte Bereiche** aus.
3. Geben Sie unter **Bereich** den Bereich `Hello.Read` und als Beschreibung `Read access to hello` ein.
4. Geben Sie unter **Bereich** den Bereich `Hello.Write` und als Beschreibung `Write access to hello` ein.
5. Klicken Sie auf **Speichern**.

Mit den veröffentlichten Bereichen können einer Clientanwendung Berechtigungen für die Web-API gewährt werden.

## <a name="grant-permissions"></a>Erteilen von Berechtigungen

Wenn Sie über eine Anwendung eine geschützte Web-API aufrufen möchten, müssen Sie Ihrer Anwendung Berechtigungen für die API erteilen. Im vorbereitenden Tutorial haben Sie in Azure AD B2C eine Webanwendung namens *webapp1* erstellt. Sie verwenden diese Anwendung für den Aufruf der Web-API.

1. Wählen Sie **Anwendungen** und dann *webapp1* aus.
2. Wählen Sie **API-Zugriff** und dann **Hinzufügen** aus.
3. Wählen Sie in der Dropdownliste **API auswählen** die Web-API *webapi1* aus.
4. Wählen Sie in der Dropdownliste **Bereiche auswählen** die Bereiche **Hello.Read** und **Hello.Write** aus, die Sie zuvor festgelegt haben.
5. Klicken Sie auf **OK**.

Ihre Anwendung ist für den Aufruf der geschützten Web-API registriert. Ein Benutzer authentifiziert sich mit Azure AD B2C, um die Anwendung zu verwenden. Die Anwendung bezieht eine Autorisierungsgewährung von Azure AD B2C, um auf die geschützte Web-API zuzugreifen.

## <a name="configure-the-sample"></a>Das Beispiel konfigurieren

Nach dem Registrieren der Web-API und dem Definieren von Bereichen konfigurieren Sie die Web-API für die Verwendung Ihres Azure AD B2C-Mandanten. In diesem Tutorial konfigurieren Sie eine Beispiel-Web-API. Die Beispiel-Web-API ist in dem Projekt enthalten, das Sie im vorbereitenden Tutorial heruntergeladen haben:

Die Beispielprojektmappe enthält zwei Projekte:

Die Beispielprojektmappe enthält die folgenden zwei Projekte:

- **TaskWebApp:** Dient zum Erstellen und Bearbeiten einer Aufgabenliste. In dem Beispiel wird der **Registrierungs- oder Anmeldebenutzerflow** für die Registrierung oder Anmeldung von Benutzern verwendet.
- **TaskService:** Unterstützt die Funktionen zum Erstellen, Lesen, Aktualisieren und Löschen der Aufgabenliste. Die API wird durch Azure AD B2C geschützt und von „TaskWebApp“ aufgerufen.

### <a name="configure-the-web-application"></a>Konfigurieren der Webanwendung

1. Öffnen Sie die Projektmappe **B2C-WebAPI-DotNet** in Visual Studio.
2. Öffnen Sie **Web.config** im Projekt **TaskWebApp**.
3. Verwenden Sie zum lokalen Ausführen der API die localhost-Einstellung für **api:TaskServiceUrl**. Nehmen Sie in der Datei „Web.config“ folgende Änderungen vor: 

    ```C#
    <add key="api:TaskServiceUrl" value="https://localhost:44332/"/>
    ```

3. Konfigurieren Sie den URI der API. Dieser URI wird von der Webanwendung für die API-Anforderung verwendet. Konfigurieren Sie außerdem die angeforderten Berechtigungen.

    ```C#
    <add key="api:ApiIdentifier" value="https://<Your tenant name>.onmicrosoft.com/api/" />
    <add key="api:ReadScope" value="Hello.Read" />
    <add key="api:WriteScope" value="Hello.Write" />
    ```

### <a name="configure-the-web-api"></a>Konfigurieren der Web-API

1. Öffnen Sie **Web.config** im Projekt **TaskService**.
2. Konfigurieren Sie die API für die Verwendung Ihres Mandanten.

    ```C#
    <add key="ida:Tenant" value="<Your tenant name>.onmicrosoft.com" />
    ```

3. Legen Sie die Client-ID auf die ID der registrierten Anwendung für Ihre API fest.

    ```C#
    <add key="ida:ClientId" value="<application-ID>"/>
    ```

4. Aktualisieren Sie die Benutzerfloweinstellung mit dem Namen des Registrierungs- und Anmeldebenutzerflows.

    ```C#
    <add key="ida:SignUpSignInUserFlowId" value="B2C_1_signupsignin1" />
    ```

5. Konfigurieren Sie die Bereichseinstellung so, dass sie dem entspricht, was Sie im Portal erstellt haben.

    ```C#
    <add key="api:ReadScope" value="Hello.Read" />
    <add key="api:WriteScope" value="Hello.Write" />
    ```

## <a name="run-the-sample"></a>Ausführen des Beispiels

Führen Sie sowohl das Projekt **TaskWebApp** als auch das Projekt **TaskService** aus. 

1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf Ihre Projektmappe, und wählen Sie **Startprojekte festlegen...** aus. 
2. Wählen Sie **Mehrere Startprojekte** aus.
3. Ändern Sie die **Aktion** für beide Projekte in **Start**.
4. Klicken Sie auf **OK**, um die Konfiguration zu speichern.
5. Drücken Sie**F5**, um beide Anwendungen auszuführen. Jede Anwendung wird auf einer eigenen Browserregisterkarte geöffnet. `https://localhost:44316/` ist die Webanwendung.
    `https://localhost:44332/` ist die Web-API.

6. Klicken Sie in der Webanwendung auf **sign-up / sign-in** (Registrierung/Anmeldung), um sich bei der Webanwendung anzumelden. Verwenden Sie das Konto, das Sie zuvor erstellt haben. 
7. Klicken Sie nach der Anmeldung auf **Aufgabenliste**, und erstellen Sie ein Aufgabenlistenelement.

Wenn Sie ein Aufgabenlistenelement erstellen, richtet die Webanwendung eine Anforderung an die Web-API, um das Element zu generieren. Ihre geschützte Webanwendung ruft die geschützte Web-API in Ihrem Azure AD B2C-Mandanten auf.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Hinzufügen einer Web-API-Anwendung
> * Konfigurieren von Bereichen für eine Web-API
> * Gewähren von Berechtigungen für die Web-API
> * Konfigurieren des Beispiels für die Verwendung der Anwendung

> [!div class="nextstepaction"]
> [Tutorial: Hinzufügen von Identitätsanbietern zu Ihren Anwendungen in Azure Active Directory B2C](tutorial-add-identity-providers.md)
