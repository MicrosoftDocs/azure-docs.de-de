---
title: Behandeln häufiger Fehler in der Azure Cosmos DB-API für MongoDB
description: In diesem Dokument erfahren Sie, wie Sie häufige Probleme in der Azure Cosmos DB-API für MongoDB behandeln.
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 07/15/2020
ms.author: chrande
ms.openlocfilehash: 9d76c3d9943300f88a146e82b862624d491cf546
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96017813"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-dbs-api-for-mongodb"></a>Behandeln häufiger Probleme in der Azure Cosmos DB-API für MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Im folgenden Artikel werden allgemeine Fehler und Lösungen für Datenbanken beschrieben, die die Azure Cosmos DB-API für MongoDB verwenden.

>[!Note]
> Azure Cosmos DB hostet nicht die MongoDB-Engine. Der Dienst bietet eine Implementierung der [Wire Protocol-Version 3.6](mongodb-feature-support-36.md) von MongoDB und Legacy-Unterstützung für die [Wire Protocol-Version 3.2](mongodb-feature-support.md). Daher treten einige dieser Fehler nur in der Azure Cosmos DB-API für MongoDB auf. 

## <a name="common-errors-and-solutions"></a>Häufige Fehler und Lösungen

| Fehler               | Code  | BESCHREIBUNG  | Lösung  |
|---------------------|-------|--------------|-----------|
| ExceededTimeLimit   | 50 | Die Anforderung hat das Ausführungstimeout von 60 Sekunden überschritten. | Dieser Fehler kann vielfältige Ursachen haben. Eine Ursache ist, wenn die Kapazität der aktuell zugeordneten Anforderungseinheiten nicht ausreicht, um die Anforderung abzuschließen. Dieses Problem kann durch Erhöhen der Anforderungseinheiten dieser Sammlung oder Datenbank behoben werden. In anderen Fällen kann dieser Fehler umgangen werden, indem eine große Anforderung in kleinere Anforderungen aufgeteilt wird. |
| TooManyRequests     | 16500 | Die Gesamtanzahl der verbrauchten Anforderungseinheiten hat die bereitgestellte Anforderungseinheitenrate für die Sammlung überschritten und wurde gedrosselt. | Skalieren Sie über das Azure-Portal ggf. den Durchsatz, der einem Container oder einer Gruppe von Containern zugewiesen ist, oder wiederholen Sie den Vorgang. |
| ExceededMemoryLimit | 16501 | Der Vorgang ist ein mehrinstanzenfähiger Dienst und hat die Speicherzuweisung des Clients überschritten. | Verringern Sie den Umfang des Vorgangs mithilfe restriktiverer Abfragekriterien, oder wenden Sie sich im [Azure-Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) an den Support. Beispiel: `db.getCollection('users').aggregate([{$match: {name: "Andy"}}, {$sort: {age: -1}}]))` |
| Der Indexpfad zum angegebenen ORDER BY-Element ist ausgeschlossen./Die ORDER BY-Abfrage verfügt nicht über einen entsprechenden zusammengesetzten Index für die Bereitstellung. | 2 | Die Abfrage fordert eine Sortierung für ein Feld an, das nicht indiziert ist. | Erstellen Sie einen übereinstimmenden Index (oder zusammengesetzten Index) für die durchzuführende Sortierabfrage. |
| Probleme mit der MongoDB-Wire-Version | - | Ältere Versionen von MongoDB-Treibern können den Namen des Azure Cosmos-Kontos in den Verbindungszeichenfolgen nicht erkennen. | Fügen Sie am Ende der Verbindungszeichenfolge Ihrer Azure Cosmos DB-API für MongoDB *appName=@**Kontoname**@* hinzu, und ersetzen Sie dabei ***Kontoname*** durch den Namen Ihres Cosmos DB-Kontos. |

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie, wie Sie [Studio 3T](mongodb-mongochef.md) mit der API für MongoDB von Azure Cosmos DB verwenden.
- Erfahren Sie, wie Sie [Robo 3T](mongodb-robomongo.md) mit der API für MongoDB von Azure Cosmos DB verwenden.
- Untersuchen Sie MongoDB-[Beispiele](mongodb-samples.md) mit der API für MongoDB von Azure Cosmos DB.

