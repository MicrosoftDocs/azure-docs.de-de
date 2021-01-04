---
title: 'Tutorial: Zusammenfassen lokaler Umgebungen mittels Peering zu einer privaten Cloud'
description: Erfahren Sie, wie Sie ExpressRoute Global Reach-Peering mit einer privaten Cloud in einer Azure VMware Solution-Instanz erstellen.
ms.topic: tutorial
ms.date: 09/21/2020
ms.openlocfilehash: 4d10972a693f7c4c3ae25a5bc986f6c15e978294
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/29/2020
ms.locfileid: "92912496"
---
# <a name="tutorial-peer-on-premises-environments-to-a-private-cloud"></a>Tutorial: Zusammenfassen lokaler Umgebungen mittels Peering zu einer privaten Cloud

Über ExpressRoute Global Reach wird Ihre lokale Umgebung mit Ihrer privaten Azure VMware Solution-Cloud verbunden. Die ExpressRoute Global Reach-Verbindung wird zwischen der ExpressRoute-Leitung der privaten Cloud und einer vorhandenen ExpressRoute-Verbindung mit Ihren lokalen Umgebungen hergestellt. 

Für die ExpressRoute-Leitung, die Sie beim [Konfigurieren des Netzwerks zwischen Azure und privater Cloud](tutorial-configure-networking.md) verwenden, müssen Autorisierungsschlüssel erstellt und verwendet werden.  Sie haben bereits einen Autorisierungsschlüssel der ExpressRoute-Leitung verwendet, und Sie erstellen in diesem Tutorial einen zweiten Autorisierungsschlüssel, um ein Peering mit Ihrer lokalen ExpressRoute-Leitung einzurichten.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen eines zweiten Autorisierungsschlüssels für _Circuit 2_ , die ExpressRoute-Leitung der privaten Cloud
> * Verwenden entweder des [Azure-Portals](#azure-portal-method) oder der [Azure CLI in einer Cloud Shell-Methode](#azure-cli-in-a-cloud-shell-method) im Abonnement von _Circuit 1_ , um das ExpressRoute Global Reach-Peering zwischen lokaler Umgebung und privater Cloud zu aktivieren


## <a name="before-you-begin"></a>Voraussetzungen

Lesen Sie zunächst die Dokumentation zum [Aktivieren der Konnektivität zwischen ExpressRoute-Leitungen in verschiedenen Azure-Abonnements](../expressroute/expressroute-howto-set-global-reach-cli.md#enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions), bevor Sie die Konnektivität zwischen zwei ExpressRoute-Leitungen mit ExpressRoute Global Reach aktivieren.  


## <a name="prerequisites"></a>Voraussetzungen

- Eine eingerichtete Verbindung mit einer privaten Azure VMware Solution-Cloud, deren ExpressRoute-Leitung mittels Peering mit einem ExpressRoute-Gateway in einem virtuellen Azure-Netzwerk (VNET) verbunden ist. Dies ist _Circuit 2_ aus den Peeringvorgängen.  
- Eine separate, funktionierende ExpressRoute-Leitung für die Verbindungsherstellung zwischen lokalen Umgebungen und Azure. Dies ist _Circuit 1_ aus Sicht der Peeringvorgänge.
- Ein [Netzwerkadressblock](../expressroute/expressroute-routing.md#ip-addresses-used-for-peerings) vom Typ „/29“ ohne Überschneidungen für das ExpressRoute Global Reach-Peering.

> [!TIP]
> Im Kontext dieser Voraussetzungen ist _Circuit 1_ Ihre lokale ExpressRoute-Leitung. Die ExpressRoute-Leitung Ihrer privaten Cloud befindet sich in einem anderen Abonnement und heißt _Circuit 2_. 


## <a name="create-an-expressroute-authorization-key-in-the-private-cloud"></a>Erstellen eines ExpressRoute-Autorisierungsschlüssels in der privaten Cloud

1. Navigieren Sie in der **Übersicht** der privaten Cloud unter „Verwalten“ zu **Konnektivität > ExpressRoute > Autorisierungsschlüssel anfordern**.

   :::image type="content" source="media/expressroute-global-reach/start-request-auth-key.png" alt-text="Auswählen von „Konnektivität“ > „ExpressRoute“ > „Autorisierungsschlüssel anfordern“, um eine neue Anforderung zu initiieren":::

2. Geben Sie den Namen für den Autorisierungsschlüssel ein, und wählen Sie **Erstellen** aus. 

   :::image type="content" source="media/expressroute-global-reach/create-global-reach-auth-key.png" alt-text="Auswählen von „Erstellen“, um einen neuen Autorisierungsschlüssel zu erstellen":::

   Nach der Erstellung wird der neue Schlüssel in der Liste der Autorisierungsschlüssel für die private Cloud angezeigt. 

   :::image type="content" source="media/expressroute-global-reach/show-global-reach-auth-key.png" alt-text="Vergewissern, dass der neue Autorisierungsschlüssel in der Liste der Schlüssel für die private Cloud angezeigt wird":::

3. Notieren Sie sich den Autorisierungsschlüssel und die ExpressRoute-ID zusammen mit dem Adressblock vom Typ „/29“. Diese Angaben werden im nächsten Schritt benötigt, um das Peering abzuschließen. 

## <a name="peer-private-cloud-to-on-premises-using-authorization-key"></a>Verknüpfen der privaten Cloud mit der lokalen Umgebung mittels Peering unter Verwendung des Autorisierungsschlüssels

Nachdem Sie einen Autorisierungsschlüssel für die ExpressRoute-Leitung der privaten Cloud erstellt haben, können Sie sie mittels Peering mit Ihrer lokalen ExpressRoute-Leitung verknüpfen.  Das Peering erfolgt aus der Perspektive der lokalen ExpressRoute-Leitung entweder über das [Azure-Portal](#azure-portal-method) oder über Verwenden der [Azure CLI in einer Cloud Shell-Umgebung](#azure-cli-in-a-cloud-shell-method). Bei beiden Methoden verwenden Sie die Ressourcen-ID und den Autorisierungsschlüssel der ExpressRoute-Leitung Ihrer privaten Cloud dazu, das Peering abzuschließen.

### <a name="azure-portal-method"></a>Azure-Portal-Methode

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) mit dem Abonnement an, das auch für die lokale ExpressRoute-Leitung verwendet wird.

1. Wählen Sie in der **Übersicht** der privaten Cloud unter „Verwalten“ die Option **Konnektivität > ExpressRoute Global Reach > Hinzufügen** aus.

   :::image type="content" source="./media/expressroute-global-reach/expressroute-global-reach-tab.png" alt-text="Wählen Sie im Menü die Option „Konnektivität“ aus, wählen Sie die Registerkarte „ExpressRoute Global Reach“ und dann „Hinzufügen“ aus.":::

1. Sie können eine lokale Cloudverbindung erstellen, indem Sie eine der folgenden Aktionen ausführen:

   - Wählen Sie die ExpressRoute-Leitung in der Liste aus.
   - Wenn Sie eine Leitungs-ID haben, kopieren Sie diese, und fügen Sie sie ein.

1. Wählen Sie **Verbinden** aus. Die neue Verbindung wird in der Liste „Lokale Cloudverbindungen“ angezeigt.  

>[!TIP]
>Sie können eine Verbindung aus der Liste löschen oder trennen, indem Sie **Mehr** auswählen.  
>
> :::image type="content" source="./media/expressroute-global-reach/on-premises-connection-disconnect.png" alt-text="Eine lokale Verbindung trennen oder löschen":::

### <a name="azure-cli-in-a-cloud-shell-method"></a>Azure CLI in einer Cloud Shell-Methode

Wir haben die [CLI-Befehle](../expressroute/expressroute-howto-set-global-reach-cli.md) mit speziellen Details und Beispielen erweitert, um Sie bei beim Konfigurieren des ExpressRoute Global Reach-Peerings zwischen lokalen Umgebungen und einer privaten Azure VMware Solution-Cloud zu unterstützen.  

> [!TIP]  
> Zur besseren Übersichtlichkeit der Azure CLI-Befehlsausgabe wird in dieser Anleitung ggf. ein [Argument vom Typ `–query` verwendet, um eine JMESPath-Abfrage auszuführen und nur die erforderlichen Ergebnisse anzuzeigen](/cli/azure/query-azure-cli).


1. Melden Sie sich beim Azure-Portal mit dem Abonnement an, das auch für die lokale ExpressRoute-Leitung verwendet wurde, und öffnen Sie eine Cloud Shell-Instanz. Verwenden Sie die standardmäßige Bash-Shell.
 
   :::image type="content" source="media/expressroute-global-reach/open-cloud-shell.png" alt-text="Anmelden beim Azure-Portal und Öffnen einer Cloud Shell-Instanz":::
 
2. Geben Sie den Azure CLI-Befehl ein, um das Peering zu erstellen. Verwenden Sie Ihre spezifischen Informationen sowie die Ressourcen-ID, den Autorisierungsschlüssel und den CIDR-Netzwerkblock vom Typ „/29“. 

   Die folgende Abbildung zeigt den zu verwendenden Befehl sowie die Ausgabe mit der Angabe, dass das Peering erfolgreich war. Der Beispielbefehl basiert auf dem Befehl, der in Schritt 3 von [Aktivieren der Konnektivität zwischen ExpressRoute-Leitungen in verschiedenen Azure-Abonnements](../expressroute/expressroute-howto-set-global-reach-cli.md#enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions) verwendet wird.

   :::image type="content" source="media/expressroute-global-reach/azure-command-with-results.png" alt-text="Erstellen des ExpressRoute Global Reach-Peerings per Azure CLI-Befehl in einer Cloud Shell-Instanz":::
 
   Sie können nun von lokalen Umgebungen aus über das ExpressRoute Global Reach-Peering eine Verbindung mit Ihrer privaten Cloud herstellen.

> [!TIP]
> Das soeben erstellte Peering kann wie unter [Deaktivieren der Konnektivität zwischen Ihren lokalen Netzwerken](../expressroute/expressroute-howto-set-global-reach-cli.md#disable-connectivity-between-your-on-premises-networks) beschrieben wieder gelöscht werden.


## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie erfahren, wie Sie einen zweiten Autorisierungsschlüssel für die ExpressRoute-Leitung der privaten Cloud erstellen. Außerdem haben Sie erfahren, wie Sie das ExpressRoute Global Reach-Peering zwischen lokaler Umgebung und privater Cloud aktivieren. 

Arbeiten Sie das nächste Tutorial durch, um zu erfahren Sie, wie Sie eine VMware HCX-Lösung für Ihre private Azure VMware Solution-Cloud bereitstellen und konfigurieren.

> [!div class="nextstepaction"]
> [Bereitstellen und Konfigurieren von VMware HCX](tutorial-deploy-vmware-hcx.md)


<!-- LINKS - external-->

<!-- LINKS - internal -->