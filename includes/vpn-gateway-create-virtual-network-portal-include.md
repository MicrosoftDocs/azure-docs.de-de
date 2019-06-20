---
title: include file
description: include file
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 04/04/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: d35da4f1eaed91411c015ed7665944d886f9d79c
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67177946"
---
Wenn Sie mit dem Azure-Portal ein VNET im Resource Manager-Bereitstellungsmodell erstellen, führen Sie die Schritte unten aus. Verwenden Sie die [Beispielwerte](#values), wenn Sie diese Schritte für ein Tutorial ausführen. Wenn Sie diese Schritte nicht als Tutorial ausführen, achten Sie darauf, die Werte durch eigene Werte zu ersetzen. Weitere Informationen zur Arbeit mit virtuellen Netzwerken finden Sie unter [Virtuelle Netzwerke im Überblick](../articles/virtual-network/virtual-networks-overview.md).

>[!NOTE]
>Damit für dieses VNET eine Verbindung mit einem lokalen Speicherort hergestellt werden kann, müssen Sie in Zusammenarbeit mit dem Administrator des lokalen Netzwerks einen IP-Adressbereich festlegen, den Sie speziell für dieses virtuelle Netzwerk verwenden können. Falls ein Adressbereich auf beiden Seiten der VPN-Verbindung und somit doppelt vorhanden ist, wird Datenverkehr unter Umständen nicht wie erwartet weitergeleitet. Wenn Sie für dieses VNet eine Verbindung mit einem anderen VNet herstellen möchten, darf sich der Adressraum außerdem nicht mit dem anderen VNet überlappen. Achten Sie darauf, dass Sie Ihre Netzwerkkonfiguration entsprechend planen.
>
>

1. Navigieren Sie in einem Browser zum [Azure-Portal](http://portal.azure.com) , und melden Sie sich mit Ihrem Azure-Konto an.
2. Klicken Sie auf **Ressource erstellen**. Geben Sie im Feld **Marketplace durchsuchen** die Zeichenfolge „Virtuelles Netzwerk“ ein. Klicken Sie in der zurückgegebenen Liste auf **Virtuelles Netzwerk**, um die Seite **Virtual Network** zu öffnen.
3. Wählen Sie unten auf der Seite „Virtuelles Netzwerk“ in der Liste **Bereitstellungsmodell auswählen** die Option **Resource Manager** aus, und klicken Sie dann auf **Erstellen**. Die Seite „Virtuelles Netzwerk erstellen“ wird geöffnet.

   ![Seite „Virtuelles Netzwerk erstellen“](./media/vpn-gateway-create-virtual-network-portal-include/create-virtual-network.png "Seite „Virtuelles Netzwerk erstellen“")
4. Konfigurieren Sie auf der Seite **Virtuelles Netzwerk erstellen** die VNET-Einstellungen. Beim Ausfüllen der Felder ändert sich das rote Ausrufezeichen in ein grünes Häkchen, sofern die in das Feld eingegebenen Zeichen zulässig sind.

   - **Name**: Geben Sie den Namen für Ihr virtuelles Netzwerk ein. In diesem Beispiel verwenden wir „VNet1“.
   - **Adressraum:** Geben Sie den Adressraum ein. Falls Sie mehrere Adressräume hinzufügen möchten, fügen Sie Ihren ersten Adressraum hinzu. Weitere Adressräume können später (nach Erstellung des VNets) hinzugefügt werden. Achten Sie darauf, dass sich der angegebene Adressraum nicht mit dem Adressraum für Ihren lokalen Standort überlappt.
   - **Abonnement**: Vergewissern Sie sich, dass das richtige Abonnement angegeben ist. Das Abonnement kann über die Dropdownliste geändert werden.
   - **Ressourcengruppe**: Wählen Sie eine vorhandene Ressourcengruppe aus, oder geben Sie einen Namen ein, um eine neue Ressourcengruppe zu erstellen. Geben Sie der Ressourcengruppe beim Erstellen einer neuen Gruppe einen Namen, der zu Ihren geplanten Konfigurationswerten passt. Weitere Informationen zu Ressourcengruppen finden Sie unter [Übersicht über den Azure-Ressourcen-Manager](../articles/azure-resource-manager/resource-group-overview.md#resource-groups).
   - **Standort**: Wählen Sie den Standort für Ihr VNET aus. Der Standort gibt an, wo sich die in diesem VNet bereitgestellten Ressourcen befinden.
   - **Subnetz**: Fügen Sie Name und Adressbereich des ersten Subnetzes hinzu. Sie können zusätzliche Subnetze und das Gatewaysubnetz später nach dem Hinzufügen dieses VNet erstellen. 

5. Wählen Sie **An Dashboard anheften** aus, wenn das VNET komfortabel auf dem Dashboard zur Verfügung stehen soll, und klicken Sie dann auf **Erstellen**. Nach dem Klicken auf **Erstellen** wird auf dem Dashboard eine Kachel angezeigt, auf der der Status des VNET angegeben wird. Die Kachel verändert sich, wenn das VNet erstellt wird.
