---
title: 'Normalize Data: Modulreferenz'
titleSuffix: Azure Machine Learning service
description: Erfahren Sie, wie Sie mit dem Modul „Normalize Data“ (Normalisieren von Daten) in Azure Machine Learning Service ein Dataset durch *Normalisierung* transformieren können.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 95069bafa94770511c7ee40e82068960298fd6c5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65027798"
---
# <a name="normalize-data-module"></a>Modul „Normalize Data“ (Normalisieren von Daten)

In diesem Artikel wird ein Modul der grafischen Benutzeroberfläche (Vorschau) für Azure Machine Learning Service beschrieben.

Verwenden Sie dieses Modul zum Transformieren eines Datasets mittels *Normalisierung*.

Normalisierung ist eine Methode, die häufig im Rahmen der Datenaufbereitung für maschinelles Lernen eingesetzt wird. Ziel der Normalisierung ist es, die Werte numerischer Spalten im Dataset so zu ändern, dass sie eine gemeinsame Skala verwenden, ohne Unterschiede in den Wertebereichen zu verzerren oder Informationen zu verlieren. Eine Normalisierung ist auch für einige Algorithmen erforderlich, um die Daten ordnungsgemäß zu modellieren.

Angenommen, Ihr Eingabedataset enthält eine Spalte mit Werten von 0 bis 1 und eine weitere Spalte mit Werten von 10.000 bis 100.000. Der große Unterschied in der *Skala* der Werte kann zu Problemen führen, wenn Sie versuchen, die Werte während der Modellierung als Features zu kombinieren.

Dank *Normalisierung* werden diese Probleme vermieden, indem neue Werte erstellt werden, die die allgemeine Verteilung und Verhältnisse in den Quelldaten beibehalten, während die Werte innerhalb einer Skala verbleiben, die für alle im Modell verwendeten numerischen Spalten gilt.

Dieses Modul bietet mehrere Optionen zum Transformieren numerischer Daten:

- Sie können alle Werte auf einer Skala von 0-1 ändern oder die Werte transformieren, indem Sie sie als Perzentilränge statt als absolute Werte darstellen.
- Sie können die Normalisierung auf eine einzelne Spalte oder mehrere Spalten in einem Dataset anwenden.
- Wenn Sie das Experiment wiederholen oder die gleichen Normalisierungsschritte auf andere Daten anwenden müssen, können Sie die Schritte als Normalisierungstransformation speichern und auf andere Datasets anwenden, die das gleiche Schema haben.

> [!WARNING]
> Einige Algorithmen erfordern, dass Daten vor dem Trainieren eines Modells normalisiert werden. Andere Algorithmen führen ihre eigenen Datenskalierung oder -normalisierung durch. Wenn Sie also einen Algorithmus für maschinelles Lernen wählen, der beim Erstellen eines prädiktiven Modells verwendet werden soll, überprüfen Sie unbedingt die Datenanforderungen des Algorithmus, bevor Sie die Normalisierung auf die Trainingsdaten anwenden.

##  <a name="configure-normalize-data"></a>Konfigurieren von „Normalize Data“

Mithilfe dieses Moduls können Sie jeweils nur eine Normalisierungsmethode anwenden. Aus diesem Grund wird die gleiche Normalisierungsmethode auf alle Spalten angewendet, die Sie auswählen. Um andere Normalisierungsmethoden anzuwenden, verwenden Sie eine zweite Instanz von **Normalize Data**.

1. Fügen Sie das Modul **Normalize Data** Ihrem Experiment hinzu. Sie finden das Modul in Azure Machine Learning unter **Data Transformation** (Datentransformation) in der Kategorie **Scale and Reduce** (Skalieren und reduzieren).

2. Stellen Sie eine Verbindung mit einem Dataset her, das mindestens eine Spalte mit allen Zahlen enthält.

3. Wählen Sie die zu normalisierenden Spalten mithilfe der Spaltenauswahlfunktion aus. Wenn Sie keine einzelnen Spalten auswählen, werden standardmäßig **alle** numerischen Spalten in der Eingabe berücksichtigt. Der gleiche Normalisierungsprozess wird auf alle ausgewählten Spalten angewendet. 

    Dies kann zu unerwünschten Ergebnissen führen, wenn Sie numerische Spalten einbeziehen, die nicht normalisiert werden sollten! Überprüfen Sie die Spalten stets sorgfältig.

    Wenn keine numerischen Spalten erkannt werden, überprüfen Sie die Metadaten der Spalte, um sicherzustellen, dass der Datentyp der Spalte ein unterstützter numerischer Typ ist.

    > [!TIP]
    > Um sicherzustellen, dass Spalten eines bestimmten Typs als Eingabe zur Verfügung gestellt werden, sollten Sie das Modul [Select Columns in Dataset](./select-columns-in-dataset.md) (Spalten im Datensatz auswählen) vor **Normalize Data** verwenden.

4. **Use 0 for constant columns when checked** (0 für konstante Spalten verwenden, falls aktiviert):  Aktivieren Sie diese Option, wenn eine numerische Spalte einen einzelnen unveränderlichen Wert enthält. Dadurch wird sichergestellt, dass solche Spalten bei Normalisierungsvorgängen nicht berücksichtigt werden.

5. Wählen Sie in der Dropdownliste **Transformation method** (Transformationsmethode) eine einzelne mathematische Funktion, die auf alle ausgewählten Spalten angewendet wird. 
  
    - **Zscore**: Konvertiert alle Werte in ein Z-Ergebnis.
    
      Die Werte in der Spalte werden mithilfe der folgenden Formel transformiert:  
  
      ![Normalisierung mithilfe von Z-Bewertungen](media/module/aml-normalization-z-score.png)
  
      Mittlere und Standardabweichung werden für jede Spalte getrennt berechnet. Die Standardabweichung der Grundgesamtheit wird verwendet.
  
    - **MinMax**: Der Min-Max-Normalisierer skaliert jedes Feature linear in das Intervall [0,1] um.
    
      Die Umskalierung in das Intervall [0,1] erfolgt durch Verschieben der Werte jedes Features, sodass der Minimalwert 0 ist. Anschließend erfolgt eine Division durch den neuen Maximalwert (was die Differenz zwischen dem ursprünglichen Maximal- und Minimalwert ergibt).
      
      Die Werte in der Spalte werden mithilfe der folgenden Formel transformiert:  
  
      ![Normalisierung mithilfe der Min-Max-Funktion](media/module/aml-normalization-minmax.png "AML_normalization-minmax")  
  
    - **Logistic** (Logistisch): Die Werte in der Spalte werden mithilfe der folgenden Formel transformiert:

      ![Formel für die Normalisierung durch logistische Funktion](media/module/aml-normalization-logistic.png "AML_normalization-logistic")  
  
    - **LogNormal**: Diese Option konvertiert alle Werte in eine logarithmische Normalverteilung.
  
      Die Werte in der Spalte werden mithilfe der folgenden Formel transformiert:
  
      ![Formel für logarithmische Normalverteilung](media/module/aml-normalization-lognormal.png "AML_normalization-lognormal")
    
      Hier sind μ und σ die Parameter der Verteilung, die empirisch anhand der Daten als Maximum-Likelihood-Schätzungen berechnet werden, und zwar für jede Spalte einzeln.  
  
    - **tanh**: Alle Werte werden in einen hyperbolischer Tangens konvertiert.
    
      Die Werte in der Spalte werden mithilfe der folgenden Formel transformiert:
    
      ![Normalisierung mithilfe der tanh-Funktion](media/module/aml-normalization-tanh.png "AML_normalization-tanh")

6. Führen Sie das Experiment aus, oder doppelklicken Sie auf das Modul **Normalize Data**, und klicken Sie auf **Run Selected** (Ausgewählte ausführen). 

## <a name="results"></a>Ergebnisse

Das Modul **Normalize Data** generiert zwei Ausgaben:

- Um die transformierten Werte anzuzeigen, klicken Sie mit der rechten Maustaste auf das Modul. Wählen Sie **Transformed dataset**  (Transformiertes Dataset) aus, und klicken Sie auf **Visualize** (Visualisieren).

    Standardmäßig werden Werte direkt transformiert. Wenn Sie die transformierten Werte mit den Originalwerten vergleichen möchten, verwenden Sie das Modul [Add Columns](./add-columns.md) (Spalten hinzufügen), um die Datasets neu zu kombinieren und die Spalten nebeneinander zu betrachten.

- Um die Transformation so zu speichern, dass Sie die gleiche Normalisierungsmethode auf ein anderes ähnliches Dataset anwenden können, klicken Sie mit der rechten Maustaste auf das Modul, wählen Sie **Transformation function** (Transformationsfunktion)aus, und klicken Sie auf **Save as Transform** (Als Transformation speichern).

    Sie können anschließend die gespeicherten Transformationen aus der Gruppe **Transforms** im linken Navigationsbereich laden und sie auf ein Dataset mit dem gleichen Schema anwenden, indem Sie [./Apply Transformation](apply-transformation.md) (Transformation anwenden) verwenden.  


## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich die [Gruppe der verfügbaren Module](module-reference.md) für Azure Machine Learning Service an. 