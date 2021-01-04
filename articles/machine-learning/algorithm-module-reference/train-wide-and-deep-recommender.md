---
title: Verwenden des Train Wide & Deep Recommender-Moduls
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie mit dem Modul Train Wide & Deep Recommender im Azure Machine Learning-Designer ein Empfehlungsmodell trainieren.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 06/12/2020
ms.openlocfilehash: ce713167272c9e97754fdf6f6d065519aaea3d15
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2020
ms.locfileid: "93421174"
---
# <a name="train-wide--deep-recommender"></a>Train Wide & Deep Recommender
In diesem Artikel wird die Verwendung des Moduls **Train Wide & Deep Recommender** im Azure Machine Learning-Designer zum Trainieren eines Empfehlungsmodells beschrieben. Dieses Modul basiert auf Wide & Deep Learning von Google.

Das **Train Wide & Deep Recommender**-Modul liest ein Dataset mit Benutzer-Element-Bewertung-Tripeln und optional einige Benutzer- und Elementfunktionen. Es gibt ein trainiertes Wide & Deep-Empfehlungsmodul zurück.  Mithilfe des trainierten Modells können Sie dann Bewertungen vorhersagen oder Empfehlungen generieren, indem Sie das Modul [Score Wide and Deep Recommender](score-wide-and-deep-recommender.md) verwenden.  

## <a name="more-about-recommendation-models-and-the-wide--deep-recommender"></a>Weitere Informationen zu Empfehlungsmodellen und dem Wide & Deep-Empfehlungsmodul  

Das Hauptziel eines Empfehlungssystems besteht darin, *Benutzern* des Systems *Elemente* zu empfehlen. Beispiele für Elemente sind Filme, Restaurants, Bücher oder Songs. Ein Benutzer kann eine Person, eine Gruppe von Personen oder eine andere Entität mit Elementeinstellungen sein.  

Es gibt zwei grundlegende Ansätze für Empfehlungssysteme. 

+ Der erste ist der **inhaltsbasierte** Ansatz, der Features für Benutzer und Elemente nutzt. Benutzer können durch Eigenschaften wie Alter und Geschlecht beschrieben werden und Elemente durch Eigenschaften wie Autor und Hersteller. Typische Beispiele für inhaltsbasierte Empfehlungssysteme finden Sie auf Partnersuche-Websites. 
+ Der zweite Ansatz ist die **kollaborative Filterung**, bei der nur Bezeichner der Benutzer und Elemente verwendet werden und implizite Informationen zu diesen Entitäten aus einer (Sparse-)Matrix von Bewertungen abgerufen werden, die von den Benutzern für die Elemente abgegeben wurden. Wir können etwas über einen Benutzer von den bewerteten Elementen und von anderen Benutzern erfahren, die dieselben Elemente bewertet haben.  

Beim Wide & Deep-Empfehlungsmodul werden diese Ansätze kombiniert, und das kollaborative Filtern wird zusammen mit einem inhaltsbasierten Ansatz verwendet. Es handelt sich dabei somit um ein **hybrides Empfehlungsmodul**. 

Funktionsweise: Bei einem neuen Benutzer im System werden Vorhersagen mithilfe von Featureinformationen über den Benutzer verbessert. Auf diese Weise wird das allgemein bekannte „Kaltstart“-Problem in Angriff genommen. Wenn jedoch eine ausreichende Menge von Bewertungen eines bestimmten Benutzers vorhanden ist, können vollständig personalisierte Vorhersagen für diesen basierend auf seinen spezifischen Bewertungen und nicht allein seinen Features formuliert werden. Der Übergang von inhaltsbasierten Empfehlungen zu Empfehlungen, die auf kollaborativem Filtern basieren, ist daher nahtlos. Selbst wenn Benutzer- oder Elementfeatures nicht verfügbar sind, funktioniert das Wide & Deep-Empfehlungsmodul immer noch im kollaborativen Filtermodus.  

Weitere Informationen zum Wide & Deep-Empfehlungsmodul und dem zugrunde liegenden probabilistischen Algorithmus finden Sie im entsprechenden Forschungsbericht: [Wide & Deep Learning für Empfehlungssysteme](https://arxiv.org/pdf/1606.07792.pdf).  

## <a name="how-to-configure-train-wide--deep-recommender"></a>Konfigurieren von Train Wide & Deep Recommender  

+ [Vorbereiten der Trainingsdaten](#prepare-data)
+ [Modelltraining](#train-the-model)

### <a name="prepare-data"></a>Vorbereiten von Daten

Vor der Verwendung des Moduls ist es von entscheidender Bedeutung, dass Ihre Daten in dem vom Empfehlungsmodell erwarteten Format vorliegen. Ein Trainingsdataset mit **Benutzer-Element-Bewertung-Tripeln** ist erforderlich. Sie können jedoch auch Benutzerfeatures und Elementfeatures (sofern verfügbar) in separate Datasets einbeziehen.

#### <a name="required-dataset-of-user-item-ratings"></a>Erforderliches Dataset von user-item-rating-Elementen

Die Eingabedaten für das Training müssen die richtigen Datentypen im richtigen Format enthalten: 

+ Die erste Spalte muss Benutzer-IDs enthalten.
+ Die zweite Spalte muss Element-IDs enthalten.
+ Die dritte Spalte enthält die Bewertung für das Benutzer-Element-Paar. Alle Werte müssen numerisch sein. 

Ein typischer Satz von Benutzer-Element-Bewertungen könnte z. B. wie folgt aussehen:

|UserId|MovieId|Rating|
|------------|-------------|------------|
|1|68646|10|
|223|31381|10|

#### <a name="user-features-dataset-optional"></a>Benutzerfeatures-Dataset (optional)

Das Dataset **Benutzerfeatures** muss Bezeichner für Benutzer enthalten und die gleichen Bezeichner verwenden, die in der ersten Spalte des Benutzer-Element-Bewertungen-Datasets bereitgestellt wurden. Die übrigen Spalten enthalten eine beliebige Anzahl an Features, die den Benutzer beschreiben.  

Ein typischer Satz von Benutzerfeatures könnte z. B. wie folgt aussehen: 

|UserId|Age|Geschlecht|Interesse|Standort|
|------------|--------------|-----------------------|---------------|------------|
|1|25|male| Drama    |Europa|
|223|40|female|Romanzen|Asia|

#### <a name="item-features-dataset-optional"></a>Elementfeatures-Dataset (optional)

Das Dataset mit Elementfeatures muss in der ersten Spalte Elementbezeichner enthalten. Die übrigen Spalten enthalten eine beliebige Anzahl an beschreibenden Features für die Elemente.  

Ein typischer Satz von Elementfeatures könnte z. B. wie folgt aussehen:  

|MovieId|Titel|Originalsprache|Genre|Jahr|
|-------------|-------------|-------------------|-----------|---------------|
|68646|Der Pate|Englisch|Drama|1972|
|31381|Vom Winde verweht|Englisch|Verlauf|1939|

### <a name="train-the-model"></a>Trainieren des Modells

1.  Fügen Sie das Modul **Train Wide and Deep Recommender** zu Ihrem Experiment im Designer hinzu, und verbinden Sie es mit dem Trainingsdataset.  
  
2. Wenn Sie über ein separates Dataset mit Benutzer- und/oder Elementfeatures verfügen, verbinden Sie sie mit dem **Train Wide and Deep Recommender**-Modul.  
  
    - **User features dataset** (Benutzerfeatures-Dataset): Stellen Sie eine Verbindung des Datasets, das Benutzer beschreibt, mit der zweiten Eingabe her.
    - **Item features dataset** (Elementfeatures-Dataset): Stellen Sie eine Verbindung des Datasets, das Elemente beschreibt, mit der dritten Eingabe her.  
    
3.  **Epochs** (Epochen): Geben Sie an, wie oft der Algorithmus die gesamten Trainingsdaten verarbeiten soll. 

    Je höher diese Zahl ist, desto besser ist das Training. Das Training nimmt dann jedoch mehr Zeit in Anspruch und kann zu einer Überanpassung führen.

4. **Batch size** (Batchgröße): Geben Sie die Anzahl der Trainingsbeispiele an, die in einem Trainingsschritt verwendet werden. 

     Dieser Hyperparameter kann die Trainingsgeschwindigkeit beeinflussen. Eine höhere Batchgröße führt zu einer kürzeren Kostenepoche, kann jedoch die Konvergenzzeit erhöhen. Wenn der Batch zu groß für GPU/CPU ist, kann ein Speicherfehler ausgelöst werden.

5.  **Wide part optimizer** (Optimierer für breiten Teil): Wählen Sie einen Optimierer aus, um Verläufe auf den breiten Teil des Modells anzuwenden.

6.  **Wide optimizer learning rate** (Lernrate für breiten Optimierer): Geben Sie eine Zahl zwischen 0,0 und 2,0 ein, die die Lernrate des Optimierers für den breiten Teil definiert.

    Dieser Hyperparameter bestimmt die Schrittgröße der einzelnen Trainingsschritte bei der Umstellung auf eine Funktion mit minimalem Verlust. Eine zu große Lernrate kann dazu führen, dass das Lernen das Minimum überspringt, während eine zu kleine Lernrate zu Konvergenzproblemen führen kann.

7.  **Crossed feature dimension** (Dimension für übergreifende Features): Geben Sie die Dimension ein, indem Sie die gewünschten Benutzer-IDs und Element-ID-Features eingeben. 

    Die Wide & Deep-Empfehlung führt standardmäßig eine produktübergreifende Transformation über Benutzer-ID- und Element-ID-Features aus. Das übergreifende Ergebnis wird gemäß dieser Zahl als Hashwert verwendet, um die Dimension sicherzustellen.

8.  **Deep part optimizer** (Optimierer für tiefen Teil): Wählen Sie einen Optimierer aus, um Verläufe auf den tiefen Teil des Modells anzuwenden.

9.  **Deep optimizer learning rate** (Lernrate für tiefen Optimierer): Geben Sie eine Zahl zwischen 0,0 und 2,0 ein, die die Lernrate des Optimierers für den tiefen Teil definiert.

10.  **User embedding dimension** (Benutzereinbettungsdimension): Geben Sie eine ganze Zahl ein, um die Einbettungsdimension für die Benutzer-ID festzulegen.

     Das Wide & Deep-Empfehlungsmodul erstellt die freigegebenen Benutzer-ID- und Element-ID-Einbettungen sowohl für die breiten als auch für die tiefen Teile.

11.  **Item embedding dimension** (Elementeinbettungsdimension): Geben Sie eine ganze Zahl ein, um die Einbettungsdimension für die Element-ID festzulegen.

12.  **Categorical features embedding dimension** (Einbettungsdimension für kategorische Features): Geben Sie eine ganze Zahl ein, um die Einbettungsdimensionen für kategorische Features festzulegen.

     In der tiefen Komponente des Wide & Deep-Empfehlungsmoduls wird ein Einbettungsvektor für jedes kategorische Feature erlernt. Diese Einbettungsvektoren verwenden dieselbe Dimension.

13.  **Hidden units** (Ausgeblendete Einheiten): Geben Sie die Anzahl der ausgeblendeten Knoten der tiefen Komponente ein. Die Knotennummer in jeder Ebene wird durch Kommas getrennt. Mit „1000,500,100“ geben Sie z. B. an, dass die tiefe Komponente über drei Ebenen verfügt, wobei die Ebenen von der ersten bis zur letzten jeweils über 1000 Knoten, 500 Knoten und 100 Knoten verfügen.

14.  **Activation function** (Aktivierungsfunktion): Wählen Sie eine Aktivierungsfunktion aus, die auf jede Ebene angewendet wird, Standard ist ReLU.

15.  **Dropout**: Geben Sie eine Zahl zwischen 0,0 und 1,0 ein, um die Wahrscheinlichkeit zu ermitteln, mit der die Ausgaben während des Trainings auf jeder Ebene abgelegt werden.

     Dropout ist eine Regularisierungsmethode, um eine Überanpassung neuronaler Netzwerke zu verhindern. Häufig wird mit 0,5 begonnen, was für viele Netzwerke und Aufgaben ein nahezu optimaler Wert ist.

16.  **Batch Normalization** (Batchnormalisierung): Wählen Sie diese Option aus, um die Batchnormalisierung nach jeder verborgenen Ebene in der tiefen Komponente zu verwenden.

     Die Batchnormalisierung ist eine Technik zur Bekämpfung interner Kovariantenabweichungen beim Netzwerktraining. Im Allgemeinen kann es hilfreich sein, um die Geschwindigkeit, die Leistung und die Stabilität der Netzwerke zu verbessern. 

17.  Ausführen der Pipeline.

##  <a name="technical-notes"></a>Technische Hinweise

Wide & Deep trainiert breite lineare Modelle und tiefe neuronale Netzwerke zusammen, um die Stärken des Lernens und der Generalisierung zu kombinieren. Die breite Komponente akzeptiert eine Reihe von Rohfeatures und Featuretransformationen, um Featureinteraktionen zu lernen. Mit weniger Featureentwicklung generalisiert die tiefe Komponente unbekannte Featurekombinationen mithilfe niederdimensionaler Einbettungen dichter Features. 

In der Implementierung des Wide & Deep-Empfehlungsmoduls verwendet das Modul eine Standardmodellstruktur. Die breite Komponente übernimmt Benutzereinbettungen, Elementeinbettungen und die produktübergreifende Transformation von Benutzer-IDs und Element-IDs als Eingabe. Für den tiefen Teil des Modells wird ein Einbettungsvektor für die einzelnen kategorischen Features erlernt. In Verbindung mit anderen numerischen Featurevektoren werden diese Vektoren dann in das tiefe neuronale Feedforward-Netz eingefügt. Der breite und der tiefe Teil werden kombiniert, indem sie die endgültige Ausgabeprotokollwahrscheinlichkeit als Vorhersage addieren, die schließlich zu einer gemeinsamen Verlustfunktion für das gemeinsame Training führt.


## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich den [Satz der verfügbaren Module](module-reference.md) für Azure Machine Learning an. 
