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
ms.date: 04/10/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: c4dc3d422e18d9ee41bf16ac3e6f22c3d7e466d7
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66121617"
---
## <a name="register-your-application"></a>Anwendung registrieren

Sie können Ihre Anwendung auf zwei Arten registrieren.

### <a name="option-1-express-mode"></a>Option 1: Expressmodi

Gehen Sie zur schnellen Registrierung Ihrer Anwendung wie folgt vor:
1. Navigieren Sie zur [Anwendungsregistrierung im Azure-Portal](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/WinDesktopQuickstartPage/sourceType/docs).
1. Geben Sie einen Namen für Ihre Anwendung ein, und wählen Sie **Registrieren** aus.
1. Befolgen Sie die Anweisungen, um Ihre neue Anwendung mit nur einem Klick herunterzuladen und automatisch zu konfigurieren.

### <a name="option-2-advanced-mode"></a>Option 2: Erweiterter Modus

Wenn Sie Ihre Anwendung registrieren und die Anwendungsregistrierungsinformationen Ihrer Projektmappe hinzufügen möchten, führen Sie folgende Schritte aus:
1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim [Azure-Portal](https://portal.azure.com) an.
1. Wenn Sie mit Ihrem Konto auf mehrere Mandanten zugreifen können, klicken Sie rechts oben auf Ihr Konto, und legen Sie Ihre Portalsitzung auf den gewünschten Azure AD-Mandanten fest.
1. Navigieren Sie zur Seite [App-Registrierungen](https://go.microsoft.com/fwlink/?linkid=2083908) von Microsoft Identity Platform für Entwickler.
1. Wählen Sie **Neue Registrierung** aus.
   - Geben Sie im Abschnitt **Name** einen aussagekräftigen Anwendungsnamen ein, der den Benutzern der App angezeigt wird (beispielsweise `Win-App-calling-MsGraph`).
   - Wählen Sie im Abschnitt **Unterstützte Kontotypen** die Option **Konten in allen Organisationsverzeichnissen und persönliche Microsoft-Konten (z. B. Skype, Xbox, Outlook.com)** aus.
   - Wählen Sie **Registrieren** aus, um die Anwendung zu erstellen.
1. Wählen Sie in der Liste mit den Seiten für die App die Option **Authentifizierung** aus.
   1. Wählen Sie im Abschnitt **Umleitungs-URIs** in der Liste der Umleitungs-URIs Folgendes aus:
   1. Wählen Sie in der **TYPE**-Spalte **Öffentlicher Client (Mobilgerät und Desktop)** aus.
   1. Geben Sie in der Spalte **UMLEITUNGS-URI** den Wert `urn:ietf:wg:oauth:2.0:oob` ein.
1. Wählen Sie **Speichern** aus.
1. Öffnen Sie in Visual Studio die Datei *App.xaml.cs*, und ersetzen Sie `Enter_the_Application_Id_here` durch die Anwendungs-ID, die Sie soeben registriert und kopiert haben.

    ```csharp
    private static string ClientId = "Enter_the_Application_Id_here";
    ```
