---
title: Parametrisieren von Zuordnungsdatenflüssen
description: Erfahren Sie, wie Sie eine Mapping Data Flow-Funktion aus Data Factory-Pipelines parametrisieren können.
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 05/01/2020
ms.openlocfilehash: 564c7cf6e9627db08d543b964ce476e71bfb473d
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93040749"
---
# <a name="parameterizing-mapping-data-flows"></a>Parametrisieren von Zuordnungsdatenflüssen

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)] 

Zuordnungsdatenflüsse in Azure Data Factory unterstützen die Verwendung von Parametern. Definieren Sie Parameter innerhalb Ihrer Datenflussdefinition, und verwenden Sie sie in allen Ihren Ausdrücken. Die Parameterwerte werden von der aufrufenden Pipeline über die Aktivität „Datenfluss ausführen“ festgelegt. Sie haben drei Möglichkeiten, die Werte in den Ausdrücken der Datenflussaktivität festzulegen:

* Verwenden der Ausdruckssprache für die Pipeline-Ablaufsteuerung, um einen dynamischen Wert festzulegen
* Verwenden der Ausdruckssprache für den Datenfluss, um einen dynamischen Wert festzulegen
* Verwenden einer der beiden Ausdruckssprachen, um einen statischen Literalwert festzulegen

Nutzen Sie diese Funktion, um Ihre Datenflüsse universell einsetzbar, flexibel und wiederverwendbar zu gestalten. Mit diesen Parametern können Sie Datenflusseinstellungen und -ausdrücke parametrisieren.

## <a name="create-parameters-in-a-mapping-data-flow"></a>Erstellen von Parametern in einem Zuordnungsdatenfluss

Wenn Sie Parameter zu Ihrem Datenfluss hinzufügen möchten, klicken Sie auf den leeren Bereich der Datenflusscanvas, um die allgemeinen Eigenschaften anzuzeigen. Im Einstellungsbereich sehen Sie die Registerkarte **Parameter**. Wählen Sie **Neu** aus, um einen neuen Parameter zu generieren. Für jeden Parameter müssen Sie einen Namen angeben, einen Typ auswählen und optional einen Standardwert festlegen.

![Erstellen von Datenflussparametern](media/data-flow/create-params.png "Erstellen von Datenflussparametern")

## <a name="use-parameters-in-a-mapping-data-flow"></a>Verwenden von Parametern in einem Zuordnungsdatenfluss 

Auf Parameter kann in jedem beliebigen Datenflussausdruck verwiesen werden. Parameter beginnen mit $ und sind unveränderlich. Die Liste der verfügbaren Parameter finden Sie im Ausdrucks-Generator auf der Registerkarte **Parameter**.

![Screenshot: Verfügbare Parameter auf der Registerkarte „Parameter“](media/data-flow/parameter-expression.png "Datenflussparameterausdruck")

Sie können zusätzliche Parameter schnell hinzufügen, indem Sie **Neuer Parameter** auswählen und dann den Namen und Typ angeben.

![Screenshot: Parameter auf der Registerkarte „Parameter“, auf der neue Parameter hinzugefügt wurden](media/data-flow/new-parameter-expression.png "Datenflussparameterausdruck")

## <a name="assign-parameter-values-from-a-pipeline"></a>Zuweisen von Parameterwerten aus einer Pipeline

Nachdem Sie einen Datenfluss mit Parametern erstellt haben, können Sie ihn mit der Aktivität „Datenfluss ausführen“ aus einer Pipeline ausführen. Nachdem Sie die Aktivität zu Ihrer Pipelinecanvas hinzugefügt haben, werden Ihnen auf der Registerkarte **Parameter** der Aktivität die verfügbaren Datenflussparameter angezeigt.

Zum Zuweisen von Parameterwerten können Sie entsprechend dem Spark-Typ entweder die [Ausdruckssprache für die ADF-Pipeline](control-flow-expression-language-functions.md) oder die [Ausdruckssprache für Datenflüsse](data-flow-expression-functions.md) verwenden. Jede Mapping Data Flow-Funktion kann eine beliebige Kombination von Pipeline- und Datenfluss-Ausdrucksparametern aufweisen.

![Screenshot: Registerkarte „Parameter“, auf der für „myparam“ der Wert für den Datenflussausdruck ausgewählt ist](media/data-flow/parameter-assign.png "Festlegen eines Datenflussparameters")

### <a name="pipeline-expression-parameters"></a>Pipeline-Ausdrucksparameter

Mit Pipeline-Ausdrucksparametern können Sie ähnlich anderen Pipelineaktivitäten auf Systemvariablen, Funktionen, Pipelineparameter und Variablen verweisen. Wenn Sie auf **Pipeline expression** (Pipelineausdruck) klicken, wird ein Seitennavigationsbereich geöffnet, in dem Sie einen Ausdruck mithilfe des Ausdrucks-Generators eingeben können.

![Screenshot: Bereich für den Ausdrucks-Generator](media/data-flow/parameter-pipeline.png "Festlegen eines Datenflussparameters")

Wenn auf die Pipelineparameter verwiesen wird, werden sie ausgewertet. Der Wert wird dann in der Ausdruckssprache für Datenflüsse verwendet. Der Typ des Pipelineausdrucks muss nicht mit dem Parametertyp im Datenfluss übereinstimmen. 

#### <a name="string-literals-vs-expressions"></a>Zeichenfolgenliterale im Vergleich zu Ausdrücken

Wenn Sie einen Pipeline-Ausdrucksparameter vom Typ String zuweisen, werden standardmäßig Anführungszeichen hinzugefügt, und der Wert wird als Literalwert ausgewertet. Um den Parameterwert als Datenflussausdruck zu lesen, aktivieren Sie das Feld „Ausdruck“ neben dem Parameter.

![Screenshot: Bereich „Datenflussparameter“, in dem für einen Parameter der Wert „Ausdruck“ aktiviert ist](media/data-flow/string-parameter.png "Festlegen eines Datenflussparameters")

Wenn der Datenflussparameter `stringParam` auf einen Pipelineparameter mit dem Wert `upper(column1)` verweist. 

- Wenn „Ausdruck“ aktiviert ist, wird `$stringParam` als der Wert von „column1“ in Großbuchstaben ausgewertet.
- Wenn „Ausdruck“ nicht aktiviert ist (Standardverhalten), wird `$stringParam` als `'upper(column1)'` ausgewertet.

#### <a name="passing-in-timestamps"></a>Übergeben von Zeitstempeln

In der Pipeline-Ausdruckssprache können Systemvariablen wie `pipeline().TriggerTime` und Funktionen wie `utcNow()` Zeitstempel als Zeichenfolgen im Format „jjjj-MM-tt\'T\'HH:mm:ss.SSSSSSZ“ zurückgeben. Verwenden Sie zum Konvertieren dieser Ausdrücke in Datenflussparametern vom Typ Zeitstempel die Zeichenfolgeninterpolation, um den gewünschten Zeitstempel in eine `toTimestamp()`-Funktion einzuschließen. Wenn Sie z. B. die Pipeline-Auslösungszeit in einen Datenflussparameter konvertieren möchten, können Sie `toTimestamp(left('@{pipeline().TriggerTime}', 23), 'yyyy-MM-dd\'T\'HH:mm:ss.SSS')` verwenden. 

![Screenshot: Registerkarte „Parameter“, auf der Sie eine Auslösungszeit eingeben können](media/data-flow/parameter-timestamp.png "Festlegen eines Datenflussparameters")

> [!NOTE]
> Datenflüsse unterstützen nur bis zu drei Millisekundenstellen. Die `left()`-Funktion wird zum Kürzen zusätzlicher Stellen verwendet.

#### <a name="pipeline-parameter-example"></a>Beispiel für Pipelineparameter

Angenommen, Sie verfügen über den Integerparameter `intParam`, der auf einen Pipelineparameter vom Typ String verweist: `@pipeline.parameters.pipelineParam`. 

![Screenshot: Registerkarte „Parameter“ mit den Parametern „stringParam“ und „intParam“](media/data-flow/parameter-pipeline-2.png "Festlegen eines Datenflussparameters")

`@pipeline.parameters.pipelineParam` wird zur Laufzeit der Wert `abs(1)` zugewiesen.

![Screenshot: Registerkarte „Parameter“, auf der der Wert „abs(1)“ ausgewählt ist](media/data-flow/parameter-pipeline-4.png "Festlegen eines Datenflussparameters")

Wenn in einem Ausdruck auf `$intParam` verwiesen wird, z. B. in einer abgeleiteten Spalte, wird `abs(1)` ausgewertet und `1` zurückgegeben. 

![Screenshot: Wert für „Spalten“](media/data-flow/parameter-pipeline-3.png "Festlegen eines Datenflussparameters")

### <a name="data-flow-expression-parameters"></a>Datenfluss-Ausdrucksparameter

Wählen Sie **Data flow expression** (Datenflussausdruck) aus, um den Ausdrucks-Generator für Datenflüsse zu öffnen. Sie können im gesamten Datenfluss auf Funktionen, andere Parameter und jede definierte Schemaspalte verweisen. Dieser Ausdruck wird in dem Zustand ausgewertet, in dem auf ihn verwiesen wird.

> [!NOTE]
> Wenn Sie einen ungültigen Ausdruck übergeben oder auf eine Schemaspalte verweisen, die in dieser Transformation nicht vorhanden ist, wird der Parameter als NULL ausgewertet.


### <a name="passing-in-a-column-name-as-a-parameter"></a>Übergeben eines Spaltennamens als Parameter

Ein gängiges Muster ist die Übergabe eines Spaltennamen als Parameterwert. Wenn die Spalte im Datenflussschema definiert ist, können Sie direkt als Zeichenfolgenausdruck auf sie verweisen. Wenn die Spalte nicht im Schema definiert ist, verwenden Sie die `byName()`-Funktion. Denken Sie daran, die Spalte mithilfe einer Umwandlungsfunktion wie `toString()` in ihren entsprechenden Typ umzuwandeln.

Wenn Sie beispielsweise eine Zeichenfolgenspalte aufgrund eines `columnName`-Parameters zuordnen möchten, können Sie eine abgeleitete Spaltentransformation gleich `toString(byName($columnName))` hinzufügen.

![Übergeben eines Spaltennamens als Parameter](media/data-flow/parameterize-column-name.png "Übergeben eines Spaltennamens als Parameter")

## <a name="next-steps"></a>Nächste Schritte
* [Ausführen der Datenflussaktivität](control-flow-execute-data-flow-activity.md)
* [Ausdrücke für die Ablaufsteuerung](control-flow-expression-language-functions.md)
