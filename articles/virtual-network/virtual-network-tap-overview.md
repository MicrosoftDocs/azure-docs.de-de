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
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/14/2019
ms.author: kaanan
ms.openlocfilehash: ff5c8c4d3f6a0c87afae67404a5a39d4fe3757d9
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60593181"
---
# <a name="virtual-network-tap"></a>TAP eines virtuellen Netzwerks

Mit dem TAP (Terminal Access Point) für virtuelle Azure-Netzwerke können Sie Ihren VM-Netzwerkdatenverkehr kontinuierlich an einen Netzwerkpaketcollector oder ein Analysetool streamen. Der Collector oder das Analysetool wird von einem Partner für [virtuelle Netzwerkappliances](https://azure.microsoft.com/solutions/network-appliances/) bereitgestellt. Eine Liste der Partnerlösungen, die für die Arbeit mit einem TAP eines virtuellen Netzwerks geprüft wurden, finden Sie unter [Partnerlösungen für TAPs von virtuellen Netzwerken](#virtual-network-tap-partner-solutions).

> [!IMPORTANT]
> Der TAP eines virtuellen Netzwerks ist derzeit in allen Azure-Regionen in der Vorschauversion. Um den TAP eines virtuellen Netzwerks verwenden zu können, müssen Sie sich bei der Vorschauversion registrieren, indem Sie eine E-Mail an  <azurevnettap@microsoft.com> mit Ihrer Abonnement-ID senden. Sie erhalten eine E-Mail zurück, nachdem Ihr Abonnement registriert wurde. Sie können die Funktion erst verwenden, wenn Sie eine Bestätigungs-E-Mail erhalten haben. Diese Vorschauversion wird ohne Servicelevelvereinbarung bereitgestellt und sollte nicht für Produktionsworkloads verwendet werden. Unter Umständen werden bestimmte Features nicht unterstützt, verfügen über eingeschränkte Funktionen und sind nicht an allen Azure-Standorten verfügbar. Weitere Informationen finden Sie unter  [Ergänzende Nutzungsbedingungen für Microsoft Azure-Vorschauversionen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) .

## <a name="virtual-network-tap-partner-solutions"></a>Partnerlösungen für TAPs von virtuellen Netzwerken

### <a name="network-packet-brokers"></a>Netzwerkpaketbroker

- [Big Switch Big Monitoring Fabric](https://www.bigswitch.com/products/big-monitoring-fabric/public-cloud/microsoft-azure)
- [Gigamon GigaSECURE](https://blog.gigamon.com/2018/09/13/why-microsofts-new-vtap-service-works-even-better-with-gigasecure-for-azure)
- [Ixia CloudLens](https://www.ixiacom.com/cloudlens/cloudlens-azure)
- [Nubeva Prisms](https://www.nubeva.com/azurevtap)

### <a name="security-analytics-networkapplication-performance-management"></a>Sicherheitsanalyse und Verwalten der Netzwerk-/Anwendungsleistung

- [Awake Security](https://awakesecurity.com/technology-partners/microsoft-azure/)
- [Cisco Stealthwatch Cloud](https://blogs.cisco.com/security/cisco-stealthwatch-cloud-and-microsoft-azure-reliable-cloud-infrastructure-meets-comprehensive-cloud-security)
- [Darktrace](https://www.darktrace.com/en/azure/)
- [ExtraHop Reveal(x)](https://www.extrahop.com/company/tech-partners/microsoft/)
- [Fidelis Cybersecurity](https://www.fidelissecurity.com/technology-partners/microsoft-azure )
- [Flowmon](https://www.flowmon.com/blog/azure-vtap)
- [NetFort LANGuardian](https://www.netfort.com/languardian/solutions/visibility-in-azure-network-tap/)
- [Netscout vSTREAM]( https://www.netscout.com/technology-partners/microsoft/azure-vtap)
- [RSA NetWitness® Platform](https://www.rsa.com/azure)
- [Vectra Cognito](https://vectra.ai/microsoftazure)

In der folgenden Abbildung wird gezeigt, wie der TAP des virtuellen Netzwerks funktioniert. Sie können eine [Netzwerkschnittstelle](virtual-network-network-interface.md) um eine TAP-Konfiguration erweitern, die einem in Ihrem virtuellen Netzwerk bereitgestellten virtuellen Computer angefügt ist. Das Ziel ist eine IP-Adresse eines virtuellen Netzwerks, das sich im gleichen virtuellen Netzwerk wie die überwachte Netzwerkschnittstelle oder einem [mittels Peering verknüpften virtuellen Netzwerk](virtual-network-peering-overview.md) befindet. Die Collectorlösung für den TAP des virtuellen Netzwerks kann für Hochverfügbarkeitszwecke hinter einem [internen Azure-Lastenausgleich](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#concepts) bereitgestellt werden. Informationen zur Auswertung von Bereitstellungsoptionen für einzelne Lösungen finden Sie unter [Partnerlösungen für TAPs von virtuellen Netzwerken](#virtual-network-tap-partner-solutions).

![Funktionsweise eines TAP eines virtuellen Netzwerks](./media/virtual-network-tap/architecture.png)

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie einen TAP für ein virtuelles Netzwerk erstellen können, müssen Sie eine E-Mail erhalten haben, die bestätigt, dass Sie für die Vorschauversion registriert sind, und mindestens einen virtuellen Computer mit dem [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)-Bereitstellungsmodell und einer Partnerlösung zum Aggregieren des TAP-Datenverkehrs in derselben Azure-Region erstellt haben. Wenn keine Partnerlösung in Ihrem virtuellen Netzwerk vorhanden ist, lesen Sie [Partnerlösungen für TAPs von virtuellen Netzwerken](#virtual-network-tap-partner-solutions), um eine solche Lösung bereitzustellen. Mit derselben TAP-Ressource für virtuelle Netzwerke können Sie Datenverkehr von mehreren Netzwerkschnittstellen in der gleichen oder in unterschiedlichen Abonnements aggregieren. Wenn sich die überwachten Netzwerkschnittstellen in verschiedenen Abonnements befinden, müssen die Abonnements demselben Azure Active Directory-Mandanten zugeordnet sein. Darüber hinaus können sich die überwachten Netzwerkschnittstellen und der Endpunkt des Ziels zum Aggregieren des TAP-Datenverkehrs in der gleichen Region befinden, in der die mittels Peering verknüpften virtuellen Netzwerken vorhanden sind. Stellen Sie bei diesem Bereitstellungsmodell sicher, dass das [Peering virtueller Netzwerke](virtual-network-peering-overview.md) aktiviert ist, bevor Sie einen TAP für virtuelle Netzwerke konfigurieren.

## <a name="permissions"></a>Berechtigungen

Die Konten, mit denen Sie die TAP-Konfiguration auf Netzwerkschnittstellen anwenden, müssen der Rolle [Netzwerkmitwirkender](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) oder einer [benutzerdefinierten](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Rolle zugewiesen sein, der die erforderlichen Aktionen aus der folgenden Tabelle zugewiesen ist:

| Aktion | NAME |
|---|---|
| Microsoft.Network/virtualNetworkTaps/* | Erforderlich, um eine TAP-Ressource für virtuelle Netzwerke zu erstellen, zu aktualisieren, zu lesen und zu löschen |
| Microsoft.Network/networkInterfaces/read | Erforderlich, um die Netzwerkschnittstellenressource zu lesen, für die der TAP konfiguriert wird |
| Microsoft.Network/tapConfigurations/* | Erforderlich, um die TAP-Konfiguration für eine Netzwerkschnittstelle zu erstellen, zu aktualisieren, zu lesen und zu löschen |

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie, wie Sie [einen TAP für ein virtuelles Netzwerk erstellen](tutorial-tap-virtual-network-cli.md).
