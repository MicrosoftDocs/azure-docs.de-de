---
title: Kenntnis der Geschäftsanforderungen im Team Data Science-Prozess
description: Die Ziele, Aufgaben und Projektleistungen für die Phase der geschäftlichen Aspekte Ihrer Data Science-Projekte im Team Data Science-Prozess.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/04/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 35d03a52125bd2646f86b96bcffe123d9fab7f64
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60303552"
---
# <a name="the-business-understanding-stage-of-the-team-data-science-process-lifecycle"></a>Die Phase der geschäftlichen Aspekte des Lebenszyklus des Team Data Science-Prozesses.

In diesem Artikel werden die Ziele, Aufgaben und Projektleistungen im Zusammenhang mit der Phase der geschäftlichen Aspekte des Team Data Science-Prozesses (TDSP) behandelt. Dieser Prozess bietet einen empfohlenen Lebenszyklus, mit dem Sie Ihre Data Science-Projekte strukturieren können. Der Lebenszyklus beschreibt die wichtigsten Phasen, die Projekte typischerweise, oft iterativ, durchlaufen:

   1. **Geschäftliche Aspekte**
   2. **Datenerfassung und -auswertung**
   3. **Modellierung**
   4. **Bereitstellung**
   5. **Kundenakzeptanz**

Dies ist eine visuelle Darstellung des TDSP-Lebenszyklus: 

![TDSP-Lebenszyklus](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Ziele
* Angeben der Hauptvariablen, die als Modellziele dienen sollen und deren verwandte Metriken verwendet werden, um den Erfolg des Projekts zu bestimmen
* Identifizieren der relevanten Datenquellen, auf die das Unternehmen Zugriff hat oder die benötigt werden

## <a name="how-to-do-it"></a>Vorgehensweise
In dieser Phase werden zwei Hauptaufgaben durchgeführt: 

   * **Definieren von Zielen**: Ermitteln Sie zusammen mit Ihrem Kunden und anderen Projektbeteiligten die geschäftlichen Probleme, um sich damit vertraut zu machen. Formulieren Sie Fragen, mit denen die geschäftlichen Ziele definiert werden und die mit Data Science-Verfahren gelöst werden können.
   * **Identifizieren von Datenquellen**: Suchen Sie nach den relevanten Daten, mit denen Sie die Fragen beantworten können, die den Zielen des Projekts zugrunde liegen.

### <a name="define-objectives"></a>Definieren von Zielen
1. Ein zentrales Ziel dieses Schritts ist die Identifizierung der wichtigsten geschäftlichen Variablen, die von der Analyse vorhergesagt werden müssen. Diese Variablen werden als *Modellziele* bezeichnet und zusammen mit den zugehörigen Metriken verwendet, um den Erfolg des Projekts zu bestimmen. Zwei Beispiele für diese Ziele sind Umsatzprognosen oder die Wahrscheinlichkeit, dass eine Bestellung betrügerisch ist.

2. Definieren Sie die Projektziele, indem Sie präzise Fragen, die relevant, spezifisch und eindeutig sind, stellen und verfeinern. Data Science ist ein Prozess, bei dem Namen und Zahlen verwendet werden, um Antworten auf solche Fragen zu finden. Data Science oder Machine Learning wird normalerweise verwendet, um fünf Arten von Fragen zu beantworten:
 
   * Wie viel bzw. wie viele? (Regression)
   * Welche Kategorie? (Klassifizierung)
   * Welche Gruppe? (Clustering)
   * Ist dies merkwürdig? (Anomalieerkennung)
   * Welche Option sollte gewählt werden? (Empfehlung)

   Legen Sie fest, welche dieser Fragen gestellt wird und wie durch ihre Beantwortung Ihre Geschäftsziele erreicht werden.

3. Definieren Sie das Projektteam, indem Sie die Rollen und Zuständigkeiten der Mitglieder angeben. Entwickeln Sie einen groben Plan mit Meilensteinen, den Sie abarbeiten, während weitere Informationen ermittelt werden. 

4. Definieren Sie die Erfolgsmetriken. Sie könnten beispielsweise eine Vorhersage von Kundenabwanderungen durchführen. Sie benötigen eine Genauigkeitsrate von „x“ Prozent am Ende dieses Projekts mit einer Laufzeit von drei Monaten. Mit diesen Daten können Sie Kunden Angebote unterbreiten, um die Abwanderungen zu reduzieren. Die Metriken sollten **SMART** sein: 

   * **S**pecific (Spezifisch) 
   * **M**easurable (Messbar)
   * **A**chievable (Realistisch) 
   * **R**elevant (Relevant) 
   * **T**ime-bound (Zeitbezogen) 

### <a name="identify-data-sources"></a>Identifizieren von Datenquellen
Identifizieren Sie Datenquellen, die bekannte Beispiele für Antworten auf Ihre präzisen Fragen enthalten. Suchen Sie nach den folgenden Daten:

* Daten, die für die Frage relevant sind. Verfügen Sie über Kennzahlen für das Ziel und über Features, die sich auf das Ziel beziehen?
* Dies sind Daten, bei denen es sich um genaue Kennzahlen unseres Modellziels und die passenden Features handelt.

Sie könnten z.B. feststellen, dass vorhandene Systeme zusätzliche Arten von Daten sammeln und protokollieren müssen, um das Problem anzugehen und die Projektziele zu erreichen. In diesem Fall kann es ratsam sein, nach externen Datenquellen zu suchen oder die Systeme zu aktualisieren, um neue Daten zu erfassen.

## <a name="artifacts"></a>Artefakte
Hier sind die Ergebnisse dieser Phase angegeben:

   * [Charta-Dokument](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Project/Charter.md): In der Strukturdefinition des TDSP-Projekts ist eine Standardvorlage enthalten. Das Charta-Dokument ist ein dynamisches Dokument. Sie passen die Vorlage im Verlauf des Projekts an, wenn Sie neue Erkenntnisse gewinnen oder sich die Geschäftsanforderungen ändern. Der Schlüssel ist, dass dieses Dokument immer wieder durchlaufen wird und während des Ermittlungsprozesses weitere Details hinzugefügt werden. Beziehen Sie den Kunden und andere Projektbeteiligte in das Vornehmen der Änderungen ein, und informieren Sie sie eindeutig über die Gründe für die Änderungen.  
   * [Datenquellen](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Data_Report/Data%20Defintion.md#raw-data-sources): Dies ist der Abschnitt **Raw Data Sources** (Rohdatenquellen) des Berichts **Datendefinitionen** im Ordner **Data Report** (Datenbericht) des TDSP-Projekts. Er enthält die Datenquellen. In diesem Abschnitt werden der Quell- und Zielspeicherort für die Rohdaten angegeben. In späteren Phasen fügen Sie weitere Details ein, z.B. Skripts für das Verschieben der Daten in die Analyseumgebung.  
   * [Data dictionaries](https://github.com/Azure/Azure-TDSP-ProjectTemplate/tree/master/Docs/Data_Dictionaries) (Datenwörterbücher): Dieses Dokument enthält Beschreibungen der Daten, die durch den Kunden bereitgestellt werden. Diese Beschreibungen enthalten Informationen zum Schema (Datentypen und Informationen zu Validierungsregeln, sofern vorhanden) und zu den Diagrammen zu den Entitätsbeziehungen, sofern vorhanden.

## <a name="next-steps"></a>Nächste Schritte

Hier finden Sie Links zu jedem Schritt im Lebenszyklus des TDSP:

   1. [Geschäftliche Aspekte](lifecycle-business-understanding.md)
   2. [Datenerfassung und -auswertung](lifecycle-data.md)
   3. [Modellierung](lifecycle-modeling.md)
   4. [Bereitstellung](lifecycle-deployment.md)
   5. [Kundenakzeptanz](lifecycle-acceptance.md)

Vollständige exemplarische Vorgehensweisen, in denen sämtliche Schritte im Prozess für bestimmte Szenarien gezeigt werden, sind ebenfalls verfügbar. Der Artikel mit [exemplarischen Vorgehensweisen](walkthroughs.md) enthält eine Liste der Szenarien mit Links und Kurzbeschreibungen. Die exemplarischen Vorgehensweisen zeigen auch, wie Cloud- und lokale Tools sowie Dienste in einem Workflow oder einer Pipeline zum Erstellen einer intelligenten Anwendung kombiniert werden. 
