---
title: 'Aktivieren von Azure Monitor für VMs: Übersicht'
description: Erfahren Sie, wie Sie eine Azure Monitor für VMs bereitstellen und konfigurieren. Informieren Sie sich über die Systemanforderungen.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/27/2020
ms.custom: references_regions
ms.openlocfilehash: f5e774e9b7327d4b403f6a09187e97082a77aa78
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96186802"
---
# <a name="enable-azure-monitor-for-vms-overview"></a>Aktivieren von Azure Monitor für VMs: Übersicht

Dieser Artikel bietet eine Übersicht über die verfügbaren Optionen zum Aktivieren von Azure Monitor für VMs, um die Integrität und Leistung folgender Elemente zu überwachen:

- Virtuelle Azure-Computer 
- Azure-VM-Skalierungsgruppen
- Hybrid-VMs, die mit Azure Arc verbunden sind
- Lokale virtuelle Computer
- In einer anderen Cloudumgebung gehostete virtuelle Computer  

So richten Sie Azure Monitor für VMs ein

* Aktivieren Sie eine einzelne Azure-VM, eine einzelne Azure-VMSS oder einen einzelnen Azure Arc-Computer, indem Sie **Insights** direkt im entsprechenden Menü im Azure-Portal aktivieren.
* Aktivieren Sie mehrere Azure-VMs, Azure-VMSS oder Azure Arc-Computer mithilfe von Azure Policy. Diese Methode stellt sicher, dass bei bestehenden und neuen virtuellen Computern und Skalierungsgruppen die erforderlichen Abhängigkeiten installiert und ordnungsgemäß konfiguriert werden. Nicht konforme VMs und Skalierungsgruppen werden gemeldet, sodass Sie entscheiden können, ob Sie sie aktivieren und den Zustand korrigieren möchten.
* Aktivieren Sie mehrere Azure-VMs, Azure Arc-VMs, Azure-VMSS oder Azure Arc-Computer über ein angegebenes Abonnement oder eine angegebene Ressourcengruppe mithilfe von PowerShell.
* Aktivieren Sie Azure Monitor für VMs zum Überwachen von virtuellen Computern oder physischen Computern, die in Ihrem Unternehmensnetzwerk oder einer anderen Cloudumgebung gehostet werden.

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie beginnen, stellen Sie sicher, dass Sie die Informationen in den folgenden Abschnitten verstanden haben. 

>[!NOTE]
>Die in diesem Abschnitt beschriebenen Informationen gelten auch für die [Dienstzuordnungslösung](service-map.md).  

### <a name="log-analytics-workspace"></a>Log Analytics-Arbeitsbereich

Azure Monitor für VMs unterstützt einen Log Analytics-Arbeitsbereich in den folgenden Regionen:

- Afrika
  - Südafrika, Norden
- Asien-Pazifik
  - Asien, Osten
  - Asien, Südosten
- Australien
  - Australien (Osten)
  - Australien, Südosten
- Azure Government
  - US Gov Az
  - US Gov Va
- Canada
  - Kanada, Mitte
- Europa
  - Nordeuropa
  - Europa, Westen
- Indien
  - Indien, Mitte
- Japan
  - Japan, Osten
- United Kingdom
  - UK, Süden
- USA
  - USA (Mitte)
  - East US
  - USA (Ost 2)
  - USA Nord Mitte
  - USA Süd Mitte
  - USA, Westen-Mitte
  - USA (Westen)
  - USA, Westen 2


>[!NOTE]
>Sie können Azure-VMs in jeder Region überwachen. Die VMs selbst sind nicht auf die vom Log Analytics-Arbeitsbereich unterstützten Bereiche beschränkt.
>

Wenn Sie über keinen Log Analytics-Arbeitsbereich verfügen, können Sie einen erstellen, indem Sie eine der folgenden Ressourcen verwenden:
* [Azure-Befehlszeilenschnittstelle](../learn/quick-create-workspace-cli.md)
* [PowerShell](../platform/powershell-workspace-configuration.md)
* [Azure portal](../learn/quick-create-workspace.md)
* [Azure Resource Manager](../samples/resource-manager-workspace.md)

- Virtueller Azure-Computer
- Azure-VM-Skalierungsgruppe
- Mit Azure Arc verbundene Hybrid-VM

## <a name="supported-operating-systems"></a>Unterstützte Betriebssysteme

Azure Monitor für VMs unterstützt alle Betriebssysteme, die den Log Analytics-Agent und den Dependency-Agent unterstützen. Eine vollständige Liste finden Sie unter [Übersicht über Azure Monitor-Agents](../platform/agents-overview.md#supported-operating-systems).

In der folgenden Liste finden Sie Überlegungen zur Linux-Unterstützung des Dependency-Agents, der Azure Monitor für VMs unterstützt:

- Es werden nur die Standardversion und SMP-Version des Linux-Kernels unterstützt.
- Nicht-Standardversionen des Kernels, z. B. PAE (Physical Address Extension) und Xen, werden für keine Linux-Distribution unterstützt. Beispielsweise wird ein System mit der Versionszeichenfolge *2.6.16.21-0.8-xen* nicht unterstützt.
- Benutzerdefinierte Kernels, einschließlich Neukompilierungen von Standardkernels, werden nicht unterstützt.
- Für andere Debian-Distributionen als Version 9.4 wird das Zuordnungsfeature nicht unterstützt, und das Leistungsfeature ist nur über das Azure Monitor-Menü verfügbar. Es ist nicht direkt über den linken Bereich des virtuellen Azure-Computers verfügbar.
- Der CentOSPlus-Kernel wird unterstützt.
- Der Linux-Kernel muss für das Spectre-Sicherheitsrisiko gepatcht werden. Weitere Informationen erhalten Sie beim Anbieter der Linux-Distribution.



## <a name="supported-azure-arc-machines"></a>Unterstützte Azure Arc-Computer
Azure Monitor für VMs ist für Azure Arc-fähige Server in Regionen verfügbar, in denen der Arc-Erweiterungsdienst verfügbar ist. Sie müssen Version 0.9 oder höher des Arc-Agents ausführen.

| Verbundene Quelle | Unterstützt | BESCHREIBUNG |
|:--|:--|:--|
| Windows-Agents | Ja | Zusätzlich zum [Log Analytics-Agent für Windows](../platform/log-analytics-agent.md) benötigen Windows-Agents den Dependency-Agent. Weitere Informationen finden Sie unter [Unterstützte Betriebssysteme](../platform/agents-overview.md#supported-operating-systems). |
| Linux-Agents | Ja | Zusätzlich zum [Log Analytics-Agent für Linux](../platform/log-analytics-agent.md) benötigen Linux-Agents den Dependency-Agent. Weitere Informationen finden Sie unter [Unterstützte Betriebssysteme](#supported-operating-systems). |
| System Center Operations Manager-Verwaltungsgruppe | Nein | |

## <a name="agents"></a>Agents
Für Azure Monitor für VMs müssen die folgenden beiden Agents auf jeder zu überwachenden VM oder VM-Skalierungsgruppe installiert sein. Das Installieren dieser Agents und das Herstellen einer Verbindung zwischen ihnen und dem Arbeitsbereich sind die einzigen Voraussetzungen für das Onboarding der Ressource.

- [Log Analytics-Agent:](../platform/log-analytics-agent.md) Dieser Agent erfasst Ereignisse und Leistungsdaten für die VM oder VM-Skalierungsgruppe und übergibt sie an den Log Analytics-Arbeitsbereich. Bei Bereitstellungsmethoden für den Log Analytics-Agent in Azure-Ressourcen wird die VM-Erweiterung für [Windows](../../virtual-machines/extensions/oms-windows.md) und [Linux](../../virtual-machines/extensions/oms-linux.md) verwendet.
- Dependency-Agent: Dieser Agent erfasst ermittelte Daten über Prozesse, die auf dem virtuellen Computer ausgeführt werden, und Abhängigkeiten von externen Prozessen. Diese Daten werden vom [Zuordnungsfeature in Azure Monitor für VMs](vminsights-maps.md) verwendet. Der Dependency-Agent verwendet den Log Analytics-Agent, um die Daten an Azure Monitor zu senden. Bei Bereitstellungsmethoden für den Dependency-Agent in Azure-Ressourcen wird die VM-Erweiterung für [Windows](../../virtual-machines/extensions/agent-dependency-windows.md) und [Linux](../../virtual-machines/extensions/agent-dependency-linux.md) verwendet.

> [!NOTE]
> Beim Log Analytics-Agent handelt es sich um den Agent, der auch in System Center Operations Manager verwendet wird. Azure Monitor für VMs kann Agents überwachen, die auch von Operations Manager überwacht werden, wenn eine direkte Verbindung vorhanden ist und Sie den Dependency-Agent auf ihnen installieren. Agents, die über eine [Verwaltungsgruppenverbindung](../tform/../platform/om-agents.md) mit Azure Monitor verbunden sind, können nicht von Azure Monitor für VMs überwacht werden.

Im Folgenden sind verschiedene Methoden für die Bereitstellung dieser Agents aufgeführt. 

| Methode | BESCHREIBUNG |
|:---|:---|
| [Azure portal](./vminsights-enable-portal.md) | Installieren Sie beide Agents auf einem einzelnen virtuellen Computer, einer VM-Skalierungsgruppe oder virtuellen Hybridcomputern, die mit Azure Arc verbunden sind. |
| [Resource Manager-Vorlagen](vminsights-enable-resource-manager.md) | Installieren Sie beide Agents mithilfe einer der unterstützten Methoden zum Bereitstellen einer Resource Manager-Vorlage, z. B. über die CLI oder PowerShell. |
| [Azure Policy](./vminsights-enable-policy.md) | Weisen Sie eine Azure Policy-Initiative zu, um die Agents bei der Erstellung eines virtuellen Computers oder einer VM-Skalierungsgruppe automatisch zu installieren. |
| [Manuelle Installation](./vminsights-enable-hybrid.md) | Installieren Sie die Agents auf dem Gastbetriebssystem auf Computern, die außerhalb von Azure gehostet werden, z. B. in Ihrem Rechenzentrum oder anderen Cloudumgebungen. |




## <a name="management-packs"></a>Management Packs
Wenn ein Log Analytics-Arbeitsbereich für Azure Monitor für VMs konfiguriert wird, werden zwei Management Packs an alle Windows-Computer weitergeleitet, die mit diesem Arbeitsbereich verbunden sind. Diese Management Packs heißen *Microsoft.IntelligencePacks.ApplicationDependencyMonitor* und *Microsoft.IntelligencePacks.VMInsights* und werden in *%Programfiles%\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs\* geschrieben. 

Die vom Management Pack *ApplicationDependencyMonitor* verwendete Datenquelle ist * *%Program files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources\<AutoGeneratedID>\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll*. Die vom Management Pack *VMInsights* verwendete Datenquelle ist *%Program files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources\<AutoGeneratedID>\ Microsoft.VirtualMachineMonitoringModule.dll*.

## <a name="diagnostic-and-usage-data"></a>Diagnose- und Nutzungsdaten

Wenn Sie den Azure Monitor-Dienst verwenden, sammelt Microsoft automatisch Nutzungs- und Leistungsdaten. Microsoft verwendet diese Daten, um die Qualität, Sicherheit und Integrität des Diensts zu verbessern. 

Das Zuordnungsfeature enthält Daten zur Konfiguration Ihrer Software, um genaue und effiziente Funktionen zur Problembehandlung bereitzustellen. Die Daten enthalten Informationen wie Betriebssystem und Version, IP-Adresse, DNS-Name und Arbeitsstationsname. Microsoft erfasst weder Namen noch Adressen oder andere Kontaktinformationen.

Weitere Informationen zur Sammlung und Nutzung von Daten finden Sie in den [Datenschutzbestimmungen für Onlinedienste von Microsoft](https://go.microsoft.com/fwlink/?LinkId=512132).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>Nächste Schritte

Informationen zum Verwenden der Leistungsüberwachung finden Sie unter [Azure Monitor für VMs – Leistung anzeigen](vminsights-performance.md). Informationen zu ermittelten Anwendungsabhängigkeiten finden Sie unter [Azure Monitor für VMs – Zuordnung anzeigen](vminsights-maps.md).
