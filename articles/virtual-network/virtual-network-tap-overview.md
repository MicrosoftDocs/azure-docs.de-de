---
title: TAP eines virtuellen Azure-Netzwerks – Übersicht | Microsoft-Dokumentation
description: Erfahren Sie mehr über TAPs von virtuellen Netzwerken. Ein TAP eines virtuellen Netzwerks bietet eine umfassende Kopie des VM-Netzwerkdatenverkehrs, der an einen Paketcollector gestreamt werden kann.
services: virtual-network
documentationcenter: na
author: karthikananth
manager: ganesr
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/14/2019
ms.author: kaanan
ms.openlocfilehash: 8347dcd60dcdfbf03f52e25eb24744909c69465a
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/07/2021
ms.locfileid: "99804869"
---
# <a name="virtual-network-tap"></a>TAP eines virtuellen Netzwerks
> [!IMPORTANT]
> Die Vorschau des TAP für virtuelle Netzwerke ist derzeit in allen Azure-Regionen ausgesetzt. Sie können eine E-Mail mit Ihrer Abonnement-ID an <azurevnettap@microsoft.com> senden. Wir informieren Sie über künftige Updates zur Vorschau. In der Zwischenzeit können Sie Agent-basierte Lösungen oder NVA-Lösungen verwenden, die über unsere im [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/networking?page=1&subcategories=appliances%3Ball&search=Network%20Traffic&filters=partners) verfügbaren [Paketbroker-Partnerlösungen](#virtual-network-tap-partner-solutions) Funktionen für TAP-/Netzwerksichtbarkeit bieten.

Mit dem TAP (Terminal Access Point) für virtuelle Azure-Netzwerke können Sie Ihren VM-Netzwerkdatenverkehr kontinuierlich an einen Netzwerkpaketcollector oder ein Analysetool streamen. Der Collector oder das Analysetool wird von einem Partner für [virtuelle Netzwerkappliances](https://azure.microsoft.com/solutions/network-appliances/) bereitgestellt. Eine Liste der Partnerlösungen, die für die Arbeit mit einem TAP eines virtuellen Netzwerks geprüft wurden, finden Sie unter [Partnerlösungen für TAPs von virtuellen Netzwerken](#virtual-network-tap-partner-solutions).
In der folgenden Abbildung wird gezeigt, wie der TAP des virtuellen Netzwerks funktioniert. Sie können eine [Netzwerkschnittstelle](virtual-network-network-interface.md) um eine TAP-Konfiguration erweitern, die einem in Ihrem virtuellen Netzwerk bereitgestellten virtuellen Computer angefügt ist. Das Ziel ist eine IP-Adresse eines virtuellen Netzwerks, das sich im gleichen virtuellen Netzwerk wie die überwachte Netzwerkschnittstelle oder einem [mittels Peering verknüpften virtuellen Netzwerk](virtual-network-peering-overview.md) befindet. Die Collectorlösung für den TAP des virtuellen Netzwerks kann für Hochverfügbarkeitszwecke hinter einem internen Azure-Lastenausgleich bereitgestellt werden.
![Funktionsweise eines TAP eines virtuellen Netzwerks](./media/virtual-network-tap/architecture.png)

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie einen TAP für virtuelle Netzwerke erstellen können, müssen Sie eine E-Mail erhalten haben, die bestätigt, dass Sie für die Vorschauversion registriert sind, und mindestens einen mit dem [Azure Resource Manager](../azure-resource-manager/management/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)-Bereitstellungsmodell erstellten virtuellen Computer und eine Partnerlösung zum Aggregieren des TAP-Datenverkehrs in derselben Azure-Region besitzen. Wenn keine Partnerlösung in Ihrem virtuellen Netzwerk vorhanden ist, lesen Sie [Partnerlösungen für TAPs von virtuellen Netzwerken](#virtual-network-tap-partner-solutions), um eine solche Lösung bereitzustellen. Mit derselben TAP-Ressource für virtuelle Netzwerke können Sie Datenverkehr von mehreren Netzwerkschnittstellen in der gleichen oder in unterschiedlichen Abonnements aggregieren. Wenn sich die überwachten Netzwerkschnittstellen in verschiedenen Abonnements befinden, müssen die Abonnements demselben Azure Active Directory-Mandanten zugeordnet sein. Darüber hinaus können sich die überwachten Netzwerkschnittstellen und der Endpunkt des Ziels zum Aggregieren des TAP-Datenverkehrs in der gleichen Region befinden, in der die mittels Peering verknüpften virtuellen Netzwerken vorhanden sind. Stellen Sie bei diesem Bereitstellungsmodell sicher, dass das [Peering virtueller Netzwerke](virtual-network-peering-overview.md) aktiviert ist, bevor Sie einen TAP für virtuelle Netzwerke konfigurieren.

## <a name="permissions"></a>Berechtigungen

Die Konten, mit denen Sie die TAP-Konfiguration auf Netzwerkschnittstellen anwenden, müssen der Rolle [Netzwerkmitwirkender](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) oder einer [benutzerdefinierten](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Rolle zugewiesen sein, der die erforderlichen Aktionen aus der folgenden Tabelle zugewiesen ist:

| Aktion | Name |
|---|---|
| Microsoft.Network/virtualNetworkTaps/* | Erforderlich, um eine TAP-Ressource für virtuelle Netzwerke zu erstellen, zu aktualisieren, zu lesen und zu löschen |
| Microsoft.Network/networkInterfaces/read | Erforderlich, um die Netzwerkschnittstellenressource zu lesen, für die der TAP konfiguriert wird |
| Microsoft.Network/tapConfigurations/* | Erforderlich, um die TAP-Konfiguration für eine Netzwerkschnittstelle zu erstellen, zu aktualisieren, zu lesen und zu löschen |


## <a name="virtual-network-tap-partner-solutions"></a>Partnerlösungen für TAPs von virtuellen Netzwerken

### <a name="network-packet-brokers"></a>Netzwerkpaketbroker

- [GigaVUE Cloud Suite for Azure](https://www.gigamon.com/solutions/cloud/public-cloud/gigavue-cloud-suite-azure.html)
- [Ixia CloudLens](https://www.ixiacom.com/cloudlens/cloudlens-azure)
- [Nubeva Prisms](https://www.nubeva.com/azurevtap)
- [Big Switch Big Monitoring Fabric](https://www.bigswitch.com/products/big-monitoring-fabric/public-cloud/microsoft-azure)

### <a name="security-analytics-networkapplication-performance-management"></a>Sicherheitsanalyse und Verwalten der Netzwerk-/Anwendungsleistung

- [Awake Security](https://awakesecurity.com/technology-partners/microsoft-azure/)
- [Cisco Stealthwatch Cloud](https://blogs.cisco.com/security/cisco-stealthwatch-cloud-and-microsoft-azure-reliable-cloud-infrastructure-meets-comprehensive-cloud-security)
- [Darktrace](https://www.darktrace.com/en/azure/)
- [ExtraHop Reveal(x)](https://www.extrahop.com/partners/tech-partners/microsoft/)
- [Fidelis Cybersecurity](https://www.fidelissecurity.com/technology-partners/microsoft-azure )
- [Flowmon](https://www.flowmon.com/blog/azure-vtap)
- [NetFort LANGuardian](https://www.netfort.com/languardian/solutions/visibility-in-azure-network-tap/)
- [Netscout vSTREAM]( https://www.netscout.com/marketplace-azure)
- [Riverbed SteelCentral AppResponse]( https://www.riverbed.com/products/steelcentral/steelcentral-appresponse-11.html)
- [RSA NetWitness® Platform](https://www.rsa.com/content/dam/en/solution-brief/rsa-netwitness-platform-overview-for-federal-agencies.pdf)
- [Vectra Cognito](https://vectra.ai/microsoftazure)



## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie, wie Sie [einen TAP für ein virtuelles Netzwerk erstellen](tutorial-tap-virtual-network-cli.md).
