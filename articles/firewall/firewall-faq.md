---
title: Azure Firewall – Häufig gestellte Fragen
description: Häufig gestellte Fragen zu Azure Firewall
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 5/3/2019
ms.author: victorh
ms.openlocfilehash: 84b42654ec472ea2c7c81bed545f56b647158c95
ms.sourcegitcommit: db3fe303b251c92e94072b160e546cec15361c2c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/22/2019
ms.locfileid: "66016024"
---
# <a name="azure-firewall-faq"></a>Azure Firewall – Häufig gestellte Fragen

## <a name="what-is-azure-firewall"></a>Was ist Azure Firewall?

Azure Firewall ist ein verwalteter, cloudbasierter Netzwerksicherheitsdienst, der Ihre Azure Virtual Network-Ressourcen schützt. Es ist eine vollständig zustandsbehaftete Firewall als ein Dienst mit integrierter Hochverfügbarkeit und uneingeschränkter Cloudskalierbarkeit. Sie können Richtlinien zur Anwendungs- und Netzwerkkonnektivität übergreifend für Abonnements und virtuelle Netzwerke zentral erstellen, erzwingen und protokollieren.

## <a name="what-capabilities-are-supported-in-azure-firewall"></a>Welche Funktionen werden in Azure Firewall unterstützt?

* Zustandsbehaftete Firewall als Dienst
* Integrierte Hochverfügbarkeit mit uneingeschränkter Cloudskalierbarkeit
* FQDN-Filterung
* FQDN-Tags
* Filterregeln für den Netzwerkdatenverkehr
* SNAT-Unterstützung für ausgehenden Datenverkehr
* DNAT-Unterstützung für eingehenden Datenverkehr
* Zentrale Erstellung, Erzwingung und Protokollierung der Richtlinien zur Anwendungs- und Netzwerkkonnektivität übergreifend für Abonnements und VNETs
* Vollständige Integration in Azure Monitor für Protokollierung und Analysen

## <a name="what-is-the-typical-deployment-model-for-azure-firewall"></a>Was ist das typische Bereitstellungsmodell für Azure Firewall?

Sie können Azure Firewall in einem virtuellen Netzwerk bereitstellen, Kunden stellen Azure Firewall jedoch in der Regel in einem zentralen virtuellen Netzwerk bereit und verbinden andere virtuelle Netzwerke in einem Hub-and-Spoke-Modell damit. Sie können die Standardroute von den verbundenen virtuellen Netzwerken anschließend so festlegen, dass sie auf dieses zentrale virtuelle Firewall-Netzwerk verweist. Globales VNet-Peering wird unterstützt, wird aber aufgrund potenzieller Leistungs- und Latenzprobleme in den Regionen nicht empfohlen. Für optimale Leistungen stellen Sie eine Firewall pro Region bereit.

Der Vorteil dieses Modells ist die Möglichkeit, die Kontrolle über mehrere Spoke-VNETs über verschiedene Abonnements hinweg zentral auszuüben. Es ergeben sich zudem Kosteneinsparungen, da Sie nicht in jedem VNet separat eine Firewall bereitstellen müssen. Die Kosteneinsparungen sollten anhand der zugeordneten Peeringkosten auf der Grundlage der Datenverkehrsmuster der Kunden gemessen werden.

## <a name="how-can-i-install-the-azure-firewall"></a>Wie kann ich Azure Firewall installieren?

Sie können Azure Firewall über das Azure-Portal, PowerShell, die REST-API oder Vorlagen einrichten. Eine Schritt-für-Schritt-Anleitung finden Sie im [Tutorial: Bereitstellen und Konfigurieren von Azure Firewall über das Azure-Portal](tutorial-firewall-deploy-portal.md).

## <a name="what-are-some-azure-firewall-concepts"></a>Wie lauten einige der Azure Firewall-Konzepte?

Azure Firewall unterstützt-Regeln und Regelsammlungen. Eine Regelsammlung ist eine Liste von Regeln mit gleicher Reihenfolge und Priorität. Regelsammlungen werden in der Reihenfolge ihrer Priorität ausgeführt. Netzwerkregelsammlungen haben höhere Priorität als Anwendungsregelsammlungen, und alle Regeln werden beendet.

Es gibt drei Arten von Regelsammlungen:

* *Anwendungsregeln*: Konfigurieren vollqualifizierter Domänennamen (Fully Qualified Domain Names, FQDNs), auf die von einem Subnetz aus zugegriffen werden kann.
* *Netzwerkregeln*: Konfigurieren von Regeln mit Quelladressen, Protokollen, Zielports und Zieladressen.
* *NAT-Regeln*: Konfigurieren von DNAT-Regeln, um eingehende Verbindungen zuzulassen.

## <a name="does-azure-firewall-support-inbound-traffic-filtering"></a>Unterstützt Azure Firewall das Filtern des eingehenden Datenverkehrs?

Azure Firewall unterstützt Filter für eingehenden und ausgehenden Datenverkehr. Der Schutz des eingehenden Datenverkehrs ist für Nicht-HTTP/S-Protokolle bestimmt, z. B. RDP-, SSH- und FTP-Protokolle.

## <a name="which-logging-and-analytics-services-are-supported-by-the-azure-firewall"></a>Welche Protokollierungs- und Analysedienste werden von Azure Firewall unterstützt?

Azure Firewall ist zum Anzeigen und Analysieren von Firewallprotokollen in Azure Monitor integriert. Protokolle können an Log Analytics, Azure Storage oder Event Hubs gesendet werden. Sie können in Log Analytics oder von anderen Tools, wie Excel und Power BI, analysiert werden. Weitere Informationen finden Sie unter [Tutorial: Überwachen von Azure Firewall-Protokollen](tutorial-diagnostics.md).

## <a name="how-does-azure-firewall-work-differently-from-existing-services-such-as-nvas-in-the-marketplace"></a>Wie unterscheidet sich die Funktionsweise von Azure Firewall von vorhandenen Diensten wie NVAs im Marketplace?

Azure Firewall ist ein grundlegender Firewalldienst für verschiedene Kundenszenarios. Es wird erwartet, dass Sie über eine Mischung aus Drittanbieter-NVAs und Azure Firewall verfügen. Eine bessere Zusammenarbeit hat höchste Priorität.

## <a name="what-is-the-difference-between-application-gateway-waf-and-azure-firewall"></a>Was ist der Unterschied zwischen Application Gateway-WAF und Azure Firewall?

Die Web Application Firewall (WAF) ist ein Feature von Application Gateway, das zentralisierten Schutz des eingehenden Datenverkehrs für Ihre Webanwendungen vor allgemeinen Exploits und Sicherheitsrisiken bietet. Azure Firewall bietet Schutz für eingehenden Datenverkehr für Nicht-HTTP/S-Protokolle (z.B. RDP, SSH, FTP), Schutz auf Netzwerkebene für eingehenden Datenverkehr für alle Ports und Protokolle sowie Schutz auf Anwendungsebene für ausgehenden HTTP/S-Datenverkehr.

## <a name="what-is-the-difference-between-network-security-groups-nsgs-and-azure-firewall"></a>Was ist der Unterschied zwischen Netzwerksicherheitsgruppen (Network Security Groups, NSGs) und Azure Firewall?

Der Azure Firewall-Dienst ergänzt die Funktionen der Netzwerksicherheitsgruppen. Zusammen bieten sie eine bessere „Defense-in-Depth“-Netzwerksicherheit. Netzwerksicherheitsgruppen bieten verteilte Datenverkehrsfilterung auf Netzwerkebene, um den Datenverkehr auf Ressourcen in virtuellen Netzwerken in jedem Abonnement zu beschränken. Azure Firewall ist eine vollständig zustandsbehaftete, zentrale Netzwerkfirewall als Dienst, die Schutz auf Netzwerk- und Anwendungsebene für verschiedene Abonnements und virtuelle Netzwerke bietet.

## <a name="how-do-i-set-up-azure-firewall-with-my-service-endpoints"></a>Wie richte ich Azure Firewall für meine Dienstendpunkte ein?

Für den sicheren Zugriff auf PaaS-Dienste werden Dienstendpunkte empfohlen. Sie können Dienstendpunkte im Azure Firewall-Subnetz aktivieren und diese im verbundenen virtuellen Spoke-Netzwerk deaktivieren. Auf diese Weise profitieren Sie von beiden Features: von der Dienstendpunktsicherheit und der zentralen Protokollierung des gesamten Datenverkehrs.

## <a name="what-is-the-pricing-for-azure-firewall"></a>Wie sieht das Preismodell für Azure Firewall aus?

Informationen hierzu finden Sie unter [Azure Firewall – Preise](https://azure.microsoft.com/pricing/details/azure-firewall/).

## <a name="how-can-i-stop-and-start-azure-firewall"></a>Wie kann ich Azure Firewall beenden und starten?

Sie können Azure PowerShell verwenden, um Methoden zuzuordnen und die Zuordnung dafür aufzuheben (*Zuordnen* und *Zuordnung aufheben*).

Beispiel: 

```azurepowershell
# Stop an existing firewall

$azfw = Get-AzFirewall -Name "FW Name" -ResourceGroupName "RG Name"
$azfw.Deallocate()
Set-AzFirewall -AzureFirewall $azfw
```

```azurepowershell
# Start a firewall

$azfw = Get-AzFirewall -Name "FW Name" -ResourceGroupName "RG Name"
$vnet = Get-AzVirtualNetwork -ResourceGroupName "RG Name" -Name "VNet Name"
$publicip = Get-AzPublicIpAddress -Name "Public IP Name" -ResourceGroupName " RG Name"
$azfw.Allocate($vnet,$publicip)
Set-AzFirewall -AzureFirewall $azfw
```

> [!NOTE]
> Sie müssen der ursprünglichen Ressourcengruppe und dem Abonnement eine Firewall und eine öffentliche IP-Adresse neu zuordnen.

## <a name="what-are-the-known-service-limits"></a>Welche Dienstgrenzwerte sind bekannt?

Die Einschränkungen des Azure Firewall-Diensts finden Sie unter [Einschränkungen für Azure-Abonnements und Dienste, Kontingente und Einschränkungen](../azure-subscription-service-limits.md#azure-firewall-limits).

## <a name="can-azure-firewall-in-a-hub-virtual-network-forward-and-filter-network-traffic-between-two-spoke-virtual-networks"></a>Kann Azure Firewall in einem virtuellen Hubnetzwerk den Netzwerkdatenverkehr zwischen zwei virtuellen Spoke-Netzwerken weiterleiten und filtern?

Ja, Sie können Azure Firewall in einem virtuellen Hubnetzwerk verwenden, um den Datenverkehr zwischen zwei virtuellen Spoke-Netzwerken weiterzuleiten und zu filtern. Subnetze in jedem der virtuellen Spoke-Netzwerke müssen über eine UDR verfügen, die auf die Azure Firewall als Standardgateway verweist, damit dieses Szenario ordnungsgemäß funktioniert.

## <a name="can-azure-firewall-forward-and-filter-network-traffic-between-subnets-in-the-same-virtual-network-or-peered-virtual-networks"></a>Kann Azure Firewall den Netzwerkdatenverkehr zwischen Subnetzen im selben virtuellen Netzwerk oder über mittels Peering verknüpfte virtuelle Netzwerke weiterleiten und filtern?

Ja. Die Konfiguration der UDRs zur Umleitung des Datenverkehrs zwischen Subnetzen im gleichen VNET erfordert jedoch zusätzliche Anstrengungen. Während die Verwendung des VNET-Adressbereichs als Zielpräfix für den UDR ausreicht, wird auch der gesamte Datenverkehr von einem Computer zu einem anderen Computer im gleichen Subnetz über die Azure Firewall-Instanz geleitet. Um dies zu vermeiden, fügen Sie eine Route für das Subnetz in den UDR mit dem Typ **VNET** für den nächsten Hop ein. Das Verwalten dieser Routen kann umständlich und fehleranfällig sein. Die empfohlene Methode für die interne Netzwerksegmentierung ist die Verwendung von Netzwerksicherheitsgruppen, die keine UDRs erfordern.

## <a name="is-forced-tunnelingchaining-to-a-network-virtual-appliance-supported"></a>Wird erzwungenes Tunneling bzw. die erzwungene Verkettung mit einem virtuellen Netzwerkgerät unterstützt?

Die Tunnelerzwingung wird standardmäßig nicht unterstützt, sie kann jedoch mit der Unterstützung vom Support aktiviert werden.

Azure Firewall muss über eine direkte Internetverbindung verfügen. Wenn Ihr Subnetz „AzureFirewallSubnet“ eine Standardroute zu Ihrem lokalen Netzwerk über BGP erfasst, müssen Sie diese mit der benutzerdefinierten Route 0.0.0.0/0 überschreiben. Legen Sie dabei den Wert **NextHopType** auf **Internet** fest, um die direkte Internetkonnektivität beizubehalten. Standardmäßig unterstützt Azure Firewall keine Tunnelerzwingung für ein lokales Netzwerk.

Wenn Ihre Konfiguration jedoch die Tunnelerzwingung für ein lokales Netzwerk erfordert, wird Microsoft dies im Einzelfall unterstützen. Wenden Sie sich in diesem Fall an den Support, damit Ihr Fall überprüft werden kann. Bei einer Annahme wird Ihr Abonnement in die Whitelist aufgenommen, damit die erforderliche Internetkonnektivität der Firewall auch sicher erhalten bleibt.

## <a name="are-there-any-firewall-resource-group-restrictions"></a>Gibt es Einschränkungen bei Ressourcengruppen?

Ja. Firewall, Subnetz, VNET und die öffentliche IP-Adresse müssen sich in der gleichen Ressourcengruppe befinden.

## <a name="when-configuring-dnat-for-inbound-network-traffic-do-i-also-need-to-configure-a-corresponding-network-rule-to-allow-that-traffic"></a>Muss ich bei der Konfiguration von DNAT für den eingehenden Netzwerkverkehr auch eine entsprechende Netzwerkregel konfigurieren, um diesen Verkehr zu ermöglichen?

Nein. Mit NAT-Regeln wird implizit eine entsprechende Netzwerkregel hinzugefügt, um den übersetzten Datenverkehr zuzulassen. Sie können dieses Verhalten außer Kraft setzen, indem Sie explizit eine Netzwerkregelsammlung mit Ablehnungsregeln hinzufügen, die für den übersetzten Datenverkehr geeignet sind. Weitere Informationen zur Logik für die Azure Firewall-Regelverarbeitung finden Sie unter [Logik für die Azure Firewall-Regelverarbeitung](rule-processing.md).

## <a name="how-do-wildcards-work-in-an-application-rule-target-fqdn"></a>Wie funktionieren Platzhalter in einem FQDN eines Anwendungsregelziels?

Wenn Sie ***.contoso.com** konfigurieren, ist *anyvalue*.contoso.com, aber nicht „contoso.com“ (Domäne an der Spitze der Zone) zulässig. Wenn Sie die Domäne an der Spitze der Zone zulassen möchten, müssen Sie sie explizit als Ziel-FQDN konfigurieren.

## <a name="what-does-provisioning-state-failed-mean"></a>Was bedeutet *Bereitstellungsstatus: Fehler*?

Wenn eine Konfigurationsänderung angewendet wird, versucht Azure Firewall alle zugrunde liegenden Back-End-Instanzen zu aktualisieren. In seltenen Fällen kann bei der Aktualisierung einer dieser Back-End-Instanzen mit der neuen Konfiguration ein Fehler auftreten, und der Aktualisierungsvorgang wird mit dem Bereitstellungsstatus „Fehler“ angehalten. Azure Firewall ist noch funktionsfähig, die angewendete Konfiguration befindet sich jedoch unter Umständen in einem inkonsistenten Zustand, in dem einige Instanzen die vorherige Konfiguration und andere den aktualisierten Regelsatz verwenden. Versuchen Sie in diesem Fall, die Konfiguration noch einmal zu aktualisieren, bis der Vorgang erfolgreich war und für Ihre Firewall der Bereitstellungsstatus *Erfolgreich* angezeigt wird.
