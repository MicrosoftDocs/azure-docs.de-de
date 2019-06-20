---
title: 'Azure Data Factory Mapping Data Flow: Transformation für Ersatzschlüssel'
description: 'Verwenden von Azure Data Factory Mapping Data Flow: Transformation für Ersatzschlüssel zum Generieren eines nachfolgenden Schlüsselwerts'
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: eaa1c577f7e208400d3430222b006e0dbbd7956a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61350429"
---
# <a name="mapping-data-flow-surrogate-key-transformation"></a>Mapping Data Flow: Transformation für Ersatzschlüssel

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Verwenden Sie die Transformation für Ersatzschlüssel, um Ihrem Datenflussrowset einen inkrementellen, nicht geschäftlichen beliebigen Schlüsselwert hinzuzufügen. Dies ist nützlich, wenn Sie Dimensionstabellen in einem analytischen Sternschema-Datenmodell entwerfen, bei dem jedes Element in Ihren Dimensionstabellen über einen eindeutigen Schlüssel verfügen muss, der kein geschäftlicher Schlüssel ist (Bestandteil der Kimball-DW-Methode).

![Transformation für Ersatzschlüssel](media/data-flow/surrogate.png "Transformation für Ersatzschlüssel")

Die „Schlüsselspalte“ ist der Name, den Sie Ihrer neuen Ersatzschlüsselspalte zuweisen.

„Startwert“ ist der Anfangspunkt des inkrementellen Werts.

## <a name="increment-keys-from-existing-sources"></a>Inkrementelle Schlüssel aus vorhandenen Quellen

Wenn Sie Ihre Sequenz von einem Wert aus starten möchten, der in einer Quelle vorhanden ist, können Sie eine Transformation für abgeleitete Spalten unmittelbar nach der Transformation für Ersatzschlüssels verwenden und die beiden Werte zusammenfügen:

![SK add Max](media/data-flow/sk006.png "Surrogate Key Transformation Add Max")

Um den Schlüsselwert mit dem vorherigen Maximum zu versehen, gibt es zwei Techniken, die Sie verwenden können:

### <a name="database-sources"></a>Datenbankquellen

Verwenden Sie die Option „Abfrage“, um MAX() aus Ihrer Quelle mithilfe der Quelltransformation auszuwählen:

![Surrogate Key Query](media/data-flow/sk002.png "Surrogate Key Transformation Query")

### <a name="file-sources"></a>Dateiquellen

Wenn sich Ihr vorheriger Maximalwert in einer Datei befindet, können Sie Ihre Quelltransformation zusammen mit einer Aggregattransformation verwenden und die Funktion „MAX() expression“ verwenden, um den vorherigen Maximalwert zu erhalten:

![Surrogate Key File](media/data-flow/sk008.png "Surrogate Key File")

In beiden Fällen müssen Sie Ihre eingehenden neuen Daten zusammen mit Ihrer Quelle, die den vorherigen Maximalwert enthält, zusammenfügen:

![Surrogate Key Join](media/data-flow/sk004.png "Surrogate Key Join")

## <a name="next-steps"></a>Nächste Schritte

Diese Beispiele verwenden die Transformationen [Verbinden](data-flow-join.md) und [Abgeleitete Spalten](data-flow-derived-column.md) Transformationen.
