---
title: 'Windows Virtual Desktop-Diagnose: Protokollanalyse – Azure'
description: Hier erfahren Sie, wie Sie die Protokollanalyse mit der Windows Virtual Desktop-Diagnosefunktion verwenden.
author: Heidilohr
ms.topic: how-to
ms.date: 05/27/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 5d8b696b175c4ef841eef1a51f1d357d1781cba7
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95018289"
---
# <a name="use-log-analytics-for-the-diagnostics-feature"></a>Verwenden von Log Analytics für die Diagnosefunktion

>[!IMPORTANT]
>Dieser Inhalt gilt für Windows Virtual Desktop mit Windows Virtual Desktop-Objekten für Azure Resource Manager. Wenn Sie Windows Virtual Desktop (klassisch) ohne Azure Resource Manager-Objekte verwenden, finden Sie weitere Informationen in [diesem Artikel](./virtual-desktop-fall-2019/diagnostics-log-analytics-2019.md).

Windows Virtual Desktop verwendet [Azure Monitor](../azure-monitor/overview.md) für die Überwachung und Warnungen wie viele andere Azure-Dienste. Dadurch können Administratoren Probleme über eine zentrale Oberfläche identifizieren. Durch den Dienst werden Aktivitätsprotokolle sowohl für Benutzer- als auch für Administratoraktionen generiert. Jedes Aktivitätsprotokoll gehört einer der folgenden Kategorien an:

- Verwaltungsaktivitäten:
    - Verfolgen, ob Windows Virtual Desktop-Objekte erfolgreich mithilfe von APIs oder PowerShell geändert werden können. Ist es beispielsweise möglich, erfolgreich einen Hostpool mithilfe von PowerShell zu erstellen?
- Feed:
    - Können Benutzer erfolgreich Arbeitsbereiche abonnieren?
    - Sehen Benutzer alle Ressourcen, die im Remotedesktopclient veröffentlicht wurden?
- Verbindungen:
    - Gibt an, wann Benutzer Verbindungen mit dem Dienst herstellen und beenden.
- Hostregistrierung:
    - Wurde der Sitzungshost beim Herstellen der Verbindung erfolgreich beim Dienst registriert?
- Fehler:
    - Bemerken Benutzer Probleme bei bestimmten Aktivitäten? Durch diese Funktion kann eine Tabelle generiert werden, in der Aktivitätsdaten nachverfolgt werden, solange die Informationen mit den Aktivitäten verknüpft sind.
- Prüfpunkte:
    - Bestimmte Schritte in der Lebensdauer einer Aktivität, die erreicht wurden. Beispielsweise wurde für einen Benutzer während einer Sitzung ein Lastausgleich auf einem bestimmten Host vorgenommen, anschließend wurde der Benutzer während einer Verbindung angemeldet usw.

Verbindungen, die Windows Virtual Desktop nicht erreichen, werden nicht in den Diagnoseergebnissen angezeigt, da der Diagnoserollendienst selbst Teil von Windows Virtual Desktop ist. Windows Virtual Desktop-Verbindungsprobleme können auftreten, wenn beim Benutzer Probleme mit der Netzwerkkonnektivität auftreten.

Mit Azure Monitor können Sie Windows Virtual Desktop-Daten analysieren und Leistungsindikatoren für virtuelle Computer (VM) überprüfen – und zwar im selben Tool. In diesem Artikel erfahren Sie mehr darüber, wie Sie die Diagnose für Ihre Windows Virtual Desktop-Umgebung aktivieren.

>[!NOTE]
>Informationen zum Überwachen Ihrer VMs in Azure finden Sie unter [Überwachen von virtuellen Azure-Computern mit Azure Monitor](../azure-monitor/insights/monitor-vm-azure.md). Achten Sie außerdem darauf, die [Schwellenwerte für Leistungsindikatoren zu überprüfen](../virtual-desktop/virtual-desktop-fall-2019/deploy-diagnostics.md#windows-performance-counter-thresholds), um bessere Einblicke in die Benutzererfahrung und den Sitzungshost zu erhalten.

## <a name="before-you-get-started"></a>Bevor Sie beginnen

Bevor Sie Log Analytics verwenden können, müssen Sie einen Arbeitsbereich erstellen. Befolgen Sie hierzu die Anweisungen in einem der beiden folgenden Artikel:

- Wenn Sie das Azure-Portal bevorzugen, informieren Sie sich unter [Erstellen eines Log Analytics-Arbeitsbereichs im Azure-Portal](../azure-monitor/learn/quick-create-workspace.md).
- Wenn Sie PowerShell bevorzugen, informieren Sie sich unter [Erstellen eines Log Analytics-Arbeitsbereichs mit PowerShell](../azure-monitor/platform/powershell-workspace-configuration.md).

Gehen Sie anschließend wie unter [Verbinden von Windows-Computern mit Azure Monitor](../azure-monitor/platform/log-analytics-agent.md#workspace-id-and-key) beschrieben vor, um die folgenden Informationen zu erhalten:

- Arbeitsbereich-ID
- Primärschlüssel Ihres Arbeitsbereichs

Diese Informationen werden später bei der Einrichtung benötigt.

Überprüfen Sie die Berechtigungsverwaltung für Azure Monitor, um Benutzern, die Ihre Windows Virtual Desktop-Umgebung überwachen und verwalten, den Datenzugriff zu ermöglichen. Weitere Informationen finden Sie unter [Erste Schritte mit Rollen, Berechtigungen und Sicherheit in Azure Monitor](../azure-monitor/platform/roles-permissions-security.md).

## <a name="push-diagnostics-data-to-your-workspace"></a>Pushen von Diagnosedaten an Ihren Arbeitsbereich

Diagnosedaten können von Ihren Windows Virtual Desktop-Objekten an die Log Analytics-Instanz für Ihren Arbeitsbereich gepusht werden. Sie können diese Funktion direkt einrichten, wenn Sie Ihre Objekte erstmalig erstellen.

So richten Sie Log Analytics für ein neues Objekt ein:

1. Melden Sie sich beim Azure-Portal an, und wechseln Sie zu **Windows Virtual Desktop**.

2. Navigieren Sie zum Objekt (z. B. ein Hostpool, eine App-Gruppe oder ein Arbeitsbereich), für das Sie Protokolle und Ereignisse erfassen möchten.

3. Wählen Sie im Menü auf der linken Bildschirmseite **Diagnoseeinstellungen** aus.

4. Wählen Sie in dem auf der rechten Bildschirmseite eingeblendeten Menü **Diagnoseeinstellung hinzufügen** aus.

    Welche Optionen auf der Seite „Diagnoseeinstellungen“ angezeigt werden, hängt von der Art des Objekts ab, das Sie gerade bearbeiten.

    Wenn Sie z. B. die Diagnose für eine App-Gruppe aktivieren, werden Optionen zum Konfigurieren von Prüfpunkten, Fehlern und Verwaltungseinstellungen angezeigt. Bei Arbeitsbereichen wird mithilfe dieser Kategorien ein Feed konfiguriert, mit dem Benutzer nachverfolgt werden sollen, die die App-Liste abonnieren. Weitere Informationen zu Diagnoseeinstellungen finden Sie unter [Erstellen einer Diagnoseeinstellung zum Sammeln von Ressourcenprotokollen und -metriken in Azure](../azure-monitor/platform/diagnostic-settings.md).

     >[!IMPORTANT]
     >Denken Sie daran, die Diagnose für jedes Azure Resource Manager-Objekt zu aktivieren, das Sie überwachen möchten. Nachdem die Diagnose aktiviert wurde, sind die Daten für Aktivitäten verfügbar. Nach der ersten Einrichtung kann dies einige Stunden dauern.

5. Geben Sie einen Namen für die Einstellungskonfiguration ein, und wählen Sie dann **An Log Analytics senden** aus. Der verwendete Name darf keine Leerzeichen enthalten und muss den [Namenskonventionen von Azure](../azure-resource-manager/management/resource-name-rules.md) entsprechen. Für die Protokolle können Sie alle Optionen auswählen, die Sie Log Analytics hinzufügen möchten, beispielsweise „Prüfpunkt“, „Fehler“, „Verwaltung“ usw.

6. Wählen Sie **Speichern** aus.

>[!NOTE]
>Log Analytics bietet Ihnen die Möglichkeit, Daten an [Event Hubs](../event-hubs/event-hubs-about.md) zu streamen oder in einem Speicherkonto zu archivieren. Weitere Informationen zu dieser Funktion finden Sie unter [Streamen von Azure-Überwachungsdaten an einen Event Hub](../azure-monitor/platform/stream-monitoring-data-event-hubs.md) und [Archivieren von Azure-Ressourcenprotokollen in einem Speicherkonto](../azure-monitor/platform/resource-logs.md#send-to-azure-storage).

## <a name="how-to-access-log-analytics"></a>So greifen Sie auf Log Analytics zu

Sie können auf Log Analytics-Arbeitsbereiche über das Azure-Portal oder über Azure Monitor zugreifen.

### <a name="access-log-analytics-on-a-log-analytics-workspace"></a>Zugreifen auf Log Analytics über einen Log Analytics-Arbeitsbereich

1. Melden Sie sich beim Azure-Portal an.

2. Suchen Sie nach **Log Analytics-Arbeitsbereich**.

3. Wählen Sie unter „Dienste“ die Option **Log Analytics-Arbeitsbereiche** aus.

4. Wählen Sie in der Liste den Arbeitsbereich aus, den Sie für Ihr Windows Virtual Desktop-Objekt konfiguriert haben.

5. Sobald Sie sich in Ihrem Arbeitsbereich befinden, wählen Sie **Protokolle** aus. Sie können die Menüliste mit der **Suchfunktion** filtern.

### <a name="access-log-analytics-on-azure-monitor"></a>Zugreifen auf Log Analytics über Azure Monitor

1. Anmelden beim Azure-Portal

2. Suchen Sie nach **Überwachen**, und wählen Sie diese Option aus.

3. Wählen Sie **Protokolle** aus.

4. Befolgen Sie die Anweisungen auf der Protokollierungsseite, um den Umfang der Abfrage festzulegen.

5. Sie sind für die Abfrage von Diagnosedaten bereit. Alle Diagnosetabellen verfügen über das Präfix „WVD“.

>[!NOTE]
>Detailliertere Informationen zu in Azure Monitor-Protokollen gespeicherten Tabellen finden Sie im [Azure Monitor-Datenverweis](/azure/azure-monitor/reference/). Alle Tabellen im Zusammenhang mit dem Windows Virtual Desktop werden mit „WVD“ bezeichnet.

## <a name="cadence-for-sending-diagnostic-events"></a>Intervall für das Senden von Diagnoseereignissen

Diagnoseereignisse werden nach Abschluss an Log Analytics gesendet.

Bei Verbindungsaktivitäten werden Log Analytics-Berichte nur in folgenden Zwischenstadien generiert:

- Gestartet: Wenn ein Benutzer eine App oder einen Desktop im Remotedesktopclient auswählt und eine Verbindung zu dieser bzw. diesem herstellt.
- Verbunden: Wenn der Benutzer erfolgreich eine Verbindung zu der VM herstellt, in der die App oder der Desktop gehostet wird.
- Abgeschlossen: Wenn der Benutzer oder Server die Verbindung der Sitzung, in der die Aktivität stattgefunden hat, trennt.

## <a name="example-queries"></a>Beispielabfragen

Greifen Sie über die Azure Monitor Log Analytics-Benutzeroberfläche auf Beispielabfragen zu:
1. Wechseln Sie zu Ihrem Log Analytics-Arbeitsbereich, und wählen Sie dann **Fertig** aus. Die Benutzeroberfläche der Beispielabfrage wird automatisch angezeigt.
1. Ändern Sie den Filter in **Kategorie**.
1. Wählen Sie **Windows Virtual Desktop** aus, um verfügbare Abfragen zu überprüfen.
1. Wählen Sie **Ausführen** aus, um die ausgewählte Abfrage auszuführen.

Erfahren Sie mehr über die Benutzeroberfläche der Beispielabfrage in [Gespeicherte Abfragen in Azure Monitor Log Analytics](../azure-monitor/log-query/example-queries.md).

Mit der folgenden Abfrageliste können Sie Verbindungsinformationen oder Probleme für einen einzelnen Benutzer überprüfen. Sie können diese Abfragen im [Log Analytics-Abfrage-Editor](../azure-monitor/log-query/log-analytics-tutorial.md#write-a-query) ausführen. Ersetzen Sie für jede Abfrage `userupn` durch den UPN des Benutzers, den Sie suchen möchten.


So suchen Sie alle Verbindungen für einen einzelnen Benutzer:

```kusto
WVDConnections
|where UserName == "userupn"
|take 100
|sort by TimeGenerated asc, CorrelationId
```


So ermitteln Sie die Anzahl der Verbindung pro Benutzer und Tag:

```kusto
WVDConnections
|where UserName == "userupn"
|take 100
|sort by TimeGenerated asc, CorrelationId
|summarize dcount(CorrelationId) by bin(TimeGenerated, 1d)
```

So ermitteln Sie die Sitzungsdauer nach Benutzer:

```kusto
let Events = WVDConnections | where UserName == "userupn" ;
Events
| where State == "Connected"
| project CorrelationId , UserName, ResourceAlias , StartTime=TimeGenerated
| join (Events
| where State == "Completed"
| project EndTime=TimeGenerated, CorrelationId)
on CorrelationId
| project Duration = EndTime - StartTime, ResourceAlias
| sort by Duration asc
```

So suchen Sie Fehler für einen bestimmten Benutzer:

```kusto
WVDErrors
| where UserName == "userupn"
|take 100
```

So ermitteln Sie, ob ein bestimmter Fehler für andere Benutzer aufgetreten ist:

```kusto
WVDErrors
| where CodeSymbolic =="ErrorSymbolicCode"
| summarize count(UserName) by CodeSymbolic
```


>[!NOTE]
>- Wenn ein Benutzer den vollständigen Desktop öffnet, wird die Nutzung der App in der Sitzung nicht als Prüfpunkte in der WVDCheckpoints-Tabelle verfolgt.
>- Die ResourcesAlias-Spalte in der WVDConnections-Tabelle zeigt, ob ein Benutzer eine Verbindung mit einem vollständigen Desktop oder einer veröffentlichten App hergestellt hat. In der Spalte wird nur die erste App angezeigt, die während der Verbindung geöffnet wurde. Alle veröffentlichten Apps, die der Benutzer öffnet, werden in WVDCheckpoints nachverfolgt.
>- Die WVDErrors-Tabelle enthält Verwaltungsfehler, Probleme bei der Hostregistrierung und andere Probleme, die aufgetreten sind, während der Benutzer eine Liste von Apps oder Desktops abonniert.
>- Mit WVDErrors können Sie Probleme identifizieren, die von Administratoraufgaben gelöst werden können. Der Wert für ServiceError lautet für diese Art von Problemen immer „false“. Bei „ServiceError = ‚true‘“ muss das Problem eskaliert und Microsoft gemeldet werden. Stellen Sie sicher, dass Sie die CorrelationID für die auftretenden Fehler angeben.

## <a name="next-steps"></a>Nächste Schritte

Informationen zu allgemeinen Fehlerszenarien, die von der Diagnosefunktion identifiziert werden können, finden Sie unter [Identifizieren und Diagnostizieren von Problemen](diagnostics-role-service.md#common-error-scenarios).