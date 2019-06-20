---
title: Häufig gestellte Fragen zu unterschiedlichen APIs in Azure Cosmos DB
description: Enthält Antworten auf häufig gestellte Fragen zu Azure Cosmos DB, einem weltweit verteilten Datenbankdienst, der Unterstützung für mehrere Datenbankmodelle bietet. Informieren Sie sich über Kapazität, Leistungsebenen und Skalierung.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 4935e06389266f049b8f7f79ca6fb9380f33c864
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65954154"
---
# <a name="frequently-asked-questions-about-different-apis-in-azure-cosmos-db"></a>Häufig gestellte Fragen zu unterschiedlichen APIs in Azure Cosmos DB

### <a name="what-are-the-typical-use-cases-for-azure-cosmos-db"></a>Was sind die typischen Anwendungsfälle für Azure Cosmos DB?

Azure Cosmos DB ist eine gute Wahl für neue Web-, Mobil-, Gaming- und IoT-Anwendungen, bei denen Dinge wie automatische Skalierung, vorhersagbare Leistung, kurze Reaktionszeiten im Millisekundenbereich und die Möglichkeit zum Abfragen von schemafreien Daten wichtig sind. Azure Cosmos DB eignet sich für schnelle Entwicklungen und die Unterstützung kontinuierlicher Iterationen von Anwendungsdatenmodellen. Anwendungen, mit denen von Benutzern erzeugte Inhalte und Daten verwaltet werden, sind ein [häufiger Anwendungsfall von Azure Cosmos DB](use-cases.md).

### <a name="how-does-azure-cosmos-db-offer-predictable-performance"></a>Wie wird bei Azure Cosmos DB für eine vorhersagbare Leistung gesorgt?

In Azure Cosmos DB wird der Durchsatz in [Anforderungseinheiten](request-units.md) (Request Units, RUs) gemessen. Ein Durchsatz von einer Anforderungseinheit entspricht dem Durchsatz der GET-Anforderung für ein Dokument mit einer Größe von 1 KB. Jeder Vorgang in Azure Cosmos DB (einschließlich der Ausführung von Lese- und Schreibvorgängen, SQL-Abfragen sowie gespeicherten Prozeduren) verfügt über einen deterministischen Wert für die Anforderungseinheiten, der auf dem erforderlichen Durchsatz zum Abschließen des Vorgangs basiert. Im Hinblick auf den Durchsatz Ihrer Anwendung müssen Sie sich also keine Gedanken um das Zusammenspiel von CPU, E/A-Leistung und Arbeitsspeicher machen, sondern können mit einer einzigen Kennzahl arbeiten: der Anforderungseinheit.

Sie können jeden Azure Cosmos-Container mit bereitgestelltem Durchsatz (Anforderungseinheiten des Durchsatzes pro Sekunde) konfigurieren. Für Anwendungen jeder Größe können Sie Vergleichstests einzelner Anforderungen durchführen, um deren Werte für die Anforderungseinheiten zu messen. Außerdem können Sie einen Container bereitstellen, um die Gesamtsumme der Anforderungseinheiten über alle Anforderungen hinweg zu verarbeiten. Sie können den Durchsatz des Containers auch zentral hoch- oder herunterskalieren, wenn sich die Anforderungen Ihrer Anwendung ändern. Um mehr über Anforderungseinheiten zu erfahren und Hilfe zum Ermitteln Ihrer Containeranforderungen zu erhalten, testen Sie den [Durchsatzrechner](https://www.documentdb.com/capacityplanner).

### <a name="how-does-azure-cosmos-db-support-various-data-models-such-as-keyvalue-columnar-document-and-graph"></a>Wie unterstützt Azure Cosmos DB verschiedene Datenmodelle wie Schlüssel/Wert-, einspaltige, Dokument- und Diagrammdaten?

Aufgrund der ARS-Konzeption (Atoms, Records and Sequences; Atoms, Datensätze und Sequenzen), auf der Azure Cosmos DB beruht, werden Schlüssel/Wert(Tabellen)-, einspaltige, Dokument- und Diagrammdaten nativ unterstützt. Atoms, Datensätze und Sequenzen können problemlos verschiedenen Datenmodellen zugeordnet und auf diese projiziert werden. Die APIs für eine Teilmenge von Modellen sind derzeit verfügbar (SQL, MongoDB, Tabelle und Gremlin), während andere APIs speziell für zusätzliche Datenmodelle in Zukunft bereitgestellt werden.

Azure Cosmos DB verfügt über eine schemaagnostische Indizierungs-Engine, die alle erfassten Daten automatisch ohne Schema oder sekundäre Indizes seitens des Entwicklers indizieren kann. Die Engine basiert auf einem Satz von logischen Indexlayouts (invertiert, einspaltig, Struktur), die die Speicheranordnung von den Index- und Abfrageverarbeitungssubsystemen entkoppeln. Cosmos DB bietet auch die Möglichkeit, eine Reihe von Übertragungsprotokollen und APIs in einer erweiterbaren Weise zu unterstützen und effizient im Kerndatenmodell (1) sowie in den logischen Indexlayouts (2) zu übersetzen. So bietet es eine einzigartige native Unterstützung für mehrere Datenmodelle.

### <a name="is-azure-cosmos-db-hipaa-compliant"></a>Ist Azure Cosmos DB HIPAA-kompatibel?

Ja, Azure Cosmos DB ist HIPAA-kompatibel. HIPAA gibt Anforderungen für die Verwendung, Offenlegung und den Schutz von individuell identifizierbaren Gesundheitsinformationen vor. Weitere Informationen finden Sie im [Microsoft Trust Center](https://www.microsoft.com/en-us/TrustCenter/Compliance/HIPAA).

### <a name="what-are-the-storage-limits-of-azure-cosmos-db"></a>Wie lauten die Speicherbeschränkungen von Azure Cosmos DB?

Es gibt keine Beschränkung in Bezug auf die Gesamtmenge der Daten, die von einem Container in Azure Cosmos DB gespeichert werden können.

### <a name="what-are-the-throughput-limits-of-azure-cosmos-db"></a>Wo liegen die Durchsatzgrenzwerte von Azure Cosmos DB?

Es gibt keine Beschränkung in Bezug auf den Durchsatz, der von einem Container in Azure Cosmos DB unterstützt werden kann. Der wichtigste Punkt hierbei ist, dass Ihre Workload zu ungefähr gleichen Teilen auf eine ausreichend große Anzahl von Partitionsschlüsseln verteilt wird.

### <a name="are-direct-and-gateway-connectivity-modes-encrypted"></a>Sind die Konnektivitätsmodi „Direkt“ und „Gateway“ verschlüsselt?

Ja, beide Modi sind immer vollständig verschlüsselt.

### <a name="how-much-does-azure-cosmos-db-cost"></a>Wie viel kostet Azure Cosmos DB?

Ausführliche Informationen hierzu finden Sie auf der Seite [Azure Cosmos DB – Preise](https://azure.microsoft.com/pricing/details/cosmos-db/). Die Nutzungsgebühren für Azure Cosmos DB werden von der Anzahl von bereitgestellten Containern, der Anzahl der Stunden, die die Container online waren, und dem bereitgestellten Durchsatz für jeden Container bestimmt.

### <a name="is-a-free-account-available"></a>Ist ein kostenloses Konto verfügbar?

Ja. Sie können sich für ein zeitlich begrenztes Konto registrieren – kostenlos und ohne Verpflichtung. Informationen zum Registrieren finden Sie unter [Azure Cosmos DB kostenlos testen](https://azure.microsoft.com/try/cosmosdb/), oder informieren Sie sich unter [Azure Cosmos DB testen – häufig gestellte Fragen](#try-cosmos-db).

Wenn Azure für Sie neu ist, können Sie sich für ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) registrieren. Sie erhalten eine Gutschrift für 30 Tage, in denen Sie alle Azure-Dienste ausprobieren können. Wenn Sie ein Visual Studio-Abonnement besitzen, haben Sie ebenfalls Anspruch auf eine [kostenlose Azure-Gutschrift](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), die Sie für beliebige Azure-Dienste nutzen können.

Sie können auch den [Azure Cosmos DB-Emulator](local-emulator.md) zum kostenlosen lokalen Entwickeln und Testen Ihrer Anwendung verwenden, ohne ein Azure-Abonnement zu erstellen. Wenn Sie mit der Funktion der Anwendung im Azure Cosmos DB-Emulator zufrieden sind, können Sie zur Verwendung eines Azure Cosmos DB-Kontos in der Cloud wechseln.

### <a name="how-can-i-get-additional-help-with-azure-cosmos-db"></a>Wie kann ich zusätzliche Hilfe zu Azure Cosmos DB erhalten?

Eine technische Frage können Sie in einem dieser beiden Frage- und Antwort-Foren stellen:

* [MSDN-Forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurecosmosdb)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-cosmosdb). Stack Overflow eignet sich am besten für Fragen zur Programmierung. Vergewissern Sie sich, dass Ihre Frage [themenbezogen](https://stackoverflow.com/help/on-topic) ist und [so viele Details wie möglich enthält, damit sie klar und beantwortbar ist](https://stackoverflow.com/help/how-to-ask).

Wenn Sie neue Funktionen wünschen, erstellen Sie auf [Uservoice](https://feedback.azure.com/forums/263030-azure-cosmos-db) eine neue Anforderung.

Um ein Problem mit Ihrem Konto zu beheben, richten Sie im Azure-Portal eine [Anfrage an den Support](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

Andere Fragen können dem Team unter [askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com) gestellt werden. Dies ist allerdings kein Alias für technischen Support.

## <a id="try-cosmos-db"></a>Testabonnements für Azure Cosmos DB

Nutzen Sie für eine begrenzte Zeit die Vorteile von Azure Cosmos DB auch ohne Abonnement – kostenlos und ohne jegliche Verpflichtung. Sie können sich unter [Azure Cosmos DB kostenlos testen](https://azure.microsoft.com/try/cosmosdb/) für ein Testabonnement für Azure Cosmos DB registrieren. Dieses Abonnement ist unabhängig von der [kostenlosen Azure-Testversion](https://azure.microsoft.com/free/) und kann zusätzlich zu einer kostenlosen Azure-Testversion oder einem kostenpflichtigen Azure-Abonnement verwendet werden.

Testabonnements für Azure Cosmos DB werden im Azure-Portal neben den anderen Abonnements zu Ihrer Benutzer-ID angezeigt.

Die folgenden Bedingungen gelten für Azure Cosmos DB-Testabonnements:

* Ein [Container mit bereitgestelltem Durchsatz](./set-throughput.md#set-throughput-on-a-container) pro Abonnement für SQL-, Gremlin-API- und Tabellenkonten.
* Bis zu drei [Sammlungen mit bereitgestelltem Durchsatz](./set-throughput.md#set-throughput-on-a-container) pro Abonnement für MongoDB-Konten.
* Eine [Datenbank mit bereitgestelltem Durchsatz](./set-throughput.md#set-throughput-on-a-database) pro Abonnement. Datenbanken mit bereitgestelltem Durchsatz können eine beliebige Anzahl von Containern enthalten.
* 10 GB Speicherkapazität
* Die globale Replikation ist in den folgenden [Azure-Regionen](https://azure.microsoft.com/regions/) verfügbar: „USA, Mitte“, „Europa, Norden“ und „Asien, Südosten“
* Maximaler Durchsatz von 5.000 RU/s bei Bereitstellung auf Containerebene.
* Maximaler Durchsatz von 20.000 RU/s bei Bereitstellung auf Datenbankebene.
* Abonnements laufen nach 30 Tagen ab und können auf maximal 31 Tage verlängert werden.
* Azure-Supporttickets können nicht für Azure Cosmos DB-Testkonten erstellt werden. Allerdings erhalten Abonnenten mit vorhandenen Supportplänen Support.

## <a name="set-up-azure-cosmos-db"></a>Einrichten von Azure Cosmos DB

### <a name="how-do-i-sign-up-for-azure-cosmos-db"></a>Wie führe ich die Registrierung für Azure Cosmos DB durch?

Azure Cosmos DB steht im Azure-Portal zur Verfügung. Registrieren Sie sich zuerst für ein Azure-Abonnement. Nachdem Sie sich registriert haben, können Sie Ihrem Azure-Abonnement ein Azure Cosmos DB-Konto hinzufügen.

### <a name="what-is-a-master-key"></a>Was ist ein Hauptschlüssel?

Ein Hauptschlüssel ist ein Sicherheitstoken für den Zugriff auf alle Ressourcen eines Kontos. Personen, die über diesen Schlüssel verfügen, haben Lese- und Schreibzugriff auf alle Ressourcen im Datenbankkonto. Gehen Sie beim Verteilen von Hauptschlüsseln mit Bedacht vor. Der primäre und sekundäre Hauptschlüssel stehen auf dem Blatt **Schlüssel** des [Azure-Portals][azure-portal] zur Verfügung. Weitere Informationen zu Schlüsseln finden Sie unter [Anzeigen, Kopieren und erneutes Generieren von Zugriffsschlüsseln](manage-with-cli.md#list-account-keys).

### <a name="what-are-the-regions-that-preferredlocations-can-be-set-to"></a>Auf welche Regionen kann PreferredLocations festgelegt werden?

Der Wert für PreferredLocations kann auf alle Azure-Regionen festgelegt werden, in denen Cosmos DB verfügbar ist. Eine Liste mit den verfügbaren Regionen finden Sie unter [Azure-Regionen](https://azure.microsoft.com/regions/).

### <a name="is-there-anything-i-should-be-aware-of-when-distributing-data-across-the-world-via-the-azure-datacenters"></a>Gibt es Punkte, die ich beim weltweiten Verteilen von Daten mithilfe der Azure-Datencenter beachten muss?

Azure Cosmos DB ist in allen Azure-Regionen präsent. Dies ist auf der Seite mit den [Azure-Regionen](https://azure.microsoft.com/regions/) angegeben. Da es sich um den Kerndienst handelt, verfügt jedes neue Datencenter über eine Azure Cosmos DB-Präsenz.

Beachten Sie beim Festlegen einer Region, dass von Azure Cosmos DB unabhängige Clouds und Government Clouds respektiert werden. Wenn Sie ein Konto in einer [unabhängigen Region](https://azure.microsoft.com/global-infrastructure/) erstellen, können Sie also keine Replikation aus dieser [unabhängigen Region](https://azure.microsoft.com/global-infrastructure/) heraus durchführen. Ebenso ist es nicht möglich, die Replikation an anderen unabhängigen Standorten über ein externes Konto zu ermöglichen.

### <a name="is-it-possible-to-switch-from-container-level-throughput-provisioning-to-database-level-throughput-provisioning-or-vice-versa"></a>Kann von einer Durchsatzbereitstellung auf Containerebene zu einer Durchsatzbereitstellung auf Datenbankebene (oder umgekehrt) gewechselt werden?

Die Bereitstellung auf Container- und Datenbankebene wird gesondert angeboten. Für einen Wechsel zwischen diesen Bereitstellungsmethoden müssen Daten von der Quelle zum Ziel migriert werden. Dies bedeutet, dass Sie eine neue Datenbank oder eine neue Sammlung erstellen müssen und anschließend Daten mithilfe der [Bulk-Executor-Bibliothek](bulk-executor-overview.md) oder mithilfe von [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) migrieren müssen.

### <a name="does-azure-cosmosdb-support-time-series-analysis"></a>Unterstützt Azure CosmosDB die Zeitreihenanalyse?

Ja, Azure CosmosDB unterstützt die Zeitreihenanalyse, hier ist ein Beispiel für das [Zeitreihenmuster](https://github.com/Azure/azure-cosmosdb-dotnet/tree/master/samples/Patterns). Dieses Beispiel zeigt, wie der Änderungsfeed zum Erstellen aggregierter Ansichten von Zeitreihendaten verwendet wird. Sie können diesen Ansatz durch die Verwendung von Spark Streaming oder einer anderen Streamdaten-Verarbeitungskomponente erweitern.

## <a name="sql-api"></a>SQL-API

### <a name="how-do-i-start-developing-against-the-sql-api"></a>Wie beginne ich mit dem Entwickeln für die SQL-API?

Zuerst müssen Sie sich für ein Azure-Abonnement registrieren. Nachdem Sie sich für ein Azure-Abonnement registriert haben, können Sie Ihrem Azure-Abonnement einen SQL-API-Container hinzufügen. Anweisungen zum Hinzufügen eines Azure Cosmos DB-Kontos finden Sie unter [Erstellen eines Azure Cosmos DB-Datenbankkontos](create-sql-api-dotnet.md#create-account).

[SDKs](sql-api-sdk-dotnet.md) sind für .NET, Python, Node.js, JavaScript und Java verfügbar. Entwickler können außerdem die [RESTful HTTP-APIs](/rest/api/cosmos-db/) zur Interaktion mit Azure Cosmos DB-Ressourcen auf unterschiedlichen Plattformen und mit verschiedenen Sprachen nutzen.

### <a name="can-i-access-some-ready-made-samples-to-get-a-head-start"></a>Kann ich auf einige fertige Beispiele als Starthilfe zugreifen?

Beispiele für die [.NET](sql-api-dotnet-samples.md)-, [Java](https://github.com/Azure/azure-documentdb-java)-, [Node.js](sql-api-nodejs-samples.md)- und [Python](sql-api-python-samples.md)-SDKs für die SQL-API sind auf GitHub verfügbar.

### <a name="does-the-sql-api-database-support-schema-free-data"></a>Unterstützt die SQL-API-Datenbank schemafreie Daten?

Ja. Die SQL-API ermöglicht Anwendungen das Speichern beliebiger JSON-Dokumente ohne Schemadefinitionen oder -hinweise. Die Daten stehen unmittelbar zur Abfrage über die Azure Cosmos DB-SQL-Abfrageschnittstelle zur Verfügung.

### <a name="does-the-sql-api-support-acid-transactions"></a>Unterstützt die SQL-API ACID-Transaktionen?

Ja, die SQL-API unterstützt dokumentübergreifende Transaktionen, die in Form von gespeicherten JavaScript-Prozeduren und Triggern ausgedrückt werden. Die Transaktionen werden einer Partition in jedem Container zugeordnet und mit ACID-Semantik nach dem Prinzip „alles oder nichts“ ausgeführt. Sie sind dabei von anderem gleichzeitig ausgeführtem Code und Benutzeranforderungen isoliert. Falls bei der serverseitigen Ausführung des JavaScript-Anwendungscodes ein Ausnahmefehler auftritt, wird für die gesamte Transaktion ein Rollback durchgeführt. 

### <a name="what-is-a-container"></a>Was ist ein Container?

Ein Container ist eine Gruppe von Dokumenten mit der zugehörigen JavaScript-Anwendungslogik. Ein Container ist eine fakturierbare Entität, deren [Kosten](performance-levels.md) vom Durchsatz und belegten Speicher bestimmt werden. Container können eine oder mehrere Partitionen oder Server umfassen und können skaliert werden, um praktisch unbegrenzte Mengen an Speicher oder Durchsatz zu verarbeiten.

* Bei der SQL-API und der Cosmos DB-API für MongoDB-Konten ist ein Container einer Sammlung zugeordnet.
* Bei Cassandra- und Tabellen-API-Konten ist ein Container einer Tabelle zugeordnet.
* Bei Gremlin-API-Konten ist ein Container einem Diagramm (Graphen) zugeordnet.

Container stellen außerdem die Abrechnungseinheiten für Azure Cosmos DB dar. Die Kosten für jeden Container werden basierend auf dem bereitgestellten Durchsatz und dem verwendeten Speicherplatz pro Stunde berechnet. Weitere Informationen finden Sie unter [Azure Cosmos DB – Preise](https://azure.microsoft.com/pricing/details/cosmos-db/).

### <a name="how-do-i-create-a-database"></a>Wie erstelle ich eine Datenbank?

Sie können Datenbanken erstellen, indem Sie das [Azure-Portal](https://portal.azure.com), wie unter [Hinzufügen einer Sammlung](create-sql-api-dotnet.md#create-collection-database) beschrieben, oder eines der [Azure Cosmos DB SDKs](sql-api-sdk-dotnet.md) oder die [REST-APIs](/rest/api/cosmos-db/) verwenden.

### <a name="how-do-i-set-up-users-and-permissions"></a>Wie richte ich Benutzer und Berechtigungen ein?

Sie können Benutzer und Berechtigungen mit einem der [Cosmos DB-API-SDKs](sql-api-sdk-dotnet.md) oder mithilfe der [REST-APIs](/rest/api/cosmos-db/) erstellen.

### <a name="does-the-sql-api-support-sql"></a>Unterstützt die SQL-API SQL?

Die von SQL-API-Konten unterstützte SQL-Abfragesprache ist eine erweiterte Teilmenge der Abfragefunktionalität, die von SQL Server unterstützt wird. Die Azure Cosmos DB SQL-Abfragesprache bietet umfassende hierarchische und relationale Operatoren sowie die Erweiterbarkeit über JavaScript-basierte benutzerdefinierte Funktionen. Die JSON-Grammatik ermöglicht die Modellierung von JSON-Dokumenten als Struktur mit beschrifteten Knoten. Diese werden sowohl von den automatischen Indizierungstechniken als auch vom SQL-Abfragedialekt von Azure Cosmos DB verwendet. Weitere Informationen zur Verwendung der SQL-Grammatik finden Sie im Artikel [SQL-Abfrage][query].

### <a name="does-the-sql-api-support-sql-aggregation-functions"></a>Werden SQL-Aggregationsfunktionen von der SQL-API unterstützt?

Die SQL-API unterstützt per SQL-Grammatik Aggregation mit geringer Latenz und beliebiger Größe über die Aggregationsfunktionen `COUNT`, `MIN`, `MAX`, `AVG` und `SUM`. Weitere Informationen finden Sie unter [Aggregationsfunktionen](how-to-sql-query.md#Aggregates).

### <a name="how-does-the-sql-api-provide-concurrency"></a>Wie stellt die SQL-API Parallelität bereit?

Die SQL-API unterstützt die Steuerung für optimistische Nebenläufigkeit (OCC) durch HTTP-Entitätstags bzw. ETags. Jede SQL-API-Ressource verfügt über ein ETag. Das ETag wird jedes Mal auf dem Server festgelegt, wenn ein Dokument aktualisiert wird. Der ETag-Header und der aktuelle Wert sind in allen Antwortnachrichten enthalten. ETags können mit dem „If-Match“-Header verwendet werden, um den Server entscheiden zu lassen, ob eine Ressource aktualisiert werden soll. Der „If-Match“-Wert ist der ETag-Wert, für den eine Überprüfung erfolgt. Wenn der ETag-Wert mit dem ETag-Wert des Servers übereinstimmt, wird die Ressource aktualisiert. Wenn das ETag nicht mehr aktuell ist, lehnt der Server den Vorgang mit dem Antwortcode „HTTP 412 – Vorbedingungsfehler“ ab. Der Client ruft anschließend die Ressource erneut ab, um den aktuellen ETag-Wert für die Ressource zu erhalten. Darüber hinaus können ETags mit dem „If-None-Match“-Header verwendet werden, um festzustellen, ob ein erneutes Abrufen einer Ressource erforderlich ist.

Zum Nutzen der optimistischen Nebenläufigkeit in .NET verwenden Sie die [AccessCondition](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.accesscondition.aspx) -Klasse. Ein Beispiel für .NET finden Sie unter [Program.cs](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/DocumentManagement/Program.cs) im „DocumentManagement“-Beispiel auf GitHub.

### <a name="how-do-i-perform-transactions-in-the-sql-api"></a>Wie führe ich Transaktionen in der SQL-API aus?

Die SQL-API unterstützt sprachintegrierte Transaktionen über gespeicherte JavaScript-Prozeduren und Trigger. Alle Datenbankvorgänge in Skripts werden im Rahmen der Momentaufnahmeisolation durchgeführt. Wenn es sich um eine Sammlung mit nur einer Partition handelt, wird der Umfang der Ausführung an die Sammlung angepasst. Falls die Sammlung partitioniert wird, wird die Ausführung an Dokumente mit dem gleichen Partitionsschlüsselwert in der Sammlung angepasst. Eine Momentaufnahme der Dokumentversionen (ETags) wird zu Beginn der Transaktion angefertigt und erst dann festgeschrieben, wenn das Skript erfolgreich ausgeführt wurde. Falls JavaScript einen Fehler ausgibt, wird für die Transaktion ein Rollback ausgeführt. Weitere Informationen finden Sie unter [Serverseitige JavaScript-Programmierung für Azure Cosmos DB](stored-procedures-triggers-udfs.md).

### <a name="how-can-i-bulk-insert-documents-into-cosmos-db"></a>Wie kann ich eine Masseneinfügung für Dokumente in Cosmos DB ausführen?

Sie haben die folgenden Möglichkeiten, um in Azure Cosmos DB die Masseneinfügung für Dokumente auszuführen:

* Das Massenexecutortool, wie unter [Verwenden der Massenexecutor-.NET-Bibliothek](bulk-executor-dot-net.md) und [Verwenden der Massenexecutor-Java-Bibliothek](bulk-executor-java.md) beschrieben.
* Mit dem Datenmigrationstool, wie unter [Datenbankmigrationstool für Azure Cosmos DB](import-data.md) beschrieben.
* Mit serverseitigen Verfahren, wie unter [Serverseitige JavaScript-Programmierung für Azure Cosmos DB](stored-procedures-triggers-udfs.md) beschrieben.

### <a name="does-the-sql-api-support-resource-link-caching"></a>Unterstützt die SQL-API die Zwischenspeicherung von Ressourcenlinks?

Ja. Da es sich bei Azure Cosmos DB um einen RESTful-Dienst handelt, sind Ressourcenlinks unveränderbar und können zwischengespeichert werden. SQL-API-Clients können einen „If-None-Match“-Header für Lesevorgänge für ressourcenähnliche Dokumente oder Sammlungen festlegen und dann ihre lokalen Kopien aktualisieren, nachdem die Serverversion geändert wurde.

### <a name="is-a-local-instance-of-sql-api-available"></a>Ist eine lokale Instanz der SQL-API verfügbar?

Ja. Der [Azure Cosmos DB-Emulator](local-emulator.md) stellt eine High-Fidelity-Emulation des Cosmos DB-Diensts bereit. Es werden die gleichen Funktionen wie bei Azure Cosmos DB unterstützt, z.B. Erstellen und Abfragen von JSON-Dokumenten, Bereitstellen und Skalieren von Sammlungen und Ausführen von gespeicherten Prozeduren und Triggern. Sie können Anwendungen mit dem Azure Cosmos DB-Emulator entwickeln und testen und diese in Azure auf globaler Ebene bereitstellen, indem Sie eine einzige Konfigurationsänderung am Verbindungsendpunkt für Azure Cosmos DB vornehmen.

### <a name="why-are-long-floating-point-values-in-a-document-rounded-when-viewed-from-data-explorer-in-the-portal"></a>Warum werden lange Gleitkommawerte in einem Dokument gerundet, wenn sie im Daten-Explorer im Portal angezeigt werden?

Dies ist eine Einschränkung von JavaScript. JavaScript verwendet das Gleitkommazahlenformat mit doppelter Genauigkeit (gemäß Definition in IEEE 754) und kann nur Zahlen zwischen -(2<sup>53</sup> - 1) und 2<sup>53</sup>-1 (d.h. 9007199254740991) enthalten.

### <a name="where-are-permissions-allowed-in-the-object-hierarchy"></a>Wo sind in der Objekthierarchie Berechtigungen zulässig?

Die Erstellung von Berechtigungen mit ResourceTokens ist auf der Containerebene und den untergeordneten Elementen (z.B. Dokumente, Anlagen) zulässig. Hieraus ergibt sich, dass auf Datenbank- oder Kontoebene derzeit keine Berechtigung erstellt werden kann.

## <a name="azure-cosmos-dbs-api-for-mongodb"></a>Azure Cosmos DB-API für MongoDB

### <a name="what-is-the-azure-cosmos-dbs-api-for-mongodb"></a>Was ist die Azure Cosmos DB-API für MongoDB?

Die Azure Cosmos DB-API für MongoDB ist eine Kompatibilitätsebene, mit der Anwendungen auf einfache und transparente Weise über vorhandene, von der Community unterstützte SDKs und Treiber für MongoDB mit der nativen Azure Cosmos DB-Datenbank-Engine kommunizieren können. Entwickler können jetzt vorhandene MongoDB-Toolketten und -Fähigkeiten verwenden, um Anwendungen zu erstellen, die Azure Cosmos DB nutzen. Entwickler profitieren von den einzigartigen Funktionen von Azure Cosmos DB, z. B. globale Verteilung mit Multimasterreplikation, automatische Indizierung, Sicherungsverwaltung, finanziell abgesicherte Vereinbarungen zum Servicelevel (SLAs) usw.

### <a name="how-do-i-connect-to-my-database"></a>Wie stelle ich eine Verbindung mit meiner Datenbank her?

Die schnellste Möglichkeit zum Herstellen einer Verbindung mit einer Cosmos-Datenbank mit der API für MongoDB von Azure Cosmos DB besteht darin, dass Sie zum [Azure-Portal](https://portal.azure.com) wechseln. Navigieren Sie zu Ihrem Konto, und klicken Sie dann im Navigationsmenü auf der linken Seite auf **Schnellstart**. Der Schnellstart ist die beste Möglichkeit, um Codeausschnitte für das Herstellen einer Verbindung mit Ihrer Datenbank abzurufen.

Azure Cosmos DB erzwingt strenge Sicherheitsanforderungen und -standards. Azure Cosmos DB-Konten erfordern eine Authentifizierung und eine sichere Kommunikation über SSL. Verwenden Sie daher unbedingt TLSv1.2.

Weitere Informationen finden Sie unter [Verbinden einer MongoDB-Anwendung mit Azure Cosmos DB](connect-mongodb-account.md).

### <a name="are-there-additional-error-codes-that-i-need-to-deal-with-while-using-azure-cosmos-dbs-api-for-mongodb"></a>Gibt es weitere Fehlercodes, die ich für den Umgang mit der Azure Cosmos DB-API für MongoDB kennen sollte?

Neben den allgemeinen MongoDB-Fehlercodes verfügt die Azure Cosmos DB-API für MongoDB über eigene, spezifische Fehlercodes:

| Error               | Code  | BESCHREIBUNG  | Lösung  |
|---------------------|-------|--------------|-----------|
| TooManyRequests     | 16500 | Die Gesamtanzahl der verbrauchten Anforderungseinheiten hat die bereitgestellte Anforderungseinheitenrate für die Sammlung überschritten und wurde gedrosselt. | Erwägen Sie eine Skalierung des einem Container oder mehreren Containern zugewiesenen Durchsatzes über das Azure-Portal oder eine Wiederholung des Vorgangs. |
| ExceededMemoryLimit | 16501 | Der Vorgang ist ein mehrinstanzenfähiger Dienst und hat die Speicherzuweisung des Clients überschritten. | Verringern Sie den Umfang des Vorgangs mithilfe restriktiverer Abfragekriterien, oder wenden Sie sich im [Azure-Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) an den Support. <br><br>Beispiel: <em>&nbsp;&nbsp;&nbsp;&nbsp;db.getCollection('users').aggregate([<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{$match: {name: "Andy"}}, <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{$sort: {age: -1}}<br>&nbsp;&nbsp;&nbsp;&nbsp;])</em>) |

### <a name="is-the-simba-driver-for-mongodb-supported-for-use-with-azure-cosmos-dbs-api-for-mongodb"></a>Wird die Verwendung des Simba-Treibers für MongoDB mit der Azure Cosmos DB-API für MongoDB unterstützt?

Ja, Sie können den Mongo-ODBC-Treiber von Simba mit der Azure Cosmos DB-API für MongoDB verwenden.

## <a id="table"></a>Tabellen-API

### <a name="how-can-i-use-the-table-api-offering"></a>Wie kann ich das Table-API-Angebot verwenden?

Die Azure Cosmos DB-Table-API ist im [Azure-Portal][azure-portal] verfügbar. Zuerst müssen Sie sich für ein Azure-Abonnement registrieren. Nach der Registrierung können Sie Ihrem Azure-Abonnement ein Azure Cosmos DB-Table-API-Konto und Ihrem Konto anschließend Tabellen hinzufügen.

Die unterstützten Sprachen und entsprechenden Schnellstartanleitungen finden Sie in der [Einführung in die Table-API von Azure Cosmos DB](table-introduction.md).

### <a name="do-i-need-a-new-sdk-to-use-the-table-api"></a>Benötige ich für die Verwendung der Table-API ein neues SDK?

Nein. Vorhandene Storage SDKs sollten auch weiterhin funktionieren. Es empfiehlt sich allerdings immer, die neuesten SDKs zu verwenden, um eine optimale Unterstützung zu gewährleisten und in vielen Fällen eine bessere Leistung zu erzielen. Eine Liste mit den verfügbaren Sprachen finden Sie in der [Einführung in die Table-API von Azure Cosmos DB](table-introduction.md).

### <a name="where-is-table-api-not-identical-with-azure-table-storage-behavior"></a>Inwiefern unterscheidet sich das Verhalten von Azure Table Storage von der Table-API?

Benutzer, die mit Azure Table Storage vertraut sind und Tabellen mit der Table-API von Azure Cosmos DB erstellen möchten, müssen sich auf einige Unterschiede beim Verhalten einstellen:

* Die Table-API von Azure Cosmos DB verwendet zur Gewährleistung der garantierten Leistung ein Modell mit reservierter Kapazität. Das bedeutet allerdings, dass für die Kapazität Kosten anfallen, sobald die Tabelle erstellt wird, auch wenn die Kapazität gar nicht genutzt wird. Bei Azure Table Storage wird dagegen nur die genutzte Kapazität in Rechnung gestellt. Das erklärt, warum bei der Tabellen-API eine SLA mit einer Lesegeschwindigkeit von 10 ms und einer Schreibgeschwindigkeit von 15 ms im 99. Perzentil möglich ist, während Azure Table Storage nur eine SLA mit 10 Sekunden bietet. Bei Table-API-Tabellen fallen dafür jedoch auch Kosten für leere Tabellen ohne jegliche Anforderungen an, um sicherzustellen, dass gemäß der SLA von Azure Cosmos DB die erforderliche Kapazität für mögliche Anforderungen an die Tabellen zur Verfügung steht.
* Von der Tabellen-API zurückgegebene Abfrageergebnisse werden nicht wie bei Azure Table Storage nach Partitionsschlüssel/Zeilenschlüssel sortiert.
* Die Größe von Zeilenschlüsseln ist auf 255 Bytes begrenzt.
* Batches können nur bis zu 2 MB enthalten.
* CORS wird derzeit nicht unterstützt.
* Bei Tabellennamen in Azure Table Storage wird die Groß- und Kleinschreibung nicht berücksichtigt, bei der Tabellen-API von Azure Cosmos DB dagegen schon.
* Einige der internen Azure Cosmos DB-Formate für Codierungsinformationen (etwa binäre Felder) sind derzeit nicht so wie effizient wie möglicherweise gewünscht. Daher können unerwartete Einschränkungen in Bezug auf die Datengröße auftreten. Beispiel: Derzeit kann zum Speichern der Binärdaten die Kapazität einer Tabellenentität von 1 MB nicht vollständig genutzt werden, da die Codierung den Datenumfang vergrößert.
* Entitätseigenschaftenname „ID“ derzeit nicht unterstützt
* „TakeCount“ von „TableQuery“ ist nicht auf 1.000 beschränkt.

Bei der REST-API werden einige Endpunkte/Abfrageoptionen von der Tabellen-API von Azure Cosmos DB nicht unterstützt:

| REST-Methode | REST-Endpunkt/-Abfrageoption | Dokumentations-URLs | Erklärung |
| ------------| ------------- | ---------- | ----------- |
| GET, PUT | /?restype=service@comp=properties| [Set Table Service Properties](https://docs.microsoft.com/rest/api/storageservices/set-table-service-properties) (Festlegen von Tabellendiensteigenschaften) und [Get Table Service Properties](https://docs.microsoft.com/rest/api/storageservices/get-table-service-properties) (Abrufen von Tabellendiensteigenschaften) | Dieser Endpunkt dient zum Festlegen von CORS-Regeln, Storage Analytics-Konfiguration und Protokollierungseinstellungen. CORS wird derzeit nicht unterstützt. Analyse und Protokollierung werden in Azure Cosmos DB anders behandelt als in Azure Storage-Tabellen. |
| OPTIONS | /\<Name_der_Tabellenressource> | [Pre-flight CORS table request](https://docs.microsoft.com/rest/api/storageservices/preflight-table-request) (Preflight-CORS-Tabellenanforderung) | Dies ist Teil von CORS und wird daher derzeit von Azure Cosmos DB nicht unterstützt. |
| GET | /?restype=service@comp=stats | [Get Table Service Stats](https://docs.microsoft.com/rest/api/storageservices/get-table-service-stats) (Abrufen der Tabellendienststatistik) | Gibt Aufschluss über die Replikationsgeschwindigkeit von Daten zwischen primärer Datenbank und sekundären Datenbanken. Wird in Cosmos DB nicht benötigt, da hier die Replikation Teil der Schreibvorgänge ist. |
| GET, PUT | /mytable?comp=acl | [Get Table ACL](https://docs.microsoft.com/rest/api/storageservices/get-table-acl) (Tabellen-ACL abrufen) und [Set Table ACL](https://docs.microsoft.com/rest/api/storageservices/set-table-acl) (Tabellen-ACL festlegen) | Dient zum Abrufen und Festlegen der gespeicherten Zugriffsrichtlinien, die für die Verwaltung von Shared Access Signatures (SAS) verwendet werden. SAS werden zwar unterstützt, aber unterschiedlich festgelegt und verwaltet. |

Darüber hinaus unterstützt die Table-API von Azure Cosmos DB nur das JSON-Format und nicht das ATOM-Format.

Azure Cosmos DB unterstützt zwar Shared Access Signatures (SAS), bestimmte Richtlinien werden jedoch nicht unterstützt. Hierzu zählen insbesondere Richtlinien im Zusammenhang mit Verwaltungsvorgängen (etwa das Recht zum Erstellen neuer Tabellen).

Beim .NET SDK werden einige Klassen und Methoden derzeit nicht von Azure Cosmos DB unterstützt.

| Klasse | Nicht unterstützte Methode |
|-------|-------- |
| CloudTableClient | \*ServiceProperties* |
|                  | \*ServiceStats* |
| CloudTable | SetPermissions* |
|            | GetPermissions* |
| TableServiceContext | * (Diese Klasse ist veraltet.) |
| TableServiceEntity | „ “ |
| TableServiceExtensions | „ “ |
| TableServiceQuery | „ “ |

Falls einer dieser Unterschiede ein Problem für Ihr Projekt darstellt, teilen Sie uns dies unter [askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com) mit.

### <a name="how-do-i-provide-feedback-about-the-sdk-or-bugs"></a>Wie kann ich Feedback zum SDK und zu Bugs geben?

Sie können Ihr Feedback wie folgt mitteilen:

* [Uservoice](https://feedback.azure.com/forums/263030-azure-cosmos-db)
* [MSDN-Forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurecosmosdb)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-cosmosdb). Stack Overflow eignet sich am besten für Fragen zur Programmierung. Vergewissern Sie sich, dass Ihre Frage [themenbezogen](https://stackoverflow.com/help/on-topic) ist und [so viele Details wie möglich enthält, damit sie klar und beantwortbar ist](https://stackoverflow.com/help/how-to-ask).

### <a name="what-is-the-connection-string-that-i-need-to-use-to-connect-to-the-table-api"></a>Welche Verbindungszeichenfolge muss ich zum Herstellen einer Verbindung mit der Table-API verwenden?

Die Verbindungszeichenfolge lautet:

```
DefaultEndpointsProtocol=https;AccountName=<AccountNamefromCosmos DB;AccountKey=<FromKeysPaneofCosmosDB>;TableEndpoint=https://<AccountName>.table.cosmosdb.azure.com
```

Die Verbindungszeichenfolge können Sie im Azure-Portal auf der Seite „Verbindungszeichenfolge“ ermitteln.

### <a name="how-do-i-override-the-config-settings-for-the-request-options-in-the-net-sdk-for-the-table-api"></a>Wie setze ich die Konfigurationseinstellungen für die Anforderungsoptionen im .NET SDK für die Table-API außer Kraft?

Einige Einstellungen werden über die CreateCloudTableClient-Methode behandelt, andere über „app.config“ im appSettings-Abschnitt der Clientanwendung. Informationen zu Konfigurationseinstellungen finden Sie unter [Azure Cosmos DB-Funktionen](tutorial-develop-table-dotnet.md).

### <a name="are-there-any-changes-for-customers-who-are-using-the-existing-azure-table-storage-sdks"></a>Müssen Kunden, die die vorhandenen Azure Table Storage SDKs verwenden, Änderungen vornehmen?

None (Keine): Für Bestands- oder Neukunden, die die vorhandenen Azure Table Storage-SDKs verwenden, ergeben sich keine Änderungen.

### <a name="how-do-i-view-table-data-thats-stored-in-azure-cosmos-db-for-use-with-the-table-api"></a>Wie zeige ich in Azure Cosmos DB gespeicherte Tabellendaten für die Verwendung mit der Tabellen-API an?

Sie können das Azure-Portal verwenden, um die Daten zu durchsuchen. Außerdem können Sie den Code der Table-API oder die in der nächsten Antwort erwähnten Tools verwenden.

### <a name="which-tools-work-with-the-table-api"></a>Welche Tools kann ich mit der Table-API verwenden?

Sie können den [Azure Storage-Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer) verwenden.

Tools, die eine Verbindungszeichenfolge im weiter oben angegebenen Format akzeptieren, können die neue Table-API unterstützen. Eine Liste mit Tabellentools steht auf der Seite [Azure Storage-Clienttools](../storage/common/storage-explorers.md) zur Verfügung.

### <a name="is-the-concurrency-on-operations-controlled"></a>Ist die Parallelität im Betrieb gesteuert?

Ja. Die optimistische Nebenläufigkeit wird über den Einsatz des ETag-Mechanismus bereitgestellt.

### <a name="is-the-odata-query-model-supported-for-entities"></a>Wird das OData-Abfragemodell für Entitäten unterstützt?

Ja. Die Table-API unterstützt OData-Abfragen und LINQ-Abfragen.

### <a name="can-i-connect-to-azure-table-storage-and-azure-cosmos-db-table-api-side-by-side-in-the-same-application"></a>Kann in einer Anwendung gleichzeitig eine Verbindung mit Azure Table Storage und mit der Table-API von Azure Cosmos DB hergestellt werden?

Ja. Sie können Verbindungen herstellen, indem Sie zwei separate CloudTableClient-Instanzen erstellen, die über die Verbindungszeichenfolge jeweils auf ihren eigenen URI verweisen.

### <a name="how-do-i-migrate-an-existing-azure-table-storage-application-to-this-offering"></a>Wie migriere ich eine bereits vorhandene Azure Table Storage-Anwendung zu diesem Angebot?

Hierzu können Sie [AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy) oder das [Migrationstool für Azure Cosmos DB-Daten](import-data.md) verwenden.

### <a name="how-is-expansion-of-the-storage-size-done-for-this-service-if-for-example-i-start-with-n-gb-of-data-and-my-data-will-grow-to-1-tb-over-time"></a>Wie erfolgt die Erweiterung der Speichergröße für diesen Dienst, wenn ich beispielsweise mit *n* GB Daten beginne und meine Daten im Laufe der Zeit auf 1 TB anwachsen?

Azure Cosmos DB wurde dafür ausgelegt, mithilfe von horizontaler Skalierung unbeschränkten Speicherplatz zu bieten. Der Dienst kann Ihren Speicher überwachen und effektiv vergrößern.

### <a name="how-do-i-monitor-the-table-api-offering"></a>Wie kann ich das Table-API-Angebot überwachen?

Anforderungen und Speicherverwendung können im Bereich **Metriken** der Table-API überwacht werden.

### <a name="how-do-i-calculate-the-throughput-i-require"></a>Wie kann ich den erforderlichen Durchsatz berechnen?

Sie können den Kapazitätskalkulator verwenden, um den für die Vorgänge erforderlichen TableThroughput zu berechnen. Weitere Informationen finden Sie unter [Estimate Request Units and Data Storage](https://www.documentdb.com/capacityplanner) (Schätzen von Anforderungseinheiten und Datenspeicher). Im Allgemeinen können Sie Ihre Entität als JSON-Code darstellen und die Zahlen für Ihre Vorgänge eingeben.

### <a name="can-i-use-the-table-api-sdk-locally-with-the-emulator"></a>Kann ich das SDK der Table-API lokal mit dem Emulator verwenden?

Derzeit leider nicht.

### <a name="can-my-existing-application-work-with-the-table-api"></a>Kann ich meine bereits vorhandene Anwendung mit der Table-API verwenden?

Ja. Die gleiche API wird unterstützt.

### <a name="do-i-need-to-migrate-my-existing-azure-table-storage-applications-to-the-sdk-if-i-dont-want-to-use-the-table-api-features"></a>Muss ich meine vorhandenen Azure Table Storage-Anwendungen zum SDK migrieren, wenn ich die Funktionen der Tabellen-API nicht nutzen möchte?

Nein. Sie können den vorhandenen Azure Table Storage-Bestand ohne irgendwelche Unterbrechungen erstellen und verwenden. Wenn Sie die Tabellen-API nicht nutzen, können Sie allerdings nicht von der automatischen Indizierung, der zusätzlichen Konsistenzoption oder der globalen Verteilung profitieren.

### <a name="how-do-i-add-replication-of-the-data-in-the-table-api-across-more-than-one-region-of-azure"></a>Wie füge ich in der Tabellen-API die Datenreplikation über mehrere Azure-Regionen hinweg hinzu?

Sie können die [globalen Replikationseinstellungen](tutorial-global-distribution-sql-api.md#portal) im Azure Cosmos DB-Portal verwenden, um Regionen hinzuzufügen, die sich für Ihre Anwendung eignen. Beim Entwickeln einer global verteilten Anwendung sollten Sie Ihre Anwendung darüber hinaus mit einem auf die lokale Region festgelegten Wert von PreferredLocation hinzufügen, um für eine niedrige Leselatenz zu sorgen.

### <a name="how-do-i-change-the-primary-write-region-for-the-account-in-the-table-api"></a>Wie ändere ich die primäre Schreibregion für das Konto in der Table-API?

Sie können den Portalbereich für globale Azure Cosmos DB-Replikation verwenden, um eine Region hinzuzufügen, und dann ein Failover in die benötigte Region durchführen. Anweisungen finden Sie unter [Entwickeln für Azure Cosmos DB-Konten für mehrere Regionen](high-availability.md).

### <a name="how-do-i-configure-my-preferred-read-regions-for-low-latency-when-i-distribute-my-data"></a>Wie lassen sich beim Verteilen meiner Daten meine bevorzugten Leseregionen konfigurieren, um niedrige Latenz zu erreichen?

Verwenden Sie den Schlüssel PreferredLocation in der Datei „app.config“, um das Lesen vom lokalen Standort zu unterstützen. Bei bereits vorhandenen Anwendungen gibt die Table-API einen Fehler aus, wenn „LocationMode“ festgelegt ist. Entfernen Sie diesen Code, da die Table-API diese Angabe aus der Datei „app.config“ abruft. 

### <a name="how-should-i-think-about-consistency-levels-in-the-table-api"></a>Wie kann ich mir die Konsistenzebenen in der Table-API vorstellen?

Azure Cosmos DB bietet einen wohlüberlegten Kompromiss zwischen Konsistenz, Verfügbarkeit und Wartezeit. Azure Cosmos DB verfügt über fünf Konsistenzebenen für Entwickler, die die Table-API nutzen. Sie können also auf Tabellenebene das passende Konsistenzmodell auswählen und beim Abfragen der Daten individuelle Anforderungen verwenden. Wenn ein Client eine Verbindung herstellt, kann er eine Konsistenzebene angeben. Die Ebene kann über das consistencyLevel-Argument von „CreateCloudTableClient“ geändert werden.

Die Table-API bietet Lesevorgänge mit geringer Wartezeit und die Garantie „Eigene Schreibvorgänge lesen“ mit begrenzter Veraltung als Standardeinstellung. Weitere Informationen finden Sie unter [Konsistenzebenen](consistency-levels.md).

Standardmäßig wird für Azure-Tabellenspeicher „Starke Konsistenz“ innerhalb einer Region und „Letztliche Konsistenz“ an den sekundären Standorten verwendet.

### <a name="does-azure-cosmos-db-table-api-offer-more-consistency-levels-than-azure-table-storage"></a>Bietet die Table-API von Azure Cosmos DB mehr Konsistenzebenen als Azure Table Storage?

Ja. Informationen dazu, wie Sie von der weiten Verteilung von Azure Cosmos DB profitieren können, finden Sie unter [Einstellbare Datenkonsistenzebenen in Azure Cosmos DB](consistency-levels.md). Da für die Konsistenzebenen Garantien gegeben werden, können Sie sie vertrauensvoll nutzen.

### <a name="when-global-distribution-is-enabled-how-long-does-it-take-to-replicate-the-data"></a>Wie viel Zeit wird für die Replikation der Daten benötigt, wenn die globale Verteilung aktiviert ist?

Azure Cosmos DB führt für die Daten dauerhaft einen Commit in der lokalen Region durch und überträgt die Daten innerhalb von Millisekunden sofort in andere Regionen. Diese Replikation ist nur von der Roundtripzeit (Round-Trip Time, RTT) des Datencenters abhängig. Weitere Informationen zur Funktion für die globale Verteilung von Azure Cosmos DB finden Sie unter [Globale Datenverteilung mit Azure Cosmos DB](distribute-data-globally.md).

### <a name="can-the-read-request-consistency-level-be-changed"></a>Kann die Konsistenzebene für Leseanforderungen geändert werden?

Mit Azure Cosmos DB können Sie die Konsistenzebene auf Containerebene (in der Tabelle) festlegen. Mit dem .NET SDK können Sie die Ebene ändern, indem Sie den Wert für den TableConsistencyLevel-Schlüssel in der Datei „app.config“ angeben. Mögliche Werte: „Stark“, „Begrenzte Veraltung“, „Sitzung“, „Präfixkonsistenz“ und „Letztlich“ Weitere Informationen finden Sie unter [Einstellbare Datenkonsistenzebenen in Azure Cosmos DB](consistency-levels.md). Der Hauptaspekt hierbei ist, dass Sie die Konsistenzebene für Anforderungen nicht auf einen höheren Wert als für die Einstellung für die Tabelle festlegen können. Beispielsweise ist es nicht möglich, die Konsistenzebene für die Tabelle auf „Letztlich“ und die Konsistenzebene für die Anforderung auf „Sicher“ festzulegen.

### <a name="how-does-the-table-api-handle-failover-if-a-region-goes-down"></a>Wie läuft mit der Table-API das Failover bei einem Ausfall einer Region ab?

Die Table-API nutzt die global verteilte Plattform von Azure Cosmos DB. Um sicherzustellen, dass Ihre Anwendung Ausfallzeiten des Datencenters tolerieren kann, sollten Sie für das Konto mindestens eine weitere Region im Azure Cosmos DB-Portal aktivieren: [Entwickeln für Azure Cosmos DB-Konten für mehrere Regionen](high-availability.md). Sie können die Priorität der Region mithilfe des Portals festlegen: [Entwickeln für Azure Cosmos DB-Konten für mehrere Regionen](high-availability.md).

Sie können für das Konto beliebig viele Regionen hinzufügen und steuern, wohin das Failover erfolgen kann, indem Sie eine Failoverpriorität festlegen. Für die Verwendung der Datenbank müssen Sie dafür eine Anwendung bereitstellen. Wenn Sie so vorgehen, treten für Ihre Kunden keine Ausfallzeiten auf. Das [neueste .NET-Client-SDK](table-sdk-dotnet.md) verfügt über Auto-Homing, die anderen SDKs dagegen nicht. Es kann also die ausgefallene Region erkennen und automatisch das Failover in die neue Region durchführen.

### <a name="is-the-table-api-enabled-for-backups"></a>Können mit der Table-API Sicherungen erstellt werden?

Ja. Die Table-API nutzt für Sicherungen die global verteilte Plattform von Azure Cosmos DB. Sicherungen werden automatisch erstellt. Weitere Informationen finden Sie unter [Automatische Onlinesicherung und -wiederherstellung mit Azure Cosmos DB](online-backup-and-restore.md).

### <a name="does-the-table-api-index-all-attributes-of-an-entity-by-default"></a>Indiziert die Table-API standardmäßig alle Attribute einer Entität?

Ja, alle Attribute einer Entität werden standardmäßig indiziert. Weitere Informationen finden Sie unter [Azure Cosmos DB: Indizierungsrichtlinien](index-policy.md).

### <a name="does-this-mean-i-dont-have-to-create-more-than-one-index-to-satisfy-the-queries"></a>Bedeutet dies, dass ich nicht mehrere Indizes erstellen muss, um Abfragen zu bedienen?

Ja. Die Table-API von Azure Cosmos DB ermöglicht die automatische Indizierung aller Attribute ganz ohne Schemadefinition. Dank dieser Automatisierung können sich Entwickler auf die Anwendung konzentrieren und verlieren keine Zeit mehr mit der Indexerstellung und -verwaltung. Weitere Informationen finden Sie unter [Azure Cosmos DB: Indizierungsrichtlinien](index-policy.md).

### <a name="can-i-change-the-indexing-policy"></a>Kann ich die Indizierungsrichtlinie ändern?

Ja. Sie können die Indizierungsrichtlinie ändern, indem Sie die Indexdefinition bereitstellen. Sie müssen die Einstellungen richtig codieren und mit Escapezeichen versehen.

Für .NET-fremde SDKs kann die Indizierungsrichtlinie nur über den **Daten-Explorer** im Portal festgelegt werden: Navigieren Sie zu der spezifischen Tabelle, die Sie ändern möchten. Navigieren Sie dann zu **Scale & Settings** (Skalierung und Einstellungen) > „Indizierungsrichtlinie“, nehmen Sie die gewünschte Änderung vor, und klicken Sie anschließend auf **Speichern**.

Bei Verwendung des .NET SDKs kann die Richtlinie in der Datei „app.config“ übermittelt werden:

```JSON
{
  "indexingMode": "consistent",
  "automatic": true,
  "includedPaths": [
    {
      "path": "/somepath",
      "indexes": [
        {
          "kind": "Range",
          "dataType": "Number",
          "precision": -1
        },
        {
          "kind": "Range",
          "dataType": "String",
          "precision": -1
        }
      ]
    }
  ],
  "excludedPaths":
[
 {
      "path": "/anotherpath"
 }
]
}
```

### <a name="azure-cosmos-db-as-a-platform-seems-to-have-lot-of-capabilities-such-as-sorting-aggregates-hierarchy-and-other-functionality-will-you-be-adding-these-capabilities-to-the-table-api"></a>Azure Cosmos DB als Plattform verfügt scheinbar über viele Funktionen, z.B. Sortierung, Aggregate, Hierarchie und andere. Werden diese Funktionen der Table-API hinzugefügt?

Die Table-API bietet die gleichen Abfragefunktionen wie Azure Table Storage. Azure Cosmos DB unterstützt auch Sortieren, Aggregieren, räumliche Abfrage, Hierarchie und eine Vielzahl von integrierten Funktionen. Weitere Informationen finden Sie unter [SQL-Abfragen](how-to-sql-query.md).

### <a name="when-should-i-change-tablethroughput-for-the-table-api"></a>Wann sollte ich „TableThroughput“ für die Table-API ändern?

Sie sollten TableThroughput ändern, wenn eine der folgenden Bedingungen gilt:

* Sie führen das Extrahieren, Transformieren und Laden (ETL) für die Daten durch, oder Sie möchten innerhalb eines kurzen Zeitraums viele Daten hochladen.
* Sie benötigen für den Container oder mehrere Container auf dem Back-End mehr Durchsatz. Beispielsweise erkennen Sie, dass der genutzte Durchsatz den bereitgestellten Durchsatz übersteigt, und es kommt zu einer Drosselung. Weitere Informationen finden Sie unter [Festlegen von Durchsatz für Azure Cosmos DB-Container](set-throughput.md).

### <a name="can-i-scale-up-or-scale-down-the-throughput-of-my-table-api-table"></a>Kann ich den Durchsatz meiner Table-API-Tabelle zentral hoch- oder herunterskalieren?

Ja. Sie können den Skalierungsbereich des Azure Cosmos DB-Portals verwenden, um den Durchsatz zu skalieren. Weitere Informationen finden Sie unter [Festlegen von Durchsatz für Azure Cosmos DB-Container](set-throughput.md).

### <a name="is-a-default-tablethroughput-set-for-newly-provisioned-tables"></a>Wird für neu bereitgestellte Tabellen ein TableThroughput-Standardwert festgelegt?

Ja. Wenn Sie TableThroughput nicht mithilfe von „app.config“ außer Kraft setzen und keinen vorkonfigurierten Container in Azure Cosmos DB verwenden, erstellt der Dienst eine Tabelle mit einem Durchsatz von 400.

### <a name="is-there-any-change-of-pricing-for-existing-customers-of-the-azure-table-storage-service"></a>Müssen Bestandskunden des Azure Table Storage-Diensts mit Preisänderungen rechnen?

None (Keine): Es gibt keine Preisänderungen für Bestandskunden von Azure Table Storage.

### <a name="how-is-the-price-calculated-for-the-table-api"></a>Wie errechnet sich der Preis für die Table-API?

Der Preis hängt vom zugewiesenen TableThroughput-Wert ab.

### <a name="how-do-i-handle-any-rate-limiting-on-the-tables-in-table-api-offering"></a>Wie kann ich beim Table-API-Angebot eine ggf. auftretende Ratenbegrenzung behandeln?

Wenn die Anforderungsrate die Kapazität des bereitgestellten Durchsatzes für die zugrunde liegenden Container übersteigt, erhalten Sie einen Fehler, und das SDK wiederholt den Aufruf, indem die Wiederholungsrichtlinie angewendet wird.

### <a name="why-do-i-need-to-choose-a-throughput-apart-from-partitionkey-and-rowkey-to-take-advantage-of-the-table-api-offering-of-azure-cosmos-db"></a>Warum muss ich zusätzlich zu „PartitionKey“ und „RowKey“ einen Durchsatz festlegen, um das Table-API-Angebot von Azure Cosmos DB nutzen zu können?

Azure Cosmos DB legt einen Standarddurchsatz für Ihren Container fest, wenn Sie in der Datei „app.config“ oder über das Portal keinen Durchsatz angeben.

Azure Cosmos DB bietet Garantien für Leistung und Latenz mit Obergrenzen im Betrieb. Diese Garantie ist möglich, wenn die Engine die Kontrolle für die Vorgänge des Mandanten erzwingen kann. Durch das Festlegen von TableThroughput wird sichergestellt, dass Sie den garantierten Durchsatz und die Wartezeit auch erhalten, da diese Kapazität von der Plattform reserviert wird und somit der Erfolg des Vorgangs sichergestellt ist.

Mithilfe der Durchsatzspezifikation können Sie dies flexibel ändern, um von der Saisonalität Ihrer Anwendung zu profitieren, die Durchsatzanforderungen zu erfüllen und Kosten zu sparen.

### <a name="azure-table-storage-has-been-inexpensive-for-me-because-i-pay-only-to-store-the-data-and-i-rarely-query-the-azure-cosmos-db-table-api-offering-seems-to-be-charging-me-even-though-i-havent-performed-a-single-transaction-or-stored-anything-can-you-explain"></a>Azure Table Storage war für mich kostengünstig, da ich nur für die Speicherung der Daten zahle und nur selten Abfragen durchführe. Beim Tabellen-API-Angebot von Azure Cosmos DB fallen für mich aber offenbar auch Kosten an, wenn ich keine einzige Transaktion durchgeführt und nichts gespeichert habe. Gibt es dafür eine Erklärung?

Azure Cosmos DB wurde als global verteiltes, SLA-basiertes System mit Garantien für Verfügbarkeit, Wartezeit und Durchsatz entwickelt. Wenn Sie den Durchsatz in Azure Cosmos DB reservieren, ist er – im Gegensatz zum Durchsatz anderer Systeme – garantiert. Mit Azure Cosmos DB werden zusätzliche Funktionen bereitgestellt, die von Kunden gefordert wurden, z.B. sekundäre Indizes und globale Verteilung.

### <a name="i-never-get-a-quota-full-notification-indicating-that-a-partition-is-full-when-i-ingest-data-into-azure-table-storage-with-the-table-api-i-do-get-this-message-is-this-offering-limiting-me-and-forcing-me-to-change-my-existing-application"></a>Ich habe die Benachrichtigung „Kontingent erschöpft“ (als Information, dass eine Partition voll ist) beim Erfassen von Daten in Azure Table Storage nie erhalten. Bei der Table-API wird diese Meldung aber angezeigt. Schränkt mich dieses Angebot ein, und zwingt es mich zum Ändern meiner vorhandenen Anwendung?

Azure Cosmos DB ist ein SLA-basiertes System, das unbeschränkte Skalierung mit Garantien für Wartezeit, Durchsatz, Verfügbarkeit und Konsistenz bietet. Achten Sie darauf, dass Ihre Datengröße und der Index verwaltbar und skalierbar sind, um sicherzustellen, dass Sie die garantierte Premium-Leistung erhalten. Mit dem 10 GB-Grenzwert für die Anzahl von Entitäten bzw. Elementen pro Partitionsschlüssel wird dafür gesorgt, dass wir eine hervorragende Such- und Abfrageleistung bieten können. Um sicherzustellen, dass Ihre Anwendung gut skaliert werden kann, raten wir Ihnen auch bei Azure Storage, *keine* Hot Partition zu erstellen, indem Sie alle Informationen in einer Partition speichern und abfragen.

### <a name="so-partitionkey-and-rowkey-are-still-required-with-the-table-api"></a>Also benötige ich „PartitionKey“ und „RowKey“ auch bei der Table-API?

Ja. Da die Oberfläche der Table-API der Oberfläche des Azure Table Storage SDKs ähnelt, ermöglicht der Partitionsschlüssel eine effiziente Datenverteilung. Der Zeilenschlüssel ist innerhalb dieser Partition eindeutig. Der Zeilenschlüssel muss vorhanden sein und darf nicht wie beim Standard-SDK NULL sein. Die RowKey-Länge beträgt 255 Bytes. Die PartitionKey-Länge beträgt 1 KB.

### <a name="what-are-the-error-messages-for-the-table-api"></a>Welche Fehlermeldungen gibt es für die Table-API?

Da Azure Table Storage und die Table-API von Azure Cosmos DB die gleichen SDKs verwenden, sind auch die Fehler größtenteils gleich.

### <a name="why-do-i-get-throttled-when-i-try-to-create-lot-of-tables-one-after-another-in-the-table-api"></a>Warum kommt es zu einer Drosselung, wenn ich versuche, über die Table-API nacheinander eine große Anzahl von Tabellen zu erstellen?

Azure Cosmos DB ist ein SLA-basiertes System mit Garantien für Wartezeit, Durchsatz, Verfügbarkeit und Konsistenz. Da es sich um ein bereitgestelltes System handelt, werden Ressourcen reserviert, um diese Anforderungen zu garantieren. Eine hohe Erstellungsrate von Tabellen in rascher Folge wird erkannt und gedrosselt. Es wird empfohlen, dass Sie sich die Erstellungsrate der Tabellen ansehen und auf weniger als „5 pro Minute“ reduzieren. Bedenken Sie, dass es sich bei der Table-API um ein bereitgestelltes System handelt. Ab dem Moment, in dem Sie es bereitstellen, werden Gebühren fällig.

## <a name="gremlin-api"></a>Gremlin-API

### <a name="for-cnet-development-should-i-use-the-microsoftazuregraphs-package-or-gremlinnet"></a>Soll ich für die C#/.NET-Entwicklung das Paket „Microsoft.Azure.Graphs“ oder Gremlin.NET verwenden?

Die Azure Cosmos DB-Gremlin-API nutzt die Open-Source-Treiber als Hauptconnectors für den Dienst. Daher ist die empfohlene Option die Verwendung von [Treibern, die von Apache Tinkerpop unterstützt werden](https://tinkerpop.apache.org/).

### <a name="how-are-rus-charged-when-running-queries-on-a-graph-database"></a>Wie werden RUs/Sek. beim Ausführen von Abfragen für eine Graphdatenbank in Rechnung gestellt?

Alle Graphobjekte – Scheitelpunkte und Kanten – werden im Back-End als JSON-Dokumente dargestellt. Da eine Gremlin-Abfrage jeweils ein oder mehrere Graphobjekte ändern kann, stehen die damit verbundenen Kosten in direkter Beziehung zu den Objekten – Kanten, die von der Abfrage verarbeitet werden. Denselben Prozess verwendet Azure Cosmos DB für alle anderen APIs. Weitere Informationen finden Sie unter [Anforderungseinheiten in Azure Cosmos DB](request-units.md).

Die RU-Gebühr basiert auf dem Arbeits-DataSet des Durchlaufs und nicht auf dem Resultset. Beispiel: Wenn eine Abfrage einen einzelnen Scheitelpunkt als Ergebnis ermitteln soll, dabei jedoch mehrere andere Objekte durchlaufen muss, basieren die Kosten auf allen Graphobjekten, die zum Berechnen des Ergebnisscheitelpunkts benötigt werden.

### <a name="whats-the-maximum-scale-that-a-graph-database-can-have-in-azure-cosmos-db-gremlin-api"></a>Was ist die maximale mögliche Skalierung einer Graphdatenbank in der Azure Cosmos DB-Gremlin-API?

Azure Cosmos DB nutzt [horizontale Partitionierung](partition-data.md), um einem Anstieg der Speicher- und Durchsatzanforderungen automatisch gerecht zu werden. Das Höchstmaß an Durchsatz und Speicherkapazität einer Workload richtet sich nach der Menge von Partitionen, die einer bestimmten Sammlung zugeordnet sind. Eine Gremlin-API-Sammlung weist jedoch einen bestimmten Satz von Richtlinien auf, um eine optimale bedarfsorientierte Leistung zu gewährleisten. Weitere Informationen zur Partitionierung und zu Best Practices finden Sie im Artikel [Partitionieren und horizontales Skalieren in Azure Cosmos DB](partition-data.md).

### <a name="how-can-i-protect-against-injection-attacks-using-gremlin-drivers"></a>Wie kann ich mithilfe von Gremlin-Treibern vor Einschleusungsangriffen schützen?

Die meisten nativen Gremlin-Treiber von Apache TinkerPop lassen die Bereitstellung eines Wörterbuchs von Parametern für die Abfrageausführung zu. Hier finden Sie Beispiele für die Bereitstellung in [Gremlin.Net](https://tinkerpop.apache.org/docs/3.2.7/reference/#gremlin-DotNet) und in [Gremlin-Javascript](https://github.com/Azure-Samples/azure-cosmos-db-graph-nodejs-getting-started/blob/master/app.js).

### <a name="why-am-i-getting-the-gremlin-query-compilation-error-unable-to-find-any-method-error"></a>Warum erhalte ich die Fehlermeldung „Gremlin Query Compilation Error: Unable to find any method“ (Fehler bei der Kompilierung von Gremlin-Abfragen: Methoden konnten nicht gefunden werden.)?

Die Azure Cosmos DB-Gremlin-API implementiert eine Teilmenge der auf der Gremlin-Oberfläche definierten Funktionen. Unterstützte Schritte und weitere Informationen finden Sie im Artikel [Gremlin-Unterstützung](gremlin-support.md).

Die beste Problemumgehung besteht darin, die Gremlin-Schritte mit den unterstützten Funktionen erneut zu schreiben, da alle wesentlichen Gremlin-Schritte von Azure Cosmos DB unterstützt werden.

### <a name="why-am-i-getting-the-websocketexception-the-server-returned-status-code-200-when-status-code-101-was-expected-error"></a>Warum erhalte ich die Fehlermeldung „WebSocketException: The server returned status code '200' when status code '101' was expected“ (WebSocketException: Der Server hat den Statuscode '200' zurückgegeben, obwohl der Statuscode '101' erwartet wurde)?

Dieser Fehler wird wahrscheinlich ausgelöst, wenn der falsche Endpunkt verwendet wird. Der Endpunkt, der diesen Fehler generiert, hat das folgende Muster:

`https:// YOUR_DATABASE_ACCOUNT.documents.azure.com:443/`

Dies ist der Dokumentendpunkt für Ihre Graphdatenbank.  Der richtige Endpunkt für die Verwendung ist der Gremlin-Endpunkt, der das folgende Format aufweist:

`https://YOUR_DATABASE_ACCOUNT.gremlin.cosmosdb.azure.com:443/`

### <a name="why-am-i-getting-the-requestrateistoolarge-error"></a>Warum erhalte ich den Fehler „RequestRateIsTooLarge“?

Dieser Fehler weist darauf hin, dass die zugeordneten Anforderungseinheiten pro Sekunde für die Verarbeitung der Abfrage nicht ausreichen. Dieser Fehler wird in der Regel angezeigt, wenn Sie eine Abfrage ausführen, die alle Scheitelpunkte abruft:

```
// Query example:
g.V()
```

Diese Abfrage versucht, alle Scheitelpunkte aus dem Graph abzurufen. Daher entsprechen die Kosten für diese Abfrage mindestens der Anzahl von Scheitelpunkten in Bezug auf RUs. Die RUs/Sek.-Einstellung muss dieser Abfrage entsprechend angepasst werden.

### <a name="why-do-my-gremlin-driver-connections-get-dropped-eventually"></a>Warum werden meine Gremlin-Treiberverbindungen schließlich gelöscht?

Eine Gremlin-Verbindung wird über eine WebSocket-Verbindung hergestellt. WebSocket-Verbindungen haben zwar keine bestimmte Gültigkeitsdauer, die Gremlin-API von Azure Cosmos DB beendet Verbindungen im Leerlauf jedoch nach 30 Minuten Inaktivität.

### <a name="why-cant-i-use-fluent-api-calls-in-the-native-gremlin-drivers"></a>Warum kann ich keine Fluent-API-Aufrufe in den nativen Gremlin-Treibern verwenden?

Fluent-API-Aufrufe werden von der Azure Cosmos DB-Gremlin-API noch nicht unterstützt. Fluent-API-Aufrufe erfordern ein internes Formatierungsfeature namens Bytecode-Unterstützung, das von der Azure Cosmos DB-Gremlin-API derzeit nicht unterstützt wird. Aus demselben Grund wird auch der aktuelle Gremlin-JavaScript-Treiber derzeit nicht unterstützt.

### <a name="how-can-i-evaluate-the-efficiency-of-my-gremlin-queries"></a>Wie kann ich die Effizienz meiner Gremlin-Abfragen auswerten?

Mit dem Vorschauschritt **executionProfile()** können Sie eine Analyse des Abfrageausführungsplans bereitstellen. Dieser Schritt muss wie im folgenden Beispiel gezeigt am Ende einer Gremlin-Abfrage hinzugefügt werden:

**Abfragebeispiel**

```
g.V('mary').out('knows').executionProfile()
```

**Beispielausgabe**

```json
[
  {
    "gremlin": "g.V('mary').out('knows').executionProfile()",
    "totalTime": 8,
    "metrics": [
      {
        "name": "GetVertices",
        "time": 3,
        "annotations": {
          "percentTime": 37.5
        },
        "counts": {
          "resultCount": 1
        }
      },
      {
        "name": "GetEdges",
        "time": 5,
        "annotations": {
          "percentTime": 62.5
        },
        "counts": {
          "resultCount": 0
        },
        "storeOps": [
          {
            "count": 0,
            "size": 0,
            "time": 0.6
          }
        ]
      },
      {
        "name": "GetNeighborVertices",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 0
        }
      },
      {
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 0
        }
      }
    ]
  }
]
```

Die Ausgabe des Profils oben zeigt, wie viel Zeit zum Abrufen der Scheitelpunkt- und Kantenobjekte aufgewendet wird, sowie die Größe des Arbeitsdatasets. Dies bezieht sich auf die Standardkostenmessungen für Azure Cosmos DB-Abfragen.

## <a id="cassandra"></a>Cassandra-API

### <a name="what-is-the-protocol-version-supported-by-azure-cosmso-db-cassandra-api-is-there-a-plan-to-support-other-protocols"></a>Welche Protokollversion wird von der Cassandra-API von Azure Cosmos DB unterstützt? Ist eine Unterstützung weiterer Protokolle geplant?

Die Apache Cassandra-API für Azure Cosmos DB unterstützt derzeit die CQL-Version 4. Wenn Sie Feedback zur Unterstützung anderer Protokolle haben, informieren Sie uns per [Uservoice-Feedback](https://feedback.azure.com/forums/263030-azure-cosmos-db), oder senden Sie eine E-Mail an [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com).

### <a name="why-is-choosing-a-throughput-for-a-table-a-requirement"></a>Warum muss ein Durchsatz für eine Tabelle ausgewählt werden?

Azure Cosmos DB legt den Standarddurchsatz für Ihren Container abhängig davon fest, ob Sie die Tabelle über das Portal oder über CQL erstellt haben.
Azure Cosmos DB bietet Garantien für Leistung und Latenz mit Obergrenzen im Betrieb. Diese Garantie ist möglich, wenn die Engine die Kontrolle für die Vorgänge des Mandanten erzwingen kann. Durch Festlegen des Durchsatzes wird sichergestellt, dass Sie den garantierten Durchsatz und die garantierte Wartezeit erhalten. Hierzu wird die entsprechende Kapazität von der Plattform reserviert, um den Erfolg des Vorgangs zu gewährleisten.
Sie können den Durchsatz flexibel anpassen, um von Schwankungen bei der Nutzung Ihrer Anwendung zu profitieren und Kosten zu sparen.

Das Durchsatzkonzept wird im Artikel [Anforderungseinheiten in Azure Cosmos DB](request-units.md) erläutert. Der Durchsatz für eine Tabelle wird gleichmäßig auf die zugrunde liegenden physischen Partitionen verteilt.

### <a name="what-is-the-default-rus-of-table-when-created-through-cql-what-if-i-need-to-change-it"></a>Wie viele Anforderungseinheiten pro Sekunde (RU/s) bietet eine über CQL erstellte Tabelle standardmäßig? Was ist, wenn ich ihn ändern muss?

Azure Cosmos DB verwendet als Einheit für den Durchsatz Anforderungseinheiten pro Sekunde (RU/s). Tabellen, die über CQL erstellt wurden, bieten 400 RU. Sie können die Anforderungseinheiten über das Portal ändern.

CQL

```
CREATE TABLE keyspaceName.tablename (user_id int PRIMARY KEY, lastname text) WITH cosmosdb_provisioned_throughput=1200
```

.NET

```csharp
int provisionedThroughput = 400;
var simpleStatement = new SimpleStatement($"CREATE TABLE {keyspaceName}.{tableName} (user_id int PRIMARY KEY, lastname text)");
var outgoingPayload = new Dictionary<string, byte[]>();
outgoingPayload["cosmosdb_provisioned_throughput"] = Encoding.UTF8.GetBytes(provisionedThroughput.ToString());
simpleStatement.SetOutgoingPayload(outgoingPayload);
```

### <a name="what-happens-when-throughput-is-used-up"></a>Was geschieht, wenn der Durchsatz verbraucht wird?

Azure Cosmos DB bietet Garantien für Leistung und Latenz mit Obergrenzen im Betrieb. Diese Garantie ist möglich, wenn die Engine die Kontrolle für die Vorgänge des Mandanten erzwingen kann. Möglich ist dies basierend auf der Einstellung für den Durchsatz, die sicherstellt, dass Sie den garantierten Durchsatz und die garantierte Wartezeit erhalten. Hierzu wird die entsprechende Kapazität von der Plattform reserviert, um den Erfolg des Vorgangs zu gewährleisten.
Wenn diese Kapazität überschritten wird, erhalten Sie diesbezüglich eine Fehlermeldung.
0x1001 Overloaded: the request can't be processed because "Request Rate is large". (0x1001 Überladen: Die Anforderung kann nicht verarbeitet werden – „Anforderungsrate ist groß“.) An diesem Punkt ist es wichtig, zu überprüfen, welche Vorgänge mit welchem Umfang zu diesem Problem beigetragen haben. Informationen über die verbrauchte Kapazität oberhalb der bereitgestellten Kapazität erhalten Sie mithilfe der Metriken im Portal. Sie müssen dann sicherstellen, dass die Kapazität nahezu gleichmäßig für alle zugrunde liegenden Partitionen verbraucht wird. Wenn der größte Teil des Durchsatzes von einer Partition verbraucht wird, tritt bei der Workload eine Scherung auf.

Es stehen Metriken zur Verfügung, die Ihnen zeigen, wie der Durchsatz von einzelnen Partitionen oder in aggregierter Form über Stunden, Tage und je sieben Tage verwendet wird. Weitere Informationen finden Sie unter [Überwachen und Debuggen mit Metriken in Azure Cosmos DB](use-metrics.md).

Diagnoseprotokolle werden im Artikel [Diagnoseprotokollierung für Azure Cosmos DB](logging.md) erläutert.

### <a name="does-the-primary-key-map-to-the-partition-key-concept-of-azure-cosmos-db"></a>Entspricht die Primärschlüsselzuordnung dem Konzept von Partitionsschlüsseln von Azure Cosmos DB?

Ja. Der Partitionsschlüssel wird verwendet, um die Entität am richtigen Speicherort zu platzieren. In Azure Cosmos DB wird er zum Suchen der richtigen logischen Partition auf einer physischen Partition verwendet. Das Konzept der Partitionierung wird im Artikel [Partitionieren und Skalieren in Azure Cosmos DB](partition-data.md) sehr gut erläutert. Die wesentliche Lektion ist hierbei, dass eine logische Partition derzeit die 10-GB-Grenze nicht überschreiten sollte.

### <a name="what-happens-when-i-get-a-quota-full-notification-indicating-that-a-partition-is-full"></a>Was passiert, wenn ich die Benachrichtigung „Kontingent erschöpft“ als Hinweis darauf erhalte, dass eine Partition voll ist?

Azure Cosmos DB ist ein SLA-basiertes System, das unbeschränkte Skalierung mit Garantien für Wartezeit, Durchsatz, Verfügbarkeit und Konsistenz bietet. Diese unbegrenzte Speicherung basiert auf einer horizontalen Skalierung der Daten mit der Partitionierung als grundlegendem Konzept. Das Konzept der Partitionierung wird im Artikel [Partitionieren und Skalieren in Azure Cosmos DB](partition-data.md) sehr gut erläutert.

Das Limit von 10 GB für die Anzahl von Entitäten oder Elementen pro logischer Partition sollte eingehalten werden. Um sicherzustellen, dass Ihre Anwendung gut skaliert werden kann, sollten Sie *keine* Hot Partition erstellen, indem Sie alle Informationen in einer Partition speichern und abfragen. Dieser Fehler kann nur bei einer Datenscherung auftreten – wenn Sie also über viele Daten für einen Partitionsschlüssel (mehr als 10&nbsp;GB) verfügen. Sie ermitteln die Verteilung der Daten mithilfe des Storage-Portals. Zum Beheben dieses Fehlers erstellen Sie die Tabelle neu und wählen einen präzisen primären Partitionsschlüssel aus, der eine bessere Verteilung der Daten ermöglicht.

### <a name="is-it-possible-to-use-cassandra-api-as-key-value-store-with-millions-or-billions-of-individual-partition-keys"></a>Ist es möglich, die Cassandra-API als Schlüsselwertspeicher mit Millionen oder Milliarden einzelnen Partitionsschlüsseln zu verwenden?

Azure Cosmos DB kann eine beliebige Datenmenge speichern, indem der Speicher horizontal hochskaliert wird. Dies gilt unabhängig vom Durchsatz. Ja. Sie können die Cassandra-API immer nur zum Speichern und Abrufen von Schlüssel-Wert-Paaren durch Angabe des richtigen primären/Partitionsschlüssels verwenden. Diese einzelnen Schlüssel erhalten ihre eigenen logischen Partitionen und sind unabhängig von der physischen Partition.

### <a name="is-it-possible-to-create-more-than-one-table-with-apache-cassandra-api-of-azure-cosmos-db"></a>Können mit der Apache Cassandra-API von Azure Cosmos DB mehrere Tabellen erstellt werden?

Ja, mit der Apache Cassandra-API von Azure Cosmos DB können mehrere Tabellen erstellt werden. Jede dieser Tabellen wird bei Durchsatz und Speicherung als Einheit behandelt.

### <a name="is-it-possible-to-create-more-than-one-table-in-succession"></a>Ist es möglich, mehrere Tabellen nacheinander zu erstellen?

Azure Cosmos DB ist ein ressourcengesteuertes System für Aktivitäten auf Daten- und Steuerungsebene. Container (beispielsweise Sammlungen und Tabellen) sind Laufzeitentitäten, die für eine bestimmte Durchsatzkapazität bereitgestellt werden. Die Erstellung dieser Container in schneller Folge ist eine unerwartete Aktivität und wird gedrosselt. Wenn Sie Tests durchführen, bei denen Tabellen sofort abgelegt/erstellt werden, sollten Sie diese Vorgänge voneinander trennen.

### <a name="what-is-maximum-number-of-tables-that-can-be-created"></a>Wie viele Tabellen können maximal erstellt werden?

Es gibt kein physisches Limit für die Anzahl von Tabellen. Senden Sie eine E-Mail an [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com), wenn Sie über viele Tabellen verfügen (wenn die Größe dauerhaft über 10 TB liegt), die mit anderen Geschwindigkeiten als den üblichen 10 s oder 100 s erstellt werden müssen.

### <a name="what-is-the-maximum--of-keyspace-that-we-can-create"></a>Wie viele Keyspaces können wir maximal erstellen?

Es gibt keine physische Obergrenze für die Anzahl von Keyspaces, da sie lediglich Metadatencontainer sind. Senden Sie eine E-Mail an [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com), wenn Sie über eine große Anzahl von Keyspaces verfügen.

### <a name="is-it-possible-to-bring-in-lot-of-data-after-starting-from-normal-table"></a>Ist es möglich, mit einer normalen Tabelle zu beginnen und später eine große Datenmenge hinzuzufügen?

Die Speicherkapazität wird automatisch verwaltet und erhöht sich, wenn weitere Daten hinzukommen. Daher können Sie unter anderem problemlos beliebig viele Daten importieren, ohne Knoten verwalten und bereitstellen zu müssen.

### <a name="is-it-possible-to-supply-yaml-file-settings-to-configure-apache-casssandra-api-of-azure-cosmos-db-behavior"></a>Kann das Verhalten der Apache Casssandra-API von Azure Cosmos DB mithilfe von YAML-Dateieinstellungen konfiguriert werden?

Bei der Apache Cassandra-API von Azure Cosmos DB handelt es sich um einen Plattformdienst. Dieser bietet Kompatibilität auf Protokollebene zum Ausführen von Vorgängen. Er vereinfacht die Verwaltung, Überwachung und Konfiguration. Entwickler und Benutzer müssen sich nicht mit Aspekten wie Verfügbarkeit, Tombstones, Schlüsselcache, Zeilencache, Bloomfiltern und zahlreichen anderen Einstellungen beschäftigen. Bei der Apache Cassandra-API von Azure Cosmos DB steht die Bereitstellung der benötigten Lese- und Schreibleistung ohne Mehraufwand durch Konfiguration und Verwaltung im Mittelpunkt.

### <a name="will-apache-cassandra-api-for-azure-cosmos-db-support-node-additioncluster-statusnode-status-commands"></a>Werden von der Apache Cassandra-API von Azure Cosmos DB Befehle zum Hinzufügen von Knoten bzw. für den Cluster-/Knotenstatus unterstützt?

Die Apache Cassandra-API ist ein Plattformdienst, der die Kapazitätsplanung und die Reaktion auf Elastizitätsanforderungen für Durchsatz und Speicher vereinfacht. Mit Azure Cosmos DB stellen Sie den benötigten Durchsatz bereit. Dieser kann dann beliebig oft zentral hoch- und herunterskaliert werden, ohne dass Sie sich Gedanken über das Hinzufügen/Löschen von Knoten machen oder sich um deren Verwaltung kümmern müssen. Dies bedeutet auch, dass Sie keine Tools zur Knoten- oder Clusterverwaltung verwenden müssen.

### <a name="what-happens-with-respect-to-various-config-settings-for-keyspace-creation-like-simplenetwork"></a>Wie verhält es sich hinsichtlich verschiedener Konfigurationseinstellungen (einfach/Netzwerk) für die Keyspaceerstellung?

Aus Gründen von Verfügbarkeit und geringer Wartezeit ist in Azure Cosmos DB bereits eine globale Verteilung integriert. Sie müssen keine Replikate oder andere Elemente einrichten. Schreibvorgänge werden in jeder Region, in der Sie Daten schreiben, immer dauerhaft im Quorum committet. Gleichzeitig werden Leistungsgarantien geboten.

### <a name="what-happens-with-respect-to-various-settings-for-table-metadata-like-bloom-filter-caching-read-repair-change-gcgrace-compression-memtableflushperiod-and-more"></a>Wie verhält es sich hinsichtlich verschiedener Einstellungen für Tabellenmetadaten wie Bloomfilter, Zwischenspeicherung, Lesereparaturänderungen, „gc_grace“ und „compression memtable_flush_period“ usw.?

Azure Cosmos DB bietet Leistung für Lese-/Schreibvorgänge und Durchsatz, ohne dass dafür Konfigurationseinstellungen bearbeitet werden müssen. Dadurch wird die versehentliche Änderung dieser Einstellungen verhindert.

### <a name="is-time-to-live-ttl-supported-for-cassandra-tables"></a>Wird für Cassandra-Tabellen Gültigkeitsdauer (Time To Live, TTL) unterstützt?

Ja. TTL wird unterstützt.

### <a name="is-it-possible-to-monitor-node-status-replica-status-gc-and-os-parameters-earlier-with-various-tools-what-needs-to-be-monitored-now"></a>Kann ich Knotenstatus, Replikatstatus, GC und Betriebssystemparameter mit verschiedenen Tools überwachen? Was muss nun überwacht werden?

Azure Cosmos DB ist ein Plattformdienst, mit dem Sie Ihre Produktivität steigern können, ohne sich Gedanken über die Verwaltung und Überwachung der Infrastruktur zu machen. Sie müssen nur auf den Durchsatz achten, der über die Metriken im Portal zu finden ist, wenn Ihr Durchsatz gedrosselt wird, und diesen dann erhöhen oder verringern.
Überwachen Sie die [SLAs](monitor-accounts.md).
Verwenden Sie [Metriken](use-metrics.md) und [Diagnoseprotokolle](logging.md).

### <a name="which-client-sdks-can-work-with-apache-cassandra-api-of-azure-cosmos-db"></a>Welche Client-SDKs können mit der Apache Cassandra-API von Azure Cosmos DB verwendet werden?

Für Clientprogramme wurden die Clienttreiber des Apache Cassandra SDK verwendet, die CQLv3 verwenden. Wenn Sie andere Treiber verwenden oder bei Ihnen Probleme auftreten, senden Sie eine E-Mail an [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com).

### <a name="is-composite-partition-key-supported"></a>Wird ein zusammengesetzter Partitionsschlüssel unterstützt?

Ja. Sie können unter Verwendung der regulären Syntax einen zusammengesetzten Partitionsschlüssel erstellen.

### <a name="can-i-use-stable-loader-for-data-loading"></a>Kann ich Daten mithilfe von „sstable loader“ laden?

Nein. In der Vorschauversion wird das sstable-Ladeprogramm nicht unterstützt.

### <a name="can-an-on-premises-apache-cassandra-cluster-be-paired-with-azure-cosmos-dbs-cassandra-api"></a>Kann ein lokaler Apache Cassandra-Cluster mit der Cassandra-API von Azure Cosmos DB gekoppelt werden?

Derzeit ist Azure Cosmos DB für Cloudumgebungen optimiert und kann ohne zusätzlichen Aufwand betrieben werden. Wenn Sie Kopplung benötigen, senden Sie eine E-Mail mit einer Beschreibung Ihres Szenarios an [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com).

### <a name="does-cassandra-api-provide-full-backups"></a>Bietet die Cassandra-API vollständige Sicherungen?

Azure Cosmos DB bietet zwei kostenlose vollständige Sicherungen, die zurzeit alle vier Stunden über alle APIs hinweg erstellt werden. Dadurch müssen Sie keinen Sicherungszeitplan o.Ä. einrichten.
Wenn Sie Aufbewahrungsdauer und Häufigkeit ändern möchten, senden Sie eine E-Mail an [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com), oder lösen Sie eine Supportanfrage aus. Informationen zu Sicherungsfunktion finden Sie im Artikel [Automatische Onlinesicherung und -wiederherstellung mit Azure Cosmos DB](online-backup-and-restore.md).

### <a name="how-does-the-cassandra-api-account-handle-failover-if-a-region-goes-down"></a>Wie läuft mit dem Cassandra-API-Konto das Failover bei einem Ausfall einer Region ab?

Die Cassandra-API von Azure Cosmos DB nutzt Teile der global verteilten Plattform von Azure Cosmos DB. Um sicherzustellen, dass Ihre Anwendung Ausfallzeiten des Datencenters tolerieren kann, sollten Sie für das Konto mindestens eine weitere Region im Azure Cosmos DB-Portal aktivieren: [Entwickeln für Azure Cosmos DB-Konten für mehrere Regionen](high-availability.md). Sie können die Priorität der Region mithilfe des Portals festlegen: [Entwickeln für Azure Cosmos DB-Konten für mehrere Regionen](high-availability.md).

Sie können für das Konto beliebig viele Regionen hinzufügen und steuern, wohin das Failover erfolgen kann, indem Sie eine Failoverpriorität festlegen. Für die Verwendung der Datenbank müssen Sie dafür eine Anwendung bereitstellen. Wenn Sie so vorgehen, treten für Ihre Kunden keine Ausfallzeiten auf.

### <a name="does-the-apache-cassandra-api-index-all-attributes-of-an-entity-by-default"></a>Indiziert die Apache Cassandra-API standardmäßig alle Attribute einer Entität?

Ja, alle Attribute einer Entität werden standardmäßig von Azure Cosmos DB indiziert. Weitere Informationen finden Sie unter [Azure Cosmos DB: Indizierungsrichtlinien](index-policy.md). Sie profitieren von garantierter Leistung mit konsistenter Indizierung und Schreibvorgängen mit dauerhaftem Commit im Quorum.

### <a name="does-this-mean-i-dont-have-to-create-more-than-one-index-to-satisfy-the-queries"></a>Bedeutet dies, dass ich nicht mehrere Indizes erstellen muss, um Abfragen zu bedienen?

Ja. Azure Cosmos DB ermöglicht die automatische Indizierung aller Attribute ganz ohne Schemadefinition. Dank dieser Automatisierung können sich Entwickler auf die Anwendung konzentrieren und verlieren keine Zeit mehr mit der Indexerstellung und -verwaltung. Weitere Informationen finden Sie unter [Azure Cosmos DB: Indizierungsrichtlinien](index-policy.md).

### <a name="can-i-use-the-new-cassandra-api-sdk-locally-with-the-emulator"></a>Kann ich das neue SDK der Cassandra-API lokal mit dem Emulator verwenden?

Wir planen die Unterstützung dieser Funktion in der Zukunft.

### <a name="azure-cosmos-db-as-a-platform-seems-to-have-lot-of-capabilities-such-as-change-feed-and-other-functionality-will-these-capabilities-be-added-to-the-cassandra-api"></a>Azure Cosmos DB als Plattform verfügt anscheinend über viele Funktionen wie z. B. Änderungsfeed und andere. Werden diese Funktionen auch noch in der Cassandra-API hinzugefügt?

Die Apache Cassandra-API bietet die gleiche CQL-Funktionalität wie Apache Cassandra. Wir werden die Durchführbarkeit einer Unterstützung der verschiedenen Funktionen untersuchen.

### <a name="feature-x-of-regular-cassandra-api-isnt-working-as-today-where-can-the-feedback-be-provided"></a>Feature X der regulären Cassandra-API funktioniert im Moment nicht, wo kann ich Feedback geben?

Übermitteln Sie Ihr Feedback über [Uservoice-Feedback](https://feedback.azure.com/forums/263030-azure-cosmos-db).

[azure-portal]: https://portal.azure.com
[query]: sql-api-sql-query.md
