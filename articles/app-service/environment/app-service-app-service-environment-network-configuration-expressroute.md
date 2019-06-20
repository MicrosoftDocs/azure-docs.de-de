---
title: Details zur Netzwerkkonfiguration für Microsoft Azure ExpressRoute – App Service
description: Details zur Netzwerkkonfiguration von App Service-Umgebungen für PowerApps in an eine Microsoft Azure ExpressRoute-Verbindung angeschlossenen virtuellen Netzwerken.
services: app-service
documentationcenter: ''
author: stefsch
manager: nirma
editor: ''
ms.assetid: 34b49178-2595-4d32-9b41-110c96dde6bf
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/14/2016
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: e0fa87facec73efdfff1a9908dcba92838215425
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62130669"
---
# <a name="network-configuration-details-for-app-service-environment-for-powerapps-with-azure-expressroute"></a>Details zur Netzwerkkonfiguration für App Service-Umgebungen für PowerApps mit Microsoft Azure ExpressRoute

Kunden können eine [Microsoft Azure ExpressRoute][ExpressRoute]-Verbindung mit der Infrastruktur ihres virtuellen Netzwerks herstellen und so ihr lokales Netzwerk auf Azure ausdehnen. Eine App Service-Umgebung kann in einem Subnetz der Infrastruktur des [virtuellen Netzwerks][virtualnetwork] erstellt werden. In der App Service-Umgebung ausgeführte Apps stellen sichere Verbindungen mit Back-End-Ressourcen her, auf die nur über die ExpressRoute-Verbindung ein Zugriff besteht.  

Eine App Service-Umgebung kann in den folgenden Szenarios erstellt werden:
- Virtuelle Netzwerke mit Azure Resource Manager.
- Klassisches Bereitstellungsmodell für virtuelle Netzwerke.
- Virtuelle Netzwerke, die öffentliche Adressbereiche oder RFC1918-Adressräume (d. h. private Adressen) verwenden. 

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="required-network-connectivity"></a>Erforderliche Netzwerkkonnektivität

Für App Service-Umgebungen gibt es Anforderungen an die Netzwerkkonnektivität, die ursprünglich nicht von einem virtuellen Netzwerk erfüllt werden konnten, das mit ExpressRoute verbunden ist.

Eine App Service-Umgebung erfordert für eine ordnungsgemäße Funktionsweise die folgenden Netzwerkkonnektivitätseinstellungen:

* Ausgehende Netzwerkverbindungen mit Azure Storage-Endpunkten in der ganzen Welt an Port 80 und Port 443. Diese Endpunkte befinden sich in derselben Region wie die App Service-Umgebung und in anderen Azure-Regionen. Azure Storage-Endpunkte werden unter den folgenden DNS-Domänen aufgelöst: table.core.windows.net, blob.core.windows.net, queue.core.windows.net und file.core.windows.net.  

* Ausgehende Netzwerkverbindungen mit dem Azure Files-Dienst an Port 445.

* Ausgehende Netzwerkkonnektivität mit Azure SQL-Datenbank-Endpunkten, die sich in der gleichen Region wie die App Service-Umgebung befinden. SQL-Datenbank-Endpunkte können unter der Domäne database.windows.net aufgelöst werden, für die ein Zugriff auf die Ports 1433, 11000-11999 und 14000-14999 erforderlich ist. Weitere Informationen zur Portverwendung von SQL-Datenbank V12 finden Sie unter [Andere Ports als 1433 für ADO.NET 4.5](../../sql-database/sql-database-develop-direct-route-ports-adonet-v12.md).

* Ausgehende Netzwerkkonnektivität zu den Azure-Endpunkten der Verwaltungsebene (klassisches Azure-Bereitstellungsmodell und Azure Resource Manager-Endpunkte). Die Konnektivität zu diesen Endpunkten beinhaltet die Domänen management.core.windows.net und management.azure.com. 

* Ausgehende Netzwerkkonnektivität zu den Domänen ocsp.msocsp.com, mscrl.microsoft.com und crl.microsoft.com. Konnektivität zu diesen Domänen ist zur Unterstützung von SSL-Funktionen erforderlich.

* Die DNS-Konfiguration für das virtuelle Netzwerk muss alle in diesem Artikel genannten Endpunkte und Domänen auflösen können. Wenn die Endpunkte nicht aufgelöst werden können, schlägt das Erstellen der App Service-Umgebung fehl. Alle vorhandenen App Service-Umgebungen werden als fehlerhaft markiert.

* Ausgehender Zugriff über Port 53 wird für die Kommunikation mit DNS-Servern benötigt.

* Falls ein benutzerdefinierter DNS-Server am anderen Ende eines VPN-Gateways vorhanden ist, muss der DNS-Server über das Subnetz mit der App Service-Umgebung erreichbar sein. 

* Der ausgehende Netzwerkpfad kann weder durch interne Unternehmensproxys laufen noch zwangsweise zur lokalen Infrastruktur getunnelt werden. Durch diese Aktionen wird die tatsächliche NAT-Adresse des ausgehenden Netzwerkdatenverkehrs der App Service-Umgebung geändert. Änderungen an der NAT-Adresse des ausgehenden Netzwerkdatenverkehrs einer App Service-Umgebung verursachen bei vielen Endpunkten Verbindungsfehler. Die Erstellung der App Service-Umgebung schlägt fehl. Alle vorhandenen App Service-Umgebungen werden als fehlerhaft markiert.

* Der eingehende Netzwerkzugriff auf die erforderlichen Ports für die App Service-Umgebung muss zulässig sein. Weitere Informationen hierzu finden Sie unter [Steuern von eingehendem Datenverkehr in eine App Service-Umgebung][requiredports].

Um die DNS-Anforderungen zu erfüllen, müssen Sie sicherstellen, dass eine gültige DNS-Infrastruktur für das virtuelle Netzwerk konfiguriert und beibehalten wird. Wenn die DNS-Konfiguration nach der Erstellung einer App Service-Umgebung geändert wird, können Entwickler erzwingen, dass die App Service-Umgebung die neue DNS-Konfiguration übernimmt. Mithilfe des Symbols **Neustart** unter der App Service-Umgebungsverwaltung in [Azure-Portal][NewPortal] können Sie einen parallelen Neustart der Umgebung auslösen. Der Neustart bewirkt, dass die Umgebung die neue DNS-Konfiguration übernimmt.

Um die Anforderungen des eingehenden Netzwerkzugriffs zu erfüllen, konfigurieren Sie eine [Netzwerksicherheitsgruppe (NSG)][NetworkSecurityGroups] im Subnetz der App Service-Umgebung. Die NSG gestattet den erforderlichen Zugriff [zum Steuern des eingehenden Datenverkehrs in die App Service-Umgebung][requiredports].

## <a name="outbound-network-connectivity"></a>Ausgehende Netzwerkkonnektivität

In der Standardeinstellung kündigt eine neu erstellte ExpressRoute-Verbindung eine Standardroute an, die ausgehende Internetkonnektivität zulässt. Die App Service-Umgebung kann diese Konfiguration verwenden, um sich mit anderen Azure-Endpunkten zu verbinden.

Eine gängige Kundenkonfiguration sieht das Definieren einer eigenen Standardroute (0.0.0.0/0) vor, die ausgehenden Internetdatenverkehr zwingt, die lokale Infrastruktur zu durchlaufen. Dieser Datenverkehr unterbricht ausnahmslos die App Service-Umgebung. Der ausgehende Datenverkehr wird entweder lokal blockiert oder mittels NAT in eine nicht mehr nachvollziehbare Gruppe von Adressen übersetzt, die nicht mehr mit verschiedenen Azure-Endpunkten funktionieren.

Die Lösung besteht darin, eine oder mehrere benutzerdefinierte Routen (UDR) in dem Subnetz zu definieren, das die App Service-Umgebung enthält. Eine UDR definiert subnetzspezifische Routen, die anstelle der Standardroute berücksichtigt werden.

Verwenden Sie wenn möglich die folgende Konfiguration:

* Die ExpressRoute-Konfiguration kündigt 0.0.0.0/0 an. In der Standardeinstellung wird das Tunneling des gesamten ausgehenden Datenverkehrs lokal durch die Konfiguration erzwungen.
* Die für das Subnetz mit der App Service-Umgebung geltende UDR definiert 0.0.0.0/0 mit „Internet“ als Typ des nächsten Hops. Ein Beispiel für diese Konfiguration wird weiter unten in diesem Artikel beschrieben.

Die gemeinsame Auswirkung dieser Konfiguration ist die, dass die UDR auf Subnetzebene Vorrang vor der ExpressRoute-Tunnelerzwingung hat. Der ausgehende Internetzugriff aus der App Service-Umgebung ist garantiert.

> [!IMPORTANT]
> Die in einer UDR definierten Routen müssen hinreichend spezifisch sein, um Vorrang vor allen von der ExpressRoute-Konfiguration angekündigten Routen zu haben. Das im nächsten Abschnitt beschriebene Beispiel verwendet den breiten Adressbereich 0.0.0.0/0. Dieser Bereich kann durch Routenankündigungen mit spezifischeren Adressbereichen versehentlich überschrieben werden.
> 
> App Service-Umgebungen werden nicht mit ExpressRoute-Konfigurationen unterstützt, die Routen „über Kreuz“ vom öffentlichen Peeringpfad zum privaten Peeringpfad ankündigen. ExpressRoute-Konfigurationen, für die öffentliches Peering konfiguriert ist, erhalten Routenankündigungen von Microsoft für zahlreiche Microsoft Azure-IP-Adressbereiche. Werden diese Adressbereiche über Kreuz auf dem privaten Peeringpfad angekündigt, werden alle ausgehenden Netzwerkpakete aus dem Subnetz der App Service-Umgebung zwangsweise zur lokalen Netzwerkinfrastruktur des Kunden getunnelt. Dieser Netzwerkdatenfluss wird derzeit für App Service-Umgebungen nicht unterstützt. Eine Lösung besteht darin, „Über-Kreuz-Ankündigungen“ von Routen vom öffentlichen Peeringpfad zum privaten Peeringpfad zu verhindern.
> 
> 

Hintergrundinformationen zu benutzerdefinierten Routen finden Sie unter [Routing von Datenverkehr für virtuelle Netzwerke][UDROverview].  

Informationen zum Erstellen und Konfigurieren benutzerdefinierter Routen finden Sie unter [Weiterleiten von Netzwerkdatenverkehr mit einer Routingtabelle mithilfe von PowerShell][UDRHowTo].

## <a name="udr-configuration"></a>UDR-Konfiguration

Dieser Abschnitt zeigt eine UDR-Beispielkonfiguration für eine App Service-Umgebung.

### <a name="prerequisites"></a>Voraussetzungen

* Installieren Sie Azure PowerShell über die [Seite mit den Azure-Downloads][AzureDownloads]. Wählen Sie einen Download mit einem Datum vom Juni 2015 oder später. Klicken Sie unter **Befehlszeilentools** > **Windows PowerShell** auf **Installieren**, um die neuesten PowerShell-Cmdlets zu installieren.

* Erstellen Sie ein eindeutiges Subnetz für die ausschließliche Verwendung durch die App Service-Umgebung. Durch das eindeutige Subnetz wird sichergestellt, dass die im Subnetz eingerichteten UDRs nur für ausgehenden Datenverkehr für die App Service-Umgebung geöffnet werden.

> [!IMPORTANT]
> Stellen Sie die App Service-Umgebung erst bereit, nachdem Sie die Konfigurationsschritte abgeschlossen haben. Die Schritte stellen sicher, dass eine ausgehende Netzwerkkonnektivität verfügbar ist, bevor Sie versuchen, eine App Service-Umgebung bereitzustellen.

### <a name="step-1-create-a-route-table"></a>Schritt 1: Erstellen einer Routingtabelle

Erstellen Sie – wie in diesem Codeausschnitt gezeigt ª eine Routingtabelle namens **DirectInternetRouteTable** in der Azure-Region USA, Westen:

`New-AzureRouteTable -Name 'DirectInternetRouteTable' -Location uswest`

### <a name="step-2-create-routes-in-the-table"></a>Schritt 2: Erstellen von Routen in der Tabelle

Fügen Sie Routen zu der Routingtabelle hinzu, um den ausgehenden Internetzugriff zu aktivieren.  

Konfigurieren Sie den ausgehenden Zugriff auf das Internet. Definieren Sie – wie im folgenden Codeausschnitt gezeigt – eine Route für 0.0.0.0/0:

`Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 0' -AddressPrefix 0.0.0.0/0 -NextHopType Internet`

0.0.0.0/0 ist ein breiter Adressbereich. Der Bereich wird durch Adressbereiche überschrieben, die von ExpressRoute angekündigt werden und spezifischer sind. Eine UDR mit einer 0.0.0.0/0-Route sollte in Verbindung mit einer ExpressRoute-Konfiguration verwendet werden, die nur 0.0.0.0/0 ankündigt. 

Alternativ können Sie eine aktuelle umfassende Liste der von Azure verwendeten CIDR-Adressbereiche herunterladen. Die XML-Datei für alle Azure-IP-Adressbereiche steht im [Microsoft Download Center][DownloadCenterAddressRanges] zur Verfügung.  

> [!NOTE]
>
> Die Azure-IP-Adressbereiche ändern sich im Laufe der Zeit. Benutzerdefinierte Routen benötigen regelmäßige manuelle Aktualisierungen, um auf dem neuesten Stand zu bleiben.
>
> Für eine einzelne UDR gilt standardmäßig eine Obergrenze von 100 Routen. Sie müssen die Azure-IP-Adressbereiche zusammenfassen, damit der Grenzwert von 100 Routen eingehalten wird. UDR-definierte Routen müssen spezifischer sein als von Ihrer ExpressRoute-Verbindung angekündigte Routen.
> 

### <a name="step-3-associate-the-table-to-the-subnet"></a>Schritt 3: Zuordnen der Tabelle zum Subnetz

Ordnen Sie die Routingtabelle dem Subnetz zu, in dem Sie die App Service-Umgebung bereitstellen möchten. Mit diesem Befehl wird die Tabelle **DirectInternetRouteTable** dem Subnetz **ASESubnet** zugeordnet, das die App Service-Umgebung enthalten wird.

`Set-AzureSubnetRouteTable -VirtualNetworkName 'YourVirtualNetworkNameHere' -SubnetName 'ASESubnet' -RouteTableName 'DirectInternetRouteTable'`

### <a name="step-4-test-and-confirm-the-route"></a>Schritt 4: Testen und Bestätigen der Route

Nachdem die Routingtabelle dem Subnetz zugeordnet wurde, testen und bestätigen Sie die Route.

Stellen Sie einen virtuellen Computer im Subnetz bereit, und bestätigen Sie die folgenden Bedingungen:

* Ausgehender Datenverkehr zu den in diesem Artikel beschriebenen Azure-Endpunkten und Nicht-Azure-Endpunkten wird **nicht** über die ExpressRoute-Verbindung übertragen. Wenn der ausgehende Datenverkehr des Subnetzes einer lokalen Tunnelerzwingung unterliegt, schlägt die Erstellung der App Service-Umgebung stets fehl.
* Alle DNS-Lookups für die in diesem Artikel beschriebenen Endpunkte werden korrekt aufgelöst. 

Nachdem Sie die Konfigurationsschritte ausgeführt und die Route bestätigt haben, löschen Sie den virtuellen Computer. Das Subnetz muss „leer“ sein, wenn die App Service-Umgebung erstellt wird.

Nun können Sie die App Service-Umgebung bereitstellen.

## <a name="next-steps"></a>Nächste Schritte

Informationen zum Einstieg in die App Service-Umgebung für PowerApps finden Sie unter [Einführung in die App Service-Umgebung][IntroToAppServiceEnvironment].

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/services/virtual-network/ 
[ExpressRoute]: https://azure.microsoft.com/services/expressroute/ 
[requiredports]: app-service-app-service-environment-control-inbound-traffic.md 
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/ 
[UDROverview]: https://azure.microsoft.com/documentation/articles/virtual-networks-udr-overview/ 
<!-- Old link -- [UDRHowTo]: https://azure.microsoft.com/documentation/articles/virtual-networks-udr-how-to/ -->

[UDRHowTo]: https://docs.microsoft.com/azure/virtual-network/tutorial-create-route-table-powershell 
[HowToCreateAnAppServiceEnvironment]: app-service-web-how-to-create-an-app-service-environment.md 
[AzureDownloads]: https://azure.microsoft.com/downloads/ 
[DownloadCenterAddressRanges]: https://www.microsoft.com/download/details.aspx?id=41653 
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/ 
[IntroToAppServiceEnvironment]:  app-service-app-service-environment-intro.md 
[NewPortal]:  https://portal.azure.com 


<!-- IMAGES -->
