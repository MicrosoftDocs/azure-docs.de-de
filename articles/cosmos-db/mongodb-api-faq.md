---
title: Häufig gestellte Fragen zur Azure Cosmos DB-API für MongoDB
description: Hier finden Sie Antworten auf häufig gestellte Fragen zur Azure Cosmos DB-API für MongoDB.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 04/28/2020
ms.author: sngun
ms.openlocfilehash: 4c93aea5a37fb286b49dabfb97522bf40c8f5429
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93333170"
---
# <a name="frequently-asked-questions-about-the-azure-cosmos-dbs-api-for-mongodb"></a>Häufig gestellte Fragen zur Azure Cosmos DB-API für MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Die Azure Cosmos DB-API für MongoDB ist eine Wire Protocol-Kompatibilitätsebene, mit der Anwendungen auf einfache und transparente Weise über vorhandene, von der Community unterstützte SDKs und Treiber für MongoDB mit der nativen Azure Cosmos-Datenbank-Engine kommunizieren können. Entwickler können jetzt vorhandene MongoDB-Toolketten und -Fähigkeiten verwenden, um Anwendungen zu erstellen, die Azure Cosmos DB nutzen. Entwickler profitieren von den einzigartigen Funktionen von Azure Cosmos DB, z. B. globale Verteilung mit Schreibvorgängen für mehrere Regionen, automatische Indizierung, Sicherungsverwaltung, finanziell abgesicherte Vereinbarungen zum Servicelevel (SLAs) usw.

## <a name="how-do-i-connect-to-my-database"></a>Wie stelle ich eine Verbindung mit meiner Datenbank her?

Die schnellste Möglichkeit zum Herstellen einer Verbindung mit einer Cosmos-Datenbank mit der API für MongoDB von Azure Cosmos DB besteht darin, dass Sie zum [Azure-Portal](https://portal.azure.com) wechseln. Navigieren Sie zu Ihrem Konto, und klicken Sie dann im Navigationsmenü auf der linken Seite auf **Schnellstart**. Der Schnellstart ist die beste Möglichkeit, um Codeausschnitte für das Herstellen einer Verbindung mit Ihrer Datenbank abzurufen.

Azure Cosmos DB erzwingt strenge Sicherheitsanforderungen und -standards. Azure Cosmos DB-Konten erfordern eine Authentifizierung und eine sichere Kommunikation über TLS. Verwenden Sie daher unbedingt TLSv1.2.

Weitere Informationen finden Sie unter [Verbinden einer MongoDB-Anwendung mit Azure Cosmos DB](connect-mongodb-account.md).

## <a name="error-codes-while-using-azure-cosmos-dbs-api-for-mongodb"></a>Welche Fehlercodes können bei Verwenden der Azure Cosmos DB-API für MongoDB angezeigt werden?

Neben den allgemeinen MongoDB-Fehlercodes verfügt die Azure Cosmos DB-API für MongoDB über eigene, spezifische Fehlercodes:

| Fehler               | Code  | BESCHREIBUNG  | Lösung  |
|---------------------|-------|--------------|-----------|
| TooManyRequests     | 16500 | Die Gesamtanzahl der verbrauchten Anforderungseinheiten hat die bereitgestellte Anforderungseinheitenrate für den Container überschritten und wurde gedrosselt. | Erwägen Sie eine Skalierung des einem Container oder mehreren Containern zugewiesenen Durchsatzes über das Azure-Portal oder eine Wiederholung des Vorgangs. |
| ExceededMemoryLimit | 16501 | Der Vorgang ist ein mehrinstanzenfähiger Dienst und hat die Speicherzuweisung des Clients überschritten. | Verringern Sie den Umfang des Vorgangs mithilfe restriktiverer Abfragekriterien, oder wenden Sie sich im [Azure-Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) an den Support. <br><br> Beispiel: `db.getCollection('users').aggregate([{$match: {name: "Andy"}}, {$sort: {age: -1}}]))` |

## <a name="supported-drivers"></a>Unterstützte Treiber

### <a name="is-the-simba-driver-for-mongodb-supported-for-use-with-azure-cosmos-dbs-api-for-mongodb"></a>Wird die Verwendung des Simba-Treibers für MongoDB mit der Azure Cosmos DB-API für MongoDB unterstützt?

Ja, Sie können den Mongo-ODBC-Treiber von Simba mit der Azure Cosmos DB-API für MongoDB verwenden.

## <a name="next-steps"></a>Nächste Schritte

* [Erstellen einer .NET-Web-App mit der API für MongoDB von Azure Cosmos DB](create-mongodb-dotnet.md)
* [Erstellen einer Konsolen-App mit Java und der MongoDB-API in Azure Cosmos DB](create-mongodb-java.md)
