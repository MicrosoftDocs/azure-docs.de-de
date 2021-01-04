---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/27/2020
ms.author: alkohli
ms.openlocfilehash: efa9e996cbfbc8954c294b4da8900b1d5bcbeeed
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96465465"
---
Zum Überprüfen des virtuellen privaten Netzwerks (VPN) können Sie ein Speicherkonto erstellen, auf das nur über das von Ihnen erstellte virtuelle Netzwerk zugegriffen werden kann. Befolgen Sie diese Schritte, um dieses Speicherkonto zu erstellen und mit dem von Ihnen erstellten virtuellen Netzwerk zu verknüpfen:

1. Erstellen Sie ein Speicherkonto. Sie können das Speicherkonto verwenden, das Sie mit Ihrem Azure Stack Edge-Gerät verwenden möchten. Versuchen Sie, von einem externen Netzwerk (außerhalb des ausgewählten Netzwerks) auf das Speicherkonto zuzugreifen. Das Speicherkonto sollte zugänglich sein.
2. Wechseln Sie im Azure-Portal zum Speicherkonto. 
3. Wechseln Sie zu **Firewalls und virtuelle Netzwerke**. Wählen Sie im rechten Bereich für **Zugriff erlauben von** die Option **Ausgewählte Netzwerke** aus. Wählen Sie unter **Schützen unseres Speicherkontos mit virtuellen Netzwerken** die Option **+ Vorhandenes virtuelles Netzwerk hinzufügen** aus.

    ![Überprüfen von VPN 1](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/verify-vpn-1.png)

4. Auf dem Blatt **Netzwerke hinzufügen**:

    - Wählen Sie das Abonnement aus. Dies ist dasselbe Abonnement, das mit Ihrer Azure Stack Edge-/Data Box Gateway-Ressource verknüpft ist. 
    - Wählen Sie in der Dropdownliste das virtuelle Netzwerk aus, das mit diesem Speicherkonto verknüpft werden soll. Wählen Sie das virtuelle Netzwerk aus, das Sie im vorherigen Schritt erstellt haben.
    - Wählen Sie in **Subnetze** die Optionen **_Standard_* _ und _GatewaySubnet* aus. Die Dienstendpunkte werden für diese Kombinationen von virtuellem Netzwerk und Subnetz aktiviert. Die Aktivierung des Zugriffs dauert bis zu 15 Minuten.
    - Wählen Sie **Aktivieren** aus.

    ![Überprüfen von VPN 2](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/verify-vpn-2.png)
    
4. Speichern Sie die **Einstellungen**.

    ![Überprüfen von VPN 3](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/verify-vpn-3.png)

5. Nachdem die Einstellungen gespeichert wurden, können Sie die Subnetze sehen, für die das virtuelle Netzwerk aktiviert ist.

    ![Überprüfen von VPN 4](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/verify-vpn-4.png)

5. Führen Sie die folgenden Schritte aus, um zu überprüfen, ob die Daten jetzt nur über das VPN übertragen werden: 
    - Versuchen Sie, von einem externen Netzwerk (außerhalb des ausgewählten Netzwerks) auf das Speicherkonto zuzugreifen. Das Speicherkonto sollte nicht zugänglich sein. 
    - Versuchen Sie, auf das Speicherkonto von virtuellen Netzwerken/Subnetzen aus zuzugreifen, die Sie in ausgewählten Netzwerken aktiviert haben. Das Speicherkonto sollte zugänglich sein. 
 
Sie können nur dann auf dieses Speicherkonto zugreifen, wenn Sie das VPN aktiviert haben. Wenn Sie VPN deaktivieren, müssen Sie auch die Netzwerkeinstellungen des Speicherkontos anpassen. 

Weitere Informationen finden Sie unter [Konfigurieren von Firewalls und virtuellen Netzwerken in Azure Storage](../articles/storage/common/storage-network-security.md). 

