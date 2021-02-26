---
title: 'Tutorial: Überwachen virtueller Windows-Computer in Azure'
description: In diesem Tutorial erfahren Sie, wie Sie die Leistung und die ermittelten Anwendungskomponenten überwachen, die auf Ihren virtuellen Windows-Computern ausgeführt werden.
author: mgoedtel
manager: carmonm
ms.service: virtual-machines-windows
ms.subservice: monitoring
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 09/27/2018
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: 3e7d93d7a1d0e804f340a7ce785ccc1165e93ee5
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100570070"
---
# <a name="tutorial-monitor-a-windows-virtual-machine-in-azure"></a>Tutorial: Überwachen eines virtuellen Windows-Computers in Azure

Bei der Überwachung von Azure werden Agents verwendet, um Start- und Leistungsdaten von Azure-VMs zu sammeln, in Azure zu speichern und über das Portal, das Azure PowerShell-Modul und die Azure-Befehlszeilenschnittstelle verfügbar zu machen. Die erweiterte Überwachung wird mit Azure Monitor für VMs bereitgestellt, indem Leistungsmetriken erfasst und auf der VM installierte Anwendungskomponenten ermittelt werden. Darüber hinaus werden Leistungsdiagramme und Abhängigkeitszuordnung zur Verfügung gestellt.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Aktivieren der Startdiagnose auf einem virtuellen Computer
> * Anzeigen der Startdiagnose
> * Anzeigen der VM-Hostmetriken
> * Aktivieren von Azure Monitor für VMs
> * Anzeigen von VM-Leistungsmetriken
> * Erstellen einer Warnung

## <a name="launch-azure-cloud-shell"></a>Starten von Azure Cloud Shell

Azure Cloud Shell ist eine kostenlose interaktive Shell, mit der Sie die Schritte in diesem Artikel ausführen können. Sie verfügt über allgemeine vorinstallierte Tools und ist für die Verwendung mit Ihrem Konto konfiguriert. 

Wählen Sie zum Öffnen von Cloud Shell oben rechts in einem Codeblock einfach die Option **Ausprobieren**. Sie können Cloud Shell auch auf einer separaten Browserregisterkarte starten, indem Sie zu [https://shell.azure.com/powershell](https://shell.azure.com/powershell) navigieren. Wählen Sie **Kopieren**, um die Blöcke mit dem Code zu kopieren. Fügen Sie ihn anschließend in Cloud Shell ein, und drücken Sie die EINGABETASTE, um ihn auszuführen.

## <a name="create-virtual-machine"></a>Erstellen eines virtuellen Computers

Zum Konfigurieren von Azure-Überwachung und Updateverwaltung in diesem Tutorial benötigen Sie einen virtuellen Windows-Computer in Azure. Legen Sie mit [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential) zuerst den Benutzernamen und das Kennwort des VM-Administrators fest:

```azurepowershell-interactive
$cred = Get-Credential
```

Erstellen Sie nun mit [New-AzVM](/powershell/module/az.compute/new-azvm) den virtuellen Computer. Im folgenden Beispiel wird eine VM mit dem Namen *myVM* für den Standort *EastUS* erstellt. Falls sie nicht bereits vorhanden sind, werden die Ressourcengruppe *myResourceGroupMonitorMonitor* und unterstützende Netzwerkressourcen erstellt:

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

Sie erhalten die Startdiagnosedaten mit dem Befehl [Get AzureRmVMBootDiagnosticsData](/powershell/module/az.compute/get-azvmbootdiagnosticsdata). Im folgenden Beispiel wird die Startdiagnose in den Stammordner des Laufwerks *c:\* heruntergeladen.

```powershell
Get-AzVMBootDiagnosticsData -ResourceGroupName "myResourceGroupMonitor" -Name "myVM" -Windows -LocalPath "c:\"
```

## <a name="view-host-metrics"></a>Anzeigen von Hostmetriken

Eine Windows-VM verfügt über eine dedizierte Host-VM in Azure, mit der sie interagiert. Es werden automatisch Metriken für den Host gesammelt, die im Azure-Portal angezeigt werden können.

1. Klicken Sie im Azure-Portal auf **Ressourcengruppen**, und wählen Sie **myResourceGroupMonitor** und dann in der Ressourcenliste **myVM** aus.
2. Klicken Sie in dem Blatt der VM auf **Metriken**, und wählen Sie dann eine der Host-Metriken aus **Verfügbare Metriken**  aus.

    ![Anzeigen von Hostmetriken](./media/tutorial-monitoring/tutorial-monitor-host-metrics.png)

## <a name="enable-advanced-monitoring"></a>Aktivieren der erweiterten Überwachung

So aktivieren Sie die Überwachung Ihrer virtuellen Azure-Computer mit Azure Monitor für VMs:

1. Klicken Sie im Azure-Portal auf **Ressourcengruppen**, und wählen Sie **myResourceGroupMonitor** und dann in der Ressourcenliste **myVM** aus.

2. Wählen Sie auf der Seite der VM im Abschnitt **Überwachung** den Eintrag **Insights (Vorschau)** aus.

3. Wählen Sie auf der Seite **Insights (Vorschau)** **Jetzt testen** aus.

    ![Aktivieren von Azure Monitor for VMs für eine VM](../../azure-monitor/vm/media/vminsights-enable-portal/enable-vminsights-vm-portal.png)

4. Wenn Sie im selben Abonnement einen Log Analytics-Arbeitsbereich haben, wählen Sie ihn auf der Seite **Azure Monitor Insights Onboarding** (Onboarding von Azure Monitor Insights) in der Dropdownliste aus.  

    In der Liste sind Standardarbeitsbereich und Speicherort, in dem die VM im Abonnement bereitgestellt wird, vorab ausgewählt. 

    >[!NOTE]
    >Informationen zum Erstellen eines neuen Log Analytics-Arbeitsbereichs für das Speichern der Überwachungsdaten aus der VM finden Sie unter [Erstellen eines Log Analytics-Arbeitsbereichs](../../azure-monitor/logs/quick-create-workspace.md). Der Arbeitsbereich muss einer der [unterstützten Regionen](../../azure-monitor/vm/vminsights-configure-workspace.md#supported-regions) angehören.

Nachdem Sie die Überwachung aktiviert haben, müssen Sie möglicherweise etwa einige Minuten warten, bis die Leistungsmetriken für die VM angezeigt werden.

![Aktivieren von Azure Monitor for VMs – Verarbeiten der Überwachungsbereitstellung](../../azure-monitor/vm/media/vminsights-enable-portal/onboard-vminsights-vm-portal-status.png)

## <a name="view-vm-performance-metrics"></a>Anzeigen von VM-Leistungsmetriken

Azure Monitor for VMs beinhaltet einen Satz Leistungsdiagramme, die auf verschiedene Key Performance Indicators (KPIs) abzielen, um Sie beim Bestimmen der Leistung eines virtuellen Computers zu unterstützen. Führen Sie für den Zugriff über die VM die folgenden Schritte aus:

1. Klicken Sie im Azure-Portal auf **Ressourcengruppen**, und wählen Sie **myResourceGroupMonitor** und dann in der Ressourcenliste **myVM** aus.

2. Wählen Sie auf der Seite der VM im Abschnitt **Überwachung** den Eintrag **Insights (Vorschau)** aus.

3. Wählen Sie die Registerkarte **Leistung** aus.

Diese Seite enthält nicht nur Diagramme zur Leistungsauslastung, sondern auch eine Tabelle, die für jeden ermittelten logischen Datenträger dessen Kapazität, die Auslastung und den Gesamtmittelwert für jede Kennzahl anzeigt.

## <a name="create-alerts"></a>Erstellen von Warnungen

Sie können Warnungen auf Grundlage von bestimmten Leistungsmetriken erstellen. Warnungen können für Benachrichtigungen verwendet werden, wenn z.B. die durchschnittliche CPU-Auslastung einen bestimmten Schwellenwert überschreitet oder wenn der verfügbare freie Speicherplatz unter einen bestimmten Wert fällt. Warnungen werden im Azure-Portal angezeigt oder können per E-Mail gesendet werden. Sie können auch als Reaktion auf die generierten Warnungen Azure Automation-Runbooks oder Azure Logic Apps auslösen.

Das folgende Beispiel erstellt eine Warnung für die durchschnittliche CPU-Auslastung.

1. Klicken Sie im Azure-Portal auf **Ressourcengruppen**, und wählen Sie **myResourceGroupMonitor** und dann in der Ressourcenliste **myVM** aus.

2. Klicken Sie auf dem Blatt des virtuellen Computers auf **Warnungsregeln** und dann am oberen Rand des Warnungsblatts auf **Metrikwarnung hinzufügen**.

3. Geben Sie einen **Namen** für die Warnung ein, z.B. *myAlertRule*.

4. Um eine Warnung auszulösen, wenn der CPU-Prozentsatz 1.0 für fünf Minuten überschreitet, belassen Sie alle anderen Standardeinstellungen ausgewählt.

5. Aktivieren Sie optional das Kontrollkästchen *E-Mail-Besitzer, Mitwirkende und Leser*, um E-Mail-Benachrichtigungen zu senden. Als Standardaktion wird im Portal eine Benachrichtigung angezeigt.

6. Klicken Sie auf die Schaltfläche **OK** .

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie die Leistung Ihrer VM konfiguriert und angezeigt. Sie haben Folgendes gelernt:

> [!div class="checklist"]
> * Erstellen einer Ressourcengruppe und VM
> * Aktivieren der Startdiagnose auf dem virtuellen Computer
> * Anzeigen der Startdiagnose
> * Anzeigen von Hostmetriken
> * Aktivieren von Azure Monitor für VMs
> * Anzeigen von Metriken des virtuellen Computers
> * Erstellen einer Warnung

Im nächsten Tutorial erhalten Sie Informationen zu Azure Security Center.

> [!div class="nextstepaction"]
> [Verwalten der VM-Sicherheit](../../security/fundamentals/overview.md)
