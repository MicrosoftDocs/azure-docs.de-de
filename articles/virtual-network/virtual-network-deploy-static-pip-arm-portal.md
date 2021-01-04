---
title: Erstellen eines virtuellen Computers mit einer statischen öffentlichen IP-Adresse (Azure-Portal)
description: Erfahren Sie, wie Sie über das Azure-Portal einen virtuellen Computer mit einer statischen öffentlichen IP-Adresse erstellen.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: ''
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/12/2020
ms.author: allensu
ms.openlocfilehash: 1ae0b869b24c4e05c88b936eceb1b9b1db3a9405
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2020
ms.locfileid: "94506310"
---
# <a name="create-a-virtual-machine-with-a-static-public-ip-address-using-the-azure-portal"></a>Erstellen eines virtuellen Computers mit einer statischen öffentlichen IP-Adresse mithilfe des Azure-Portals

Mit einer öffentlichen IP-Adresse können Sie über das Internet mit einer VM kommunizieren. 

Um sicherzustellen, dass sich die Adresse niemals ändert, weisen Sie anstelle einer dynamischen Adresse eine statische öffentliche IP-Adresse zu.   

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

## <a name="create-a-virtual-machine"></a>Erstellen eines virtuellen Computers

1. Wählen Sie oben links im Portal die Option **Ressource erstellen** > **Compute** > **Virtueller Computer** aus, oder suchen Sie über das Suchfeld nach **Virtueller Computer**.
   
2. Geben Sie unter **Virtuellen Computer erstellen** auf der Registerkarte **Grundlagen** die folgenden Werte ein, oder wählen Sie sie aus:

    | Einstellung | Wert                                          |
    |-----------------------|----------------------------------|
    | **Projektdetails** |  |
    | Subscription | Auswählen des Azure-Abonnements |
    | Ressourcengruppe | Wählen Sie **Neu erstellen**. </br> Geben Sie **myResourceGroup** unter **Name** ein. </br> Klicken Sie auf **OK**. |
    | **Instanzendetails** |  |
    | Name des virtuellen Computers | Geben Sie **myVM** ein. |
    | Region | Wählen Sie **USA, Osten** aus. |
    | Verfügbarkeitsoptionen | Wählen Sie **Keine Infrastrukturredundanz erforderlich** aus. |
    | Image | Wählen Sie **Windows Server 2019 Datacenter – Gen1** aus. |
    | Azure Spot-Instanz | Wählen Sie **Nein** aus. |
    | Size | Wählen Sie eine VM-Größe aus, oder übernehmen Sie die Standardeinstellung. |
    | **Administratorkonto** |  |
    | Username | Geben Sie einen Benutzernamen ein. |
    | Kennwort | Geben Sie ein Kennwort ein. |
    | Kennwort bestätigen | Geben Sie das Kennwort erneut ein. |

3. Wählen Sie die Registerkarte **Netzwerk** aus, oder wählen Sie **Weiter: Datenträger** und anschließend **Weiter: Netzwerk** aus.
  
4. Wählen Sie auf der Registerkarte „Netzwerk“ die folgenden Werte aus, oder geben Sie sie ein:

    | Einstellung | Wert |
    |-|-|
    | **Netzwerkschnittstelle** |  |
    | Virtuelles Netzwerk | Übernehmen Sie den Standardnetzwerknamen. |
    | Subnet | Übernehmen Sie die Standard-Subnetzkonfiguration. |
    | Öffentliche IP-Adresse | Wählen Sie **Neu erstellen**. </br> Geben Sie unter **Öffentliche IP-Adresse erstellen** als Namen **myPublicIP** ein. </br> Wählen Sie unter **SKU** die Option **Standard** aus. </br> Wählen Sie unter **Zuweisung** die Option **Statisch** aus. </br> Klicken Sie auf **OK**.  |
    | NIC-Netzwerksicherheitsgruppe | Wählen Sie **Basic** aus.|
    | Öffentliche Eingangsports | Wählen Sie **Ausgewählte Ports zulassen** aus. |
    | Eingangsports auswählen | Wählen Sie **RDP (3389)** aus. |

    > [!WARNING]
    > Port 3389 ist ausgewählt, um den Remotezugriff auf den virtuellen Windows Server-Computer über das Internet zu aktivieren. Beim Verwalten von Produktionsworkloads wird vom Öffnen von Port 3389 für das Internet abgeraten. </br> Informationen zum sicheren Zugriff auf virtuelle Azure-Computer finden Sie unter **[Was ist Azure Bastion?](/azure/bastion/bastion-overview)**
   
5. Klicken Sie auf **Überprüfen + erstellen**. 
  
6. Überprüfen Sie die Einstellungen, und wählen Sie dann die Option **Erstellen**.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Löschen Sie die Ressourcengruppe mit allen ihren Ressourcen, wenn Sie sie nicht mehr benötigen:

1. Geben Sie im oben im Portal im Feld **Suche** die Zeichenfolge **myResourceGroup** ein. Wenn **myResourceGroup** in den Suchergebnissen angezeigt wird, wählen Sie diese Angabe aus.
2. Wählen Sie die Option **Ressourcengruppe löschen**.
3. Geben Sie für **Geben Sie den Ressourcengruppennamen ein:** den Namen **myResourceGroup** ein, und klicken Sie auf **Löschen**.

## <a name="next-steps"></a>Nächste Schritte

Unter [Hinzufügen, Ändern oder Entfernen von IP-Adressen](virtual-network-network-interface-addresses.md) finden Sie Informationen zu Folgendem:

* Ändern einer öffentlichen IP-Adresse von „dynamisch“ in „statisch“
* Arbeiten mit privaten IP-Adressen

Für öffentliche IP-Adressen fällt eine [geringe Gebühr](https://azure.microsoft.com/pricing/details/ip-addresses) an. Die Anzahl der öffentlichen IP-Adressen, die pro Abonnement verwendet werden können, ist [begrenzt](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

Die SKU der öffentlichen IP-Adresse des virtuellen Computers muss beim Hinzufügen zu einem Back-End-Pool mit der SKU der öffentlichen IP-Adresse von Azure Load Balancer übereinstimmen. Weitere Informationen finden Sie unter [Azure Load Balancer](../load-balancer/skus.md).

Sie können die Liste von Bereichen (Präfixen) für die [öffentliche Azure-Cloud](https://www.microsoft.com/download/details.aspx?id=56519), die [Azure US Government-Cloud](https://www.microsoft.com/download/details.aspx?id=57063) sowie für die Azure-Cloud in [China](https://www.microsoft.com/download/details.aspx?id=57062) und [Deutschland](https://www.microsoft.com/download/details.aspx?id=57064) herunterladen.

- Lesen Sie im folgenden Artikel mehr über [statische öffentliche IP-Adressen](virtual-network-ip-addresses-overview-arm.md#allocation-method).
- Lesen Sie mehr über [öffentliche IP-Adressen](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) in Azure.
- Erfahren Sie mehr über alle [Einstellungen für öffentliche IP-Adressen](virtual-network-public-ip-address.md#create-a-public-ip-address).
- Lesen Sie mehr über [private IP-Adressen](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) und das Zuweisen einer [statischen privaten IP-Adresse](virtual-network-network-interface-addresses.md#add-ip-addresses) zu einer Azure-VM.
