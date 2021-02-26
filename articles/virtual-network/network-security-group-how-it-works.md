---
title: Netzwerksicherheitsgruppe – Funktionsweise
titlesuffix: Azure Virtual Network
description: Erfahren Sie, wie Sie mithilfe von Netzwerksicherheitsgruppen den Netzwerkdatenverkehr zwischen Azure-Ressourcen filtern können.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/24/2020
ms.author: kumud
ms.reviewer: kumud
ms.openlocfilehash: 9510c4b0940a0a03ae9a232c3329817468aaf358
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99537897"
---
# <a name="how-network-security-groups-filter-network-traffic"></a>Filtern von Netzwerkdatenverkehr mit Netzwerksicherheitsgruppen
<a name="network-security-groups"></a>

Sie können eine Azure-Netzwerksicherheitsgruppe verwenden, um Netzwerkdatenverkehr von und zu Azure-Ressourcen in einem virtuellen Azure-Netzwerk zu filtern. Eine Netzwerksicherheitsgruppe enthält [Sicherheitsregeln](./network-security-groups-overview.md#security-rules), die eingehenden Netzwerkdatenverkehr an verschiedene Typen von Azure-Ressourcen oder ausgehenden Netzwerkdatenverkehr von diesen zulassen oder verweigern. Für jede Regel können Sie die Quelle, das Ziel, den Port und das Protokoll angeben.

Sie können Ressourcen von mehreren Azure-Diensten in einem virtuellen Azure-Netzwerk bereitstellen. Eine vollständige Liste finden Sie unter [Dienste, die in einem virtuellen Netzwerk bereitgestellt werden können](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network). Sie können jedem [Subnetz](virtual-network-manage-subnet.md#change-subnet-settings) eines virtuellen Netzwerks und jeder [Netzwerkschnittstelle](virtual-network-network-interface.md#associate-or-dissociate-a-network-security-group) eines virtuellen Computers keine oder eine Netzwerksicherheitsgruppe zuordnen. Sie können dieselbe Netzwerksicherheitsgruppe beliebig vielen Subnetzen und Netzwerkschnittstellen zuordnen.

Die folgende Abbildung veranschaulicht verschiedene Szenarien dazu, wie Netzwerksicherheitsgruppen bereitgestellt werden können, um Netzwerkdatenverkehr zum und aus dem Internet über TCP-Port 80 zuzulassen:

![NSG-Verarbeitung](./media/network-security-group-how-it-works/network-security-group-interaction.png)

Sehen Sie sich die Abbildung oben zusammen mit dem folgenden Text an, um weitere Informationen dazu zu erhalten, wie Azure ein- und ausgehende Regeln für Netzwerksicherheitsgruppen verarbeitet:

## <a name="inbound-traffic"></a>Eingehender Datenverkehr

Für eingehenden Datenverkehr verarbeitet Azure zuerst die Regeln in einer Netzwerksicherheitsgruppe, die einem Subnetz (sofern vorhanden) zugeordnet sind, und anschließend die Regeln in einer Netzwerksicherheitsgruppe, die der Netzwerkschnittstelle (sofern vorhanden) zugeordnet sind.

- **VM1**: Die Sicherheitsregeln in *NSG1* werden verarbeitet, da diese Netzwerksicherheitsgruppe *Subnet1* zugeordnet ist und *VM1* sich in *Subnet1* befindet. Sofern Sie keine Regel erstellt haben, die Port 80 als eingehenden Port zulässt, wird der Datenverkehr von der Standardsicherheitsregel [DenyAllInbound](./network-security-groups-overview.md#denyallinbound) abgelehnt und von *NSG2* nicht ausgewertet, da *NSG2* der Netzwerkschnittstelle zugeordnet ist. Wenn *NSG1* über eine Sicherheitsregel verfügt, die Port 80 zulässt, wird der Datenverkehr von *NSG2* verarbeitet. Damit Port 80 für den virtuellen Computer zulässig ist, müssen sowohl *NSG1* als auch *NSG2* über eine Regel verfügen, die Port 80 aus dem Internet zulässt.
- **VM2**: Die Regeln in *NSG1* werden verarbeitet, da *VM2* sich auch in *Subnet1* befindet. Da *VM2* über keine Netzwerksicherheitsgruppe verfügt, die seiner Netzwerkschnittstelle zugeordnet ist, empfängt er den gesamten zulässigen Datenverkehr über *NSG1*, oder der gesamte Datenverkehr wird durch *NSG1* verweigert. Datenverkehr wird für alle Ressourcen im selben Subnetz entweder zugelassen oder verweigert, wenn eine Netzwerksicherheitsgruppe einem Subnetz zugeordnet ist.
- **VM3**: Da *Subnet2* keine Netzwerksicherheitsgruppe zugeordnet ist, ist Datenverkehr in das Subnetz zulässig und wird von *NSG2* verarbeitet, da *NSG2* der Netzwerkschnittstelle zugeordnet ist, die *VM3* angefügt ist.
- **VM4**: Datenverkehr für *VM4* ist zulässig, da *Subnet3* oder der Netzwerkschnittstelle in der VM keine Netzwerksicherheitsgruppe zugeordnet ist. Der gesamte Netzwerkdatenverkehr ist über ein Subnetz und eine Netzwerkschnittstelle zulässig, wenn diesen keine Netzwerksicherheitsgruppe zugeordnet ist.

## <a name="outbound-traffic"></a>Ausgehender Datenverkehr

Für ausgehenden Datenverkehr verarbeitet Azure zuerst die Regeln in einer Netzwerksicherheitsgruppe, die einer Netzwerkschnittstelle (sofern vorhanden) zugeordnet sind, und anschließend die Regeln in einer Netzwerksicherheitsgruppe, die dem Subnetz (sofern vorhanden) zugeordnet sind.

- **VM1**: Die Sicherheitsregeln in *NSG2* werden verarbeitet. Sofern Sie keine Sicherheitsregel erstellen, die Port 80 als ausgehenden Port zum Internet zulässt, wird der Datenverkehr von der Standardsicherheitsregel [AllowInternetOutbound](./network-security-groups-overview.md#allowinternetoutbound) in *NSG1* sowie *NSG2* zugelassen. Wenn *NSG2* über eine Sicherheitsregel verfügt, die Port 80 ablehnt, wird der Datenverkehr abgelehnt und nicht von *NSG1* ausgewertet. Zum Verweigern von Port 80 vom virtuellen Computer oder beiden Netzwerksicherheitsgruppen ist eine Regel erforderlich, die Port 80 für das Internet verweigert.
- **VM2**: Der gesamte Datenverkehr wird über die Netzwerkschnittstelle an das Subnetz gesendet, da der Netzwerkschnittstelle, die an *VM2* angefügt ist, keine Netzwerksicherheitsgruppe zugeordnet ist. Die Regeln in *NSG1* werden verarbeitet.
- **VM3**: Wenn *NSG2* über eine Sicherheitsregel verfügt, die Port 80 verweigert, wird der Datenverkehr verweigert. Wenn *NSG2* über eine Sicherheitsregel verfügt, die Port 80 zulässt, dann ist Port 80 als ausgehender Port für das Internet zulässig, da *Subnet2* keine Netzwerksicherheitsgruppe zugeordnet ist.
- **VM4**: Der gesamte Netzwerkdatenverkehr von *VM4* ist zulässig, da der Netzwerkschnittstelle, die an die VM angefügt ist, oder *Subnet3* keine Netzwerksicherheitsgruppe zugeordnet ist.


## <a name="intra-subnet-traffic"></a>Subnetzinterner Datenverkehr

Hier muss darauf hingewiesen werden, dass Sicherheitsregeln in einer Netzwerksicherheitsgruppe, die einem Subnetz zugeordnet ist, die Konnektivität von darin enthaltenen VMs beeinflussen kann. Wenn z. B. eine Regel zu *NSG1* hinzugefügt wird, die den gesamten ein- und ausgehenden Datenverkehr sperrt, können *VM1* und *VM2* nicht mehr miteinander kommunizieren. Um dies möglich zu machen, müsste eine weitere spezielle Regel hinzugefügt werden. 

Sie können die Aggregatregeln, die auf eine Netzwerkschnittstelle angewendet werden, leicht prüfen, indem Sie die [effektiven Sicherheitsregeln](virtual-network-network-interface.md#view-effective-security-rules) für eine Netzwerkschnittstelle anzeigen. Sie können auch in Azure Network Watcher die Funktion [Überprüfen des IP-Flusses](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json) verwenden, um zu ermitteln, ob die Kommunikation für eine Netzwerkschnittstelle in ein- oder ausgehender Richtung zulässig ist. Die IP-Datenflussüberprüfung gibt an, ob die Kommunikation zugelassen oder verweigert wird und für welche Netzwerksicherheitsregel Datenverkehr zugelassen ist oder verweigert wird.

> [!NOTE]
> Netzwerksicherheitsgruppen sind bei der Bereitstellung im klassischen Bereitstellungsmodell Subnetzen oder virtuellen Computern und Clouddiensten zugeordnet sowie Subnetzen oder Netzwerkschnittstellen gemäß dem Resource Manager-Bereitstellungsmodell. Weitere Informationen zu den Azure-Bereitstellungsmodellen finden Sie unter [Azure Resource Manager-Bereitstellung im Vergleich zur klassischen Bereitstellung: Grundlegendes zu Bereitstellungsmodellen und zum Status von Ressourcen](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

> [!TIP]
> Wir empfehlen Ihnen, eine Netzwerksicherheitsgruppe einem Subnetz oder einer Netzwerkschnittstelle zuzuordnen, aber nicht beiden, sofern kein zwingender Grund dafür vorliegt. Da Regeln in einer Netzwerksicherheitsgruppe, die einem Subnetz zugeordnet ist, unter Umständen mit Regeln in einer Netzwerksicherheitsgruppe, die einer Netzwerkschnittstelle zugeordnet sind, in Konflikt stehen, können unerwartete Kommunikationsprobleme auftreten, die behoben werden müssen.

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen über die Azure-Ressourcen, die in einem virtuellen Netzwerk bereitgestellt werden können und denen Netzwerksicherheitsgruppen zugeordnet sind, finden Sie unter [Integration virtueller Netzwerke für Azure-Dienste](virtual-network-for-azure-services.md).
* Wenn Sie noch nie eine Netzwerksicherheitsgruppe erstellt haben, gehen Sie dieses kurze [Tutorial](tutorial-filter-network-traffic.md) durch, um sich mit dem Erstellen von Netzwerksicherheitsgruppen vertraut zu machen.
* Wenn Sie sich mit Netzwerksicherheitsgruppen auskennen und diese verwalten müssen, finden Sie unter [Erstellen, Ändern oder Löschen einer Netzwerksicherheitsgruppe](manage-network-security-group.md) weitere Informationen. 
* Wenn Kommunikationsschwierigkeiten auftreten und Sie Probleme mit Netzwerksicherheitsgruppen beheben müssen, finden Sie unter [Diagnostizieren von Problemen mit dem Filter für Netzwerkdatenverkehr eines virtuellen Computers](diagnose-network-traffic-filter-problem.md) weitere Informationen. 
* Informieren Sie sich über die Aktivierung von [Netzwerksicherheitsgruppen-Flussprotokollen](../network-watcher/network-watcher-nsg-flow-logging-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) zum Analysieren des Netzwerkdatenverkehrs zu und von Ressourcen, denen eine Netzwerksicherheitsgruppe zugeordnet ist.