---
title: 'Tutorial: Überwachen virtueller Linux-Computer in Azure'
description: In diesem Tutorial erfahren Sie, wie Sie die Leistung und die ermittelten Anwendungskomponenten überwachen, die auf Ihren virtuellen Linux-Computern ausgeführt werden.
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
ms.date: 09/30/2019
ms.author: magoedte
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: a21df6e5f8d437415bb5376969d56d26153b5c5f
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96500460"
---
# <a name="tutorial-monitor-a-linux-virtual-machine-in-azure"></a>Tutorial: Überwachen eines virtuellen Linux-Computers in Azure

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

Wenn Sie die CLI lokal installieren und verwenden möchten, müssen Sie für dieses Tutorial die Azure CLI-Version 2.0.30 oder höher ausführen. Führen Sie `az --version` aus, um die Version zu ermitteln. Installations- und Upgradeinformationen finden Sie bei Bedarf unter [Installieren von Azure CLI](/cli/azure/install-azure-cli).

## <a name="create-vm"></a>Erstellen eines virtuellen Computers

Um die Diagnose und die Metriken in Aktion anzuzeigen, benötigen Sie einen virtuellen Computer. Erstellen Sie zunächst mit [az group create](/cli/azure/group?view=azure-cli-latest#az-group-create) eine Ressourcengruppe. Das folgende Beispiel erstellt die Ressourcengruppe *myResourceGroupMonitor* am Standort *eastus*.

```azurecli-interactive
az group create --name myResourceGroupMonitor --location eastus
```

Jetzt können Sie mit [az vm create](/cli/azure/vm?view=azure-cli-latest#az-vm-create) einen virtuellen Computer erstellen. Im folgenden Beispiel wird ein virtueller Computer namens *myVM* erstellt, und es werden SSH-Schlüssel generiert, sofern noch nicht unter *~/.ssh/* vorhanden:

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

Vor dem Aktivieren der Startdiagnose muss ein Speicherkonto zum Speichern der Startprotokolle erstellt werden. Speicherkonten benötigen einen global eindeutigen Namen, der zwischen 3 und 24 Zeichen lang sein muss und nur Ziffern und Kleinbuchstaben enthalten darf. Sie erstellen ein Speicherkonto mit dem Befehl [az storage account create](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create). In diesem Beispiel wird eine zufällige Zeichenfolge verwendet, um einen eindeutigen Speicherkontonamen zu erstellen.

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

Aktivieren Sie nun die Startdiagnose mit [az vm boot-diagnostics enable](/cli/azure/vm/boot-diagnostics#az-vm-boot-diagnostics-enable). Der `--storage`-Wert ist der Blob-URI, der im vorherigen Schritt erfasst wurde.

```azurecli-interactive
az vm boot-diagnostics enable \
  --resource-group myResourceGroupMonitor \
  --name myVM \
  --storage $bloburi
```

## <a name="view-boot-diagnostics"></a>Anzeigen der Startdiagnose

Wenn die Startdiagnose aktiviert ist, werden bei jedem Beenden und Starten des virtuellen Computers Informationen zum Startvorgang in eine Protokolldatei geschrieben. Heben Sie in diesem Beispiel zunächst die Zuordnung des virtuellen Computers mit dem Befehl [az vm deallocate](/cli/azure/vm?view=azure-cli-latest#az-vm-deallocate) wie folgt auf:

```azurecli-interactive
az vm deallocate --resource-group myResourceGroupMonitor --name myVM
```

Starten Sie nun den virtuellen Computer mit dem Befehl [az vm start](/cli/azure/vm?view=azure-cli-latest#az-vm-start) wie folgt:

```azurecli-interactive
az vm start --resource-group myResourceGroupMonitor --name myVM
```

Sie erhalten die Startdiagnosedaten für *myVM* mithilfe des Befehls [az vm boot-diagnostics get-boot-log](/cli/azure/vm/boot-diagnostics#az-vm-boot-diagnostics-get-boot-log) wie folgt:

```azurecli-interactive
az vm boot-diagnostics get-boot-log --resource-group myResourceGroupMonitor --name myVM
```

## <a name="view-host-metrics"></a>Anzeigen von Hostmetriken

Eine Linux-VM verfügt über einen dedizierten Host in Azure, mit dem sie interagiert. Es werden automatisch Metriken für den Host gesammelt, die folgendermaßen im Azure-Portal angezeigt werden können:

1. Klicken Sie im Azure-Portal auf **Ressourcengruppen**, und wählen Sie **myResourceGroupMonitor** und dann in der Ressourcenliste **myVM** aus.
1. Um die Leistung des virtuellen Hostcomputers anzuzeigen, klicken Sie im Fenster des virtuellen Computers auf **Metriken**, und wählen Sie dann eine der *[Host]* -Metriken unter **Verfügbare Metriken** aus.

    ![Anzeigen von Hostmetriken](./media/tutorial-monitoring/monitor-host-metrics.png)

## <a name="enable-advanced-monitoring"></a>Aktivieren der erweiterten Überwachung

So aktivieren Sie die Überwachung Ihrer virtuellen Azure-Computer mit Azure Monitor für VMs:

1. Klicken Sie im Azure-Portal auf **Ressourcengruppen**, und wählen Sie **myResourceGroupMonitor** und dann in der Ressourcenliste **myVM** aus.

2. Wählen Sie auf der Seite der VM im Abschnitt **Überwachung** den Eintrag **Insights (Vorschau)** aus.

3. Wählen Sie auf der Seite **Insights (Vorschau)** **Jetzt testen** aus.

    ![Aktivieren von Azure Monitor for VMs für eine VM](../../azure-monitor/insights/media/vminsights-enable-single-vm/enable-vminsights-vm-portal.png)

4. Wenn Sie im selben Abonnement einen Log Analytics-Arbeitsbereich haben, wählen Sie ihn auf der Seite **Azure Monitor Insights Onboarding** (Onboarding von Azure Monitor Insights) in der Dropdownliste aus.  

    In der Liste sind Standardarbeitsbereich und Speicherort, in dem die VM im Abonnement bereitgestellt wird, vorab ausgewählt. 

    >[!NOTE]
    >Informationen zum Erstellen eines neuen Log Analytics-Arbeitsbereichs für das Speichern der Überwachungsdaten aus der VM finden Sie unter [Erstellen eines Log Analytics-Arbeitsbereichs](../../azure-monitor/learn/quick-create-workspace.md). Der Arbeitsbereich muss einer der [unterstützten Regionen](../../azure-monitor/insights/vminsights-configure-workspace.md#supported-regions) angehören.

Nachdem Sie die Überwachung aktiviert haben, müssen Sie möglicherweise etwa einige Minuten warten, bis die Leistungsmetriken für die VM angezeigt werden.

![Aktivieren von Azure Monitor for VMs – Verarbeiten der Überwachungsbereitstellung](../../azure-monitor/insights/media/vminsights-enable-single-vm/onboard-vminsights-vm-portal-status.png)

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
> [Verwalten der VM-Sicherheit](../tutorial-azure-security.md)