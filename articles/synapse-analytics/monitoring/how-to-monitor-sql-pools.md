---
title: Überwachen von SQL-Pools in Synapse Studio
description: Hier erfahren Sie, wie Sie Ihre SQL-Pools mit Synapse Studio überwachen.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 11/30/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: 63b63526333435387c3ff5b5c9d5599ec851c1a8
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96465471"
---
# <a name="use-synapse-studio-to-monitor-your-sql-pools"></a>Überwachen Ihrer SQL-Pools mit Synapse Studio

Mit Synapse Studio können Sie SQL-Skripts für die SQL-Pools in Ihrem Arbeitsbereich ausführen.

In diesem Artikel wird erläutert, wie Sie Ihre SQL-Pools überwachen, damit Sie den Status und die Aktivität Ihrer Pools im Auge behalten können.

## <a name="access-sql-pools-list"></a>Zugreifen auf die Liste der SQL-Pools

Um die Liste der SQL-Pools in Ihrem Arbeitsbereich anzuzeigen, [öffnen Sie zunächst Synapse Studio](https://web.azuresynapse.net/), und wählen Sie Ihren Arbeitsbereich aus.

![Anmelden beim Arbeitsbereich](./media/common/login-workspace.png)

Nachdem Sie Ihren Arbeitsbereich geöffnet haben, wählen Sie links den Abschnitt **Überwachen** aus.

![Auswählen des Hubs „Überwachen“](./media/common/left-nav.png)

Wählen Sie **SQL-Pools** aus, um die Liste der SQL-Pools anzuzeigen.

 ![Auswählen von „SQL-Pools“](./media/how-to-monitor-sql-pools/monitor-hub-nav-sql-pools.png)

## <a name="filter-your-sql-pools"></a>Filtern Ihrer SQL-Pools

Sie können die Liste der SQL-Pools nach den Pools filtern, die Sie interessieren. Mit den Filtern am oberen Rand des Bildschirms können Sie ein Feld angeben, nach dem Sie filtern möchten.

Beispielsweise können Sie die Ansicht so filtern, dass nur die SQL-Pools angezeigt werden, die den Namen „salesrecords“ enthalten:

![Beispielfilter](./media/how-to-monitor-sql-pools/filter-example.png)

## <a name="view-details-about-a-specific-sql-pool"></a>Anzeigen von Details zu einem bestimmten SQL-Pool

Um die Details zu einem Ihrer SQL-Pools anzuzeigen, wählen Sie den gewünschten SQL-Pool aus.

![Details eines SQL-Pools](./media/how-to-monitor-sql-pools/sql-pool-details.png)

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Überwachen von Pipelineausführungen finden Sie im Artikel [Verwenden von Synapse Studio zum Überwachen Ihrer Pipelineausführungen](how-to-monitor-pipeline-runs.md). 

Weitere Informationen zum Überwachen von SQL-Anforderungen finden Sie im Artikel [Verwenden von Synapse Studio zum Überwachen Ihrer SQL-Anforderungen](how-to-monitor-sql-requests.md).