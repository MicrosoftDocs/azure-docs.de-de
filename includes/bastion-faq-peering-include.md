---
title: include file
description: include file
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: include
ms.date: 11/05/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 57b0bb9ab8ceb34021a38db0d0abf2c9c919e808
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/07/2020
ms.locfileid: "94356649"
---
### <a name="can-i-still-deploy-multiple-bastion-hosts-across-peered-virtual-networks"></a>Kann ich weiterhin mehrere Bastionhosts in mittels Peering verbundenen virtuellen Netzwerken bereitstellen?

Ja. Standardmäßig wird Benutzern der Bastionhost angezeigt, der im selben virtuellen Netzwerk bereitgestellt ist, in dem sich die VM befindet. Im Menü **Verbinden** können Benutzer jedoch mehrere Bastionhosts anzeigen, die in den mittels Peering verbundenen Netzwerken erkannt wurden. Sie können den gewünschten Bastionhost auswählen, über den sie eine Verbindung mit der im virtuellen Netzwerk bereitgestellten VM herstellen möchten.

### <a name="if-my-peered-vnets-are-deployed-in-different-subscriptions-will-connectivity-via-bastion-work"></a>Funktionieren Verbindungen über Bastion auch, wenn meine Peering-VNETs in unterschiedlichen Abonnements bereitgestellt wurden?

Ja, die Konnektivität über Bastion funktioniert weiterhin für mittels Peering verbundene VNETs in verschiedenen Abonnements für einen einzelnen Mandanten. Abonnements von zwei unterschiedlichen Mandanten werden nicht unterstützt. Damit Bastion im Dropdownmenü **Verbinden** angezeigt wird, muss der Benutzer unter **Abonnement > Globales Abonnement** die Abonnements auswählen, auf die er Zugriff hat.

:::image type="content" source="./media/bastion-faq-peering-include/global-subscriptions.png" alt-text="Globaler Abonnementfilter" lightbox="./media/bastion-faq-peering-include/global-subscriptions.png":::

### <a name="i-have-access-to-the-peered-vnet-but-i-cant-see-the-vm-deployed-there"></a>Ich habe Zugriff auf das Peering-VNET, sehe aber die dort bereitgestellte VM nicht.

Vergewissern Sie sich, dass der Benutzer über **Lesezugriff** auf die VM und das mittels Peering verbundene VNET verfügt. Überprüfen Sie außerdem unter IAM, ob der Benutzer **Lesezugriff** auf folgende Ressourcen hat:

* Rolle „Leser“ für den virtuellen Computer
* Rolle „Leser“ für den Netzwerkadapter mit privater IP-Adresse des virtuellen Computers
* Rolle „Leser“ für die Azure Bastion-Ressource
* Rolle „Leser“ im virtuellen Netzwerk (nicht erforderlich, wenn kein mittels Peering verbundenes virtuelles Netzwerk vorhanden ist)

|Berechtigungen|BESCHREIBUNG|Berechtigungstyp|
|---|---| ---|
|Microsoft.Network/bastionHosts/read |Ruft einen Bastionhost ab.|Aktion|
|Microsoft.Network/virtualNetworks/BastionHosts/action |Ruft Bastionhostverweise in einem virtuellen Netzwerk ab.|Aktion|
|Microsoft.Network/virtualNetworks/bastionHosts/default/action|Ruft Bastionhostverweise in einem virtuellen Netzwerk ab.|Aktion|
|Microsoft.Network/networkInterfaces/read|Ruft eine Netzwerkschnittstellendefinition ab.|Aktion|
|Microsoft.Network/networkInterfaces/ipconfigurations/read|Ruft eine IP-Konfigurationsdefinition für Netzwerkschnittstellen ab.|Aktion|
|Microsoft.Network/virtualNetworks/read|Dient zum Abrufen der Definition des virtuellen Netzwerks.|Aktion|
|Microsoft.Network/virtualNetworks/subnets/virtualMachines/read|Ruft Verweise auf alle virtuellen Computer im Subnetz eines virtuellen Netzwerks ab.|Aktion|
|Microsoft.Network/virtualNetworks/virtualMachines/read|Ruft Verweise auf alle virtuellen Computer in einem virtuellen Netzwerk ab.|Aktion|