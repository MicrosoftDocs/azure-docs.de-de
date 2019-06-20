---
title: Azure Data Factory-Mapping-Datenfluss – Visuelle Überwachung
description: Erfahren Sie, wie Sie Azure Data Factory -Datenflüsse visuell überwachen.
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/01/2019
ms.openlocfilehash: 90aa6261aebb9d1f7da89c101854bad8061dd6ff
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61268992"
---
# <a name="monitor-data-flows"></a>Überwachen von Datenflüssen

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Nachdem Sie den Aufbau und das Debugging Ihres Datenflusses abgeschlossen haben, sollten Sie Ihren Datenfluss so planen, dass er nach einem Zeitplan im Rahmen einer Pipeline ausgeführt wird. Sie können die Pipeline von Azure Data Factory mit Hilfe von Auslösern planen. Oder Sie können die Option „Jetzt auslösen“ aus dem Azure Data Factory Pipeline Builder verwenden, um eine einmalige Ausführung auszuführen, um Ihren Datenfluss im Kontext der Pipeline zu testen.

Wenn Sie Ihre Pipeline ausführen, können Sie die Pipeline und alle in der Pipeline enthaltenen Aktivitäten einschließlich der Datenflussaktivität überwachen. Klicken Sie auf das Symbol „Überwachen“ im linken Bereich der Azure Data Factory-Benutzeroberfläche. Es wird ein Bildschirm angezeigt, der dem folgenden Bildschirm ähnelt. Über die hervorgehobenen Symbole können Sie einen Drilldown für die Aktivitäten in der Pipeline ausführen, einschließlich der Datenflussaktivität.

<img src="media/data-flow/mon001.png" width="800">

Sehen Sie die Statistiken auf dieser Ebene als auch die Laufzeiten und den Status. Die Ausführungs-ID auf der Aktivitätsebene unterscheidet sich von der Ausführungs-ID auf Pipelineebene. Die Ausführungs-ID der vorherigen Ebene ist für die Pipeline. Durch Anklicken der Brille erhalten Sie detaillierte Informationen über Ihre Datenflussausführung.

<img src="media/data-flow/mon002.png" width="800">

Wenn Sie sich in der grafischen Knotenüberwachungsansicht befinden, sehen Sie eine vereinfachte reine Ansichtsversion Ihres Datenflussdiagramms.

<img src="media/data-flow/mon003.png" width="800">

## <a name="view-data-flow-execution-plans"></a>Anzeigen der Ausführungspläne für den Datenfluss

Wenn Ihr Datenfluss in Databricks ausgeführt wird, bestimmt Azure Data Factory basierend auf Ihrem gesamten Datenfluss die optimalen Codepfade. Darüber hinaus können die Ausführungspfade auf verschiedenen horizontalen Skalierungsknoten und Datenpartitionen auftreten. Daher stellt das Überwachungsdiagramm den Entwurf Ihres Flusses dar, wobei der Ausführungspfad Ihrer Transformationen berücksichtigt wird. Wenn Sie auf einzelne Knoten klicken, werden „Gruppierungen“ angezeigt, die Code darstellen, der gemeinsam auf dem Cluster ausgeführt wurde. Die angezeigten Zeitangaben und Zählungen stellen diese Gruppen im Gegensatz zu den einzelnen Schritten in Ihrem Entwurf dar.

<img src="media/data-flow/mon004.png" width="800"> 

* Wenn Sie auf das freie Feld im Überwachungsfenster klicken, zeigen die Statistiken im unteren Fensterbereich die Zeitangabe und die Anzahl der Zeilen für jede Senke und die Transformationen an, die zu den Senkendaten für die Transformationslinie führten.

* Wenn Sie einzelne Transformationen auswählen, erhalten Sie auf der rechten Seite zusätzliches Feedback, das Partitionsstatistiken, Spaltenanzahl, Schiefe (wie gleichmäßig sind die Daten auf Partitionen verteilt) und Kurtosis (wie viele Spitzen die Daten haben) enthält.

* Wenn Sie auf die Senke in der Knotenansicht klicken, sehen Sie die Spaltenherkunft. Es gibt drei verschiedene Methoden, mit denen Spalten während des gesamten Datenflusses akkumuliert werden, um in die Senke weitergeleitet zu werden. Sie lauten wie folgt:

  * Berechnet: Sie verwenden die Spalte für die bedingte Verarbeitung oder innerhalb eines Ausdrucks in Ihrem Datenfluss, sie wird aber nicht in die Senke weitergeleitet.
  * Abgeleitet: Die Spalte ist eine neue Spalte, die Sie in Ihrem Fluss generiert haben, d.h. sie war nicht in der Quelle vorhanden.
  * Zugeordnet: Die Spalte stammt aus der Quelle und Sie ordnen sie einem Senkenfeld zu.
  
## <a name="monitor-icons"></a>Symbole für die Überwachung

Dieses Symbol bedeutet, dass die Transformationsdaten bereits auf dem Cluster zwischengespeichert wurden, sodass die Zeitangaben und der Ausführungspfad dies berücksichtigt haben:

<img src="media/data-flow/mon005.png" width="800"> 

Es werden auch grüne Kreissymbolen in der Transformation angezeigt. Sie stellen die Anzahl von Senken dar, in die der Datenfluss geleitet wird.
