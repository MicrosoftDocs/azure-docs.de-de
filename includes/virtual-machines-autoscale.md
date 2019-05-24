---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 3c008e77116a9b42a2ea137069529c5e241adddd
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66160072"
---
Sie können Ihre [virtuellen Computer (VMs)](../articles/virtual-machines/windows/overview.md) leicht [automatisch skalieren](../articles/azure-monitor/platform/autoscale-best-practices.md), indem Sie [VM-Skalierungsgruppen](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) und das [Feature für die automatische Skalierung von Azure Monitor](../articles/azure-monitor/platform/autoscale-overview.md) verwenden. Ihre VMs müssen Mitglieder einer Skalierungsgruppe sein, um automatisch skaliert werden zu können. Dieser Artikel enthält Informationen, mit denen Sie besser verstehen, wie Sie Ihre VMs mit automatischen und manuellen Methoden sowohl vertikal als auch horizontal skalieren können.

## <a name="horizontal-or-vertical-scaling"></a>Horizontale oder vertikale Skalierung

Das Feature für die automatische Skalierung von Azure Monitor ermöglicht nur die horizontale Skalierung, also eine Erhöhung (hochskalieren) oder Verringerung (herunterskalieren) der VM-Anzahl. Die horizontale Skalierung ist in einer Cloudsituation flexibler, da Sie bei Bedarf Tausende von VMs ausführen können, um die Last zu bewältigen. Sie können horizontal skalieren, indem Sie die Kapazität (oder die Instanzanzahl) der Skalierungsgruppe entweder automatisch oder manuell ändern. 

Bei der vertikalen Skalierung wird die gleiche Anzahl von VMs beibehalten, aber die Leistungsfähigkeit der VMs wird erhöht („zentral hochskalieren“) oder verringert („zentral herunterskalieren“). Die Leistung wird anhand von Attributen gemessen, z.B. Arbeitsspeicher, CPU-Geschwindigkeit oder Speicherplatz. Die vertikale Skalierung ist von der Verfügbarkeit größerer Hardware abhängig, die relativ schnell das obere Limit erreicht und je nach Region variieren kann. Außerdem ist für die vertikale Skalierung auch das Beenden und Starten einer VM erforderlich. Sie skalieren vertikal, indem Sie für die Konfiguration der VMs in der Skalierungsgruppe eine neue Größe festlegen.

Durch die Verwendung von Runbooks in [Azure Automation](../articles/automation/automation-intro.md) können Sie [VMs in einer Skalierungsgruppe skalieren](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-vertical-scale-reprovision.md) (hoch oder herunter).

## <a name="create-a-virtual-machine-scale-set"></a>Erstellen einer Skalierungsgruppe für virtuelle Computer

Skalierungsgruppen erleichtern Ihnen die Bereitstellung und Verwaltung von identischen VMs als Gruppe. Sie können Linux- oder Windows-Skalierungsgruppen mit dem [Azure-Portal](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-portal-create.md), [Azure PowerShell](../articles/virtual-machines/windows/tutorial-create-vmss.md) oder der [Azure CLI](../articles/virtual-machines/linux/tutorial-create-vmss.md) erstellen. Außerdem können Sie Skalierungsgruppen mit SDKs, z.B. [Python](https://azure.microsoft.com/develop/python/) oder [Node.js](/nodejs/azure), oder direkt mit den [REST-APIs](/rest/api/compute/virtualmachinescalesets) erstellen und verwalten. Die automatische Skalierung von VMs wird erreicht, indem Metriken und Regeln auf die Skalierungsgruppe angewendet werden.

## <a name="configure-autoscale-for-a-scale-set"></a>Konfigurieren der automatischen Skalierung für eine Skalierungsgruppe

Bei der automatischen Skalierung wird die richtige Anzahl von VMs bereitgestellt, um die Last für Ihre Anwendung zu bewältigen. Sie können VMs hinzufügen, um einen Anstieg der Auslastung auszugleichen, und Kosten sparen, indem Sie ungenutzte VMs entfernen. Sie geben basierend auf einer Gruppe von Regeln eine minimale und maximale Anzahl von VMs an, die ausgeführt werden sollen. Eine minimale Anzahl sorgt dafür, dass Ihre Anwendung auch dann immer ausgeführt wird, wenn keine Last vorhanden ist. Bei Verwendung eines Höchstwerts können Sie eine Obergrenze für die Gesamtkosten pro Stunde festlegen.

Sie können die automatische Skalierung aktivieren, wenn Sie die Skalierungsgruppe erstellen, indem Sie [Azure PowerShell](../articles/azure-monitor/platform/powershell-quickstart-samples.md#create-and-manage-autoscale-settings) oder [Azure CLI](https://docs.microsoft.com/cli/azure/monitor/autoscale-settings) verwenden. Die Aktivierung ist auch nach der Erstellung der Skalierungsgruppe möglich. Sie können eine Skalierungsgruppe erstellen, die Erweiterung installieren und die automatische Skalierung mit einer [Azure Resource Manager-Vorlage](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-windows-autoscale.md) konfigurieren. Aktivieren Sie im Azure-Portal die automatische Skalierung über Azure Monitor, oder führen Sie die Aktivierung dafür über die Einstellungen für die Skalierungsgruppe durch.

![Aktivieren der automatischen Skalierung](./media/virtual-machines-autoscale/virtual-machines-autoscale-enable.png)
 
### <a name="metrics"></a>Metriken

Mit dem Azure Monitor-Feature für die automatische Skalierung können Sie die Anzahl von ausgeführten VMs basierend auf [Metriken](../articles/azure-monitor/platform/autoscale-common-metrics.md) zentral hoch- oder herunterskalieren. Standardmäßig werden von VMs grundlegende Hostebenenmetriken für die Datenträger-, Netzwerk- und CPU-Auslastung bereitgestellt. Wenn Sie die Sammlung von Diagnosedaten mit der Diagnoseerweiterung konfigurieren, werden für den Datenträger, die CPU und den Arbeitsspeicher zusätzliche Leistungsindikatoren für das Gastbetriebssystem bereitgestellt.

![Kriterien für Metriken](./media/virtual-machines-autoscale/virtual-machines-autoscale-criteria.png)

Falls Ihre Anwendung basierend auf Metriken skaliert werden muss, die über den Host nicht verfügbar sind, muss auf den VMs in der Skalierungsgruppe entweder die [Linux-Diagnoseerweiterung](../articles/virtual-machines/linux/diagnostic-extension.md) oder die [Windows-Diagnoseerweiterung](../articles/virtual-machines/windows/ps-extensions-diagnostics.md) installiert sein. Wenn Sie mit dem Azure-Portal eine Skalierungsgruppe erstellen, müssen Sie auch Azure PowerShell oder die Azure CLI verwenden, um die Erweiterung mit der erforderlichen Diagnosekonfiguration zu installieren.
 
### <a name="rules"></a>Regeln

Bei [Regeln](../articles/monitoring-and-diagnostics/monitoring-autoscale-scale-by-custom-metric.md) wird eine Metrik mit einer durchzuführenden Aktion kombiniert. Wenn die Bedingungen der Regel erfüllt sind, werden eine oder mehrere Aktionen der automatischen Skalierung ausgelöst. Sie können beispielsweise eine Regel definieren, mit der die Anzahl von VMs um 1 erhöht wird, wenn die durchschnittliche CPU-Auslastung über 85 Prozent steigt.

![Aktionen für die automatische Skalierung](./media/virtual-machines-autoscale/virtual-machines-autoscale-actions.png)
 
### <a name="notifications"></a>Benachrichtigungen

Sie können [Trigger einrichten](../articles/azure-monitor/platform/autoscale-webhook-email.md), damit basierend auf den von Ihnen erstellten Regeln für die automatische Skalierung bestimmte Web-URLs aufgerufen oder E-Mails gesendet werden. Mithilfe von Webhooks können Sie die Azure-Warnbenachrichtigungen für die Nachbearbeitung oder benutzerdefinierte Benachrichtigungen an andere Systeme weiterleiten.

## <a name="manually-scale-vms-in-a-scale-set"></a>Manuelles Skalieren von VMs in einer Skalierungsgruppe

### <a name="horizontal"></a>Horizontal

Sie können VMs hinzufügen oder entfernen, indem Sie die Skalierungsgruppe ändern. Im Azure-Portal können Sie die Anzahl von VMs (als **Instanzanzahl** angezeigt) in der Skalierungsgruppe verringern oder erhöhen, indem Sie die Leiste zum Außerkraftsetzen der Bedingung auf der Skalierungsseite nach links oder rechts verschieben.

Mit Azure PowerShell müssen Sie das Skalierungsgruppenobjekt abrufen, indem Sie [Get-AzVmss](https://docs.microsoft.com/powershell/module/az.compute/get-azvmss) verwenden. Anschließend legen Sie die Eigenschaft **sku.capacity** auf die gewünschte Anzahl von VMs fest und aktualisieren die Skalierungsgruppe mit [Update-AzVmss](https://docs.microsoft.com/powershell/module/az.compute/update-azvmss). Mit der Azure CLI ändern Sie die Kapazität für den Befehl [az vmss scale](/cli/azure/vmss?view=azure-cli-latest#az-vmss-scale) mit dem Parameter **--new-capacity**.

### <a name="vertical"></a>Vertical

Sie können die Größe der VMs im Azure-Portal auf der Seite „Größe“ für die Skalierungsgruppe manuell ändern. Sie können Azure PowerShell mit „Get-AzVmss“ nutzen, indem Sie die Eigenschaft für die Imageverweis-SKU festlegen und dann [Update-AzVmss](https://docs.microsoft.com/powershell/module/az.compute/update-azvmss) und [Update-AzVmssInstance](https://docs.microsoft.com/powershell/module/az.compute/update-azvmssinstance) verwenden.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu Skalierungsgruppen finden Sie unter [Überlegungen zum Entwurf von Skalierungsgruppen](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-design-overview.md).

