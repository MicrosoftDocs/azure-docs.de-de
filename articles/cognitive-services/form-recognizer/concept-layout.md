---
title: Layouts – Formularerkennung
titleSuffix: Azure Cognitive Services
description: Lernen Sie die Konzepte im Zusammenhang mit Layoutanalyse mit der Formularerkennung-API (Verwendung und Einschränkungen) kennen.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 11/18/2020
ms.author: pafarley
ms.openlocfilehash: 60ef490bbba4dca4f01b82967ad6ea8636e7bd4e
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96353696"
---
# <a name="form-recognizer-layout-service"></a>Layoutdienst für die Formularerkennung

Die Azure-Formularerkennung kann Text, Tabellen, Auswahlmarkierungen und Strukturinformationen aus Dokumenten mithilfe ihres Layoutdiensts extrahieren. Die Layout-API ermöglicht es Kunden, aus Dokumenten in vielen verschiedenen Formaten strukturierte Daten und eine strukturierte Darstellung des Dokuments zurückzugeben. Sie kombiniert unsere leistungsstarken Funktionen für [optische Zeichenerkennung (Optical Character Recognition, OCR)](../computer-vision/concept-recognizing-text.md) mit Deep Learning-Modellen für Dokumentenverständnis zum Extrahieren von Text, Tabellen, Auswahlmarkierungen und der Struktur von Dokumenten. 

## <a name="what-does-the-layout-service-do"></a>Welche Aufgaben führt der Layoutdienst aus?

Die Layout-API extrahiert Text, Tabellen, Auswahlmarkierungen und Strukturinformationen aus Dokumenten mit einer außergewöhnlichen Genauigkeit und gibt sie in einer organisierten strukturierten JSON-Antwort zurück. Dokumente können viele verschiedene Formate und Qualitätsmerkmale haben, beispielsweise mit dem Handy fotografierte Bilder, gescannte Dokumente und digitale PDF-Dateien. Die Layout-API extrahiert die strukturierte Ausgabe aus allen diesen Dokumenten.

![Beispiel für ein Layout](./media/layout-tool-example.JPG)

## <a name="try-it-out"></a>Ausprobieren

Wenn Sie den Layoutdienst für die Formularerkennung ausprobieren möchten, wechseln Sie zum Onlinetool für die Beispielbenutzeroberfläche:

> [!div class="nextstepaction"]
> [Beispielbenutzeroberfläche](https://fott-preview.azurewebsites.net/)

Zum Ausprobieren der Layout-API für die Formularerkennung benötigen Sie ein Azure-Abonnement ([kann hier kostenlos erstellt werden](https://azure.microsoft.com/free/cognitive-services)), einen Endpunkt für eine [Formularerkennungsressource](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) und einen entsprechenden Schlüssel. 

![Screenshot der Beispielbenutzeroberfläche; der Text, die Tabellen und Auswahlmarkierungen eines Dokuments werden analysiert.](./media/analyze-layout.png)

### <a name="input-requirements"></a>Eingabeanforderungen 

[!INCLUDE [input requirements](./includes/input-requirements-receipts.md)]

## <a name="the-analyze-layout-operation"></a>Der Vorgang „Analysieren des Layouts“

Der Vorgang [Analysieren des Layouts](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeLayoutAsync) verwendet ein Dokument (Bild-, TIFF- oder PDF-Datei) als Eingabe und extrahiert den Text, die Tabellen, Auswahlmarkierungen und Struktur des Dokuments. Bei diesem Aufruf wird ein Antwortheaderfeld namens `Operation-Location` zurückgegeben. Der `Operation-Location`-Wert ist eine URL, die die Ergebnis-ID enthält, die im nächsten Schritt verwendet werden soll.

|Antwortheader| Ergebnis-URL |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/formrecognizer/v2.1-preview.2/prebuilt/layout/analyzeResults/44a436324-fc4b-4387-aa06-090cfbf0064f` |

## <a name="the-get-analyze-layout-result-operation"></a>Der Vorgang „Abrufen des Ergebnisses der Layoutanalyse“

Im zweiten Schritt wird der Vorgang [Abrufen des Ergebnisses der Layoutanalyse](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/GetAnalyzeLayoutResult) aufgerufen. Bei diesem Vorgang wird die Ergebnis-ID als Eingabe verwendet, die durch den Vorgang „Analysieren des Layouts“ erstellt wurde. Er gibt eine JSON-Antwort zurück, die ein **Status**-Feld mit den folgenden möglichen Werten enthält. 

|Feld| type | Mögliche Werte |
|:-----|:----:|:----|
|status | Zeichenfolge | `notStarted`: Der Analysevorgang wurde noch nicht gestartet.<br /><br />`running`: Der Analysevorgang wird ausgeführt.<br /><br />`failed`: Beim Analysevorgang ist ein Fehler aufgetreten.<br /><br />`succeeded`: Der Analysevorgang war erfolgreich.|

Sie rufen diesen Vorgang iterativ auf, bis er mit dem Wert `succeeded` (erfolgreich) zurückgegeben wird. Verwenden Sie ein Intervall von 3 bis 5 Sekunden, um zu vermeiden, dass die Rate der Anforderungen pro Sekunde (RPS) überschritten wird.

Wenn das Feld **Status** den Wert `succeeded` hat, besteht die JSON-Antwort aus den Ergebnissen der Layoutextraktion, Text, Tabellen und extrahierten Auswahlmarkierungen. Die extrahierten Daten enthalten die extrahierten Textzeilen und Wörter, das umgebende Feld, die handschriftliche Anzeige von Textdarstellung, Tabellen und Auswahlmarkierungen mit der Angabe „ausgewählt“/„nicht ausgewählt“. 

### <a name="sample-json-output"></a>JSON-Beispielausgabe

Die Antwort auf den Vorgang „Abrufen des Ergebnisses der Layoutanalyse“ ist die strukturierte Darstellung des Dokuments mit allen extrahierten Informationen. Hier sehen Sie ein [Beispiel für eine Dokumentdatei](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/curl/form-recognizer/sample-layout.pdf) und deren strukturierte Ausgabe [Beispiel für eine Layoutausgabe](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/curl/form-recognizer/sample-layout-output.json).

Die JSON-Ausgabe besteht aus zwei Teilen: 
* Der Knoten `"readResults"` enthält den gesamten erkannten Text und alle erkannten Auswahlmarkierungen. Der Text ist nach Seite, dann nach Zeile und dann nach einzelnen Wörtern sortiert. 
* Der Knoten `"pageResults"` enthält die Tabellen und Zellen, die mit ihren umgebenden Feldern, Konfidenz (Zuverlässigkeit) und einem Verweis auf die Zeilen und Wörter in „readResults“ extrahiert wurden.

## <a name="example-output"></a>Beispielausgabe

### <a name="text"></a>Text

Layout extrahiert Text aus Dokumenten (PDF, TIFF) und Bildern (JPG, PNG, BMP) mit unterschiedlichen Textwinkeln, Farben, Winkeln, Fotos von Dokumenten, Faxen, gedruckten, handschriftlichen (nur Englisch) und gemischten Modi. Text wird mit Informationen zu Zeilen, Wörtern, umgebenden Feldern, Zuverlässigkeitsbewertungen und Stil (handschriftlich oder anderer) extrahiert. Alle Textinformationen sind im Abschnitt `"readResults"` der JSON-Ausgabe enthalten. 

### <a name="tables"></a>Tabellen

Layout extrahiert Tabellen aus Dokumenten (PDF, TIFF) und Bildern (JPG, PNG, BMP). Dokumente können gescannt, fotografiert oder digitalisiert sein. Tabellen können komplexe Tabellen mit zusammengeführten Zellen oder Spalten (mit oder ohne Rahmen) und mit ungeraden Winkeln sein. Extrahierte Tabellen enthalten die Anzahl von Spalten und Zeilen sowie den Zeilen- und Spaltenabstand. Jede Zelle wird mit ihrem umgebenden Feld und einem Verweis auf den im `"readResults"` Abschnitt extrahierten Text extrahiert. Tabelleninformationen sind im Abschnitt `"pageResults"` der JSON-Ausgabe zu finden. 

![Beispiel für Tabellen](./media/tables-example.jpg)

### <a name="selection-marks"></a>Auswahlmarkierungen

Layout extrahiert auch Auswahlmarkierungen aus Dokumenten. Extrahierte Auswahlmarkierungen enthalten das umgebende Feld, Konfidenz (Zuverlässigkeit) und den Status („ausgewählt“/“nicht ausgewählt“). Informationen zur Auswahlmarkierung werden im Abschnitt `"readResults"` der JSON-Ausgabe extrahiert. 

## <a name="next-steps"></a>Nächste Schritte

- Ausprobieren Ihrer eigenen Layoutextraktion mithilfe der [Beispielbenutzeroberfläche für die Formularerkennung](https://fott-preview.azurewebsites.net/)
- Arbeiten Sie einen [Schnellstart für die Clientbibliothek zur Formularerkennung](quickstarts/client-library.md) durch, um die ersten Schritte zum Extrahieren von Layouts in der Sprache Ihrer Wahl auszuführen.
- Oder führen Sie die Schritte im Schnellstart zum [Extrahieren von Layoutdaten](./QuickStarts/python-layout.md) aus, um die Extraktion von Layoutdaten mithilfe von Python und der REST-API zu implementieren.

## <a name="see-also"></a>Weitere Informationen

* [Was ist die Formularerkennung?](./overview.md)
* [REST-API-Referenzdokumente](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeLayoutAsync)