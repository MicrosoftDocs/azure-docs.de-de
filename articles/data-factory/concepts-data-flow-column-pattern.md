---
title: Azure Data Factory Mapping Data Flow – Spaltenmuster
description: Erfahren Sie, wie Sie mit Spaltenmustern von Azure Data Factory im Mappingdatenfluss generalisierte Vorlagenmuster zum Transformieren von Feldern in einem Datenfluss ohne Berücksichtigung der zugrunde liegenden Schemametadaten erstellen.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 08cdaafe00b7dc586ea75f6ff03fdb89107edee9
ms.sourcegitcommit: 087ee51483b7180f9e897431e83f37b08ec890ae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/31/2019
ms.locfileid: "66430760"
---
# <a name="azure-data-factory-mapping-data-flows-column-patterns"></a>Azure Data Factory-Mappingdatenfluss – Spaltenmuster

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Mehrere Azure Data Factory-Datenflusstransformationen unterstützen das Konzept von „Spaltenmustern“, sodass Sie Vorlagenspalten erstellen können, die auf Mustern anstelle von hartcodierten Spaltennamen basieren. Sie können dieses Feature im Ausdrucks-Generator verwenden, um Muster gemäß den Spalten für die Transformation zu definieren, sodass keine exakten, bestimmten Feldnamen erforderlich sind. Muster sind bei sich häufig ändernden eingehenden Quellfeldern hilfreich, insbesondere beim Ändern von Spalten in Textdateien oder NoSQL-Datenbanken. Diese Bedingung wird manchmal als „Schemaabweichung“ bezeichnet.

![Spaltenmuster](media/data-flow/columnpattern2.png "Spaltenmuster")

Spaltenmuster sind sowohl in Schemaabweichungs- als auch in allgemeinen Szenarien nützlich. Sie eignen sich gut für Bedingungen, bei denen Sie nicht jeden Spaltennamen vollständig kennen. Sie können eine Musterübereinstimmung basierend auf dem Spaltennamen und Spaltendatentyp herstellen und einen Ausdruck für die Transformation erstellen, die diesen Vorgang für jedes Feld im Datenstrom ausführt, das Ihren `name` & `type`-Mustern entspricht.

Wählen Sie beim Hinzufügen eines Ausdrucks zu einer Transformation, die Muster akzeptiert, die Option „Spaltenmuster hinzufügen“ aus. Spaltenmuster ermöglichen den Abgleich von Schemaabweichungs-Spaltenmustern.

Geben Sie beim Erstellen von Vorlagenspaltenmustern im Ausdruck `$$` an, um einen Verweis auf jedes übereinstimmende Feld aus dem Eingabedatenstrom darzustellen.

Wenn Sie eine der RegEx-Funktionen des Ausdrucks-Generators verwenden möchten, können Sie anschließend mit $1, $2, $3 usw. auf die von Ihrem regulären Ausdruck abgeglichenen Teilmuster verweisen.

Ein Beispiel für ein Spaltenmusterszenario ist die Verwendung von SUMME mit einer Reihe von eingehenden Feldern. Die aggregierten Berechnungen von SUMME befinden sich in der Aggregat-Transformation. Sie können dann bei jeder Übereinstimmung der Feldtypen, die mit „Integer“ übereinstimmen, SUMME verwenden und dann mit $$ auf jede Übereinstimmung in Ihrem Ausdruck verweisen.
