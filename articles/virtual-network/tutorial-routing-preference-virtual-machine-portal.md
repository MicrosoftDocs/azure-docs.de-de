---
title: 'Konfigurieren der Routingpräferenz für einen virtuellen Computer: Azure-Portal'
description: Hier erfahren Sie, wie Sie mithilfe des Azure-Portals einen virtuellen Computer mit öffentlicher IP-Adresse und Routingpräferenz erstellen.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/02/2020
ms.author: mnayak
ms.openlocfilehash: 35e4b90e9bdd52a20bf892f0784b191384f271ac
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96533705"
---
# <a name="configure-routing-preference-for-a-vm-using-the-azure-portal"></a>Konfigurieren der Routingpräferenz für einen virtuellen Computer mithilfe des Azure-Portals

In diesem Artikel erfahren Sie, wie Sie die Routingpräferenz für einen virtuellen Computer konfigurieren. Der Internetdatenverkehr vom virtuellen Computer wird über das ISP-Netzwerk geleitet, wenn Sie **Internet** als Option für die Routingpräferenz auswählen. Das Standardrouting erfolgt über das globale Netzwerk von Microsoft.

In diesem Artikel wird gezeigt, wie Sie einen virtuellen Computer mit einer öffentlichen IP-Adresse erstellen, die zum Weiterleiten von Datenverkehr über das öffentliche Internet unter Verwendung des Azure-Portals festgelegt ist.

> [!IMPORTANT]
> „Routingpräferenz“ ist zurzeit als öffentliche Vorschauversion verfügbar.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Melden Sie sich beim [Azure-Portal](https://preview.portal.azure.com/) an.

## <a name="create-a-virtual-machine"></a>Erstellen eines virtuellen Computers

1. Klicken Sie im Azure-Portal links oben auf **+ Ressource erstellen**.
2. Wählen Sie **Compute** und dann **Windows Server 2016-VM** oder ein anderes Betriebssystem Ihrer Wahl aus.
3. Geben Sie die folgenden Informationen ein, oder wählen Sie sie aus, übernehmen Sie die Standardwerte für die übrigen Einstellungen, und klicken Sie auf **OK**:

    |Einstellung|Wert|
    |---|---|
    |Name|myVM|
    |Benutzername| Geben Sie den gewünschten Benutzernamen ein.|
    |Kennwort| Geben Sie das gewünschte Kennwort ein. Das Kennwort muss mindestens zwölf Zeichen lang sein und die [definierten Anforderungen an die Komplexität](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm) erfüllen.|
    |Subscription| Wählen Sie Ihr Abonnement aus.|
    |Resource group| Wählen Sie **Vorhandene verwenden** und dann **myResourceGroup** aus.|
    |Standort| Wählen Sie **USA, Osten** aus.|

4. Wählen Sie eine Größe für den virtuellen Computer aus, und klicken Sie dann auf **Auswählen**.
5. Klicken Sie unter der Registerkarte **Netzwerk** auf **Neu erstellen** für **Öffentliche IP-Adresse**.
6. Geben Sie *myPublicIpAddress* ein, wählen Sie „sku“ als **Standard** und dann die Routingpräferenz vom Typ **Internet** aus, und drücken Sie dann **OK**, wie in der folgenden Abbildung gezeigt:

   ![Auswählen der Option „Statisch“](./media/tutorial-routing-preference-virtual-machine-portal/routing-preference-internet-new.png)

6. Wählen Sie unter **Öffentliche eingehende Ports hinzufügen** einen Port oder keine Ports aus. Port 3389 ist ausgewählt, um den Remotezugriff auf den virtuellen Windows Server-Computer über das Internet zu aktivieren. Bei Produktionsworkloads wird vom Öffnen von Port 3389 über das Internet abgeraten.

   ![Auswählen eines Ports](./media/tutorial-routing-preference-virtual-machine-portal/pip-ports-new.png)

7. Übernehmen Sie die restlichen Standardeinstellungen, und wählen Sie **OK** aus.
8. Klicken Sie auf der Seite **Zusammenfassung** auf **Erstellen**. Das Bereitstellen des virtuellen Computers dauert einige Minuten.
9. Nachdem der virtuelle Computer bereitgestellt wurde, geben Sie in das Suchfeld am oberen Rand des Portals *myPublicIpAddress* ein. Wenn **myPublicIpAddress** in den Suchergebnissen angezeigt wird, wählen Sie diese Angabe aus.
10. Die zugewiesene öffentliche IP-Adresse und die Adresse, die dem virtuellen Computer **myVM** zugewiesen ist, werden angezeigt, wie in der folgenden Abbildung gezeigt wird:

    ![Screenshot der öffentlichen IP-Adresse der NIC für die Netzwerkschnittstelle mynic.](./media/tutorial-routing-preference-virtual-machine-portal/pip-properties-new.png)

11. Wählen Sie **Netzwerk** aus, klicken Sie dann auf die NIC **mynic**, und wählen Sie dann die öffentliche IP-Adresse aus, um zu bestätigen, dass die Routingpräferenz vom Typ **Internet** zugewiesen ist.

    ![Screenshot der öffentlichen IP-Adresse und Routingeinstellung für eine öffentliche IP-Adresse.](./media/tutorial-routing-preference-virtual-machine-portal/pip-routing-internet-new.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Löschen Sie die Ressourcengruppe mit allen ihren Ressourcen, wenn Sie sie nicht mehr benötigen:

1. Geben Sie im oben im Portal im Feld *Suche* die Zeichenfolge **myResourceGroup** ein. Wenn **myResourceGroup** in den Suchergebnissen angezeigt wird, wählen Sie diese Angabe aus.
2. Wählen Sie die Option **Ressourcengruppe löschen**.
3. Geben Sie für **Geben Sie den Ressourcengruppennamen ein:** den Namen *myResourceGroup* ein, und klicken Sie auf **Löschen**.

## <a name="next-steps"></a>Nächste Schritte
- Erfahren Sie mehr über [öffentliche IP-Adressen mit Routingpräferenz](routing-preference-overview.md).
- Lesen Sie mehr über [öffentliche IP-Adressen](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) in Azure.
- Erfahren Sie mehr über alle [Einstellungen für öffentliche IP-Adressen](virtual-network-public-ip-address.md#create-a-public-ip-address).
