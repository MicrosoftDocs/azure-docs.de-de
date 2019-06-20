---
title: Azure Traffic Analytics-Schema | Microsoft-Dokumentation
description: Hier erfahren Sie Grundlagen zum Traffic Analytics-Schema, mit dem Flowprotokolle für Azure-Netzwerksicherheitsgruppen analysiert werden.
services: network-watcher
documentationcenter: na
author: vinynigam
manager: agummadi
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/26/2019
ms.author: vinigam
ms.openlocfilehash: 491f19abfd87c28ede45e98a24f31fe7e599b18b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64691415"
---
# <a name="schema-and-data-aggregation-in-traffic-analytics"></a>Schema und Datenaggregation in Traffic Analytics

Traffic Analytics ist eine cloudbasierte Lösung, die Einblick in Benutzer- und Anwendungsaktivitäten in Cloudnetzwerken bietet. Traffic Analytics analysiert die Flussprotokolle von Network Watcher für Netzwerksicherheitsgruppen (NSG), um Einblicke in den Datenfluss in Ihrer Azure-Cloud bereitstellen. Mit der Datenverkehrsanalyse können Sie folgende Aktionen durchführen:

- Visualisieren der Netzwerkaktivität für Ihre Azure-Abonnements und Identifizieren von Hotspots
- Erkennen von Sicherheitsrisiken für das Netzwerk und Schützen Ihres Netzwerks mithilfe von Informationen zu offenen Ports, Anwendungen, die versuchen, Zugriff auf das Internet zu erhalten, und virtuellen Computern (VM), die Verbindungen mit betrügerischen Netzwerken herstellen
- Verstehen von Mustern im Datenverkehr über Azure-Regionen und das Internet zur Optimierung Ihrer Netzwerkbereitstellung im Hinblick auf Leistung und Kapazität
- Ermitteln von Fehlkonfigurationen im Netzwerk, die zu fehlerhaften Verbindungen in Ihrem Netzwerk führen
- Kennen der Netzwerkauslastung in Byte, Paketen oder Flows

### <a name="data-aggregation"></a>Daten-Aggregation

1. Alle Flowprotokolle in einer Netzwerksicherheitsgruppe zwischen „FlowIntervalStartTime_t“ und „FlowIntervalEndTime_t“ werden im Speicherkonto in Intervallen von einer Minute als Blobs erfasst, bevor sie von Traffic Analytics verarbeitet werden. 
2. Das Standardintervall für die Verarbeitung in Traffic Analytics beträgt 60 Minuten. Das bedeutet, dass Traffic Analytics alle 60 Minuten Blobs aus dem Speicher erfasst, um diese zu aggregieren.
3. Flows, die die gleichen Werte für Quell-IP, Ziel-IP, Zielport, NSG-Name, NSG-Regel, Flowrichtung und Transportschichtprotokoll (TCP oder UDP) aufweisen (Hinweis: der Quellport wird bei der Aggregation ausgeschlossen), werden von Traffic Analytics in einen einzigen Flow zusammengefasst.
4. Dieser einzelne Datensatz wird ergänzt (Details dazu im folgenden Abschnitt) und von Traffic Analytics in Log Analytics erfasst. Dieser Vorgang kann bis zu 1 Stunde (max.) dauern.
5. Das Feld „FlowStartTime_t“ gibt das erste Vorkommen eines solchen aggregierten Flows (gleiches 4-Tupel) im Verarbeitungsintervall des Flowprotokolls zwischen „FlowIntervalStartTime_t“ und „FlowIntervalEndTime_t“ an. 
6. Bei allen Ressourcen in Traffic Analytics handelt es sich bei den auf der Benutzeroberfläche angezeigten Flows um alle Flows, die von Mitgliedern der Netzwerksicherheitsgruppe gesehen werden. In Log Analytics sehen Benutzer jedoch nur den einzelnen, zusammengefassten Datensatz. Um alle Flows anzuzeigen, verwenden Sie das Feld „blob_id“, auf das aus dem Speicher verwiesen werden kann. Die Gesamtanzahl von Flows für diesen Datensatz entspricht den einzelnen Flows, die im Blob zu sehen sind.


### <a name="fields-used-in-traffic-analytics-schema"></a>Im Traffic Analytics-Schema verwendete Felder

Traffic Analytics setzt auf Log Analytics auf, sodass Sie benutzerdefinierte Abfragen für Daten ausführen können, die von Traffic Analytics ergänzt wurden, und dann für diese Daten Warnungen festlegen können.

Im Folgenden werden die Felder im Schema und ihre Bedeutung aufgeführt.

| Feld | Format | Kommentare | 
|:---   |:---    |:---  |
| TableName | AzureNetworkAnalytics_CL | Tabelle für Traffic Analytics-Daten.
| SubType_s | FlowLog | Untertyp für die Flowprotokolle. |
| FASchemaVersion_s |   1   | Schemaversion. Spiegelt nicht die Version des NSG-Flowprotokolls wider. |
| TimeProcessed_t   | Datum und Uhrzeit in UTC  | Der Zeitpunkt, zu dem Traffic Analytics die unformatierten Flowprotokolle aus dem Speicherkonto verarbeitet hat. |
| FlowIntervalStartTime_t | Datum und Uhrzeit in UTC |  Startzeit des Verarbeitungsintervalls des Flowprotokolls. Dies ist der Zeitpunkt, ab dem das Flowintervall gemessen wird. |
| FlowIntervalEndTime_t | Datum und Uhrzeit in UTC | Endzeit des Verarbeitungsintervalls des Flowprotokolls. |
| FlowStartTime_t | Datum und Uhrzeit in UTC |  Das erste Vorkommen des Flows (der aggregiert wird) im Verarbeitungsintervall des Flowprotokolls zwischen „FlowIntervalStartTime_t“ und „FlowIntervalEndTime_t“. Dieser Flow wird basierend auf Aggregationslogik aggregiert. |
| FlowEndTime_t | Datum und Uhrzeit in UTC | Das letzte Vorkommen des Flows (der aggregiert wird) im Verarbeitungsintervall des Flowprotokolls zwischen „FlowIntervalStartTime_t“ und „FlowIntervalEndTime_t“. Beim Flowprotokoll V2 enthält dieses Feld den Zeitpunkt, zu dem der letzte Flow mit dem gleichen 4-Tupel gestartet wurde (im unformatierten Flowdatensatz als „B“ markiert). |
| FlowType_s |  * IntraVNet <br> * InterVNet <br> * S2S <br> * P2S <br> * AzurePublic <br> * ExternalPublic <br> * MaliciousFlow <br> * UnknownPrivate <br> * Unknown | Definitionen finden Sie in den Hinweisen unter der Tabelle. |
| SrcIP_s | Quell-IP-Adresse | Ist im Fall von AzurePublic- und ExternalPublic-Flows leer. |
| DestIP_s | IP-Zieladresse | Ist im Fall von AzurePublic- und ExternalPublic-Flows leer. |
| VMIP_s | IP der VM | Wird für AzurePublic- und ExternalPublic-Flows verwendet. |
| PublicIP_s | Öffentliche IP-Adressen | Wird für AzurePublic- und ExternalPublic-Flows verwendet. |
| DestPort_d | Zielport | Port, an dem Datenverkehr eingeht. | 
| L4Protocol_s  | * T <br> * U  | Transportprotokoll. T = TCP <br> U = UDP | 
| L7Protocol_s  | Name des Protokolls | Wird aus dem Zielport abgeleitet. |
| FlowDirection_s | * I = Inbound (eingehend)<br> * O = Outbound (ausgehend) | Richtung des Flows in die oder aus der NSG gemäß Flowprotokoll. | 
| FlowStatus_s  | * A = Allowed by NSG Rule (durch NSG-Regel zugelassen) <br> *  D = Denied by NSG Rule (durch NSG-Regel abgelehnt)  | Status des durch die NSG zugelassenen oder abgelehnten Flows gemäß Flowprotokoll. |
| NSGList_s | \<ABONNEMENT-ID>\/<NAME_DER_RESSOURCENGRUPPE>\/<NAME_DER_NSG> | Die NSG (Netzwerksicherheitsgruppe), die dem Flow zugeordnet ist. |
| NSGRules_s | \<Indexwert 0)><NAME_DER_NSG_REGEL>\<Flowrichtung>\<Flowstatus>\<FlowCount ProcessedByRule> |  Die NSG-Regel, die diesen Flow zugelassen oder abgelehnt hat. |
| NSGRuleType_s | * User Defined *  Default (benutzerdefiniert, Standardwert) |   Der vom Flow verwendete NSG-Regeltyp. |
| MACAddress_s | MAC-Adresse | Die MAC-Adresse des Netzwerkadapters, an dem der Flow erfasst wurde. |
| Subscription_s | Das Abonnement des virtuellen Azure-Netzwerks, der Azure-Netzwerkschnittstelle oder der Azure-VM wird in diesem Feld aufgefüllt. | Gilt nur für diese Flowtypen; S2S, P2S, AzurePublic, ExternalPublic, MaliciousFlow, UnknownPrivate (Flowtypen, bei denen es sich bei nur einer Seite um Azure handelt). |
| Subscription1_s | Abonnement-ID | Abonnement-ID des virtuellen Netzwerks, der Netzwerkschnittstelle oder der VM, zu dem bzw. der die Quell-IP im Flow gehört. |
| Subscription2_s | Abonnement-ID | Abonnement-ID des virtuellen Netzwerks, der Netzwerkschnittstelle oder der VM, zu dem bzw. der die Ziel-IP im Flow gehört. |
| Region_s | Azure-Region des virtuellen Netzwerks, der Netzwerkschnittstelle oder der VM, zu dem bzw. der die IP im Flow gehört. | Gilt nur für diese Flowtypen; S2S, P2S, AzurePublic, ExternalPublic, MaliciousFlow, UnknownPrivate (Flowtypen, bei denen es sich bei nur einer Seite um Azure handelt). |
| Region1_s | Azure-Region | Azure-Region des virtuellen Netzwerks, der Netzwerkschnittstelle oder der VM, zu dem bzw. der die Quell-IP im Flow gehört. |
| Region2_s | Azure-Region | Azure-Region des virtuellen Netzwerks, zu dem die Ziel-IP im Flow gehört. |
| NIC_s | \<Name_der_Ressourcengruppe>\/\<Name_der_Netzwerkschnittstelle> |  Der Netzwerkadapter, der der VM zugeordnet ist, die Datenverkehr sendet oder empfängt. |
| NIC1_s | Name_der_Ressourcengruppe>/\<Name_der_Netzwerkschnittstelle> | Der Netzwerkadapter, der der Quell-IP im Flow zugeordnet ist. |
| NIC2_s | Name_der_Ressourcengruppe>/\<Name_der_Netzwerkschnittstelle> | Der Netzwerkadapter, der der Ziel-IP im Flow zugeordnet ist. |
| VM_s | <Name_der_Ressourcengruppe>\/\<Name_der_Netzwerkschnittstelle> | Die VM, die der Netzwerkschnittstelle „NIC_s“ zugeordnet ist. |
| VM1_s | <Name_der_Ressourcengruppe>/\<Name_der_VM> | Die VM, die der Quell-IP im Flow zugeordnet ist. |
| VM2_s | <Name_der_Ressourcengruppe>/\<Name_der_VM> | Die VM, die der Ziel-IP im Flow zugeordnet ist. |
| Subnet_s | <Name_der_Ressourcengruppe>/<Name_des_virtuellen_Netzwerks>/\<Name_des_Subnetzes> | Das Subnetz, das „NIC_s“ zugeordnet ist. |
| Subnet1_s | <Name_der_Ressourcengruppe>/<Name_des_virtuellen_Netzwerks>/\<Name_des_Subnetzes> | Das Subnetz, das der Quell-IP im Flow zugeordnet ist. |
| Subnet2_s | <Name_der_Ressourcengruppe>/<Name_des_virtuellen_Netzwerks>/\<Name_des_Subnetzes>    | Das Subnetz, das der Ziel-IP im Flow zugeordnet ist. |
| ApplicationGateway1_s | \<Abonnement-ID>/\<Name_der_Ressourcengruppe>/\<Name_des_Anwendungsgateways> | Das Anwendungsgateway, das der Quell-IP im Flow zugeordnet ist. | 
| ApplicationGateway2_s | \<Abonnement-ID>/\<Name_der_Ressourcengruppe>/\<Name_des_Anwendungsgateways> | Das Anwendungsgateway, das der Ziel-IP im Flow zugeordnet ist. |
| LoadBalancer1_s | \<Abonnement-ID>/\<Name_der_Ressourcengruppe>/\<Name_des_Lastenausgleichsmoduls> | Das Lastenausgleichsmodul, das der Quell-IP im Flow zugeordnet ist. |
| LoadBalancer2_s | \<Abonnement-ID>/\<Name_der_Ressourcengruppe>/\<Name_des_Lastenausgleichsmoduls> | Das Lastenausgleichsmodul, das der Ziel-IP im Flow zugeordnet ist. |
| LocalNetworkGateway1_s | \<Abonnement-ID>/\<Name_der_Ressourcengruppe>/\<Name_des_lokalen_Netzwerkgateways> | Das lokale Netzwerkgateway, das der Quell-IP im Flow zugeordnet ist. |
| LocalNetworkGateway2_s | \<Abonnement-ID>/\<Name_der_Ressourcengruppe>/\<Name_des_lokalen_Netzwerkgateways> | Das lokale Netzwerkgateway, das der Ziel-IP im Flow zugeordnet ist. |
| ConnectionType_s | Mögliche Werte: VNetPeering, VpnGateway und ExpressRoute. |    Verbindungstyp. |
| ConnectionName_s | \<Abonnement-ID>/\<Name_der_Ressourcengruppe>/\<Name_der_Verbindung> | Verbindungsname |
| ConnectingVNets_s | Durch Leerzeichen getrennte Liste mit Namen virtueller Netzwerke | Im Fall einer Hub-Spoke-Topologie werden hier virtuelle Hubnetzwerke aufgefüllt. |
| Country_s | Zweibuchstabiger Ländercode (ISO 3166-1 Alpha-2) | Wird für den Flowtyp „ExternalPublic“ aufgefüllt. Alle IP-Adressen im Feld „PublicIPs_s“ weisen den gleichen Ländercode auf. |
| AzureRegion_s | Standorte in der Azure-Region | Wird für den Flowtyp „AzurePublic“ aufgefüllt. Alle IP-Adressen im Feld „PublicIPs_s“ weisen die gleiche Azure-Region auf. |
| AllowedInFlows_d | | Anzahl der eingehenden Flows, die zugelassen wurden. Stellt die Anzahl von Flows dar, die das gleiche eingehende 4-Tupel an der Netzwerkschnittstelle aufweisen, an der der Flow erfasst wurde. | 
| DeniedInFlows_d |  | Anzahl der eingehenden Flows, die abgelehnt wurden. (Eingehend an der Netzwerkschnittstelle, an der der Flow erfasst wurde.) |
| AllowedOutFlows_d | | Anzahl der ausgehenden Flows, die zugelassen wurden. (Ausgehend an der Netzwerkschnittstelle, an der der Flow erfasst wurde.) |
| DeniedOutFlows_d  | | Anzahl der ausgehenden Flows, die abgelehnt wurden. (Ausgehend an der Netzwerkschnittstelle, an der der Flow erfasst wurde.) |
| FlowCount_d | Veraltet. Gesamtanzahl von Flows, die dem gleichen 4-Tupel entsprechen. Bei den Flowtypen „ExternalPublic“ und „AzurePublic“ enthält diese Zahl auch die Flows aus verschiedenen PublicIP-Adressen.
| InboundPackets_d | Empfangene Pakete, die an der Netzwerkschnittstelle erfasst wurden, an der die NSG-Regel angewendet wurde | Dies wird nur für Version 2 des NSG-Flowprotokollschemas aufgefüllt. |
| OutboundPackets_d  | Gesendete Pakete, die an der Netzwerkschnittstelle erfasst wurden, an der die NSG-Regel angewendet wurde | Dies wird nur für Version 2 des NSG-Flowprotokollschemas aufgefüllt. |
| InboundBytes_d |  Empfangene Bytes, die an der Netzwerkschnittstelle erfasst wurden, an der die NSG-Regel angewendet wurde | Dies wird nur für Version 2 des NSG-Flowprotokollschemas aufgefüllt. |
| OutboundBytes_d | Gesendete Bytes, die an der Netzwerkschnittstelle erfasst wurden, an der die NSG-Regel angewendet wurde | Dies wird nur für Version 2 des NSG-Flowprotokollschemas aufgefüllt. |
| CompletedFlows_d  |  | Dies wird nur für Version 2 des NSG-Flowprotokollschemas mit einem Wert aufgefüllt, der nicht Null ist. |
| PublicIPs_s | <ÖFFENTLICHEP_IP>\|\<ANZAHL_GESTARTETER_FLOWS>\|\<ANZAHL_BEENDETER_FLOWS>\|\<AUSGEHENDE_PAKETE>\|\<EINGEHENDE_PAKETE>\|\<AUSGEHENDE_BYTES>\|\<EINGEHENDE_BYTES> | Einträge sind durch Balken getrennt. |
    
### <a name="notes"></a>Notizen
    
1. Bei AzurePublic- und ExternalPublic-Flows wird die IP der im Kundenbesitz befindlichen Azure-VM im Feld „VMIP_s“ aufgefüllt, öffentliche IP-Adressen werden im Feld „PublicIPs_s“ aufgefüllt. Bei diesen beiden Flowtypen müssen die Felder „VMIP_s“ und „PublicIPs_s“ anstelle von „SrcIP_s“ und „DestIP_s“ verwendet werden. Bei AzurePublic- und ExternalPublicIP-Adressen erfolgt eine weitere Aggregation, sodass die Anzahl der Datensätze, die im Log Analytics-Arbeitsbereich des Kunden erfasst werden, sehr gering ist. (Dieses Feld wird bald außer Dienst gestellt, stattdessen sollten „SrcIP_s“ und „DestIP_s“ verwendet werden, je nachdem, ob die Azure-VM die Quelle oder das Ziel im Flow war.) 
1. Details zu Flowtypen: Basierend auf den am Flow beteiligten IP-Adressen werden die Flows in die folgenden Flowtypen unterteilt: 
1. IntraVNet – beide IP-Adressen im Flow befinden sich im gleichen Azure Virtual Network. 
1. InterVNet – die IP-Adressen im Flow befinden sich in zwei verschiedenen Azure Virtual Networks. 
1. S2S (Site-to-Site) – eine der IP-Adressen gehört zu einem Azure Virtual Network, die andere zu einem Kundennetzwerk (Standort), das über ExpressRoute oder ein VPN-Gateway mit dem Azure Virtual Network verbunden ist. 
1. P2S (Point-to-Site) – eine der IP-Adressen gehört zu einem Azure Virtual Network, die andere zu einem Kundennetzwerk (Standort), das über ein VPN-Gateway mit dem Azure Virtual Network verbunden ist.
1. AzurePublic – eine der IP-Adressen gehört zu einem Azure Virtual Network, die andere zu internen öffentlichen Azure-IP-Adressen im Besitz von Microsoft. Öffentliche IP-Adressen im Besitz von Kunden gehören nicht zu diesem Flowtyp. Beispielsweise würde eine VM im Kundenbesitz, die Datenverkehr an einen Azure-Dienst (Storage-Endpunkt) sendet, in diesen Flowtyp kategorisiert. 
1. ExternalPublic – eine der IP-Adressen gehört zu einem Azure Virtual Network, die andere ist eine öffentliche IP-Adresse außerhalb von Azure. Diese wird in den ASC-Feeds, die Traffic Analytics im Verarbeitungsintervall zwischen „FlowIntervalStartTime_t“ und „FlowIntervalEndTime_t“ nutzt, nicht als schädlich gemeldet. 
1. MaliciousFlow – eine der IP-Adressen gehört zu einem Azure Virtual Network, die andere ist eine öffentliche IP-Adresse außerhalb von Azure. Diese wird in den ASC-Feeds, die Traffic Analytics im Verarbeitungsintervall zwischen „FlowIntervalStartTime_t“ und „FlowIntervalEndTime_t“ nutzt, als schädlich gemeldet. 
1. UnknownPrivate – eine der IP-Adressen gehört zu einem Azure Virtual Network, die andere zu einem privaten IP-Adressbereich, wie in RFC 1918 definiert. Diese kann von Traffic Analytics keinem Standort im Besitz eines Kunden und keinem Azure Virtual Network zugeordnet werden.
1. Unknown – keine der IP-Adressen in den Flows kann der Kundentopologie in Azure oder einem lokalen Standort zugeordnet werden.
1. An einige Feldnamen wird „_s“ oder „_d“ angefügt. Diese bezeichnen NICHT die Quelle (Source) und das Ziel (Destination).

### <a name="next-steps"></a>Nächste Schritte
Antworten auf häufig gestellte Fragen finden Sie in den [FAQ zu Traffic Analytics](traffic-analytics-faq.md). Weitere Informationen zur Funktionalität finden Sie in der [Dokumentation zu Traffic Analytics](traffic-analytics.md).
    


    


