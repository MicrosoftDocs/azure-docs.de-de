---
title: Qualifikationsgruppenkonzepte und Workflow
titleSuffix: Azure Cognitive Search
description: In Qualifikationsgruppen erstellen Sie eine KI-Anreicherungspipeline in der kognitiven Azure-Suche. Informieren Sie sich über wichtige Konzepte und Details zur Zusammenstellung von Qualifikationsgruppen.
manager: nitinme
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/15/2020
ms.openlocfilehash: b5a893ee1923ba4b2bec53b20fb164337bd65902
ms.sourcegitcommit: 65a4f2a297639811426a4f27c918ac8b10750d81
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96558112"
---
# <a name="skillset-concepts-in-azure-cognitive-search"></a>Skillsetkonzepte in Azure Cognitive Search

Dieser Artikel richtet sich an Entwickler, die ein besseres Verständnis der Konzepte und des Aufbaus von Skillsets benötigen, und setzt Vertrautheit mit dem Prozess der KI-Anreicherung voraus. Wenn dieses Konzept für Sie neu ist, beginnen Sie mit [AI-Anreicherung in Azure Cognitive Search](cognitive-search-concept-intro.md).

## <a name="introducing-skillsets"></a>Einführung in Skillsets

Ein Skillset ist eine wiederverwendbare Ressource in Azure Cognitive Search, die eine Sammlung von Skills (Qualifikationen) festlegt, die zum Analysieren, Transformieren und Anreichern von Text- oder Bildinhalten während der Indizierung verwendet werden. Skills haben Ein- und Ausgaben, und oft wird die Ausgabe eines Skills zur Eingabe eines anderen Skills in einer Kette oder Sequenz von Prozessen.

Ein Skillset verfügt über drei Haupteigenschaften:

+ `skills`, eine ungeordnete Sammlung von Skills, für die die Plattform die Ausführungssequenz basierend auf den für jeden Skill erforderlichen Eingaben bestimmt.
+ `cognitiveServices`, der Schlüssel einer Cognitive Services-Ressource, die die Bild- und Textverarbeitung für Skillsets mit integrierten Skills durchführt.
+ `knowledgeStore`, (optional) ein Azure Storage-Konto, in das Ihre angereicherten Dokumente projiziert werden. Angereicherte Dokumente werden auch von Suchindizes verarbeitet.

Qualifikationsgruppen werden in JSON erstellt. Das folgende Beispiel ist eine leicht vereinfachte Version dieses [Skillsets „hotel-reviews“](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotelreviews/HotelReviews_skillset.json), das zur Veranschaulichung der Konzepte in diesem Artikel verwendet wird. 

Die ersten beiden Skills sind unten dargestellt:

+ Der erste Skill ist ein [Skill zur Textaufteilung](cognitive-search-skill-textsplit.md), der den Inhalt des Felds „reviews_text“ als Eingabe akzeptiert und diesen Inhalt als Ausgabe in „Seiten“ mit 5.000 Zeichen aufteilt.
+ Der zweite Skill ist ein [Skill zur Stimmungserkennung](cognitive-search-skill-sentiment.md), der „Seiten“ als Eingabe akzeptiert und ein neues Feld namens „Sentiment“ (Stimmung) als Ausgabe erstellt, das die Ergebnisse der Stimmungsanalyse enthält.


```json
{
    "skills": [
        {
            "@odata.type": "#Microsoft.Skills.Text.SplitSkill",
            "name": "#1",
            "description": null,
            "context": "/document/reviews_text",
            "defaultLanguageCode": "en",
            "textSplitMode": "pages",
            "maximumPageLength": 5000,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/reviews_text"
                }
            ],
            "outputs": [
                {
                    "name": "textItems",
                    "targetName": "pages"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
            "name": "#2",
            "description": null,
            "context": "/document/reviews_text/pages/*",
            "defaultLanguageCode": "en",
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/reviews_text/pages/*",
                }
            ],
            "outputs": [
                {
                    "name": "score",
                    "targetName": "Sentiment"
                }
            ]
        },
  "cognitiveServices": null,
  "knowledgeStore": {  }
}
```
> [!NOTE]
> Sie können komplexe Skillsets mit Schleifen und Branches erstellen, indem Sie den [Skill „Bedingt“](cognitive-search-skill-conditional.md) zur Erstellung der Ausdrücke verwenden. Die Syntax basiert auf der [JSON-Zeiger](https://tools.ietf.org/html/rfc6901)-Pfadnotation, mit einigen Änderungen zum Identifizieren von Knoten in der Anreicherungsstruktur. Ein `"/"` durchläuft eine niedrigere Ebene in der Struktur und `"*"` fungiert als „for-each“-Operator im Kontext. Zahlreiche Beispiele in diesem Artikel veranschaulichen die Syntax. 

### <a name="enrichment-tree"></a>Anreicherungsstruktur

In der Abfolge von [Schritten in einer Anreicherungspipeline](cognitive-search-concept-intro.md#enrichment-steps) folgt die Inhaltsverarbeitung auf die Phase *Dokumententschlüsselung*, in der Text und Bilder aus der Quelle extrahiert werden. Bildinhalte können dann an Skills weitergeleitet werden, die die Bildverarbeitung spezifizieren, während Textinhalte in die Warteschlange für die Textverarbeitung gestellt werden. Für Quelldokumente, die große Textmengen enthalten, können Sie einen *Analysemodus* auf dem Indexer festlegen, um den Text zur optimaleren Verarbeitung in kleinere Blöcke zu segmentieren. 

![Wissensspeicher im Pipelinediagramm](./media/knowledge-store-concept-intro/knowledge-store-concept-intro.svg "Wissensspeicher im Pipelinediagramm")

Sobald sich ein Dokument in der Anreicherungspipeline befindet, wird es als Inhaltsstruktur mit zugeordneten Anreicherungen dargestellt. Diese Struktur wird als Ausgabe der Dokumententschlüsselung instanziiert.  Das Anreicherungsstruktur-Format ermöglicht der Anreicherungspipeline das Anfügen von Metadaten auch an primitive Datentypen. Es handelt sich nicht um ein gültiges JSON-Objekt, kann jedoch in ein gültiges JSON-Format projiziert werden. In der folgenden Tabelle wird der Zustand eines Dokuments gezeigt, das in die Anreicherungspipeline wechselt:

|Datenquelle\Analysemodus|Standard|JSON, JSON-Zeilen & CSV|
|---|---|---|
|Blob Storage|/document/content<br>/document/normalized_images/*<br>…|/document/{key1}<br>/document/{key2}<br>…|
|SQL|/document/{column1}<br>/document/{column2}<br>…|– |
|Cosmos DB|/document/{key1}<br>/document/{key2}<br>…|–|

 Beim Ausführen von Qualifikationen werden neue Knoten zur Anreicherungsstruktur hinzugefügt. Diese neuen Knoten können dann als Eingaben für Downstreamqualifikationen verwendet werden, und somit in den Wissensspeicher projiziert oder Indexfeldern zugeordnet werden. Anreicherungen sind nicht änderbar: Die Knoten können nach der Erstellung nicht bearbeitet werden. Mit steigender Komplexität Ihrer Qualifikationen wird auch Ihre Anreicherungsstruktur komplexer, aber nicht alle Knoten in der Anreicherungsstruktur müssen in den Index oder Wissensspeicher aufgenommen werden. 

Sie können selektiv nur eine Teilmenge der Anreicherungen im Index oder Wissensspeicher beibehalten.

### <a name="context"></a>Kontext

Jede Qualifikation erfordert einen Kontext. Ein Kontext bestimmt Folgendes:

+ Wie oft die Qualifikation ausgeführt wird, basierend auf den ausgewählten Knoten. Wird bei Kontextwerten einer Typsammlung ein `/*` an das Ende hinzugefügt, führt dies dazu, dass die Qualifikation für jede Instanz in der Sammlung einmal aufgerufen wird. 

+ Wo in der Anreicherungsstruktur die Qualifikationsausgaben hinzugefügt werden. Ausgaben werden der Struktur immer als untergeordnete Elemente des Kontextknotens hinzugefügt. 

+ Form der Eingaben. Bei Sammlungen mit mehreren Ebenen hat das Festlegen des Kontexts auf die übergeordnete Sammlung Auswirkungen auf die Form der Eingabe für den Skill. Sie verfügen beispielsweise über eine Anreicherungsstruktur mit einer Liste von Ländern oder Regionen, von denen jedes bzw. jede mit einer Liste von Bundesländern/Kantonen angereichert ist, die wiederum eine Liste von Postleitzahlen enthalten.

|Kontext|Eingabe|Form der Eingabe|Aufruf einer Qualifikation|
|-------|-----|--------------|----------------|
|`/document/countries/*` |`/document/countries/*/states/*/zipcodes/*` |Eine Liste aller Postleitzahlen im Land oder der Region |Einmal pro Land oder Region |
|`/document/countries/*/states/*` |`/document/countries/ */states/* /zipcodes/*`` |Eine Liste aller Postleitzahlen im Bundesland | Einmal pro Kombination aus Land oder Region und Bundesland/Kanton|

## <a name="generate-enriched-data"></a>Generieren von angereicherten Daten 

Anhand des [Skillsets „hotel-reviews“](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotelreviews/HotelReviews_skillset.json) als Referenzpunkt werden wir uns mit Folgendem befassen:

+ Die Entwicklung der Anreicherungsstruktur mit Ausführung der einzelnen Skills
+ Die Funktionsweise des Kontexts und der Eingaben, um zu bestimmen, wie oft ein Skill ausgeführt wird
+ Die Form der Eingabe in Abhängigkeit vom Kontext

Ein „Dokument“ innerhalb des Anreicherungsprozesses stellt eine einzelne Zeile (eine Hotelbewertung) innerhalb der Quelldatei „hotel_reviews.csv“ dar.

### <a name="skill-1-split-skill"></a>Qualifikation 1: Qualifikation „Aufteilung“

Wenn der Quellinhalt aus großen Textblöcken besteht, ist es hilfreich, ihn in kleinere Komponenten zu zerlegen, um eine größere Genauigkeit bei der Erkennung von Sprache, Stimmung und Schlüsselbegriffen zu erreichen. Es stehen zwei Einheiten zur Verfügung: Seiten und Sätze. Eine Seite besteht aus ungefähr 5.000 Zeichen.

Ein Skill zur Textaufteilung erfolgt normalerweise an erster Stelle in einem Skillset.

```json
      "@odata.type": "#Microsoft.Skills.Text.SplitSkill",
      "name": "#1",
      "description": null,
      "context": "/document/reviews_text",
      "defaultLanguageCode": "en",
      "textSplitMode": "pages",
      "maximumPageLength": 5000,
      "inputs": [
        {
          "name": "text",
          "source": "/document/reviews_text"
        }
      ],
      "outputs": [
        {
          "name": "textItems",
          "targetName": "pages"
        }
```

Mit dem Skillkontext `"/document/reviews_text"` wird der Aufteilungsskill für `reviews_text` genau einmal ausgeführt. Die Qualifikationsausgabe ist eine Liste, in der `reviews_text` in 5000-Zeichen-Segmente aufgeteilt ist. Die Ausgabe des Skills „Aufteilung“ wird `pages` benannt und der Anreicherungsstruktur hinzugefügt. Mit dem Feature `targetName` können Sie eine Qualifikationsausgabe umbenennen, bevor sie der Anreicherungsstruktur hinzugefügt wird.

Die Anreicherungsstruktur verfügt jetzt über einen neuen Knoten, der unter den Kontext der Qualifikation platziert wird. Dieser Knoten ist für alle Qualifikationen, Projektionen oder Ausgabefeldzuordnungen verfügbar. Konzeptionell sieht die Struktur wie folgt aus:

![Anreicherungsstruktur nach der Dokumententschlüsselung](media/cognitive-search-working-with-skillsets/enrichment-tree-doc-cracking.png "Anreicherungsstruktur nach der Dokumententschlüsselung")

Der Stammknoten für alle Anreicherungen ist `"/document"`. Wenn Sie Blob-Indexer verwenden, verfügt der `"/document"`-Knoten über die untergeordneten Knoten `"/document/content"` und `"/document/normalized_images"`. Wenn Sie, wie in diesem Beispiel, CSV-Daten verwenden, werden die Spaltennamen den Knoten unter `"/document"` zugeordnet. 

Um auf eine der Anreicherungen zuzugreifen, die einem Knoten durch eine Qualifikation hinzugefügt wurden, ist der vollständige Pfad für die Anreicherung erforderlich. Wenn Sie z.B. den Text aus dem Knoten ```pages``` als Eingabe für eine andere Qualifikation verwenden möchten, müssen Sie ihn als ```"/document/reviews_text/pages/*"``` angeben.
 
 ![Anreicherungsstruktur nach Qualifikation 1](media/cognitive-search-working-with-skillsets/enrichment-tree-skill1.png "Anreicherungsstruktur nach Ausführung von Qualifikation 1")

### <a name="skill-2-language-detection"></a>Qualifikation 2: Sprachenerkennung

Dokumente zur Hotelbewertung enthalten Kundenfeedback in mehreren Sprachen. Der Skill zur Spracherkennung bestimmt, welche Sprache verwendet wird. Das Ergebnis wird dann an die Extraktion von Schlüsselbegriffen und die Erkennung von Stimmungen weitergeleitet, wobei die Sprache bei der Erkennung von Stimmungen und Begriffen berücksichtigt wird.

Die Qualifikation „Spracherkennung“ ist zwar die dritte in der Qualifikationsgruppe definierte Qualifikation (Qualifikation 3), sie wird aber als nächste Qualifikation ausgeführt. Da keine Eingaben erforderlich sind, wird sie nicht blockiert und parallel mit der vorherigen Qualifikation ausgeführt. Genau wie die Qualifikation „Aufteilung“ wird die Qualifikation „Spracherkennung“ auch einmal für jedes Dokument aufgerufen. Die Anreicherungsstruktur verfügt jetzt über einen neuen Knoten für die Sprache.

 ![Anreicherungsstruktur nach Qualifikation 2](media/cognitive-search-working-with-skillsets/enrichment-tree-skill2.png "Anreicherungsstruktur nach Ausführung von Skill 2")
 
 ### <a name="skill-3-key-phrases-skill"></a>Qualifikation 3: Qualifikation „Schlüsselbegriffe“ 

Im Kontext `/document/reviews_text/pages/*` wird die Qualifikation „Schlüsselbegriffe“ einmal für jedes Element in der `pages`-Sammlung aufgerufen. Die Ausgabe der Qualifikation ist ein Knoten unter dem zugeordneten page-Element. 

 Wenn Sie sich jetzt die restlichen Qualifikationen in der Qualifikationsgruppe anschauen, sollten Sie sich vorstellen können, wie die Anreicherungsstruktur mit der Ausführung jeder weiteren Qualifikation weiter wächst. Einige Qualifikationen, wie z.B. die Qualifikation „Zusammenführen“ und die Qualifikation „Shaper“, erstellen auch neue Knoten, verwenden jedoch nur Daten aus vorhandenen Knoten und erstellen keine eigenen neuen Anreicherungen.

![Anreicherungsstruktur nach allen Qualifikationen](media/cognitive-search-working-with-skillsets/enrichment-tree-final.png "Anreicherungsstruktur nach allen Qualifikationen")

Die Farben der Connectors in der Struktur oben zeigen an, dass die Anreicherungen durch unterschiedliche Qualifikationen erstellt wurden, und die Knoten müssen einzeln adressiert werden und sind nicht Teil des Objekts, das bei der Auswahl des übergeordneten Knotens zurückgegeben wird.

## <a name="save-enrichments"></a>Speichern von Anreicherungen

In Azure Cognitive Search speichert ein Indexer die von ihm erstellte Ausgabe. Eine der Ausgaben ist immer ein [durchsuchbarer Index](search-what-is-an-index.md). Die Angabe eines Index ist eine Voraussetzung, und wenn Sie ein Skillset anfügen, enthalten die von einem Index erfassten Daten den Inhalt der Anreicherungen. In der Regel werden die Ergebnisse bestimmter Skills, z. B. Schlüsselbegriffe oder Stimmungswerte, im Index in einem zu diesem Zweck erstellten Feld erfasst.

Optional kann ein Indexer die Ausgabe auch an einen [Wissensspeicher](knowledge-store-concept-intro.md) senden, um sie in anderen Tools oder Prozessen zu verwenden. Ein Wissensspeicher wird als Teil des Skillsets definiert. Seine Definition bestimmt, ob Ihre angereicherten Dokumente als Tabellen oder Objekte (Dateien oder Blobs) projiziert werden. Tabellarische Projektionen eignen sich gut für interaktive Analysen in Tools wie Power BI, während Dateien und Blobs typischerweise in Data Science- oder ähnlichen Prozessen verwendet werden. In diesem Abschnitt erfahren Sie, wie die Zusammenstellung von Skillsets die Tabellen oder Objekte formen kann, die Sie projizieren möchten.

### <a name="projections"></a>Projektionen

Bei Inhalten, die auf einen Wissensspeicher ausgerichtet sind, sollten Sie sich überlegen, wie der Inhalt strukturiert ist. *Projektion* ist der Prozess der Auswahl der Knoten aus der Anreicherungsstruktur und der Erstellung eines physischen Ausdrucks dieser Knoten im Wissensspeicher. Projektionen sind benutzerdefinierte Formen des Dokuments (Inhalt und Anreicherungen), die entweder als Tabellen- oder Objektprojektionen ausgegeben werden können. Weitere Informationen zum Arbeiten mit Projektionen finden Sie unter [Arbeiten mit Projektionen](knowledge-store-projection-overview.md).

![Feldzuordnungsoptionen](./media/cognitive-search-working-with-skillsets/field-mapping-options.png "Feldzuordnungsoptionen für die Anreicherungspipeline")

### <a name="sourcecontext"></a>SourceContext

Das `sourceContext`-Element wird nur in Skilleingaben und Projektionen verwendet. Es wird verwendet, um geschachtelte Objekte mit mehreren Ebenen zu erstellen. Möglicherweise müssen Sie ein neues Objekt erstellen, um es als Eingabe an einen Skill zu übergeben oder in den Wissensspeicher zu projizieren. Da es sich bei Anreicherungsknoten möglicherweise nicht um ein gültiges JSON-Objekt in der Anreicherungsstruktur handelt und bei Verweisen auf einen Knoten in der Struktur nur der Zustand des Knotens zum Zeitpunkt seiner Erstellung zurückgegeben wird, müssen Sie ein wohlgeformtes JSON-Objekt erstellen, um die Anreicherungen als Skilleingaben oder Projektionen verwenden zu können. Mit `sourceContext` können Sie ein hierarchisches, anonymes Typobjekt erstellen, für das mehrere Qualifikationen erforderlich wären, wenn Sie nur den Kontext verwenden würden. 

Die Verwendung von `sourceContext` wird in den folgenden Beispielen gezeigt. Sehen Sie sich die Qualifikationsausgabe an, die eine Anreicherung generiert hat, um zu ermitteln, ob es sich um ein gültiges JSON-Objekt und nicht um einen primitiven Typ handelt.

### <a name="slicing-projections"></a>Aufteilen von Projektionen

Wenn Sie eine Tabellenprojektionsgruppe definieren, kann ein einzelner Knoten in der Anreicherungsstruktur per Slicing in mehrere verknüpfte Tabellen aufgeteilt werden. Wenn Sie eine Tabelle mit einem Quellpfad hinzufügen, der ein untergeordnetes Element einer vorhandenen Tabellenprojektion ist, wird der resultierende untergeordnete Knoten kein untergeordnetes Element der vorhandenen Tabellenprojektion sein, sondern stattdessen in die neue, verwandte Tabelle projiziert. Mit dieser Aufteilungstechnik können Sie einen einzelnen Knoten in einer Shaper-Qualifikation als Quelle für alle Ihre Tabellenprojektionen definieren. 

### <a name="shaping-projections"></a>Strukturieren von Projektionen

Projektionen können auf zwei Arten definiert werden:

+ Verwenden Sie den Skill „Text Shaper“, um einen neuen Knoten zu erstellen, der als Stammknoten für alle von Ihnen projizierten Anreicherungen dient. Dann würden Sie in ihren Projektionen nur auf die Ausgabe der Shaper-Qualifikation verweisen.

+ Verwenden Sie eine Inlinestruktur als Projektion innerhalb der Projektionsdefinition selbst.

Der Shaper-Ansatz ist ausführlicher als die Inlinestrukturierung, stellt jedoch sicher, dass alle Mutationen der Anreicherungsstruktur in den Qualifikationen enthalten sind und dass die Ausgabe ein Objekt ist, das wiederverwendet werden kann. Mit der Inlinestrukturierung können Sie im Gegensatz dazu die benötigte Form erstellen, aber es handelt sich um ein anonymes Objekt, das nur für die Projektion verfügbar ist, für die es definiert wurde. Die Ansätze können gleichzeitig oder getrennt einzeln verwendet werden. Die für Sie im Portalworkflow erstellte Qualifikationsgruppe enthält beide. Sie verwendet eine Shaper-Qualifikation für die Tabellenprojektionen, aber auch Inlinestrukturierung zum Projizieren der Tabelle mit den Schlüsselbegriffen.

Um das Beispiel zu erweitern, könnten Sie die Inlinestrukturierung entfernen und mithilfe einer Shaper-Qualifikation einen neuen Knoten für die Schlüsselbegriffe erstellen. Zum Erstellen einer in drei Tabellen (`hotelReviewsDocument`, `hotelReviewsPages` und `hotelReviewsKeyPhrases`) projizierten Form werden die beiden Optionen in den folgenden Abschnitten beschrieben.

#### <a name="shaper-skill-and-projection"></a>Shaper-Qualifikation und Projektion

> [!Note]
> Einige der Spalten aus der Dokumententabelle wurden aus Platzgründen aus diesem Beispiel entfernt.
>
```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "name": "#5",
    "description": null,
    "context": "/document",
    "inputs": [        
        {
            "name": "reviews_text",
            "source": "/document/reviews_text",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "reviews_title",
            "source": "/document/reviews_title",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "AzureSearch_DocumentKey",
            "source": "/document/AzureSearch_DocumentKey",
            "sourceContext": null,
            "inputs": []
        },  
        {
            "name": "pages",
            "source": null,
            "sourceContext": "/document/reviews_text/pages/*",
            "inputs": [
                {
                    "name": "SentimentScore",
                    "source": "/document/reviews_text/pages/*/Sentiment",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "LanguageCode",
                    "source": "/document/Language",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "Page",
                    "source": "/document/reviews_text/pages/*",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "keyphrase",
                    "sourceContext": "/document/reviews_text/pages/*/Keyphrases/*",
                    "inputs": [
                        {
                            "source": "/document/reviews_text/pages/*/Keyphrases/*",
                            "name": "Keyphrases"
                        }
                    ]
                }
            ]
        }
    ],
    "outputs": [
        {
            "name": "output",
            "targetName": "tableprojection"
        }
    ]
}
```

Mit dem `tableprojection`-Knoten, der im Abschnitt `outputs` oben definiert wurde, können wir jetzt das Aufteilungsfeature verwenden, um Teile des `tableprojection`-Knotens in verschiedene Tabellen zu projizieren:

> [!Note]
> Dies ist nur ein Codeausschnitt der Projektion innerhalb der Wissensspeicherkonfiguration.
>
```json
"projections": [
    {
        "tables": [
            {
                "tableName": "hotelReviewsDocument",
                "generatedKeyName": "Documentid",
                "source": "/document/tableprojection"
            },
            {
                "tableName": "hotelReviewsPages",
                "generatedKeyName": "Pagesid",
                "source": "/document/tableprojection/pages/*"
            },
            {
                "tableName": "hotelReviewsKeyPhrases",
                "generatedKeyName": "KeyPhrasesid",
                "source": "/document/tableprojection/pages/*/keyphrase/*"
            }
        ]
    }
]
```

#### <a name="inline-shaping-projections"></a>Inlinestrukturierung von Projektionen

Der Inlinestrukturierungsansatz erfordert keine Shaper-Qualifikationen, da alle für die Projektionen benötigten Formen genau dann erstellt werden, wenn Sie benötigt werden. Um die gleichen Daten wie im vorherigen Beispiel zu projizieren, würde die Inlineprojektionsoption wie folgt aus:

```json
"projections": [
    {
        "tables": [
            {
                "tableName": "hotelReviewsInlineDocument",
                "generatedKeyName": "Documentid",
                "sourceContext": "/document",     
                "inputs": [
                    {
                        "name": "reviews_text",
                        "source": "/document/reviews_text"
                    },
                    {
                        "name": "reviews_title",
                        "source": "/document/reviews_title"
                    },
                    {
                        "name": "AzureSearch_DocumentKey",
                        "source": "/document/AzureSearch_DocumentKey"
                    }                             
                ]
            },
            {
                "tableName": "hotelReviewsInlinePages",
                "generatedKeyName": "Pagesid",
                "sourceContext": "/document/reviews_text/pages/*",
                "inputs": [
                        {
                    "name": "SentimentScore",
                    "source": "/document/reviews_text/pages/*/Sentiment"
                    },
                    {
                        "name": "LanguageCode",
                        "source": "/document/Language"
                    },
                    {
                        "name": "Page",
                        "source": "/document/reviews_text/pages/*"
                    }
                ]
            },
            {
                "tableName": "hotelReviewsInlineKeyPhrases",
                "generatedKeyName": "KeyPhraseId",
                "sourceContext": "/document/reviews_text/pages/*/Keyphrases/*",
                "inputs": [
                    {
                        "name": "Keyphrases",
                        "source": "/document/reviews_text/pages/*/Keyphrases/*"
                    }
                ]
            }
        ]
    }
]
```
  
Bei beiden Ansätzen ist zu beobachten, wie Werte von `"Keyphrases"` mithilfe von `"sourceContext"` projiziert werden. Der `"Keyphrases"`-Knoten, der eine Sammlung von Zeichenfolgen enthält, ist selbst ein untergeordnetes Element des Seitentexts. Da Projektionen jedoch ein JSON-Objekt erfordern und es sich bei der Seite um einen Grundtyp (Zeichenfolge) handelt, wird `"sourceContext"` verwendet, um den Schlüsselbegriff in ein Objekt mit einer benannten Eigenschaft zu umschließen. Diese Technik ermöglicht, dass sogar Grundtypen unabhängig projiziert werden können.

## <a name="next-steps"></a>Nächste Schritte

Im nächsten Schritt erstellen Sie ihre erste Qualifikationsgruppe mit kognitiven Fähigkeiten.

> [!div class="nextstepaction"]
> [Erstellen Ihrer ersten Qualifikationsgruppe](cognitive-search-defining-skillset.md).
