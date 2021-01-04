---
title: Azure Application Insights – Auswirkungen der Nutzung | Microsoft-Dokumentation
description: Analysieren Sie, wie verschiedene Eigenschaften potenziell die Konvertierungsraten für Teile Ihrer Apps beeinflussen.
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 01/08/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 7393c36099795bbc989ae4b690100284d53f08e5
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92678394"
---
# <a name="impact-analysis-with-application-insights"></a>Auswirkungsanalyse mit Application Insights

Impact analysiert, wie Ladezeiten und andere Eigenschaften Konvertierungsraten für verschiedene Teile Ihrer App beeinflussen. Genauer gesagt, wird ermittelt, wie **eine beliebige Dimension** einer **Seitenansicht** , eines **benutzerdefinierten Ereignisses** oder einer **Anforderung** sich auf die Verwendung anderer **Seitenansichten** oder **benutzerdefinierter Ereignisse** auswirkt. 

![Impact-Tool](./media/usage-impact/0001-impact.png)

## <a name="still-not-sure-what-impact-does"></a>Sie wissen noch nicht, was Impact macht?

Betrachten Sie Impact als ultimatives Tool zur Beilegung der Meinungsverschiedenheit mit jemand aus Ihrem Team in der Frage, wie die Langsamkeit Ihrer Website in einem bestimmten Punkt darauf Einfluss hat, ob Benutzer auf der Website bleiben. Benutzer tolerieren Langsamkeit vielleicht in gewissem Maße, aber mit Impact können Sie bestimmen, wie Sie Optimierung und Leistung am besten in Einklang bringen, um die Benutzerkonvertierung zu maximieren.

Die Leistungsanalyse ist jedoch nur ein Teilbereich der Funktionen von Impact. Da Impact benutzerdefinierte Ereignisse und Dimensionen unterstützt, werden Fragen der Art „Wie korreliert die Browserauswahl des Benutzers mit unterschiedlichen Konvertierungsraten?“ mit ein paar Mausklicks beantwortet.

![Screenshot der Konvertierung nach Browsern](./media/usage-impact/0004-browsers.png)

> [!NOTE]
> Ihre Application Insights-Ressource muss Seitenaufrufe oder benutzerdefinierte Ereignisse enthalten, damit das Impact-Tool eingesetzt werden kann. [Informieren Sie sich darüber, wie Sie Ihre App so einrichten können, dass Seitenansichten automatisch mit dem Application Insights-JavaScript-SDK erfasst werden](./javascript.md). Bedenken Sie auch, dass die Beispielgröße von Bedeutung ist, weil Sie die Korrelation analysieren.
>
>

## <a name="is-page-load-time-impacting-how-many-people-convert-on-my-page"></a>Beeinflusst die Seitenladezeit, wie viele Personen zu meiner Seite konvertieren?

Beginnen Sie, Fragen mit dem Impact-Tool zu beantworten, indem Sie eine erste Seitenansicht, ein benutzerdefiniertes Ereignis oder eine Anforderung auswählen.

![Screenshot der Auswahloptionen für eine erste Seitenansicht, ein benutzerdefiniertes Ereignis oder eine Anforderung](./media/usage-impact/0002-dropdown.png)

1. Wählen Sie eine Seitenansicht aus der Dropdownliste **Für die Seitenansicht**.
2. Behalten Sie für die Dropdownliste **Analysieren der Auswirkungen von** die Standardauswahl **Dauer** bei (in diesem Kontext ist **Dauer** ein Alias für **Seitenladezeit** ).
3. Wählen Sie für die Dropdownliste **auf die Verwendung von** ein benutzerdefiniertes Ereignis aus. Dieses Ereignis sollte einem Element der Benutzeroberfläche auf der Seitenansicht entsprechen, die Sie in Schritt 1 ausgewählt haben.

![Screenshot der Ergebnisse](./media/usage-impact/0003-results.png)

In dieser Instanz sinkt mit wachsender Ladezeit der **Produktseite** die Rate der Konvertierung zu **Angeklicktes Produkt kaufen**. Basierend auf der obigen Verteilung könnte eine optimale Seitenladedauer von 3,5 Sekunden angestrebt werden, um eine potenzielle Konvertierungsrate von 55 % zu erreichen. Weitere Leistungsverbesserungen zur Reduzierung der Ladezeit unter 3,5 Sekunden korrelieren derzeit nicht mit zusätzlichen Konvertierungsvorteilen.

## <a name="what-if-im-tracking-page-views-or-load-times-in-custom-ways"></a>Kann ich Seitenansichten oder Ladezeiten auf benutzerdefinierte Weise nachverfolgen?

Impact unterstützt sowohl standardmäßige als auch benutzerdefinierte Eigenschaften und Messungen. Sie haben die Wahl. Verwenden Sie anstelle der Dauer Filter zu primären und sekundären Ereignissen, um genauere Ergebnisse zu erzielen.

## <a name="do-users-from-different-countries-or-regions-convert-at-different-rates"></a>Unterscheiden sich die Konvertierungsraten von Benutzern aus unterschiedlichen Ländern oder Regionen?

1. Wählen Sie eine Seitenansicht aus der Dropdownliste **Für die Seitenansicht**.
2. Wählen Sie in der Dropdownliste **Analysieren der Auswirkungen von** „Land oder Region“.
3. Wählen Sie für die Dropdownliste **auf die Verwendung von** ein benutzerdefiniertes Ereignis aus, das einem Element der Benutzeroberfläche in der Seitenansicht entspricht, die Sie in Schritt 1 ausgewählt haben.

In diesem Fall passen die Ergebnisse nicht mehr wie im ersten Beispiel in ein Modell mit fortlaufender X-Achse. Stattdessen wird eine Visualisierung dargestellt, die einem segmentierten Trichter ähnelt. Sortieren Sie nach **Verwendung** , um die Variation der Konvertierung zu Ihrem benutzerdefinierten Ereignis auf Länder-/Regionsbasis anzuzeigen.


## <a name="how-does-the-impact-tool-calculate-these-conversion-rates"></a>Wie berechnet das Impact-Tool diese Konvertierungsraten?

Im Hintergrund nutzt das Impact-Tool den [Pearson-Korrelationskoeffizienten](https://en.wikipedia.org/wiki/Pearson_correlation_coefficient). Ergebnisse werden zwischen -1 und 1 berechnet, wobei -1 eine negative lineare Korrelation und 1 eine positive lineare Korrelation darstellt.

Die grundlegende Aufschlüsselung der Funktionsweise der Auswirkungsanalyse lautet wie folgt:

_A_ = Hauptseitenansicht/benutzerdefiniertes Ereignis/Anforderung Ihrer Wahl in der ersten Dropdownliste. ( **Für die Seitenansicht** ).

_B_ = sekundäre Seitenansicht/sekundäres benutzerdefiniertes Ereignis Ihrer Wahl ( **auf die Verwendung von** ).

Impact untersucht ein Beispiel für alle Sitzungen von Benutzern in dem ausgewählten Zeitraum. Für jede Sitzung wird nach jedem Auftreten von _A_ gesucht.

Sitzungen werden dann aufgrund einer von zwei Bedingungen in zwei unterschiedliche Arten von _untergeordneten Sitzungen_ unterteilt:

- Eine konvertierte untergeordnete Sitzung besteht aus einer Sitzung, die mit einem _B_ -Ereignis endet, und umfasst alle _A_ -Ereignisse, die vor _B_ auftreten.
- Eine nicht konvertierte untergeordnete Sitzung liegt vor, wenn alle _A_ s ohne ein abschließendes _B_ auftreten.

Wie die Auswirkung letztlich berechnet wird, hängt davon ab, ob wir nach Metrik oder Dimension analysieren. Für Metriken wird für alle _A_ s in einer untergeordneten Sitzung der Mittelwert gebildet. Für Dimensionen hingegen trägt der Wert von jedem einzelnen _A_ _1/N_ zu dem Wert bei, der _B_ zugewiesen wird, wobei _N_ die Anzahl der _A_ s in der untergeordneten Sitzung ist.

## <a name="next-steps"></a>Nächste Schritte

- Um mit der Nutzung zu beginnen, senden Sie [benutzerdefinierte Ereignisse](./api-custom-events-metrics.md#trackevent) oder [Seitenansichten](./api-custom-events-metrics.md#page-views).
- Wenn Sie bereits benutzerdefinierte Ereignisse oder Seitenansichten senden, finden Sie mithilfe der Nutzungstools heraus, wie Benutzer den Dienst verwenden.
    - [Trichter](usage-funnels.md)
    - [Vermerkdauer](usage-retention.md)
    - [Benutzerabläufe](usage-flows.md)
    - [Arbeitsmappen](../platform/workbooks-overview.md)
    - [Hinzufügen von Benutzerkontext](usage-send-user-context.md)

