---
title: Indexerzugriff auf geschützte Ressourcen
titleSuffix: Azure Cognitive Search
description: Konzeptionelle Übersicht über die Sicherheitsoptionen auf Netzwerkebene für den Azure-Datenzugriff durch Indexer in Azure Cognitive Search.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: bcb6e91bba367363385214806077146b1a24fe7b
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2020
ms.locfileid: "92503486"
---
# <a name="indexer-access-to-content-protected-by-azure-network-security-features-azure-cognitive-search"></a>Indexerzugriff auf Inhalte, die durch Azure-Netzwerksicherheitsfeatures geschützt sind (Azure Cognitive Search)

Azure Cognitive Search-Indexer können während der Ausführung ausgehende Aufrufe an verschiedene Azure-Ressourcen richten. In diesem Artikel werden die Konzepte hinter dem Indexerzugriff auf Inhalt erläutert, der durch IP-Firewalls, private Endpunkte oder andere Azure-Sicherheitsmechanismen auf Netzwerkebene geschützt werden. Ein Indexer tätigt in zwei Situationen ausgehende Aufrufe: Verbindung zu Datenquellen während der Indexierung und Verbindung zu gekapseltem Code über ein Skillset. Eine Liste aller möglichen Ressourcentypen, auf die ein Indexer bei normaler Ausführung zugreifen könnte, ist in der folgenden Tabelle aufgeführt.

| Resource | Zweck innerhalb der Indexerausführung |
| --- | --- |
| Azure Storage (Blobs, Tabellen, ADLS Gen 2) | Datenquelle |
| Azure Storage (Blobs, Tabellen) | Skillsets (Zwischenspeichern von angereicherten Dokumenten und Speichern von Wissensspeicherprojektionen) |
| Azure Cosmos DB (verschiedene APIs) | Datenquelle |
| Azure SQL-Datenbank | Datenquelle |
| SQL Server auf virtuellen Azure-Computern | Datenquelle |
| Verwaltete SQL-Instanz | Datenquelle |
| Azure-Funktionen | Host für „Benutzerdefinierte Web-API“-Qualifikationen |
| Cognitive Services | Dem Skillset angefügt, das zum Abrechnen der Anreicherung über das Limit der 20 kostenlosen Dokumente hinaus verwendet wird |

> [!NOTE]
> Die einem Skillset angefügte Cognitive Service-Ressource wird für die Abrechnung auf Basis der im Suchindex ausgeführten und dort hinein geschriebenen Anreicherungen verwendet. Sie wird nicht für den Zugriff auf die Cognitive Services-APIs verwendet. Der Zugriff eines Indexers aus der Anreicherungspipeline auf Cognitive Services-APIs erfolgt über einen internen sicheren Kommunikationskanal, bei dem Daten während der Übertragung stark verschlüsselt und nie im Ruhezustand gespeichert werden.

Kunden können diese Ressourcen über verschiedene von Azure angebotene Netzwerkisolations-Mechanismen sichern. Mit Ausnahme der Cognitive Service-Ressource haben Indexer auch dann eingeschränkten Zugriff auf alle anderen Ressourcen, wenn sie wie in der folgenden Tabelle aufgeführt netzwerkisoliert sind.

| Resource | IP-Einschränkung | Privater Endpunkt |
| --- | --- | ---- |
| Azure Storage (Blobs, Tabellen, ADLS Gen 2) | Wird nur unterstützt, wenn sich das Speicherkonto und der Suchdienst in unterschiedlichen Regionen befinden | Unterstützt |
| Azure Cosmos DB – SQL-API | Unterstützt | Unterstützt |
| Azure Cosmos DB – Cassandra-, Mongo- und Gremlin-API | Unterstützt | Nicht unterstützt |
| Azure SQL-Datenbank | Unterstützt | Unterstützt |
| SQL Server auf virtuellen Azure-Computern | Unterstützt | – |
| Verwaltete SQL-Instanz | Unterstützt | – |
| Azure-Funktionen | Unterstützt | Nur für bestimmte Ebenen von Azure-Funktionen unterstützt |

> [!NOTE]
> Zusätzlich zu den oben aufgeführten Optionen können Kunden für netzwerkgesicherte Azure-Speicherkonten die Tatsache nutzen, dass Azure Cognitive Search ein [vertrauenswürdiger Microsoft-Dienst](../storage/common/storage-network-security.md#trusted-microsoft-services) ist. Dies bedeutet, dass ein bestimmter Suchdienst das virtuelle Netzwerk oder IP-Einschränkungen für das Speicherkonto umgehen und auf Daten im Speicherkonto zugreifen kann, wenn die entsprechende rollenbasierte Zugriffssteuerung für das Speicherkonto aktiviert ist. Weitere Informationen finden Sie unter [Indexerverbindungen mithilfe der Ausnahme für vertrauenswürdige Dienste](search-indexer-howto-access-trusted-service-exception.md). Diese Option kann anstelle der IP-Einschränkungsroute verwendet werden, wenn das Speicherkonto oder der Suchdienst nicht in eine andere Region verschoben werden kann.

Beachten Sie bei der Auswahl des sicheren Zugriffsmechanismus, den ein Indexer verwenden sollte, die folgenden Einschränkungen:

- Ein Indexer kann keine Verbindung mit einem [virtuellen Netzwerkdienst-Endpunkt](../virtual-network/virtual-network-service-endpoints-overview.md) herstellen. Öffentliche Endpunkte mit Anmeldeinformationen, private Endpunkte, ein vertrauenswürdiger Dienst und IP-Adressierung sind die einzigen unterstützten Methoden für Indexerverbindungen.
- Ein Suchdienst kann nicht in einem spezifischen virtuellen Netzwerk bereitgestellt werden, das nativ auf einem virtuellen Computer ausgeführt wird. Diese Funktion wird von Azure Cognitive Search nicht angeboten.
- Wenn Indexer (ausgehende) private Endpunkte für den Zugriff auf Ressourcen verwenden, fallen möglicherweise zusätzliche [Private Link-Gebühren](https://azure.microsoft.com/pricing/details/search/) an.

## <a name="indexer-execution-environment"></a>Indexerausführungsumgebung

Azure Cognitive Search-Indexer können effizient Inhalt aus Datenquellen extrahieren, dem extrahierten Inhalt Anreicherungen hinzufügen und optional Projektionen erzeugen, bevor die Ergebnisse in den Suchindex geschrieben werden. Abhängig von der Anzahl der Zuständigkeiten, die einem Indexer zugewiesen sind, kann er in einer von zwei Umgebungen ausgeführt werden:

- Eine Umgebung, die für einen bestimmten Suchdienst privat ist. In solchen Umgebungen ausgeführte Indexer teilen Ressourcen mit anderen Workloads (z. B. anderen vom Kunden initiierten Indizierungen oder Abfragen der Workloads). In der Regel werden nur Indexer, die textbasierte Indizierung ausführen (z. B. kein Skillset verwenden), in dieser Umgebung ausgeführt.
- Eine Umgebung mit mehreren Mandanten, die ressourcenintensive Indexer hostet, wie z. B. solche mit Skillsets. Diese Umgebung wird verwendet, um rechenintensive Verarbeitungen auszulagern, sodass dienstspezifische Ressourcen für Routineoperationen verfügbar bleiben. Diese mehrinstanzenfähige Umgebung wird von Microsoft ohne zusätzliche Kosten für den Kunden verwaltet und gesichert.

Für eine bestimmte Indexerausführung bestimmt Azure Cognitive Search die beste Umgebung. Wenn Sie eine IP-Firewall verwenden, um den Zugriff auf Azure-Ressourcen zu steuern, helfen Ihnen die Kenntnisse über Ausführungsumgebungen dabei, einen IP-Adressbereich einzurichten, der beides einschließt.

## <a name="granting-access-to-indexer-ip-ranges"></a>Gewähren von Zugriff auf die Indexer-IP-Adressbereiche

Wenn die Ressource, auf die Ihr Indexer zugreifen möchte, auf eine bestimmte Gruppe von IP-Adressbereichen beschränkt ist, müssen Sie die Gruppe so erweitern, dass sie die möglichen IP-Adressbereiche einschließt, aus denen eine Indexeranforderung kommen kann. Wie bereits erwähnt, gibt es zwei mögliche Umgebungen, in denen Indexer ausgeführt werden und aus denen Zugriffsanforderungen kommen können. Sie müssen die IP-Adressen **beider** Umgebungen hinzufügen, damit der Indexerzugriff funktioniert.

- Zum Abrufen der IP-Adresse der jeweiligen privaten Umgebung des Suchdiensts führen Sie ein `nslookup` (oder `ping`) für den vollqualifizierten Domänennamen (FQDN) des Suchdiensts durch. Der FQDN eines Suchdiensts in der öffentlichen Cloud wäre z. B. `<service-name>.search.windows.net`. Diese Informationen finden Sie im Azure-Portal.
- Die IP-Adressen der mehrinstanzenfähigen Umgebungen sind über das `AzureCognitiveSearch`-Diensttag verfügbar. Für [Azure-Diensttags](../virtual-network/service-tags-overview.md) ist für jeden Dienst ein veröffentlichter Bereich von IP-Adressen verfügbar. Sie sind über [Verwendung der Diensttagermittlungs-API (öffentliche Vorschau)](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview) oder [Ermitteln von Diensttags mithilfe von herunterladbaren JSON-Dateien](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) verfügbar. In beiden Fällen sind die IP-Adressbereiche nach Regionen untergliedert. Sie können nur die IP-Adressbereiche auswählen, die der Region zugewiesen sind, in der Ihr Suchdienst bereitgestellt wird.

Für bestimmte Datenquellen kann das Diensttag selbst direkt verwendet werden, anstatt die Liste der IP-Adressbereiche aufzulisten (die IP-Adresse des Suchdiensts muss weiterhin explizit verwendet werden). Diese Datenquellen schränken den Zugriff durch das Einrichten einer [Netzwerksicherheitsgruppen-Regel](../virtual-network/network-security-groups-overview.md) ein, die native Unterstützung für das Hinzufügen eines Diensttags bietet, im Gegensatz zu IP-Regeln wie den von Azure Storage, Cosmos DB, Azure SQL usw. angebotenen. Folgende Datenquellen unterstützen die direkte Verwendung des `AzureCognitiveSearch`-Diensttags zusätzlich zur IP-Adresse des Suchdiensts:

- [SQL Server auf Azure Virtual Machines](./search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md#restrict-access-to-the-azure-cognitive-search)

- [SQL Managed Instance](./search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md#verify-nsg-rules)

Weitere Informationen zu dieser Konnektivitätsoption finden Sie unter [Indexerverbindungen über eine IP-Firewall](search-indexer-howto-access-ip-restricted.md).

## <a name="granting-access-via-private-endpoints"></a>Gewähren des Zugriffs über private Endpunkte

Indexer können [private Endpunkte](../private-link/private-endpoint-overview.md) für den Zugriff auf Ressourcen verwenden, die entweder zum Auswählen virtueller Netzwerke gesperrt sind, oder für die kein öffentlicher Zugriff aktiviert ist.
Diese Funktion ist nur in kostenpflichtigen Suchdiensten verfügbar, mit Beschränkungen hinsichtlich der Anzahl der privaten Endpunkte, die erstellt werden. Weitere Informationen finden Sie unter [Diensteinschränkungen](search-limits-quotas-capacity.md#shared-private-link-resource-limits).

### <a name="step-1-create-a-private-endpoint-to-the-secure-resource"></a>Schritt 1: Erstellen eines privaten Endpunkts für die sichere Ressource

Kunden sollten den Suchverwaltungsvorgang, [CreateOrUpdate-API](/rest/api/searchmanagement/sharedprivatelinkresources/createorupdate), in einer **gemeinsam genutzten Private Link-Ressource** aufrufen, um eine Verbindung über einen privaten Endpunkt mit ihrer sicheren Ressource (z. B. einem Speicherkonto) herzustellen. Datenverkehr, der über diese (ausgehende) Verbindung über einen privaten Endpunkt geleitet wird, stammt nur aus dem virtuellen Netzwerk, das sich in der suchdienstspezifischen „privaten“ Indexerausführungsumgebung befindet.

Azure Cognitive Search überprüft, ob Aufrufer dieser API über Azure RBAC-Berechtigungen zum Genehmigen von Anforderungen von Verbindungen über private Endpunkte an die sichere Ressource verfügen. Wenn Sie z. B. eine Verbindung über einen privaten Endpunkt mit einem Speicherkonto mit Leseberechtigungen anfordern, wird dieser Aufruf abgelehnt.

### <a name="step-2-approve-the-private-endpoint-connection"></a>Schritt 2: Genehmigen der Verbindung mit einem privaten Endpunkt

Wenn der (asynchrone) Vorgang abgeschlossen wird, der eine freigegebene Private Link-Ressource erstellt, wird eine Verbindung über einen privaten Endpunkt mit dem Status „Ausstehend“ erstellt. Es wird noch kein Datenverkehr über die Verbindung übertragen.
Es wird dann erwartet, dass der Kunde diese Anforderung auf seiner sicheren Ressource findet und „genehmigt“. In der Regel kann dies entweder über das Azure-Portal oder die [REST-API](/rest/api/virtualnetwork/privatelinkservices/updateprivateendpointconnection) erfolgen.

### <a name="step-3-force-indexers-to-run-in-the-private-environment"></a>Schritt 3: Erzwingen der Indexerausführung in der „privaten“ Umgebung

Ein genehmigter privater Endpunkt ermöglicht erfolgreiche vom Suchdienst ausgehende Aufrufe an eine Ressource, die über eine bestimmte Form von Zugriffsbeschränkungen auf Netzwerkebene verfügt (z. B. eine Speicherkonto-Datenquelle, die so konfiguriert ist, dass nur von bestimmten virtuellen Netzwerken darauf zugegriffen werden kann).
Dies bedeutet, dass jeder Indexer, der über den privaten Endpunkt eine solche Datenquelle erreichen kann, erfolgreich ausgeführt wird.
Wenn der private Endpunkt nicht genehmigt wird oder der Indexer die Verbindung mit dem privaten Endpunkt nicht nutzt, endet die Indexerausführung mit `transientFailure`.

Damit Indexer über Verbindungen über private Endpunkte auf Ressourcen zugreifen können, ist es obligatorisch, die `executionEnvironment` des Indexers auf `"Private"` festzulegen, um sicherzustellen, dass alle Indexerausführungen den privaten Endpunkt verwenden können. Dies liegt daran, dass private Endpunkte innerhalb der privaten suchdienstspezifischen Umgebung bereitgestellt werden.

```json
    {
      "name" : "myindexer",
      ... other indexer properties
      "parameters" : {
          ... other parameters
          "configuration" : {
            ... other configuration properties
            "executionEnvironment": "Private"
          }
        }
    }
```

Diese Schritte werden unter [Indexerverbindungen über einen privaten Endpunkt](search-indexer-howto-access-private.md) ausführlicher beschrieben.
Wenn Sie über einen genehmigten privaten Endpunkt für eine Ressource verfügen, werden als *privat* festgelegte Indexer versuchen, über die Verbindung über den privaten Endpunkt Zugriff zu erhalten.

### <a name="limits"></a>Grenzwerte

Um eine optimale Leistung und Stabilität des Suchdiensts zu gewährleisten, werden Einschränkungen (von der Suchdienstebene) in den folgenden Dimensionen auferlegt:

- Die Arten von Indexern, die als *privat* festgelegt werden können.
- Die Anzahl der freigegebenen Private Link-Ressourcen, die erstellt werden können.
- Die Anzahl der unterschiedlichen Ressourcentypen, für die freigegebene Private Link-Ressourcen erstellt werden können.

Diese Grenzwerte werden in [Dienstgrenzwerte in der kognitiven Azure-Suche](search-limits-quotas-capacity.md) dokumentiert.

## <a name="next-steps"></a>Nächste Schritte

- [Indexerverbindungen über IP-Firewalls](search-indexer-howto-access-ip-restricted.md)
- [Indexerverbindungen mithilfe der Ausnahme für vertrauenswürdige Dienste](search-indexer-howto-access-trusted-service-exception.md)
- [Indexerverbindungen mit einem privaten Endpunkt](search-indexer-howto-access-private.md)