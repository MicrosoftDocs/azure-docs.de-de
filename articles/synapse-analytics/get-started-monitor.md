---
title: 'Tutorial: Erste Schritte mit Azure Synapse Analytics – Überwachen des Synapse-Arbeitsbereichs'
description: In diesem Tutorial erfahren Sie, wie Sie Aktivitäten in Ihrem Synapse-Arbeitsbereich überwachen.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: monitoring
ms.topic: tutorial
ms.date: 10/15/2020
ms.openlocfilehash: c2260cc0d2c6b889b44c738576e33abceebe23f1
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96444407"
---
# <a name="monitor-your-synapse-workspace"></a>Überwachen Ihres Synapse-Arbeitsbereichs

In diesem Tutorial erfahren Sie, wie Sie Aktivitäten in Ihrem Synapse-Arbeitsbereich überwachen. Sie können aktuelle und vergangene Aktivitäten für SQL, Apache Spark und Pipelines überwachen. 

## <a name="introduction-to-the-monitor-hub"></a>Einführung in den Überwachungshub

Öffnen Sie Synapse Studio, und navigieren Sie zum Hub **Überwachen**. Hier können Sie einen Verlauf aller Aktivitäten anzeigen, die im Arbeitsbereich stattfinden, und welche davon jetzt aktiv sind. 

* Unter **Integration** können Sie Pipelines, Trigger und Integration Runtimes überwachen.
* Unter **Aktivitäten** können Sie Spark- und SQL-Aktivitäten überwachen. 

## <a name="integration"></a>Integration

1. Navigieren Sie zu **Integration > Pipeline**. In dieser Ansicht können Sie alle Ausführungen einer Pipeline in Ihrem Arbeitsbereich anzeigen. 
1. Suchen Sie die Pipeline, die Sie im vorherigen Schritt ausgeführt haben, und klicken Sie auf den **Pipelinenamen**, um Details anzuzeigen.
1. Klicken Sie am oberen Rand von Synapse Studio auf die **Breadcrumb-Leiste** und dann auf **Alle Pipelineausführungen**, um zur vorherigen Ansicht zurückzukehren.

## <a name="apache-spark-activities"></a>Apache Spark-Aktivitäten

1. Navigieren Sie zu **Integration > Aktivitäten > Apache Spark-Anwendungen**. Nun sehen Sie alle Spark-Anwendungen, die in Ihrem Arbeitsbereich ausgeführt werden oder wurden.
1. Suchen Sie nach einer Anwendung, die nicht mehr ausgeführt wird, und klicken Sie auf den **Anwendungsnamen**. Sie sehen nun die Details der Spark-Anwendung.
1. Wenn Sie mit Apache Spark vertraut sind, können Sie zur Standardbenutzeroberfläche des Apache Spark-Verlaufsservers navigieren, indem Sie auf **Spark-Verlaufsserver** klicken.

## <a name="sql-activities"></a>SQL-Aktivitäten

1. Navigieren Sie zu **Integration > Aktivitäten > SQL-Anforderungen**.
1. In dieser Ansicht können Sie SQL-Anforderungen anzeigen.
1. Wählen Sie einen zu überwachenden **Pool** aus. Nun sehen Sie alle SQL-Anforderungen, die in Ihrem Arbeitsbereich in diesem Pool ausgeführt werden oder wurden.
1. Suchen Sie eine bestimmte SQL-Anforderung, und zeigen Sie mit der Maus auf das betreffende Element. Wenn Sie mit der Maus darauf zeigen, wird ein SQL-Skriptsymbol angezeigt.
1. Klicken Sie auf das SQL-Skriptsymbol, um den vollständigen Text der SQL-Anforderung anzuzeigen.

    > [!NOTE] 
    > SQL-Anforderungen, die über Synapse Studio in einem für den Arbeitsbereich aktivierten dedizierten SQL-Pool (vormals SQL DW) übermittelt werden, können im Überwachungshub angezeigt werden. Für alle anderen Überwachungsaktivitäten können Sie die Überwachung von dedizierten SQL-Pools (vormals SQL DW) im Azure-Portal verwenden.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Weitere Informationen zu Knowledge Center](get-started-knowledge-center.md)
