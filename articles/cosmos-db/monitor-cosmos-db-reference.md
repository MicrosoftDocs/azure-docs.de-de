---
title: Referenz zur Überwachung von Daten in Azure Cosmos DB | Microsoft-Dokumentation
description: Hier finden Sie wichtige Referenzmaterialien für die Überwachung von Protokollen und Metriken in Azure Cosmos DB.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: how-to
ms.date: 12/07/2020
ms.author: sngun
ms.custom: subject-monitoring
ms.openlocfilehash: 1b1ff2649f54005eff139b1ed1d4d0b4ea152b1f
ms.sourcegitcommit: 48cb2b7d4022a85175309cf3573e72c4e67288f5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96855021"
---
# <a name="monitoring-azure-cosmos-db-data-reference"></a>Referenz zur Überwachung von Daten in Azure Cosmos DB

[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Dieser Artikel enthält eine Referenz von Protokoll- und Metrikdaten, die gesammelt werden, um die Leistung und Verfügbarkeit von Azure Cosmos DB zu analysieren. Ausführliche Informationen zum Erfassen und Analysieren von Überwachungsdaten für Azure Cosmos DB finden Sie unter [Überwachen von Cosmos DB](monitor-cosmos-db.md).

## <a name="metrics"></a>Metriken

Alle Metriken in Bezug auf Azure Cosmos DB werden im Namespace **Cosmos DB-Standardmetriken** gespeichert. Eine Liste aller von Azure Monitor unterstützten Metriken (einschließlich Azure Cosmos DB) finden Sie unter [Unterstützte Metriken von Azure Monitor](../azure-monitor/platform/metrics-supported.md). In diesem Abschnitt werden alle automatisch erfassten Plattformmetriken aufgeführt, die für Azure Cosmos DB gesammelt werden.  

### <a name="request-metrics"></a>Anforderungsmetriken

|Metrik (Metrikanzeigename)|Einheit (Aggregationstyp) |BESCHREIBUNG|Dimensionen| Zeitgranularitäten| Zuordnung von Legacymetriken | Verwendung |
|---|---|---|---| ---| ---| ---|
| TotalRequests (Anforderungen insgesamt) | Count (Anzahl) | Anzahl von gesendeten Anforderungen| DatabaseName, CollectionName, Region, StatusCode| All | TotalRequests, Http 2xx, Http 3xx, Http 400 "," Http 401, Interner Serverfehler, Dienst nicht verfügbar, Anforderungen gedrosselt, Anforderungen pro Sekunde | Wird verwendet, um Anforderungen nach Statuscode zu überwachen, Container mit einer Granularität von einer Minute. Um einen Durchschnittswert für die Anforderungen pro Sekunde zu erhalten, verwenden Sie die Zähl-Aggregation in einer Minute, und teilen Sie sie durch 60. |
| MetadataRequests (Metadatenanforderungen) |Count (Anzahl) | Anzahl der Metadatenanforderungen. Azure Cosmos DB unterhält einen Container mit Systemmetadaten für jedes Konto, wodurch Sie Sammlungen, Datenbanken usw. und deren Konfigurationen ohne anfallende Kosten auflisten können. | DatabaseName, CollectionName, Region, StatusCode| All| |Wird verwendet, um die Drosselung aufgrund von Metadatenanforderungen zu überwachen.|
| MongoRequests (Mongo-Anforderungen) | Count (Anzahl) | Anzahl der ausgegebenen Mongo-Anforderungen | DatabaseName, CollectionName, Region, CommandName, ErrorCode| All |Mongo-Abfrage-Anforderungsrate, Mongo-Aktualisieren-Anforderungsrate, Mongo-Löschen-Anforderungsrate, Mongo-Einfügen-Anforderungsrate, Mongo-Zählen-Anforderungsrate| Wird verwendet, um Mongo-Anforderungsfehler zu überwachen, Verwendungen pro Befehlstyp. |

### <a name="request-unit-metrics"></a>Metriken für Anforderungseinheiten

|Metrik (Metrikanzeigename)|Einheit (Aggregationstyp)|BESCHREIBUNG|Dimensionen| Zeitgranularitäten| Zuordnung von Legacymetriken | Verwendung |
|---|---|---|---| ---| ---| ---|
| MongoRequestCharge (Kosten der Mongo-Anforderung) | Count (Gesamt) |Verbrauchte Mongo-Anforderungseinheiten| DatabaseName, CollectionName, Region, CommandName, ErrorCode| All |Mongo-Abfrage-Anforderungsgebühr, Mongo-Aktualisieren-Anforderungsgebühr, Mongo-Löschen-Anforderungsgebühr, Mongo-Einfügen-Anforderungsgebühr, Mongo-Zählen-Anforderungsgebühr| Wird verwendet, um die Mongo-Ressourcen-RUs in einer Minute zu überwachen.|
| TotalRequestUnits (Anforderungseinheiten gesamt)| Count (Gesamt) | Verbrauchte Anforderungseinheiten| DatabaseName, CollectionName, Region, StatusCode |All| TotalRequestUnits| Wird verwendet, um die gesamte RU-Nutzung mit einer Granularität von einer Minute zu überwachen. Um einen Durchschnittswert für die verbrauchten RU zu erhalten, verwenden Sie die Gesamtaggregation in einer Minute, und teilen Sie sie durch 60.|
| ProvisionedThroughput (Bereitgestellter Durchsatz)| Count (Maximum) |Bereitgestellter Durchsatz bei Containergranularität| DatabaseName, ContainerName| 5M| | Wird verwendet, um den bereitgestellten Durchsatz pro Container zu überwachen.|

### <a name="storage-metrics"></a>Speichermetrik

|Metrik (Metrikanzeigename)|Einheit (Aggregationstyp)|BESCHREIBUNG|Dimensionen| Zeitgranularitäten| Zuordnung von Legacymetriken | Verwendung |
|---|---|---|---| ---| ---| ---|
| AvailableStorage (Verfügbarer Speicher) |Bytes (Gesamt) | Gemeldeter Gesamtspeicher mit 5-Minuten-Granularität pro Region| DatabaseName, CollectionName, Region| 5M| Verfügbarer Speicher| Wird verwendet, um die verfügbare Speicherkapazität zu überwachen (gilt nur für feste Speichersammlungen). Die Mindestgranularität sollte 5 Minuten betragen.| 
| DataUsage (Datennutzung) |Bytes (Gesamt) |Gemeldete Gesamtdatennutzung mit 5-Minuten-Granularität pro Region| DatabaseName, CollectionName, Region| 5M |Datengröße | Wird verwendet, um den gesamten Datenverbrauch für Container und Region zu überwachen. Die Mindestgranularität sollte 5 Minuten betragen.|
| IndexUsage (Indexnutzung) | Bytes (Gesamt) |Gemeldete Gesamtindexnutzung mit 5-Minuten-Granularität pro Region| DatabaseName, CollectionName, Region| 5M| Indexgröße| Wird verwendet, um den gesamten Datenverbrauch für Container und Region zu überwachen. Die Mindestgranularität sollte 5 Minuten betragen. |
| DocumentQuota (Dokumentenkontingent) | Bytes (Gesamt) | Gemeldetes Gesamtspeicherkontingent mit 5-Minuten-Granularität pro Region| DatabaseName, CollectionName, Region| 5M |Speicherkapazität| Wird verwendet, um das gesamte Kontingent für Container und Region zu überwachen. Die Mindestgranularität sollte 5 Minuten betragen.|
| DocumentCount (Dokumentanzahl) | Count (Gesamt) |Gemeldete Gesamtdokumentanzahl mit 5-Minuten-Granularität pro Region| DatabaseName, CollectionName, Region| 5M |Dokumentanzahl|Wird verwendet, um das Gesamtanzahl der Dokumente für Container und Region zu überwachen. Die Mindestgranularität sollte 5 Minuten betragen.|

### <a name="latency-metrics"></a>Latenzmetriken

|Metrik (Metrikanzeigename)|Einheit (Aggregationstyp)|BESCHREIBUNG|Dimensionen| Zeitgranularitäten| Verwendung |
|---|---|---|---| ---| ---|
| ReplicationLatency (Replikationswartezeit)| MilliSeconds (Minimum, Maximum, Durchschnitt) | P99-Replikationswartezeit für Quell- und Zielregionen für geofähiges Konto| SourceRegion, TargetRegion| All | Wird verwendet, um die P99-Replikationswartezeit zwischen zwei beliebigen Regionen für ein georepliziertes Konto zu überwachen. |
| Serverseitige Latenz| Millisekunden (Durchschnitt) | Die Zeit, die der Server für die Verarbeitung der Anforderung benötigt. | CollectionName, ConnectionMode, DatabaseName, OperationType, PublicAPIType, Region | All | Wird verwendet, um die Wartezeit der Anforderungen auf dem Azure Cosmos DB-Server zu überwachen. |

### <a name="availability-metrics"></a>Verfügbarkeitsmetriken

|Metrik (Metrikanzeigename) |Einheit (Aggregationstyp)|BESCHREIBUNG| Zeitgranularitäten| Zuordnung von Legacymetriken | Verwendung |
|---|---|---|---| ---| ---|
| ServiceAvailability (Dienstverfügbarkeit)| Prozent (Minimum, Maximum) | Verfügbarkeit der Kontoanforderungen mit einer Granularität von einer Stunde| 1H | Dienstverfügbarkeit | Stellt den Prozentsatz der insgesamt übergebenen Anforderungen dar. Eine Anforderung gilt als fehlgeschlagen aufgrund eines Systemfehlers, wenn der Statuscode 410, 500 oder 503 lautet. Wird verwendet, um die Verfügbarkeit des Kontos bei einer Granularität von einer Stunde zu überwachen. |

### <a name="cassandra-api-metrics"></a>Cassandra-API-Metriken

|Metrik (Metrikanzeigename)|Einheit (Aggregationstyp)|BESCHREIBUNG|Dimensionen| Zeitgranularitäten| Verwendung |
|---|---|---|---| ---| ---|
| CassandraRequests (Cassandra-Anforderungen) | Count (Anzahl) | Anzahl der ausgeführten Cassandra-API-Anforderungen| DatabaseName, CollectionName, ErrorCode, Region, OperationType, ResourceType| All| Wird verwendet, um Cassandra-Anforderungen mit einer Granularität von einer Minute zu überwachen. Um einen Durchschnittswert für die Anforderungen pro Sekunde zu erhalten, verwenden Sie die Zähl-Aggregation in einer Minute, und teilen Sie sie durch 60.|
| CassandraRequestCharges (Gebühren für Cassandra-Anforderungen) | Count (Summe, Minimum, Maximum, Durchschnitt) | Von der Cassandra-API verbrauchte Anforderungseinheiten | DatabaseName, CollectionName, Region, OperationType, ResourceType| All| Wird verwendet, um die RUs zu überwachen, die pro Minute von einem Cassandra API-Konto genutzt werden.|
| CassandraConnectionClosures (Abschluss von Cassandra-Verbindungen) |Count (Anzahl) |Anzahl der geschlossenen Cassandra-Verbindungen| ClosureReason, Region| All | Wird verwendet, um die Verbindung zwischen Clients und der Cassandra-API von Azure Cosmos DB zu überwachen.|

Weitere Informationen finden Sie in der Liste [aller in Azure Monitor unterstützten Plattformmetriken](../azure-monitor/platform/metrics-supported.md).

## <a name="resource-logs"></a>Ressourcenprotokolle

In der folgenden Tabelle sind die Eigenschaften der Ressourcenprotokolle in Azure Cosmos DB aufgeführt. Die Ressourcenprotokolle werden in Azure Monitor-Protokollen oder in Azure Storage erfasst. In Azure Monitor werden Protokolle in der Tabelle **AzureDiagnostics** unter dem Ressourcenanbieternamen** `MICROSOFT.DOCUMENTDB` erfasst.

| Azure Storage-Feld oder -Eigenschaft | Eigenschaft von Azure Monitor-Protokolle | BESCHREIBUNG |
| --- | --- | --- |
| **time** | **TimeGenerated** | Datum und Uhrzeit (UTC), zu denen der Vorgang aufgetreten ist. |
| **Ressourcen-ID** | **Ressource** | Das Azure Cosmos DB-Konto, für das Protokolle aktiviert sind.|
| **category** | **Kategorie** | Die verfügbaren Protokolltypen bei Azure Cosmos DB sind: **DataPlaneRequests**, **MongoRequests**, **QueryRuntimeStatistics**, **PartitionKeyStatistics**, **PartitionKeyRUConsumption** und **ControlPlaneRequests**. |
| **operationName** | **OperationName** | Name des Vorgangs. Der Vorgangsname kann `Create`, `Update`, `Read`, `ReadFeed`, `Delete`, `Replace`, `Execute`, `SqlQuery`, `Query`, `JSQuery`, `Head`, `HeadFeed` oder `Upsert` sein.   |
| **properties** | – | Die Inhalte dieser Felder werden in den folgenden Zeilen beschrieben. |
| **activityId** | **activityId_g** | Die eindeutige GUID für den protokollierten Vorgang. |
| **userAgent** | **userAgent_s** | Eine Zeichenfolge, die den Clientbenutzer-Agent des Clients angibt, von dem die Anforderung gesendet wurde. Der Benutzer-Agent hat das Format `{user agent name}/{version}`.|
| **requestResourceType** | **requestResourceType_s** | Der Typ der Ressource, auf die zugegriffen wird. Dieser Wert kann eine Datenbank, ein Container, ein Dokument, eine Anlage, ein Benutzer, eine Berechtigung, eine gespeicherte Prozedur, ein Trigger, eine benutzerdefinierte Funktion oder ein Angebot sein. |
| **statusCode** | **statusCode_s** | Der Antwortstatus des Vorgangs. |
| **requestResourceId** | **ResourceId** | Die Ressourcen-ID der Anforderung. Abhängig vom ausgeführten Vorgang kann dieser Wert auf `databaseRid`, `collectionRid` oder `documentRid` verweisen.|
| **clientIpAddress** | **clientIpAddress_s** | Die IP-Adresse des Clients. |
| **requestCharge** | **requestCharge_s** | Die Anzahl der vom Vorgang verwendeten RU/s |
| **collectionRid** | **collectionId_s** | Die eindeutige ID für die Sammlung.|
| **duration** | **duration_d** | Die Dauer des Vorgangs in Millisekunden. |
| **requestLength** | **requestLength_s** | Die Länge der Anforderung in Bytes. |
| **responseLength** | **responseLength_s** | Die Länge der Antwort in Bytes.|
| **resourceTokenPermissionId** | **resourceTokenPermissionId_s** | Diese Eigenschaft gibt die festgelegte Ressourcentokenberechtigungs-ID an. Weitere Informationen zu Berechtigungen finden Sie im Artikel [Schützen des Zugriffs auf Ihre Daten](./secure-access-to-data.md#permissions). |
| **resourceTokenPermissionMode** | **resourceTokenPermissionMode_s** | Diese Eigenschaft gibt den Berechtigungsmodus an, den Sie beim Erstellen des Ressourcentokens festgelegt haben. Der Berechtigungsmodus kann Werte wie „Alle“ oder „Lesen“ aufweisen. Weitere Informationen zu Berechtigungen finden Sie im Artikel [Schützen des Zugriffs auf Ihre Daten](./secure-access-to-data.md#permissions). |
| **resourceTokenUserRid** | **resourceTokenUserRid_s** | Dieser Wert ist nicht leer, wenn [Ressourcentoken](./secure-access-to-data.md#resource-tokens) für die Authentifizierung verwendet werden. Der Wert verweist auf die Ressourcen-ID des Benutzers. |
| **responseLength** | **responseLength_s** | Die Länge der Antwort in Bytes.|

Eine Liste aller Azure Monitor-Protokollkategorien und Links zu zugeordneten Schemas finden Sie unter [Azure Monitor Logs categories and schemas](../azure-monitor/platform/resource-logs-schema.md) (Kategorien und Schemas in Azure Monitor-Protokolle). 

## <a name="azure-monitor-logs-tables"></a>Tabellen in Azure Monitor-Protokollen

Azure Cosmos DB verwendet Kusto-Tabellen aus Azure Monitor-Protokollen. Sie können diese Tabellen mit Log Analytics abfragen. Eine Liste der Einsatzzwecke von Kusto-Paketen finden Sie im Artikel [Tabellenverweise für Azure Monitor-Protokolle](/azure-monitor/reference/tables/tables-resourcetype#azure-cosmos-db).

## <a name="see-also"></a>Weitere Informationen

- Eine Beschreibung der Überwachung von Azure Cosmos DB finden Sie unter [Überwachen von Azure Cosmos DB](monitor-cosmos-db.md).
- Ausführliche Informationen zur Überwachung von Azure-Ressourcen finden Sie unter [Überwachen von Azure-Ressourcen mit Azure Monitor](../azure-monitor/insights/monitor-azure-resource.md).