---
title: include file
description: include file
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/30/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 835f23f98ebe56e0b19081f07dc3302ef93b27b9
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67177933"
---
Sie können ein VNET wie im Folgenden beschrieben mit dem Resource Manager-Bereitstellungsmodell im Azure-Portal erstellen. Weitere Informationen zu virtuellen Netzwerken finden Sie in der [Übersicht über Azure Virtual Network](../articles/virtual-network/virtual-networks-overview.md).

>[!NOTE]
>Um das VNET mit einem lokalen Standort zu verbinden, legen Sie in Zusammenarbeit mit dem Administrator des lokalen Netzwerks einen IP-Adressbereich fest, den Sie speziell für dieses virtuelle Netzwerk verwenden können. Wenn ein Adressbereich auf beiden Seiten der VPN-Verbindung und somit doppelt vorhanden ist, wird Datenverkehr nicht wie erwartet weitergeleitet. Wenn Sie für dieses VNet eine Verbindung mit einem anderen VNet herstellen möchten, darf sich der Adressraum außerdem nicht mit dem anderen VNet überlappen. Planen Sie Ihre Netzwerkkonfiguration entsprechend.
>
>

1. Melden Sie sich beim [Azure-Portal](http://portal.azure.com) an, und klicken Sie auf **Ressource erstellen**. Die Seite **Neu** wird geöffnet.

2. Geben Sie *virtuelles Netzwerk* in das Feld **Marketplace durchsuchen** ein, und wählen Sie in der zurückgegebenen Liste den Eintrag **Virtuelles Netzwerk** aus. Die Seite **Virtuelles Netzwerk** wird geöffnet.

   ![Zur Seite mit den Ressourcen des virtuellen Netzwerks navigieren](./media/vpn-gateway-basic-vnet-rm-portal-include/newvnetportal700.png "Zur Seite mit den Ressourcen des virtuellen Netzwerks navigieren")

3. Wählen Sie unten auf der Seite in der Liste **Bereitstellungsmodell auswählen** die Option **Resource Manager** aus, und klicken Sie anschließend auf **Erstellen**. Die Seite **Virtuelles Netzwerk erstellen** wird geöffnet.

   ![Seite „Virtuelles Netzwerk erstellen“](./media/vpn-gateway-basic-vnet-rm-portal-include/vnet.png "Seite „Virtuelles Netzwerk erstellen“")

4. Konfigurieren Sie auf der Seite **Virtuelles Netzwerk erstellen** die VNET-Einstellungen. Beim Ausfüllen der Felder ändert sich das rote Ausrufezeichen in ein grünes Häkchen, sobald die eingegebenen Zeichen überprüft wurden. Einige Werte werden automatisch eingetragen und können durch eigene Werte ersetzt werden:

   - **Name**: Geben Sie den Namen für Ihr virtuelles Netzwerk ein.

   - **Adressraum**: Geben Sie den Adressraum ein. Falls Sie mehrere Adressräume hinzufügen möchten, geben Sie hier den ersten Adressraum ein. Sie können später weitere Adressräume hinzufügen, nachdem Sie das VNET erstellt haben.

   - **Abonnement**: Vergewissern Sie sich, dass das richtige Abonnement angegeben ist. Das Abonnement kann über die Dropdownliste geändert werden.

   - **Ressourcengruppe**: Wählen Sie eine vorhandene Ressourcengruppe aus, oder geben Sie einen Namen ein, um eine neue Ressourcengruppe zu erstellen. Verwenden Sie für eine neue Ressourcengruppe einen Namen, der zu Ihren geplanten Konfigurationswerten passt. Weitere Informationen zu Ressourcengruppen finden Sie unter [Azure Resource Manager – Übersicht](../articles/azure-resource-manager/resource-group-overview.md#resource-groups).

   - **Standort**: Wählen Sie den Standort für Ihr VNET aus. Der Standort gibt an, wo sich die in diesem VNET bereitgestellten Ressourcen befinden.

   - **Subnetz**: Fügen Sie den **Namen** und **Adressbereich** des Subnetzes hinzu. Sie können später weitere Subnetze hinzufügen, nachdem Sie das VNET erstellt haben. 
     
5. Klicken Sie auf **Erstellen**.
