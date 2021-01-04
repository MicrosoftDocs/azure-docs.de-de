---
title: Erweiterungen und Features für virtuelle Azure-Computer
description: Informationen zu Erweiterungen für virtuelle Azure-Computer
services: virtual-machines
author: amjads1
ms.service: virtual-machines
ms.subservice: extensions
ms.topic: article
ms.workload: infrastructure-services
ms.date: 08/03/2020
ms.author: amjads
ms.openlocfilehash: b1dd26fce2e0a761ceed211933cb79ce518905e2
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94965883"
---
# <a name="azure-virtual-machine-extensions-and-features"></a>Erweiterungen und Features für virtuelle Azure-Computer
Erweiterungen sind kleine Anwendungen, die Konfiguration und Automatisierung für virtuelle Azure-Computer nach der Bereitstellung bereitstellen. Auf der Azure-Plattform werden Erweiterungen für viele Zwecke gehostet, z. B. VM-Konfiguration, Überwachung, Sicherheit und Hilfsprogramme. Herausgeber nehmen eine Anwendung, umschließen diese in einer Erweiterung und vereinfachen die Installation. Sie müssen lediglich noch die obligatorischen Parameter angeben. 

## <a name="how-can-i-find-what-extensions-are-available"></a>Wie kann ich ermitteln, welche Erweiterungen verfügbar sind?
Sie können die verfügbaren Erweiterungen anzeigen, indem Sie einen virtuellen Computer auswählen, indem Sie im linken Menü **Erweiterungen** auswählen. Informationen zum Abrufen einer vollständigen Liste der Erweiterungen finden Sie unter [Ermitteln von VM-Erweiterungen für Linux](features-linux.md) und [Ermitteln von VM-Erweiterungen für Windows](features-windows.md).

## <a name="how-can-i-install-an-extension"></a>Wie kann ich eine Erweiterung installieren?
Azure VM-Erweiterungen können mithilfe der Azure-Befehlszeilenoberfläche, PowerShell, Resource Manager-Vorlagen und dem Azure-Portal verwaltet werden. Zum Ausprobieren einer Erweiterung wählen Sie im Azure-Portal die Erweiterung „Benutzerdefiniertes Skript“ aus, und übergeben Sie dann einen Befehl bzw. ein Skript als Eingabe, um die Erweiterung auszuführen.

Weitere Informationen finden Sie unter [Benutzerdefinierte Skripterweiterung für Windows](custom-script-windows.md) und [Benutzerdefinierte Skripterweiterung für Linux](custom-script-linux.md).

## <a name="how-do-i-manage-extension-application-lifecycle"></a>Wie kann ich den Anwendungslebenszyklus einer Erweiterung verwalten?
Sie müssen keine direkte Verbindung mit einer VM herstellen, um eine Erweiterung zu installieren oder zu löschen. Der Lebenszyklus von Azure-Erweiterungen außerhalb der VM verwaltet und ist in die Azure-Plattform integriert.

## <a name="anything-else-i-should-be-thinking-about-for-extensions"></a>Was muss in Bezug auf Erweiterungen sonst noch bedacht werden?
Einige einzelne VM-Erweiterungsanwendungen verfügen ggf. über eigene Umgebungsvoraussetzungen, z.B. den Zugriff auf einen Endpunkt. Zu jeder Erweiterung gibt es einen Artikel, in dem alle Voraussetzungen erläutert werden, einschließlich der unterstützten Betriebssysteme.

## <a name="troubleshoot-extensions"></a>Problembehandlung bei Erweiterungen

Informationen zur Problembehandlung für jede Erweiterung finden Sie im Abschnitt **Problembehandlung und Support** in der Übersicht für die jeweilige Erweiterung. Es folgt eine Liste der verfügbaren Problembehandlungsinformatonen:

| Namespace | Problembehandlung |
|-----------|-----------------|
| microsoft.azure.monitoring.dependencyagent.dependencyagentlinux | [Azure Monitor Dependency-VM-Erweiterung für Linux](agent-dependency-linux.md#troubleshoot-and-support) |
| microsoft.azure.monitoring.dependencyagent.dependencyagentwindows | [Azure Monitor Dependency-VM-Erweiterung für Windows](agent-dependency-windows.md#troubleshoot-and-support) |
| microsoft.azure.security.azurediskencryptionforlinux | [Azure Disk Encryption für Linux](azure-disk-enc-linux.md#troubleshoot-and-support) |
| microsoft.azure.security.azurediskencryption | [Azure Disk Encryption für Windows](azure-disk-enc-windows.md#troubleshoot-and-support) |
| microsoft.compute.customscriptextension | [CustomScript-Erweiterung für Windows](custom-script-windows.md#troubleshoot-and-support) |
| microsoft.ostcextensions.customscriptforlinux | [DSC-Erweiterung für Linux](dsc-linux.md#troubleshoot-and-support) |
| microsoft.powershell.dsc | [DSC-Erweiterung für Windows](dsc-windows.md#troubleshoot-and-support) |
| microsoft.hpccompute.nvidiagpudriverlinux | [NVIDIA-GPU-Treibererweiterung für Linux](hpccompute-gpu-linux.md#troubleshoot-and-support) |
| microsoft.hpccompute.nvidiagpudriverwindows | [NVIDIA-GPU-Treibererweiterung für Windows](hpccompute-gpu-windows.md#troubleshoot-and-support) |
| microsoft.azure.security.iaasantimalware | [Microsoft Antimalware-Erweiterung für Windows](iaas-antimalware-windows.md#troubleshoot-and-support) |
| microsoft.enterprisecloud.monitoring.omsagentforlinux | [Azure Monitor für Linux](oms-linux.md#troubleshoot-and-support)
| microsoft.enterprisecloud.monitoring.microsoftmonitoringagent | [Azure Monitor für Windows](oms-windows.md#troubleshoot-and-support) |
| stackify.linuxagent.extension.stackifylinuxagentextension | [Stackify Retrace-Agent-Erweiterung für Linux](stackify-retrace-linux.md#troubleshoot-and-support) |
| vmaccessforlinux.microsoft.ostcextensions | [Zurücksetzen des Kennworts für Linux](vmaccess.md#troubleshoot-and-support) |
| microsoft.recoveryservices.vmsnapshot | [Momentaufnahme für Linux](vmsnapshot-linux.md#troubleshoot-and-support) |
| microsoft.recoveryservices.vmsnapshot | [Momentaufnahme für Windows](vmsnapshot-windows.md#troubleshoot-and-support) |


## <a name="next-steps"></a>Nächste Schritte
* Weitere Informationen zur Funktionsweise des Linux-Agents und der Erweiterungen finden Sie unter [Informationen zu Erweiterungen und Features für virtuelle Computer für Linux](features-linux.md).
* Weitere Informationen zur Funktionsweise des Windows-Gast-Agents und der Erweiterungen finden Sie unter [Azure VM-Erweiterungen und -Features für Windows](features-windows.md).  
* Informationen zur Installation des Windows-Gast-Agents finden Sie unter [Azure Virtual Machine Agent overview](agent-windows.md) (Übersicht über den Agent für virtuelle Azure-Computer).  
* Informationen zur Installation des Linux-Agents finden Sie unter [Understanding and using the Azure Linux Agent](agent-linux.md) (Grundlegendes zum Azure Linux-Agent und seiner Verwendung).  

