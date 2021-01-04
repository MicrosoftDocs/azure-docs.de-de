---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 10/21/2020
ms.author: ccompy
ms.openlocfilehash: 963f0698b921caa413c61059ad69284c41b4f265
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95999433"
---
Die Verwendung der regionalen VNET-Integration ermöglicht der App Zugriff auf Folgendes:

* Ressourcen in einem VNET in derselben Region wie Ihre App.
* Ressourcen in VNETs, die mit dem VNET, in das Ihre App integriert ist, per Peering verbunden sind.
* Durch Dienstendpunkte geschützte Dienste.
* Ressourcen über Azure ExpressRoute-Verbindungen.
* Ressourcen in dem VNET, in das Sie integriert sind.
* Ressourcen über Verbindungen mit Peering, einschließlich von Azure ExpressRoute-Verbindungen.
* Private Endpunkte 

Wenn Sie VNET-Integration mit VNETs in derselben Region verwenden, können Sie die folgenden Azure-Netzwerkfunktionen nutzen:

* **Netzwerksicherheitsgruppen (NSGs)** : Sie können ausgehenden Datenverkehr mit einer NSG blockieren, die in Ihrem Integrationssubnetz platziert ist. Die Regeln für eingehenden Datenverkehr gelten nicht, da Sie die VNET-Integration nicht verwenden können, um eingehenden Zugriff auf Ihre App bereitzustellen.
* **Routingtabellen (UDRs)** : Sie können eine Routingtabelle im Integrationssubnetz platzieren, um ausgehenden Datenverkehr an beliebige gewünschte Ziele zu senden.

Standardmäßig leitet Ihre App nur RFC 1918-Datenverkehr in das VNET weiter. Wenn Sie den gesamten ausgehenden Datenverkehr an das VNET weiterleiten möchten, wenden Sie die App-Einstellung WEBSITE_VNET_ROUTE_ALL auf Ihre App an. So konfigurieren Sie die App-Einstellung:

1. Navigieren Sie in Ihrem App-Portal zur Benutzeroberfläche für die **Konfiguration**. Wählen Sie **Neue Anwendungseinstellung** aus.
1. Geben Sie **WEBSITE_VNET_ROUTE_ALL** im Feld **Name** ein, und geben Sie **1** im Feld **Wert** ein.

   ![Angeben der Anwendungseinstellung][4]

1. Klicken Sie auf **OK**.
1. Wählen Sie **Speichern** aus.

> [!NOTE]
> Wenn Sie den gesamten ausgehenden Datenverkehr in Ihr VNET weiterleiten, unterliegt dieser den NSGs und UDRs, die auf Ihr Integrationssubnetz angewandt werden. Wenn Sie den gesamten ausgehenden Datenverkehr in das VNET weiterleiten, sind Ihre ausgehenden Adressen weiterhin die in Ihren App-Eigenschaften aufgeführten ausgehenden Adressen, es sei denn, Sie stellen Routen bereit, um den Datenverkehr an andere Ziele zu senden.

Es gibt einige Einschränkungen bei der Verwendung der VNET-Integration in VNETs in derselben Region:

* Sie können nicht über Verbindungen mit globalem Peering auf Ressourcen zugreifen.
* Das Feature ist in allen App Service-Skalierungseinheiten in den Tarifen „Premium V2“ und „Premium V3“ verfügbar. Es ist außerdem im Tarif „Standard“ verfügbar, jedoch nur in neueren App Service-Skalierungseinheiten. Wenn Sie sich auf einer älteren Skalierungseinheit befinden, können Sie die Funktion nur aus einem „Premium V2“-App Service-Plan verwenden. Wenn Sie sicherstellen möchten, dass Sie die Funktion in einem „Standard“-App Service-Plan verwenden können, erstellen Sie Ihre App in einem „Premium V3“-App Service-Plan. Diese Pläne werden nur für auf unseren neuesten Skalierungseinheiten unterstützt. Sie können anschließend nach Bedarf herunterskalieren.  
* Das Integrationssubnetz kann nur von einem App Service-Plan verwendet werden.
* Die Funktion kann nicht für Apps im Plan „App Service (isoliert)“ in einer App Service-Umgebung verwendet werden.
* Für das Feature ist ein nicht genutztes Subnetz vom Typ /28 oder größer in einem Azure Resource Manager-VNet erforderlich.
* Die App und das VNET müssen sich in derselben Region befinden.
* Es ist nicht möglich, ein VNET mit einer integrierten App zu löschen. Entfernen Sie die Integration, bevor Sie das VNET löschen.
* Sie können nur VNETs im selben Abonnement wie die App integrieren.
* Sie können nur eine regionale VNET-Integration pro App Service-Plan verwenden. Mehrere Apps im gleichen App Service-Plan können das gleiche VNET verwenden.
* Sie können das Abonnement einer App oder eines Plans nicht ändern, solange eine App mit regionaler VNET-Integration vorhanden ist.
* Ihre App kann ohne Konfigurationsänderungen keine Adressen in Azure DNS Private Zones auflösen.

VNet-Integration hängt von der Verwendung eines dedizierten Subnetzes ab.  Wenn Sie ein Subnetz bereitstellen, verliert das Azure-Subnetz 5 IP-Adressen von Anfang an. Für jede Instanz des Plans wird eine Adresse aus dem Integrationssubnetz verwendet. Wenn Sie Ihre App auf vier Instanzen skalieren, werden vier Adressen verwendet. Das Soll von 5 Adressen aus der Subnetzgröße bedeutet, dass pro CIDR-Block maximal Adressen wie folgt verfügbar sind:

- /28 verfügt über 11 Adressen
- /27 verfügt über 27 Adressen
- /26 verfügt über 59 Adressen

Wenn Sie die Größe zentral hoch- oder herunterskalieren, müssen Sie Ihren Adressenbedarf für einen kurzen Zeitraum verdoppeln. Die Grenzwerte für die Größe bedeuten, dass die tatsächlich verfügbaren, unterstützten Instanzen pro Subnetzgröße wie folgt lauten, wenn Ihr Subnetz vom folgenden Typ ist:

- /28, Ihre maximale horizontale Skalierung beträgt 5 Instanzen
- /27, Ihre maximale horizontale Skalierung beträgt 13 Instanzen
- /26, Ihre maximale horizontale Skalierung beträgt 29 Instanzen

Bei den für die horizontale Skalierung angegebenen Grenzwerten wird vorausgesetzt, dass Sie zu einem Zeitpunkt entweder die Größe oder die SKU zentral hoch- oder herunterskalieren müssen. 

Da die Subnetzgröße nach der Zuweisung nicht mehr geändert werden kann, verwenden Sie ein Subnetz, das für die Aufnahme jedweder Skalierung Ihrer App groß genug ist. Um jegliche Probleme mit der Subnetzkapazität zu vermeiden, ist die empfohlene Größe /26 mit 64 Adressen.  

Wenn Ihre Apps in einem anderen Plan ein VNET erreichen sollen, das bereits mit Apps in einem anderen Plan verbunden ist, müssen Sie ein anderes Subnetz als das von der bereits vorhandenen VNET-Integration verwendete auswählen.

Das Feature wird für Windows- und Linux-Apps vollständig unterstützt, einschließlich [benutzerdefinierten Containern](../articles/app-service/quickstart-custom-container.md). Das Verhalten ist in Windows- und Linux-Apps gleich.

### <a name="service-endpoints"></a>Dienstendpunkte

Die regionale VNET-Integration ermöglicht es Ihnen, Dienstendpunkte zu verwenden. Um Dienstendpunkte mit Ihrer App zu verwenden, stellen Sie über die regionale VNET-Integration eine Verbindung mit einem ausgewählten VNET her, und konfigurieren Sie dann in dem für die Integration verwendeten Subnetz Dienstendpunkte für den Zieldienst. Wenn Sie dann über Dienstendpunkte auf einen Dienst zugreifen möchten:

1. Konfigurieren Sie die Integration des regionalen VNET mit Ihrer Web-App.
1. Wechseln Sie zum Zieldienst, und konfigurieren Sie Dienstendpunkte für das Subnetz, das für die Integration verwendet wird.

### <a name="network-security-groups"></a>Netzwerksicherheitsgruppen

Mit Netzwerksicherheitsgruppen können Sie eingehenden und ausgehenden Datenverkehr an bzw. von Ressourcen in einem VNET blockieren. Eine App mit regionaler VNET-Integration kann eine [Netzwerksicherheitsgruppe][VNETnsg] verwenden, um ausgehenden Datenverkehr an Ressourcen in Ihrem VNET oder im Internet zu blockieren. Zum Blockieren von Datenverkehr an öffentliche Adressen muss die Anwendungseinstellung WEBSITE_VNET_ROUTE_ALL auf 1 festgelegt werden. Die Regeln für eingehenden Datenverkehr in einer NSG gelten nicht für Ihre App, weil sich die VNET-Integration nur auf ausgehenden Datenverkehr von Ihrer App auswirkt.

Um den eingehenden Datenverkehr für Ihre App zu steuern, verwenden Sie das Feature für Zugriffsbeschränkungen. Eine NSG, die auf Ihr Integrationssubnetz angewendet wird, ist unabhängig von den Routen wirksam, die auf Ihr Integrationssubnetz angewendet werden. Wenn WEBSITE_VNET_ROUTE_ALL auf 1 festgelegt ist und Sie über keine Routen verfügen, die sich auf den Datenverkehr der öffentlichen Adresse in Ihrem Integrationssubnetz auswirken, unterliegt der gesamte ausgehende Datenverkehr den NSGs, die Ihrem Integrationssubnetz zugewiesen sind. Wenn WEBSITE_VNET_ROUTE_ALL nicht festgelegt ist, werden NSGs nur auf RFC1918-Datenverkehr angewendet.

### <a name="routes"></a>Routen

Sie können mithilfe von Routingtabellen ausgehenden Datenverkehr von Ihrer App an beliebige Ziele weiterleiten. Routingtabellen wirken sich standardmäßig nur auf Datenverkehr mit dem Ziel RFC1918 aus. Wenn Sie WEBSITE_VNET_ROUTE_ALL auf 1 festlegen, sind alle ausgehenden Aufrufe betroffen. Routen, die für Ihr Integrationssubnetz festgelegt werden, wirken sich nicht auf Antworten auf eingehende App-Anforderungen aus. Gängige Ziele können Firewallgeräte oder Gateways beinhalten.

Wenn Sie den gesamten ausgehenden Datenverkehr lokal weiterleiten möchten, können Sie eine Routingtabelle verwenden, um den gesamten ausgehenden Datenverkehr an das ExpressRoute-Gateway zu senden. Wenn Sie Datenverkehr nicht an ein Gateway weiterleiten, müssen Sie unbedingt Routen im externen Netzwerk festlegen, über die Antworten zurückgesendet werden.

BGP-Routen (Border Gateway Protocol) wirken sich ebenfalls auf den App-Datenverkehr aus. Wenn Sie über BGP-Routen von einem ExpressRoute-Gateway verfügen, ist der ausgehende Datenverkehr Ihrer App betroffen. BGP-Routen wirken sich standardmäßig nur auf Datenverkehr mit dem Ziel RFC1918 aus. Wenn WEBSITE_VNET_ROUTE_ALL auf 1 festgelegt ist, kann der gesamte ausgehende Datenverkehr von ihren BGP-Routen betroffen sein.

### <a name="azure-dns-private-zones"></a>Azure DNS Private Zones 

Nachdem Ihre App in Ihr VNET integriert wurde, verwendet sie den DNS-Server, mit dem Ihr VNET konfiguriert ist. Sie können dieses Verhalten in Ihrer App außer Kraft setzen, indem Sie die App-Einstellung WEBSITE_DNS_SERVER mit der Adresse Ihres gewünschten DNS-Servers konfigurieren. Wenn Sie einen benutzerdefinierten DNS-Server mit Ihrem VNet konfiguriert haben, aber eigentlich wollten, dass Ihre App private Azure DNS-Zonen verwendet, sollten Sie WEBSITE_DNS_SERVER auf den Wert 168.63.129.16 festlegen. 

### <a name="private-endpoints"></a>Private Endpunkte

Wenn Sie Aufrufe an [private Endpunkte][privateendpoints] durchführen möchten, müssen Sie sicherstellen, dass Ihre DNS-Suchen zu dem privaten Endpunkt aufgelöst werden. Um sicherzustellen, dass die DNS-Suchen aus Ihrer App auf Ihre privaten Endpunkte verweisen, können Sie Folgendes ausführen:

* Integrieren in private Azure DNS-Zonen. Wenn Ihr VNet nicht über einen benutzerdefinierten DNS-Server verfügt, erfolgt dies automatisch.
* Verwalten des privaten Endpunkts im DNS-Server, der von Ihrer App verwendet wird. Hierzu müssen Sie die Adresse des privaten Endpunkts kennen und dann den Endpunkt, den Sie erreichen möchten, mit einem A-Eintrag auf diese Adresse verweisen lassen.
* Konfigurieren Ihres eigenen DNS-Servers zur Weiterleitung an private Azure DNS-Zonen.

<!--Image references-->
[4]: ../includes/media/web-sites-integrate-with-vnet/vnetint-appsetting.png

<!--Links-->
[VNETnsg]: /azure/virtual-network/security-overview/
[privateendpoints]: ../articles/app-service/networking/private-endpoint.md