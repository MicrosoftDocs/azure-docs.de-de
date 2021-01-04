---
title: Arbeiten mit gespeicherten Prozeduren, Triggern und benutzerdefinierten Funktionen in Azure Cosmos DB
description: In diesem Artikel werden Konzepte wie gespeicherte Prozeduren, Trigger und benutzerdefinierte Funktionen in Azure Cosmos DB erläutert.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: tisande
ms.reviewer: sngun
ms.openlocfilehash: 0bd572da9bba9048e2c8b9c4b426056620c4c265
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93340701"
---
# <a name="stored-procedures-triggers-and-user-defined-functions"></a>Gespeicherte Prozeduren, Trigger und benutzerdefinierte Funktionen
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB ermöglicht die in die JavaScript-Sprache integrierte, transaktionale Ausführung von JavaScript. Wenn Sie die SQL-API in Azure Cosmos DB verwenden, können Sie **gespeicherte Prozeduren** , **Trigger** und **benutzerdefinierte Funktionen** (User-Defined Functions, UDFs) in der JavaScript-Sprache schreiben. Sie können Ihre Logik in JavaScript-Code schreiben, der in der Datenbank-Engine ausgeführt wird. Sie können Trigger, gespeicherte Prozeduren und benutzerdefinierte Funktionen im [Azure-Portal](https://portal.azure.com/), mithilfe der [JavaScript-Language Integrated Query-API in Azure Cosmos DB](javascript-query-api.md) oder mit den [SQL-API-Client-SDKs von Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md) erstellen und ausführen.

## <a name="benefits-of-using-server-side-programming"></a>Nutzen der serverseitigen Programmierung

Das Schreiben von gespeicherten Prozeduren, Triggern und benutzerdefinierten Funktionen in JavaScript ermöglicht Ihnen das Erstellen umfassender Anwendungen, die folgende Vorteile bieten:

* **Prozedurale Logik:** JavaScript bietet als höhere Programmiersprache eine umfangreiche und vertraute Benutzeroberfläche zur Darstellung der Geschäftslogik. Sie können eine Sequenz komplexer Vorgänge für die Daten ausführen.

* **Unteilbare Transaktionen:** Azure Cosmos DB-Datenbankvorgänge, die in einer einzelnen gespeicherten Prozedur oder einem Trigger ausgeführt werden, sind unteilbar. Durch diese Unteilbarkeit kann eine Anwendung zusammengehörige Vorgänge in einem einzelnen Batch kombinieren, sodass entweder alle Vorgänge erfolgreich ausgeführt werden oder keiner von ihnen.

* **Leistung:** Die JSON-Daten sind dem Typensystem der JavaScript-Sprache zugeordnet. Diese Zuordnung ermöglicht eine Reihe von Optimierungen, beispielsweise die verzögerte Materialisierung von JSON-Dokumenten im Pufferpool und deren bedarfsgesteuerte Bereitstellung für den ausführenden Code. Weitere Leistungsvorteile betreffen die Übertragung der Geschäftslogik in die Datenbank:

   * *Batchverarbeitung:* Sie können Vorgänge wie Einfügungen gruppieren und dann gleichzeitig übermitteln. Die durch die Wartezeit des Netzwerkdatenverkehrs verursachten Kosten und der erhöhte Speicheraufwand für die Erstellung separater Transaktionen werden erheblich verringert.

   * *Vorkompilierung:* Gespeicherte Prozeduren, Trigger und benutzerdefinierte Funktionen werden implizit in das Bytecodeformat vorkompiliert, um die aufwändige Kompilierung bei jedem Skriptaufruf zu vermeiden. Durch die Vorkompilierung werden gespeicherte Prozeduren schnell und mit geringem Speicherbedarf aufgerufen.

   * *Sequenzierung:* Manchmal erfordern Vorgänge einen Auslösemechanismus, der eine oder zusätzliche Aktualisierungen an den Daten vornehmen kann. Neben der Unteilbarkeit ergeben sich durch die Ausführung auf dem Server auch Leistungsvorteile.

* **Kapselung:** Mithilfe gespeicherter Prozeduren kann die Logik an einem Ort zusammengefasst werden. Durch die Kapselung wird eine Abstraktionsebene über den Daten hinzugefügt, die es Ihnen erlaubt, Ihre Anwendungen unabhängig von den Daten zu entwickeln. Diese Abstraktionsebene ist hilfreich, wenn die Daten kein Schema aufweisen und Sie keine zusätzliche Logik direkt in der Anwendung hinzufügen müssen. Durch diese Abstraktion können Sie den Zugriff über die Scripts optimieren und dadurch Ihre Daten schützen.

> [!TIP]
> Gespeicherte Prozeduren eignen sich am besten für Vorgänge mit vielen Schreibvorgängen und erfordern eine Transaktion über einen Partitionsschlüsselwert. Bei der Überlegung, ob Sie gespeicherte Prozeduren verwenden, sollten Sie die Kapselung der maximal möglichen Anzahl von Schreibvorgängen optimieren. Gespeicherte Prozeduren sind im Allgemeinen nicht die effizienteste Methode für die Ausführung zahlreicher Lese- oder Abfragevorgänge. Eine große Anzahl von an den Client zurückzugebenden Lesevorgängen mithilfe von gespeicherten Prozeduren in einem Batch zusammenzufassen, bietet daher nicht den gewünschten Nutzen. Um eine optimale Leistung zu erzielen, sollten Vorgänge mit zahlreichen Lesevorgängen auf der Clientseite unter Verwendung des Cosmos SDK ausgeführt werden. 

## <a name="transactions"></a>Transaktionen

Eine Transaktion in einer typischen Datenbank kann als Folge von Vorgängen definiert werden, die als einzelne logische Arbeitseinheit ausgeführt wird. Jede Transaktion bietet **ACID-Eigenschaftsgarantien**. ACID ist ein bekanntes Akronym und steht für **A** tomicity, **C** onsistency, **I** solation und **D** urability (Atomarität, Konsistenz, Isolation, Dauerhaftigkeit). 

* Die Atomarität bzw. Unteilbarkeit gewährleistet, dass alle innerhalb einer Transaktion ausgeführten Vorgänge als eine einzige Einheit betrachtet werden und entweder alle Vorgänge committet werden oder keiner von ihnen. 

* Die Konsistenz stellt sicher, dass die Daten zwischen den Transaktionen immer einen gültigen Zustand aufweisen. 

* Die Isolation sorgt dafür, dass es niemals zu Konflikten zwischen zwei Transaktionen kommt. Viele kommerzielle Systeme bieten mehrere Isolationsstufen, die je nach Anforderungen der Anwendung genutzt werden können. 

* Die Dauerhaftigkeit stellt sicher, dass jede Änderung, die in einer Datenbank committet wird, immer vorhanden ist.

In Azure Cosmos DB wird die JavaScript-Runtime in der Datenbank-Engine gehostet. Daher werden Anforderungen in den gespeicherten Prozeduren und Triggern im gleichen Bereich ausgeführt wie die Datenbanksitzung. Durch dieses Feature kann Azure Cosmos DB die vier ACID-Eigenschaften für alle Vorgänge garantieren, die Teil einer gespeicherten Prozedur oder eines Triggers sind. Beispiele finden Sie im Artikel zum [Implementieren von Transaktionen](how-to-write-stored-procedures-triggers-udfs.md#transactions).

### <a name="scope-of-a-transaction"></a>Bereich einer Transaktion

Gespeicherte Prozeduren sind einem Azure Cosmos-Container zugeordnet. Die Ausführung gespeicherter Prozeduren ist auf einen logischen Partitionsschlüssel beschränkt. Gespeicherte Prozeduren müssen während der Ausführung einen logischen Partitionsschlüsselwert enthalten, der die logische Partition für den Transaktionsumfang definiert. Weitere Informationen finden Sie unter [Partitionierung in Azure Cosmos DB](partitioning-overview.md).

### <a name="commit-and-rollback"></a>Commit und Rollback

Transaktionen sind nativ in das JavaScript-Programmiermodell von Azure Cosmos DB integriert. Innerhalb einer JavaScript-Funktion werden alle Vorgänge automatisch von einer einzelnen Transaktion umschlossen. Wenn die JavaScript-Logik in einer gespeicherten Prozedur ohne Ausnahmen abgeschlossen wird, werden alle Vorgänge in der Transaktion in die Datenbank committet. Anweisungen wie `BEGIN TRANSACTION` und `COMMIT TRANSACTION` (bei relationalen Datenbanken bekannt) sind in Azure Cosmos DB implizit. Wenn das Skript Ausnahmen ausgibt, führt die JavaScript-Runtime von Azure Cosmos DB ein Rollback für die gesamte Transaktion aus. Das Auslösen einer Ausnahme entspricht daher praktisch einer `ROLLBACK TRANSACTION` in Azure Cosmos DB.

### <a name="data-consistency"></a>Datenkonsistenz

Gespeicherte Prozeduren und Trigger werden immer auf dem primären Replikat eines Azure Cosmos-Containers ausgeführt. Dadurch wird sichergestellt, dass Lesevorgänge in gespeicherten Prozeduren eine [hohe Konsistenz](./consistency-levels.md) aufweisen. Abfragen mit benutzerdefinierten Funktionen können auf dem primären oder einem sekundären Replikat ausgeführt werden. Gespeicherte Prozeduren und Trigger dienen zur Unterstützung von transaktionalen Schreibvorgängen. Schreibgeschützte Logik lässt sich jedoch am besten als anwendungsseitige Logik implementieren, und Abfragen mit den [SDKs der SQL-API von Azure Cosmos DB](sql-api-dotnet-samples.md) helfen Ihnen, den Datenbankdurchsatz vollständig zu nutzen. 

> [!TIP]
> Bei innerhalb einer gespeicherten Prozedur oder eines Triggers ausgeführten Abfragen werden möglicherweise keine Änderungen angezeigt, die von derselben Skripttransaktion an Elementen vorgenommen wurden. Dies gilt sowohl für SQL-Abfragen wie z. B. `getContent().getCollection.queryDocuments()` als auch für integrierte Sprachabfragen wie z. B. `getContext().getCollection().filter()`.

## <a name="bounded-execution"></a>Gebundene Ausführung

Alle Azure Cosmos DB-Vorgänge müssen innerhalb des angegebenen Zeitlimits abgeschlossen werden. Diese Einschränkung gilt auch für JavaScript-Funktionen, d. h. für gespeicherte Prozeduren, Trigger und benutzerdefinierte Funktionen. Wird ein Vorgang nicht innerhalb des Zeitlimits abgeschlossen, wird für die Transaktion ein Rollback ausgeführt.

Sie können entweder sicherstellen, dass Ihre JavaScript-Funktionen innerhalb des Zeitlimits abgeschlossen werden oder ein auf der Fortführung basierendes Modell implementieren, um die Ausführung in einem Batch zusammenzufassen/fortzusetzen. Um die Entwicklung gespeicherter Prozeduren und Trigger zur Behandlung von Zeitlimits zu vereinfachen, geben alle Funktionen unter dem Azure Cosmos-Container (z. B. Erstellen, Lesen, Ersetzen und Löschen von Elementen) einen booleschen Wert zurück, der angibt, ob der jeweilige Vorgang abgeschlossen wird. Lautet dieser Wert „false“, weist dies darauf hin, dass die Prozedur die Ausführung beenden muss, weil das Skript mehr Zeit oder bereitgestellten Durchsatz beansprucht als der konfigurierte Wert zulässt. Vorgänge, die vor dem ersten nicht angenommenen Speichervorgang in die Warteschlange gestellt wurden, werden garantiert abgeschlossen, wenn die gespeicherte Prozedur rechtzeitig abgeschlossen wird und keine weiteren Anforderungen in die Warteschlange stellt. Daher sollten Vorgänge mithilfe der Rückrufkonvention von JavaScript nacheinander in die Warteschlange eingereiht werden, um die Ablaufsteuerung des Skripts zu verwalten. Da Skripts in einer serverseitigen Umgebung ausgeführt werden, unterliegen sie einen strikten Kontrolle und Steuerung. Skripts, die wiederholt Ausführungsgrenzen verletzt haben, werden möglicherweise als inaktiv markiert und können nicht ausgeführt werden. Sie sollten unter Berücksichtigung der Ausführungsgrenzen neu erstellt werden.

Für JavaScript-Funktionen gilt auch die [bereitgestellte Durchsatzkapazität](request-units.md). Letztendlich können JavaScript-Funktionen unter Umständen eine große Anzahl von Anforderungseinheiten innerhalb eines kurzen Zeitraums verwenden. Wenn die bereitgestellte Durchsatzkapazität erreicht ist, werden sie möglicherweise begrenzt. Wichtig ist, dass Skripts neben dem Durchsatz für die Ausführung von Datenbankvorgängen zusätzlichen Durchsatz beanspruchen. Diese Datenbankvorgänge sind jedoch etwas weniger aufwendig als die Ausführung der gleichen Vorgänge auf dem Client.

## <a name="triggers"></a>Trigger

Azure Cosmos DB unterstützt zwei Arten von Triggern:

### <a name="pre-triggers"></a>Vorangestellte Trigger

Azure Cosmos DB stellt Trigger bereit, die durch Ausführen eines Vorgangs für ein Azure Cosmos-Element aufgerufen werden können. Sie können beispielsweise beim Erstellen eines Elements einen vorangestellter Trigger angeben. In diesem Fall wird der vorangestellte Trigger ausgeführt, bevor das Element erstellt wird. Vorangestellte Trigger können keine Eingabeparameter übernehmen. Bei Bedarf kann das Anforderungsobjekt verwendet werden, um den Dokumenttext der ursprünglichen Anforderung zu aktualisieren. Wenn Trigger registriert werden, können die Benutzer die Vorgänge angeben, mit denen sie ausgeführt werden können. Wenn ein Trigger mit `TriggerOperation.Create` erstellt wurde, bedeutet dies, dass die Verwendung des Triggers in einem Ersetzungsvorgang nicht zulässig ist. Beispiele finden Sie unter [Schreiben von Triggern](how-to-write-stored-procedures-triggers-udfs.md#triggers).

### <a name="post-triggers"></a>Nachgestellte Trigger

Ähnlich wie vorangestellte Trigger sind nachgestellte Trigger ebenfalls einem Vorgang für ein Azure Cosmos-Element zugeordnet, und sie erfordern keine Eingabeparameter. Sie werden ausgeführt, *nachdem* der Vorgang abgeschlossen wurde, und haben Zugriff auf die Antwortnachricht, die an den Client gesendet wird. Beispiele finden Sie unter [Schreiben von Triggern](how-to-write-stored-procedures-triggers-udfs.md#triggers).

> [!NOTE]
> Registrierte Trigger werden bei Auftreten der entsprechenden Vorgänge (Erstellen/Löschen/Ersetzen/Aktualisieren) nicht automatisch ausgeführt. Sie müssen beim Ausführen dieser Vorgänge explizit aufgerufen werden. Weitere Informationen finden Sie im Artikel [Ausführen von Triggern](how-to-use-stored-procedures-triggers-udfs.md#pre-triggers).

## <a name="user-defined-functions"></a><a id="udfs"></a>Benutzerdefinierte Funktionen

Mithilfe von [benutzerdefinierten Funktionen](sql-query-udfs.md) (User-Defined Functions, UDFs) kann die Syntax der SQL-API-Abfragesprache erweitert und mühelos eine benutzerdefinierte Geschäftslogik implementiert werden. Benutzerdefinierte Funktionen können nur innerhalb von Abfragen aufgerufen werden. Sie haben keinen Zugriff auf das Kontextobjekt und sind als JavaScript-Komponente vorgesehen, die ausschließlich der Berechnung dient. Daher können benutzerdefinierte Funktionen auf sekundären Replikaten ausgeführt werden. Beispiele finden Sie unter [Schreiben von benutzerdefinierten Funktionen](how-to-write-stored-procedures-triggers-udfs.md#udfs).

## <a name="javascript-language-integrated-query-api"></a><a id="jsqueryapi"></a>JavaScript-Language Integrated Query-API

Zusätzlich zu Abfragen mit der SQL-API-Abfragesyntax ermöglicht das [serverseitige SDK](https://azure.github.io/azure-cosmosdb-js-server) die Durchführung von Abfragen mithilfe einer JavaScript-Schnittstelle, die keinerlei SQL-Kenntnisse voraussetzt. Mit der JavaScript-Abfrage-API können Sie programmgesteuert Abfragen erstellen, indem Sie Prädikatfunktionen an die Funktionsaufrufsequenz übergeben. Abfragen werden von der JavaScript-Runtime analysiert und in Azure Cosmos DB effizient ausgeführt. Weitere Informationen zur Unterstützung der JavaScript-Abfrage-API finden Sie im Artikel [Working with JavaScript language integrated query API](javascript-query-api.md) (Arbeiten mit der JavaScript-Language Integrated Query-API). Beispiele finden Sie im Artikel [Schreiben von gespeicherten Prozeduren und Triggern mit der JavaScript-Abfrage-API](how-to-write-javascript-query-api.md).

## <a name="next-steps"></a>Nächste Schritte

Lesen Sie die folgenden Artikel, um sich mit dem Schreiben und Verwenden von gespeicherten Prozeduren, Triggern und benutzerdefinierten Funktionen in Azure Cosmos DB vertraut zu machen:

* [Schreiben von gespeicherten Prozeduren, Triggern und benutzerdefinierten Funktionen](how-to-write-stored-procedures-triggers-udfs.md)

* [Verwenden von gespeicherten Prozeduren, Triggern und benutzerdefinierten Funktionen](how-to-use-stored-procedures-triggers-udfs.md)

* [Working with JavaScript language integrated query API](javascript-query-api.md) (Arbeiten mit der JavaScript-Language Integrated Query-API)