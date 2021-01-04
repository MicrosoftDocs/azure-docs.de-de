---
title: Aktivieren und Abfragen von Diagnoseprotokollen
titleSuffix: Azure Digital Twins
description: Erfahren Sie, wie Sie die Protokollierung mit Diagnoseeinstellungen aktivieren und die Protokolle für eine sofortige Anzeige abfragen.
author: baanders
ms.author: baanders
ms.date: 11/9/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: d988617fcaf7479c7bb3356e6ef6f87824ed23a7
ms.sourcegitcommit: 9706bee6962f673f14c2dc9366fde59012549649
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/13/2020
ms.locfileid: "94616653"
---
# <a name="troubleshooting-azure-digital-twins-diagnostics-logging"></a>Problembehandlung von Azure Digital Twins: Diagnoseprotokollierung

Azure Digital Twins kann Protokolle für Ihre Dienstinstanz zur Überwachung von Leistung, Zugriff und anderen Daten erfassen. Anhand dieser Protokolle können Sie eine Vorstellung davon erhalten, was in Ihrer Azure Digital Twins-Instanz geschieht, und eine Ursachenanalyse für Probleme durchführen, ohne sich an den Azure-Support wenden zu müssen.

In diesem Artikel wird das [**Konfigurieren von Diagnoseeinstellungen**](#turn-on-diagnostic-settings) im [Azure-Portal](https://portal.azure.com) erläutert, um mit der Erfassung von Protokollen von Ihrer Azure Digital Twins-Instanz zu beginnen. Sie können auch angeben, wo die Protokolle gespeichert werden sollen (z. B. Log Analytics oder ein Speicherkonto Ihrer Wahl).

Dieser Artikel enthält außerdem Listen aller [Protokollkategorien](#log-categories) und [Protokollschemas](#log-schemas), die von Azure Digital Twins erfasst werden.

Nachdem Sie Protokolle eingerichtet haben, können Sie die [**Protokolle abfragen**](#view-and-query-logs), um schnell benutzerdefinierte Einblicke zu gewinnen.

## <a name="turn-on-diagnostic-settings"></a>Aktivieren von Diagnoseeinstellungen 

Aktivieren Sie Diagnoseeinstellungen, um mit dem Erfassen von Protokollen in Ihrer Azure Digital Twins-Instanz zu beginnen. Sie können auch das Ziel auswählen, in dem die exportierten Protokolle gespeichert werden sollen. Im Folgenden wird beschrieben, wie Sie Diagnoseeinstellungen für Ihre Azure Digital Twins-Instanz aktivieren.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und navigieren Sie zu Ihrer Azure Digital Twins-Instanz. Sie finden sie, indem Sie ihren Namen auf der Suchleiste des Portals eingeben. 

2. Wählen Sie im Menü die Option **Diagnoseeinstellungen** und dann **Diagnoseeinstellung hinzufügen** aus.

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings.png" alt-text="Screenshot der Seite „Diagnoseeinstellungen“ und der Schaltfläche zum Hinzufügen" lightbox="media/troubleshoot-diagnostics/diagnostic-settings.png":::

3. Geben Sie auf der daraufhin geöffneten Seite die folgenden Werte ein:
     * **Name der Diagnoseeinstellung:** Benennen Sie die Diagnoseeinstellungen.
     * **Kategoriedetails:** Wählen Sie aus, welche Vorgänge Sie überwachen möchten, und aktivieren Sie die Kontrollkästchen für die Diagnose dieser Vorgänge. Die Vorgänge, für die über die Diagnoseeinstellungen Berichte erstellt werden können, sind die folgenden:
        - DigitalTwinsOperation
        - EventRoutesOperation
        - ModelsOperation
        - QueryOperation
        - AllMetrics
        
        Weitere Details zu diesen Kategorien und den darin enthaltenen Informationen finden Sie im Abschnitt [*Protokollkategorien*](#log-categories) weiter unten.
     * **Zieldetails:** Wählen Sie aus, wohin die Protokolle gesendet werden sollen. Sie können eine beliebige Kombination der drei Optionen auswählen:
        - An Log Analytics senden
        - In einem Speicherkonto archivieren
        - An einen Event Hub streamen

        Möglicherweise werden Sie aufgefordert, weitere Details einzugeben, wenn diese für Ihr ausgewähltes Ziel erforderlich sind.  
    
4. Speichern Sie die neuen Einstellungen. 

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings-details.png" alt-text="Screenshot der Seite mit den Diagnoseeinstellungen, auf der der Benutzer einen Namen für eine Diagnoseeinstellung eingegeben und einige Kontrollkästchen für Kategorie- und Zieldetails aktiviert hat. Die Schaltfläche „Speichern“ ist hervorgehoben." lightbox="media/troubleshoot-diagnostics/diagnostic-settings-details.png":::

Neue Einstellungen werden in etwa zehn Minuten wirksam. Anschließend werden die Protokolle im konfigurierten Ziel auf der Seite **Diagnoseeinstellungen** für Ihre Instanz angezeigt. 

Ausführlichere Informationen zu Diagnoseeinstellungen und deren Einrichtungsoptionen finden Sie unter [*Erstellen von Diagnoseeinstellungen zum Senden von Plattformprotokollen und Metriken an verschiedene Ziele*](../azure-monitor/platform/diagnostic-settings.md).

## <a name="log-categories"></a>Protokollkategorien

Nachfolgend finden Sie weitere Details zu den Protokollkategorien, die von Azure Digital Twins erfasst werden.

| Protokollkategorie | BESCHREIBUNG |
| --- | --- |
| ADTModelsOperation | Protokolliert alle API-Aufrufe im Zusammenhang mit Modellen. |
| ADTQueryOperation | Protokolliert alle API-Aufrufe im Zusammenhang mit Abfragen. |
| ADTEventRoutesOperation | Protokolliert alle API-Aufrufe im Zusammenhang mit Ereignisrouten sowie ausgehende Ereignisse von Azure Digital Twins an einen Endpunktdienst wie Event Grid, Event Hubs und Service Bus. |
| ADTDigitalTwinsOperation | Protokolliert alle API-Aufrufe im Zusammenhang mit Azure Digital Twins. |

Jede Protokollkategorie besteht aus Schreib-, Lese-, Lösch- und Aktionsvorgängen.  Diese lassen sich REST-API-Aufrufen wie folgt zuordnen:

| Ereignistyp | REST-API-Vorgänge |
| --- | --- |
| Schreiben | PUT und PATCH |
| Lesen | GET |
| Löschen | Delete |
| Aktion | POST |

Im Folgenden finden Sie eine umfassende Liste der Vorgänge und entsprechenden [REST-API-Aufrufe für Azure Digital Twins](/rest/api/azure-digitaltwins/), die in jeder Kategorie protokolliert werden. 

>[!NOTE]
> Jede Protokollkategorie enthält mehrere Vorgänge/REST-API-Aufrufe. In der folgenden Tabelle ist jede Protokollkategorie allen darunter aufgeführten Vorgänge/REST-API-Aufrufen zugeordnet, anschließend folgt die nächste Kategorie. 

| Protokollkategorie | Vorgang | REST-API-Aufrufe und andere Ereignisse |
| --- | --- | --- |
| ADTModelsOperation | Microsoft.DigitalTwins/models/write | API zum Aktualisieren von Digital Twin-Modellen |
|  | Microsoft.DigitalTwins/models/read | APIs zum Abrufen nach ID und Auflisten von Digital Twin-Modellen |
|  | Microsoft.DigitalTwins/models/delete | API zum Löschen von Digital Twin-Modellen |
|  | Microsoft.DigitalTwins/models/action | API zum Hinzufügen von Digital Twin-Modellen |
| ADTQueryOperation | Microsoft.DigitalTwins/query/action | API zum Abfragen von Digital Twin-Instanzen |
| ADTEventRoutesOperation | Microsoft.DigitalTwins/eventroutes/write | API zum Hinzufügen von Ereignisrouten |
|  | Microsoft.DigitalTwins/eventroutes/read | APIs zum Abrufen nach ID und Auflisten von Ereignisrouten |
|  | Microsoft.DigitalTwins/eventroutes/delete | API zum Löschen von Ereignisrouten |
|  | Microsoft.DigitalTwins/eventroutes/action | Fehler beim Veröffentlichen von Ereignissen in einem Endpunktdienst (kein API-Aufruf). |
| ADTDigitalTwinsOperation | Microsoft.DigitalTwins/digitaltwins/write | Vorgänge zum Hinzufügen, Hinzufügen von Beziehungen, Aktualisieren und Aktualisieren von Komponenten von Digital Twins |
|  | Microsoft.DigitalTwins/digitaltwins/read | Vorgänge zum Abrufen nach ID, Abrufen von Komponenten, Abrufen der Beziehung nach ID, Auflisten eingehender Beziehungen und Auflisten von Beziehungen von Digital Twins |
|  | Microsoft.DigitalTwins/digitaltwins/delete | Vorgänge zum Löschen und Löschen von Beziehungen von Digital Twins |
|  | Microsoft.DigitalTwins/digitaltwins/action | Senden von Komponententelemetrie und Senden von Telemetrie von Digital Twins |

## <a name="log-schemas"></a>Protokollschemas 

Jede Protokollkategorie verfügt über ein Schema, das definiert, wie Ereignisse in dieser Kategorie gemeldet werden. Jeder einzelne Protokolleintrag wird als Text gespeichert und als JSON-Blob formatiert. Die Felder im Protokoll und die JSON-Beispieltexte werden für jeden unten angegebenen Protokolltyp bereitgestellt. 

`ADTDigitalTwinsOperation`, `ADTModelsOperation` und `ADTQueryOperation` verwenden ein konsistentes API-Protokollschema. `ADTEventRoutesOperation` verfügt über ein eigenes separates Schema.

### <a name="api-log-schemas"></a>API-Protokollschemas

Dieses Protokollschema ist für `ADTDigitalTwinsOperation`, `ADTModelsOperation` und `ADTQueryOperation` konsistent. Es enthält Informationen, die für API-Aufrufe an eine Azure Digital Twins-Instanz relevant sind.

Im Folgenden finden Sie die Feld- und Eigenschaftsbeschreibungen für API-Protokolle.

| Feldname | Datentyp | BESCHREIBUNG |
|-----|------|-------------|
| `Time` | Datetime | Datum und Uhrzeit des Auftretens dieses Ereignisses in UTC |
| `ResourceID` | String | Azure Resource Manager-Ressourcen-ID für die Ressource, in der das Ereignis aufgetreten ist |
| `OperationName` | String  | Typ der Aktion, die während des Ereignisses ausgeführt wurde |
| `OperationVersion` | String | Während des Ereignisses verwendete API-Version |
| `Category` | String | Typ der auszugebenden Ressource |
| `ResultType` | String | Ergebnis des Ereignisses |
| `ResultSignature` | String | HTTP-Statuscode für das Ereignis |
| `ResultDescription` | String | Weitere Details zum Ereignis |
| `DurationMs` | String | Zeit für die Ausführung des Ereignisses in Millisekunden |
| `CallerIpAddress` | String | Maskierte Quell-IP-Adresse für das Ereignis |
| `CorrelationId` | Guid | Vom Kunden angegebener eindeutiger Bezeichner für das Ereignis |
| `Level` | String | Protokollschweregrad des Ereignisses |
| `Location` | String | Region, in der das Ereignis aufgetreten ist |
| `RequestUri` | Uri | Während des Ereignisses verwendeter Endpunkt |

Im Folgenden finden Sie Beispiel-JSON-Texte für diese Arten von Protokollen.

#### <a name="adtdigitaltwinsoperation"></a>ADTDigitalTwinsOperation

```json
{
  "time": "2020-03-14T21:11:14.9918922Z",
  "resourceId": "/SUBSCRIPTIONS/BBED119E-28B8-454D-B25E-C990C9430C8F/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.DIGITALTWINS/DIGITALTWINSINSTANCES/MYINSTANCENAME",
  "operationName": "Microsoft.DigitalTwins/digitaltwins/write",
  "operationVersion": "2020-10-31",
  "category": "DigitalTwinOperation",
  "resultType": "Success",
  "resultSignature": "200",
  "resultDescription": "",
  "durationMs": "314",
  "callerIpAddress": "13.68.244.*",
  "correlationId": "2f6a8e64-94aa-492a-bc31-16b9f0b16ab3",
  "level": "4",
  "location": "southcentralus",
  "uri": "https://myinstancename.api.scus.digitaltwins.azure.net/digitaltwins/factory-58d81613-2e54-4faa-a930-d980e6e2a884?api-version=2020-10-31"
}
```

#### <a name="adtmodelsoperation"></a>ADTModelsOperation

```json
{
  "time": "2020-10-29T21:12:24.2337302Z",
  "resourceId": "/SUBSCRIPTIONS/BBED119E-28B8-454D-B25E-C990C9430C8F/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.DIGITALTWINS/DIGITALTWINSINSTANCES/MYINSTANCENAME",
  "operationName": "Microsoft.DigitalTwins/models/write",
  "operationVersion": "2020-10-31",
  "category": "ModelsOperation",
  "resultType": "Success",
  "resultSignature": "201",
  "resultDescription": "",
  "durationMs": "935",
  "callerIpAddress": "13.68.244.*",
  "correlationId": "9dcb71ea-bb6f-46f2-ab70-78b80db76882",
  "level": "4",
  "location": "southcentralus",
  "uri": "https://myinstancename.api.scus.digitaltwins.azure.net/Models?api-version=2020-10-31",
}
```

#### <a name="adtqueryoperation"></a>ADTQueryOperation

```json
{
  "time": "2020-12-04T21:11:44.1690031Z",
  "resourceId": "/SUBSCRIPTIONS/BBED119E-28B8-454D-B25E-C990C9430C8F/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.DIGITALTWINS/DIGITALTWINSINSTANCES/MYINSTANCENAME",
  "operationName": "Microsoft.DigitalTwins/query/action",
  "operationVersion": "2020-10-31",
  "category": "QueryOperation",
  "resultType": "Success",
  "resultSignature": "200",
  "resultDescription": "",
  "durationMs": "255",
  "callerIpAddress": "13.68.244.*",
  "correlationId": "1ee2b6e9-3af4-4873-8c7c-1a698b9ac334",
  "level": "4",
  "location": "southcentralus",
  "uri": "https://myinstancename.api.scus.digitaltwins.azure.net/query?api-version=2020-10-31",
}
```

### <a name="egress-log-schemas"></a>Ausgangsprotokollschemas

Dies ist das Schema für `ADTEventRoutesOperation`-Protokolle. Diese enthalten Details zu Ausnahmen und den API-Vorgängen für Ausgangsendpunkte, die mit einer Instanz von Azure Digital Twins verbunden sind.

|Feldname | Datentyp | BESCHREIBUNG |
|-----|------|-------------|
| `Time` | Datetime | Datum und Uhrzeit des Auftretens dieses Ereignisses in UTC |
| `ResourceId` | String | Azure Resource Manager-Ressourcen-ID für die Ressource, in der das Ereignis aufgetreten ist |
| `OperationName` | String  | Typ der Aktion, die während des Ereignisses ausgeführt wurde |
| `Category` | String | Typ der auszugebenden Ressource |
| `ResultDescription` | String | Weitere Details zum Ereignis |
| `Level` | String | Protokollschweregrad des Ereignisses |
| `Location` | String | Region, in der das Ereignis aufgetreten ist |
| `EndpointName` | String | Name des Ausgangsendpunkts, der in Azure Digital Twins erstellt wurde |

Im Folgenden finden Sie Beispiel-JSON-Texte für diese Arten von Protokollen.

#### <a name="adteventroutesoperation"></a>ADTEventRoutesOperation

```json
{
  "time": "2020-11-05T22:18:38.0708705Z",
  "resourceId": "/SUBSCRIPTIONS/BBED119E-28B8-454D-B25E-C990C9430C8F/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.DIGITALTWINS/DIGITALTWINSINSTANCES/MYINSTANCENAME",
  "operationName": "Microsoft.DigitalTwins/eventroutes/action",
  "category": "EventRoutesOperation",
  "resultDescription": "Unable to send EventGrid message to [my-event-grid.westus-1.eventgrid.azure.net] for event Id [f6f45831-55d0-408b-8366-058e81ca6089].",
  "correlationId": "7f73ab45-14c0-491f-a834-0827dbbf7f8e",
  "level": "3",
  "location": "southcentralus",
  "properties": {
    "endpointName": "endpointEventGridInvalidKey"
  }
}
```

## <a name="view-and-query-logs"></a>Anzeigen und Abfragen von Protokollen

Weiter oben in diesem Artikel wurden die zu speichernden Protokolltypen konfiguriert und deren Speicherort angegeben.

Zum Beheben von Problemen und Generieren von Erkenntnissen aus diesen Protokollen können Sie **benutzerdefinierte Abfragen** generieren. Zu Beginn können Sie auch einige Beispielabfragen nutzen, die Ihnen vom Dienst bereitgestellt werden. Diese betreffen allgemeine Fragen, die Kunden zu ihrer Instanz haben können.

Nachfolgend wird beschrieben, wie Sie die Protokolle für Ihre Instanz abfragen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und navigieren Sie zu Ihrer Azure Digital Twins-Instanz. Sie finden sie, indem Sie ihren Namen auf der Suchleiste des Portals eingeben. 

2. Wählen Sie im Menü die Option **Protokolle** aus, um die Seite für Protokollabfragen zu öffnen. Die Seite wird mit einem Fenster mit dem Namen *Abfragen* geöffnet.

    :::image type="content" source="media/troubleshoot-diagnostics/logs.png" alt-text="Screenshot der Seite „Protokolle“ für eine Azure Digital Twins-Instanz. Sie wird von einem Fenster „Abfragen“ überlagert, das vorgefertigte Abfragen enthält, die nach verschiedenen Protokolloptionen benannt sind, z. B. der DigitalTwin-API-Latenz und Modell-API-Latenz." lightbox="media/troubleshoot-diagnostics/logs.png":::

    Dies sind vorgefertigte Beispielabfragen, die für verschiedene Protokolle geschrieben wurden. Sie können eine der Abfragen auswählen, um sie in den Abfrage-Editor zu laden, und die Abfrage ausführen, um diese Protokolle für Ihre Instanz anzuzeigen.

    Sie können das Fenster *Abfragen* auch ohne Ausführen einer Abfrage schließen, um direkt zur Seite mit dem Abfrage-Editor zu gelangen, auf der Sie benutzerdefinierten Abfragecode schreiben oder bearbeiten können.

3. Nachdem Sie das Fenster *Abfragen* verlassen haben, wird die Hauptseite des Abfrage-Editors angezeigt. Hier können Sie den Text der Beispielabfragen anzeigen und bearbeiten oder eigene Abfragen von Grund auf neu schreiben.
    :::image type="content" source="media/troubleshoot-diagnostics/logs-query.png" alt-text="Screenshot der Seite „Protokolle“ für eine Azure Digital Twins-Instanz. Das Fenster „Abfragen“ wird nicht mehr angezeigt. Stattdessen sind eine Liste mit verschiedenen Protokollen, ein Bearbeitungsbereich mit bearbeitbarem Abfragecode und ein Bereich mit dem Abfrageverlauf zu sehen." lightbox="media/troubleshoot-diagnostics/logs-query.png":::

    Linker Bereich: 
    - Die Registerkarte *Tabellen* zeigt die verschiedenen [Protokollkategorien](#log-categories) von Azure Digital Twins, die Sie in Ihren Abfragen verwenden können. 
    - Die Registerkarte *Abfragen* enthält die Beispielabfragen, die Sie in den Editor laden können.
    - Mithilfe der Registerkarte *Filter* können Sie eine gefilterte Ansicht der Daten festlegen, die von der Abfrage zurückgegeben werden.

Ausführlichere Informationen zu Protokollabfragen und deren Erstellung finden Sie in der [*Übersicht über Protokollabfragen in Azure Monitor*](../azure-monitor/log-query/log-query-overview.md).

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zum Konfigurieren von Diagnosen finden Sie unter [*Erfassen und Nutzen von Protokolldaten aus Ihren Azure-Ressourcen*](../azure-monitor/platform/platform-logs-overview.md).
* Informationen zu den Azure Digital Twins-Metriken finden Sie unter [*Problembehandlung: Anzeigen von Metriken mit Azure Monitor*](troubleshoot-metrics.md).
* Informationen zur Aktivierung von Warnungen für Ihre Metriken finden Sie unter [*Problembehandlung: Einrichten von Warnungen*](troubleshoot-alerts.md).