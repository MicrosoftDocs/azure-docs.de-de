---
title: Senden von Freihanddaten an die Freihanderkennungs-API
titleSuffix: Azure Cognitive Services
description: Erfahren Sie mehr über das Aufrufen der Freihandanalyse-API für unterschiedliche Anwendungen.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: conceptual
ms.date: 08/24/2020
ms.author: aahi
ms.openlocfilehash: 9ecf3ad43b46d491d5f15270790251a0bc4e882e
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2020
ms.locfileid: "94365362"
---
# <a name="send-ink-data-to-the-ink-recognizer-api"></a>Senden von Freihanddaten an die Freihanderkennungs-API 

[!INCLUDE [ink-recognizer-deprecation](../includes/deprecation-note.md)]

Digitale Freihandeingabe bezieht sich auf Technologien, die digitale Darstellungen von Eingaben wie Handschrift und Zeichnungen ermöglichen. Dies wird normalerweise mithilfe eines Digitalisierungsgeräts realisiert, das die Bewegungen der Eingabegeräte, z. B. eines Tablettstifts, erfasst. Mit der zunehmenden Bereitstellung funktionsreicher digitaler Freihandeingabeerfahrungen durch die Geräte ermöglichen künstliche Intelligenz und maschinelles Lernen die Erkennung von geschriebene Formen und Text in jedem Kontext. Die Freihanderkennungs-API ermöglicht es Ihnen, Freihandstriche zu senden und detaillierte Informationen über diese zu erhalten. 

## <a name="the-ink-recognizer-api-vs-ocr-services"></a>Die Freihanderkennungs-API im Vergleich zu OCR-Diensten

Die Freihanderkennungs-API verwendet keine optische Zeichenerkennung (Optical Character Recognition, OCR). OCR-Dienste verarbeiten die Pixeldaten aus Bildern, um Handschrift- und Texterkennung bereitzustellen. Dies wird mitunter als Offlineerkennung bezeichnet. Die Freihanderkennung-API benötigt stattdessen digitale Freihandstrichdaten, die während der Verwendung des Eingabegeräts erfasst werden. Die Verarbeitung digitaler Freihanddaten auf diese Weise kann im Vergleich zu OCR-Diensten genauere Erkennungsergebnisse erzeugen. 

## <a name="sending-ink-data"></a>Senden von Freihanddaten

Die Freihanderkennungs-API benötigt die X- und Y-Koordinaten, die die von einem Eingabegerät erstellten Freihandstriche darstellen, vom Zeitpunkt des Berührens der Erkennungsoberfläche bis zum Zeitpunkt des Abhebens. Die Punkte jedes Strichs müssen eine Zeichenfolge von durch Trennzeichen getrennten Werte und im JSON-Format sein, wie im folgenden Beispiel zu sehen. Darüber hinaus muss jeder einzelne Freihandstrich in jeder Anforderung eine eindeutige ID besitzen. Wenn sich die ID innerhalb derselben Anforderung wiederholt, gibt die API einen Fehler zurück. Für genaueste Erkennungsergebnisse sollten die Werte mindestens acht Ziffern nach dem Dezimaltrennzeichen besitzen. Als Ursprung (0,0) des Zeichenbereichs wird die obere linken Ecke des Zeichenbereichs angenommen.

> [!NOTE]
> Das folgende Beispiel ist kein gültiges JSON-Format. Eine vollständige Freihanderkennungsanforderung im JSON-Format finden Sie auf [GitHub](https://go.microsoft.com/fwlink/?linkid=2089909).
 
```json
{
  "language": "en-US",
  "strokes": [
   {
    "id": 43,
    "points": 
        "5.1365, 12.3845,
        4.9534, 12.1301,
        4.8618, 12.1199,
        4.7906, 12.2217,
        4.7906, 12.5372,
        4.8211, 12.9849,
        4.9534, 13.6667,
        5.0958, 14.4503,
        5.3299, 15.2441,
        5.6555, 16.0480,
        ..."
   },
    ...
  ]
}
```

## <a name="ink-recognizer-response"></a>Freihanderkennungsantwort

Die Freihanderkennungs-API gibt eine Analyseantwort zu den Objekten zurück, die sie aus dem Freihandinhalt erkannt hat. Die Antwort enthält Erkennungseinheiten, die die Beziehungen zwischen verschiedenen Freihandstrichen beschreiben. Beispielsweise werden Striche, die unterschiedliche, deutlich getrennte Formen bilden, in unterschiedliche Einheiten aufgenommen. Jede Einheit enthält detaillierte Informationen über ihre Freihandstriche, einschließlich des erkannten Objekts, seiner Koordinaten und anderer Zeichnungsattribute.

## <a name="shapes-recognized-by-the-ink-recognizer-api"></a>Von der Freihanderkennungs-API erkannte Formen

Die Freihanderkennungs-API kann die beim Erstellen von Notizen am häufigsten verwendeten Formen identifizieren. Die folgende Abbildung zeigt einige einfache Beispiele. Eine vollständige Liste der Formen und anderer Freihandinhalte, die von der API erkannt werden, finden Sie im [API-Referenzartikel](/rest/api/cognitiveservices/inkrecognizer/inkrecognizer). 

![Die Liste der von der Freihanderkennungs-API erkannten Formen](../media/shapes.png)

## <a name="recommended-calling-patterns"></a>Empfohlene Aufrufmuster

Sie können die Freihanderkennungs-REST-API mit verschiedenen Mustern gemäß Ihrer Anwendung aufrufen. 

### <a name="user-initiated-api-calls"></a>Vom Benutzer initiierte API-Aufrufe

Wenn Sie eine App erstellen, die Benutzereingaben annimmt (z. B. eine Notiz- oder Anmerkungs-App), empfiehlt es sich, diesen die Kontrolle über den Zeitpunkt zu gewähren, wann und welche Freihandeingaben an die Freihanderkennungs-API gesendet werden. Diese Funktionalität ist besonders nützlich, wenn sowohl Text als auch Formen im Zeichenbereich vorhanden sind und Benutzer für jedes dieser Elemente unterschiedliche Aktionen durchführen möchten. Erwägen Sie, Auswahlfunktionen hinzuzufügen (wie ein Lasso- oder ein anderes geometrisches Auswahltool), mit denen Benutzer auswählen können, was an die API gesendet wird.  

### <a name="app-initiated-api-calls"></a>Von der App initiierte API-Aufrufe

Sie können Ihre App -die Freihanderkennungs-API auch nach einem Timeout aufrufen lassen. Wenn Sie die aktuellen Freihandstriche regelmäßig an die API senden, können Sie Erkennungsergebnisse speichern, während sie erstellt werden und gleichzeitig die Reaktionszeit der API verbessern. Beispielsweise können Sie eine Zeile mit handschriftlichem Text an die API senden, nachdem erkannt wurde, dass Ihr Benutzer diesen fertig gestellt hat. 

Im Voraus über die Ergebnisse zu verfügen, liefert Ihnen Informationen über die Eigenschaften von Freihandstrichen und wie diese miteinander in Beziehung stehen. Beispielsweise welche Striche gruppiert sind, um dasselbe Wort, eine Zeile, Liste, einen Absatz oder eine Form zu bilden. Diese Informationen können die Freihandauswahlfunktionen Ihrer App verbessern, indem z. B. Gruppen von Strichen gleichzeitig ausgewählt werden können.

## <a name="integrate-the-ink-recognizer-api-with-windows-ink"></a>Integrieren der Freihanderkennungs-API in Windows Ink

[Windows Ink](/windows/uwp/design/input/pen-and-stylus-interactions) bietet Tools und Technologien, um digitale Freihanderfahrungen auf einer Vielzahl unterschiedlicher Geräte bereitzustellen. Sie können die Windows Ink-Plattform mit der Freihanderkennungs-API kombinieren, um Anwendungen zu erstellen, die digitale Freihandstriche anzeigen und interpretieren.

## <a name="next-steps"></a>Nächste Schritte

* [Was ist die Freihanderkennungs-API?](../overview.md)
* [Freihanderkennungs-REST-API-Referenz](/rest/api/cognitiveservices/inkrecognizer/inkrecognizer)

* Das Senden digitaler Freihandstrichdaten beginnen mithilfe von:
    * [C#](../quickstarts/csharp.md)
    * [Java](../quickstarts/java.md)
    * [JavaScript](../quickstarts/javascript.md)