---
title: Feldzuordnungen für die automatisierte Indizierung mithilfe von Indexern – Azure Search
description: Konfigurieren Sie Feldzuordnungen im Azure Search-Indexer zum Ausgleichen von Unterschieden in Feldnamen und Datendarstellungen.
ms.date: 05/02/2019
author: mgottein
manager: cgronlun
ms.author: magottei
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: d3e0d876550b0c3baf89f3f13e0458fc97e11351
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65025217"
---
# <a name="field-mappings-in-azure-search-indexers"></a>Feldzuordnungen in Azure Search-Indexern
Bei Verwendung des Azure Search-Indexers gibt es gelegentlich Situationen, in denen die Eingabedaten nicht ganz dem Schema des Zielindexes entsprechen. In diesen Fällen können Sie Ihre Daten mithilfe von **Feldzuordnungen** in das gewünschte Format umwandeln.

Feldzuordnungen sind beispielsweise in folgenden Situationen hilfreich:

* Ihre Datenquelle verfügt über ein Feld `_id`, aber Azure Search lässt keine Feldnamen zu, die mit einem Unterstrich beginnen. Mit einer Feldzuordnung können Sie das Feld „umbenennen“.
* Sie möchten mehrere Felder im Index mit denselben Datenquellendaten auffüllen, da Sie z.B. verschiedene Analysen auf diese Felder anwenden möchten. Mit Feldzuordnungen können Sie ein Datenquellenfeld „verzweigen“.
* Sie müssen Ihre Daten mit Base64 codieren oder decodieren. Feldzuordnungen unterstützen mehrere **Zuordnungsfunktionen**, einschließlich Funktionen für die Base64-Codierung und -Decodierung.   

## <a name="setting-up-field-mappings"></a>Einrichten von Feldzuordnungen
Sie können Feldzuordnungen beim Erstellen eines neuen Indexers über die API [Indexer erstellen](https://msdn.microsoft.com/library/azure/dn946899.aspx) hinzufügen. Sie können Feldzuordnungen für einen Indexer mithilfe der API [Indexer aktualisieren](https://msdn.microsoft.com/library/azure/dn946892.aspx) verwalten.

Eine Feldzuordnung besteht aus drei Teilen:

1. Einem `sourceFieldName`, der ein Feld in der Datenquelle darstellt. Diese Eigenschaft ist obligatorisch.
2. Einem optionalen `targetFieldName`, der ein Feld im Suchindex darstellt. Wenn dieser nicht angegeben wird, wird derselbe Name wie in der Datenquelle verwendet.
3. Einer optionalen `mappingFunction`, die die Daten mit einer von mehreren vordefinierten Funktionen transformieren kann. Die vollständige Liste der Funktionen finden Sie [unten](#mappingFunctions).

Feldzuordnungen werden dem `fieldMappings` -Array in der Indexerdefinition hinzugefügt.

Beispielsweise können Sie Unterschiede in Feldnamen folgendermaßen ausgleichen:

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

Ein Indexer kann mehrere Feldzuordnungen aufweisen. Hier sehen Sie beispielsweise, wie Sie ein Feld „verzweigen“ können:

```JSON

"fieldMappings" : [
    { "sourceFieldName" : "text", "targetFieldName" : "textStandardEnglishAnalyzer" },
    { "sourceFieldName" : "text", "targetFieldName" : "textSoundexAnalyzer" }
]
```

> [!NOTE]
> Azure Search verwendet einen von Groß- und Kleinschreibung unabhängigen Vergleich zum Auflösen von Feld- und Funktionsnamen in Feldzuordnungen. Dies ist zwar praktisch, weil Sie die Groß- und Kleinschreibung nicht berücksichtigen müssen, bedeutet aber, dass die Datenquelle oder der Index keine Felder aufweisen dürfen, die sich nur in der Groß- und Kleinschreibung unterscheiden.  
>
>

<a name="mappingFunctions"></a>

## <a name="field-mapping-functions"></a>Feldzuordnungsfunktionen
Diese Funktionen werden derzeit unterstützt:

* [base64Encode](#base64EncodeFunction)
* [base64Decode](#base64DecodeFunction)
* [extractTokenAtPosition](#extractTokenAtPositionFunction)
* [jsonArrayToStringCollection](#jsonArrayToStringCollectionFunction)

<a name="base64EncodeFunction"></a>

## <a name="base64encode"></a>base64Encode
Führt eine *URL-sichere* Base64-Codierung der Eingabezeichenfolge durch. Geht davon aus, dass die Eingabe mit UTF-8 codiert ist.

### <a name="sample-use-case---document-key-lookup"></a>Beispielanwendungsfall – Suche des Dokumentschlüssels
Nur URL-sichere Zeichen können in einem Azure Search-Dokumentschlüssel enthalten sein (da Kunden das Dokument z.B. über die [Lookup-API](https://docs.microsoft.com/rest/api/searchservice/lookup-document) aufrufen können müssen). Wenn Ihre Daten URL-unsichere Zeichen enthalten und Sie damit ein Schlüsselfeld in Ihrem Suchindex auffüllen möchten, verwenden Sie diese Funktion. Nach der Codierung des Schlüssels können Sie mit einer Base64-Codierung den ursprünglichen Wert abrufen. Einzelheiten finden Sie im Abschnitt [Base64-Codierung und -Decodierung](#base64details).

#### <a name="example"></a>Beispiel
```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "SourceKey",
    "targetFieldName" : "IndexKey",
    "mappingFunction" : { "name" : "base64Encode" }
  }]
```

### <a name="sample-use-case---retrieve-original-key"></a>Beispielanwendungsfall – Abrufen des Originalschlüssels
Sie verfügen über einen Blobindexer, der Blobs mit den Metadaten des Blobpfads als Dokumentschlüssel indiziert. Nach dem Abrufen des codierten Dokumentschlüssels möchten Sie den Pfad decodieren und das Blob herunterladen.

#### <a name="example"></a>Beispiel
```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "SourceKey",
    "targetFieldName" : "IndexKey",
    "mappingFunction" : { "name" : "base64Encode", "parameters" : { "useHttpServerUtilityUrlTokenEncode" : false } }
  }]
 ```

Wenn Sie Dokumente nicht mit Schlüsseln suchen müssen und auch den codierten Inhalt nicht decodieren müssen, können Sie einfach `parameters` für die Zuordnungsfunktion auslassen, mit der `useHttpServerUtilityUrlTokenEncode` standardmäßig auf `true` festgelegt wird. Andernfalls finden Sie im Abschnitt mit den [Details zu Base64](#base64details) Informationen zum Festlegen der zu verwendenden Einstellungen.

<a name="base64DecodeFunction"></a>

## <a name="base64decode"></a>base64Decode
Führt die Base64-Decodierung der Eingabezeichenfolge durch. Es wird davon ausgegangen, dass es sich bei der Eingabe um eine *URL-sichere* Base64-codierte Zeichenfolge handelt.

### <a name="sample-use-case"></a>Beispiel für einen Anwendungsfall
Benutzerdefinierte Blob-Metadatenwerte müssen ASCII-codiert sein. Sie können die Base64-Codierung verwenden, um beliebige UTF-8-Zeichenfolgen in benutzerdefinierten Blobmetadaten darzustellen. Um die Suche aussagekräftig zu gestalten, können Sie mit dieser Funktion die codierten Daten beim Auffüllen des Suchindexes wieder in „normale“ Zeichenfolgen umwandeln.

#### <a name="example"></a>Beispiel
```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "Base64EncodedMetadata",
    "targetFieldName" : "SearchableMetadata",
    "mappingFunction" : { "name" : "base64Decode", "parameters" : { "useHttpServerUtilityUrlTokenDecode" : false } }
  }]
```

Wenn Sie keine `parameters` angeben, ist `true` der Standardwert von `useHttpServerUtilityUrlTokenDecode`. Informationen zum Festlegen der zu verwendenden Einstellungen finden Sie im Abschnitt mit den [Details zu Base64](#base64details).

<a name="base64details"></a>

### <a name="details-of-base64-encoding-and-decoding"></a>Details zur Base64-Codierung und -Decodierung
Azure Search unterstützt zwei base64-Codierungen: das HttpServerUtility-URL-Token und die URL-sichere Base64-Codierung ohne Auffüllung. Sie müssen die gleiche Codierung wie die Zuordnungsfunktionen verwenden, wenn Sie einen Dokumentschlüssel für die Suche codieren, einen Wert für die Decodierung durch den Indexer codieren oder ein durch den Indexer codiertes Feld decodieren möchten.

Wenn der Parameter `useHttpServerUtilityUrlTokenEncode` zum Codieren bzw. `useHttpServerUtilityUrlTokenDecode` zum Decodieren auf `true` eingestellt ist, verhält sich `base64Encode` wie [HttpServerUtility.UrlTokenEncode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokenencode.aspx) und `base64Decode` wie [HttpServerUtility.UrlTokenDecode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokendecode.aspx).

Wenn Sie nicht das vollständige .NET Framework verwenden (d. h., Sie verwenden .NET Core oder eine andere Programmierumgebung), um die Schlüsselwerte zum Emulieren des Azure Search-Verhaltens zu erzeugen, sollten Sie `useHttpServerUtilityUrlTokenEncode` und `useHttpServerUtilityUrlTokenDecode` auf `false` festlegen. Je nach verwendeter Bibliothek können sich die Hilfsfunktionen der Base64-Codierung und -Decodierung von Azure Search unterscheiden.

In der folgenden Tabelle werden verschiedene Base64-Codierungen der Zeichenfolge `00>00?00` verglichen. Um die erforderliche weitere Verarbeitung (sofern vorhanden) für die Base64-Funktionen zu ermitteln, wenden Sie die Codierfunktion der Bibliothek auf die Zeichenfolge `00>00?00` an und vergleichen die Ausgabe mit der erwarteten Ausgabe `MDA-MDA_MDA`.

| Codieren | Base64-codierte Ausgabe | Weitere Verarbeitung nach Codierung der Bibliothek | Weitere Verarbeitung vor Decodierung der Bibliothek |
| --- | --- | --- | --- |
| Base64 mit Auffüllung | `MDA+MDA/MDA=` | Verwenden URL-sicherer Zeichen und Entfernen der Auffüllung | Verwenden von Base64-Standardzeichen und Hinzufügen der Auffüllung |
| Base64-Codierung ohne Auffüllung | `MDA+MDA/MDA` | Verwenden URL-sicherer Zeichen | Verwenden von Base64-Standardzeichen |
| URL-sichere Base64-Codierung mit Auffüllung | `MDA-MDA_MDA=` | Entfernen der Auffüllung | Hinzufügen der Auffüllung |
| URL-sichere Base64-Codierung ohne Auffüllung | `MDA-MDA_MDA` | Keine | Keine |

<a name="extractTokenAtPositionFunction"></a>

## <a name="extracttokenatposition"></a>extractTokenAtPosition
Teilt ein Zeichenfolgenfeld mithilfe des angegebenen Trennzeichens und wählt das Token an der angegebenen Position in der resultierenden Teilung aus.

Wenn die Eingabe beispielsweise `Jane Doe` lautet, `" "` (Leerzeichen) als Trennzeichen (`delimiter`) dient und die `position` 0 ist, ist das Ergebnis `Jane`. Ist die `position` 1, ist das Ergebnis `Doe`. Wenn die Position auf ein Token verweist, das nicht vorhanden ist, wird ein Fehler zurückgegeben.

### <a name="sample-use-case"></a>Beispiel für einen Anwendungsfall
Die Datenquelle enthält ein Feld `PersonName`, und Sie möchten es als zwei separate Felder `FirstName` und `LastName` indizieren. Mit dieser Funktion können Sie die Eingabe mit dem Leerzeichen als Trennzeichen unterteilen.

### <a name="parameters"></a>Parameter
* `delimiter`: eine Zeichenfolge, die beim Teilen der Eingabezeichenfolge als Trennzeichen verwendet werden soll.
* `position`: eine ganzzahlige nullbasierte Position des Tokens, das nach der Teilung der Eingabezeichenfolge ausgewählt werden soll.    

### <a name="example"></a>Beispiel
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

## <a name="jsonarraytostringcollection"></a>jsonArrayToStringCollection
Wandelt eine Zeichenfolge, die als JSON-Zeichenfolgenarray formatiert ist, in ein Zeichenfolgenarray um, das zum Auffüllen eines Felds `Collection(Edm.String)` im Index verwendet werden kann.

Wenn die Eingabezeichenfolge beispielsweise `["red", "white", "blue"]` lautet, wird das Zielfeld vom Typ `Collection(Edm.String)` mit drei Werten gefüllt: `red`, `white` und `blue`. Für Eingabewerte, die nicht als JSON-Zeichenfolgenarrays analysiert werden können, wird ein Fehler zurückgegeben.

### <a name="sample-use-case"></a>Beispiel für einen Anwendungsfall
Die Azure SQL-Datenbank verfügt über keinen integrierten Datentyp, der `Collection(Edm.String)` -Feldern in Azure Search natürlich zugeordnet werden kann. Um Zeichenfolgen-Sammlungsfelder aufzufüllen, formatieren Sie Ihre Quelldaten als JSON-Zeichenfolgenarray, und verwenden Sie diese Funktion.

### <a name="example"></a>Beispiel
```JSON

"fieldMappings" : [
  { "sourceFieldName" : "tags", "mappingFunction" : { "name" : "jsonArrayToStringCollection" } }
]
```


## <a name="help-us-make-azure-search-better"></a>Helfen Sie uns bei der Verbesserung von Azure Search
Teilen Sie uns auf unserer [UserVoice-Website](https://feedback.azure.com/forums/263029-azure-search/)mit, wenn Sie sich Features wünschen oder Verbesserungsvorschläge haben.
