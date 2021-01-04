---
title: Überwachen der serverseitigen Latenz bei Vorgängen in Azure Cosmos DB
description: Hier erfahren Sie, wie Sie die serverseitige Latenz bei Vorgängen im Azure Cosmos DB-Konto oder einem Container überwachen. Besitzer eines Azure Cosmos DB-Kontos können die serverseitigen Latenzprobleme bei Ihren Azure Cosmos-Konten verstehen.
ms.service: cosmos-db
ms.topic: how-to
author: kanshiG
ms.author: govindk
ms.date: 04/07/2020
ms.openlocfilehash: ec6a9db63504958640137fcd0fcfc904eb01afa5
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93074728"
---
# <a name="how-to-monitor-the-server-side-latency-for-operations-in-an-azure-cosmos-db-container-or-account"></a>Überwachen der serverseitigen Latenz bei Vorgängen in einem Azure Cosmos DB-Container oder -Konto
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Monitor für Azure Cosmos DB bietet eine Metrikansicht zum Überwachen Ihres Kontos und zum Erstellen von Dashboards. Weil die Azure Cosmos DB-Metriken standardmäßig erfasst werden, müssen Sie bei diesem Feature nichts explizit aktivieren oder konfigurieren. Die serverseitige Latenzmetrik wird zum Anzeigen der serverseitigen Latenz eines Vorgangs verwendet. Azure Cosmos DB bietet eine SLA von weniger als 10 ms für Punktlese-/Punktschreibvorgänge mit direkter Konnektivität. Bei Punktlese- und -schreibvorgängen werden die SLAs so berechnet, wie es im [SLA-Dokument](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_3/) ausführlich erläutert wird.

Sie können die serverseitige Latenz überwachen, wenn Sie eine ungewöhnlich hohe Latenz bei Punktvorgängen feststellen. Beispiele:

* Ein GET- oder SET-Vorgang mit Partitionsschlüssel und ID im Modus für direkte Konnektivität
* Ein Lese- oder Schreibvorgang oder
* Eine Abfrage

Sie können im Diagnoseprotokoll nachschlagen, um die Größe der zurückgegebenen Daten zu ermitteln. Wenn Sie eine dauerhafte hohe Latenz bei Abfragevorgängen feststellen, sollten Sie im Diagnoseprotokoll nach einem höheren verwendeten [Durchsatz oder RU/s](cosmosdb-monitor-resource-logs.md#diagnostic-queries) suchen. Die serverseitige Latenz gibt die Zeitspanne an, die in der Back-End-Infrastruktur aufgewendet wurde, bevor die Daten an den Client zurückgegeben wurden. Es ist wichtig, sich diese Metrik anzusehen, um jegliche Probleme mit der Back-End-Latenz auszuschließen.

## <a name="view-the-server-side-latency-metric"></a>Anzeigen der serverseitigen Latenzmetrik

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

1. Wählen Sie in der Navigationsleiste auf der linken Seite die Option **Monitor** und dann **Metrik** aus.

   :::image type="content" source="./media/monitor-server-side-latency/monitor-metrics-blade.png" alt-text="Bereich „Metriken“ in Azure Monitor":::

1. Klicken Sie im Bereich **Metriken** auf **Ressource auswählen**. Wählen Sie dann das erforderliche **Abonnement** und die **Ressourcengruppe** aus. Wählen Sie unter **Ressourcentyp** die Option **Azure Cosmos DB-Konten** aus. Wählen Sie dann eins der vorhandenen Azure Cosmos-Konten und anschließend **Anwenden** aus.
   
   :::image type="content" source="./media/monitor-server-side-latency/select-cosmos-db-account.png" alt-text="Auswählen des Azure Cosmos DB-Kontos zum Anzeigen von Metriken":::

1. Wählen Sie als Nächstes in der Liste der verfügbaren Metriken die Metrik **Serverseitige Latenz** aus. Ausführliche Informationen zu allen verfügbaren Metriken in dieser Liste finden Sie im Artikel [Metriken nach Kategorie](monitor-cosmos-db-reference.md). In diesem Beispiel wählen Sie **Serverseitige Latenz** und als Aggregationswert **Durchschn.** aus. Zusätzlich zu diesen Angaben können Sie auch **Zeitbereich** und **Zeitgranularität** für die Metriken auswählen. Sie können Metriken maximal für die letzten 30 Tage anzeigen.  Nach Anwendung des Filters wird ein darauf basierendes Diagramm angezeigt. Die serverseitige Latenz pro Minute wird für den ausgewählten Zeitraum angezeigt.  

   :::image type="content" source="./media/monitor-server-side-latency/server-side-latency-metric.png" alt-text="Auswählen der serverseitigen Latenzmetrik über das Azure-Portal":::

## <a name="filters-for-server-side-latency"></a>Filter für serverseitige Latenz

Sie können Metriken und die angezeigten Diagramme auch nach bestimmten Werten für **CollectionName** , **ConnectionMode** , **DatabaseName** , **OperationType** , **Region** und **PublicAPIType** filtern. 

Wählen Sie zum Filtern der Metriken **Filter hinzufügen** und dann die erforderliche Eigenschaft aus, z. B. **PublicAPIType**. Wählen Sie anschließend den Wert **sql** aus. Fügen Sie einen weiteren Filter für **OperationType** hinzu. Im Diagramm wird dann die serverseitige Latenz bei verschiedenen Vorgänge während des ausgewählten Zeitraums angezeigt. Die über eine gespeicherte Prozedur ausgeführten Vorgänge werden nicht protokolliert und sind daher nicht unter der Metrik „OperationType“ verfügbar.

Die **serverseitigen Latenzmetriken** für jeden Vorgang werden wie in der folgenden Abbildung dargestellt angezeigt:

:::image type="content" source="./media/monitor-server-side-latency/server-side-latency-filters.png" alt-text="Filter für serverseitige Latenzmetriken":::

Sie können die Metriken mit der Option **Teilung anwenden** auch gruppieren.  

## <a name="next-steps"></a>Nächste Schritte

* Überwachen der Azure Cosmos DB-Daten anhand der [Diagnoseeinstellungen](cosmosdb-monitor-resource-logs.md) in Azure
* [Überwachen von Azure Cosmos DB-Vorgängen auf Steuerungsebene](audit-control-plane-logs.md)
