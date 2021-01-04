---
title: Durchsuchen von JSON-Blobs
titleSuffix: Azure Cognitive Search
description: Durchforsten von Azure-JSON-Blobs nach Textinhalten mithilfe des Blobindexers der kognitiven Azure-Suche. Indexer automatisieren die Datenerfassung für ausgewählte Datenquellen wie Azure Blob Storage.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/25/2020
ms.openlocfilehash: a3c44d667b6baaf16e109dfb88c22c16a1ea2ce1
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94697202"
---
# <a name="how-to-index-json-blobs-using-a-blob-indexer-in-azure-cognitive-search"></a>Indizieren von JSON-Blobs mit einem Blobindexer in der kognitiven Azure-Suche

In diesem Artikel wird beschrieben, wie Sie einen [Blobindexer](search-indexer-overview.md) der kognitiven Azure-Suche konfigurieren, um strukturierte Inhalte aus JSON-Dokumenten in Azure Blob Storage zu extrahieren und so vorzubereiten, dass er in der kognitiven Azure-Suche durchsucht werden kann. Dieser Workflow erstellt einen Index der kognitiven Azure-Suche und lädt ihn mit vorhandenem aus JSON-Blobs extrahiertem Text. 

Sie können das [Portal](#json-indexer-portal), [REST-APIs](#json-indexer-rest) oder das [.NET SDK](#json-indexer-dotnet) verwenden, um JSON-Inhalt zu indizieren. JSON-Dokumente befinden sich grundsätzlich in einem Blobcontainer eines Azure Storage-Kontos. Anleitungen zum Übertragen von JSON-Dokumenten von anderen Nicht-Azure-Plattformen finden Sie unter [Importieren von Daten in der kognitiven Azure-Suche](search-what-is-data-import.md).

JSON-Blobs in Azure Blob Storage bestehen normalerweise entweder aus einem einzelnen JSON-Dokument (Analysemodus `json`) oder einer Sammlung von JSON-Entitäten. Bei Sammlungen kann das Blob ein **Array** wohlgeformter JSON-Elemente enthalten (Analysemodus `jsonArray`). Blobs können auch aus mehreren einzelnen JSON-Entitäten zusammengesetzt sein, die durch einen Zeilenvorschub getrennt werden (Analysemodus `jsonLines`). Der **parsingMode**-Parameter in der Anforderung bestimmt die Ausgabestrukturen.

> [!NOTE]
> Weitere Informationen zum Indizieren mehrerer Suchdokumente aus einem einzelnen Blob finden Sie unter [1:n-Indizierung](search-howto-index-one-to-many-blobs.md).

<a name="json-indexer-portal"></a>

## <a name="use-the-portal"></a>Verwenden des Portals

Die einfachste Methode zum Indizieren von JSON-Dokumenten ist die Verwendung eines Assistenten im [Azure-Portal](https://portal.azure.com/). Der [**Datenimport**](search-import-data-portal.md)-Assistent kann durch das Analysieren von Metadaten im Azure-Blobcontainer einen Standardindex erstellen, Quellfelder Zielindexfeldern zuordnen und den Index in einem einzigen Vorgang laden. Je nach Größe und Komplexität der Quelldaten können Sie auch innerhalb von Minuten einen funktionsfähigen Volltextsuchindex erstellen.

Es wird empfohlen, die gleiche Region oder den gleichen Speicherort für Azure Cognitive Search und Azure Storage zu verwenden, um geringere Latenzzeiten zu erreichen und Bandbreitengebühren zu vermeiden.

### <a name="1---prepare-source-data"></a>1\. Vorbereiten von Quelldaten

[Melden Sie sich beim Azure-Portal an](https://portal.azure.com/), und [erstellen Sie einen Blobcontainer](../storage/blobs/storage-quickstart-blobs-portal.md), der Ihre Daten enthält. Die öffentliche Zugriffsebene kann auf jeden der gültigen Werte festgelegt werden.

Sie benötigen den Namen des Speicherkontos, den Containernamen und einen Zugriffsschlüssel zum Abrufen Ihrer Daten im **Datenimport**-Assistenten.

### <a name="2---start-import-data-wizard"></a>2\. Starten des Datenimport-Assistenten

Auf der Seite „Übersicht“ Ihres Suchdiensts können Sie über die Befehlsleiste [den Assistenten starten](search-import-data-portal.md).

   :::image type="content" source="media/search-import-data-portal/import-data-cmd2.png" alt-text="Befehl „Daten importieren“ im Portal" border="false":::

### <a name="3---set-the-data-source"></a>3\. Einrichten der Datenquelle

Auf der Seite **Datenquelle** muss die Quelle **Azure Blob Storage** lauten und folgende Spezifikationen aufweisen:

+ **Zu extrahierende Daten** muss *Inhalt und Metadaten* sein. Durch die Auswahl dieser Option kann der Assistent ein Indexschema ableiten und so die Felder für den Importvorgang zuordnen.
   
+ **Analysemodus** muss auf *JSON*, *JSON-Array* oder *JSON-Zeilen* festgelegt sein. 

  *JSON* definiert jedes Blob als einzelnes Suchdokument, das als unabhängiges Element in Suchergebnissen angezeigt wird. 

  *JSON-Array* ist für Blobs, die wohlgeformte JSON-Daten enthalten – die wohlgeformten JSON-Daten entsprechen einem Array von Objekten, oder verfügen über eine Eigenschaft, die ein Array von Objekten ist, und jedes Element soll als eigenständiges, unabhängiges Suchdokument dargestellt werden. Wenn Blobs komplex sind und Sie *JSON-Array* nicht auswählen, dann wird das gesamte Blob als einzelnes Dokument erfasst.

  *JSON-Zeilen* ist für Blobs, die aus mehreren durch einen Zeilenvorschub getrennte JSON-Entitäten bestehen, wobei jede Entität als eigenständiges unabhängiges Suchdokument angesehen werden soll. Wenn Blobs komplex sind und Sie den Analysemodus *JSON-Zeilen* nicht auswählen, dann wird das gesamte Blob als einzelnes Dokument erfasst.
   
+ **Speichercontainer** muss Ihr Speicherkonto sowie den Container angeben oder alternativ eine Verbindungszeichenfolge, die im Container aufgelöst wird. Sie können Verbindungszeichenfolgen auf der Seite des Blob-Dienst-Portals abrufen.

   :::image type="content" source="media/search-howto-index-json/import-wizard-json-data-source.png" alt-text="Definition der Blobdatenquelle" border="false":::

### <a name="4---skip-the-enrich-content-page-in-the-wizard"></a>Schritt 4: Überspringen der Seite „Inhalte anreichern“ im Assistenten

Das Hinzufügen kognitiver Qualifikationen (für Anreicherungen) ist keine Importanforderung. Wenn Sie der Indizierungspipeline keine [KI-Anreicherung hinzufügen](cognitive-search-concept-intro.md) möchten, können Sie diesen Schritt überspringen.

Klicken Sie auf die blauen Schaltflächen ganz unten auf der Seite (für „Weiter“ und „Überspringen“), um diesen Schritt zu überspringen.

### <a name="5---set-index-attributes"></a>5\. Festlegen von Indexattributen

Auf der **Indexseite** sollte eine Liste von Feldern mit einem Datentyp sowie mehrere Kontrollkästchen zum Festlegen von Indexattributen aufgeführt sein. Der Assistent kann basierend auf Metadaten und durch Sampling der Quelldaten eine Felderliste erstellen. 

Sie können durch Klicken auf das Kontrollkästchen am Kopf einer Attributspalte mehrere Attribute gleichzeitig auswählen. Wählen Sie **Abrufbar** und **Durchsuchbar** für jedes Feld aus, das an eine Client-App zurückgegeben werden sollte und der Volltextsuche unterliegt. Sie werden feststellen, dass Ganzzahlen nicht mit der Volltext- oder Fuzzysuche durchsuchbar sind (Zahlen werden wörtlich ausgewertet und eignen sich häufig in Filtern).

Weitere Informationen finden Sie in den Beschreibungen der [Indexattribute](/rest/api/searchservice/create-index#bkmk_indexAttrib) und [Sprachanalysetools](/rest/api/searchservice/language-support). 

Nehmen Sie sich einen Moment Zeit, um Ihre Auswahl zu überprüfen. Wenn Sie den Assistenten ausführen, werden physische Datenstrukturen erstellt, und Sie können diese Felder nicht bearbeiten, ohne alle Objekte zu löschen und neu zu erstellen.

   :::image type="content" source="media/search-howto-index-json/import-wizard-json-index.png" alt-text="Blobindexdefinition" border="false":::

### <a name="6---create-indexer"></a>6: Erstellen des Indexers

Wenn alle Angaben gemacht wurden, erstellt der Assistent drei unterschiedliche Objekte in Ihrem Suchdienst. Ein Datenquellenobjekt und ein Indexobjekt werden als benannte Ressourcen in Ihrem Dienst der kognitiven Azure-Suche gespeichert. Mit dem letzten Schritt wird ein Indexerobjekt erstellt. Wenn der Indexer benannt wird, kann er als eigenständige Ressource existieren. Diese können Sie unabhängig vom Index und Datenquellenobjekt, die im selben Durchlauf des Assistenten erstellt wurden, planen und verwalten.

Falls Sie noch nicht mit Indexern vertraut sind: Ein *Indexer* ist eine Ressource in der kognitiven Azure-Suche, die eine externe Datenquelle nach durchsuchbarem Inhalt durchforstet. Die Ausgabe des Assistenten **Daten importieren** ist ein Indexer, der die JSON-Datenquelle durchforstet, durchsuchbaren Inhalt extrahiert und diesen in einen Index in der kognitiven Azure-Suche importiert.

   :::image type="content" source="media/search-howto-index-json/import-wizard-json-indexer.png" alt-text="Definition des Blobindexers" border="false":::

Klicken Sie auf **OK**, um den Assistenten auszuführen und alle Objekte zu erstellen. Die Indizierung wird sofort durchgeführt.

Sie können den Datenimport auf den Portalseiten überwachen. Fortschrittsbenachrichtigungen geben den Indizierungsstatus an und wie viele Dokumente hochgeladen werden. 

Wenn die Indizierung abgeschlossen ist, können Sie den [Such-Explorer](search-explorer.md) zum Abfragen Ihres Indexes verwenden.

> [!NOTE]
> Wenn nicht die Daten angezeigt werden, die Sie erwarten, müssen Sie vielleicht weitere Attribute für weitere Felder festlegen. Löschen Sie den Index und Indexer, die Sie gerade erstellt haben, und führen Sie die Schritte des Assistenten erneut aus, wobei Sie Ihre Auswahl für Indexattribute in Schritt 5 ändern. 

<a name="json-indexer-rest"></a>

## <a name="use-rest-apis"></a>Verwenden von REST-APIs

Sie können die REST-API für die Indizierung von JSON-Blobs in einem dreiteiligen Workflow verwenden, der für alle Indexer in der kognitiven Azure-Suche gleich ist: Erstellen einer Datenquelle, eines Index und eines Indexers. Das Extrahieren von Daten aus Blobspeicher wird durchgeführt, wenn Sie die Anforderung „Indexer erstellen“ senden. Nachdem diese Anforderung abgeschlossen ist, verfügen Sie über einen Index, der abgefragt werden kann. 

Sie können den [REST-Beispielcode](#rest-example) am Ende dieses Abschnitts überprüfen, der zeigt, wie alle drei Objekte erstellt werden. Dieser Abschnitt enthält auch Informationen über [JSON-Analysemodi](#parsing-modes), [einzelne Blobs](#parsing-single-blobs), [JSON-Arrays](#parsing-arrays) und [geschachtelte Arrays](#nested-json-arrays).

Verwenden Sie für die Indizierung von JSON-Code auch [Postman oder Visual Studio Code](search-get-started-rest.md) und die REST-API, um diese Objekte zu erstellen:

+ [Index](/rest/api/searchservice/create-index)
+ [Datenquelle](/rest/api/searchservice/create-data-source)
+ [Indexer](/rest/api/searchservice/create-indexer)

Die Reihenfolge der Vorgänge erfordert, dass Sie Objekte in der angegebenen Reihenfolge erstellen und aufrufen. Im Gegensatz zum Portalworkflow erfordert ein Codeansatz einen verfügbaren Index, um die JSON-Dokumente zu akzeptieren, die über die **Indexer erstellen**-Anforderung gesendet werden.

JSON-Blobs in Azure Blob Storage bestehen normalerweise entweder aus einem einzelnen JSON-Dokument oder einem JSON-„Array“. Mit dem Blobindexer in der kognitiven Azure-Suche können beide Fälle analysiert werden. Dies richtet sich danach, wie Sie den **parsingMode**-Parameter in der Anforderung festlegen.

| JSON-Dokument | parsingMode | BESCHREIBUNG | Verfügbarkeit |
|--------------|-------------|--------------|--------------|
| Ein Dokument pro Blob | `json` | JSON-Blobs werden als einzelne Textblöcke analysiert. Jedes JSON-Blob wird zu einem einzelnen Dokument der kognitiven Azure-Suche. | Allgemein sowohl in [REST](/rest/api/searchservice/indexer-operations)-API als auch [.NET](/dotnet/api/azure.search.documents.indexes.models.searchindexer) SDK verfügbar. |
| Mehrere Dokumente pro Blob | `jsonArray` | Analysiert ein JSON-Array im Blob, wobei jedes Element des Arrays ein separates Dokument der kognitiven Azure-Suche wird.  | Allgemein sowohl in [REST](/rest/api/searchservice/indexer-operations)-API als auch [.NET](/dotnet/api/azure.search.documents.indexes.models.searchindexer) SDK verfügbar. |
| Mehrere Dokumente pro Blob | `jsonLines` | Analysiert ein Blob, das mehrere durch einen Zeilenvorschub getrennte JSON-Entitäten (ein „Array“) enthält, wobei jede Entität ein separates Dokument der kognitiven Azure-Suche wird. | Allgemein sowohl in [REST](/rest/api/searchservice/indexer-operations)-API als auch [.NET](/dotnet/api/azure.search.documents.indexes.models.searchindexer) SDK verfügbar. |

### <a name="1---assemble-inputs-for-the-request"></a>1 – Zusammenstellen der Eingaben für die Anforderung

Für jede Anforderung müssen Sie den Dienstnamen und den Administratorschlüssel für die kognitive Azure-Suche (im POST-Header) sowie den Namen des Speicherkontos und den Schlüssel für Blob Storage angeben. Sie können ein [Web-API-Testtool](search-get-started-rest.md) zum Senden von HTTP-Anforderungen an Azure Cognitive Search verwenden.

Kopieren Sie die folgenden vier Werten in den Editor, sodass Sie sie in eine Anforderung einfügen können:

+ Dienstname der kognitiven Azure-Suche
+ Administratorschlüssel der kognitiven Azure-Suche
+ Azure-Speicherkontoname
+ Azure-Speicherkontoschlüssel

Sie finden diese Werte im Portal:

1. Kopieren Sie in den Portalseiten für die kognitive Azure-Suche die Suchdienst-URL von der Seite „Übersicht“.

2. Klicken Sie im linken Navigationsbereich auf **Schlüssel**, und kopieren Sie dann entweder den primären oder sekundären Schlüssel (sie sind identisch).

3. Wechseln Sie zu den Portalseiten für Ihr Speicherkonto. Klicken Sie im linken Navigationsbereich unter **Einstellungen** auf die Option **Zugriffsschlüssel**. Diese Seite enthält sowohl den Kontonamen als auch den Schlüssel. Kopieren Sie den Namen des Speicherkontos und einen der Schlüssel in den Editor.

### <a name="2---create-a-data-source"></a>2 - Erstellen einer Datenquelle

Dieser Schritt umfasst die Bereitstellung der Datenquellen-Verbindungsinformationen, die vom Indexer verwendet werden. Die Datenquelle ist ein benanntes Objekt in der kognitiven Azure-Suche, das die Verbindungsinformationen beibehält. Mit dem Datenquellentyp, `azureblob`, wird bestimmt, welches Datenextraktionsverhalten vom Indexer aufgerufen wird. 

Geben Sie gültige Werte für die Platzhalter von Dienstnamen, Administratorschlüssel, Speicherkonto und Kontoschlüssel an.

```http
    POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "optional, my-folder" }
    }   
```

### <a name="3---create-a-target-search-index"></a>3 - Erstellen eines Zielsuchindex 

Indexer sind mit einem Indexschema gekoppelt. Bereiten Sie bei Verwendung der API (anstelle des Portals) vorher einen Index vor, damit Sie ihn für den Indexervorgang angeben können.

Der Index speichert durchsuchbaren Inhalt in der kognitiven Azure-Suche. Stellen Sie ein Schema bereit, mit dem die Felder in einem Dokument, Attribute und andere Konstrukte für die Suchoberfläche angegeben werden, um einen Index zu erstellen. Wenn Sie einen Index erstellen, der über dieselben Feldnamen und Datentypen wie die Quelle verfügt, entspricht der Indexer der Quelle und den Zielfeldern. Dadurch wird Ihnen die Arbeit erspart, die Felder explizit zuordnen zu müssen.

Im Anschluss sehen Sie ein Beispiel für die Anforderung [Index erstellen](/rest/api/searchservice/create-index). Der Index verfügt über ein durchsuchbares `content`-Feld, in dem der aus den Blobs extrahierten Text gespeichert wird:   

```http
    POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }
```


### <a name="4---configure-and-run-the-indexer"></a>4 - Konfigurieren und Ausführen des Indexers

Wie bei einem Index und einer Datenquelle handelt es sich auch beim Indexer um ein benanntes Objekt, das Sie erstellen und in einem Dienst der kognitiven Azure-Suche wiederverwenden. Eine vollständige Anforderung zum Erstellen eines Indexers kann beispielsweise wie folgt aussehen:

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "json" } }
    }
```

Die Indexerkonfiguration befindet sich im Text der Anforderung. Sie erfordert eine Datenquelle und einen leeren Zielindex, der bereits in der kognitiven Azure-Suche vorhanden ist. 

Zeitplan und Parameter sind optional. Wenn Sie diese Angaben weglassen, wird der Indexer sofort ausgeführt, indem `json` als Analysemodus verwendet wird.

Dieser bestimmte Indexer enthält keine Feldzuordnungen. Innerhalb der Indexerdefinition können Sie **Feldzuordnungen** weglassen, wenn die Eigenschaften des JSON-Quelldokuments mit den Feldern Ihres Zielsuchindexes übereinstimmen. 


### <a name="rest-example"></a>Beispiel für REST

Dieser Abschnitt enthält eine Zusammenfassung aller Anforderungen, die zum Erstellen von Objekten verwendet werden. Eine Erläuterung der Komponententeile finden Sie in den vorherigen Abschnitten dieses Artikels.

### <a name="data-source-request"></a>Anforderung der Datenquelle

Alle Indexer erfordern ein Datenquellenobjekt, das Informationen zur Verbindung mit vorhandenen Daten bereitstellt. 

```http
    POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "optional, my-folder" }
    }  
```

### <a name="index-request"></a>Indexanforderung

Alle Indexer erfordern einen Zielindex, der die Daten empfängt. Der Text der Anforderung definiert das aus Feldern bestehende Indexschema, das das gewünschte Verhalten in einem durchsuchbaren Index unterstützen soll. Dieser Index sollte leer sein, wenn Sie den Indexer ausführen. 

```http
    POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }
```

### <a name="indexer-request"></a>Indexeranforderung

Diese Anforderung zeigt einen vollständig angegebenen Indexer. Er enthält Feldzuordnungen, die in den vorherigen Beispielen ausgelassen wurden. Denken Sie daran, dass „schedule“, „parameter“ und „fieldMappings“ optional sind, solange ein Standard verfügbar ist. Das Weglassen von „schedule“ bewirkt, dass der Indexer sofort ausgeführt wird. Das Weglassen von „parsingMode“ bewirkt, dass der Index die Standardeinstellung „json“ verwendet.

Das Erstellen des Indexers in der kognitiven Azure-Suche löst einen Datenimport aus. Er wird sofort ausgeführt, und anschließend nach einem Zeitplan, wenn Sie einen angegeben haben.

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

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
```

<a name="json-indexer-dotnet"></a>

## <a name="use-net-sdk"></a>Verwenden von .NET SDK

Das .NET SDK ist der REST-API vollständig gleichgestellt. Es wird empfohlen, dass Sie den vorherige Abschnitt zur REST-API genau lesen, um die Konzepte, den Workflow und die Anforderungen zu verstehen. Sie können sich anschließend auf die folgende .NET-API-Referenzdokumentation beziehen, um einen JSON-Indexer in verwalteten Code zu implementieren.

+ [azure.search.documents.indexes.models.searchindexerdatasourceconnection](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourceconnection)
+ [azure.search.documents.indexes.models.searchindexerdatasourcetype](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourcetype) 
+ [azure.search.documents.indexes.models.searchindex](/dotnet/api/azure.search.documents.indexes.models.searchindex) 
+ [azure.search.documents.indexes.models.searchindexer](/dotnet/api/azure.search.documents.indexes.models.searchindexer)

<a name="parsing-modes"></a>

## <a name="parsing-modes"></a>Analysemodi

JSON-Blobs können mehrere Formulare annehmen. Der **parsingMode**-Parameter im JSON-Indexer bestimmt, wie JSON-Blobinhalt in einem Index der kognitiven Azure-Suche analysiert und strukturiert wird:

| parsingMode | BESCHREIBUNG |
|-------------|-------------|
| `json`  | Indizieren Sie jedes Blob als einzelnes Dokument. Dies ist die Standardoption. |
| `jsonArray` | Wählen Sie diesen Modus aus, wenn die Blobs aus JSON-Arrays bestehen und jedes Element des Arrays in der kognitiven Azure-Suche ein separates Dokument werden soll. |
|`jsonLines` | Wählen Sie diesen Modus aus, wenn die Blobs aus mehreren durch einen Zeilenvorschub getrennten JSON-Entitäten bestehen und jede Entität in der kognitiven Azure-Suche ein separates Dokument werden soll. |

Sie können sich ein Dokument als einzelnes Element in Suchergebnissen vorstellen. Wenn Sie möchten, dass jedes Element im Array in den Suchergebnissen als unabhängiges Element angezeigt wird, verwenden Sie entsprechend die `jsonArray`- oder `jsonLines`-Option.

Innerhalb der Indexerdefinition können Sie optional [Feldzuordnungen](search-indexer-field-mappings.md) verwenden, um auszuwählen, welche Eigenschaften des JSON-Quelldokuments zum Auffüllen Ihres Zielsuchindex verwendet werden sollen. Für den `jsonArray`-Analysemodus gilt Folgendes: Wenn das Array als Eigenschaft auf niedrigerer Ebene vorhanden ist, können Sie einen Dokumentstamm festlegen, mit dem angegeben wird, wo das Array im Blob angeordnet ist.

> [!IMPORTANT]
> Bei Verwendung des Analysemodus `json`, `jsonArray` oder `jsonLines` wird in der kognitiven Azure-Suche davon ausgegangen, dass alle Blobs in der Datenquelle JSON enthalten. Wenn Sie eine Kombination von JSON- und Nicht-JSON-Blobs in der gleichen Datenquelle unterstützen müssen, informieren Sie uns auf unserer [UserVoice-Website](https://feedback.azure.com/forums/263029-azure-search).


<a name="parsing-single-blobs"></a>

## <a name="parse-single-json-blobs"></a>Analysieren von einzelnen JSON-Blobs

Standardmäßig analysiert der [Blobindexer der kognitiven Azure-Suche](search-howto-indexing-azure-blob-storage.md) JSON-Blobs als einzelnen Textblock. Häufig möchten Sie die Struktur Ihrer JSON-Dokumente beibehalten. Angenommen, Sie verwenden in Azure Blob Storage das folgende JSON-Dokument:

```http
    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13",
            "tags" : [ "search", "storage", "howto" ]    
        }
    }
```

Der Blobindexer löst das JSON-Dokument in ein einzelnes Dokument der kognitiven Azure-Suche auf. Der Indexer lädt einen Index, indem die Elemente „text“, „datePublished“ und „tags“ aus der Quelle mit Zielindexfeldern abgeglichen werden, die den gleichen Namen und Typ aufweisen.

Wie schon erwähnt, sind Feldzuordnungen nicht erforderlich. Bei einem Index mit den Feldern „text“, „datePublished“ und „tags“ kann der Blobindexer die richtige Zuordnung ableiten, ohne dass in der Anforderung eine Feldzuordnung enthalten ist.

<a name="parsing-arrays"></a>

## <a name="parse-json-arrays"></a>Analysieren von JSON-Arrays

Alternativ können Sie auch die JSON-Arrayoption verwenden. Diese Option ist nützlich, wenn Blobs ein *Array wohlgeformter JSON-Objekte* enthalten und jedes Element ein separates Dokument der kognitiven Azure-Suche werden soll. Bei dem folgenden JSON-Blob können Sie beispielsweise den Index der kognitiven Azure-Suche mit drei separaten Dokumenten auffüllen, die jeweils die Felder „id“ und „text“ enthalten.  

```text
    [
        { "id" : "1", "text" : "example 1" },
        { "id" : "2", "text" : "example 2" },
        { "id" : "3", "text" : "example 3" }
    ]
```

Für ein JSON-Array sollte die Indexerdefinition dem folgenden Beispiel ähneln. Beachten Sie, dass der parsingMode-Parameter den `jsonArray`-Parser angibt. Die einzigen beiden arrayspezifischen Anforderungen zum Indizieren von JSON-Blobs sind die Angabe des richtigen Parsers und die Eingabe der richtigen Daten.

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "jsonArray" } }
    }
```

Beachten Sie auch hierbei wieder, dass die Feldzuordnungen ausgelassen werden können. Angenommen, Sie verfügen über einen Index mit den gleichnamigen Feldern „id“ und „text“, dann kann der Blobindexer die richtige Zuordnung ohne explizite Feldzuordnungsliste ableiten.

<a name="nested-json-arrays"></a>

## <a name="parse-nested-arrays"></a>Analysieren geschachtelter Arrays
Damit JSON-Arrays geschachtelte Elemente enthalten, können Sie eine `documentRoot` angeben, um eine Struktur mit mehreren Ebenen anzugeben. Angenommen, Ihre Blobs sehen folgendermaßen aus:

```http
    {
        "level1" : {
            "level2" : [
                { "id" : "1", "text" : "Use the documentRoot property" },
                { "id" : "2", "text" : "to pluck the array you want to index" },
                { "id" : "3", "text" : "even if it's nested inside the document" }  
            ]
        }
    }
```

Verwenden Sie diese Konfiguration, um das in der `level2`-Eigenschaft enthaltene Array zu indizieren:

```http
    {
        "name" : "my-json-array-indexer",
        ... other indexer properties
        "parameters" : { "configuration" : { "parsingMode" : "jsonArray", "documentRoot" : "/level1/level2" } }
    }
```

## <a name="parse-blobs-separated-by-newlines"></a>Analysieren von Blobs, die durch einen Zeilenvorschub getrennt sind

Wenn das Blob mehrere JSON-Entitäten enthält, die durch einen Zeilenvorschub getrennt sind, und jedes Element ein separates Dokument der kognitiven Azure-Suche werden soll, können Sie die JSON-Zeilen-Option auswählen. Beispielsweise können Sie bei dem folgenden Blob (in dem drei verschiedene JSON-Entitäten vorliegen) den Index der kognitiven Azure-Suche mit drei separaten Dokumenten auffüllen, die jeweils die Felder „id“ und „text“ enthalten.

```text
{ "id" : "1", "text" : "example 1" }
{ "id" : "2", "text" : "example 2" }
{ "id" : "3", "text" : "example 3" }
```

Für JSON-Zeilen sollte die Indexerdefinition dem folgenden Beispiel ähneln. Beachten Sie, dass der parsingMode-Parameter den `jsonLines`-Parser angibt. 

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "jsonLines" } }
    }
```

Beachten Sie auch hierbei wieder, dass die Feldzuordnungen ähnlich wie beim `jsonArray`-Analysemodus ausgelassen werden können.

## <a name="add-field-mappings"></a>Hinzufügen von Feldzuordnungen

Wenn Quell- und Zielfelder nicht genau aneinander ausgerichtet sind, können Sie im Anforderungstext einen Abschnitt für die Feldzuordnung definieren, um explizite Feld-zu-Feld-Zuordnungen zu ermöglichen.

Derzeit können in der kognitiven Azure-Suche nicht alle JSON-Dokumente direkt indiziert werden, da nur primitive Datentypen, Zeichenfolgenarrays und GeoJSON-Punkte unterstützt werden. Sie können jedoch mit **Feldzuordnungen** Teile des JSON-Dokuments auswählen und diese in die Felder der obersten Ebene des Suchdokuments „heben“. Informationen zu den Grundlagen von Feldzuordnungen finden Sie unter [Feldzuordnungen in Indexern der kognitiven Azure-Suche](search-indexer-field-mappings.md).

Wir verwenden wieder unser JSON-Beispieldokument:

```http
    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13"
            "tags" : [ "search", "storage", "howto" ]    
        }
    }
```

Angenommen, Sie haben einen Suchindex mit den folgenden Feldern: `text` vom Typ `Edm.String`, `date` vom Typ `Edm.DateTimeOffset` und `tags` vom Typ `Collection(Edm.String)`. Beachten Sie die Abweichung zwischen „datePublished“ in der Quelle und dem Feld `date` im Index. Um das JSON-Objekt in der gewünschten Form zuzuordnen, verwenden Sie die folgenden Feldzuordnungen:

```http
    "fieldMappings" : [
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
      ]
```

Die Quellenfeldnamen in den Zuordnungen werden mit der [JSON-Zeiger](https://tools.ietf.org/html/rfc6901) -Notation angegeben. Sie beginnen mit einem Schrägstrich, um auf das Stammverzeichnis des JSON-Dokuments zu verweisen, und wählen dann mittels eines schrägstrichgetrennten Weiterleitungspfads den Pfad zur gewünschten Eigenschaft (auf beliebiger Schachtelungsebene) aus.

Sie können auch mit einem nullbasierten Index auf einzelne Arrayelemente verweisen. Um z. B. das erste Element des Arrays „tags“ aus dem obigen Beispiel auszuwählen, verwenden Sie eine Feldzuordnung wie folgt:

```http
    { "sourceFieldName" : "/article/tags/0", "targetFieldName" : "firstTag" }
```

> [!NOTE]
> Wenn ein Quellfeldname in einer Feldzuordnung auf eine Eigenschaft verweist, die nicht in JSON vorhanden ist, wird die Zuordnung ohne Fehler übersprungen. Dies geschieht, damit Dokumente mit einem anderen Schema unterstützt werden können (ein häufiger Anwendungsfall). Da keine Überprüfung erfolgt, müssen Sie darauf achten, Tippfehler in Ihrer Feldzuordnungspezifikation zu vermeiden.
>

## <a name="help-us-make-azure-cognitive-search-better"></a>Helfen Sie uns bei der Verbesserung der kognitiven Azure-Suche
Wenn Sie sich Features wünschen oder Verbesserungsvorschläge haben, stimmen Sie unter [UserVoice](https://feedback.azure.com/forums/263029-azure-search/) dafür ab. Wenn Sie Hilfe bei der Verwendung des vorhandenen Features benötigen, veröffentlichen Sie Ihre Frage in [Stack Overflow](https://stackoverflow.microsoft.com/questions/tagged/18870).

## <a name="see-also"></a>Weitere Informationen

+ [Indexer in der kognitiven Azure-Suche](search-indexer-overview.md)
+ [Indizieren von Azure Blob Storage mit der kognitiven Azure-Suche](search-howto-index-json-blobs.md)
+ [Indizieren von CSV-Blobs mit einem Blobindexer der kognitiven Azure-Suche](search-howto-index-csv-blobs.md)
+ [Tutorial: Durchsuchen von teilweise strukturierten Daten in Azure Blob Storage](search-semi-structured-data.md)