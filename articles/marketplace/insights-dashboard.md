---
title: Dashboard „Marketplace-Erkenntnisse“ in Analysen für den kommerziellen Marketplace
description: Nutzen Sie eine Zusammenfassung der Marketplace-Webanalysen im Partner Center, mit denen Sie die Kundeninteraktion in Microsoft AppSource- und Azure Marketplace messen können.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 11/09/2020
author: sayantanroy83
ms.author: sroy
ms.openlocfilehash: 9089fee7565633b198043efeed929fc7446437d5
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96458288"
---
# <a name="marketplace-insights-dashboard-in-commercial-marketplace-analytics"></a>Dashboard „Marketplace-Erkenntnisse“ in Analysen für den kommerziellen Marketplace

Dieser Artikel bietet Informationen zum Dashboard „Marketplace-Erkenntnisse“ im Partner Center. In diesem Dashboard wird eine Zusammenfassung der Webanalysen im kommerziellen Marketplace angezeigt, mit deren Hilfe Herausgeber die Kundeninteraktion mit ihren jeweiligen Produktdetailseiten messen können, die in den Onlineshops des kommerziellen Marketplace aufgelistet sind: Microsoft AppSource und Azure Marketplace.

Um auf das Dashboard **Marketplace-Erkenntnisse** im Partner Center zuzugreifen, wählen Sie unter „Kommerzieller Marketplace“ **[Analyse](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)**  > **Marketplace-Erkenntnisse** aus.

Ausführliche Definitionen der Analyseterminologie finden Sie unter [Analysen für den kommerziellen Marketplace: Häufig gestellte Fragen und Terminologie](./analytics-faq.md).

## <a name="marketplace-insights-dashboard"></a>Dashboard „Marketplace-Erkenntnisse“

Das Dashboard „Marketplace-Erkenntnisse“ bietet eine Übersicht über die Geschäftsleistung der Angebote von Azure Marketplace und AppSource. Dieses Dashboard bietet eine umfassende Übersicht über Folgendes:

- Seitenbesuchstrend
- Handlungsaufforderungstrend
- Seitenbesuche und Handlungsaufforderungen im Vergleich zu Angeboten, Empfehlungsdomänen und Kampagnen-IDs
- Marketplace-Erkenntnisse nach Geografie
- Marketplace-Erkenntnisse – Tabelle „Details“

Das Marketplace Insights-Dashboard bietet Clickstreamdaten, die nicht mit Leads korreliert werden dürfen, die auf dem Leadzielendpunkt generiert werden.

> [!NOTE]
> Die maximale Latenz zwischen dem Zeitpunkt, zu dem sich Benutzer Angebote im Azure Marketplace oder in AppSource ansehen, und der Berichterstattung im Partner Center beträgt 48 Stunden.

## <a name="elements-of-the-marketplace-insights-dashboard"></a>Elemente des Dashboards „Marketplace-Erkenntnisse“

Das Dashboard „Marketplace-Erkenntnisse“ zeigt Webtelemetriedetails für Azure Marketplace und AppSource auf zwei separaten Registerkarten an. In den folgenden Abschnitten wird beschrieben, wie das Dashboard „Marketplace-Erkenntnisse“ verwendet und wie die Daten gelesen werden.

### <a name="month-range"></a>Monatsbereich

Oben rechts auf jeder Seite finden Sie eine Monatsbereichsauswahl. Passen Sie die Ausgabe der Diagramme auf der Seite **Marketplace-Erkenntnisse** an, indem Sie einen Monatsbereich basierend auf den letzten 6 oder 12 Monaten oder einen benutzerdefinierten Monatsbereich mit einer maximalen Dauer von 12 Monaten auswählen. Der standardmäßige Monatsbereich (Berechnungszeitraum) beträgt sechs Monate.

:::image type="content" source="./media/insights-dashboard/month-filters.png" alt-text="Veranschaulicht die Monatsfilter im Dashboard „Marketplace-Erkenntnisse“.":::

> [!NOTE]
> Alle Metriken in den Visualisierungswidgets und Exportberichten berücksichtigen den vom Benutzer ausgewählten Berechnungszeitraum.

### <a name="page-visits-trends"></a>Seitenbesuchstrends

Im Diagramm **Besucher** von „Marketplace-Erkenntnisse“ wird die Anzahl der _Seitenbesuche_ und _Unterschiedliche Besucher_ für den ausgewählten Berechnungszeitraum angezeigt.

**Seitenbesuche**: Dieser Wert stellt die Anzahl unterschiedlicher Benutzersitzungen auf der Angebotsauflistungsseite (Produktdetailseite) für einen ausgewählten Berechnungszeitraum dar. Die roten und grünen Prozentangaben stellen das prozentuale Wachstum der Seitenbesuche dar. Im Trenddiagramm wird die monatliche Anzahl von Seitenbesuchen dargestellt.

**Unterschiedliche Besucher**: Diese Zahl stellt die Anzahl unterschiedlicher Besucher während des ausgewählten Berechnungszeitraums für die Angebote in Azure Marketplace und AppSource dar. Ein Besucher, der mindestens eine Produktdetailseite besucht hat, wird als ein individueller Benutzer gezählt.

[![Veranschaulicht das Diagramm „Besucher“ im Dashboard „Marketplace-Erkenntnisse“.](./media/insights-dashboard/visitors.png)](./media/insights-dashboard/visitors.png#lightbox)

### <a name="call-to-actions-trend"></a>Handlungsaufforderungstrend

Dieser Wert steht für die Anzahl von Klicks auf die Schaltfläche **Handlungsaufforderung**, die auf der Angebotsauflistungsseite (Produktdetailseite) ausgeführt wurden. _Handlungsaufforderungen_ werden gezählt, wenn Benutzer die Schaltfläche **Jetzt abrufen**, **Kostenlose Testversion**, **Kontakt mit mir aufnehmen** oder **Testversion** auswählen.

[![Veranschaulicht das Diagramm „Handlungsaufforderung“ im Dashboard „Marketplace-Erkenntnisse“.](./media/insights-dashboard/call-to-actions-trend.png)](./media/insights-dashboard/call-to-actions-trend.png#lightbox)

### <a name="page-visits-and-call-to-actions-against-offers-referral-domains-and-campaign-ids"></a>Seitenbesuche und Handlungsaufforderungen im Vergleich zu Angeboten, Empfehlungsdomänen und Kampagnen-IDs

**Empfehlungsdomänen**: Wenn Sie eine bestimmte Empfehlungsdomäne auswählen, wird der monatliche Trend von Seitenbesuchen und Handlungsaufforderungen im Diagramm auf der rechten Seite angezeigt.

:::image type="content" source="./media/insights-dashboard/referral-domain.png" alt-text="Veranschaulicht das Diagramm „Empfehlungsdomäne“ im Dashboard „Marketplace-Erkenntnisse“.":::

**Angebote**: Wählen Sie ein bestimmtes Angebot aus, um den monatlichen Trend von Seitenbesuchen und Handlungsaufforderungen im Diagramm auf der rechten Seite anzuzeigen.

:::image type="content" source="./media/insights-dashboard/offer-alias.png" alt-text="Veranschaulicht das Diagramm „Angebotsalias“ im Dashboard „Marketplace-Erkenntnisse“.":::

**Kampagnen-IDs**: Wenn Sie eine bestimmte Kampagnen-ID auswählen, sollten Sie den Erfolg der Kampagne nachvollziehen können. Für jede Kampagne sollte der monatliche Trend von Seitenbesuchen und Handlungsaufforderungen im Diagramm auf der rechten Seite angezeigt werden.

:::image type="content" source="./media/insights-dashboard/campaign.png" alt-text="Veranschaulicht das Kampagnendiagramm im Dashboard „Marketplace-Erkenntnisse“.":::

### <a name="marketplace-insights-by-geography"></a>Marketplace-Erkenntnisse nach Geografie

Für den ausgewählten Berechnungszeitraum zeigt das Wärmebild die Anzahl von Seitenbesuchen, unterschiedlichen Besuchern und Handlungsaufforderungen (Calls To Action, CTA) an. Die Helligkeit der Farbe auf der Karte entspricht einer niedrigen (hell) bis hohen (dunkel) Anzahl unterschiedlicher Besucher. Wählen Sie einen Datensatz in der Tabelle aus, um ein Land/eine Region zu vergrößern.

:::image type="content" source="./media/insights-dashboard/geographical-spread.png" alt-text="Veranschaulicht das Diagramm der geografischen Verteilung im Dashboard „Marketplace-Erkenntnisse“.":::

Beachten Sie Folgendes:

- Sie können die Karte verschieben, um den genauen Standort anzuzeigen.
- Sie können an einen bestimmten Ort zoomen.
- Das Wärmebild verfügt über ein zusätzliches Raster, um Details zur Anzahl der Kunden oder Aufträge und zu den Stunden für die normalisierte Nutzung an einem bestimmten Ort anzuzeigen.
- Sie können ein Land oder eine Region im Raster suchen und auswählen, um an den Ort in der Karte zu zoomen. Um wieder zur ursprünglichen Ansicht zurückzukehren, wählen Sie in der Karte die Schaltfläche **Startseite** aus.

### <a name="marketplace-insights-details-table"></a>Marketplace-Erkenntnisse – Tabelle „Details“

Diese Tabelle enthält eine Listenansicht der Seitenbesuche und Handlungsaufforderungen der Seiten Ihrer ausgewählten Angebote nach Datum sortiert.

- Die Daten können in eine TSV- oder CSV-Datei extrahiert werden, wenn die Anzahl der Einträge unter 1.000 liegt.
- Liegt die Anzahl der Einträge über 1.000, werden die exportierten Daten für die nächsten 30 Tage asynchron auf einer Downloadseite abgelegt.
- Filtern Sie Daten nach Angebotsnamen und Kampagnennamen, um die für Sie interessanten Daten anzuzeigen.

> [!TIP]
> Sie können das Downloadsymbol in der oberen rechten Ecke eines Widgets verwenden, um die Daten herunterzuladen. Sie können Feedback zu jedem der Widgets geben, indem Sie auf das Symbol „Daumen nach oben“ oder „Daumen nach unten“ klicken.

## <a name="next-steps"></a>Nächste Schritte

- Eine Übersicht über die im kommerziellen Marketplace verfügbaren Analyseberichte finden Sie unter [Zugreifen auf Analysen für den kommerziellen Marketplace in Partner Center](./partner-center-portal/analytics.md).
- Informationen zu Ihren Aufträgen in einem grafischen und herunterladbaren Format finden Sie unter [Dashboard „Aufträge“ in Analysen für den kommerziellen Marketplace](./orders-dashboard.md).
- Informationen zu Metriken zur Nutzung und zur getakteten Abrechnung für VM-Angebote finden Sie unter [Dashboard „Nutzung“ in Analysen für den kommerziellen Marketplace](./usage-dashboard.md).
- Ausführliche Informationen zu Ihren Kunden, einschließlich Wachstumstrends, finden Sie unter [Dashboard „Kunde“ in Analysen für den kommerziellen Marketplace](./customer-dashboard.md).
- Eine Liste Ihrer Downloadanforderungen der letzten 30 Tagen finden Sie unter [Dashboard „Downloads“ in Analysen für den kommerziellen Marketplace](./partner-center-portal/downloads-dashboard.md).
- Eine konsolidierte Ansicht des Kundenfeedbacks für Angebote im Azure Marketplace und in AppSource finden Sie unter [Analyse-Dashboard „Bewertungen und Rezensionen“ in Partner Center](./partner-center-portal/ratings-reviews.md).
- Häufig gestellte Fragen zu Analysen für den kommerziellen Marketplace und ein umfassendes Wörterbuch mit Datenbegriffen finden Sie unter [Analysen für den kommerziellen Marketplace: Häufig gestellte Fragen](./analytics-faq.md).