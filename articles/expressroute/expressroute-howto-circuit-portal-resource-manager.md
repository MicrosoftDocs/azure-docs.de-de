---
title: 'Erstellen und Ändern einer ExpressRoute-Verbindung – Portal: Azure | Microsoft-Dokumentation'
description: Erstellen, Bereitstellen, Überprüfen, Aktualisieren, Löschen und Aufheben der Bereitstellung einer ExpressRoute-Verbindung.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 10/20/2018
ms.author: cherylmc;ganesr
ms.custom: seodec18
ms.openlocfilehash: 16f3ad1aa037dca2e7b8c3e68ae952c27b952711
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60366481"
---
# <a name="create-and-modify-an-expressroute-circuit"></a>Erstellen und Ändern einer ExpressRoute-Verbindung

> [!div class="op_single_selector"]
> * [Azure-Portal](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [Azure-Befehlszeilenschnittstelle](howto-circuit-cli.md)
> * [Video – Azure-Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (klassisch)](expressroute-howto-circuit-classic.md)
>

In diesem Artikel wird beschrieben, wie Sie eine ExpressRoute-Verbindung mithilfe des Azure-Portals und des Azure Resource Manager-Bereitstellungsmodells erstellen. Außerdem können Sie den Status einer Verbindung überprüfen, die Verbindung aktualisieren oder löschen oder ihre Bereitstellung aufheben.

## <a name="before-you-begin"></a>Voraussetzungen

* Lesen Sie vor Beginn der Konfiguration die Seiten zu den [Voraussetzungen](expressroute-prerequisites.md) und [Workflows](expressroute-workflows.md).
* Stellen Sie sicher, dass Sie auf das [Azure-Portal](https://portal.azure.com)zugreifen können.
* Stellen Sie sicher, dass Sie über die notwendigen Berechtigungen verfügen, um neue Netzwerkressourcen zu erstellen. Wenden Sie sich an Ihren Kontoadministrator, wenn Sie nicht über die richtigen Berechtigungen verfügen.
* Sie können sich das [Video ansehen](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit), bevor Sie beginnen, um die Schritte besser zu verstehen.

## <a name="create"></a>Erstellen und Bereitstellen einer ExpressRoute-Verbindung

### <a name="1-sign-in-to-the-azure-portal"></a>1. Melden Sie sich auf dem Azure-Portal an.

Navigieren Sie in einem Browser zum [Azure-Portal](https://portal.azure.com) , und melden Sie sich mit Ihrem Azure-Konto an.

### <a name="2-create-a-new-expressroute-circuit"></a>2. Erstellen Sie eine neue ExpressRoute-Verbindung.

> [!IMPORTANT]
> Ihre ExpressRoute-Verbindung wird von dem Moment an berechnet, in dem ein Dienstschlüssel ausgegeben wird. Stellen Sie sicher, dass Sie diesen Vorgang ausführen, sobald der Konnektivitätsanbieter dazu bereit ist, die Verbindung bereitzustellen.

1. Sie können eine ExpressRoute-Verbindung erstellen, indem Sie die Option zum Erstellen einer neuen Ressource auswählen. Klicken Sie auf **Ressource erstellen** > **Netzwerk** > **ExpressRoute**, wie in der Abbildung unten gezeigt:

   ![Erstellen Sie eine ExpressRoute-Verbindung.](./media/expressroute-howto-circuit-portal-resource-manager/createcircuit1.png)
2. Nachdem Sie auf **ExpressRoute** geklickt haben, wird die Seite **ExpressRoute-Verbindung erstellen** angezeigt. Wenn Sie die Werte auf der Seite ausfüllen, stellen Sie sicher, dass Sie den richtigen SKU-Tarif (Standard oder Premium) sowie das richtige Abrechnungsmodell für die Datenmessung (Unbegrenzt oder Taktung) angeben.

   ![Konfigurieren von SKU-Tarif und Datenmessung](./media/expressroute-howto-circuit-portal-resource-manager/createcircuit.png)

   * Der **Tarif** bestimmt, ob ein ExpressRoute Standard- oder ein ExpressRoute Premium-Add-On aktiviert wird. Sie können **Standard** für die Standard-SKU bzw. **Premium** für das Premium-Add-On angeben.
   * **Datenabrechnung** bestimmt den Abrechnungstyp. Sie können **Taktung** für einen Volumentarif und **Unbegrenzt** für einen Tarif mit Datenflatrate auswählen. Beachten Sie, dass Sie den Abrechnungstyp von **Taktung** in **Unbegrenzt** ändern können.

     > [!IMPORTANT]
     > Sie können den Typ nicht von **Unbegrenzt** in **Taktung** ändern.

   * Der **Peeringort** ist der physische Standort, an dem Ihr Peering mit Microsoft stattfindet.

     > [!IMPORTANT]
     > Der Peeringstandort entspricht dem [physischen Standort](expressroute-locations.md), an dem Ihr Peering mit Microsoft stattfindet. Dieser ist **nicht** mit der Eigenschaft „Standort“ verknüpft, die sich auf den geografischen Standort des Azure-Netzwerkressourcenanbieters befindet. Obgleich sie nicht miteinander in Zusammenhang stehen, sollten Sie einen Netzwerkressourcenanbieter in geografischer Nähe des Peeringstandorts der Verbindung wählen.

### <a name="3-view-the-circuits-and-properties"></a>3. Anzeigen von Verbindungen und Eigenschaften

**Anzeigen aller Verbindungen**

Sie können alle erstellten Verbindungen anzeigen, indem Sie im Menü auf der linken Seite **Alle Ressourcen** auswählen.

![Verbindungen anzeigen](./media/expressroute-howto-circuit-portal-resource-manager/listresource.png)

**Anzeigen der Eigenschaften**

Sie können die Eigenschaften der Verbindung anzeigen, indem Sie die Verbindung auswählen. Auf der Seite **Übersicht** Ihrer Verbindung wird der Dienstschlüssel im Dienstschlüsselfeld angezeigt. Sie müssen den Dienstschlüssel für Ihre Verbindung kopieren und ihn an den Dienstanbieter übergeben, um den Bereitstellungsprozess abzuschließen. Der Verbindungsdienstschlüssel ist für Ihre Verbindung spezifisch.

![Eigenschaften anzeigen](./media/expressroute-howto-circuit-portal-resource-manager/servicekey1.png)

### <a name="4-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>4. Senden Sie den Dienstschlüssel zur Bereitstellung an Ihren Konnektivitätsanbieter.

Auf dieser Seite bietet der **Anbieterstatus** Informationen zum aktuellen Zustand der Bereitstellung auf der Dienstanbieterseite. **Schaltkreisstatus** wird der Zustand auf Microsoft-Seite angegeben. Weitere Informationen zu den Bereitstellungszuständen einer Verbindung finden Sie im Artikel [Workflows](expressroute-workflows.md#expressroute-circuit-provisioning-states) .

Wenn Sie eine neue ExpressRoute-Verbindung erstellen, weist die Verbindung folgenden Zustand auf:

Anbieterstatus: Nicht bereitgestellt<BR>
Schaltkreisstatus: Enabled

![Bereitstellung initiieren](./media/expressroute-howto-circuit-portal-resource-manager/status.png)

Die Verbindung wechselt in den folgenden Zustand, wenn sie vom Konnektivitätsanbieter für Sie aktiviert wird:

Anbieterstatus: Bereitstellung<BR>
Schaltkreisstatus: Enabled

Damit Sie eine ExpressRoute-Verbindung verwenden können, muss sie sich im folgenden Zustand befinden:

Anbieterstatus: Bereitgestellt<BR>
Schaltkreisstatus: Enabled

### <a name="5-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>5. Überprüfen Sie regelmäßig den Status und Zustand des Verbindungsschlüssels.

Sie können die Eigenschaften der gewünschten Verbindung anzeigen, indem Sie die Verbindung auswählen. Überprüfen Sie den **Anbieterstatus**, und stellen Sie sicher, dass er in **Provisioned** geändert wurde, bevor Sie fortfahren.

![Verbindungs- und Anbieterstatus](./media/expressroute-howto-circuit-portal-resource-manager/provisioned.png)

### <a name="6-create-your-routing-configuration"></a>6. Erstellen Sie die Routingkonfiguration.

Eine Schritt-für-Schritt-Anleitung zum Erstellen und Ändern von Verbindungspeerings finden Sie im Artikel [Routingkonfiguration für ExpressRoute-Verbindung](expressroute-howto-routing-portal-resource-manager.md) .

> [!IMPORTANT]
> Diese Anweisungen gelten nur für Verbindungen, die über Dienstanbieter erstellt wurden, von denen Layer 2-Konnektivitätsdienste angeboten werden. Wenn Sie einen Dienstanbieter nutzen, der verwaltete Layer 3-Dienste anbietet (meist ein IP-VPN, z.B. MPLS), übernimmt Ihr Konnektivitätsanbieter die Konfiguration und Verwaltung des Routings für Sie.

### <a name="7-link-a-virtual-network-to-an-expressroute-circuit"></a>7. Verknüpfen eines virtuellen Netzwerks mit einer ExpressRoute-Verbindung

Verknüpfen Sie anschließend ein virtuelles Netzwerk mit Ihrer ExpressRoute-Verbindung. Lesen Sie den Artikel [Verknüpfen von virtuellen Netzwerken mit ExpressRoute-Verbindungen](expressroute-howto-linkvnet-arm.md) , wenn Sie mit dem Resource Manager-Bereitstellungsmodell arbeiten.

## <a name="status"></a>Abrufen des Status einer ExpressRoute-Verbindung

Sie können den Status einer Verbindung anzeigen, indem Sie die Verbindung auswählen und die Übersichtsseite anzeigen.

## <a name="modify"></a>Ändern einer ExpressRoute-Verbindung

Sie können bestimmte Eigenschaften einer ExpressRoute-Verbindung ändern, ohne die Konnektivität zu beeinträchtigen. Sie können die Bandbreite, die SKU und das Abrechnungsmodell verändern und klassische Vorgänge auf der Seite **Konfiguration** zulassen. Informationen zu Grenzwerten und Beschränkungen finden Sie unter [ExpressRoute – FAQs](expressroute-faqs.md). 

Sie können folgende Aufgaben ausführen, ohne Ausfallzeiten zu verursachen:

* Aktivieren oder deaktivieren Sie ein ExpressRoute Premium-Add-On für Ihre ExpressRoute-Verbindung.
* Erhöhen Sie die Bandbreite der ExpressRoute-Verbindung, sofern Kapazität am Port vorhanden ist.

  > [!IMPORTANT]
  > Ein Downgrade der Bandbreite einer Verbindung wird nicht unterstützt.

* Sie können den Abrechnungsplan von *Datentaktung* zu *Datenflatrate* ändern.

  > [!IMPORTANT]
  > Ein Ändern des Abrechnungsplans von „Datenflatrate“ in „Datentaktung“ (Volumentarif) wird nicht unterstützt.

* Sie können die Option *Klassische Vorgänge zulassen*aktivieren und deaktivieren.
  > [!IMPORTANT]
  > Unter Umständen müssen Sie die ExpressRoute-Verbindung neu erstellen, wenn nicht ausreichend Kapazität am vorhandenen Port verfügbar ist. Die Verbindung kann nicht aktualisiert werden, wenn an dieser Stelle keine zusätzliche Kapazität verfügbar ist.
  > 
  > Sie können zwar nahtlos die Bandbreite erhöhen, es ist jedoch nicht möglich, die Bandbreite einer ExpressRoute-Verbindung ohne Störungen zu reduzieren. Ein Downgrade der Bandbreite erfordert, dass Sie die Bereitstellung der ExpressRoute-Verbindung aufheben und dann eine neue ExpressRoute-Verbindung bereitstellen.
  > 
  > Beim Deaktivieren von Premium-Add-On-Vorgängen kann ein Fehler auftreten, wenn Sie Ressourcen verwenden, die die zulässige Menge für die Standardverbindung überschreiten.

Klicken Sie auf **Konfiguration**, um eine ExpressRoute-Verbindung zu bearbeiten.

![Ändern der Verbindung](./media/expressroute-howto-circuit-portal-resource-manager/modifycircuit.png)

## <a name="delete"></a>Aufheben der Bereitstellung und Löschen einer ExpressRoute-Verbindung

Sie können Ihre ExpressRoute-Verbindung löschen. Wählen Sie dazu das Symbol **Löschen** aus. Beachten Sie die folgenden Informationen:

* Sie müssen die Verknüpfung aller virtuellen Netzwerke mit der ExpressRoute-Verbindung aufheben. Falls dieser Vorgang nicht erfolgreich ist, überprüfen Sie, ob noch virtuelle Netzwerke mit der Verbindung verknüpft sind.
* Wenn der Bereitstellungsstatus des ExpressRoute-Verbindungsdienstanbieters **Bereitstellung** oder **Bereitgestellt** lautet, arbeiten Sie mit Ihrem Dienstanbieter zusammen, um die Verbindungsbereitstellung auf Anbieterseite aufzuheben. Microsoft reserviert weiterhin Ressourcen für Sie und stellt Ihnen dies in Rechnung, bis der Dienstanbieter die Aufhebung der Verbindungsbereitstellung abgeschlossen hat und uns benachrichtigt.
* Wenn der Dienstanbieter die Bereitstellung der Verbindung aufgehoben hat (Bereitstellungsstatus des Dienstanbieters lautet **Nicht bereitgestellt**), können Sie die Verbindung löschen. Damit wird die Abrechnung für die Verbindung beendet.

## <a name="next-steps"></a>Nächste Schritte

Führen Sie nach dem Erstellen Ihrer Verbindung folgende Aufgaben durch:

* [Erstellen und Ändern des Routings für Ihre ExpressRoute-Verbindung](expressroute-howto-routing-portal-resource-manager.md)
* [Verknüpfen Ihres virtuelles Netzwerks mit Ihrer ExpressRoute-Verbindung](expressroute-howto-linkvnet-arm.md)
