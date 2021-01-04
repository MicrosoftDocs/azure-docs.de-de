---
title: Konfigurieren des „MultiValue“-Routings für Datenverkehr – Azure Traffic Manager
description: In diesem Artikel wird erläutert, wie Traffic Manager so konfiguriert wird, dass Datenverkehr an A/AAAA-Endpunkte geleitet wird.
services: traffic-manager
documentationcenter: ''
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: duau
ms.openlocfilehash: 85e088dda767a6f6c80ac0a9f6eed84e8802e5ee
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95994926"
---
# <a name="configure-multivalue-routing-method-in-traffic-manager"></a>Konfigurieren der Routingmethode „MultiValue“ in Traffic Manager

In diesem Artikel wird das Konfigurieren der Routingmethode „MultiValue“ für Datenverkehr beschrieben. Mit der Routingmethode **MultiValue** für Datenverkehr können mehrere fehlerfreie Endpunkte zurückgegeben werden, was dazu beiträgt, die Zuverlässigkeit Ihrer Anwendung zu erhöhen, da Clients mehr Möglichkeiten haben, wiederholte Versuche ohne eine erneute DNS-Suche durchzuführen. Das MultiValue-Routing ist nur für Profile aktiviert, bei denen alle Endpunkte über IPv4- oder IPv6-Adressen angegeben sind. Wenn eine Abfrage für dieses Profil empfangen wird, werden alle fehlerfreien Endpunkte basierend auf der angegebenen konfigurierbaren maximalen Rückgabeanzahl zurückgegeben. 

>[!NOTE]
> Derzeit wird das Hinzufügen von Endpunkten mit IPv4- oder IPv6-Adressen nur für Endpunkte vom Typ **Extern** unterstützt, sodass auch MultiValue-Routing nur für solche Endpunkte unterstützt wird.

## <a name="sign-in-to-azure"></a>Anmelden bei Azure 

Melden Sie sich unter https://portal.azure.com beim Azure-Portal an.
## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe
Erstellen Sie eine Ressourcengruppe für das Traffic Manager-Profil.
1. Wählen Sie auf der linken Seite im Azure-Portal **Ressourcengruppen** aus.
2. Klicken Sie unter **Ressourcengruppen** oben auf der Seite auf **Hinzufügen**.
3. Geben Sie unter **Ressourcengruppenname** den Namen *myResourceGroupTM1* ein. Wählen Sie für **Ressourcengruppenstandort** die Option **USA, Osten** aus, und klicken Sie dann auf **OK**.

## <a name="create-a-traffic-manager-profile"></a>Erstellen eines Traffic Manager-Profils
Erstellen Sie ein Traffic Manager-Profil, das den Benutzerdatenverkehr weiterleitet, indem es ihn an den Endpunkt mit der niedrigsten Wartezeit sendet.

1. Klicken Sie links oben auf dem Bildschirm auf **Ressource erstellen** > **Netzwerk** > **Traffic Manager-Profil** > **Erstellen**.
2. Geben Sie unter **Traffic Manager-Profil erstellen** die folgenden Informationen ein, oder wählen Sie sie aus, übernehmen Sie die Standardwerte für die übrigen Einstellungen, und klicken Sie auf **Erstellen**:
    
    | Einstellung                 | value                                              |
    | ---                     | ---                                                |
    | Name                   | Dieser Name muss innerhalb der Zone „trafficmanager.net“ eindeutig sein und ergibt den DNS-Namen „trafficmanager.net“, der für den Zugriff auf Ihr Traffic Manager-Profil verwendet wird.                                   |
    | Routingmethode          | Wählen Sie die Routingmethode **MultiValue** aus.                                       |
    | Subscription            | Wählen Sie Ihr Abonnement aus.                          |
    | Resource group          | Wählen Sie *myResourceGroupTM1* aus. |
    | Location                | Diese Einstellung bezieht sich auf den Speicherort der Ressourcengruppe und hat keine Auswirkungen auf das Traffic Manager-Profil, das global bereitgestellt wird.                              |
   |        |           | 
  
   ![Erstellen eines Traffic Manager-Profils](./media/traffic-manager-multivalue-routing-method/create-traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>Hinzufügen von Traffic Manager-Endpunkten

Fügen Sie zwei IP-Adressen als externe Endpunkte zum Traffic Manager-Profil „MultiValue“ hinzu, das Sie im vorherigen Schritt erstellt haben.

1. Suchen Sie in der Suchleiste des Portals nach dem Namen des Traffic Manager-Profils, das Sie im vorhergehenden Abschnitt erstellt haben, und wählen Sie in den angezeigten Ergebnissen das Profil aus.
2. Klicken Sie unter **Traffic Manager-Profil** im Abschnitt **Einstellungen** auf **Endpunkte** und dann auf **Hinzufügen**.
3. Geben Sie die folgenden Informationen ein, oder wählen Sie sie aus, übernehmen Sie die Standardwerte für die übrigen Einstellungen, und klicken Sie auf **OK**:

    | Einstellung                 | value                                              |
    | ---                     | ---                                                |
    | type                    | Externer Endpunkt                                   |
    | Name           | myEndpoint1                                        |
    | Vollqualifizierter Domänenname (FQDN) oder IP           | Geben Sie die öffentliche IP-Adresse des Endpunkts ein, den Sie diesem Traffic Manager-Profil hinzufügen möchten.                         |
    |        |           |

4. Wiederholen Sie die Schritte 2 und 3, um einen weiteren Endpunkt mit dem Namen *myEndpunkt2* hinzuzufügen, und geben Sie für **Vollqualifizierter Domänenname (FQDN) oder IP** die öffentliche IP-Adresse des zweiten Endpunkts ein.
5. Wenn Sie das Hinzufügen beider Endpunkte abgeschlossen haben, werden diese unter **Traffic Manager-Profil** zusammen mit ihrem Überwachungsstatus als **Online** angezeigt.

   ![Hinzufügen eines Traffic Manager-Endpunkts](./media/traffic-manager-multivalue-routing-method/add-endpoint.png)
 
## <a name="next-steps"></a>Nächste Schritte

- Informationen zur [gewichteten Routingmethode für Datenverkehr](traffic-manager-configure-weighted-routing-method.md).
- Informationen zur [prioritätsbasierten Routingmethode](traffic-manager-configure-priority-routing-method.md).
- Weitere Informationen zur [Leistungsorientierten Routingmethode](traffic-manager-configure-performance-routing-method.md)
- Informationen zur [geografischen Routingmethode](traffic-manager-configure-geographic-routing-method.md).


