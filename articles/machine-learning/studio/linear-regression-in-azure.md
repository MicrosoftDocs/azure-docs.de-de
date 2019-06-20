---
title: Migrieren von Analysen aus Excel
titleSuffix: Azure Machine Learning Studio
description: Ein Vergleich von Modellen zur linearen Regression in Excel und in Azure Machine Learning Studio
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/20/2017
ms.openlocfilehash: f6b2f4ef9a4f3f1615081a422a16ea9f2e156571
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60861113"
---
# <a name="migrate-analytics-from-excel-to-azure-machine-learning-studio"></a>Migrieren von Analysen aus Excel zu Azure Machine Learning Studio

> *Kate Baroni* und *Ben Boatman* sind Enterprise Solution Architects im Microsoft Data Insights Center of Excellence. In diesem Artikel beschreiben die beiden ihre Erfahrungen beim Migrieren einer vorhandenen Regressionsanalysesuite zu einer cloudbasierten Lösung mit Azure Machine Learning Studio.

## <a name="goal"></a>Zielsetzung

Unser Projekt begann zunächst mit zwei Zielsetzungen: 

1. Durch den Einsatz von Vorhersageanalysen die Genauigkeit der Umsatzprognosen unserer Organisation zu verbessern 
2. Mithilfe von Azure Machine Learning Studio eine Bestätigung, Optimierung, Beschleunigung und Skalierung unserer Ergebnisse herbeizuführen. 

Wie viele andere Unternehmen durchläuft auch unsere Organisation einen monatlichen Prozess zur Erlösprognose. Unser kleines Analystenteam wurde beauftragt, diesen Prozess mithilfe von Azure Machine Learning Studio zu unterstützen und die Prognosegenauigkeit zu verbessern. Das Team sammelte im Verlauf einiger Monate Daten aus mehreren Quellen und ermittelte in dieser Zeit mit statistischen Hilfsmitteln die wichtigsten Attribute für Vertriebsprognosen im Bereich Services. Im nächsten Schritt wurden verschiedene Prototypen für die Regressionsmodelle in Excel erstellt und mit den vorhandenen Daten getestet. Innerhalb einiger Wochen hatten wir ein Regressionsmodell in Excel erstellt, das besser war als unsere derzeitigen Prognoseverfahren aus dem Finanzbereich. Dieses Modell wurde unser Prognose-Benchmark. 

Weiter ging es mit dem Versuch, unsere Vorhersageanalyse in Studio zu implementieren, um herauszufinden, wie Studio die Vorhersageleistung weiter verbessern würde.

## <a name="achieving-predictive-performance-parity"></a>Erreichen eines Leistungsgleichstands
Unsere oberste Priorität bestand darin, ein zu unserem Excel-Regressionsmodell gleichwertiges Modell in Studio zu erschaffen. Wir wollten mit denselben Daten und derselben Aufteilung von Trainings- und Testdaten einen Gleichstand der Vorhersageleistung bei Excel und Studio herstellen. Zunächst scheiterten wir dabei. Das Excel-Modell übertraf das Studio-Modell. Grund für das Scheitern waren unsere fehlenden Kenntnisse über die Basiseinstellung in Studio. Nachdem wir uns mit dem Studio-Produktteam kurzgeschlossen hatten, erhielten wir ein besseres Verständnis der erforderlichen Basiseinstellung für unsere Datasets und erzielten schließlich einen Leistungsgleichstand der beiden Modelle. 

### <a name="create-regression-model-in-excel"></a>Erstellen eines Regressionsmodells in Excel
Unsere Excel-Regression verwendete das Standardmodell für lineare Regression aus dem Excel-Analysis-ToolPak. 

Wir berechneten den *Mean Absolute % Error* und verwendeten diesen Wert als Leistungsmaß für unser Modell. Die Entwicklung des funktionierenden Excel-Modells dauerte 3 Monate. Viele Aspekte des Lernmodells konnten wir für das Studio-Experiment übernehmen, was letztlich vorteilhaft für unser Verständnis der Anforderungen war.

### <a name="create-comparable-experiment-in-studio"></a>Erstellen eines vergleichbaren Experiments in Studio
Das Erstellen unseres Experiments in Studio fand in folgenden Schritten statt: 

1. Hochladen des Datasets als CSV-Datei in Studio (sehr kleine Datei)
2. Erstellen eines neuen Experiments und Verwenden des Moduls [Select Columns in Dataset][select-columns], um die gleichen Datenfeatures wie in Excel auszuwählen 
3. Verwenden des Moduls [Split Data][split] (im Modus *Relative Expression* (Relativer Ausdruck)), um die Daten in dieselben Trainingsdatasets wie in Excel aufzugliedern 
4. Experimentieren mit dem Modul [Linear Regression][linear-regression] (nur Standardoptionen), Dokumentieren und Vergleichen mit den Ergebnissen unseres Excel-Regressionsmodells

### <a name="review-initial-results"></a>Beurteilung erster Ergebnisse
Zunächst war das Excel-Modell dem Studio-Modell deutlich überlegen: 

|  | Excel | Studio |
| --- |:---:|:---:|
| Leistung | | |
| <ul style="list-style-type: none;"><li>Adjusted R Square</li></ul> |0,96 |– |
| <ul style="list-style-type: none;"><li>Koeffizient von <br />Ermittlung</li></ul> |– |0,78<br />(geringe Genauigkeit) |
| Mittlerer absoluter Fehler |9,5 Mio. $ |19,4 Mio. $ |
| Mean Absolute Error (%) |6,03 % |12,2 % |

Als wir unser Verfahren und die Ergebnisse den Entwicklern und Datenanalysten des Machine Learning-Teams zeigten, konnte man uns dort ein paar rasche Tipps geben. 

* Bei Verwendung des Moduls [Linear Regression][linear-regression] in Studio werden zwei Methoden bereitgestellt:
  * Online Gradient Descent: Erscheint eher geeignet für umfangreichere Problemstellungen.
  * Ordinary Least Squares: Diese Methode wird allgemein am häufigsten mit dem Stichwort lineare Regression assoziiert. Für kleine DataSets ist die "Ordinary Least Squares"-Methode möglicherweise besser geeignet.
* Denken Sie an die Möglichkeit, den "L2 Regularization Weight"-Parameter anzupassen, um die Leistung zu verbessern. Standardmäßig ist er auf 0,001 eingestellt, aber für unser kleines Dataset haben wir durch Ändern des Werts auf 0,005 eine bessere Leistung erzielt. 

### <a name="mystery-solved"></a>Rätsel gelöst!
Als wir die Empfehlungen umgesetzt hatten, konnten wir in Studio denselben Benchmark erzielen wie mit Excel: 

|  | Excel | Studio (ursprünglich) | Studio mit der Methode der kleinsten Quadrate |
| --- |:---:|:---:|:---:|
| Bezeichneter Wert |Ist-Werte (numerisch) |identisch |identisch |
| Lernender |Excel -> Data Analysis -> Regression |Lineare Regression |Linear Regression |
| Lernmoduloptionen |– |Standardeinstellungen |Methode der kleinsten Quadrate<br />L2 = 0,005 |
| Dataset |26 Zeilen, 3 Funktionen, 1 Bezeichnung Alle numerisch |identisch |identisch |
| Split: Trainieren |Excel mithilfe der ersten 18 Zeilen trainiert, mit den letzten 8 Zeilen getestet |identisch |identisch |
| Split: Test |Excels Regressionsformel auf die letzten 8 Zeilen angewendet |identisch |identisch |
| **Leistung** | | | |
| Adjusted R Square |0,96 |– | |
| Coefficient of Determination |– |0,78 |0,952049 |
| Mittlerer absoluter Fehler |9,5 Mio. $ |19,4 Mio. $ |9,5 Mio. $ |
| Mean Absolute Error (%) |<span style="background-color: 00FF00;"> 6,03%</span> |12,2 % |<span style="background-color: 00FF00;"> 6,03%</span> |

Zudem gab es eine gute Übereinstimmung der Excel-Koeffizienten mit den Funktionsgewichten im trainierten Azure-Modell.

|  | Excel-Koeffizienten | Stärken der Azure-Funktion |
| --- |:---:|:---:|
| Schnittpunkt/Verschiebung |19470209,88 |19328500 |
| Funktion A |0,832653063 |0,834156 |
| Funktion B |11071967,08 |11007300 |
| Funktion C |25383318,09 |25140800 |

## <a name="next-steps"></a>Nächste Schritte
Wir wollten innerhalb von Excel den Machine Learning-Webdienst aufrufen. Unsere Analysten sind auf Excel angewiesen, daher war es wichtig, den Machine Learning-Webdienst mit einer Zeile Excel-Daten aufrufen und den Prognosewert in Excel zurückliefern zu können. 

Wir wollten unser Modell auch mithilfe der in Studio verfügbaren Optionen und Algorithmen optimieren.

### <a name="integration-with-excel"></a>Integrieren in Excel
Unsere Lösung bestand darin, das Machine Learning-Regressionsmodell in Form eines von uns aus dem Trainingsmodell erstellten Webdiensts in Betrieb zu nehmen. Der Webdienst war in wenigen Minuten fertiggestellt. Wir konnten ihn direkt in Excel aufrufen und bekamen einen Wert für die Erlösprognose zurück. 

Der Abschnitt *Web Services-Dashboard* enthält eine Excel-Arbeitsmappe als Download. Die Arbeitsmappe ist bereits vorformatiert und enthält die Webdienst-API und die Schema-Informationen. Die Arbeitsmappe wird durch Klicken auf *Download Excel Workbook* (Excel-Arbeitsmappe herunterladen) geöffnet, und Sie können sie auf Ihrem lokalen Computer speichern. 

![Herunterladen der Excel-Arbeitsmappe aus dem Web Services-Dashboard](./media/linear-regression-in-azure/machine-learning-linear-regression-in-azure-1.png)

Kopieren Sie bei geöffnetem Arbeitsblatt Ihre vordefinierten Parameter in den blauen Parameterabschnitt, wie unten gezeigt. Sobald die Parameter eingegeben werden, ruft Excel den Machine Learning-Webdienst auf, und die prognostizierten Werte werden im grünen Abschnitt „Predicted Values“ (Prognostizierte Werte) angezeigt. Das Arbeitsblatt erstellt anhand Ihres trainierten Modells fortlaufend Prognosen für alle unter Parameter eingegebenen Zeilenelemente. Weitere Informationen zum Verwenden dieser Funktion finden Sie unter [Verwenden eines Azure Machine Learning-Webdiensts aus Excel](consuming-from-excel.md). 

![Vorlage: Herstellen einer Verbindung mit dem bereitgestellten Webdienst durch eine Excel-Arbeitsmappe](./media/linear-regression-in-azure/machine-learning-linear-regression-in-azure-2.png)

### <a name="optimization-and-further-experiments"></a>Optimieren und weitere Untersuchungen
Nun, da wir mit dem Excel-Modell unseren Benchmark erreicht hatten, versuchten wir uns daran, unser Modell der linearen Regression in Machine Learning zu optimieren. Wir verwendeten das Modul [Filter-Based Feature Selection][filter-based-feature-selection], um unsere Auswahl der anfänglichen Datenelemente zu verbessern, und konnten dadurch eine Leistungsverbesserung von 4,6 % (mittlerer absoluter Fehler) erreichen. Für zukünftige Projekte werden wir diese Funktion verwenden, die uns möglicherweise wochenlanges Iterieren von Datenattributen zum Herausfinden des richtigen Funktionssatzes für die Modellierung erspart. 

Als Nächstes planen wir die Einbeziehung zusätzlicher Algorithmen wie [Bayes][bayesian-linear-regression] oder [Boosted Decision Trees][boosted-decision-tree-regression] in unser Experiment, um deren Leistung vergleichen zu können. 

Ein gutes DataSet für eigene Versuche mit Regressions-Modellen ist das Beispiel-DataSet "Energy Efficiency Regression" mit einer Vielzahl numerischer Attribute. Dieses Dataset wird als Teil der Beispieldatasets in Studio bereitgestellt. Zur Vorhersage der Kühl- bzw. Wärmelast lässt sich eine Vielzahl von Lernmodulen verwenden. Das nachstehende Diagramm zeigt einen Leistungsvergleich verschiedener Regressions-Lernmodelle im Vergleich zum Energieeffizienz-DataSet für die Zielvariable Kühllast. 

| Modell | Mittlerer absoluter Fehler | Root Mean Squared Error | Relative Absolute Error | Relative Squared Error | Coefficient of Determination |
| --- | --- | --- | --- | --- | --- |
| Boosted Decision Tree |0,930113 |1,4239 |0,106647 |0,021662 |0,978338 |
| Linear Regression (Gradient Descent) |2,035693 |2,98006 |0,233414 |0,094881 |0,905119 |
| Regression mit neuronalen Netzwerken |1,548195 |2,114617 |0,177517 |0,047774 |0,952226 |
| Linear Regression (Ordinary Least Squares) |1,428273 |1,984461 |0,163767 |0,042074 |0,957926 |

## <a name="key-takeaways"></a>Wesentliche Punkte
Wir haben durch das parallele Weiterführen von Excel-Regressionen und Studio-Experimenten viel gelernt. Durch das Erstellen des Benchmark-Modells in Excel und das anschließende Vergleichen mit Modellen, die [Linear Regression][linear-regression] mit maschinellem Lernen verwenden, konnten wir uns mit Studio vertraut machen und Möglichkeiten entdecken, die Datenauswahl und die Modellleistung zu verbessern. 

Wir haben auch festgestellt, dass es ratsam ist, [Filter-Based Feature Selection][filter-based-feature-selection] zu verwenden, um zukünftige Vorhersageprojekte zu beschleunigen. Durch Anwenden der Funktionsauswahl auf Ihre Daten lässt sich ein verbessertes Modell in Studio mit insgesamt besserer Leistung erstellen. 

Die Möglichkeit, die Vorhersageanalysen für Prognosen aus Studio auch in Excel nutzen zu können, sorgt durch die Verfügbarkeit auf verschiedenen Systemen für einen erheblich breiteren Anwenderkreis im Unternehmen. 

## <a name="resources"></a>Ressourcen
Im Folgenden finden Sie einige Ressourcen für Ihre Arbeit mit Regression: 

* Regression in Excel. Sie haben noch nie versucht, eine Regression in Excel durchzuführen? Dieses Tutorial vereinfacht sie: [https://www.excel-easy.com/examples/regression.html](https://www.excel-easy.com/examples/regression.html)
* Regression im Vergleich zu Vorhersagen. Tyler Chessman hat einen Blog-Artikel mit einer leicht verständlichen Beschreibung von linearer Regression für Anfänger verfasst, in dem er den Gebrauch von Zeitreihen zu Prognosezwecken erläutert. [http://sqlmag.com/sql-server-analysis-services/understanding-time-series-forecasting-concepts](http://sqlmag.com/sql-server-analysis-services/understanding-time-series-forecasting-concepts) 
* Ordinary Least Squares Linear Regression: Unzulänglichkeiten, Probleme und Fallstricke. Einführung und Erläuterung der Regression: [https://www.clockbackward.com/2009/06/18/ordinary-least-squares-linear-regression-flaws-problems-and-pitfalls/ ](https://www.clockbackward.com/2009/06/18/ordinary-least-squares-linear-regression-flaws-problems-and-pitfalls/)

<!-- Module References -->
[bayesian-linear-regression]: https://msdn.microsoft.com/library/azure/ee12de50-2b34-4145-aec0-23e0485da308/
[boosted-decision-tree-regression]: https://msdn.microsoft.com/library/azure/0207d252-6c41-4c77-84c3-73bdf1ac5960/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/

