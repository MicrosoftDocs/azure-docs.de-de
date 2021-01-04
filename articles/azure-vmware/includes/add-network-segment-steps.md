---
title: Hinzufügen eines NSX-T-Netzwerksegments
description: Schritte zum Hinzufügen eines NSX-T-Netzwerksegments für Azure VMware Solution
ms.topic: include
ms.date: 11/09/2020
ms.openlocfilehash: 5b97f0b280fa12eff39c9601bb73e439dba8e9fd
ms.sourcegitcommit: 2a8a53e5438596f99537f7279619258e9ecb357a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2020
ms.locfileid: "94335064"
---
<!-- Used in manage-dhcp.md and tutorial-nsx-t-network-segment.md -->

1. Wählen Sie in NSX-T Manager die Optionen **Networking** > **Segments** (Netzwerk > Segmente) und dann **Add Segment** (Segment hinzufügen) aus. 

   :::image type="content" source="../media/nsxt/nsxt-segments-overview.png" alt-text="Screenshot: Hinzufügen eines neuen Segments":::

1. Wählen Sie **Add Segment** (Segment hinzufügen) aus, und geben Sie einen Namen für das Segment ein.

1. Wählen Sie das Tier1-Gateway (TNTxx-T1) als **Connected Gateway** (Verbundenes Gateway) aus, und belassen Sie den **Typ** „Flexible“ (Flexibel).

1. Wählen Sie die vorkonfigurierte **Transportzone** für die Überlagerung (TNTxx-OVERLAY-TZ) und anschließend **Set Subnets** (Subnetze festlegen) aus. 

   :::image type="content" source="../media/nsxt/nsxt-create-segment-specs.png" alt-text="Festlegen von Segmentname, verbundenem Gateway und Typ sowie Transportzone und Auswählen von „Set Subnets“ (Subnetze festlegen)":::

1. Geben Sie die IP-Adresse des Gateways ein, und wählen Sie dann **Add** (Hinzufügen) aus. 

   >[!IMPORTANT]
   >Die IP-Adresse muss Teil eines nicht überlappenden RFC1918-Adressblocks sein. Dadurch wird eine Verbindung mit den virtuellen Computern im neuen Segment sichergestellt.

   :::image type="content" source="../media/nsxt/nsxt-create-segment-gateway.png" alt-text="Legen Sie die IP-Adresse des Gateways für das neue Segment fest, und wählen Sie dann „ADD“ (HINZUFÜGEN) aus.":::

1. Wählen Sie **Apply** (Übernehmen) und dann **Save** (Speichern) aus.

1. Wählen Sie **No** (Nein) aus, um die Option zur Fortsetzung der Konfiguration des Segments abzulehnen. 

   :::image type="content" source="../media/nsxt/nsxt-create-segment-continue-no.png" alt-text="Ablehnen der weiteren Konfiguration des neu erstellten Netzwerksegments durch Auswählen von „NO“ (NEIN)":::

1. Bestätigen Sie, dass das neue Netzwerksegment vorhanden ist. In diesem Beispiel ist **ls01** das neue Netzwerksegment.

   1. Wählen Sie in NSX-T Manager die Optionen **Networking** > **Segments** (Netzwerk > Segmente) aus. 

      :::image type="content" source="../media/nsxt/nsxt-new-segment-overview-2.png" alt-text="Überprüfen, ob das neue Netzwerksegment in NSX-T vorhanden ist":::

   1. Wählen Sie in vCenter die Option **Networking > SDDC-Datacenter** (Netzwerk > SDDC-Datacenter) aus.

      :::image type="content" source="../media/nsxt/vcenter-with-ls01-2.png" alt-text="Überprüfen, ob das neue Netzwerksegment in vCenter vorhanden ist":::