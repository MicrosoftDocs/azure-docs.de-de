---
title: 'Tutorial: Konfigurieren von geografischem Routing für Datenverkehr mit Azure Traffic Manager'
description: In diesem Tutorial wird die Konfiguration der geografischen Routingmethode für Datenverkehr mit Azure Traffic Manager beschrieben.
services: traffic-manager
author: duongau
manager: kumudD
ms.service: traffic-manager
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/15/2020
ms.author: duau
ms.openlocfilehash: 29b3cdde328a994e5806df810db15b529a6da9af
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96188665"
---
# <a name="tutorial-configure-the-geographic-traffic-routing-method-using-traffic-manager"></a>Tutorial: Konfigurieren der geografischen Routingmethode für Datenverkehr mithilfe von Traffic Manager

Die geografische Routingmethode für Datenverkehr ermöglicht das Leiten von Datenverkehr an bestimmte Endpunkte auf der Grundlage des geografischen Ursprungsorts von Anforderungen. Dieses Tutorial zeigt, wie ein Traffic Manager-Profil mit dieser Routingmethode erstellt wird und die Endpunkte für den Empfang von Datenverkehr von angegebenen Geografien konfiguriert werden.

In diesem Tutorial lernen Sie, wie die folgenden Aufgaben ausgeführt werden:
> [!div class="checklist"]
> - Erstellen eines Traffic Manager-Profils mit geografischem Routing
> - Konfigurieren eines geschachtelten Endpunkts
> - Verwenden des Traffic Manager-Profils
> - Löschen des Traffic Manager-Profils

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-traffic-manager-profile"></a>Erstellen eines Traffic Manager-Profils

1. Melden Sie sich im Browser beim [Azure-Portal](https://portal.azure.com) an.

1. Wählen Sie im linken Bereich **+ Ressource erstellen** aus. Suchen Sie nach **Traffic Manager-Profil**, und wählen Sie **Erstellen** aus.

    :::image type="content" source="./media/traffic-manager-geographic-routing-method/create-traffic-manager.png" alt-text="Erstellen eines Traffic Manager-Profils":::

1. Definieren Sie auf der Seite *Traffic Manager-Profil erstellen* die folgenden Einstellungen:

    | Einstellung         | Wert                                              |
    | ---             | ---                                                |
    | Name            | Geben Sie einen Namen für Ihr Profil an. Dieser Name muss innerhalb der Zone trafficmanager.net eindeutig sein. Um auf das Traffic Manager-Profil zuzugreifen, verwenden Sie den DNS-Namen `<profilename>.trafficmanager.net`. |    
    | Routingmethode  | Wählen Sie **Geografisch** aus. |
    | Subscription    | Wählen Sie Ihr Abonnement aus. |
    | Resource group   | Verwenden Sie eine vorhandene Ressourcengruppe, oder erstellen Sie eine neue Ressourcengruppe, unter der Sie das Profil platzieren möchten. Wenn Sie sich für die Erstellung einer neuen Ressourcengruppe entscheiden, verwenden Sie die Dropdownliste *Ressourcengruppenstandort*, um den Speicherort der Ressourcengruppe anzugeben. Diese Einstellung bezieht sich auf den Speicherort der Ressourcengruppe und hat keine Auswirkungen auf das global bereitgestellte Traffic Manager-Profil. |

1. Wählen Sie **Erstellen** aus, um Ihr Traffic Manager-Profil zu erstellen.

    :::image type="content" source="./media/traffic-manager-geographic-routing-method/create-traffic-manager-profile.png" alt-text="Seite für die Erstellung eines Traffic Manager-Profils":::

## <a name="add-endpoints"></a>Hinzufügen von Endpunkten

1. Wählen Sie das Traffic Manager-Profil in der Liste aus.

    :::image type="content" source="./media/traffic-manager-geographic-routing-method/traffic-manager-list-geographic.png" alt-text="Traffic Manager: Liste „Geografisch“":::

1. Wählen Sie unter *Einstellungen* die Option **Endpunkte** und dann **+ Hinzufügen** aus, um einen neuen Endpunkt hinzuzufügen.

    :::image type="content" source="./media/traffic-manager-geographic-routing-method/add-geographic-endpoint.png" alt-text="Hinzufügen von Endpunkten":::

1. Wählen Sie die folgenden Einstellungen aus, oder geben Sie sie ein: 

    | Einstellung                | Wert                                              |
    | ---                    | ---                                                |
    | Typ                   | Wählen Sie den Endpunkttyp aus. Für geografische Routingprofile, die in Produktionsumgebungen verwendet werden, empfehlen wir dringend die Verwendung von geschachtelten Endpunkttypen, die ein untergeordnetes Profil mit mehr als einem Endpunkt enthalten. Weitere Informationen finden Sie unter [Häufig gestellte Fragen zu geografischen Routingmethoden für Datenverkehr](traffic-manager-FAQs.md). |    
    | Name                   | Vergeben Sie einen Namen zum Identifizieren dieses Endpunkts. |
    | Zielressourcentyp   | Wählen Sie den Ressourcentyp für das Ziel aus. |
    | Zielressource        | Wählen Sie die Ressource in der Liste aus. |

    > [!Note]
    > Bestimmte Felder auf dieser Seite sind vom Typ des hinzugefügten Endpunkts abhängig:
    > 1. Wenn Sie einen Azure-Endpunkt hinzufügen, wählen Sie basierend auf der Ressource, zu der Sie Datenverkehr leiten möchten, den **Zielressourcentyp** und das **Ziel** aus
    > 1. Wenn Sie einen **externen** Endpunkt hinzufügen, geben Sie den **vollqualifizierten Domänennamen (FQDN)** für Ihren Endpunkt an.
    > 1. Wenn Sie einen **geschachtelten Endpunkt** hinzufügen, wählen Sie die **Zielressource** aus, die dem untergeordneten Profil entspricht, das Sie verwenden möchten, und legen Sie die **Mindestanzahl der untergeordneten Endpunkte** fest.

1. Verwenden Sie im Abschnitt *Geografische Zuordnung* das Dropdownfeld, um die Regionen hinzuzufügen, aus denen Datenverkehr an den betreffenden Endpunkt gesendet werden soll. Es muss mindestens eine Region hinzugefügt werden. Die Zuordnung von mehreren Regionen ist möglich.

1. Wiederholen Sie den letzten Schritt für alle Endpunkte, die Sie unter diesem Profil hinzufügen möchten, und wählen Sie dann **Speichern** aus.

    :::image type="content" source="./media/traffic-manager-geographic-routing-method/traffic-manager-add-endpoint.png" alt-text="Hinzufügen eines Traffic Manager-Endpunkts":::

## <a name="use-the-traffic-manager-profile"></a>Verwenden des Traffic Manager-Profils

1.  Suchen Sie in der Suchleiste des Portals nach dem Namen des **Traffic Manager-Profils**, das Sie im vorherigen Abschnitt erstellt haben, und wählen Sie in den angezeigten Ergebnissen das Traffic Manager-Profil aus.
    
    :::image type="content" source="./media/traffic-manager-geographic-routing-method/search-traffic-manager-profile.png" alt-text="Suchen nach einem Traffic Manager-Profil":::

1. Unter **Traffic Manager-Profil** wird der DNS-Name Ihres neu erstellten Traffic Manager-Profils angezeigt. Der Name kann von beliebigen Clients (z. B. per Zugriff mit einem Webbrowser) für das Routing zum richtigen Endpunkt anhand des Routingtyps verwendet werden. Beim geografischen Routing untersucht Traffic Manager die IP-Quelladresse der eingehenden Anforderung und ermittelt die Region, aus der sie stammt. Wenn diese Region einem Endpunkt zugeordnet ist, wird der Datenverkehr dorthin geroutet. Wenn diese Region keinem Endpunkt zugeordnet ist, gibt Traffic Manager die Abfrageantwort NODATA zurück.

    :::image type="content" source="./media/traffic-manager-geographic-routing-method/traffic-manager-geographic-overview.png" alt-text="Übersicht über geografisches Routing mit Traffic Manager":::

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie das Traffic Manager-Profil nicht mehr benötigen, können Sie nach dem Profil suchen und **Profil löschen** auswählen.

:::image type="content" source="./media/traffic-manager-geographic-routing-method/delete-traffic-manager-profile.png" alt-text="Löschen des Traffic Manager-Profils":::

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur geografischen Routingmethode finden Sie unter:

> [!div class="nextstepaction"]
> [Geografische Routingmethode für Datenverkehr](traffic-manager-routing-methods.md#geographic)
