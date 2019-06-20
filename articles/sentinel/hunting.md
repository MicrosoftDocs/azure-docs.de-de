---
title: Bedrohungssuchfunktionen in Azure Sentinel Preview | Microsoft-Dokumentation
description: In diesem Artikel wird beschrieben, wie die Bedrohungssuchfunktionen von Azure Sentinel verwendet werden.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 6aa9dd27-6506-49c5-8e97-cc1aebecee87
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: 754722508de248294667ee9e923e8faf3ad67f61
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65236287"
---
# <a name="hunt-for-threats-with-in-azure-sentinel-preview"></a>Suchen nach Bedrohungen mit Azure Sentinel Preview

> [!IMPORTANT]
> Azure Sentinel ist aktuell als Public Preview verfügbar.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Wenn Sie ein Ermittler sind, der proaktiv nach Sicherheitsbedrohungen suchen möchte, können Sie die leistungsstarken Bedrohungssuch- und Abfragetools von Azure Sentinel verwenden, um nach Sicherheitsbedrohungen in den Datenquellen Ihrer Organisation zu suchen. Aber Ihre Systeme und Sicherheitsappliances erzeugen Unmengen von Daten, für die es schwierig sein kann, sie zu aussagefähigen Ereignissen zu analysieren und zu filtern. Um Sicherheitsanalysten dabei zu unterstützen, proaktiv nach neuen Anomalien zu suchen, die von Ihren Sicherheitsanwendungen nicht erkannt wurden, werden Sie von den in Azure Sentinel integrierten Bedrohungssuchabfragen dazu geführt, die richtigen Fragen zu stellen, um Probleme in den Daten zu finden, die bereits in Ihrem Netzwerk vorhanden sind. 

Zum Beispiel liefert eine integrierte Abfrage Daten über die ungewöhnlichsten Prozesse, die in Ihrer Infrastruktur ausgeführt werden – Sie möchten nicht, dass bei jeder Ausführung eines solchen Prozesse eine Benachrichtigung erfolgt, denn er könnte völlig unproblematisch sein, aber Sie möchten die Abfrage gelegentlich ausführen, um zu sehen, ob es etwas Ungewöhnliches gibt. 



Mit Azure Sentinel-Bedrohungssuche (Ermittlung) können Sie die folgenden Funktionen nutzen:

- Integrierte Abfragen: Um Ihnen den Einstieg zu erleichtern, bietet eine Startseite vorab geladene Abfragebeispiele, um Ihnen die ersten Schritte zu vereinfachen und Sie mit den Tabellen und der Abfragesprache vertraut zu machen. Diese integrierten Bedrohungssuchabfragen werden von Microsoft-Sicherheitsexperten kontinuierlich entwickelt. Dies geschieht durch Hinzufügen neuer Abfragen und Optimieren vorhandener Abfragen, um Ihnen einen Einstiegspunkt zu bieten, ab dem Sie nach neuen Erkennungen suchen und feststellen können, wo Sie nach den Anfängen neuer Angriffe suchen sollten. 

- Leistungsfähige Abfragesprache mit IntelliSense: Aufgesetzt auf eine Abfragesprache, die Ihnen die Flexibilität bietet, die Sie benötigen, um die Bedrohungssuche auf die nächste Stufe zu heben.

- Erstellen Sie Ihre eigenen Lesezeichen: Während des Bedrohungssuchprozesses stoßen Sie möglicherweise auf Übereinstimmungen oder Ergebnisse, Dashboards oder Aktivitäten, die ungewöhnlich oder verdächtig aussehen. Um diese Elemente zu markieren, damit Sie zukünftig zu ihnen zurückkehren können, verwenden Sie die Lesezeichenfunktionalität. Mit Lesezeichen können Sie Elemente speichern, damit diese später zum Erstellen eines zu untersuchenden Falls verwendet werden können. Weitere Informationen über Lesezeichen finden Sie unter „Verwenden von [Lesezeichen in Bedrohungssuche]“.

- Verwenden von Notebooks, um Untersuchungen zu automatisieren: Notebooks sind wie Schritt-für-Schritt-Playbooks, die Sie erstellen können, um die Schritte einer Untersuchung zu durchlaufen und nach Bedrohungen zu suchen.  Notebooks kapseln alle Bedrohungssuchschritte in einem wiederverwendbaren Playbook, das für andere Personen in Ihrer Organisation freigegeben werden kann. 
- Fragen Sie die gespeicherten Daten ab: Die Daten sind in Tabellen zugänglich, die Sie abfragen können. Beispielsweise können Sie Prozesserstellung, DNS-Ereignisse und viele andere Ereignistypen abfragen.

- Links zur Community: Nutzen Sie die Leistungsfähigkeit der größeren Community, um weitere Abfragen und Datenquellen zu finden.
 
## <a name="get-started-hunting"></a>Beginnen mit der Bedrohungssuche (Hunting)

1. Klicken Sie im Azure Sentinel-Portal auf **Suche** (Hunting).
  ![Azure Sentinel beginnt mit der Bedrohungssuche](media/tutorial-hunting/hunting-start.png)

2. Wenn Sie die Seite **Suche** (Hunting) öffnen, werden alle Bedrohungssuchabfragen in einer einzelnen Tabelle angezeigt. In der Tabelle werden alle Abfragen, die von Microsofts Team der Sicherheitsanalysten geschrieben wurden, sowie alle weiteren Abfragen aufgelistet, die Sie erstellt oder geändert haben. Jede Abfrage enthält eine Beschreibung dazu, nach welcher Bedrohung mit ihr gesucht wird und für welche Art von Daten sie ausgeführt wird. Diese Vorlagen sind nach ihren verschiedene Taktiken gruppiert: Die Symbole auf der rechten Seite kategorisieren den Typ der Bedrohung, etwa Erstzugriff, Persistenz und Exfiltration. Sie können diese Vorlagen für Bedrohungsabfragen mit jedem dieser Felder filtern. Sie können jede Abfrage in Ihren Favoriten speichern. Ist eine Abfrage in Ihren Favoriten gespeichert, wird sie automatisch jedes Mal ausgeführt, wenn auf die Seite **Suche** (Hunting) zugegriffen wird. Sie können eine eigene Bedrohungssuchabfrage erstellen oder eine vorhandene Vorlage für Bedrohungssuchabfragen klonen oder anpassen. 
 
2. Klicken Sie auf der Seite mit den Details der Bedrohungssuchabfrage auf **Abfrage ausführen**, um eine Abfrage auszuführen, ohne die Seite „Suche“ (Hunting) zu verlassen.  Die Anzahl der Übereinstimmungen wird in der Tabelle angezeigt. Überprüfen Sie die Liste der Bedrohungssuchabfragen und deren Übereinstimmungen. Stellen Sie fest, mit welcher Stufe in der Kill Chain die Übereinstimmung verknüpft ist.

3. Nehmen Sie eine schnelle Überprüfung der zugrunde liegenden Abfrage im Bereich für Abfragedetails vor, oder klicken Sie auf **Abfrageergebnis anzeigen** (View query result), um die Abfrage in Log Analytics zu öffnen. Überprüfen Sie im unteren Bereich die Übereinstimmungen für die Abfrage.

4.  Klicken Sie auf die Zeile, und wählen **Lesezeichen hinzufügen** (Add bookmark) aus, um die Zeilen hinzuzufügen, die ausgewertet werden sollen. Sie können dies für jeden Eintrag ausführen, der verdächtig aussieht. 

5. Kehren Sie dann zur Hauptseite von **Suche** (Hunting) zurück, und klicken Sie auf die Registerkarte **Lesezeichen** (Bookmarks), um alle verdächtigen Aktivitäten anzuzeigen. 

6. Wählen Sie ein Lesezeichen aus, und klicken Sie dann auf **Untersuchen**, um die Untersuchungsumgebung zu öffnen. Sie können die Lesezeichen filtern. Wenn Sie beispielsweise eine Kampagne untersuchen, können Sie ein Tag für die Kampagne erstellen und dann alle Lesezeichen auf Basis der Kampagne filtern.

1. Sobald Sie wissen, welche Suchabfrage wertvolle Erkenntnisse über mögliche Angriffe liefert, können Sie basierend auf der Abfrage auch benutzerdefinierte Erkennungsregeln erstellen und diese Erkenntnisse als Warnungen in Ihren Antwortdiensten für Sicherheitsvorfälle anzeigen.

 

## <a name="query-language"></a>Abfragesprache 

Bedrohungssuchen in Azure Sentinel basiert auf der Abfragesprache von Azure Log Analytics. Weitere Informationen zu der Abfragesprache und den unterstützten Operatoren finden Sie unter [Erste Schritte mit Azure Monitor-Protokollabfragen](https://docs.loganalytics.io/docs/Language-Reference/).

## <a name="public-hunting-query-github-repository"></a>Öffentliches GitHub-Repository für Bedrohungssuchabfragen (Hunting-Abfragen)

Sehen Sie sich das [Repository für Bedrohungssuchabfragen](https://github.com/Azure/Orion) an. Tragen Sie eigene Abfragen bei, und verwenden Sie Beispielabfragen, die von unseren Kunden geteilt wurden.

 

## <a name="sample-query"></a>Beispielabfrage

Eine typische Abfrage beginnt mit einem Tabellennamen, gefolgt von einer Reihe von Operatoren, die durch \| getrennt sind.

Beginnen Sie im obigen Beispiel mit dem Tabellennamen „SecurityEvent“, und fügen Sie nach Bedarf über Pipes verkettete Elemente hinzu.

1. Definieren Sie einen Zeitfilter, um nur Datensätze aus den letzten sieben Tagen zu überprüfen.

2. Fügen Sie der Abfrage einen Filter hinzu, um nur Ereignisse mit der ID 4688 anzuzeigen.

3. Fügen Sie der Abfrage einen Filter für die Befehlszeile (CommandLine) hinzu, sodass nur Instanzen von „cscript.exe“ enthalten sind.

4. Wählen Sie über „project“ nur die Spalten aus, Sie untersuchen möchten, begrenzen Sie die Ergebnisse auf 1000, und klicken Sie auf **Abfrage ausführen**.
5. Klicken Sie auf das grüne Dreieck, um die Abfrage auszuführen. Sie können die Abfrage testen, und sie ausführen, um nach anormalem Verhalten zu suchen.

## <a name="useful-operators"></a>Hilfreiche Operatoren

Die Abfragesprache ist leistungsstark und hat zahlreiche Operatoren, von denen einige besonders nützliche hier aufgeführt sind:

**where**: Filtert eine Tabelle auf die Teilmenge von Zeilen, die ein Prädikat erfüllen.

**summarize**: Erzeugt eine Tabelle, in der der Inhalt der Eingabetabelle aggregiert ist.

**join**: Führt die Zeilen zweier Tabellen zusammen, um eine neue Tabelle zu erzeugen, indem Werte aus den angegebenen Spalten aus beiden Tabellen zugeordnet werden.

**count**: Gibt die Anzahl von Datensätzen in der Eingabedatensatzgruppe zurück.

**top**: Gibt die ersten N Datensätze zurück, wobei diese nach den angegebenen Spalten sortiert sind.

**limit**: Gibt maximal die angegebene Anzahl von Zeilen zurück.

**project**: Wählt die Spalten aus, die einbezogen, umbenannt oder gelöscht werden sollen, und fügt neue berechnete Spalten ein.

**extend**: Erstellt berechnete Spalten und fügt diese an das Resultset an.

**makeset**: Gibt ein dynamisches(JSON) Array der Menge unterschiedlicher Werte zurück, die ein Ausdruck (Expr) in der Gruppe annimmt.

**find**: Sucht nach Zeilen, die einem Prädikat in einer Gruppe von Tabellen entsprechen.

## <a name="save-a-query"></a>Speichern einer Abfrage

Sie können eine Abfrage erstellen oder ändern, und Sie können sie als Ihre eigene Abfrage speichern oder für Benutzer freigeben, die sich im selben Mandanten befinden.

   ![Speichern der Abfrage](./media/tutorial-hunting/save-query.png)

Erstellen Sie eine neue Bedrohungssuchabfrage (Hunting-Abfrage):

1. Klicken Sie auf **Neue Abfrage**, und wählen Sie **Speichern** aus.
2. Füllen Sie alle leeren Felder aus, und wählen Sie **Speichern** aus.

   ![Neue Abfrage](./media/tutorial-hunting/new-query.png)

Klonen und ändern Sie eine vorhandene Bedrohungssuchabfrage:

1. Wählen Sie in der Tabelle die Bedrohungssuchabfrage aus, die Sie ändern möchten.
2. Wählen Sie in der Zeile der Abfrage, die Sie ändern möchten, die Auslassungspunkte (...) aus, und wählen Sie **Abfrage klonen**.

   ![Abfrage klonen](./media/tutorial-hunting/clone-query.png)
 

3. Ändern Sie die Abfrage, und wählen Sie **Erstellen** aus.

   ![Benutzerdefinierte Abfrage](./media/tutorial-hunting/custom-query.png)

## <a name="next-steps"></a>Nächste Schritte
In diesem Artikel haben Sie erfahren, wie Sie eine Bedrohungsuntersuchung mit Azure Sentinel ausführen. Weitere Informationen zu Azure Sentinel finden Sie in den folgenden Artikeln:


- [Verwenden von Notebooks zur Ermittlung von Anomalien](notebooks.md)
- [Behalten des Überblicks über Daten bei einer Bedrohungssuche](bookmarks.md)