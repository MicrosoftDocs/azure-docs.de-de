---
title: 'Tutorial: Python und KI über Azure-Blobs'
titleSuffix: Azure Cognitive Search
description: Hier finden Sie ein ausführliches Beispiel für die Textextraktion und die Verarbeitung natürlicher Sprache auf der Grundlage von Blobspeicherinhalten mit einem Jupyter Python-Notebook und den Azure Cognitive Search-REST-APIs.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.devlang: python
ms.topic: tutorial
ms.date: 09/25/2020
ms.custom: devx-track-python
ms.openlocfilehash: ea1cc022cbea7dbf3d1fa12cb83cfe3084b28560
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92788080"
---
# <a name="tutorial-use-python-and-ai-to-generate-searchable-content-from-azure-blobs"></a>Tutorial: Verwenden von Python und KI zum Generieren von durchsuchbarem Inhalt über Azure-Blobs

Wenn Sie in Azure-Blobspeicher über unstrukturierten Text oder Bilder verfügen, können Sie mithilfe einer [KI-Anreicherungspipeline](cognitive-search-concept-intro.md) Informationen extrahieren und neue Inhalte erstellen, die für die Volltextsuche oder in Knowledge Mining-Szenarien verwendet werden können. Eine Pipeline kann Bilder verarbeiten. In diesem Python-Tutorial steht aber Text im Mittelpunkt, um unter Verwendung der Spracherkennung und der Verarbeitung natürlicher Sprache neue Felder zu erstellen, die Sie in Abfragen, Facetten und Filtern nutzen können.

In diesem Tutorial werden Python und die [Azure Cognitive Search-REST-APIs](/rest/api/searchservice/) verwendet, um folgende Aufgaben durchzuführen:

> [!div class="checklist"]
> * Beginnen Sie mit vollständigen Dokumenten (unstrukturierter Text, beispielsweise im PDF-, HTML-, DOCX- oder PPTX-Format) in Azure Blob Storage.
> * Erstellen Sie eine Pipeline, die Text extrahiert und Sprache, Entitäten sowie Schlüsselbegriffe erkennt.
> * Definieren Sie einen Index zum Speichern der Ausgabe (Rohinhalte sowie von der Pipeline generierte Name/Wert-Paare).
> * Führen Sie die Pipeline aus, um Transformationen und Analysen zu starten und den Index zu erstellen und zu laden.
> * Erkunden Sie Ergebnisse per Volltextsuche und umfangreicher Abfragesyntax.

Sollten Sie über kein Azure-Abonnement verfügen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

+ [Azure Storage (in englischer Sprache)](https://azure.microsoft.com/services/storage/)
+ [Anaconda 3.7](https://www.anaconda.com/distribution/#download-section)
+ [Neuer](search-create-service-portal.md) oder [bereits vorhandener](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) Suchdienst 

> [!Note]
> In diesem Tutorial können Sie den kostenlosen Dienst verwenden. Der kostenlose Suchdienst ist auf drei Indizes, drei Indexer und drei Datenquellen beschränkt. In diesem Tutorial wird davon jeweils eine Instanz erstellt. Vergewissern Sie sich zunächst, dass Ihr Dienst über genügend freie Kapazität für die neuen Ressourcen verfügt.

## <a name="download-files"></a>Herunterladen von Dateien

1. Öffnen Sie [diesen OneDrive-Ordner](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4), und klicken Sie links oben auf **Herunterladen** , um die Dateien auf Ihren Computer zu kopieren. 

1. Klicken Sie mit der rechten Maustaste auf die ZIP-Datei, und wählen Sie **Alle extrahieren** aus. Es stehen 14 Dateien verschiedener Art zur Verfügung. In dieser Übung werden sieben davon verwendet.

## <a name="1---create-services"></a>1\. Erstellen der Dienste

In diesem Tutorial werden Azure Cognitive Search für Indizierungsvorgänge und Abfragen, Cognitive Services auf dem Back-End für die KI-Anreicherung und Azure Blob Storage für die Datenbereitstellung verwendet. Dieses Tutorial bleibt unter der kostenlosen Zuteilung von 20 Transaktionen pro Indexer pro Tag für Cognitive Services. Aus diesem Grund müssen Sie nur den Such- und den Speicherdienst erstellen.

Erstellen Sie diese beiden Dienste nach Möglichkeit in derselben Region und Ressourcengruppe, um eine möglichst große Nähe zu erreichen und die Verwaltung zu vereinfachen. In der Praxis kann sich Ihr Azure Storage-Konto in einer beliebigen Region befinden.

### <a name="start-with-azure-storage"></a>Azure Storage

1. [Melden Sie sich beim Azure-Portal](https://portal.azure.com/) an, und klicken Sie auf **+ Ressource erstellen** .

1. Suchen Sie nach *Speicherkonto* , und wählen Sie das Speicherkonto-Angebot von Microsoft aus.

   :::image type="content" source="media/cognitive-search-tutorial-blob/storage-account.png" alt-text="Erstellen eines Speicherkontos" border="false":::

1. Auf der Registerkarte „Grundlagen“ sind folgende Angaben erforderlich. Übernehmen Sie bei allen anderen Optionen die Standardeinstellungen.

   + **Ressourcengruppe** . Sie können entweder eine vorhandene Ressourcengruppe auswählen oder eine neue Ressourcengruppe erstellen. Verwenden Sie jedoch für alle Dienste die gleiche Gruppe, um die Dienste gemeinsam verwalten zu können.

   + **Speicherkontoname** : Falls Sie über mehrere Ressourcen des gleichen Typs verfügen, geben Sie zur Unterscheidung den Typ und die Region an (Beispiel: *blobstoragewestus* ). 

   + **Standort** . Wählen Sie nach Möglichkeit den gleichen Standort aus, der auch für Azure Cognitive Search und Cognitive Services verwendet wird. Bei Verwendung eines einzelnen Standorts fallen keine Bandbreitengebühren an.

   + **Kontoart** : Verwenden Sie die Standardeinstellung *StorageV2 (allgemein, Version 2)* .

1. Klicken Sie zum Erstellen des Diensts auf **Überprüfen + erstellen** .

1. Klicken Sie nach der Erstellung auf **Go to the resource** (Zur Ressource), um die Übersichtsseite zu öffnen.

1. Klicken Sie auf den Dienst **Blobs** .

1. Klicken Sie auf **+ Container** , um einen Container zu erstellen, und nennen Sie ihn *cog-search-demo* .

1. Wählen Sie *cog-search-demo* aus, und klicken Sie auf **Hochladen** , um den Ordner zu öffnen, in dem Sie die Downloaddateien gespeichert haben. Wählen Sie alle Dateien aus, bei denen es sich nicht um Bilddateien handelt. Es müssen sieben Dateien sein. Klicken Sie auf **OK** , um die Dateien hochzuladen.

   :::image type="content" source="media/cognitive-search-tutorial-blob/sample-files.png" alt-text="Erstellen eines Speicherkontos" border="false":::

1. Rufen Sie eine Verbindungszeichenfolge ab, bevor Sie Azure Storage verlassen, um in Azure Cognitive Search eine Verbindung herstellen zu können. 

   1. Kehren Sie zur Übersichtsseite Ihres Speicherkontos zurück. (Wir haben *blobstoragewestus* als Beispiel verwendet.) 
   
   1. Wählen Sie im linken Navigationsbereich die Option **Zugriffsschlüssel** aus, und kopieren Sie eine der Verbindungszeichenfolgen. 

   Die Verbindungszeichenfolge ist eine URL, die in etwa wie folgt aussieht:

      ```http
      DefaultEndpointsProtocol=https;AccountName=<storageaccountname>;AccountKey=<your account key>;EndpointSuffix=core.windows.net
      ```

1. Speichern Sie die Verbindungszeichenfolge im Editor. Sie wird später bei der Einrichtung der Datenquellenverbindung benötigt.

### <a name="cognitive-services"></a>Cognitive Services

Die KI-Anreicherung basiert auf Cognitive Services (einschließlich Textanalyse und maschinellem Sehen für die Verarbeitung von natürlicher Sprache und Bildern). Bei der Erstellung eines echten Prototyps oder Projekts würden Sie an dieser Stelle Cognitive Services bereitstellen (in der gleichen Region wie Azure Cognitive Search), um eine Verknüpfung mit Indizierungsvorgängen zu ermöglichen.

Da in diesem Tutorial nur 7 Transaktionen verwendet werden, können Sie die Ressourcenbereitstellung überspringen, weil Azure Cognitive Search eine Verbindung mit Cognitive Services für 20 kostenlose Transaktionen pro Indexer-Ausführung herstellen kann. Die kostenlose Speicherbelegung ist ausreichend. Planen Sie bei umfangreicheren Projekten die Bereitstellung von Cognitive Services im S0-Tarif (nutzungsbasierte Bezahlung). Weitere Informationen finden Sie unter [Anfügen einer Cognitive Services-Ressource an eine Qualifikationsgruppe in Azure Search](cognitive-search-attach-cognitive-services.md).

### <a name="azure-cognitive-search"></a>Azure Cognitive Search

Die dritte Komponente, Azure Cognitive Search, können Sie [im Portal erstellen](search-create-service-portal.md). Im Rahmen dieser exemplarischen Vorgehensweise können Sie den Free-Tarif verwenden. 

Erfassen Sie genau wie bei Azure Blob Storage den Zugriffsschlüssel. Wenn Sie später mit der Strukturierung von Anforderungen beginnen, müssen Sie den Endpunkt und den Administrator-API-Schlüssel für die Authentifizierung der jeweiligen Anforderung angeben.

### <a name="get-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Abrufen eines Administrator-API-Schlüssels und einer URL für Azure Cognitive Search

1. [Melden Sie sich beim Azure-Portal an](https://portal.azure.com/), und rufen Sie auf der Seite **Übersicht** Ihres Suchdiensts den Namen Ihres Suchdiensts ab. Sie können den Dienstnamen anhand der Endpunkt-URL überprüfen. Wenn Ihre Endpunkt-URL z.B. `https://mydemo.search.windows.net` lautet, ist der Name des Diensts `mydemo`.

2. Rufen Sie unter **Einstellungen** > **Schlüssel** einen Administratorschlüssel ab, um Vollzugriff auf den Dienst zu erhalten. Es gibt zwei austauschbare Administratorschlüssel – diese wurden zum Zweck der Geschäftskontinuität bereitgestellt, falls Sie einen Rollover für einen Schlüssel durchführen müssen. Für Anforderungen zum Hinzufügen, Ändern und Löschen von Objekten können Sie den primären oder den sekundären Schlüssel verwenden.

   Rufen Sie auch den Abfrageschlüssel ab. Es empfiehlt sich, Abfrageanforderungen mit schreibgeschütztem Zugriff auszugeben.

   :::image type="content" source="media/search-get-started-javascript/service-name-and-keys.png" alt-text="Erstellen eines Speicherkontos" border="false":::

Für alle an Ihren Dienst gesendeten Anforderungen ist ein API-Schlüssel im Header erforderlich. Ein gültiger Schlüssel stellt anforderungsbasiert eine Vertrauensstellung her zwischen der Anwendung, die die Anforderung sendet, und dem Dienst, der sie verarbeitet.

## <a name="2---start-a-notebook"></a>2\. Starten eines Notebooks

Verwenden Sie zum Erstellen des Notebooks die folgende Anleitung, oder laden Sie aus dem [Repository „Azure-Search-python-samples“](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Tutorial-AI-Enrichment) ein fertiges Notebook herunter.

Verwenden Sie Anaconda Navigator zum Starten von Jupyter Notebook, und erstellen Sie ein neues Python 3-Notebook.

Führen Sie in Ihrem Notebook das folgende Skript aus, um die Bibliotheken zum Verwenden von JSON und Formulieren von HTTP-Anforderungen zu laden.

```python
import json
import requests
from pprint import pprint
```

Definieren Sie in demselben Notebook die Namen für die Datenquelle, den Index, den Indexer und die Qualifikationsgruppe. Führen Sie das folgende Skript aus, um die Namen für dieses Tutorial einzurichten.

```python
# Define the names for the data source, skillset, index and indexer
datasource_name = "cogsrch-py-datasource"
skillset_name = "cogsrch-py-skillset"
index_name = "cogsrch-py-index"
indexer_name = "cogsrch-py-indexer"
```

Ersetzen Sie im folgenden Skript die Platzhalter für Ihren Suchdienst (YOUR-SEARCH-SERVICE-NAME) und Ihren Admin-API-Schlüssel (YOUR-ADMIN-API-KEY). Führen Sie dann das Skript aus, um den Suchdienstendpunkt einzurichten.

```python
# Setup the endpoint
endpoint = 'https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/'
headers = {'Content-Type': 'application/json',
           'api-key': '<YOUR-ADMIN-API-KEY>'}
params = {
    'api-version': '2020-06-30'
}
```

## <a name="3---create-the-pipeline"></a>3\. Erstellen der Pipeline

In Azure Cognitive Search erfolgt die KI-Verarbeitung während der Indizierung (oder Datenerfassung). In diesem Teil der exemplarischen Vorgehensweise werden vier Objekte erstellt: Datenquelle, Indexdefinition, Skillset und Indexer. 

### <a name="step-1-create-a-data-source"></a>Schritt 1: Erstellen einer Datenquelle

Ein [Datenquellenobjekt](/rest/api/searchservice/create-data-source) stellt die Verbindungszeichenfolge für den Blobcontainer mit den Dateien bereit.

Ersetzen Sie im folgenden Skript den Platzhalter YOUR-BLOB-RESOURCE-CONNECTION-STRING durch die Verbindungszeichenfolge für das im vorherigen Schritt erstellte Blob. Ersetzen Sie den Platzhaltertext für den Container. Führen Sie dann das Skript aus, um eine Datenquelle mit dem Namen `cogsrch-py-datasource` zu erstellen.

```python
# Create a data source
datasourceConnectionString = "<YOUR-BLOB-RESOURCE-CONNECTION-STRING>"
datasource_payload = {
    "name": datasource_name,
    "description": "Demo files to demonstrate cognitive search capabilities.",
    "type": "azureblob",
    "credentials": {
        "connectionString": datasourceConnectionString
    },
    "container": {
        "name": "<YOUR-BLOB-CONTAINER-NAME>"
    }
}
r = requests.put(endpoint + "/datasources/" + datasource_name,
                 data=json.dumps(datasource_payload), headers=headers, params=params)
print(r.status_code)
```

Die Anforderung sollte die erfolgreiche Ausführung durch Rückgabe von Statuscode 201 bestätigen.

Überprüfen Sie im Azure-Portal auf der Dashboardseite des Suchdiensts, ob „cogsrch-py-datasource“ in der Liste **Datenquellen** aufgeführt ist. Klicken Sie auf **Aktualisieren** , um die Seite zu aktualisieren.

:::image type="content" source="media/cognitive-search-tutorial-blob-python/py-data-source-tile.png" alt-text="Erstellen eines Speicherkontos" border="false":::

### <a name="step-2-create-a-skillset"></a>Schritt 2: Erstellen eines Skillsets

In diesem Schritt definieren Sie eine Reihe von Anreicherungsschritten, die auf Ihre Daten angewandt werden sollen. Jeder Anreicherungsschritt wird als *Qualifikation* und der Satz von Anreicherungsschritten als *Qualifikationsgruppe* bezeichnet. In diesem Tutorial werden für die Qualifikationsgruppe [integrierte kognitive Qualifikationen](cognitive-search-predefined-skills.md) verwendet:

+ [Entitätserkennung](cognitive-search-skill-entity-recognition.md), um die Namen von Organisationen aus Inhalten im Blobcontainer zu extrahieren.

+ [Spracherkennung](cognitive-search-skill-language-detection.md), um die Sprache der Inhalte zu bestimmen.

+ [Text unterteilen](cognitive-search-skill-textsplit.md), um große Inhalte vor dem Aufrufen der Schlüsselbegriffserkennungs-Qualifikation in kleinere Stücke aufzuteilen. Die Schlüsselbegriffserkennung akzeptiert Eingaben von 50.000 Zeichen oder weniger. Für einige der Beispieldateien ist eine Aufteilung erforderlich, um diesen Grenzwert zu erfüllen.

+ [Schlüsselbegriffserkennung](cognitive-search-skill-keyphrases.md), um die wichtigsten Schlüsselbegriffe herauszuziehen. 

Führen Sie das folgende Skript aus, um eine Qualifikationsgruppe mit dem Namen `cogsrch-py-skillset` zu erstellen.

```python
# Create a skillset
skillset_payload = {
    "name": skillset_name,
    "description":
    "Extract entities, detect language and extract key-phrases",
    "skills":
    [
        {
            "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
            "categories": ["Organization"],
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
            "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
            "inputs": [
                {
                    "name": "text", 
                    "source": "/document/content"
                }
            ],
            "outputs": [
                {
                    "name": "languageCode",
                    "targetName": "languageCode"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.SplitSkill",
            "textSplitMode": "pages",
            "maximumPageLength": 4000,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/content"
                },
                {
                    "name": "languageCode",
                    "source": "/document/languageCode"
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
            "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
            "context": "/document/pages/*",
            "inputs": [
                {
                    "name": "text", 
                    "source": "/document/pages/*"
                },
                {
                    "name": "languageCode", 
                    "source": "/document/languageCode"
                }
            ],
            "outputs": [
                {
                    "name": "keyPhrases",
                    "targetName": "keyPhrases"
                }
            ]
        }
    ]
}

r = requests.put(endpoint + "/skillsets/" + skillset_name,
                 data=json.dumps(skillset_payload), headers=headers, params=params)
print(r.status_code)
```

Die Anforderung sollte die erfolgreiche Ausführung durch Rückgabe von Statuscode 201 bestätigen.

Die Qualifikation zur Schlüsselbegriffserkennung wird für die einzelnen Seiten angewandt. Durch Festlegen des Kontexts auf `"document/pages/*"` führen Sie diesen Anreicherungsschritt für jedes Element des Dokument-/Seitenarrays (für jede Seite im Dokument) aus.

Jede Qualifikation wird auf den Inhalten des Dokuments ausgeführt. Während der Verarbeitung entschlüsselt Azure Cognitive Search jedes Dokument, um die Inhalte aus verschiedenen Dateiformaten zu lesen. In der Quelldatei gefundener Text wird für jedes Dokument jeweils in einem `content`-Feld gespeichert. Legen Sie die Eingabe daher auf `"/document/content"` fest.

Unten finden Sie eine grafische Darstellung der Qualifikationsgruppe.

:::image type="content" source="media/cognitive-search-tutorial-blob/skillset.png" alt-text="Erstellen eines Speicherkontos" border="false":::

Ausgaben können einem Index zugeordnet, als Eingabe einer Downstream-Qualifikation verwendet oder in beider Weise zugleich eingesetzt werden, wie etwa bei Sprachcode. Im Index ist ein Sprachcode zu Filterungszwecken nützlich. Als Eingabe wird ein Sprachcode von Qualifikationen zur Textanalyse verwendet, um die Linguistikregeln über Wörtertrennung zu informieren.

Weitere Informationen zu den Grundlagen von Qualifikationsgruppen finden Sie unter [How to define a skillset](cognitive-search-defining-skillset.md) (Definieren von Qualifikationsgruppen).

### <a name="step-3-create-an-index"></a>Schritt 3: Erstellen eines Index

In diesem Abschnitt definieren Sie das Indexschema, indem Sie die Felder angeben, die in den durchsuchbaren Index aufgenommen werden, und die Suchattribute für die einzelnen Felder festlegen. Felder besitzen einen Typ und können Attribute annehmen, die bestimmen, wie das Feld verwendet wird (durchsuchbar, sortierbar usw.). Feldname in einem Index müssen nicht exakt mit den Feldnamen in der Quelle übereinstimmen. In einem späteren Schritt fügen Sie in einem Indexer Feldzuordnungen hinzu, um die Quell- und Zielfelder zu verbinden. Definieren Sie für diesen Schritt den Index mit Feldbenennungskonventionen, die für Ihre Suchanwendung angemessen sind.

In dieser Übung werden die folgenden Felder und Feldtypen verwendet:

| Feldnamen: | ID         | Inhalt   | languageCode | keyPhrases         | organizations     |
|--------------|----------|-------|----------|--------------------|-------------------|
| field-types: | Edm.String|Edm.String| Edm.String| List<Edm.String>  | List<Edm.String>  |

Führen Sie das folgende Skript aus, um den Index `cogsrch-py-index` zu erstellen.

```python
# Create an index
index_payload = {
    "name": index_name,
    "fields": [
        {
            "name": "id",
            "type": "Edm.String",
            "key": "true",
            "searchable": "true",
            "filterable": "false",
            "facetable": "false",
            "sortable": "true"
        },
        {
            "name": "content",
            "type": "Edm.String",
            "sortable": "false",
            "searchable": "true",
            "filterable": "false",
            "facetable": "false"
        },
        {
            "name": "languageCode",
            "type": "Edm.String",
            "searchable": "true",
            "filterable": "false",
            "facetable": "false"
        },
        {
            "name": "keyPhrases",
            "type": "Collection(Edm.String)",
            "searchable": "true",
            "filterable": "false",
            "facetable": "false"
        },
        {
            "name": "organizations",
            "type": "Collection(Edm.String)",
            "searchable": "true",
            "sortable": "false",
            "filterable": "false",
            "facetable": "false"
        }
    ]
}

r = requests.put(endpoint + "/indexes/" + index_name,
                 data=json.dumps(index_payload), headers=headers, params=params)
print(r.status_code)
```

Die Anforderung sollte die erfolgreiche Ausführung durch Rückgabe von Statuscode 201 bestätigen.

Weitere Informationen zum Definieren eines Index finden Sie unter [Index erstellen (Azure Cognitive Search-REST-API)](/rest/api/searchservice/create-index).

### <a name="step-4-create-and-run-an-indexer"></a>Schritt 4: Erstellen und Ausführen eines Indexers

Ein [Indexer](/rest/api/searchservice/create-indexer) steuert die Pipeline. Die drei bereits erstellten Komponenten (Datenquelle, Skillset und Index) sind Eingaben für einen Indexer. Durch die Erstellung des Indexers in Azure Cognitive Search wird die gesamte Pipeline aktiviert. 

Um diese Objekte in einem Indexer zusammenzuführen, müssen Sie Feldzuordnungen definieren.

+ Die `"fieldMappings"` werden vor dem Skillset verarbeitet, wobei die Quellfelder aus der Datenquelle Zielfeldern in einem Index zugeordnet werden. Wenn die Feldnamen und -typen auf beiden Seiten gleich sind, ist keine Zuordnung erforderlich.

+ Die `"outputFieldMappings"` werden nach dem Skillset verarbeitet. Dabei wird auf `"sourceFieldNames"` verwiesen, die so lange nicht vorhanden sind, bis sie per Dokumententschlüsselung oder Anreicherung erstellt werden. Der `"targetFieldName"` ist ein Feld in einem Index.

Neben dem Verknüpfen von Ein- und Ausgaben können Sie auch Feldzuordnungen nutzen, um Datenstrukturen zu vereinfachen. Weitere Informationen finden Sie unter [Zuordnen angereicherter Felder zu einem durchsuchbaren Index](cognitive-search-output-field-mapping.md).

Führen Sie das folgende Skript aus, um einen Indexer mit dem Namen `cogsrch-py-indexer` zu erstellen.

```python
# Create an indexer
indexer_payload = {
    "name": indexer_name,
    "dataSourceName": datasource_name,
    "targetIndexName": index_name,
    "skillsetName": skillset_name,
    "fieldMappings": [
        {
            "sourceFieldName": "metadata_storage_path",
            "targetFieldName": "id",
            "mappingFunction":
            {"name": "base64Encode"}
        },
        {
            "sourceFieldName": "content",
            "targetFieldName": "content"
        }
    ],
    "outputFieldMappings":
    [
        {
            "sourceFieldName": "/document/organizations",
            "targetFieldName": "organizations"
        },
        {
            "sourceFieldName": "/document/pages/*/keyPhrases/*",
            "targetFieldName": "keyPhrases"
        },
        {
            "sourceFieldName": "/document/languageCode",
            "targetFieldName": "languageCode"
        }
    ],
    "parameters":
    {
        "maxFailedItems": -1,
        "maxFailedItemsPerBatch": -1,
        "configuration":
        {
            "dataToExtract": "contentAndMetadata",
            "imageAction": "generateNormalizedImages"
        }
    }
}

r = requests.put(endpoint + "/indexers/" + indexer_name,
                 data=json.dumps(indexer_payload), headers=headers, params=params)
print(r.status_code)
```

Die Anforderung sollte nach kurzer Zeit den Statuscode 201 zurückgeben, aber die Verarbeitung kann einige Minuten dauern. Das Dataset ist zwar klein, aber Analysequalifikationen, z. B. Bildanalyse, sind rechenintensiv und nehmen Zeit in Anspruch.

Sie können den [Indexerstatus überwachen](#check-indexer-status), um zu ermitteln, wann der Indexer ausgeführt wird oder der Vorgang abgeschlossen wurde.

> [!TIP]
> Die Pipeline wird durch Erstellen eines Indexers aufgerufen. Wenn beim Zugriff auf die Daten, dem Zuordnen von Ein- und Ausgaben oder der Reihenfolge der Vorgänge Probleme auftreten, äußern sie sich in dieser Phase. Um die Pipeline mit Code- oder Skriptänderungen auszuführen, müssen Sie möglicherweise zuerst Objekte löschen. Weitere Informationen finden Sie unter [Reset and re-run](#reset) (Zurücksetzen und erneut Ausführen).

#### <a name="about-the-request-body"></a>Informationen zum Anforderungstext

Das Skript legt `"maxFailedItems"` auf -1 fest, was die Indexengine anweist, Fehler beim Datenimport zu ignorieren. Dies ist nützlich, weil die Demodatenquelle nur wenige Dokumente enthält. Für eine größere Datenquelle sollten Sie den Wert größer als 0 festlegen.

Achten Sie außerdem auf die Anweisung `"dataToExtract":"contentAndMetadata"` in den Konfigurationsparametern. Diese Anweisung weist den Indexer an, die Inhalte aus verschiedenen Dateiformaten und die den einzelnen Dateien zugeordneten Metadaten zu extrahieren.

Wenn die Inhalte extrahiert werden, können Sie `imageAction` darauf festlegen, Text aus in der Datenquelle gefundenen Bildern zu extrahieren. Die Konfiguration `"imageAction":"generateNormalizedImages"` in Kombination mit der OCR- und der Textzusammenführungsqualifikation weist den Indexer an, Text aus den Bildern zu extrahieren (beispielsweise das Wort „Stop“ aus einem Stoppschild) und ihn als Teil des Inhaltsfelds einzubetten. Dieses Verhalten betrifft sowohl die in den Dokumenten eingebetteten Bilder (denken Sie etwa an Bilder in PDF-Dateien) als auch die in der Datenquelle gefundenen Bilder, z. B. eine JPG-Datei.

<a name="check-indexer-status"></a>

## <a name="4---monitor-indexing"></a>4\. Überwachen der Indizierung

Nachdem der Indexer definiert wurde, wird er automatisch ausgeführt, wenn Sie die Anforderung senden. Abhängig von den kognitiven Qualifikationen, die Sie definiert haben, kann die Indizierung länger als erwartet dauern. Führen Sie das folgende Skript aus, um herauszufinden, wann die Indexerverarbeitung abgeschlossen ist.

```python
# Get indexer status
r = requests.get(endpoint + "/indexers/" + indexer_name +
                 "/status", headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

Überwachen Sie in der Antwort das `"lastResult"` auf seine `"status"`- und `"endTime"`-Werte. Führen Sie das Skript regelmäßig aus, um den Status zu überprüfen. Wenn der Indexer abgeschlossen wurde, wird der Status auf „success“ festlegt, ein Wert für „endTime“ wird angegeben, und die Antwort enthält Fehler und Warnungen, die bei der Anreicherung aufgetreten sind.

:::image type="content" source="media/cognitive-search-tutorial-blob-python/py-indexer-is-created.png" alt-text="Erstellen eines Speicherkontos" border="false":::

Warnungen sind bei bestimmten Kombinationen aus Quelldatei und Qualifikation häufig und weisen nicht immer auf ein Problem hin. Viele Warnungen sind unbedenklich. Wenn Sie beispielsweise eine JPEG-Datei indizieren, die nicht über Text verfügt, wird eine Warnung wie im folgenden Screenshot angezeigt.

:::image type="content" source="media/cognitive-search-tutorial-blob-python/py-indexer-warning-example.png" alt-text="Erstellen eines Speicherkontos" border="false":::

## <a name="5---search"></a>5\. Suchen

Führen Sie nach dem Abschluss der Indizierung Abfragen aus, die die Inhalte einzelner Felder zurückgeben. Standardmäßig gibt Azure Cognitive Search die 50 besten Ergebnisse zurück. Die Beispieldaten sind klein, so dass die Standardeinstellung gut funktioniert. Beim Arbeiten mit größeren Datensets müssen Sie jedoch möglicherweise Parameter in die Abfragezeichenfolge aufnehmen, um mehr Ergebnisse zurückzugeben. Eine entsprechende Anleitung finden Sie unter [Arbeiten mit Suchergebnissen in Azure Search](search-pagination-page-layout.md).

Zeigen Sie zur Überprüfung alle Felder für die Indexdefinition an.

```python
# Query the service for the index definition
r = requests.get(endpoint + "/indexes/" + index_name,
                 headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

Die Ergebnisse sollten in etwa dem folgenden Beispiel entsprechen. Im Screenshot wird nur ein Teil der Antwort angezeigt.

:::image type="content" source="media/cognitive-search-tutorial-blob-python/py-query-index-for-fields.png" alt-text="Erstellen eines Speicherkontos" border="false":::

Die Ausgabe ist das Indexschema mit dem Namen, dem Typ und den Attributen für jedes Feld.

Senden Sie eine zweite Abfrage nach `"*"`, um alle Inhalte eines einzelnen Felds zurückzugeben, z.B. `organizations`.

```python
# Query the index to return the contents of organizations
r = requests.get(endpoint + "/indexes/" + index_name +
                 "/docs?&search=*&$select=organizations", headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

Die Ergebnisse sollten in etwa dem folgenden Beispiel entsprechen. Im Screenshot wird nur ein Teil der Antwort angezeigt.

:::image type="content" source="media/cognitive-search-tutorial-blob-python/py-query-index-for-organizations.png" alt-text="Erstellen eines Speicherkontos" border="false":::

Wiederholen Sie das für die folgenden zusätzlichen Felder in dieser Übung: `content`, `languageCode`, `keyPhrases` und `organizations`. Mithilfe von `$select` können Sie unter Einsatz einer durch Trennzeichen getrennten Liste mehrere Felder zurückgeben.

Sie können GET oder POST verwenden, abhängig von der Komplexität und Länge der Abfragezeichenfolge. Weitere Informationen finden Sie unter [Abfragen mithilfe der REST-API](/rest/api/searchservice/search-documents).

<a name="reset"></a>

## <a name="reset-and-rerun"></a>Zurücksetzen und erneut ausführen

In den frühen experimentellen Phasen der Entwicklung besteht der praktikabelste Ansatz für den Übergang von einer Entwurfsphase zur nächsten darin, die Objekte aus Azure Cognitive Search zu löschen und Ihrem Code zu erlauben, sie neu zu erstellen. Ressourcennamen sind eindeutig. Wenn Sie ein Objekt löschen, können Sie es unter dem gleichen Namen neu erstellen.

Sie können das Portal verwenden, um Indizes, Indexer, Datenquellen und Qualifikationsgruppen zu löschen. Wenn Sie den Indexer löschen, können Sie optional auch den Index, die Qualifikationsgruppen und die Datenquelle löschen.

:::image type="content" source="media/cognitive-search-tutorial-blob-python/py-delete-indexer-delete-all.png" alt-text="Erstellen eines Speicherkontos" border="false":::

Sie können sie auch mithilfe eines Skripts löschen. Das folgende Skript veranschaulicht, wie ein Skillset gelöscht wird. 

```python
# delete the skillset
r = requests.delete(endpoint + "/skillsets/" + skillset_name,
                    headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

Nach erfolgreichem Löschen wird der Statuscode 204 zurückgegeben.

## <a name="takeaways"></a>Wesentliche Punkte

Dieses Tutorial veranschaulicht die grundlegenden Schritte beim Erstellen einer erweiterten Indizierungspipeline durch Erstellung von Komponenten: eine Datenquelle, eine Qualifikationsgruppe, ein Index und ein Indexer.

Es wurden [integrierte Qualifikationen](cognitive-search-predefined-skills.md) sowie Skillsetdefinitionen und eine Möglichkeit zur Verkettung von Qualifikationen mithilfe von Ein- und Ausgaben vorgestellt. Sie haben darüber hinaus erfahren, dass `outputFieldMappings` in der Indexerdefinition erforderlich ist, um angereicherte Werte aus der Pipeline an einen durchsuchbaren Index in einem Azure Cognitive Search-Dienst weiterzuleiten.

Ferner haben Sie erfahren, wie die Ergebnisse getestet werden und das System für weitere Entwicklungsschritte zurückgesetzt wird. Sie haben gelernt, dass das Ausgeben von Abfragen auf den Index die von der angereicherten Indizierungspipeline erstellte Ausgabe zurückgibt. In dieser Version ist ein Mechanismus zum Anzeigen von internen Konstrukten (vom System erstellten angereicherten Dokumenten) verfügbar. Darüber hinaus haben Sie erfahren, wie der Indexerstatus überprüft wird und welche Objekte vor der erneuten Ausführung einer Pipeline gelöscht werden müssen.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie in Ihrem eigenen Abonnement arbeiten, ist es ratsam, nach Abschluss eines Projekts die nicht mehr benötigten Ressourcen zu entfernen. Ressourcen, die weiterhin ausgeführt werden, können Sie Geld kosten. Sie können entweder einzelne Ressourcen oder aber die Ressourcengruppe löschen, um den gesamten Ressourcensatz zu entfernen.

Ressourcen können im Portal über den Link „Alle Ressourcen“ oder „Ressourcengruppen“ im linken Navigationsbereich gesucht und verwaltet werden.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie sich nun mit allen Objekten einer KI-Anreicherungspipeline vertraut gemacht haben, können Sie sich weiter über Skillsetdefinitionen und einzelne Qualifikationen informieren.

> [!div class="nextstepaction"]
> [Erstellen eines Skillsets](cognitive-search-defining-skillset.md)