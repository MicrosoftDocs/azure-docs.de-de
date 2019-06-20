---
title: 'Split Data (Daten aufteilen): Modulreferenz'
titleSuffix: Azure Machine Learning service
description: Erfahren Sie, wie Sie das Modul „Split Data“ (Daten aufteilen) in Azure Machine Learning Service verwenden, um ein Dataset in zwei verschiedene Sätze aufzuteilen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: a7395280ed9a2e9dcb94a081f0b3bf10a28da719
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65027792"
---
# <a name="split-data-module"></a>Modul „Split Data“ (Daten aufteilen)

In diesem Artikel wird ein Modul der grafischen Benutzeroberfläche (Vorschau) für Azure Machine Learning Service beschrieben.

Verwenden Sie dieses Modul, um ein Dataset in zwei verschiedene Sätze aufzuteilen.

Dieses Modul ist besonders hilfreich, wenn Sie Daten in Trainings- und Testsätze aufteilen müssen. Sie können außerdem anpassen, wie die Daten aufgeteilt werden sollen. Einige Optionen unterstützen die zufällige Anordnung von Daten, andere sind auf einen bestimmten Daten- oder Modelltyp zugeschnitten.

## <a name="how-to-configure"></a>Vorgehensweise zur Konfiguration

> [!TIP]
> Informieren Sie sich vor der Wahl des Aufteilungsmodus über alle Optionen, um den benötigten Aufteilungstyp zu ermitteln.
> Wenn Sie den Aufteilungsmodus ändern, können alle anderen Optionen zurückgesetzt werden.

1. Fügen Sie das Modul **Split Data** Ihrem Experiment in der Oberfläche hinzu. Sie finden dieses Modul unter **Data Transformation** (Datentransformation) in der Kategorie **Sample and Split** (Stichprobe entnehmen und aufteilen).

2. **Splitting mode** (Aufteilungsmodus): Wählen Sie je nach Datentyp und gewünschter Aufteilung einen der folgenden Modi. Jeder Aufteilungsmodus verfügt über unterschiedliche Optionen. Klicken Sie auf die folgenden Themen, um ausführliche Anweisungen und Beispiele zu erhalten. 

    - **Split Rows** (Zeilen aufteilen): Verwenden Sie diese Option, wenn Sie Daten einfach in zwei Teile aufteilen möchten. Sie können angeben, wie viel Prozent Daten jeweils in einem Teil enthalten sein sollen. Die Standardeinstellung beträgt „50:50“.

        Sie können Zeilen für die einzelnen Gruppen auch nach dem Zufallsprinzip auswählen und geschichtete Stichproben verwenden. Bei geschichteten Stichproben müssen Sie eine einzelne Datenspalte auswählen, für die Werte gleichmäßig auf zwei Ergebnisdatasets aufgeteilt werden sollen.  

    - **Regular Expression Split** (Nach regulärem Ausdruck aufteilen) Wählen Sie diese Option, wenn Sie Ihr Dataset danach aufteilen möchten, ob ein Wert in einer einzelnen Spalte enthalten ist.

        Bei Stimmungsanalysen können Sie beispielsweise überprüfen, ob ein bestimmter Produktname in einem Textfeld enthalten ist. Anschließend können Sie das Dataset in Zeilen aufteilen, die den gewünschten Produktnamen enthalten bzw. nicht enthalten.

    - **Relative Expression Split** (Nach relativem Ausdruck aufteilen):  Verwenden Sie diese Option, wenn Sie eine Bedingung auf eine Ziffernspalte anwenden möchten. Die Ziffer kann für ein Datums-/Uhrzeitfeld, eine Spalte mit Alters- oder Währungsangaben oder sogar für einen Prozentsatz stehen. Beispielsweise ist es möglich, das Dataset nach den Kosten der einzelnen Posten zu unterteilen, Personen nach Alter zu gruppieren oder Daten nach einem Kalenderdatum aufzuteilen.

### <a name="split-rows"></a>Aufteilen von Zeilen
1.  Fügen Sie das Modul [Split Data](./split-data.md) Ihrem Experiment in der Oberfläche hinzu, und verbinden Sie das aufzuteilende Dataset.
  
2.  Wählen Sie für **Splitting mode** (Aufteilungsmodus) die Option **Split rows** (Zeilen aufteilen) aus. 

3.  **Fraction of rows in the first output dataset** (Anteil der Zeilen im ersten Ausgabedataset). Ermitteln Sie anhand dieser Option, wie viele Zeilen der ersten (linken) Ausgabe zugeordnet werden. Alle weiteren Zeilen werden der zweiten (rechten) Ausgabe zugewiesen.

    Das Verhältnis stellt den Prozentsatz der Zeilen dar, die an das erste Ausgabedataset gesendet werden, sodass Sie eine Dezimalzahl zwischen 0 und 1 eingeben müssen.
     
     Wenn Sie z. B. 0,75 als Wert eingeben, wird das Dataset im Verhältnis 75:25 aufgeteilt. Dabei werden 75 % der Zeilen an das erste Ausgabedataset und 25 % an das zweite Ausgabedataset gesendet.
  
4. Wählen Sie die Option **Randomized split** (Zufällige Aufteilung), wenn Daten den beiden Gruppen nach dem Zufallsprinzip zugewiesen werden sollen. Dies ist die bevorzugte Option bei der Erstellung von Trainings-und Testdatasets.

5.  **Random Seed** (Zufälliger Startwert): Geben Sie einen nicht negativen ganzzahligen Wert ein, um die pseudozufällige Sequenz der zu verwendenden Instanzen zu initialisieren. Dieser standardmäßige Startwert wird in allen Modulen verwendet, die Zufallszahlen generieren. 

     Die Angabe eines Startwerts führt dazu, dass die Ergebnisse grundsätzlich reproduzierbar sind. Wenn Sie die Ergebnisse eines Aufteilungsvorgangs wiederholen müssen, muss auch ein Startwert für den Zufallszahlengenerator angegeben werden. Andernfalls wird der zufällige Startwert standardmäßig auf „0“ festgelegt, was bedeutet, dass der ursprüngliche Startwert von der Systemuhr abgerufen wird. Daher kann die Verteilung der Daten bei einer neuen Aufteilung geringfügig abweichen. 

6. **Stratified split** (Geschichtete Aufteilung): Legen Sie diese Option auf **True** fest, um sicherzustellen, dass die beiden Ausgabedatasets eine repräsentative Stichprobe der Werte aus der *Schichtspalte* oder *Schichtungsschlüsselspalte* enthalten. 

    Bei geschichteten Stichproben werden die Daten unterteilt, sodass jedes Ausgabedataset annähernd den gleichen Prozentsatz der einzelnen Zielwerte aufweist. Sie können z. B. sicherstellen, dass Ihre Trainings- und Testsätze im Hinblick auf das Ergebnis oder eine andere Spalte (z. B. Geschlecht) ungefähr ausgeglichen sind.

7. Führen Sie das Experiment aus.


## <a name="regular-expression-split"></a>Aufteilung nach regulären Ausdrücken

1.  Fügen Sie das Modul [Split Data](./split-data.md) Ihrem Experiment hinzu, und verbinden Sie es als Eingabe mit dem aufzuteilenden Dataset.  
  
2.  Wählen Sie für **Splitting mode** (Aufteilungsmodus) die Option **Regular expression split** (Nach regulärem Ausdruck aufteilen) aus.

3. Geben Sie im Feld **Regular expression** (Regulärer Ausdruck) einen gültigen, regulären Ausdruck ein. 
  
   Der reguläre Ausdruck sollte der Syntax für reguläre Python-Ausdrücke folgen.


4. Führen Sie das Experiment aus.

    Basierend auf dem angegebenen regulären Ausdruck wird das Dataset in zwei Sätze von Zeilen unterteilt: Zeilen mit Werten, die mit dem Ausdruck übereinstimmen, und alle übrigen Zeilen. 

## <a name="relative-expression-split"></a>Aufteilen nach relativen Ausdrücken

1. Fügen Sie das Modul [Split Data](./split-data.md) Ihrem Experiment hinzu, und verbinden Sie es als Eingabe mit dem aufzuteilenden Dataset.
  
2. Wählen Sie für **Splitting mode** (Aufteilungsmodus) die Option **Relative expression split** (Nach relativem Ausdruck aufteilen) aus.
  
3. Geben Sie im Textfeld **Relational expression** (Relationaler Ausdruck) einen Ausdruck ein, durch den ein Vergleichsvorgang für eine einzelne Spalte durchgeführt wird:


 - Numerische Spalte:
    - Die Spalte enthält Ziffern jedes numerischen Datentyps einschließlich der Datums-/Uhrzeitdatentypen.

    - Im Ausdruck kann auf maximal einen Spaltennamen verwiesen werden.

    - Verwenden Sie das kaufmännische Und-Zeichen (&) für den AND-Vorgang und den senkrechten Strich (|) für den OR-Vorgang.

    - Folgende Operatoren werden unterstützt: `<`, `>`, `<=`, `>=`, `==`, `!=`

    - Vorgänge können nicht mithilfe von `(` und `)` gruppiert werden.

 - Zeichenfolgenspalte: 
    - Folgende Operatoren werden unterstützt: `==`, `!=`



4. Führen Sie das Experiment aus.

    Durch den Ausdruck wird das Dataset in zwei Sätzen von Zeilen unterteilt: Zeilen mit Werten, die die Bedingung erfüllen, und alle übrigen Zeilen.

## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich die [Gruppe der verfügbaren Module](module-reference.md) für Azure Machine Learning Service an. 