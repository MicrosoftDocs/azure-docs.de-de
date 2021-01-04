---
title: 'Tutorial: Verknüpfen eines virtuellen Netzwerks mit einer ExpressRoute-Leitung: Azure-Portal'
description: In diesem Tutorial erfahren Sie, wie Sie über das Azure-Portal eine Verbindung erstellen, um ein virtuelles Netzwerk mit einer Azure ExpressRoute-Leitung zu verknüpfen.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/15/2020
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 0ffc9c2ee17862497d3fd986da8e003f7a497056
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95998769"
---
# <a name="tutorial-connect-a-virtual-network-to-an-expressroute-circuit-using-the-portal"></a>Tutorial: Verbinden eines virtuellen Netzwerks mit einer ExpressRoute-Verbindung mithilfe des Portals

> [!div class="op_single_selector"]
> * [Azure portal](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Azure-Befehlszeilenschnittstelle](howto-linkvnet-cli.md)
> * [Video – Azure-Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (klassisch)](expressroute-howto-linkvnet-classic.md)
> 

Dieses Tutorial hilft Ihnen dabei, über das Azure-Portal eine Verbindung zu erstellen, über die Sie ein virtuelles Netzwerk mit einer Azure ExpressRoute-Leitung verknüpfen. Die virtuellen Netzwerke, die Sie mit Ihrer Azure ExpressRoute-Leitung verbinden, können zum selben oder einem anderen Abonnement gehören.

In diesem Tutorial lernen Sie, wie die folgenden Aufgaben ausgeführt werden:
> [!div class="checklist"]
> - Verbinden eines virtuellen Netzwerks mit einer Leitung im selben Abonnement
> - Verbinden eines virtuellen Netzwerks mit einer Leitung in einem anderen Abonnement
> - Löschen der Verknüpfung zwischen einem virtuellen Netzwerk und einer ExpressRoute-Leitung

## <a name="prerequisites"></a>Voraussetzungen

* Lesen Sie, bevor Sie mit der Konfiguration beginnen, die Seiten [Voraussetzungen](expressroute-prerequisites.md), [Routinganforderungen](expressroute-routing.md) und [Workflows](expressroute-workflows.md).

* Sie benötigen eine aktive ExpressRoute-Verbindung.
  * Führen Sie die Schritte zum [Erstellen einer ExpressRoute-Verbindung](expressroute-howto-circuit-portal-resource-manager.md) aus, und lassen Sie sie vom Konnektivitätsanbieter aktivieren.
  * Stellen Sie sicher, dass privates Azure-Peering für die Verbindung konfiguriert ist. Eine Anleitung für Peering und Routing finden Sie im Artikel [Erstellen und Ändern des Peerings für eine ExpressRoute-Verbindung](expressroute-howto-routing-portal-resource-manager.md).
  * Stellen Sie sicher, dass das private Azure-Peering konfiguriert wird und das BGP-Peering zwischen Ihrem Netzwerk und Microsoft einrichtet, damit End-to-End-Konnektivität bereitgestellt werden kann.
  * Vergewissern Sie sich, dass ein virtuelles Netzwerk und ein virtuelles Netzwerkgateway erstellt und vollständig bereitgestellt wurden. Befolgen Sie die Anweisungen zum [Erstellen eines virtuellen Netzwerkgateways für ExpressRoute](expressroute-howto-add-gateway-resource-manager.md). Für ein virtuelles Netzwerkgateway für ExpressRoute wird der Gatewaytyp „ExpressRoute“ und nicht „VPN“ verwendet.

* Sie können bis zu zehn virtuelle Netzwerke mit einer ExpressRoute-Standardverbindung verknüpfen. Alle virtuellen Netzwerke müssen sich in der gleichen geopolitischen Region befinden, wenn Sie eine ExpressRoute-Standardverbindung verwenden.

* Ein einzelnes VNET kann mit bis zu vier ExpressRoute-Leitungen verknüpft werden. Erstellen Sie anhand der folgenden Vorgehensweise ein neues Verbindungsobjekt für jede ExpressRoute-Leitung, mit der Sie eine Verbindung herstellen. Die ExpressRoute-Leitungen können sich im gleichen Abonnement, in verschiedenen Abonnements oder in einer Kombination aus beidem befinden.

* Wenn Sie das ExpressRoute-Premium-Add-On aktivieren, können Sie virtuelle Netzwerke außerhalb der geopolitischen Region der ExpressRoute-Leitung verknüpfen. Über das Premium-Add-On können Sie – je nach ausgewählter Bandbreite – auch mehr als 10 virtuelle Netzwerke mit Ihrer ExpressRoute-Leitung verbinden. Weitere Informationen zum Premium-Add-On finden Sie in den [häufig gestellten Fragen](expressroute-faqs.md) .

* Sie können sich das [Video ansehen](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit), bevor Sie beginnen, um die Schritte besser zu verstehen.

## <a name="connect-a-vnet-to-a-circuit---same-subscription"></a>Verknüpfen eines VNET mit einer Verbindung – gleiches Abonnement

> [!NOTE]
> BGP-Konfigurationsinformationen werden nicht angezeigt, wenn Ihre Peerings vom Layer 3-Anbieter konfiguriert wurden. Wenn sich Ihre Verbindung im bereitgestellten Zustand befindet, sollten Sie Verbindungen erstellen können.
>

### <a name="to-create-a-connection"></a>So stellen Sie eine Verbindung her

1. Stellen Sie sicher, dass Ihre ExpressRoute-Verbindung und das private Azure-Peering erfolgreich konfiguriert wurden. Folgen Sie der Anleitung unter [Erstellen und Ändern einer ExpressRoute-Verbindung mit PowerShell](expressroute-howto-circuit-arm.md) sowie unter [Erstellen und Ändern des Peerings für eine ExpressRoute-Verbindung mithilfe von PowerShell](expressroute-howto-routing-arm.md). Ihre ExpressRoute-Verbindung sollte in etwa wie in der folgenden Abbildung dargestellt aussehen:

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/express-route-circuit.png" alt-text="Screenshot einer ExpressRoute-Verbindung":::

1. Jetzt können Sie damit beginnen, eine Verbindung zum Verknüpfen des virtuellen Netzwerkgateways mit Ihrer ExpressRoute-Verbindung bereitzustellen. Wählen Sie **Verbindung** > **Hinzufügen** aus, um die Seite **Verbindung hinzufügen** zu öffnen.

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/add-connection.png" alt-text="Screenshot zum Hinzufügen der Verbindung":::

1. Geben Sie einen Namen für die Verbindung ein, und wählen Sie dann **Weiter: Einstellungen >** aus.

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/create-connection-basic.png" alt-text="Basisseite für Verbindungserstellung":::

1. Wählen Sie das Gateway aus, das zu dem virtuellen Netzwerk gehört, für das Sie eine Verknüpfung mit der Leitung herstellen möchten. Wählen Sie anschließend **Überprüfen + erstellen** aus. Wählen Sie nach Abschluss der Überprüfung die Option **Erstellen** aus.

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/create-connection-settings.png" alt-text="Seite mit Einstellungen für Verbindungserstellung":::

1. Nachdem die Verbindung erfolgreich konfiguriert wurde, zeigt das Verbindungsobjekt die Daten für die Verbindung an.

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/connection-object.png" alt-text="Screenshot des Verbindungsobjekts":::

## <a name="connect-a-vnet-to-a-circuit---different-subscription"></a>Verknüpfen eines VNET mit einer Verbindung – anderes Abonnement

Sie können eine ExpressRoute-Verbindung für mehrere Abonnements freigeben. Die folgende Abbildung zeigt eine einfache schematische Darstellung der Freigabe von Lasten für ExpressRoute-Verbindungen für mehrere Abonnements.

:::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/cross-subscription.png" alt-text="Abonnementübergreifende Konnektivität":::

Jede der kleineren Clouds innerhalb der großen Cloud stellt Abonnements dar, die zu verschiedenen Abteilungen innerhalb einer Organisation gehören. Jede Abteilung in der Organisation verwendet ihr eigenes Abonnement zum Bereitstellen von Diensten. Für die Verbindung mit dem lokalen Netzwerk kann jedoch eine einzelne ExpressRoute-Leitung gemeinsam genutzt werden. Eine einzelne Abteilung (in diesem Beispiel: IT) kann die ExpressRoute-Verbindung besitzen. Andere Abonnements innerhalb der Organisation können die ExpressRoute-Leitung ebenfalls nutzen.

  > [!NOTE]
  > Konnektivitäts- und Bandbreitengebühren für die dedizierte Verbindung werden dem Besitzer der ExpressRoute-Verbindung in Rechnung gestellt. Alle virtuellen Netzwerke verwenden gemeinsam dieselbe Bandbreite.
  >

### <a name="administration---about-circuit-owners-and-circuit-users"></a>Verwaltung – Informationen zu Verbindungsbesitzern und Verbindungsbenutzern

Der „Leitungsbesitzer“ (Verbindungsbesitzer) ist ein autorisierter Poweruser der ExpressRoute-Leitungsressource. Der Verbindungsbesitzer kann Autorisierungen erstellen, die durch „Verbindungsbenutzer“ eingelöst werden können. Leitungsbenutzer sind Besitzer virtueller Netzwerkgateways, die sich nicht im selben Abonnement wie die ExpressRoute-Leitung befinden. Verbindungsbenutzer können Autorisierungen einlösen (eine Autorisierung für jedes virtuelle Netzwerk).

Der Besitzer der Verbindung hat die Möglichkeit, Autorisierungen jederzeit zu ändern und aufzuheben. Das Aufheben einer Autorisierung führt dazu, dass alle Verbindungen aus dem Abonnement gelöscht werden, dessen Zugriff aufgehoben wurde.

### <a name="circuit-owner-operations"></a>Aktionen als Verbindungsbesitzer

**So erstellen Sie eine Verbindungsautorisierung**

Der Leitungsbesitzer erstellt eine Autorisierung, die wiederum einen Autorisierungsschlüssel erstellt. Mit diesem kann ein Leitungsbenutzer seine virtuellen Netzwerkgateways mit der ExpressRoute-Leitung verbinden. Eine Autorisierung gilt nur für jeweils eine Verbindung.

> [!NOTE]
> Jede Verbindung erfordert eine separate Autorisierung.
>

1. Klicken Sie auf der Seite „ExpressRoute“ auf **Autorisierungen**, geben Sie einen **Namen** für die Autorisierung ein, und klicken Sie auf **Speichern**.

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/authorization.png" alt-text="Authorizations":::

2. Sobald die Konfiguration gespeichert ist, kopieren Sie die **Ressourcen-ID** und den **Autorisierungsschlüssel**.

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/authorization-key.png" alt-text="Autorisierungsschlüssel":::

**So löschen Sie eine Verbindungsautorisierung**

Sie können eine Verbindung löschen, indem Sie das Symbol **Löschen** für den Autorisierungsschlüssel Ihrer Verbindung auswählen.

:::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/delete-authorization-key.png" alt-text="Löschen des Autorisierungsschlüssels":::

Wenn Sie die Verbindung löschen, den Autorisierungsschlüssel aber beibehalten möchten, können Sie die Verbindung auf der entsprechenden Verbindungsseite der Leitung löschen.

:::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/delete-connection-owning-circuit.png" alt-text="Löschen der Leitung, die Besitzer der Verbindung ist":::

### <a name="circuit-user-operations"></a>Aktionen als Verbindungsbenutzer

Der Verbindungsbenutzer benötigt die Ressourcen-ID und einen Autorisierungsschlüssel vom Verbindungsbesitzer.

**So lösen Sie eine Verbindungsautorisierung ein**

1. Wählen Sie die Schaltfläche **+ Ressource erstellen** aus. Suchen Sie nach **Verbindung**, und wählen Sie **Erstellen** aus.

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/create-new-resources.png" alt-text="Erstellen neuer Ressourcen":::

1. Stellen Sie sicher, dass die Option *Verbindungstyp* auf **ExpressRoute** festgelegt ist. Wählen Sie auf der Seite mit den Grundlagen die *Ressourcengruppe* und den *Standort* und dann **OK** aus.

    > [!NOTE]
    > Der Standort *muss* mit dem Ort des Gateways für virtuelle Netzwerke übereinstimmen, für das Sie die Verbindung erstellen.

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/connection-basics.png" alt-text="Seite „Grundlagen“":::

1. Wählen Sie auf der Seite **Einstellungen** die Option *Gateway für virtuelle Netzwerke* aus, und aktivieren Sie das Kontrollkästchen **Autorisierung einlösen**. Geben Sie den *Autorisierungsschlüssel* und *Peerleitungs-URI* ein, und benennen Sie die Verbindung. Klicken Sie auf **OK**. 
 
    > [!NOTE]
    > Der *Peerleitungs-URI* ist die Ressourcen-ID der ExpressRoute-Leitung (die Sie im Einstellungsbereich „Eigenschaften“ der ExpressRoute-Leitung finden).

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/connection-settings.png" alt-text="Einstellungsseite":::

1. Überprüfen Sie die Informationen auf der Seite **Zusammenfassung**, und klicken Sie auf **OK**.

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/connection-summary.png" alt-text="Seite „Zusammenfassung“":::

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Sie können eine Verbindung löschen und die Verknüpfung Ihres VNET mit einer ExpressRoute-Verbindung aufheben, indem Sie das Symbol **Löschen** auf der Seite für die Verbindung auswählen.

:::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/delete-connection.png" alt-text="Löschen einer Verbindung":::

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie gelernt, wie Sie ein virtuelles Netzwerk mit einer Leitung im selben und in einem anderen Abonnement verbinden. Weitere Informationen zum ExpressRoute-Gateway finden Sie hier: 

> [!div class="nextstepaction"]
> [Informationen zu ExpressRoute-Gateways für virtuelle Netzwerke](expressroute-about-virtual-network-gateways.md)
