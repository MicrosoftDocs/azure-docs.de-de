---
title: Erfassen von Protokollen und Metrikdaten mithilfe der Azure-Speicheranalyse | Microsoft-Dokumentation
description: Mit der Speicheranalyse können Sie Metrikdaten für alle Speicherdienste nachverfolgen und Protokolle für Blob-, Warteschlangen- und Tabellenspeicher erfassen.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 03/03/2017
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.custom: monitoring
ms.openlocfilehash: 5465fa63a3dff71de2e370281df9351e2bac76c7
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92790524"
---
# <a name="storage-analytics"></a>Speicheranalyse

Die Azure-Speicheranalyse führt die Protokollierung aus und stellt Metrikdaten für ein Speicherkonto bereit. Mit diesen Daten können Sie Anforderungen verfolgen, Verwendungstrends analysieren und Probleme mit dem Speicherkonto diagnostizieren.

Zum Verwenden der Speicheranalyse müssen Sie sie einzeln für jeden zu überwachenden Dienst aktivieren. Sie können sie über das [Azure-Portal](https://portal.azure.com)aktivieren. Details finden Sie unter [Überwachen eines Speicherkontos im Azure-Portal](storage-monitor-storage-account.md). Sie können die Speicheranalyse auch programmgesteuert über die REST-API oder die Clientbibliothek aktivieren. Über die Vorgänge [Blobdiensteigenschaften festlegen](/rest/api/storageservices/set-blob-service-properties), [Eigenschaften des Warteschlangendiensts festlegen](/rest/api/storageservices/set-queue-service-properties), [Tabellendiensteigenschaften festlegen](/rest/api/storageservices/set-table-service-properties) und [Dateidiensteigenschaften festlegen](/rest/api/storageservices/Get-File-Service-Properties) können Sie Storage Analytics für jeden Dienst aktivieren.

Die aggregierten Daten werden in einem bekannten BLOB (zur Protokollierung) und in bekannten Tabellen (als Metrik) gespeichert. Der Zugriff erfolgt über APIs für den BLOB-Dienst und Tabellendienst.

Bei der Speicheranalyse ist die Menge der gespeicherten Daten auf 20 TB beschränkt. Diese Beschränkung gilt unabhängig vom Gesamtlimit für Ihr Speicherkonto. Weitere Informationen zu Speicherkontogrenzwerten finden Sie unter [Skalierbarkeits- und Leistungsziele für Standardspeicherkonten](scalability-targets-standard-account.md).

Eine ausführliche Anleitung zum Verwenden der Speicheranalyse sowie weiterer Tools, um Azure Storage-bezogene Probleme zu identifizieren, zu diagnostizieren und zu beheben, finden Sie unter [Microsoft Azure Storage: Überwachung, Diagnose und Problembehandlung](storage-monitoring-diagnosing-troubleshooting.md).

## <a name="billing-for-storage-analytics"></a>Abrechnung von Speicheranalysen

Alle Metrikdaten werden von den Diensten eines Speicherkontos geschrieben. Deshalb ist jeder Schreibvorgang, der von der Speicheranalyse ausgeführt wird, gebührenpflichtig. Darüber hinaus ist die Menge des von Metrikdaten genutzten Speichers ebenfalls gebührenpflichtig.

Die folgenden Aktionen der Speicheranalyse sind gebührenpflichtig:

* Anforderungen zum Erstellen von Blobs für die Protokollierung
* Anforderungen zum Erstellen von Tabellenentitäten für Metriken

Wenn Sie eine Datenbeibehaltungsrichtlinie konfiguriert haben und die Speicheranalyse ältere Protokollierungs- und Metrikdaten löscht, werden Ihnen keine Löschtransaktionen in Rechnung gestellt. Löschtransaktionen von einem Client sind jedoch gebührenpflichtig. Weitere Informationen zu Aufbewahrungsrichtlinien finden Sie unter [Festlegen einer Beibehaltungsrichtlinie für Speicheranalysedaten](/rest/api/storageservices/Setting-a-Storage-Analytics-Data-Retention-Policy).

### <a name="understanding-billable-requests"></a>Grundlegendes zu gebührenpflichtigen Anforderungen

Jede Anforderung, die an den Speicherdienst eines Kontos erfolgt, ist entweder gebührenpflichtig oder nicht. Storage Analytics protokolliert jede einzelne an einen Dienst gerichtete Anforderung, einschließlich einer Statusmeldung, die angibt, wie die Anforderung behandelt wurde. Entsprechend speichert die Speicheranalyse Metriken sowohl für einen Dienst als auch für die API-Vorgänge dieses Diensts, einschließlich der Prozentsätze und Anzahl bestimmter Statusmeldungen. Diese gesamten Funktionen können Ihnen helfen, die gebührenpflichtigen Anforderungen zu analysieren, Verbesserungen an der Anwendung vorzunehmen und Probleme bei den Anforderungen an Dienste zu diagnostizieren. Weitere Informationen zur Abrechnung finden Sie unter [Understanding Azure Storage Billing - Bandwidth, Transactions, and Capacity](/archive/blogs/windowsazurestorage/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity)(Grundlagen zur Abrechnung von Azure-Speicher – Bandbreite, Transaktionen und Kapazität, in englischer Sprache).

Wenn Sie Speicheranalysedaten überprüfen, können Sie anhand der Tabellen im Thema [Protokollierte Speicheranalysevorgänge und Statusmeldungen](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) bestimmen, welche Anforderungen gebührenpflichtig sind. Anschließend können Sie die Protokolle und Metrikdaten mit den Statusmeldungen vergleichen, um zu ermitteln, ob Ihnen eine bestimmte Anforderung in Rechnung gestellt wurde. Sie können auch die Tabellen im vorherigen Thema verwenden, um die Verfügbarkeit für einen Speicherdienst oder einen einzelnen API-Vorgang zu bestimmen.

## <a name="next-steps"></a>Nächste Schritte
* [Überwachen eines Speicherkontos im Azure-Portal](storage-monitor-storage-account.md)
* [Metriken der Speicheranalyse](storage-analytics-metrics.md)
* [Protokollierung durch die Speicheranalyse](storage-analytics-logging.md)