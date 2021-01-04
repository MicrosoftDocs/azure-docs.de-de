---
title: 'Erkennen von Gesichtern auf einem Bild: Gesichtserkennung'
titleSuffix: Azure Cognitive Services
description: In diesem Leitfaden wird gezeigt, wie mithilfe der Gesichtserkennung aus einem bestimmten Bild Attribute wie Geschlecht, Alter oder Körperhaltung extrahiert werden können.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: sbowles
ms.custom: devx-track-csharp
ms.openlocfilehash: f7a740b1015bda80000f65180eda2c5e618670da
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/29/2020
ms.locfileid: "92911238"
---
# <a name="get-face-detection-data"></a>Abrufen von Gesichtserkennungsdaten

In diesem Leitfaden wird gezeigt, wie mithilfe der Gesichtserkennung aus einem bestimmten Bild Attribute wie Geschlecht, Alter oder Körperhaltung extrahiert werden können. Die Codeausschnitte in diesem Leitfaden wurden in C# unter Verwendung der Clientbibliothek für die Gesichtserkennung von Azure Cognitive Services geschrieben. Die gleiche Funktionalität ist auch über die [REST-API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) verfügbar.

In diesem Leitfaden wird Folgendes veranschaulicht:

- Abrufen der Position und der Dimensionen von Gesichtern auf einem Bild
- Abrufen der Position verschiedener Gesichtsmerkmale, z. B. Pupillen, Nase und Mund, auf einem Bild
- Schätzen des Geschlechts, des Alters, der Stimmung und anderer Attribute des erkannten Gesichts

## <a name="setup"></a>Einrichten

Für diesen Leitfaden wird davon ausgegangen, dass Sie bereits ein [FaceClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet)-Objekt namens `faceClient` mit einem Gesichtserkennungsabonnementschlüssel und einer Gesichtserkennungs-Endpunkt-URL erstellt haben. Hiervon ausgehend können Sie das Gesichtserkennungsfeature verwenden, indem Sie entweder – wie in diesem Leitfaden – [DetectWithUrlAsync](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithurlasync?view=azure-dotnet) oder [DetectWithStreamAsync](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithstreamasync?view=azure-dotnet) aufrufen. Anweisungen zum Einrichten dieses Feature finden Sie in den folgenden Schnellstarts.

Das Hauptaugenmerk dieses Leitfadens liegt auf dem Detect-Aufruf, z. B. welche Argumente Sie übergeben und was Sie mit den zurückgegebenen Daten tun können. Wir empfehlen Ihnen, nur die Features abzufragen, die Sie benötigen. Für jeden Vorgang wird zusätzliche Zeit für die Durchführung benötigt.

## <a name="get-basic-face-data"></a>Abrufen von einfachen Gesichtsdaten

Um Gesichter zu suchen und ihre Positionen auf einem Bild abzurufen, rufen Sie die Methoden [DetectWithUrlAsync](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithurlasync?view=azure-dotnet) oder [DetectWithStreamAsync](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithstreamasync?view=azure-dotnet) mit auf **true** festgelegtem _returnFaceId_ -Parameter auf. Dies ist die Standardeinstellung.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="basic1":::

Sie können die zurückgegebenen [DetectedFace](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface?view=azure-dotnet)-Objekte mit der zugehörigen eindeutigen ID sowie ein Rechteck (Gesichtsrahmen) abfragen, mit dem die Pixelkoordinaten des Gesichts angegeben werden.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="basic2":::

Weitere Informationen dazu, wie Sie die Position und die Dimensionen des Gesichts analysieren, finden Sie unter [FaceRectangle](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.facerectangle?view=azure-dotnet). Normalerweise enthält dieses Rechteck Augen, Augenbrauen, Nase und Mund. Die Oberseite des Kopfs, die Ohren und das Kinn sind meist nicht enthalten. Um das Gesichtsrechteck zum Ausschneiden eines vollständigen Kopfs oder zum Produzieren eines Porträts in der Halbtotalen zu verwenden, z. B. als Ausweisfoto, können Sie das Rechteck in alle Richtungen erweitern.

## <a name="get-face-landmarks"></a>Abrufen von Gesichtsmerkmalpunkten

[Gesichtsmerkmale](../concepts/face-detection.md#face-landmarks) bestehen aus mehreren einfach zu findenden Punkten im Gesicht. Beispiele hierfür sind die Pupillen oder die Nasenspitze. Um Daten zu Gesichtsmerkmalen abzurufen, legen Sie den Parameter _detectionModel_ auf **DetectionModel.Detection01** und den Parameter _returnFaceLandmarks_ auf **true** fest.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="landmarks1":::

Der folgende Code zeigt, wie Sie die Position von Nase und Pupillen abrufen können:

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="landmarks2":::

Sie können die Punktdaten für Gesichtsmerkmale auch verwenden, um die Ausrichtung eines Gesichts exakt zu bestimmen. Beispielsweise können Sie die Ausrichtung des Gesichts als Vektor vom Mittelpunkt des Munds zum Mittelpunkt der Augen definieren. Dieser Vektor wird mit dem folgenden Code berechnet:

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="direction":::

Wenn Ihnen die Ausrichtung des Gesichts bekannt ist, können Sie den rechteckigen Gesichtsrahmen drehen, um ihn noch besser am Gesicht auszurichten. Zum Ausschneiden von Gesichtern aus einem Bild können Sie Bilder programmgesteuert drehen, damit Gesichter immer senkrecht dargestellt werden.

## <a name="get-face-attributes"></a>Abrufen von Gesichtsattributen

Zusätzlich zu Gesichtsrahmen und Gesichtsmerkmalpunkten kann die Gesichtserkennungs-API mehrere konzeptuelle Gesichtsattribute analysieren. Eine vollständige Liste finden Sie im Abschnitt [Attribute](../concepts/face-detection.md#attributes), in dem die Konzepte beschrieben sind.

Um Gesichtsattribute zu analysieren, legen Sie den Parameter _detectionModel_ auf **DetectionModel.Detection01** und den Parameter _returnFaceAttributes_ auf eine Liste von [FaceAttributeType Enum](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype?view=azure-dotnet)-Werten fest.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="attributes1":::

Rufen Sie dann Verweise für die zurückgegebenen Daten ab, und führen Sie je nach Bedarf weitere Vorgänge durch.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="attributes2":::

Weitere Informationen zu den einzelnen Attributen finden Sie im Leitfaden [Face detection and attributes](../concepts/face-detection.md) (Gesichtserkennung und -attribute) zu den Konzepten.

## <a name="next-steps"></a>Nächste Schritte

In diesem Leitfaden haben Sie erfahren, wie die verschiedenen Funktionen der Gesichtserkennung verwendet werden. Als Nächstes integrieren Sie diese Features in Ihre App, indem Sie die Schritte eines ausführlichen Tutorials ausführen.

- [Tutorial: Erstellen einer WPF-App zum Anzeigen von Gesichtserkennungsdaten in einem Bild](../Tutorials/FaceAPIinCSharpTutorial.md)

## <a name="related-topics"></a>Verwandte Themen

- [Referenzdokumentation (REST)](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
- [Referenzdokumentation (.NET SDK)](/dotnet/api/overview/azure/cognitiveservices/client/faceapi?view=azure-dotnet)