---
title: Definieren von Projektionen in einem Wissensspeicher
titleSuffix: Azure Cognitive Search
description: Beispiele für gängige Muster für das Projizieren von angereicherten Dokumenten in den Wissensspeicher für die Verwendung mit Power BI oder Azure Machine Learning.
manager: nitinme
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: 3985564d49ce8a5c62b15f9537364418c0a8f5da
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97509919"
---
# <a name="how-to-shape-and-export-enrichments"></a>Strukturieren und Exportieren von Anreicherungen

Projektionen sind der physische Ausdruck angereicherter Dokumente in einem Wissensspeicher. Für eine effektive Verwendung angereicherter Dokumente ist eine Struktur erforderlich. In diesem Artikel untersuchen Sie sowohl die Struktur als auch die Beziehungen und erfahren, wie Sie Projektionseigenschaften erstellen, und wie Sie Daten über die erstellten Projektionstypen hinweg verknüpfen. 

Zum Erstellen einer Projektion werden die Daten entweder mit einer [Shaper-Qualifikation](cognitive-search-skill-shaper.md) strukturiert, um ein benutzerdefiniertes Objekt zu erstellen, oder mit der Inline-Strukturierungssyntax innerhalb einer Projektionsdefinition. 

Eine Datenform enthält alle zu projizierenden Daten als Hierarchie von Knoten. In diesem Artikel werden verschiedene Techniken zum Strukturieren von Daten erläutert, sodass sie in physische, für Berichterstellung, Analyse oder Downstreamverarbeitung geeignete Strukturen projiziert werden können. 

Sie finden die in diesem Artikel vorgestellten Beispiele in diesem [REST-API-Beispiel](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/projections/Projections%20Docs.postman_collection.json), das Sie herunterladen und in einem HTTP-Client ausführen können.

## <a name="introduction-to-projection-examples"></a>Einführung in Projektionsbeispiele

Es gibt drei Typen von [Projektionen](knowledge-store-projection-overview.md):

+ Tabellen
+ Objekte
+ Dateien

Tabellenprojektionen werden in Azure Table Storage gespeichert. Objekt- und Dateiprojektionen werden in Blob Storage geschrieben, während Objektprojektionen als JSON-Dateien gespeichert werden und Inhalte aus dem Ursprungsdokument sowie Ausgaben oder Anreicherungen von Qualifikationen enthalten können. Die Anreicherungspipeline kann auch Binärdateien wie Bilder extrahieren. Diese Binärdateien werden als Dateiprojektionen projiziert. Wenn ein binäres Objekt als Objektprojektion projiziert wird, werden nur die zugeordneten Metadaten als JSON-Blob gespeichert. 

Um die Überschneidungen zwischen Datenformen und Projektionen zu verstehen, verwenden wir das folgende Skillset als Grundlage für die Untersuchung verschiedener Konfigurationen. Dieses Skillset verarbeitet unformatierte Bilder und Textinhalte. Projektionen werden aus dem Inhalt des Dokuments und den Ausgaben der Qualifikationen für die gewünschten Szenarien definiert.

> [!IMPORTANT] 
> Beim Experimentieren mit Projektionen empfiehlt es sich, die [Eigenschaft für den Indexercache festzulegen](search-howto-incremental-index.md), um die Kosten unter Kontrolle zu halten. Das Bearbeiten von Projektionen führt dazu, dass das gesamte Dokument erneut angereichert wird, wenn der Indexercache nicht festgelegt ist. Wenn der Cache festgelegt ist und nur die Projektionen aktualisiert wurden, führen Skillsetausführungen für zuvor angereicherte Dokumente nicht zu neuen Cognitive Services-Gebühren.

```json
{
    "name": "azureblob-skillset",
    "description": "Skillset created from the portal. skillsetName: azureblob-skillset; contentField: merged_content; enrichmentGranularity: document; knowledgeStoreStorageAccount: confdemo;",
    "skills": [
        {
            "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
            "name": "#1",
            "description": null,
            "context": "/document/merged_content",
            "categories": [
                "Person",
                "Quantity",
                "Organization",
                "URL",
                "Email",
                "Location",
                "DateTime"
            ],
            "defaultLanguageCode": "en",
            "minimumPrecision": null,
            "includeTypelessEntities": null,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/merged_content"
                },
                {
                    "name": "languageCode",
                    "source": "/document/language"
                }
            ],
            "outputs": [
                {
                    "name": "persons",
                    "targetName": "people"
                },
                {
                    "name": "organizations",
                    "targetName": "organizations"
                },
                {
                    "name": "locations",
                    "targetName": "locations"
                },
                {
                    "name": "entities",
                    "targetName": "entities"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
            "name": "#2",
            "description": null,
            "context": "/document/merged_content",
            "defaultLanguageCode": "en",
            "maxKeyPhraseCount": null,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/merged_content"
                },
                {
                    "name": "languageCode",
                    "source": "/document/language"
                }
            ],
            "outputs": [
                {
                    "name": "keyPhrases",
                    "targetName": "keyphrases"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
            "name": "#3",
            "description": null,
            "context": "/document",
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/merged_content"
                }
            ],
            "outputs": [
                {
                    "name": "languageCode",
                    "targetName": "language"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.MergeSkill",
            "name": "#4",
            "description": null,
            "context": "/document",
            "insertPreTag": " ",
            "insertPostTag": " ",
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/content"
                },
                {
                    "name": "itemsToInsert",
                    "source": "/document/normalized_images/*/text"
                },
                {
                    "name": "offsets",
                    "source": "/document/normalized_images/*/contentOffset"
                }
            ],
            "outputs": [
                {
                    "name": "mergedText",
                    "targetName": "merged_content"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Vision.OcrSkill",
            "name": "#5",
            "description": null,
            "context": "/document/normalized_images/*",
            "textExtractionAlgorithm": "printed",
            "lineEnding": "Space",
            "defaultLanguageCode": "en",
            "detectOrientation": true,
            "inputs": [
                {
                    "name": "image",
                    "source": "/document/normalized_images/*"
                }
            ],
            "outputs": [
                {
                    "name": "text",
                    "targetName": "text"
                },
                {
                    "name": "layoutText",
                    "targetName": "layoutText"
                }
            ]
        }
    ],
    "cognitiveServices": {
        "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey",
        "description": "DemosCS",
        "key": "<COGNITIVE SERVICES KEY>"
    },
    "knowledgeStore": null
}
```

Bei Verwendung dieses Skillsets mit dem NULL-`knowledgeStore` als Grundlage füllt das erste Beispiel das `knowledgeStore`-Objekt auf, das mit Projektionen für das Erstellen von tabellarischen Datenstrukturen konfiguriert ist, die Sie in anderen Szenarien verwenden können. 

## <a name="projecting-to-tables"></a>Projizieren in Tabellen

Die Projektion in Tabellen in Azure Storage eignet sich für die Berichterstellung und Analyse mithilfe von Tools wie Power BI. Power BI kann aus Tabellen lesen und Beziehungen basierend auf den Schlüsseln ermitteln, die während der Projektion generiert werden. Wenn Sie versuchen, ein Dashboard zu erstellen, wird diese Aufgabe durch zusammenhängende Daten vereinfacht. 

Wir erstellen ein Dashboard, um die Schlüsselausdrücke zu visualisieren, die aus Dokumenten als Wortwolke extrahiert werden. Um die richtige Datenstruktur zu erstellen, fügen wird dem Skillset eine Shaper-Qualifikation hinzu, um eine benutzerdefinierte Form zu erstellen, die dokumentspezifische Details und Schlüsselausdrücke enthält. Die benutzerdefinierte Form wird im Stammknoten von `document` als `pbiShape` bezeichnet.

> [!NOTE] 
> Tabellenprojektionen sind Azure Storage-Tabellen, die durch die durch Azure Storage vorgegebenen Speicherlimits gesteuert werden. Weitere Informationen finden Sie unter [Grenzwerte von Table Storage](/rest/api/storageservices/understanding-the-table-service-data-model). Sie sollten auch wissen, dass die Entitätsgröße 1 MB nicht überschreiten und eine einzelne Eigenschaft nicht größer als 64 KB sein darf. Aufgrund dieser Einschränkungen stellen Tabellen eine gute Lösung für die Speicherung einer großen Anzahl kleiner Entitäten dar.

### <a name="using-a-shaper-skill-to-create-a-custom-shape"></a>Verwenden einer Shaper-Qualifikation zum Erstellen einer benutzerdefinierten Form

Erstellen Sie eine benutzerdefinierte Form, die Sie in Table Storage projizieren können. Ohne eine benutzerdefinierte Form kann eine Projektion nur auf einen einzelnen Knoten verweisen (eine Projektion pro Ausgabe). Durch das Erstellen einer benutzerdefinierten Form werden verschiedene Elemente in einer neuen logischen Einheit aggregiert, die als eine einzelne Tabelle projiziert oder in Segmenten auf eine Sammlung von Tabellen verteilt werden kann. 

In diesem Beispiel kombiniert die benutzerdefinierte Form Metadaten und identifizierte Entitäten und Schlüsselbegriffe. Das Objekt heißt `pbiShape` und ist `/document` untergeordnet. 

> [!IMPORTANT] 
> Ein Zweck der Strukturierung besteht darin, sicherzustellen, dass alle Anreicherungsknoten in wohlgeformten JSON-Objekten ausgedrückt werden. Dies ist für die Projektion in den Wissensspeicher erforderlich. Das gilt insbesondere, wenn eine Anreicherungsstruktur Knoten enthält, die keine wohlgeformten JSON-Objekte sind (beispielsweise wenn eine Anreicherung einem einfachen Typ wie einer Zeichenfolge untergeordnet ist).
>
> Beachten Sie die letzten beiden Knoten `KeyPhrases` und `Entities`. Diese werden von einem gültigen JSON-Objekt mit dem `sourceContext` umschlossen. Dies ist erforderlich, da `keyphrases` und `entities` Anreicherungen von einfachen Typen sind und in gültige JSON-Objekte konvertiert werden müssen, bevor sie projiziert werden können.
>


```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "name": "ShaperForTables",
    "description": null,
    "context": "/document",
    "inputs": [
        {
            "name": "metadata_storage_content_type",
            "source": "/document/metadata_storage_content_type",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "metadata_storage_name",
            "source": "/document/metadata_storage_name",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "metadata_storage_path",
            "source": "/document/metadata_storage_path",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "metadata_content_type",
            "source": "/document/metadata_content_type",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "keyPhrases",
            "source": null,
            "sourceContext": "/document/merged_content/keyphrases/*",
            "inputs": [
                {
                    "name": "KeyPhrases",
                    "source": "/document/merged_content/keyphrases/*"
                }

            ]
        },
        {
            "name": "Entities",
            "source": null,
            "sourceContext": "/document/merged_content/entities/*",
            "inputs": [
                {
                    "name": "Entities",
                    "source": "/document/merged_content/entities/*/name"
                }

            ]
        }
    ],
    "outputs": [
        {
            "name": "output",
            "targetName": "pbiShape"
        }
    ]
}
```

Fügen Sie dem Skillset die oben genannte Shaper-Qualifikation hinzu. 

```json
    "name": "azureblob-skillset",
    "description": "A friendly description of the skillset goes here.",
    "skills": [
        {
            Shaper skill goes here
            }
        ],
    "cognitiveServices":  "A key goes here",
    "knowledgeStore": []
}  
```

Nachdem Sie nun über alle Daten verfügen, die für das Projizieren in Tabellen erforderlich sind, aktualisieren Sie das knowledgeStore-Objekt mit den Tabellendefinitionen. In diesem Beispiel sind drei Tabellen enthalten, die durch Festlegen der Eigenschaften `tableName`, `source` und `generatedKeyName` definiert werden.

```json
"knowledgeStore" : {
    "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
    "projections": [
        {
            "tables": [
                {
                    "tableName": "pbiDocument",
                    "generatedKeyName": "Documentid",
                    "source": "/document/pbiShape"
                },
                {
                    "tableName": "pbiKeyPhrases",
                    "generatedKeyName": "KeyPhraseid",
                    "source": "/document/pbiShape/keyPhrases/*"
                },
                {
                    "tableName": "pbiEntities",
                    "generatedKeyName": "Entityid",
                    "source": "/document/pbiShape/Entities/*"
                }
            ],
            "objects": [],
            "files": []
        }
    ]
}
```

Sie können Ihre Arbeit anhand der folgenden Schritte verarbeiten:

1. Legen Sie die ```storageConnectionString```-Eigenschaft auf eine gültige Verbindungszeichenfolge für ein Speicherkonto vom Typ „Universell v2“ fest.  

1. Aktualisieren Sie das Skillset durch Ausgabe der PUT-Anforderung.

1. Führen Sie nach dem Aktualisieren des Skillsets den Indexer aus. 

Sie verfügen nun über eine funktionierende Projektion mit drei Tabellen. Wenn diese Tabellen in Power BI importiert werden, sollte Power BI die Beziehungen automatisch ermitteln.

Bevor Sie mit dem nächsten Beispiel fortfahren, sollten Sie die Aspekte der Tabellenprojektion analysieren, um die Verfahren zum Aufteilen und Abgleichen von Daten zu verstehen.

### <a name="slicing"></a>Aufteilen 

Beim Aufteilen handelt es sich um ein Verfahren, mit dem eine vollständige, konsolidierte Form in ihre Bestandteile unterteilt wird. Das Ergebnis besteht aus separaten, aber verknüpften Tabellen, die Sie einzeln verarbeiten können.

Im Beispiel ist `pbiShape` die konsolidierte Form (oder der Anreicherungsknoten). In der Projektionsdefinition wird `pbiShape` in zusätzliche Tabellen aufgeteilt, sodass Sie die Teile ```keyPhrases``` und ```Entities``` der Form abrufen können. Dies ist in Power BI nützlich, wenn jedem Dokument mehrere Entitäten und Schlüsselausdrücke zugeordnet sind. Sie können zusätzliche Erkenntnisse gewinnen, wenn die Entitäten und Schlüsselausdrücke als kategorisierte Daten vorliegen.

Durch das Aufteilen wird implizit eine Beziehung zwischen der übergeordneten und der untergeordneten Tabelle hergestellt. Dabei wird der ```generatedKeyName``` in der übergeordneten Tabelle verwendet, um in der untergeordneten Tabelle eine Spalte mit demselben Namen zu erstellen. 

### <a name="naming-relationships"></a>Namensbeziehungen

Mithilfe der Eigenschaften ```generatedKeyName``` und ```referenceKeyName``` werden Daten über Tabellen hinweg oder sogar über Projektionstypen hinweg miteinander verknüpft. Jede Zeile in der untergeordneten Tabelle/Projektion verfügt über eine Eigenschaft, die zurück auf das übergeordnete Element verweist. Der Name der Spalte oder Eigenschaft im untergeordneten Element ist der ```referenceKeyName``` des übergeordneten Elements. Wenn kein ```referenceKeyName``` bereitgestellt wird, verwendet der Dienst standardmäßig den ```generatedKeyName``` des übergeordneten Elements. 

Power BI benötigt diese generierten Schlüssel, um Beziehungen innerhalb der Tabellen zu ermitteln. Wenn Sie die Spalte in der untergeordneten Tabelle anders benennen möchten, legen Sie die ```referenceKeyName```-Eigenschaft der übergeordneten Tabelle fest. Ein Beispiel wäre das Festlegen von ```generatedKeyName``` auf die ID in der pbiDocument-Tabelle und von ```referenceKeyName``` auf die DocumentID. Dies würde dazu führen, dass die Spalte in den Tabellen pbiEntities und pbiKeyPhrases die Dokument-ID mit dem Namen DocumentID enthält.

## <a name="projecting-to-objects"></a>Projizieren in Objekte

Objektprojektionen unterliegen nicht denselben Einschränkungen wie Tabellenprojektionen und eignen sich besser für das Projizieren großer Dokumente. In diesem Beispiel wird das gesamte Dokument als Objektprojektion gesendet. Objektprojektionen sind auf eine einzelne Projektion in einem Container beschränkt und können nicht aufgeteilt werden.

Zum Definieren einer Objektprojektion verwenden Sie das ```objects```-Array in den Projektionen. Sie können mithilfe der Shaper-Qualifikation eine neue Form generieren oder die Inline-Strukturierung der Objektprojektion verwenden. Während das Tabellenbeispiel den Ansatz der Erstellung einer Form und der anschließenden Aufteilung zeigt, veranschaulicht dieses Beispiel die Verwendung der Inline-Strukturierung. 

Die Inline-Strukturierung ist die Möglichkeit, eine neue Form in der Definition der Eingaben für eine Projektion zu erstellen. Bei der Inline-Strukturierung wird ein anonymes Objekt erstellt, das mit dem Ergebnis einer Shaper-Ausführung identisch ist (in diesem Fall `pbiShape`). Die Inline-Strukturierung ist nützlich, wenn Sie eine Form definieren, die nicht wiederverwendet werden soll.

Die Projektionseigenschaft ist ein Array. In diesem Beispiel wird dem Array eine neue Projektionsinstanz hinzugefügt, in der die knowledgeStore-Definition Inline-Projektionen enthält. Wenn Sie Inline-Projektionen verwenden, können Sie die Shaper-Qualifikation auslassen.

```json
"knowledgeStore" : {
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
        "projections": [
             {
                "tables": [ ],
                "objects": [
                    {
                        "storageContainer": "sampleobject",
                        "source": null,
                        "generatedKeyName": "myobject",
                        "sourceContext": "/document",
                        "inputs": [
                            {
                                "name": "metadata_storage_name",
                                "source": "/document/metadata_storage_name"
                            },
                            {
                                "name": "metadata_storage_path",
                                "source": "/document/metadata_storage_path"
                            },
                            {
                                "name": "content",
                                "source": "/document/content"
                            },
                            {
                                "name": "keyPhrases",
                                "source": "/document/merged_content/keyphrases/*"
                            },
                            {
                                "name": "entities",
                                "source": "/document/merged_content/entities/*/name"
                            },
                            {
                                "name": "ocrText",
                                "source": "/document/normalized_images/*/text"
                            },
                            {
                                "name": "ocrLayoutText",
                                "source": "/document/normalized_images/*/layoutText"
                            }
                        ]

                    }
                ],
                "files": []
            }
        ]
    }
```

## <a name="projecting-to-file"></a>Projizieren in Dateien

Dateiprojektionen sind Bilder, die entweder aus dem Quelldokument extrahiert wurden, oder Ausgaben von Anreicherungen darstellen, die aus dem Anreicherungsprozess projiziert werden können. Dateiprojektionen werden ähnlich wie Objektprojektionen als Blobs in Azure Storage implementiert und enthalten das Bild. 

Zum Generieren einer Dateiprojektion verwenden Sie das `files`-Array im Projektionsobjekt. In diesem Beispiel werden alle aus dem Dokument extrahierten Bilder in den Container `samplefile` projiziert.

```json
"knowledgeStore" : {
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
        "projections": [
            {
                "tables": [ ],
                "objects": [ ],
                "files": [
                    {
                        "storageContainer": "samplefile",
                        "source": "/document/normalized_images/*"
                    }
                ]
            }
        ]
    }
```

## <a name="projecting-to-multiple-types"></a>Projizieren auf mehrere Typen

Ein komplexeres Szenario erfordert möglicherweise das Projizieren der Inhalte auf verschiedene Projektionstypen. Wenn Sie z. B. einige Daten wie Schlüsselausdrücke und Entitäten in Tabellen projizieren möchten, speichern Sie die OCR-Ergebnisse von Text und Layouttext als Objekte, und projizieren Sie dann die Bilder als Dateien. 

In diesem Beispiel wird das Skillset mit folgenden Änderungen aktualisiert:

1. Erstellen einer Tabelle mit einer Zeile für jedes Dokument
1. Erstellen einer Tabelle, die mit der Dokumenttabelle verknüpft ist, wobei jeder Schlüsselausdruck als Zeile in dieser Tabelle bezeichnet wird
1. Erstellen einer Tabelle, die mit der Dokumenttabelle verknüpft ist, wobei jede Entität als Zeile in dieser Tabelle bezeichnet wird
1. Erstellen einer Objektprojektion mit dem Layouttext für jedes Bild
1. Erstellen einer Dateiprojektion, die jedes extrahierte Bild projiziert
1. Erstellen einer Querverweistabelle mit Verweisen auf die Dokumenttabelle, die Objektprojektion mit dem Layouttext und die Dateiprojektion

Diese Änderungen spiegeln sich in der knowledgeStore-Definition weiter unten wider. 

### <a name="shape-data-for-cross-projection"></a>Strukturieren von Daten für eine Kreuzprojektion

Um die für diese Projektionen benötigten Formen zu erhalten, fügen Sie zunächst eine neue Shaper-Qualifikation hinzu, die ein strukturiertes Objekt namens `crossProjection` erstellt. 

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "name": "ShaperForCross",
    "description": null,
    "context": "/document",
    "inputs": [
        {
            "name": "metadata_storage_name",
            "source": "/document/metadata_storage_name",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "keyPhrases",
            "source": null,
            "sourceContext": "/document/merged_content/keyphrases/*",
            "inputs": [
                {
                    "name": "KeyPhrases",
                    "source": "/document/merged_content/keyphrases/*"
                }

            ]
        },
        {
            "name": "entities",
            "source": null,
            "sourceContext": "/document/merged_content/entities/*",
            "inputs": [
                {
                    "name": "Entities",
                    "source": "/document/merged_content/entities/*/name"
                }

            ]
        },
        {
            "name": "images",
            "source": null,
            "sourceContext": "/document/normalized_images/*",
            "inputs": [
                {
                    "name": "image",
                    "source": "/document/normalized_images/*"
                },
                {
                    "name": "layoutText",
                    "source": "/document/normalized_images/*/layoutText"
                },
                {
                    "name": "ocrText",
                    "source": "/document/normalized_images/*/text"
                }
                ]
        }
 
    ],
    "outputs": [
        {
            "name": "output",
            "targetName": "crossProjection"
        }
    ]
}
```

### <a name="define-table-object-and-file-projections"></a>Definieren von Tabellen-, Objekt- und Dateiprojektionen

Teilen Sie das Objekt aus dem konsolidierten crossProjection-Objekt in mehrere Tabellen auf, erfassen Sie die OCR-Ausgabe als Blobs und speichern Sie das Bild dann als Dateien (auch in Blobspeicher).

```json
"knowledgeStore" : {
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
        "projections": [
             {
                "tables": [
                    {
                        "tableName": "crossDocument",
                        "generatedKeyName": "Id",
                        "source": "/document/crossProjection"
                    },
                    {
                        "tableName": "crossEntities",
                        "generatedKeyName": "EntityId",
                        "source": "/document/crossProjection/entities/*"
                    },
                    {
                        "tableName": "crossKeyPhrases",
                        "generatedKeyName": "KeyPhraseId",
                        "source": "/document/crossProjection/keyPhrases/*"
                    },
                    {
                        "tableName": "crossReference",
                        "generatedKeyName": "CrossId",
                        "source": "/document/crossProjection/images/*"
                    }
                     
                ],
                "objects": [
                    {
                        "storageContainer": "crossobject",
                        "generatedKeyName": "crosslayout",
                        "source": null,
                        "sourceContext": "/document/crossProjection/images/*/layoutText",
                        "inputs": [
                            {
                                "name": "OcrLayoutText",
                                "source": "/document/crossProjection/images/*/layoutText"
                            }
                        ]
                    }
                ],
                "files": [
                    {
                        "storageContainer": "crossimages",
                        "generatedKeyName": "crossimages",
                        "source": "/document/crossProjection/images/*/image"
                    }
                ]
            }
        ]
    }
```

Objektprojektionen erfordern einen Containernamen für jede Projektion. Objektprojektionen oder Dateiprojektionen dürfen nicht im selben Container verwendet werden. 

### <a name="relationships-among-table-object-and-file-projections"></a>Beziehungen zwischen Tabellen-, Objekt- und Dateiprojektionen

In diesem Beispiel wird ein weiteres Feature von Projektionen veranschaulicht. Durch die Definition mehrerer Projektionstypen innerhalb desselben Projektionsobjekts wird eine Beziehung innerhalb der Typen (Tabellen, Objekte, Dateien) und die verschiedenen Typen übergreifend ausgedrückt. So können Sie mit einer Tabellenzeile für ein Dokument beginnen und den gesamten OCR-Text für die Bilder in diesem Dokument in der Objektprojektion suchen. 

Wenn Sie nicht möchten, dass die Daten in Beziehung gesetzt werden, definieren Sie die Projektionen in verschiedenen Projektionsobjekten. Der folgende Codeausschnitt führt z. B. dazu, dass die Tabellen verknüpft werden, jedoch ohne Beziehungen zwischen den Tabellen- und den Objektprojektionen (OCR-Text) herzustellen. 

Projektionsgruppen sind nützlich, wenn Sie dieselben Daten in unterschiedlichen Formen für verschiedene Anforderungen projizieren möchten. Beispiel: eine Projektionsgruppe für das Power BI-Dashboard und eine weitere Projektionsgruppe für die Erfassung von Daten zum Trainieren eines Machine Learning-Modells in einer benutzerdefinierten Qualifikation.

Wenn Sie Projektionen für unterschiedliche Typen erstellen, werden zunächst die Datei- und Objektprojektionen generiert und die Pfade den Tabellen hinzugefügt.

```json
"knowledgeStore" : {
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
        "projections": [
            {
                "tables": [
                    {
                        "tableName": "unrelatedDocument",
                        "generatedKeyName": "Documentid",
                        "source": "/document/pbiShape"
                    },
                    {
                        "tableName": "unrelatedKeyPhrases",
                        "generatedKeyName": "KeyPhraseid",
                        "source": "/document/pbiShape/keyPhrases"
                    }
                ],
                "objects": [
                    
                ],
                "files": []
            }, 
            {
                "tables": [],
                "objects": [
                    {
                        "storageContainer": "unrelatedocrtext",
                        "source": null,
                        "sourceContext": "/document/normalized_images/*/text",
                        "inputs": [
                            {
                                "name": "ocrText",
                                "source": "/document/normalized_images/*/text"
                            }
                        ]
                    },
                    {
                        "storageContainer": "unrelatedocrlayout",
                        "source": null,
                        "sourceContext": "/document/normalized_images/*/layoutText",
                        "inputs": [
                            {
                                "name": "ocrLayoutText",
                                "source": "/document/normalized_images/*/layoutText"
                            }
                        ]
                    }
                ],
                "files": []
            }
        ]
    }
```

## <a name="common-issues"></a>Häufige Probleme

Beim Definieren einer Projektion treten einige Probleme häufiger auf, die zu unerwarteten Ergebnissen führen können. Überprüfen Sie diese Probleme, wenn die Ausgabe im Wissensspeicher nicht Ihren Erwartungen entspricht.

+ Zeichenfolgenanreicherungen werden nicht in gültige JSON-Objekte strukturiert. Wenn Zeichenfolgen angereichert werden (z. B. `merged_content` mit Schlüsselausdrücken), wird die angereicherte Eigenschaft als untergeordnetes Element von `merged_content` innerhalb der Anreicherungsstruktur dargestellt. Die Standarddarstellung ist kein wohlgeformtes JSON-Objekt. Sie müssen also zur Projektionszeit sicherstellen, dass sie in ein gültiges JSON-Objekt mit einem Namen und einem Wert transformiert wird.

+ Auslassen von ```/*``` am Ende eines Quellpfads. Wenn die Quelle einer Projektion z. B. `/document/pbiShape/keyPhrases` ist, wird das Array mit den Schlüsselausdrücken als einzelnes Objekt/einzelne Zeile projiziert. Legen Sie stattdessen den Quellpfad auf `/document/pbiShape/keyPhrases/*` fest, um für jeden Schlüsselausdruck eine einzelne Zeile oder ein einzelnes Objekt zu erzeugen.

+ Pfadsyntaxfehler. Bei Pfadselektoren wird die Groß-/Kleinschreibung berücksichtigt. Dies kann zu Warnungen über fehlende Eingaben führen, wenn Sie nicht die richtige Schreibung für den Selektor verwenden.

## <a name="next-steps"></a>Nächste Schritte

Die Beispiele in diesem Artikel veranschaulichen allgemeine Muster zum Erstellen von Projektionen. Wenn Sie sich ausführlich mit den Konzepten vertraut gemacht haben, verfügen Sie über bessere Voraussetzungen für das Erstellen von Projektionen für Ihr spezielles Szenario.

Wenn Sie neue Features erkunden, sollten Sie die inkrementelle Anreicherung als nächsten Schritt in Betracht ziehen. Die inkrementelle Anreicherung basiert auf dem Caching, mit dem Sie alle Anreicherungen wiederverwenden können, die nicht anderweitig von einer Skillsetänderung betroffen sind. Dies ist besonders nützlich für Pipelines, die OCR- und Bildanalysen enthalten.

> [!div class="nextstepaction"]
> [Einführung in die inkrementelle Anreicherung und das Zwischenspeichern](cognitive-search-incremental-indexing-conceptual.md)

Hier finden Sie eine Übersicht über Projektionen, erfahren mehr über Funktionen wie Gruppen und Aufteilung, und darüber, wie Sie [diese in einem Fähigkeiten definieren](knowledge-store-projection-overview.md).

> [!div class="nextstepaction"]
> [Projektionen in einem Wissensspeicher](knowledge-store-projection-overview.md)