---
title: Diagnostizieren und Behandeln von Problemen bei Verwendung des .NET SDK für Azure Cosmos DB
description: Verwenden Sie Features wie clientseitige Protokollierung und andere Tools von Drittanbietern, um bei Verwenden des .NET SDK Probleme im Zusammenhang mit Azure Cosmos DB zu erkennen, zu diagnostizieren und zu beheben.
author: j82w
ms.service: cosmos-db
ms.date: 05/28/2019
ms.author: jawilley
ms.devlang: c#
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 7e48809537acc21edbcf12d299a333df486c258f
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/28/2019
ms.locfileid: "66257153"
---
# <a name="diagnose-and-troubleshoot-issues-when-using-azure-cosmos-db-net-sdk"></a>Diagnostizieren und Behandeln von Problemen bei Verwendung des .NET SDK für Azure Cosmos DB
Dieser Artikel behandelt allgemeine Probleme, Problemumgehungen, Diagnoseschritte und Tools bei der Verwendung des [.NET SDK](sql-api-sdk-dotnet.md) mit Azure Cosmos DB-SQL-API-Konten.
Das .NET SDK bietet eine clientseitige logische Darstellung für den Zugriff auf die Azure Cosmos DB-SQL-API. Dieser Artikel beschreibt hilfreiche Tools und Vorgehensweisen für Problemfälle.

## <a name="checklist-for-troubleshooting-issues"></a>Checkliste für die Problembehandlung:
Gehen Sie die folgende Prüfliste durch, bevor Sie Ihre Anwendung in die Produktion überführen. Mithilfe der Prüfliste werden einige häufige Probleme vermieden, die möglicherweise auftreten. Sie können ein Problem auch schnell diagnostizieren, sobald es auftritt:

*   Verwenden Sie das neueste [SDK](https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/changelog.md). SDKs in der Vorschauversion sollten nicht für die Produktion verwendet werden. Dadurch wird verhindert, dass bekannte Probleme, die bereits behoben wurden, erneut auftreten.
*   Überprüfen Sie die [Leistungstipps](performance-tips.md), und implementieren Sie die Empfehlungen. Dadurch können Skalierungs-, Latenz- und andere Leistungsprobleme vermieden werden.
*   Aktivieren Sie die SDK-Protokollierung, um die Problembehandlung zu unterstützen. Die Aktivierung der Protokollierung kann die Leistung beeinträchtigen, weshalb es am besten ist, sie nur für die Problembehandlung zu aktivieren. Sie können die folgenden Protokolle aktivieren:
    *   [Protokollmetriken](monitor-accounts.md) im Azure-Portal. Metriken im Portal zeigen die Azure Cosmos DB-Telemetriedaten, die hilfreich sind, um festzustellen, ob das Problem im Zusammenhang mit Azure Cosmos DB steht oder ob es von der Clientseite kommt.
    *   Protokollieren Sie in den Antworten des Verwaltungspunktsvorgangs die [Diagnosezeichenfolge](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.resourceresponsebase.requestdiagnosticsstring?view=azure-dotnet).
    *   Protokollieren Sie in allen Abfrageantworten die [SQL-Abfragemetriken](sql-api-query-metrics.md). 
    *   Führen Sie das Setup für die [SDK-Protokollierung]( https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/docs/documentdb-sdk_capture_etl.md) aus.

Sehen Sie sich den Abschnitt [Häufig auftretende Probleme und Problemumgehungen](#common-issues-workarounds) in diesem Artikel an.

Überprüfen Sie den [Problemabschnitt auf GitHub](https://github.com/Azure/azure-cosmos-dotnet-v2/issues), der aktiv überwacht wird. Überprüfen Sie, ob Sie ein ähnliches Problem finden, für das bereits eine Problemumgehung dokumentiert wurde. Wenn Sie keine Lösung gefunden haben, legen Sie ein GitHub-Problem an. Bei dringenden Problemen können Sie ein Supportticket öffnen.


## <a name="common-issues-workarounds"></a>Häufig auftretende Probleme und Problemumgehungen

### <a name="general-suggestions"></a>Allgemeine Empfehlungen
* Führen Sie Ihre App nach Möglichkeit in der gleichen Azure-Region wie Ihr Azure Cosmos DB-Konto aus. 
* Aufgrund fehlender Ressourcen auf Ihrem Clientcomputer kann es zu Verbindungs-/Verfügbarkeitsproblemen kommen. Wir empfehlen, die CPU-Auslastung auf Knoten zu überwachen, auf denen der Azure Cosmos DB-Client ausgeführt wird, und hoch bzw. zentral zu skalieren, wenn ihre Last hoch ist.

### <a name="check-the-portal-metrics"></a>Überprüfen der Metriken im Portal
Das Überprüfen der [Metriken im Portal](monitor-accounts.md) hilft festzustellen, ob es sich um ein Problem auf Clientseite handelt oder ob es ein Problem mit dem Dienst gibt. Wenn die Metriken beispielsweise viele Anforderungen mit Ratenbegrenzungen aufweisen (HTTP-Statuscode 429), bedeutet dies, dass die Anforderung gedrosselt wird. Siehe dann den Abschnitt [Anforderungsrate zu hoch]. 

### <a name="request-timeouts"></a>Timeouts von Anforderungen
Ein Timeout einer Anforderung tritt in der Regel bei Verwenden von Direkt-/TCP-Verbindungen auf, kann aber auch im Gatewaymodus auftreten. Es folgen die gängigen bekannten Ursachen und Vorschläge zur Behebung des Problems.

* Die CPU-Auslastung ist hoch, was zu Wartezeiten und/oder Timeouts bei Anforderungen führt. Der Kunde kann den Hostcomputer zentral skalieren, um ihn mit mehr Ressourcen auszustatten, oder die Last auf mehrere Computer verteilen.
* Die Verfügbarkeit von Sockets und Ports kann niedrig sein. Bei Verwendung der vor der Version 2.0 veröffentlichten .NET SDKs konnte es auf in Azure ausgeführten Clients zur [Azure SNAT-Portauslastung (PAT)] kommen. Dies ist ein Beispiel dafür, warum es empfehlenswert ist, immer die neueste SDK-Version zu verwenden.
* Das Erstellen mehrerer DocumentClient-Instanzen kann zu Verbindungskonflikten und Timeoutproblemen führen. Befolgen Sie die [Leistungstipps](performance-tips.md), und verwenden Sie eine einzige „DocumentClient“-Instanz für den gesamten Prozess.
* Benutzer beobachten mitunter erhöhte Latenzen oder Timeouts bei Anforderungen, weil ihre Sammlungen unzureichend bereitgestellt sind, das Back-End Anforderungen drosselt und der Client intern Vorgänge wiederholt, ohne dies dem Aufrufer zu melden. Überprüfen Sie die [Metriken im Portal](monitor-accounts.md).
* Azure Cosmos DB verteilt den gesamten bereitgestellten Durchsatz gleichmäßig auf die physischen Partitionen. Überprüfen Sie die Metriken im Portal, um festzustellen, ob die Workload einen sehr aktiven [Partitionsschlüssel](partition-data.md) vorfindet. Dies führt dazu, dass der gesamte in Anspruch genommene Durchsatz (Anforderungseinheiten pro Sekunde, RU/s) anscheinend unter den bereitgestellten RU/s liegt, während der von einer einzelnen Partition genutzte Durchsatz (RU/s) den bereitgestellten Durchsatz übersteigt. 
* Zusätzlich fügt das SDK 2.0 Direkt-/TCP-Verbindungen eine Kanalsemantik hinzu. Eine TCP-Verbindung wird für mehrere Anforderungen gleichzeitig verwendet. Dies kann unter bestimmten Umständen zu zwei Problemen führen:
    * Ein hoher Grad an Parallelität kann zu Konflikten im Kanal führen.
    * Große Anforderungen oder Antworten können zu Head-of-Line-Blockierungen im Kanal führen und die Konfliktsituation verschärfen, selbst bei einem relativ geringen Grad an Parallelität.
    * Wenn der Fall zu einer dieser beiden Kategorien gehört (oder wenn hohe CPU-Auslastung vermutet wird), sind folgende Abhilfemaßnahmen möglich:
        * Versuchen Sie, die Anwendung zentral/hoch zu skalieren.
        * Darüber hinaus können über den [Ablaufverfolgungslistener](https://github.com/Azure/azure-cosmosdb-dotnet/blob/master/docs/documentdb-sdk_capture_etl.md) SDK-Protokolle erfasst werden, um weitere Details zu erhalten.

### <a name="connection-throttling"></a>Verbindungsdrosselung
Eine Verbindungsdrosselung kann aufgrund eines Verbindungslimits auf dem Hostcomputer auftreten. Vor Version 2.0 konnte es auf in Azure ausgeführten Clients zur [Azure SNAT-Portauslastung (PAT)] kommen.

### <a name="snat"></a>Azure SNAT-Portauslastung (PAT)

Wenn Ihre App auf einem virtuellen Azure-Computer ohne öffentliche IP-Adresse bereitgestellt wird, werden standardmäßig [Azure SNAT-Ports](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#preallocatedports) verwendet, um Verbindungen mit beliebigen Endpunkten außerhalb Ihres virtuellen Computers herzustellen. Die Anzahl zulässiger Verbindungen des virtuellen Computers mit dem Azure Cosmos DB-Endpunkt wird durch die [Azure SNAT-Konfiguration](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#preallocatedports) eingeschränkt.

 Azure SNAT-Ports werden nur verwendet, wenn Ihr virtueller Computer eine private IP-Adresse besitzt und ein Prozess auf dem virtuellen Computer versucht, eine Verbindung mit einer öffentlichen IP-Adresse herzustellen. Es gibt zwei Problemumgehungen, um die Einschränkung durch Azure SNAT zu vermeiden:

* Fügen Sie Ihren Azure Cosmos DB-Dienstendpunkt dem Subnetz Ihres virtuellen Netzwerks von Azure Virtual Machines hinzu. Weitere Informationen finden Sie unter [Azure Virtual Network-Dienstendpunkte](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview). 

    Wenn der Dienstendpunkt aktiviert ist, werden die Anforderungen nicht mehr von einer öffentlichen IP-Adresse an Azure Cosmos DB gesendet. Stattdessen wird die Identität des virtuellen Netzwerks und des Subnetzes gesendet. Diese Änderung kann zu Firewallproblemen führen, wenn nur öffentliche IP-Adressen zulässig sind. Wenn Sie eine Firewall verwenden und den Dienstendpunkt aktivieren, fügen Sie der Firewall mithilfe von [VNET-ACLs](https://docs.microsoft.com/azure/virtual-network/virtual-networks-acl) ein Subnetz hinzu.
* Weisen Sie Ihrem virtuellen Azure-Computer eine öffentliche IP-Adresse zu.

### <a name="http-proxy"></a>HTTP-Proxy
Wenn Sie einen HTTP-Proxy verwenden, vergewissern Sie sich, dass er die Anzahl von Verbindungen unterstützt, die in `ConnectionPolicy` des SDK konfiguriert ist.
Andernfalls treten Verbindungsprobleme auf.

### Anforderungsrate zu groß<a name="request-rate-too-large"></a>
„Anforderungsrate zu groß“ oder Fehlercode 429 bedeutet, dass Ihre Anforderungen gedrosselt werden, weil der in Anspruch genommene Durchsatz (RU/s) den bereitgestellten Durchsatz überschritten hat. Das SDK wiederholt Anforderungen basierend auf der angegebenen [Wiederholungsrichtlinie](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.retryoptions?view=azure-dotnet) automatisch. Wenn dieser Fehler häufig auftritt, erwägen Sie das Erhöhen des Durchsatzes für die Sammlung. Überprüfen Sie die [Metriken im Portal](use-metrics.md) auf HTTP-Fehler des Typs 429. Überprüfen Sie Ihren [Partitionsschlüssel](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview#choose-partitionkey), um sicherzustellen, dass dieser zu einer gleichmäßigen Verteilung von Speicher- und Anforderungsvolumen führt. 

### <a name="slow-query-performance"></a>Schwache Abfrageleistung
Anhand der [Metriken zu Abfragen](sql-api-query-metrics.md) können Sie bestimmen, wo die Abfrage die meiste Zeit verbringt. Sie können erkennen, wie viel Zeit im Back-End und auf dem Client verbracht wird.
* Wenn die Back-End-Abfrage schnell zurückgegeben wird und viel Zeit auf den Client verbringt, überprüfen Sie die Last auf dem Computer. Es ist wahrscheinlich, dass nicht genügend Ressourcen vorhanden sind und das SDK darauf wartet, dass Ressourcen für die Bearbeitung der Antwort verfügbar werden.
* Wenn die Back-End-Abfrage langsam ist, versuchen Sie, [die Abfrage zu optimieren](optimize-cost-queries.md), und sehen Sie sich die aktuelle [Indexierungsrichtlinie](index-overview.md) an. 

 <!--Anchors-->
[Common issues and workarounds]: #common-issues-workarounds
[Enable client SDK logging]: #logging
[Anforderungsrate zu hoch]: #request-rate-too-large
[Request Timeouts]: #request-timeouts
[Azure SNAT-Portauslastung (PAT)]: #snat
[Production check list]: #production-check-list


