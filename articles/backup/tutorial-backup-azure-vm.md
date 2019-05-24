---
title: Sichern mehrerer virtueller Azure-Computer mit PowerShell
description: In diesem Tutorial wird erläutert, wie Sie mithilfe von Azure PowerShell mehrere virtuelle Azure-Computer in einem Recovery Services-Tresor sichern.
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: tutorial
ms.date: 03/05/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 2a35435f56a4bb09a8a1958fbc175ef7c889c380
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66127666"
---
# <a name="back-up-azure-vms-with-powershell"></a>Sichern virtueller Azure-Computer mit PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

In diesem Tutorial wird beschrieben, wie Sie mithilfe von PowerShell einen [Azure Backup](backup-overview.md) Recovery Services-Tresor zum Sichern mehrerer virtueller Azure-Computer bereitstellen.  

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen eines Recovery Services-Tresors und Einrichten des Tresorkontexts.
> * Definieren einer Sicherungsrichtlinie
> * Anwenden der Sicherungsrichtlinie zum Schützen mehrerer virtueller Computer
> * Auslösen eines bedarfsgesteuerten Sicherungsauftrags für die geschützten virtuellen Computer. Bevor Sie einen virtuellen Computer (Virtual Machine, VM) sichern (oder schützen) können, müssen Sie die [Voraussetzungen](backup-azure-arm-vms-prepare.md) schaffen, um Ihre Umgebung auf den Schutz Ihrer VMs vorzubereiten. 

> [!IMPORTANT]
> In diesem Tutorial wird davon ausgegangen, dass Sie bereits eine Ressourcengruppe und einen virtuellen Azure-Computer erstellt haben.


## <a name="log-in-and-register"></a>Anmeldung und Registrierung


1. Melden Sie sich mit dem Befehl `Connect-AzAccount` bei Ihrem Azure-Abonnement an, und befolgen Sie die Anweisungen auf dem Bildschirm.

    ```powershell
    Connect-AzAccount
    ```
2. Bei der ersten Verwendung von Azure Backup müssen Sie den Azure Recovery Service-Anbieter in Ihrem Abonnement mit [Register-AzResourceProvider](/powershell/module/az.Resources/Register-azResourceProvider) registrieren. Wenn Sie die Registrierung bereits vorgenommen haben, überspringen Sie diesen Schritt.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```


## <a name="create-a-recovery-services-vault"></a>Erstellen eines Recovery Services-Tresors

Ein [Recovery Services-Tresor](backup-azure-recovery-services-vault-overview.md) ist ein logischer Container, in dem Sicherungsdaten für geschützte Ressourcen wie Azure-VMs gespeichert werden. Beim Ausführen eines Sicherungsauftrags wird im Recovery Services-Tresor ein Wiederherstellungspunkt erstellt. Sie können einen dieser Wiederherstellungspunkte dann verwenden, um Daten für einen bestimmten Zeitpunkt wiederherzustellen.


- In diesem Tutorial erstellen Sie den Tresor in der Ressourcengruppe und an dem Speicherort, die/den Sie auch für den zu sichernden virtuellen Computer verwendet haben.
- Azure Backup übernimmt automatisch die Speicherung der gesicherten Daten. Der Tresor verwendet standardmäßig den [georedundanten Speicher (GRS)](../storage/common/storage-redundancy-grs.md). Durch Georedundanz wird sichergestellt, dass die gesicherten Daten in einer sekundären Azure-Region repliziert werden, die Hunderte von Kilometern von der primären Region entfernt ist.

Erstellen Sie den Tresor wie folgt:

1. Verwenden Sie das Cmdlet [New-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/new-azrecoveryservicesvault), um den Tresor zu erstellen. Geben Sie den Ressourcengruppennamen und den Speicherort des zu sichernden virtuellen Computers an.

    ```powershell
    New-AzRecoveryServicesVault -Name myRSvault -ResourceGroupName "myResourceGroup" -Location "EastUS"
    ```
2. Viele Azure Backup-Cmdlets benötigen das Recovery Services-Tresorobjekt als Eingabe. Aus diesem Grund sollte das zur Sicherung verwendete Recovery Services-Tresorobjekt in einer Variablen gespeichert werden.

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault –Name myRSVault
    ```
    
3. Legen Sie den Tresorkontext mit [Set-AzRecoveryServicesVaultContext](/powershell/module/az.RecoveryServices/Set-azRecoveryServicesVaultContext) fest.

   - Der Tresorkontext ist der Datentyp, der im Tresor geschützt wird.
   - Der festgelegte Kontext gilt für alle nachfolgenden Cmdlets.

     ```powershell
     Get-AzRecoveryServicesVault -Name "myRSVault" | Set-AzRecoveryServicesVaultContext
     ```

## <a name="back-up-azure-vms"></a>Sichern von Azure-VMs

Sicherungen werden gemäß dem in der Sicherungsrichtlinie angegebenen Zeitplan ausgeführt. Wenn Sie einen Recovery Services-Tresor erstellen, enthält er automatisch standardmäßige Schutz- und Aufbewahrungsrichtlinien.

- Die Standardschutzrichtlinie löst täglich zu einem angegebenen Zeitpunkt einen Sicherungsauftrag aus.
- Die standardmäßige Aufbewahrungsrichtlinie bewahrt den täglichen Wiederherstellungspunkt für 30 Tage auf. 

In diesem Tutorial führen Sie zum Aktivieren und Sichern des virtuellen Azure-Computers die folgenden Schritte aus:

1. Geben Sie mit [Get-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-Azrecoveryservicesbackupcontainer) einen Container im Tresor an, der Ihre Sicherungsdaten enthält.
2. Bei jedem für die Sicherung vorgesehenen virtuellen Computer handelt es sich um ein Element. Zum Starten eines Sicherungsauftrags rufen Sie mit [Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem) Informationen zum virtuellen Computer ab.
3. Führen Sie mit dem Cmdlet [Backup-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-Azrecoveryservicesbackupitem) eine Ad-hoc-Sicherung aus. 
    - Beim ersten Sicherungsauftrag wird ein vollständiger Wiederherstellungspunkt erstellt.
    - Bei jedem Sicherungsauftrag nach der ersten Sicherung werden inkrementelle Wiederherstellungspunkte erstellt.
    - Inkrementelle Wiederherstellungspunkte sind in Bezug auf die Speicherung und die Dauer effizient, da nur Änderungen übertragen werden, die seit der letzten Sicherung vorgenommen wurden.

Sie können die Sicherung wie folgt aktivieren und ausführen:

```powershell
$namedContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVM -Status Registered -FriendlyName "V2VM"
$item = Get-AzRecoveryServicesBackupItem -Container $namedContainer -WorkloadType AzureVM
$job = Backup-AzRecoveryServicesBackupItem -Item $item
```

## <a name="troubleshooting"></a>Problembehandlung 

Wenn beim Sichern des virtuellen Computers Probleme auftreten, lesen Sie den Artikel [Problembehandlung bei der Sicherung virtueller Azure-Computer](backup-azure-vms-troubleshoot.md).

### <a name="deleting-a-recovery-services-vault"></a>Löschen eines Recovery Services-Tresors

Wenn Sie einen Tresor löschen müssen, löschen Sie zuerst die Wiederherstellungspunkte im Tresor, und heben Sie dann die Registrierung im Tresor wie folgt auf:


```powershell
$Cont = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVM -Status Registered
$PI = Get-AzRecoveryServicesBackupItem -Container $Cont[0] -WorkloadType AzureVm
Disable-AzRecoveryServicesBackupProtection -RemoveRecoveryPoints $PI[0]
Unregister-AzRecoveryServicesBackupContainer -Container $namedContainer
Remove-AzRecoveryServicesVault -Vault $vault1
```

## <a name="next-steps"></a>Nächste Schritte

- [Befassen Sie sich](backup-azure-vms-automation.md) mit einer detaillierteren exemplarischen Vorgehensweise für das Sichern und Wiederherstellen von virtuellen Azure-Computern mit PowerShell. 
- [Verwalten und Überwachen von Azure-VMs](backup-azure-manage-vms.md)
- [Wiederherstellen virtueller Azure-Computer](backup-azure-arm-restore-vms.md)
