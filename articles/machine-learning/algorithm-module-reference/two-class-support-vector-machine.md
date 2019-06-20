---
title: 'Zweiklassige Support Vector Machine: Modulreferenz'
titleSuffix: Azure Machine Learning service
description: Hier erfahren Sie, wie Sie mithilfe des Moduls **Zweiklassige Support Vector Machine** in Azure Machine Learning Service ein Modell erstellen, das auf dem Algorithmus für die Support Vector Machine basiert.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 2f076dd3a5b1ceb9e24548652a71fda5b9aa48b7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65027620"
---
# <a name="two-class-support-vector-machine-module"></a>Modul „Zweiklassige Support Vector Machine“

In diesem Artikel wird ein Modul der grafischen Benutzeroberfläche (Vorschau) für den Azure Machine Learning Service beschrieben.

Verwenden Sie dieses Modul, um ein Modell zu erstellen, das auf dem Algorithmus der Support Vector Machine basiert. 

Support Vector Machines (SVMs) sind eine gut erforschte Klasse von überwachten Learning-Methoden. Diese spezielle Implementierung eignet sich zur Vorhersage von zwei möglichen Ergebnissen, die entweder auf kontinuierlichen oder kategorischen Variablen basieren.

Nachdem Sie die Modellparameter definiert haben, trainieren Sie das Modell unter Verwendung der Trainingsmodule und stellen Sie einen *getaggten Datensatz* bereit, der eine Bezeichnung oder eine Ergebnisspalte enthält.

## <a name="about-support-vector-machines"></a>Über Support Vector Machines

Support Vector Machines gehören zu den frühesten Machine Learning-Algorithmen, und SVM-Modelle wurden in vielen Anwendungen verwendet – von der Informationsbeschaffung bis zur Text- und Bildklassifizierung. SVMs können für sowohl Klassifizierungs-als auch Regressionsaufgaben verwendet werden.

Diese SVM-Modell ist ein überwachtes Learning-Modell, für das bezeichnete Daten erforderlich sind. Im Trainingsprozess analysiert der Algorithmus Eingabedaten und erkennt Muster in einem mehrdimensionalen Featureraum namens *Hyperebene*.  Alle Eingabebeispiele werden in diesem Raum als Punkte dargestellt und auf Ausgabekategorien so abgebildet, dass Kategorien durch eine möglichst große Breite geteilt werden und eine Lücke schließen.

Für die Vorhersage ordnet der SVM-Algorithmus neue Beispiele der einen oder anderen Kategorie zu und ordnet sie dem gleichen Raum zu. 

## <a name="how-to-configure"></a>Vorgehensweise zur Konfiguration 

Für diesen Modelltyp wird empfohlen, dass Sie den Datensatz normalisieren, bevor Sie ihn zum Trainieren des Klassifikators verwenden.
  
1.  Fügen Sie das Modul **Zweiklassige Support Vector Machine** zu Ihrem Experiment hinzu.  
  
2.  Geben Sie an, wie das Modell trainiert werden soll, indem Sie die Option **Trainermodus erstellen** aktivieren.  
  
    -   **Single Parameter** (Einzelner Parameter): Wenn Sie wissen, wie Sie das Modell konfigurieren möchten, können Sie einen bestimmten Satz von Werten als Argumente angeben.  

3.  Geben Sie für **Anzahl der Iterationen** eine Zahl ein, die die Anzahl der beim Erstellen des Modells verwendeten Iterationen angibt.  
  
     Dieser Parameter kann verwendet werden, um den Kompromiss zwischen Trainingsgeschwindigkeit und Genauigkeit zu steuern.  
  
4.  Geben Sie für **Lambda** einen Wert ein, der als Gewichtung für die L1-Regularisierung verwendet werden soll.  
  
     Dieser Regularisierungskoeffizient kann zur Optimierung des Modells verwendet werden. Größere Werte führen zu komplexeren Modellen.  
  
5.  Wählen Sie die Option **Features normalisieren**, wenn Sie Features vor dem Training normalisieren möchten.
  
     Wenn Sie die Normalisierung anwenden, werden die Datenpunkte vor dem Training auf den Mittelwert zentriert und auf eine Einheit der Standardabweichung skaliert.
  
6.  Wählen Sie die Option **In Einheitssphäre projizieren**, um die Koeffizienten zu normalisieren.
  
     Das Projizieren von Werten zum Einheitsraum bedeutet, dass die Datenpunkte vor dem Training auf 0 zentriert und auf eine Einheit der Standardabweichung skaliert werden.
  
7.  Geben Sie unter **Zufälliger Ausgangswert** einen ganzzahligen Wert ein, der als Ausgangswert verwendet werden soll, wenn Sie die Reproduzierbarkeit über mehrere Ausführungen hinweg sicherstellen möchten.  Andernfalls wird ein von der Systemuhr bereitgestellter Wert als Ausgangswert verwendet, was bei jeder Ausführung zu geringfügig unterschiedlichen Ergebnissen führen kann.
  
9. Stellen Sie eine Verbindung mit einem bezeichneten Dataset und einem der [Trainingsmodule](module-reference.md) her:
  
    -   Wenn Sie **Trainermodus erstellen** auf **Einzelparameter** festlegen, müssen Sie das Modul [Modell trainieren](train-model.md) verwenden.
  

10. Führen Sie das Experiment aus.

## <a name="results"></a>Ergebnisse

Nach Abschluss des Trainings:

+ Um eine Zusammenfassung der Parameter des Modells sowie der aus dem Training gewonnenen Featuregewichtungen, klicken Sie mit der rechten Maustaste auf die Ausgabe von [Modell trainieren](./train-model.md), und wählen Sie **Visualisieren** aus.

+ Um mit den trainierten Modellen Vorhersagen zu treffen, verbinden Sie das trainierte Modell mit dem Modul [Modell bewerten](score-model.md).


## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich die [Gruppe der verfügbaren Module](module-reference.md) für Azure Machine Learning Service an. 