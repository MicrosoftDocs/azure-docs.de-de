---
title: 'Konnektivitätsanbieter und Standorte: Azure ExpressRoute | Microsoft-Dokumentation'
description: Dieser Artikel enthält eine ausführliche Übersicht über die Standorte, an denen Dienste angeboten werden, sowie Informationen darüber, wie Sie eine Verbindung mit den Azure-Regionen herstellen können. Sortiert nach Konnektivitätsanbieter.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 01/22/2021
ms.author: duau
ms.openlocfilehash: ea3a36fdc9ff9458677f43ef43bd7f45ea4652bd
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99576632"
---
# <a name="expressroute-connectivity-partners-and-peering-locations"></a>ExpressRoute-Konnektivitätspartner und Peeringstandorte

> [!div class="op_single_selector"]
> * [Standorte nach Anbieter](expressroute-locations.md)
> * [Anbieter nach Standort](expressroute-locations-providers.md)


In den Tabellen in diesem Artikel finden Sie Informationen zum geografischen Geltungsbereich von ExpressRoute sowie zu ExpressRoute-Standorten, zu ExpressRoute-Konnektivitätsanbietern und zu ExpressRoute-Systemintegratoren (SIs).

> [!Note]
> Azure-Regionen und ExpressRoute-Standorte sind zwei unterschiedliche Konzepte, und das Verstehen der Unterschiede zwischen den beiden ist wichtig für die Untersuchung der Konnektivität von Azure-Hybridnetzwerken. 
>
>

## <a name="azure-regions"></a>Azure-Regionen
Azure-Regionen sind globale Rechenzentren, in denen sich Azure Compute-, Netzwerk- und Speicherressourcen befinden. Beim Erstellen einer Azure-Ressource muss ein Kunde einen Ressourcenspeicherort auswählen. Der Ressourcenspeicherort bestimmt, in welchem Azure-Rechenzentrum (oder in welcher Verfügbarkeitszone) die Ressource erstellt wird.

## <a name="expressroute-locations"></a>ExpressRoute-Standorte
ExpressRoute-Standorte (manchmal auch als „Peeringstandorte“ oder „Meet-Me-Standorte“ bezeichnet) sind Kollokationsumgebungen, in denen sich MSEE-Geräte (Microsoft Enterprise Edge) befinden. ExpressRoute-Standorte sind der Einstiegspunkt in das Netzwerk von Microsoft. Sie sind global verteilt, sodass Kunden die Möglichkeit erhalten, eine Verbindung mit dem Microsoft-Netzwerk weltweit herzustellen. An diesen Standorten stellen ExpressRoute-Partner und ExpressRoute Direct-Kunden Querverbindungen mit dem Microsoft-Netzwerk her. Im Allgemeinen muss der ExpressRoute-Standort der Azure-Region nicht entsprechen. Ein Kunde kann beispielsweise eine ExpressRoute-Leitung mit dem Ressourcenspeicherort *USA, Osten* am Peeringstandort *Seattle* erstellen.

Wenn Sie mit mindestens einem ExpressRoute-Standort innerhalb der geopolitischen Region eine Verbindung hergestellt haben, haben Sie Zugriff auf die Azure-Dienste in allen Regionen dieser geopolitischen Region.

## <a name="azure-regions-to-expressroute-locations-within-a-geopolitical-region"></a><a name="locations"></a>Azure-Regionen mit ExpressRoute-Standorten in einer geopolitischen Region.
Die folgende Tabelle bietet eine Übersicht über die Azure-Regionen mit ExpressRoute-Standorten in einer geopolitischen Region.

| **Geopolitische Region** | **Azure-Regionen** | **ExpressRoute-Standorte** |
| --- | --- | --- |
| **Australische Behörden** |Australien, Mitte; Australien, Mitte 2 |Canberra, Canberra2 |
| **Europa** | Frankreich, Mitte; Frankreich, Süden; Deutschland, Norden; Deutschland, Westen-Mitte; Europa, Norden; Norwegen, Osten; Norwegen, Westen; Schweiz, Norden; Schweiz, Westen; Vereinigtes Königreich, Westen; Vereinigtes Königreich, Süden; Europa, Westen |Amsterdam, Amsterdam2, Berlin, Dublin, Frankfurt, Genf, Kopenhagen, London, London2, Mailand, Marseille, München, Newport (Wales), Oslo, Paris, Stavanger, Stockholm, Zürich |
| **Nordamerika** |USA, Osten; USA, Westen; USA, Osten 2; USA, Westen 2; USA, Mitte; USA, Süden-Mitte; USA, Norden-Mitte; USA, Westen-Mitte; Kanada, Mitte; Kanada, Osten |Atlanta, Chicago, Dallas, Denver, Las Vegas, Los Angeles, Los Angeles2, Miami, Minneapolis, Montreal, New York, Phoenix, Quebec City, Queretaro (Mexico), Quincy, San Antonio, Seattle, Silicon Valley, Silicon Valley2, Toronto, Vancouver, Washington DC, Washington DC2 |
| **Asien** | Ostasien, Südostasien | Bangkok, Hongkong, Hongkong2, Jakarta, Kuala Lumpur, Singapur, Singapur2, Taipeh |
| **Indien** | Indien, Westen, Indien, Mitte, Indien, Süden |Chennai, Chennai2, Mumbai, Mumbai2 |
| **Japan** | Japan West, Japan Ost |Osaka, Tokio, Tokio2 |
| **Ozeanien** | Südostaustralien, Ostaustralien |Auckland, Melbourne, Perth, Sydney, Sydney2 |
| **Südkorea** | Korea, Mitte, Korea, Süden |Busan, Seoul|
| **Vereinigte Arabische Emirate** | VAE, Mitte; VAE, Norden | Dubai, Dubai2 |
| **Südafrika** | „Südafrika, Westen“, „Südafrika, Norden“ |Kapstadt, Johannesburg |
| **Südamerika** | Brasilien Süd |Bogota, Sao Paulo |


## <a name="regions-and-geopolitical-boundaries-for-national-clouds"></a>Regionen und geopolitische Grenzen für nationale Clouds
In der folgenden Tabelle finden Sie Informationen zu Regionen und geopolitischen Grenzen für nationale Clouds.

| **Geopolitische Region** | **Azure-Regionen** | **ExpressRoute-Standorte** |
| --- | --- | --- |
| **US-Government Cloud** |„US Gov Arizona“, „US Gov Iowa“, „US Gov Texas“, „US Gov Virginia“, „US DoD, Mitte“, „US DoD, Osten“  |Atlanta, Chicago, Dallas, New York, Phoenix, San Antonio, Seattle, Silicon Valley, Washington DC |
| **China, Osten** |China, Osten; China, Osten2 |Shanghai, Shanghai2 |
| **China, Norden** |China, Norden; China, Norden2 |Peking, Peking2 |
| **Deutschland** |Deutschland, Mitte, Deutschland, Ost |Berlin, Frankfurt |

Konnektivität zwischen geopolitischen Regionen wird bei der ExpressRoute-Standard-SKU nicht unterstützt. Sie müssen das ExpressRoute Premium-Add-On aktivieren, um Unterstützung für globale Konnektivität zu erhalten. Verbindungen mit nationalen Cloudumgebungen werden nicht unterstützt. Wenden Sie sich an Ihren Konnektivitätsanbieter, wenn Sie derartige Verbindungen implementieren möchten.

## <a name="expressroute-connectivity-providers"></a><a name="partners"></a>ExpressRoute-Konnektivitätsanbieter

Die folgende Tabelle enthält die Standorte nach Service Provider. [Hier](expressroute-locations-providers.md) finden Sie eine Tabelle, die nach den verfügbaren Service Providern mit den jeweiligen Standorten sortiert ist.


### <a name="global-commercial-azure"></a>Globales Azure Commercial

| **Service Provider** | **Microsoft Azure** | **Microsoft 365**  | **Speicherorte** |
| --- | --- | --- | --- |
| **[AARNet](https://www.aarnet.edu.au/network-and-services/connectivity-services/azure-expressroute)** |Unterstützt |Unterstützt |Melbourne, Sydney |
| **[Airtel](https://www.airtel.in/business/#/)** | Unterstützt | Unterstützt | Chennai2, Mumbai2 |
| **[AIS](https://business.ais.co.th/solution/en/azure-expressroute.html)** | Unterstützt | Unterstützt | Bangkok |
| **[Aryaka Networks](https://www.aryaka.com/)** |Unterstützt |Unterstützt |Amsterdam, Chicago, Dallas, Hongkong (SAR), São Paulo, Seattle, Silicon Valley, Singapur, Tokio, Washington DC |
| **[Ascenty-Rechenzentren](https://www.ascenty.com/en/cloud/microsoft-express-route)** |Unterstützt |Unterstützt |Sao Paulo |
| **[AT&amp;T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** |Unterstützt |Unterstützt |Amsterdam, Chicago, Dallas, Frankfurt, London, Silicon Valley, Singapur, Sydney, Tokio, Toronto, Washington DC |
| **[AT TOKYO](https://www.attokyo.com/connectivity/azure.html)** | Unterstützt | Unterstützt | Osaka, Tokyo2 |
| **[BBIX](https://www.bbix.net/en/service/ix/)** | Unterstützt | Unterstützt | Tokio |
| **[BCX](https://www.bcx.co.za/solutions/connectivity/data-networks)** |Unterstützt |Unterstützt |Kapstadt, Johannesburg|
| **[Bell Canada](https://business.bell.ca/shop/enterprise/cloud-connect-access-to-cloud-partner-services)** |Unterstützt |Unterstützt |Montreal, Toronto, Quebec City |
| **[British Telecom](https://www.globalservices.bt.com/en/solutions/products/cloud-connect-azure)** |Unterstützt |Unterstützt |Amsterdam, Amsterdam2, Chicago, Hongkong SAR, Johannesburg, London, London2, Newport(Wales), Paris, Sao Paulo, Silicon Valley, Singapur, Sydney, Tokio, Washington DC |
| **[C3ntro](https://www.c3ntro.com/data1/express-route1.php)** |Unterstützt |Unterstützt |Miami |
| **CDC** | Unterstützt | Unterstützt | Canberra, Canberra2 |
| **[CenturyLink Cloud Connect](https://www.centurylink.com/cloudconnect)** |Unterstützt |Unterstützt |Amsterdam2, Chicago, Dublin, Frankfurt, Hongkong, Las Vegas, London2, New York, Paris, San Antonio, Silicon Valley, Tokio, Toronto, Washington DC, Washington DC2 |
| **[Chief Telecom](https://www.chief.com.tw/)** |Unterstützt |Unterstützt |Hongkong, Taipei |
| **China Mobile International** |Unterstützt |Unterstützt | Hongkong, Hongkong2, Singapur |
| **China Telecom Global** |Unterstützt |Unterstützt |Hongkong, Hongkong2 |
| **China Unicom Global** |Unterstützt |Unterstützt | Hongkong, Singapur2 |
| **[Chunghwa Telecom](https://www.cht.com.tw/en/home/cht/about-cht/products-and-services/International/Cloud-Service)** |Unterstützt |Unterstützt |Taipeh |
| **[Claro](https://www.usclaro.com/enterprise-mnc/connectivity/mpls/)** |Unterstützt |Unterstützt |Miami |
| **[Cologix](https://www.cologix.com/hyperscale/microsoft-azure/)** |Unterstützt |Unterstützt |Chicago, Dallas, Minneapolis, Montreal, Toronto, Vancouver, Washington DC |
| **[Colt](https://www.colt.net/direct-connect/azure/)** |Unterstützt |Unterstützt |Amsterdam, Amsterdam2, Chicago, Dublin, Frankfurt, London, London2, Mailand, Newport, New York, Osaka, Paris, Silicon Valley, Silicon Valley2, Singapur2, Tokio, Washington DC |
| **[Comcast](https://business.comcast.com/landingpage/microsoft-azure)** |Unterstützt |Unterstützt |Chicago, Silicon Valley, Washington, D.C. |
| **[CoreSite](https://www.coresite.com/solutions/cloud-services/public-cloud-providers/microsoft-azure-expressroute)** |Unterstützt |Unterstützt |Chicago, Denver, Los Angeles, New York, Silicon Valley, Silicon Valley2, Washington DC, Washington DC2 |
| **[DE-CIX](https://www.de-cix.net/en/de-cix-service-world/cloud-exchange/find-a-cloud-service/detail/microsoft-azure)** | Unterstützt |Unterstützt |Amsterdam2, Dubai2, Frankfurt, Marseille, Mumbai, München, New York |
| **[Devoli](https://devoli.com/expressroute)** | Unterstützt |Unterstützt | Auckland, Melbourne, Sydney |
| **du datamena** |Unterstützt |Unterstützt | Dubai2 |
| **eir** |Unterstützt |Unterstützt |Dublin|
| **[Epsilon Global Communications](https://www.epsilontel.com/solutions/direct-cloud-connect)** |Unterstützt |Unterstützt |Singapur, Singapur2 |
| **[Equinix](https://www.equinix.com/partners/microsoft-azure/)** |Unterstützt |Unterstützt |Amsterdam, Amsterdam2, Atlanta, Berlin, Canberra2, Chicago, Dallas, Dubai2, Dublin, Frankfurt, Genf, Hongkong SAR, London, London2, Los Angeles, Los Angeles2, Melbourne, Miami, Mailand, New York, Osaka, Paris, Sao Paulo, Seattle, Seoul, Silicon Valley, Singapur, Stockholm, Sydney, Tokyo, Toronto, Washington DC, Zürich |
| **Etisalat (VAE)** |Unterstützt |Unterstützt |Dubai|
| **[euNetworks](https://eunetworks.com/services/solutions/cloud-connect/microsoft-azure-expressroute/)** |Unterstützt |Unterstützt |Amsterdam, Amsterdam2, Dublin, Frankfurt, London |
| **[FarEasTone](https://www.fetnet.net/corporate/en/Enterprise.html)** |Unterstützt |Unterstützt |Taipeh|
| **[Fastweb](https://www.fastweb.it/grandi-aziende/cloud/scheda-prodotto/fastcloud-interconnect/)** | Unterstützt |Unterstützt |Mailand|
| **[Fibrenoire](https://fibrenoire.ca/en/services/cloudextn-2/)** |Unterstützt |Unterstützt |Montreal|
| **[GÉANT](https://www.geant.org/Networks)** |Unterstützt |Unterstützt |Amsterdam, Amsterdam2, Dublin, Frankfurt, Marseille |
| **[GlobalConnect]()** | Unterstützt |Unterstützt | Oslo, Stavanger | 
| **GTT** |Unterstützt |Unterstützt |London2 |
| **[Global Cloud Xchange (GCX)](https://globalcloudxchange.com/cloud-platform/cloud-x-fusion/)** | Unterstützt| Unterstützt | Chennai, Mumbai |
| **Intelsat** | Unterstützt | Unterstützt | Washington DC2 |
| **[InterCloud](https://www.intercloud.com/)** |Unterstützt |Unterstützt |Amsterdam, Chicago, Frankfurt, Hongkong, London, New York, Paris, Silicon Valley, Singapur, Washington DC, Zürich |
| **[Internet2](https://internet2.edu/services/cloud-connect/#service-cloud-connect)** |Unterstützt |Unterstützt |Chicago, Dallas, Silicon Valley, Washington DC |
| **[Internet Initiative Japan Inc. - IIJ](https://www.iij.ad.jp/en/news/pressrelease/2015/1216-2.html)** |Unterstützt |Unterstützt |Osaka, Tokio |
| **[Internet Solutions – Cloud Connect](https://www.is.co.za/solution/cloud-connect/)** |Unterstützt |Unterstützt |Kapstadt, Johannesburg, London |
| **[Interxion](https://www.interxion.com/why-interxion/colocate-with-the-clouds/Microsoft-Azure/)** |Unterstützt |Unterstützt |Amsterdam, Amsterdam2, Kopenhagen, Dublin, Frankfurt, London, Marseille, Paris, Zürich |
| **[IRIDEOS](https://irideos.it/)** |Unterstützt |Unterstützt |Mailand |
| **[IX Reach](https://www.ixreach.com/partners/cloud-partners/microsoft-azure/)**|Unterstützt |Unterstützt | Amsterdam, London2, Silicon Valley, Toronto, Washington DC |
| **Jaguar Network** |Unterstützt |Unterstützt |Marseille, Paris |
| **[Jisc](https://www.jisc.ac.uk/microsoft-azure-expressroute)** |Unterstützt |Unterstützt |London, Newport (Wales) |
| **[KINX](https://www.kinx.net/service/network/cloudhub/ms-expressroute/?lang=en)** |Unterstützt |Unterstützt |Seoul |
| **[Kordia](https://www.kordia.co.nz/cloudconnect)** | Unterstützt |Unterstützt |Auckland, Sydney |
| **[KPN](https://www.kpn.com/zakelijk/cloud/connect.htm)** | Unterstützt | Unterstützt | Amsterdam |
| **[KT](https://cloud.kt.com/portal/ktcloudportal.epc.productintro.partnershipcloud_ConnectHub.html)** | Unterstützt | Unterstützt | Seoul |
| **[Level 3 Communications](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** |Unterstützt |Unterstützt |Amsterdam, Chicago, Dallas, London, Newport (Wales), Sao Paulo, Seattle, Silicon Valley, Singapur, Washington DC |
| **LG CNS** |Unterstützt |Unterstützt |Busan, Seoul |
| **[Liquid Telecom](https://www.liquidtelecom.com/products-and-services/cloud.html)** |Unterstützt |Unterstützt |Kapstadt, Johannesburg |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Unterstützt |Unterstützt |Amsterdam, Atlanta, Auckland, Chicago, Dallas, Denver, Dubai2, Dublin, Frankfurt, Genf, Hongkong, Hongkong2, Las Vegas, London, London2, Los Angeles, Melbourne, Miami, Minneapolis, Montreal, New York, Osaka, Oslo, Perth, Quebec City, San Antonio, Seattle, Silicon Valley, Singapur, Singapur2, Stavanger, Stockholm, Sydney, Sydney2, Tokio, Tokio2, Toronto, Vancouver, Washington DC, Washington DC2, Zürich |
| **[MTN](https://www.mtnbusiness.co.za/en/Cloud-Solutions/Pages/microsoft-express-route.aspx)** |Unterstützt |Unterstützt |London |
| **[Neutrona Networks](https://www.neutrona.com/index.php/azure-expressroute/)** |Unterstützt |Unterstützt |Dallas, Los Angeles, Miami, Sao Paulo, Washington, D.C. |
| **[Daten der nächsten Generation](https://vantage-dc-cardiff.co.uk/)** |Unterstützt |Unterstützt |Newport(Wales) |
| **[NEXTDC](https://www.nextdc.com/services/axon-ethernet/microsoft-expressroute)** |Unterstützt |Unterstützt |Melbourne, Perth, Sydney, Sydney2 |
| **[NOS](https://www.nos.pt/empresas/corporate/cloud/cloud/Pages/nos-cloud-connect.aspx)** |Unterstützt |Unterstützt |Amsterdam2 |
| **[NTT Communications](https://www.ntt.com/en/services/network/virtual-private-network.html)** |Unterstützt |Unterstützt |Amsterdam, Hongkong (SAR), London, Los Angeles, Osaka, Singapur, Sydney, Tokio, Washington DC |
| **[NTT EAST](https://business.ntt-east.co.jp/service/crossconnect/)** |Unterstützt |Unterstützt |Tokio |
| **[NTT Global DataCenters EMEA](https://hello.global.ntt/)** |Unterstützt |Unterstützt |Amsterdam2, Berlin |
| **[NTT SmartConnect](https://cloud.nttsmc.com/cxc/azure.html)** |Unterstützt |Unterstützt |Osaka |
| **[Ooredoo Cloud Connect](https://www.ooredoo.qa/portal/OoredooQatar/cloud-connect-expressroute)** |Unterstützt |Unterstützt |Marseille |
| **[Optus](https://www.optus.com.au/enterprise/)** |Unterstützt |Unterstützt |Melbourne, Sydney |
| **[Orange](https://www.orange-business.com/en/products/business-vpn-galerie)** |Unterstützt |Unterstützt |Amsterdam, Amsterdam2, Dubai2, Frankfurt, Hongkong SAR, Johannesburg, London, Paris, Sao Paulo, Silicon Valley, Singapur, Sydney, Tokio, Washington DC |
| **[Orixcom](https://www.orixcom.com/cloud-solutions/)** | Unterstützt | Unterstützt | Dubai2 |
| **[PacketFabric](https://www.packetfabric.com/cloud-connectivity/microsoft-azure)** |Unterstützt |Unterstützt |Chicago, Las Vegas, Silicon Valley, Washington DC |
| **[PCCW Global Limited](https://consoleconnect.com/clouds/#azureRegions)** |Unterstützt |Unterstützt |Chicago, Hongkong, Hongkong2, London, Singapur2 |
| **[Retelit](https://www.retelit.it/EN/Home.aspx)** | Unterstützt | Unterstützt | Mailand | 
| **[Sejong Telecom](https://www.sejongtelecom.net/en/pages/service/cloud_ms)** |Unterstützt |Unterstützt |Seoul |
| **[SES](https://www.ses.com/networks/signature-solutions/signature-cloud/ses-and-azure-expressroute)** | Unterstützt |Unterstützt | London2, Washington DC |
| **[SIFY](http://telecom.sify.com/azure-expressroute.html)** |Unterstützt |Unterstützt |Chennai, Mumbai2 |
| **[SingTel](https://www.singtel.com/about-us/news-releases/singtel-provide-secure-private-access-microsoft-azure-public-cloud)** |Unterstützt |Unterstützt |Hongkong2, Singapur, Singapur2 |
| **[Softbank](https://www.softbank.jp/biz/cloud/cloud_access/direct_access_for_az/)** |Unterstützt |Unterstützt |Osaka, Tokio |
| **[Sohonet](https://www.sohonet.com/fastlane/)** |Unterstützt |Unterstützt |London2 |
| **[Spark NZ](https://www.sparkdigital.co.nz/solutions/connectivity/cloud-connect/)** |Unterstützt |Unterstützt |Auckland, Sydney |
| **[Sprint](https://business.sprint.com/solutions/cloud-networking/)** |Unterstützt |Unterstützt |Chicago, Silicon Valley, Washington, D.C. |
| **[Swisscom](https://www.swisscom.ch/en/business/enterprise/offer/cloud-data-center/microsoft-cloud-services/microsoft-azure-von-swisscom.html)** | Unterstützt | Unterstützt | Zürich |
| **[Tata Communications](https://www.tatacommunications.com/lp/izo/azure/azure_index.html)** |Unterstützt |Unterstützt |Amsterdam, Chennai, Hongkong (SAR), London, Mumbai, São Paulo, Silicon Valley, Singapur, Washington DC |
| **[Telefonica](https://www.business-solutions.telefonica.com/es/enterprise/solutions/efficient-infrastructure/managed-voice-data-connectivity/)** |Unterstützt |Unterstützt |Amsterdam, Sao Paulo |
| **[Telehouse - KDDI](https://www.telehouse.net/solutions/cloud-services/cloud-link)** |Unterstützt |Unterstützt |London, London2, Singapur2 |
| **Telenor** |Unterstützt |Unterstützt |Amsterdam, London, Oslo |
| **[Telia Carrier](https://www.teliacarrier.com/)** | Unterstützt | Unterstützt |Amsterdam, Chicago, Dallas, Frankfurt, Hongkong, London, Oslo, Paris, Silicon Valley, Stockholm, Washington DC |
| **[Telin](https://www.telin.net/)** | Unterstützt | Unterstützt |Jakarta |
| **Telmex Uninet**| Unterstützt | Unterstützt | Dallas |
| **[Telstra Corporation](https://www.telstra.com.au/business-enterprise/network-services/networks/cloud-direct-connect/)** |Unterstützt |Unterstützt |Melbourne, Singapur, Sydney |
| **[Telus](https://www.telus.com)** |Unterstützt |Unterstützt |Montreal, Seattle, Toronto, Vancouver |
| **[Teraco](https://www.teraco.co.za/services/africa-cloud-exchange/)** |Unterstützt |Unterstützt |Kapstadt, Johannesburg |
| **[TIME dotCom](https://www.time.com.my/enterprise/connectivity/direct-cloud)** | Unterstützt | Unterstützt | Kuala Lumpur |
| **[Tokai Communications](https://www.tokai-com.co.jp/en/)** | Unterstützt | Unterstützt | Osaka, Tokyo2 |
| **[Transtelco](https://transtelco.net/enterprise-services/)** |Unterstützt |Unterstützt |Dallas, Queretaro (Mexiko)|
| **T-Systems** |Unterstützt |Unterstützt |Frankfurt|
| **[UOLDIVEO](https://www.uoldiveo.com.br/)** |Unterstützt |Unterstützt |Sao Paulo |
| **[UIH](https://www.uih.co.th/en/network-solutions/global-network/cloud-direct-for-microsoft-azure-expressroute)** | Unterstützt | Unterstützt | Bangkok |
| **[Verizon](https://enterprise.verizon.com/products/network/application-enablement/secure-cloud-interconnect/)** |Unterstützt |Unterstützt |Amsterdam, Chicago, Dallas, Hongkong (SAR), London, Mumbai, Silicon Valley, Singapur, Sydney, Tokio, Toronto, Washington DC |
| **[Viasat](http://www.directcloud.viasatbusiness.com/)** | Unterstützt | Unterstützt | Washington DC2 |
| **[Vocus Group NZ](https://www.vocus.co.nz/business/cloud-data-centres)** | Unterstützt | Unterstützt | Auckland, Sydney |
| **[Vodafone](https://www.vodafone.com/business/global-enterprise/global-connectivity/vodafone-ip-vpn-cloud-connect)** |Unterstützt |Unterstützt |Amsterdam2, London, Singapur |
| **[Vodafone Idea](https://www.vodafone.in/business/enterprise-solutions/connectivity/vpn-extended-connect)** | Unterstützt | Unterstützt | Mumbai2 |
| **[Zayo](https://www.zayo.com/solutions/industries/cloud-connectivity/microsoft-expressroute)** |Unterstützt |Unterstützt |Amsterdam, Chicago, Dallas, Denver, London, Los Angeles, Montreal, New York, Paris, Seattle, Silicon Valley, Toronto, Washington DC, Washington DC2 |

 **+** steht für "In Kürze"

### <a name="national-cloud-environment"></a>Nationale Cloudumgebungen

Nationale Azure-Clouds sind voneinander und vom globalen Azure Commercial isoliert.Nationale Azure-Clouds sind voneinander und vom globalen Azure Commercial isoliert. ExpressRoute für eine Azure-Cloud kann keine Verbindung mit den Azure-Regionen in den anderen Clouds herstellen. 

### <a name="us-government-cloud"></a>US-Government Cloud

| **Service Provider** | **Microsoft Azure** | **Office 365** | **Speicherorte** |
| --- | --- | --- | --- |
| **[AT&amp;T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** |Unterstützt |Unterstützt |Chicago, Phoenix, Silicon Valley, Washington DC |
| **[CenturyLink Cloud Connect](https://www.centurylink.com/cloudconnect)** |Unterstützt |Unterstützt |New York, Phoenix, San Antonio, Washington DC |
| **[Equinix](https://www.equinix.com/partners/microsoft-azure/)** |Unterstützt |Unterstützt |Atlanta,Chicago, Dallas, New York, Seattle, Silicon Valley, Washington DC |
| **[Level 3 Communications](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** |Unterstützt |Unterstützt |Chicago, Silicon Valley, Washington, D.C. |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Unterstützt | Unterstützt | Chicago, Dallas, San Antonio, Seattle, Washington DC |
| **[Verizon](http://news.verizonenterprise.com/2014/04/secure-cloud-interconnect-solutions-enterprise/)** |Unterstützt |Unterstützt |Chicago, Dallas, New York, Silicon Valley, Washington DC |

### <a name="china"></a>China

| **Service Provider** | **Microsoft Azure** | **Office 365** | **Speicherorte** |
| --- | --- | --- | --- |
| **China Telecom** |Unterstützt |Nicht unterstützt |Peking (Beijing), Peking2 (Beijing2), Shanghai, Shanghai2 |
| **China Unicom** | Unterstützt | Nicht unterstützt | Peking2, Shanghai2 |
| **[GDS](http://www.gds-services.com/en/about_2.html)** |Unterstützt |Nicht unterstützt |Peking2, Shanghai2 |

Weitere Informationen finden Sie unter [ExpressRoute in China](http://www.windowsazure.cn/home/features/expressroute/).

### <a name="germany"></a>Deutschland

| **Service Provider** | **Microsoft Azure** | **Office 365** | **Speicherorte** |
| --- | --- | --- | --- |
| **[Colt](https://www.colt.net/direct-connect/azure/)** |Unterstützt |Nicht unterstützt |Frankfurt |
| **[Equinix](https://www.equinix.com/partners/microsoft-azure/)** |Unterstützt |Nicht unterstützt |Frankfurt |
| **[e-shelter](https://www.e-shelter.de/en/microsoft-expressroute)** |Unterstützt |Nicht unterstützt |Berlin |
| **Interxion** |Unterstützt |Nicht unterstützt |Frankfurt |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Unterstützt  | Nicht unterstützt | Berlin |
| **T-Systems** |Unterstützt |Nicht unterstützt |Berlin |

## <a name="connectivity-through-exchange-providers"></a>Konnektivität über Exchange-Anbieter

Wenn Ihr Konnektivitätsanbieter nicht in den vorherigen Abschnitten enthalten ist, können Sie dennoch eine Verbindung erstellen.

* Fragen Sie Ihren Konnektivitätsanbieter, ob eine Verbindung mit einem der Exchange-Standorte aus der vorherigen Tabelle bereitgestellt werden kann. Sie können auch die folgenden Links überprüfen, um weitere Informationen über die von den Exchange-Anbietern angebotenen Dienste zu erhalten. Viele Konnektivitätsanbieter sind bereits mit Ethernet-Exchanges verbunden.
  * [Cologix](https://www.cologix.com/)
  * [CoreSite](https://www.coresite.com/)
  * [DE-CIX](https://www.de-cix.net/en/de-cix-service-world/cloud-exchange)
  * [Equinix Cloud Exchange](https://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
  * [Interxion](https://www.interxion.com/products/interconnection/cloud-connect/)
  * [IX Reach](https://www.ixreach.com/partners/cloud-partners/microsoft-azure/)
  * [Megaport](https://www.megaport.com/services/microsoft-expressroute/)
  * [NextDC](https://www.nextdc.com/)
  * [PacketFabric](https://www.packetfabric.com/cloud-connectivity/microsoft-azure) 
  * [Teraco](https://www.teraco.co.za/platform-teraco/africa-cloud-exchange/)

* Bitten Sie Ihren Konnektivitätsanbieter, Ihr Netzwerk auf den Peeringstandort Ihrer Wahl zu erweitern.
  * Stellen Sie sicher, dass Ihr Konnektivitätsanbieter Ihre Konnektivität mit hoher Verfügbarkeit erweitert, sodass es keine einzelnen Fehlerquellen mehr gibt.
* Bestellen Sie eine ExpressRoute-Verbindung mit der Exchange als Konnektivitätsanbieter für die Verbindung mit Microsoft.
  * Führen Sie die Schritte in [Erstellen einer ExpressRoute-Verbindung](expressroute-howto-circuit-classic.md) aus, um die Verbindung einzurichten.

## <a name="connectivity-through-satellite-operators"></a>Konnektivität durch Satellitenoperatoren
Wenn Sie an einem Remotestandort nicht über Glasfaserkonnektivität verfügen oder andere Konnektivitätsoptionen untersuchen möchten, können Sie die folgenden Satellitenoperatoren überprüfen. 

* Intelsat
* [SES](https://www.ses.com/networks/signature-solutions/signature-cloud/ses-and-azure-expressroute)
* [Viasat](http://www.directcloud.viasatbusiness.com/)

## <a name="connectivity-through-additional-service-providers"></a>Konnektivität über zusätzliche Dienstanbieter

| **Konnektivitätsanbieter** | **Exchange** | **Speicherorte** |
| --- | --- | --- |
| **[1CLOUDSTAR](https://www.1cloudstar.com/service/cloudconnect-azure-expressroute/)** |Equinix |Singapur |
| **[Airgate Technologies, Inc.](https://www.airgate.ca/)** | Equinix, Cologix | Toronto, Montreal |
| **[Alaska Communications](https://www.alaskacommunications.com/Business)** |Equinix |Seattle |
| **[Altice Business](https://golightpath.com/transport)** |Equinix |New York, Washington DC |
| **[Arteria Networks Corporation](https://www.arteria-net.com/business/service/cloud/sca/)** |Equinix |Tokio |
| **[Axtel](https://alestra.mx/landing/expressrouteazure/)** |Equinix |Dallas|
| **[Beanfield Metroconnect](https://www.beanfield.com/business/cloud-exchange)** |Megaport |Toronto|
| **[Bezeq International Ltd.](https://www.bezeqint.net/english)** | euNetworks | London |
| **[BICS](https://bics.com/bics-solutions-suite/cloud-connect/)** | Equinix | Amsterdam, Frankfurt, London, Singapur, Washington DC |https://www.chief.com.tw/
| **[BroadBand Tower, Inc.](https://www.bbtower.co.jp/product-service/data-center/network/dcconnect-for-azure/)** | Equinix | Tokio |
| **[C3ntro Telecom](https://www.c3ntro.com/data/express-route)** | Equinix, Megaport | Dallas |
| **[Chief]()** | Equinix | Hongkong (SAR) |
| **[Cinia](https://www.cinia.fi/en/services/connectivity-services/direct-public-cloud-connection.html)** | Equinix, Megaport | Frankfurt, Hamburg |
| **[CloudXpress](https://www2.telenet.be/fr/business/produits-services/internet/cloudxpress/)** | Equinix | Amsterdam | 
| **[CMC Telecom](https://cmctelecom.vn/san-pham/value-added-service-and-it/cmc-telecom-cloud-express-en/)** | Equinix | Singapur | 
| **[Aptum Technologies](https://aptum.com/services/cloud/managed-azure/)**| Equinix | Montreal, Toronto |
| **[CoreAzure](https://www.coreazure.com/)**| Equinix | London |
| **[Cox Business](https://www.cox.com/business/networking/cloud-connectivity.html)**| Equinix | Dallas, Silicon Valley, Washington D.C. |
| **[Crown Castle](https://fiber.crowncastle.com/solutions/added/cloud-connect)**| Equinix | Atlanta, Chicago, Dallas, Los Angeles, New York, Washington DC |
| **[Data Foundry](https://www.datafoundry.com/services/cloud-connect)** | Megaport | Dallas |
| **[Epsilon Telecommunications Limited](https://www.epsilontel.com/solutions/cloud-connect/)** | Equinix | London, Singapur, Washington D.C. |
| **[Eurofiber](https://eurofiber.nl/microsoft-azure/)** | Equinix | Amsterdam |
| **[Exponential E](https://www.exponential-e.com/services/connectivity-services/cloud-connect-exchange)** | Equinix | London |
| **[Fastweb S.p.A](https://www.fastweb.it/grandi-aziende/connessione-voce-e-wifi/scheda-prodotto/rete-privata-virtuale/)** | Equinix | Amsterdam |
| **[Fibrenoire](https://www.fibrenoire.ca/en/cloudextn)** | Megaport | Quebec City |
| **[Gtt Communications Inc](https://www.gtt.net)** |Equinix | Washington DC |
| **[Gulf Bridge International](https://www.gbiinc.com/microsoft-azure-expressroute/)** | Equinix | Amsterdam |
| **[HSO](https://www.hso.co.uk/products/cloud-direct)** |Equinix | London, Slough |
| **[IVedha Inc](http://www.ivedha.com/cloud/manage-azure-cloud/express-route-4/)**| Equinix | Toronto |
| **[Kaalam Telecom Bahrain B.S.C](http://www.kalaam-telecom.com/azure/)**| Level 3 Communications |Amsterdam |
| **LGA Telecom** |Equinix |Singapur|
| **[Macroview Telecom](http://www.macroview.com/en/scripts/catitem.php?catid=solution&sectionid=expressroute)** |Equinix |Hongkong (SAR) 
| **[Macquarie Telecom Group](https://macquariegovernment.com/secure-cloud/secure-cloud-exchange/)** | Megaport | Sydney |
| **[MainOne](https://www.mainone.net/services/connectivity/cloud-connect/)** |Equinix | Amsterdam |
| **[Masergy](https://www.masergy.com/solutions/hybrid-networking/cloud-marketplace/microsoft-azure)** | Equinix | Washington DC |
| **[MTN](https://www.mtnbusiness.co.za/en/Cloud-Solutions/Pages/microsoft-express-route.aspx)** | Teraco | Kapstadt, Johannesburg |
| **[NexGen Networks](https://www.nexgen-net.com/nexgen-networks-direct-connect-microsoft-azure-expressroute.html)** | Interxion | London |
| **[Nianet](https://nianet.dk/produkter/internet/microsoft-expressroute)** |Equinix | Amsterdam, Frankfurt |
| **[Oncore Cloud Service Inc](https://www.oncore.cloud/services/ue-for-expressroute)**| Equinix | Toronto |
| **[POST Telecom Luxembourg](https://www.teralinksolutions.com/cloud-connectivity/cloudbridge-to-azure-expressroute/)**|Equinix | Amsterdam |
| **[Proximus](https://www.proximus.be/en/id_b_cl_proximus_external_cloud_connect/companies-and-public-sector/discover/magazines/expert-blog/proximus-external-cloud-connect.html)**|Equinix | Amsterdam, Dublin, London, Paris |
| **[QSC AG](https://www.qsc.de/de/produkte-loesungen/cloud-services-und-it-outsourcing/pure-enterprise-cloud/multi-cloud-management/azure-expressroute/)** |Interxion | Frankfurt |  
| **[RETN](https://retn.net/services/cloud-connect/)** | Equinix | Amsterdam |
| **Rogers** | Cologix, Equinix | Montreal, Toronto |
| **[Spectrum Enterprise](https://enterprise.spectrum.com/services/cloud/cloud-connect.html)** | Equinix | Chicago, Dallas, Los Angeles, New York, Silicon Valley | 
| **[Tamares Telecom](http://www.tamarestelecom.com/our-services/#Connectivity)** | Equinix | London | 
| **[TDC Erhverv](https://tdc.dk/Produkter/cloudaccessplus)** | Equinix | Amsterdam | 
| **[Telecom Italia Sparkle](https://www.tisparkle.com/our-platform/corporate-platform/sparkle-cloud-connect#catalogue)**| Equinix | Amsterdam |
| **[Telekom Deutschland GmbH](https://cloud.telekom.de/de/infrastruktur/managed-it-services/managed-hybrid-infrastructure-mit-microsoft-azure)** | Interxion | Amsterdam, Frankfurt |
| **[Telia](https://www.telia.se/foretag/losningar/produkter-tjanster/datanet)** | Equinix | Amsterdam |
| **[ThinkTel](https://www.thinktel.ca/services/agile-ix-data/expressroute/)** | Equinix | Toronto | 
| **[United Information Highway (UIH)](https://www.uih.co.th/en/internet-solution/cloud-direct/uih-cloud-direct-for-microsoft-azure-expressroute)**| Equinix | Singapur |
| **[Venha Pra Nuvem](https://venhapranuvem.com.br/)** | Equinix | Sao Paulo |
| **[Webair](https://www.webair.com/microsoft-express-route-partnership/)**| Megaport | New York |
| **[Windstream](https://www.windstreambusiness.com/solutions/cloud-services/cloud-and-managed-hosting-services)**| Equinix | Chicago, Silicon Valley, Washington, D.C. |
| **[X2nsat Inc.](https://www.x2nsat.com/expressroute/)** |Coresite |Silicon Valley, Silicon Valley 2|
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
| **[T5 Datacenters](https://t5datacenters.com/)** | IX Reach |

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
| **[Ensyst](https://www.ensyst.com.au)** | Asia
| **[Equinix Professional Services](https://www.equinix.com/services/consulting/)** | Nordamerika |
| **[FlexManage](https://www.flexmanage.com/cloud)** | Nordamerika |
| **[Lightstream](https://www.lightstream.tech/partners/microsoft-azure/)** | Nordamerika |
| **[The IT Consultancy Group](https://itconsult.com.au/)** | Australien |
| **[MOQdigital](https://www.moqdigital.com/insights)** | Australien |
| **[MSG Services](https://www.msg-services.de/it-services/managed-services/cloud-outsourcing/)** | Europa (Deutschland) |
| **[Nelite](https://www.exakis-nelite.com/offres/)** | Europa |
| **[Neue Signatur](https://newsignature.com/technologies/express-route/)** | Europa |
| **[OneAs1a](https://www.oneas1a.com/connectivity.html)** | Asia |
| **[Orange Networks](https://orange-networks.com/blog/88-azureexpressroute)** | Europa |
| **[Perficient](https://www.perficient.com/Partners/Microsoft/Cloud/Azure-ExpressRoute)** | Nordamerika |
| **[Presidio](https://www.presidio.com/subpage/1107/microsoft-azure)** | Nordamerika |
| **[sol-tec](https://www.sol-tec.com/what-we-do/)** | Europa |
| **[Venha Pra Nuvem](https://venhapranuvem.com.br/)** | Südamerika |
| **[Vigilant.IT](https://vigilant.it/expressroute)** | Australien |

## <a name="next-steps"></a>Nächste Schritte
* Weitere Informationen über ExpressRoute finden Sie unter [ExpressRoute – FAQ](expressroute-faqs.md).
* Stellen Sie sicher, dass alle Voraussetzungen erfüllt sind. Informationen finden Sie unter [ExpressRoute-Voraussetzungen](expressroute-prerequisites.md).

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "Standortkarte"
