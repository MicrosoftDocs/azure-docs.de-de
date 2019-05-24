---
title: Automatisieren der Verwaltungsaufgaben auf virtuellen SQL-Computern (Resource Manager) | Microsoft-Dokumentation
description: In diesem Artikel wird die Verwaltung der SQL Server-Agent-Erweiterung beschrieben, mit der bestimmte SQL Server-Verwaltungsaufgaben automatisiert werden. Dazu gehören die automatische Sicherung, das automatische Patchen und die Azure-Schlüsseltresor-Integration.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-resource-manager
ms.assetid: effe4e2f-35b5-490a-b5ef-b06746083da4
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 07/12/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 2943a1501e1d81ff4884c21b5aa3861e16523bbb
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65827700"
---
# <a name="automate-management-tasks-on-azure-virtual-machines-with-the-sql-server-agent-extension-resource-manager"></a>Automatisieren von Verwaltungsaufgaben auf virtuellen Azure-Computern mit der SQL Server-Agent-Erweiterung (Resource Manager)
> [!div class="op_single_selector"]
> * [Ressourcen-Manager](virtual-machines-windows-sql-server-agent-extension.md)
> * [Klassisch](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md)

Die Erweiterung für SQL Server-IaaS-Agent (SqlIaasExtension) wird auf virtuellen Azure-Computern zum Automatisieren von Verwaltungsaufgaben ausgeführt. Dieser Artikel enthält eine Übersicht über die Dienste, die von der Erweiterung unterstützt werden, sowie Anweisungen zu Installation, Status und Deinstallation.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Den Artikel zum klassischen Bereitstellungsmodell finden Sie unter [SQL Server-Agent-Erweiterung für virtuelle SQL Server-Computer (klassisch)](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md).

## <a name="supported-services"></a>Unterstützte Dienste
Die Erweiterung für SQL Server-IaaS-Agent unterstützt die folgenden Verwaltungsaufgaben:

| Verwaltungsfeature | BESCHREIBUNG |
| --- | --- |
| **SQL – Automatisierte Sicherung** |Automatisiert die Planung von Sicherungen für alle Datenbanken entweder der Standardinstanz oder einer [ordnungsgemäß installierten](virtual-machines-windows-sql-server-iaas-faq.md#administration) benannten Instanz von SQL Server in der VM. Weitere Informationen finden Sie unter [Automatisierte Sicherung für SQL Server auf virtuellen Azure-Computern (Resource Manager)](virtual-machines-windows-sql-automated-backup.md). |
| **SQL – Automatisiertes Patchen** |Mit diesem Dienst können Sie ein Wartungsfenster konfigurieren, in dem wichtige Windows-Updates für den virtuellen Computer ausgeführt werden. Auf diese Weise werden Updates für Ihre Workload während der Spitzenzeiten vermieden. Weitere Informationen finden Sie unter [Automatisiertes Patchen für SQL Server auf virtuellen Azure-Computern (Resource Manager)](virtual-machines-windows-sql-automated-patching.md). |
| **Azure-Schlüsseltresor-Integration** |Mit diesem Dienst können Sie Azure Key Vault auf Ihrem virtuellen SQL Server-Computer automatisch installieren und konfigurieren. Weitere Informationen finden Sie unter [Konfigurieren der Azure Key Vault-Integration für SQL Server auf virtuellen Azure-Computern (Resource Manager)](virtual-machines-windows-ps-sql-keyvault.md). |

Nach der Installation und Ausführung stellt die SQL Server-IaaS-Agent-Erweiterung diese Verwaltungsfeatures im Bereich „SQL Server“ des virtuellen Computers im Azure-Portal bereit. Außerdem stehen sie für SQL Server-Marketplace-Images über Azure PowerShell und bei der manuellen Installation der Erweiterung über Azure PowerShell zur Verfügung. 

## <a name="prerequisites"></a>Voraussetzungen
Anforderungen für die Verwendung der Erweiterung für SQL Server-IaaS-Agent auf Ihrem virtuellen Computer:

**Betriebssystem:**

* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

**SQL Server-Versionen:**

* SQL Server 2008 
* SQL Server 2008 R2
* SQL Server 2012
* SQL Server 2014
* SQL Server 2016

**Azure PowerShell:**

* [Herunterladen und Konfigurieren der aktuellen Azure PowerShell-Befehle](/powershell/azure/overview)

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

> [!IMPORTANT]
> Derzeit wird die [SQL Server-IaaS-Agent-Erweiterung](virtual-machines-windows-sql-server-agent-extension.md) nicht für SQL Server-Failoverclusterinstanzen (FCI) in Azure unterstützt. Sie sollten die Erweiterung auf virtuellen Computern deinstallieren, die an einer FCI beteiligt sind. Die von der Erweiterung unterstützten Features sind nach der Deinstallation des Agents nicht für die SQL-VMs verfügbar.

## <a name="installation"></a>Installation
Die Erweiterung für SQL Server-IaaS-Agent wird automatisch installiert, wenn Sie eines der Katalogimages für virtuelle SQL Server-Computer bereitstellen. Die SQL-IaaS-Erweiterung ermöglicht für eine einzelne Instanz in der SQL Server-VM eine bessere Verwaltbarkeit. Wenn es eine Standardinstanz gibt, arbeitet die Erweiterung mit der Standardinstanz und unterstützt nicht die Verwaltung anderer Instanzen. Wenn es keine Standardinstanz, sondern nur eine benannte Instanz gibt, wird die benannte Instanz verwaltet. Wenn es keine Standardinstanz und mehrere benannte Instanzen gibt, kann die Erweiterung nicht installiert werden. 



Falls Sie die Erweiterung auf einer dieser SQL Server-VMs manuell neu installieren müssen, verwenden Sie den folgenden PowerShell-Befehl:

```powershell
Set-AzVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SqlIaasExtension" -Version "2.0" -Location "East US 2"
```

> [!WARNING]
> Wenn die Erweiterung noch nicht installiert ist, wird der SQL Server-Dienst durch die Installation der Erweiterung neu gestartet. Ein Aktualisieren die SQL-IaaS-Erweiterung bewirkt jedoch nicht, dass der SQL Server-Dienst neu gestartet wird. 

> [!NOTE]
> Obwohl es möglich ist, die IaaS-Agent-Erweiterung für SQL Server in benutzerdefinierten SQL Server-Images zu installieren, ist die Funktionalität derzeit auf das [Ändern des Lizenztyps](virtual-machines-windows-sql-ahb.md) beschränkt. Andere von der IaaS-Erweiterung für SQL Server bereitgestellte Features funktionieren nur in [SQL Server-VM-Katalogimages](virtual-machines-windows-sql-server-iaas-overview.md#get-started-with-sql-vms) (mit nutzungsbasierter Bezahlung oder Bring-Your-Own-License).

### <a name="use-a-single-named-instance"></a>Verwenden einer einzelnen benannten Instanz
Die IaaS-Erweiterung für SQL Server funktioniert mit einer benannten Instanz in einem SQL Server-Image, wenn die Standardinstanz ordnungsgemäß deinstalliert und die IaaS-Erweiterung neu installiert wird.

Um eine benannte Instanz von SQL Server zu verwenden, gehen Sie folgendermaßen vor:
   1. Stellen Sie eine SQL Server-VM aus Marketplace bereit. 
   1. Deinstallieren Sie die IaaS-Erweiterung im [Azure-Portal](https://portal.azure.com).
   1. Deinstallieren Sie SQL Server in der SQL Server-VM vollständig.
   1. Installieren Sie SQL Server mit einer benannten Instanz in der SQL Server-VM. 
   1. Installieren Sie die IaaS-Erweiterung im Azure-Portal.  

## <a name="status"></a>Status
Eine Möglichkeit, zu überprüfen, ob die Erweiterung installiert ist, ist das Anzeigen des Agent-Status im Azure-Portal. Wählen Sie im Fenster des virtuellen Computers die Option **Alle Einstellungen** aus, und klicken Sie dann auf **Erweiterungen**. Die Erweiterung **SqlIaasExtension** sollte aufgeführt sein.

![Erweiterung für SQL Server-IaaS-Agent im Azure-Portal](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-portal.png)

Sie können auch das Azure PowerShell-Cmdlet **Get-AzVMSqlServerExtension** verwenden.

    Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"

Mit dem Befehl oben wird überprüft, ob der Agent installiert ist. Zudem werden allgemeine Statusinformationen angegeben. Mit den folgenden Befehlen können Sie zudem bestimmte Statusinformationen zur automatischen Sicherung und zum automatischen Patchen abrufen.

    $sqlext = Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
    $sqlext.AutoPatchingSettings
    $sqlext.AutoBackupSettings

## <a name="removal"></a>Entfernen
Im Azure-Portal können Sie die Erweiterung deinstallieren, indem Sie im Fenster **Erweiterungen** in den Eigenschaften des virtuellen Computers auf die Auslassungspunkte klicken. Klicken Sie dann auf **Löschen**.

![Deinstallieren der Erweiterung für SQL Server-IaaS-Agent im Azure-Portal](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-uninstall.png)

Sie können auch das PowerShell-Cmdlet **Remove-AzVMSqlServerExtension** verwenden.

    Remove-AzVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SqlIaasExtension"

## <a name="next-steps"></a>Nächste Schritte
Verwenden Sie einen der von der Erweiterung unterstützten Dienste. Weitere Informationen finden Sie in den im Abschnitt [Unterstützte Dienste](#supported-services) in diesem Artikel aufgeführten Artikeln.

Ausführlichere Informationen zur Verwendung von SQL Server auf virtuellen Azure-Computern finden Sie unter [Übersicht zu SQL Server auf virtuellen Azure-Computern](virtual-machines-windows-sql-server-iaas-overview.md).

