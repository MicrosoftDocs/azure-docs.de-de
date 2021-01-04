---
title: Hinzufügen eines Popups zu einem Punkt auf einer Karte | Microsoft Azure Maps
description: In diesem Artikel erhalten Sie Informationen zu Popupelementen, Popupvorlagen und Popupereignissen in Azure Maps. Sie erfahren außerdem, wie Sie ein Popupelement an einem bestimmten Punkt auf der Karte hinzufügen und wie Sie Popupelemente wiederverwenden und anpassen.
author: anastasia-ms
ms.author: v-stharr
ms.date: 02/27/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 903574a8c8696f10d9903a685bab8d12b3e6395f
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92891035"
---
# <a name="add-a-popup-to-the-map"></a>Hinzufügen eines Popupfensters zu der Karte

In diesem Artikel wird gezeigt, wie Sie ein Popupfenster einem Punkt auf einer Karte hinzufügen.

## <a name="understand-the-code"></a>Grundlegendes zum Code

Der folgende Code fügt der Karte über eine Symbolebene ein Punktfeature mit den Eigenschaften `name` und `description` hinzu. Es wird eine Instanz der [Popup-Klasse](/javascript/api/azure-maps-control/atlas.popup) hinzugefügt, aber nicht angezeigt. Mausereignisse werden der Symbolebene hinzugefügt, um das Öffnen und Schließen des Popups auszulösen. Wenn Sie den Mauszeiger über das Markersymbol bewegen, wird die `position`-Eigenschaft des Popups mit der Position des Markers aktualisiert und die `content`-Option wird mit HTML aktualisiert, das die `name`- und `description`-Eigenschaften des zu bewegenden Punktfeatures umschließt. Das Popup wird dann mithilfe der `open`-Funktion auf der Karte angezeigt.

```javascript
//Define an HTML template for a custom popup content laypout.
var popupTemplate = '<div class="customInfobox"><div class="name">{name}</div>{description}</div>';

//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

dataSource.add(new atlas.data.Feature(new atlas.data.Point([-122.1333, 47.63]), {
  name: 'Microsoft Building 41', 
  description: '15571 NE 31st St, Redmond, WA 98052'
}));

//Create a layer to render point data.
var symbolLayer = new atlas.layer.SymbolLayer(dataSource);

//Add the polygon and line the symbol layer to the map.
map.layers.add(symbolLayer);

//Create a popup but leave it closed so we can update it and display it later.
popup = new atlas.Popup({
  pixelOffset: [0, -18],
  closeButton: false
});

//Add a hover event to the symbol layer.
map.events.add('mouseover', symbolLayer, function (e) {
  //Make sure that the point exists.
  if (e.shapes && e.shapes.length > 0) {
    var content, coordinate;
    var properties = e.shapes[0].getProperties();
    content = popupTemplate.replace(/{name}/g, properties.name).replace(/{description}/g, properties.description);
    coordinate = e.shapes[0].getCoordinates();

    popup.setOptions({
      //Update the content of the popup.
      content: content,

      //Update the popup's position with the symbol's coordinate.
      position: coordinate

    });
    //Open the popup.
    popup.open(map);
  }
});

map.events.add('mouseleave', symbolLayer, function (){
  popup.close();
});
```

Nachfolgend finden Sie das vollständige ausführbare Codebeispiel für die oben erläuterte Funktionalität.

<br/>

<iframe height='500' scrolling='no' title='Hinzufügen eines Popupfensters mit Azure Maps' src='//codepen.io/azuremaps/embed/MPRPvz/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Sehen Sie sich bei <a href='https://codepen.io'>CodePen</a> die Informationen unter <a href='https://codepen.io/azuremaps/pen/MPRPvz/'>Add a pop up using Azure Maps</a> (Hinzufügen eines Popupfensters mit Azure Maps) von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) an.
</iframe>

## <a name="reusing-a-popup-with-multiple-points"></a>Wiederverwenden eines Popups für mehrere Punkte

Es gibt Fälle, in denen die beste Vorgehensweise darin besteht, ein Popup zu erstellen und wiederzuverwenden. Es kann beispielsweise sein, dass Sie eine große Anzahl von Punkten haben und nur ein Popup auf einmal anzeigen möchten. Durch Wiederverwenden des Popups wird die Anzahl der von der Anwendung erstellten DOM-Elemente erheblich reduziert, wodurch eine bessere Leistung erzielt werden kann. Im folgenden Beispiel werden drei Punktfeatures erstellt. Wenn Sie auf eines davon klicken, wird ein Popup mit dem Inhalt für dieses Punktfeature angezeigt.

<br/>

<iframe height='500' scrolling='no' title='Wiederverwenden eines Popups für mehrere Stecknadeln' src='//codepen.io/azuremaps/embed/rQbjvK/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Informationen hierzu finden Sie unter <a href='https://codepen.io/azuremaps/pen/rQbjvK/'>Reusing Popup with Multiple Pins</a> (Wiederverwenden eines Popups mit mehreren Stecknadeln) von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customizing-a-popup"></a>Anpassen eines Popups

Standardmäßig hat das Popup einen weißen Hintergrund, unten einen Zeigerpfeil und rechts oben die Schaltfläche „Schließen“. Das folgende Beispiel ändert die Hintergrundfarbe mit der Option `fillColor` des Popups in Schwarz. Die Schaltfläche „Schließen“ wird entfernt, indem die Option `CloseButton` auf „false“ gesetzt wird. Der HTML-Inhalt des Popups wird von den Rändern des Popups aus mit 10 Pixeln aufgefüllt. Der Text ist weiß, damit er auf dem schwarzen Hintergrund gut sichtbar ist.  

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Benutzerdefiniertes Popup" src="//codepen.io/azuremaps/embed/ymKgdg/?height=500&theme-id=0&default-tab=result" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Weitere Informationen finden Sie unter dem Pen <a href='https://codepen.io/azuremaps/pen/ymKgdg/'>Angepasste Popups</a> von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-popup-templates-to-the-map"></a>Hinzufügen von Popupvorlagen zur Karte

Popupvorlagen erleichtern das Erstellen datengesteuerter Layouts für Popups. In den folgenden Abschnitten wird gezeigt, wie mithilfe verschiedener Popupvorlagen formatierter Inhalt unter Verwendung von Eigenschaften von Features generiert wird.

> [!NOTE]
> Standardmäßig werden alle Inhalte, die mithilfe der Popupvorlage gerendert werden, aus Sicherheitsgründen in einer Sandbox innerhalb eines iframes ausgeführt. Es gibt jedoch Einschränkungen:
>
> - Die gesamte Funktionalität von Skripts, Formularen, der Zeigersperre und der oberen Navigationsleiste ist deaktiviert. Das Öffnen von angeklickten Links wird auf einer neuen Registerkarte erlaubt. 
> - Ältere Browser, die den `srcdoc`-Parameter in iframes nicht unterstützen, werden auf das Rendern einer kleinen Menge von Inhalten eingeschränkt.
> 
> Wenn Sie die in die Popups geladenen Daten als vertrauenswürdig einstufen und diesen in Popups geladenen Skripts möglicherweise den Zugriff auf Ihre Anwendung gestatten möchten, können Sie dies deaktivieren, indem Sie die Option `sandboxContent` der Popupvorlagen auf „false“ festlegen. 

### <a name="string-template"></a>String-Vorlage

Die String-Vorlage ersetzt Platzhalter durch Werte der Eigenschaften des Features. Den Eigenschaften des Features muss kein Wert des Typs „String“ zugewiesen werden. `value1` enthält z. B. eine ganze Zahl. Diese Werte werden dann an die Content-Eigenschaft von `popupTemplate` weitergegeben. 

Die Option `numberFormat` gibt das anzuzeigende Format der Zahl an. Wenn `numberFormat` nicht angegeben ist, verwendet der Code das Datumsformat in den Popupvorlagen. Die Option `numberFormat` formatiert Zahlen mithilfe der Funktion [Number.toLocaleString](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Number/toLocaleString). Um große Zahlen zu formatieren, empfiehlt es sich, die Option `numberFormat` mit Funktionen aus [NumberFormat.format](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/NumberFormat/format) zu verwenden. Im folgenden Codeausschnitt wird beispielsweise `maximumFractionDigits` verwendet, um die Anzahl der Nachkommastellen auf zwei zu begrenzen.

> [!Note]
> Es gibt nur eine Möglichkeit, wie die String-Vorlage Bilder rendern kann. Zunächst muss die String-Vorlage ein Imagetag enthalten. Der Wert, der an das Imagetag übergeben wird, muss eine URL zu einem Bild sein. Dann muss für die String-Vorlage in `HyperLinkFormatOptions` der Wert `isImage` auf TRUE festgelegt werden. Die Option `isImage` gibt an, dass der Link für ein Bild bestimmt ist. Der Link wird in ein Imagetag geladen. Wenn auf den Link geklickt wird, wird das Bild geöffnet.

```javascript
var templateOptions = {
  content: 'This template uses a string template with placeholders.<br/><br/> - Value 1 = {value1}<br/> - Value 2 = {value2/subValue}<br/> - Array value [2] = {arrayValue/2}',
  numberFormat: {
    maximumFractionDigits: 2
  }
};

var feature = new atlas.data.Feature(new atlas.data.Point([0, 0]), {
    title: 'Template 1 - String template',
    value1: 1.2345678,
    value2: {
        subValue: 'Pizza'
    },
    arrayValue: [3, 4, 5, 6]
});

var popup = new atlas.Popup({
  content: atlas.PopupTemplate.applyTemplate(feature.properties, templateOptions),
  position: feature.geometry.coordinates
});
```

### <a name="propertyinfo-template"></a>PropertyInfo-Vorlage

Die PropertyInfo-Vorlage zeigt die verfügbaren Eigenschaften des Features an. Die Option `label` gibt den Text an, der dem Benutzer angezeigt werden soll. Wenn `label` nicht angegeben ist, wird der Link angezeigt. Wenn der Link ein Bild ist, wird der dem Tag „alt“ zugewiesene Wert angezeigt. `dateFormat` gibt das Format des Datums an. Wenn das Datumsformat nicht angegeben ist, wird das Datum als Zeichenfolge gerendert. Die Option `hyperlinkFormat` rendert klickbare Links. Ähnlich kann die Option `email` verwendet werden, um klickbare E-Mail-Adressen zu rendern.

Bevor die PropertyInfo-Vorlage dem Endbenutzer die Eigenschaften anzeigt, prüft sie rekursiv, ob die Eigenschaften tatsächlich für dieses Feature definiert sind. Sie ignoriert auch die Anzeige von Stil- und Titeleigenschaften. Beispielsweise werden `color`, `size`, `anchor`, `strokeOpacity` und `visibility` nicht angezeigt. Sobald die Überprüfung des Eigenschaftspfads im Back-End erfolgt ist, zeigt die PropertyInfo-Vorlage den Inhalt in einem Tabellenformat an.

```javascript
var templateOptions = {
  content: [
    {
        propertyPath: 'createDate',
        label: 'Created Date'
    },
    {
        propertyPath: 'dateNumber',
        label: 'Formatted date from number',
        dateFormat: {
          weekday: 'long',
          year: 'numeric',
          month: 'long',
          day: 'numeric',
          timeZone: 'UTC',
          timeZoneName: 'short'
        }
    },
    {
        propertyPath: 'url',
        label: 'Code samples',
        hideLabel: true,
        hyperlinkFormat: {
          lable: 'Go to code samples!',
          target: '_blank'
        }
    },
    {
        propertyPath: 'email',
        label: 'Email us',
        hideLabel: true,
        hyperlinkFormat: {
          target: '_blank',
          scheme: 'mailto:'
        }
    }
  ]
};

var feature = new atlas.data.Feature(new atlas.data.Point([0, 0]), {
    title: 'Template 2 - PropertyInfo',
    createDate: new Date(),
    dateNumber: 1569880860542,
    url: 'https://aka.ms/AzureMapsSamples',
    email: 'info@microsoft.com'
}),

var popup = new atlas.Popup({
  content: atlas.PopupTemplate.applyTemplate(feature.properties, templateOptions),
  position: feature.geometry.coordinates
});
```

### <a name="multiple-content-templates"></a>Mehrere Inhaltsvorlagen

Ein Feature kann Inhalt auch unter Verwendung einer Kombination aus String-Vorlage und PropertyInfo-Vorlage anzeigen. In diesem Fall rendert die String-Vorlage Platzhalterwerte auf weißem Hintergrund.  Außerdem rendert die PropertyInfo-Vorlage ein Bild in voller Breite innerhalb einer Tabelle. Die Eigenschaften in diesem Beispiel ähneln den Eigenschaften, die wir in den vorherigen Beispielen erläutert haben.

```javascript
var templateOptions = {
  content: [
    'This template has two pieces of content; a string template with placeholders and a array of property info which renders a full width image.<br/><br/> - Value 1 = {value1}<br/> - Value 2 = {value2/subValue}<br/> - Array value [2] = {arrayValue/2}',
    [{
      propertyPath: 'imageLink',
      label: 'Image',
      hideImageLabel: true,
      hyperlinkFormat: {
        isImage: true
      }
    }]
  ],
  numberFormat: {
    maximumFractionDigits: 2
  }
};

var feature = new atlas.data.Feature(new atlas.data.Point([0, 0]), {
    title: 'Template 3 - Multiple content template',
    value1: 1.2345678,
    value2: {
    subValue: 'Pizza'
    },
    arrayValue: [3, 4, 5, 6],
    imageLink: 'https://azuremapscodesamples.azurewebsites.net/common/images/Pike_Market.jpg'
});

var popup = new atlas.Popup({
  content: atlas.PopupTemplate.applyTemplate(feature.properties, templateOptions),
  position: feature.geometry.coordinates
});
```

### <a name="points-without-a-defined-template"></a>Punkte ohne definierte Vorlage

Wenn die Popup-Vorlage nicht als String-Vorlage, als PropertyInfo-Vorlage oder als eine Kombination aus beiden definiert ist, verwendet sie die Standardeinstellungen. Wenn `title` und `description` die einzigen zugewiesenen Eigenschaften sind, zeigt die Popupvorlage einen weißen Hintergrund und rechts oben eine Schaltfläche zum Schließen. Auf kleinen und mittelgroßen Bildschirmen zeigt sie unten einen Pfeil. Die Standardeinstellungen werden innerhalb einer Tabelle für alle Eigenschaften außer `title` und `description` gezeigt. Auch wenn Sie auf die Standardeinstellungen zurückgreifen, kann die Popupvorlage weiterhin programmgesteuert bearbeitet werden. Wenn Benutzer beispielsweise die Linkerkennung ausschalten, gelten für andere Eigenschaften weiterhin die Standardeinstellungen.

Klicken Sie im CodePen auf die Punkte auf der Karte. Für jede der folgenden Popupvorlagen gibt es einen Punkt auf der Karte: String-Vorlage, PropertyInfo-Vorlage und Vorlage für mehrere Inhalte. Es gibt auch drei Punkte, die zeigen, wie Vorlagen mit den Standardeinstellungen gerendert werden.

<br/>

<iframe height='500' scrolling='no' title='PopupTemplates' src='//codepen.io/azuremaps/embed/dyovrzL/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Weitere Informationen finden Sie unter dem Pen <a href='https://codepen.io/azuremaps/pen/dyovrzL/'>PopupTemplates</a> von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="reuse-popup-template"></a>Wiederverwenden von Popupvorlagen

Popupvorlagen können ebenso wie Popups wiederverwendet werden. Dieser Ansatz ist nützlich, wenn Sie jeweils nur eine Popupvorlage für mehrere Punkte anzeigen möchten. Durch Wiederverwendung der Popupvorlage wird die Anzahl der von der Anwendung erstellten DOM-Elemente reduziert, was wiederum die Leistung Ihrer Anwendung verbessert. Im folgenden Beispiel wird die gleiche Popupvorlage für drei Punkte verwendet. Wenn Sie auf eines davon klicken, wird ein Popup mit dem Inhalt für dieses Punktfeature angezeigt.

<br/>

<iframe height='500' scrolling='no' title='ReusePopupTemplate' src='//codepen.io/azuremaps/embed/WNvjxGw/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Weitere Informationen finden Sie unter dem Pen <a href='https://codepen.io/azuremaps/pen/WNvjxGw/'>ReusePopupTemplate</a> von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="popup-events"></a>Popup-Ereignisse

Popups können geöffnet, geschlossen und gezogen werden. Die Popup-Klasse stellt Ereignisse für Hilfeentwickler bereit, die auf diese Ereignisse reagieren können. Im folgenden Beispiel wird hervorgehoben, welche Ereignisse ausgelöst werden, wenn der Benutzer das Popup öffnet, schließt oder zieht. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Popup-Ereignisse" src="//codepen.io/azuremaps/embed/BXrpvB/?height=500&theme-id=0&default-tab=result" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Weitere Informationen finden Sie unter dem Pen <a href='https://codepen.io/azuremaps/pen/BXrpvB/'>Popup-Ereignisse</a> von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr zu den in diesem Artikel verwendeten Klassen und Methoden:

> [!div class="nextstepaction"]
> [Popup](/javascript/api/azure-maps-control/atlas.popup)

> [!div class="nextstepaction"]
> [PopupOptions](/javascript/api/azure-maps-control/atlas.popupoptions)

> [!div class="nextstepaction"]
> [PopupTemplate](/javascript/api/azure-maps-control/atlas.popuptemplate)

Die folgenden interessanten Artikel enthalten vollständige Codebeispiele:

> [!div class="nextstepaction"]
> [Hinzufügen einer Symbolebene](./map-add-pin.md)

> [!div class="nextstepaction"]
> [Hinzufügen eines HTML-Markers](./map-add-custom-html.md)

> [!div class="nextstepaction"]
> [Hinzufügen einer Linienebene](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Hinzufügen einer Polygonebene](map-add-shape.md)