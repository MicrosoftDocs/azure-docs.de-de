---
title: Zentrale E/A-Vorgänge | Microsoft Azure Maps
description: Erfahren Sie, wie Sie XML-Daten und Daten mit Trennzeichen mithilfe von Kernbibliotheken im Modul Spatial IO effizient lesen und schreiben können.
author: anastasia-ms
ms.author: v-stharr
ms.date: 03/03/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-js
ms.openlocfilehash: 08966549d2c4d2bf701ee0dbfc5ed24f202396fc
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92891375"
---
# <a name="core-io-operations"></a>Zentrale E/A-Vorgänge

Neben der Bereitstellung von Tools zum Lesen von Dateien mit räumlichen Daten stellt das Modul Spatial IO auch die zugrunde liegenden Kernbibliotheken bereit, um XML-Daten und Daten mit Trennzeichen schnell und effizient zu lesen und zu schreiben.

Der Namespace `atlas.io.core` enthält zwei Klassen auf niedriger Ebene, die CSV- und XML-Daten schnell lesen und schreiben können. Diese Basisklassen unterstützen die Reader und Writer räumlicher Daten im Modul Spatial IO. Sie können sie nutzen, um CSV- oder XML-Dateien mit zusätzlicher Unterstützung für Lese- und Schreibvorgänge zu versehen.
 
## <a name="read-delimited-files"></a>Lesen von Dateien mit Trennzeichen

Die `atlas.io.core.CsvReader`-Klasse liest Zeichenfolgen, die Datasets mit Trennzeichen enthalten. Diese Klasse stellt zwei Methoden zum Lesen von Daten bereit:

- Die Funktion `read` liest das vollständige Dataset und gibt ein zweidimensionales Array von Zeichenfolgen zurück, das alle Zellen im Dataset mit Trennzeichen darstellt.
- Die Funktion `getNextRow` liest jede Textzeile in einem Dataset mit Trennzeichen und gibt ein Array von Zeichenfolgen zurück, das alle Zellen in der betreffenden Zeile des Datasets darstellt. Der Benutzer kann die Zeile verarbeiten und nicht benötigten Speicher aus dieser Zeile entfernen, bevor er die nächste Zeile verarbeitet. Dadurch ist die Funktion speichereffizienter.

Standardmäßig verwendet der Reader das Kommazeichen als Trennzeichen. Das Trennzeichen kann jedoch in ein beliebiges Einzelzeichen geändert oder auf `'auto'` festgelegt werden. Bei Festlegung auf `'auto'` analysiert der Reader die erste Zeile des Texts in der Zeichenfolge. Anschließend wählt er in der nachstehenden Tabelle das am häufigsten verwendete Zeichen aus, um es als Trennzeichen zu verwenden.

| Trennzeichen | Zeichen |
| :-- | :-- |
| Komma | `,` |
| Registerkarte | `\t` |
| Pipe | `|` |

Dieser Reader unterstützt auch Textqualifizierer, die zur Behandlung von Zellen verwendet werden, die das Trennzeichen enthalten. Das Anführungszeichen (`'"'`) ist der Standardtextqualifizierer. Es kann jedoch in ein beliebiges Einzelzeichen geändert werden.

## <a name="write-delimited-files"></a>Schreiben von Dateien mit Trennzeichen

`atlas.io.core.CsvWriter` schreibt ein Array von Objekten als Zeichenfolge mit Trennzeichen. Jedes Einzelzeichen kann als Trennzeichen oder Textqualifizierer verwendet werden. Das Standardtrennzeichen ist das Komma (`','`), der Standardtextqualifizierer das Anführungszeichen (`'"'`).

Um diese Klasse zu verwenden, führen Sie die folgenden Schritte aus:

- Erstellen Sie eine Instanz der Klasse, und legen Sie optional ein benutzerdefiniertes Trennzeichen oder einen Textqualifizierer fest.
- Schreiben Sie mit der Funktion `write` oder `writeRow` Daten in die Klasse. Übergeben Sie für die Funktion `write` ein zweidimensionales Array von Objekten, das mehrere Zeilen und Zellen darstellt. Um die Funktion `writeRow` zu verwenden, übergeben Sie ein Array von Objekten, das eine Datenzeile mit mehreren Spalten darstellt.
- Rufen Sie die Funktion `toString` auf, um die Zeichenfolge mit Trennzeichen abzurufen. 
- Rufen Sie optional die `clear`-Methode auf, um den Writer wiederverwendbar zu machen und seine Ressourcenzuordnung zu reduzieren. Sie können auch die `delete`-Methode aufrufen, um die Instanz des Writers zu entsorgen.

> [!Note]
> Die Anzahl der geschriebenen Spalten ist auf die Anzahl der Zellen in der ersten Zeile der an den Writer übergebenen Daten beschränkt.

## <a name="read-xml-files"></a>Lesen von XML-Dateien

Die Klasse `atlas.io.core.SimpleXmlReader` ist beim Analysieren von XML-Dateien schneller als die Klasse `DOMParser`. Die Klasse `atlas.io.core.SimpleXmlReader` erfordert jedoch, dass XML-Dateien einwandfrei formatiert sind. XML-Dateien, die nicht einwandfrei formatiert sind, weil z. B. schließende Tags fehlen, führen voraussichtlich zu einem Fehler.

Der folgende Code veranschaulicht, wie die Klasse `SimpleXmlReader` verwendet wird, um eine XML-Zeichenfolge in ein JSON-Objekt zu analysieren und sie in ein gewünschtes Format zu serialisieren.

```javascript
//Create an instance of the SimpleXmlReader and parse an XML string into a JSON object.
var xmlDoc = new atlas.io.core.SimpleXmlReader().parse(xmlStringToParse);

//Verify that the root XML tag name of the document is the file type your code is designed to parse.
if (xmlDoc && xmlDoc.root && xmlDoc.root.tagName && xmlDoc.root.tagName === '<Your desired root XML tag name>') {

    var node = xmlDoc.root;

    //Loop through the child node tree to navigate through the parsed XML object.
    for (var i = 0, len = node.childNodes.length; i < len; i++) {
        childNode = node.childNodes[i];

        switch (childNode.tagName) {
            //Look for tag names, parse and serialized as desired.
        }
    }
}
```

## <a name="write-xml-files"></a>Schreiben von XML-Dateien

Die Klasse `atlas.io.core.SimpleXmlWriter` schreibt einwandfrei formatierten XML-Code auf speichereffiziente Weise.

Der folgende Code zeigt, wie die Klasse `SimpleXmlWriter` verwendet wird, um eine einwandfrei formatierte XML-Zeichenfolge zu generieren.

```javascript
//Create an instance of the SimpleXmlWriter class.
var writer = new atlas.io.core.SimpleXmlWriter();

//Start writing the document. All write functions return a reference to the writer, making it easy to chain the function calls to reduce the code size.
writer.writeStartDocument(true)
    //Specify the root XML tag name, in this case 'root'
    .writeStartElement('root', {
        //Attributes to add to the root XML tag.
        'version': '1.0',
        'xmlns': 'http://www.example.com',
         //Example of a namespace.
        'xmlns:abc': 'http://www.example.com/abc'
    });

//Start writing an element that has the namespace abc and add other XML elements as children.
writer.writeStartElement('abc:parent');

//Write a simple XML element like <title>Azure Maps is awesome!</title>
writer.writeElement('title', 'Azure Maps is awesome!');

//Close the element that we have been writing children to.
writer.writeEndElement();

//Finish writing the document by closing the root tag and the document.
writer.writeEndElement().writeEndDocument();

//Get the generated XML string from the writer.
var xmlString = writer.toString();
```

Die aus dem obigen Code generierte XML-Datei sieht wie folgt aus.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<root version="1.0" xmlns="http://www.example.com" xmlns:abc="http://www.example.com/abc">
    <abc:parent>
        <title>Azure Maps is awesome!</title>
    </abc:parent>
</root>
```

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr zu den in diesem Artikel verwendeten Klassen und Methoden:

[CsvReader](/javascript/api/azure-maps-spatial-io/atlas.io.core.csvreader)

[CsvWriter](/javascript/api/azure-maps-spatial-io/atlas.io.core.csvwriter)

[SimpleXmlReader](/javascript/api/azure-maps-spatial-io/atlas.io.core.simplexmlreader)

[SimpleXmlWriter](/javascript/api/azure-maps-spatial-io/atlas.io.core.simplexmlwriter)

In den folgenden Artikeln finden Sie weitere Codebeispiele, die Sie Ihren Karten hinzufügen können:

[Details zu unterstützten Datenformaten](spatial-io-supported-data-format-details.md)