---
title: 'K-Means Clustering: Modulreferenz'
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie das Modul „K-Means Clustering“ in Azure Machine Learning zum Trainieren von Clusteringmodellen verwendet wird.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 08/04/2020
ms.openlocfilehash: 7e0b61c1ca6ae30044e4c9d4705bdce01eac1942
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93323682"
---
# <a name="module-k-means-clustering"></a>Modul: K-Means Clustering

In diesem Artikel wird beschrieben, wie Sie das Modul *k-Means-Algorithmus* im Azure Machine Learning-Designer verwenden, um ein untrainiertes k-Means-Algorithmusmodell zu erstellen. 
 
k-Means ist einer der einfachsten und bekanntesten *unüberwachten* Lernalgorithmen. Sie können den Algorithmus für eine Vielzahl von Aufgaben des maschinellen Lernens verwenden, z. B.: 

* [Erkennen anormaler Daten](/archive/msdn-magazine/2013/february/data-clustering-detecting-abnormal-data-using-k-means-clustering)
* Clustering von Textdokumenten
* Analysieren von Datasets vor der Verwendung anderer Klassifizierungs- oder Regressionsmethoden 

So erstellen Sie ein Clusteringmodell:

* Fügen Sie dieses Modul Ihrer Pipeline hinzu.
* Verbinden Sie ein Dataset.
* Legen Sie Parameter fest, z. B. die erwartete Anzahl der Cluster, die bei der Erstellung der Cluster zu verwendende Abstandsmetrik usw. 
  
Nachdem Sie die Hyperparameter des Moduls konfiguriert haben, verbinden Sie das untrainierte Modell mit dem Modul [Train Clustering Model](train-clustering-model.md). Da der k-Means-Algorithmus eine unbeaufsichtigte Lernmethode ist, ist eine Bezeichnungsspalte optional. 

+ Wenn Ihre Daten eine Bezeichnung enthalten, können Sie die Bezeichnungswerte verwenden, um die Auswahl der Cluster zu steuern und das Modell zu optimieren. 

+ Wenn Ihre Daten keine Bezeichnung haben, erstellt der Algorithmus Cluster, die mögliche Kategorien darstellen, ausschließlich basierend auf den Daten.  

##  <a name="understand-k-means-clustering"></a>Grundlegendes zum k-Means-Algorithmus
 
Im Allgemeinen werden beim Clustering iterative Techniken befolgt, um Fälle in einem Datensatz in Clustern zu gruppieren, die ähnliche Merkmale aufweisen. Diese Gruppierungen eignen sich für die Erkundung von Daten, die Erkennung von Anomalien in den Daten und möglicherweise für Vorhersagen. Clusteringmodelle können Ihnen auch helfen, Beziehungen in einem Dataset zu erkennen, die sich möglicherweise durch Durchsuchen oder einfaches Beobachten nicht logisch zu erkennen geben. Aus diesen Gründen erfolgt das Clustering oft in der Frühphase maschineller Lernaufgaben, um die Daten zu untersuchen und unerwartete Korrelationen zu entdecken.  
  
 Wenn Sie ein Clusteringmodell mit der k-Means-Methode konfigurieren, müssen Sie den Zielwert *k* angeben, der die Anzahl der im Modell gewünschten *Schwerpunkte* angibt. Der Schwerpunkt ist ein Punkt, der für jeden Cluster repräsentativ ist. Der k-Means-Algorithmus ordnet jeden eingehenden Datenpunkt einem der Cluster zu, indem er die Summe der Quadrate innerhalb des Clusters minimiert. 
 
Bei der Verarbeitung der Trainingsdaten beginnt der k-Means-Algorithmus mit einem anfänglichen Satz von zufällig ausgewählten Schwerpunkten. Schwerpunkte dienen als Ausgangspunkte für die Cluster, und sie wenden den Lloyd-Algorithmus an, um ihre Positionen iterativ zu verbessern. Der k-Means-Algorithmus beendet die Bildung und Verbesserung von Clustern, sobald er eine oder mehrere dieser Bedingungen erfüllt:  
  
-   Die Schwerpunkte stabilisieren sich, was bedeutet, dass sich die Clusterzuweisungen für einzelne Punkte nicht mehr ändern und der Algorithmus zu einer Lösung konvergiert ist.  
  
-   Der Algorithmus hat die angegebene Anzahl von Iterationen ausgeführt.  
  
 Nach Abschluss der Trainingsphase verwenden Sie das Modul [Assign Data to Clusters](assign-data-to-clusters.md) (Zuweisen von Daten zu Clustern), um einem der Cluster neue Fälle zuzuweisen, die mithilfe des k-Means-Algorithmus gefunden wurden. Die Clusterzuweisung erfolgt durch Berechnung der Entfernung zwischen dem neuen Fall und dem Schwerpunkt jedes Clusters. Jeder neue Fall wird dem Cluster mit dem nächstgelegenen Schwerpunkt zugeordnet.  

## <a name="configure-the-k-means-clustering-module"></a>Konfigurieren des k-Means-Algorithmusmoduls
  
1.  Fügen Sie Ihrer Pipeline das Modul **K-Means Clustering** hinzu.  
  
2.  Geben Sie an, wie das Modell trainiert werden soll, indem Sie die Option **Create trainer mode** (Trainermodus erstellen) auswählen.  
  
    -   **Single Parameter** (Einzelner Parameter): Wenn Sie die genauen Parameter kennen, die Sie im Clusteringmodell verwenden möchten, können Sie eine bestimmte Menge von Werten als Argumente angeben.  
  
3.  Für **Number of centroids** (Anzahl der Schwerpunkte) geben Sie die Anzahl von Clustern ein, mit der der Algorithmus beginnen soll.  
  
     Es gibt keine Garantie dafür, dass das Modell genau diese Anzahl von Clustern produziert. Der Algorithmus beginnt mit dieser Anzahl von Datenpunkten und iteriert, um die optimale Konfiguration zu finden. Sie können auf den [Quellcode von sklearn](https://github.com/scikit-learn/scikit-learn/blob/fd237278e/sklearn/cluster/_kmeans.py#L1069) verweisen.
  
4.  Die Eigenschaften unter **Initialization** (Initialisierung) werden verwendet, um den Algorithmus anzugeben, der zum Definieren der anfänglichen Clusterkonfiguration verwendet wird.  
  
    -   **First N** (Erste N): Eine bestimmte Anfangsanzahl von Datenpunkten wird im Dataset ausgewählt und als Anfangsmittelwert verwendet. 
    
         Diese Methode wird auch als *Forgy-Methode* bezeichnet.  
  
    -   **Random** (Zufällig): Der Algorithmus platziert einen Datenpunkt nach dem Zufallsprinzip in einem Cluster und berechnet dann den Anfangsmittelwert als Schwerpunkt der zufällig zugewiesenen Punkte des Clusters. 

         Diese Methode wird auch als Methode der *zufälligen Partition* bezeichnet.  
  
    -   **k-Means++** : Dies ist die Standardmethode für die Initialisierung von Clustern.  
  
         Der Algorithmus **k-Means ++** wurde 2007 von David Arthur und Sergei Vassilvitskii vorgeschlagen, um schlechtes Clustering durch den standardmäßigen k-Means Algorithmus zu vermeiden. **k-Means ++** verbessert den standardmäßigen k-Means-Algorithmus durch Verwenden einer anderen Methode zur Auswahl der anfänglichen Clusterzentren.  
  
    
5.  Geben Sie für **Random number seed** (zufällig gewählter Startwert) optional einen Wert ein, der als Startwert für die Clusterinitialisierung verwendet wird. Dieser Wert kann erhebliche Auswirkungen auf die Clusterauswahl haben.  
  
6.  Wählen Sie für **Metric** (Metrik) die Funktion, mit der Sie die Entfernung zwischen Clustervektoren oder zwischen neuen Datenpunkten und dem zufällig gewählten Schwerpunkt messen möchten. Azure Machine Learning unterstützt die folgenden Metriken für die Clusterentfernung:  
  
    -   **Euclidean** (Euklidisch): Die euklidische Entfernung wird beim K-Means-Clustering häufig als ein Maß der Clusterstreuung verwendet. Diese Metrik wird bevorzugt, da sie die mittlere Entfernung zwischen Punkten und den Schwerpunkten minimiert.
  
7.  Geben Sie für **Iterations** (Iterationen) die Anzahl der Iterationen des Algorithmus über die Trainingsdaten ein, bevor Sie die Auswahl der Schwerpunkte abschließen.  
  
     Sie können diesen Parameter so anpassen, dass die Genauigkeit im Verhältnis zur Trainingszeit ausgeglichen wird.  
  
8.  Wählen Sie für **Assign label mode** (Bezeichnungsmodus zuweisen) eine Option, die angibt, wie eine Bezeichnungsspalte, falls im Dataset vorhanden, behandelt werden soll.  
  
     Da K-Means-Clustering eine unüberwachte maschinelle Lernmethode ist, sind Bezeichnungen optional. Wenn Ihr Dataset jedoch bereits eine Bezeichnungsspalte aufweist, können Sie diese Werte verwenden, um die Auswahl der Cluster zu steuern, oder Sie können festlegen, dass die Werte ignoriert werden sollen.  
  
    -   **Ignore label column** (Bezeichnungsspalte ignorieren): Die Werte in der Bezeichnungsspalte werden ignoriert und nicht für die Erstellung des Modells verwendet.
  
    -   **Fill missing values** (Fehlende Werte auffüllen): Die Werte der Bezeichnungsspalte werden als Features verwendet, um die Erstellung der Cluster zu unterstützen. Wenn in Zeilen eine Bezeichnung fehlt, wird der Wert durch Verwendung anderer Features impliziert.  
  
    -   **Overwrite from closest to center** (Vom nächstgelegenen zum Zentrum überschreiben): Die Werte der Bezeichnungsspalte werden durch vorhergesagte Bezeichnungswerte ersetzt, wobei die Bezeichnung des Punkts verwendet wird, der dem aktuellen Schwerpunkt am nächsten liegt.  

8.  Wählen Sie die Option **Features normalisieren** , wenn Sie Features vor dem Training normalisieren möchten.
  
     Wenn Sie die Normalisierung anwenden, werden die Datenpunkte vor dem Training von MinMaxNormalizer zu `[0,1]` normalisiert.

10. Trainieren des Modells.  
  
    -   Wenn Sie **Create trainer mode** (Trainermodus erstellen) auf **Single Parameter** (Einzelner Parameter) festlegen, fügen Sie ein mit Tags versehenes Dataset hinzu, und trainieren Sie das Modell mit dem Modul [Train Clustering Model](train-clustering-model.md) (Trainieren des Clusteringsmodells).  
  
## <a name="results"></a>Ergebnisse

Nach Abschluss der Konfiguration und des Trainings des Modells haben Sie ein Modell, mit dem Sie Ergebnisse generieren können. Es gibt jedoch mehrere Möglichkeiten, das Modell zu trainieren, die Ergebnisse anzuzeigen und sie zu nutzen: 

### <a name="capture-a-snapshot-of-the-model-in-your-workspace"></a>Erfassen einer Momentaufnahme des Modells in Ihrem Arbeitsbereich

Bei Verwendung des Moduls [Train Clustering Model](train-clustering-model.md):

1. Wählen Sie das Modul **Train Clustering Model** (Trainieren des Clusteringmodells) aus, und öffnen Sie den rechten Bereich.

2. Wählen Sie die Registerkarte **Ausgaben** aus. Wählen Sie das Symbol **Dataset registrieren** aus, um eine Kopie des trainierten Modells zu speichern.

Das gespeicherte Modell stellt die Trainingsdaten zum Zeitpunkt der Speicherung des Modells dar. Wenn Sie die in der Pipeline verwendeten Trainingsdaten später aktualisieren, wird das gespeicherte Modell nicht aktualisiert. 

### <a name="see-the-clustering-result-dataset"></a>Anzeigen des Ergebnisdatasets des Clusterings 

Bei Verwendung des Moduls [Train Clustering Model](train-clustering-model.md):

1. Klicken Sie mit der rechten Maustaste auf das Modul **Train Clustering Model**.

2. Wählen Sie **Visualisieren** aus.

### <a name="tips-for-generating-the-best-clustering-model"></a>Tipps zum Generieren des besten Clusteringmodells  

Es ist bekannt, dass der beim Clustering verwendete *Seeding* -Prozess das Modell erheblich beeinflussen kann. Seeding bedeutet die anfängliche Platzierung von Punkten in potenziellen Schwerpunkten.
 
Wenn das Dataset beispielsweise viele Ausreißer enthält und ein Ausreißer für das Seeding der Cluster gewählt wird, passen keine anderen Datenpunkte gut zu diesem Cluster, sodass der Cluster ein Singleton sein könnte. Das bedeutet, dass er ggf. nur einen Punkt aufweist.  
  
Sie können dieses Problem auf verschiedene Weise vermeiden:  
  
-   Ändern Sie die Anzahl der Schwerpunkte, und probieren Sie mehrere Startwerte aus.  
  
-   Erstellen Sie mehrere Modelle, wobei Sie die Metrik variieren oder mehr Iterationen vornehmen.  
  
Im Allgemeinen ist es bei Clusteringmodellen möglich, dass eine bestimmte Konfiguration zu einer lokal optimierten Menge von Clustern führt. Mit anderen Worten, die vom Modell zurückgegebene Menge von Clustern passt nur zu den aktuellen Datenpunkten und ist nicht für andere Daten generalisierbar. Wenn Sie eine andere Anfangskonfiguration verwendet haben, kann die k-Means-Methode eine andere (bessere) Konfiguration finden. 

## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich den [Satz der verfügbaren Module](module-reference.md) für Azure Machine Learning an.