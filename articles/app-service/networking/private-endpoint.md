---
title: Herstellen einer privaten Verbindung mit einer Azure-Web-App mithilfe eines privaten Endpunkts
description: Herstellen einer privaten Verbindung mit einer Web-App mithilfe eines privaten Azure-Endpunkts
author: ericgre
ms.assetid: 2dceac28-1ba6-4904-a15d-9e91d5ee162c
ms.topic: article
ms.date: 10/09/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.custom: fasttrack-edit, references_regions
ms.openlocfilehash: bebc7dcbc18a25b0d6d0761a8ca3ac476e83e581
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96183038"
---
# <a name="using-private-endpoints-for-azure-web-app"></a>Verwenden von Private Endpoint für eine Azure-Web-App

> [!IMPORTANT]
> Private Endpoint ist für Windows- und Linux-Web-Apps ggf. in Containern verfügbar und wird unter den folgenden App Service-Plänen gehostet: **Isolated**, **PremiumV2**, **PremiumV3**, **Functions Premium** (gelegentlich als elastischer Premium-Plan bezeichnet). 

Sie können private Endpunkte für Ihre Azure-Web-App verwenden, um Clients in Ihrem privaten Netzwerk den sicheren Zugriff auf die App über Private Link zu ermöglichen. Der private Endpunkt verwendet eine IP-Adresse aus dem Adressraum Ihres virtuellen Azure-Netzwerks. Der Netzwerkdatenverkehr zwischen einem Client in Ihrem privaten Netzwerk und der Web-App wird über das virtuelle Netzwerk und Private Link im Microsoft-Backbonenetzwerk geleitet, sodass keine Verfügbarmachung im öffentlichen Internet erfolgt.

Die Verwendung privater Endpunkte für Ihre Web-App bietet Ihnen folgende Möglichkeiten:

- Schützen Ihrer Web-App durch Konfigurieren des privaten Endpunkts, wodurch eine öffentliche Verfügbarmachung beseitigt wird.
- Sicheres Verbinden mit der Web-App aus lokalen Netzwerken, die eine Verbindung mit dem virtuellen Netzwerk über VPN oder ExpressRoute mit privatem Peering herstellen.
- Vermeiden Sie die Exfiltration von Daten aus Ihrem VNet. 

Wenn Sie nur eine sichere Verbindung zwischen Ihrem virtuellen Netzwerk und Ihrer Web-App benötigen, ist ein Dienstendpunkt die einfachste Lösung. Wenn Sie auch aus einem lokalen Netz über ein Azure-Gateway auf die Web-App zugreifen können müssen, ist ein privater Endpunkt mit einem VNet mit regionalem Peering oder mit globalem Peering die Lösung.  

Weitere Informationen finden Sie unter [Dienstendpunkte][serviceendpoint].

## <a name="conceptual-overview"></a>Konzeptionelle Übersicht

Ein privater Endpunkt ist eine spezielle Netzwerkschnittstelle (NIC, Netzwerkadapter) für Ihre Azure-Web-App in einem Subnetz in Ihrem virtuellen Netzwerk (VNet).
Wenn Sie einen privaten Endpunkt für Ihre Web-App erstellen, stellt dieser eine sichere Verbindung zwischen Clients in Ihrem privaten Netzwerk und Ihrer Web-App bereit. Dem privaten Endpunkt wird eine IP-Adresse aus dem IP-Adressbereich Ihres VNet zugewiesen.
Für die Verbindung zwischen dem privaten Endpunkt und der Web-App wird ein sicherer [Private Link][privatelink] verwendet. Der private Endpunkt wird nur für eingehende Flows in Ihre Web-App verwendet. Ausgehende Flows verwenden diesen privaten Endpunkt nicht, aber Sie können ausgehende Flows über die [VNet-Integrationsfunktion][vnetintegrationfeature] in Ihr Netzwerk in einem anderen Subnetz einfügen.

Das Subnetz, in dem Sie den privaten Endpunkt einbinden, kann über andere Ressourcen verfügen. Sie benötigen also kein dediziertes leeres Subnetz.
Sie können den privaten Endpunkt auch in einer anderen Region als der der Web-App bereitstellen. 

> [!Note]
>Die VNet-Integrationsfunktion kann nicht dasselbe Subnetz wie der private Endpunkt verwenden. Dies ist eine Einschränkung der VNet-Integrationsfunktion.

Unter dem Aspekt der Sicherheit:

- Wenn Sie private Endpunkte für Ihre Web-App aktivieren, deaktivieren Sie jeglichen öffentlichen Zugriff.
- Sie können mehrere private Endpunkte in anderen VNets und Subnetzen aktivieren, einschließlich VNets in anderen Regionen.
- Die IP-Adresse der NIC des privaten Endpunkts muss dynamisch sein, bleibt jedoch unverändert, bis Sie den privaten Endpunkt löschen.
- Der NIC des privaten Endpunkts kann keine NSG zugeordnet sein.
- Dem Subnetz, das den privaten Endpunkt hostet, kann eine Netzwerksicherheitsgruppe zugeordnet sein, aber Sie müssen die Erzwingung von Netzwerkrichtlinien für den privaten Endpunkt deaktivieren. Weitere Informationen hierzu finden Sie unter [Deaktivieren von Netzwerkrichtlinien für private Endpunkte][disablesecuritype]. Als Resultat hieraus können Sie den Zugriff auf den privaten Endpunkt nach keiner Netzwerksicherheitsgruppe filtern.
- Wenn Sie einen privaten Endpunkt für Ihre Web-App aktivieren, wird die Konfiguration der [Zugriffsbeschränkungen][accessrestrictions] der Web-App nicht ausgewertet.
- Sie können das Risiko der Datenexfiltration aus dem VNet beseitigen, indem Sie alle NSG-Regeln entfernen, in denen das Zieltag das Internet oder Azure-Dienste sind. Wenn Sie einen privaten Endpunkt für eine Web-App bereitstellen, können Sie diese spezifische Web-App nur über den privaten Endpunkt erreichen. Wenn Sie über eine andere Web-App verfügen, müssen Sie einen weiteren dedizierten privaten Endpunkt für diese andere Web-App bereitstellen.

In den Web-HTTP-Protokollen Ihrer Web-App finden Sie die Clientquell-IP. Dieses Feature wird mithilfe des TCP-Proxyprotokolls implementiert, das die Client-IP-Eigenschaft an die Web-App weiterleitet. Weitere Informationen finden Sie unter [Abrufen von Verbindungsinformationen mithilfe von TCP-Proxy v2][tcpproxy].


  > [!div class="mx-imgBorder"]
  > ![Privater Web-App-Endpunkt – globale Übersicht](media/private-endpoint/global-schema-web-app.png)

## <a name="dns"></a>DNS

Wenn Sie einen privaten Endpunkt für die Web-App verwenden, muss die angeforderte URL dem Namen Ihrer Web-App entsprechen. Der Standardname lautet mywebappname.azurewebsites.net.

Standardmäßig ist der öffentliche Name Ihrer Web-App ohne einen privaten Endpunkt ein kanonischer Name für den Cluster.
Der Name wird beispielsweise wie folgt aufgelöst:

|Name |type |Wert |
|-----|-----|------|
|mywebapp.azurewebsites.net|CNAME|clustername.azurewebsites.windows.net|
|clustername.azurewebsites.windows.net|CNAME|cloudservicename.cloudapp.net|
|cloudservicename.cloudapp.net|Ein|40.122.110.154| 


Wenn Sie einen privaten Endpunkt bereitstellen, wird der DNS-Eintrag so aktualisiert, dass er auf den kanonischen Namen mywebapp.privatelink.azurewebsites.net verweist.
Der Name wird beispielsweise wie folgt aufgelöst:

|Name |type |Wert |Anmerkung |
|-----|-----|------|-------|
|mywebapp.azurewebsites.net|CNAME|mywebapp.privatelink.azurewebsites.net|
|mywebapp.privatelink.azurewebsites.net|CNAME|clustername.azurewebsites.windows.net|
|clustername.azurewebsites.windows.net|CNAME|cloudservicename.cloudapp.net|
|cloudservicename.cloudapp.net|Ein|40.122.110.154|<– Diese öffentliche IP-Adresse ist nicht der private Endpunkt, führt zu einem Fehler 403|

Sie müssen einen privaten DNS-Server oder eine private Azure DNS-Zone einrichten. Für Tests können Sie den Hosteintrag des Testcomputers ändern.
Die DNS-Zone, die Sie erstellen müssen, ist: **privatelink.azurewebsites.net**. Registrieren Sie den Eintrag für Ihre Web-App mit einem A-Eintrag und der IP des privaten Endpunkts.
Der Name wird beispielsweise wie folgt aufgelöst:

|Name |type |Wert |Anmerkung |
|-----|-----|------|-------|
|mywebapp.azurewebsites.net|CNAME|mywebapp.privatelink.azurewebsites.net|<--Azure erstellt diesen Eintrag im öffentlichen Azure-DNS, um die App auf die private Verbindung zu verweisen. Dies wird von uns verwaltet.|
|mywebapp.privatelink.azurewebsites.net|Ein|10.10.10.8|<– Sie verwalten diesen Eintrag in Ihrem DNS-System, sodass er auf die IP-Adresse Ihres privaten Endpunkts verweist|

Nachdem Sie diese DNS-Konfiguration vorgenommen haben, können Sie Ihre Web-App privat unter dem Standardnamen mywebappname.azurewebsites.net erreichen. Dieser Name muss verwendet werden, da das Standardzertifikat für „*.azurewebsites.net“ ausgestellt wird.


Wenn Sie einen benutzerdefinierten DNS-Namen verwenden müssen, müssen Sie den benutzerdefinierten Namen in Ihrer Web-App hinzufügen. Der benutzerdefinierte Name muss wie jeder andere benutzerdefinierte Name mithilfe der öffentlichen DNS-Auflösung überprüft werden. Weitere Informationen finden Sie unter [Benutzerdefinierte DNS-Validierung][dnsvalidation].

Für die Kudu-Konsole oder die Kudu-REST-API (z. B. zusammen mit selbstgehosteten DevOps-Agents bereitgestellt) müssen Sie zwei Einträge in Ihrer privaten Azure DNS-Zone oder auf Ihrem benutzerdefinierten DNS-Server erstellen. 

| Name | type | Wert |
|-----|-----|-----|
| mywebapp.privatelink.azurewebsites.net | Ein | PrivateEndpointIP | 
| mywebapp.scm.privatelink.azurewebsites.net | Ein | PrivateEndpointIP | 



## <a name="pricing"></a>Preise

Ausführliche Preisinformationen finden Sie unter [Azure Private Link – Preise][pricing].

## <a name="limitations"></a>Einschränkungen

Wenn Sie die Azure Functions in einem elastischen Premium-Plan mit einem privaten Endpunkt verwenden, um die Funktion im Azure-Webportal auszuführen, benötigen Sie direkten Netzwerkzugriff, oder Sie erhalten einen HTTP 403-Fehler. Anders ausgedrückt, muss Ihr Browser den privaten Endpunkt erreichen können, um die Funktion aus dem Azure-Webportal ausführen zu können. 

Sie können bis zu 100 Private Endpoint-Instanzen mit einer bestimmten Web-App verbinden.

Für Einschubfächer kann Private Endpoint nicht verwendet werden.

Die Funktion „Remotedebuggen“ ist nicht verfügbar, wenn Private Endpoint für die Web-App aktiviert wurde. Daher wird empfohlen, den Code für ein Einschubfach bereitzustellen und dort ein Remotedebugging durchzuführen.

Wir verbessern regelmäßig die Funktionen private Verbindung (Private Link) und privater Endpunkt. Aktuelle Informationen zu Einschränkungen finden Sie in [diesem Artikel][pllimitations].

## <a name="next-steps"></a>Nächste Schritte

- Informationen zum Bereitstellen des privaten Endpunkts für Ihre Web-App über das Portal finden Sie unter [Herstellen einer privaten Verbindung mit einer Web-App mithilfe eines privaten Azure-Endpunkts (Vorschau)][howtoguide1].
- Informationen zum Bereitstellen des privaten Endpunkts für Ihre Web-App über die Azure CLI finden Sie unter [Erstellen einer App Service-App und Bereitstellen eines privaten Endpunkts mithilfe der Azure-Befehlszeilenschnittstelle][howtoguide2].
- Informationen zum Bereitstellen des privaten Endpunkts für Ihre Web-App mithilfe von PowerShell finden Sie unter [Erstellen einer App Service-App und Bereitstellen eines privaten Endpunkts mithilfe von PowerShell][howtoguide3].
- Informationen zum Bereitstellen des privaten Endpunkts für Ihre Web-App über eine Azure-Vorlage finden Sie unter [Erstellen einer App Service-App und Bereitstellen eines privaten Endpunkts mithilfe einer Azure Resource Manager-Vorlage][howtoguide4].
- Ein End-to-End-Beispiel, wie Sie eine Front-End-Web-App mit einer gesicherten Back-End-Web-App mittels VNet-Einfügung und privatem Endpunkt mit ARM-Vorlage verbinden, finden Sie in dieser [Schnellstartanleitung][howtoguide5]
- Ein End-to-End-Beispiel, wie Sie eine Front-End-Web-App mit einer gesicherten Back-End-Web-App mittels VNet-Einfügung und privatem Endpunkt mit terraform verbinden, finden Sie in diesem [Beispiel][howtoguide6]


<!--Links-->
[serviceendpoint]: ../../virtual-network/virtual-network-service-endpoints-overview.md
[privatelink]: ../../private-link/private-link-overview.md
[vnetintegrationfeature]: ../web-sites-integrate-with-vnet.md
[disablesecuritype]: ../../private-link/disable-private-endpoint-network-policy.md
[accessrestrictions]: ../app-service-ip-restrictions.md
[tcpproxy]: ../../private-link/private-link-service-overview.md#getting-connection-information-using-tcp-proxy-v2
[dnsvalidation]: ../app-service-web-tutorial-custom-domain.md
[pllimitations]: ../../private-link/private-endpoint-overview.md#limitations
[pricing]: https://azure.microsoft.com/pricing/details/private-link/
[howtoguide1]: ../../private-link/tutorial-private-endpoint-webapp-portal.md
[howtoguide2]: ../scripts/cli-deploy-privateendpoint.md
[howtoguide3]: ../scripts/powershell-deploy-private-endpoint.md
[howtoguide4]: ../scripts/template-deploy-private-endpoint.md
[howtoguide5]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-privateendpoint-vnet-injection
[howtoguide6]: ../scripts/terraform-secure-backend-frontend.md