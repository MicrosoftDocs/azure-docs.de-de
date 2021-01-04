---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: c1b56cfb85595b8a17dc18f69a0b162d504c04ec
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96025954"
---
Um Ihr Gerät zurückzusetzen, müssen Sie alle Daten vom Daten- und Startdatenträger Ihres Geräts sicher löschen. 

Verwenden Sie das Cmdlet `Reset-HcsAppliance`, um sowohl die Datenträger als auch den Startdatenträger oder nur die Datenträger zu löschen. Mit den Schaltern `ClearData` und `BootDisk` können Sie die Datenträger bzw. den Startdatenträger löschen.

Der Schalter `BootDisk` setzt den Startdatenträger zurück und macht das Gerät unbrauchbar. Er sollte nur verwendet werden, wenn das Gerät an Microsoft zurückgegeben werden muss. Weitere Informationen finden Sie unter [Zurückgeben oder Ersetzen Ihres Azure Data Box Edge-Geräts](../articles/databox-online/azure-stack-edge-return-device.md).

Wenn Sie auf der lokalen Webbenutzeroberfläche das Gerät zurücksetzen, werden nur die Datenträger sicher gelöscht, aber der Startdatenträger bleibt intakt. Der Startdatenträger enthält die Gerätekonfiguration.

1. [Herstellen einer Verbindung mit der PowerShell-Schnittstelle](#connect-to-the-powershell-interface).
2. Geben Sie an der Eingabeaufforderung Folgendes ein:

    `Reset-HcsAppliance -ClearData -BootDisk`

    Das folgende Beispiel zeigt die Verwendung dieses Cmdlets:

    ```powershell
    [10.128.24.33]: PS>Reset-HcsAppliance -ClearData -BootDisk

    Confirm
    Are you sure you want to perform this action?
    Performing the operation "Reset-HcsAppliance" on target "ShouldProcess appliance".
    [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [?] Help (default is "Y"): N
    ```