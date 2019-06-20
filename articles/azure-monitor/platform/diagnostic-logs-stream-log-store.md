---
title: Streamen von Azure-Diagnoseprotokollen an einen Log Analytics-Arbeitsbereich in Azure Monitor
description: Hier erfahren Sie, wie Sie Azure-Diagnoseprotokolle an einen Log Analytics-Arbeitsbereich in Azure Monitor streamen.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: johnkem
ms.subservice: logs
ms.openlocfilehash: b17978da3195b364f868d33ab7ad9faa1544e9ec
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60238005"
---
# <a name="stream-azure-diagnostic-logs-to-log-analytics-workspace-in-azure-monitor"></a>Streamen von Azure-Diagnoseprotokollen an einen Log Analytics-Arbeitsbereich in Azure Monitor

**[Azure-Diagnoseprotokolle](diagnostic-logs-overview.md)** können über das Portal, über PowerShell-Cmdlets oder die Azure CLI nahezu in Echtzeit an einen Log Analytics-Arbeitsbereich in Azure Monitor gestreamt werden.

## <a name="what-you-can-do-with-diagnostics-logs-in-a-log-analytics-workspace"></a>Verwendungsmöglichkeiten für Diagnoseprotokolle in einem Log Analytics-Arbeitsbereich

Azure Monitor bietet ein flexibles Tool zur Protokollabfrage und -analyse, mit dem Sie einen Einblick in die unformatierten Protokolldaten erhalten, die aus den Azure-Ressourcen generiert werden. Zu den Funktionen gehören:

* **Protokollabfrage**: Schreiben Sie erweiterte Abfragen für Ihre Protokolldaten, stellen Sie Zusammenhänge zwischen Protokollen aus verschiedenen Quellen her, und generieren Sie Diagramme, die an Ihr Azure-Dashboard angeheftet werden können.
* **Warnungen**: Stellen Sie Übereinstimmungen von Ereignissen mit einer bestimmten Abfrage fest, und lassen Sie sich per E-Mail oder Webhookaufruf mithilfe von Azure Monitor-Warnungen benachrichtigen.
* **Erweiterte Analysen**: Wenden Sie Machine Learning- und Musterabgleichsalgorithmen an, um mögliche Probleme zu ermitteln, die durch Ihre Protokolle aufgedeckt werden.

## <a name="enable-streaming-of-diagnostic-logs-to-log-analytics-workspace"></a>Aktivieren des Streamens von Diagnoseprotokollen an einen Log Analytics-Arbeitsbereich

Das Streamen von Diagnoseprotokollen kann programmgesteuert, über das Portal oder mithilfe der [Azure Monitor-REST-APIs](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings)aktiviert werden. In jedem Fall erstellen Sie eine Diagnoseeinstellung, in der Sie einen Log Analytics-Arbeitsbereich sowie die Protokollkategorien und Metriken angeben, die an den Arbeitsbereich gesendet werden sollen. Eine **Diagnoseprotokollkategorie** ist ein Protokolltyp, der von einer Ressource bereitgestellt werden kann.

Der Log Analytics-Arbeitsbereich muss sich nicht unter demselben Abonnement befinden wie die Ressource, die Protokolle ausgibt, sofern der Benutzer, der die Einstellung konfiguriert, den entsprechenden RBAC-Zugriff auf beide Abonnements hat.

> [!NOTE]
> Das Senden mehrdimensionaler Metriken über die Diagnoseeinstellungen wird derzeit nicht unterstützt. Metriken mit Dimensionen werden als vereinfachte eindimensionale Metriken exportiert und dimensionswertübergreifend aggregiert.
>
> *Beispiel*: Die Metrik „Eingehende Nachrichten“ in einem Event Hub kann auf Warteschlangenebene untersucht und in einem Diagramm dargestellt werden. Wenn Sie die Metrik allerdings über die Diagnoseeinstellungen exportieren, umfasst die Darstellung alle eingehenden Nachrichten für alle Warteschlangen im Event Hub.
>
>

## <a name="stream-diagnostic-logs-using-the-portal"></a>Streamen von Diagnoseprotokollen mithilfe des Portals
1. Navigieren Sie im Portal zu Azure Monitor, und klicken Sie im Menü **Einstellungen** auf **Diagnoseeinstellungen**.


2. Filtern Sie optional die Liste nach Ressourcengruppe oder Ressourcentyp, und klicken Sie auf die Ressource, für die Sie eine Diagnoseeinstellung festlegen möchten.

3. Wenn keine Einstellungen für die Ressource vorhanden sind, die Sie ausgewählt haben, werden Sie aufgefordert, eine Einstellung zu erstellen. Klicken Sie auf „Diagnose aktivieren“.

   ![Diagnoseeinstellung hinzufügen – keine Einstellungen vorhanden](media/diagnostic-logs-stream-log-store/diagnostic-settings-none.png)

   Wenn Einstellungen für die Ressource vorhanden sind, sehen Sie eine Liste der Einstellungen, die bereits für diese Ressource konfiguriert sind. Klicken Sie auf „Diagnoseeinstellung hinzufügen“.

   ![Diagnoseeinstellung hinzufügen – Einstellungen vorhanden](media/diagnostic-logs-stream-log-store/diagnostic-settings-multiple.png)

3. Legen Sie einen Namen für Ihre Einstellung fest, und aktivieren Sie das Kontrollkästchen **An Log Analytics senden**. Wählen Sie dann einen Log Analytics-Arbeitsbereich aus.

   ![Diagnoseeinstellung hinzufügen – Einstellungen vorhanden](media/diagnostic-logs-stream-log-store/diagnostic-settings-configure.png)

4. Klicken Sie auf **Speichern**.

Nach einigen Augenblicken wird die neue Einstellung in der Liste der Einstellungen für diese Ressource angezeigt, und Diagnoseprotokolle werden an diesen Arbeitsbereich gestreamt, sobald neue Ereignisdaten generiert werden. Beachten Sie, dass zwischen der Ausgabe eines Ereignisses und der Anzeige in Log Analytics möglicherweise bis zu 15 Minuten vergehen.

### <a name="via-powershell-cmdlets"></a>Verwenden von PowerShell-Cmdlets

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Wenn Sie das Streaming über die [Azure PowerShell-Cmdlets](../../azure-monitor/platform/powershell-quickstart-samples.md) aktivieren möchten, können Sie das Cmdlet `Set-AzDiagnosticSetting` mit folgenden Parametern verwenden:

```powershell
Set-AzDiagnosticSetting -ResourceId [your resource ID] -WorkspaceID [resource ID of the Log Analytics workspace] -Categories [list of log categories] -Enabled $true
```

Beachten Sie, dass die workspaceID-Eigenschaft die gesamte Azure-Ressourcen-ID des Arbeitsbereichs benötigt, nicht die im Log Analytics-Portal angezeigte Kombination aus Arbeitsbereichs-ID und Schlüssel.

### <a name="via-azure-cli"></a>Verwenden der Azure-Befehlszeilenschnittstelle

Zum Ermöglichen des Streamings per [Azure CLI](../../azure-monitor/platform/cli-samples.md) können Sie den Befehl [az monitor diagnostic-settings create](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create) verwenden.

```azurecli
az monitor diagnostic-settings create --name <diagnostic name> \
    --workspace <log analytics name or object ID> \
    --resource <target resource object ID> \
    --resource-group <log analytics workspace resource group> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true
    }
    ]'
```

Sie können das Diagnoseprotokoll um weitere Kategorien ergänzen, indem Sie dem JSON-Array, das als `--logs`-Parameter übergeben wird, Wörterbücher hinzufügen.

Das Argument `--resource-group` ist nur erforderlich, wenn `--workspace` keine Objekt-ID ist.

## <a name="how-do-i-query-the-data-from-a-log-analytics-workspace"></a>Wie frage ich die Daten aus einem Log Analytics-Arbeitsbereich ab?

Auf dem Blatt „Protokolle“ im Azure Monitor-Portal können Sie Diagnoseprotokolle im Rahmen der Protokollverwaltungslösung unter der AzureDiagnostics-Tabelle abfragen. Es gibt auch [verschiedene Überwachungslösungen für Azure-Ressourcen](../../azure-monitor/insights/solutions.md), die Sie installieren können, um sofort Informationen zu den an Azure Monitor gesendeten Protokolldaten zu erhalten.

### <a name="known-limitation-column-limit-in-azurediagnostics"></a>Bekannte Einschränkung: Spaltenlimit in Azure-Diagnose
Da viele SEND-Datentypen von Ressourcen an dieselbe Tabelle (_Azure-Diagnose_) gesendet werden, stellt das Schema dieser Tabelle die Obermenge der Schemata aller erfassten einzelnen Datentypen dar. Wenn Sie beispielsweise Diagnoseeinstellungen für die Sammlung der folgenden Datentypen erstellt haben, werden sie alle an denselben Arbeitsbereich gesendet:
- Überwachungsprotokolle der Ressource 1 (mit einem Schema, das aus den Spalten A, B und C besteht)  
- Fehlerprotokolle der Ressource 2 (mit einem Schema, das aus den Spalten D, E und F besteht)  
- Datenflussprotokolle der Ressourcen 3 (mit einem Schema, das aus den Spalten G, H und I besteht)  
 
Die Azure-Diagnosetabelle sieht mit einigen Beispieldaten wie folgt aus:  
 
| ResourceProvider | Category (Kategorie) | Eine Datei | b | C | D | E | F | G | H | I |
| -- | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- |
| Microsoft.Resource1 | AuditLogs | x1 | y1 | z1 |
| Microsoft.Resource2 | ErrorLogs | | | | q1 | w1 | e1 |
| Microsoft.Resource3 | DataFlowLogs | | | | | | | j1 | k1 | l1|
| Microsoft.Resource2 | ErrorLogs | | | | q2 | w2 | e2 |
| Microsoft.Resource3 | DataFlowLogs | | | | | | | j3 | k3 | l3|
| Microsoft.Resource1 | AuditLogs | x5 | y5 | z5 |
| ... |
 
Eine einzelne Azure-Protokolltabelle ist explizit auf maximal 500 Spalten begrenzt. Sobald dieser Wert erreicht ist, werden alle Zeilen, die Daten in einer Spalte außerhalb der ersten 500 Spalten enthalten, zum Zeitpunkt der Erfassung gelöscht. Die Azure-Diagnosetabelle ist besonders anfällig für diese Begrenzung. Üblicherweise wird sie relevant, wenn eine Vielzahl von Datenquellen oder mehrere sehr ausführliche Datenquellen an denselben Arbeitsbereich gesendet werden. 
 
#### <a name="azure-data-factory"></a>Azure Data Factory  
Azure Data Factory ist aufgrund eines sehr detaillierten Protokollsatzes eine Ressource, von der bekannt ist, dass sie besonders von dieser Begrenzung betroffen ist. Dies gilt insbesondere für Folgendes:  
- *Für eine Aktivität in Ihrer Pipeline definierte Benutzerparameter*: Für jeden eindeutig benannten Benutzerparameter wird für jede Aktivität eine neue Spalte erstellt. 
- *Aktivitätseingaben und -ausgaben*: Diese variieren von Aktivität zu Aktivität und generieren aufgrund ihrer ausführlichen Natur eine große Anzahl von Spalten. 
 
Wie bei den weiter unten aufgeführten allgemeineren Vorschlägen zur Problemumgehung wird empfohlen, ADF-Protokolle in einem eigenen Arbeitsbereich zu isolieren, um die Wahrscheinlichkeit zu minimieren, dass diese Protokolle Auswirkungen auf andere Protokolltypen haben, die in Ihren Arbeitsbereichen erfasst werden. Kuratierte Protokolle für Azure Data Factory sind bald verfügbar.
 
#### <a name="workarounds"></a>Problemumgehungen
Kurzfristig, d. h. bis zur Neudefinition der Obergrenze von 500 Spalten, wird empfohlen, ausführliche Datentypen an separate Arbeitsbereiche zu senden, um die Wahrscheinlichkeit zu verringern, dass die Obergrenze erreicht wird.
 
Längerfristig wird Azure-Diagnose von einem einheitlichen Schema mit geringer Dichte zu individuellen Tabellen pro Datentyp wechseln. In Verbindung mit der Unterstützung dynamischer Typen wird dies die Verwendbarkeit der in Azure-Protokollen mithilfe des Azure-Diagnosemechanismus erfassten Daten erheblich verbessern. Sie können dies bereits für ausgewählte Azure-Ressourcentypen wie [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-analyze-activity-logs-log-analytics)- oder [Intune](https://docs.microsoft.com/intune/review-logs-using-azure-monitor)-Protokolle sehen. Suchen Sie im Blog zu [Azure-Updates](https://azure.microsoft.com/updates/) nach Nachrichten über neue Ressourcentypen in Azure, die diese kuratierten Protokolle unterstützen.


## <a name="next-steps"></a>Nächste Schritte

* [Informieren Sie sich ausführlicher über Azure-Diagnoseprotokolle.](diagnostic-logs-overview.md)

