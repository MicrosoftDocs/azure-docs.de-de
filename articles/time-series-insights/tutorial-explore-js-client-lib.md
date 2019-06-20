---
title: 'Tutorial: Erkunden der Azure Time Series Insights-JavaScript-Clientbibliothek | Microsoft-Dokumentation'
description: Informieren Sie sich über die Azure Time Series Insights-JavaScript-Clientbibliothek und das zugehörige Programmiermodell.
author: ashannon7
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 05/06/2019
ms.author: dpalled
ms.custom: seodec18
ms.openlocfilehash: c6cfd2069851138d738b1533eaab74d9d7aedda6
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66243984"
---
# <a name="tutorial-explore-the-azure-time-series-insights-javascript-client-library"></a>Tutorial: Erkunden der Azure Time Series Insights-JavaScript-Clientbibliothek

Die JavaScript D3-basierte TSI-Clientbibliothek wurde entwickelt, um Webentwickler beim Abfragen und Visualisieren von in Time Series Insights (TSI) gespeicherten Daten zu unterstützen. In diesem Tutorial werden die Time Series Insights-Clientbibliothek und das Programmiermodell anhand einer gehosteten Beispiel-App Schritt für Schritt beschrieben.

Im Tutorial wird das Arbeiten mit der Bibliothek, der Zugriff auf Time Series Insights-Daten und die Verwendung von Diagrammsteuerelementen zum Rendern und Visualisieren von Daten erläutert. Sie lernen auch, mit verschiedenen Arten von Diagrammen zu experimentieren, um Daten zu visualisieren. Am Ende des Tutorials können Sie die Clientbibliothek zum Integrieren von Time Series Insights-Funktionen in Ihre eigene Web-App verwenden.

Insbesondere erfahren Sie mehr zu:

> [!div class="checklist"]
> * Time Series Insights-Beispielanwendung
> * Time Series Insights-JavaScript-Clientbibliothek
> * Verwendung der Bibliothek durch die Beispielanwendung zum Visualisieren von Time Series Insights-Daten

> [!NOTE]
> * Im Tutorial wird eine kostenlose, gehostete [Time Series Insights-Webdemo](https://insights.timeseries.azure.com/clientsample) verwendet.
> * Die Quelldateien der Time Series Insights-Beispiel-App finden Sie im [GitHub-Beispielrepository](https://github.com/Microsoft/tsiclient/tree/tutorial/pages/tutorial).
> * Lesen Sie die [Referenzdokumentation zum Time Series Insights-Client](https://github.com/microsoft/tsiclient/blob/master/docs/API.md).

## <a name="video"></a>Video

In diesem Video stellen wir das Open Source-JavaScript-SDK für Time Series Insights vor:
<br /><br />

> [!VIDEO https://www.youtube.com/embed/X8sSm7Pl9aA]

## <a name="prerequisites"></a>Voraussetzungen

In diesem Tutorial wird das Feature **Entwicklertools** Ihres Browsers verwendet. In modernen Webbrowsern ([Microsoft Edge](/microsoft-edge/devtools-guide), [Chrome](https://developers.google.com/web/tools/chrome-devtools/), [Firefox](https://developer.mozilla.org/en-US/docs/Learn/Common_questions/What_are_browser_developer_tools), [Safari](https://developer.apple.com/safari/tools/) usw.) lässt sich die **Web Inspector-Ansicht** in der Regel über F12 auf der Tastatur aufrufen. Eine weitere Möglichkeit zum Zugreifen auf die Ansicht ist das Klicken mit der rechten Maustaste auf eine Webseite und das anschließende Auswählen von **Element prüfen**.

## <a name="time-series-insights-sample-application"></a>Time Series Insights-Beispielanwendung

Im gesamten Tutorial verwenden wir eine kostenlose, gehostete Time Series Insights-Beispiel-App, um den Quellcode der Anwendung und die Time Series Insights-JavaScript-Clientbibliothek zu erkunden. Anhand der Beispiel-App lernen Sie, wie Sie mit Time Series Insights in JavaScript interagieren und Daten in Diagrammen und Graphen visualisieren.

1. Navigieren Sie zur [Time Series Insights-Beispielanwendung](https://insights.timeseries.azure.com/clientsample). Die folgende Anmeldeaufforderung wird angezeigt:

   [![Time Series Insights-Clientbeispiel: Anmeldeaufforderung](media/tutorial-explore-js-client-lib/tcs-sign-in.png)](media/tutorial-explore-js-client-lib/tcs-sign-in.png#lightbox)

1. Wählen Sie **Anmelden** aus, um Ihre Anmeldeinformationen einzugeben oder auszuwählen. Verwenden Sie ein Unternehmenskonto (Azure Active Directory) oder ein persönliches Konto (Microsoft-Konto).

   [![Time Series Insights-Clientbeispiel: Eingabeaufforderung für Anmeldeinformationen](media/tutorial-explore-js-client-lib/tcs-sign-in-enter-account.png)](media/tutorial-explore-js-client-lib/tcs-sign-in-enter-account.png#lightbox)

1. Nach dem Anmelden wird eine Seite mit Diagrammen angezeigt, die Time Series Insights-Daten enthalten. Ihr Benutzerkonto und die Option **Abmelden** werden oben rechts angezeigt:

   [![Time Series Insights-Clientbeispiel: Hauptseite nach der Anmeldung](media/tutorial-explore-js-client-lib/tcs-main-after-signin.png)](media/tutorial-explore-js-client-lib/tcs-main-after-signin.png#lightbox)

### <a name="page-source-and-structure"></a>Quelle und Struktur der Seite

Zuerst betrachten wir den [HTML- und JavaScript-Quellcode](https://github.com/Microsoft/tsiclient/blob/tutorial/pages/tutorial/index.html) der gerenderten Webseite:

1. Öffnen Sie die **Entwicklertools** in Ihrem Browser. Sehen Sie sich die HTML-Elemente an, aus denen die aktuelle Seite besteht (auch als HTML- oder DOM-Struktur bezeichnet).

1. Erweitern Sie die Elemente `<head>` und `<body>`, und beachten Sie die folgenden Abschnitte:

   * Unter dem `<head>`-Element befinden sich Seitenmetadaten und Abhängigkeiten, die die Ausführung der App ermöglichen:
     * Ein `<script>`-Element, das zum Verweisen auf die ADAL-Datei *adal.min.js* (Azure Active Directory Authentication Library) verwendet wird. ADAL ist eine JavaScript-Bibliothek, über die die OAuth 2.0-Authentifizierung (Anmeldung) und die Tokenbeschaffung für den Zugriff auf APIs bereitgestellt werden.
     * Mehrere `<link>`-Elemente für Stylesheets (auch als *CSS* bezeichnet), z. B. *sampleStyles.css* und *tsiclient.css*. Mit den Stylesheets werden visuelle Formatierungsdetails für Seiten gesteuert, z. B. Farben, Schriftarten und Abstände.
     * Ein `<script>`-Element, das zum Verweisen auf die Time Series Insights-JavaScript-Clientbibliothek *tsiclient.js* verwendet wird. Die Bibliothek wird von der Seite genutzt, um Time Series Insights-Dienst-APIs aufzurufen und Diagrammsteuerelemente auf der Seite zu rendern.

     >[!NOTE]
     > * Der Quellcode für die JavaScript-ADAL steht im Repository [azure-activedirectory-library-for-js](https://github.com/AzureAD/azure-activedirectory-library-for-js) zur Verfügung.
     > * Der Quellcode für die Time Series Insights-JavaScript-Clientbibliothek ist im [tsiclient-Repository](https://github.com/Microsoft/tsiclient/tree/tutorial/pages/tutorial) enthalten.

   * Unter dem `<body>`-Element finden Sie `<div>`-Elemente, die das Layout von Elementen auf der Seite definieren, sowie ein weiteres `<script>`-Element:
     * Mit dem ersten `<div>`-Element wird das Dialogfeld **Anmelden** (`id="loginModal"`) angegeben.
     * Das zweite `<div>`-Element fungiert als übergeordnetes Element für:
       * Ein `<div>`-Headerelement, das oben auf der Seite (`class="header"`) für Statusmeldungen und Anmeldeinformationen verwendet wird.
       * Ein `<div>`-Element für die restlichen Elemente des Hauptteils der Seite, einschließlich der Diagramme (`class="chartsWrapper"`).
       * Ein `<script>`-Abschnitt, der den JavaScript-Code für die Steuerung der Seite enthält.

   [![Time Series Insights-Clientbeispiel mit Entwicklertools](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-head-body.png)](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-head-body.png#lightbox)

1. Wenn Sie das `<div class="chartsWrapper">`-Element erweitern, werden weitere untergeordnete `<div>`-Elemente angezeigt. Diese Elemente werden verwendet, um die einzelnen Beispiele für Diagrammsteuerelemente zu positionieren. Es sind verschiedene `<div>`-Elementpaare vorhanden, und zwar eines für jedes Diagrammbeispiel:

   * Das erste Element (`class="rowOfCardsTitle"`) enthält einen Kurztitel, der beschreibt, was die Diagramme veranschaulichen. Beispiel: `Static Line Charts With Full-Size Legends.`
   * Das zweite Element (`class="rowOfCards"`) ist ein übergeordnetes Element, das weitere untergeordnete `<div>`-Elemente enthält, mit denen die eigentlichen Diagrammsteuerelemente in einer Zeile positioniert werden.

   [![Body-div-Elemente](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-divs.png)](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-divs.png#lightbox)

1. Erweitern Sie das `<script type="text/javascript">`-Element direkt unter dem `<div class="chartsWrapper">`-Element. Der Anfang des JavaScript-Abschnitts auf Seitenebene wird verwendet, um die gesamte Seitenlogik (Authentifizieren, Aufrufen von Time Series Insights-Dienst-APIs, Rendern der Diagrammsteuerelemente usw.) zu verarbeiten:

   [![Textskript](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-script.png)](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-script.png#lightbox)

## <a name="time-series-insights-javascript-client-library-concepts"></a>Konzepte der Time Series Insights-JavaScript-Clientbibliothek

Die Time Series Insights-Clientbibliothek (*tsclient.js*) verfügt über Abstraktionen für zwei wichtige JavaScript-Funktionalitäten:

* **Wrappermethoden für den Aufruf der Time Series Insights-Abfrage-APIs**: REST-APIs, mit denen Sie Time Series Insights-Daten unter Verwendung von Aggregatausdrücken abrufen können. Die Methoden sind unter dem Namespace „TsiClient.Server“ der Bibliothek organisiert.

* **Methoden zum Erstellen und Auffüllen mehrerer Typen von Diagrammsteuerelementen:** Methoden, die Sie zum Rendern der Time Series Insights-Aggregatdaten auf einer Webseite verwenden können. Die Methoden sind unter dem Namespace „TsiClient.UX“ der Bibliothek organisiert.

Durch diese Vereinfachungen können Entwickler Benutzeroberflächenkomponenten für Graphen und Diagramme mit Einbindung von Time Series Insights-Daten leichter erstellen.

### <a name="authentication"></a>Authentication

Die [Time Series Insights-Beispielanwendung](https://insights.timeseries.azure.com/clientsample) ist eine Einzelseiten-App (Single-Page App, SPA), für die die OAuth 2.0-Benutzerauthentifizierung von ADAL unterstützt wird:

1. Bei der Verwendung von ADAL zur Authentifizierung muss die Client-App in Azure Active Directory (Azure AD) registriert sein. Tatsächlich ist die Einzelseiten-App für die Verwendung des [Ablaufs der impliziten OAuth 2.0-Gewährung](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-implicit-grant-flow) registriert.
1. Die Anwendung muss zur Laufzeit einige der Registrierungseigenschaften angeben. Zu den Eigenschaften gehören die Client-GUID (`clientId`) und der Umleitungs-URI (`postLogoutRedirectUri`).
1. Später fordert die App ein *Zugriffstoken* von Azure AD an. Das Zugriffstoken wird für einen begrenzten Satz an Berechtigungen für einen bestimmten Dienst- oder API-Bezeichner (https:\//api.timeseries.azure.com) ausgestellt. Die Tokenberechtigungen werden im Namen des angemeldeten Benutzers ausgegeben. Der Bezeichner für den Dienst bzw. die API ist eine weitere Eigenschaft, die in der Azure AD-Registrierung der App enthalten ist.
1. Nachdem ADAL das Zugriffstoken an die App zurückgegeben hat, wird es beim Zugriff auf die Time Series Insights-Dienst-APIs als *Bearertoken* übergeben.

   [!code-javascript[head-sample](~/samples-javascript/pages/tutorial/index.html?range=147-204&highlight=3-7,34-37)]

> [!TIP]
> Weitere Informationen zu den von Microsoft unterstützten Azure AD-Authentifizierungsbibliotheken finden Sie in der Referenzdokumentation unter [Azure Active Directory-Authentifizierungsbibliotheken](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries#microsoft-supported-client-libraries).

### <a name="control-identification"></a>Identifizierung der Steuerelemente

Im vorliegenden Beispiel sind `<div>`-Elemente im übergeordneten `<body>`-Element so angeordnet, dass ein sinnvolles Layout für die auf der Seite gerenderten Diagrammsteuerelemente erstellt wird.

Jedes `<div>`-Element gibt Eigenschaften für die Positionierung und visuellen Attribute von Diagrammsteuerelementen an. Die `id`-Eigenschaften des HTML-Elements dienen als eindeutige Bezeichner, die zum Rendern und Aktualisieren visualisierter Daten an bestimmte Steuerelemente gebunden werden.

### <a name="aggregate-expressions"></a>Aggregatausdrücke

Für die APIs der Time Series Insights-Clientbibliothek werden Aggregatausdrücke verwendet:

* Ein Aggregatausdruck bietet die Möglichkeit, einen oder mehrere *Suchbegriffe* zu erstellen.

* Die Client-APIs sind so konzipiert, dass sie eine ähnliche Funktionalität für eine andere Demo-App ([Time Series Insights-Explorer](https://insights.timeseries.azure.com/demo)) bereitstellen, die Suchzeitspannen, `where`-Prädikate, Measures und `splitBy`-Werte verwendet.

* Für die meisten Clientbibliothek-APIs wird ein Array mit Aggregatausdrücken genutzt, die vom Dienst zum Erstellen einer Time Series Insights-Datenabfrage verwendet werden.

### <a name="call-pattern"></a>Aufrufmuster

Das Auffüllen und Rendern von Diagrammsteuerelementen folgt einem allgemeinen Muster. Das allgemeine Muster lässt sich in der gesamten Beispiel-App beobachten und kann Ihnen bei der Nutzung der Clientbibliothek als Hilfe dienen:

1. Deklarieren Sie ein `array`-Element so, dass es mindestens einen Time Series Insights-Aggregatausdruck enthält:

   ```javascript
   var aes =  [];
   ```

1. Erstellen Sie *1* bis *n* Aggregatausdrucksobjekte. Fügen Sie diese dann dem Aggregatausdrucksarray hinzu:

   ```javascript
   var ae = new tsiClient.ux.aggregateExpression(predicateObject, measureObject, measureTypes, searchSpan, splitByObject, color, alias, contextMenuActions);
   aes.push(ae);
   ```

   **aggregateExpression-Parameter**

   | Parameter | BESCHREIBUNG | Beispiel |
   | --------- | ----------- | ------- |
   | `predicateObject` | Der Datenfilterausdruck |`{predicateString: "Factory = 'Factory3'"}` |
   | `measureObject`   | Der Eigenschaftenname des verwendeten Measures | `{property: 'Temperature', type: "Double"}` |
   | `measureTypes`    | Die Aggregationen der gewünschten Measureeigenschaft | `['avg', 'min']` |
   | `searchSpan`      | Die Dauer und Intervallgröße des Aggregatausdrucks | `{from: startDate, to: endDate, bucketSize: '2m'}` |
   | `splitByObject`   | Die Zeichenfolgeneigenschaft, nach der die Aufteilung erfolgen soll (optional: kann NULL sein) | `{property: 'Station', type: 'String'}` |
   | `color`         | Die Farbe der Objekte, die Sie rendern möchten | `'pink'` |
   | `alias`           | Ein Anzeigename für den Aggregatausdruck | `'Factory3Temperature'` |
   | `contextMenuActions` | Ein Array mit Aktionen, die an die Zeitreihenobjekte in einer Visualisierung gebunden werden sollen (optional) | Weitere Informationen finden Sie unter [Popupkontextmenüs](#pop-up-context-menus). |

1. Rufen Sie eine Time Series Insights-Abfrage auf, indem Sie die TsiClient.Server-APIs zum Anfordern der Aggregatdaten verwenden:

   ```javascript
   tsiClient.server.getAggregates(token, envFQDN, aeTsxArray);
   ```

   **getAggregates-Parameter**

   | Parameter | BESCHREIBUNG | Beispiel |
   | --------- | ----------- | ------- |
   | `token`     | Zugriffstoken für die Time Series Insights-API |  `authContext.getTsiToken()`<br />Weitere Informationen finden Sie unter [Authentifizierung](#authentication). |
   | `envFQDN`   | Der vollqualifizierte Domänenname (FQDN) für die Time Series Insights-Umgebung | Über das Azure-Portal. Beispiel: `10000000-0000-0000-0000-100000000108.env.timeseries.azure.com`. |
   | `aeTsxArray` | Ein Array mit Time Series Insights-Abfrageausdrücken | Verwenden Sie die Variable `aes` wie oben beschrieben: `aes.map(function(ae){return ae.toTsx()}`. |

1. Transformieren Sie das von der Time Series Insights-Abfrage zurückgegebene komprimierte Ergebnis zur Visualisierung in JSON-Code:

   ```javascript
   var transformedResult = tsiClient.ux.transformAggregatesForVisualization(result, aes);
   ```

1. Erstellen Sie mit den TsiClient.UX-APIs ein Diagrammsteuerelement. Binden Sie es an eines der `<div>`-Elemente auf der Seite:

   ```javascript
   var barChart = new tsiClient.ux.BarChart(document.getElementById('chart3'));
   ```

1. Füllen Sie das Diagrammsteuerelement mit den transformierten JSON-Datenobjekten auf, und rendern Sie das Steuerelement auf der Seite:

   ```javascript
   barChart.render(transformedResult, {grid: true, legend: 'compact', theme: 'light'}, aes);
   ```

## <a name="render-controls"></a>Rendern von Steuerelementen

Die Time Series Insights-Clientbibliothek verfügt über acht einzigartige Steuerelemente für die Analyse, die sofort einsatzbereit sind:

* **Liniendiagramm**
* **Kreisdiagramm**
* **Balkendiagramm**
* **heatmap**
* **Hierarchiesteuerelemente**
* **Raster mit Zugriffsmöglichkeit**
* **diskrete Ereigniszeitreihen**
* **Zustandsübergang-Zeitreihen**

### <a name="line-chart-bar-chart-and-pie-chart-examples"></a>Beispiele für Liniendiagramm, Balkendiagramm und Kreisdiagramm

Sehen Sie sich den Democode an, mit dem einige Standard-Diagrammsteuerelemente gerendert werden. Beachten Sie das Programmiermodell und die Muster für die Erstellung dieser Steuerelemente. Achten Sie besonders auf den HTML-Code unter dem Kommentar `// Example 3/4/5`, der die Steuerelemente mit den HTML-`id`-Werten `chart3`, `chart4` und `chart5` rendert.

Sie erinnern sich sicher aus Schritt 3 im Abschnitt [Quelle und Struktur der Seite](#page-source-and-structure) daran, dass Diagrammsteuerelemente in Zeilen auf der Seite angeordnet sind. Jedes Diagrammsteuerelement verfügt über eine Zeile mit einem beschreibenden Titel. In diesem Beispiel werden die drei Diagramme unter dem `<div>`-Element des Titels `Multiple Chart Types From the Same Data` aufgefüllt und sind an die drei `<div>`-Elemente unterhalb des Titels gebunden:

[!code-html[code-sample1-line-bar-pie](~/samples-javascript/pages/tutorial/index.html?range=59-73&highlight=1,5,9,13)]

Im folgenden Abschnitt des JavaScript-Codes werden die oben beschriebenen Muster verwendet: Erstellen von Time Series Insights-Aggregatausdrücken, Verwenden dieser Ausdrücke zum Abfragen von Time Series Insights-Daten und anschließendes Rendern der drei Diagramme. Es werden drei Diagrammtypen des Namespace „tsiClient.ux“ verwendet: `LineChart`, `BarChart` und `PieChart`. Die Diagrammtypen werden zum Erstellen und Rendern der entsprechenden Diagramme genutzt. Für alle drei Diagramme können die gleichen Aggregatausdrucksdaten (`transformedResult`) verwendet werden:

[!code-javascript[code-sample2-line-bar-pie](~/samples-javascript/pages/tutorial/index.html?range=241-262&highlight=13-14,16-17,19-20)]

Die drei Diagramme sehen nach dem Rendern folgendermaßen aus:

[![Multiple chart types from the same data](media/tutorial-explore-js-client-lib/tcs-multiple-chart-types-from-the-same-data.png)](media/tutorial-explore-js-client-lib/tcs-multiple-chart-types-from-the-same-data.png#lightbox)

## <a name="advanced-features"></a>Erweiterte Funktionen

Die Time Series Insights-Clientbibliothek verfügt über mehrere zusätzliche Funktionen, mit denen Sie Datenvisualisierungen kreativ implementieren können.

### <a name="states-and-events"></a>Zustände und Ereignisse

Eine erweiterte Funktionalität ist die Möglichkeit, Diagrammen Zustandsübergänge und diskrete Ereignisse hinzuzufügen. Diese Funktion ist nützlich zum Hervorheben von Incidents, für Warnungen sowie für das Erstellen von Zustandsschaltern (z. B. Ein/Aus).

Sehen Sie sich den Code um den Kommentar `// Example 10` an. Der Code rendert unter dem Titel `Line Charts with Multiple Series Types` ein Liniendiagramm-Steuerelement und bindet es an das `<div>`-Element mit dem HTML-`id`-Wert `chart10`.

Die folgenden Schritte beschreiben den Prozess:

1. Es wird eine Struktur mit dem Namen `events4` definiert, in der die nachzuverfolgenden Zustandswechselelemente enthalten sind. Die Struktur enthält Folgendes:

   * Einen Zeichenfolgenschlüssel mit dem Namen `Component States`.
   * Ein Array mit Wertobjekten, die die Zustände darstellen. Jedes Objekt enthält Folgendes:
     * Einen Zeichenfolgenschlüssel mit einem JavaScript-ISO-Zeitstempel.
     * Ein Array mit den Zustandsmerkmalen: eine Farbe und eine Beschreibung.

1. Die Struktur `events5` wird für das `Incidents`-Element definiert, das ein Array der nachzuverfolgenden Ereigniselemente enthält. Die Arraystruktur weist die gleiche Form wie die Struktur auf, die für `events4` angegeben ist.

1. Das Liniendiagramm wird gerendert und übergibt die beiden Strukturen mit den Optionsparametern für das Diagramm: `events:` und `states:`. Beachten Sie die weiteren Optionsparameter zum Angeben eines `tooltip:`-, `theme:`- oder `grid:`-Elements.

[!code-javascript[code-sample-states-events](~/samples-javascript/pages/tutorial/index.html?range=337-389&highlight=5,26,51)]

Die rautenförmigen Marker/Popupfenster werden verwendet, um auf Incidents hinzuweisen, und mit den farbigen Balken/Popupfenstern entlang der Zeitskala werden Zustandsänderungen angegeben:

[![Liniendiagramme mit mehreren Serientypen](media/tutorial-explore-js-client-lib/tcs-line-charts-with-multiple-series-types.png)](media/tutorial-explore-js-client-lib/tcs-line-charts-with-multiple-series-types.png#lightbox)

### <a name="pop-up-context-menus"></a>Popupkontextmenüs

Eine weitere erweiterte Funktionalität ist die Möglichkeit zum Erstellen von benutzerdefinierten Kontextmenüs (per Rechtsklick aufrufbare Popupmenüs). Benutzerdefinierte Kontextmenüs eignen sich gut zum Ermöglichen von Aktionen und logischen nächsten Schritten innerhalb des Bereichs Ihrer Anwendung.

Sehen Sie sich den Code in der Nähe des Kommentars `// Example 13/14/15` an. Mit diesem Code wird zunächst ein Liniendiagramm unter dem Titel `Line Chart with Context Menu to Create Pie/Bar Chart` gerendert. Das Diagramm ist an das `<div>`-Element mit dem HTML-`id`-Wert `chart13` gebunden.

Mithilfe von Kontextmenüs stellt das Liniendiagramm die Möglichkeit bereit, dynamisch ein Kreis- und ein Balkendiagramm zu erstellen, die an `<div>`-Elemente mit den IDs `chart14` und `chart15` gebunden sind. Sowohl für das Kreis- als auch für das Balkendiagramm werden Kontextmenüs genutzt, um jeweils eigene Funktionen zu aktivieren: Kopieren von Daten aus dem Kreis- in das Balkendiagramm und Ausgeben der Balkendiagrammdaten im Browserkonsolenfenster.

Die folgenden Schritte beschreiben den Prozess:

1. Eine Reihe von benutzerdefinierten Aktionen wird definiert. Jede Aktion enthält ein Array mit einem oder mehreren Elementen. Mit jedem Element wird ein einzelnes Kontextmenüelement definiert:

   * `barChartActions`: Mit dieser Aktion wird das Kontextmenü für das Kreisdiagramm definiert, das ein Element zum Definieren eines einzelnen Elements enthält:
     * `name`: Der Text, der für das Menüelement verwendet wird: "Parameter an der Konsole ausgeben".
     * `action`: Die Aktion, die dem Menüelement zugeordnet ist. Die Aktion ist immer eine anonyme Funktion, für die drei Argumente basierend auf dem Aggregatausdruck verwendet werden, mit dem das Diagramm erstellt wird. In diesem Fall werden die Argumente in das Browserkonsolenfenster geschrieben:
       * `ae`: Das Array mit den Aggregatausdrücken.
       * `splitBy`: Der `splitBy`-Wert.
       * `timestamp`: Der Zeitstempel.

   * `pieChartActions`: Diese Aktion definiert das Kontextmenü für das Balkendiagramm, das ein Element enthält, um ein einzelnes Element zu definieren. Form und Schema entsprechen dem `barChartActions`-Element, das weiter oben beschrieben wurde. Die Funktion `action` unterscheidet sich hiervon aber erheblich, da sie das Balkendiagramm instanziiert und rendert. Das `ae`-Argument wird verwendet wird, um das Array mit dem Aggregatausdruck anzugeben, das zur Laufzeit beim Öffnen des Menüelements übergeben wird. Die Funktion legt auch die `ae.contextMenu`-Eigenschaft mit dem `barChartActions`-Kontextmenü fest.
   * `contextMenuActions`: Mit dieser Aktion wird das Kontextmenü für das Liniendiagramm definiert, das drei Elemente enthält, um drei Menüelemente zu definieren. Die Form und das Schema für die einzelnen Elemente entsprechen der Form und dem Schema der oben beschriebenen Elemente. Genauso wie beim `barChartActions`-Element schreibt das erste Element die drei Funktionsargumente in das Browserkonsolenfenster. Ebenso wie beim `pieChartActions`-Element instanziieren und rendern die letzten beiden Elemente das Kreis- bzw. Balkendiagramm. Die letzten beiden Elemente legen auch ihre `ae.contextMenu`-Eigenschaften mit den Kontextmenüs `pieChartActions` bzw. `barChartActions` fest.

1. Zwei Aggregatausdrücke werden per Pushvorgang in das Array `aes` für Aggregatausdrücke übertragen. Hiermit wird das Array `contextMenuActions` für jedes Element angegeben. Diese Ausdrücke werden mit dem Liniendiagramm-Steuerelement verwendet.

1. Zunächst wird nur das Liniendiagramm gerendert. Basierend hierauf kann zur Laufzeit sowohl das Kreis- als auch das Balkendiagramm gerendert werden.

[!code-javascript[code-sample-context-menus](~/samples-javascript/pages/tutorial/index.html?range=461-540&highlight=7,16,29,61-64,78)]

Im folgenden Screenshot sind die Diagramme mit ihren jeweiligen Popupkontextmenüs dargestellt. Das Kreis- und das Balkendiagramm wurden mithilfe der Kontextmenüoptionen des Liniendiagramms dynamisch erstellt.

[![Liniendiagramm mit Kontextmenü für die Erstellung eines Kreis- und Balkendiagramms](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart.png)](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart.png#lightbox)

### <a name="brushes"></a>Pinsel

Mit Pinseln können Sie einen Zeitbereich angegeben, um Aktionen wie das Zoomen und Untersuchen zu definieren.

Der Code zum Darstellen von Pinseln wird im Beispiel `Line Chart with Context Menu to Create Pie/Bar Chart` veranschaulicht, in dem Popupkontextmenüs beschrieben werden.

* Pinselaktionen ähneln einem Kontextmenü darin, dass sie eine Reihe von benutzerdefinierten Aktionen für den Pinsel definieren. Jede Aktion enthält ein Array mit einem oder mehreren Elementen. Mit jedem Element wird ein einzelnes Kontextmenüelement definiert:
   * `name`: Der Text, der für das Menüelement verwendet wird: "Parameter an der Konsole ausgeben".
   * `action`: Die Aktion, die mit dem Menüelement verknüpft ist. Hierbei handelt es sich immer um eine anonyme Funktion, für die zwei Argumente verwendet werden. In diesem Fall werden die Argumente in das Browserkonsolenfenster geschrieben:
     * `fromTime`: Der `from`-Zeitstempel der Pinselauswahl.
     * `toTime`: Der `to`-Zeitstempel der Pinselauswahl.

* Pinselaktionen werden als weitere Diagrammoptionseigenschaft hinzugefügt. Die `brushContextMenuActions: brushActions`-Eigenschaft wird an den `linechart.Render`-Aufruf übergeben.

[!code-javascript[code-sample-brushes](~/samples-javascript/pages/tutorial/index.html?range=526-540&highlight=1,13)]

[![Liniendiagramm mit Kontextmenü für die Erstellung eines Kreis- und Balkendiagramms mit Pinseln](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart-brushes.png)](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart-brushes.png#lightbox)

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Anmelden und Erkunden der Time Series Insights-Beispielanwendung und der zugehörigen Quelle
> * Verwenden von APIs in der Time Series Insights-JavaScript-Clientbibliothek
> * Verwenden von JavaScript zum Erstellen und Auffüllen von Diagrammsteuerelementen mit Time Series Insights-Daten

In der Time Series Insights-Beispielanwendung wird ein Demodataset verwendet. Lesen Sie den folgenden Artikel, um zu erfahren, wie Sie Ihre eigene Time Series Insights-Umgebung und ein Dataset erstellen können:

> [!div class="nextstepaction"]
> [Tutorial: Erstellen einer Azure Time Series Insights-Umgebung](tutorial-create-populate-tsi-environment.md)

Oder zeigen Sie die Quelldateien der Time Series Insights-Beispielanwendung an:

> [!div class="nextstepaction"]
> [Repository mit Time Series Insights-Beispiel-App](https://github.com/Microsoft/tsiclient/tree/tutorial/pages/tutorial)

Lesen Sie die Referenzdokumentation zur Time Series Insights-Client-API:

> [!div class="nextstepaction"]
> [Referenzdokumentation zur Time Series Insights-API](https://github.com/microsoft/tsiclient/blob/master/docs/API.md)
