---
title: Bereitstellen einer Anwendung in einer Azure-VM-Skalierungsgruppe | Microsoft-Dokumentation
description: Es wird beschrieben, wie Sie Anwendungen auf Instanzen von virtuellen Linux- und Windows-Computern in einer Skalierungsgruppe bereitstellen.
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: f8892199-f2e2-4b82-988a-28ca8a7fd1eb
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/29/2018
ms.author: cynthn
ms.openlocfilehash: 09145612821cb669e26e3ccb8d15611112eca700
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60618624"
---
# <a name="deploy-your-application-on-virtual-machine-scale-sets"></a>Bereitstellen der App in VM-Skalierungsgruppen

Zum Ausführen von Anwendungen auf VM-Instanzen in einer Skalierungsgruppe müssen Sie zuerst die Anwendungskomponenten und erforderlichen Dateien installieren. In diesem Artikel werden Möglichkeiten zum Erstellen eines benutzerdefinierten VM-Image für Instanzen in einer Skalierungsgruppe oder zum automatischen Ausführen von Installationsskripts auf vorhandenen VM-Instanzen aufgezeigt. Außerdem erfahren Sie, wie Sie Anwendungs- oder Betriebssystemupdates für eine Skalierungsgruppe verwalten.


## <a name="build-a-custom-vm-image"></a>Erstellen eines benutzerdefinierten VM-Image
Wenn Sie eines der Azure-Plattformimages zum Erstellen der Instanzen in Ihrer Skalierungsgruppe erstellen, wird keine zusätzliche Software installiert oder konfiguriert. Sie können die Installation dieser Komponenten automatisieren, aber dies erhöht den Zeitaufwand für die Bereitstellung von VM-Instanzen in Ihren Skalierungsgruppen. Wenn Sie viele Konfigurationsänderungen auf die VM-Instanzen anwenden, fällt Verwaltungsaufwand für diese Konfigurationsskripts und -aufgaben an.

Zum Reduzieren der Konfigurationsverwaltung und der Zeit für die Bereitstellung einer VM können Sie ein benutzerdefiniertes VM-Image erstellen, das für die Ausführung Ihrer Anwendung bereitsteht, sobald in der Skalierungsgruppe eine Instanz bereitgestellt wird. Weitere Informationen zur Erstellung und Verwendung eines benutzerdefinierten VM-Images mit einer Skalierungsgruppe finden Sie in den folgenden Tutorials:

- [Azure-Befehlszeilenschnittstelle](tutorial-use-custom-image-cli.md)
- [Azure PowerShell](tutorial-use-custom-image-powershell.md)


## <a name="already-provisioned"></a>Installieren einer App mit der benutzerdefinierten Skripterweiterung
Die benutzerdefinierte Skripterweiterung lädt Skripts auf Azure-VMs herunter und führt sie aus. Diese Erweiterung ist hilfreich bei der Konfiguration nach der Bereitstellung, bei der Softwareinstallation oder bei anderen Konfigurations-/Verwaltungsaufgaben. Skripts können aus Azure Storage oder GitHub heruntergeladen oder dem Azure-Portal zur Laufzeit für die Erweiterung bereitgestellt werden. Weitere Informationen zur Erstellung und Verwendung eines benutzerdefinierten VM-Images mit einer Skalierungsgruppe finden Sie in den folgenden Tutorials:

- [Azure-Befehlszeilenschnittstelle](tutorial-install-apps-cli.md)
- [Azure PowerShell](tutorial-install-apps-powershell.md)
- [Azure Resource Manager-Vorlage](tutorial-install-apps-template.md)


## <a name="install-an-app-to-a-windows-vm-with-powershell-dsc"></a>Installieren einer App auf einer Windows-VM mit PowerShell DSC
[PowerShell DSC (Desired State Configuration)](https://msdn.microsoft.com/powershell/dsc/overview) ist eine Verwaltungsplattform zum Definieren der Konfiguration von Zielcomputern. DSC-Konfigurationen definieren, was auf einem Computer installiert und wie der Host konfiguriert werden soll. Eine LCM-Engine (Local Configuration Manager) wird auf jedem Zielknoten ausgeführt, der angeforderte Aktionen auf der Grundlage gepushter Konfigurationen verarbeitet.

Mit der Erweiterung PowerShell DSC können Sie VM-Instanzen in einer Skalierungsgruppe mit PowerShell anpassen. Das folgende Beispiel:

- Weist die VM-Instanzen an, ein DSC-Paket von GitHub herunterzuladen: *https://github.com/Azure-Samples/compute-automation-configurations/raw/master/dsc.zip*
- Die Erweiterung wird so festgelegt, dass ein Installationsskript ausgeführt wird: `configure-http.ps1`
- Mit [Get-AzVmss](/powershell/module/az.compute/get-azvmss) werden Informationen zu einer Skalierungsgruppe abgerufen.
- Mit [Update-AzVmss](/powershell/module/az.compute/update-azvmss) wird die Erweiterung auf die VM-Instanzen angewandt.

Die DSC-Erweiterung wird auf die *myScaleSet*-VM-Instanzen in der Ressourcengruppe mit dem Namen *myResourceGroup* angewendet. Geben Sie Ihre eigenen Namen wie folgt ein:

```powershell
# Define the script for your Desired Configuration to download and run
$dscConfig = @{
  "wmfVersion" = "latest";
  "configuration" = @{
    "url" = "https://github.com/Azure-Samples/compute-automation-configurations/raw/master/dsc.zip";
    "script" = "configure-http.ps1";
    "function" = "WebsiteTest";
  };
}

# Get information about the scale set
$vmss = Get-AzVmss `
                -ResourceGroupName "myResourceGroup" `
                -VMScaleSetName "myScaleSet"

# Add the Desired State Configuration extension to install IIS and configure basic website
$vmss = Add-AzVmssExtension `
    -VirtualMachineScaleSet $vmss `
    -Publisher Microsoft.Powershell `
    -Type DSC `
    -TypeHandlerVersion 2.24 `
    -Name "DSC" `
    -Setting $dscConfig

# Update the scale set and apply the Desired State Configuration extension to the VM instances
Update-AzVmss `
    -ResourceGroupName "myResourceGroup" `
    -Name "myScaleSet"  `
    -VirtualMachineScaleSet $vmss
```

Wenn als Upgraderichtlinie für Ihre Skalierungsgruppe *manuell* festgelegt ist, sollten Sie Ihre VM-Instanzen mit [Update-AzVmssInstance](/powershell/module/az.compute/update-azvmssinstance) aktualisieren. Mit diesem Cmdlet wird die aktualisierte Konfiguration für die Skalierungsgruppe auf die VM-Instanzen angewendet und Ihre Anwendung installiert.


## <a name="install-an-app-to-a-linux-vm-with-cloud-init"></a>Installieren einer App auf einer Linux-VM mit „cloud-init“
[Cloud-init](https://cloudinit.readthedocs.io/en/latest/index.html) ist ein weit verbreiteter Ansatz zum Anpassen einer Linux-VM beim ersten Start. Sie können mit cloud-init Pakete installieren und Dateien schreiben oder Benutzer und Sicherheit konfigurieren. Da cloud-init während des ersten Startvorgangs ausgeführt wird, müssen Sie keine zusätzlichen Schritte oder erforderlichen Agents auf Ihre Konfiguration anwenden.

Cloud-init funktioniert auch Distributionen übergreifend. Verwenden Sie z.B. nicht **apt-get install** oder **yum install**, um ein Paket zu installieren. Stattdessen können Sie eine Liste mit zu installierenden Paketen definieren. „cloud-init“ verwendet automatisch das native Paketverwaltungstool für die ausgewählte Distribution.

Weitere Informationen sowie die Beispieldatei *cloud-init.txt* finden Sie unter [Anpassen einer Linux-VM in Azure mit cloud-init](../virtual-machines/linux/using-cloud-init.md).

Fügen Sie zum Erstellen einer Skalierungsgruppe und zum Verwenden einer cloud-init-Datei dem Befehl [az vmss create](/cli/azure/vmss) den Parameter `--custom-data` hinzu, und geben Sie den Namen einer cloud-init-Datei an. Im folgenden Beispiel wird eine Skalierungsgruppe mit dem Namen *myScaleSet* in *myResourceGroup* erstellt, und VM-Instanzen werden mit einer Datei mit dem Namen *cloud-init.txt* konfiguriert. Geben Sie Ihre eigenen Namen wie folgt ein:

```azurecli
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --custom-data cloud-init.txt \
  --admin-username azureuser \
  --generate-ssh-keys
```


### <a name="install-applications-with-os-updates"></a>Installieren von Anwendungen mit Betriebssystemupdates
Wenn neue Releases von Betriebssystemen verfügbar sind, können Sie ein neues benutzerdefiniertes Image verwenden oder erstellen und [Betriebssystemupgrades](virtual-machine-scale-sets-upgrade-scale-set.md) für eine Skalierungsgruppe bereitstellen. Jede VM-Instanz wird auf das aktuelle Image aktualisiert, das Sie angegeben haben. Sie können ein benutzerdefiniertes Image mit der vorinstallierten Anwendung, der benutzerdefinierten Skripterweiterung oder PowerShell DSC verwenden, damit die Anwendung automatisch verfügbar ist, wenn Sie das Upgrade durchführen. Unter Umständen müssen Sie die Anwendungswartung planen, wenn Sie diesen Prozess durchführen, um sicherzustellen, dass keine Probleme aufgrund der Kompatibilität von Versionen auftreten.

Wenn Sie ein benutzerdefiniertes VM-Image mit vorinstallierter Anwendung verwenden, können Sie die Anwendungsupdates in eine Bereitstellungspipeline integrieren, um die neuen Images zu erstellen und Betriebssystemupgrades für die gesamte Skalierungsgruppe bereitzustellen. Bei diesem Ansatz kann die Pipeline die aktuellen Builds der Anwendung nutzen, ein VM-Image erstellen und überprüfen und anschließend die VM-Instanzen in der Skalierungsgruppe aktualisieren. Zum Ausführen einer Bereitstellungspipeline, mit der Anwendungsupdates über benutzerdefinierte VM-Images hinweg erstellt und bereitgestellt werden, können Sie [mit Azure DevOps Services ein Packer-Image erstellen und bereitstellen](/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset) oder eine andere Plattform (beispielsweise [Spinnaker](https://www.spinnaker.io/) oder [Jenkins](https://jenkins.io/)) verwenden.


## <a name="next-steps"></a>Nächste Schritte
Beim Erstellen und Bereitstellen von Anwendungen in Ihren Skalierungsgruppen helfen Ihnen die Informationen unter [Überlegungen zum Entwurf von Skalierungsgruppen](virtual-machine-scale-sets-design-overview.md) weiter. Weitere Informationen zum Verwalten Ihrer Skalierungsgruppe finden Sie unter [Manage a virtual machine scale set with Azure PowerShell](virtual-machine-scale-sets-windows-manage.md) (Verwalten einer VM-Skalierungsgruppe mit Azure PowerShell).
