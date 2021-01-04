---
title: Probleme beim Neustart oder Ändern der Größe eines virtuellen Computers in Azure | Microsoft Docs
description: Beheben von Resource Manager-Bereitstellungsproblemen beim Neustart oder Ändern der Größe eines vorhandenen virtuellen Computers in Azure
services: virtual-machines
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: top-support-issue
ms.assetid: 0756b52d-4f5a-4503-ae45-c00a6a2edcdf
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: delhan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 85acd8e26ca10730638332047a37d281358d205f
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96022886"
---
# <a name="troubleshoot-deployment-issues-with-restarting-or-resizing-an-existing-windows-vm-in-azure"></a>Behandeln von Bereitstellungsproblemen beim Neustart oder Ändern der Größe eines vorhandenen virtuellen Windows-Computers in Azure
Wenn Sie versuchen, einen beendeten virtuellen Azure-Computer zu starten oder die Größe eines vorhandenen virtuellen Azure-Computers zu ändern, tritt häufig ein Zuordnungsfehler auf. Dieser Fehler tritt auf, wenn in dem Cluster oder der Region keine Ressourcen verfügbar sind, oder wenn die angeforderte Größe des virtuellen Computers nicht unterstützt werden kann.

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="collect-activity-logs"></a>Erfassen von Aktivitätsprotokollen
Sammeln Sie zur Problembehandlung zunächst die Aktivitätsprotokolle, um den Fehler zu ermitteln, auf den das Problem zurückzuführen ist. Die folgenden Links enthalten detaillierte Informationen zur Vorgehensweise:

[Bereitstellungsvorgänge anzeigen](../../azure-resource-manager/templates/deployment-history.md)

[Anzeigen von Aktivitätsprotokollen zum Verwalten von Azure-Ressourcen](../../azure-resource-manager/management/view-activity-logs.md)

## <a name="issue-error-when-starting-a-stopped-vm"></a>Problem: Fehler beim Starten eines angehaltenen virtuellen Computers
Sie versuchen, einen angehaltenen virtuellen Computer zu starten, erhalten aber eine Zuordnungsfehlermeldung.

### <a name="cause"></a>Ursache
Die Anforderung zum Start des angehaltenen virtuellen Computers muss im Originalcluster, der den Clouddienst hostet, ausgeführt werden. Im Cluster ist jedoch nicht genügend freier Speicherplatz verfügbar, um die Anforderung zu erfüllen.

### <a name="resolution"></a>Lösung
* Beenden Sie alle virtuellen Computer in der Verfügbarkeitsgruppe, und starten Sie dann jeden virtuellen Computer neu.
  
  1. Klicken Sie auf **Ressourcengruppen** > *Ihre Ressourcengruppe* > **Ressourcen** > *Ihre Verfügbarkeitsgruppe* > **Virtuelle Computer** > *Ihr virtueller Computer* > **Beenden**.
  2. Nachdem alle virtuellen Computer angehalten wurden, wählen Sie jeden einzelnen angehaltenen virtuellen Computer aus, und klicken Sie auf "Start".
* Wiederholen Sie die Neustartanforderung zu einem späteren Zeitpunkt.

## <a name="issue-error-when-resizing-an-existing-vm"></a>Problem: Fehler beim Ändern der Größe eines vorhandenen virtuellen Computers
Sie versuchen, die Größe eines angehaltenen virtuellen Computers zu ändern, erhalten aber eine Zuordnungsfehlermeldung.

### <a name="cause"></a>Ursache
Die Anforderung zur Größenänderung des virtuellen Computers muss im Originalcluster, der den Clouddienst hostet, ausgeführt werden. Der Cluster unterstützt jedoch nicht die angeforderte Größe des virtuellen Computers.

### <a name="resolution"></a>Lösung
* Wiederholen Sie die Anforderung mit einer geringeren Größe des virtuellen Computers.
* Wenn die Größe des angeforderten virtuellen Computers nicht geändert werden kann:
  
  1. Beenden Sie alle virtuellen Computer in der Verfügbarkeitsgruppe.
     
     * Klicken Sie auf **Ressourcengruppen** > *Ihre Ressourcengruppe* > **Ressourcen** > *Ihre Verfügbarkeitsgruppe* > **Virtuelle Computer** > *Ihr virtueller Computer* > **Beenden**.
  2. Nachdem alle virtuellen Computer beendet wurden, vergrößern Sie den gewünschten virtuellen Computer.
  3. Wählen Sie den virtuellen Computer mit der geänderten Größe aus, und klicken Sie auf **Starten**. Starten Sie dann jeden einzelnen beendeten virtuellen Computer.

## <a name="next-steps"></a>Nächste Schritte
Wenn beim Erstellen eines neuen virtuellen Windows-Computers in Azure Probleme auftreten, finden Sie Informationen unter [Behandeln von Bereitstellungsproblemen beim Erstellen eines neuen virtuellen Windows-Computers in Azure](./troubleshoot-deployment-new-vm-windows.md).
