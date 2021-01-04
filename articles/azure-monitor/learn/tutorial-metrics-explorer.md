---
title: 'Tutorial: Erstellen eines Metrikdiagramms in Azure Monitor'
description: Hier erfahren Sie, wie Sie mit dem Azure-Metrik-Explorer ein Metrikdiagramm erstellen.
author: bwren
ms.author: bwren
ms.subservice: metrics
ms.topic: tutorial
ms.date: 03/09/2020
ms.openlocfilehash: 6ce9183fad50c43ea7070f5fa09e0d445caf89df
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96451245"
---
# <a name="tutorial-create-a-metrics-chart-in-azure-monitor"></a>Tutorial: Erstellen eines Metrikdiagramms in Azure Monitor
Mit dem Metrik-Explorer – einem Azure Monitor-Feature im Azure-Portal – können Sie auf der Grundlage von Metrikwerten Diagramme erstellen, Trends visuell korrelieren sowie Spitzen und Einbrüche bei Metrikwerten untersuchen. Verwenden Sie den Metrik-Explorer, um die Integrität und Auslastung Ihrer Azure-Ressourcen zu untersuchen oder um Diagramme auf der Grundlage benutzerdefinierter Metriken zu zeichnen. 

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Auswählen einer Metrik, für die Sie ein Diagramm zeichnen möchten
> * Durchführen verschiedener Aggregationen von Metrikwerten
> * Ändern des Zeitbereichs und der Granularität für das Diagramm

Im folgenden Video wird ein ausführlicheres Szenario als das in diesem Artikel beschriebene Verfahren gezeigt. Wenn Sie mit Metriken noch nicht vertraut sind, sollten Sie zuerst diesen Artikel lesen und sich dann das Video mit ausführlicheren Informationen ansehen. 

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4qO59]

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial wird eine zu überwachende Azure-Ressource benötigt. Sie können eine beliebige Ressource in Ihrem Azure-Abonnement verwenden, die Metriken unterstützt. Ob eine Ressource Metriken unterstützt, kann wie folgt überprüft werden: Navigieren Sie im Azure-Portal zum Menü der Ressource, und vergewissern Sie sich, dass im Abschnitt **Überwachung** des Menüs eine Option vom Typ **Metrik** vorhanden ist.


## <a name="log-in-to-azure"></a>Anmelden an Azure
Melden Sie sich unter [https://portal.azure.com](https://portal.azure.com) beim Azure-Portal an.

## <a name="open-metrics-explorer-and-select-a-scope"></a>Öffnen des Metrik-Explorers und Auswählen eines Bereichs
Sie können den Metrik-Explorer entweder über das Menü „Azure Monitor“ oder über das Menü einer Ressource im Azure-Portal öffnen. Metriken von allen Ressourcen stehen unabhängig von der verwendeten Option zur Verfügung. 

1. Wählen Sie im Menü **Azure Monitor** oder im Abschnitt **Überwachung** des Menüs einer Ressource die Option **Metriken** aus.

1. Wählen Sie unter **Bereich** die Ressource aus, für die Sie Metriken anzeigen möchten. Wenn Sie den Metrik-Explorer über das Menü einer Ressource geöffnet haben, ist der Bereich bereits angegeben.

    ![Auswählen eines Bereichs](media/tutorial-metrics-explorer/scope-picker.png)

2. Wählen Sie unter **Namespace** einen Namespace aus, falls der Bereich mehrere Namespaces umfasst. Der Namespace dient lediglich der Organisation der Metriken, damit Sie sich mühelos finden können. Speicherkonten verfügen beispielsweise über separate Namespaces zum Speichern von Metriken zu Dateien, Tabellen, Blobs und Warteschlangen. Viele Ressourcentypen verfügen über nur einen Namespace.

3. Wählen Sie für den ausgewählten Bereich und Namespace aus einer Liste mit verfügbaren Metriken eine Metrik aus.

    ![Metrik auswählen](media/tutorial-metrics-explorer/metric-picker.png)

4. Ändern Sie optional unter **Aggregation** die Metrikaggregation. Dadurch wird definiert, wie die Metrikwerte im Rahmen der Zeitgranularität für das Diagramm aggregiert werden. Wenn die Zeitgranularität also beispielsweise auf 15 Minuten und die Aggregation auf „Summe“ festgelegt ist, ist jeder Punkt im Diagramm die Summe aller gesammelten Werte innerhalb des jeweiligen 15-minütigen Segments.

    ![Screenshot: Diagramm mit dem Titel „Sum Ingress for contosoretailweb“ (Eingangssumme für contosoretailweb)](media/tutorial-metrics-explorer/chart.png)

5. Klicken Sie auf **Metrik hinzufügen**, und wiederholen Sie diese Schritte, wenn mehrere Metriken im gleichen Diagramm angezeigt werden sollen. Wählen Sie die Schaltfläche **Neues Diagramm** aus, wenn Sie mehrere Diagramme in einer einzelnen Ansicht erhalten möchten.

## <a name="select-a-time-range-and-granularity"></a>Auswählen von Zeitbereich und -granularität

Das Diagramm zeigt standardmäßig die Metrikdaten der letzten 24 Stunden an. Verwenden Sie die Zeitauswahl, um den **Zeitbereich** für das Diagramm oder die **Zeitgranularität** zu ändern, die den Zeitbereich für jeden Datenpunkt definiert. Das Diagramm verwendet die angegebene Aggregation, um alle als Stichprobe entnommenen Werte innerhalb der angegebenen Zeitgranularität zu berechnen.

![Bereich zum Ändern des Zeitraums](media/tutorial-metrics-explorer/time-picker.png)


Verwenden Sie **Time Brush**, um einen interessanten Bereich des Diagramms (beispielsweise eine Spitze oder einen Einbruch) zu untersuchen. Platzieren Sie den Mauszeiger am Anfang des Bereichs, halten Sie die linke Maustaste gedrückt, ziehen Sie den Mauszeiger zur anderen Seite des Bereichs, und lassen Sie die Maustaste wieder los. Im Diagramm wird dieser Zeitbereich vergrößert. 

![Time Brush](media/tutorial-metrics-explorer/time-brush.png)

## <a name="apply-dimension-filters-and-splitting"></a>Anwenden von Dimensionsfiltern und Aufteilung
Die folgenden Referenzen enthalten Informationen zu erweiterten Features, mit denen Sie Ihre Metriken weiter analysieren und potenzielle Ausreißer in Ihren Daten ermitteln können.

- Durch das [Filtern](../platform/metrics-charts.md#apply-filters-to-charts) können Sie entscheiden, welche Dimensionswerte im Diagramm enthalten sind. So können Sie beispielsweise erreichen, dass in einem Diagramm für eine Metrik vom Typ *Serverantwortzeit* nur erfolgreiche Anforderungen angezeigt werden. 

- Durch das [Aufteilen](../platform/metrics-charts.md#apply-splitting-to-a-chart) können Sie steuern, ob das Diagramm separate Linien für jeden Wert einer Dimension anzeigt oder die Werte in eine Linie zusammenfügt. So können Sie beispielsweise eine einzelne Linie für die durchschnittliche Antwortzeit aller Serverinstanzen oder separate Linien für jeden Server anzeigen. 

In den [Beispielen für Metrikdiagramme](../platform/metric-chart-samples.md) können Sie sich Beispiele mit angewandter Filterung und Aufteilung ansehen.

## <a name="advanced-chart-settings"></a>Erweiterte Diagrammeinstellungen

Sie können die Formatierung des Diagramms, den Titel und erweiterte Diagrammeinstellungen anpassen. Wenn Sie Ihre Anpassungen vorgenommen haben, heften Sie das Diagramm an ein Dashboard an, um Ihr Werk zu speichern. Sie können auch Metrikwarnungen konfigurieren. Unter [Erweiterte Funktionen von Azure Metrik-Explorer](../platform/metrics-charts.md#lock-boundaries-of-chart-y-axis) finden Sie Informationen zu diesen und anderen erweiterten Features des Azure Monitor-Metrik-Explorers.


## <a name="next-steps"></a>Nächste Schritte
Sie wissen nun, wie Sie mit Metriken in Azure Monitor arbeiten. Als Nächstes erfahren Sie, wie Sie Metriken verwenden, um proaktive Warnungen zu senden.

> [!div class="nextstepaction"]
> [Erstellen, Anzeigen und Verwalten von Metrikwarnungen mit Azure Monitor](../platform/metrics-charts.md#create-alert-rules)

