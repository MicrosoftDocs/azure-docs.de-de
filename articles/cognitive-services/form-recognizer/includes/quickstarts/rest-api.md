---
title: 'Schnellstart: REST-API für die Formularerkennung'
description: Verwenden Sie die REST-API der Formularerkennung, um eine Formularverarbeitungs-App zu erstellen, die Schlüssel-Wert-Paare und Tabellendaten aus Ihren benutzerdefinierten Dokumenten extrahiert.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 12/15/2020
ms.author: pafarley
ms.openlocfilehash: 3112c93e0877a8441875e3c7627c2a7b84ac8ab1
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/07/2021
ms.locfileid: "99808504"
---
> [!NOTE]
> In diesem Leitfaden wird cURL genutzt, um REST-API-Aufrufe auszuführen. Im [Beispielcode auf GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/python/FormRecognizer/rest) wird veranschaulicht, wie die REST-APIs mit Python aufgerufen werden.

## <a name="prerequisites"></a>Voraussetzungen

* [cURL](https://curl.haxx.se/windows/) muss installiert sein.
* Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/cognitive-services/)
* Trainingsdaten in einem Azure Storage-Blob. Tipps und Optionen für das Zusammenstellen eines Trainingsdatasets finden Sie unter [Erstellen eines Trainingsdatasets für ein benutzerdefiniertes Modell](../../build-training-data-set.md). In dieser Schnellstartanleitung können Sie die Dateien im Ordner **Trainieren** des [Beispieldatasets](https://go.microsoft.com/fwlink/?linkid=2090451) verwenden (*sample_data.zip* herunterladen und extrahieren).
* Sobald Sie über Ihr Azure-Abonnement verfügen, sollten Sie über <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title="Erstellen einer Formularerkennungsressource"  target="_blank"> im Azure-Portal eine Formularerkennungsressource <span class="docon docon-navigate-external x-hidden-focus"></span></a> erstellen, um Ihren Schlüssel und Endpunkt abzurufen. Klicken Sie nach Abschluss der Bereitstellung auf **Zu Ressource wechseln**.
    * Sie benötigen den Schlüssel und Endpunkt der von Ihnen erstellten Ressource, um Ihre Anwendung mit der Formularerkennungs-API zu verbinden. Der Schlüssel und der Endpunkt werden weiter unten in der Schnellstartanleitung in den Code eingefügt.
    * Sie können den kostenlosen Tarif (`F0`) verwenden, um den Dienst zu testen, und später für die Produktion auf einen kostenpflichtigen Tarif upgraden.
* Eine URL für ein Bild eines Verkaufsbelegs. Für diesen Schnellstart können Sie ein [Beispielbild](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/contoso-allinone.jpg) verwenden.
* Eine URL für ein Bild einer Visitenkarte. Für diesen Schnellstart können Sie ein [Beispielbild](https://raw.githubusercontent.com/Azure/azure-sdk-for-python/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/business_cards/business-card-english.jpg) verwenden.
* Eine URL für ein Bild einer Rechnung. Für diese Schnellstartanleitung können Sie ein [Beispieldokument](https://raw.githubusercontent.com/Azure/azure-sdk-for-python/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/forms/Invoice_1.pdf) verwenden.


## <a name="analyze-layout"></a>Analysieren des Layouts

Mit der Formularerkennung können Sie Tabellen, Auswahlmarkierungen, Text und Struktur in Dokumenten analysieren und extrahieren, ohne ein Modell trainieren zu müssen. Weitere Informationen zur Layoutextraktion finden Sie im [Konzeptleitfaden zum Layout](../../concept-layout.md). Nehmen Sie die folgenden Änderungen vor, bevor Sie den Befehl ausführen:

1. Ersetzen Sie `{Endpoint}` durch den Endpunkt, den Sie mit Ihrem Abonnement für die Formularerkennung erhalten haben.
1. Ersetzen Sie `{subscription key}` durch den Abonnementschlüssel, den Sie im vorherigen Schritt kopiert haben.
1. Ersetzen Sie `\"{your-document-url}` durch eine der Beispiel-URLs.

# <a name="v20"></a>[v2.0](#tab/v2-0)    


```bash
curl -v -i POST "https://{Endpoint}/formrecognizer/v2.0/layout/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{'source': '{your-document-url}'}"
```

# <a name="v21-preview"></a>[Vorschauversion v2.1](#tab/v2-1)    

```bash
curl -v -i POST "https://{Endpoint}/formrecognizer/v2.1-preview.2/layout/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{'source': '{your-document-url}'}"
```
---

Sie erhalten die Antwort `202 (Success)` mit einem **Operation-Location**-Header. Der Wert dieses Headers enthält eine Vorgangs-ID, mit der Sie den Status des asynchronen Vorgangs abfragen und die Ergebnisse abrufen können. Im folgenden Beispiel ist die Zeichenfolge nach `analyzeResults/` die Vorgangs-ID.

```console
https://cognitiveservice/formrecognizer/v2.1-preview.2/layout/analyzeResults/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

### <a name="get-layout-results"></a>Abrufen von Layoutergebnissen

Nachdem Sie die **[Analyze Layout](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeLayoutAsync)** -API aufgerufen haben, rufen Sie die **[Get Analyze Layout Result](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/GetAnalyzeLayoutResult)** -API auf, um den Status des Vorgangs und die extrahierten Daten abzurufen. Nehmen Sie die folgenden Änderungen vor, bevor Sie den Befehl ausführen:

1. Ersetzen Sie `{Endpoint}` durch den Endpunkt, den Sie mit Ihrem Abonnement für die Formularerkennung erhalten haben.
1. Ersetzen Sie `{subscription key}` durch den Abonnementschlüssel, den Sie im vorherigen Schritt kopiert haben.
1. Ersetzen Sie `{resultId}` durch die Vorgangs-ID aus dem vorherigen Schritt.

# <a name="v20"></a>[v2.0](#tab/v2-0)    

```bash
curl -v -X GET "https://{Endpoint}/formrecognizer/v2.0/layout/analyzeResults/{resultId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```


# <a name="v21-preview"></a>[Vorschauversion v2.1](#tab/v2-1)  
```bash
curl -v -X GET "https://{Endpoint}/formrecognizer/v2.1-preview.2/layout/analyzeResults/{resultId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```
---

### <a name="examine-the-results"></a>Untersuchen der Ergebnisse

Sie erhalten die Antwort `200 (success)` mit dem JSON-Inhalt.

Vergleichen Sie das folgende Bild einer Rechnung mit der entsprechenden JSON-Ausgabe. Die Ausgabe wurde zur Vereinfachung gekürzt. Der Knoten `"readResults"` enthält jede Textzeile mit der Platzierung des zugehörigen Begrenzungsrahmens auf der Seite. Der Knoten `"selectionMarks"` (in der Vorschauversion v2.1) zeigt jede Auswahlmarkierung (Kontrollkästchen, Optionsfeld) und ihren Status („ausgewählt“ oder „nicht ausgewählt“). Der Abschnitt `"pageResults"` enthält die extrahierten Tabellen. Für jede Tabelle werden der Text-, Zeilen- und Spaltenindex, die Zeilen- und Spaltenaufteilung, der Begrenzungsrahmen und mehr extrahiert.

:::image type="content" source="../../media/contoso-invoice.png" alt-text="Contoso-Projektangabedokument mit einer Tabelle":::

# <a name="v20"></a>[v2.0](#tab/v2-0)    
```json
{
    "status": "succeeded",
    "createdDateTime": "2020-08-20T20:36:52Z",
    "lastUpdatedDateTime": "2020-08-20T20:36:58Z",
    "analyzeResult": {
        "version": "2.0.0",
        "readResults": [
            {
                "page": 1,
                "language": "en",
                "angle": 0,
                "width": 8.5,
                "height": 11,
                "unit": "inch",
                "lines": [
                    {
                        "boundingBox": [
                            0.5826,
                            0.4411,
                            2.3387,
                            0.4411,
                            2.3387,
                            0.7969,
                            0.5826,
                            0.7969
                        ],
                        "text": "Contoso, Ltd.",
                        "words": [
                            {
                                "boundingBox": [
                                    0.5826,
                                    0.4411,
                                    1.744,
                                    0.4411,
                                    1.744,
                                    0.7969,
                                    0.5826,
                                    0.7969
                                ],
                                "text": "Contoso,",
                                "confidence": 1
                            },
                            {
                                "boundingBox": [
                                    1.8448,
                                    0.4446,
                                    2.3387,
                                    0.4446,
                                    2.3387,
                                    0.7631,
                                    1.8448,
                                    0.7631
                                ],
                                "text": "Ltd.",
                                "confidence": 1
                            }
                        ]
                    },
                    ...
                ]
            }
        ],
        "pageResults": [
            {
                "page": 1,
                "tables": [
                    {
                        "rows": 5,
                        "columns": 5,
                        "cells": [
                            {
                                "rowIndex": 0,
                                "columnIndex": 0,
                                "text": "Training Date",
                                "boundingBox": [
                                    0.5133,
                                    4.2167,
                                    1.7567,
                                    4.2167,
                                    1.7567,
                                    4.4492,
                                    0.5133,
                                    4.4492
                                ],
                                "elements": [
                                    "#/readResults/0/lines/14/words/0",
                                    "#/readResults/0/lines/14/words/1"
                                ]
                            },
                            ...
                        ]
                    },
                    ...
                ]
            }
        ]
    }
}
```

# <a name="v21-preview"></a>[Vorschauversion v2.1](#tab/v2-1)   
```json
{
    "status": "succeeded",
    "createdDateTime": "2020-08-20T20:40:50Z",
    "lastUpdatedDateTime": "2020-08-20T20:40:55Z",
    "analyzeResult": {
        "version": "2.1.0",
        "readResults": [
            {
                "page": 1,
                "angle": 0,
                "width": 8.5,
                "height": 11,
                "unit": "inch",
                "lines": [
                    {
                        "boundingBox": [
                            0.5826,
                            0.4411,
                            2.3387,
                            0.4411,
                            2.3387,
                            0.7969,
                            0.5826,
                            0.7969
                        ],
                        "text": "Contoso, Ltd.",
                        "words": [
                            {
                                "boundingBox": [
                                    0.5826,
                                    0.4411,
                                    1.744,
                                    0.4411,
                                    1.744,
                                    0.7969,
                                    0.5826,
                                    0.7969
                                ],
                                "text": "Contoso,",
                                "confidence": 1
                            },
                            {
                                "boundingBox": [
                                    1.8448,
                                    0.4446,
                                    2.3387,
                                    0.4446,
                                    2.3387,
                                    0.7631,
                                    1.8448,
                                    0.7631
                                ],
                                "text": "Ltd.",
                                "confidence": 1
                            }
                        ]
                    },
                    ...
                        ]
                    }
                ],
                "selectionMarks": [
                    {
                        "boundingBox": [
                            3.9737,
                            3.7475,
                            4.1693,
                            3.7475,
                            4.1693,
                            3.9428,
                            3.9737,
                            3.9428
                        ],
                        "confidence": 0.989,
                        "state": "selected"
                    },
                    ...
                ]
            }
        ],
        "pageResults": [
            {
                "page": 1,
                "tables": [
                    {
                        "rows": 5,
                        "columns": 5,
                        "cells": [
                            {
                                "rowIndex": 0,
                                "columnIndex": 0,
                                "text": "Training Date",
                                "boundingBox": [
                                    0.5133,
                                    4.2167,
                                    1.7567,
                                    4.2167,
                                    1.7567,
                                    4.4492,
                                    0.5133,
                                    4.4492
                                ],
                                "elements": [
                                    "#/readResults/0/lines/12/words/0",
                                    "#/readResults/0/lines/12/words/1"
                                ]
                            },
                            ...
                        ]
                    },
                    ...
                ]
            }
        ]
    }
}
``` 

---



## <a name="analyze-invoices"></a>Analysieren von Rechnungen

# <a name="version-20"></a>[Version 2.0](#tab/v2-0)

> [!IMPORTANT]
> Dieses Feature ist in der ausgewählten API-Version nicht verfügbar.

# <a name="version-21-preview"></a>[Version 2.1 (Vorschau)](#tab/v2-1)

Verwenden Sie den folgenden cURL-Befehl, um mit dem Analysieren einer Rechnung zu beginnen. Weitere Informationen zur Rechnungsanalyse finden Sie im [Konzeptleitfaden zu Rechnungen](../../concept-invoices.md). Nehmen Sie die folgenden Änderungen vor, bevor Sie den Befehl ausführen:

1. Ersetzen Sie `{Endpoint}` durch den Endpunkt, den Sie mit Ihrem Abonnement für die Formularerkennung erhalten haben.
1. Ersetzen Sie `{your invoice URL}` durch die URL-Adresse eines Rechnungsdokuments.
1. Ersetzen Sie `{subscription key}` durch Ihren Abonnementschlüssel.


```bash
curl -v -i POST "https://{Endpoint}/formrecognizer/v2.1-preview.2/prebuilt/invoice/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key:  {subscription key}" --data-ascii "{'source': '{your invoice URL}'}"
```

Sie erhalten die Antwort `202 (Success)` mit einem **Operation-Location**-Header. Der Wert dieses Headers enthält eine Vorgangs-ID, mit der Sie den Status des asynchronen Vorgangs abfragen und die Ergebnisse abrufen können.

```console
https://cognitiveservice/formrecognizer/v2.1-preview.2/prebuilt/invoice/analyzeResults/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

### <a name="get-invoice-results"></a>Abrufen von Rechnungsergebnissen

Nachdem Sie die **[Analyze Invoice](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/5ed8c9843c2794cbb1a96291)** -API aufgerufen haben, rufen Sie die **[Get Analyze Invoice Result](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/5ed8c9acb78c40a2533aee83)** -API auf, um den Status des Vorgangs und die extrahierten Daten abzurufen. Nehmen Sie die folgenden Änderungen vor, bevor Sie den Befehl ausführen:

1. Ersetzen Sie `{Endpoint}` durch den Endpunkt, den Sie mit Ihrem Abonnementschlüssel für die Formularerkennung erhalten haben. Sie finden ihn auf der Registerkarte **Übersicht** der Formularerkennungsressource.
1. Ersetzen Sie `{resultId}` durch die Vorgangs-ID aus dem vorherigen Schritt.
1. Ersetzen Sie `{subscription key}` durch Ihren Abonnementschlüssel.

```bash
curl -v -X GET "https://{Endpoint}/formrecognizer/v2.1-preview.2/prebuilt/invoice/analyzeResults/{resultId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="examine-the-response"></a>Untersuchen der Antwort

Sie erhalten die Antwort `200 (Success)` mit der folgenden JSON-Ausgabe: Das `"readResults"` Feld enthält alle aus der Rechnung extrahierten Textzeilen, `"pageResults"` enthält die aus der Rechnung extrahierten Tabellen und Auswahlmarkierungen, und das Feld `"documentResults"` enthält Schlüssel-/Wertinformationen für die wichtigsten Teile der Rechnung.

Sehen Sie sich das folgende Rechnungsdokument und die zugehörige JSON-Ausgabe an. Der JSON-Inhalt wurde gekürzt, um die Lesbarkeit zu verbessern.

* [Beispielrechnung](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/curl/form-recognizer/sample-invoice.pdf)

```json
{
    "status": "succeeded",
    "createdDateTime": "2020-11-06T23:32:11Z",
    "lastUpdatedDateTime": "2020-11-06T23:32:20Z",
    "analyzeResult": {
        "version": "2.1.0",
        "readResults": [{
            "page": 1,
            "angle": 0,
            "width": 8.5,
            "height": 11,
            "unit": "inch"
        }],
        "pageResults": [{
            "page": 1,
            "tables": [{
                "rows": 3,
                "columns": 4,
                "cells": [{
                    "rowIndex": 0,
                    "columnIndex": 0,
                    "text": "QUANTITY",
                    "boundingBox": [0.4953,
                    5.7306,
                    1.8097,
                    5.7306,
                    1.7942,
                    6.0122,
                    0.4953,
                    6.0122]
                },
                {
                    "rowIndex": 0,
                    "columnIndex": 1,
                    "text": "DESCRIPTION",
                    "boundingBox": [1.8097,
                    5.7306,
                    5.7529,
                    5.7306,
                    5.7452,
                    6.0122,
                    1.7942,
                    6.0122]
                },
                ...
                ],
                "boundingBox": [0.4794,
                5.7132,
                8.0158,
                5.714,
                8.0118,
                6.5627,
                0.4757,
                6.5619]
            },
            {
                "rows": 2,
                "columns": 6,
                "cells": [{
                    "rowIndex": 0,
                    "columnIndex": 0,
                    "text": "SALESPERSON",
                    "boundingBox": [0.4979,
                    4.963,
                    1.8051,
                    4.963,
                    1.7975,
                    5.2398,
                    0.5056,
                    5.2398]
                },
                {
                    "rowIndex": 0,
                    "columnIndex": 1,
                    "text": "P.O. NUMBER",
                    "boundingBox": [1.8051,
                    4.963,
                    3.3047,
                    4.963,
                    3.3124,
                    5.2398,
                    1.7975,
                    5.2398]
                },
                ...
                ],
                "boundingBox": [0.4976,
                4.961,
                7.9959,
                4.9606,
                7.9959,
                5.5204,
                0.4972,
                5.5209]
            }]
        }],
        "documentResults": [{
            "docType": "prebuilt:invoice",
            "pageRange": [1,
            1],
            "fields": {
                "AmountDue": {
                    "type": "number",
                    "valueNumber": 610,
                    "text": "$610.00",
                    "boundingBox": [7.3809,
                    7.8153,
                    7.9167,
                    7.8153,
                    7.9167,
                    7.9591,
                    7.3809,
                    7.9591],
                    "page": 1,
                    "confidence": 0.875
                },
                "BillingAddress": {
                    "type": "string",
                    "valueString": "123 Bill St, Redmond WA, 98052",
                    "text": "123 Bill St, Redmond WA, 98052",
                    "boundingBox": [0.594,
                    4.3724,
                    2.0125,
                    4.3724,
                    2.0125,
                    4.7125,
                    0.594,
                    4.7125],
                    "page": 1,
                    "confidence": 0.997
                },
                "BillingAddressRecipient": {
                    "type": "string",
                    "valueString": "Microsoft Finance",
                    "text": "Microsoft Finance",
                    "boundingBox": [0.594,
                    4.1684,
                    1.7907,
                    4.1684,
                    1.7907,
                    4.2837,
                    0.594,
                    4.2837],
                    "page": 1,
                    "confidence": 0.998
                },
                ...                
            }
        }]
    }
}
```

---

## <a name="train-a-custom-model"></a>Trainieren eines benutzerdefinierten Modells

Zum Trainieren eines benutzerdefinierten Modells benötigen Sie zunächst Trainingsdaten in einem Azure Storage-Blob. Sie benötigen mindestens fünf ausgefüllte Formulare (PDF-Dokumente und/oder Bilder) desselben Typs/mit der gleichen Struktur. Unter [Erstellen eines Schulungsdatensatzes für ein benutzerdefiniertes Modell](../../build-training-data-set.md) finden Sie Tipps und Optionen für das Zusammenstellen von Trainingsdaten.

Das Training ohne beschriftete Daten ist der Standardvorgang und einfacher. Alternativ können Sie einige oder alle Ihrer Trainingsdaten vorab manuell beschriften. Dies ist ein komplexerer Prozess, der jedoch ein besser trainiertes Modell hervorbringt.

> [!NOTE]
> Sie können Modelle auch mithilfe einer grafischen Benutzeroberfläche trainieren, z. B. dem [Formularerkennungstool für die Bezeichnung von Beispielen](../../quickstarts/label-tool.md).


### <a name="train-a-model-without-labels"></a>Trainieren eines Modells ohne Bezeichnungen

Um ein Formularerkennungsmodell mit den Dokumenten in Ihrem Azure-Blobcontainer zu trainieren, rufen Sie die **[Train Custom Model](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/TrainCustomModelAsync)** -API (Trainieren des benutzerdefinierten Modells) auf, indem Sie den folgenden cURL-Befehl ausführen. Nehmen Sie die folgenden Änderungen vor, bevor Sie den Befehl ausführen:

1. Ersetzen Sie `{Endpoint}` durch den Endpunkt, den Sie mit Ihrem Abonnement für die Formularerkennung erhalten haben.
1. Ersetzen Sie `{subscription key}` durch den Abonnementschlüssel, den Sie im vorherigen Schritt kopiert haben.
1. Ersetzen Sie `{SAS URL}` mit der Shared Access Signature-URL (SAS) des Azure Blob Storage-Containers. [!INCLUDE [get SAS URL](../sas-instructions.md)]

   :::image type="content" source="../../media/quickstarts/get-sas-url.png" alt-text="SAS-URL-Abruf":::

# <a name="v20"></a>[v2.0](#tab/v2-0)
```bash
curl -i -X POST "https://{Endpoint}/formrecognizer/v2.0/custom/models" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{SAS URL}'}"
```

# <a name="v21-preview"></a>[Vorschauversion v2.1](#tab/v2-1)    
```bash
curl -i -X POST "https://{Endpoint}/formrecognizer/v2.1-preview.2/custom/models" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{SAS URL}'}"
```

---


Sie erhalten eine `201 (Success)`-Antwort mit einem **Location**-Header. Der Wert dieses Headers ist die ID des neuen Modells, das trainiert wird.

### <a name="train-a-model-with-labels"></a>Trainieren eines Modells mit Bezeichnungen

Zum Training mit Bezeichnungen benötigen Sie zusätzlich zu den Trainingsdokumenten spezielle Informationsdateien mit Bezeichnungen (`\<filename\>.pdf.labels.json`) in Ihrem Blobspeichercontainer. Das [Formularerkennungstool für die Bezeichnung von Beispielen](../../quickstarts/label-tool.md) bietet eine Benutzeroberfläche, auf der Sie diese Bezeichnungsdateien erstellen können. Sobald Sie über diese Dateien verfügen, können Sie die **[Train Custom Model](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/TrainCustomModelAsync)** -API aufrufen. Legen Sie dabei im JSON-Text den Parameter `"useLabelFile"` auf `true` fest.

Nehmen Sie die folgenden Änderungen vor, bevor Sie den Befehl ausführen:

1. Ersetzen Sie `{Endpoint}` durch den Endpunkt, den Sie mit Ihrem Abonnement für die Formularerkennung erhalten haben.
1. Ersetzen Sie `{subscription key}` durch den Abonnementschlüssel, den Sie im vorherigen Schritt kopiert haben.
1. Ersetzen Sie `{SAS URL}` mit der Shared Access Signature-URL (SAS) des Azure Blob Storage-Containers. [!INCLUDE [get SAS URL](../sas-instructions.md)]

   :::image type="content" source="../../media/quickstarts/get-sas-url.png" alt-text="SAS-URL-Abruf":::

# <a name="v20"></a>[v2.0](#tab/v2-0)
```bash
curl -i -X POST "https://{Endpoint}/formrecognizer/v2.0/custom/models" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{SAS URL}', 'useLabelFile':true }"
```
# <a name="v21-preview"></a>[Vorschauversion v2.1](#tab/v2-1)    
```bash
curl -i -X POST "https://{Endpoint}/formrecognizer/v2.1-preview.2/custom/models" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{SAS URL}', 'useLabelFile':true}"
```

---


Sie erhalten eine `201 (Success)`-Antwort mit einem **Location**-Header. Der Wert dieses Headers ist die ID des neuen Modells, das trainiert wird.

### <a name="get-training-results"></a>Abrufen der Trainingsergebnisse

Nachdem Sie das Training gestartet haben, verwenden Sie einen neuen Vorgang, **[Get Custom Model](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/GetCustomModel)** (Benutzerdefiniertes Modell abrufen), um den Trainingsstatus zu überprüfen. Übergeben Sie die Modell-ID an diesen API-Aufruf, um den Trainingsstatus zu überprüfen:

1. Ersetzen Sie `{Endpoint}` durch den Endpunkt, den Sie mit Ihrem Abonnementschlüssel für die Formularerkennung erhalten haben.
1. Ersetzen Sie `{subscription key}` durch Ihren Abonnementschlüssel.
1. Ersetzen Sie `{model ID}` durch die Modell-ID, die Sie im vorherigen Schritt erhalten haben.


# <a name="v20"></a>[v2.0](#tab/v2-0)    
```bash
curl -X GET "https://{Endpoint}/formrecognizer/v2.0/custom/models/{model ID}" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```
# <a name="v21-preview"></a>[Vorschauversion v2.1](#tab/v2-1)    
```bash
curl -X GET "https://{Endpoint}/formrecognizer/v2.1-preview.2/custom/models/{model ID}" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```
    
---

Sie erhalten eine `200 (Success)`-Antwort mit einem JSON-Text im folgenden Format. Beachten Sie das `"status"`-Feld. Dies hat nach Abschluss des Trainings den Wert `"ready"`. Wenn das Training des Modells noch nicht abgeschlossen ist, müssen Sie den Dienst erneut abfragen, indem Sie den Befehl erneut ausführen. Ein Intervall von mindestens einer Sekunde zwischen den Aufrufen wird empfohlen.

Das `"modelId"`-Feld enthält die ID des Modells, das Sie trainieren. Sie benötigen sie im nächsten Schritt.

```json
{
  "modelInfo":{
    "status":"ready",
    "createdDateTime":"2019-10-08T10:20:31.957784",
    "lastUpdatedDateTime":"2019-10-08T14:20:41+00:00",
    "modelId":"1cfb372bab404ba3aa59481ab2c63da5"
  },
  "trainResult":{
    "trainingDocuments":[
      {
        "documentName":"invoices\\Invoice_1.pdf",
        "pages":1,
        "errors":[

        ],
        "status":"succeeded"
      },
      {
        "documentName":"invoices\\Invoice_2.pdf",
        "pages":1,
        "errors":[

        ],
        "status":"succeeded"
      },
      {
        "documentName":"invoices\\Invoice_3.pdf",
        "pages":1,
        "errors":[

        ],
        "status":"succeeded"
      },
      {
        "documentName":"invoices\\Invoice_4.pdf",
        "pages":1,
        "errors":[

        ],
        "status":"succeeded"
      },
      {
        "documentName":"invoices\\Invoice_5.pdf",
        "pages":1,
        "errors":[

        ],
        "status":"succeeded"
      }
    ],
    "errors":[

    ]
  },
  "keys":{
    "0":[
      "Address:",
      "Invoice For:",
      "Microsoft",
      "Page"
    ]
  }
}
```

## <a name="analyze-forms-with-a-custom-model"></a>Analysieren von Formularen mit einem benutzerdefinierten Modell

Als Nächstes verwenden Sie Ihr gerade trainiertes Modell, um ein Dokument zu analysieren und Schlüssel-Werte-Paare und Tabellen daraus zu extrahieren. Rufen Sie die **[Analyze Form](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm)** -API (Formular analysieren) auf, indem Sie den folgenden cURL-Befehl ausführen. Nehmen Sie die folgenden Änderungen vor, bevor Sie den Befehl ausführen:

1. Ersetzen Sie `{Endpoint}` durch den Endpunkt, den Sie mit Ihrem Abonnementschlüssel für die Formularerkennung erhalten haben. Sie finden ihn auf der Registerkarte **Übersicht** der Formularerkennungsressource.
1. Ersetzen Sie `{model ID}` durch die Modell-ID, die Sie im vorherigen Abschnitt erhalten haben.
1. Ersetzen Sie `{SAS URL}` durch eine SAS-URL zu Ihrer Datei in Azure Storage. Befolgen Sie die Schritte im Abschnitt „Training“. Rufen Sie jedoch anstelle einer SAS-URL für den gesamten Blobcontainer eine URL für die zu analysierende Datei ab.
1. Ersetzen Sie `{subscription key}` durch Ihren Abonnementschlüssel.

# <a name="v20"></a>[v2.0](#tab/v2-0)    

```bash
curl -v "https://{Endpoint}/formrecognizer/v2.0/custom/models/{model ID}/analyze?includeTextDetails=true" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" -d "{ 'source': '{SAS URL}' } "
```

# <a name="v21-preview"></a>[Vorschauversion v2.1](#tab/v2-1)    
```bash
curl -v "https://{Endpoint}/formrecognizer/v2.1-preview.2/custom/models/{model ID}/analyze?includeTextDetails=true" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" -d "{ 'source': '{SAS URL}' } "
```
    
---



Sie erhalten eine `202 (Success)`-Antwort mit einem **Operation-Location**-Header. Der Wert dieses Headers enthält eine Ergebnis-ID, mit der Sie die Ergebnisse des Analysevorgangs nachverfolgen können. Speichern Sie diese Ergebnis-ID für den nächsten Schritt.

### <a name="get-the-analyze-results"></a>Abrufen der Analyseergebnisse

Rufen Sie die **[Analyze Form Result](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/GetAnalyzeFormResult)** -API auf, um die Ergebnisse des Analysevorgangs abzufragen.

1. Ersetzen Sie `{Endpoint}` durch den Endpunkt, den Sie mit Ihrem Abonnementschlüssel für die Formularerkennung erhalten haben. Sie finden ihn auf der Registerkarte **Übersicht** der Formularerkennungsressource.
1. Ersetzen Sie `{result ID}` durch die ID, die Sie im vorherigen Abschnitt erhalten haben.
1. Ersetzen Sie `{subscription key}` durch Ihren Abonnementschlüssel.

# <a name="v20"></a>[v2.0](#tab/v2-0)    
```bash
curl -X GET "https://{Endpoint}/formrecognizer/v2.0/custom/models/{model ID}/analyzeResults/{result ID}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```
# <a name="v21-preview"></a>[Vorschauversion v2.1](#tab/v2-1)    
```bash
curl -X GET "https://{Endpoint}/formrecognizer/v2.1-preview/custom/models/{model ID}/analyzeResults/{result ID}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```
---

Sie erhalten eine `200 (Success)`-Antwort mit einem JSON-Text im folgenden Format. Die Ausgabe wurde zur Vereinfachung gekürzt. Beachten Sie das `"status"`-Feld im unteren Bereich. Dies hat den Wert `"succeeded"`, wenn der Analysevorgang beendet ist. Wenn der Analysevorgang noch nicht abgeschlossen ist, müssen Sie den Dienst erneut abfragen, indem Sie den Befehl erneut ausführen. Ein Intervall von mindestens einer Sekunde zwischen den Aufrufen wird empfohlen.

In benutzerdefinierten Modellen, die ohne Bezeichnungen trainiert werden, befinden sich die Schlüssel-Wert-Paar-Zuordnungen und Tabellen im Knoten `"pageResults"` der JSON-Ausgabe. In benutzerdefinierten Modellen, die mit Bezeichnungen trainiert werden, befinden sich die Schlüssel-Wert-Paar-Zuordnungen im Knoten `"documentResults"`. Wenn Sie auch die Nur-Text-Extraktion über den *includeTextDetails*-URL-Parameter angegeben haben, zeigt der Knoten `"readResults"` den Inhalt und die Positionen des gesamten Texts im Dokument an.

Diese JSON-Beispielausgabe wurde zur Vereinfachung gekürzt.

# <a name="v20"></a>[v2.0](#tab/v2-0)
```JSON
{
  "status": "succeeded",
  "createdDateTime": "2020-08-21T00:46:25Z",
  "lastUpdatedDateTime": "2020-08-21T00:46:32Z",
  "analyzeResult": {
    "version": "2.0.0",
    "readResults": [
      {
        "page": 1,
        "angle": 0,
        "width": 8.5,
        "height": 11,
        "unit": "inch",
        "lines": [
          {
            "text": "Project Statement",
            "boundingBox": [
              5.0153,
              0.275,
              8.0944,
              0.275,
              8.0944,
              0.7125,
              5.0153,
              0.7125
            ],
            "words": [
              {
                "text": "Project",
                "boundingBox": [
                  5.0153,
                  0.275,
                  6.2278,
                  0.275,
                  6.2278,
                  0.7125,
                  5.0153,
                  0.7125
                ]
              },
              {
                "text": "Statement",
                "boundingBox": [
                  6.3292,
                  0.275,
                  8.0944,
                  0.275,
                  8.0944,
                  0.7125,
                  6.3292,
                  0.7125
                ]
              }
            ]
          }, 
        ...
        ]
      }
    ],
    "pageResults": [
      {
        "page": 1,
        "keyValuePairs": [
          {
            "key": {
              "text": "Date:",
              "boundingBox": [
                6.9722,
                1.0264,
                7.3417,
                1.0264,
                7.3417,
                1.1931,
                6.9722,
                1.1931
              ],
              "elements": [
                "#/readResults/0/lines/2/words/0"
              ]
            },
            "confidence": 1
          },
         ...
        ],
        "tables": [
          {
            "rows": 4,
            "columns": 5,
            "cells": [
              {
                "text": "Training Date",
                "rowIndex": 0,
                "columnIndex": 0,
                "boundingBox": [
                  0.6931,
                  4.2444,
                  1.5681,
                  4.2444,
                  1.5681,
                  4.4125,
                  0.6931,
                  4.4125
                ],
                "confidence": 1,
                "rowSpan": 1,
                "columnSpan": 1,
                "elements": [
                  "#/readResults/0/lines/15/words/0",
                  "#/readResults/0/lines/15/words/1"
                ],
                "isHeader": true,
                "isFooter": false
              },
              ...
            ]
          }
        ], 
        "clusterId": 0
      }
    ],
    "documentResults": [],
    "errors": []
  }
}
```    
# <a name="v21-preview"></a>[Vorschauversion v2.1](#tab/v2-1)    
```JSON
{
  "status": "succeeded",
  "createdDateTime": "2020-08-21T01:13:28Z",
  "lastUpdatedDateTime": "2020-08-21T01:13:42Z",
  "analyzeResult": {
    "version": "2.1.0",
    "readResults": [
      {
        "page": 1,
        "angle": 0,
        "width": 8.5,
        "height": 11,
        "unit": "inch",
        "lines": [
          {
            "text": "Project Statement",
            "boundingBox": [
              5.0444,
              0.3613,
              8.0917,
              0.3613,
              8.0917,
              0.6718,
              5.0444,
              0.6718
            ],
            "words": [
              {
                "text": "Project",
                "boundingBox": [
                  5.0444,
                  0.3587,
                  6.2264,
                  0.3587,
                  6.2264,
                  0.708,
                  5.0444,
                  0.708
                ]
              },
              {
                "text": "Statement",
                "boundingBox": [
                  6.3361,
                  0.3635,
                  8.0917,
                  0.3635,
                  8.0917,
                  0.6396,
                  6.3361,
                  0.6396
                ]
              }
            ]
          }, 
          ...
        ] 
      }
    ],
    "pageResults": [
      {
        "page": 1,
        "keyValuePairs": [
          {
            "key": {
              "text": "Date:",
              "boundingBox": [
                6.9833,
                1.0615,
                7.3333,
                1.0615,
                7.3333,
                1.1649,
                6.9833,
                1.1649
              ],
              "elements": [
                "#/readResults/0/lines/2/words/0"
              ]
            },
            "value": {
              "text": "9/10/2020",
              "boundingBox": [
                7.3833,
                1.0802,
                7.925,
                1.0802,
                7.925,
                1.174,
                7.3833,
                1.174
              ],
              "elements": [
                "#/readResults/0/lines/3/words/0"
              ]
            },
            "confidence": 1
          },
          ...
        ], 
        "tables": [
          {
            "rows": 5,
            "columns": 5,
            "cells": [
              {
                "text": "Training Date",
                "rowIndex": 0,
                "columnIndex": 0,
                "boundingBox": [
                  0.6944,
                  4.2779,
                  1.5625,
                  4.2779,
                  1.5625,
                  4.4005,
                  0.6944,
                  4.4005
                ],
                "confidence": 1,
                "rowSpan": 1,
                "columnSpan": 1,
                "elements": [
                  "#/readResults/0/lines/15/words/0",
                  "#/readResults/0/lines/15/words/1"
                ],
                "isHeader": true,
                "isFooter": false
              },
              ...
            ]
          }
        ], 
        "clusterId": 0
      }
    ], 
    "documentResults": [],
    "errors": []
  }
}
``` 
---

### <a name="improve-results"></a>Verbessern der Ergebnisse

[!INCLUDE [improve results](../improve-results-unlabeled.md)]

## <a name="analyze-receipts"></a>Analysieren von Belegen

In diesem Abschnitt wird veranschaulicht, wie Sie mithilfe eines vorab trainierten Belegmodells gängige Felder in US-Belegen analysieren und extrahieren. Weitere Informationen zur Beleganalyse finden Sie im [Konzeptleitfaden zu Belegen](../../concept-receipts.md). Rufen Sie die **[Analyze Receipt](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeReceiptAsync)** -API mit dem unten angegebenen cURL-Befehl auf, um mit der Analyse eines Verkaufsbelegs zu beginnen. Nehmen Sie die folgenden Änderungen vor, bevor Sie den Befehl ausführen:

1. Ersetzen Sie `{Endpoint}` durch den Endpunkt, den Sie mit Ihrem Abonnement für die Formularerkennung erhalten haben.
1. Ersetzen Sie `{your receipt URL}` mit der URL-Adresse eines Verkaufsbelegbilds.
1. Ersetzen Sie `{subscription key>` durch den Abonnementschlüssel, den Sie im vorherigen Schritt kopiert haben.

# <a name="v20"></a>[v2.0](#tab/v2-0)

```bash
curl -i -X POST "https://{Endpoint}/formrecognizer/v2.0/prebuilt/receipt/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{your receipt URL}'}"
```

# <a name="v21-preview"></a>[Vorschauversion v2.1](#tab/v2-1)    

```bash
curl -i -X POST "https://{Endpoint}/formrecognizer/v2.1-preview.2/prebuilt/receipt/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{your receipt URL}'}"
```
---

Sie erhalten die Antwort `202 (Success)` mit einem **Operation-Location**-Header. Der Wert dieses Headers enthält eine Vorgangs-ID, mit der Sie den Status des asynchronen Vorgangs abfragen und die Ergebnisse abrufen können. Im folgenden Beispiel ist die Zeichenfolge nach `operations/` die Vorgangs-ID.

```console
https://cognitiveservice/formrecognizer/v2.1-preview.2/prebuilt/receipt/operations/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

### <a name="get-the-receipt-results"></a>Abrufen der Verkaufsbelegergebnisse

Nachdem Sie die **Analyze Receipt**-API aufgerufen haben, rufen Sie die **[Get Analyze Receipt Result](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/GetAnalyzeReceiptResult)** -API auf, um den Status des Vorgangs und die extrahierten Daten abzurufen. Nehmen Sie die folgenden Änderungen vor, bevor Sie den Befehl ausführen:

1. Ersetzen Sie `{Endpoint}` durch den Endpunkt, den Sie mit Ihrem Abonnementschlüssel für die Formularerkennung erhalten haben. Sie finden ihn auf der Registerkarte **Übersicht** der Formularerkennungsressource.
1. Ersetzen Sie `{operationId}` durch die Vorgangs-ID aus dem vorherigen Schritt.
1. Ersetzen Sie `{subscription key}` durch Ihren Abonnementschlüssel.

# <a name="v20"></a>[v2.0](#tab/v2-0)  
```bash
curl -X GET "https://{Endpoint}/formrecognizer/v2.0/prebuilt/receipt/analyzeResults/{operationId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```
# <a name="v21-preview"></a>[Vorschauversion v2.1](#tab/v2-1)  
```bash
curl -X GET "https://{Endpoint}/formrecognizer/v2.1-preview.2/prebuilt/receipt/analyzeResults/{operationId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```
---

### <a name="examine-the-response"></a>Untersuchen der Antwort

Sie erhalten die Antwort `200 (Success)` mit der folgenden JSON-Ausgabe: Das erste Feld, `"status"`, gibt den Status des Vorgangs an. Wenn der Vorgang abgeschlossen ist, enthält das Feld `"readResults"` jede Textzeile, die aus dem Beleg extrahiert wurde, und das Feld `"documentResults"` enthält Schlüssel-/Wert-Informationen für die wichtigsten Teile des Belegs. Wenn der Vorgang nicht abgeschlossen ist, ist der Wert von `"status"``"running"` oder `"notStarted"`, und Sie sollten die API Aufrufen entweder manuell oder über ein Skript erneut aufrufen. Ein Intervall von mindestens einer Sekunde zwischen den Aufrufen wird empfohlen.

Vergleichen Sie das folgende Bild des Belegs mit der entsprechenden JSON-Ausgabe. Die Ausgabe wurde zur besseren Lesbarkeit gekürzt.

![Ein Verkaufsbeleg vom Contoso-Laden](../../media/contoso-allinone.jpg)

Der Knoten `"readResults"` enthält den gesamten erkannten Text (wenn Sie den optionalen Parameter *includeTextDetails* auf `true` festlegen). Der Text ist nach Seite, dann nach Zeile und dann nach einzelnen Wörtern sortiert. Der Knoten `"documentResults"` enthält die belegspezifischen Werte, die vom Modell erkannt wurden. Hier finden Sie nützliche Schlüssel-Wert-Paare wie Steuern, Gesamtsumme, Händleranschrift usw.

```json
{
  "status":"succeeded",
  "createdDateTime":"2019-12-17T04:11:24Z",
  "lastUpdatedDateTime":"2019-12-17T04:11:32Z",
  "analyzeResult":{
    "version":"2.1.0",
    "readResults":[
      {
        "page":1,
        "angle":0.6893,
        "width":1688,
        "height":3000,
        "unit":"pixel",
        "language":"en",
        "lines":[
          {
            "text":"Contoso",
            "boundingBox":[
              635,
              510,
              1086,
              461,
              1098,
              558,
              643,
              604
            ],
            "words":[
              {
                "text":"Contoso",
                "boundingBox":[
                  639,
                  510,
                  1087,
                  461,
                  1098,
                  551,
                  646,
                  604
                ],
                "confidence":0.955
              }
            ]
          },
          ...
        ]
      }
    ],
    "documentResults":[
      {
        "docType":"prebuilt:receipt",
        "pageRange":[
          1,
          1
        ],
        "fields":{
          "ReceiptType":{
            "type":"string",
            "valueString":"Itemized",
            "confidence":0.692
          },
          "MerchantName":{
            "type":"string",
            "valueString":"Contoso Contoso",
            "text":"Contoso Contoso",
            "boundingBox":[
              378.2,
              292.4,
              1117.7,
              468.3,
              1035.7,
              812.7,
              296.3,
              636.8
            ],
            "page":1,
            "confidence":0.613,
            "elements":[
              "#/readResults/0/lines/0/words/0",
              "#/readResults/0/lines/1/words/0"
            ]
          },
          "MerchantAddress":{
            "type":"string",
            "valueString":"123 Main Street Redmond, WA 98052",
            "text":"123 Main Street Redmond, WA 98052",
            "boundingBox":[
              302,
              675.8,
              848.1,
              793.7,
              809.9,
              970.4,
              263.9,
              852.5
            ],
            "page":1,
            "confidence":0.99,
            "elements":[
              "#/readResults/0/lines/2/words/0",
              "#/readResults/0/lines/2/words/1",
              "#/readResults/0/lines/2/words/2",
              "#/readResults/0/lines/3/words/0",
              "#/readResults/0/lines/3/words/1",
              "#/readResults/0/lines/3/words/2"
            ]
          },
          "MerchantPhoneNumber":{
            "type":"phoneNumber",
            "valuePhoneNumber":"+19876543210",
            "text":"987-654-3210",
            "boundingBox":[
              278,
              1004,
              656.3,
              1054.7,
              646.8,
              1125.3,
              268.5,
              1074.7
            ],
            "page":1,
            "confidence":0.99,
            "elements":[
              "#/readResults/0/lines/4/words/0"
            ]
          },
          "TransactionDate":{
            "type":"date",
            "valueDate":"2019-06-10",
            "text":"6/10/2019",
            "boundingBox":[
              265.1,
              1228.4,
              525,
              1247,
              518.9,
              1332.1,
              259,
              1313.5
            ],
            "page":1,
            "confidence":0.99,
            "elements":[
              "#/readResults/0/lines/5/words/0"
            ]
          },
          "TransactionTime":{
            "type":"time",
            "valueTime":"13:59:00",
            "text":"13:59",
            "boundingBox":[
              541,
              1248,
              677.3,
              1261.5,
              668.9,
              1346.5,
              532.6,
              1333
            ],
            "page":1,
            "confidence":0.977,
            "elements":[
              "#/readResults/0/lines/5/words/1"
            ]
          },
          "Items":{
            "type":"array",
            "valueArray":[
              {
                "type":"object",
                "valueObject":{
                  "Quantity":{
                    "type":"number",
                    "text":"1",
                    "boundingBox":[
                      245.1,
                      1581.5,
                      300.9,
                      1585.1,
                      295,
                      1676,
                      239.2,
                      1672.4
                    ],
                    "page":1,
                    "confidence":0.92,
                    "elements":[
                      "#/readResults/0/lines/7/words/0"
                    ]
                  },
                  "Name":{
                    "type":"string",
                    "valueString":"Cappuccino",
                    "text":"Cappuccino",
                    "boundingBox":[
                      322,
                      1586,
                      654.2,
                      1601.1,
                      650,
                      1693,
                      317.8,
                      1678
                    ],
                    "page":1,
                    "confidence":0.923,
                    "elements":[
                      "#/readResults/0/lines/7/words/1"
                    ]
                  },
                  "TotalPrice":{
                    "type":"number",
                    "valueNumber":2.2,
                    "text":"$2.20",
                    "boundingBox":[
                      1107.7,
                      1584,
                      1263,
                      1574,
                      1268.3,
                      1656,
                      1113,
                      1666
                    ],
                    "page":1,
                    "confidence":0.918,
                    "elements":[
                      "#/readResults/0/lines/8/words/0"
                    ]
                  }
                }
              },
              ...
            ]
          },
          "Subtotal":{
            "type":"number",
            "valueNumber":11.7,
            "text":"11.70",
            "boundingBox":[
              1146,
              2221,
              1297.3,
              2223,
              1296,
              2319,
              1144.7,
              2317
            ],
            "page":1,
            "confidence":0.955,
            "elements":[
              "#/readResults/0/lines/13/words/1"
            ]
          },
          "Tax":{
            "type":"number",
            "valueNumber":1.17,
            "text":"1.17",
            "boundingBox":[
              1190,
              2359,
              1304,
              2359,
              1304,
              2456,
              1190,
              2456
            ],
            "page":1,
            "confidence":0.979,
            "elements":[
              "#/readResults/0/lines/15/words/1"
            ]
          },
          "Tip":{
            "type":"number",
            "valueNumber":1.63,
            "text":"1.63",
            "boundingBox":[
              1094,
              2479,
              1267.7,
              2485,
              1264,
              2591,
              1090.3,
              2585
            ],
            "page":1,
            "confidence":0.941,
            "elements":[
              "#/readResults/0/lines/17/words/1"
            ]
          },
          "Total":{
            "type":"number",
            "valueNumber":14.5,
            "text":"$14.50",
            "boundingBox":[
              1034.2,
              2617,
              1387.5,
              2638.2,
              1380,
              2763,
              1026.7,
              2741.8
            ],
            "page":1,
            "confidence":0.985,
            "elements":[
              "#/readResults/0/lines/19/words/0"
            ]
          }
        }
      }
    ]
  }
}
```

## <a name="analyze-business-cards"></a>Analysieren von Visitenkarten

# <a name="v20"></a>[v2.0](#tab/v2-0)  

> [!IMPORTANT]
> Dieses Feature ist in der ausgewählten API-Version nicht verfügbar.

# <a name="v21-preview"></a>[Vorschauversion v2.1](#tab/v2-1)  

In diesem Abschnitt wird veranschaulicht, wie Sie mithilfe eines vorab trainierten Modells gängige Felder englischsprachiger Visitenkarten analysieren und extrahieren. Weitere Informationen zur Analyse von Visitenkarten finden Sie im [Konzeptleitfaden zu Visitenkarten](../../concept-business-cards.md). Rufen Sie die API **[Analyze Business Card](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeBusinessCardAsync)** mit dem unten angegebenen cURL-Befehl auf, um mit der Analyse einer Visitenkarte zu beginnen. Nehmen Sie die folgenden Änderungen vor, bevor Sie den Befehl ausführen:

1. Ersetzen Sie `{Endpoint}` durch den Endpunkt, den Sie mit Ihrem Abonnement für die Formularerkennung erhalten haben.
1. Ersetzen Sie `{your receipt URL}` mit der URL-Adresse eines Verkaufsbelegbilds.
1. Ersetzen Sie `{subscription key}` durch den Abonnementschlüssel, den Sie im vorherigen Schritt kopiert haben.

```bash
curl -i -X POST "https://{Endpoint}/formrecognizer/v2.1-preview.2/prebuilt/businessCard/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{your receipt URL}'}"
```

Sie erhalten die Antwort `202 (Success)` mit einem **Operation-Location**-Header. Der Wert dieses Headers enthält eine Vorgangs-ID, mit der Sie den Status des asynchronen Vorgangs abfragen und die Ergebnisse abrufen können.

```console
https://cognitiveservice/formrecognizer/v2.1-preview.2/prebuilt/businessCard/analyzeResults/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

### <a name="get-business-card-results"></a>Abrufen der Ergebnisse für die Visitenkarte

Nachdem Sie die API **Analyze Business Card** aufgerufen haben, rufen Sie die API **[Get Analyze Business Card Result](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/GetAnalyzeBusinessCardResult)** auf, um den Status des Vorgangs und die extrahierten Daten abzurufen. Nehmen Sie die folgenden Änderungen vor, bevor Sie den Befehl ausführen:

1. Ersetzen Sie `{Endpoint}` durch den Endpunkt, den Sie mit Ihrem Abonnementschlüssel für die Formularerkennung erhalten haben. Sie finden ihn auf der Registerkarte **Übersicht** der Formularerkennungsressource.
1. Ersetzen Sie `{resultId}` durch die Vorgangs-ID aus dem vorherigen Schritt.
1. Ersetzen Sie `{subscription key}` durch Ihren Abonnementschlüssel.

```bash
curl -v -X GET "https://westcentralus.api.cognitive.microsoft.com/formrecognizer/v2.1-preview.2/prebuilt/businessCard/analyzeResults/{resultId}"
-H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="examine-the-response"></a>Untersuchen der Antwort

Sie erhalten die Antwort `200 (Success)` mit der folgenden JSON-Ausgabe: Der Knoten `"readResults"` enthält den gesamten erkannten Text. Der Text ist nach Seite, dann nach Zeile und dann nach einzelnen Wörtern sortiert. Der Knoten `"documentResults"` enthält die visitenkartenspezifischen Werte, die vom Modell erkannt wurden. Er enthält nützliche Kontaktinformationen wie Firmenname, Vorname, Nachname, Telefonnummer usw.

![Eine Visitenkarte des Unternehmens Contoso](../../media/business-card-english.jpg)

Das folgende Beispiel veranschaulicht die JSON-Ausgabe, die von der Formularerkennung zurückgegeben wurde. Sie wurde zur besseren Lesbarkeit gekürzt.

```json
{
    "status": "succeeded",
    "createdDateTime": "2020-06-04T08:19:29Z",
    "lastUpdatedDateTime": "2020-06-04T08:19:35Z",
    "analyzeResult": {
        "version": "2.1.1",
        "readResults": [
            {
                "page": 1,
                "angle": -17.0956,
                "width": 4032,
                "height": 3024,
                "unit": "pixel"
            }
        ],
        "documentResults": [
            {
                "docType": "prebuilt:businesscard",
                "pageRange": [
                    1,
                    1
                ],
                "fields": {
                    "ContactNames": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "object",
                                "valueObject": {
                                    "FirstName": {
                                        "type": "string",
                                        "valueString": "Avery",
                                        "text": "Avery",
                                        "boundingBox": [
                                            703,
                                            1096,
                                            1134,
                                            989,
                                            1165,
                                            1109,
                                            733,
                                            1206
                                        ],
                                        "page": 1
                                },
                                "text": "Dr. Avery Smith",
                                "boundingBox": [
                                    419.3,
                                    1154.6,
                                    1589.6,
                                    877.9,
                                    1618.9,
                                    1001.7,
                                    448.6,
                                    1278.4
                                ],
                                "confidence": 0.993
                            }
                        ]
                    },
                    "Emails": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "avery.smith@contoso.com",
                                "text": "avery.smith@contoso.com",
                                "boundingBox": [
                                    2107,
                                    934,
                                    2917,
                                    696,
                                    2935,
                                    764,
                                    2126,
                                    995
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "Websites": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "https://www.contoso.com/",
                                "text": "https://www.contoso.com/",
                                "boundingBox": [
                                    2121,
                                    1002,
                                    2992,
                                    755,
                                    3014,
                                    826,
                                    2143,
                                    1077
                                ],
                                "page": 1,
                                "confidence": 0.995
                            }
                        ]
                    }
                }
            }
        ]
    }
}
```

Das Skript gibt Antworten in der Konsole aus, bis der **Analyze Business Card**-Vorgang abgeschlossen ist.

---

## <a name="manage-custom-models"></a>Verwalten benutzerdefinierter Modelle

### <a name="get-a-list-of-custom-models"></a>Abrufen einer Liste mit benutzerdefinierten Modellen

Verwenden Sie die **[List Custom Models](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/GetCustomModels)** -API im folgenden Befehl, um eine Liste mit allen benutzerdefinierten Modellen zurückzugeben, die zu Ihrem Abonnement gehören.

1. Ersetzen Sie `{Endpoint}` durch den Endpunkt, den Sie mit Ihrem Abonnement für die Formularerkennung erhalten haben.
1. Ersetzen Sie `{subscription key}` durch den Abonnementschlüssel, den Sie im vorherigen Schritt kopiert haben.

# <a name="v20"></a>[v2.0](#tab/v2-0)    
```bash
curl -v -X GET "https://{Endpoint}/formrecognizer/v2.0/custom/models?op=full"
-H "Ocp-Apim-Subscription-Key: {subscription key}"
```

# <a name="v21-preview"></a>[Vorschauversion v2.1](#tab/v2-1)    

```bash
curl -v -X GET "https://{Endpoint}/formrecognizer/v2.1-preview.2/custom/models?op=full"
-H "Ocp-Apim-Subscription-Key: {subscription key}"
```
---

Sie erhalten eine Antwort mit dem Hinweis zur erfolgreichen Durchführung (`200`) und den folgenden JSON-Daten. Das `"modelList"`-Element enthält Ihre gesamten erstellten Modelle und die zugehörigen Informationen.

```json
{
  "summary": {
    "count": 0,
    "limit": 0,
    "lastUpdatedDateTime": "string"
  },
  "modelList": [
    {
      "modelId": "string",
      "status": "creating",
      "createdDateTime": "string",
      "lastUpdatedDateTime": "string"
    }
  ],
  "nextLink": "string"
}
```

### <a name="get-a-specific-model"></a>Abrufen eines bestimmten Modells

Verwenden Sie die **[Get Custom Model](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/GetCustomModel)** -API im folgenden Befehl, um ausführliche Informationen zu einem bestimmten benutzerdefinierten Modell abzurufen.

1. Ersetzen Sie `{Endpoint}` durch den Endpunkt, den Sie mit Ihrem Abonnement für die Formularerkennung erhalten haben.
1. Ersetzen Sie `{subscription key}` durch den Abonnementschlüssel, den Sie im vorherigen Schritt kopiert haben.
1. Ersetzen Sie `{modelId}` durch die ID des benutzerdefinierten Modells, nach dem Sie suchen möchten.

# <a name="v20"></a>[v2.0](#tab/v2-0)    

```bash
curl -v -X GET "https://{Endpoint}/formrecognizer/v2.0/custom/models/{modelId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

# <a name="v21-preview"></a>[Vorschauversion v2.1](#tab/v2-1)    

```bash
curl -v -X GET "https://{Endpoint}/formrecognizer/v2.1-preview.2/custom/models/{modelId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```
---

Sie erhalten eine Antwort mit dem Hinweis zur erfolgreichen Durchführung (`200`) und den folgenden JSON-Daten.

```json
{
  "modelInfo": {
    "modelId": "string",
    "status": "creating",
    "createdDateTime": "string",
    "lastUpdatedDateTime": "string"
  },
  "keys": {
    "clusters": {}
  },
  "trainResult": {
    "trainingDocuments": [
      {
        "documentName": "string",
        "pages": 0,
        "errors": [
          "string"
        ],
        "status": "succeeded"
      }
    ],
    "fields": [
      {
        "fieldName": "string",
        "accuracy": 0.0
      }
    ],
    "averageModelAccuracy": 0.0,
    "errors": [
      {
        "message": "string"
      }
    ]
  }
}
```

### <a name="delete-a-model-from-the-resource-account"></a>Löschen eines Modells aus dem Ressourcenkonto

Sie können ein Modell auch aus Ihrem Konto löschen, indem Sie auf die ID des Modells verweisen. Mit diesem Befehl wird die **[Delete Custom Model](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/DeleteCustomModel)** -API aufgerufen, um das im vorherigen Abschnitt verwendete Modell zu löschen.

1. Ersetzen Sie `{Endpoint}` durch den Endpunkt, den Sie mit Ihrem Abonnement für die Formularerkennung erhalten haben.
1. Ersetzen Sie `{subscription key}` durch den Abonnementschlüssel, den Sie im vorherigen Schritt kopiert haben.
1. Ersetzen Sie `{modelId}` durch die ID des benutzerdefinierten Modells, nach dem Sie suchen möchten.

# <a name="v20"></a>[v2.0](#tab/v2-0)    

```bash
curl -v -X DELETE "https://{Endpoint}/formrecognizer/v2.0/custom/models/{modelId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

# <a name="v21-preview"></a>[Vorschauversion v2.1](#tab/v2-1)

```bash
curl -v -X DELETE "https://{Endpoint}/formrecognizer/v2.1-preview.2/custom/models/{modelId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```
---

Sie erhalten eine `204`-Erfolgsmeldung, in der angegeben ist, dass Ihr Modell zum Löschen markiert wurde. Modellartefakte werden innerhalb von 48 Stunden gelöscht.


## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie die REST-API der Formularerkennung verwendet, um auf unterschiedliche Weise Modelle zu trainieren und Formulare zu analysieren. Lesen Sie als Nächstes die Referenzdokumentation, um die Formularerkennungs-API eingehender zu erkunden.

> [!div class="nextstepaction"]
> [Referenzdokumentation zur Rest-API](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm)

* [Was ist die Formularerkennung?](../../overview.md)
