---
title: 'Gewusst wie: Erstellen einer Überlagerung mit Media Encoder Standard'
description: Erfahren Sie, wie Sie eine Überlagerung mit Media Encoder Standard erstellen.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: how-to
ms.date: 08/31/2020
ms.openlocfilehash: 743fe146042c7b52394cc4ee8ced49a0f540e79c
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94844283"
---
# <a name="how-to-create-an-overlay-with-media-encoder-standard"></a>Gewusst wie: Erstellen einer Überlagerung mit Media Encoder Standard

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Mit Media Encoder Standard können Sie Videos mit einem Bild, einer Audiodatei oder einem anderen Video überlagern. Als Eingabe muss genau eine Datei angegeben werden. Sie können eine Bilddatei im JPG-, PNG-, GIF- oder BMP-Format oder eine Audiodatei (z. B. eine WAV-, MP3-, WMA- oder M4A-Datei) oder eine Videodatei angeben.


## <a name="prerequisites"></a>Voraussetzungen

* Sammeln Sie die Kontoinformationen, die Sie zum Konfigurieren der Datei *appSettings.json* im Beispiel benötigen. Wenn Sie sich nicht sicher sind, wie dies zu tun ist, finden Sie Informationen hierzu im [Schnellstart: Registrieren einer Anwendung bei der Microsoft Identity Platform](../../active-directory/develop/quickstart-register-app.md). Die folgenden Werte werden in der Datei *appsettings.json* erwartet.

    ```json
    {
    "AadClientId": "",
    "AadEndpoint": "https://login.microsoftonline.com",
    "AadSecret": "",
    "AadTenantId": "",
    "AccountName": "",
    "ArmAadAudience": "https://management.core.windows.net/",
    "ArmEndpoint": "https://management.azure.com/",
    "Location": "",
    "ResourceGroup": "",
    "SubscriptionId": ""
    }
    ```

Wenn Sie sich mit Transformationen noch nicht auskennen, wird empfohlen, dass Sie die folgenden Aktivitäten ausführen:

* Lesen Sie [Codieren von Video- und Audiodaten mit Media Services](encoding-concept.md).
* Lesen Sie [Codieren mit einer benutzerdefinierten Transformation – .NET](customize-encoder-presets-how-to.md). Führen Sie die Schritte in diesem Artikel aus, um .NET einzurichten, das für die Arbeit mit Transformationen erforderlich ist, und kehren Sie dann hierher zurück, um ein vordefiniertes Beispiel für Überlagerungen auszuprobieren.
* Weitere Informationen finden Sie im [Referenzdokument zu Transformationen](/rest/api/media/transforms).

Sobald Sie sich mit Transformationen vertraut gemacht haben, laden Sie das Beispiel für Überlagerungen herunter.

## <a name="overlays-preset-sample"></a>Vordefiniertes Beispiel für Überlagerungen

Laden Sie das [media-services-overlay-sample](https://github.com/Azure-Samples/media-services-overlays) herunter, um mit Überlagerungen zu beginnen.

## <a name="next-steps"></a>Nächste Schritte

* [Erstellen von Subclips für ein Video beim Codieren mit Media Services: .NET](subclip-video-dotnet-howto.md)
