---
title: Was ist Azure DNS?
description: Übersicht über DNS-Hostingdienste in Microsoft Azure. Hosten Ihrer Domäne in Microsoft Azure.
author: rohinkoul
ms.service: dns
ms.topic: overview
ms.date: 3/21/2019
ms.author: rohink
ms.openlocfilehash: 7c3aa5505ca15238b2f9e84069de58ab6b700521
ms.sourcegitcommit: 4c89d9ea4b834d1963c4818a965eaaaa288194eb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/04/2020
ms.locfileid: "96608486"
---
# <a name="what-is-azure-dns"></a>Was ist Azure DNS?

Azure DNS ist ein Hostingdienst für DNS-Domänen, der eine Namensauflösung mittels Microsoft Azure-Infrastruktur bietet. Indem Sie Ihre Domänen in Azure hosten, können Sie Ihre DNS-Einträge unter Verwendung der gleichen Anmeldeinformationen, APIs, Tools und Abrechnungsabläufe wie bei Ihren anderen Azure-Diensten verwalten.

Es ist nicht möglich, Azure DNS zum Erwerben eines Domänennamens zu verwenden. Ein Domänenname kann für eine Jahresgebühr über [App Service-Domänen](../app-service/manage-custom-dns-buy-domain.md#buy-an-app-service-domain) oder über die Domänennamen-Registrierungsstelle eines Drittanbieters erworben werden. Ihre Domänen können dann in Azure DNS für die Verwaltung von Einträgen gehostet werden. Weitere Informationen finden Sie unter [Delegieren einer Domäne an Azure DNS](dns-domain-delegation.md).

Azure DNS bietet folgende Features.

## <a name="reliability-and-performance"></a>Zuverlässigkeit und Leistung

DNS-Domänen in Azure DNS werden im globalen Azure-Netzwerk von DNS-Servern gehostet. Azure DNS verwendet Anycast-Netzwerke. Jede DNS-Abfrage wird vom jeweils nächstgelegenen verfügbaren DNS-Server beantwortet, um für Ihre Domäne eine hohe Leistung und Verfügbarkeit zu erzielen.

## <a name="security"></a>Sicherheit

 Azure DNS basiert auf Azure Resource Manager, wodurch beispielsweise folgende Features zur Verfügung stehen:

* [Rollenbasierte Zugriffssteuerung in Azure (Azure RBAC)](../azure-resource-manager/management/overview.md), um zu steuern, wer Zugriff auf bestimmte Aktionen für Ihre Organisation hat

* [Aktivitätsprotokolle](../azure-resource-manager/management/overview.md), um nachzuverfolgen, wie ein Benutzer in Ihrer Organisation eine Ressource geändert hat, oder um im Rahmen der Problembehandlung Fehler zu finden.

* [Ressourcensperre](../azure-resource-manager/management/lock-resources.md), um ein Abonnement, eine Ressourcengruppe oder eine Ressource zu sperren. Eine Sperre verhindert, dass andere Benutzer in Ihrer Organisation versehentlich wichtige Ressourcen löschen oder ändern.

Weitere Informationen finden Sie unter [Schützen von DNS-Zonen und -Einträgen](dns-protect-zones-recordsets.md). 

## <a name="dnssec"></a>DNSSEC

Azure DNS unterstützt derzeit DNSSEC nicht. In den meisten Fällen können Sie die Notwendigkeit von DNSSEC reduzieren, indem Sie in Ihren Anwendungen konsistent HTTPS/TLS verwenden. Wenn DNSSEC eine kritische Anforderung für Ihre DNS-Zonen ist, können Sie diese Zonen bei Drittanbietern für das DNS-Hosting hosten.

## <a name="ease-of-use"></a>Einfache Bedienung

 Azure DNS kann DNS-Einträge für Ihre Azure-Dienste verwalten und außerdem DNS für Ihre externen Ressourcen bereitstellen. Azure DNS ist in das Azure-Portal integriert und verwendet die gleichen Anmeldeinformationen, den gleichen Supportvertrag und die gleiche Abrechnung wie Ihre anderen Azure-Dienste. 

Die DNS-Abrechnung basiert auf der Anzahl von DNS-Zonen, die in Azure gehostet werden, und der Anzahl von empfangenen DNS-Abfragen. Weitere Informationen zu Preisen finden Sie in den [Preisangaben zu Azure DNS](https://azure.microsoft.com/pricing/details/dns/).

Ihre Domänen und Einträge können über das Azure-Portal, mithilfe von Azure PowerShell-Cmdlets sowie über die plattformübergreifende Azure-Befehlszeilenschnittstelle verwaltet werden. Anwendungen, die eine automatisierte DNS-Verwaltung erfordern, können über die REST-API und SDKs mit dem Dienst zusammenarbeiten.

## <a name="customizable-virtual-networks-with-private-domains"></a>Anpassbare virtuelle Netzwerke mit privaten Domänen

Azure DNS unterstützt auch private DNS-Domänen. Mit diesem Feature können Sie in Ihren privaten virtuellen Netzwerken anstelle der derzeit von Azure bereitgestellten Namen Ihre eigenen benutzerdefinierten Domänennamen verwenden.

Weitere Informationen finden Sie unter [Verwenden von Azure DNS für private Domänen](private-dns-overview.md).

## <a name="alias-records"></a>Aliaseinträge

Azure DNS unterstützt Aliasdatensätze. Sie können einen Aliaseintragssatz verwenden, um auf eine Azure-Ressource zu verweisen, beispielsweise eine öffentliche Azure-IP-Adresse, ein Azure Traffic Manager-Profil oder einen Azure CDN-Endpunkt (Azure Content Delivery Network). Wenn sich die IP-Adresse der zugrunde liegenden Ressource ändert, wird der Aliasdatensatz während der DNS-Auflösung nahtlos automatisch aktualisiert. Der Aliasdatensatz verweist auf die Dienstinstanz, und der Dienstinstanz ist eine IP-Adresse zugeordnet.

Darüber hinaus kann Ihre Apex- oder Naked-Domäne jetzt über einen Aliaseintrag auf ein Traffic Manager-Profil oder einen CDN-Endpunkt verweisen. Ein Beispiel wäre etwa „contoso.com“.

Weitere Informationen finden Sie in der [Übersicht über Azure-DNS-Aliaseinträge](dns-alias.md).

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zu DNS-Zonen und -Einträgen finden Sie in der [Übersicht über DNS-Zonen und -Einträge](dns-zones-records.md).

* Informationen zur Erstellung einer Zone in Azure DNS finden Sie unter [Erstellen einer DNS-Zone](./dns-getstarted-portal.md).

* Häufig gestellte Fragen zu Azure DNS finden Sie unter der [Häufig gestellte Fragen zu Azure DNS](dns-faq.md).