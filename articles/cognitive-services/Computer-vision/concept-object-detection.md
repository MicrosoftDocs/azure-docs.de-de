---
title: Objekterkennung – maschinelles Sehen
titleSuffix: Azure Cognitive Services
description: Machen Sie sich mit Konzepten des Objekterkennungsfeatures der API für maschinelles Sehen und mit ihrer Verwendung sowie mit ihren Einschränkungen vertraut.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 4269209017ecc0afa740bc3ed56cbdcbd915201e
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96533841"
---
# <a name="detect-common-objects-in-images"></a>Erkennen von alltäglichen Objekten in Bildern

Die Objekterkennung ist vergleichbar mit dem [Tagging](concept-tagging-images.md), die API gibt aber die Koordinaten des umgebenden Felds (in Pixel) für jedes gefundene Objekt zurück. Wenn ein Bild z. B. einen Hund, eine Katze und eine Person enthält, werden diese Objekte bei der Erkennung zusammen mit ihren Koordinaten im Bild aufgelistet. Sie können diese Funktion verwenden, um die Beziehungen zwischen den Objekten in einem Bild zu verarbeiten. Außerdem können Sie ermitteln, ob mehrere Instanzen des gleichen Tags in einem Bild enthalten sind.

Die Erkennungs-API wendet Tags basierend auf den Objekten oder Lebewesen, die im Bild identifiziert wurden, an. Es gibt aktuell keine formale Beziehung zwischen der Taggingtaxonomie und der Objekterkennungstaxonomie. Auf konzeptioneller Ebene findet die Erkennungs-API nur Objekte und lebende Dinge, während die Tag-API auch kontextbezogene Begriffe wie „drinnen“ einfügen kann, die über die umgebenden Felder nicht gefunden werden können.

## <a name="object-detection-example"></a>Beispiel für die Objekterkennung

Die folgende JSON-Antwort veranschaulicht, was vom maschinellen Sehen beim Erkennen von Objekten im Beispielbild zurückgegeben wird.

![Eine Frau mit einem Microsoft Surface-Gerät in einer Küche](./Images/windows-kitchen.jpg)

```json
{
   "objects":[
      {
         "rectangle":{
            "x":730,
            "y":66,
            "w":135,
            "h":85
         },
         "object":"kitchen appliance",
         "confidence":0.501
      },
      {
         "rectangle":{
            "x":523,
            "y":377,
            "w":185,
            "h":46
         },
         "object":"computer keyboard",
         "confidence":0.51
      },
      {
         "rectangle":{
            "x":471,
            "y":218,
            "w":289,
            "h":226
         },
         "object":"Laptop",
         "confidence":0.85,
         "parent":{
            "object":"computer",
            "confidence":0.851
         }
      },
      {
         "rectangle":{
            "x":654,
            "y":0,
            "w":584,
            "h":473
         },
         "object":"person",
         "confidence":0.855
      }
   ],
   "requestId":"a7fde8fd-cc18-4f5f-99d3-897dcd07b308",
   "metadata":{
      "width":1260,
      "height":473,
      "format":"Jpeg"
   }
}
```

## <a name="limitations"></a>Einschränkungen

Es ist wichtig, die Einschränkungen bei der Objekterkennung zu beachten, damit Sie die Auswirkungen von falsch negativen Ergebnissen (ausgelassene Objekten) und begrenzten Details vermeiden oder minimieren können.

* Objekte werden in der Regel nicht erkannt, wenn sie klein (kleiner als 5 % des Bilds) sind.
* Objekte werden in der Regel nicht erkannt, wenn sie eng beieinander liegen (z.B. ein Stapel Teller).
* Objekte werden nicht nach Marken- oder Produktnamen unterschieden (z.B. verschiedene Arten von Mineralwasser in einem Verkaufsregal). Mithilfe der Funktion [Markenerkennung](concept-brand-detection.md) können Sie jedoch Markeninformationen aus einem Bild auslesen.

## <a name="use-the-api"></a>Verwenden der API

Die Funktion zur Erkennung von Objekten ist Teil der [Bildanalyse](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b)-API. Sie können diese API über ein natives SDK oder REST-Aufrufe aufrufen. Beziehen Sie `Objects` in den Abfrageparameter **visualFeatures** ein. Nachdem Sie die vollständige JSON-Antwort erhalten haben, analysieren Sie einfach die Zeichenfolge auf den Inhalt im Abschnitt `"objects"`.

* [Schnellstart: Verwenden der Clientbibliothek für maschinelles Sehen](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
