---
title: 'Featureentwicklung in Data Science: Team Data Science-Prozess'
description: Informationen zu Feature Engineering und seine Rolle im Datenaufbereitungsprozess für Machine Learning
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: conceptual
ms.date: 05/14/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath, contperf-fy20q4
ms.openlocfilehash: 7cf97edcb2f2f7a23d5c899194d497deade1f507
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97031860"
---
# <a name="feature-engineering-in-data-science"></a>Featureentwicklung im Data Science-Prozess

In diesem Artikel erfahren Sie mehr über Feature Engineering und seine Rolle bei der Datenaufbereitung in Machine Learning. Lernen Sie aus den anschaulichen Beispielen, die aus [Azure Machine Learning Studio (Classic)](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio) entnommen wurden. 

* **Featureentwicklung**: Das Erstellen neuer Features aus Rohdaten, um die Vorhersageleistung des Lernalgorithmus zu erhöhen. Die entwickelten Features sollten zusätzliche Informationen erfassen, die im ursprünglichen Satz der Features nicht leicht ersichtlich sind.
* **Featureauswahl**: Das Auswählen der wichtigsten Teilmenge von Features, um die Anzahl der Dimensionen des Trainingsproblems zu verringern.

Normalerweise wird **Feature Engineering** zuerst ausgeführt, um zusätzliche Features zu generieren, und anschließend wird die **Featureauswahl** ausgeführt, um irrelevante, redundante oder hoch korrelierte Features zu entfernen.

Feature Engineering und Featureauswahl sind Teil der [Modellierungsphase](lifecycle-modeling.md) des Team Data Science-Prozesses (TDSP). Weitere Informationen zum TDSP und zum Data Science-Lebenszyklus finden Sie unter [Was ist der Team Data Science-Prozess (TDSP)?](overview.md)

## <a name="what-is-feature-engineering"></a>Was ist Feature Engineering?

Trainingsdaten bestehen aus einer Matrix von Zeilen und Spalten. Jede Zeile in der Matrix ist eine Beobachtung oder ein Datensatz. Die Spalten jeder Zeile sind die Features, mit denen die einzelnen Datensätze beschrieben werden. Die im Entwurf des Experiments angegebenen Features sollen die Muster in den Daten beschreiben.

Obwohl viele der Rohdatenfelder direkt zum Trainieren eines Modells verwendet werden können, ist es häufig erforderlich, zusätzliche (entwickelte) Features für ein verbessertes Trainingsdataset zu erstellen.

Entwickelte Features, die das Training verbessern, liefern Informationen, die die Muster in den Daten besser differenzieren. Doch dieser Prozess nicht einfach. Fundierte und produktive Entscheidungen erfordern häufig Fachwissen.

## <a name="example-1-add-temporal-features-for-a-regression-model"></a>Beispiel 1: Hinzufügen von temporären Features zu einem Regressionsmodell

Das Experiment [Vorhersage der Anzahl ausgeliehener Fahrräder](https://gallery.azure.ai/Experiment/Regression-Demand-estimation-4) in Azure Machine Learning Studio (Classic) soll veranschaulichen, wie Features für Regressionsaufgaben entwickelt werden können. Dieses Experiment soll den Bedarf an vermieteten Fahrrädern für eine(n) bestimmte(n) Monat/Tag/Stunde vorhersagen.

### <a name="bike-rental-dataset"></a>Fahrradvermietungsdataset

Das [UCI-Dataset zur Fahrradvermietung](http://archive.ics.uci.edu/ml/datasets/Bike+Sharing+Dataset/) basiert auf echten Daten eines Fahrradverleihers in den USA. Es stellt die Anzahl von geliehenen Fahrrädern zu einer bestimmten Tageszeit in den Jahren 2011 und 2012 dar. Es enthält 17.379 Zeilen und 17 Spalten.

Die unformatierte Featuregruppe enthält Wetterbedingungen (Temperatur/Luftfeuchtigkeit/Windgeschwindigkeit) und den Typ des Tags (Feiertag/Wochentag). Das Feld für die Vorhersage ist die Anzahl, die die ausgeliehenen Fahrräder innerhalb einer bestimmten Tageszeit darstellt. Die Anzahl reicht von 1 bis 977.

### <a name="create-a-feature-engineering-experiment"></a>Erstellen eines Feature Engineering-Experiments

Mit dem Ziel, effektive Features in den Trainingsdaten zu entwickeln, werden vier Regressionsmodelle mit demselben Algorithmus, jedoch mit vier verschiedenen Trainingsdatasets erstellt. Die vier Datasets enthalten dieselben Roheingabedaten, jedoch mit einer steigenden Anzahl von festgelegten Features. Diese Features sind in vier Kategorien unterteilt:

1. A = Wetter + Feiertag + Wochenende + Wochenendfeatures für den vorhergesagten Tag
2. B = Anzahl von Fahrrädern, die in jeder der letzten 12 Stunden geliehen wurden
3. C = Anzahl von Fahrrädern, die an jedem der vergangenen 12 Tage zur gleichen Stunde geliehen wurden
4. D = Anzahl von Fahrrädern, die in jeder der vorherigen 12 Wochen zur gleichen Stunde und am gleichen Tag geliehen wurden

Neben Featuregruppe A, die bereits in den ursprünglichen Rohdaten vorhanden ist, werden die anderen drei Featuregruppen über den Featureentwicklungsprozess erstellt. Featuregruppe B erfasst die jüngste Nachfrage nach Fahrrädern. Featuregruppe C erfasst die Nachfrage nach Fahrrädern zu einer bestimmten Stunde Featuregruppe D erfasst die Nachfrage nach Fahrrädern zu bestimmten Uhrzeiten an bestimmten Wochentagen. Die vier Trainingsdatasets umfassen jeweils Featuregruppe A, A+B, A+B+C und A+B+C+D.

### <a name="feature-engineering-using-studio-classic"></a>Feature Engineering mit Studio (Classic)

Diese vier Trainingsdatasets werden im Studio (Classic)-Experiment über vier Verzweigungen aus den vorbearbeiteten Eingabedatasets gebildet. Mit Ausnahme der am weitesten links liegenden Verzweigung enthält jede dieser Verzweigungen das Modul [Execute R Script](/azure/machine-learning/studio-module-reference/execute-r-script), in dem die abgeleiteten Features (Featuregruppe B, C und D) erstellt und an das importierte Dataset angehängt werden.

Die folgende Abbildung veranschaulicht das R-Skript, das zur Erstellung der Featuregruppe B in der zweiten Verzweigung von links verwendet wird.

![Features erstellen](./media/create-features/addFeature-Rscripts.png)

### <a name="results"></a>Ergebnisse

Ein Vergleich der Leistungsergebnisse der vier Modelle ist in der folgenden Tabelle zusammengefasst: 

![Ergebnisvergleich](./media/create-features/result1.png)

Die besten Ergebnisse werden mit den Features A+B+C angezeigt. Die Fehlerrate ist niedriger, wenn eine zusätzliche Featuregruppe in den Trainingsdaten enthalten ist. Dies bestätigt die Vermutung, dass die Featuregruppen B und C weitere relevante Informationen für die Regressionsaufgabe liefern. Das Hinzufügen des Features D scheint jedoch keine zusätzliche Verringerung der Fehlerrate zu ergeben.

## <a name="example-2-create-features-for-text-mining"></a><a name="example2"></a> Beispiel 2: Erstellen von Features für Textmining

Feature Engineering wird häufig in Aufgaben verwendet, die mit Textmining im Zusammenhang stehen, z. B. Dokumentklassifizierung und Stimmungsanalyse. Da einzelne Teile des Textinhalts in der Regel als Eingabedaten dienen, ist Feature Engineering erforderlich, um Features in Zusammenhang mit Häufigkeiten von Wörtern/Ausdrücken zu erstellen.

### <a name="feature-hashing"></a>Feature Hashing

Für diese Aufgabe wird ein Verfahren namens [Feature Hashing](/azure/machine-learning/studio-module-reference/feature-hashing) angewendet, um beliebige Textfeatures effizient in Indizes zu verwandeln. Statt jedes Textfeature (Wörter/Ausdrücke) einem bestimmten Index zuzuweisen, wird bei dieser Methode durch Anwenden einer Hashfunktion auf die Features und die direkte Verwendung ihrer Hashwerte als Indizes ein Ergebnis erzielt.

Studio (Classic) enthält das Modul [Feature Hashing](/azure/machine-learning/studio-module-reference/feature-hashing), das Wort/Ausdruck-Features bequem erstellt. Die folgende Abbildung zeigt ein Beispiel für die Verwendung dieses Moduls. Das Eingabedataset enthält zwei Spalten: die Buchbewertung, die im Bereich von 1 bis 5 liegt, und den tatsächlichen Inhalt der Bewertung. Dieses Modul dient dem Zweck, eine Reihe von neuen Features abzurufen, die die Häufigkeit des Auftretens entsprechender Wörter/Ausdrücke innerhalb der jeweiligen Buchbewertung zeigen. Um dieses Modul zu verwenden, führen Sie die folgenden Schritte aus:

* Wählen Sie zuerst die Spalte, die den eingegebenen Text enthält (in diesem Beispiel "Col2").
* Legen Sie dann "Hashing bitsize" auf 8 fest, d. h. 2^8 = 256 Features werden erstellt. Die Wörter/Ausdrücke im gesamten Text werden auf 256 Indizes gehasht. Der Parameter "Hashing bitsize" reicht von 1 bis 31. Die Wörter/Ausdrücke werden mit geringerer Wahrscheinlichkeit in den gleichen Index gehasht, wenn eine größere Zahl festgelegt wird.
* Setzen Sie dann den Parameter "N-grams" auf 2. Mit diesem Wert wird die Häufigkeit der Unigramme (ein Feature für jedes einzelne Wort) und Bigramme (ein Feature für jedes Paar angrenzender Wörter) aus dem Eingabetext abgerufen. Der Parameter "N-grams" reicht von 0 bis 10, wodurch die maximale Anzahl sequenzieller Wörter in einem Feature angezeigt wird.  

!["Feature Hashing"-Modul](./media/create-features/feature-Hashing1.png)

Die folgende Abbildung zeigt, wie dieses neue Feature aussehen wird.

!["Feature Hashing"-Beispiel](./media/create-features/feature-Hashing2.png)

## <a name="conclusion"></a>Zusammenfassung
Entwickelte und ausgewählte Features steigern die Effizienz des Trainingsprozesses, der versucht, die in den Daten enthaltenen Schlüsselinformationen zu extrahieren. Sie verbessern auch die Leistungsfähigkeit dieser Modelle für eine exakte Klassifizierung der Eingabedaten und verlässlichere Vorhersagen von Ergebnissen, die von Interesse sind.

Entwicklung und Auswahl von Features können auch kombiniert werden, damit das Lernen besser rechnerisch verfolgt werden kann. Dies erfolgt durch eine Erweiterung und anschließende Verringerung der Anzahl von Features zum Kalibrieren oder Trainieren eines Modells. Mathematisch gesehen stehen die ausgewählten Features für einen minimalen Satz von unabhängigen Variablen, die Muster in den Daten erklären und erfolgreich Vorhersageergebnisse liefern.

Feature Engineering bzw. Featureauswahl muss nicht immer unbedingt erfolgen. Dies hängt von den Daten, dem ausgewählten Algorithmus und dem Ziel des Experiments ab.

## <a name="next-steps"></a>Nächste Schritte

Informationen zum Erstellen von Features für Daten in bestimmten Umgebungen finden Sie in den folgenden Artikeln:

* [Erstellen von Features für Daten in SQL Server](create-features-sql-server.md)
* [Erstellen von Features für Daten in einem Hadoop-Cluster mit Hive-Abfragen](create-features-hive.md)