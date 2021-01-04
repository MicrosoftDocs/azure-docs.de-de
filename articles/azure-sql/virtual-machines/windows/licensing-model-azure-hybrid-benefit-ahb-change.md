---
title: Ändern des Lizenzierungsmodells für eine SQL-VM in Azure
description: Es wird beschrieben, wie Sie die Lizenzierung für einen virtuellen SQL Server-Computer in Azure von „nutzungsbasierter Bezahlung“ (Pay-as-you-go, PAYG) in „Bring-Your-Own-License“ (BYOL) ändern, indem Sie den Azure-Hybridvorteil nutzen.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.subservice: management
ms.workload: iaas-sql-server
ms.date: 11/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: da136c1f3ce36bf85592c3c73c9e8f92c41c7442
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/12/2020
ms.locfileid: "97357907"
---
# <a name="change-the-license-model-for-a-sql-virtual-machine-in-azure"></a>Ändern des Lizenzierungsmodells für einen virtuellen SQL-Computer in Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]


In diesem Artikel wird beschrieben, wie Sie das Lizenzierungsmodell für einen virtuellen SQL Server-Computer (Virtual Machine, VM) in Azure mithilfe der [SQL-IaaS-Agent-Erweiterung](./sql-server-iaas-agent-extension-automate-management.md) ändern.

## <a name="overview"></a>Übersicht

Es gibt drei Lizenzierungsmodelle für eine Azure-VM, die SQL Server hostet: nutzungsbasierte Bezahlung, Azure-Hybridvorteil (Azure Hybrid Benefit, AHB) und Hochverfügbarkeit/Notfallwiederherstellung (High Availability/Disaster Recovery, HA/DR). Sie können das Lizenzierungsmodell Ihrer SQL Server-VM im Azure-Portal, mit der Azure-Befehlszeilenschnittstelle (Azure CLI) oder mit PowerShell ändern. 

- **Nutzungsbasierte Bezahlung** bedeutet, dass die pro Sekunde berechneten Kosten für die Ausführung des virtuellen Azure-Computers die Kosten für die SQL Server-Lizenz enthalten.
- Der [Azure-Hybridvorteil](https://azure.microsoft.com/pricing/hybrid-benefit/) ermöglicht Ihnen die Verwendung Ihrer eigenen SQL Server-Lizenz mit einer VM, auf der SQL Server ausgeführt wird. 
- Der Lizenztyp **Hochverfügbarkeit/Notfallwiederherstellung** wird für das [kostenlose HADR-Replikat](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure) in Azure verwendet. 

Beim Azure-Hybridvorteil können Sie SQL Server-Lizenzen mit Software Assurance („qualifizierte Lizenz“) auf Azure-VMs verwenden. Beim Azure-Hybridvorteil wird Kunden nicht die Verwendung einer SQL Server-Lizenz auf einem virtuellen Computer in Rechnung gestellt. Sie müssen jedoch weiterhin die Kosten des zugrunde liegenden Cloudcomputings (d. h. den Basistarif), des Speichers und der Sicherungen bezahlen. Außerdem müssen sie für die E/A-Vorgänge bezahlen, die mit der Nutzung der Dienste in Verbindung stehen (sofern zutreffend).

Gemäß den Microsoft-Produktbestimmungen: „...müssen Kunden beim Konfigurieren von Workloads in Azure angeben, dass sie Azure SQL-Datenbank (verwaltete Instanz, Pool für elastische Datenbanken und Einzeldatenbank), Azure Data Factory, SQL Server Integration Services oder SQL Server-VMs im Rahmen des Azure-Hybridvorteils für SQL Server verwenden“.

Um die Verwendung des Azure-Hybridvorteils für SQL Server auf einer Azure-VM anzugeben und die Konformität zu wahren, stehen drei Optionen für Sie zur Auswahl:

- Stellen Sie eine VM mit einem „Bring-Your-Own-License“-SQL Server-Image aus dem Azure Marketplace bereit. Diese Option ist nur für Kunden verfügbar, die über ein Enterprise Agreement verfügen.
- Stellen Sie eine VM mit einem SQL Server-Image mit nutzungsbasierter Bezahlung aus dem Azure Marketplace bereit, und aktivieren Sie den Azure-Hybridvorteil.
- Installieren Sie SQL Server selbst auf einer Azure-VM, [registrieren Sie die VM mit der SQL-IaaS-Agent-Erweiterung](sql-agent-extension-manually-register-single-vm.md) manuell, und aktivieren Sie den Azure-Hybridvorteil.

Der SQL Server-Lizenztyp kann beim Bereitstellen der VM konfiguriert und danach jederzeit geändert werden. Der Wechsel zwischen den Lizenzmodellen führt zu keinen Ausfallzeiten. Die VM oder der SQL Server-Dienst startet nicht neu, es entstehen keine zusätzlichen Kosten, und das neue Modell ist sofort wirksam. Durch die Aktivierung des Azure-Hybridvorteils werden die Kosten *reduziert*.

## <a name="prerequisites"></a>Voraussetzungen

Das Ändern des Lizenzierungsmodells Ihrer SQL Server-VM stellt die folgenden Anforderungen: 

- Ein [Azure-Abonnement](https://azure.microsoft.com/free/).
- Eine mit der [SQL-IaaS-Agent-Erweiterung](./sql-server-iaas-agent-extension-automate-management.md) registrierte [SQL Server-VM](./create-sql-vm-portal.md).
- [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default) ist eine Voraussetzung für die Verwendung des [Azure-Hybridvorteils](https://azure.microsoft.com/pricing/hybrid-benefit/). 


## <a name="change-license-model"></a>Ändern des Lizenzierungsmodells für einen virtuellen SQL-Computer in Azure

# <a name="azure-portal"></a>[Azure portal](#tab/azure-portal)

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Sie können das Lizenzierungsmodell direkt über das Portal ändern: 

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com), und starten Sie die Ressource [Virtueller SQL-Computer](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource) für Ihre SQL Server-VM. 
1. Wählen Sie **Konfigurieren** unter **Einstellungen** aus. 
1. Wählen Sie **Azure-Hybridvorteil** aus, und aktivieren Sie das Kontrollkästchen, um zu bestätigen, dass Sie über eine SQL Server-Lizenz mit Software Assurance verfügen. 
1. Wählen Sie **Übernehmen** am unteren Rand der Seite **Konfigurieren** aus. 

![Azure-Hybridvorteil im Portal](./media/licensing-model-azure-hybrid-benefit-ahb-change/ahb-in-portal.png)


# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Sie können die Azure CLI (Azure-Befehlszeilenschnittstelle) nutzen, um das Lizenzierungsmodell zu wechseln.  

Geben Sie die folgenden Werte für den Lizenztyp (**license-type**) an:
- `AHUB` für den Azure-Hybridvorteil
- `PAYG` für nutzungsbasierte Bezahlung
- `DR` zum Aktivieren des kostenlosen HADR-Replikats


```azurecli-interactive
# example: az sql vm update -n AHBTest -g AHBTest --license-type AHUB

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type <license-type>
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Sie können Ihr Lizenzierungsmodell auch mit PowerShell ändern.

Geben Sie die folgenden Werte für den Lizenztyp (**license-type**) an:
- `AHUB` für den Azure-Hybridvorteil
- `PAYG` für nutzungsbasierte Bezahlung
- `DR` zum Aktivieren des kostenlosen HADR-Replikats


```powershell-interactive
Update-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name> -LicenseType <license-type>
```

---

## <a name="remarks"></a>Bemerkungen

- Azure CSP-Kunden (Azure Cloud Solution Provider) können den Azure-Hybridvorteil nutzen, indem sie zuerst eine VM mit nutzungsbasierter Bezahlung bereitstellen und sie dann in Bring-Your-Own-License umwandeln, wenn sie über aktive Software Assurance verfügen.
- Wenn Sie Ihre SQL Server-VM-Ressource verwerfen, gelangen Sie zurück zur hartcodierten Lizenzeinstellung des Images. 
- Die Möglichkeit, das Lizenzierungsmodell zu ändern, ist ein Feature der SQL-IaaS-Agent-Erweiterung. Beim Bereitstellen eines Azure Marketplace-Images über das Azure-Portal wird eine SQL Server-VM mit der Erweiterung automatisch registriert. Kunden, die SQL Server selbst installieren, müssen [ihre SQL Server-VM jedoch manuell registrieren](sql-agent-extension-manually-register-single-vm.md). 
- Das Hinzufügen einer SQL Server-VM zu einer Verfügbarkeitsgruppe erfordert die Neuerstellung der VM. Daher kehren alle VMs, die einer Verfügbarkeitsgruppe hinzugefügt werden, zum Standardlizenztyp „Nutzungsbasierte Bezahlung“ zurück. Der Azure-Hybridvorteil muss erneut aktiviert werden. 


## <a name="limitations"></a>Einschränkungen

Das Ändern des Lizenzmodells
   - ist nur für Kunden mit [Software Assurance](https://www.microsoft.com/en-us/licensing/licensing-programs/software-assurance-overview) verfügbar.
   - wird nur für die Standard Edition und Enterprise Edition von SQL Server unterstützt. unterstützt keine Lizenzänderungen für Express, Web und Developer. 
   - wird nur für virtuelle Computer unterstützt, die mit dem Azure Resource Manager-Modell bereitgestellt wurden. unterstützt keine SQL Server-VMs, die mit dem klassischen Modell bereitgestellt wurden. 
   - Nur für öffentliche oder Azure Government-Clouds verfügbar. 
   - wird nur auf virtuellen Computern unterstützt, die eine einzelne Netzwerkschnittstelle haben. 

> [!Note]
> Der Azure-Hybridvorteil kann nur für die core-basierte SQL Server-Lizenzierung mit Software Assurance- oder Abonnementlizenzen genutzt werden. Wenn Sie die Server- und CAL-Lizenzierung für SQL Server verwenden und über Software Assurance verfügen, können Sie BYOL für ein Azure SQL Server-VM-Image verwenden, um die Lizenzmobilität für diese Server zu nutzen. Die anderen AHB-Features können Sie jedoch nicht nutzen. 

## <a name="known-errors"></a>Bekannte Fehler

Überprüfen Sie die allgemein bekannten Fehler und ihre Lösungen. 

**Die Ressource „Microsoft.SqlVirtualMachine/SqlVirtualMachines/\<resource-group>“ unter der Ressourcengruppe „\<resource-group>“ wurde nicht gefunden.**

Dieser Fehler tritt auf, wenn Sie versuchen, das Lizenzierungsmodell auf einer SQL Server-VM zu ändern, die nicht mit der SQL-IaaS-Agent-Erweiterung registriert wurde:

`The Resource 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/\<resource-group>' under resource group '\<resource-group>' was not found. The property 'sqlServerLicenseType' cannot be found on this object. Verify that the property exists and can be set.`

Sie müssen Ihr Abonnement beim Ressourcenanbieter registrieren und dann [Ihre SQL Server-VM mit der SQL-IaaS-Agent-Erweiterung registrieren](sql-agent-extension-manually-register-single-vm.md). 


**Der virtuelle Computer „\<vmname\>“ hat mehr als eine zugeordnete Netzwerkschnittstelle**

Dieser Fehler tritt auf virtuellen Computern auf, die über mehr als eine Netzwerkschnittstelle verfügen. Entfernen Sie eine der Netzwerkschnittstellen, bevor Sie das Lizenzierungsmodell ändern. Obwohl Sie die Netzwerkschnittstelle nach dem Ändern des Lizenzmodells wieder zur VM hinzufügen können, werden Vorgänge im Azure-Portal wie die automatische Sicherung und das automatische Patchen nicht mehr unterstützt. 


## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in den folgenden Artikeln: 

* [Übersicht über SQL Server auf einem virtuellen Windows-Computer](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Häufig gestellte Fragen zu SQL Server auf virtuellen Windows-Computern](frequently-asked-questions-faq.md)
* [Preisinformationen für SQL Server auf virtuellen Windows-Computern](pricing-guidance.md)
* [SQL Server auf Windows-VMs – Versionshinweise](../../database/doc-changes-updates-release-notes.md)
* [Übersicht über die Erweiterung für den SQL-IaaS-Agent](./sql-server-iaas-agent-extension-automate-management.md)
