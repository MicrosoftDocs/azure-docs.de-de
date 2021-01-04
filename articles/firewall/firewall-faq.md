---
title: Azure Firewall – Häufig gestellte Fragen
description: Häufig gestellte Fragen zu Azure Firewall Ein verwalteter, cloudbasierter Netzwerksicherheitsdienst, der Ihre Azure Virtual Network-Ressourcen schützt.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 08/13/2020
ms.author: victorh
ms.openlocfilehash: 3e6ea6692a81a06bbf3180904dfb465a88b105d1
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94653418"
---
# <a name="azure-firewall-faq"></a>Azure Firewall – Häufig gestellte Fragen

## <a name="what-is-azure-firewall"></a>Was ist Azure Firewall?

Azure Firewall ist ein verwalteter, cloudbasierter Netzwerksicherheitsdienst, der Ihre Azure Virtual Network-Ressourcen schützt. Es ist eine vollständig zustandsbehaftete Firewall als ein Dienst mit integrierter Hochverfügbarkeit und uneingeschränkter Cloudskalierbarkeit. Sie können Richtlinien zur Anwendungs- und Netzwerkkonnektivität übergreifend für Abonnements und virtuelle Netzwerke zentral erstellen, erzwingen und protokollieren.

## <a name="what-capabilities-are-supported-in-azure-firewall"></a>Welche Funktionen werden in Azure Firewall unterstützt?

Informationen zu Features von Azure Firewall finden Sie unter [Azure Firewall-Features](features.md).

## <a name="what-is-the-typical-deployment-model-for-azure-firewall"></a>Was ist das typische Bereitstellungsmodell für Azure Firewall?

Sie können Azure Firewall in einem virtuellen Netzwerk bereitstellen, Kunden stellen Azure Firewall jedoch in der Regel in einem zentralen virtuellen Netzwerk bereit und verbinden andere virtuelle Netzwerke in einem Hub-and-Spoke-Modell damit. Sie können die Standardroute von den verbundenen virtuellen Netzwerken anschließend so festlegen, dass sie auf dieses zentrale virtuelle Firewall-Netzwerk verweist. Globales VNET-Peering wird unterstützt, aber aufgrund von potenziellen Leistungs- und Latenzproblemen in den Regionen nicht empfohlen. Für optimale Leistungen stellen Sie eine Firewall pro Region bereit.

Der Vorteil dieses Modells ist die Möglichkeit, die Kontrolle über mehrere Spoke-VNETs über verschiedene Abonnements hinweg zentral auszuüben. Es ergeben sich zudem Kosteneinsparungen, da Sie nicht in jedem VNet separat eine Firewall bereitstellen müssen. Die Kosteneinsparungen sollten anhand der zugeordneten Peeringkosten auf der Grundlage der Datenverkehrsmuster der Kunden gemessen werden.

## <a name="how-can-i-install-the-azure-firewall"></a>Wie kann ich Azure Firewall installieren?

Sie können Azure Firewall über das Azure-Portal, PowerShell, die REST-API oder Vorlagen einrichten. Siehe [Tutorial: Bereitstellen und Konfigurieren von Azure Firewall über das Azure-Portal](tutorial-firewall-deploy-portal.md).

## <a name="what-are-some-azure-firewall-concepts"></a>Wie lauten einige der Azure Firewall-Konzepte?

Azure Firewall unterstützt-Regeln und Regelsammlungen. Eine Regelsammlung ist eine Liste von Regeln mit gleicher Reihenfolge und Priorität. Regelsammlungen werden in der Reihenfolge ihrer Priorität ausgeführt. Netzwerkregelsammlungen haben höhere Priorität als Anwendungsregelsammlungen, und alle Regeln werden beendet.

Es gibt drei Arten von Regelsammlungen:

* *Anwendungsregeln*: Konfigurieren vollqualifizierter Domänennamen (Fully Qualified Domain Names, FQDNs), auf die von einem Subnetz aus zugegriffen werden kann.
* *Netzwerkregeln*: Konfigurieren von Regeln mit Quelladressen, Protokollen, Zielports und Zieladressen.
* *NAT-Regeln*: Konfigurieren von DNAT-Regeln, um eingehende Internetverbindungen zuzulassen.

## <a name="does-azure-firewall-support-inbound-traffic-filtering"></a>Unterstützt Azure Firewall das Filtern des eingehenden Datenverkehrs?

Azure Firewall unterstützt Filter für eingehenden und ausgehenden Datenverkehr. Der Schutz des eingehenden Datenverkehrs ist typischerweise für Nicht-HTTP/S-Protokolle bestimmt, z. B. RDP-, SSH- und FTP-Protokolle. Verwenden Sie für den optimalen Schutz eingehenden HTTP/S-Datenverkehrs eine Webanwendungsfirewall z. B. [Azure Web Application Firewall (WAF)](../web-application-firewall/overview.md).

## <a name="which-logging-and-analytics-services-are-supported-by-the-azure-firewall"></a>Welche Protokollierungs- und Analysedienste werden von Azure Firewall unterstützt?

Azure Firewall ist zum Anzeigen und Analysieren von Firewallprotokollen in Azure Monitor integriert. Protokolle können an Log Analytics, Azure Storage oder Event Hubs gesendet werden. Sie können in Log Analytics oder von anderen Tools, wie Excel und Power BI, analysiert werden. Weitere Informationen finden Sie im [Tutorial: Überwachen von Azure Firewall-Protokollen](./firewall-diagnostics.md).

## <a name="how-does-azure-firewall-work-differently-from-existing-services-such-as-nvas-in-the-marketplace"></a>Wie unterscheidet sich die Funktionsweise von Azure Firewall von vorhandenen Diensten wie NVAs im Marketplace?

Azure Firewall ist ein grundlegender Firewalldienst für verschiedene Kundenszenarios. Es wird erwartet, dass Sie über eine Mischung aus Drittanbieter-NVAs und Azure Firewall verfügen. Eine bessere Zusammenarbeit hat höchste Priorität.

## <a name="what-is-the-difference-between-application-gateway-waf-and-azure-firewall"></a>Was ist der Unterschied zwischen Application Gateway-WAF und Azure Firewall?

Die Web Application Firewall (WAF) ist ein Feature von Application Gateway, das zentralisierten Schutz des eingehenden Datenverkehrs für Ihre Webanwendungen vor allgemeinen Exploits und Sicherheitsrisiken bietet. Azure Firewall bietet Schutz für eingehenden Datenverkehr für Nicht-HTTP/S-Protokolle (z.B. RDP, SSH, FTP), Schutz auf Netzwerkebene für eingehenden Datenverkehr für alle Ports und Protokolle sowie Schutz auf Anwendungsebene für ausgehenden HTTP/S-Datenverkehr.

## <a name="what-is-the-difference-between-network-security-groups-nsgs-and-azure-firewall"></a>Was ist der Unterschied zwischen Netzwerksicherheitsgruppen (Network Security Groups, NSGs) und Azure Firewall?

Der Azure Firewall-Dienst ergänzt die Funktionen der Netzwerksicherheitsgruppen. Zusammen bieten sie eine bessere „Defense-in-Depth“-Netzwerksicherheit. Netzwerksicherheitsgruppen bieten verteilte Datenverkehrsfilterung auf Netzwerkebene, um den Datenverkehr auf Ressourcen in virtuellen Netzwerken in jedem Abonnement zu beschränken. Azure Firewall ist eine vollständig zustandsbehaftete, zentrale Netzwerkfirewall als Dienst, die Schutz auf Netzwerk- und Anwendungsebene für verschiedene Abonnements und virtuelle Netzwerke bietet.

## <a name="are-network-security-groups-nsgs-supported-on-the-azurefirewallsubnet"></a>Werden Netzwerksicherheitsgruppen (NSGs) in AzureFirewallSubnet unterstützt?

Azure Firewall ist ein verwalteter Dienst mit mehreren Schutzebenen, z. B. Plattformschutz mit NSGs auf NIC-Ebene (nicht sichtbar).  NSGs auf Subnetzebene sind in AzureFirewallSubnet nicht erforderlich und deaktiviert, um zu verhindern, dass es zu Dienstunterbrechungen kommt.

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
$publicip1 = Get-AzPublicIpAddress -Name "Public IP1 Name" -ResourceGroupName "RG Name"
$publicip2 = Get-AzPublicIpAddress -Name "Public IP2 Name" -ResourceGroupName "RG Name"
$azfw.Allocate($vnet,@($publicip1,$publicip2))

Set-AzFirewall -AzureFirewall $azfw
```

> [!NOTE]
> Sie müssen der ursprünglichen Ressourcengruppe und dem Abonnement eine Firewall und eine öffentliche IP-Adresse neu zuordnen.

## <a name="what-are-the-known-service-limits"></a>Welche Dienstgrenzwerte sind bekannt?

Die Einschränkungen des Azure Firewall-Diensts finden Sie unter [Einschränkungen für Azure-Abonnements und Dienste, Kontingente und Einschränkungen](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-firewall-limits).

## <a name="can-azure-firewall-in-a-hub-virtual-network-forward-and-filter-network-traffic-between-two-spoke-virtual-networks"></a>Kann Azure Firewall in einem virtuellen Hubnetzwerk den Netzwerkdatenverkehr zwischen zwei virtuellen Spoke-Netzwerken weiterleiten und filtern?

Ja, Sie können Azure Firewall in einem virtuellen Hubnetzwerk verwenden, um den Datenverkehr zwischen zwei virtuellen Spoke-Netzwerken weiterzuleiten und zu filtern. Subnetze in jedem der virtuellen Spoke-Netzwerke müssen über eine UDR verfügen, die auf die Azure Firewall als Standardgateway verweist, damit dieses Szenario ordnungsgemäß funktioniert.

## <a name="can-azure-firewall-forward-and-filter-network-traffic-between-subnets-in-the-same-virtual-network-or-peered-virtual-networks"></a>Kann Azure Firewall den Netzwerkdatenverkehr zwischen Subnetzen im selben virtuellen Netzwerk oder über mittels Peering verknüpfte virtuelle Netzwerke weiterleiten und filtern?

Ja. Die Konfiguration der UDRs zur Umleitung des Datenverkehrs zwischen Subnetzen im gleichen VNET erfordert jedoch zusätzliche Anstrengungen. Während die Verwendung des VNET-Adressbereichs als Zielpräfix für den UDR ausreicht, wird auch der gesamte Datenverkehr von einem Computer zu einem anderen Computer im gleichen Subnetz über die Azure Firewall-Instanz geleitet. Um dies zu vermeiden, fügen Sie eine Route für das Subnetz in den UDR mit dem Typ **VNET** für den nächsten Hop ein. Das Verwalten dieser Routen kann umständlich und fehleranfällig sein. Die empfohlene Methode für die interne Netzwerksegmentierung ist die Verwendung von Netzwerksicherheitsgruppen, die keine UDRs erfordern.

## <a name="does-azure-firewall-outbound-snat-between-private-networks"></a>Übermittelt Azure Firewall SNAT bei privaten Netzwerken?

Azure Firewall bietet kein SNAT, wenn die Ziel-IP-Adresse ein privater IP-Bereich gemäß [IANA RFC 1918](https://tools.ietf.org/html/rfc1918) ist. Wenn Ihre Organisation einen öffentlichen IP-Adressbereich für private Netzwerke verwendet, leitet Azure Firewall den Datenverkehr per SNAT an eine der privaten IP-Adressen der Firewall in AzureFirewallSubnet weiter. Sie können Azure Firewall so konfigurieren, dass Ihr öffentlicher IP-Adressbereich **nicht** per SNAT weitergeleitet wird. Weitere Informationen finden Sie unter [Azure Firewall SNAT private IP address ranges](snat-private-range.md) (Azure Firewall SNAT – private Adressbereiche).

## <a name="is-forced-tunnelingchaining-to-a-network-virtual-appliance-supported"></a>Wird erzwungenes Tunneling bzw. die erzwungene Verkettung mit einem virtuellen Netzwerkgerät unterstützt?

Tunnelerzwingung wird beim Erstellen einer neuen Firewall unterstützt. Sie können eine vorhandene Firewall nicht für die Tunnelerzwingung konfigurieren. Weitere Informationen finden Sie unter [Azure Firewall-Tunnelerzwingung](forced-tunneling.md).

Azure Firewall muss über eine direkte Internetverbindung verfügen. Wenn Ihr Subnetz „AzureFirewallSubnet“ eine Standardroute zu Ihrem lokalen Netzwerk über BGP erfasst, müssen Sie diese mit der benutzerdefinierten Route 0.0.0.0/0 überschreiben. Legen Sie dabei den Wert **NextHopType** auf **Internet** fest, um die direkte Internetkonnektivität beizubehalten.

Wenn für Ihre Konfiguration die Erzwingung eines Tunnels zu einem lokalen Netzwerk erforderlich ist und Sie die Ziel-IP-Präfixe für Ihre Internetziele ermitteln können, können Sie diese Bereiche mit dem lokalen Netzwerk als nächsten Hop über eine benutzerdefinierte Route im Subnetz „AzureFirewallSubnet“ konfigurieren. Oder Sie können BGP verwenden, um diese Routen zu definieren.

## <a name="are-there-any-firewall-resource-group-restrictions"></a>Gibt es Einschränkungen bei Ressourcengruppen?

Ja. Firewall, VNET und die öffentliche IP-Adresse müssen sich in der gleichen Ressourcengruppe befinden.

## <a name="when-configuring-dnat-for-inbound-internet-network-traffic-do-i-also-need-to-configure-a-corresponding-network-rule-to-allow-that-traffic"></a>Muss ich bei der Konfiguration von DNAT für den eingehenden Internet-Netzwerkdatenverkehr auch eine entsprechende Netzwerkregel konfigurieren, um diesen Verkehr zu ermöglichen?

Nein. Mit NAT-Regeln wird implizit eine entsprechende Netzwerkregel hinzugefügt, um den übersetzten Datenverkehr zuzulassen. Sie können dieses Verhalten außer Kraft setzen, indem Sie explizit eine Netzwerkregelsammlung mit Ablehnungsregeln hinzufügen, die für den übersetzten Datenverkehr geeignet sind. Weitere Informationen zur Logik für die Azure Firewall-Regelverarbeitung finden Sie unter [Logik für die Azure Firewall-Regelverarbeitung](rule-processing.md).

## <a name="how-do-wildcards-work-in-an-application-rule-target-fqdn"></a>Wie funktionieren Platzhalter in einem FQDN eines Anwendungsregelziels?

Platzhalter können derzeit nur auf der linken Seite des FQDN verwendet werden. Beispiel: **_.contoso.com_* und **_contoso.com_*.

Wenn Sie **_.contoso.com_* konfigurieren, ist „*anyvalue*.contoso.com“ zulässig, nicht aber „contoso.com“ (Domänenapex). Wenn Sie die Domäne an der Spitze der Zone zulassen möchten, müssen Sie sie explizit als Ziel-FQDN konfigurieren.

## <a name="what-does-provisioning-state-failed-mean"></a>Was bedeutet *Bereitstellungsstatus: Fehler*?

Wenn eine Konfigurationsänderung angewendet wird, versucht Azure Firewall alle zugrunde liegenden Back-End-Instanzen zu aktualisieren. In seltenen Fällen kann bei der Aktualisierung einer dieser Back-End-Instanzen mit der neuen Konfiguration ein Fehler auftreten, und der Aktualisierungsvorgang wird mit dem Bereitstellungsstatus „Fehler“ angehalten. Azure Firewall ist noch funktionsfähig, die angewendete Konfiguration befindet sich jedoch unter Umständen in einem inkonsistenten Zustand, in dem einige Instanzen die vorherige Konfiguration und andere den aktualisierten Regelsatz verwenden. Versuchen Sie in diesem Fall, die Konfiguration noch einmal zu aktualisieren, bis der Vorgang erfolgreich war und für Ihre Firewall der Bereitstellungsstatus *Erfolgreich* angezeigt wird.

## <a name="how-does-azure-firewall-handle-planned-maintenance-and-unplanned-failures"></a>Wie verarbeitet Azure Firewall geplante Wartungsarbeiten und nicht geplante Fehler?
Azure Firewall besteht aus mehreren Back-End-Knoten in einer aktiv/aktiv-Konfiguration.  Für jede geplante Wartung gibt es Verbindungsausgleichslogik zum ordnungsgemäßen Aktualisieren von Knoten.  Updates sind für jede Azure-Region außerhalb der Geschäftszeiten geplant, um das Risiko einer Unterbrechung weiter einzuschränken.  Bei nicht geplanten Problemen wird ein neuer Knoten instanziiert, um den fehlerhaften Knoten zu ersetzen.  Die Konnektivität zum neuen Knoten wird in der Regel innerhalb von 10 Sekunden nach dem Zeitpunkt des Fehlers wieder hergestellt.

## <a name="how-does-connection-draining-work"></a>Wie funktioniert ein Verbindungsausgleich?

Für jede geplante Wartung gibt es Verbindungsausgleichslogik zum ordnungsgemäßen Aktualisieren von Back-End-Knoten. Azure Firewall wartet 90 Sekunden lang darauf, dass bestehende Verbindungen geschlossen werden. Bei Bedarf können Clients automatisch eine neue Verbindung mit einem anderen Back-End-Knoten herstellen.

## <a name="is-there-a-character-limit-for-a-firewall-name"></a>Gibt es eine Beschränkung der Zeichenzahl für einen Firewallnamen?

Ja. Ein Firewallname ist auf 50 Zeichen beschränkt.

## <a name="why-does-azure-firewall-need-a-26-subnet-size"></a>Warum benötigt Azure Firewall die Subnetzgröße /26?

Der Azure Firewall-Dienst muss mehr VM-Instanzen bereitstellen, als er skaliert. Ein /26-Adressraum stellt sicher, dass für die Firewall genügend IP-Adressen vorhanden sind, um der Skalierung gerecht zu werden.

## <a name="does-the-firewall-subnet-size-need-to-change-as-the-service-scales"></a>Muss die Größe des Firewallsubnetzes geändert werden, wenn der Dienst eine Skalierung ausführt?

Nein. Azure Firewall benötigt kein Subnetz, das größer als /26 ist.

## <a name="how-can-i-increase-my-firewall-throughput"></a>Wie kann ich meinen Firewalldurchsatz erhöhen?

Die anfängliche Durchsatzkapazität von Azure Firewall liegt zwischen 2,5 und 3 GBit/s und kann bis 30 GBit/s skaliert werden. Das automatische Aufskalieren erfolgt basierend auf der CPU-Auslastung und dem Durchsatz.

## <a name="how-long-does-it-take-for-azure-firewall-to-scale-out"></a>Wie lange dauert es, bis Azure Firewall aufskaliert wird?

Azure Firewall wird schrittweise skaliert, wenn der durchschnittliche Durchsatz oder die CPU-Auslastung 60 % beträgt. Der maximale Durchsatz einer standardmäßigen Bereitstellung beträgt ungefähr 2,5–3 GBit/s und beginnt mit dem Aufskalieren, wenn 60 % dieses Werts erreicht werden. Das Aufskalieren dauert zwischen fünf und sieben Minuten. 

Sorgen Sie bei Leistungstests dafür, mindestens 10 bis 15 Minuten zu testen, und richten Sie neue Verbindungen ein, um neu erstellte Firewallknoten nutzen zu können.

## <a name="does-azure-firewall-allow-access-to-active-directory-by-default"></a>Lässt Azure Firewall standardmäßig den Zugriff auf Active Directory zu?

Nein. Standardmäßig blockiert Azure Firewall den Zugriff auf Active Directory. Konfigurieren Sie das Diensttag „AzureActiveDirectory“, um den Zugriff zuzulassen. Weitere Informationen finden Sie unter [Azure Firewall-Diensttags](service-tags.md).

## <a name="can-i-exclude-a-fqdn-or-an-ip-address-from-azure-firewall-threat-intelligence-based-filtering"></a>Kann ich einen FQDN oder eine IP-Adresse von der auf Azure Firewall Threat Intelligence basierenden Filterung ausschließen?

Ja, Sie können dafür Azure PowerShell verwenden:

```azurepowershell
# Add a Threat Intelligence allow list to an Existing Azure Firewall

## Create the allow list with both FQDN and IPAddresses

$fw = Get-AzFirewall -Name "Name_of_Firewall" -ResourceGroupName "Name_of_ResourceGroup"
$fw.ThreatIntelWhitelist = New-AzFirewallThreatIntelWhitelist `
   -FQDN @("fqdn1", "fqdn2", …) -IpAddress @("ip1", "ip2", …)

## Or Update FQDNs and IpAddresses separately

$fw = Get-AzFirewall -Name $firewallname -ResourceGroupName $RG
$fw.ThreatIntelWhitelist.IpAddresses = @($fw.ThreatIntelWhitelist.IpAddresses + $ipaddresses)
$fw.ThreatIntelWhitelist.fqdns = @($fw.ThreatIntelWhitelist.fqdns + $fqdns)


Set-AzFirewall -AzureFirewall $fw
```

## <a name="why-can-a-tcp-ping-and-similar-tools-successfully-connect-to-a-target-fqdn-even-when-no-rule-on-azure-firewall-allows-that-traffic"></a>Warum kann ein TCP-Ping und können ähnliche Tools eine Verbindung mit einem Ziel-FQDN erfolgreich herstellen, auch wenn keine Regel von Azure Firewall diesen Datenverkehr zulässt?

Ein TCP-Ping stellt tatsächlich keine Verbindung mit dem Ziel-FQDN her. Der Grund dafür: Der transparente Proxy von Azure Firewall lauscht an Port 80/443 für ausgehenden Datenverkehr. Das TCP-Pingsignal stellt eine Verbindung mit der Firewall her, die das Paket dann verwirft. Dieses Verhalten hat keinerlei Auswirkung auf die Sicherheit. Um ein Durcheinander zu vermeiden, untersuchen wir jedoch mögliche Änderungen an diesem Verhalten.

## <a name="are-there-limits-for-the-number-of-ip-addresses-supported-by-ip-groups"></a>Gibt es Beschränkungen bei der Anzahl von IP-Adressen, die von IP-Gruppen unterstützt werden?

Ja. Weitere Informationen finden Sie unter [Grenzwerte für Azure-Abonnements, -Dienste und -Kontingente sowie allgemeine Beschränkungen](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-firewall-limits).

## <a name="can-i-move-an-ip-group-to-another-resource-group"></a>Kann ich eine IP-Gruppe in eine andere Ressourcengruppe verschieben?

Nein, das Verschieben einer IP-Gruppe in eine andere Ressourcengruppe wird zurzeit nicht unterstützt.

## <a name="what-is-the-tcp-idle-timeout-for-azure-firewall"></a>Was ist das TCP-Leerlauftimeout für Azure Firewall?

Das Standardverhalten einer Netzwerkfirewall besteht darin, TCP-Verbindungen aufrechtzuerhalten und sie sofort zu schließen, wenn es keine Aktivität gibt. Das TCP-Leerlauftimeout von Azure Firewall beträgt vier Minuten. Diese Einstellung ist nicht konfigurierbar. Wenn die Dauer einer Inaktivitätsperiode den Timeoutwert überschreitet, gibt es keine Garantie dafür, dass die TCP- oder HTTP-Sitzung aufrechterhalten wird. Eine gängige Methode zur Aufrechterhaltung von Verbindungen ist TCP-Keep-Alive. Dadurch bleibt die Verbindung länger aktiv. Weitere Informationen finden Sie in den [.NET-Beispielen](/dotnet/api/system.net.servicepoint.settcpkeepalive?view=netcore-3.1#System_Net_ServicePoint_SetTcpKeepAlive_System_Boolean_System_Int32_System_Int32_).

## <a name="can-i-deploy-azure-firewall-without-a-public-ip-address"></a>Kann ich die Azure Firewall ohne eine öffentliche IP-Adresse bereitstellen?

Nein, derzeit müssen Sie Azure Firewall mit einer öffentlichen IP-Adresse bereitstellen.

## <a name="where-does-azure-firewall-store-customer-data"></a>Wo speichert Azure Firewall Kundendaten?

Azure Firewall speichert Kundendaten in der Region, in der sie bereitgestellt werden, und verschiebt sie nicht aus dieser Region.