---
title: Verwenden von HeadPose zum Anpassen des Gesichtsrechtecks
titleSuffix: Azure Cognitive Services
description: Erfahren Sie, wie Sie mit dem HeadPose-Attribut das Gesichtsrechteck automatisch drehen können.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: pafarley
ms.openlocfilehash: ddc5bc522c0d3ac258581f2a48a5c3b755302f01
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/26/2019
ms.locfileid: "64576493"
---
# <a name="use-the-headpose-attribute-to-adjust-the-face-rectangle"></a>Verwenden des HeadPose-Attributs zum Anpassen des Gesichtsrechtecks

In diesem Leitfaden verwenden Sie ein erkanntes Gesichtsattribut, HeadPose, um das Rechteck eines Gesichtsobjekts zu drehen. Der Beispielcode in diesem Leitfaden aus der Beispielanwendung [Cognitive Services Face WPF](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/Cognitive-Services-Face-WPF) verwendet das .NET SDK.

Das Gesichtsrechteck, das bei jedem erkannten Gesicht zurückgegeben wird, markiert die Position und Größe des Gesichts im Bild. Standardmäßig ist das Rechteck immer mit dem Bild ausgerichtet (seine Seiten sind perfekt vertikal und horizontal). Dies kann für das Umrahmen von abgewinkelten bzw. geneigten Gesichtern ineffizient sein. In Situationen, in denen Sie programmgesteuert Gesichter in einem Bild zuschneiden möchten, ist es von Vorteil, das zuzuschneidende Rechteck drehen zu können.

## <a name="explore-the-sample-code"></a>Untersuchen des Beispielcodes

Sie können das Gesichtsrechteck programmgesteuert drehen, indem Sie das HeadPose-Attribut verwenden. Wenn Sie dieses Attribut bei der Gesichtserkennung angeben (siehe [Informationen zur Gesichtserkennung](HowtoDetectFacesinImage.md)), können Sie es später abfragen. Die folgende Methode aus der App [Cognitive Services Face WPF](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/Cognitive-Services-Face-WPF) nimmt eine Liste von **DetectedFace**-Objekten und gibt eine Liste von **[Face](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/app-samples/Cognitive-Services-Face-WPF/Sample-WPF/Controls/Face.cs)** -Objekten zurück. **Face** ist hier eine benutzerdefinierte Klasse, die Gesichtsdaten speichert, einschließlich der aktualisierten Rechteckkoordinaten. Neue Werte werden für **top** (oben), **left** (links), **width** (Breite) und **height** (Höhe) berechnet, und ein neues Feld **FaceAngle** gibt die Drehung an.

```csharp
/// <summary>
/// Calculate the rendering face rectangle
/// </summary>
/// <param name="faces">Detected face from service</param>
/// <param name="maxSize">Image rendering size</param>
/// <param name="imageInfo">Image width and height</param>
/// <returns>Face structure for rendering</returns>
public static IEnumerable<Face> CalculateFaceRectangleForRendering(IList<DetectedFace> faces, int maxSize, Tuple<int, int> imageInfo)
{
    var imageWidth = imageInfo.Item1;
    var imageHeight = imageInfo.Item2;
    var ratio = (float)imageWidth / imageHeight;
    int uiWidth = 0;
    int uiHeight = 0;
    if (ratio > 1.0)
    {
        uiWidth = maxSize;
        uiHeight = (int)(maxSize / ratio);
    }
    else
    {
        uiHeight = maxSize;
        uiWidth = (int)(ratio * uiHeight);
    }

    var uiXOffset = (maxSize - uiWidth) / 2;
    var uiYOffset = (maxSize - uiHeight) / 2;
    var scale = (float)uiWidth / imageWidth;

    foreach (var face in faces)
    {
        var left = (int)(face.FaceRectangle.Left * scale + uiXOffset);
        var top = (int)(face.FaceRectangle.Top * scale + uiYOffset);

        // Angle of face rectangles, default value is 0 (not rotated).
        double faceAngle = 0;

        // If head pose attributes have been obtained, re-calculate the left & top (X & Y) positions.
        if (face.FaceAttributes?.HeadPose != null)
        {
            // Head pose's roll value acts directly as the face angle.
            faceAngle = face.FaceAttributes.HeadPose.Roll;
            var angleToPi = Math.Abs((faceAngle / 180) * Math.PI);

            // _____       | / \ |
            // |____|  =>  |/   /|
            //             | \ / |
            // Re-calculate the face rectangle's left & top (X & Y) positions.
            var newLeft = face.FaceRectangle.Left +
                face.FaceRectangle.Width / 2 -
                (face.FaceRectangle.Width * Math.Sin(angleToPi) + face.FaceRectangle.Height * Math.Cos(angleToPi)) / 2;

            var newTop = face.FaceRectangle.Top +
                face.FaceRectangle.Height / 2 -
                (face.FaceRectangle.Height * Math.Sin(angleToPi) + face.FaceRectangle.Width * Math.Cos(angleToPi)) / 2;

            left = (int)(newLeft * scale + uiXOffset);
            top = (int)(newTop * scale + uiYOffset);
        }

        yield return new Face()
        {
            FaceId = face.FaceId?.ToString(),
            Left = left,
            Top = top,
            OriginalLeft = (int)(face.FaceRectangle.Left * scale + uiXOffset),
            OriginalTop = (int)(face.FaceRectangle.Top * scale + uiYOffset),
            Height = (int)(face.FaceRectangle.Height * scale),
            Width = (int)(face.FaceRectangle.Width * scale),
            FaceAngle = faceAngle,
        };
    }
}
```

## <a name="display-the-updated-rectangle"></a>Anzeigen des aktualisierten Rechtecks

Von hier aus können Sie die zurückgegebenen **Face**-Objekte in Ihrer Anzeige verwenden. Die folgenden Zeilen aus [FaceDetectionPage.xaml](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/app-samples/Cognitive-Services-Face-WPF/Sample-WPF/Controls/FaceDetectionPage.xaml) zeigen, wie das neue Rechteck aus diesen Daten gerendert wird:

```xaml
 <DataTemplate>
    <Rectangle Width="{Binding Width}" Height="{Binding Height}" Stroke="#FF26B8F4" StrokeThickness="1">
        <Rectangle.LayoutTransform>
            <RotateTransform Angle="{Binding FaceAngle}"/>
        </Rectangle.LayoutTransform>
    </Rectangle>
</DataTemplate>
```

## <a name="next-steps"></a>Nächste Schritte

Ein funktionierendes Beispiel für gedrehte Gesichtsrechtecke finden Sie in der App [Cognitive Services Face WPF](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/Cognitive-Services-Face-WPF) auf GitHub. Oder schauen Sie sich die App [Face API HeadPose Sample](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples) (HeadPose-Beispiel für die Gesichtserkennungs-API) an, die das HeadPose-Attribut in Echtzeit verfolgt, um verschiedene Kopfbewegungen (Nicken, Schütteln) zu erkennen.