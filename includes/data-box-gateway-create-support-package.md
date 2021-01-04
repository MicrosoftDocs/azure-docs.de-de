---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/15/2020
ms.author: alkohli
ms.openlocfilehash: f002fec531a0355e803a1545990fc0b13b535742
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96581046"
---
Wenn Sie Geräteprobleme haben, können Sie anhand der Systemprotokolle ein Protokollpaket erstellen. Der Microsoft-Support nutzt dieses Paket, um die Probleme zu beheben. Um ein Protokollpaket erstellen, gehen Sie wie folgt vor:

1. [Stellen Sie auf Ihrem Gerät eine Verbindung mit der PowerShell-Schnittstelle her](#connect-to-the-powershell-interface).
2. Verwenden Sie den Befehl `Get-HcsNodeSupportPackage`, um ein Protokollpaket zu erstellen. Die Nutzung des Cmdlets ist wie folgt:

    ```powershell
    Get-HcsNodeSupportPackage [-Path] <string> [-Zip] [-ZipFileName <string>] [-Include {None | RegistryKeys | EtwLogs
            | PeriodicEtwLogs | LogFiles | DumpLog | Platform | FullDumps | MiniDumps | ClusterManagementLog | ClusterLog |
            UpdateLogs | CbsLogs | StorageCmdlets | ClusterCmdlets | ConfigurationCmdlets | KernelDump | RollbackLogs |
            Symbols | NetworkCmdlets | NetworkCmds | Fltmc | ClusterStorageLogs | UTElement | UTFlag | SmbWmiProvider |
            TimeCmds | LocalUILogs | ClusterHealthLogs | BcdeditCommand | BitLockerCommand | DirStats | ComputeRolesLogs |
            ComputeCmdlets | DeviceGuard | Manifests | MeasuredBootLogs | Stats | PeriodicStatLogs | MigrationLogs |
            RollbackSupportPackage | ArchivedLogs | Default}] [-MinimumTimestamp <datetime>] [-MaximumTimestamp <datetime>]
            [-IncludeArchived] [-IncludePeriodicStats] [-Credential <pscredential>]  [<CommonParameters>]
    ```

    Das Cmdlet erfasst Protokolle von Ihrem Gerät und kopiert diese in eine angegebene Netzwerk- oder lokale Freigabe.

    Die verwendeten Parameter sind wie folgt:

    - `-Path`: Geben Sie den Netzwerk- oder lokalen Pfad an, in den das Protokollpaket kopiert werden soll. (erforderlich)
    - `-Credential`: Geben Sie die Anmeldeinformationen für den Zugriff auf den geschützten Pfad an.
    - `-Zip`: Dient zum Angeben, dass eine ZIP-Datei generiert werden soll.
    - `-Include`: Dient zum Angeben der Komponenten, die in das Protokollpaket einbezogen werden sollen. Falls nicht angegeben, `Default` wird angenommen.
    - `-IncludeArchived`: Dient zum Angeben, dass archivierte Protokolle in das Protokollpaket einbezogen werden sollen.
    - `-IncludePeriodicStats`: Dient zum Angeben, dass regelmäßige Statistikprotokolle in das Protokollpaket einbezogen werden sollen.

    
