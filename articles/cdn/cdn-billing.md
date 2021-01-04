---
title: Grundlegendes zur Abrechnung für Azure CDN | Microsoft-Dokumentation
description: Erfahren Sie mehr über die Abrechnungsstruktur für Inhalte, die von Azure Content Delivery Network gehostet werden, einschließlich Abrechnungsregionen, Übermittlungsgebühren und Kostenverwaltung.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/13/2019
ms.author: allensu
ms.openlocfilehash: aa2f00a732a3978524fc017481285859c9535387
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96018680"
---
# <a name="understanding-azure-cdn-billing"></a>Grundlegendes zur Abrechnung für Azure CDN

In diesen häufig gestellten Fragen wird die Abrechnungsstruktur für von Azure Content Delivery Network (CDN) gehostete Inhalte beschrieben.

## <a name="what-is-a-billing-region"></a>Was ist eine Abrechnungsregion?
Eine Abrechnungsregion ist ein geografisches Gebiet, mit dem bestimmt wird, welcher Tarif für die Übermittlung von Objekten aus Azure CDN in Rechnung gestellt wird. Die aktuellen Abrechnungszonen und ihre jeweiligen Regionen lauten wie folgt:

- Zone 1: Nordamerika, Europa, Naher Osten und Afrika

- Zone 2: Asien-Pazifik (einschließlich Japan)

- Zone 3: Südamerika

- Zone 4: Australien und Neuseeland

- Zone 5: Indien

Informationen zu POP-Regionen (Point of Presence) finden Sie unter [Azure CDN-POP-Standorte nach Region](./cdn-pop-locations.md). Ein in Mexiko befindlicher POP gehört beispielsweise zur Region Nordamerika und daher zur Zone 1. 

Weitere Informationen zur Azure CDN-Preisgestaltung finden Sie unter [Azure Content Delivery Network – Preise ](https://azure.microsoft.com/pricing/details/cdn/).

## <a name="how-are-delivery-charges-calculated-by-region"></a>Wie werden Übermittlungsgebühren nach Region berechnet?
Die Azure CDN-Abrechnungsregion basiert auf dem Standort des Quellservers, der den Inhalt an den Endbenutzer übermittelt. Das Ziel (physischer Standort) des Clients wird bei der Abrechnungsregion nicht berücksichtigt.

Wenn beispielsweise ein Benutzer, der sich in Mexiko befindet, eine Anforderung sendet, und diese Anforderung aufgrund von Peering- oder Datenverkehrsbedingungen von einem Server an einem POP in den Vereinigten Staaten bedient wird, sind die Vereinigten Staaten die Abrechnungsregion.

## <a name="what-is-a-billable-azure-cdn-transaction"></a>Was ist eine abrechenbare Azure CDN-Transaktion?
Jede HTTP(S)-Anforderung, die im CDN endet, ist ein abrechenbares Ereignis, das alle Antworttypen einschließt: Erfolg, Fehler oder andere. Allerdings können verschiedene Antworten unterschiedliche Datenverkehrsmengen generieren. Beispielsweise generieren *304 Nicht geändert*-Antworten und andere Antworten, die nur aus einem Header bestehen, nur wenig Datenverkehr, da es sich um kleine Headerantworten handelt; ebenso sind Fehlerantworten (z. B. *404 Nicht gefunden*) zwar abrechenbar, aber aufgrund der kleinen Antwortnutzlast fallen nur geringe Kosten an.

## <a name="what-other-azure-costs-are-associated-with-azure-cdn-use"></a>Welche sonstigen Azure-Kosten fallen für die Azure CDN-Verwendung an?
Durch Verwenden von Azure CDN fallen auch einige Nutzungsgebühren für die Dienste an, die als Ursprung für Ihre Objekte verwendet werden. Diese Kosten belaufen sich in der Regel nur auf ein Bruchteil der gesamten Kosten für die CDN-Nutzung.

Wenn Sie Azure-BLOB-Speicher als Ursprung für Ihre Inhalte verwenden, fallen auch die folgenden Speichergebühren für Cachefüllungen an:

- Tatsächlich verwendete GB: die tatsächliche Speicherung Ihrer Quellobjekte.

- Transaktionen: nach Bedarf, um den Cache zu füllen.

- Übertragungen in GB: die Menge der übertragenen Daten zum Füllen des CDN-Caches.

> [!NOTE]
> Wenn Sie Azure CDN von Microsoft verwenden, ist die Datenübertragung von Ursprüngen, die in Azure zu CDN-POPs gehostet werden, ab Oktober 2019 kostenlos. Azure CDN von Verizon und Azure CDN von Akamai unterliegen den unten beschriebenen Tarifen.

Weitere Informationen zur Azure Storage-Abrechnung finden Sie unter [Understanding Azure Storage Billing – Bandwidth, Transactions, and Capacity](https://blogs.msdn.microsoft.com/windowsazurestorage/2010/07/08/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity/) (Grundlagen zur Azure Storage-Abrechnung – Bandbreite, Transaktionen und Kapazität).

Bei Verwendung der *Übermittlung über einen gehosteten Dienst* fallen die folgenden Gebühren an:

- Azure-Computezeit: die Serverinstanzen, die als Ursprung fungieren.

- Azure-Computeübertragung: die Datenübertragungen von den Compute-Instanzen zum Füllen der Azure CDN-Caches.

Wenn Ihr Client Bytebereichsanforderungen (unabhängig vom ursprünglichen Dienst) verwendet, ergeben sich folgende Aspekte:

- Eine *Bytebereichsanforderung* ist eine abrechenbare Transaktion im CDN. Wenn ein Client eine Bytebereichsanforderung ausgibt, erfolgt diese Anforderung für eine Teilmenge (Bereich) des Objekts. Die Antwort des CDN enthält nur einen Teilbereich des angeforderten Inhalts. Diese Teilantwort ist eine abrechenbare Transaktion, und die übertragene Menge wird auf die Größe der Bereichsantwort (plus Header) begrenzt.

- Beim Empfang einer Anforderung für nur einen Teil eines Objekts (durch Angabe eines Bytebereichsheaders) ruft das CDN möglicherweise das gesamte Objekt in den Cache ab. Daher kann sich die abrechenbare Transaktion vom Ursprung, auch wenn sie vom CDN nur für eine Teilantwort ausgeführt wird, auf die vollständige Größe des Objekts beziehen.

## <a name="how-much-transfer-activity-occurs-to-support-the-cache"></a>Wie viele Übertragungsaktivitäten finden zur Unterstützung des Cache statt?
Jedes Mal, wenn ein CDN POP seinen Cache füllen muss, führt er für das im Cache befindliche Objekt eine Anforderung an den Ursprung aus. Dadurch tritt am Ursprung bei jedem fehlgeschlagenen Zugriff auf den Cache eine abrechenbare Transaktion auf. Die Anzahl der fehlgeschlagenen Zugriffe auf den Cache hängt von mehreren Faktoren ab:

- Cachefähigkeit des Inhalts: Wenn der Inhalt eine lange Gültigkeitsdauer (time-to-live, TTL)/lange Laufzeit hat und häufig auf ihn zugegriffen wird, sodass er oft im Cache verbleibt, wird der Großteil der Last vom CDN übernommen. Eine typische gute Cachetrefferquote beträgt mehr als 90 %, was bedeutet, dass weniger als 10 % der Clientanforderungen entweder aufgrund von fehlgeschlagenen Zugriffen auf den Cache oder Objektaktualisierungen an den Ursprung zurückgegeben werden müssen.

- Anzahl der Knoten, die das Objekt laden müssen: Jedes Mal, wenn ein Knoten ein Objekt aus dem Ursprung lädt, fällt eine abrechenbare Transaktion an. Daher führen mehr globale Inhalte (Zugriff über mehr Knoten) zu mehr abrechenbaren Transaktionen.

- Einfluss der Gültigkeitsdauer (TTL): Eine längere Gültigkeitsdauer für ein Objekt bedeutet, dass es weniger häufig aus dem Ursprung abgerufen werden muss. Dies bedeutet auch, dass Clients (z. B. Browser) das Objekt länger zwischenspeichern können, wodurch sich die Anzahl der Transaktionen zum CDN reduzieren kann.

## <a name="which-origin-services-are-eligible-for-free-data-transfer-with-azure-cdn-from-microsoft"></a>Welche Ursprungsdienste sind für die kostenlose Datenübertragung mit Azure CDN von Microsoft qualifiziert? 
Wenn Sie einen der folgenden Azure-Dienste als CDN-Ursprung verwenden, wird Ihnen die Datenübertragung vom Ursprung an die CDN-POPs nicht in Rechnung gestellt. 

- Azure Storage
- Azure Media Services
- Azure Virtual Machines
- Virtual Network
- Load Balancer
- Application Gateway
- Azure DNS
- ExpressRoute
- VPN Gateway
- Traffic Manager
- Network Watcher
- Azure Firewall
- Azure Front Door Service
- Azure Bastion
- Azure App Service
- Azure-Funktionen
- Azure Data Factory
- Azure API Management
- Azure Batch 
- Azure-Daten-Explorer
- HDInsight
- Azure Cosmos DB
- Azure Data Lake Store
- Azure Machine Learning 
- Azure SQL-Datenbank
- Verwaltete Azure SQL-Instanz
- Azure Cache for Redis

## <a name="how-do-i-manage-my-costs-most-effectively"></a>Wie verwalte ich meine Kosten besonders effektiv?
Legen Sie eine möglichst lange Gültigkeitsdauer für Ihre Inhalte fest.