---
title: 'Tutorial: Indizieren von teilweise strukturierten Daten in JSON-Blobs'
titleSuffix: Azure Cognitive Search
description: Hier erfahren Sie, wie Sie teilweise strukturierte Azure-JSON-Blobs mithilfe von Azure Cognitive Search-REST-APIs und Postman indizieren und durchsuchen.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 09/25/2020
ms.openlocfilehash: 7c88aea6aff942cdcf5cbc022df8f07cfe0d4cce
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94701278"
---
# <a name="tutorial-index-json-blobs-from-azure-storage-using-rest"></a>Tutorial: Indizieren von JSON-Blobs aus Azure Storage per REST

Azure Cognitive Search kann JSON-Dokumente und -Arrays in Azure Blob Storage mithilfe eines [Indexers](search-indexer-overview.md) indizieren, der teilweise strukturierte Daten lesen kann. Teilweise strukturierte Daten enthalten Tags oder Markierungen, mit denen Inhalte in den Daten voneinander getrennt werden. Sie bilden den Unterschied zwischen unstrukturierten Daten, die vollständig indiziert werden müssen, und formal strukturierten Daten, die einem Datenmodell entsprechen (wie z. B. einem Schema einer relationalen Datenbank), das pro Feld indiziert werden kann.

In diesem Tutorial werden Postman und die [Azure Cognitive Search-REST-APIs](/rest/api/searchservice/) verwendet, um folgende Aufgaben durchzuführen:

> [!div class="checklist"]
> * Konfigurieren einer Azure Cognitive Search-Datenquelle für einen Azure-Blobcontainer
> * Erstellen eines Azure Cognitive Search-Index mit durchsuchbarem Inhalt
> * Konfigurieren und Ausführen eines Indexers zum Lesen des Containers und zum Extrahieren von durchsuchbaren Inhalten aus Azure Blob Storage
> * Durchsuchen des soeben erstellten Index

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

+ [Azure Storage (in englischer Sprache)](../storage/common/storage-account-create.md)
+ [Postman-Desktop-App](https://www.getpostman.com/)
+ [Neuer](search-create-service-portal.md) oder [bereits vorhandener](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) Suchdienst 

> [!Note]
> In diesem Tutorial können Sie den kostenlosen Dienst verwenden. Der kostenlose Suchdienst ist auf drei Indizes, drei Indexer und drei Datenquellen beschränkt. In diesem Tutorial wird davon jeweils eine Instanz erstellt. Vergewissern Sie sich zunächst, dass Ihr Dienst über genügend freie Kapazität für die neuen Ressourcen verfügt.

## <a name="download-files"></a>Herunterladen von Dateien

[Clinical-trials-json.zip](https://github.com/Azure-Samples/storage-blob-integration-with-cdn-search-hdi/raw/master/clinical-trials-json.zip) enthält die Daten, die in diesem Tutorial verwendet werden. Laden Sie die Datei herunter, und entzippen Sie sie in einem eigenen Ordner. Die Daten stammen aus [ClinicalTrials.gov](https://clinicaltrials.gov/ct2/results) und werden für dieses Tutorial in JSON konvertiert.

## <a name="1---create-services"></a>1\. Erstellen der Dienste

In diesem Tutorial wird Azure Cognitive Search für die Indizierung und für Abfragen und Azure-Blobspeicher zum Bereitstellen der Daten verwendet. 

Erstellen Sie diese beiden Dienste nach Möglichkeit in derselben Region und Ressourcengruppe, um eine möglichst große Nähe zu erreichen und die Verwaltung zu vereinfachen. In der Praxis kann sich Ihr Azure Storage-Konto in einer beliebigen Region befinden.

### <a name="start-with-azure-storage"></a>Azure Storage

1. [Melden Sie sich beim Azure-Portal](https://portal.azure.com/) an, und klicken Sie auf **+ Ressource erstellen**.

1. Suchen Sie nach *Speicherkonto*, und wählen Sie das Speicherkonto-Angebot von Microsoft aus.

   :::image type="content" source="media/cognitive-search-tutorial-blob/storage-account.png" alt-text="Erstellen eines Speicherkontos" border="false":::

1. Auf der Registerkarte „Grundlagen“ sind folgende Angaben erforderlich. Übernehmen Sie bei allen anderen Optionen die Standardeinstellungen.

   + **Ressourcengruppe**. Sie können entweder eine vorhandene Ressourcengruppe auswählen oder eine neue Ressourcengruppe erstellen. Verwenden Sie jedoch für alle Dienste die gleiche Gruppe, um die Dienste gemeinsam verwalten zu können.

   + **Speicherkontoname**: Falls Sie über mehrere Ressourcen des gleichen Typs verfügen, geben Sie zur Unterscheidung den Typ und die Region an (Beispiel: *blobstoragewestus*). 

   + **Standort**. Wählen Sie nach Möglichkeit den gleichen Standort aus, der auch für Azure Cognitive Search und Cognitive Services verwendet wird. Bei Verwendung eines einzelnen Standorts fallen keine Bandbreitengebühren an.

   + **Kontoart**: Verwenden Sie die Standardeinstellung *StorageV2 (allgemein, Version 2)* .

1. Klicken Sie zum Erstellen des Diensts auf **Überprüfen + erstellen**.

1. Klicken Sie nach der Erstellung auf **Go to the resource** (Zur Ressource), um die Übersichtsseite zu öffnen.

1. Klicken Sie auf den Dienst **Blobs**.

1. [Erstellen Sie einen Blobcontainer](../storage/blobs/storage-quickstart-blobs-portal.md) für die Beispieldaten. Sie können die öffentliche Zugriffsebene auf beliebige gültige Werte festlegen.

1. Nachdem der Container erstellt wurde, öffnen Sie ihn, und wählen Sie auf der Befehlsleiste die Option **Hochladen** aus.

   :::image type="content" source="media/search-semi-structured-data/upload-command-bar.png" alt-text="„Hochladen“ auf der Befehlsleiste" border="false":::

1. Navigieren Sie zu dem Ordner, der die Beispieldateien enthält. Wählen Sie alle Dateien aus, und klicken Sie dann auf **Hochladen**.

   :::image type="content" source="media/search-semi-structured-data/clinicalupload.png" alt-text="Hochladen von Dateien" border="false":::

Nach Abschluss des Uploads sollten die Dateien im Datencontainer in einem eigenen Unterordner angezeigt werden.

### <a name="azure-cognitive-search"></a>Azure Cognitive Search

Die nächste Ressource ist Azure Cognitive Search, die Sie [im Portal erstellen](search-create-service-portal.md) können. Im Rahmen dieser exemplarischen Vorgehensweise können Sie den Free-Tarif verwenden. 

Erfassen Sie genau wie bei Azure Blob Storage den Zugriffsschlüssel. Wenn Sie später mit der Strukturierung von Anforderungen beginnen, müssen Sie den Endpunkt und den Administrator-API-Schlüssel für die Authentifizierung der jeweiligen Anforderung angeben.

### <a name="get-a-key-and-url"></a>Abrufen eines Schlüssels und einer URL

Für REST-Aufrufe sind die Dienst-URL und ein Zugriffsschlüssel für jede Anforderung erforderlich. Ein Suchdienst wird mit beidem erstellt. Gehen Sie daher wie folgt vor, um die erforderlichen Informationen zu erhalten, falls Sie Azure Cognitive Search Ihrem Abonnement hinzugefügt haben:

1. [Melden Sie sich beim Azure-Portal an](https://portal.azure.com/), und rufen Sie auf der Seite **Übersicht** Ihres Suchdiensts die URL ab. Ein Beispiel für einen Endpunkt ist `https://mydemo.search.windows.net`.

1. Rufen Sie unter **Einstellungen** > **Schlüssel** einen Administratorschlüssel ab, um Vollzugriff auf den Dienst zu erhalten. Es gibt zwei austauschbare Administratorschlüssel – diese wurden zum Zweck der Geschäftskontinuität bereitgestellt, falls Sie einen Rollover für einen Schlüssel durchführen müssen. Für Anforderungen zum Hinzufügen, Ändern und Löschen von Objekten können Sie den primären oder den sekundären Schlüssel verwenden.

:::image type="content" source="media/search-get-started-rest/get-url-key.png" alt-text="Abrufen eines HTTP-Endpunkts und eines Zugriffsschlüssels" border="false":::

Für alle an Ihren Dienst gesendeten Anforderungen ist ein API-Schlüssel erforderlich. Ein gültiger Schlüssel stellt anforderungsbasiert eine Vertrauensstellung her zwischen der Anwendung, die die Anforderung versendet, und dem Dienst, der sie verarbeitet.

## <a name="2---set-up-postman"></a>2\. Einrichten von Postman

Starten Sie Postman, und richten Sie eine HTTP-Anforderung ein. Wenn Sie mit diesem Tool nicht vertraut sind, lesen Sie [Schnellstart: Erstellen eines Azure Cognitive Search-Index mithilfe von REST-APIs](search-get-started-rest.md).

Die Anforderungsmethoden für die Aufrufe in diesem Tutorial sind jeweils **POST** und **GET**. Sie senden drei API-Aufrufe an Ihren Suchdienst, um eine Datenquelle, einen Index und einen Indexer zu erstellen. Die Datenquelle enthält einen Zeiger auf Ihr Speicherkonto und Ihre JSON-Daten. Ihr Suchdienst stellt die Verbindung beim Laden der Daten her.

Legen Sie unter „Header“ den Inhaltstyp (Content-type) auf `application/json` und `api-key` auf den Administrator-API-Schlüssel Ihres Azure Cognitive Search-Diensts fest. Die festgelegten Header können Sie dann für jede Anforderung in dieser Übung verwenden.

  :::image type="content" source="media/search-get-started-rest/postman-url.png" alt-text="Postman-Anforderungs-URL und -Header" border="false":::

In URIs muss eine API-Version angegeben werden, und für jeden Aufruf sollte **201 Created** zurückgegeben werden. Die allgemein verfügbare API-Version zur Verwendung von JSON-Arrays lautet `2020-06-30`.

## <a name="3---create-a-data-source"></a>3\. Erstellen einer Datenquelle

Die [API zum Erstellen einer Datenquelle](/rest/api/searchservice/create-data-source) erstellt ein Azure Cognitive Search-Objekt, das angibt, welche Daten indiziert werden sollen.

1. Legen Sie den Endpunkt dieses Aufrufs auf `https://[service name].search.windows.net/datasources?api-version=2020-06-30` fest. Ersetzen Sie `[service name]` durch den Namen Ihres Suchdiensts. 

1. Kopieren Sie den folgenden JSON-Code in den Anforderungstext.

    ```json
    {
        "name" : "clinical-trials-json-ds",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=[storage account name];AccountKey=[storage account key];" },
        "container" : { "name" : "[blob container name]"}
    }
    ```

1. Ersetzen Sie die Verbindungszeichenfolge durch eine gültige Zeichenfolge für Ihr Konto.

1. Ersetzen Sie „[blob container name]“ durch den Container, den Sie für die Beispieldaten erstellt haben. 

1. Senden Sie die Anforderung. Die Antwort sollte in etwa wie folgt aussehen:

    ```json
    {
        "@odata.context": "https://exampleurl.search.windows.net/$metadata#datasources/$entity",
        "@odata.etag": "\"0x8D505FBC3856C9E\"",
        "name": "clinical-trials-json-ds",
        "description": null,
        "type": "azureblob",
        "subtype": null,
        "credentials": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=[mystorageaccounthere];AccountKey=[[myaccountkeyhere]]];"
        },
        "container": {
            "name": "[mycontainernamehere]",
            "query": null
        },
        "dataChangeDetectionPolicy": null,
        "dataDeletionDetectionPolicy": null
    }
    ```

## <a name="4---create-an-index"></a>4\. Erstellen eines Index
    
Beim zweiten Aufruf wird die [API zum Erstellen eines Index](/rest/api/searchservice/create-index) aufgerufen, die einen Azure Cognitive Search-Index mit allen durchsuchbaren Daten erstellt. Ein Index gibt alle Parameter und die dazugehörigen Attribute an.

1. Legen Sie den Endpunkt dieses Aufrufs auf `https://[service name].search.windows.net/indexes?api-version=2020-06-30` fest. Ersetzen Sie `[service name]` durch den Namen Ihres Suchdiensts.

1. Kopieren Sie den folgenden JSON-Code in den Anforderungstext.

    ```json
    {
      "name": "clinical-trials-json-index",  
      "fields": [
      {"name": "FileName", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": false, "filterable": false, "sortable": true},
      {"name": "Description", "type": "Edm.String", "searchable": true, "retrievable": false, "facetable": false, "filterable": false, "sortable": false},
      {"name": "MinimumAge", "type": "Edm.Int32", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": true},
      {"name": "Title", "type": "Edm.String", "searchable": true, "retrievable": true, "facetable": false, "filterable": true, "sortable": true},
      {"name": "URL", "type": "Edm.String", "searchable": false, "retrievable": false, "facetable": false, "filterable": false, "sortable": false},
      {"name": "MyURL", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": false, "filterable": false, "sortable": false},
      {"name": "Gender", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": false},
      {"name": "MaximumAge", "type": "Edm.Int32", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": true},
      {"name": "Summary", "type": "Edm.String", "searchable": true, "retrievable": true, "facetable": false, "filterable": false, "sortable": false},
      {"name": "NCTID", "type": "Edm.String", "key": true, "searchable": true, "retrievable": true, "facetable": false, "filterable": true, "sortable": true},
      {"name": "Phase", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": false},
      {"name": "Date", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": false, "filterable": false, "sortable": true},
      {"name": "OverallStatus", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": false},
      {"name": "OrgStudyId", "type": "Edm.String", "searchable": true, "retrievable": true, "facetable": false, "filterable": true, "sortable": false},
      {"name": "HealthyVolunteers", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": false},
      {"name": "Keywords", "type": "Collection(Edm.String)", "searchable": true, "retrievable": true, "facetable": true, "filterable": false, "sortable": false},
      {"name": "metadata_storage_last_modified", "type":"Edm.DateTimeOffset", "searchable": false, "retrievable": true, "filterable": true, "sortable": false},
      {"name": "metadata_storage_size", "type":"Edm.String", "searchable": false, "retrievable": true, "filterable": true, "sortable": false},
      {"name": "metadata_content_type", "type":"Edm.String", "searchable": true, "retrievable": true, "filterable": true, "sortable": false}
      ]
    }
   ```

1. Senden Sie die Anforderung. Die Antwort sollte in etwa wie folgt aussehen:

    ```json
    {
        "@odata.context": "https://exampleurl.search.windows.net/$metadata#indexes/$entity",
        "@odata.etag": "\"0x8D505FC00EDD5FA\"",
        "name": "clinical-trials-json-index",
        "fields": [
            {
                "name": "FileName",
                "type": "Edm.String",
                "searchable": false,
                "filterable": false,
                "retrievable": true,
                "sortable": true,
                "facetable": false,
                "key": false,
                "indexAnalyzer": null,
                "searchAnalyzer": null,
                "analyzer": null,
                "synonymMaps": []
            },
            {
                "name": "Description",
                "type": "Edm.String",
                "searchable": true,
                "filterable": false,
                "retrievable": false,
                "sortable": false,
                "facetable": false,
                "key": false,
                "indexAnalyzer": null,
                "searchAnalyzer": null,
                "analyzer": null,
                "synonymMaps": []
            },
            ...
          }
    ```

## <a name="5---create-and-run-an-indexer"></a>5\. Erstellen und Ausführen eines Indexers

Ein Indexer stellt eine Verbindung mit der Datenquelle her, importiert Daten in den Zielsuchindex und stellt optional einen Zeitplan für die Automatisierung der Datenaktualisierung bereit. Die REST-API für diese Aufgabe ist [Indexer erstellen](/rest/api/searchservice/create-indexer).

1. Legen Sie den URI für diesen Aufruf auf `https://[service name].search.windows.net/indexers?api-version=2020-06-30` fest. Ersetzen Sie `[service name]` durch den Namen Ihres Suchdiensts.

1. Kopieren Sie den folgenden JSON-Code in den Anforderungstext.

    ```json
    {
      "name" : "clinical-trials-json-indexer",
      "dataSourceName" : "clinical-trials-json-ds",
      "targetIndexName" : "clinical-trials-json-index",
      "parameters" : { "configuration" : { "parsingMode" : "jsonArray" } }
    }
    ```

1. Senden Sie die Anforderung. Die Anforderung wird sofort verarbeitet. Wenn die Antwort eintrifft, verfügen Sie über einen Index, der für die Volltextsuche verwendet werden kann. Die Antwort sollte in etwa wie folgt aussehen:

    ```json
    {
        "@odata.context": "https://exampleurl.search.windows.net/$metadata#indexers/$entity",
        "@odata.etag": "\"0x8D505FDE143D164\"",
        "name": "clinical-trials-json-indexer",
        "description": null,
        "dataSourceName": "clinical-trials-json-ds",
        "targetIndexName": "clinical-trials-json-index",
        "schedule": null,
        "parameters": {
            "batchSize": null,
            "maxFailedItems": null,
            "maxFailedItemsPerBatch": null,
            "base64EncodeKeys": null,
            "configuration": {
                "parsingMode": "jsonArray"
            }
        },
        "fieldMappings": [],
        "enrichers": [],
        "disabled": null
    }
    ```

## <a name="6---search-your-json-files"></a>6\. Durchsuchen Ihrer JSON-Dateien

Sie können mit der Suche beginnen, sobald das erste Dokument geladen wurde.

1. Ändern Sie das Verb in **GET**.

1. Legen Sie den URI für diesen Aufruf auf `https://[service name].search.windows.net/indexes/clinical-trials-json-index/docs?search=*&api-version=2020-06-30&$count=true` fest. Ersetzen Sie `[service name]` durch den Namen Ihres Suchdiensts.

1. Senden Sie die Anforderung. Dies ist eine nicht spezifizierte Volltextsuchabfrage, mit der zusätzlich zur Dokumentanzahl alle Felder zurückgegeben werden, die im Index als abrufbar gekennzeichnet sind. Die Antwort sollte in etwa wie folgt aussehen:

    ```json
    {
        "@odata.context": "https://exampleurl.search.windows.net/indexes('clinical-trials-json-index')/$metadata#docs(*)",
        "@odata.count": 100,
        "value": [
            {
                "@search.score": 1.0,
                "FileName": "NCT00000102.txt",
                "MinimumAge": 14,
                "Title": "Congenital Adrenal Hyperplasia: Calcium Channels as Therapeutic Targets",
                "MyURL": "https://azure.storagedemos.com/clinical-trials/NCT00000102.txt",
                "Gender": "Both",
                "MaximumAge": 35,
                "Summary": "This study will test the ability of extended release nifedipine (Procardia XL), a blood pressure medication, to permit a decrease in the dose of glucocorticoid medication children take to treat congenital adrenal hyperplasia (CAH).",
                "NCTID": "NCT00000102",
                "Phase": "Phase 1/Phase 2",
                "Date": "ClinicalTrials.gov processed this data on October 25, 2016",
                "OverallStatus": "Completed",
                "OrgStudyId": "NCRR-M01RR01070-0506",
                "HealthyVolunteers": "No",
                "Keywords": [],
                "metadata_storage_last_modified": "2019-04-09T18:16:24Z",
                "metadata_storage_size": "33060",
                "metadata_content_type": null
            },
            . . . 
    ```

1. Fügen Sie den Abfrageparameter `$select` hinzu, um die Ergebnisse auf eine geringere Anzahl von Feldern zu beschränken: `https://[service name].search.windows.net/indexes/clinical-trials-json-index/docs?search=*&$select=Gender,metadata_storage_size&api-version=2020-06-30&$count=true`.  Für diese Abfrage ergibt sich für 100 Dokumente eine Übereinstimmung, aber standardmäßig werden von Azure Cognitive Search in den Ergebnissen nur 50 Dokumente zurückgegeben.

   :::image type="content" source="media/search-semi-structured-data/lastquery.png" alt-text="Parametrisierte Abfrage" border="false":::

1. Ein Beispiel für eine komplexere Abfrage ist `$filter=MinimumAge ge 30 and MaximumAge lt 75`. Hiermit werden nur Ergebnisse zurückgegeben, bei denen der Parameter „MinimumAge“ größer oder gleich 30 und der Parameter „MaximumAge“ kleiner als 75 ist. Ersetzen Sie den Ausdruck `$select` durch den Ausdruck `$filter`.

   :::image type="content" source="media/search-semi-structured-data/metadatashort.png" alt-text="Teilweise strukturierte Suche" border="false":::

Sie können auch logische Operatoren (and, or, not) und Vergleichsoperatoren (eq, ne, gt, lt, ge, le) nutzen. Bei Zeichenfolgenvergleichen wird die Groß-/Kleinschreibung beachtet. Weitere Informationen und Beispiele finden Sie unter [Erstellen einer einfachen Abfrage](search-query-simple-examples.md).

> [!NOTE]
> Der Parameter `$filter` funktioniert nur mit Metadaten, die beim Erstellen des Index als filterbar gekennzeichnet wurden.

## <a name="reset-and-rerun"></a>Zurücksetzen und erneut ausführen

In den frühen experimentellen Phasen der Entwicklung besteht der praktikabelste Ansatz für den Übergang von einer Entwurfsphase zur nächsten darin, die Objekte aus Azure Cognitive Search zu löschen und Ihrem Code zu erlauben, sie neu zu erstellen. Ressourcennamen sind eindeutig. Wenn Sie ein Objekt löschen, können Sie es unter dem gleichen Namen neu erstellen.

Sie können das Portal verwenden, um Indizes, Indexer und Datenquellen zu löschen. Alternativ können Sie **DELETE** verwenden und URLs für jedes Objekt angeben. Der folgende Befehl löscht einen Indexer:

```http
DELETE https://[YOUR-SERVICE-NAME].search.windows.net/indexers/clinical-trials-json-indexer?api-version=2020-06-30
```

Nach erfolgreichem Löschen wird der Statuscode 204 zurückgegeben.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie in Ihrem eigenen Abonnement arbeiten, ist es ratsam, nach Abschluss eines Projekts die nicht mehr benötigten Ressourcen zu entfernen. Ressourcen, die weiterhin ausgeführt werden, können Sie Geld kosten. Sie können entweder einzelne Ressourcen oder aber die Ressourcengruppe löschen, um den gesamten Ressourcensatz zu entfernen.

Ressourcen können im Portal über den Link „Alle Ressourcen“ oder „Ressourcengruppen“ im linken Navigationsbereich gesucht und verwaltet werden.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie sich nun mit den Grundlagen der Azure-Blobindizierung vertraut gemacht haben, können Sie sich die Indexerkonfiguration für JSON-Blobs in Azure Storage genauer ansehen.

> [!div class="nextstepaction"]
> [Indizieren von JSON-Blobs mit einem Blobindexer in der kognitiven Azure-Suche](search-howto-index-json-blobs.md)