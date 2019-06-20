---
title: Verkäufer-Insights – Anmerkungen zu dieser Version
description: Enthält Informationen zu Änderungen an den Verkäufer-Insights.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 3/3/2019
ms.author: pabutler
ms.openlocfilehash: c6e9e4fe672c7e171ed4b1cd60655f9e71a562e6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64943124"
---
# <a name="seller-insights-release-notes"></a>Verkäufer-Insights – Anmerkungen zu dieser Version 

(Veröffentlichungsdatum: 1. März 2019)

Dieser Artikel enthält Informationen zu Änderungen am Feature für Verkäufer-Insights im [Cloud-Partnerportal](https://cloudpartner.azure.com/#insights).

## <a name="release-highlights-for-march-1-2019"></a>Highlights der Version für 1. März 2019

* *Kundentrend* wurde zu „Zusammenfassung“ hinzugefügt.
* *Top 5-Kunden* unter „Zusammenfassung“ zeigt alle Azure-Abonnements eines Kunden.
* *Normalisierter Nutzungstrend und Trend bei aktiven Aufträgen* unter „Zusammenfassung“ wurde unter *Monatliche Aufträge auf einen Blick* verschoben.
* *Auszahlung „Abstimmungsbericht“* wurde aktualisiert.
* *Top 5-Kunden* unter „Auszahlung“ zeigt alle Azure-Abonnements eines Kunden.
* *Nutzungsbericht* wurde mit „Kunden-ID“ aktualisiert.
* *Kundenbesitz* unter „Aufträge und Nutzung“ zeigt alle Azure-Abonnements eines Kunden.


(Veröffentlichungsdatum: 28. Juli 2018)

## <a name="release-highlights-for-july-28-2018"></a>Highlights der Version für 28. Juli 2018


-   *Geschätzte Preise* bieten einen Überblick über die Kundengebühren mit Auswirkungen auf die Wechselkurse.
-   *Vorhergesagte Auszahlungen* bieten einen vorgezogenen Überblick über potenzielle Auszahlungen.
-  *Nutzungsreferenzbezeichner* sorgen für Datengenauigkeit zwischen Kundennutzung und Bestellungen mit Auszahlungen.
-   *Nutzung nach täglicher Struktur* bietet mehr Granularität und bessere Einblicke in die Nutzung der Kunden.


### <a name="changes-to-data-structure-and-taxonomy"></a>Änderungen an Datenstruktur und Taxonomie

Die folgende Tabelle listet die Metriken auf, die mit dieser Version hinzugefügt oder wesentlich geändert wurden. 

| **Neuer Begriff**                   |    **Definition**                                                             |
|--------------------------------|  ---------------------------------------------------------------------------- |
| Preis (CC)                     | Der Preis für eine Verbrauchseinheit einer bestimmten SKU (in der Währung des Kunden).       |
| Geschätzte erweiterte Gebühren (CC) | Die geschätzten erweiterten Gebühren für die Menge der Verbrauchseinheiten für eine bestimmte SKU (in der Währung des Kunden). Dieser Wert kann aufgrund von Rundungs- oder Kürzungsfehlern nicht exakt sein.   |
| Währung des Herausgebers (Publisher Currency, PC)        | Die vom Herausgeber für Auszahlungen bevorzugte Währung.                               |
| Geschätzter Preis (PC)           | Der geschätzte Preis für eine Verbrauchseinheit für eine bestimmte SKU basierend auf dem Wechselkurs an dem Datum, an dem die Nutzung berechnet wird (in der Währung des Herausgebers). Dieser Wert kann aufgrund von Rundungs- oder Kürzungsfehlern nicht exakt sein.   |
| Geschätzte erweiterte Gebühren (PC) | Die geschätzten erweiterten Gebühren für die Menge der Verbrauchseinheiten für eine bestimmte SKU basierend auf dem Wechselkurs an dem Datum, an dem die Nutzung berechnet wird (in der Währung des Herausgebers). Dieser Wert kann aufgrund von Rundungs- oder Kürzungsfehlern nicht exakt sein. |
| Geschätzte Auszahlung (PC)          | Die geschätzte Zahlung für die Menge der Verbrauchseinheiten für eine bestimmte SKU basierend auf dem Wechselkurs an dem Datum, an dem die Nutzung berechnet wird (in der Währung des Herausgebers). Dieser Wert kann aufgrund von Rundungs- oder Kürzungsfehlern nicht exakt sein.   |
| Nutzungsreferenz                | Der Bezeichner für einen oder mehrere Tage der Nutzung einer bestimmten SKU durch den Kunden, der einem Eintrag im Auszahlungsbericht zugeordnet ist. |
|  |  |
