---
title: Problembehandlung bei Designermodulfehlern
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie Fehlercodes automatisierter Module im Azure Machine Learning-Designer lesen und die entsprechenden Probleme beheben.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.custom: troubleshooting
author: likebupt
ms.author: keli19
ms.date: 11/25/2020
ms.openlocfilehash: 846c5519dced06ed16f5a0d12b0bb25443961f93
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96753908"
---
# <a name="exceptions-and-error-codes-for-the-designer"></a>Ausnahmen und Fehlercodes für den Designer

In diesem Artikel werden die Fehlermeldungen und Ausnahmecodes im Azure Machine Learning-Designer beschrieben, um Ihnen bei der Problembehandlung Ihrer Pipelines für maschinelles Lernen zu helfen.

Sie können die Fehlermeldung im Designer finden, indem Sie die folgenden Schritte ausführen:  

- Wählen Sie das fehlerhafte Modul aus, und wechseln Sie zur Registerkarte **Outputs+logs** (Ausgaben und Protokolle). Sie finden das ausführliche Protokoll in der Datei **70_driver_log.txt** unter der Kategorie **azureml-logs**.

- Ausführliche Informationen zum Modulfehler finden Sie in der Datei „error_info.json“ unter der Kategorie **module_statistics**.

Im Folgenden finden Sie die Fehlercodes von Modulen im Designer.

## <a name="error-0001"></a>Fehler 0001  
 Eine Ausnahme tritt auf, wenn eine oder mehrere angegebene Spalten des Datasets nicht gefunden werden konnten.  

 Sie erhalten diesen Fehler, wenn für ein Modul eine Spaltenauswahl vorgenommen wird, die ausgewählte(n) Spalte(n) jedoch nicht im Eingabedataset vorhanden ist (sind). Dieser Fehler kann auftreten, wenn Sie einen Spaltennamen manuell eingegeben haben oder wenn der Spaltenselektor eine vorgeschlagene Spalte bereitgestellt hat, die bei der Pipelineausführung nicht im Dataset vorhanden war.  

**Lösung:** Rufen Sie das Modul erneut auf, das diese Ausnahme auslöst, und überprüfen Sie, ob die Spaltennamen korrekt und alle referenzierten Spalten vorhanden sind.  

|Ausnahmemeldungen|
|------------------------|
|One or more specified columns were not found. (Eine oder mehrere angegebene Spalten wurden nicht gefunden.)|
|Column with name or index "{column_id}" not found. (Spalte mit Name oder Index „{column_id}“ nicht gefunden.)|
|Column with name or index "{column_id}" does not exist in "{arg_name_missing_column}". (Spalte mit Name oder Index „{column_id}“ ist in „{arg_name_missing_column}“ nicht vorhanden.)|
|Column with name or index "{column_id}" does not exist in "{arg_name_missing_column}", but exists in "{arg_name_has_column}". (Spalte mit Name oder Index "{column_id}" ist in "{arg_name_missing_column}"nicht vorhanden, aber in „{arg_name_has_column}“ vorhanden.)|
|Column with name or index „{column_names}“ not found. (Spalte mit Name oder Index „{column_names}“ nicht gefunden.)|
|Column with name or index „{column_names}“ does not exist in „{arg_name_missing_column}“. (Spalte mit Name oder Index „{column_names}“ ist in „{arg_name_missing_column}“ nicht vorhanden.)|
|Column with name or index „{column_id}“ does not exist in „{arg_name_missing_column}“, but exists in „{arg_name_has_column}“. (Spalte mit Name oder Index „{column_names}“ ist in „{arg_name_missing_column}“ nicht vorhanden, aber in „{arg_name_has_column}“.)|


## <a name="error-0002"></a>Fehler 0002  
 Eine Ausnahme tritt auf, wenn mindestens ein Parameter nicht analysiert oder vom angegebenen Typ in den vom Zielmethodentyp erforderlichen Typ konvertiert werden konnte.  

 Dieser Fehler tritt in Azure Machine Learning auf, wenn Sie einen Parameter als Eingabe angeben und der Werttyp sich von dem erwarteten Typ unterscheidet und eine implizite Konvertierung nicht durchgeführt werden kann.  

**Lösung:** Überprüfen Sie die Anforderungen des Moduls und ermitteln Sie, welcher Werttyp erforderlich ist (string, integer, double usw.).  

|Ausnahmemeldungen|
|------------------------|
|Failed to parse parameter. (Fehler beim Analysieren eines Parameters.)|
|Failed to parse "{arg_name_or_column}" parameter. (Fehler beim Analysieren des Parameters „{arg_name_or_column}“.)|
|Failed to convert "{arg_name_or_column}" parameter to "{to_type}". (Fehler beim Konvertieren des Parameters „{arg_name_or_column}“ in „{to_type}“.)|
|Failed to convert "{arg_name_or_column}" parameter from "{from_type}" to "{to_type}". (Fehler beim Konvertieren des Parameters „{arg_name_or_column}“ von „{from_type}“ in „{to_type}“.)|
|Failed to convert "{arg_name_or_column}" parameter value "{arg_value}" from "{from_type}" to "{to_type}". (Fehler beim Konvertieren des Werts „{arg_value}“ des Parameters „{arg_name_or_column}“ von „{from_type}“ in „{to_type}“.)|
|Failed to convert value "{arg_value}" in column "{arg_name_or_column}" from "{from_type}" to "{to_type}" with usage of the format "{fmt}" provided. (Fehler beim Konvertieren des Werts „{arg_value}“ in Spalte „{arg_name_or_column}“ von „{from_type}“ in „{to_type}“ unter Verwendung des Formats „{fmt}“.)|


## <a name="error-0003"></a>Fehler 0003  
 Eine Ausnahme tritt auf, wenn mindestens eine Eingabe NULL oder leer ist.  

 Sie erhalten diesen Fehler in Azure Machine Learning, wenn Eingaben oder Parameter in einem Modul NULL oder leer sind.  Dieser Fehler kann z. B. auftreten, wenn Sie für einen Parameter keinen Wert eingegeben haben. Er kann auch auftreten, wenn Sie ein Dataset mit fehlenden Werten oder ein leeres Dataset ausgewählt haben.  

**Lösung:**

+ Öffnen Sie das Modul, das die Ausnahme ausgelöst hat, und überprüfen Sie, ob alle Eingaben angegeben wurden. Stellen Sie sicher, dass alle erforderlichen Eingaben vorgenommen wurden. 
+ Stellen Sie sicher, dass auf aus Azure-Speicher geladene Daten zugegriffen werden kann und sich der Kontoname oder Schlüssel nicht geändert hat.  
+ Überprüfen Sie die Eingabedaten auf fehlende oder NULL-Werte.
+ Wenn Sie eine Abfrage für eine Datenquelle verwenden, überprüfen Sie, ob die Daten in dem von Ihnen erwarteten Format zurückgegeben werden. 
+ Überprüfen Sie, ob Tippfehler oder andere Änderungen in der Spezifikation der Daten vorliegen.
  
|Ausnahmemeldungen|
|------------------------|
|One or more of inputs are null or empty. (Mindestens eine Eingabe ist null oder leer.)|
|Die Eingabe „{name}“ ist NULL oder leer.|


## <a name="error-0004"></a>Fehler 0004  
 Eine Ausnahme tritt auf, wenn der Parameter kleiner als oder gleich dem bestimmten Wert ist.  

 Sie erhalten diesen Fehler in Azure Machine Learning, wenn der Parameter in der Meldung unter einem Grenzwert liegt, der für das Modul zur Verarbeitung der Daten erforderlich ist.  

**Lösung:** Rufen Sie das Modul erneut auf, und ändern Sie den Parameter so, dass er größer als der angegebene Wert ist.  

|Ausnahmemeldungen|
|------------------------|
|Der Parameter muss größer als der Grenzwert sein|
|Der Wert des Parameters „{arg_name}“ sollte größer als {lower_boundary} sein.|
|Der Parameter „{arg_name}“ besitzt den Wert „{actual_value}“, der größer als {lower_boundary} sein sollte.|


## <a name="error-0005"></a>Fehler 0005  
 Eine Ausnahme tritt auf, wenn der Parameter kleiner als ein bestimmter Wert ist.  

 Sie erhalten diesen Fehler in Azure Machine Learning, wenn der Parameter in der Meldung kleiner als oder gleich einem Grenzwert ist, der für das Modul zur Verarbeitung der Daten erforderlich ist.  

**Lösung:** Rufen Sie das Modul erneut auf, und ändern Sie den Parameter so, dass er größer als oder gleich dem angegebenen Wert ist.  

|Ausnahmemeldungen|
|------------------------|
|Der Parameter muss größer als oder gleich dem Grenzwert sein|
|Der Wert des Parameters „{arg_name}“ sollte größer als oder gleich {lower_boundary} sein.|
|Der Parameter „{arg_name}“ besitzt den Wert „{value}“, der größer als oder gleich {lower_boundary} sein sollte.|


## <a name="error-0006"></a>Fehler 0006  
 Eine Ausnahme tritt auf, wenn der Parameter größer als oder gleich dem bestimmten Wert ist.  

 Sie erhalten diesen Fehler in Azure Machine Learning, wenn der Parameter in der Meldung größer als oder gleich einem Grenzwert ist, der für das Modul zur Verarbeitung der Daten erforderlich ist.  

**Lösung:** Rufen Sie das Modul erneut auf, und ändern Sie den Parameter so, dass er kleiner als der angegebene Wert ist.  

|Ausnahmemeldungen|
|------------------------|
|Parameterkonflikt Einer der Parameter muss kleiner als ein anderer sein|
|Der Wert des Parameters „{arg_name}“ sollte kleiner als der Wert von Parameter „{upper_boundary_parameter_name}“ sein.|
|Der Parameter „{arg_name}“ besitzt den Wert „{value}“, der kleiner als {upper_boundary_parameter_name} sein sollte.|


## <a name="error-0007"></a>Fehler 0007  
 Eine Ausnahme tritt auf, wenn der Parameter größer als ein bestimmter Wert ist.  

 Sie erhalten diesen Fehler in Azure Machine Learning, wenn Sie in den Eigenschaften des Moduls einen Wert angegeben haben, der größer als erlaubt ist. Sie können z. B. Daten angeben, die außerhalb des Bereichs der unterstützten Daten liegen, oder Sie können angeben, dass fünf Spalten verwendet werden, wenn nur drei Spalten verfügbar sind. 

 Dieser Fehler kann auch auftreten, wenn Sie zwei Datensätze angeben, die in gewisser Weise übereinstimmen müssen. Wenn Sie z. B. Spalten umbenennen und die Spalten nach Index angeben, muss die Anzahl der von Ihnen bereitgestellten Namen mit der Anzahl der Spaltenindizes übereinstimmen. Ein weiteres Beispiel wäre eine mathematische Operation, die zwei Spalten verwendet, wobei die Spalten dieselbe Anzahl von Zeilen aufweisen müssen. 

**Lösung:**

 + Öffnen Sie das betreffende Modul, und überprüfen Sie die Einstellungen der numerischen Eigenschaft.
 + Stellen Sie sicher, dass alle Parameterwerte innerhalb des unterstützten Wertebereichs für diese Eigenschaft liegen.
 + Wenn das Modul mehrere Eingaben übernimmt, stellen Sie sicher, dass die Eingaben dieselbe Größe aufweisen.
<!-- + If the module has multiple properties that can be set, ensure that related properties have appropriate values. For example, when using [Group Data into Bins](group-data-into-bins.md), if you use the option to specify custom bin edges, the number of bins must match the number of values you provide as bin boundaries.-->
 + Überprüfen Sie, ob sich das Dataset oder die Datenquelle geändert hat. Gelegentlich führt ein Wert, der mit einer früheren Version der Daten funktioniert hat, zu einem Fehler, nachdem sich die Anzahl der Spalten, die Datentypen der Spalte oder die Größe der Daten geändert hat.  

|Ausnahmemeldungen|
|------------------------|
|Parameterkonflikt Einer der Parameter muss kleiner als oder gleich einem anderen Parameter sein|
|Der Wert des Parameters „{arg_name}“ sollte kleiner als oder gleich dem Wert von Parameter „{upper_boundary_parameter_name}“ sein.|
|Der Parameter „{arg_name}“ besitzt den Wert „{actual_value}“, der kleiner als oder gleich {upper_boundary} sein sollte.|
|Der Wert {actual_value} des Parameters „{arg_name}“ sollte kleiner als oder gleich dem Wert {upper_boundary} von Parameter „{upper_boundary_parameter_name}“ sein.|
|Der Wert „{actual_value}“ des Parameters „{arg_name}“ sollte kleiner als oder gleich dem Wert „{upper_boundary}“ des Parameters „{upper_boundary_meaning}“ sein.|


## <a name="error-0008"></a>Fehler 0008  
 Eine Ausnahme tritt auf, wenn der Parameter nicht im Bereich liegt.  

 Sie erhalten diesen Fehler in Azure Machine Learning, wenn der Parameter in der Meldung außerhalb der Grenzen liegt, die für das Modul zur Verarbeitung der Daten erforderlich sind.  

 Dieser Fehler wird z. B. angezeigt, wenn Sie versuchen, mit [Add Rows](add-rows.md) (Zeilen hinzufügen) zwei Datasets zu kombinieren, die eine unterschiedliche Anzahl von Spalten aufweisen.  

**Lösung:** Rufen Sie das Modul erneut auf, und ändern Sie den Parameter so, dass er innerhalb des angegebenen Bereichs liegt.  

|Ausnahmemeldungen|
|------------------------|
|Der Parameterwert liegt nicht im angegebenen Bereich|
|Parameter "{arg_name}" value is not in range.|
|Der Wert des Parameters „{arg_name}“ sollte sich im Bereich von [{lower_boundary}, {upper_boundary}] befinden.|
|Parameter "{arg_name}" value is not in range. {reason}|


## <a name="error-0009"></a>Fehler 0009  
 Eine Ausnahme tritt auf, wenn der Name des Azure-Speicherkontos oder der Containername falsch angegeben wurde.  

Dieser Fehler tritt in Azure Machine Learning-Designer auf, wenn Sie Parameter für ein Azure-Speicherkonto angeben, aber der Name oder das Kennwort nicht aufgelöst werden kann. Fehler bei Kennwörtern oder Kontonamen können viele Ursachen haben:

 + Das Konto weist den falschen Typ auf. Einige neue Kontotypen werden für die Verwendung mit Machine Learning-Designer nicht unterstützt. Weitere Informationen finden Sie unter [Import Data](import-data.md) (Daten importieren).
 + Sie haben den falschen Kontonamen eingegeben.
 + Das Konto ist nicht mehr vorhanden.
 + Das Kennwort für das Speicherkonto ist falsch oder hat sich geändert.
 + Sie haben den Containernamen nicht angegeben, oder der Container ist nicht vorhanden.
 + Sie haben den Dateipfad (Pfad zum Blob) nicht vollständig angegeben.
   

**Lösung:**

Solche Probleme treten häufig auf, wenn Sie versuchen, den Kontonamen, das Passwort oder den Containerpfad manuell einzugeben. Es wird empfohlen, den neuen Assistenten für das Modul [Import Data](import-data.md) (Daten importieren) zu verwenden, der Ihnen beim Suchen und Überprüfen von Namen hilft.

Überprüfen Sie auch, ob Konto, Container oder Blob gelöscht wurde. Verwenden Sie ein anderes Azure-Speicherhilfsprogramm, um sicherzustellen, dass Kontoname und Kennwort ordnungsgemäß eingegeben wurden und der Container vorhanden ist. 

Einige neuere Kontotypen werden von Azure Machine Learning nicht unterstützt. Die neuen Speichertypen „hot“ (heiß) oder „cold“ (kalt) können z. B. nicht für maschinelles Lernen verwendet werden. Sowohl klassische als auch universelle Speicherkonten funktionieren einwandfrei.

Wenn der vollständige Pfad zu einem Blob angegeben wurde, vergewissern Sie sich, dass der Pfad das Format **Container/Blobname** aufweist und sowohl Container als auch Blob im Konto vorhanden sind.  

 Der Pfad darf keinen führenden Schrägstrich enthalten. Beispiel: **/Container/Blob** ist falsch und muss als **Container/Blob** eingegeben werden.  


|Ausnahmemeldungen|
|------------------------|
|Der Name des Azure-Speicherkontos oder des Containers ist falsch.|
|Der Name des Azure-Speicherkontos „{account_name}“oder des Containers „{container_name}“ ist falsch. Es wird ein Containername im Format „Container/Blob“ erwartet.|


## <a name="error-0010"></a>Fehler 0010  
 Eine Ausnahme tritt auf, wenn Eingabedatasets übereinstimmende Spaltennamen aufweisen sollten, dies aber nicht der Fall ist.  

 Sie erhalten diesen Fehler in Azure Machine Learning, wenn der Spaltenindex in der Meldung unterschiedliche Spaltennamen in den beiden Eingabedatasets aufweist.  

**Lösung:** Verwenden Sie [Edit Metadata](edit-metadata.md) (Metadaten bearbeiten), oder ändern Sie das ursprüngliche Dataset, um denselben Spaltennamen für den angegebenen Spaltenindex zu erhalten.  

|Ausnahmemeldungen|
|------------------------|
|Spalten mit entsprechendem Index in Eingabedatasets weisen unterschiedliche Namen auf.|
|Die Spaltennamen sind für die Spalte {col_index} (nullbasiert) der Eingabedatasets ({dataset1} und {dataset2}) nicht identisch.|


## <a name="error-0011"></a>Fehler 0011  
 Eine Ausnahme tritt auf, wenn das übergebene Spaltensatzargument nicht für eine der Datasetspalten gilt.  

 Sie erhalten diesen Fehler in Azure Machine Learning, wenn die angegebene Spaltenauswahl nicht mit einer der Spalten im angegebenen Dataset übereinstimmt.  

 Sie können diesen Fehler auch erhalten, wenn Sie keine Spalte ausgewählt haben und mindestens eine Spalte erforderlich ist, damit das Modul funktioniert.  

**Lösung:** Ändern Sie die Spaltenauswahl im Modul so, dass sie auf die Spalten im Dataset angewendet wird.  

 Wenn das Modul die Auswahl einer bestimmten Spalte erfordert, z. B. eine Bezeichnungsspalte, vergewissern Sie sich, dass die richtige Spalte ausgewählt ist.  

 Sind ungeeignete Spalten ausgewählt, entfernen Sie sie, und führen Sie die Pipeline erneut aus.  

|Ausnahmemeldungen|
|------------------------|
|Der angegebene Spaltensatz gilt für keine der Datasetspalten.|
|Der angegebene Spaltensatz „{column_set}“ gilt für keine der Datasetspalten.|


## <a name="error-0012"></a>Fehler 0012  
 Eine Ausnahme tritt auf, wenn die Instanz der Klasse nicht mit dem übergebenen Satz von Argumenten erstellt werden konnte.  

**Lösung:** Dieser Fehler ist für den Benutzer nicht umsetzbar und wird in einem zukünftigen Release veraltet sein.  

|Ausnahmemeldungen|
|------------------------|
|Untrained model, please train model first.|
|Untrained model ({arg_name}), use trained model.|


## <a name="error-0013"></a>Fehler 0013  
 Eine Ausnahme tritt auf, wenn der an das Modul übergebene Learner ein ungültiger Typ ist.  

 Dieser Fehler tritt auf, wenn ein trainiertes Modell mit dem angeschlossenen Bewertungsmodul nicht kompatibel ist. <!--For example, connecting the output of [Train Matchbox Recommender](train-matchbox-recommender.md) to [Score Model](score-model.md) (instead of [Score Matchbox Recommender](score-matchbox-recommender.md)) will generate this error when the pipeline is run.  -->

**Lösung:**

Bestimmen Sie den Typ des Learner, der vom Trainingsmodul erzeugt wird, und bestimmen Sie das für den Learner geeignete Bewertungsmodul. 

Wenn das Modell mit einem der speziellen Trainingsmodule trainiert wurde, verbinden Sie das trainierte Modell nur mit dem entsprechenden spezialisierten Bewertungsmodul. 


|Modelltyp|Trainingsmodul| Bewertungsmodul|
|----|----|----|
|Alle Klassifizierer|[Train Model](train-model.md) (Modell trainieren) |[Score Model](score-model.md) (Modell bewerten)|
|Alle Regressionsmodelle|[Train Model](train-model.md) (Modell trainieren) |[Score Model](score-model.md) (Modell bewerten)|

<!--| clustering models| [Train Clustering Model](train-clustering-model.md) or [Sweep Clustering](sweep-clustering.md)| [Assign Data to Clusters](assign-data-to-clusters.md)|
| anomaly detection - One-Class SVM | [Train Anomaly Detection Model](train-anomaly-detection-model.md) |[Score Model](score-model.md)|
| anomaly detection - PCA |[Train Model](train-model.md) |[Score Model](score-model.md) </br> Some additional steps are required to evaluate the model. |
| anomaly detection - time series|  [Time Series Anomaly Detection](time-series-anomaly-detection.md) |Model trains from data and generates scores. The module does not create a trained learner and no additional scoring is required. |
| recommendation model| [Train Matchbox Recommender](train-matchbox-recommender.md) | [Score Matchbox Recommender](score-matchbox-recommender.md) |
| image classification | [Pretrained Cascade Image Classification](pretrained-cascade-image-classification.md) | [Score Model](score-model.md) |
|Vowpal Wabbit models| [Train Vowpal Wabbit Version 7-4 Model](train-vowpal-wabbit-version-7-4-model.md) | [Score Vowpal Wabbit Version 7-4 Model](score-vowpal-wabbit-version-7-4-model.md) |   
|Vowpal Wabbit models| [Train Vowpal Wabbit Version 7-10 Model](train-vowpal-wabbit-version-7-10-model.md) | [Score Vowpal Wabbit Version 7-10 Model](score-vowpal-wabbit-version-7-10-model.md) |
|Vowpal Wabbit models| [Train Vowpal Wabbit Version 8 Model](score-vowpal-wabbit-version-8-model.md) | [Score Vowpal Wabbit Version 8 Model](score-vowpal-wabbit-version-8-model.md) |-->

|Ausnahmemeldungen|
|------------------------|
|Learner mit ungültigem Typ wird übergeben.|
|Learner "{arg_name}" has invalid type.|
|Learner "{arg_name}" has invalid type "{learner_type}".|
|Learner mit ungültigem Typ wird übergeben. Ausnahmemeldung: {exception_message}|


## <a name="error-0014"></a>Fehler 0014  
 Eine Ausnahme tritt auf, wenn die Anzahl der in der Spalte eindeutigen Werte größer als der zulässige Wert ist.  

 Dieser Fehler tritt auf, wenn eine Spalte zu viele eindeutige Werte enthält, z. B. eine ID-Spalte oder eine Textspalte. Dieser Fehler kann auftreten, wenn Sie angeben, dass eine Spalte als kategorische Daten behandelt wird, aber es zu viele eindeutige Werte in der Spalte gibt, als dass die Verarbeitung abgeschlossen werden kann. Dieser Fehler kann auch auftreten, wenn die Anzahl der eindeutigen Werte bei zwei Eingaben nicht übereinstimmt.   

Der Fehler einer zu hohen Anzahl von eindeutigen Werten tritt auf, wenn für eine Besprechung die **beiden** folgenden Bedingungen zutreffen:

- Mehr als 97 % der Instanzen einer Spalte sind eindeutige Werte. Dies bedeutet, dass sich fast alle Kategorien voneinander unterscheiden.
- Eine Spalte enthält mehr als 1.000 eindeutige Werte.

**Lösung:**

Öffnen Sie das Modul, das den Fehler generiert hat, und identifizieren Sie die als Eingaben verwendeten Spalten. Bei einigen Modulen können Sie mit der rechten Maustaste auf die Dataseteingabe klicken und **Visualize** (Visualisieren) auswählen, um Statistiken zu einzelnen Spalten zu erhalten, einschließlich der Anzahl der eindeutigen Werte und deren Verteilung.

Für Spalten, die Sie für die Gruppierung oder Kategorisierung verwenden möchten, führen Sie entsprechende Schritte durch, um die Anzahl der eindeutigen Werte in den Spalten zu reduzieren. Je nach Datentyp der Spalte kann die Reduzierung auf unterschiedliche Weise erfolgen. 

Für ID-Spalten, die beim Training eines Modells keine sinnvollen Features darstellen, können Sie [Metadaten bearbeiten](../algorithm-module-reference/edit-metadata.md) verwenden, um diese Spalte als **Feature löschen** zu markieren, damit sie beim Training eines Modells nicht verwendet wird. 

Für Textspalten können Sie [Feature Hashing](../algorithm-module-reference/feature-hashing.md) oder [Extract N-Gram Features from Text module](../algorithm-module-reference/extract-n-gram-features-from-text.md) verwenden, um Textspalten vorzuverarbeiten.
<!--
+ For text data, you might be able to use [Preprocess Text](preprocess-text.md) to collapse similar entries. 
+ For numeric data, you can create a smaller number of bins using [Group Data into Bins](group-data-into-bins.md), remove or truncate values using [Clip Values](clip-values.md), or use machine learning methods such as [Principal Component Analysis](principal-component-analysis.md) or [Learning with Counts](data-transformation-learning-with-counts.md) to reduce the dimensionality of the data.  
-->
> [!TIP]
> Sie können keine zu Ihrem Szenario passende Lösung finden? Sie können Feedback zu diesem Thema bereitstellen, das den Namen des Moduls, das den Fehler generiert hat, sowie den Datentyp und die Kardinalität der Spalte enthält. Wir werden diese Informationen verwenden, um besser auf das Ziel ausgerichtete Schritte zur Problembehandlung für gängige Szenarien bereitzustellen.   

|Ausnahmemeldungen|
|------------------------|
|Amount of column unique values is greater than allowed.|
|Number of unique values in column: "{column_name}" is greater than allowed.|
|Number of unique values in column: "{column_name}" exceeds tuple count of {limitation}.|


## <a name="error-0015"></a>Fehler 0015  
 Eine Ausnahme tritt auf, wenn bei der Datenbankverbindung ein Fehler aufgetreten ist.  

 Sie erhalten diesen Fehler, wenn Sie einen falschen SQL-Kontonamen, ein falsches Kennwort, einen falschen Datenbankserver oder Datenbanknamen eingeben oder wenn eine Verbindung mit der Datenbank aufgrund von Problemen mit der Datenbank oder dem Server nicht hergestellt werden kann.  

**Lösung:** Vergewissern Sie sich, dass Kontoname, Kennwort, Datenbankserver und Datenbank ordnungsgemäß eingegeben wurden und dass das angegebene Konto über die richtige Berechtigungsebene verfügt. Überprüfen Sie, ob auf die Datenbank derzeit zugegriffen werden kann.  

|Ausnahmemeldungen|
|------------------------|
|Fehler beim Herstellen der Datenbankverbindung.|
|Fehler beim Herstellen der Datenbankverbindung: {connection_str}.|


## <a name="error-0016"></a>Fehler 0016  
 Eine Ausnahme tritt auf, wenn an das Modul übergebene Eingabedatasets kompatible Spaltentypen aufweisen sollten, dies aber nicht der Fall ist.  

 Sie erhalten diesen Fehler in Azure Machine Learning, wenn die Typen der Spalten, die in zwei oder mehr Datasets übergeben werden, untereinander nicht kompatibel sind.  

**Lösung:** Verwenden Sie [Edit Metadata](edit-metadata.md) (Metadaten bearbeiten), oder ändern Sie das ursprüngliche Eingabedataset,<!--, or use [Convert to Dataset](convert-to-dataset.md)--> um sicherzustellen, dass die Typen der Spalten kompatibel sind.  

|Ausnahmemeldungen|
|------------------------|
|Spalten mit entsprechendem Index in Eingabedatasets weisen nicht kompatible Typen auf.|
|Columns '{first_col_names}' are incompatible between train and test data.|
|Columns '{first_col_names}' and '{second_col_names}' are incompatible.|
|Column element types are not compatible for column '{first_col_names}' (zero-based) of input datasets ({first_dataset_names} and {second_dataset_names} respectively).|


## <a name="error-0017"></a>Fehler 0017  
 Eine Ausnahme tritt auf, wenn eine ausgewählte Spalte einen Datentyp verwendet, der vom aktuellen Modul nicht unterstützt wird.  

 Möglicherweise erhalten Sie diesen Fehler in Azure Machine Learning, wenn Ihre Spaltenauswahl eine Spalte mit einem Datentyp umfasst, der vom Modul nicht verarbeitet werden kann, z. B. eine Zeichenfolgenspalte für eine mathematische Operation oder eine Bewertungsspalte, in der eine kategorische Featurespalte erforderlich ist.  

**Lösung:**
 1. Identifizieren Sie die fehlerhafte Spalte.
 2. Überprüfen Sie die Anforderungen des Moduls.
 3. Ändern Sie die Spalte so, dass sie den Anforderungen entspricht. Möglicherweise müssen Sie mehrere der folgenden Module verwenden, um Änderungen je nach versuchter Spalte und Konvertierung vorzunehmen:
    + Verwenden Sie [Edit Metadata](edit-metadata.md) (Metadaten bearbeiten), um den Datentyp von Spalten oder die Spaltenverwendung von „feature“ zu „numerisch“, „kategorisch“ zu „nicht kategorisch“ usw. zu ändern.
<!--    + Use [Convert to Dataset](convert-to-dataset.md) to ensure that all included columns use data types that are supported by Azure Machine Learning.  If you cannot convert the columns, consider removing them from the input dataset.
    + Use the [Apply SQL Transformation](apply-sql-transformation.md) or [Execute R Script](execute-r-script.md) modules to cast or convert any columns that cannot be modified using [Edit Metadata](edit-metadata.md). These modules provide more flexibility for working with datetime data types.
    + For numeric data types, you can use the [Apply Math Operation](apply-math-operation.md) module to round or truncate values, or use the [Clip Values](clip-values.md) module to remove out of range values.  -->
 4. Als letztes Mittel kann es sein, dass Sie das ursprüngliche Eingabedataset ändern müssen.

> [!TIP]
> Sie können keine zu Ihrem Szenario passende Lösung finden? Sie können Feedback zu diesem Thema bereitstellen, das den Namen des Moduls, das den Fehler generiert hat, sowie den Datentyp und die Kardinalität der Spalte enthält. Wir werden diese Informationen verwenden, um besser auf das Ziel ausgerichtete Schritte zur Problembehandlung für gängige Szenarien bereitzustellen. 

|Ausnahmemeldungen|
|------------------------|
|Die Spalte mit dem aktuellen Typ kann nicht verarbeitet werden. Der Typ wird vom Modul nicht unterstützt.|
|Cannot process column of type {col_type}. Der Typ wird vom Modul nicht unterstützt.|
|Cannot process column "{col_name}" of type {col_type}. Der Typ wird vom Modul nicht unterstützt.|
|Cannot process column "{col_name}" of type {col_type}. Der Typ wird vom Modul nicht unterstützt. Parameter name: {arg_name}.|


## <a name="error-0018"></a>Fehler 0018  
 Eine Ausnahme tritt auf, wenn das Eingabedataset nicht gültig ist.  

**Lösung:** Dieser Fehler in Azure Machine Learning kann in vielen Kontexten auftreten, sodass es keine einzelne Lösung gibt. Im Allgemeinen zeigt der Fehler an, dass die als Eingabe für ein Modul bereitgestellten Daten die falsche Anzahl von Spalten aufweisen oder dass der Datentyp nicht den Anforderungen des Moduls entspricht. Beispiel:  

-   Das Modul erfordert eine Bezeichnungsspalte, aber keine Spalte ist als Bezeichnung gekennzeichnet, oder Sie haben noch keine Bezeichnungsspalte ausgewählt.  
  
-   Das Modul erfordert kategorische Daten, aber Ihre Daten sind numerisch.  

<!---   The module requires a specific data type. For example, ratings provided to [Train Matchbox Recommender](train-matchbox-recommender.md) can be either numeric or categorical, but cannot be floating point numbers.  -->

-   Die Daten weisen nicht das richtige Format auf.  
  
-   Importierte Daten enthalten ungültige Zeichen, ungültige oder außerhalb des Bereichs liegende Werte.  
-   Die Spalte ist leer oder enthält zu viele fehlende Werte.  

 Lesen Sie das Hilfethema zum Modul, das das Dataset als Eingabe verwenden soll, um die Anforderungen zu ermitteln.  

 <!--We also recommend that you use [Summarize Data](summarize-data.md) or [Compute Elementary Statistics](compute-elementary-statistics.md) to profile your data, and use these modules to fix metadata and clean values: [Edit Metadata](edit-metadata.md) and [Clean Missing Data](clean-missing-data.md), [Clip Values](clip-values.md)-->.  

|Ausnahmemeldungen|
|------------------------|
|Das Dataset ist ungültig.|
|{dataset1} contains invalid data.|
|{dataset1} and {dataset2} should be consistent columnwise.|
|{dataset1} contains invalid data, {reason}.|
|{dataset1} contains {invalid_data_category}. {troubleshoot_hint}|
|{Dataset1} ist ungültig, {Grund}. {troubleshoot_hint}|


## <a name="error-0019"></a>Fehler 0019  
 Eine Ausnahme tritt auf, wenn die Spalte sortierte Werte enthalten soll, aber das nicht der Fall ist.  

 Sie erhalten diesen Fehler in Azure Machine Learning, wenn die angegebenen Spaltenwerte nicht die richtige Reihenfolge aufweisen.  

**Lösung:** Sortieren Sie die Spaltenwerte, indem Sie das Eingabedataset manuell ändern und das Modul erneut ausführen.  

|Ausnahmemeldungen|
|------------------------|
|Die Werte in der Spalte sind nicht sortiert.|
|Die Werte in der Spalte „{col_index}“ sind nicht sortiert.|
|Die Werte in der Spalte „{col_index}“ von Dataset „{dataset}“ sind nicht sortiert.|
|Werte im Argument „{arg_name}“ werden nicht in der Reihenfolge „{sorting_order}“ sortiert.|


## <a name="error-0020"></a>Fehler 0020  
 Eine Ausnahme tritt auf, wenn die Anzahl der Spalten in einigen der an das Modul übergebenen Datasets zu klein ist.  

 Sie erhalten diesen Fehler in Azure Machine Learning, wenn für ein Modul nicht genügend Spalten ausgewählt wurden.  

**Lösung:** Rufen Sie das Modul erneut auf, und stellen Sie sicher, dass der Spaltenselektor die richtige Anzahl von Spalten ausgewählt hat.  

|Ausnahmemeldungen|
|------------------------|
|Die Anzahl der Spalten im Eingabedataset ist kleiner als der zulässige Mindestwert.|
|Die Anzahl der Spalten im Eingabedataset „{arg_name}“ ist kleiner als der zulässige Mindestwert.|
|Number of columns in input dataset is less than allowed minimum of {required_columns_count} column(s).|
|Number of columns in input dataset "{arg_name}" is less than allowed minimum of {required_columns_count} column(s).|


## <a name="error-0021"></a>Fehler 0021  
 Eine Ausnahme tritt auf, wenn die Anzahl der Zeilen in einigen der an das Modul übergebenen Datasets zu klein ist.  

 Dieser Fehler wird in Azure Machine Learning angezeigt, wenn nicht genügend Zeilen im Dataset vorhanden sind, um die angegebene Operation durchzuführen. Dieser Fehler kann z. B. auftreten, wenn das Eingabedataset leer ist oder wenn Sie versuchen, eine Operation auszuführen, die eine Mindestanzahl von Zeilen erfordert, um gültig zu sein. Derartige Operationen können die Gruppierung oder Klassifizierung auf der Grundlage statistischer Methoden, bestimmter Arten der Quantisierung und des Lernens durch Anzahl umfassen (sind aber nicht darauf beschränkt).  

**Lösung:**

 + Öffnen Sie das Modul, das den Fehler zurückgegeben hat, und überprüfen Sie das Eingabedataset und die Moduleigenschaften. 
 + Überprüfen Sie, ob das Eingabedataset nicht leer ist und ob genügend Datenzeilen vorhanden sind, um die in der Modulhilfe beschriebenen Anforderungen zu erfüllen.  
 + Wenn Ihre Daten aus einer externen Quelle geladen werden, stellen Sie sicher, dass die Datenquelle verfügbar ist und dass keine Fehler oder Änderungen in der Datendefinition vorliegen, die dazu führen würden, dass der Importvorgang weniger Zeilen erhält.
 + Wenn Sie eine Operation mit den dem Modul vorgelagerten Daten durchführen, die sich auf den Typ der Daten oder die Anzahl der Werte auswirken könnte, z. B. Operationen zur Bereinigung, Aufteilung oder Verknüpfung, überprüfen Sie die Ausgaben dieser Operationen, um die Anzahl der zurückgegebenen Zeilen zu ermitteln.  

|Ausnahmemeldungen|
|------------------------|
|Number of rows in input dataset is less than allowed minimum.|
|Number of rows in input dataset is less than allowed minimum of {required_rows_count} row(s).|
|Number of rows in input dataset is less than allowed minimum of {required_rows_count} row(s). {reason}|
|Number of rows in input dataset "{arg_name}" is less than allowed minimum of {required_rows_count} row(s).|
|Number of rows in input dataset "{arg_name}" is {actual_rows_count}, less than allowed minimum of {required_rows_count} row(s).|
|Number of "{row_type}" rows in input dataset "{arg_name}" is {actual_rows_count}, less than allowed minimum of {required_rows_count} row(s).|


## <a name="error-0022"></a>Fehler 0022  
 Eine Ausnahme tritt auf, wenn die Anzahl der ausgewählten Spalten im Eingabedataset nicht mit der erwarteten Anzahl übereinstimmt.  

 Dieser Fehler in Azure Machine Learning kann auftreten, wenn das Downstream-Modul oder die Operation eine bestimmte Anzahl von Spalten oder Eingaben erfordert und Sie zu wenige bzw. zu viele Spalten oder Eingaben bereitgestellt haben. Beispiel:  

-   Sie geben eine einzelne Bezeichnungsspalte oder Schlüsselspalte an und haben versehentlich mehrere Spalten ausgewählt.  
  
-   Sie benennen Spalten um, haben aber mehr oder weniger Namen bereitgestellt, als Spalten vorhanden sind.  
  
-   Die Anzahl der Spalten in der Quelle oder im Ziel hat sich geändert oder stimmt nicht mit der Anzahl der vom Modul verwendeten Spalten überein.  
  
-   Sie haben eine durch Kommas getrennte Liste von Werten für Eingaben angegeben, aber die Anzahl der Werte stimmt nicht überein, oder mehrere Eingaben werden nicht unterstützt.  

**Lösung:** Rufen Sie das Modul erneut auf, und überprüfen Sie die Spaltenauswahl, um sicherzustellen, dass die richtige Anzahl von Spalten ausgewählt ist. Überprüfen Sie die Ergebnisse der Upstream-Module und die Anforderungen der Downstream-Operationen.  

 Wenn Sie eine der Spaltenauswahloptionen verwendet haben, die mehrere Spalten auswählen kann (Spaltenindizes, alle Features, alle numerisch usw.), überprüfen Sie die genaue Anzahl der von der Auswahl zurückgegebenen Spalten.  

 <!--If you are trying to specify a comma-separated list of datasets as inputs to [Unpack Zipped Datasets](unpack-zipped-datasets.md), unpack only one dataset at a time. Multiple inputs are not supported.  -->

 Stellen Sie sicher, dass sich die Anzahl oder der Typ der Upstream-Spalten nicht geändert hat.  

 Wenn Sie zum Trainieren eines Modells ein Empfehlungsdataset verwenden, denken Sie daran, dass der Empfehlende eine begrenzte Anzahl von Spalten erwartet, die den Benutzer-Element-Paaren oder Benutzer-Element-Rangfolgen entspricht. Entfernen Sie zusätzliche Spalten, bevor Sie das Modell trainieren oder Empfehlungsdatasets aufteilen. Weitere Informationen finden Sie unter [Split Data](split-data.md) (Daten aufteilen).  

|Ausnahmemeldungen|
|------------------------|
|Die Anzahl der ausgewählten Spalten im Eingabedataset stimmt nicht mit der erwarteten Anzahl überein.|
|Die Anzahl der ausgewählten Spalten im Eingabedataset stimmt nicht {expected_col_count} überein.|
|Das Spaltenauswahlmuster „{selection_pattern_friendly_name}“ liefert die Anzahl der ausgewählten Spalten im Eingabedataset, die nicht {expected_col_count} entspricht.|
|Für das Spaltenauswahlmuster „{selection_pattern_friendly_name}“ sollte(n) {expected_col_count} Spalte(n) im Eingabedataset ausgewählt sein, aber es wird/werden {selected_col_count} Spalte(n) bereitgestellt.|


## <a name="error-0023"></a>Fehler 0023  

Eine Ausnahme tritt auf, wenn die Zielspalte des Eingabedatasets für das aktuelle Trainermodul nicht gültig ist.  

Dieser Fehler in Azure Machine Learning tritt auf, wenn die Zielspalte (gemäß der Auswahl in den Modulparametern) nicht dem gültigen Datentyp entspricht, alle fehlenden Werte enthält oder nicht wie erwartet kategorisch war.  

**Lösung:** Rufen Sie die Moduleingabe erneut auf, um den Inhalt der Bezeichnungs-/Zielspalte zu untersuchen. Stellen Sie sicher, dass sie nicht alle fehlenden Werte enthält. Wenn das Modul eine kategorische Zielspalte erwartet, stellen Sie sicher, dass es mehr als einen eindeutigen Wert in der Zielspalte gibt.  

|Ausnahmemeldungen|
|------------------------|
|Das Eingabedataset enthält eine nicht unterstützte Zielspalte.|
|Das Eingabedataset enthält die nicht unterstützte Zielspalte „{column_index}“.|
|Das Eingabedataset enthält die nicht unterstützte Zielspalte „{column_index}“ für Learner des Typs {learner_type}.|


## <a name="error-0024"></a>Fehler 0024  
Eine Ausnahme tritt auf, wenn das Dataset keine Bezeichnungsspalte enthält.  

 Dieser Fehler in Azure Machine Learning tritt auf, wenn das Modul eine Bezeichnungsspalte erfordert und das Dataset keine Bezeichnungsspalte bereitstellt. Die Auswertung eines bewerteten Datasets erfordert z. B. in der Regel, dass eine Bezeichnungsspalte vorhanden ist, um die Genauigkeitsmetriken zu berechnen.  

Es kann auch vorkommen, dass eine Bezeichnungsspalte im Dataset vorhanden ist, aber von Azure Machine Learning nicht ordnungsgemäß erkannt wird.

**Lösung:**

+ Öffnen Sie das Modul, das den Fehler generiert hat, und ermitteln Sie, ob eine Bezeichnungsspalte vorhanden ist. Der Name oder Datentyp der Spalte ist unerheblich, solange die Spalte ein einzelnes Ergebnis (oder eine abhängige Variable) enthält, das Sie vorherzusagen versuchen. Wenn Sie sich nicht sicher sind, welche Spalte die Bezeichnung aufweist, suchen Sie nach einem generischen Namen wie *Class* (Klasse) oder *Target* (Ziel). 
+  Wenn das Dataset keine Bezeichnungsspalte enthält, ist es möglich, dass die Bezeichnungsspalte explizit oder versehentlich an einer Upstream-Position entfernt wurde. Es ist auch möglich, dass das Dataset nicht die Ausgabe eines Upstream-Bewertungsmoduls ist.
+ Um die Spalte explizit als Bezeichnungsspalte zu markieren, fügen Sie das Modul [Edit Metadata](edit-metadata.md) (Metadaten bearbeiten) hinzu und verbinden Sie das Dataset. Wählen Sie nur die Bezeichnungsspalte und anschließend **Label** (Bezeichnung) aus der Dropdownliste **Fields** (Felder) aus. 
+ Wenn die falsche Spalte als Bezeichnung ausgewählt wurde, können Sie **Clear label** (Bezeichnung löschen) aus **Fields** (Felder) auswählen, um die Metadaten in der Spalte zu korrigieren. 
  
|Ausnahmemeldungen|
|------------------------|
|Es befindet sich keine Bezeichnungsspalte im Dataset.|
|Es befindet sich keine Bezeichnungsspalte in "{dataset_name}".|


## <a name="error-0025"></a>Fehler 0025  
 Eine Ausnahme tritt auf, wenn das Dataset keine Bewertungsspalte enthält.  

 Dieser Fehler in Azure Machine Learning tritt auf, wenn die Eingabe für das Auswertungsmodell keine gültigen Bewertungsspalten enthält. Der Benutzer versucht z. B., ein Dataset auszuwerten, bevor es mit einem ordnungsgemäß trainierten Modell bewertet wurde, oder die Bewertungsspalte wurde explizit an einer Upstream-Position gelöscht. Diese Ausnahme tritt auch auf, wenn die Bewertungsspalten der beiden Datasets nicht kompatibel sind. Sie könnten z. B. versuchen, die Genauigkeit eines linearen Regressors mit der eines binären Klassifizierers zu vergleichen.  

**Lösung:** Rufen Sie die Eingabe für das Bewertungsmodell erneut auf und prüfen Sie, ob sie eine oder mehrere Bewertungsspalten enthalten. Andernfalls wurde das Dataset nicht bewertet oder die Bewertungsspalten wurden in einem Upstream-Modul gelöscht.  

|Ausnahmemeldungen|
|------------------------|
|Es befindet sich keine Bewertungsspalte im Dataset.|
|Es befindet sich keine Bewertungsspalte in „{dataset_name}“.|
|Es gibt keine Bewertungsspalte in „{dataset_name}“, die von „{learner_type}“ erzeugt wurde. Bewerten Sie das Dataset mit dem richtigen Learnertyp.|


## <a name="error-0026"></a>Fehler 0026  
 Eine Ausnahme tritt auf, wenn Spalten mit demselben Namen nicht zulässig sind.  

 Dieser Fehler in Azure Machine Learning tritt auf, wenn mehrere Spalten denselben Namen aufweisen. Sie können diesen Fehler z. B. erhalten, wenn das Dataset keine Kopfzeile hat und die Spaltennamen automatisch vergeben werden: Col0, Col1, usw.  

**Lösung:** Wenn Spalten denselben Namen aufweisen, fügen Sie ein [Edit Metadata](edit-metadata.md)-Modul (Metadaten bearbeiten) zwischen dem Eingabedataset und dem Modul ein. Verwenden Sie den Spaltenselektor in [Edit Metadata](edit-metadata.md) (Metadaten bearbeiten), um die umzubenennenden Spalten auszuwählen, indem Sie die neuen Namen in das Textfeld **New column names** (Neue Spaltennamen) eingeben.  

|Ausnahmemeldungen|
|------------------------|
|Gleiche Spaltennamen werden in Argumenten angegeben. Gleiche Spaltennamen sind im Modul nicht zulässig.|
|Gleiche Spaltennamen in den Argumenten „{arg_name_1}“ und „{arg_name_2}“ sind nicht zulässig. Geben Sie unterschiedliche Namen an.|


## <a name="error-0027"></a>Fehler 0027  
 Eine Ausnahme tritt auf, wenn zwei Objekte dieselbe Größe aufweisen müssen, dies aber nicht der Fall ist.  

 Dies ist ein allgemeiner Fehler in Azure Machine Learning, der durch viele Bedingungen verursacht werden kann.  

**Lösung:** Es gibt keine bestimmte Lösung. Sie können jedoch auf Bedingungen wie die folgenden prüfen:  

-   Wenn Sie Spalten umbenennen, stellen Sie sicher, dass jede Liste (die Eingabespalten und die Liste der neuen Namen) dieselbe Anzahl von Einträgen enthält.  
  
-   Wenn Sie zwei Datasets verknüpfen oder verketten, stellen Sie sicher, dass sie dasselbe Schema aufweisen.  
  
-   Wenn Sie zwei Datasets mit mehreren Spalten verknüpfen, stellen Sie sicher, dass die Schlüsselspalten denselben Datentyp aufweisen, und wählen Sie die Option **Allow duplicates and preserve column order in selection** (Duplikate zulassen und Spaltenreihenfolge in der Auswahl beibehalten) aus.  

|Ausnahmemeldungen|
|------------------------|
|Die Größe der übergebenen Objekte ist inkonsistent.|
|Die Größe von „{friendly_name1}“ ist inkonsistent mit der Größe von „{friendly_name2}“.|


## <a name="error-0028"></a>Fehler 0028  
 Eine Ausnahme tritt auf, wenn der Spaltensatz doppelte Spaltennamen enthält und dies nicht zulässig ist.  

 Dieser Fehler in Azure Machine Learning tritt auf, wenn Spaltennamen dupliziert werden, d. h. nicht eindeutig sind.  

**Lösung:** Wenn eine Spalte denselben Namen hat, fügen Sie eine Instanz von [Edit Metadata](edit-metadata.md) (Metadaten bearbeiten) zwischen dem Eingabedataset und dem Modul hinzu, das den Fehler auslöst. Verwenden Sie den Spaltenselektor in [Edit Metadata](edit-metadata.md) (Metadaten bearbeiten), um die umzubenennenden Spalten auszuwählen, und geben Sie die neuen Spaltennamen in das Textfeld **New column names** (Neue Spaltennamen) ein. Wenn Sie mehrere Spalten umbenennen, stellen Sie sicher, dass die Werte, die Sie in **New column names** (Neue Spaltennamen) eingeben, eindeutig sind.  

|Ausnahmemeldungen|
|------------------------|
|Der Spaltensatz enthält doppelte Spaltennamen.|
|The name "{duplicated_name}" is duplicated.|
|The name "{duplicated_name}" is duplicated in "{arg_name}".|
|The name "{duplicated_name}" is duplicated. Details: {details}|


## <a name="error-0029"></a>Fehler 0029  
 Eine Ausnahme tritt auf, wenn eine ungültige URI übergeben wird.  

 Dieser Fehler in Azure Machine Learning tritt auf, wenn ein ungültiger URI übergeben wird.  Sie erhalten diesen Fehler, wenn eine der folgenden Bedingungen erfüllt ist:  

-   Der für Azure Blob Storage zum Lesen oder Schreiben bereitgestellte Public- oder SAS-URI enthält einen Fehler.  
  
-   Das Zeitfenster für die SAS ist abgelaufen.  
  
-   Die Web-URL via HTTP-Quelle stellt eine Datei oder einen Loopback-URI dar.  
  
-   Die Web-URL via HTTP enthält eine falsch formatierte URL.  
  
-   Die URL kann von der Remotequelle nicht aufgelöst werden.  

**Lösung:** Rufen Sie das Modul erneut auf, und überprüfen Sie das Format des URI. Wenn die Datenquelle eine Web-URL via HTTP ist, stellen Sie sicher, dass es sich bei der vorgesehenen Quelle nicht um eine Datei oder einen Loopback-URI (localhost) handelt.  

|Ausnahmemeldungen|
|------------------------|
|Es wird ein ungültiger URI übergeben.|
|Der URI „{invalid_url}“ ist ungültig.|


## <a name="error-0030"></a>Fehler 0030  
 Eine Ausnahme tritt auf, wenn eine Datei nicht heruntergeladen werden konnte.  

 Diese Ausnahme in Azure Machine Learning tritt auf, wenn eine Datei nicht heruntergeladen werden konnte. Sie erhalten diese Ausnahme, wenn beim Leseversuch von einer HTTP-Quelle nach drei (3) Wiederholungsversuchen ein Fehler aufgetreten ist.  

**Lösung:** Überprüfen Sie, ob der URI zur HTTP-Quelle richtig ist und ob derzeit über das Internet auf die Website zugegriffen werden kann.  

|Ausnahmemeldungen|
|------------------------|
|Eine Datei konnte nicht heruntergeladen werden.|
|Fehler beim Herunterladen der Datei: {file_url}.|


## <a name="error-0031"></a>Fehler 0031  
 Eine Ausnahme tritt auf, wenn die Anzahl der Spalten im Spaltensatz kleiner als erforderlich ist.  

 Dieser Fehler in Azure Machine Learning tritt auf, wenn die Anzahl der ausgewählten Spalten kleiner als erforderlich ist.  Sie erhalten diesen Fehler, wenn nicht die minimal erforderliche Anzahl von Spalten ausgewählt ist.  

**Lösung:** Fügen Sie der Spaltenauswahl zusätzliche Spalten hinzu, indem Sie den **Spaltenselektor** verwenden.  

|Ausnahmemeldungen|
|------------------------|
|Die Anzahl der Spalten im Spaltensatz ist kleiner als erforderlich.|
|Für das Eingabeargument „{arg_name}“ sollten mindestens {required_columns_count} Spalte(n) angegeben werden.|
|Für das Eingabeargument „{arg_name}“ sollten mindestens {required_columns_count} Spalte(n) angegeben werden. Die tatsächliche Anzahl der angegebenen Spalten ist {input_columns_count}.|


## <a name="error-0032"></a>Fehler 0032  
 Eine Ausnahme tritt auf, wenn das Argument keine Zahl ist.  

 Sie erhalten diesen Fehler in Azure Machine Learning, wenn das Argument ein Double- oder NaN-Wert ist.  

**Lösung:** Ändern Sie das angegebene Argument, um einen gültigen Wert zu verwenden.  

|Ausnahmemeldungen|
|------------------------|
|Das Argument ist keine Zahl.|
|„{arg_name}“ ist keine Zahl.|


## <a name="error-0033"></a>Fehler 0033  
 Eine Ausnahme tritt auf, wenn das Argument „Infinity“ (Unendlich) ist.  

 Dieser Fehler in Azure Machine Learning tritt auf, wenn das Argument ein unendlicher Wert ist. Sie erhalten diesen Fehler, wenn das Argument entweder `double.NegativeInfinity` oder `double.PositiveInfinity` ist.  

**Lösung:** Ändern Sie das angegebene Argument, um einen gültigen Wert zu erhalten.  

|Ausnahmemeldungen|
|------------------------|
|Das Argument muss endlich sein.|
|„{arg_name}“ ist nicht endlich.|
|Die Spalte „{column_name}“ enthält unendliche Werte.|


## <a name="error-0034"></a>Fehler 0034  
 Eine Ausnahme tritt auf, wenn mehr als eine Bewertung für ein bestimmtes Benutzer-Element-Paar vorhanden ist.  

 Dieser Fehler in Azure Machine Learning tritt bei der Empfehlung auf, wenn ein Benutzer-Element-Paar mehr als einen Bewertungswert aufweist.  

**Lösung:** Stellen Sie sicher, dass das Benutzer-Element-Paar nur einen Bewertungswert besitzt.  

|Ausnahmemeldungen|
|------------------------|
|Für die Werte im Dataset sind mehrere Bewertungen vorhanden.|
|More than one rating for user {user} and item {item} in rating prediction data table.|
|More than one rating for user {user} and item {item} in {dataset}.|


## <a name="error-0035"></a>Fehler 0035  
 Eine Ausnahme tritt auf, wenn für einen bestimmten Benutzer oder ein bestimmtes Element keine Features bereitgestellt wurden.  

 Dieser Fehler in Azure Machine Learning tritt auf, wenn Sie versuchen, ein Empfehlungsmodell für die Bewertung zu verwenden, aber kein Featurevektor gefunden werden konnte.  

**Lösung:**

Der Matchbox Recommender hat bestimmte Anforderungen, die erfüllt werden müssen, wenn Sie entweder Element- oder Benutzerfeatures verwenden.  Dieser Fehler zeigt an, dass für einen von Ihnen als Eingabe bereitgestellten Benutzer oder für ein Element ein Featurevektor fehlt. Stellen Sie sicher, dass für jeden Benutzer oder jedes Element ein Vektor von Features in den Daten vorhanden ist.  

 Wenn Sie z. B. ein Empfehlungsmodell mit Features wie Alter, Standort oder Einkommen des Benutzers trainiert haben, jetzt aber Ergebnisse für neue Benutzer erstellen möchten, die während des Trainings nicht angezeigt wurden, müssen Sie einige gleichwertige Features (d. h. Werte für Alter, Standort und Einkommen) für die neuen Benutzer bereitstellen, um entsprechende Vorhersagen für sie treffen zu können. 

 Wenn Sie für diese Benutzer über keine Features verfügen, sollten Sie die Featureentwicklung in Betracht ziehen, um entsprechende Features zu generieren.  Wenn Sie z. B. nicht über individuelle Alters- oder Einkommenswerte verfügen, können Sie ungefähre Werte generieren, die Sie für eine Gruppe von Benutzern verwenden können. 

<!--When you are scoring from a recommendation mode, you can use item or user features only if you previously used item or user features during training. For more information, see [Score Matchbox Recommender](score-matchbox-recommender.md).

For general information about how the Matchbox recommendation algorithm works, and how to prepare a dataset of item features or user features, see [Train Matchbox Recommender](train-matchbox-recommender.md).  -->

 > [!TIP]
 > Ist die Lösung nicht auf Ihren Fall anwendbar? Sie können jederzeit Feedback zu diesem Artikel senden und Informationen zu diesem Szenario bereitstellen, einschließlich des Moduls und der Anzahl der Zeilen in der Spalte. Wir werden diese Informationen verwenden, um in Zukunft ausführlichere Schritte zur Problembehandlung bereitzustellen.

|Ausnahmemeldungen|
|------------------------|
|Für einen erforderlichen Benutzer oder ein erforderliches Element wurden keine Features bereitgestellt.|
|Für {required_feature_name} sind Features erforderlich, aber sie wurden nicht bereitgestellt.|


## <a name="error-0036"></a>Fehler 0036  
 Eine Ausnahme tritt auf, wenn mehrere Featurevektoren für einen bestimmten Benutzer oder ein bestimmtes Element bereitgestellt wurden.  

 Dieser Fehler in Azure Machine Learning tritt auf, wenn ein Featurevektor mehrfach definiert wurde.  

**Lösung:** Stellen Sie sicher, dass der Featurevektor nicht mehrfach definiert ist.  

|Ausnahmemeldungen|
|------------------------|
|Doppelte Featuredefinition für einen Benutzer oder ein Element.|


## <a name="error-0037"></a>Fehler 0037  
 Eine Ausnahme tritt auf, wenn mehrere Bezeichnungsspalten angegeben sind und nur eine einzelne Spalte zulässig ist.  

 Dieser Fehler in Azure Machine Learning tritt auf, wenn mehr als eine Spalte als neue Bezeichnungsspalte ausgewählt ist. Die meisten überwachten Lernalgorithmen erfordern, dass eine einzelne Spalte als Ziel oder Bezeichnung markiert wird.  

**Lösung:** Stellen Sie sicher, dass Sie eine einzelne Spalte als neue Bezeichnungsspalte auswählen.  

|Ausnahmemeldungen|
|------------------------|
|Es wurden mehrere Bezeichnungsspalten angegeben.|
|Es wurden mehrere Bezeichnungsspalten in „{dataset_name}“ angegeben.|


## <a name="error-0039"></a>Fehler 0039  
 Eine Ausnahme tritt auf, wenn bei einer Operation ein Fehler aufgetreten ist.  

 Dieser Fehler in Azure Machine Learning tritt auf, wenn eine interne Operation nicht abgeschlossen werden kann.  

**Lösung:** Dieser Fehler wird durch viele Bedingungen verursacht und es gibt keine bestimmte Lösung.  
 Die folgende Tabelle enthält allgemeine Meldungen zu diesem Fehler, auf die eine bestimmte Beschreibung der Bedingung folgt. 

 Wenn keine Details verfügbar sind, besuchen Sie die [Microsoft-Seite mit Fragen und Antworten zum Senden von Feedback](/answers/topics/azure-machine-learning-studio-classic.html), und stellen Sie Informationen zu den Modulen, die den Fehler ausgelöst haben, und zu den entsprechenden Bedingungen bereit.

|Ausnahmemeldungen|
|------------------------|
|Fehler bei dem Vorgang.|
|Error while completing operation: "{failed_operation}".|
|Error while completing operation: "{failed_operation}". Reason: "{reason}".|


## <a name="error-0042"></a>Fehler 0042  
 Eine Ausnahme tritt auf, wenn es nicht möglich ist, eine Spalte in einen anderen Typ zu konvertieren.  

 Dieser Fehler in Azure Machine Learning tritt auf, wenn es nicht möglich ist, eine Spalte in den angegebenen Typ zu konvertieren.  Sie erhalten diesen Fehler, wenn ein Modul einen bestimmten Datentyp erfordert, z. B. Datum, Text, Gleitkommazahl oder ganze Zahl, aber es nicht möglich ist, eine vorhandene Spalte in den erforderlichen Typ zu konvertieren.  

Sie können z. B. eine Spalte auswählen und versuchen, sie in einen numerischen Datentyp für die Verwendung in einer mathematischen Operation zu konvertieren, und dann diesen Fehler erhalten, wenn die Spalte ungültige Daten enthält. 

Ein weiterer Grund für diesen Fehler ist der Versuch, eine Spalte mit Gleitkommazahlen oder vielen eindeutigen Werten als kategorische Spalte zu verwenden. 

**Lösung:**

+ Öffnen Sie die Hilfeseite für das Modul, das den Fehler ausgelöst hat, und überprüfen Sie die Anforderungen an den Datentyp.
+ Überprüfen Sie die Datentypen der Spalten im Eingabedataset.
+ Überprüfen Sie Daten, die aus sogenannten schemalosen Datenquellen stammen.
+ Überprüfen Sie das Dataset auf fehlende Werte oder Sonderzeichen, die die Konvertierung in den gewünschten Datentyp verhindern könnten. 
    + Numerische Datentypen sollten konsistent sein: Überprüfen Sie z. B. eine Spalte mit ganzen Zahlen auf Gleitkommazahlen.
    + Suchen Sie in einer Spalte mit Zahlen nach Textzeichenfolgen oder NA-Werten. 
    + Boolesche Werte können je nach erforderlichem Datentyp in eine geeignete Darstellung konvertiert werden.
    + Untersuchen Sie Textspalten auf Nicht-Unicode-Zeichen, Tabulatorzeichen oder Steuerzeichen.
    + DateTime-Daten müssen konsistent sein, um Modellierungsfehler zu vermeiden, aber die Bereinigung kann sich aufgrund der zahlreichen Formate komplex gestalten. Mögliche Verwendung von: <!--the [Execute R Script](execute-r-script.md) or -->Führen Sie [Python-Skript](execute-python-script.md)-Module aus, um die Bereinigung durchzuführen.  
+ Ändern Sie bei Bedarf die Werte im Eingabedataset, damit die Spalte erfolgreich konvertiert werden kann. Die Änderung kann Quantisierungs-, Kürzungs- oder Rundungsoperationen, die Eliminierung von Ausreißern oder die Imputation fehlender Werte umfassen. In den folgenden Artikeln finden Sie einige häufige Szenarien zur Datentransformation beim maschinellen Lernen:
    + [Bereinigen fehlender Daten](clean-missing-data.md)
    + [Normalisieren von Daten](normalize-data.md)
<!--+ [Clip Values](clip-values.md) 
    + [Group Data Into Bins](group-data-into-bins.md)
  -->

> [!TIP]
> Ist die Lösung nicht eindeutig oder nicht auf Ihren Fall anwendbar? Sie können jederzeit Feedback zu diesem Artikel senden und Informationen zu diesem Szenario bereitstellen, einschließlich des Moduls und des Datentyps der Spalte. Wir werden diese Informationen verwenden, um in Zukunft ausführlichere Schritte zur Problembehandlung bereitzustellen.  

|Ausnahmemeldungen|
|------------------------|
|Nicht zulässige Konvertierung.|
|Es konnte die Spalte vom Typ {type1} nicht in die Spalte vom Typ {type2} konvertiert werden.|
|Es konnte die Spalte „{col_name1}“ vom Typ {type1} nicht in die Spalte vom Typ {type2} konvertiert werden.|
|Es konnte die Spalte „{col_name1}“ vom Typ {type1} nicht in die Spalte „{col_name2}“ vom Typ {type2} konvertiert werden.|


## <a name="error-0044"></a>Fehler 0044  
 Eine Ausnahme tritt auf, wenn es nicht möglich ist, den Elementtyp der Spalte aus den vorhandenen Werten abzuleiten.  

 Dieser Fehler in Azure Machine Learning tritt auf, wenn es nicht möglich ist, den Typ einer Spalte oder von Spalten in einem Dataset abzuleiten. Dies ist in der Regel der Fall, wenn zwei oder mehr Datasets mit unterschiedlichen Elementtypen verkettet werden. Wenn Azure Machine Learning den gemeinsamen Typ nicht bestimmen kann, der alle Werte in einer oder mehreren Spalten ohne Informationsverlust darstellen kann, wird dieser Fehler generiert.  

**Lösung:** Stellen Sie sicher, dass alle Werte in einer bestimmten Spalte in beiden zu kombinierenden Datasets entweder vom gleichen Typ sind (numerisch, boolesch, kategorisch, Zeichenfolge, Datum usw.) oder in denselben Typ umgewandelt werden können.  

|Ausnahmemeldungen|
|------------------------|
|Der Elementtyp der Spalte kann nicht abgeleitet werden.|
|Der Elementtyp für die Spalte „{column_name}“ kann nicht abgeleitet werden – alle Elemente sind Nullverweise.|
|Der Elementtyp für die Spalte „{column_name}“ von Dataset „{dataset_name}“ kann nicht abgeleitet werden – alle Elemente sind Nullverweise.|


## <a name="error-0045"></a>Fehler 0045  
 Eine Ausnahme tritt auf, wenn es aufgrund von gemischten Elementtypen in der Quelle nicht möglich ist, eine Spalte zu erstellen.  

 Dieser Fehler in Azure Machine Learning tritt auf, wenn die Elementtypen der beiden zu kombinierenden Datasets verschieden sind.  

**Lösung:** Stellen Sie sicher, dass alle Werte in einer bestimmten Spalte in beiden zu kombinierenden Datasets vom gleichen Typ sind (numerisch, boolesch, kategorisch, Zeichenfolge, Datum usw.).  

|Ausnahmemeldungen|
|------------------------|
|Es kann keine Spalte mit gemischten Elementtypen erstellt werden.|
|Es kann keine Spalte mit der ID „{column_id}“ aus gemischten Elementtypen erstellt werden:<br />Der Typ von data[{row_1}, {column_id}] ist „{type_1}“. <br />Type of data[{row_2}, {column_id}] is "{type_2}".|
|Es kann keine Spalte mit der ID „{column_id}“ aus gemischten Elementtypen erstellt werden:<br />Der Typ in Chunk {chunk_id_1} ist „{type_1}“. <br />Type in chunk {chunk_id_2} is "{type_2}" with chunk size: {chunk_size}.|


## <a name="error-0046"></a>Fehler 0046  
 Eine Ausnahme tritt auf, wenn in einem bestimmten Pfad kein Verzeichnis erstellt werden kann.  

 Dieser Fehler in Azure Machine Learning tritt auf, wenn in einem bestimmten Pfad kein Verzeichnis erstellt werden kann. Sie erhalten diesen Fehler, wenn ein Teil des Pfads zum Ausgabeverzeichnis für eine Hive-Abfrage falsch oder nicht zugänglich ist.  

**Lösung:** Rufen Sie das Modul erneut auf und überprüfen Sie, ob der Verzeichnispfad ordnungsgemäß formatiert ist und ob auf ihn mit den aktuellen Anmeldeinformationen zugegriffen werden kann.  

|Ausnahmemeldungen|
|------------------------|
|Geben Sie ein gültiges Ausgabeverzeichnis an.|
|Verzeichnis: {path} kann nicht erstellt werden. Geben Sie einen gültigen Pfad an.|


## <a name="error-0047"></a>Fehler 0047  
 Eine Ausnahme tritt auf, wenn die Anzahl der Featurespalten in einigen der an das Modul übergebenen Datasets zu klein ist.  

 Dieser Fehler in Azure Machine Learning tritt auf, wenn das Eingabedataset für das Training nicht die vom Algorithmus geforderte Mindestanzahl von Spalten enthält. Normalerweise ist entweder das Dataset leer oder enthält nur Trainingsspalten.  

**Lösung:** Rufen Sie das Eingabedataset erneut auf, um sicherzustellen, dass es neben der Bezeichnungsspalte eine oder mehrere zusätzliche Spalten enthält.  

|Ausnahmemeldungen|
|------------------------|
|Die Anzahl der Featurespalten im Eingabedataset ist kleiner als der zulässige Mindestwert.|
|Number of feature columns in input dataset is less than allowed minimum of {required_columns_count} column(s).|
|Number of feature columns in input dataset "{arg_name}" is less than allowed minimum of {required_columns_count} column(s).|


## <a name="error-0048"></a>Fehler 0048  
 Eine Ausnahme tritt auf, wenn eine Datei nicht geöffnet werden konnte.  

 Dieser Fehler in Azure Machine Learning tritt auf, wenn es nicht möglich ist, eine Datei zum Lesen oder Schreiben zu öffnen. Dieser Fehler kann aus folgenden Gründen angezeigt werden:  

-   Der Container oder die Datei (Blob) ist nicht vorhanden.  
  
-   Die Zugriffsebene der Datei oder des Containers gestattet Ihnen keinen Zugriff auf die Datei.  
  
-   Die Datei ist zu groß zum Lesen oder weist ein falsches Format auf.  

**Lösung:** Rufen Sie das Modul und die zu lesende Datei erneut auf.  

 Überprüfen Sie, ob die Namen von Container und Datei richtig sind.  

 Verwenden Sie das klassische Azure-Portal oder ein Azure-Speichertool, um sicherzustellen, dass Sie über die Berechtigung zum Zugriff auf die Datei verfügen.  

  <!--If you are trying to read an image file, make sure that it meets the requirements for image files in terms of size, number of pixels, and so forth. For more information, see [Import Images](import-images.md).  -->

|Ausnahmemeldungen|
|------------------------|
|Eine Datei kann nicht geöffnet werden.|
|Fehler beim Öffnen der Datei: {file_name}.|
|Fehler beim Öffnen der Datei: {file_name}. Speicherausnahmemeldung: {exception}.|


## <a name="error-0049"></a>Fehler 0049  
 Eine Ausnahme tritt auf, wenn eine Datei nicht analysiert werden konnte.  

 Dieser Fehler in Azure Machine Learning tritt auf, wenn eine Datei nicht analysiert werden konnte. Sie erhalten diesen Fehler, wenn das im Modul [Import Data](import-data.md) (Daten importieren) ausgewählte Dateiformat nicht mit dem tatsächlichen Format der Datei übereinstimmt oder die Datei ein nicht erkennbares Zeichen enthält.  

**Lösung:** Rufen Sie das Modul erneut auf, und korrigieren Sie die Auswahl des Dateiformats, wenn es nicht mit dem Format der Datei übereinstimmt. Überprüfen Sie nach Möglichkeit die Datei, um sicherzustellen, dass sie keine unzulässigen Zeichen enthält.  

|Ausnahmemeldungen|
|------------------------|
|Eine Datei konnte nicht analysiert werden.|
|Error while parsing the {file_format} file.|
|Error while parsing the {file_format} file: {file_name}.|
|Error while parsing the {file_format} file. Reason: {failure_reason}.|
|Error while parsing the {file_format} file: {file_name}. Reason: {failure_reason}.|


## <a name="error-0052"></a>Fehler 0052  
 Eine Ausnahme tritt auf, wenn der Schlüssel des Azure-Speicherkontos falsch angegeben wurde.  

 Dieser Fehler in Azure Machine Learning tritt auf, wenn der Schlüssel für den Zugriff auf das Azure-Speicherkonto falsch ist. Dieser Fehler kann z. B. auftreten, wenn der Azure-Speicherschlüssel beim Kopieren und Einfügen abgeschnitten oder der falsche Schlüssel verwendet wurde.  

 Weitere Informationen zum Abrufen des Schlüssels für ein Azure-Speicherkonto finden Sie unter [Anzeigen, Kopieren und erneutes Generieren von Speicherzugriffsschlüsseln](../../storage/common/storage-account-create.md).  

**Lösung:** Rufen Sie das Modul erneut auf und überprüfen Sie, ob der Azure-Speicherschlüssel für das Konto richtig ist. Kopieren Sie den Schlüssel bei Bedarf erneut aus dem klassischen Azure-Portal.  

|Ausnahmemeldungen|
|------------------------|
|Der Schlüssel des Azure-Speicherkontos ist falsch.|


## <a name="error-0053"></a>Fehler 0053  
 Eine Ausnahme tritt in dem Fall auf, wenn für Matchbox-Empfehlungen keine Benutzerfeatures oder Elemente vorhanden sind.  

 Dieser Fehler in Azure Machine Learning tritt auf, wenn ein Featurevektor nicht gefunden werden konnte.  

**Lösung:** Stellen Sie sicher, dass im Eingabedataset ein Featurevektor vorhanden ist.  

|Ausnahmemeldungen|
|------------------------|
|Benutzerfeatures oder/und Elemente sind erforderlich, werden aber nicht bereitgestellt.|


## <a name="error-0056"></a>Fehler 0056  
 Eine Ausnahme tritt auf, wenn die von Ihnen für eine Operation ausgewählten Spalten gegen die Anforderungen verstoßen.  

 Dieser Fehler in Azure Machine Learning tritt auf, wenn Sie Spalten für eine Operation auswählen, bei der die Spalte einen bestimmten Datentyp aufweisen muss. 

 Dieser Fehler kann auch auftreten, wenn die Spalte den richtigen Datentyp besitzt, aber das von Ihnen verwendete Modul erfordert, dass die Spalte auch als Feature, Bezeichnung oder kategorische Spalte gekennzeichnet ist.  

  <!--For example, the [Convert to Indicator Values](convert-to-indicator-values.md) module requires that columns be categorical, and will raise this error if you select a feature column or label column.  -->

**Lösung:**

1.  Überprüfen Sie den Datentyp der aktuell ausgewählten Spalten. 

2. Ermitteln Sie, ob es sich bei den ausgewählten Spalten um kategorische, Bezeichnungs- oder Featurespalten handelt.  
  
3.  Lesen Sie das Hilfethema zu dem Modul, in dem Sie die Spaltenauswahl vorgenommen haben, um festzustellen, ob besondere Anforderungen an den Datentyp oder die Spaltennutzung gestellt werden.  
  
3.  Verwenden Sie [Edit Metadata](edit-metadata.md) (Metadaten bearbeiten), um den Spaltentyp für die Dauer dieser Operation zu ändern. Stellen Sie sicher, dass Sie den Spaltentyp wieder auf seinen ursprünglichen Wert zurücksetzen und eine andere Instanz von [Edit Metadata](edit-metadata.md) (Metadaten bearbeiten) verwenden, wenn Sie ihn für Downstream-Operationen benötigen.  

|Ausnahmemeldungen|
|------------------------|
|Mindestens eine ausgewählte Spalte hat sich nicht in einer zulässigen Kategorie befunden.|
|Die Spalte namens „{col_name}“ befindet sich nicht in einer zulässigen Kategorie.|


## <a name="error-0057"></a>Fehler 0057  
 Eine Ausnahme tritt bei dem Versuch auf, eine bereits vorhandene Datei oder ein bereits vorhandenes Blob zu erstellen.  

 Diese Ausnahme tritt auf, wenn Sie das Modul [Export Data](export-data.md) (Daten exportieren) oder ein anderes Modul verwenden, um die Ergebnisse einer Pipeline in Azure Machine Learning in Azure-Blobspeicher zu speichern, dabei aber versuchen, eine bereits vorhandene Datei oder ein bereits vorhandenes Blob zu erstellen.   

**Lösung:**

 Sie erhalten diesen Fehler nur, wenn Sie zuvor die Eigenschaft **Azure blob storage write mode** (Schreibmodus für Azure-Blobspeicher) auf **Error** (Fehler) festgelegt haben. Dieses Modul löst standardmäßig einen Fehler aus, wenn Sie versuchen, ein Dataset in ein bereits bestehendes Blob zu schreiben.

 - Öffnen Sie die Moduleigenschaften, und ändern Sie die Eigenschaft **Azure blob storage write mode** (Schreibmodus für Azure-Blobspeicher) in **Overwrite** (Überschreiben).
 - Alternativ können Sie auch den Namen eines anderen Zielblobs oder einer anderen Datei eingeben und ein Blob angeben, das nicht bereits vorhanden ist.  

|Ausnahmemeldungen|
|------------------------|
|Datei oder Blob ist bereits vorhanden.|
|Datei oder Blob „{file_path}“ ist bereits vorhanden.|


## <a name="error-0058"></a>Fehler 0058  
 Dieser Fehler in Azure Machine Learning tritt auf, wenn das Dataset nicht die erwartete Bezeichnungsspalte enthält.  

 Diese Ausnahme kann auch auftreten, wenn die angegebene Bezeichnungsspalte nicht mit den vom Learner erwarteten Daten oder Datentyp übereinstimmt oder die falschen Werte aufweist. Diese Ausnahme wird z. B. erzeugt, wenn beim Training eines binären Klassifikators eine Bezeichnungsspalte mit realem Wert verwendet wird.  

**Lösung:** Die Lösung hängt vom verwendeten Learner oder Trainer und den Datentypen der Spalten in Ihrem Dataset ab. Überprüfen Sie zunächst die Anforderungen an den maschinellen Lernalgorithmus oder das Trainingsmodul.  

 Rufen Sie das Eingabedataset erneut auf. Überprüfen Sie, ob die Spalte, von der Sie erwarten, dass sie als Bezeichnung behandelt wird, den richtigen Datentyp für das von Ihnen zu erstellende Modell aufweist.  

 Überprüfen Sie die Eingaben auf fehlende Werte und beseitigen oder ersetzen Sie diese gegebenenfalls.  

 Fügen Sie bei Bedarf das Modul [Edit Metadata](edit-metadata.md) (Metadaten bearbeiten) hinzu und stellen Sie sicher, dass die Bezeichnungsspalte als Bezeichnung gekennzeichnet ist.  

|Ausnahmemeldungen|
|------------------------|
|The label column values and scored label column values are not comparable.|
|The label column is not as expected in "{dataset_name}".|
|The label column is not as expected in "{dataset_name}", {reason}.|
|The label column "{column_name}" is not expected in "{dataset_name}".|
|The label column "{column_name}" is not expected in "{dataset_name}", {reason}.|


## <a name="error-0059"></a>Fehler 0059  
 Eine Ausnahme tritt auf, wenn ein in einer Spaltenauswahl angegebener Spaltenindex nicht analysiert werden kann.  

 Dieser Fehler in Azure Machine Learning tritt auf, wenn ein bei der Verwendung der Spaltenauswahl angegebener Spaltenindex nicht analysiert werden kann.  Sie erhalten diesen Fehler, wenn der Spaltenindex in einem ungültigen Format vorliegt, das nicht analysiert werden kann.  

**Lösung:** Ändern Sie den Spaltenindex, um einen gültigen Indexwert zu verwenden.  

|Ausnahmemeldungen|
|------------------------|
|Mindestens ein angegebener Spaltenindex oder Indexbereich konnte nicht analysiert werden.|
|Der Spaltenindex oder der Bereich „{column_index_or_range}“ konnte nicht analysiert werden.|


## <a name="error-0060"></a>Fehler 0060  
 Eine Ausnahme tritt auf, wenn in einer Spaltenauswahl ein Spaltenbereich außerhalb des Bereichs angegeben ist.  

 Dieser Fehler in Azure Machine Learning tritt auf, wenn in der Spaltenauswahl ein Spaltenbereich außerhalb des Bereichs angegeben ist. Sie erhalten diesen Fehler, wenn der Spaltenbereich in der Spaltenauswahl nicht mit den Spalten im Dataset übereinstimmt.  

**Lösung:** Ändern Sie den Spaltenbereich in der Spaltenauswahl so, dass er den Spalten im Dataset entspricht.  

|Ausnahmemeldungen|
|------------------------|
|Es wurde ein ungültiger oder außerhalb des Bereichs liegender Spaltenindexbereich angegeben.|
|Der Spaltenbereich „{column_range}“ ist ungültig oder außerhalb des Bereichs.|


## <a name="error-0061"></a>Fehler 0061  
 Eine Ausnahme tritt bei dem Versuch auf, eine Zeile zu einer DataTable hinzuzufügen, die eine andere Anzahl von Spalten als die Tabelle aufweist.  

 Dieser Fehler in Azure Machine Learning tritt bei dem Versuch auf, eine Zeile zu einem Dataset hinzuzufügen, das eine andere Anzahl von Spalten als das Dataset aufweist.  Sie erhalten diesen Fehler, wenn die Zeile, die dem Dataset hinzugefügt wird, eine andere Anzahl von Spalten verwendet als das Eingabedataset.  Die Zeile kann nicht an das Dataset angefügt werden, wenn die Anzahl der Spalten verschieden ist.  

**Lösung:** Ändern Sie das Eingabedataset so, dass es die gleiche Anzahl von Spalten aufweist wie die hinzugefügte Zeile, oder ändern Sie die hinzugefügte Zeile so, dass sie dieselbe Anzahl von Spalten verwendet wie das Dataset.  

|Ausnahmemeldungen|
|------------------------|
|Alle Tabellen müssen dieselbe Anzahl von Spalten aufweisen.|
|Columns in chunk "{chunk_id_1}" is different with chunk "{chunk_id_2}" with chunk size: {chunk_size}.|
|Column count in file "{filename_1}" (count={column_count_1}) is different with file "{filename_2}" (count={column_count_2}).|


## <a name="error-0062"></a>Fehler 0062  
 Eine Ausnahme tritt bei dem Versuch auf, zwei Modelle mit unterschiedlichen Learnertypen zu vergleichen.  

 Dieser Fehler in Azure Machine Learning tritt auf, wenn Auswertungsmetriken für zwei verschiedene bewertete Datasets nicht verglichen werden können. In diesem Fall ist es nicht möglich, die Effektivität der Modelle zu vergleichen, die zur Erstellung der beiden bewerteten Datasets verwendet werden.  

**Lösung:** Überprüfen Sie, ob die Ergebnisse von derselben Art von maschinellem Lernmodell stammen (binäre Klassifizierung, Regression, Klassifizierung mit mehreren Klassen, Empfehlung, Clustering, Anomalieerkennung usw.). Alle von Ihnen zu vergleichenden Modelle müssen denselben Learnertyp aufweisen.  

|Ausnahmemeldungen|
|------------------------|
|Alle Modelle müssen denselben Learnertyp aufweisen.|
|Got incompatible learner type: "{actual_learner_type}". Expected learner types are: "{expected_learner_type_list}".|


## <a name="error-0064"></a>Fehler 0064  
 Eine Ausnahme tritt auf, wenn der Name oder Speicherschlüssel des Azure-Speicherkontos falsch angegeben wurde.  

 Dieser Fehler in Azure Machine Learning tritt auf, wenn der Name oder Speicherschlüssel des Azure-Speicherkontos falsch angegeben wurde. Sie erhalten diesen Fehler, wenn Sie einen falschen Kontonamen oder ein falsches Kennwort für das Speicherkonto eingeben. Dies kann vorkommen, wenn Sie den Kontonamen oder das Kennwort manuell eingeben. Der Fehler kann auch auftreten, wenn das Konto gelöscht wurde.  

**Lösung:** Überprüfen Sie, ob der Kontoname und das Kennwort ordnungsgemäß eingegeben wurden und ob das Konto vorhanden ist.  

|Ausnahmemeldungen|
|------------------------|
|Der Name oder Speicherschlüssel des Azure-Speicherkontos ist falsch.|
|Der Azure-Speicherkontoname „{account_name}“ oder der Speicherschlüssel für den Kontonamen ist falsch.|


## <a name="error-0065"></a>Fehler 0065  
 Eine Ausnahme tritt auf, wenn der Azure-Blobname nicht ordnungsgemäß angegeben wurde.  

 Dieser Fehler in Azure Machine Learning tritt auf, wenn der Name des Azure-Blobs falsch angegeben wurde.  Sie erhalten den Fehler in den folgenden Situationen:  

-   Das Blob kann im angegebenen Container nicht gefunden werden.  

 <!---   The fully qualified name of the blob specified for output in one of the [Learning with Counts](data-transformation-learning-with-counts.md) modules is greater than 512 characters.  -->

-   Nur der Container wurde als Quelle in einer [Import Data](import-data.md)-Anforderung (Daten importieren) angegeben, wenn das Format Excel oder CSV mit Codierung verwendet wurde. Die Verkettung des Inhalts aller Blobs innerhalb eines Containers ist mit diesen Formaten nicht zulässig.  
  
-   Ein SAS-URI enthält nicht den Namen eines gültigen Blobs.  

**Lösung:** Rufen Sie das Modul erneut auf, das die Ausnahme auslöst. Überprüfen Sie, ob das angegebene Blob im Container im Speicherkonto vorhanden ist und ob Sie mit den Berechtigungen das Blob anzeigen können. Überprüfen Sie, ob die Eingabe der Form **Containername/Dateiname** entspricht, wenn Sie Excel oder CSV mit Codierungsformaten verwenden. Überprüfen Sie, ob ein SAS-URI den Namen eines gültigen Blobs enthält.  

|Ausnahmemeldungen|
|------------------------|
|The Azure storage blob name is incorrect.|
|The Azure storage blob name "{blob_name}" is incorrect.|
|The Azure storage blob name with prefix "{blob_name_prefix}" does not exist.|
|Failed to find any Azure storage blobs under container "{container_name}".|
|Failed to find any Azure storage blobs with wildcard path "{blob_wildcard_path}".|


## <a name="error-0066"></a>Fehler 0066  
 Eine Ausnahme tritt auf, wenn eine Ressource nicht in ein Azure-Blob hochgeladen werden konnte.  

 Dieser Fehler in Azure Machine Learning tritt auf, wenn eine Ressource nicht in ein Azure-Blob hochgeladen werden konnte.  <!--You will receive this message if [Train Vowpal Wabbit 7-4 Model](train-vowpal-wabbit-version-7-4-model.md) encounters an error attempting to save either the model or the hash created when training the model.--> Beide werden in demselben Azure-Speicherkonto gespeichert wie das Konto, das die Eingabedatei enthält.  

**Lösung:** Rufen Sie das Modul erneut auf. Überprüfen Sie, ob Azure-Kontoname, Speicherschlüssel und Container richtig sind und ob das Konto über die Berechtigung verfügt, in den Container zu schreiben.  

|Ausnahmemeldungen|
|------------------------|
|Die Ressource konnte nicht in den Azure-Speicher hochgeladen werden.|
|Die Datei „{source_path}“ konnte nicht als „{dest_path}“ in den Azure-Speicher hochgeladen werden.|


## <a name="error-0067"></a>Fehler 0067  
 Eine Ausnahme tritt auf, wenn ein Dataset eine andere Spaltenanzahl aufweist als erwartet.  

 Dieser Fehler in Azure Machine Learning tritt auf, wenn ein Dataset eine andere Spaltenanzahl aufweist als erwartet.  Sie erhalten diesen Fehler, wenn die Anzahl der Spalten im Dataset von der Anzahl der Spalten abweicht, die das Modul während der Ausführung erwartet.  

**Lösung:** Ändern Sie das Eingabedataset oder die Parameter.  

|Ausnahmemeldungen|
|------------------------|
|Unerwartete Anzahl von Spalten in der Datentabelle.|
|Unexpected number of columns in the dataset "{dataset_name}".|
|Expected "{expected_column_count}" column(s) but found "{actual_column_count}" column(s) instead.|
|In input dataset "{dataset_name}", expected "{expected_column_count}" column(s) but found "{actual_column_count}" column(s) instead.|


## <a name="error-0068"></a>Fehler 0068  
 Eine Ausnahme tritt auf, wenn das angegebene Hive-Skript nicht richtig ist.  

 Dieser Fehler in Azure Machine Learning tritt auf, wenn Syntaxfehler in einem Hive-QL-Skript aufgetreten sind, oder wenn der Hive-Interpreter bei der Ausführung der Abfrage oder des Skripts einen Fehler erkennt.  

**Lösung:**

Die Fehlermeldung von Hive wird normalerweise im Fehlerprotokoll gemeldet, sodass Sie aufgrund des bestimmten Fehlers Maßnahmen ergreifen können. 

+ Öffnen Sie das Modul, und überprüfen Sie die Abfrage auf Fehler.  
+ Überprüfen Sie, ob die Abfrage außerhalb von Azure Machine Learning ordnungsgemäß funktioniert, indem Sie sich an der Hive-Konsole Ihres Hadoop-Clusters anmelden und die Abfrage ausführen.  
+ Versuchen Sie, Kommentare in Ihrem Hive-Skript in einer separaten Zeile zu platzieren, anstatt ausführbare Anweisungen und Kommentare in einer einzelnen Zeile zu kombinieren.  

### <a name="resources"></a>Ressourcen

In den folgenden Artikeln finden Sie Hilfe bei Hive-Abfragen für maschinelles Lernen:

+ [Erstellen von Hive-Tabellen und Laden von Daten aus Azure Blob Storage](../team-data-science-process/move-hive-tables.md)
+ [Durchsuchen von Daten in Tabellen mithilfe von Hive-Abfragen](../team-data-science-process/explore-data-hive-tables.md)
+ [Erstellen von Features für Daten in einem Hadoop-Cluster mit Hive-Abfragen](../team-data-science-process/create-features-hive.md)
+ [Cheat Sheet für Hive für SQL-Benutzer (PDF)](http://hortonworks.com/wp-content/uploads/2013/05/hql_cheat_sheet.pdf)

  
|Ausnahmemeldungen|
|------------------------|
|Das Hive-Skript ist falsch.|


## <a name="error-0069"></a>Fehler 0069  
 Eine Ausnahme tritt auf, wenn das angegebene SQL-Skript nicht richtig ist.  

 Dieser Fehler in Azure Machine Learning tritt auf, wenn das angegebene SQL-Skript Syntaxprobleme aufweist oder wenn die im Skript angegebenen Spalten oder Tabellen nicht gültig sind. 

 Sie erhalten diesen Fehler, wenn die SQL-Engine beim Ausführen der Abfrage oder des Skripts einen Fehler erkennt. Die SQL-Fehlermeldung wird normalerweise im Fehlerprotokoll gemeldet, sodass Sie aufgrund des bestimmten Fehlers Maßnahmen ergreifen können.  

**Lösung:** Rufen Sie das Modul erneut auf, und überprüfen Sie die SQL-Abfrage auf Fehler.  

 Stellen Sie sicher, dass die Abfrage außerhalb von Azure ML ordnungsgemäß funktioniert, indem Sie sich direkt am Datenbankserver anmelden und die Abfrage ausführen.  

 Wenn eine von der Modulausnahme gemeldete SQL generierte Nachricht vorliegt, ergreifen Sie aufgrund des gemeldeten Fehlers die entsprechenden Maßnahmen. Die Fehlermeldungen umfassen z. B. gelegentlich eine bestimmte Anleitung für den wahrscheinlichen Fehler:
+ *No such column or missing database* (Keine derartige Spalte vorhanden oder fehlende Datenbank) weist darauf hin, dass Sie möglicherweise einen Spaltennamen falsch eingegeben haben. Wenn Sie sicher sind, dass der Spaltenname richtig ist, versuchen Sie, den Spaltenbezeichner in Klammern oder Anführungszeichen einzuschließen.
+ *SQL logic error near \<SQL keyword\>* (SQL-Logikfehler in der Nähe von SQL-Schlüsselwort) weist darauf hin, dass möglicherweise ein Syntaxfehler vor dem angegebenen Schlüsselwort vorliegt.

  
|Ausnahmemeldungen|
|------------------------|
|Das SQL-Skript ist falsch.|
|Die SQL-Abfrage „{sql_query}“ ist falsch.|
|Die SQL-Abfrage „{sql_query}“ ist falsch. Ausnahmemeldung: {exception}.|


## <a name="error-0070"></a>Fehler 0070  
 Eine Ausnahme tritt bei dem Versuch auf, auf eine nicht vorhandene Azure-Tabelle zuzugreifen.  

 Dieser Fehler in Azure Machine Learning tritt bei dem Versuch auf, auf eine nicht vorhandene Azure-Tabelle zuzugreifen. Sie erhalten diesen Fehler, wenn Sie eine Tabelle im Azure-Speicher angeben, die beim Lesen von oder Schreiben in den Azure-Tabellenspeicher nicht vorhanden ist. Dies kann vorkommen, wenn Sie sich bei der Eingabe des Namens der gewünschten Tabelle vertippen oder ein Konflikt zwischen Zielname und Speichertyp vorliegt. Sie wollten z. B. aus einer Tabelle lesen, haben aber stattdessen einen Blobnamen eingegeben.  

**Lösung:** Rufen Sie das Modul erneut auf, um sicherzustellen, dass der Name der Tabelle richtig ist.  

|Ausnahmemeldungen|
|------------------------|
|Die Azure-Tabelle ist nicht vorhanden.|
|Die Azure-Tabelle „{table_name}“ ist nicht vorhanden.|


## <a name="error-0072"></a>Fehler 0072  
 Eine Ausnahme tritt bei einem Verbindungstimeout auf.  

 Dieser Fehler in Azure Machine Learning tritt auf, wenn bei einer Verbindung ein Timeout auftritt. Sie erhalten diesen Fehler, wenn derzeit Verbindungsprobleme mit der Datenquelle oder dem Ziel vorliegen, z. B. eine langsame Internetverbindung, oder wenn das Dataset groß ist und/oder die zu lesende SQL-Abfrage komplizierte Verarbeitungsvorgänge durchführt.  

**Lösung:** Ermitteln Sie, ob derzeit Probleme mit langsamen Verbindungen zum Azure-Speicher oder zum Internet vorliegen.  

|Ausnahmemeldungen|
|------------------------|
|Es ist ein Verbindungstimeout aufgetreten.|


## <a name="error-0073"></a>Fehler 0073  
 Eine Ausnahme tritt auf, wenn bei der Konvertierung einer Spalte in einen anderen Typ ein Fehler auftritt.  

 Dieser Fehler in Azure Machine Learning tritt auf, wenn es nicht möglich ist, eine Spalte in einen anderen Typ zu konvertieren.  Sie erhalten diesen Fehler, wenn ein Modul einen bestimmten Typ erfordert und es nicht möglich ist, die Spalte in den neuen Typ zu konvertieren.  

**Lösung:** Ändern Sie das Eingabedataset so, dass die Spalte aufgrund der inneren Ausnahme konvertiert werden kann.  

|Ausnahmemeldungen|
|------------------------|
|Fehler beim Konvertieren der Spalte.|
|Fehler beim Konvertieren der Spalte in {target_type}.|


## <a name="error-0075"></a>Fehler 0075  
Eine Ausnahme tritt auf, wenn bei der Quantisierung eines Datasets eine ungültige Quantisierungsfunktion verwendet wird.  

Dieser Fehler in Azure Machine Learning tritt bei dem Versuch auf, Daten mit einer nicht unterstützten Methode zu quantisieren, oder wenn die Parameterkombinationen ungültig sind.  

**Lösung:**

Die Fehlerbehandlung für dieses Ereignis wurde in einer früheren Version von Azure Machine Learning eingeführt, die eine umfassendere Anpassung der Quantisierungsmethoden ermöglichte. Derzeit basieren alle Quantisierungsmethoden auf einer Auswahl aus einer Dropdownliste, sodass es technisch nicht mehr möglich sein sollte, diesen Fehler zu erhalten.

 <!--If you get this error when using the [Group Data into Bins](group-data-into-bins.md) module, consider reporting the issue in the [Microsoft Q&A question page for Azure Machine Learning](/answers/topics/azure-machine-learning-studio-classic.html), providing the data types, parameter settings, and the exact error message.  -->

|Ausnahmemeldungen|
|------------------------|
|Es wurde eine ungültige Quantisierungsfunktion verwendet.|


## <a name="error-0077"></a>Fehler 0077  
 Eine Ausnahme tritt auf, wenn ein unbekannter Schreibmodus der Blobdatei übergeben wird.  

 Dieser Fehler in Azure Machine Learning tritt auf, wenn in den Spezifikationen für ein Blobdateiziel oder eine Quelle ein ungültiges Argument übergeben wird.  

**Lösung:** In fast allen Modulen, die Daten in Azure-Blobspeicher importieren oder aus diesem exportieren, werden Parameterwerte, die den Schreibmodus steuern, über eine Dropdownliste zugewiesen. Daher ist es nicht möglich, einen ungültigen Wert zu übergeben, und dieser Fehler sollte nicht auftreten. Dieser Fehler ist in einem späteren Release veraltet.  

|Ausnahmemeldungen|
|------------------------|
|Unsupported blob write mode.|
|Nicht unterstützter Blobschreibmodus: {blob_write_mode}.|


## <a name="error-0078"></a>Fehler 0078  
 Eine Ausnahme tritt auf, wenn die HTTP-Option für [Import Data](import-data.md) (Daten importieren) einen 3xx-Statuscode erhält, der eine Umleitung anzeigt.  

 Dieser Fehler in Azure Machine Learning tritt auf, wenn die HTTP-Option für [Import Data](import-data.md) einen 3xx-Statuscode (301, 302, 304, usw.) erhält, der eine Umleitung anzeigt. Sie erhalten diesen Fehler bei dem Versuch, eine Verbindung zu einer HTTP-Quelle herzustellen, die den Browser zu einer anderen Seite umleitet. Aus Sicherheitsgründen sind Umleitungen von Websites als Datenquellen für Azure Machine Learning nicht zulässig.  

**Lösung:** Wenn es sich bei der Website um eine vertrauenswürdige Website handelt, geben Sie die umgeleitete URL direkt ein.  

|Ausnahmemeldungen|
|------------------------|
|Die HTTP-Umleitung ist nicht zulässig.|


## <a name="error-0079"></a>Fehler 0079  
 Eine Ausnahme tritt auf, wenn der Name des Azure-Speichercontainers falsch angegeben wurde.  

 Dieser Fehler in Azure Machine Learning tritt auf, wenn der Name des Azure-Speichercontainers falsch angegeben wurde. Sie erhalten diesen Fehler, wenn Sie beim Schreiben in Azure Blob Storage nicht sowohl den Container als auch den Blob(datei)namen mit der Option **Path to blob beginning with container** (Pfad zum Blob beginnt mit Container) angegeben haben.  

**Lösung:** Rufen Sie das Modul [Export Data](export-data.md) (Daten exportieren) erneut auf und überprüfen Sie, ob der angegebene Pfad zum Blob sowohl den Container als auch den Dateinamen im Format **Container/Dateiname** enthält.  

|Ausnahmemeldungen|
|------------------------|
|Der Azure-Speichercontainername ist falsch.|
|Der Azure-Speichercontainername „{container_name}“ ist falsch. Es wurde ein Containername im Format „Container/Blob“ erwartet.|


## <a name="error-0080"></a>Fehler 0080  
 Eine Ausnahme tritt auf, wenn die Spalte mit allen fehlenden Werten vom Modul nicht zulässig ist.  

 Dieser Fehler in Azure Machine Learning tritt auf, wenn mindestens eine der vom Modul verarbeiteten Spalten alle fehlenden Werte enthält. Wenn ein Modul z. B. Aggregatstatistiken für die einzelnen Spalten berechnet, kann es nicht mit einer Spalte arbeiten, die keine Daten enthält. In solchen Fällen wird die Modulausführung mit dieser Ausnahme angehalten.  

**Lösung:** Rufen Sie das Eingabedataset erneut auf, und entfernen Sie alle Spalten, die alle fehlenden Werte enthalten.  

|Ausnahmemeldungen|
|------------------------|
|Spalten, denen alle Werte fehlen, sind nicht zulässig.|
|In der Spalte {col_index_or_name} fehlen alle Werte.|


## <a name="error-0081"></a>Fehler 0081  
 Eine Ausnahme tritt im PCA-Modul auf, wenn die Anzahl der zu verringernden Dimensionen gleich der Anzahl der Featurespalten im Eingabedataset ist, die mindestens eine Sparse-Featurespalte enthalten.  

 Dieser Fehler in Azure Machine Learning tritt auf, wenn die folgenden Bedingungen erfüllt sind: (a) das Eingabedataset weist mindestens eine Sparsespalte auf und (b) die endgültige Anzahl der angeforderten Dimensionen ist gleich der Anzahl der eingegebenen Dimensionen.  

**Lösung:** Erwägen Sie, die Anzahl der Dimensionen in der Ausgabe auf einen Wert zu reduzieren, der kleiner ist als die Anzahl der Dimensionen in der Eingabe. Dies ist typisch in Anwendungen von PCA.   <!--For more information, see [Principal Component Analysis](principal-component-analysis.md).  -->

|Ausnahmemeldungen|
|------------------------|
|Für Datasets, die Sparse-Featurespalten enthalten, muss die Anzahl der Dimensionen, auf die reduziert wird, kleiner sein als die Anzahl der Featurespalten.|


## <a name="error-0082"></a>Fehler 0082  
 Eine Ausnahme tritt auf, wenn ein Modell nicht erfolgreich deserialisiert werden kann.  

 Dieser Fehler in Azure Machine Learning tritt auf, wenn ein gespeichertes maschinelles Lernmodell oder eine gespeicherte Transformation aufgrund einer fehlerhaften Änderung nicht von einer neueren Version der Azure Machine Learning Runtime geladen werden kann.  

**Lösung:** Die Trainingspipeline, die das Modell oder die Transformation erzeugt hat, muss erneut ausgeführt und das Modell oder die Transformation erneut gespeichert werden.  

|Ausnahmemeldungen|
|------------------------|
|Das Modell konnte nicht deserialisiert werden, da es wahrscheinlich mit einem älteren Serialisierungsformat serialisiert wurde. Trainieren und speichern Sie das Modell erneut.|


## <a name="error-0083"></a>Fehler 0083  
 Eine Ausnahme tritt auf, wenn das für das Training verwendete Dataset nicht für einen konkreten Learnertyp verwendet werden kann.  

 Dieser Fehler in Azure Machine Learning tritt auf, wenn das Dataset mit dem zu trainierenden Learner nicht kompatibel ist. Das Dataset kann z. B. mindestens einen fehlenden Wert in den einzelnen Zeilen enthalten, sodass das gesamte Dataset beim Training übersprungen wird. In anderen Fällen erwarten einige Algorithmen des maschinellen Lernens, z. B. die Anomalieerkennung, dass keine Bezeichnungen vorhanden sind. Daher können sie diese Ausnahme auslösen, wenn Bezeichnungen im Dataset vorhanden sind.  

**Lösung:** Ziehen Sie die Dokumentation des Learners zu Rate, der zum Überprüfen der Anforderungen für das Eingabedataset verwendet wird. Überprüfen Sie die Spalten, um sicherzustellen, dass alle erforderlichen Spalten vorhanden sind.  

|Ausnahmemeldungen|
|------------------------|
|Das für das Training verwendete Dataset ist ungültig.|
|{data_name} enthält ungültige Daten für das Training.|
|{data_name} enthält ungültige Daten für das Training. Learnertyp: {learner_type}.|
|{data_name} enthält ungültige Daten für das Training. Learnertyp: {learner_type}. Reason: {Grund}.|
|Fehler beim Anwenden der Aktion „{action_name}“ auf Trainingsdaten {data_name}. Reason: {Grund}.|


## <a name="error-0084"></a>Fehler 0084  
 Eine Ausnahme tritt auf, wenn die von einem R-Skript erzeugten Bewertungen ausgewertet werden. Dies wird derzeit nicht unterstützt.  

 Dieser Fehler in Azure Machine Learning tritt bei dem Versuch auf, eines der Module zum Auswerten eines Modells mit der Ausgabe eines R-Skripts zu verwenden, das Bewertungen enthält.  

**Lösung:**

|Ausnahmemeldungen|
|------------------------|
|Evaluating scores produced by Custom Model is currently unsupported.|


## <a name="error-0085"></a>Fehler 0085  
 Eine Ausnahme tritt auf, wenn bei der Skriptauswertung ein Fehler auftritt.  

 Dieser Fehler in Azure Machine Learning tritt auf, wenn Sie ein benutzerdefiniertes Skript ausführen, das Syntaxfehler enthält.  

**Lösung:** Öffnen Sie Ihren Code in einem externen Editor, und prüfen Sie ihn auf Fehler.  

|Ausnahmemeldungen|
|------------------------|
|Fehler bei der Auswertung des Skripts.|
|Der folgende Fehler ist während der Skriptauswertung aufgetreten. Weitere Informationen finden Sie im Ausgabeprotokoll:<br />---------- Start der Fehlermeldung vom {script_language}-Interpreter ----------<br />{message}<br />---------- Ende der Fehlermeldung vom {script_language}-Interpreter ----------|


## <a name="error-0090"></a>Fehler 0090  
 Eine Ausnahme tritt auf, wenn bei der Erstellung der Hive-Tabelle ein Fehler auftritt.  

 Dieser Fehler in Azure Machine Learning tritt auf, wenn Sie [Export Data](export-data.md) (Daten exportieren) oder eine andere Option zum Speichern von Daten in einem HDInsight-Cluster verwenden und die angegebene Hive-Tabelle nicht erstellt werden kann.  

**Lösung:** Überprüfen Sie den dem Cluster zugeordneten Azure-Speicherkontonamen und stellen Sie sicher, dass Sie dasselbe Konto in den Moduleigenschaften verwenden.  

|Ausnahmemeldungen|
|------------------------|
|Die Hive-Tabelle konnte nicht erstellt werden. Stellen Sie für einen HDInsight-Cluster sicher, dass der dem Cluster zugeordnete Azure-Speicherkontoname mit dem identisch ist, der über den Modulparameter übergeben wird.|
|Die Hive-Tabelle „{table_name}“ konnte nicht erstellt werden. Stellen Sie für einen HDInsight-Cluster sicher, dass der dem Cluster zugeordnete Azure-Speicherkontoname mit dem identisch ist, der über den Modulparameter übergeben wird.|
|Die Hive-Tabelle „{table_name}“ konnte nicht erstellt werden. Stellen Sie für einen HDInsight-Cluster sicher, dass der dem Cluster zugeordnete Azure-Speicherkontoname „{cluster_name}“ lautet.|


## <a name="error-0102"></a>Fehler 0102  
 Wird ausgelöst, wenn eine ZIP-Datei nicht extrahiert werden kann.  

 Dieser Fehler in Azure Machine Learning tritt auf, wenn Sie ein gezipptes Paket mit der Erweiterung ZIP importieren, aber das Paket entweder keine ZIP-Datei ist, oder die Datei kein unterstütztes ZIP-Format verwendet.  

**Lösung:** Stellen Sie sicher, dass es sich bei der ausgewählten Datei um eine gültige ZIP-Datei handelt und dass sie mit einem der unterstützten Komprimierungsalgorithmen komprimiert wurde.  

 Wenn Sie beim Importieren von Datasets in komprimiertem Format diesen Fehler erhalten, überprüfen Sie, ob alle enthaltenen Dateien eines der unterstützten Dateiformate verwenden und im Unicode-Format vorliegen.  <!--For more information, see [Unpack Zipped Datasets](unpack-zipped-datasets.md).  -->

 Versuchen Sie, die gewünschten Dateien in einen neuen komprimierten gezippten Ordner zu lesen und versuchen Sie, das benutzerdefinierte Modul erneut hinzuzufügen.  

|Ausnahmemeldungen|
|------------------------|
|Die angegebene ZIP-Datei weist nicht das richtige Format auf.|


## <a name="error-0105"></a>Fehler 0105  
 Dieser Fehler wird angezeigt, wenn eine Moduldefinitionsdatei einen nicht unterstützten Parametertyp enthält.  
  
 Dieser Fehler in Azure Machine Learning tritt auf, wenn Sie eine XML-Definition für ein benutzerdefiniertes Modul erstellen und der Typ eines Parameters oder Arguments in der Definition nicht mit einem unterstützten Typ übereinstimmt.  
  
**Lösung:** Stellen Sie sicher, dass die Typeigenschaft eines beliebigen **Arg**-Elements in der XML-Definitionsdatei für das benutzerdefinierte Modul ein unterstützter Typ ist.  
  
|Ausnahmemeldungen|  
|------------------------|  
|Nicht unterstützter Parametertyp.|  
|Es wurde der nicht unterstützte Parametertyp „{0}“ angegeben.|  


## <a name="error-0107"></a>Fehler 0107  
 Wird ausgelöst, wenn eine Moduldefinitionsdatei einen nicht unterstützten Ausgabetyp definiert.  
  
 Dieser Fehler in Azure Machine Learning tritt auf, wenn der Typ eines Ausgangsports in einer XML-Definition eines benutzerdefinierten Moduls nicht mit einem unterstützten Typ übereinstimmt.  
  
**Lösung:** Stellen Sie sicher, dass die Typeigenschaft eines Ausgabeelements in der XML-Definitionsdatei des benutzerdefinierten Moduls ein unterstützter Typ ist.  
  
|Ausnahmemeldungen|  
|------------------------|  
|Nicht unterstützter Ausgabetyp.|  
|Es wurde ein nicht unterstützter Ausgabetyp „{output_type}“ angegeben.|  


## <a name="error-0125"></a>Fehler 0125  
 Wird ausgelöst, wenn das Schema für mehrere Datasets nicht übereinstimmt.  

**Lösung:**

|Ausnahmemeldungen|
|------------------------|
|Das Datasetschema stimmt nicht überein.|


## <a name="error-0127"></a>Fehler 0127  
 Die Bildpixelgröße überschreitet den zulässigen Grenzwert.  

 Dieser Fehler tritt auf, wenn Sie Bilder aus einem Bilddataset zur Klassifizierung lesen und die Bilder größer sind, als das Modell verarbeiten kann.  

 <!--**Resolution:**
 For more information about the image size and other requirements, see these topics:  

-   [Import Images](import-images.md)  
  
-   [Pretrained Cascade Image Classification](pretrained-cascade-image-classification.md)  -->

|Ausnahmemeldungen|
|------------------------|
|Die Bildpixelgröße überschreitet den zulässigen Grenzwert.|
|Die Bildpixelgröße in der Datei „{file_path}“ überschreitet den zulässigen Grenzwert: {size_limit}.|


## <a name="error-0128"></a>Fehler 0128  
 Die Anzahl der bedingten Wahrscheinlichkeiten für kategorische Spalten überschreitet den Grenzwert.  

**Lösung:**

|Ausnahmemeldungen|
|------------------------|
|Die Anzahl der bedingten Wahrscheinlichkeiten für kategorische Spalten überschreitet den Grenzwert.|
|Die Anzahl der bedingten Wahrscheinlichkeiten für kategorische Spalten überschreitet den Grenzwert. Die Spalten „{column_name_or_index_1}“ und „{column_name_or_index_2}“ sind das problembehaftete Paar.|


## <a name="error-0129"></a>Fehler 0129  
 Die Anzahl der Spalten im Dataset überschreitet den zulässigen Grenzwert.  

**Lösung:**

|Ausnahmemeldungen|
|------------------------|
|Die Anzahl der Spalten im Dataset überschreitet den zulässigen Grenzwert.|
|Number of columns in the dataset in '{dataset_name}' exceeds allowed.|
|Number of columns in the dataset in '{dataset_name}' exceeds allowed limit of '{component_name}'.|
|Number of columns in the dataset in '{dataset_name}' exceeds allowed '{limit_columns_count}' limit of '{component_name}'.|


## <a name="error-0134"></a>Fehler 0134
Eine Ausnahme tritt auf, wenn die Bezeichnungsspalte fehlt oder die Anzahl der bezeichneten Zeilen nicht ausreicht.  

Dieser Fehler tritt auf, wenn das Modul eine Bezeichnungsspalte erfordert, Sie aber keine in die Spaltenauswahl einbezogen haben, oder wenn der Bezeichnungsspalte zu viele Werte fehlen.

Dieser Fehler kann auch auftreten, wenn eine vorhergehende Operation das Dataset so ändert, dass für eine Downstream-Operation nicht ausreichend Zeilen zur Verfügung stehen. Nehmen Sie z. B. an, Sie verwenden einen Ausdruck im Modul **Partition and Sample** (Partition und Beispiel), um ein Dataset durch Werte zu teilen. Wenn für Ihren Ausdruck keine Übereinstimmungen gefunden werden, ist eines der Datasets, die sich aus der Partition ergeben, leer.

Lösung: 

 Wenn Sie eine Bezeichnungsspalte in die Spaltenauswahl aufnehmen, diese aber nicht erkannt wird, verwenden Sie das Modul [Edit Metadata](edit-metadata.md), um sie als Bezeichnungsspalte zu kennzeichnen.

  <!--Use the [Summarize Data](summarize-data.md) module to generate a report that shows how many values are missing in each column. -->
  Anschließend können Sie das Modul [Clean Missing Data](clean-missing-data.md) (Fehlende Daten bereinigen) verwenden, um Zeilen mit fehlenden Werten in der Bezeichnungsspalte zu entfernen. 

 Überprüfen Sie Ihre Eingabedatasets, um sicherzustellen, dass sie gültige Daten und ausreichend Zeilen enthalten, um die Anforderungen der Operation zu erfüllen. Viele Algorithmen erzeugen eine Fehlermeldung, wenn sie eine Mindestanzahl von Datenzeilen benötigen, aber die Daten nur wenige Zeilen oder nur eine Kopfzeile enthalten.

|Ausnahmemeldungen|
|------------------------|
|Eine Ausnahme tritt auf, wenn die Bezeichnungsspalte fehlt oder die Anzahl der bezeichneten Zeilen nicht ausreicht.|
|Eine Ausnahme tritt auf, wenn die Bezeichnungsspalte fehlt oder weniger als {required_rows_count} bezeichnete Zeilen aufweist.|
|Eine Ausnahme tritt auf, wenn die Bezeichnungsspalte in Dataset {dataset_name} fehlt oder weniger als {required_rows_count} bezeichnete Zeilen aufweist.|


## <a name="error-0138"></a>Fehler 0138  
 Der Arbeitsspeicher ist ausgeschöpft, die Ausführung des Moduls kann nicht abgeschlossen werden. Das Downsampling des Datasets kann dabei helfen, das Problem zu beheben.  

 Dieser Fehler tritt auf, wenn das ausgeführte Modul mehr Arbeitsspeicher erfordert, als im Azure-Container verfügbar ist. Dies kann vorkommen, wenn Sie mit einem großen Dataset arbeiten und die aktuelle Operation nicht in den Arbeitsspeicher passt.  

**Lösung:** Wenn Sie versuchen, ein großes Dataset zu lesen und die Operation nicht abgeschlossen werden kann, ist es möglicherweise hilfreich, ein Downsampling für das Dataset durchzuführen.  

  <!--If you use the visualizations on datasets to check the cardinality of columns, only some rows are sampled. To get a full report, use [Summarize Data](summarize-data.md). You can also use the [Apply SQL Transformation](apply-sql-transformation.md) to check for the number of unique values in each column.  

 Sometimes transient loads can lead to this error. Machine support also changes over time. 

 Try using [Principal Component Analysis](principal-component-analysis.md) or one of the provided feature selection methods to reduce your dataset to a smaller set of more feature-rich columns: [Feature Selection](feature-selection-modules.md)  -->

|Ausnahmemeldungen|
|------------------------|
|Der Arbeitsspeicher ist ausgeschöpft, die Ausführung des Moduls kann nicht abgeschlossen werden.|
|Der Arbeitsspeicher ist ausgeschöpft, die Ausführung des Moduls kann nicht abgeschlossen werden. Details: {details}|


## <a name="error-0141"></a>Fehler 0141  
 Eine Ausnahme tritt auf, wenn die Anzahl der ausgewählten numerischen Spalten und eindeutigen Werte in den kategorischen und Zeichenfolgenspalten zu klein ist.  

 Dieser Fehler in Azure Machine Learning tritt auf, wenn nicht ausreichend eindeutige Werte in der ausgewählten Spalte vorhanden sind, um die Operation durchzuführen.  

**Lösung:** Einige Operationen führen statistische Vorgänge für Feature- und kategorische Spalten durch, und wenn nicht genügend Werte vorhanden sind, kann bei der Operation ein Fehler auftreten oder ein ungültiges Ergebnis ausgegeben werden. Überprüfen Sie Ihr Dataset, um zu ermitteln, wie viele Werte in den Feature- und Bezeichnungsspalten vorhanden sind, und stellen Sie fest, ob die auszuführende Operation statistisch gültig ist.  

 Wenn das Quelldataset gültig ist, können Sie auch überprüfen, ob eine Upstream-Datenmanipulation oder Metadatenoperation die Daten geändert und einige Werte entfernt hat.  

 Wenn Upstream-Operationen die Aufteilung, Stichprobenentnahme oder Wiederholungsprobennahme umfassen, überprüfen Sie, ob die Ausgaben die erwartete Anzahl von Zeilen und Werten enthalten.  

|Ausnahmemeldungen|
|------------------------|
|Die Anzahl der ausgewählten numerischen Spalten und eindeutigen Werte in den kategorischen und Zeichenfolgenspalten ist zu klein.|
|Die Gesamtzahl der ausgewählten numerischen Spalten und eindeutigen Werte in den kategorischen und Zeichenfolgenspalten (aktuell {actual_num}) muss mindestens {lower_boundary} sein.|


## <a name="error-0154"></a>Fehler 0154  
 Eine Ausnahme tritt auf, wenn der Benutzer versucht, Daten über Schlüsselspalten zu verknüpfen, die inkompatible Spaltentypen haben.

|Ausnahmemeldungen|
|------------------------|
|Key column element types are not compatible.|
|Key column element types are not compatible.(left: {keys_left}; right: {keys_right})|


## <a name="error-0155"></a>Fehler 0155  
 Eine Ausnahme tritt auf, wenn Spaltennamen im Dataset keine Zeichenfolgen sind.

|Ausnahmemeldungen|
|------------------------|
|Der Name der Datenrahmenspalte muss vom Typ string sein. Column names are not string.|
|Der Name der Datenrahmenspalte muss vom Typ string sein. Spaltennamen: {column_names} weisen nicht den Typ string auf.|


## <a name="error-0156"></a>Fehler 0156  
 Eine Ausnahme tritt auf, wenn keine Daten aus Azure SQL-Datenbank gelesen werden konnten.

|Ausnahmemeldungen|
|------------------------|
|Failed to read data from Azure SQL Database.|
|Failed to read data from Azure SQL Database: {detailed_message} DB: {database_server_name}:{database_name} Query: {sql_statement}|


## <a name="error-0157"></a>Fehler 0157  
 Der Datenspeicher wurde nicht gefunden.

|Ausnahmemeldungen|
|------------------------|
|Datastore information is invalid.|
|Datastore information is invalid. Failed to get AzureML datastore '{datastore_name}' in workspace '{workspace_name}'.|


## <a name="error-0158"></a>Fehler 0158
 Wird ausgelöst, wenn ein Transformationsverzeichnis ungültig ist.

|Ausnahmemeldungen|
|------------------------------------------------------------|
|Das angegebene TransformationDirectory ist ungültig.|
|TransformationDirectory „{arg_name}“ ist ungültig. Reason: {Grund}. Führen Sie das Trainingsexperiment, das die Transformationsdatei generiert, erneut aus. Wenn das Trainingsexperiment gelöscht wurde, erstellen Sie die Transformationsdatei erneut, und speichern Sie sie.|
|TransformationDirectory „{arg_name}“ ist ungültig. Reason: {Grund}. {troubleshoot_hint}|


## <a name="error-0159"></a>Fehler 0159
 Die Ausnahme tritt auf, wenn das Modulmodellverzeichnis ungültig ist. 

|Ausnahmemeldungen|
|------------------------------------------------------------|
|Das angegebene ModelDirectory ist ungültig.|
|ModelDirectory „{arg_name}“ ist ungültig.|
|ModelDirectory „{arg_name}“ ist ungültig. Reason: {Grund}.|
|ModelDirectory „{arg_name}“ ist ungültig. Reason: {Grund}. {troubleshoot_hint}|


## <a name="error-1000"></a>Fehler 1000  
Interne Bibliotheksausnahme.  

Dieser Fehler wird bereitgestellt, um ansonsten unbehandelte interne Enginefehler zu erfassen. Daher kann die Ursache für diesen Fehler je nach Modul, das den Fehler generiert hat, unterschiedlich sein.  

Um weitere Hilfe zu erhalten, wird empfohlen, die ausführliche Meldung, die dem Fehler zugeordnet ist, zusammen mit einer Beschreibung des Szenarios (einschließlich der als Eingaben verwendeten Daten) an das [Azure Machine Learning-Forum](/answers/topics/azure-machine-learning.html) zu senden. Dieses Feedback hilft uns, Fehler zu priorisieren und die wichtigsten Probleme für die weitere Arbeit zu identifizieren.  

|Ausnahmemeldungen|
|------------------------|
|Bibliotheksausnahme.|
|Bibliotheksausnahme: {exception}.|
|Ausnahme für unbekannte Bibliothek: {exception}. {customer_support_guidance}.|