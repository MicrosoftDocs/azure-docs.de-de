---
title: 'Konfigurieren der Twitter-Authentifizierung: Azure App Service'
description: Erfahren Sie, wie Sie die Twitter-Authentifizierung für Ihre App Services-Anwendung konfigurieren.
services: app-service
documentationcenter: ''
author: mattchenderson
manager: syntaxc4
editor: ''
ms.assetid: c6dc91d7-30f6-448c-9f2d-8e91104cde73
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 04/19/2018
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: 51a2ac93fd2d863855c820ba147418c5397c2a89
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60851539"
---
# <a name="how-to-configure-your-app-service-application-to-use-twitter-login"></a>Konfigurieren Ihrer App Service-Anwendung zur Nutzung der Twitter-Anmeldung
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

In diesem Thema wird veranschaulicht, wie Sie Azure App Service zur Verwendung von Twitter als Authentifizierungsanbieter konfigurieren.

Sie benötigen ein Twitter-Konto mit verifizierter E-Mail-Adresse und Telefonnummer, um den in diesem Thema beschriebenen Vorgang abzuschließen. Rufen Sie die Seite <a href="https://go.microsoft.com/fwlink/p/?LinkID=268287" target="_blank">twitter.com</a> auf, um ein neues Twitter-Konto zu erstellen.

## <a name="register"></a>Registrieren Ihrer Anwendung für Twitter
1. Melden Sie sich beim [Azure-Portal]an, und navigieren Sie zu Ihrer Anwendung. Kopieren Sie die **URL**. Diese verwenden Sie zur Konfiguration der Twitter-App.
2. Navigieren Sie zur [Twitter Developers] -Website, melden Sie sich mit den Anmeldeinformationen für Ihr Twitter-Konto an, und klicken Sie auf **Create new app**.
3. Geben Sie **Name** und **Beschreibung** für Ihre neue App ein. Fügen Sie die **URL** Ihrer Anwendung für den Wert **Website** ein. Fügen Sie dann für die **Rückruf-URL** die **Rückruf-URL** ein, die Sie zuvor kopiert haben. Dabei handelt es sich um Ihr Mobile App-Gateway mit dem angehängten Pfad */.auth/login/twitter/callback*. Beispiel: `https://contoso.azurewebsites.net/.auth/login/twitter/callback`. Stellen Sie sicher, dass Sie das HTTPS-Schema verwenden.
4. Lesen und akzeptieren Sie die Bedingungen am Ende der Seite. Klicken Sie dann auf **Create your Twitter application**. Daraufhin wird die Anwendung mit den angegebenen Details registriert.
5. Klicken Sie auf die Registerkarte **Einstellungen**, aktivieren Sie die Option **Allow this application to be used to sign in with Twitter** (Anmeldung bei Twitter mithilfe dieser Anwendung zulassen), und klicken Sie dann auf **Update Settings** (Einstellungen aktualisieren).
6. Wählen Sie die Registerkarte **Keys and Access Tokens** . Notieren Sie die Werte für Consumer Key (API Key) (Verbraucherschlüssel (API-Schlüssel)) und **Consumer secret (API Secret)** (Verbrauchergeheimnis (API-Geheimnis)).
   
   > [!NOTE]
   > Consumer secret ist eine wichtige Anmeldeinformation. Teilen Sie diesen Schlüssel mit niemandem, und geben Sie ihn nicht über Ihre App frei.
   > 
   > 

## <a name="secrets"></a>Hinzufügen von Twitter-Informationen zu Ihrer Anwendung
1. Navigieren Sie im [Azure-Portal]wieder zu Ihrer Anwendung. Klicken Sie auf **Einstellungen** und anschließend auf **Authentifizierung/Autorisierung**.
2. Falls das Authentifizierungs-/Autorisierungsfeature nicht aktiviert ist, aktivieren Sie es über die Option **Ein**.
3. Klicken Sie auf **Twitter**. Fügen Sie die Werte für App-ID und den geheimen App-Schlüssel ein, die Sie zuvor abgerufen haben. Klicken Sie dann auf **OK**.
   
   ![][1]
   
   Standardmäßig erfolgt die Authentifizierung über App Service, wobei jedoch der Zugriff auf die Inhalte Ihrer Website und APIs nicht autorisiert wird. Sie müssen die Benutzer in Ihrem App-Code autorisieren.
4. (Optional) Um den Zugriff auf Ihre Website ausschließlich auf Benutzer zu beschränken, die von Twitter authentifiziert wurden, legen Sie **Die auszuführende Aktion, wenn die Anforderung nicht authentifiziert ist** auf **Twitter** fest. Dadurch müssen alle Anforderungen authentifiziert werden. Alle nicht authentifizierten Anforderungen werden zur Authentifizierung an Twitter umgeleitet.
5. Klicken Sie auf **Speichern**.

Sie können nun Twitter für die Authentifizierung in Ihrer App verwenden.

## <a name="related-content"></a>Verwandte Inhalte
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-twitter-authentication/app-service-twitter-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-twitter-authentication/mobile-app-twitter-settings.png

<!-- URLs. -->

[Twitter Developers]: https://go.microsoft.com/fwlink/p/?LinkId=268300
[Azure-Portal]: https://portal.azure.com/
[xamarin]: ../app-services-mobile-app-xamarin-ios-get-started-users.md
