---
title: Erstellen von ereignisbasierten Triggern in Azure Data Factory | Microsoft-Dokumentation
description: Erfahren Sie, wie in Azure Data Factory ein Trigger erstellt wird, der eine Pipeline als Reaktion auf ein Ereignis ausführt.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/18/2018
author: sharonlo101
ms.author: shlo
manager: craigg
ms.openlocfilehash: 94c9c3f997143d72262c1ba3d8dbfea90d6f920c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61347693"
---
# <a name="create-a-trigger-that-runs-a-pipeline-in-response-to-an-event"></a>Erstellen eines Triggers, der eine Pipeline als Reaktion auf ein Ereignis ausführt

Dieser Artikel beschreibt die ereignisbasierten Trigger, die Sie in Ihren Data Factory-Pipelines erstellen können.

Die ereignisgesteuerte Architektur (EDA) ist ein allgemeines Datenintegrationsmuster, das die Produktion, Erkennung, Verbrauch und Reaktion auf Ereignisse beinhaltet. In Datenintegrationsszenarien müssen Data Factory-Kunden häufig Pipelines basierend auf Ereignissen auslösen. Data Factory und [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) sind jetzt integriert, sodass Sie bei einem Ereignis Pipelines auslösen können.

Das folgende Video enthält eine zehnminütige Einführung und Demonstration dieses Features:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Event-based-data-integration-with-Azure-Data-Factory/player]


> [!NOTE]
> Die in diesem Artikel beschriebene Integration basiert auf [Azure Event Grid](https://azure.microsoft.com/services/event-grid/). Stellen Sie sicher, dass Ihr Abonnement für den Event Grid-Ressourcenanbieter registriert ist. Weitere Informationen finden Sie unter [Ressourcenanbieter und -typen](../azure-resource-manager/resource-manager-supported-services.md#azure-portal).

## <a name="data-factory-ui"></a>Data Factory-Benutzeroberfläche

### <a name="create-a-new-event-trigger"></a>Erstellen eines neuen Triggers

Ein typisches Ereignis ist der Eingang einer Datei oder das Löschen einer Datei in Ihrem Azure-Speicherkonto. Sie können einen Trigger erstellen, der auf dieses Ereignis in Ihrer Data Factory-Pipeline reagiert.

> [!NOTE]
> Diese Integration unterstützt nur Storage-Konten der Version 2 (Universell).

![Erstellen eines neuen Triggers](media/how-to-create-event-trigger/event-based-trigger-image1.png)

### <a name="configure-the-event-trigger"></a>Konfigurieren des Ereignistriggers

Mit den Eigenschaften **Blob-Pfad beginnt mit** und **Blob-Pfad endet mit** können Sie die Container, Ordner und Blob-Namen angeben, für die Ereignisse empfangen werden sollen. Für die Eigenschaften **Blob-Pfad beginnt mit** und **Blob-Pfad endet mit** können Sie eine Vielzahl von Mustern verwenden, wie in den Beispielen im weiteren Verlauf dieses Artikels dargestellt. Sie müssen mindestens eine dieser Eigenschaften angeben.

![Konfigurieren des Ereignistriggers](media/how-to-create-event-trigger/event-based-trigger-image2.png)

### <a name="select-the-event-trigger-type"></a>Auswählen des Ereignistriggertyps

Sobald die Datei in Ihrem Speicherort eingeht und das entsprechende Blob-Element erstellt wird, löst dieses Ereignis Ihre Data Factory-Pipeline aus und führt sie aus. Sie können einen Trigger erstellen, der auf ein Blob-Erstellungsereignis, ein Blob-Löschereignis oder auf beide Ereignisse in den Data Factory-Pipelines reagiert.

![Auswählen des Triggertyps als Ereignis](media/how-to-create-event-trigger/event-based-trigger-image3.png)

### <a name="map-trigger-properties-to-pipeline-parameters"></a>Zuordnen von Triggereigenschaften zu Pipelineparametern

Wenn ein Ereignistrigger für einen bestimmten Blob ausgelöst wird, erfasst das Ereignis den Ordnerpfad und den Dateinamen des Blobs in den Eigenschaften `@triggerBody().folderPath` und `@triggerBody().fileName`. Um die Werte dieser Eigenschaften in einer Pipeline zu verwenden, müssen Sie die Eigenschaften Pipelineparametern zuordnen. Nach der Zuordnung der Eigenschaften zu Parametern können Sie über den Ausdruck `@pipeline().parameters.parameterName` auf die vom Trigger erfassten Werte in der gesamten Pipeline zugreifen.

![Zuordnung von Eigenschaften zu Pipelineparametern](media/how-to-create-event-trigger/event-based-trigger-image4.png)

Ein Beispiel sehen Sie im obigen Screenshot. Der Trigger ist so konfiguriert, dass er ausgelöst wird, sobald ein Blobpfad mit der Endung `.csv` im Speicherkonto erstellt wird. Wenn ein Blob mit der Erweiterung `.csv` im Speicherkonto erstellt wird, erfassen die Eigenschaften `folderPath` und `fileName` den Speicherort des neuen Blobs. Zum Beispiel weist `@triggerBody().folderPath` einen Wert wie `/containername/foldername/nestedfoldername` und `@triggerBody().fileName` einen Wert wie `filename.csv` auf. Diese Werte werden im Beispiel den Pipelineparametern `sourceFolder` und `sourceFile` zugeordnet. Sie können sie in der gesamten Pipeline als `@pipeline().parameters.sourceFolder` bzw. `@pipeline().parameters.sourceFile` verwenden.

## <a name="json-schema"></a>JSON-Schema

Die folgende Tabelle enthält eine Übersicht über die Schemaelemente, die mit ereignisbasierten Triggern verknüpft sind:

| **JSON-Element** | **Beschreibung** | **Typ** | **Zulässige Werte** | **Erforderlich** |
| ---------------- | --------------- | -------- | ------------------ | ------------ |
| **scope** | Die Ressourcen-ID von Azure Resource Manager im Speicherkonto. | string | Azure Resource Manager-ID | Ja |
| **events** | Der Ereignistyp, die diesen Trigger auslöst. | Array    | Microsoft.Storage.BlobCreated, Microsoft.Storage.BlobDeleted | Ja (beliebige Kombination dieser Werte). |
| **blobPathBeginsWith** | Der Blobpfad muss mit dem angegebenen Muster beginnen, damit der Trigger ausgelöst wird. `/records/blobs/december/` löst den Trigger beispielsweise nur für Blobs im Ordner `december` unter dem Container `records` aus. | string   | | Geben Sie einen Wert für mindestens eine der folgenden Eigenschaften an: `blobPathBeginsWith` oder `blobPathEndsWith`. |
| **blobPathEndsWith** | Der Blobpfad muss mit dem angegebenen Muster enden, damit der Trigger ausgelöst wird. `december/boxes.csv` löst den Trigger beispielsweise nur für Blobs namens `boxes` aus, die sich in einem Ordner namens `december` befinden. | string   | | Geben Sie einen Wert für mindestens eine der folgenden Eigenschaften an: `blobPathBeginsWith` oder `blobPathEndsWith`. |

## <a name="examples-of-event-based-triggers"></a>Beispiele für ereignisbasierte Trigger

Dieser Abschnitt enthält Beispiele für die Einstellungen für ereignisbasierte Trigger.

> [!IMPORTANT]
> Sie müssen das Segment `/blobs/` des Pfads wie in den folgenden Beispielen gezeigt immer dann einbeziehen, wenn Sie Container und Ordner, Container und Datei oder Container, Ordner und Datei angeben.

| Eigenschaft | Beispiel | BESCHREIBUNG |
|---|---|---|
| **Blobpfad beginnt mit** | `/containername/` | Empfängt Ereignisse für ein beliebiges Blob im Container. |
| **Blobpfad beginnt mit** | `/containername/blobs/foldername/` | Empfängt Ereignisse für ein beliebiges Blob im Container `containername` und Ordner `foldername`. |
| **Blobpfad beginnt mit** | `/containername/blobs/foldername/subfoldername/` | Sie können auch auf einen Unterordner verweisen. |
| **Blobpfad beginnt mit** | `/containername/blobs/foldername/file.txt` | Empfängt Ereignisse für ein Blob namens `file.txt` im Ordner `foldername` unter dem Container `containername`. |
| **Blobpfad endet mit** | `file.txt` | Empfängt Ereignisse für ein Blob namens `file.txt` unter einem beliebigen Pfad. |
| **Blobpfad endet mit** | `/containername/blobs/file.txt` | Empfängt Ereignisse für ein Blob namens `file.txt` unter dem Container `containername`. |
| **Blobpfad endet mit** | `foldername/file.txt` | Empfängt Ereignisse für ein Blob namens `file.txt` im Ordner `foldername` unter einem beliebigen Container. |

## <a name="next-steps"></a>Nächste Schritte
Detaillierte Informationen zu Triggern finden Sie unter [Pipelineausführung und -trigger](concepts-pipeline-execution-triggers.md#triggers).
