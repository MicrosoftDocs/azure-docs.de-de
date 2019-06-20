---
title: Überwachen der Azure Data Explorer-Leistung, -Integrität und -Nutzung mit Metriken
description: Es wird beschrieben, wie Sie Azure Data Explorer-Metriken zum Überwachen der Leistung, Integrität und Nutzung des Clusters verwenden.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/01/2019
ms.openlocfilehash: a9c9f4d827d21c374bebba9d39e33b0bcad8a83e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60826797"
---
# <a name="monitor-azure-data-explorer-performance-health-and-usage-with-metrics"></a>Überwachen der Azure Data Explorer-Leistung, -Integrität und -Nutzung mit Metriken

Azure Data Explorer ist ein schneller, vollständig verwalteter Datenanalysedienst für Echtzeitanalysen großer Datenmengen, die von Anwendungen, Websites, IoT-Geräten usw. gestreamt werden. Um den Azure Data Explorer zu verwenden, erstellen Sie zuerst einen Cluster und anschließend eine oder mehrere Datenbanken in diesem Cluster. Anschließend erfassen (laden) Sie Daten in eine Datenbank, damit Sie diese abfragen können. Azure Data Explorer-Metriken liefern wichtige Hinweise in Bezug auf die Integrität und Leistung der Clusterressourcen. Verwenden Sie die in diesem Artikel beschriebenen Metriken, um die Integrität und Leistung des Azure Data Explorer-Clusters in Ihrem jeweiligen Szenario als eigenständige Metriken zu überwachen. Sie können Metriken auch als Basis für den Betrieb von [Azure-Dashboards](/azure/azure-portal/azure-portal-dashboards) und [Azure-Warnungen](/azure/azure-monitor/platform/alerts-metric-overview) verwenden.

## <a name="prerequisites"></a>Voraussetzungen

* Erstellen Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/), falls Sie nicht über ein Azure-Abonnement verfügen.

* Erstellen Sie einen [Cluster und eine Datenbank](create-cluster-database-portal.md).

## <a name="sign-in-to-the-azure-portal"></a>Melden Sie sich auf dem Azure-Portal an.

Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

## <a name="using-metrics"></a>Verwenden von Metriken

Wählen Sie in Ihrem Azure Data Explorer-Cluster die Option **Metriken**, um den Bereich „Metriken“ zu öffnen und mit der Analyse Ihres Clusters zu beginnen.

![Auswählen von „Metriken“](media/using-metrics/select-metrics.png)

Im Bereich „Metriken“:

![Bereich „Metriken“](media/using-metrics/metrics-pane.png)

1. Wählen Sie zum Erstellen eines Metrikdiagramms wie unten beschrieben den Namen der **Metrik** und die relevante **Aggregation** pro Metrik aus. In der Auswahl für die **Ressource** und den **Metriknamespace** ist Ihr Azure Data Explorer-Cluster bereits ausgewählt.

    **Metrik** | **Einheit** | **Aggregation** | **Beschreibung der Metrik**
    |---|---|---|---|
    | Cacheauslastung | Percent | Avg, Max, Min | Prozentsatz der zugewiesenen Cacheressourcen, die derzeit vom Cluster genutzt werden. Der Cache bezieht sich auf die Größe der SSD, die für die Benutzeraktivität gemäß der definierten Cacherichtlinie zugeordnet wird. Eine durchschnittliche Cacheauslastung von 80 % oder weniger ist für einen Cluster ein tragbarer Zustand. Wenn die durchschnittliche Cacheauslastung über 80 % liegt, sollte für den Cluster das [zentrale Hochskalieren](manage-cluster-scale-up.md) auf einen Tarif mit Datenspeicheroptimierung oder das [horizontale Hochskalieren](manage-cluster-scale-out.md) auf weitere Instanzen durchgeführt werden. Alternativ hierzu können Sie die Cacherichtlinie anpassen (weniger Tage im Cache). Falls die Cacheauslastung über 100 % liegt, übersteigt die Größe der zwischenzuspeichernden Daten gemäß Cacherichtlinie die Gesamtgröße des Caches im Cluster. |
    | CPU | Percent | Avg, Max, Min | Prozentsatz der zugewiesenen Computeressourcen, die derzeit von Computern im Cluster genutzt werden. Eine durchschnittliche CPU-Auslastung von 80 % ist für einen Cluster ein tragbarer Zustand. Der Maximalwert der CPU beträgt 100 %. Dies bedeutet, dass keine weiteren Computeressourcen zum Verarbeiten von Daten vorhanden sind. Wenn die Leistung eines Clusters nicht gut ist, sollten Sie den Maximalwert der CPU überprüfen, um zu ermitteln, ob bestimmte CPUs blockiert sind. |
    | Verarbeitete Ereignisse (für Event Hubs) | Count | Max, Min, Sum | Gesamtzahl der Ereignisse, die von Event Hubs gelesen und vom Cluster verarbeitet werden. Die Ereignisse werden danach unterteilt, ob sie vom Clustermodul abgelehnt oder akzeptiert werden. |
    | Latenz bei der Erfassung | Sekunden | Avg, Max, Min | Latenz der erfassten Daten ab dem Empfangszeitpunkt der Daten im Cluster bis zu dem Zeitpunkt, zu dem die Daten bereit zum Abfragen sind. Der Zeitraum der Erfassungslatenz richtet sich nach dem Erfassungsszenario. |
    | Ergebnis der Datenerfassung | Count | Count | Gesamtzahl von nicht erfolgreichen und erfolgreichen Erfassungsvorgängen. Verwenden Sie die Option **Teilung anwenden**, um Buckets mit Erfolgs- und Fehlerergebnissen zu erstellen und die Dimensionen zu analysieren (**Wert** > **Status**).|
    | Datenerfassungsauslastung | Percent | Avg, Max, Min | Prozentsatz der tatsächlichen Ressourcen zum Erfassen von Daten von den gesamten Ressourcen, die in der Kapazitätsrichtlinie für die Erfassung zugeordnet sind. Die Standardrichtlinie für die Kapazität umfasst nicht mehr als 512 gleichzeitige Erfassungsvorgänge oder 75 % der Clusterressourcen, die für die Erfassung genutzt werden. Eine durchschnittliche Erfassungsauslastung von 80 % oder weniger ist für einen Cluster ein tragbarer Zustand. Der Maximalwert der Erfassungsauslastung beträgt 100 %. Dies bedeutet, dass die gesamte Erfassungskapazität des Clusters genutzt wird und unter Umständen eine Erfassungswarteschlange entsteht. |
    | Datenerfassungsvolumen (in MB) | Count | Max, Min, Sum | Die Gesamtgröße der im Cluster erfassten Daten (in MB) vor der Komprimierung. |
    | Keep-Alive | Count | Avg | Dient zum Nachverfolgen der Reaktionsfähigkeit des Clusters. Für einen Cluster mit höchster Reaktionsfähigkeit wird der Wert 1 zurückgegeben, und für einen blockierten oder getrennten Cluster lautet der Wert 0. |
    | Abfragedauer | Sekunden | Count, Avg, Min, Max, Sum | Gesamtzeit bis zum Empfangen der Abfrageergebnisse (ohne Netzwerklatenz). |
    | | | |

    Weitere Informationen zu [unterstützten Azure Data Explorer-Clustermetriken](/azure/azure-monitor/platform/metrics-supported#microsoftkustoclusters)

2. Wählen Sie die Schaltfläche **Metrik hinzufügen**, um mehrere Metriken in demselben Diagramm anzuzeigen.
3. Wählen Sie die Schaltfläche **+ Neues Diagramm**, um mehrere Diagramme in einer Ansicht anzuzeigen.
4. Verwenden Sie die Zeitauswahl, um den Zeitbereich zu ändern (Standardeinstellung: Letzte 24 Stunden).
5. Verwenden Sie [**Filter hinzufügen** und **Teilung anwenden**](/azure/azure-monitor/platform/metrics-getting-started#apply-dimension-filters-and-splitting) für Metriken, die über Dimensionen verfügen.
6. Wählen Sie die Option **An Dashboard anheften**, um Ihre Diagrammkonfiguration den Dashboards hinzuzufügen, damit Sie die Anzeige erneut verwenden können.
7. Legen Sie die Option **Neue Warnungsregel** fest, um Ihre Metriken mit den festgelegten Kriterien zu visualisieren. Die neue Warnungsregel enthält Ihre Dimensionen für Zielressource, Metrik, Teilung und Filter aus dem Diagramm. Ändern Sie diese Einstellungen im [Bereich für die Erstellung von Warnungsregeln](/azure/azure-monitor/platform/metrics-charts#create-alert-rules).

Weitere Informationen zur Verwendung von [Metrik-Explorer](/azure/azure-monitor/platform/metrics-getting-started)


## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Schnellstart: Abfragen von Daten in Azure Data Explorer](web-query-data.md)
