---
title: Häufig gestellte Fragen zur Tabellen-API in Azure Cosmos DB
description: Hier finden Sie Antworten auf häufig gestellte Fragen zur Tabellen-API in Azure Cosmos DB.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: conceptual
ms.date: 08/12/2020
ms.author: sngun
ms.openlocfilehash: c5404c026385f9ebf06c4f0289a4db0bf944d172
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93341233"
---
# <a name="frequently-asked-questions-about-the-table-api-in-azure-cosmos-db"></a>Häufig gestellte Fragen zur Tabellen-API in Azure Cosmos DB
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

Die Tabellen-API von Azure Cosmos DB steht im [Azure-Portal](https://portal.azure.com) zur Verfügung. Sie müssen sich zunächst für ein Azure-Abonnement registrieren. Nach der Registrierung können Sie Ihrem Azure-Abonnement ein Azure Cosmos DB-Table-API-Konto und Ihrem Konto anschließend Tabellen hinzufügen. Die unterstützten Sprachen und entsprechenden Schnellstartanleitungen finden Sie in der [Einführung in die Table-API von Azure Cosmos DB](table-introduction.md).

## <a name="table-api-in-azure-cosmos-db-vs-azure-table-storage"></a><a id="table-api-vs-table-storage"></a>Vergleich von Tabellen-API in Azure Cosmos DB und Azure Table Storage

### <a name="where-is-table-api-not-identical-with-azure-table-storage-behavior"></a>Inwiefern unterscheidet sich das Verhalten von Azure Table Storage von der Table-API?

Benutzer, die mit Azure Table Storage vertraut sind und Tabellen mit der Table-API von Azure Cosmos DB erstellen möchten, müssen sich auf einige Unterschiede beim Verhalten einstellen:

* Die Table-API von Azure Cosmos DB verwendet zur Gewährleistung der garantierten Leistung ein Modell mit reservierter Kapazität. Das bedeutet allerdings, dass für die Kapazität Kosten anfallen, sobald die Tabelle erstellt wird, auch wenn die Kapazität gar nicht genutzt wird. Bei Azure Table Storage wird dagegen nur die genutzte Kapazität in Rechnung gestellt. Das erklärt, warum bei der Tabellen-API eine SLA mit einer Lesegeschwindigkeit von 10 ms und einer Schreibgeschwindigkeit von 15 ms im 99. Perzentil möglich ist, während Azure Table Storage nur eine SLA mit 10 Sekunden bietet. Bei Table-API-Tabellen fallen dafür jedoch auch Kosten für leere Tabellen ohne jegliche Anforderungen an, um sicherzustellen, dass gemäß der SLA von Azure Cosmos DB die erforderliche Kapazität für mögliche Anforderungen an die Tabellen zur Verfügung steht.

* Von der Tabellen-API zurückgegebene Abfrageergebnisse werden nicht wie bei Azure Table Storage nach Partitionsschlüssel/Zeilenschlüssel sortiert.

* Die Größe von Zeilenschlüsseln ist auf 255 Bytes begrenzt.

* Batches können nur bis zu 2 MB enthalten.

* CORS wird derzeit nicht unterstützt.

* Bei Tabellennamen in Azure Table Storage wird die Groß- und Kleinschreibung nicht berücksichtigt, bei der Tabellen-API von Azure Cosmos DB dagegen schon.

* Einige der internen Azure Cosmos DB-Formate für Codierungsinformationen (etwa binäre Felder) sind derzeit nicht so wie effizient wie möglicherweise gewünscht. Daher können unerwartete Einschränkungen in Bezug auf die Datengröße auftreten. Beispiel: Derzeit kann zum Speichern der Binärdaten die Kapazität einer Tabellenentität von 1 MB nicht vollständig genutzt werden, da die Codierung den Datenumfang vergrößert.

* Der Entitätseigenschaftenname „ID“ wird derzeit nicht unterstützt.

* „TakeCount“ von „TableQuery“ ist nicht auf 1.000 beschränkt.

* Bei der REST-API werden einige Endpunkte/Abfrageoptionen von der Tabellen-API von Azure Cosmos DB nicht unterstützt:

  | REST-Methode | REST-Endpunkt/-Abfrageoption | Dokumentations-URLs | Erklärung |
  | ------------| ------------- | ---------- | ----------- |
  | GET, PUT | `/?restype=service@comp=properties`| [Set Table Service Properties](/rest/api/storageservices/set-table-service-properties) (Festlegen von Tabellendiensteigenschaften) und [Get Table Service Properties](/rest/api/storageservices/get-table-service-properties) (Abrufen von Tabellendiensteigenschaften) | Dieser Endpunkt dient zum Festlegen von CORS-Regeln, Storage Analytics-Konfiguration und Protokollierungseinstellungen. CORS wird derzeit nicht unterstützt. Analyse und Protokollierung werden in Azure Cosmos DB anders behandelt als in Azure Storage-Tabellen. |
  | OPTIONS | `/<table-resource-name>` | [Pre-flight CORS table request](/rest/api/storageservices/preflight-table-request) (Preflight-CORS-Tabellenanforderung) | Dies ist Teil von CORS und wird daher derzeit von Azure Cosmos DB nicht unterstützt. |
  | GET | `/?restype=service@comp=stats` | [Get Table Service Stats](/rest/api/storageservices/get-table-service-stats) (Abrufen der Tabellendienststatistik) | Gibt Aufschluss über die Replikationsgeschwindigkeit von Daten zwischen primärer Datenbank und sekundären Datenbanken. Wird in Cosmos DB nicht benötigt, da hier die Replikation Teil der Schreibvorgänge ist. |
  | GET, PUT | `/mytable?comp=acl` | [Get Table ACL](/rest/api/storageservices/get-table-acl) (Tabellen-ACL abrufen) und [Set Table ACL](/rest/api/storageservices/set-table-acl) (Tabellen-ACL festlegen) | Dient zum Abrufen und Festlegen der gespeicherten Zugriffsrichtlinien, die für die Verwaltung von Shared Access Signatures (SAS) verwendet werden. SAS werden zwar unterstützt, aber unterschiedlich festgelegt und verwaltet. |

* Die Tabellen-API von Azure Cosmos DB unterstützt nur das JSON-Format und nicht das ATOM-Format.

* Azure Cosmos DB unterstützt zwar Shared Access Signatures (SAS), bestimmte Richtlinien werden jedoch nicht unterstützt. Hierzu zählen insbesondere Richtlinien im Zusammenhang mit Verwaltungsvorgängen (etwa das Recht zum Erstellen neuer Tabellen).

* Beim .NET SDK werden einige Klassen und Methoden derzeit nicht von Azure Cosmos DB unterstützt.

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

## <a name="other-frequently-asked-questions"></a>Weitere häufig gestellte Fragen

### <a name="do-i-need-a-new-sdk-to-use-the-table-api"></a>Benötige ich für die Verwendung der Table-API ein neues SDK?

Nein. Vorhandene Storage SDKs sollten auch weiterhin funktionieren. Es empfiehlt sich allerdings immer, die neuesten SDKs zu verwenden, um eine optimale Unterstützung zu gewährleisten und in vielen Fällen eine bessere Leistung zu erzielen. Eine Liste mit den verfügbaren Sprachen finden Sie in der [Einführung in die Table-API von Azure Cosmos DB](table-introduction.md).

### <a name="what-is-the-connection-string-that-i-need-to-use-to-connect-to-the-table-api"></a>Welche Verbindungszeichenfolge muss ich zum Herstellen einer Verbindung mit der Table-API verwenden?

Die Verbindungszeichenfolge lautet:

```
DefaultEndpointsProtocol=https;AccountName=<AccountNamefromCosmosDB;AccountKey=<FromKeysPaneofCosmosDB>;TableEndpoint=https://<AccountName>.table.cosmosdb.azure.com
```

Die Verbindungszeichenfolge können Sie im Azure-Portal auf der Seite „Verbindungszeichenfolge“ ermitteln.

### <a name="how-do-i-override-the-config-settings-for-the-request-options-in-the-net-sdk-for-the-table-api"></a>Wie setze ich die Konfigurationseinstellungen für die Anforderungsoptionen im .NET SDK für die Table-API außer Kraft?

Einige Einstellungen werden über die CreateCloudTableClient-Methode behandelt, andere über „app.config“ im appSettings-Abschnitt der Clientanwendung. Informationen zu Konfigurationseinstellungen finden Sie unter [Azure Cosmos DB-Funktionen](tutorial-develop-table-dotnet.md).

### <a name="are-there-any-changes-for-customers-who-are-using-the-existing-azure-table-storage-sdks"></a>Müssen Kunden, die die vorhandenen Azure Table Storage SDKs verwenden, Änderungen vornehmen?

Keine. Für Bestands- oder Neukunden, die die vorhandenen Azure Table Storage-SDKs verwenden, ergeben sich keine Änderungen.

### <a name="how-do-i-view-table-data-thats-stored-in-azure-cosmos-db-for-use-with-the-table-api"></a>Wie zeige ich in Azure Cosmos DB gespeicherte Tabellendaten für die Verwendung mit der Tabellen-API an?

Sie können das Azure-Portal verwenden, um die Daten zu durchsuchen. Außerdem können Sie den Code der Table-API oder die in der nächsten Antwort erwähnten Tools verwenden.

### <a name="which-tools-work-with-the-table-api"></a>Welche Tools kann ich mit der Table-API verwenden?

Sie können den [Azure Storage-Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) verwenden.

Tools, die eine Verbindungszeichenfolge im weiter oben angegebenen Format akzeptieren, können die neue Table-API unterstützen. Eine Liste mit Tabellentools steht auf der Seite [Azure Storage-Clienttools](../storage/common/storage-explorers.md) zur Verfügung.

### <a name="is-the-concurrency-on-operations-controlled"></a>Ist die Parallelität im Betrieb gesteuert?

Ja. Die optimistische Nebenläufigkeit wird über den Einsatz des ETag-Mechanismus bereitgestellt.

### <a name="is-the-odata-query-model-supported-for-entities"></a>Wird das OData-Abfragemodell für Entitäten unterstützt?

Ja. Die Table-API unterstützt OData-Abfragen und LINQ-Abfragen.

### <a name="can-i-connect-to-azure-table-storage-and-azure-cosmos-db-table-api-side-by-side-in-the-same-application"></a>Kann in einer Anwendung gleichzeitig eine Verbindung mit Azure Table Storage und mit der Table-API von Azure Cosmos DB hergestellt werden?

Ja. Sie können Verbindungen herstellen, indem Sie zwei separate CloudTableClient-Instanzen erstellen, die über die Verbindungszeichenfolge jeweils auf ihren eigenen URI verweisen.

### <a name="how-do-i-migrate-an-existing-azure-table-storage-application-to-this-offering"></a>Wie migriere ich eine bereits vorhandene Azure Table Storage-Anwendung zu diesem Angebot?

Hierzu können Sie [AzCopy](../storage/common/storage-use-azcopy-v10.md) oder das [Migrationstool für Azure Cosmos DB-Daten](import-data.md) verwenden.

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

Die Table-API bietet die gleichen Abfragefunktionen wie Azure Table Storage. Azure Cosmos DB unterstützt auch Sortieren, Aggregieren, räumliche Abfrage, Hierarchie und eine Vielzahl von integrierten Funktionen. Weitere Informationen finden Sie unter [SQL-Abfragen](./sql-query-getting-started.md).

### <a name="when-should-i-change-tablethroughput-for-the-table-api"></a>Wann sollte ich „TableThroughput“ für die Table-API ändern?

Sie sollten TableThroughput ändern, wenn eine der folgenden Bedingungen gilt:

* Sie führen das Extrahieren, Transformieren und Laden (ETL) für die Daten durch, oder Sie möchten innerhalb eines kurzen Zeitraums viele Daten hochladen.
* Sie benötigen für den Container oder mehrere Container auf dem Back-End mehr Durchsatz. Beispielsweise erkennen Sie, dass der genutzte Durchsatz den bereitgestellten Durchsatz übersteigt, und es kommt zu einer Drosselung. Weitere Informationen finden Sie unter [Festlegen von Durchsatz für Azure Cosmos-Container](set-throughput.md).

### <a name="can-i-scale-up-or-scale-down-the-throughput-of-my-table-api-table"></a>Kann ich den Durchsatz meiner Table-API-Tabelle hoch- oder herunterskalieren?

Ja. Sie können den Skalierungsbereich des Azure Cosmos DB-Portals verwenden, um den Durchsatz zu skalieren. Weitere Informationen finden Sie unter [Festlegen von Durchsatz für Azure Cosmos DB-Container](set-throughput.md).

### <a name="is-a-default-tablethroughput-set-for-newly-provisioned-tables"></a>Wird für neu bereitgestellte Tabellen ein TableThroughput-Standardwert festgelegt?

Ja. Wenn Sie TableThroughput nicht mithilfe von „app.config“ außer Kraft setzen und keinen vorkonfigurierten Container in Azure Cosmos DB verwenden, erstellt der Dienst eine Tabelle mit einem Durchsatz von 400.

### <a name="is-there-any-change-of-pricing-for-existing-customers-of-the-azure-table-storage-service"></a>Müssen Bestandskunden des Azure Table Storage-Diensts mit Preisänderungen rechnen?

Keine. Es gibt keine Preisänderungen für Bestandskunden von Azure Table Storage.

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

Azure Cosmos DB ist ein SLA-basiertes System, das unbeschränkte Skalierung mit Garantien für Wartezeit, Durchsatz, Verfügbarkeit und Konsistenz bietet. Achten Sie darauf, dass Ihre Datengröße und der Index verwaltbar und skalierbar sind, um sicherzustellen, dass Sie die garantierte Premium-Leistung erhalten. Mit dem 20 GB-Grenzwert für die Anzahl von Entitäten bzw. Elementen pro Partitionsschlüssel wird dafür gesorgt, dass wir eine hervorragende Such- und Abfrageleistung bieten können. Um sicherzustellen, dass Ihre Anwendung gut skaliert werden kann, raten wir Ihnen auch bei Azure Storage, *keine* Hot Partition zu erstellen, indem Sie alle Informationen in einer Partition speichern und abfragen.

### <a name="so-partitionkey-and-rowkey-are-still-required-with-the-table-api"></a>Also benötige ich „PartitionKey“ und „RowKey“ auch bei der Table-API?

Ja. Da die Oberfläche der Table-API der Oberfläche des Azure Table Storage SDKs ähnelt, ermöglicht der Partitionsschlüssel eine effiziente Datenverteilung. Der Zeilenschlüssel ist innerhalb dieser Partition eindeutig. Der Zeilenschlüssel muss vorhanden sein und darf nicht wie beim Standard-SDK NULL sein. Die RowKey-Länge beträgt 255 Bytes. Die PartitionKey-Länge beträgt 1 KB.

### <a name="what-are-the-error-messages-for-the-table-api"></a>Welche Fehlermeldungen gibt es für die Table-API?

Da Azure Table Storage und die Table-API von Azure Cosmos DB die gleichen SDKs verwenden, sind auch die Fehler größtenteils gleich.

### <a name="why-do-i-get-throttled-when-i-try-to-create-lot-of-tables-one-after-another-in-the-table-api"></a>Warum kommt es zu einer Drosselung, wenn ich versuche, über die Table-API nacheinander eine große Anzahl von Tabellen zu erstellen?

Azure Cosmos DB ist ein SLA-basiertes System mit Garantien für Wartezeit, Durchsatz, Verfügbarkeit und Konsistenz. Da es sich um ein bereitgestelltes System handelt, werden Ressourcen reserviert, um diese Anforderungen zu garantieren. Eine hohe Erstellungsrate von Tabellen in rascher Folge wird erkannt und gedrosselt. Es wird empfohlen, dass Sie sich die Erstellungsrate der Tabellen ansehen und auf weniger als „5 pro Minute“ reduzieren. Bedenken Sie, dass es sich bei der Table-API um ein bereitgestelltes System handelt. Ab dem Moment, in dem Sie es bereitstellen, werden Gebühren fällig.

### <a name="how-do-i-provide-feedback-about-the-sdk-or-bugs"></a>Wie kann ich Feedback zum SDK und zu Bugs geben?

Sie können Ihr Feedback wie folgt mitteilen:

* [Uservoice](https://feedback.azure.com/forums/263030-azure-cosmos-db)
* [Frageseite von Microsoft Q&A (Fragen und Antworten)](/answers/topics/azure-cosmos-db.html)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-cosmosdb). Stack Overflow eignet sich am besten für Fragen zur Programmierung. Vergewissern Sie sich, dass Ihre Frage [themenbezogen](https://stackoverflow.com/help/on-topic) ist und [so viele Details wie möglich enthält, damit sie klar und beantwortbar ist](https://stackoverflow.com/help/how-to-ask).

## <a name="next-steps"></a>Nächste Schritte

* [Erstellen einer Tabellen-API-App per .NET SDK und Azure Cosmos DB](create-table-dotnet.md)
* [Erstellen einer Java-App zum Verwalten von Tabellen-API-Daten in Azure Cosmos DB](create-table-java.md)