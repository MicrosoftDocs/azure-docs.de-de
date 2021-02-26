---
title: Verwenden von Text Analytics for Health
titleSuffix: Azure Cognitive Services
description: Hier erfahren Sie, wie medizinische Informationen mithilfe von Text Analytics for Health aus unstrukturiertem klinischem Text extrahiert und bezeichnet werden.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: aahi
ms.custom: references_regions
ms.openlocfilehash: f7ba6363ec3a38d37ea3df0f76409289069638e8
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99537795"
---
# <a name="how-to-use-text-analytics-for-health-preview"></a>Gewusst wie: Verwenden von Text Analytics for Health (Vorschauversion)

> [!IMPORTANT] 
> Text Analytics for Health ist eine Vorschaufunktion, die „WIE BESEHEN“ und „MIT ALLEN MÄNGELN“ zur Verfügung gestellt wird. Daher sollte **Text Analytics for Health (Vorschau) nicht in einer Produktionsumgebung implementiert oder bereitgestellt werden.** Text Analytics for Health ist nicht für die Verwendung als medizinisches Hilfsmittel, zur klinischen Unterstützung, als Diagnosetool oder als sonstige Technologie für den Einsatz in Diagnose, Therapie, Entschärfung, Behandlung oder Prävention von Krankheiten oder medizinischen Fällen vorgesehen oder verfügbar, und Microsoft erteilt keine Lizenzen oder Rechte zur Nutzung dieser Funktion für die beschriebenen Zwecke. Diese Funktion ist nicht als Ersatz für professionelle medizinische Beratung oder medizinische Gutachten, Diagnosen, Behandlungen oder das klinische Urteilsvermögen einer medizinischen Fachkraft konzipiert oder vorgesehen und sollte nicht als solcher eingesetzt werden. Jede Verwendung von Text Analytics for Health liegt in der alleinigen Verantwortung des Kunden. Microsoft garantiert nicht, dass Text Analytics for Health oder die in Verbindung mit dieser Funktion bereitgestellten Materialien für medizinische Zwecke ausreichen oder anderweitig die gesundheitlichen oder medizinischen Anforderungen von Personen erfüllen. 


Text Analytics for Health ist ein Feature des Textanalyse-API-Diensts, der relevante medizinische Informationen aus unstrukturierten Texten wie Arztbriefen, Entlassungszusammenfassungen, klinischen Dokumenten und elektronischen Gesundheitsakten extrahiert und bezeichnet.  Es gibt zwei Möglichkeiten zur Nutzung dieses Diensts: 

* [Die webbasierte API (asynchron)](#structure-the-api-request-for-the-hosted-asynchronous-web-api)
* [Einen Docker-Container (synchron)](#hosted-asynchronous-web-api-response)   

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Introducing-Text-Analytics-for-Health/player]

## <a name="features"></a>Features

Text Analytics for Health führt Erkennung benannter Entitäten (Named Entity Recognition, NER), Beziehungsextrahierung, Entitätsnegation und Entitätsverknüpfung in englischsprachigem Text aus, um tiefere Einblicke in unstrukturierten klinischen und biomedizinischen Text zu erhalten.

### <a name="named-entity-recognition"></a>[Erkennung benannter Entitäten](#tab/ner)

Die Erkennung benannter Entitäten erkennt Wörter und Ausdrücke in unstrukturiertem Text, die einem oder mehr semantischen Typen zugeordnet werden können, wie etwa Diagnose, Namen von Medikamenten, Symptom/Auffälligkeit oder Alter.

> [!div class="mx-imgBorder"]
> ![Health NER](../media/ta-for-health/health-named-entity-recognition.png)

### <a name="relation-extraction"></a>[Beziehungsextrahierung](#tab/relation-extraction)

Die Beziehungsextrahierung identifiziert sinnvolle Verbindungen zwischen Begriffen, die im Text erwähnt werden. Beispielsweise wird die Beziehung „Zeit der Bedingung“ durch Zuordnen eines Bedingungsnamens zu einem Zeitpunkt gefunden. 

> [!div class="mx-imgBorder"]
> ![Health RE](../media/ta-for-health/health-relation-extraction.png)


### <a name="entity-linking"></a>[Entitätsverknüpfung](#tab/entity-linking)

Entitätsverknüpfung unterscheidet separate Entitäten, indem sie im Text erwähnte benannte Entitäten Konzepten zuordnet, die sie in einer vordefinierten Konzeptdatenbank findet. Beispielsweise im Unified Medical Language System (UMLS).

> [!div class="mx-imgBorder"]
> ![Health EL](../media/ta-for-health/health-entity-linking.png)

Text Analytics for Health unterstützt die Verknüpfung mit den Vokabularen für Gesundheit und Biomedizin in der Metathesaurus Knowledge Source des Unified Medical Language System ([UMLS](https://www.nlm.nih.gov/research/umls/sourcereleasedocs/index.html)).

### <a name="negation-detection"></a>[Negationserkennung](#tab/negation-detection) 

Die Bedeutung medizinischer Inhalte wird in hohem Maße durch Modifizierer wie Verneinung beeinflusst, die bei Fehldiagnosen kritische Auswirkungen haben können. Text Analytics for Health unterstützt die Negationserkennung für die verschiedenen im Text erwähnten Entitäten. 

> [!div class="mx-imgBorder"]
> ![Health NEG](../media/ta-for-health/health-negation.png)

---

Die vollständige Liste der unterstützten Entitäten finden Sie in den von Text Analytics for Health zurückgegeben [Entitätskategorien](../named-entity-types.md?tabs=health). Informationen zu Zuverlässigkeitsbewertungen finden Sie unter dem [Hinweis zur Transparenz der Textanalyse](/legal/cognitive-services/text-analytics/transparency-note#general-guidelines-to-understand-and-improve-performance?context=/azure/cognitive-services/text-analytics/context/context). 

### <a name="supported-languages-and-regions"></a>Unterstützte Sprachen und Regionen

Text Analytics for Health unterstützt nur Dokumente in englischer Sprache. 

Die gehostete Web-API für Text Analytics for Health steht zurzeit nur in diesen Regionen zur Verfügung: USA, Westen 2; USA, Osten 2; USA, Mitte; Europa, Norden; Europa, Westen.

## <a name="request-access-to-the-public-preview"></a>Anfordern des Zugriffs auf die öffentliche Vorschau

Füllen Sie das [Formular zum Anfordern von Cognitive Services](https://aka.ms/csgate) aus, und senden Sie es, um Zugriff auf Text Analytics for Health (öffentliche Vorschau) anzufordern. Für die Verwendung von Text Analytics for Health fallen keine Gebühren an. 

Im Formular müssen Sie Informationen über Sie selbst, Ihr Unternehmen und das Benutzerszenario eintragen, für das Sie den Container verwenden möchten. Das Azure Cognitive Services-Team überprüft das Formular nach der Übermittlung und sendet Ihnen eine E-Mail mit der Entscheidung.

> [!IMPORTANT]
> * Im Formular müssen Sie eine E-Mail-Adresse angeben, die einer Azure-Abonnement-ID zugeordnet ist.
> * Die von Ihnen verwendete Azure-Ressource muss mit der genehmigten Azure-Abonnement-ID erstellt worden sein. 
> * Überprüfen Sie Ihre E-Mail-Adresse (Posteingang und Junk-Ordner) auf Updates zum Status Ihrer Anwendung von Microsoft.

## <a name="using-the-docker-container"></a>Verwenden des Docker-Containers 

Wenn Sie den Text Analytics for Health-Container in Ihrer eigenen Umgebung ausführen möchten, folgen Sie diesen [Anleitungen zum Herunterladen und Installieren des Containers](../how-tos/text-analytics-how-to-install-containers.md?tabs=healthcare).

## <a name="using-the-client-library"></a>Verwenden der Clientbibliothek

Die neueste Vorabversion der Text Analytics-Clientbibliothek ermöglicht es Ihnen, Text Analytics for Health mithilfe eines Clientobjekts aufzurufen. Weitere Informationen finden Sie in der Referenzdokumentation und in den Beispielen auf GitHub:
* [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics)
* [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/)
* [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics)



## <a name="sending-a-rest-api-request"></a>Senden einer REST-API-Anforderung 

### <a name="preparation"></a>Vorbereitung

Text Analytics for Health liefert bessere Ergebnisse, wenn Sie ihr kleinere Textmengen zur Bearbeitung zuführen. Dies steht im Gegensatz zu einigen der anderen Text Analytics-Features, wie z. B. die Schlüsselbegriffserkennung, die bei größeren Textblöcken besser funktioniert. Wenn Sie aus diesen Vorgängen optimale Ergebnisse erzielen möchten, sollten Sie die Eingaben ggf. entsprechend umstrukturieren.

Sie benötigen JSON-Dokumente im folgenden Format: ID, Text und Sprache. 

Ein Dokument darf maximal 5.120 Zeichen enthalten. Die maximal zulässige Anzahl von Dokumenten in einer Sammlung finden Sie im Artikel [Datengrenzwerte und Ratenbegrenzungen für die Textanalyse-API](../concepts/data-limits.md?tabs=version-3) unter „Konzepte“. Die Sammlung wird im Hauptteil der Anforderung übermittelt.

### <a name="structure-the-api-request-for-the-hosted-asynchronous-web-api"></a>Strukturieren der API-Anforderung für die gehostete asynchrone Web-API

Sowohl für den Container als auch die gehostete Web-API müssen Sie eine POST-Anforderung erstellen. Sie können mithilfe von [Postman](text-analytics-how-to-call-api.md), einem cURL-Befehl oder der **API-Testkonsole** in der [Referenz zur gehosteten API für Text Analytics for Health](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/Health) schnell eine POST-Anforderung erstellen und an die gehostete Web-API in Ihrer gewünschten Region senden. 

> [!NOTE]
> Die asynchronen Endpunkte `/analyze` und `/health` sind nur in den folgenden Regionen verfügbar: USA, Westen 2; USA, Osten 2; USA, Mitte; Europa, Norden; Europa, Westen.  Um erfolgreiche Anforderungen an diese Endpunkte zu stellen, stellen Sie sicher, dass Ihre Ressource in einer dieser Regionen erstellt wurde.

Nachstehend finden Sie ein Beispiel für eine JSON-Datei, die an den POST-Text der Text Analytics for Health-API-Anforderung angefügt ist:

```json
example.json

{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "Subject was administered 100mg remdesivir intravenously over a period of 120 min"
    }
  ]
}
```

### <a name="hosted-asynchronous-web-api-response"></a>Antwort der gehosteten asynchronen Web-API 

Da mithilfe dieser POST-Anforderung ein Auftrag für den asynchronen Vorgang übermittelt wird, enthält das Antwortobjekt keinen Text.  Sie benötigen jedoch den Wert des „operation-location“-Schlüssels in den Antwortheadern, um eine GET-Anforderung zum Überprüfen des Auftragsstatus und der Ausgabe zu erstellen.  Nachstehend finden Sie ein Beispiel für den Wert des „operation-location“-Schlüssels im Antwortheader der POST-Anforderung:

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.3/entities/health/jobs/<jobID>`

Wenn Sie den Auftragsstatus überprüfen möchten, senden Sie eine GET-Anforderung an die URL im Wert für den „operation-location“-Schlüsselheader der POST-Antwort.  Mithilfe der folgenden Status können Sie den Status eines Auftrag anzeigen: `NotStarted`, `running`, `succeeded`, `failed`, `rejected`, `cancelling` und `cancelled`.  

Sie können einen Auftrag mit dem Status `NotStarted` oder `running` mit einem HTTP-Aufruf „DELETE“ in derselben URL abbrechen wie die GET-Anforderung.  Weitere Informationen zum DELETE-Aufruf finden Sie in der [Referenz zur gehosteten API für Text Analytics for Health](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/CancelHealthJob).

Das nachstehende Beispiel zeigt die Antwort auf eine GET-Anforderung.  Beachten Sie, dass die Ausgabe zum Abrufen zur Verfügung steht, bis `expirationDateTime` (24 Stunden ab der Uhrzeit, zu der der Auftrag erstellt wurde) nach Bereinigung der Ausgabe verstrichen ist.

```json
{
    "jobId": "b672c6f5-7c0d-4783-ba8c-4d0c47213454",
    "lastUpdateDateTime": "2020-11-18T01:45:00Z",
    "createdDateTime": "2020-11-18T01:44:55Z",
    "expirationDateTime": "2020-11-19T01:44:55Z",
    "status": "succeeded",
    "errors": [],
    "results": {
        "documents": [
            {
                "id": "1",
                "entities": [
                    {
                        "offset": 25,
                        "length": 5,
                        "text": "100mg",
                        "category": "Dosage",
                        "confidenceScore": 1.0,
                        "isNegated": false
                    },
                    {
                        "offset": 31,
                        "length": 10,
                        "text": "remdesivir",
                        "category": "MedicationName",
                        "confidenceScore": 1.0,
                        "isNegated": false,
                        "links": [
                            {
                                "dataSource": "UMLS",
                                "id": "C4726677"
                            },
                            {
                                "dataSource": "MSH",
                                "id": "C000606551"
                            },
                            {
                                "dataSource": "NCI",
                                "id": "C152185"
                            },
                            {
                                "dataSource": "NCI_FDA",
                                "id": "3QKI37EEHE"
                            }
                        ]
                    },
                    {
                        "offset": 42,
                        "length": 13,
                        "text": "intravenously",
                        "category": "MedicationRoute",
                        "confidenceScore": 1.0,
                        "isNegated": false
                    },
                    {
                        "offset": 56,
                        "length": 4,
                        "text": "over",
                        "category": "Time",
                        "confidenceScore": 0.87,
                        "isNegated": false
                    },
                    {
                        "offset": 73,
                        "length": 7,
                        "text": "120 min",
                        "category": "Time",
                        "confidenceScore": 0.99,
                        "isNegated": false
                    }
                ],
                "relations": [
                    {
                        "relationType": "DosageOfMedication",
                        "bidirectional": false,
                        "source": "#/results/documents/0/entities/0",
                        "target": "#/results/documents/0/entities/1"
                    },
                    {
                        "relationType": "RouteOfMedication",
                        "bidirectional": false,
                        "source": "#/results/documents/0/entities/2",
                        "target": "#/results/documents/0/entities/1"
                    },
                    {
                        "relationType": "TimeOfMedication",
                        "bidirectional": false,
                        "source": "#/results/documents/0/entities/3",
                        "target": "#/results/documents/0/entities/1"
                    },
                    {
                        "relationType": "TimeOfMedication",
                        "bidirectional": false,
                        "source": "#/results/documents/0/entities/4",
                        "target": "#/results/documents/0/entities/1"
                    }
                ],
                "warnings": []
            }
        ],
        "errors": [],
        "modelVersion": "2020-09-03"
    }
}
```


### <a name="structure-the-api-request-for-the-container"></a>Strukturieren der API-Anforderung für den Container

Sie können [mithilfe von Postman](text-analytics-how-to-call-api.md) oder der unten aufgeführten cURL-Beispielanforderung eine Abfrage an den von Ihnen bereitgestellten Container senden und darin die Variable `serverURL` durch den geeigneten Wert ersetzen.  Beachten Sie, dass die Version der API in der URL für den Container anders als diejenige der gehosteten API ist.

```bash
curl -X POST 'http://<serverURL>:5000/text/analytics/v3.2-preview.1/entities/health' --header 'Content-Type: application/json' --header 'accept: application/json' --data-binary @example.json

```

Der folgende JSON-Code ist ein Beispiel für eine JSON-Datei, die an den POST-Text der Text Analytics for Health-API-Anforderung angefügt ist:

```json
example.json

{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "Patient reported itchy sores after swimming in the lake."
    },
    {
      "language": "en",
      "id": "2",
      "text": "Prescribed 50mg benadryl, taken twice daily."
    }
  ]
}
```

### <a name="container-response-body"></a>Container-Antworttext

Der folgende JSON-Code ist ein Beispiel für den Text Analytics for Health-API-Antworttext aus dem synchronen containerisierten Aufruf:

```json
{
    "documents": [
        {
            "id": "1",
            "entities": [
                {
                    "id": "0",
                    "offset": 25,
                    "length": 5,
                    "text": "100mg",
                    "category": "Dosage",
                    "confidenceScore": 1.0,
                    "isNegated": false
                },
                {
                    "id": "1",
                    "offset": 31,
                    "length": 10,
                    "text": "remdesivir",
                    "category": "MedicationName",
                    "confidenceScore": 1.0,
                    "isNegated": false,
                    "links": [
                        {
                            "dataSource": "UMLS",
                            "id": "C4726677"
                        },
                        {
                            "dataSource": "MSH",
                            "id": "C000606551"
                        },
                        {
                            "dataSource": "NCI",
                            "id": "C152185"
                        },
                        {
                            "dataSource": "NCI_FDA",
                            "id": "3QKI37EEHE"
                        }
                    ]
                },
                {
                    "id": "2",
                    "offset": 42,
                    "length": 13,
                    "text": "intravenously",
                    "category": "MedicationRoute",
                    "confidenceScore": 1.0,
                    "isNegated": false
                },
                {
                    "id": "3",
                    "offset": 56,
                    "length": 4,
                    "text": "over",
                    "category": "Time",
                    "confidenceScore": 0.87,
                    "isNegated": false
                },
                {
                    "id": "4",
                    "offset": 73,
                    "length": 7,
                    "text": "120 min",
                    "category": "Time",
                    "confidenceScore": 0.99,
                    "isNegated": false
                }
            ],
            "relations": [
                {
                    "relationType": "DosageOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/0/entities/0",
                    "target": "#/documents/0/entities/1"
                },
                {
                    "relationType": "RouteOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/0/entities/2",
                    "target": "#/documents/0/entities/1"
                },
                {
                    "relationType": "TimeOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/0/entities/3",
                    "target": "#/documents/0/entities/1"
                },
                {
                    "relationType": "TimeOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/0/entities/4",
                    "target": "#/documents/0/entities/1"
                }
            ]
        }
    ],
    "errors": [],
    "modelVersion": "2020-09-03"
}
```

### <a name="negation-detection-output"></a>Ausgabe der Negationserkennung

Bei Verwendung der Negationserkennung kann sich in manchen Fällen ein einzelner Negationsausdruck auf mehrere Begriffe zugleich beziehen. Die Negation einer erkannten Entität wird in der JSON-Ausgabe durch den booleschen Wert des `isNegated`-Flags dargestellt, beispielsweise:

```json
{
  "id": "2",
  "offset": 90,
  "length": 10,
  "text": "chest pain",
  "category": "SymptomOrSign",
  "score": 0.9972,
  "isNegated": true,
  "links": [
    {
      "dataSource": "UMLS",
      "id": "C0008031"
    },
    {
      "dataSource": "CHV",
      "id": "0000023593"
    },
    ...
```

### <a name="relation-extraction-output"></a>Ausgabe der Beziehungsextraktion

Die Ausgabe der Beziehungsextraktion enthält URI-Verweise auf die *Quelle* der Beziehung und deren *Ziel*. Entitäten mit der Beziehungsrolle `ENTITY` werden dem `target`-Feld zugewiesen. Entitäten mit der Beziehungsrolle `ATTRIBUTE` werden dem `source`-Feld zugewiesen. Abkürzungsbeziehungen enthalten bidirektionale `source`- und `target`-Felder, und `bidirectional` wird auf `true` festgelegt. 

```json
"relations": [
                {
                    "relationType": "DosageOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/1/entities/0",
                    "target": "#/documents/1/entities/1"
                },
                {
                    "relationType": "FrequencyOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/1/entities/2",
                    "target": "#/documents/1/entities/1"
                }
            ]
  },
...
]
```

## <a name="see-also"></a>Weitere Informationen

* [Übersicht über die Textanalyse](../overview.md)
* [Kategorien benannter Entitäten](../named-entity-types.md)
* [Neuigkeiten](../whats-new.md)
