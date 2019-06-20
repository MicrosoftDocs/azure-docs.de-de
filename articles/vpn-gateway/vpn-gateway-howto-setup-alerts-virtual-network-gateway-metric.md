---
title: Einrichten von Warnungen zu Azure VPN Gateway-Metriken
description: Schritte zum Konfigurieren von Warnungen zu VPN Gateway-Metriken
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptional
ms.date: 04/22/2019
ms.author: alzam
ms.openlocfilehash: e54dadbda0582095e8152ea30376d369177bfd86
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65509905"
---
# <a name="set-up-alerts-on-vpn-gateway-metrics"></a>Einrichten von Warnungen zu VPN Gateway-Metriken

Dieser Artikel hilft Ihnen beim Einrichten von Warnungen zu den Azure VPN Gateway-Metriken. Azure Monitor bietet die Möglichkeit, Warnungen für Azure-Ressourcen einzurichten. Sie können Warnungen für virtuelle Netzwerkgateways vom Typ „VPN“ einrichten.


|**Metrik**   | **Einheit** | **Granularität** | **Beschreibung** | 
|---       | ---        | ---       | ---            | ---       |
|**AverageBandwidth**| Bytes/s  | 5 Minuten| Durchschnittliche kombinierte Bandbreitennutzung aller Site-to-Site-Verbindungen für das Gateway.     |
|**P2SBandwidth**| Bytes/s  | 1 Minute  | Durchschnittliche kombinierte Bandbreitennutzung aller Point-to-Site-Verbindungen für das Gateway.    |
|**P2SConnectionCount**| Count  | 1 Minute  | Die Anzahl der Point-to-Site-Verbindungen für das Gateway.   |
|**TunnelAverageBandwidth** | Bytes/s    | 5 Minuten  | Durchschnittliche Bandbreitennutzung der für das Gateway erstellten Tunnel. |
|**TunnelEgressBytes** | Byte | 5 Minuten | Ausgehender Datenverkehr in Tunneln, die für das Gateway erstellt wurden.   |
|**TunnelEgressPackets** | Count | 5 Minuten | Anzahl der ausgehenden Pakete in Tunneln, die für das Gateway erstellt wurden.   |
|**TunnelEgressPacketDropTSMismatch** | Count | 5 Minuten | Anzahl der ausgehenden Pakete, die in Tunneln aufgrund eines Konflikts beim Datenverkehrsselektor verworfen werden. |
|**TunnelIngressBytes** | Byte | 5 Minuten | Eingehender Datenverkehr in Tunneln, die für das Gateway erstellt wurden.   |
|**TunnelIngressPackets** | Count | 5 Minuten | Anzahl der eingehenden Pakete in Tunneln, die für das Gateway erstellt wurden.   |
|**TunnelIngressPacketDropTSMismatch** | Count | 5 Minuten | Anzahl der eingehenden Pakete, die in Tunneln aufgrund eines Konflikts beim Datenverkehrsselektor verworfen werden. |


## <a name="setup"></a>Einrichten von Azure Monitor-Warnungen auf Basis von Metriken mithilfe des Azure-Portals

Die folgenden Beispielschritte erstellen für Folgendes eine Warnung für ein Gateway:

- **Metrik:** TunnelAverageBandwidth
- **Bedingung:** Bandbreite > 10 Bytes/Sekunde
- **Fenster:** 5 Minuten
- **Warnungsaktion:** E-Mail



1. Wechseln Sie zur virtuellen Netzwerkgatewayressource, und wählen Sie **Warnungen** auf der Registerkarte **Überwachung** aus. Erstellen Sie dann eine neue Warnungsregel, oder bearbeiten Sie eine bestehende Warnungsregel.

   ![Auswahl zum Erstellen einer Warnungsregel](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert1.png "Erstellen")

2. Wählen Sie Ihr VPN-Gateway als Ressource aus.

   ![Die Schaltfläche „Auswählen“ und das VPN-Gateway in der Liste der Ressourcen](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert2.png "Auswählen")

3. Wählen Sie eine Metrik aus, die für die Warnung konfiguriert werden soll.

   ![Ausgewählte Metrik in der Liste der Metriken](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert3.png "Auswählen")
4. Konfigurieren Sie die Signallogik. Es gibt drei Komponenten:

    a. **Dimensions** (Dimensionen): Wenn die Metrik über Dimensionen verfügt, können Sie bestimmte Dimensionswerte auswählen, sodass die Warnung nur Daten dieser Dimension auswertet. Diese sind optional.

    b. **Bedingung:** Dies ist der Vorgang zum Auswerten des Metrikwerts.

    c. **Zeit**: Geben Sie die Granularität der metrischen Daten und den Zeitraum für die Auswertung der Warnung an.

   ![Details zur Konfiguration der Signallogik](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert4.png "Auswählen")

5. Wählen Sie **Warnungsregeln verwalten** aus, um die konfigurierten Regeln anzuzeigen.

   ![Schaltfläche zum Verwalten von Warnungsregeln](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert8.png "Auswählen")

## <a name="next-steps"></a>Nächste Schritte

Informationen zum Konfigurieren von Warnungen für Tunneldiagnoseprotokolle finden Sie unter [Einrichten von Warnungen für Diagnoseprotokolle von VPN Gateway](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md).
