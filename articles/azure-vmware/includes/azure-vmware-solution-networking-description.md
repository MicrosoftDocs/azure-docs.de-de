---
title: Azure VMware Solution-Netzwerkbetrieb und -Konnektivität
description: Beschreibung von Azure VMware Solution-Netzwerkbetrieb und -Konnektivität
ms.topic: include
ms.date: 09/28/2020
ms.openlocfilehash: 47b5fab8430f4e462f1e4a0e8cb6ddb69ec38c02
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/29/2020
ms.locfileid: "92925032"
---
<!-- Used in introduction.md and concepts-networking.md -->

Azure VMware Solution stellt eine private Cloud bereit, auf die aus lokalen und Azure-basierten Umgebungen oder Ressourcen aus zugegriffen werden kann. Dienste wie Azure ExpressRoute und VPN-Verbindungen stellen die Konnektivität bereit. Diese Dienste benötigen bestimmte Netzwerkadressbereiche und Firewallports für die Aktivierung der Dienste.

Wenn eine private Cloud bereitgestellt wird, werden private Netzwerke für Verwaltung, Bereitstellung und vMotion erstellt. Verwenden Sie diese privaten Netzwerke, um auf vCenter und NSX-T Manager zuzugreifen sowie vMotion oder Bereitstellung virtueller Computer umzusetzen.  ExpressRoute Global Reach wird verwendet, um private Clouds mit lokalen Umgebungen zu verbinden. Die Verbindung erfordert ein virtuelles Netzwerk mit einer ExpressRoute-Leitung in Ihrem Abonnement.

Ressourcen, etwa Webserver und virtuelle Computer, sind über die öffentliche IP-Adresse von Azure Virtual WAN für das Internet zugänglich.  Der Internetzugriff ist für neue private Clouds standardmäßig deaktiviert. Weitere Informationen finden Sie unter [Verwenden der Funktion „öffentliche IP-Adresse“ in Azure VMware Solution](../public-ip-usage.md).