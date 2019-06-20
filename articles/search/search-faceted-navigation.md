---
title: Implementieren der Facettennavigation in einer Kategorienhierarchie – Azure Search
description: Fügen Sie die Facettennavigation Anwendungen hinzu, die in Azure Search integriert sind, einem in Microsoft Azure gehosteten Cloudsuchdienst.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: c032dbc528ed5034280d0ecb4c95700b51869991
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65793619"
---
# <a name="how-to-implement-faceted-navigation-in-azure-search"></a>Implementieren der Facettennavigation in Azure Search
Bei der Facettennavigation handelt es sich um einen Filtermechanismus für die selbstständige Drilldownnavigation in Suchanwendungen. Der Begriff „Facettennavigation“ ist Ihnen zwar möglicherweise nicht bekannt, aber Sie haben diese wahrscheinlich bereits verwendet. Die Facettennavigation ist im Prinzip nichts weiter als die Kategorien, die zum Filtern der Ergebnisse verwendet werden.

 ![Azure Search-Demo „Job Portal“](media/search-faceted-navigation/azure-search-faceting-example.png "Azure Search-Demo „Job Portal“")

Die Facettennavigation ist ein alternativer Einstiegspunkt für die Suche. Sie bietet eine praktische Alternative zum manuellen Eintippen komplexer Suchausdrücke. Facetten unterstützen Sie bei der Suche und stellen sicher, dass Sie Ergebnisse erhalten. Als Entwickler können Sie mithilfe von Facets besonders hilfreiche Suchkriterien für die Navigation in Ihrem Suchindex verfügbar machen. Bei Onlinehändlern basiert die Facettennavigation häufig auf Marken, Abteilungen (Kinderschuhe), Größe, Preis, Beliebtheit und Bewertungen. 

Die Implementierung der Facettennavigation ist abhängig von der jeweiligen Suchtechnologie. In Azure Search wird die Facettennavigation zur Abfragezeit mithilfe von Feldern erstellt, die Sie zuvor in Ihrem Schema attributiert wurden.

-   In den von Ihrer Anwendung erstellten Abfragen müssen *Facettenabfrageparameter* gesendet werden, um die verfügbaren Facettenfilterwerte für das Dokumentresultset zu erhalten.

-   Zur Eingrenzung des Dokumentresultsets muss die Anwendung auch einen `$filter`-Ausdruck anwenden.

Bei Ihrer Anwendungsentwicklung entfällt ein Großteil der Arbeit auf die Erstellung von Code für Abfragen. Der Dienst stellt viele der Verhaltensweisen der Anwendung bereit, die Sie von der Facettennavigation erwarten würden – einschließlich integrierter Unterstützung für das Definieren von Bereichen und das Abrufen der Anzahl von Facettenergebnissen. Darüber hinaus bietet der Dienst sinnvolle Standardwerte zur Vermeidung umständlicher Navigationsstrukturen. 

## <a name="sample-code-and-demo"></a>Beispielcode und Demo
In diesem Artikel wird ein Stellenportal als Beispiel verwendet. Das Beispiel wird als eine ASP.NET MVC-Anwendung implementiert.

-   Die Arbeitsdemo können Sie online unter [Azure Search-Demo „Job Portal“](https://azjobsdemo.azurewebsites.net/) anzeigen und testen.

-   Laden Sie den Code aus dem [Repository „Azure-Beispiele“ in GitHub](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs) herunter.

## <a name="get-started"></a>Erste Schritte
Wenn Sie noch keine Erfahrung mit der Suchentwicklung gesammelt haben, können Sie sich die Facettennavigation am besten als eine Lösung vorstellen, die die Möglichkeiten für die selbstständige Suche aufzeigt. Die Facettennavigation ist eine Art Drilldownsuche, die auf vordefinierten Filtern basiert, und dient zur schnellen Eingrenzung der Suchergebnisse durch Point-and-Click-Aktionen. 

### <a name="interaction-model"></a>Interaktionsmodell

Die Suchumgebung für die Facettennavigation ist iterativ und kann als Sequenz von Abfragen betrachtet werden, die infolge von Benutzeraktionen ausgeführt werden.

Der Ausgangspunkt ist eine Anwendungsseite mit Facettennavigation (üblicherweise in einem Randbereich). Die Facettennavigation wird häufig als Baumstruktur mit Kontrollkästchen für die jeweiligen Werte oder mit anklickbarem Text dargestellt. 

1. Die Facettennavigationsstruktur wird in einer an Azure Search gesendeten Abfrage mithilfe entsprechender Abfrageparameter angegeben. So kann die Abfrage beispielsweise `facet=Rating` enthalten und ggf. mit einer `:values`- oder `:sort`-Option versehen werden, um die Darstellung weiter zu verfeinern.
2. Die Darstellungsschicht rendert unter Verwendung der in der Anforderung angegebenen Facetten eine Suchseite mit Facettennavigation.
3. Bei Verwendung einer bewertungsbasierten Facettennavigationsstruktur klicken Sie auf „4“, um anzugeben, dass nur Produkte mit der Bewertung 4 oder höher angezeigt werden sollen. 
4. Im Gegenzug sendet die Anwendung eine Abfrage mit `$filter=Rating ge 4` 
5. Die Darstellungsschicht aktualisiert die Seite und zeigt ein reduziertes Resultset an, das nur Elemente enthält, die den neuen Kriterien entsprechen (in diesem Fall Produkte mit der Bewertung 4 oder höher).

Eine Facette ist ein Abfrageparameter, darf aber nicht mit einer Abfrageeingabe verwechselt werden. Sie wird nie als Auswahlkriterium in einer Abfrage verwendet. Stellen Sie sich Facettenabfrageparameter stattdessen als Eingaben für die Navigationsstruktur vor, die in der Antwort zurückgegeben wird. Azure Search prüft für jeden angegebenen Facettenabfrageparameter, wie viele Dokumente in den Teilergebnissen für die einzelnen Facettenwerte enthalten sind.

Beachten Sie `$filter` in Schritt 4. Der Filter ist ein wichtiger Aspekt der Facettennavigation. Facetten und Filter sind in der API zwar unabhängig, werden aber beide benötigt, um das gewünschte Ergebnis zu erzielen. 

### <a name="app-design-pattern"></a>App-Entwurfsmuster

Im Anwendungscode werden Facettenabfrageparameter für die Rückgabe der Facettennavigationsstruktur und der Facettenergebnisse sowie ein $filter-Ausdruck verwendet.  Der Filterausdruck behandelt das Klickereignis auf den Facettenwert. Stellen Sie sich den `$filter` -Ausdruck als CodeBehind für die tatsächliche Eingrenzung der an die Darstellungsschicht zurückgegebenen Suchergebnisse vor. Bei Verwendung einer farbbasierten Facette wird das Klicken auf die Farbe Rot mithilfe eines `$filter` -Ausdrucks implementiert, der nur Elemente mit der Farbe Rot auswählt. 

### <a name="query-basics"></a>Grundlagen der Abfrage

In Azure Search wird eine Anforderung mithilfe von Abfrageparametern angegeben. (Eine Beschreibung der einzelnen Parameter finden Sie unter [Durchsuchen von Dokumenten](https://docs.microsoft.com/rest/api/searchservice/Search-Documents).) Keiner der Abfrageparameter ist erforderlich, Sie benötigen jedoch mindestens einen, damit die Abfrage gültig ist.

Genauigkeit (d.h. die Möglichkeit zum Herausfiltern irrelevanter Treffer), wird durch mindestens einen der folgenden Ausdrücke erzielt:

-   **search=**  
    Der Wert dieses Parameters stellt den Suchausdruck dar. Hierbei kann es sich um ein einfaches Textelement, aber auch um einen komplexen Suchausdruck mit mehreren Begriffen und Operatoren handeln. Auf dem Server wird ein Suchbegriff für die Volltextsuche verwendet. Dabei werden durchsuchbare Felder im Index nach passenden Begriffen durchsucht und nach Rang sortierte Ergebnisse zurückgegeben. Wenn Sie `search` auf NULL festlegen, wird die Abfrage für den gesamten Index (`search=*`) ausgeführt. In diesem Fall sind andere Elemente der Abfrage – etwa ein `$filter`-Element oder ein Bewertungsprofil – die primären Faktoren, die bestimmen, welche Dokumente zurückgegeben werden `($filter`), und in welcher Reihenfolge (`scoringProfile` oder `$orderby`) sie zurückgegeben werden.

-   **$filter=**  
    Ein Filter ist ein leistungsstarker Mechanismus für die Einschränkung von Suchergebnissen auf der Grundlage der Werte bestimmter Dokumentattribute. Ein `$filter`-Element wird ganz zu Beginn ausgewertet. Anschließend werden mithilfe einer Facettenlogik die verfügbaren Werte und die entsprechende Anzahl für den jeweiligen Wert generiert.

Komplexe Suchausdrücke wirken sich nachteilig auf die Abfrageleistung aus. Nutzen Sie möglichst durchdachte Filterausdrücke, um die Genauigkeit und die Abfrageleistung zu verbessern.

Die folgende Gegenüberstellung eines komplexen Suchausdrucks und eines Suchausdrucks mit Filterausdruck veranschaulicht, wie ein Filter für mehr Genauigkeit sorgt:

-   `GET /indexes/hotel/docs?search=lodging budget +Seattle –motel +parking`
-   `GET /indexes/hotel/docs?search=lodging&$filter=City eq ‘Seattle’ and Parking and Type ne ‘motel’`

In beiden Fällen handelt es sich um eine gültige Abfrage, mit der zweiten kann jedoch besser nach Unterkünften mit Parkmöglichkeiten in Seattle gesucht werden, bei denen es sich nicht um ein Motel handelt.
-   Die erste Abfrage basiert darauf, dass in Zeichenfolgenfeldern wie „Name“, „Beschreibung“ oder in anderen Feldern mit durchsuchbaren Daten bestimmte Wörter verwendet oder nicht verwendet werden.
-   Die zweite Abfrage sucht nach exakten Übereinstimmungen für strukturierte Daten und ist wahrscheinlich deutlich genauer.

Stellen Sie in Anwendungen mit Facettennavigation sicher, dass jede Benutzeraktion mit einer Facettennavigationsstruktur eine Einschränkung der Suchergebnisse zur Folge hat. Verwenden Sie zur Einschränkung der Ergebnisse einen Filterausdruck.

<a name="howtobuildit"></a>

## <a name="build-a-faceted-navigation-app"></a>Erstellen einer App mit Facettennavigation
Sie implementieren die Facettennavigation mit Azure Search in Ihrem Anwendungscode, mit dem die Suchanforderung erstellt wird. Die Facettennavigation stützt sich auf Elemente im Schema, die Sie zuvor festgelegt haben.

In Ihrem Suchindex ist das `Facetable [true|false]`-Indexattribut vordefiniert. Dieses wird auf bestimmte Felder festgelegt, um deren Verwendung in einer Facettennavigationsstruktur zu aktivieren oder zu deaktivieren. Ohne `"Facetable" = true` kann ein Feld nicht für die Facettennavigation verwendet werden.

Die Darstellungsschicht in Ihrem Code stellt die Benutzeroberfläche bereit. Sie führt die Bestandteile der Facettennavigation (wie Beschriftung, Werte, Kontrollkästchen und die Anzahl) auf. Die REST-API von Azure Search ist plattformunabhängig und kann somit mit jeder beliebigen Programmiersprache und Plattform verwendet werden. Wichtig: Verwenden Sie Benutzeroberflächenelemente, die eine inkrementelle Aktualisierung unterstützen und bei denen jeweils der Benutzeroberflächenzustand aktualisiert wird, wenn weitere Facetten ausgewählt werden. 

Zur Abfragezeit erstellt Ihr Anwendungscode eine Anforderung mit `facet=[string]`. Dieser Anforderungsparameter gibt das Feld an, das der Facette zugrunde liegen soll. Eine Abfrage kann mehrere Facetten (Beispiel: `&facet=color&facet=category&facet=rating`) umfassen. Diese müssen jeweils durch ein kaufmännisches Und-Zeichen (&) voneinander getrennt werden.

Der Anwendungscode muss auch einen `$filter` -Ausdruck konstruieren, um Klickereignisse in einer Facettennavigation zu behandeln. Der `$filter` -Ausdruck verwendet die Facettenwerte als Filterkriterien und grenzt so die Suchergebnisse ein.

Azure Search gibt die Suchergebnisse basierend auf mindestens einem eingegeben Begriff sowie Aktualisierungen für die Facettennavigationsstruktur zurück. Bei der Facettennavigation in Azure Search handelt es sich um eine einstufige Konstruktion mit Facettenwerten und der jeweiligen Anzahl gefundener Ergebnisse.

In den folgenden Abschnitten wird ausführlicher auf die Erstellung der einzelnen Komponenten eingegangen.

<a name="buildindex"></a>

## <a name="build-the-index"></a>Erstellen des Index
Die Facettenerstellung wird mithilfe des folgenden Indexattributs auf Feldbasis im Index aktiviert: `"Facetable": true`.  
Alle Feldtypen, die ggf. für die Facettennavigation verwendet werden können, sind standardmäßig als `Facetable` konfiguriert. Zu diesen Feldtypen zählen `Edm.String`, `Edm.DateTimeOffset` sowie alle numerischen Feldtypen. (Mit Ausnahme von `Edm.GeographyPoint` sind eigentlich alle Feldtypen für die Facettennavigation geeignet.) 

Bei der Indexerstellung empfiehlt es sich, die Facettenerstellung für Felder, die nicht als Facetten verwendet werden sollen, ausdrücklich zu deaktivieren.  Insbesondere Zeichenfolgenfelder für Singleton-Werte (wie etwa eine ID oder ein Produktname) sollten auf `"Facetable": false` festgelegt werden, damit sie nicht versehentlich für die Facettennavigation verwendet werden. Die Deaktivierung der Facettenerstellung für Elemente, die nicht als Facetten verwendet werden, verringert den Umfang des Index und verbessert in der Regel die Leistung.

Die folgende Abbildung zeigt einen Teil des Schemas für die Beispiel-App „Job Portal“, das um einige Attribute gekürzt wurde, um die Größe zu reduzieren:

```json
{
  ...
  "name": "nycjobs",
  "fields": [
    { “name”: "id",                 "type": "Edm.String",              "searchable": false, "filterable": false, ... "facetable": false, ... },
    { “name”: "job_id",             "type": "Edm.String",              "searchable": false, "filterable": false, ... "facetable": false, ... },
    { “name”: "agency",              "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { “name”: "posting_type",        "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { “name”: "num_of_positions",    "type": "Edm.Int32",              "searchable": false, "filterable": true, ...  "facetable": true, ...  },
    { “name”: "business_title",      "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { “name”: "civil_service_title", "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { “name”: "title_code_no",       "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { “name”: "level",               "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { “name”: "salary_range_from",   "type": "Edm.Int32",              "searchable": false, "filterable": true, ...  "facetable": true, ...  },
    { “name”: "salary_range_to",     "type": "Edm.Int32",              "searchable": false, "filterable": true, ...  "facetable": true, ...  },
    { “name”: "salary_frequency",    "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { “name”: "work_location",       "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
…
    { “name”: "geo_location",        "type": "Edm.GeographyPoint",     "searchable": false, "filterable": true, ...  "facetable": false, ... },
    { “name”: "tags",                "type": "Collection(Edm.String)", "searchable": true,  "filterable": true, ...  "facetable": true, ...  }
  ],
…
}
```

Wie Sie im Beispielschema sehen können, ist `Facetable` für Zeichenfolgenfelder, die nicht als Facetten verwendet werden sollen (wie etwa ID-Werte), deaktiviert ist. Die Deaktivierung der Facettenerstellung für Elemente, die nicht als Facetten verwendet werden, verringert den Umfang des Index und verbessert in der Regel die Leistung.

> [!TIP]
> Es empfiehlt sich, für jedes Feld den vollständigen Indexattributsatz einzubeziehen. `Facetable` ist zwar standardmäßig für fast alle Felder aktiviert, das bewusste Festlegen der einzelnen Attribute kann jedoch beim Nachvollziehen der einzelnen Schemaentscheidungen hilfreich sein. 

<a name="checkdata"></a>

## <a name="check-the-data"></a>Überprüfen der Daten
Die Qualität Ihrer Daten hat direkten Einfluss darauf, ob die Facettennavigationsstruktur erwartungsgemäß funktioniert. Sie beeinflusst zudem die Effektivität bei der Erstellung von Filtern zur Eingrenzung des Resultsets.

Wenn die Facet auf der Marke oder auf dem Preis basieren soll, muss jedes Dokument gültige, einheitliche und produktive Filteroptionswerte für *BrandName* und *ProductPrice* aufweisen.

Hier finden Sie einige Erinnerungen, wofür ein Scrubbing durchgeführt werden sollte:

* Überlegen Sie sich bei jedem Feld, das als Facette fungieren soll, ob es Werte enthält, die als Filter für eine selbstständige Suche geeignet sind. Die Werte müssen kurz, aussagekräftig und ausreichend unterschiedlich sein, um eine eindeutige Entscheidung zwischen verschiedenen Optionen treffen zu können.
* Tippfehler oder fast übereinstimmende Werte: Wenn die Facette auf der Farbe basiert und die Feldwerte „Orange“ und „Ornage“ (Tippfehler) umfasst, werden bei einer Facette, die auf dem Feld „Color“ basiert, beide Optionen verwendet.
* Auch gemischte Groß-/Kleinschreibung kann die Facettennavigation durcheinander bringen, da „orange“ und „Orange“ als zwei unterschiedliche Werte angezeigt werden. 
* Singular- und Pluralformen des gleichen Werts können ebenfalls zu separaten Facetten führen.

Für eine effektive Facettennavigation ist daher eine sorgfältige Datenvorbereitung unerlässlich.

<a name="presentationlayer"></a>

## <a name="build-the-ui"></a>Erstellen der Benutzeroberfläche
Auf der Grundlage der Darstellungsschicht können Sie Anforderungen ermitteln, die andernfalls womöglich übersehen werden, sowie Funktionen nachvollziehen, die für Suchvorgänge unverzichtbar sind.

Für die Facettennavigation gilt: Ihre Web- oder Anwendungsseite zeigt die Facettennavigationsstruktur an, erkennt Benutzereingaben auf der Seite und fügt die geänderten Elemente ein. 

Bei Webanwendungen wird in der Darstellungsschicht üblicherweise AJAX verwendet, da damit inkrementelle Änderungen aktualisiert werden können. Alternativ können Sie auch ASP.NET MVC oder eine andere Visualisierungsplattform verwenden, die eine HTTP-Verbindung mit einem Azure Search-Dienst herstellen kann. Bei der in diesem Artikel verwendeten Beispielanwendung – der **Azure Search-Demo „Job Portal“** handelt es sich um eine ASP.NET MVC-Anwendung.

In diesem Beispiel ist die Facettennavigation in Suchergebnisseite integriert. Das folgende Beispiel stammt aus der Datei `index.cshtml` der Beispielanwendung und zeigt die statische HTML-Struktur zum Anzeigen einer Facettennavigation auf der Suchergebnisseite. Die Liste der Facetten wird erstellt oder dynamisch neu erstellt, wenn Sie einen Suchbegriff übermitteln oder eine Facette aktivieren bzw. deaktivieren.

```html
<div class="widget sidebar-widget jobs-filter-widget">
  <h5 class="widget-title">Filter Results</h5>
    <p id="filterReset"></p>
    <div class="widget-content">

      <h6 id="businessTitleFacetTitle">Business Title</h6>
      <ul class="filter-list" id="business_title_facets">
      </ul>

      <h6>Location</h6>
      <ul class="filter-list" id="posting_type_facets">
      </ul>

      <h6>Posting Type</h6>
      <ul class="filter-list" id="posting_type_facets"></ul>

      <h6>Minimum Salary</h6>
      <ul class="filter-list" id="salary_range_facets">
      </ul>

  </div>
</div>
```

Der folgende Codeausschnitt aus der `index.cshtml` Seite erstellt dynamisch den HTML-Code, um die erste Facette – Berufsbezeichnung – anzuzeigen. Ähnliche Funktionen erstellen dynamisch den HTML-Code für die anderen Facetten. Jede Facette weist eine Bezeichnung und einen Zähler auf, der die Anzahl von für dieses Facettenergebnis gefundenen Elemente anzeigt.

```js
function UpdateBusinessTitleFacets(data) {
  var facetResultsHTML = '';
  for (var i = 0; i < data.length; i++) {
    facetResultsHTML += '<li><a href="javascript:void(0)" onclick="ChooseBusinessTitleFacet(\'' + data[i].Value + '\');">' + data[i].Value + ' (' + data[i].Count + ')</span></a></li>';
  }

  $("#business_title_facets").html(facetResultsHTML);
}
```

> [!TIP]
> Denken Sie bei der Entwicklung der Suchergebnisseite daran, einen Mechanismus zum Deaktivieren von Facetten hinzuzufügen. Wenn Sie Kontrollkästchen hinzufügen, können Sie ganz einfach anzeigen, wie die Filter gelöscht werden. Bei einem anderen Layout benötigen Sie unter Umständen eine Breadcrumb-Struktur oder einer anderen kreativen Ansatz. Beispielsweise können Sie in der Stellenportal-Beispielanwendung auf das `[X]` hinter einer ausgewählte Facette klicken, um sie zu löschen.

<a name="buildquery"></a>

## <a name="build-the-query"></a>Erstellen der Abfrage
Der Code, den Sie zum Erstellen von Abfragen schreiben, muss alle Bestandteile einer gültigen Abfrage umfassen. Hierzu zählen unter anderem Suchausdrücke, Facetten, Filter und Bewertungsprofile (also alles, was für die Erstellung einer Anforderung benötigt wird). In diesem Abschnitt erfahren Sie, wo Facetten in eine Abfrage eingefügt und wie Filter mit Facetten kombiniert werden, um Resultsets einzugrenzen.

Beachten Sie, dass Facetten ein integraler Bestandteil dieser Beispielanwendung sind. Die Suchfunktion in der Demo „Job Portal“ basiert auf Facettennavigation und Filtern. Die Bedeutung der Facettennavigation wird durch ihre gut sichtbare Platzierung auf der Seite hervorgehoben. 

Ein Beispiel ist häufig ein guter Ausgangspunkt. Das folgende Beispiel stammt aus der Datei `JobsSearch.cs` und erstellt eine Anforderung, die eine Facettennavigation auf der Grundlage von Berufsbezeichnung, Standort, Buchungsart und Mindestgehalt erstellt. 

```cs
SearchParameters sp = new SearchParameters()
{
  ...
  // Add facets
  Facets = new List<String>() { "business_title", "posting_type", "level", "salary_range_from,interval:50000" },
};
```

Ein Facettenabfrageparameter wird auf ein Feld festgelegt und kann abhängig vom Datentyp durch eine kommagetrennte Liste mit `count:<integer>`, `sort:<>`, `interval:<integer>` und `values:<list>` weiter parametrisiert werden. Für numerische Daten wird beim Einrichten von Bereichen eine Werteliste unterstützt. Ausführlichere Informationen zur Verwendung finden Sie unter [Dokumente durchsuchen (Azure Search-API)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) .

Neben Facetten muss die von Ihrer Anwendung erstellte Anforderung auch Filter zum Einschränken der auf der Grundlage der Facettenwertauswahl infrage kommenden Dokumente erstellen. Für ein Fahrradgeschäft bietet Facettennavigation bietet Hinweise zu Fragen wie *Welche Farben, Hersteller und Fahrradtypen sind verfügbar?* . Filtern Sie Antworten auf Fragen wie *Welche Fahrräder in diesem Preisspanne sind rote Mountainbikes?* . Wenn Sie durch Klicken auf „Rot“ angeben, dass nur rote Produkte angezeigt werden sollen, enthält die nächste von der Anwendung gesendete Abfrage `$filter=Color eq ‘Red’`.

Mit dem folgenden Codeausschnitt von der `JobsSearch.cs`-Seite wird die Berufsbezeichnung dem Filter hinzugefügt, wenn Sie einen Wert aus der Berufsbezeichnungsfacette auswählen.

```cs
if (businessTitleFacet != "")
  filter = "business_title eq '" + businessTitleFacet + "'";
```

<a name="tips"></a> 

## <a name="tips-and-best-practices"></a>Tipps und bewährte Methoden

### <a name="indexing-tips"></a>Tipps zur Indizierung
**Verbessern Sie die Indexeffizienz, indem Sie kein Suchfeld verwenden**

Wenn Ihre Anwendung kein Suchfeld, sondern ausschließlich eine Facetnavigation aufweist, können Sie das Feld als `searchable=false`, `facetable=true` markieren, um einen kompakteren Index zu erhalten. Die Indizierung wird außerdem nur für vollständige Facettenwerte durchgeführt – ohne Wortumbruch oder Indizierung der Komponenten eines Werts mit mehreren Wörtern.

**Legen Sie fest, welche Felder als Facette verwendet werden können**

Denken Sie daran, dass das Schema des Index bestimmt, welche Felder als Facette zur Verfügung stehen. Wenn ein Feld als Facette verwendet werden kann, gibt die Abfrage an, welches Feld als Facettengrundlage fungieren soll. Das Feld, das als Facettengrundlage fungiert, liefert die Werte, die unter der Beschriftung erscheinen. 

Die Werte unter den einzelnen Beschriftungen stammen aus dem Index. Ein Beispiel: Wenn das Feld *Farbe*als Facette fungiert, basieren die für weitere Filtervorgänge verfügbaren Werte auf den Werten für dieses Feld (Rot, Schwarz usw.).

Bei numerischen Werten und DateTime-Werten können explizit Werte für das Facettenfeld festgelegt werden (etwa `facet=Rating,values:1|2|3|4|5`). Zur einfacheren Unterteilung von Facettenergebnissen in zusammenhängende Bereiche (entweder auf der Grundlage numerischer Werte oder auf der Grundlage von Zeiträumen) kann für diese Feldtypen eine Werteliste verwendet werden. 

**Standardmäßig können Sie nur über eine Ebene der Facettennavigation verfügen** 

Wie bereits erwähnt wird die hierarchische Schachtelung von Facetten nicht direkt unterstützt. Standardmäßig unterstützt die Facettennavigation in Azure Search nur eine Ebene von Filtern. Diese Beschränkung lässt sich jedoch umgehen. Sie können eine hierarchische Facettenstruktur in einem Element vom Typ `Collection(Edm.String)` codieren, indem Sie pro Hierarchie jeweils einen Eintrag angeben. Die Implementierung dieser Umgehung ist nicht Gegenstand dieses Artikels. 

### <a name="querying-tips"></a>Abfragetipps
**Überprüfen von Feldern**

Wenn Sie die Facettenliste dynamisch auf der Grundlage nicht vertrauenswürdiger Benutzereingaben erstellen, stellen Sie sicher, dass die Namen der Facettenfelder gültig sind, oder versehen Sie die Namen bei der URL-Erstellung mit Escapezeichen – entweder mithilfe von `Uri.EscapeDataString()` in .NET oder mithilfe einer Entsprechung für die gewählte Plattform.

### <a name="filtering-tips"></a>Filtertipps
**Erhöhen Sie die Genauigkeit der Suche mit Filtern**

Verwenden Sie Filter. Wenn Sie sich ausschließlich auf Suchausdrücke verlassen, wird aufgrund der Wortstammerkennung unter Umständen ein Dokument zurückgegeben, bei dem keines der Felder den exakten Facettenwert enthält.

**Erhöhen Sie die Leistung der Suche mit Filtern**

Filter grenzen die für die Suche infrage kommenden Dokumente ein und schließen sie von der Sortierung aus. Wenn Sie über eine große Anzahl von Dokumenten verfügen, können Sie die Leistung mit einem selektiven Facettendrilldownelement in der Regel steigern.
  
**Filtern Sie nur die facettierten Felder**

Bei einem facettenbasierten Drilldownvorgang sollen in der Regel nur Dokumente enthalten sein, die den Facettenwert in einem bestimmten Feld enthalten (und nicht in einem beliebigen durchsuchbaren Feld). Durch Hinzufügen eines Filters wird der Dienst angewiesen, nur im als Facette fungierenden Feld nach einem übereinstimmenden Wert zu suchen.

**Kürzen Sie Facettenergebnisse mit weiteren Filtern**

Facettenergebnisse sind Dokumente in den Suchergebnissen, die einem Facettenbegriff entsprechen. Im folgenden Beispiel weisen 254 Elemente der Suchergebnisse für *cloud computing* auch den Inhaltstyp *interne Spezifikation* auf. Die Elemente schließen sich nicht zwingend gegenseitig aus. Erfüllt ein Element die Kriterien beider Filter, wird es in jedem Filter gezählt. Diese Duplizierung ist möglich, wenn die Facette auf Feldern vom Typ `Collection(Edm.String)` basiert, die häufig zur Implementierung von Dokumententags verwendet werden.

        Search term: "cloud computing"
        Content type
           Internal specification (254)
           Video (10) 

Allgemein gilt: Wenn die Facettenergebnisse ständig zu umfangreich ausfallen, empfiehlt es sich, weitere Filter hinzuzufügen, um den Benutzern mehr Optionen zum Eingrenzen der Suche zu geben.

### <a name="tips-about-result-count"></a>Tipps zur Ergebnisanzahl

**Begrenzen Sie die Anzahl der Elemente in der Facettennavigation**

Jedes Facettenfeld in der Navigationsstruktur ist standardmäßig auf maximal zehn Werte beschränkt. Dieser Standardwert ist für Navigationsstrukturen sinnvoll, da die Werteliste dadurch überschaubar bleibt. Sie können den Standardwert überschreiben, indem Sie einen Zählwert zuweisen.

* `&facet=city,count:5` gibt an, dass als Facettenergebnis nur die ersten fünf Städte aus den Topergebnissen zurückgegeben werden sollen. Angenommen, eine Beispielabfrage mit dem Suchbegriff „Flughafen“ weist 32 Treffer auf. Wenn für die Abfrage `&facet=city,count:5`angegeben ist, werden in den Facettenergebnissen nur die ersten fünf eindeutigen Städte mit den meisten Dokumenten in den Suchergebnissen berücksichtigt.

Beachten Sie die Unterscheidung zwischen Facettenergebnissen und Suchergebnissen: Suchergebnisse umfassen alle Dokumente, die der Abfrage entsprechen. Facettenergebnisse sind die Übereinstimmungen für die einzelnen Facettenwerte. In unserem Beispiel umfassen die Suchergebnisse Städtenamen, die sich nicht in der Facettenklassifizierungsliste (in unserem Beispiel: fünf) befinden. Durch die Facettennavigation herausgefilterte Ergebnisse werden angezeigt, wenn Sie Facetten löschen oder zusätzliche Facetten neben Städtenamen auswählen. 

> [!NOTE]
> Die Erläuterung von `count` kann verwirrend sein, wenn mehrere Typen vorhanden sind. Die folgende Tabelle bietet einen kurzen Überblick über die Verwendung des Begriffs in Azure Search-API, Beispielcode und Dokumentation: 

* `@colorFacet.count`<br/>
  Im Darstellungscode wird die Facette mit einem count-Parameter versehen, um die Anzahl der Facettenergebnisse anzuzeigen. In den Facettenergebnissen gibt „count“ die Anzahl von Dokumenten an, die dem Facettenbegriff oder -bereich entsprechen.
* `&facet=City,count:12`<br/>
  In einer Facettenabfrage können Sie „count“ auf einen Wert festlegen.  Der Standardwert lautet „10“, Sie können jedoch einen größeren oder kleineren Wert angeben. Mit `count:12` werden in den Facettenergebnissen die 12 relevantesten Übereinstimmungen (auf der Grundlage der Dokumentanzahl) zurückgegeben.
* "`@odata.count`"<br/>
  In der Abfrageantwort gibt dieser Wert die Anzahl übereinstimmender Elemente in den Suchergebnissen an. Dieser ist häufig größer als die Summe aller Facettenergebnisse, da hier auch Elemente enthalten sind, die zwar dem Suchbegriff entsprechen, für die aber keine übereinstimmenden Facettenwerte vorhanden sind.

**Ermitteln Sie die Anzahl in Facettenergebnissen**

Wenn Sie einer Facettenabfrage einen Filter hinzufügen, empfiehlt es sich unter Umständen, die Facettenanweisung beizubehalten (Beispiel: `facet=Rating&$filter=Rating ge 4`). Aus technischer Sicht wird „facet=Rating“ zwar nicht benötigt, die Angabe liefert jedoch die Anzahl von Facettenwerten für Bewertungen mit dem Wert „4“ und höher. Wenn Sie beispielsweise auf „4“ klicken und die Abfrage einen Filter für „größer oder gleich 4“ enthält, wird die Anzahl für jede Bewertung ab dem Wert „4“ zurückgegeben.  

**Stellen Sie sicher, dass Sie die genaue Facettenanzahl erhalten**

Unter bestimmten Umständen kann es vorkommen, dass die Facettenanzahl nicht den Resultsets entspricht (siehe Forumsbeitrag [Facettennavigation in Azure Search](https://social.msdn.microsoft.com/Forums/azure/06461173-ea26-4e6a-9545-fbbd7ee61c8f/faceting-on-azure-search?forum=azuresearch)).

Die Abweichung der Facettenanzahl kann auf die Sharding-Architektur zurückzuführen sein. Jeder Suchindex besitzt mehrere Shards, von denen jeweils die x relevantesten Facetten (auf der Grundlage der Dokumentanzahl) zurückgegeben werden. Diese werden dann zu einem einzelnen Ergebnis zusammengefasst. Falls nun für einige Shards viele und für andere Shards weniger Werte vorhanden sind, kann es vorkommen, dass einige Facettenwerte fehlen oder in den Ergebnissen nicht korrekt erfasst wurden.

Dieses Verhalten kann sich zwar jederzeit ändern, wenn Sie jedoch aktuell davon betroffen sind, können Sie das Problem umgehen, indem Sie „count:\<number>“ künstlich auf eine große Zahl erhöhen, um vollständige Berichte aus jeder Shard zu erzwingen. Wenn der Wert von „count:“ größer oder gleich der Anzahl eindeutiger Werte im Feld ist, erhalten Sie garantiert exakte Ergebnisse. Bei einer hohen Anzahl von Dokumenten ist jedoch mit Leistungseinbußen zu rechnen. Verwenden Sie die Option daher mit Bedacht.

### <a name="user-interface-tips"></a>Benutzeroberflächentipps
**Hinzufügen von Beschriftungen für die einzelnen Felder in der Facettennavigation**

Beschriftungen werden üblicherweise im HTML-Code oder im Formular definiert (`index.cshtml` in der Beispielanwendung). Azure Search verfügt über keine API für Facettennavigationsbeschriftungen oder andere Metadaten.

<a name="rangefacets"></a>

## <a name="filter-based-on-a-range"></a>Filtern nach einem Bereich
Die Verwendung von Facettenwertbereichen ist eine verbreitete Anforderung für Suchanwendungen. Bereiche werden für numerische Daten und für DateTime-Werte unterstützt. Weitere Informationen zu den einzelnen Ansätzen finden Sie unter [Dokumente durchsuchen (Azure Search-API)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents).

Azure Search vereinfacht die Bereichserstellung durch zwei Bereichsberechnungsansätze. Bei beiden Ansätzen erstellt Azure Search die entsprechenden Bereiche auf der Grundlage Ihrer Eingaben. Wenn Sie also beispielsweise die Bereichswerte „10|20|30“ angeben, werden automatisch folgende Bereiche erstellt: 0–10, 10–20, 20–30. Ihre Anwendung kann optional alle leeren Intervalle entfernen. 

**Vorgehensweise 1: Verwenden des interval-Parameters**  
Um Preisfacets in Schritten von 10 $ festzulegen, geben Sie Folgendes an: `&facet=price,interval:10`

**Vorgehensweise 2: Verwenden einer Werteliste**  
Bei numerischen Daten können Sie eine Werteliste verwenden.  Sehen Sie sich die folgende Darstellung des Facettenbereichs für ein `listPrice`-Feld an:

  ![Beispielwerteliste](media/search-faceted-navigation/Facet-5-Prices.PNG "Beispielwerteliste")

Verwenden Sie zum Angeben eines Facettenbereichs wie im vorhergehenden Screenshot eine Werteliste:

    facet=listPrice,values:10|25|100|500|1000|2500

Jeder Bereich wird mit „0“ als Ausgangspunkt und einem Wert aus der Liste als Endpunkt erstellt und anschließend um den vorherigen Bereich gekürzt, um eigenständige Intervalle zu erhalten. Dies wird im Rahmen der Facettennavigation von Azure Search durchgeführt. Sie müssen keinen Code schreiben, um die einzelnen Intervalle zu strukturieren.

### <a name="build-a-filter-for-a-range"></a>Erstellen Sie einen Filter für einen Bereich
Wenn Sie Dokumente auf der Grundlage eines von Ihnen ausgewählten Bereichs filtern möchten, können Sie die Filteroperatoren `"ge"` und `"lt"` in einem zweiteiligen Ausdruck verwenden, um die Endpunkte des Bereichs zu definieren. Beispiel: Wenn Sie für ein `listPrice`-Feld den Bereich 10–25 wählen, ist der Filter `$filter=listPrice ge 10 and listPrice lt 25`. Im Beispielcode legt der Filterausdruck die Endpunkte über die Parameter **priceFrom** und **priceTo** fest. 

  ![Abfrage für einen Bereich von Werten](media/search-faceted-navigation/Facet-6-buildfilter.PNG "Abfrage für einen Bereich von Werten")

<a name="geofacets"></a> 

## <a name="filter-based-on-distance"></a>Filtern Sie nach Entfernung
Filter, die den Benutzer bei der Wahl eines Geschäfts, Restaurants oder eines Ziels in Abhängigkeit von der Nähe zum aktuellen Standort unterstützen, sind weit verbreitet. Diese Art von Filter sieht zwar vielleicht wie eine Facettennavigation aus, ist aber nur ein Filter. Diese Option wird hier nur erwähnt, falls Sie nach Implementierungstipps für dieses spezielle Designproblem suchen.

In Azure Search stehen zwei räumliche Funktionen zur Verfügung: **geo.distance** und **geo.intersects**.

* Die **geo.distance**-Funktion gibt die Entfernung in Kilometern zwischen zwei Punkten zurück. Ein Punkt ist ein Feld, der andere ist eine Konstante, die als Teil des Filters übergeben wird. 
* Die **geo.intersects**-Funktion gibt „true“ zurück, wenn sich ein bestimmter Punkt innerhalb eines bestimmten Polygons befindet. Der Punkt ist ein Feld, und das Polygon ist als Konstantenliste mit Koordinaten angegeben, die als Teil des Filters übergeben wird.

Filterbeispiele finden Sie unter [OData-Ausdruckssyntax für Azure Search](query-odata-filter-orderby-syntax.md).

<a name="tryitout"></a>

## <a name="try-the-demo"></a>Testen Sie die Demo
Die Azure Search-Demo „Job Portal“ enthält die Beispiele, auf die in diesem Artikel Bezug genommen wird.

-   Die Arbeitsdemo können Sie online unter [Azure Search-Demo „Job Portal“](https://azjobsdemo.azurewebsites.net/) anzeigen und testen.

-   Laden Sie den Code aus dem [Repository „Azure-Beispiele“ in GitHub](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs) herunter.

Achten Sie bei der Arbeit mit Suchergebnissen auf die Veränderung der Abfragekonstruktion in der URL. Diese Anwendung fügt die ausgewählten Facetten dem URI hinzu.

1. Zum Verwenden der Zuordnungsfunktionalität der Demo-App rufen Sie einen Bing Maps-Schlüssel aus dem [Bing Maps Dev Center](https://www.bingmapsportal.com/) ab. Überschreiben Sie damit den vorhandenen Schlüssel auf der `index.cshtml`-Seite. Die `BingApiKey`-Einstellung in der Datei `Web.config` wird nicht verwendet. 

2. Führen Sie die Anwendung aus. Machen Sie die optionale Tour, oder schließen Sie das Dialogfeld.
   
3. Geben Sie einen Suchbegriff wie z.B. „Analyst“ ein, und klicken Sie auf das Suchsymbol. Die Abfrage wird schnell ausgeführt.
   
   Zusammen mit den Suchergebnissen wird auch eine Facettennavigationsstruktur zurückgegeben. Die Facettennavigationsstruktur auf der Suchergebnisseite enthält jeweils die Anzahl für die einzelnen Facettenergebnisse. Es sind keine Facetten ausgewählt, daher werden alle übereinstimmenden Ergebnisse zurückgegeben.
   
   ![Suchergebnisse vor dem Auswählen von Facetten](media/search-faceted-navigation/faceted-search-before-facets.png "Suchergebnisse vor dem Auswählen von Facetten")

4. Klicken Sie auf eine Berufsbezeichnung, einen Standort oder ein Mindestgehalt. Die Facetten waren beim ersten Suchvorgang NULL, wenn sie jedoch Werte erhalten, werden Elemente, die den Kriterien nicht mehr entsprechen, aus den Suchergebnissen entfernt.
   
   ![Suchergebnisse nach dem Auswählen von Facetten](media/search-faceted-navigation/faceted-search-after-facets.png "Suchergebnisse nach dem Auswählen von Facetten")

5. Klicken Sie am oberen Seitenrand auf das `[X]` hinter den ausgewählten Facetten, um sie zu löschen, damit Sie verschiedene Abfrageverhalten testen können.
   
<a name="nextstep"></a>

## <a name="learn-more"></a>Weitere Informationen
[Azure Search Deep Dive](https://channel9.msdn.com/Events/TechEd/Europe/2014/DBI-B410) ist in diesem Zusammenhang interessant. Ab 45:25 wird die Implementierung von Facetten vorgeführt.

Weitere Einblicke in die Entwurfsprinzipien der Facettennavigation erhalten Sie unter folgenden Links:

* [Entwurfsmuster: Facettennavigation](https://alistapart.com/article/design-patterns-faceted-navigation)
* [Front End Concerns When Implementing Faceted Search – Part 1 ](https://articles.uie.com/faceted_search2/) (Front-End-Aspekte bei der Implementierung von facettierten Suche – Teil 1)

