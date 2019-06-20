---
title: Was ist die Maschinelles Sehen-API? Maschinelles Sehen
titlesuffix: Azure Cognitive Services
description: Über den Dienst für maschinelles Sehen haben Entwickler Zugriff auf erweiterte Algorithmen für die Bildverarbeitung und die Rückgabe von Informationen.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 03/04/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 3a307c32d15aa8c7809d728d98220b0819e91c7c
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66357390"
---
# <a name="what-is-computer-vision"></a>Worum handelt es sich bei maschinellem Sehen?

Über den Azure-Dienst für maschinelles Sehen haben Entwickler Zugriff auf erweiterte Algorithmen, die Bilder verarbeiten und Informationen zurückgeben. Für die Bildanalyse können Sie entweder ein Bild hochladen oder eine Bild-URL angeben. Die Bildverarbeitungsalgorithmen können Inhalte auf unterschiedliche Weise analysieren – je nachdem, für welche visuellen Merkmale Sie sich interessieren. So kann maschinelles Sehen beispielsweise erkennen, ob ein Bild anzügliche oder nicht jugendfreie Inhalte enthält, oder alle menschlichen Gesichter in einem Bild finden.

Sie können maschinelles Sehen in Ihrer Anwendung entweder über ein natives SDK oder durch direktes Aufrufen der REST-API nutzen. Auf dieser Seite erfahren Sie ganz allgemein, welche Möglichkeiten maschinelles Sehen bietet.

## <a name="analyze-images-for-insight"></a>Analysieren von Bildern, um Erkenntnisse zu gewinnen

Sie können Bilder analysieren, um visuelle Merkmale und Eigenschaften zu erkennen und Erkenntnisse zu gewinnen. Alle Features in der folgenden Tabelle werden von der [API für die Bildanalyse](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) bereitgestellt.

| Aktion | BESCHREIBUNG |
| ------ | ----------- |
|**[Markieren visueller Merkmale](concept-tagging-images.md)**|Erkennen und markieren Sie visuelle Merkmale in einem Bild auf der Grundlage von Tausenden von erkennbaren Objekten, Lebewesen, Landschaften und Aktionen. Wenn die Tags nicht eindeutig sind oder nicht zum Allgemeinwissen gehören, enthält die API-Antwort „Hinweise“, um die Bedeutung des Tags in einem bekannten Kontext zu erläutern. Die Markierung ist nicht auf den Hauptinhalt (etwa eine Person im Vordergrund) beschränkt, sondern bezieht auch die Umgebung (Innen- oder Außenbereich), Möbel, Werkzeuge, Pflanzen, Tiere, Zubehör, Geräte und Ähnliches mit ein.|
|**[Erkennen von Objekten](concept-object-detection.md)**| Die Objekterkennung ähnelt dem Tagging, die API gibt jedoch die Koordinaten des Begrenzungsrahmens für jedes angewendete Tag zurück. Wenn ein Bild z. B. einen Hund, eine Katze sowie eine Person enthält, werden diese Objekte bei der Erkennung zusammen mit ihren Koordinaten im Bild aufgelistet. Sie können diese Funktion verwenden, um weitere Beziehungen zwischen den Objekten in einem Bild zu verarbeiten. Außerdem können Sie feststellen, ob mehrere Instanzen des gleichen Tags in einem Bild enthalten sind.|
|**[Erkennen von Marken](concept-brand-detection.md)**|Erkennen Sie auf Bildern oder in Videos kommerzielle Marken auf der Grundlage einer Datenbank mit Tausenden Logos aus der ganzen Welt. Mit diesem Feature können Sie beispielsweise ermitteln, welche Marken in sozialen Medien besonders beliebt sind oder besonders gerne in Medien platziert werden.|
|**[Kategorisieren eines Bilds](concept-categorizing-images.md)**|Erkennen und kategorisieren Sie ein gesamtes Bild unter Verwendung einer [Kategorietaxonomie](Category-Taxonomy.md) mit über-/untergeordneten vererbbaren Hierarchien. Kategorien können einzeln oder in Kombination mit unseren neuen Markierungsmodellen verwendet werden.<br/>Als Sprache für die Markierung und Kategorisierung von Bildern wird derzeit nur Englisch unterstützt.|
|**[Beschreiben eines Bilds](concept-describing-images.md)**|Generieren Sie eine Beschreibung eines gesamten Bilds mit vollständigen Sätzen in lesbarer Sprache. Algorithmen für maschinelles Sehen generieren verschiedene Beschreibungen auf der Grundlage der im Bild erkannten Objekte. Die Beschreibungen werden jeweils ausgewertet, und eine Zuverlässigkeitsbewertung wird generiert. Dann wird eine Liste in der Reihenfolge von höchster Zuverlässigkeitsbewertung zu niedrigster zurückgegeben.|
|**[Erkennen von Gesichtern](concept-detecting-faces.md)** |Erkennen Sie Gesichter in einem Bild, und stellen Sie Informationen zu den einzelnen Gesichtern bereit. Maschinelles Sehen gibt für jedes erkannte Gesicht die Koordinaten, ein Rechteck, das Geschlecht und das Alter zurück.<br/>Maschinelles Sehen bietet einen Teil der Funktionen aus der [Gesichtserkennung](/azure/cognitive-services/face/). Der Gesichtserkennungsdienst ermöglicht eine detailliertere Analyse zur Erkennung von Gesichtsausdruck, Kopfhaltung und Ähnlichem.|
|**[Erkennen von Bildtypen](concept-detecting-image-types.md)**|Erkennen Sie Merkmale eines Bilds – also beispielsweise, ob es sich bei dem Bild um eine Strichzeichnung handelt oder wie wahrscheinlich es ist, dass es sich bei dem Bild um ein ClipArt handelt.|
|**[Erkennen domänenspezifischer Inhalte](concept-detecting-domain-content.md)**|Verwenden Sie Domänenmodelle, um domänenspezifische Inhalte (etwa berühmte Personen und Orientierungspunkte) in einem Bild zu erkennen. Wenn ein Bild also beispielsweise Personen enthält, kann maschinelles Sehen auf ein im Dienst enthaltenes Domänenmodell für berühmte Personen zurückgreifen und so ermitteln, ob es sich bei den Personen auf dem Bild um berühmte Personen handelt.|
|**[Erkennen des Farbschemas](concept-detecting-color-schemes.md)**|Analysieren Sie die Farben eines Bilds. Maschinelles Sehen kann ermitteln, ob es sich um ein Schwarzweißbild oder um ein Farbbild handelt. Bei Farbbildern kann maschinelles Sehen außerdem die dominante Farbe sowie Akzentfarben erkennen.|
|**[Generieren einer Miniaturansicht](concept-generating-thumbnails.md)**|Analysieren Sie den Inhalt eines Bilds, um eine geeignete Miniaturansicht für das Bild zu generieren. Die Maschinelles Sehen-API generiert zunächst eine hochwertige Miniaturansicht und analysiert dann die Objekte im Bild, um den *relevanten Bereich* zu bestimmen. Anschließend wird das Bild auf den relevanten Bereich zugeschnitten. Das Seitenverhältnis der generierten Miniaturansicht kann sich bei Bedarf vom Seitenverhältnis des ursprünglichen Bilds unterscheiden.|
|**[Abrufen des relevanten Bereichs](concept-generating-thumbnails.md#area-of-interest)**|Analysieren Sie den Inhalt eines Bilds, um die Koordinaten des *relevanten Bereichs* zurückzugeben. Hierbei handelt es sich um die gleiche Funktion, die auch zum Generieren einer Miniaturansicht verwendet wird. Anstatt das Bild zuzuschneiden, gibt die Maschinelles Sehen-API jedoch die Koordinaten des Begrenzungsrahmens des Bereichs zurück, sodass die aufrufende Anwendung das ursprüngliche Bild nach Bedarf ändern kann.|

## <a name="extract-text-from-images"></a>Extrahieren von Text aus Bildern

Mit maschinellem Sehen können Sie Text aus einem Bild unter Verwendung von [optischer Zeichenerkennung (Optical Character Recognition, OCR)](concept-recognizing-text.md#ocr-optical-character-recognition-api) in eine computerlesbare Zeichenfolge extrahieren. Bei Bedarf korrigiert die optische Zeichenerkennung die Drehung des erkannten Texts und liefert die Frame-Koordinaten der einzelnen Wörter. Die optische Zeichenerkennung unterstützt 25 Sprachen und ermittelt automatisch die Sprache des erkannten Texts.

Sie können auch die [Read-API](concept-recognizing-text.md#read-api) verwenden, um sowohl gedruckten als auch handschriftlichen Text aus Bildern und textlastigen Dokumenten zu extrahieren. Die Read-API verwendet aktualisierte Modelle und kann für verschiedene Objekte mit unterschiedlichen Oberflächen und Hintergründen verwendet werden. Hierzu zählen etwa Belege, Poster, Visitenkarten, Briefe und Whiteboards. Englisch ist derzeit die einzige unterstützte Sprache.

## <a name="moderate-content-in-images"></a>Moderieren von Bildinhalten

Maschinelles Sehen ermöglicht die [Erkennung anzüglicher und nicht jugendfreier Inhalte](concept-detecting-adult-content.md) in einem Bild sowie die Rückgabe einer Zuverlässigkeitsbewertung für beides. Der Filter für die Erkennung anzüglicher und nicht jugendfreier Inhalte kann mithilfe eines Schiebereglers nach Bedarf angepasst werden.

## <a name="use-containers"></a>Verwenden von Containern

[Verwenden Sie Container für maschinelles Sehen](computer-vision-how-to-install-containers.md), um gedruckten und handschriftlichen Text lokal zu erkennen. Installieren Sie dazu einen standardisierten Docker-Container, der sich näher bei Ihren Daten befindet.

## <a name="image-requirements"></a>Anforderungen an Images

Maschinelles Sehen kann Bilder analysieren, die folgende Anforderungen erfüllen:

- Das Bild muss im JPEG-, PNG-, GIF- oder BMP-Format vorliegen.
- Die Dateigröße muss weniger als 4 MB betragen.
- Das Bild muss größer als 50 x 50 Pixel sein.
  - Für OCR muss die Größe des Bilds zwischen 50 x 50 und 4.200 x 4.200 Pixel liegen.

## <a name="data-privacy-and-security"></a>Datenschutz und Sicherheit

Wie bei allen Cognitive Services-Diensten müssen Entwickler, die den Maschinelles Sehen-Dienst nutzen, die Microsoft-Richtlinien zu Kundendaten beachten. Weitere Informationen finden Sie im Microsoft Trust Center auf der [Seite zu Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices).

## <a name="next-steps"></a>Nächste Schritte

Nutzen Sie die folgende Schnellstartanleitung als Einführung in die Verwendung von maschinellem Sehen:

- [Schnellstart: Analysieren eines Bilds mit dem SDK für maschinelles Sehen und C#](quickstarts-sdk/csharp-analyze-sdk.md)
- [Schnellstart: Extrahieren von Text mit dem SDK für maschinelles Sehen und C#](quickstarts-sdk/csharp-hand-text-sdk.md)
- [Schnellstart: Generieren einer Miniaturansicht mit dem SDK für maschinelles Sehen und C#](quickstarts-sdk/csharp-thumb-sdk.md)
