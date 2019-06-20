---
title: Übergeben von Authentifizierungstoken an Azure Media Services | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Authentifizierungstoken vom Client an den Schlüsselbereitstellungsdienst von Azure Media Services senden.
services: media-services
keywords: Content Protection, DRM, Tokenauthentifizierung
documentationcenter: ''
author: dbgeorge
manager: jasonsue
editor: ''
ms.assetid: 7c3b35d9-1269-4c83-8c91-490ae65b0817
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: dwgeo
ms.openlocfilehash: 71925a1ee67956df45901950b2a59fa4c1b458a7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61463224"
---
# <a name="learn-how-clients-pass-tokens-to-the-azure-media-services-key-delivery-service"></a>Erfahren Sie, wie Clients Token an den Schlüsselbereitstellungsdienst von Azure Media Services übergeben.
Kunden fragen häufig, wie ein Player Token für die Überprüfung an den Schlüsselübermittlungsdienst von Azure Media Services übergeben kann, damit der Player den Schlüssel abrufen kann. Media Services unterstützt die Formate „Simple Web Token“ (SWT) und „JSON Web Token“ (JWT). Die Tokenauthentifizierung kann auf jede Art von Schlüssel angewendet werden, unabhängig davon, ob Sie die Common Encryption oder die AES-Umschlagverschlüsselung (Advanced Encryption Standard) im System verwenden.

 Je nach Player und Zielplattform haben Sie folgende Möglichkeiten, das Token mit Ihrem Player zu übergeben:

- Über die HTTP-Autorisierungsheader.
    > [!NOTE]
    > Das Präfix „Bearer“ wird gemäß den OAuth 2.0-Spezifikationen erwartet. Ein Beispielplayer mit einer Tokenkonfiguration wird auf der Azure Media Player-[Demoseite](https://ampdemo.azureedge.net/) gehostet. Wählen Sie **AES (JWT-Token)** oder **AES (SWT-Token)** aus, um die Videoquelle festzulegen. Das Token wird über den Autorisierungsheader übergeben.

- Durch Hinzufügen eines URL-Abfrageparameters mit „token=tokenvalue“:  
    > [!NOTE]
    > Das Präfix „Bearer“ wird nicht erwartet. Da das Token über eine URL gesendet wird, müssen Sie die Tokenzeichenfolge schützen. Hier folgt ein C#-Beispielcode zur Veranschaulichung der Vorgehensweise:

    ```csharp
    string armoredAuthToken = System.Web.HttpUtility.UrlEncode(authToken);
    string uriWithTokenParameter = string.Format("{0}&token={1}", keyDeliveryServiceUri.AbsoluteUri, armoredAuthToken);
    Uri keyDeliveryUrlWithTokenParameter = new Uri(uriWithTokenParameter);
    ```

- Über das Feld „CustomData“.
Diese Option wird nur für den Erwerb von PlayReady-Lizenzen über das Feld „CustomData“ der PlayReady License Acquisition Challenge verwendet. In diesem Fall muss sich das Token innerhalb des hier beschriebenen XML-Dokuments befinden:

    ```xml
    <?xml version="1.0"?>
    <CustomData xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyCustomData/v1"> 
        <Token></Token> 
    </CustomData>
    ```
    Platzieren Sie Ihr Authentifizierungstoken im Token-Element.

- Über eine alternative HLS-Wiedergabeliste (HTTP Live Streaming). Wenn Sie die Tokenauthentifizierung für die AES- und HLS-Wiedergabe unter iOS/Safari konfigurieren müssen, gibt es keine Möglichkeit, das Token direkt zu senden. Weitere Informationen zum Ändern der Wiedergabeliste, damit dieses Szenario ermöglicht wird, finden Sie in [diesem Blogbeitrag](https://azure.microsoft.com/blog/2015/03/06/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/).

## <a name="next-steps"></a>Nächste Schritte

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]