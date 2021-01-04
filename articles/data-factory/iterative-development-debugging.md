---
title: Iteratives Entwickeln und Debuggen in Azure Data Factory
description: Erfahren Sie, wie Sie Data Factory-Pipelines auf der Benutzeroberfläche von Azure Data Factory iterativ entwickeln und debuggen.
ms.date: 10/29/2020
ms.topic: conceptual
ms.service: data-factory
services: data-factory
documentationcenter: ''
ms.workload: data-services
author: dcstwh
ms.author: weetok
ms.openlocfilehash: 9b28fb24439354e09e5262281a99cd9dc0153a04
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96485245"
---
# <a name="iterative-development-and-debugging-with-azure-data-factory"></a>Iteratives Entwickeln und Debuggen mit Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Mit Azure Data Factory können Sie bei der Entwicklung Ihrer Datenintegrationslösungen Data Factory-Pipelines iterativ entwickeln und debuggen. Diese Features ermöglichen Ihnen, Ihre Änderungen zu testen, bevor Sie eine Pull Request erstellen oder sie im Data Factory-Dienst veröffentlichen. 

Das folgende Video enthält eine achtminütige Einführung und Demonstration dieses Features:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Iterative-development-and-debugging-with-Azure-Data-Factory/player]

## <a name="debugging-a-pipeline"></a>Debuggen einer Pipeline

Wenn Sie auf der Pipelinecanvas arbeiten, können Sie Ihre Aktivitäten mit der Funktion **Debuggen** testen. Beim Ausführen von Testläufen müssen Sie Ihre Änderungen nicht in der Data Factory veröffentlichen, bevor Sie auf **Debuggen** klicken. Dieses Feature ist in Szenarios praktisch, bei denen Sie sicherstellen möchten, dass die Änderungen wie erwartet funktionieren, bevor Sie den Data Factory-Workflow aktualisieren.

![Funktion zum Debuggen auf der Pipelinecanvas](media/iterative-development-debugging/iterative-development-1.png)

Während die Pipeline ausgeführt wird, können Sie die Ergebnisse der einzelnen Aktivitäten auf der Registerkarte **Ausgabe** der Pipelinecanvas sehen.

Zeigen Sie die Ergebnisse der Testläufe auf der Pipelinecanvas im Fenster **Ausgabe** an.

![Ausgabefenster der Pipelinecanvas](media/iterative-development-debugging/iterative-development-2.png)

Fügen Sie nach der erfolgreichen Ausführung eines Testlaufs weitere Aktivitäten zur Pipeline hinzu, und setzen Sie das iterative Debuggen fort. Sie können einen aktiven Testlauf auch über **Abbrechen** beenden.

> [!IMPORTANT]
> Durch Auswählen von **Debuggen** wird die Pipeline ausgeführt. Wenn die Pipeline beispielsweise die Kopieraktivität enthält, werden beim Testlauf Daten aus der Quelle in das Ziel kopiert. Daher wird empfohlen, beim Debuggen Testordner in Ihrer copy-Aktivität und Ihren anderen Aktivitäten zu verwenden. Wechseln Sie nach dem Debuggen der Pipeline zu den Ordnern, die Sie tatsächlich in normalen Vorgängen verwenden möchten.

### <a name="setting-breakpoints"></a>Setzen von Haltepunkten

Mit Azure Data Factory können Sie eine Pipeline debuggen, bis Sie eine bestimmte Aktivität auf der Pipelinecanvas erreichen. Setzen Sie einen Haltepunkt für die Aktivität, bis zu dem der Test ausgeführt werden soll, und klicken Sie auf **Debuggen**. Data Factory stellt sicher, dass die Testläufe nur bis zur Breakpoint-Aktivität auf der Pipelinecanvas ausgeführt werden. Dieses Feature namens *Debug Until* (Debuggen bis) ist nützlich, wenn Sie nicht die gesamte Pipeline, sondern nur eine Teilmenge der Aktivitäten in der Pipeline testen möchten.

![Breakpoints auf der Pipelinecanvas](media/iterative-development-debugging/iterative-development-3.png)

Um einen Haltepunkt festzulegen, wählen Sie ein Element auf der Pipelinecanvas aus. Eine Option *Debug Until* wird als ein leerer roter Kreis in der oberen rechten Ecke des Elements angezeigt.

![Vor dem Festlegen eines Haltepunkts für das ausgewählte Element](media/iterative-development-debugging/iterative-development-4.png)

Nachdem Sie die Option *Debug Until* ausgewählt haben, ändert sich der leere Kreis in einen ausgefüllten roten Kreis, um anzugeben, dass der Breakpoint aktiviert ist.

![Nach dem Festlegen eines Haltepunkts für das ausgewählte Element](media/iterative-development-debugging/iterative-development-5.png)

## <a name="monitoring-debug-runs"></a>Überwachen von Debugausführungen

Bei einer Debugausführung für eine Pipeline werden die Ergebnisse im Fenster **Ausgabe** der Pipelinecanvas gezeigt. Die Registerkarte „Ausgabe“ enthält nur die jüngste Ausführung, die während der aktuellen Browsersitzung erfolgt ist. 

![Ausgabefenster der Pipelinecanvas](media/iterative-development-debugging/iterative-development-2.png)

Um eine Verlaufsansicht von Debugausführungen oder eine Liste aller aktiven Debugausführungen anzuzeigen, können Sie zur Oberfläche **Überwachung** wechseln. 

![Auswählen des Symbols zum Anzeigen aktiver Debugausführungen](media/iterative-development-debugging/view-debug-runs.png)

> [!NOTE]
> Der Azure Data Factory-Dienst speichert den Verlauf von Debugausführungen nur 15 Tage. 

## <a name="debugging-mapping-data-flows"></a>Debuggen von Zuordnungsdatenflüssen

Zuordnungsdatenflüsse ermöglichen Ihnen, codefreie Datentransformationslogik zu erstellen, die sich nach Maß ausführen lässt. Wenn Sie Ihre Logik entwickeln, können Sie eine Debugsitzung aktivieren, um mit Ihren Daten in einem aktiven Spark-Cluster interaktiv zu arbeiten. Weitere Informationen finden Sie unter [Debugmodus für den Zuordnungsdatenfluss](concepts-data-flow-debug-mode.md).

Auf der Oberfläche **Überwachung** können Sie aktive Debugsitzungen für den Datenfluss in einer Factory überwachen.

![Anzeigen von Debugsitzungen für einen Datenfluss](media/iterative-development-debugging/view-dataflow-debug-sessions.png)
 
### <a name="debugging-a-pipeline-with-a-data-flow-activity"></a>Debuggen einer Pipeline mit einer Datenflussaktivität

Bei der Ausführung einer Debugpipeline in einem Datenfluss haben Sie für Compute die Wahl zwischen zwei Optionen. Sie können entweder einen vorhandenen Debugcluster verwenden oder einen neuen Just-in-Time-Cluster für Ihre Datenflüsse einrichten.

Bei Verwendung einer vorhandenen Debugsitzung wird die Uptime des Datenflusses erheblich verkürzt, da der Cluster bereits läuft. Dies wird jedoch bei komplexen oder parallelen Workloads nicht empfohlen, da bei gleichzeitiger Ausführung mehrerer Aufträge Fehler auftreten können.

Bei Verwenden der Aktivitätslaufzeit wird ein neuer Cluster mit den Einstellungen erstellt, die in der Integration Runtime jeder Datenflussaktivität angegeben sind. Dies ermöglicht die Isolierung jedes einzelnen Auftrags und sollte für komplexe Workloads oder Leistungstests verwendet werden. Außerdem können Sie die Gültigkeitsdauer (TTL) in der Azure IR steuern, damit die für das Debuggen verwendeten Clusterressourcen während dieses Zeitraums weiterhin für zusätzliche Auftragsanforderungen verfügbar sind.

> [!NOTE]
> Wenn Sie über eine Pipeline mit Datenflüssen verfügen, die parallel ausgeführt werden, wählen Sie „Use Activity Runtime“ (Aktivitätsruntime verwenden) aus, damit Data Factory die in der Datenflussaktivität ausgewählte Integration Runtime verwenden kann. Dies ermöglicht das Ausführen der Datenflüsse in mehreren Clustern und die Verarbeitung der parallelen Datenflussausführungen.

![Ausführen einer Pipeline mit einem Datenfluss](media/iterative-development-debugging/iterative-development-dataflow.png)

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie Ihre Änderungen getestet haben, stufen Sie sie mithilfe von [Continuous Integration und Deployment in Azure Data Factory](continuous-integration-deployment.md) in höhere Umgebungen hoch.
