---
title: Behandeln von Problemen bei Modulfehlern
titleSuffix: Azure Machine Learning service
description: Behandeln von Modulausnahmen in Azure Machine Learning Studio anhand von Fehlercodes
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 09a2b616e2bba93be86241c64d37daec7d6dea3b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65027840"
---
# <a name="exceptions-and-error-codes-for-algorithm--module-reference"></a>Ausnahmen und Fehlercodes für Algorithmen und Module – Referenz

Erfahren Sie mehr über Fehlermeldungen und Ausnahmecodes, die bei der Verwendung von Modulen in Azure Machine Learning Studio auftreten können. 

Um das Problem zu beheben, suchen Sie nach dem Fehler in diesem Artikel, um mehr über häufige Ursachen zu erfahren. Es gibt zwei Möglichkeiten, den vollständigen Text einer Fehlermeldung in Studio abzurufen:  
 
- Klicken Sie im rechten Bereich auf den Link zum **Anzeigen des Ausgabeprotokolls** und scrollen Sie zum Ende. Die ausführliche Fehlermeldung wird in den letzten beiden Zeilen des Fensters angezeigt.  
  
- Wählen Sie das Modul mit dem Fehler aus, und klicken Sie auf das rote X. Es wird nur der relevante Fehlertext angezeigt.  
  
Wenn der Text der Fehlermeldung nicht hilfreich ist, senden Sie uns Informationen zum Kontext und zu allen gewünschten Ergänzungen oder Änderungen. Sie können entweder Feedback zum Fehlerthema übermitteln oder das [Azure Machine Learning STUDIO-Forum](https://aka.ms/aml-forum-studio) besuchen und eine Frage posten.  


## <a name="error-0001"></a>Fehler 0001  
 Eine Ausnahme tritt auf, wenn eine oder mehrere angegebene Spalten des Datasets nicht gefunden werden konnten.  
  
 Sie erhalten diesen Fehler, wenn für ein Modul eine Spaltenauswahl vorgenommen wird, die ausgewählte(n) Spalte(n) jedoch nicht im Eingabedataset vorhanden ist (sind). Dieser Fehler kann auftreten, wenn Sie einen Spaltennamen manuell eingegeben haben oder wenn der Spaltenselektor eine vorgeschlagene Spalte bereitgestellt hat, die bei der Durchführung des Experiments in Ihrem Dataset nicht vorhanden war.  
  
**Lösung:** Rufen Sie das Modul erneut auf, das diese Ausnahme auslöst, und überprüfen Sie, ob die Spaltennamen korrekt und alle referenzierten Spalten vorhanden sind.  
  
|Ausnahmemeldungen|  
|------------------------|  
|Eine oder mehrere angegebene Spalten wurden nicht gefunden|  
|Spalte mit Name oder Index „{0}“ nicht gefunden|  
|Spalte mit Name oder Index „{0}“ ist in „{1}“ nicht vorhanden|  
 

## <a name="error-0002"></a>Fehler 0002  
 Eine Ausnahme tritt auf, wenn mindestens ein Parameter nicht analysiert oder vom angegebenen Typ in den vom Zielmethodentyp erforderlichen Typ konvertiert werden konnte.  
  
 Dieser Fehler tritt in Azure Machine Learning auf, wenn Sie einen Parameter als Eingabe angeben und der Werttyp sich von dem erwarteten Typ unterscheidet und eine implizite Konvertierung nicht durchgeführt werden kann.  
  
**Lösung:** Überprüfen Sie die Anforderungen des Moduls und ermitteln Sie, welcher Werttyp erforderlich ist (string, integer, double usw.).  
  
|Ausnahmemeldungen|  
|------------------------|  
|Fehler beim Analysieren des Parameters|  
|Fehler beim Analysieren des Parameters „{0}“|  
|Fehler beim Analysieren (Konvertieren) des Parameters „{0}“ in „{1}“|  
|Fehler beim Konvertieren des Parameters „{0}“ von „{1}“ in „{2}“|  
|Fehler beim Konvertieren von „{0}“-Parameterwert „{1}“ von „{2}“ in „{3}“|  
|Fehler beim Konvertieren des Werts „{0}“ in der Spalte „{1}“ von „{2}“ in „{3}“ bei Verwendung des bereitgestellten Formats „{4}“|  
  

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
|Mindestens eine Eingabe ist NULL oder leer|  
|Eingabe „{0}“ ist null oder leer|  
  

## <a name="error-0004"></a>Fehler 0004  
 Eine Ausnahme tritt auf, wenn der Parameter kleiner als oder gleich dem bestimmten Wert ist.  
  
 Sie erhalten diesen Fehler in Azure Machine Learning, wenn der Parameter in der Meldung unter einem Grenzwert liegt, der für das Modul zur Verarbeitung der Daten erforderlich ist.  
  
**Lösung:** Rufen Sie das Modul erneut auf, und ändern Sie den Parameter so, dass er größer als der angegebene Wert ist.  
  
|Ausnahmemeldungen|  
|------------------------|  
|Der Parameter muss größer als der Grenzwert sein|  
|Der Wert des Parameters „{0}“ muss größer als {1} sein|  
|Der Parameter „{0}“ besitzt den Wert „{1}“, der größer als {2} sein muss|  
  


## <a name="error-0005"></a>Fehler 0005  
 Eine Ausnahme tritt auf, wenn der Parameter kleiner als ein bestimmter Wert ist.  
  
 Sie erhalten diesen Fehler in Azure Machine Learning, wenn der Parameter in der Meldung kleiner als oder gleich einem Grenzwert ist, der für das Modul zur Verarbeitung der Daten erforderlich ist.  
  
**Lösung:** Rufen Sie das Modul erneut auf, und ändern Sie den Parameter so, dass er größer als oder gleich dem angegebenen Wert ist.  
  
|Ausnahmemeldungen|  
|------------------------|  
|Der Parameter muss größer als oder gleich dem Grenzwert sein|  
|Der Parameterwert „{0}“ muss größer als oder gleich {1} sein|  
|Der Parameter „{0}“ besitzt den Wert „{1}“, der größer als oder gleich {2} sein muss|  
  

## <a name="error-0006"></a>Fehler 0006  
 Eine Ausnahme tritt auf, wenn der Parameter größer als oder gleich dem bestimmten Wert ist.  
  
 Sie erhalten diesen Fehler in Azure Machine Learning, wenn der Parameter in der Meldung größer als oder gleich einem Grenzwert ist, der für das Modul zur Verarbeitung der Daten erforderlich ist.  
  
**Lösung:** Rufen Sie das Modul erneut auf, und ändern Sie den Parameter so, dass er kleiner als der angegebene Wert ist.  
  
|Ausnahmemeldungen|  
|------------------------|  
|Parameterkonflikt Einer der Parameter muss kleiner als ein anderer sein|  
|Der Wert des Parameters „{0}“ muss kleiner sein als der Wert des Parameters „{1}“|  
|Der Parameter „{0}“ besitzt den Wert „{1}“, der kleiner als {2} sein muss|  
  

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
|Der Wert des Parameters „{0}“ muss kleiner als oder gleich dem Wert des Parameters „{1}“ sein|  
|Der Parameter „{0}“ besitzt den Wert „{1}“, der kleiner als oder gleich {2} sein muss|  
  

## <a name="error-0008"></a>Fehler 0008  
 Eine Ausnahme tritt auf, wenn der Parameter nicht im Bereich liegt.  
  
 Sie erhalten diesen Fehler in Azure Machine Learning, wenn der Parameter in der Meldung außerhalb der Grenzen liegt, die für das Modul zur Verarbeitung der Daten erforderlich sind.  
  
 Dieser Fehler wird z. B. angezeigt, wenn Sie versuchen, mit [Add Rows](add-rows.md) (Zeilen hinzufügen) zwei Datasets zu kombinieren, die eine unterschiedliche Anzahl von Spalten aufweisen.  
  
**Lösung:** Rufen Sie das Modul erneut auf, und ändern Sie den Parameter so, dass er innerhalb des angegebenen Bereichs liegt.  
  
|Ausnahmemeldungen|  
|------------------------|  
|Der Parameterwert liegt nicht im angegebenen Bereich|  
|Der Parameterwert „{0}“ liegt nicht im Bereich.|  
|Der Wert des Parameters „{0}“ muss im Bereich [{1}, {2}] liegen|  
  

## <a name="error-0009"></a>Fehler 0009  
 Eine Ausnahme tritt auf, wenn der Name des Azure-Speicherkontos oder der Containername falsch angegeben wurde.  
  
Dieser Fehler tritt in Azure Machine Learning Studio auf, wenn Sie Parameter für ein Azure-Speicherkonto angeben, aber der Name oder das Kennwort nicht aufgelöst werden kann. Fehler bei Kennwörtern oder Kontonamen können viele Ursachen haben:
 
 + Das Konto weist den falschen Typ auf. Einige neue Kontotypen werden für die Verwendung mit Machine Learning Studio nicht unterstützt. Weitere Informationen finden Sie unter [Import Data](import-data.md) (Daten importieren).
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
|Der Name des Azure-Speicherkontos „{0}“oder des Containers „{1}“ ist falsch. Es wird ein Containername im Format „Container/Blob“ erwartet.|  
  

## <a name="error-0010"></a>Fehler 0010  
 Eine Ausnahme tritt auf, wenn Eingabedatasets übereinstimmende Spaltennamen aufweisen sollten, dies aber nicht der Fall ist.  
  
 Sie erhalten diesen Fehler in Azure Machine Learning, wenn der Spaltenindex in der Meldung unterschiedliche Spaltennamen in den beiden Eingabedatasets aufweist.  
  
**Lösung:** Verwenden Sie [Edit Metadata](edit-metadata.md) (Metadaten bearbeiten), oder ändern Sie das ursprüngliche Dataset, um denselben Spaltennamen für den angegebenen Spaltenindex zu erhalten.  
  
|Ausnahmemeldungen|  
|------------------------|  
|Spalten mit entsprechendem Index in Eingabedatasets weisen unterschiedliche Namen auf.|  
|Die Spaltennamen sind für die Spalte {0} (nullbasiert) der Eingabedatasets ({1} und {2}) nicht identisch.|  
  

## <a name="error-0011"></a>Fehler 0011  
 Eine Ausnahme tritt auf, wenn das übergebene Spaltensatzargument nicht für eine der Datasetspalten gilt.  
  
 Sie erhalten diesen Fehler in Azure Machine Learning, wenn die angegebene Spaltenauswahl nicht mit einer der Spalten im angegebenen Dataset übereinstimmt.  
  
 Sie können diesen Fehler auch erhalten, wenn Sie keine Spalte ausgewählt haben und mindestens eine Spalte erforderlich ist, damit das Modul funktioniert.  
  
**Lösung:** Ändern Sie die Spaltenauswahl im Modul so, dass sie auf die Spalten im Dataset angewendet wird.  
  
 Wenn das Modul die Auswahl einer bestimmten Spalte erfordert, z. B. eine Bezeichnungsspalte, vergewissern Sie sich, dass die richtige Spalte ausgewählt ist.  
  
 Wenn nicht geeignete Spalten ausgewählt sind, entfernen Sie diese, und führen Sie das Experiment erneut durch.  
  
|Ausnahmemeldungen|  
|------------------------|  
|Der angegebene Spaltensatz gilt für keine der Datasetspalten.|  
|Der angegebene Spaltensatz „{0}“ gilt für keine der Datasetspalten.|  
  

## <a name="error-0012"></a>Fehler 0012  
 Eine Ausnahme tritt auf, wenn die Instanz der Klasse nicht mit dem übergebenen Satz von Argumenten erstellt werden konnte.  
  
**Lösung:** Dieser Fehler ist für den Benutzer nicht umsetzbar und wird in einem zukünftigen Release veraltet sein.  
  
|Ausnahmemeldungen|  
|------------------------|  
|Nicht trainiertes Modell, zuerst Modell trainieren.|  
|Nicht trainiertes Modell ({0}), trainiertes Modell verwenden.|  
  

## <a name="error-0013"></a>Fehler 0013  
 Eine Ausnahme tritt auf, wenn der an das Modul übergebene Learner ein ungültiger Typ ist.  
  
 Dieser Fehler tritt auf, wenn ein trainiertes Modell mit dem angeschlossenen Bewertungsmodul nicht kompatibel ist. <!--For example, connecting the output of [Train Matchbox Recommender](train-matchbox-recommender.md) to [Score Model](score-model.md) (instead of [Score Matchbox Recommender](score-matchbox-recommender.md)) will generate this error when the experiment is run.  -->
  
**Lösung:**

Bestimmen Sie den Typ des Learner, der vom Trainingsmodul erzeugt wird, und bestimmen Sie das für den Learner geeignete Bewertungsmodul. 

Wenn das Modell mit einem der speziellen Trainingsmodule trainiert wurde, verbinden Sie das trainierte Modell nur mit dem entsprechenden spezialisierten Bewertungsmodul. 


|Modelltyp|Trainingsmodul| Bewertungsmodul|
|----|----|----|
|Alle Klassifizierer|[Train Model](train-model.md) (Modell trainieren) |[Score Model](score-model.md) (Modell bewerten)|
|Alle Regressionsmodelle|[Train Model](train-model.md) (Modell trainieren) |[Score Model](score-model.md) (Modell bewerten)|
<!--| Clusteringmodelle| [Train Clustering Model](train-clustering-model.md) (Clusteringmodell trainieren) oder [Sweep Clustering](sweep-clustering.md) (Sweepclustering)| [Assign Data to Clusters](assign-data-to-clusters.md) (Zuweisen von Daten zu Clustern)|
| Anomalieerkennung – Einklassige SVM | [Train Anomaly Detection Model](train-anomaly-detection-model.md) (Anomalieerkennungsmodell trainieren) |[Score Model](score-model.md) (Modell bewerten)|
| Anomalieerkennung – PCA |[Train Model](train-model.md) (Modell trainieren) |[Score Model](score-model.md) (Modell bewerten) </br> Zur Bewertung des Modells sind einige zusätzliche Schritte erforderlich. |
| Anomalieerkennung – Zeitreihen|  [Time Series Anomaly Detection](time-series-anomaly-detection.md) (Anomalieerkennung in Zeitreihen) |Das Modell wird mit Daten trainiert und generiert Bewertungen. Das Modul erstellt keinen trainierten Learner und es ist keine zusätzliche Bewertung erforderlich. |
| Empfehlungsmodell| [Train Matchbox Recommender](train-matchbox-recommender.md) (Matchbox Recommender trainieren) | [Score Matchbox Recommender](score-matchbox-recommender.md) (Matchbox Recommender bewerten) |
| Bildklassifizierung | [Pretrained Cascade Image Classification](pretrained-cascade-image-classification.md) (Vortrainierte Kaskadenbildklassifizierung) | [Score Model](score-model.md) (Modell bewerten) |
|Vowpal Wabbit-Modelle| [Train Vowpal Wabbit Version 7-4 Model](train-vowpal-wabbit-version-7-4-model.md) (Vowpal Wabbit-Modelle der Version 7-4 trainieren) | [Score Vowpal Wabbit Version 7-4 Model](score-vowpal-wabbit-version-7-4-model.md) (Vowpal Wabbit-Modelle der Version 7-4 bewerten) |   
|Vowpal Wabbit-Modelle| [Train Vowpal Wabbit Version 7-10 Model](train-vowpal-wabbit-version-7-10-model.md) (Vowpal Wabbit-Modelle der Version 7-10 trainieren) | [Score Vowpal Wabbit Version 7-10 Model](score-vowpal-wabbit-version-7-10-model.md) (Vowpal Wabbit-Modelle der Version 7-10 bewerten) |
|Vowpal Wabbit-Modelle| [Train Vowpal Wabbit Version 8 Model](score-vowpal-wabbit-version-8-model.md) (Vowpal Wabbit-Modelle der Version 8 trainieren) | [Score Vowpal Wabbit Version 8 Model](score-vowpal-wabbit-version-8-model.md) (Vowpal Wabbit-Modelle der Version 8 bewerten) |-->
  
|Ausnahmemeldungen|  
|------------------------|  
|Learner mit ungültigem Typ wird übergeben.|  
|Der Learner „{0}“ weist einen ungültigen Typ auf.|  


## <a name="error-0014"></a>Fehler 0014  
 Eine Ausnahme tritt auf, wenn die Anzahl der in der Spalte eindeutigen Werte größer als der zulässige Wert ist.  
  
 Dieser Fehler tritt auf, wenn eine Spalte zu viele eindeutige Werte enthält.  Dieser Fehler kann z. B. auftreten, wenn Sie angeben, dass eine Spalte als kategorische Daten behandelt wird, aber es zu viele eindeutige Werte in der Spalte gibt, als dass die Verarbeitung abgeschlossen werden kann. Dieser Fehler kann auch auftreten, wenn die Anzahl der eindeutigen Werte bei zwei Eingaben nicht übereinstimmt.   
  
**Lösung:**

Öffnen Sie das Modul, das den Fehler generiert hat, und identifizieren Sie die als Eingaben verwendeten Spalten. Bei einigen Modulen können Sie mit der rechten Maustaste auf die Dataseteingabe klicken und **Visualize** (Visualisieren) auswählen, um Statistiken zu einzelnen Spalten zu erhalten, einschließlich der Anzahl der eindeutigen Werte und deren Verteilung.

Für Spalten, die Sie für die Gruppierung oder Kategorisierung verwenden möchten, führen Sie entsprechende Schritte durch, um die Anzahl der eindeutigen Werte in den Spalten zu reduzieren. Je nach Datentyp der Spalte kann die Reduzierung auf unterschiedliche Weise erfolgen. 
<!--
+ For text data, you might be able to use [Preprocess Text](preprocess-text.md) to collapse similar entries. 
+ For numeric data, you can create a smaller number of bins using [Group Data into Bins](group-data-into-bins.md), remove or truncate values using [Clip Values](clip-values.md), or use machine learning methods such as [Principal Component Analysis](principal-component-analysis.md) or [Learning with Counts](data-transformation-learning-with-counts.md) to reduce the dimensionality of the data.  
-->
> [!TIP]
> Sie können keine zu Ihrem Szenario passende Lösung finden? Sie können Feedback zu diesem Thema bereitstellen, das den Namen des Moduls, das den Fehler generiert hat, sowie den Datentyp und die Kardinalität der Spalte enthält. Wir werden diese Informationen verwenden, um besser auf das Ziel ausgerichtete Schritte zur Problembehandlung für gängige Szenarien bereitzustellen.   
  
|Ausnahmemeldungen|  
|------------------------|  
|Die Anzahl der in der Spalte eindeutigen Werte ist größer als der zulässige Wert.|  
|Die Anzahl der eindeutigen Werte in der Spalte: „{0}“ überschreitet die Tupelanzahl von {1}.|  
  

## <a name="error-0015"></a>Fehler 0015  
 Eine Ausnahme tritt auf, wenn bei der Datenbankverbindung ein Fehler aufgetreten ist.  
  
 Sie erhalten diesen Fehler, wenn Sie einen falschen SQL-Kontonamen, ein falsches Kennwort, einen falschen Datenbankserver oder Datenbanknamen eingeben oder wenn eine Verbindung mit der Datenbank aufgrund von Problemen mit der Datenbank oder dem Server nicht hergestellt werden kann.  
  
**Lösung:** Vergewissern Sie sich, dass Kontoname, Kennwort, Datenbankserver und Datenbank ordnungsgemäß eingegeben wurden und dass das angegebene Konto über die richtige Berechtigungsebene verfügt. Überprüfen Sie, ob auf die Datenbank derzeit zugegriffen werden kann.  
  
|Ausnahmemeldungen|  
|------------------------|  
|Fehler beim Herstellen der Datenbankverbindung.|  
|Fehler beim Herstellen der Datenbankverbindung: {0}.|  
  


## <a name="error-0016"></a>Fehler 0016  
 Eine Ausnahme tritt auf, wenn an das Modul übergebene Eingabedatasets kompatible Spaltentypen aufweisen sollten, dies aber nicht der Fall ist.  
  
 Sie erhalten diesen Fehler in Azure Machine Learning, wenn die Typen der Spalten, die in zwei oder mehr Datasets übergeben werden, untereinander nicht kompatibel sind.  
  
**Lösung:** Verwenden Sie [Edit Metadata](edit-metadata.md) (Metadaten bearbeiten), oder ändern Sie das ursprüngliche Eingabedataset,<!--, or use [Convert to Dataset](convert-to-dataset.md)--> um sicherzustellen, dass die Typen der Spalten kompatibel sind.  
  
|Ausnahmemeldungen|  
|------------------------|  
|Spalten mit entsprechendem Index in Eingabedatasets weisen nicht kompatible Typen auf.|  
|Die Spalten {0} und {1} sind nicht kompatibel.|  
|Die Spaltenelementtypen sind für die Spalte {0} (nullbasiert) der Eingabedatasets ({1} und {2}) nicht kompatibel.|  
  

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
|Die Spalte vom Typ {0} kann nicht verarbeitet werden. Der Typ wird vom Modul nicht unterstützt.|  
|Die Spalte „{1}“ vom Typ {0} kann nicht verarbeitet werden. Der Typ wird vom Modul nicht unterstützt.|  
|Die Spalte „{1}“ vom Typ {0} kann nicht verarbeitet werden. Der Typ wird vom Modul nicht unterstützt. Parametername: {2}|  
  

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
|{0} enthält ungültige Daten.|  
|{0} und {1} sollten in Bezug auf die Spalte konsistent sein.|  
  

## <a name="error-0019"></a>Fehler 0019  
 Eine Ausnahme tritt auf, wenn die Spalte sortierte Werte enthalten soll, aber das nicht der Fall ist.  
  
 Sie erhalten diesen Fehler in Azure Machine Learning, wenn die angegebenen Spaltenwerte nicht die richtige Reihenfolge aufweisen.  
  
**Lösung:** Sortieren Sie die Spaltenwerte, indem Sie das Eingabedataset manuell ändern und das Modul erneut ausführen.  
  
|Ausnahmemeldungen|  
|------------------------|  
|Die Werte in der Spalte sind nicht sortiert.|  
|Die Werte in der Spalte „{0}“ sind nicht sortiert.|  
|Die Werte in der Spalte „{0}“ von Dataset „{1}“ sind nicht sortiert.|  
  

## <a name="error-0020"></a>Fehler 0020  
 Eine Ausnahme tritt auf, wenn die Anzahl der Spalten in einigen der an das Modul übergebenen Datasets zu klein ist.  
  
 Sie erhalten diesen Fehler in Azure Machine Learning, wenn für ein Modul nicht genügend Spalten ausgewählt wurden.  
  
**Lösung:** Rufen Sie das Modul erneut auf, und stellen Sie sicher, dass der Spaltenselektor die richtige Anzahl von Spalten ausgewählt hat.  
  
|Ausnahmemeldungen|  
|------------------------|  
|Die Anzahl der Spalten im Eingabedataset ist kleiner als der zulässige Mindestwert.|  
|Die Anzahl der Spalten im Eingabedataset ist kleiner als der zulässige Mindestwert von {0} Spalte(n).|  
|Die Anzahl der Spalten im Eingabedataset „{0}“ ist kleiner als der zulässige Mindestwert von {1} Spalte(n).|

## <a name="error-0021"></a>Fehler 0021  
 Eine Ausnahme tritt auf, wenn die Anzahl der Zeilen in einigen der an das Modul übergebenen Datasets zu klein ist.  
  
 Dieser Fehler wird in Azure Machine Learning angezeigt, wenn nicht genügend Zeilen im Dataset vorhanden sind, um die angegebene Operation durchzuführen. Dieser Fehler kann z. B. auftreten, wenn das Eingabedataset leer ist oder wenn Sie versuchen, eine Operation auszuführen, die eine Mindestanzahl von Zeilen erfordert, um gültig zu sein. Derartige Operationen können die Gruppierung oder Klassifizierung auf der Grundlage statistischer Methoden, bestimmter Arten der Quantisierung und des Lernens durch Anzahl umfassen (sind aber nicht darauf beschränkt).  
  
**Lösung:**
 
 + Öffnen Sie das Modul, das den Fehler zurückgegeben hat, und überprüfen Sie das Eingabedataset und die Moduleigenschaften. 
 + Überprüfen Sie, ob das Eingabedataset nicht leer ist und ob genügend Datenzeilen vorhanden sind, um die in der Modulhilfe beschriebenen Anforderungen zu erfüllen.  
 + Wenn Ihre Daten aus einer externen Quelle geladen werden, stellen Sie sicher, dass die Datenquelle verfügbar ist und dass keine Fehler oder Änderungen in der Datendefinition vorliegen, die dazu führen würden, dass der Importvorgang weniger Zeilen erhält.
 + Wenn Sie eine Operation mit den dem Modul vorgelagerten Daten durchführen, die sich auf den Typ der Daten oder die Anzahl der Werte auswirken könnte, z. B. Operationen zur Bereinigung, Aufteilung oder Verknüpfung, überprüfen Sie die Ausgaben dieser Operationen, um die Anzahl der zurückgegebenen Zeilen zu ermitteln.  



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
|Die Anzahl der ausgewählten Spalten im Eingabedataset entspricht nicht {0}.|  
|Das Spaltenauswahlmuster „{0}“ liefert die Anzahl der ausgewählten Spalten im Eingabedataset, die nicht {1} entspricht.|  
|Das Spaltenauswahlmuster „{0}“ sollte {1} Spalten bereitstellen, die im Eingabedataset ausgewählt wurden, aber {2} Spalte(n) wird/werden bereitgestellt.|  



## <a name="error-0023"></a>Fehler 0023  
 Eine Ausnahme tritt auf, wenn die Zielspalte des Eingabedatasets für das aktuelle Trainermodul nicht gültig ist.  
  
 Dieser Fehler in Azure Machine Learning tritt auf, wenn die Zielspalte (gemäß der Auswahl in den Modulparametern) nicht dem gültigen Datentyp entspricht, alle fehlenden Werte enthält oder nicht wie erwartet kategorisch war.  
  
**Lösung:** Rufen Sie die Moduleingabe erneut auf, um den Inhalt der Bezeichnungs-/Zielspalte zu untersuchen. Stellen Sie sicher, dass sie nicht alle fehlenden Werte enthält. Wenn das Modul eine kategorische Zielspalte erwartet, stellen Sie sicher, dass es mehr als einen eindeutigen Wert in der Zielspalte gibt.  
  
|Ausnahmemeldungen|  
|------------------------|  
|Das Eingabedataset enthält eine nicht unterstützte Zielspalte.|  
|Das Eingabedataset enthält die nicht unterstützte Zielspalte „{0}“.|  
|Das Eingabedataset enthält die nicht unterstützte Zielspalte „{0}“ für Learner des Typs {1}.|  
 

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
|Es befindet sich keine Bezeichnungsspalte in „{0}“.|  
  

## <a name="error-0025"></a>Fehler 0025  
 Eine Ausnahme tritt auf, wenn das Dataset keine Bewertungsspalte enthält.  
  
 Dieser Fehler in Azure Machine Learning tritt auf, wenn die Eingabe für das Auswertungsmodell keine gültigen Bewertungsspalten enthält. Der Benutzer versucht z. B., ein Dataset auszuwerten, bevor es mit einem ordnungsgemäß trainierten Modell bewertet wurde, oder die Bewertungsspalte wurde explizit an einer Upstream-Position gelöscht. Diese Ausnahme tritt auch auf, wenn die Bewertungsspalten der beiden Datasets nicht kompatibel sind. Sie könnten z. B. versuchen, die Genauigkeit eines linearen Regressors mit der eines binären Klassifizierers zu vergleichen.  
  
**Lösung:** Rufen Sie die Eingabe für das Bewertungsmodell erneut auf und prüfen Sie, ob sie eine oder mehrere Bewertungsspalten enthalten. Andernfalls wurde das Dataset nicht bewertet oder die Bewertungsspalten wurden in einem Upstream-Modul gelöscht.  
  
|Ausnahmemeldungen|  
|------------------------|  
|Es befindet sich keine Bewertungsspalte im Dataset.|  
|Es befindet sich keine Bewertungsspalte in „{0}“.|  
|Es gibt keine Bewertungsspalte in „{0}“, die von „{1}“ erzeugt wurde. Bewerten Sie das Dataset mit dem richtigen Learnertyp.|  
  

## <a name="error-0026"></a>Fehler 0026  
 Eine Ausnahme tritt auf, wenn Spalten mit demselben Namen nicht zulässig sind.  
  
 Dieser Fehler in Azure Machine Learning tritt auf, wenn mehrere Spalten denselben Namen aufweisen. Sie können diesen Fehler z. B. erhalten, wenn das Dataset keine Kopfzeile hat und die Spaltennamen automatisch vergeben werden: Col0, Col1, usw.  
  
**Lösung:** Wenn Spalten denselben Namen aufweisen, fügen Sie ein [Edit Metadata](edit-metadata.md)-Modul (Metadaten bearbeiten) zwischen dem Eingabedataset und dem Modul ein. Verwenden Sie den Spaltenselektor in [Edit Metadata](edit-metadata.md) (Metadaten bearbeiten), um die umzubenennenden Spalten auszuwählen, indem Sie die neuen Namen in das Textfeld **New column names** (Neue Spaltennamen) eingeben.  
  
|Ausnahmemeldungen|  
|------------------------|  
|Gleiche Spaltennamen werden in Argumenten angegeben. Gleiche Spaltennamen sind im Modul nicht zulässig.|  
|Gleiche Spaltennamen in den Argumenten „{0}“ und „{1}“ sind nicht zulässig. Geben Sie unterschiedliche Namen an.|  
  

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
|Die Größe von „{0}“ stimmt nicht mit der Größe von „{1}“ überein.|  
  

## <a name="error-0028"></a>Fehler 0028  
 Eine Ausnahme tritt auf, wenn der Spaltensatz doppelte Spaltennamen enthält und dies nicht zulässig ist.  
  
 Dieser Fehler in Azure Machine Learning tritt auf, wenn Spaltennamen dupliziert werden, d. h. nicht eindeutig sind.  
  
**Lösung:** Wenn eine Spalte denselben Namen hat, fügen Sie eine Instanz von [Edit Metadata](edit-metadata.md) (Metadaten bearbeiten) zwischen dem Eingabedataset und dem Modul hinzu, das den Fehler auslöst. Verwenden Sie den Spaltenselektor in [Edit Metadata](edit-metadata.md) (Metadaten bearbeiten), um die umzubenennenden Spalten auszuwählen, und geben Sie die neuen Spaltennamen in das Textfeld **New column names** (Neue Spaltennamen) ein. Wenn Sie mehrere Spalten umbenennen, stellen Sie sicher, dass die Werte, die Sie in **New column names** (Neue Spaltennamen) eingeben, eindeutig sind.  
  
|Ausnahmemeldungen|  
|------------------------|  
|Der Spaltensatz enthält doppelte Spaltennamen.|  
|Der Name „{0}“ ist doppelt vorhanden.|  
|Der Name „{0}“ ist in „{1}“ doppelt vorhanden.|  
  

## <a name="error-0029"></a>Fehler 0029  
 Eine Ausnahme tritt auf, wenn eine ungültige URI übergeben wird.  
  
 Dieser Fehler in Azure Machine Learning tritt auf, wenn ein ungültiger URI übergeben wird.  Sie erhalten diesen Fehler, wenn eine der folgenden Bedingungen erfüllt ist: , oder.  
  
-   Der für Azure Blob Storage zum Lesen oder Schreiben bereitgestellte Public- oder SAS-URI enthält einen Fehler.  
  
-   Das Zeitfenster für die SAS ist abgelaufen.  
  
-   Die Web-URL via HTTP-Quelle stellt eine Datei oder einen Loopback-URI dar.  
  
-   Die Web-URL via HTTP enthält eine falsch formatierte URL.  
  
-   Die URL kann von der Remotequelle nicht aufgelöst werden.  
  
**Lösung:** Rufen Sie das Modul erneut auf, und überprüfen Sie das Format des URI. Wenn die Datenquelle eine Web-URL via HTTP ist, stellen Sie sicher, dass es sich bei der vorgesehenen Quelle nicht um eine Datei oder einen Loopback-URI (localhost) handelt.  
  
|Ausnahmemeldungen|  
|------------------------|  
|Es wird ein ungültiger URI übergeben.|  
  

## <a name="error-0030"></a>Fehler 0030  
 Eine Ausnahme tritt auf, wenn eine Datei nicht heruntergeladen werden konnte.  
  
 Diese Ausnahme in Azure Machine Learning tritt auf, wenn eine Datei nicht heruntergeladen werden konnte. Sie erhalten diese Ausnahme, wenn beim Leseversuch von einer HTTP-Quelle nach drei (3) Wiederholungsversuchen ein Fehler aufgetreten ist.  
  
**Lösung:** Überprüfen Sie, ob der URI zur HTTP-Quelle richtig ist und ob derzeit über das Internet auf die Website zugegriffen werden kann.  
  
|Ausnahmemeldungen|  
|------------------------|  
|Eine Datei konnte nicht heruntergeladen werden.|  
|Fehler beim Herunterladen der Datei: {0}.|  
  

## <a name="error-0031"></a>Fehler 0031  
 Eine Ausnahme tritt auf, wenn die Anzahl der Spalten im Spaltensatz kleiner als erforderlich ist.  
  
 Dieser Fehler in Azure Machine Learning tritt auf, wenn die Anzahl der ausgewählten Spalten kleiner als erforderlich ist.  Sie erhalten diesen Fehler, wenn nicht die minimal erforderliche Anzahl von Spalten ausgewählt ist.  
  
**Lösung:** Fügen Sie der Spaltenauswahl zusätzliche Spalten hinzu, indem Sie den **Spaltenselektor** verwenden.  
  
|Ausnahmemeldungen|  
|------------------------|  
|Die Anzahl der Spalten im Spaltensatz ist kleiner als erforderlich.|  
|{0} Spalte(n) muss (müssen) angegeben werden. Die tatsächliche Anzahl der angegebenen Spalten ist {1}.|  

## <a name="error-0032"></a>Fehler 0032  
 Eine Ausnahme tritt auf, wenn das Argument keine Zahl ist.  
  
 Sie erhalten diesen Fehler in Azure Machine Learning, wenn das Argument ein Double- oder NaN-Wert ist.  
  
**Lösung:** Ändern Sie das angegebene Argument, um einen gültigen Wert zu verwenden.  
  
|Ausnahmemeldungen|  
|------------------------|  
|Das Argument ist keine Zahl.|  
|„{0}“ ist keine Zahl.|  
  

## <a name="error-0033"></a>Fehler 0033  
 Eine Ausnahme tritt auf, wenn das Argument „Infinity“ (Unendlich) ist.  
  
 Dieser Fehler in Azure Machine Learning tritt auf, wenn das Argument ein unendlicher Wert ist. Sie erhalten diesen Fehler, wenn das Argument entweder `double.NegativeInfinity` oder `double.PositiveInfinity` ist.  
  
**Lösung:** Ändern Sie das angegebene Argument, um einen gültigen Wert zu erhalten.  
  
|Ausnahmemeldungen|  
|------------------------|  
|Das Argument muss endlich sein.|  
|„{0}“ ist nicht endlich.|  
  

## <a name="error-0034"></a>Fehler 0034  
 Eine Ausnahme tritt auf, wenn mehr als eine Bewertung für ein bestimmtes Benutzer-Element-Paar vorhanden ist.  
  
 Dieser Fehler in Azure Machine Learning tritt bei der Empfehlung auf, wenn ein Benutzer-Element-Paar mehr als einen Bewertungswert aufweist.  
  
**Lösung:** Stellen Sie sicher, dass das Benutzer-Element-Paar nur einen Bewertungswert besitzt.  
  
|Ausnahmemeldungen|  
|------------------------|  
|Für die Werte im Dataset sind mehrere Bewertungen vorhanden.|  
|Es sind mehrere Bewertungen für den Benutzer {0} und das Element {1} in der Datentabelle der Bewertungsvorhersage enthalten.|  
  

## <a name="error-0035"></a>Fehler 0035  
 Eine Ausnahme tritt auf, wenn für einen bestimmten Benutzer oder ein bestimmtes Element keine Features bereitgestellt wurden.  
  
 Dieser Fehler in Azure Machine Learning tritt auf, wenn Sie versuchen, ein Empfehlungsmodell für die Bewertung zu verwenden, aber kein Featurevektor gefunden werden konnte.  
  
**Lösung:**

Der Matchbox Recommender hat bestimmte Anforderungen, die erfüllt werden müssen, wenn Sie entweder Element- oder Benutzerfeatures verwenden.  Dieser Fehler zeigt an, dass für einen von Ihnen als Eingabe bereitgestellten Benutzer oder für ein Element ein Featurevektor fehlt.  Sie müssen sicherstellen, dass für jeden Benutzer oder jedes Element ein Vektor von Features in den Daten vorhanden ist.  
  
 Wenn Sie z. B. ein Empfehlungsmodell mit Features wie Alter, Standort oder Einkommen des Benutzers trainiert haben, jetzt aber Ergebnisse für neue Benutzer erstellen möchten, die während des Trainings nicht angezeigt wurden, müssen Sie einige gleichwertige Features (d. h. Werte für Alter, Standort und Einkommen) für die neuen Benutzer bereitstellen, um entsprechende Vorhersagen für sie treffen zu können. 
 
 Wenn Sie für diese Benutzer über keine Features verfügen, sollten Sie die Featureentwicklung in Betracht ziehen, um entsprechende Features zu generieren.  Wenn Sie z. B. nicht über individuelle Alters- oder Einkommenswerte verfügen, können Sie ungefähre Werte generieren, die Sie für eine Gruppe von Benutzern verwenden können. 
 
<!--When you are scoring from a recommendation mode, you can use item or user features only if you previously used item or user features during training. For more information, see [Score Matchbox Recommender](score-matchbox-recommender.md).
 
For general information about how the Matchbox recommendation algorithm works, and how to prepare a dataset of item features or user features, see [Train Matchbox Recommender](train-matchbox-recommender.md).  -->
  
 > [!TIP]
 > Ist die Lösung nicht auf Ihren Fall anwendbar? Sie können jederzeit Feedback zu diesem Artikel senden und Informationen zu diesem Szenario bereitstellen, einschließlich des Moduls und der Anzahl der Zeilen in der Spalte. Wir werden diese Informationen verwenden, um in Zukunft ausführlichere Schritte zur Problembehandlung bereitzustellen.
   
|Ausnahmemeldungen|  
|------------------------|  
|Für einen erforderlichen Benutzer oder ein erforderliches Element wurden keine Features bereitgestellt.|  
|Für {0} sind Features erforderlich, aber sie wurden bereitgestellt.|  
  

## <a name="error-0036"></a>Fehler 0036  
 Eine Ausnahme tritt auf, wenn mehrere Featurevektoren für einen bestimmten Benutzer oder ein bestimmtes Element bereitgestellt wurden.  
  
 Dieser Fehler in Azure Machine Learning tritt auf, wenn ein Featurevektor mehrfach definiert wurde.  
  
**Lösung:** Stellen Sie sicher, dass der Featurevektor nicht mehrfach definiert ist.  
  
|Ausnahmemeldungen|  
|------------------------|  
|Doppelte Featuredefinition für einen Benutzer oder ein Element.|  
|Doppelte Featuredefinition für {0}.|  
  

## <a name="error-0037"></a>Fehler 0037  
 Eine Ausnahme tritt auf, wenn mehrere Bezeichnungsspalten angegeben sind und nur eine einzelne Spalte zulässig ist.  
  
 Dieser Fehler in Azure Machine Learning tritt auf, wenn mehr als eine Spalte als neue Bezeichnungsspalte ausgewählt ist. Die meisten überwachten Lernalgorithmen erfordern, dass eine einzelne Spalte als Ziel oder Bezeichnung markiert wird.  
  
**Lösung:** Stellen Sie sicher, dass Sie eine einzelne Spalte als neue Bezeichnungsspalte auswählen.  
  
|Ausnahmemeldungen|  
|------------------------|  
|Es wurden mehrere Bezeichnungsspalten angegeben.|  
  

## <a name="error-0038"></a>Fehler 0038  
 Eine Ausnahme tritt auf, wenn die Anzahl der erwarteten Elemente ein exakter Wert sein sollte, aber dies nicht der Fall ist.  
  
 Dieser Fehler in Azure Machine Learning tritt auf, wenn die Anzahl der erwarteten Elemente ein exakter Wert sein sollte, aber dies nicht der Fall ist.  Sie erhalten diesen Fehler, wenn die Anzahl der Elemente ungleich dem gültigen Erwartungswert ist.  
  
**Lösung:** Ändern Sie die Eingabe, um die richtige Anzahl von Elementen zu erhalten.  
  
|Ausnahmemeldungen|  
|------------------------|  
|Die Anzahl der Elemente ist nicht gültig.|  
|Die Anzahl der Elemente in „{0}“ ist nicht gültig.|  
|Die Anzahl der Elemente in „{0}“ entspricht nicht der gültigen Anzahl von {1} Element(en).|  
  

## <a name="error-0039"></a>Fehler 0039  
 Eine Ausnahme tritt auf, wenn bei einer Operation ein Fehler aufgetreten ist.  
  
 Dieser Fehler in Azure Machine Learning tritt auf, wenn eine interne Operation nicht abgeschlossen werden kann.  
  
**Lösung:** Dieser Fehler wird durch viele Bedingungen verursacht und es gibt keine bestimmte Lösung.  
 Die folgende Tabelle enthält allgemeine Meldungen zu diesem Fehler, auf die eine bestimmte Beschreibung der Bedingung folgt. 
 
 Wenn keine Details verfügbar sind, senden Sie ein [Feedback](https://social.msdn.microsoft.com/forums/azure/home?forum=MachineLearning), und stellen Sie Informationen zu den Modulen, die den Fehler ausgelöst haben, und zu den zugehörigen Bedingungen bereit.
  
|Ausnahmemeldungen|  
|------------------------|  
|Fehler beim Vorgang.|  
|Fehler beim Abschließen der Operation: {0}.|  
  

## <a name="error-0040"></a>Fehler 0040  
 Eine Ausnahme tritt auf, wenn ein veraltetes Modul aufgerufen wird.  
  
 Dieser Fehler in Azure Machine Learning tritt beim Aufrufen eines veralteten Moduls auf.  
  
**Lösung:** Ersetzen Sie das veraltete Modul durch ein unterstütztes Modul. Im Ausgabeprotokoll des Moduls finden Sie weitere Informationen dazu, welches Modul stattdessen verwendet werden soll.  
  
|Ausnahmemeldungen|  
|------------------------|  
|Zugriff auf veraltetes Modul.|  
|Das Modul „{0}“ ist veraltet. Verwenden Sie stattdessen das Modul „{1}“.|  
 

## <a name="error-0041"></a>Fehler 0041  
 Eine Ausnahme tritt auf, wenn ein veraltetes Modul aufgerufen wird.  
  
 Dieser Fehler in Azure Machine Learning tritt beim Aufrufen eines veralteten Moduls auf.  
  
**Lösung:** Ersetzen Sie das veraltete Modul durch eine Reihe unterstützter Module. Diese Informationen sollten im Ausgabeprotokoll des Moduls verfügbar sein.  
  
|Ausnahmemeldungen|  
|------------------------|  
|Zugriff auf veraltetes Modul.|  
|Das Modul „{0}“ ist veraltet. Verwenden Sie die „{1}“-Module für die angeforderte Funktionalität.|  
 

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
|Die Spalte vom Typ {0} konnte nicht in eine Spalte vom Typ {1} konvertiert werden.|  
|Die Spalte „{2}“ vom Typ {0} konnte nicht in eine Spalte vom Typ {1} konvertiert werden.|  
|Die Spalte „{2}“ vom Typ {0} konnte nicht in eine Spalte „{3}“ vom Typ {1} konvertiert werden.|  
  

## <a name="error-0043"></a>Fehler 0043  
 Eine Ausnahme tritt auf, wenn der Elementtyp nicht explizit „Equals“ (Ist gleich) implementiert.  
  
 Dieser Fehler in Azure Machine Learning wird nicht verwendet und ist veraltet.  
  
**Lösung:** None (Keine):  
  
|Ausnahmemeldungen|  
|------------------------|  
|Keine zugängliche explizite Methode „Equals“ (Ist gleich) gefunden.|  
|Werte für Spalte \\„{0}\\“ vom Typ {1} können nicht verglichen werden. Keine zugängliche explizite Methode „Equals“ (Ist gleich) gefunden.|  


## <a name="error-0044"></a>Fehler 0044  
 Eine Ausnahme tritt auf, wenn es nicht möglich ist, den Elementtyp der Spalte aus den vorhandenen Werten abzuleiten.  
  
 Dieser Fehler in Azure Machine Learning tritt auf, wenn es nicht möglich ist, den Typ einer Spalte oder von Spalten in einem Dataset abzuleiten. Dies ist in der Regel der Fall, wenn zwei oder mehr Datasets mit unterschiedlichen Elementtypen verkettet werden. Wenn Azure Machine Learning den gemeinsamen Typ nicht bestimmen kann, der alle Werte in einer oder mehreren Spalten ohne Informationsverlust darstellen kann, wird dieser Fehler generiert.  
  
**Lösung:** Stellen Sie sicher, dass alle Werte in einer bestimmten Spalte in beiden zu kombinierenden Datasets entweder vom gleichen Typ sind (numerisch, boolesch, kategorisch, Zeichenfolge, Datum usw.) oder in denselben Typ umgewandelt werden können.  
  
|Ausnahmemeldungen|  
|------------------------|  
|Der Elementtyp der Spalte kann nicht abgeleitet werden.|  
|Der Elementtyp für die Spalte „{0}“ kann nicht abgeleitet werden – alle Elemente sind Nullverweise.|  
|Der Elementtyp für die Spalte „{0}“ von Dataset „{1}“ kann nicht abgeleitet werden – alle Elemente sind Nullverweise.|  
  

## <a name="error-0045"></a>Fehler 0045  
 Eine Ausnahme tritt auf, wenn es aufgrund von gemischten Elementtypen in der Quelle nicht möglich ist, eine Spalte zu erstellen.  
  
 Dieser Fehler in Azure Machine Learning tritt auf, wenn die Elementtypen der beiden zu kombinierenden Datasets verschieden sind.  
  
**Lösung:** Stellen Sie sicher, dass alle Werte in einer bestimmten Spalte in beiden zu kombinierenden Datasets vom gleichen Typ sind (numerisch, boolesch, kategorisch, Zeichenfolge, Datum usw.).  
  
|Ausnahmemeldungen|  
|------------------------|  
|Es kann keine Spalte mit gemischten Elementtypen erstellt werden.|  
|Es kann keine Spalte mit der ID „{0}“ von gemischten Elementtypen erstellt werden:\n\tDatentyp[{1}, {0}] ist {2}\n\tDatentyp[{3}, {0}] ist {4}.|  
  

## <a name="error-0046"></a>Fehler 0046  
 Eine Ausnahme tritt auf, wenn in einem bestimmten Pfad kein Verzeichnis erstellt werden kann.  
  
 Dieser Fehler in Azure Machine Learning tritt auf, wenn in einem bestimmten Pfad kein Verzeichnis erstellt werden kann. Sie erhalten diesen Fehler, wenn ein Teil des Pfads zum Ausgabeverzeichnis für eine Hive-Abfrage falsch oder nicht zugänglich ist.  
  
**Lösung:** Rufen Sie das Modul erneut auf und überprüfen Sie, ob der Verzeichnispfad ordnungsgemäß formatiert ist und ob auf ihn mit den aktuellen Anmeldeinformationen zugegriffen werden kann.  
  
|Ausnahmemeldungen|  
|------------------------|  
|Geben Sie ein gültiges Ausgabeverzeichnis an.|  
|Verzeichnis: {0} kann nicht erstellt werden. Geben Sie einen gültigen Pfad an.|  
  

## <a name="error-0047"></a>Fehler 0047  
 Eine Ausnahme tritt auf, wenn die Anzahl der Featurespalten in einigen der an das Modul übergebenen Datasets zu klein ist.  
  
 Dieser Fehler in Azure Machine Learning tritt auf, wenn das Eingabedataset für das Training nicht die vom Algorithmus geforderte Mindestanzahl von Spalten enthält. Normalerweise ist entweder das Dataset leer oder enthält nur Trainingsspalten.  
  
**Lösung:** Rufen Sie das Eingabedataset erneut auf, um sicherzustellen, dass es neben der Bezeichnungsspalte eine oder mehrere zusätzliche Spalten enthält.  
  
|Ausnahmemeldungen|  
|------------------------|  
|Die Anzahl der Featurespalten im Eingabedataset ist kleiner als der zulässige Mindestwert.|  
|Die Anzahl der Featurespalten im Eingabedataset ist kleiner als der zulässige Mindestwert von {0} Spalte(n).|  
|Die Anzahl der Featurespalten im Eingabedataset „{0}“ ist kleiner als der zulässige Mindestwert von {1} Spalte(n).|  
  

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
|Fehler beim Öffnen der Datei: {0}.|  


## <a name="error-0049"></a>Fehler 0049  
 Eine Ausnahme tritt auf, wenn eine Datei nicht analysiert werden konnte.  
  
 Dieser Fehler in Azure Machine Learning tritt auf, wenn eine Datei nicht analysiert werden konnte. Sie erhalten diesen Fehler, wenn das im Modul [Import Data](import-data.md) (Daten importieren) ausgewählte Dateiformat nicht mit dem tatsächlichen Format der Datei übereinstimmt oder die Datei ein nicht erkennbares Zeichen enthält.  
  
**Lösung:** Rufen Sie das Modul erneut auf, und korrigieren Sie die Auswahl des Dateiformats, wenn es nicht mit dem Format der Datei übereinstimmt. Überprüfen Sie nach Möglichkeit die Datei, um sicherzustellen, dass sie keine unzulässigen Zeichen enthält.  
  
|Ausnahmemeldungen|  
|------------------------|  
|Eine Datei konnte nicht analysiert werden.|  
|Fehler beim Analysieren der Datei: {0}.|  
  

## <a name="error-0050"></a>Fehler 0050  
 Eine Ausnahme tritt auf, wenn Ein- und Ausgabedateien identisch sind.  
  
**Lösung:** Dieser Fehler in Azure Machine Learning wird nicht verwendet und ist veraltet.  
  
|Ausnahmemeldungen|  
|------------------------|  
|Die angegebenen Dateien für die Ein- und Ausgabe dürfen nicht identisch sein.|


## <a name="error-0051"></a>Fehler 0051  
 Eine Ausnahme tritt auf, wenn mehrere Ausgabedateien identisch sind.  
  
**Lösung:** Dieser Fehler in Azure Machine Learning wird nicht verwendet und ist veraltet.  
  
|Ausnahmemeldungen|  
|------------------------|  
|Die angegebenen Dateien für die Ausgabe dürfen nicht identisch sein.|


## <a name="error-0052"></a>Fehler 0052  
 Eine Ausnahme tritt auf, wenn der Schlüssel des Azure-Speicherkontos falsch angegeben wurde.  
  
 Dieser Fehler in Azure Machine Learning tritt auf, wenn der Schlüssel für den Zugriff auf das Azure-Speicherkonto falsch ist. Dieser Fehler kann z. B. auftreten, wenn der Azure-Speicherschlüssel beim Kopieren und Einfügen abgeschnitten oder der falsche Schlüssel verwendet wurde.  
  
 Weitere Informationen zum Abrufen des Schlüssels für ein Azure-Speicherkonto finden Sie unter [Anzeigen, Kopieren und erneutes Generieren von Speicherzugriffsschlüsseln](https://azure.microsoft.com/documentation/articles/storage-create-storage-account-classic-portal/).  
  
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

## <a name="error-0054"></a>Fehler 0054  
 Eine Ausnahme tritt auf, wenn zu wenige eindeutige Werte in der Spalte vorhanden sind, um die Operation abzuschließen.  
  
**Lösung:** Dieser Fehler in Azure Machine Learning wird nicht verwendet und ist veraltet.  
  
|Ausnahmemeldungen|  
|------------------------|  
|Die Daten weisen in der angegebenen Spalte zu wenige unterschiedliche Werte auf, um die Operation abzuschließen.|  
|Die Daten weisen in der angegebenen Spalte zu wenige unterschiedliche Werte auf, um die Operation abzuschließen. Der erforderliche Mindestwert sind {0} Elemente.|  
|Die Daten weisen in der Spalte „{1}“ zu wenige unterschiedliche Werte auf, um die Operation abzuschließen. Der erforderliche Mindestwert sind {0} Elemente.|  
  

## <a name="error-0055"></a>Fehler 0055  
 Eine Ausnahme tritt auf, wenn ein veraltetes Modul aufgerufen wird.  Dieser Fehler in Azure Machine Learning tritt auf, wenn Sie versuchen, ein veraltetes Modul aufzurufen.
  
**Lösung:**
  
|Ausnahmemeldungen|  
|------------------------|  
|Zugriff auf veraltetes Modul.|  
|Das Modul „{0}“ ist veraltet.|  

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
|Die Spalte namens „{0}“ befindet sich nicht in einer zulässigen Kategorie.|  
  

## <a name="error-0057"></a>Fehler 0057  
 Eine Ausnahme tritt bei dem Versuch auf, eine bereits vorhandene Datei oder ein bereits vorhandenes Blob zu erstellen.  
  
 Diese Ausnahme tritt auf, wenn Sie das Modul [Export Data](export-data.md) (Daten exportieren) oder ein anderes Modul verwenden, um die Ergebnisse eines Experiments in Azure Machine Learning in Azure-Blobspeicher zu speichern, dabei aber versuchen, eine bereits vorhandene Datei oder ein bereits vorhandenes BLOB zu erstellen.   
  
**Lösung:**
 
 Sie erhalten diesen Fehler nur, wenn Sie zuvor die Eigenschaft **Azure blob storage write mode** (Schreibmodus für Azure-Blobspeicher) auf **Error** (Fehler) festgelegt haben. Dieses Modul löst standardmäßig einen Fehler aus, wenn Sie versuchen, ein Dataset in ein bereits bestehendes Blob zu schreiben.
 
 - Öffnen Sie die Moduleigenschaften, und ändern Sie die Eigenschaft **Azure blob storage write mode** (Schreibmodus für Azure-Blobspeicher) in **Overwrite** (Überschreiben).
 - Alternativ können Sie auch den Namen eines anderen Zielblobs oder einer anderen Datei eingeben und ein Blob angeben, das nicht bereits vorhanden ist.  
  
|Ausnahmemeldungen|  
|------------------------|  
|Datei oder Blob ist bereits vorhanden.|  
|Datei oder Blob „{0}“ ist bereits vorhanden.|  
  

## <a name="error-0058"></a>Fehler 0058  
 Dieser Fehler in Azure Machine Learning tritt auf, wenn das Dataset nicht die erwartete Bezeichnungsspalte enthält.  
  
 Diese Ausnahme kann auch auftreten, wenn die angegebene Bezeichnungsspalte nicht mit den vom Learner erwarteten Daten oder Datentyp übereinstimmt oder die falschen Werte aufweist. Diese Ausnahme wird z. B. erzeugt, wenn beim Training eines binären Klassifikators eine Bezeichnungsspalte mit realem Wert verwendet wird.  
  
**Lösung:** Die Lösung hängt vom verwendeten Learner oder Trainer und den Datentypen der Spalten in Ihrem Dataset ab. Überprüfen Sie zunächst die Anforderungen an den maschinellen Lernalgorithmus oder das Trainingsmodul.  
  
 Rufen Sie das Eingabedataset erneut auf. Überprüfen Sie, ob die Spalte, von der Sie erwarten, dass sie als Bezeichnung behandelt wird, den richtigen Datentyp für das von Ihnen zu erstellende Modell aufweist.  
  
 Überprüfen Sie die Eingaben auf fehlende Werte und beseitigen oder ersetzen Sie diese gegebenenfalls.  
  
 Fügen Sie bei Bedarf das Modul [Edit Metadata](edit-metadata.md) (Metadaten bearbeiten) hinzu und stellen Sie sicher, dass die Bezeichnungsspalte als Bezeichnung gekennzeichnet ist.  
  
|Ausnahmemeldungen|  
|------------------------|  
|Die Bezeichnungsspalte entspricht nicht den Erwartungen.|  
|Die Bezeichnungsspalte entspricht in „{0}“ nicht den Erwartungen.|  
|Die Bezeichnungsspalte „{0}“ wird in „{1}“ nicht erwartet.|  
  

## <a name="error-0059"></a>Fehler 0059  
 Eine Ausnahme tritt auf, wenn ein in einer Spaltenauswahl angegebener Spaltenindex nicht analysiert werden kann.  
  
 Dieser Fehler in Azure Machine Learning tritt auf, wenn ein bei der Verwendung der Spaltenauswahl angegebener Spaltenindex nicht analysiert werden kann.  Sie erhalten diesen Fehler, wenn der Spaltenindex in einem ungültigen Format vorliegt, das nicht analysiert werden kann.  
  
**Lösung:** Ändern Sie den Spaltenindex, um einen gültigen Indexwert zu verwenden.  
  
|Ausnahmemeldungen|  
|------------------------|  
|Mindestens ein angegebener Spaltenindex oder Indexbereich konnte nicht analysiert werden.|  
|Der Spaltenindex oder der Bereich „{0}“ konnte nicht analysiert werden.|  
  

## <a name="error-0060"></a>Fehler 0060  
 Eine Ausnahme tritt auf, wenn in einer Spaltenauswahl ein Spaltenbereich außerhalb des Bereichs angegeben ist.  
  
 Dieser Fehler in Azure Machine Learning tritt auf, wenn in der Spaltenauswahl ein Spaltenbereich außerhalb des Bereichs angegeben ist. Sie erhalten diesen Fehler, wenn der Spaltenbereich in der Spaltenauswahl nicht mit den Spalten im Dataset übereinstimmt.  
  
**Lösung:** Ändern Sie den Spaltenbereich in der Spaltenauswahl so, dass er den Spalten im Dataset entspricht.  
  
|Ausnahmemeldungen|  
|------------------------|  
|Es wurde ein ungültiger oder außerhalb des Bereichs liegender Spaltenindexbereich angegeben.|  
|Der Spaltenbereich „{0}“ ist ungültig oder außerhalb des Bereichs.|  
  

## <a name="error-0061"></a>Fehler 0061  
 Eine Ausnahme tritt bei dem Versuch auf, eine Zeile zu einer DataTable hinzuzufügen, die eine andere Anzahl von Spalten als die Tabelle aufweist.  
  
 Dieser Fehler in Azure Machine Learning tritt bei dem Versuch auf, eine Zeile zu einem Dataset hinzuzufügen, das eine andere Anzahl von Spalten als das Dataset aufweist.  Sie erhalten diesen Fehler, wenn die Zeile, die dem Dataset hinzugefügt wird, eine andere Anzahl von Spalten verwendet als das Eingabedataset.  Die Zeile kann nicht an das Dataset angefügt werden, wenn die Anzahl der Spalten verschieden ist.  
  
**Lösung:** Ändern Sie das Eingabedataset so, dass es die gleiche Anzahl von Spalten aufweist wie die hinzugefügte Zeile, oder ändern Sie die hinzugefügte Zeile so, dass sie dieselbe Anzahl von Spalten verwendet wie das Dataset.  
  
|Ausnahmemeldungen|  
|------------------------|  
|Alle Tabellen müssen dieselbe Anzahl von Spalten aufweisen.|  
  

## <a name="error-0062"></a>Fehler 0062  
 Eine Ausnahme tritt bei dem Versuch auf, zwei Modelle mit unterschiedlichen Learnertypen zu vergleichen.  
  
 Dieser Fehler in Azure Machine Learning tritt auf, wenn Auswertungsmetriken für zwei verschiedene bewertete Datasets nicht verglichen werden können. In diesem Fall ist es nicht möglich, die Effektivität der Modelle zu vergleichen, die zur Erstellung der beiden bewerteten Datasets verwendet werden.  
  
**Lösung:** Überprüfen Sie, ob die Ergebnisse von derselben Art von maschinellem Lernmodell stammen (binäre Klassifizierung, Regression, Klassifizierung mit mehreren Klassen, Empfehlung, Clustering, Anomalieerkennung usw.). Alle von Ihnen zu vergleichenden Modelle müssen denselben Learnertyp aufweisen.  
  
|Ausnahmemeldungen|  
|------------------------|  
|Alle Modelle müssen denselben Learnertyp aufweisen.|  
  

 <!--## Error 0063  
 This exception is raised when R script evaluation fails with an error.  
  
 This error occurs when you have provided an R script in one of the [R language modules](r-language-modules.md) in Azure Machine Learning, and the R code contains internal syntax errors. The exception can also occur if you provide the wrong inputs to the R script. 
 
 The error can also occur if the script is too large to execute in the workspace. The maximum script size for the **Execute R Script** module is 1,000 lines or 32 KB of work space, whichever is lesser.
  
**Resolution:**

1. In Azure Machine Learning Studio, right-click the module that has the error, and select **View Log**.
2. Examine the standard error log of the module, which contains the stack trace.
    + Lines beginning with [ModuleOutput] indicate output from R.
    + Messages from R marked as **warnings** typically do not cause the experiment to fail.
3. Resolve script issues.  
    + Check for R syntax errors. Check for variables that are defined but never populated.
    + Review the input data and the script to determine if either the data or variables in the script use characters not supported by Azure Machine Learning.
    + Check whether all package dependencies are installed.
    + Check whether your code loads required libraries that are not loaded by default.
    + Check whether the required packages are the correct version.
    + Make sure that any dataset that you want to output is converted to a data frame.  
4.  Resubmit the experiment.

 <!--
> [!NOTE]
> These topics contains examples of R code that you can use, as well as links to experiments in the [Cortana Intelligence Gallery](https://gallery.cortanaintelligence.com) that use R script.
> + [Execute R Script](execute-r-script.md)
> + [Create R Model](create-r-model.md)
-->  
|Ausnahmemeldungen|  
|------------------------|  
|Fehler bei der Auswertung des R-Skripts.|  
|Der folgende Fehler trat bei der Auswertung des R-Skripts auf: ---------- Start der Fehlermeldung von R -------------- {0} Ende der Fehlermeldung von R ---------------- Ende der Fehlermeldung von R ----------------|  
|Bei der Auswertung des R-Skripts „{1}“ ist der folgende Fehler aufgetreten: ---------- Start der Fehlermeldung von R -------------- {0} --------------- Ende der Fehlermeldung von R -----------------|  
  


## <a name="error-0064"></a>Fehler 0064  
 Eine Ausnahme tritt auf, wenn der Name oder Speicherschlüssel des Azure-Speicherkontos falsch angegeben wurde.  
  
 Dieser Fehler in Azure Machine Learning tritt auf, wenn der Name oder Speicherschlüssel des Azure-Speicherkontos falsch angegeben wurde. Sie erhalten diesen Fehler, wenn Sie einen falschen Kontonamen oder ein falsches Kennwort für das Speicherkonto eingeben. Dies kann vorkommen, wenn Sie den Kontonamen oder das Kennwort manuell eingeben. Der Fehler kann auch auftreten, wenn das Konto gelöscht wurde.  
  
**Lösung:** Überprüfen Sie, ob der Kontoname und das Kennwort ordnungsgemäß eingegeben wurden und ob das Konto vorhanden ist.  
  
|Ausnahmemeldungen|  
|------------------------|  
|Der Name oder Speicherschlüssel des Azure-Speicherkontos ist falsch.|  
|Der Azure-Speicherkontoname „{0}“ oder der Speicherschlüssel für den Kontonamen ist falsch.|  
  

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
|Der Azure-Speicherblob ist falsch.|  
|Der Azure-Speicherblobname „{0}“ ist falsch.|  
  

## <a name="error-0066"></a>Fehler 0066  
 Eine Ausnahme tritt auf, wenn eine Ressource nicht in ein Azure-Blob hochgeladen werden konnte.  
  
 Dieser Fehler in Azure Machine Learning tritt auf, wenn eine Ressource nicht in ein Azure-Blob hochgeladen werden konnte.  <!--You will receive this message if [Train Vowpal Wabbit 7-4 Model](train-vowpal-wabbit-version-7-4-model.md) encounters an error attempting to save either the model or the hash created when training the model.--> Beide werden in demselben Azure-Speicherkonto gespeichert wie das Konto, das die Eingabedatei enthält.  
  
**Lösung:** Rufen Sie das Modul erneut auf. Überprüfen Sie, ob Azure-Kontoname, Speicherschlüssel und Container richtig sind und ob das Konto über die Berechtigung verfügt, in den Container zu schreiben.  
  
|Ausnahmemeldungen|  
|------------------------|  
|Die Ressource konnte nicht in den Azure-Speicher hochgeladen werden.|  
|Die Datei „{0}“ konnte nicht als {1} in den Azure-Speicher hochgeladen werden.|  
  

## <a name="error-0067"></a>Fehler 0067  
 Eine Ausnahme tritt auf, wenn ein Dataset eine andere Spaltenanzahl aufweist als erwartet.  
  
 Dieser Fehler in Azure Machine Learning tritt auf, wenn ein Dataset eine andere Spaltenanzahl aufweist als erwartet.  Sie erhalten diesen Fehler, wenn die Anzahl der Spalten im Dataset von der Anzahl der Spalten abweicht, die das Modul während der Ausführung erwartet.  
  
**Lösung:** Ändern Sie das Eingabedataset oder die Parameter.  
  
|Ausnahmemeldungen|  
|------------------------|  
|Unerwartete Anzahl von Spalten in der Datentabelle.|  
|„{0}“ Spalten erwartet, aber stattdessen „{1}“ Spalten gefunden.|  
  

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

+ [Erstellen von Hive-Tabellen und Laden von Daten aus Azure Blob Storage](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-move-hive-tables)
+ [Durchsuchen von Daten in Tabellen mithilfe von Hive-Abfragen](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-explore-data-hive-tables)
+ [Erstellen von Features für Daten in einem Hadoop-Cluster mit Hive-Abfragen](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-create-features-hive)
+ [Cheat Sheet für Hive für SQL-Benutzer (PDF)](http://hortonworks.com/wp-content/uploads/2013/05/hql_cheat_sheet.pdf)

  
|Ausnahmemeldungen|  
|------------------------|  
|Das Hive-Skript ist falsch.|  
|Das Hive-Skript {0} ist falsch.|  
  

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
|Die SQL-Abfrage „{0}“ ist falsch.|  
|Die SQL-Abfrage „{0}“ ist falsch: {1}|  
  

## <a name="error-0070"></a>Fehler 0070  
 Eine Ausnahme tritt bei dem Versuch auf, auf eine nicht vorhandene Azure-Tabelle zuzugreifen.  
  
 Dieser Fehler in Azure Machine Learning tritt bei dem Versuch auf, auf eine nicht vorhandene Azure-Tabelle zuzugreifen. Sie erhalten diesen Fehler, wenn Sie eine Tabelle im Azure-Speicher angeben, die beim Lesen von oder Schreiben in den Azure-Tabellenspeicher nicht vorhanden ist. Dies kann vorkommen, wenn Sie sich bei der Eingabe des Namens der gewünschten Tabelle vertippen oder ein Konflikt zwischen Zielname und Speichertyp vorliegt. Sie wollten z. B. aus einer Tabelle lesen, haben aber stattdessen einen Blobnamen eingegeben.  
  
**Lösung:** Rufen Sie das Modul erneut auf, um sicherzustellen, dass der Name der Tabelle richtig ist.  
  
|Ausnahmemeldungen|  
|------------------------|  
|Die Azure-Tabelle ist nicht vorhanden.|  
|Die Azure-Tabelle „{0}“ ist nicht vorhanden.|  
  
## <a name="error-0071"></a>Fehler 0071  
 Eine Ausnahme tritt auf, wenn die bereitgestellten Anmeldeinformationen falsch sind.  
  
 Dieser Fehler in Azure Machine Learning tritt auf, wenn die bereitgestellten Anmeldeinformationen falsch sind.  
  
 Sie können diesen Fehler auch erhalten, wenn das Modul keine Verbindung zu einem HDInsight-Cluster herstellen kann.  
  
**Lösung:** Überprüfen Sie die Eingaben für das Modul, und überprüfen Sie den Kontonamen und das Kennwort.  
  
 Überprüfen Sie die folgenden Probleme, die zu einem Fehler führen können:  
  
-   Das Schema des Datasets stimmt nicht mit dem Schema der Zieldatentabelle überein.  
  
-   Spaltennamen fehlen oder sind falsch geschrieben.  
  
-   Sie schreiben in eine Tabelle, die Spaltennamen mit unzulässigen Zeichen enthält. Normalerweise können Sie solche Spaltennamen in eckige Klammern setzen, aber wenn das nicht funktioniert, bearbeiten Sie die Spaltennamen, um nur Buchstaben und Unterstriche zu verwenden (_).  
  
-   Zeichenfolgen, die Sie zu schreiben versuchen, enthalten einfache Anführungszeichen.  
  
 Wenn Sie versuchen, eine Verbindung mit einem HDInsight-Cluster herzustellen, vergewissern Sie sich, dass mit den bereitgestellten Anmeldeinformationen auf den Zielcluster zugegriffen werden kann.  
  
|Ausnahmemeldungen|  
|------------------------|  
|Es werden falsche Anmeldeinformationen übergeben.|  
|Falscher Benutzername „{0}“ oder falsches Kennwort wurde übergeben.|  
  

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
|Fehler beim Konvertieren der Spalte in {0}.|  
  

## <a name="error-0074"></a>Fehler 0074  
 Eine Ausnahme tritt auf, wenn das Modul [Edit Metadata](edit-metadata.md) (Metadaten bearbeiten) versucht, eine Sparsespalte in eine kategorische Spalte zu konvertieren.  
  
 Dieser Fehler in Azure Machine Learning tritt auf, wenn das Modul [Edit Metadata](edit-metadata.md) (Metadaten bearbeiten) versucht, eine Sparsespalte in eine kategorische Spalte zu konvertieren.  Sie erhalten diesen Fehler bei dem Versuch, Sparsespalten mit der Option **Make categorical** (Kategorische Spalte erstellen) in kategorische Spalten zu konvertieren.  Azure Machine Learning unterstützt keine platzsparenden kategorischen Arrays, wodurch beim Modul ein Fehler auftritt.  
  
 <!--**Resolution:**
 Make the column dense by using [Convert to Dataset](convert-to-dataset.md) first or do not convert the column to categorical.  -->
  
|Ausnahmemeldungen|  
|------------------------|  
|Sparsespalten können nicht in „Categorical“ (kategorisch) konvertiert werden.|  
  

## <a name="error-0075"></a>Fehler 0075  
Eine Ausnahme tritt auf, wenn bei der Quantisierung eines Datasets eine ungültige Quantisierungsfunktion verwendet wird.  
  
Dieser Fehler in Azure Machine Learning tritt bei dem Versuch auf, Daten mit einer nicht unterstützten Methode zu quantisieren, oder wenn die Parameterkombinationen ungültig sind.  
  
**Lösung:**

Die Fehlerbehandlung für dieses Ereignis wurde in einer früheren Version von Azure Machine Learning eingeführt, die eine umfassendere Anpassung der Quantisierungsmethoden ermöglichte. Derzeit basieren alle Quantisierungsmethoden auf einer Auswahl aus einer Dropdownliste, sodass es technisch nicht mehr möglich sein sollte, diesen Fehler zu erhalten.

 <!--If you get this error when using the [Group Data into Bins](group-data-into-bins.md) module, consider reporting the issue in the [Azure Machine Learning forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=MachineLearning), providing the data types, parameter settings, and the exact error message.  -->
  
|Ausnahmemeldungen|  
|------------------------|  
|Es wurde eine ungültige Quantisierungsfunktion verwendet.|  
  

## <a name="error-0077"></a>Fehler 0077  
 Eine Ausnahme tritt auf, wenn ein unbekannter Schreibmodus der Blobdatei übergeben wird.  
  
 Dieser Fehler in Azure Machine Learning tritt auf, wenn in den Spezifikationen für ein Blobdateiziel oder eine Quelle ein ungültiges Argument übergeben wird.  
  
**Lösung:** In fast allen Modulen, die Daten in Azure-Blobspeicher importieren oder aus diesem exportieren, werden Parameterwerte, die den Schreibmodus steuern, über eine Dropdownliste zugewiesen. Daher ist es nicht möglich, einen ungültigen Wert zu übergeben, und dieser Fehler sollte nicht auftreten. Dieser Fehler ist in einem späteren Release veraltet.  
  
|Ausnahmemeldungen|  
|------------------------|  
|Nicht unterstützter Blobschreibmodus.|  
|Nicht unterstützter Blobschreibmodus: {0}.|  
  

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
|Der Azure-Speichercontainername „{0}“ ist falsch. Es wurde ein Containername im Format „Container/Blob“ erwartet.|  
  

## <a name="error-0080"></a>Fehler 0080  
 Eine Ausnahme tritt auf, wenn die Spalte mit allen fehlenden Werten vom Modul nicht zulässig ist.  
  
 Dieser Fehler in Azure Machine Learning tritt auf, wenn mindestens eine der vom Modul verarbeiteten Spalten alle fehlenden Werte enthält. Wenn ein Modul z. B. Aggregatstatistiken für die einzelnen Spalten berechnet, kann es nicht mit einer Spalte arbeiten, die keine Daten enthält. In solchen Fällen wird die Modulausführung mit dieser Ausnahme angehalten.  
  
**Lösung:** Rufen Sie das Eingabedataset erneut auf, und entfernen Sie alle Spalten, die alle fehlenden Werte enthalten.  
  
|Ausnahmemeldungen|  
|------------------------|  
|Spalten, denen alle Werte fehlen, sind nicht zulässig.|  
|In der Spalte {0} fehlen alle Werte.|  
  

## <a name="error-0081"></a>Fehler 0081  
 Eine Ausnahme tritt im PCA-Modul auf, wenn die Anzahl der zu verringernden Dimensionen gleich der Anzahl der Featurespalten im Eingabedataset ist, die mindestens eine Sparse-Featurespalte enthalten.  
  
 Dieser Fehler in Azure Machine Learning tritt auf, wenn die folgenden Bedingungen erfüllt sind: (a) das Eingabedataset weist mindestens eine Sparsespalte auf und (b) die endgültige Anzahl der angeforderten Dimensionen ist gleich der Anzahl der eingegebenen Dimensionen.  
  
**Lösung:** Erwägen Sie, die Anzahl der Dimensionen in der Ausgabe auf einen Wert zu reduzieren, der kleiner ist als die Anzahl der Dimensionen in der Eingabe. Dies ist typisch in Anwendungen von PCA.   <!--For more information, see [Principal Component Analysis](principal-component-analysis.md).  -->
  
|Ausnahmemeldungen|  
|------------------------|  
|Für Datasets, die Sparse-Featurespalten enthalten, sollte die Anzahl der zu reduzierenden Dimensionen kleiner sein als die Anzahl der Featurespalten.|  
 

## <a name="error-0082"></a>Fehler 0082  
 Eine Ausnahme tritt auf, wenn ein Modell nicht erfolgreich deserialisiert werden kann.  
  
 Dieser Fehler in Azure Machine Learning tritt auf, wenn ein gespeichertes maschinelles Lernmodell oder eine gespeicherte Transformation aufgrund einer fehlerhaften Änderung nicht von einer neueren Version der Azure Machine Learning Runtime geladen werden kann.  
  
**Lösung:** Das Trainingsexperiment, das das Modell oder die Transformation erzeugt hat, muss erneut ausgeführt und das Modell oder die Transformation erneut gespeichert werden.  
  
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
|{0} enthält ungültige Daten für das Training.|  
|{0} enthält ungültige Daten für das Training. Learnertyp: {1}.|  
  

## <a name="error-0084"></a>Fehler 0084  
 Eine Ausnahme tritt auf, wenn die von einem R-Skript erzeugten Bewertungen ausgewertet werden. Dies wird derzeit nicht unterstützt.  
  
 Dieser Fehler in Azure Machine Learning tritt bei dem Versuch auf, eines der Module zum Auswerten eines Modells mit der Ausgabe eines R-Skripts zu verwenden, das Bewertungen enthält.  
  
**Lösung:**
  
|Ausnahmemeldungen|  
|------------------------|  
|Die Auswertung der von R erzeugten Bewertungen wird derzeit nicht unterstützt.|  
  

## <a name="error-0085"></a>Fehler 0085  
 Eine Ausnahme tritt auf, wenn bei der Skriptauswertung ein Fehler auftritt.  
  
 Dieser Fehler in Azure Machine Learning tritt auf, wenn Sie ein benutzerdefiniertes Skript ausführen, das Syntaxfehler enthält.  
  
**Lösung:** Öffnen Sie Ihren Code in einem externen Editor, und prüfen Sie ihn auf Fehler.  
  
|Ausnahmemeldungen|  
|------------------------|  
|Fehler bei der Auswertung des Skripts.|  
|Der folgende Fehler ist während der Skriptauswertung aufgetreten. Weitere Informationen finden Sie im Ausgabeprotokoll: -------------- Start der Fehlermeldung vom {0}-Interpreter ---------- {1} -------------- Ende der Fehlermeldung vom {0}-Interpreter ----------|  
  

## <a name="error-0086"></a>Fehler 0086  
 Eine Ausnahme tritt auf, wenn eine Zähltransformation ungültig ist.  
  
 Dieser Fehler in Azure Machine Learning tritt auf, wenn Sie eine Transformation basierend auf einer Zahlentabelle auswählen, die ausgewählte Transformation jedoch mit den aktuellen Daten oder mit der neuen Zahlentabelle nicht kompatibel ist.  
  
**Lösung:** Das Modul unterstützt das Speichern der Zahlen und Regeln, aus denen sich die Transformation zusammensetzt, in zwei verschiedenen Formaten. Stellen Sie beim Zusammenführen von Zahlentabellen sicher, dass beide zusammenzuführenden Tabellen dasselbe Format verwenden.  
  
Im Allgemeinen kann eine zahlenbasierte Transformation nur auf Datasets angewendet werden, die dasselbe Schema aufweisen wie das Dataset, auf dessen Grundlage die Transformation ursprünglich erstellt wurde.  
  
 <!-- For general information, see [Learning with Counts](data-transformation-learning-with-counts.md). For requirements specific to creating and merging count-based features, see these topics:  
  
-   [Merge Count Transform](merge-count-transform.md)  
  
-   [Import Count Table](import-count-table.md)  
  
-   [Modify Count Table Parameters](modify-count-table-parameters.md)  
  -->
|Ausnahmemeldungen|  
|------------------------|  
|Ungültige Zähltransformation angegeben.|  
|Die Zähltransformation am Eingangsport „{0}“ ist ungültig.|  
|Die Zähltransformation am Eingangsport „{0}“ kann nicht mit der Zähltransformation am Eingangsport „{1}“ zusammengeführt werden. Überprüfen Sie die für die Zählung der Übereinstimmungen verwendeten Metadaten.|  
  

## <a name="error-0087"></a>Fehler 0087  
 Eine Ausnahme tritt auf, wenn ein ungültiger Typ der Zahlentabelle für das Lernen mit Zählmodulen angegeben wird.  
  
 Dieser Fehler in Azure Machine Learning tritt bei dem Versuch auf, eine vorhandene Zahlentabelle zu importieren, wenn die Tabelle mit den aktuellen Daten oder mit der neuen Zahlentabelle nicht kompatibel ist.  
  
**Lösung:** Es gibt verschiedene Formate für die Speicherung der Zahlen und Regeln, die die Transformation bilden. Wenn Sie Zahlentabellen zusammenführen, stellen Sie sicher, dass beide dasselbe Format verwenden.  
  
 Im Allgemeinen kann eine zahlenbasierte Transformation nur auf Datasets angewendet werden, die dasselbe Schema aufweisen wie das Dataset, auf dessen Grundlage die Transformation ursprünglich erstellt wurde.  
  
  <!--For general information, see [Learning with Counts](data-transformation-learning-with-counts.md). -->
  

## <a name="error-0088"></a>Fehler 0088  
 Eine Ausnahme tritt auf, wenn ein ungültiger Zählungstyp für das Lernen mit Zählmodulen angegeben wird.  
  
 Dieser Fehler in Azure Machine Learning tritt bei dem Versuch auf, eine andere Zählmethode zu verwenden, als bei der zahlenbasierten Featurebereitstellung unterstützt wird.  
  
**Lösung:** Im Allgemeinen werden die Zählmethoden aus einer Dropdownliste ausgewählt, sodass dieser Fehler nicht angezeigt werden sollte.  
  
  <!--For general information, see [Learning with Counts](data-transformation-learning-with-counts.md). For requirements specific to creating and merging count-based features, see these topics:  
  
-   [Merge Count Transform](merge-count-transform.md)  
  
-   [Import Count Table](import-count-table.md)  
  
-   [Modify Count Table Parameters](modify-count-table-parameters.md)  
  -->
|Ausnahmemeldungen|  
|------------------------|  
|Es wurde ein ungültiger Zählungstyp angegeben.|  
|Der angegebene Zählungstyp „{0}“ ist kein gültiger Zählungstyp.|  
  

## <a name="error-0089"></a>Fehler 0089  
 Eine Ausnahme tritt auf, wenn die angegebene Anzahl von Klassen kleiner ist als die tatsächliche Anzahl von Klassen in einem zum Zählen verwendeten Dataset.  
  
 Dieser Fehler in Azure Machine Learning tritt auf, wenn Sie eine Zahlentabelle erstellen und die Bezeichnungsspalte eine andere Anzahl von Klassen enthält, als Sie in den Modulparametern angegeben haben.  
  
**Lösung:** Überprüfen Sie Ihr Dataset und ermitteln Sie genau, wie viele verschiedene Werte (mögliche Klassen) es in der Bezeichnungsspalte gibt. Beim Erstellen der Zahlentabelle müssen Sie mindestens diese Anzahl von Klassen angeben.  
  
 Die Zahlentabelle kann die Anzahl der verfügbaren Klassen nicht automatisch ermitteln.  
  
 Wenn Sie die Zahlentabelle erstellen, können Sie weder 0 noch eine Zahl angeben, die kleiner ist als die tatsächliche Anzahl der Klassen in der Bezeichnungsspalte.  
  
|Ausnahmemeldungen|  
|------------------------|  
|Die Anzahl der Klassen ist falsch. Stellen Sie sicher, dass die Anzahl der Klassen, die Sie im Parameterbereich angeben, größer als oder gleich der Anzahl der Klassen in der Bezeichnungsspalte ist.|  
|Die Anzahl der angegebenen Klassen ist „{0}“, die nicht größer ist als ein Bezeichnungswert „{1}“ in dem zum Zählen verwendeten Dataset. Stellen Sie sicher, dass die Anzahl der Klassen, die Sie im Parameterbereich angeben, größer als oder gleich der Anzahl der Klassen in der Bezeichnungsspalte ist.|  
  

## <a name="error-0090"></a>Fehler 0090  
 Eine Ausnahme tritt auf, wenn bei der Erstellung der Hive-Tabelle ein Fehler auftritt.  
  
 Dieser Fehler in Azure Machine Learning tritt auf, wenn Sie [Export Data](export-data.md) (Daten exportieren) oder eine andere Option zum Speichern von Daten in einem HDInsight-Cluster verwenden und die angegebene Hive-Tabelle nicht erstellt werden kann.  
  
**Lösung:** Überprüfen Sie den dem Cluster zugeordneten Azure-Speicherkontonamen und stellen Sie sicher, dass Sie dasselbe Konto in den Moduleigenschaften verwenden.  
  
|Ausnahmemeldungen|  
|------------------------|  
|Die Hive-Tabelle konnte nicht erstellt werden. Stellen Sie für einen HDInsight-Cluster sicher, dass der dem Cluster zugeordnete Azure-Speicherkontoname mit dem identisch ist, der über den Modulparameter übergeben wird.|  
|Die Hive-Tabelle „{0}“ konnte nicht erstellt werden. Stellen Sie für einen HDInsight-Cluster sicher, dass der dem Cluster zugeordnete Azure-Speicherkontoname mit dem identisch ist, der über den Modulparameter übergeben wird.|  
|Die Hive-Tabelle „{0}“ konnte nicht erstellt werden. Stellen Sie für einen HDInsight-Cluster sicher, dass der dem Cluster zugeordnete Azure-Speicherkontoname „{1}“ lautet.|  
 

## <a name="error-0100"></a>Fehler 0100  
 Eine Ausnahme tritt auf, wenn für ein benutzerdefiniertes Modul eine nicht unterstützte Sprache angegeben wird.  
  
 Dieser Fehler in Azure Machine Learning tritt auf, wenn ein benutzerdefiniertes Modul erstellt wird und die Namenseigenschaft des Elements **Language** (Sprache) in einer XML-Definitionsdatei für benutzerdefinierte Module einen ungültigen Wert aufweist. Derzeit ist der einzige gültige Wert für diese Eigenschaft `R`. Beispiel:  
  
 `<Language name="R" sourceFile="CustomAddRows.R" entryPoint="CustomAddRows" />`  
  
**Lösung:** Überprüfen Sie, ob die Namenseigenschaft des Elements **Language** (Sprache) in der benutzerdefinierten XML-Definitionsdatei des benutzerdefinierten Moduls auf `R` festgelegt ist. Speichern Sie die Datei, aktualisieren Sie das ZIP-Paket des benutzerdefinierten Moduls und versuchen Sie, das benutzerdefinierte Modul erneut hinzuzufügen.  
  
|Ausnahmemeldungen|  
|------------------------|  
|Es wurde eine nicht unterstützte Sprache für das benutzerdefinierte Modul angegeben.|  
  

## <a name="error-0101"></a>Fehler 0101  
 Alle Port- und Parameter-IDs müssen eindeutig sein.  
  
 Dieser Fehler in Azure Machine Learning tritt auf, wenn mindestens einem Port oder Parameter in einer XML-Definitionsdatei eines benutzerdefinierten Moduls derselbe ID-Wert zugewiesen wurde.  
  
**Lösung:** Überprüfen Sie, ob die ID-Werte über alle Ports und Parameter hinweg eindeutig sind. Speichern Sie die XML-Datei, aktualisieren Sie das ZIP-Paket des benutzerdefinierten Moduls und versuchen Sie, das benutzerdefinierte Modul erneut hinzuzufügen.  
  
|Ausnahmemeldungen|  
|------------------------|  
|Alle Port- und Parameter-IDs für ein Modul müssen eindeutig sein.|  
|Das Modul „{0}“ weist doppelte Port-/Argument-IDs auf. Alle Port-/Argument-IDs müssen für ein Modul eindeutig sein.|  
  

## <a name="error-0102"></a>Fehler 0102  
 Wird ausgelöst, wenn eine ZIP-Datei nicht extrahiert werden kann.  
  
 Dieser Fehler in Azure Machine Learning tritt auf, wenn Sie ein gezipptes Paket mit der Erweiterung ZIP importieren, aber das Paket entweder keine ZIP-Datei ist, oder die Datei kein unterstütztes ZIP-Format verwendet.  
  
**Lösung:** Stellen Sie sicher, dass es sich bei der ausgewählten Datei um eine gültige ZIP-Datei handelt und dass sie mit einem der unterstützten Komprimierungsalgorithmen komprimiert wurde.  
  
 Wenn Sie beim Importieren von Datasets in komprimiertem Format diesen Fehler erhalten, überprüfen Sie, ob alle enthaltenen Dateien eines der unterstützten Dateiformate verwenden und im Unicode-Format vorliegen.  <!--For more information, see [Unpack Zipped Datasets](unpack-zipped-datasets.md).  -->
  
 Versuchen Sie, die gewünschten Dateien in einen neuen komprimierten gezippten Ordner zu lesen und versuchen Sie, das benutzerdefinierte Modul erneut hinzuzufügen.  
  
|Ausnahmemeldungen|  
|------------------------|  
|Die angegebene ZIP-Datei weist nicht das richtige Format auf.|  


## <a name="error-0103"></a>Fehler 0103  
 Wird ausgelöst, wenn eine ZIP-Datei keine XML-Dateien enthält.  
  
 Dieser Fehler in Azure Machine Learning tritt auf, wenn das ZIP-Paket für das benutzerdefinierte Modul keine Moduldefinitionsdateien (XML) enthält. Diese Dateien müssen sich im Stammverzeichnis des ZIP-Pakets befinden (z. B. nicht in einem Unterordner).  
  
**Lösung:** Überprüfen Sie, ob sich eine oder mehrere XML-Moduldefinitionsdateien im Stammordner des ZIP-Pakets befinden, indem Sie sie in einen temporären Ordner auf Ihrem Laufwerk extrahieren. Alle XML-Dateien sollten sich direkt in dem Ordner befinden, in den Sie das ZIP-Paket extrahiert haben. Stellen Sie beim Erstellen des ZIP-Pakets sicher, dass Sie keinen Ordner auswählen, der zu verpackende XML-Dateien enthält, da dadurch ein Unterordner innerhalb des ZIP-Pakets mit demselben Namen wie der Ordner erstellt wird, den Sie zum Packen ausgewählt haben.  
  
|Ausnahmemeldungen|  
|------------------------|  
|Die angegebene ZIP-Datei enthält keine Moduldefinitionsdateien (XML-Dateien).|  


## <a name="error-0104"></a>Fehler 0104  
 Wird ausgelöst, wenn eine Moduldefinitionsdatei auf ein Skript verweist, das nicht gefunden werden kann.  
  
 Dieser Fehler in Azure Machine Learning tritt auf, wenn eine benutzerdefinierte XML-Moduldefinitionsdatei auf eine Skriptdatei im **Language**-Element (Sprache) verweist, die im ZIP-Paket nicht vorhanden ist. Der Pfad der Skriptdatei ist in der **sourceFile**-Eigenschaft des Elements **Language** (Sprache) definiert. Der Pfad zur Quelldatei ist relativ zum Stammverzeichnis des ZIP-Pakets (derselbe Speicherort wie die XML-Moduldefinitionsdateien). Wenn sich die Skriptdatei in einem Unterordner befindet, muss der relative Pfad zur Skriptdatei angegeben werden. Wenn z. B. alle Skripts in einem Ordner **myScripts** innerhalb des ZIP-Pakets gespeichert wären, müsste das Element **Language** diesen Pfad zur **sourceFile**-Eigenschaft wie folgt hinzufügen. Beispiel:  
  
 `<Language name="R" sourceFile="myScripts/CustomAddRows.R" entryPoint="CustomAddRows" />`  
  
**Lösung:** Stellen Sie sicher, dass der Wert der **sourceFile**-Eigenschaft im Element **Language** der benutzerdefinierten XML-Definition des benutzerdefinierten Moduls richtig ist und dass die Quelldatei im richtigen relativen Pfad im ZIP-Paket vorhanden ist.  
  
|Ausnahmemeldungen|  
|------------------------|  
|Die verwiesene R-Skriptdatei ist nicht vorhanden.|  
|Die verwiesene R-Skriptdatei „{0}“ konnte nicht gefunden werden. Stellen Sie sicher, dass der relative Pfad zur Datei vom Speicherort der Definitionen richtig ist.|  


## <a name="error-0105"></a>Fehler 0105  
 Dieser Fehler wird angezeigt, wenn eine Moduldefinitionsdatei einen nicht unterstützten Parametertyp enthält.  
  
 Dieser Fehler in Azure Machine Learning tritt auf, wenn Sie eine XML-Definition für ein benutzerdefiniertes Modul erstellen und der Typ eines Parameters oder Arguments in der Definition nicht mit einem unterstützten Typ übereinstimmt.  
  
**Lösung:** Stellen Sie sicher, dass die Typeigenschaft eines beliebigen **Arg**-Elements in der XML-Definitionsdatei für das benutzerdefinierte Modul ein unterstützter Typ ist.  
  
|Ausnahmemeldungen|  
|------------------------|  
|Nicht unterstützter Parametertyp.|  
|Es wurde der nicht unterstützte Parametertyp „{0}“ angegeben.|  


## <a name="error-0106"></a>Fehler 0106  
 Wird ausgelöst, wenn eine Moduldefinitionsdatei einen nicht unterstützten Eingabetyp definiert.  
  
 Dieser Fehler in Azure Machine Learning tritt auf, wenn der Typ eines Eingangsports in einer XML-Definition eines benutzerdefinierten Moduls nicht mit einem unterstützten Typ übereinstimmt.  
  
**Lösung:** Stellen Sie sicher, dass die Typeigenschaft eines Eingabeelements in der XML-Definitionsdatei des benutzerdefinierten Moduls ein unterstützter Typ ist.  
  
|Ausnahmemeldungen|  
|------------------------|  
|Nicht unterstützter Eingabetyp.|  
|Es wurde ein nicht unterstützter Eingabetyp „{0}“ angegeben.|  


## <a name="error-0107"></a>Fehler 0107  
 Wird ausgelöst, wenn eine Moduldefinitionsdatei einen nicht unterstützten Ausgabetyp definiert.  
  
 Dieser Fehler in Azure Machine Learning tritt auf, wenn der Typ eines Ausgangsports in einer XML-Definition eines benutzerdefinierten Moduls nicht mit einem unterstützten Typ übereinstimmt.  
  
**Lösung:** Stellen Sie sicher, dass die Typeigenschaft eines Ausgabeelements in der XML-Definitionsdatei des benutzerdefinierten Moduls ein unterstützter Typ ist.  
  
|Ausnahmemeldungen|  
|------------------------|  
|Nicht unterstützter Ausgabetyp.|  
|Es wurde ein nicht unterstützter Ausgabetyp „{0}“ angegeben.|  


## <a name="error-0108"></a>Fehler 0108  
 Wird ausgelöst, wenn eine Moduldefinitionsdatei mehr Ein- oder Ausgangsports definiert, als unterstützt werden.  
  
 Dieser Fehler in Azure Machine Learning tritt auf, wenn zu viele Ein- oder Ausgangsports in einer XML-Definition eines benutzerdefinierten Moduls definiert sind.  
  
**Lösung:** Stellt sicher, dass die maximale Anzahl der in der XML-Definition des benutzerdefinierten Moduls definierten Ein- und Ausgangsports nicht die maximale Anzahl der unterstützten Ports überschreitet.  
  
|Ausnahmemeldungen|  
|------------------------|  
|Die Anzahl der unterstützten Ein- und Ausgangsports wurde überschritten.|  
|Die Anzahl der unterstützten „{0}“ Ports wurde überschritten. Die maximal zulässige Anzahl von „{0}“-Ports ist „{1}“.| 

## <a name="error-0109"></a>Fehler 0109  
 Wird ausgelöst, wenn eine Moduldefinitionsdatei eine Spaltenauswahl nicht richtig definiert.  
  
 Dieser Fehler in Azure Machine Learning tritt auf, wenn die Syntax für ein Spaltenauswahlargument einen Fehler in einer XML-Definition eines benutzerdefinierten Moduls enthält.  
  
**Lösung:** Dieser Fehler tritt auf, wenn die Syntax für ein Spaltenauswahlargument einen Fehler in einer XML-Definition eines benutzerdefinierten Moduls enthält.  
  
|Ausnahmemeldungen|  
|------------------------|  
|Nicht unterstützte Syntax für die Spaltenauswahl.|  
  

## <a name="error-0110"></a>Fehler 0110  
 Wird ausgelöst, wenn eine Moduldefinitionsdatei eine Spaltenauswahl definiert, die auf eine nicht vorhandene Eingangsport-ID verweist.  
  
 Dieser Fehler in Azure Machine Learning tritt auf, wenn die *portId*-Eigenschaft innerhalb des Properties-Elements eines Arg-Elements vom Typ „ColumnPicker“ nicht mit dem ID-Wert eines Eingangsports übereinstimmt.  
  
**Lösung:** Stellen Sie sicher, dass die portId-Eigenschaft mit dem ID-Wert eines in der XML-Definition des benutzerdefinierten Moduls definierten Eingangsports übereinstimmt.  
  
|Ausnahmemeldungen|  
|------------------------|  
|Die Spaltenauswahl verweist auf eine nicht vorhandene Eingangsport-ID.|  
|Die Spaltenauswahl verweist auf eine nicht vorhandene Eingangsport-ID „{0}“.|  
  

## <a name="error-0111"></a>Fehler 0111  
 Wird ausgelöst, wenn eine Moduldefinitionsdatei eine ungültige Eigenschaft definiert.  
  
 Dieser Fehler in Azure Machine Learning tritt auf, wenn einem Element in der XML-Definition des benutzerdefinierten Moduls eine ungültige Eigenschaft zugewiesen wird.  
  
**Lösung:** Stellen Sie sicher, dass die Eigenschaft vom benutzerdefinierten Modulelement unterstützt wird.  
  
|Ausnahmemeldungen|  
|------------------------|  
|Die Eigenschaftsdefinition ist ungültig.|  
|Die Eigenschaftsdefinition „{0}“ ist ungültig.|  
  

## <a name="error-0112"></a>Fehler 0112  
 Wird ausgelöst, wenn eine Moduldefinitionsdatei nicht analysiert werden kann.  
  
 Dieser Fehler in Azure Machine Learning tritt auf, wenn ein Fehler im XML-Format vorliegt, der verhindert, dass die XML-Definition des benutzerdefinierten Moduls als gültige XML-Datei analysiert wird.  
  
**Lösung:** Stellen Sie sicher, dass jedes Element ordnungsgemäß geöffnet und geschlossen wird. Stellen Sie sicher, dass die XML-Formatierung keine Fehler aufweist.  
  
|Ausnahmemeldungen|  
|------------------------|  
|Die Moduldefinitionsdatei konnte nicht analysiert werden.|  
|Die Moduldefinitionsdatei „{0}“ konnte nicht analysiert werden.|  
  

## <a name="error-0113"></a>Fehler 0113  
 Wird ausgelöst, wenn eine Moduldefinitionsdatei Fehler enthält.  
  
 Dieser Fehler in Azure Machine Learning tritt auf, wenn die XML-Definitionsdatei des benutzerdefinierten Moduls analysiert werden kann, aber Fehler enthält, z. B. eine nicht von benutzerdefinierten Modulen unterstützte Definition von Elementen.  
  
**Lösung:** Stellen Sie sicher, dass die Definitionsdatei für benutzerdefinierte Module Elemente und Eigenschaften definiert, die von benutzerdefinierten Modulen unterstützt werden.  
  
|Ausnahmemeldungen|  
|------------------------|  
|Die Moduldefinitionsdatei enthält Fehler.|  
|Die Moduldefinitionsdatei „{0}“ enthält Fehler.|  
|Die Moduldefinitionsdatei „{0}“ enthält Fehler. [https://login.microsoftonline.com/tfp/00000000-0000-0000-0000-000000000000/b2c_1a_tp_sign-up-or-sign-in/v2.0/]({1})|  
  

## <a name="error-0114"></a>Fehler 0114  
 Wird ausgelöst, wenn beim Erstellen eines benutzerdefinierten Moduls ein Fehler auftritt.  
  
 Dieser Fehler in Azure Machine Learning tritt auf, wenn beim Erstellen eines benutzerdefinierten Moduls ein Fehler erkannt wird. Dies tritt auf, wenn beim Hinzufügen des benutzerdefinierten Moduls mindestens ein auf das benutzerdefinierte Modul bezogener Fehler erkannt wird. Die weiteren Fehler werden in dieser Fehlermeldung angezeigt.  
  
**Lösung:** Beheben Sie die in dieser Ausnahmemeldung gemeldeten Fehler.  
  
|Ausnahmemeldungen|  
|------------------------|  
|Fehler beim Erstellen des benutzerdefinierten Moduls.|  
|Fehler beim Erstellen des benutzerdefinierten Moduls: {0}|  
  

## <a name="error-0115"></a>Fehler 0115  
 Wird ausgelöst, wenn ein Standardskript für ein benutzerdefiniertes Modul eine nicht unterstützte Erweiterung aufweist.  
  
 Dieser Fehler in Azure Machine Learning tritt auf, wenn Sie ein Skript für ein benutzerdefiniertes Modul bereitstellen, das eine unbekannte Dateierweiterung verwendet.  
  
**Lösung:** Überprüfen Sie das Dateiformat und die Dateierweiterung aller Skriptdateien, die im benutzerdefinierten Modul enthalten sind.  
  
|Ausnahmemeldungen|  
|------------------------|  
|Nicht unterstützte Erweiterung für Standardskript.|  
|Nicht unterstützte Dateierweiterung „{0}“ für Standardskript.|  
  

## <a name="error-0121"></a>Fehler 0121  
 Wird ausgelöst, wenn SQL-Schreibfehler auftreten, da nicht in die Tabelle geschrieben werden kann.  
  
 Dieser Fehler in Azure Machine Learning tritt auf, wenn Sie das Modul [Export Data](export-data.md) (Daten exportieren) verwenden, um Ergebnisse in einer Tabelle in einer SQL-Datenbank zu speichern, und nicht in die Tabelle geschrieben werden kann. Normalerweise wird dieser Fehler angezeigt, wenn das Modul [Export Data](export-data.md) (Daten exportieren) erfolgreich eine Verbindung mit der SQL Server-Instanz herstellt, dann aber den Inhalt des Azure ML-Datasets nicht in die Tabelle schreiben kann.  
  
**Lösung:**
 - Öffnen Sie den Eigenschaftenbereich des Moduls [Export Data](export-data.md) (Daten exportieren) und überprüfen Sie, ob die Datenbank- und Tabellennamen ordnungsgemäß eingegeben wurden. 
 - Überprüfen Sie das Schema des zu exportierenden Datasets und stellen Sie sicher, dass die Daten mit der Zieltabelle kompatibel sind.
 - Überprüfen Sie, ob die SQL-Anmeldung, die mit dem Benutzernamen und dem Kennwort verknüpft ist, über die Berechtigung zum Schreiben in die Tabelle verfügt. 
 - Wenn die Ausnahme zusätzliche Fehlerinformationen von SQL Server enthält, verwenden Sie diese Informationen, um Korrekturen vorzunehmen.  
  
|Ausnahmemeldungen|  
|------------------------|  
|Mit Server verbunden, es kann aber nicht in die Tabelle geschrieben werden.|  
|Es kann nicht in die SQL-Tabelle geschrieben werden: {0}|  


## <a name="error-0122"></a>Fehler 0122  
 Eine Ausnahme tritt auf, wenn mehrere Gewichtungsspalten angegeben sind und nur eine einzelne Spalte zulässig ist.  
  
 Dieser Fehler in Azure Machine Learning tritt auf, wenn zu viele Spalten als Gewichtungsspalten ausgewählt wurden.  
  
**Lösung:** Überprüfen Sie das Eingabedataset und dessen Metadaten. Stellen Sie sicher, dass nur eine Spalte Gewichtungen enthält.  
  
|Ausnahmemeldungen|  
|------------------------|  
|Es sind mehrere Gewichtungsspalten angegeben.|  


## <a name="error-0123"></a>Fehler 0123  
 Eine Ausnahme tritt auf, wenn die Spalte der Vektoren als Bezeichnungsspalte angegeben ist.  
  
 Dieser Fehler in Azure Machine Learning tritt auf, wenn Sie einen Vektor als Bezeichnungsspalte verwenden.  
  
**Lösung:** Ändern Sie bei Bedarf das Datenformat der Spalte, oder wählen Sie eine andere Spalte aus.  
  
|Ausnahmemeldungen|  
|------------------------|  
|Die Spalte der Vektoren ist als Bezeichnungsspalte angegeben.|  


## <a name="error-0124"></a>Fehler 0124  
 Eine Ausnahme tritt auf, wenn nicht numerische Spalten als Gewichtungsspalte angegeben sind.  
  
**Lösung:**
  
|Ausnahmemeldungen|  
|------------------------|  
|Die nicht numerische Spalte ist als Gewichtungsspalte angegeben.|  
  


## <a name="error-0125"></a>Fehler 0125  
 Wird ausgelöst, wenn das Schema für mehrere Datasets nicht übereinstimmt.  
  
**Lösung:**
  
|Ausnahmemeldungen|  
|------------------------|  
|Das Datasetschema stimmt nicht überein.|  


## <a name="error-0126"></a>Fehler 0126  
 Eine Ausnahme tritt auf, wenn der Benutzer eine SQL-Domäne angibt, die in Azure ML nicht unterstützt wird.  
  
 Dieser Fehler tritt auf, wenn der Benutzer eine SQL-Domäne angibt, die von Azure Machine Learning nicht unterstützt wird. Sie erhalten diesen Fehler bei dem Versuch, eine Verbindung mit einem Datenbankserver in einer Domäne herzustellen, die nicht in der Whitelist enthalten ist. Derzeit sind die zulässigen SQL-Domänen: „.database.windows.net“, „.cloudapp.net“ oder „.database.secure.windows.net“. Das heißt, der Server muss ein Azure SQL-Server oder ein Server auf einem virtuellen Computer in Azure sein.  
  
**Lösung:** Rufen Sie das Modul erneut auf. Überprüfen Sie, ob der SQL-Datenbankserver zu einer der akzeptierten Domänen gehört:  
  
-   .database.windows.net  
  
-   .cloudapp.net  
  
-   .database.secure.windows.net  
  
|Ausnahmemeldungen|  
|------------------------|  
|Nicht unterstützte SQL-Domäne.|  
|Die SQL-Domäne {0} wird derzeit in Azure ML nicht unterstützt.|  
  

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
|Die Bildpixelgröße in der Datei „{0}“ überschreitet den zulässigen Grenzwert: „{1}“|  


## <a name="error-0128"></a>Fehler 0128  
 Die Anzahl der bedingten Wahrscheinlichkeiten für kategorische Spalten überschreitet den Grenzwert.  
  
**Lösung:**
  
|Ausnahmemeldungen|  
|------------------------|  
|Die Anzahl der bedingten Wahrscheinlichkeiten für kategorische Spalten überschreitet den Grenzwert.|  
|Die Anzahl der bedingten Wahrscheinlichkeiten für kategorische Spalten überschreitet den Grenzwert. Die Spalten „{0}“ und „{1}“ sind das problembehaftete Paar.|  


## <a name="error-0129"></a>Fehler 0129  
 Die Anzahl der Spalten im Dataset überschreitet den zulässigen Grenzwert.  
  
**Lösung:**
  
|Ausnahmemeldungen|  
|------------------------|  
|Die Anzahl der Spalten im Dataset überschreitet den zulässigen Grenzwert.|  
|Die Anzahl der Spalten im Dataset in „{0}“ überschreitet den zulässigen Grenzwert.|  
|Die Anzahl der Spalten im Dataset in „{0}“ überschreitet den zulässigen Grenzwert von „{1}“.|  
|Die Anzahl der Spalten im Dataset in „{0}“ überschreitet den zulässigen „{1}“-Grenzwert von „{2}“.|  
## <a name="error-0130"></a>Fehler 0130  
 Eine Ausnahme tritt auf, wenn alle Zeilen des Trainingsdatasets fehlende Werte enthalten.  
  
 Dies tritt auf, wenn eine Spalte im Trainingsdataset leer ist.  
  
**Lösung:** Verwenden Sie das Modul [Clean Missing Data](clean-missing-data.md) (Fehlende Daten bereinigen), um Spalten mit allen fehlenden Werten zu entfernen.  
  
|Ausnahmemeldungen|  
|------------------------|  
|Alle Zeilen im Trainingsdataset enthalten fehlende Werte.  Erwägen Sie, das Modul „Clean Missing Data“ (Fehlende Daten bereinigen) zu verwenden, um fehlende Werte zu entfernen.|  
 

## <a name="error-0131"></a>Fehler 0131  
 Eine Ausnahme tritt auf, wenn mindestens ein Dataset in einer ZIP-Datei nicht entzippt und ordnungsgemäß registriert werden kann.  
  
 Dieser Fehler tritt auf, wenn mindestens ein Dataset in einer ZIP-Datei nicht entzippt und ordnungsgemäß gelesen werden kann. Sie erhalten diesen Fehler, wenn beim Entpacken ein Fehler erkannt wird, da die ZIP-Datei selbst oder eine der darin enthaltenen Dateien beschädigt ist. Sie erhalten den Fehler auch, wenn beim Entpacken und Erweitern einer Datei ein Systemfehler auftritt.  
  
**Lösung:** Verwenden Sie die in der Fehlermeldung angegebenen Details, um die weitere Vorgehensweise zu ermitteln.  
  
|Ausnahmemeldungen|  
|------------------------|  
|Fehler beim Hochladen gezippter Datasets.|  
|Fehler beim gezippten Dataset {0} mit folgender Meldung: {1}|  
|Fehler beim gezippten Dataset {0} mit einer {1}-Ausnahme mit folgender Meldung: {2}|  
  

## <a name="error-0132"></a>Fehler 0132  
 Für das Entpacken wurde kein Dateiname angegeben. Es wurden mehrere Dateien in der ZIP-Datei gefunden.  
  
 Dieser Fehler tritt auf, wenn für das Entpacken kein Dateiname angegeben wurde. Es wurden mehrere Dateien in der ZIP-Datei gefunden. Sie erhalten diesen Fehler, wenn die ZIP-Datei mehr als eine komprimierte Datei enthält, aber Sie im Textfeld **Dataset to Unpack** (Zu entpackendes Dataset) im Bereich **Property** (Eigenschaft) des Moduls keine Datei zum Extrahieren angegeben haben. Derzeit kann bei jedem Modulstart nur eine Datei extrahiert werden.  
  
**Lösung:** Die Fehlermeldung stellt eine Liste der Dateien bereit, die sich in der ZIP-Datei befinden. Kopieren Sie den Namen der gewünschten Datei, und fügen Sie sie in das Textfeld **Dataset to Unpack** (Zu entpackendes Dataset) ein.  
  
|Ausnahmemeldungen|  
|------------------------|  
|Die ZIP-Datei enthält mehrere Dateien. Sie müssen die zu erweiternde Datei angeben.|  
|Die Datei enthält mehr als eine Datei. Geben Sie die zu erweiternde Datei an. Die folgenden Dateien wurden gefunden: {0}|  
  

## <a name="error-0133"></a>Fehler 0133  
 Die angegebene Datei wurde in der ZIP-Datei nicht gefunden.  
  
 Dieser Fehler tritt auf, wenn der im Feld **Dataset to Unpack** (Zu entpackendes Dataset) des Bereichs **Property** (Eigenschaft) eingegebene Dateiname nicht mit dem Namen einer Datei in der ZIP-Datei übereinstimmt. Die häufigsten Ursachen für diesen Fehler sind Tippfehler oder die Suche nach der falschen Archivdatei für die zu erweiternde Datei.  
  
**Lösung:** Rufen Sie das Modul erneut auf. Wenn der Name der zu entpackenden Datei in der Liste der gefundenen Dateien angezeigt wird, kopieren Sie den Dateinamen und fügen Sie ihn in das Eigenschaftsfeld **Dataset to Unpack** (Zu entpackendes Dataset) ein. Wenn der gewünschte Dateiname nicht in der Liste angezeigt wird, überprüfen Sie, ob Sie über die richtige ZIP-Datei und den richtigen Namen für die gewünschte Datei verfügen.  
  
|Ausnahmemeldungen|  
|------------------------|  
|Die angegebene Datei wurde in der ZIP-Datei nicht gefunden.|  
|Die angegebene Datei wurde nicht gefunden. Folgende Datei(en) wurde(n) gefunden: {0}|  
  

## <a name="error-0134"></a>Fehler 0134
Eine Ausnahme tritt auf, wenn die Bezeichnungsspalte fehlt oder die Anzahl der bezeichneten Zeilen nicht ausreicht.  
  
Dieser Fehler tritt auf, wenn das Modul eine Bezeichnungsspalte erfordert, Sie aber keine in die Spaltenauswahl einbezogen haben, oder wenn der Bezeichnungsspalte zu viele Werte fehlen.

Dieser Fehler kann auch auftreten, wenn eine vorhergehende Operation das Dataset so ändert, dass für eine Downstream-Operation nicht ausreichend Zeilen zur Verfügung stehen. Nehmen Sie z. B. an, Sie verwenden einen Ausdruck im Modul **Partition and Sample** (Partition und Beispiel), um ein Dataset durch Werte zu teilen. Wenn für Ihren Ausdruck keine Übereinstimmungen gefunden werden, ist eines der Datasets, die sich aus der Partition ergeben, leer.

Lösung: 

 Wenn Sie eine Bezeichnungsspalte in die Spaltenauswahl aufnehmen, sie aber nicht erkannt wird, verwenden Sie das Modul [Edit Metadata](edit-metadata.md) (Metadaten bearbeiten), um sie als Bezeichnungsspalte zu kennzeichnen.
  
  <!--Use the [Summarize Data](summarize-data.md) module to generate a report that shows how many values are missing in each column. -->Anschließend können Sie das Modul [Clean Missing Data](clean-missing-data.md) (Fehlende Daten bereinigen) verwenden, um Zeilen mit fehlenden Werten in der Bezeichnungsspalte zu entfernen. 

 Überprüfen Sie Ihre Eingabedatasets, um sicherzustellen, dass sie gültige Daten und ausreichend Zeilen enthalten, um die Anforderungen der Operation zu erfüllen. Viele Algorithmen erzeugen eine Fehlermeldung, wenn sie eine Mindestanzahl von Datenzeilen benötigen, aber die Daten nur wenige Zeilen oder nur eine Kopfzeile enthalten.
  
|Ausnahmemeldungen|
|------------------------|
|Eine Ausnahme tritt auf, wenn die Bezeichnungsspalte fehlt oder die Anzahl der bezeichneten Zeilen nicht ausreicht.|  
|Eine Ausnahme tritt auf, wenn die Bezeichnungsspalte fehlt oder weniger als {0} bezeichnete Zeilen aufweist.|  
  

## <a name="error-0135"></a>Fehler 0135  
 Es wird nur ein schwerpunktbasierter Cluster unterstützt.  
  
**Lösung:** Diese Fehlermeldung kann bei dem Versuch der Bewertung eines Clusteringmodells auftreten, das auf einem benutzerdefinierten Clusteringalgorithmus basiert, der keine Schwerpunkte zur Initialisierung des Clusters verwendet.  
  
  <!--You can use [Evaluate Model](evaluate-model.md) to evaluate clustering models that are based on the  [K-Means Clustering](k-means-clustering.md) module. For custom algorithms, use the [Execute R Script](execute-r-script.md) module to create a custom evaluation script.  -->
  
|Ausnahmemeldungen|  
|------------------------|  
|Es wird nur ein schwerpunktbasierter Cluster unterstützt.|  
  

## <a name="error-0136"></a>Fehler 0136  
 Es wurde kein Dateiname zurückgegeben. Die Datei konnte daher nicht verarbeitet werden.  
  
**Lösung:**
  
|Ausnahmemeldungen|  
|------------------------|  
|Es wurde kein Dateiname zurückgegeben. Die Datei konnte daher nicht verarbeitet werden.|  
  

## <a name="error-0137"></a>Fehler 0137  
 Das Azure Storage SDK hat beim Lesen oder Schreiben einen Fehler bei der Konvertierung zwischen Tabelleneigenschaften und Datasetspalten erkannt.  
  
**Lösung:**
  
|Ausnahmemeldungen|  
|------------------------|  
|Konvertierungsfehler zwischen Azure-Tabellenspeichereigenschaft und Datasetspalte.|  
|Konvertierungsfehler zwischen Azure-Tabellenspeichereigenschaft und Datasetspalte. Weitere Informationen: {0}|  

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
  

## <a name="error-0139"></a>Fehler 0139  
 Eine Ausnahme tritt auf, wenn es nicht möglich ist, eine Spalte in einen anderen Typ zu konvertieren.  
  
 Dieser Fehler in Azure Machine Learning tritt bei dem Versuch auf, eine Spalte in einen anderen Datentyp zu konvertieren, wenn dieser Typ von der aktuellen Operation oder dem Modul nicht unterstützt wird.  
  
 Der Fehler kann auch auftreten, wenn ein Modul versucht, Daten implizit zu konvertieren, um den Anforderungen des aktuellen Moduls zu entsprechen, aber die Konvertierung ist nicht möglich.  
  
**Lösung:**

1. Überprüfen Sie Ihre Eingabedaten und ermitteln Sie den genauen Datentyp der zu verwendenden Spalte und den Datentyp der Spalte, die den Fehler verursacht. Gelegentlich können Sie den Eindruck erhalten, dass der richtige Datentyp verwendet wird, aber dann stellen Sie fest, dass eine Upstream-Operation den Datentyp oder die Verwendung einer Spalte geändert hat. Verwenden Sie das Modul [Edit Metadata](edit-metadata.md) (Metadaten bearbeiten), um die Spaltenmetadaten in ihren ursprünglichen Zustand zurückzusetzen. 
2. Überprüfen Sie auf der Modulhilfeseite die Anforderungen für die angegebene Operation. Ermitteln Sie, welche Datentypen das aktuelle Modul unterstützt und welcher Wertebereich unterstützt wird. 
 <!--3. If values need to be truncated, rounded, or outliers removed, use the [Apply Math Operation](apply-math-operation.md) or [Clip Values](clip-values.md) modules to make corrections.-->
4. Überlegen Sie, ob es möglich ist, die Spalte in einen anderen Datentyp zu konvertieren oder umzuwandeln. Die folgenden Module bieten alle eine hohe Flexibilität und Leistungsfähigkeit beim Ändern von Daten: 
 <!--
   + [Apply SQL Transformation](apply-sql-transformation.md)
   + [Execute R Script](execute-r-script.md)
-->   
   + [Execute Python Script](execute-python-script.md) (Python-Skript ausführen).  

> [!NOTE]
> Funktioniert es immer noch nicht? Stellen Sie ggf. zusätzliches Feedback zum Problem bereit, um uns dabei zu helfen, bessere Anleitungen zur Problembehandlung zu entwickeln. Übermitteln Sie das Feedback einfach auf dieser Seite, und geben Sie den Namen des fehlerhaften Moduls sowie die Datentypkonvertierung auf, bei der ein Fehler aufgetreten ist.
  
|Ausnahmemeldungen|  
|------------------------|  
|Nicht zulässige Konvertierung.|  
|Konnte nicht konvertiert werden: {0}.|  
|Konnte nicht konvertiert werden: {0}, in Zeile {1}.|  
|Es konnte die Spalte vom Typ {0} nicht in eine Spalte vom Typ {1} in der Zeile {2} konvertiert werden.|  
|Es konnte die Spalte „{2}“ vom Typ {0} nicht in eine Spalte vom Typ {1} in der Zeile {3} konvertiert werden.|  
|Es konnte die Spalte „{2}“ vom Typ {0} nicht in die Spalte „{3}“ vom Typ {1} in der Zeile {4} konvertiert werden.| 

## <a name="error-0140"></a>Fehler 0140  
 Eine Ausnahme tritt auf, wenn das übergebene Spaltensatzargument keine anderen Spalten außer der Bezeichnungsspalte enthält.  
  
 Dieser Fehler tritt auf, wenn Sie ein Dataset mit einem Modul verbunden haben, das mehrere Spalten erfordert (einschließlich Features), aber nur die Bezeichnungsspalte bereitgestellt haben.  
  
**Lösung:** Wählen Sie mindestens eine Featurespalte aus, die in das Dataset einbezogen werden soll.  
  
|Ausnahmemeldungen|  
|------------------------|  
|Der angegebene Spaltensatz enthält keine anderen Spalten außer der Bezeichnungsspalte.|  
  

## <a name="error-0141"></a>Fehler 0141  
 Eine Ausnahme tritt auf, wenn die Anzahl der ausgewählten numerischen Spalten und eindeutigen Werte in den kategorischen und Zeichenfolgenspalten zu klein ist.  
  
 Dieser Fehler in Azure Machine Learning tritt auf, wenn nicht ausreichend eindeutige Werte in der ausgewählten Spalte vorhanden sind, um die Operation durchzuführen.  
  
**Lösung:** Einige Operationen führen statistische Vorgänge für Feature- und kategorische Spalten durch, und wenn nicht genügend Werte vorhanden sind, kann bei der Operation ein Fehler auftreten oder ein ungültiges Ergebnis ausgegeben werden. Überprüfen Sie Ihr Dataset, um zu ermitteln, wie viele Werte in den Feature- und Bezeichnungsspalten vorhanden sind, und stellen Sie fest, ob die auszuführende Operation statistisch gültig ist.  
  
 Wenn das Quelldataset gültig ist, können Sie auch überprüfen, ob eine Upstream-Datenmanipulation oder Metadatenoperation die Daten geändert und einige Werte entfernt hat.  
  
 Wenn Upstream-Operationen die Aufteilung, Stichprobenentnahme oder Wiederholungsprobennahme umfassen, überprüfen Sie, ob die Ausgaben die erwartete Anzahl von Zeilen und Werten enthalten.  
  
|Ausnahmemeldungen|  
|------------------------|  
|Die Anzahl der ausgewählten numerischen Spalten und eindeutigen Werte in den kategorischen und Zeichenfolgenspalten ist zu klein.|  
|Die Gesamtzahl der ausgewählten numerischen Spalten und eindeutigen Werte in den kategorischen und Zeichenfolgenspalten (aktuell {0}) muss mindestens {1} sein.|  
  

## <a name="error-0142"></a>Fehler 0142  
 Eine Ausnahme tritt auf, wenn das System das Zertifikat zur Authentifizierung nicht laden kann.  
  
**Lösung:**
  
|Ausnahmemeldungen|  
|------------------------|  
|Das Zertifikat kann nicht geladen werden.|  
|Das Zertifikat {0} kann nicht geladen werden. Sein Fingerabdruck ist {1}.|  
  

## <a name="error-0143"></a>Fehler 0143  
 Die vom Benutzer bereitgestellte URL, die von GitHub stammen soll, kann nicht analysiert werden.  
  
 Dieser Fehler in Azure Machine Learning tritt auf, wenn Sie eine ungültige URL angeben und das Modul eine gültige GitHub-URL erfordert.  
  
**Lösung:** Überprüfen Sie, ob die URL auf ein gültiges GitHub-Repository verweist. Andere Websitetypen werden nicht unterstützt.  
  
|Ausnahmemeldungen|  
|------------------------|  
|Die URL stammt nicht von „github.com“.|  
|Die URL stammt nicht von „github.com“: {0}|  

## <a name="error-0144"></a>Fehler 0144  
 Der vom Benutzer bereitgestellten GitHub-URL fehlt der erwartete Teil.  
  
 Dieser Fehler in Azure Machine Learning tritt auf, wenn Sie eine GitHub-Dateiquelle mit einem ungültigen URL-Format angeben.  
  
**Lösung:** Überprüfen Sie, ob die URL des GitHub-Repositorys gültig ist und mit „\blob\“ oder „\tree\\“ endet.  
  
|Ausnahmemeldungen|  
|------------------------|  
|Die GitHub-URL kann nicht analysiert werden.|  
|Die GitHub-URL kann nicht analysiert werden („\blob\\“ oder „\tree\\“ hinter dem Namen des Repositorys erwartet): {0}|  

## <a name="error-0145"></a>Fehler 0145  
 Das Replikationsverzeichnis kann aus einem bestimmten Grund nicht erstellt werden.  
  
 Dieser Fehler in Azure Machine Learning tritt auf, wenn das Modul das angegebene Verzeichnis nicht erstellen konnte.  
  
**Lösung:**
  
|Ausnahmemeldungen|  
|------------------------|  
|Das Replikationsverzeichnis konnte nicht erstellt werden.|  
  

## <a name="error-0146"></a>Fehler 0146  
 Wenn die Benutzerdateien in das lokale Verzeichnis entzippt werden, ist der kombinierte Pfad möglicherweise zu lang.  
  
 Dieser Fehler in Azure Machine Learning tritt auf, wenn Sie Dateien extrahieren, aber einige Dateinamen beim Entzippen zu lang sind.  
  
**Lösung:** Bearbeiten Sie die Dateinamen so, dass die Kombination aus Pfad und Dateiname nicht länger als 248 Zeichen ist.  
  
|Ausnahmemeldungen|  
|------------------------|  
|Der Replikationspfad ist länger als 248 Zeichen, kürzen Sie den Skriptnamen oder Pfad.|  

## <a name="error-0147"></a>Fehler 0147  
 Aus irgendeinem Grund konnten keine Dateien von GitHub heruntergeladen werden.  
  
 Dieser Fehler in Azure Machine Learning tritt auf, wenn Sie die angegebenen Dateien von GitHub nicht lesen oder herunterladen können.  
  
**Lösung:** Das Problem ist möglicherweise temporär. Sie können versuchen, zu einem anderen Zeitpunkt auf die Dateien zuzugreifen. Oder stellen Sie sicher, dass Sie über die erforderlichen Berechtigungen verfügen und dass die Quelle gültig ist.  
  
|Ausnahmemeldungen|  
|------------------------|  
|Fehler beim Zugriff auf GitHub.|  
|Fehler beim Zugriff auf GitHub. [https://login.microsoftonline.com/tfp/00000000-0000-0000-0000-000000000000/b2c_1a_tp_sign-up-or-sign-in/v2.0/]({0})|  
  

## <a name="error-0148"></a>Fehler 0148  
 Probleme mit nicht autorisiertem Zugriff beim Extrahieren von Daten oder Erstellen von Verzeichnissen.  
  
 Dieser Fehler in Azure Machine Learning tritt bei dem Versuch auf, ein Verzeichnis zu erstellen oder Daten aus dem Speicher zu lesen, wenn Sie nicht über die erforderlichen Berechtigungen verfügen.  
  
**Lösung:**
  
|Ausnahmemeldungen|  
|------------------------|  
|Ausnahme für nicht autorisierten Zugriff beim Extrahieren von Daten.|  
  

## <a name="error-0149"></a>Fehler 0149  
 Die Benutzerdatei ist nicht im GitHub-Paket vorhanden.  
  
 Dieser Fehler in Azure Machine Learning tritt auf, wenn die angegebene Datei nicht gefunden werden kann.  
  
Lösung: 
  
|Ausnahmemeldungen|  
|------------------------|  
|Die GitHub-Datei konnte nicht gefunden werden.|  
|Die GitHub-Datei konnte nicht gefunden werden: {0}|  
  

## <a name="error-0150"></a>Fehler 0150  
 Die aus dem Benutzerpaket stammenden Skripts konnten nicht entzippt werden, was höchstwahrscheinlich auf einen Konflikt mit GitHub-Dateien zurückzuführen ist.  
  
 Dieser Fehler in Azure Machine Learning tritt auf, wenn ein Skript nicht extrahiert werden kann. Dies ist üblicherweise der Fall, wenn eine Datei mit demselben Namen vorhanden ist.  
  
Lösung:
  
|Ausnahmemeldungen|  
|------------------------|  
|Das Paket kann nicht entzippt werden. Möglicher Namenskonflikt mit GitHub-Dateien.|  
  

## <a name="error-0151"></a>Fehler 0151  
 Beim Schreiben in den Cloudspeicher ist ein Fehler aufgetreten. Überprüfen Sie die URL.  
  
 Dieser Fehler in Azure Machine Learning tritt auf, wenn das Modul versucht, Daten in den Cloudspeicher zu schreiben, die URL aber nicht verfügbar oder ungültig ist.  
  
Lösung: Überprüfen Sie die URL und stellen Sie sicher, dass sie beschreibbar ist.  
  
|Ausnahmemeldungen|  
|------------------------|  
|Fehler beim Schreiben in den Cloudspeicher (möglicherweise ungültige URL).|  
|Fehler beim Schreiben in den Cloudspeicher: {0}. Überprüfen Sie die URL.|  
  
## <a name="error-0152"></a>Fehler 0152  
 Der Azure-Cloudtyp wurde im Modulkontext falsch angegeben.  
  
|Ausnahmemeldungen|  
|------------------------|  
|Ungültiger Azure-Cloudtyp.|  
|Ungültiger Azure-Cloudtyp: {0}|  
  
## <a name="error-0153"></a>Fehler 0153  
 Der angegebene Speicherendpunkt ist ungültig.  
  
|Ausnahmemeldungen|  
|------------------------|  
|Ungültiger Azure-Cloudtyp.|  
|Ungültiger Speicherendpunkt: {0}|  

## <a name="error-0154"></a>Fehler 0154  
 Der angegebene Servername konnte nicht aufgelöst werden.  
  
|Ausnahmemeldungen|  
|------------------------|  
|Der angegebene Servername konnte nicht aufgelöst werden.|  
|Der angegebene Server „{0}.documents.azure.com“ konnte nicht aufgelöst werden.|

## <a name="error-0155"></a>Fehler 0155  
 Der DocDb-Client hat eine Ausnahme ausgelöst.  
  
|Ausnahmemeldungen|  
|------------------------|  
|Der DocDb-Client hat eine Ausnahme ausgelöst.|  
|DocDb-Client: {0}|

## <a name="error-0156"></a>Fehler 0156  
 Ungültige Antwort für HCatalog Server.  
  
|Ausnahmemeldungen|  
|------------------------|  
|Ungültige Antwort für HCatalog Server. Überprüfen Sie, ob alle Dienste ausgeführt werden.|  
|Ungültige Antwort für HCatalog Server. Überprüfen Sie, ob alle Dienste ausgeführt werden. Fehlerdetails: {0}|

## <a name="error-0157"></a>Fehler 0157  
 Beim Lesen von Azure Cosmos DB ist aufgrund inkonsistenter oder unterschiedlicher Dokumentschemas ein Fehler aufgetreten. Der Leser erfordert, dass alle Dokumente dasselbe Schema aufweisen.  
  
|Ausnahmemeldungen|  
|------------------------|  
|Es wurden Dokumente mit unterschiedlichen Schemas erkannt. Stellen Sie sicher, dass alle Dokumente dasselbe Schema aufweisen.|

## <a name="error-1000"></a>Fehler 1000  
Interne Bibliotheksausnahme.  
  
Dieser Fehler wird bereitgestellt, um ansonsten unbehandelte interne Enginefehler zu erfassen. Daher kann die Ursache für diesen Fehler je nach Modul, das den Fehler generiert hat, unterschiedlich sein.  
  
Um weitere Hilfe zu erhalten, wird empfohlen, die ausführliche Meldung, die dem Fehler zugeordnet ist, zusammen mit einer Beschreibung des Szenarios (einschließlich der als Eingaben verwendeten Daten) an das Azure Machine Learning-Forum zu senden. Dieses Feedback hilft uns, Fehler zu priorisieren und die wichtigsten Probleme für die weitere Arbeit zu identifizieren.  
  
|Ausnahmemeldungen|  
|------------------------|  
|Bibliotheksausnahme.|  
|Bibliotheksausnahme: {0}|  
|{0}-Bibliotheksausnahme: {1}|  
