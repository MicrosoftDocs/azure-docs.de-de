---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: aebb82690a7a49aba071ed64349d37d516208cca
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178552"
---
Um Ihr Gerät zurückzusetzen, müssen Sie alle Daten vom Daten- und Startdatenträger Ihres Geräts sicher löschen. 

Verwenden Sie das Cmdlet `Reset-HcsAppliance`, um sowohl die Datenträger als auch den Startdatenträger oder nur die Datenträger zu löschen. Mit den Schaltern `ClearData` und `BootDisk` können Sie die Datenträger bzw. den Startdatenträger löschen.

Wenn Sie auf der lokalen Webbenutzeroberfläche das Gerät zurücksetzen, werden nur die Datenträger sicher gelöscht, aber der Startdatenträger bleibt intakt. Der Startdatenträger enthält die Gerätekonfiguration.

1. [Herstellen einer Verbindung mit der PowerShell-Schnittstelle](#connect-to-the-powershell-interface).
2. Geben Sie an der Eingabeaufforderung Folgendes ein:

    `Reset-HcsAppliance -ClearData -BootDisk`

    Das folgende Beispiel zeigt die Verwendung dieses Cmdlets:
    ```
    [10.128.24.33]: PS>Reset-HcsAppliance -ClearData -BootDisk
    
    Confirm
    Are you sure you want to perform this action?
    Performing the operation "Reset-HcsAppliance" on target "ShouldProcess appliance".
    [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [?] Help (default is "Y"): N
    ```
