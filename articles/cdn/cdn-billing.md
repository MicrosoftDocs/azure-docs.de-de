---
title: Grundlegendes zur Abrechnung für Azure CDN | Microsoft-Dokumentation
description: In diesen häufig gestellten Fragen erfahren Sie, wie die Azure CDN-Abrechnung funktioniert.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/20/2018
ms.author: magattus
ms.openlocfilehash: af8e57f39b5b83b1d1be09c29d8b6eb5d49c7b6c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60782059"
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

Informationen zu POP-Regionen (Point of Presence) finden Sie unter [Azure CDN-POP-Standorte nach Region](https://docs.microsoft.com/azure/cdn/cdn-pop-locations). Ein in Mexiko befindlicher POP gehört beispielsweise zur Region Nordamerika und daher zur Zone 1. 

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

- Übertragungen in GB: die Menge der übertragenen Daten zum Füllen des CDN-Caches.

- Transaktionen: nach Bedarf, um den Cache zu füllen.

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

## <a name="how-do-i-manage-my-costs-most-effectively"></a>Wie verwalte ich meine Kosten besonders effektiv?
Legen Sie eine möglichst lange Gültigkeitsdauer für Ihre Inhalte fest. 
