---
title: Erstellen eines Standpunktanalysemodells
titleSuffix: Azure Machine Learning Studio
description: Erstellen von Textanalysemodellen in Azure Machine Learning Studio unter Verwendung von Modulen für Textvorverarbeitung, N-Gramme oder Feature Hashing
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 03/14/2018
ms.openlocfilehash: 08d62e7a6c9503d415fe144da57eee72ce3bfafd
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60636567"
---
# <a name="create-a-sentiment-analysis-model-in-azure-machine-learning-studio"></a>Erstellen eines Standpunktanalysemodells in Azure Machine Learning Studio

Mit Azure Machine Learning Studio können Sie Textanalysemodelle erstellen und operationalisieren. Diese Modelle können Ihnen z.B. helfen, Probleme bei der Klassifizierung von Dokumenten oder Stimmungsanalyse zu lösen.

In einem Experiment zur Textanalyse würden Sie in der Regel Folgendes tun:

1. Bereinigen und Vorverarbeiten des Textdatasets
2. Extrahieren numerischer Featurevektoren aus vorverarbeitetem Text
3. Trainieren des Klassifikations- oder Regressionsmodells
4. Bewerten und Validieren des Modells
5. Bereitstellen des Modells für die Produktion

In diesem Tutorial erlernen Sie die erforderlichen Schritte, während wir ein Stimmungsanalysemodell mit dem Dataset „Amazon Book Reviews“ schrittweise durcharbeiten (weitere Informationen finden Sie in der Forschungsarbeit „Biographies, Bollywood, Boom-boxes and Blenders: Domain Adaptation for Sentiment Classification“ von John Blitzer, Mark Dredze und Fernando Pereira, Association of Computational Linguistics (ACL), 2007.) Dieses Dataset besteht aus Beurteilungsergebnissen (1 - 2 oder 4 - 5) und einem Freiformtext. Das Ziel ist die Vorhersage des Beurteilungsergebnisses: niedrig (1 - 2) oder hoch (4 - 5).

Sie finden Experimente, die in diesem Tutorial behandelt werden, im Azure KI-Katalog:

[Predict Book Reviews (Vorhersagen von Buchbesprechungen)](https://gallery.azure.ai/Experiment/Predict-Book-Reviews-1)

[Predict Book Reviews – Predictive Experiment (Vorhersagen von Buchbesprechungen – Vorhersageexperiment)](https://gallery.azure.ai/Experiment/Predict-Book-Reviews-Predictive-Experiment-1)

## <a name="step-1-clean-and-preprocess-text-dataset"></a>Schritt 1: Bereinigen und Vorverarbeiten des Textdatasets
Wir beginnen das Experiment mit der Aufteilung der Beurteilungsergebnisse in die Kategorienbuckets „niedrig“ und „hoch“, um das Problem als Klassifizierung in zwei Klassen zu formulieren. Wir verwenden die Module [Edit Metadata](https://msdn.microsoft.com/library/azure/dn905986.aspx) und [Group Categorical Values](https://msdn.microsoft.com/library/azure/dn906014.aspx).

![Bezeichnung erstellen](./media/text-analytics-module-tutorial/create-label.png)

Dann bereinigen wir den Text mit dem Modul [Preprocess Text](https://msdn.microsoft.com/library/azure/mt762915.aspx) . Die Bereinigung reduziert das „Rauschen“ im Dataset und hilft Ihnen, die wichtigsten Features zu finden und die Genauigkeit des endgültigen Modells zu verbessern. Wir entfernen Stoppwörter – allgemeine Wörter wie „der, die das“ oder „ein, eine, einen“ – und Ziffern, Sonderzeichen, duplizierte Zeichen, E-Mail-Adressen und URLs. Außerdem konvertieren wir den Text in Kleinbuchstaben, lemmatisieren die Wörter und erkennen Satzgrenzen, die dann im vorverarbeiteten Text mit dem Symbol „|||“ gekennzeichnet werden.

![Preprocess Text](./media/text-analytics-module-tutorial/preprocess-text.png)

Was geschieht, wenn Sie eine benutzerdefinierte Liste von Stoppwörtern verwenden möchten? Sie können sie als optionale Eingabe übergeben. Sie können auch benutzerdefinierte reguläre Ausdrücke in C#-Syntax verwenden, um Teilzeichenfolgen zu ersetzen und Wörter nach Wortklassen zu entfernen: Substantive, Verben oder Adjektive.

Nachdem die Vorverarbeitung abgeschlossen ist, teilen wir die Daten in Trainings- und Testgruppen auf.

## <a name="step-2-extract-numeric-feature-vectors-from-pre-processed-text"></a>Schritt 2: Extrahieren numerischer Featurevektoren aus vorverarbeitetem Text
Um ein Modell für Textdaten zu erstellen, müssen Sie in der Regel Freiformtext in numerische Featurevektoren konvertieren. In diesem Beispiel verwenden wir das Modul [Extract N-Gram Features from Text](https://msdn.microsoft.com/library/azure/mt762916.aspx), um die Textdaten in ein solches Format zu transformieren. Dieses Modul berechnet auf der Basis einer Spalte mit Leerzeichen getrennter Wörter ein Wörterbuch, bzw. N-Gramme von Wörtern, die in Ihrem Dataset vorkommen. Dann zählt es, wie oft jedes einzelne Wort – bzw. N-Gramm – in jedem Datensatz vorkommt, und erstellt auf der Basis dieser Zählungen Featurevektoren. In diesem Tutorial legen wir die N-Gramm-Größe auf 2 fest, damit unsere Featurevektoren einzelne Wörter und Kombinationen von zwei aufeinander folgenden Wörtern einbeziehen.

![N-Gramme extrahieren](./media/text-analytics-module-tutorial/extract-ngrams.png)

Wir wenden die TF*IDF-Gewichtung (Term Frequency Inverse Document Frequency‚ Vorkommenshäufigkeit – inverse Dokumenthäufigkeit) auf N-Gramm-Zählungen an. Dieser Ansatz bezieht die Gewichtung von Wörtern ein, die häufig in einem einzelnen Datensatz vorkommen, insgesamt jedoch im Dataset selten sind. Weitere Optionen umfassen die Binär-, TF- und Diagrammgewichtung.

Solche Textfeatures haben häufig eine hohe Dimensionalität. Wenn Ihr Korpus beispielsweise 100.000 eindeutige Wörter umfasst, hat Ihr Featureraum 100.000 Dimensionen – oder mehr, wenn N-Gramme verwendet werden. Das Modul zum Extrahieren von N-Gramm-Features bietet Ihnen eine Reihe von Optionen zum Reduzieren der Dimensionalität. Sie können wahlweise Wörter ausschließen, die zu kurz oder zu lang bzw. zu selten oder zu häufig sind, um signifikante Vorhersagewerte zu erhalten. In diesem Tutorial schließen wir N-Gramme aus, die in weniger als 5 oder mehr als 80% der Datensätze vorkommen.

Sie können die Featureauswahl auch nutzen, um nur die Features auszuwählen, die am meisten mit dem Vorhersageziel korrelieren. Wir verwenden die Chi-Quadrat-Featureauswahl, um 1.000 Features auszuwählen. Sie können das Vokabular ausgewählter Wörter oder N-Gramme durch Klicken auf die rechte Ausgabe des Moduls zum Extrahieren von N-Grammen anzeigen.

Als Alternativansatz zum Extrahieren von N-Gramm-Features können Sie das Feature Hashing-Modul verwenden. Beachten Sie jedoch, dass [Feature Hashing](https://msdn.microsoft.com/library/azure/dn906018.aspx) nicht über integrierte Funktionen zur Featureauswahl oder TF*IDF-Gewichtung verfügt.

## <a name="step-3-train-classification-or-regression-model"></a>Schritt 3: Trainieren des Klassifikations- oder Regressionsmodells
Jetzt ist der Text in numerische Featurespalten transformiert. Da das Dataset weiterhin Zeichenfolgenspalten aus vorherigen Phasen enthält, schließen wir sie mit „Select Columns in Dataset“ aus.

Anschließend verwenden wir [Two-Class Logistic Regression](https://msdn.microsoft.com/library/azure/dn905994.aspx) zur Vorhersage unseres Ziels: hohes oder niedriges Beurteilungsergebnis. An diesem Punkt wurde das Textanalyseproblem in ein reguläres Klassifizierungsproblem transformiert. Sie können die in Azure Machine Learning Studio verfügbaren Tools zur Verbesserung des Modells verwenden. Beispielsweise können Sie mit verschiedenen Klassifizierungen experimentieren, um herauszufinden, wie präzise die von ihnen gelieferten Ergebnisse sind, oder die Genauigkeit durch Optimierung der Hyperparameter verbessern.

![Trainieren und bewerten](./media/text-analytics-module-tutorial/scoring-text.png)

## <a name="step-4-score-and-validate-the-model"></a>Schritt 4: Bewerten und Validieren des Modells
Wie würden Sie das trainierte Modell validieren? Wir bewerten es anhand des Testdatasets und werten die Genauigkeit aus. Allerdings hat das Modell das Vokabular der N-Gramme und ihre Gewichtungen aus dem Trainingsdataset gelernt. Daher sollten wir dieses Vokabular und diese Gewichtungen beim Extrahieren von Features aus den Testdaten verwenden, anstatt das Vokabular neu zu erstellen. Aus diesem Grund fügen wir das Modul zum Extrahieren von N-Gramm-Features dem Bewertungszweig des Experiments hinzu, stellen eine Verbindung mit dem Ausgabevokabular vom Trainingszweig her und legen Schreibschutz für den Vokabularmodus fest. Außerdem deaktivieren wir das Filtern von N-Grammen nach Häufigkeit, indem wir als Minimum 1 Instanz und als Maximum 100% festlegen sowie die Auswahl von Features ausschalten.

Nach der Transformation der Textspalte in den Testdaten in Spalten mit numerischen Features schließen wir die Zeichenfolgenspalten aus vorherigen Phasen wie im Trainingszweig aus. Wir verwenden dann das Modul „Score Model“, um Vorhersagen zu treffen, und das Modul „Evaluate Model“, um die Genauigkeit zu bewerten.

## <a name="step-5-deploy-the-model-to-production"></a>Schritt 5: Bereitstellen des Modells für die Produktion
Das Modell ist fast bereit zur Bereitstellung für die Produktion. Bei der Bereitstellung als Webdienst akzeptiert es formfreie Zeichenfolgen als Eingabe und gibt eine „Hoch“- oder „Niedrig“-Vorhersage zurück. Es verwendet das gelernte N-Gramm-Vokabular, um den Text in Features zu transformieren, und das trainierte logistische Regressionsmodell, um auf Basis dieser Features Vorhersagen zu treffen. 

Zum Einrichten des Vorhersageexperiments speichern wir zunächst das N-Gramm-Vokabular als Dataset und das trainierte logistische Regressionsmodell aus dem Trainingszweig des Experiments. Anschließend speichern wir das Experiment mit „Save As“, um ein Experimentdiagramm für das Vorhersageexperiment zu erstellen. Wir entfernen das Modul „Split Data“ und den Trainingszweig aus dem Experiment. Wir stellen dann eine Verbindung des zuvor gespeicherten N-Gramm-Vokabulars und des Modells mit dem Modul „Extract N-Gram Features from Text“ bzw. „Score Model“ her. Wir entfernen auch das Modul „Evaluate Model“.

Wir fügen das Modul „Select Columns in Dataset“ vor dem Modul „Preprocess Text“ ein, um die Bezeichnungsspalte zu entfernen, und deaktivieren die Option „Append score column to dataset“ in „Score Model“. So fordert der Webdienst nicht die Bezeichnung an, die er vorherzusagen versucht, und wiederholt die Eingabefeatures nicht in der Antwort.

![Vorhersageexperiment](./media/text-analytics-module-tutorial/predictive-text.png)

Jetzt haben wir ein Experiment, das als Webdienst veröffentlicht und mit Anforderung/Antwort- oder Batchausführungs-APIs aufgerufen werden kann.

## <a name="next-steps"></a>Nächste Schritte
In der [MSDN-Dokumentation](https://msdn.microsoft.com/library/azure/dn905886.aspx)erfahren Sie mehr über Textanalysemodule.

