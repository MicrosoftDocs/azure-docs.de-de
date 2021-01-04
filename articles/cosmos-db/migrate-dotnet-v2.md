---
title: Migrieren einer Anwendung für die Verwendung des Azure Cosmos DB .NET SDK 2.0 (Microsoft.Azure.Cosmos)
description: Erfahren Sie, wie Sie ein Upgrade Ihrer vorhandenen .NET-Anwendung von v1 des SDK auf .NET SDK v2 für Core-API (SQL) durchführen.
author: stefArroyo
ms.author: esarroyo
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/15/2020
ms.openlocfilehash: 88c40452da72ed4ab43d2d7613636136a5b78cfe
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94550028"
---
# <a name="migrate-your-application-to-use-the-azure-cosmos-db-net-sdk-v2"></a>Migrieren einer Anwendung für die Verwendung des Azure Cosmos DB .NET SDK v2

> [!IMPORTANT]
> Es ist zu beachten, dass derzeit v3 des .NET SDK verfügbar ist und ein Plan für die Migration von v2 zu v3 [hier](migrate-dotnet-v3.md) zur Verfügung steht. Informationen zum Azure Cosmos DB .NET SDK v2 finden Sie in den [Versionshinweisen](sql-api-sdk-dotnet.md), im [.NET-GitHub-Repository](https://github.com/Azure/azure-cosmos-dotnet-v2), in den [Tipps zur Leistungssteigerung](performance-tips.md) für das .NET SDK v2 sowie im [Leitfaden zur Problembehandlung](troubleshoot-dot-net-sdk.md).
>

Dieser Artikel enthält einige Überlegungen zum Upgrade Ihrer vorhandenen v1 .NET-Anwendung auf Azure Cosmos DB .NET SDK v2 für Core-API (SQL). Azure Cosmos DB .NET SDK v2 entspricht dem Namespace `Microsoft.Azure.DocumentDB`. Sie können die Informationen in diesem Dokument verwenden, wenn Sie Ihre Anwendung von einer der folgenden Azure Cosmos DB .NET-Plattformen für die Verwendung des v2 SDK `Microsoft.Azure.Cosmos` migrieren:

* Azure Cosmos DB .NET Framework SDK v1 für SQL-API
* Azure Cosmos DB .NET Core SDK v1 für SQL-API

## <a name="whats-available-in-the-net-v2-sdk"></a>Was ist im .NET v2 SDK verfügbar?

Das v2 SDK enthält viele Verbesserungen der Benutzerfreundlichkeit und Leistung, darunter:

* Unterstützung des TCP-Direktmodus für Nicht-Windows-Clients
* Unterstützung von Schreibvorgängen in mehreren Regionen
* Verbesserungen der Abfrageleistung
* Unterstützung für Geo-/Geometriesammlungen und Indizierung
* Weitere Verbesserungen der direkten/TCP-Transportdiagnose
* Aktualisierungen des direkten TCP-Transportstapels zur Verringerung der Anzahl hergestellter Verbindungen
* Verbesserungen der Latenzreduzierung bei RequestTimeout

Der Großteil der Wiederholungslogik und der unteren Ebenen des SDK bleibt weitgehend unverändert.

## <a name="why-migrate-to-the-net-v2-sdk"></a>Gründe für das Migrieren zum .NET v2 SDK

Neben den zahlreichen Leistungsverbesserungen werden beim neuesten SDK getätigte Investitionen in neue Funktionen nicht auf ältere Versionen zurückportiert.

Außerdem werden die älteren SDKs durch neuere Versionen ersetzt, und das v1 SDK wird in den [Wartungsmodus](sql-api-sdk-dotnet.md) gesetzt. Für eine optimale Entwicklung empfiehlt es sich, die Anwendung zu einer neueren Version des SDK zu migrieren.

## <a name="major-changes-from-v1-sdk-to-v2-sdk"></a>Wesentliche Änderungen vom v1 SDK zum v2 SDK

### <a name="direct-mode--tcp"></a>Direkter Modus + TCP

Das .NET v2 SDK unterstützt jetzt sowohl den direkten Modus als auch den Gatewaymodus. Der direkte Modus unterstützt die Konnektivität über das TCP-Protokoll und bietet eine bessere Leistung, da eine direkte Verbindung mit den Back-End-Replikaten mit weniger Netzwerkhops hergestellt wird.

Weitere Informationen finden Sie im [Leitfaden zu den Azure Cosmos DB SQL SDK-Konnektivitätsmodi](sql-sdk-connection-modes.md).

### <a name="session-token-formatting"></a>Sitzungstokenformatierung

Das v2 SDK verwendet nicht mehr das *einfache* Sitzungstokenformat wie in v1, sondern die *Vektorformatierung*. Das Format muss bei der Übergabe an die Clientanwendung mit unterschiedlichen Versionen konvertiert werden, da die Formate nicht austauschbar sind.

Weitere Informationen finden Sie unter [Konvertieren von Sitzungstokenformaten im .NET SDK](how-to-convert-session-token.md).

### <a name="using-the-net-change-feed-processor-sdk"></a>Verwenden des .NET Change Feed Processor-SDK

Die .NET Change Feed Processor-Bibliothek 2.1.x erfordert `Microsoft.Azure.DocumentDB` 2.0 oder höher.

Die 2.1.x-Bibliothek weist die folgenden wichtigen Änderungen auf:

* Verbesserungen hinsichtlich Stabilität und Diagnostizierbarkeit
* Verbesserte Behandlung von Fehlern und Ausnahmen
* Zusätzliche Unterstützung für partitionierte Leasesammlungen
* Größere Erweiterungen zum Implementieren der `ChangeFeedDocument`-Schnittstelle und -Klasse für zusätzliche Fehlerbehandlung und -ablaufverfolgung
* Zusätzliche Unterstützung für die Verwendung eines benutzerdefinierten Speichers für das Beibehalten von Fortsetzungstoken pro Partition

Weitere Informationen finden Sie in den [Versionshinweisen](sql-api-sdk-dotnet-changefeed.md) zur Änderungsfeed-Prozessorbibliothek.

### <a name="using-the-bulk-executor-library"></a>Verwenden der BulkExecutor-Bibliothek

Die v2 BulkExecutor-Bibliothek weist derzeit eine Abhängigkeit vom Azure Cosmos DB .NET SDK 2.5.1 oder höher auf.

Weitere Informationen finden Sie unter [Azure Cosmos DB-BulkExecutor-Bibliothek – Übersicht](bulk-executor-overview.md) und in den [Versionshinweisen](sql-api-sdk-bulk-executor-dot-net.md) zur .NET-BulkExecutor-Bibliothek.

## <a name="next-steps"></a>Nächste Schritte

* Informieren Sie sich über [zusätzliche Tipps zur Leistungssteigerung](sql-api-get-started.md) mithilfe von Azure Cosmos DB für SQL-API v2 zur Optimierung Ihrer Anwendung für maximale Leistung.
* Erfahren Sie mehr über die [Möglichkeiten, die Ihnen das v2 SKD bietet](sql-api-dotnet-samples.md).