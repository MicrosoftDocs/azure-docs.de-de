---
title: Gesichtserkennung und -attribute – Konzepte
titleSuffix: Azure Cognitive Services
description: Bei der Gesichtserkennung werden menschliche Gesichter in einem Bild gesucht und optional verschiedene Arten von gesichtsbezogenen Daten zurückgegeben.
services: cognitive-services
author: PatrickFarley
manager: nitime
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: pafarley
ms.openlocfilehash: 92b19941f34b9bf5656c9beb188a68d2cf01f674
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2020
ms.locfileid: "92504128"
---
# <a name="face-detection-and-attributes"></a>Gesichtserkennung und -attribute

Dieser Artikel erläutert die Konzepte der Gesichtserkennung und der Attributdaten für Gesichter. Bei der Gesichtserkennung werden menschliche Gesichter in einem Bild gesucht und optional verschiedene Arten von gesichtsbezogenen Daten zurückgegeben.

Sie verwenden den Vorgang [Face – Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), um Gesichter in einem Bild zu erkennen. Jedes erkannte Gesicht entspricht mindestens einem faceRectangle-Feld in der Antwort. Dieser Satz von Pixelkoordinaten für links, oben, Breite und Höhe markieren das erkannte Gesicht. Mithilfe dieser Koordinaten finden Sie die Position des Gesichts und seine Größe. In der API-Antwort werden Gesichter in Reihenfolge nach Größe – vom größten zum kleinsten Gesicht – aufgeführt.

## <a name="face-id"></a>Gesichts-ID

Die Gesichtserkennungs-ID ist eine Zeichenfolge mit dem eindeutigen Bezeichner für jedes erkannte Gesicht in einem Bild. Sie können eine Gesichtserkennungs-ID in Ihrem API-Aufruf [Face – Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) anfordern.

## <a name="face-landmarks"></a>Gesichtszüge

Gesichtsmerkmale bestehen aus mehreren einfach zu findenden Punkten im Gesicht. Beispiel sind die Pupillen oder die Nasenspitze. Standardmäßig sind 27 Bezugspunkte vordefiniert. Die folgende Abbildung zeigt alle 27 Punkte:

![Abbildung eines Gesichts mit allen 27 beschrifteten Gesichtsmerkmalpunkten](../Images/landmarks.1.jpg)

Die Koordinaten der Punkte werden in Pixel zurückgegeben.

## <a name="attributes"></a>Attributes

Attribute sind ein Satz von Merkmalen, die optional durch die API [Face – Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) erkannt werden können. Die folgenden Attribute können erkannt werden:

* **Alter:** Das geschätzte Alter eines bestimmten Gesichts in Jahren.
* **Weichzeichnung:** Die Unschärfe des Gesichts im Bild. Dieses Attribut gibt einen Wert zwischen 0 und 1 und eine informelle Bewertung von gering, mittel oder hoch zurück.
* **Emotionen:** Eine Liste von Emotionen und deren Erkennungssicherheit für das jeweilige Gesicht. Die Zuverlässigkeitsbewertungen werden normalisiert, und die Bewertungen über alle Emotionen hinweg ergeben eine Summe von eins. Die zurückgegebenen Emotionen sind Glück, Traurigkeit, Neutralität, Wut, Verachtung, Ekel, Überraschung und Angst.
* **Belichtung:** Die Belichtung des Gesichts im Bild. Dieses Attribut gibt einen Wert zwischen 0 und 1 und die informelle Bewertung underExposure, goodExposure oder overExposure zurück.
* **Gesichtsbehaarung:** Das geschätzte Vorhandensein von Gesichtsbehaarung und die Länge für das jeweilige Gesicht.
* **Geschlecht:** Das geschätzte Geschlecht des jeweiligen Gesichts. Mögliche Werte sind „male“, „female“ und „genderless“.
* **Brille:** Gibt an, ob das Gesicht eine Brille aufweist. Mögliche Werte sind NoGlasses, ReadingGlasses, SunGlasses und SwimmingGoggles.
* **Haare:** Die Art der Haare des Gesichts. Dieses Attribut zeigt an, ob die Haare sichtbar sind, ob Haarausfall erkannt wurde und welche Farbe die Haare haben.
* **Kopfhaltung:** Die Ausrichtung des Gesichts im dreidimensionalen Raum. Dieses Attribut wird durch die Roll-Nick-Gier-Winkel in Grad beschrieben. Als Wertebereiche gelten –90 Grad bis 90 Grad, –90 Grad bis 90 Grad bzw. –90 Grad bis 90 Grad. Die Winkelzuordnungen sind im folgenden Diagramm dargestellt:

    ![Kopf mit Bezeichnungen für Roll-, Nick- und Gierachse](../Images/headpose.1.jpg)
* **Makeup:** Gibt an, ob das Gesicht Makeup trägt. Dieses Attribut gibt einen booleschen Wert für eyeMakeup und lipMakeup zurück.
* **Rauschen:** Visuelle Störungen, die im Gesicht erkannt wurden. Dieses Attribut gibt einen Wert zwischen 0 und 1 und eine informelle Bewertung von gering, mittel oder hoch zurück.
* **Okklusion:** Gibt an, ob Objekte Teile des Gesichts verdecken. Dieses Attribut gibt einen booleschen Wert für die eyeOccluded, foreheadOccluded und mouthOccluded zurück.
* **Lächeln:** Gibt die Stärke des Lächelns des jeweiligen Gesichts an. Dieser Wert liegt zwischen 0 (kein Lächeln) und 1 (deutliches Lächeln).

> [!IMPORTANT]
> Gesichtsattribute werden mithilfe statistischer Algorithmen vorhergesagt. Sie sind möglicherweise nicht immer genau. Treffen Sie Entscheidungen auf Grundlage von Attributdaten mit Umsicht.

## <a name="input-data"></a>Eingabedaten

Anhand der folgenden Tipps können Sie sicherstellen, dass Ihre Eingabebilder möglichst genaue Erkennungsergebnisse liefern:

* Als Eingabebildformate werden JPEG, PNG, GIF (der erste Frame) und BMP unterstützt.
* Die Bilddateien dürfen maximal 6 MB groß sein.
* Die erkennbare Gesichtsgröße reicht von 36 x 36 bis 4.096 x 4.096 Pixel. Außerhalb dieses Bereichs liegende Gesichter werden nicht erkannt.
* Einige Gesichter können möglicherweise aufgrund technischer Probleme nicht erkannt werden. Extreme Gesichtswinkel (Kopfhaltung) oder verdeckte Gesichter (Objekte wie Sonnenbrille oder Hände bedecken Teile des Gesichts) können die Erkennung beeinträchtigen. Frontalansichten und nahezu der Frontalansicht entsprechende Ansichten von Gesichtern führen zu den besten Ergebnissen.

Wenn Sie Gesichter in einem Videofeed ermitteln möchten, können Sie die Leistung verbessern, indem Sie bestimmte Einstellungen an Ihrer Videokamera anpassen:

* **Glättung:** Viele Videokameras wenden einen glättenden Effekt an. Sie sollten diesen deaktivieren, da er einen Weichzeichnungseffekt zwischen den Frames einfügt und damit die Schärfe reduziert.
* **Belichtungszeit:** Eine kürzere Belichtungszeit verringert die Anzahl der Bewegungen zwischen den Frames und macht damit die einzelnen Frame deutlicher. Es werden Belichtungszeiten von 1/60 Sekunde oder kürzer empfohlen.
* **Verschlusswinkel:** Einige Kameras geben anstelle der Belichtungszeit den Verschlusswinkel an. Sie sollten nach Möglichkeit einen niedrigeren Verschlusswinkel verwenden. Dies führt zu deutlicheren Videoframes.

    >[!NOTE]
    > Eine Kamera mit einem niedrigeren Verschlusswinkel nimmt pro Frame weniger Licht auf, sodass das Bild dunkler wird. Sie müssen die beste Einstellung selbst bestimmen.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie nun mit den Konzepten der Gesichtserkennung vertraut sind, erfahren Sie, wie Sie ein Skript schreiben, das Gesichter in einem vorgegebenen Bild erkennt.

* [Gesichtserkennung in einem Bild](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)