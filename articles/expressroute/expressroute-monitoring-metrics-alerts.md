---
title: 'Azure ExpressRoute: Überwachung, Metriken und Warnungen'
description: Hier erfahren Sie mehr über die Azure ExpressRoute-Überwachung, -Metriken und -Warnungen mithilfe von Azure Monitor, der zentralen Anlaufstelle für alle Metriken, Warnungen und Diagnoseprotokolle in Azure.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 01/11/2020
ms.author: duau
ms.openlocfilehash: 7a5da35da35b2f447256bc742681ccd7a7d403da
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/30/2021
ms.locfileid: "99091565"
---
# <a name="expressroute-monitoring-metrics-and-alerts"></a>ExpressRoute-Überwachung, Metriken und Warnungen

Dieser Artikel macht Sie mit der ExpressRoute-Überwachung, Metriken und Warnungen in Azure Monitor vertraut. Azure Monitor ist die Zentrale für alle Metriken, Warnungen und Diagnoseprotokolle im gesamten Azure-System.
 
>[!NOTE]
>Die Verwendung **klassischer Metriken** wird nicht empfohlen.
>

## <a name="expressroute-metrics"></a>ExpressRoute-Metriken

Um **Metriken** anzuzeigen, navigieren Sie zur Seite *Azure Monitor*, und klicken Sie auf *Metriken*. Wenn Sie **ExpressRoute**-Metriken anzeigen möchten, filtern Sie nach dem Ressourcentyp *ExpressRoute-Leitungen*. Um **Global Reach**-Metriken anzuzeigen, filtern Sie nach dem Ressourcentyp *ExpressRoute-Leitungen*, und wählen Sie eine ExpressRoute-Leitungsressource aus, bei der Global Reach aktiviert ist. Um **ExpressRoute Direct**-Metriken anzuzeigen, filtern Sie nach dem Ressourcentyp *ExpressRoute-Ports*. 

Nach dem Auswählen einer Metrik wird die Standardaggregation angewendet. Optional können Sie die Teilung anwenden, wonach die Metrik mit anderen Dimensionen angezeigt wird.

### <a name="available-metrics"></a>Verfügbare Metriken

|**Metrik**|**Kategorie**|**Dimension(en)**|**Feature(s)**|
| --- | --- | --- | --- |
|ARP-Verfügbarkeit|Verfügbarkeit|<ui><li>Peer (Primärer/Sekundärer ExpressRoute-Router)</ui></li><ui><li> Peeringtyp (Privat/Öffentlich/Microsoft)</ui></li>|ExpressRoute|
|BGP-Verfügbarkeit|Verfügbarkeit|<ui><li> Peer (Primärer/Sekundärer ExpressRoute-Router)</ui></li><ui><li> Peeringtyp</ui></li>|ExpressRoute|
|BitsInPerSecond|Verkehr|<ui><li> Peeringtyp (ExpressRoute)</ui></li><ui><li>Link (ExpressRoute Direct)</ui></li>|<li>ExpressRoute</li><li>ExpressRoute Direct</li><ui><li>ExpressRoute-Gatewayverbindung</ui></li>|
|BitsOutPerSecond|Verkehr| <ui><li>Peeringtyp (ExpressRoute)</ui></li><ui><li> Link (ExpressRoute Direct) |<ui><li>ExpressRoute<ui><li>ExpressRoute Direct</ui></li><ui><li>ExpressRoute-Gatewayverbindung</ui></li>|
|CPU-Auslastung|Leistung| <ui><li>Instanz</ui></li>|ExpressRoute-Gateway für virtuelle Netzwerke|
|Pakete pro Sekunde|Leistung| <ui><li>Instanz</ui></li>|ExpressRoute-Gateway für virtuelle Netzwerke|
|Anzahl der für Peer angekündigten Routen |Verfügbarkeit| <ui><li>Instanz</ui></li>|ExpressRoute-Gateway für virtuelle Netzwerke|
|Anzahl der von Peer gelernten Routen |Verfügbarkeit| <ui><li>Instanz</ui></li>|ExpressRoute-Gateway für virtuelle Netzwerke|
|Häufigkeit der Routenänderung |Verfügbarkeit| <ui><li>Instanz</ui></li>|ExpressRoute-Gateway für virtuelle Netzwerke|
|Anzahl der virtuellen Computer im virtuellen Netzwerk |Verfügbarkeit| – |ExpressRoute-Gateway für virtuelle Netzwerke|
|GlobalReachBitsInPerSecond|Verkehr|<ui><li>Dienstschlüssel der Peeringleitung</ui></li>|Global Reach|
|GlobalReachBitsOutPerSecond|Verkehr|<ui><li>Dienstschlüssel der Peeringleitung</ui></li>|Global Reach|
|AdminState|Physische Konnektivität|Link|ExpressRoute Direct|
|LineProtocol|Physische Konnektivität|Link|ExpressRoute Direct|
|RxLightLevel|Physische Konnektivität|<ui><li>Link</ui></li><ui><li>Bereich</ui></li>|ExpressRoute Direct|
|TxLightLevel|Physische Konnektivität|<ui><li>Link</ui></li><ui><li>Bereich</ui></li>|ExpressRoute Direct|
>[!NOTE]
>Die Verwendung von *GlobalGlobalReachBitsInPerSecond* und *GlobalGlobalReachBitsOutPerSecond* ist nur sichtbar, wenn mindestens eine Global Reach-Verbindung hergestellt wird.
>

## <a name="circuits-metrics"></a>Leitungsmetriken

### <a name="bits-in-and-out---metrics-across-all-peerings"></a>Ein- und ausgehende Bits – Metriken über alle Peerings

Sie können Metriken über alle Peerings hinweg für eine bestimmte ExpressRoute-Leitung anzeigen.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/ermetricspeering.jpg" alt-text="Metriken zu Leitungen":::

### <a name="bits-in-and-out---metrics-per-peering"></a>Ein- und ausgehende Bits – Metriken pro Peering

Sie können Metriken für privates, öffentliches und Microsoft-Peering in Bits pro Sekunde anzeigen.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erpeeringmetrics.jpg" alt-text="Peeringspezifische Metriken":::

### <a name="bgp-availability---split-by-peer"></a>BGP-Verfügbarkeit – Aufgeteilt nach Peer  

Sie können die Verfügbarkeit von BGP nahezu in Echtzeit über Peerings und Peers (primäre und sekundäre ExpressRoute-Router) hinweg anzeigen. Dieses Dashboard zeigt die primäre BGP-Sitzung aufwärts für privates Peering und die zweite BGP-Sitzung abwärts für privates Peering an. 

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erBgpAvailabilityMetrics.jpg" alt-text="BGP-Verfügbarkeit pro Peer":::

### <a name="arp-availability---split-by-peering"></a>ARP-Verfügbarkeit – Aufgeteilt nach Peering  

Sie können die Verfügbarkeit von [ARP](./expressroute-troubleshooting-arp-resource-manager.md) nahezu in Echtzeit über Peerings und Peers (primäre und sekundäre ExpressRoute-Router) hinweg anzeigen. Dieses Dashboard zeigt die ARP-Sitzung aufwärts für privates Peering auf beiden Peers an, aber vollständig abwärts für Microsoft-Peering über Peerings hinweg. Die Standardaggregation (Durchschnitt) wurde für beide Peers verwendet.  

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erArpAvailabilityMetrics.jpg" alt-text="ARP-Verfügbarkeit pro Peer":::

## <a name="expressroute-direct-metrics"></a>ExpressRoute Direct-Metriken

### <a name="admin-state---split-by-link"></a>Administratorstatus nach Link

Sie können den Administratorstatus für jeden Link des ExpressRoute Direct-Portpaars anzeigen.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/adminstate-per-link.jpg" alt-text="ExpressRoute Direct: Administratorstatus":::

### <a name="bits-in-per-second---split-by-link"></a>Eingehende Bits pro Sekunde nach Link

Sie können die eingehenden Bits pro Sekunde über beide Links des ExpressRoute Direct-Portpaars anzeigen.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/bits-in-per-second-per-link.jpg" alt-text="ExpressRoute Direct: eingehende Bits pro Sekunde":::

### <a name="bits-out-per-second---split-by-link"></a>Ausgehende Bits pro Sekunde nach Link

Sie können auch die ausgehenden Bits pro Sekunde über beide Links des ExpressRoute Direct-Portpaars anzeigen.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/bits-out-per-second-per-link.jpg" alt-text="ExpressRoute Direct: ausgehende Bits pro Sekunde":::

### <a name="line-protocol---split-by-link"></a>Zeilenprotokoll nach Link

Sie können das Zeilenprotokoll für jeden Link des ExpressRoute Direct-Portpaars anzeigen.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/line-protocol-per-link.jpg" alt-text="ExpressRoute Direct: Zeilenprotokoll":::

### <a name="rx-light-level---split-by-link"></a>Rx-Lichtpegel nach Link

Sie können den Rx-Lichtpegel (vom ExpressRoute Direct-Port **empfangene** Lichtintensität) für jeden Port anzeigen. Fehlerfreie Rx-Lichtpegel liegen in der Regel innerhalb eines Bereichs von -10 bis 0 dBm.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/rxlight-level-per-link.jpg" alt-text="ExpressRoute Direct: Empfangslichtpegel":::

### <a name="tx-light-level---split-by-link"></a>Tx-Lichtpegel nach Link

Sie können den Tx-Lichtpegel (vom ExpressRoute Direct-Port **übertragene** Lichtintensität) für jeden Port anzeigen. Fehlerfreie Tx-Lichtpegel liegen in der Regel innerhalb eines Bereichs von -10 bis 0 DBM.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/txlight-level-per-link.jpg" alt-text="ExpressRoute Direct: Sendelichtpegel":::

## <a name="expressroute-virtual-network-gateway-metrics"></a>Metriken zum ExpressRoute-Gateway für virtuelle Netzwerke

### <a name="cpu-utilization---split-instance"></a>CPU-Auslastung – geteilte Instanz

Sie können die CPU-Auslastung der Gatewayinstanzen anzeigen.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/cpu-split.jpg" alt-text="CPU-Aufteilung":::

### <a name="packets-per-second---split-by-instance"></a>Pakete pro Sekunde – Unterteilung nach Instanz

Sie können das Gateway durchlaufende Pakete pro Sekunde anzeigen.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/pps-split.jpg" alt-text="Pakete pro Sekunde – Aufteilung":::

### <a name="count-of-routes-advertised-to-peer---split-by-instance"></a>Anzahl der für Peer angekündigten Routen – Aufgeteilt nach Instanz

Sie können die Anzahl der Routen anzeigen, die für die ExpressRoute-Verbindung angekündigt wurden.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/count-of-routes-advertised-to-peer.png" alt-text="Anzahl der für Peer angekündigten Routen":::

### <a name="count-of-routes-learned-from-peer---split-by-instance"></a>Anzahl der von Peer gelernten Routen – Aufgeteilt nach Instanz

Sie können die Anzahl der Routen anzeigen, die von der ExpressRoute-Verbindung empfangen wurden.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/count-of-routes-learned-from-peer.png" alt-text="Anzahl der von Peer gelernten Routen":::

### <a name="frequency-of-routes-change---split-by-instance"></a>Häufigkeit der Routenänderung – Aufgeteilt nach Instanz

Sie können die Häufigkeit anzeigen, mit der die Route im Gateway geändert wird.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/frequency-of-routes-changed.png" alt-text="Häufigkeit von geänderten Routen":::

### <a name="number-of-vms-in-the-virtual-network"></a>Anzahl der virtuellen Computer im virtuellen Netzwerk

Sie können die Anzahl der virtuellen Computer im virtuellen Netzwerk anzeigen.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/number-of-virtual-machines-virtual-network.png" alt-text="Anzahl der virtuellen Computer im virtuellen Netzwerk":::

## <a name="expressroute-gateway-connections-in-bitsseconds"></a>ExpressRoute-Gatewayverbindungen in Bits pro Sekunde

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erconnections.jpg" alt-text="Gatewayverbindungen":::

## <a name="alerts-for-expressroute-gateway-connections"></a>Warnungen zu ExpressRoute-Gatewayverbindungen

1. Um Warnungen zu konfigurieren, navigieren Sie zu **Azure Monitor**, und wählen Sie dann **Warnungen** aus.

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/eralertshowto.jpg" alt-text="alerts":::
2. Klicken Sie auf **+Ziel auswählen**, und wählen Sie die ExpressRoute-Gatewayverbindungsressource aus.

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/alerthowto2.jpg" alt-text="Ziel":::
3. Definieren Sie die Warnungsdetails.

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/alerthowto3.jpg" alt-text="Aktionsgruppe":::
4. Definieren Sie die Aktionsgruppe, und fügen Sie sie hinzu.

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/actiongroup.png" alt-text="Hinzufügen einer Aktionsgruppe":::

## <a name="alerts-based-on-each-peering"></a>Warnungen basierend auf dem jeweiligen Peering

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/basedpeering.jpg" alt-text="Jedes Peering":::

## <a name="configure-alerts-for-activity-logs-on-circuits"></a>Konfigurieren von Warnungen für Aktivitätsprotokolle zu Leitungen

In **Warnungskriterien** können Sie **Aktivitätsprotokoll** als Signaltyp und dann das Signal auswählen.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/alertshowto6activitylog.jpg" alt-text="Aktivitätsprotokolle":::

## <a name="additional-metrics-in-log-analytics"></a>Zusätzliche Metriken in Log Analytics

Sie können die ExpressRoute-Metriken auch anzeigen, indem Sie zu Ihrer ExpressRoute-Leitung navigieren und die Registerkarte *Protokolle* auswählen. Für alle Metriken, die Sie abfragen, enthält die Ausgabe die nachfolgenden Spalten.

|**Spalte**|**Typ**|**Beschreibung**|
| --- | --- | --- |
|TimeGrain|Zeichenfolge|PT1M (Metrikwerte werden jede Minute gepusht)|
|Anzahl|real|Normalerweise gleich 2 (jedes MSEE pusht jede Minute einen einzelnen Metrikwert).|
|Minimum|real|Der Mindestwert der beiden Metrikwerte, die von den beiden MSEEs gepusht werden|
|Maximum|real|Der Höchstwert der beiden Metrikwerte, die von den beiden MSEEs gepusht werden|
|Average|real|Entspricht (Minimum + Maximum)/2|
|Gesamt|real|Summe der beiden Metrikwerte aus beiden MSEEs (der Hauptwert, auf den sich bei der abgefragten Metrik konzentriert werden sollte)|
  
## <a name="next-steps"></a>Nächste Schritte

Konfigurieren Sie Ihre ExpressRoute-Verbindung.
  
* [Erstellen und Ändern einer Verbindung](expressroute-howto-circuit-arm.md)
* [Erstellen und Ändern einer Peeringkonfiguration](expressroute-howto-routing-arm.md)
* [Verknüpfen eines VNet mit einer ExpressRoute-Verbindung](expressroute-howto-linkvnet-arm.md)
