---
title: Archivieren von Daten aus dem Log Analytics-Arbeitsbereich in Azure Storage mithilfe von Logic Apps
description: In diesem Artikel wird eine Methode zur Verwendung von Azure Logic Apps zum Abfragen von Daten in einem Log Analytics-Arbeitsbereich und zum Senden der Daten an Azure Storage beschrieben.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/02/2020
ms.openlocfilehash: 04f1eb0d9db00a2be1a4619cafe38aa18145fc78
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96185996"
---
# <a name="archive-data-from-log-analytics-workspace-to-azure-storage-using-logic-app"></a>Archivieren von Daten aus dem Log Analytics-Arbeitsbereich in Azure Storage mithilfe von Logic Apps
In diesem Artikel wird eine Methode zur Verwendung von [Azure Logic Apps](../../logic-apps/index.yml) zum Abfragen von Daten in einem Log Analytics-Arbeitsbereich in Azure Monitor und zum Senden der Daten an Azure Storage beschrieben. Verwenden Sie diesen Vorgang, wenn Sie Ihre Azure Monitor Logs-Daten für Überprüfungs- und Complianceszenarios exportieren müssen oder wenn Sie zulassen müssen, dass diese Daten von einem anderen Dienst abgerufen werden können.  

## <a name="other-export-methods"></a>Andere Exportmethoden
Die in diesem Artikel beschriebene Methode beschreibt einen geplanten Export aus einer Protokollabfrage mit einer Logik-App. Darüber hinaus gibt es die folgenden Möglichkeiten zum Exportieren von Daten für bestimmte Szenarios:

- Wenn Sie alle Daten aus dem Log Analytics-Arbeitsbereich in ein Azure-Speicherkonto oder einen Event Hub exportieren möchten, verwenden Sie das Datenexportfeature im Log Analytics-Arbeitsbereich von Azure Monitor Logs. Informationen hierzu finden Sie unter [Datenexport im Log Analytics-Arbeitsbereich in Azure Monitor (Vorschau)](logs-data-export.md).
- Einmaliger Export mit einer Logik-App. Informationen hierzu finden Sie unter [Azure Monitor Logs-Connector für Logic Apps und Power Automate](logicapp-flow-connector.md)
- Einmaliger Export auf lokalen Computer mit einem PowerShell-Skript. Informationen hierzu finden Sie unter [Invoke-AzOperationalInsightsQueryExport]](https://www.powershellgallery.com/packages/Invoke-AzOperationalInsightsQueryExport).

## <a name="overview"></a>Übersicht
Für dieses Verfahren wird der [Azure Monitor Logs-Connector](/connectors/azuremonitorlogs/) verwendet, mit dem Sie eine Protokollabfrage über eine Logik-App ausführen und das Ergebnis in anderen Aktionen im Workflow verwenden können. Der [Azure Blob Storage-Connector](/connectors/azureblob/) wird in diesem Verfahren verwendet, um das Abfrageergebnis an Azure Storage zu senden. Die anderen Aktionen werden in den folgenden Abschnitten beschrieben.

![Übersicht: Logic Apps](media/logs-export-logicapp/logic-app-overview.png)

Beim Exportieren von Daten aus einem Log Analytics-Arbeitsbereich sollten Sie Ihre Protokolldaten filtern und aggregieren und Ihre Abfrage insofern optimieren, als dass Sie die Menge der durch Ihren Logic Apps-Workflow verarbeiteten Daten auf die erforderlichen Daten begrenzen. Wenn Sie beispielsweise Anmeldeereignisse archivieren müssen, können Sie mithilfe der folgenden Abfrage nach erforderlichen Ereignissen filtern und nur die erforderlichen Felder projizieren: 

```json
SecurityEvent
| where EventID == 4624 or EventID == 4625
| project TimeGenerated , Account , AccountType , Computer
```

Wenn Sie die Daten nach einem Zeitplan exportieren, verwenden Sie die Funktion „ingestion_time()“ in Ihrer Abfrage, um sicherzustellen, dass spät eintreffende Daten nicht übersehen werden. Wenn Daten aufgrund eines Netzwerk- oder Plattformproblems mit Verzögerung eintreffen, wird mithilfe der Erfassungszeit sichergestellt, dass sie in die nächste Ausführung von Logic Apps einbezogen werden. Ein Beispiel hierzu finden Sie unter [Hinzufügen einer Azure Monitor Logs-Aktion](#add-azure-monitor-logs-action).

## <a name="prerequisites"></a>Voraussetzungen
Im Folgenden sind die Voraussetzungen aufgeführt, die vor der Durchführung dieses Verfahrens erfüllt sein müssen.

- Log Analytics-Arbeitsbereich. Der Benutzer, der die Logik-App erstellt, muss mindestens über eine Leseberechtigung für den Arbeitsbereich verfügen. 
- Azure-Speicherkonto. Das Speicherkonto muss sich nicht im selben Abonnement wie der Log Analytics-Arbeitsbereich befinden. Der Benutzer, der die Logik-App erstellt, muss über eine Schreibberechtigung für das Speicherkonto verfügen. 


## <a name="connector-limits"></a>Grenzwerte des Connectors
Beim Log Analytics-Arbeitsbereich und den Protokollabfragen in Azure Monitor handelt es sich um mehrinstanzfähige Dienste mit Grenzwerten zum Schützen und Isolieren von Kunden und zur Gewährleistung von Servicequalität. Beim Abfragen großer Datenmengen sollten Sie die folgenden Grenzwerte verwenden, die sich auf die Konfiguration der Logic Apps-Serie und Ihrer Protokollabfrage auswirken können:

- Protokollabfragen können nicht mehr als 500.000 Zeilen zurückgeben.
- Protokollabfragen können nicht mehr als 64.000.000 Bytes zurückgeben.
- Protokollabfragen können standardmäßig nicht länger als 10 Minuten ausgeführt werden. 
- Der Log Analytics-Connector ist auf 100 Aufrufe pro Minute begrenzt.


## <a name="create-container-in-the-storage-account"></a>Erstellen eines Containers im Speicherkonto
Verwenden Sie das Verfahren in [Erstellen eines Containers](../../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container), um Ihrem Speicherkonto zum Speichern der exportierten Daten einen Container hinzuzufügen. Für den Container in diesem Artikel wird der Name **loganalytics-data** verwendet. Sie können jedoch einen beliebigen anderen Namen verwenden.


## <a name="create-logic-app"></a>Erstellen einer Logik-App

Navigieren Sie im Azure-Portal zu **Logic Apps**, und klicken Sie auf **Hinzufügen**. Wählen Sie ein **Abonnement**, eine **Ressourcengruppe** und eine **Region** aus, um die neue Logik-App zu speichern, und geben Sie ihr einen eindeutigen Namen. Sie können die Einstellung **Log Analytics** aktivieren, um Informationen über Laufzeitdaten und Ereignisse zu sammeln, wie unter [Einrichten von Azure Monitor-Protokollen und Sammeln von Diagnosedaten für Azure Logic Apps](../../logic-apps/monitor-logic-apps-log-analytics.md) beschrieben. Diese Einstellung ist für die Verwendung des Connectors für Azure Monitor-Protokolle nicht erforderlich.

![Erstellen einer Logik-App](media/logs-export-logicapp/create-logic-app.png)


Klicken Sie auf **Überprüfen + erstellen** und danach auf **Erstellen**. Wenn die Bereitstellung abgeschlossen ist, klicken Sie auf **Zu Ressource wechseln**, um den **Designer für Logik-Apps** zu öffnen.

## <a name="create-a-trigger-for-the-logic-app"></a>Erstellen eines Triggers für die Logik-App
Wählen Sie unter **Starten Sie mit einem gängigen Trigger** die Option **Wiederholung** aus. Dadurch wird eine Logik-App erstellt, die in regelmäßigen Abständen automatisch ausgeführt wird. Wählen Sie im Feld **Häufigkeit** der Aktion **Stunde** aus, und geben Sie in das Feld **Intervall** den Wert **1** ein, um den Workflow einmal pro Tag auszuführen.

![Wiederholungsaktion](media/logs-export-logicapp/recurrence-action.png)


### <a name="add-azure-monitor-logs-action"></a>Hinzufügen der Aktion „Azure Monitor-Protokolle“
Klicken Sie auf **+ Neuer Schritt**, um eine Aktion hinzuzufügen, die nach der Wiederholungsaktion ausgeführt wird. Geben Sie unter **Aktion auswählen** den Text **azure monitor** ein, und wählen Sie dann **Azure Monitor-Protokolle** aus.

![Aktion „Azure Monitor-Protokolle“](media/logs-export-logicapp/select-azure-monitor-connector.png)

Klicken Sie auf **Azure Log Analytics – Abfrage ausführen und Ergebnisse auflisten**.

![Screenshot: Neue Aktion, die einem Schritt in Logik-App-Designer hinzugefügt wurde. Azure Monitor Logs ist unter „Aktion auswählen“ hervorgehoben.](media/logs-export-logicapp/select-query-action-list.png)

Sie werden aufgefordert, mit dem Konto, das im Workflow zum Ausführen der Abfrage verwendet wird, einen Mandanten auszuwählen und Zugriff auf den Log Analytics-Arbeitsbereich zu gewähren.


## <a name="add-azure-monitor-logs-action"></a>Hinzufügen der Aktion „Azure Monitor-Protokolle“
Mit der Azure Monitor Logs-Aktion können Sie festlegen, welche Abfrage ausgeführt werden soll. Die in diesem Beispiel verwendete Protokollabfrage wird für eine stündliche Serie optimiert und sammelt die für die jeweilige Ausführungszeit erfassten Daten. Wenn der Workflow beispielsweise um 4:35 ausgeführt wird, liegt der Zeitbereich zwischen 4:00 und 5:00. Wenn Sie die Logik-App so ändern, dass die Ausführung mit einer anderen Häufigkeit erfolgt, müssen Sie auch die Abfrage entsprechend ändern. Wenn Sie beispielsweise festlegen, dass die Serie täglich ausgeführt wird, legen Sie für „startTime“ in der Abfrage „startofday(make_datetime(year,month,day,0,0))“ fest. 

Wählen Sie das **Abonnement** und die **Ressourcengruppe** für Ihren Log Analytics-Arbeitsbereich aus. Wählen Sie *Log Analytics-Arbeitsbereich* als **Ressourcentyp** und anschließend unter **Ressourcenname** den Namen des Arbeitsbereichs aus.

Fügen Sie dem Fenster **Abfrage** die unten angegebene Protokollabfrage hinzu.  

```Kusto
let dt = now();
let year = datetime_part('year', dt);
let month = datetime_part('month', dt);
let day = datetime_part('day', dt);
let hour = datetime_part('hour', dt);
let startTime = make_datetime(year,month,day,hour,0)-1h;
let endTime = startTime + 1h - 1tick;
AzureActivity
| where ingestion_time() between(startTime .. endTime)
| project 
    TimeGenerated,
    BlobTime = startTime, 
    OperationName ,
    OperationNameValue ,
    Level ,
    ActivityStatus ,
    ResourceGroup ,
    SubscriptionId ,
    Category ,
    EventSubmissionTimestamp ,
    ClientIpAddress = parse_json(HTTPRequest).clientIpAddress ,
    ResourceId = _ResourceId 
```

Mit dem **Zeitbereich** werden die Datensätze angegeben, die basierend auf der Spalte **TimeGenerated** in die Abfrage einbezogen werden. Diese Einstellung muss auf einen Wert festgelegt werden, der gleich oder größer als der in der Abfrage ausgewählte Zeitbereich ist. Da in dieser Abfrage die Spalte **TimeGenerated** nicht verwendet wird, ist die Option **In Abfrage festlegen** nicht verfügbar. Weitere Informationen zum Zeitbereich finden Sie unter [Abfragebereich](../log-query/scope.md). 

Wählen Sie für den **Zeitbereich** die Option **Letzte 4 Stunden** aus. Damit wird sichergestellt, dass Datensätze, bei denen der Wert für die Erfassungszeit größer ist als der Wert für **TimeGenerated**, in die Ergebnisse einbezogen werden.
   
![Screenshot: Einstellungen für die neue Azure Monitor Logs-Aktion mit dem Namen „Abfrage ausführen und Ergebnisse visualisieren“](media/logs-export-logicapp/run-query-list-action.png)


### <a name="add-parse-json-activity-optional"></a>Hinzufügen der Aktivität „JSON analysieren“ (optional)
Die Ausgabe der Aktion **Abfrage ausführen und Ergebnisse auflisten** ist in JSON formatiert. Sie können diese Daten im Rahmen der Vorbereitung auf die Aktion **Compose** analysieren und bearbeiten. 

Sie können ein JSON-Schema angeben, das die Nutzdaten beschreibt, die Sie zu empfangen erwarten. Der Designer analysiert JSON-Inhalte mithilfe dieses Schemas und generiert benutzerfreundliche Token, die die Eigenschaften in Ihrem JSON-Inhalt darstellen. Sie können dann in Ihrem Logik-App-Workflow ganz einfach auf diese Eigenschaften verweisen und sie verwenden. 


Klicken Sie auf **+ Neuer Schritt** und anschließend auf **+ Aktion hinzufügen**. Geben Sie unter **Aktion auswählen** **json** ein, und wählen Sie dann **JSON analysieren** aus.

![Auswählen der Aktivität „JSON analysieren“](media/logs-export-logicapp/select-parse-json.png)

Klicken Sie in das Feld **Inhalt**, um eine Liste mit Werten aus früheren Aktivitäten anzuzeigen. Wählen Sie in der Aktion **Abfrage ausführen und Ergebnisse auflisten** die Option **Body** (Textkörper) aus. Dies ist die Ausgabe der Protokollabfrage.

[![Auswählen eines Textkörpers](media/logs-export-logicapp/select-body.png)](media/logs-export-logicapp/select-body.png#lightbox)

5.  Klicken Sie auf **Beispielnutzdaten zum Generieren eines Schemas verwenden**. Führen Sie die Protokollabfrage aus, und kopieren Sie die Ausgabe zur Verwendung für die Beispielnutzdaten.  Für die Beispielabfrage hier können Sie die folgende Ausgabe verwenden:


```json
{
    "TimeGenerated": "2020-09-29T23:11:02.578Z",
    "BlobTime": "2020-09-29T23:00:00Z",
    "OperationName": "Returns Storage Account SAS Token",
    "OperationNameValue": "MICROSOFT.RESOURCES/DEPLOYMENTS/WRITE",
    "Level": "Informational",
    "ActivityStatus": "Started",
    "ResourceGroup": "monitoring",
    "SubscriptionId": "00000000-0000-0000-0000-000000000000",
    "Category": "Administrative",
    "EventSubmissionTimestamp": "2020-09-29T23:11:02Z",
    "ClientIpAddress": "192.168.1.100",
    "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/monitoring/providers/microsoft.storage/storageaccounts/my-storage-account"
}
```



![Analysieren der JSON-Nutzdaten](media/logs-export-logicapp/parse-json-payload.png)

## <a name="add-the-compose-action"></a>Hinzufügen der Aktion „Compose“
Bei der Aktion **Compose** wird mithilfe der analysierten JSON-Ausgabe das Objekt erstellt, das im Blob gespeichert werden muss.

Klicken Sie auf **+ Neuer Schritt** und anschließend auf **+ Aktion hinzufügen**. Geben Sie unter **Aktion auswählen** **compose** ein, und wählen Sie dann die Aktion **Compose** aus.

![Auswählen der Aktion „Compose“](media/logs-export-logicapp/select-compose.png)


Klicken Sie in das Feld **Eingaben**, um eine Liste mit Werten aus früheren Aktivitäten anzuzeigen. Wählen Sie in der Aktion **JSON analysieren** die Option **Body** (Textkörper) aus. Dies ist die analysierte Ausgabe der Protokollabfrage.

[![Auswählen des Textkörpers für die Aktion „Compose“](media/logs-export-logicapp/select-body-compose.png)](media/logs-export-logicapp/select-body-compose.png#lightbox)


## <a name="add-the-create-blob-action"></a>Hinzufügen der Aktion „Blob erstellen“
Mit der Aktion „Blob erstellen“ wird die zusammengesetzte JSON-Ausgabe in den Speicher geschrieben.

Klicken Sie auf **+ Neuer Schritt** und anschließend auf **+ Aktion hinzufügen**. Geben Sie unter **Aktion auswählen** **blob** ein, und wählen Sie dann die Aktion **Blob erstellen** aus.

![Auswählen von „Blob erstellen“](media/logs-export-logicapp/select-create-blob.png)

Geben Sie in **Verbindungsname** einen Namen für die Verbindung mit Ihrem Speicherkonto ein, und klicken Sie anschließend auf das Ordnersymbol im Feld **Ordnerpfad**, um den Container in Ihrem Speicherkonto auszuwählen. Klicken Sie in das Feld **Blobname**, um eine Liste mit Werten aus früheren Aktivitäten anzuzeigen. Klicken Sie auf **Ausdruck**, und geben Sie einen Ausdruck ein, der Ihrem Zeitintervall entspricht. Für diese Abfrage, die stündlich ausgeführt wird, wird mit dem folgenden Ausdruck der Blobname auf pro vorheriger Stunde festgelegt: 

```json
subtractFromTime(formatDateTime(utcNow(),'yyyy-MM-ddTHH:00:00'), 1,'Hour')
```

[![Blobausdruck](media/logs-export-logicapp/blob-expression.png)](media/logs-export-logicapp/blob-expression.png#lightbox)

Klicken Sie in das Feld **Blobinhalt**, um eine Liste mit Werten aus früheren Aktivitäten anzuzeigen, und wählen Sie anschließend im Abschnitt **Compose** die Option **Ausgaben** aus.


![Erstellen des Blobausdrucks](media/logs-export-logicapp/create-blob.png)


## <a name="test-the-logic-app"></a>Testen der Logik-App
Testen Sie den Workflow, indem Sie auf **Ausführen** klicken. Wenn der Workflow Fehler aufweist, werden diese im Schritt mit dem Problem angegeben. Sie können die Ausführungen anzeigen und einen Drilldown auf die einzelnen Schritte ausführen, um zur Untersuchung von Fehlern die Eingabe und die Ausgabe anzuzeigen. Informationen hierzu finden Sie ggf. unter [Diagnostizieren und Beheben von Workflowfehlern in Azure Logic Apps](../../logic-apps/logic-apps-diagnosing-failures.md).

[![Ausführungsverlauf](media/logs-export-logicapp/runs-history.png)](media/logs-export-logicapp/runs-history.png#lightbox)


## <a name="view-logs-in-storage"></a>Anzeigen von Protokollen im Speicher
Wechseln Sie zum Menü **Speicherkonten** im Azure-Portal, und wählen Sie Ihr Speicherkonto aus. Klicken Sie auf die Kachel **Blobs**, und wählen Sie den Container aus, den Sie in der Aktion „Blob erstellen“ angegeben haben. Wählen einen der Blobs aus, und klicken Sie auf **Blob bearbeiten**.

[![Blobdaten](media/logs-export-logicapp/blob-data.png)](media/logs-export-logicapp/blob-data.png#lightbox)

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über [Protokollabfragen in Azure Monitor](../log-query/log-query-overview.md).
- Weitere Informationen zu [Logik-Apps](../../logic-apps/index.yml)
- Weitere Informationen zu [Power Automate](https://flow.microsoft.com)