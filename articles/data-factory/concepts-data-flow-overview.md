---
title: Übersicht über Azure Data Factory Mapping Data Flow
description: Eine zusammenfassende Erläuterung von Mapping Data Flows in Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: 051886f98d6d35594336291bbb2defb2a4acdfc5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65233052"
---
# <a name="what-are-mapping-data-flows"></a>Was sind Mapping Data Flows?

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Mapping Data Flows sind visuell entworfene Datentransformationen in Azure Data Factory. Mit Data Flows können Data Engineers grafische Datentransformationslogik entwickeln, ohne Code schreiben zu müssen. Die daraus resultierenden Datenflüsse werden als Aktivitäten in Azure Data Factory-Pipelines mit horizontal skalierten Azure Databricks-Clustern ausgeführt.

Die Absicht von Azure Data Factory-Datenfluss ist es, eine vollständig visuelle Erfahrung ohne Codeerstellung zu ermöglichen. Ihre Datenflüsse werden in Ihrem eigenen Ausführungscluster für die horizontal skalierte Datenverarbeitung ausgeführt. Azure Data Factory übernimmt die gesamte Codeübersetzung, Pfadoptimierung und Ausführung Ihrer Datenflussaufträge.

Erstellen Sie zunächst Datenflüsse im Debugmodus, damit Sie Ihre Transformationslogik interaktiv überprüfen können. Fügen Sie anschließend Ihrer Pipeline eine Datenflussaktivität zum Ausführen und Testen Ihres Datenflusses in der Pipeline im Debugmodus hinzu, oder verwenden Sie in der Pipeline die Option „Trigger Now“ (Jetzt auslösen), um den Datenfluss in einer Pipelineaktivität zu testen.

Dann können Sie Ihre Datenflussaktivitäten mithilfe von Azure Data Factory-Pipelines planen und überwachen, die die Datenflussaktivität ausführen.

Der Umschalter „Debugmodus“ auf der Datenfluss-Entwurfsoberfläche ermöglicht die interaktive Erstellung von Datentransformationen. Der Debugmodus bietet eine Umgebung für die Datenvorbereitung zur Erstellung von Datenflüssen.
