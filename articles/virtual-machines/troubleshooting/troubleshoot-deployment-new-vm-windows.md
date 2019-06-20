---
title: Behandeln von Problemen beim Bereitstellen virtueller Windows-Computer in Azure | Microsoft Docs
description: Behandeln von Problemen beim Erstellen eines neuen virtuellen Windows-Computers in Azure (Resource Manager-Bereitstellungsmodell)
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: JiangChen79
manager: jeconnoc
editor: ''
tags: top-support-issue, azure-resource-manager
ms.assetid: afc6c1a4-2769-41f6-bbf9-76f9f23bcdf4
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: cjiang
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8f18fbabe52c9170cde70900933ce96a3a6400c7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62123840"
---
# <a name="troubleshoot-deployment-issues-when-creating-a-new-windows-vm-in-azure"></a>Behandeln von Bereitstellungsproblemen beim Erstellen eines neuen virtuellen Windows-Computers in Azure
[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-opening](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-opening-include.md)]

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="top-issues"></a>Häufigste Probleme
[!INCLUDE [support-disclaimer](../../../includes/virtual-machines-windows-troubleshoot-deploy-vm-top.md)]

Weitere VM-Bereitstellungsprobleme und Fragen finden Sie unter [Problembehandlung Bereitstellen von Windows virtuellen Computer in Azure](troubleshoot-deploy-vm-windows.md).

## <a name="collect-activity-logs"></a>Erfassen von Aktivitätsprotokollen
Sammeln Sie zur Problembehandlung zunächst die Aktivitätsprotokolle, um den Fehler zu ermitteln, auf den das Problem zurückzuführen ist. Die folgenden Links enthalten detaillierte Informationen zur Vorgehensweise.

[Anzeigen von Bereitstellungsvorgängen](../../azure-resource-manager/resource-manager-deployment-operations.md)

[Anzeigen von Aktivitätsprotokollen zum Verwalten von Azure-Ressourcen](../../resource-group-audit.md)

[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-issue1](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-issue1-include.md)]

[!INCLUDE [virtual-machines-windows-troubleshoot-deployment-new-vm-table](../../../includes/virtual-machines-windows-troubleshoot-deployment-new-vm-table.md)]

**J:** Bei einem generalisierten Windows-Betriebssystem, das mit der Generalisierungseinstellung hochgeladen und/oder erfasst wird, treten keine Fehler auf. Analog dazu gilt: Bei einem spezialisierten Windows-Betriebssystem, das mit der Spezialisierungseinstellung hochgeladen und/oder erfasst wird, treten keine Fehler auf.

**Uploadfehler:**

**N<sup>1</sup>:** Wenn ein generalisiertes Windows-Betriebssystem als spezialisiertes Betriebssystem hochgeladen wird, tritt bei der Bereitstellung ein Timeoutfehler auf, und der virtuelle Computer kommt nicht über den OOBE-Bildschirm hinaus.

**N<sup>2</sup>:** Wenn ein spezialisiertes Windows-Betriebssystem als generalisiertes Betriebssystem hochgeladen wird, tritt ein Bereitstellungsfehler auf, und der virtuelle Computer kommt nicht über den OOBE-Bildschirm hinaus, da der neue virtuelle Computer mit dem ursprünglichen Computernamen, Benutzernamen und Kennwort ausgeführt wird.

**Lösung**

Laden Sie zur Behebung dieser Fehler mithilfe von [Add-AzVhd](https://docs.microsoft.com/powershell/module/az.compute/add-azvhd)die ursprüngliche (lokal verfügbare) virtuelle Festplatte mit der gleichen Einstellung (generalisiert/spezialisiert) hoch, die auch für das Betriebssystem verwendet wird. Beim Hochladen als generalisiertes Image muss zuerst „sysprep“ ausgeführt werden.

**Erfassungsfehler:**

**N<sup>3</sup>:** Wenn ein generalisiertes Windows-Betriebssystem als spezialisiertes Betriebssystem erfasst wird, tritt bei der Bereitstellung ein Timeoutfehler auf, da der ursprüngliche virtuelle Computer als generalisiert gekennzeichnet und somit nicht verwendbar ist.

**N<sup>4</sup>:** Wenn ein spezialisiertes Windows-Betriebssystem als generalisiertes Betriebssystem erfasst wird, tritt ein Bereitstellungsfehler auf, da der neue virtuelle Computer mit dem ursprünglichen Computernamen, Benutzernamen und Kennwort ausgeführt wird. Darüber hinaus ist der ursprüngliche virtuelle Computer als spezialisiert gekennzeichnet und somit nicht verwendbar.

**Lösung**

Löschen Sie zur Behebung dieser Fehler das aktuelle Image über das Portal, und [erfassen Sie es auf der Grundlage der aktuellen VHDs erneut](../windows/create-vm-specialized.md). Verwenden Sie dabei die gleiche Einstellung (generalisiert/spezialisiert), die auch für das Betriebssystem verwendet wird.

## <a name="issue-customgallerymarketplace-image-allocation-failure"></a>Problem: Benutzerdefiniertes Image/Katalogimage/Marketplace-Image; Zuordnungsfehler
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
Wenn beim Starten eines beendeten virtuellen Windows-Computers oder beim Ändern der Größe eines vorhandenen virtuellen Windows-Computers in Azure Probleme auftreten, finden Sie Informationen unter [Behandeln von Problemen beim Neustart oder Ändern der Größe eines vorhandenen virtuellen Windows-Computers in Azure (Resource Manager-Bereitstellungsmodell)](restart-resize-error-troubleshooting.md).


