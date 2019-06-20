---
title: Erste Schritte mit dem Azure-Metrik-Explorer
description: Hier erfahren Sie, wie Sie Ihr erstes Metrikdiagramm mit dem Azure-Metrik-Explorer erstellen.
author: vgorbenko
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: 3306e888970d99132d17d4ccf967f074302412ca
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65595452"
---
# <a name="getting-started-with-azure-metrics-explorer"></a>Erste Schritte mit dem Azure-Metrik-Explorer

## <a name="where-do-i-start"></a>Erste Schritte
Der Azure Monitor-Metrik-Explorer ist eine Komponente des Microsoft Azure-Portals, die das Zeichnen von Diagrammen, das visuelle Korrelieren von Trends und das Untersuchen von Spitzen und Tiefen in Metrikwerten ermöglicht. Verwenden Sie den Metrik-Explorer zum Untersuchen der Integrität und Auslastung Ihrer Ressourcen. Beginnen Sie in der folgenden Reihenfolge:

1. [Wählen Sie eine Ressource und eine Metrik aus](#create-your-first-metric-chart), dann wird Ihnen ein einfaches Diagramm angezeigt. [Wählen Sie anschließend einen Zeitraum aus](#select-a-time-range), der für Ihre Untersuchung relevant ist.

1. Versuchen Sie, [Dimensionsfilter und Aufteilung anzuwenden](#apply-dimension-filters-and-splitting). Mithilfe der Filter und der Aufteilung können Sie analysieren, welche Segmente der Metrik zu dem Gesamtwert der Metrik beitragen und mögliche Ausreißer identifizieren.

1. Verwenden Sie [erweiterte Einstellungen](#advanced-chart-settings), um das Diagramm anzupassen, bevor Sie es an Dashboards anheften. [Konfigurieren Sie Warnungen](alerts-metric-overview.md), um Benachrichtigungen zu erhalten, wenn der Metrikwert einen Schwellenwert überschreitet oder darunter fällt.

## <a name="create-your-first-metric-chart"></a>Erstellen eines ersten Metrikdiagramms

Öffnen Sie die Registerkarte **Metriken**, und führen Sie die folgenden Schritte aus, um ein Metrikdiagramm aus Ihrer Ressource, Ressourcengruppe, Ihrem Abonnement oder einer Azure Monitor-Ansicht zu erstellen:

1. Wählen Sie die Ressource, deren Metriken Sie anzeigen möchten, mithilfe der Ressourcenauswahl aus. (Wenn Sie die Registerkarte **Metriken** über den Kontext einer spezifischen Ressource ausgewählt haben, ist diese Ressource bereits ausgewählt.)

    > ![Auswählen einer Ressource](./media/metrics-getting-started/resource-picker.png)

2. Für einige Ressourcen müssen Sie einen Namespace auswählen. Der Namespace dient lediglich der Organisation der Metriken, damit Sie sich mühelos finden können. Speicherkonten verfügen beispielsweise über separate Namespaces zum Speichern von Metriken zu Dateien, Tabellen, Blobs und Warteschlangen. Viele Ressourcentypen verfügen über nur einen Namespace.

3. Wählen Sie eine Metrik aus der Liste der verfügbaren Metriken aus.

    > ![Metrik auswählen](./media/metrics-getting-started/metric-picker.png)

4. Optional können Sie die Metrikaggregation ändern, wenn Sie beispielsweise möchten, dass Ihr Diagramm nur die minimalen, maximalen oder durchschnittlichen Werte der Metrik anzeigt.

> [!NOTE]
> Klicken Sie auf **Metrik hinzufügen**, und wiederholen Sie diese Schritte, wenn mehrere Metriken im gleichen Diagramm angezeigt werden sollen. Wenn mehrere Diagramme in einer Ansicht angezeigt werden sollen, klicken Sie oben auf **Diagramm hinzufügen**.

## <a name="select-a-time-range"></a>Auswählen eines Zeitbereichs

Das Diagramm zeigt standardmäßig die Metrikdaten der letzten 24 Stunden an. Verwenden Sie den Bereich **Zeitauswahl**, um den Zeitraum, die Vergrößerung oder die Verkleinerung Ihres Diagramms zu ändern. 

![Bereich zum Ändern des Zeitraums](./media/metrics-getting-started/time-picker.png)

## <a name="apply-dimension-filters-and-splitting"></a>Anwenden von Dimensionsfiltern und Aufteilung

[Filtern](metrics-charts.md#apply-filters-to-charts) und [Aufteilen](metrics-charts.md#apply-splitting-to-a-chart) sind leistungsstarke Diagnosetools für Metriken mit Dimensionen. Diese Features zeigen, wie sich verschiedene Metriksegmente („Dimensionswerte“) auf den Gesamtwert der Metrik auswirken und ermöglichen Ihnen, mögliche Ausreißer zu identifizieren.

- Durch das **Filtern** können Sie entscheiden, welche Dimensionswerte im Diagramm enthalten sind. Wenn Sie beispielsweise die erfolgreichen Anforderungen in einem Diagramm der Metrik für die *Serverantwortzeit* veranschaulichen möchten, müssen Sie den Filter auf die Dimension *success of request* (Erfolg der Anforderung) anwenden. 

- Durch das **Aufteilen** können Sie steuern, ob das Diagramm separate Linien für jeden Wert einer Dimension anzeigt oder die Werte in eine Linie zusammenfügt. Beispielsweise können Sie eine Linie für die durchschnittliche Antwortzeit aller Serverinstanzen oder separate Linien für jeden Server anzeigen. In diesem Fall müssten Sie die Aufteilung auf die Dimension *Serverinstanz* anwenden, damit separate Linien angezeigt werden.

In den [Beispielen für Metrikdiagramme](metric-chart-samples.md) können Sie sich Beispiele mit angewandter Filterung und Aufteilung ansehen. Im Artikel werden auch die Schritte für die Konfiguration der Beispieldiagramme erläutert.

## <a name="advanced-chart-settings"></a>Erweiterte Diagrammeinstellungen

Sie können die Formatierung des Diagramms, den Titel und erweiterte Diagrammeinstellungen anpassen. Wenn Sie Ihre Anpassungen vorgenommen haben, heften Sie das Diagramm an ein Dashboard an, um Ihr Werk zu speichern. Sie können auch Metrikwarnungen konfigurieren. Lesen Sie die [Produktdokumentation](metrics-charts.md), um mehr über diese und weitere erweiterte Features des Azure Monitor-Metrik-Explorers zu erfahren.

## <a name="next-steps"></a>Nächste Schritte

* [Erweiterte Funktionen des Metrik-Explorers](metrics-charts.md)
* [Problembehandlung für den Metrik-Explorer](metrics-troubleshoot.md)
* [Unterstützte Metriken von Azure Monitor](metrics-supported.md)
* [Beispiele für Metrikdiagramme](metric-chart-samples.md)
