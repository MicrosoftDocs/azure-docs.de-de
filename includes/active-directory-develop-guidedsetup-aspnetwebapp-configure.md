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
ms.openlocfilehash: 2cdc6ea01e6c3555740102f319d0f4e8e4fc1c22
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66121742"
---
## <a name="register-your-application"></a>Anwendung registrieren

Wenn Sie Ihre Anwendung registrieren und die Anwendungsregistrierungsinformationen Ihrer Projektmappe hinzufügen möchten, haben Sie zwei Möglichkeiten:

### <a name="option-1-express-mode"></a>Option 1: Expressmodi

Gehen Sie zur schnellen Registrierung Ihrer Anwendung wie folgt vor:

1. Navigieren Sie zum neuen Bereich [Azure-Portal – App-Registrierungen](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs).
1. Geben Sie einen Namen für Ihre Anwendung ein, und klicken Sie auf **Registrieren**.
1. Befolgen Sie die Anweisungen, um Ihre neue Anwendung mit einem Klick herunterzuladen und automatisch zu konfigurieren.

### <a name="option-2-advanced-mode"></a>Option 2: Erweiterter Modus

Führen Sie die folgenden Schritte aus, um Ihre Anwendung zu registrieren und Ihrer Projektmappe manuell die Registrierungsinformationen Ihrer App hinzuzufügen:

1. Navigieren Sie zu Visual Studio, und gehen Sie wie folgt vor:
   1. Wählen Sie im Projektmappen-Explorer das Projekt aus. Untersuchen Sie das Fenster „Eigenschaften“ (drücken Sie F4, wenn es nicht angezeigt wird).
   1. Ändern Sie „SSL aktiviert“ in `True`.
   1. Klicken Sie mit der rechten Maustaste in Visual Studio auf das Projekt, und wählen Sie dann **Eigenschaften** und die Registerkarte **Web** aus. Ändern Sie im Abschnitt *Server* die *Projekt-URL* in die SSL-URL.
   1. Kopieren Sie die SSL-URL. Sie fügen diese URL im nächsten Schritt der Liste mit den Umleitungs-URLs im Registrierungsportal hinzu:<br/><br/>![Projekteigenschaften](media/active-directory-develop-guidedsetup-aspnetwebapp-configure/vsprojectproperties.png)<br />
1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim [Azure-Portal](https://portal.azure.com) an.
1. Wenn Sie mit Ihrem Konto auf mehrere Mandanten zugreifen können, klicken Sie rechts oben auf Ihr Konto, und legen Sie Ihre Portalsitzung auf den gewünschten Azure AD-Mandanten fest.
1. Navigieren Sie zur Seite [App-Registrierungen](https://go.microsoft.com/fwlink/?linkid=2083908) von Microsoft Identity Platform für Entwickler.
1. Wählen Sie **Neue Registrierung** aus.
1. Geben Sie auf der daraufhin angezeigten Seite **Anwendung registrieren** die Registrierungsinformationen für Ihre Anwendung ein:
   1. Geben Sie im Abschnitt **Name** einen aussagekräftigen Anwendungsnamen ein, der den Benutzern der App angezeigt wird (beispielsweise `ASPNET-Tutorial`).
   1. Fügen Sie die SSL-URL, die Sie in Schritt 1 aus Visual Studio kopiert haben (z. B. `https://localhost:44368/`) unter **Antwort-URL** hinzu, und klicken Sie auf **Registrieren**.
1. Wählen Sie das Menü **Authentifizierung** aus, und legen Sie **ID-Token** unter **Implizite Genehmigung** fest. Wählen Sie dann **Speichern** aus.
1. Fügen Sie im Stammordner der Datei `web.config` im Abschnitt `configuration\appSettings` Folgendes hinzu:

    ```xml
    <add key="ClientId" value="Enter_the_Application_Id_here" />
    <add key="redirectUri" value="Enter_the_Redirect_URL_here" />
    <add key="Tenant" value="common" />
    <add key="Authority" value="https://login.microsoftonline.com/{0}/v2.0" />
    ```

1. Ersetzen Sie `ClientId` durch die Anwendungs-ID, die Sie gerade registriert haben.
1. Ersetzen Sie `redirectUri` durch die SSL-URL Ihres Projekts.
