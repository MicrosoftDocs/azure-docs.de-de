---
title: Referenzleitfaden zu den Ansicht-Designer-Kacheln in Azure Monitor | Microsoft-Dokumentation
description: Mit dem Ansicht-Designer in Azure Monitor können Sie benutzerdefinierte Ansichten erstellen, die im Azure-Portal angezeigt werden und verschiedene Visualisierungen für Daten im Log Analytics-Arbeitsbereich enthalten. Dieser Artikel ist ein Referenzleitfaden zu den Einstellungen für die in Ihren benutzerdefinierten Ansichten verfügbaren Kacheln.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/17/2018
ms.openlocfilehash: b73fa9702a95897afbca53e2de70dcabc04a4665
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/18/2020
ms.locfileid: "92168505"
---
# <a name="reference-guide-to-view-designer-tiles-in-azure-monitor"></a>Referenzleitfaden zu Ansicht-Designer-Kacheln in Azure Monitor
Mithilfe des Ansicht-Designers in Azure Monitor können Sie verschiedene benutzerdefinierten Ansichten im Azure-Portal erstellen, in denen Sie Daten in Ihrem Log Analytics-Arbeitsbereich visualisieren können. Dieser Artikel ist ein Referenzleitfaden zu den Einstellungen für die in Ihren benutzerdefinierten Ansichten verfügbaren Kacheln.

Weitere Informationen zum Ansicht-Designer finden Sie in folgenden Artikeln:

* [Ansicht-Designer](view-designer.md): Bietet eine Übersicht über den Ansicht-Designer und die Verfahren zum Erstellen und Bearbeiten von benutzerdefinierten Ansichten.
* [Referenz der Visualisierungskomponenten:](view-designer-parts.md) Referenzleitfaden zu den Einstellungen für die in den benutzerdefinierten Ansichten verfügbaren Visualisierungskomponenten.


Die verfügbaren Ansicht-Designer-Kacheln werden in der folgenden Tabelle beschrieben:  

| Kachel | BESCHREIBUNG |
|:--- |:--- |
| [Number](#number-tile) |Die Anzahl von Datensätzen einer Abfrage. |
| [Zwei Zahlen](#two-numbers-tile) |Die Anzahl von Datensätzen aus zwei unterschiedlichen Abfragen. |
| [Ring](#donut-tile) | Ein auf einer Abfrage basierendes Diagramm mit einem zusammenfassenden Wert in der Mitte. |
| Liniendiagramm und Legende | Ein auf einer Abfrage basierendes Liniendiagramm und eine Legende mit einem zusammenfassenden Wert. |
| [Liniendiagramm](#line-chart-tile) |Ein Liniendiagramm, das auf einer Abfrage basiert. |
| [Zwei Zeitachsen](#two-timelines-tile) | Ein Säulendiagramm mit zwei Datenreihen, die jeweils auf einer separaten Abfrage basieren. |

Die nächsten Abschnitte beschreiben die Kacheltypen und ihre Eigenschaften ausführlich.

> [!NOTE]
> Kacheln in Ansichten basieren auf den [Protokollabfragen](../log-query/log-query-overview.md) in Ihrem Log Analytics-Arbeitsbereich. [Ressourcenübergreifende Abfragen](../log-query/cross-workspace-query.md) werden derzeit nicht zum Abrufen von Daten aus Application Insights unterstützt.

## <a name="number-tile"></a>Kachel „Zahl“
Die Kachel **Zahl** zeigt sowohl die Anzahl von Datensätzen einer Protokollabfrage als auch eine Legende an.

![Kachel „Zahl“](media/view-designer-tiles/tile-number.png)

| Einstellung | BESCHREIBUNG |
|:--- |:--- |
| Name |Der Text, der im oberen Bereich der Kachel angezeigt wird. |
| BESCHREIBUNG |Der Text, der unter dem Kachelnamen angezeigt wird. |
| **Kachel** | |
| Legende |Der Text, der unter dem Wert angezeigt wird. |
| Abfrage |Die Abfrage, die ausgeführt wird. Die Anzahl von Datensätzen wird angezeigt, die von der Abfrage zurückgegeben werden. |
| **Erweitert** |**&gt; Datenflussüberprüfung** |
| Enabled |Wählen Sie diesen Link aus, wenn die Datenflussüberprüfung für die Kachel aktiviert werden soll. Bei diesem Ansatz wird eine alternative Meldung angezeigt, wenn keine Daten für die Kachel verfügbar sind. Dieser Ansatz wird normalerweise genutzt, um eine Meldung während des vorübergehenden Zeitraums anzuzeigen, in dem die Ansicht installiert wird und Daten zur Verfügung gestellt werden. |
| Abfrage |Die Abfrage, die ausgeführt wird, um zu bestimmen, ob Daten für die Ansicht verfügbar sind. Wenn die Abfrage keine Ergebnisse zurückgibt, wird anstelle des Werts aus der Hauptabfrage eine Meldung angezeigt. |
| `Message` |Die Meldung, die angezeigt wird, wenn die Abfrage zur Datenflussüberprüfung keine Daten zurückgibt. Wenn Sie keine Meldung angeben, wird die Statusmeldung *Bewertung wird durchgeführt* angezeigt. |


## <a name="two-numbers-tile"></a>Kachel „Zwei Zahlen“
Diese Kachel zeigt die Anzahl von Datensätzen aus zwei verschiedenen Protokollabfragen sowie jeweils eine Bezeichnung an.

![Kachel „Zwei Zahlen“](media/view-designer-tiles/tile-two-numbers.png)

| Einstellung | BESCHREIBUNG |
|:--- |:--- |
| Name |Der Text, der im oberen Bereich der Kachel angezeigt wird. |
| BESCHREIBUNG |Der Text, der unter dem Kachelnamen angezeigt wird. |
| **Erste Kachel** | |
| Legende |Der Text, der unter dem Wert angezeigt wird. |
| Abfrage |Die Abfrage, die ausgeführt wird. Die Anzahl von Datensätzen wird angezeigt, die von der Abfrage zurückgegeben werden. |
| **Zweite Kachel** | |
| Legende |Der Text, der unter dem Wert angezeigt wird. |
| Abfrage |Die Abfrage, die ausgeführt wird. Die Anzahl von Datensätzen wird angezeigt, die von der Abfrage zurückgegeben werden. |
| **Erweitert** |**&gt; Datenflussüberprüfung** |
| Enabled |Wählen Sie diesen Link aus, wenn die Datenflussüberprüfung für die Kachel aktiviert werden soll. Bei diesem Ansatz wird eine alternative Meldung angezeigt, wenn keine Daten für die Kachel verfügbar sind. Dieser Ansatz wird normalerweise genutzt, um eine Meldung während des vorübergehenden Zeitraums anzuzeigen, in dem die Ansicht installiert wird und Daten zur Verfügung gestellt werden. |
| Abfrage |Die Abfrage, die ausgeführt wird, um zu bestimmen, ob Daten für die Ansicht verfügbar sind. Wenn die Abfrage keine Ergebnisse zurückgibt, wird anstelle des Werts aus der Hauptabfrage eine Meldung angezeigt. |
| `Message` |Die Meldung, die angezeigt wird, wenn die Abfrage zur Datenflussüberprüfung keine Daten zurückgibt. Wenn Sie keine Meldung angeben, wird die Statusmeldung *Bewertung wird durchgeführt* angezeigt. |


## <a name="donut-tile"></a>Kachel „Ring“
Die Kachel **Ring** zeigt eine einzelne Zahl an, die eine Spalte mit Werten in einer Protokollabfrage zusammenfasst. Der Ring stellt die Ergebnisse der ersten drei Datensätze grafisch dar.

![Kachel „Ring“](media/view-designer-tiles/tile-donut.png)

| Einstellung | BESCHREIBUNG |
|:--- |:--- |
| Name |Der Text, der im oberen Bereich der Kachel angezeigt wird. |
| BESCHREIBUNG |Der Text, der unter dem Kachelnamen angezeigt wird. |
| **Ring** | |
| Abfrage |Die Abfrage, die für das Ringdiagramm ausgeführt wird. Die erste Eigenschaft ist ein Textwert, die zweite Eigenschaft ist ein numerischer Wert. Bei dieser Abfrage wird üblicherweise das Schlüsselwort *measure* verwendet, um die Ergebnisse zusammenzufassen. |
| **Ring** |**&gt; Mitte** |
| Text |Der Text, der unter dem Wert innerhalb des Ringdiagramms angezeigt wird. |
| Vorgang |Der Vorgang, der für die Werteigenschaft ausgeführt wird, um sie zu einem einzelnen Wert zusammenzufassen.<ul><li>Summe: Addiert die Werte aller Datensätze mit dem Eigenschaftswert.</li><li>Prozentsatz: Prozentsatz der summierten Werte aus Datensätzen mit dem Eigenschaftswert, verglichen mit den summierten Werten aller Datensätze.</li></ul> |
| Vom Vorgang in der Mitte verwendete Ergebniswerte |Klicken Sie optional auf das Pluszeichen (+), um einen oder mehrere Werte hinzuzufügen. Die Ergebnisse der Abfrage werden auf Datensätze mit den von Ihnen angegebenen Eigenschaftswerten beschränkt. Wenn keine Werte hinzugefügt werden, werden alle Datensätze in die Abfrage aufgenommen. |
| **Ring** |**&gt; Weitere Optionen** |
| Farben |Die Farbe, die für jede der ersten drei Eigenschaften angezeigt wird. Um andere Farben für bestimmte Eigenschaftswerte anzugeben, verwenden Sie *Erweiterte Farbzuordnung* . |
| Erweiterte Farbzuordnung |Zeigt eine Farbe, die bestimmte Eigenschaftswerte repräsentiert. Gehört der angegebene Wert zu den ersten drei, wird anstelle der Standardfarbe die alternative Farbe angezeigt. Wenn die Eigenschaft nicht in den ersten drei enthalten ist, wird die Farbe nicht angezeigt. |
| **Erweitert** |**&gt; Datenflussüberprüfung** |
| Enabled |Wählen Sie diesen Link aus, wenn die Datenflussüberprüfung für die Kachel aktiviert werden soll. Bei diesem Ansatz wird eine alternative Meldung angezeigt, wenn keine Daten für die Kachel verfügbar sind. Dieser Ansatz wird normalerweise genutzt, um eine Meldung während des vorübergehenden Zeitraums anzuzeigen, in dem die Ansicht installiert wird und Daten zur Verfügung gestellt werden. |
| Abfrage |Die Abfrage, die ausgeführt wird, um zu bestimmen, ob Daten für die Ansicht verfügbar sind. Wenn die Abfrage keine Ergebnisse zurückgibt, wird anstelle des Werts aus der Hauptabfrage eine Meldung angezeigt. |
| `Message` |Die Meldung, die angezeigt wird, wenn die Abfrage zur Datenflussüberprüfung keine Daten zurückgibt. Wenn Sie keine Meldung angeben, wird die Statusmeldung *Bewertung wird durchgeführt* angezeigt. |


## <a name="line-chart-tile"></a>Kachel „Liniendiagramm“
Diese Kachel ist ein Liniendiagramm, das mehrere Datenreihen aus einer Protokollabfrage im zeitlichen Verlauf anzeigt. 

![Screenshot einer Liniendiagramm-Kachel im Azure Monitor-Ansicht-Designer](media/view-designer-tiles/tile-line-chart.png)

| Einstellung | BESCHREIBUNG |
|:--- |:--- |
| Name |Der Text, der im oberen Bereich der Kachel angezeigt wird. |
| BESCHREIBUNG |Der Text, der unter dem Kachelnamen angezeigt wird. |
| **Liniendiagramm** | |
| Abfrage |Die Abfrage, die für das Liniendiagramm ausgeführt wird. Die erste Eigenschaft ist ein Textwert, die zweite Eigenschaft ist ein numerischer Wert. Bei dieser Abfrage wird üblicherweise das Schlüsselwort *measure* verwendet, um die Ergebnisse zusammenzufassen. Wenn in der Abfrage das Schlüsselwort für das *Intervall* verwendet wird, wird für die X-Achse des Diagramms dieses Intervall verwendet. Wenn in der Abfrage das Schlüsselwort für das *Intervall* nicht verwendet wird, wird für die X-Achse ein Stundenintervall verwendet. |
| **Liniendiagramm** |**> Y-Achse** |
| Logarithmische Skala verwenden |Wählen Sie diesen Link aus, um eine logarithmische Skala für die Y-Achse zu verwenden. |
| Units |Geben Sie die Einheiten für die von der Abfrage zurückgegebenen Werte an. Diese Informationen werden verwendet, um Bezeichnungen im Diagramm anzuzeigen, die Werttypen angeben, und um optional Werte zu konvertieren. Der **Typ der Einheit** gibt die Kategorie der Einheit an und definiert die verfügbaren Werte für den Typ in **Aktuelle Einheit** . Bei Auswahl eines Werts in **Konvertieren in** werden die numerischen Werte vom Typ in **Aktuelle Einheit** in den Typ in **Konvertieren in** konvertiert. |
| Benutzerdefinierte Bezeichnung |Der Text, der für die Y-Achse neben der Bezeichnung für den Typ der *Einheit* angezeigt wird. Wenn keine Bezeichnung angegeben ist, wird nur der Typ der *Einheit* angezeigt. |
| **Erweitert** |**&gt; Datenflussüberprüfung** |
| Enabled |Wählen Sie diesen Link aus, wenn die Datenflussüberprüfung für die Kachel aktiviert werden soll. Bei diesem Ansatz wird eine alternative Meldung angezeigt, wenn keine Daten für die Kachel verfügbar sind. Dieser Ansatz wird normalerweise genutzt, um eine Meldung während des vorübergehenden Zeitraums anzuzeigen, in dem die Ansicht installiert wird und Daten zur Verfügung gestellt werden. |
| Abfrage |Die Abfrage, die ausgeführt wird, um zu bestimmen, ob Daten für die Ansicht verfügbar sind. Wenn die Abfrage keine Ergebnisse zurückgibt, wird anstelle des Werts aus der Hauptabfrage eine Meldung angezeigt. |
| `Message` |Die Meldung, die angezeigt wird, wenn die Abfrage zur Datenflussüberprüfung keine Daten zurückgibt. Wenn Sie keine Meldung angeben, wird die Statusmeldung *Bewertung wird durchgeführt* angezeigt. |


## <a name="line-chart-and-callout-tile"></a>Kachel mit Liniendiagramm und Legende
Diese Kachel umfasst sowohl ein Liniendiagramm mit mehreren Datenreihen aus einer Protokollabfrage im zeitlichen Verlauf als auch eine Legende mit einem zusammengefassten Wert. 

![Screenshot einer Liniendiagramm- und Legende-Kachel im Azure Monitor-Ansicht-Designer. Die Legende erweitert das Liniendiagramm, indem ein zusammengefasster Wert angezeigt wird.](media/view-designer-tiles/tile-line-chart-callout.png)

| Einstellung | BESCHREIBUNG |
|:--- |:--- |
| Name |Der Text, der im oberen Bereich der Kachel angezeigt wird. |
| BESCHREIBUNG |Der Text, der unter dem Kachelnamen angezeigt wird. |
| **Liniendiagramm** | |
| Abfrage |Die Abfrage, die für das Liniendiagramm ausgeführt wird. Die erste Eigenschaft ist ein Textwert, die zweite Eigenschaft ist ein numerischer Wert. Bei dieser Abfrage wird üblicherweise das Schlüsselwort *measure* verwendet, um die Ergebnisse zusammenzufassen. Wenn in der Abfrage das Schlüsselwort für das *Intervall* verwendet wird, wird für die X-Achse des Diagramms dieses Intervall verwendet. Wenn in der Abfrage das Schlüsselwort für das *Intervall* nicht verwendet wird, wird für die X-Achse ein Stundenintervall verwendet. |
| **Liniendiagramm** |**&gt; Legende** |
| Legendentitel | Der Text, der über dem Legendenwert angezeigt wird. |
| Datenreihenname |Der Datenreiheneigenschaftswert, der als Legendenwert verwendet werden soll. Wenn keine Reihe angegeben wird, werden alle Datensätze der Abfrage verwendet. |
| Vorgang |Der Vorgang, der für die Werteigenschaft ausgeführt wird, um sie zu einem einzelnen Wert für die Legende zusammenzufassen.<ul><li>Durchschnitt: Der Durchschnitt der Werte aller Datensätze.</li><li>Anzahl: Die Anzahl von Datensätzen, die von der Abfrage zurückgegeben werden.</li><li>Letztes Beispiel: Der Wert aus dem letzten Intervall im Diagramm.</li><li>Max: Der Höchstwert aus den Intervallen im Diagramm.</li><li>Min: Der Mindestwert aus den Intervallen im Diagramm.</li><li>Summe: Die Summe der Werte aller Datensätze.</li></ul> |
| **Liniendiagramm** |**> Y-Achse** |
| Logarithmische Skala verwenden |Wählen Sie diesen Link aus, um eine logarithmische Skala für die Y-Achse zu verwenden. |
| Units |Geben Sie die Einheiten für die von der Abfrage zurückgegebenen Werte an. Diese Informationen werden zur Anzeige von Bezeichnungen im Diagramm verwendet, die die Werttypen angeben, und um optional Werte zu konvertieren. Der Typ der *Einheit* gibt die Kategorie der Einheit an und definiert die verfügbaren Werte für den Typ in *Aktuelle Einheit* . Bei Auswahl eines Werts in *Konvertieren in* werden die numerischen Werte vom Typ in *Aktuelle Einheit* in den Typ in *Konvertieren in* konvertiert. |
| Benutzerdefinierte Bezeichnung |Der Text, der für die Y-Achse neben der Bezeichnung für den Typ der *Einheit* angezeigt wird. Wenn keine Bezeichnung angegeben ist, wird nur der Typ der *Einheit* angezeigt. |
| **Erweitert** |**&gt; Datenflussüberprüfung** |
| Enabled |Wählen Sie diesen Link aus, wenn die Datenflussüberprüfung für die Kachel aktiviert werden soll. Bei diesem Ansatz wird eine alternative Meldung angezeigt, wenn keine Daten für die Kachel verfügbar sind. Dieser Ansatz wird normalerweise genutzt, um eine Meldung während des vorübergehenden Zeitraums anzuzeigen, in dem die Ansicht installiert wird und Daten zur Verfügung gestellt werden. |
| Abfrage |Die Abfrage, die ausgeführt wird, um zu bestimmen, ob Daten für die Ansicht verfügbar sind. Wenn die Abfrage keine Ergebnisse zurückgibt, wird anstelle des Werts aus der Hauptabfrage eine Meldung angezeigt. |
| `Message` |Die Meldung, die angezeigt wird, wenn die Abfrage zur Datenflussüberprüfung keine Daten zurückgibt. Wenn Sie keine Meldung angeben, wird die Statusmeldung *Bewertung wird durchgeführt* angezeigt. |


## <a name="two-timelines-tile"></a>Kachel „Zwei Zeitachsen“
Die Kachel **Zwei Zeitachsen** zeigt die Ergebnisse von zwei Protokollabfragen im Verlauf als Säulendiagramme an. Für jede Reihe wird eine Legende angezeigt. 

![Kachel „Zwei Zeitachsen“](media/view-designer-tiles/tile-two-timelines.png)

| Einstellung | BESCHREIBUNG |
|:--- |:--- |
| Name |Der Text, der im oberen Bereich der Kachel angezeigt wird. |
| BESCHREIBUNG |Der Text, der unter dem Kachelnamen angezeigt wird. |
| Erstes Diagramm | |
| Legende |Der Text, der unter der Legende für die erste Datenreihe angezeigt wird. |
| Color |Die Farbe, die für die Säulen in der ersten Datenreihe verwendet wird. |
| Diagrammabfrage |Die Abfrage, die für die ersten Datenreihen ausgeführt wird. Die Anzahl von Datensätzen in jedem Zeitintervall wird durch die Säulen im Diagramm dargestellt. |
| Vorgang |Der Vorgang, der für die Werteigenschaft ausgeführt wird, um sie zu einem einzelnen Wert für die Legende zusammenzufassen.<ul><li>Durchschnitt: Der Durchschnitt der Werte aller Datensätze.</li><li>Anzahl: Die Anzahl von Datensätzen, die von der Abfrage zurückgegeben werden.</li><li>Letztes Beispiel: Der Wert aus dem letzten Intervall im Diagramm.</li><li>Max: Der Höchstwert aus den Intervallen im Diagramm.</li></ul> |
| **Zweites Diagramm** | |
| Legende |Der Text, der unter der Legende für die erste Datenreihe angezeigt wird. |
| Color |Die Farbe, die für die Säulen in der zweiten Datenreihe verwendet wird. |
| Diagrammabfrage |Die Abfrage, die für die zweite Datenreihe ausgeführt wird. Die Anzahl von Datensätzen in jedem Zeitintervall wird durch die Säulen im Diagramm dargestellt. |
| Vorgang |Der Vorgang, der für die Werteigenschaft ausgeführt wird, um sie zu einem einzelnen Wert für die Legende zusammenzufassen.<ul><li>Durchschnitt: Der Durchschnitt der Werte aller Datensätze.</li><li>Anzahl: Die Anzahl von Datensätzen, die von der Abfrage zurückgegeben werden.</li><li>Letztes Beispiel: Der Wert aus dem letzten Intervall im Diagramm.</li><li>Max: Der Höchstwert aus den Intervallen im Diagramm. |
| **Erweitert** |**&gt; Datenflussüberprüfung** |
| Enabled |Wählen Sie diesen Link aus, wenn die Datenflussüberprüfung für die Kachel aktiviert werden soll. Bei diesem Ansatz wird eine alternative Meldung angezeigt, wenn keine Daten für die Kachel verfügbar sind. Dieser Ansatz wird normalerweise genutzt, um eine Meldung während des vorübergehenden Zeitraums anzuzeigen, in dem die Ansicht installiert wird und Daten zur Verfügung gestellt werden. |
| Abfrage |Die Abfrage, die ausgeführt wird, um zu bestimmen, ob Daten für die Ansicht verfügbar sind. Wenn die Abfrage keine Ergebnisse zurückgibt, wird anstelle des Werts aus der Hauptabfrage eine Meldung angezeigt. |
| `Message` |Die Meldung, die angezeigt wird, wenn die Abfrage zur Datenflussüberprüfung keine Daten zurückgibt. Wenn Sie keine Meldung angeben, wird die Statusmeldung *Bewertung wird durchgeführt* angezeigt. |


## <a name="next-steps"></a>Nächste Schritte
* Erfahren Sie mehr über [Protokollabfragen](../log-query/log-query-overview.md) zur Unterstützung der Abfragen in Kacheln.
* Fügen Sie [Visualisierungskomponenten](view-designer-parts.md) zu Ihrer benutzerdefinierten Ansicht hinzu.
