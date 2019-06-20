---
title: 'Tutorial: Erfassen von Diagnose- und Aktivitätsprotokolldaten in Azure Data Explorer ohne jeglichen Code'
description: In diesem Tutorial wird beschrieben, wie Sie Daten in Azure Data Explorer erfassen, ohne Code verwenden zu müssen, und diese Daten dann abfragen.
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: tutorial
ms.date: 04/29/2019
ms.openlocfilehash: 187aa4b02e389c485b24ad7de256422d1880182b
ms.sourcegitcommit: 8a681ba0aaba07965a2adba84a8407282b5762b2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/29/2019
ms.locfileid: "64872597"
---
# <a name="tutorial-ingest-data-in-azure-data-explorer-without-one-line-of-code"></a>Tutorial: Erfassen von Daten in Azure Data Explorer ohne jeglichen Code

In diesem Tutorial wird beschrieben, wie Sie Daten aus Diagnose- und Aktivitätsprotokolldaten in einem Azure Data Explorer-Cluster erfassen, ohne jeglichen Code schreiben zu müssen. Mit dieser einfachen Erfassungsmethode können Sie schnell damit beginnen, Azure Data Explorer zu Datenanalysezwecken abzufragen.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen von Tabellen und einer Erfassungszuordnung in einer Azure Data Explorer-Datenbank
> * Formatieren der erfassten Daten mit einer Updaterichtlinie
> * Erstellen eines [Event Hub](/azure/event-hubs/event-hubs-about) und Herstellen einer Verbindung mit Azure Data Explorer
> * Streamen von Daten an einen Event Hub aus [Azure Monitor-Diagnoseprotokollen](/azure/azure-monitor/platform/diagnostic-logs-overview) und [Azure Monitor-Aktivitätsprotokollen](/azure/azure-monitor/platform/activity-logs-overview)
> * Abfragen der erfassten Daten mit Azure Data Explorer

> [!NOTE]
> Erstellen aller Ressourcen an demselben Azure-Standort bzw. in derselben Region. Dies ist eine Anforderung für Azure Monitor-Diagnoseprotokolle.

## <a name="prerequisites"></a>Voraussetzungen

* Wenn Sie über kein Azure-Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.
* [Schnellstart: Erstellen eines Azure Data Explorer-Clusters und einer Datenbank](create-cluster-database-portal.md). In diesem Tutorial lautet der Datenbankname *TestDatabase*.

## <a name="azure-monitor-data-provider-diagnostic-and-activity-logs"></a>Azure Monitor-Datenanbieter: Diagnose- und Aktivitätsprotokolle

Unten können Sie die Daten anzeigen, die von den Diagnose- und Aktivitätsprotokollen von Azure Monitor bereitgestellt werden, und sich damit vertraut machen. Basierend auf diesen Datenschemas erstellen wir eine Erfassungspipeline. Beachten Sie, dass jedes Ereignis in einem Protokoll ein Array von Datensätzen enthält. Dieses Array von Datensätzen wird später im Tutorial aufgeteilt.

### <a name="diagnostic-logs-example"></a>Beispiel für Diagnoseprotokolle

Bei Azure-Diagnoseprotokollen handelt es sich um Metriken, die von einem Azure-Dienst ausgegeben werden und Daten zum Betrieb dieses Diensts liefern. Daten werden mit einem Aggregationsintervall von einer Minute aggregiert. Hier ist ein Beispiel für ein Azure Data Explorer-Metrikereignisschema zur Abfragedauer angegeben:

```json
{
    "records": [
    {
        "count": 14,
        "total": 0,
        "minimum": 0,
        "maximum": 0,
        "average": 0,
        "resourceId": "/SUBSCRIPTIONS/F3101802-8C4F-4E6E-819C-A3B5794D33DD/RESOURCEGROUPS/KEDAMARI/PROVIDERS/MICROSOFT.KUSTO/CLUSTERS/KEREN",
        "time": "2018-12-20T17:00:00.0000000Z",
        "metricName": "QueryDuration",
        "timeGrain": "PT1M"
    },
    {
        "count": 12,
        "total": 0,
        "minimum": 0,
        "maximum": 0,
        "average": 0,
        "resourceId": "/SUBSCRIPTIONS/F3101802-8C4F-4E6E-819C-A3B5794D33DD/RESOURCEGROUPS/KEDAMARI/PROVIDERS/MICROSOFT.KUSTO/CLUSTERS/KEREN",
        "time": "2018-12-21T17:00:00.0000000Z",
        "metricName": "QueryDuration",
        "timeGrain": "PT1M"
    }
    ]
}
```

### <a name="activity-logs-example"></a>Beispiel für Aktivitätsprotokolle

Azure-Aktivitätsprotokolle sind Protokolle auf Abonnementebene, die einen Einblick in die Vorgänge geben, die mit Ressourcen in Ihrem Abonnement ausgeführt werden. Hier ist ein Beispiel für ein Aktivitätsprotokollereignis zur Überprüfung des Zugriffs angegeben:

```json
{
    "records": [
    {
        "time": "2018-12-26T16:23:06.1090193Z",
        "resourceId": "/SUBSCRIPTIONS/F80EB51C-C534-4F0B-80AB-AEBC290C1C19/RESOURCEGROUPS/CLEANUPSERVICE/PROVIDERS/MICROSOFT.WEB/SITES/CLNB5F73B70-DCA2-47C2-BB24-77B1A2CAAB4D/PROVIDERS/MICROSOFT.AUTHORIZATION",
        "operationName": "MICROSOFT.AUTHORIZATION/CHECKACCESS/ACTION",
        "category": "Action",
        "resultType": "Start",
        "resultSignature": "Started.",
        "durationMs": 0,
        "callerIpAddress": "13.66.225.188",
        "correlationId": "0de9f4bc-4adc-4209-a774-1b4f4ae573ed",
        "identity": {
            "authorization": {
                ...
            },
            "claims": {
                ...
            }
        },
        "level": "Information",
        "location": "global",
        "properties": {
            ...
        }
    },
    {
        "time": "2018-12-26T16:23:06.3040244Z",
        "resourceId": "/SUBSCRIPTIONS/F80EB51C-C534-4F0B-80AB-AEBC290C1C19/RESOURCEGROUPS/CLEANUPSERVICE/PROVIDERS/MICROSOFT.WEB/SITES/CLNB5F73B70-DCA2-47C2-BB24-77B1A2CAAB4D/PROVIDERS/MICROSOFT.AUTHORIZATION",
        "operationName": "MICROSOFT.AUTHORIZATION/CHECKACCESS/ACTION",
        "category": "Action",
        "resultType": "Success",
        "resultSignature": "Succeeded.OK",
        "durationMs": 194,
        "callerIpAddress": "13.66.225.188",
        "correlationId": "0de9f4bc-4adc-4209-a774-1b4f4ae573ed",
        "identity": {
            "authorization": {
                ...
            },
            "claims": {
                ...
            }
        },
        "level": "Information",
        "location": "global",
        "properties": {
            "statusCode": "OK",
            "serviceRequestId": "87acdebc-945f-4c0c-b931-03050e085626"
        }
    }]
}
```

## <a name="set-up-an-ingestion-pipeline-in-azure-data-explorer"></a>Einrichten einer Erfassungspipeline in Azure Data Explorer

Das Einrichten einer Azure Data Explorer-Pipeline umfasst mehrere Schritte, z. B. die [Tabellenerstellung und Datenerfassung](/azure/data-explorer/ingest-sample-data#ingest-data). Sie können die Daten auch ändern, zuordnen und aktualisieren.

### <a name="connect-to-the-azure-data-explorer-web-ui"></a>Herstellen einer Verbindung mit der Azure Data Explorer-Webbenutzeroberfläche

Wählen Sie in Azure Data Explorer für *TestDatabase* die Option **Abfrage**, um die Azure Data Explorer-Webbenutzeroberfläche zu öffnen.

![Seite „Abfrage“](media/ingest-data-no-code/query-database.png)

### <a name="create-the-target-tables"></a>Erstellen der Zieltabellen

Die Struktur der Azure Monitor-Protokolle ist nicht tabellarisch. Sie bearbeiten die Daten und erweitern jedes Ereignis auf einen oder mehrere Datensätze. Die Rohdaten werden in eine Zwischentabelle namens *ActivityLogsRawRecords* für Aktivitätsprotokolle und *DiagnosticLogsRawRecords* für Diagnoseprotokolle aufgenommen. Die Daten werden nun bearbeitet und erweitert. Mithilfe einer Updaterichtlinie werden die erweiterten Daten dann in die Tabelle *ActivityLogsRecords* für Aktivitätsprotokolle und *DiagnosticLogsRecords* für Diagnoseprotokolle aufgenommen. Das bedeutet, dass Sie zwei separate Tabellen für das Erfassen von Aktivitätsprotokollen und zwei separate Tabellen für das Erfassen von Diagnoseprotokollen erstellen müssen.

Verwenden Sie die Azure Data Explorer-Webbenutzeroberfläche, um die Zieltabellen in der Azure Data Explorer-Datenbank zu erstellen.

#### <a name="the-diagnostic-logs-table"></a>Tabelle für Diagnoseprotokolle

1. Erstellen Sie in der Datenbank *TestDatabase* eine Tabelle mit dem Namen *DiagnosticLogsRecords*, um die Diagnoseprotokoll-Datensätze zu speichern. Verwenden Sie den folgenden `.create table`-Steuerungsbefehl:

    ```kusto
    .create table DiagnosticLogsRecords (Timestamp:datetime, ResourceId:string, MetricName:string, Count:int, Total:double, Minimum:double, Maximum:double, Average:double, TimeGrain:string)
    ```

1. Wählen Sie **Ausführen**, um die Tabelle zu erstellen.

    ![Abfrage ausführen](media/ingest-data-no-code/run-query.png)

1. Erstellen Sie die Zwischendatentabelle *DiagnosticLogsRawRecords* in der *TestDatabase*-Datenbank für die Datenbearbeitung mit der folgenden Abfrage. Wählen Sie **Ausführen**, um die Tabelle zu erstellen.

    ```kusto
    .create table DiagnosticLogsRawRecords (Records:dynamic)
    ```

#### <a name="the-activity-logs-tables"></a>Tabellen für Aktivitätsprotokolle

1. Erstellen Sie eine Tabelle mit dem Namen *ActivityLogsRecords* in der Datenbank *TestDatabase*, um Aktivitätsprotokoll-Datensätze zu erhalten. Führen Sie zum Erstellen der Tabelle die folgende Azure Data Explorer-Abfrage aus:

    ```kusto
    .create table ActivityLogsRecords (Timestamp:datetime, ResourceId:string, OperationName:string, Category:string, ResultType:string, ResultSignature:string, DurationMs:int, IdentityAuthorization:dynamic, IdentityClaims:dynamic, Location:string, Level:string)
    ```

1. Erstellen Sie die Zwischentabelle mit dem Namen *ActivityLogsRawRecords* für Daten in der Datenbank *TestDatabase*, um die Bearbeitung der Daten zu ermöglichen:

    ```kusto
    .create table ActivityLogsRawRecords (Records:dynamic)
    ```

<!--
     ```kusto
     .alter-merge table ActivityLogsRawRecords policy retention softdelete = 0d
    <[Retention](/azure/kusto/management/retention-policy) for an intermediate data table is set at zero retention policy.
-->

### <a name="create-table-mappings"></a>Erstellen von Tabellenzuordnungen

 Da das Datenformat `json` lautet, ist eine Datenzuordnung erforderlich. Mit der `json`-Zuordnung wird jeder JSON-Pfad einem Tabellenspaltennamen zugeordnet.

#### <a name="table-mapping-for-diagnostic-logs"></a>Tabellenzuordnung für Diagnoseprotokolle

Verwenden Sie die folgende Abfrage, um die Daten der Diagnoseprotokolle der Tabelle zuzuordnen:

```kusto
.create table DiagnosticLogsRawRecords ingestion json mapping 'DiagnosticLogsRawRecordsMapping' '[{"column":"Records","path":"$.records"}]'
```

#### <a name="table-mapping-for-activity-logs"></a>Tabellenzuordnung für Aktivitätsprotokolle

Verwenden Sie die folgende Abfrage, um die Daten der Aktivitätsprotokolle der Tabelle zuzuordnen:

```kusto
.create table ActivityLogsRawRecords ingestion json mapping 'ActivityLogsRawRecordsMapping' '[{"column":"Records","path":"$.records"}]'
```

### <a name="create-the-update-policy-for-log-data"></a>Erstellen der Updaterichtlinie für Protokolldaten

#### <a name="activity-log-data-update-policy"></a>Updaterichtlinie für Aktivitätsprotokolldaten

1. Erstellen Sie eine [Funktion](/azure/kusto/management/functions), mit der die Sammlung an Aktivitätsprotokolldatensätzen so erweitert wird, dass jeder Wert der Sammlung in einer separaten Zeile angeordnet wird. Verwenden Sie den Operator [`mv-expand`](/azure/kusto/query/mvexpandoperator):

    ```kusto
    .create function ActivityLogRecordsExpand() {
        ActivityLogsRawRecords
        | mv-expand events = Records
        | project
            Timestamp = todatetime(events["time"]),
            ResourceId = tostring(events["resourceId"]),
            OperationName = tostring(events["operationName"]),
            Category = tostring(events["category"]),
            ResultType = tostring(events["resultType"]),
            ResultSignature = tostring(events["resultSignature"]),
            DurationMs = toint(events["durationMs"]),
            IdentityAuthorization = events.identity.authorization,
            IdentityClaims = events.identity.claims,
            Location = tostring(events["location"]),
            Level = tostring(events["level"])
    }
    ```

2. Fügen Sie die [Updaterichtlinie](/azure/kusto/concepts/updatepolicy) der Zieltabelle hinzu. Mit dieser Richtlinie wird die Abfrage automatisch für alle neu erfassten Daten in der Zwischentabelle *ActivityLogsRawRecords* ausgeführt, und die Ergebnisse werden in der Tabelle *ActivityLogsRecords* erfasst:

    ```kusto
    .alter table ActivityLogsRecords policy update @'[{"Source": "ActivityLogsRawRecords", "Query": "ActivityLogRecordsExpand()", "IsEnabled": "True"}]'
    ```

#### <a name="diagnostic-log-data-update-policy"></a>Updaterichtlinie für Diagnoseprotokolldaten

1. Erstellen Sie eine [Funktion](/azure/kusto/management/functions), mit der die Sammlung an Diagnoseprotokolldatensätzen so erweitert wird, dass jeder Wert der Sammlung in einer separaten Zeile angeordnet wird. Verwenden Sie den Operator [`mv-expand`](/azure/kusto/query/mvexpandoperator):
     ```kusto
    .create function DiagnosticLogRecordsExpand() {
        DiagnosticLogsRawRecords
        | mv-expand events = Records
        | project
            Timestamp = todatetime(events["time"]),
            ResourceId = tostring(events["resourceId"]),
            MetricName = tostring(events["metricName"]),
            Count = toint(events["count"]),
            Total = todouble(events["total"]),
            Minimum = todouble(events["minimum"]),
            Maximum = todouble(events["maximum"]),
            Average = todouble(events["average"]),
            TimeGrain = tostring(events["timeGrain"])
    }
    ```

2. Fügen Sie die [Updaterichtlinie](/azure/kusto/concepts/updatepolicy) der Zieltabelle hinzu. Mit dieser Richtlinie wird die Abfrage automatisch für alle neu erfassten Daten in der Zwischentabelle *DiagnosticLogsRawRecords* ausgeführt, und die Ergebnisse werden in der Tabelle *DiagnosticLogsRecords* erfasst:

    ```kusto
    .alter table DiagnosticLogsRecords policy update @'[{"Source": "DiagnosticLogsRawRecords", "Query": "DiagnosticLogRecordsExpand()", "IsEnabled": "True"}]'
    ```

## <a name="create-an-azure-event-hubs-namespace"></a>Erstellen eines Azure Event Hubs-Namespace

Mit Azure-Diagnoseprotokollen können Metriken in ein Speicherkonto oder einen Event Hub exportiert werden. In diesem Tutorial leiten wir die Metriken über einen Event Hub weiter. Sie erstellen mit den folgenden Schritten einen Event Hubs-Namespace und einen Event Hub für die Diagnoseprotokolle. Azure Monitor erstellt den Event Hub *insights-operational-logs* für die Aktivitätsprotokolle.

1. Erstellen Sie einen Event Hub, indem Sie im Azure-Portal eine Azure Resource Manager-Vorlage verwenden. Klicken Sie zum Ausführen der restlichen Schritte dieses Artikels mit der rechten Maustaste auf die Schaltfläche **In Azure bereitstellen**, und wählen Sie die Option **In neuem Fenster öffnen**. Mit der Schaltfläche **In Azure bereitstellen** gelangen Sie zum Azure-Portal.

    [![Schaltfläche „In Azure bereitstellen“](media/ingest-data-no-code/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-event-hubs-create-event-hub-and-consumer-group%2Fazuredeploy.json)

1. Erstellen Sie einen Event Hubs-Namespace und einen Event Hub für die Diagnoseprotokolle.

    ![Event Hub-Erstellung](media/ingest-data-no-code/event-hub.png)

1. Füllen Sie das Formular mit den folgenden Informationen aus. Verwenden Sie für alle Einstellungen, die nicht in der folgenden Tabelle aufgeführt sind, die jeweiligen Standardwerte.

    **Einstellung** | **Empfohlener Wert** | **Beschreibung**
    |---|---|---|
    | **Abonnement** | *Ihr Abonnement* | Wählen Sie das Azure-Abonnement aus, das Sie für Ihren Event Hub verwenden möchten.|
    | **Ressourcengruppe** | *test-resource-group* | Erstellen Sie eine neue Ressourcengruppe. |
    | **Location** | Wählen Sie die Region aus, die Ihre Anforderungen am besten erfüllt. | Erstellen Sie den Event Hubs-Namespace an demselben Standort wie die anderen Ressourcen.
    | **Namespacename** | *AzureMonitoringData* | Wählen Sie einen eindeutigen Namen, der Ihren Namespace identifiziert.
    | **Event Hub-Name** | *DiagnosticLogsData* | Der Event Hub befindet sich unter dem Namespace, der einen eindeutigen Bereichscontainer bereitstellt. |
    | **Name der Consumergruppe** | *adxpipeline* | Erstellen Sie einen Consumergruppennamen. Durch Consumergruppen können mehrere verarbeitende Anwendungen jeweils über eine separate Ansicht des Ereignisdatenstroms verfügen. |
    | | |

## <a name="connect-azure-monitor-logs-to-your-event-hub"></a>Verbinden von Azure Monitor-Protokollen mit Ihrem Event Hub

Jetzt müssen Sie für Ihre Diagnoseprotokolle und Aktivitätsprotokolle eine Verbindung mit dem Event Hub herstellen.

### <a name="connect-diagnostic-logs-to-your-event-hub"></a>Herstellen einer Verbindung für Diagnoseprotokolle mit Ihrem Event Hub

Wählen Sie eine Ressource aus, für die Metriken exportiert werden sollen. Mehrere Ressourcentypen unterstützen Diagnoseprotokolle, z. B. Event Hubs-Namespace, Azure Key Vault, Azure IoT Hub und Azure Data Explorer-Cluster. In diesem Tutorial verwenden wir einen Azure Data Explorer-Cluster als unsere Ressource.

1. Wählen Sie im Azure-Portal Ihren Kusto-Cluster aus.
1. Wählen Sie **Diagnoseeinstellungen** und dann den Link **Diagnose aktivieren**. 

    ![Diagnoseeinstellungen](media/ingest-data-no-code/diagnostic-settings.png)

1. Der Bereich **Diagnoseeinstellungen** wird geöffnet. Führen Sie die folgenden Schritte aus:
   1. Geben Sie den Diagnoseprotokolldaten den Namen *ADXExportedData*.
   1. Aktivieren Sie unter **METRIC** das Kontrollkästchen **AllMetrics** (optional).
   1. Aktivieren Sie das Kontrollkästchen **An einen Event Hub streamen**.
   1. Wählen Sie **Konfigurieren**aus.

      ![Bereich „Diagnoseeinstellungen“](media/ingest-data-no-code/diagnostic-settings-window.png)

1. Konfigurieren Sie im Bereich **Event Hub auswählen**, wie Daten aus Diagnoseprotokollen auf den von Ihnen erstellten Event Hub exportiert werden sollen:
    1. Wählen Sie in der Liste **Event Hub-Namespace auswählen** die Option *AzureMonitoringData*.
    1. Wählen Sie in der Liste **Event Hub-Name auswählen** die Option *diagnosticlogsdata*.
    1. Wählen Sie in der Liste **Event Hub-Richtlinienname auswählen**  die Option **RootManagerSharedAccessKey**.
    1. Klicken Sie auf **OK**.

1. Wählen Sie **Speichern** aus.

### <a name="connect-activity-logs-to-your-event-hub"></a>Verbinden von Aktivitätsprotokollen mit Ihrem Event Hub

1. Wählen Sie im Azure-Portal im Menü auf der linken Seite die Option **Aktivitätsprotokoll**.
1. Das Fenster **Aktivitätsprotokoll** wird geöffnet. Wählen Sie **In Event Hub exportieren**.

    ![Fenster „Aktivitätsprotokoll“](media/ingest-data-no-code/activity-log.png)

1. Das Fenster **Aktivitätsprotokoll exportieren** wird geöffnet:
 
    ![Fenster „Aktivitätsprotokoll exportieren“](media/ingest-data-no-code/export-activity-log.png)

1. Führen Sie im Fenster **Aktivitätsprotokoll exportieren** die folgenden Schritte aus:
      1. Wählen Sie Ihr Abonnement aus.
      1. Wählen Sie in der Liste **Regionen** die Option **Alle auswählen**.
      1. Aktivieren Sie das Kontrollkästchen **In einen Event Hub exportieren**.
      1. Wählen Sie **Service Bus-Namespace auswählen**, um den Bereich **Event Hub auswählen** zu öffnen.
      1. Wählen Sie im Bereich **Event Hub auswählen** Ihr Abonnement aus.
      1. Wählen Sie in der Liste **Event Hub-Namespace auswählen** die Option *AzureMonitoringData*.
      1. Wählen Sie in der Liste **Event Hub-Richtlinienname auswählen** den Event Hub-Standard-Richtliniennamen aus.
      1. Klicken Sie auf **OK**.
      1. Wählen Sie oben links im Fenster die Option **Speichern**.
   Ein Event Hub mit dem Namen *insights-operational-logs* wird erstellt.

### <a name="see-data-flowing-to-your-event-hubs"></a>Verfolgen des Datenflusses zu Ihren Event Hubs

1. Warten Sie einige Minuten, bis die Verbindung definiert und der Export des Aktivitätsprotokolls auf den Event Hub abgeschlossen wurde. Navigieren Sie zu Ihrem Event Hubs-Namespace, um die von Ihnen erstellten Event Hubs anzuzeigen.

    ![Erstellte Event Hubs](media/ingest-data-no-code/event-hubs-created.png)

1. Verfolgen Sie den Datenfluss zu Ihrem Event Hub:

    ![Event Hub-Daten](media/ingest-data-no-code/event-hubs-data.png)

## <a name="connect-an-event-hub-to-azure-data-explorer"></a>Herstellen einer Verbindung für einen Event Hub mit Azure Data Explorer

Nun müssen Sie die Datenverbindungen für Ihre Diagnose- und Aktivitätsprotokolle herstellen.

### <a name="create-the-data-connection-for-diagnostic-logs"></a>Erstellen der Datenverbindung für Diagnoseprotokolle

1. Wählen Sie in Ihrem Azure Data Explorer-Cluster mit dem Namen *kustodocs* im Menü auf der linken Seite die Option **Datenbanken**.
1. Wählen Sie im Fenster **Datenbanken** Ihre Datenbank *TestDatabase* aus.
1. Wählen Sie im Menü auf der linken Seite die Option **Datenerfassung**.
1. Klicken Sie im Fenster **Datenerfassung** auf **+ Datenverbindung hinzufügen**.
1. Geben Sie im Fenster **Datenverbindung** die folgenden Informationen ein:

    ![Event Hub-Datenverbindung](media/ingest-data-no-code/event-hub-data-connection.png)

    Datenquelle:

    **Einstellung** | **Empfohlener Wert** | **Feldbeschreibung**
    |---|---|---|
    | **Name der Datenverbindung** | *DiagnosticsLogsConnection* | Der Name der Verbindung, die Sie im Azure-Daten-Explorer erstellen möchten.|
    | **Event Hub-Namespace** | *AzureMonitoringData* | Der von Ihnen zuvor ausgewählte Name, der Ihren Namespace identifiziert. |
    | **Event Hub** | *diagnosticlogsdata* | Der von Ihnen erstellte Event Hub. |
    | **Consumergruppe** | *adxpipeline* | Die Consumergruppe, die in dem von Ihnen erstellten Event Hub definiert ist. |
    | | |

    Zieltabelle:

    Es stehen zwei Routingoptionen zur Verfügung: *statisch* und *dynamisch*. In diesem Tutorial verwenden Sie statisches Routing (Standardeinstellung), für das Sie den Tabellennamen, das Datenformat und die Zuordnung angeben. Lassen Sie das Kontrollkästchen **My data includes routing info** (Meine Daten enthalten Routinginformationen) deaktiviert.

     **Einstellung** | **Empfohlener Wert** | **Feldbeschreibung**
    |---|---|---|
    | **Tabelle** | *DiagnosticLogsRawRecords* | Die Tabelle, die Sie in der Datenbank *TestDatabase* erstellt haben. |
    | **Datenformat** | *JSON* | Das Format, das in der Tabelle verwendet wird. |
    | **Spaltenzuordnung** | *DiagnosticLogsRecordsMapping* | Die Zuordnung, die Sie in der Datenbank *TestDatabase* erstellt haben und mit der eingehende JSON-Daten den Spaltennamen und Datentypen der Tabelle *DiagnosticLogsRawRecords* zugeordnet werden.|
    | | |

1. Klicken Sie auf **Erstellen**.  

### <a name="create-the-data-connection-for-activity-logs"></a>Erstellen der Datenverbindung für Aktivitätsprotokolle

Wiederholen Sie die Schritte des Abschnitts „Erstellen der Datenverbindung für Diagnoseprotokolle“, um die Datenverbindung für Ihre Aktivitätsprotokolle zu erstellen.

1. Verwenden Sie im Fenster **Datenverbindung** die folgenden Einstellungen:

    Datenquelle:

    **Einstellung** | **Empfohlener Wert** | **Feldbeschreibung**
    |---|---|---|
    | **Name der Datenverbindung** | *ActivityLogsConnection* | Der Name der Verbindung, die Sie im Azure-Daten-Explorer erstellen möchten.|
    | **Event Hub-Namespace** | *AzureMonitoringData* | Der von Ihnen zuvor ausgewählte Name, der Ihren Namespace identifiziert. |
    | **Event Hub** | *insights-operational-logs* | Der von Ihnen erstellte Event Hub. |
    | **Consumergruppe** | *$Default* | Die Standardconsumergruppe. Bei Bedarf können Sie auch eine andere Consumergruppe erstellen. |
    | | |

    Zieltabelle:

    Es stehen zwei Routingoptionen zur Verfügung: *statisch* und *dynamisch*. In diesem Tutorial verwenden Sie statisches Routing (Standardeinstellung), für das der Tabellenname, das Datenformat und die Zuordnung angegeben werden müssen. Lassen Sie das Kontrollkästchen **My data includes routing info** (Meine Daten enthalten Routinginformationen) deaktiviert.

     **Einstellung** | **Empfohlener Wert** | **Feldbeschreibung**
    |---|---|---|
    | **Tabelle** | *ActivityLogsRawRecords* | Die Tabelle, die Sie in der Datenbank *TestDatabase* erstellt haben. |
    | **Datenformat** | *JSON* | Das Format, das in der Tabelle verwendet wird. |
    | **Spaltenzuordnung** | *ActivityLogsRawRecordsMapping* | Die Zuordnung, die Sie in der Datenbank *TestDatabase* erstellt haben und mit der eingehende JSON-Daten den Spaltennamen und Datentypen der Tabelle *ActivityLogsRawRecords* zugeordnet werden.|
    | | |

1. Klicken Sie auf **Erstellen**.  

## <a name="query-the-new-tables"></a>Abfragen der neuen Tabellen

Sie verfügen nun über eine Pipeline, durch die Daten fließen. Da die Erfassung über den Cluster standardmäßig fünf Minuten dauert, sollten Sie den Datenfluss einige Minuten lang zulassen, bevor Sie mit dem Abfragen beginnen.

### <a name="an-example-of-querying-the-diagnostic-logs-table"></a>Beispiel für Abfrage der Tabelle für Diagnoseprotokolle

Mit der folgenden Abfrage werden Daten zur Abfragedauer aus den Datensätzen von Diagnoseprotokollen in Azure Data Explorer analysiert:

```kusto
DiagnosticLogsRecords
| where Timestamp > ago(15m) and MetricName == 'QueryDuration'
| summarize avg(Average)
```

Abfrageergebnisse:

|   |   |
| --- | --- |
|   |  avg_Average |
|   | 00:06.156 |
| | |

### <a name="an-example-of-querying-the-activity-logs-table"></a>Beispiel für eine Abfrage der Tabelle für Aktivitätsprotokolle

Mit der folgenden Abfrage werden die Daten aus den Datensätzen von Aktivitätsprotokollen in Azure Data Explorer analysiert:

```kusto
ActivityLogsRecords
| where OperationName == 'MICROSOFT.EVENTHUB/NAMESPACES/AUTHORIZATIONRULES/LISTKEYS/ACTION'
| where ResultType == 'Success'
| summarize avg(DurationMs)
```

Abfrageergebnisse:

|   |   |
| --- | --- |
|   |  avg(DurationMs) |
|   | 768.333 |
| | |

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich folgenden Artikel, wie Sie viele weitere Abfragen für Daten schreiben können, die Sie aus Azure Data Explorer extrahieren:

> [!div class="nextstepaction"]
> [Schreiben von Abfragen für den Azure-Daten-Explorer](write-queries.md)
