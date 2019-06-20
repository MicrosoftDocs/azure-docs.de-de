---
title: Herstellen einer Verbindung zwischen Azure Data Lake Storage Gen1 und VNETs | Microsoft-Dokumentation
description: Herstellen einer Verbindung zwischen Azure Data Lake Storage Gen1 und VNETs
services: data-lake-store,data-catalog
documentationcenter: ''
author: esung22
manager: mtillman
editor: cgronlun
ms.assetid: 683fcfdc-cf93-46c3-b2d2-5cb79f5e9ea5
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.date: 01/31/2018
ms.author: elsung
ms.openlocfilehash: c8d028a981d7811ed2c864db5750afc83ab93b2b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60878867"
---
# <a name="access-azure-data-lake-storage-gen1-from-vms-within-an-azure-vnet"></a>Zugreifen auf Azure Data Lake Storage Gen1 von virtuellen Computern in einem Azure-VNET
Bei Azure Data Lake Storage Gen1 handelt es sich um einen PaaS-Dienst, der unter öffentlichen Internet-IP-Adressen ausgeführt wird. Jeder Server, der eine Verbindung mit dem öffentlichen Internet herstellen kann, kann in der Regel auch eine Verbindung mit Azure Data Lake Storage Gen1-Endpunkten herstellen. Standardmäßig können alle virtuellen Computer in Azure-VNETs auf das Internet und damit auf Azure Data Lake Storage Gen1 zugreifen. Allerdings ist es möglich, virtuelle Computer in einem VNet so zu konfigurieren, dass sie nicht auf das Internet zugreifen können. Bei diesen virtuellen Computern ist auch der Zugriff auf Azure Data Lake Storage Gen1 eingeschränkt. Der Zugriff auf das öffentliche Internet kann für virtuelle Computer in Azure-VNETs mit einer der folgenden Methoden blockiert werden:

* Konfigurieren von Netzwerksicherheitsgruppen (NSG)
* Konfigurieren von benutzerdefinierten Routen (User Defined Routes, UDR)
* Austauschen von Routen, die den Zugriff auf das Internet blockieren, über BGP (Branchenstandardprotokoll für das dynamische Routing) bei Verwendung von ExpressRoute

In diesem Artikel erfahren Sie, wie Sie den Zugriff auf Azure Data Lake Storage Gen1 von Azure-VMs aktivieren, deren Zugriff auf Ressourcen mit einer der zuvor aufgeführten Methoden eingeschränkt wurde.

## <a name="enabling-connectivity-to-azure-data-lake-storage-gen1-from-vms-with-restricted-connectivity"></a>Aktivieren von Verbindungen mit Azure Data Lake Storage Gen1 von virtuellen Computern mit eingeschränkter Konnektivität
Um von diesen virtuellen Computern auf Azure Data Lake Storage Gen1 zugreifen zu können, müssen Sie sie für den Zugriff auf die IP-Adresse für die Region konfigurieren, unter der das Azure Data Lake Storage Gen1-Konto verfügbar ist. Sie können die IP-Adressen für Ihre Data Lake Storage Gen1-Konten ermitteln, indem Sie die DNS-Namen der Kontenregionen auflösen (`<account>.azuredatalakestore.net`). Zum Auflösen von DNS-Namen Ihrer Konten können Sie Tools wie **nslookup** nutzen. Öffnen Sie eine Eingabeaufforderung auf dem Computer, und führen Sie den folgenden Befehl aus:

    nslookup mydatastore.azuredatalakestore.net

Die Ausgabe sieht ungefähr wie folgt aus. Der Wert der **Address**-Eigenschaft ist die dem Data Lake Storage Gen1-Konto zugeordnete IP-Adresse.

    Non-authoritative answer:
    Name:    1434ceb1-3a4b-4bc0-9c69-a0823fd69bba-mydatastore.projectcabostore.net
    Address:  104.44.88.112
    Aliases:  mydatastore.azuredatalakestore.net


### <a name="enabling-connectivity-from-vms-restricted-by-using-nsg"></a>Aktivieren der Konnektivität von virtuellen Computern, deren Konnektivität mithilfe von Netzwerksicherheitsgruppen eingeschränkt wurde
Wenn eine NSG-Regel zum Blockieren des Internetzugriffs verwendet wird, können Sie eine weitere NSG erstellen, die den Zugriff auf die Data Lake Storage Gen1-IP-Adresse zulässt. Weitere Informationen zu Netzwerksicherheitsgruppen-Regeln finden Sie unter [Netzwerksicherheit](../virtual-network/security-overview.md). Anweisungen zum Erstellen von Netzwerksicherheitsgruppen finden Sie unter [Erstellen einer Netzwerksicherheitsgruppe](../virtual-network/tutorial-filter-network-traffic.md).

### <a name="enabling-connectivity-from-vms-restricted-by-using-udr-or-expressroute"></a>Ermöglichen der Verbindungsherstellung von virtuellen Computern, deren Konnektivität mithilfe von UDR oder ExpressRoute eingeschränkt wurde
Wenn der Internetzugriff mithilfe von benutzerdefinierten Routen oder über BGP ausgetauschten Routen blockiert wird, muss eine spezielle Route konfiguriert werden, damit virtuelle Computer in diesen Subnetzen auf Data Lake Storage Gen1-Endpunkte zugreifen können. Weitere Informationen finden Sie unter [Routing von Datenverkehr für virtuelle Netzwerke](../virtual-network/virtual-networks-udr-overview.md). Anleitungen zum Erstellen von benutzerdefinierten Routen finden Sie unter [Erstellen von benutzerdefinierten Routen (UDR) im Resource Manager mit PowerShell](../virtual-network/tutorial-create-route-table-powershell.md).

### <a name="enabling-connectivity-from-vms-restricted-by-using-expressroute"></a>Ermöglichen der Verbindungsherstellung von virtuellen Computern, deren Konnektivität mithilfe von ExpressRoute eingeschränkt wurde
Wenn eine ExpressRoute-Verbindung konfiguriert wird, können die lokalen Server über öffentliches Peering auf Data Lake Storage Gen1 zugreifen. Weitere Informationen zum Konfigurieren von ExpressRoute für öffentliches Peering stehen unter [ExpressRoute – FAQ](../expressroute/expressroute-faqs.md) zur Verfügung.

## <a name="see-also"></a>Weitere Informationen
* [Übersicht über Azure Data Lake Storage Gen1](data-lake-store-overview.md)
* [Sichern von in Azure Data Lake Storage Gen1 gespeicherten Daten](data-lake-store-security-overview.md)

