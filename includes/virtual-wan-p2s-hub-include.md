---
title: include file
description: include file
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 10/06/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 134f1dc7cb6e53c181b2f518055e5cb758fccf31
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91812720"
---
1. Wählen Sie unter Ihrem virtuellen WAN „Hubs“ und dann **+ Neuer Hub** aus.

   :::image type="content" source="media/virtual-wan-p2s-hub/new-hub.jpg" alt-text="Neuer Hub":::

1. Füllen Sie auf der Seite zum Erstellen virtueller Hubs die folgenden Felder aus.

   * **Region**: Wählen Sie die Region aus, in der Sie den virtuellen Hub bereitstellen möchten.
   * **Name**: Geben Sie den Namen ein, den Sie Ihrem virtuellen Hub zuweisen möchten.
   * **Privater Adressraum des Hubs**: Der Adressbereich des Hubs in CIDR-Notation.

   :::image type="content" source="media/virtual-wan-p2s-hub/create-hub.jpg" alt-text="Virtuellen Hub erstellen":::

1. Füllen Sie auf der Registerkarte „Point-to-Site“ die folgenden Felder aus:

   * **Gatewayskalierungseinheiten**: Steht für die Aggregatkapazität des Benutzer-VPN-Gateways.
   * **Point-to-Site-Konfiguration**: Diese Konfiguration haben Sie im vorherigen Schritt erstellt.
   * **Clientadresspool**: Für die Remotebenutzer bestimmt.
   * **IP-Adresse des benutzerdefinierten DNS-Servers**.

   :::image type="content" source="media/virtual-wan-p2s-hub/hub-with-p2s.png" alt-text="Hub mit Point-to-Site":::

1. Klicken Sie auf **Überprüfen + erstellen**.
1. Wählen Sie auf der Seite **Überprüfung erfolgreich** die Option **Erstellen** aus.