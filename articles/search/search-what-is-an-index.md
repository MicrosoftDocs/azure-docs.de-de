---
title: Erstellen eines Suchindex
titleSuffix: Azure Cognitive Search
description: Hier finden Sie eine Einführung in die Indizierungskonzepte und -tools in Azure Cognitive Search, einschließlich Schemadefinitionen und der physischen Datenstruktur.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/15/2020
ms.openlocfilehash: aa7c06c3bad59bad11fa288631042cca86109706
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94701132"
---
# <a name="create-a-basic-search-index-in-azure-cognitive-search"></a>Erstellen eines einfachen Suchindex in Azure Cognitive Search

In Azure Cognitive Search speichert ein *Suchindex* durchsuchbaren Inhalt, der für Volltext- und gefilterte Abfragen verwendet wird. Ein Index wird durch ein Schema definiert und im Dienst gespeichert. Der Datenimport erfolgt in einem zweiten Schritt. 

Indizes enthalten *Dokumente*. Konzeptionell ist ein Dokument eine einzelne Einheit mit durchsuchbaren Daten im Index. Ein Einzelhändler verfügt beispielsweise über ein Dokument für jedes Produkt, eine Nachrichtenagentur über ein Dokument pro Zeitungsartikel usw. So lassen sich diese Konzepte vertrauteren Entsprechungen in der Datenbank zuordnen: Ein *Suchindex* entspricht einer *Tabelle*, und *Dokumente* entsprechen ungefähr den *Zeilen* einer Tabelle.

Die physische Struktur eines Index wird durch das Schema vorgegeben. Wenn dabei ein Feld als „durchsuchbar“ gekennzeichnet ist, wird ein invertierter Index für dieses Feld erstellt. 

Mit den folgenden Tools und APIs können Sie einen Index erstellen:

* Im Azure-Portal mit den Assistenten zum **Hinzufügen von Indizes** oder **Importieren von Daten**
* Mit [Create Index (REST-API)](/rest/api/searchservice/create-index)
* Per [.NET SDK](./search-get-started-dotnet.md)

Für das Lernen ist es einfacher, ein Portal-Tool zu verwenden. Das Portal erzwingt Anforderungen und Schemaregeln für bestimmte Datentypen, z. B. das Deaktivieren von Funktionen zur Volltextsuche bei numerischen Feldern. Wenn Sie über einen funktionierenden Index verfügen, können Sie zum Code wechseln, indem Sie die JSON-Definition mit [Get Index (REST-API)](/rest/api/searchservice/get-index) vom Dienst abrufen und sie der Projektmappe hinzufügen.

## <a name="recommended-workflow"></a>Empfohlener Workflow

Das Vervollständigen bis zu einem endgültigen Indexentwurf ist ein iterativer Prozess. Es ist üblich, im Portal zu beginnen, einen anfänglichen Index zu erstellen, und dann zum Code zu wechseln, um den Index unter die Quellcodeverwaltung zu stellen.

1. Ermitteln Sie, ob Sie [**Daten importieren**](search-import-data-portal.md) verwenden können. Der Assistent führt eine umfassende indexerbasierte Indizierung aus, wenn die Quelldaten einen [unterstützten Datenquellentyp in Azure](search-indexer-overview.md#supported-data-sources) aufweisen.

1. Wenn Sie **Daten importieren** nicht verwenden können, beginnen Sie mit **Index hinzufügen**, um so das Schema zu definieren.

   ![Befehl „Index hinzufügen“](media/search-what-is-an-index/add-index.png "Befehl „Index hinzufügen“")

1. Geben Sie einen Namen und einen Schlüssel an, mit denen die einzelnen Suchdokumente im Index eindeutig identifiziert werden. Der Schlüssel ist erforderlich und muss den Typ „Edm.String“ aufweisen. Sie sollten während des Imports die Zuordnung eines eindeutigen Felds in den Quelldaten zu diesem Feld planen. 

   Im Portal steht Ihnen das Feld `id` für den Schlüssel zur Verfügung. Um die Standard-`id` zu überschreiben, erstellen Sie ein neues Feld (z. B. eine neue Felddefinition namens `HotelId`), und wählen Sie es dann in **Schlüssel** aus.

   ![Ausfüllen der erforderlichen Eigenschaften](media/search-what-is-an-index//field-attributes.png "Ausfüllen der erforderlichen Eigenschaften")

1. Fügen Sie weitere Felder hinzu. Im Portal sehen Sie, welche [Feldattribute](#index-attributes) für die unterschiedlichen Datentypen zur Verfügung stehen. Wenn der Indexentwurf für Sie neu ist, ist dies hilfreich.

   Wenn eingehende Daten hierarchisch sind, weisen Sie einen [komplexen Typ](search-howto-complex-data-types.md) zu, um die geschachtelten Strukturen darzustellen. Das integrierte Beispieldataset „Hotels“ veranschaulicht komplexe Typen anhand einer Adresse (enthält mehrere untergeordnete Felder) mit einer 1:1-Beziehung zu den einzelnen Hotels verwenden und einer komplexen Zimmersammlung, wobei jedem Hotel mehrere Zimmer zugeordnet sind. 

1. Weisen Sie alle [Analysetools](#analyzers) Zeichenfolgenfeldern zu, bevor der Index erstellt wird. Führen Sie die gleichen Schritte für [Vorschlagsfunktionen](#suggesters) aus, wenn Sie die automatische Vervollständigung für bestimmte Felder aktivieren möchten.

1. Klicken Sie auf **Erstellen**, um die physischen Strukturen in Ihrem Suchdienst zu erstellen.

1. Nachdem ein Index erstellt wurde, verwenden Sie weitere Befehle, um Definitionen zu überprüfen oder zusätzliche Elemente hinzuzufügen.

   ![Seite „Index hinzufügen“ mit Attributen nach Datentyp](media/search-what-is-an-index//field-definitions.png "Seite „Index hinzufügen“ mit Attributen nach Datentyp")

1. Laden Sie das Indexschema mit [Get Index (REST-API)](/rest/api/searchservice/get-index) und einem Webtesttool wie [Postman](search-get-started-rest.md) herunter. Sie verfügen nun über eine JSON-Darstellung des Index, die Sie im Code anpassen können.

1. [Laden Sie Ihren Index mit Daten](search-what-is-data-import.md). Die kognitive Azure-Suche akzeptiert JSON-Dokumente. Um Ihre Daten programmgesteuert zu laden, können Sie Postman mit JSON-Dokumenten in der Anforderungsnutzlast verwenden. Wenn Ihre Daten nicht einfach als JSON-Code ausgedrückt werden, erfordert dieser Schritt den meisten Arbeitsaufwand. 

    Nachdem ein Index mit Daten geladen wurde, müssen Sie bei den meisten Änderungen an vorhandenen Feldern den Index löschen und neu erstellen.

1. Fragen Sie Ihren Index ab, überprüfen Sie Ergebnisse, und durchlaufen Sie weiter das Indexschema, bis Sie die Ergebnisse sehen, die Sie erwarten. Sie können [**Suchexplorer**](search-explorer.md) oder Postman verwenden, um Ihren Index abzufragen.

Planen Sie während der Entwicklung häufige Neuerstellungen ein. Da physische Strukturen im Dienst erstellt werden, ist das [Löschen und Neuerstellen von Indizes](search-howto-reindex.md) bei den meisten Änderungen an einer vorhandenen Felddefinition erforderlich. Sie sollten erwägen, mit einer Teilmenge Ihrer Daten zu arbeiten, damit Neuerstellungen schneller gehen. 

> [!Tip]
> Anstelle des Portals empfiehlt es sich, im Code zu arbeiten, wenn Sie den Indexentwurf und den Datenimport gleichzeitig bearbeiten möchten. Als Alternative sind Tools wie [Postman und Visual Studio Code](search-get-started-rest.md) hilfreich zum Testen eines Proof of Concept, wenn Entwicklungsprojekte sich noch in frühen Phasen befinden. Sie können inkrementelle Änderungen an einer Indexdefinition im Anforderungstext vornehmen und dann die Anforderung an Ihren Dienst senden, um einen Index mit einem aktualisierten Schema neu zu erstellen.

## <a name="index-schema"></a>Indexschema

Ein Index muss einen Namen und ein designiertes Schlüsselfeld (vom Typ „Edm.string“) in der Feldsammlung aufweisen. Die [*Feldsammlung*](#fields-collection) ist typischerweise der größte Teil eines Index, wobei jedes Feld benannt, typisiert und mit zulässigen Verhaltensweisen versehen wird, die bestimmen, wie es verwendet wird. 

Weitere Elemente sind [Vorschlagsfunktionen](#suggesters), [Bewertungsprofile](#scoringprofiles) und [Analysetools](#analyzers), die zum Verarbeiten von Zeichenfolgen in Token gemäß den linguistischen Regeln oder anderen vom Analysetool unterstützten Merkmalen verwendet werden, sowie [CORS-Einstellungen (Cross-Origin Remote Scripting)](#corsoptions).

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

Felder haben einen Namen, einen Typ, der die gespeicherten Daten klassifiziert, und Attribute, die angeben, wie das Feld verwendet wird.

### <a name="data-types"></a>Datentypen

| type | BESCHREIBUNG |
|------|-------------|
| Edm.String |Text, der optional für die Volltextsuche (Worttrennung, Wortstammerkennung usw.) mit einem Token versehen werden kann. |
| Collection(Edm.String) |Eine Liste von Zeichenfolgen, die für die Volltextsuche mit einem Token versehen werden können. Für die Anzahl der Elemente in einer Sammlung gibt es keine Obergrenze, allerdings gilt die Obergrenze für die Größe der Nutzlast von 16 MB auch für Sammlungen. |
| Edm.Boolean |Enthält TRUE/FALSE-Werte |
| Edm.Int32 |32-Bit-Ganzzahlwerte |
| Edm.Int64 |64-Bit-Ganzzahlwerte |
| Edm.Double |Numerische Daten mit doppelter Genauigkeit |
| Edm.DateTimeOffset |Datums-/Uhrzeitwerte im OData V4-Format (z.B. `yyyy-MM-ddTHH:mm:ss.fffZ` oder `yyyy-MM-ddTHH:mm:ss.fff[+/-]HH:mm`). |
| Edm.GeographyPoint |Ein Punkt, der einen weltweiten geografischen Standort darstellt |

Weitere Informationen finden Sie unter [Unterstützte Datentypen](/rest/api/searchservice/Supported-data-types).

<a name="index-attributes"></a>

### <a name="attributes"></a>Attributes

Feldattribute bestimmen, wie ein Feld verwendet wird, z.B. ob es in der Volltextsuche, in der Facettennavigation, für Sortiervorgänge usw. verwendet wird. 

Zeichenfolgenfelder werden häufig als „durchsuchbar“ und „abrufbar“ gekennzeichnet. Zu den Feldern zum Eingrenzen der Suchergebnisse gehören „sortierbar“, „filterbar“ und „facettenreich“.

|Attribut|BESCHREIBUNG|  
|---------------|-----------------|  
|„durchsuchbar“ |Volltextsuche ist möglich, unterliegt einer lexikalischen Analyse, z.B. Worttrennung, während der Indizierung. Wenn Sie ein durchsuchbares Feld auf einen Wert wie „sunny day“ festlegen, wird es intern in die einzelnen Token „sunny“ und „day“ unterteilt. Weitere Informationen finden Sie unter [Funktionsweise der Volltextsuche](search-lucene-query-architecture.md).|  
|„filterbar“ |Wird in $filter-Abfragen angegeben. Filterbare Felder vom Typ `Edm.String` oder `Collection(Edm.String)` werden keiner Worttrennung unterzogen, sodass nur nach exakten Übereinstimmungen gesucht wird. Beispiel: Wenn Sie ein solches Feld „f“ auf „sunny day“ festlegen, werden mit `$filter=f eq 'sunny'` keine Übereinstimmungen gefunden, während Sie mit `$filter=f eq 'sunny day'` Suchergebnisse erhalten. |  
|„sortierbar“ |Standardmäßig sortiert das System Ergebnisse nach Bewertung, aber Sie können die Sortierung auf Grundlage von Feldern in den Dokumenten konfigurieren. Felder vom Typ `Collection(Edm.String)` können nicht „sortierbar“ sein. |  
|„facettenreich“ |Wird in der Regel in einer Darstellung der Suchergebnisse verwendet, die eine Trefferanzahl nach Kategorie umfasst (z.B. Hotels in einer bestimmten Stadt). Diese Option kann nicht für Felder vom Typ `Edm.GeographyPoint` verwendet werden. Felder vom Typ `Edm.String`, die „filterbar“, „sortierbar“ oder „facettenreich“ sind, dürfen höchstens 32 KB lang sein. Weitere Details finden Sie unter [Erstellen des Index (REST API)](/rest/api/searchservice/create-index).|  
|„Schlüssel“ |Eindeutiger Bezeichner für Dokumente im Index. Es muss genau ein Feld als Schlüsselfeld ausgewählt werden, und es muss vom Typ `Edm.String` sein.|  
|„abrufbar“ |Legt fest, ob das Feld in einem Suchergebnis zurückgegeben werden kann. Dies ist hilfreich, wenn Sie ein Feld (z.B. *Gewinnspanne*) zum Filtern, Sortieren oder Bewerten verwenden möchten, das Feld jedoch für den Endbenutzer nicht sichtbar sein soll. Dieses Attribut muss für `key`-Felder auf `true` gesetzt sein.|  

Obwohl Sie jederzeit neue Felder hinzufügen können, sind vorhandene Felddefinitionen für die Lebensdauer des Index gesperrt. Aus diesem Grund verwenden Entwickler in der Regel das Portal zum Erstellen einfacher Indizes und zum Testen von Ideen. Oder sie verwenden Seiten des Portals, um eine Einstellung zu suchen. Häufige Iterationen über einen Indexentwurf sind effizienter, wenn Sie einem codebasierten Ansatz folgen, sodass Sie den Index einfach neu erstellen können.

> [!NOTE]
> Die APIs, die Sie zum Erstellen eines Index verwenden, verfügen über unterschiedliches Standardverhalten. Für die [REST-APIs](/rest/api/searchservice/Create-Index) sind die meisten Attribute standardmäßig aktiviert (z. B. sind „durchsuchbar“ und „abrufbar“ für Zeichenfolgenfelder auf TRUE festgelegt). Sie müssen sie häufig nur festlegen, wenn Sie sie deaktivieren möchten. Für das .NET SDK gilt das Gegenteil. Für jede Eigenschaft, die Sie nicht explizit festlegen, wird das entsprechende Suchverhalten standardmäßig deaktiviert, sofern Sie es nicht ausdrücklich aktivieren.

## `analyzers`

Das Analysetoolelement legt den Namen des Sprachanalysetools fest, das für das Feld verwendet werden soll. Weitere Informationen zu den verfügbaren Analysetools finden Sie unter [Hinzufügen von Analysetools zu einem Index der kognitiven Azure-Suche](search-analyzers.md). Analysetools können nur mit durchsuchbaren Feldern verwendet werden. Sobald das Analysetool einem Feld zugewiesen ist, kann es nicht geändert werden, solange Sie den Index nicht neu erstellen.

## `suggesters`

Eine Vorschlagsfunktion ist ein Abschnitt des Schemas, das definiert, welche Felder in einem Index verwendet werden, um AutoVervollständigen oder Eingabevorschläge für Abfragen bei Suchvorgängen zu unterstützen. In der Regel werden während der Eingabe einer Suchabfrage Teilzeichenfolgen an die [Suggestions-REST-API](/rest/api/searchservice/suggestions) gesendet. Diese gibt daraufhin eine Reihe vorgeschlagener Dokumente oder Ausdrücke zurück. 

Mit Feldern, die einer Vorschlagsfunktion hinzugefügt werden, werden Eingabevorschläge von Suchbegriffen erstellt. Alle Suchbegriffe werden während der Indizierung erstellt und separat gespeichert. Weitere Informationen zum Erstellen einer Vorschlagsfunktionsstruktur finden Sie unter [Hinzufügen von Vorschlagsfunktionen zu einem Azure Search-Index](index-add-suggesters.md).

## `corsOptions`

Clientseitiger JavaScript-Code kann standardmäßig keine APIs aufrufen, da der Browser jegliche ursprungsübergreifenden Anforderungen verhindert. Um ursprungsübergreifende Abfragen für Ihren Index zu ermöglichen, aktivieren Sie CORS (Cross-Origin Resource Sharing), indem Sie das Attribut **corsOptions** festlegen. Aus Sicherheitsgründen wird CORS nur von Abfrage-APIs unterstützt. 

Die folgenden Optionen können für CORS festgelegt werden:

+ **allowedOrigins** (erforderlich): Dies ist eine Liste der Ursprünge, denen Zugriff auf Ihren Index gewährt wird. Dies bedeutet, dass jeglicher von diesen Ursprüngen bereitgestellte Javascript-Code Indexabfragen durchführen kann (sofern er den richtigen API-Schlüssel angibt). Ursprünge werden in der Regel im Format `protocol://<fully-qualified-domain-name>:<port>` angegeben, wobei `<port>` häufig weggelassen wird. Weitere Informationen finden Sie unter [Cross-Origin Resource Sharing, CORS (Wikipedia)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing).

  Wenn Sie den Zugriff auf alle Ursprünge zulassen möchten, beziehen Sie `*` als ein einzelnes Element in das Array **allowedOrigins** ein. *Dies ist keine empfohlene Vorgehensweise für Produktionssuchdienste*, ist für Entwicklung und Debugging jedoch oft nützlich.

+ **maxAgeInSeconds** (optional): Von Browsern wird dieser Wert verwendet, um die Dauer (in Sekunden) des Zwischenspeicherns von CORS-Preflight-Antworten zu ermitteln. Dies muss eine positive ganze Zahl sein. Mit dem Wert steigt auch die Leistung, aber es dauert auch länger, bis CORS-Richtlinienänderungen in Kraft treten. Wenn diese Einstellung nicht festgelegt ist, gilt die Standarddauer von 5 Minuten.

## `scoringProfiles`

Ein [Bewertungsprofil](index-add-scoring-profiles.md) ist ein Abschnitt des Schemas, der benutzerdefinierte Bewertungsverhalten definiert, mit denen Sie beeinflussen können, welche Elemente weiter oben in den Suchergebnissen angezeigt werden. Bewertungsprofile bestehen aus Feldgewichtungen und Funktionen. Um sie verwenden zu können, fügen Sie in die Abfragezeichenfolge den Profilnamen ein.

Ein standardmäßiges Bewertungsprofil berechnet im Hintergrund für jedes Element eines Ergebnissatzes eine Suchbewertung. Sie können das interne, unbenannte Bewertungsprofil verwenden. Oder legen Sie für **defaultScoringProfile** ein benutzerdefiniertes Profil fest, das standardmäßig aufgerufen wird, wenn für eine Abfragezeichenfolge kein benutzerdefiniertes Profil angegeben wurde.

<a name="index-size"></a>

## <a name="attributes-and-index-size-storage-implications"></a>Attribute und Indexgröße (Auswirkungen auf den Speicher)

Die Größe eines Indexes wird durch die Größe der von Ihnen hochgeladenen Dokumente sowie durch die Indexkonfiguration bestimmt, also beispielsweise dadurch, ob Sie Vorschlagsfunktionen einbeziehen und wie Sie Attribute für einzelne Felder festlegen. 

Der folgende Screenshot veranschaulicht die Indexspeichermuster aus verschiedenen Kombinationen von Attributen. Der Index basiert auf dem **Real Estate-Beispielindex**, den Sie ganz einfach mithilfe des Datenimport-Assistenten erstellen können. Obwohl die Indexschemas nicht angezeigt werden, können Sie die auf dem Indexnamen basierenden Attribute ableiten. Beispielsweise ist für den *realestate-searchable*-Index das searchable-Attribut ausgewählt und sonst nichts, für den *realestate-retrievable*-Index ist das retrievable-Attribut ausgewählt und nichts anderes usw.

![Indexgröße basierend auf der Attributauswahl](./media/search-what-is-an-index/realestate-index-size.png "Indexgröße basierend auf der Attributauswahl")

Obwohl diese Indexvarianten künstlich sind, können wir sie für umfassende Vergleiche des Einflusses von Attributen auf Speicher nutzen. Setzt die Einstellung „abrufbar“ die Indexgröße herauf? Nein. Setzt das Hinzufügen von Feldern zu einer **Vorschlagsfunktion** die Indexgröße herauf? Ja.

Indizes, die das Filtern und Sortieren unterstützen, sind proportional größer als Indizes, die nur die Volltextsuche unterstützen. Der Grund ist, dass bei Filter- und Sortiervorgängen exakte Übereinstimmungen gesucht werden. Dabei wird vorausgesetzt, dass ausführliche Zeichenfolgen vorhanden sind. Im Gegensatz dazu verwenden durchsuchbare Felder, die Volltextabfragen unterstützen, invertierte Indizes, die mit tokenisierten Begriffen aufgefüllt werden, die weniger Speicherplatz als ganze Dokumente benötigen. 

> [!Note]
> Die Speicherarchitektur gilt als Implementierungsdetail der kognitiven Azure-Suche und kann ohne vorherige Ankündigung geändert werden. Es gibt keine Garantie, dass das aktuelle Verhalten in der Zukunft beibehalten wird.

## <a name="next-steps"></a>Nächste Schritte

Wenn Sie mit den Grundlagen der Indexerstellung vertraut sind, können Sie im Portal fortfahren, Ihren ersten Index zu erstellen. Es wird empfohlen, mit dem Assistenten zum **Importieren von Daten** zu beginnen. Dabei können Sie als gehostete Datenquellen *realestate-us-sample* oder *hotels-sample* auswählen.

> [!div class="nextstepaction"]
> [Datenimport-Assistent (Portal)](search-get-started-portal.md)

Für beide Datasets kann der Assistent ein Indexschema ableiten, die Daten importieren und einen durchsuchbaren Index ausgeben, den Sie mit dem Such-Explorer abfragen können. Sie finden diese Datenquellen auf der Seite **Mit Ihren Daten verbinden** des Assistenten zum **Importieren von Daten**.

   ![Erstellen eines Beispielindex](media/search-what-is-an-index//import-wizard-sample-data.png "Erstellen eines Beispielindex")