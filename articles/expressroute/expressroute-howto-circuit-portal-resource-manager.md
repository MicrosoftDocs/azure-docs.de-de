---
title: 'Schnellstart: Erstellen und Ändern einer Verbindung mit ExpressRoute: Azure-Portal'
description: In diesem Schnellstart erfahren Sie, wie Sie eine ExpressRoute-Verbindung im Azure-Portal erstellen, bereitstellen, überprüfen, aktualisieren, löschen und ihre Bereitstellung aufheben können.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: quickstart
ms.date: 10/21/2020
ms.author: duau
ms.openlocfilehash: b0fd844021e7398e061072d7939b782616580c1c
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96185197"
---
# <a name="quickstart-create-and-modify-an-expressroute-circuit"></a>Schnellstart: Erstellen und Ändern einer ExpressRoute-Verbindung

In diesem Schnellstart erfahren Sie, wie Sie eine ExpressRoute-Verbindung mithilfe des Azure-Portals und des Azure Resource Manager-Bereitstellungsmodells erstellen. Außerdem können Sie den Status einer Verbindung überprüfen, die Verbindung aktualisieren oder löschen oder ihre Bereitstellung aufheben.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Lesen Sie vor Beginn der Konfiguration die Seiten zu den [Voraussetzungen](expressroute-prerequisites.md) und [Workflows](expressroute-workflows.md).
* Sie können sich das [Video ansehen](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit), bevor Sie beginnen, um die Schritte besser zu verstehen.

## <a name="create-and-provision-an-expressroute-circuit"></a><a name="create"></a>Erstellen und Bereitstellen einer ExpressRoute-Verbindung

### <a name="sign-in-to-the-azure-portal"></a>Melden Sie sich auf dem Azure-Portal an.

Navigieren Sie in einem Browser zum [Azure-Portal](https://portal.azure.com) , und melden Sie sich mit Ihrem Azure-Konto an.

### <a name="create-a-new-expressroute-circuit"></a>Erstellen Sie eine neue ExpressRoute-Verbindung.

> [!IMPORTANT]
> Ihre ExpressRoute-Verbindung wird von dem Moment an berechnet, in dem ein Dienstschlüssel ausgegeben wird. Stellen Sie sicher, dass Sie diesen Vorgang ausführen, sobald der Konnektivitätsanbieter dazu bereit ist, die Verbindung bereitzustellen.

Sie können eine ExpressRoute-Verbindung erstellen, indem Sie die Option zum Erstellen einer neuen Ressource auswählen. 

1. Wählen Sie im Menü des Azure-Portals die Option **Ressource erstellen** aus. Wählen Sie **Netzwerk** > **ExpressRoute** aus, wie in der folgenden Abbildung gezeigt:

    :::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/create-expressroute-circuit-menu.png" alt-text="Erstellen Sie eine ExpressRoute-Verbindung.":::

2. Nachdem Sie **ExpressRoute** ausgewählt haben, wird die Seite **ExpressRoute erstellen** angezeigt. Geben Sie **Ressourcengruppe**, **Region** und **Name** für die Verbindung an. Wählen Sie anschließend **Next: Konfiguration >** .

    :::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-create-basic.png" alt-text="Konfigurieren der Ressourcengruppe und der Region":::

3. Wenn Sie die Werte auf der Seite ausfüllen, stellen Sie sicher, dass Sie den richtigen SKU-Tarif (Local, Standard oder Premium) sowie das richtige Abrechnungsmodell für die Datenmessung (Unbegrenzt oder Taktung) angeben.

    :::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-create-configuration.png" alt-text="Konfigurieren der Verbindung":::
    
    * Der **Porttyp** bestimmt, ob Sie eine Verbindung mit einem Dienstanbieter oder direkt mit dem globalen Netzwerk von Microsoft an einem Peeringstandort herstellen.
    * **Neue erstellen oder aus klassischer importieren** bestimmt, ob eine neue Verbindung erstellt wird oder ob eine klassische Verbindung zu Azure Resource Manager migriert wird.
    * **Anbieter** ist der Internetdienstanbieter, von dem Sie den Dienst anfordern.
    * Der **Peeringort** ist der physische Standort, an dem Ihr Peering mit Microsoft stattfindet.

    > [!IMPORTANT]
    > Der Peeringstandort entspricht dem [physischen Standort](expressroute-locations.md), an dem Ihr Peering mit Microsoft stattfindet. Dieser ist **nicht** mit der Eigenschaft „Standort“ verknüpft, die sich auf den geografischen Standort des Azure-Netzwerkressourcenanbieters befindet. Obgleich sie nicht miteinander in Zusammenhang stehen, sollten Sie einen Netzwerkressourcenanbieter in geografischer Nähe des Peeringstandorts der Verbindung wählen.

    * **SKU** bestimmt, ob ein ExpressRoute Local-, ExpressRoute Standard- oder ExpressRoute Premium-Add-On aktiviert wird. Sie können **Local** für die Local-SKU, **Standard** für die Standard-SKU oder **Premium** für das Premium-Add-On angeben. Sie können die SKU ändern, um das Premium-Add-On zu aktivieren.
    > [!IMPORTANT]
    > Es ist nicht möglich, die SKU von **Standard/Premium** in **Local** zu ändern.
    
    * **Abrechnungsmodell** bestimmt den Abrechnungstyp. Sie können **Taktung** für einen Volumentarif und **Unbegrenzt** für einen Tarif mit Datenflatrate auswählen. Sie können den Abrechnungstyp von **Taktung** in **Unbegrenzt** ändern.

    > [!IMPORTANT]
    > Sie können den Typ nicht von **Unbegrenzt** in **Taktung** ändern.

    * **Klassische Vorgänge zulassen** ermöglicht, klassische virtuelle Netzwerke mit der Verbindung zu verknüpfen.

### <a name="view-the-circuits-and-properties"></a>Anzeigen von Verbindungen und Eigenschaften

**Anzeigen aller Verbindungen**

Sie können alle erstellten Verbindungen anzeigen, indem Sie im Menü auf der linken Seite **Alle Dienste > Netzwerk > ExpressRoute-Leitungen** auswählen.

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-menu.png" alt-text="ExpressRoute-Verbindungsmenü":::

Alle ExpressRoute-Verbindungen, die im Abonnement erstellt werden, werden hier angezeigt.

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-list.png" alt-text="ExpressRoute-Verbindungsliste":::

**Anzeigen der Eigenschaften**

Sie können die Eigenschaften der Verbindung anzeigen, indem Sie die Verbindung auswählen. Auf der Seite **Übersicht** Ihrer Verbindung wird der Dienstschlüssel im Dienstschlüsselfeld angezeigt. Stellen Sie den Dienstschlüssel für Ihre Verbindung dem Dienstanbieter bereit, um den Bereitstellungsprozess abzuschließen. Der Dienstschlüssel ist für Ihre Verbindung spezifisch.

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-overview.png" alt-text="Anzeigen von Eigenschaften":::

### <a name="send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>Senden Sie den Dienstschlüssel zur Bereitstellung an Ihren Konnektivitätsanbieter.

Auf dieser Seite bietet der **Anbieterstatus** Informationen zum aktuellen Zustand der Bereitstellung auf der Dienstanbieterseite. **Verbindungsstatus** gibt den Zustand auf der Microsoft-Seite an. Weitere Informationen zu den Bereitstellungszuständen einer Verbindung finden Sie im Artikel [Workflows](expressroute-workflows.md#expressroute-circuit-provisioning-states) .

Wenn Sie eine neue ExpressRoute-Verbindung erstellen, weist die Verbindung folgenden Zustand auf:

Anbieterstatus: **Nicht bereitgestellt**<BR>
Schaltkreisstatus: **Aktiviert**

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-overview-provisioning-state.png" alt-text="Startet den Bereitstellungsprozess":::

Die Verbindung wechselt in den folgenden Zustand, wenn sie vom Konnektivitätsanbieter aktuell für Sie aktiviert wird:

Anbieterstatus: **Bereitstellung**<BR>
Schaltkreisstatus: **Aktiviert**

Damit Sie eine ExpressRoute-Verbindung verwenden können, muss sie sich im folgenden Zustand befinden:

Anbieterstatus: **Bereitgestellt**<BR>
Schaltkreisstatus: **Aktiviert**

### <a name="periodically-check-the-status-and-the-state-of-the-circuit-key"></a>Überprüfen Sie regelmäßig den Status und Zustand des Verbindungsschlüssels.

Sie können die Eigenschaften der gewünschten Verbindung anzeigen, indem Sie die Verbindung auswählen. Überprüfen Sie den **Anbieterstatus**, und stellen Sie sicher, dass er in **Provisioned** geändert wurde, bevor Sie fortfahren.

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/provisioned.png" alt-text="Verbindungs- und Anbieterstatus":::

### <a name="create-your-routing-configuration"></a>Erstellen Sie die Routingkonfiguration.

Eine Schritt-für-Schritt-Anleitung zum Erstellen und Ändern von Verbindungspeerings finden Sie im Artikel [Routingkonfiguration für ExpressRoute-Verbindung](expressroute-howto-routing-portal-resource-manager.md) .

> [!IMPORTANT]
> Diese Anweisungen gelten nur für Verbindungen, die über Dienstanbieter erstellt wurden, von denen Layer 2-Konnektivitätsdienste angeboten werden. Wenn Sie einen Dienstanbieter nutzen, der verwaltete Layer 3-Dienste anbietet (meist ein IP-VPN, z.B. MPLS), übernimmt Ihr Konnektivitätsanbieter die Konfiguration und Verwaltung des Routings für Sie.

### <a name="link-a-virtual-network-to-an-expressroute-circuit"></a>Verknüpfen eines virtuellen Netzwerks mit einer ExpressRoute-Verbindung

Verknüpfen Sie anschließend ein virtuelles Netzwerk mit Ihrer ExpressRoute-Verbindung. Lesen Sie den Artikel [Verknüpfen von virtuellen Netzwerken mit ExpressRoute-Verbindungen](expressroute-howto-linkvnet-arm.md) , wenn Sie mit dem Resource Manager-Bereitstellungsmodell arbeiten.

## <a name="getting-the-status-of-an-expressroute-circuit"></a><a name="status"></a>Abrufen des Status einer ExpressRoute-Verbindung

Sie können den Status einer Verbindung anzeigen, indem Sie die Verbindung auswählen und die Übersichtsseite anzeigen.

## <a name="modifying-an-expressroute-circuit"></a><a name="modify"></a>Ändern einer ExpressRoute-Verbindung

Sie können bestimmte Eigenschaften einer ExpressRoute-Verbindung ändern, ohne die Konnektivität zu beeinträchtigen. Sie können die Bandbreite, die SKU und das Abrechnungsmodell verändern und klassische Vorgänge auf der Seite **Konfiguration** zulassen. Informationen zu Grenzwerten und Beschränkungen finden Sie unter [ExpressRoute – FAQs](expressroute-faqs.md).

Sie können folgende Aufgaben ausführen, ohne Ausfallzeiten zu verursachen:

* Aktivieren oder deaktivieren Sie ein ExpressRoute Premium-Add-On für Ihre ExpressRoute-Verbindung.

> [!IMPORTANT]
  > Die Änderung der SKU von **Standard/Premium** in **Local** wird nicht unterstützt.

* Erhöhen Sie die Bandbreite der ExpressRoute-Verbindung, sofern Kapazität am Port verfügbar ist.

  > [!IMPORTANT]
  > Ein Downgrade der Bandbreite einer Verbindung wird nicht unterstützt.

* Sie können den Abrechnungsplan von *Datentaktung* zu *Datenflatrate* ändern.

  > [!IMPORTANT]
  > Ein Ändern des Abrechnungsplans von **Datenflatrate** in **Datentaktung** (Volumentarif) wird nicht unterstützt.

* Sie können die Option *Klassische Vorgänge zulassen* aktivieren und deaktivieren.
  > [!IMPORTANT]
  > Unter Umständen müssen Sie die ExpressRoute-Verbindung neu erstellen, wenn nicht ausreichend Kapazität am vorhandenen Port verfügbar ist. Die Verbindung kann nicht aktualisiert werden, wenn an dieser Stelle keine zusätzliche Kapazität verfügbar ist.
  >
  > Sie können zwar nahtlos die Bandbreite erhöhen, es ist jedoch nicht möglich, die Bandbreite einer ExpressRoute-Verbindung ohne Störungen zu reduzieren. Ein Downgrade der Bandbreite erfordert, dass Sie die Bereitstellung der ExpressRoute-Verbindung aufheben und dann eine neue ExpressRoute-Verbindung bereitstellen.
  >
  > Beim Deaktivieren von Premium-Add-On-Vorgängen kann ein Fehler auftreten, wenn Sie Ressourcen verwenden, die die zulässige Menge für die Standardverbindung überschreiten.

Wählen Sie **Konfiguration** aus, um eine ExpressRoute-Verbindung zu bearbeiten.

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-configuration.png" alt-text="Ändern der Verbindung":::

## <a name="deprovisioning-an-expressroute-circuit"></a><a name="delete"></a>Aufheben der Bereitstellung einer ExpressRoute-Verbindung

Wenn der Bereitstellungsstatus des ExpressRoute-Verbindungsdienstanbieters **Bereitstellung** oder **Bereitgestellt** lautet, arbeiten Sie mit Ihrem Dienstanbieter zusammen, um die Verbindungsbereitstellung auf Anbieterseite aufzuheben. Microsoft reserviert weiterhin Ressourcen für Sie und stellt Ihnen dies in Rechnung, bis der Dienstanbieter die Aufhebung der Verbindungsbereitstellung abgeschlossen hat und uns benachrichtigt.

> [!NOTE]
>* Sie müssen die Verknüpfung *aller virtuellen Netzwerke* mit der ExpressRoute-Verbindung vor dem Aufheben der Bereitstellung aufheben. Falls dieser Vorgang nicht erfolgreich ist, überprüfen Sie, ob noch virtuelle Netzwerke mit der Verbindung verknüpft sind.
>* Wenn der Dienstanbieter die Bereitstellung der Verbindung aufgehoben hat (Bereitstellungsstatus des Dienstanbieters lautet **Nicht bereitgestellt**), können Sie die Verbindung löschen. Damit wird die Abrechnung für die Verbindung beendet.


## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Sie können Ihre ExpressRoute-Verbindung löschen. Wählen Sie hierzu das Symbol **Löschen**. Sorgen Sie dafür, dass der Bereitstellungsstatus *Nicht bereitgestellt* lautet, bevor Sie fortfahren.

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-delete.png" alt-text="Löschen der Verbindung":::

## <a name="next-steps"></a>Nächste Schritte

Führen Sie nach dem Erstellen Ihrer Verbindung den folgenden nächsten Schritt durch:

> [!div class="nextstepaction"]
> [Erstellen und Ändern des Routings für Ihre ExpressRoute-Verbindung](expressroute-howto-routing-portal-resource-manager.md)
