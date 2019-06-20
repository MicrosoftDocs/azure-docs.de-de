---
title: Vorbereiten lokaler Hyper-V-Server für die Notfallwiederherstellung von Hyper-V-VMs in Azure | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie lokale Hyper-V-VMs mit dem Azure Site Recovery-Dienst für die Notfallwiederherstellung in Azure vorbereiten.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 05/30/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: aec5632b5ea29d52426c5d065ca41e18573fd5b9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66399386"
---
# <a name="prepare-on-premises-hyper-v-servers-for-disaster-recovery-to-azure"></a>Vorbereiten lokaler Hyper-V-Server für die Notfallwiederherstellung in Azure

In diesem Artikel wird beschrieben, wie Sie Ihre lokale Hyper-V-Infrastruktur vorbereiten, wenn Sie mit [Azure Site Recovery](site-recovery-overview.md) eine Notfallwiederherstellung von virtuellen Hyper-V-Computern für Azure einrichten möchten.


Dies ist das zweite Tutorial in einer Reihe, welche die Einrichtung der Notfallwiederherstellung in Azure für lokale virtuelle Hyper-V-Computer veranschaulicht. Im [ersten Tutorial](tutorial-prepare-azure.md) haben wir die erforderlichen Azure-Komponenten für die Hyper-V-Notfallwiederherstellung eingerichtet.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Überprüfen der Hyper-V-Anforderungen und VMM-Anforderungen, wenn Ihre Hyper-V-Hosts von System Center VMM verwaltet werden
> * Vorbereiten von VMM, falls erforderlich
> * Überprüfen des Internetzugriffs auf Azure-Standorte
> * Vorbereiten von VMs, damit nach einem Failover in Azure auf sie zugegriffen werden kann

> [!NOTE]
> In den Tutorials wird der einfachste Bereitstellungspfad für ein Szenario erläutert. Sie verwenden nach Möglichkeit Standardoptionen und zeigen nicht alle möglichen Einstellungen und Pfade. Ausführliche Anweisungen finden Sie in dem Artikel, der im Anleitungsabschnitt des Site Recovery-Inhaltsverzeichnisses angegeben ist.

## <a name="before-you-start"></a>Vorbereitung

Vergewissern Sie sich, dass Azure wie im [ersten Tutorial dieser Reihe](tutorial-prepare-azure.md) beschrieben vorbereitet wurde.

## <a name="review-requirements-and-prerequisites"></a>Überprüfen von Anforderungen und Voraussetzungen

Stellen Sie sicher, dass Hyper-V-Hosts und VMs die Anforderungen erfüllen.

1. [Überprüfen](hyper-v-azure-support-matrix.md#on-premises-servers) Sie lokale Serveranforderungen.
2. [Überprüfen Sie die Anforderungen](hyper-v-azure-support-matrix.md#replicated-vms) für Hyper-V-VMs, die Sie in Azure replizieren möchten.
3. Überprüfen Sie die Hyper-V-[Hostnetzwerke](hyper-v-azure-support-matrix.md#hyper-v-network-configuration) sowie die [Speicherunterstützung](hyper-v-azure-support-matrix.md#hyper-v-host-storage) durch Host- und Gastcomputer für lokale Hyper-V-Hosts.
4. Überprüfen Sie, was nach einem Failover für [Azure-Netzwerke](hyper-v-azure-support-matrix.md#azure-vm-network-configuration-after-failover), [Speicher](hyper-v-azure-support-matrix.md#azure-storage) und [Computer](hyper-v-azure-support-matrix.md#azure-compute-features) unterstützt wird.
5. Ihre lokalen virtuellen Computer, die Sie in Azure replizieren, müssen die [Azure-VM-Anforderungen](hyper-v-azure-support-matrix.md#azure-vm-requirements) einhalten.


## <a name="prepare-vmm-optional"></a>Vorbereiten von VMM (optional)

Wenn Hyper-V-Hosts von VMM verwaltet werden, müssen Sie den lokalen VMM-Server vorbereiten. 

- Stellen Sie sicher, dass der VMM-Server über mindestens eine Cloud mit mindestens einer Hostgruppe verfügt. Der Hyper-V-Host, auf dem virtuelle Computer ausgeführt werden, sollte sich in der Cloud befinden.
- Bereiten Sie den VMM-Server für die Netzwerkzuordnung vor.

### <a name="prepare-vmm-for-network-mapping"></a>Vorbereiten von VMM für die Netzwerkzuordnung

Bei Verwendung von VMM gilt die [Netzwerkzuordnung](site-recovery-network-mapping.md) zwischen lokalen VMM-VM-Netzwerken und virtuellen Azure-Netzwerken. Die Zuordnung stellt sicher, dass Azure-VMs mit dem richtigen Netzwerk verbunden sind, wenn sie nach einem Failover erstellt werden.

Bereiten Sie VMM wie folgt für die Netzwerkzuordnung vor:

1. Bereiten Sie ein [logisches VMM-Netzwerk](https://docs.microsoft.com/system-center/vmm/network-logical) vor, das der Cloud zugeordnet ist, in der sich die Hyper-V-Hosts befinden.
2. Stellen Sie sicher, dass Sie über ein [VM-Netzwerk](https://docs.microsoft.com/system-center/vmm/network-virtual) verfügen, das mit dem logischen Netzwerk verknüpft ist.
3. Verbinden Sie in VMM die virtuellen Computer mit dem VM-Netzwerk.

## <a name="verify-internet-access"></a>Überprüfen des Internetzugriffs

1. Im Rahmen des Tutorials besteht die einfachste Konfiguration darin, dass die Hyper-V-Hosts und VMM-Server direkten Zugriff auf das Internet haben, ohne einen Proxy zu verwenden. 
2. Stellen Sie sicher, dass Hyper-V-Hosts und ggf. der VMM-Server auf die erforderlichen URLs zugreifen können, die weiter unten angegeben sind.   
3. Wenn Sie den Zugriff über die IP-Adresse steuern, stellen Sie Folgendes sicher:
    - Bei Verwendung von auf IP-Adressen basierenden Firewallregeln muss eine Verbindung mit [IP-Bereichen für das Azure-Rechenzentrum](https://www.microsoft.com/download/confirmation.aspx?id=41653) und dem HTTPS-Port (443) möglich sein.
    - Lassen Sie IP-Adressbereiche für die Azure-Region Ihres Abonnements zu.
    
### <a name="required-urls"></a>Erforderliche URLs


[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]


## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Vorbereiten der Verbindungsherstellung mit Azure-VMs nach dem Failover

In einem Failoverszenario sollten Sie eine Verbindung mit Ihrem replizierten lokalen Netzwerk herstellen können.

Lassen Sie den Zugriff wie folgt zu, um die Verbindung mit Windows-VMs nach dem Failover per RDP herzustellen:

1. Aktivieren Sie vor dem Failover RDP auf dem lokalen virtuellen Computer, um über das Internet darauf zuzugreifen. Stellen Sie sicher, dass TCP- und UDP-Regeln für das Profil **Öffentlich** hinzugefügt werden und dass RDP unter **Windows-Firewall** > **Zugelassene Apps** für alle Profile zugelassen ist.
2. Für den Zugriff über ein Site-to-Site-VPN aktivieren Sie RDP auf dem lokalen Computer. RDP sollte unter **Windows-Firewall** -> **Zugelassene Apps und Feature** für **private und Domänennetzwerke** zugelassen werden.
   Achten Sie darauf, dass die SAN-Richtlinie des Betriebssystems auf **OnlineAll** festgelegt ist. [Weitere Informationen](https://support.microsoft.com/kb/3031135) Auf dem virtuellen Computer sollten keine ausstehenden Windows-Updates vorhanden sein, wenn Sie ein Failover auslösen. Andernfalls können Sie sich nach Abschluss des Updates nicht mehr bei dem virtuellen Computer anmelden.
3. Aktivieren Sie nach dem Failover auf der Windows-Azure-VM die **Startdiagnose**, um einen Screenshot des virtuellen Computers anzuzeigen. Wenn Sie keine Verbindung herstellen können, überprüfen Sie, ob der virtuelle Computer ausgeführt wird, und sehen sich dann diese [Tipps zur Problembehandlung](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx) an.

Nach dem Failover können Sie mit derselben IP-Adresse wie die lokale replizierte VM oder einer anderen IP-Adresse zugreifen. [Erfahren Sie mehr](concepts-on-premises-to-azure-networking.md) zum Einrichten der IP-Adressierung für das Failover.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Einrichten der Notfallwiederherstellung in Azure für Hyper-V-VMs](tutorial-hyper-v-to-azure.md)
> [Einrichten der Notfallwiederherstellung in Azure für Hyper-V-VMs in VMM-Clouds](tutorial-hyper-v-vmm-to-azure.md)
