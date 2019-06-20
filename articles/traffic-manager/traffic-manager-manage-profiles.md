---
title: Verwalten von Azure Traffic Manager-Profilen | Microsoft Docs
description: In diesem Artikel wird beschrieben, wie Sie ein Azure Traffic Manager-Profil erstellen, deaktivieren, aktivieren und löschen.
services: traffic-manager
documentationcenter: ''
author: asudbring
ms.service: traffic-manager
manager: twooley
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/10/2017
ms.author: allensu
ms.openlocfilehash: 8ec30a4d3f02505e764cd6f8dcec42c56d11ed27
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67071095"
---
# <a name="manage-an-azure-traffic-manager-profile"></a>Verwalten von Azure Traffic Manager-Profilen

Für Traffic Manager-Profile werden Datenverkehr-Routingmethoden verwendet, um die Verteilung von Datenverkehr auf Ihre Clouddienste oder Websiteendpunkte zu steuern. In diesem Artikel wird beschrieben, wie Sie diese Profile erstellen und verwalten.

## <a name="create-a-traffic-manager-profile"></a>Erstellen eines Traffic Manager-Profils

Sie können ein Traffic Manager-Profil mit dem Azure-Portal erstellen. Nach dem Erstellen Ihres Profils können Sie Endpunkte, Überwachung und andere Einstellungen im Azure-Portal konfigurieren. Traffic Manager unterstützt bis zu 200 Endpunkte pro Profil. Für die meisten Verwendungsszenarien sind aber nur einige Endpunkte erforderlich.

### <a name="to-create-a-traffic-manager-profile"></a>So erstellen Sie ein Traffic Manager-Profil

1. Melden Sie sich im Browser beim [Azure-Portal](https://portal.azure.com) an. Falls Sie noch nicht über ein Azure-Konto verfügen, können Sie sich für eine [kostenlose einmonatige Testversion](https://azure.microsoft.com/free/) registrieren. 
2. Klicken Sie auf **Ressource erstellen** > **Netzwerk** > **Traffic Manager-Profil** > **Erstellen**.
4. Füllen Sie den Bereich **Traffic Manager-Profil erstellen** wie folgt aus:
    1. Geben Sie im Feld **Name** einen Namen für Ihr Profil ein. Dieser Name muss innerhalb der Zone „trafficmanager.net“ eindeutig sein und ergibt den DNS-Namen `<name>`.trafficmanager.net, der für den Zugriff auf Ihr Traffic Manager-Profil verwendet wird.
    2. Wählen Sie unter **Routingmethode** die Routingmethode **Priorität** aus.
    3. Wählen Sie unter **Abonnement** das Abonnement aus, unter dem Sie dieses Profil erstellen möchten
    4. Erstellen Sie unter **Ressourcengruppe** eine neue Ressourcengruppe, unter der Sie das Profil platzieren möchten.
    5. Wählen Sie unter **Ressourcengruppenstandort** den Speicherort für die Ressourcengruppe aus. Diese Einstellung bezieht sich auf den Speicherort der Ressourcengruppe und hat keine Auswirkungen auf das Traffic Manager-Profil, das global bereitgestellt wird.
    6. Klicken Sie auf **Create**.
    7. Wenn die globale Bereitstellung Ihres Traffic Manager-Profils abgeschlossen ist, wird sie in der betreffenden Ressourcengruppe als eine der Ressourcen aufgelistet.

## <a name="disable-enable-or-delete-a-profile"></a>Deaktivieren, Aktivieren oder Löschen von Profilen

Sie können ein vorhandenes Profil deaktivieren, damit Benutzeranforderungen von Traffic Manager nicht an die konfigurierten Endpunkte geleitet werden. Wenn Sie ein Traffic Manager-Profil deaktivieren, bleiben das Profil und die Informationen im Profil erhalten und können auf der Traffic Manager-Benutzeroberfläche bearbeitet werden.  Die Weiterleitungen werden fortgesetzt, wenn Sie das Profil wieder aktivieren. Wenn Sie ein Traffic Manager-Profil im Azure-Portal erstellen, wird es automatisch aktiviert. Falls ein Profil nicht mehr erforderlich ist, können Sie es löschen.

### <a name="to-disable-a-profile"></a>So deaktivieren Sie ein Profil

1. Ändern Sie bei Verwendung eines benutzerdefinierten Domänennamens den CNAME-Eintrag auf Ihrem Internet-DNS-Server, damit er nicht mehr auf Ihr Traffic Manager-Profil verweist.
2. Der Datenverkehr wird nicht mehr über die Traffic Manager-Profileinstellungen an die Endpunkte weitergeleitet.
3. Melden Sie sich im Browser beim [Azure-Portal](https://portal.azure.com) an.
2. Suchen Sie über die Suchleiste des Portals nach dem Namen des **Traffic Manager-Profils**, das Sie ändern möchten, und klicken Sie in den angezeigten Ergebnissen auf das Traffic Manager-Profil.
3. Klicken Sie auf **Übersicht** > **Deaktivieren**.
4. Bestätigen Sie die Deaktivierung des Traffic Manager-Profils.

### <a name="to-enable-a-profile"></a>So aktivieren Sie ein Profil

1. Melden Sie sich im Browser beim [Azure-Portal](https://portal.azure.com) an.
2. Suchen Sie über die Suchleiste des Portals nach dem Namen des **Traffic Manager-Profils**, das Sie ändern möchten, und klicken Sie in den angezeigten Ergebnissen auf das Traffic Manager-Profil.
3. Klicken Sie auf **Übersicht** > **Aktivieren**.
1. Wenn Sie einen benutzerdefinierten Domänennamen verwenden, sollten Sie einen CNAME-Ressourceneintrag auf Ihrem Internet-DNS-Server erstellen, der auf den Domänennamen Ihres Traffic Manager-Profils verweist.
2. Der Datenverkehr wird wieder an die Endpunkte geleitet.

### <a name="to-delete-a-profile"></a>So löschen Sie ein Profil

1. Ändern Sie den DNS-Ressourceneintrag auf dem Internet-DNS-Server so, dass er nicht mehr einen CNAME-Ressourceneintrag verwendet, der auf den Domänennamen Ihres Traffic Manager-Profils verweist.
2. Suchen Sie über die Suchleiste des Portals nach dem Namen des **Traffic Manager-Profils**, das Sie ändern möchten, und klicken Sie in den angezeigten Ergebnissen auf das Traffic Manager-Profil.
3. Klicken Sie auf **Übersicht** > **Löschen**.
4. Bestätigen Sie die Löschung des Traffic Manager-Profils.

## <a name="next-steps"></a>Nächste Schritte

* [Hinzufügen eines Endpunkts](traffic-manager-endpoints.md)
* [Konfigurieren einer vorrangigen Routingmethode](traffic-manager-configure-priority-routing-method.md)
* [Konfigurieren einer geografischen Routingmethode](traffic-manager-configure-geographic-routing-method.md) 
* [Konfigurieren einer gewichteten Routingmethode](traffic-manager-configure-weighted-routing-method.md)
* [Konfigurieren der leistungsorientierten Routingmethode](traffic-manager-configure-performance-routing-method.md)
