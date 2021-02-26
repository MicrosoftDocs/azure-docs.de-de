---
title: Visuelles Erstellen
description: Informationen zum Verwenden der visuellen Erstellung in Azure Data Factory
ms.service: data-factory
ms.topic: conceptual
author: dcstwh
ms.author: weetok
ms.date: 09/08/2020
ms.openlocfilehash: 704360ac37e016de9efe2248181f7db358f5a7cf
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100371480"
---
# <a name="visual-authoring-in-azure-data-factory"></a>Visuelles Erstellen in Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Mithilfe der Azure Data Factory-Benutzeroberfläche (UX) können Sie Ressourcen für Ihre Data Factory visuell erstellen und bereitstellen, ohne Code schreiben zu müssen. Sie können Aktivitäten auf eine Pipelinecanvas ziehen, Testläufe ausführen, iterativ debuggen sowie Ihre Pipelineausführungen bereitstellen und überwachen.

Die Azure Data Factory-Benutzererfahrung wird zurzeit nur unter Microsoft Edge und Google Chrome unterstützt.

## <a name="authoring-canvas"></a>Erstellungsbereich

Um den **Erstellungsbereich** zu öffnen, klicken Sie auf das Stiftsymbol. 

![Erstellungsbereich](media/author-visually/authoring-canvas.png)

Hier erstellen Sie die Pipelines, Aktivitäten, Datasets, verknüpften Dienste, Datenflüsse, Trigger und Integrationslaufzeiten, aus denen Ihre Factory besteht. Informationen zum Einstieg in das Erstellen einer Pipeline im Erstellungsbereich finden Sie unter [Kopieren von Daten mit der Kopieraktivität](tutorial-copy-data-portal.md). 

Die standardmäßige visuelle Erstellungsumgebung arbeitet direkt mit dem Data Factory-Dienst zusammen. Die Integration in Azure Repos Git oder GitHub wird ebenfalls unterstützt, um die Versionskontrolle und Zusammenarbeit an Ihren Data Factory-Pipelines zu ermöglichen. Weitere Informationen zu den Unterschieden dieser Erstellungsumgebungen finden Sie unter [Quellcodeverwaltung in Azure Data Factory](source-control.md).

### <a name="properties-pane"></a>Eigenschaftenbereich

Bei Ressourcen der obersten Ebene, z. B Pipelines, Datasets und Datenflüssen, können allgemeine Eigenschaften im Eigenschaftenbereich auf der rechten Seite der Canvas bearbeitet werden. Der Eigenschaftenbereich enthält Eigenschaften wie Name, Beschreibung, Anmerkungen und andere allgemeine Eigenschaften. Unterressourcen, z. B. Pipelineaktivitäten und Datenflusstransformationen, werden in dem Bereich unten in der Canvas bearbeitet. 

![Eigenschaftenbereich](media/author-visually/properties-pane.png)

Der Eigenschaftenbereich wird standardmäßig nur bei der Ressourcenerstellung geöffnet. Wenn Sie ihn bearbeiten möchten, klicken Sie oben rechts im Erstellungsbereich auf das Symbol für den Eigenschaftenbereich.

### <a name="related-resources"></a>Verwandte Ressourcen

Im Bereich „Eigenschaften“ können Sie sehen, welche Ressourcen von der ausgewählten Ressource abhängig sind, indem Sie die Registerkarte **Verknüpft** auswählen. Hier werden alle Ressourcen aufgeführt, die auf die aktuelle Ressource verweisen.

![Verwandte Ressourcen](media/author-visually/related-resources.png)

Beispielsweise verwenden in der obigen Abbildung eine Pipeline und zwei Datenflüsse das aktuell ausgewählte Dataset.

## <a name="management-hub"></a>Verwaltungshub

Der Verwaltungshub, den Sie in der Azure Data Factory-Benutzeroberfläche über die Registerkarte *Verwalten* aufrufen, ist ein Portal mit globalen Verwaltungsaktionen für Ihre Data Factory. Hier können Sie die Verbindungen mit Datenspeichern und externen Compute-Umgebungen, die Konfiguration der Quellcodeverwaltung und die Triggereinstellungen verwalten. Weitere Informationen zu den Funktionen finden Sie unter [Verwaltungshub](author-management-hub.md).

![Verwalten von verknüpften Diensten](media/author-management-hub/management-hub-linked-services.png)

## <a name="expressions-and-functions"></a>Ausdrücke und Funktionen

Anstelle statischer Werte können Sie Ausdrücke und Funktionen verwenden, um viele Eigenschaften in Azure Data Factory anzugeben.

Zum Angeben eines Ausdrucks für einen Eigenschaftswert wählen Sie **Dynamischen Inhalt hinzufügen** aus oder klicken auf **ALT+P**, wenn sich der Eingabefokus auf dem Feld befindet.

![Dynamischen Inhalt hinzufügen](media/author-visually/dynamic-content-1.png)

Dadurch wird der **Ausdrucks-Generator von Data Factory** geöffnet, in dem Sie Ausdrücke anhand von unterstützten Systemvariablen, Aktivitätsausgaben, Funktionen und benutzerdefinierten Variablen oder Parametern erstellen können. 

![Ausdrucks-Generator](media/author-visually/dynamic-content-2.png)

Informationen zur Ausdruckssprache finden Sie unter [Ausdrücke und Funktionen in Azure Data Factory](control-flow-expression-language-functions.md).

## <a name="provide-feedback"></a>Feedback geben

Wählen Sie **Feedback** aus, um Kommentare zu Funktionen abzugeben oder um Microsoft Probleme mit dem Tool zu melden:

![Feedback](media/author-visually/provide-feedback.png)

## <a name="next-steps"></a>Nächste Schritte

Unter [Programmgesteuertes Überwachen und Verwalten von Pipelines](monitor-programmatically.md) erfahren Sie mehr über das Überwachen und Verwalten von Pipelines.
