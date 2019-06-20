---
title: Datenzuordnungsflow in Azure Data Factory – Verweisknoten
description: Im Data Factory-Datenfluss wird ein Verweisknoten für Joins, Suchvorgänge und Unions hinzugefügt.
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: 626943143e8fa193f143e66d856d9b00e3589fb5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61262669"
---
# <a name="mapping-data-flow-reference-node"></a>Mapping Data Flow: Verweisknoten

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

![Verweisknoten](media/data-flow/referencenode.png "Verweisknoten")

Ein Verweisknoten wird in der Canvas automatisch hinzugefügt, um anzugeben, dass der Knoten an Verweise auf einen anderen Knoten in der Canvas angefügt ist. Sie können sich einen Verweisknoten als Zeiger oder Verweis auf eine andere Datenflusstransformation vorstellen.

Beispiel:  Wenn Sie mehrere Datenströme verknüpfen oder vereinigen, wird in der Datenfluss-Canvas möglicherweise ein Verweisknoten hinzugefügt, der den Namen und die Einstellungen des nicht primären eingehenden Datenstroms darstellt.

Der Verweisknoten kann nicht verschoben oder gelöscht werden. Sie können jedoch in den Knoten klicken, um die ursprünglichen Transformationseinstellungen zu ändern.

Die Benutzeroberflächenregeln, die angewandt werden, wenn der Verweisknoten im Datenfluss hinzugefügt wird, basieren auf dem verfügbaren Platz und vertikalen Abstand zwischen den Zeilen.
