---
title: Feldzuordnungen in Indexern
titleSuffix: Azure Cognitive Search
description: Konfigurieren von Feldzuordnungen in einem Indexer zum Ausgleichen von Unterschieden in Feldnamen und Datendarstellungen.
manager: nitinme
author: mattmsft
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/11/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 579d0e334b4e60815b3a5efc877833ab75a3375d
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/07/2020
ms.locfileid: "94358931"
---
# <a name="field-mappings-and-transformations-using-azure-cognitive-search-indexers"></a>Feldzuordnungen und Transformationen mithilfe von Indexern der kognitiven Azure-Suche

![Indexerphasen](./media/search-indexer-field-mappings/indexer-stages-field-mappings.png "Indexerphasen")

Bei Verwendung von Indexern der kognitiven Azure-Suche kommt es gelegentlich vor, dass die Eingabedaten nicht ganz dem Schema des Zielindex entsprechen. In diesen Fällen können Sie **Feldzuordnungen** verwenden, um Ihre Daten während der Indizierung neu zu strukturieren.

Feldzuordnungen sind beispielsweise in folgenden Situationen hilfreich:

* Die Datenquelle enthält ein Feld mit dem Namen `_id`, in der kognitiven Azure-Suche sind jedoch keine Feldnamen zulässig, die mit einem Unterstrich beginnen. Mit einer Feldzuordnung können Sie ein Feld effektiv umbenennen.
* Sie möchten im Index mehrere Felder aus der gleichen Datenquelle auffüllen. Beispielsweise möchten Sie verschiedene Analysen auf diese Felder anwenden.
* Sie möchten ein Indexfeld mit Daten aus mehreren Datenquellen auffüllen, und in den einzelnen Datenquellen werden unterschiedliche Feldnamen verwendet.
* Sie müssen Ihre Daten mit Base64 codieren oder decodieren. Feldzuordnungen unterstützen mehrere **Zuordnungsfunktionen** , einschließlich Funktionen für die Base64-Codierung und -Decodierung.

> [!NOTE]
> Feldzuordnungen in Indexern sind eine einfache Möglichkeit, um Datenfelder Indexfeldern zuzuordnen, wobei in gewissem Umfang eine einfache Datenkonvertierung möglich ist. Komplexere Daten müssen möglicherweise vorverarbeitet werden, um sie in eine für die Indizierung geeignete Form umzuwandeln. Eine Option, die Sie ggf. in Betracht ziehen sollten, ist [Azure Data Factory](../data-factory/index.yml).

## <a name="set-up-field-mappings"></a>Einrichten von Feldzuordnungen

Eine Feldzuordnung besteht aus drei Teilen:

1. Einem `sourceFieldName`, der ein Feld in der Datenquelle darstellt. Diese Eigenschaft ist obligatorisch.
2. Einem optionalen `targetFieldName`, der ein Feld im Suchindex darstellt. Wenn dieser nicht angegeben wird, wird derselbe Name wie in der Datenquelle verwendet.
3. Einer optionalen `mappingFunction`, die die Daten mit einer von mehreren vordefinierten Funktionen transformieren kann. Dies kann sowohl für Eingabe- als auch für Ausgabefeldzuordnungen angewendet werden. Die vollständige Liste der Funktionen finden Sie [unten](#mappingFunctions).

Feldzuordnungen werden dem Array `fieldMappings` der Indexerdefinition hinzugefügt.

> [!NOTE]
> Wenn keine Feldzuordnungen hinzugefügt werden, nehmen Indexer an, dass die Datenquellenfelder den Indexfeldern mit demselben Namen zugeordnet werden sollen. Das Hinzufügen einer Feldzuordnung entfernt diese Standardfeldzuordnungen für das Quell- und Zielfeld. Manche Indexer, z. B. der [Blobspeicherindexer](search-howto-indexing-azure-blob-storage.md), fügen Standardfeldzuordnungen für das Indexschlüsselfeld hinzu.

## <a name="map-fields-using-the-rest-api"></a>Zuordnen von Feldern mithilfe der REST-API

Sie können Feldzuordnungen beim Erstellen eines neuen Indexers mithilfe der API-Anforderung [Indexer erstellen](/rest/api/searchservice/create-Indexer) hinzufügen. Sie können die Feldzuordnungen eines vorhandenen Indexers mithilfe der API-Anforderung [Indexer aktualisieren](/rest/api/searchservice/update-indexer) verwalten.

Aus dem nachstehenden Beispiel geht hervor, wie Sie ein Quellfeld einem Zielfeld mit einem anderen Namen zuordnen:

```JSON

PUT https://[service name].search.windows.net/indexers/myindexer?api-version=[api-version]
Content-Type: application/json
api-key: [admin key]
{
    "dataSourceName" : "mydatasource",
    "targetIndexName" : "myindex",
    "fieldMappings" : [ { "sourceFieldName" : "_id", "targetFieldName" : "id" } ]
}
```

Auf ein Quellfeld kann in mehreren Feldzuordnungen verwiesen werden. Im folgenden Beispiel wird gezeigt, wie Sie ein Feld „forken“, indem Sie das gleiche Quellfeld in zwei verschiedene Indexfelder kopieren:

```JSON

"fieldMappings" : [
    { "sourceFieldName" : "text", "targetFieldName" : "textStandardEnglishAnalyzer" },
    { "sourceFieldName" : "text", "targetFieldName" : "textSoundexAnalyzer" }
]
```

> [!NOTE]
> Bei der kognitiven Azure-Suche wird ein von Groß- und Kleinschreibung unabhängiger Vergleich zum Auflösen von Feld- und Funktionsnamen in Feldzuordnungen verwendet. Dies ist zwar praktisch, weil Sie die Groß- und Kleinschreibung nicht berücksichtigen müssen, bedeutet aber, dass die Datenquelle oder der Index keine Felder aufweisen dürfen, die sich nur in der Groß- und Kleinschreibung unterscheiden.  
>
>

## <a name="map-fields-using-the-net-sdk"></a>Zuordnen von Feldern mithilfe des .NET SDK

Sie definieren Feldzuordnungen im .NET SDK mit der [FieldMapping](/dotnet/api/azure.search.documents.indexes.models.fieldmapping)-Klasse. Diese weist die Eigenschaften `SourceFieldName` und `TargetFieldName` und einen optionalen `MappingFunction`-Verweis auf.

Sie können Feldzuordnungen beim Erstellen des Indexers oder später durch direktes Festlegen der `Indexer.FieldMappings`-Eigenschaft angeben.

Im folgenden C#-Beispiel werden die Feldzuordnungen beim Erstellen eines Indexers festgelegt.

```csharp
  List<FieldMapping> map = new List<FieldMapping> {
    // removes a leading underscore from a field name
    new FieldMapping("_custId", "custId"),
    // URL-encodes a field for use as the index key
    new FieldMapping("docPath", "docId", FieldMappingFunction.Base64Encode() )
  };

  Indexer sqlIndexer = new Indexer(
    name: "azure-sql-indexer",
    dataSourceName: sqlDataSource.Name,
    targetIndexName: index.Name,
    fieldMappings: map,
    schedule: new IndexingSchedule(TimeSpan.FromDays(1)));

  await searchService.Indexers.CreateOrUpdateAsync(indexer);
```

<a name="mappingFunctions"></a>

## <a name="field-mapping-functions"></a>Feldzuordnungsfunktionen

Eine Feldzuordnungsfunktion transformiert den Inhalt eines Felds, bevor es im Index gespeichert wird. Die folgenden Zuordnungsfunktionen werden derzeit unterstützt:

* [base64Encode](#base64EncodeFunction)
* [base64Decode](#base64DecodeFunction)
* [extractTokenAtPosition](#extractTokenAtPositionFunction)
* [jsonArrayToStringCollection](#jsonArrayToStringCollectionFunction)
* [urlEncode](#urlEncodeFunction)
* [urlDecode](#urlDecodeFunction)

<a name="base64EncodeFunction"></a>

### <a name="base64encode-function"></a>Funktion „base64Encode“

Führt eine *URL-sichere* Base64-Codierung der Eingabezeichenfolge durch. Geht davon aus, dass die Eingabe mit UTF-8 codiert ist.

#### <a name="example---document-key-lookup"></a>Beispiel: Dokumentschlüsselsuche

Nur URL-sichere Zeichen können in einem Dokumentschlüssel der kognitiven Azure-Suche enthalten sein (da Kunden in der Lage sein müssen, das Dokument über die [Lookup-API](/rest/api/searchservice/lookup-document) aufzurufen). Wenn das Quellfeld für den Schlüssel URL-unsichere Zeichen enthält, können Sie die Funktion `base64Encode` verwenden, um die Zeichenfolge bei der Indizierung zu konvertieren. Ein Dokumentschlüssel darf (vor und nach der Konvertierung) jedoch nicht länger als 1.024 Zeichen sein.

Wenn Sie den codierten Schlüssel während der Suche abrufen, können Sie die Funktion `base64Decode` verwenden, um den ursprünglichen Schlüsselwert abzurufen, mit dem Sie dann das Quelldokument abrufen können.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "SourceKey",
    "targetFieldName" : "IndexKey",
    "mappingFunction" : {
      "name" : "base64Encode",
      "parameters" : { "useHttpServerUtilityUrlTokenEncode" : false }
    }
  }]
 ```

#### <a name="example---preserve-original-values"></a>Beispiel: Beibehalten von Ursprungswerten

Der [Blobspeicherindexer](search-howto-indexing-azure-blob-storage.md) fügt dem Indexschlüsselfeld automatisch eine Feldzuordnung aus `metadata_storage_path` hinzu, den URI des Blobs, wenn keine Feldzuordnung angegeben ist. Dieser Wert ist Base64-codiert. Er kann also sicher als Dokumentschlüssel für Azure Cognitive Search verwendet werden. Im folgenden Beispiel sehen Sie, wie Sie gleichzeitig eine Base64-codierte Version mit *sicherer URL* von `metadata_storage_path` einem `index_key`-Feld zuordnen und den Ursprungswert in einem `metadata_storage_path`-Feld beibehalten:

```JSON

"fieldMappings": [
  {
    "sourceFieldName": "metadata_storage_path",
    "targetFieldName": "metadata_storage_path"
  },
  {
    "sourceFieldName": "metadata_storage_path",
    "targetFieldName": "index_key",
    "mappingFunction": {
       "name": "base64Encode"
    }
  }
]
```

Wenn Sie keine parameters-Eigenschaft für Ihre Zuordnungsfunktion angeben, wird standardmäßig der Wert `{"useHttpServerUtilityUrlTokenEncode" : true}` verwendet.

Die kognitive Azure-Suche unterstützt zwei verschiedene Base64-Codierungen. Verwenden Sie beim Codieren und Decodieren eines bestimmten Felds dieselben Parameter. Weitere Informationen, anhand der Sie entscheiden können, welche Parameter verwendet werden sollen, finden Sie unter [Base64-Codierungsoptionen](#base64details).

<a name="base64DecodeFunction"></a>

### <a name="base64decode-function"></a>Funktion „base64Decode“

Führt die Base64-Decodierung der Eingabezeichenfolge durch. Es wird davon ausgegangen, dass es sich bei der Eingabe um eine *URL-sichere* Base64-codierte Zeichenfolge handelt.

#### <a name="example---decode-blob-metadata-or-urls"></a>Beispiel: Decodieren von Blobmetadaten oder URLs

Ihre Quelldaten enthalten möglicherweise Base64-codierte Zeichenfolgen wie Blobmetadaten-Zeichenfolgen oder Web-URLs, die Sie konvertieren möchten, damit sie als Nur-Text durchsuchbar sind. Mit der Funktion `base64Decode` können Sie die codierten Daten beim Auffüllen des Suchindex wieder in „normale“ Zeichenfolgen umwandeln.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "Base64EncodedMetadata",
    "targetFieldName" : "SearchableMetadata",
    "mappingFunction" : { 
      "name" : "base64Decode", 
      "parameters" : { "useHttpServerUtilityUrlTokenDecode" : false }
    }
  }]
```

Wenn Sie keine parameters-Eigenschaft angeben, wird standardmäßig der Wert `{"useHttpServerUtilityUrlTokenEncode" : true}` verwendet.

Die kognitive Azure-Suche unterstützt zwei verschiedene Base64-Codierungen. Verwenden Sie beim Codieren und Decodieren eines bestimmten Felds dieselben Parameter. Ausführlichere Informationen, anhand der Sie entscheiden können, welche Parameter verwendet werden sollen, finden Sie unter [Base64-Codierungsoptionen](#base64details).

<a name="base64details"></a>

#### <a name="base64-encoding-options"></a>Base64-Codierungsoptionen

Azure Cognitive Search unterstützt die URL-sichere Base64-Codierung sowie normale Base64-Codierung. Eine während der Indizierung base64-codierte Zeichenfolge muss später mit denselben Codierungsoptionen decodiert werden. Andernfalls stimmt das Ergebnis nicht mit dem ursprünglichen Wert überein.

Wenn der Parameter `useHttpServerUtilityUrlTokenEncode` zum Codieren bzw. `useHttpServerUtilityUrlTokenDecode` zum Decodieren auf `true` festgelegt ist, verhält sich `base64Encode` wie [HttpServerUtility.UrlTokenEncode](/dotnet/api/system.web.httpserverutility.urltokenencode) und `base64Decode` wie [HttpServerUtility.UrlTokenDecode](/dotnet/api/system.web.httpserverutility.urltokendecode).

> [!WARNING]
> Wenn `base64Encode` verwendet wird, um Schlüsselwerte zu erzeugen, muss `useHttpServerUtilityUrlTokenEncode` auf „true“ festgelegt werden. Für Schlüsselwerte kann nur die URL-sichere Base64-Codierung verwendet werden. Weitere Informationen zum vollständigen Satz der Einschränkungen für Zeichen in Schlüsselwerten finden Sie unter [Benennungsregeln &#40;Azure Cognitive Search&#41;](/rest/api/searchservice/naming-rules).

Die .NET-Bibliotheken in Azure Cognitive Search setzen das vollständige .NET Framework voraus, das integrierte Codierung bereitstellt. Die Optionen `useHttpServerUtilityUrlTokenEncode` und `useHttpServerUtilityUrlTokenDecode` nutzen diese integrierte Funktion. Wenn Sie .NET Core oder ein anderes Framework verwenden, empfiehlt es sich, diese Optionen auf `false` festzulegen und die Codierungs- und Decodierungsfunktionen Ihres Frameworks direkt aufzurufen.

In der folgenden Tabelle werden verschiedene Base64-Codierungen der Zeichenfolge `00>00?00` verglichen. Um die erforderliche weitere Verarbeitung (sofern vorhanden) für die Base64-Funktionen zu ermitteln, wenden Sie die Codierfunktion der Bibliothek auf die Zeichenfolge `00>00?00` an und vergleichen die Ausgabe mit der erwarteten Ausgabe `MDA-MDA_MDA`.

| Codieren | Base64-codierte Ausgabe | Weitere Verarbeitung nach Codierung der Bibliothek | Weitere Verarbeitung vor Decodierung der Bibliothek |
| --- | --- | --- | --- |
| Base64 mit Auffüllung | `MDA+MDA/MDA=` | Verwenden URL-sicherer Zeichen und Entfernen der Auffüllung | Verwenden von Base64-Standardzeichen und Hinzufügen der Auffüllung |
| Base64-Codierung ohne Auffüllung | `MDA+MDA/MDA` | Verwenden URL-sicherer Zeichen | Verwenden von Base64-Standardzeichen |
| URL-sichere Base64-Codierung mit Auffüllung | `MDA-MDA_MDA=` | Entfernen der Auffüllung | Hinzufügen der Auffüllung |
| URL-sichere Base64-Codierung ohne Auffüllung | `MDA-MDA_MDA` | Keine | Keine |

<a name="extractTokenAtPositionFunction"></a>

### <a name="extracttokenatposition-function"></a>Funktion „extractTokenAtPosition“

Teilt ein Zeichenfolgenfeld mithilfe des angegebenen Trennzeichens und wählt das Token an der angegebenen Position in der resultierenden Teilung aus.

Diese Funktion verwendet die folgenden Parameter:

* `delimiter`: eine Zeichenfolge, die beim Teilen der Eingabezeichenfolge als Trennzeichen verwendet werden soll.
* `position`: eine ganzzahlige nullbasierte Position des Tokens, das nach der Teilung der Eingabezeichenfolge ausgewählt werden soll.

Wenn die Eingabe beispielsweise `Jane Doe` lautet, `" "` (Leerzeichen) als Trennzeichen (`delimiter`) dient und die `position` 0 ist, ist das Ergebnis `Jane`. Ist die `position` 1, ist das Ergebnis `Doe`. Wenn die Position auf ein Token verweist, das nicht vorhanden ist, wird ein Fehler zurückgegeben.

#### <a name="example---extract-a-name"></a>Beispiel: Extrahieren eines Namens

Die Datenquelle enthält ein Feld `PersonName`, und Sie möchten es als zwei separate Felder `FirstName` und `LastName` indizieren. Mit dieser Funktion können Sie die Eingabe mit dem Leerzeichen als Trennzeichen unterteilen.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "PersonName",
    "targetFieldName" : "FirstName",
    "mappingFunction" : { "name" : "extractTokenAtPosition", "parameters" : { "delimiter" : " ", "position" : 0 } }
  },
  {
    "sourceFieldName" : "PersonName",
    "targetFieldName" : "LastName",
    "mappingFunction" : { "name" : "extractTokenAtPosition", "parameters" : { "delimiter" : " ", "position" : 1 } }
  }]
```

<a name="jsonArrayToStringCollectionFunction"></a>

### <a name="jsonarraytostringcollection-function"></a>Funktion „jsonArrayToStringCollection“

Wandelt eine Zeichenfolge, die als JSON-Zeichenfolgenarray formatiert ist, in ein Zeichenfolgenarray um, das zum Auffüllen eines Felds `Collection(Edm.String)` im Index verwendet werden kann.

Wenn die Eingabezeichenfolge beispielsweise `["red", "white", "blue"]` lautet, wird das Zielfeld vom Typ `Collection(Edm.String)` mit drei Werten gefüllt: `red`, `white` und `blue`. Für Eingabewerte, die nicht als JSON-Zeichenfolgenarrays analysiert werden können, wird ein Fehler zurückgegeben.

#### <a name="example---populate-collection-from-relational-data"></a>Beispiel: Auffüllen der Sammlung aus relationalen Daten

Azure SQL-Datenbank verfügt nicht über einen integrierten Datentyp, der `Collection(Edm.String)`-Feldern in der kognitiven Azure-Suche natürlich zugeordnet werden kann. Um Zeichenfolgen-Sammlungsfelder aufzufüllen, können Sie Ihre Quelldaten als JSON-Zeichenfolgenarray vorverarbeiten und dann die Zuordnungsfunktion `jsonArrayToStringCollection` verwenden.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "tags", 
    "mappingFunction" : { "name" : "jsonArrayToStringCollection" }
  }]
```

<a name="urlEncodeFunction"></a>

### <a name="urlencode-function"></a>urlEncode-Funktion

Diese Funktion kann verwendet werden, um eine Zeichenfolge zu codieren, sodass Sie „URL-sicher“ ist. Bei Verwendung mit einer Zeichenfolge, die Zeichen enthält, die in einer URL nicht zulässig sind, konvertiert diese Funktion diese „unsicheren“ Zeichen in Entsprechungen von Zeichenentitäten. Diese Funktion verwendet das UTF-8-Codierungsformat.

#### <a name="example---document-key-lookup"></a>Beispiel: Dokumentschlüsselsuche

Die `urlEncode`-Funktion kann als Alternative zur `base64Encode`-Funktion verwendet werden, wenn nur URL-unsichere Zeichen konvertiert werden sollen, während andere Zeichen unverändert bleiben sollen.

Wenn die Eingabezeichenfolge beispielsweise `<hello>` lautet, wird das Zielfeld des Typs `(Edm.String)` mit dem Wert `%3chello%3e` aufgefüllt.

Wenn Sie den codierten Schlüssel während der Suche abrufen, können Sie die Funktion `urlDecode` verwenden, um den ursprünglichen Schlüsselwert abzurufen, mit dem Sie dann das Quelldokument abrufen können.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "SourceKey",
    "targetFieldName" : "IndexKey",
    "mappingFunction" : {
      "name" : "urlEncode"
    }
  }]
 ```

 <a name="urlDecodeFunction"></a>

 ### <a name="urldecode-function"></a>urlDecode-Funktion

 Diese Funktion konvertiert eine URL-codierte Zeichenfolge unter Verwendung des UTF-8-Codierungsformats in eine decodierte Zeichenfolge.

 ### <a name="example---decode-blob-metadata"></a>Beispiel: Decodieren von Blobmetadaten

 Einige Azure Storage-Clients codieren Blob-Metadaten automatisch als URL, wenn Sie Nicht-ASCII-Zeichen enthalten. Wenn Sie jedoch das Durchsuchen solcher Metadaten (als unformatierter Text) ermöglichen möchten, können Sie die `urlDecode`-Funktion verwenden, um die codierten Daten beim Auffüllen Ihres Suchindexes wieder in „normale“ Zeichenfolgen umzuwandeln.

 ```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "UrlEncodedMetadata",
    "targetFieldName" : "SearchableMetadata",
    "mappingFunction" : {
      "name" : "urlDecode"
    }
  }]
 ```
 
 <a name="fixedLengthEncodeFunction"></a>
 
 ### <a name="fixedlengthencode-function"></a>Funktion „fixedLengthEncode“
 
 Diese Funktion konvertiert eine Zeichenfolge in eine beliebige Zeichenfolge mit fester Länge.
 
 ### <a name="example---map-document-keys-that-are-too-long"></a>Beispiel: Zuordnen von zu langen Dokumentschlüsseln
 
Wenn Fehler mit der Meldung zurückgegeben werden, dass der Dokumentschlüssel mehr als 1.024 Zeichen enthält, können Sie mit dieser Funktion die Länge des Dokumentschlüssels verringern.

 ```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "metadata_storage_path",
    "targetFieldName" : "your key field",
    "mappingFunction" : {
      "name" : "fixedLengthEncode"
    }
  }]
 ```