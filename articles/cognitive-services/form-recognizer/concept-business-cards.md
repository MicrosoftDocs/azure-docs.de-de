---
title: Visitenkarten – Formularerkennung
titleSuffix: Azure Cognitive Services
description: Lernen Sie die Konzepte im Zusammenhang mit der Visitenkartenanalyse mit der Formularerkennungs-API (Verwendung und Einschränkungen) kennen.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 08/17/2019
ms.author: pafarley
ms.openlocfilehash: 4cd762d6c264d95ecb1bd0f3f4c3a4d96eb5a57d
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99585091"
---
# <a name="form-recognizer-prebuilt-business-cards-model"></a>Vordefiniertes Visitenkartenmodell der Formularerkennung 

Mit der Azure-Formularerkennung können Kontaktinformationen aus Visitenkarten mithilfe des vordefinierten Modells für Visitenkarten analysiert und extrahiert werden. Hierbei werden die leistungsstarken Funktionen der optischen Zeichenerkennung (Optical Character Recognition, OCR) mit unserem Modell zur Visitenkartenerfassung kombiniert, um die wichtigsten Informationen auf Visitenkarten zu extrahieren (in englischer Sprache). Dabei werden persönliche Kontaktinformationen, der Firmenname, die Position und weitere Details extrahiert. Die vordefinierte Visitenkarten-API ist in der Vorschauversion der Formularerkennung (v2.1) öffentlich verfügbar. 

## <a name="what-does-the-business-card-service-do"></a>Wozu wird der Dienst für Visitenkarten eingesetzt?

Die vorgefertigte Visitenkarten-API extrahiert wichtige Felder von Visitenkarten und gibt diese Informationen in einer organisierten JSON-Antwort zurück.

![FOTT- und JSON-Ausgabe mit Contoso-Logo](./media/business-card-example.jpg)



### <a name="fields-extracted"></a>Extrahierte Felder:

|Name| type | BESCHREIBUNG | Text | 
|:-----|:----|:----|:----|
| ContactNames | Array von Objekten | Aus der Visitenkarte extrahierter Name des Kontakts | [{ "FirstName": "John", "LastName": "Doe" }] |
| FirstName | Zeichenfolge | Vorname des Kontakts | „John“ | 
| LastName | Zeichenfolge | Nachname des Kontakts |     „Doe“ | 
| CompanyNames | array of strings | Aus der Visitenkarte extrahierter Unternehmensname | ["Contoso"] | 
| Departments | array of strings | Abteilung oder Organisation des Kontakts | ["R&D"] | 
| JobTitles | array of strings | Aufgeführte Position des Kontakts | ["Software Engineer"] | 
| E-Mails | array of strings | Aus der Visitenkarte extrahierte E-Mail-Adresse des Kontakts | ["johndoe@contoso.com"] | 
| Websites | array of strings | Aus der Visitenkarte extrahierte Website | ["https://www.contoso.com"] | 
| Adressen | array of strings | Aus der Visitenkarte extrahierte Adresse | ["123 Main Street, Redmond, WA 98052"] | 
| MobilePhones | Array aus Telefonnummern | Aus der Visitenkarte extrahierte Mobiltelefonnummer | ["+19876543210"] |
| Faxnummern | Array aus Telefonnummern | Aus der Visitenkarte extrahierte Faxnummer | ["+19876543211"] |
| WorkPhones | Array aus Telefonnummern | Aus der Visitenkarte extrahierte geschäftliche Telefonnummer | ["+19876543231"] |
| OtherPhones     | Array aus Telefonnummern | Aus der Visitenkarte extrahierte weitere Telefonnummern | ["+19876543233"] |


Darüber hinaus kann die Visitenkarten-API auch den gesamten erkannten Text auf der Visitenkarte zurückgeben. Diese OCR-Ausgabe ist in der JSON-Antwort enthalten.  

### <a name="input-requirements"></a>Eingabeanforderungen 

[!INCLUDE [input reqs](./includes/input-requirements-receipts.md)]

## <a name="the-analyze-business-card-operation"></a>Der Vorgang zur Analyse der Visitenkarte

Beim Vorgang zur [Analyse der Visitenkarte](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeBusinessCardAsync) wird ein Bild oder eine PDF-Version einer Visitenkarte als Eingabe verwendet, um die relevanten Werte zu extrahieren. Bei diesem Aufruf wird ein Antwortheaderfeld namens `Operation-Location` zurückgegeben. Der `Operation-Location`-Wert ist eine URL, die die Ergebnis-ID enthält, die im nächsten Schritt verwendet werden soll.

|Antwortheader| Ergebnis-URL |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/formrecognizer/v2.1-preview.2/prebuilt/businessCard/analyzeResults/49a36324-fc4b-4387-aa06-090cfbf0064f` |

## <a name="the-get-analyze-business-card-result-operation"></a>Der Vorgang zum Abrufen des Ergebnisses der Visitenkartenanalyse

Im zweiten Schritt wird der Vorgang zum [Abrufen des Ergebnisses der Visitenkartenanalyse](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/GetAnalyzeBusinessCardResult) aufgerufen. Bei diesem Vorgang wird die Ergebnis-ID als Eingabe verwendet, die durch den Vorgang zur Analyse der Visitenkarte erstellt wurde. Er gibt eine JSON-Antwort zurück, die ein **Status**-Feld mit den folgenden möglichen Werten enthält. Sie rufen diesen Vorgang iterativ auf, bis er mit dem Wert **succeeded** (erfolgreich) zurückgegeben wird. Verwenden Sie ein Intervall von 3 bis 5 Sekunden, um zu vermeiden, dass die Rate der Anforderungen pro Sekunde (RPS) überschritten wird.

|Feld| type | Mögliche Werte |
|:-----|:----:|:----|
|status | Zeichenfolge | notStarted: Der Analysevorgang wurde noch nicht gestartet.<br /><br />running: Der Analysevorgang wird ausgeführt.<br /><br />failed: Beim Analysevorgang ist ein Fehler aufgetreten.<br /><br />succeeded: Der Analysevorgang war erfolgreich.|

Wenn im Feld **status** der Wert **succeeded** angezeigt wird, enthält die JSON-Antwort die Ergebnisse der Visitenkartenanalyse und optional die der Texterkennung, sofern diese angefordert wurde. Das Ergebnis der Visitenkartenerfassung ist als Wörterbuch benannter Feldwerte organisiert, wobei jeder Wert den extrahierten Text, den normalisierten Wert, den Begrenzungsrahmen, den Vertrauensgrad und die entsprechenden Wortelemente enthält. Das Ergebnis der Texterkennung ist als eine Hierarchie von Zeilen und Wörtern mit Text, Begrenzungsrahmen und Informationen zum Vertrauensgrad organisiert.

![Beispielausgabe für eine Visitenkarte](./media/business-card-results.png)

### <a name="sample-json-output"></a>JSON-Beispielausgabe

Eine erfolgreiche JSON-Antwort sieht in etwa wie folgendes Beispiel aus: Der readResults-Knoten enthält den gesamten erkannten Text. Der Text ist nach Seite, dann nach Zeile und dann nach einzelnen Wörtern sortiert. Der documentResults-Knoten enthält die visitenkartenspezifischen Werte, die vom Modell erkannt wurden. Er enthält nützliche Kontaktinformationen wie Vorname, Nachname, Firmenname usw.

```json
{
    "status": "succeeded",
    "createdDateTime": "2020-08-20T17:41:19Z",
    "lastUpdatedDateTime": "2020-08-20T17:41:24Z",
    "analyzeResult": {
        "version": "2.1.0",
        "readResults": [
            {
                "page": 1,
                "angle": -17.0956,
                "width": 4032,
                "height": 3024,
                "unit": "pixel",
                   "lines": 
                             {
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
                        "words": [
                            {
                                "text": "Dr.",
                                "boundingBox": [
                                    419,
                            ]
    
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
                                    "LastName": {
                                        "type": "string",
                                        "valueString": "Smith",
                                        "text": "Smith",
                                        "boundingBox": [
                                            1186,
                                            976,
                                            1585,
                                            879,
                                            1618,
                                            998,
                                            1218,
                                            1096
                                        ],
                                        "page": 1
                                    }
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
                                "confidence": 0.97
                            }
                        ]
                    },
                    "JobTitles": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "Senior Researcher",
                                "text": "Senior Researcher",
                                "boundingBox": [
                                    451.8,
                                    1301.9,
                                    1313.5,
                                    1099.9,
                                    1333.8,
                                    1186.7,
                                    472.2,
                                    1388.7
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "Departments": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "Cloud & Al Department",
                                "text": "Cloud & Al Department",
                                "boundingBox": [
                                    480.1,
                                    1403.3,
                                    1590.5,
                                    1129.6,
                                    1612.6,
                                    1219.6,
                                    502.3,
                                    1493.3
                                ],
                                "page": 1,
                                "confidence": 0.99
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
                    },
                    "MobilePhones": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "phoneNumber",
                                "text": "+44 (0) 7911 123456",
                                "boundingBox": [
                                    2434.9,
                                    1033.3,
                                    3072,
                                    836,
                                    3096.2,
                                    914.3,
                                    2459.1,
                                    1111.6
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "OtherPhones": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "phoneNumber",
                                "text": "+44 (0) 20 9876 5432",
                                "boundingBox": [
                                    2473.2,
                                    1115.4,
                                    3139.2,
                                    907.7,
                                    3163.2,
                                    984.7,
                                    2497.2,
                                    1192.4
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "Faxes": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "phoneNumber",
                                "text": "+44 (0) 20 6789 2345",
                                "boundingBox": [
                                    2525,
                                    1185.4,
                                    3192.4,
                                    977.9,
                                    3217.9,
                                    1060,
                                    2550.5,
                                    1267.5
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "Addresses": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "2 Kingdom Street Paddington, London, W2 6BD",
                                "text": "2 Kingdom Street Paddington, London, W2 6BD",
                                "boundingBox": [
                                    1230,
                                    2138,
                                    2535.2,
                                    1678.6,
                                    2614.2,
                                    1903.1,
                                    1309,
                                    2362.5
                                ],
                                "page": 1,
                                "confidence": 0.977
                            }
                        ]
                    },
                    "CompanyNames": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "Contoso",
                                "text": "Contoso",
                                "boundingBox": [
                                    1152,
                                    1916,
                                    2293,
                                    1552,
                                    2358,
                                    1733,
                                    1219,
                                    2105
                                ],
                                "page": 1,
                                "confidence": 0.97
                            }
                        ]
                    }
                }
            }
        ]
    }
}
```

Befolgen Sie den [Schnellstart](./QuickStarts/client-library.md), um den Vorgang zum Extrahieren von Visitenkartendaten mit Python und der REST-API zu implementieren.

## <a name="customer-scenarios"></a>Kundenszenarien  

Die Daten, die mit der Visitenkarten-API extrahiert werden, können für eine Vielzahl von Aufgaben verwendet werden. Durch das Extrahieren dieser Kontaktinformationen wird der Zeitaufwand für Mitarbeiter mit Kundenkontakt deutlich reduziert. Im Folgenden finden Sie einige Beispiele dafür, was unsere Kunden mit der Visitenkarten-API erreicht haben:

* Extrahieren der Kontaktinformationen von Visitenkarten und schnelles Erstellen von Telefonkontakten. 
* Integration in CRM, um Kontakte automatisch anhand von Visitenkartenbildern zu erstellen. 
* Verfolgen von Vertriebsleads.  
* Extrahieren von Kontaktinformationen von vorhandenen Visitenkartenbildern als Massenvorgang. 

Die Visitenkarten-API unterstützt zudem die [AI Builder-Funktion zur Verarbeitung von Visitenkarten](/ai-builder/prebuilt-business-card).

## <a name="next-steps"></a>Nächste Schritte

- Befolgen Sie den [Schnellstart](./quickstarts/client-library.md), um mit der Erkennung von Visitenkarten zu beginnen.

## <a name="see-also"></a>Weitere Informationen

* [Was ist die Formularerkennung?](./overview.md)
* [REST-API-Referenzdokumente](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeBusinessCardAsync)
