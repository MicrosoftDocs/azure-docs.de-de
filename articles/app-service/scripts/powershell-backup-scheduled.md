---
title: Azure PowerShell-Skriptbeispiel – Erstellen einer geplanten Sicherung für eine Web-App | Microsoft-Dokumentation
description: Azure PowerShell-Skriptbeispiel – Erstellen einer geplanten Sicherung für eine Web-App
services: app-service\web
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: a2a27d94-d378-4c17-a6a9-ae1e69dc4a72
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: sample
ms.date: 10/30/2017
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: acb17386a2c2e0b2a89f46c607befb56eadaee48
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66136592"
---
# <a name="create-a-scheduled-backup-for-a-web-app-using-powershell"></a>Erstellen einer geplanten Sicherung für eine Web-App mit PowerShell

Dieses Beispielskript erstellt in App Service eine Web-App mit den zugehörigen Ressourcen und dann eine geplante Sicherung dafür. 

Installieren Sie bei Bedarf Azure PowerShell anhand der Anleitung im [Azure PowerShell-Handbuch](/powershell/azure/overview), und führen Sie dann `Connect-AzAccount` aus, um eine Verbindung mit Azure herzustellen. 

## <a name="sample-script"></a>Beispielskript

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/backup-scheduled/backup-scheduled.ps1?highlight=1-4 "Create a scheduled backup for a web app")]

## <a name="clean-up-deployment"></a>Bereinigen der Bereitstellung 

Nach dem Ausführen des Skriptbeispiels können mit dem folgenden Befehl die Ressourcengruppe, die Web-App und alle zugehörigen Ressourcen entfernt werden.

```powershell
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>Erläuterung des Skripts

Das Skript verwendet die folgenden Befehle. Jeder Befehl in der Tabelle ist mit der zugehörigen Dokumentation verknüpft.

| Get-Help | Notizen |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Erstellt eine Ressourcengruppe, in der alle Ressourcen gespeichert sind. |
| [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) | Erstellt ein Speicherkonto. |
| [New-AzStorageContainer](/powershell/module/az.storage/new-AzStoragecontainer) | Erstellt einen Azure-Speichercontainer. |
| [New-AzStorageContainerSASToken](/powershell/module/az.storage/new-AzStoragecontainersastoken) | Generiert ein SAS-Token für einen Azure-Speichercontainer. |
| [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) | Erstellt einen App Service-Plan. |
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | Erstellt die Web-App. |
| [Edit-AzWebAppBackupConfiguration](/powershell/module/az.websites/edit-azwebappbackupconfiguration) | Bearbeitet die Sicherungskonfiguration für die Web-App. |
| [Get-AzWebAppBackupList](/powershell/module/az.websites/get-azwebappbackuplist) | Ruft eine Liste der Sicherungen für eine Web-App ab. |
| [Get-AzWebAppBackupConfiguration](/powershell/module/az.websites/get-azwebappbackupconfiguration) | Ruft die Sicherungskonfiguration für eine Web-App ab. |

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Azure PowerShell-Modul finden Sie in der [Azure PowerShell-Dokumentation](/powershell/azure/overview).

Zusätzliche Azure PowerShell-Beispiele für Azure App Service-Web-Apps finden Sie unter [Azure PowerShell-Beispiele](../samples-powershell.md).
