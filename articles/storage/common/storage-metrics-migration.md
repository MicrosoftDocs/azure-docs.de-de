---
title: Wechseln von Storage Analytics-Metriken zu Azure Monitor-Metriken | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie von Storage Analytics Metriken (klassische Metriken) zu Metriken in Azure Monitor wechseln.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 10/20/2020
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.custom: monitoring
ms.openlocfilehash: 4a4624a94a27f00201c55a320f1745783b06d169
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92781922"
---
# <a name="transition-to-metrics-in-azure-monitor"></a>Wechseln zu Metriken in Azure Monitor

Am **31. August 2023** werden Storage Analytics-Metriken (auch als *klassische Metriken* bezeichnet) eingestellt. Weitere Informationen finden Sie in der [offiziellen Ankündigung](https://azure.microsoft.com/updates/azure-storage-classic-metrics-will-be-retired-on-31-august-2023/). Wenn Sie klassische Metriken verwenden, sollten Sie unbedingt vor diesem Datum zu den Metriken in Azure Monitor wechseln. Dieser Artikel hilft Ihnen bei der Umstellung. 

## <a name="steps-to-complete-the-transition"></a>Schritte zum Durchführen der Umstellung

Für den Übergang zu Metriken in Azure Monitor wird folgende Vorgehensweise empfohlen.

1. Informieren Sie sich über einige der [wichtigsten Unterschiede](#key-differences-between-classic-metrics-and-metrics-in-azure-monitor) zwischen klassischen Metriken und Metriken in Azure Monitor. 

2. Stellen Sie eine Liste der klassischen Metriken zusammen, die Sie zurzeit verwenden.

3. Ermitteln Sie, [welche Metriken in Azure Monitor](#metrics-mapping-between-old-metrics-and-new-metrics) die gleichen Daten wie die derzeit verwendeten Metriken bereitstellen. 
   
4. Erstellen Sie [Diagramme](/learn/modules/gather-metrics-blob-storage/2-viewing-blob-metrics-in-azure-portal) oder [Dashboards](/learn/modules/gather-metrics-blob-storage/4-using-dashboards-in-the-azure-portal), um die Metrikdaten anzuzeigen.

   > [!NOTE]
   > Metriken in Azure Monitor sind standardmäßig aktiviert. Sie müssen also nichts tun, um die Erfassung von Metriken zu starten. Sie müssen jedoch Diagramme oder Dashboards erstellen, um diese Metriken anzuzeigen. 
 
5. Wenn Sie Warnungsregeln erstellt haben, die auf klassischen Speichermetriken basieren, [erstellen Sie Warnungsregeln](../../azure-monitor/platform/alerts-overview.md), die auf Metriken in Azure Monitor basieren. 

6. Nachdem alle Ihre Metriken in Azure Monitor angezeigt werden, können Sie die klassische Protokollierung deaktivieren. 

<a id="key-differences-between-classic-metrics-and-metrics-in-azure-monitor"></a>

## <a name="classic-metrics-vs-metrics-in-azure-monitor"></a>Vergleich klassischer Metriken mit Metriken in Azure Monitor

In diesem Abschnitt werden einige wichtige Unterschiede zwischen diesen beiden Metrikplattformen beschrieben.

Der Hauptunterschied besteht darin, wie die Metriken verwaltet werden. Klassische Metriken werden von Azure Storage verwaltet, während Metriken in Azure Monitor von Azure Monitor verwaltet werden. Bei klassischen Metriken sammelt Azure Storage Metrikwerte, aggregiert sie und speichert sie dann in Tabellen, die sich im Speicherkonto befinden. Für Metriken in Azure Monitor sendet Azure Storage die Metrikdaten an das Azure Monitor-Back-End. Azure Monitor bietet eine einheitliche Überwachungsoberfläche, auch für Daten aus dem Azure-Portal sowie für erfasste Daten. 

Klassische Metriken werden an ein Azure-Speicherkonto gesendet und in diesem gespeichert. Azure Monitor-Metriken können an mehrere Speicherorte gesendet werden. Ein Speicherkonto kann einer dieser Speicherorte sein, dies ist jedoch nicht erforderlich.  

Mit Blick auf die unterstützten Metriken bieten klassische Metriken nur Werte zur **Kapazität** für Azure Blob Storage. Die Metriken in Azure Monitor umfassen Kapazitätsmetriken für Blobs, Tabellen, Dateien, Warteschlangen und Storage Premium. Klassische Metriken bieten **Transaktionsmetriken** für Blob-, Tabellen-, Azure Files- und Queue Storage. Metriken in Azure Monitor ergänzen diese Liste noch um Storage Premium.

Wenn die Aktivität in Ihrem Konto keine Metrik auslöst, wird bei klassischen Metriken für diese Metrik der Wert null (0) angezeigt. Bei den Metriken in Azure Monitor werden diese Daten vollständig ausgelassen. Dies führt zu übersichtlicheren Berichten. Wenn beispielsweise bei klassischen Metriken keine Timeoutfehler auf dem Server gemeldet werden, wird der Wert `ServerTimeoutError` in der Metrikentabelle auf „0“ festgelegt. Azure Monitor gibt keine Daten zurück, wenn Sie den Wert der Metrik `Transactions` mit einer Dimension `ResponseType` abfragen, die `ServerTimeoutError` entspricht. 

Weitere Informationen zu Metriken in Azure Monitor finden Sie unter [Metriken in Azure Monitor](../../azure-monitor/platform/data-platform-metrics.md).

<a id="metrics-mapping-between-old-metrics-and-new-metrics"></a>

## <a name="map-classic-metrics-to-metrics-in-azure-monitor"></a>Zuordnen klassischer Metriken zu Metriken in Azure Monitor

 Ermitteln Sie anhand der folgenden Tabellen, welche Metriken in Azure Monitor dieselben Daten wie die derzeit verwendeten Metriken bereitstellen. 

**Kapazitätsmetriken**

| Klassische Metrik | Metrik in Azure Monitor |
| ------------------- | ----------------- |
| `Capacity`            | `BlobCapacity` mit `BlockBlob` oder `PageBlob` als `BlobType`-Dimension |
| `ObjectCount`        | `BlobCount` mit `BlockBlob` oder `PageBlob` als `BlobType`-Dimension |
| `ContainerCount`      | `ContainerCount` |

> [!NOTE]
> Es gibt auch mehrere neue Kapazitätsmetriken, die als klassische Metriken nicht zur Verfügung standen. Eine vollständige Liste finden Sie unter [Metriken](../blobs/monitor-blob-storage-reference.md#metrics).

**Transaktionsmetriken**

| Klassische Metrik | Metrik in Azure Monitor |
| ------------------- | ----------------- |
| `AnonymousAuthorizationError` | Transaktionen mit der `ResponseType`-Dimension `AuthorizationError` und der `Authentication`-Dimension `Anonymous` |
| `AnonymousClientOtherError` | Transaktionen mit der `ResponseType`-Dimension `ClientOtherError` und der `Authentication`-Dimension `Anonymous` |
| `AnonymousClientTimeoutError` | Transaktionen mit der `ResponseType`-Dimension `ClientTimeoutError` und der `Authentication`-Dimension `Anonymous` |
| `AnonymousNetworkError` | Transaktionen mit der `ResponseType`-Dimension `NetworkError` und der `Authentication`-Dimension `Anonymous` |
| `AnonymousServerOtherError` | Transaktionen mit der `ResponseType`-Dimension `ServerOtherError` und der `Authentication`-Dimension `Anonymous` |
| `AnonymousServerTimeoutError` | Transaktionen mit der `ResponseType`-Dimension `ServerTimeoutError` und der `Authentication`-Dimension `Anonymous` |
| `AnonymousSuccess` | Transaktionen mit der `ResponseType`-Dimension `Success` und der `Authentication`-Dimension `Anonymous` |
| `AnonymousThrottlingError` | Transaktionen mit der `ResponseType`-Dimension `ClientThrottlingError` oder `ServerBusyError` und der `Authentication`-Dimension `Anonymous` |
| `AuthorizationError` | Transaktionen mit der `ResponseType`-Dimension `AuthorizationError` |
| `Availability` | `Availability` |
| `AverageE2ELatency` | `SuccessE2ELatency` |
| `AverageServerLatency` | `SuccessServerLatency` |
| `ClientOtherError` | Transaktionen mit der `ResponseType`-Dimension `ClientOtherError` |
| `ClientTimeoutError` | Transaktionen mit der `ResponseType`-Dimension `ClientTimeoutError` |
| `NetworkError` | Transaktionen mit der `ResponseType`-Dimension `NetworkError` |
| `PercentAuthorizationError` | Transaktionen mit der `ResponseType`-Dimension `AuthorizationError` |
| `PercentClientOtherError` | Transaktionen mit der `ResponseType`-Dimension `ClientOtherError` |
| `PercentNetworkError` | Transaktionen mit der `ResponseType`-Dimension `NetworkError` |
| `PercentServerOtherError` | Transaktionen mit der `ResponseType`-Dimension `ServerOtherError` |
| `PercentSuccess` | Transaktionen mit der `ResponseType`-Dimension `Success` |
| `PercentThrottlingError` | Transaktionen mit der `ResponseType`-Dimension `ClientThrottlingError` oder `ServerBusyError` |
| `PercentTimeoutError` | Transaktionen mit der `ResponseType`-Dimension `ServerTimeoutError` oder der `ResponseType`-Dimension `ClientTimeoutError` |
| `SASAuthorizationError` | Transaktionen mit der `ResponseType`-Dimension `AuthorizationError` und der `Authentication`-Dimension `SAS` |
| `SASClientOtherError` | Transaktionen mit der `ResponseType`-Dimension `ClientOtherError` und der `Authentication`-Dimension `SAS` |
| `SASClientTimeoutError` | Transaktionen mit der `ResponseType`-Dimension `ClientTimeoutError` und der `Authentication`-Dimension `SAS` |
| `SASNetworkError` | Transaktionen mit der `ResponseType`-Dimension `NetworkError` und der `Authentication`-Dimension `SAS` |
| `SASServerOtherError` | Transaktionen mit der `ResponseType`-Dimension `ServerOtherError` und der `Authentication`-Dimension `SAS` |
| `SASServerTimeoutError` | Transaktionen mit der `ResponseType`-Dimension `ServerTimeoutError` und der `Authentication`-Dimension `SAS` |
| `SASSuccess` | Transaktionen mit der `ResponseType`-Dimension `Success` und der `Authentication`-Dimension `SAS` |
| `SASThrottlingError` | Transaktionen mit der `ResponseType`-Dimension `ClientThrottlingError` oder `ServerBusyError` und der `Authentication`-Dimension `SAS` |
| `ServerOtherError` | Transaktionen mit der `ResponseType`-Dimension `ServerOtherError` |
| `ServerTimeoutError` | Transaktionen mit der `ResponseType`-Dimension `ServerTimeoutError` |
| `Success` | Transaktionen mit der `ResponseType`-Dimension `Success` |
| `ThrottlingError` | `Transactions` mit `ClientThrottlingError` oder `ServerBusyError` als `ResponseType`-Dimension|
| `TotalBillableRequests` | `Transactions` |
| `TotalEgress` | `Egress` |
| `TotalIngress` | `Ingress` |
| `TotalRequests` | `Transactions` |

## <a name="next-steps"></a>Nächste Schritte

* [Azure Monitor](../../azure-monitor/overview.md)