---
title: Behandeln von HTTP-408- oder Anforderungstimeoutproblemen in Azure Cosmos DB mit dem Java v4 SDK
description: Hier erfahren Sie, wie Anforderungstimeoutausnahmen im Java SDK mit dem Java v4 SDK diagnostiziert und behoben werden.
author: kushagrathapar
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 10/28/2020
ms.author: kuthapar
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: a805300ac62d0627c9b06188c9764a6887947afe
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94411285"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-java-v4-sdk-request-timeout-exceptions"></a>Diagnose und Problembehandlung bei Java v4 SDK-Anforderungstimeoutausnahmen in Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Der Fehler „HTTP-408“ tritt auf, wenn das SDK die Anforderung nicht abschließen konnte, bevor das Timeoutlimit überschritten wurde.

## <a name="troubleshooting-steps"></a>Schritte zur Problembehandlung
Die folgende Liste enthält bekannte Gründe und Lösungen für Anforderungstimeoutausnahmen.

### <a name="existing-issues"></a>Vorhandene Probleme
Wenn Sie feststellen, dass Anforderungen über längere Zeit nicht reagieren oder häufiger ein Timeout auftritt, führen Sie ein Upgrade des Java v4 SDK auf die neueste Version durch. HINWEIS: Es wird dringend empfohlen, Version 4.7.0 oder höher zu verwenden. Weitere Informationen finden Sie in den [Versionshinweisen zum Java v4 SDK](sql-api-sdk-java-v4.md).

### <a name="high-cpu-utilization"></a>Hohe CPU-Auslastung
Hohe CPU-Auslastung ist der häufigste Fall. Für optimale Latenz sollte die CPU-Auslastung ungefähr 40 Prozent betragen. Verwenden Sie 10 Sekunden als Intervall zur Überwachung der maximalen (nicht durchschnittlichen) CPU-Nutzung. CPU-Spitzenwerte treten bei partitionsübergreifenden Abfragen häufiger auf, wenn es möglicherweise erforderlich ist, mehrere Verbindungen für eine einzelne Abfrage herzustellen.

#### <a name="solution"></a>Lösung:
Die Clientanwendung, die das SDK verwendet, sollte entsprechend skaliert werden.

### <a name="connection-throttling"></a>Verbindungsdrosselung
Eine Verbindungsdrosselung kann entweder aufgrund eines Verbindungslimit auf einem Hostcomputer oder aufgrund von Azure SNAT-Portauslastung (PAT) auftreten.

### <a name="connection-limit-on-a-host-machine"></a>Verbindungslimit auf einem Hostcomputer
Bei einigen Linux-Systemen (beispielsweise Red Hat) gilt eine Obergrenze für die Gesamtzahl geöffneter Dateien. Da Sockets in Linux als Dateien implementiert werden, schränkt dies auch die Gesamtanzahl von Verbindungen ein. Führen Sie den folgenden Befehl aus.

```bash
ulimit -a
```

#### <a name="solution"></a>Lösung:
Die maximal zulässige Anzahl geöffneter Dateien, die als „nofile“ angegeben sind, muss mindestens 10.000 betragen. Weitere Informationen finden Sie im Artikel zu den [Leistungstipps](performance-tips-java-sdk-v4-sql.md) für das Azure Cosmos DB Java SDK v4.

### <a name="socket-or-port-availability-might-be-low"></a>Die Verfügbarkeit des Sockets oder Ports ist möglicherweise gering.
Bei Ausführung in Azure kann es auf Clients, die das Java SDK verwenden, zur Azure SNAT-Portauslastung (PAT) kommen.

#### <a name="solution-1"></a>Lösung 1:
Bei Ausführung auf Azure-VMs folgen Sie dem [Leitfaden für die SNAT-Portauslastung](troubleshoot-java-sdk-v4-sql.md#snat).

#### <a name="solution-2"></a>Lösung 2:
Bei Ausführung im Azure App Service folgen Sie dem [Leitfaden zur Problembehandlung bei Verbindungsfehlern](../app-service/troubleshoot-intermittent-outbound-connection-errors.md#cause), und [verwenden Sie die App Service-Diagnose](https://azure.github.io/AppService/2018/03/01/Deep-Dive-into-TCP-Connections-in-App-Service-Diagnostics.html).

#### <a name="solution-3"></a>Lösung 3:
Bei Ausführung in Azure Functions müssen Sie der [Azure Functions-Empfehlung](../azure-functions/manage-connections.md#static-clients) zur Wartung von Singleton-Clients oder statischen Clients für alle beteiligten Dienste (einschließlich Azure Cosmos DB) folgen. Überprüfen Sie die [Diensteinschränkungen](../azure-functions/functions-scale.md#service-limits) basierend auf Typ und Größe Ihres Funktions-App-Hostings.

#### <a name="solution-4"></a>Lösung 4:
Wenn Sie einen HTTP-Proxy verwenden, vergewissern Sie sich, dass er die Anzahl von Verbindungen unterstützt, die in `GatewayConnectionConfig` des SDK konfiguriert ist. Andernfalls werden Verbindungsprobleme auftreten.

### <a name="create-multiple-client-instances"></a>Erstellen mehrerer Clientinstanzen
Das Erstellen mehrerer Clientinstanzen kann zu Verbindungskonflikten und Timeoutproblemen führen.

#### <a name="solution-1"></a>Lösung 1:
Befolgen Sie die [Leistungstipps](performance-tips-java-sdk-v4-sql.md#sdk-usage), und verwenden Sie eine einzige CosmosClient-Instanz für die gesamte Anwendung.

#### <a name="solution-2"></a>Lösung 2:
Wenn die Verwendung einer einzigen CosmosClient-Instanz in einer Anwendung nicht möglich ist, wird empfohlen, eine gemeinsam genutzte Verbindung für mehrere CosmosClient-Instanzen über diese API `connectionSharingAcrossClientsEnabled(true)` in CosmosClient zu verwenden. Wenn Sie über mehrere CosmosClient-Instanzen in derselben JVM verfügen, die mit mehreren Cosmos-Konten interagieren, ermöglicht dies eine gemeinsam genutzte Verbindung im Direktmodus, sofern dies zwischen Instanzen von CosmosClient möglich ist. Beachten Sie, dass bei Festlegen dieser Option die Verbindungskonfiguration (z. B. Sockettimeout-Konfiguration, Leerlauftimeout-Konfiguration) des ersten instanziierten Clients für alle anderen Clientinstanzen verwendet wird.

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
* [Diagnostizieren und Behandeln](troubleshoot-java-sdk-v4-sql.md) von Problemen bei Verwendung des Java v4 SDK für Azure Cosmos DB.
* Weitere Informationen zu Leistungsrichtlinien für das [Java v4 SDK](performance-tips-java-sdk-v4-sql.md).
