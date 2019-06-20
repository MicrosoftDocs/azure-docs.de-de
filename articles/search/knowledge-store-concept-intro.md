---
title: Wissensspeicher – Einführung und Übersicht (Vorschauversion) – Azure Search
description: Senden Sie angereicherte Dokumente an Azure Storage, wo Sie die angereicherten Dokumente in Azure Search und anderen Anwendungen anzeigen, umstrukturieren und nutzen können.
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.devlang: NA
ms.topic: overview
ms.date: 05/02/2019
ms.author: heidist
ms.openlocfilehash: 4a27e4d8f2fbaafe6d27a3e3cabd31aa715b9d80
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/11/2019
ms.locfileid: "65540746"
---
# <a name="what-is-knowledge-store-in-azure-search"></a>Was ist ein Wissensspeicher in Azure Search?

> [!Note]
> Wissensspeicher befinden sich in der Vorschau und sind nicht für die Produktion ausgelegt. Dieses Feature wird durch die [REST-API-Version 2019-05-06-Preview](search-api-preview.md) bereitgestellt. Das .NET SDK wird derzeit nicht unterstützt.
>

Ein Wissensspeicher ist ein optionales Feature von Azure Search, das der Speicherung angereicherter Dokumente und Metadaten dient, die von einer KI-basierten Indizierungspipeline ([kognitive Suche](cognitive-search-concept-intro.md)) erstellt werden. Die Speicherung von Wissensspeichern erfolgt unter einem Azure Storage-Konto, das Sie als Teil der Pipeline konfigurieren. Bei der Aktivierung verwendet der Suchdienst dieses Storage-Konto, um eine Darstellung der einzelnen angereicherten Dokumente zwischenzuspeichern. 

Wenn Sie die kognitive Suche schon in der Vergangenheit verwendet haben, wissen Sie bereits, dass Sie Qualifikationsgruppen nutzen können, um ein Dokument eine Sequenz von Anreicherungen durchlaufen zu lassen. Das Ergebnis kann ein Azure Search-Index oder (neu in dieser Vorschauversion) Projektionen in einem Wissensspeicher sein.

Projektionen sind Mechanismen zum Strukturieren von Daten für die Nutzung in einer Downstream-App. Sie können [Storage-Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) für Azure-Speicher oder jede App mit einer Verbindung mit Azure Storage verwenden, um die neuen Möglichkeiten für angereicherte Dokumente zu nutzen. Einige Beispiele sind Data Science-Pipelines und benutzerdefinierte Analysen.

![Diagramm: Wissensspeicher in Pipeline](./media/knowledge-store-concept-intro/annotationstore_sans_internalcache.png "Diagramm: Wissensspeicher in Pipeline")

Um Wissensspeicher zu verwenden, fügen Sie einer Qualifikationsgruppe ein `knowledgeStore`-Element hinzu, das die Vorgänge in mehreren Schritten in einer Indizierungspipeline definiert. Während der Ausführung erstellt Azure Search einen Bereich in Ihrem Azure Storage-Konto und füllt diesen mit den Definitionen und Inhalten auf, die von der Pipeline erstellt werden.

## <a name="benefits-of-knowledge-store"></a>Vorteile von Wissensspeichern

Ein Wissensspeicher enthält die Struktur, den Kontext und den eigentlichen Inhalt. Diese Informationen werden aus unstrukturierten und teilweise strukturierten Datendateien wie Blobs und analysierten Bilddateien oder sogar aus strukturierten Daten gewonnen, die umgeformt wurden. In einer [ausführlichen exemplarischen Vorgehensweise](knowledge-store-howto.md), die für diese Vorschau verfasst wurde, können Sie genau sehen, wie ein komplexes JSON-Dokument in Unterstrukturen partitioniert, in neue Strukturen zusammengesetzt und für nachfolgende Prozesse wie Machine Learning- und Data Science-Workloads verfügbar gemacht wird.

Auch wenn es bereits nützlich ist, zu sehen, was eine KI-basierte Indizierungspipeline erstellen kann, liegt die eigentliche Stärke von Wissensspeichern in der Möglichkeit, Daten neu zu strukturieren. Sie können mit einer grundlegenden Qualifikationsgruppe beginnen und diese dann durchlaufen, um immer neue Strukturebenen zu erschaffen, die Sie dann in neuen Strukturen kombinieren können, um sie in Azure Search und anderen Apps zu verwenden.

Wissensspeicher bieten u. a. folgende Vorteile:

+ Nutzen Sie angereicherte Dokumente auch in anderen [Analyse- und Berichtstools](#tools-and-apps) als Search. Power BI mit Power Query ist eine hervorragende Wahl, aber jedes Tool und jede App, das bzw. die eine Verbindung mit Azure Storage herstellen kann, kann auch Daten aus dem von Ihnen erstellten Wissensspeicher pullen.

+ Optimieren Sie eine KI-basierte Indizierungspipeline während der Debuggingschritte und der Definition von Qualifikationen. Ein Wissensspeicher zeigt das Produkt einer Qualifikationsgruppendefinition in einer KI-Indizierungspipeline. Sie können diese Ergebnisse verwenden, um eine bessere Qualifikationsgruppe zu entwerfen, da Sie genau sehen können, wie die Anreicherungen aussehen. Verwenden Sie [Storage-Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) in Azure Storage, um die Inhalte eines Wissensspeichers anzuzeigen.

+ Formen Sie die Daten um. Die Umgestaltung ist in Qualifikationsgruppen codiert, das Wichtigste ist aber, dass diese Funktion nun von einer Qualifikationsgruppe bereitgestellt werden kann. Die [Qualifikation „Shaper“](cognitive-search-skill-shaper.md) in Azure Search wurde erweitert, um genau diese Aufgabe zu ermöglichen. Die Umstrukturierung ermöglicht Ihnen die Definition einer Projektion für die beabsichtigte Verwendung der Daten unter Beibehaltung der Beziehungen.

> [!Note]
> Sie sind nicht mit der KI-basierten Indizierung mithilfe von Cognitive Services vertraut? Azure Search ist mit den Bild- und Sprachfunktionen von Cognitive Services integriert, die zum Extrahieren und Anreichern von Quelldaten mithilfe der optischen Zeichenerkennung (OCR) über Bilddateien, der Entitätserkennung, der Extraktion von Schlüsselbegriffen aus Textdateien u. v. m. verwendet werden. Weitere Informationen finden Sie unter [Was ist die kognitive Suche?](cognitive-search-concept-intro.md).

## <a name="create-a-knowledge-store"></a>Erstellen von Wissensspeichern

Ein Wissensspeicher ist Teil der Definition einer Qualifikationsgruppe. In dieser Vorschau muss für die Erstellung die REST-API mit `api-version=2019-05-06-Preview` oder der **Assistent zum Importieren von Daten** im Portal verwendet werden.

Der folgende JSON-Code gibt einen `knowledgeStore` an, der Teil einer von einem Indexer (nicht dargestellt) aufgerufenen Qualifikationsgruppe ist. Die Spezifikation von Projektionen innerhalb des `knowledgeStore` legt fest, ob Tabellen oder Objekte in Azure Storage erstellt werden.

Wenn Sie bereits mit der KI-basierten Indizierung vertraut sind, wissen Sie, dass die Definition einer Qualifikationsgruppe Erstellung, Anordnung und Inhalt der einzelnen angereicherten Dokumente festlegt.

```json
{
  "name": "my-new-skillset",
  "description": 
  "Example showing knowledgeStore placement, supported in api-version=2019-05-06-Preview. You need at least one skill, most likely a Shaper skill if you are modulating data structures.",
  "skills":
  [
    {
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "context": "/document/content/phrases/*",
    "inputs": [
        {
        "name": "text",
        "source": "/document/content/phrases/*"
        },
        {
        "name": "sentiment",
        "source": "/document/content/phrases/*/sentiment"
        }
    ],
    "outputs": [
        {
        "name": "output",
        "targetName": "analyzedText"
        }
    ]
    },
  ],
  "cognitiveServices": 
    {
    "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey",
    "description": "mycogsvcs resource in West US 2",
    "key": "<your key goes here>"
    },
  "knowledgeStore": { 
    "storageConnectionString": "<your connection string goes here>", 
    "projections": [ 
        { 
            "tables": [  
            { "tableName": "Reviews", "generatedKeyName": "ReviewId", "source": "/document/Review" , "sourceContext": null, "inputs": []}, 
            { "tableName": "Sentences", "generatedKeyName": "SentenceId", "source": "/document/Review/Sentences/*", "sourceContext": null, "inputs": []}, 
            { "tableName": "KeyPhrases", "generatedKeyName": "KeyPhraseId", "source": "/document/Review/Sentences/*/KeyPhrases", "sourceContext": null, "inputs": []}, 
            { "tableName": "Entities", "generatedKeyName": "EntityId", "source": "/document/Review/Sentences/*/Entities/*" ,"sourceContext": null, "inputs": []} 

            ], 
            "objects": [ 
                { 
                "storageContainer": "Reviews", 
                "format": "json", 
                "source": "/document/Review", 
                "key": "/document/Review/Id" 
                } 
            ]      
        }    
    ]     
    } 
}
```

## <a name="components-backing-a-knowledge-store"></a>Komponenten von Wissensspeichern

Um einen Wissensspeicher zu erstellen, benötigen Sie die folgenden Dienste und Artefakte.

### <a name="1---source-data"></a>1. Quelldaten

Die Daten oder Dokumente, die Sie anreichern möchten, müssen in einer Azure-Datenquelle vorliegen, die von Azure Search-Indexern unterstützt wird: 

* [Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)

* [Azure Cosmos DB](search-howto-index-cosmosdb.md)

* [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)

[Azure Table Storage](search-howto-indexing-azure-tables.md) kann für ausgehende Daten in einem Wissensspeicher verwendet werden, aber nicht als Ressource für die eingehenden Daten einer KI-basierten Indizierungspipeline.

### <a name="2---azure-search-service"></a>2. Azure Search-Dienst

Sie benötigen außerdem einen Azure Search-Dienst und die REST-API, um die für die Datenanreicherung verwendeten Objekte zu erstellen und zu konfigurieren. Die REST-API für die Erstellung eines Wissensspeichers ist `api-version=2019-05-06-Preview`.

Azure Search bietet die Indexerfunktion. Indexer werden verwendet, um den gesamten Prozess von Anfang bis Ende durchzuführen, sodass die angereicherten Dokumente dauerhaft in Azure Storage beibehalten werden. Indexer verwenden eine Datenquelle, einen Index und eine Qualifikationsgruppe, die alle zum Erstellen und Auffüllen eines Wissensspeichers erforderlich sind.

| Object | REST-API | BESCHREIBUNG |
|--------|----------|-------------|
| Datenquelle | [Erstellen der Datenquelle](https://docs.microsoft.com/rest/api/searchservice/create-data-source)  | Eine Ressource, die eine externe Azure-Datenquelle identifiziert, welche Quelldaten zum Erstellen angereicherter Dokumente bereitstellt.  |
| Qualifikationsgruppe | [Erstellen einer Qualifikationsgruppe (API-Version 2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | Eine Ressource, die die Verwendung von [integrierten Qualifikationen](cognitive-search-predefined-skills.md) und [benutzerdefinierten kognitiven Qualifikationen](cognitive-search-custom-skill-interface.md) in einer Anreicherungspipeline während der Indizierung koordiniert. |
| index | [Index erstellen](https://docs.microsoft.com/rest/api/searchservice/create-index)  | Ein Schema zur Beschreibung eines Azure Search-Indexes. Felder im Index werden Feldern in den Quelldaten zugeordnet oder Feldern, die während der Anreicherungsphase erstellt wurden (z.B. ein von der Entitätserkennung erstelltes Feld für Organisationsnamen). |
| Indexer | [Erstellen eines Indexers (API-Version 2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | Eine Ressource, die während der Indizierung verwendete Komponenten definiert: Dazu zählen eine Datenquelle, eine Fähigkeitengruppe, Feldzuordnungen der Quelle, intermediäre Datenstrukturen für den Zielindex und der Index selbst. Die Ausführung des Indexers ist Auslöser für die Datenerfassung und -anreicherung. Die Ausgabe ist ein Suchindex basierend auf dem Indexschema, der mit Quelldaten aufgefüllt wird, die durch Qualifikationsgruppen angereichert werden.  |

### <a name="3---cognitive-services"></a>3. Cognitive Services

Die in einer Qualifikationsgruppe angegebenen Anreicherungen basieren auf den Funktionen für maschinelles Sehen und zur Spracherkennung in Cognitive Services. Die Cognitive Services-Funktionen werden während der Indizierung über Ihre Qualifikationsgruppe genutzt. Eine Qualifikationsgruppe ist eine Zusammenstellung von Qualifikationen, die wiederum an bestimmte Funktionen für maschinelles Sehen und Sprachfunktionen gebunden sind. Für die Integration mit Cognitive Services fügen Sie einer Qualifikationsgruppe [eine Cognitive Services-Ressource](cognitive-search-attach-cognitive-services.md) hinzu.

### <a name="4---storage-account"></a>4. Speicherkonto

Azure Search erstellt unter Ihrem Azure Storage-Konto einen Blobcontainer oder Tabellen (je nachdem, wie Sie die Qualifikationsgruppe konfigurieren). Wenn Ihre Daten aus Azure Blob Storage oder Table Storage stammen, sind Sie damit bereits fertig. Andernfalls müssen Sie noch ein Azure Storage-Konto erstellen. Tabellen und Objekte in Azure Storage enthalten die angereicherten Dokumente, die von der KI-basierten Indizierungspipeline erstellt werden.

Das Speicherkonto wird in der Qualifikationsgruppe angegeben. In `api-version=2019-05-06-Preview` umfasst die Definition einer Qualifikationsgruppe auch einen Wissensspeicher, sodass Sie Kontoinformationen angeben können.

<a name="tools-and-apps"></a>

### <a name="5---access-and-consume"></a>5. Zugriff und Verwendung

Wenn die Anreicherungen in Storage gespeichert sind, können Sie beliebige Tools oder Technologien mit einer Verbindung mit Azure Blob Storage oder Azure Table Storage verwenden, um die Inhalte zu untersuchen, zu analysieren oder zu nutzen. Die folgende Liste gibt einen ersten Einblick:

+ Mit [Storage-Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) können Sie die Struktur und den Inhalt angereicherter Dokumente anzeigen. Nutzen Sie dies als Ihr Basistool zum Anzeigen des Inhalts von Wissensspeichern.

+ [Power BI mit Power Query](https://support.office.com/article/connect-to-microsoft-azure-blob-storage-power-query-f8165faa-4589-47b1-86b6-7015b330d13e) können Sie für Abfragen in natürlicher Sprache verwenden. Sie können auch die Tools zur Berichterstellung und Analyse nutzen, wenn Sie mit numerischen Daten arbeiten.

+ Verwenden Sie [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/) für die weiterführende Bearbeitung.

+ Nutzen Sie den Azure Search-Index für die Volltextsuche über Inhalte, die Sie mithilfe von [Cognitive Search](cognitive-search-concept-intro.md) indiziert haben.

## <a name="document-persistence"></a>Dokumentpersistenz

Innerhalb des Speicherkontos können die Anreicherungen als Tabellen in Azure Table Storage oder als Objekte in Azure Blob Storage ausgedrückt werden. Denken Sie daran, dass Anreicherungen nach dem Speichern als Quelle zum Laden von Daten in andere Datenbanken und Tools verwendet werden können.

+ Table Storage ist nützlich, wenn Sie eine schemafähige Darstellung der Daten in tabellarischer Form benötigen. Wenn Sie Elemente umstrukturieren oder anders kombinieren möchten, bietet Table Storage die erforderlichen Granularität.

+ Blob Storage erstellt eine umfassende JSON-Darstellung jedes Dokuments. Sie können beide Speicheroptionen in einer Qualifikationsgruppe verwenden, um sämtliche Ausdrücke nutzen zu können.

+ Azure Search speichert Inhalte in einem Index. Wenn Ihr Szenario keine Suchen umfasst (z. B. wenn das Ziel Ihrer Analyse ein anderes Tool ist), können Sie den von der Pipeline erstellten Index löschen. Sie können den Index aber auch beibehalten und ein integriertes Tool wie den [Suchexplorer](search-explorer.md) als drittes Medium (neben Storage-Explorer und Ihrer Analyse-App) für die Interaktion mit Ihren Inhalten verwenden.

Neben dem Inhalt enthalten angereicherte Dokumente auch Metadaten zur Version der Qualifikationsgruppe, mit der die Anreicherungen erstellt wurden.  

## <a name="inside-a-knowledge-store"></a>Inhalt von Wissensspeichern

Ein Wissensspeicher besteht aus einem Anmerkungscache und Projektionen. Der *Cache* wird intern vom Dienst verwendet, um die Ergebnisse von Qualifikationen zwischenzuspeichern und Änderungen nachzuverfolgen. Eine *Projektion* definiert das Schema und die Struktur der Anreicherungen, die der beabsichtigten Verwendung entsprechen. Es gibt einen Cache pro Wissensspeicher, aber mehrere Projektionen. 

Der Cache ist immer ein Blobcontainer, Projektionen können hingegen als Tabellen oder Objekte vorliegen:

+ Als ein Objekt ist die Projektion Blob Storage zugeordnet. Dort wird die Projektion in einem Container gespeichert, der wiederum die Objekte oder hierarchischen Darstellungen im JSON-Format für Szenarien wie eine Data Science-Pipeline enthält.

+ Als eine Tabelle ist die Projektion Table Storage zugeordnet. Eine tabellarische Darstellung behält die Beziehungen für Szenarien wie Datenanalysen oder das Exportieren als Datenrahmen für das maschinelle Lernen bei. Die angereicherten Projektionen können dann problemlos in andere Datenspeicher importiert werden. 

Sie können mehrere Projektionen in einem Wissensspeicher erstellen, um verschiedene Bereiche in Ihrer Organisation abzubilden. Ein Entwickler benötigt möglicherweise Zugriff auf die vollständige JSON-Darstellung eines angereicherten Dokuments, während Data Science-Experten oder Analysten abgestimmte oder modulare Datenstrukturen benötigen, die von Ihrer Qualifikationsgruppe strukturiert wurden.

Wenn beispielsweise eines der Ziele der Anreicherung auch das Erstellen eines Datasets zum Trainieren eines Modells ist, wäre eine Möglichkeit zur Nutzung Ihrer Daten in Ihren Data Science-Pipelines das Projizieren der Daten in den Objektspeicher. Wenn Sie alternativ schnell ein Power BI-Dashboard basierend auf den angereicherten Dokumenten erstellen möchten, wäre die tabellarische Projektion gut geeignet.

<!---
## Data lifecycle and billing

Each time you run the indexer, the cache in Azure storage is updated if the skillset definition or underlying source data has changed. As input documents are edited or deleted, changes are propagated through the annotation cache to the projections, ensuring that your projected data is a current representation of your inputs at the end of the indexer run. 

Generally speaking, pipeline processing can be an all-or-nothing operation, but Azure Search can process incremental changes, which saves you time and money.

If a document is new or updated, all skills are run. If only the skillset changes, reprocessing is scoped to just those skills and documents affected by your edit.

### Changes to a skillset
Suppose that you have a pipeline composed of multiple skills, operating over a large body of static data (for example, scanned documents), that takes 8 hours and costs $200 to create the knowledge store. Now suppose you need to tweak one of the skills in the skillset. Rather than starting over, Azure Search can determine which skill is affected, and reprocess only that skill. Cached data and projections that are unaffected by the change remain intact in the knowledge store.

### Changes in the data
Scenarios can vary considerably, but let's suppose instead of static data, you have volatile data that changes between indexer invocations. Given no changes to the skillset, you are charged for processing the delta of new and modified document. The timestamp information varies by data source, but for illustration, in a Blob container, Azure Search looks at the `lastmodified` date to determine which blobs need to be ingested.

> [!Note]
> While you can edit the data in the projections, any edits will be overwritten on the next pipeline invocation, assuming the document in source data is updated. 

### Deletions

Although Azure Search creates and updates structures and content in Azure storage, it does not delete them. Projections and cached documents continue to exist even when the skillset is deleted. As the owner of the storage account, you should delete a projection if it is no longer needed. 

### Tips for development

+ Start small with a representative sample of your data as you make significant changes to skillset composition. As your design finalizes, you can slowly add more data during later-stage development, and then roll in the entire data set when you are comfortable with the pipeline composition.

+ Retain control over indexer invocation. Indexers can run on a schedule, which is helpful for solutions that are rolled into production, but less helpful if you are actively developing your pipeline. During development, avoid schedules so that you don’t lose track of cache or projection state. Once your solution is in production and skillset composition is static, you can put the indexer on a schedule to pick up routine changes in the external source data. 

-->

## <a name="where-do-i-start"></a>Wo beginne ich?

Es empfiehlt sich, zu Lernzwecken den kostenlosen Dienst zu verwenden. Beachten Sie jedoch, dass die Anzahl kostenloser Transaktionen auf 20 Dokumente pro Tag und Abonnement beschränkt ist.

Wenn Sie mehrere Dienste verwenden, erstellen Sie alle Dienste in derselben Region, um eine optimale Leistung zu erzielen und die Kosten zu minimieren. Für die ein- und ausgehenden Daten zu Diensten in derselben Region fallen keine Kosten für die Bandbreite an.

**Schritt 1: [Erstellen einer Azure Search-Ressource](search-create-service-portal.md)** 

**Schritt 2: [Erstellen eines Azure Storage-Kontos](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)** 

**Schritt 3: [Erstellen einer Cognitive Services-Ressource](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)** 

**Schritt 4: [Erste Schritte mit dem Portal](cognitive-search-quickstart-blob.md) – oder – [Erste Schritte mit Beispieldaten unter Verwendung von REST und Postman](knowledge-store-howto.md)** 

Sie können die REST-`api-version=2019-05-06-Preview` verwenden, um eine KI-basierte Pipeline zu erstellen, die einen Wissensspeicher umfasst. In der neuesten Vorschau-API enthält das Skillset-Objekt die `knowledgeStore`-Definition.

## <a name="takeaways"></a>Wesentliche Punkte

Wissensspeicher bieten viele Vorteile, einschließlich der Verwendung der angereicherten Dokumente in anderen Szenarien als der Suche, der Kostenkontrolle oder der Verwaltung von Abweichungen in Ihrem Anreicherungsprozess – um nur einige zu nennen. Alle diese Funktionen sind direkt verfügbar, indem Sie Ihrer Qualifikationsgruppe ein Speicherkonto hinzufügen und die aktualisierte Ausdruckssprache verwenden. Eine Beschreibung finden Sie unter [Erste Schritte mit Wissensspeichern](knowledge-store-howto.md). 

## <a name="next-steps"></a>Nächste Schritte

Der einfachste Ansatz zum Erstellen von angereicherten Dokumenten stellt der **Assistent zum Importieren von Daten** dar.

> [!div class="nextstepaction"]
> [Schnellstart: Testen der kognitiven Suche anhand einer exemplarischen Vorgehensweise zu Portalfunktionen](cognitive-search-quickstart-blob.md)
