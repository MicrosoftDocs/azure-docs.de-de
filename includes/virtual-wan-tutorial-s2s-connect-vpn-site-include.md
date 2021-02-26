---
title: include file
description: include file
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 02/04/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 614a13a140453e3c7ed55a7fc0f9173626ad2f2f
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/06/2021
ms.locfileid: "99627701"
---
1. Wählen Sie **VPN-Standorte verbinden** aus, um die Seite **Standorte verbinden** zu öffnen.

    ![Screenshot: Bereich „Standorte verbinden“ für einen virtuellen Hub, bereit für die Eingabe eines vorinstallierten Schlüssels und zugehöriger Einstellungen](./media/virtual-wan-tutorial-connect-vpn-site-include/connect.png "Verbinden")

   Füllen Sie die folgenden Felder aus:

   * Geben Sie einen vorinstallierten Schlüssel ein. Wenn Sie keinen Schlüssel eingeben, wird von Azure automatisch ein Schlüssel generiert.
   * Wählen Sie das Protokoll und die IPsec-Einstellungen aus. Weitere Informationen finden Sie unter [Virtual WAN-Standardrichtlinien für IPSec-Konnektivität](../articles/virtual-wan/virtual-wan-ipsec.md).
   * Wählen Sie die entsprechende Option für **Standardroute weitergeben** aus. Mit der Option **Aktivieren** kann der virtuelle Hub eine bekannte Standardroute für diese Verbindung weitergeben. Dieses Flag ermöglicht die Weitergabe der Standardroute an eine Verbindung, wenn diese dem Virtual WAN-Hub bereits bekannt ist, weil darin eine Firewall bereitgestellt wurde, oder wenn für eine andere verbundene Site die Tunnelerzwingung aktiviert ist. Der Ursprung der Standardroute liegt nicht im Virtual WAN-Hub.

2. Wählen Sie **Verbinden**.
3. Nach wenigen Minuten werden für die Site die Verbindung und der Konnektivitätsstatus angezeigt.

   ![Screenshot: Site-to-Site-Verbindung und Konnektivitätsstatus](./media/virtual-wan-tutorial-connect-vpn-site-include/status.png "status")

   **Verbindungsstatus:** Dies ist der Status der Azure-Ressource für die Verbindung, mit der der VPN-Standort mit dem VPN-Gateway des Azure-Hubs verbunden wird. Nachdem dieser Vorgang auf der Steuerungsebene erfolgreich abgeschlossen wurde, wird mit dem Herstellen der Konnektivität für das Azure-VPN-Gateway und das lokale VPN-Gerät fortgefahren.

   **Konnektivitätsstatus:** Dies ist der tatsächliche Status der Konnektivität (Datenpfad) zwischen dem VPN-Gateway von Azure im Hub und dem VPN-Standort. Der Status kann wie folgt lauten:

    * **Unbekannt:** Dieser Status tritt normalerweise auf, wenn die Back-End-Systeme aktiv sind und der Übergang in einen anderen Status erfolgt.
    * **Verbindung wird hergestellt**: Das Azure-VPN-Gateway versucht, den tatsächlichen lokalen VPN-Standort zu erreichen.
    * **Verbunden**: Die Konnektivität zwischen dem Azure-VPN-Gateway und dem lokalen VPN-Standort wird hergestellt.
    * **Getrennt**: Dieser Status tritt auf, wenn die Verbindung aus irgendeinem Grund (lokal oder in Azure) getrennt wurde.
4. An einem Hub-VPN-Standort können Sie zusätzlich Folgendes durchführen: 

   * Bearbeiten oder Löschen der VPN-Verbindung
   * Löschen des Standorts im Azure-Portal
   * Laden Sie eine branchspezifische Konfiguration herunter, um Details zur Azure-Seite zu erhalten, indem Sie das Kontextmenü (…) neben dem Standort öffnen. Wählen Sie im obersten Menü die Option **VPN-Konfiguration herunterladen** aus, wenn Sie die Konfiguration für alle verbundenen Standorte Ihres Hubs herunterladen möchten.
