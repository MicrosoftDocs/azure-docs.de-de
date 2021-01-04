---
title: Dashboard „Zusammenfassung“ für Partner Center-Analysen im kommerziellen Marketplace
description: Erfahren Sie, wie Sie über das Dashboard „Zusammenfassung“ im Partner Center auf Diagramme, Trends und Werte von aggregierten Daten zugreifen, mit deren Hilfe Marketplace-Aktivitäten zusammengefasst werden.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/09/2020
author: sayantanroy83
ms.author: sroy
ms.openlocfilehash: c02ba12c790d745904d241f121e269aac3ed12f6
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96462977"
---
# <a name="summary-dashboard-in-commercial-marketplace-analytics"></a>Dashboard „Zusammenfassung“ in Analysen für den kommerziellen Marketplace

Dieser Artikel enthält Informationen zum Dashboard „Zusammenfassung“ im Partner Center. In diesem Dashboard werden Diagramme, Trends und Werte von aggregierten Daten angezeigt, mit deren Hilfe die Marketplace-Aktivitäten für Ihre Angebote zusammengefasst werden.

Um auf das Dashboard „Zusammenfassung“ im Partner Center zuzugreifen, wählen Sie unter **Kommerzieller Marketplace** die Option **[Analysieren](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)**  > **Zusammenfassung** aus.

>[!NOTE]
> Ausführliche Definitionen der Analyseterminologie finden Sie unter [Analysen für den kommerziellen Marketplace: Häufig gestellte Fragen und Terminologie](./analytics-faq.md).

## <a name="summary-dashboard"></a>Dashboard „Zusammenfassung“

Das Dashboard „Zusammenfassung“ bietet eine Übersicht über die Geschäftsleistung der Angebote von Azure Marketplace und Microsoft AppSource. Dieses Dashboard bietet eine umfassende Übersicht über Folgendes:

- Aufträge der Kunden
- Kunden
- Nutzung der Angebote durch Kunden
- Seitenbesuche der Kunden in Azure Marketplace und AppSource

## <a name="elements-of-the-summary-dashboard"></a>Elemente des Dashboards „Zusammenfassung“

In den folgenden Abschnitten wird beschrieben, wie das Dashboard „Zusammenfassung“ verwendet und wie die Daten gelesen werden.

### <a name="month-range"></a>Monatsbereich

Oben rechts auf jeder Seite finden Sie eine Monatsbereichsauswahl. Passen Sie die Ausgabe der Diagramme auf der Seite **Zusammenfassung** an, indem Sie einen Monatsbereich basierend auf den letzten 3, 6 oder 12 Monaten oder einen benutzerdefinierten Monatsbereich mit einer maximalen Dauer von 12 Monaten auswählen. Der standardmäßige Monatsbereich (Berechnungszeitraum) beträgt sechs Monate.

:::image type="content" source="./media/summary-dashboard/summary-dashboard.png" alt-text="Veranschaulicht die Monatsbereichsoptionen auf dem Dashboard „Zusammenfassung“.":::

> [!NOTE]
> Alle Metriken in den Visualisierungswidgets und Exportberichten berücksichtigen den vom Benutzer ausgewählten Berechnungszeitraum.

### <a name="orders-widget"></a>Widget „Aufträge“

Das Widget „Aufträge“ auf dem Dashboard „Zusammenfassung“** zeigt die aktuellen Aufträge für alle Ihre transaktionsbasierten Angebote an. Das Widget „Aufträge“ zeigt eine Anzahl und den Trend aller erworbenen Aufträge (stornierte Aufträge ausgenommen) für den ausgewählten Berechnungszeitraum an. Der Prozentwert **Aufträge** stellt den Zuwachs im ausgewählten Berechnungszeitraum dar.

[![Veranschaulicht das Widget „Aufträge“ auf dem Dashboard „Zusammenfassung“.](./media/summary-dashboard/orders-widget.png)](./media/summary-dashboard/orders-widget.png#lightbox)


Sie können auch zum Bericht „Aufträge“ wechseln, indem Sie in der linken unteren Ecke des Widgets auf den **Dashboard „Aufträge“** -Link klicken.

### <a name="customers-widget"></a>Widget „Kunden“

Im Widget **Kunden** des Dashboards **Zusammenfassung** wird die Gesamtzahl der Kunden angezeigt, die ihre Angebote für den ausgewählten Berechnungszeitraum erworben haben. Das Widget „Kunden“ zeigt eine Anzahl und einen Trend der Gesamtzahl aktiver (einschließlich neuer und vorhandener) Kunden (mit Ausnahme abgewanderter Kunden) für den ausgewählten Berechnungszeitraum an. Der Prozentwert unter **Kunden** stellt den Zuwachs im ausgewählten Berechnungszeitraum dar.

[![Veranschaulicht das Widget „Kunden“ auf dem Dashboard „Zusammenfassung“.](./media/summary-dashboard/customers-widget.png)](./media/summary-dashboard/customers-widget.png#lightbox)

Sie können auch zum detaillierten Bericht „Kunden“ wechseln, indem Sie in der linken unteren Ecke des Widgets den **Dashboard „Kunden“** -Link auswählen.

### <a name="usage-widget"></a>Widget „Nutzung“

Das Widget **Nutzung** des Dashboards **Zusammenfassung** stellt die Gesamtzahl der Stunden normalisierter und tatsächlicher Nutzung für alle Angebote virtueller Azure-Computer (VM) dar. Das Widget „Nutzung“ zeigt eine Anzahl und einen Trend der Gesamtnutzungsstunden für den ausgewählten Berechnungszeitraum an.

In der Tabelle „Nutzungszusammenfassung“ werden die Nutzungsstunden der Kunden für alle erworbenen Angebote angezeigt.

- Normalisierte Nutzungsstunden werden als Nutzungsstunden definiert, die normalisiert wurden, um die Anzahl der VM-Kerne ([Anzahl der VM-Kerne] ·x [Stunden der Rohdatennutzung]) zu berücksichtigen. Für VMs, die als „SHAREDCORE“ festgelegt wurden, wird der Multiplikator 1/6 (oder 0,1666) für [Anzahl der VM-Kerne] verwendet.
- Die tatsächlichen Nutzungsstunden werden als die Zeitspanne (in Stunden) definiert, in der VMs ausgeführt wurden.

Der unter den Gesamtnutzungsstunden angegebene Prozentwert stellt den prozentualen Zuwachs in Nutzungsstunden im ausgewählten Berechnungszeitraum dar.

[![Veranschaulicht das Widget „Nutzung“ auf dem Dashboard „Zusammenfassung“.](./media/summary-dashboard/usage-widget.png)](./media/summary-dashboard/usage-widget.png#lightbox)

Sie können auch zum Bericht „Nutzung“ wechseln, indem Sie in der linken unteren Ecke des Widgets auf den **Dashboard „Nutzung“** -Link klicken.

### <a name="marketplace-insights"></a>Marketplace-Erkenntnisse

„Marketplace-Erkenntnisse“ zeigt die Anzahl der Benutzer an, die die Seiten Ihrer Angebote in Azure Marketplace und AppSource besucht haben. **Anzahl der Seitenbesuche** zeigt eine Zusammenfassung der Webanalysen im kommerziellen Marketplace, mit deren Hilfe Herausgeber die Kundeninteraktion mit ihren jeweiligen Produktdetailseiten messen können, die in den Onlineshops des kommerziellen Marketplace aufgelistet sind: Microsoft AppSource und Azure Marketplace. Dieses Widget zeigt eine Anzahl und einen Trend der gesamten Seitenbesuche für den ausgewählten Berechnungszeitraum an.

[![Veranschaulicht das Widget „Anzahl der Seitenbesuche“ auf dem Dashboard „Zusammenfassung“.](./media/summary-dashboard/page-visit-count.png)](./media/summary-dashboard/page-visit-count.png#lightbox)

Sie können auch zum Bericht „Marketplace-Erkenntnisse“ wechseln, indem Sie in der linken unteren Ecke des Widgets den **Dashboard „Marketplace-Erkenntnisse“** -Link auswählen.

### <a name="geographical-spread"></a>Geografische Verteilung

Für den ausgewählten Berechnungszeitraum zeigt das Wärmebild die Gesamtzahl der Kunden, Aufträge und Stunden normalisierter Nutzung für den jeweiligen geografischen Bereich an.

:::image type="content" source="./media/summary-dashboard/geo-spread.png" alt-text="Veranschaulicht das Widget „Länderverteilung“ auf dem Dashboard „Zusammenfassung“.":::

Beachten Sie Folgendes:

- Sie können die Karte verschieben, um den genauen Standort anzuzeigen.
- Sie können an einen bestimmten Ort zoomen.
- Das Wärmebild verfügt über ein zusätzliches Raster, um Details zur Anzahl der Kunden oder Aufträge und zu den normalisierten Nutzungsstunden an einem bestimmten Ort anzuzeigen.
- Sie können ein Land oder eine Region im Raster suchen und auswählen, um an den Ort in der Karte zu zoomen. Um wieder zur ursprünglichen Ansicht zurückzukehren, wählen Sie in der Karte die Schaltfläche **Startseite** aus.

> [!TIP]
> Sie können das Downloadsymbol in der oberen rechten Ecke eines Widgets verwenden, um die Daten herunterzuladen. Sie können Feedback zu jedem der Widgets geben, indem Sie das Symbol „Daumen nach oben“ oder „Daumen nach unten“ auswählen.

## <a name="next-steps"></a>Nächste Schritte

- Eine Übersicht über die im kommerziellen Marketplace verfügbaren Analyseberichte finden Sie unter [Zugreifen auf Analysen für den kommerziellen Marketplace in Partner Center](./partner-center-portal/analytics.md).
- Informationen zu Ihren Aufträgen in einem grafischen und herunterladbaren Format finden Sie unter [Dashboard „Aufträge“ in Analysen für den kommerziellen Marketplace](orders-dashboard.md).
- Informationen zu Metriken zur Nutzung und zur getakteten Abrechnung für VM-Angebote finden Sie unter [Dashboard „Nutzung“ in Analysen für den kommerziellen Marketplace](usage-dashboard.md).
- Ausführliche Informationen zu Ihren Kunden, einschließlich Wachstumstrends, finden Sie unter [Dashboard „Kunde“ in Analysen für den kommerziellen Marketplace](customer-dashboard.md).
- Eine Liste Ihrer Downloadanforderungen in den letzten 30 Tagen finden Sie unter [Dashboard „Downloads“ in Analysen für den kommerziellen Marketplace](./partner-center-portal/downloads-dashboard.md).
- Eine konsolidierte Ansicht des Kundenfeedbacks für Angebote im Azure Marketplace und in AppSource finden Sie unter [Analyse-Dashboard „Bewertungen und Rezensionen“ in Partner Center](./partner-center-portal/ratings-reviews.md).
- Häufig gestellte Fragen zu Analysen für den kommerziellen Marketplace und ein umfassendes Wörterbuch mit Datenbegriffen finden Sie unter [Analysen für den kommerziellen Marketplace: Häufig gestellte Fragen](./analytics-faq.md).