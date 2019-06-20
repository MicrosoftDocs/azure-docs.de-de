---
title: Öffnen von Ports für einen virtuellen Computer mithilfe des Azure-Portals | Microsoft Docs
description: Erfahren Sie, wie Sie mit dem Resource Manager-Bereitstellungsmodell im Azure-Portal für Ihren virtuellen Windows-Computer einen Port öffnen oder einen Endpunkt erstellen.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
ms.assetid: f7cf0319-5ee7-435e-8f94-c484bf5ee6f1
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/27/2018
ms.author: cynthn
ms.openlocfilehash: cac17403425f53593d4f48692b4216a92c8624e3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61481750"
---
# <a name="how-to-open-ports-to-a-virtual-machine-with-the-azure-portal"></a>Öffnen von Ports zu einem virtuellen Computer mit dem Azure-Portal
[!INCLUDE [virtual-machines-common-nsg-quickstart](../../../includes/virtual-machines-common-nsg-quickstart.md)]


## <a name="sign-in-to-azure"></a>Anmelden bei Azure
Melden Sie sich unter https://portal.azure.com beim Azure-Portal an.

## <a name="create-a-network-security-group"></a>Erstellen einer Netzwerksicherheitsgruppe

1. Suchen Sie nach der Ressourcengruppe für den virtuellen Computer, und wählen Sie diese aus. Wählen Sie **Hinzufügen** aus, suchen Sie dann nach **Netzwerksicherheitsgruppe**, und wählen Sie diese Option aus.

2. Klicken Sie auf **Erstellen**.

    Das Fenster **Netzwerksicherheitsgruppe erstellen** wird geöffnet.

    ![Erstellen einer Netzwerksicherheitsgruppe](./media/nsg-quickstart-portal/create-nsg.png)

2. Geben Sie einen Namen für Ihre Netzwerksicherheitsgruppe ein. 

3. Wählen Sie eine Ressourcengruppe aus, oder erstellen Sie eine, und wählen Sie dann einen Speicherort aus.

4. Wählen Sie **Erstellen** aus, um die Netzwerksicherheitsgruppe zu erstellen.

## <a name="create-an-inbound-security-rule"></a>Erstellen einer Eingangssicherheitsregel

1. Wählen Sie Ihre neue Netzwerksicherheitsgruppe aus. 

2. Wählen Sie **Eingangssicherheitsregeln** aus, und wählen Sie dann **Hinzufügen** aus.

    ![Hinzufügen einer Eingangsregel](./media/nsg-quickstart-portal/add-inbound-rule.png)

3. Wählen Sie **Advanced** (Erweitert). 

4. Wählen Sie im Dropdownmenü einen allgemeinen **Dienst**, z.B. **HTTP**, aus. Sie können auch **Benutzerdefiniert** auswählen, wenn Sie einen bestimmten zu verwendenden Port angeben möchten. 

5. Ändern Sie optional die **Priorität** oder den **Namen**. Die Priorität wirkt sich auf die Reihenfolge aus, in der Regeln angewendet werden: je niedriger der numerische Wert, desto früher wird die Regel angewendet.

6. Wählen Sie **Hinzufügen** aus, um die Regel zu erstellen.

## <a name="associate-your-network-security-group-with-a-subnet"></a>Zuordnen Ihrer Netzwerksicherheitsgruppe zu einem Subnetz

Im letzten Schritt muss die Netzwerksicherheitsgruppe einem Subnetz oder einer bestimmten Netzwerkschnittstelle zugeordnet werden. In diesem Beispiel ordnen wir die Netzwerksicherheitsgruppe einem Subnetz zu. 

1. Wählen Sie **Subnetze** aus, und wählen Sie dann **Zuordnen** aus.

    ![Zuordnen einer Netzwerksicherheitsgruppe zu einem Subnetz](./media/nsg-quickstart-portal/associate-subnet.png)

2. Wählen Sie das virtuelle Netzwerk und dann das gewünschte Subnetz aus.

    ![Zuordnen einer Netzwerksicherheitsgruppe zu einem virtuellen Netzwerk](./media/nsg-quickstart-portal/select-vnet-subnet.png)

    Alle virtuellen Computer, die Sie mit diesem Subnetz verbinden, sind jetzt über Port 80 erreichbar.

## <a name="additional-information"></a>Zusätzliche Informationen

Sie können [die in diesem Artikel beschriebenen Schritte auch mithilfe von Azure PowerShell ausführen](nsg-quickstart-powershell.md).

Mithilfe der in diesem Artikel beschriebenen Befehle können Sie dafür sorgen, dass Datenverkehr schnell zu Ihrem virtuellen Computer fließt. Netzwerksicherheitsgruppen bieten viele hervorragende Funktionen sowie eine differenzierte Steuerung des Zugriffs auf Ihre Ressourcen. Weitere Informationen finden Sie unter [Filtern des Netzwerkdatenverkehrs mit einer Netzwerksicherheitsgruppe](../../virtual-network/tutorial-filter-network-traffic.md).

Bei hochverfügbaren Webanwendungen sollten Sie die virtuellen Computer hinter einem Azure Load Balancer anordnen. Der Load Balancer verteilt den Datenverkehr auf virtuelle Computer mit einer Netzwerksicherheitsgruppe, die den Datenverkehr filtert. Weitere Informationen finden Sie unter [Durchführen eines Lastenausgleichs bei virtuellen Windows-Computern in Azure zum Erstellen einer hoch verfügbaren Anwendung](tutorial-load-balancer.md).

## <a name="next-steps"></a>Nächste Schritte
In diesem Artikel haben Sie eine Netzwerksicherheitsgruppe sowie eine Eingangsregel erstellt, die HTTP-Datenverkehr über Port 80 ermöglicht, und dann haben Sie diese Regel einem Subnetz zugeordnet. 

Informationen zum Erstellen von detaillierteren Umgebungen finden Sie in den folgenden Artikeln:
- [Übersicht über den Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md)
- [Sicherheitsgruppen](../../virtual-network/security-overview.md)