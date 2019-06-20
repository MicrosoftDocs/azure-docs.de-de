---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: 926fb3e9a2c09d30da549330842d8b7e185674ae
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178620"
---
Geben Sie unter **Add vCenter** (vCenter hinzufügen) einen Anzeigenamen für den vSphere-Host oder den vCenter-Server an, und geben Sie anschließend die IP-Adresse oder den FQDN des Servers an. Belassen Sie den Port als Port 443, es sei denn Ihre VMware-Server sind so konfiguriert, dass sie auf Anforderungen auf einem anderen Port lauschen können. Wählen Sie das Konto aus, unter dem die Verbindung mit dem VMware vCenter- oder vSphere ESXi-Server hergestellt werden soll. Klicken Sie auf **OK**.

    ![VMware](./media/site-recovery-add-vcenter/vmware-server.png)

   > [!NOTE]
   > Wenn Sie den VMware vCenter-Server oder den VMware vSphere-Host mit einem Konto hinzufügen, das auf dem vCenter- oder Hostserver keine Administratorrechte besitzt, müssen Sie sicherstellen, dass für das Konto folgende Berechtigungen aktiviert sind: Rechenzentrum, Datenspeicher, Ordner, Host, Netzwerk, Ressource, Virtueller Computer, vSphere Distributed Switch. Außerdem benötigt der VMware vCenter-Server die Anzeigeberechtigung für den Speicher.
