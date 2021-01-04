---
title: Angeben eines Erkennungsmodells – Gesichtserkennung
titleSuffix: Azure Cognitive Services
description: In diesem Artikel erfahren Sie, wie Sie das Erkennungsmodell auswählen, das Sie mit Ihrer Azure-Gesichtserkennungsanwendung verwenden möchten.
services: cognitive-services
author: longli0
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: longl
ms.custom: devx-track-csharp
ms.openlocfilehash: 016b8bf010f597e963e0901d1ec48486f79bbb35
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/29/2020
ms.locfileid: "92913125"
---
# <a name="specify-a-face-recognition-model"></a>Angeben eines Gesichtserkennungsmodells

In dieser Anleitung erfahren Sie, wie Sie ein Gesichtserkennungsmodell für die Gesichtserkennung, Identifizierung und Ähnlichkeitssuche mit dem Azure-Gesichtserkennungsdienst angeben.

Der Gesichtserkennungsdienst nutzt Machine Learning-Modelle für Vorgänge mit menschlichen Gesichter in Bildern. Wir verbessern die Genauigkeit unserer Modelle auf der Grundlage von Kundenfeedback und Forschungsergebnissen kontinuierlich, und wir stellen diese Verbesserungen als Modellaktualisierungen zur Verfügung. Entwickler können angeben, welche Version des Gesichtserkennungsmodells sie verwenden möchten. Sie können das Modell auswählen, das am besten zu ihrem Anwendungsfall passt.

Für den Azure-Gesichtserkennungsdienst sind drei Erkennungsmodelle verfügbar. Die Modelle _recognition_01_ (2017 veröffentlicht) und _recognition_02_ (2019 veröffentlicht) werden fortlaufend unterstützt, um Abwärtskompatibilität für Benutzer sicherzustellen, die Gesichterlisten oder **Personengruppen** verwenden, die mit diesen Modellen erstellt wurden. Eine **Gesichterliste** oder **Personengruppe** verwendet immer das Erkennungsmodell, mit dem sie erstellt wurde, und neue Gesichter werden diesem Modell beim Hinzufügen zugeordnet. Dies kann nach der Erstellung nicht mehr geändert werden, und Kunden müssen das entsprechende Erkennungsmodell zusammen mit der entsprechenden **Gesichterliste** oder **Personengruppe** verwenden.

Sie können jederzeit nach Wunsch zu späteren Erkennungsmodellen wechseln; allerdings müssen Sie neue Gesichterlisten und Personengruppen mit dem Erkennungsmodell Ihrer Wahl erstellen.

Das Modell _recognition_03_ (2020 veröffentlicht) ist das genaueste derzeit verfügbare Modell. Wenn Sie Neukunde sind, empfehlen wir die Verwendung dieses Modells. _Recognition_03_ bietet bessere Genauigkeit sowohl bei Ähnlichkeitsvergleichen als auch bei Vergleichen zur Personenzuordnung. Beachten Sie, dass jedes Modell unabhängig von den anderen arbeitet und dass ein Übereinstimmungsschwellenwert, der für ein Modell festgelegt wurde, nicht mit Werten in den anderen Erkennungsmodellen verglichen werden kann.

Im Folgenden erfahren Sie, wie Sie in verschiedenen Vorgängen zur Gesichtserkennung ein ausgewähltes Modell angeben und dabei Modellkonflikte vermeiden. Wenn Sie ein fortgeschrittener Benutzer sind und bestimmen möchten, ob ein Wechsel zum neuesten Modell sinnvoll ist, gehen Sie zum Abschnitt [Auswerten verschiedener Modelle](#evaluate-different-models), um das neue Modell zu testen und die Ergebnisse mit Ihrem aktuellen Dataset zu vergleichen.


## <a name="prerequisites"></a>Voraussetzungen

Sie sollten mit den Konzepten der KI-Gesichtserkennung und -identifikation vertraut sein. Wenn dies nicht der Fall ist, lesen Sie zuerst die folgenden Anleitungen:

* [Konzepte der Gesichtserkennung](../concepts/face-detection.md)
* [Konzepte der Gesichtserkennung](../concepts/face-recognition.md)
* [Gewusst wie: Gesichtserkennung in Bildern](HowtoDetectFacesinImage.md)

## <a name="detect-faces-with-specified-model"></a>Erkennen von Gesichtern mit dem angegebenen Modell

Die Gesichtserkennung identifiziert die besonderen visuellen Merkmale von menschlichen Gesichtern und findet deren Positionen im Begrenzungsrahmen. Zudem werden die Gesichtsmerkmale extrahiert und für die Identifikation gespeichert. Alle diese Informationen bilden die Darstellung eines Gesichts.

Das Erkennungsmodell wird beim Extrahieren der Gesichtsmerkmale verwendet, sodass Sie bei der Ausführung des Erkennungsvorgangs eine Modellversion angeben können.

Wenn Sie die API [Face – Detect] (Gesicht – Erkennen) verwenden, weisen Sie die Modellversion mit dem Parameter `recognitionModel` zu. Die verfügbaren Werte sind:
* recognition_01
* recognition_02
* recognition_03


Optional können Sie den Parameter _returnRecognitionModel_ (Standardwert **false** ) angeben, um festzulegen, ob _recognitionModel_ als Antwort zurückgegeben werden soll. Eine Anforderungs-URL für die REST-API [Face – Detect] sieht also wie folgt aus:

`https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes][&recognitionModel][&returnRecognitionModel]&subscription-key=<Subscription key>`

Wenn Sie die Clientbibliothek verwenden, können Sie den Wert für `recognitionModel` zuweisen, indem Sie eine Zeichenfolge übergeben, die die Version darstellt. Wenn Sie diesen Wert nicht zuweisen, wird die Standardmodellversion `recognition_01` verwendet. Nachfolgend ist ein Codebeispiel für die .NET-Clientbibliothek aufgeführt.

```csharp
string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
var faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, true, recognitionModel: "recognition_01", returnRecognitionModel: true);
```

## <a name="identify-faces-with-specified-model"></a>Identifizieren von Gesichtern mit dem angegebenen Modell

Der Gesichtserkennungsdienst kann Gesichtsdaten aus einem Bild extrahieren und sie mit einem **Person** -Objekt verknüpfen (z. B. über den API-Aufruf [Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b)). Mehrere **Person** -Objekte können zusammen in einem **PersonGroup** -Objekt gespeichert werden. Dann kann ein neues Gesicht mit einem **PersonGroup** -Objekt (mit dem Aufruf [Face - Identify]) verglichen und die entsprechende Person innerhalb dieser Gruppe identifiziert werden.

Ein **PersonGroup** -Objekt sollte ein eindeutiges Erkennungsmodell für alle **Person** -Objekte aufweisen, und Sie können dies mit dem Parameter `recognitionModel` beim Erstellen der Gruppe ( [PersonGroup - Create] oder [LargePersonGroup - Create]) angeben. Wenn Sie diesen Parameter nicht angeben, wird das ursprüngliche `recognition_01`-Modell verwendet. Eine Gruppe verwendet immer das Erkennungsmodell, mit dem sie erstellt wurde, und neue Gesichter werden diesem Modell beim Hinzufügen zugeordnet. Dies kann nach der Erstellung einer Gruppe nicht mehr geändert werden. Verwenden Sie die API [PersonGroup – Get] mit dem Parameter _returnRecognitionModel_ , der auf **true** festgelegt ist, um festzustellen, mit welchem Modell ein **PersonGroup** -Objekt konfiguriert ist.

Nachfolgend ist ein Codebeispiel für die .NET-Clientbibliothek aufgeführt.

```csharp
// Create an empty PersonGroup with "recognition_02" model
string personGroupId = "mypersongroupid";
await faceClient.PersonGroup.CreateAsync(personGroupId, "My Person Group Name", recognitionModel: "recognition_02");
```

In diesem Code wird ein **PersonGroup** -Objekt mit der ID `mypersongroupid` erstellt, das so eingerichtet ist, dass es das Modell _recognition_02_ zum Extrahieren von Gesichtsmerkmalen verwendet.

Dementsprechend müssen Sie angeben, welches Modell bei der Gesichtserkennung verwendet werden soll, um es mit diesem **PersonGroup** -Objekt zu vergleichen (über die API [Face – Detect]). Das von Ihnen verwendete Modell sollte immer mit der Konfiguration des **PersonGroup** -Objekts übereinstimmen, da bei der Operation andernfalls aufgrund inkompatibler Modelle Fehler auftreten können.

Es gibt keine Änderung bei der API [Face - Identify]. Sie müssen nur die Modellversion bei der Erkennung angeben.

## <a name="find-similar-faces-with-specified-model"></a>Suchen ähnlicher Gesichter mit dem angegebenen Modell

Sie können auch ein Erkennungsmodell für die Ähnlichkeitssuche angeben. Sie können die Modellversion mit `recognitionModel` beim Erstellen der Liste der Gesichter mit der API [FaceList – Create] oder [LargeFaceList – Create] zuweisen. Wenn Sie diesen Parameter nicht angeben, wird standardmäßig Modell `recognition_01` verwendet. Eine Gesichterliste verwendet immer das Erkennungsmodell, mit dem sie erstellt wurde, und neue Gesichter werden diesem Modell beim Hinzufügen zur Liste zugeordnet. Dies kann nach der Erstellung nicht mehr geändert werden. Verwenden Sie die API [FaceList – Get] mit dem Parameter _returnRecognitionModel_ , der auf **true** festgelegt ist, um festzustellen, mit welchem Modell eine Gesichterliste konfiguriert ist.

Nachfolgend ist ein Codebeispiel für die .NET-Clientbibliothek aufgeführt.

```csharp
await faceClient.FaceList.CreateAsync(faceListId, "My face collection", recognitionModel: "recognition_03");
```

Dieser Code erstellt eine Gesichterliste namens `My face collection`, die das Modell _recognition_03_ zum Extrahieren der Merkmale verwendet. Wenn Sie diese Gesichterliste für ein neu erkanntes Gesicht nach ähnlichen Gesichtern durchsuchen, dann muss dieses Gesicht mit dem Modell _recognition_03_ erkannt worden sein ( [Face – Detect]). Wie im vorherigen Abschnitt muss das Modell konsistent sein.

Es gibt keine Änderung bei der API [Face – Find Similar]. Sie geben nur die Modellversion bei der Erkennung an.

## <a name="verify-faces-with-specified-model"></a>Überprüfen von Gesichtern mit dem angegebenen Modell

Die API [Face – Verify] prüft, ob zwei Gesichter zu derselben Person gehören. Hinsichtlich der Erkennungsmodelle gibt es keine Änderung bei der Verify-API, aber Sie können nur Gesichter vergleichen, die mit demselben Modell erkannt wurden.

## <a name="evaluate-different-models"></a>Auswerten unterschiedlicher Modelle

Wenn Sie die Leistungen verschiedener Erkennungsmodelle mit Ihren eigenen Daten vergleichen möchten, müssen Sie wie folgt vorgehen:
1. Erstellen Sie drei Personengruppen, für die Sie _recognition_01_ , _recognition_02_ bzw. _recognition_03_ verwenden.
1. Verwenden Sie Ihre Bilddaten, um Gesichter zu erkennen und sie für **Personen** innerhalb dieser drei **Personengruppen** zu registrieren. 
1. Trainieren Sie Ihre Personengruppen mithilfe der API „PersonGroup – Train“.
1. Testen Sie mit „Face – Identify“ alle diese drei **Personengruppen** , und vergleichen Sie die Ergebnisse.


Wenn Sie normalerweise einen Übereinstimmungsschwellenwert angeben (ein Wert zwischen Null und Eins, der bestimmt, wie sicher das Modell sein muss, um ein Gesicht zu identifizieren), müssen Sie möglicherweise verschiedene Schwellenwerte für unterschiedlichen Modelle verwenden. Ein Schwellenwert für ein Modell ist nicht dazu vorgesehen, mit einem anderen geteilt zu werden und führt nicht unbedingt zu denselben Ergebnissen.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie gelernt, wie Sie das Erkennungsmodell für die Verwendung mit verschiedenen APIs für den Gesichtserkennungsdienst festlegen. Als nächstes befolgen Sie einen Schnellstart zu den ersten Schritten mit der Gesichtserkennung.

* [.NET SDK zur Gesichtserkennung](../quickstarts/client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp)
* [Python SDK zur Gesichtserkennung](../quickstarts/client-libraries.md?pivots=programming-language-python%253fpivots%253dprogramming-language-python)
* [Go SDK zur Gesichtserkennung](../quickstarts/client-libraries.md?pivots=programming-language-go%253fpivots%253dprogramming-language-go)

[Face – Detect]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d
[Face – Find Similar]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237
[Face - Identify]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239
[Face – Verify]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a
[PersonGroup - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244
[PersonGroup – Get]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246
[PersonGroup Person - Add Face]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b
[PersonGroup - Train]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249
[LargePersonGroup - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d
[FaceList – Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b
[FaceList – Get]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c
[LargeFaceList – Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc