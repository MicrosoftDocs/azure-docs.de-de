---
title: Überwachen von Pipelineausführungen mithilfe von Synapse Studio
description: Verwenden Sie Synapse Studio, um Ihre Pipelineausführungen im Arbeitsbereich zu überwachen.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 11/30/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: dbd890117c78274392d5745e0563332371b404c5
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96452586"
---
# <a name="use-synapse-studio-to-monitor-your-workspace-pipeline-runs"></a>Verwenden von Synapse Studio zum Überwachen Ihrer Pipelineausführungen im Arbeitsbereich

Mit Azure Synapse Analytics können Sie komplexe Pipelines erstellen, mit denen Sie Ihre Datenverschiebung, Datentransformation und Computeaktivitäten innerhalb Ihrer Lösung automatisieren und integrieren können. Sie können diese Pipelines mithilfe von Synapse Studio erstellen und überwachen.

In diesem Artikel wird erläutert, wie Sie Ihre Pipelineausführungen überwachen, was es Ihnen gestattet, den aktuellen Status, die aktuellen Probleme und den Fortschritt ihrer Pipelines im Auge zu behalten.

## <a name="access-pipeline-runs-list"></a>Zugreifen auf die Liste der Pipelineausführungen

Um die Liste der Pipelineausführungen in Ihrem Arbeitsbereich anzuzeigen, [öffnen Sie zunächst Synapse Studio](https://web.azuresynapse.net/), und wählen Sie Ihren Arbeitsbereich aus.

![Anmelden beim Arbeitsbereich](./media/common/login-workspace.png)

Nachdem Sie Ihren Arbeitsbereich geöffnet haben, wählen Sie links den Abschnitt **Überwachen** aus.

![Auswählen des Hubs „Überwachen“](./media/common/left-nav.png)

Wählen Sie **Pipelineausführungen** aus, um die Liste der Pipelineausführungen anzuzeigen.

![Auswählen von Pipelineausführungen](./media/how-to-monitor-pipeline-runs/monitor-hub-nav-pipelineruns.png)

## <a name="filter-your-pipeline-runs"></a>Filtern Ihrer Pipelineausführungen

Sie können die Liste der Pipelineausführungen nach den für Sie interessanten Ausführungen filtern. Mit den Filtern am oberen Rand des Bildschirms können Sie ein Feld angeben, nach dem Sie filtern möchten.

Beispielsweise können Sie die Ansicht so filtern, dass nur die Pipelineausführungen für die Pipeline namens „Feiertag“ (holiday) angezeigt werden:

![Beispielfilter](./media/how-to-monitor-pipeline-runs/filter-example.png)

## <a name="view-details-about-a-specific-pipeline-run"></a>Anzeigen von Details zu einer bestimmten Pipelineausführung

Um Details zu Ihrer Pipelineausführung anzuzeigen, wählen Sie die Pipelineausführung aus. Zeigen Sie dann mit der Pipelineausführung verknüpften Aktivitätsausführungen an. Wenn die Pipeline noch ausgeführt wird, können Sie den Fortschritt überwachen. 
  
## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Überwachen von Anwendungen finden Sie im Artikel [Überwachen von Apache Spark-Anwendungen](how-to-monitor-spark-applications.md). 
