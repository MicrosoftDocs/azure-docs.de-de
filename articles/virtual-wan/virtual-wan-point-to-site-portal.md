---
title: 'Tutorial: Verwenden von Azure Virtual WAN zum Erstellen einer Point-to-Site-Verbindung mit Azure'
description: In diesem Tutorial wird beschrieben, wie Sie Azure Virtual WAN verwenden, um eine Point-to-Site-VPN-Verbindung mit Azure zu erstellen.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 11/09/2020
ms.author: cherylmc
ms.openlocfilehash: e7e65d5d2941765df98b3bf3b7fb8ff2e89b7e9f
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94411200"
---
# <a name="tutorial-create-a-user-vpn-connection-using-azure-virtual-wan"></a>Tutorial: Erstellen einer Benutzer-VPN-Verbindung per Azure Virtual WAN

In diesem Tutorial wird beschrieben, wie Sie Virtual WAN zum Verbinden Ihrer Ressourcen in Azure über eine IPsec/IKE (IKEv2)- oder OpenVPN-VPN-Verbindung nutzen. Für diese Art von Verbindung muss auf dem Clientcomputer der VPN-Client konfiguriert sein. Weitere Informationen zu Virtual WAN finden Sie auf der Seite mit der [Übersicht über Virtual WAN](virtual-wan-about.md).

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen eines virtuellen WAN
> * Erstellen einer P2S-Konfiguration
> * Erstellen eines virtuellen Hubs
> * Angeben von DNS-Servern
> * Generieren eines Profilkonfigurationspakets für den VPN-Client
> * VPN-Clients konfigurieren
> * Anzeigen Ihrer Virtual WAN-Instanz

![Virtual WAN-Diagramm](./media/virtual-wan-about/virtualwanp2s.png)

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [Before beginning](../../includes/virtual-wan-before-include.md)]

## <a name="create-a-virtual-wan"></a><a name="wan"></a>Erstellen eines Virtual WAN

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-create-vwan-include.md)]

## <a name="create-a-p2s-configuration"></a><a name="p2sconfig"></a>Erstellen einer P2S-Konfiguration

Eine P2S-Konfiguration (Point-to-Site) definiert die Parameter für das Herstellen der Verbindung mit Remoteclients.

[!INCLUDE [Create P2S configuration](../../includes/virtual-wan-p2s-configuration-include.md)]

## <a name="create-virtual-hub-and-gateway"></a><a name="hub"></a>Erstellen eines virtuellen Hubs und Gateways

[!INCLUDE [Create hub](../../includes/virtual-wan-p2s-hub-include.md)]

## <a name="specify-dns-server"></a><a name="dns"></a>Angeben des DNS-Servers

Sie können diese Einstellung beim Erstellen des Hubs konfigurieren oder zu einem späteren Zeitpunkt ändern. Suchen Sie für die Änderung nach dem virtuellen Hub. Wählen Sie unter **Benutzer-VPN (Point-to-Site)** die Option **Konfigurieren** aus, und geben Sie die IP-Adresse(n) des DNS-Servers in die entsprechenden Textfelder für die **benutzerdefinierten DNS-Server** ein. Sie können bis zu fünf DNS-Server angeben.

   :::image type="content" source="media/virtual-wan-point-to-site-portal/custom-dns.png" alt-text="Benutzerdefinierter DNS-Server" lightbox="media/virtual-wan-point-to-site-portal/custom-dns-expand.png":::

## <a name="generate-vpn-client-profile-package"></a><a name="download"></a>Generieren eines VPN-Clientprofilpakets

Generieren Sie das VPN-Clientprofilpaket zur Konfiguration Ihrer VPN-Clients, und laden Sie es herunter.

[!INCLUDE [Download profile](../../includes/virtual-wan-p2s-download-profile-include.md)]

## <a name="configure-vpn-clients"></a><a name="configure-client"></a>VPN-Clients konfigurieren

Verwenden Sie das heruntergeladene Profilpaket, um die VPN-Clients für den Remotezugriff zu konfigurieren. Die Vorgehensweise variiert für die jeweiligen Betriebssysteme. Befolgen Sie die Anweisungen, die für Ihr System gelten.
Sobald Sie die Konfiguration Ihres Clients abgeschlossen haben, können Sie eine Verbindung herstellen.

[!INCLUDE [Configure clients](../../includes/virtual-wan-p2s-configure-clients-include.md)]

## <a name="view-your-virtual-wan"></a><a name="viewwan"></a>Anzeigen Ihrer Virtual WAN-Instanz

1. Navigieren Sie zum virtuellen WAN.
1. Auf der Seite **Übersicht** steht jeder Punkt auf der Karte für einen Hub.
1. Im Abschnitt **Hubs und Verbindungen** können Sie den Hubstatus, die Site, die Region, den VPN-Verbindungsstatus und die Anzahl von ein- und ausgehenden Bytes anzeigen.

## <a name="clean-up-resources"></a><a name="cleanup"></a>Bereinigen von Ressourcen

Wenn Sie diese Ressourcen nicht mehr benötigen, können Sie den Befehl [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) verwenden, um die Ressourcengruppe und alle darin enthaltenen Ressourcen zu entfernen. Ersetzen Sie „myResourceGroup“ durch den Namen Ihrer Ressourcengruppe, und führen Sie den folgenden PowerShell-Befehl aus:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Virtual WAN finden Sie im folgenden Artikel:

> [!div class="nextstepaction"]
> * [Virtual WAN – Häufig gestellte Fragen](virtual-wan-faq.md)
