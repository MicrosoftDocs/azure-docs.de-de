---
title: Datenbanktransaktionen und Steuerung für optimistische Nebenläufigkeit in Azure Cosmos DB
description: In diesem Artikel werden Datenbanktransaktionen und die Steuerung für optimistische Nebenläufigkeit in Azure Cosmos DB beschrieben.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/04/2019
ms.reviewer: sngun
ms.openlocfilehash: bdfbe5106f220a9fe4a3568709187b9071bc7917
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93334275"
---
# <a name="transactions-and-optimistic-concurrency-control"></a>Transaktionen und Steuerung für optimistische Nebenläufigkeit
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Datenbanktransaktionen bieten ein sicheres und vorhersagbares Programmiermodell zur Verarbeitung von gleichzeitig an den Daten vorgenommenen Änderungen. In herkömmlichen relationalen Datenbanken wie SQL Server können Sie die Geschäftslogik mithilfe von gespeicherten Prozeduren und/oder Triggern schreiben und zur direkten Ausführung innerhalb der Datenbank-Engine an den Server senden. Sie müssen bei herkömmlichen relationalen Datenbanken zwei verschiedene Programmiersprachen verwenden: die (nicht transaktionale) Anwendungsprogrammiersprache, z. B. JavaScript, Python, C#, Java usw., und die transaktionale Programmiersprache (T-SQL), die in der Datenbank nativ ausgeführt wird.

Die Datenbank-Engine in Azure Cosmos DB unterstützt vollständige ACID-konforme Transaktionen (Atomarität, Konsistenz, Isolation, Dauerhaftigkeit) mit Momentaufnahmeisolation. Alle Datenbankvorgänge innerhalb des Bereichs der [logischen Partition](partitioning-overview.md) eines Containers werden transaktional in der Datenbank-Engine ausgeführt, die im Replikat der Partition gehostet wird. Diese Vorgänge umfassen sowohl Schreibvorgänge (Aktualisieren eines oder mehrerer Elemente in der logischen Partition) als auch Lesevorgänge. In der folgenden Tabelle sind verschiedene Vorgänge und Transaktionstypen aufgeführt:

| **Vorgang**  | **Operation Type** | **Transaktion für einzelnes oder mehrere Elemente** |
|---------|---------|---------|
| Einfügen (ohne vorangestellten oder nachgestellten Trigger) | Schreiben | Transaktion für einzelnes Element |
| Einfügen (mit vorangestelltem oder nachgestelltem Trigger) | Schreiben und Lesen | Transaktion für mehrere Elemente |
| Ersetzen (ohne vorangestellten oder nachgestellten Trigger) | Schreiben | Transaktion für einzelnes Element |
| Ersetzen (mit vorangestelltem oder nachgestelltem Trigger) | Schreiben und Lesen | Transaktion für mehrere Elemente |
| Upsert (ohne vorangestellten oder nachgestellten Trigger) | Schreiben | Transaktion für einzelnes Element |
| Upsert (mit vorangestelltem oder nachgestelltem Trigger) | Schreiben und Lesen | Transaktion für mehrere Elemente |
| Löschen (ohne vorangestellten oder nachgestellten Trigger) | Schreiben | Transaktion für einzelnes Element |
| Löschen (mit vorangestelltem oder nachgestelltem Trigger) | Schreiben und Lesen | Transaktion für mehrere Elemente |
| Gespeicherte Prozedur ausführen | Schreiben und Lesen | Transaktion für mehrere Elemente |
| Vom System initiierte Ausführung einer Mergeprozedur | Schreiben | Transaktion für mehrere Elemente |
| Vom System initiierte Ausführung zum Löschen von Elementen basierend auf der Gültigkeitsdauer (TTL) eines Elements | Schreiben | Transaktion für mehrere Elemente |
| Lesen | Lesen | Transaktion für einzelnes Element |
| Änderungsfeed | Lesen | Transaktion für mehrere Elemente |
| Paginiertes Lesen | Lesen | Transaktion für mehrere Elemente |
| Paginierte Abfrage | Lesen | Transaktion für mehrere Elemente |
| UDF als Teil der paginierten Abfrage ausführen | Lesen | Transaktion für mehrere Elemente |

## <a name="multi-item-transactions"></a>Transaktionen für mehrere Elemente

In Azure Cosmos DB können Sie [gespeicherte Prozeduren, voran- und nachgestellte Trigger, benutzerdefinierte Funktionen (UDFs)](stored-procedures-triggers-udfs.md) und Zusammenführungsprozeduren in JavaScript schreiben. Azure Cosmos DB unterstützt die JavaScript-Ausführung nativ innerhalb der Datenbank-Engine. Sie können gespeicherte Prozeduren, voran- und nachgestellte Trigger, benutzerdefinierte Funktionen (UDFs) und Mergeprozeduren für einen Container registrieren und später in der Azure Cosmos-Datenbank-Engine transaktional ausführen. Das Schreiben der Anwendungslogik in JavaScript ermöglicht eine natürliche Ausdrucksweise für Ablaufsteuerung, Variablen-Bereichssteuerung, Zuweisung und Integration von Grundtypen zur Ausnahmebehandlung für die Datenbanktransaktionen direkt in der Programmiersprache JavaScript.

Die JavaScript-basierten gespeicherten Prozeduren, Trigger, UDFs und Mergeprozeduren werden für alle Elemente innerhalb der logischen Partition in einer umgebenden ACID-Transaktion mit Momentaufnahmeisolation umschlossen. Wenn das JavaScript-Programm während des Ausführungsverlaufs eine Ausnahme auslöst, wird die gesamte Transaktion abgebrochen und zurückgesetzt. Das sich ergebende Programmiermodell ist einfach, aber dennoch leistungsstark. JavaScript-Entwickler erhalten ein beständiges Programmiermodell, während sie weiterhin ihre vertrauten Sprachkonstrukte und Bibliotheksstammfunktionen verwenden können.

Die Möglichkeit zur direkten Ausführung von JavaScript innerhalb der Datenbank-Engine bietet die leistungsfähige und transaktionale Ausführung von Datenbankvorgängen für die Elemente eines Containers. Da die Azure Cosmos-Datenbank-Engine JSON und JavaScript nativ unterstützt, treten darüber hinaus keine Impedanzabweichungen zwischen den Typsystemen einer Anwendung und der Datenbank auf.

## <a name="optimistic-concurrency-control"></a>Steuerung durch vollständige Parallelität

Mit der Steuerung für optimistische Nebenläufigkeit können Sie den Verlust von Aktualisierungen und Löschvorgängen vermeiden. Gleichzeitige, konfliktverursachende Vorgänge unterliegen der regulären pessimistischen Sperrung der Datenbank-Engine, die in der logischen Partition gehostet wird, die das Element besitzt. Wenn in zwei parallelen Vorgängen versucht wird, die neueste Version eines Elements in einer logischen Partition zu aktualisieren, ist nur einer der beiden Vorgänge erfolgreich. Wenn jedoch in einem oder beiden Vorgängen, mit denen versucht wird, gleichzeitig dasselbe Element zu aktualisieren, zuvor bereits ein älterer Wert des Elements gelesen wurde, kann in der Datenbank nicht ermittelt werden, ob der zuvor mit den konfliktverursachenden Vorgängen gelesene Wert tatsächlich der neueste Wert des Elements war. Dies kann zum Glück mit der Steuerung für **optimistische Nebenläufigkeit (Optimistic Concurrency Control, OCC)** erkannt werden, bevor die beiden Vorgänge die Transaktionsgrenze innerhalb der Datenbank-Engine überschreiten. Mit der Steuerung für optimistische Nebenläufigkeit werden Ihre Daten davor geschützt, dass Änderungen, die von anderen Benutzern vorgenommen wurden, versehentlich überschrieben werden. Ebenso wird verhindert, dass andere Benutzer versehentlich die von Ihnen vorgenommenen Änderungen überschreiben.

Die gleichzeitigen Aktualisierungen eines Elements unterliegen über die Kommunikationsprotokollebene von Azure Cosmos DB der Steuerung für optimistische Nebenläufigkeit. In der Azure Cosmos-Datenbank wird sichergestellt, dass die clientseitige Version des Elements, das Sie aktualisieren (oder löschen), der Version des Elements im Azure Cosmos-Container entspricht. Dadurch wird sichergestellt, dass Ihre Schreibvorgänge geschützt sind und nicht versehentlich durch Schreibvorgänge von anderen Benutzern überschrieben werden und umgekehrt. In einer Umgebung mit mehreren Benutzern verhindert die Steuerung für optimistische Nebenläufigkeit, dass Sie versehentlich die falsche Version eines Elements löschen oder aktualisieren. Elemente werden so vor den berüchtigten Problemen mit verloren gegangenen Aktualisierungen oder Löschvorgängen geschützt.

Jedes in einem Azure Cosmos-Container gespeicherte Element weist eine systemdefinierte `_etag`-Eigenschaft auf. Der Wert von `_etag` wird automatisch generiert und bei jeder Aktualisierung des Elements vom Server aktualisiert. `_etag` kann mit dem vom Client bereitgestellten Anforderungsheader `if-match` verwendet werden, sodass der Server festlegen kann, ob ein Element bedingt aktualisiert werden kann. Wenn der Wert des `if-match`-Headers dem Wert von `_etag` auf dem Server entspricht, wird das Element aktualisiert. Wenn der Wert des `if-match`-Anforderungsheaders nicht mehr aktuell ist, lehnt der Server den Vorgang mit der Antwortmeldung „HTTP 412 – Vorbedingungsfehler“ ab. Der Client kann dann das Element erneut abrufen, um die aktuelle Version des Elements auf dem Server zu erhalten, oder die Version des Elements auf dem Server mit dem eigenen `_etag`-Wert für das Element überschreiben. Darüber hinaus kann `_etag` mit dem `if-none-match`-Header verwendet werden, um festzustellen, ob ein erneutes Abrufen einer Ressource erforderlich ist.

Der Wert `_etag` des Elements ändert sich bei jeder Aktualisierung des Elements. Bei Vorgängen zum Ersetzen von Elementen muss `if-match` ausdrücklich als Teil der Anforderungsoptionen ausgedrückt werden. Einen entsprechenden Beispielcode finden Sie in [GitHub](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L676-L772). `_etag`-Werte werden für alle geschriebenen Elemente, die von der gespeicherten Prozedur verwendet werden, implizit überprüft. Wenn ein Konflikt erkannt wird, setzt die gespeicherte Prozedur die Transaktion zurück und löst eine Ausnahme aus. Mit dieser Methode werden entweder alle oder keine Schreibvorgänge in der gespeicherten Prozedur unteilbar angewandt. Dies ist ein Signal für die Anwendung, Aktualisierungen erneut anzuwenden und die ursprüngliche Clientanforderung zu wiederholen.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Datenbanktransaktionen und der Steuerung für optimistische Nebenläufigkeit finden Sie in den folgenden Artikeln:

- [Arbeiten mit Azure Cosmos-Datenbanken, -Containern und -Elementen](account-databases-containers-items.md)
- [Konsistenzebenen](consistency-levels.md)
- [Konflikttypen und Konfliktauflösungsrichtlinien](conflict-resolution-policies.md)
- [Verwenden von TransactionalBatch](transactional-batch.md)
- [Gespeicherte Prozeduren, Trigger und benutzerdefinierte Funktionen](stored-procedures-triggers-udfs.md)
