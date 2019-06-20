---
title: Erkennen von Gesichtern auf einem Bild – Gesichtserkennungs-API
titleSuffix: Azure Cognitive Services
description: Erfahren Sie, wie Sie die verschiedenen Daten verwenden, die vom Gesichtserkennungsfeature zurückgegeben werden.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: sbowles
ms.openlocfilehash: 46bd1bdd55725878bc7b1bd55d5e24b78d82aada
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66124552"
---
# <a name="get-face-detection-data"></a>Abrufen von Gesichtserkennungsdaten

In diesem Leitfaden wird gezeigt, wie mithilfe der Gesichtserkennung aus einem bestimmten Bild Attribute wie Geschlecht, Alter oder Körperhaltung extrahiert werden können. Die Codeausschnitte in diesem Leitfaden wurden in C# unter Verwendung der Clientbibliothek für die Gesichtserkennungs-API von Azure Cognitive Services geschrieben. Die gleiche Funktionalität ist auch über die [REST-API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) verfügbar.

In diesem Leitfaden wird Folgendes veranschaulicht:

- Abrufen der Position und der Dimensionen von Gesichtern auf einem Bild
- Abrufen der Position verschiedener Gesichtsmerkmale, z. B. Pupillen, Nase und Mund, auf einem Bild
- Schätzen des Geschlechts, des Alters, der Stimmung und anderer Attribute des erkannten Gesichts

## <a name="setup"></a>Einrichtung

Für diesen Leitfaden wird davon ausgegangen, dass Sie bereits ein [FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet)-Objekt namens `faceClient` mit einem Gesichtserkennungsabonnementschlüssel und einer Gesichtserkennungs-Endpunkt-URL erstellt haben. Hiervon ausgehend können Sie das Gesichtserkennungsfeature verwenden, indem Sie entweder – wie in diesem Leitfaden – [DetectWithUrlAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithurlasync?view=azure-dotnet) oder [DetectWithStreamAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithstreamasync?view=azure-dotnet) aufrufen. Eine Anleitung dazu, wie Sie dieses Feature einrichten, finden Sie unter [Schnellstart: Erkennen von Gesichtern in einem Bild mit dem Azure Face .NET SDK](../quickstarts/csharp-detect-sdk.md).

Das Hauptaugenmerk dieses Leitfadens liegt auf dem Detect-Aufruf, z. B. welche Argumente Sie übergeben und was Sie mit den zurückgegebenen Daten tun können. Wir empfehlen Ihnen, nur die Features abzufragen, die Sie benötigen. Für jeden Vorgang wird zusätzliche Zeit für die Durchführung benötigt.

## <a name="get-basic-face-data"></a>Abrufen von einfachen Gesichtsdaten

Rufen Sie die Methode auf, wenn Sie auf einem Bild Gesichter erkennen und deren Position auf einem Bild bestimmen möchten. Hierbei muss der Parameter _returnFaceId_ auf **true** festgelegt sein. Dies ist die Standardeinstellung.

```csharp
IList<DetectedFace> faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, false, null);
```

Sie können die zurückgegebenen [DetectedFace](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface?view=azure-dotnet)-Objekte mit der zugehörigen eindeutigen ID sowie ein Rechteck (Gesichtsrahmen) abfragen, mit dem die Pixelkoordinaten des Gesichts angegeben werden.

```csharp
foreach (var face in faces)
{
    string id = face.FaceId.ToString();
    FaceRectangle rect = face.FaceRectangle;
}
```

Weitere Informationen dazu, wie Sie die Position und die Dimensionen des Gesichts analysieren, finden Sie unter [FaceRectangle](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.facerectangle?view=azure-dotnet). Normalerweise enthält dieses Rechteck Augen, Augenbrauen, Nase und Mund. Die Oberseite des Kopfs, die Ohren und das Kinn sind meist nicht enthalten. Um das Gesichtsrechteck zum Ausschneiden eines vollständigen Kopfs oder zum Produzieren eines Porträts in der Halbtotalen zu verwenden, z. B. als Ausweisfoto, können Sie das Rechteck in alle Richtungen erweitern.

## <a name="get-face-landmarks"></a>Abrufen von Gesichtsmerkmalpunkten

[Gesichtsmerkmale](../concepts/face-detection.md#face-landmarks) bestehen aus mehreren einfach zu findenden Punkten im Gesicht. Beispiele hierfür sind die Pupillen oder die Nasenspitze. Legen Sie den Parameter _returnFaceLandmarks_ auf **true** fest, um die Punktdaten für Gesichtsmerkmale zu erhalten.

```csharp
IList<DetectedFace> faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, true, null);
```

Der folgende Code zeigt, wie Sie die Position von Nase und Pupillen abrufen können:

```csharp
foreach (var face in faces)
{
    var landmarks = face.FaceLandmarks;

    double noseX = landmarks.NoseTip.X;
    double noseY = landmarks.NoseTip.Y;

    double leftPupilX = landmarks.PupilLeft.X;
    double leftPupilY = landmarks.PupilLeft.Y;

    double rightPupilX = landmarks.PupilRight.X;
    double rightPupilY = landmarks.PupilRight.Y;
}
```

Sie können die Punktdaten für Gesichtsmerkmale auch verwenden, um die Ausrichtung eines Gesichts exakt zu bestimmen. Beispielsweise können Sie die Ausrichtung des Gesichts als Vektor vom Mittelpunkt des Munds zum Mittelpunkt der Augen definieren. Dieser Vektor wird mit dem folgenden Code berechnet:

```csharp
var upperLipBottom = landmarks.UpperLipBottom;
var underLipTop = landmarks.UnderLipTop;

var centerOfMouth = new Point(
    (upperLipBottom.X + underLipTop.X) / 2,
    (upperLipBottom.Y + underLipTop.Y) / 2);

var eyeLeftInner = landmarks.EyeLeftInner;
var eyeRightInner = landmarks.EyeRightInner;

var centerOfTwoEyes = new Point(
    (eyeLeftInner.X + eyeRightInner.X) / 2,
    (eyeLeftInner.Y + eyeRightInner.Y) / 2);

Vector faceDirection = new Vector(
    centerOfTwoEyes.X - centerOfMouth.X,
    centerOfTwoEyes.Y - centerOfMouth.Y);
```

Wenn Ihnen die Ausrichtung des Gesichts bekannt ist, können Sie den rechteckigen Gesichtsrahmen drehen, um ihn noch besser am Gesicht auszurichten. Zum Ausschneiden von Gesichtern aus einem Bild können Sie Bilder programmgesteuert drehen, damit Gesichter immer senkrecht dargestellt werden.

## <a name="get-face-attributes"></a>Abrufen von Gesichtsattributen

Zusätzlich zu Gesichtsrahmen und Gesichtsmerkmalpunkten kann die Gesichtserkennungs-API mehrere konzeptuelle Gesichtsattribute analysieren. Eine vollständige Liste finden Sie im Abschnitt [Attribute](../concepts/face-detection.md#attributes), in dem die Konzepte beschrieben sind.

Legen Sie den Parameter _returnFaceAttributes_ auf eine Liste mit [FaceAttributeType-Enumerationswerten](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype?view=azure-dotnet) fest, wenn Sie Gesichtsattribute analysieren möchten.

```csharp
var requiredFaceAttributes = new FaceAttributeType[] {
    FaceAttributeType.Age,
    FaceAttributeType.Gender,
    FaceAttributeType.Smile,
    FaceAttributeType.FacialHair,
    FaceAttributeType.HeadPose,
    FaceAttributeType.Glasses,
    FaceAttributeType.Emotion
};
var faces = await faceClient.DetectWithUrlAsync(imageUrl, true, false, requiredFaceAttributes);
```

Rufen Sie dann Verweise für die zurückgegebenen Daten ab, und führen Sie je nach Bedarf weitere Vorgänge durch.

```csharp
foreach (var face in faces)
{
    var attributes = face.FaceAttributes;
    var age = attributes.Age;
    var gender = attributes.Gender;
    var smile = attributes.Smile;
    var facialHair = attributes.FacialHair;
    var headPose = attributes.HeadPose;
    var glasses = attributes.Glasses;
    var emotion = attributes.Emotion;
}
```

Weitere Informationen zu den einzelnen Attributen finden Sie im Leitfaden [Face detection and attributes](../concepts/face-detection.md) (Gesichtserkennung und -attribute) zu den Konzepten.

## <a name="next-steps"></a>Nächste Schritte

In diesem Leitfaden haben Sie erfahren, wie die verschiedenen Funktionen der Gesichtserkennung verwendet werden. Als Nächstes integrieren Sie diese Features in Ihre App, indem Sie die Schritte eines ausführlichen Tutorials ausführen.

- [Tutorial: Erstellen einer WPF-App zum Anzeigen von Gesichtserkennungsdaten in einem Bild](../Tutorials/FaceAPIinCSharpTutorial.md)
- [Tutorial: Erstellen einer Android-App zum Erkennen und Umranden von Gesichtern in einem Bild](../Tutorials/FaceAPIinJavaForAndroidTutorial.md)

## <a name="related-topics"></a>Verwandte Themen

- [Referenzdokumentation (REST)](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
- [Referenzdokumentation (.NET SDK)](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/face?view=azure-dotnet)