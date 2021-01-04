---
title: Netzwerkisolation in Azure DevTest Labs
description: Hier erfahren Sie mehr über die Netzwerkisolation in Azure DevTest Labs.
ms.topic: article
ms.date: 08/25/2020
ms.openlocfilehash: 28bfa048a8a6666deb58a8ecfff38779c91d95b8
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96352931"
---
# <a name="network-isolation-in-devtest-labs"></a>Netzwerkisolation in DevTest Labs

Ein [virtuelles Azure-Netzwerk](../virtual-network/virtual-networks-overview.md) fungiert als Sicherheitsgrenze und schirmt Ihre Azure-Ressourcen vom öffentlichen Internet ab. Sie können ein virtuelles Azure-Netzwerk auch mit Ihrem lokalen Netzwerk verknüpfen, um eine sichere Verbindung mit Ihren lokalen Ressourcen herstellen zu können. In DevTest Labs können Sie [alle virtuellen Labcomputer](devtest-lab-configure-vnet.md) und [Umgebungen in Ihrem Netzwerk isolieren](connect-environment-lab-virtual-network.md), um sicherzustellen, dass die Labressourcen die Netzwerkrichtlinien Ihrer Organisation einhalten. 

Als Labbesitzer können Sie das Lab auch vollständig isolieren. Das bedeutet, dass Sie nicht nur virtuelle Computer und Umgebungen im ausgewählten Netzwerk, sondern auch die in Ihrem Abonnement erstellten Lab-Speicherkonten und Schlüsseltresore isolieren können. In diesem Artikel wird die Erstellung eines isolierten Netzwerklabs Schritt für Schritt beschrieben. 

Lesen Sie auch die folgenden Artikel:

- [Verschlüsseln eines von einem Lab verwendeten Azure-Speichers in Azure DevTest Labs](encrypt-storage.md)
- [Speichern von Geheimnissen in einem Schlüsseltresor in Azure DevTest Labs](devtest-lab-store-secrets-in-key-vault.md)
 
> [!NOTE]
> Die Netzwerkisolation wird derzeit nur für die Erstellung neuer Labs unterstützt.

## <a name="steps-to-enable-network-isolation-during-lab-creation"></a>Schritte zum Aktivieren der Netzwerkisolation während der Erstellung von Labs

1. Wechseln Sie während der Lab-Erstellung zur Registerkarte **Netzwerk**.
1. Sie können entweder ein Netzwerk vom Typ **Standard** auswählen, das vom Lab für Sie erstellt wird, oder im Dropdownmenü ein vorhandenes Netzwerk auswählen. Sie können nur Netzwerke auswählen, die sich in der gleichen Region und im gleichen Abonnement wie das Lab befinden. 

    > [!div class="mx-imgBorder"]
    > ![Labs erstellen](./media/network-isolation/create-lab.png)
1. Wählen Sie ein Subnetz aus.

    > [!div class="mx-imgBorder"]
    > ![Erstellen eines Subnetzes](./media/network-isolation/create-lab-subnet.png)
1. Wenn Sie Labressourcen (Lab-Speicherkonto und Schlüsseltresor) im Standardnetzwerk isolieren, sind keine weiteren Aktionen erforderlich, und das Lab verarbeitet zukünftig die Isolation von Ressourcen.
 
    > [!div class="mx-imgBorder"]
    > ![Netzwerkisolation](./media/network-isolation/isolate-lab-resources.png)
1. Wenn Sie Labressourcen (Lab-Speicherkonto und Schlüsseltresor) in einem ausgewählten vorhandenen Netzwerk isolieren, müssen Sie nach der Erstellung des Labs die folgenden Schritte ausführen, um sicherzustellen, dass das Lab im isolierten Modus weiterhin funktioniert. 
 
    > [!div class="mx-imgBorder"]
    > ![Isolieren von Ressourcen](./media/network-isolation/isolate-my-vnet.png)

    > [!IMPORTANT]
    > Der Labbesitzer muss diese Schritte ausführen, bevor er Ressourcen im Lab konfiguriert und/oder erstellt.

### <a name="steps-to-follow-post-lab-creation"></a>Schritte nach der Lab-Erstellung

1. Wählen Sie auf der Homepage für das Lab die **Ressourcengruppe** auf der Seite **Übersicht** aus. Die Seite **Ressourcengruppe** für die Ressourcengruppe, in der das Lab enthalten ist, sollte angezeigt werden. 
 
   > [!div class="mx-imgBorder"]
   > ![Contoso-Lab](./media/network-isolation/contoso-lab.png)
1. Wählen Sie das Azure-Speicherkonto des Labs aus. Die Namenskonvention für das Lab-Speicherkonto lautet: a<*LabnameOhneUngültigeZeichen*> *<vierstellige Zahl*>. Wenn der Name des Labs beispielsweise „contosolab“ ist, könnte der Name des Speicherkontos „acontosolab1234“ lauten.
 
   > [!div class="mx-imgBorder"]
   > ![Contoso-Test](./media/network-isolation/contoso-test.png)
1. Navigieren Sie im Speicherkonto zu „Firewalls und virtuelle Netzwerke“, und vergewissern Sie sich, dass das Kontrollkästchen „Vertrauenswürdigen Microsoft-Diensten den Zugriff auf dieses Speicherkonto erlauben“ aktiviert ist. Da [DevTest Labs ein vertrauenswürdiger Microsoft-Dienst](../storage/common/storage-network-security.md#trusted-microsoft-services) ist, ermöglicht diese Option die normale Nutzung des Labs im netzwerkisolierten Modus. 

   > [!div class="mx-imgBorder"]
   > ![Contoso-Labfirewalls](./media/network-isolation/contoso-lab-firewalls-vnets.png)
1. Klicken Sie als Nächstes auf **+ Vorhandenes virtuelles Netzwerk hinzufügen**, wählen Sie das virtuelle Netzwerk und das Subnetz aus, die Sie beim Erstellen des Labs ausgewählt haben, und klicken Sie auf **Aktivieren**. 

   > [!div class="mx-imgBorder"]
   > ![Contoso: my-vnet](./media/network-isolation/contoso-lab-my-vnet.png)
5.  Nachdem der Dienstendpunkt für das ausgewählte virtuelle Netzwerk aktiviert wurde, klicken Sie auf **Hinzufügen**. 

   > [!div class="mx-imgBorder"]
   > ![Add (Hinzufügen)](./media/network-isolation/contoso-firewall-add.png)
 
Dadurch lässt Azure Storage eingehende Verbindungen aus dem hinzugefügten virtuellen Netzwerk zu und ermöglicht die ordnungsgemäße Nutzung des Labs im netzwerkisolierten Modus. 

Sie können diese Schritte auch automatisieren, um diese Einstellung für mehrere Labs zu konfigurieren. 

[Weitere Informationen zum Verwalten standardmäßiger Netzwerkzugriffsregeln für Azure Storage mithilfe von PowerShell und der CLI](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json#powershell)

## <a name="things-to-remember-while-using-a-lab-in-a-network-isolated-mode"></a>Wichtige Aspekte bei der Verwendung eines Labs im netzwerkisolierten Modus

### <a name="accessing-labs-storage-account-outside-the-lab"></a>Zugreifen auf das Lab-Speicherkonto außerhalb des Labs 

In einem netzwerkisolierten Lab müssen Labbesitzer explizit den Zugriff auf das Speicherkonto über einen zulässigen Endpunkt ermöglichen, damit Aktionen wie das Hochladen einer VHD in das Lab-Speicherkonto zum Erstellen benutzerdefinierter Images ausgeführt werden können. Hierfür können Sie einen virtuellen Computer erstellen und über diesen sicher auf das Lab-Speicherkonto zugreifen. 

[Weitere Informationen zum privaten Zugriff auf ein Speicherkonto über einen virtuellen Computer](../private-link/tutorial-private-endpoint-storage-portal.md)

### <a name="exporting-usage-data-from-the-lab"></a>Exportieren von Nutzungsdaten aus dem Lab 

Zum [Exportieren persönlicher Nutzungsdaten für das Lab](personal-data-delete-export.md) muss der Labbesitzer in einem netzwerkisolierten Lab explizit ein Speicherkonto bereitstellen und ein Blob innerhalb des Kontos generieren, um die Daten zu speichern. 

Wird kein Speicherkonto bereitgestellt, tritt bei diesem Vorgang im netzwerkisolierten Modus ein Fehler auf, da das Lab nicht auf das Lab-Speicherkonto zugreifen kann, sollte vom Kunden kein Speicherkonto bereitgestellt worden sein. 

[Weitere Informationen zum Exportieren der Labnutzungsdaten in einem bestimmten Speicherkonto](personal-data-delete-export.md#azure-powershell)

## <a name="next-steps"></a>Nächste Schritte

[Automatisches Erstellen oder Ändern von Labs mit Azure Resource Manager-Vorlagen und PowerShell](devtest-lab-use-arm-and-powershell-for-lab-resources.md)