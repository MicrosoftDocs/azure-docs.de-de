---
title: Analysieren der Kundenabwanderung
titleSuffix: Azure Machine Learning Studio
description: Fallstudie zur Entwicklung eines integrierten Modells für die Analyse und Bewertung der Kundenbindung unter Verwendung von Azure Machine Learning Studio.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 12/18/2017
ms.openlocfilehash: e6a7eaa94e7196c830a66b2d77023bd562119c92
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64699440"
---
# <a name="analyze-customer-churn-using-azure-machine-learning-studio"></a>Analysieren der Kundenabwanderung mithilfe von Azure Machine Learning Studio
## <a name="overview"></a>Übersicht
Dieser Artikel beschreibt eine Referenzimplementierung für ein Projekt zur Analyse der Kundenabwanderung, das in Azure Machine Learning Studio erstellt wird. In diesem Artikel wird das Zuordnen generischer Modelle für eine ganzheitliche Lösung des Kundenabwanderungsproblems in der Industrie erläutert. Wir messen auch die Genauigkeit von Modellen, die mit Machine Learning erstellt werden, und wir bewerten mögliche Richtungen der weiteren Entwicklung.  

### <a name="acknowledgements"></a>Danksagung
Dieses Experiment wurde von Serge Berger, Principal Data Scientist bei Microsoft, und Roger Barga, zuvor Produktmanager für Microsoft Azure Machine Learning Studio, entwickelt und getestet. Der Azure-Dokumentationsteam bedankt sich herzlich dafür, dass beide ihre Erkenntnisse in diesem Whitepaper präsentieren.

> [!NOTE]
> Die für dieses Experiment genutzten Daten sind nicht öffentlich zugänglich. Ein Beispiel zum Erstellen eines Machine Learning-Modells für die Kundenabwanderungsanalyse finden Sie hier: [Vorlage für ein Abwanderungsmodell im Einzelhandel](https://gallery.azure.ai/Collection/Retail-Customer-Churn-Prediction-Template-1) im [Azure KI-Katalog](https://gallery.azure.ai/)
> 
> 



## <a name="the-problem-of-customer-churn"></a>Das Problem der Kundenabwanderung
Firmen müssen sich sowohl auf dem Verbrauchermarkt als auch in allen Unternehmenssektoren mit der Kundenabwanderung auseinandersetzen. Gelegentlich kann es zur übermäßigen Abwanderung kommen, die dann Grundsatzentscheidungen beeinflusst. Die traditionelle Lösung besteht darin, eine starke Abwanderungsbereitschaft von Kunden vorherzusagen und sich über einen Concierge-Dienst, Marketingkampagnen oder Sonderregelungen mit ihren Bedürfnissen auseinanderzusetzen. Die jeweilige Vorgehensweise variiert je nach Branche. Auch innerhalb einer einzigen Branche (z.B. Telekommunikation) kann das Vorgehen je nach Verbrauchergruppe variieren.

Die Gemeinsamkeit besteht darin, dass die Unternehmen diese besonderen Bemühungen zur Kundenbindung minimieren müssen. Eine natürliche Methode wäre somit, jeden Kunden mit einer Abwanderungswahrscheinlichkeit zu bewerten und sich mit einer bestimmten Anzahl (N) der Kunden mit den höchsten Werten zu befassen. Die Kunden mit den höchsten Werten sind wahrscheinlich die lukrativsten. In komplexeren Szenarien wird bei der Auswahl der Kandidaten für Sonderregelungen eine Gewinnfunktion verwendet. Diese Überlegungen sind jedoch nur ein Teil der Gesamtstrategie zur Bekämpfung der Kundenabwanderung. Die Unternehmen müssen auch das Risiko (und die zugehörige Risikobereitschaft), das Interventionsniveau und die zugehörigen Kosten sowie die eingängige Kundensegmentierung berücksichtigen.  

## <a name="industry-outlook-and-approaches"></a>Branchenprognose und Ansätze
Ein durchdachter Umgang mit der Abwanderung ist ein Zeichen für eine gefestigte Branche. Das klassische Beispiel ist die Telekommunikationsbranche, in der Kunden häufig den Anbieter wechseln. Diese spontane Abwanderung ist ein wesentliches Problem. Darüber hinaus haben die Anbieter wichtige Erfahrungen zu *Abwanderungsargumenten*gesammelt, die für die Kunden die Faktoren für einen Wechsel darstellen.

In der Mobilfunkbranche stellt z. B. die Auswahl an Mobiltelefonen oder Geräten ein bekanntes Argument für eine Abwanderung dar. Daher besteht eine beliebte Strategie darin, den Preis für ein Mobiltelefon für Neukunden zu subventionieren und Bestandskunden bei einem Upgrade den vollen Preis zu berechnen. Diese Strategie hat in der Vergangenheit dazu geführt, dass die Kunden von einem Anbieter zum nächsten wechseln, um erneut einen Rabatt zu erhalten. Dies hat die Anbieter wiederum dazu veranlasst, ihre Strategien zu überarbeiten.

Die hohe Volatilität bei Mobiltelefonangeboten ist ein Faktor, durch den Abwanderungsmodelle, die auf aktuellen Mobiltelefonmodellen basieren, schnell unbrauchbar werden. Darüber hinaus sind Mobiltelefone nicht nur Kommunikationsgeräte, sondern ein modisches Statement (denken Sie an das iPhone). Diese sozialen Einflüsse befinden sich außerhalb des Bereichs normaler Datasets für die Telekommunikation.

Das Endergebnis hinsichtlich der Modellerstellung ist, dass Sie keinen vernünftigen Grundsatz formulieren können, indem Sie einfach bekannte Abwanderungsgründe beseitigen. Tatsächlich ist eine durchgängige Modellierungsstrategie **obligatorisch**, einschließlich der klassischen Modelle zur Quantifizierung bestimmter Variablen (z. B. Entscheidungsbäume).

Die Unternehmen führen großangelegte Datenanalysen, insbesondere zur Abwanderungserkennung auf Basis großer Datenmengen, mit umfangreichen Datenbeständen zu ihren Kunden als effektiven Lösungsansatz für das Problem durch. Weitere Informationen zum Ansatz mit der Analyse großer Datenmengen für das Abwanderungsproblem finden Sie im Abschnitt mit den Empfehlungen zu ETL.  

## <a name="methodology-to-model-customer-churn"></a>Methode zum Abbilden der Kundenabwanderung
Ein allgemeiner Problemlösungsprozess für die Kundenabwanderung ist in den Abbildungen 1 bis 3 dargestellt:  

1. Mit einem Risikomodell können Sie berücksichtigen, wie sich Aktionen auf die Wahrscheinlichkeit und das Risiko auswirken.
2. Mit einem Interventionsmodell können Sie berücksichtigen, wie sich das Interventionsniveau auf die Abwanderungswahrscheinlichkeit und den Langzeitwert von Kundenbeziehungen (Customer Lifetime Value, CLV) auswirken kann.
3. Diese Analyse bietet sich für eine qualitative Analyse an, die in eine auf Kundensegmente ausgerichtete proaktive Marketingkampagne ausgeweitet wird, um das optimale Angebot bereitzustellen.  

![Diagramm, das zeigt, wie sich anhand von Risikotoleranz und Entscheidungsmodellen verwertbare Erkenntnisse gewinnen lassen](./media/azure-ml-customer-churn-scenario/churn-1.png)

Dieser zukunftsorientierte Ansatz stellt die beste Möglichkeit zur Behandlung von Abwanderung dar, bringt jedoch Komplexität mit sich: Wir müssen einen Multimodell-Prototyp entwickeln und Abhängigkeiten zwischen den Modellen nachverfolgen. Die Interaktion zwischen den Modellen kann gekapselt werden, wie im folgenden Diagramm veranschaulicht:  

![Diagramm zur Interaktion zwischen Abwanderungsmodellen](./media/azure-ml-customer-churn-scenario/churn-2.png)

*Abbildung 4: Vereinheitlichter Prototyp mit mehreren Modellen*  

Interaktionen zwischen den verschiedenen Modellen sind entscheidend, wenn wir einen ganzheitlichen Ansatz für die Kundenbindung bereitstellen müssen. Jedes Modell lässt im Laufe der Zeit zwangsläufig nach. Daher besteht die Architektur aus einer impliziten Schleife (ähnlich dem Prototyp, der vom CRISP-DM Data Mining-Standard, [***3***], festgelegt wurde).  

Der Gesamtzyklus von Risiko-Entscheidung-Marketingsegmentierung/-spaltung entspricht weiterhin einer verallgemeinerten Struktur, die auf viele Geschäftsprobleme angewendet werden kann. Die Abwanderungsanalyse ist einfach ein starker Vertreter dieser Gruppe von Problemen, da sie alle Merkmale eines komplexen Geschäftsproblems aufweist, das keine vereinfachte vorhersehbare Lösung gestattet. Die gesellschaftlichen Aspekte des modernen Ansatzes für die Abwanderung werden im Ansatz nicht ausdrücklich hervorgehoben, aber die gesellschaftlichen Aspekte sind wie bei allen Modellen auch im Prototyp für die Modellerstellung gekapselt.  

Eine interessante Ergänzung ist hier die Analyse großer Datenmengen. Die heutigen Telekommunikations- und Einzelhandelsunternehmen erfassen flächendeckend Daten zu ihren Kunden, und es ist leicht vorherzusehen, dass sich der Bedarf an Verbindungen zwischen den verschiedenen Modellen zu einem allgemeinen Trend entwickelt, wenn man aufkommende Trends wie das "Internet der Dinge" und allgegenwärtige Geräte bedenkt, die es Unternehmen gestatten, intelligente Lösungen auf verschiedenen Ebenen einzusetzen.  

 

## <a name="implementing-the-modeling-archetype-in-machine-learning-studio"></a>Implementieren des Modellierungs-Prototyps in Machine Learning Studio
Wie kann für das beschriebene Problem am besten ein integrierter Ansatz für die Modellierung und Bewertung implementiert werden? In diesem Abschnitt wird veranschaulicht, wie dies mithilfe von Azure Machine Learning Studio erreicht wird.  

Der Multi-Modell-Ansatz ist beim Entwerfen eines globalen Prototyps für die Abwanderung zwingend erforderlich. Selbst der (voraussagende) Bewertungsteil des Ansatzes sollte mehrere Modelle einbeziehen.  

Das folgende Diagramm zeigt den erstellten Prototyp, der vier Bewertungsalgorithmen in Machine Learning Studio einbezieht, um die Abwanderung vorherzusagen. Der Grund für die Verwendung eines Multi-Modell-Ansatzes besteht nicht nur darin, einen Gesamtklassifizierer zum Erhöhen der Genauigkeit zu erstellen, sondern er dient auch zum Schutz vor einer Überanpassung sowie zur Verbesserung der vorgeschriebenen Funktionsauswahl.  

![Screenshot eines komplexen Studio-Arbeitsbereichs mit vielen verbundenen Modulen](./media/azure-ml-customer-churn-scenario/churn-3.png)

*Abbildung 5: Prototyp eines Abwanderungsmodell-Ansatzes*  

Die folgenden Abschnitte enthalten Einzelheiten zum Bewertungsmodell des Prototyps, das wir mithilfe von Machine Learning Studio implementiert haben.  

### <a name="data-selection-and-preparation"></a>Datenauswahl und Vorbereitung
Die zum Erstellen der Modelle und zum Bewerten der Kunden verwendeten Daten wurden aus einer vertikalen CRM-Lösung abgerufen, wobei die Daten verschleiert wurden, um den Datenschutz der Kunden zu gewährleisten. Die Daten enthalten Informationen über 8.000 Abonnements in den USA, und es werden drei Quellen kombiniert: Bereitstellungsdaten (Abonnementmetadaten), Aktivitätsdaten (Nutzung des Systems) und Kundensupportdaten. Die Daten beinhalten keine geschäftlichen Daten der Kunden, so sind z. B. keine Treuemetadaten oder Bonitätsbewertungen enthalten.  

Der Einfachheit halber liegen die ETL- und Datenbereinigungsprozesse außerhalb des Anwendungsbereichs, da wir annehmen, dass die Vorbereitung der Daten bereits an anderer Stelle durchgeführt wurde.

Die Funktionsauswahl für die Modellerstellung basiert auf der Bewertung der vorläufigen Signifikanz für eine Reihe von in den Prozess einbezogenen Einflusswerten, wobei das Random Forest-Modul verwendet wird. Für die Implementierung in Machine Learning Studio haben wir den Mittelwert, den Medianwert und Bereiche für repräsentative Funktionen berechnet. Es wurden z. B. Mengen für die qualitativen Daten hinzugefügt, etwa Mindest- und Höchstwerte für die Benutzeraktivität.

Zudem wurden temporale Informationen für die letzten sechs Monate erfasst. Wir haben die Daten eines Jahres analysiert und dabei bewiesen, dass die Auswirkung auf die Abwanderung nach sechs Monaten erheblich abnimmt, auch wenn statistisch signifikante Trends vorhanden sind.  

Der wichtigste Punkt dabei ist, dass der gesamte Prozess, einschließlich ETL, Funktionsauswahl und Modellerstellung, mithilfe von Datenquellen in Microsoft Azure in Machine Learning Studio implementiert wurde.   

Die folgenden Diagramme veranschaulichen die verwendeten Daten.  

![Screenshot mit einem Beispiel zur Verwendung der Daten mit Rohwerten](./media/azure-ml-customer-churn-scenario/churn-4.png)

*Abbildung 6: Auszug aus der Datenquelle (verschleiert)*  

![Screenshot mit statistischen Funktionen, die aus der Datenquelle extrahiert wurden](./media/azure-ml-customer-churn-scenario/churn-5.png)

*Abbildung 7: Aus der Datenquelle extrahierte Funktionen*
 

> Beachten Sie, dass diese Daten privat sind, weshalb Modell und Daten nicht freigegeben werden dürfen.
> Sie finden jedoch ein ähnliches Modell mit öffentlich verfügbaren Daten in diesem Beispielexperiment im [Azure KI-Katalog](https://gallery.azure.ai/): [Kundenabwanderung in der Telekommunikationsbranche](https://gallery.azure.ai/Experiment/31c19425ee874f628c847f7e2d93e383).
> 
> Weitere Informationen zum Implementieren eines Abwanderungsanalysemodells mit Cortana Intelligence Suite finden Sie auch in [diesem Video](https://info.microsoft.com/Webinar-Harness-Predictive-Customer-Churn-Model.html) von Senior Program Manager Wee Hyong Tok. 
> 
> 

### <a name="algorithms-used-in-the-prototype"></a>Im Prototyp verwendete Algorithmen
Wir haben die folgenden vier Machine Learning-Algorithmen für die Erstellung des Prototyps verwendet (keine Anpassung):  

1. Logistische Regression (LR)
2. Boosted Decision Tree (BT, verstärkter Entscheidungsbaum)
3. Averaged Perceptron (AP, gemitteltes Perzeptron)
4. Support Vector Machine (SVM, Stützvektormethode)  

Das folgende Diagramm veranschaulicht einen Teil der Experimententwurfsoberfläche, die die Reihenfolge anzeigt, in der die Modelle erstellt wurden:  

![Screenshot mit einem kleinen Abschnitt des Studio-Experimentbereichs](./media/azure-ml-customer-churn-scenario/churn-6.png)  

*Abbildung 8: Erstellen von Modellen in Machine Learning Studio*  

### <a name="scoring-methods"></a>Bewertungsmethoden
Wir haben die vier Modelle mithilfe eines bezeichneten Trainings-DataSets bewertet.  

Wir haben das Bewertungs-DataSet auch mit der Desktop-Edition von SAS Enterprise Miner 12 an ein vergleichbares Modell übermittelt. Wir maßen die Genauigkeit des SAS-Modells und aller vier Machine Learning Studio-Modelle.  

## <a name="results"></a>Ergebnisse
In diesem Abschnitt werden unsere Ergebnisse zur Genauigkeit der Modelle auf Basis des Bewertungsdatasets präsentiert.  

### <a name="accuracy-and-precision-of-scoring"></a>Richtigkeit und Genauigkeit der Bewertung
Im Allgemeinen ist die Implementierung in Azure Machine Learning Studio etwas ungenauer als SAS, und zwar um etwa 10 – 15 % (Area Under Curve oder AUC).  

Die wichtigste Kennzahl in Codeänderung ist jedoch die Rate der falschen Klassifizierung: Welcher der vom Klassifizierer vorhergesagten wichtigsten N Abwanderer ist tatsächlich **nicht** abgewandert und hat trotzdem besondere Behandlung erhalten? Das folgende Diagramm vergleicht die Fehlklassifizierungsrate für alle Modelle:  

![AUC-Diagramm mit einem Vergleich der Leistung von vier Algorithmen](./media/azure-ml-customer-churn-scenario/churn-7.png)

*Abbildung 9: Area Under Curve (AUC, Fläche unter der Kurve) für den Passauer Prototyp*

### <a name="using-auc-to-compare-results"></a>Verwenden von AUC zum Vergleichen von Ergebnissen
„Area Under Curve“ (AUC) ist eine Metrik, die eine globale Maßeinheit für die *Trennbarkeit* zwischen den Verteilungen von Punktzahlen für positive und negative Bestände. Sie ähnelt dem traditionellen ROC-Diagramm (Receiver Operator Characteristic), aber ein wesentlicher Unterschied ist, dass die AUC-Kennzahl von Ihnen keinen Schwellenwert erfordert. Stattdessen fasst sie die Ergebnisse über **alle** möglichen Optionen zusammen. Im Gegensatz dazu zeigt das traditionelle ROC-Diagramm den positiven Wert an der vertikalen und den falsch positiven Wert an der horizontalen Achse an, wobei der Schwellenwert für die Klassifizierung variiert.   

AUC wird als Maßstab für den Wert verschiedener Algorithmen (oder verschiedener Systeme) verwendet, da die Kennzahl den Vergleich von Modellen anhand ihrer AUC-Werte ermöglicht. Dies ist ein beliebter Ansatz in Branchen wie der Meteorologie oder Biosciences. Somit stellt AUC ein beliebtes Werkzeug zur Bewertung der Leistung von Klassifizierern dar.  

### <a name="comparing-misclassification-rates"></a>Vergleichen der Fehlklassifikationsraten
Wir haben die Fehlklassifikationsraten für das fragliche Dataset mithilfe der CRM-Daten von ungefähr 8.000 Kunden verglichen.  

* Die SAS-Fehlklassifizierungsrate lag bei 10-15 %.
* Die Fehlklassifizierungsrate von Machine Learning Studio lag für die obersten 200-300 Abwanderer bei 15-20 %.  

In der Telekommunikationsbranche ist es wichtig, sich nur mit den Kunden mit dem höchsten Abwanderungsrisiko zu befassen, indem ihnen ein Concierge-Dienst oder andere Sonderbehandlungen angeboten werden. In dieser Hinsicht erzielt die Machine Learning Studio-Implementierung Ergebnisse, die mit dem SAS-Modell vergleichbar sind.  

Ebenso ist die Richtigkeit wichtiger als die Genauigkeit, da wir am stärksten an der korrekten Klassifizierung potenzieller Abwanderer interessiert sind.  

Das folgende Diagramm aus Wikipedia veranschaulicht die Beziehung in einer einfach zu verstehenden Grafik:  

![Zwei Ziele. Ein Ziel mit lose gruppierten Treffermarkierungen, die sich aber in der Nähe der Zielscheibenmitte befinden, markiert mit „Low accuracy: good trueness, poor precision“ (Geringe Treffergenauigkeit: gute Richtigkeit, schlechte Genauigkeit) Ein weiteres Ziel mit eng gruppierten Treffermarkierungen, die aber weit von der Zielscheibenmitte entfernt sind, markiert mit „Low accuracy: poor trueness, good precision (Geringe Treffergenauigkeit: schlechte Richtigkeit, gute Genauigkeit)](./media/azure-ml-customer-churn-scenario/churn-8.png)

*Abbildung 10: Kompromiss zwischen Richtigkeit und Genauigkeit*

### <a name="accuracy-and-precision-results-for-boosted-decision-tree-model"></a>Ergebnisse für Richtigkeit und Genauigkeit für das Boosted Tree-Modell
Das folgende Diagramm zeigt die Originalergebnisse der Bewertung mithilfe des Machine Learning-Prototyps für das Boosted Decision Tree-Modell, bei dem es sich um das genaueste der vier Modelle handelt:  

![Tabellenausschnitt mit den Werten „Accuracy“ (Treffergenauigkeit), „Precision“ (Genauigkeit), „Recall“ (Trefferquote), „F-Score“ (F-Maß), „AUC“ (Fläche unter der Kurve), „Average Log Loss“ (logarithmische Durchschnittsdämpfung) und „Training Log Loss“ (logarithmische Trainingsdämpfung) für vier Algorithmen](./media/azure-ml-customer-churn-scenario/churn-9.png)

*Abbildung 11: Merkmale des Boosted Decision Tree-Modells*

## <a name="performance-comparison"></a>Leistungsvergleich
Wir haben die Geschwindigkeit, mit der Daten mithilfe der Machine Learning Studio-Modelle bewertet wurden, mit einem vergleichbaren Modell verglichen, das mithilfe der Desktopversion von SAS Enterprise Miner 12.1 erstellt wurde.  

Die folgende Tabelle fasst die Leistung der Algorithmen zusammen:  

*Tabelle 1: Allgemeine Leistung (Genauigkeit) der Algorithmen*

| LR | BT | AP | SVM |
| --- | --- | --- | --- |
| Durchschnittliches Modell |Bestes Modell |Unterdurchschnittliches Modell |Durchschnittliches Modell |

Die in Machine Learning Studio gehosteten Modelle haben SAS hinsichtlich der Ausführungsgeschwindigkeit um 15-25 % übertroffen, aber die Richtigkeit war größtenteils vergleichbar.  

## <a name="discussion-and-recommendations"></a>Diskussion und Empfehlungen
In der Telekommunikationsbranche haben sich verschiedene Methoden zur Analyse der Abwanderung entwickelt, darunter:  

* Ableitung der Metriken für vier grundlegende Kategorien:
  * **Entität (z.B. ein Abonnement)** . Bereitstellung grundlegender Informationen zum Abonnement und/oder Kunden, das/der Gegenstand der Abwanderung ist.
  * **Aktivität**. Abrufen aller möglichen Nutzungsinformationen, die sich auf die Entität beziehen, z. B. die Anzahl der Anmeldungen.
  * **Kundensupport**. Sammeln von Informationen aus Kundensupportprotokollen, um anzuzeigen, ob es für das Abonnement Probleme oder Interaktionen mit dem Kundensupport gab.
  * **Wettbewerbs- und Geschäftsdaten**. Abrufen aller möglichen Informationen zum Kunden (können nicht verfügbar oder schwer nachzuverfolgen sein).
* Verwenden der Gewichtung zur Steuerung der Funktionsauswahl. Das bedeutet, dass das Boosted Decision Tree-Modell immer einen vielversprechenden Ansatz darstellt.  

Die Verwendung dieser vier Kategorien schafft die Illusion, dass ein einfacher *deterministischer* Ansatz basierend auf Indizes, die anhand von angemessenen Faktoren pro Kategorie gebildet werden, ausreichen sollte, um das Abwanderungsrisiko von Kunden zu erkennen. Obwohl dieser Gedanke plausibel scheint, beruht er leider auf einem falschen Verständnis. Der Grund hierfür ist, dass die Abwanderung ein temporaler Effekt ist, während die zur Abwanderung führenden Faktoren normalerweise schwankende Zustände aufweisen. Was bringt einen Kunden dazu, zu glauben, dass es einen Unterschied macht, ob er heute oder morgen oder in sechs Monaten abwandert? Daher ist ein *Wahrscheinlichkeitsmodell* erforderlich.  

Diese wichtige Beobachtung wird in Unternehmen häufig übersehen, die im Allgemeinen einen Business Intelligence-orientierten Ansatz für die Analyse bevorzugen (hauptsächlich, da er besser zu verkaufen ist und eine unkomplizierte Automatisierung gestattet).  

Die Aussicht der Self-Service-Analyse mithilfe von Machine Learning Studio ist jedoch, dass die vier Informationskategorien, die nach Geschäftsbereich oder Abteilung bewertet werden, für das maschinelle Lernen hinsichtlich der Abwanderung zu einer wichtigen Quelle werden.  

Eine weitere interessante Funktion von Azure Machine Learning Studio ist die Möglichkeit, dem Repository der bereits verfügbaren vordefinierten Module ein benutzerdefiniertes Modul hinzuzufügen. Diese Funktion bietet im Wesentlichen eine Möglichkeit, Bibliotheken auszuwählen und Vorlagen für vertikale Märkte zu erstellen. Sie ist am Markt ein wichtiges Unterscheidungsmerkmal von Azure Machine Learning Studio.  

Wir hoffen, dieses Thema weiter behandeln zu können, insbesondere in Bezug auf die Analyse großer Datenmengen.
  

## <a name="conclusion"></a>Zusammenfassung
In diesem Dokument wird ein sinnvoller Ansatz zur Bewältigung eines allgemeinen Problems, der Kundenabwanderung, mithilfe einer allgemeinen Struktur beschrieben. Wir haben einen Prototyp zur Bewertung von Modellen betrachtet und ihn mithilfe von Azure Machine Learning Studio implementiert. Abschließend haben wird die Genauigkeit und Leistung der Prototyplösung hinsichtlich vergleichbarer Algorithmen in SAS bewertet.  

 

## <a name="references"></a>Referenzen
[1] Predictive Analytics: Beyond the Predictions, W. McKnight, Information Management, Juli/August 2011, S. 18-20.  

[2] Wikipedia-Artikel: [Richtigkeit und Genauigkeit](https://en.wikipedia.org/wiki/Accuracy_and_precision)

[3] [CRISP-DM 1.0: Step-by-Step Data Mining Guide](https://www.the-modeling-agency.com/crisp-dm.pdf)   

[4] [Big Data Marketing: Engage Your Customers More Effectively and Drive Value](https://www.amazon.com/Big-Data-Marketing-Customers-Effectively/dp/1118733894/ref=sr_1_12?ie=UTF8&qid=1387541531&sr=8-12&keywords=customer+churn) (in englischer Sprache)

[5] Vorlage [Telco Churn Model](https://gallery.azure.ai/Experiment/Telco-Customer-Churn-5) im [Azure KI-Katalog](https://gallery.azure.ai/) 
 

## <a name="appendix"></a>Anhang
![Snapshot einer Präsentation zum Abwanderungsprototyp](./media/azure-ml-customer-churn-scenario/churn-10.png)

*Abbildung 12: Snapshot einer Präsentation zum Abwanderungs-Prototyp*
