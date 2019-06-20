---
title: Tokenbasierte (HTTP/2)-Authentifizierung für APNS in Azure Notification Hubs | Microsoft-Dokumentation
description: In diesem Thema wird erläutert, wie Sie die neue Tokenauthentifizierung für APNS nutzen können.
services: notification-hubs
documentationcenter: .net
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 02/13/2019
ms.author: jowargo
ms.openlocfilehash: 890577c013a96fc06acf3b05881649ad8202a083
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60872366"
---
# <a name="token-based-http2-authentication-for-apns"></a>Tokenbasierte (HTTP/2)-Authentifizierung für APNS

## <a name="overview"></a>Übersicht

In diesem Artikel wird erläutert, wie Sie das neue APNS-HTTP/2-Protokoll mit tokenbasierter Authentifizierung verwenden.

Zu den Hauptvorteilen des neuen Protokolls zählen:

* Die Generierung von Tokens ist relativ problemlos möglich (verglichen mit Zertifikaten)
* Keine Ablaufdaten mehr – Sie steuern die Authentifizierungstoken und deren Sperrung
* Nutzlasten können jetzt bis zu 4 KB betragen
* Synchrones Feedback
* Sie verwenden das neueste Apple-Protokoll – Zertifikate verwenden weiterhin das binäre Protokolls, das demnächst veraltet sein wird

Nach zwei Schritten, die Sie in wenigen Minuten ausführen, können Sie diesen neuen Mechanismus verwenden:

1. Abrufen der erforderlichen Informationen aus dem Apple-Entwicklerkontoportal
2. Konfigurieren Ihres Benachrichtigungs-Hubs mit den neuen Informationen

Notification Hubs ist jetzt für die Verwendung des neuen Authentifizierungssystems mit APNS eingerichtet.

Beachten Sie Folgendes, wenn Sie zur Zertifikatanmeldeinformationen für APNS verwendet haben:

* Die Tokeneigenschaften überschreiben Ihr Zertifikat in unserem System,
* Ihre Anwendung empfängt jedoch weiterhin nahtlos Benachrichtigungen.

## <a name="obtaining-authentication-information-from-apple"></a>Abrufen von Authentifizierungsinformationen von Apple

Um die tokenbasierte Authentifizierung zu aktivieren, benötigen Sie die folgenden Eigenschaften aus Ihrem Apple-Entwicklerkonto:

### <a name="key-identifier"></a>Schlüsselbezeichner

Den Schlüsselbezeichner können Sie auf der Seite „Keys“ in Ihrem Apple Entwicklerkonto abrufen.

![](./media/notification-hubs-push-notification-http2-token-authentification/obtaining-auth-information-from-apple.png)

### <a name="application-identifier--application-name"></a>Anwendungsbezeichner und Anwendungsname

Der Anwendungsname ist über die Seite mit den App-IDs im Entwicklerkonto verfügbar.

![](./media/notification-hubs-push-notification-http2-token-authentification/app-name.png)

Der Anwendungsbezeichner ist über die Seite mit den Details zur Mitgliedschaft im Entwicklerkonto verfügbar.

![](./media/notification-hubs-push-notification-http2-token-authentification/app-id.png)

### <a name="authentication-token"></a>Authentifizierungstoken

Das Authentifizierungstoken kann heruntergeladen werden, nachdem Sie ein Token für die Anwendung generiert haben. Weitere Informationen dazu, wie Sie dieses Token generieren, finden Sie in der [Apple-Entwicklerdokumentation](https://help.apple.com/xcode/mac/current/#/devdfd3d04a1).

## <a name="configuring-your-notification-hub-to-use-token-based-authentication"></a>Konfigurieren Ihres Benachrichtigungs-Hubs zur Verwendung der tokenbasierten Authentifizierung

### <a name="configure-via-the-azure-portal"></a>Konfigurieren über das Azure-Portal

Um die tokenbasierte Authentifizierung im Portal zu aktivieren, melden Sie sich beim Azure-Portal an, und wechseln Sie zum Bereich „Notification Hub > Notification Services > APNs“.

Hier finden Sie eine neue Eigenschaft: *Authentifizierungsmodus*. Wenn Sie „Token“ auswählen, können Sie Ihren Hub mit allen relevanten Tokeneigenschaften aktualisieren.

![](./media/notification-hubs-push-notification-http2-token-authentification/azure-portal-apns-settings.png)

* Geben Sie die Eigenschaften ein, die Sie in Ihrem Apple-Entwicklerkonto abgerufen haben.
* Wählen Sie Ihren Anwendungsmodus aus (Produktion oder Sandbox).
* Klicken Sie auf **Speichern**, um Ihre APNs-Anmeldeinformationen zu aktualisieren.

### <a name="configure-via-management-api-rest"></a>Konfigurieren über die Verwaltungs-API (REST)

Sie können unsere [Verwaltungs-APIs](https://msdn.microsoft.com/library/azure/dn495827.aspx) verwenden, um Ihren Benachrichtigungs-Hub auf die Verwendung der tokenbasierten Authentifizierung zu aktualisieren.
Abhängig davon, ob die Anwendung, die Sie konfigurieren, eine Sandbox- oder eine Produktionsanwendung ist (dies geben Sie in Ihrem Apple-Entwicklerkonto an), verwenden Sie einen der entsprechenden Endpunkte:

* Sandbox-Endpunkt: [https://api.development.push.apple.com:443/3/device](https://api.development.push.apple.com:443/3/device)
* Produktionsendpunkt: [https://api.push.apple.com:443/3/device](https://api.push.apple.com:443/3/device)

> [!IMPORTANT]
> Für die tokenbasierte Authentifizierung ist die folgende API-Version erforderlich: **2017-04 oder höher**.

Hier finden Sie ein Beispiel für eine PUT-Anforderung zum Aktualisieren eines Hubs auf die tokenbasierte Authentifizierung:

    ```text
    PUT https://{namespace}.servicebus.windows.net/{Notification Hub}?api-version=2017-04
      "Properties": {
        "ApnsCredential": {
          "Properties": {
            "KeyId": "<Your Key Id>",
            "Token": "<Your Authentication Token>",
            "AppName": "<Your Application Name>",
            "AppId": "<Your Application Id>",
            "Endpoint":"<Sandbox/Production Endpoint>"
          }
        }
      }
    ```

### <a name="configure-via-the-net-sdk"></a>Konfigurieren über das .NET SDK

Sie können Ihren Hub mithilfe unseres [neuesten Client-SDK](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/1.0.8) zur Verwendung der tokenbasierten Authentifizierung konfigurieren.

Das folgende Codebeispiel veranschaulicht die richtige Verwendung:

```csharp
NamespaceManager nm = NamespaceManager.CreateFromConnectionString(_endpoint);
string token = "YOUR TOKEN HERE";
string keyId = "YOUR KEY ID HERE";
string appName = "YOUR APP NAME HERE";
string appId = "YOUR APP ID HERE";
NotificationHubDescription desc = new NotificationHubDescription("PATH TO YOUR HUB");
desc.ApnsCredential = new ApnsCredential(token, keyId, appId, appName);
desc.ApnsCredential.Endpoint = @"https://api.development.push.apple.com:443/3/device";
nm.UpdateNotificationHubAsync(desc);
```

## <a name="reverting-to-using-certificate-based-authentication"></a>Zurücksetzen auf die Verwendung der zertifikatbasierten Authentifizierung

Sie können jederzeit zur Verwendung der zertifikatbasierten Authentifizierung zurückkehren, indem Sie eine der früheren Methoden verwenden und anstelle der Tokeneigenschaften das Zertifikat übergeben. Mit dieser Aktion werden die zuvor gespeicherten Anmeldeinformationen überschrieben.
