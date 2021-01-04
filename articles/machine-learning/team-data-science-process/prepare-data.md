---
title: Vorbereiten von Daten für ML Studio (klassisch) – Team Data Science-Prozess
description: Vorverarbeiten und Bereinigen von Daten als Vorbereitung für die effektive Verwendung in Machine Learning.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: caedcf313ab809e9607907545f26ca1b62bbeca7
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96012406"
---
# <a name="tasks-to-prepare-data-for-enhanced-machine-learning"></a>Aufgaben zur Vorbereitung von Daten für erweitertes Machine Learning
Die Vorverarbeitung und Bereinigung von Daten sind wichtige Aufgaben, die durchgeführt werden müssen, bevor ein Dataset zum Trainieren von Modellen verwendet werden kann. Unformatierte Daten enthalten oft unnötige bzw. fehlende Werte und sind unzuverlässig. Die Verwendung dieser Daten für die Modellierung kann zu falschen Ergebnissen führen. Diese Aufgaben sind Teil des Team Data Science-Prozesses (TDSP). Normalerweise wird zuerst eine Untersuchung eines Datasets durchgeführt, um die erforderliche Vorverarbeitung zu ermitteln und zu planen. Eine ausführliche Anleitung zum TDSP-Prozess finden Sie in den Schritten, die unter [Team Data Science-Prozess](overview.md) beschrieben sind.

Aufgaben der Vorverarbeitung und Bereinigung, z. B. die Datenuntersuchung, können in vielen verschiedenen Umgebungen ausgeführt werden, wie etwa SQL oder Hive oder Azure Machine Learning Studio (klassisch), sowie mit unterschiedlichen Tools und Sprachen, z. B. R oder Python. Dies hängt davon ab, wo die Daten gespeichert sind und wie sie formatiert sind. Da der TDSP iterativ ist, können diese Aufgaben im Workflow des Prozesses in verschiedenen Schritten erfolgen.

In diesem Artikel werden verschiedene Konzepte und Aufgaben zur Datenverarbeitung beschrieben, die vor oder nach der Erfassung von Daten in Azure Machine Learning Studio (klassisch) durchgeführt werden können.

Ein Beispiel für die Datenuntersuchung und Vorverarbeitung in Azure Machine Learning Studio (Classic) finden Sie im [Video zur Vorverarbeitung von Daten](https://azure.microsoft.com/documentation/videos/preprocessing-data-in-azure-ml-studio/).

## <a name="why-pre-process-and-clean-data"></a>Warum müssen Daten vorverarbeitet und bereinigt werden?
Echte Daten stammen aus verschiedenen Quellen und Abläufen und können daher Unregelmäßigkeiten oder beschädigte Daten enthalten, die die Qualität des DataSets beeinträchtigen. Folgende Qualitätsprobleme treten bei Daten häufiger auf:

* **Unvollständige Informationen**: Den Daten fehlen Attribute oder Werte.
* **Überflüssige Informationen**: Die Daten enthalten fehlerhafte Datensätze oder Ausreißer.
* **Inkonsistente Informationen**: Die Daten enthalten widersprüchliche Datensätze oder Abweichungen.

Daten von hoher Qualität sind eine wichtige Voraussetzung für die Qualität von Vorhersagemodellen. Um schlechte Ergebnisse aufgrund schlechter Ausgangsdaten zu vermeiden und die Datenqualität und damit die Leistung des Modells zu verbessern, ist es unerlässlich, die Datenintegrität zu untersuchen, um Probleme mit den Daten frühzeitig zu erkennen und geeignete Schritte zur Vorverarbeitung und Bereinigung durchführen zu können.

## <a name="what-are-some-typical-data-health-screens-that-are-employed"></a>Welche Methoden zur Überprüfung der Datenintegrität werden am häufigsten eingesetzt?
Sie können die allgemeine Qualität der Daten überprüfen, indem Sie Folgendes prüfen:

* Die Anzahl der **Datensätze**.
* Die Anzahl der **Attribute** (oder **Features**).
* Die Attribut- **Datentypen** (nominal, ordinal oder fortlaufend).
* Die Anzahl der **fehlenden Werte**.
* **Wohlgeformte** Daten.
  * Wenn die Daten in TSV- oder CSV-Dateien gespeichert sind, sollten Sie prüfen, ob die Spalten- und Zeilentrennzeichen die Spalten und Zeilen auch immer richtig trennen.
  * Bei Daten im HTML- oder XML-Format überprüfen Sie, ob die Daten gemäß den jeweiligen Standards wohlgeformt sind.
  * Es ist möglicherweise eine Analyse erforderlich, um strukturierte Informationen aus teilweise strukturierten oder unstrukturierten Daten zu extrahieren.
* **Inkonsistente Datensätze**. Überprüfen Sie den zulässigen Wertebereich. Wenn die Daten z. B. die Notendurchschnitte von Schülern enthalten, sollte der Durchschnitt im festgelegten Bereich liegen, also z. B. 0 bis 4.

Wenn Sie Probleme für die Daten ermitteln, sind **Verarbeitungsschritte** erforderlich. Dazu gehören häufig die Bereinigung fehlender Werte, die Datennormalisierung, die Diskretisierung, die Textverarbeitung zum Entfernen und/oder Ersetzen eingebetteter Zeichen, die Einfluss auf die Datenausrichtung haben können, das Bereinigen gemischter Datentypen in gemeinsamen Feldern u. a.

**In Azure Machine Learning werden wohlgeformte Tabellendaten verarbeitet.**  Wenn die Daten bereits in tabellarischer Form vorliegen, kann die Datenvorverarbeitung direkt mit Azure Machine Learning Studio (klassisch) im Machine Learning ausgeführt werden.  Wenn Daten nicht in tabellarischer Form vorliegen, sondern z. B. als XML, ist eine Analyse erforderlich, um die Daten in eine tabellarische Form zu konvertieren.  

## <a name="what-are-some-of-the-major-tasks-in-data-pre-processing"></a>Welches sind die wichtigsten Vorgänge bei der Datenvorverarbeitung?
* **Datenbereinigung**:  Auffüllen fehlender Werte, Erkennen und Entfernen überflüssiger Daten und Ausreißer.
* **Datentransformation**:  Normalisierung der Daten, um Umfang und Störungen zu verringern.
* **Datenreduzierung**:  Erstellen von Stichproben aus den Datensätzen oder Attributen zur einfacheren Datenverarbeitung.
* **Datendiskretisierung**:  Konvertieren kontinuierlicher Attribute in kategorische Attribute zur einfacheren Verwendung in bestimmten Machine Learning-Methoden.
* **Textbereinigung**: Entfernen eingebetteter Zeichen, die zu einer falschen Datenausrichtung führen können, z. B. eingebetteter Tabstopps in tabstoppgetrennten Dateien oder eingebetteter Zeilenumbrüche, die z. B. Datensätze unterbrechen könnten.

In den folgenden Abschnitten werden einige dieser Schritte zur Datenverarbeitung beschrieben.

## <a name="how-to-deal-with-missing-values"></a>Wie werden fehlende Daten behandelt?
Bei fehlenden Werten empfiehlt es sich, zunächst den Grund für die fehlenden Werte zu ermitteln, um das Problem besser angehen zu können. Folgende Vorgehensweisen werden bei fehlenden Werten häufig angewendet:

* **Löschen**: Entfernen von Datensätze mit fehlenden Werten.
* **Ersetzen durch Platzhalterwerte**: Ersetzen fehlender Werte durch Platzhalterwerte: z.B. *unbekannt* bei kategorischen oder „0“ bei numerischen Werten.
* **Ersetzen durch Mittelwerte**: Ersetzen fehlender numerischer Daten durch Mittelwerte.
* **Ersetzen durch häufige Werte**: Ersetzen fehlender kategorischer Daten durch den häufigsten Eintrag.
* **Ersetzen durch Regressionswerte**: Verwenden eines Regressionsverfahren, um fehlende Werte durch Regressionswerte zu ersetzen.  

## <a name="how-to-normalize-data"></a>Wie werden Daten normalisiert?
Bei der Datennormalisierung werden numerische Werte in einen angegebenen Bereich neu skaliert. Folgende Normalisierungsverfahren werden häufig angewendet:

* **Min-Max-Normalisierung**: Lineares Transformieren der Daten in einen Bereich, z.B. zwischen 0 und 1. Der Mindestwert wird auf 0 skaliert und der Höchstwert auf 1.
* **Z-Wert-Normalisierung**: Skalieren der Daten basierend auf Mittelwert und Standardabweichung: Teilen der Differenz aus Daten und Mittelwert durch die Standardabweichung.
* **Dezimalskalierung**: Skalieren der Daten durch Verschieben des Dezimaltrennzeichens des Attributwerts.  

## <a name="how-to-discretize-data"></a>Wie werden Daten diskretisiert?
Daten können durch die Konvertierung kontinuierlicher Werte in nominale Attribute oder Intervalle diskretisiert werden. Dazu gibt es u. a. folgende Möglichkeiten:

* **Festbreitengruppierung**: Aufteilen des Bereichs aller möglichen Werte eines Attributs in N Gruppen derselben Größe und Zuweisen der Gruppennummer zu den Werten in einer Gruppe.
* **Festhöhengruppierung**: Aufteilen des Bereichs aller möglichen Werte eines Attributs in N Gruppen mit derselben Anzahl von Instanzen und Zuweisen der Gruppennummer zu den Werten in einer Gruppe.  

## <a name="how-to-reduce-data"></a>Wie werden Daten reduziert?
Es gibt verschiedene Methoden zum Reduzieren der Größe zur einfacheren Datenverarbeitung. Je nach Größe und Inhalt der Daten können folgende Verfahren angewendet werden:

* **Datensatzstichproben**: Erstellen von Stichproben aus den Datensätzen und Auswählen nur einer repräsentativen Teilmenge von Daten.
* **Attributstichproben**: Auswählen nur einer Teilmenge der wichtigsten Attribute aus den Daten.  
* **Aggregation**: Unterteilen der Daten in Gruppen und Speichern der Zahlen für jede Gruppe. Beispielsweise können die Tageseinnahmen einer Restaurant-Kette aus den letzten 20 Jahren im monatlichen Umsatz zusammengefasst werden, um die Größe der Daten zu verringern.  

## <a name="how-to-clean-text-data"></a>Wie werden Textdaten bereinigt?
**Textfelder in Tabellendaten** können Zeichen enthalten, die sich auf die Spaltenausrichtung und/oder die Datensatzgrenzen auswirken. Eingebettete Tabstopps in einer tabstoppgetrennten Datei verursachen z. B. Fehlausrichtungen von Spalten, während eingebettete Zeilenumbrüche Datensatzzeilen beschädigen. Eine fehlerhafte Verarbeitung von Textcodierungen beim Schreiben oder Lesen von Text kann zu Datenverlusten und unbeabsichtigten Einfügungen von unlesbaren Zeichen (z. B. NULL-Werte) führen und möglicherweise auch Auswirkungen auf die Textanalyse haben. Es ist möglicherweise eine sorgfältige Analyse und Bearbeitung erforderlich, um Textfelder für die korrekte Ausrichtung zu bereinigen und um strukturierte Daten aus unstrukturierten oder teilweise strukturierten Textdaten zu extrahieren.

**Durchsuchen von Daten** ermöglicht einen frühzeitigen Einblick in die Daten. Während dieses Schritts können bereits einige Probleme mit den Daten aufgedeckt und entsprechende Methoden angewendet werden, um diese Probleme zu beheben.  Es ist wichtig, Fragen wie die zur Ursache des Problems und dessen Ursprung zu stellen. Mit diesem Prozess können Sie auch bessere Entscheidungen für die Datenverarbeitungsschritte treffen, die zum Lösen der Probleme erforderlich sind. Die Identifizierung der endgültigen Anwendungsfälle und Personas kann auch zur Priorisierung des Datenverarbeitungsaufwands verwendet werden.

## <a name="references"></a>References
> *Datamining: Concepts and Techniques*, 3. Auflage, Morgan Kaufmann, 2011, Jiawei Han, Micheline Kamber und Jian Pei
> 
> 

