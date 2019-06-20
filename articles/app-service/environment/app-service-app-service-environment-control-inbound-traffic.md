---
title: Steuern von eingehendem Datenverkehr in eine App Service-Umgebung – Azure
description: Erfahren Sie, wie Netzwerksicherheitsregeln für das Steuern des in einer App Service-Umgebung eingehenden Datenverkehrs konfiguriert werden.
services: app-service
documentationcenter: ''
author: ccompy
manager: erikre
editor: ''
ms.assetid: 4cc82439-8791-48a4-9485-de6d8e1d1a08
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2017
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: 84575dcb67845a074ce19cf9d819e1dda3f90e20
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62130788"
---
# <a name="how-to-control-inbound-traffic-to-an-app-service-environment"></a>Steuern von eingehendem Datenverkehr in eine App Service-Umgebung
## <a name="overview"></a>Übersicht
Eine App Service-Umgebung kann **entweder** in einem virtuellen Netzwerk von Azure Resource Manager **oder** einem [virtuellen Netzwerk][virtualnetwork] des klassischen Bereitstellungsmodells erstellt werden.  Ein neues virtuelles Netzwerk und ein neues Subnetz können bei der Erstellung einer App Service-Umgebung definiert werden.  Alternativ kann eine App Service-Umgebung in einem bereits vorhandenen virtuellen Netzwerk und einem bereits vorhandenen Subnetz erstellt werden.  Infolge einer im Juni 2016 vorgenommenen Änderung können ASEs auch in virtuellen Netzwerken bereitgestellt werden, die entweder öffentliche Adressbereiche oder RFC1918-Adressräume (d.h. private Adressen) verwenden.  Weitere Informationen zum Erstellen einer App Service-Umgebung finden Sie unter [Erstellen einer App Service-Umgebung][HowToCreateAnAppServiceEnvironment].

Eine App Service-Umgebung muss immer innerhalb eines Subnetzes erstellt werden, da ein Subnetz eine Netzwerkgrenze bereitstellt, die zum Sperren von eingehendem Datenverkehr hinter Upstreamgeräten und -diensten verwendet werden kann. Auf diese Weise wird HTTP- und HTTPS-Datenverkehr nur von bestimmten Upstream-IP-Adressen akzeptiert.

Der ein- und ausgehende Netzwerkdatenverkehr in einem Subnetz wird anhand einer [Netzwerksicherheitsgruppe][NetworkSecurityGroups] gesteuert. Zur Steuerung des eingehenden Datenverkehrs müssen Netzwerksicherheitsregeln in einer Netzwerksicherheitsgruppe erstellt werden. Anschließend wird die Netzwerksicherheitsgruppe dem Subnetz mit der App Service-Umgebung zugewiesen.

Sobald eine Netzwerksicherheitsgruppe einem Subnetz zugewiesen wurde, wird der in den Apps in der App Service-Umgebung eingehende Datenverkehr abhängig von den Zulassungs- und Ablehnungsregeln, die in der Netzwerksicherheitsgruppe definiert wurden, entweder zugelassen oder blockiert.

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="inbound-network-ports-used-in-an-app-service-environment"></a>In einer App Service-Umgebung verwendete eingehende Netzwerkports
Bevor Sie den eingehenden Netzwerkdatenverkehr mithilfe einer Netzwerksicherheitsgruppe sperren, sollten Sie unbedingt die erforderlichen und optionalen Netzwerkports kennen, die von einer App Service-Umgebung verwendet werden.  Das versehentliche Ausschließen von Datenverkehr für bestimmte Ports kann zu einem Funktionalitätsverlust in einer App Service-Umgebung führen.

Im Folgenden sehen Sie eine Liste der Ports, die von einer App Service-Umgebung verwendet werden. Alle Ports haben den Typ **TCP**, sofern nichts anderes angegeben ist:

* 454:  **Erforderlicher Port**, der von der Azure-Infrastruktur für die Verwaltung und Wartung von App Service-Umgebungen über SSL verwendet wird.  Der Datenverkehr für diesen Port darf nicht blockiert werden.  Dieser Port ist immer an die öffentliche VIP-Adresse einer ASE gebunden.
* 455:  **Erforderlicher Port**, der von der Azure-Infrastruktur für die Verwaltung und Wartung von App Service-Umgebungen über SSL verwendet wird.  Der Datenverkehr für diesen Port darf nicht blockiert werden.  Dieser Port ist immer an die öffentliche VIP-Adresse einer ASE gebunden.
* 80:  Standardport für eingehenden HTTP-Datenverkehr in Apps, die in App Service-Plänen in einer App Service-Umgebung ausgeführt werden.  Dieser Port ist in einer für den internen Lastenausgleich geeigneten App Service-Umgebung an die Adresse der ASE für den internen Lastenausgleich gebunden.
* 443: Standardport für eingehenden SSL-Datenverkehr in Apps, die in App Service-Plänen in einer App Service-Umgebung ausgeführt werden.  Dieser Port ist in einer für den internen Lastenausgleich geeigneten App Service-Umgebung an die Adresse der ASE für den internen Lastenausgleich gebunden.
* 21:  Steuerungskanal für FTP.  Dieser Port kann sicher blockiert werden, wenn FTP nicht verwendet wird.  Dieser Port kann in einer für den internen Lastenausgleich geeigneten App Service-Umgebung an die Adresse der ASE für den internen Lastenausgleich gebunden werden.
* 990:  Steuerungskanal für FTPS.  Dieser Port kann sicher blockiert werden, wenn FTPS nicht verwendet wird.  Dieser Port kann in einer für den internen Lastenausgleich geeigneten App Service-Umgebung an die Adresse der ASE für den internen Lastenausgleich gebunden werden.
* 10001–10020: Datenkanäle für FTP.  Wie der Steuerungskanal können diese Ports sicher blockiert werden, wenn FTP nicht verwendet wird.  Dieser Port kann in einer für den internen Lastenausgleich geeigneten App Service-Umgebung an die Adresse der ASE für den internen Lastenausgleich gebunden werden.
* 4016: Wird zum Remotedebuggen in Visual Studio 2012 verwendet.  Dieser Port kann sicher blockiert werden, wenn die Funktion nicht verwendet wird.  Dieser Port ist in einer für den internen Lastenausgleich geeigneten App Service-Umgebung an die Adresse der ASE für den internen Lastenausgleich gebunden.
* 4018: Wird zum Remotedebuggen in Visual Studio 2013 verwendet.  Dieser Port kann sicher blockiert werden, wenn die Funktion nicht verwendet wird.  Dieser Port ist in einer für den internen Lastenausgleich geeigneten App Service-Umgebung an die Adresse der ASE für den internen Lastenausgleich gebunden.
* 4020: Wird zum Remotedebuggen in Visual Studio 2015 verwendet.  Dieser Port kann sicher blockiert werden, wenn die Funktion nicht verwendet wird.  Dieser Port ist in einer für den internen Lastenausgleich geeigneten App Service-Umgebung an die Adresse der ASE für den internen Lastenausgleich gebunden.

## <a name="outbound-connectivity-and-dns-requirements"></a>Ausgehende Verbindungen und DNS-Anforderungen
Damit eine App Service-Umgebung richtig funktioniert, ist auch ausgehender Zugriff auf verschiedene Endpunkte erforderlich. Eine vollständige Liste externer Endpunkte, die von einer ASE verwendet werden, befindet sich im Artikel [Netzwerkkonfiguration für ExpressRoute](app-service-app-service-environment-network-configuration-expressroute.md#required-network-connectivity) im Abschnitt „Erforderliche Netzwerkverbindung“.

App Service-Umgebungen erfordern zudem eine gültige DNS-Infrastruktur, die für das virtuelle Netzwerk konfiguriert ist.  Falls die DNS-Konfiguration nach der Erstellung einer App Service-Umgebung geändert wird, können Entwickler erzwingen, dass eine App Service-Umgebung die neue DNS-Konfiguration übernimmt.  Wird im [Azure-Portal][NewPortal] über das Symbol „Neu starten“ oben auf dem Verwaltungsblatt der App Service-Umgebung ein paralleler Neustart der Umgebung ausgelöst, übernimmt diese die neue DNS-Konfiguration.

Es empfiehlt sich auch, benutzerdefinierte DNS-Server im VNet vor dem Erstellen einer App-Service-Umgebung einzurichten.  Wenn die DNS-Konfiguration eines virtuellen Netzwerks geändert wird, während eine App Service-Umgebung erstellt wird, misslingt das Erstellen der App Service-Umgebung.  Wenn ein benutzerdefinierter DNS-Server am anderen Ende eines VPN-Gateways vorhanden ist und der DNS-Server nicht erreichbar oder nicht verfügbar ist, kommt es ebenso zu einem Fehler beim Erstellen der App-Service-Umgebung.

## <a name="creating-a-network-security-group"></a>Erstellen einer Netzwerksicherheitsgruppe
Einzelheiten zur Funktionsweise von Netzwerksicherheitsgruppen finden Sie in den folgenden [Informationen][NetworkSecurityGroups].  Das Azure-Dienstverwaltungsbeispiel unten geht kurz auf die wichtigsten Aspekte von Netzwerksicherheitsgruppen ein. Dabei liegt der Schwerpunkt auf dem Konfigurieren und Anwenden einer Netzwerksicherheitsgruppe auf ein Subnetz, das eine App Service-Umgebung enthält.

**Hinweis:** Netzwerksicherheitsgruppen können im [Azure-Portal](https://portal.azure.com) oder über Azure PowerShell grafisch konfiguriert werden.

Netzwerksicherheitsgruppen werden zuerst als eigenständige Entität erstellt, die einem Abonnement zugeordnet ist. Da Netzwerksicherheitsgruppen in einer Azure-Region erstellt werden, stellen Sie sicher, dass die Netzwerksicherheitsgruppe in derselben Region wie die App Service-Umgebung erstellt wird.

Das folgende Beispiel veranschaulicht das Erstellen einer Netzwerksicherheitsgruppe:

    New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"

Sobald eine Netzwerksicherheitsgruppe erstellt wurde, werden ihr eine oder mehrere Netzwerksicherheitsregeln hinzugefügt.  Da der Regelsatz sich mit der Zeit ändern kann, wird empfohlen, in dem für Regelprioritäten verwendeten Nummerierungsschema Abstände einzuräumen, damit zusätzliche Regeln leicht eingefügt werden können.

Das folgende Beispiel zeigt eine Regel, die explizit Zugriff auf die Verwaltungsports gewährt, die von der Azure-Infrastruktur für die Verwaltung und Wartung einer App Service-Umgebung benötigt werden.  Beachten Sie, dass der gesamte Verwaltungsdatenverkehr über SSL verläuft und durch Clientzertifikate gesichert wird. Selbst wenn die Ports geöffnet werden, sind sie für keine Entität außer für die Azure-Verwaltungsinfrastruktur zugänglich.

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "ALLOW AzureMngmt" -Type Inbound -Priority 100 -Action Allow -SourceAddressPrefix 'INTERNET'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '454-455' -Protocol TCP


Wenn Sie den Zugriff auf Port 80 und 443 sperren, um eine App Service-Umgebung hinter Upstreamgeräten oder -diensten "auszublenden", müssen Sie die Upstream-IP-Adresse kennen.  Wenn Sie beispielsweise eine Web Application Firewall (WAF) verwenden, besitzt die WAF eine eigene IP-Adresse (oder mehrere Adressen) zum Leiten von Datenverkehr über einen Proxy an eine Downstream-App Service-Umgebung.  Diese IP-Adresse müssen Sie im Parameter *SourceAddressPrefix* einer Netzwerksicherheitsregel verwenden.

Im folgenden Beispiel wird der eingehende Datenverkehr von einer bestimmten Upstream-IP-Adresse explizit zugelassen.  Die Adresse *1.2.3.4* dient als Platzhalter für die IP-Adresse einer Upstream-WAF.  Ändern Sie den Wert in die Adresse, die von Ihrem Upstreamgerät oder -dienst verwendet wird.

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT HTTP" -Type Inbound -Priority 200 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT HTTPS" -Type Inbound -Priority 300 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

Wenn FTP-Unterstützung gewünscht wird, können die folgenden Regeln als Vorlage für das Erteilen des Zugriffs auf den FTP-Steuerungsport und die Datenkanalports verwendet werden.  Da es sich bei FTP um ein zustandsbehaftetes Protokoll handelt, können Sie FTP-Datenverkehr möglicherweise nicht über eine herkömmliche HTTP/HTTPS-Firewall oder ein Proxygerät leiten.  In diesem Fall müssen Sie das *SourceAddressPrefix* auf einen anderen Wert festlegen, z.B. den IP-Adressbereich von Entwicklungs- oder Bereitstellungscomputern, auf denen FTP-Clients ausgeführt werden. 

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT FTPCtrl" -Type Inbound -Priority 400 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '21' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT FTPDataRange" -Type Inbound -Priority 500 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '10001-10020' -Protocol TCP

(**Hinweis:** Der Datenkanal-Portbereich kann sich während des Vorschauzeitraums unter Umständen ändern.)

Für das Remotedebuggen mit Visual Studio wird das Erteilen des Zugriffs durch die folgenden Regeln veranschaulicht.  Es gibt für jede unterstützte Version von Visual Studio eine separate Regel, da jede Version einen anderen Port für das Remotedebuggen verwendet.  Wie beim FTP-Zugriff fließt der Datenverkehr für das Remotedebuggen möglicherweise nicht ordnungsgemäß durch eine herkömmliche WAF oder ein Proxygerät.  Das *SourceAddressPrefix* kann stattdessen auf den IP-Adressbereich von Entwicklungscomputern mit Visual Studio festgelegt werden.

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2012" -Type Inbound -Priority 600 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4016' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2013" -Type Inbound -Priority 700 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4018' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2015" -Type Inbound -Priority 800 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4020' -Protocol TCP

## <a name="assigning-a-network-security-group-to-a-subnet"></a>Zuweisen einer Netzwerksicherheitsgruppe zu einem Subnetz
Eine Netzwerksicherheitsgruppe verfügt über eine Standardsicherheitsregel, die den Zugriff auf den gesamten externen Datenverkehr verweigert.  Die Kombination der oben beschriebenen Netzwerksicherheitsregeln und der Standardsicherheitsregel zum Blockieren des eingehenden Datenverkehrs hat zur Folge, dass nur der Datenverkehr aus den Quelladressbereichen, die einer *Zulassen* -Aktion zugeordnet sind, Datenverkehr an Apps senden kann, die in einer App Service-Umgebung ausgeführt werden.

Nachdem eine Netzwerksicherheitsgruppe mit Sicherheitsregeln aufgefüllt wurde, muss sie dem Subnetz mit der App Service-Umgebung zugewiesen werden.  Der Befehl für die Zuordnung verweist sowohl auf den Namen des virtuellen Netzwerks, in dem sich die App Service-Umgebung befindet, als auch auf den Namen des Subnetzes, in dem die App Service-Umgebung erstellt wurde.  

Das folgende Beispiel zeigt eine Netzwerksicherheitsgruppe, die einem Subnetz und einem virtuellen Netzwerk zugewiesen wird:

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-test'

Nach der erfolgreichen Zuweisung der Netzwerksicherheitsgruppe (die Zuweisung ist ein längerer Vorgang und kann einige Minuten dauern) erreicht nur derjenige eingehende Datenverkehr die Apps in der App Service-Umgebung, der den *Zulassen* -Regeln entspricht.

Das folgende Beispiel zeigt der Vollständigkeit halber das Entfernen und somit das Trennen der Netzwerksicherheitsgruppe vom Subnetz:

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Remove-AzureNetworkSecurityGroupFromSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-test'

## <a name="special-considerations-for-explicit-ip-ssl"></a>Besonderheiten für explizites IP-SSL
Wenn eine App mit einer expliziten SSL-IP-Adresse (anwendbar *ausschließlich* auf ASEs, die nur eine öffentliche VIP-Adresse besitzen) und nicht mit der Standard-IP-Adresse der App Service-Umgebung konfiguriert wird, fließt sowohl HTTP- als auch HTTPS-Datenverkehr über einen anderen Satz von Ports (nicht über Port 80 und 443) in das Subnetz.

Die von jeder IP-SSL-Adresse verwendeten einzelnen Portspaare finden Sie in der Portalbenutzeroberfläche über das Details-UX-Blatt der App Service-Umgebung.  Wählen Sie „Alle Einstellungen“ > „IP-Adressen“ aus.  Das Blatt „IP-Adressen“ enthält eine Tabelle aller explizit konfigurierten IP-SSL-Adressen für die App Service-Umgebung sowie das Portpaar, mit dem HTTP- und HTTPS-Datenverkehr für jede IP-SSL-Adresse weitergeleitet wird.  Dieses Portpaar muss beim Konfigurieren von Regeln in einer Netzwerksicherheitsgruppe für den DestinationPortRange-Parameter verwendet werden.

Wenn eine App in einer ASE für die IP-SSL-Adresse konfiguriert ist, wird die spezielle Portpaarzuordnung externen Kunden nicht angezeigt, und sie müssen sich darüber keine Gedanken machen.  Der Datenverkehr zu den Apps fließt normal an die konfigurierte IP-SSL-Adresse.  Die Übersetzung in das spezielle Portpaar geschieht automatisch intern während des letzten Abschnitts des Datenverkehrsroutings in das Subnetz, das die ASE enthält. 

## <a name="getting-started"></a>Erste Schritte
Informationen zum Einstieg in App Service-Umgebungen finden Sie unter [Einführung in die App Service-Umgebung][IntroToAppServiceEnvironment].

Einzelheiten zum Herstellen einer sicheren Verbindung von Apps mit der Back-End-Ressource von einer App Service-Umgebung aus finden Sie unter [Sicheres Verbinden mit Back-End-Ressourcen von einer App Service-Umgebung aus][SecurelyConnecttoBackend].

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[HowToCreateAnAppServiceEnvironment]: app-service-web-how-to-create-an-app-service-environment.md
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[IntroToAppServiceEnvironment]:  app-service-app-service-environment-intro.md
[SecurelyConnecttoBackend]:  app-service-app-service-environment-securely-connecting-to-backend-resources.md
[NewPortal]:  https://portal.azure.com  

<!-- IMAGES -->

