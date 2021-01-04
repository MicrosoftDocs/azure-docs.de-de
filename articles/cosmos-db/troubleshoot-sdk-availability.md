---
title: Diagnostizieren und Beheben von Problemen bei der Verfügbarkeit von Azure Cosmos DB-SDKs in Umgebungen mit mehreren Regionen
description: Informationen über das Verfügbarkeitsverhalten von Azure Cosmos DB-SDKs in Umgebungen mit mehreren Regionen
author: ealsur
ms.service: cosmos-db
ms.date: 10/20/2020
ms.author: maquaran
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: b1c2377ba26b4ca64f5028fb1a51ca4e64f6a67c
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93097888"
---
# <a name="diagnose-and-troubleshoot-the-availability-of-azure-cosmos-sdks-in-multiregional-environments"></a>Diagnostizieren und Beheben von Problemen bei der Verfügbarkeit von Azure Cosmos DB-SDKs in Umgebungen mit mehreren Regionen
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Dieser Artikel beschreibt das Verhalten der neuesten Version von Azure Cosmos DB-SDKs, wenn ein Problem bei der Verbindung mit einer bestimmten Region oder ein Regionsfailover auftritt.

Alle Azure Cosmos DB-SDKs verfügen über eine Option zum Anpassen der bevorzugten Region. In den verschiedenen SDKs werden die folgenden Eigenschaften verwendet:

* Die [ConnectionPolicy.PreferredLocations](/dotnet/api/microsoft.azure.documents.client.connectionpolicy.preferredlocations)-Eigenschaft im .NET V2 SDK
* Die [CosmosClientOptions.ApplicationRegion](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.applicationregion)- oder [CosmosClientOptions.ApplicationPreferredRegions](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.applicationpreferredregions)-Eigenschaft im .NET V3 SDK.
* Die [CosmosClientBuilder.preferredRegions](/java/api/com.azure.cosmos.cosmosclientbuilder.preferredregions)-Methode im Java V4 SDK.
* Der [CosmosClient.preferred_locations](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient)-Parameter im Python SDK.
* Der Parameter [CosmosClientOptions.ConnectionPolicy.preferredLocations](/javascript/api/@azure/cosmos/connectionpolicy#preferredlocations) im JS-SDK

Wenn Sie eine bevorzugte Region festlegen, stellt der Client Verbindungen mit Regionen wie in der folgenden Tabelle gezeigt her:

|Kontotyp |Lesevorgänge |Schreibvorgänge |
|------------------------|--|--|
| Eine Schreibregion | Bevorzugte Region | Primäre Region  |
| Mehrere Schreibregionen | Bevorzugte Region | Bevorzugte Region  |

Wenn Sie **keine bevorzugte Region festlegen** , wird der SDK-Client standardmäßig auf die primäre Region festgelegt:

|Kontotyp |Lesevorgänge |Schreibvorgänge |
|------------------------|--|--|
| Eine Schreibregion | Primäre Region | Primäre Region |
| Mehrere Schreibregionen | Primäre Region  | Primäre Region  |

> [!NOTE]
> „Primäre Region“ bezeichnet die erste Region in der [Regionenliste für Azure Cosmos-Konten](distribute-data-globally.md).

Unter normalen Umständen stellt der SDK-Client eine Verbindung mit der bevorzugten Region (sofern eine diese festgelegt ist) oder der primären Region (wenn keine bevorzugte Region festgelegt ist) her, und die Vorgänge werden außer in den folgenden Szenarien auf diese Region beschränkt.

In diesen Fällen macht der Client, der das Azure Cosmos-SDK verwendet, Protokolle verfügbar und schließt die Wiederholungsinformationen in die **Vorgangsdiagnoseinformationen** ein:

* Die Eigenschaft *RequestDiagnosticsString* für Antworten im .NET-V2-SDK
* Die Eigenschaft *Diagnose* für Antworten und Ausnahmen im .NET-V3-SDK
* Die Methode *getDiagnostics()* für Antworten und Ausnahmen im Java-V4-SDK

Beim Ermitteln der nächsten Region bei Sortierung nach Bevorzugung verwendet der SDK-Client die Kontoregionenliste und priorisiert die bevorzugten Regionen (sofern vorhanden).

Detaillierte und umfassende Informationen zu SLA-Garantien während dieser Ereignisse finden Sie unter [SLAs für Verfügbarkeit](high-availability.md#slas-for-availability).

## <a name="removing-a-region-from-the-account"></a><a id="remove-region"></a>Entfernen einer Region aus dem Konto

Wenn Sie eine Region aus einem Azure Cosmos DB-Konto entfernen, erkennt jeder SDK-Client, der das Konto aktiv verwendet, das Entfernen der Region über einen Back-End-Antwortcode. Der Client markiert dann den regionalen Endpunkt als nicht verfügbar. Der Client versucht, den aktuellen Vorgang zu wiederholen, und alle zukünftigen Vorgänge werden stets nach der Reihenfolge der Bevorzugung an die nächste Region weitergeleitet.

## <a name="adding-a-region-to-an-account"></a>Hinzufügen einer Region zu einem Konto

Der Azure Cosmos DB-SDK-Client liest alle 5 Minuten die Kontokonfiguration und aktualisiert die Regionen, die er erkennt.

Wenn Sie eine Region entfernen und sie später wieder dem Konto hinzufügen, verwendet das SDK dauerhaft wieder diese Region, sofern für die hinzugefügte Region in der SDK-Konfiguration eine höhere regionale Bevorzugung als für die Region festgelegt ist, mit der zu diesem Zeitpunkt eine Verbindung besteht. Nachdem die hinzugefügte Region erkannt wurde, werden alle zukünftigen Anforderungen an sie weitergeleitet.

Wenn Sie den Client so konfigurieren, dass er vorzugsweise eine Verbindung mit einer Region herstellt, die im Azure Cosmos DB-Konto nicht enthalten ist, wird die bevorzugte Region ignoriert. Wenn Sie diese Region zu einem späteren Zeitpunkt hinzufügen, wird sie vom Client erkannt und stets für Verbindungen bevorzugt.

## <a name="fail-over-the-write-region-in-a-single-write-region-account"></a><a id="manual-failover-single-region"></a>Durchführen eines Failovers der Schreibregion in einem Konto mit nur einer Schreibregion

Wenn Sie ein Failover der aktuellen Schreibregion initiieren, schlägt die nächste Schreibanforderung mit einer bekannten Back-End-Antwort fehl. Wenn diese Antwort erkannt wird, fragt der Client das Konto ab, um die neue Schreibregion zu ermitteln. Er setzt dann den aktuellen Vorgang fort und leitet alle zukünftigen Schreibvorgänge stets an die neue Region weiter.

## <a name="regional-outage"></a>Regionaler Ausfall

Wenn es sich bei dem Konto um eine einzelne Schreibregion handelt und der regionale Ausfall während eines Schreibvorgangs auftritt, ähnelt das Verhalten einem [manuellen Failover](#manual-failover-single-region). Bei Leseanforderungen oder Konten mit mehreren Schreibregionen ähnelt das Verhalten dem [Entfernen einer Region](#remove-region).

## <a name="session-consistency-guarantees"></a>Garantien für die Sitzungskonsistenz

Wenn Sie [Sitzungskonsistenz](consistency-levels.md#guarantees-associated-with-consistency-levels) verwenden, muss der Client sicherstellen, dass er die eigenen Schreibvorgänge lesen kann. In Konten mit nur einer Schreibregion, bei denen sich die bevorzugte Leseregion von der Schreibregion unterscheidet, kann es vorkommen, dass der Benutzer einen Schreibvorgang auslöst und bei einem Lesevorgang aus einer lokalen Region die lokale Region die Datenreplikation noch nicht erhalten hat (physikalische Beschränkung der maximalen Übertragungsgeschwindigkeit). In solchen Fällen erkennt das SDK diesen Fehler beim Lesevorgang und wiederholt den Lesevorgang für die primäre Region, um Sitzungskonsistenz sicherzustellen.

## <a name="transient-connectivity-issues-on-tcp-protocol"></a>Vorübergehende Konnektivitätsprobleme beim TCP-Protokoll

In Szenarien, in denen der Azure Cosmos DB-SDK-Client für die Verwendung des TCP-Protokolls konfiguriert ist, kann es für eine bestimmte Anforderung vorkommen, dass die Netzwerkbedingungen die Kommunikation mit einem bestimmten Endpunkt vorübergehend beeinträchtigen. Bei diesen temporären Netzwerkbedingungen kann es sich um TCP-Timeouts handeln. Der Client versucht einige Sekunden lang, die Anforderung lokal auf demselben Endpunkt zu wiederholen.

Wenn der Benutzer eine Liste bevorzugter Regionen mit mehreren Regionen konfiguriert hat, das Azure Cosmos DB-Konto mehrere Schreibregionen oder eine einzelne Schreibregion aufweist und der Vorgang eine Leseanforderung ist, wiederholt der Client den einzelnen Vorgang in der nächsten Region aus der Liste bevorzugter Regionen.

## <a name="next-steps"></a>Nächste Schritte

* Sehen Sie sich die [SLAs für Verfügbarkeit](high-availability.md#slas-for-availability) an.
* Verwenden Sie das neueste [.NET SDK](sql-api-sdk-dotnet-standard.md).
* Verwenden Sie das neueste [Java SDK](sql-api-sdk-java-v4.md).
* Verwenden Sie das neueste [Python SDK](sql-api-sdk-python.md).
* Verwenden Sie das neueste [Node SDK](sql-api-sdk-node.md).
