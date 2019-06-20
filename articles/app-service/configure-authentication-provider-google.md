---
title: 'Konfigurieren der Google-Authentifizierung: Azure App Service'
description: Erfahren Sie, wie Sie die Google-Authentifizierung für Ihre App Services-Anwendung konfigurieren.
services: app-service
documentationcenter: ''
author: mattchenderson
manager: syntaxc4
editor: ''
ms.assetid: 2b2f9abf-9120-4aac-ac5b-4a268d9b6e2b
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 04/19/2018
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: 50905b86924e0f564eaf4867c2906ad8740ddbaf
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60851176"
---
# <a name="how-to-configure-your-app-service-application-to-use-google-login"></a>Konfigurieren Ihrer App Service-Anwendung zur Nutzung der Google-Anmeldung
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

In diesem Thema wird veranschaulicht, wie Sie Azure App Service zur Verwendung von Google als Authentifizierungsanbieter konfigurieren.

Sie benötigen ein Google-Konto mit verifizierter E-Mail-Adresse, um den in diesem Thema beschriebenen Vorgang abzuschließen. Besuchen Sie die Seite [accounts.google.com](https://go.microsoft.com/fwlink/p/?LinkId=268302), um ein neues Google-Konto zu erstellen.

## <a name="register"></a>Registrieren Ihrer Anwendung für Google
1. Melden Sie sich beim [Azure-Portal]an, und navigieren Sie zu Ihrer Anwendung. Kopieren Sie Ihre **URL**. Sie wird später zum Konfigurieren der Google-App benötigt.
2. Navigieren Sie zur Website [Google apis](https://go.microsoft.com/fwlink/p/?LinkId=268303) (Google-APIs), melden Sie sich mit den Anmeldeinformationen für Ihr Google-Konto an, klicken Sie auf **Create project** (Projekt erstellen), geben Sie einen **Projektnamen** an, und klicken Sie auf **Create** (Erstellen).
3. Nachdem das Projekt erstellt wurde, wählen Sie es aus. Klicken Sie im Projektdashboard auf **Go to APIs overview** (Zur API-Übersicht wechseln).
4. Wählen Sie **Enable APIs and Services** (APIs und Dienste aktivieren) aus. Suchen Sie nach **Google + API**, und wählen Sie diese Option aus. Klicken Sie dann auf **Enable** (Aktivieren).
5. Klicken Sie im linken Navigationsbereich auf **Credentials** > **OAuth consent screen** (Anmeldeinformationen > OAuth-Zustimmungsbildschirm), wählen Sie dann Ihre **E-Mail-Adresse** aus, geben Sie einen **Produktnamen** ein, und klicken Sie auf **Save** (Speichern).
6. Klicken Sie auf der Registerkarte **Credentials** (Anmeldeinformationen) auf **Create credentials** > **OAuth client ID** (Anmeldeinformationen erstellen > OAuth-Client-ID).
7. Wählen Sie auf dem Bildschirm "Create Client ID“ (Client-ID erstellen) die Option **Web application** (Webanwendung) aus.
8. Fügen Sie die zuvor kopierte App Service-**URL** in **Authorized JavaScript Origins** (Autorisierte JavaScript-Ursprünge) und dann Ihren Umleitungs-URI in **Authorized Redirect-URI** (Autorisierter Umleitungs-URI) ein. Der Umleitungs-URI ist die URL Ihrer Anwendung mit angefügtem Pfad */.auth/login/google/callback*. Beispiel: `https://contoso.azurewebsites.net/.auth/login/google/callback`. Stellen Sie sicher, dass Sie das HTTPS-Schema verwenden. Klicken Sie dann auf **Erstellen**.
9. Notieren Sie sich auf dem nächsten Bildschirm die Werte für die Client-ID und den geheimen Clientschlüssel.

    > [!IMPORTANT]
    > Der geheime Clientschlüssel ist eine wichtige Anmeldeinformation. Teilen Sie diesen Schlüssel mit niemandem, und geben Sie ihn nicht über Ihre Anwendung weiter.


## <a name="secrets"></a>Hinzufügen von Google-Informationen zu Ihrer Anwendung
1. Navigieren Sie im [Azure-Portal]wieder zu Ihrer Anwendung. Klicken Sie auf **Einstellungen** und anschließend auf **Authentifizierung/Autorisierung**.
2. Falls das Authentifizierungs-/Autorisierungsfeature nicht aktiviert ist, aktivieren Sie es über die Option **Ein**.
3. Klicken Sie auf **Google**. Fügen Sie die ID und den geheimen Schlüssel ein, die Sie zuvor erhalten haben, und aktivieren Sie optional alle Bereiche, die bei Ihrer Anwendung erforderlich sind. Klicken Sie dann auf **OK**.
   
   ![][1]
   
   Standardmäßig erfolgt die Authentifizierung über App Service, wobei jedoch der Zugriff auf die Inhalte Ihrer Website und APIs nicht autorisiert wird. Sie müssen die Benutzer in Ihrem App-Code autorisieren.
4. (Optional) Um den Zugriff auf Ihre Website ausschließlich auf Benutzer zu beschränken, die von Google authentifiziert wurden, legen Sie **Die auszuführende Aktion, wenn die Anforderung nicht authentifiziert ist.** auf **Google** fest. Dadurch müssen alle Anforderungen authentifiziert werden. Alle nicht authentifizierten Anforderungen werden zur Authentifizierung an Google umgeleitet.
5. Klicken Sie auf **Speichern**.

Sie können nun Google für die Authentifizierung in Ihrer App verwenden.

## <a name="related-content"></a>Verwandte Inhalte
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Anchors. -->

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-settings.png

<!-- URLs. -->

[Google apis]: https://go.microsoft.com/fwlink/p/?LinkId=268303

[Azure-Portal]: https://portal.azure.com/

