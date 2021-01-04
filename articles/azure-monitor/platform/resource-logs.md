---
title: Azure-Ressourcenprotokolle
description: Hier erfahren Sie, wie Sie Azure-Ressourcenprotokolle an einen Log Analytics-Arbeitsbereich in Azure Monitor streamen.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: a2f46440a4214e298bc6d2f3b9c2b5680437ead7
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95522701"
---
# <a name="azure-resource-logs"></a>Azure-Ressourcenprotokolle
Azure-Ressourcenprotokolle sind [Plattformprotokolle](platform-logs-overview.md), die Einblicke in Vorgänge bereitstellen, die in einer Azure-Ressource ausgeführt wurden. Der Inhalt dieser Protokolle variiert je nach Azure-Dienst und -Ressourcentyp. Ressourcenprotokolle werden standardmäßig nicht erfasst. Sie müssen eine Diagnoseeinstellung für jede Azure-Ressource erstellen, um deren Ressourcenprotokolle an einen Log Analytics-Arbeitsbereich zu senden, damit sie mit [Azure Monitor-Protokollen](data-platform-logs.md), Azure Event Hubs zur Weiterleitung außerhalb von Azure oder Azure Storage zur Archivierung verwendet werden können.

Ausführliche Informationen zum Erstellen einer Diagnoseeinstellung finden Sie unter [Erstellen von Diagnoseeinstellungen zum Senden von Plattformprotokollen und Metriken an verschiedene Ziele](diagnostic-settings.md). Informationen zur Verwendung von Azure Policy zum automatischen Erstellen einer Diagnoseeinstellung für jede erstellte Azure-Ressource finden Sie unter [Bedarfsorientiertes Bereitstellen von Azure Monitor mithilfe von Azure Policy](../deploy-scale.md).

## <a name="send-to-log-analytics-workspace"></a>Senden an den Log Analytics-Arbeitsbereich
 Senden Sie Ressourcenprotokolle an einen Log Analytics-Arbeitsbereich, um die Features von [Azure Monitor-Protokolle](data-platform-logs.md) zu aktivieren. Hierzu zählen Folgende:

- Korrelieren von Ressourcenprotokolldaten mit anderen von Azure Monitor gesammelten Überwachungsdaten
- Konsolidieren von Protokolleinträgen mehrerer Azure-Ressourcen, -Abonnements und -Mandanten an einem einzigen Ort zur gemeinsamen Analyse
- Verwenden von Protokollabfragen zum Ausführen komplexer Analysen und Erhalten tiefer Einblicke in Protokolldaten
- Verwenden von Protokollwarnungen mit komplexer Warnungslogik

[Erstellen Sie eine Diagnoseeinstellung](diagnostic-settings.md), um Ressourcenprotokolle an einen Log Analytics-Arbeitsbereich zu senden. Diese Daten werden in Tabellen gespeichert, wie es unter [Struktur von Azure Monitor-Protokollen](./data-platform-logs.md) beschrieben ist. Welche Tabellen von Ressourcenprotokollen verwendet werden, hängt von dem von der Ressource verwendeten Sammlungstyp ab:

- Azure-Diagnose: Alle Daten werden in die Tabelle _AzureDiagnostics_ geschrieben.
- Ressourcenspezifisch: Daten werden für jede Kategorie der Ressource in eine individuelle Tabelle geschrieben.

### <a name="azure-diagnostics-mode"></a>Modus „Azure-Diagnose“ 
In diesem Modus werden alle Daten aus einer beliebigen Diagnoseeinstellung in der Tabelle _AzureDiagnostics_ gesammelt. Dies ist die Legacymethode, die heute von den meisten Azure-Diensten genutzt wird. Da mehrere Ressourcentypen Daten an dieselbe Tabelle senden, stellt deren Schema die Obermenge der Schemas aller erfassten einzelnen Datentypen dar.

Sehen Sie sich das folgende Beispiel an, in dem Diagnoseeinstellungen für die folgenden Datentypen im gleichen Arbeitsbereich gesammelt werden:

- Überwachungsprotokolle von Dienst 1 (mit einem Schema, das aus den Spalten A, B und C besteht)  
- Fehlerprotokolle von Dienst 1 (mit einem Schema, das aus den Spalten D, E und F besteht)  
- Überwachungsprotokolle von Dienst 2 (mit einem Schema, das aus den Spalten G, H und I besteht)  

Die Tabelle „AzureDiagnostics“ sieht dann wie folgt aus:  

| ResourceProvider    | Category     | A  | B  | C  | D  | E  | F  | G  | H  | I  |
| -- | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- |
| Microsoft.Service1 | AuditLogs    | x1 | y1 | z1 |    |    |    |    |    |    |
| Microsoft.Service1 | ErrorLogs    |    |    |    | q1 | w1 | e1 |    |    |    |
| Microsoft.Service2 | AuditLogs    |    |    |    |    |    |    | j1 | k1 | l1 |
| Microsoft.Service1 | ErrorLogs    |    |    |    | q2 | w2 | e2 |    |    |    |
| Microsoft.Service2 | AuditLogs    |    |    |    |    |    |    | j3 | k3 | l3 |
| Microsoft.Service1 | AuditLogs    | x5 | y5 | z5 |    |    |    |    |    |    |
| ... |

### <a name="resource-specific"></a>Modus „Ressourcenspezifisch“
In diesem Modus werden individuelle Tabellen in dem ausgewählten Arbeitsbereich für die einzelnen Kategorien erstellt, die in der Diagnoseeinstellung ausgewählt werden. Diese Methode wird empfohlen, da sie ein einfacheres Arbeiten mit den Daten in Protokollabfragen ermöglicht, bessere Auffindbarkeit von Schemas und deren Struktur bietet, die Leistung sowohl in Hinsicht auf die Erfassungslatenz als auch Abfragezeiten verbessert und die Möglichkeit bietet, Azure RBAC-Rechte für eine bestimmte Tabelle zu gewähren. Alle Azure-Dienste werden letztendlich in den Modus „Ressourcenspezifisch“ migriert. 

Im obigen Beispiel hätte das zur Folge, dass drei Tabellen erstellt werden:
 
- Tabelle *Service1AuditLogs* wie folgt:

    | Ressourcenanbieter | Category | A | B | C |
    | -- | -- | -- | -- | -- |
    | Service1 | AuditLogs | x1 | y1 | z1 |
    | Service1 | AuditLogs | x5 | y5 | z5 |
    | ... |

- Tabelle *Service1ErrorLogs* wie folgt:  

    | Ressourcenanbieter | Category | D | E | F |
    | -- | -- | -- | -- | -- | 
    | Service1 | ErrorLogs |  q1 | w1 | e1 |
    | Service1 | ErrorLogs |  q2 | w2 | e2 |
    | ... |

- Tabelle *Service2AuditLogs* wie folgt:  

    | Ressourcenanbieter | Category | G | H | I |
    | -- | -- | -- | -- | -- |
    | Service2 | AuditLogs | j1 | k1 | l1|
    | Service2 | AuditLogs | j3 | k3 | l3|
    | ... |



### <a name="select-the-collection-mode"></a>Auswählen des Sammlungsmodus
Die meisten Azure-Ressourcen schreiben Daten entweder im Modus **Azure-Diagnose** oder im Modus **Ressourcenspezifisch** in den Arbeitsbereich, ohne dass Sie eine Auswahl treffen können. Ausführliche Informationen zum jeweils verwendeten Modus finden Sie in der [Dokumentation zu den einzelnen Diensten](./resource-logs-schema.md). Alle Azure-Dienste verwenden letztendlich den Modus „Ressourcenspezifisch“. Im Rahmen dieses Übergangs können Sie bei einigen Ressourcen einen Modus in der Diagnoseeinstellung auswählen. Geben Sie den Modus „Ressourcenspezifisch“ für alle neuen Diagnoseeinstellungen an, da die Daten dadurch einfacher zu verwalten sind und komplexe Migrationen zu einem späteren Zeitpunkt vermieden werden können.
  
   ![Auswahl eines Modus in den Diagnose-Einstellungen](media/resource-logs-collect-workspace/diagnostic-settings-mode-selector.png)

> [!NOTE]
> Ein Beispiel für das Festlegen des Sammlungsmodus mithilfe einer Resource Manager-Vorlage finden Sie unter [Beispiele für Resource Manager-Vorlagen für Diagnoseeinstellungen in Azure Monitor](../samples/resource-manager-diagnostic-settings.md#diagnostic-setting-for-recovery-services-vault).


Sie können eine vorhandene Diagnoseeinstellung in den Modus „Ressourcenspezifisch“ ändern. In diesem Fall verbleiben Daten, die bereits gesammelt wurden, in der Tabelle _AzureDiagnostics_, bis sie gemäß Ihrer Aufbewahrungseinstellung für den Arbeitsbereich entfernt werden. Neue Daten werden in der dedizierten Tabelle gesammelt. Verwenden Sie den [union](/azure/kusto/query/unionoperator)-Operator, um Daten in beiden Tabellen abzufragen.

Sehen Sie regelmäßig im Blog [Azure-Updates](https://azure.microsoft.com/updates/) nach, ob Ankündigungen für Azure-Dienste vorliegen, die den Modus „Ressourcenspezifisch“ unterstützen.

### <a name="column-limit-in-azurediagnostics"></a>Spaltenlimit in AzureDiagnostics
Für jede Tabelle in Azure Monitor-Protokollen besteht ein Eigenschaftenlimit von 500. Sobald dieses Limit erreicht ist, werden alle Zeilen, die Daten mit einer Eigenschaft außerhalb der ersten 500 enthalten, zum Zeitpunkt der Erfassung gelöscht. Die Tabelle *AzureDiagnostics* ist besonders anfällig für dieses Limit, da sie Eigenschaften für alle Azure-Dienste enthält, die in diese Tabelle schreiben.

Wenn Sie Ressourcenprotokolle von mehreren Diensten erfassen, kann _AzureDiagnostics_ diesen Grenzwert überschreiten, wodurch Daten fehlen. Bis alle Azure-Dienste den Modus „Ressourcenspezifisch“ unterstützen, sollten Sie Ressourcen so konfigurieren, dass sie in mehrere Arbeitsbereiche schreiben, um die Wahrscheinlichkeit zu verringern, dass das Limit von 500 Spalten erreicht wird.

### <a name="azure-data-factory"></a>Azure Data Factory
Azure Data Factory ist aufgrund eines detaillierten Satzes von Protokollen ein Dienst, der bekanntermaßen eine große Anzahl von Spalten schreibt und möglicherweise dazu führt, dass _AzureDiagnostics_ den Grenzwert überschreitet. Für alle Diagnoseeinstellungen, die vor dem Aktivieren des Modus „Ressourcenspezifisch“ konfiguriert wurden, wird eine neue Spalte für jeden Benutzerparameter mit eindeutigem Namen zu jeder Aktivität erstellt. Aufgrund der Ausführlichkeit von Aktivitätseingaben und -ausgaben werden weitere Spalten erstellt.
 
Sie sollten Ihre Protokolle so schnell wie möglich zum Modus „Ressourcenspezifisch“ migrieren. Wenn Sie dafür nicht sofort eine Gelegenheit finden, können Sie als Zwischenlösung Azure Data Factory-Protokolle in einem eigenen Arbeitsbereich isolieren, um die Wahrscheinlichkeit zu minimieren, dass diese Protokolle Auswirkungen auf andere Protokolltypen haben, die in Ihren Arbeitsbereichen erfasst werden.


## <a name="send-to-azure-event-hubs"></a>Senden an Azure Event Hubs
Senden Sie Ressourcenprotokolle an einen Event Hub, um diese an Ressourcen außerhalb von Azure zu senden, z. B. an eine SIEM-Lösung oder andere Protokollanalyselösungen von Drittanbietern. Ressourcenprotokolle von Event Hubs werden im JSON-Format mit einem `records`-Element genutzt, das die Datensätze in jeder Nutzlast enthält. Das Schema hängt vom Ressourcentyp ab, wie es unter [Allgemeines und dienstspezifisches Schema für Azure-Ressourcenprotokolle](resource-logs-schema.md) beschrieben ist.

Es folgt ein Beispiel für eine Datenausgabe aus Event Hubs für ein Ressourcenprotokoll:

```json
{
    "records": [
        {
            "time": "2019-07-15T18:00:22.6235064Z",
            "workflowId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA/RUNS/08587330013509921957/ACTIONS/SEND_EMAIL",
            "category": "WorkflowRuntime",
            "level": "Error",
            "operationName": "Microsoft.Logic/workflows/workflowActionCompleted",
            "properties": {
                "$schema": "2016-04-01-preview",
                "startTime": "2016-07-15T17:58:55.048482Z",
                "endTime": "2016-07-15T18:00:22.4109204Z",
                "status": "Failed",
                "code": "BadGateway",
                "resource": {
                    "subscriptionId": "00000000-0000-0000-0000-000000000000",
                    "resourceGroupName": "JohnKemTest",
                    "workflowId": "243aac67fe904cf195d4a28297803785",
                    "workflowName": "JohnKemTestLA",
                    "runId": "08587330013509921957",
                    "location": "westus",
                    "actionName": "Send_email"
                },
                "correlation": {
                    "actionTrackingId": "29a9862f-969b-4c70-90c4-dfbdc814e413",
                    "clientTrackingId": "08587330013509921958"
                }
            }
        },
        {
            "time": "2019-07-15T18:01:15.7532989Z",
            "workflowId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA/RUNS/08587330012106702630/ACTIONS/SEND_EMAIL",
            "category": "WorkflowRuntime",
            "level": "Information",
            "operationName": "Microsoft.Logic/workflows/workflowActionStarted",
            "properties": {
                "$schema": "2016-04-01-preview",
                "startTime": "2016-07-15T18:01:15.5828115Z",
                "status": "Running",
                "resource": {
                    "subscriptionId": "00000000-0000-0000-0000-000000000000",
                    "resourceGroupName": "JohnKemTest",
                    "workflowId": "243aac67fe904cf195d4a28297803785",
                    "workflowName": "JohnKemTestLA",
                    "runId": "08587330012106702630",
                    "location": "westus",
                    "actionName": "Send_email"
                },
                "correlation": {
                    "actionTrackingId": "042fb72c-7bd4-439e-89eb-3cf4409d429e",
                    "clientTrackingId": "08587330012106702632"
                }
            }
        }
    ]
}
```

## <a name="send-to-azure-storage"></a>Senden an Azure Storage
Senden Sie Ressourcenprotokolle zur Aufbewahrung für die Archivierung an Azure Storage. Nach dem Einrichten der Diagnoseeinstellung wird im Speicherkonto ein Speichercontainer erstellt, sobald ein Ereignis in einer der aktivierten Protokollkategorien auftritt. Die Blobs im Container verwenden die folgende Benennungskonvention:

```
insights-logs-{log category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/RESOURCEGROUPS/{resource group name}/PROVIDERS/{resource provider name}/{resource type}/{resource name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Beispielsweise könnte das Blob für eine Netzwerksicherheitsgruppe einen Namen aufweisen, der dem folgenden Beispiel ähnelt:

```
insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUP/TESTNSG/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

Jedes Blob vom Typ „PT1H.json“ enthält ein JSON-Blob mit Ereignissen, die innerhalb der in der Blob-URL angegebenen Stunde (Beispiel: h=12) aufgetreten sind. Während der aktuellen Stunde werden Ereignisse an die Datei „PT1H.json“ angefügt, sobald sie auftreten. Der Minutenwert (m=00) ist immer „00“, da Ressourcenprotokollereignisse stundenweise in einzelne Blobs unterteilt werden.

Innerhalb der Datei „PT1H.json“ wird jedes Ereignis im folgenden Format gespeichert. Hierbei wird ein gemeinsames Schema der obersten Ebene verwendet. Es ist aber für jeden Azure-Dienst eindeutig, wie unter [Ressourcenprotokollschema](./resource-logs-schema.md) beschrieben.

``` JSON
{"time": "2016-07-01T00:00:37.2040000Z","systemId": "46cdbb41-cb9c-4f3d-a5b4-1d458d827ff1","category": "NetworkSecurityGroupRuleCounter","resourceId": "/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/TESTNSG","operationName": "NetworkSecurityGroupCounters","properties": {"vnetResourceGuid": "{12345678-9012-3456-7890-123456789012}","subnetPrefix": "10.3.0.0/24","macAddress": "000123456789","ruleName": "/subscriptions/ s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg/securityRules/default-allow-rdp","direction": "In","type": "allow","matchedConnections": 1988}}
```

> [!NOTE]
> Plattformprotokolle werden mit [JSON Lines](http://jsonlines.org/) in den Blobspeicher geschrieben. Dies bedeutet, dass jedes Ereignis in einer Zeile enthalten ist und mit dem Zeilenumbruchzeichen ein neues Ereignis angegeben wird. Dieses Format wurde im November 2018 implementiert. Vor diesem Datum wurden Protokolle in den Blobspeicher als JSON-Array von Datensätzen geschrieben, wie unter [Vorbereiten auf die Formatänderung für Azure Monitor-Plattformprotokolle, die in einem Speicherkonto archiviert werden](resource-logs-blob-format.md) beschrieben.


## <a name="next-steps"></a>Nächste Schritte

* [Erfahren Sie mehr über Ressourcenprotokolle](platform-logs-overview.md).
* [Erstellen Sie Diagnoseeinstellungen zum Senden von Plattformprotokollen und Metriken an verschiedene Ziele](diagnostic-settings.md).