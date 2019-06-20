---
title: 'Tutorial: Überwachen und Aktualisieren virtueller Linux-Computer in Azure | Microsoft-Dokumentation'
description: In diesem Tutorial erfahren Sie, wie Sie auf einem virtuellen Linux-Computer Startdiagnose und Leistungsmetriken überwachen und Paketupdates verwalten.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/26/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 921505e7f470d337d9e9e491c6db79930d487eb5
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/07/2019
ms.locfileid: "66754378"
---
# <a name="tutorial-monitor-and-update-a-linux-virtual-machine-in-azure"></a>Tutorial: Überwachen und Aktualisieren eines virtuellen Linux-Computers in Azure

Um sicherzustellen, dass die virtuellen Computer (VMs) in Azure ordnungsgemäß ausgeführt werden, können Sie Startdiagnose und Leistungsmetriken überprüfen sowie Paketupdates verwalten. In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Aktivieren der Startdiagnose auf dem virtuellen Computer
> * Anzeigen der Startdiagnose
> * Anzeigen von Hostmetriken
> * Aktivieren der Diagnoseerweiterung auf dem virtuellen Computer
> * Anzeigen von Metriken des virtuellen Computers
> * Erstellen von Benachrichtigungen basierend auf Diagnosemetriken
> * Verwalten von Paketupdates
> * Überwachen von Änderungen und Bestand
> * Einrichten der erweiterten Überwachung

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Wenn Sie die CLI lokal installieren und verwenden möchten, müssen Sie für dieses Tutorial die Azure CLI-Version 2.0.30 oder höher ausführen. Führen Sie `az --version` aus, um die Version zu finden. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sei bei Bedarf unter [Installieren der Azure CLI]( /cli/azure/install-azure-cli).

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

## <a name="enable-boot-diagnostics"></a>Aktivieren der Startdiagnose

Wenn virtuelle Linux-Computer starten, erfasst die Startdiagnoseerweiterung die Startausgabe und speichert sie in Azure Storage. Diese Daten können zum Beheben von Startproblemen bei virtuellen Computern verwendet werden. Die Startdiagnose wird nicht automatisch aktiviert, wenn Sie eine Linux-VM mithilfe der Azure-Befehlszeilenschnittstelle erstellen.

Vor dem Aktivieren der Startdiagnose muss ein Speicherkonto zum Speichern der Startprotokolle erstellt werden. Speicherkonten benötigen einen global eindeutigen Namen, der zwischen 3 und 24 Zeichen lang sein muss und nur Ziffern und Kleinbuchstaben enthalten darf. Sie erstellen ein Speicherkonto mit dem Befehl [az storage account create](/cli/azure/storage/account#az-storage-account-create). In diesem Beispiel wird eine zufällige Zeichenfolge verwendet, um einen eindeutigen Speicherkontonamen zu erstellen.

```azurecli-interactive
storageacct=mydiagdata$RANDOM

az storage account create \
  --resource-group myResourceGroupMonitor \
  --name $storageacct \
  --sku Standard_LRS \
  --location eastus
```

Bei der Aktivierung der Startdiagnose wird der URI zum Blob Storage-Container benötigt. Der folgende Befehl fragt das Speicherkonto ab, um diesen URI zurückzugeben. Der URI-Wert wird in der Variable *bloburi* gespeichert, die im nächsten Schritt verwendet wird.

```azurecli-interactive
bloburi=$(az storage account show --resource-group myResourceGroupMonitor --name $storageacct --query 'primaryEndpoints.blob' -o tsv)
```

Aktivieren Sie nun die Startdiagnose mit [az vm boot-diagnostics enable](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics#az-vm-boot-diagnostics-enable). Der `--storage`-Wert ist der Blob-URI, der im vorherigen Schritt erfasst wurde.

```azurecli-interactive
az vm boot-diagnostics enable \
  --resource-group myResourceGroupMonitor \
  --name myVM \
  --storage $bloburi
```

## <a name="view-boot-diagnostics"></a>Anzeigen der Startdiagnose

Wenn die Startdiagnose aktiviert ist, werden bei jedem Beenden und Starten des virtuellen Computers Informationen zum Startvorgang in eine Protokolldatei geschrieben. Heben Sie in diesem Beispiel zunächst die Zuordnung des virtuellen Computers mit dem Befehl [az vm deallocate](/cli/azure/vm#az-vm-deallocate) wie folgt auf:

```azurecli-interactive
az vm deallocate --resource-group myResourceGroupMonitor --name myVM
```

Starten Sie nun den virtuellen Computer mit dem Befehl [az vm start]( /cli/azure/vm#az-vm-stop) wie folgt:

```azurecli-interactive
az vm start --resource-group myResourceGroupMonitor --name myVM
```

Sie erhalten die Startdiagnosedaten für *myVM* mithilfe des Befehls [az vm boot-diagnostics get-boot-log](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics#az-vm-boot-diagnostics-get-boot-log) wie folgt:

```azurecli-interactive
az vm boot-diagnostics get-boot-log --resource-group myResourceGroupMonitor --name myVM
```

## <a name="view-host-metrics"></a>Anzeigen von Hostmetriken

Eine Linux-VM verfügt über einen dedizierten Host in Azure, mit dem sie interagiert. Es werden automatisch Metriken für den Host gesammelt, die folgendermaßen im Azure-Portal angezeigt werden können:

1. Klicken Sie im Azure-Portal auf **Ressourcengruppen**, und wählen Sie **myResourceGroupMonitor** und dann in der Ressourcenliste **myVM** aus.
1. Um die Leistung des virtuellen Hostcomputers anzuzeigen, klicken Sie im Fenster des virtuellen Computers auf **Metriken**, und wählen Sie dann eine der *[Host]* -Metriken unter **Verfügbare Metriken** aus.

    ![Anzeigen von Hostmetriken](./media/tutorial-monitoring/monitor-host-metrics.png)

## <a name="install-diagnostics-extension"></a>Installieren der Diagnoseerweiterung

Die grundlegenden Hostmetriken sind direkt verfügbar. Wenn Sie aber detailliertere und VM-spezifische Metriken anzeigen möchten, müssen Sie die Azure-Diagnose-Erweiterung auf dem virtuellen Computer installieren. Die Azure-Diagnose-Erweiterung ermöglicht das Abrufen zusätzlicher Überwachungs- und Diagnosedaten von virtuellen Computern. Sie können diese Leistungsmetriken anzeigen und basierend auf der VM-Leistung Benachrichtigungen erstellen. Die Diagnose-Erweiterung wird wie folgt über das Azure-Portal installiert:

1. Klicken Sie im Azure-Portal auf **Ressourcengruppen**, und wählen Sie **myResourceGroupMonitor** und dann in der Ressourcenliste **myVM** aus.
1. Klicken Sie auf **Diagnoseeinstellungen**. Wählen Sie im Dropdownmenü *Pick a storage account* (Speicherkonto auswählen) das im vorherigen Abschnitt erstellte Konto *mydiagdata[1234]* aus, sofern noch nicht geschehen.
1. Klicken Sie auf die Schaltfläche **Überwachung auf Gastebene aktivieren**.

    ![Anzeigen von Diagnosemetriken](./media/tutorial-monitoring/enable-diagnostics-extension.png)

## <a name="view-vm-metrics"></a>Anzeigen von Metriken des virtuellen Computers

Sie können die VM-Metriken auf die gleiche Weise anzeigen, wie Sie die Metriken zur Host-VM angezeigt haben:

1. Klicken Sie im Azure-Portal auf **Ressourcengruppen**, und wählen Sie **myResourceGroupMonitor** und dann in der Ressourcenliste **myVM** aus.
1. Um die Leistung des virtuellen Computers anzuzeigen, klicken Sie im Fenster des virtuellen Computers auf **Metriken**, und wählen Sie dann unter **Verfügbare Metriken** eine der Diagnosemetriken vom Typ *[Gast]* aus.

    ![Anzeigen von Metriken des virtuellen Computers](./media/tutorial-monitoring/monitor-vm-metrics.png)

## <a name="create-alerts"></a>Erstellen von Warnungen

Sie können Warnungen auf Grundlage von bestimmten Leistungsmetriken erstellen. Warnungen können für Benachrichtigungen verwendet werden, wenn z.B. die durchschnittliche CPU-Auslastung einen bestimmten Schwellenwert überschreitet oder wenn der verfügbare freie Speicherplatz unter einen bestimmten Wert fällt. Warnungen werden im Azure-Portal angezeigt oder können per E-Mail gesendet werden. Sie können auch als Reaktion auf die generierten Warnungen Azure Automation-Runbooks oder Azure Logic Apps auslösen.

Das folgende Beispiel erstellt eine Warnung für die durchschnittliche CPU-Auslastung.

1. Klicken Sie im Azure-Portal auf **Ressourcengruppen**, und wählen Sie **myResourceGroupMonitor** und dann in der Ressourcenliste **myVM** aus.
2. Klicken Sie auf **Warnungen (klassisch)** und anschließend oben im Warnungsfenster auf **Metrikwarnung hinzufügen (klassisch)** .
3. Geben Sie einen **Namen** für die Warnung ein, z.B. *myAlertRule*.
4. Um eine Warnung auszulösen, wenn der CPU-Prozentsatz 1.0 für fünf Minuten überschreitet, belassen Sie alle anderen Standardeinstellungen ausgewählt.
5. Aktivieren Sie optional das Kontrollkästchen *E-Mail-Besitzer, Mitwirkende und Leser*, um E-Mail-Benachrichtigungen zu senden. Als Standardaktion wird im Portal eine Benachrichtigung angezeigt.
6. Klicken Sie auf die Schaltfläche **OK**.

## <a name="manage-software-updates"></a>Verwalten von Softwareupdates

Mithilfe der Updateverwaltung können Sie Updates und Patches für Ihre Linux-basierten Azure-VMs verwalten.
Direkt von Ihrem virtuellen Computer aus können Sie schnell den Status der verfügbaren Updates bewerten, die Installation der erforderlichen Updates planen und Bereitstellungsergebnisse überprüfen, um sicherzustellen, dass Updates erfolgreich auf den virtuellen Computer angewendet wurden.

Informationen zu den Preisen finden Sie unter [Automation – Preise](https://azure.microsoft.com/pricing/details/automation/) unter „Updateverwaltung“.

### <a name="enable-update-management"></a>Aktivieren der Updateverwaltung

So aktivieren Sie die Updateverwaltung für Ihre VM

1. Wählen Sie auf der linken Seite des Bildschirms **Virtuelle Computer** aus.
2. Wählen Sie einen virtuellen Computer in der Liste aus.
3. Klicken Sie auf dem Bildschirm des virtuellen Computers im Abschnitt **Vorgänge** auf **Updateverwaltung**. Der Bildschirm **Updateverwaltung aktivieren** wird geöffnet.

Eine Überprüfung wird ausgeführt, um festzustellen, ob die Updateverwaltung für diesen virtuellen Computer aktiviert ist.
Die Überprüfung umfasst Prüfungen für einen Log Analytics-Arbeitsbereich und ein verknüpftes Automation-Konto, und ob die Lösung im Arbeitsbereich vorhanden ist.

Mit einem [Log Analytics](../../log-analytics/log-analytics-overview.md)-Arbeitsbereich werden Daten gesammelt, die von Features und Diensten wie der Updateverwaltung generiert werden.
Der Arbeitsbereich ist ein zentraler Ort zum Überprüfen und Analysieren von Daten aus mehreren Quellen.
Um weitere Aktionen auf virtuellen Computern auszuführen, die Updates erfordern, können Sie mit Azure Automation Runbooks für virtuelle Computer ausführen, z.B. um Updates herunterzuladen und anzuwenden.

Beim Überprüfungsprozess wird auch geprüft, ob der virtuelle Computer mit dem Log Analytics-Agent und Automation Hybrid Runbook Worker bereitgestellt wird. Dieser Agent wird verwendet, um mit dem virtuellen Computer zu kommunizieren und Informationen zum Updatestatus abzurufen.

Wählen Sie den Log Analytics-Arbeitsbereich und das Automation-Konto aus, und klicken Sie auf **Aktivieren**, um die Lösung zu aktivieren. Es dauert ungefähr 15 Minuten, bis die Lösung aktiviert ist.

Wenn beim Onboarding festgestellt wird, dass eine der folgenden Voraussetzungen fehlt, wird sie automatisch hinzugefügt:

* [Log Analytics](../../log-analytics/log-analytics-overview.md)-Arbeitsbereich
* [Automation-Konto](../../automation/automation-offering-get-started.md)
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

## <a name="advanced-monitoring"></a>Erweiterte Überwachung

Mit einer Lösung wie [Azure Monitor für VMs](../../azure-monitor/insights/vminsights-overview.md) stehen Ihnen erweiterte Optionen für die Überwachung Ihrer virtuellen Computer zur Verfügung. Die Lösung überwacht Ihre Azure-VMs bedarfsorientiert durch Analysieren der Leistung und Integrität der Windows- und Linux-VMs, einschließlich ihrer verschiedenen Prozesse und miteinander verbundenen Abhängigkeiten von anderen Ressourcen und externen Prozessen. Die Konfigurationsverwaltung Ihrer Azure-VMs wird mit der [Azure Automation](../../automation/automation-intro.md)-Lösung für die Änderungsnachverfolgung und den Bestand bereitgestellt, mit der Änderungen in Ihrer Umgebung einfach ermittelt werden können. Die Verwaltung der Updatekonformität erfolgt mit der Azure Automation-Updateverwaltungslösung.   

Über den Log Analytics-Arbeitsbereich, mit dem die VM verbunden ist, können Sie darüber hinaus gesammelte Daten mit der [umfassenden Abfragesprache](../../azure-monitor/log-query/log-query-overview.md) abrufen, konsolidieren und analysieren. 

![Log Analytics-Arbeitsbereich](./media/tutorial-monitoring/tutorial-monitor-oms.png)

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Updates für einen virtuellen Computer konfiguriert, überprüft und verwaltet. Es wurde Folgendes vermittelt:

> [!div class="checklist"]
> * Aktivieren der Startdiagnose auf dem virtuellen Computer
> * Anzeigen der Startdiagnose
> * Anzeigen von Hostmetriken
> * Aktivieren der Diagnoseerweiterung auf dem virtuellen Computer
> * Anzeigen von Metriken des virtuellen Computers
> * Erstellen von Benachrichtigungen basierend auf Diagnosemetriken
> * Verwalten von Paketupdates
> * Überwachen von Änderungen und Bestand
> * Einrichten der erweiterten Überwachung

Im nächsten Tutorial erhalten Sie Informationen zu Azure Security Center.

> [!div class="nextstepaction"]
> [Verwalten der VM-Sicherheit](./tutorial-azure-security.md)
