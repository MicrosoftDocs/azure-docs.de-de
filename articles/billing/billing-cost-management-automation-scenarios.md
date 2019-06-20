---
title: Automatisierungsszenarien für Azure zur Abrechnungs- und Kostenverwaltung | Microsoft-Dokumentation
description: Erfahren Sie, wie gängige Abrechnungs- und Kostenverwaltungsszenarien verschiedenen APIs zugeordnet werden.
services: billing
documentationcenter: ''
author: Erikre
manager: dougeby
editor: ''
tags: billing
ms.assetid: 204b15b2-6667-4b6c-8ea4-f32c06f287fd
ms.service: billing
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 6/13/2018
ms.author: erikre
ms.openlocfilehash: cb7a13d9abcf7c677d51f03df002ea06b543014e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65232484"
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
| RateCard                    |             X             |                  |           X          |         X        |          X         |           |
| Nicht bewertete Nutzung               |             X             |                  |           X          |                  |          X         |           |

> [!NOTE]
> Die Zuordnungen von Szenarios zu APIs enthalten nicht die Enterprise-Nutzungs-APIs. Verwenden Sie für neue Entwicklungsszenarios möglichst die allgemeinen Nutzung-APIs.

## <a name="api-summaries"></a>API-Zusammenfassungen

### <a name="consumption"></a>Nutzung
Web Direct- und Enterprise-Kunden können alle folgenden APIs verwenden, sofern nicht anders angegeben:

-   [API für Budgets](https://docs.microsoft.com/rest/api/consumption/budgets) (*Nur Enterprise-Kunden*): Erstellen Sie entweder Kosten- oder Nutzungsbudgets für Ressourcen, Ressourcengruppen oder Abrechnungszähler. Wenn Sie Budgets erstellt haben, können Sie Warnungen konfigurieren, um benachrichtigt zu werden, wenn Sie definierte Budgetschwellenwerte überschritten haben. Sie können auch Aktionen konfigurieren, die auftreten, wenn Sie Budgetsummen erreicht haben.

-   [API für Marketplace-Gebühren](https://docs.microsoft.com/rest/api/consumption/marketplaces): Rufen Sie Kosten- und Nutzungsdaten für alle Azure Marketplace-Ressourcen (Azure-Partnerangebote) ab. Sie können mit diesen Daten Kosten für alle Marketplace-Ressourcen addieren oder die Kosten/Nutzung für bestimmte Ressourcen untersuchen.

-   [Preisblatt-API](https://docs.microsoft.com/rest/api/consumption/pricesheet) (*Nur Enterprise-Kunden*): Sie erhalten benutzerdefinierte Preise für alle Verbrauchseinheiten. Unternehmen können diese Daten in Kombination mit Nutzungsdetails und Marketplace-Nutzungsinformationen verwenden, um Kosten anhand von Nutzungs- und Marketplace-Daten zu berechnen. 

-   [API für Reservierungsempfehlungen](https://docs.microsoft.com/rest/api/consumption/reservationrecommendations): Sie erhalten Empfehlungen für den Kauf reservierter VM-Instanzen. Mit Empfehlungen können Sie erwartete Kosteneinsparungen und Kaufbeträge analysieren. Weitere Informationen finden Sie unter [APIs für die Automatisierung von Azure-Reservierungen](billing-reservation-apis.md).

-   [API für Reservierungsdetails](https://docs.microsoft.com/rest/api/consumption/reservationsdetails): Informationen zu bereits erworbenen VM-Reservierungen werden angezeigt, z.B. reservierte Nutzung im Vergleich zu in Anspruch genommener Nutzung. Sie können die Daten auf VM-Ebene im Detail anzeigen. Weitere Informationen finden Sie unter [APIs für die Automatisierung von Azure-Reservierungen](billing-reservation-apis.md).

-   [API für Reservierungszusammenfassungen](https://docs.microsoft.com/rest/api/consumption/reservationssummaries): Aggregierte Informationen zu VM-Reservierungen, die Ihre Organisation erworben hat, werden angezeigt, z.B. reservierte Nutzung im Vergleich zu im Aggregat in Anspruch genommener Nutzung. Weitere Informationen finden Sie unter [APIs für die Automatisierung von Azure-Reservierungen](billing-reservation-apis.md).

-   [Nutzungsdetails-API](https://docs.microsoft.com/rest/api/consumption/usagedetails): Rufen Sie Kosten- und Nutzungsdaten für alle Azure-Ressourcen von Microsoft ab. Die Informationen liegen in Form von Nutzungsdetail-Datensätzen vor, die zurzeit einmal pro Verbrauchszähler und Tag ausgegeben werden. Mit diesen Informationen können Sie die Kosten für alle Ressourcen addieren oder die Kosten/Nutzung für bestimmte Ressourcen untersuchen.

-   [RateCard-API](/previous-versions/azure/reference/mt219005(v=azure.100)): Rufen Sie Gebühren für Verbrauchseinheiten ab, wenn Sie Web Direct-Kunde sind. Sie können dann die zurückgegebenen Informationen mit ihren Ressourcenverbrauchsinformationen verwenden, um die zu erwartende Rechnung manuell zu berechnen. 

-   [API für nicht bewertete Nutzung](/previous-versions/azure/reference/mt219003(v=azure.100)): Rufen Sie Rohnutzungsdaten ab, bevor Azure Messungen/Gebührenberechnungen durchführt.

### <a name="billing"></a>Abrechnung
-   [API für Abrechnungszeiträume](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-billing-periods): Bestimmen Sie einen zu analysierenden Abrechnungszeitraum zusammen mit den Rechnungs-IDs für diesen Zeitraum. Sie können die Rechnung-IDs mit der Rechnungen-API verwenden.

-   [Rechnungen-API](https://docs.microsoft.com/rest/api/billing/2018-11-01-preview/invoices): Rufen Sie die Download-URL für eine Rechnung für einen bestimmten Abrechnungszeitraum im PDF-Format ab.

### <a name="enterprise-consumption"></a>Enterprise-Verbrauch
Die folgenden APIs sind nur für Unternehmen bestimmt:

-   [Saldozusammenfassungs-API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-balance-summary): Rufen Sie eine monatliche Übersicht über Informationen zu Salden, neuen Käufen, Gebühren für den Azure Marketplace-Dienst, Korrekturen und Überschreitungsgebühren ab. Sie können diese Informationen für den aktuellen Abrechnungszeitraum oder einen beliebigen Zeitraum in der Vergangenheit abrufen. Unternehmen können diese Daten mit manuell berechneten Summengebühren vergleichen. Diese API stellt keine ressourcenspezifische Informationen und keine Aggregatansicht der Kosten bereit.

-   [Nutzungsdetails-API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail): Rufen Sie Informationen über die Azure-Nutzung (von Microsoft-Angeboten) für den aktuellen Monat, einen bestimmten Abrechnungszeitraum oder einen benutzerdefinierten Datumszeitraum ab. Unternehmen können mit diesen Daten Rechnungen basierend auf Rate und Verbrauch manuell berechnen. Unternehmen können auch mit den Abteilungs-/Organisationsinformationen Kosten Organisationen übergreifend zuordnen. Die Daten stellen eine ressourcenspezifische Ansicht von Nutzung/Kosten bereit.

-   [API für Marketplace Store-Gebühren](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge): Rufen Sie Informationen über die Azure-Nutzung (von Partnerangeboten) für den aktuellen Monat, einen bestimmten Abrechnungszeitraum oder einen benutzerdefinierten Datumszeitraum ab. Unternehmen können mit diesen Daten Rechnungen basierend auf Rate und Verbrauch manuell berechnen. Unternehmen können auch mit den Abteilungs-/Organisationsinformationen Kosten Organisationen übergreifend zuordnen. Diese API stellt eine ressourcenspezifische Ansicht von Nutzung/Kosten bereit.

-   [Preisblatt-API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-pricesheet): Rufen Sie den zutreffenden Tarif für jede Verbrauchseinheit für den angegebenen Registrierungs- und Abrechnungszeitraum ab. Mit diesen Tarifinformationen können Sie in Kombination mit Nutzungsdetails und Marketplace-Nutzungsinformationen die zu erwartende Rechnung manuell berechnen.

-   [API für Abrechnungszeiträume](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-billing-periods): Rufen Sie eine Liste von Abrechnungszeiträumen ab. Die API bietet Ihnen außerdem eine Eigenschaft, die auf die API-Route für die vier Sätze von Enterprise-API-Daten verweist, die sich auf den Abrechnungszeitraum beziehen: BalanceSummary, UsageDetails, Marketplace Charges und PriceSheet.

-   [API für Empfehlungen für reservierte Instanzen](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation): Betrachten Sie 7 Tage, 30 Tage oder 60 Tage VM-Nutzung, und Sie erhalten Empfehlungen für Einzel- und gemeinsame Käufe. Mit dieser API können Sie zu erwartende Kosteneinsparungen und empfohlene Kaufbeträge analysieren. Weitere Informationen finden Sie unter [APIs für die Automatisierung von Azure-Reservierungen](billing-reservation-apis.md).

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

### <a name="whats-the-difference-between-the-enterprise-reporting-apis-and-the-consumption-apis-when-should-i-use-each"></a>Worin besteht der Unterschied zwischen den Enterprise-Berichterstellungs-APIs und den Nutzungs-APIs? Wann sollte ich diese jeweils verwenden?
Diese APIs weisen ähnliche Funktionen auf und können die gleiche Bandbreite an Fragen im Abrechnungs- und Kostenverwaltungsbereich beantworten. Aber sie richten sich an verschiedene Zielgruppen: 

- Enterprise-Berichterstellungs-APIs stehen für Kunden zur Verfügung, die ein Enterprise Agreement mit Microsoft geschlossen haben, das ihnen Zugang zu ausgehandelten finanziellen Verpflichtungen und benutzerdefinierten Preisen gewährt. Für die APIs ist ein Schlüssel erforderlich, den Sie aus dem [Enterprise Portal](https://ea.azure.com) abrufen können. Eine Beschreibung dieser APIs finden Sie unter [Überblick über Berichterstellungs-APIs für Enterprise-Kunden](billing-enterprise-api.md).

- Nutzungs-APIs sind mit einigen Ausnahmen für alle Kunden verfügbar. Weitere Informationen finden Sie unter [Azure-Nutzungs-API: Übersicht](billing-consumption-api-overview.md) und [Azure-Verbrauch](https://docs.microsoft.com/rest/api/consumption/). Die bereitgestellten APIs werden als Lösung für die aktuellen Entwicklungsszenarien empfohlen. 

### <a name="whats-the-difference-between-the-usage-details-api-and-the-usage-api"></a>Worin besteht der Unterschied zwischen der Nutzungsdetails-API und der Nutzungs-API?
Diese APIs stellen im Grundsatz verschiedene Daten bereit:

- Die [Nutzungsdetails-API](https://docs.microsoft.com/rest/api/consumption/usagedetails) bietet Informationen zur Nutzung und den Kosten von Azure auf Basis von Verbrauchseinheiten. Die bereitgestellten Daten haben bereits das System zur Zählung von Kostenverbrauchseinheiten in Azure durchlaufen. Kosten sowie andere mögliche Änderungen wurden auf diese angewendet:

   - Änderungen am Konto für die Verwendung der im Voraus bezahlten finanziellen Verpflichtungen
   - Änderungen am Konto für die Verwendung der von Azure ermittelten Abweichungen

- Die [Nutzungs-API](/previous-versions/azure/reference/mt219003(v=azure.100)) bietet Informationen zu Azure-Nutzungsrohdaten, bevor diese das Kostenerfassungssystem in Azure durchlaufen. Diese Daten weisen möglicherweise keine Korrelation mit dem Nutzungsumfang oder dem Gebührenbetrag auf, der nach dem Kostenerfassungssystem von Azure zu sehen ist.

### <a name="whats-the-difference-between-the-invoice-api-and-the-usage-details-api"></a>Worin besteht der Unterschied zwischen der Rechnungs-API und der Nutzungsdetails-API?
Diese APIs bieten unterschiedliche Ansichten derselben Daten:

- Die [Rechnungs-API](https://docs.microsoft.com/rest/api/billing/2018-11-01-preview/invoices) ist nur für Web-Direct-Kunden bestimmt. Sie bietet ein monatliches Rollup Ihrer Rechnung basierend auf den aggregierten Gebühren für jeden Verbrauchseinheitentyp. 

- Die [Nutzungsdetails-API](https://docs.microsoft.com/rest/api/consumption/usagedetails) bietet eine präzise Ansicht der Nutzung/Kosten-Datensätze für jeden Tag. Sowohl Enterprise- als auch Web Direct-Kunden können sie nutzen.

### <a name="whats-the-difference-between-the-price-sheet-api-and-the-ratecard-api"></a>Worin besteht der Unterschied zwischen der Preisblatt-API und der RateCard-API?
Diese APIs stellen ähnliche Datasets bereit, sind jedoch auf unterschiedliche Zielgruppen ausgerichtet:

- Die [Preisblatt-API](https://docs.microsoft.com/rest/api/consumption/pricesheet) stellt die benutzerdefinierten Preise bereit, die für einen Enterprise-Kunden ausgehandelt wurden.

- Die [RateCard-API](/previous-versions/azure/reference/mt219005(v=azure.100)) stellt die öffentlichen Preise für Web Direct-Kunden bereit.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zur Verwendung der Azure-APIs, um programmgesteuert Einblick in Ihre Azure-Nutzung zu erhalten, finden Sie unter [Azure-Nutzungs-API: Übersicht](billing-consumption-api-overview.md) und [Programmgesteuertes Abrufen von Informationen zu Ihrer Azure-Nutzung mit Azure-Abrechnungs-APIs](billing-usage-rate-card-overview.md).

- Um Ihre Rechnung mit der Datei mit den ausführlichen Daten zur täglichen Nutzung und den Kostenverwaltungsberichten im Azure-Portal zu vergleichen, lesen Sie [Grundlegendes zu Ihrer Rechnung für Microsoft Azure](billing-understand-your-bill.md).

- Wenn Sie weitere Fragen haben oder Hilfe benötigen, [erstellen Sie eine Supportanfrage](https://go.microsoft.com/fwlink/?linkid=2083458).
