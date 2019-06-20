---
title: Migration von Azure Storage-Metriken | Microsoft-Dokumentation
description: Erfahren Sie, wie alte Metriken zu den neuen von Azure Monitor verwalteten Metriken migriert werden.
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 03/30/2018
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: 58ac15c1aba715c9a5b67e723401b531e76608b2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65153598"
---
# <a name="azure-storage-metrics-migration"></a>Migration von Azure Storage-Metriken

In Übereinstimmung mit der Strategie zur Vereinheitlichung der Monitor-Oberfläche in Azure integriert Azure Storage Metriken für die Azure Monitor-Plattform. Der Dienst mit den alten Metriken wird in Zukunft mit einer rechtzeitigen Benachrichtigung gemäß der Azure-Richtlinie eingestellt. Wenn Sie alte Speichermetriken benötigen, müssen Sie vor dem Enddatum des Diensts eine Migration durchführen, um Ihre Metrikdaten beizubehalten.

In diesem Artikel wird erläutert, wie Sie die alten Metriken zu den neuen Metriken migrieren.

## <a name="understand-old-metrics-that-are-managed-by-azure-storage"></a>Informationen zu den von Azure Storage verwalteten alten Metriken

Azure Storage sammelt alte Metrikwerte und aggregiert und speichert diese in $Metric-Tabellen im selben Speicherkonto. Sie können das Azure-Portal verwenden, um ein Überwachungsdiagramm einzurichten. Sie können auch die Azure Storage SDKs verwenden, um die Daten aus $Metric-Tabellen zu lesen, die auf dem Schema basieren. Weitere Informationen finden Sie unter [Storage Analytics](./storage-analytics.md).

Alte Metriken bieten Kapazitätsmetriken nur für Azure-BLOB-Speicher. Alte Metriken bieten Transaktionsmetriken für BLOB-Speicher, Tabellenspeicher, Azure Files und Warteschlangenspeicher.

Alte Metriken sind im Flatfile-Schema entworfen. Wenn die Metrik nicht durch die Datenverkehrsmuster ausgelöst wird, weist der Entwurf einen Metrikwert von Null auf. Beispielsweise ist der Wert **ServerTimeoutError** in $Metric-Tabellen auf „0“ festgelegt, auch wenn Sie keine Servertimeoutfehler beim Livedatenverkehr zu einem Speicherkonto erhalten.

## <a name="understand-new-metrics-managed-by-azure-monitor"></a>Informationen zu den von Azure Monitor verwalteten neuen Metriken

Für neue Speichermetriken gibt Azure Storage die Metrikdaten an das Azure Monitor-Back-End aus. Azure Monitor bietet eine einheitliche Überwachungsoberfläche, auch für Daten aus dem Portal sowie für die Datenerfassung. Weitere Einzelheiten finden Sie in [diesem Artikel](../../monitoring-and-diagnostics/monitoring-overview-metrics.md).

Neue Metriken bieten Kapazitäts- und Transaktionsmetriken für Blobs, Tabellen, Dateien, Warteschlangen und Storage Premium.

Das Feature für mehrere Dimensionen ist eines der von Azure Monitor bereitgestellten Features. Azure Storage übernimmt den Entwurf bei der Definition des Schemas für neue Metriken. Informationen zu unterstützten Dimensionen für Metriken finden Sie unter [Azure Storage-Metriken in Azure Monitor](./storage-metrics-in-azure-monitor.md). Ein mehrdimensionaler Entwurf bietet Kosteneffizienz sowohl im Hinblick auf den Bandbreitenverbrauch bei der Erfassung als auch im Hinblick auf die Kapazität bei der Speicherung von Metriken. Wenn Ihr Datenverkehr keine entsprechenden Metriken ausgelöst hat, werden die zugehörigen Metrikdaten infolgedessen nicht generiert. Beispiel: Wenn Ihr Datenverkehr keine Servertimeoutfehler ausgelöst hat, gibt Azure Monitor keine Daten zurück, wenn Sie den Wert der Metrik **Transactions** mit der Dimension **ResponseType** gleich **ServerTimeoutError** abfragen.

## <a name="metrics-mapping-between-old-metrics-and-new-metrics"></a>Zuordnung von Metriken zwischen alten und neuen Metriken

Wenn Sie Metrikdaten programmgesteuert lesen, müssen Sie das Schema für neue Metriken in Ihren Programmen verwenden. Zur Verdeutlichung der Änderungen wird in der folgenden Tabelle die entsprechende Zuordnung aufgeführt:

**Kapazitätsmetriken**

| Alte Metrik | Neue Metrik |
| ------------------- | ----------------- |
| **Capacity**            | **BlobCapacity** mit der Dimension **BlobType** gleich **BlockBlob** oder **PageBlob** |
| **ObjectCount**        | **BlobCount** mit der Dimension **BlobType** gleich **BlockBlob** oder **PageBlob** |
| **ContainerCount**      | **ContainerCount** |

Die folgenden Metriken sind neue Angebote, die von den alten Metriken nicht unterstützt werden:
* **TableCapacity**
* **TableCount**
* **TableEntityCount**
* **QueueCapacity**
* **QueueCount**
* **QueueMessageCount**
* **FileCapacity**
* **FileCount**
* **FileShareCount**
* **UsedCapacity**

**Transaktionsmetriken**

| Alte Metrik | Neue Metrik |
| ------------------- | ----------------- |
| **AnonymousAuthorizationError** | Transaktionen mit der Dimension **ResponseType** gleich **AuthorizationError** und der Dimension **Authentication** gleich **Anonymous** |
| **AnonymousClientOtherError** | Transaktionen mit der Dimension **ResponseType** gleich **ClientOtherError** und der Dimension **Authentication** gleich **Anonymous** |
| **AnonymousClientTimeoutError** | Transaktionen mit Dimension **ResponseType** gleich **ClientTimeoutError** und Dimension **Authentication** gleich **Anonymous** |
| **AnonymousNetworkError** | Transaktionen mit der Dimension **ResponseType** gleich **NetworkError** und der Dimension **Authentication** gleich **Anonymous** |
| **AnonymousServerOtherError** | Transaktionen mit der Dimension **ResponseType** gleich **ServerOtherError** und der Dimension **Authentication** gleich **Anonymous** |
| **AnonymousServerTimeoutError** | Transaktionen mit Dimension **ResponseType** gleich **ServerTimeoutError** und Dimension **Authentication** gleich **Anonymous** |
| **AnonymousSuccess** | Transaktionen mit der Dimension **ResponseType** gleich **Success** und der Dimension **Authentication** gleich **Anonymous** |
| **AnonymousThrottlingError** | Transaktionen mit der Dimension **ResponseType** gleich **ClientThrottlingError** oder **ServerBusyError** und der Dimension **Authentication** gleich **Anonymous** |
| **AuthorizationError** | Transaktionen mit der Dimension **ResponseType** gleich **AuthorizationError** |
| **Verfügbarkeit** | **Verfügbarkeit** |
| **AverageE2ELatency** | **SuccessE2ELatency** |
| **AverageServerLatency** | **SuccessServerLatency** |
| **ClientOtherError** | Transaktionen mit der Dimension **ResponseType** gleich **ClientOtherError** |
| **ClientTimeoutError** | Transaktionen mit der Dimension **ResponseType** gleich **ClientTimeoutError** |
| **NetworkError** | Transaktionen mit der Dimension **ResponseType** gleich **NetworkError** |
| **PercentAuthorizationError** | Transaktionen mit der Dimension **ResponseType** gleich **AuthorizationError** |
| **PercentClientOtherError** | Transaktionen mit der Dimension **ResponseType** gleich **ClientOtherError** |
| **PercentNetworkError** | Transaktionen mit der Dimension **ResponseType** gleich **NetworkError** |
| **PercentServerOtherError** | Transaktionen mit der Dimension **ResponseType** gleich **ServerOtherError** |
| **PercentSuccess** | Transaktionen mit der Dimension **ResponseType** gleich **Success** |
| **PercentThrottlingError** | Transaktionen mit der Dimension **ResponseType** gleich **ClientThrottlingError** oder **ServerBusyError** |
| **PercentTimeoutError** | Transaktionen mit der Dimension **ResponseType** gleich **ServerTimeoutError** oder **ResponseType** gleich **ClientTimeoutError** |
| **SASAuthorizationError** | Transaktionen mit der Dimension **ResponseType** gleich **AuthorizationError** und der Dimension **Authentication** gleich **SAS** |
| **SASClientOtherError** | Transaktionen mit der Dimension **ResponseType** gleich **ClientOtherError** und der Dimension **Authentication** gleich **SAS** |
| **SASClientTimeoutError** | Transaktionen mit der Dimension **ResponseType** gleich **ClientTimeoutError** und der Dimension **Authentication** gleich **SAS** |
| **SASNetworkError** | Transaktionen mit der Dimension **ResponseType** gleich **NetworkError** und der Dimension **Authentication** gleich **SAS** |
| **SASServerOtherError** | Transaktionen mit der Dimension **ResponseType** gleich **ServerOtherError** und der Dimension **Authentication** gleich **SAS** |
| **SASServerTimeoutError** | Transaktionen mit der Dimension **ResponseType** gleich **ServerTimeoutError** und der Dimension **Authentication** gleich **SAS** |
| **SASSuccess** | Transaktionen mit der Dimension **ResponseType** gleich **Success** und der Dimension **Authentication** gleich **SAS** |
| **SASThrottlingError** | Transaktionen mit der Dimension **ResponseType** gleich **ClientThrottlingError** oder **ServerBusyError** und der Dimension **Authentication** gleich **SAS** |
| **ServerOtherError** | Transaktionen mit der Dimension **ResponseType** gleich **ServerOtherError** |
| **ServerTimeoutError** | Transaktionen mit der Dimension **ResponseType** gleich **ServerTimeoutError** |
| **Erfolgreich** | Transaktionen mit der Dimension **ResponseType** gleich **Success** |
| **ThrottlingError** | **Transaktionen** mit der Dimension **ResponseType** gleich **ClientThrottlingError** oder **ServerBusyError**|
| **TotalBillableRequests** | **Transaktionen** |
| **TotalEgress** | **Ausgehend** |
| **TotalIngress** | **Eingehend** |
| **TotalRequests** | **Transaktionen** |

## <a name="faq"></a>Häufig gestellte Fragen

### <a name="how-should-i-migrate-existing-alert-rules"></a>Wie sollte ich vorhandene Warnungsregeln migrieren?

Wenn Sie klassische Warnungsregeln basierend auf alten Speichermetriken erstellt haben, müssen Sie neue Warnungsregeln basierend auf dem Schema für neue Metriken erstellen.

### <a name="is-new-metric-data-stored-in-the-same-storage-account-by-default"></a>Werden neue Metrikdaten standardmäßig im selben Speicherkonto gespeichert?

Nein. Wenn Sie die Metrikdaten in einem Speicherkonto archivieren möchten, verwenden Sie die [Azure Monitor Diagnostic Setting API](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings/createorupdate) (Diagnoseeinstellung-Anwendungsprogrammierschnittstelle in Azure Monitor).

## <a name="next-steps"></a>Nächste Schritte

* [Azure Monitor](../../monitoring-and-diagnostics/monitoring-overview.md)
* [Storage-Metriken in Azure Monitor](./storage-metrics-in-azure-monitor.md)
