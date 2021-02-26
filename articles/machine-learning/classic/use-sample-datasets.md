---
title: 'ML Studio (Classic): Verwenden der Beispieldatasets – Azure'
description: Enthält Beschreibungen der Datasets, die in Beispielmodellen von Machine Learning Studio (klassisch) verwendet werden. Sie können diese Beispieldatasets für Experimente verwenden.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: sample
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 01/19/2018
ms.openlocfilehash: 8a848f6589902c2eabf5b4e14a4d000dc4ed081e
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100518435"
---
# <a name="use-the-sample-datasets-in-azure-machine-learning-studio-classic"></a>Verwenden von Beispieldatasets in Azure Machine Learning Studio (klassisch)

**GILT FÜR:**  ![Gilt für ](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (Classic) ![Gilt nicht für ](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)


[top]: #machine-learning-sample-datasets

Beim Erstellen eines neuen Arbeitsbereichs in Azure Machine Learning Studio (klassisch) ist eine Reihe von Beispieldatasets und -experimenten standardmäßig enthalten. Viele dieser Beispieldatasets werden von den Beispielmodellen im [Azure KI-Katalog](https://gallery.azure.ai/) verwendet. Andere sind als Beispiele für verschiedene Arten von Daten vorhanden, die normalerweise für Machine Learning verwendet werden.

Einige dieser Datasets sind in Azure Blob Storage verfügbar. Für diese Datasets enthält die folgende Tabelle einen direkten Link. Sie können die Datasets mithilfe des Moduls [Import Data][import-data] in Ihren Experimenten nutzen.

Die restlichen dieser Beispieldatasets sind in Ihrem Arbeitsbereich unter **Saved Datasets** verfügbar. Sie finden dies in der Modulpalette links vom Experimentbereich in Machine Learning Studio (klassisch).
Sie können alle diese DataSets für Ihre eigenen Experimente verwenden, indem Sie sie auf Ihren Experimentcanvas ziehen.

## <a name="datasets"></a>Datasets

<table>

<tr>
  <th>Datasetname</th>
  <th>Datasetbeschreibung</th>
</tr>

<tr>
  <td>Dataset "Adult Census Income Binary Classification"</td>
  <td>
Eine Teilmenge der Volkszählungsdatenbank von 1994, die arbeitende Erwachsene (älter als 16 Jahre) mit einem bereinigten Einkommensindex über 100 verwendet.
<p></p>
<b>Verwendung:</b> Klassifizierung von Personen mithilfe von demografischen Daten für die Vorhersage, ob eine Person mehr als 50.000 pro Jahr verdient.
<p></p>
<b>Zugrunde liegende Untersuchungen:</b> Kohavi, R., Becker, B., (1996). UCI Machine Learning Repository <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>. Irvine, CA: University of California, School of Information and Computer Science </td>
</tr>

<tr>
  <td>Flughafencodes-Dataset</td>
  <td>
Flughafencodes (USA).
<p></p>
Dieses Dataset enthält eine Zeile für jeden Flughafen in den USA, in der die ID-Nummer des Flughafens zusammen mit dem Ort und Bundesstaat enthalten ist.
  </td>
</tr>

<tr>
  <td>Automobile price data (Raw)</td>
  <td>
Informationen zu Automobilen nach Marke und Modell, einschließlich Preis und Merkmalen wie Zylinderanzahl und Verbrauch sowie einer Risikoeinstufung der Versicherung.
<p></p>
Der Risikobewertung ist anfänglich mit dem Fahrzeugpreis verknüpft. Sie wird dann in einem Prozess, den Versicherungsfachleute als „Symbolisierung“ bezeichnen, an das tatsächliche Risiko angepasst. Der Wert +3 weist auf ein Fahrzeug mit hohem Risiko hin, während der Wert -3 auf ein voraussichtlich sicheres Fahrzeug hinweist.
<p></p>
<b>Verwendung:</b> Vorhersage der Risikoeinstufung nach Merkmalen unter Verwendung der Regression oder multivariaten Klassifizierung. 
<p></p>
<b>Zugrunde liegende Untersuchungen:</b> Schlimmer, J.C. (1987). UCI Machine Learning Repository <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>. Irvine, CA: University of California, School of Information and Computer Science </td>
</tr>

<tr>
  <td>Fahrradvermietung UCI-Datensatz</td>
  <td>
UCI Bike Rental-Dataset basierend auf tatsächlichen Daten der Firma Capital Bikeshare, die ein Fahrradverleih-Netzwerk in Washington DC betreibt.
<p></p>
Dieses DataSet enthält eine Zeile pro Stunde für jeden Tag der Jahre 2011 und 2012, insgesamt 17.379 Zeilen. Der Bereich der stündlich verliehenen Fahrräder reicht von 1 bis 977.

  </td>
</tr>

<tr>
  <td>RGB-Bild von Bill Gates</td>
  <td>
Öffentlich verfügbare, in CSV-Daten konvertierte Imagedatei.
<p></p>
Der Code zum Konvertieren des Bildes wird auf der Detailseite des Modells <strong>Farbquantifizierung mithilfe von K-Means-Clustering</strong> bereitgestellt.
  </td>
</tr>

<tr>
  <td>Blutspendedaten</td>
  <td>
Eine Teilmenge der Daten aus der Blutspendedatenbank des Blood Transfusion Service Centers von Hsin-Chu City, Taiwan.
<p></p>
Die Spenderdaten beinhalten die Anzahl der Monate seit der letzten Spende, die Häufigkeit oder Gesamtanzahl der Spenden, der seit der letzten Spende vergangenen Zeit sowie die Menge des gespendeten Bluts.
<p></p>
<b>Verwendung:</b> Das Ziel ist die Vorhersage über die Klassifizierung, ob der Spender im März 2007 Blut gespendet hat, wobei „1“ auf einen Spender im Zielzeitraum und „0“ auf eine Person hinweist, die nicht gespendet hat. 
<p></p>
<b>Zugrunde liegende Untersuchungen:</b> Yeh, I.C., (2008). UCI Machine Learning Repository <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>. Irvine, CA: University of California, School of Information and Computer Science
<p></p>
Yeh, I-Cheng, Yang, King-Jang und Ting, Tao-Ming, „Knowledge discovery on RFM model using Bernoulli sequence“, Expert Systems with Applications, 2008, <a href="https://dx.doi.org/10.1016/j.eswa.2008.07.018">https://dx.doi.org/10.1016/j.eswa.2008.07.018</a>
  </td>
</tr>

<tr>
  <td>Brustkrebsdaten</td>
  <td>
Eines von drei krebsbezogenen DataSets, das vom Onkologieinstitut bereitgestellt wurde, das häufig in Dokumenten zum maschinellen Lernen verwendet wird. Kombiniert Diagnoseinformationen mit Merkmalen der Laboranalyse von über 300 Gewebeproben.
<p></p>
<b>Verwendung:</b> Klassifizierung des Krebstyps auf Basis von neun Attributen, von denen einige linear, andere kategorisch sind. 
<p></p>
<b>Zugrunde liegende Untersuchungen:</b> Wohlberg, W.H., Street, W.N. und Mangasarian, O.L. (1995). UCI Machine Learning Repository <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>. Irvine, CA: University of California, School of Information and Computer Science </td>
</tr>

<tr>
  <td>Brustkrebsfunktionen <td>
Das Dataset enthält Informationen über 102.000 verdächtige Regionen (Kandidaten) von Röntgenbildern, jede beschrieben durch 117 Merkmale. Die Funktionen sind proprietär und ihre Bedeutung wird vom Dataset-Ersteller (Siemens Healthcare) nicht offengelegt. 
  </td>
</tr>

<tr>
  <td>Brustkrebsdaten</td>
  <td>
Das Dataset enthält zusätzliche Informationen für jede verdächtige Region eines Röntgenbilds. Jedes Beispiel enthält Informationen (z. B. Überschrift, Patienten-ID, die Koordinaten des Patches in Bezug auf das gesamte Bild) über die Nummer der entsprechenden Zeile im Dataset für Brustkrebsmerkmale. Jeder Patient hat eine Anzahl von Beispielen. Für Patienten, die an Krebs erkrankt sind, sind einige Beispiele positiv und einige negativ. Für Patienten, die nicht an Krebs erkrankt sind, sind alle Beispiele negativ. Das Dataset hat 102.000 Beispiele. Das Dataset ist verzerrt, 0,6 % der Punkte sind positiv, der Rest ist negativ. Das Dataset wurde von Siemens Healthcare zur Verfügung gestellt.
  </td>
</tr>

<tr>
  <td>CRM Appetency Bezeichnungen freigegeben</td>
  <td>
Beschriftungen vom KDD Cup 2009 (Kundenbeziehungsprognose, <a href="http://www.sigkdd.org/site/2009/files/orange_small_train_appetency.labels">orange_small_train_appetency.labels</a>).
  </td>
</tr>

<tr>
  <td>CRM Codeänderung Bezeichnungen freigegeben</td>
  <td>
Beschriftungen vom KDD Cup 2009 (Kundenbeziehungsprognose, <a href="http://www.sigkdd.org/site/2009/files/orange_small_train_churn.labels">orange_small_train_churn.labels</a>).
  </td>
</tr>

<tr>
  <td>CRM-Dataset gemeinsam genutzt</td>
  <td>
Diese Daten stammen vom KDD Cup 2009, Kundenbeziehungsprognose (<a href="http://www.sigkdd.org/site/2009/files/orange_small_train.data.zip">orange_small_train.data.zip</a>).
<p></p>
Das DataSet enthält 50.000 Kunden des französischen Telekommunikationsunternehmens Orange. Jeder Kunde verfügt über 230 anonymisierte Merkmale, von denen 190 numerisch und 40 kategorisch sortiert sind. Die Merkmale sind sehr karg.
  </td>
</tr>

<tr>
  <td>CRM Upselling Bezeichnungen freigegeben</td>
  <td>
Beschriftungen vom KDD Cup 2009 (Kundenbeziehungsprognose, <a href="http://www.sigkdd.org/site/2009/files/orange_large_train_upselling.labels">orange_large_train_upselling.labels</a>).
  </td>
</tr>

<tr>
  <td>Daten zur Energieeffizienzregression</td>
  <td>
Eine Sammlung von simulierten Energieprofilen, die auf 12 verschiedenen Gebäudeformen basieren. Das Gebäude unterscheiden sich in acht Merkmalen. Hierzu gehören die Verglasungsbereiche, die Verteilung der Verglasungsbereiche und die Ausrichtung.
<p></p>
<b>Verwendung:</b> Regression oder Klassifizierung zur Vorhersage der Energieeffizienzbewertung, die die Grundlage für eine von zwei Realwertantworten darstellt. Für die Multiklassen-Klassifizierung wird die Antwortvariable auf die nächste ganze Zahl gerundet. 
<p></p>
<b>Zugrunde liegende Untersuchungen:</b> Xifara, A. und Tsanas, A. (2012). UCI Machine Learning Repository <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>. Irvine, CA: University of California, School of Information and Computer Science </td>
</tr>

<tr>
  <td>Flugverspätungsdaten</td>
  <td>
Pünktlichkeitsratendaten zu Passagierflügen aus der TranStats-Datensammlung des US-Verkehrsministeriums (<a href="https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time">On-Time</a>).
<p></p>
Das DataSet deckt den Zeitraum April bis Oktober 2013 ab. Das Dataset wurde vor dem Hochladen in Azure Machine Learning Studio (klassisch) wie folgt verarbeitet:
<ul>
  <li>Es wurde gefiltert, damit nur die 70 verkehrsreichsten Flughäfen in Kontinental-USA enthalten waren.</li>
  <li>Ausgefallene Flüge wurden als um mehr als 15 Minuten verspätet gekennzeichnet.</li>
  <li>Umgeleitete Flüge wurden herausgefiltert.</li>
  <li>Folgende Spalten wurden ausgewählt: Year, Month, DayofMonth, DayOfWeek, Carrier, OriginAirportID, DestAirportID, CRSDepTime, DepDelay, DepDel15, CRSArrTime, ArrDelay, ArrDel15, Canceled</li>
</ul>
</td>
</tr>

<tr>
  <td>Planmäßige Durchführung von Flügen (unformatiert)</td>
  <td>
Datensätze zu Ankunft und Abflug von Flügen innerhalb der USA von Oktober 2011.
<p></p>
<b>Verwendung:</b> Vorhersage von Verspätungen im Flugverkehr. 
<p></p>
<b>Zugrunde liegende Untersuchungen:</b> <a href="https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time">https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time</a> (US-Verkehrsministerium).
  </td>
</tr>

<tr>
  <td>Waldbranddaten</td>
  <td>
Enthält Wetterdaten, etwa Temperatur und Luftfeuchtigkeit sowie die Windgeschwindigkeit. Die Daten stammen aus einem Gebiet im Nordosten von Portugal und sind mit Datensätzen von Waldbränden kombiniert.
<p></p>
<b>Verwendung:</b> Eine schwierige Regressionsaufgabe zur Vorhersage der bei Waldbränden verbrannten Fläche. 
<p></p>
<b>Zugrunde liegende Untersuchungen:</b> Cortez, P. und Morais, A. (2008). UCI Machine Learning Repository <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>. Irvine, CA: University of California, School of Information and Computer Science
<p></p>
[Cortez and Morais, 2007] P. Cortez und A. Morais. A Data Mining Approach to Predict Forest Fires using Meteorological Data. In: J. Neves, M. F. Santos und J. Machado Eds., New Trends in Artificial Intelligence, Proceedings of the 13th EPIA 2007 – Portuguese Conference on Artificial Intelligence, Dezember, Guimarães, Portugal, Seiten 512-523, 2007. APPIA, ISBN-13 978-989-95618-0-9. Verfügbar unter: <a href="http://www.dsi.uminho.pt/~pcortez/fires.pdf">http://www.dsi.uminho.pt/~pcortez/fires.pdf</a>.
  </td>
</tr>

<tr>
  <td>Deutsche Kreditkarte UCI-Dataset</td>
  <td>
Das DataSet „UCI Statlog“ (deutsche Kreditkarte) (<a href="https://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)">Statlog+German+Credit+Data</a>), das die Datei „german.data“ verwendet.
<p></p>
Dieses DataSet klassifiziert Personen anhand verschiedener Attribute in hohes und niedriges Kreditrisiko. Jedes Beispiel stellt eine Person dar. Insgesamt existieren 20 numerische und kategorische Merkmale sowie eine binäre Beschriftung (der Wert für das Kreditrisiko). Hohe Kreditrisiken tragen die Beschriftung = 2, während geringe Kreditrisiken die Beschriftung = 1 tragen. Die Belastung für die falsche Klassifizierung eines Beispiels mit geringem Risiko beträgt 1, während die Belastung für die falsche Klassifizierung eines Beispiels mit hohem Risiko 5 beträgt.
  </td>
</tr>

<tr>
  <td>IMDB-Filmtitel</td>
  <td>
Das Dataset enthält Informationen über Filme, die in Tweets auf Twitter bewertet wurden: Film-ID in der IMDB, Filmname, Genre und Produktionsjahr. Das DataSet enthält 17.000 Filme. Das Dataset wurde im Dokument „S. Dooms, T. De Pessemier und L. Martens. MovieTweetings: a Movie Rating Dataset Collected From Twitter. Workshop on Crowdsourcing and Human Computation for Recommender Systems, CrowdRec at RecSys 2013“ eingeführt.
  </td>
</tr>

<tr>
  <td>Zwei-Klassen-Daten zu Schwertlilien</td>
  <td>
Dies ist wahrscheinlich die bekannteste Datenbank, die in der Literatur zur Mustererkennung gefunden werden kann. Das Dataset ist relativ klein und enthält 50 Proben, die jeweils Blütenblatteinstufungen von drei Unterarten der Schwertlilie enthalten.
<p></p>
<b>Verwendung:</b> Vorhersage der Schwertlilienart aus diesen Einstufungen.  
<p></p>
<b>Zugrunde liegende Untersuchungen:</b> Fisher, R.A. (1988). UCI Machine Learning Repository <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>. Irvine, CA: University of California, School of Information and Computer Science </td>
</tr>

<tr>
  <td>Film-Tweets</td>
  <td>
Das Dataset ist eine erweiterte Version des Films Tweetings Datasets. Das Dataset enthält 170K Bewertungen für Filme, extrahiert aus gut strukturierten Tweets auf Twitter. Jede Instanz stellt einen Tweet dar und ist ein Tupel: Benutzer-ID, IMDB Film-ID, Bewertung, Timestamp, Anzahl von Favoriten für diesen Tweet und Anzahl der Retweets dieses Tweets. Das Dataset wurde von A. Said, S. Dooms, B. Loni und D. Tikk für Recommender Systems Challenge 2014 zur Verfügung gestellt.
  </td>
</tr>

<tr>
  <td>Verbrauchsdaten für verschiedene Fahrzeuge</td>
  <td>
Dieses DataSet ist eine leicht abgewandelte Version des DataSets, das von der StatLib-Bibliothek der Carnegie Mellon University bereitgestellt wurde. Das Dataset wurde 1983 in der Ausführung der American Statistical Association verwendet.
<p></p>
Die Daten geben den Kraftstoffverbrauch für verschiedene Fahrzeuge in Meilen pro Gallone an. Sie umfassen außerdem Informationen wie Anzahl der Zylinder, Hubraum, Leistung, Gesamtgewicht und Beschleunigung.
<p></p>
<b>Verwendung:</b> Vorhersage der Kraftstoffeinsparung auf Basis von drei mehrwertigen Attributen und fünf kontinuierlichen Attributen. 
<p></p>
<b>Zugrunde liegende Untersuchungen:</b> StatLib, Carnegie Mellon University, (1993). UCI Machine Learning Repository <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>. Irvine, CA: University of California, School of Information and Computer Science </td>
</tr>

<tr>
  <td>Dataset für binäre Klassifizierung der Diabetes bei Pima-Indianern</td>
  <td>
Eine Teilmenge der Daten der Datenbank des National Institute of Diabetes and Digestive and Kidney Diseases. Das Dataset wurde gefiltert, um sich auf weibliche Patienten zu konzentrieren, die von den Pima-Indianern abstammen. Die Daten umfassen medizinische Daten wie Zucker- und Insulinspiegel sowie Lebensstilfaktoren.
<p></p>
<b>Verwendung:</b> Vorhersage einer Diabeteserkrankung für den Patienten (binäre Klassifikation). 
<p></p>
<b>Zugrunde liegende Untersuchungen:</b> Sigillito, V. (1990). UCI Machine Learning Repository <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml"</a>. Irvine, CA: University of California, School of Information and Computer Science </td>
</tr>

<tr>
  <td>Daten zu Restaurantkunden</td>
  <td>
Eine Sammlung von Metadaten zu Kunden, einschließlich Demografie und Präferenzen.
<p></p>
<b>Verwendung:</b> Verwenden Sie dieses Dataset in Kombination mit den anderen beiden Restaurantdatasets, um ein Empfehlungssystem zu trainieren und zu testen. 
<p></p>
<b>Zugrunde liegende Untersuchungen:</b> Bache, K. und Lichman, M. (2013). UCI Machine Learning Repository <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>. Irvine, CA: University of California, School of Information and Computer Science
  </td>
</tr>

<tr>
  <td>Daten zu Restaurantmerkmalen</td>
  <td>
Eine Sammlung von Metadaten zu Restaurants und ihren Merkmalen wie Speisetyp, Stil und Lage.
<p></p>
<b>Verwendung:</b> Verwenden Sie dieses Dataset in Kombination mit den anderen beiden Restaurantdatasets, um ein Empfehlungssystem zu trainieren und zu testen. 
<p></p>
<b>Zugrunde liegende Untersuchungen:</b> Bache, K. und Lichman, M. (2013). UCI Machine Learning Repository <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>. Irvine, CA: University of California, School of Information and Computer Science
  </td>
</tr>

<tr>
  <td>Restaurantbewertungen</td>
  <td>
Enthält von Benutzern abgegebene Bewertungen für Restaurants auf einer Skala von 0 bis 2.
<p></p>
<b>Verwendung:</b> Verwenden Sie dieses Dataset in Kombination mit den anderen beiden Restaurantdatasets, um ein Empfehlungssystem zu trainieren und zu testen. 
<p></p>
<b>Zugrunde liegende Untersuchungen:</b> Bache, K. und Lichman, M. (2013). UCI Machine Learning Repository <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>. Irvine, CA: University of California, School of Information and Computer Science
  </td>
</tr>

<tr>
  <td>Multiklassen-Dataset zur Stahlhärtung</td>
  <td>
Dieses Dataset enthält eine Reihe von Datensätzen aus Stahlvergütungsversuchen. Es enthält die physischen Attribute (Breite, Dicke, Typ (Coil, Blech usw.)) der resultierenden Stahltypen.
<p></p>
<b>Verwendung:</b> Vorhersage eines von zwei numerischen Klassenattributen: Härte oder Belastbarkeit. Sie können auch Korrelationen zwischen den Attributen analysieren.
<p></p>
Die Stahlsorten folgen einem festgelegten Standard, der von SAE und anderen Organisationen definiert wurde. Sie suchen nach einer bestimmten „Sorte“ (die Klassenvariable) und möchten die erforderlichen Werte verstehen. 
<p></p>
<b>Zugrunde liegende Untersuchungen:</b> Sterling, D. und Buntine, W. (NA). UCI Machine Learning Repository <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>. Irvine, CA: University of California, School of Information and Computer Science
<p></p>
Ein nützliches Handbuch zu den Stahlsorten finden Sie hier: <a href="https://www.steamforum.com/pictures/Outokumpu-steel-grades-properties-global-standards.pdf">https://www.steamforum.com/pictures/Outokumpu-steel-grades-properties-global-standards.pdf</a>
  </td>
</tr>

<tr>
  <td>Teleskopdaten</td>
  <td>
Aufzeichnung von hochenergetischen Gammapartikelausbrüchen zusammen mit Hintergrundrauschen, die beide mithilfe eines Monte Carlo-Prozesses simuliert wurden.
<p></p>
Die Absicht der Simulation war, die Genauigkeit von erdgebundenen atmosphärischen Tscherenkow-Gammateleskopen zu verbessern. Dies erfolgt über statistische Methoden, um zwischen gewünschtem Signal (Tscherenkow-Strahlungsschauer) und Hintergrundrauschen (hadronische Schauer, die durch kosmische Strahlung in der oberen Atmosphäre ausgelöst werden) zu unterscheiden.
<p></p>
Die Daten wurden vorverarbeitet, um ein gestrecktes Bündel zu erstellen, bei dem die Längsachse zur Kameramitte ausgerichtet ist. Die Merkmale dieser Ellipse (die häufig als Hillas-Parameter bezeichnet werden) gehören zu den Bildparametern, die zur Unterscheidung verwendet werden können.
<p></p>
<b>Verwendung:</b> Vorhersage, ob das Bild eines Schauers ein Signal oder Hintergrundrauschen darstellt.
<p></p>
<b>Hinweise:</b> Eine einfache Klassifizierungsgenauigkeit ist für diese Daten nicht aussagekräftig, da die Klassifizierung eines Hintergrundereignisses als Signal schlechter ist als die Klassifizierung eines Signals als Hintergrundrauschen. Für den Vergleich der verschiedenen Klassifizierer soll das ROC-Diagramm verwendet werden. Die Wahrscheinlichkeit der Akzeptierung eines Hintergrundereignisses als Signal muss unter einem der folgenden Schwellenwerte liegen: 0,01, 0,02, 0,05, 0,1 oder 0,2.
<p></p>
Beachten Sie außerdem, dass die Anzahl der Hintergrundereignisse (h für hadronische Schauer) zu niedrig angesetzt ist. In realen Messungen stellt die h- oder Rauschen-Klasse die Mehrzahl der Ereignisse dar. 
<p></p>
<b>Zugrunde liegende Untersuchungen:</b> Bock, R.K. (1995). UCI Machine Learning Repository <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>. Irvine, CA: University of California, School of Information </td>
</tr>

<tr>
  <td>Wetter-Dataset</td>
  <td>
Stündliche flächenbasierte Wetterbeobachtungen aus NOAA (<a href="https://az754797.vo.msecnd.net/data/WeatherDataset.csv">zusammengeführte Daten von 201304 bis 201310</a>).
<p></p>
Die Wetterdaten umfassen Beobachtungen der Wetterstationen von Flughäfen für den Zeitraum April bis Oktober 2013. Das Dataset wurde vor dem Hochladen in Azure Machine Learning Studio (klassisch) wie folgt verarbeitet:
<ul>
  <li>Die IDs der Wetterstationen wurden den entsprechenden Flughafen-IDs zugeordnet.</li>
  <li>Wetterstationen, die nicht zu den 70 verkehrsreichsten Flughäfen gehören, wurden herausgefiltert</li>
  <li>Die Datumsspalte wurde in separate Jahres-, Monats- und Tagesspalten aufgeteilt.</li>
  <li>Die folgenden Spalten wurden ausgewählt: AirportID, Year, Month, Day, Time, TimeZone, SkyCondition, Visibility, WeatherType, DryBulbFarenheit, DryBulbCelsius, WetBulbFarenheit, WetBulbCelsius, DewPointFarenheit, DewPointCelsius, RelativeHumidity, WindSpeed, WindDirection, ValueForWindCharacter, StationPressure, PressureTendency, PressureChange, SeaLevelPressure, RecordType, HourlyPrecip, Altimeter</li>
</ul>
  </td>
</tr>

<tr>
  <td>Wikipedia SP 500 Dataset</td>
  <td>
Die Daten stammen aus Wikipedia (<a href="https://www.wikipedia.org/">https://www.wikipedia.org/</a>) und basieren auf Artikeln der einzelnen S&P-500-Unternehmen, die als XML-Daten gespeichert wurden.
<p></p>
Das Dataset wurde vor dem Hochladen in Azure Machine Learning Studio (klassisch) wie folgt verarbeitet:
<ul>
  <li>Extrahieren der Textinhalte für die einzelnen Unternehmen</li>
  <li>Entfernen der Wiki-Formatierung</li>
  <li>Entfernen aller nicht alphanumerischen Zeichen</li>
  <li>Konvertieren sämtlicher Texte in Kleinbuchstaben</li>
  <li>Bekannte Firmenkategorien wurden hinzugefügt.</li>
</ul>
<p></p>
Beachten Sie, dass für einige Unternehmen keine Artikel gefunden werden konnten, daher ist die Anzahl der Datensätze kleiner als 500.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/direct_marketing.csv">direct_marketing.csv</a></td>
  <td>
Das Dataset enthält Kundendaten und Angaben über ihre Reaktion auf eine direkte Mailing-Kampagne. Jede Zeile stellt einen Kunden dar. Das Dataset enthält neun Merkmale über demografische Benutzerdaten und Verhalten in der Vergangenheit und drei Beschriftungsspalten („visit“, „conversion“ und „spend“).  „visit“ ist eine Binärspalte, die angibt, dass ein Kunde nach der Marketingkampagne die Seite besucht hat. „conversion“ kennzeichnet, dass ein Kunde etwas gekauft hat. „spend“ ist der Betrag, der ausgegeben wurde.  Das Dataset wurde von Kevin Hillstrom für MineThatData E-Mail Analytics And Data Mining Challenge zur Verfügung gestellt.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/lyrl2004_tokens_test.csv">lyrl2004_tokens_test.csv</a></td>
  <td>
Merkmale der Beispiele im Reuters-Nachrichtendataset RCV1 V2. Das Dataset besteht aus 781.000 Nachrichtenbeiträgen sowie deren IDs (erste Spalte des Datasets). Alle Artikel sind mit Token und Stoppwort versehen und bezeichnet. Das DataSet wurde von D: Lewis zur Verfügung gestellt.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/lyrl2004_tokens_train.csv">lyrl2004_tokens_train.csv</a></td>
  <td>
Merkmale von Schulungsbeispielen im Reuters-Nachrichtendataset RCV1 V2. Das Dataset besteht aus 23.000 Nachrichtenbeiträgen sowie deren IDs (erste Spalte des Datasets). Alle Artikel sind mit Token und Stoppwort versehen und bezeichnet. Das DataSet wurde von D: Lewis zur Verfügung gestellt.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/network_intrusion_detection.csv">network_intrusion_detection.csv</a><br></td>
  <td>
Dataset von KDD Cup 1999 Knowledge Discovery and Data Mining Tools Competition (<a href="https://kdd.ics.uci.edu/databases/kddcup99/kddcup99.html">kddcup99.html</a>).
<p></p>
Das Dataset wurde heruntergeladen und in Azure Blob Storage abgelegt (<a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/network_intrusion_detection.csv">network_intrusion_detection.csv</a>). Es enthält Trainings- und Testdatasets. Das Trainingsdataset enthält ca. 126.000 Zeilen und 43 Spalten, inklusive der Bezeichnungen. Drei Spalten sind Teil der Bezeichnungsinformationen, und 40 Spalten, die aus numerischen und Zeichenfolgen-/Kategoriefeatures bestehen, sind zum Trainieren des Modells verfügbar. Die Testdaten enthalten ca. 22.500 Testbeispiele, die die gleichen 43 Spalten wie die Schulungsdaten enthalten.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/rcv1-v2.topics.qrels.csv">rcv1-v2.topics.qrels.csv</a></td>
  <td>
Themenzuweisungen für Nachrichtenartikel im Reuters-Nachrichtendataset RCV1-V2 Ein Nachrichtenartikel kann verschiedenen Themen zugewiesen werden. Das Format jeder Zeile lautet „&lt;Themenname&gt; &lt;Dokument-ID&gt; 1“. Das Dataset enthält 2,6 Mio. Themenzuweisungen. Das DataSet wurde von D: Lewis zur Verfügung gestellt.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/student_performance.txt">student_performance.txt</a></td>
  <td>
Diese Daten stammen aus dem KDD Cup 2010 zur Leistungsbewertung von Schülern (<a href="https://www.kdd.org/kdd-cup/view/kdd-cup-2010-student-performance-evaluation">Schülerleistungsbewertung</a>). Die verwendeten Daten stammen aus dem Trainingssatz „Algebra_2008_2009“ (Stamper, J., Niculescu-Mizil, A., Ritter, S., Gordon, G.J., & Koedinger, K.R. (2010). Algebra I 2008-2009. Dataset des KDD Cup 2010 – Educational Data Mining Challenge. Sie finden das Dataset unter <a href="https://pslcdatashop.web.cmu.edu/KDDCup/downloads.jsp">downloads.jsp</a>.
<p></p>
Das Dataset wurde in Azure Blob Storage heruntergeladen und gespeichert (<a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/student_performance.txt">student_performance.txt</a>). Es enthält Protokolldateien aus einem Tutorensystem für Schüler. Die bereitgestellten Funktionen umfassen eine Problem-ID und eine zugehörige Kurzbeschreibung, die Schüler-ID, einen Zeitstempel und die Anzahl der Versuche, bevor das Problem vom Schüler ordnungsgemäß gelöst wurde. Das ursprüngliche Dataset umfasst 8,9 Millionen Datensätze. Dieses Dataset wurde auf die ersten 100.000 Zeilen reduziert. Das DataSet umfasst 23 durch Tabulatoren getrennte Spalten mit verschiedenen Typen: numerisch, kategorisch und Zeitstempel.
  </td>
</tr>

</table>

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Schnellstart Ihrer Experimente anhand von Beispielen](sample-experiments.md)

<!-- Module References -->
[import-data]: /azure/machine-learning/studio-module-reference/import-data