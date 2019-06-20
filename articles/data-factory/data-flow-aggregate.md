---
title: 'Azure Data Factory Mapping Data Flow: Transformation für das Aggregieren'
description: Azure Data Factory-Datenflusstransformation für das Aggregieren
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/01/2019
ms.openlocfilehash: 7b488b243c0520befb6b5470598f460b5a759fed
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61467356"
---
# <a name="azure-data-factory-mapping-data-flow-aggregate-transformation"></a>Azure Data Factory Mapping Data Flow: Transformation für das Aggregieren

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

In der Transformation für das Aggregieren definieren Sie Aggregationen von Spalten in Ihren Datenströmen. Im Ausdrucks-Generator können Sie verschiedene Typen von Aggregationen (z. B. SUMME, MIN, MAX, ANZAHL usw.) definieren und ein neues Feld in Ihrer Ausgabe erstellen. Darin sind diese Aggregationen mit optionalen Feldern des Typs „Gruppieren nach“ enthalten.

![Optionen für die Transformation für das Aggregieren](media/data-flow/agg.png "Aggregieren 1")

## <a name="group-by"></a>Gruppieren nach
(Optional) Wählen Sie eine GROUP BY-Klausel für Ihre Aggregation aus, und verwenden Sie entweder den Namen einer vorhandenen Spalte oder einen neuen Namen. Verwenden Sie „Spalte hinzufügen“, um weitere GROUP BY-Klauseln hinzuzufügen, und klicken Sie auf das Textfeld neben dem Spaltennamen, um den Ausdrucks-Generator zu starten, um entweder eine vorhandene Spalte, eine Kombination aus Spalten oder Ausdrücke für die Gruppierung auszuwählen.

## <a name="the-aggregate-column-tab"></a>Die Registerkarte „Spalte aggregieren“ 
(Erforderlich) Wählen Sie die Registerkarte „Spalte aggregieren“ aus, um die Aggregationsausdrücke zu erstellen. Sie können entweder eine vorhandene Spalte auswählen, um den Wert mit der Aggregation zu überschreiben, oder ein neues Feld mit dem neuen Namen für die Aggregation erstellen. Der Ausdruck, den Sie für die Aggregation verwenden möchten, wird im Feld rechts neben der Spaltennamenauswahl eingegeben. Durch Klicken auf dieses Textfeld wird der Ausdrucks-Generator geöffnet.

![Optionen für die Transformation für das Aggregieren](media/data-flow/agg2.png "Aggregator")

## <a name="data-preview-in-expression-builder"></a>Datenvorschau im Ausdrucks-Generator

Im Debugmodus kann der Ausdrucks-Generator keine Datenvorschau mit Aggregatfunktionen generieren. Wenn Sie Datenvorschauen für Transformationen für das Aggregieren anzeigen möchten, schließen Sie den Ausdrucks-Generator, und zeigen Sie das Datenprofil aus dem Datenfluss-Designer an.
