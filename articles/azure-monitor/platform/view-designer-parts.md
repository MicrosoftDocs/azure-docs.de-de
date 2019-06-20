---
title: Referenzleitfaden zu den Ansicht-Designer-Komponenten in Azure Monitor | Microsoft-Dokumentation
description: Mit dem Ansicht-Designer in Azure Monitor können Sie benutzerdefinierte Ansichten erstellen, die im Azure-Portal angezeigt werden und verschiedene Visualisierungen für Daten im Log Analytics-Arbeitsbereich enthalten. Dieser Artikel ist ein Referenzleitfaden zu den Einstellungen für die in Ihren benutzerdefinierten Ansichten verfügbaren Visualisierungsteilen.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: 5718d620-b96e-4d33-8616-e127ee9379c4
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: bwren
ms.openlocfilehash: dead1fae9bc3287ed0fc80c6120914e965ef96dd
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61341876"
---
# <a name="reference-guide-to-view-designer-visualization-parts-in-azure-monitor"></a>Referenzleitfaden zu Ansicht-Designer-Visualisierungskomponenten in Azure Monitor
Mithilfe des Ansicht-Designers in Azure Monitor können Sie verschiedene benutzerdefinierten Ansichten im Azure-Portal erstellen, in denen Sie Daten in Ihrem Log Analytics-Arbeitsbereich visualisieren können. Dieser Artikel ist ein Referenzleitfaden zu den Einstellungen für die in Ihren benutzerdefinierten Ansichten verfügbaren Visualisierungsteilen.

Weitere Informationen zum Ansicht-Designer finden Sie in folgenden Artikeln:

* [Ansicht-Designer](view-designer.md): Bietet eine Übersicht über den Ansicht-Designer und die Verfahren zum Erstellen und Bearbeiten von benutzerdefinierten Ansichten.
* [Kachelreferenz](view-designer-tiles.md): Dient als Referenz zu den Einstellungen für die einzelnen verfügbaren Kacheln in den benutzerdefinierten Ansichten.


Die verfügbaren Ansicht-Designer-Kacheltypen werden in der folgenden Tabelle beschrieben:

| Ansichtstyp | BESCHREIBUNG |
|:--- |:--- |
| [Liste der Abfragen](#list-of-queries-part) |Zeigt eine Liste der Protokollabfragen an. Sie können einzelne Abfragen auswählen, um die Ergebnisse anzuzeigen. |
| [Zahl und Liste](#number-and-list-part) |Die Kopfzeile zeigt eine einzelne Zahl an, die die Anzahl der Datensätze aus einer Protokollabfrage angibt. Die Liste zeigt die ersten zehn Ergebnisse aus einer Abfrage an. Ein Diagramm gibt dabei den relativen Wert einer numerischen Spalte oder die Änderung des Werts im Verlauf. |
| [Zwei Zahlen und Liste](#two-numbers-and-list-part) |Die Kopfzeile zeigt zwei Zahlen an, die die Anzahl der Datensätze aus separaten Protokollabfragen angeben. Die Liste zeigt die ersten zehn Ergebnisse aus einer Abfrage an. Ein Diagramm gibt dabei den relativen Wert einer numerischen Spalte oder die Änderung des Werts im Verlauf. |
| [Ringdiagramm und Liste](#donut-and-list-part) |Die Kopfzeile zeigt eine einzelne Zahl, die eine Wertspalte in einer Protokollabfrage zusammenfasst. Der Ring stellt die Ergebnisse der ersten drei Datensätze grafisch dar. |
| [Zwei Zeitachsen und Liste](#two-timelines-and-list-part) |Die Kopfzeile zeigt die Ergebnisse von zwei Protokollabfragen im Verlauf als Säulendiagramme an. In einer Legende wird eine einzelne Zahl dargestellt, die eine Wertspalte in einer Protokollabfrage zusammenfasst. Die Liste zeigt die ersten zehn Ergebnisse aus einer Abfrage an. Ein Diagramm gibt dabei den relativen Wert einer numerischen Spalte oder die Änderung des Werts im Verlauf. |
| [Informationen](#information-part) |Die Kopfzeile zeigt statischen Text und einen optionalen Link. Die Liste zeigt mindestens ein Element mit einer statischen Kachel und Text. |
| [Liniendiagramm, Legende und Liste](#line-chart-callout-and-list-part) |Die Kopfzeile zeigt ein Liniendiagramm mit mehreren Reihen aus einer Protokollabfrage im Verlauf und eine Legende mit einem zusammengefassten Wert an. Die Liste zeigt die ersten zehn Ergebnisse aus einer Abfrage an. Ein Diagramm gibt dabei den relativen Wert einer numerischen Spalte oder die Änderung des Werts im Verlauf. |
| [Liniendiagramm und Liste](#line-chart-and-list-part) |Die Kopfzeile zeigt ein Liniendiagramm mit mehreren Reihen aus einer Protokollabfrage im Verlauf an. Die Liste zeigt die ersten zehn Ergebnisse aus einer Abfrage an. Ein Diagramm gibt dabei den relativen Wert einer numerischen Spalte oder die Änderung des Werts im Verlauf. |
| [Komponente mit Liniendiagrammstapel](#stack-of-line-charts-part) |Zeigt ein Liniendiagramm mit mehreren Reihen aus einer Protokollabfrage im Verlauf an. |

Die nächsten Abschnitte beschreiben die Kacheltypen und ihre Eigenschaften ausführlich.

> [!NOTE]
> Teile der Ansichten basieren auf den [Protokollabfragen](../log-query/log-query-overview.md) in Ihrem Log Analytics-Arbeitsbereich. [Ressourcenübergreifende Abfragen](../log-query/cross-workspace-query.md) werden derzeit nicht zum Abrufen von Daten aus Application Insights unterstützt.

## <a name="list-of-queries-part"></a>Komponente mit der Liste der Abfragen
Die Komponente für die Liste der Abfragen zeigt eine Liste der Protokollabfragen an. Sie können einzelne Abfragen auswählen, um die Ergebnisse anzuzeigen. Die Ansicht enthält standardmäßig eine einzelne Abfrage, und Sie können **+ Abfrage** wählen, um zusätzliche Abfragen hinzuzufügen.

![Ansicht der Liste der Abfragen](media/view-designer-parts/view-list-queries.png)

| Einstellung | BESCHREIBUNG |
|:--- |:--- |
| **Allgemein** | |
| Titel |Der Text, der im oberen Bereich der Ansicht angezeigt wird. |
| Neue Gruppe |Wählen Sie diesen Link aus, um in der Ansicht auf Grundlage der aktuellen Ansicht eine neue Gruppe zu erstellen. |
| Vorab ausgewählte Filter |Eine durch Trennzeichen getrennte Liste von Eigenschaften, die in den linken Filterbereich aufgenommen werden sollen, wenn Sie eine Abfrage auswählen. |
| Rendermodus |Die anfängliche Ansicht, die bei Auswahl der Abfrage angezeigt wird. Sie können nach dem Öffnen der Abfrage alle verfügbaren Ansichten auswählen. |
| **Abfragen** | |
| Suchabfrage |Dies ist die auszuführende Abfrage. |
| Anzeigename | Der beschreibende Name, der angezeigt wird. |

## <a name="number-and-list-part"></a>Teil mit Zahl und Liste
Die Kopfzeile zeigt eine einzelne Zahl an, die die Anzahl der Datensätze aus einer Protokollabfrage angibt. Die Liste zeigt die ersten zehn Ergebnisse aus einer Abfrage an. Ein Diagramm gibt dabei den relativen Wert einer numerischen Spalte oder die Änderung des Werts im Verlauf.

![Ansicht der Liste der Abfragen](media/view-designer-parts/view-number-list.png)

| Einstellung | BESCHREIBUNG |
|:--- |:--- |
| **Allgemein** | |
| Gruppentitel |Der Text, der im oberen Bereich der Ansicht angezeigt wird. |
| Neue Gruppe |Wählen Sie diesen Link aus, um in der Ansicht auf Grundlage der aktuellen Ansicht eine neue Gruppe zu erstellen. |
| Symbol |Die Bilddatei, die neben dem Ergebnis in der Kopfzeile angezeigt wird. |
| Symbol verwenden |Wählen Sie diesen Link, um das Symbol anzuzeigen. |
| **Titel** | |
| Legende |Der Text, der oben in der Kopfzeile angezeigt wird. |
| Abfragen |Die Abfrage, die für die Kopfzeile ausgeführt wird. Die Anzahl von Datensätzen wird angezeigt, die von der Abfrage zurückgegeben werden. |
| Click-through navigation (Navigation per Klick) | Aktion, die durchgeführt wird, wenn Sie auf den Header klicken.  Weitere Informationen finden Sie unter [Allgemeine Einstellungen](#click-through-navigation). |
| **Liste** | |
| Abfragen |Die Abfrage, die für die Liste ausgeführt wird. Die ersten beiden Eigenschaften für die ersten zehn Datensätze in den Ergebnissen werden angezeigt. Die erste Eigenschaft ist ein Textwert, die zweite Eigenschaft ist ein numerischer Wert. Balken werden automatisch basierend auf dem relativen Wert der numerischen Spalte erstellt.<br><br>Verwenden Sie den Befehl `Sort` in der Abfrage, um die Datensätze in der Liste zu sortieren. Sie können **Alle anzeigen** auswählen, um die Abfrage auszuführen und alle Datensätze zurückzugeben. |
| Diagramm ausblenden |Wählen Sie diesen Link, um das Diagramm rechts neben der numerischen Spalte zu deaktivieren. |
| Sparklines aktivieren |Wählen Sie diesen Link, um eine Sparkline anstatt eines horizontalen Balkens anzuzeigen. Weitere Informationen finden Sie unter [Allgemeine Einstellungen](#sparklines). |
| Farbe |Die Farbe der Balken oder Sparklines. |
| Trennlinie für Name und Wert |Ein Trennzeichen, um die Texteigenschaft in mehrere Werte zu gliedern. Weitere Informationen finden Sie unter [Allgemeine Einstellungen](#sparklines). |
| Click-through navigation (Navigation per Klick) | Aktion, die durchgeführt wird, wenn Sie in der Liste auf einen Eintrag klicken.  Weitere Informationen finden Sie unter [Allgemeine Einstellungen](#click-through-navigation). |
| **Liste** |**> Spaltentitel** |
| NAME |Der Text, der oben in der ersten Spalte angezeigt wird. |
| Wert |Der Text, der oben in der zweiten Spalte angezeigt wird. |
| **Liste** |**&gt; Schwellenwerte** |
| Schwellenwerte aktivieren |Wählen Sie diesen Link, um Schwellenwerte zu aktivieren. Weitere Informationen finden Sie unter [Allgemeine Einstellungen](#thresholds). |

## <a name="two-numbers-and-list-part"></a>Teil mit zwei Zahlen und Liste
Die Kopfzeile zeigt zwei Zahlen an, die die Anzahl der Datensätze aus separaten Protokollabfragen angeben. Die Liste zeigt die ersten zehn Ergebnisse aus einer Abfrage an. Ein Diagramm gibt dabei den relativen Wert einer numerischen Spalte oder die Änderung des Werts im Verlauf.

![Ansicht mit zwei Zahlen und Liste](media/view-designer-parts/view-two-numbers-list.png)

| Einstellung | BESCHREIBUNG |
|:--- |:--- |
| **Allgemein** | |
| Gruppentitel |Der Text, der im oberen Bereich der Ansicht angezeigt wird. |
| Neue Gruppe |Wählen Sie diesen Link aus, um in der Ansicht auf Grundlage der aktuellen Ansicht eine neue Gruppe zu erstellen. |
| Symbol |Die Bilddatei, die neben dem Ergebnis in der Kopfzeile angezeigt wird. |
| Symbol verwenden |Wählen Sie diesen Link, um das Symbol anzuzeigen. |
| **Titelnavigation** | |
| Click-through navigation (Navigation per Klick) | Aktion, die durchgeführt wird, wenn Sie auf den Header klicken.  Weitere Informationen finden Sie unter [Allgemeine Einstellungen](#click-through-navigation). |
| **Titel** | |
| Legende |Der Text, der oben in der Kopfzeile angezeigt wird. |
| Abfragen |Die Abfrage, die für die Kopfzeile ausgeführt wird. Die Anzahl von Datensätzen wird angezeigt, die von der Abfrage zurückgegeben werden. |
| **Liste** | |
| Abfragen |Die Abfrage, die für die Liste ausgeführt wird. Die ersten beiden Eigenschaften für die ersten zehn Datensätze in den Ergebnissen werden angezeigt. Die erste Eigenschaft ist ein Textwert, die zweite Eigenschaft ist ein numerischer Wert. Balken werden automatisch basierend auf dem relativen Wert der numerischen Spalte erstellt.<br><br>Verwenden Sie den Befehl `Sort` in der Abfrage, um die Datensätze in der Liste zu sortieren. Sie können **Alle anzeigen** auswählen, um die Abfrage auszuführen und alle Datensätze zurückzugeben. |
| Diagramm ausblenden |Wählen Sie diesen Link, um das Diagramm rechts neben der numerischen Spalte zu deaktivieren. |
| Sparklines aktivieren |Wählen Sie diesen Link, um eine Sparkline anstatt eines horizontalen Balkens anzuzeigen. Weitere Informationen finden Sie unter [Allgemeine Einstellungen](#sparklines). |
| Farbe |Die Farbe der Balken oder Sparklines. |
| Vorgang |Der Vorgang, der für die Sparkline ausgeführt werden soll. Weitere Informationen finden Sie unter [Allgemeine Einstellungen](#sparklines). |
| Trennlinie für Name und Wert |Ein Trennzeichen, um die Texteigenschaft in mehrere Werte zu gliedern. Weitere Informationen finden Sie unter [Allgemeine Einstellungen](#sparklines). |
| Click-through navigation (Navigation per Klick) | Aktion, die durchgeführt wird, wenn Sie in der Liste auf einen Eintrag klicken.  Weitere Informationen finden Sie unter [Allgemeine Einstellungen](#click-through-navigation). |
| **Liste** |**> Spaltentitel** |
| NAME |Der Text, der oben in der ersten Spalte angezeigt wird. |
| Wert |Der Text, der oben in der zweiten Spalte angezeigt wird. |
| **Liste** |**&gt; Schwellenwerte** |
| Schwellenwerte aktivieren |Wählen Sie diesen Link, um Schwellenwerte zu aktivieren. Weitere Informationen finden Sie unter [Allgemeine Einstellungen](#thresholds). |

## <a name="donut-and-list-part"></a>Teil mit Ringdiagramm und Liste
Die Kopfzeile zeigt eine einzelne Zahl, die eine Wertspalte in einer Protokollabfrage zusammenfasst. Der Ring stellt die Ergebnisse der ersten drei Datensätze grafisch dar.

![Ansicht mit Ringdiagramm und Liste](media/view-designer-parts/view-donut-list.png)

| Einstellung | BESCHREIBUNG |
|:--- |:--- |
| **Allgemein** | |
| Gruppentitel |Der Text, der im oberen Bereich der Kachel angezeigt wird. |
| Neue Gruppe |Wählen Sie diesen Link aus, um in der Ansicht auf Grundlage der aktuellen Ansicht eine neue Gruppe zu erstellen. |
| Symbol |Die Bilddatei, die neben dem Ergebnis in der Kopfzeile angezeigt wird. |
| Symbol verwenden |Wählen Sie diesen Link, um das Symbol anzuzeigen. |
| **Kopfzeile** | |
| Titel |Der Text, der oben in der Kopfzeile angezeigt wird. |
| Untertitel |Der Text, der unter dem Titel oben in der Kopfzeile angezeigt wird. |
| **Ring** | |
| Abfragen |Die Abfrage, die für das Ringdiagramm ausgeführt wird. Die erste Eigenschaft ist ein Textwert, die zweite Eigenschaft ist ein numerischer Wert. |
| Click-through navigation (Navigation per Klick) | Aktion, die durchgeführt wird, wenn Sie auf den Header klicken.  Weitere Informationen finden Sie unter [Allgemeine Einstellungen](#click-through-navigation). |
| **Ring** |**&gt; Mitte** |
| Text |Der Text, der unter dem Wert innerhalb des Ringdiagramms angezeigt wird. |
| Vorgang |Der Vorgang, der für die Werteigenschaft ausgeführt wird, um sie als Einzelwert zusammenzufassen.<ul><li>Summe: Addiert die Werte aller Datensätze.</li><li>Prozentsatz: Das Verhältnis der Datensätze, die von den Werten in **Vom Vorgang in der Mitte verwendete Ergebniswerte** zurückgegeben werden, zu der Gesamtanzahl der Datensätze in der Abfrage.</li></ul> |
| Vom Vorgang in der Mitte verwendete Ergebniswerte |Klicken Sie optional auf das Pluszeichen (+), um einen oder mehrere Werte hinzuzufügen. Die Ergebnisse der Abfrage werden auf Datensätze mit den von Ihnen angegebenen Eigenschaftswerten beschränkt. Wenn keine Werte hinzugefügt werden, werden alle Datensätze in die Abfrage aufgenommen. |
| **Weitere Optionen** |**&gt; Farben** |
| Farbe 1<br>Farbe 2<br>Farbe 3 |Wählen Sie die Farbe für jeden der im Ring angezeigten Werte aus. |
| **Weitere Optionen** |**> Erweiterte Farbzuordnung** |
| Feldwert |Geben Sie den Namen eines Felds ein, um es in einer anderen Farbe anzuzeigen, wenn es im Ring enthalten ist. |
| Farbe |Wählen Sie die Farbe für das eindeutige Feld aus. |
| **Liste** | |
| Abfragen |Die Abfrage, die für die Liste ausgeführt wird. Die Anzahl von Datensätzen wird angezeigt, die von der Abfrage zurückgegeben werden. |
| Diagramm ausblenden |Wählen Sie diesen Link, um das Diagramm rechts neben der numerischen Spalte zu deaktivieren. |
| Sparklines aktivieren |Wählen Sie diesen Link, um eine Sparkline anstatt eines horizontalen Balkens anzuzeigen. Weitere Informationen finden Sie unter [Allgemeine Einstellungen](#sparklines). |
| Farbe |Die Farbe der Balken oder Sparklines. |
| Vorgang |Der Vorgang, der für die Sparkline ausgeführt werden soll. Weitere Informationen finden Sie unter [Allgemeine Einstellungen](#sparklines). |
| Trennlinie für Name und Wert |Ein Trennzeichen, um die Texteigenschaft in mehrere Werte zu gliedern. Weitere Informationen finden Sie unter [Allgemeine Einstellungen](#sparklines). |
| Click-through navigation (Navigation per Klick) | Aktion, die durchgeführt wird, wenn Sie in der Liste auf einen Eintrag klicken.  Weitere Informationen finden Sie unter [Allgemeine Einstellungen](#click-through-navigation). |
| **Liste** |**> Spaltentitel** |
| NAME |Der Text, der oben in der ersten Spalte angezeigt wird. |
| Wert |Der Text, der oben in der zweiten Spalte angezeigt wird. |
| **Liste** |**&gt; Schwellenwerte** |
| Schwellenwerte aktivieren |Wählen Sie diesen Link, um Schwellenwerte zu aktivieren. Weitere Informationen finden Sie unter [Allgemeine Einstellungen](#thresholds). |

## <a name="two-timelines-and-list-part"></a>Teil mit zwei Zeitachsen und Liste
Die Kopfzeile zeigt die Ergebnisse von zwei Protokollabfragen im Verlauf als Säulendiagramme an. In einer Legende wird eine einzelne Zahl dargestellt, die eine Wertspalte in einer Protokollabfrage zusammenfasst. Die Liste zeigt die ersten zehn Ergebnisse aus einer Abfrage an. Ein Diagramm gibt dabei den relativen Wert einer numerischen Spalte oder die Änderung des Werts im Verlauf.

![Ansicht mit zwei Zeitachsen und Liste](media/view-designer-parts/view-two-timelines-list.png)

| Einstellung | BESCHREIBUNG |
|:--- |:--- |
| **Allgemein** | |
| Gruppentitel |Der Text, der im oberen Bereich der Kachel angezeigt wird. |
| Neue Gruppe |Wählen Sie diesen Link aus, um in der Ansicht auf Grundlage der aktuellen Ansicht eine neue Gruppe zu erstellen. |
| Symbol |Die Bilddatei, die neben dem Ergebnis in der Kopfzeile angezeigt wird. |
| Symbol verwenden |Wählen Sie diesen Link, um das Symbol anzuzeigen. |
| **Titelnavigation** | |
| Click-through navigation (Navigation per Klick) | Aktion, die durchgeführt wird, wenn Sie auf den Header klicken.  Weitere Informationen finden Sie unter [Allgemeine Einstellungen](#click-through-navigation). |
| **Erstes Diagramm<br>Zweites Diagramm** | |
| Legende |Der Text, der unter der Legende für die erste Datenreihe angezeigt wird. |
| Farbe |Die Farbe, die für die Säulen in der Reihe verwendet wird. |
| Abfragen |Die Abfrage, die für die erste Reihe ausgeführt wird. Die Anzahl von Datensätzen in jedem Zeitintervall wird durch die Säulen im Diagramm dargestellt. |
| Vorgang |Der Vorgang, der für die Werteigenschaft ausgeführt wird, um sie für die Legende als Einzelwert zusammenzufassen.<ul><li>Summe: Die Summe der Werte aller Datensätze.</li><li>Durchschnitt: Der Durchschnitt der Werte aller Datensätze.</li><li>Letztes Beispiel: Der Wert aus dem letzten Intervall im Diagramm.</li><li>Letztes Beispiel: Der Wert aus dem ersten Intervall im Diagramm.</li><li>Anzahl: Die Anzahl aller Datensätze, die von der Abfrage zurückgegeben werden.</li></ul> |
| **Liste** | |
| Abfragen |Die Abfrage, die für die Liste ausgeführt wird. Die Anzahl von Datensätzen wird angezeigt, die von der Abfrage zurückgegeben werden. |
| Diagramm ausblenden |Wählen Sie diesen Link, um das Diagramm rechts neben der numerischen Spalte zu deaktivieren. |
| Sparklines aktivieren |Wählen Sie diesen Link, um eine Sparkline anstatt eines horizontalen Balkens anzuzeigen. Weitere Informationen finden Sie unter [Allgemeine Einstellungen](#sparklines). |
| Farbe |Die Farbe der Balken oder Sparklines. |
| Vorgang |Der Vorgang, der für die Sparkline ausgeführt werden soll. Weitere Informationen finden Sie unter [Allgemeine Einstellungen](#sparklines). |
| Click-through navigation (Navigation per Klick) | Aktion, die durchgeführt wird, wenn Sie in der Liste auf einen Eintrag klicken.  Weitere Informationen finden Sie unter [Allgemeine Einstellungen](#click-through-navigation). |
| **Liste** |**> Spaltentitel** |
| NAME |Der Text, der oben in der ersten Spalte angezeigt wird. |
| Wert |Der Text, der oben in der zweiten Spalte angezeigt wird. |
| **Liste** |**&gt; Schwellenwerte** |
| Schwellenwerte aktivieren |Wählen Sie diesen Link, um Schwellenwerte zu aktivieren. Weitere Informationen finden Sie unter [Allgemeine Einstellungen](#thresholds). |

## <a name="information-part"></a>Komponente mit Informationen
Die Kopfzeile zeigt statischen Text und einen optionalen Link. Die Liste zeigt mindestens ein Element mit einer statischen Kachel und Text.

![Ansicht mit Informationen](media/view-designer-parts/view-information.png)

| Einstellung | BESCHREIBUNG |
|:--- |:--- |
| **Allgemein** | |
| Gruppentitel |Der Text, der im oberen Bereich der Kachel angezeigt wird. |
| Neue Gruppe |Wählen Sie diesen Link aus, um in der Ansicht auf Grundlage der aktuellen Ansicht eine neue Gruppe zu erstellen. |
| Farbe |Die Hintergrundfarbe der Kopfzeile. |
| **Kopfzeile** | |
| Image |Die Bilddatei, die in der Kopfzeile angezeigt wird. |
| Bezeichnung |Der Text, der in der Kopfzeile angezeigt wird. |
| **Kopfzeile** |**&gt; Link** |
| Bezeichnung |Der Text des Links |
| url |Die URL für den Link |
| **Informationselemente** | |
| Titel |Der Text, der für den Titel der einzelnen Elemente angezeigt wird. |
| Inhalt |Der Text, der für die einzelnen Elemente angezeigt wird. |

## <a name="line-chart-callout-and-list-part"></a>Teil mit Liniendiagramm, Legende und Liste
Die Kopfzeile zeigt ein Liniendiagramm mit mehreren Reihen aus einer Protokollabfrage im Verlauf und eine Legende mit einem zusammengefassten Wert an. Die Liste zeigt die ersten zehn Ergebnisse aus einer Abfrage an. Ein Diagramm gibt dabei den relativen Wert einer numerischen Spalte oder die Änderung des Werts im Verlauf.

![Ansicht mit Liniendiagramm, Legende und Liste](media/view-designer-parts/view-line-chart-callout-list.png)

| Einstellung | BESCHREIBUNG |
|:--- |:--- |
| **Allgemein** | |
| Gruppentitel |Der Text, der im oberen Bereich der Kachel angezeigt wird. |
| Neue Gruppe |Wählen Sie diesen Link aus, um in der Ansicht auf Grundlage der aktuellen Ansicht eine neue Gruppe zu erstellen. |
| Symbol |Die Bilddatei, die neben dem Ergebnis in der Kopfzeile angezeigt wird. |
| Symbol verwenden |Wählen Sie diesen Link, um das Symbol anzuzeigen. |
| **Kopfzeile** | |
| Titel |Der Text, der oben in der Kopfzeile angezeigt wird. |
| Untertitel |Der Text, der unter dem Titel oben in der Kopfzeile angezeigt wird. |
| **Liniendiagramm** | |
| Abfragen |Die Abfrage, die für das Liniendiagramm ausgeführt wird. Die erste Eigenschaft ist ein Textwert, die zweite Eigenschaft ist ein numerischer Wert. Bei dieser Abfrage wird üblicherweise das Schlüsselwort *measure* verwendet, um die Ergebnisse zusammenzufassen. Wenn in der Abfrage das Schlüsselwort *interval* verwendet wird, wird für die X-Achse des Diagramms dieses Zeitintervall verwendet. Wenn in der Abfrage das Schlüsselwort *interval* nicht verwendet wird, wird für die X-Achse ein Stundenintervall verwendet. |
| Click-through navigation (Navigation per Klick) | Aktion, die durchgeführt wird, wenn Sie auf den Header klicken.  Weitere Informationen finden Sie unter [Allgemeine Einstellungen](#click-through-navigation). |
| **Liniendiagramm** |**&gt; Legende** |
| Legendentitel |Der Text, der über dem Legendenwert angezeigt wird. |
| Reihenname |Eigenschaftswert für die Reihe, die für den Wert der Legende verwendet wird. Wenn keine Reihe angegeben wird, werden alle Datensätze der Abfrage verwendet. |
| Vorgang |Der Vorgang, der für die Werteigenschaft ausgeführt wird, um sie für die Legende als Einzelwert zusammenzufassen.<ul><li>Durchschnitt: Der Durchschnitt der Werte aller Datensätze.</li><li>Anzahl: Die Anzahl aller Datensätze, die von der Abfrage zurückgegeben werden.</li><li>Letztes Beispiel: Der Wert aus dem letzten Intervall im Diagramm.</li><li>Max: Der Höchstwert aus den Intervallen im Diagramm.</li><li>Min: Der Mindestwert aus den Intervallen im Diagramm.</li><li>Summe: Die Summe der Werte aller Datensätze.</li></ul> |
| **Liniendiagramm** |**> Y-Achse** |
| Logarithmische Skala verwenden |Wählen Sie diesen Link aus, um eine logarithmische Skala für die Y-Achse zu verwenden. |
| Units |Geben Sie die Einheiten für die von der Abfrage zurückgegebenen Werte an. Diese Informationen werden zur Anzeige von Bezeichnungen im Diagramm verwendet, die die Werttypen angeben, und um optional Werte zu konvertieren. Der Typ der *Einheit* gibt die Kategorie der Einheit an und definiert die verfügbaren Werte für den Typ in *Aktuelle Einheit*. Bei Auswahl eines Werts in *Konvertieren in* werden die numerischen Werte vom Typ in *Aktuelle Einheit* in den Typ in *Konvertieren in* konvertiert. |
| Benutzerdefinierte Bezeichnung |Der Text, der für die Y-Achse neben der Bezeichnung für den Typ der *Einheit* angezeigt wird. Wenn keine Bezeichnung angegeben ist, wird nur der Typ der *Einheit* angezeigt. |
| **Liste** | |
| Abfragen |Die Abfrage, die für die Liste ausgeführt wird. Die Anzahl von Datensätzen wird angezeigt, die von der Abfrage zurückgegeben werden. |
| Diagramm ausblenden |Wählen Sie diesen Link, um das Diagramm rechts neben der numerischen Spalte zu deaktivieren. |
| Sparklines aktivieren |Wählen Sie diesen Link, um eine Sparkline anstatt eines horizontalen Balkens anzuzeigen. Weitere Informationen finden Sie unter [Allgemeine Einstellungen](#sparklines). |
| Farbe |Die Farbe der Balken oder Sparklines. |
| Vorgang |Der Vorgang, der für die Sparkline ausgeführt werden soll. Weitere Informationen finden Sie unter [Allgemeine Einstellungen](#sparklines). |
| Trennlinie für Name und Wert |Ein Trennzeichen, um die Texteigenschaft in mehrere Werte zu gliedern. Weitere Informationen finden Sie unter [Allgemeine Einstellungen](#sparklines). |
| Click-through navigation (Navigation per Klick) | Aktion, die durchgeführt wird, wenn Sie in der Liste auf einen Eintrag klicken.  Weitere Informationen finden Sie unter [Allgemeine Einstellungen](#click-through-navigation). |
| **Liste** |**> Spaltentitel** |
| NAME |Der Text, der oben in der ersten Spalte angezeigt wird. |
| Wert |Der Text, der oben in der zweiten Spalte angezeigt wird. |
| **Liste** |**&gt; Schwellenwerte** |
| Schwellenwerte aktivieren |Wählen Sie diesen Link, um Schwellenwerte zu aktivieren. Weitere Informationen finden Sie unter [Allgemeine Einstellungen](#thresholds). |

## <a name="line-chart-and-list-part"></a>Teil mit Liniendiagramm und Liste
Die Kopfzeile zeigt ein Liniendiagramm mit mehreren Reihen aus einer Protokollabfrage im Verlauf an. Die Liste zeigt die ersten zehn Ergebnisse aus einer Abfrage an. Ein Diagramm gibt dabei den relativen Wert einer numerischen Spalte oder die Änderung des Werts im Verlauf.

![Ansicht mit Liniendiagramm und Liste](media/view-designer-parts/view-line-chart-callout-list.png)

| Einstellung | BESCHREIBUNG |
|:--- |:--- |
| **Allgemein** | |
| Gruppentitel |Der Text, der im oberen Bereich der Kachel angezeigt wird. |
| Neue Gruppe |Wählen Sie diesen Link aus, um in der Ansicht auf Grundlage der aktuellen Ansicht eine neue Gruppe zu erstellen. |
| Symbol |Die Bilddatei, die neben dem Ergebnis in der Kopfzeile angezeigt wird. |
| Symbol verwenden |Wählen Sie diesen Link, um das Symbol anzuzeigen. |
| **Kopfzeile** | |
| Titel |Der Text, der oben in der Kopfzeile angezeigt wird. |
| Untertitel |Der Text, der unter dem Titel oben in der Kopfzeile angezeigt wird. |
| **Liniendiagramm** | |
| Abfragen |Die Abfrage, die für das Liniendiagramm ausgeführt wird. Die erste Eigenschaft ist ein Textwert, die zweite Eigenschaft ist ein numerischer Wert. Bei dieser Abfrage wird üblicherweise das Schlüsselwort *measure* verwendet, um die Ergebnisse zusammenzufassen. Wenn in der Abfrage das Schlüsselwort *interval* verwendet wird, wird für die X-Achse des Diagramms dieses Zeitintervall verwendet. Wenn in der Abfrage das Schlüsselwort *interval* nicht verwendet wird, wird für die X-Achse ein Stundenintervall verwendet. |
| Click-through navigation (Navigation per Klick) | Aktion, die durchgeführt wird, wenn Sie auf den Header klicken.  Weitere Informationen finden Sie unter [Allgemeine Einstellungen](#click-through-navigation). |
| **Liniendiagramm** |**> Y-Achse** |
| Logarithmische Skala verwenden |Wählen Sie diesen Link aus, um eine logarithmische Skala für die Y-Achse zu verwenden. |
| Units |Geben Sie die Einheiten für die von der Abfrage zurückgegebenen Werte an. Diese Informationen werden zur Anzeige von Bezeichnungen im Diagramm verwendet, die die Werttypen angeben, und um optional Werte zu konvertieren. Der Typ der *Einheit* gibt die Kategorie der Einheit an und definiert die verfügbaren Werte für den Typ in *Aktuelle Einheit*. Bei Auswahl eines Werts in *Konvertieren in* werden die numerischen Werte vom Typ in *Aktuelle Einheit* in den Typ in *Konvertieren in* konvertiert. |
| Benutzerdefinierte Bezeichnung |Der Text, der für die Y-Achse neben der Bezeichnung für den Typ der *Einheit* angezeigt wird. Wenn keine Bezeichnung angegeben ist, wird nur der Typ der *Einheit* angezeigt. |
| **Liste** | |
| Abfragen |Die Abfrage, die für die Liste ausgeführt wird. Die Anzahl von Datensätzen wird angezeigt, die von der Abfrage zurückgegeben werden. |
| Diagramm ausblenden |Wählen Sie diesen Link, um das Diagramm rechts neben der numerischen Spalte zu deaktivieren. |
| Sparklines aktivieren |Wählen Sie diesen Link, um eine Sparkline anstatt eines horizontalen Balkens anzuzeigen. Weitere Informationen finden Sie unter [Allgemeine Einstellungen](#sparklines). |
| Farbe |Die Farbe der Balken oder Sparklines. |
| Vorgang |Der Vorgang, der für die Sparkline ausgeführt werden soll. Weitere Informationen finden Sie unter [Allgemeine Einstellungen](#sparklines). |
| Trennlinie für Name und Wert |Ein Trennzeichen, um die Texteigenschaft in mehrere Werte zu gliedern. Weitere Informationen finden Sie unter [Allgemeine Einstellungen](#sparklines). |
| Click-through navigation (Navigation per Klick) | Aktion, die durchgeführt wird, wenn Sie in der Liste auf einen Eintrag klicken.  Weitere Informationen finden Sie unter [Allgemeine Einstellungen](#click-through-navigation). |
| **Liste** |**> Spaltentitel** |
| NAME |Der Text, der oben in der ersten Spalte angezeigt wird. |
| Wert |Der Text, der oben in der zweiten Spalte angezeigt wird. |
| **Liste** |**&gt; Schwellenwerte** |
| Schwellenwerte aktivieren |Wählen Sie diesen Link, um Schwellenwerte zu aktivieren. Weitere Informationen finden Sie unter [Allgemeine Einstellungen](#thresholds). |

## <a name="stack-of-line-charts-part"></a>Komponente mit Liniendiagrammstapel
Der Liniendiagrammstapel zeigt drei separate Liniendiagramme mit mehreren Reihen aus einer Protokollabfrage im Verlauf an, wie hier dargestellt:

![Liniendiagrammstapel](media/view-designer-parts/view-stack-line-charts.png)

| Einstellung | BESCHREIBUNG |
|:--- |:--- |
| **Allgemein** | |
| Gruppentitel |Der Text, der im oberen Bereich der Kachel angezeigt wird. |
| Neue Gruppe |Wählen Sie diesen Link aus, um in der Ansicht auf Grundlage der aktuellen Ansicht eine neue Gruppe zu erstellen. |
| Symbol |Die Bilddatei, die neben dem Ergebnis in der Kopfzeile angezeigt wird. |
| **Diagramm 1<br>Diagramm 2<br>Diagramm 3** |**&gt; Kopfzeile** |
| Titel |Der Text, der im oberen Bereich des Diagramms angezeigt wird. |
| Untertitel |Der Text, der unter dem Titel oben im Diagramm angezeigt wird. |
| **Diagramm 1<br>Diagramm 2<br>Diagramm 3** |**Liniendiagramm** |
| Abfragen |Die Abfrage, die für das Liniendiagramm ausgeführt wird. Die erste Eigenschaft ist ein Textwert, die zweite Eigenschaft ist ein numerischer Wert. Bei dieser Abfrage wird üblicherweise das Schlüsselwort *measure* verwendet, um die Ergebnisse zusammenzufassen. Wenn in der Abfrage das Schlüsselwort *interval* verwendet wird, wird für die X-Achse des Diagramms dieses Zeitintervall verwendet. Wenn in der Abfrage das Schlüsselwort *interval* nicht verwendet wird, wird für die X-Achse ein Stundenintervall verwendet. |
| Click-through navigation (Navigation per Klick) | Aktion, die durchgeführt wird, wenn Sie auf den Header klicken.  Weitere Informationen finden Sie unter [Allgemeine Einstellungen](#click-through-navigation). |
| **Diagramm** |**> Y-Achse** |
| Logarithmische Skala verwenden |Wählen Sie diesen Link aus, um eine logarithmische Skala für die Y-Achse zu verwenden. |
| Units |Geben Sie die Einheiten für die von der Abfrage zurückgegebenen Werte an. Diese Informationen werden zur Anzeige von Bezeichnungen im Diagramm verwendet, die die Werttypen angeben, und um optional Werte zu konvertieren. Der Typ der *Einheit* gibt die Kategorie der Einheit an und definiert die verfügbaren Werte für den Typ in *Aktuelle Einheit*. Bei Auswahl eines Werts in *Konvertieren in* werden die numerischen Werte vom Typ in *Aktuelle Einheit* in den Typ in *Konvertieren in* konvertiert. |
| Benutzerdefinierte Bezeichnung |Der Text, der für die Y-Achse neben der Bezeichnung für den Typ der *Einheit* angezeigt wird. Wenn keine Bezeichnung angegeben ist, wird nur der Typ der *Einheit* angezeigt. |

## <a name="common-settings"></a>Allgemeine Einstellungen
In den folgenden Abschnitten werden die Einstellungen beschrieben, die für verschiedene Visualisierungsteile gelten.

### <a name="name-value-separator"></a>Trennlinie für Name und Wert
Die Trennlinie für Name und Wert ist ein einstelliges Trennzeichen, um die Texteigenschaft aus einer Listenabfrage in mehrere Werte zu gliedern. Wenn Sie ein Trennzeichen angeben, können Sie Namen für die einzelnen Felder getrennt durch dieses Trennzeichen im Feld **Name** bereitstellen.

Beispiel: Eine Eigenschaft namens *Standort* weist Werte wie *Redmond-Gebäude 41* und *Bellevue-Gebäude 12* auf. Sie können eine Bindestrich (-) als Trennlinie für Name und Wert festlegen und *Ort-Gebäude* als Namen angeben. Durch diesen Ansatz wird jeder Wert in die beiden Eigenschaften *Ort* und *Gebäude* gliedert.

### <a name="click-through-navigation"></a>Click-through navigation (Navigation per Klick)
Mit der Navigation per Klick wird definiert, welche Aktion durchgeführt wird, wenn Sie in einer Ansicht auf einen Header oder einen Listeneintrag klicken.  Hierdurch wird entweder eine Abfrage in [Log Analytics](../../azure-monitor/log-query/portals.md) geöffnet oder eine andere Ansicht gestartet.

In der folgenden Tabelle sind die Einstellungen für die Navigation per Klick beschrieben.

| Einstellung           | BESCHREIBUNG |
|:--|:--|
| Protokollsuche (Automatisch) | Protokollabfrage, die durchgeführt wird, wenn Sie ein Headerelement auswählen.  Dies ist die gleiche Protokollabfrage, auf der das Element basiert.
| Protokollsuche        | Die Protokollabfrage, die durchgeführt wird, wenn Sie in einer Liste einen Eintrag auswählen.  Geben Sie die Abfrage im Feld **Navigationsabfrage** ein.   Verwenden Sie *{ausgewähltes Element}* , um die Syntax für das Element einzubinden, das der Benutzer ausgewählt hat.  Beispiel: Wenn die Abfrage eine Spalte namens *Computer* aufweist und die Navigationsabfrage *{ausgewähltes Element}* ist, wird eine Abfrage wie *Computer="MeinComputer"* ausgeführt, wenn der Benutzer einen Computer auswählt. Wenn die Navigationsabfrage *Type=Event {ausgewähltes Element}* ist, wird *Type=Event Computer="MeinComputer"* ausgeführt. |
| Sicht              | Die zu öffnende Ansicht, wenn Sie ein Headerelement oder einen Eintrag einer Liste auswählen.  Wählen Sie den Namen einer Ansicht Ihres Arbeitsbereichs im Feld **Ansichtsname** aus. |



### <a name="sparklines"></a>Sparklines
Eine Sparkline ist ein kleines Liniendiagramm, das den Wert eines Listeneintrags im Lauf der Zeit veranschaulicht. Bei Visualisierungsteilen mit einer Liste können Sie auswählen, ob ein horizontaler Balken mit dem relativen Wert einer numerischen Spalte oder eine Sparkline mit dem Wert im Verlauf angezeigt werden soll.

In der folgenden Tabelle sind Einstellungen für Sparklines beschrieben:

| Einstellung | BESCHREIBUNG |
|:--- |:--- |
| Sparklines aktivieren |Wählen Sie diesen Link, um eine Sparkline anstatt eines horizontalen Balkens anzuzeigen. |
| Vorgang |Wenn Sparklines aktiviert sind, ist dies der Vorgang, der für jede Eigenschaft in der Liste ausgeführt wird, um die Werte für die Sparkline zu berechnen.<ul><li>Letztes Beispiel: Der letzte Wert für die Reihe im Zeitintervall.</li><li>Max: Der Höchstwert für die Reihe im Zeitintervall.</li><li>Min: Der Mindestwert für die Reihe im Zeitintervall.</li><li>Summe: Die Summe der Werte für die Reihe im Zeitintervall.</li><li>Zusammenfassung: Verwendet den gleichen `measure`-Befehl wie die Abfrage in der Kopfzeile.</li></ul> |

### <a name="thresholds"></a>Schwellenwerte
Mithilfe von Schwellenwerten können Sie ein farbiges Symbol neben jedem Element in einer Liste anzeigen. Schwellenwerte bieten Ihnen einen schnellen visuellen Indikator für Elemente, die einen bestimmten Wert überschreiten oder innerhalb eines bestimmten Bereichs liegen. Sie können beispielsweise folgende Farbgebung festlegen: ein grünes Symbol für Elemente mit akzeptablem Wert, ein gelbes Symbol für Elemente mit einem Wert in einem Bereich, der auf eine Warnung hindeutet, und ein rotes Symbol für Elemente, die einen Fehlerwert überschreiten.

Wenn Sie Schwellenwerte für eine Komponente aktivieren, müssen Sie mindestens einen Schwellenwert angeben. Wenn der Wert eines Elements größer ist als ein Schwellenwert und kleiner als der nächste Schwellenwert, wird die entsprechende Farbe für diesen Wert verwendet. Wenn das Element größer ist als der höchste Schwellenwert, wird eine andere Farbe verwendet. 

Jeder Schwellenwertsatz verfügt über einen Schwellenwert mit dem Wert **Standard**. Wenn keine anderen Werte überschritten werden, wird diese Farbe verwendet. Sie können Schwellenwerte hinzufügen oder entfernen, indem Sie auf die Schaltfläche **Hinzufügen** (+) oder **Löschen** (x) klicken.

In der folgenden Tabelle sind Einstellungen für Schwellenwerte beschrieben:

| Einstellung | BESCHREIBUNG |
|:--- |:--- |
| Schwellenwerte aktivieren |Wählen Sie diesen Link, um ein Farbsymbol links von jedem Wert anzuzeigen. Das Symbol gibt die Integrität des Werts relativ zu den angegebenen Schwellenwerten an. |
| NAME |Der Name des Schwellenwerts |
| Schwellenwert |Der Wert für den Schwellenwert. Die Farbe der Integrität für jedes Listenelement ist auf die Farbe des höchsten Schwellenwerts festgelegt, den der Wert des Listenelements überschritten hat. Wenn keine Schwellenwerte überschritten werden, wird eine Standardfarbe verwendet. |
| Farbe |Die Farbe, die den Schwellenwert angibt. |

## <a name="next-steps"></a>Nächste Schritte
* Erfahren Sie mehr über [Protokollabfragen](../log-query/log-query-overview.md) zur Unterstützung der Abfragen in Visualisierungskomponenten.
