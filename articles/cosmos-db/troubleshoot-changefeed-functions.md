---
title: Behandeln von Problemen bei Verwendung des Azure Functions-Triggers für Cosmos DB
description: Häufig auftretende Probleme, Problemumgehungen und Diagnoseschritte bei Verwendung des Azure Functions-Triggers für Cosmos DB
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 03/13/2020
ms.author: maquaran
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 9fc5da214a50cb000d2154d08bb9b6f6f98ac5ec
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93340529"
---
# <a name="diagnose-and-troubleshoot-issues-when-using-azure-functions-trigger-for-cosmos-db"></a>Diagnostizieren und Behandeln von Problemen bei Verwendung des Azure Functions-Triggers für Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

In diesem Artikel werden häufig auftretende Probleme, Problemumgehungen und Diagnoseschritte bei Verwendung des [Azure Functions-Triggers für Cosmos DB](change-feed-functions.md) erläutert.

## <a name="dependencies"></a>Abhängigkeiten

Der Azure Functions-Trigger und die Bindungen für Cosmos DB hängen von den Erweiterungspaketen für die zugrunde liegende Azure Functions-Laufzeit ab. Halten Sie diese Pakete stets auf dem aktuellen Stand, da sie Fixes und neue Funktionen enthalten können, mit denen u.U. potenzielle Probleme behoben werden können:

* Für Azure Functions V2 siehe [Microsoft.Azure.WebJobs.Extensions.CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB).
* Für Azure Functions V1 siehe [Microsoft.Azure.WebJobs.Extensions.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DocumentDB).

Dieser Artikel bezieht bei jeder Erwähnung der Laufzeit auf Azure Functions V2, es sei denn, es wird ausdrücklich etwas anderes angegeben.

## <a name="consume-the-azure-cosmos-db-sdk-independently"></a>Nutzen des Azure Cosmos DB SDK (unabhängig)

Die wichtigste Funktion des Erweiterungspakets besteht in der Unterstützung des Azure Functions-Triggers und der Bindungen für Cosmos DB. Außerdem enthält es das [Azure Cosmos DB .NET-SDK](sql-api-sdk-dotnet-core.md). Dieses ist hilfreich bei der programmgesteuerten Interaktion mit Azure Cosmos DB ohne Verwendung des Triggers und der Bindungen.

Wenn Sie das Azure Cosmos DB-SDK verwenden möchten, dürfen Sie das Projekt keinem anderen NuGet-Paketverweis hinzufügen. Sorgen Sie stattdessen für eine **Auflösung des SDK-Verweises über das Erweiterungspaket von Azure Functions**. Nutzen des Azure Cosmos DB SDK (separat vom Trigger und den Bindungen)

Wenn Sie manuell eine eigene Instanz des [Azure Cosmos DB-SDK-Clients](./sql-api-sdk-dotnet-core.md) erstellen, müssen Sie zudem dem Muster folgen, dass nur eine Instanz des Clients vorhanden ist; [nutzen Sie hierfür den Ansatz mit Singleton-Mustern](../azure-functions/manage-connections.md#documentclient-code-example-c). Durch diesen Prozess werden potenzielle Socket-Probleme in Ihren Vorgängen vermieden.

## <a name="common-scenarios-and-workarounds"></a>Gängige Szenarien und Problemumgehungen

### <a name="azure-function-fails-with-error-message-collection-doesnt-exist"></a>Fehlermeldung für die Azure-Funktion mit dem Hinweis, dass eine Sammlung nicht vorhanden ist

Die Azure-Funktion schlägt mit folgender Fehlermeldung fehl: „Either the source collection 'collection-name' (in database 'database-name') or the lease collection 'collection2-name' (in database 'database2-name') does not exist. Both collections must exist before the listener starts. To automatically create the lease collection, set 'CreateLeaseCollectionIfNotExists' to 'true'“ (Die Quellsammlung 'Name der Sammlung' (in Datenbank 'Datenbankname') oder die Lease-Sammlung 'Name der Sammlung2' (in Datenbank 'Datenbankname2') ist nicht vorhanden. Beide Sammlungen müssen vorhanden sein, damit der Listener gestartet werden kann. Legen Sie zum automatischen Erstellen der Lease-Sammlung 'CreateLeaseCollectionIfNotExists' auf 'true' fest).

Das heißt, dass einer oder beide der für die ordnungsgemäße Funktion des Triggers erforderlichen Azure Cosmos-Container nicht vorhanden oder für die Azure-Funktion nicht erreichbar ist. **In der Fehlermeldung wird Ihnen mitgeteilt, nach welcher Azure Cosmos-Datenbank und welchen Containern der Trigger sucht** , entsprechend Ihrer jeweiligen Konfiguration.

1. Überprüfen Sie das `ConnectionStringSetting`-Attribut, und stellen Sie sicher, dass es **auf eine Einstellung verweist, die in Ihrer Azure-Funktions-App vorhanden ist**. Der Wert dieses Attributs darf nicht die Verbindungszeichenfolge selbst sein, sondern muss dem Namen der Konfigurationseinstellung entsprechen.
2. Überprüfen Sie, ob `databaseName` und `collectionName` in Ihrem Azure Cosmos-Konto vorhanden sind. Wenn Sie die automatische Ersetzung von Werten (mit `%settingName%`-Mustern) nutzen, vergewissern Sie sich, dass der Name der Einstellung in der Azure-Funktions-App vorhanden ist.
3. Wenn Sie keinen `LeaseCollectionName/leaseCollectionName` angeben, ist der Standardwert „leases“. Vergewissern Sie sich, dass dieser Container vorhanden ist. Optional können Sie das `CreateLeaseCollectionIfNotExists`-Attribut im Trigger auf `true` festlegen, sodass er automatisch erstellt wird.
4. Überprüfen Sie die [Firewall-Konfiguration Ihres Azure Cosmos-Kontos](how-to-configure-firewall.md), und stellen Sie sicher, dass sie nicht die Azure-Funktion blockiert.

### <a name="azure-function-fails-to-start-with-shared-throughput-collection-should-have-a-partition-key"></a>Die Azure Funktion kann nicht gestartet werden, und der folgende Fehler wird ausgegeben: „Shared throughput collection should have a partition key“ (Gemeinsam verwendete Durchsatz-Sammlung muss einen Partitionsschlüssel aufweisen)

Die früheren Versionen der Azure Cosmos DB-Erweiterung unterstützten nicht die Verwendung eines Leases-Containers, der in einer [gemeinsam genutzten Durchsatz-Datenbank](./set-throughput.md#set-throughput-on-a-database) erstellt wurde. Beheben Sie dieses Problem, indem Sie die Erweiterung [Microsoft.Azure.WebJobs.Extensions.CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB) auf die neueste Version aktualisieren.

### <a name="azure-function-fails-to-start-with-partitionkey-must-be-supplied-for-this-operation"></a>Die Azure-Funktion wird mit der Fehlermeldung „PartitionKey must be supplied for this operation“ (PartitionKey muss für diesen Vorgang bereitgestellt werden) nicht gestartet.

Dieser Fehler bedeutet, dass Sie derzeit eine partitionierte Leasesammlung mit einer alten [Erweiterungsabhängigkeit](#dependencies) nutzen. Führen Sie ein Upgrade auf die neueste verfügbare Version durch. Wenn Sie derzeit mit Azure Functions V1 arbeiten, müssen Sie ein Upgrade auf Azure Functions V2 durchführen.

### <a name="azure-function-fails-to-start-with-the-lease-collection-if-partitioned-must-have-partition-key-equal-to-id"></a>Die Azure Funktion kann nicht gestartet werden, und der folgende Fehler wird ausgegeben: „The lease collection, if partitioned, must have partition key equal to id“ (Die Lease-Sammlung, falls partitioniert, muss einen Partitionsschlüssel aufweisen, der gleich id ist).

Dieser Fehler weist darauf hin, dass Ihr aktueller Leases-Container partitioniert ist, der Partitionsschlüsselpfad jedoch nicht `/id` ist. Zum Beheben dieses Problems müssen Sie den Leases-Container mit `/id` als Partitionsschlüssel neu erstellen.

### <a name="you-see-a-value-cannot-be-null-parameter-name-o-in-your-azure-functions-logs-when-you-try-to-run-the-trigger"></a>Die folgende Fehlermeldung wird angezeigt: „Value cannot be null. Parameter name: o" in your Azure Functions logs when you try to Run the Trigger (Wert darf nicht null sein. Beim Ausführen des Triggers wird Parametername: o" in Azure Functions protokolliert)

Dieses Problem tritt auf, wenn Sie im Azure-Portal beim Untersuchen einer Azure Function, die den Trigger verwendet, die Schaltfläche **Ausführen** auswählen. Aufgrund des Triggers müssen Sie zum Starten nicht „Ausführen“ auswählen, der Start erfolgt automatisch, wenn die Azure-Funktion bereitgestellt wird. Wenn Sie den Protokollstream der Azure-Funktion im Azure-Portal überprüfen möchten, wechseln Sie einfach zu Ihrem überwachten Container, und fügen Sie einige neue Elemente hinzu. Sie stellen fest, dass der Trigger automatisch ausgeführt wird.

### <a name="my-changes-take-too-long-to-be-received"></a>Der Empfang meiner Änderungen dauert zu lange

Dieses Szenario kann mehrere Ursachen haben, und alle von ihnen sollten überprüft werden:

1. Ist Ihre Azure-Funktion in derselben Region wie Ihr Azure Cosmos-Konto bereitgestellt? Für optimale Netzwerklatenz müssen sich die Azure-Funktion und Ihr Azure Cosmos-Konto in derselben Azure-Region befinden.
2. Treten die Änderungen in Ihrem Azure Cosmos-Container laufend oder sporadisch auf?
Wenn Letzteres der Fall ist, kann zwischen dem Speichern der Änderungen und dem Übernehmen durch die Azure-Funktion eine gewisse Verzögerung auftreten. Dies hat folgende Ursache: Wenn der Trigger intern eine Überprüfung auf Änderungen in Ihrem Azure Cosmos-Container durchführt und keine für Lesevorgänge anstehenden Änderungen findet, wechselt er für einen konfigurierbaren Zeitraum (standardmäßig 5 Sekunden) in den Ruhezustand, ehe er auf neue Änderungen prüft (um einen hohen RU-Verbrauch zu vermeiden). Sie können diesen Ruhemodus-Zeitraum über die `FeedPollDelay/feedPollDelay`-Einstellung in der [Konfiguration](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration) des Triggers konfigurieren (der Wert muss in Millisekunden angegeben werden).
3. Für Ihren Azure Cosmos-Container gilt möglicherweise eine [Ratenbegrenzung](./request-units.md).
4. Sie können mit dem `PreferredLocations`-Attribut im Trigger eine durch Trennzeichen getrennte Liste von Azure-Regionen angeben, um eine Verbindungsreihenfolge mit benutzerdefinierten Präferenzen festzulegen.

### <a name="some-changes-are-repeated-in-my-trigger"></a>Einige Änderungen werden in meinem Trigger wiederholt

Das Konzept einer „Änderung“ ist ein Vorgang in einem Dokument. Die gängigsten Szenarien, in denen Ereignisse für dasselbe Dokument empfangen werden, sind Folgende:
* Das Konto verwendet letztliche Konsistenz. Bei Nutzung des Änderungsfeeds auf einer Ebene des Typs „Letztliche Konsistenz“ können in nachfolgenden Lesevorgängen im Änderungsfeed duplizierte Ereignisse auftreten (das letzte Ereignis eines Lesevorgangs wird als erstes des nächsten angezeigt).
* Das Dokument wird aktualisiert. Der Änderungsfeed kann mehrere Vorgänge für dieselben Dokumente enthalten. Wenn ein Dokument Aktualisierungen empfängt, kann es mehrere Ereignisse aufnehmen (eines für jede Aktualisierung). Eine einfache Möglichkeit, zwischen verschiedenen Vorgängen für ein und dasselbe Dokument zu unterscheiden, ist die Nachverfolgung der `_lsn`-[Eigenschaft für jede Änderung](change-feed.md#change-feed-and-_etag-_lsn-or-_ts). Wenn sie nicht übereinstimmen, handelt es sich um verschiedene Änderungen am selben Dokument.
* Wenn Sie Dokumente nur anhand der `id` identifizieren, denken Sie daran, dass der eindeutige Bezeichner für ein Dokument die `id` und sein Partitionsschlüssel sind (es kann zwei Dokumente mit der gleichen `id`, aber unterschiedlichem Partitionsschlüssel geben).

### <a name="some-changes-are-missing-in-my-trigger"></a>Einige Änderungen sind in meinem Trigger nicht vorhanden

Wenn Sie feststellen, dass einige der im Azure Cosmos-Container aufgetretenen Änderungen von der Azure-Funktion nicht übernommen werden, sollten Sie zuerst den folgenden Untersuchungsschritt ausführen.

Wenn Ihre Azure-Funktion die Änderungen empfängt, verarbeitet sie diese häufig und könnte das Ergebnis (optional) an ein anderes Ziel senden. Wenn Sie fehlende Änderungen untersuchen, stellen Sie sicher, dass Sie **messen, welche Änderungen am Erfassungspunkt** (beim Start der Azure-Funktion) und nicht am Ziel empfangen werden.

Wenn einige Änderungen am Ziel nicht vorhanden sind, kann dies bedeuten, dass während der Ausführung der Azure-Funktion nach Empfang der Änderungen ein Fehler aufgetreten ist.

Gehen Sie in diesem Szenario am besten wie folgt vor: Fügen Sie `try/catch`-Blöcke im Code und innerhalb der Schleifen hinzu, die möglicherweise die Änderungen verarbeiten, um etwaige Fehler für eine bestimmte Teilmenge von Elementen zu erkennen, und behandeln Sie diese entsprechend (senden Sie sie zur weiteren Analyse oder eine erneute Ausführung an einen anderen Speicher). 

> [!NOTE]
> Der Azure Functions-Trigger für Cosmos DB unternimmt standardmäßig keinen erneuten Versuch für einen Batch von Änderungen, wenn während der Codeausführung ein Ausnahmefehler aufgetreten ist. Das heißt, dass die Änderungen nicht am Ziel eintreffen, weil sie nicht verarbeitet werden.

Wenn Sie feststellen, dass einige Änderungen vom Trigger überhaupt nicht empfangen wurden, liegt dies am häufigsten daran, dass **eine andere Azure-Funktion ausgeführt wird**. Dies kann eine andere in Azure bereitgestellte Azure-Funktion sein oder eine Azure-Funktion, die lokal auf dem Computer des Entwicklers ausgeführt wird und **genau dieselbe Konfiguration aufweist** (die gleichen überwachten und Lease-Container) – und diese Azure-Funktion „stiehlt“ eine Teilmenge der Änderungen, die von Ihrer Azure-Funktion verarbeitet werden sollten.

Sie können ein solches Szenario auch überprüfen, wenn Ihnen bekannt ist, wie viele Instanzen der Azure-Funktions-App ausgeführt werden. Wenn Sie Ihren Leases-Container untersuchen und die Anzahl der darin enthaltenen Lease-Elemente zählen, müssen die eindeutigen Werte der enthaltenen `Owner`-Eigenschaft gleich der Anzahl der Instanzen Ihrer Funktions-App sein. Wenn andere Besitzer als die bekannten Azure-Funktions-App-Instanzen vorhanden sind, heißt das, dass diese zusätzlichen Besitzer die Änderungen „stehlen“.

Eine einfache Möglichkeit, dies zu vermeiden, besteht darin, ein `LeaseCollectionPrefix/leaseCollectionPrefix` mit einem neuen/anderen Wert auf die Funktion anzuwenden oder alternativ eine Überprüfung mit einem neuen Container für Leases durchzuführen.

### <a name="need-to-restart-and-reprocess-all-the-items-in-my-container-from-the-beginning"></a>Ich muss alle Elemente in meinem Container von Anfang an neu starten und erneut verarbeiten 
So verarbeiten Sie alle Elemente in einem Container von Anfang an erneut
1. Beenden Sie Ihre Azure-Funktion, wenn sie gerade ausgeführt wird. 
1. Löschen Sie die Dokumente in der Lease-Sammlung (oder löschen Sie die Lease-Sammlung, und erstellen Sie sie erneut, damit sie leer ist).
1. Legen Sie das CosmosDBTrigger-Attribut [StartFromBeginning](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration) in der Funktion auf „true“ fest. 
1. Starten Sie die Azure-Funktion neu. Jetzt werden alle Änderungen von Anfang an gelesen und verarbeitet. 

Durch Festlegen von [StartFromBeginning](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration) auf „true“ wird die Azure-Funktion angewiesen, damit zu beginnen, Änderungen ab dem Anfang des Verlaufs der Sammlung statt ab der aktuellen Uhrzeit zu lesen. Dies funktioniert nur, wenn es keine bereits erstellten Leases (d. h. Dokumente in der Sammlung der Leases) gibt. Wird diese Eigenschaft auf „true“ festgelegt, wenn es bereits Leases gibt, hat dies keine Auswirkungen. Wenn in diesem Szenario eine Funktion beendet und neu gestartet wird, beginnt sie mit dem Lesen ab dem letzten Prüfpunkt, wie es in der Leases-Sammlung definiert wurde. Führen Sie die obigen Schritte 1-4 aus, um die erneute Verarbeitung von Anfang an auszuführen.  

### <a name="binding-can-only-be-done-with-ireadonlylistdocument-or-jarray"></a>Bindung kann nur mit IReadOnlyList\<Document> oder JArray erfolgen

Dieser Fehler tritt auf, wenn Ihr Azure Functions-Projekt (oder ein beliebiges Projekt, auf das verwiesen wird) einen manuelle NuGet-Verweis auf das Azure Cosmos DB-SDK mit einer anderen Version als die durch die [Azure Functions Cosmos DB-Erweiterung](./troubleshoot-changefeed-functions.md#dependencies) bereitgestellte Version enthält.

Zum Umgehen dieses Problems entfernen Sie den hinzugefügten manuellen NuGet-Verweis, und lassen Sie den Azure Cosmos DB SDK-Verweis über das Azure Functions Cosmos DB-Erweiterungspaket auflösen.

### <a name="changing-azure-functions-polling-interval-for-the-detecting-changes"></a>Ändern des Abrufintervalls der Azure-Funktion für die erkannten Änderungen

Wie bereits zuvor für [Der Empfang meiner Änderungen dauert zu lange](./troubleshoot-changefeed-functions.md#my-changes-take-too-long-to-be-received) erläutert, befindet sich die Azure-Funktion für einen konfigurierbaren Zeitraum im Ruhezustand (in der Standardeinstellung für 5 Sekunden), bevor auf neue Änderungen überprüft wird (um einen hohen RU-Verbrauch zu verhindern). Sie können diesen Ruhemodus-Zeitraum über die `FeedPollDelay/feedPollDelay`-Einstellung in der [Konfiguration](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration) des Triggers konfigurieren (der Wert muss in Millisekunden angegeben werden).

## <a name="next-steps"></a>Nächste Schritte

* [Aktivieren der Überwachung für Azure Functions](../azure-functions/functions-monitoring.md)
* [Problembehandlung für Azure Cosmos DB .NET-SDK](./troubleshoot-dot-net-sdk.md)
