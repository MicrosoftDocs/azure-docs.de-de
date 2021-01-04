---
title: 'Tutorial: Aktivieren der Authentifizierung in einer Webanwendung'
titleSuffix: Azure AD B2C
description: Tutorial zur Verwendung von Azure Active Directory B2C zum Bereitstellen einer Benutzeranmeldung für eine ASP.NET-Webanwendung.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 10/02/2020
ms.custom: devx-track-csharp, mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 9c3c63b6116e02e8a742b69e90c11e182d72ab2e
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94953028"
---
# <a name="tutorial-enable-authentication-in-a-web-application-using-azure-active-directory-b2c"></a>Tutorial: Aktivieren der Authentifizierung in einer Webanwendung mit Azure Active Directory B2C

In diesem Tutorial erfahren Sie, wie Sie Azure Active Directory B2C (Azure AD B2C) für die Anmeldung und Registrierung von Benutzern in einer ASP.NET-Webanwendung verwenden. Mit Azure AD B2C können sich Ihre Anwendungen über offene Standardprotokolle bei Konten für soziale Netzwerke sowie bei Unternehmenskonten und Azure Active Directory-Konten authentifizieren.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Aktualisieren der Anwendung in Azure AD B2C
> * Konfigurieren des Beispiels für die Verwendung der Anwendung
> * Anmelden über den Benutzerflow

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

> [!NOTE]
> Für dieses Tutorial wird eine ASP.NET-Beispielwebanwendung verwendet. Informationen zu anderen Beispielanwendungen (einschließlich ASP.NET Core, Node.js und Python) finden Sie unter [Azure Active Directory B2C-Codebeispiele](code-samples.md).

## <a name="prerequisites"></a>Voraussetzungen

* [Erstellen Sie Benutzerflows](tutorial-create-user-flows.md), um Benutzererfahrungen in Ihrer Anwendung zu aktivieren.
* Installieren Sie [Visual Studio 2019](https://www.visualstudio.com/downloads/) mit der Workload **ASP.NET und Webentwicklung**.

## <a name="update-the-application-registration"></a>Aktualisieren der Anwendungsregistrierung

In dem Tutorial, das Sie zur Vorbereitung absolviert haben, wurde eine Webanwendung in Azure AD B2C registriert. Um die Kommunikation mit dem Beispiel in diesem Tutorial zu ermöglichen, müssen Sie für die registrierte Anwendung einen Umleitungs-URI hinzufügen und einen geheimen Clientschlüssel (Schlüssel) erstellen.

### <a name="add-a-redirect-uri-reply-url"></a>Hinzufügen eines Umleitungs-URI (Antwort-URL)

Zum Aktualisieren einer Anwendung in Ihrem Azure AD B2C-Mandanten können Sie unsere neue einheitliche Benutzeroberfläche **App-Registrierungen** oder unsere alte Benutzeroberfläche **Anwendungen (Legacy)** verwenden. [Weitere Informationen zur neuen Oberfläche](./app-registrations-training-guide.md)

#### <a name="app-registrations"></a>[App-Registrierungen](#tab/app-reg-ga/)

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie im oberen Menü den Filter **Verzeichnis und Abonnement** aus, und wählen Sie dann das Verzeichnis aus, das Ihren Azure AD B2C-Mandanten enthält.
1. Wählen Sie im linken Menü die Option **Azure AD B2C** aus. Oder wählen Sie **Alle Dienste** aus, suchen Sie nach dem Eintrag **Azure AD B2C**, und wählen Sie ihn aus.
1. Wählen Sie **App-Registrierungen**, die Registerkarte **Anwendungen mit Besitzer** und dann die Anwendung *webapp1* aus.
1. Wählen Sie unter **Web** den Link **URI hinzufügen** aus, geben Sie `https://localhost:44316` ein, und wählen Sie dann **Speichern** aus.
1. Wählen Sie **Übersicht**.
1. Notieren Sie sich die **Anwendungs-ID (Client)** , die Sie später beim Konfigurieren der Webanwendung verwenden.

#### <a name="applications-legacy"></a>[Anwendungen (Legacy)](#tab/applications-legacy/)

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält, indem Sie im oberen Menü auf den **Verzeichnis- und Abonnementfilter** klicken und das entsprechende Verzeichnis auswählen.
1. Wählen Sie links oben im Azure-Portal die Option **Alle Dienste** aus, suchen Sie nach **Azure AD B2C**, und wählen Sie dann diese Option aus.
1. Wählen Sie **Anwendungen (Legacy)** und anschließend die Anwendung *webapp1* aus.
1. Fügen Sie unter **Antwort-URL** Folgendes hinzu: `https://localhost:44316`.
1. Wählen Sie **Speichern** aus.
1. Notieren Sie sich auf der Eigenschaftenseite die Anwendungs-ID, die Sie später beim Konfigurieren der Webanwendung verwenden.

* * *

### <a name="create-a-client-secret"></a>Erstellen eines Clientgeheimnisses

Erstellen Sie als Nächstes einen geheimen Clientschlüssel für die registrierte Webanwendung. Das Codebeispiel für Webanwendungen verwendet ihn beim Anfordern von Token als Identitätsnachweis.

[!INCLUDE [active-directory-b2c-client-secret](../../includes/active-directory-b2c-client-secret.md)]

## <a name="configure-the-sample"></a>Das Beispiel konfigurieren

In diesem Tutorial konfigurieren Sie ein Beispiel, das Sie von GitHub herunterladen können. In dem Beispiel wird mithilfe von ASP.NET eine einfache Aufgabenliste bereitgestellt. Das Beispiel verwendet [Microsoft OWIN-Middleware-Komponenten](/aspnet/aspnet/overview/owin-and-katana/). [Laden Sie eine ZIP-Datei herunter](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi/archive/master.zip), oder klonen Sie das Beispiel aus GitHub. Extrahieren Sie die Beispieldatei unbedingt in einem Ordner, dessen Pfad insgesamt maximal 259 Zeichen lang ist.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

Die Beispielprojektmappe enthält die folgenden zwei Projekte:

* **TaskWebApp:** Dient zum Erstellen und Bearbeiten einer Aufgabenliste. In dem Beispiel wird der **Registrierungs- oder Anmeldebenutzerflow** für die Registrierung und Anmeldung von Benutzern verwendet.
* **TaskService:** Unterstützt die Funktionen zum Erstellen, Lesen, Aktualisieren und Löschen der Aufgabenliste. Die API wird durch Azure AD B2C geschützt und von „TaskWebApp“ aufgerufen.

Sie ändern das Beispiel, um die in Ihrem Mandanten registrierte Anwendung zu verwenden. Dazu benötigen Sie die Anwendungs-ID und den Anwendungsschlüssel, den bzw. die Sie zuvor notiert haben. Außerdem konfigurieren Sie die von Ihnen erstellten Benutzerflows. Das Beispiel definiert die Konfigurationswerte als Einstellungen in der Datei *Web.config*.

Aktualisieren Sie die Einstellungen in der Datei „Web.config“ zur Verwendung mit Ihrem Benutzerflow:

1. Öffnen Sie die Projektmappe **B2C-WebAPI-DotNet** in Visual Studio.
1. Öffnen Sie im Projekt **TaskWebApp** die Datei **Web.config**.
    1. Aktualisieren Sie den Wert für `ida:Tenant` und `ida:AadInstance` mit dem Namen des von Ihnen erstellten Azure AD B2C-Mandanten. Ersetzen Sie beispielsweise `fabrikamb2c` durch `contoso`.
    1. Ersetzen Sie den Wert `ida:TenantId` durch die Verzeichnis-ID, die Sie im Azure-Portal (unter **Azure Active Directory** > **Eigenschaften** > **Verzeichnis-ID**) in den Eigenschaften für Ihren Azure B2C-Mandanten finden können.
    1. Ersetzen Sie den Wert für `ida:ClientId` durch die notierte Anwendungs-ID.
    1. Ersetzen Sie den Wert für `ida:ClientSecret` durch den notierten Schlüssel. Wenn der geheime Clientschlüssel vordefinierte XML-Entitäten enthält, z. B. kleiner als (`<`), größer als (`>`), kaufmännisches und-Zeichen (`&`) oder doppelte Anführungszeichen (`"`), müssen Sie diese Zeichen durch XML-Codierung des geheimen Clientschlüssels mit Escapezeichen versehen, bevor Sie sie der Datei „Web.config“ hinzufügen.
    1. Ersetzen Sie den Wert für `ida:SignUpSignInPolicyId` durch `b2c_1_signupsignin1`.
    1. Ersetzen Sie den Wert für `ida:EditProfilePolicyId` durch `b2c_1_profileediting1`.
    1. Ersetzen Sie den Wert für `ida:ResetPasswordPolicyId` durch `b2c_1_passwordreset1`.

## <a name="run-the-sample"></a>Ausführen des Beispiels

1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt **TaskWebApp**, und klicken Sie anschließend auf **Als Startprojekt festlegen**.
1. Drücken Sie **F5**. Der Standardbrowser wird mit der lokalen Websiteadresse `https://localhost:44316/` geöffnet.

### <a name="sign-up-using-an-email-address"></a>Registrieren mit einer E-Mail-Adresse

1. Wählen Sie **Registrieren/Anmelden**, um sich als Benutzer der Anwendung zu registrieren. Der Benutzerflow **b2c_1_signupsignin1** wird verwendet.
1. Azure AD B2C zeigt eine Anmeldeseite mit einem Registrierungslink an. Da Sie noch nicht über ein Konto verfügen, wählen Sie **Jetzt registrieren** aus. Der Registrierungsworkflow zeigt eine Seite an, über die die Identität des Benutzers mithilfe einer E-Mail-Adresse erfasst und überprüft wird. Darüber hinaus werden im Rahmen des Registrierungsworkflows auch das Kennwort des Benutzers sowie die angeforderten Attribute erfasst, die im Benutzerflow definiert wurden.
1. Verwenden Sie eine gültige E-Mail-Adresse, und bestätigen Sie sie mithilfe des Prüfcodes. Legen Sie ein Kennwort fest. Geben Sie Werte für die angeforderten Attribute ein.

    ![Die Registrierungsseite wird im Rahmen des Anmelde-/Registrierungsworkflows angezeigt.](./media/tutorial-web-app-dotnet/sign-up-workflow.PNG)

1. Wählen Sie **Erstellen**, um im Azure AD B2C-Mandanten ein lokales Konto zu erstellen.

Der Anwendungsbenutzer kann sich jetzt mit seiner E-Mail-Adresse anmelden und die Webanwendung verwenden.

Das Feature **Aufgabenliste** funktioniert jedoch erst, wenn Sie das nächste Tutorial in der Reihe abgeschlossen haben, [Tutorial: Schützen einer ASP.NET-Web-API mithilfe von Azure AD B2C](tutorial-web-api-dotnet.md).

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Aktualisieren der Anwendung in Azure AD B2C
> * Konfigurieren des Beispiels für die Verwendung der Anwendung
> * Anmelden über den Benutzerflow

Fahren Sie nun mit dem nächsten Tutorial fort, um das Feature **Aufgabenliste** der Webanwendung zu aktivieren. Darin registrieren Sie eine Web-API-Anwendung in Ihrem eigenen Azure AD B2C-Mandanten und ändern dann das Codebeispiel, um ihren Mandanten für die API-Authentifizierung zu verwenden.

> [!div class="nextstepaction"]
> [Tutorial: Schützen einer ASP.NET-Web-API mithilfe von Azure Active Directory B2C >](tutorial-web-api-dotnet.md)