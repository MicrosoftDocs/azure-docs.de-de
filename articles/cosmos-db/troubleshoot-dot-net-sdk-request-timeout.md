---
title: Troubleshooting bei HTTP-408- oder Anforderungstimeoutproblemen in Azure Cosmos DB mit dem .NET SDK
description: Hier erfahren Sie, wie Anforderungstimeoutausnahmen im .NET SDK diagnostiziert und behoben werden.
author: j82w
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 08/06/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.custom: devx-track-dotnet
ms.openlocfilehash: c8d448cf335f328b5ae55579fd30127ef0e37e9d
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93340497"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-net-sdk-request-timeout-exceptions"></a>Diagnose und Troubleshooting bei .NET SDK-Anforderungstimeoutausnahmen in Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Der Fehler „HTTP-408“ tritt auf, wenn das SDK die Anforderung nicht abschließen konnte, bevor das Timeoutlimit überschritten wurde.

## <a name="customize-the-timeout-on-the-azure-cosmos-db-net-sdk"></a>Anpassen des Timeouts beim Azure Cosmos DB .NET SDK

Das SDK verfügt über zwei unterschiedliche Möglichkeiten zum Steuern von Timeouts, die jeweils für einen anderen Bereich gelten.

### <a name="requesttimeout"></a>RequestTimeout

Mit der Konfiguration `CosmosClientOptions.RequestTimeout` (bzw. `ConnectionPolicy.RequestTimeout` bei SDK v2) können Sie ein Timeout festlegen, das sich auf jede einzelne Netzwerkanforderung auswirkt. Ein von einem Benutzer gestarteter Vorgang kann mehrere Netzwerkanforderungen umfassen (z. B. könnte eine Drosselung auftreten). Diese Konfiguration gilt für jede Netzwerkanforderung beim Wiederholungsversuch. Dieses Timeout ist kein Anforderungstimeout bei einem End-to-End-Vorgang.

### <a name="cancellationtoken"></a>CancellationToken

Alle asynchronen Vorgänge im SDK enthalten einen optionalen CancellationToken-Parameter. Dieser [CancellationToken](/dotnet/standard/threading/how-to-listen-for-cancellation-requests-by-polling)-Parameter wird während des gesamten Vorgangs in allen Netzwerkanforderungen verwendet. Zwischen den Netzwerkanforderungen wird das Abbruchtoken (cancellation token) möglicherweise überprüft und ein Vorgang abgebrochen, wenn das zugehörige Token abgelaufen ist. Das Abruftoken (CancellationToken) sollte zum Definieren eines ungefähren erwarteten Timeouts für den Vorgangsbereich verwendet werden.

> [!NOTE]
> Der Parameter `CancellationToken` ist ein Mechanismus, bei dem die Bibliothek den Abbruch überprüft, wenn das [zu keinem ungültigen Status führt](https://devblogs.microsoft.com/premier-developer/recommended-patterns-for-cancellationtoken/). Der Vorgang wird möglicherweise nicht exakt abgebrochen, wenn die im Abbruch definierte Zeit abgelaufen ist. Stattdessen wird er nach Ablauf der Zeit dann abgebrochen, wenn dies auf sichere Weise möglich ist.

## <a name="troubleshooting-steps"></a>Schritte zur Problembehandlung
Die folgende Liste enthält bekannte Gründe und Lösungen für Anforderungstimeoutausnahmen.

### <a name="high-cpu-utilization"></a>Hohe CPU-Auslastung
Hohe CPU-Auslastung ist der häufigste Fall. Für optimale Latenz sollte die CPU-Auslastung ungefähr 40 Prozent betragen. Verwenden Sie 10 Sekunden als Intervall zur Überwachung der maximalen (nicht durchschnittlichen) CPU-Nutzung. CPU-Spitzenwerte treten bei partitionsübergreifenden Abfragen häufiger auf, wenn es möglicherweise erforderlich ist, mehrere Verbindungen für eine einzelne Abfrage herzustellen.

#### <a name="solution"></a>Lösung:
Die Clientanwendung, die das SDK verwendet, sollte entsprechend skaliert werden.

### <a name="socket-or-port-availability-might-be-low"></a>Die Verfügbarkeit des Sockets oder Ports ist möglicherweise gering.
Bei Ausführung in Azure kann es auf Clients, die das .NET SDK verwenden, zur Azure SNAT-Portauslastung (PAT) kommen.

#### <a name="solution-1"></a>Lösung 1:
Bei Ausführung auf Azure-VMs folgen Sie dem [Leitfaden für die SNAT-Portauslastung](troubleshoot-dot-net-sdk.md#snat).

#### <a name="solution-2"></a>Lösung 2:
Bei Ausführung im Azure App Service folgen Sie dem [Leitfaden zur Problembehandlung bei Verbindungsfehlern](../app-service/troubleshoot-intermittent-outbound-connection-errors.md#cause), und [verwenden Sie die App Service-Diagnose](https://azure.github.io/AppService/2018/03/01/Deep-Dive-into-TCP-Connections-in-App-Service-Diagnostics.html).

#### <a name="solution-3"></a>Lösung 3:
Bei Ausführung in Azure Functions müssen Sie der [Azure Functions-Empfehlung](../azure-functions/manage-connections.md#static-clients) zur Wartung von Singleton-Clients oder statischen Clients für alle beteiligten Dienste (einschließlich Azure Cosmos DB) folgen. Überprüfen Sie die [Diensteinschränkungen](../azure-functions/functions-scale.md#service-limits) basierend auf Typ und Größe Ihres Funktions-App-Hostings.

#### <a name="solution-4"></a>Lösung 4:
Wenn Sie einen HTTP-Proxy verwenden, vergewissern Sie sich, dass er die Anzahl von Verbindungen unterstützt, die in `ConnectionPolicy` des SDK konfiguriert ist. Andernfalls werden Verbindungsprobleme auftreten.

### <a name="create-multiple-client-instances"></a>Erstellen mehrerer Clientinstanzen
Das Erstellen mehrerer Clientinstanzen kann zu Verbindungskonflikten und Timeoutproblemen führen.

#### <a name="solution"></a>Lösung:
Befolgen Sie die [Leistungstipps](performance-tips-dotnet-sdk-v3-sql.md#sdk-usage), und verwenden Sie eine einzige CosmosClient-Instanz für den gesamten Prozess.

### <a name="hot-partition-key"></a>Partitionsschlüssel auf heißer Ebene
Azure Cosmos DB verteilt den gesamten bereitgestellten Durchsatz gleichmäßig auf die physischen Partitionen. Wenn es eine heiße Partition gibt, werden alle Anforderungseinheiten pro Sekunde (RU/s) einer physischen Partition von einem logischen Partitionsschlüssel (oder mehreren logischen Partitionsschlüsseln) darauf verbraucht. Gleichzeitig werden die RU/s auf anderen physischen Partitionen nicht genutzt. Die insgesamt genutzten RU/s sind weniger als die insgesamt bereitgestellten RU/s in der Datenbank oder im Container. Anforderungen für den logischen Partitionsschlüssel auf heißer Ebene werden jedoch weiterhin gedrosselt (429s). Im Artikel [Überwachen von normalisierten RU/s für einen Azure Cosmos-Container oder ein -Konto](monitor-normalized-request-units.md) finden Sie weitere Informationen, wie Sie erfahren, ob die Workload Probleme mit der Partition auf heißer Ebene hat. 

#### <a name="solution"></a>Lösung:
Wählen Sie einen geeigneten Partitionsschlüssel aus, der Anforderungsvolume und -speicher gleichmäßig verteilt. Weitere Informationen finden Sie unter [Ändern des Partitionsschlüssels in Azure Cosmos DB](https://devblogs.microsoft.com/cosmosdb/how-to-change-your-partition-key/).

### <a name="high-degree-of-concurrency"></a>Hoher Parallelitätsgrad
Die Anwendung weist einen hohen Parallelitätsgrad auf. Das kann zu Konflikten im Kanal führen.

#### <a name="solution"></a>Lösung:
Die Clientanwendung, die das SDK verwendet, sollte entsprechend skaliert werden.

### <a name="large-requests-or-responses"></a>Große Anforderungen oder Antworten
Große Anforderungen oder Antworten können zu Head-of-Line-Blockierungen im Kanal führen und die Konfliktsituation verschärfen, selbst bei einem relativ geringen Grad an Parallelität.

#### <a name="solution"></a>Lösung:
Die Clientanwendung, die das SDK verwendet, sollte entsprechend skaliert werden.

### <a name="failure-rate-is-within-the-azure-cosmos-db-sla"></a>Die Fehlerrate liegt innerhalb der Azure Cosmos DB SLA (Service Level Agreement, Vereinbarung zum Servicelevel).
Die Anwendung sollte vorübergehende Fehler verarbeiten können und bei Bedarf wiederholte Versuche ausführen. Bei 408-Ausnahmen wird kein wiederholter Versuch ausgeführt, weil es bei Erstellpfaden nicht möglich ist zu wissen, ob der Dienst das Element erstellt hat oder nicht. Wenn dasselbe Element noch mal für einen Erstellvorgang gesendet wird, führt dies zu einer Konfliktausnahme. In der Geschäftslogik von Benutzeranwendungen ist möglicherweise eine benutzerdefinierte Logik zur Verarbeitung von Konflikten enthalten. Dabei würde aufgrund der Mehrdeutigkeit eines vorhandenen Elements und dem Konflikt aus einem wiederholten Erstellversuch ein Fehler auftreten.

### <a name="failure-rate-violates-the-azure-cosmos-db-sla"></a>Fehlerrate verstößt gegen die Azure Cosmos DB SLA
Wenden Sie sich an den [Azure-Support](https://aka.ms/azure-support).

## <a name="next-steps"></a>Nächste Schritte
* Informieren Sie sich zu [Diagnostizieren und Behandeln](troubleshoot-dot-net-sdk.md) von Problemen bei Verwendung des .NET SDK für Azure Cosmos DB.
* Informieren Sie sich zu Leistungsrichtlinien für [.NET v3](performance-tips-dotnet-sdk-v3-sql.md) und [.NET v2](performance-tips.md).