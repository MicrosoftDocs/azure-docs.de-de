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
ms.openlocfilehash: 1d633b1a9f5fee0a5cceb48f2b37aaec2092069f
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/09/2021
ms.locfileid: "99979532"
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

> [!NOTE]
> **Spracheneingabe** 
>
> Der [Read-Aufruf](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005) weist einen optionalen Anforderungsparameter für die Sprache auf. Read unterstützt die automatische Sprachidentifikation und mehrsprachige Dokumente. Geben Sie also nur einen Sprachcode an, wenn Sie erzwingen möchten, dass das Dokument in dieser spezifischen Sprache verarbeitet wird.

## <a name="ocr-demo-examples"></a>OCR-Demo (Beispiele)

![OCR-Demos](./Images/ocr-demo.gif)

## <a name="step-1-the-read-operation"></a>Schritt 1: Der Read-Vorgang

Beim [Read-Aufruf](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005) der Read-API wird ein Bild oder PDF-Dokument als Eingabe erfasst und asynchron Text extrahiert. Der Aufruf wird mit einem Antwortheaderfeld namens `Operation-Location` zurückgegeben. Der `Operation-Location`-Wert ist eine URL, die die Vorgangs-ID enthält, die im nächsten Schritt verwendet werden soll.

|Antwortheader| Ergebnis-URL |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/vision/v3.1/read/analyzeResults/49a36324-fc4b-4387-aa06-090cfbf0064f` |

> [!NOTE]
> **Abrechnung** 
>
> Die Seite [Preise für maschinelles Sehen](https://azure.microsoft.com/pricing/details/cognitive-services/computer-vision/) enthält den Tarif für Read. Jedes analysierte Bild oder jede analysierte Seite ist eine Transaktion. Wenn Sie den Vorgang mit einem PDF- oder TIFF-Dokument mit 100 Seiten aufrufen, wird dies vom Read-Vorgang als 100 Transaktionen gezählt und Ihnen werden 100 Transaktionen in Rechnung gestellt. Wenn Sie für den Vorgang 50 Aufrufe durchgeführt haben und jeder Aufruf ein Dokument mit 100 Seiten übermittelt hat, werden Ihnen 5000 Transaktionen (50 x 100) in Rechnung gestellt.

## <a name="step-2-the-get-read-results-operation"></a>Schritt 2: Der „Get Read Results“-Vorgang

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
> Die an den Vorgang `Read` übermittelten Daten werden vorübergehend verschlüsselt, für eine kurze Dauer im Ruhezustand gespeichert und dann gelöscht. Dadurch können Ihre Anwendungen den extrahierten Text als Teil der Dienstantwort abrufen.

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

## <a name="natural-reading-order-output"></a>Ausgabe der natürlichen Leserichtung
Geben Sie mit der [Read 3.2-API (Vorschauversion)](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-2/operations/5d986960601faab4bf452005) die Reihenfolge an, in der die Textzeilen mit dem `readingOrder`-Abfrageparameter ausgegeben werden. Verwenden Sie `natural` für eine benutzerfreundlichere Ausgabe der Lesereihenfolge, wie im folgenden Beispiel gezeigt.

:::image border type="content" source="./Images/ocr-reading-order-example.png" alt-text="Beispiel für OCR-Leserichtung":::

## <a name="handwritten-classification-for-text-lines-latin-only"></a>Handschriftliche Klassifizierung für Textzeilen (nur Lateinisch)
Die Antwort der [Read 3.2-API (Vorschauversion)](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-2/operations/5d986960601faab4bf452005) umfasst die Klassifizierung, ob es sich bei den einzelnen Textzeilen um einen handschriftlichen Stil handelt, zusammen mit einer Zuverlässigkeitsbewertung. Dieses Feature wird nur für lateinische Sprachen unterstützt. Das folgende Beispiel zeigt die handschriftliche Klassifizierung für den Text im Bild.

:::image border type="content" source="./Images/ocr-handwriting-classification.png" alt-text="Beispiel für OCR-Handschriftklassifizierung":::

## <a name="select-pages-or-page-ranges-for-text-extraction"></a>Auswählen von Seiten oder Seitenbereichen für die Textextraktion
Verwenden Sie mit der [Read 3.2-API (Vorschauversion)](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-2/operations/5d986960601faab4bf452005) bei großen mehrseitigen Dokumenten den Abfrageparameter `pages` zur Angabe von Seitenzahlen oder Seitenbereichen, um Text nur von diesen Seiten zu extrahieren. Das folgende Beispiel zeigt ein Dokument mit 10 Seiten, wobei für beide Fälle – alle Seiten (1-10) und ausgewählte Seiten (3-6) – Text extrahiert wurde.

:::image border type="content" source="./Images/ocr-select-pages.png" alt-text="Ausgabe ausgewählter Seiten":::

## <a name="supported-languages"></a>Unterstützte Sprachen
Die Lese-APIs unterstützen insgesamt 73 Sprachen für gedruckten Text. Eine vollständige Liste finden Sie unter [Sprachunterstützung für maschinelles Sehen](./language-support.md#optical-character-recognition-ocr). OCR für handgeschriebenen Text wird ausschließlich für Englisch unterstützt.

## <a name="use-the-cloud-api-or-deploy-on-premise"></a>Verwenden der Cloud-API oder lokale Bereitstellung
Die Read 3.x-Cloud-APIs sind die bevorzugte Option der meisten Kunden, da sie sich einfach integrieren und schnell vorgefertigt produktiv einsetzen lassen. Azure und der Dienst für maschinelles Sehen verarbeiten Skalierungs-, Leistungs-, Datensicherheits- und Complianceanforderungen, während Sie sich auf die Erfüllung der Anforderungen Ihrer Kunden konzentrieren.

Für die lokale Bereitstellung ermöglicht der [Read-Docker-Container (Vorschau)](./computer-vision-how-to-install-containers.md) Ihnen die Bereitstellung der neuen OCR-Funktionen in Ihrer eigenen lokalen Umgebung. Container eignen sich hervorragend für bestimmte Sicherheits- und Datengovernanceanforderungen.

## <a name="ocr-api"></a>OCR-API

Die [OCR-API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f20d) verwendet ein älteres Erkennungsmodell, unterstützt nur Bilder und wird synchron ausgeführt, sodass der erkannte Text direkt zurückgegeben wird. Informationen finden Sie unter [Für OCR unterstützte Sprachen](./language-support.md#optical-character-recognition-ocr) in der Read-API.

> [!NOTE]
> Die RecognizeText-Vorgänge der Maschinelles Sehen-API 2.0 werden allmählich zugunsten der neuen in diesem Artikel erläuterten Lese-API eingestellt. Bestehende Kunden sollten dazu [übergehen, Lesevorgänge zu nutzen](upgrade-api-versions.md).

## <a name="next-steps"></a>Nächste Schritte

- Beginnen Sie mit den [Schnellstartanleitungen für die REST-API für maschinelles Sehen oder für die Clientbibliothek](./quickstarts-sdk/client-library.md).
- Erfahren Sie mehr über die [Read 3.1-REST-API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005).
- Erfahren Sie mehr über die [öffentliche Vorschau der Read 3.2-REST-API](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-2/operations/5d986960601faab4bf452005) mit Unterstützung für insgesamt 73 Sprachen.
