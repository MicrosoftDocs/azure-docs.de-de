---
title: 'Tutorial: Verwalten der Konfiguration eines virtuellen Windows-Computers in Azure'
description: In diesem Tutorial erfahren Sie, wie Sie auf einem virtuellen Windows-Computer Änderungen ermitteln und Paketupdates verwalten.
author: cynthn
ms.service: virtual-machines-windows
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 12/05/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: a478d9c620219a768983570897715f924565a80f
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100594303"
---
# <a name="tutorial-monitor-changes-and-update-a-windows-virtual-machine-in-azure"></a>Tutorial: Überwachen von Änderungen und Aktualisieren eines virtuellen Windows-Computers in Azure

Per [Änderungsnachverfolgung](../../automation/change-tracking/overview.md) und [Updateverwaltung](../../automation/update-management/overview.md) von Azure können Sie auf einfache Weise Änderungen auf Ihren virtuellen Windows-Computern in Azure ermitteln und Betriebssystemupdates für diese VMs verwalten.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Verwalten von Windows-Updates
> * Überwachen von Änderungen und des Bestands

## <a name="open-azure-cloud-shell"></a>Öffnen von Azure Cloud Shell

Azure Cloud Shell ist eine kostenlose interaktive Shell, mit der Sie die Schritte in diesem Artikel ausführen können. Sie verfügt über allgemeine vorinstallierte Tools und ist für die Verwendung mit Ihrem Azure-Konto konfiguriert.

Wählen Sie zum Öffnen eines beliebigen Codeblocks in Cloud Shell einfach oben rechts im Codeblock die Option **Ausprobieren** aus.

Sie können Cloud Shell auch auf einer separaten Browserregisterkarte öffnen, indem Sie zu [https://shell.azure.com/powershell](https://shell.azure.com/powershell) navigieren. Wählen Sie **Kopieren** aus, um einen Codeblock zu kopieren, und fügen Sie ihn dann auf der Cloud Shell-Registerkarte ein. Drücken Sie die EINGABETASTE, um den Code auszuführen.

## <a name="create-a-virtual-machine"></a>Erstellen eines virtuellen Computers

Zum Konfigurieren von Azure-Überwachung und Updateverwaltung in diesem Tutorial benötigen Sie einen virtuellen Windows-Computer in Azure.

Legen Sie mit [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential) zuerst den Benutzernamen und das Kennwort des VM-Administrators fest:

```azurepowershell-interactive
$cred = Get-Credential
```

Erstellen Sie als Nächstes mit [New-AzVM](/powershell/module/az.compute/new-azvm) den virtuellen Computer. Im folgenden Beispiel wird ein virtueller Computer mit dem Namen `myVM` in der Region `East US` erstellt. Falls diese Elemente nicht bereits vorhanden sind, werden die Ressourcengruppe `myResourceGroupMonitor` und die zugehörigen Netzwerkressourcen erstellt:

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroupMonitor" `
    -Name "myVM" `
    -Location "East US" `
    -Credential $cred
```

Die Erstellung der Ressourcen und VM dauert einige Minuten.

## <a name="manage-windows-updates"></a>Verwalten von Windows-Updates

Mit der Updateverwaltung können Sie Updates und Patches für Ihre virtuellen Windows-Computer in Azure verwalten. Sie können direkt auf Ihrem virtuellen Computer schnell Folgendes durchführen:

- Den Status verfügbarer Updates bewerten
- Die Installation erforderlicher Updates planen
- Bereitstellungsergebnisse überprüfen, um die erfolgreiche Anwendung von Updates auf die VM sicherzustellen

Informationen zu den Preisen finden Sie unter [Automation – Preise](https://azure.microsoft.com/pricing/details/automation/) unter „Updateverwaltung“.

### <a name="enable-update-management"></a>Aktivieren der Updateverwaltung

Aktivieren Sie die Updateverwaltung für Ihre VM wie folgt:

1. Wählen Sie ganz links im Fenster die Option **Virtuelle Computer** aus.
1. Wählen Sie in der Liste eine VM aus.
1. Wählen Sie im VM-Fenster im Bereich **Vorgänge** die Option **Updateverwaltung** aus.
1. Das Fenster **Updateverwaltung aktivieren** wird geöffnet.

Eine Überprüfung wird durchgeführt, um festzustellen, ob die Updateverwaltung für diesen virtuellen Computer aktiviert ist. Bei der Überprüfung wird sichergestellt, dass ein Log Analytics-Arbeitsbereich und ein verknüpftes Automation-Konto vorhanden sind und sich die Lösung im Arbeitsbereich befindet.

Sie nutzen einen [Log Analytics](../../azure-monitor/logs/log-query-overview.md)-Arbeitsbereich, um Daten zu sammeln, die von Features und Diensten wie der Updateverwaltung generiert werden. Der Arbeitsbereich ist ein zentraler Ort zum Überprüfen und Analysieren von Daten aus mehreren Quellen.

Zum Durchführen von zusätzlichen Aktionen für VMs, für die Updates erforderlich sind, können Sie Azure Automation verwenden, um Runbooks für VMs auszuführen. Zu diesen Aktionen gehört auch das Herunterladen oder Anwenden von Updates.

Beim Überprüfungsprozess wird auch geprüft, ob der virtuelle Computer mit dem Microsoft Monitoring Agent (MMA) und Automation Hybrid Runbook Worker bereitgestellt wird. Sie verwenden den Agent, um mit dem virtuellen Computer zu kommunizieren und Informationen zum Updatestatus abzurufen.

Wählen Sie im Fenster **Updateverwaltung aktivieren** den Log Analytics-Arbeitsbereich und das Automation-Konto und anschließend die Option **Aktivieren** aus. Es dauert ungefähr 15 Minuten, bis die Lösung aktiviert ist.

Falls beim Onboarding erkannt wird, dass die folgenden erforderlichen Komponenten fehlen, werden sie automatisch hinzugefügt:

* [Log Analytics](../../azure-monitor/logs/log-query-overview.md)-Arbeitsbereich
* [Automation](../../automation/index.yml)
* Ein [Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md), der auf dem virtuellen Computer aktiviert ist

Nachdem die Lösung aktiviert wurde, wird das Fenster **Updateverwaltung** geöffnet. Konfigurieren Sie den Standort, den Log Analytics-Arbeitsbereich und das Automation-Konto, und wählen Sie anschließend die Option **Aktivieren** aus. Wenn diese Optionen abgeblendet angezeigt werden, ist eine andere Automatisierungslösung für die VM aktiviert, sodass deren Arbeitsbereich und das zugehörige Automation-Konto verwendet werden müssen.

![Aktivieren der Lösung „Updateverwaltung“](./media/tutorial-monitoring/manageupdates-update-enable.png)

Es kann bis zu 15 Minuten dauern, bis die Lösung „Updateverwaltung“ aktiviert wird. Schließen Sie das Browserfenster während dieses Zeitraums nicht. Nachdem die Lösung aktiviert wurde, werden Informationen zu fehlenden Updates auf dem virtuellen Computer an Azure Monitor-Protokolle übermittelt. Es kann zwischen 30 Minuten und sechs Stunden dauern, bis die Daten für die Analyse verfügbar sind.

### <a name="view-an-update-assessment"></a>Anzeigen einer Updatebewertung

Nachdem die Updateverwaltung aktiviert wurde, wird das Fenster **Updateverwaltung** angezeigt. Nach Abschluss der Bewertung von Updates wird auf der Registerkarte **Fehlende Updates** eine Liste mit den entsprechenden Updates angezeigt.

 ![Anzeigen des Updatestatus](./media/tutorial-monitoring/manageupdates-view-status-win.png)

### <a name="schedule-an-update-deployment"></a>Planen einer Updatebereitstellung

Planen Sie zum Installieren von Updates eine Bereitstellung, die Ihrem Releasezeitplan und Wartungsfenster entspricht. Sie wählen aus, welche Updatetypen in die Bereitstellung eingeschlossen werden sollen. Beispielsweise können Sie kritische oder Sicherheitsupdates einschließen und Updaterollups ausschließen.

Um eine neue Updatebereitstellung für den virtuellen Computer zu planen, klicken Sie am oberen Rand des Fensters **Updateverwaltung** auf **Updatebereitstellung planen**. Geben Sie im Fenster **Neue Updatebereitstellung** die folgenden Informationen an:

| Option | Beschreibung |
| --- | --- |
| **Name** |Geben Sie einen eindeutigen Namen zur Identifizierung der Updatebereitstellung ein. |
|**Betriebssystem**| Wählen Sie **Linux** oder **Windows** aus.|
| **Zu aktualisierende Gruppen** |Definieren Sie für virtuelle Computer, die in Azure gehostet werden, eine Abfrage, die auf einer Kombination aus Abonnement, Ressourcengruppen, Standorten und Tags basiert. Mit dieser Abfrage wird eine dynamische Gruppe mit in Azure gehosteten virtuellen Computern erstellt, die Teil der Bereitstellung sein sollen. </br></br>Wählen Sie für virtuelle Computer, die nicht in Azure gehostet werden, eine vorhandene gespeicherte Suche aus. Mit dieser Suche können Sie eine Gruppe dieser virtuellen Computer für die Bereitstellung auswählen. </br></br> Weitere Informationen finden Sie unter [Dynamische Gruppen](../../automation/update-management/configure-groups.md).|
| **Zu aktualisierende Computer** |Wählen Sie **Gespeicherte Suche**, **Importierte Gruppe** oder **Computer** aus.<br/><br/>Bei der Option **Computer** können Sie in der Dropdownliste einzelne Computer auswählen. Die Bereitschaft der einzelnen Computer wird in der Spalte **BEREITSCHAFT DES UPDATE-AGENTS** der Tabelle angezeigt.</br></br> Weitere Informationen zu den verschiedenen Methoden zum Erstellen von Computergruppen in Azure Monitor-Protokollen finden Sie unter [Computergruppen in Azure Monitor-Protokollen](../../azure-monitor/logs/computer-groups.md). |
|**Updateklassifizierungen**|Wählen Sie alle erforderlichen Updateklassifizierungen aus.|
|**Updates einschließen/ausschließen**|Wählen Sie diese Option aus, um den Bereich **Einschließen/ausschließen** zu öffnen. Die ein- und auszuschließenden Updates befinden sich auf separaten Registerkarten. Weitere Informationen zur Vorgehensweise beim Einschließen finden Sie unter [Planen einer Updatebereitstellung](../../automation/update-management/deploy-updates.md#schedule-an-update-deployment). |
|**Zeitplaneinstellungen**|Wählen Sie eine Startzeit und dann entweder **Einmalig** oder **Wiederholt** aus.|
| **Vor und nach dem Vorgang auszuführende Skripts**|Wählen Sie die Skripts aus, die vor und nach Ihrer Bereitstellung ausgeführt werden sollen.|
| **Wartungsfenster** | Geben Sie die Anzahl von Minuten ein, die für Updates festgelegt werden soll. Der gültige Bereich reicht von 30 bis 360 Minuten. |
| **Neustartsteuerung**| Wählen Sie aus, wie bei Neustarts vorgegangen werden soll. Verfügbare Optionen:<ul><li>**Neustart bei Bedarf**</li><li>**Immer neu starten**</li><li>**Nie neu starten**</li><li>**Nur neu starten**</li></ul>**Neustart bei Bedarf** ist die Standardeinstellung. Wenn Sie **Nur neu starten** auswählen, werden keine Updates installiert.|

Klicken Sie nach Abschluss der Konfiguration des Zeitplans auf die Schaltfläche **Erstellen**, um zum Statusdashboard zurückzukehren. Die Tabelle **Geplant** zeigt den von Ihnen erstellten Bereitstellungszeitplan.

Sie können Updatebereitstellungen auch programmgesteuert erstellen. Weitere Informationen zum Erstellen einer Updatebereitstellung mit der REST-API finden Sie unter [Softwareupdatekonfigurationen – Erstellen](/rest/api/automation/softwareupdateconfigurations/create). Es ist auch ein Beispielrunbook vorhanden, das Sie zum Erstellen einer wöchentlichen Updatebereitstellung verwenden können. Weitere Informationen zu diesem Runbook finden Sie unter [Erstellen einer wöchentlichen Updatebereitstellung für einen oder mehrere virtuelle Computer in einer Ressourcengruppe](https://gallery.technet.microsoft.com/scriptcenter/Create-a-weekly-update-2ad359a1).

### <a name="view-results-of-an-update-deployment"></a>Anzeigen der Ergebnisse einer Updatebereitstellung

Nach dem Start der geplanten Bereitstellung wird der Bereitstellungsstatus im Fenster **Updateverwaltung** auf der Registerkarte **Updatebereitstellungen** angezeigt.

Wenn die Bereitstellung derzeit ausgeführt wird, lautet der Status „In Bearbeitung“. Nach dem erfolgreichen Abschluss des Vorgangs ändert sich der Status in „Erfolgreich“. Falls es für Updates der Bereitstellung zu Fehlern kommt, lautet der Status „Vorgang teilweise fehlgeschlagen“.

Klicken Sie auf die abgeschlossene Updatebereitstellung, um das Dashboard für diese Bereitstellung anzuzeigen.

![Updatebereitstellungs-Statusdashboard für bestimmte Bereitstellung](./media/tutorial-monitoring/manageupdates-view-results.png)

Auf der Kachel **Updateergebnisse** wird eine Zusammenfassung der Gesamtzahl der Updates und der Bereitstellungsergebnisse auf dem virtuellen Computer angezeigt. Die Tabelle rechts enthält eine detaillierte Aufschlüsselung der einzelnen Updates und der Installationsergebnisse. Jedes Ergebnis verfügt über einen der folgenden Werte:

* **Kein Versuch erfolgt**: Das Update wird nicht installiert. Aufgrund der festgelegten Dauer des Wartungsfensters war nicht genügend Zeit verfügbar.
* **Erfolg:** Das Update war erfolgreich.
* **Fehler:** Beim Update ist ein Fehler aufgetreten.

Klicken Sie auf **Alle Protokolle**, um alle von der Bereitstellung erstellten Protokolleinträge anzuzeigen.

Wählen Sie die Kachel **Ausgabe** aus, um den Auftragsdatenstrom des Runbooks anzuzeigen, das für die Verwaltung der Updatebereitstellung auf dem virtuellen Zielcomputer verantwortlich ist.

Wählen Sie **Fehler** aus, um ausführliche Informationen zu Bereitstellungsfehlern anzuzeigen.

## <a name="monitor-changes-and-inventory"></a>Überwachen von Änderungen und Bestand

Sie können Bestandsinformationen für Software, Dateien, Linux-Daemons, Windows-Dienste und Windows-Registrierungsschlüssel auf Ihren Computern erfassen und anzeigen. Durch das Nachverfolgen der Konfigurationen Ihrer Computer können Sie betriebsbezogene Probleme in Ihrer Umgebung erkennen und ein besseres Verständnis des Zustands Ihrer Computer entwickeln.

### <a name="enable-change-and-inventory-management"></a>Aktivieren der Änderungs- und Bestandsverwaltung

Aktivieren Sie die Änderungs- und Bestandsverwaltung für Ihren virtuellen Computer wie folgt:

1. Wählen Sie ganz links im Fenster die Option **Virtuelle Computer** aus.
1. Wählen Sie in der Liste eine VM aus.
1. Wählen Sie im VM-Fenster unter **Vorgänge** entweder **Bestand** oder **Änderungsnachverfolgung** aus.
1. Der Bereich **Änderungsnachverfolgung und Bestand aktivieren** wird geöffnet.

Konfigurieren Sie den Standort, den Log Analytics-Arbeitsbereich und das Automation-Konto, und wählen Sie anschließend die Option **Aktivieren** aus. Wenn die Optionen abgeblendet angezeigt werden, ist für den virtuellen Computer bereits eine Automatisierungslösung aktiviert. In diesem Fall müssen der bereits aktivierte Arbeitsbereich und das entsprechende Automation-Konto verwendet werden.

Die Lösungen sind im Menü zwar getrennt aufgeführt, aber es handelt sich um die gleiche Lösung. Wenn Sie eine der Lösungen aktivieren, wird automatisch auch die andere Lösung für Ihren virtuellen Computer aktiviert.

![Aktivieren der Änderungs- und Bestandsnachverfolgung](./media/tutorial-monitoring/manage-inventory-enable.png)

Nach der Aktivierung der Lösung kann es einige Zeit dauern, bis der Bestand auf dem virtuellen Computer erfasst wurde und Daten angezeigt werden.

### <a name="track-changes"></a>Nachverfolgen von Änderungen

Wählen Sie auf Ihrem virtuellen Computer unter **VORGÄNGE** die Option **Änderungsnachverfolgung** und dann **Einstellungen bearbeiten** aus. Der Bereich **Änderungsnachverfolgung** wird geöffnet. Wählen Sie die Art der Einstellung aus, die Sie nachverfolgen möchten, und klicken Sie dann zum Konfigurieren der Einstellungen auf **+ Hinzufügen**.

Die verfügbaren Einstellungsoptionen für Windows sind:

* Windows-Registrierung
* Windows-Dateien

Ausführliche Informationen zur Änderungsnachverfolgung finden Sie unter [Problembehandlung für Änderungen in Ihrer Umgebung](../../automation/automation-tutorial-troubleshoot-changes.md).

### <a name="view-inventory"></a>Anzeigen des Bestands

Wählen Sie auf Ihrer VM unter **VORGÄNGE** die Option **Bestand**. Auf der Registerkarte **Software** werden in einer Tabelle die gefundenen Softwareoptionen angezeigt. Die allgemeinen Details zu den einzelnen Softwaredatensätzen werden in der Tabelle angezeigt. Zu diesen Details zählen Softwarename, Version, Herausgeber und der Zeitpunkt der letzten Aktualisierung.

![Anzeigen des Bestands](./media/tutorial-monitoring/inventory-view-results.png)

### <a name="monitor-activity-logs-and-changes"></a>Überwachen von Aktivitätsprotokollen und Änderungen

Wählen Sie auf Ihrem virtuellen Computer im Fenster **Änderungsnachverfolgung** die Option **Aktivitätsprotokollverbindung verwalten** aus, um den Bereich **Azure-Aktivitätsprotokoll** zu öffnen. Wählen Sie **Verbinden** aus, um die Änderungsnachverfolgung mit der Azure-Aktivität für Ihre VM zu verbinden.

Navigieren Sie nach dem Aktivieren der Änderungsnachverfolgung zum Bereich **Übersicht** Ihres virtuellen Computers, und wählen Sie **Beenden** aus, um Ihren virtuellen Computer zu beenden. Wählen Sie nach der entsprechenden Aufforderung **Ja**, um die VM zu beenden. Wählen Sie nach Aufhebung der Zuordnung des virtuellen Computers **Starten** aus, um ihn neu zu starten.

Beim Beenden und Neustarten einer VM wird im Aktivitätsprotokoll dazu ein Ereignis protokolliert. Wechseln Sie zurück zum Bereich **Änderungsnachverfolgung**, und wählen Sie unten im Bereich die Registerkarte **Ereignisse** aus. Nach kurzer Wartezeit werden die Ereignisse im Diagramm und in der Tabelle angezeigt. Sie können die einzelnen Ereignisse auswählen, um ausführliche Informationen dazu anzuzeigen.

![Anzeigen von Änderungen im Aktivitätsprotokoll](./media/tutorial-monitoring/manage-activitylog-view-results.png)

Im obigen Diagramm sind die Änderungen dargestellt, die im Laufe der Zeit durchgeführt wurden. Nachdem Sie eine Verbindung für Azure-Aktivitätsprotokolle hinzugefügt haben, werden oben im Liniendiagramm die Ereignisse des Azure-Aktivitätsprotokolls angezeigt.

Jede Zeile mit Balkendiagrammen steht für einen eigenen Änderungstyp, der nachverfolgt werden kann. Diese Typen sind Linux-Daemons, Dateien, Windows-Registrierungsschlüssel, Software und Windows-Dienste. Auf der Registerkarte **Ändern** werden die Details der Änderung angezeigt. Die Änderungen werden in der Reihenfolge ihres Auftretens angezeigt (letzte Änderung zuerst).

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie die Änderungsnachverfolgung und Updateverwaltung für Ihre virtuellen Computer konfiguriert und überprüft. Sie haben Folgendes gelernt:

> [!div class="checklist"]
> * Erstellen einer Ressourcengruppe und VM
> * Verwalten von Windows-Updates
> * Überwachen von Änderungen und des Bestands

Fahren Sie mit dem nächsten Tutorial fort, um sich über die Überwachung Ihres virtuellen Computers zu informieren.

> [!div class="nextstepaction"]
> [Überwachen virtueller Computer](tutorial-monitor.md)
