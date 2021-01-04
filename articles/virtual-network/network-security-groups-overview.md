---
title: Übersicht über Azure-Netzwerksicherheitsgruppen
titlesuffix: Azure Virtual Network
description: Erfahren Sie mehr über Netzwerksicherheitsgruppen. Mithilfe von Netzwerksicherheitsgruppen können Sie den Netzwerkdatenverkehr zwischen Azure-Ressourcen filtern.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: kumud
ms.reviewer: kumud
ms.custom: contperf-fy21q1
ms.openlocfilehash: 4e23c6f25145724a5300c9e5cdcb55431fb0b4f2
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97028766"
---
# <a name="network-security-groups"></a>Netzwerksicherheitsgruppen
<a name="network-security-groups"></a>

Sie können eine Azure-Netzwerksicherheitsgruppe verwenden, um Netzwerkdatenverkehr von und zu Azure-Ressourcen in einem virtuellen Azure-Netzwerk zu filtern. Eine Netzwerksicherheitsgruppe enthält [Sicherheitsregeln](#security-rules), die eingehenden Netzwerkdatenverkehr an verschiedene Typen von Azure-Ressourcen oder ausgehenden Netzwerkdatenverkehr von diesen zulassen oder verweigern. Für jede Regel können Sie die Quelle, das Ziel, den Port und das Protokoll angeben.

In diesem Artikel werden Eigenschaften einer Netzwerksicherheitsgruppen-Regel, die angewendeten [Standardsicherheitsregeln](#default-security-rules) und die Regeleigenschaften beschrieben, die Sie ändern können, um eine [ergänzte Sicherheitsregel](#augmented-security-rules) zu erstellen.

## <a name="security-rules"></a><a name="security-rules"></a> Sicherheitsregeln

Eine Netzwerksicherheitsgruppe kann – innerhalb der [Grenzwerte](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) des Azure-Abonnements – null oder mehr Regeln enthalten. Für jede Regel werden die folgenden Eigenschaften angegeben:

|Eigenschaft  |Erklärung  |
|---------|---------|
|Name|Ein eindeutiger Name in der Netzwerksicherheitsgruppe.|
|Priority | Eine Zahl zwischen 100 und 4.096. Regeln werden in der Reihenfolge ihrer Priorität verarbeitet. Regeln mit niedrigeren Zahlen werden vor Regeln mit höheren Zahlen verarbeitet, weil die Priorität für niedrigere Zahlen höher ist. Nachdem sich für den Datenverkehr eine Übereinstimmung mit einer Regel ergibt, wird die Verarbeitung angehalten. Daher werden alle Regeln mit niedrigerer Priorität (höherer Zahl), die über die gleichen Attribute wie Regeln mit höheren Prioritäten verfügen, nicht verarbeitet.|
|Quelle oder Ziel| Beliebiges Element oder eine einzelne IP-Adresse, ein CIDR-Block (klassenloses domänenübergreifendes Routing, z. B. 10.0.0.0/24), ein Diensttag oder eine Anwendungssicherheitsgruppe. Wenn Sie eine Adresse für eine Azure-Ressource angeben, geben Sie die private IP-Adresse an, die der Ressource zugewiesen ist. Netzwerksicherheitsgruppen werden verarbeitet, nachdem Azure eine öffentliche IP-Adresse in eine private IP-Adresse für eingehenden Datenverkehr übersetzt hat und bevor Azure eine private IP-Adresse in eine öffentliche IP-Adresse für ausgehenden Datenverkehr übersetzt. . Durch das Angeben eines Bereichs, eines Diensttags oder einer Anwendungssicherheitsgruppe haben Sie die Möglichkeit, weniger Sicherheitsregeln zu erstellen. Die Option zum Angeben mehrerer einzelner IP-Adressen und Bereiche (die Angabe mehrerer Diensttags oder Anwendungsgruppen ist nicht zulässig) in einer Regel wird als [Ergänzte Sicherheitsregeln](#augmented-security-rules) bezeichnet. Ergänzte Sicherheitsregeln können nur in Netzwerksicherheitsgruppen erstellt werden, die mit dem Resource Manager-Bereitstellungsmodell erstellt wurden. Es ist nicht möglich, mehrere IP-Adressen und IP-Adressbereiche in Netzwerksicherheitsgruppen anzugeben, die mit dem klassischen Bereitstellungsmodell erstellt wurden.|
|Protocol     | „TCP“, „UDP“, „ICMP“ oder „Beliebig“.|
|Direction| Gibt an, ob die Regel für ein- oder ausgehenden Datenverkehr gilt.|
|Portbereich     |Sie können einen einzelnen Port oder einen Bereich mit Ports angeben. Mögliche Angaben sind beispielsweise „80“ oder „10.000 - 10.005“. Das Angeben von Bereichen ermöglicht Ihnen die Erstellung von weniger Sicherheitsregeln. Ergänzte Sicherheitsregeln können nur in Netzwerksicherheitsgruppen erstellt werden, die mit dem Resource Manager-Bereitstellungsmodell erstellt wurden. In Netzwerksicherheitsgruppen, die mit dem klassischen Bereitstellungsmodell erstellt wurden, können Sie in derselben Sicherheitsregel nicht mehrere Ports oder Portbereiche angeben.   |
|Aktion     | Zulassen oder Verweigern        |

Netzwerksicherheitsgruppen-Sicherheitsregeln werden nach Priorität anhand der 5-Tupel-Informationen (Quelle, Quellport, Ziel, Zielport und Protokoll) ausgewertet, um den Datenverkehr zuzulassen oder zu verweigern. Sie können keine zwei Sicherheitsregeln mit gleicher Priorität und Richtung erstellen. Für vorhandene Verbindungen wird ein Flussdatensatz erstellt. Die Kommunikation wird basierend auf dem Verbindungszustand der Flussdatensätze zugelassen oder verweigert. Der Flussdatensatz ermöglicht es, dass eine Netzwerksicherheitsgruppe zustandsbehaftet ist. Wenn Sie beispielsweise eine Sicherheitsregel für Datenverkehr in ausgehender Richtung an eine beliebige Adresse über Port 80 angeben, ist es nicht erforderlich, für die Antwort auf den ausgehenden Datenverkehr eine Sicherheitsregel für Datenverkehr in eingehender Richtung anzugeben. Sie müssen nur dann eine Sicherheitsregel für Datenverkehr in eingehender Richtung angeben, wenn die Kommunikation extern initiiert wird. Dies gilt auch für den umgekehrten Fall. Wenn eingehender Datenverkehr über einen Port zugelassen wird, ist es nicht erforderlich, für die Beantwortung des Datenverkehrs über den Port eine Sicherheitsregel für Datenverkehr in ausgehender Richtung anzugeben.

Vorhandene Verbindungen können nicht unterbrochen werden, wenn Sie eine Sicherheitsregel entfernen, die den Datenfluss ermöglicht hat. Datenverkehrsflüsse werden unterbrochen, wenn die Verbindungen beendet wurden und in beide Richtungen mindestens einige Minuten lang kein Datenverkehr besteht.

Es gibt Beschränkungen für die Anzahl von Sicherheitsregeln, die Sie in einer Netzwerksicherheitsgruppe erstellen können. Ausführliche Informationen finden Sie im Artikel zu den [Einschränkungen für Azure-Abonnements](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

### <a name="default-security-rules"></a><a name="default-security-rules"></a> Standardsicherheitsregeln

Azure erstellt in jeder von Ihnen erstellten Netzwerksicherheitsgruppe die folgenden Standardregeln:

#### <a name="inbound"></a>Eingehend

##### <a name="allowvnetinbound"></a>AllowVNetInBound

|Priority|`Source`|Quellports|Destination|Zielports|Protocol|Zugriff|
|---|---|---|---|---|---|---|
|65000|VirtualNetwork|0 - 65535|VirtualNetwork|0 - 65535|Any|Allow|

##### <a name="allowazureloadbalancerinbound"></a>AllowAzureLoadBalancerInBound

|Priority|`Source`|Quellports|Destination|Zielports|Protocol|Zugriff|
|---|---|---|---|---|---|---|
|65001|AzureLoadBalancer|0 - 65535|0.0.0.0/0|0 - 65535|Any|Allow|

##### <a name="denyallinbound"></a>DenyAllInbound

|Priority|`Source`|Quellports|Destination|Zielports|Protocol|Zugriff|
|---|---|---|---|---|---|---|
|65500|0.0.0.0/0|0 - 65535|0.0.0.0/0|0 - 65535|Any|Verweigern|

#### <a name="outbound"></a>Ausgehend

##### <a name="allowvnetoutbound"></a>AllowVnetOutBound

|Priority|`Source`|Quellports| Destination | Zielports | Protocol | Zugriff |
|---|---|---|---|---|---|---|
| 65000 | VirtualNetwork | 0 - 65535 | VirtualNetwork | 0 - 65535 | Any | Allow |

##### <a name="allowinternetoutbound"></a>AllowInternetOutBound

|Priority|`Source`|Quellports| Destination | Zielports | Protocol | Zugriff |
|---|---|---|---|---|---|---|
| 65001 | 0.0.0.0/0 | 0 - 65535 | Internet | 0 - 65535 | Any | Allow |

##### <a name="denyalloutbound"></a>DenyAllOutBound

|Priority|`Source`|Quellports| Destination | Zielports | Protocol | Zugriff |
|---|---|---|---|---|---|---|
| 65500 | 0.0.0.0/0 | 0 - 65535 | 0.0.0.0/0 | 0 - 65535 | Any | Verweigern |

In den Spalten **Quelle** und **Ziel** handelt es sich bei *VirtualNetwork*, *AzureLoadBalancer* und *Internet* um [Diensttags](service-tags-overview.md) und nicht um IP-Adressen. In der Protokollspalte steht **Beliebig** für TCP, UDP und ICMP. Beim Erstellen einer Regel können Sie „TCP“, „UDP“, „ICMP“ oder „Beliebig“ angeben. *0.0.0.0/0* in den Spalten **Quelle** und **Ziel** steht für alle Adressen. Clients wie Azure-Portal, Azure-Befehlszeilenschnittstelle oder PowerShell können „*“ oder „any“ für diesen Ausdruck verwenden.
 
Sie können die Standardregeln nicht entfernen, aber Sie können sie außer Kraft setzen, indem Sie Regeln mit höheren Prioritäten erstellen.

### <a name="augmented-security-rules"></a><a name="augmented-security-rules"></a> Ergänzte Sicherheitsregeln

Mit ergänzten Sicherheitsregeln wird die Sicherheitsdefinition für virtuelle Netzwerke vereinfacht, da Sie umfangreichere und komplexe Netzwerksicherheitsregeln mit weniger Regeln definieren können. Sie können mehrere Ports und mehrere explizite IP-Adressen und Bereiche zu einer einzelnen Sicherheitsregel zusammenfassen, die leicht verständlich ist. Verwenden Sie ergänzte Regeln in den Feldern für die Quelle, das Ziel und den Port einer Regel. Kombinieren Sie ergänzte Sicherheitsregeln mit [Diensttags](service-tags-overview.md) oder [Anwendungssicherheitsgruppen](#application-security-groups), um die Wartung Ihrer Sicherheitsregeldefinition zu vereinfachen. Es gibt Beschränkungen für die Anzahl von Adressen, Bereichen und Ports, die Sie in einer Regel angeben können. Ausführliche Informationen finden Sie im Artikel zu den [Einschränkungen für Azure-Abonnements](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

#### <a name="service-tags"></a>Diensttags

Ein Diensttag steht für eine Gruppe von IP-Adresspräfixen eines bestimmten Azure-Diensts. Mit Diensttags kann die Komplexität häufiger Aktualisierungen von Netzwerksicherheitsregeln verringert werden.

Weitere Informationen finden Sie unter [Azure-Diensttags](service-tags-overview.md). Ein Beispiel für die Verwendung des Speicherdiensttags, um den Netzwerkzugriff einzuschränken, finden Sie unter [Einschränken des Netzwerkzugriffs auf PaaS-Ressourcen](tutorial-restrict-network-access-to-resources.md).

#### <a name="application-security-groups"></a>Anwendungssicherheitsgruppen

Mit Anwendungssicherheitsgruppen können Sie die Netzwerksicherheit als natürliche Erweiterung einer Anwendungsstruktur konfigurieren und virtuelle Computer gruppieren sowie auf der Grundlage dieser Gruppen Netzwerksicherheitsrichtlinien definieren. Sie können Ihre Sicherheitsrichtlinie nach Bedarf wiederverwenden, ohne dass Sie explizite IP-Adressen manuell warten müssen. Weitere Informationen finden Sie unter [Anwendungssicherheitsgruppen](application-security-groups.md).

## <a name="azure-platform-considerations"></a>Aspekte der Azure Platform

- **Virtuelle IP des Hostknotens**: Grundlegende Infrastrukturdienste wie DHCP, DNS, IMDS und die Systemüberwachung werden über die virtualisierten Host-IP-Adressen 168.63.129.16 und 169.254.169.254 bereitgestellt. Diese IP-Adressen gehören Microsoft und sind die einzigen virtuellen IP-Adressen, die in allen Regionen zu diesem Zweck verwendet werden. Effektive Sicherheitsregeln und effektive Routen berücksichtigen diese Plattformregeln nicht. Zum Überschreiben dieser grundlegenden Infrastrukturkommunikation können Sie eine Sicherheitsregel erstellen, um Datenverkehr mithilfe der folgenden [Diensttags](service-tags-overview.md) abzulehnen, die in den Regeln Ihrer Netzwerksicherheitsgruppen verwendet werden: AzurePlatformDNS, AzurePlatformIMDS und AzurePlatformLKM. Weitere Informationen zum [Diagnostizieren von Problemen mit der Netzwerkfilterung](diagnose-network-traffic-filter-problem.md) und zum [Diagnostizieren von Problemen mit dem Netzwerkrouting](diagnose-network-routing-problem.md)
- **Lizenzierung (Key Management Service)** : Die auf VMs ausgeführten Windows-Images müssen lizenziert werden. Zum Sicherstellen der Lizenzierung wird eine entsprechende Anforderung an die Hostserver des Schlüsselverwaltungsdiensts gesendet, die solche Abfragen verarbeiten. Die Anforderung wird in ausgehender Richtung über Port 1688 gesendet. Für Bereitstellungen mit einer Konfiguration der [Standardroute 0.0.0.0/0](virtual-networks-udr-overview.md#default-route) wird diese Plattformregel deaktiviert.
- **VMs in Pools mit Lastenausgleich**: Der angewendete Quellport und -adressbereich stammen vom Ausgangscomputer, nicht vom Lastenausgleich. Der Zielport und -adressbereich sind für den Zielcomputer bestimmt, nicht für den Lastenausgleich.
- **Azure-Dienstinstanzen**: Instanzen mehrerer Azure-Dienste (z. B. HDInsight, App Service-Umgebungen und Virtual Machine Scale Sets) werden in Subnetzen des virtuellen Netzwerks bereitgestellt. Eine vollständige Liste mit den Diensten, die Sie in virtuellen Netzwerken bereitstellen können, finden Sie unter [Virtuelles Netzwerk für Azure-Dienste](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network). Machen Sie sich auf jeden Fall mit den Portanforderungen für die einzelnen Dienste vertraut, bevor Sie eine Netzwerksicherheitsgruppe auf das Subnetz anwenden, in dem die Ressource bereitgestellt wurde. Wenn Sie den Datenverkehr für Ports verweigern, die für den Dienst benötigt werden, funktioniert der Dienst nicht richtig.
- **Senden von E-Mails in ausgehender Richtung**: Microsoft empfiehlt die Nutzung von authentifizierten SMTP-Relaydiensten (normalerweise über TCP-Port 587 verbunden, aber auch über andere Ports), um E-Mails von Azure Virtual Machines zu senden. SMTP-Relaydienste sind auf Absenderzuverlässigkeit ausgelegt, um die Wahrscheinlichkeit zu verringern, dass Nachrichten von E-Mail-Drittanbietern abgelehnt werden. Zu diesen SMTP-Relaydiensten gehören u.a. auch Exchange Online Protection und SendGrid. Die Nutzung von SMTP-Relaydiensten ist in Azure unabhängig von Ihrem Abonnementtyp auf keinerlei Weise eingeschränkt. 

  Wenn Sie Ihr Azure-Abonnement vor dem 15. November 2017 erstellt haben, können Sie nicht nur die SMTP-Relaydienste nutzen, sondern auch E-Mails direkt über TCP-Port 25 senden. Falls Sie Ihr Abonnement nach dem 15. November 2017 erstellt haben, ist es unter Umständen nicht möglich, E-Mails direkt über Port 25 zu senden. Das Verhalten der ausgehenden Kommunikation über Port 25 hängt wie folgt vom Typ Ihres Abonnements ab:

     - **Enterprise Agreement**: Die Kommunikation in ausgehender Richtung über Port 25 ist zulässig. Sie können ausgehende E-Mails direkt von virtuellen Computern an externe E-Mail-Anbieter senden, ohne dass Einschränkungen der Azure Platform bestehen. 
     - **Nutzungsbasierte Bezahlung:** Die Kommunikation in ausgehender Richtung über Port 25 wird für alle Ressourcen blockiert. Wenn Sie E-Mails von einem virtuellen Computer direkt an externe E-Mail-Anbieter senden müssen (ohne authentifiziertes SMTP-Relay), können Sie die Aufhebung der Einschränkung anfordern. Anfragen dieser Art werden von Microsoft geprüft und erst nach Durchführung von Betrugsprüfungen genehmigt. Erstellen Sie hierzu eine Supportanfrage mit einem Problemtyp der Art *Technisch*, *Konnektivität virtueller Netzwerke*, *E-Mail senden nicht möglich (SMTP/Port 25)* . Fügen Sie in die Supportanfrage Details dazu ein, warum für Ihr Abonnement das direkte Senden von E-Mails an E-Mail-Anbieter erforderlich ist, anstatt ein authentifiziertes SMTP-Relay zu verwenden. Wenn für Ihr Abonnement eine Ausnahmeregelung gewährt wird, können nur virtuelle Computer, die nach dem Startdatum der Ausnahmeregelung erstellt wurden, in ausgehender Richtung über Port 25 kommunizieren.
     - **MSDN, Azure Pass, Azure in Open, Education, BizSpark und kostenlose Testversion**: Die Kommunikation in ausgehender Richtung über Port 25 wird für alle Ressourcen blockiert. Es können keine Anfragen zur Beseitigung der Einschränkungen gesendet werden, da keine Ausnahmen gewährt werden. Wenn Sie über Ihren virtuellen Computer E-Mails senden müssen, müssen Sie einen SMTP-Relaydienst verwenden.
     - **Cloud-Dienstanbieter**: Kunden, die Azure-Ressourcen über einen Cloud-Dienstanbieter nutzen, können bei ihrem Cloud-Dienstanbieter eine Supportanfrage erstellen und anfordern, dass der Anbieter in ihrem Auftrag eine Anfrage zum Aufheben der Blockierung stellt, wenn ein sicheres SMTP-Relay nicht verwendet werden kann.

  Wenn für Sie in Azure das Senden von E-Mails über Port 25 zugelassen wird, kann von Microsoft nicht garantiert werden, dass E-Mail-Anbieter eingehende E-Mails von Ihrem virtuellen Computer akzeptieren. Falls ein bestimmter Anbieter E-Mails von Ihrem virtuellen Computer ablehnt, müssen Sie sich direkt an den Anbieter wenden, um Probleme mit der Nachrichtenzustellung oder Spamfilterung zu beheben, oder einen authentifizierten SMTP-Relaydienst verwenden.

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zu den Azure-Ressourcen, die in einem virtuellen Netzwerk bereitgestellt werden können und denen Netzwerksicherheitsgruppen zugeordnet sind, finden Sie unter [Integration virtueller Netzwerke für Azure-Dienste](virtual-network-for-azure-services.md).
* Informationen zum Auswerten des Datenverkehrs mit Netzwerksicherheitsgruppen finden Sie unter [Filtern von Netzwerkdatenverkehr mit Netzwerksicherheitsgruppen](network-security-group-how-it-works.md).
* Wenn Sie noch nie eine Netzwerksicherheitsgruppe erstellt haben, gehen Sie dieses kurze [Tutorial](tutorial-filter-network-traffic.md) durch, um sich mit dem Erstellen von Netzwerksicherheitsgruppen vertraut zu machen.
* Wenn Sie sich mit Netzwerksicherheitsgruppen auskennen und diese verwalten müssen, finden Sie unter [Erstellen, Ändern oder Löschen einer Netzwerksicherheitsgruppe](manage-network-security-group.md) weitere Informationen. 
* Wenn Kommunikationsschwierigkeiten auftreten und Sie Probleme mit Netzwerksicherheitsgruppen beheben müssen, finden Sie unter [Diagnostizieren von Problemen mit dem Filter für Netzwerkdatenverkehr eines virtuellen Computers](diagnose-network-traffic-filter-problem.md) weitere Informationen. 
* Informieren Sie sich über die Aktivierung von [Netzwerksicherheitsgruppen-Flussprotokollen](../network-watcher/network-watcher-nsg-flow-logging-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) zum Analysieren des Netzwerkdatenverkehrs zu und von Ressourcen, denen eine Netzwerksicherheitsgruppe zugeordnet ist.
