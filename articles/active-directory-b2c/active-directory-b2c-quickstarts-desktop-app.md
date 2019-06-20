---
title: 'Schnellstart: Einrichten der Anmeldung für eine Desktop-App mit Azure Active Directory B2C | Microsoft-Dokumentation'
description: Führen Sie eine ASP.NET-Desktopbeispielanwendung aus, bei der die Kontoanmeldung über Azure Active Directory B2C erfolgt.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: quickstart
ms.custom: mvc
ms.date: 11/30/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: dd0679cc418cb1a15a69c01d3267d5d11b20fd3e
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66509838"
---
# <a name="quickstart-set-up-sign-in-for-a-desktop-app-using-azure-active-directory-b2c"></a>Schnellstart: Einrichten der Anmeldung für eine Desktop-App mit Azure Active Directory B2C 

Azure Active Directory (Azure AD) B2C ermöglicht die Cloudidentitätsverwaltung zum Schützen Ihrer Anwendung, Ihres Unternehmens und Ihrer Kunden. Mit Azure AD B2C können sich Ihre Anwendungen über offene Standardprotokolle bei Konten für soziale Netzwerke und bei Unternehmenskonten authentifizieren. In dieser Schnellstartanleitung verwenden Sie eine WPF-Desktopanwendung (Windows Presentation Foundation) zum Anmelden, indem Sie ein soziales Netzwerk als Identitätsanbieter nutzen und eine per Azure AD B2C geschützte Web-API aufrufen.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

- [Visual Studio 2019](https://www.visualstudio.com/downloads/) mit der Workload **ASP.NET und Webentwicklung** 
- Sie besitzen ein Konto bei einem sozialen Netzwerk, d.h. bei Facebook, Google, Microsoft oder Twitter.
- [Laden Sie eine ZIP-Datei herunter](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop/archive/master.zip), oder klonen Sie die Beispiel-Web-App aus GitHub.

    ```
    git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop.git
    ```

## <a name="run-the-application-in-visual-studio"></a>Ausführen der Anwendung in Visual Studio

1. Öffnen Sie in Visual Studio im Projektordner der Beispielanwendung die Projektmappe **active-directory-b2c-wpf.sln**.
2. Drücken Sie **F5**, um die Anwendung zu debuggen.

## <a name="sign-in-using-your-account"></a>Anmelden mit Ihrem Konto

1. Klicken Sie auf **Anmelden**, um den Workflow für das **Registrieren oder Anmelden** zu starten.

    ![Beispielanwendung](media/active-directory-b2c-quickstarts-desktop-app/wpf-sample-application.png)

    Das Beispiel unterstützt mehrere Registrierungsoptionen. Diese Optionen umfassen die Nutzung eines sozialen Netzwerks als Identitätsanbieter oder die Erstellung eines lokalen Kontos per E-Mail-Adresse. Verwenden Sie für diese Schnellstartanleitung ein Konto bei einem sozialen Netzwerk als Identitätsanbieter (etwa Facebook, Google, Microsoft oder Twitter). 


2. Azure AD B2C zeigt für die Beispiel-Web-App eine benutzerdefinierte Anmeldeseite für die fiktive Marke „Wingtip Toys“ an. Um sich mit einem Social Media-Konto als Identitätsanbieter zu registrieren, klicken Sie auf die Schaltfläche des Identitätsanbieters, den Sie verwenden möchten. 

    ![Anbieter für Registrierung oder Anmeldung](media/active-directory-b2c-quickstarts-desktop-app/sign-in-or-sign-up-wpf.png)

    Sie authentifizieren sich mit den Anmeldeinformationen Ihres Social Media-Kontos und autorisieren die Anwendung dazu, Informationen aus Ihrem Social Media-Konto zu lesen. Wenn Sie der Anwendung Zugriff auf diese gewähren, kann die Anwendung Profilinformationen aus dem Social Media-Konto abrufen, z.B. Ihren Namen und Ihre Stadt. 

2. Schließen Sie den Anmeldevorgang für den Identitätsanbieter ab.

    Die Profildetails für Ihr neues Konto werden mit Informationen aus Ihrem Social Media-Konto aufgefüllt.

## <a name="edit-your-profile"></a>Bearbeiten des Profils

Azure AD B2C umfasst Funktionen, mit denen Benutzer ihre Profile aktualisieren können. Die Beispiel-Web-App nutzt einen Azure AD B2C-Bearbeitungsprofil-Benutzerflow für den Workflow. 

1. Klicken Sie in der Menüleiste der Anwendung auf **Profil bearbeiten**, um das von Ihnen erstellte Profil zu bearbeiten.

    ![Bearbeiten eines Profils](media/active-directory-b2c-quickstarts-desktop-app/edit-profile-wpf.png)

2. Wählen Sie den Identitätsanbieter aus, der dem erstellten Konto zugeordnet ist. Wenn Sie beim Erstellen Ihres Kontos z.B. Twitter als Identitätsanbieter verwendet haben, wählen Sie Twitter aus, um die Details des verknüpften Profils zu ändern.

3. Ändern Sie Ihren **Anzeigenamen** oder den **Ort**, und klicken Sie anschließend auf **Weiter**.

    Im Textfeld *Tokeninformationen* wird ein neues Zugriffstoken angezeigt. Wenn Sie die Änderungen an Ihrem Profil überprüfen möchten, kopieren Sie das Zugriffstoken und fügen es in den Tokendecoder https://jwt.ms ein.

## <a name="access-a-protected-api-resource"></a>Zugreifen auf eine geschützte API-Ressource

Klicken Sie auf **API aufrufen**, um eine Anforderung an die geschützte Ressource zu übermitteln. 

    ![Call API](media/active-directory-b2c-quickstarts-desktop-app/call-api-wpf.png)

    The application includes the Azure AD access token in the request to the protected web API resource. The web API sends back the display name contained in the access token.

Sie haben Ihr Azure AD B2C-Benutzerkonto verwendet, um einen autorisierten Aufruf einer geschützten Azure AD B2C-Web-API durchzuführen.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Sie können Ihren Azure AD B2C-Mandanten für weitere Azure AD B2C-Schnellstarts oder -Tutorials verwenden. Wenn Sie ihn nicht mehr benötigt, können Sie [Ihren Azure AD B2C-Mandanten löschen](active-directory-b2c-faqs.md#how-do-i-delete-my-azure-ad-b2c-tenant).

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie mithilfe einer Desktop-Beispielanwendung folgende Aktionen durchgeführt: 

* Anmelden mit einer benutzerdefinierten Anmeldeseite
* Anmelden mit einem sozialen Netzwerk als Identitätsanbieter
* Erstellen eines Azure AD B2C-Kontos
* Aufrufen einer durch Azure AD B2C geschützten Web-API

Machen Sie sich als Nächstes mit der Erstellung Ihres eigenen Azure AD B2C-Mandanten vertraut. 

> [!div class="nextstepaction"]
> [Erstellen eines Azure Active Directory B2C-Mandanten im Azure-Portal](tutorial-create-tenant.md)
