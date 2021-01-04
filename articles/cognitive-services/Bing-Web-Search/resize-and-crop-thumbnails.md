---
title: Ändern der Größe und Zuschneiden von Miniaturbildern – Bing-Websuche-API
titleSuffix: Azure Cognitive Services
description: Einige Antworten aus den Bing-Suche-APIs beinhalten URLs zu den von Bing bereitgestellten Miniaturansichten, die Sie in Größe und Beschaffenheit anpassen und zuschneiden können und die möglicherweise Abfrageparameter enthalten.
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: 05A08B01-89FF-4781-AFE7-08DA92F25047
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 07/08/2019
ms.author: aahi
ms.openlocfilehash: a85c5b2333418367742678a529b69c95164eda53
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96350482"
---
# <a name="resize-and-crop-thumbnail-images"></a>Ändern der Größe und Zuschneiden von Miniaturbildern

> [!WARNING]
> Die APIs der Bing-Suche werden von Cognitive Services auf Bing-Suchdienste umgestellt. Ab dem **30. Oktober 2020** müssen alle neuen Instanzen der Bing-Suche mit dem [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) dokumentierten Prozess bereitgestellt werden.
> APIs der Bing-Suche, die mit Cognitive Services bereitgestellt wurden, werden noch drei Jahre lang bzw. bis zum Ablauf Ihres Enterprise Agreement unterstützt (je nachdem, was zuerst geschieht).
> Eine Anleitung zur Migration finden Sie unter [Bing-Suchdienste](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Einige Antworten aus den Bing-Suche-APIs beinhalten URLs zu den von Bing bereitgestellten Miniaturansichten, die Sie in Größe und Beschaffenheit anpassen und zuschneiden können und die möglicherweise Abfrageparameter enthalten. Beispiel:

`https://<host>/th?id=AMMS_92772df988...&w=110&h=73&rs=1&qlt=80&cdv=1&pid=16.1`

Wenn Sie eine Teilmenge dieser Miniaturansichten anzeigen, stellen Sie eine Möglichkeit bereit, die restlichen Images anzuzeigen.

> [!NOTE]
> Stellen Sie sicher, dass das Zuschneiden und Ändern der Größe von Miniaturansichten ein Suchszenario bietet, das die Rechte Dritter respektiert, wie es die Bing-Suche-API in den [Verwendungs- und Anzeigeanforderungen](use-display-requirements.md) vorschreibt.

## <a name="resize-a-thumbnail"></a>Ändern der Größe von Miniaturansichten 

Um die Größe einer Miniaturansicht zu ändern, empfiehlt Bing, dass Sie nur einen der Suchparameter `w` (Breite) oder `h` (Höhe) in der URL der Miniaturansicht angeben. Wenn Sie nur die Höhe oder Breite angeben, kann Bing die ursprüngliche Perspektive des Bilds beibehalten. Geben Sie Breite und Höhe in Pixel an. 

Wenn die ursprüngliche Miniaturansicht z. B. 480 x 620 beträgt:

`https://<host>/th?id=JN.5l3yzwy%2f%2fHj59U6XhssIQ&pid=Api&w=480&h=620`

Und Sie möchten seine Größe verringern, dann setzten Sie den Parameter `w` auf einen neuen Wert (z. B. `336`) und entfernen den Parameter `h`:

`https://<host>/th?id=JN.5l3yzwy%2f%2fHj59U6XhssIQ&pid=Api&w=336`

Wenn Sie nur die Höhe oder Breite einer Miniaturansicht angeben, bleibt das ursprüngliche Seitenverhältnis des Bilds erhalten. Wenn Sie beide Parameter angeben und das Seitenverhältnis nicht eingehalten wird, fügt Bing dem Rand des Bilds einen weißen Bereich hinzu.

Wenn Sie also z. B. die Größe eines Bilds mit den Abmessungen 480 x 359 auf 200 x 200 festlegen, ohne das Bild zuzuschneiden, füllt das Bild zwar die gesamte Breite aus, oben und unten wird jedoch ein weißer Bereich mit 25 Pixeln hinzugefügt. Bei einem Bild mit den Abmessungen 359 x 480 würden links und rechts weiße Bereiche hinzugefügt werden. Wenn Sie das Bild zuschneiden, wird der weiße Bereich nicht hinzugefügt.  

Die folgende Abbildung zeigt die Originalgröße eines Miniaturbilds (480 x 300):  
  
![Originalbild einer Landschaft](./media/resize-crop/bing-resize-crop-landscape.png)  
  
Die folgende Abbildung zeigt das Bild nach der Größenänderung auf 200 x 200: Das Seitenverhältnis wird beibehalten, und am oberen und unteren Rand wird ein weißer Bereich hinzugefügt. (Der schwarze Rand dient lediglich zur Veranschaulichung der Auffüllung.)  
  
![Landschaftsbild nach der Größenänderung](./media/resize-crop/bing-resize-crop-landscape-resized.png)  

Wenn Sie Abmessungen angeben, die größer sind als die ursprüngliche Breite und Höhe des Bilds, dann fügt Bing am linken und oberen Rand weiße Bereiche hinzu.  

## <a name="request-different-thumbnail-sizes"></a>Anfordern verschiedener Miniaturansichtsgrößen

Um eine andere Größe der Miniaturansicht anzufordern, entfernen Sie alle Suchparameter aus der URL der Miniaturansicht, mit Ausnahme der Parameter `id` und `pid`. Fügen Sie dann entweder den `&w` (Breite) oder `&h` (Höhe)-Abfrageparameter mit der gewünschten Bildgröße in Pixel hinzu, aber nicht beides. Bing behält das ursprüngliche Seitenverhältnis des Bilds bei. 

Um die Breite des durch die obige URL angegebenen Bilds auf 165 Pixel zu erhöhen, verwenden Sie die folgende URL:

`https://<host>/th?id=AMMS_92772df988...&w=165&pid=16.1`

Wenn Sie ein Bild anfordern, das größer ist als die Originalgröße des Bilds, fügt Bing bei Bedarf einen weißen Bereich um das Bild hinzu. Wenn die Originalgröße des Bilds beispielsweise 474 x 316 beträgt und Sie `&w` auf 500 festgelegt haben, gibt Bing ein Bild mit 500 x 333 zurück. Dieses Image hat einen weißen Bereich von 8,5 Pixel am oberen und unteren Rand und 13 Pixel am linken und rechten Rand.

Um zu verhindern, dass Bing einen weißen Bereich hinzufügt, wenn die angeforderte Größe größer als die Originalgröße des Images ist, setzen Sie den Suchparameter `&p` auf 0. Wenn Sie beispielsweise den Parameter `&p=0` in die obige URL aufnehmen, gibt Bing ein 474 x 316-Image anstelle eines 500 x 333-Images zurück:

`https://<host>/th?id=AMMS_92772df988...&w=500&p=0&pid=16.1`

Wenn Sie sowohl `&w` als auch `&h` als Abfrageparameter angeben, behält Bing das Seitenverhältnis des Bilds bei und fügt bei Bedarf einen weißen Bereich hinzu. Wenn die Originalgröße des Bilds z. B. 474 x 316 beträgt und Sie die Parameter für Breite und Höhe auf 200 x 200 (`&w=200&h=200`) einstellen, gibt Bing ein Bild zurück, das oben und unten einen weißen Bereich von 33 Pixel enthält. Wenn Sie den Abfrageparameter `&p` angeben, gibt Bing ein Bild von 200 x 134 zurück.

## <a name="crop-a-thumbnail"></a>Zuschneiden einer Miniaturansicht 

Bilder können durch Angabe des Abfrageparameters `c` (Zuschneiden) zugeschnitten werden. Sie können die folgenden Werte verwenden:
  
- `4` &mdash; Blindes Seitenverhältnis  
- `7` &mdash; Intelligentes Seitenverhältnis  

### <a name="smart-ratio-cropping"></a>Zuschneiden nach intelligentem Seitenverhältnis

Wenn Sie das Zuschneiden nach intelligentem Seitenverhältnis anfordern (durch Setzen des Parameters `c` auf `7`), schneidet Bing ein Bild aus der Mitte des relevanten Bereichs nach außen, wobei das Seitenverhältnis des Bilds erhalten bleibt. Bei dem relevanten Bereich handelt es sich um den Bereich, der nach Ermessen von Bing die wichtigsten Elemente enthält. Die folgende Abbildung zeigt ein Beispiel für einen relevanten Bereich:  
  
![Relevanter Bereich](./media/resize-crop/bing-resize-crop-regionofinterest.png)

Wenn Sie die Größe eines Bilds ändern und den Zuschnitt mit intelligentem Seitenverhältnis verwenden, reduziert Bing das Bild unter Beibehaltung des Seitenverhältnisses auf die angeforderte Größe. Bing schneidet das Bild dann auf der Grundlage der geänderten Abmessungen zu. Wenn die Breite nach der Größenänderung also z. B. kleiner oder gleich der Höhe ist, schneidet Bing das Bild links und rechts von der Mitte des relevanten Bereichs zu. Andernfalls schneidet Bing das Bild ausgehend von der Mitte des relevanten Bereichs oben und unten zu.  
  
 
Die folgende Abbildung zeigt das Bild nach einer Verkleinerung auf 200 x 200 unter Verwendung des Zuschnitts mit intelligentem Seitenverhältnis: Da Bing das Bild von der linken oberen Ecke aus misst, wird der untere Teil des Bilds zugeschnitten. 
  
![Landschaftsbild, zugeschnitten auf 200 x 200](./media/resize-crop/bing-resize-crop-landscape200x200c7.png) 
  
Die folgende Abbildung zeigt das Bild nach einer Verkleinerung auf 200 x 100 unter Verwendung des Zuschnitts mit intelligentem Seitenverhältnis: Da Bing das Bild von der linken oberen Ecke aus misst, wird der untere Teil des Bilds zugeschnitten. 
   
![Landschaftsbild, zugeschnitten auf 200 x 100](./media/resize-crop/bing-resize-crop-landscape200x100c7.png)
  
Die folgende Abbildung zeigt das Bild nach einer Verkleinerung auf 100 x 200 unter Verwendung des Zuschnitts mit intelligentem Seitenverhältnis: Da Bing das Bild von der Mitte aus misst, werden der linke und rechte Teil des Bilds zugeschnitten.
  
![Landschaftsbild, zugeschnitten auf 100 x 200](./media/resize-crop/bing-resize-crop-landscape100x200c7.png) 

Falls Bing den relevanten Bereich des Bilds nicht bestimmen kann, wird der Dienst den Zuschnitt mit blindem Seitenverhältnis verwenden.  

### <a name="blind-ratio-cropping"></a>Zuschneiden nach blindem Seitenverhältnis

Wenn Sie das Zuschneiden nach blindem Seitenverhältnis anfordern (durch Setzen des Parameters `c` auf `4`), verwendet Bing die folgenden Regeln, um das Bild zuzuschneiden.  
  
- Bei `(Original Image Width / Original Image Height) < (Requested Image Width / Requested Image Height)` wird das Bild von oben links gemessen und unten zugeschnitten.  
- Bei `(Original Image Width / Original Image Height) > (Requested Image Width / Requested Image Height)` wird das Bild von der Mitte aus gemessen und nach links und rechts zugeschnitten.  

Die folgende Abbildung zeigt ein Bild im Hochformat mit den Abmessungen 225 x 300:  
  
![Originalbild einer Sonnenblume](./media/resize-crop/bing-resize-crop-sunflower.png)
  
Die folgende Abbildung zeigt das Bild nach einer Verkleinerung auf 200 x 200 unter Verwendung des Zuschnitts mit blindem Seitenverhältnis: Das Bild wird von der linken oberen Ecke aus gemessen, sodass der untere Teil des Bilds zugeschnitten wird.  
  
![Bild einer Sonnenblume, zugeschnitten auf 200 x 200](./media/resize-crop/bing-resize-crop-sunflower200x200c4.png)
  
Die folgende Abbildung zeigt das Bild nach einer Verkleinerung auf 200 x 100 unter Verwendung des Zuschnitts mit blindem Seitenverhältnis: Das Bild wird von der linken oberen Ecke aus gemessen, sodass der untere Teil des Bilds zugeschnitten wird.  
  
![Bild einer Sonnenblume, zugeschnitten auf 200 x 100](./media/resize-crop/bing-resize-crop-sunflower200x100c4.png)
  
Die folgende Abbildung zeigt das Bild nach einer Verkleinerung auf 100 x 200 unter Verwendung des Zuschnitts mit blindem Seitenverhältnis: Das Bild wird von der Mitte aus gemessen. Das führt dazu, dass das Bild links und rechts zugeschnitten wird.  
  
![Bild einer Sonnenblume, zugeschnitten auf 100 x 200](./media/resize-crop/bing-resize-crop-sunflower100x200c4.png)

## <a name="next-steps"></a>Nächste Schritte

* [Was sind die Bing-Suche-APIs?](bing-api-comparison.md)
* [Anforderungen für die Verwendung und Anzeige der Bing-Suche-API](use-display-requirements.md)