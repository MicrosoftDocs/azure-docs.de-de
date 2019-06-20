---
title: Indizieren von JSON-Blobs aus Azure Blob-Indexer für die Volltextsuche – Azure Search
description: Durchforsten Sie Azure JSON-Blobs mithilfe des Azure Search-Blobindexers nach Textinhalten. Indexer automatisieren die Datenerfassung für ausgewählte Datenquellen wie Azure Blob Storage.
ms.date: 05/02/2019
author: HeidiSteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 4f3fb624f5e6137c9edb0be97adc16d8c808ebd9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65523071"
---
# <a name="how-to-index-json-blobs-using-azure-search-blob-indexer"></a>Indizieren von JSON-Blobs mit dem Azure Search-Blobindexer
In diesem Artikel wird beschrieben, wie Sie einen Azure Search-[Blobindexer](search-indexer-overview.md) konfigurieren, um strukturierten Inhalt aus JSON-Dokumenten in Azure Blob Storage zu extrahieren und so vorzubereiten, dass er ihn Azure Search durchsucht werden kann. Dieser Workflow erstellt einen Azure Search-Index und lädt ihn mit vorhandenem, aus JSON-Blobs extrahiertem Text. 

Sie können das [Portal](#json-indexer-portal), [REST-APIs](#json-indexer-rest) oder das [.NET SDK](#json-indexer-dotnet) verwenden, um JSON-Inhalt zu indizieren. JSON-Dokumente befinden sich grundsätzlich in einem Blobcontainer eines Azure Storage-Kontos. Anleitungen zum Übertragen von JSON-Dokumenten mithilfe von Push von anderen Nicht-Azure-Plattformen finden Sie unter [Indizieren externer Daten für Abfragen in Azure Search](search-what-is-data-import.md).

JSON-Blobs in Azure Blob Storage bestehen normalerweise entweder aus einem einzelnen JSON-Dokument oder einer Sammlung von JSON-Entitäten. Für JSON-Sammlungen könnte das Blob möglicherweise ein **Array** wohlgeformter JSON-Elemente enthalten. Blobs könnten auch aus mehreren einzelnen JSON-Entitäten zusammengesetzt sein, die durch einen Zeilenvorschub getrennt werden. Mit dem Blobindexer in Azure Search können beide Fälle analysiert werden. Dies richtet sich danach, wie Sie den Parameter **parsingMode** in der Anforderung festlegen.

Alle JSON-Analysemodi (`json`, `jsonArray`, `jsonLines`) sind jetzt allgemein verfügbar. 

> [!NOTE]
> Befolgen Sie die Empfehlungen zur Indexerkonfiguration in [1:n-Indizierung](search-howto-index-one-to-many-blobs.md), um mehrere Suchdokumente aus einem Azure-Blob auszugegeben.

<a name="json-indexer-portal"></a>

## <a name="use-the-portal"></a>Verwenden des Portals

Die einfachste Methode zum Indizieren von JSON-Dokumenten ist die Verwendung eines Assistenten im [Azure-Portal](https://portal.azure.com/). Der [**Datenimport**](search-import-data-portal.md)-Assistent kann durch das Analysieren von Metadaten im Azure-Blobcontainer einen Standardindex erstellen, Quellfelder Zielindexfeldern zuordnen und den Index in einem einzigen Vorgang laden. Je nach Größe und Komplexität der Quelldaten können Sie auch innerhalb von Minuten einen funktionsfähigen Volltextsuchindex erstellen.

Sie sollten für Azure Search und Azure Storage dasselbe Azure-Abonnement verwenden, vorzugsweise in derselben Region.

### <a name="1---prepare-source-data"></a>1\. Vorbereiten von Quelldaten

1. [Melden Sie sich beim Azure-Portal an](https://portal.azure.com/).

1. [Erstellen Sie einen Blobcontainer](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) für Ihre Daten. Die öffentliche Zugriffsebene kann auf jeden der gültigen Werte festgelegt werden.

Sie benötigen den Namen des Speicherkontos, den Containernamen und einen Zugriffsschlüssel zum Abrufen Ihrer Daten im **Datenimport**-Assistenten.

### <a name="2---start-import-data-wizard"></a>2\. Starten des Datenimport-Assistenten

Auf der Seite „Übersicht“ Ihres Azure Search-Diensts können Sie [den Assistenten über die Befehlsleiste starten](search-import-data-portal.md). Alternativ können Sie im Abschnitt **Blob-Dienst**, der sich im linken Navigationsbereich Ihres Speicherkontos befindet, auf **Azure Search hinzufügen** klicken.

   ![Befehl „Daten importieren“ im Portal](./media/search-import-data-portal/import-data-cmd2.png "Starten des Datenimport-Assistenten")

### <a name="3---set-the-data-source"></a>3\. Einrichten der Datenquelle

Auf der Seite **Datenquelle** muss die Quelle **Azure Blob Storage** lauten und folgende Spezifikationen aufweisen:

+ **Zu extrahierende Daten** muss *Inhalt und Metadaten* sein. Durch die Auswahl dieser Option kann der Assistent ein Indexschema ableiten und so die Felder für den Importvorgang zuordnen.
   
+ **Analysemodus** muss auf *JSON*, *JSON-Array* oder *JSON-Zeilen* festgelegt sein. 

  *JSON* definiert jedes Blob als einzelnes Suchdokument, das als unabhängiges Element in Suchergebnissen angezeigt wird. 

  *JSON-Array* ist für Blobs, die wohlgeformte JSON-Daten enthalten – die wohlgeformten JSON-Daten entsprechen einem Array von Objekten, oder verfügen über eine Eigenschaft, die ein Array von Objekten ist, und jedes Element soll als eigenständiges, unabhängiges Suchdokument dargestellt werden. Wenn Blobs komplex sind und Sie *JSON-Array* nicht auswählen, dann wird das gesamte Blob als einzelnes Dokument erfasst.

  *JSON-Zeilen* ist für Blobs, die aus mehreren durch einen Zeilenvorschub getrennte JSON-Entitäten bestehen, wobei jede Entität als eigenständiges unabhängiges Suchdokument angesehen werden soll. Wenn Blobs komplex sind und Sie den Analysemodus *JSON-Zeilen* nicht auswählen, dann wird das gesamte Blob als einzelnes Dokument erfasst.
   
+ **Speichercontainer** muss Ihr Speicherkonto sowie den Container angeben oder alternativ eine Verbindungszeichenfolge, die im Container aufgelöst wird. Sie können Verbindungszeichenfolgen auf der Seite des Blob-Dienst-Portals abrufen.

   ![Definition der Blobdatenquelle](media/search-howto-index-json/import-wizard-json-data-source.png)

### <a name="4---skip-the-add-cognitive-search-page-in-the-wizard"></a>4\. Überspringen der Seite „Kognitive Suche hinzufügen“ im Assistenten

Das Hinzufügen kognitiver Qualifikationen ist für den Import eines JSON-Dokuments nicht erforderlich. Wenn Sie nicht unbedingt [Cognitive Services-APIs und Transformationen](cognitive-search-concept-intro.md) zu Ihrer Indizierungspipeline hinzufügen müssen, können Sie diesen Schritt überspringen.

Um den Schritt zu überspringen, wechseln Sie zunächst zur nächsten Seite.

   ![Nächste Seite, Schaltfläche für kognitive Suche](media/search-get-started-portal/next-button-add-cog-search.png)

Von dieser Seite aus können Sie zur Indexanpassung springen.

   ![Überspringen des Schritts zu kognitiven Qualifikationen](media/search-get-started-portal/skip-cog-skill-step.png)

### <a name="5---set-index-attributes"></a>5\. Festlegen von Indexattributen

Auf der **Indexseite** sollte eine Liste von Feldern mit einem Datentyp sowie mehrere Kontrollkästchen zum Festlegen von Indexattributen aufgeführt sein. Der Assistent kann basierend auf Metadaten und durch Sampling der Quelldaten eine Felderliste erstellen. 

Sie können durch Klicken auf das Kontrollkästchen am Kopf einer Attributspalte mehrere Attribute gleichzeitig auswählen. Wählen Sie **Abrufbar** und **Durchsuchbar** für jedes Feld aus, das an eine Client-App zurückgegeben werden sollte und der Volltextsuche unterliegt. Sie werden feststellen, dass Ganzzahlen nicht mit der Volltext- oder Fuzzysuche durchsuchbar sind (Zahlen werden wörtlich ausgewertet und eignen sich häufig in Filtern).

Weitere Informationen finden Sie in den Beschreibungen der [Indexattribute](https://docs.microsoft.com/rest/api/searchservice/create-index#bkmk_indexAttrib) und [Sprachanalysetools](https://docs.microsoft.com/rest/api/searchservice/language-support). 

Nehmen Sie sich einen Moment Zeit, um Ihre Auswahl zu überprüfen. Wenn Sie den Assistenten ausführen, werden physische Datenstrukturen erstellt, und Sie können diese Felder nicht bearbeiten, ohne alle Objekte zu löschen und neu zu erstellen.

   ![Blobindexdefinition](media/search-howto-index-json/import-wizard-json-index.png)

### <a name="6---create-indexer"></a>6: Erstellen des Indexers

Wenn alle Angaben gemacht wurden, erstellt der Assistent drei unterschiedliche Objekte in Ihrem Suchdienst. Ein Datenquellenobjekt und ein Indexobjekt werden als benannte Ressourcen in Ihrem Azure Search-Dienst gespeichert. Mit dem letzten Schritt wird ein Indexerobjekt erstellt. Wenn der Indexer benannt wird, kann er als eigenständige Ressource existieren. Diese können Sie unabhängig vom Index und Datenquellenobjekt, die im selben Durchlauf des Assistenten erstellt wurden, planen und verwalten.

Falls Sie noch nicht mit Indexern vertraut sind: Ein *Indexer* ist eine Ressource in Azure Search, die eine externe Datenquelle nach durchsuchbarem Inhalt durchforstet. Die Ausgabe des **Datenimport**-Assistenten ist ein Indexer, der Ihre JSON-Datenquelle durchforstet, durchsuchbaren Inhalt extrahiert und diesen in einen Index im Azure Search-Dienst importiert.

   ![Definition des Blobindexers](media/search-howto-index-json/import-wizard-json-indexer.png)

Klicken Sie auf **OK**, um den Assistenten auszuführen und alle Objekte zu erstellen. Die Indizierung wird sofort durchgeführt.

Sie können den Datenimport auf den Portalseiten überwachen. Fortschrittsbenachrichtigungen geben den Indizierungsstatus an und wie viele Dokumente hochgeladen werden. 

Wenn die Indizierung abgeschlossen ist, können Sie den [Such-Explorer](search-explorer.md) zum Abfragen Ihres Indexes verwenden.

> [!NOTE]
> Wenn nicht die Daten angezeigt werden, die Sie erwarten, müssen Sie vielleicht weitere Attribute für weitere Felder festlegen. Löschen Sie den Index und Indexer, die Sie gerade erstellt haben, und führen Sie die Schritte des Assistenten erneut aus, wobei Sie Ihre Auswahl für Indexattribute in Schritt 5 ändern. 

<a name="json-indexer-rest"></a>

## <a name="use-rest-apis"></a>Verwenden von REST-APIs

Sie können die REST-API für die Indizierung von JSON-Blobs in einem dreiteiligen Workflow verwenden, der für alle Indexer in Azure Search gleich ist: Erstellen einer Datenquelle, eines Indexes und eines Indexers. Das Extrahieren von Daten aus Blobspeicher wird durchgeführt, wenn Sie die Anforderung „Indexer erstellen“ senden. Nachdem diese Anforderung abgeschlossen ist, verfügen Sie über einen Index, der abgefragt werden kann. 

Sie können den [REST-Beispielcode](#rest-example) am Ende dieses Abschnitts überprüfen, der zeigt, wie alle drei Objekte erstellt werden. Dieser Abschnitt enthält auch Informationen über [JSON-Analysemodi](#parsing-modes), [einzelne Blobs](#parsing-single-blobs), [JSON-Arrays](#parsing-arrays) und [geschachtelte Arrays](#nested-json-arrays).

Verwenden Sie für die Indizierung von JSON-Code auch [Postman](search-fiddler.md) und die REST-API, um diese Objekte zu erstellen:

+ [Index](https://docs.microsoft.com/rest/api/searchservice/create-index)
+ [Datenquelle](https://docs.microsoft.com/rest/api/searchservice/create-data-source)
+ [Indexer](https://docs.microsoft.com/rest/api/searchservice/create-indexer)

Die Reihenfolge der Vorgänge erfordert, dass Sie Objekte in der angegebenen Reihenfolge erstellen und aufrufen. Im Gegensatz zum Portalworkflow erfordert ein Codeansatz einen verfügbaren Index, um die JSON-Dokumente zu akzeptieren, die über die **Indexer erstellen**-Anforderung gesendet werden.

JSON-Blobs in Azure Blob Storage bestehen normalerweise entweder aus einem einzelnen JSON-Dokument oder einem JSON-„Array“. Mit dem Blobindexer in Azure Search können beide Fälle analysiert werden. Dies richtet sich danach, wie Sie den Parameter **parsingMode** in der Anforderung festlegen.

| JSON-Dokument | parsingMode | BESCHREIBUNG | Verfügbarkeit |
|--------------|-------------|--------------|--------------|
| Ein Dokument pro Blob | `json` | JSON-Blobs werden als einzelne Textblöcke analysiert. Jedes JSON-Blob wird zu einem einzelnen Azure Search-Dokument. | Allgemein sowohl in [REST](https://docs.microsoft.com/rest/api/searchservice/indexer-operations)-API als auch [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) SDK verfügbar. |
| Mehrere Dokumente pro Blob | `jsonArray` | Analysiert ein JSON-Array im Blob, und jedes Element des Arrays wird zu einem separaten Azure Search-Dokument.  | Allgemein sowohl in [REST](https://docs.microsoft.com/rest/api/searchservice/indexer-operations)-API als auch [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) SDK verfügbar. |
| Mehrere Dokumente pro Blob | `jsonLines` | Analysiert ein Blob, das mehrere durch einen Zeilenvorschub getrennte JSON-Entitäten (ein „Array“) enthält, wobei jede Entität ein separates Azure Search-Dokument wird. | Allgemein sowohl in [REST](https://docs.microsoft.com/rest/api/searchservice/indexer-operations)-API als auch [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) SDK verfügbar. |

### <a name="1---assemble-inputs-for-the-request"></a>1 – Zusammenstellen der Eingaben für die Anforderung

Für jede Anforderung müssen Sie den Dienstnamen und den Administratorschlüssel für Azure Search (im POST-Header) und den Namen des Speicherkontos sowie den Schlüssel für den Blobspeicher bereitstellen. Sie können [Postman](search-fiddler.md) zum Senden von HTTP-Anforderungen an Azure Search verwenden.

Kopieren Sie die folgenden vier Werten in den Editor, sodass Sie sie in eine Anforderung einfügen können:

+ Azure Search-Dienstname
+ Azure Search-Administratorschlüssel
+ Azure-Speicherkontoname
+ Azure-Speicherkontoschlüssel

Sie finden diese Werte im Portal:

1. Kopieren Sie in den Portalseiten für Azure Search die Suchdienst-URL aus der Seite „Übersicht“.

2. Klicken Sie im linken Navigationsbereich auf **Schlüssel**, und kopieren Sie dann entweder den primären oder sekundären Schlüssel (sie sind identisch).

3. Wechseln Sie zu den Portalseiten für Ihr Speicherkonto. Klicken Sie im linken Navigationsbereich unter **Einstellungen** auf die Option **Zugriffsschlüssel**. Diese Seite enthält sowohl den Kontonamen als auch den Schlüssel. Kopieren Sie den Namen des Speicherkontos und einen der Schlüssel in den Editor.

### <a name="2---create-a-data-source"></a>2 – Erstellen einer Datenquelle

Dieser Schritt umfasst die Bereitstellung der Datenquellen-Verbindungsinformationen, die vom Indexer verwendet werden. Die Datenquelle ist ein benanntes Objekt in Azure Search, das die Verbindungsinformationen beibehält. Mit dem Datenquellentyp, `azureblob`, wird bestimmt, welches Datenextraktionsverhalten vom Indexer aufgerufen wird. 

Geben Sie gültige Werte für die Platzhalter von Dienstnamen, Administratorschlüssel, Speicherkonto und Kontoschlüssel an.

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Search]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "optional, my-folder" }
    }   

### <a name="3---create-a-target-search-index"></a>3 – Erstellen eines Zielsuchindexes 

Indexer sind mit einem Indexschema gekoppelt. Bereiten Sie bei Verwendung der API (anstelle des Portals) vorher einen Index vor, damit Sie ihn für den Indexervorgang angeben können.

Der Index speichert durchsuchbaren Inhalt in Azure Search. Stellen Sie ein Schema bereit, mit dem die Felder in einem Dokument, Attribute und andere Konstrukte für die Suchoberfläche angegeben werden, um einen Index zu erstellen. Wenn Sie einen Index erstellen, der über dieselben Feldnamen und Datentypen wie die Quelle verfügt, entspricht der Indexer der Quelle und den Zielfeldern. Dadurch wird Ihnen die Arbeit erspart, die Felder explizit zuordnen zu müssen.

Im Anschluss sehen Sie ein Beispiel für die Anforderung [Index erstellen](https://docs.microsoft.com/rest/api/searchservice/create-index). Der Index verfügt über ein durchsuchbares `content`-Feld, in dem der aus den Blobs extrahierten Text gespeichert wird:   

    POST https://[service name].search.windows.net/indexes?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Search]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }


### <a name="4---configure-and-run-the-indexer"></a>4 – Konfigurieren und Ausführen des Indexers

Wie bei einem Index und einer Datenquelle handelt es sich auch beim Indexer um ein benanntes Objekt, das Sie erstellen und in einem Azure Search-Dienst wiederverwenden. Eine vollständige Anforderung zum Erstellen eines Indexers kann beispielsweise wie folgt aussehen:

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Search]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "json" } }
    }

Die Indexerkonfiguration befindet sich im Text der Anforderung. Sie erfordert eine Datenquelle und einen leeren Zielindex, der bereits in Azure Search vorhanden ist. 

Zeitplan und Parameter sind optional. Wenn Sie diese Angaben weglassen, wird der Indexer sofort ausgeführt, indem `json` als Analysemodus verwendet wird.

Dieser bestimmte Indexer enthält keine Feldzuordnungen. Innerhalb der Indexerdefinition können Sie **Feldzuordnungen** weglassen, wenn die Eigenschaften des JSON-Quelldokuments mit den Feldern Ihres Zielsuchindexes übereinstimmen. 


### <a name="rest-example"></a>Beispiel für REST

Dieser Abschnitt enthält eine Zusammenfassung aller Anforderungen, die zum Erstellen von Objekten verwendet werden. Eine Erläuterung der Komponententeile finden Sie in den vorherigen Abschnitten dieses Artikels.

### <a name="data-source-request"></a>Anforderung der Datenquelle

Alle Indexer erfordern ein Datenquellenobjekt, das Informationen zur Verbindung mit vorhandenen Daten bereitstellt. 

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Search]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "optional, my-folder" }
    }  


### <a name="index-request"></a>Indexanforderung

Alle Indexer erfordern einen Zielindex, der die Daten empfängt. Der Text der Anforderung definiert das aus Feldern bestehende Indexschema, das das gewünschte Verhalten in einem durchsuchbaren Index unterstützen soll. Dieser Index sollte leer sein, wenn Sie den Indexer ausführen. 

    POST https://[service name].search.windows.net/indexes?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Search]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }


### <a name="indexer-request"></a>Indexeranforderung

Diese Anforderung zeigt einen vollständig angegebenen Indexer. Er enthält Feldzuordnungen, die in den vorherigen Beispielen ausgelassen wurden. Denken Sie daran, dass „schedule“, „parameter“ und „fieldMappings“ optional sind, solange ein Standard verfügbar ist. Das Weglassen von „schedule“ bewirkt, dass der Indexer sofort ausgeführt wird. Das Weglassen von „parsingMode“ bewirkt, dass der Index die Standardeinstellung „json“ verwendet.

Das Erstellen des Indexers in Azure Search löst einen Datenimport aus. Er wird sofort ausgeführt, und anschließend nach einem Zeitplan, wenn Sie einen angegeben haben.

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Search]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "json" } },
      "fieldMappings" : [
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
        ]
    }


<a name="json-indexer-dotnet"></a>

## <a name="use-net-sdk"></a>Verwenden von .NET SDK

Das .NET SDK ist der REST-API vollständig gleichgestellt. Es wird empfohlen, dass Sie den vorherige Abschnitt zur REST-API genau lesen, um die Konzepte, den Workflow und die Anforderungen zu verstehen. Sie können sich anschließend auf die folgende .NET-API-Referenzdokumentation beziehen, um einen JSON-Indexer in verwalteten Code zu implementieren.

+ [microsoft.azure.search.models.datasource](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource?view=azure-dotnet)
+ [microsoft.azure.search.models.datasourcetype](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasourcetype?view=azure-dotnet) 
+ [microsoft.azure.search.models.index](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) 
+ [microsoft.azure.search.models.indexer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet)

<a name="parsing-modes"></a>

## <a name="parsing-modes"></a>Analysemodi

JSON-Blobs können mehrere Formulare annehmen. Der **parsingMode**-Parameter im JSON-Indexer bestimmt, wie JSON-Blobinhalt in einem Azure Search-Index analysiert und strukturiert wird:

| parsingMode | BESCHREIBUNG |
|-------------|-------------|
| `json`  | Indizieren Sie jedes Blob als einzelnes Dokument. Dies ist die Standardoption. |
| `jsonArray` | Wählen Sie diesen Modus aus, wenn Ihre Blobs aus JSON-Arrays bestehen und Sie möchten, dass jedes Element des Arrays in Azure Search zu einem einzelnen Dokument wird. |
|`jsonLines` | Wählen Sie diesen Modus aus, wenn Ihre Blobs aus mehreren durch einen Zeilenvorschub getrennte JSON-Entitäten bestehen, und jede Entität in Azure Search zu einem separaten Dokument werden muss. |

Sie können sich ein Dokument als einzelnes Element in Suchergebnissen vorstellen. Wenn Sie möchten, dass jedes Element im Array in den Suchergebnissen als unabhängiges Element angezeigt wird, verwenden Sie entsprechend die `jsonArray`- oder `jsonLines`-Option.

Innerhalb der Indexerdefinition können Sie optional [Feldzuordnungen](search-indexer-field-mappings.md) verwenden, um auszuwählen, welche Eigenschaften des JSON-Quelldokuments zum Auffüllen Ihres Zielsuchindex verwendet werden sollen. Für den `jsonArray`-Analysemodus gilt Folgendes: Wenn das Array als Eigenschaft auf niedrigerer Ebene vorhanden ist, können Sie einen Dokumentstamm festlegen, mit dem angegeben wird, wo das Array im Blob angeordnet ist.

> [!IMPORTANT]
> Bei Verwendung des `json`-, `jsonArray`- oder `jsonLines`-Analysemodus geht Azure Search davon aus, dass alle Blobs in Ihrer Datenquelle JSON enthalten. Wenn Sie eine Kombination von JSON- und Nicht-JSON-Blobs in der gleichen Datenquelle unterstützen müssen, informieren Sie uns auf unserer [UserVoice-Website](https://feedback.azure.com/forums/263029-azure-search).


<a name="parsing-single-blobs"></a>

## <a name="parse-single-json-blobs"></a>Analysieren von einzelnen JSON-Blobs

In der Standardeinstellung analysiert der [Azure Search-Blob-Indexer](search-howto-indexing-azure-blob-storage.md) JSON-Blobs als einen einzelnen Textblock. Häufig möchten Sie die Struktur Ihrer JSON-Dokumente beibehalten. Angenommen, Sie verwenden in Azure Blob Storage das folgende JSON-Dokument:

    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13",
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

Der Blobindexer löst das JSON-Dokument in ein einzelnes Azure Search-Dokument auf. Der Indexer lädt einen Index, indem die Elemente „text“, „datePublished“ und „tags“ aus der Quelle mit Zielindexfeldern abgeglichen werden, die den gleichen Namen und Typ aufweisen.

Wie schon erwähnt, sind Feldzuordnungen nicht erforderlich. Bei einem Index mit den Feldern „text“, „datePublished“ und „tags“ kann der Blobindexer die richtige Zuordnung ableiten, ohne dass in der Anforderung eine Feldzuordnung enthalten ist.

<a name="parsing-arrays"></a>

## <a name="parse-json-arrays"></a>Analysieren von JSON-Arrays

Alternativ können Sie auch die JSON-Arrayoption verwenden. Diese Option ist nützlich, wenn Blobs ein *Array mit wohlgeformten JSON-Objekten* enthalten und jedes Element zu einem separaten Azure Search-Dokument werden soll. Wenn beispielsweise das folgende JSON-Blob vorhanden ist, können Sie Ihren Azure Search-Index mit drei separaten Dokumenten auffüllen, die jeweils die Felder „id“ und „text“ aufweisen.  

    [
        { "id" : "1", "text" : "example 1" },
        { "id" : "2", "text" : "example 2" },
        { "id" : "3", "text" : "example 3" }
    ]

Für ein JSON-Array sollte die Indexerdefinition dem folgenden Beispiel ähneln. Beachten Sie, dass der parsingMode-Parameter den `jsonArray`-Parser angibt. Die einzigen beiden arrayspezifischen Anforderungen zum Indizieren von JSON-Blobs sind die Angabe des richtigen Parsers und die Eingabe der richtigen Daten.

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "jsonArray" } }
    }

Beachten Sie auch hierbei wieder, dass die Feldzuordnungen ausgelassen werden können. Angenommen, Sie verfügen über einen Index mit den gleichnamigen Feldern „id“ und „text“, dann kann der Blobindexer die richtige Zuordnung ohne explizite Feldzuordnungsliste ableiten.

<a name="nested-json-arrays"></a>

## <a name="parse-nested-arrays"></a>Analysieren geschachtelter Arrays
Damit JSON-Arrays geschachtelte Elemente enthalten, können Sie eine `documentRoot` angeben, um eine Struktur mit mehreren Ebenen anzugeben. Angenommen, Ihre Blobs sehen folgendermaßen aus:

    {
        "level1" : {
            "level2" : [
                { "id" : "1", "text" : "Use the documentRoot property" },
                { "id" : "2", "text" : "to pluck the array you want to index" },
                { "id" : "3", "text" : "even if it's nested inside the document" }  
            ]
        }
    }

Verwenden Sie diese Konfiguration, um das in der `level2`-Eigenschaft enthaltene Array zu indizieren:

    {
        "name" : "my-json-array-indexer",
        ... other indexer properties
        "parameters" : { "configuration" : { "parsingMode" : "jsonArray", "documentRoot" : "/level1/level2" } }
    }

## <a name="parse-blobs-separated-by-newlines"></a>Analysieren von Blobs, die durch einen Zeilenvorschub getrennt sind

Wenn das Blob mehrere JSON-Entitäten enthält, die durch einen Zeilenvorschub getrennt sind, und jedes Element ein separates Azure Search-Dokument werden soll, können Sie die JSON-Zeilen-Option auswählen. Beispielsweise können Sie bei dem folgenden Blob (in dem drei verschiedene JSON-Entitäten vorliegen) Ihren Azure Search-Index mit drei separaten Dokumenten auffüllen, die jeweils die Felder „id“ und „text“ aufweisen.

    { "id" : "1", "text" : "example 1" }
    { "id" : "2", "text" : "example 2" }
    { "id" : "3", "text" : "example 3" }

Für JSON-Zeilen sollte die Indexerdefinition dem folgenden Beispiel ähneln. Beachten Sie, dass der parsingMode-Parameter den `jsonLines`-Parser angibt. 

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "jsonLines" } }
    }

Beachten Sie auch hierbei wieder, dass die Feldzuordnungen ähnlich wie beim `jsonArray`-Analysemodus ausgelassen werden können.

## <a name="add-field-mappings"></a>Hinzufügen von Feldzuordnungen

Wenn Quell- und Zielfelder nicht genau aneinander ausgerichtet sind, können Sie im Anforderungstext einen Abschnitt für die Feldzuordnung definieren, um explizite Feld-zu-Feld-Zuordnungen zu ermöglichen.

Derzeit kann Azure Search JSON-Dokumente nicht willkürlich direkt indizieren, da nur primitive Datentypen, Zeichenfolgenarrays und GeoJSON-Punkte unterstützt werden. Sie können jedoch mit **Feldzuordnungen** Teile des JSON-Dokuments auswählen und diese in die Felder der obersten Ebene des Suchdokuments „heben“. Informationen zu den Grundlagen von Feldzuordnungen finden Sie unter [Feldzuordnungen in Azure Search-Indexern](search-indexer-field-mappings.md).

Wir verwenden wieder unser JSON-Beispieldokument:

    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13"
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

Angenommen, Sie haben einen Suchindex mit den folgenden Feldern: `text` vom Typ `Edm.String`, `date` vom Typ `Edm.DateTimeOffset` und `tags` vom Typ `Collection(Edm.String)`. Beachten Sie die Abweichung zwischen „datePublished“ in der Quelle und dem Feld `date` im Index. Um das JSON-Objekt in der gewünschten Form zuzuordnen, verwenden Sie die folgenden Feldzuordnungen:

    "fieldMappings" : [
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
      ]

Die Quellenfeldnamen in den Zuordnungen werden mit der [JSON-Zeiger](https://tools.ietf.org/html/rfc6901) -Notation angegeben. Sie beginnen mit einem Schrägstrich, um auf das Stammverzeichnis des JSON-Dokuments zu verweisen, und wählen dann mittels eines schrägstrichgetrennten Weiterleitungspfads den Pfad zur gewünschten Eigenschaft (auf beliebiger Schachtelungsebene) aus.

Sie können auch mit einem nullbasierten Index auf einzelne Arrayelemente verweisen. Um z. B. das erste Element des Arrays „tags“ aus dem obigen Beispiel auszuwählen, verwenden Sie eine Feldzuordnung wie folgt:

    { "sourceFieldName" : "/article/tags/0", "targetFieldName" : "firstTag" }

> [!NOTE]
> Wenn ein Quellfeldname in einer Feldzuordnung auf eine Eigenschaft verweist, die nicht in JSON vorhanden ist, wird die Zuordnung ohne Fehler übersprungen. Dies geschieht, damit Dokumente mit einem anderen Schema unterstützt werden können (ein häufiger Anwendungsfall). Da keine Überprüfung erfolgt, müssen Sie darauf achten, Tippfehler in Ihrer Feldzuordnungspezifikation zu vermeiden.
>
>

## <a name="see-also"></a>Weitere Informationen

+ [Indexer in Azure Search](search-indexer-overview.md)
+ [Indizieren von Azure Blob Storage mit Azure Search](search-howto-index-json-blobs.md)
+ [Indizierung von CSV-Blobs mit Azure Search-Blobindexer](search-howto-index-csv-blobs.md)
+ [Tutorial: Durchsuchen von teilweise strukturierten Daten in Azure Blob Storage](search-semi-structured-data.md)
