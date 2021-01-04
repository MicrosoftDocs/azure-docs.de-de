---
author: cherylmc
ms.date: 12/06/2019
ms.service: expressroute
ms.topic: include
ms.author: cherylmc
ms.openlocfilehash: f04861d55c9cea3c79f4983f7be2e1f5a3c6d864
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96018155"
---
Installieren Sie die aktuellen Versionen der PowerShell-Module für die Azure-Dienstverwaltung und das ExpressRoute-Modul. Sie können die Azure Cloud Shell-Umgebung nicht zum Ausführen von SM-Modulen verwenden.

1. Befolgen Sie die Anweisungen im Artikel [Installieren des Azure PowerShell-Dienstverwaltungsmoduls](/powershell/azure/servicemanagement/install-azure-ps), um das Azure-Dienstverwaltungsmodul zu installieren. Wenn Sie das Az- oder RM-Modul bereits installiert haben, achten Sie darauf, „-AllowClobber“ zu verwenden.
2. Importieren Sie die installierten Module. Wenn Sie das folgende Beispiel verwenden, passen Sie den Pfad an, um den Speicherort und die Version der installierten PowerShell-Module widerzuspiegeln.

   ```powershell
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.3.0\Azure.psd1'
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.3.0\ExpressRoute\ExpressRoute.psd1'
   ```
3. Melden Sie sich bei Ihrem Azure-Konto an, öffnen Sie die PowerShell-Konsole mit erhöhten Rechten, und stellen Sie eine Verbindung mit Ihrem Konto her. Verwenden Sie das folgende Beispiel, um eine Verbindung mit dem Dienstverwaltungsmodul herzustellen:

   ```powershell
   Add-AzureAccount
   ```