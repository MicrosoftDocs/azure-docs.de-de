---
title: Überprüfen von Durchsatz- und Latenzmetriken für ein Speicherkonto im Azure-Portal | Microsoft Docs
description: Hier erfahren Sie, wie Sie Durchsatz- und Latenzmetriken für ein Speicherkonto im Portal überprüfen.
author: roygara
ms.service: storage
ms.topic: tutorial
ms.date: 02/20/2018
ms.author: rogarana
ms.subservice: blobs
ms.openlocfilehash: 708a2369e5ba21f47f3d3fea509654b0385363f7
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95912313"
---
# <a name="verify-throughput-and-latency-metrics-for-a-storage-account"></a>Überprüfen von Durchsatz- und Latenzmetriken für ein Speicherkonto

Dieses Tutorial ist der vierte und letzte Teil einer Serie. In den vorherigen Tutorials wurde das Hoch- und Herunterladen großer Mengen von Zufallsdaten in ein Azure-Speicherkonto erörtert. In diesem Tutorial wird gezeigt, wie Sie Metriken verwenden können, um den Durchsatz und die Latenz im Azure-Portal anzuzeigen.

Im vierten Teil der Serie lernen Sie Folgendes:

> [!div class="checklist"]
> * Konfigurieren von Diagrammen im Azure-Portal
> * Überprüfen von Durchsatz- und Latenzmetriken

[Azure-Speichermetrik](./monitor-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) verwendet Azure Monitor, um einen einheitlichen Einblick in die Leistung und Verfügbarkeit Ihres Speicherkontos zu bieten.

## <a name="configure-metrics"></a>Konfigurieren von Metriken

Navigieren Sie in Ihrem Speicherkonto unter **EINSTELLUNGEN** zu **Metriken (Vorschau)** .

Wählen Sie in der Dropdownliste **SUBDIENST** den Eintrag „Blob“ aus.

Wählen Sie unter **METRIK** eine der in der folgenden Tabelle aufgeführten Metriken aus:

Die folgenden Metriken vermitteln Ihnen einen Eindruck von der Latenz und dem Durchsatz der Anwendung. Die Metriken, die Sie im Portal konfigurieren, liegen bei 1 Minute im Durchschnitt. Wenn eine Transaktion nach einer halben Minute abgeschlossen ist, wird dieser Minutenwert für den Durchschnitt halbiert. In der Anwendung wurden die Upload- und Downloadvorgänge zeitlich getaktet, und sie lieferten die Ausgabe der tatsächlichen Zeit, die für das Hoch- und Herunterladen der Dateien benötigt wurde. Diese Informationen können in Verbindung mit den Metriken im Portal verwendet werden, um den Durchsatz vollständig nachzuvollziehen.

|Metrik|Definition|
|---|---|
|**E2E-Wartezeit bei Erfolg**|Die durchschnittliche End-to-End-Wartezeit (in Millisekunden) bei erfolgreichen Anforderungen, die an einen Speicherdienst oder an den angegebenen API-Vorgang gerichtet wurden. Dieser Wert umfasst die erforderliche Verarbeitungszeit in Azure Storage für das Lesen der Anforderung, das Senden der Antwort und das Empfangen der Antwortbestätigung.|
|**Serverwartezeit bei Erfolg**|Die durchschnittliche Verarbeitungszeit einer erfolgreichen Anforderung durch Azure Storage. Dieser Wert beinhaltet nicht die in „SuccessE2ELatency“ angegebene Netzwerklatenz. |
|**Transaktionen**|Die Anzahl von Anforderungen, die an einen Speicherdienst oder an den angegebenen API-Vorgang gerichtet wurden. Diese Anzahl umfasst erfolgreiche und nicht erfolgreiche Anforderungen sowie Anforderungen, die zu Fehlern geführt haben. In dem Beispiel wurde die Blockgröße auf 100 MB festgelegt. In diesem Fall wird jeder 100-MB-Block als Transaktion betrachtet.|
|**Eingehend**|Die eingehende Datenmenge. Dieser Wert umfasst an Azure Storage gerichtete eingehende Daten von einem externen Client sowie eingehende Daten innerhalb von Azure. |
|**Ausgehend**|Die ausgehende Datenmenge. Dieser Wert umfasst an Azure Storage gerichtete ausgehende Daten von einem externen Client sowie ausgehende Daten innerhalb von Azure. Der Wert stellt somit keine gebührenpflichtigen ausgehenden Daten dar. |

Wählen Sie neben **Zeit** die Option **Letzte 24 Stunden (Automatisch)** aus. Wählen Sie **Letzte Stunde** und **Minute** für **Zeitgranularität** aus, und klicken Sie dann auf **Übernehmen**.

![Speicherkontometriken](./media/storage-blob-scalable-app-verify-metrics/figure1.png)

Diagrammen können mehrere Metriken zugewiesen sein, aber die Zuweisung von mehr als einer Metrik schließt die Möglichkeit aus, nach Dimensionen zu gruppieren.

## <a name="dimensions"></a>Dimensionen

[Dimensionen](./monitor-blob-storage-reference.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#metrics-dimensions) werden verwendet, um einen tieferen Einblick in die Diagramme und detailliertere Informationen zu erhalten. Unterschiedliche Metriken weisen unterschiedliche Dimensionen auf. Eine verfügbare Dimension ist die Dimension **API-Name**. Diese Dimension bricht das Diagramm in jeden einzelnen API-Aufruf auf. Die erste Abbildung unten enthält ein Beispieldiagramm der gesamten Transaktionen für ein Speicherkonto. In der zweiten Abbildung ist das gleiche Diagramm dargestellt. Hier ist jedoch die Dimension „API-Name“ ausgewählt. Wie Sie sehen können, ist jede Transaktion aufgeführt und gibt weitere Details darüber an, wie viele Aufrufe von „API-Name“ vorgenommen wurden.

![Speicherkontometriken – Transaktionen ohne Dimension](./media/storage-blob-scalable-app-verify-metrics/transactionsnodimensions.png)

![Speicherkontometriken – Transaktionen](./media/storage-blob-scalable-app-verify-metrics/transactions.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Ressourcengruppe, VM und alle zugehörigen Ressourcen nicht mehr benötigt werden, löschen Sie sie. Wählen Sie hierzu die Ressourcengruppe für die VM aus, und klicken Sie auf „Löschen“.

## <a name="next-steps"></a>Nächste Schritte

Im vierten Teil der Serie haben Sie gelernt, wie Sie Metriken für die Beispiellösung anzeigen können. Und Sie haben beispielsweise Folgendes gelernt:

> [!div class="checklist"]
> * Konfigurieren von Diagrammen im Azure-Portal
> * Überprüfen von Durchsatz- und Latenzmetriken

Unter diesem Link finden Sie vordefinierte Speicherbeispiele.

> [!div class="nextstepaction"]
> [Beispielskripts für Azure Storage](storage-samples-blobs-cli.md)

[previous-tutorial]: storage-blob-scalable-app-download-files.md