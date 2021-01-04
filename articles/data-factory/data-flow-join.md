---
title: Join-Transformation in einem Zuordnungsdatenfluss
description: Hier erfahren Sie, wie Sie Daten aus zwei Datenquellen mithilfe der Join-Transformation in einem Azure Data Factory-Zuordnungsdatenfluss miteinander kombinieren.
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/15/2020
ms.openlocfilehash: ac84ce17f53145ffd85ffa31b6633d8b4b184962
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93042657"
---
# <a name="join-transformation-in-mapping-data-flow"></a>Join-Transformation in einem Zuordnungsdatenfluss

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Mithilfe der Join-Transformation können Sie Daten aus zwei Quellen oder Streams in einem Zuordnungsdatenfluss miteinander kombinieren. Der Ausgabestream enthält alle Spalten aus beiden Quellen, die auf der Grundlage einer Verknüpfungsbedingung abgeglichen wurden. 

## <a name="join-types"></a>Join-Typen

Von Zuordnungsdatenflüssen werden aktuell fünf verschiedene Jointypen unterstützt:

### <a name="inner-join"></a>Innerer Join

Bei einem inneren Join werden nur Zeilen ausgegeben, die übereinstimmende Werte in beiden Tabellen enthalten.

### <a name="left-outer"></a>Linker äußerer Join

Bei einem linken äußeren Join werden alle Zeilen aus dem linken Stream sowie übereinstimmende Datensätze aus dem rechten Stream zurückgegeben. Ist für eine Zeile aus dem linken Stream keine Übereinstimmung vorhanden, werden die Ausgabespalten aus dem rechten Stream auf NULL festgelegt. Die Ausgabe umfasst die von einem inneren Join zurückgegebenen Zeilen sowie die nicht übereinstimmenden Zeilen aus dem linken Stream.

> [!NOTE]
> Bei der von Datenflüssen verwendeten Spark-Engine wird aufgrund etwaiger kartesischer Produkte in Ihren Joinbedingungen gelegentlich ein Fehler auftreten. Wenn dies der Fall ist, können Sie zu einem benutzerdefinierten Kreuzprodukt wechseln und die Joinbedingung manuell eingeben. Dies kann zu einer geringeren Leistung in Ihren Datenflüssen führen, da die Ausführungs-Engine u. U. alle Zeilen von beiden Seiten der Beziehung berechnen und dann Zeilen filtern muss.

### <a name="right-outer"></a>Rechter äußerer Join

Bei einem rechten äußeren Join werden alle Zeilen aus dem rechten Stream sowie übereinstimmende Datensätze aus dem linken Stream zurückgegeben. Ist für eine Zeile aus dem rechten Stream keine Übereinstimmung vorhanden, werden die Ausgabespalten aus dem linken Stream auf NULL festgelegt. Die Ausgabe umfasst die von einem inneren Join zurückgegebenen Zeilen sowie die nicht übereinstimmenden Zeilen aus dem rechten Stream.

### <a name="full-outer"></a>Vollständiger äußerer Join

Bei einem vollständigen äußeren Join werden alle Spalten und Zeilen von beiden Seiten zurückgegeben. Spalten ohne Übereinstimmung erhalten jeweils einen NULL-Wert.

### <a name="custom-cross-join"></a>Benutzerdefiniertes Kreuzprodukt

Bei einem Kreuzprodukt wird das Kreuzprodukt der beiden Streams auf der Grundlage einer Bedingung ausgegeben. Geben Sie bei Verwendung einer Ungleichheitsbedingung einen benutzerdefinierten Ausdruck als Kreuzproduktbedingung an. Der Ausgabestream umfasst alle Zeilen, die der Verknüpfungsbedingung entsprechen.

Dieser Jointyp kann für Nicht-Gleichheitsverknüpfungen und ```OR```-Bedingungen verwendet werden.

Wenn Sie explizit ein vollständiges kartesisches Produkt erzeugen möchten, verwenden Sie vor dem Join die Transformation für abgeleitete Spalten in beiden unabhängigen Datenströmen, um einen synthetischen Schlüssel für den Vergleich zu erstellen. Erstellen Sie z. B. mit der Transformation für abgeleitete Spalten eine neue Spalte in jedem Datenstrom mit dem Namen ```SyntheticKey``` und legen Sie ihn auf ```1``` fest. Verwenden Sie dann ```a.SyntheticKey == b.SyntheticKey``` als benutzerdefinierten Joinausdruck.

> [!NOTE]
> Achten Sie darauf, dass Sie mindestens eine Spalte von jeder Seite der linken und rechten Beziehung in ein benutzerdefiniertes Kreuzprodukt einbeziehen. Die Ausführung von Kreuzprodukten mit statischen Werten anstelle von Spalten von jeder Seite führt zu vollständigen Überprüfungen des gesamten Datasets, sodass der Datenfluss mit geringer Leistung ausgeführt wird.

## <a name="configuration"></a>Konfiguration

1. Wählen Sie in der Dropdownliste **Rechter Stream** den Datenstrom für die Verknüpfung aus.
1. Wählen Sie unter **Jointyp** den gewünschten Jointyp aus.
1. Wählen Sie die Schlüsselspalten aus, auf denen der Abgleich für Ihre Verknüpfungsbedingung basieren soll. Standardmäßig sucht der Datenfluss nach Übereinstimmung mit einer Spalte in jedem Datenstrom. Wenn der Vergleich auf einem berechneten Wert basieren soll, zeigen Sie mit dem Mauszeiger auf die Dropdownliste für die Spalte, und wählen Sie **Berechnete Spalte** aus.

![Join-Transformation](media/data-flow/join.png "Join")

### <a name="non-equi-joins"></a>Nicht-Gleichheitsverknüpfungen

Wenn Sie in Ihren Joinbedingungen einen bedingten Operator wie „ungleich“ „(!=)“ oder „größer als“ „(>)“ verwenden möchten, ändern Sie die Dropdownliste des Operators zwischen den beiden Spalten. Bei Nicht-Gleichheitsverknüpfungen muss mindestens einer der beiden Datenströme mithilfe der Übertragungsoption **Fixed** (Fest) auf der Registerkarte **Optimieren** übertragen werden.

![Nicht-Gleichheitsverknüpfung](media/data-flow/non-equi-join.png "Nicht-Gleichheitsverknüpfung")

## <a name="optimizing-join-performance"></a>Optimieren der Leistung beim Verknüpfen

Im Gegensatz zu „Merge Join“ in Tools wie SSIS ist die Join-Transformation kein obligatorischer Vorgang vom Typ „Merge Join“. Die Join-Schlüssel müssen nicht sortiert werden. Der Join-Vorgang erfolgt basierend auf dem optimalen Join-Vorgang in Spark (Broadcast- oder Map-Side-Join).

![Optimieren der Join-Transformation](media/data-flow/joinoptimize.png "Join-Optimierung")

Wenn bei Joins, Suchvorgängen und Exists-Transformationen der Arbeitsspeicher des Workerknotens groß genug für einen oder beide Datenströme ist, können Sie die Leistung optimieren, indem Sie die **Übertragung** aktivieren. Standardmäßig entscheidet die Spark-Engine automatisch, ob eine Seite übertragen werden soll oder nicht. Klicken Sie auf **Fest** , um die zu übertragende Seite manuell auszuwählen.

Es wird nicht empfohlen, die Übertragung über die Option **Off** (Aus) zu deaktivieren, es sei denn, für Ihre Joins treten Timeoutfehler auf.

## <a name="self-join"></a>Selbstverknüpfung

Wenn Sie einen Datenstrom mit sich selbst verknüpfen möchten, müssen Sie einen vorhandenen Stream per Auswahltransformation mit einem Alias versehen. Erstellen Sie einen neuen Branch, indem Sie neben einer Transformation auf das Pluszeichen klicken und **Neuer Branch** auswählen. Fügen Sie eine Auswahltransformation hinzu, um den ursprünglichen Stream mit einem Alias zu versehen. Fügen Sie eine Join-Transformation hinzu. Wählen Sie dabei den ursprünglichen Stream als **Linker Stream** und die Auswahltransformation als **Rechter Stream** aus.

![Selbstverknüpfung](media/data-flow/selfjoin.png "Selbstverknüpfung")

## <a name="testing-join-conditions"></a>Testen der Join-Bedingungen

Verwenden Sie einen kleinen Satz bekannter Daten, wenn Sie die Join-Transformationen mit Datenvorschau im Debugmodus testen. Bei der Stichprobenentnahme aus einem umfangreichen Dataset können Sie nicht vorhersagen, welche Zeilen und Schlüssel zu Testzwecken gelesen werden. Das Ergebnis ist nicht deterministisch. Das bedeutet, dass für Ihre Verknüpfungsbedingungen möglicherweise keine Übereinstimmungen zurückgegeben werden.

## <a name="data-flow-script"></a>Datenflussskript

### <a name="syntax"></a>Syntax

```
<leftStream>, <rightStream>
    join(
        <conditionalExpression>,
        joinType: { 'inner'> | 'outer' | 'left_outer' | 'right_outer' | 'cross' }
        broadcast: { 'auto' | 'left' | 'right' | 'both' | 'off' }
    ) ~> <joinTransformationName>
```

### <a name="inner-join-example"></a>Beispiel für einen inneren Join

Das folgende Beispiel ist eine Join-Transformation namens `JoinMatchedData` mit dem linken Stream `TripData` und dem rechten Stream `TripFare`.  Die Verknüpfungsbedingung ist der Ausdruck `hack_license == { hack_license} && TripData@medallion == TripFare@medallion && vendor_id == { vendor_id} && pickup_datetime == { pickup_datetime}`, der „true“ zurückgibt, wenn die Spalten `hack_license`, `medallion`, `vendor_id` und `pickup_datetime` in den beiden Streams übereinstimmen. Der Jointyp (`joinType`) lautet `'inner'`. Da Broadcasting nur im linken Stream aktiviert wird, hat `broadcast` den Wert `'left'`.

Auf der Data Factory-Benutzeroberfläche sieht diese Transformation wie folgt aus:

![Screenshot der Transformation mit ausgewählter Registerkarte für Joineinstellungen und dem Jointyp „Innerer“](media/data-flow/join-script1.png "Join-Beispiel")

Das Datenflussskript für diese Transformation befindet sich im folgenden Codeausschnitt:

```
TripData, TripFare
    join(
        hack_license == { hack_license}
        && TripData@medallion == TripFare@medallion
        && vendor_id == { vendor_id}
        && pickup_datetime == { pickup_datetime},
        joinType:'inner',
        broadcast: 'left'
    )~> JoinMatchedData
```

### <a name="custom-cross-join-example"></a>Beispiel für ein benutzerdefiniertes Kreuzprodukt

Das folgende Beispiel ist eine Join-Transformation namens `JoiningColumns` mit dem linken Stream `LeftStream` und dem rechten Stream `RightStream`. Diese Transformation übernimmt zwei Streams und verknüpft alle Zeilen, in denen die Spalte `leftstreamcolumn` größer als die Spalte `rightstreamcolumn` ist. Der Jointyp (`joinType`) lautet `cross`. Broadcasting ist nicht aktiviert, `broadcast` weist den Wert `'none'` auf.

Auf der Data Factory-Benutzeroberfläche sieht diese Transformation wie folgt aus:

![Screenshot der Transformation mit ausgewählter Registerkarte für Joineinstellungen und dem Jointyp „Benutzerdefiniert“ (Kreuzprodukt)](media/data-flow/join-script2.png "Join-Beispiel")

Das Datenflussskript für diese Transformation befindet sich im folgenden Codeausschnitt:

```
LeftStream, RightStream
    join(
        leftstreamcolumn > rightstreamcolumn,
        joinType:'cross',
        broadcast: 'none'
    )~> JoiningColumns
```

## <a name="next-steps"></a>Nächste Schritte

Erstellen Sie nach dem Verknüpfen von Daten eine [abgeleitete Spalte](data-flow-derived-column.md), und [senken](data-flow-sink.md) Sie Ihre Daten in einen Zieldatenspeicher.
