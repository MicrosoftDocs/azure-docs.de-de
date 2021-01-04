---
title: Öffnen von Ports für einen virtuellen Computer mithilfe des Azure-Portals
description: Hier erfahren Sie, wie Sie mithilfe des Azure-Portals für Ihre VM einen Port öffnen bzw. einen Endpunkt erstellen.
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 05/27/2020
ms.author: cynthn
ms.openlocfilehash: 1c3c39dd8c907f780876f97f9703857653d4e359
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92777757"
---
# <a name="how-to-open-ports-to-a-virtual-machine-with-the-azure-portal"></a>Öffnen von Ports zu einem virtuellen Computer mit dem Azure-Portal
[!INCLUDE [virtual-machines-common-nsg-quickstart](../../../includes/virtual-machines-common-nsg-quickstart.md)]


## <a name="sign-in-to-azure"></a>Anmelden bei Azure
Melden Sie sich unter https://portal.azure.com beim Azure-Portal an.

## <a name="create-a-network-security-group"></a>Erstellen einer Netzwerksicherheitsgruppe

1. Suchen Sie nach der Ressourcengruppe für den virtuellen Computer, und wählen Sie diese aus. Wählen Sie **Hinzufügen** aus, suchen Sie dann nach **Netzwerksicherheitsgruppe** , und wählen Sie diese Option aus.

1. Klicken Sie auf **Erstellen**.

    Das Fenster **Netzwerksicherheitsgruppe erstellen** wird geöffnet.

    ![Erstellen einer Netzwerksicherheitsgruppe](./media/nsg-quickstart-portal/create-nsg.png)

1. Geben Sie einen Namen für Ihre Netzwerksicherheitsgruppe ein. 

1. Wählen Sie eine Ressourcengruppe aus, oder erstellen Sie eine, und wählen Sie dann einen Speicherort aus.

1. Wählen Sie **Erstellen** aus, um die Netzwerksicherheitsgruppe zu erstellen.

## <a name="create-an-inbound-security-rule"></a>Erstellen einer Eingangssicherheitsregel

1. Wählen Sie Ihre neue Netzwerksicherheitsgruppe aus. 

1. Wählen Sie **Eingangssicherheitsregeln** im linken Menü und dann **Hinzufügen** aus.

    ![Wechsel zur Seite "Erweitert"](./media/nsg-quickstart-portal/advanced.png)

1. Wechseln Sie auf der Seite **Eingehende Sicherheitsregel hinzufügen** oben auf der Seite von **Grundlegend** zu **Erweitert**. 

1. Wählen Sie im Dropdownmenü einen allgemeinen **Dienst** , z.B. **HTTP** , aus. Sie können auch **Benutzerdefiniert** auswählen, wenn Sie einen bestimmten zu verwendenden Port angeben möchten. 

1. Ändern Sie optional die **Priorität** oder den **Namen**. Die Priorität wirkt sich auf die Reihenfolge aus, in der Regeln angewendet werden: je niedriger der numerische Wert, desto früher wird die Regel angewendet.

1. Wählen Sie **Hinzufügen** aus, um die Regel zu erstellen.

## <a name="associate-your-network-security-group-with-a-subnet"></a>Zuordnen Ihrer Netzwerksicherheitsgruppe zu einem Subnetz

Im letzten Schritt muss die Netzwerksicherheitsgruppe einem Subnetz oder einer bestimmten Netzwerkschnittstelle zugeordnet werden. In diesem Beispiel ordnen wir die Netzwerksicherheitsgruppe einem Subnetz zu. 

1. Wählen Sie im Menü auf der linken Seite **Subnetze** und dann **Zuordnen** aus.

1. Wählen Sie das virtuelle Netzwerk und dann das gewünschte Subnetz aus.

    ![Zuordnen einer Netzwerksicherheitsgruppe zu einem virtuellen Netzwerk](./media/nsg-quickstart-portal/select-vnet-subnet.png)

1. Wählen Sie **OK** , wenn der Vorgang abgeschlossen ist.

## <a name="additional-information"></a>Zusätzliche Informationen

Sie können [die in diesem Artikel beschriebenen Schritte auch mithilfe von Azure PowerShell ausführen](nsg-quickstart-powershell.md).

Mithilfe der in diesem Artikel beschriebenen Befehle können Sie dafür sorgen, dass Datenverkehr schnell zu Ihrem virtuellen Computer fließt. Netzwerksicherheitsgruppen bieten viele hervorragende Funktionen sowie eine differenzierte Steuerung des Zugriffs auf Ihre Ressourcen. Weitere Informationen finden Sie unter [Filtern des Netzwerkdatenverkehrs mit einer Netzwerksicherheitsgruppe](../../virtual-network/tutorial-filter-network-traffic.md).

Bei hochverfügbaren Webanwendungen sollten Sie die virtuellen Computer hinter einem Azure Load Balancer anordnen. Der Load Balancer verteilt den Datenverkehr auf virtuelle Computer mit einer Netzwerksicherheitsgruppe, die den Datenverkehr filtert. Weitere Informationen finden Sie unter [Durchführen eines Lastenausgleichs bei virtuellen Windows-Computern in Azure zum Erstellen einer hoch verfügbaren Anwendung](tutorial-load-balancer.md).

## <a name="next-steps"></a>Nächste Schritte
In diesem Artikel haben Sie eine Netzwerksicherheitsgruppe sowie eine Eingangsregel erstellt, die HTTP-Datenverkehr über Port 80 ermöglicht, und dann haben Sie diese Regel einem Subnetz zugeordnet. 

Informationen zum Erstellen von detaillierteren Umgebungen finden Sie in den folgenden Artikeln:
- [Übersicht über den Azure Resource Manager](../../azure-resource-manager/management/overview.md)
- [Sicherheitsgruppen](../../virtual-network/network-security-groups-overview.md)
