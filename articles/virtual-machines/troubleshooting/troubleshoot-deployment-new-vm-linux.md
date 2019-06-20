---
title: Behandeln von Problemen beim Bereitstellen virtueller Linux-Computer | Microsoft-Dokumentation
description: Behandeln von Ressourcen-Manager-Bereitstellungsproblemen beim Erstellen eines neuen virtuellen Linux-Computers in Azure
services: virtual-machines-linux, azure-resource-manager
documentationcenter: ''
author: JiangChen79
manager: jeconnoc
editor: ''
tags: top-support-issue, azure-resource-manager
ms.assetid: 906a9c89-6866-496b-b4a4-f07fb39f990c
ms.service: virtual-machines-linux
ms.workload: na
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/09/2016
ms.author: cjiang
ms.openlocfilehash: 9fea914fdf9b025fd5d38219a6bfc81b4a9cc584
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62125615"
---
# <a name="troubleshoot-resource-manager-deployment-issues-with-creating-a-new-linux-virtual-machine-in-azure"></a>Behandeln von Ressourcen-Manager-Bereitstellungsproblemen beim Erstellen eines neuen virtuellen Linux-Computers in Azure
[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-opening](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-opening-include.md)]

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="top-issues"></a>Wichtigste Probleme
[!INCLUDE [support-disclaimer](../../../includes/virtual-machines-linux-troubleshoot-deploy-vm-top.md)]

Weitere VM-Bereitstellungsprobleme und Fragen finden Sie unter [Troubleshoot deploying Linux virtual machine issues in Azure (Behandlung von Problemen bei der Bereitstellung von virtuellen Linux-Computern)](troubleshoot-deploy-vm-linux.md).

## <a name="collect-activity-logs"></a>Erfassen von Aktivitätsprotokollen
Sammeln Sie zur Problembehandlung zunächst die Aktivitätsprotokolle, um den Fehler zu ermitteln, auf den das Problem zurückzuführen ist. Die folgenden Links enthalten detaillierte Informationen zur Vorgehensweise.

[Anzeigen von Bereitstellungsvorgängen](../../azure-resource-manager/resource-manager-deployment-operations.md)

[Anzeigen von Aktivitätsprotokollen zum Verwalten von Azure-Ressourcen](../../resource-group-audit.md)

[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-issue1](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-issue1-include.md)]

[!INCLUDE [virtual-machines-linux-troubleshoot-deployment-new-vm-table](../../../includes/virtual-machines-linux-troubleshoot-deployment-new-vm-table.md)]

**J:** Bei einem generalisierten Linux-Betriebssystem, das mit der Generalisierungseinstellung hochgeladen und/oder erfasst wird, treten keine Fehler auf. Analog dazu gilt: Bei einem spezialisierten Linux-Betriebssystem, das mit der Spezialisierungseinstellung hochgeladen und/oder erfasst wird, treten keine Fehler auf.

**Uploadfehler:**

**N<sup>1</sup>:** Wenn ein generalisiertes Linux-Betriebssystem als spezialisiertes Betriebssystem hochgeladen wird, tritt bei der Bereitstellung ein Timeoutfehler auf, da der virtuelle Computer nicht über die Bereitstellungsphase hinauskommt.

**N<sup>2</sup>:** Wenn ein spezialisiertes Linux-Betriebssystem als generalisiertes Betriebssystem hochgeladen wird, tritt ein Bereitstellungsfehler auf, da der neue virtuelle Computer mit dem ursprünglichen Computernamen, Benutzernamen und Kennwort ausgeführt wird.

**Lösung:**

Laden Sie zur Behebung dieser Fehler die ursprüngliche (lokal verfügbare) virtuelle Festplatte mit der gleichen Einstellung (generalisiert/spezialisiert) hoch, die auch für das Betriebssystem verwendet wird. Beim Hochladen als generalisiertes Image muss zuerst "-deprovision" ausgeführt werden.

**Erfassungsfehler:**

**N<sup>3</sup>:** Wenn ein generalisiertes Linux-Betriebssystem als spezialisiertes Betriebssystem erfasst wird, tritt bei der Bereitstellung ein Timeoutfehler auf, da der ursprüngliche virtuelle Computer als generalisiert gekennzeichnet und somit nicht verwendbar ist.

**N<sup>4</sup>:** Wenn ein spezialisiertes Linux-Betriebssystem als generalisiertes Betriebssystem erfasst wird, tritt ein Bereitstellungsfehler auf, da der neue virtuelle Computer mit dem ursprünglichen Computernamen, Benutzernamen und Kennwort ausgeführt wird. Darüber hinaus ist der ursprüngliche virtuelle Computer als spezialisiert gekennzeichnet und somit nicht verwendbar.

**Lösung:**

Löschen Sie zur Behebung dieser Fehler das aktuelle Image über das Portal, und [erfassen Sie es auf der Grundlage der aktuellen VHDs erneut](../linux/capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Verwenden Sie dabei die gleiche Einstellung (generalisiert/spezialisiert), die auch für das Betriebssystem verwendet wird.

## <a name="issue-custom-gallery-marketplace-image-allocation-failure"></a>Problem: Benutzerdefiniertes Image/Katalogimage/Marketplace-Image; Zuordnungsfehler
Dieser Fehler tritt auf, wenn die Anforderung für einen neuen virtuellen Computer an einen Cluster gesendet wird, der entweder nicht über genügend Speicherplatz verfügt oder die angeforderte VM-Größe nicht unterstützt.

**Ursache 1:** Der Cluster unterstützt nicht die angeforderte Größe des virtuellen Computers.

**Lösung 1:**

* Wiederholen Sie die Anforderung mit einer geringeren Größe des virtuellen Computers.
* Wenn die Größe des angeforderten virtuellen Computers nicht geändert werden kann:
  * Beenden Sie alle virtuellen Computer in der Verfügbarkeitsgruppe.
    Klicken Sie auf **Ressourcengruppen** > *Ihre Ressourcengruppe* > **Ressourcen** > *Ihre Verfügbarkeitsgruppe* > **Virtuelle Computer** > *Ihr virtueller Computer* > **Beenden**.
  * Nachdem alle virtuellen Computer beendet wurden, erstellen Sie den neuen virtuellen Computer in der gewünschten Größe.
  * Starten Sie zunächst den neuen virtuellen Computer, wählen Sie dann alle angehaltenen virtuellen Computer aus, und klicken Sie auf **Starten**.

**Ursache 2:** Der Cluster verfügt über keine freien Ressourcen.

**Lösung 2:**

* Wiederholen Sie die Anforderung zu einem späteren Zeitpunkt.
* Wenn der neue virtuelle Computer Teil einer anderen Verfügbarkeitsgruppe sein kann
  * Erstellen Sie einen neuen virtuellen Computer in einer anderen Verfügbarkeitsgruppe (in derselben Region).
  * Fügen Sie den neuen virtuellen Computer zum gleichen virtuellen Netzwerk hinzu.

## <a name="next-steps"></a>Nächste Schritte
Wenn beim Starten eines beendeten virtuellen Linux-Computers oder beim Ändern der Größe eines vorhandenen virtuellen Linux-Computers Probleme in Azure auftreten, finden Sie Informationen unter [Problembehandlung von Resource Manager-Bereitstellungsproblemen beim Neustart oder Ändern der Größe eines vorhandenen virtuellen Linux-Computers in Azure](../linux/restart-resize-error-troubleshooting.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

