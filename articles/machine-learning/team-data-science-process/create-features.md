---
title: 'Featureentwicklung in Data Science: Team Data Science-Prozess'
description: Erläutert den Zweck der Entwicklung von Features, und es werden Beispiele für ihre Rolle im Datenaufbereitungsprozess für Machine Learning vorgestellt.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/21/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 8b0973007a78b492cff1c5ffc2ce1e43116a0847
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60398637"
---
# <a name="feature-engineering-in-data-science"></a>Featureentwicklung im Data Science-Prozess
Dieser Artikel erläutert den Zweck der Erstellung von Features und stellt Beispiele ihrer Rolle im Datenaufbereitungsprozess für maschinelles Lernen vor. Die Beispiele zur Veranschaulichung dieses Prozesses stammen aus Azure Machine Learning Studio. 

Dieser Task ist ein Schritt im [Team Data Science-Prozess (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).

Bei der Featureentwicklung wird versucht, die Vorhersageleistung der Lernalgorithmen durch Erstellen von Merkmalen aus Rohdaten zu verbessern und mit diesen den Lernprozess zu vereinfachen. Entwicklung und Auswahl von Features sind ein Teil des Team Data Science-Prozesses, der unter [Was ist der Team Data Science-Prozesslebenszyklus?](overview.md) beschrieben ist. Entwicklung und Auswahl von Features sind Teile des Schritts **Entwickeln von Features** des TDSP. 

* **Featureentwicklung**: Bei diesem Prozess wird versucht, zusätzliche relevante Features aus den vorhandenen Rohfeatures in den Daten zu erstellen und die Vorhersageleistung des Lernalgorithmus zu steigern.
* **Featureauswahl**: Dieser Prozess wählt bei dem Versuch, die Anzahl von Dimensionen des Trainingsproblems zu verringern, die wichtigste Teilmenge der ursprünglichen Datenfeatures aus.

Normalerweise wird die **Featureentwicklung** zuerst ausgeführt, um zusätzliche Features zu generieren, und anschließend wird die **Featureauswahl** ausgeführt, um irrelevante, redundante oder hoch korrelierte Features zu entfernen.

Die beim Machine Learning verwendeten Trainingsdaten können häufig durch Extrahieren von Features aus den gesammelten Rohdaten verbessert werden. Ein Beispiel für ein entwickeltes Feature im Kontext des Lernvorgangs, wie Bilder von handschriftlichen Zeichen klassifiziert werden, ist eine aus den Rohdaten der Bitverteilung erstellte Bitdichtekarte. Diese Karte kann helfen, die Kanten der Zeichen effizienter als einfach mit der direkten Rohdatenverteilung zu ermitteln.

Informationen zum Erstellen von Features für Daten in bestimmten Umgebungen finden Sie in den folgenden Artikeln:

* [Erstellen von Features für Daten in SQL Server](create-features-sql-server.md)
* [Erstellen von Features für Daten in einem Hadoop-Cluster mit Hive-Abfragen](create-features-hive.md)

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="create-features-from-your-data---feature-engineering"></a>Erstellen von Features aus Ihren Daten – Featureentwicklung
Die Trainingsdaten bestehen aus einer aus Bespielen zusammengesetzten Matrix (in Zeilen gespeicherte Datensätze oder Beobachtungen), von denen jedes eine Reihe von Features (in Spalten gespeicherten Variablen oder Feldern) aufweist. Die Features, die in der Experimentanordnung angegeben werden, beschreiben wahrscheinlich die Muster in den Daten. Obwohl viele der Rohdatenfelder direkt in die ausgewählte Featuregruppe einbezogen werden können, die zum Trainieren eines Modells dient, müssen oft zusätzliche (entwickelte) Features aus den Features in den Rohdaten erstellt werden, um ein verbessertes Trainingsdataset generieren zu können.

Welche Art von Features sollte erstellt werden, um das Dataset beim Trainieren eines Modells zu verbessern? Entwickelte Features, die das Training verbessern, liefern Informationen, die die Muster in den Daten besser differenzieren. Die neuen Features sollen zusätzliche Informationen bereitstellen, die in den ursprünglichen oder vorhandenen Featuregruppen nicht eindeutig erfasst werden können oder nicht einfach ersichtlich sind. Doch dieser Prozess nicht einfach. Fundierte und produktive Entscheidungen erfordern häufig ein gewisses Fachwissen.

Wenn Sie Azure Machine Learning zum ersten Mal verwenden, ist es am einfachsten, wenn Sie sich diesen Prozess anhand der Beispiele in Studio verdeutlichen. Zwei Beispiele sind hier dargestellt:

* Ein Beispiel für die Regression: [Vorhersage der Anzahl ausgeliehener Fahrräder](https://gallery.cortanaintelligence.com/Experiment/Regression-Demand-estimation-4) in einem überwachten Experiment, in dem die Zielwerte bekannt sind
* Ein Text Mining-Klassifizierungsbeispiel mit [Feature Hashing](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/)

## <a name="example-1-add-temporal-features-for-a-regression-model"></a>Beispiel 1: Hinzufügen von temporären Features zu einem Regressionsmodell
Das Experiment "Vorhersage der Anzahl ausgeliehener Fahrräder" in Azure Machine Learning Studio soll veranschaulichen, wie Features für Regressionsaufgaben entwickelt werden können. Dieses Experiment soll den Bedarf an Fahrrädern, d. h. die Anzahl von vermieteten Fahrrädern für einen bestimmten Monat/Tag/Stunde, vorhersagen. Das Dataset "Bike Rental UCI" liefert die Rohdaten für die Eingabe. Dieses Dataset basiert auf echten Daten des Unternehmens Capital Bikeshare, das eine Fahrradvermietung in Washington DC in den USA betreibt. Das Dataset stellt die Anzahl von geliehenen Fahrrädern zu einer bestimmten Tageszeit in den Jahren 2011 und 2012 dar und enthält 17.379 Zeilen und 17 Spalten. Die unformatierte Featuregruppe enthält Wetterbedingungen (Temperatur/Luftfeuchtigkeit/Windgeschwindigkeit) und den Typ des Tags (Feiertag/Wochentag). Das Feld für die Vorhersage ist der Zähler „cnt“, der die ausgeliehenen Fahrräder innerhalb einer bestimmten Tageszeit darstellt und von 1 bis 977 reicht.

Mit dem Ziel, effektive Features in den Trainingsdaten zu entwickeln, werden vier Regressionsmodelle mit demselben Algorithmus, jedoch mit vier verschiedenen Trainingsdatasets erstellt. Die vier Datasets enthalten dieselben Roheingabedaten, jedoch mit einer steigenden Anzahl von festgelegten Features. Diese Features sind in vier Kategorien unterteilt:

1. A = Wetter + Feiertag + Wochenende + Wochenendfeatures für den vorhergesagten Tag
2. B = Anzahl von Fahrrädern, die in jeder der letzten 12 Stunden geliehen wurden
3. C = Anzahl von Fahrrädern, die an jedem der vergangenen 12 Tage zur gleichen Stunde geliehen wurden
4. D = Anzahl von Fahrrädern, die in jeder der vorherigen 12 Wochen zur gleichen Stunde und am gleichen Tag geliehen wurden

Neben Featuregruppe A, die bereits in den ursprünglichen Rohdaten vorhanden ist, werden die anderen drei Featuregruppen über den Featureentwicklungsprozess erstellt. Featuregruppe B erfasst die jüngste Nachfrage nach Fahrrädern. Featuregruppe C erfasst die Nachfrage nach Fahrrädern zu einer bestimmten Stunde Featuregruppe D erfasst die Nachfrage nach Fahrrädern zu bestimmten Uhrzeiten an bestimmten Wochentagen. Die vier Trainingsdatasets umfassen jeweils Featuregruppe A, A+B, A+B+C und A+B+C+D.

Diese vier Trainingsdatasets werden im Azure Machine Learning-Experiment über vier Verzweigungen aus den vorbearbeiteten Eingabedatasets gebildet. Mit Ausnahme der am weitesten links liegenden Verzweigung enthält jede dieser Verzweigungen das Modul [Execute R Script](https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/), in dem die abgeleiteten Features (Featuregruppe B, C und D) erstellt und an das importierte DataSet angehängt werden. Die folgende Abbildung veranschaulicht das R-Skript, das zur Erstellung der Featuregruppe B in der zweiten Verzweigung von links verwendet wird.

![Features erstellen](./media/create-features/addFeature-Rscripts.png)

Ein Vergleich der Leistungsergebnisse der vier Modelle ist in der folgenden Tabelle zusammengefasst: 

![Ergebnisvergleich](./media/create-features/result1.png)

Die besten Ergebnisse werden mit den Features A+B+C angezeigt. Beachten Sie, dass die Fehlerrate niedriger ist, wenn eine zusätzliche Featuregruppe in den Trainingsdaten enthalten ist. Dies bestätigt die Vermutung, dass die Featuregruppen B und C weitere relevante Informationen für die Regressionsaufgabe liefern. Das Hinzufügen des Features D scheint jedoch keine zusätzliche Verringerung der Fehlerrate zu ergeben.

## <a name="example2"></a> Beispiel 2: Erstellen von Features beim Text Mining
Die Featureentwicklung wird häufig in Aufgaben verwendet, die mit Text Mining im Zusammenhang stehen, z.B. Dokumentklassifizierung und Stimmungsanalyse. Wenn z.B. Dokumente in verschiedene Kategorien klassifiziert werden sollen, ist eine typische Annahme, dass die in der einen Dokumentkategorie enthaltenen Wörter/Ausdrücke mit geringerer Wahrscheinlichkeit in der anderen Dokumentkategorie auftreten. Anders ausgedrückt kann die Häufigkeit der Verteilung von Wörtern und Ausdrücken verschiedene Dokumenttypen charakterisieren. In Text Mining-Anwendungen, bei denen einzelne Teile des Textinhalts in der Regel als Eingabedaten dienen, ist der Featureentwicklungsprozess erforderlich, um Features in Zusammenhang mit Häufigkeiten von Wörtern/Ausdrücken zu erstellen.

Für diese Aufgabe wird ein Verfahren namens **Feature Hashing** angewendet, um beliebige Textfeatures effizient in Indizes zu verwandeln. Statt jedes Textfeature (Wörter/Ausdrücke) einem bestimmten Index zuzuweisen, wird bei dieser Methode durch Anwenden einer Hashfunktion auf die Features und die direkte Verwendung ihrer Hashwerte als Indizes ein Ergebnis erzielt.

Azure Machine Learning enthält ein Modul [Feature Hashing](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/) , das diese Wort/Ausdruck-Features bequem erstellt. Die folgende Abbildung zeigt ein Beispiel für die Verwendung dieses Moduls. Das Eingabedataset enthält zwei Spalten: die Buchbewertung, die im Bereich von 1 bis 5 liegt, und den tatsächlichen Inhalt der Bewertung. Das Ziel dieses [Feature Hashing](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/) -Moduls ist, eine Reihe von neuen Features abzurufen, die die Häufigkeit des Auftretens entsprechender Wörter/Ausdrücke innerhalb der jeweiligen Buchbewertung zeigen. Um dieses Modul zu verwenden, führen Sie die folgenden Schritte aus:

* Wählen Sie zuerst die Spalte, die den eingegebenen Text enthält (in diesem Beispiel "Col2").
* Legen Sie dann "Hashing bitsize" auf 8 fest, d. h. 2^8 = 256 Features werden erstellt. Die Wörter/Ausdrücke im gesamten Text werden auf 256 Indizes gehasht. Der Parameter "Hashing bitsize" reicht von 1 bis 31. Die Wörter/Ausdrücke werden mit geringerer Wahrscheinlichkeit in den gleichen Index gehasht, wenn eine größere Zahl festgelegt wird.
* Setzen Sie dann den Parameter "N-grams" auf 2. Mit diesem Wert wird die Häufigkeit der Unigramme (ein Feature für jedes einzelne Wort) und Bigramme (ein Feature für jedes Paar angrenzender Wörter) aus dem Eingabetext abgerufen. Der Parameter "N-grams" reicht von 0 bis 10, wodurch die maximale Anzahl sequenzieller Wörter in einem Feature angezeigt wird.  

!["Feature Hashing"-Modul](./media/create-features/feature-Hashing1.png)

Die folgende Abbildung zeigt, wie dieses neue Feature aussehen wird.

!["Feature Hashing"-Beispiel](./media/create-features/feature-Hashing2.png)

## <a name="conclusion"></a>Zusammenfassung
Entwickelte und ausgewählte Features steigern die Effizienz des Trainingsprozesses, der versucht, die in den Daten enthaltenen Schlüsselinformationen zu extrahieren. Sie verbessern auch die Leistungsfähigkeit dieser Modelle für eine exakte Klassifizierung der Eingabedaten und verlässlichere Vorhersagen von Ergebnissen, die von Interesse sind. Entwicklung und Auswahl von Features können auch kombiniert werden, damit das Lernen besser rechnerisch verfolgt werden kann. Dies erfolgt durch eine Erweiterung und anschließende Verringerung der Anzahl von Features zum Kalibrieren oder Trainieren eines Modells. Mathematisch gesehen stehen die Features, die zum Trainieren des Modells ausgewählt werden, für einen minimalen Satz von unabhängigen Variablen, die Muster in den Daten erklären und dann erfolgreich Vorhersageergebnisse liefern.

Die Entwicklung bzw. Auswahl von Features muss nicht immer unbedingt erfolgen. Ob sie benötigt werden, hängt von den vorliegenden oder gesammelten Daten, dem ausgewählten Algorithmus und dem Ziel des Experiments ab.

