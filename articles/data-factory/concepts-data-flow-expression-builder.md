---
title: Azure Data Factory Mapping Data Flow – Ausdrucks-Generator
description: Der Ausdrucks-Generator für Azure Data Factory Mapping Data Flow
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: df9cfb0c0e36f54c8b1fbee4def552c78e9d42c1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61269164"
---
# <a name="mapping-data-flow-expression-builder"></a>Mapping Data Flow: Ausdrucks-Generator

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

In Azure Data Factory Mapping Data Flow gibt es Ausdrucksfelder, in die Sie Ausdrücke für die Datentransformation eingeben können. In diesen Feldern können Sie Spalten, Felder, Variablen, Parameter und Funktionen Ihres Datenflusses verwenden. Zum Erstellen des Ausdrucks verwenden Sie den Ausdrucks-Generator, der gestartet wird, wenn Sie in der Transformation auf das Ausdruckstextfeld klicken. Manchmal werden auch Optionen für „Berechnete Spalte“ angezeigt, wenn Sie Spalten für die Transformation auswählen. Wenn Sie darauf klicken, wird der Ausdrucks-Generator ebenfalls gestartet.

![Ausdrucks-Generator](media/data-flow/expression.png "Ausdrucks-Generator")

Der Ausdrucks-Generator verwendet standardmäßig die Text-Editor-Option. Die Funktion „AutoVervollständigen“ liest aus dem gesamten Azure Data Factory-Datenfluss-Objektmodell mit Syntaxprüfung und -hervorhebungen.

![Automatische Vervollständigung des Ausdrucks-Generators](media/data-flow/expb1.png "Automatische Vervollständigung des Ausdrucks-Generators")

## <a name="currently-working-on-field"></a>Das Feld, an dem Sie aktuell arbeiten

![Ausdrucks-Generator](media/data-flow/exp3.png "Feld, an dem Sie aktuell arbeiten")

Oben links auf der Benutzeroberfläche des Ausdrucks-Generators wird ein Feld („Currently Working On“) mit dem Namen des Felds angezeigt, an dem Sie aktuell arbeiten. Der Ausdruck, den Sie über die Benutzeroberfläche erstellen, wird nur auf das Feld angewendet, an dem Sie aktuell arbeiten. Wenn Sie ein anderes Feld transformieren möchten, speichern Sie Ihre aktuelle Arbeit, und verwenden Sie dieses Dropdownmenü, um ein anderes Feld auszuwählen und einen Ausdruck für die anderen Felder zu erstellen.

## <a name="data-preview-in-debug-mode"></a>Datenvorschau im Debugmodus

![Ausdrucks-Generator](media/data-flow/exp4b.png "Ausdrucksdatenvorschau")

Wenn Sie an Ihren Ausdrücken arbeiten, können Sie den Debugmodus optional über die Entwurfsoberfläche von Azure Data Factory-Datenfluss aktivieren, um eine Livevorschau Ihrer Datenergebnisse aus dem von Ihnen erstellten Ausdruck zu erhalten. Für Ihre Ausdrücke ist Livedebugging in Echtzeit aktiviert.

![Debugmodus](media/data-flow/debugbutton.png "Schaltfläche „Debuggen“")


![Ausdrucks-Generator](media/data-flow/exp5.png "Ausdrucksdatenvorschau")

## <a name="comments"></a>Kommentare

Fügen Sie Ihren Ausdrücken unter Verwendung der einzeiligen und mehrzeiligen Kommentarsyntax Kommentare hinzu:

![Kommentare](media/data-flow/comments.png "Kommentare")

## <a name="regular-expressions"></a>Reguläre Ausdrücke

Die Ausdruckssprache von Azure Data Factory-Datenfluss ([die vollständige Referenzdokumentation finden Sie hier)](https://aka.ms/dataflowexpressions) ermöglicht das Verwenden von Funktionen, die eine Syntax für reguläre Ausdrücke enthalten. Wenn Sie Funktionen mit regulären Ausdrücken verwenden, versucht der Ausdrucks-Generator, den umgekehrten Schrägstrich (\\) als Escapezeichensequenz zu interpretieren. Wenn Sie in Ihrem regulären Ausdruck umgekehrte Schrägstriche verwenden, schließen Sie entweder den gesamten regulären Ausdruck (RegEx) in Ticks (\`) ein, oder verwenden Sie einen doppelten umgekehrten Schrägstrich.

Beispiel für die Verwendung von Ticks

```
regex_replace('100 and 200', `(\d+)`, 'digits')
```

Beispiel für die Verwendung eines doppelten umgekehrten Schrägstrichs

```
regex_replace('100 and 200', '(\\d+)', 'digits')
```

## <a name="addressing-array-indexes"></a>Angeben von Arrayindizes

Verwenden Sie bei Ausdrucksfunktionen, die Arrays zurückgeben, eckige Klammern [], um bestimmte Indizes innerhalb dieses Rückgabearrayobjekts anzugeben. Das Array ist einzelbasiert.

![Ausdrucks-Generator-Array](media/data-flow/expb2.png "Ausdrucksdatenvorschau")

## <a name="handling-names-with-special-characters"></a>Verarbeiten von Namen mit Sonderzeichen

Wenn Sie Spaltennamen besitzen, die Sonderzeichen oder Leerzeichen enthalten, setzen Sie die Namen in geschweiften Klammern.
* ```{[dbo].this_is my complex name$$$}```

## <a name="next-steps"></a>Nächste Schritte

[Mit dem Erstellen von Datentransformationsausdrücken beginnen](data-flow-expression-functions.md)
