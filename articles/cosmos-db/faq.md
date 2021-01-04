---
title: Häufig gestellte Fragen zu unterschiedlichen APIs in Azure Cosmos DB
description: Enthält Antworten auf häufig gestellte Fragen zu Azure Cosmos DB, einem weltweit verteilten Datenbankdienst, der Unterstützung für mehrere Datenbankmodelle bietet. Informieren Sie sich über Kapazität, Leistungsebenen und Skalierung.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/01/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 81c959467c4bb3700120917cf150773fb18e866d
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/12/2020
ms.locfileid: "97359828"
---
# <a name="frequently-asked-questions-about-different-apis-in-azure-cosmos-db"></a>Häufig gestellte Fragen zu unterschiedlichen APIs in Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

### <a name="what-are-the-typical-use-cases-for-azure-cosmos-db"></a>Was sind die typischen Anwendungsfälle für Azure Cosmos DB?

Azure Cosmos DB ist eine gute Wahl für neue Web-, Mobil-, Gaming- und IoT-Anwendungen, bei denen Dinge wie automatische Skalierung, vorhersagbare Leistung, kurze Reaktionszeiten im Millisekundenbereich und die Möglichkeit zum Abfragen von schemafreien Daten wichtig sind. Azure Cosmos DB eignet sich für schnelle Entwicklungen und die Unterstützung kontinuierlicher Iterationen von Anwendungsdatenmodellen. Anwendungen, mit denen von Benutzern erzeugte Inhalte und Daten verwaltet werden, sind ein [häufiger Anwendungsfall von Azure Cosmos DB](use-cases.md).

### <a name="how-does-azure-cosmos-db-offer-predictable-performance"></a>Wie wird bei Azure Cosmos DB für eine vorhersagbare Leistung gesorgt?

In Azure Cosmos DB wird der Durchsatz in [Anforderungseinheiten](request-units.md) (Request Units, RUs) gemessen. Ein Durchsatz von einer Anforderungseinheit entspricht dem Durchsatz der GET-Anforderung für ein Dokument mit einer Größe von 1 KB. Jeder Vorgang in Azure Cosmos DB (einschließlich der Ausführung von Lese- und Schreibvorgängen, SQL-Abfragen sowie gespeicherten Prozeduren) verfügt über einen deterministischen Wert für die Anforderungseinheiten, der auf dem erforderlichen Durchsatz zum Abschließen des Vorgangs basiert. Im Hinblick auf den Durchsatz Ihrer Anwendung müssen Sie sich also keine Gedanken um das Zusammenspiel von CPU, E/A-Leistung und Arbeitsspeicher machen, sondern können mit einer einzigen Kennzahl arbeiten: der Anforderungseinheit.

Sie können jeden Azure Cosmos-Container mit bereitgestelltem Durchsatz (Anforderungseinheiten des Durchsatzes pro Sekunde) konfigurieren. Für Anwendungen jeder Größe können Sie Vergleichstests einzelner Anforderungen durchführen, um deren Werte für die Anforderungseinheiten zu messen. Außerdem können Sie einen Container bereitstellen, um die Gesamtsumme der Anforderungseinheiten über alle Anforderungen hinweg zu verarbeiten. Sie können den Durchsatz des Containers auch hoch- oder herunterskalieren, wenn sich die Anforderungen Ihrer Anwendung ändern. Um mehr über Anforderungseinheiten zu erfahren und Hilfe zum Ermitteln Ihrer Containeranforderungen zu erhalten, testen Sie den [Durchsatzrechner](https://www.documentdb.com/capacityplanner).

### <a name="how-does-azure-cosmos-db-support-various-data-models-such-as-keyvalue-columnar-document-and-graph"></a>Wie unterstützt Azure Cosmos DB verschiedene Datenmodelle wie Schlüssel/Wert-, einspaltige, Dokument- und Diagrammdaten?

Aufgrund der ARS-Konzeption (Atoms, Records and Sequences; Atoms, Datensätze und Sequenzen), auf der Azure Cosmos DB beruht, werden Schlüssel/Wert(Tabellen)-, einspaltige, Dokument- und Diagrammdaten nativ unterstützt. Atoms, Datensätze und Sequenzen können problemlos verschiedenen Datenmodellen zugeordnet und auf diese projiziert werden. Die APIs für eine Teilmenge von Modellen sind derzeit verfügbar (SQL, MongoDB, Tabelle und Gremlin), während andere APIs speziell für zusätzliche Datenmodelle in Zukunft bereitgestellt werden.

Azure Cosmos DB verfügt über eine schemaagnostische Indizierungs-Engine, die alle erfassten Daten automatisch ohne Schema oder sekundäre Indizes seitens des Entwicklers indizieren kann. Die Engine basiert auf einem Satz von logischen Indexlayouts (invertiert, einspaltig, Struktur), die die Speicheranordnung von den Index- und Abfrageverarbeitungssubsystemen entkoppeln. Cosmos DB bietet auch die Möglichkeit, eine Reihe von Übertragungsprotokollen und APIs in einer erweiterbaren Weise zu unterstützen und effizient im Kerndatenmodell (1) sowie in den logischen Indexlayouts (2) zu übersetzen. So bietet es eine einzigartige native Unterstützung für mehrere Datenmodelle.

### <a name="can-i-use-multiple-apis-to-access-my-data"></a>Kann ich für den Zugriff auf meine Daten mehrere APIs verwenden?

Azure Cosmos DB ist der global verteilte Datenbankdienst von Microsoft mit mehreren Modellen. Mehrere Modelle bedeutet, dass Azure Cosmos DB mehrere APIs und mehrere Datenmodelle unterstützt. Unterschiedliche APIs verwenden jedoch verschiedene Datenformate für die Speicherung und das Übertragungsprotokoll. SQL verwendet beispielsweise JSON, MongoDB verwendet BSON, Table verwendet EDM, Cassandra verwendet CQL, und Gremlin verwendet JSON. Daher wird empfohlen, für den Zugriff auf die Daten in einem bestimmten Konto immer die gleiche API zu verwenden.

Jede API wird unabhängig ausgeführt, mit Ausnahme der Gremlin- und der SQL-API, die interoperabel sind.

### <a name="is-azure-cosmos-db-hipaa-compliant"></a>Ist Azure Cosmos DB HIPAA-kompatibel?

Ja, Azure Cosmos DB ist HIPAA-kompatibel. HIPAA gibt Anforderungen für die Verwendung, Offenlegung und den Schutz von individuell identifizierbaren Gesundheitsinformationen vor. Weitere Informationen finden Sie im [Microsoft Trust Center](/compliance/regulatory/offering-hipaa-hitech).

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

* [Frageseite von Microsoft Q&A](/answers/topics/azure-cosmos-db.html)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-cosmosdb). Stack Overflow eignet sich am besten für Fragen zur Programmierung. Vergewissern Sie sich, dass Ihre Frage [themenbezogen](https://stackoverflow.com/help/on-topic) ist und [so viele Details wie möglich enthält, damit sie klar und beantwortbar ist](https://stackoverflow.com/help/how-to-ask).

Wenn Sie neue Funktionen wünschen, erstellen Sie auf [Uservoice](https://feedback.azure.com/forums/263030-azure-cosmos-db) eine neue Anforderung.

Um ein Problem mit Ihrem Konto zu beheben, richten Sie im Azure-Portal eine [Anfrage an den Support](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="try-azure-cosmos-db-subscriptions"></a><a id="try-cosmos-db"></a>Testabonnements für Azure Cosmos DB

Nutzen Sie für eine begrenzte Zeit die Vorteile von Azure Cosmos DB auch ohne Abonnement – kostenlos und ohne jegliche Verpflichtung. Wenn Sie sich für ein Azure Cosmos DB-Testabonnement registrieren möchten, wechseln Sie zu [Azure Cosmos DB kostenlos testen](https://azure.microsoft.com/try/cosmosdb/), und verwenden Sie ein beliebiges persönliches Microsoft-Konto (Microsoft Account, MSA). Dieses Abonnement ist unabhängig von der [kostenlosen Azure-Testversion](https://azure.microsoft.com/free/) und kann zusätzlich zu einer kostenlosen Azure-Testversion oder einem kostenpflichtigen Azure-Abonnement verwendet werden.

Testabonnements für Azure Cosmos DB werden im Azure-Portal neben den anderen Abonnements zu Ihrer Benutzer-ID angezeigt.

Die folgenden Bedingungen gelten für Azure Cosmos DB-Testabonnements:

* Kontozugriff kann persönlichen Microsoft-Konten (MSA) gewährt werden. Vermeiden Sie die Verwendung von Azure Active Directory (Azure AD)-Konten oder Konten, die zu den Azure AD-Mandanten des Unternehmens gehören, weil es bei ihnen möglicherweise Einschränkungen gibt, die ein Gewähren des Zugriffs blockieren könnten.
* Ein [Container mit bereitgestelltem Durchsatz](./set-throughput.md#set-throughput-on-a-container) pro Abonnement für SQL-, Gremlin-API- und Tabellenkonten.
* Bis zu drei [Sammlungen mit bereitgestelltem Durchsatz](./set-throughput.md#set-throughput-on-a-container) pro Abonnement für MongoDB-Konten.
* Eine [Datenbank mit bereitgestelltem Durchsatz](./set-throughput.md#set-throughput-on-a-database) pro Abonnement. Datenbanken mit bereitgestelltem Durchsatz können eine beliebige Anzahl von Containern enthalten.
* 10 GB Speicherkapazität
* Die globale Replikation ist in den folgenden [Azure-Regionen](https://azure.microsoft.com/regions/) verfügbar: „USA, Mitte“, „Europa, Norden“ und „Asien, Südosten“
* Maximaler Durchsatz von 5.000 RU/s bei Bereitstellung auf Containerebene.
* Maximaler Durchsatz von 20.000 RU/s bei Bereitstellung auf Datenbankebene.
* Abonnements laufen nach 30 Tagen ab und können auf maximal 31 Tage verlängert werden. Nach dem Ablauf werden die enthaltenen Informationen gelöscht.
* Azure-Supporttickets können nicht für Azure Cosmos DB-Testkonten erstellt werden. Allerdings erhalten Abonnenten mit vorhandenen Supportplänen Support.

## <a name="set-up-azure-cosmos-db"></a>Einrichten von Azure Cosmos DB

### <a name="how-do-i-sign-up-for-azure-cosmos-db"></a>Wie führe ich die Registrierung für Azure Cosmos DB durch?

Azure Cosmos DB steht im Azure-Portal zur Verfügung. Registrieren Sie sich zuerst für ein Azure-Abonnement. Nachdem Sie sich registriert haben, können Sie Ihrem Azure-Abonnement ein Azure Cosmos DB-Konto hinzufügen.

### <a name="what-is-a-primary-key"></a>Was ist ein Primärschlüssel?

Ein Primärschlüssel ist ein Sicherheitstoken für den Zugriff auf alle Ressourcen eines Kontos. Personen, die über diesen Schlüssel verfügen, haben Lese- und Schreibzugriff auf alle Ressourcen im Datenbankkonto. Gehen Sie beim Verteilen von Primärschlüsseln mit Bedacht vor. Der primäre und sekundäre Primärschlüssel stehen auf dem Blatt **Schlüssel** des [Azure-Portals][azure-portal] zur Verfügung. Weitere Informationen zu Schlüsseln finden Sie unter [Anzeigen, Kopieren und erneutes Generieren von Zugriffsschlüsseln](manage-with-cli.md#list-account-keys).

### <a name="what-are-the-regions-that-preferredlocations-can-be-set-to"></a>Auf welche Regionen kann PreferredLocations festgelegt werden?

Der Wert für PreferredLocations kann auf alle Azure-Regionen festgelegt werden, in denen Cosmos DB verfügbar ist. Eine Liste mit den verfügbaren Regionen finden Sie unter [Azure-Regionen](https://azure.microsoft.com/regions/).

### <a name="is-there-anything-i-should-be-aware-of-when-distributing-data-across-the-world-via-the-azure-datacenters"></a>Gibt es Punkte, die ich beim weltweiten Verteilen von Daten mithilfe der Azure-Datencenter beachten muss?

Azure Cosmos DB ist in allen Azure-Regionen präsent. Dies ist auf der Seite mit den [Azure-Regionen](https://azure.microsoft.com/regions/) angegeben. Da es sich um den Kerndienst handelt, verfügt jedes neue Datencenter über eine Azure Cosmos DB-Präsenz.

Beachten Sie beim Festlegen einer Region, dass von Azure Cosmos DB unabhängige Clouds und Government Clouds respektiert werden. Wenn Sie ein Konto in einer [unabhängigen Region](https://azure.microsoft.com/global-infrastructure/) erstellen, können Sie also keine Replikation aus dieser [unabhängigen Region](https://azure.microsoft.com/global-infrastructure/) heraus durchführen. Ebenso ist es nicht möglich, die Replikation an anderen unabhängigen Standorten über ein externes Konto zu ermöglichen.

### <a name="is-it-possible-to-switch-from-container-level-throughput-provisioning-to-database-level-throughput-provisioning-or-vice-versa"></a>Kann von einer Durchsatzbereitstellung auf Containerebene zu einer Durchsatzbereitstellung auf Datenbankebene (oder umgekehrt) gewechselt werden?

Die Bereitstellung auf Container- und Datenbankebene wird gesondert angeboten. Für einen Wechsel zwischen diesen Bereitstellungsmethoden müssen Daten von der Quelle zum Ziel migriert werden. Dies bedeutet, dass Sie eine neue Datenbank oder einen neuen Container erstellen müssen und anschließend Daten mithilfe der [BulkExecutor-Bibliothek](bulk-executor-overview.md) oder mithilfe von [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) migrieren müssen.

### <a name="does-azure-cosmosdb-support-time-series-analysis"></a>Unterstützt Azure CosmosDB die Zeitreihenanalyse?

Ja, Azure CosmosDB unterstützt die Zeitreihenanalyse, hier ist ein Beispiel für das [Zeitreihenmuster](https://github.com/Azure/azure-cosmosdb-dotnet/tree/master/samples/Patterns). Dieses Beispiel zeigt, wie der Änderungsfeed zum Erstellen aggregierter Ansichten von Zeitreihendaten verwendet wird. Sie können diesen Ansatz durch die Verwendung von Spark Streaming oder einer anderen Streamdaten-Verarbeitungskomponente erweitern.

## <a name="what-are-the-azure-cosmos-db-service-quotas-and-throughput-limits"></a>Was sind die Dienstkontingente und Durchsatzgrenzwerte von Azure Cosmos DB?

Weitere Informationen finden Sie in den Azure Cosmos DB-Artikeln [Kontingente im Azure Cosmos DB-Dienst](concepts-limits.md) und [Bereitstellen des Durchsatzes für Container und Datenbanken](set-throughput.md#comparison-of-models).

## <a name="frequently-asked-questions-about-sql-api"></a><a id="sql-api-faq"></a>Häufig gestellte Fragen zur SQL-API

### <a name="how-do-i-start-developing-against-the-sql-api"></a>Wie beginne ich mit dem Entwickeln für die SQL-API?

Zuerst müssen Sie sich für ein Azure-Abonnement registrieren. Nachdem Sie sich für ein Azure-Abonnement registriert haben, können Sie Ihrem Azure-Abonnement einen SQL-API-Container hinzufügen. Anweisungen zum Hinzufügen eines Azure Cosmos DB-Kontos finden Sie unter [Erstellen eines Azure Cosmos-Datenbankkontos](create-sql-api-dotnet.md#create-account).

[SDKs](sql-api-sdk-dotnet.md) sind für .NET, Python, Node.js, JavaScript und Java verfügbar. Entwickler können außerdem die [RESTful HTTP-APIs](/rest/api/cosmos-db/) zur Interaktion mit Azure Cosmos DB-Ressourcen auf unterschiedlichen Plattformen und mit verschiedenen Sprachen nutzen.

### <a name="can-i-access-some-ready-made-samples-to-get-a-head-start"></a>Kann ich auf einige fertige Beispiele als Starthilfe zugreifen?

Beispiele für die [.NET](sql-api-dotnet-samples.md)-, [Java](https://github.com/Azure/azure-documentdb-java)-, [Node.js](sql-api-nodejs-samples.md)- und [Python](sql-api-python-samples.md)-SDKs für die SQL-API sind auf GitHub verfügbar.

### <a name="does-the-sql-api-database-support-schema-free-data"></a>Unterstützt die SQL-API-Datenbank schemafreie Daten?

Ja. Die SQL-API ermöglicht Anwendungen das Speichern beliebiger JSON-Dokumente ohne Schemadefinitionen oder -hinweise. Die Daten stehen unmittelbar zur Abfrage über die Azure Cosmos DB-SQL-Abfrageschnittstelle zur Verfügung.

### <a name="does-the-sql-api-support-acid-transactions"></a>Unterstützt die SQL-API ACID-Transaktionen?

Ja, die SQL-API unterstützt dokumentübergreifende Transaktionen, die in Form von gespeicherten JavaScript-Prozeduren und Triggern ausgedrückt werden. Die Transaktionen werden einer Partition in jedem Container zugeordnet und mit ACID-Semantik nach dem Prinzip „alles oder nichts“ ausgeführt. Sie sind dabei von anderem gleichzeitig ausgeführtem Code und Benutzeranforderungen isoliert. Falls bei der serverseitigen Ausführung des JavaScript-Anwendungscodes ein Ausnahmefehler auftritt, wird für die gesamte Transaktion ein Rollback durchgeführt. 

### <a name="what-is-a-container"></a>Was ist ein Container?

Ein Container ist eine Gruppe von Dokumenten mit der zugehörigen JavaScript-Anwendungslogik. Ein Container ist eine fakturierbare Entität, deren [Kosten](performance-levels.md) vom Durchsatz und belegten Speicher bestimmt werden. Container können eine oder mehrere Partitionen oder Server umfassen und können skaliert werden, um praktisch unbegrenzte Mengen an Speicher oder Durchsatz zu verarbeiten.

* Für die SQL-API wird die Ressource als Container bezeichnet.
* Bei der Cosmos DB-API für MongoDB-Konten ist ein Container einer Sammlung zugeordnet.
* Bei Cassandra- und Tabellen-API-Konten ist ein Container einer Tabelle zugeordnet.
* Bei Gremlin-API-Konten ist ein Container einem Diagramm (Graphen) zugeordnet.

Container stellen außerdem die Abrechnungseinheiten für Azure Cosmos DB dar. Die Kosten für jeden Container werden basierend auf dem bereitgestellten Durchsatz und dem verwendeten Speicherplatz pro Stunde berechnet. Weitere Informationen finden Sie unter [Azure Cosmos DB – Preise](https://azure.microsoft.com/pricing/details/cosmos-db/).

### <a name="how-do-i-create-a-database"></a>Wie erstelle ich eine Datenbank?

Sie können Datenbanken erstellen, indem Sie das [Azure-Portal](https://portal.azure.com), wie unter [Hinzufügen eines Containers](create-sql-api-java.md#add-a-container) beschrieben, oder eines der [Azure Cosmos DB SDKs](sql-api-sdk-dotnet.md) oder die [REST-APIs](/rest/api/cosmos-db/) verwenden.

### <a name="how-do-i-set-up-users-and-permissions"></a>Wie richte ich Benutzer und Berechtigungen ein?

Sie können Benutzer und Berechtigungen mit einem der [Cosmos DB-API-SDKs](sql-api-sdk-dotnet.md) oder mithilfe der [REST-APIs](/rest/api/cosmos-db/) erstellen.

### <a name="does-the-sql-api-support-sql"></a>Unterstützt die SQL-API SQL?

Die von SQL-API-Konten unterstützte SQL-Abfragesprache ist eine erweiterte Teilmenge der Abfragefunktionalität, die von SQL Server unterstützt wird. Die Azure Cosmos DB SQL-Abfragesprache bietet umfassende hierarchische und relationale Operatoren sowie die Erweiterbarkeit über JavaScript-basierte benutzerdefinierte Funktionen. Die JSON-Grammatik ermöglicht die Modellierung von JSON-Dokumenten als Struktur mit beschrifteten Knoten. Diese werden sowohl von den automatischen Indizierungstechniken als auch vom SQL-Abfragedialekt von Azure Cosmos DB verwendet. Weitere Informationen zur Verwendung der SQL-Grammatik finden Sie im Artikel [SQL-Abfrage][query].

### <a name="does-the-sql-api-support-sql-aggregation-functions"></a>Werden SQL-Aggregationsfunktionen von der SQL-API unterstützt?

Die SQL-API unterstützt per SQL-Grammatik Aggregation mit geringer Latenz und beliebiger Größe über die Aggregationsfunktionen `COUNT`, `MIN`, `MAX`, `AVG` und `SUM`. Weitere Informationen finden Sie unter [Aggregationsfunktionen](sql-query-aggregate-functions.md).

### <a name="how-does-the-sql-api-provide-concurrency"></a>Wie stellt die SQL-API Parallelität bereit?

Die SQL-API unterstützt die Steuerung für optimistische Nebenläufigkeit (OCC) durch HTTP-Entitätstags bzw. ETags. Jede SQL-API-Ressource verfügt über ein ETag. Das ETag wird jedes Mal auf dem Server festgelegt, wenn ein Dokument aktualisiert wird. Der ETag-Header und der aktuelle Wert sind in allen Antwortnachrichten enthalten. ETags können mit dem „If-Match“-Header verwendet werden, um den Server entscheiden zu lassen, ob eine Ressource aktualisiert werden soll. Der „If-Match“-Wert ist der ETag-Wert, für den eine Überprüfung erfolgt. Wenn der ETag-Wert mit dem ETag-Wert des Servers übereinstimmt, wird die Ressource aktualisiert. Wenn das ETag nicht mehr aktuell ist, lehnt der Server den Vorgang mit dem Antwortcode „HTTP 412 – Vorbedingungsfehler“ ab. Der Client ruft anschließend die Ressource erneut ab, um den aktuellen ETag-Wert für die Ressource zu erhalten. Darüber hinaus können ETags mit dem „If-None-Match“-Header verwendet werden, um festzustellen, ob ein erneutes Abrufen einer Ressource erforderlich ist.

Zum Nutzen der optimistischen Nebenläufigkeit in .NET verwenden Sie die [AccessCondition](/dotnet/api/microsoft.azure.documents.client.accesscondition) -Klasse. Ein Beispiel für .NET finden Sie unter [Program.cs](https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/samples/code-samples/DocumentManagement/Program.cs) im „DocumentManagement“-Beispiel auf GitHub.

### <a name="how-do-i-perform-transactions-in-the-sql-api"></a>Wie führe ich Transaktionen in der SQL-API aus?

Die SQL-API unterstützt sprachintegrierte Transaktionen über gespeicherte JavaScript-Prozeduren und Trigger. Alle Datenbankvorgänge in Skripts werden im Rahmen der Momentaufnahmeisolation durchgeführt. Wenn es sich um einen Container mit nur einer Partition handelt, wird der Umfang der Ausführung an den Container angepasst. Falls der Container partitioniert wird, wird die Ausführung an Dokumente mit dem gleichen Partitionsschlüsselwert in dem Container angepasst. Eine Momentaufnahme der Dokumentversionen (ETags) wird zu Beginn der Transaktion angefertigt und erst dann festgeschrieben, wenn das Skript erfolgreich ausgeführt wurde. Falls JavaScript einen Fehler ausgibt, wird für die Transaktion ein Rollback ausgeführt. Weitere Informationen finden Sie unter [Serverseitige JavaScript-Programmierung für Azure Cosmos DB](stored-procedures-triggers-udfs.md).

### <a name="how-can-i-bulk-insert-documents-into-cosmos-db"></a>Wie kann ich eine Masseneinfügung für Dokumente in Cosmos DB ausführen?

Sie haben die folgenden Möglichkeiten, um in Azure Cosmos DB die Masseneinfügung für Dokumente auszuführen:

* Das Massenexecutortool, wie unter [Verwenden der Massenexecutor-.NET-Bibliothek](bulk-executor-dot-net.md) und [Verwenden der Massenexecutor-Java-Bibliothek](bulk-executor-java.md) beschrieben.
* Mit dem Datenmigrationstool, wie unter [Datenbankmigrationstool für Azure Cosmos DB](import-data.md) beschrieben.
* Mit serverseitigen Verfahren, wie unter [Serverseitige JavaScript-Programmierung für Azure Cosmos DB](stored-procedures-triggers-udfs.md) beschrieben.

### <a name="does-the-sql-api-support-resource-link-caching"></a>Unterstützt die SQL-API die Zwischenspeicherung von Ressourcenlinks?

Ja. Da es sich bei Azure Cosmos DB um einen RESTful-Dienst handelt, sind Ressourcenlinks unveränderbar und können zwischengespeichert werden. SQL-API-Clients können einen „If-None-Match“-Header für Lesevorgänge für ressourcenähnliche Dokumente oder Container festlegen und dann ihre lokalen Kopien aktualisieren, nachdem die Serverversion geändert wurde.

### <a name="is-a-local-instance-of-sql-api-available"></a>Ist eine lokale Instanz der SQL-API verfügbar?

Ja. Der [Azure Cosmos DB-Emulator](local-emulator.md) stellt eine High-Fidelity-Emulation des Cosmos DB-Diensts bereit. Es werden die gleichen Funktionen wie bei Azure Cosmos DB unterstützt, z.B. Erstellen und Abfragen von JSON-Dokumenten, Bereitstellen und Skalieren von Sammlungen und Ausführen von gespeicherten Prozeduren und Triggern. Sie können Anwendungen mit dem Azure Cosmos DB-Emulator entwickeln und testen und diese in Azure auf globaler Ebene bereitstellen, indem Sie eine einzige Konfigurationsänderung am Verbindungsendpunkt für Azure Cosmos DB vornehmen.

### <a name="why-are-long-floating-point-values-in-a-document-rounded-when-viewed-from-data-explorer-in-the-portal"></a>Warum werden lange Gleitkommawerte in einem Dokument gerundet, wenn sie im Daten-Explorer im Portal angezeigt werden?

Dies ist eine Einschränkung von JavaScript. JavaScript verwendet das Gleitkommazahlenformat mit doppelter Genauigkeit (gemäß Definition in IEEE 754) und kann nur Zahlen zwischen -(2<sup>53</sup> - 1) und 2<sup>53</sup>-1 (d.h. 9007199254740991) enthalten.

### <a name="where-are-permissions-allowed-in-the-object-hierarchy"></a>Wo sind in der Objekthierarchie Berechtigungen zulässig?

Die Erstellung von Berechtigungen mit ResourceTokens ist auf der Containerebene und den untergeordneten Elementen (z.B. Dokumente, Anlagen) zulässig. Hieraus ergibt sich, dass auf Datenbank- oder Kontoebene derzeit keine Berechtigung erstellt werden kann.

[azure-portal]: https://portal.azure.com
[query]: ./sql-query-getting-started.md

## <a name="next-steps"></a>Nächste Schritte

Informationen zu häufig gestellten Fragen anderer APIs finden Sie unter:

* Häufig gestellte Fragen zur [Azure Cosmos DB-API für MongoDB](mongodb-api-faq.md)
* Häufig gestellte Fragen zur [Gremlin-API in Azure Cosmos DB](gremlin-api-faq.md)
* Häufig gestellte Fragen zur [Cassandra-API in Azure Cosmos DB](cassandra-faq.md)
* Häufig gestellte Fragen zur [Tabellen-API in Azure Cosmos DB](table-api-faq.md)