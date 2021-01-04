---
title: include file
description: include file
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/22/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 48f33514510618abadf329a11a9ab71a020be0bd
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/14/2020
ms.locfileid: "92479595"
---
1. Öffnen Sie die Seite Ihres Gateways für virtuelle Netzwerke. Sie können zum Gateway navigieren, indem Sie zu **Name Ihres VNET -> Übersicht -> Verbundene Geräte -> Name Ihres Gateways** wechseln, obwohl es auch mehrere andere Möglichkeiten zur Navigation gibt.
1. Wählen Sie auf der Seite für Ihr Gateway die Option **Verbindungen** aus. Wählen Sie oben auf der Seite „Verbindungen“ die Option **+Hinzufügen** aus, um die Seite **Verbindung hinzufügen** zu öffnen.

   :::image type="content" source="./media/vpn-gateway-add-site-to-site-connection-portal-include/connection.png" alt-text="Site-to-Site-Verbindung":::
1. Konfigurieren Sie auf der Seite **Verbindung hinzufügen** die Werte für Ihre Verbindung.

   * **Name:** Benennen Sie Ihre Verbindung.
   * **Verbindungstyp:** Wählen Sie **Site-to-Site (IPsec)** aus.
   * **Gateway für virtuelle Netzwerke:** Der Wert ist festgelegt, da Sie von diesem Gateway aus die Verbindung herstellen.
   * **Lokales Netzwerkgateway:** Wählen Sie **Lokales Netzwerkgateway auswählen** und dann das lokale Netzwerkgateway aus, das Sie verwenden möchten.
   * **Gemeinsam verwendeter Schlüssel:** Dieser Wert muss mit dem Wert übereinstimmen, den Sie für Ihr lokales VPN-Gerät verwenden. Im Beispiel wird „abc123“ verwendet. Sie können (und sollten) allerdings einen komplexeren Wert verwenden. Entscheidend ist Folgendes: Der Wert, den Sie hier angeben, muss dem Wert entsprechen, den Sie beim Konfigurieren Ihres VPN-Geräts angeben.
   * Lassen Sie **Private Azure-IP-Adresse verwenden** deaktiviert.
   * Lassen Sie **BGP aktivieren** deaktiviert.
   * Wählen Sie **IKEv2** aus.
   * Die verbleibenden Werte für **Abonnement**, **Ressourcengruppe** und **Speicherort** wurden korrigiert.

1. Wählen Sie **OK**, um die Verbindung zu erstellen. Auf dem Bildschirm blinkt der Hinweis *Verbindung wird erstellt* .
1. Die Verbindung wird auf der Seite **Verbindungen** des Gateways für virtuelle Netzwerke angezeigt. Der Status wechselt von *Unbekannt* zu *Verbindung wird hergestellt* und dann zu *Erfolgreich*.
