---
title: Optische Zeichenerkennung (OCR) – Maschinelles Sehen
titleSuffix: Azure Cognitive Services
description: Konzepte, die im Zusammenhang mit optischen Zeichenerkennung (OCR) in Bildern und Dokumenten mit gedrucktem und handschriftlichem Text stehen unter Verwendung der API für maschinelles Sehen.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 08/11/2020
ms.author: pafarley
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 37a989082b63dc101bb519fea1cc4ef16c76ae49
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/05/2020
ms.locfileid: "96621534"
---
# <a name="optical-character-recognition-ocr"></a>Optische Zeichenerkennung (OCR)

Die API für maschinelles Sehen von Azure umfasst Funktionen zur optischen Zeichenerkennung (Optical Character Recognition, OCR), mit denen gedruckter oder handschriftlicher Text aus Bildern extrahiert wird. Sie können Text aus Bildern, z. B. Fotos von Kfz-Kennzeichen oder von Containern mit Seriennummern, sowie aus Dokumenten, z. B. Rechnungen, Finanzberichten, Artikeln usw., extrahieren.

## <a name="read-api"></a>Lese-API 

Die [Read-API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005) für maschinelles Sehen ist die neueste OCR-Technologie von Azure ([Neuerungen kennenlernen](./whats-new.md)), mit der sich gedruckter Text in mehreren Sprachen, handschriftlicher Text (nur Englisch), Ziffern und Währungssymbole aus Bildern und mehrseitigen PDF-Dokumenten extrahieren lassen. Die API ist für das Extrahieren von Text aus textlastigen Bildern und mehrseitigen PDF-Dokumenten mit gemischten Sprachen optimiert. Sie unterstützt sowohl die Erkennung von gedrucktem als auch von handschriftlichem Text im selben Bild oder Dokument.

![So konvertiert die optische Zeichenerkennung Bilder und Dokumente in eine strukturierte Ausgabe mit extrahiertem Text](./Images/how-ocr-works.svg)

## <a name="input-requirements"></a>Eingabeanforderungen
Der Aufruf **Read** nimmt Bilder und Dokumente als Eingabe entgegen. Es bestehen die folgenden Anforderungen:

* Unterstützte Dateiformate: JPEG, PNG, BMP, PDF und TIFF
* Für PDF- und TIFF-Dateien werden bis zu 2000 Seiten (nur die ersten beiden Seiten für den Free-Tarif) verarbeitet.
* Die Dateigröße muss weniger als 50 MB (4 MB für den Free-Tarif) betragen und eine Größe von mindestens 50 x 50 Pixel und höchstens 10000 × 10000 Pixel aufweisen. 
* Die Abmessungen bei PDF-Dateien dürfen maximal 17 × 17 Zoll betragen. Dies entspricht den Papierformaten Legal oder DIN A3 und kleineren Formaten.

### <a name="read-32-preview-allows-selecting-pages"></a>Die Vorschauversion von Read 3.2 ermöglicht die Auswahl von Seiten
Mit der [Read 3.2-API (Vorschauversion)](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-1/operations/5d986960601faab4bf452005) können Sie bei großen, mehrseitigen Dokumenten bestimmte Seitenzahlen oder Seitenbereiche als Eingabeparameter angeben, um Text nur von diesen Seiten zu extrahieren. Dies ist ein neuer Eingabeparameter zusätzlich zu dem optionalen Sprachparameter.

> [!NOTE]
> **Spracheneingabe** 
>
> Der [Read-Aufruf](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005) weist einen optionalen Anforderungsparameter für die Sprache auf. Dies ist der BCP-47-Sprachcode des Texts im Dokument. Read unterstützt die automatische Sprachidentifikation und mehrsprachige Dokumente. Geben Sie also nur einen Sprachcode an, wenn Sie erzwingen möchten, dass das Dokument in dieser spezifischen Sprache verarbeitet wird.

## <a name="the-read-call"></a>Read-Aufruf

Beim [Read-Aufruf](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005) der Read-API wird ein Bild oder PDF-Dokument als Eingabe erfasst und asynchron Text extrahiert. Der Aufruf wird mit einem Antwortheaderfeld namens `Operation-Location` zurückgegeben. Der `Operation-Location`-Wert ist eine URL, die die Vorgangs-ID enthält, die im nächsten Schritt verwendet werden soll.

|Antwortheader| Ergebnis-URL |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/vision/v3.1/read/analyzeResults/49a36324-fc4b-4387-aa06-090cfbf0064f` |

> [!NOTE]
> **Abrechnung** 
>
> Die Seite [Preise für maschinelles Sehen](https://azure.microsoft.com/pricing/details/cognitive-services/computer-vision/) enthält den Tarif für Read. Jedes analysierte Bild oder jede analysierte Seite ist eine Transaktion. Wenn Sie den Vorgang mit einem PDF- oder TIFF-Dokument mit 100 Seiten aufrufen, wird dies vom Read-Vorgang als 100 Transaktionen gezählt und Ihnen werden 100 Transaktionen in Rechnung gestellt. Wenn Sie für den Vorgang 50 Aufrufe durchgeführt haben und jeder Aufruf ein Dokument mit 100 Seiten übermittelt hat, werden Ihnen 5000 Transaktionen (50 x 100) in Rechnung gestellt.

## <a name="the-get-read-results-call"></a>„Get Read Results“-Aufruf

Der zweite Schritt umfasst das Aufrufen des Vorgangs [Get Read Results](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d9869604be85dee480c8750). Dieser Vorgang erfasst die Vorgangs-ID, die beim Read-Vorgang erstellt wurde, als Eingabe. Er gibt eine JSON-Antwort zurück, die ein **Status**-Feld mit den folgenden möglichen Werten enthält. Sie rufen diesen Vorgang iterativ auf, bis er mit dem Wert **succeeded** (erfolgreich) zurückgegeben wird. Verwenden Sie ein Intervall von 1 bis 2 Sekunden, um zu vermeiden, dass die Rate der Anforderungen pro Sekunde (RPS) überschritten wird.

|Feld| type | Mögliche Werte |
|:-----|:----:|:----|
|status | Zeichenfolge | notStarted: Der Vorgang wurde noch nicht gestartet. |
| |  | running: Der Vorgang wird verarbeitet. |
| |  | failed: Beim Vorgang ist ein Fehler aufgetreten. |
| |  | succeeded: Der Vorgang war erfolgreich. |

> [!NOTE]
> Im kostenlosen Tarif wird die Anforderungsrate auf 20 Aufrufe pro Minute beschränkt. Im kostenpflichtige Tarif sind 10 Anforderungen pro Sekunde (RPS) zulässig, die auf Anforderung erhöht werden können. Verwenden Sie den Azure-Supportkanal oder Ihr Kontoteam, um eine höheren Anforderungswert pro Sekunde (RPS) anzufordern.

Wenn das Feld **Status** den Wert **succeeded** aufweist, enthält die JSON-Antwort den extrahierten Textinhalt aus Ihrem Bild oder Dokument. In der JSON-Antwort werden die ursprünglichen Zeilengruppierungen der erkannten Wörter beibehalten. Sie enthält die extrahierten Textzeilen und die zugehörigen Begrenzungsrahmenkoordinaten. Jede Textzeile enthält alle extrahierten Wörter mit den zugehörigen Koordinaten und Zuverlässigkeitsbewertungen.

> [!NOTE]
> Die an den Vorgang `Read` übermittelten Daten werden vorübergehend verschlüsselt, im Ruhezustand gespeichert und innerhalb von 48 Stunden gelöscht. Dadurch können Ihre Anwendungen den extrahierten Text als Teil der Dienstantwort abrufen.

## <a name="sample-json-output"></a>JSON-Beispielausgabe

Eine erfolgreiche JSON-Antwort sieht in etwa wie folgendes Beispiel aus:

```json
{
  "status": "succeeded",
  "createdDateTime": "2020-05-28T05:13:21Z",
  "lastUpdatedDateTime": "2020-05-28T05:13:22Z",
  "analyzeResult": {
    "version": "3.1.0",
    "readResults": [
      {
        "page": 1,
        "angle": 0.8551,
        "width": 2661,
        "height": 1901,
        "unit": "pixel",
        "lines": [
          {
            "boundingBox": [
              67,
              646,
              2582,
              713,
              2580,
              876,
              67,
              821
            ],
            "text": "The quick brown fox jumps",
            "words": [
              {
                "boundingBox": [
                  143,
                  650,
                  435,
                  661,
                  436,
                  823,
                  144,
                  824
                ],
                "text": "The",
                "confidence": 0.958
              }
            ]
          }
        ]
      }
    ]
  }
}
```
### <a name="read-32-preview-adds-text-line-style-latin-languages-only"></a>Die Vorschauversion von Read 3.2 fügt Textzeilenstil hinzu (nur Sprachen mit lateinischem Alphabet)
Die [Read 3.2-API (Vorschauversion)](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-1/operations/5d986960601faab4bf452005) gibt ein **appearance**-Objekt aus, das klassifiziert, ob es sich bei den einzelnen Textzeilen um Drucktext oder eine Handschrift handelt. Außerdem wird eine Zuverlässigkeitsbewertung ausgegeben. Dieses Feature wird nur für lateinische Sprachen unterstützt.

Beginnen Sie mit den [Schnellstartanleitungen für die REST-API für maschinelles Sehen oder für die Clientbibliothek](./quickstarts-sdk/client-library.md), um OCR-Funktionen in Ihre Anwendungen zu integrieren.

## <a name="supported-languages-for-print-text"></a>Unterstützte Sprachen für Drucktext
Mit der [Lese-API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005) kann gedruckter Text in englischer, spanischer, deutscher, französischer, italienischer, portugiesischer und niederländischer Sprache extrahiert werden.

Eine vollständige Liste der für OCR unterstützten Sprachen finden Sie unter [Unterstützte Sprachen](./language-support.md#optical-character-recognition-ocr).

### <a name="read-32-preview-adds-simplified-chinese-and-japanese"></a>Chinesisch (vereinfacht) und Japanisch in der Vorschauversion von Read 3.2
Die [öffentliche Vorschau der Read 3.2-API](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-1/operations/5d986960601faab4bf452005) unterstützt jetzt Chinesisch (vereinfacht) und Japanisch. Wenn Sie für Ihr Szenario Unterstützung für weitere Sprachen benötigen, finden Sie entsprechende Informationen im Abschnitt [OCR-API](#ocr-api). 

## <a name="supported-languages-for-handwritten-text"></a>Unterstützte Sprachen für handschriftlichen Text
Der Read-Vorgang unterstützt zurzeit das Extrahieren von handschriftlichem Text ausschließlich in englischer Sprache.

## <a name="use-the-rest-api-and-sdk"></a>Verwenden von REST-API und SDK
Die [Read 3.x-REST-API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005) ist die bevorzugte Option der meisten Kunden, da sie sich einfach integrieren und schnell vorgefertigt produktiv einsetzen lässt. Azure und der Dienst für maschinelles Sehen verarbeiten Skalierungs-, Leistungs-, Datensicherheits- und Complianceanforderungen, während Sie sich auf die Erfüllung der Anforderungen Ihrer Kunden konzentrieren.

## <a name="deploy-on-premise-with-docker-containers"></a>Lokales Bereitstellen mit Docker-Containern
Der [Read-Docker-Container (Vorschau)](./computer-vision-how-to-install-containers.md) ermöglicht Ihnen die Bereitstellung der neuen OCR-Funktionen in Ihrer eigenen lokalen Umgebung. Container eignen sich hervorragend für bestimmte Sicherheits- und Datengovernanceanforderungen.

## <a name="example-outputs"></a>Beispielausgaben

### <a name="text-from-images"></a>Text in Bildern

Die folgende Ausgabe der Read-API zeigt den aus einem Bild extrahierten Text in unterschiedlichen Textwinkeln, -farben und -schriftarten.

![Ein Bild mit mehreren Wörtern in unterschiedlichen Farben und Winkeln mit Auflistung des extrahierten Texts](./Images/text-from-images-example.png)

### <a name="text-from-documents"></a>Text in Dokumenten

Die Read-API akzeptiert auch PDF-Dokumente als Eingabe.

![Ein Rechnungsdokument mit Auflistung des extrahierten Texts](./Images/text-from-pdf-example.png)

### <a name="handwritten-text"></a>Handschriftlicher Text

Der Read-Vorgang extrahiert handschriftlichen Text aus Bildern (zurzeit nur in Englisch).

![Eine Abbildung einer handschriftlichen Notiz mit Auflistung des extrahierten Texts](./Images/handwritten-example.png)

### <a name="printed-text"></a>Gedruckter Text

Der Read-Vorgang kann gedruckten Text in verschiedenen Sprachen extrahieren.

![Eine Abbildung eines Spanisch-Lehrbuchs mit Auflistung des extrahierten Texts](./Images/supported-languages-example.png)

### <a name="mixed-language-documents"></a>Dokumente mit gemischten Sprachen

Die Read-API unterstützt Bilder und Dokumente, die mehrere verschiedene Sprachen enthalten, im Allgemeinen als Dokumente mit gemischten Sprachen bezeichnet. Dabei wird jede einzelne Textzeile in einem Dokument in die erkannte Sprache klassifiziert, bevor die Textinhalte extrahiert werden.

![Ein Abbildung von Ausdrücken in mehreren Sprachen mit Auflistung des extrahierten Texts](./Images/mixed-language-example.png)

## <a name="ocr-api"></a>OCR-API

Die [OCR-API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f20d) verwendet ein älteres Erkennungsmodell, unterstützt nur Bilder und wird synchron ausgeführt, sodass der erkannte Text direkt zurückgegeben wird. Informationen finden Sie unter [Für OCR unterstützte Sprachen](./language-support.md#optical-character-recognition-ocr) in der Read-API.

## <a name="data-privacy-and-security"></a>Datenschutz und Sicherheit

Wie bei allen Cognitive Services-Diensten müssen Entwickler, die Read/OCR-Dienste nutzen, die Microsoft-Richtlinien zu Kundendaten beachten. Weitere Informationen finden Sie im [Microsoft Trust Center](https://www.microsoft.com/trust-center/product-overview) auf der Seite zu Cognitive Services.

> [!NOTE]
> Die RecognizeText-Vorgänge der Maschinelles Sehen-API 2.0 werden allmählich zugunsten der neuen in diesem Artikel erläuterten Lese-API eingestellt. Bestehende Kunden sollten dazu [übergehen, Lesevorgänge zu nutzen](upgrade-api-versions.md).

## <a name="next-steps"></a>Nächste Schritte

- Beginnen Sie mit den [Schnellstartanleitungen für die REST-API für maschinelles Sehen oder für die Clientbibliothek](./quickstarts-sdk/client-library.md).
- Erfahren Sie mehr über die [Read-REST-API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005).
- Erfahren Sie mehr über die [öffentliche Vorschau der Read 3.2-REST-API](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-1/operations/5d986960601faab4bf452005) mit Unterstützung für Chinesisch (vereinfacht) und Japanisch.