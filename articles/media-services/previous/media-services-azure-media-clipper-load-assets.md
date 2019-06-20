---
title: Laden von Medienobjekten in Azure Media Clipper | Microsoft-Dokumentation
description: Schritte zum Laden von Medienobjekten in Azure Media Clipper
services: media-services
keywords: Clip; Teilclip; Codierung; Medien
author: dbgeorge
manager: jasonsue
ms.author: dwgeo
ms.date: 03/14/2019
ms.topic: article
ms.service: media-services
ms.openlocfilehash: ec8cd06be78bbd8df0bca390696e736c3a6ee075
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61465880"
---
# <a name="loading-assets-into-azure-media-clipper"></a>Laden von Medienobjekten in Azure Media Clipper  

Medienobjekte können auf zweierlei Weise in den Azure Media Clipper geladen werden:
1. Durch statische Übergabe einer Bibliothek mit Medienobjekten
2. Durch dynamisches Erzeugen einer Liste mit Medienobjekten per API

## <a name="statically-load-videos-into-clipper"></a>Statisches Laden von Videos in Clipper
Laden Sie Videos statisch in Clipper, damit Endbenutzer entsprechende Clips erstellen können, ohne Videos aus dem Auswahlfenster für Medienobjekte auswählen zu müssen.

In diesem Fall übergeben Sie Medienobjekte statisch an den Clipper. Jedes Medienobjekt enthält eine AMS-Objekt-/Filter-ID, einen Namen und eine veröffentlichte Streaming-URL. Gegebenenfalls können auch ein Authentifizierungstoken für den Inhaltsschutz oder ein Array mit URLs von Miniaturansichten übergeben werden. Übergebene Miniaturansichten werden auf der Oberfläche angezeigt. In Szenarien, in denen die Medienobjektbibliothek statisch und klein ist, können Sie für jedes Medienobjekt in der Bibliothek den Medienobjektvertrag übergeben.

> [!NOTE]
> Beim statischen Laden von Medienobjekten in Clipper werden die Objekte **direkt zur Zeitachse** hinzugefügt und der Bereich für **Medienobjekte wird nicht gerendert**. Das erste Medienobjekt wird der Zeitachse hinzugefügt und die restlichen Objekte werden auf der rechten Seite der Zeitachse gestapelt.

Zum Laden einer statischen Medienobjektbibliothek verwenden Sie die Methode **load**, um eine JSON-Darstellung jedes Medienobjekts zu übergeben. Im folgenden Codebeispiel ist die JSON-Darstellung für ein Medienobjekt veranschaulicht.

```javascript
var assets = [
    {
      /* Required: represents the Azure Media Services asset Id when "type" === "asset"; otherwise, represents the dynamic manifest asset filter Id ("type" === "filter")  */
      "id": "my-asset-or-dynamic-manifest-asset-filter-id",
    
      /* Required: represents the asset name as shown in the Clipper interface */
      "name": "My Asset or Dynamic Manifest Asset Filter Name",
    
      /* Required: must be one of the following values: "asset" or "filter" */
      /* NOTE: "asset" type represents a rendered asset; "filter" type represents a dynamic manifest asset filter */
      "type": "asset",
    
      /* Required */
      "source": {
    
        /* Required: represents the asset streaming locator, the base Smooth Streaming URL */
        "src": "//amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest",
    
        /* Optional: default value "application/vnd.ms-sstr+xml" */
        "type": "application/vnd.ms-sstr+xml",
    
        /* Required: If the asset has content protection applied, then you must include an array with the different protection types along with the token to request the license/key; otherwise, provide an empty array */
        "protectionInfo": [{
            "type": "AES",
            "authenticationToken": "Bearer aes-token-placeholder"
          },
          {
            "type": "PlayReady",
            "authenticationToken": "Bearer playready-token-placeholder"
          },
          {
            "type": "Widevine",
            "authenticationToken": "Bearer widevine-token-placeholder"
          },
          {
            "type": "FairPlay",
            "certificateUrl": "//example/path/to/myfairplay.der",
            "authenticationToken": "Bearer fairplay-token-placeholder"
          }
        ]
      },
    
      /* Optional: array containing thumbnail URLs for the video. */
      /* NOTE: For the thumbnail URLs to work as expected in the Clipper timeline they must be evenly distributed across the video (based on the duration) and in chronological order within the array. */
      "thumbnails": [
        "//example/path/thumbnail_001.jpg",
        "//example/path/thumbnail_002.jpg",
        "//example/path/thumbnail_003.jpg",
        "//example/path/thumbnail_004.jpg",
        "//example/path/thumbnail_005.jpg"
        ]
    }
];

var subclipper = new subclipper({
    selector: '#root',
    restVersion: '2.0',
    submitSubclipCallback: onSubmitSubclip,
});
subclipper.ready(function () {
    subclipper.load(assets);
});

```

> [!NOTE]
> Es wird empfohlen, den Aufruf der load()-Methode mit der ready(handler)-Methode zu verketten, wie im vorhergehenden Beispiel gezeigt. Im vorherige Beispiel wird sichergestellt, dass das Widget bereit ist, bevor die Medienobjekte geladen werden.

> [!NOTE]
> Damit die Miniaturansicht-URLs auf der Clipper-Zeitachse wie erwartet funktionieren, müssen Sie (entsprechend ihrer Dauer) gleichmäßig über das Video verteilt und innerhalb des Arrays in chronologischer Reihenfolge angeordnet sein. Der folgende vordefinierte JSON-Codeausschnitt kann als Referenzbeispiel für das Erzeugen von Bildern mit dem Media Encoder Standard-Prozessor dienen:

```json
{
  "Start": "0",
  "Step": "00:00:05",
  "Range": "100%",
  "Type": "PngImage",
  "PngLayers": [
    {
      "Type": "PngLayer",
      "Width": 48,
      "Height": 26
    }
  ]
}
```

## <a name="dynamically-load-videos-in-clipper"></a>Dynamisches Laden von Videos in Clipper
Laden Sie Videos dynamisch in Clipper, damit Endbenutzer Videos aus dem Auswahlfenster für Medienobjekte auswählen können, die beschnitten werden.

Alternativ können Sie Medienobjekte dynamisch per Rückruf laden. In Szenarien, in denen Objekte dynamisch generiert werden oder die Bibliothek umfangreich ist, sollten Sie die Möglichkeit zum Laden per Rückruf nutzen. Um Medienobjekte dynamisch zu laden, müssen Sie die optionale OnLoadAssets-Rückruffunktion implementieren. Diese Funktion wird bei der Initialisierung an den Clipper übergeben. Die aufgelösten Medienobjekte sollten auf den gleichen Vertrag Bezug nehmen wie statisch geladene Medienobjekte. Das folgende Codebeispiel veranschaulicht die Methodensignatur, die erwartete Eingabe und die erwartete Ausgabe.

> [!NOTE]
> Beim dynamischen Laden von Medienobjekten in Clipper werden die Objekte im **Auswahlbereich für Medienobjekte** gerendert.

```javascript
// Video Assets Pane Callback
    //
    // Filter Parameters:
    // - search: string value term that will be used in the back-end to filter assets by name.
    // - skip: int value used for pagination in the back-end that allows skipping a number of assets in the response.
    // - take: int value used for pagination in the back-end that allows defining the number of assets to include in the response.
    // - type: ('filter', 'asset') value that will be used in the back-end to filter assets by type.
    //
    // Returns: a Promise object that, when resolved, returns an object containing an array of assets (input contract)
    //          that satisfies the filter parameters, plus optionally the total types of files available:
    // {
    //  total: 100,
    //  assets: [{...}],
    // }
    var onLoadAssets = function (search, skip, take, type) {
        var promise = new Promise(function (resolve, reject) {
            // TODO: implement the getAssetsFromBackend method to get the assets from the back-end using the filter parameters (search, skip, take, type).
            getAssetsFromBackend(search, skip, take, type)
                .then(function (assets) {
                    resolve({
                        total: assets.length,
                        assets: assets
                    });
                }).catch(function () {
                    reject(Error("error details"));
                });
        });
    
        return promise;
    };

    // Create widget instance:
    // - using a root element selector
    // - enabling the Video Assets panel by registering a callback in the 'assetsPanelLoaderCallback' option parameter.
    var widget = new subclipper({
        selector: '#root',

        // Enable the Video Assets panel in the widget to automatically load assets (input contract)
        assetsPanelLoaderCallback: onLoadAssets
    });

    // ...
    // The widget will automatically invoke the 'assetsPanelLoaderCallback' callback with the filter parameters specified by the user 
    // and load assets returned by the Promise into the Video Assets panel.
    // ...
```
