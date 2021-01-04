---
title: Rechnungen –-Formularerkennung
titleSuffix: Azure Cognitive Services
description: Lernen Sie die Konzepte im Zusammenhang mit Rechnungsanalyse mit der Formularerkennungs-API (Verwendung und Einschränkungen) kennen.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 11/18/2020
ms.author: pafarley
ms.openlocfilehash: 9a3a6bd6489baea90ed4143b42a09e7d697bbc50
ms.sourcegitcommit: c4246c2b986c6f53b20b94d4e75ccc49ec768a9a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/04/2020
ms.locfileid: "96602443"
---
# <a name="form-recognizer-prebuilt-invoice-model"></a>Vordefiniertes Rechnungsmodell für die Formularerkennung

Die Azure-Formularerkennung kann Informationen aus Verkaufsrechnungen mithilfe seiner vordefinierten Rechnungsmodelle analysieren und extrahieren. Mit der Rechnungs-API können Kunden Rechnungen in verschiedenen Formaten verwenden und strukturierte Daten zurückgeben, um die Rechnungsverarbeitung zu automatisieren. Sie kombiniert unsere leistungsstarken Funktionen zur [optischen Zeichenerkennung (Optical Character Recognition, OCR)](../computer-vision/concept-recognizing-text.md) mit Deep Learning-Modellen zum Rechnungsverständnis, um wichtige Informationen aus Rechnungen in englischer Sprache zu extrahieren. Sie extrahiert den Text, Tabellen und Informationen wie Kunde, Anbieter, Rechnungs-ID, Fälligkeitsdatum für die Rechnung, Summe, fälliger Betrag, Steuerbetrag, Lieferadresse, Rechnungsadresse und mehr. Die vordefinierte Rechnungs-API ist in der Vorschauversion der Formularerkennung (v2.1) öffentlich verfügbar.

## <a name="what-does-the-invoice-service-do"></a>Welche Aufgaben führt der Rechnungsdienst aus?

Die Rechnungs-API extrahiert Schlüsselfelder aus Rechnungen und gibt sie in einer organisierten strukturierten JSON-Antwort zurück. Rechnungen können viele verschiedene Formate und Qualitätsmerkmale haben, beispielsweise mit dem Handy fotografierte Bilder, gescannte Dokumente und digitale PDF-Dateien. Die Rechnungs-API extrahiert die strukturierte Ausgabe aus allen diesen Rechnungen. 

![Beispiel für eine Contoso-Rechnung](./media/invoice-example.jpg)

## <a name="try-it-out"></a>Ausprobieren

Wenn Sie den Rechnungsdienst „Formularerkennung“ ausprobieren möchten, wechseln Sie zum Onlinetool für die Beispielbenutzeroberfläche:

> [!div class="nextstepaction"]
> [Ausprobieren von vordefinierten Modellen](https://fott-preview.azurewebsites.net/)

Zum Ausprobieren des Rechnungsdiensts „Formularerkennung“ benötigen Sie ein Azure-Abonnement ([kann hier kostenlos erstellt werden](https://azure.microsoft.com/free/cognitive-services)), einen Endpunkt für eine [Formularerkennungsressource](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) und einen entsprechenden Schlüssel. 

![Beispiel für eine analysierte Rechnung](./media/analyze-invoice.png)


### <a name="input-requirements"></a>Eingabeanforderungen 

[!INCLUDE [input reqs](./includes/input-requirements-receipts.md)]

## <a name="the-analyze-invoice-operation"></a>Der Vorgang „Analysieren der Rechnung“

Der Vorgang [Analysieren der Rechnung](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/5ed8c9843c2794cbb1a96291) verwendet ein Bild oder eine PDF-Datei einer Rechnung als Eingabe und extrahiert die relevanten Werte. Bei diesem Aufruf wird ein Antwortheaderfeld namens `Operation-Location` zurückgegeben. Der `Operation-Location`-Wert ist eine URL, die die Ergebnis-ID enthält, die im nächsten Schritt verwendet werden soll.

|Antwortheader| Ergebnis-URL |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/formrecognizer/v2.1-preview.2/prebuilt/invoice/analyzeResults/49a36324-fc4b-4387-aa06-090cfbf0064f` |

## <a name="the-get-analyze-invoice-result-operation"></a>Der Vorgang „Abrufen des Ergebnisses der Rechnungsanalyse“

Im zweiten Schritt wird der Vorgang zum [Abrufen des Ergebnisses der Rechnungsanalyse](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/5ed8c9acb78c40a2533aee83) aufgerufen. Dieser Vorgang verwendet als Eingabe die Ergebnis-ID, die durch den Vorgang „Analysieren der Rechnung“ erstellt wurde. Er gibt eine JSON-Antwort zurück, die ein **Status**-Feld mit den folgenden möglichen Werten enthält. Sie rufen diesen Vorgang iterativ auf, bis er mit dem Wert **succeeded** (erfolgreich) zurückgegeben wird. Verwenden Sie ein Intervall von 3 bis 5 Sekunden, um zu vermeiden, dass die Rate der Anforderungen pro Sekunde (RPS) überschritten wird.

|Feld| type | Mögliche Werte |
|:-----|:----:|:----|
|status | Zeichenfolge | notStarted: Der Analysevorgang wurde noch nicht gestartet.<br /><br />running: Der Analysevorgang wird ausgeführt.<br /><br />failed: Beim Analysevorgang ist ein Fehler aufgetreten.<br /><br />succeeded: Der Analysevorgang war erfolgreich.|

Wenn im Feld **status** der Wert **succeeded** (erfolgreich) angezeigt wird, enthält die JSON-Antwort die Ergebnisse des Rechnungsverständnisses und optional die der Texterkennung, wenn sie angefordert wurden. Das Ergebnis des Rechnungsverständnisses ist als Wörterbuch benannter Feldwerte organisiert, wobei jeder Wert den extrahierten Text, den normalisierten Wert, den Begrenzungsrahmen, den Vertrauensgrad und die entsprechenden Wortelemente enthält. Das Ergebnis der Texterkennung ist als eine Hierarchie von Zeilen und Wörtern mit Text, Begrenzungsrahmen und Informationen zum Vertrauensgrad organisiert.

### <a name="sample-json-output"></a>JSON-Beispielausgabe

Die Antwort auf den Vorgang „Abrufen des Ergebnisses der Rechnungsanalyse“ ist die strukturierte Darstellung der Rechnung mit allen extrahierten Informationen. Hier finden Sie ein [Beispiel für eine Rechnungsdatei](./media/sample-invoice.jpg) und deren strukturierte Ausgabe ([Beispiel für eine Rechnungsausgabe](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/curl/form-recognizer/sample-invoice-output.json)).

Die JSON-Ausgabe besteht aus drei Teilen: 
* Der Knoten `"readResults"` enthält den gesamten erkannten Text und alle erkannten Auswahlmarkierungen. Der Text ist nach Seite, dann nach Zeile und dann nach einzelnen Wörtern sortiert. 
* Der Knoten `"pageResults"` enthält die Tabellen und Zellen, die mit ihren Begrenzungsrahmen, Konfidenz (Vertrauen) und einem Verweis auf die Zeilen und Wörter in „readResults“ extrahiert wurden.
* Der Knoten `"documentResults"` enthält die rechnungsspezifischen Werte, die vom Modell erkannt wurden. Hier finden Sie alle Felder aus der Rechnung, wie z B. Rechnungs-ID, Lieferadresse, Rechnungsadresse, Kunde, Gesamtsumme und viele mehr.

## <a name="example-output"></a>Beispielausgabe

Der Rechnungsdienst extrahiert den Text, die Tabellen und 26 Rechnungsfelder. Nachstehend sehen Sie die aus einer Rechnung extrahierten Felder in der JSON-Ausgabeantwort (die folgende Ausgabe verwendet diese [Beispielrechnung](./media/sample-invoice.jpg)).  

|Name| Type | BESCHREIBUNG | Text | Wert (standardisierte Ausgabe) |
|:-----|:----|:----|:----| :----|
| CustomerName | Zeichenfolge | Kunde, dem die Rechnung gestellt wird | Microsoft Corp. |  |
| CustomerId | Zeichenfolge | Referenz-ID für den Kunden | CID-12345 |  |
| PurchaseOrder | Zeichenfolge | Eine Referenznummer für die Bestellung | PO-3333 | |  |
| InvoiceId | Zeichenfolge | ID für diese bestimmte Rechnung (oft „Rechnungsnummer“) | INV-100 | |  |
| Rechnungsdatum | date | Datum, an dem die Rechnung ausgestellt wurde | 15.11.2019 | 
| DueDate | date | Datum, an dem die Zahlung für diese Rechnung fällig ist | 15.12.2019 | 2019-12-15 | 15.11.2019 |
| VendorName | Zeichenfolge | Anbieter, der diese Rechnung erstellt hat | CONTOSO LTD. | |
| VendorAddress | Zeichenfolge | Postanschrift für den Anbieter | 123 456th St New York, NY, 10001 | |
| VendorAddressRecipient | Zeichenfolge | Der „VendorAddress“ (Anbieteranschrift) zugeordnete Name | Contoso Headquarters | |
| CustomerAddress | Zeichenfolge | Postanschrift für den Kunden | 123 Other St, Redmond WA, 98052 | |
| CustomerAddressRecipient | Zeichenfolge | Der „CustomerAddress“ (Kundenadresse) zugeordnete Name | Microsoft Corp. | |
| BillingAddress | Zeichenfolge | Explizite Rechnungsadresse für den Kunden | 123 Bill St, Redmond WA, 98052 | |
| BillingAddressRecipient | Zeichenfolge | Der „BillingAddress“ (Rechnungsadresse) zugeordnete Name | Microsoft-Dienste | |
| ShippingAddress | Zeichenfolge | Explizite Lieferadresse für den Kunden | 123 Ship St, Redmond WA, 98052 | |
| ShippingAddressRecipient | Zeichenfolge | Der „ShippingAdresss“ (Lieferadresse) zugeordnete Name | Microsoft-Lieferung | |
| SubTotal | Anzahl | In dieser Rechnung identifiziertes Feld „Subtotal“ (Zwischensumme) | $ 100,00 | 100 | 
| TotalTax | Anzahl | In dieser Rechnung identifiziertes Feld „Total tax“ (Gesamtsteuerbetrag) | $ 10,00 | 10 |
| InvoiceTotal | Anzahl | Summe der dieser Rechnung zugeordneten neuen Gebühren | $ 110,00 | 110 |
| AmountDue |  Anzahl | Gesamtbetrag, der an den Anbieter zu zahlen ist | $ 610,00 | 610 |
| ServiceAddress | Zeichenfolge | Explizite Dienstadresse oder Immobilienadresse für den Kunden | 123 Service St, Redmond WA, 98052 | |
| ServiceAddressRecipient | Zeichenfolge | Der „ServiceAddress“ (Dienstadresse) zugeordnete Name | Microsoft-Dienste | |
| RemittanceAddress | Zeichenfolge | Explizite Überweisungs- oder Zahlungsadresse für den Kunden | 123 Remit St New York, NY, 10001 |  |
| RemittanceAddressRecipient | Zeichenfolge | Der „RemittanceAddress“ (Überweisungsadresse) zugeordnete Name | Contoso-Abrechnung |  |
| ServiceStartDate | date | Anfangsdatum für den Dienstzeitraum (z. B. ein Dienstzeitraum für Hilfsprogrammrechnungen) | 14.10.2019 | 14.10.2019 |
| ServiceEndDate | date | Enddatum für den Dienstzeitraum (z B. ein Dienstzeitraum für Hilfsprogrammrechnungen) | 14.11.2019 | 2019-11-14 |
| PreviousUnpaidBalance | Anzahl | Zuvor explizit ausstehende Zahlung | $ 500,00 | 500 |


## <a name="next-steps"></a>Nächste Schritte

- Probieren Sie Ihre eigenen Rechnungen und Beispiele in der [Beispielbenutzeroberfläche für die Formularerkennung](https://fott-preview.azurewebsites.net/) aus.
- Arbeiten Sie eine [Schnellstartanleitung zur Clientbibliothek für die Formularerkennung](quickstarts/client-library.md) durch, um mit dem Schreiben einer Rechnungsverarbeitungs-App mit Formularerkennung in der Sprache Ihrer Wahl zu beginnen.
- Oder führen Sie die Schritte im Schnellstart zum [Extrahieren von Layoutdaten](./quickstarts/python-invoices.md) aus, um die Extraktion von Layoutdaten mithilfe von Python und der REST-API zu implementieren.
## <a name="see-also"></a>Weitere Informationen

* [Was ist die Formularerkennung?](./overview.md)
* [REST-API-Referenzdokumente](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/5ed8c9843c2794cbb1a96291)
