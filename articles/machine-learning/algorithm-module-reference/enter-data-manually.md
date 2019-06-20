---
title: 'Enter Data Manually: Modulreferenz'
titleSuffix: Azure Machine Learning service
description: Erfahren Sie, wie Sie mit dem Modul „Enter Data Manually“ (Manuelles Eingeben von Daten) in Azure Machine Learning Service ein kleines Dataset durch Eingabe von Werten erstellen können. Das Dataset kann mehrere Spalten enthalten.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: ee15b6fb7160ece907d55e790b0ae38ee458ab96
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65027700"
---
# <a name="enter-data-manually-module"></a>Modul „Enter Data Manually“ (Manuelles Eingeben von Daten)

In diesem Artikel wird ein Modul der grafischen Benutzeroberfläche (Vorschau) für Azure Machine Learning Service beschrieben.

Mithilfe dieses Moduls können Sie ein kleines Dataset durch Eingabe von Werten erstellen. Das Dataset kann mehrere Spalten enthalten.
  
Dieses Modul kann in folgenden Szenarien hilfreich sein:  
  
- Generieren einer kleinen Menge von Werten für Tests  
  
- Erstellen einer Kurzliste mit Bezeichnungen
  
- Eingeben einer Liste mit Spaltennamen zum Einfügen in ein Dataset

## <a name="enter-data-manually"></a>Enter Data Manually 
  
1.  Fügen Sie das Modul [Enter Data Manually](./enter-data-manually.md) Ihrem Experiment hinzu. Sie finden dieses Modul in Azure Machine Learning in der Kategorie **Data Input and Output** (Dateieingabe und -ausgabe). 
  
2.  Wählen Sie für **DataFormat** eine der folgenden Optionen aus. Diese Optionen bestimmen, wie die von Ihnen bereitgestellten Daten analysiert werden sollen. Die Anforderungen für jedes Format sind sehr unterschiedlich, weshalb Sie die entsprechenden Themen unbedingt lesen sollten.  
  
    -   **ARFF**. Steht für „Attribute-Relation File Format“ und wird von Weka verwendet.   
  
    -   **CSV**. Format mit durch Trennzeichen getrennte Werte. Weitere Informationen finden Sie unter [Convert to CSV](./convert-to-csv.md) (Konvertieren in das CSV-Format).  
  
    -   **SVMLight**. Ein Format, das von Vowpal Wabbit und anderen Frameworks für maschinelles Lernen verwendet wird.  
  
    -   **TSV**. Format mit per Tabulator getrennten Werten.

     Wenn Sie ein Format wählen und keine Daten bereitstellen, die den Formatvorgaben entsprechen, tritt ein Laufzeitfehler auf.
  
3.  Klicken Sie in das Textfeld **Daten**, um mit der Dateneingabe zu beginnen. Die folgenden Formate erfordern besondere Aufmerksamkeit:  
  
    - **CSV**:  Um mehrere Spalten zu erstellen, fügen Sie Text mit Kommas als Trennzeichen ein, oder geben Sie mehrere Spalten mit Kommas zwischen den Feldern ein.
  
        Wenn Sie die Option **HasHeader** auswählen, können Sie die erste Zeile der Werte als Spaltenüberschrift verwenden.  
  
        Wenn Sie diese Option deaktivieren, werden die Spaltennamen Col1, Col2 usw. verwendet. Sie können Spaltennamen später mit der Option [Edit Metadata](./edit-metadata.md) (Metadaten bearbeiten) hinzufügen oder ändern.  
  
    - **TSV**: Um mehrere Spalten zu erstellen, fügen Sie Text mit Tabulatoren als Trennzeichen ein, oder geben Sie mehrere Spalten mit Tabulatoren zwischen den Feldern ein.  
  
        Wenn Sie die Option **HasHeader** auswählen, können Sie die erste Zeile der Werte als Spaltenüberschrift verwenden.  
  
        Wenn Sie diese Option deaktivieren, werden die Spaltennamen Col1, Col2 usw. verwendet. Sie können Spaltennamen später mit der Option [Edit Metadata](./edit-metadata.md) (Metadaten bearbeiten) hinzufügen oder ändern.  
  
    -   **ARFF**:  Fügen Sie Daten in eine vorhandene Datei im ARFF-Format ein. Wenn Sie Werte direkt eingeben, fügen Sie die optionalen Kopfzeilen- und erforderlichen Attributfelder am Anfang der Daten hinzu. 
    
        Beispielsweise können die folgenden Kopf- und Attributzeilen einer einfachen Liste hinzugefügt werden. Die Überschrift der Spalte lautet dann `SampleText`.
    
        ```text
        % Title: SampleText.ARFF  
        % Source: Enter Data module  
        @ATTRIBUTE SampleText STRING  
        @DATA  
        \<type first data row here>  
        ```

    -   **SVMLight**: Geben oder fügen Sie Werte im SVMLight-Format ein.  
  
        Das folgende Beispiel stellt beispielsweise die ersten Zeilen des Datasets „Blood Donation“ im SVMight-Format dar:  
  
        ```text  
        # features are [Recency], [Frequency], [Monetary], [Time]  
        1 1:2 2:50 3:12500 4:98   
        1 1:0 2:13 3:3250 4:28   
        ```  
  
        Wenn Sie das Modul [Enter Data Manually](./enter-data-manually.md) ausführen, werden diese Zeilen wie folgt in ein Dataset mit Spalten und Indexwerten konvertiert:  
  
        |Col1|Col2|Col3|Col4|Bezeichnungen|  
        |-|-|-|-|-|  
        |0,00016|0,004|0,999961|0,00784|1|  
        |0|0,004|0,999955|0,008615|1|  
  
4.  Drücken Sie nach jeder Zeile die EINGABETASTE, um eine neue Zeile zu beginnen.  
  
     **Drücken Sie unbedingt nach der letzten Zeile die EINGABETASTE.** 
     
     Wenn Sie mehrmals die EINGABETASTE drücken, um mehrere leere nachstehende Zeilen hinzuzufügen, wird die letzte leere Zeile beschnitten entfernt, aber andere leere Zeilen werden als fehlende Werte behandelt.  
  
     Wenn Sie Zeilen mit fehlenden Werten erstellen, können Sie diese später jederzeit wieder herausfiltern.  
  
5.  Klicken Sie mit der rechten Maustaste auf das Modul, und wählen Sie **Run selected** (Ausgewählte ausführen), um die Daten zu analysieren und als Dataset in Ihren Arbeitsbereich zu laden.  
  
     Um eine Vorschau des Datasets anzuzeigen, klicken Sie auf den Ausgabeport, und wählen Sie **Visualize** (Visualisieren).  
## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich die [Gruppe der verfügbaren Module](module-reference.md) für Azure Machine Learning Service an. 