---
title: Erstellen einer Routingtabelle für einen virtuellen Hub für Azure Virtual WAN im Azure-Portal | Microsoft-Dokumentation
description: Informationen zu einer Routingtabelle für einen virtuellen Hub für Virtual WAN zum Steuern des Datenverkehrs zu einem virtuellen Netzwerkgerät im Portal.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to create a route table using the portal.
ms.openlocfilehash: 2c8b3b4671fd14f9b10b8491861ae2c652f0188b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60461653"
---
# <a name="create-a-virtual-wan-hub-route-table-for-nvas-azure-portal"></a>Erstellen einer Routingtabelle für den Virtual WAN-Hub für virtuelle Netzwerkgeräte: Azure-Portal

In diesem Artikel wird veranschaulicht, wie Sie Datenverkehr von einem Hub zu einem virtuellen Netzwerkgerät steuern.

![Virtual WAN-Diagramm](./media/virtual-wan-route-table/vwanroute.png)

## <a name="before-you-begin"></a>Voraussetzungen

Vergewissern Sie sich, dass die folgenden Kriterien erfüllt sind:

*  Sie verfügen über ein virtuelles Netzwerkgerät. Ein virtuelles Netzwerkgerät ist Drittanbietersoftware Ihrer Wahl, die in der Regel über Azure Marketplace in einem virtuellen Netzwerk bereitgestellt wird.

    * Sie haben der Netzwerkschnittstelle des virtuellen Netzwerkgeräts eine private IP-Adresse zugewiesen.

    * Das virtuelle Netzwerkgerät wird nicht im virtuellen Hub bereitgestellt. Es muss in einem gesonderten VNET bereitgestellt werden.

    *  Mit dem VNET des virtuellen Netzwerkgeräts kann mindestens ein virtuelles Netzwerk verbunden sein. In diesem Artikel wird dieses VNET als „Indirektes Spoke-VNET“ bezeichnet. Diese VNETs können mithilfe von VNET-Peering mit dem VNET des virtuellen Netzwerkgeräts verbunden werden.
*  Sie haben 2 VNETs erstellt. Diese werden als Spoke-VNETs verwendet.

    * In diesem Artikel sind die VNET-Spoke-Adressräume wie folgt: VNet1: 10.0.2.0/24 und VNET2: 10.0.3.0/24. Informationen zum Erstellen eines VNET finden Sie unter [Erstellen eines virtuellen Netzwerks](../virtual-network/quick-create-portal.md).

    * Stellen Sie sicher, dass in den VNETs keine Gateways für virtuelle Netzwerke vorhanden sind.
    * Bei dieser Konfiguration benötigen diese VNETs kein Gatewaysubnetz.

## <a name="signin"></a>1. Anmelden

Navigieren Sie in einem Browser zum [Azure-Portal](https://portal.azure.com) , und melden Sie sich mit Ihrem Azure-Konto an.

## <a name="vwan"></a>2. Erstellen eines virtuellen WAN

Erstellen Sie ein virtuelles WAN. Bei dieser Übung können Sie die folgenden Werte verwenden:

* **Name des virtuellen WAN:** myVirtualWAN
* **Ressourcengruppe:** testRG
* **Standort:** USA (Westen)

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-tutorial-vwan-include.md)]

## <a name="hub"></a>3. Erstellen eines Hubs

Erstellen Sie den Hub. Für diese Übung können Sie die folgenden Werte verwenden:

* **Standort:** USA (Westen)
* **Name:** westushub
* **Privater Adressraum des Hubs:** 10.0.1.0/24

[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-hub-include.md)]

## <a name="route"></a>4. Erstellen und Anwenden einer Routingtabelle für einen Hub

Aktualisieren Sie den Hub mit einer Routingtabelle für den Hub. Für diese Übung können Sie die folgenden Werte verwenden:

* **Adressräume des indirekten Spoke-VNET:** (VNET1 und VNET2) 10.0.2.0/24 und 10.0.3.0/24
* **Private IP-Adressen der Netzwerkschnittstelle der DMZ des virtuellen Netzwerkgeräts:** 10.0.4.5

1. Navigieren Sie zu Ihrem virtuellen WAN.
2. Klicken Sie auf den Hub, für den Sie eine Routingtabelle erstellen möchten.
3. Klicken Sie auf **...** und dann auf **Virtuellen Hub bearbeiten**.
4. Scrollen Sie auf der Seite **Virtuellen Hub bearbeiten** nach unten, und aktivieren Sie das Kontrollkästchen **Tabelle für Routing verwenden**.
5. Fügen Sie der Spalte **Wenn das Zielpräfix folgendermaßen lautet** die Adressräume hinzu. Fügen Sie der Spalte **An Adresse für nächsten Hop senden** die private IP-Adresse der Netzwerkschnittstelle der DMZ des virtuellen Netzwerkgeräts hinzu.
6. Klicken Sie auf **Bestätigen**, um die Hubressource mit den Einstellungen der Routingtabelle zu aktualisieren.

## <a name="connections"></a>5. Herstellen der VNET-Verbindungen

Stellen Sie eine Verbindung zwischen jedem indirekten Spoke-VNET (VNET1 und VNET2) und dem Hub her. Richten Sie dann eine Verbindung zwischen dem VNET des virtuellen Netzwerkgeräts und dem Hub ein.

 Für diesen Schritt können Sie die folgenden Werte verwenden:

| VNet-Name| Verbindungsname|
| --- | --- |
| VNet1 | testconnection1 |
| VNet2 | testconnection2 |
| NVAVNet | testconnection3 |

Wiederholen Sie das folgende Verfahren für jedes VNET, mit dem Sie eine Verbindung herstellen möchten.

1. Klicken Sie auf der Seite für Ihr virtuelles WAN auf **Virtuelle Netzwerkverbindungen**.
2. Klicken Sie auf der Seite für die VNET-Verbindung auf **+Add connection** (+Verbindung hinzufügen).
3. Füllen Sie auf der Seite **Add connection** (Verbindung hinzufügen) die folgenden Felder aus:

    * **Verbindungsname**: Dies ist der Name Ihrer Verbindung.
    * **Hubs**: Wählen Sie den Hub aus, den Sie dieser Verbindung zuordnen möchten.
    * **Abonnement**: Überprüfen Sie das Abonnement.
    * **Virtuelles Netzwerk**: Wählen Sie das virtuelle Netzwerk aus, das Sie mit diesem Hub verbinden möchten. Für das virtuelle Netzwerk kann nicht bereits ein Gateway für virtuelle Netzwerke vorhanden sein.
4. Klicken Sie auf **OK**, um die Verbindung zu erstellen.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Virtual WAN finden Sie auf der Seite mit der [Übersicht über Virtual WAN](virtual-wan-about.md).