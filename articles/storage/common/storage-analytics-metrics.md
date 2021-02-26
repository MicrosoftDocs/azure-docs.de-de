---
title: Metriken von Azure Storage Analytics (klassisch)
description: Hier erfahren Sie, wie Storage Analytics-Metriken in Azure Storage verwendet werden. Informieren Sie sich über Transaktions- und Kapazitätsmetriken, wie Metriken gespeichert und aktiviert werden, und vieles mehr.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 01/29/2021
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.custom: monitoring, devx-track-csharp
ms.openlocfilehash: 8f698aadc24d0dc0691743f1d8dd54c5d5fd287e
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/31/2021
ms.locfileid: "99220955"
---
# <a name="azure-storage-analytics-metrics-classic"></a>Metriken von Azure Storage Analytics (klassisch)

Am **31. August 2023** werden Storage Analytics-Metriken (auch als *klassische Metriken* bezeichnet) eingestellt. Weitere Informationen finden Sie in der [offiziellen Ankündigung](https://azure.microsoft.com/updates/azure-storage-classic-metrics-will-be-retired-on-31-august-2023/). Wenn Sie klassische Metriken verwenden, sollten Sie unbedingt vor diesem Datum zu den Metriken in Azure Monitor wechseln. Dieser Artikel hilft Ihnen bei der Umstellung. 

Azure Storage verwendet die Storage Analytics-Lösung, um Metriken zu speichern, zu denen aggregierte Transaktionsstatistiken und Kapazitätsdaten für die an einen Speicherdienst gesendeten Anforderungen zählen. Transaktionen werden sowohl auf API-Vorgangsebene als auch auf Speicherdienstebene gemeldet. Die Kapazität wird auf der Speicherdienstebene gemeldet. Metrikdaten können für Folgendes verwendet werden:
- Analysieren der Speicherdienstnutzung.
- Diagnose von Problemen mit Anforderungen, die an den Speicherdienste gerichtet wurden.
- Verbessern der Leistung von Anwendungen, die einen Dienst verwenden.

 Metriken der Speicheranalyse sind standardmäßig für neue Speicherkonten aktiviert. Sie können Metriken über das [Azure-Portal](https://portal.azure.com/), mithilfe von PowerShell oder über die Azure-Befehlszeilenschnittstelle konfigurieren. Eine ausführliche Anleitung finden Sie unter [Aktivieren und Verwalten von Azure Storage-Analysemetriken (klassisch)](./storage-monitor-storage-account.md). Sie können die Speicheranalyse auch programmgesteuert über die REST-API oder die Clientbibliothek aktivieren. Verwenden Sie den Vorgang „Diensteigenschaften festlegen“, um Storage Analytics für alle Dienste zu aktivieren.  

> [!NOTE]
> Storage Analytics-Metriken sind für Azure Blob Storage, Azure Queue Storage, Azure-Tabellenspeicher und Azure Files verfügbar.
> Storage Analytics-Metriken sind jetzt klassische Metriken. Wir empfehlen, anstelle von Storage Analytics-Metriken [Azure Storage-Metriken in Azure Monitor](../blobs/monitor-blob-storage.md) zu verwenden.

## <a name="transaction-metrics"></a>Transaktionsmetriken  
 Ein robuster Datensatz wird stündlich oder minütlich für jeden Speicherdienst und jeden angeforderten API-Vorgang aufgezeichnet, einschließlich Ein- und Ausgang, Verfügbarkeit, Fehlern und nach Prozentanteil kategorisierten Anforderungen. Eine vollständige Liste der Transaktionsdetails finden Sie unter [Schema der Tabellen für Storage Analytics-Metriken](/rest/api/storageservices/storage-analytics-metrics-table-schema).  

 Transaktionsdaten werden auf Dienstebene und auf API-Vorgangsebene aufgezeichnet. Auf Dienstebene werden stündlich zusammenfassende Statistiken für alle angeforderten API-Vorgänge in eine Tabellenentität geschrieben, wenn keine Anforderungen für den Dienst gesendet wurden. Auf API-Vorgangsebene werden Statistiken nur dann in eine Entität geschrieben, wenn der Vorgang während der betreffenden Stunde angefordert wurde.  

 Wenn Sie beispielsweise einen **GetBlob**-Vorgang für Ihren Blob-Dienst ausführen, protokollieren Storage Analytics-Metriken die Anforderung und fügen diese in die aggregierten Daten für den Blob-Dienst und den **GetBlob**-Vorgang ein. Wenn im Verlauf der Stunde kein **GetBlob**-Vorgang angefordert wurde, wird für den betreffenden Vorgang keine Entität in *$MetricsTransactionsBlob* geschrieben.  

 Transaktionsmetriken werden sowohl für Anforderungen von Benutzern als auch für Anforderungen aufgezeichnet, die von Storage Analytics selbst generiert wurden. So werden z. B. Anforderungen von Storage Analytics zum Schreiben von Protokollen und Tabellenentitäten aufgezeichnet.

## <a name="capacity-metrics"></a>Kapazitätsmetriken  

> [!NOTE]
>  Kapazitätsmetriken sind derzeit nur für den Blobdienst verfügbar.

 Kapazitätsdaten werden für den Blobdienst eines Speicherkontos täglich aufgezeichnet, und es werden zwei Tabellenentitäten geschrieben. Eine Entität stellt Statistiken für Benutzerdaten bereit, während die andere Statistiken zum Blob-Container `$logs` bereitstellt, der von der Speicheranalyse verwendet wird. Die Tabelle *$MetricsCapacityBlob* enthält die folgenden Statistiken:  

- **Capacity**: Die Größe des vom Blobdienst des Speicherkontos genutzten Speichers in Byte.  
- **ContainerCount**: Die Anzahl von Blobcontainern im Blobdienst des Speicherkontos.  
- **ObjectCount**: Die Anzahl der Block- oder Seitenblobs mit und ohne ausgeführtem Commit im Blobdienst des Speicherkontos.  

  Weitere Informationen zu den Kapazitätsmetriken finden Sie unter [Schema der Tabellen für Storage Analytics-Metriken](/rest/api/storageservices/storage-analytics-metrics-table-schema).  

## <a name="how-metrics-are-stored"></a>Speichern von Metriken  

 Alle Metrikdaten für jeden der Speicherdienste werden in drei Tabellen gespeichert, die für diesen Dienst reserviert sind. Eine Tabelle ist für Transaktionsinformationen, eine Tabelle für minutenbezogene Transaktionsinformationen und eine weitere Tabelle für Kapazitätsinformationen. Informationen zur Transaktion und deren Dauer umfassen Anforderungs- und Antwortdaten. Kapazitätsinformationen umfassen Daten zur Speicherverwendung. Der Zugriff auf Stunden- und Minutenmetriken sowie Kapazitätsinformationen zum Blobdienst eines Speicherkontos erfolgt über Tabellen, die, wie in der folgenden Tabelle beschrieben, benannt sind.  

|Metrikebene|Tabellennamen|Unterstützt für folgende Versionen|  
|-------------------|-----------------|----------------------------|  
|Stundenmetriken, primärer Standort|-   $MetricsTransactionsBlob<br />-   $MetricsTransactionsTable<br />-   $MetricsTransactionsQueue|Nur Versionen vor dem 15. August 2013. Obwohl diese Namen weiterhin unterstützt werden, empfehlen wir, dass Sie stattdessen die folgenden Tabellen verwenden.|  
|Stundenmetriken, primärer Standort|-   $MetricsHourPrimaryTransactionsBlob<br />-   $MetricsHourPrimaryTransactionsTable<br />-   $MetricsHourPrimaryTransactionsQueue<br />-   $MetricsHourPrimaryTransactionsFile|Alle Versionen. Die Unterstützung für Dateidienstmetriken ist nur in der Version vom 5. April 2015 und später verfügbar.|  
|Minutenmetriken, primärer Standort|-   $MetricsMinutePrimaryTransactionsBlob<br />-   $MetricsMinutePrimaryTransactionsTable<br />-   $MetricsMinutePrimaryTransactionsQueue<br />-   $MetricsMinutePrimaryTransactionsFile|Alle Versionen. Die Unterstützung für Dateidienstmetriken ist nur in der Version vom 5. April 2015 und später verfügbar.|  
|Stundenmetriken, sekundärer Standort|-   $MetricsHourSecondaryTransactionsBlob<br />-   $MetricsHourSecondaryTransactionsTable<br />-   $MetricsHourSecondaryTransactionsQueue|Alle Versionen. Georedundante Replikation mit Lesezugriff muss aktiviert sein.|  
|Minutenmetriken, sekundärer Standort|-   $MetricsMinuteSecondaryTransactionsBlob<br />-   $MetricsMinuteSecondaryTransactionsTable<br />-   $MetricsMinuteSecondaryTransactionsQueue|Alle Versionen. Georedundante Replikation mit Lesezugriff muss aktiviert sein.|  
|Kapazität (nur Blobdienst)|$MetricsCapacityBlob|Alle Versionen.|  

 Diese Tabellen werden automatisch erstellt, wenn Storage Analytics für einen Speicherdienstendpunkt aktiviert wird. Der Zugriff auf diese Tabellen erfolgt über den Namespace des Speicherkontos. Beispiel: `https://<accountname>.table.core.windows.net/Tables("$MetricsTransactionsBlob")`. Die Metriktabellen werden bei einem Auflistungsvorgang nicht angezeigt und müssen über den Tabellennamen direkt aufgerufen werden.

## <a name="metrics-alerts"></a>Metrikwarnungen
Ziehen Sie die Einrichtung von Warnungen im [Azure-Portal](https://portal.azure.com) in Betracht, damit Sie automatisch über wichtige Änderungen im Verhalten der Speicherdienste informiert werden. Eine ausführliche Anleitung finden Sie unter [Erstellen von Metrikwarnungen](storage-monitor-storage-account.md#create-metric-alerts).

Wenn Sie ein Storage-Explorer-Tool zum Herunterladen dieser Metrikdaten in einem Trennzeichen-getrennten Format verwenden, können Sie die Daten mithilfe von Microsoft Excel analysieren. Eine Liste der verfügbaren Tools für Storage-Explorer finden Sie unter [Azure Storage-Clienttools](./storage-explorers.md).

> [!IMPORTANT]
> Möglicherweise gibt es eine Verzögerung zwischen einem Speicherereignis und der Aufzeichnung der zugehörigen stündlichen oder minütlichen Metrikdaten. Bei Minutenmetriken werden möglicherweise mehrere Minuten von Daten gleichzeitig geschrieben. Dieses Problem kann dazu führen, dass Transaktionen früherer Minuten in der Transaktion der aktuellen Minute zusammengeführt werden. In diesem Fall stehen dem Benachrichtigungsdienst möglicherweise nicht alle verfügbaren Metrikdaten für das konfigurierte Warnungsintervall zur Verfügung, sodass Warnungen unerwartet ausgelöst werden können.
>

## <a name="billing-on-storage-metrics"></a>Abrechnung von Speichermetriken
Für Schreibanforderungen zum Erstellen von Tabellenentitäten für Metriken fallen die Standardraten an, die für alle Azure Storage-Vorgänge gelten.  

Für Lese- und Löschanforderungen für Metrikdaten durch einen Client gelten ebenfalls Standardraten. Wenn Sie eine Datenaufbewahrungsrichtlinie konfiguriert haben, fallen keine Kosten an, wenn Azure Storage alte Metrikdaten löscht. Wenn Sie Analysedaten löschen, wird Ihr Konto für die Löschvorgänge belastet.  

Die von Metriktabellen beanspruchte Kapazität ist ebenfalls kostenpflichtig. Verwenden Sie die folgenden Angaben, um die erforderliche Kapazität zum Speichern von Metrikdaten einzuschätzen:  

-   Wenn ein Dienst jede Stunde alle APIs in jedem Dienst nutzt, werden ungefähr 148 KB Daten pro Stunde in den Metriktransaktionstabellen gespeichert, wenn Sie eine Zusammenfassung auf Dienstebene und auf API-Ebene aktiviert haben.  
-   Wenn ein Dienst jede Stunde alle APIs im Dienst nutzt, werden ungefähr 12 KB Daten pro Stunde in den Metriktransaktionstabellen gespeichert, sofern Sie die Zusammenfassung nur auf Dienstebene aktiviert haben.  
-   In der Kapazitätstabelle für Blobs werden pro Tag zwei Zeilen hinzugefügt, vorausgesetzt, Sie haben Protokolle abonniert. Dieses Szenario impliziert, dass die Größe dieser Tabelle pro Tag um ungefähr 300 Byte zunimmt.

## <a name="next-steps"></a>Nächste Schritte
* [Überwachen eines Speicherkontos](https://www.windowsazure.com/manage/services/storage/how-to-monitor-a-storage-account/)   
* [Schema der Tabellen für Storage Analytics-Metriken](/rest/api/storageservices/storage-analytics-metrics-table-schema)   
* [Protokollierte Storage Analytics-Vorgänge und -Statusmeldungen](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)   
* [Storage Analytics-Protokollierung](storage-analytics-logging.md)