---
title: Tutorial – Überwachen und Aktualisieren virtueller Windows-Computer in Azure | Microsoft-Dokumentation
description: In diesem Tutorial erfahren Sie, wie Sie auf einem virtuellen Windows-Computer Startdiagnose und Leistungsmetriken überwachen und Paketupdates verwalten.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/05/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: aa423fc441c50c774a9670feec64d0f844a4f5ec
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/07/2019
ms.locfileid: "66755297"
---
# <a name="tutorial-monitor-and-update-a-windows-virtual-machine-in-azure"></a>Tutorial: Überwachen und Aktualisieren eines virtuellen Windows-Computers in Azure

Bei der Überwachung von Azure werden Agents verwendet, um Start- und Leistungsdaten von Azure-VMs zu sammeln, in Azure zu speichern und über das Portal, das Azure PowerShell-Modul und die Azure-Befehlszeilenschnittstelle verfügbar zu machen. Mithilfe der Updateverwaltung können Sie Updates und Patches für Ihre Windows-Azure-VMs verwalten.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Aktivieren der Startdiagnose auf einem virtuellen Computer
> * Anzeigen der Startdiagnose
> * Anzeigen der VM-Hostmetriken
> * Bereitstellen der Diagnoseerweiterung
> * Anzeigen von Metriken des virtuellen Computers
> * Erstellen einer Warnung
> * Verwalten von Windows-Updates
> * Überwachen von Änderungen und Bestand
> * Einrichten der erweiterten Überwachung

## <a name="launch-azure-cloud-shell"></a>Starten von Azure Cloud Shell

Azure Cloud Shell ist eine kostenlose interaktive Shell, mit der Sie die Schritte in diesem Artikel ausführen können. Sie verfügt über allgemeine vorinstallierte Tools und ist für die Verwendung mit Ihrem Konto konfiguriert. 

Wählen Sie zum Öffnen von Cloud Shell oben rechts in einem Codeblock einfach die Option **Ausprobieren**. Sie können Cloud Shell auch auf einer separaten Browserregisterkarte starten, indem Sie zu [https://shell.azure.com/powershell](https://shell.azure.com/powershell) navigieren. Wählen Sie **Kopieren**, um die Blöcke mit dem Code zu kopieren. Fügen Sie ihn anschließend in Cloud Shell ein, und drücken Sie die EINGABETASTE, um ihn auszuführen.

## <a name="create-virtual-machine"></a>Erstellen eines virtuellen Computers

Zum Konfigurieren von Azure-Überwachung und Updateverwaltung in diesem Tutorial benötigen Sie einen virtuellen Windows-Computer in Azure. Legen Sie mit [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) zuerst den Benutzernamen und das Kennwort des VM-Administrators fest:

```azurepowershell-interactive
$cred = Get-Credential
```

Erstellen Sie nun mit [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) den virtuellen Computer. Im folgenden Beispiel wird eine VM mit dem Namen *myVM* für den Standort *EastUS* erstellt. Falls sie nicht bereits vorhanden sind, werden die Ressourcengruppe *myResourceGroupMonitorMonitor* und unterstützende Netzwerkressourcen erstellt:

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroupMonitor" `
    -Name "myVM" `
    -Location "East US" `
    -Credential $cred
```

Die Erstellung der Ressourcen und VM dauert einige Minuten.

## <a name="view-boot-diagnostics"></a>Anzeigen der Startdiagnose

Beim Start von virtuellen Windows-Computern erfasst der Startdiagnose-Agent Bildschirmausgaben, die zur Fehlerbehebung verwendet werden können. Diese Funktionalität ist standardmäßig aktiviert. Die erfassten Screenshots werden in einem Azure-Speicherkonto gespeichert, das auch standardmäßig erstellt wird.

Sie erhalten die Startdiagnosedaten mit dem Befehl [Get AzureRmVMBootDiagnosticsData](https://docs.microsoft.com/powershell/module/az.compute/get-azvmbootdiagnosticsdata). Im folgenden Beispiel wird die Startdiagnose in den Stammordner des Laufwerks *c:\* heruntergeladen.

```powershell
Get-AzVMBootDiagnosticsData -ResourceGroupName "myResourceGroupMonitor" -Name "myVM" -Windows -LocalPath "c:\"
```

## <a name="view-host-metrics"></a>Anzeigen von Hostmetriken

Eine Windows-VM verfügt über eine dedizierte Host-VM in Azure, mit der sie interagiert. Es werden automatisch Metriken für den Host gesammelt, die im Azure-Portal angezeigt werden können.

1. Klicken Sie im Azure-Portal auf **Ressourcengruppen**, und wählen Sie **myResourceGroupMonitor** und dann in der Ressourcenliste **myVM** aus.
2. Klicken Sie in dem Blatt der VM auf **Metriken**, und wählen Sie dann eine der Host-Metriken aus **Verfügbare Metriken**  aus.

    ![Anzeigen von Hostmetriken](./media/tutorial-monitoring/tutorial-monitor-host-metrics.png)

## <a name="install-diagnostics-extension"></a>Installieren der Diagnoseerweiterung

Die grundlegenden Hostmetriken sind direkt verfügbar. Wenn Sie aber detailliertere und VM-spezifische Metriken anzeigen möchten, müssen Sie die Azure-Diagnose-Erweiterung auf dem virtuellen Computer installieren. Die Azure-Diagnose-Erweiterung ermöglicht das Abrufen zusätzlicher Überwachungs- und Diagnosedaten von virtuellen Computern. Sie können diese Leistungsmetriken anzeigen und basierend auf der VM-Leistung Benachrichtigungen erstellen. Die Diagnose-Erweiterung wird wie folgt über das Azure-Portal installiert:

1. Klicken Sie im Azure-Portal auf **Ressourcengruppen**, und wählen Sie **myResourceGroupMonitor** und dann in der Ressourcenliste **myVM** aus.
2. Klicken Sie auf **Diagnoseeinstellungen**. Die Liste zeigt, dass die *Startdiagnose* bereits im vorherigen Abschnitt aktiviert wurde. Klicken Sie auf das Kontrollkästchen für *Grundlegende Metriken*.
3. Klicken Sie auf die Schaltfläche **Überwachung auf Gastebene aktivieren**.

    ![Anzeigen von Diagnosemetriken](./media/tutorial-monitoring/enable-diagnostics-extension.png)

## <a name="view-vm-metrics"></a>Anzeigen von Metriken des virtuellen Computers

Sie können die VM-Metriken auf die gleiche Weise anzeigen, wie Sie die Metriken zur Host-VM angezeigt haben:

1. Klicken Sie im Azure-Portal auf **Ressourcengruppen**, und wählen Sie **myResourceGroupMonitor** und dann in der Ressourcenliste **myVM** aus.
2. Um die Leistung des virtuellen Computers anzuzeigen, klicken Sie dem Blatt der VM auf **Metriken**, und wählen Sie dann eine der Diagnosemetriken unter **Verfügbare Metriken** aus.

    ![Anzeigen von Metriken des virtuellen Computers](./media/tutorial-monitoring/monitor-vm-metrics.png)

## <a name="create-alerts"></a>Erstellen von Warnungen

Sie können Warnungen auf Grundlage von bestimmten Leistungsmetriken erstellen. Warnungen können für Benachrichtigungen verwendet werden, wenn z.B. die durchschnittliche CPU-Auslastung einen bestimmten Schwellenwert überschreitet oder wenn der verfügbare freie Speicherplatz unter einen bestimmten Wert fällt. Warnungen werden im Azure-Portal angezeigt oder können per E-Mail gesendet werden. Sie können auch als Reaktion auf die generierten Warnungen Azure Automation-Runbooks oder Azure Logic Apps auslösen.

Das folgende Beispiel erstellt eine Warnung für die durchschnittliche CPU-Auslastung.

1. Klicken Sie im Azure-Portal auf **Ressourcengruppen**, und wählen Sie **myResourceGroupMonitor** und dann in der Ressourcenliste **myVM** aus.
2. Klicken Sie auf dem Blatt des virtuellen Computers auf **Warnungsregeln** und dann am oberen Rand des Warnungsblatts auf **Metrikwarnung hinzufügen**.
3. Geben Sie einen **Namen** für die Warnung ein, z.B. *myAlertRule*.
4. Um eine Warnung auszulösen, wenn der CPU-Prozentsatz 1.0 für fünf Minuten überschreitet, belassen Sie alle anderen Standardeinstellungen ausgewählt.
5. Aktivieren Sie optional das Kontrollkästchen *E-Mail-Besitzer, Mitwirkende und Leser*, um E-Mail-Benachrichtigungen zu senden. Als Standardaktion wird im Portal eine Benachrichtigung angezeigt.
6. Klicken Sie auf die Schaltfläche **OK**.

## <a name="manage-windows-updates"></a>Verwalten von Windows-Updates

Mithilfe der Updateverwaltung können Sie Updates und Patches für Ihre Windows-Azure-VMs verwalten.
Direkt von Ihrem virtuellen Computer aus können Sie schnell den Status der verfügbaren Updates bewerten, die Installation der erforderlichen Updates planen und Bereitstellungsergebnisse überprüfen, um sicherzustellen, dass Updates erfolgreich auf den virtuellen Computer angewendet wurden.

Informationen zu den Preisen finden Sie unter [Automation – Preise](https://azure.microsoft.com/pricing/details/automation/) unter „Updateverwaltung“.

### <a name="enable-update-management"></a>Aktivieren der Updateverwaltung

So aktivieren Sie die Updateverwaltung für Ihre VM

1. Wählen Sie auf der linken Seite des Bildschirms **Virtuelle Computer** aus.
2. Wählen Sie einen virtuellen Computer in der Liste aus.
3. Klicken Sie auf dem VM-Bildschirm im Abschnitt **Vorgänge** auf **Updateverwaltung**. Der Bildschirm **Updateverwaltung aktivieren** wird geöffnet.

Eine Überprüfung wird ausgeführt, um festzustellen, ob die Updateverwaltung für diesen virtuellen Computer aktiviert ist.
Die Überprüfung umfasst Prüfungen für einen Log Analytics-Arbeitsbereich und ein verknüpftes Automation-Konto, und ob die Lösung im Arbeitsbereich vorhanden ist.

Mit einem [Log Analytics](../../log-analytics/log-analytics-overview.md)-Arbeitsbereich werden Daten gesammelt, die von Features und Diensten wie der Updateverwaltung generiert werden.
Der Arbeitsbereich ist ein zentraler Ort zum Überprüfen und Analysieren von Daten aus mehreren Quellen.
Um weitere Aktionen auf virtuellen Computern auszuführen, die Updates erfordern, können Sie mit Azure Automation Runbooks für virtuelle Computer ausführen, z.B. um Updates herunterzuladen und anzuwenden.

Der Überprüfungsprozess prüft auch, ob der virtuelle Computer mit dem Microsoft Monitoring Agent (MMA) und Automation Hybrid Runbook Worker bereitgestellt wird.
Dieser Agent wird verwendet, um mit dem virtuellen Computer zu kommunizieren und Informationen zum Updatestatus abzurufen.

Wählen Sie den Log Analytics-Arbeitsbereich und das Automation-Konto aus, und klicken Sie auf **Aktivieren**, um die Lösung zu aktivieren. Es dauert ungefähr 15 Minuten, bis die Lösung aktiviert ist.

Wenn beim Onboarding festgestellt wird, dass eine der folgenden Voraussetzungen fehlt, wird sie automatisch hinzugefügt:

* [Log Analytics](../../log-analytics/log-analytics-overview.md)-Arbeitsbereich
* [Automatisierung](../../automation/automation-offering-get-started.md)
* Ein [Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md) ist auf dem virtuellen Computer aktiviert.

Der Bildschirm **Updateverwaltung** wird geöffnet. Konfigurieren Sie den gewünschten Standort, den Log Analytics-Arbeitsbereich und das Automation-Konto, und klicken Sie auf **Aktivieren**. Wenn die Felder ausgegraut sind, bedeutet dies, dass eine andere Automatisierungslösung für die VM aktiviert ist und derselbe Arbeitsbereich und dasselbe Automation-Konto verwendet werden müssen.

![Aktivieren der Updateverwaltungslösung](./media/tutorial-monitoring/manageupdates-update-enable.png)

Das Aktivieren der Lösung kann bis zu 15 Minuten dauern. Während dieses Zeitraums sollten Sie das Browserfenster nicht schließen. Nachdem die Lösung aktiviert wurde, werden Informationen zu fehlenden Updates auf dem virtuellen Computer an Azure Monitor-Protokolle übermittelt. Es kann zwischen 30 Minuten und 6 Stunden dauern, bis die Daten für die Analyse verfügbar sind.

### <a name="view-update-assessment"></a>Anzeigen der Updatebewertung

Sobald die **Updateverwaltung** aktiviert ist, wird der Bildschirm **Updateverwaltung** angezeigt. Nach Abschluss der Bewertung von Updates wird auf der Registerkarte **Fehlende Updates** eine Liste mit den entsprechenden Updates angezeigt.

 ![Anzeigen des Updatestatus](./media/tutorial-monitoring/manageupdates-view-status-win.png)

### <a name="schedule-an-update-deployment"></a>Planen einer Updatebereitstellung

Planen Sie zum Installieren von Updates eine Bereitstellung, die Ihrem Releasezeitplan und Wartungsfenster entspricht. Sie können auswählen, welche Updatetypen in die Bereitstellung eingeschlossen werden sollen. Beispielsweise können Sie kritische oder Sicherheitsupdates einschließen und Updaterollups ausschließen.

Um eine neue Updatebereitstellung für den virtuellen Computer zu planen, klicken Sie auf **Updatebereitstellung planen** am oberen Rand des Bildschirms **Updateverwaltung**. Geben Sie auf dem Bildschirm **Neue Updatebereitstellung** die folgenden Informationen ein:

Wählen Sie zum Erstellen einer neuen Updatebereitstellung **Updatebereitstellung planen** aus. Die Seite **Neue Updatebereitstellung** wird geöffnet. Geben Sie Werte für die Eigenschaften ein, die in der folgenden Tabelle beschrieben werden, und klicken Sie auf **Erstellen**:

| Eigenschaft | BESCHREIBUNG |
| --- | --- |
| NAME |Eindeutiger Name zum Identifizieren der Updatebereitstellung |
|Betriebssystem| Linux oder Windows|
| Zu aktualisierende Gruppen |Definieren Sie für Azure-Computer eine Abfrage basierend auf einer Kombination aus Abonnement, Ressourcengruppen, Standorten und Tags, um eine dynamische Gruppe von virtuellen Azure-Computern zu erstellen, die in die Bereitstellung eingeschlossen werden sollen. </br></br>Wählen Sie für Azure-fremde Computer eine vorhandene gespeicherte Suche aus, um eine Gruppe von Azure-fremden Computern auszuwählen, die in die Bereitstellung eingeschlossen werden sollen. </br></br>Weitere Informationen finden Sie unter [Dynamische Gruppen](../../automation/automation-update-management.md#using-dynamic-groups).|
| Zu aktualisierende Computer |Wählen Sie eine gespeicherte Suche oder eine importierte Gruppe aus, oder wählen Sie im Dropdownmenü „Computer“ und dann einzelne Computer aus. Bei Auswahl von **Computer** wird die Bereitschaft des Computers in der Spalte **BEREITSCHAFT DES UPDATE-AGENTS** angezeigt.</br> Weitere Informationen zu den verschiedenen Methoden zum Erstellen von Computergruppen in Azure Monitor-Protokollen finden Sie unter [Computergruppen in Azure Monitor-Protokollen](../../azure-monitor/platform/computer-groups.md). |
|Updateklassifizierungen|Wählen Sie alle benötigten Updateklassifizierungen aus|
|Einschließen/Ausschließen von Updates|Daraufhin wird die Seite **Einschließen/ausschließen** geöffnet. Updates, die eingeschlossen oder ausgeschlossen werden sollen, befinden sich auf verschiedenen Registerkarten. Weitere Informationen zur Vorgehensweise beim Einschließen finden Sie unter [Verhalten beim Einschließen](../../automation/automation-update-management.md#inclusion-behavior). |
|Zeitplaneinstellungen|Wählen Sie den Startzeitpunkt aus, und geben Sie unter „Wiederholung“ entweder „Einmal“ oder „Serie“ an|
| Vor und nach dem Vorgang auszuführende Skripts|Wählen Sie die Skripts aus, die vor und nach Ihrer Bereitstellung ausgeführt werden sollen.|
| Wartungsfenster |Festgelegte Minutenanzahl für Updates Der Wert darf nicht unter 30 Minuten und nicht über 6 Stunden betragen. |
| Neustartsteuerung| Legt fest, wie Neustarts behandelt werden sollen. Die verfügbaren Optionen lauten wie folgt:</br>Neu starten bei Bedarf (Standard)</br>Immer neu starten</br>Nie neu starten</br>Nur neu starten – Updates werden nicht installiert|

Updatebereitstellungen können ebenfalls programmgesteuert erstellt werden. Weitere Informationen zum Erstellen einer Updatebereitstellung mit der REST-API finden Sie unter [Softwareupdatekonfigurationen – Erstellen](/rest/api/automation/softwareupdateconfigurations/create). Es gibt auch ein Beispielrunbook, das zum Erstellen einer wöchentlichen Updatebereitstellung verwendet werden kann. Weitere Informationen zu diesem Runbook finden Sie unter [Erstellen einer wöchentlichen Updatebereitstellung für einen oder mehrere virtuelle Computer in einer Ressourcengruppe](https://gallery.technet.microsoft.com/scriptcenter/Create-a-weekly-update-2ad359a1).

Nachdem Sie die Konfiguration des Zeitplans abgeschlossen haben, klicken Sie auf die Schaltfläche **Erstellen**. Sie kehren damit zum Statusdashboard zurück.
Beachten Sie, dass die Tabelle **Geplant** den von Ihnen erstellten Bereitstellungszeitplan anzeigt.

### <a name="view-results-of-an-update-deployment"></a>Anzeigen der Ergebnisse einer Updatebereitstellung

Nach dem Start der geplanten Bereitstellung sehen Sie den Status der Bereitstellung auf der Registerkarte **Updatebereitstellungen** auf dem Bildschirm **Updateverwaltung**.
Wenn sie aktuell ausgeführt wird, wird der Status **Vorgang wird ausgeführt** angezeigt. Nach erfolgreichem Abschluss ändert sich der Status in **Erfolgreich**.
Wenn bei einem oder mehreren Updates in der Bereitstellung ein Fehler auftritt, wird der Status **Der Vorgang ist teilweise fehlgeschlagen** angezeigt.
Klicken Sie auf die abgeschlossene Updatebereitstellung, um das Dashboard für diese Updatebereitstellung anzuzeigen.

![Updatebereitstellungs-Statusdashboard für bestimmte Bereitstellung](./media/tutorial-monitoring/manageupdates-view-results.png)

Die Kachel **Updateergebnisse** enthält eine Zusammenfassung der Gesamtzahl der Updates und die Ergebnisse der Bereitstellung auf dem virtuellen Computer.
Die Tabelle rechts enthält eine detaillierte Analyse der einzelnen Updates und die Installationsergebnisse, wobei jeweils einer der folgenden Werte infrage kommt:

* **Kein Versuch erfolgt:** Das Update wurde nicht installiert, da aufgrund des definierten Wartungsfensters nicht genügend Zeit zur Verfügung stand.
* **Erfolgreich:** Das Update wurde erfolgreich ausgeführt.
* **Fehler:** Bei der Aktualisierung ist ein Fehler aufgetreten.

Klicken Sie auf **Alle Protokolle**, um alle von der Bereitstellung erstellten Protokolleinträge anzuzeigen.

Klicken Sie auf die Kachel **Ausgabe**, um den Auftragsdatenstrom des Runbooks anzuzeigen, dass für die Verwaltung der Updatebereitstellung auf der Ziel-VM verantwortlich ist.

Klicken Sie auf **Fehler**, um ausführliche Informationen zu Fehlern aus der Bereitstellung anzuzeigen.

## <a name="monitor-changes-and-inventory"></a>Überwachen von Änderungen und Bestand

Sie können die Bestandsinformationen für Software, Dateien, Linux-Daemons, Windows-Dienste und Windows-Registrierungsschlüssel auf Ihren Computern sammeln und anzeigen. Durch das Nachverfolgen der Konfigurationen Ihrer Computer können Sie betriebsbezogene Problem in Ihrer Umgebung erkennen und ein besseres Verständnis des Zustands Ihrer Computer entwickeln.

### <a name="enable-change-and-inventory-management"></a>Aktivieren der Änderungs- und Bestandsverwaltung

So aktivieren Sie die Änderungs- und Bestandsverwaltung für Ihren virtuellen Computer:

1. Wählen Sie auf der linken Seite des Bildschirms die Option **Virtuelle Computer**.
2. Wählen Sie einen virtuellen Computer in der Liste aus.
3. Klicken Sie auf dem Bildschirm des virtuellen Computers im Abschnitt **Vorgänge** auf **Bestand** oder **Änderungsnachverfolgung**. Der Bildschirm **Änderungsnachverfolgung und Bestand aktivieren** wird geöffnet.

Konfigurieren Sie den gewünschten Standort, den Log Analytics-Arbeitsbereich und das Automation-Konto, und klicken Sie auf **Aktivieren**. Wenn die Felder ausgegraut sind, bedeutet dies, dass eine andere Automatisierungslösung für die VM aktiviert ist und derselbe Arbeitsbereich und dasselbe Automation-Konto verwendet werden müssen. Die Lösungen sind zwar im Menü getrennt, es handelt sich jedoch um dieselbe Lösung. Wenn Sie eine der Lösungen aktivieren, wird automatisch auch die andere Lösung für Ihren virtuellen Computer aktiviert.

![„Änderungsnachverfolgung und Bestand aktivieren“](./media/tutorial-monitoring/manage-inventory-enable.png)

Nach der Aktivierung der Lösung kann es einige Zeit dauern, während der Bestand auf dem virtuellen Computer gesammelt wird, bevor Daten angezeigt werden.

### <a name="track-changes"></a>Nachverfolgen von Änderungen

Wählen Sie auf Ihrer VM unter **VORGÄNGE** die Option **Änderungsnachverfolgung**. Klicken Sie auf **Einstellungen bearbeiten**, und die Seite **Änderungsnachverfolgung** wird angezeigt. Wählen Sie den Typ der Einstellung, die Sie verfolgen möchten, und klicken Sie dann zum Konfigurieren der Einstellungen auf **+ Hinzufügen**. Die verfügbaren Optionen für Windows sind:

* Windows-Registrierung
* Windows-Dateien

Ausführliche Informationen zur „Änderungsnachverfolgung“ finden Sie unter [Problembehandlung für Änderungen in Ihrer Umgebung](../../automation/automation-tutorial-troubleshoot-changes.md).

### <a name="view-inventory"></a>Anzeigen des Bestands

Wählen Sie auf Ihrer VM unter **VORGÄNGE** die Option **Bestand**. Auf der Registerkarte **Software** wird eine Tabellenliste mit der gefundenen Software angezeigt. Die allgemeinen Details zu den einzelnen Softwaredatensätzen können in der Tabelle angezeigt werden. Zu diesen Details zählen Softwarename, Version, Herausgeber und der Zeitpunkt der letzten Aktualisierung.

![Anzeigen des Bestands](./media/tutorial-monitoring/inventory-view-results.png)

### <a name="monitor-activity-logs-and-changes"></a>Überwachen von Aktivitätsprotokollen und Änderungen

Wählen Sie auf Ihrer VM auf der Seite **Änderungsnachverfolgung** die Option **Aktivitätsprotokollverbindung verwalten**. Mit diesem Vorgang wird die Seite **Azure-Aktivitätsprotokoll** geöffnet. Wählen Sie **Verbinden**, um die Änderungsnachverfolgung mit der Azure-Aktivität für Ihre VM zu verbinden.

Navigieren Sie bei aktivierter Einstellung auf die Seite **Übersicht** Ihrer VM, und wählen Sie **Beenden**, um die VM zu beenden. Wählen Sie nach der entsprechenden Aufforderung **Ja**, um die VM zu beenden. Wählen Sie nach der Aufhebung der Zuordnung die Option **Starten**, um die VM neu zu starten.

Beim Beenden und Starten einer VM wird im Aktivitätsprotokoll dazu ein Ereignis protokolliert. Navigieren Sie zurück zur Seite **Änderungsnachverfolgung**. Wählen Sie unten auf der Seite die Registerkarte **Ereignisse**. Nach kurzer Wartezeit werden die Ereignisse im Diagramm und in der Tabelle angezeigt. Durch Klicken auf die einzelnen Ereignisse können Sie ausführliche Informationen anzeigen.

![Anzeigen von Änderungen im Aktivitätsprotokoll](./media/tutorial-monitoring/manage-activitylog-view-results.png)

Im Diagramm werden Änderungen angezeigt, die im Laufe der Zeit durchgeführt wurden. Nachdem Sie eine Aktivitätsprotokollverbindung hinzugefügt haben, werden oben im Liniendiagramm Azure-Aktivitätsprotokollereignisse angezeigt. Jede Zeile mit Balkendiagrammen steht für einen eigenen Änderungstyp, der nachverfolgt werden kann. Diese Typen sind Linux-Daemons, Dateien, Windows-Registrierungsschlüssel, Software und Windows-Dienste. Auf der Registerkarte mit den Änderungen werden die Details für die angezeigten Änderungen der Visualisierung in absteigender Reihenfolge ihres Auftretens aufgeführt (neueste zuerst).

## <a name="advanced-monitoring"></a>Erweiterte Überwachung

Sie können die erweiterte Überwachung Ihres virtuellen Computers mit Lösungen von [Azure Automation](../../automation/automation-intro.md) wie Updateverwaltung sowie Änderungs- und Bestandsverwaltung durchführen.

Wenn Sie Zugriff auf den Log Analytics-Arbeitsbereich haben, finden Sie den Arbeitsbereichsschlüssel und die Arbeitsbereich-ID, indem Sie unter **EINSTELLUNGEN** auf **Erweiterte Einstellungen** klicken. Verwenden Sie den Befehl [Set-AzVMExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmextension), um dem virtuellen Computer die Microsoft Monitoring Agent-Erweiterung hinzufügen. Aktualisieren Sie die Variablenwerte im nachstehenden Beispiel gemäß Ihrem Log Analytics-Arbeitsbereichsschlüssel und Ihrer Arbeitsbereichs-ID.

```powershell
$workspaceId = "<Replace with your workspace Id>"
$key = "<Replace with your primary key>"

Set-AzVMExtension -ResourceGroupName "myResourceGroupMonitor" `
  -ExtensionName "Microsoft.EnterpriseCloud.Monitoring" `
  -VMName "myVM" `
  -Publisher "Microsoft.EnterpriseCloud.Monitoring" `
  -ExtensionType "MicrosoftMonitoringAgent" `
  -TypeHandlerVersion 1.0 `
  -Settings @{"workspaceId" = $workspaceId} `
  -ProtectedSettings @{"workspaceKey" = $key} `
  -Location "East US"
```

Nach wenigen Minuten sollte der neue virtuelle Computer im Log Analytics-Arbeitsbereich angezeigt werden.

![Blatt des Log Analytics-Arbeitsbereichs](./media/tutorial-monitoring/tutorial-monitor-oms.png)

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie virtuelle Computer mit Azure Security Center konfiguriert und überprüft. Es wurde Folgendes vermittelt:

> [!div class="checklist"]
> * Erstellen eines virtuellen Netzwerks
> * Erstellen einer Ressourcengruppe und VM
> * Aktivieren der Startdiagnose auf dem virtuellen Computer
> * Anzeigen der Startdiagnose
> * Anzeigen von Hostmetriken
> * Bereitstellen der Diagnoseerweiterung
> * Anzeigen von Metriken des virtuellen Computers
> * Erstellen einer Warnung
> * Verwalten von Windows-Updates
> * Überwachen von Änderungen und Bestand
> * Einrichten der erweiterten Überwachung

Im nächsten Tutorial erhalten Sie Informationen zu Azure Security Center.

> [!div class="nextstepaction"]
> [Verwalten der VM-Sicherheit](./tutorial-azure-security.md)
