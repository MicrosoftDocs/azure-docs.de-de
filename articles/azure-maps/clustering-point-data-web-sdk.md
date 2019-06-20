---
title: Clustering von Punktdaten in Azure Maps | Microsoft-Dokumentation
description: Gruppieren von Punktdaten im Web-SDK
author: rbrundritt
ms.author: richbrun
ms.date: 03/27/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.custom: codepen
ms.openlocfilehash: 6dbd4461e7b8382ec3c4075b9688de59678f98f5
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65957319"
---
# <a name="clustering-point-data"></a>Clustering von Punktdaten

Bei der Visualisierung vieler Datenpunkte auf der Karte überlappen sich die Punkte, die Karte wirkt überladen und es wird schwierig, die Karte zu überblicken und zu verwenden. Das Clustering von Punktdaten kann dazu verwendet werden, die Benutzerfreundlichkeit zu erhöhen. Das Clustering von Punktdaten ist der Prozess, bei dem benachbarte Punktdaten kombiniert und auf der Karte als ein einzelner gruppierter Datenpunkt dargestellt werden. Wenn der Benutzer in die Karte zoomt, werden die Cluster in ihre einzelnen Datenpunkte unterteilt.

## <a name="enabling-clustering-on-a-data-source"></a>Aktivieren des Clustering für eine Datenquelle

Das Clustering kann für die Klasse `DataSource` einfach aktiviert werden, indem die Option `cluster` auf „true“ festgelegt wird. Zusätzlich kann der Pixelradius zum Auswählen von nahegelegenen Punkten, die zu einem Cluster kombiniert werden sollen, mithilfe von `clusterRadius` festgelegt werden. Des Weiteren kann ein Zoomfaktor angegeben werden, bei dem die Logik für das Clustering mit der Option `clusterMaxZoom` deaktiviert werden soll. Hier folgt ein Beispiel, wie Sie das Clustering in einer Datenquelle aktivieren können.

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
> Wenn zwei Datenpunkte vor Ort dicht beieinander liegen, ist es möglich, dass der Cluster nie unterteilt wird, unabhängig davon, wie stark der Benutzer zoomt. Um dies zu berücksichtigen, können Sie die Option `clusterMaxZoom` der Datenquelle festlegen, die für den Zoomfaktor angibt, dass die Clustering-Logik deaktiviert und somit einfach alles angezeigt wird.

Die Klasse `DataSource` verfügt im Hinblick auf das Clustering auch über die folgenden Methoden:

| Methode | Rückgabetyp | BESCHREIBUNG |
|--------|-------------|-------------|
| getClusterChildren(clusterId: number) | Promise&lt;Array&lt;Feature&lt;Geometry, any&gt; \| Shape&gt;&gt; | Ruft die untergeordneten Elemente des angegebenen Clusters für den nächsten Zoomfaktor ab. Diese untergeordneten Elemente können eine Kombination aus Formen und untergeordneten Clustern sein. Die untergeordneten Cluster sind Features mit Eigenschaften, die „ClusteredProperties“ entsprechen. |
| getClusterExpansionZoom(clusterId: number) | Promise&lt;number&gt; | Berechnet einen Zoomfaktor, bei dem der Cluster mit der Erweiterung oder Unterteilung beginnt. |
| getClusterLeaves(clusterId: number, limit: number, offset: number) | Promise&lt;Array&lt;Feature&lt;Geometry, any&gt; \| Shape&gt;&gt; | Ruft alle Punkte in einem Cluster ab. Legen Sie `limit` fest, um eine Teilmenge der Punkte zurückzugeben, und verwenden Sie `offset`, um die Punkte zu durchlaufen. |

## <a name="display-clusters-using-a-bubble-layer"></a>Anzeigen von Clustern mithilfe einer Blasenebene

Eine Blasenebene ist eine hervorragende Möglichkeit, um gruppierte Punkte darzustellen, da Sie den Radius leicht skalieren und die Farbe basierend auf der Anzahl der Punkte im Cluster mithilfe eines Ausdrucks ändern können. Bei der Darstellung von Clustern mit einer Blasenebene sollten Sie auch eine separate Ebene für das Rendering von nicht gruppierten Datenpunkten verwenden. Es ist oftmals hilfreich, auch die Größe des Clusters über den Blasen anzeigen zu können. Eine Symbolebene mit Text und ohne Symbol kann verwendet werden, um dieses Verhalten zu erreichen. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Einfaches Clustering auf Blasenebene" src="//codepen.io/azuremaps/embed/qvzRZY/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Weitere Informationen finden Sie unter dem Pen <a href='https://codepen.io/azuremaps/pen/qvzRZY/'>Basic bubble layer clustering (Einfaches Clustering auf Blasenebene)</a> von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="display-clusters-using-a-symbol-layer"></a>Anzeigen von Clustern mithilfe einer Symbolebene

Bei der Visualisierung der Punktdaten mithilfe der Symbolebene werden standardmäßig automatisch sich überlappende Symbole ausgeblendet, um einen übersichtlicheren Eindruck zu vermitteln. Dies ist jedoch möglicherweise nicht die gewünschte Erfahrung, wenn Sie die Dichte der Datenpunkte auf der Karte anzeigen möchten. Wenn Sie die Option `allowOverlap` der Eigenschaft `iconOptions` der Symbolebene auf `true` festlegen, wird dieses Verhalten deaktiviert. Es führt jedoch dazu, dass alle Symbole angezeigt werden. Mithilfe von Clustering können Sie die Dichte aller Daten erkennen und gleichzeitig eine angenehme, übersichtliche Benutzeroberfläche erzeugen. In diesem Beispiel werden benutzerdefinierte Symbole verwendet, um Cluster und einzelne Datenpunkte darzustellen.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Gruppierte Symbolebene" src="//codepen.io/azuremaps/embed/Wmqpzz/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Weitere Informationen finden Sie unter dem Pen <a href='https://codepen.io/azuremaps/pen/Wmqpzz/'>Clustered Symbol layer (Gruppierte Symbolebene)</a> von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="clustering-and-the-heat-maps-layer"></a>Clustering und Wärmebildebene

Wärmebilder sind eine hervorragende Möglichkeit, die Dichte der Daten auf der Karte anzuzeigen. Diese Visualisierung kann bereits eine große Anzahl von Datenpunkten verarbeiten, aber sie kann noch mehr Daten verarbeiten, wenn die Datenpunkte gruppiert werden und die Clustergröße als Gewichtung des Wärmebilds verwendet wird. Legen Sie dazu die Option `weight` der Wärmebildebene auf `['get', 'point_count']` fest. Bei einem kleinen Clusterradius sieht das Wärmebild nahezu identisch zu einem Wärmebild aus, das die nicht gruppierten Datenpunkte verwendet. Es wird aber viel besser funktionieren. Je kleiner der Clusterradius, desto genauer ist das Wärmebild, jedoch mit geringerem Leistungsvorteil.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Wärmebild mit gewichteten Clustern" src="//codepen.io/azuremaps/embed/VRJrgO/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Weitere Informationen finden Sie unter dem Pen <a href='https://codepen.io/azuremaps/pen/VRJrgO/'>Cluster weighted Heat Map (Wärmebild mit gewichteten Clustern)</a> von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="mouse-events-on-clustered-data-points"></a>Mausereignisse für gruppierte Datenpunkte

Wenn Mausereignisse auf einer Ebene auftreten, die gruppierte Datenpunkte enthalten, wird der gruppierte Datenpunkt als GeoJSON-Punktfeatureobjekt an das Ereignis zurückgegeben. Dieses Punktfeature weist die folgenden Eigenschaften auf:

| Eigenschaftenname | Type | BESCHREIBUNG |
|---------------|------|-------------|
| cluster | boolean | Gibt an, ob das Feature einen Cluster darstellt. |
| cluster_id | Zeichenfolge | Eine eindeutige ID für den Cluster, die mit den DataSource-Methoden `getClusterExpansionZoom`, `getClusterChildren` und `getClusterLeaves` verwendet werden kann. |
| point_count | number | Die Anzahl der Punkte, die der Cluster enthält. |
| point_count_abbreviated | Zeichenfolge | Eine Zeichenfolge, die den `point_count`-Wert kürzt, falls zu lang. (Beispiel: 4.000 wird zu 4K) |

Dieses Beispiel nimmt eine Blasenebene, die Clusterpunkte rendert, und fügt ein Click-Ereignis hinzu, das beim Auslösen den nächsten Zoomfaktor berechnet und die Karte entsprechend zoomt, bei dem der Cluster mithilfe der `getClusterExpansionZoom`-Methode der Klasse `DataSource` und der `cluster_id`-Eigenschaft des angeklickten gruppierten Datenpunkts unterteilt wird. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Cluster getClusterExpansionZoom" src="//codepen.io/azuremaps/embed/moZWeV/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Weitere Informationen finden Sie unter dem Pen <a href='https://codepen.io/azuremaps/pen/moZWeV/'>Cluster getClusterExpansionZoom</a> von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="display-cluster-area"></a>Anzeigen des Clusterbereichs 

Die Punktdaten, die ein Cluster darstellt, sind über einen Bereich verteilt. Wenn Sie den Mauszeiger über einen Cluster bewegen, werden in diesem Beispiel die darin enthaltenen einzelnen Datenpunkte (Blätter), zur Berechnung einer konvexen Hülle verwendet und auf der Karte angezeigt, um das Gebiet zu veranschaulichen. Alle in einem Cluster enthaltenen Punkte können mit der Methode `getClusterLeaves` aus der Datenquelle abgerufen werden. Eine konvexe Hülle ist ein Polygon, das einen Satz von Punkten wie ein elastisches Band umhüllt und mit der `atlas.math.getConvexHull`-Methode berechnet werden kann.

<br/>

 <iframe height="500" style="width: 100%;" scrolling="no" title="Konvexe Hülle des Clusterbereichs" src="//codepen.io/azuremaps/embed/QoXqWJ/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Weitere Informationen finden Sie unter dem Pen <a href='https://codepen.io/azuremaps/pen/QoXqWJ/'>Cluster area convex hull (Konvexe Hülle des Clusterbereichs)</a> von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr zu den in diesem Artikel verwendeten Klassen und Methoden:

> [!div class="nextstepaction"]
> [DataSource-Klasse](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [DataSourceOptions-Objekt](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.datasourceoptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [atlas.math namespace](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.math?view=azure-iot-typescript-latest)

Sehen Sie sich Codebeispiele an, die zeigen, wie Sie Ihrer App Funktionen hinzufügen:

> [!div class="nextstepaction"]
> [Hinzufügen einer Blasenebene](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Hinzufügen einer Symbolebene](map-add-pin.md)

> [!div class="nextstepaction"]
> [Hinzufügen einer Wärmebildebene](map-add-heat-map-layer.md)
