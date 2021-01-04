---
title: Konfigurieren eines Log Analytics-Arbeitsbereichs für Azure Monitor für VMs
description: Hier wird beschrieben, wie der von Azure Monitor für VMs verwendete Log Analytics-Arbeitsbereich erstellt und konfiguriert wird.
ms.subservice: ''
ms.topic: conceptual
ms.custom: references_regions
author: bwren
ms.author: bwren
ms.date: 07/27/2020
ms.openlocfilehash: a4380ae7fdb523f8b2f5bc14fefa094b6d96e547
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95750529"
---
# <a name="configure-log-analytics-workspace-for-azure-monitor-for-vms"></a>Konfigurieren eines Log Analytics-Arbeitsbereichs für Azure Monitor für VMs
Azure Monitor für VMs erfasst die Daten von einem oder mehreren Log Analytics-Arbeitsbereichen in Azure Monitor. Vor dem Integrieren von Agents müssen Sie einen Arbeitsbereich erstellen und konfigurieren. In diesem Artikel werden die Anforderungen für den Arbeitsbereich und dessen Konfiguration für Azure Monitor für VMs beschrieben.

## <a name="overview"></a>Übersicht
Ein einzelnes Abonnement kann je nach Anforderungen eine beliebige Anzahl von Arbeitsbereichen verwenden. Die einzige Anforderung für den Arbeitsbereich ist, dass er sich an einem unterstützten Standort befindet und mit der *VMInsights*-Lösung konfiguriert ist.

Nach der Konfiguration des Arbeitsbereichs können Sie die erforderlichen Agents über eine der verfügbaren Optionen auf der VM und in VM-Skalierungsgruppen installieren und einen Arbeitsbereich angeben, an den diese ihre Daten senden. Azure Monitor für VMs erfasst Daten aus jedem konfigurierten Arbeitsbereich in seinem Abonnement.

> [!NOTE]
> Wenn Sie Azure Monitor für VMs für eine einzelne VM oder VM-Skalierungsgruppe über das Azure-Portal aktivieren, haben Sie die Möglichkeit, einen vorhandenen Arbeitsbereich auszuwählen oder einen neuen zu erstellen. Die *VMInsights*-Lösung wird in diesem Arbeitsbereich installiert, falls dies noch nicht geschehen ist. Anschließend können Sie diesen Arbeitsbereich für andere Agents verwenden.


## <a name="create-log-analytics-workspace"></a>Erstellen eines Log Analytics-Arbeitsbereichs

>[!NOTE]
>Die in diesem Abschnitt beschriebenen Informationen gelten auch für die [Dienstzuordnungslösung](service-map.md). 

Greifen Sie auf Log Analytics-Arbeitsbereiche im Azure-Portal über das Menü **Log Analytics-Arbeitsbereiche** zu.

[![Log Analytics-Arbeitsbereiche](media/vminsights-configure-workspace/log-analytics-workspaces.png)](media/vminsights-configure-workspace/log-analytics-workspaces.png#lightbox)

Sie können einen neuen Log Analytics-Arbeitsbereich mit einer der folgenden Methoden erstellen. Eine Anleitung zum Bestimmen der Anzahl von Arbeitsbereichen, die Sie in Ihrer Umgebung verwenden sollten, und zum Entwerfen Ihrer Zugriffsstrategie finden Sie unter [Entwerfen Ihrer Azure Monitor-Protokollbereitstellung](../platform/design-logs-deployment.md).


* [Azure portal](../../azure-monitor/learn/quick-create-workspace.md)
* [Azure-Befehlszeilenschnittstelle](../../azure-monitor/learn/quick-create-workspace-cli.md)
* [PowerShell](../platform/powershell-workspace-configuration.md)
* [Azure Resource Manager](../samples/resource-manager-workspace.md)

## <a name="supported-regions"></a>Unterstützte Regionen

Azure Monitor für VMs unterstützt Log Analytics-Arbeitsbereiche in den folgenden Regionen, doch können Sie virtuelle Computer in jeder Region überwachen. Die virtuellen Computer selbst sind nicht auf die vom Log Analytics-Arbeitsbereich unterstützten Bereiche beschränkt.

- USA, Westen-Mitte
- USA (Westen)
- USA, Westen 2
- USA Süd Mitte
- East US
- USA (Ost 2)
- USA (Mitte)
- USA Nord Mitte
- US Gov Az
- US Gov Va
- Kanada, Mitte
- UK, Süden
- Nordeuropa
- Europa, Westen
- Asien, Osten
- Asien, Südosten
- Indien, Mitte
- Japan, Osten
- Australien (Osten)
- Australien, Südosten

## <a name="azure-role-based-access-control"></a>Rollenbasierte Zugriffssteuerung in Azure
Um die Features in Azure Monitor für VMs zu aktivieren und darauf zuzugreifen, müssen Sie über die Rolle [Log Analytics-Mitwirkender](../platform/manage-access.md#manage-access-using-azure-permissions) im Arbeitsbereich verfügen. Sie müssen über die Rolle [Überwachungsleser](../platform/roles-permissions-security.md#built-in-monitoring-roles) für die Azure-VM verfügen, um Leistungs-, Integritäts- und Zuordnungsdaten anzeigen zu können. Weitere Informationen zur Zugriffssteuerung auf einen Log Analytics-Arbeitsbereich finden Sie unter [Verwalten von Arbeitsbereichen](../platform/manage-access.md).

## <a name="add-vminsights-solution-to-workspace"></a>Hinzufügen der VMInsights-Lösung zum Arbeitsbereich
Bevor ein Log Analytics-Arbeitsbereich mit Azure Monitor für VMs verwendet werden kann, muss die *VMInsights*-Lösung installiert sein. Die Methoden zum Konfigurieren des Arbeitsbereichs werden in den folgenden Abschnitten beschrieben.

> [!NOTE]
> Wenn Sie dem Arbeitsbereich die *VMInsights*-Lösung hinzufügen, beginnen alle vorhandenen virtuellen Computer, die mit dem Arbeitsbereich verbunden sind, mit dem Senden von Daten an InsightsMetrics. Daten für die anderen Datentypen werden erst erfasst, wenn Sie den Dependency-Agent zu den vorhandenen virtuellen Computern hinzufügen, die mit dem Arbeitsbereich verbunden sind.

### <a name="azure-portal"></a>Azure-Portal
Es gibt drei Optionen zum Konfigurieren eines vorhandenen Arbeitsbereichs über das Azure-Portal. Diese werden unten im Einzelnen beschrieben.

Um einen einzelnen Arbeitsbereich zu konfigurieren, wechseln Sie im Menü **Azure Monitor** zur Option **Virtuelle Computer**, und wählen Sie **Weitere Onboardingoptionen** und dann **Arbeitsbereich konfigurieren** aus. Wählen Sie ein Abonnement und einen Arbeitsbereich aus, und klicken Sie dann auf **Konfigurieren**.

[![Konfigurieren eines Arbeitsbereichs](media/vminsights-enable-at-scale-policy/configure-workspace.png)](media/vminsights-enable-at-scale-policy/configure-workspace.png#lightbox)

Wenn Sie mehrere Arbeitsbereiche konfigurieren möchten, wählen Sie im Azure-Portal im Menü **Überwachen** im Menü **Virtuelle Computer** die Registerkarte **Arbeitsbereichskonfiguration** aus. Legen Sie die Filterwerte so fest, dass eine Liste der vorhandenen Arbeitsbereiche angezeigt wird. Aktivieren Sie die Kontrollkästchen neben den einzelnen Arbeitsbereichen, um sie zu aktivieren, und klicken Sie dann auf **Ausgewählte konfigurieren**.

[![Arbeitsbereichskonfiguration](media/vminsights-enable-at-scale-policy/workspace-configuration.png)](media/vminsights-enable-at-scale-policy/workspace-configuration.png#lightbox)


Wenn Sie Azure Monitor für VMs für eine einzelne VM oder VM-Skalierungsgruppe über das Azure-Portal aktivieren, haben Sie die Möglichkeit, einen vorhandenen Arbeitsbereich auszuwählen oder einen neuen zu erstellen. Die *VMInsights*-Lösung wird in diesem Arbeitsbereich installiert, falls dies noch nicht geschehen ist. Anschließend können Sie diesen Arbeitsbereich für andere Agents verwenden.

[![Aktivieren einer einzelnen VM im Portal](media/vminsights-enable-single-vm/enable-vminsights-vm-portal.png)](media/vminsights-enable-single-vm/enable-vminsights-vm-portal.png#lightbox)


### <a name="resource-manager-template"></a>Resource Manager-Vorlage
Die Azure Resource Manager-Vorlagen für Azure Monitor für VMs werden in einer Archivdatei (.zip) bereitgestellt, die Sie [aus dem GitHub-Repository herunterladen](https://aka.ms/VmInsightsARMTemplates) können. Dies umfasst eine Vorlage mit dem Namen **ConfigureWorkspace**, mit der ein Log Analytics-Arbeitsbereich für Azure Monitor für VMs konfiguriert wird. Sie stellen diese Vorlage mithilfe einer der Standardmethoden bereit. Dazu gehören auch die folgenden PowerShell- und CLI-Beispielbefehle: 

# <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/CLI)

```azurecli
az deployment group create --name ConfigureWorkspace --resource-group my-resource-group --template-file CreateWorkspace.json  --parameters workspaceResourceId='/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace' workspaceLocation='eastus'

```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
New-AzResourceGroupDeployment -Name ConfigureWorkspace -ResourceGroupName my-resource-group -TemplateFile ConfigureWorkspace.json -workspaceResourceId /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace -location eastus
```

---



## <a name="next-steps"></a>Nächste Schritte
- Informationen zum Verbinden von Agents mit Azure Monitor für VMs finden Sie unter [Integrieren von Agents in Azure Monitor für VMs](vminsights-enable-overview.md).
- Informationen zum Einschränken der Datenmenge, die von einer Lösung an den Arbeitsbereich gesendet wird, finden Sie unter [Zielgruppenadressierung für Überwachungslösungen in Azure Monitor (Vorschau)](solution-targeting.md).
