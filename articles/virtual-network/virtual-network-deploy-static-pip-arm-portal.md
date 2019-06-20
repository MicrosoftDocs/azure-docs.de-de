---
title: Erstellen eines virtuellen Computers mit einer statischen öffentlichen IP-Adresse – Azure-Portal | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie über das Azure-Portal einen virtuellen Computer mit einer statischen öffentlichen IP-Adresse erstellen.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: e9546bcc-f300-428f-b94a-056c5bd29035
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/08/2018
ms.author: kumud
ms.openlocfilehash: f6914a9894db07a40b372a8c247a7623c3957d86
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64692423"
---
# <a name="create-a-virtual-machine-with-a-static-public-ip-address-using-the-azure-portal"></a>Erstellen eines virtuellen Computers mit einer statischen öffentlichen IP-Adresse mithilfe des Azure-Portals

Sie können einen virtuellen Computer mit einer statischen öffentlichen IP-Adresse erstellen. Mit einer öffentlichen IP-Adresse können Sie über das Internet mit einer VM kommunizieren. Um sicherzustellen, dass sich die Adresse niemals ändert, weisen Sie anstelle einer dynamischen Adresse eine statische öffentliche IP-Adresse zu. Lesen Sie im folgenden Artikel mehr über [statische öffentliche IP-Adressen](virtual-network-ip-addresses-overview-arm.md#allocation-method). Um eine öffentliche dynamische IP-Adresse, die einem vorhandenen virtuellen Computer zugewiesen ist, in eine statische Adresse zu ändern oder mit privaten IP-Adressen zu arbeiten, lesen Sie [Hinzufügen, Ändern oder Entfernen von IP-Adressen](virtual-network-network-interface-addresses.md). Für öffentliche IP-Adressen fällt eine [Schutzgebühr](https://azure.microsoft.com/pricing/details/ip-addresses) an, und für die Anzahl der öffentlichen IP-Adressen, die Sie pro Abonnement verwenden können, ist ein [Grenzwert](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) festgelegt.

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Melden Sie sich unter https://portal.azure.com beim Azure-Portal an.

## <a name="create-a-virtual-machine"></a>Erstellen eines virtuellen Computers

1. Klicken Sie im Azure-Portal links oben auf **+ Ressource erstellen**.
2. Wählen Sie **Compute** und dann **Windows Server 2016-VM** oder ein anderes Betriebssystem Ihrer Wahl aus.
3. Geben Sie die folgenden Informationen ein, oder wählen Sie sie aus, übernehmen Sie die Standardwerte für die übrigen Einstellungen, und klicken Sie auf **OK**:

    |Einstellung|Wert|
    |---|---|
    |NAME|myVM|
    |Benutzername| Geben Sie den gewünschten Benutzernamen ein.|
    |Kennwort| Geben Sie das gewünschte Kennwort ein. Das Kennwort muss mindestens zwölf Zeichen lang sein und die [definierten Anforderungen an die Komplexität](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm) erfüllen.|
    |Abonnement| Wählen Sie Ihr Abonnement aus.|
    |Ressourcengruppe| Wählen Sie **Vorhandene verwenden** und dann **myResourceGroup** aus.|
    |Location| Wählen Sie **USA, Osten** aus.|

4. Wählen Sie eine Größe für den virtuellen Computer aus, und klicken Sie dann auf **Auswählen**.
5. Wählen Sie unter **Einstellungen** die Option **Öffentliche IP-Adresse** aus.
6. Geben Sie *myPublicIpAddress* ein, wählen Sie **Statisch** aus, und klicken Sie dann auf **OK**, wie in der folgenden Abbildung gezeigt wird:

   ![Auswählen der Option „Statisch“](./media/virtual-network-deploy-static-pip-arm-portal/select-static.png)

   Wenn die öffentliche IP-Adresse die SKU „Standard“ aufweisen muss, wählen Sie unter **SKU** die Option **Standard** aus. Weitere Informationen zu SKUs für öffentliche IP-Adressen finden Sie [hier](virtual-network-ip-addresses-overview-arm.md#sku). Wenn die VM dem Back-End-Pool einer öffentlichen Azure Load Balancer-Instanz hinzugefügt wird, muss die SKU der öffentlichen IP-Adresse der VM der SKU der öffentlichen IP-Adresse des Lastenausgleichsmoduls entsprechen. Weitere Informationen finden Sie im Artikel zu [Azure Load Balancer](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#skus).

6. Wählen Sie unter **Öffentliche eingehende Ports hinzufügen** einen Port oder keine Ports aus. Port 3389 ist ausgewählt, um den Remotezugriff auf den virtuellen Windows Server-Computer über das Internet zu aktivieren. Bei Produktionsworkloads wird vom Öffnen von Port 3389 über das Internet abgeraten.

   ![Auswählen eines Ports](./media/virtual-network-deploy-static-pip-arm-portal/select-port.png)

7. Übernehmen Sie die restlichen Standardeinstellungen, und wählen Sie **OK** aus.
8. Klicken Sie auf der Seite **Zusammenfassung** auf **Erstellen**. Das Bereitstellen des virtuellen Computers dauert einige Minuten.
9. Nachdem der virtuelle Computer bereitgestellt wurde, geben Sie in das Suchfeld am oberen Rand des Portals *myPublicIpAddress* ein. Wenn **myPublicIpAddress** in den Suchergebnissen angezeigt wird, wählen Sie diese Angabe aus.
10. Die zugewiesene öffentliche IP-Adresse und die Adresse, die dem virtuellen Computer **myVM** zugewiesen ist, werden angezeigt, wie in der folgenden Abbildung gezeigt wird:

    ![Anzeigen einer öffentlichen IP-Adresse](./media/virtual-network-deploy-static-pip-arm-portal/public-ip-overview.png)

    Azure hat eine öffentliche IP-Adresse über Adressen in zugewiesen, die in der Region, in der der virtuelle Computer erstellt wurde, verwendet werden. Sie können die Liste von Bereichen (Präfixen) für die [öffentliche Azure-Cloud](https://www.microsoft.com/download/details.aspx?id=56519), die [Azure US Government-Cloud](https://www.microsoft.com/download/details.aspx?id=57063) sowie für die Azure-Cloud in [China](https://www.microsoft.com/download/details.aspx?id=57062) und [Deutschland](https://www.microsoft.com/download/details.aspx?id=57064) herunterladen.

11. Wählen Sie **Konfiguration** aus, um zu bestätigen, dass die Zuweisung **Statisch** erfolgen soll.

    ![Anzeigen einer öffentlichen IP-Adresse](./media/virtual-network-deploy-static-pip-arm-portal/public-ip-configuration.png)

> [!WARNING]
> Ändern Sie die IP-Adresseinstellungen nicht innerhalb des Betriebssystems des virtuellen Computers. Öffentliche Azure-IP-Adressen sind dem Betriebssystem nicht bekannt. Auch wenn Sie dem Betriebssystem Einstellungen für private IP-Adressen hinzufügen können, wird empfohlen, dies erst nach Lesen des Artikels [Hinzufügen einer privaten IP-Adresse zu einem Betriebssystem](virtual-network-network-interface-addresses.md#private) und bei absoluter Notwendigkeit zu tun.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Löschen Sie die Ressourcengruppe mit allen ihren Ressourcen, wenn Sie sie nicht mehr benötigen:

1. Geben Sie im oben im Portal im Feld *Suche* die Zeichenfolge **myResourceGroup** ein. Wenn **myResourceGroup** in den Suchergebnissen angezeigt wird, wählen Sie diese Angabe aus.
2. Wählen Sie die Option **Ressourcengruppe löschen**.
3. Geben Sie für **Geben Sie den Ressourcengruppennamen ein:** den Namen *myResourceGroup* ein, und klicken Sie auf **Löschen**.

## <a name="next-steps"></a>Nächste Schritte

- Lesen Sie mehr über [öffentliche IP-Adressen](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) in Azure.
- Erfahren Sie mehr über [Einstellungen für öffentliche IP-Adressen](virtual-network-public-ip-address.md#create-a-public-ip-address).
- Lesen Sie mehr über [private IP-Adressen](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) und das Zuweisen einer [statischen privaten IP-Adresse](virtual-network-network-interface-addresses.md#add-ip-addresses) zu einer Azure-VM.
- Erfahren Sie mehr über das Erstellen von VMs unter [Linux](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) und [Windows](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json).