---
title: Zugreifen auf Diagnoseprotokolle für Azure Data Lake Storage Gen1 | Microsoft-Dokumentation
description: 'Grundlegendes zum Einrichten von Diagnoseprotokollen für Azure Data Lake Storage Gen1 und Zugriff auf diese '
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: f6e75eb1-d0ae-47cf-bdb8-06684b7c0a94
ms.service: data-lake-store
ms.devlang: na
ms.topic: how-to
ms.date: 03/26/2018
ms.author: twooley
ms.openlocfilehash: 434917c1cee26a4a8eeb7f27808e3fcb487f3f55
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96350040"
---
# <a name="accessing-diagnostic-logs-for-azure-data-lake-storage-gen1"></a>Zugreifen auf Diagnoseprotokolle für Azure Data Lake Storage Gen1
Erfahren Sie, wie Sie die Diagnoseprotokollierung für Ihr Azure Data Lake Storage Gen1-Konto aktivieren und wie Sie die für Ihr Konto erfassten Protokolle anzeigen.

Organisationen können die Diagnoseprotokollierung für ihre Azure Data Lake Storage Gen1-Konten zum Erfassen von Datenzugriffs-Überwachungspfaden aktivieren, die Informationen wie die Liste der Benutzer, die auf die Daten zugreifen, die Häufigkeit des Zugriffs auf die Daten, die Größe der im Konto gespeicherten Datenmenge usw. liefern. Wenn die Diagnose- und/oder Anforderungsprotokollierung aktiviert ist, erfolgt diese auf Grundlage der besten Leistung. Protokolleinträge für Anforderungen und Diagnose werden nur erstellt, wenn Anforderungen für den Dienstendpunkt vorhanden sind.

## <a name="prerequisites"></a>Voraussetzungen
* **Ein Azure-Abonnement**. Siehe [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/).
* **Ein Azure Data Lake Storage Gen1-Konto**. Befolgen Sie die Anweisungen unter [Erste Schritte mit Azure Data Lake Storage Gen1 über das Azure-Portal](data-lake-store-get-started-portal.md).

## <a name="enable-diagnostic-logging-for-your-data-lake-storage-gen1-account"></a>Aktivieren der Diagnoseprotokollierung für Ihr Data Lake Storage Gen1-Konto
1. Melden Sie sich am neuen [Azure-Portal](https://portal.azure.com)an.
2. Öffnen Sie Ihr Data Lake Storage Gen1-Konto, und klicken Sie auf Ihrem Data Lake Storage Gen1-Kontoblatt auf **Diagnoseeinstellungen**.
3. Klicken Sie auf dem Blatt **Diagnoseeinstellungen** auf **Diagnose aktivieren**.

    ![Screenshot des Data Lake Storage Gen1-Kontos mit hervorgehobenen Optionen „Diagnoseeinstellung“ und „Diagnose aktivieren“.](./media/data-lake-store-diagnostic-logs/turn-on-diagnostics.png "Aktivieren von Diagnoseprotokollen")

3. Nehmen Sie auf dem Blatt **Diagnoseeinstellungen** die folgenden Änderungen vor, um die Diagnoseprotokollierung zu konfigurieren.
   
    ![Screenshot des Abschnitts mit Diagnoseeinstellungen mit hervorgehobenem Textfeld „Name“ und hervorgehobener Option „Speichern“.](./media/data-lake-store-diagnostic-logs/enable-diagnostic-logs.png "Aktivieren von Diagnoseprotokollen")
   
   * Geben Sie unter **Name** einen Wert für die Konfiguration des Diagnoseprotokolls ein.
   * Sie können die Daten auf verschiedene Arten speichern/verarbeiten.
     
        * Wählen Sie die Option zum **Archivieren in einem Speicherkonto**, um Protokolle in einem Azure Storage-Konto zu speichern. Verwenden Sie diese Option, wenn Sie die Daten archivieren möchten, die zu einem späteren Zeitpunkt der Batchverarbeitung unterzogen werden sollen. Bei Auswahl dieser Option müssen Sie ein Azure Storage-Konto zum Speichern der Protokolle bereitstellen.
        
        * Wählen Sie die Option zum **Streamen an einen Event Hub**, um die Protokolldaten an einen Azure Event Hub zu streamen. Wahrscheinlich werden Sie diese Option verwenden, wenn Sie eine Downstreamverarbeitungs-Pipeline einsetzen, um eingehende Protokolle in Echtzeit zu analysieren. Wenn Sie diese Option auswählen, müssen Sie die Details für den Azure Event Hub angeben, den Sie verwenden möchten.

        * Wählen Sie die Option zum **Senden an Log Analytics**, um die generierten Protokolldaten mithilfe des Azure Monitor-Diensts zu analysieren. Wenn Sie diese Option auswählen, müssen Sie die Details für den Log Analytics-Arbeitsbereich angeben, den Sie zum Ausführen der Protokollanalyse verwenden. Informationen zum Verwenden von Azure Monitor-Protokollen finden Sie unter [Anzeigen oder Analysieren der mit Azure Monitor-Protokollen gesammelten Daten](../azure-monitor/log-query/log-analytics-tutorial.md).
     
   * Geben Sie an, ob Sie Überwachungsprotokolle oder Anforderungsprotokolle oder beides abrufen möchten.
   * Geben Sie die Anzahl der Tage an, für die die Daten beibehalten werden müssen. Die Aufbewahrung ist nur zutreffend, wenn Sie Azure-Speicherkonten zum Archivieren von Protokolldaten verwenden.
   * Klicken Sie auf **Speichern**.

Nachdem Sie die Diagnoseeinstellungen aktiviert haben, können Sie die Protokolle auf der Registerkarte **Diagnoseprotokolle** verfolgen.

## <a name="view-diagnostic-logs-for-your-data-lake-storage-gen1-account"></a>Anzeigen der Diagnoseprotokolle für Ihr Data Lake Storage Gen1-Konto
Es gibt zwei Möglichkeiten, die Protokolldaten Ihres Data Lake Storage Gen1-Kontos anzuzeigen.

* Über die Ansicht „Einstellungen“ des Data Lake Storage Gen1-Kontos
* Über das Azure Storage-Konto, in dem die Daten gespeichert sind

### <a name="using-the-data-lake-storage-gen1-settings-view"></a>Verwenden der Ansicht „Einstellungen“ von Data Lake Storage Gen1
1. Klicken Sie in Ihrem Data Lake Storage Gen1-Konto auf dem Blatt **Einstellungen** auf **Diagnoseprotokolle**.
   
    ![Anzeigen von Diagnoseprotokollen](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs.png "Anzeigen von Diagnoseprotokollen") 
2. Auf dem Blatt **Diagnoseprotokolle** sollten die Protokolle nach **Überwachungsprotokollen** und **Anforderungsprotokollen** kategorisiert sein.
   
   * Anforderungsprotokolle erfassen jede API-Anforderung im Data Lake Storage Gen1-Konto.
   * Überwachungsprotokolle ähneln Anforderungsprotokollen, bieten aber eine viel detailliertere Aufschlüsselung der Vorgänge, die auf dem Data Lake Storage Gen1-Konto ausgeführt werden. Ein einzelner API-Hochladeaufruf in Anforderungsprotokollen könnte möglicherweise in den Überwachungsprotokollen in mehreren „Anfügen“-Vorgängen resultieren.
3. Klicken Sie zum Herunterladen der Protokolle für jeden Protokolleintrag auf den Link **Herunterladen**.

### <a name="from-the-azure-storage-account-that-contains-log-data"></a>Im Azure Storage-Konto, das die Protokolldaten enthält
1. Öffnen Sie das Azure Storage-Kontoblatt, das Data Lake Storage Gen1 zur Protokollierung zugeordnet ist, und klicken Sie dann auf „Blobs“. Auf dem Blatt **Blob-Dienst** werden zwei Container aufgelistet.
   
    ![Screenshot des Blatts „Data Lake Storage Gen1“ mit ausgewählter Option „Blobs“ und des Blatts „Blobdienst“ mit den hervorgehobenen Namen der beiden Blobdienste.](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs-storage-account.png "Anzeigen von Diagnoseprotokollen")
   
   * Der Container **insights-logs-audit** enthält die Überwachungsprotokolle.
   * Der Container **insights-logs-requests** enthält die Anforderungsprotokolle.
2. Innerhalb dieser Container werden die Protokolle in der folgenden Struktur gespeichert.
   
    ![Screenshot der im Container gespeicherten Protokollstruktur.](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs-storage-account-structure.png "Anzeigen von Diagnoseprotokollen")
   
    Der vollständige Pfad zu einem Überwachungsprotokoll könnte z.B. folgendermaßen lauten: `https://adllogs.blob.core.windows.net/insights-logs-audit/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/mydatalakestorage/y=2016/m=07/d=18/h=04/m=00/PT1H.json`
   
    Entsprechend kann der vollständige Pfad zu einem Anforderungsprotokoll wie folgt lauten: `https://adllogs.blob.core.windows.net/insights-logs-requests/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/mydatalakestorage/y=2016/m=07/d=18/h=14/m=00/PT1H.json`.

## <a name="understand-the-structure-of-the-log-data"></a>Grundlegendes zur Struktur der Protokolldaten
Die Überwachungs- und Anforderungsprotokolle liegen im JSON-Format vor. In diesem Abschnitt betrachten wir die Struktur von JSON für Anforderungs- und Überwachungsprotokolle.

### <a name="request-logs"></a>Anforderungsprotokolle
Hier ist ein Beispiel für einen Eintrag im JSON-formatierten Anforderungsprotokoll. Jedes Blob hat ein Stammobjekt namens **records** , das ein Array mit Protokollobjekten enthält.

```json
{
"records": 
  [        
    . . . .
    ,
    {
        "time": "2016-07-07T21:02:53.456Z",
        "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/<data_lake_storage_gen1_account_name>",
        "category": "Requests",
        "operationName": "GETCustomerIngressEgress",
        "resultType": "200",
        "callerIpAddress": "::ffff:1.1.1.1",
        "correlationId": "4a11c709-05f5-417c-a98d-6e81b3e29c58",
        "identity": "1808bd5f-62af-45f4-89d8-03c5e81bac30",
        "properties": {"HttpMethod":"GET","Path":"/webhdfs/v1/Samples/Outputs/Drivers.csv","RequestContentLength":0,"ClientRequestId":"3b7adbd9-3519-4f28-a61c-bd89506163b8","StartTime":"2016-07-07T21:02:52.472Z","EndTime":"2016-07-07T21:02:53.456Z"}
    }
    ,
    . . . .
  ]
}
```

#### <a name="request-log-schema"></a>Anforderungsprotokollschema
| Name | type | BESCHREIBUNG |
| --- | --- | --- |
| time |String |Der Zeitstempel (UTC) des Protokolls. |
| resourceId |String |Die ID der Ressource, auf der der Vorgang stattfand. |
| category |String |Die Protokollkategorie. Beispiel: **Anforderungen**. |
| operationName |String |Der Name des protokollierten Vorgangs. Beispielsweise „getfilestatus“. |
| resultType |String |Der Status des Vorgangs, beispielsweise 200. |
| callerIpAddress |String |Die IP-Adresse des Clients, der die Anforderung gestellt hat. |
| correlationId |String |Die ID des Protokolls, die verwendet werden kann, um einen Satz verwandter Protokolleinträge zu gruppieren. |
| identity |Object |Die Identität, die das Protokoll erstellt hat. |
| properties |JSON |Weitere Informationen siehe unten. |

#### <a name="request-log-properties-schema"></a>Eigenschaftenschema des Anforderungsprotokolls
| Name | type | BESCHREIBUNG |
| --- | --- | --- |
| HttpMethod |String |Die HTTP-Methode, die für den Vorgang verwendet werden. Beispiel: GET. |
| `Path` |String |Der Pfad, in dem der Vorgang durchgeführt wurde. |
| RequestContentLength |INT |Die Inhaltslänge der HTTP-Anforderung. |
| ClientRequestId |String |Die ID, die diese Anforderung eindeutig identifiziert. |
| StartTime |String |Der Zeitpunkt, zu dem der Server die Anforderung empfangen hat. |
| EndTime |String |Der Zeitpunkt, zu dem der Server eine Antwort gesendet hat. |

### <a name="audit-logs"></a>Überwachungsprotokolle
Hier ist ein Beispiel für einen Eintrag im JSON-formatierten Überwachungsprotokoll. Jeder Blob hat ein Stammobjekt namens **records** , das ein Array von Protokollobjekten enthält.

```json
{
"records": 
  [        
    . . . .
    ,
    {
        "time": "2016-07-08T19:08:59.359Z",
        "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/<data_lake_storage_gen1_account_name>",
        "category": "Audit",
        "operationName": "SeOpenStream",
        "resultType": "0",
        "resultSignature": "0",
        "correlationId": "381110fc03534e1cb99ec52376ceebdf;Append_BrEKAmg;25.66.9.145",
        "identity": "A9DAFFAF-FFEE-4BB5-A4A0-1B6CBBF24355",
        "properties": {"StreamName":"adl://<data_lake_storage_gen1_account_name>.azuredatalakestore.net/logs.csv"}
    }
    ,
    . . . .
  ]
}
```

#### <a name="audit-log-schema"></a>Überwachungsprotokollschema
| Name | type | BESCHREIBUNG |
| --- | --- | --- |
| time |String |Der Zeitstempel (UTC) des Protokolls. |
| resourceId |String |Die ID der Ressource, auf der der Vorgang stattfand. |
| category |String |Die Protokollkategorie. Beispiel: **Überwachung**. |
| operationName |String |Der Name des protokollierten Vorgangs. Beispielsweise „getfilestatus“. |
| resultType |String |Der Status des Vorgangs, beispielsweise 200. |
| resultSignature |String |Weitere Details zum Vorgang |
| correlationId |String |Die ID des Protokolls, die verwendet werden kann, um einen Satz verwandter Protokolleinträge zu gruppieren. |
| identity |Object |Die Identität, die das Protokoll erstellt hat. |
| properties |JSON |Weitere Informationen siehe unten. |

#### <a name="audit-log-properties-schema"></a>Eigenschaftenschema des Überwachungsprotokolls
| Name | type | BESCHREIBUNG |
| --- | --- | --- |
| StreamName |String |Der Pfad, in dem der Vorgang durchgeführt wurde. |

## <a name="samples-to-process-the-log-data"></a>Beispiele für die Verarbeitung der Protokolldaten
Beim Senden von Protokollen aus Azure Data Lake Storage Gen1 an Azure Monitor-Protokolle (Informationen zum Verwenden von Azure Monitor-Protokollen finden Sie unter [Anzeigen oder Analysieren der mit der Azure Monitor-Protokollsuche gesammelten Daten](../azure-monitor/log-query/log-analytics-tutorial.md)) gibt die folgende Abfrage eine Tabelle mit einer Liste der Benutzeranzeigenamen, die Uhrzeit der Ereignisse, die Anzahl von Ereignissen zur Zeit des Ereigniseintritts sowie ein visuelles Diagramm zurück. Diese kann ganz einfach bearbeitet werden, sodass Benutzer-GUIDs oder weitere Attribute angezeigt werden:

```
search *
| where ( Type == "AzureDiagnostics" )
| summarize count(TimeGenerated) by identity_s, TimeGenerated
```


Azure Data Lake Storage Gen1 stellt ein Muster bereit, nach dem die Protokolldaten verarbeitet und analysiert werden sollen. Sie finden das Beispiel unter [https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample](https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample). 

## <a name="see-also"></a>Weitere Informationen
* [Übersicht über Azure Data Lake Storage Gen1](data-lake-store-overview.md)
* [Schützen von Daten in Data Lake Storage Gen1](data-lake-store-secure-data.md)