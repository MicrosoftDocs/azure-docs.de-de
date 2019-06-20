---
title: Auswählen von Algorithmen
titleSuffix: Azure Machine Learning Studio
description: So wählen Sie einen Azure Machine Learning Studio-Algorithmus für überwachtes und nicht überwachtes Lernen bei Clustering-, Klassifizierungs- oder Regressionsexperimenten aus.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-ms.author=pakalra, previous-author=pakalra
ms.date: 03/04/2019
ms.openlocfilehash: 3bb88f2f9546ec25433061a0704bd144730bd34c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60752883"
---
# <a name="how-to-choose-algorithms-for-azure-machine-learning-studio"></a>Auswählen von Algorithmen für Azure Machine Learning Studio

Die Antwort auf die Frage „Welchen Algorithmus für Machine Learning sollte ich verwenden?“ lautet immer „Kommt darauf an“. Es hängt von der Größe, Qualität und Art der Daten ab. Es hängt davon ab, was Sie mit der Antwort anfangen möchten. Es hängt davon ab, wie die mathematische Logik des Algorithmus in Anweisungen für den Computer übersetzt wurde, den Sie verwenden. Und es hängt davon ab, wie viel Zeit Sie haben. Selbst der erfahrenste Datenanalyst kann Ihnen nicht sagen, welcher Algorithmus die besten Ergebnisse liefert, bevor er ihn ausprobiert hat.

Machine Learning Studio unterstützt moderne Algorithmen, z.B. skalierbare Boosted Decision-Strukturen, Bayessche Empfehlungssysteme, tiefe neuronale Netze und die von Microsoft Research entwickelten „Entscheidungsdschungel“. Skalierbare Open Source-Pakete für Machine Learning-Pakete, z.B. Vowpal Wabbit, sind ebenfalls enthalten. Machine Learning Studio unterstützt Algorithmen für Machine Learning für mehrklassige und binäre Klassifizierung, Regression und Clustering. Siehe die vollständigen Liste der [Machine Learning-Module](/azure/machine-learning/studio-module-reference/index).
Die Dokumentation enthält einige Informationen zu jedem Algorithmus und zur Anpassung von Parametern, um den Algorithmus für Ihre Zwecke zu optimieren.  


## <a name="the-machine-learning-algorithm-cheat-sheet"></a>Das "Machine Learning – Cheat Sheet für Algorithmen"

Das **[Cheat Sheet für Microsoft Azure Machine Learning Studio-Algorithmen](algorithm-cheat-sheet.md)** erleichtert Ihnen die Auswahl des richtigen Algorithmus des maschinellen Lernens für Ihre Predictive Analytics-Lösungen aus der Azure Machine Learning Studio-Algorithmenbibliothek.
In diesem Artikel werden Sie durch die Verwendung dieses Cheat Sheets geführt.

> [!NOTE]
> Um das Cheat Sheet herunterzuladen und parallel zu diesem Artikel als Referenz verwenden zu können, wechseln Sie zu [Machine Learning – Cheat Sheet für Algorithmen für Microsoft Azure Machine Learning Studio](algorithm-cheat-sheet.md).
> 
> 

Dieses Cheat Sheet richtet sich an eine ganz bestimmte Zielgruppe: einen unerfahrenen Datenanalysten mit Machine Learning-Kenntnissen auf Studentenniveau, der einen Algorithmus für den Einstieg in Azure Machine Learning Studio auswählen möchte. Dies bedeutet, dass einige Verallgemeinerungen und Vereinfachungen getroffen werden, Sie aber dennoch auf dem richtigen Weg sind. Dies bedeutet außerdem, dass hier zahlreiche Algorithmen nicht aufgeführt sind.

Diese Empfehlungen sind eine Zusammenstellung aus Feedback und Tipps von zahlreichen Datenanalysten und Machine Learning-Experten. Wir waren uns nicht immer einig, aber ich habe versucht, unsere Meinung zu einem groben Konsens zu vereinheitlichen. Die meisten Aussagen bei abweichenden Meinungen beginnen mit "Es hängt davon ab...".

### <a name="how-to-use-the-cheat-sheet"></a>Verwenden des Cheat Sheets

Die Pfad- und Algorithmusbezeichnungen in den Diagrammen sind wie folgt zu lesen: „Für *&lt;Pfadbezeichnung&gt;* verwenden Sie *&lt;Algorithmus&gt;* “. Beispiel: „Für *Geschwindigkeit* verwenden Sie *Logistische Regression mit zwei Klassen*“. Manchmal sind mehrere Verzweigungen gültig.
Manchmal ist keine davon eine optimale Lösung. Sie dienen als Faustregelempfehlungen. Machen Sie sich also keine Sorgen über ihre Exaktheit.
Einige Datenanalysten, mit denen wir gesprochen haben, sagten, dass die einzig sichere Methode zum Herausfinden des idealen Algorithmus darin besteht, alle auszuprobieren.

Hier folgt ein Beispiel aus dem [Azure KI-Katalog](https://gallery.azure.ai/). Dabei handelt es sich um ein Experiment, mit dem mehrere Algorithmen auf dieselben Daten angewendet und die Ergebnisse verglichen werden: [Vergleichen von Multiklassen-Klassifizierungen: Buchstabenerkennung](https://gallery.azure.ai/Details/a635502fc98b402a890efe21cec65b92).

> [!TIP]
> Eine einfach verständliche Infografikübersicht über Grundlagen von Machine Learning mit gängigen Algorithmen für häufige Probleme beim maschinellen Lernen können Sie unter [Grundlagen des maschinellen Lernens mit Algorithmusbeispielen](basics-infographic-with-algorithm-examples.md) herunterladen.

## <a name="flavors-of-machine-learning"></a>Ausprägungen des maschinellen Lernens

### <a name="supervised"></a>Beaufsichtigt

Algorithmen zum beaufsichtigten Lernen treffen Vorhersagen auf Grundlage eines Satzes von Beispielen. Beispielsweise können historische Aktienkurse herangezogen werden, um Vermutungen über zukünftige Preise abzugeben. Jedes für das Training verwendete Beispiel wird mit dem Wert der von Interesse ist, bezeichnet – in diesem Fall dem Aktienkurs. Ein Algorithmus zum beaufsichtigten Lernen sucht nach Mustern in diesen Wertebezeichnungen. Er kann alle Informationen verwenden, die relevant sein könnten – den Wochentag, die Jahreszeit, die Finanzdaten des Unternehmens, den Branchentyp, das Vorhandensein störender geopolitischer Ereignisse – und jeder Algorithmus sucht nach anderen Arten von Mustern. Nachdem der Algorithmus das beste Muster gefunden hat, das ihm möglich ist, verwendet er dieses Muster, um Vorhersagen für bezeichnungslose Testdaten zu treffen – die Preise von morgen.

Beaufsichtigtes Lernen ist eine häufige und nützliche Verwendung für das maschinelle Lernen. Mit einer Ausnahme sind alle Module in Azure Machine Learning Studio Algorithmen zum beaufsichtigten Lernen. Es gibt mehrere spezifische Typen beaufsichtigten Lernens, die in Azure Machine Learning Studio repräsentiert werden: Klassifizierung, Regression und Erkennung von Anomalien.

* **Klassifizierung**. Wenn die Daten zur Vorhersage einer Kategorie verwendet werden, wird beaufsichtigtes Lernen auch als Klassifizierung bezeichnet. Dies ist der Fall, wenn ein Bild als ein Bild einer "Katze" oder eines "Hundes" eingeordnet wird. Wenn es nur zwei Auswahlmöglichkeiten gibt, wird dies als **zweiklassige** oder **binomiale Klassifizierung** bezeichnet. Wenn es mehr Kategorien gibt, z. B. beim Vorhersagen des Gewinners des NCAA March Madness-Turniers, ist dieses Problem als **mehrklassige Klassifizierung** bekannt.
* **Regression**. Wenn ein Wert vorhergesagt wird, wie bei Aktienkursen, wird beaufsichtigtes Lernen als Regression bezeichnet.
* **Erkennung von Anomalien**. Manchmal ist es das Ziel, Datenpunkte zu identifizieren, die schlicht ungewöhnlich sind. Bei der Betrugserkennung sind z. B. alle ungewöhnlichen Muster von Kreditkartenbelastungen verdächtig. Die möglichen Variationen sind so zahlreich, und es gibt nur so wenige Trainingsbeispiele, dass es nicht praktikabel ist, zu lernen, wie betrügerische Aktivitäten aussehen. Die Betrugserkennung verfolgt den Ansatz, einfach zu lernen, wie normale Aktivitäten aussehen (unter Verwendung eines Verlaufs von nicht betrügerischen Transaktionen), und alles zu identifizieren, das sich signifikant davon unterscheidet.

### <a name="unsupervised"></a>Unbeaufsichtigt

Beim unbeaufsichtigten Lernen sind Datenpunkten keine Bezeichnungen zugeordnet. Stattdessen besteht das Ziel von Algorithmen zum unbeaufsichtigten Lernen im Organisieren der Daten in einer bestimmten Form oder in der Beschreibung ihrer Struktur. Dies kann das Gruppieren in Clustern bedeuten oder das Suchen nach unterschiedlichen Möglichkeiten zur Darstellung komplexer Daten in einfacherer oder stärker organisierter Form.

### <a name="reinforcement-learning"></a>Vertiefendes Lernen

Beim vertiefenden Lernen wählt der Algorithmus eine Aktion als Reaktion auf jeden Datenpunkt aus. Der Lernalgorithmus erhält außerdem kurz danach ein Erfolgssignal, das angibt, wie gut die Entscheidung war.
Auf dieser Grundlage ändert der Algorithmus die Strategie zum Erreichen des bestmöglichen Ergebnisses. Derzeit bietet Azure Machine Learning Studio keine Algorithmen zum vertiefenden Lernen. Vertiefendes Lernen wird häufig in der Robotik angewendet. Dabei ist der Satz der Sensorenwerte zu einem bestimmten Zeitpunkt ein Datenpunkt, und der Algorithmus muss dann die nächste Aktion des Roboters auswählen. Diese Methode eignet sich auch für Anwendungen im Zusammenhang mit dem Internet der Dinge.

## <a name="considerations-when-choosing-an-algorithm"></a>Überlegungen bei der Auswahl eines Algorithmus

### <a name="accuracy"></a>Genauigkeit

Eine so genaue Antwort wie möglich zu erhalten, ist nicht immer erforderlich.
Manchmal ist ein Näherungswert ausreichend, je nachdem, wofür Sie ihn verwenden möchten. Wenn dies der Fall ist, können Sie die Verarbeitungszeit möglicherweise erheblich verringern, indem Sie stärker Näherungsmethoden verwenden. Ein weiterer Vorteil der stärkeren Verwendung von Näherungsmethoden ist, dass sie tendenziell von sich aus Überanpassungen vermeiden.

### <a name="training-time"></a>Trainingsdauer

Die Anzahl der Minuten oder Stunden, die zum Trainieren eines Modells notwendig sind, variieren sehr stark von Algorithmus zu Algorithmus. Die Trainingsdauer ist oft eng an die Genauigkeit gebunden – das eine geht in der Regel mit dem anderen einher. Darüber hinaus reagieren einige Algorithmen empfindlicher auf die Anzahl der Datenpunkte als andere.
Wenn nur begrenzte Zeit zur Verfügung steht, kann dies die Auswahl des Algorithmus beeinflussen, insbesondere dann, wenn das Dataset groß ist.

### <a name="linearity"></a>Linearität

Viele Algorithmen für maschinelles Lernen nutzen Linearität. Lineare Klassifizierungsalgorithmen gehen davon aus, dass sich Klassen durch einer gerade Linie (oder deren höher dimensionale Entsprechung) trennen lassen. Dies schließt logistische Regression und Support Vector Machines ein (wie sie in Azure Machine Learning Studio implementiert sind).
Linear Regressionsalgorithmen gehen davon aus, dass Datentrends einer geraden Linie folgen. Diese Annahmen sind für einige Probleme nicht schlecht, verschlechtern bei anderen aber die Genauigkeit.

![Nicht lineare Klassengrenze](./media/algorithm-choice/image1.png)

***Nicht lineare Klassengrenze*** *– Die Verwendung eines linearen Klassifizierungsalgorithmus würde zu einer verringerten Genauigkeit führen.*

![Daten mit einem nicht linearen Trend](./media/algorithm-choice/image2.png)

***Daten mit einem nicht linearen Trend*** *– Die Verwendung einer linearen Regressionsmethode würde wesentlich höhere Fehler erzeugen als nötig.*

Trotz ihrer Gefahren sind lineare Algorithmen als erster Ansatzpunkt sehr beliebt. Sie sind tendenziell algorithmisch einfach und lassen sich schnell trainieren.

### <a name="number-of-parameters"></a>Anzahl von Parametern

Parameter sind die Knöpfe, an denen ein Datenanalyst drehen kann, wenn er einen Algorithmus einrichtet. Es handelt sich dabei um Zahlen, die das Verhalten des Algorithmus beeinflussen, z. B. Fehlertoleranz oder Anzahl von Iterationen oder Optionen zwischen Varianten der Verhaltensweisen des Algorithmus. Die Trainingsdauer und die Genauigkeit des Algorithmus können beim Finden der richtigen Einstellungen manchmal sehr empfindlich reagieren. Normalerweise erfordern Algorithmen mit vielen Parametern die meisten Testversuche, um eine gute Kombination zu finden.

Alternativ biete Azure Machine Learning Studio den Modulblock [Parameter Sweeping](algorithm-parameters-optimize.md), der automatisch alle Parameterkombinationen bei der von Ihnen beliebig auswählbaren Granularität ausprobiert. Zwar stellt dies eine hervorragende Möglichkeit dar, um sicherzustellen, dass Sie den gesamten Parameterbereich abgedeckt haben, doch die Zeit, die zum Trainieren eines Modells erforderlich ist, steigt exponentiell mit der Anzahl der Parameter.

Der Vorteil ist, dass das Vorhandensein vieler Parameter normalerweise darauf hindeutet, dass ein Algorithmus über größere Flexibilität verfügt. Er erreicht oft eine hervorragende Genauigkeit, vorausgesetzt, dass Sie die richtige Kombination von Parametereinstellungen finden.

### <a name="number-of-features"></a>Anzahl von Merkmalen

Bei bestimmten Datentypen kann die Anzahl der Merkmale im Vergleich zur Anzahl der Datenpunkte sehr groß sein. Dies ist häufig der Fall bei Genetik- oder Textdaten. Die große Anzahl von Merkmalen kann bei einigen Lernalgorithmen dazu führen, dass sie sich verzetteln, was impraktikabel lange Trainingsdauern nach sich zieht. Support Vector Machines sind für diesen Fall besonders gut geeignet (siehe unten).

### <a name="special-cases"></a>Spezialfälle

Einige Lernalgorithmen treffen bestimmte Annahmen über die Struktur der Daten oder der gewünschten Ergebnisse. Wenn Sie einen finden, der Ihren Bedürfnissen entspricht, kann Ihnen dieser genauere Vorhersagen oder kürzere Trainingsdauern liefern.

| **Algorithmus** | **Genauigkeit** | **Trainingsdauer** | **Linearität** | **Parameter** | **Hinweise** |
| --- |:---:|:---:|:---:|:---:| --- |
| **Klassifizierung mit zwei Klassen** | | | | | |
| [Logistische Regression](/azure/machine-learning/studio-module-reference/two-class-logistic-regression) | |● |● |5 | |
| [Decision Forest (Entscheidungswald)](/azure/machine-learning/studio-module-reference/two-class-decision-forest) |● |○ | |6 | |
| [Decision Jungle (Entscheidungsdschungel)](/azure/machine-learning/studio-module-reference/two-class-decision-jungle) |● |○ | |6 |Niedriger Speicherbedarf |
| [Boosted Decision Tree (Verstärkter Entscheidungsbaum)](/azure/machine-learning/studio-module-reference/two-class-boosted-decision-tree) |● |○ | |6 |Hoher Speicherbedarf |
| [Neuronales Netzwerk](/azure/machine-learning/studio-module-reference/two-class-neural-network) |● | | |9 |[Zusätzliche Anpassung möglich](azure-ml-netsharp-reference-guide.md) |
| [Averaged Perceptron (gemitteltes Perzeptron)](/azure/machine-learning/studio-module-reference/two-class-averaged-perceptron) |○ |○ |● |4 | |
| [Support Vector Machine (Stützvektormethode)](/azure/machine-learning/studio-module-reference/two-class-support-vector-machine) | |○ |● |5 |Für große Merkmalssätze geeignet |
| [Locally Deep Support Vector Machine (lokal tiefe SVM)](/azure/machine-learning/studio-module-reference/two-class-locally-deep-support-vector-machine) |○ | | |8 |Für große Merkmalssätze geeignet |
| [Bayes' Point Machine](/azure/machine-learning/studio-module-reference/two-class-bayes-point-machine) | |○ |● |3 | |
| **Klassifizierung mit mehreren Klassen** | | | | | |
| [Logistische Regression](/azure/machine-learning/studio-module-reference/multiclass-logistic-regression) | |● |● |5 | |
| [Decision Forest (Entscheidungswald)](/azure/machine-learning/studio-module-reference/multiclass-decision-forest) |● |○ | |6 | |
| [Decision Jungle (Entscheidungsdschungel)](/azure/machine-learning/studio-module-reference/multiclass-decision-jungle) |● |○ | |6 |Niedriger Speicherbedarf |
| [Neuronales Netzwerk](/azure/machine-learning/studio-module-reference/multiclass-neural-network) |● | | |9 |[Zusätzliche Anpassung möglich](azure-ml-netsharp-reference-guide.md) |
| [one-v-all](/azure/machine-learning/studio-module-reference/one-vs-all-multiclass) |- |- |- |- |Siehe die Eigenschaften der ausgewählten zweiklassigen Methode |
| **Regression** | | | | | |
| [linear](/azure/machine-learning/studio-module-reference/linear-regression) | |● |● |4 | |
| [Bayesian linear](/azure/machine-learning/studio-module-reference/bayesian-linear-regression) | |○ |● |2 | |
| [Decision Forest (Entscheidungswald)](/azure/machine-learning/studio-module-reference/decision-forest-regression) |● |○ | |6 | |
| [Boosted Decision Tree (Verstärkter Entscheidungsbaum)](/azure/machine-learning/studio-module-reference/boosted-decision-tree-regression) |● |○ | |5 |Hoher Speicherbedarf |
| [fast forest quantile](/azure/machine-learning/studio-module-reference/fast-forest-quantile-regression) |● |○ | |9 |Verteilungen anstelle von Punktvorhersagen |
| [Neuronales Netzwerk](/azure/machine-learning/studio-module-reference/neural-network-regression) |● | | |9 |[Zusätzliche Anpassung möglich](azure-ml-netsharp-reference-guide.md) |
| [Poisson](/azure/machine-learning/studio-module-reference/poisson-regression) | | |● |5 |Technically log-linear. Für die Vorhersage von Anzahlen |
| [ordinal](/azure/machine-learning/studio-module-reference/ordinal-regression) | | | |0 |Für die Vorhersage von Rangreihenfolgen |
| **Anomalieerkennung** | | | | | |
| [Support Vector Machine (Stützvektormethode)](/azure/machine-learning/studio-module-reference/one-class-support-vector-machine) |○ |○ | |2 |Besonders für große Featuresätze geeignet |
| [PCA-basierte Erkennung von Anomalien](/azure/machine-learning/studio-module-reference/pca-based-anomaly-detection) | |○ |● |3 | |
| [K-Means](/azure/machine-learning/studio-module-reference/k-means-clustering) | |○ |● |4 |Clusteringalgorithmus |

**Algorithmuseigenschaften:**

**●** – zeigt ausgezeichnete Genauigkeit, schnelle Trainingszeiten und Verwendung von Linearität.

**○** – zeigt gute Genauigkeit und moderate Trainingszeiten

## <a name="algorithm-notes"></a>Hinweise zu den Algorithmen

### <a name="linear-regression"></a>Lineare Regression

Wie zuvor erwähnt, passt die [lineare Regression](/azure/machine-learning/studio-module-reference/linear-regression) eine Linie (oder Ebene oder Hyperebene) an das Dataset an. Es handelt sich um ein Arbeitspferd, einfach und schnell, aber möglicherweise ein wenig zu stark vereinfachend für einige Probleme.

![Daten mit einem linearen Trend](./media/algorithm-choice/image3.png)

***Daten mit einem linearen Trend***

### <a name="logistic-regression"></a>Logistische Regression

Obwohl sie „Regression“ im Namen trägt, handelt es sich bei der logistischen Regression tatsächlich um ein leistungsfähiges Tool für die [zweiklassige](/azure/machine-learning/studio-module-reference/two-class-logistic-regression) und [mehrklassige](/azure/machine-learning/studio-module-reference/multiclass-logistic-regression) Klassifizierung. Sie ist schnell und einfach. Die Tatsache, dass sie eine S-förmige Kurve anstelle einer geraden Linie verwendet, prädestiniert sie für den Einsatz beim Unterteilen von Daten in Gruppen. Die logistische Regression liefert lineare Klassengrenzen. Wenn Sie sie also verwenden, vergewissern Sie sich, dass Sie mit einer linearen Näherung leben können.

![Logistische Regression mit zweiklassigen Daten mit nur einem Merkmal](./media/algorithm-choice/image4.png)

***Eine logistische Regression mit zweiklassigen Daten mit nur einem Merkmal*** *– die Klassengrenze ist der Punkt, an dem die logistische Kurve gleich nah an beiden Klassen ist*

### <a name="trees-forests-and-jungles"></a>Bäume, Wälder und Dschungel

Decision Forests (Entscheidungswälder; [Regression](/azure/machine-learning/studio-module-reference/decision-forest-regression), [zweiklassig](/azure/machine-learning/studio-module-reference/two-class-decision-forest) und [mehrklassig](/azure/machine-learning/studio-module-reference/multiclass-decision-forest)), Decision Jungles (Entscheidungsdschungel; [zweiklassig](/azure/machine-learning/studio-module-reference/two-class-decision-jungle) und [mehrklassig](/azure/machine-learning/studio-module-reference/multiclass-decision-jungle)) und Boosted Decision Trees (verstärkte Entscheidungsbäume; [Regression](/azure/machine-learning/studio-module-reference/boosted-decision-tree-regression) und [zweiklassig](/azure/machine-learning/studio-module-reference/two-class-boosted-decision-tree)) basieren alle auf Entscheidungsbäumen (Decision Trees), einem grundlegenden Machine Learning-Konzept. Es gibt viele Varianten von Entscheidungsbäumen, aber alle machen dasselbe – sie unterteilen den Merkmalsraum in Bereiche mit größtenteils derselben Bezeichnung. Dies können Bereiche mit konsistenter Kategorie oder mit konstantem Wert sein, je nachdem, ob Sie eine Klassifizierung oder Regression durchführen.

![Entscheidungsbaum unterteilt einen Merkmalsraum](./media/algorithm-choice/image5.png)

***Ein Entscheidungsbaum unterteilt einen Merkmalsraum in Bereiche mit grob einheitlichen Werten***

Da ein Merkmalsraum in beliebig kleine Bereiche unterteilt werden kann, ist es gut vorstellbar, ihn so fein zu unterteilen, dass jeder Bereich einen Datenpunkt enthält. Dies ist ein extremes Beispiel für Überanpassung. Um dies zu vermeiden, wird ein großer Satz von Strukturen erzeugt, wobei besondere mathematische Sorgfalt darauf verwendet wird, sicherzustellen, dass die Strukturen nicht korreliert sind. Der Durchschnitt dieses "Entscheidungswalds" (Decision Forest) ist ein Baum, der eine Überanpassung vermeidet. Entscheidungswälder können sehr speicherintensiv sein. Entscheidungsdschungel sind eine Variante, die weniger Speicher verbraucht, auf Kosten einer etwas längeren Trainingsdauer.

Verstärkte Entscheidungsbäume (Boosted Decision Trees) vermeiden eine Überanpassung, indem sie einschränken, wie oft sie unterteilen können und wie wenige Datenpunkte in jedem Bereich zulässig sind. Der Algorithmus erstellt eine Sequenz von Bäumen, in der jeder lernt, den vom vorherigen Baum übrig gebliebenen Fehler zu kompensieren. Das Ergebnis ist ein sehr genauer Lerner, der dazu neigt, sehr viel Arbeitsspeicher zu verwenden. Die vollständige technische Beschreibung finden Sie im [Originalartikel von Friedman](https://www-stat.stanford.edu/~jhf/ftp/trebst.pdf).

[Fast Forest Quantile Regression](/azure/machine-learning/studio-module-reference/fast-forest-quantile-regression) ist eine Variation von Entscheidungsbäumen für den Spezialfall, dass wir nicht nur den typischen Wert (Median) der Daten innerhalb eines Bereichs wissen möchten, sondern auch seine Verteilung in Form von Quantilen.

### <a name="neural-networks-and-perceptrons"></a>Neuronale Netzwerke und Perzeptrons

Neuronale Netzwerke sind vom Gehirn inspirierte Lernalgorithmen, die [mehrklassige](/azure/machine-learning/studio-module-reference/multiclass-neural-network), [zweiklassige](/azure/machine-learning/studio-module-reference/two-class-neural-network) und [Regressionsprobleme](/azure/machine-learning/studio-module-reference/neural-network-regression) abdecken. Es gibt eine unendliche Vielfalt von neuronalen Netzwerken, aber alle in Azure Machine Learning Studio enthaltenen haben die Form von gerichteten azyklischen Graphen (Directed Acyclic Graphs, DAG). Das bedeutet, dass Eingabemerkmale durch eine Abfolge von Schichten vorwärts weitergegeben werden (niemals zurück), bevor sie in Ausgaben umgewandelt werden. In jeder Schicht werden Eingaben in verschiedenen Kombinationen gewichtet, summiert und an die nächste Schicht übergeben. Diese Kombination aus einfachen Berechnungen resultiert in der Fähigkeit, komplexe Klassengrenzen und Datentrends lernen zu können – fast schon Zauberei. Netzwerke dieser Art mit vielen Schichten führen das "tiefe Lernen" aus, das den Stoff für so viele Technikberichte und Science-Fiction liefert.

Diese hohe Leistung gibt es jedoch nicht umsonst. Das Training neuronaler Netzwerke kann sehr lange dauern, insbesondere bei großen Datasets mit vielen Merkmalen. Sie verfügen außerdem über mehr Parameter als die meisten Algorithmen, was bedeutet, dass "Parameter Sweeping" die Trainingsdauer extrem verlängert.
Und für die Streber, die [ihre eigene Netzwerkstruktur spezifizieren](azure-ml-netsharp-reference-guide.md)möchten, sind die Möglichkeiten geradezu unerschöpflich.

![Von neuronalen Netzwerken gelernte Grenzen](./media/algorithm-choice/image6.png)

***Die von neuronalen Netzwerken gelernten Grenzen können komplex und unregelmäßig sein.***

Das [Two-Class Averaged Perceptron](/azure/machine-learning/studio-module-reference/two-class-averaged-perceptron) (zweiklassiges, gemitteltes Perzeptron) ist die neuronale Netzwerkantwort auf in den Himmel schießende Trainingsdauern. Er verwendet eine Netzwerkstruktur, die lineare Klassengrenzen liefert. Nach heutigen Standards ist es geradezu primitiv, aber es funktioniert schon sehr lange robust und ist klein genug, um schnell zu lernen.

### <a name="svms"></a>SVMs

Support Vector Maschines (SVMs) finden die Grenze, die Klassen mit maximalem Abstand voneinander trennt. Wenn sich die beiden Klassen nicht klar trennen lassen, findet der Algorithmus die bestmögliche Grenze. In der in Azure Machine Learning Studio geschriebenen Form erzielt die [zweiklassige SVM](/azure/machine-learning/studio-module-reference/two-class-support-vector-machine) dies ausschließlich mit einer geraden Linie (der entsprechende SVM-Begriff lautet „linearer Kernel“).
Da sie diese linearen Näherung vollzieht, kann sie relativ schnell ausgeführt werden. Ihre Spezialität sind Daten mit umfassenden Merkmalen, z. B. Texte oder Genomdaten. In diesen Fällen können SVMs Klassen schneller und mit weniger Überanpassung trennen als die meisten anderen Algorithmen, wobei sie zusätzlich auch nur recht bescheidene Speichermengen beanspruchen.

![Support Vector Machine-Klassengrenze](./media/algorithm-choice/image7.png)

***Eine typische Normale Support Vector Machine-Klassengrenze maximiert den Abstand, der zwei Klassen trennt.***

Ein weiteres Produkt von Microsoft Research, die [Two-Class Locally Deep SVM](/azure/machine-learning/studio-module-reference/two-class-locally-deep-support-vector-machine) (zweiklassige lokal tiefe SVM), ist eine nicht lineare Variante der SVM, die den größten Teil der Geschwindigkeit und Speichereffizienz der linearen Version erhält. Sie eignet sich optimal für Fälle, in denen die Ergebnisse des linearen Ansatzes nicht genau genug sind. Die Entwickler erzielten eine hohe Geschwindigkeit, indem sie das Problem in eine Reihe von kleineren linearen SVM-Problemen aufteilten. Ausführliche Informationen, wie sie das geschafft haben, finden Sie in der [vollständigen Beschreibung](http://proceedings.mlr.press/v28/jose13.html) .

Unter Verwendung einer raffinierten Erweiterung der nicht linearen SVMs zieht die [einklassige SVM](/azure/machine-learning/studio-module-reference/one-class-support-vector-machine) eine Grenze, die das gesamte Dataset eng umreißt. Sie eignet sich gut für die Erkennung von Anomalien. Alle neuen Datenpunkte, die weit außerhalb dieser Grenze liegen, sind ungewöhnlich genug, um beachtenswert zu sein.

### <a name="bayesian-methods"></a>Bayes-Methoden

Bayes-Methoden haben eine extrem wünschenswerte Eigenschaft: Sie vermeiden Überanpassungen. Sie erreichen dies, indem sie im Voraus einige Annahmen über die wahrscheinliche Verteilung der Antwort treffen. Ein weiterer Nebeneffekt dieses Ansatzes ist, dass sie nur sehr wenige Parameter besitzen. Azure Machine Learning Studio enthält Bayes-Algorithmen sowohl für Klassifizierung ([Two-Class Bayes' Point Machine](/azure/machine-learning/studio-module-reference/two-class-bayes-point-machine)) als auch für Regression ([Bayes Linear Regression](/azure/machine-learning/studio-module-reference/bayesian-linear-regression)).
Beachten Sie, dass diese voraussetzen, dass die Daten durch eine gerade Linie aufgeteilt oder angepasst werden können.

Rein historisch sei darauf hingewiesen, dass Bayes' Point Machines bei Microsoft Research entwickelt wurden. Ihnen liegt einige außergewöhnlich schöne theoretische Arbeit zugrunde. Der interessierte Student sei auf den [Originalartikel in JMLR](http://jmlr.org/papers/volume1/herbrich01a/herbrich01a.pdf) und einen [aufschlussreichen Blog von Chris Bishop](https://blogs.technet.com/b/machinelearning/archive/2014/10/30/embracing-uncertainty-probabilistic-inference.aspx) verwiesen.

### <a name="specialized-algorithms"></a>Spezialisierte Algorithmen
Wenn Sie ein sehr spezielles Ziel haben, könnten Sie Glück haben. In der Sammlung von Azure Machine Learning Studio gibt es Algorithmen mit verschiedenen Spezialisierungen:

- Rangvorhersage ([Ordinal-Regression](/azure/machine-learning/studio-module-reference/ordinal-regression))
- Anzahlvorhersage ([Poisson-Regression](/azure/machine-learning/studio-module-reference/poisson-regression))
- Anomalieerkennung (ein Algorithmus basierend auf der [Hauptkomponentenanalyse](/azure/machine-learning/studio-module-reference/pca-based-anomaly-detection) (Principal Components Analysis) und einer basierend auf [Support Vector Machines](/azure/machine-learning/studio-module-reference/one-class-support-vector-machine))
- Clustering ([K-Means](/azure/machine-learning/studio-module-reference/k-means-clustering))

![PCA-basierte Erkennung von Anomalien](./media/algorithm-choice/image8.png)

***PCA-basierte Erkennung von Anomalien*** *– die überwiegende Mehrheit der Daten liegt in einer stereotypen Verteilung; von dieser Verteilung erheblich abweichende Punkte sind verdächtig*

![Mittels K-Means gruppiertes Dataset](./media/algorithm-choice/image9.png)

***Ein Dataset wird mithilfe von K-Means in fünf Cluster gruppiert***

Es gibt auch einen [mehrklassigen One-vs-All-Ensemble-Klassifizierer](/azure/machine-learning/studio-module-reference/one-vs-all-multiclass), der das N-klassige Klassifizierungsproblem in N-1 zweiklassige Klassifizierungsprobleme aufteilt. Genauigkeit, Trainingsdauer und Linearitätseigenschaften werden von den verwendeten zweiklassigen Klassifizierern bestimmt.

![Zweiklassige Klassifizierer so kombiniert, dass sie einen dreiklassigen Klassifizierer bilden](./media/algorithm-choice/image10.png)

***Ein Paar zweiklassiger Klassifizierer wird so kombiniert, dass sich ein dreiklassiger Klassifizierer ergibt***

Azure Machine Learning Studio umfasst außerdem Zugriff auf ein leistungsfähiges Machine Learning-Framework namens [Vowpal Wabbit](/azure/machine-learning/studio-module-reference/train-vowpal-wabbit-version-7-4-model).
VW widersetzt sich einer Kategorisierung, weil es sowohl Klassifizierungs- als auch Regressionsprobleme lernen und sogar von teilweise bezeichnungslosen Daten lernen kann. Sie können es so konfigurieren, dass es jeden einer Anzahl von Lernalgorithmen, Verlustfunktionen und Optimierungsalgorithmen verwendet. Es wurde von Grund auf unter den Aspekten Effizienz, Parallelität und extreme Schnelligkeit entwickelt. Es verarbeitet unglaublich große Merkmalsgruppen, ohne einen nennenswerten Aufwand zu zeigen.
Begonnen und geleitet von John Langford bei Microsoft Research, ist VW ein Formel 1-Wagen in einem Feld aus Stockcar-Algorithmen. Nicht jedes Problem eignet sich für den Einsatz von VW, aber wenn sich Ihres eignet, kann es durchaus den zeitlichen Aufwand lohnen, sich mit der Schnittstelle auseinanderzusetzen. Es steht auch als [eigenständiger Open Source-Code](https://github.com/JohnLangford/vowpal_wabbit) in mehreren Sprachen zur Verfügung.

## <a name="next-steps"></a>Nächste Schritte

* Eine einfach verständliche Infografikübersicht über Grundlagen von Machine Learning mit gängigen Algorithmen für häufige Probleme beim maschinellen Lernen können Sie unter [Grundlagen des maschinellen Lernens mit Algorithmusbeispielen](basics-infographic-with-algorithm-examples.md) herunterladen.

* Eine in Kategorien unterteilte Liste aller in Machine Learning Studio verfügbaren Algorithmen für maschinelles Lernen finden Sie in der Hilfe zu den Algorithmen und Modulen in Machine Learning Studio unter [Initialisieren des Modells](/azure/machine-learning/studio-module-reference/machine-learning-initialize-model).

* Eine vollständige alphabetische Liste aller in Machine Learning Studio verfügbaren Algorithmen und Module finden Sie in der Hilfe zu den Algorithmen und Modulen in Machine Learning Studio in der [Modulliste von A bis Z für Machine Learning Studio](/azure/machine-learning/studio-module-reference/a-z-module-list).
