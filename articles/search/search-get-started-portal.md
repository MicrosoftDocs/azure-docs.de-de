---
title: 'Schnellstart: Erstellen, Laden und Abfragen eines Index im Azure-Portal – Azure Search'
description: Hier erfahren Sie, wie Sie im Azure-Portal mithilfe von integrierten Daten und mithilfe des Datenimport-Assistenten Ihren ersten Index in Azure Search erstellen und abfragen.
author: HeidiSteen
manager: cgronlun
tags: azure-portal
services: search
ms.service: search
ms.topic: tutorial
ms.date: 05/02/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: e5baa0876dc976553e8e541cef6b481329500bf6
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/14/2019
ms.locfileid: "65606451"
---
# <a name="quickstart-use-built-in-portal-tools-for-azure-search-import-indexing-and-queries"></a>Schnellstart: Verwenden von integrierten Portaltools für Azure Search-Importe, -Indizierungen und -Abfragen
> [!div class="op_single_selector"]
> * [Portal](search-get-started-portal.md)
> * [PowerShell](search-howto-dotnet-sdk.md)
> * [Postman](search-fiddler.md)
> * [C#](search-create-index-dotnet.md)
>*

Für einen schnellen Einstieg in die Azure Search-Konzepte testen Sie die integrierten Tools im Azure-Portal. Assistenten und Editoren bieten keinen vollständigen Ersatz für die .NET- und REST-APIs, aber Sie können schnell mit einer codefreie Einführung beginnen, indem Sie interessante Abfragen für Beispieldaten innerhalb von Minuten schreiben.

> [!div class="checklist"]
> * Beginnen Sie mit einem kostenlosen öffentlichen Beispieldataset, das in Azure gehostet wird.
> * Führen Sie den **Daten importieren**-Assistenten in Azure Search aus, um Daten zu laden und einen Index zu generieren.
> * Überwachen Sie den Indizierungsfortschritt im Portal.
> * Zeigen Sie einen vorhandenen Index und die Optionen an, mit denen er geändert werden kann.
> * Verwenden Sie den **Suchexplorer**, um sich mit Volltextsuche, Filtern, Facets, Fuzzysuche und Geosuche vertraut zu machen.

Falls die Tools über zu viele Einschränkungen verfügen, können Sie eine [codebasierte Einführung in die Azure Search-Programmierung in .NET](search-howto-dotnet-sdk.md) lesen oder [Postman oder Fiddler zur Erstellung von REST-API-Aufrufen](search-fiddler.md) verwenden.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen. Außerdem können Sie sich ein sechsminütiges Demovideo zu den Schritten in diesem Tutorial ansehen (etwa ab der dritten Minute [dieses Übersichtsvideos für Azure Search](https://channel9.msdn.com/Events/Connect/2016/138)).

## <a name="prerequisites"></a>Voraussetzungen

[Erstellen Sie einen Azure Search-Dienst](search-create-service-portal.md), oder suchen Sie in Ihrem aktuellen Abonnement [nach einem vorhandenen Dienst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). Für diesen Schnellstart können Sie einen kostenlosen Dienst verwenden. 

### <a name="check-for-space"></a>Überprüfen des Speicherplatzes

Viele Kunden beginnen mit dem kostenlosen Dienst (Free). Diese Version ist auf drei Indizes, drei Datenquellen und drei Indexer beschränkt. Stellen Sie sicher, dass Sie über ausreichend Platz für zusätzliche Elemente verfügen, bevor Sie beginnen. In diesem Tutorial wird jeweils eine Instanz von jedem Objekt erstellt.

In Abschnitten des Service-Dashboards sehen Sie, über wie viele Indizes, Indexer und Datenquellen Sie bereits verfügen. 

![Listen mit Indizes, Indexern und Datenquellen](media/search-get-started-portal/tiles-indexers-datasources2.png)

## <a name="create-index"></a> Erstellen eines Index und Laden von Daten

Suchabfragen durchlaufen einen [*Index*](search-what-is-an-index.md) mit durchsuchbaren Daten, Metadaten und zusätzlichen Konstrukten, die bestimmte Suchverhaltensweisen optimieren.

Für dieses Tutorial verwenden Sie ein integriertes Beispieldataset, das über den Assistenten **Daten importieren** mit einem [*Indexer*](search-indexer-overview.md) durchforstet werden kann. Ein Indexer ist ein quellspezifischer Crawler, mit dem Metadaten und Inhalte von unterstützten Azure-Datenquellen gelesen werden können. Normalerweise werden Indexer programmgesteuert verwendet, aber im Portal können Sie über den **Daten importieren**-Assistenten auf sie zugreifen. 

### <a name="step-1---start-the-import-data-wizard-and-create-a-data-source"></a>Schritt 1: Starten des „Daten importieren“-Assistenten und Erstellen einer Datenquelle

1. Klicken Sie auf dem Dashboard des Azure Search-Diensts in der Befehlsleiste auf **Daten importieren**, um einen Suchindex zu erstellen und zu füllen.

   ![Befehl zum Importieren von Daten](media/search-get-started-portal/import-data-cmd2.png)

2. Klicken Sie im Assistenten auf **Daten verbinden** > **Beispiele** > **realestate-us-sample**. Diese Datenquelle ist integriert. Wenn Sie eine eigene Datenquelle erstellen, müssen Sie einen Namen, einen Typ und Verbindungsinformationen angeben. Nach der Erstellung wird sie zu einer vorhandenen Datenquelle, die in anderen Importvorgängen wiederverwendet werden kann.

   ![Auswählen des Beispieldatasets](media/search-get-started-portal/import-datasource-sample2.png)

3. Wechseln Sie zur nächsten Seite.

   ![Nächste Seite-Schaltfläche für kognitive Suche](media/search-get-started-portal/next-button-add-cog-search.png)

### <a name="step-2---skip-cognitive-skills"></a>Schritt2: Überspringen von kognitiven Qualifikationen

Der Assistent unterstützt die Erstellung einer [Pipeline für kognitive Qualifikationen](cognitive-search-concept-intro.md), um die Cognitive Services-KI-Algorithmen in die Indizierung einzubeziehen. 

Überspringen Sie diesen Schritt vorerst, und wechseln Sie direkt zu **Zielindex anpassen**.

   ![Überspringen des Schritts zu kognitiven Qualifikationen](media/search-get-started-portal/skip-cog-skill-step.png)

> [!TIP]
> Sie können ein Beispiel für die AI-Indizierung in einem [Schnellstart](cognitive-search-quickstart-blob.md) oder [Tutorial](cognitive-search-tutorial-blob.md) durchgehen.

### <a name="step-3---configure-index"></a>Schritt 3: Konfigurieren des Index

In der Regel erfolgt eine Indexerstellung codebasiert und vor dem Laden der jeweiligen Daten. Wie in diesem Tutorial gezeigt wird, kann der Assistent jedoch einen Basisindex für jede Datenquelle erstellen, die er durchforsten kann. Für einen Index sind mindestens ein Name und eine Felderauflistung erforderlich, wobei ein Feld als Dokumentschlüssel gekennzeichnet sein muss, um die einzelnen Dokumente eindeutig zu identifizieren. Außerdem können Sie Sprachanalysen oder Vorschlagsfunktionen angeben, wenn Sie Abfragen mit AutoVervollständigen oder vorgeschlagene Abfragen verwenden möchten.

Felder besitzen Datentypen und Attribute. Die Kontrollkästchen im oberen Bereich sind *Indexattribute*, mit denen die Verwendung des Felds gesteuert wird.

* **Abrufbar** bedeutet, dass es in der Liste mit den Suchergebnissen angezeigt wird. Sie können einzelne Felder kennzeichnen, indem Sie dieses Kontrollkästchen deaktivieren, um sie aus den Suchergebnissen auszuschließen, z. B. wenn Felder nur in Filterausdrücken verwendet werden.
* **Schlüssel** ist der eindeutige Bezeichner des Dokuments. Er ist immer eine Zeichenfolge, und er ist erforderlich.
* Mit **Filterbar**, **Sortierbar** und **Facettenreich** wird festgelegt, ob Felder in einer Filter-, Sortier- oder Facettennavigationsstruktur verwendet werden können.
* **Durchsuchbar** bedeutet, dass ein Feld in die Volltextsuche einbezogen wird. Zeichenfolgen sind durchsuchbar. Numerische Felder und boolesche Felder werden häufig als „Nicht durchsuchbar“ markiert.

Speicheranforderungen ändern sich nicht als Folge Ihrer Auswahl. Wenn Sie z. B. das Attribut **Abrufbar** für mehrere Felder festlegen, erhöhen sich die Speicheranforderungen nicht.

Standardmäßig durchsucht der Assistent die Datenquelle nach eindeutigen Bezeichnern als Grundlage für das Schlüsselfeld. *Zeichenfolgen* werden durch Attribute als **Abrufbar** und **Durchsuchbar** gekennzeichnet. *Ganze Zahlen* werden durch Attribute als **Abrufbar**, **Filterbar**, **Sortierbar** und **Facettierbar** gekennzeichnet.

1. Übernehmen Sie die Standardeinstellungen. 

   Wenn Sie den Assistenten ein zweites Mal unter Verwendung einer Immobiliendatenquelle ausführen, wird der Index nicht mit Standardattributen konfiguriert. Sie müssen Attribute für künftige Importe manuell auswählen.

   ![Generierter Immobilienindex](media/search-get-started-portal/realestateindex2.png)

2. Wechseln Sie zur nächsten Seite.

   ![Nächste Seite, Indexer erstellen](media/search-get-started-portal/next-button-create-indexer.png)

### <a name="step-4---configure-indexer"></a>Schritt 4: Konfigurieren des Indexers

Klicken Sie im Assistenten **Daten importieren** auf **Indexer** > **Name**, und geben Sie einen Namen für den Indexer ein.

Mit diesem Objekt wird ein ausführbarer Prozess definiert. Sie könnten zwar auch einen wiederkehrenden Zeitplan einrichten, in diesem Tutorial verwenden Sie allerdings die Standardoption, um den Indexer sofort einmalig auszuführen.

Klicken Sie auf **Senden**, um den Indexer zu erstellen und gleichzeitig auszuführen.

  ![Immobilienindexer](media/search-get-started-portal/realestate-indexer2.png)

## <a name="monitor-progress"></a>Fortschritt überwachen

Der Assistent sollte die Liste der Indexer anzeigen, in der Sie den Fortschritt überwachen können. Zur eigenen Navigation wechseln Sie zur Seite „Übersicht“, und klicken Sie auf **Indexer**.

Es kann einige Minuten dauern, bis die Seite im Portal aktualisiert ist, aber der neu erstellte Indexer sollte in der Liste angezeigt werden. Der Status sollte „In Bearbeitung“ oder „Erfolgreich“ lauten, und die Anzahl indizierter Dokumente sollte angegeben sein.

   ![Statusmeldung des Indexers](media/search-get-started-portal/indexers-inprogress2.png)

## <a name="view-the-index"></a>Anzeigen des Index

Die Hauptseite des Diensts enthält Links zu den Ressourcen, die unter Ihrem Azure Search-Dienst erstellt wurden.  Klicken Sie zum Anzeigen des gerade erstellten Index in der Liste mit den Links auf **Indizes**. 

   ![Liste „Indizes“ im Dashboard des Diensts](media/search-get-started-portal/indexes-list.png)

In dieser Liste können Sie auf den gerade erstellten Index *realestate-us-sample* klicken und das Indexschema anzeigen. Optional können Sie auch neue Felder hinzufügen. 

Auf der Registerkarte **Felder** wird das Indexschema angezeigt. Scrollen Sie in der Liste nach unten, um ein neues Feld einzugeben. In den meisten Fällen ist es nicht möglich, bereits vorhandene Felder zu ändern. Vorhandene Felder verfügen über eine physische Darstellung in Azure Search und können daher nicht geändert werden (auch nicht im Code). Wenn Sie ein vorhandenes Feld grundlegend ändern möchten, erstellen Sie einen neuen Index, wobei der ursprüngliche Index verworfen wird.

   ![Beispiel für Indexdefinition](media/search-get-started-portal/sample-index-def.png)

Andere Konstrukte, z.B. Bewertungsprofile und CORS-Optionen, können jederzeit hinzugefügt werden.

Nehmen Sie sich kurz Zeit, um die Optionen für die Indexdefinition anzuzeigen und sich damit vertraut zu machen, was Sie beim Entwerfen des Index bearbeiten können. Wenn Optionen ausgegraut sind, ist dies ein Hinweis darauf, dass ein Wert nicht geändert oder gelöscht werden kann. 

## <a name="query-index"></a> Abfragen mit dem Suchexplorer

Sie sollten nun über einen Suchindex verfügen, der über die integrierte [**Suchexplorer**](search-explorer.md)-Abfrageseite abgefragt werden kann. Sie enthält ein Suchfeld, über das Sie beliebige Abfragezeichenfolgen testen können.

Der **Suchexplorer** ist nur auf die Verarbeitung von [REST-API-Anforderungen](https://docs.microsoft.com/rest/api/searchservice/search-documents) ausgelegt. Er akzeptiert aber auch [einfache Abfragesyntax](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) und Syntax für den [vollständigen Lucene-Abfrageparser](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) sowie alle Suchparameter, die für Vorgänge mit der [REST-API zum Durchsuchen von Dokumenten](https://docs.microsoft.com/rest/api/searchservice/search-documents#bkmk_examples) verfügbar sind.

> [!TIP]
> Die folgenden Schritte werden im [Übersichtsvideo für Azure Search](https://channel9.msdn.com/Events/Connect/2016/138) ab 6:08 vorgeführt.
>

1. Klicken Sie auf der Befehlsleiste auf **Suchexplorer** .

   ![Suchexplorerbefehl](media/search-get-started-portal/search-explorer-cmd2.png)

2. Klicken Sie auf der Befehlsleiste auf **Index ändern**, um zu *realestate-us-sample* zu wechseln. Klicken Sie auf der Befehlsleiste auf **API-Version festlegen**, um die verfügbaren REST-APIs anzuzeigen. Verwenden Sie für die Abfragen weiter unten die allgemein verfügbare Version (2019-05-06).

   ![Index- und API-Befehle](media/search-get-started-portal/search-explorer-changeindex-se2.png)

3. Fügen Sie über die Suchleiste die folgenden Abfragezeichenfolgen ein, und klicken Sie auf **Suchen**.

   ![Abfragezeichenfolge und Schaltfläche „Suchen“](media/search-get-started-portal/search-explorer-query-string-example.png)

## <a name="example-queries"></a>Beispielabfragen

Sie können Begriffe und Ausdrücke eingeben, ähnlich wie bei einer Bing- oder Google-Suche, oder Sie können vollständig formulierte Abfrageausdrücke eingeben. Die Ergebnisse werden als ausführliche JSON-Dokumente zurückgegeben.

### <a name="simple-query-with-top-n-results"></a>Einfache Abfrage mit Top N-Ergebnissen

#### <a name="example-string-query-searchseattle"></a>Beispiel (Abfragezeichenfolge): `search=seattle`

* Der Parameter **search** dient zum Eingeben eines Schlüsselworts für die Volltextsuche. In diesem Fall werden Angebote in King County (Washington) zurückgegeben, die den Begriff *Seattle* in einem beliebigen durchsuchbaren Feld des Dokuments enthalten.

* Der **Suchexplorer** gibt Ergebnisse im JSON-Format zurück. Dieses Format ist sehr ausführlich und in Dokumenten mit einer dichten Struktur nur schwer lesbar. Dies ist so gewollt. Die Sichtbarkeit in das gesamte Dokument ist wichtig für Entwicklungszwecke, vor allem beim Testen. Zur Verbesserung der Benutzerfreundlichkeit müssen Sie Code schreiben, mit dem [Suchergebnisse verarbeitet werden](search-pagination-page-layout.md), um wichtige Elemente hervorzuheben.

* Dokumente bestehen aus allen Feldern, die im Index als „abrufbar“ gekennzeichnet sind. Klicken Sie zum Anzeigen von Indexattributen im Portal in der Liste **Indizes** auf *realestate-us-sample*.

#### <a name="example-parameterized-query-searchseattlecounttruetop100"></a>Beispiel (parametrisierte Abfrage): `search=seattle&$count=true&$top=100`

* Das Symbol **&** dient zum Anfügen von Suchparametern. Diese können in beliebiger Reihenfolge angegeben werden.

* Der Parameter **$count=true** gibt die Gesamtanzahl aller zurückgegebenen Dokumente zurück. Dieser Wert wird oben im Bereich mit den Suchergebnissen angezeigt. Zur Überprüfung von Filterabfragen können Sie von **$count=true** gemeldete Änderungen überwachen. Niedrigere Zahlen weisen darauf hin, dass Ihr Filter funktioniert.

* **$top=100** gibt von allen Dokumenten die 100 Dokumente mit dem höchsten Rang zurück. Standardmäßig gibt Azure Search die ersten 50 der besten Treffer zurück. Die Menge kann mithilfe von **$top** erhöht oder verringert werden.

### <a name="filter-query"></a> Filtern der Abfrage

Filter werden in Suchanfragen eingefügt, wenn Sie den Parameter **$filter** anfügen. 

#### <a name="example-filtered-searchseattlefilterbeds-gt-3"></a>Beispiel (gefiltert): `search=seattle&$filter=beds gt 3`

* Der Parameter **$filter** gibt Ergebnisse zurück, die den angegebenen Kriterien entsprechen. In diesem Fall: mehr als drei Schlafzimmer.

* Bei der Filtersyntax handelt es sich um eine OData-Konstruktion. Weitere Informationen finden Sie unter [OData Expression Syntax for Azure Search](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) (OData-Ausdruckssyntax für Azure Search).

### <a name="facet-query"></a> Facettieren der Abfrage

Facettenfilter werden in Suchanfragen eingebunden. Sie können den Parameter „facet“ verwenden, um eine aggregierte Anzahl von Dokumenten zurückzugeben, die mit einem von Ihnen angegebenen Facetwert übereinstimmen.

#### <a name="example-faceted-with-scope-reduction-searchfacetcitytop2"></a>Beispiel (facettiert mit Verringerung des Umfangs): `search=*&facet=city&$top=2`

* **search=** * ist eine leere Suche. Bei einer leeren Suche wird alles durchsucht. Eine leere Abfrage kann beispielsweise übermittelt werden, um den vollständigen Satz von Dokumenten zu filtern oder zu facettieren – etwa, wenn eine Faceting-Navigationsstruktur alle Städte im Index enthalten soll.

* **facet** gibt eine Navigationsstruktur zurück, die Sie an ein Benutzeroberflächenelement übergeben können. Er gibt Kategorien und eine Anzahl zurück. In diesem Fall basieren die Kategorien auf der Anzahl von Städten. In Azure Search steht zwar keine Aggregation zur Verfügung, mit `facet` lässt sich jedoch eine Art Aggregation erreichen, die eine Anzahl von Dokumenten in den einzelnen Kategorien liefert.

* **$top=2** gibt zwei Dokumente zurück und zeigt, dass Sie mithilfe von `top` die Ergebnisse sowohl verringern als auch erhöhen können.

#### <a name="example-facet-on-numeric-values-searchseattlefacetbeds"></a>Beispiel (Facet für numerische Werte): `search=seattle&facet=beds`**

* Bei dieser Abfrage handelt es sich um ein Facet für Betten bei einer Textsuche nach *Seattle*. Der Ausdruck *beds* kann als Facet angegeben werden, da das Feld im Index als filterbar und facettierbar markiert ist und die enthaltenen Werte (numerische Werte von 1 bis 5) für die Kategorisierung von Angeboten in Gruppen (Angebote mit drei Schlafzimmern, vier Schlafzimmern...) geeignet ist.

* Nur filterbare Felder können facettiert werden. In den Ergebnissen können nur abrufbare Felder zurückgegeben werden.

### <a name="highlight-query"></a> Hervorheben von Suchergebnissen

Bei Treffermarkierungen wird Text, der dem Schlüsselwort entspricht, mit einer Formatierung versehen (sofern Treffer in einem bestimmten Feld gefunden werden). Falls Ihr Suchbegriff Teil einer umfangreicheren Beschreibung ist, können Sie ihn mithilfe einer Treffermarkierung hervorheben.

#### <a name="example-highlighter-searchgranite-countertopshighlightdescription"></a>Beispiel (Textmarker): `search=granite countertops&highlight=description`

* In diesem Beispiel ist der formatierte Ausdruck *granite countertops* im Beschreibungsfeld leichter zu erkennen.

#### <a name="example-linguistic-analysis-searchmicehighlightdescription"></a>Beispiel (linguistische Analyse): `search=mice&highlight=description`

* Die Volltextsuche sucht nach Wortformen mit ähnlicher Semantik. In diesem Fall wird bei Häusern mit einer Mäuseplage in den Ergebnissen einer Suche nach dem Schlüsselwort „mice“ der Text „mouse“ hervorgehoben. Dank linguistischer Analyse können in den Ergebnissen unterschiedliche Formen des gleichen Worts erscheinen.

* Azure Search unterstützt 56 Analysen von Lucene und Microsoft. Standardmäßig verwendet Azure Search die Standardanalyse von Lucene.

### <a name="fuzzy-search"></a> Ausprobieren der Fuzzysuche

Für falsch geschriebene Wörter wie *samamish* für das Sammamish-Plateau in der Region Seattle werden bei einer herkömmlichen Suche standardmäßig keine Treffer zurückgegeben. Im folgenden Beispiel werden keine Ergebnisse zurückgegeben.

#### <a name="example-misspelled-term-unhandled-searchsamamish"></a>Beispiel (falsch geschriebener Begriff, keine Verarbeitung): `search=samamish`

Sie können die Fuzzysuche verwenden, damit auch Begriffe mit Rechtschreibfehlern einbezogen werden. Die Fuzzysuche wird aktiviert, wenn Sie die vollständige Lucene-Abfragesyntax verwenden. Dies ist der Fall, wenn Sie zwei Schritte ausführen: Festlegen von **queryType=full** für die Abfrage und Anfügen von **~** an die Suchzeichenfolge.

#### <a name="example-misspelled-term-handled-searchsamamishquerytypefull"></a>Beispiel (falsch geschriebener Begriff, Verarbeitung): `search=samamish~&queryType=full`

In diesem Beispiel werden jetzt Dokumente zurückgegeben, die Treffer zu „Sammamish“ enthalten.

Ohne Angabe von **queryType** wird der standardmäßige Parser für einfache Abfragen verwendet. Der Parser für einfache Abfragen ist zwar schneller, Fuzzysuche, reguläre Ausdrücke, NEAR-Suche und andere erweiterte Abfragetypen stehen jedoch nur bei Verwendung der vollständigen Syntax zur Verfügung.

Die Fuzzysuche und die Platzhaltersuche haben Auswirkungen auf die Suchausgabe. Die linguistische Analyse wird für diese Abfrageformate nicht durchgeführt. Bevor Sie die Fuzzy- und Platzhaltersuche verwenden, sollten Sie sich unter [Funktionsweise der Volltextsuche in Azure Search – Phase 2: Lexikalische Analyse](search-lucene-query-architecture.md#stage-2-lexical-analysis) den Abschnitt zu den Ausnahmen bei der lexikalischen Analyse durchlesen.

Weitere Informationen zu möglichen Abfrageszenarien mit dem Parser für vollständige Abfragen finden Sie unter [Lucene query syntax in Azure Search](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) (Lucene-Abfragesyntax in Azure Search).

### <a name="geo-search"></a> Ausprobieren der Geosuche

Die Geosuche wird über den [Datentyp „edm.GeographyPoint“](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) für ein Feld mit Koordinaten unterstützt. Die Geosuche ist ein Filtertyp und wird in der [OData-Ausdruckssyntax für Azure Search](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) angegeben.

#### <a name="example-geo-coordinate-filters-searchcounttruefiltergeodistancelocationgeographypoint-122121513-47673988-le-5"></a>Beispiel (Geokoordinatenfilter): `search=*&$count=true&$filter=geo.distance(location,geography'POINT(-122.121513 47.673988)') le 5`

Die Beispielabfrage filtert alle Ergebnisse nach Positionsdaten und gibt Ergebnisse zurück, die weniger als fünf Kilometer von einem bestimmten Punkt (angegeben als Koordinaten mit Breiten-und Längengrad) entfernt sind. Durch Hinzufügen von **$count** sehen Sie, wie viele Ergebnisse zurückgegeben werden, wenn Sie die Entfernung oder die Koordinaten ändern.

Die Geosuche ist hilfreich, wenn Ihre Suchanwendung über eine Umgebungssuche oder Kartennavigation verfügt. Es ist allerdings keine Volltextsuche. Falls eine Suche nach Städtename oder Länder-/Regionsname möglich sein muss, fügen Sie zusätzlich zu den Koordinaten Felder mit Städtenamen oder Länder-/Regionsnamen hinzu.

## <a name="takeaways"></a>Wesentliche Punkte

Dieses Tutorial enthält eine kurze Einführung, wie Azure Search im Azure-Portal verwendet werden kann.

Sie haben erfahren, wie Sie mithilfe des Assistenten **Daten importieren** einen Suchindex erstellen. Sie haben sich über [Indexer](search-indexer-overview.md) und über den grundlegenden Workflow für das Entwerfen von Indizes informiert, z.B. [unterstützte Änderungen an einem veröffentlichten Index](https://docs.microsoft.com/rest/api/searchservice/update-index).

Unter Verwendung des **Suchexplorers** im Azure-Portal haben Sie eine grundlegende Abfragesyntax anhand von praktischen Beispielen erlernt, und es wurden wichtige Funktionen wie Filter, Treffermarkierung, Fuzzysuche und die geografische Suche veranschaulicht.

Außerdem haben Sie gelernt, wie Indizes, Indexer und Datenquellen im Portal zu finden sind. Wenn Sie zukünftig Datenquellen verwenden, können Sie schnell und mit minimalem Aufwand über das Portal ihre Definitionen oder Felderauflistungen überprüfen.

## <a name="clean-up"></a>Bereinigen

Wenn Sie den Azure Search-Dienst in diesem Tutorial zum ersten Mal verwendet haben, löschen Sie die Ressourcengruppe, die den Azure Search-Dienst enthalten. Ist dies nicht das erste Mal, suchen Sie den entsprechenden Ressourcengruppennamen in der Liste der Dienste, und löschen Sie ihn.

## <a name="next-steps"></a>Nächste Schritte

Anhand der programmatischen Tools können Sie Azure Search genauer erkunden:

* [Erstellen eines Index mit dem .NET SDK](https://docs.microsoft.com/azure/search/search-create-index-dotnet)
* [Erstellen eines Index mit REST-APIs](https://docs.microsoft.com/azure/search/search-create-index-rest-api)
* [Erstellen eines Index mit Postman oder Fiddler und den Azure Search-REST-APIs](search-fiddler.md)
