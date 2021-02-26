---
title: 'Belege: Formularerkennung'
titleSuffix: Azure Cognitive Services
description: Lernen Sie die Konzepte im Zusammenhang mit Beleganalyse mit der Formularerkennung (Verwendung und Einschränkungen) kennen.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 08/17/2019
ms.author: pafarley
ms.openlocfilehash: c1ae52b2b92c5c8d5a1a98632e19d3140672d6ea
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99585040"
---
# <a name="form-recognizer-prebuilt-receipt-model"></a>Vordefiniertes Belegmodell der Formularerkennung

Die Azure-Formularerkennung kann Informationen aus Belegen mithilfe der dazugehörigen vordefinierten Belegmodelle analysieren und extrahieren. Sie kombiniert unsere leistungsstarken Funktionen zur [optischen Zeichenerkennung (Optical Character Recognition, OCR)](../computer-vision/concept-recognizing-text.md) mit Deep-Learning-Modellen zur Beleganalyse, um wichtige Informationen aus Belegen in englischer Sprache zu extrahieren. Die Beleg-API extrahiert wichtige Informationen aus Verkaufsbelegen in englischer Sprache, z. B. Händlername, Transaktionsdatum, Transaktionssumme, Einzelposten und mehr. 

## <a name="understanding-receipts"></a>Informationen zu Belegen 

Viele Unternehmen und Einzelpersonen sind nach wie vor darauf angewiesen, Daten manuell aus ihren Verkaufsbelegen zu extrahieren, sei es für Reisekostenabrechnungen, Rückerstattungen, Buchprüfung, Steuerzwecke, Budgetierung, Marketing oder andere Zwecke. Häufig werden in diesen Szenarien die Originalbelege oder Fotos der physischen Belege zu Validierungszwecken benötigt.  

Das automatische Extrahieren von Daten aus diesen Belegen kann kompliziert sein. Belege können zerknittert und schwer lesbar sein und gedruckte oder handgeschriebene Teile enthalten. Mit dem Smartphone aufgenommene Fotos von Belegen können außerdem eine geringe Qualität aufweisen. Auch die Belegvorlagen und -felder können je nach Markt, Region und Anbieter stark variieren. Diese Herausforderungen sowohl bei der Datenextraktion als auch bei der Felderkennung machen die Belegverarbeitung zu einem einzigartigen Problem.  

Mithilfe der optischen Zeichenerkennung (OCR) und unserem vorgefertigten Belegmodell ermöglicht die Beleg-API diese Belegverarbeitungsszenarien und extrahiert Daten aus den Belegen, z. B. Händlername, Trinkgeld, Gesamtsumme, Einzelposten und mehr. Mit dieser API ist es nicht erforderlich, ein Modell zu trainieren. Sie senden lediglich ein Bild des Belegs an die Beleganalyse-API, und die Daten werden extrahiert.

![Beispielbeleg](./media/receipts-example.jpg)


## <a name="what-does-the-receipt-service-do"></a>Wie funktioniert der Belegdienst? 

Der vordefinierte Belegdienst extrahiert den Inhalt von Verkaufsbelegen – die Art von Beleg, die Sie üblicherweise in einem Restaurant, von einem Einzelhändler oder im Lebensmittelgeschäft erhalten würden.

### <a name="fields-extracted"></a>Extrahierte Felder

|Name| type | BESCHREIBUNG | Text | Wert (standardisierte Ausgabe) |
|:-----|:----|:----|:----| :----|
| ReceiptType | Zeichenfolge | Der Typ des Belegs | Aufgeschlüsselt |  |
| MerchantName | Zeichenfolge | Der Name des Händlers, der den Beleg ausstellt | Contoso |  |
| MerchantPhoneNumber | phoneNumber | Die aufgeführte Telefonnummer des Händlers | 987-654-3210 | +19876543210 |
| MerchantAddress | Zeichenfolge | Die aufgeführte Adresse des Händlers | 123 Main St Redmond WA 98052 |  |
| TransactionDate | date | Das Datum der Ausstellung des Belegs | June 06, 2019 | 2019-06-26 (26. Juni 2019)  |
| TransactionTime | time | Die Uhrzeit der Ausstellung des Belegs | 4:49 PM | 16:49:00  |
| Gesamt | number | Die Gesamttransaktion des Belegs | $14.34 | 14,34 |
| Subtotal (Zwischensumme) | number | Die Zwischensumme des Belegs, oft vor Steuern | $12.34 | 12.34 |
| Tax (Steuern) | number | Die Steuern auf dem Beleg, oft Mehrwertsteuer oder Äquivalent | 2 $ | 2.00 |
| Tip (Trinkgeld) | number | Vom Käufer gegebenes Trinkgeld | $1.00 | 1.00 |
| Elemente | Array von Objekten | Die extrahierten Positionen mit extrahierten Werten für Name, Menge, Stückpreis und Gesamtpreis | |
| Name | Zeichenfolge | Name des Elements | Surface Pro 6 | |
| Menge | number | Menge der einzelnen Positionen | 1 | |
| Preis | number | Einzelpreis der einzelnen Positionen | $999.00 | 999.00 (999,00) |
| Gesamtpreis | number | Gesamtpreis der Position | $999.00 | 999.00 (999,00) |

### <a name="additional-features"></a>Zusätzliche Features

Die Beleg-API gibt außerdem die folgenden Informationen zurück:

* Feldvertrauensgrad (jedes Feld gibt einen zugehörigen Vertrauenswert zurück)
* OCR-Rohtext (OCR-extrahierte Textausgabe für den gesamten Beleg)
* Begrenzungsrahmen für die einzelnen Werte, Zeilen und Wörter

## <a name="try-it-out"></a>Ausprobieren

Wenn Sie den Belegdienst „Formularerkennung“ ausprobieren möchten, wechseln Sie zum Onlinetool für die Beispielbenutzeroberfläche:

> [!div class="nextstepaction"]
> [Ausprobieren von vordefinierten Modellen](https://fott-preview.azurewebsites.net/)

## <a name="input-requirements"></a>Eingabeanforderungen

[!INCLUDE [input reqs](./includes/input-requirements-receipts.md)]

## <a name="supported-locales"></a>Unterstützte Gebietsschemas 

* **Pre-built Receipt v2.0** (GA) unterstützt Verkaufsbelege im Gebietsschema „en-US“.
* **Pre-built Receipt v2.1-preview.2** (Public Preview) fügt zusätzliche Unterstützung für die folgenden EN-Gebietsschemas für Belege hinzu: 
  * en-AU 
  * en-CA 
  * en-GB 
  * en-IN 

  > [!NOTE]
  > Spracheingabe 
  >
  > Prebuilt Receipt v2.1-preview.2 enthält einen optionalen Anforderungsparameter, um ein Beleggebietsschema aus zusätzlichen englischsprachigen Märkten anzugeben. Für Verkaufsbelege in englischer Sprache aus Australien (en-AU), Kanada (en-CA), Großbritannien (en-GB) und Indien (en-IN) können Sie das Gebietsschema angeben, um verbesserte Ergebnisse zu erhalten. Wenn in v2.1-preview.2 kein Gebietsschema angegeben wird, verwendet das Modell standardmäßig das en-US-Modell.


## <a name="the-analyze-receipt-operation"></a>Der Vorgang zum Analysieren von Belegen

[Analyze Receipt](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeReceiptAsync) nimmt ein Bild oder eine PDF-Datei als Eingabe an und extrahiert die relevanten Werte und relevanten Text. Bei diesem Aufruf wird ein Antwortheaderfeld namens `Operation-Location` zurückgegeben. Der `Operation-Location`-Wert ist eine URL, die die Ergebnis-ID enthält, die im nächsten Schritt verwendet werden soll.

|Antwortheader| Ergebnis-URL |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/formrecognizer/v2.0/prebuilt/receipt/analyzeResults/56a36454-fc4d-4354-aa07-880cfbf0064f` |

## <a name="the-get-analyze-receipt-result-operation"></a>Der Vorgang zum Abrufen des Ergebnisses der Beleganalyse

Im zweiten Schritt wird der Vorgang zum [Abrufen des Ergebnisses der Beleganalyse](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/GetAnalyzeReceiptResult) aufgerufen. Dieser Vorgang nimmt als Eingabe die Ergebnis-ID an, die durch den Vorgang „Beleg analysieren“ erstellt wurde. Er gibt eine JSON-Antwort zurück, die ein **Status**-Feld mit den folgenden möglichen Werten enthält. Sie rufen diesen Vorgang iterativ auf, bis er mit dem Wert **succeeded** (erfolgreich) zurückgegeben wird. Verwenden Sie ein Intervall von 3 bis 5 Sekunden, um zu vermeiden, dass die Rate der Anforderungen pro Sekunde (RPS) überschritten wird.

|Feld| type | Mögliche Werte |
|:-----|:----:|:----|
|status | Zeichenfolge | notStarted: Der Analysevorgang wurde noch nicht gestartet. |
| |  | running: Der Analysevorgang wird ausgeführt. |
| |  | failed: Beim Analysevorgang ist ein Fehler aufgetreten. |
| |  | succeeded: Der Analysevorgang war erfolgreich. |

Wenn im Feld **status** der Wert **succeeded** angezeigt wird, enthält die JSON-Antwort die Ergebnisse der Belegerfassung und Texterkennung. Das Ergebnis der Belegerfassung ist als Wörterbuch benannter Feldwerte organisiert, wobei jeder Wert den extrahierten Text, den normalisierten Wert, den Begrenzungsrahmen, den Vertrauensgrad und die entsprechenden Wortelemente enthält. Das Ergebnis der Texterkennung ist als eine Hierarchie von Zeilen und Wörtern mit Text, Begrenzungsrahmen und Informationen zum Vertrauensgrad organisiert.

![Ergebnisse des Beispielbelegs](./media/contoso-receipt-2-information.png)

### <a name="sample-json-output"></a>JSON-Beispielausgabe

Eine erfolgreiche JSON-Antwort sieht in etwa wie folgendes Beispiel aus: Der readResults-Knoten enthält den gesamten erkannten Text. Der Text ist nach Seite, dann nach Zeile und dann nach einzelnen Wörtern sortiert. Der documentResults-Knoten enthält die visitenkartenspezifischen Werte, die vom Modell erkannt wurden. Er enthält nützliche Schlüssel-Wert-Paare wie Vorname, Nachname, Firmenname usw.

```json
{ 
  "status":"succeeded",
  "createdDateTime":"2019-12-17T04:11:24Z",
  "lastUpdatedDateTime":"2019-12-17T04:11:32Z",
  "analyzeResult":{ 
    "version":"2.0.0",
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


## <a name="customer-scenarios"></a>Kundenszenarien  

Die Daten, die mit der Beleg-API extrahiert werden, können für eine Vielzahl von Aufgaben verwendet werden. Im Folgenden finden Sie einige Beispiele dafür, was unsere Kunden mit der Beleg-API erreicht haben. 

### <a name="business-expense-reporting"></a>Berichterstattung zu Geschäftsausgaben  

Bei der Archivierung von Geschäftsausgaben wird oft Zeit damit verbracht, Daten von Bildern von Belegen manuell einzugeben. Mit der Beleg-API können Sie die extrahierten Felder verwenden, um diesen Prozess teilweise zu automatisieren und Ihre Belege schnell zu analysieren.  

Da die Beleg-API über eine einfache JSON-Ausgabe verfügt, können Sie die extrahierten Feldwerte auf verschiedene Weise verwenden. Integrieren Sie sie in interne Ausgabenanwendungen, um Spesenabrechnungen vorab mit Daten aufzufüllen. Wenn Sie mehr über dieses Szenario erfahren möchten, informieren Sie sich darüber, wie Acumatica die Beleg-API nutzt, damit [die Kostenabrechnung zu einem weniger beschwerlichen Vorgang wird](https://customers.microsoft.com/story/762684-acumatica-partner-professional-services-azure).  

### <a name="auditing-and-accounting"></a>Rechnungsprüfung und Rechnungswesen 

Die Ausgabe der Beleg-API kann auch dazu verwendet werden, Analysen zu einer großen Anzahl von Ausgaben an verschiedenen Punkten im Vorgang der Kostenabrechnung und -erstattung durchzuführen. Sie können Belege verarbeiten, um sie für manuelle Überwachung oder schnelle Genehmigungen zu selektieren.  

Die Belegausgabe ist auch für die allgemeine Buchhaltung für den geschäftlichen oder privaten Gebrauch nützlich. Verwenden Sie die Beleg-API, um alle Rohdaten der Belege als Foto oder PDF-Datei in eine digitale Ausgabe umzuwandeln, die verwertbar ist.

### <a name="consumer-behavior"></a>Kundenverhalten 

Belege enthalten nützliche Daten, die Sie verwenden können, um Kundenverhalten und Einkaufstrends zu analysieren.

Die Beleg-API unterstützt außerdem das [AI Builder-Belegverarbeitungsfeature](/ai-builder/prebuilt-receipt-processing).

## <a name="next-steps"></a>Nächste Schritte

- Sehen Sie sich die Informationen unter [Schnellstart: Verwenden der Clientbibliothek für die Formularerkennung](quickstarts/client-library.md) an, um damit zu beginnen, eine Belegverarbeitungs-App mit Formularerkennung in einer Entwicklungssprache Ihrer Wahl zu schreiben.

## <a name="see-also"></a>Weitere Informationen

* [Was ist die Formularerkennung?](./overview.md)
* [REST-API-Referenzdokumente](./index.yml)