---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 01/15/2021
ms.author: alkohli
ms.openlocfilehash: 8f3031669723cd61715c12a42f99869ac0eaf3bc
ms.sourcegitcommit: 445ecb22233b75a829d0fcf1c9501ada2a4bdfa3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99500490"
---
Bevor Sie virtuelle Computer auf Ihrem Azure Stack Edge-Gerät bereitstellen können, müssen Sie den Client so konfigurieren, dass er über Azure Resource Manager über Azure PowerShell eine Verbindung mit dem Gerät herstellt. Ausführliche Schritte finden Sie unter [Herstellen einer Verbindung mit Azure Resource Manager auf Ihrem Azure Stack Edge-Gerät](../articles/databox-online/azure-stack-edge-j-series-connect-resource-manager.md).

Stellen Sie sicher, dass Sie mit den folgenden Schritten von Ihrem Client aus auf das Gerät zugreifen können. (Sie haben diese Konfiguration beim Herstellen einer Verbindung mit Azure Resource Manager vorgenommen. Jetzt überprüfen Sie lediglich, ob die Konfiguration erfolgreich war.) 

1. Überprüfen Sie, ob die Azure Resource Manager-Kommunikation funktioniert. Geben Sie Folgendes ein:     

    ```powershell
    Add-AzureRmEnvironment -Name <Environment Name> -ARMEndpoint "https://management.<appliance name>.<DNSDomain>"
    ```

1. Lokale Geräte-APIs zum Authentifizieren aufrufen. Geben Sie Folgendes ein: 

    `login-AzureRMAccount -EnvironmentName <Environment Name>`

    Benutzername bereitstellen – *EdgeARMuser* und das Kennwort für die Verbindung über Azure Resource Manager.

1. Wenn Sie **Compute** für Kubernetes konfiguriert haben, können Sie diesen Schritt überspringen. Stellen Sie sicher, dass Sie eine Netzwerkschnittstelle für Compute aktiviert haben. Wechseln Sie auf der lokalen Benutzeroberfläche zu den Einstellungen für **Compute**. Wählen Sie die Netzwerkschnittstelle aus, die Sie verwenden möchten, um einen virtuellen Switch zu erstellen. Die von Ihnen erstellten virtuellen Computer werden an einen virtuellen Switch angefügt, der an diesen Port und das zugehörige Netzwerk angefügt ist. Achten Sie darauf, ein Netzwerk auszuwählen, das mit der IP-Adresse übereinstimmt, die Sie für den virtuellen Computer verwenden werden.  

    ![Screenshot der Aktivierung von Computeeinstellungen](../articles/databox-online/media/azure-stack-edge-gpu-deploy-virtual-machine-templates/enable-compute-setting.png)

    Aktivieren Sie Compute für die Netzwerkschnittstelle. Azure Stack Edge erstellt und verwaltet einen virtuellen Switch, der dieser Netzwerkschnittstelle entspricht. Geben Sie zu diesem Zeitpunkt keine bestimmten IP-Adressen für Kubernetes ein. Die Aktivierung von Compute kann mehrere Minuten dauern.

    > [!NOTE]
    > Wählen Sie beim Erstellen von GPU-VMs eine Netzwerkschnittstelle aus, die mit dem Internet verbunden ist. Auf diese Weise können Sie die GPU-Erweiterung auf Ihrem Gerät installieren.


