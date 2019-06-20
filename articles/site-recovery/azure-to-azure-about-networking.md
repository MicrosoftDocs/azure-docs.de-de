---
title: Netzwerkkonzepte für die Notfallwiederherstellung zwischen Azure-Standorten mithilfe von Azure Site Recovery | Microsoft-Dokumentation
description: Bietet eine Übersicht über die Netzwerkkonzepte für die Replikation von Azure-VMs mithilfe von Azure Site Recovery.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 3/29/2019
ms.author: sujayt
ms.openlocfilehash: a6c9c690efe8b75cd1a939de1c68cf4e5bd40d70
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60789753"
---
# <a name="about-networking-in-azure-to-azure-replication"></a>Netzwerkkonzepte für die Replikation zwischen Azure-Standorten



Dieser Artikel bietet einen Netzwerkleitfaden für die Replikation und Wiederherstellung von Azure-VMs zwischen verschiedenen Regionen mithilfe von [Azure Site Recovery](site-recovery-overview.md).

## <a name="before-you-start"></a>Vorbereitung

Erfahren Sie, wie Site Recovery die Notfallwiederherstellung für [dieses Szenario](azure-to-azure-architecture.md) bereitstellt.

## <a name="typical-network-infrastructure"></a>Typische Netzwerkinfrastruktur

Das folgende Diagramm zeigt eine typische Azure-Umgebung für Anwendungen, die auf Azure-VMs ausgeführt werden:

![Kundenumgebung](./media/site-recovery-azure-to-azure-architecture/source-environment.png)

Wenn Ihr lokales Netzwerk über Azure ExpressRoute oder VPN mit Azure verbunden ist, sieht die Umgebung wie folgt aus:

![Kundenumgebung](./media/site-recovery-azure-to-azure-architecture/source-environment-expressroute.png)

Normalerweise werden Netzwerke durch Firewalls und Netzwerksicherheitsgruppen (NSGs) geschützt. Firewalls verwenden eine URL- oder IP-basierte Whitelist zum Steuern der Netzwerkkonnektivität. NSGs stellen Regeln bereit, die IP-Adressbereiche zum Steuern der Netzwerkkonnektivität verwenden.

>[!IMPORTANT]
> Die Verwendung eines authentifizierten Proxys zum Steuern der Netzwerkkonnektivität wird von Site Recovery nicht unterstützt. In diesem Fall kann die Replikation nicht aktiviert werden.


## <a name="outbound-connectivity-for-urls"></a>Ausgehende Konnektivität für URLs

Lassen Sie die folgenden Site Recovery-URLs zu, wenn Sie einen URL-basierten Firewallproxy zum Steuern der ausgehenden Konnektivität verwenden:


**URL** | **Details**  
--- | ---
*.blob.core.windows.net | Erforderlich, damit Daten in das Cachespeicherkonto in der Quellregion über die VM geschrieben werden können. Wenn Sie alle Cachespeicherkonten für Ihre VMs kennen, können Sie die spezifischen Speicherkonten-URLs (z. B. cache1.blob.core.windows.net and cache2.blob.core.windows.net) anstelle von „*.blob.core.windows.net“ in die Whitelist aufnehmen.
login.microsoftonline.com | Erforderlich für die Autorisierung und Authentifizierung bei den Site Recovery-Dienst-URLs.
*.hypervrecoverymanager.windowsazure.com | Erforderlich, um die Kommunikation mit dem Site Recovery-Dienst über die VM zu ermöglichen. Sie können die entsprechende Site Recovery-IP verwenden, wenn Ihr Firewallproxy IPs unterstützt.
*.servicebus.windows.net | Erforderlich, damit die Site Recovery-Überwachungs- und -Diagnosedaten über die VM geschrieben werden können. Sie können die entsprechende Site Recovery-Überwachungs-IP verwenden, wenn Ihr Firewallproxy IPs unterstützt.

## <a name="outbound-connectivity-for-ip-address-ranges"></a>Ausgehende Konnektivität für IP-Adressbereiche

Wenn Sie einen IP-basierten Firewallproxy oder NSG-Regeln zum Steuern der ausgehenden Konnektivität verwenden, müssen die folgenden IP-Adressbereiche zugelassen werden.

- Alle IP-Adressbereiche, die den Speicherkonten am Quellstandort entsprechen.
    - Erstellen Sie ein [Speicherdiensttag](../virtual-network/security-overview.md#service-tags) basierend auf der NSG-Regel für die Quellregion.
    - Lassen Sie diese Adressen zu, damit Daten von der VM in das Cachespeicherkonto geschrieben werden können.
- Erstellen Sie basierend auf der NSG-Regel ein [Azure Active Directory-Diensttag (AAD)](../virtual-network/security-overview.md#service-tags) für den Zugriff auf alle IP-Adressen für AAD.
    - Wenn in Azure Active Directory (AAD) später neue Adressen hinzugefügt werden, müssen Sie neue NSG-Regeln erstellen.
- IP-Adressen von Site Recovery-Dienstendpunkten – in einer [XML-Datei ](https://aka.ms/site-recovery-public-ips) verfügbar und von Ihrem Zielstandort abhängig.
- Wir empfehlen, die erforderlichen NSG-Regeln in einer Test-Netzwerksicherheitsgruppe zu erstellen und sicherzustellen, dass keine Probleme vorliegen, bevor Sie die Regeln in einer Netzwerksicherheitsgruppe in der Produktionsumgebung erstellen.


Die Site Recovery-IP-Adressbereiche lauten wie folgt:

   **Ziel** | **IP-Adressen für Site Recovery** |  **IP-Adressen zur Site Recovery-Überwachung**
   --- | --- | ---
   Asien, Osten | 52.175.17.132 | 13.94.47.61
   Asien, Südosten | 52.187.58.193 | 13.76.179.223
   Indien, Mitte | 52.172.187.37 | 104.211.98.185
   Indien (Süden) | 52.172.46.220 | 104.211.224.190
   USA Nord Mitte | 23.96.195.247 | 168.62.249.226
   Nordeuropa | 40.69.212.238 | 52.169.18.8
   Europa, Westen | 52.166.13.64 | 40.68.93.145
   USA (Ost) | 13.82.88.226 | 104.45.147.24
   USA (Westen) | 40.83.179.48 | 104.40.26.199
   USA Süd Mitte | 13.84.148.14 | 104.210.146.250
   USA (Mitte) | 40.69.144.231 | 52.165.34.144
   USA (Ost) 2 | 52.184.158.163 | 40.79.44.59
   Japan, Osten | 52.185.150.140 | 138.91.1.105
   Japan, Westen | 52.175.146.69 | 138.91.17.38
   Brasilien Süd | 191.234.185.172 | 23.97.97.36
   Australien (Osten) | 104.210.113.114 | 191.239.64.144
   Australien, Südosten | 13.70.159.158 | 191.239.160.45
   Kanada, Mitte | 52.228.36.192 | 40.85.226.62
   Kanada, Osten | 52.229.125.98 | 40.86.225.142
   USA, Westen-Mitte | 52.161.20.168 | 13.78.149.209
   USA, Westen 2 | 52.183.45.166 | 13.66.228.204
   UK, Westen | 51.141.3.203 | 51.141.14.113
   UK, Süden | 51.140.43.158 | 51.140.189.52
   Vereinigtes Königreich, Süden 2 | 13.87.37.4| 13.87.34.139
   Vereinigtes Königreich, Norden | 51.142.209.167 | 13.87.102.68
   Korea, Mitte | 52.231.28.253 | 52.231.32.85
   Korea, Süden | 52.231.198.185 | 52.231.200.144
   Frankreich, Mitte | 52.143.138.106 | 52.143.136.55
   Frankreich, Süden | 52.136.139.227 |52.136.136.62
   Australien, Mitte| 20.36.34.70 | 20.36.46.142
   Australien, Mitte 2| 20.36.69.62 | 20.36.74.130
   Südafrika, Westen | 102.133.72.51 | 102.133.26.128
   Südafrika, Norden | 102.133.160.44 | 102.133.154.128
   US Government, Virginia | 52.227.178.114 | 23.97.0.197
   US Gov Iowa | 13.72.184.23 | 23.97.16.186
   US Gov Arizona | 52.244.205.45 | 52.244.48.85
   US Gov Texas | 52.238.119.218 | 52.238.116.60
   US DoD, Osten | 52.181.164.103 | 52.181.162.129
   US DoD, Mitte | 52.182.95.237 | 52.182.90.133
## <a name="example-nsg-configuration"></a>Beispielkonfiguration für eine Netzwerksicherheitsgruppe

Dieses Beispiel zeigt, wie NSG-Regeln für eine zu replizierende VM konfiguriert werden.

- Wenn Sie die ausgehende Konnektivität mit NSG-Regeln steuern, verwenden Sie für alle erforderlichen IP-Adressbereiche Regeln zum Zulassen ausgehender HTTPS-Verbindungen für „port:443“.
- Im Beispiel wird davon ausgegangen, dass der VM-Quellstandort „USA, Osten“ ist und der Zielstandort „USA, Mitte“.

### <a name="nsg-rules---east-us"></a>NSG-Regeln – USA, Osten

1. Erstellen Sie eine NSG-Sicherheitsregel für ausgehende HTTPS-Verbindungen (443) für „Storage.EastUS“, wie im Screenshot unten gezeigt.

      ![storage-tag](./media/azure-to-azure-about-networking/storage-tag.png)

2. Erstellen Sie eine NSG-Sicherheitsregel für ausgehende HTTPS-Verbindungen (443) für „AzureActiveDirectory“, wie im Screenshot unten gezeigt.

      ![aad-tag](./media/azure-to-azure-about-networking/aad-tag.png)

3. Erstellen Sie Regeln für ausgehende HTTPS-Verbindungen (443) für die Site Recovery-IP-Adressen, die dem Zielstandort entsprechen:

   **Location** | **IP-Adressen für Site Recovery** |  **IP-Adressen zur Site Recovery-Überwachung**
    --- | --- | ---
   USA (Mitte) | 40.69.144.231 | 52.165.34.144

### <a name="nsg-rules---central-us"></a>NSG-Regeln – USA, Mitte

Diese Regeln sind erforderlich, damit nach dem Failover die Replikation von der Zielregion zur Quellregion aktiviert werden kann:

1. Erstellen Sie eine NSG-Sicherheitsregel für ausgehende HTTPS-Verbindungen (443) für „Storage.CentralUS“.

2. Erstellen Sie eine NSG-Sicherheitsregel für ausgehende HTTPS-Verbindungen (443) für „AzureActiveDirectory“.

3. Erstellen Sie Regeln für ausgehende HTTPS-Verbindungen (443) für die Site Recovery-IP-Adressen, die dem Quellstandort entsprechen:

   **Location** | **IP-Adressen für Site Recovery** |  **IP-Adressen zur Site Recovery-Überwachung**
    --- | --- | ---
   USA (Mitte) | 13.82.88.226 | 104.45.147.24

## <a name="network-virtual-appliance-configuration"></a>Konfiguration der virtuellen Netzwerkappliance

Wenn Sie virtuelle Netzwerkappliances zur Steuerung des ausgehenden Netzwerkverkehrs von VMs verwenden, wird die Appliance möglicherweise gedrosselt, wenn der gesamte Replikationsdatenverkehr über die virtuelle Netzwerkappliance läuft. Es wird empfohlen, einen Netzwerk-Dienstendpunkt in Ihrem virtuellen Netzwerk für „Storage“ zu erstellen, damit der Replikationsdatenverkehr nicht an die virtuelle Netzwerkappliance geleitet wird.

### <a name="create-network-service-endpoint-for-storage"></a>Erstellen eines Netzwerk-Dienstendpunkts für Storage
Sie können einen Netzwerk-Dienstendpunkt in Ihrem virtuellen Netzwerk für „Storage“ erstellen, damit der Replikationsdatenverkehr die Azure-Grenze nicht überschreitet.

- Wählen Sie Ihre virtuelles Azure-Netzwerk aus, und klicken Sie auf „Dienstendpunkte“.

    ![storage-endpoint](./media/azure-to-azure-about-networking/storage-service-endpoint.png)

- Klicken Sie auf „Hinzufügen“, und die Registerkarte „Dienstendpunkte hinzufügen“ wird geöffnet.
- Wählen Sie unter „Dienst“ die Option „Microsoft.Storage“ und unter dem Feld „Subnetze“ die erforderlichen Subnetze, und klicken Sie auf „Hinzufügen“.

>[!NOTE]
>Beschränken Sie nicht den Zugriff des virtuellen Netzwerks auf Ihre für ASR verwendeten Speicherkonten. Sie sollten den Zugriff für „Alle Netzwerke“ zulassen.

### <a name="forced-tunneling"></a>Tunnelerzwingung

Sie können die Standardsystemroute von Azure für das Adresspräfix 0.0.0.0/0 mit einer [benutzerdefinierten Route](../virtual-network/virtual-networks-udr-overview.md#custom-routes) überschreiben und VM-Datenverkehr auf ein lokales virtuelles Netzwerkgerät umleiten, aber diese Konfiguration wird für die Site Recovery-Replikation nicht empfohlen. Wenn Sie benutzerdefinierte Routen verwenden, sollten Sie [einen VNET-Dienstendpunkt](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage) in Ihrem virtuellen Netzwerk für „Storage“ erstellen, damit der Replikationsdatenverkehr innerhalb der Azure-Begrenzung bleibt.

## <a name="next-steps"></a>Nächste Schritte
- Schützen Sie Ihre Workloads durch die [Replikation virtueller Azure-Computer](site-recovery-azure-to-azure.md).
- Weitere Informationen zur [Beibehaltung von IP-Adressen](site-recovery-retain-ip-azure-vm-failover.md) für das Failover von virtuellen Azure-Computern.
- Weitere Informationen zur Notfallwiederherstellung von [virtuellen Azure-Computern mit ExpressRoute ](azure-vm-disaster-recovery-with-expressroute.md).
