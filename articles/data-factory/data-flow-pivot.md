---
title: 'Azure Data Factory Mapping Data Flow: Pivottransformation'
description: Pivotieren Sie Daten aus Zeilen in Spalten mithilfe der Azure Data Factory Mapping Data Flow-Pivottransformation
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: e16cac281b77f3ca93d9ef358ae806203bc8b663
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61348446"
---
# <a name="azure-data-factory-pivot-transformation"></a>Azure Data Factory-Pivottransformation
[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Verwenden Sie Pivot in ADF-Datenfluss als Aggregation, bei der die unterschiedlichen Zeilenwerte einer oder mehrerer Gruppierungsspalten in einzelne Spalten transformiert wurden. Im Wesentlichen können Sie Zeilenwerte in neue Spalten pivotieren (Daten in Metadaten umwandeln).

![Pivotoptionen](media/data-flow/pivot1.png "Pivot 1")

## <a name="group-by"></a>Gruppieren nach

![Pivotoptionen](media/data-flow/pivot2.png "Pivot 2")

Legen Sie zuerst die Spalten fest, nach denen Sie für Ihre Pivotaggregation gruppieren möchten. Sie können mit dem Pluszeichen (+) neben der Spaltenliste mehr als 1 Spalte festlegen.

## <a name="pivot-key"></a>Pivotschlüssel

![Pivotoptionen](media/data-flow/pivot3.png "Pivot 3")

Der Pivotschlüssel ist die Spalte, die ADF von Zeile in Spalte pivotiert. Standardmäßig wird jeder eindeutige Wert im Dataset für dieses Feld in eine Spalte pivotiert. Sie können jedoch die Werte aus dem Dataset optional eingeben, die in Spaltenwerte pivotiert werden sollen. Dies ist die Spalte, die die neuen, zu erstellenden Spalten bestimmt.

## <a name="pivoted-columns"></a>Pivotierte Spalten

![Pivotoptionen](media/data-flow/pivot4.png "Pivot 4")

Wählen Sie abschließend die für die pivotierten Werte zu verwendende Aggregation aus, und wählen Sie aus, wie die Spalten in der neuen Ausgabeprojektion der Transformation angezeigt werden sollen.

(Optional) Sie können ein Benennungsmuster mit einem Präfix, einem Mittelteil und einem Suffix festlegen, das/der jedem neuen Spaltennamen aus den Zeilenwerten hinzugefügt wird.

Wenn Sie beispielsweise „Umsatz“ nach „Region“ pivotieren, erhalten Sie neue Spaltenwerte aus den einzelnen Umsatzzahlen. Beispiel: „25“, „50“, „1000“ usw. Wenn Sie jedoch den Präfixwert „Sales-“ festlegen, wird „Sales-“ am Anfang jedes Spaltenwerts hinzugefügt.

![Pivotoptionen](media/data-flow/pivot5.png "Pivot 5")

Wenn Sie die Spaltenanordnung auf „Normal“ festlegen, werden alle pivotierten Spalten zusammen mit ihren aggregierten Werten gruppiert. Wenn Sie die Spaltenanordnung auf „Lateral“ festlegen, wird zwischen Spalte und Wert gewechselt.

### <a name="aggregation"></a>Aggregation

Klicken Sie zum Festlegen der für die Pivotwerte zu verwendenden Aggregation auf das Feld am unteren Rand des Bereichs „Pivotierte Spalten“. Der ADF-Datenfluss-Ausdrucks-Generator wird geöffnet, in dem Sie einen Aggregationsausdruck erstellen und einen beschreibenden Aliasnamen für Ihre neuen aggregierten Werte angeben können.

Verwenden Sie die ADF-Datenfluss-Ausdruckssprache, um die Transformationen der pivotierten Spalten im Ausdrucks-Generator zu beschreiben: https://aka.ms/dataflowexpressions.

## <a name="pivot-metadata"></a>Pivotmetadaten

Die Pivottransformation generiert auf der Basis Ihrer eingehenden Daten neue, dynamische Spaltendaten. Der Pivotschlüssel produziert die Werte für jeden neuen Spaltennamen. Wenn Sie keine einzelnen Namen angeben und dynamische Spaltennamen für jeden eindeutigen Wert in Ihrem Pivotschlüssel erstellen möchten, werden die Metadaten auf der Benutzeroberfläche nicht in „Untersuchen“ angezeigt, und es gibt keine Spaltenweitergabe an die Sink-Transformation. Wenn Sie Werte für den Pivotschlüssel festlegen, kann ADF die neuen Spaltennamen bestimmen, und diese Spaltennamen stehen Ihnen in der Inspect- und Sink-Zuordnung zur Verfügung.

### <a name="landing-new-columns-in-sink"></a>Bereitstellen neuer Spalten in Sink

Trotz dynamischer Spaltennamen in Pivot können Sie Ihre neuen Spaltennamen und -werte in Ihren Zieldatenspeicher senken. Legen Sie einfach in Ihren Sink-Einstellungen „Schemaabweichung zulassen“ fest. Die neuen dynamischen Namen werden nicht in Ihren Spaltenmetadaten angezeigt, die Option zur Schemaabweichung ermöglicht Ihnen aber trotzdem die Bereitstellung der Daten.

### <a name="view-metadata-in-design-mode"></a>Anzeigen von Metadaten im Entwurfsmodus

Wenn Sie die neuen Spaltennamen in „Untersuchen“ als Metadaten anzeigen und die Weitergabe der Spalten an die Sink-Transformation explizit sehen möchten, legen Sie auf der Registerkarte Pivotschlüssel explizite Werte fest.

### <a name="how-to-rejoin-original-fields"></a>So verknüpfen Sie die ursprünglichen Felder erneut
Die Pivottransformation projiziert nur die in der Aggregation, Gruppierung und Pivotaktion verwendeten Spalten. Wenn Sie die anderen Spalten aus dem vorherigen Schritt in Ihren Datenfluss einbeziehen möchten, verwenden Sie eine neue Verzweigung aus dem vorherigen Schritt, und verwenden Sie dann das Selbstverknüpfungsmuster, um den Datenfluss mit den ursprünglichen Metadaten zu verbinden.

## <a name="next-steps"></a>Nächste Schritte

Versuchen Sie, die [Entpivotierungstransformation](data-flow-unpivot.md) durchzuführen, um Spaltenwerte in Zeilenwerte umzuwandeln. 
