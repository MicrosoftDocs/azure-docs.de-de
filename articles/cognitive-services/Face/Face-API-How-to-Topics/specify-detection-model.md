---
title: 'Angeben eines Erkennungsmodells: Gesichtserkennung'
titleSuffix: Azure Cognitive Services
description: In diesem Artikel erfahren Sie, wie Sie das Gesichtserkennungsmodell auswählen, das Sie mit Ihrer Azure-Gesichtserkennungsanwendung verwenden möchten.
services: cognitive-services
author: yluiu
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: yluiu
ms.custom: devx-track-csharp
ms.openlocfilehash: 5a70b10f7d22c9cc04427bdfbb44243fad457ba0
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/29/2020
ms.locfileid: "92913482"
---
# <a name="specify-a-face-detection-model"></a>Angeben eines Gesichtserkennungsmodells

In dieser Anleitung erfahren Sie, wie Sie ein Gesichtserkennungsmodell für den Azure-Gesichtserkennungsdienst angeben.

Der Gesichtserkennungsdienst nutzt Machine Learning-Modelle für Vorgänge mit menschlichen Gesichter in Bildern. Wir verbessern die Genauigkeit unserer Modelle auf der Grundlage von Kundenfeedback und Forschungsergebnissen kontinuierlich, und wir stellen diese Verbesserungen als Modellaktualisierungen zur Verfügung. Entwickler können angeben, welche Version des Gesichtserkennungsmodells sie verwenden möchten. Sie können das Modell auswählen, das am besten zu ihrem Anwendungsfall passt.

Im Folgenden erfahren Sie, wie Sie in bestimmten Gesichtserkennungsvorgängen das Gesichtserkennungsmodell angeben. Der Gesichtserkennungsdienst verwendet die Gesichtserkennung, sobald das Bild eines Gesichts in eine andere Datenform konvertiert wird.

Wenn Sie nicht sicher sind, ob Sie das neueste Modell verwenden sollten, gehen Sie zum Abschnitt [Auswerten unterschiedlicher Modelle](#evaluate-different-models) über, um das neue Modell zu testen und die Ergebnisse anhand Ihres aktuellen Datasets zu vergleichen.

## <a name="prerequisites"></a>Voraussetzungen

Sie sollten mit den Konzepten der KI-Gesichtserkennung vertraut sein. Falls nicht, informieren Sie sich im konzeptionellen Leitfaden oder in der Schrittanleitung:

* [Konzepte der Gesichtserkennung](../concepts/face-detection.md)
* [Gewusst wie: Gesichtserkennung in Bildern](HowtoDetectFacesinImage.md)

## <a name="detect-faces-with-specified-model"></a>Erkennen von Gesichtern mit dem angegebenen Modell

Bei der Gesichtserkennung werden die Positionen von menschlichen Gesichtern im Begrenzungsrahmen gesucht und ihre besonderen visuellen Merkmale identifiziert. Dabei werden die Gesichtsmerkmale extrahiert und gespeichert, um sie später in [Gesichtserkennungsvorgängen](../concepts/face-recognition.md) zu nutzen.

Bei Verwendung der API [Face – Detect] können Sie die Modellversion mit dem `detectionModel`-Parameter zuweisen. Die verfügbaren Werte sind:

* `detection_01`
* `detection_02`

Eine Anforderungs-URL für die REST-API [Face – Detect] sieht wie folgt aus:

`https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes][&recognitionModel][&returnRecognitionModel][&detectionModel]&subscription-key=<Subscription key>`

Wenn Sie die Clientbibliothek verwenden, können Sie den Wert für `detectionModel` zuweisen, indem Sie eine geeignete Zeichenfolge übergeben. Wenn Sie diesen Wert nicht zuweisen, verwendet die API die Standardmodellversion (`detection_01`). Nachfolgend ist ein Codebeispiel für die .NET-Clientbibliothek aufgeführt.

```csharp
string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
var faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, false, false, recognitionModel: "recognition_03", detectionModel: "detection_02");
```

## <a name="add-face-to-person-with-specified-model"></a>Hinzufügen eines Gesichts zu einem Person-Objekt mit einem angegebenen Modell

Der Gesichtserkennungsdienst kann Gesichtsdaten aus einem Bild extrahieren und über die API [PersonGroup Person – Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) einem **Person** -Objekt zuordnen. In diesem API-Aufruf können Sie das Erkennungsmodell auf die gleiche Weise wie bei [Face – Detect] angeben.

Nachfolgend ist ein Codebeispiel für die .NET-Clientbibliothek aufgeführt.

```csharp
// Create a PersonGroup and add a person with face detected by "detection_02" model
string personGroupId = "mypersongroupid";
await faceClient.PersonGroup.CreateAsync(personGroupId, "My Person Group Name", recognitionModel: "recognition_03");

string personId = (await faceClient.PersonGroupPerson.CreateAsync(personGroupId, "My Person Name")).PersonId;

string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
await client.PersonGroupPerson.AddFaceFromUrlAsync(personGroupId, personId, imageUrl, detectionModel: "detection_02");
```

Mit diesem Code wird ein **PersonGroup** -Objekt mit der ID `mypersongroupid` erstellt. Anschließend wird dem Objekt ein **Person** -Objekt hinzugefügt. Danach wird diesem **Person** -Objekt unter Verwendung des Modells `detection_02` ein Gesicht hinzugefügt. Wenn Sie den *detectionModel* -Parameter nicht angeben, verwendet die API das Standardmodell `detection_01`.

> [!NOTE]
> Sie müssen nicht dasselbe Erkennungsmodell für alle Gesichter in einem **Person** -Objekt verwenden. Es ist auch nicht erforderlich, dasselbe Erkennungsmodell bei der Erkennung neuer Gesichter zu verwenden, um diese mit einem **Person** -Objekt zu vergleichen (beispielsweise in der API [Face - Identify]).

## <a name="add-face-to-facelist-with-specified-model"></a>Hinzufügen eines Gesichts zu einem FaceList-Objekt mit einem angegebenen Modell

Sie können auch ein Erkennungsmodell angeben, wenn Sie ein Gesicht einem vorhandenen **FaceList** -Objekt hinzufügen. Nachfolgend ist ein Codebeispiel für die .NET-Clientbibliothek aufgeführt.

```csharp
await faceClient.FaceList.CreateAsync(faceListId, "My face collection", recognitionModel: "recognition_03");

string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
await client.FaceList.AddFaceFromUrlAsync(faceListId, imageUrl, detectionModel: "detection_02");
```

Mit diesem Code wird ein **FaceList** -Objekt namens `My face collection` erstellt. Anschließend wird dem Objekt mit dem Modell `detection_02` ein Gesicht hinzugefügt. Wenn Sie den *detectionModel* -Parameter nicht angeben, verwendet die API das Standardmodell `detection_01`.

> [!NOTE]
> Sie müssen nicht dasselbe Erkennungsmodell für alle Gesichter in einem **FaceList** -Objekt verwenden. Es ist auch nicht erforderlich, dasselbe Erkennungsmodell bei der Erkennung neuer Gesichter zu verwenden, um diese mit einem **FaceList** -Objekt zu vergleichen.

## <a name="evaluate-different-models"></a>Auswerten unterschiedlicher Modelle

Die verschiedenen Gesichtserkennungsmodelle wurden für unterschiedliche Aufgaben optimiert. In der folgenden Tabelle sind die Unterschiede übersichtlich dargestellt.

|**detection_01**  |**detection_02**  |
|---------|---------|
|Standardauswahl für alle Gesichtserkennungsvorgänge. | Im Mai 2019 veröffentlich und optional in allen Gesichtserkennungsvorgängen verfügbar.
|Nicht optimiert für kleine oder unscharfe Gesichter bzw. Gesichter in der Profilansicht.  | Verbesserte Genauigkeit bei kleinen oder unscharfen Gesichtern bzw. Gesichtern in der Profilansicht. |
|Gibt Gesichtsattribute (Kopfhaltung, Alter, Stimmung usw.) zurück, wenn sie im Erkennungsaufruf angegeben werden. |  Gibt keine Gesichtsattribute zurück.     |
|Gibt Orientierungspunkte im Gesicht zurück, wenn sie im Erkennungsaufruf angegeben werden.   | Gibt keine Orientierungspunkte im Gesicht zurück.  |

Am besten lässt sich die Leistungsfähigkeit der Modelle `detection_01` und `detection_02` vergleichen, indem Sie sie für ein Beispieldataset verwenden. Es wird empfohlen die API [Face – Detect] unter Verwendung der beiden Erkennungsmodelle für verschiedene Bilder aufzurufen, insbesondere für Bilder mit zahlreichen oder schwer erkennbaren Gesichtern. Achten Sie auf die Anzahl der Gesichter, die jedes Modell zurückgibt.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie gelernt, wie Sie das Erkennungsmodell angeben, das für verschiedene Gesichtserkennungs-APIs verwendet werden soll. Als nächstes befolgen Sie einen Schnellstart zu den ersten Schritten mit der Gesichtserkennung.

* [.NET SDK zur Gesichtserkennung](../quickstarts/client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp)
* [Python SDK zur Gesichtserkennung](../quickstarts/client-libraries.md?pivots=programming-language-python%253fpivots%253dprogramming-language-python)
* [Go SDK zur Gesichtserkennung](../quickstarts/client-libraries.md?pivots=programming-language-go%253fpivots%253dprogramming-language-go)

[Face – Detect]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d
[Face - Find Similar]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237
[Face - Identify]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239
[Face - Verify]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a
[PersonGroup - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244
[PersonGroup - Get]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246
[PersonGroup Person - Add Face]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b
[PersonGroup - Train]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249
[LargePersonGroup - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d
[FaceList - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b
[FaceList - Get]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c
[FaceList - Add Face]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250
[LargeFaceList - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc