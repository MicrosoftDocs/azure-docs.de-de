---
title: Suchtransformation in einem Zuordnungsdatenfluss
description: Verweisen Sie mithilfe der Suchtransformation in einem Zuordnungsdatenfluss auf Daten aus einer anderen Quelle.
author: kromerm
ms.reviewer: daperlov
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/30/2020
ms.openlocfilehash: 7ed1d9db09357b0702188c01a802600ff6350aff
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/01/2020
ms.locfileid: "93147265"
---
# <a name="lookup-transformation-in-mapping-data-flow"></a>Suchtransformation in einem Zuordnungsdatenfluss

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Verwenden Sie die Suchtransformation, um auf Daten aus einer anderen Quelle in einem Datenfluss zu verweisen. Die Suchtransformation fügt Spalten aus übereinstimmenden Daten an Ihre Quelldaten an.

Eine Suchtransformation ist mit einer linken äußeren Verknüpfung vergleichbar. Alle Zeilen aus dem primären Datenstrom sind im Ausgabedatenstrom mit zusätzlichen Spalten aus dem Suchdatenstrom enthalten.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4xsVT]

## <a name="configuration"></a>Konfiguration

![Screenshot der Registerkarte „Sucheinstellungen“ mit den im folgenden Text beschriebenen Bezeichnungen](media/data-flow/lookup1.png "Nachschlagen")

**Primärer Datenstrom:** Der eingehende Datenstrom. Dieser Datenstrom entspricht der linken Seite einer Verknüpfung.

**Suchdatenstrom:** Die Daten, die an den primären Datenstrom angefügt werden. Welche Daten hinzugefügt werden, wird durch die Suchbedingungen festgelegt. Dieser Datenstrom entspricht der rechten Seite einer Verknüpfung.

**Übereinstimmung mit mehreren Zeilen:** Bei Aktivierung dieser Option werden für eine Zeile mit mehreren Übereinstimmungen im primären Datenstrom mehrere Zeilen zurückgegeben. Anderenfalls wird basierend auf der Bedingung „Übereinstimmung mit“ nur eine einzelne Zeile zurückgegeben.

**Übereinstimmung mit:** Nur sichtbar, wenn „Übereinstimmung mit mehreren Zeilen“ nicht aktiviert ist. Wählen Sie aus, ob eine Übereinstimmung für eine beliebige Zeile, die erste Übereinstimmung oder die letzte Übereinstimmung ermittelt werden soll. Da die Ausführung bei Auswahl einer beliebigen Zeile am schnellsten ist, wird diese Einstellung empfohlen. Wenn die erste Zeile oder die letzte Zeile ausgewählt wird, müssen Sortierbedingungen angegeben werden.

**Suchbedingungen:** Wählen Sie aus, für welche Spalten eine Übereinstimmung ermittelt werden soll. Wenn die Gleichheitsbedingung erfüllt ist, werden die Zeilen als Übereinstimmung betrachtet. Zeigen Sie mit dem Mauszeiger auf „Berechnete Spalte“, und wählen Sie diese Option aus, um mithilfe der [Ausdruckssprache für Datenflüsse](data-flow-expression-functions.md) einen Wert zu extrahieren.

Die Suchtransformation unterstützt ausschließlich Gleichheitsübereinstimmungen. Um den Suchausdruck so anzupassen, dass er andere Operatoren (z. B. „größer als“) umfasst, sollten Sie einen [Cross Join in der Join-Transformation](data-flow-join.md#custom-cross-join) verwenden. Durch einen Cross Join werden mögliche kartesische Produktfehler bei der Ausführung vermieden.

In den Ausgabedaten sind alle Spalten beider Datenströme enthalten. Um doppelte oder unerwünschte Spalten zu löschen, fügen Sie nach Ihrer Suchtransformation eine [Auswahltransformation](data-flow-select.md) hinzu. Spalten können auch in einer Senkentransformation gelöscht oder umbenannt werden.

### <a name="non-equi-joins"></a>Nicht-Gleichheitsverknüpfungen

Wenn Sie in Ihren Joinbedingungen einen bedingten Operator wie „ungleich“ „(!=)“ oder „größer als“ „(>)“ verwenden möchten, ändern Sie die Dropdownliste des Operators zwischen den beiden Spalten. Bei Nicht-Gleichheitsverknüpfungen muss mindestens einer der beiden Datenströme mithilfe der Übertragungsoption **Fixed** (Fest) auf der Registerkarte **Optimieren** übertragen werden.

![Nichtgleichheitsuche](media/data-flow/non-equi-lookup.png "Nichtgleichheitsuche")

## <a name="analyzing-matched-rows"></a>Analysieren übereinstimmender Zeilen

Nach der Suchtransformation können Sie mithilfe der Funktion `isMatch()` feststellen, ob bei der Suche Übereinstimmungen für einzelne Zeilen ermittelt wurden.

![Suche: Muster](media/data-flow/lookup111.png "Suche: Muster")

Ein Beispiel für dieses Muster ist die Verwendung der Transformation für bedingtes Teilen für die Funktion `isMatch()`. Im obigen Beispiel durchlaufen übereinstimmende Zeilen den obersten Stream und nicht übereinstimmende Zeilen den Stream ```NoMatch```.

## <a name="testing-lookup-conditions"></a>Testen von Suchbedingungen

Verwenden Sie einen kleinen Satz bekannter Daten, wenn Sie die Suchtransformation mit Datenvorschau im Debugmodus testen. Bei der Stichprobenentnahme aus einem umfangreichen Dataset können Sie nicht vorhersagen, welche Zeilen und Schlüssel zu Testzwecken gelesen werden. Das Ergebnis ist nicht deterministisch. Das bedeutet, dass für Ihre Verknüpfungsbedingungen möglicherweise keine Übereinstimmungen zurückgegeben werden.

## <a name="broadcast-optimization"></a>Broadcastoptimierung

![Broadcastjoin](media/data-flow/broadcast.png "Broadcastjoin")

Wenn bei Join, Lookup- und Exists-Transformationen der Arbeitsspeicher des Workerknotens groß genug für einen oder beide Datenströme ist, können Sie die Leistung optimieren, indem Sie die **Übertragung** aktivieren. Standardmäßig entscheidet die Spark-Engine automatisch, ob eine Seite übertragen werden soll oder nicht. Klicken Sie auf **Fest** , um die zu übertragende Seite manuell auszuwählen.

Es wird nicht empfohlen, die Übertragung über die Option **Aus** zu deaktivieren, es sei denn, für Ihre Joins treten Timeoutfehler auf.

## <a name="cached-lookup"></a>Zwischengespeicherte Suche

Wenn Sie mehrere kleinere Suchvorgänge für dieselbe Quelle ausführen, sind eine Cachesenke und zwischengespeicherte Suche möglicherweise besser geeignet als eine Suchtransformation. Gängige Beispiele, in denen eine Cachesenke besser geeignet sein könnte, ist das Suchen eines Höchstwerts in einem Datenspeicher und das Abgleichen von Fehlercodes mit einer Fehlermeldungsdatenbank. Weitere Informationen finden Sie unter [Cachesenken](data-flow-sink.md#cache-sink) und [zwischengespeicherten Suchen](concepts-data-flow-expression-builder.md#cached-lookup).

## <a name="data-flow-script"></a>Datenflussskript

### <a name="syntax"></a>Syntax

```
<leftStream>, <rightStream>
    lookup(
        <lookupConditionExpression>,
        multiple: { true | false },
        pickup: { 'first' | 'last' | 'any' },  ## Only required if false is selected for multiple
        { desc | asc }( <sortColumn>, { true | false }), ## Only required if 'first' or 'last' is selected. true/false determines whether to put nulls first
        broadcast: { 'auto' | 'left' | 'right' | 'both' | 'off' }
    ) ~> <lookupTransformationName>
```
### <a name="example"></a>Beispiel

![Screenshot der Registerkarte „Sucheinstellungen“ für den folgenden Code](media/data-flow/lookup-dsl-example.png "Nachschlagen")

Der nachfolgende Codeausschnitt zeigt das Datenflussskript für die obige Suchkonfiguration.

```
SQLProducts, DimProd lookup(ProductID == ProductKey,
    multiple: false,
    pickup: 'first',
    asc(ProductKey, true),
    broadcast: 'auto')~> LookupKeys
```
## 
Nächste Schritte

* Für die [Join](data-flow-join.md)- und [Exists](data-flow-exists.md)-Transformation können mehrere Datenströme als Eingabe verwendet werden.
* Verwenden Sie eine [Transformation für bedingtes Teilen](data-flow-conditional-split.md) mit ```isMatch()```, um Zeilen nach übereinstimmenden und nicht übereinstimmenden Werten aufzuteilen.
