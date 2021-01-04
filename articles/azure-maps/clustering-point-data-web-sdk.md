---
title: Clustering von Punktdaten auf einer Karte | Microsoft Azure Maps
description: Hier wird erläutert, wie Sie Punktdaten auf Karten gruppieren. Sie erfahren, wie Sie mit dem Azure Maps Web SDK Daten gruppieren, auf Mausereignisse für den Cluster reagieren und Clusteraggregate anzeigen.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 9ecde4cb0c8a3bfe3dd8fb2edb59423838e0751c
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92889866"
---
# <a name="clustering-point-data"></a>Clustering von Punktdaten

Wenn auf der Karte viele Datenpunkte visualisiert werden, kann es bei diesen Datenpunkten zu gegenseitigen Überlappungen kommen. Die Überlappungen können dazu führen, dass die Karte unlesbar und schwer zu verwenden ist. Das Clustering von Punktdaten ist der Prozess, bei dem benachbarte Punktdaten kombiniert und auf der Karte als ein einzelner gruppierter Datenpunkt dargestellt werden. Wenn der Benutzer in die Karte zoomt, werden die Cluster in ihre einzelnen Datenpunkte unterteilt. Wenn Sie mit einer großen Anzahl von Datenpunkten arbeiten, verwenden Sie die Clusteringprozesse, um die Erfahrung Ihrer Benutzer zu verbessern.

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Clustering-point-data-in-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="enabling-clustering-on-a-data-source"></a>Aktivieren des Clusterings für eine Datenquelle

Clustering aktivieren Sie in der `DataSource`-Klasse, indem Sie die Option `cluster` auf „true“ festlegen. Durch Festlegen von `ClusterRadius` werden nahe gelegene Punkte ausgewählt und zu einem Cluster kombiniert. Der Wert von `ClusterRadius` wird in Pixel angegeben. Verwenden Sie `clusterMaxZoom`, um eine Zoomfaktor anzugeben, ab dem die Clusteringlogik deaktiviert werden soll. Hier folgt ein Beispiel, wie Sie das Clustering in einer Datenquelle aktivieren können.

```javascript
//Create a data source and enable clustering.
var datasource = new atlas.source.DataSource(null, {
    //Tell the data source to cluster point data.
    cluster: true,

    //The radius in pixels to cluster points together.
    clusterRadius: 45,

    //The maximum zoom level in which clustering occurs.
    //If you zoom in more than this, all points are rendered as symbols.
    clusterMaxZoom: 15
});
```

> [!TIP]
> Wenn zwei Datenpunkte im Gelände dicht beieinander liegen, ist es möglich, dass der Cluster nie aufgelöst wird, unabhängig davon, wie stark der Benutzer vergrößert. Um dies zu umgehen, können Sie die Option `clusterMaxZoom` so festlegen, dass die Clusteringlogik deaktiviert und somit einfach alles angezeigt wird.

Im Folgenden finden Sie weitere Methoden, die die `DataSource`-Klasse für das Clustering bereitstellt:

| Methode | Rückgabetyp | BESCHREIBUNG |
|--------|-------------|-------------|
| getClusterChildren(clusterId: number) | Promise&lt;Array&lt;Feature&lt;Geometry, any&gt; \| Shape&gt;&gt; | Ruft die untergeordneten Elemente des angegebenen Clusters für den nächsten Zoomfaktor ab. Diese untergeordneten Elemente können eine Kombination aus Formen und untergeordneten Clustern sein. Die untergeordneten Cluster sind Features mit Eigenschaften, die ClusteredProperties entsprechen. |
| getClusterExpansionZoom(clusterId: number) | Promise&lt;number&gt; | Berechnet einen Zoomfaktor, bei dem der Cluster mit der Erweiterung oder Unterteilung beginnt. |
| getClusterLeaves(clusterId: number, limit: number, offset: number) | Promise&lt;Array&lt;Feature&lt;Geometry, any&gt; \| Shape&gt;&gt; | Ruft alle Punkte in einem Cluster ab. Legen Sie `limit` fest, um eine Teilmenge der Punkte zurückzugeben, und verwenden Sie `offset`, um die Punkte zu durchlaufen. |

## <a name="display-clusters-using-a-bubble-layer"></a>Anzeigen von Clustern mithilfe einer Blasenebene

Eine Blasenebene ist eine hervorragende Möglichkeit, um geclusterte Datenpunkte zu rendern. Verwenden Sie Ausdrücke zum Skalieren des Radius, und ändern Sie die Farbe basierend auf der Anzahl der Punkte im Cluster. Bei der Darstellung von Clustern mit einer Blasenebene sollten Sie eine separate Ebene für das Rendering nicht geclusterter Datenpunkte verwenden.

Um die Größe des Clusters auf der Blase anzuzeigen, verwenden Sie eine Symbolebene mit Text und verwenden kein Symbol.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Einfaches Clustering auf Blasenebene" src="//codepen.io/azuremaps/embed/qvzRZY/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Weitere Informationen finden Sie unter dem Pen <a href='https://codepen.io/azuremaps/pen/qvzRZY/'>Basic bubble layer clustering (Einfaches Clustering auf Blasenebene)</a> von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="display-clusters-using-a-symbol-layer"></a>Anzeigen von Clustern mithilfe einer Symbolebene

Beim Visualisieren von Datenpunkten blendet die Symbolebene automatisch Symbole aus, die einander überlappen, um eine klarere Benutzeroberfläche sicherzustellen. Dieses Standardverhalten ist möglicherweise unerwünscht, wenn Sie die Datenpunktdichte auf der Karte anzeigen möchten. Diese Einstellungen können jedoch geändert werden. Um alle Symbole anzuzeigen, legen Sie die Option `allowOverlap` der `iconOptions`-Eigenschaft der Symbolebenen auf `true` fest. 

Verwenden Sie Clustering, um die Datenpunktdichte anzuzeigen und gleichzeitig eine aufgeräumte Benutzeroberfläche zu erhalten. Im Folgenden Beispiel wird gezeigt, wie Sie mithilfe der Symbolebene benutzerdefinierte Symbole hinzufügen sowie Cluster und einzelne Datenpunkte darstellen.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Gruppierte Symbolebene" src="//codepen.io/azuremaps/embed/Wmqpzz/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Weitere Informationen finden Sie unter dem Pen <a href='https://codepen.io/azuremaps/pen/Wmqpzz/'>Clustered Symbol layer (Gruppierte Symbolebene)</a> von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="clustering-and-the-heat-maps-layer"></a>Clustering und Wärmebildebene

Wärmebilder sind eine hervorragende Möglichkeit, die Dichte der Daten auf der Karte anzuzeigen. Diese Visualisierungsmethode kann eigenständig eine große Anzahl von Datenpunkten verarbeiten. Wenn die Datenpunkte geclustert werden und die Clustergröße als Gewichtung des Wärmebilds verwendet wird, kann das Wärmebild sogar noch mehr Daten handhaben. Um diese Option zu realisieren, legen Sie die `weight`-Option der Wärmebildebene auf `['get', 'point_count']` fest. Bei einem kleinen Clusterradius sieht das Wärmebild nahezu identisch zu einem Wärmebild aus, das die nicht gruppierten Datenpunkte verwendet. Es wird aber viel besser funktionieren. Je kleiner der Clusterradius, desto genauer ist das Wärmebild, jedoch mit geringerem Leistungsvorteil.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Wärmebild mit gewichteten Clustern" src="//codepen.io/azuremaps/embed/VRJrgO/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Weitere Informationen finden Sie unter dem Pen <a href='https://codepen.io/azuremaps/pen/VRJrgO/'>Cluster weighted Heat Map (Wärmebild mit gewichteten Clustern)</a> von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="mouse-events-on-clustered-data-points"></a>Mausereignisse für gruppierte Datenpunkte

Wenn Mausereignisse auf einer Ebene auftreten, die gruppierte Datenpunkte enthält, wird der gruppierte Datenpunkt als GeoJSON-Punktfeatureobjekt an das Ereignis zurückgegeben. Dieses Punktfeature weist die folgenden Eigenschaften auf:

| Eigenschaftenname             | type    | BESCHREIBUNG   |
|---------------------------|---------|---------------|
| `cluster`                 | boolean | Gibt an, ob das Feature einen Cluster darstellt. |
| `cluster_id`              | Zeichenfolge  | Eine eindeutige ID für den Cluster, die mit den DataSource-Methoden `getClusterExpansionZoom`, `getClusterChildren` und `getClusterLeaves` verwendet werden kann. |
| `point_count`             | number  | Die Anzahl der Punkte, die der Cluster enthält.  |
| `point_count_abbreviated` | Zeichenfolge  | Eine Zeichenfolge, die den `point_count`-Wert abkürzt, falls er zu lang ist. (Beispiel: 4.000 wird zu 4K)  |

Dieses Beispiel nutzt eine Blasenebene, die Clusterpunkte rendert und ein Klickereignis hinzufügt. Wenn das Klickereignis ausgelöst wird, berechnet der Code die Karte und zoomt Sie auf die nächste Zoomstufe, bei der der Cluster aufgelöst wird. Diese Funktion wird mit der `getClusterExpansionZoom`-Methode der `DataSource`-Klasse und der `cluster_id`-Eigenschaft des angeklickten geclusterten Datenpunkts implementiert.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Cluster getClusterExpansionZoom" src="//codepen.io/azuremaps/embed/moZWeV/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Weitere Informationen finden Sie unter dem Pen <a href='https://codepen.io/azuremaps/pen/moZWeV/'>Cluster getClusterExpansionZoom</a> von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="display-cluster-area"></a>Anzeigen des Clusterbereichs 

Die Punktdaten, die ein Cluster darstellt, sind über einen Bereich verteilt. In diesem Beispiel treten zwei Hauptverhaltensweisen auf, wenn mit der Maus auf einen Cluster gezeigt wird. Zuerst werden die einzelnen Datenpunkte, die im Cluster enthalten sind, verwendet, um eine konvexe Hülle zu berechnen. Anschließend wird die konvexe Hülle auf der Karte angezeigt, um einen Bereich darzustellen.  Eine konvexe Hülle ist ein Polygon, das einen Satz von Punkten wie ein elastisches Band umhüllt und mit der `atlas.math.getConvexHull`-Methode berechnet werden kann. Alle in einem Cluster enthaltenen Punkte können mit der Methode `getClusterLeaves` aus der Datenquelle abgerufen werden.

<br/>

 <iframe height="500" style="width: 100%;" scrolling="no" title="Konvexe Hülle des Clusterbereichs" src="//codepen.io/azuremaps/embed/QoXqWJ/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Weitere Informationen finden Sie unter dem Pen <a href='https://codepen.io/azuremaps/pen/QoXqWJ/'>Cluster area convex hull (Konvexe Hülle des Clusterbereichs)</a> von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="aggregating-data-in-clusters"></a>Aggregieren von Daten in Clustern

Cluster werden oft mithilfe eines Symbols mit der Anzahl der im Cluster enthaltenen Punkte dargestellt. Manchmal ist es jedoch wünschenswert, den Clusterstil mit zusätzlichen Metriken anzupassen. Mit Clusteraggregaten können benutzerdefinierte Eigenschaften erstellt und dann mithilfe einer Berechnung vom Typ [Aggregatausdruck](data-driven-style-expressions-web-sdk.md#aggregate-expression) aufgefüllt werden.  Clusteraggregate können in der Option `clusterProperties` von `DataSource` definiert werden.

Im folgenden Beispiel wird ein Aggregatausdruck verwendet. Der Code berechnet eine Anzahl basierend auf der Eigenschaft „Entitätstyp“ der einzelnen Datenpunkte in einem Cluster. Wenn ein Benutzer auf einen Cluster klickt, wird ein Popup mit zusätzlichen Informationen über den Cluster angezeigt.

<iframe height="500" style="width: 100%;" scrolling="no" title="Clusteraggregate" src="//codepen.io/azuremaps/embed/jgYyRL/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Weitere Informationen finden Sie unter dem Pen <a href='https://codepen.io/azuremaps/pen/jgYyRL/'>Cluster aggregates</a> (Clusteraggregate) von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>Code Pen</a>.
</iframe>

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr zu den in diesem Artikel verwendeten Klassen und Methoden:

> [!div class="nextstepaction"]
> [DataSource-Klasse](/javascript/api/azure-maps-control/atlas.source.datasource)

> [!div class="nextstepaction"]
> [DataSourceOptions-Objekt](/javascript/api/azure-maps-control/atlas.datasourceoptions)

> [!div class="nextstepaction"]
> [atlas.math namespace](/javascript/api/azure-maps-control/atlas.math)

Sehen Sie sich Codebeispiele an, die zeigen, wie Sie Ihrer App Funktionen hinzufügen:

> [!div class="nextstepaction"]
> [Hinzufügen einer Blasenebene](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Hinzufügen einer Symbolebene](map-add-pin.md)

> [!div class="nextstepaction"]
> [Hinzufügen einer Wärmebildebene](map-add-heat-map-layer.md)