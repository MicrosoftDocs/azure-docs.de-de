---
title: 'Tutorial: Verwalten der Konfiguration eines virtuellen Linux-Computers in Azure'
description: In diesem Tutorial erfahren Sie, wie Sie auf einem virtuellen Linux-Computer Änderungen ermitteln und Paketupdates verwalten.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: mgoedtel
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/27/2019
ms.author: magoedte
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 3210829b3281aa862cdf0dbdc9c915249a55e423
ms.sourcegitcommit: 5831eebdecaa68c3e006069b3a00f724bea0875a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2020
ms.locfileid: "94518004"
---
# <a name="tutorial-monitor-changes-and-update-a-linux-virtual-machine-in-azure"></a>Tutorial: Überwachen von Änderungen und Aktualisieren eines virtuellen Linux-Computers in Azure

Die [Änderungsnachverfolgung](../../automation/change-tracking/overview.md) von Azure ermöglicht Ihnen das einfache Identifizieren von Änderungen, und mit der [Updateverwaltung](../../automation/update-management/overview.md) können Sie Betriebssystemupdates für Ihre virtuellen Azure-Linux-Computer verwalten.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Verwalten von Linux-Updates
> * Überwachen von Änderungen und Bestand

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Für dieses Tutorial ist mindestens Version 2.0.30 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert.

## <a name="create-vm"></a>Erstellen eines virtuellen Computers

Um die Diagnose und die Metriken in Aktion anzuzeigen, benötigen Sie einen virtuellen Computer. Erstellen Sie zunächst mit [az group create](/cli/azure/group#az-group-create) eine Ressourcengruppe. Das folgende Beispiel erstellt die Ressourcengruppe *myResourceGroupMonitor* am Standort *eastus*.

```azurecli-interactive
az group create --name myResourceGroupMonitor --location eastus
```

Jetzt können Sie mit [az vm create](/cli/azure/vm#az-vm-create) einen virtuellen Computer erstellen. Im folgenden Beispiel wird ein virtueller Computer namens *myVM* erstellt, und es werden SSH-Schlüssel generiert, sofern noch nicht unter *~/.ssh/* vorhanden:

```azurecli-interactive
az vm create \
  --resource-group myResourceGroupMonitor \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="manage-software-updates"></a>Verwalten von Softwareupdates

Mithilfe der Updateverwaltung können Sie Updates und Patches für Ihre Linux-basierten Azure-VMs verwalten.
Direkt von Ihrem virtuellen Computer aus können Sie schnell den Status der verfügbaren Updates bewerten, die Installation der erforderlichen Updates planen und Bereitstellungsergebnisse überprüfen, um sicherzustellen, dass Updates erfolgreich auf den virtuellen Computer angewendet wurden.

Informationen zu den Preisen finden Sie unter [Automation – Preise](https://azure.microsoft.com/pricing/details/automation/) unter „Updateverwaltung“.

### <a name="enable-update-management"></a>Aktivieren der Updateverwaltung

So aktivieren Sie die Updateverwaltung für Ihre VM

1. Wählen Sie auf der linken Seite des Bildschirms die Option **Virtuelle Computer**.
2. Wählen Sie einen virtuellen Computer in der Liste aus.
3. Klicken Sie auf dem Bildschirm des virtuellen Computers im Abschnitt **Vorgänge** auf **Updateverwaltung**. Der Bildschirm **Updateverwaltung aktivieren** wird geöffnet.

Eine Überprüfung wird ausgeführt, um festzustellen, ob die Updateverwaltung für diesen virtuellen Computer aktiviert ist.
Die Überprüfung umfasst Prüfungen für einen Log Analytics-Arbeitsbereich und ein verknüpftes Automation-Konto, und ob die Lösung im Arbeitsbereich vorhanden ist.

Mit einem [Log Analytics](../../azure-monitor/log-query/log-query-overview.md)-Arbeitsbereich werden Daten gesammelt, die von Features und Diensten wie der Updateverwaltung generiert werden.
Der Arbeitsbereich ist ein zentraler Ort zum Überprüfen und Analysieren von Daten aus mehreren Quellen.
Um weitere Aktionen auf virtuellen Computern auszuführen, die Updates erfordern, können Sie mit Azure Automation Runbooks für virtuelle Computer ausführen, z.B. um Updates herunterzuladen und anzuwenden.

Beim Überprüfungsprozess wird auch geprüft, ob der virtuelle Computer mit dem Log Analytics-Agent und Automation Hybrid Runbook Worker bereitgestellt wird. Dieser Agent wird verwendet, um mit dem virtuellen Computer zu kommunizieren und Informationen zum Updatestatus abzurufen.

Wählen Sie den Log Analytics-Arbeitsbereich und das Automation-Konto aus, und klicken Sie auf **Aktivieren**, um die Lösung zu aktivieren. Es dauert ungefähr 15 Minuten, bis die Lösung aktiviert ist.

Wenn beim Onboarding festgestellt wird, dass eine der folgenden Voraussetzungen fehlt, wird sie automatisch hinzugefügt:

* [Log Analytics](../../azure-monitor/log-query/log-query-overview.md)-Arbeitsbereich
* [Automation-Konto](../../automation/index.yml)
* Ein [Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md) ist auf dem virtuellen Computer aktiviert.

Der Bildschirm **Updateverwaltung** wird geöffnet. Konfigurieren Sie den Standort, den Log Analytics-Arbeitsbereich und das Automation-Konto, und wählen Sie **Aktivieren** aus. Wenn die Felder ausgegraut sind, bedeutet dies, dass eine andere Automatisierungslösung für die VM aktiviert ist und derselbe Arbeitsbereich und dasselbe Automation-Konto verwendet werden müssen.

![Aktivieren der Updateverwaltungslösung](./media/tutorial-monitoring/manage-updates-update-enable.png)

Das Aktivieren der Lösung kann bis zu 15 Minuten dauern. Während dieses Zeitraums sollten Sie das Browserfenster nicht schließen. Nachdem die Lösung aktiviert wurde, werden Informationen zu fehlenden Updates auf dem virtuellen Computer an Azure Monitor-Protokolle übermittelt. Es kann zwischen 30 Minuten und 6 Stunden dauern, bis die Daten für die Analyse verfügbar sind.

### <a name="view-update-assessment"></a>Anzeigen der Updatebewertung

Sobald die **Updateverwaltung** aktiviert ist, wird der Bildschirm **Updateverwaltung** angezeigt. Nach Abschluss der Bewertung von Updates wird auf der Registerkarte **Fehlende Updates** eine Liste mit den entsprechenden Updates angezeigt.

 ![Anzeigen des Updatestatus](./media/tutorial-monitoring/manage-updates-view-status-linux.png)

### <a name="schedule-an-update-deployment"></a>Planen einer Updatebereitstellung

Planen Sie zum Installieren von Updates eine Bereitstellung, die Ihrem Releasezeitplan und Wartungsfenster entspricht. Sie können auswählen, welche Updatetypen in die Bereitstellung eingeschlossen werden sollen. Beispielsweise können Sie kritische oder Sicherheitsupdates einschließen und Updaterollups ausschließen.

Um eine neue Updatebereitstellung für den virtuellen Computer zu planen, klicken Sie auf **Updatebereitstellung planen** am oberen Rand des Bildschirms **Updateverwaltung**. Geben Sie auf dem Bildschirm **Neue Updatebereitstellung** die folgenden Informationen ein:

Wählen Sie zum Erstellen einer neuen Updatebereitstellung **Updatebereitstellung planen** aus. Die Seite **Neue Updatebereitstellung** wird geöffnet. Geben Sie Werte für die Eigenschaften ein, die in der folgenden Tabelle beschrieben werden, und klicken Sie auf **Erstellen**:

| Eigenschaft | BESCHREIBUNG |
| --- | --- |
| Name |Eindeutiger Name zum Identifizieren der Updatebereitstellung |
|Betriebssystem| Linux oder Windows|
| Zu aktualisierende Gruppen |Definieren Sie für Azure-Computer eine Abfrage basierend auf einer Kombination aus Abonnement, Ressourcengruppen, Standorten und Tags, um eine dynamische Gruppe von virtuellen Azure-Computern zu erstellen, die in die Bereitstellung eingeschlossen werden sollen. </br></br>Wählen Sie für Azure-fremde Computer eine vorhandene gespeicherte Suche aus, um eine Gruppe von Azure-fremden Computern auszuwählen, die in die Bereitstellung eingeschlossen werden sollen. </br></br>Weitere Informationen finden Sie unter [Dynamische Gruppen](../../automation/update-management/configure-groups.md).|
| Zu aktualisierende Computer |Wählen Sie eine gespeicherte Suche oder eine importierte Gruppe aus, oder wählen Sie im Dropdownmenü „Computer“ und dann einzelne Computer aus. Bei Auswahl von **Computer** wird die Bereitschaft des Computers in der Spalte **BEREITSCHAFT DES UPDATE-AGENTS** angezeigt.</br> Weitere Informationen zu den verschiedenen Methoden zum Erstellen von Computergruppen in Azure Monitor-Protokollen finden Sie unter [Computergruppen in Azure Monitor-Protokollen](../../azure-monitor/platform/computer-groups.md). |
|Updateklassifizierungen|Wählen Sie alle benötigten Updateklassifizierungen aus|
|Einschließen/Ausschließen von Updates|Daraufhin wird die Seite **Einschließen/ausschließen** geöffnet. Updates, die eingeschlossen oder ausgeschlossen werden sollen, befinden sich auf verschiedenen Registerkarten. Weitere Informationen zur Vorgehensweise beim Einschließen finden Sie unter [Planen einer Updatebereitstellung](../../automation/update-management/deploy-updates.md#schedule-an-update-deployment). |
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

![Updatebereitstellungs-Statusdashboard für bestimmte Bereitstellung](./media/tutorial-monitoring/manage-updates-view-results.png)

Die Kachel **Updateergebnisse** enthält eine Zusammenfassung der Gesamtzahl der Updates und die Ergebnisse der Bereitstellung auf dem virtuellen Computer.
Die Tabelle rechts enthält eine detaillierte Analyse der einzelnen Updates und die Installationsergebnisse, wobei jeweils einer der folgenden Werte infrage kommt:

* **Kein Versuch erfolgt:** Das Update wurde nicht installiert, da aufgrund des definierten Wartungsfensters nicht genügend Zeit zur Verfügung stand.
* **Erfolgreich:** Das Update wurde erfolgreich ausgeführt.
* **Fehler:** Bei der Aktualisierung ist ein Fehler aufgetreten.

Klicken Sie auf **Alle Protokolle**, um alle von der Bereitstellung erstellten Protokolleinträge anzuzeigen.

Klicken Sie auf die Kachel **Ausgabe**, um den Auftragsdatenstrom des Runbooks anzuzeigen, das für die Verwaltung der Updatebereitstellung auf dem virtuellen Zielcomputer verantwortlich ist.

Klicken Sie auf **Fehler**, um ausführliche Informationen zu Fehlern bei der Bereitstellung anzuzeigen.

## <a name="monitor-changes-and-inventory"></a>Überwachen von Änderungen und Bestand

Sie können die Bestandsinformationen für Software, Dateien, Linux-Daemons, Windows-Dienste und Windows-Registrierungsschlüssel auf Ihren Computern sammeln und anzeigen. Durch das Nachverfolgen der Konfigurationen Ihrer Computer können Sie betriebsbezogene Problem in Ihrer Umgebung erkennen und ein besseres Verständnis des Zustands Ihrer Computer entwickeln.

### <a name="enable-change-and-inventory-management"></a>Aktivieren der Änderungs- und Bestandsverwaltung

So aktivieren Sie die Änderungs- und Bestandsverwaltung für Ihren virtuellen Computer:

1. Wählen Sie auf der linken Seite des Bildschirms die Option **Virtuelle Computer**.
2. Wählen Sie einen virtuellen Computer in der Liste aus.
3. Klicken Sie auf dem Bildschirm des virtuellen Computers im Abschnitt **Vorgänge** auf **Bestand** oder **Änderungsnachverfolgung**. Der Bildschirm **Änderungsnachverfolgung und Bestand aktivieren** wird geöffnet.

Konfigurieren Sie den Standort, den Log Analytics-Arbeitsbereich und das Automation-Konto, und wählen Sie **Aktivieren** aus. Wenn die Felder ausgegraut sind, bedeutet dies, dass eine andere Automatisierungslösung für die VM aktiviert ist und derselbe Arbeitsbereich und dasselbe Automation-Konto verwendet werden müssen. Die Lösungen sind zwar im Menü getrennt, es handelt sich jedoch um dieselbe Lösung. Wenn Sie eine der Lösungen aktivieren, wird automatisch auch die andere Lösung für Ihren virtuellen Computer aktiviert.

![Aktivieren der Änderungs- und Bestandsnachverfolgung](./media/tutorial-monitoring/manage-inventory-enable.png)

Nach Aktivierung der Lösung kann es einige Zeit dauern, bis der Bestand auf dem virtuellen Computer erfasst wurde und Daten angezeigt werden.

### <a name="track-changes"></a>Nachverfolgen von Änderungen

Klicken Sie auf Ihrem virtuellen Computer unter **VORGÄNGE** auf **Änderungsnachverfolgung**. Klicken Sie auf **Einstellungen bearbeiten**. Daraufhin wird die Seite **Änderungsnachverfolgung** angezeigt. Wählen Sie die Art der Einstellung aus, die Sie nachverfolgen möchten, und klicken Sie dann zum Konfigurieren der Einstellungen auf **+ Hinzufügen**. Die verfügbare Option für Linux ist **Linux-Dateien**.

Ausführliche Informationen zur Änderungsnachverfolgung finden Sie unter [Problembehandlung für Änderungen in Ihrer Umgebung](../../automation/automation-tutorial-troubleshoot-changes.md).

### <a name="view-inventory"></a>Anzeigen des Bestands

Klicken Sie auf Ihrem virtuellen Computer unter **VORGÄNGE** auf **Bestand**. Auf der Registerkarte **Software** wird eine Tabellenliste mit der gefundenen Software angezeigt. Die allgemeinen Details zu den einzelnen Softwaredatensätzen können in der Tabelle angezeigt werden. Zu diesen Details zählen Softwarename, Version, Herausgeber und der Zeitpunkt der letzten Aktualisierung.

![Anzeigen des Bestands](./media/tutorial-monitoring/inventory-view-results.png)

### <a name="monitor-activity-logs-and-changes"></a>Überwachen von Aktivitätsprotokollen und Änderungen

Wählen Sie auf Ihrer VM auf der Seite **Änderungsnachverfolgung** die Option **Aktivitätsprotokollverbindung verwalten**. Mit diesem Vorgang wird die Seite **Azure-Aktivitätsprotokoll** geöffnet. Wählen Sie **Verbinden**, um die Änderungsnachverfolgung mit der Azure-Aktivität für Ihre VM zu verbinden.

Navigieren Sie bei aktivierter Einstellung auf die Seite **Übersicht** Ihrer VM, und wählen Sie **Beenden**, um die VM zu beenden. Wählen Sie nach der entsprechenden Aufforderung **Ja**, um die VM zu beenden. Wählen Sie nach der Aufhebung der Zuordnung die Option **Starten**, um die VM neu zu starten.

Beim Beenden und Starten einer VM wird im Aktivitätsprotokoll dazu ein Ereignis protokolliert. Navigieren Sie zurück zur Seite **Änderungsnachverfolgung**. Wählen Sie unten auf der Seite die Registerkarte **Ereignisse**. Nach kurzer Wartezeit werden die Ereignisse im Diagramm und in der Tabelle angezeigt. Durch Klicken auf die einzelnen Ereignisse können Sie ausführliche Informationen anzeigen.

![Anzeigen von Änderungen im Aktivitätsprotokoll](./media/tutorial-monitoring/manage-activitylog-view-results.png)

Im Diagramm werden Änderungen angezeigt, die im Laufe der Zeit durchgeführt wurden. Nachdem Sie eine Aktivitätsprotokollverbindung hinzugefügt haben, werden oben im Liniendiagramm Azure-Aktivitätsprotokollereignisse angezeigt. Jede Zeile mit Balkendiagrammen steht für einen eigenen Änderungstyp, der nachverfolgt werden kann. Bei diesen Typen handelt es sich um Linux-Daemons, -Dateien und -Software. Auf der Registerkarte mit den Änderungen werden die Details für die angezeigten Änderungen der Visualisierung in absteigender Reihenfolge ihres Auftretens aufgeführt (neueste zuerst).

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie die Änderungsnachverfolgung und Updateverwaltung für Ihre virtuellen Computer konfiguriert und überprüft. Sie haben Folgendes gelernt:

> [!div class="checklist"]
> * Erstellen einer Ressourcengruppe und VM
> * Verwalten von Linux-Updates
> * Überwachen von Änderungen und Bestand

Im nächsten Tutorial erhalten Sie Informationen zur Überwachung virtueller Computer.

> [!div class="nextstepaction"]
> [Überwachen virtueller Computer](tutorial-monitor.md)
