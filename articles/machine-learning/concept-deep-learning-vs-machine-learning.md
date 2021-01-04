---
title: Deep Learning im Vergleich zu maschinellem Lernen
titleSuffix: Azure Machine Learning
description: Es wird beschrieben, wie Deep Learning mit maschinellem Lernen (Machine Learning) und KI zusammenhängt. Verwenden Sie in Azure Machine Learning die Deep Learning-Modelle für Betrugserkennung, Objekterkennung und mehr.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: lazzeri
author: FrancescaLazzeri
ms.date: 09/22/2020
ms.custom: contperfq1
ms.openlocfilehash: c612e2a95e2596b2732ed8282037c8ded908a0a0
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96571384"
---
# <a name="deep-learning-vs-machine-learning-in-azure-machine-learning"></a>Vergleich von Deep Learning und Machine Learning in Azure Machine Learning

Dieser Artikel enthält einen Vergleich von Deep Learning und Machine Learning sowie eine Beschreibung, wie dies mit dem weiteren Feld der künstlichen Intelligenz zusammenhängt. Informieren Sie sich über Deep Learning-Lösungen, die Sie unter Azure Machine Learning erstellen können, z. B. Betrugserkennung, Sprach- und Gesichtserkennung, Standpunktanalyse und Zeitreihenvorhersagen.

Eine Anleitung zur Auswahl von Algorithmen für Ihre Lösungen finden Sie unter [Spickzettel für Machine Learning-Algorithmen](./algorithm-cheat-sheet.md?WT.mc_id=docs-article-lazzeri).

## <a name="deep-learning-machine-learning-and-ai"></a>Deep Learning, maschinelles Lernen und KI

![Beziehungsdiagramm: KI, maschinelles Lernen und Deep Learning](./media/concept-deep-learning-vs-machine-learning/ai-vs-machine-learning-vs-deep-learning.png)

Beachten Sie die folgenden Definitionen, um Deep Learning im Vergleich zu maschinellem Lernen und KI zu verstehen:

- **Deep Learning** ist ein Teilbereich des maschinellen Lernens und basiert auf künstlichen neuronalen Netzen. Der _Lernprozess_ wird als _Deep Learning_ bezeichnet, da die Struktur von künstlichen neuronalen Netzen aus mehreren Eingabe-, Ausgabe-und verborgenen Schichten besteht. Jede Schicht enthält Einheiten, die die Eingabedaten in Informationen transformieren, damit sie von der nächsten Schicht zum Ausführen einer bestimmten Vorhersageaufgabe verwenden können. Dank dieser Struktur kann ein Computer durch seine eigene Datenverarbeitung lernen.

- **Maschinelles Lernen** ist ein Teilbereich der künstlichen Intelligenz, der Techniken (z.B Deep Learning) umfasst, über die Computer sich beim Lösen von Aufgaben durch Erfahrung verbessern können. Der _Lernprozess_ basiert auf den folgenden Schritten:

   1. Einspeisen von Daten in einen Algorithmus. (In diesem Schritt können Sie zusätzliche Informationen für das Modell bereitstellen, z.B. durch Durchführen einer Merkmalsextraktion.)
   1. Verwenden dieser Daten zum Trainieren eines Modells
   1. Testen und Bereitstellen des Modells
   1. Nutzen des bereitgestellten Modells zum Ausführen einer automatisierten Vorhersageaufgabe. (Mit anderen Worten: Aufrufen und Verwenden des bereitgestellten Modells, um die vom Modell zurückgegebenen Vorhersagen zu empfangen.)

- **Künstliche Intelligenz (KI)** ist ein Verfahren, mit dem Computer menschliche Intelligenz imitieren können. Sie umfasst auch maschinelles Lernen. 
 
Mithilfe von Machine Learning- und Deep Learning-Techniken können Sie Computersysteme und Anwendungen erstellen, die Aufgaben erledigen, die in der Regel mit menschlicher Intelligenz verbunden werden. Zu diesen Aufgaben gehören Bilderkennung, Spracherkennung und Sprachübersetzung.

## <a name="techniques-of-deep-learning-vs-machine-learning"></a>Techniken von Deep Learning und maschinellem Lernen 

Nachdem Sie sich einen Überblick über maschinelles Lernen und Deep Learning verschafft haben, können wir die beiden Verfahren vergleichen. Beim maschinellen Lernen muss dem Algorithmus mitgeteilt werden, wie er eine genaue Vorhersage treffen kann, indem er zusätzliche Informationen nutzt (z.B. durch Merkmalsextraktion). Beim Deep Learning kann der Algorithmus dank der künstlichen neuronalen Netzstruktur durch eigene Datenverarbeitung lernen, wie eine genaue Vorhersage getroffen werden kann.

In der folgenden Tabelle werden die beiden Verfahren ausführlicher verglichen:

| |Vollständig Machine Learning |Nur Deep Learning|
|---|---|---|
|  **Anzahl von Datenpunkten** | Kann mithilfe kleiner Datenmengen Vorhersagen treffen | Benötigt große Mengen von Trainingsdaten, um Vorhersagen zu treffen |
|  **Hardwareabhängigkeiten** | Kann auf Low-End-Computern ausgeführt werden. Erfordert keine hohe Rechenleistung. | Hängt von High-End-Computern ab. Grundsätzlich wird eine große Zahl von Matrixmultiplikationsvorgängen durchgeführt. Eine GPU kann diese Vorgänge effizient optimieren. |
|  **Featurebereitstellung** | Merkmale müssen von Benutzern genau identifiziert und erstellt werden. | Lernt allgemeine Merkmale aus Daten und erstellt selbstständig neue Merkmale. |
|  **Lernansatz** | Unterteilt den Lernprozess in kleinere Schritte. Anschließend werden die Ergebnisse aus den einzelnen Schritten in einer Ausgabe zusammengefasst. | Durchläuft den Lernprozess durch Lösen des Problems auf Gesamtbasis. |
|  **Ausführungszeit** | Das Training nimmt im Vergleich weniger Zeit in Anspruch: von wenigen Sekunden bis hin zu einigen Stunden. | Das Training dauert meist lange, da ein Deep Learning-Algorithmus viele Schichten aufweist. |
|  **Ausgabe** | Die Ausgabe ist normalerweise ein numerischer Wert, z.B. eine Bewertung oder Klassifizierung. | Die Ausgabe kann mehrere Formate aufweisen, z. B. einen Text, eine Bewertung oder eine Tonausgabe. |

## <a name="deep-learning-use-cases"></a>Anwendungsfälle für Deep Learning

Aufgrund der Struktur des künstlichen neuronalen Netzes zeichnet sich Deep Learning dadurch aus, dass Muster in unstrukturierten Daten identifiziert werden, z.B. Bilder, Sound, Video und Text. Aus diesem Grund verändert Deep Learning schnell viele Branchen, darunter Gesundheitswesen, Energie, Finanzwesen und Transportwesen. Diese Branchen überdenken jetzt traditionelle Geschäftsprozesse. 

Einige der häufigsten Anwendungen für Deep Learning werden in den folgenden Abschnitten beschrieben. In Azure Machine Learning können Sie ein Modell aus Ihrem Build eines Open-Source-Frameworks verwenden oder das Modell mit den bereitgestellten Tools erstellen.

### <a name="named-entity-recognition"></a>Named-entity recognition (NER)

Named-entity recognition (Erkennung benannter Entitäten) ist eine Deep Learning-Methode, die einen Text als Eingabe entgegennimmt und in eine vordefinierte Klasse umwandelt. Diese neue Angabe kann etwa eine Postleitzahl, ein Datum oder eine Produkt-ID sein. Die Informationen können dann in einem strukturierten Schema gespeichert werden, um eine Liste von Adressen zu erstellen oder als Benchmark für eine Engine zur Identitätsüberprüfung verwendet zu werden.

### <a name="object-detection"></a>Objekterkennung

Deep Learning wird in vielen Anwendungsfällen zur Objekterkennung angewandt. Die Objekterkennung erfolgt in zwei Schritten: Bildklassifizierung und anschließende Bildlokalisierung. Die Bild _klassifizierung_ identifiziert die Objekte des Bilds, z.B. Autos oder Personen. Die Bild _lokalisierung_ stellt die spezifische Position dieser Objekte bereit. 

Objekterkennung wird bereits in Branchen wie Gaming, Einzelhandel, Tourismus und selbstfahrenden Autos eingesetzt.

Informieren Sie sich über die Verwendung eines Bildklassifizierungsmodells aus einem Open-Source-Framework in Azure Machine Learning: [Klassifizieren von Bildern mit einem Pytorch-Modell](./how-to-train-pytorch.md?WT.mc_id=docs-article-lazzeri)

### <a name="image-caption-generation"></a>Generierung der Bildbeschriftung

Wie bei der Bilderkennung muss bei der Bildbeschriftung im System für ein bestimmtes Bild eine Beschriftung generiert werden, die den Inhalt des Bilds beschreibt. Wenn Sie Objekte in Fotos erkennen und bezeichnen können, besteht der nächste Schritt darin, diese Bezeichnungen in beschreibende Sätze umzuwandeln. 

Im Allgemeinen verwenden Bildbeschriftungsanwendungen Convolutional Neural Networks, um Objekte auf den Fotos zu erkennen, und dann ein Recurrent Neural Network, um die Bezeichnungen in zusammenhängende Sätze umzuwandeln.

### <a name="machine-translation"></a>Maschinelle Übersetzung

Bei der maschinellen Übersetzung werden Wörter oder Sätze aus einer Sprache automatisch in eine andere Sprache übersetzt. Die maschinelle Übersetzung gibt es schon seit geraumer Zeit, mit Deep Learning werden jedoch beeindruckende Ergebnisse in zwei bestimmten Bereichen erzielt: bei der automatischen Übersetzung von Text (und Spracherkennung) sowie bei der automatischen Übersetzung von Bildern.

Mit der richtigen Datentransformation kann ein neuronales Netz Text-, Audio- und visuelle Signale verstehen. Die maschinelle Übersetzung kann verwendet werden, um Tonausschnitte in größeren Audiodateien zu identifizieren und gesprochenen Text oder Bilder in Text zu transkribieren.

### <a name="text-analytics"></a>Textanalyse

Die auf Deep Learning-Methoden basierende Textanalyse umfasst das Analysieren großer Mengen von Textdaten (z. B. medizinischen Dokumenten oder Ausgabenbelegen), das Erkennen von Mustern und das Erstellen von organisierten und präzisen Informationen aus diesen.

Unternehmen führen mit Deep Learning Textanalysen durch, um Insidergeschäfte zu erkennen und die Einhaltung gesetzlicher Vorschriften zu gewährleisten. Ein weiteres gängiges Beispiel ist Versicherungsbetrug: Mithilfe von Textanalysen wurden häufig große Mengen von Dokumenten analysiert, um zu ermitteln, mit welcher Wahrscheinlichkeit es sich bei einem Versicherungsanspruch um Betrug handelte. 

## <a name="artificial-neural-networks"></a>Künstliche neuronale Netze

Künstliche neuronale Netze setzen sich aus Ebenen verbundener Knoten zusammen. Deep Learning-Modelle verwenden neuronale Netze mit einer sehr großen Anzahl von Schichten. 

Die gängigsten Typologien künstlicher neuronaler Netze werden in den folgenden Abschnitten erläutert.

### <a name="feedforward-neural-network"></a>Neuronales feedforward-Netz

Das neuronale Feedforward-Netz ist die einfachste Art von künstlichen neuronalen Netzen. In einem Feedforward-Netz werden Informationen nur in einer Richtung von der Eingabeschicht an die Ausgabeschicht übertragen. In neuronalen feedforward-Netzen werden Eingaben transformiert, indem sie eine Reihe von Zwischenschichten durchlaufen. Jede Schicht besteht aus einer Reihe von Neuronen, wobei jede Schicht vollständig mit allen Neuronen in der vorherigen Schicht verbunden ist. Die letzte vollständig verbundene Schicht (die Ausgabeschicht) stellt die generierten Vorhersagen dar.

### <a name="recurrent-neural-network"></a>Recurrent Neural Network

Recurrent Neural Networks sind häufig verwendete künstliche neuronale Netze. Diese Netze speichern die Ausgabe einer Schicht und geben Sie zurück in die Eingabeschicht, um das Ergebnis der Schicht vorherzusagen. Recurrent Neural Networks weisen großartige Lernmöglichkeiten auf. Sie werden häufig für komplexe Aufgaben wie Zeitreihenvorhersagen, das Erlernen von Handschrift und die Spracherkennung verwendet.

### <a name="convolutional-neural-networks"></a>Convolutional Neural Networks

Ein Convolutional Neural Network (faltendes neuronales Netz, CNN) ist ein besonders effektives künstliches neuronales Netz und stellt eine einzigartige Architektur dar. Die Schichten sind in drei Dimensionen organisiert: Breite, Höhe und Tiefe. Die Neuronen in einer Schicht sind nicht mit allen Neuronen in der nächsten Schicht, sondern nur mit einem kleinen Bereich der Neuronen der Schicht verbunden. Die endgültige Ausgabe wird auf einen einzelnen Vektor mit Wahrscheinlichkeitsbewertungen reduziert, der entlang der Tiefendimension organisiert ist. 

Convolutional Neural Networks wurden bereits in Bereichen wie Videoerkennung, Bilderkennung und Empfehlungssysteme verwendet.

## <a name="next-steps"></a>Nächste Schritte

Die folgenden Artikel enthalten weitere Optionen zur Verwendung von Open-Source-Deep Learning-Modellen in [Azure Machine Learning](./index.yml?WT.mc_id=docs-article-lazzeri):


- [Klassifizieren handschriftlicher Ziffern mit einem TensorFlow-Modell](./how-to-train-tensorflow.md?WT.mc_id=docs-article-lazzeri) 

- [Klassifizieren von handschriftlichen Ziffern mithilfe einer TensorFlow-Schätzung und Keras](./how-to-train-keras.md?WT.mc_id=docs-article-lazzeri)

- [Klassifizieren handschriftlicher Ziffern mit einem Chainer-Modell](./how-to-set-up-training-targets.md)
