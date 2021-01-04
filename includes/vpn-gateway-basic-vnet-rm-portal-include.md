---
title: include file
description: include file
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/20/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: a66596ecb926c1bf3c6b61cc99e1eb1b56e99158
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/14/2020
ms.locfileid: "92328431"
---
1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Geben Sie in **Nach Ressourcen, Diensten und Dokumenten suchen (G+/)** den Begriff *virtuelles Netzwerk* ein.

   ![Navigieren zur Seite mit den Ressourcen des virtuellen Netzwerks](./media/vpn-gateway-basic-vnet-rm-portal-include/marketplace.png "Navigieren zur Seite mit den Ressourcen des virtuellen Netzwerks")
1. Wählen Sie aus den **Marketplace**-Ergebnissen das Ergebnis **Virtual Network** aus.

   ![Virtuelles Netzwerk auswählen](./media/vpn-gateway-basic-vnet-rm-portal-include/marketplace-results.png "Navigieren zur Seite mit den Ressourcen des virtuellen Netzwerks")
1. Wählen Sie auf der Seite **Virtuelles Netzwerk** die Option **Erstellen** aus.

   ![Seite „Virtuelles Netzwerk“](./media/vpn-gateway-basic-vnet-rm-portal-include/vnet-click-create.png "Wählen Sie „Erstellen“ aus.")
1. Nachdem Sie **Erstellen** ausgewählt haben, wird die Seite **Virtuelles Netzwerk erstellen** geöffnet.
1. Konfigurieren Sie auf der Registerkarte **Grundlagen** die VNET-Einstellungen **Projektdetails** und **Instanzendetails**.

   ![Registerkarte „Grundlagen“:](./media/vpn-gateway-basic-vnet-rm-portal-include/basics.png "Registerkarte „Grundlagen“") Beim Ausfüllen der Felder wird ein grünes Häkchen angezeigt, nachdem die eingegebenen Zeichen überprüft wurden. Einige Werte werden automatisch eingetragen und können durch eigene Werte ersetzt werden:

   - **Abonnement**: Vergewissern Sie sich, dass das richtige Abonnement angegeben ist. Das Abonnement kann über die Dropdownliste geändert werden.
   - **Ressourcengruppe**: Wählen Sie eine vorhandene Ressourcengruppe aus, oder klicken Sie auf **Neu erstellen**, um eine zu erstellen. Weitere Informationen zu Ressourcengruppen finden Sie unter [Azure Resource Manager – Übersicht](../articles/azure-resource-manager/management/overview.md#resource-groups).
   - **Name**: Geben Sie den Namen für Ihr virtuelles Netzwerk ein.
   - **Region**: Wählen Sie den Standort für Ihr VNET aus. Der Standort gibt an, wo sich die in diesem VNET bereitgestellten Ressourcen befinden.

1. Konfigurieren Sie auf der Registerkarte **IP-Adressen** die Werte. Die in den nachstehenden Beispielen dargestellten Werte dienen zu Demonstrationszwecken. Passen Sie diese Werte entsprechend Ihren erforderlichen Einstellungen an.

   ![Registerkarte „IP-Adressen“](./media/vpn-gateway-basic-vnet-rm-portal-include/addresses.png "Registerkarte „IP-Adressen“")  
   - **IPv4-Adressraum:** Standardmäßig wird automatisch ein Adressraum erstellt. Sie können auf den Adressraum klicken, um ihn an Ihre eigenen Werte anzupassen. Darüber hinaus können Sie zusätzliche Adressräume hinzufügen.
   - **Subnetz**: Wenn Sie den Standardadressraum verwenden, wird automatisch ein Standardsubnetz erstellt. Wenn Sie den Adressraum ändern, müssen Sie ein Subnetz hinzufügen. Wählen Sie **+ Subnetz hinzufügen** aus, um das Fenster **Subnetz hinzufügen** zu öffnen. Konfigurieren Sie die folgenden Einstellungen, und wählen Sie dann **Hinzufügen** aus, um die Werte hinzuzufügen:
      - **Subnetzname**: In diesem Beispiel haben wir dem Subnetz den Namen „FrontEnd“ gegeben.
      - **Subnetzadressbereich:** Der Adressbereich für dieses Subnetz.

1. Behalten Sie auf der Registerkarte **Sicherheit** vorläufig die Standardwerte bei:

   - **DDoS Protection**: Basic
   - **Firewall**: Disabled
1. Wählen Sie **Bewerten + erstellen** aus, um die Einstellungen für das virtuelle Netzwerk zu überprüfen.
1. Nachdem die Einstellungen überprüft wurden, wählen Sie **Erstellen** aus.
