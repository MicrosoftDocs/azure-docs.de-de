---
title: Erstellen der Indexdefinition und Konzepte – Azure Search
description: Einführung in Indexbegriffe und Konzepte in Azure Search, einschließlich Komponenten und physischer Struktur.
author: HeidiSteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.custom: seodec2018
ms.openlocfilehash: 462a99ffab8038f34b1ffd038ce5c8e8ec9a8565
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65024431"
---
# <a name="create-a-basic-index-in-azure-search"></a>Erstellen eines grundlegenden Index in Azure Search

In Azure Search ist ein *Index* ein dauerhafter Speicher von *Dokumenten* und anderen Konstrukten, die von einem Azure Search-Dienst für die gefilterte und Volltextsuche verwendet werden. Konzeptionell ist ein Dokument eine einzelne Einheit mit durchsuchbaren Daten im Index. Ein Internetversandhändler verfügt beispielsweise über ein Dokument für jeden angebotenen Artikel, eine Nachrichtenagentur über ein Dokument pro Zeitungsartikel usw. So lassen sich diese Konzepte vertrauteren Entsprechungen in der Datenbank zuordnen: Ein *Index* entspricht etwa einer *Tabelle*, und *Dokumente* entsprechen ungefähr den *Zeilen* einer Tabelle.

Wenn Sie einen Index hinzufügen oder hochladen, erstellt Azure Search physische Strukturen basierend auf dem von Ihnen bereitgestellten Schema. Wenn beispielsweise ein Feld in Ihrem Index als durchsuchbar markiert ist, wird ein invertierter Index für dieses Feld erstellt. Wenn Sie später Dokumente hinzufügen oder hochladen oder Suchabfragen an Azure Search übermitteln, senden Sie Anforderungen an einen bestimmten Index in Ihrem Suchdienst. Das Laden von Feldern mit Dokumentwerten wird als *Indizierung* oder Datenerfassung bezeichnet.

Sie können einen Index im Portal, in der [REST-API](search-create-index-rest-api.md) oder im [.NET SDK](search-create-index-dotnet.md) erstellen.

## <a name="recommended-workflow"></a>Empfohlener Workflow

Der richtige Indexentwurf wird in der Regel über mehrere Iterationen erreicht. Mithilfe einer Kombination aus Tools und APIs können Sie Ihren Entwurf schnell abschließen.

1. Bestimmen Sie, ob Sie einen [Indexer](search-indexer-overview.md#supported-data-sources) verwenden können. Wenn Ihre externen Daten aus einer der unterstützten Datenquellen stammen, können Sie einen Index mithilfe des [**Datenimport**](search-import-data-portal.md)-Assistenten als Prototyp verwenden und laden.

2. Wenn Sie **Daten importieren** nicht verwenden können, können Sie weiterhin mithilfe der Steuerelemente auf der Seite **Index hinzufügen** [einen anfänglichen Index im Portal erstellen](search-create-index-portal.md) und Felder und Datentypen hinzufügen und Attribute zuweisen. Das Portal zeigt Ihnen, welche Attribute für verschiedene Datentypen zur Verfügung stehen. Wenn der Indexentwurf für Sie neu ist, ist dies hilfreich.

   ![Seite „Index hinzufügen“ mit Attributen nach Datentyp](media/search-create-index-portal/field-attributes.png "Seite „Index hinzufügen“ mit Attributen nach Datentyp")
  
   Beim Klicken auf **Erstellen** werden alle physischen Strukturen, die Ihren Index unterstützen, in Ihrem Suchdienst erstellt.

3. Laden Sie das Indexschema mit [Index-REST-API abrufen](https://docs.microsoft.com/rest/api/searchservice/get-index) und einem Webtesttool wie [Postman](search-fiddler.md) herunter. Sie haben nun eine JSON-Darstellung des Indexes, den Sie im Portal erstellt haben. 

   Sie wechseln an diesem Punkt zu einem codebasierten Ansatz. Das Portal eignet sich nun ideal für die Iteration, da Sie keinen Index bearbeiten können, der bereits erstellt wurde. Sie können aber Postman und REST für die verbleibenden Aufgaben verwenden.

4. [Laden Sie Ihren Index mit Daten](search-what-is-data-import.md). Azure Search akzeptiert JSON-Dokumente. Um Ihre Daten programmgesteuert zu laden, können Sie Postman mit JSON-Dokumenten in der Anforderungsnutzlast verwenden. Wenn Ihre Daten nicht einfach als JSON-Code ausgedrückt werden, erfordert dieser Schritt den meisten Arbeitsaufwand.

5. Fragen Sie Ihren Index ab, überprüfen Sie Ergebnisse, und durchlaufen Sie weiter das Indexschema, bis Sie die Ergebnisse sehen, die Sie erwarten. Sie können [**Suchexplorer**](search-explorer.md) oder Postman verwenden, um Ihren Index abzufragen.

6. Verwenden Sie weiterhin Code zum Durchlaufen Ihres Entwurfs.  

Da physische Strukturen im Dienst erstellt werden, ist das [Löschen und Neuerstellen von Indizes](search-howto-reindex.md) erforderlich, wenn Sie wesentliche Änderungen an einer vorhandenen Felddefinition vornehmen. Dies bedeutet, dass Sie während der Entwicklung häufige Neuerstellungen einplanen sollten. Sie sollten erwägen, mit einer Teilmenge Ihrer Daten zu arbeiten, damit Neuerstellungen schneller gehen. 

Code eignet sich eher als ein Portalansatz für den iterativen Entwurf. Wenn Sie sich für die Indexdefinition auf das Portal verlassen, müssen Sie bei jeder Neuerstellung die Indexdefinition ausfüllen. Als Alternative sind Tools wie [Postman und die REST-API](search-fiddler.md) hilfreich zum Proof of Concept-Testen, wenn Entwicklungsprojekte sich noch in frühen Phasen befinden. Sie können inkrementelle Änderungen an einer Indexdefinition im Anforderungstext vornehmen und dann die Anforderung an Ihren Dienst senden, um einen Index mit einem aktualisierten Schema neu zu erstellen.

## <a name="components-of-an-index"></a>Komponenten eines Index

Schematisch besteht ein Azure Search-Index aus den folgenden Elementen. 

Die [*Feldsammlung*](#fields-collection) ist typischerweise der größte Teil eines Index, wobei jedes Feld benannt, typisiert und mit zulässigen Verhaltensweisen versehen wird, die bestimmen, wie es verwendet wird. Weitere Elemente sind [Vorschlagsfunktionen](#suggesters), [Bewertungsprofile](#scoring-profiles), [Analysetools](#analyzers) mit Komponenten zur Unterstützung der Anpassung sowie Optionen für [CORS](#cors) und [Verschlüsselungsschlüssel](#encryption-key).

```json
{
  "name": (optional on PUT; required on POST) "name_of_index",
  "fields": [
    {
      "name": "name_of_field",
      "type": "Edm.String | Collection(Edm.String) | Edm.Int32 | Edm.Int64 | Edm.Double | Edm.Boolean | Edm.DateTimeOffset | Edm.GeographyPoint",
      "searchable": true (default where applicable) | false (only Edm.String and Collection(Edm.String) fields can be searchable),
      "filterable": true (default) | false,
      "sortable": true (default where applicable) | false (Collection(Edm.String) fields cannot be sortable),
      "facetable": true (default where applicable) | false (Edm.GeographyPoint fields cannot be facetable),
      "key": true | false (default, only Edm.String fields can be keys),
      "retrievable": true (default) | false,
      "analyzer": "name_of_analyzer_for_search_and_indexing", (only if 'searchAnalyzer' and 'indexAnalyzer' are not set)
      "searchAnalyzer": "name_of_search_analyzer", (only if 'indexAnalyzer' is set and 'analyzer' is not set)
      "indexAnalyzer": "name_of_indexing_analyzer", (only if 'searchAnalyzer' is set and 'analyzer' is not set)
      "synonymMaps": [ "name_of_synonym_map" ] (optional, only one synonym map per field is currently supported)
    }
  ],
  "suggesters": [
    {
      "name": "name of suggester",
      "searchMode": "analyzingInfixMatching",
      "sourceFields": ["field1", "field2", ...]
    }
  ],
  "scoringProfiles": [
    {
      "name": "name of scoring profile",
      "text": (optional, only applies to searchable fields) {
        "weights": {
          "searchable_field_name": relative_weight_value (positive #'s),
          ...
        }
      },
      "functions": (optional) [
        {
          "type": "magnitude | freshness | distance | tag",
          "boost": # (positive number used as multiplier for raw score != 1),
          "fieldName": "...",
          "interpolation": "constant | linear (default) | quadratic | logarithmic",
          "magnitude": {
            "boostingRangeStart": #,
            "boostingRangeEnd": #,
            "constantBoostBeyondRange": true | false (default)
          },
          "freshness": {
            "boostingDuration": "..." (value representing timespan leading to now over which boosting occurs)
          },
          "distance": {
            "referencePointParameter": "...", (parameter to be passed in queries to use as reference location)
            "boostingDistance": # (the distance in kilometers from the reference location where the boosting range ends)
          },
          "tag": {
            "tagsParameter": "..." (parameter to be passed in queries to specify a list of tags to compare against target fields)
          }
        }
      ],
      "functionAggregation": (optional, applies only when functions are specified) 
        "sum (default) | average | minimum | maximum | firstMatching"
    }
  ],
  "analyzers":(optional)[ ... ],
  "charFilters":(optional)[ ... ],
  "tokenizers":(optional)[ ... ],
  "tokenFilters":(optional)[ ... ],
  "defaultScoringProfile": (optional) "...",
  "corsOptions": (optional) {
    "allowedOrigins": ["*"] | ["origin_1", "origin_2", ...],
    "maxAgeInSeconds": (optional) max_age_in_seconds (non-negative integer)
  },
  "encryptionKey":(optional){
    "keyVaultUri": "azure_key_vault_uri",
    "keyVaultKeyName": "name_of_azure_key_vault_key",
    "keyVaultKeyVersion": "version_of_azure_key_vault_key",
    "accessCredentials":(optional){
      "applicationId": "azure_active_directory_application_id",
      "applicationSecret": "azure_active_directory_application_authentication_key"
    }
  }
}
```

<a name="fields-collection"></a>

## <a name="fields-collection-and-field-attributes"></a>Feldersammlung und Feldattribute

Wenn Sie Ihr Schema definieren, müssen Sie den Namen, den Typ und die Attribute jedes Felds in Ihrem Index festlegen. Der Feldtyp klassifiziert die Daten, die in dem Feld gespeichert sind. Attribute werden für einzelne Felder festlegen, um anzugeben, wie das Feld verwendet wird. Die folgende Tabelle listet die Typen und Attribute auf, die Sie angeben können.

### <a name="data-types"></a>Datentypen
| Type | BESCHREIBUNG |
| --- | --- |
| *Edm.String* |Text, der optional für die Volltextsuche (Worttrennung, Wortstammerkennung usw.) mit einem Token versehen werden kann. |
| *Collection(Edm.String)* |Eine Liste von Zeichenfolgen, die für die Volltextsuche mit einem Token versehen werden können. Für die Anzahl der Elemente in einer Sammlung gibt es keine Obergrenze, allerdings gilt die Obergrenze für die Größe der Nutzlast von 16 MB auch für Sammlungen. |
| *Edm.Boolean* |Enthält TRUE/FALSE-Werte |
| *Edm.Int32* |32-Bit-Ganzzahlwerte |
| *Edm.Int64* |64-Bit-Ganzzahlwerte |
| *Edm.Double* |Numerische Daten mit doppelter Genauigkeit |
| *Edm.DateTimeOffset* |Datums-/Uhrzeitwerte im OData V4-Format (z.B. `yyyy-MM-ddTHH:mm:ss.fffZ` oder `yyyy-MM-ddTHH:mm:ss.fff[+/-]HH:mm`). |
| *Edm.GeographyPoint* |Ein Punkt, der einen weltweiten geografischen Standort darstellt |

Ausführlichere Informationen zu den von Azure Search unterstützten Datentypen finden Sie [hier](https://docs.microsoft.com/rest/api/searchservice/Supported-data-types).

### <a name="index-attributes"></a>Indexattribute
| Attribut | BESCHREIBUNG |
| --- | --- |
| *Schlüssel* |Eine Zeichenfolge, die die eindeutige ID der einzelnen Dokumente darstellt und für die Dokumentsuche verwendet wird. Jeder Index muss über einen Schlüssel verfügen. Als Schlüssel kann immer nur ein einzelnes Feld fungieren, und sein Typ muss auf „Edm.String“ festgelegt sein. |
| *Abrufbar* |Gibt an, ob ein Feld in einem Suchergebnis zurückgegeben werden kann. |
| *Filterbar* |Ermöglicht die Verwendung des Felds in Filterabfragen. |
| *Sortierbar* |Ermöglicht einer Abfrage das Sortieren von Suchergebnissen mithilfe dieses Felds. |
| *Facettierbar* |Ermöglicht die Verwendung eines Felds in einer [Facettennavigationsstruktur](search-faceted-navigation.md) für benutzerdefiniertes Filtern. Repetitive Werte, mit denen sich mehrere Dokumente zu einer Gruppe zusammenfassen lassen (etwa mehrere Dokumente der gleichen Marken- oder Dienstleistungskategorie), sind in der Regel am besten für die Verwendung als Facetten geeignet. |
| *Durchsuchbar* |Markiert das Feld als in die Volltextsuche einbeziehbar. |

Ausführlichere Informationen zu den Indexattributen von Azure Search finden Sie [hier](https://docs.microsoft.com/rest/api/searchservice/Create-Index).

## <a name="storage-implications"></a>Hinweise zum Speicher

Die Attribute, die Sie auswählen, haben Auswirkungen auf den Speicher. Der folgende Screenshot veranschaulicht die Indexspeichermuster aus verschiedenen Kombinationen von Attributen.

Der Index basiert auf der Datenquelle des [integrierten „realestate“-Beispiels](search-get-started-portal.md), die Sie indizieren und im Portal abfragen können. Obwohl die Indexschemas nicht angezeigt werden, können Sie die auf dem Indexnamen basierenden Attribute ableiten. Beispielsweise ist für den *realestate-searchable*-Index das **searchable**-Attribut ausgewählt und sonst nichts, für den *realestate-retrievable*-Index ist das **retrievable**-Attribut ausgewählt und nichts anderes usw.

![Indexgröße basierend auf der Attributauswahl](./media/search-what-is-an-index/realestate-index-size.png "Indexgröße basierend auf der Attributauswahl")

Obwohl diese Indexvarianten künstlich sind, können wir sie für umfassende Vergleiche des Einflusses von Attributen auf Speicher nutzen. Setzt die Einstellung **retrievable** die Indexgröße hinauf? Nein. Setzt das Hinzufügen von Feldern zu einer **Vorschlagsfunktion** die Indexgröße herauf? Ja.

Indizes, die Filtern und Sortieren unterstützen, sind proportional größer als Indizes, die nur die Volltextsuche unterstützen. Der Grund ist, dass Filtern und Sortieren genaue Übereinstimmungen abfragen, sodass Dokumente als Ganzes gespeichert werden. Im Gegensatz dazu verwenden durchsuchbare Felder, die Volltext- als auch Fuzzysuche unterstützen, invertierte Indizes, die mit tokenisierten Begriffen aufgefüllt werden, die weniger Speicherplatz als ganze Dokumente benötigen.

> [!Note]
> Die Speicherarchitektur gilt als Implementierungsdetail von Azure Search und könnte ohne vorherige Ankündigung geändert werden. Es gibt keine Garantie, dass das aktuelle Verhalten in der Zukunft beibehalten wird.

## <a name="suggesters"></a>Vorschläge
Eine Vorschlagsfunktion ist ein Abschnitt des Schemas, das definiert, welche Felder in einem Index verwendet werden, um AutoVervollständigen oder Eingabevorschläge für Abfragen bei Suchvorgängen zu unterstützen. In der Regel werden während der Eingabe einer Suchabfrage Teile von Suchzeichenfolgen an die [Suggestions-REST-API](https://docs.microsoft.com/rest/api/searchservice/suggestions) gesendet. Diese gibt daraufhin eine Reihe von Vorschlägen zurück. 

Mit Feldern, die einer Vorschlagsfunktion hinzugefügt werden, werden Eingabevorschläge von Suchbegriffen erstellt. Alle Suchbegriffe werden während der Indizierung erstellt und separat gespeichert. Weitere Informationen zum Erstellen einer Vorschlagsfunktionsstruktur finden Sie unter [Hinzufügen von Vorschlagsfunktionen zu einem Azure Search-Index](index-add-suggesters.md).

## <a name="scoring-profiles"></a>Bewertungsprofile

Ein [Bewertungsprofil](index-add-scoring-profiles.md) ist ein Abschnitt des Schemas, der benutzerdefinierte Bewertungsverhalten definiert, mit denen Sie beeinflussen können, welche Elemente weiter oben in den Suchergebnissen angezeigt werden. Bewertungsprofile bestehen aus Feldgewichtungen und Funktionen. Um sie verwenden zu können, fügen Sie in die Abfragezeichenfolge den Profilnamen ein.

Ein standardmäßiges Bewertungsprofil berechnet im Hintergrund für jedes Element eines Ergebnissatzes eine Suchbewertung. Sie können das interne, unbenannte Bewertungsprofil verwenden. Oder legen Sie für **defaultScoringProfile** ein benutzerdefiniertes Profil fest, das standardmäßig aufgerufen wird, wenn für eine Abfragezeichenfolge kein benutzerdefiniertes Profil angegeben wurde.

## <a name="analyzers"></a>Analysemodule

Das Analysetoolelement legt den Namen des Sprachanalysetools fest, das für das Feld verwendet werden soll. Weitere Informationen über den Bereich der Ihnen zur Verfügung stehenden Analysetools finden Sie unter [Analysetools für Textverarbeitung in Azure Search](search-analyzers.md). Analysetools können nur mit durchsuchbaren Feldern verwendet werden. Sobald das Analysetool einem Feld zugewiesen ist, kann es nicht geändert werden, solange Sie den Index nicht neu erstellen.

## <a name="cors"></a>CORS

Clientseitiger JavaScript-Code kann standardmäßig keine APIs aufrufen, da der Browser jegliche ursprungsübergreifenden Anforderungen verhindert. Um ursprungsübergreifende Abfragen für Ihren Index zu ermöglichen, aktivieren Sie CORS (Cross-Origin Resource Sharing), indem Sie das Attribut **corsOptions** festlegen. Aus Sicherheitsgründen wird CORS nur von Abfrage-APIs unterstützt. 

Die folgenden Optionen können für CORS festgelegt werden:

+ **allowedOrigins** (erforderlich): Dies ist eine Liste der Ursprünge, denen Zugriff auf Ihren Index gewährt wird. Dies bedeutet, dass jeglicher von diesen Ursprüngen bereitgestellte Javascript-Code Indexabfragen durchführen kann (sofern er den richtigen API-Schlüssel angibt). Ursprünge werden in der Regel im Format `protocol://<fully-qualified-domain-name>:<port>` angegeben, wobei `<port>` häufig weggelassen wird. Weitere Informationen finden Sie unter [Cross-Origin Resource Sharing, CORS (Wikipedia)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing).

  Wenn Sie den Zugriff auf alle Ursprünge zulassen möchten, beziehen Sie `*` als ein einzelnes Element in das Array **allowedOrigins** ein. *Dies ist keine empfohlene Vorgehensweise für Produktionssuchdienste*, ist für Entwicklung und Debugging jedoch oft nützlich.

+ **maxAgeInSeconds** (optional): Von Browsern wird dieser Wert verwendet, um die Dauer (in Sekunden) des Zwischenspeicherns von CORS-Preflight-Antworten zu ermitteln. Dies muss eine positive ganze Zahl sein. Mit dem Wert steigt auch die Leistung, aber es dauert auch länger, bis CORS-Richtlinienänderungen in Kraft treten. Wenn diese Einstellung nicht festgelegt ist, gilt die Standarddauer von 5 Minuten.

## <a name="encryption-key"></a>Verschlüsselungsschlüssel

Alle Azure Search-Indizes werden standardmäßig mit von Microsoft verwalteten Schlüsseln verschlüsselt. Die Indizes können jedoch auch so konfiguriert werden, dass sie mit **von Kunden verwalteten Schlüsseln** in Key Vault verschlüsselt werden. Weitere Informationen finden Sie unter [Verwalten von Verschlüsselungsschlüsseln in Azure Search](search-security-manage-encryption-keys.md).

## <a name="next-steps"></a>Nächste Schritte

Wenn Sie mit den Grundlagen der Indexerstellung vertraut sind, können Sie im Portal fortfahren, Ihren ersten Index zu erstellen.

> [!div class="nextstepaction"]
> [Hinzufügen eines Index (Portal)](search-create-index-portal.md)
