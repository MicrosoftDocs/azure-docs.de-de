---
title: Einführung in die Tabellen-API von Azure Cosmos DB
description: Hier erfahren Sie, wie Sie Azure Cosmos DB verwenden können, um riesige Mengen von Schlüssel-Wert-Daten mit geringer Wartezeit mithilfe der Azure-Tabellen-APIs zu speichern und abzufragen.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: overview
ms.date: 11/25/2020
ms.author: sngun
ms.openlocfilehash: 98b1db18b72aad0c68f2faee031cc040a2a1f369
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96181236"
---
# <a name="introduction-to-azure-cosmos-db-table-api"></a>Einführung in Azure Cosmos DB: Tabelle-API
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

Mit [Azure Cosmos DB](introduction.md) wird die Tabellen-API für Anwendungen bereitgestellt, die für Azure Table Storage geschrieben wurden und beispielsweise folgende Premium-Funktionen benötigen:

* [Globale, sofort einsatzbereite Verteilung](distribute-data-globally.md).
* [Dedizierter Durchsatz](partitioning-overview.md) weltweit.
* Einstellige Latenzzeiten im Millisekundenbereich im 99. Perzentil.
* Garantierte Hochverfügbarkeit.
* Automatische sekundäre Indizierung

Anwendungen, die für Azure Table Storage geschrieben sind, können mit der Table-API ohne Codeänderungen zu Azure Cosmos DB migriert werden und Premium-Funktionen nutzen. Die Tabellen-API verfügt über Client-SDKs, die für .NET, Java, Python und Node.js verfügbar sind.

> [!NOTE]
> Der [serverlose Kapazitätsmodus](serverless.md) ist nun in der Tabellen-API von Azure Cosmos DB verfügbar.

> [!IMPORTANT]
> Das .NET Framework SDK [Microsoft.Azure.CosmosDB.Table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table) befindet sich im Wartungsmodus und wird demnächst eingestellt. Aktualisieren Sie auf die neue .NET Standard-Bibliothek [Microsoft.Azure.Cosmos.Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table), um weiterhin die neuesten Features zu erhalten, die von der Tabellen-API unterstützt werden.

## <a name="table-offerings"></a>Tabellenangebote
Wenn Sie derzeit Azure Table Storage verwenden, bietet Ihnen der Wechsel zur Azure Cosmos DB-Tabellen-API folgende Vorteile:

| Funktion | Azure-Tabellenspeicher | Azure Cosmos DB-Tabellen-API |
| --- | --- | --- |
| Latency | Schnell, aber keine Obergrenzen für die Wartezeit. | Wartezeit im einstelligen Millisekundenbereich für Lese- und Schreibvorgänge, unterstützt durch weniger als 10 ms Wartezeit bei Lese- und Schreibvorgängen im 99. Perzentil, bei beliebiger Skalierung weltweit |
| Throughput | Variables Durchsatzmodell. Tabellen verfügen über eine maximale Skalierbarkeit von 20.000 Vorgängen/s. | Hochgradig skalierbar mit einem [dedizierten reservierten Durchsatz pro Tabelle](request-units.md), der durch SLAs abgedeckt ist. Konten haben keine Obergrenze für den Durchsatz und unterstützen pro Tabelle mehr als 10 Millionen Vorgänge/s. |
| Globale Verteilung | Einzelne Region mit einer optionalen sekundären Leseregion für Hochverfügbarkeit. | [Globale, sofort einsatzbereite Verteilung](distribute-data-globally.md) für eine beliebige Anzahl von Regionen. Unterstützung von [automatischen und manuellen Failovern](high-availability.md) zu jeder Zeit und an jedem Ort der Welt. Mehrere Schreibregionen, damit jede Region Schreibvorgänge akzeptieren kann. |
| Indizierung | Nur primärer Index für PartitionKey und RowKey. Keine sekundären Indizes. | Standardmäßig automatische und vollständige Indizierung für alle Eigenschaften, keine Indexverwaltung. |
| Abfrage | Abfrageausführung verwendet Index für Primärschlüssel, andernfalls die Suche. | Abfragen können die automatische Indizierung für Eigenschaften für schnelle Abfragezeiten nutzen. |
| Konsistenz | „Stark“ in der primären Region. „Möglich“ in der sekundären Region. | [Fünf klar definierte Konsistenzebenen](consistency-levels.md) zur Abstimmung von Verfügbarkeit, Latenz, Durchsatz und Konsistenz basierend auf Ihren Anwendungsanforderungen. |
| Preise | Speicheroptimiert | Durchsatzoptimiert |
| SLAs | Verfügbarkeit von 99,9 bis 99,99 %, abhängig von der Replikationsstrategie | 99,999 % Leseverfügbarkeit, 99,99 % Schreibverfügbarkeit für ein Konto in einer Region und 99,999 % Schreibverfügbarkeit für Konten in mehreren Regionen. Die Bereiche Verfügbarkeit, Latenz, Durchsatz und Konsistenz sind durch [umfassende SLAs](https://azure.microsoft.com/support/legal/sla/cosmos-db/) abgedeckt. |

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
* [Azure Cosmos DB Table .NET Standard SDK](table-sdk-dotnet-standard.md)
* [Azure Cosmos DB Table .NET SDK](table-sdk-dotnet.md)
* [Azure Cosmos DB Table Java SDK](table-sdk-java.md)
* [Azure Cosmos DB Table Node.js SDK](table-sdk-nodejs.md)
* [Azure Cosmos DB-Table-SDK für Python](table-sdk-python.md)
