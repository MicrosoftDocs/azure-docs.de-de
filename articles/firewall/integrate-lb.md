---
title: Integrieren von Azure Firewall mit Azure Load Balancer Standard
description: Sie können eine Azure Firewall mit einem Azure Load Balancer Standard (öffentlich oder intern) in ein virtuelles Netzwerk integrieren.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 09/25/2020
ms.author: victorh
ms.openlocfilehash: 3b8fbc47b46f8be6e4ad7636a1d7552445501f0f
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94653163"
---
# <a name="integrate-azure-firewall-with-azure-standard-load-balancer"></a>Integrieren von Azure Firewall mit Azure Load Balancer Standard

Sie können eine Azure Firewall mit einem Azure Load Balancer Standard (öffentlich oder intern) in ein virtuelles Netzwerk integrieren. 

Der bevorzugte Entwurf ist die Integration eines internen Load Balancers in Ihre Azure Firewall, da dies ein viel einfacheres Design ist. Sie können einen öffentlichen Load Balancer verwenden, wenn Sie bereits einen bereitgestellt haben und diesen beibehalten möchten. Sie müssen jedoch wissen, dass es ein Problem mit asymmetrischem Routing geben kann, das die Funktionalität mit dem Szenario eines öffentlichen Load Balancers (Lastenausgleich) beeinträchtigen kann.

Weitere Informationen zu Azure Load Balancer finden Sie unter [Was versteht man unter Azure Load Balancer?](../load-balancer/load-balancer-overview.md).

## <a name="public-load-balancer"></a>Öffentlicher Load Balancer

Unter einem öffentlichen Load Balancer versteht man einen Load Balancer, der mit einer öffentlichen Front-End-IP-Adresse bereitgestellt wird.

### <a name="asymmetric-routing"></a>Asymmetrisches Routing

Asymmetrisches Routing liegt vor, wenn ein Paket auf einem Pfad zum Ziel gesendet und auf einem anderen Pfad zur Quelle zurückgesendet wird. Dieses Problem tritt auf, wenn ein Subnetz eine Standardroute hat, die zur privaten IP-Adresse der Firewall führt, und Sie einen öffentlichen Lastenausgleich (Load Balancer) verwenden. In diesem Fall wird der eingehende Load Balancer-Datenverkehr über die öffentliche IP-Adresse empfangen, während der Rückpfad über die private IP-Adresse der Firewall verläuft. Da die Firewall zustandsbehaftet ist, löscht sie das zurückgegebene Paket, weil die Firewall nichts über eine solche vorhandene Sitzung weiß.

### <a name="fix-the-routing-issue"></a>Beheben des Routingproblems

Wenn Sie eine Azure-Firewall in einem Subnetz bereitstellen, besteht ein Schritt darin, eine Standardroute für das Subnetz zu erstellen, in der Pakete über die private IP-Adresse der Firewall geleitet werden, die sich im Azure Firewall-Subnetz befindet. Weitere Informationen finden Sie im [Tutorial: Bereitstellen und Konfigurieren von Azure Firewall über das Azure-Portal](tutorial-firewall-deploy-portal.md#create-a-default-route).

Wenn Sie die Firewall in Ihr Load Balancer-Szenario einführen, sollte Ihr Internetdatenverkehr über die öffentliche IP-Adresse der Firewall eingehen. Hier wendet die Firewall ihre Firewallregeln an und sendet die Pakete per Netzwerkadressenübersetzung an die öffentliche IP-Adresse Ihres Load Balancers. Dies ist die Stelle, an der das Problem auftritt. Pakete treffen an der öffentlichen IP-Adresse der Firewall ein, kehren aber über die private IP-Adresse (die Standardroute wird verwendet) an die Firewall zurück.
Um dieses Problem zu vermeiden, erstellen Sie eine zusätzliche Hostroute für die öffentliche IP-Adresse der Firewall. Pakete, die an die öffentliche IP-Adresse der Firewall gesendet werden, werden über das Internet weitergeleitet. Dadurch wird vermieden, dass die Standardroute zur privaten IP-Adresse der Firewall genommen wird.

![Asymmetrisches Routing](media/integrate-lb/Firewall-LB-asymmetric.png)

### <a name="route-table-example"></a>Routingtabellenbeispiel

Beispielsweise sind die folgenden Routen für eine Firewall mit der öffentlichen IP-Adresse 20.185.97.136 und der privaten IP-Adresse 10.0.1.4 vorgesehen.

> [!div class="mx-imgBorder"]
> ![Routingtabelle](media/integrate-lb/route-table.png)

### <a name="nat-rule-example"></a>NAT-Regelbeispiel

Im folgenden Beispiel übersetzt eine NAT-Regel den RDP-Datenverkehr zur Firewall bei 20.185.97.136 zum Lastenausgleich bei 20.42.98.220:

> [!div class="mx-imgBorder"]
> ![NAT-Regel](media/integrate-lb/nat-rule-02.png)

### <a name="health-probes"></a>Integritätstests

Denken Sie daran, dass auf den Hosts im Lastenausgleichspool ein Webdienst ausgeführt werden muss, wenn Sie TCP-Integritätstests an Port 80 oder HTTP/HTTPS-Tests verwenden.

## <a name="internal-load-balancer"></a>Interner Lastenausgleich

Mit einem internen Lastenausgleich (Load Balancer) wird der Load Balancer mit einer privaten Front-End-IP-Adresse bereitgestellt.

Es gibt kein Problem mit asymmetrischem Routing in diesem Szenario. Die eingehenden Pakete treffen an der öffentlichen IP-Adresse der Firewall ein, werden in die private IP-Adresse des Load Balancers übersetzt und kehren dann über denselben Rückgabepfad an die private IP-Adresse der Firewall zurück.

Somit können Sie dieses Szenario ähnlich zum Szenario mit öffentlichem Load Balancer bereitstellen, jedoch ohne die Notwendigkeit für eine Hostroute für die öffentliche IP-Adresse der Firewall.

>[!NOTE]
>Die virtuellen Computer im Back-End-Pool verfügen mit dieser Konfiguration nicht über ausgehende Internetkonnektivität. </br> Weitere Informationen zu ausgehender Konnektivität finden Sie unter: </br> **[Ausgehende Verbindungen in Azure](../load-balancer/load-balancer-outbound-connections.md)**</br> Optionen zum Bereitstellen von Konnektivität: </br> **[Lastenausgleichskonfiguration (nur ausgehender Datenverkehr)](../load-balancer/egress-only.md)** </br> [**Was ist Virtual Network NAT?**](../virtual-network/nat-overview.md)


## <a name="additional-security"></a>Zusätzliche Sicherheit

Um die Sicherheit Ihres Szenarios mit Lastenausgleich weiter zu verbessern, können Sie Netzwerksicherheitsgruppen (NSGs) verwenden.

Beispielsweise können Sie eine NSG für das Back-End-Subnetz erstellen, in dem sich die virtuellen Computer mit Lastenausgleich befinden. Lassen Sie eingehenden Datenverkehr zu, der von der IP-Adresse und dem Port der Firewall stammt.

![Netzwerksicherheitsgruppe](media/integrate-lb/nsg-01.png)

Weitere Informationen zu Netzwerksicherheitsgruppen finden Sie unter [Sicherheitsgruppen](../virtual-network/network-security-groups-overview.md).

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie, wie Sie eine [Azure Firewall bereitstellen und konfigurieren](tutorial-firewall-deploy-portal.md).