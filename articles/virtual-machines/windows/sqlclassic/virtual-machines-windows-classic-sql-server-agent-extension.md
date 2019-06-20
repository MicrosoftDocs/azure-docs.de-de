---
title: Automatisieren der Verwaltungsaufgaben unter virtuellen SQL-Computern (Klassisch) | Microsoft-Dokumentation
description: In diesem Thema wird die Verwaltung der SQL Server-Agent-Erweiterung beschrieben, mit der bestimmte SQL Server-Verwaltungsaufgaben automatisiert werden. Dazu gehören die automatische Sicherung, das automatische Patchen und die Azure-Schlüsseltresor-Integration. In diesem Thema wird der klassische Bereitstellungsmodus verwendet.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: a9bda2e7-cdba-427c-bc30-77cde4376f3a
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 07/12/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 2b719185aabd39cd70b9cb890a9599aa06ca4ff4
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60334824"
---
# <a name="automate-management-tasks-on-azure-virtual-machines-with-the-sql-server-agent-extension-classic"></a>Automatisieren von Verwaltungsaufgaben auf Azure Virtual Machines mit der SQL Server-Agent-Erweiterung (klassisch)
> [!div class="op_single_selector"]
> * [Ressourcen-Manager](../sql/virtual-machines-windows-sql-server-agent-extension.md)
> * [Klassisch](../classic/sql-server-agent-extension.md)
> 
>
 
Die Erweiterung für SQL Server-IaaS-Agent (SQLIaaSAgent) wird auf virtuellen Azure-Computern zum Automatisieren von Verwaltungsaufgaben ausgeführt. Dieses Thema bietet eine Übersicht über die Dienste, die von der Erweiterung unterstützt werden, sowie Anweisungen zu Installation, Status und Deinstallation.

> [!IMPORTANT] 
> Azure verfügt über zwei verschiedene Bereitstellungsmodelle für das Erstellen und Verwenden von Ressourcen: [Resource Manager-Bereitstellungen und klassische Bereitstellungen](../../../azure-resource-manager/resource-manager-deployment-model.md). Dieser Artikel befasst sich mit der Verwendung des klassischen Bereitstellungsmodells. Microsoft empfiehlt für die meisten neuen Bereitstellungen die Verwendung des Ressourcen-Manager-Modells. Die Resource Manager-Version dieses Artikels finden Sie unter [SQL Server-Agent-Erweiterung für virtuelle SQL Server-Computer (Resource Manager)](../sql/virtual-machines-windows-sql-server-agent-extension.md).

## <a name="supported-services"></a>Unterstützte Dienste
Die Erweiterung für SQL Server-IaaS-Agent unterstützt die folgenden Verwaltungsaufgaben:

| Verwaltungsfeature | BESCHREIBUNG |
| --- | --- |
| **SQL – Automatisierte Sicherung** |Dieser Dienst automatisiert die Planung von Sicherungen für alle Datenbanken für die Standardinstanz von SQL Server auf dem virtuellen Computer. Weitere Informationen finden Sie unter [Automatisierte Sicherung für SQL Server auf virtuellen Azure-Computern (klassisch)](../classic/sql-automated-backup.md). |
| **SQL – Automatisiertes Patchen** |Mit diesem Dienst können Sie ein Wartungsfenster konfigurieren, in dem wichtige Windows-Updates für den virtuellen Computer ausgeführt werden. Auf diese Weise werden Updates für Ihre Workload während der Spitzenzeiten vermieden. Weitere Informationen finden Sie unter [Automatisiertes Patchen für SQL Server auf virtuellen Azure-Computern (klassisch)](../classic/sql-automated-patching.md). |
| **Azure-Schlüsseltresor-Integration** |Mit diesem Dienst können Sie Azure Key Vault auf Ihrem virtuellen SQL Server-Computer automatisch installieren und konfigurieren. Weitere Informationen finden Sie unter [Konfigurieren der Azure-Schlüsseltresor-Integration für SQL Server auf virtuellen Azure-Computern (klassisch)](../classic/ps-sql-keyvault.md). |

## <a name="prerequisites"></a>Voraussetzungen
Anforderungen für die Verwendung der Erweiterung für SQL Server-IaaS-Agent auf Ihrem virtuellen Computer:

### <a name="operating-system"></a>Betriebssystem:
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

### <a name="sql-server-versions"></a>SQL Server-Versionen:
* SQL Server 2012
* SQL Server 2014
* SQL Server 2016

### <a name="azure-powershell"></a>Azure PowerShell:
[Herunterladen und Konfigurieren der aktuellen Azure PowerShell-Befehle](/powershell/azure/overview)

Starten Sie Windows PowerShell, und verknüpfen Sie dann mithilfe des Befehls **Add-AzureAccount** PowerShell mit Ihrem Azure-Abonnement.

    Add-AzureAccount

Wenn Sie über mehrere Abonnements verfügen, verwenden Sie **Select-AzureSubscription** , um das Abonnement auszuwählen, das die klassische Ziel-VM enthält.

    Select-AzureSubscription -SubscriptionName <subscriptionname>

Zu diesem Zeitpunkt können Sie mit dem Befehl **Get-AzureVM** eine Liste der klassischen virtuellen Computer und der zugeordneten Dienstnamen abrufen.

    Get-AzureVM

## <a name="installation"></a>Installation
Bei klassischen virtuellen Computern müssen Sie PowerShell für die Installation der Erweiterung für SQL Server-IaaS-Agent verwenden und die zugehörigen Dienste konfigurieren. Verwenden Sie zum Installieren der Erweiterung das PowerShell-Cmdlet **Set-AzureVMSqlServerExtension** . Mit dem folgenden Befehl wird die Erweiterung beispielsweise auf einem virtuellen Windows Server-Computer (klassisch) erstellt und „SQLIaaSExtension“ genannt.

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -ReferenceName "SQLIaasExtension" -Version "1.2" | Update-AzureVM

Bei einer Aktualisierung auf die aktuelle Version der SQL-IaaS-Agent-Erweiterung müssen Sie den virtuellen Computer nach der Aktualisierung neu starten.

> [!NOTE]
> Auf klassischen virtuellen Computern ist keine Option zum Installieren und Konfigurieren der Erweiterung für SQL-IaaS-Agent über das Portal verfügbar.

> [!NOTE]
> Die SQL Server-IaaS-Agent-Erweiterung wird nur für [SQL Server-VM-Katalogimages](../sql/virtual-machines-windows-sql-server-iaas-overview.md#get-started-with-sql-vms) unterstützt (mit nutzungsbasierter Bezahlung oder Bring Your Own License). Sie wird nicht unterstützt, wenn Sie SQL Server manuell auf einem virtuellen Computer installieren, der ausschließlich unter dem Betriebssystem Windows-Server ausgeführt wird, oder wenn Sie eine benutzerdefinierte SQL Server-VM-VHD bereitstellen. In diesen Fällen können Sie die Erweiterung mit PowerShell möglicherweise manuell installieren und verwalten, es wird jedoch ausdrücklich empfohlen, stattdessen ein SQL Server-VM-Katalogimage zu installieren und dieses dann anzupassen.

## <a name="status"></a>Status
Eine Möglichkeit, zu überprüfen, ob die Erweiterung installiert ist, ist das Anzeigen des Agent-Status im Azure-Portal. Wählen Sie auf dem Blatt des virtuellen Computers einen virtuellen Computer aus, und klicken Sie dann auf **Erweiterungen**. Die Erweiterung **SQLIaaSAgent** sollte aufgeführt werden.

![Erweiterung für SQL Server-IaaS-Agent im Azure-Portal](./media/virtual-machines-windows-classic-sql-server-agent-extension/azure-sql-server-iaas-agent-portal.png)

Sie können auch das Azure PowerShell-Cmdlet **Get-AzureVMSqlServerExtension** verwenden.

    Get-AzureVM –ServiceName "service" –Name "vmname" | Get-AzureVMSqlServerExtension

## <a name="removal"></a>Entfernen
Im Azure-Portal können Sie die Erweiterung deinstallieren, indem Sie in den Eigenschaften des virtuellen Computers auf dem Blatt **Erweiterungen** auf die Auslassungspunkte klicken. Klicken Sie anschließend auf **Deinstallieren**.

![Deinstallieren der Erweiterung für SQL Server-IaaS-Agent im Azure-Portal](./media/virtual-machines-windows-classic-sql-server-agent-extension/azure-sql-server-iaas-agent-uninstall.png)

Sie können auch das PowerShell-Cmdlet **Remove-AzureVMSqlServerExtension** verwenden.

    Get-AzureVM –ServiceName "service" –Name "vmname" | Remove-AzureVMSqlServerExtension | Update-AzureVM

## <a name="next-steps"></a>Nächste Schritte
Verwenden Sie einen der von der Erweiterung unterstützten Dienste. Weitere Informationen finden Sie in den im Abschnitt [Unterstützte Dienste](#supported-services) in diesem Artikel aufgeführten Themen.

Ausführlichere Informationen zur Verwendung von SQL Server auf virtuellen Azure-Computern finden Sie unter [Übersicht zu SQL Server auf virtuellen Azure-Computern](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

