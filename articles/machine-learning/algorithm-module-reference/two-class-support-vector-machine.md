---
title: 'Zweiklassige Support Vector Machine: Modulreferenz'
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie mit dem Modul „zweiklassige Support Vector Machine“ in Azure Machine Learning einen binären Klassifizierer erstellen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/22/2020
ms.openlocfilehash: 46cfdd319fc89e569d165dc2e11303e67c6dd54e
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2020
ms.locfileid: "93420562"
---
# <a name="two-class-support-vector-machine-module"></a>Modul „Zweiklassige Support Vector Machine“

In diesem Artikel wird ein Modul im Azure Machine Learning-Designer beschrieben.

Verwenden Sie dieses Modul, um ein Modell zu erstellen, das auf dem Algorithmus der Support Vector Machine basiert. 

Support Vector Machines (SVMs) sind eine gut erforschte Klasse von überwachten Learning-Methoden. Diese spezielle Implementierung eignet sich zur Vorhersage von zwei möglichen Ergebnissen, die entweder auf kontinuierlichen oder kategorischen Variablen basieren.

Nachdem Sie die Modellparameter definiert haben, trainieren Sie das Modell unter Verwendung der Trainingsmodule und stellen Sie einen *getaggten Datensatz* bereit, der eine Bezeichnung oder eine Ergebnisspalte enthält.

## <a name="about-support-vector-machines"></a>Über Support Vector Machines

Support Vector Machines gehören zu den frühesten Machine Learning-Algorithmen, und SVM-Modelle wurden in vielen Anwendungen verwendet – von der Informationsbeschaffung bis zur Text- und Bildklassifizierung. SVMs können für sowohl Klassifizierungs-als auch Regressionsaufgaben verwendet werden.

Diese SVM-Modell ist ein überwachtes Learning-Modell, für das bezeichnete Daten erforderlich sind. Im Trainingsprozess analysiert der Algorithmus Eingabedaten und erkennt Muster in einem mehrdimensionalen Featureraum namens *Hyperebene*.  Alle Eingabebeispiele werden in diesem Raum als Punkte dargestellt und auf Ausgabekategorien so abgebildet, dass Kategorien durch eine möglichst große Breite geteilt werden und eine Lücke schließen.

Für die Vorhersage ordnet der SVM-Algorithmus neue Beispiele der einen oder anderen Kategorie zu und ordnet sie dem gleichen Raum zu. 

## <a name="how-to-configure"></a>Vorgehensweise zur Konfiguration 

Für diesen Modelltyp wird empfohlen, dass Sie den Datensatz normalisieren, bevor Sie ihn zum Trainieren des Klassifikators verwenden.
  
1.  Fügen Sie das Modul **Support Vector Machine: zwei Klassen** zu Ihrer Pipeline hinzu.  
  
2.  Geben Sie an, wie das Modell trainiert werden soll, indem Sie die Option **Create trainer mode** (Trainermodus erstellen) aktivieren.  
  
    -   **Single Parameter** (Einzelner Parameter): Wenn Sie wissen, wie Sie das Modell konfigurieren möchten, können Sie einen bestimmten Satz von Werten als Argumente angeben.  

    -   **Parameter Range** (Parameterbereich): Wenn Sie sich hinsichtlich der besten Parameter nicht sicher sind, können Sie optimale Parameter mithilfe des Moduls [Tune Model Hyperparameters](tune-model-hyperparameters.md) finden. Sie geben einen Wertebereich an, woraufhin das Training über mehrere Einstellungskombinationen iteriert, um die Wertekombination zu bestimmen, die das beste Ergebnis liefert.

3.  Geben Sie für **Anzahl der Iterationen** eine Zahl ein, die die Anzahl der beim Erstellen des Modells verwendeten Iterationen angibt.  
  
     Dieser Parameter kann verwendet werden, um den Kompromiss zwischen Trainingsgeschwindigkeit und Genauigkeit zu steuern.  
  
4.  Geben Sie für **Lambda** einen Wert ein, der als Gewichtung für die L1-Regularisierung verwendet werden soll.  
  
     Dieser Regularisierungskoeffizient kann zur Optimierung des Modells verwendet werden. Größere Werte führen zu komplexeren Modellen.  
  
5.  Wählen Sie die Option **Features normalisieren**, wenn Sie Features vor dem Training normalisieren möchten.
  
     Wenn Sie die Normalisierung anwenden, werden die Datenpunkte vor dem Training auf den Mittelwert zentriert und auf eine Einheit der Standardabweichung skaliert.
  
6.  Wählen Sie die Option **In Einheitssphäre projizieren**, um die Koeffizienten zu normalisieren.
  
     Das Projizieren von Werten zum Einheitsraum bedeutet, dass die Datenpunkte vor dem Training auf 0 zentriert und auf eine Einheit der Standardabweichung skaliert werden.
  
7.  Geben Sie unter **Zufälliger Ausgangswert** einen ganzzahligen Wert ein, der als Ausgangswert verwendet werden soll, wenn Sie die Reproduzierbarkeit über mehrere Ausführungen hinweg sicherstellen möchten.  Andernfalls wird ein von der Systemuhr bereitgestellter Wert als Ausgangswert verwendet, was bei jeder Ausführung zu geringfügig unterschiedlichen Ergebnissen führen kann.
  
9. Verbinden Sie ein bezeichnetes Dataset, und trainieren Sie das Modell:

    + Wenn Sie **Create trainer mode** (Trainermodus erstellen) auf **Single Parameter** (Einzelner Parameter) festlegen, müssen Sie ein mit Tags versehenes Dataset und das Modul [Train Model](train-model.md) (Modell trainieren) verbinden.  
  
    + Wenn Sie **Create trainer mode** (Trainermodus erstellen) auf **Parameter Range** (Parameterbereich) festlegen, verbinden Sie ein mit Tags versehenes Dataset, und trainieren Sie das Modell mithilfe von [Tune Model Hyperparameters](tune-model-hyperparameters.md).  
  
    > [!NOTE]
    > 
    > Wenn Sie einen Parameterbereich an [Train Model](train-model.md) übergeben, wird nur der Standardwert in der Liste der Einzelparameter verwendet.  
    > 
    > Wenn Sie eine einzelne Reihe bestimmter Parameterwerte an das Modul [Tune Model Hyperparameters](tune-model-hyperparameters.md) übergeben und ein Bereich von Einstellungen für jeden Parameter erwartet wird, werden die Werte ignoriert und stattdessen die Standardwerte für den Learner verwendet.  
    > 
    > Wenn Sie die Option **Parameter Range** (Parameterbereich) auswählen und einen einzelnen Wert für einen beliebigen Parameter eingeben, wird dieser angegebene einzelne Wert während des gesamten Löschvorgangs verwendet, auch wenn andere Parameter in einem Wertebereich geändert werden.
  
10. Übermitteln Sie die Pipeline.

## <a name="results"></a>Ergebnisse

Nach Abschluss des Trainings:

+ Um eine Momentaufnahme des trainierten Modells zu speichern, wählen Sie die Registerkarte **Ausgaben** im rechten Bereich des Moduls **Train model** aus. Wählen Sie das Symbol **Register dataset** (Dataset registrieren) aus, um das Modell als wiederverwendbares Modul zu speichern.

+ Um das Modell zur Bewertung zu verwenden, fügen Sie einer Pipeline das Modul **Score Model** hinzu.


## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich den [Satz der verfügbaren Module](module-reference.md) für Azure Machine Learning an. 