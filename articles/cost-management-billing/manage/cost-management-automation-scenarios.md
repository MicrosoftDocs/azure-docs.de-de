---
title: Automatisierungsszenarien zur Abrechnungs- und Kostenverwaltung in Azure
description: Erfahren Sie, wie gängige Abrechnungs- und Kostenverwaltungsszenarien verschiedenen APIs zugeordnet werden.
author: bandersmsft
ms.reviewer: adwise
tags: billing
ms.service: cost-management-billing
ms.subservice: common
ms.topic: reference
ms.date: 01/26/2021
ms.author: banders
ms.openlocfilehash: 12c13b8a65296fb0ee74e0ee0449b604facf2f48
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99051260"
---
# <a name="automation-scenarios-for-billing-and-cost-management"></a>Automatisierungsszenarien zur Abrechnungs- und Kostenverwaltung

In diesem Artikel werden allgemeine Szenarien für die Abrechnungs- und Kostenverwaltung für Azure aufgelistet. Diese Szenarien werden APIs zugeordnet, die Sie verwenden können. Unter jeder Zuordnung eines Szenarios zu einer API finden Sie einen Überblick über die APIs und ihre Funktionalität.

## <a name="common-scenarios"></a>Häufige Szenarios

Sie können die APIs für Abrechnung und Kostenverwaltung in mehreren Szenarios verwenden, um kosten- und nutzungsbezogene Fragen zu beantworten. Hier finden Sie einen Überblick über gängige Szenarien:

- **Rechnungsabgleich**: Hat Microsoft den richtigen Betrag berechnet?  Wie sieht meine Rechnung aus, und kann ich den Betrag selbst berechnen?

- **Weiterbelastung**: Jetzt weiß ich, was mir in Rechnung gestellt wird. Wer in meiner Organisation muss diesen Rechnungsbetrag zahlen?

- **Kostenoptimierung:** Ich weiß, wie viel mir berechnet wurde. Wie kann ich mehr aus dem Geld herausholen, das ich für Azure ausgebe?

- **Kostenverfolgung**: Ich möchte ermitteln, wie viel ich im Lauf der Zeit für Azure ausgebe und wie ich Azure nutze. Welche Trends gibt es? Wie kann ich dies optimieren?

- **Azure-Ausgaben während des Monats**: Wie hoch sind meine Ausgaben im aktuellen Monat bis jetzt? Muss ich Änderungen hinsichtlich meiner Ausgaben für und/oder Nutzung von Azure vornehmen? Wann nutze ich im Lauf des Monats Azure am meisten?

- **Warnungen**: Wie kann ich ressourcenbasierte Nutzungswarnungen oder kostenbasierte Warnungen einrichten?

## <a name="scenario-to-api-mapping"></a>Zuordnung eines Szenarios zu einer API

|         API        | Rechnungsabgleich    | Weiterbelastung    | Kostenoptimierung    | Kostenverfolgung    | Ausgaben in der Monatsmitte    | Alerts    |
|:---------------------------:|:-------------------------:|:----------------:|:--------------------:|:----------------:|:------------------:|:---------:|
| Budgets                     |                           |                  |           X          |                  |                    |     X     |
| Marketplace-Gebühren                |             X             |         X        |           X          |         X        |          X         |     X     |
| Preisblatt                 |             X             |         X        |           X          |         X        |          X         |           |
| Reservierungsempfehlungen |                           |                  |           X          |                  |                    |           |
| Reservierungsdetails         |                           |                  |           X          |         X        |                    |           |
| Reservierungszusammenfassungen       |                           |                  |           X          |         X        |                    |           |
| Nutzungsdetails               |             X             |         X        |           X          |         X        |          X         |     X     |
| Abrechnungszeiträume             |             X             |         X        |           X          |         X        |                    |           |
| Invoices                    |             X             |         X        |           X          |         X        |                    |           |
| Azure-Einzelhandelspreise                    |             X             |                  |           X          |         X        |                    |           |


> [!NOTE]
> Die Zuordnungen von Szenarios zu APIs enthalten nicht die Enterprise-Nutzungs-APIs. Verwenden Sie für neue Entwicklungsszenarios möglichst die allgemeinen Nutzung-APIs.

## <a name="api-summaries"></a>API-Zusammenfassungen

### <a name="consumption"></a>Nutzung
Web Direct- und Enterprise-Kunden können alle folgenden APIs verwenden, sofern nicht anders angegeben:

-    [API für Budgets](/rest/api/consumption/budgets) (*Nur Enterprise-Kunden*): Erstellen Sie entweder Kosten- oder Nutzungsbudgets für Ressourcen, Ressourcengruppen oder Abrechnungszähler. Wenn Sie Budgets erstellt haben, können Sie Warnungen konfigurieren, um benachrichtigt zu werden, wenn Sie definierte Budgetschwellenwerte überschritten haben. Sie können auch Aktionen konfigurieren, die auftreten, wenn Sie Budgetsummen erreicht haben.

-    [API für Marketplace-Gebühren](/rest/api/consumption/marketplaces): Rufen Sie Kosten- und Nutzungsdaten für alle Azure Marketplace-Ressourcen (Azure-Partnerangebote) ab. Sie können mit diesen Daten Kosten für alle Marketplace-Ressourcen addieren oder die Kosten/Nutzung für bestimmte Ressourcen untersuchen.

-    [Preisblatt-API](/rest/api/consumption/pricesheet) (*Nur Enterprise-Kunden*): Sie erhalten benutzerdefinierte Preise für alle Verbrauchseinheiten. Unternehmen können diese Daten in Kombination mit Nutzungsdetails und Marketplace-Nutzungsinformationen verwenden, um Kosten anhand von Nutzungs- und Marketplace-Daten zu berechnen.

-    [API für Reservierungsempfehlungen](/rest/api/consumption/reservationrecommendations): Sie erhalten Empfehlungen für den Kauf reservierter VM-Instanzen. Mit Empfehlungen können Sie erwartete Kosteneinsparungen und Kaufbeträge analysieren. Weitere Informationen finden Sie unter [APIs für die Automatisierung von Azure-Reservierungen](../reservations/reservation-apis.md).

-    [API für Reservierungsdetails](/rest/api/consumption/reservationsdetails): Informationen zu bereits erworbenen VM-Reservierungen werden angezeigt, z.B. reservierte Nutzung im Vergleich zu in Anspruch genommener Nutzung. Sie können die Daten auf VM-Ebene im Detail anzeigen. Weitere Informationen finden Sie unter [APIs für die Automatisierung von Azure-Reservierungen](../reservations/reservation-apis.md).

-    [API für Reservierungszusammenfassungen](/rest/api/consumption/reservationssummaries): Aggregierte Informationen zu VM-Reservierungen, die Ihre Organisation erworben hat, werden angezeigt, z.B. reservierte Nutzung im Vergleich zu im Aggregat in Anspruch genommener Nutzung. Weitere Informationen finden Sie unter [APIs für die Automatisierung von Azure-Reservierungen](../reservations/reservation-apis.md).

-    [Nutzungsdetails-API](/rest/api/consumption/usagedetails): Rufen Sie Kosten- und Nutzungsdaten für alle Azure-Ressourcen von Microsoft ab. Die Informationen liegen in Form von Nutzungsdetail-Datensätzen vor, die zurzeit einmal pro Verbrauchszähler und Tag ausgegeben werden. Mit diesen Informationen können Sie die Kosten für alle Ressourcen addieren oder die Kosten/Nutzung für bestimmte Ressourcen untersuchen.

-    [Azure-Einzelhandelspreise](/rest/api/cost-management/retail-prices/azure-retail-prices): Rufen Sie Gebühren für Verbrauchseinheiten mit nutzungsbasierter Bezahlung ab. Sie können dann die zurückgegebenen Informationen mit ihren Ressourcenverbrauchsinformationen verwenden, um die zu erwartende Rechnung manuell zu berechnen.

### <a name="billing"></a>Abrechnung
-    [API für Abrechnungszeiträume](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods): Bestimmen Sie einen zu analysierenden Abrechnungszeitraum zusammen mit den Rechnungs-IDs für diesen Zeitraum. Sie können die Rechnung-IDs mit der Rechnungen-API verwenden.

-    [Rechnungen-API](/rest/api/billing/2019-10-01-preview/invoices): Rufen Sie die Download-URL für eine Rechnung für einen bestimmten Abrechnungszeitraum im PDF-Format ab.

### <a name="enterprise-consumption"></a>Enterprise-Verbrauch
Die folgenden APIs sind nur für Unternehmen bestimmt:

-    [Saldozusammenfassungs-API](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary): Rufen Sie eine monatliche Übersicht über Informationen zu Salden, neuen Käufen, Gebühren für den Azure Marketplace-Dienst, Korrekturen und Überschreitungsgebühren ab. Sie können diese Informationen für den aktuellen Abrechnungszeitraum oder einen beliebigen Zeitraum in der Vergangenheit abrufen. Unternehmen können diese Daten mit manuell berechneten Summengebühren vergleichen. Diese API stellt keine ressourcenspezifische Informationen und keine Aggregatansicht der Kosten bereit.

-    [Nutzungsdetails-API](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail): Rufen Sie Informationen über die Azure-Nutzung (von Microsoft-Angeboten) für den aktuellen Monat, einen bestimmten Abrechnungszeitraum oder einen benutzerdefinierten Datumszeitraum ab. Unternehmen können mit diesen Daten Rechnungen basierend auf Rate und Verbrauch manuell berechnen. Unternehmen können auch mit den Abteilungs-/Organisationsinformationen Kosten Organisationen übergreifend zuordnen. Die Daten stellen eine ressourcenspezifische Ansicht von Nutzung/Kosten bereit.

-    [API für Marketplace Store-Gebühren](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge): Rufen Sie Informationen über die Azure-Nutzung (von Partnerangeboten) für den aktuellen Monat, einen bestimmten Abrechnungszeitraum oder einen benutzerdefinierten Datumszeitraum ab. Unternehmen können mit diesen Daten Rechnungen basierend auf Rate und Verbrauch manuell berechnen. Unternehmen können auch mit den Abteilungs-/Organisationsinformationen Kosten Organisationen übergreifend zuordnen. Diese API stellt eine ressourcenspezifische Ansicht von Nutzung/Kosten bereit.

-    [Preisblatt-API](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet): Rufen Sie den zutreffenden Tarif für jede Verbrauchseinheit für den angegebenen Registrierungs- und Abrechnungszeitraum ab. Mit diesen Tarifinformationen können Sie in Kombination mit Nutzungsdetails und Marketplace-Nutzungsinformationen die zu erwartende Rechnung manuell berechnen.

-    [API für Abrechnungszeiträume](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods): Rufen Sie eine Liste von Abrechnungszeiträumen ab. Die API bietet Ihnen außerdem eine Eigenschaft, die auf die API-Route für die vier Sätze von Enterprise-API-Daten verweist, die sich auf den Abrechnungszeitraum beziehen: BalanceSummary, UsageDetails, Marketplace Charges und PriceSheet.

-    [API für Empfehlungen für reservierte Instanzen](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation): Betrachten Sie 7 Tage, 30 Tage oder 60 Tage VM-Nutzung, und Sie erhalten Empfehlungen für Einzel- und gemeinsame Käufe. Mit dieser API können Sie zu erwartende Kosteneinsparungen und empfohlene Kaufbeträge analysieren. Weitere Informationen finden Sie unter [APIs für die Automatisierung von Azure-Reservierungen](../reservations/reservation-apis.md).

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

### <a name="whats-the-difference-between-the-enterprise-reporting-apis-and-the-consumption-apis-when-should-i-use-each"></a>Worin besteht der Unterschied zwischen den Enterprise-Berichterstellungs-APIs und den Nutzungs-APIs? Wann sollte ich diese jeweils verwenden?
Diese APIs weisen ähnliche Funktionen auf und können die gleiche Bandbreite an Fragen im Abrechnungs- und Kostenverwaltungsbereich beantworten. Aber sie richten sich an verschiedene Zielgruppen:

- Enterprise-Berichterstellungs-APIs stehen für Kunden zur Verfügung, die ein Enterprise Agreement mit Microsoft geschlossen haben, das ihnen Zugang zu ausgehandelten Azure-Vorauszahlungen (zuvor als „Mindestverbrauch“ bezeichnet) und benutzerdefinierten Preisen gewährt. Für die APIs ist ein Schlüssel erforderlich, den Sie aus dem [Enterprise Portal](https://ea.azure.com) abrufen können. Eine Beschreibung dieser APIs finden Sie unter [Überblick über Berichterstellungs-APIs für Enterprise-Kunden](enterprise-api.md).

- Nutzungs-APIs sind mit einigen Ausnahmen für alle Kunden verfügbar. Weitere Informationen finden Sie unter [Azure-Nutzungs-API: Übersicht](consumption-api-overview.md) und [Azure-Verbrauch](/rest/api/consumption/). Die bereitgestellten APIs werden als Lösung für die aktuellen Entwicklungsszenarien empfohlen.

### <a name="whats-the-difference-between-the-invoice-api-and-the-usage-details-api"></a>Worin besteht der Unterschied zwischen der Rechnungs-API und der Nutzungsdetails-API?
Diese APIs bieten unterschiedliche Ansichten derselben Daten:

- Die [Rechnungs-API](/rest/api/billing/2019-10-01-preview/invoices) ist nur für Web-Direct-Kunden bestimmt. Sie bietet ein monatliches Rollup Ihrer Rechnung basierend auf den aggregierten Gebühren für jeden Verbrauchseinheitentyp.

- Die [Nutzungsdetails-API](/rest/api/consumption/usagedetails) bietet eine präzise Ansicht der Nutzung/Kosten-Datensätze für jeden Tag. Sowohl Enterprise- als auch Web Direct-Kunden können sie nutzen.

### <a name="whats-the-difference-between-the-price-sheet-api-and-the-ratecard-api"></a>Worin besteht der Unterschied zwischen der Preisblatt-API und der RateCard-API?
Diese APIs stellen ähnliche Datasets bereit, sind jedoch auf unterschiedliche Zielgruppen ausgerichtet:

- Die [Preisblatt-API](/rest/api/consumption/pricesheet) stellt die benutzerdefinierten Preise bereit, die für einen Enterprise-Kunden ausgehandelt wurden.

- Die [API für Azure-Einzelhandelspreise](/rest/api/cost-management/retail-prices/azure-retail-prices) verfügt über die Möglichkeit zur öffentlichen nutzungsbasierten Bezahlung, die für Web Direct-Kunden gilt.

## <a name="next-steps"></a>Nächste Schritte

- Informationen zur Verwendung von REST-APIs zum Abrufen von Preisen für alle Azure-Dienste finden Sie unter [Übersicht über Azure-Einzelhandelspreise](/rest/api/cost-management/retail-prices/azure-retail-prices).

- Um Ihre Rechnung mit der Datei mit den ausführlichen Daten zur täglichen Nutzung und den Kostenverwaltungsberichten im Azure-Portal zu vergleichen, lesen Sie [Grundlegendes zu Ihrer Rechnung für Microsoft Azure](../understand/review-individual-bill.md).

- Wenn Sie weitere Fragen haben oder Hilfe benötigen, [erstellen Sie eine Supportanfrage](https://go.microsoft.com/fwlink/?linkid=2083458).