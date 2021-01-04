---
title: Transformation für Ersatzschlüssel im Zuordnungsdatenfluss
description: Hier erfahren Sie, wie Sie die Transformation für Ersatzschlüssel von Azure Data Factory Mapping Data Flow zum Generieren nachfolgender Schlüsselwerte verwenden.
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/30/2020
ms.openlocfilehash: d1f8993b1adc297b1bfadba114df76a66e59afa2
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/01/2020
ms.locfileid: "93147175"
---
# <a name="surrogate-key-transformation-in-mapping-data-flow"></a>Transformation für Ersatzschlüssel im Zuordnungsdatenfluss 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Verwenden Sie die Transformation für Ersatzschlüssel, um einen inkrementell geänderten Schlüsselwert für jede Datenzeile hinzuzufügen. Dieses Vorgehen ist nützlich, wenn Sie Dimensionstabellen in einem analytischen Datenmodell mit Sternschema entwerfen. In einem Sternschema erfordert jedes Mitglied in Dimensionstabellen einen eindeutigen Schlüssel, bei dem es sich nicht um einen Geschäftsschlüssel handelt.

## <a name="configuration"></a>Konfiguration

![Transformation für Ersatzschlüssel](media/data-flow/surrogate.png "Transformation für Ersatzschlüssel")

**Schlüsselspalte** : Der Name der Spalte mit dem generierten Ersatzschlüssel.

**Startwert** : Die niedrigste Schlüsselwert, der generiert wird.

## <a name="increment-keys-from-existing-sources"></a>Inkrementelle Schlüssel aus vorhandenen Quellen

Um Ihre Sequenz von einem Wert aus zu starten, der in einer Quelle vorhanden ist, wird empfohlen, eine Cachesenke zum Speichern dieses Werts und eine Transformation für abgeleitete Spalten zum gemeinsamen Hinzufügen der beiden Werte zu verwenden. Verwenden Sie eine zwischengespeicherte Suche, um die Ausgabe abzurufen, und fügen Sie sie an den generierten Schlüssel an. Weitere Informationen finden Sie unter [Cachesenken](data-flow-sink.md#cache-sink) und [zwischengespeicherten Suchen](concepts-data-flow-expression-builder.md#cached-lookup).

![Ersatzschlüsselsuche](media/data-flow/cached-lookup-example.png "Ersatzschlüsselsuche")

### <a name="increment-from-existing-maximum-value"></a>Inkrement des vorhandenen Maximalwerts

Ein Seeding für den Schlüsselwert mit dem vorherigen Maximalwert lässt sich mit zwei Methoden durchführen, je nachdem, wo sich Ihre Quelldaten befinden.

#### <a name="database-sources"></a>Datenbankquellen

Verwenden Sie eine SQL-Abfrageoption, um „MAX()“ aus Ihrer Quelle auszuwählen. Beispiel: `Select MAX(<surrogateKeyName>) as maxval from <sourceTable>`.

![Ersatzschlüsselabfrage](media/data-flow/surrogate-key-max-database.png "Transformation für Ersatzschlüssel – Abfrage")

#### <a name="file-sources"></a>Dateiquellen

Wenn sich der vorherige Maximalwert in einer Datei befindet, verwenden Sie die `max()`-Funktion in der Aggregattransformation, um den vorherigen Maximalwert abzurufen:

![Ersatzschlüsseldatei](media/data-flow/surrogate-key-max-file.png "Ersatzschlüsseldatei")

In beiden Fällen müssen Sie in eine Cachesenke schreiben und den Wert suchen. 


## <a name="data-flow-script"></a>Datenflussskript

### <a name="syntax"></a>Syntax

```
<incomingStream> 
    keyGenerate(
        output(<surrogateColumnName> as long),
        startAt: <number>L
    ) ~> <surrogateKeyTransformationName>
```

### <a name="example"></a>Beispiel

![Transformation für Ersatzschlüssel](media/data-flow/surrogate.png "Transformation für Ersatzschlüssel")

Der nachfolgende Codeausschnitt zeigt das Datenflussskript für die obige Konfiguration des Ersatzschlüssels.

```
AggregateDayStats
    keyGenerate(
        output(key as long),
        startAt: 1L
    ) ~> SurrogateKey1
```

## <a name="next-steps"></a>Nächste Schritte

Diese Beispiele verwenden die Transformationen [Verbinden](data-flow-join.md) und [Abgeleitete Spalten](data-flow-derived-column.md) Transformationen.
