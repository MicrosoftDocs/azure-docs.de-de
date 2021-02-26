---
title: Behandeln häufiger Fehler in der Azure Cosmos DB-API für MongoDB
description: In diesem Dokument erfahren Sie, wie Sie häufige Probleme in der Azure Cosmos DB-API für MongoDB behandeln.
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 07/15/2020
ms.author: chrande
ms.openlocfilehash: f4ed242dced4798f5f416dae90ef2d6b6bde0e06
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99258183"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-dbs-api-for-mongodb"></a>Behandeln häufiger Probleme in der Azure Cosmos DB-API für MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Im folgenden Artikel werden allgemeine Fehler und Lösungen für Bereitstellung beschrieben, die die Azure Cosmos DB-API für MongoDB verwenden.

>[!Note]
> Azure Cosmos DB hostet nicht die MongoDB-Engine. Es bietet eine Implementierung für das Wire Protocol von MongoDB. Daher finden sich einige dieser Fehler nur in der Azure Cosmos DB-API für MongoDB. 

## <a name="common-errors-and-solutions"></a>Häufige Fehler und Lösungen

| Code       | Fehler                | BESCHREIBUNG  | Lösung  |
|------------|----------------------|--------------|-----------|
| 2 | Der Indexpfad zum angegebenen ORDER BY-Element ist ausgeschlossen, oder die ORDER BY-Abfrage verfügt nicht über einen entsprechenden zusammengesetzten Index für die Bereitstellung. | Die Abfrage fordert eine Sortierung für ein Feld an, das nicht indiziert ist. | Erstellen Sie einen übereinstimmenden Index (oder zusammengesetzten Index) für die durchzuführende Sortierabfrage. |
| 13 | Nicht autorisiert | Der Anforderung fehlen die Berechtigungen für die vollständige Ausführung. | Stellen Sie sicher, dass Sie die richtigen Berechtigungen für Ihre Datenbank und Sammlung festlegen.  |
| 16 | InvalidLength | Die angegebene Anforderung weist eine ungültige Länge auf. | Wenn Sie die explain()-Funktion verwenden, stellen Sie sicher, dass Sie nur einen einzigen Vorgang bereitstellen. |
| 26 | NamespaceNotFound | Die Datenbank oder Sammlung, auf die in der Abfrage verwiesen wird, wird nicht gefunden. | Stellen Sie sicher, dass der Name Ihrer Datenbank bzw. Sammlung exakt dem Namen in Ihrer Abfrage entspricht.|
| 50 | ExceededTimeLimit | Die Anforderung hat das Ausführungstimeout von 60 Sekunden überschritten. |  Dieser Fehler kann vielfältige Ursachen haben. Eine dieser Ursachen kann darin bestehen, dass die Kapazität der aktuell zugeordneten Anforderungseinheiten nicht ausreicht, um die Anforderung abzuschließen. Dieses Problem kann durch Erhöhen der Anforderungseinheiten dieser Sammlung oder Datenbank behoben werden. In anderen Fällen kann dieser Fehler umgangen werden, indem eine große Anforderung in kleinere Anforderungen aufgeteilt wird. Das Wiederholen eines Schreibvorgangs, bei dem dieser Fehler aufgetreten ist, kann zu einem doppelten Schreibvorgang führen. <br><br>Gehen Sie wie folgt vor, wenn Sie große Datenmengen löschen möchten, ohne dass sich dies auf RUs auswirkt: <br>\- Erwägen Sie die Verwendung einer Gültigkeitsdauer (basierend auf dem Zeitstempel): [Ablauf von Daten mit der Azure Cosmos DB-API für MongoDB](https://docs.microsoft.com/azure/cosmos-db/mongodb-time-to-live) <br>\- Verwenden Sie Cursor-/Batchgröße, um den Löschvorgang durchzuführen. Sie können jeweils ein Dokument abrufen und per Schleife löschen. Dies ermöglicht das langsame Löschen von Daten ohne Beeinträchtigung Ihrer Produktionsanwendung.|
| 61 | ShardKeyNotFound | Das Dokument in Ihrer Anforderung enthielt den Shardschlüssel (Azure Cosmos DB-Partitionsschlüssel) der Sammlung nicht. | Stellen Sie sicher, dass in der Anforderung der Shardschlüssel der Sammlung verwendet wird.|
| 66 | ImmutableField | Die Anforderung versucht, ein unveränderliches Feld zu ändern. | „id“-Felder sind unveränderlich. Stellen Sie sicher, dass Ihre Anforderung nicht versucht, dieses Feld zu aktualisieren. |
| 67 | CannotCreateIndex | Die Anforderung zum Erstellen eines Indexes kann nicht abgeschlossen werden. | In einem Container können bis zu 500 Einzelfeldindizes erstellt werden. Ein zusammengesetzter Index kann bis zu acht Felder enthalten (zusammengesetzte Indizes werden in Version 3.6 und höher unterstützt). |
| 115 | CommandNotSupported | Die Anforderung wird nicht unterstützt. | In der Fehlermeldung sollten weitere Informationen angegeben sein. Wenn diese Funktion für Ihre Bereitstellungen wichtig ist, informieren Sie uns per Supportticket im [Azure-Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) darüber. |
| 11000 | DuplicateKey | Der Shardschlüssel (Azure Cosmos DB-Partitionsschlüssel) des einzufügenden Dokuments ist in der Sammlung bereits vorhanden, oder eine Einschränkung in Bezug auf eindeutige Indexfelder wurde verletzt. | Verwenden Sie die update()-Funktion, um ein vorhandenes Dokument zu aktualisieren. Wenn eine Einschränkung in Bezug auf eindeutige Indexfelder verletzt wurde, fügen Sie einen Feldwert in das Dokument ein, der im Shard bzw. in der Partition noch nicht vorhanden ist, oder aktualisieren Sie das Dokument mit einem entsprechenden Feldwert. |
| 16500 | TooManyRequests  | Die Gesamtanzahl der verbrauchten Anforderungseinheiten hat die bereitgestellte Anforderungseinheitenrate für die Sammlung überschritten und wurde gedrosselt. | Skalieren Sie über das Azure-Portal ggf. den Durchsatz, der einem Container oder einer Gruppe von Containern zugewiesen ist, oder wiederholen Sie den Vorgang. Wenn Sie [SSR (Server-Side Retry, serverseitige Wiederholung) aktivieren](prevent-rate-limiting-errors.md), versucht Azure Cosmos DB automatisch, die Anforderungen zu wiederholen, die aufgrund dieses Fehlers nicht ausgeführt werden konnten. |
| 16501 | ExceededMemoryLimit | Der Vorgang ist ein mehrinstanzenfähiger Dienst und hat die Speicherzuweisung des Clients überschritten. | Verringern Sie den Umfang des Vorgangs mithilfe restriktiverer Abfragekriterien, oder wenden Sie sich im [Azure-Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) an den Support. Beispiel: `db.getCollection('users').aggregate([{$match: {name: "Andy"}}, {$sort: {age: -1}}]))` |
| 40324 | Nicht erkannter Name einer Pipelinestage | Der Stagename in der Anforderung Ihrer Aggregationspipeline wurde nicht erkannt. | Stellen Sie sicher, dass alle Aggregationspipelinenamen in Ihrer Anforderung gültig sind. |
| - | Probleme mit der MongoDB-Wire-Version | Ältere Versionen von MongoDB-Treibern können den Namen des Azure Cosmos-Kontos in den Verbindungszeichenfolgen nicht erkennen. | Fügen Sie am Ende der Verbindungszeichenfolge Ihrer Azure Cosmos DB-API für MongoDB *appName=@**Kontoname**@* hinzu, und ersetzen Sie dabei **Kontoname** durch den Namen Ihres Cosmos DB-Kontos. |
| - | Netzwerkprobleme beim MongoDB-Client (beispielsweise Socket- oder endOfStream-Ausnahmen)| Bei der Netzwerkanforderung ist ein Fehler aufgetreten. Dies wird häufig dadurch verursacht, dass der MongoDB-Client versucht, eine inaktive TCP-Verbindung zu verwenden. MongoDB-Treiber nutzen häufig Verbindungspools, was dazu führt, dass für Anforderungen zufällige Verbindungen aus dem Pool ausgewählt werden. Bei inaktiven Verbindungen tritt im Azure Cosmos DB-Endpunkt in der Regel nach vier Minuten ein Timeout auf. | Sie können diese fehlerhaften Anforderungen über den Anwendungscode wiederholen, die Treibereinstellungen Ihres MongoDB-Clients so ändern, dass inaktive TCP-Verbindungen vor Ablauf des vierminütigen Timeoutfensters abgebrochen werden, oder die Keep-Alive-Einstellungen des Betriebssystems so konfigurieren, dass TCP-Verbindungen im aktiven Status beibehalten werden.<br><br>Zur Vermeidung von Konnektivitätsmeldungen kann es ratsam sein, die Verbindungszeichenfolge zu ändern und „maxConnectionIdleTime“ auf 1 - 2 Minuten festzulegen.<br>- Mongo-Treiber: Konfigurieren Sie Folgendes: _maxIdleTimeMS=120000* <br>- Node.js: Konfigurieren Sie Folgendes: *socketTimeoutMS=120000*, *autoReconnect* = true, *keepAlive* = true, *keepAliveInitialDelay* = 3 minutes

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie, wie Sie [Studio 3T](mongodb-mongochef.md) mit der API für MongoDB von Azure Cosmos DB verwenden.
- Erfahren Sie, wie Sie [Robo 3T](mongodb-robomongo.md) mit der API für MongoDB von Azure Cosmos DB verwenden.
- Untersuchen Sie MongoDB-[Beispiele](mongodb-samples.md) mit der API für MongoDB von Azure Cosmos DB.
