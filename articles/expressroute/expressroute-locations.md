---
title: 'Konnektivitätsanbieter und Standorte: Azure ExpressRoute | Microsoft-Dokumentation'
description: Dieser Artikel enthält eine ausführliche Übersicht über die Standorte, an denen Dienste angeboten werden, sowie Informationen darüber, wie Sie eine Verbindung mit den Azure-Regionen herstellen können. Sortiert nach Konnektivitätsanbieter.
services: expressroute
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
ms.assetid: c878513a-d594-42ad-8b0e-403efd0c4b25
ms.service: expressroute
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/31/2019
ms.author: jaredr80
ms.openlocfilehash: 96413d08edd18693fb2d04e49abebad6ed505485
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66478137"
---
# <a name="expressroute-partners-and-peering-locations"></a>ExpressRoute-Partner und Peeringstandorte

> [!div class="op_single_selector"]
> * [Standorte nach Anbieter](expressroute-locations.md)
> * [Anbieter nach Standort](expressroute-locations-providers.md)


In den Tabellen in diesem Artikel finden Sie Informationen zu ExpressRoute-Konnektivitätsanbietern, zum geografischen Geltungsbereich von ExpressRoute, zu Microsoft-Clouddiensten, die über ExpressRoute unterstützt werden, und zu ExpressRoute-Systemintegratoren (SIs).

## <a name="partners"></a>ExpressRoute-Konnektivitätsanbieter
ExpressRoute wird in allen Azure-Regionen und an allen Standorten unterstützt. Die folgende Karte zeigt die Azure-Regionen und ExpressRoute-Standorte. ExpressRoute-Standorte beziehen sich auf jene Orte, an denen Microsoft eine Peeringkooperation mit mehreren Service Providern bietet.

![Standortkarte][0]

Wenn Sie mit mindestens einem ExpressRoute-Standort innerhalb der geopolitischen Region eine Verbindung hergestellt haben, haben Sie Zugriff auf die Azure-Dienste in allen Regionen dieser geopolitischen Region.

### <a name="azure-regions-to-expressroute-locations-within-a-geopolitical-region"></a>Azure-Regionen mit ExpressRoute-Standorten in einer geopolitischen Region
Die folgende Tabelle bietet eine Übersicht über die Azure-Regionen mit ExpressRoute-Standorten in einer geopolitischen Region.

| **Geopolitische Region** | **Zone** | **Azure-Regionen** | **ExpressRoute-Standorte** |
| --- | --- | --- | --- |
| **Australische Behörden** | 1 | Australien, Mitte; Australien, Mitte 2 |Canberra, Canberra2 |
| **Europa** | 1 |„Frankreich, Mitte“, „Frankreich, Süden“, „Europa, Norden“, „Europa, Westen“, „Vereinigtes Königreich, Westen“, „Vereinigtes Königreich, Süden“ |Amsterdam, Amsterdam2, Dublin, Frankfurt, London, London2, Marseille, Newport(Wales), Paris, Zürich |
| **Nordamerika** | 1 |USA, Osten; USA, Westen; USA, Osten 2; USA, Westen 2; USA, Mitte; USA, Süden-Mitte; USA, Norden-Mitte; USA, Westen-Mitte; Kanada, Mitte; Kanada, Osten |Atlanta, Chicago, Dallas, Denver, Las Vegas, Los Angeles, Miami, New York, San Antonio, Seattle, Silicon Valley, Silicon Valley2, Washington DC, Washington DC2, Montreal, Quebec City, Toronto |
| **Asien** | 2 |Ostasien, Südostasien |Hongkong (SAR), Kuala Lumpur, Singapur, Singapur2, Taipeh |
| **Australien** | 2 |Südostaustralien, Ostaustralien |Melbourne, Perth, Sydney | 
| **Indien** | 2 |Indien, Westen, Indien, Mitte, Indien, Süden |Chennai, Chennai2, Mumbai, Mumbai2 |
| **Japan** | 2 |Japan West, Japan Ost |Osaka, Tokio |
| **Südkorea** | 2 |Korea, Mitte, Korea, Süden |Busan, Seoul|
| **Vereinigte Arabische Emirate** | 3 | VAE, Mitte; VAE, Norden | Dubai, Dubai2 |
| **Südafrika** | 3 |„Südafrika, Westen“, „Südafrika, Norden“ |Kapstadt, Johannesburg |
| **Südamerika** | 3 |Brasilien Süd |Sao Paulo |


### <a name="regions-and-geopolitical-boundaries-for-national-clouds"></a>Regionen und geopolitische Grenzen für nationale Clouds
In der folgenden Tabelle finden Sie Informationen zu Regionen und geopolitischen Grenzen für nationale Clouds.

| **Geopolitische Region** | **Azure-Regionen** | **ExpressRoute-Standorte** |
| --- | --- | --- |
| **US-Government Cloud** |„US Gov Arizona“, „US Gov Iowa“, „US Gov Texas“, „US Gov Virginia“, „US DoD, Mitte“, „US DoD, Osten“  |Chicago, Dallas, New York, Phoenix, San Antonio, Seattle, Silicon Valley, Washington DC |
| **China, Osten** |China, Osten; China, Osten2 |Shanghai, Shanghai2 |
| **China, Norden** |China, Norden; China, Norden2 |Peking, Peking2 |
| **Deutschland** |Deutschland, Mitte, Deutschland, Ost |Berlin, Frankfurt |

Konnektivität zwischen geopolitischen Regionen wird bei der ExpressRoute-Standard-SKU nicht unterstützt. Sie müssen das ExpressRoute Premium-Add-On aktivieren, um Unterstützung für globale Konnektivität zu erhalten. Verbindungen mit nationalen Cloudumgebungen werden nicht unterstützt. Wenden Sie sich an Ihren Konnektivitätsanbieter, wenn Sie derartige Verbindungen implementieren möchten.

## <a name="locations"></a>Standorte von Konnektivitätsanbietern

Die folgende Tabelle enthält die Standorte nach Service Provider. [Hier](expressroute-locations-providers.md#locations) finden Sie eine Tabelle, die nach den verfügbaren Service Providern mit den jeweiligen Standorten sortiert ist.


### <a name="production-azure"></a>Azure-Produktionsumgebungen

| **Service Provider** | **Microsoft Azure** | **Office 365 und Dynamics 365** | **Standorte** |
| --- | --- | --- | --- |
| **[AARNet](https://www.aarnet.edu.au/network-and-services/cloud-services-applications/azure-expressroute/)** |Unterstützt |Unterstützt |Melbourne, Sydney |
| **[Airtel](https://www.airtel.in/creatingsmiles/)** | Unterstützt | Unterstützt | Chennai2, Mumbai2 |
| **[Aryaka Networks](https://www.aryaka.com/)** |Unterstützt |Unterstützt |Amsterdam, Chicago, Dallas, Hongkong (SAR), São Paulo, Seattle, Silicon Valley, Singapur, Tokio, Washington DC |
| **[Ascenty-Rechenzentren](https://ascenty.com/servicos/cloud-connect/microsoft-expressroute/)** |Unterstützt |Unterstützt |Sao Paulo |
| **[AT&amp;T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** |Unterstützt |Unterstützt |Amsterdam, Chicago, Dallas, London, Silicon Valley, Singapur, Sydney, Tokio, Toronto, Washington DC |
| **[Bell Canada](https://business.bell.ca/shop/enterprise/cloud-connect-access-to-cloud-partner-services)** |Unterstützt |Unterstützt |Montreal, Toronto, Quebec City |
| **[British Telecom](https://www.globalservices.bt.com/en/solutions/products/bt-compute-for-microsoft-azure)** |Unterstützt |Unterstützt |Amsterdam, Hongkong (SAR), London, Newport (Wales), São Paulo, Silicon Valley, Singapur, Sydney, Tokio, Washington DC |
| **C3ntro** |In Kürze verfügbar |In Kürze verfügbar |Miami |
| **CDC** | Unterstützt | Unterstützt | Canberra, Canberra2 |
| **[CenturyLink Cloud Connect](https://www.centurylink.com/cloudconnect)** |Unterstützt |Unterstützt |Las Vegas, New York, San Antonio, Silicon Valley, Tokio, Toronto |
| **Chief Telecom** |Unterstützt |Unterstützt |Taipeh |
| **China Telecom Global** |Unterstützt |Nicht unterstützt |Hongkong (SAR) |
| **[Cologix](https://www.cologix.com/solutions/cloud-connect/public-clouds/microsoft-cloud/)** |Unterstützt |Unterstützt |Chicago, Dallas, Montreal, Toronto, Washington DC |
| **[Colt](https://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** |Unterstützt |Unterstützt |Amsterdam, Dublin, London, Newport, Paris, Singapur2, Tokio |
| **[Comcast](https://business.comcast.com/landingpage/microsoft-azure)** |Unterstützt |Unterstützt |Chicago, Silicon Valley, Washington, D.C. |
| **[CoreSite](https://www.coresite.com/solutions/cloud-services/public-cloud-providers/microsoft-azure-expressroute)** |Unterstützt |Unterstützt |Chicago, Denver, Los Angeles, New York, Silicon Valley, Silicon Valley2, Washington DC, Washington DC2 |
| **[DE-CIX](https://www.de-cix.net/en/de-cix-service-world/directcloud/find-a-cloud-service/detail/microsoft-azure)** | Unterstützt |Unterstützt |Amsterdam2, Frankfurt|
| **Devoli** | Unterstützt |Unterstützt | Melbourne, Sydney |
| **du datamena** |Unterstützt |Unterstützt | Dubai2 |
| **eir** |Unterstützt |Unterstützt |Dublin|
| **[Epsilon Global Communications](https://www.epsilontel.com/solutions/direct-cloud-connect)** |Unterstützt |Unterstützt |Singapur, Singapur2 |
| **[Equinix](https://www.equinix.com/partners/microsoft-azure/)** |Unterstützt |Unterstützt |Amsterdam, Atlanta, Chicago, Dallas, Dublin, Hongkong (SAR), London, London2, Los Angeles, Melbourne, Miami, New York, Osaka, Paris, São Paulo, Seattle, Silicon Valley, Singapur, Sydney, Tokio, Toronto, Washington DC |
| **Etisalat (VAE)** |Unterstützt |Unterstützt |Dubai|
| **[euNetworks](https://eunetworks.com/services/solutions/cloud-connect/microsoft-azure-expressroute/)** |Unterstützt |Unterstützt |Amsterdam, Dublin, London |
| **FarEasTone** |Unterstützt |Unterstützt |Taipeh|
| **GÉANT** |Unterstützt |Unterstützt |Amsterdam |
| **[Global Cloud Xchange (GCX)](https://globalcloudxchange.com/cloud-platform/cloud-x-fusion/)** | Unterstützt| Unterstützt | Chennai, Mumbai |
| **[InterCloud](https://www.intercloud.com/)** |Unterstützt |Unterstützt |Amsterdam, Chicago, London, New York, Paris, Silicon Valley, Singapore, Washington DC |
| **Internet2** |Unterstützt |Unterstützt |Chicago, Dallas, Washington DC |
| **[Internet Initiative Japan Inc. - IIJ](https://www.iij.ad.jp/en/news/pressrelease/2015/1216-2.html)** |Unterstützt |Unterstützt |Osaka, Tokio |
| **[Internet Solutions – Cloud Connect](https://www.is.co.za/solution/cloud-connect/)** |Unterstützt |Unterstützt |Kapstadt, Johannesburg, London |
| **[Interxion](https://www.interxion.com/why-interxion/colocate-with-the-clouds/Microsoft-Azure/)** |Unterstützt |Unterstützt |Amsterdam, Amsterdam2, Dublin, Frankfurt, London, Marseille, Paris, Zürich |
| **[IX Reach](https://www.ixreach.com/partners/cloud-partners/microsoft-azure/)**|Unterstützt |Unterstützt | Amsterdam, London2, Silicon Valley, Toronto |
| **[Jisc](https://www.jisc.ac.uk/microsoft-azure-expressroute)** |Unterstützt |Unterstützt |London |
| **[KINX](https://www.kinx.net/service/network/cloudhub/ms-expressroute/?lang=en)** |Unterstützt |Unterstützt |Seoul |
| **[Kordia](https://www.kordia.co.nz/cloudconnect)** | Unterstützt |Unterstützt |Sydney |
| **[KPN](https://www.kpn.com/zakelijk/cloud/connect.htm)** | Unterstützt | Unterstützt | Amsterdam | 
| **[Level 3 Communications](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** |Unterstützt |Unterstützt |Amsterdam, Chicago, Dallas, London, Newport (Wales), Sao Paulo, Seattle, Silicon Valley, Singapur, Washington DC |
| **LG CNS** |Unterstützt |Unterstützt |Busan, Seoul |
| **[Liquid Telecom](https://www.liquidtelecom.com/products-and-services/cloud.html)** |Unterstützt |Unterstützt |Kapstadt, Johannesburg |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Unterstützt |Unterstützt |Amsterdam, Atlanta, Chicago, Dallas, Denver, Dublin, Hongkong (SAR), Las Vegas, London, Los Angeles, Melbourne, Miami, New York, Perth, Quebec City, San Antonio, Seattle, Silicon Valley, Singapur, Singapur2, Sydney, Toronto, Washington DC |
| **[MTN](https://www.mtnbusiness.com/en/enterprise/Pages/microsoft-express-route.aspx)** |Unterstützt |Unterstützt |London |
| **[Neutrona Networks](http://www.neutrona.com/index.php/azure-expressroute/)** |Unterstützt |Unterstützt |Dallas, Los Angeles, Miami, Sao Paulo, Washington, D.C. |
| **[Daten der nächsten Generation](https://www.nextgenerationdata.co.uk/ngd-cloud-gateway/)** |Unterstützt |Unterstützt |Newport(Wales) |
| **[NEXTDC](https://www.nextdc.com/services/axon-ethernet/microsoft-expressroute)** |Unterstützt |Unterstützt |Melbourne, Perth, Sydney |
| **[NTT Communications](https://www.ntt.com/en/services/network/virtual-private-network.html)** |Unterstützt |Unterstützt |Amsterdam, Hongkong (SAR), London, Los Angeles, Osaka, Singapur, Sydney, Tokio, Washington DC |
| **[NTT EAST](https://flets.com/cloudgateway/crossconnect/)** |Unterstützt |Unterstützt |Tokio |
| **[NTT SmartConnect](https://cloud.nttsmc.com/cxc/azure.html)** |Unterstützt |Unterstützt |Osaka |
| **[Optus](https://www.optus.com.au/enterprise/networking/network-connectivity/express-link)** |Unterstützt |Unterstützt |Melbourne, Sydney |
| **[Orange](https://www.orange-business.com/en/products/business-vpn-galerie)** |Unterstützt |Unterstützt |Amsterdam, Hongkong (SAR), London, Paris, São Paulo, Silicon Valley, Singapur, Sydney, Tokio, Washington DC |
| **[Orixcom](https://www.orixcom.com/cloud-solutions/)** | Unterstützt | Unterstützt | Dubai2 |
| **[PacketFabric](https://www.packetfabric.com/packetcor/microsoft-azure/)** |Unterstützt |Unterstützt |Chicago, Silicon Valley, Washington, D.C. |
| **[PCCW Global Limited](https://consoleconnect.com/clouds/#azureRegions)** |Unterstützt |Unterstützt |Chicago, Hong Kong (SAR), London |
| **[Sejong Telecom](https://www.sejongtelecom.net/en/pages/service/cloud_ms)** |Unterstützt |Unterstützt |Seoul |
| **[SIFY](http://telecom.sify.com/azure-expressroute.html)** |Unterstützt |Unterstützt |Chennai, Mumbai2 |
| **[SingTel](http://info.singtel.com/about-us/news-releases/singtel-provide-secure-private-access-microsoft-azure-public-cloud)** |Unterstützt |Unterstützt |Singapur, Singapur2 |
| **[Softbank](https://www.softbank.jp/biz/cloud/cloud_access/direct_access_for_az/)** |Unterstützt |Unterstützt |Osaka, Tokio |
| **[Sprint](https://business.sprint.com/solutions/cloud-networking/)** |Unterstützt |Unterstützt |Chicago, Silicon Valley, Washington, D.C. |
| **[Tata Communications](https://www.tatacommunications.com/lp/izo/azure/azure_index.html)** |Unterstützt |Unterstützt |Amsterdam, Chennai, Hongkong (SAR), London, Mumbai, São Paulo, Silicon Valley, Singapur, Washington DC |
| **Telecity Group** |Unterstützt |Unterstützt |Amsterdam |
| **[Telefonica](https://www.business-solutions.telefonica.com/es/enterprise/solutions/efficient-infrastructure/managed-voice-data-connectivity/)** |Unterstützt |Unterstützt |Amsterdam, Sao Paulo |
| **[Telehouse – KDDI](https://www.telehouse.net/solutions/cloud-services/cloud-link)**  |Unterstützt |Unterstützt |London |
| **Telenor** |Unterstützt |Unterstützt |Amsterdam, London |
| **[Telia Carrier](https://teliacarrier.com/our-services/connectivity/cloud-connect.html?title=Cloud%20Connect)** | Unterstützt | Unterstützt |Amsterdam, Chicago, Dallas, London, Paris, Washington |
| **Telmex Uninet**| Unterstützt | Unterstützt | Dallas |
| **[Telstra Corporation](https://www.telstra.com.au/business-enterprise/network-services/networks/cloud-direct-connect/)** |Unterstützt |Unterstützt |Melbourne, Singapur, Sydney |
| **[Telus](https://www.telus.com)** |Unterstützt |Unterstützt |Montreal, Seattle, Toronto |
| **[Teraco](https://www.teraco.co.za/services/africa-cloud-exchange/)** |Unterstützt |Unterstützt |Kapstadt, Johannesburg |
| **[TIME dotCom](https://www.time.com.my/enterprise/connectivity/cloud-interconnect)** | Unterstützt | Unterstützt | Kuala Lumpur |
| **[Transtelco](https://transtelco.net/enterprise-services/)** |Unterstützt |Unterstützt |Dallas |
| **[UOLDIVEO](https://www.uoldiveo.com.br/)** |Unterstützt |Unterstützt |Sao Paulo |
| **[Verizon](https://enterprise.verizon.com/products/network/application-enablement/secure-cloud-interconnect/)** |Unterstützt |Unterstützt |Amsterdam, Chicago, Dallas, Hongkong (SAR), London, Mumbai, Silicon Valley, Singapur, Sydney, Tokio, Toronto, Washington DC |
| **[Vodafone](https://www.vodafone.com/business/global-enterprise/global-connectivity/vodafone-ip-vpn-cloud-connect)** |Unterstützt |Unterstützt |London, Singapur |
| **Vodafone Idea** | Unterstützt | Unterstützt | Mumbai, Mumbai2 |
| **[Zayo](https://www.zayo.com/solutions/industries/cloud-connectivity/microsoft-expressroute)** |Unterstützt |Unterstützt |Amsterdam, Chicago, Dallas, Denver, London, Los Angeles, Montreal, New York, Paris, Seattle, Silicon Valley, Toronto, Washington DC |

 **+** steht für "In Kürze"

### <a name="national-cloud-environment"></a>Nationale Cloudumgebungen

### <a name="us-government-cloud"></a>US-Government Cloud

| **Service Provider** | **Microsoft Azure** | **Office 365** | **Standorte** |
| --- | --- | --- | --- |
| **[AT&amp;T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** |Unterstützt |Unterstützt |Chicago, Phoenix, Washington DC |
| **[CenturyLink Cloud Connect](https://www.centurylink.com/cloudconnect)** |Unterstützt |Unterstützt |New York, Phoenix, San Antonio |
| **[Equinix](https://www.equinix.com/partners/microsoft-azure/)** |Unterstützt |Unterstützt |Chicago, Dallas, New York, Seattle, Silicon Valley, Washington DC |
| **[Level 3 Communications](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** |Unterstützt |Unterstützt |Chicago, Silicon Valley, Washington, D.C. |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Unterstützt | Unterstützt | Chicago, Dallas, San Antonio, Seattle, Washington DC |
| **[Verizon](http://news.verizonenterprise.com/2014/04/secure-cloud-interconnect-solutions-enterprise/)** |Unterstützt |Unterstützt |Chicago, Dallas, New York, Silicon Valley, Washington DC |

### <a name="china"></a>China

| **Service Provider** | **Microsoft Azure** | **Office 365** | **Standorte** |
| --- | --- | --- | --- |
| **China Telecom** |Unterstützt |Nicht unterstützt |Peking (Beijing), Peking2 (Beijing2), Shanghai, Shanghai2 |
| **[GDS](http://en.gds-services.com/news_detail/newsId=21.html)** |Unterstützt |Nicht unterstützt |Peking2, Shanghai2 |

Weitere Informationen finden Sie unter [ExpressRoute in China](http://www.windowsazure.cn/home/features/expressroute/).

### <a name="germany"></a>Deutschland

| **Service Provider** | **Microsoft Azure** | **Office 365** | **Standorte** |
| --- | --- | --- | --- |
| **[Colt](https://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** |Unterstützt |Nicht unterstützt |Frankfurt |
| **[Equinix](https://www.equinix.com/partners/microsoft-azure/)** |Unterstützt |Nicht unterstützt |Frankfurt |
| **[e-shelter](https://www.e-shelter.de/en/microsoft-expressroutetm)** |Unterstützt |Nicht unterstützt |Berlin |
| **Interxion** |Unterstützt |Nicht unterstützt |Frankfurt |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Unterstützt  | Nicht unterstützt | Berlin |
| **T-Systems** |Unterstützt |Nicht unterstützt |Berlin |

## <a name="connectivity-through-exchange-providers"></a>Konnektivität über Exchange-Anbieter

Wenn Ihr Konnektivitätsanbieter nicht in den vorherigen Abschnitten enthalten ist, können Sie dennoch eine Verbindung erstellen.

* Fragen Sie Ihren Konnektivitätsanbieter, ob eine Verbindung mit einem der Exchange-Standorte aus der vorherigen Tabelle bereitgestellt werden kann. Sie können auch die folgenden Links überprüfen, um weitere Informationen über die von den Exchange-Anbietern angebotenen Dienste zu erhalten. Viele Konnektivitätsanbieter sind bereits mit Ethernet-Exchanges verbunden.
  * [Cologix](https://www.cologix.com/)
  * [CoreSite](https://www.coresite.com/)
  * [Equinix Cloud Exchange](https://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
  * [Interxion](https://www.interxion.com/products/interconnection/cloud-connect/)
  * [IX Reach](https://www.ixreach.com/partners/cloud-partners/microsoft-azure/)
  * [Megaport](https://www.megaport.com/services/microsoft-expressroute/)
  * [NextDC](https://www.nextdc.com/)
  * [PacketFabric](https://www.packetfabric.com/packetcor/microsoft-azure/) 
* Bitten Sie Ihren Konnektivitätsanbieter, Ihr Netzwerk auf den Peeringstandort Ihrer Wahl zu erweitern.
  * Stellen Sie sicher, dass Ihr Konnektivitätsanbieter Ihre Konnektivität mit hoher Verfügbarkeit erweitert, sodass es keine einzelnen Fehlerquellen mehr gibt.
* Bestellen Sie eine ExpressRoute-Verbindung mit der Exchange als Konnektivitätsanbieter für die Verbindung mit Microsoft.
  * Führen Sie die Schritte in [Erstellen einer ExpressRoute-Verbindung](expressroute-howto-circuit-classic.md) aus, um die Verbindung einzurichten.

## <a name="connectivity-through-additional-service-providers"></a>Konnektivität über zusätzliche Dienstanbieter

| **Konnektivitätsanbieter** | **Exchange** | **Standorte** |
| --- | --- | --- |
| **[1CLOUDSTAR](https://www.1cloudstar.com/service/cloudconnect-azure-expressroute/)** |Equinix |Singapur |
| **[Airgate Technologies, Inc.](https://www.airgate.ca/expressroute)** | Equinix, Cologix | Toronto, Montreal |
| **[Alaska Communications](https://www.alaskacommunications.com/For-Your-Business/Direct-Cloud-Service)** |Equinix |Seattle |
| **[Altice Business](https://golightpath.com/transport)** |Equinix |New York, Washington DC |
| **[Arteria Networks Corporation](https://www.arteria-net.com/business/service/cloud/sca/)** |Equinix |Tokio |
| **[Axtel](https://alestra.mx/landing/expressrouteazure/)** |Equinix |Dallas|
| **[Bezeq International Ltd.](https://www.bezeqint.net/english)** | euNetworks | London |
| **[BICS](https://bics.com/bics-solutions-suite/cloud-connect/)** | Equinix | Amsterdam, Frankfurt, London, Singapur, Washington DC |
| **[BroadBand Tower, Inc.](https://www.bbtower.co.jp/product-service/data-center/network/dcconnect-for-azure/)** | Equinix | Tokio |
| **[C3ntro Telecom](https://www.c3ntro.com/data/express-route)** | Equinix, Megaport | Dallas |
| **[Chief](https://www.chief.com.tw/dispPageBox/ct.aspx?ddsPageID=ENCLOUDSERVICE&dbid=4852937342)** | Equinix | Hongkong (SAR) |
| **[Cinia](https://www.cinia.fi/en/services/connectivity-services/direct-public-cloud-connection.html)** | Equinix, Megaport | Frankfurt, Hamburg |
| **[CloudXpress](https://www2.telenet.be/fr/business/produits-services/internet/cloudxpress/)** | Equinix | Amsterdam | 
| **[Cogeco Peer 1](https://www.cogecopeer1.com/en/)**| Equinix | Montreal, Toronto |
| **[CoreAzure](http://coreazure.com/expressroute)**| Equinix | London |
| **[Cox Business](https://www.cox.com/business/networking/cloud-connectivity.html)** | Equinix | Dallas, Silicon Valley, Washington D.C. | 
| **[Data Foundry](https://www.datafoundry.com/services/cloud-connect)** | Megaport | Dallas |
| **[Epsilon Telecommunications Limited](https://www.epsilontel.com/data-connectivity/cloud-access/)** | Equinix | London, Singapur, Washington D.C. |
| **[Eurofiber](https://eurofiber.nl/microsoft-azure/)** | Equinix | Amsterdam |
| **[Exponential E](https://www.exponential-e.com/services/connectivity-services/cloud-connect-exchange)** | Equinix | London |
| **[Fastweb S.p.A](https://www.fastweb.it/grandi-aziende/connessione-voce-e-wifi/scheda-prodotto/rete-privata-virtuale/)** | Equinix | Amsterdam |
| **[Fibrenoire](https://www.fibrenoire.ca/en/cloudextn)** | Megaport | Quebec City |
| **[Gtt Communications Inc](https://www.gtt.net)** |Equinix | Washington DC |
| **[Gulf Bridge International](https://www.gbiinc.com/microsoft-azure-expressroute/)** | Equinix | Amsterdam |
| **[HSO](https://www.hso.co.uk/products/cloud-direct)** |Equinix | London, Slough |
| **[IVedha Inc](http://www.ivedha.com/cloud/manage-azure-cloud/express-route-4/)**| Equinix | Toronto |
| **LGA Telecom** |Equinix |Singapur|
| **[Lightower](http://www.lightower.com/network-solutions/cloud-connect/#microsoft-azure)** |Equinix | Chicago, New York, Washington DC |
| **[Macroview Telecom](http://www.macroview.com/en/scripts/catitem.php?catid=solution&sectionid=expressroute)** |Equinix |Hongkong (SAR) |
| **[Macquarie Telecom Group](https://macquariegovernment.com/secure-cloud/secure-cloud-exchange/)** | Megaport | Sydney |
| **[MainOne](https://www.mainone.net/services/connectivity/cloud-connect/)** |Equinix | Amsterdam |
| **[Masergy](https://www.masergy.com/solutions/hybrid-networking/cloud-marketplace/microsoft-azure)** | Equinix | Washington DC |
| **[MTN](https://www.mtnbusiness.com/en/enterprise/Pages/microsoft-express-route.aspx)** | Teraco | Kapstadt, Johannesburg |
| **[NexGen Networks](https://www.nexgen-net.com/nexgen-networks-direct-connect-microsoft-azure-expressroute.html)** | Interxion | London |
| **[Nianet](https://nianet.dk/produkter/internet/microsoft-expressroute)** |Telecity | Amsterdam, Frankfurt |
| **[Post](https://www.teralinksolutions.com/cloud-connectivity/cloudbridge-to-azure-expressroute/)**|Equinix | Amsterdam |
| **[Proximus](https://www.proximus.be/en/id_b_cl_proximus_external_cloud_connect/companies-and-public-sector/discover/magazines/expert-blog/proximus-external-cloud-connect.html)**|Equinix | Amsterdam, Dublin, London, Paris |
| **[QSC AG](https://www.qsc.de/de/produkte-loesungen/cloud-services-und-it-outsourcing/pure-enterprise-cloud/multi-cloud-management/azure-expressroute/)** |Interxion | Frankfurt |  
| **Rogers** | Cologix, Equinix | Montreal, Toronto |
| **[Spectrum Enterprise](https://enterprise.spectrum.com/services/cloud.html)** | Equinix | Chicago, Dallas, Los Angeles, New York, Silicon Valley | 
| **[Tamares Telecom](https://www.tamarestelecom.com/our-services/#Connectivity)** | Telecity | London | 
| **[TDC Erhverv](https://tdc.dk/Produkter/cloudaccessplus)** | Equinix | Amsterdam | 
| **[Telecom Italia Sparkle](https://www.tisparkle.com/our-platform/corporate-platform/sparkle-cloud-connect#catalogue)**| Equinix | Amsterdam |
| **[Telia](https://www.telia.se/foretag/losningar/produkter-tjanster/datanet)** | Equinix | Amsterdam |
| **[ThinkTel](https://www.thinktel.ca/services/agile-ix-data/expressroute/)** | Equinix | Toronto | 
| **[Transtelco](https://www.transtelco.net/tcloud/microsoft)** |Equinix | Dallas, Los Angeles |
| **[United Information Highway (UIH)](https://www.uih.co.th/en/internet-solution/cloud-direct/uih-cloud-direct-for-microsoft-azure-expressroute)**| Equinix | Singapur |
| **[Venha Pra Nuvem](https://venhapranuvem.com.br/)** | Equinix | Sao Paulo |
| **[Webair](https://www.webair.com/microsoft-express-route-partnership/)**| Megaport | New York |
| **[Windstream](https://www.windstreambusiness.com/solutions/cloud-services/cloud-and-managed-hosting-services)**| Equinix | Chicago, Silicon Valley, Washington, D.C. |
| **Zain** |Equinix |London|
| **[Zertia](https://www.zertia.es)**| Level 3 | Madrid |
| **[Zirro](https://zirro.com/services/)**| Cologix, Equinix | Montreal, Toronto |

## <a name="connectivity-through-datacenter-providers"></a>Konnektivität über Rechenzentrumsanbieter

| **Anbieter** | **Exchange** |
| --- | --- |
| **[CyrusOne](https://cyrusone.com/enterprise-data-center-services/connectivity-and-interconnection/cloud-connectivity-reaching-amazon-microsoft-google-and-more/microsoft-azure-expressroute/?doing_wp_cron=1498512235.6733090877532958984375)** | Megaport, PacketFabric |
| **[Cyxtera](https://www.cyxtera.com/data-center-services/interconnection)** | Megaport, PacketFabric |
| **[Databank](https://www.databank.com/platforms/connectivity/cloud-direct-connect/)** | Megaport |
| **[DataFoundry](https://www.datafoundry.com/services/cloud-connect/)** | Megaport |
| **[Digital Realty](https://www.digitalrealty.com/services/interconnection/service-exchange/)** | IX Reach, Megaport PacketFabric |
| **[EdgeConnex](https://www.edgeconnex.com/services/edge-data-centers-proximity-matters/)** | Megaport, PacketFabric |
| **[Flexential](https://www.flexential.com/connectivity/cloud-connect-microsoft-azure-expressroute)** | IX Reach, Megaport, PacketFabric |
| **[QTS Data Centers](https://www.qtsdatacenters.com/hybrid-solutions/connectivity/azure-cloud )** | Megaport, PacketFabric |
| **[Stream Data Centers]( https://www.streamdatacenters.com/products-services/network-cloud/ )** | Megaport |
| **[RagingWire-Rechenzentren](https://www.ragingwire.com/wholesale/wholesale-data-centers-worldwide-nexcenters)** | IX Reach, Megaport, PacketFabric |
| **[vXchnge](https://www.vxchnge.com/colocation-services/interconnection)** | IX Reach, Megaport |
| **[T5 Datacenters](https://t5datacenters.com/network-cloud-connect/)** | IX Reach |

## <a name="connectivity-through-national-research-and-education-networks-nren"></a>Konnektivität über National Research and Education Networks (NREN)

| **Anbieter**|
| --- |
| **AARNET**| 
| **DeIC (über GÉANT)**|
| **GARR (über GÉANT)**|
| **GÉANT**|
| **HEAnet (über GÉANT)**|
| **Internet2**|
| **JISC**|
| **RedIRIS (über GÉANT)**|
| **SINET**|
| **Surfnet (über GÉANT)**|

* Wenn Ihr Konnektivitätsanbieter nicht hier aufgeführt wird, überprüfen Sie, ob dieser mit einem der oben aufgeführten ExpressRoute Exchange-Partner verbunden ist.

## <a name="expressroute-system-integrators"></a>ExpressRoute-Systemintegratoren
Je nach Ihrer Netzwerkgröße kann das Aktivieren privater Verbindungen für Ihre Anforderungen problematisch sein. Alle Systemintegratoren, die in der folgenden Tabelle aufgeführt sind, können Ihnen beim Integrieren von ExpressRoute helfen.

| **Systemintegrator** | **Kontinent** |
| --- | --- |
| **[Altogee](https://altogee.be/diensten/express-route/)** | Europa |
| **[Avanade Inc.](https://www.avanade.com/)** | Asien, Europa, Nordamerika, Südamerika |
| **[Bright Skies GmbH](https://bskies.io/expressroute)** | Europa
| **[Ensyst](https://www.ensyst.com.au)** | Asien
| **[Equinix Professional Services](https://www.equinix.com/services/consulting/)** | Nordamerika |
| **[FlexManage](https://www.flexmanage.com/cloud)** | Nordamerika |
| **[Lightstream](https://www.lightstream.tech/partners/microsoft-azure/)** | Nordamerika |
| **[The IT Consultancy Group](https://itconsult.com.au/)** | Australien |
| **[MOQdigital](https://www.moqdigital.com.au/insights/technical/network-connectivity-options-for-azure)** | Australien |
| **[MSG Services](https://www.msg-services.de/it-services/managed-services/cloud-outsourcing/)** | Europa (Deutschland) |
| **[Nelite](https://www.nelite.com/offres-services/)** | Europa |
| **[Neue Signatur](https://newsignature.com/technologies/express-route/)** | Europa |
| **[OneAs1a](https://www.oneas1a.com/connectivity.html)** | Asien |
| **[Orange Networks](https://orange-networks.com/blog/88-azureexpressroute)** | Europa |
| **[Perficient](https://www.perficient.com/Partners/Microsoft/Cloud/Azure-ExpressRoute)** | Nordamerika |
| **[Presidio](https://info.presidio.com/microsoft-azure-expressroute)** | Nordamerika |
| **[sol-tec](https://www.sol-tec.com/services)** | Europa |
| **[Venha Pra Nuvem](https://venhapranuvem.com.br/)** | Südamerika |
| **[Vigilant.IT](https://vigilant.it/expressroute)** | Australien |

## <a name="next-steps"></a>Nächste Schritte
* Weitere Informationen über ExpressRoute finden Sie unter [ExpressRoute – FAQ](expressroute-faqs.md).
* Stellen Sie sicher, dass alle Voraussetzungen erfüllt sind. Informationen finden Sie unter [ExpressRoute-Voraussetzungen](expressroute-prerequisites.md).

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "Standortkarte"
