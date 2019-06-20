---
title: Einführung in die Tabellen-API von Azure Cosmos DB
description: Hier erfahren Sie, wie Sie Azure Cosmos DB verwenden können, um riesige Mengen von Schlüssel-Wert-Daten mit geringer Wartezeit mithilfe der Azure-Tabellen-APIs zu speichern und abzufragen.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: overview
ms.date: 05/20/2019
ms.author: sngun
ms.openlocfilehash: bd86b230d801f5fff8a9fb0de85f9f3025527382
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65953457"
---
# <a name="introduction-to-azure-cosmos-db-table-api"></a>Einführung in Azure Cosmos DB: Tabelle-API

Mit [Azure Cosmos DB](introduction.md) wird die Tabellen-API für Anwendungen bereitgestellt, die für Azure Table Storage geschrieben wurden und beispielsweise folgende Premium-Funktionen benötigen:

* [Globale, sofort einsatzbereite Verteilung](distribute-data-globally.md).
* [Dedizierter Durchsatz](partition-data.md) weltweit.
* Einstellige Latenzzeiten im Millisekundenbereich im 99. Perzentil.
* Garantierte Hochverfügbarkeit.
* [Automatische sekundäre Indizierung](https://www.vldb.org/pvldb/vol8/p1668-shukla.pdf).

Anwendungen, die für Azure Table Storage geschrieben sind, können mit der Table-API ohne Codeänderungen zu Azure Cosmos DB migriert werden und Premium-Funktionen nutzen. Die Tabellen-API verfügt über Client-SDKs, die für .NET, Java, Python und Node.js verfügbar sind.

> [!IMPORTANT]
> Das .NET Framework SDK [Microsoft.Azure.CosmosDB.Table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table) befindet sich im Wartungsmodus und wird demnächst eingestellt. Aktualisieren Sie auf die neue .NET Standard-Bibliothek [Microsoft.Azure.Cosmos.Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table), um weiterhin die neuesten Features zu erhalten, die von der Tabellen-API unterstützt werden.

## <a name="table-offerings"></a>Tabellenangebote
Wenn Sie derzeit Azure Table Storage verwenden, bietet Ihnen der Wechsel zur Azure Cosmos DB-Tabellen-API folgende Vorteile:

| | Azure-Tabellenspeicher | Azure Cosmos DB-Tabellen-API |
| --- | --- | --- |
| Latency | Schnell, aber keine Obergrenzen für die Wartezeit. | Wartezeit im einstelligen Millisekundenbereich für Lese- und Schreibvorgänge, unterstützt durch weniger als 10 ms Wartezeit bei Lese- und weniger als 15 ms Wartezeit bei Schreibvorgängen im 99. Perzentil, bei beliebiger Skalierung weltweit. |
| Throughput | Variables Durchsatzmodell. Tabellen verfügen über eine maximale Skalierbarkeit von 20.000 Vorgängen/s. | Hochgradig skalierbar mit einem [dedizierten reservierten Durchsatz pro Tabelle](request-units.md), der durch SLAs abgedeckt ist. Konten haben keine Obergrenze für den Durchsatz und unterstützen pro Tabelle mehr als 10 Millionen Vorgänge/s. |
| Globale Verteilung | Einzelne Region mit einer optionalen sekundären Leseregion für Hochverfügbarkeit. Es kann kein Failover initiiert werden. | [Globale, sofort einsatzbereite Verteilung](distribute-data-globally.md) für 1 bis mehr als 30 Regionen. Unterstützung von [automatischen und manuellen Failovern](high-availability.md) zu jeder Zeit und an jedem Ort der Welt. |
| Indizierung | Nur primärer Index für PartitionKey und RowKey. Keine sekundären Indizes. | Automatische und vollständige Indizierung für alle Eigenschaften, keine Indexverwaltung. |
| Abfragen | Abfrageausführung verwendet Index für Primärschlüssel, andernfalls die Suche. | Abfragen können die automatische Indizierung für Eigenschaften für schnelle Abfragezeiten nutzen. |
| Konsistenz | „Stark“ in der primären Region. „Möglich“ in der sekundären Region. | [Fünf klar definierte Konsistenzebenen](consistency-levels.md) zur Abstimmung von Verfügbarkeit, Latenz, Durchsatz und Konsistenz basierend auf Ihren Anwendungsanforderungen. |
| Preise | Speicheroptimiert | Durchsatzoptimiert |
| SLAs | Verfügbarkeit von 99,99%. | Verfügbarkeit von 99,99% (SLA) für alle Konten für eine einzelne Region und alle Konten für mehrere Regionen mit gelockerter Konsistenz sowie Leseverfügbarkeit von 99,999% für alle Datenbankkonten für mehrere Regionen ([branchenführende umfassende SLAs](https://azure.microsoft.com/support/legal/sla/cosmos-db/)) mit allgemeiner Verfügbarkeit. |

## <a name="get-started"></a>Erste Schritte

Erstellen Sie im [Azure-Portal](https://portal.azure.com) ein Azure Cosmos DB-Konto. Fahren Sie anschließend mit dem [Schnellstart für die Table-API per .NET](create-table-dotnet.md) fort. 

> [!IMPORTANT]
> Wenn Sie während der Vorschauphase ein Tabellen-API-Konto erstellt haben, erstellen Sie ein [neues Tabellen-API-Konto](create-table-dotnet.md#create-a-database-account) für die Verwendung mit den allgemein verfügbaren Table API SDKs.
>

## <a name="next-steps"></a>Nächste Schritte

Hier einige Hinweise, um Ihnen den Einstieg zu erleichtern:
* [Erstellen einer .NET-Anwendung mit der Table-API](create-table-dotnet.md)
* [Entwickeln mit der Table-API in .NET](tutorial-develop-table-dotnet.md)
* [Abfragen von Tabellendaten mit der Table-API](tutorial-query-table.md)
* [Einrichten der globalen Verteilung von Azure Cosmos DB mithilfe der Table-API](tutorial-global-distribution-table.md)
* [Azure Cosmos DB-Table-.NET-API](table-sdk-dotnet.md)
* [Azure Cosmos DB-Tabellen-Java-API](table-sdk-java.md)
* [Azure Cosmos DB-Tabellen-Node.js-API](table-sdk-nodejs.md)
* [Azure Cosmos DB-Table-SDK für Python](table-sdk-python.md)

