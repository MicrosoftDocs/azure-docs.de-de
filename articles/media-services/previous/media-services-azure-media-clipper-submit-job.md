---
title: Übermitteln von Azure Media Clipper-Beschneidungsaufträgen | Microsoft-Dokumentation
description: Schritte zum Übermitteln von Beschneidungsaufträgen aus Azure Media Clipper
services: media-services
keywords: Beschneiden;Subclip;Codierung;Medien
author: dbgeorge
manager: jasonsue
ms.author: dwgeo
ms.date: 03/14/2019
ms.topic: article
ms.service: media-services
ms.openlocfilehash: f0dc6879ccbb22dbebd57de98e4610cd593318db
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61242847"
---
# <a name="submit-clipping-jobs-from-azure-media-clipper"></a>Übermitteln von Beschneidungsaufträgen aus Azure Media Clipper 

Azure Media Clipper erfordert die Implementierung einer **SubmitSubclipCallback**-Methode zur Übermittlung von Beschneidungsaufträgen. Diese Funktion dient zum Implementieren einer HTTP POST-Methode für die Clipper-Ausgabe an einen Webdienst. Dies ist der Webdienst, an den Sie den Codierungsauftrag übermitteln können. Die Ausgabe von Clipper ist entweder eine Media Encoder Standard-Codierungsvoreinstellung für gerenderte Aufträge oder die REST-API-Nutzlast für Aufrufe der Filter für dynamische Manifeste. Dieses Pass-Through-Modell ist notwendig, da Anmeldeinformationen für Media Services-Konten im Browser des Clients nicht sicher sind.

Das folgende Sequenzdiagramm veranschaulicht den Workflow zwischen dem Browserclient, Ihrem Webdienst und Azure Media Services: ![Azure Media Clipper-Sequenzdiagramm](media/media-services-azure-media-clipper-submit-job/media-services-azure-media-clipper-sequence-diagram.PNG)

Das vorangehende Diagramm enthält die folgenden vier Entitäten: Browser des Endbenutzers, Ihr Webdienst, der CDN-Endpunkt, der die Clipper-Ressourcen hostet, und Azure Media Services. Wenn der Endbenutzer zu Ihrer Webseite navigiert, erhält die Seite die Clipper JavaScript- und CSS-Ressourcen vom hostenden CDN-Endpunkt. Der Endbenutzer konfiguriert den Clipping-Auftrag oder den Aufruf zur Erstellung eines dynamischen Manifestfilters über seinen Browser. Wenn der Endbenutzer den Aufruf zur Auftrags- oder Filtererstellung sendet, überträgt der Browser die Nutzlast des Auftrags an einen Webdienst, den Sie bereitstellen müssen. Dieser Webdienst übermittelt den Clipping-Auftrag oder den Aufruf zur Erstellung von Filtern schließlich an Azure Media Services, indem er die Zugangsdaten für Ihr Media Services-Konto verwendet.

Das folgenden Codebeispiel zeigt eine **SubmitSubclipCallback**-Beispielmethode. Bei dieser Methode implementieren Sie den HTTP-POST für die Media Encoder Standard-Codierungsvoreinstellung. Wenn POST erfolgreich ausgeführt wurde (**result**), wird **Promise** aufgelöst. Andernfalls erfolgt eine Ablehnung mit Fehlerdetails.

```javascript
// Submit Subclip Callback
//
// Parameter:
// - subclip: object that represents the subclip (output contract).
//
// Returns: a Promise object that, when resolved, returns true if the operation was accept in the back-end; otherwise, returns false.
var onSubmitSubclip = function (subclip) {
    var promise = new Promise(function (resolve, reject) {
        // TODO: perform the back-end AJAX request to submit the subclip job.
        var result = true;

        if (/* everything turned out fine */ result) {
            resolve(result);
        }
        else {
            reject(Error("error details"));
        }
    });

    return promise;
};

var subclipper = new subclipper({
    selector: '#root',
    restVersion: '2.0',
    submitSubclipCallback: onSubmitSubclip,
});
```
Die Ausgabe der Auftragsübermittlung ist entweder die Media Encoder Standard-Codierungsvoreinstellung für gerenderte Aufträge oder die REST-API-Nutzlast für Filter für dynamische Manifeste.

## <a name="submitting-encoding-job-to-create-video"></a>Übermitteln von Codierungsaufträgen zum Erstellen von Videos
Sie können einen Media Encoder Standard-Codierungsauftrag einreichen, um einen Videoclip mit Bildpräzision zu erstellen. Der Codierungsauftrag erzeugt gerenderte Videos, eine neue fragmentierte MP4-Datei.

Die Auftragsausgabe für gerenderte Beschneidung ist ein JSON-Objekt mit den folgenden Eigenschaften:

```json
{
  /* Required */
  "name": "My New Subclip Output Name",

  /* Required: string specifying the format of the Json file.
   */
  /* NOTE: The REST API version, currently 2.0 is supported
   */
  "restVersion": "2.0"

  /* Required: array containing all the input Ids (both "asset" or "filter" type) used in the subclipper timeline;
  it must contain at least one item. */
  /* NOTE: when "output"."type" === "job", all items must match the "inputsIds"[i]."type" === "asset" condition;
  this array can be used in the back-end to get the input assets for the subclipping job with the Media Encoder Standard (MES) processor. */
  /* NOTE: when "output"."type" === "filter", there must be a single item in the array ("inputsIds".length === 1)
  that can be used in the back-end to get the source for the dynamic manifest filter (create or update). */
  "inputsIds": [{
    /* Required */
    "id": "my-asset-id-1",

    /* Required: must be one of the following values: "asset" or "filter" */
    "type": "asset"
  }, {
    /* Required */
    "id": "my-asset-id-2",

    /* Required: must be one of the following values: "asset" or "filter" */
    "type": "asset"
  }],

  /* Required */
  "output": {

    /* Required: must be one of the following values: "job" or "filter" */
    "type": "job",

    /* Required if "type" === "job" */
    /* NOTE: This is the preset for the Media Encoder Standard (MES) processor that can be used in the back-end to submit the subclip job.
    The encoding profile ("Codecs" property) depends on the "singleBitrateMp4Profile" and "multiBitrateMp4Profile" option parameters
    specified when creating the widget instance. */
    /* REFERENCE: https://docs.microsoft.com/azure/media-services/media-services-advanced-encoding-with-mes */
    "job": {
      "Version": 1.0,
      "Codecs": [{
          "KeyFrameInterval": "00:00:02",
          "SceneChangeDetection": true,
          "H264Layers": [{
            "Profile": "Auto",
            "Level": "auto",
            "Bitrate": 6750,
            "MaxBitrate": 6750,
            "BufferWindow": "00:00:05",
            "Width": 1920,
            "Height": 1080,
            "BFrames": 3,
            "ReferenceFrames": 3,
            "AdaptiveBFrame": true,
            "Type": "H264Layer",
            "FrameRate": "0/1"
          }],
          "Type": "H264Video"
        },
        {
          "Profile": "AACLC",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128,
          "Type": "AACAudio"
        }
      ],
      "Outputs": [{
        "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",
        "Format": {
          "Type": "MP4Format"
        }
      }],
      "Sources": [{
        "AssetId": "my-asset-id-1",
        "StartTime": "0.00:01:15.000",
        "Duration": "0.00:00:25.000"
      }, {
        "AssetId": "my-asset-id-2",
        "StartTime": "0.00:20:04.000",
        "Duration": "0.00:33:07.500"
      }]
    }
  }
}
```

Senden Sie den Media Encoder Standard-Codierungsauftrag mit der zugehörigen Voreinstellung, um den Codierungsauftrag auszuführen. In diesem Artikel finden Sie Details zum Übermitteln von Codierungsaufträgen mithilfe des [.NET SDK](https://docs.microsoft.com/azure/media-services/media-services-dotnet-encode-with-media-encoder-standard) oder der [REST-API](https://docs.microsoft.com/azure/media-services/media-services-rest-encode-asset).

## <a name="quickly-creating-video-clips-without-encoding"></a>Schnelles Erstellen von Videoclips ohne Codierung
Alternativ zum Erstellen eines Codierungsauftrags können Sie mit Azure Media Clipper dynamische Manifestfilter erstellen. Filter erfordern keine Codierung und können schnell erstellt werden, da kein neues Objekt erstellt wird. Die Ausgabe für eine Filterbeschneidung ist ein JSON-Objekt mit den folgenden Eigenschaften:

```json
{
  /* Required: Filter name (Alphanumeric characters and hyphens only, no whitespace) */
  "name": "FilterName",

  /* Required: string specifying the format of the Json file.
   */
  /* NOTE: This subclipper version always returns '2.0' in this field.
   */
  "restVersion": "2.0"

  /* Required: array containing all the input Ids (both "asset" or "filter" type) used in the subclipper timeline;
  it must contain at least one item. */
  /* NOTE: when "output"."type" === "job", all items must match the "inputsIds"[i]."type" === "asset" condition;
  this array can be used in the back-end to get the input assets for the subclipping job with the Media Encoder Standard (MES) processor. */
  /* NOTE: when "output"."type" === "filter", there must be a single item in the array ("inputsIds".length === 1)
  that can be used in the back-end to get the source for the dynamic manifest filter (create or update). */
  "inputsIds": [{
    /* Required */
    "id": "my-asset-id",

    /* Required: must be one of the following values: "asset" or "filter" */
    "type": "asset"
  }],

  /* Required */
  "output": {

    /* Required: must be one of the following values: "job" or "filter" */
    "type": "filter",

    /* Required if "type" === "filter" */
    /* REFERENCE: https://docs.microsoft.com/rest/api/media/operations/assetfilter */
    "filter": {
      "PresentationTimeRange": {
        "StartTimestamp": "340000000",
        "EndTimestamp": "580000000",
        "Timescale": "10000000"
      },
      "Tracks": [{
        "PropertyConditions": [{
          "Property": "Type",
          "Value": "video",
          "Operator": "Equal"
        }]
      }, {
        "PropertyConditions": [{
          "Property": "Type",
          "Value": "audio",
          "Operator": "Equal"
        }, {
          "Property": "Name",
          "Value": "audio-track-1",
          "Operator": "Equal"
        }]
      }, {
        "PropertyConditions": [{
          "Property": "Type",
          "Value": "text",
          "Operator": "Equal"
        }, {
          "Property": "Language",
          "Value": "en",
          "Operator": "Equal"
        }]
      }]
    }
  }
}
```

Übermitteln Sie zum Senden des REST-Aufrufs zur Erstellung dynamischer Manifestfilter die zugehörige Filternutzlast mit der [REST-API](https://docs.microsoft.com/azure/media-services/media-services-rest-dynamic-manifest).
