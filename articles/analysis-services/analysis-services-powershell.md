---
title: Verwalten von Azure Analysis Services mit PowerShell | Microsoft-Dokumentation
description: Verwaltung von Azure Analysis Services mit PowerShell
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: reference
ms.date: 12/19/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 1f9c30f1c914f6c8d42967e014d967ba0d5b85cc
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66142297"
---
# <a name="manage-azure-analysis-services-with-powershell"></a>Verwalten von Azure Analysis Services mit PowerShell

Dieser Artikel beschreibt PowerShell-Cmdlets, die zum Ausführen von Azure Analysis Services-Verwaltungsaufgaben für Server und Datenbanken verwendet werden. 

Für Serververwaltungsaufgaben wie das Erstellen oder Löschen eines Servers, das Anhalten oder Fortsetzen von Servervorgängen oder das Ändern des Servicelevels (des Tarifs) werden Azure Resource Manager-Cmdlets (Ressourcen-Cmdlets) und Analysis Services-Cmdlets (Server-Cmdlets) verwendet. Für andere Aufgaben zum Verwalten von Datenbanken, z.B. Hinzufügen oder Entfernen von Rollenmitgliedern, Verarbeiten oder Partitionieren, werden die im gleichen SqlServer-Modul wie SQL Server Analysis Services enthaltenen Cmdlets verwendet.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="permissions"></a>Berechtigungen

Die meisten PowerShell-Aufgaben erfordern, dass Sie über Administratorberechtigungen auf dem verwalteten Analysis Services-Server verfügen. Geplante PowerShell-Aufgaben sind unbeaufsichtigte Vorgänge. Das Konto oder der Dienstprinzipal, auf dem der Scheduler ausgeführt wird, muss auf dem Analysis Services-Server über Administratorrechte verfügen. 

Bei Servervorgängen, für die Azure PowerShell-Cmdlets verwendet werden, muss Ihr Konto oder das Konto mit dem Scheduler außerdem zur Rolle „Besitzer“ für die Ressource in der [rollenbasierten Zugriffssteuerung in Azure](../role-based-access-control/overview.md) gehören. 

## <a name="resource-management-operations"></a>Ressourcenverwaltungsvorgänge 

Modul: [Az.AnalysisServices](/powershell/module/az.analysisservices)

|Cmdlet|BESCHREIBUNG| 
|------------|-----------------| 
|[Get-AzAnalysisServicesServer](/powershell/module/az.analysisservices/get-azanalysisservicesserver)|Ruft die Details einer Serverinstanz ab.|  
|[New-AzAnalysisServicesServer](/powershell/module/az.analysisservices/new-azanalysisservicesserver)|Erstellt eine Serverinstanz.|   
|[New-AzAnalysisServicesFirewallConfig](/powershell/module/az.analysisservices/new-azanalysisservicesfirewallconfig)|Erstellt eine neue Analysis Services-Firewallkonfiguration.|   
|[New-AzAnalysisServicesFirewallRule](/powershell/module/az.analysisservices/new-azanalysisservicesfirewallrule)|Erstellt eine neue Analysis Services-Firewallregel.|   
|[Remove-AzAnalysisServicesServer](/powershell/module/az.analysisservices/remove-azanalysisservicesserver)|Entfernt eine Serverinstanz.|  
|[Resume-AzAnalysisServicesServer](/powershell/module/az.analysisservices/resume-azanalysisservicesserver)|Setzt eine Serverinstanz fort.|  
|[Suspend-AzAnalysisServicesServer](/powershell/module/az.analysisservices/suspend-azanalysisservicesserver)|Hält eine Serverinstanz an.| 
|[Set-AzAnalysisServicesServer](/powershell/module/az.analysisservices/set-azanalysisservicesserver)|Ändert eine Serverinstanz.|   
|[Test-AzAnalysisServicesServer](/powershell/module/az.analysisservices/test-azanalysisservicesserver)|Überprüft das Vorhandensein einer Serverinstanz.| 

## <a name="server-management-operations"></a>Serververwaltungsvorgänge

Modul – [Azure.AnalysisServices](https://www.powershellgallery.com/packages/Azure.AnalysisServices)

|Cmdlet|BESCHREIBUNG| 
|------------|-----------------| 
|[Add-AzAnalysisServicesAccount](/powershell/module/az.analysisservices/add-AzAnalysisServicesaccount)|Fügt ein authentifiziertes Konto hinzu, das für Cmdlet-Anforderungen von Azure Analysis Services-Servern verwendet werden soll.| 
|[Export-AzAnalysisServicesInstance](/powershell/module/az.analysisservices/export-AzAnalysisServicesinstancelog)|Exportiert ein Protokoll aus einer Instanz von Analysis Services-Server in der aktuell angemeldeten Umgebung gemäß der Angabe im Befehl „Add-AzAnalysisServicesAccount“.|  
|[Restart-AzAnalysisServicesInstance](/powershell/module/az.analysisservices/restart-AzAnalysisServicesinstance)|Startet eine Instanz von Analysis Services-Server in der derzeit angemeldeten Umgebung neu, wird im Befehl Add-AzAnalysisServicesAccount angegeben.|  
|[Sync-AzAnalysisServicesInstance](/powershell/module/az.analysisservices/restart-AzAnalysisServicesinstance)|Synchronisiert eine angegebene Datenbank für die angegebene Instanz von Analysis Services-Server für alle Abfrageinstanzen mit horizontaler Skalierung in der aktuell angemeldeten Umgebung gemäß der Angabe im Befehl „Add-AzAnalysisServicesAccount“.|  

## <a name="database-operations"></a>Datenbankvorgänge

Für Vorgänge der Azure Analysis Services-Datenbank wird das gleiche [SqlServer-Modul](https://www.powershellgallery.com/packages/SqlServer) wie bei SQL Server Analysis Services verwendet. Allerdings werden nicht alle Cmdlets für Azure Analysis Services unterstützt. Weitere Informationen finden Sie unter [SQL Server PowerShell](https://docs.microsoft.com/sql/powershell/sql-server-powershell).

Das SqlServer-Modul bietet aufgabenspezifische Cmdlets für die Datenbankverwaltung sowie das allgemeine Cmdlet Invoke-ASCmd, das TMSL-Abfragen (Tabular Model Scripting Language) und -Skripts akzeptiert. Die folgenden Cmdlets im SqlServer-Modul werden von Azure Analysis Services unterstützt.

  
|Cmdlet|BESCHREIBUNG|
|------------|-----------------| 
|[Add-RoleMember](https://docs.microsoft.com/powershell/module/sqlserver/Add-RoleMember)|Hinzufügen eines Mitglieds zu einer Datenbankrolle.| 
|[Backup-ASDatabase](https://docs.microsoft.com/powershell/module/sqlserver/backup-asdatabase)|Sichern einer Analysis Services-Datenbank.|  
|[Remove-RoleMember](https://docs.microsoft.com/powershell/module/sqlserver/remove-rolemember)|Entfernen eines Mitglieds aus einer Datenbankrolle.|   
|[Invoke-ASCmd](https://docs.microsoft.com/powershell/module/sqlserver/invoke-ascmd)|Ausführen eines TMSL-Skripts.|
|[Invoke-ProcessASDatabase](https://docs.microsoft.com/powershell/module/sqlserver/invoke-processasdatabase)|Verarbeiten einer Datenbank.|  
|[Invoke-ProcessPartition](https://docs.microsoft.com/powershell/module/sqlserver/invoke-processpartition)|Verarbeiten einer Partition.| 
|[Invoke-ProcessTable](https://docs.microsoft.com/powershell/module/sqlserver/invoke-processtable)|Verarbeiten einer Tabelle.|  
|[Merge-Partition](https://docs.microsoft.com/powershell/module/sqlserver/merge-partition)|Zusammenführen einer Partition.|  
|[Restore-ASDatabase](https://docs.microsoft.com/powershell/module/sqlserver/restore-asdatabase)|Wiederherstellen einer Analysis Services-Datenbank.| 
  

## <a name="related-information"></a>Verwandte Informationen

* [Herunterladen des SQL Server PowerShell-Moduls](https://docs.microsoft.com/sql/ssms/download-sql-server-ps-module)   
* [Herunterladen von SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)   
* [SqlServer-Modul im PowerShell-Katalog](https://www.powershellgallery.com/packages/SqlServer)    
* [Programmierung von tabellarischen Modellen für den Kompatibilitätsgrad 1200 und höher](/sql/analysis-services/tabular-model-programming-compatibility-level-1200/tabular-model-programming-for-compatibility-level-1200)
