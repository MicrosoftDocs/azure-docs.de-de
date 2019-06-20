---
title: 'Train Model (Modell trainieren): Modulreferenz'
titleSuffix: Azure Machine Learning service
description: Erfahren Sie, wie Sie das Modul **Train Model** (Modell trainieren) in Azure Machine Learning Service zum Trainieren eines Klassifizierungs- oder Regressionsmodells verwenden.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 29d0f698456b83c1520a92bc7df47b26540325f4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65027644"
---
# <a name="train-model-module"></a>Train Model-Modul

In diesem Artikel wird ein Modul der grafischen Benutzeroberfläche (Vorschau) für Azure Machine Learning Service beschrieben.

Verwenden Sie dieses Modul, um ein Klassifizierungs- oder Regressionsmodell zu trainieren. Das Training findet statt, nachdem Sie ein Modell definiert und die zugehörigen Parameter festgelegt haben. Für das Training werden markierte Daten benötigt. Darüber hinaus können Sie **Train Model** auch verwenden, um ein bestehendes Modell erneut anhand neuer Daten zu trainieren. 

## <a name="how-the-training-process-works"></a>So funktioniert der Trainingsprozess

In Azure Machine Learning werden Machine Learning-Modelle in der Regel in einem dreistufigen Prozess erstellt und verwendet. 

1. Um ein Modell zu konfigurieren, wählen Sie einen bestimmten Algorithmustyp aus und definieren dann die zugehörigen Parameter oder Hyperparameter. Wählen Sie einen der folgenden Modelltypen: 

    + **Klassifizierungsmodelle** auf der Basis neuronaler Netze, Entscheidungsstrukturen und Entscheidungswälder sowie weitere Algorithmen
    + **Regressionsmodelle**, die lineare Standardregressionen umfassen können, oder die andere Algorithmen wie neuronale Netze und bayessche Regressionen verwenden  

2. Geben Sie ein Dataset an, das mit einer Bezeichnung versehen wurde und über Daten verfügt, die mit dem Algorithmus kompatibel sind. Verbinden Sie sowohl die Daten als auch das Modell mit **Train Model**.

    Beim Training wird ein bestimmtes Binärformat namens iLearner erstellt, in dem die aus den Daten abgeleiteten Statistikmuster gekapselt sind. Es ist nicht möglich, dieses Format direkt zu ändern oder zu lesen. Das trainierte Modell kann jedoch von anderen Modulen verwendet werden. 
    
    Darüber hinaus können Sie die Eigenschaften des Modells anzeigen. Weitere Informationen finden Sie im Abschnitt „Ergebnisse“.

3. Nach Abschluss des Trainings verwenden Sie das trainierte Modell mit einem der [Bewertungsmodule](./score-model.md), um Vorhersagen für neue Daten zu treffen.

## <a name="how-to-use-train-model"></a>Gewusst wie: Verwenden von **Train Model**  
  
1.  Konfigurieren Sie in Azure Machine Learning ein Klassifizierungs- oder Regressionsmodell.
    
2. Fügen Sie dem Experiment das Modul **Train Model** hinzu.  Sie finden dieses Modul unter der Kategorie **Machine Learning**. Erweitern Sie **Train** (Trainieren), und ziehen Sie das Modul **Train Model** dann in Ihr Experiment.
  
3.  Fügen Sie den untrainierten Modus an die linke Eingabe an. Fügen Sie das Trainingsdataset an die rechte Eingabe von **Train Model** an.

    Das Trainingsdataset muss eine Bezeichnungsspalte enthalten. Zeilen ohne Bezeichnung werden ignoriert.
  
4.  Klicken Sie für **Label column** (Bezeichnungsspalte) auf **Launch column selector** (Spaltenauswahl starten), und wählen Sie eine einzelne Spalte mit Ergebnissen aus, die vom Modell zu Trainingszwecken verwendet werden können.
  
    - Bei Klassifizierungsproblemen muss die Bezeichnungsspalte entweder **kategorische** oder **diskrete** Werte enthalten. Einige Beispiele sind: Ja/keine Bewertung, ein Code oder ein Name zur Klassifizierung von Krankheiten oder eine Gehaltsgruppe.  Wenn Sie eine Spalte mit nicht kategorischen Werten auswählen, gibt das Modul während des Trainings einen Fehler zurück.
  
    -   Bei Regressionsproblemen muss die Bezeichnungsspalte **numerische** Daten enthalten, die die Antwortvariable darstellen. Im Idealfall stellen die numerischen Daten eine fortlaufende Skala dar. 
    
    Beispiele sind: eine Kreditrisikobewertung, die prognostizierte Zeit bis zum Ausfall einer Festplatte oder die vorausgesagte Anzahl von Call-Center-Anrufen an einem bestimmten Tag oder zu einer bestimmten Uhrzeit.  Wenn Sie keine numerische Spalte auswählen, kann dies zu einem Fehler führen.
  
    -   Wenn Sie die zu verwendende Bezeichnungsspalte nicht angeben, versucht Azure Machine Learning, die geeignete Bezeichnungsspalte anhand der Metadaten des Datasets abzuleiten. Wenn die falsche Spalte ausgewählt wird, korrigieren Sie dies mithilfe der Spaltenauswahl.
  
    > [!TIP] 
    > Tipps zur richtigen Verwendung der Spaltenauswahl finden Sie im Artikel zum [Auswählen von Spalten im Dataset](./select-columns-in-dataset.md). Dort werden einige häufige Szenarien beschrieben, und Sie erhalten Tipps zur Verwendung der Optionen **WITH RULES** und **BY NAME**.
  
5.  Führen Sie das Experiment aus. Wenn Sie über eine große Datenmenge verfügen, kann dies eine Weile dauern.

## <a name="bkmk_results"></a> Ergebnisse

Nachdem das Modell trainiert wurde:

+ Um Modellparameter und Featuregewichtungen anzuzeigen, klicken Sie mit der rechten Maustaste auf die Ausgabe, und wählen Sie **Visualize** (Visualisieren) aus.
+ Um das Modell in weiteren Experimenten zu verwenden, klicken Sie mit der rechten Maustaste auf das Modell, und wählen Sie **Save Model** (Modell speichern) aus. Geben Sie einen Namen für das Modell ein. 

    Dadurch wird das Modell als Momentaufnahme gespeichert, die durch wiederholte Ausführungen des Experiments aber nicht aktualisiert wird.
+ Um das Modell zur Vorhersage neuer Werte zu verwenden, verbinden Sie es mit dem Modul [Score Model](./score-model.md) (Modell bewerten) sowie mit neuen Eingabedaten.


## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich die [Gruppe der verfügbaren Module](module-reference.md) für Azure Machine Learning Service an. 