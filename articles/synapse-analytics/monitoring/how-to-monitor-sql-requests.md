---
title: Überwachen von SQL-Anforderungen in Synapse Studio
description: Erfahren Sie, wie Sie SQL-Anforderungen mit Synapse Studio überwachen.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 11/30/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: 7296fec91decaf1bd80829f9f3a743a518fbb7a7
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96465468"
---
# <a name="use-synapse-studio-to-monitor-your-sql-requests"></a>Überwachen Ihrer SQL-Anforderungen mit Synapse Studio

Sie können mit Synapse Studio SQL-Skripts für die SQL-Pools in Ihrem Arbeitsbereich ausführen.

In diesem Artikel wird erläutert, wie Sie Ihre SQL-Anforderungen überwachen, damit Sie den Status der Ausführung von Anforderungen im Blick behalten und Details zu früheren Anforderungen nachverfolgen können.

## <a name="access-sql-requests-list"></a>Zugreifen auf die Liste der SQL-Anforderungen

Um die Liste der SQL-Anforderungen in Ihrem Arbeitsbereich anzuzeigen, [öffnen Sie zunächst Synapse Studio](https://web.azuresynapse.net/), und wählen Sie Ihren Arbeitsbereich aus.

![Anmelden beim Arbeitsbereich](./media/common/login-workspace.png)

Nachdem Sie Ihren Arbeitsbereich geöffnet haben, wählen Sie links den Abschnitt **Überwachen** aus.

![Auswählen des Hubs „Überwachen“](./media/common/left-nav.png)

Wählen Sie **SQL-Anforderungen** aus, um die Liste der SQL-Anforderungen anzuzeigen.

 ![Auswählen von SQL-Anforderungen](./media/how-to-monitor-sql-requests/monitor-hub-nav-sql-requests.png)

## <a name="select-a-sql-pool"></a>Auswählen eines SQL-Pools

Standardmäßig wird in dieser Sicht der SQL-Anforderungsverlauf für den integrierten serverlosen SQL-Pool angezeigt. Sie können einen Ihrer dedizierten SQL-Pools auswählen, um den SQL-Anforderungsverlauf für diesen Pool anzuzeigen.

![Auswählen eines SQL-Pools](./media/how-to-monitor-sql-requests/select-pool.png)

## <a name="filter-your-sql-requests"></a>Filtern der SQL-Anforderungen

Sie können die Liste nach den SQL-Anforderungen filtern, die Sie interessieren. Mit den Filtern am oberen Rand des Bildschirms können Sie ein Feld angeben, nach dem Sie filtern möchten.

Beispielsweise können Sie die Sicht filtern, um nur die von `maria@contoso.com` übermittelten SQL-Anforderungen anzuzeigen:

![Beispielfilter](./media/how-to-monitor-sql-requests/filter-example.png)

## <a name="view-details-about-a-specific-sql-request"></a>Anzeigen von Details zu einer bestimmten SQL-Anforderung

Um Details zu einer Ihrer SQL-Anforderungen anzuzeigen, öffnen Sie diese und navigieren zur Detailansicht. Bei komplexen Anforderungen, die in dedizierten SQL-Pools ausgeführt werden, können Sie den Fortschritt überwachen.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Überwachen von Pipelineausführungen finden Sie im Artikel [Überwachen von Pipelineausführungen mithilfe von Synapse Studio](how-to-monitor-pipeline-runs.md). 

Weitere Informationen zum Überwachen von Apache Spark-Anwendungen finden Sie im Artikel [Überwachen von Apache Spark-Anwendungen in Synapse Studio](how-to-monitor-spark-applications.md).