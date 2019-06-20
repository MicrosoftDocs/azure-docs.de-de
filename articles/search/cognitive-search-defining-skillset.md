---
title: Erstellen einer Qualifikationsgruppe in einer Pipeline der kognitiven Suche – Azure Search
description: Definieren Sie Datenextrahierung, Verarbeitung natürlicher Sprache oder Bildanalyseschritte, um strukturierte Informationen aus Ihren Daten für die Verwendung in Azure Search anzureichern und zu extrahieren.
manager: pablocas
author: luiscabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 010880304c5011bd5a767650aa0ae6b6103bdde2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65540858"
---
# <a name="how-to-create-a-skillset-in-an-enrichment-pipeline"></a>Erstellen eines Skillsets in einer Anreicherungspipeline

Die kognitive Suche extrahiert Daten und reichert diese an, um sie in Azure Search durchsuchbar zu machen. Extrahierungs- und Anreicherungsschritte werden als *kognitive Skills* bezeichnet, die in einem *Skillset* kombiniert sind, auf das während der Indizierung verwiesen wird. Eine Qualifikationsgruppe kann [integrierte Qualifikationen](cognitive-search-predefined-skills.md) oder benutzerdefinierte Qualifikationen verwenden. (Weitere Informationen finden Sie unter [Beispiel: Erstellen einer benutzerdefinierten Qualifikation](cognitive-search-create-custom-skill-example.md).)

In diesem Artikel erfahren Sie, wie Sie eine Anreicherungspipeline für die Skills erstellen, die Sie verwenden möchten. Ein Skillset wird an einen [Azure Search-Indexer](search-indexer-overview.md) angehängt. Ein Teil des Pipelineentwurfs, der in diesem Artikel behandelt wird, ist die Konstruktion des Skillsets selbst. 

> [!NOTE]
> Ein weiterer Teil des Pipelineentwurfs ist die Festlegung eines Indexers im [nächsten Schritt](#next-step). Eine Indexerdefinition enthält einen Verweis auf das Skillset sowie Feldzuordnungen, die für die Verbindung von Eingaben zu Ausgaben im Zielindex verwendet werden.

Wichtige Punkte, die Sie beachten sollten:

+ Pro Indexer kann nur ein Skillset vorhanden sein.
+ Ein Skillset muss mindestens einen Skill umfassen.
+ Sie können mehrere Qualifikationen des gleichen Typs erstellen (z. B. Varianten einer Bildanalysequalifikation).

## <a name="begin-with-the-end-in-mind"></a>Denken Sie bereits zu Beginn an das Ende

Ein empfohlener erster Schritt ist die Entscheidung, welche Daten Sie aus Ihren Rohdaten extrahieren und wie Sie diese Daten in einer Suchanwendung verwenden wollen. Das Erstellen einer Abbildung der gesamten Anreicherungspipeline kann Ihnen helfen, die notwendigen Schritte zu identifizieren.

Angenommen, Sie möchten eine Reihe von Kommentaren von Finanzexperten verarbeiten. Für jede Datei möchten Sie Firmennamen und die allgemeine Stimmung der Kommentare extrahieren. Sie können auch eine benutzerdefinierte Anreicherungsfunktion schreiben, die den Bing-Entitätssuche-Dienst verwendet, um zusätzliche Informationen über das Unternehmen zu finden, z.B. welche Art von Geschäft das Unternehmen betreibt. Im Wesentlichen möchten Sie Informationen wie die folgenden extrahieren, die für jedes Dokument indiziert sind:

| Datensatztext | Unternehmen | Stimmung | Unternehmensbeschreibung |
|--------|-----|-----|-----|
|Beispiel-Datensatz| [„Microsoft“, „LinkedIn“] | 0,99 | [„Microsoft Corporation ist ein amerikanisches multinationales Technologieunternehmen....“ „LinkedIn ist ein wirtschafts- und beschäftigungsorientiertes soziales Netzwerk....“]

Das folgende Diagramm veranschaulicht eine hypothetische Anreicherungspipeline:

![Eine hypothetische Anreicherungspipeline](media/cognitive-search-defining-skillset/sample-skillset.png "Eine hypothetische Anreicherungspipeline")


Sobald Sie eine ungefähre Vorstellung davon haben, wie die Pipeline aussehen soll, können Sie das Skillset für die erforderlichen Schritte erstellen. Funktionell wird das Skillset ausgedrückt, sobald Sie Ihre Indexerdefinition in Azure Search hochladen. Weitere Informationen darüber, wie Sie Ihren Indexer hochladen können, finden Sie in der [Indexerdokumentation](https://docs.microsoft.com/rest/api/searchservice/create-indexer).


Im Diagramm erfolgt der Schritt zur *Dokumententschlüsselung* automatisch. Bekannte Dateien werden in Azure Search problemlos geöffnet, und es wird ein *content*-Feld erstellt, das den aus jedem Dokument extrahierten Text enthält. Die weißen Felder sind integrierte Anreicherungsfunktionen, und das gepunktete Feld „Bing Entity Search“ (Bing-Entitätssuche) stellt eine benutzerdefinierte Anreicherungsfunktion dar, die Sie erstellen. Wie dargestellt, enthält das Skillset drei Skills.

## <a name="skillset-definition-in-rest"></a>Skillsetdefinition in REST

Ein Skillset wird als ein Array von Skills definiert. Jeder Skill definiert die Quelle der Eingaben und den Namen der erzeugten Ausgaben. Mit der [REST-API zum Erstellen von Skillsets](https://docs.microsoft.com/rest/api/searchservice/create-skillset) können Sie einen Skillset definieren, der dem vorherigen Diagramm entspricht: 

```http
PUT https://[servicename].search.windows.net/skillsets/[skillset name]?api-version=2019-05-06
api-key: [admin key]
Content-Type: application/json
```

```json
{
  "description": 
  "Extract sentiment from financial records, extract company names, and then find additional information about each company mentioned.",
  "skills":
  [
    {
      "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
      "context": "/document",
      "categories": [ "Organization" ],
      "defaultLanguageCode": "en",
      "inputs": [
        {
          "name": "text",
          "source": "/document/content"
        }
      ],
      "outputs": [
        {
          "name": "organizations",
          "targetName": "organizations"
        }
      ]
    },
    {
      "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
      "inputs": [
        {
          "name": "text",
          "source": "/document/content"
        }
      ],
      "outputs": [
        {
          "name": "score",
          "targetName": "mySentiment"
        }
      ]
    },
    {
      "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
     "description": "Calls an Azure function, which in turn calls Bing Entity Search",
      "uri": "https://indexer-e2e-webskill.azurewebsites.net/api/InvokeTextAnalyticsV3?code=foo",
      "httpHeaders": {
          "Ocp-Apim-Subscription-Key": "foobar"
      },
      "context": "/document/organizations/*",
      "inputs": [
        {
          "name": "query",
          "source": "/document/organizations/*"
        }
      ],
      "outputs": [
        {
          "name": "description",
          "targetName": "companyDescription"
        }
      ]
    }
  ]
}
```

## <a name="create-a-skillset"></a>Erstellen eines Skillsets

Beim Erstellen eines Skillsets können Sie eine Beschreibung angeben, um das Skillset selbstdokumentierend zu gestalten. Eine Beschreibung ist optional, aber zum Nachverfolgen der Wirkungsweise eines Skillsets durchaus nützlich. Da das Skillset ein JSON-Dokument ist, das keine Kommentare zulässt, müssen Sie dafür ein `description`-Element verwenden.

```json
{
  "description": 
  "This is our first skill set, it extracts sentiment from financial records, extract company names, and then finds additional information about each company mentioned.",
  ...
}
```

Das nächste Segment im Skillset ist ein Array von Skills. Man kann sich jeden Skill als einen Grundtyp für die Anreicherung vorstellen. In dieser Anreicherungspipeline führt jeder Skill eine kleine Aufgabe aus. Dies ist eine Eingabe (oder eine Reihe von Eingaben), durch die einige Ausgaben zurückgegeben werden. In den nächsten Abschnitten geht es darum, vordefinierte und integrierte Qualifikationen festzulegen und diese durch Eingabe- und Ausgabeverweise miteinander zu verketten. Eingaben können aus Quelldaten oder aus einem anderen Skill stammen. Ausgaben können einem Feld in einem Suchindex zugeordnet oder als Eingabe für einen nachfolgenden Skill verwendet werden.

## <a name="add-built-in-skills"></a>Hinzufügen integrierter Qualifikationen

Betrachten Sie die erste Qualifikation, bei der es sich um die integrierte [Qualifikation „Entitätserkennung“](cognitive-search-skill-entity-recognition.md) handelt:

```json
    {
      "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
      "context": "/document",
      "categories": [ "Organization" ],
      "defaultLanguageCode": "en",
      "inputs": [
        {
          "name": "text",
          "source": "/document/content"
        }
      ],
      "outputs": [
        {
          "name": "organizations",
          "targetName": "organizations"
        }
      ]
    }
```

* Jede integrierte Qualifikation verfügt über die Eigenschaften `odata.type`, `input` und `output`. Skillspezifische Eigenschaften bieten zusätzliche Informationen, die auf diesen Skill anwendbar sind. Bei der Entitätserkennung ist `categories` eine Entität aus einem festen Satz von Entitätstypen, die das vortrainierte Modell erkennen kann.

* Jeder Skill muss über ein ```"context"```-Element verfügen. Der Kontext stellt die Ebene dar, auf der Vorgänge ausgeführt werden. In der oben gezeigten Qualifikation ist der Kontext das gesamte Dokument. Das bedeutet, dass die Qualifikation „Entitätserkennung“ einmal pro Dokument aufgerufen wird. Auf dieser Ebene werden auch Ausgaben erzeugt. Genauer gesagt werden ```"organizations"``` als Element von ```"/document"``` generiert. In den nachfolgenden Skills wird auf diese neu erstellten Informationen mit ```"/document/organizations"``` verwiesen.  Wenn das Feld ```"context"``` nicht explizit festgelegt ist, wird das Dokument als Standardkontext genommen.

* Der Skill hat eine Eingabe namens „text“, wobei die Eingabe der Quelle auf ```"/document/content"``` gesetzt ist. Die Qualifikation (Entitätserkennung) wird für das *content*-Feld jedes Dokuments ausgeführt. Hierbei handelt es sich um ein Standardfeld, das vom Azure Blob-Indexer erstellt wird. 

* Der Skill verfügt über eine Ausgabe mit der Bezeichnung ```"organizations"```. Ausgaben gibt es nur während der Verarbeitung. Um diese Ausgabe mit der Eingabe eines nachfolgenden Skills zu verketten, verweisen Sie auf die Ausgabe mit ```"/document/organizations"```.

* Für ein bestimmtes Dokument ist der Wert von ```"/document/organizations"``` ein Array von Organisationen, die aus dem Text extrahiert werden. Beispiel:

  ```json
  ["Microsoft", "LinkedIn"]
  ```

In manchen Situationen ist es notwendig, auf jedes Element eines Arrays einzeln zu verweisen. Angenommen, Sie möchten jedes Element von ```"/document/organizations"``` separat an einen anderen Skill übergeben (wie z.B. die benutzerdefinierte Anreicherungsfunktion der Bing-Entitätssuche). Sie können auf jedes Element des Arrays verweisen, indem Sie dem Pfad ein Sternchen hinzufügen: ```"/document/organizations/*"``` 

Der zweite Skill zum Extrahieren von Stimmungen folgt dem gleichen Muster wie die erste Anreicherungsfunktion. Er verwendet ```"/document/content"``` als Eingabe und gibt einen Wert für die Stimmung für jede Inhaltsinstanz zurück. Da Sie das Feld ```"context"``` nicht explizit festgelegt haben, ist die Ausgabe (mySentiment) nun ein untergeordnetes Element von ```"/document"```.

```json
    {
      "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
      "inputs": [
        {
          "name": "text",
          "source": "/document/content"
        }
      ],
      "outputs": [
        {
          "name": "score",
          "targetName": "mySentiment"
        }
      ]
    },
```

## <a name="add-a-custom-skill"></a>Hinzufügen eines benutzerdefinierten Skills

Erinnern Sie sich an die Struktur der benutzerdefinierten Anreicherungsfunktion für die Bing-Entitätssuche:

```json
    {
      "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
     "description": "This skill calls an Azure function, which in turn calls Bing Entity Search",
      "uri": "https://indexer-e2e-webskill.azurewebsites.net/api/InvokeTextAnalyticsV3?code=foo",
      "httpHeaders": {
          "Ocp-Apim-Subscription-Key": "foobar"
      }
      "context": "/document/organizations/*",
      "inputs": [
        {
          "name": "query",
          "source": "/document/organizations/*"
        }
      ],
      "outputs": [
        {
          "name": "description",
          "targetName": "companyDescription"
        }
      ]
    }
```

Diese Definition ist ein [benutzerdefinierter Skill](cognitive-search-custom-skill-web-api.md), der eine Web-API als Teil des Anreicherungsprozesses aufruft. Für jede Organisation, die durch die Entitätserkennung identifiziert wird, ruft diese Qualifikation eine Web-API auf, um die Beschreibung dieser Organisation zu suchen. Eine interne Anreicherungs-Engine regelt die Orchestrierung, wann die Web-API aufgerufen werden soll und wie die empfangenen Informationen weitergeleitet werden sollen. Die für den Aufruf dieser benutzerdefinierten API erforderliche Initialisierung muss jedoch über das JSON-Dokument bereitgestellt werden (z.B. URI, HTTP-Header und die erwarteten Eingaben). Informationen zum Erstellen einer benutzerdefinierten Web-API für die Anreicherungspipeline finden Sie unter [Definieren einer benutzerdefinierten Schnittstelle](cognitive-search-custom-skill-interface.md).

Beachten Sie, dass das Feld „context“ mit einem Sternchen auf ```"/document/organizations/*"``` gesetzt ist, d.h. der Anreicherungsschritt wird *für jede* Organisation unter ```"/document/organizations"``` aufgerufen. 

Die Ausgabe, in diesem Fall eine Firmenbeschreibung, wird für jede identifizierte Organisation generiert. Bei Bezugnahme auf die Beschreibung in einem nachfolgenden Schritt (z.B. bei der Schlüsselbegriffserkennung) würden Sie dazu den Pfad ```"/document/organizations/*/description"``` verwenden. 

## <a name="add-structure"></a>Hinzufügen einer Struktur

Das Skillset generiert strukturierte Informationen aus unstrukturierten Daten. Betrachten Sie das folgende Beispiel:

*„In seinem vierten Quartal verzeichnete Microsoft 1,1 Milliarden Dollar Umsatz mit LinkedIn, dem im letzten Jahr erworbenen Unternehmen für soziale Netzwerke. Die Übernahme ermöglicht es Microsoft, LinkedIn-Funktionen mit seinen CRM- und Office-Funktionen zu kombinieren. Die Aktionäre sind von den bisherigen Fortschritten begeistert.“*

Ein wahrscheinliches Ergebnis wäre eine generierte Struktur ähnlich der folgenden Abbildung:

![Beispiel für Ausgabestruktur](media/cognitive-search-defining-skillset/enriched-doc.png "Beispiel für Ausgabestruktur")

Bis jetzt wurde diese Struktur nur intern, ausschließlich im Arbeitsspeicher und nur in Azure Search-Indizes verwendet. Das Hinzufügen eines Wissensspeichers bietet Ihnen eine Möglichkeit zum Speichern von Anreicherungen mit Formen zur Verwendung außerhalb der Suche.

## <a name="add-a-knowledge-store"></a>Hinzufügen von Wissensspeichern

Ein [Wissensspeicher](knowledge-store-concept-intro.md) ist eine Previewfunktion in Azure Search zum Speichern Ihrer angereicherten Dokumente. Wenn Sie einen Wissensspeicher erstellen, der durch ein Azure Storage-Konto unterstützt wird, fungiert er als Repository für Ihre angereicherten Daten. 

Die Definition eines Wissensspeichers wird einer Qualifikationsgruppe hinzugefügt. Eine exemplarische Vorgehensweise des gesamten Prozesses finden Sie unter [Erste Schritte mit Wissensspeichern](knowledge-store-howto.md).

```json
"knowledgeStore": {
  "storageConnectionString": "<an Azure storage connection string>",
  "projections" : [
    {
      "tables": [ ]
    },
    {
      "objects": [
        {
          "storageContainer": "containername",
          "source": "/document/EnrichedShape/",
          "key": "/document/Id"
        }
      ]
    }
  ]
}
```

Sie können angereicherte Dokumente auch als Tabellen mit hierarchischen Beziehungen oder als JSON-Dokumente in Blob Storage speichern. Die Ausgabe einer der Qualifikationen in der Qualifikationsgruppe kann als Eingabe für die Projektion verwendet werden. Wenn Sie die Daten in eine bestimmte Form projizieren möchten, kann die aktualisierte [Qualifikation „Shaper“](cognitive-search-skill-shaper.md) nun auch komplexe Typen modellieren, die Sie dann verwenden können. 

<a name="next-step"></a>

## <a name="next-steps"></a>Nächste Schritte

Nun, da Sie mit der Anreicherungspipeline und den Skillsets vertraut sind, können Sie mit den Artikeln [Verweisen auf Anmerkungen in einem Skillset](cognitive-search-concept-annotations-syntax.md) oder [Zuordnen von Ausgaben zu Feldern in einem Index](cognitive-search-output-field-mapping.md) fortfahren. 
