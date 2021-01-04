---
title: Partner Center-Dashboard „Aufträge“ in Analysen für den kommerziellen Marketplace, Microsoft AppSource und dem Azure Marketplace
description: Erfahren Sie, wie Sie auf Analyseberichte in einem grafischen und herunterladbaren Format zugreifen, die Informationen zu Aufträgen für Ihre Angebote im kommerziellen Marketplace enthalten.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 11/09/2020
author: sayantanroy83
ms.author: sroy
ms.openlocfilehash: 943f1361a2a9d6bf8d891f8b4f2137b8738f0836
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96450766"
---
# <a name="orders-dashboard-in-commercial-marketplace-analytics"></a>Dashboard „Aufträge“ in Analysen für den kommerziellen Marketplace

Dieser Artikel enthält Informationen zum Dashboard „Aufträge“ im Partner Center. Dieses Dashboard zeigt Informationen zu Ihren Aufträgen einschließlich Wachstumstrends in einem grafischen und herunterladbaren Format an.

Um auf das Dashboard „Aufträge“ in Partner Center zuzugreifen, wählen Sie unter **Kommerzieller Marketplace** die Option **[Analysieren](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)**  > **Aufträge** aus.

>[!NOTE]
> Ausführliche Definitionen der Analyseterminologie finden Sie unter [Analysen für den kommerziellen Marketplace: Häufig gestellte Fragen und Terminologie](./analytics-faq.md).

## <a name="orders-dashboard"></a>Dashboard „Bestellungen“

Im Dashboard „Aufträge“ werden die aktuellen Aufträge für all Ihre SaaS-Angebote (Software-as-a-Service) angezeigt. Sie können grafische Darstellungen folgender Elemente anzeigen:

- Trend für Aufträge
- Trend für Aufträge pro Arbeitsplatz und Standort
- Aufträge nach Angeboten und SKUs
- Bestellungen nach Geographie
- Detaillierte Aufträgetabelle
- Filter für die Seite „Aufträge“

> [!NOTE]
> Die maximale Latenz zwischen der Kundengewinnung und der Berichterstattung im Partner Center beträgt 48 Stunden.

## <a name="elements-of-the-orders-dashboard"></a>Elemente des Dashboards „Aufträge“

In den folgenden Abschnitten wird beschrieben, wie das Dashboard „Aufträge“ verwendet und wie die Daten gelesen werden.

### <a name="month-range"></a>Monatsbereich

Oben rechts auf jeder Seite finden Sie eine Monatsbereichsauswahl. Passen Sie die Ausgabe der Diagramme auf der Seite **Aufträge** an, indem Sie einen Monatsbereich basierend auf den letzten 6 oder 12 Monaten oder einen benutzerdefinierten Monatsbereich mit einer maximalen Dauer von 12 Monaten auswählen. Der standardmäßige Monatsbereich (Berechnungszeitraum) beträgt sechs Monate.

:::image type="content" source="./media/orders-dashboard/month-filters.png" alt-text="Veranschaulicht die Monatsfilter im Dashboard „Aufträge“":::

> [!NOTE]
> Alle Metriken in den Visualisierungswidgets und Exportberichten berücksichtigen den vom Benutzer ausgewählten Berechnungszeitraum.

### <a name="orders-trend"></a>Trend für Aufträge

In diesem Abschnitt finden Sie das Diagramm **Aufträge**, in dem der Trend der aktiven und stornierten Aufträge für den ausgewählten Berechnungszeitraum angezeigt wird. Metriken und Wachstumstrends werden durch ein Liniendiagramm dargestellt und zeigen den Wert für jeden Monat an, wenn Sie den Cursor über die Linie des Diagramms bewegen. Der unter den Aufträgemetriken im Widget angegebene Prozentwert stellt den prozentualen Zuwachs bzw. Rückgang im ausgewählten Berechnungszeitraum dar.

Es gibt zwei Aufträgezähler: _Aktiv_ und _Storniert_.

- **Aktiv** ist mit der Anzahl der Aufträge identisch, die derzeit während des ausgewählten Datumsbereichs von Kunden erteilt worden und in Bearbeitung sind.
- **Storniert** entspricht der Anzahl der Aufträge, die zuvor erteilt und dann während des ausgewählten Datumsbereichs storniert wurden.

[![Veranschaulicht das Aufträge-Widget auf dem Dashboard „Aufträge“, das den Trend aktiver und stornierter Aufträge anzeigt.](./media/orders-dashboard/orders-trend.png)](./media/orders-dashboard/orders-trend.png#lightbox)

### <a name="orders-by-per-seat-and-site-trend"></a>Trend für Aufträge nach Arbeitsplatz und Standort

Im Liniendiagramm **Trend für Aufträge nach Arbeitsplatz und Standort** sind Metrik und Trend der von Kunden erworbenen SaaS-Aufträge pro Standort und pro Arbeitsplatz dargestellt (dieses Diagramm umfasst stornierte Aufträge).

[![Veranschaulicht das Aufträge-Widget auf dem Dashboard „Aufträge“, das den Trend für Aufträge nach Arbeitsplatz und Standort anzeigt.](./media/orders-dashboard/seats-sites.png)](./media/orders-dashboard/seats-sites.png#lightbox)

Für SaaS-Angebote können Sie bei jedem Plan eines von zwei Preismodellen verwenden: Pauschalgebühr (standortbasiert) oder Pro Benutzer (arbeitsplatzbasiert).

- **Pauschalgebühr**: Ermöglichen Sie den Zugriff auf Ihr Angebot mit einer einzelnen monatlichen oder jährlichen Pauschalgebühr. Dies wird manchmal als arbeitsplatzbasierter Preis bezeichnet.
- **Pro Benutzer**: Ermöglichen Sie den Zugriff auf Ihr Angebot mit einem Preis, der auf der Anzahl von Benutzern, die auf Ihr Angebot zugreifen können, oder auf der Anzahl beanspruchter Arbeitsplätze basiert. Mit diesem nutzungsbasierten Modell können Sie die minimale und maximale Anzahl von Benutzern festlegen, die vom Plan unterstützt werden. Sie können mehrere Pläne erstellen, um verschiedene Preispunkte basierend auf der Anzahl der Benutzer zu konfigurieren. Diese Felder sind optional. Wenn keine Auswahl erfolgt, wird angenommen, dass die Anzahl der Arbeitsplätze unbegrenzt ist (mindestens 1 und maximal die vom Service unterstützte Anzahl). Diese Felder können beim Aktualisieren des Plans bearbeitet werden.
- **Getaktete Abrechnung**: Zusätzlich zur Pauschalgebühr. Bei diesem Preismodell können Sie optional Volumentarife definieren, um Kunden mithilfe der Marketplace-Messungsdienst-API die Nutzung in Rechnung zu stellen, die nicht durch die Pauschalgebühr abgedeckt ist.

Weitere Informationen zu arbeitsplatzbasierter, standortbasierter und getakteter Abrechnung finden Sie unter [Planen eines SaaS-Angebots im kommerziellen Marketplace](plan-saas-offer.md).

### <a name="orders-by-offers-and-skus"></a>Aufträge nach Angeboten und SKUs

Im Diagramm „Aufträge nach Angeboten und SKUs“ werden die Measures und Trends aller Angebote angezeigt:

- Die Topangebote werden im Diagramm angezeigt, während die übrigen unter **Alle restlichen** gruppiert sind.
- Sie können bestimmte Angebote in der Legende auswählen, um nur diese Angebote und die zugeordneten SKUs im Diagramm anzuzeigen.
- Wenn Sie mit der Maus auf ein Segment im Diagramm zeigen, wird die Anzahl der Bestellungen und der Prozentsatz dieses Angebots im Vergleich zur Gesamtanzahl der Bestellungen für alle Ihre Angebote angezeigt.
- Der **Trend „Aufträge nach Angeboten“** zeigt Wachstumstrends nach Monaten an. Die Spalte „Monat“ gibt die Anzahl der Bestellungen nach Angebotsname an. Das Liniendiagramm stellt den prozentualen Wachstumstrend auf einer z-Achse dar.

:::image type="content" source="./media/orders-dashboard/orders-by-offers.png" alt-text="Veranschaulicht das Diagramm „Aufträge nach Angeboten“ im Dashboard „Aufträge“.":::

Sie können ein beliebiges Angebot und maximal drei SKUs dieses Angebots auswählen, um den Trend für das Angebot, die SKUs und die Arbeitsplätze nach Monaten anzuzeigen.

### <a name="orders-by-geography"></a>Bestellungen nach Geographie

Für den ausgewählten Berechnungszeitraum zeigt das Wärmebild die Gesamtzahl der Aufträge und den prozentualen Zuwachs der neu hinzugefügten Aufträge für den jeweiligen geografischen Bereich an.  Die Helligkeit der Farbe auf der Karte entspricht einer niedrigen bis hohen Anzahl von Kunden. Wählen Sie einen Datensatz in der Tabelle aus, um ein bestimmtes Land oder eine bestimmte Region zu vergrößern.

[![Veranschaulicht das geografische Streuungsdiagramm im Dashboard „Aufträge“.](./media/orders-dashboard/geographical-spread.png)](./media/orders-dashboard/geographical-spread.png#lightbox)

Beachten Sie Folgendes:

- Sie können die Karte verschieben, um den genauen Standort anzuzeigen.
- Sie können an einen bestimmten Ort zoomen.
- Das Wärmebild verfügt über ein zusätzliches Raster, um Details zur Anzahl der Kunden oder Aufträge und zu den normalisierten Nutzungsstunden an einem bestimmten Ort anzuzeigen.
- Sie können ein Land oder eine Region im Raster suchen und auswählen, um an den Ort in der Karte zu zoomen. Um wieder zur ursprünglichen Ansicht zurückzukehren, wählen Sie die Schaltfläche **Home** in der Karte aus.

### <a name="orders-details-table"></a>Tabelle „Auftragsdetails“

In der Tabelle „Auftragsdetails“ wird eine nummerierte Liste der 1.000 besten Aufträge nach dem Kaufdatum sortiert angezeigt.

- Jede Spalte im Raster ist sortierbar.
- Die Daten können in eine CSV- oder TSV-Datei extrahiert werden, wenn die Anzahl weniger als 1.000 Einträge beträgt.
- Liegt die Anzahl der Datensätze über 1.000, werden die exportierten Daten für die nächsten 30 Tage asynchron auf einer Downloadseite abgelegt.
- Wenden Sie Filter auf die Tabelle **Auftragsdetails** an, um nur die Daten anzuzeigen, die für Sie von Interesse sind. Filtern Sie nach Land/Region, Azure-Lizenztyp, Lizenztyp im kommerziellen Marketplace, Angebotstyp, Bestellstatus, kostenlosen Testversionen, Abonnement-ID im kommerziellen Marketplace, Kunden-ID und Unternehmensname.
- Da für SaaS-Angebote, die über den Azure Marketplace oder Microsoft AppSource erworben werden, kein Azure-Abonnement erforderlich ist, wird im Abschnitt **Detaillierte Auftragsdaten** die Marketplace-Abonnement-ID in der Form „00000000-0000-0000-0000-000000000000“ angezeigt.
- Wenn ein Auftrag von einem geschützten Kunden erworben wird, werden die Informationen in **Detaillierte Auftragsdaten** maskiert (************).

**_Tabelle 1: Wörterbuch mit Datenbegriffen_* _

| Spaltenname | Attributname | Definition |
| ------------ | ------------- | ------------- |
| Marketplace Subscription Id | Marketplace-Abonnement-ID | Der eindeutige Bezeichner (ID), der mit dem Azure-Abonnement verknüpft ist, über das der Kunde Ihr Angebot im kommerziellen Marketplace erworben hat. Früher war ID die Azure-Abonnement-GUID. |
| MonthStartDate | Monatsstartdatum | Das Monatsstartdatum steht für den Monat des Kaufs. |
| Angebotstyp | Angebotstyp | Der Typ des Angebots im kommerziellen Marketplace |
| Azure-Lizenztyp | Azure-Lizenztyp | Die Art der Lizenzvereinbarung, über die Kunden Azure erwerben. Wird auch als Kanal bezeichnet. Mögliche Werte sind:<ul><li>Cloud Solution Provider</li><li>Enterprise</li><li>Enterprise über Handelspartner</li><li>Nutzungsbasierte Bezahlung</li></ul> |
| Marketplace-Lizenztyp | Marketplace-Lizenztyp | Die Abrechnungsmethode für das Angebot im kommerziellen Marketplace. Die unterschiedlichen Werte sind:<ul><li>Abrechnung über Azure</li><li>BYOL (Bring Your Own License)</li><li>Kostenlos</li><li>Microsoft als Handelspartner</li></ul> |
| SKU | SKU | Der dem Angebot zugeordnete Plan |
| Land des Kunden | Land/Region des Kunden | Der vom Kunden angegebene Name für das Land bzw. die Region. Das Land/die Region kann sich von dem im Azure-Abonnement eines Kunden angegebenen Land bzw. der angegebenen Region unterscheiden. |
| Vorschau-SKU | Vorschau-SKU | Der Wert gibt an, ob Sie die SKU als „Vorschau“ gekennzeichnet haben. Der Wert ist „Ja“, wenn Sie die SKU entsprechend gekennzeichnet haben und nur von Ihnen autorisierte Azure-Abonnements dieses Image bereitstellen und nutzen können. Der Wert ist „Nein“, wenn die SKU nicht als „Vorschau“ definiert wurde. |
| Auftrags-ID | Bestell-ID | Der eindeutige Bezeichner (ID) des Kundenauftrags für Ihren Dienst im kommerziellen Marketplace. Nutzungsbasierte Angebote für virtuelle Computer werden keiner Bestellung zugeordnet. |
| Order Quantity | Order Quantity | Anzahl der Objekte, die der Auftrags-ID für aktive Aufträge zugeordnet sind. |
| Cloudinstanzname | Cloudinstanzname | Die Microsoft Cloud, in der die Bereitstellung eines virtuellen Computers erfolgt ist. |
| Neuer Kunde | Neuer Kunde | Der Wert gibt an, ob ein neuer Kunde eines oder mehrere Ihrer Angebote zum ersten Mal erworben hat. Der Wert ist „Ja“, wenn das Erwerbsdatum im gleichen Kalendermonat liegt. Der Wert ist „Nein“, wenn der Kunde eines Ihrer Angebote vor dem berichteten Kalendermonat gekauft hat. |
| Bestellstatus | Bestellstatus | Der Status eines Auftrags im kommerziellen Marketplace zu dem Zeitpunkt, zu dem die Daten zuletzt aktualisiert wurden. |
| Stornierungsdatum der Bestellung | Stornierungsdatum der Bestellung | Das Datum, an dem der Auftrag im kommerziellen Marketplace storniert wurde. |
| Firmenname des Kunden | Firmenname des Kunden | Der vom Kunden angegebene Firmenname. Der Name kann sich von der Angabe im Azure-Abonnement eines Kunden unterscheiden. |
| Kaufdatum der Bestellung | Kaufdatum der Bestellung | Das Datum, an dem der Auftrag im kommerziellen Marketplace gelöscht wurde. |
| Angebotsname | Angebotsname | Der Name des Angebots im kommerziellen Marketplace. |
| Enddatum der Testversion | Enddatum der Testversion | Das Datum, an dem der Testzeitraum für die Bestellung endet oder beendet ist. |
| Customer Id | Customer ID | Der eindeutige, einem Kunden zugewiesene Bezeichner. Ein Kunde kann keine oder mehrere Azure Marketplace-Abonnements besitzen. |
| Billing Account Id | ID des Abrechnungskontos | Der Bezeichner des Kontos, für das die Abrechnung generiert wird. Ordnen Sie die _ *ID des Abrechnungskontos** der **customerID** zu, um Ihren Auszahlungstransaktionsbericht mit den Kunden-, Auftrags- und Nutzungsberichten zu verbinden. |
| AssetCount | Anzahl der Objekte | Die Anzahl der Objekte, die der Auftrags-ID zugeordnet sind. |
||||

### <a name="orders-page-filters"></a>Filter für die Seite „Aufträge“

Die Filter für die Seite **Aufträge** werden auf der Ebene der Seite „Aufträge“ angewendet. Sie können einen oder mehrere Filter auswählen, um das Diagramm nach den gewünschten Anzeigekriterien und den gewünschten Daten im Raster/Export „Detaillierte Auftragsdaten“ zu rendern. Filter werden auf die Daten angewandt, die für den in der rechten oberen Ecke der Seite „Aufträge“ von Ihnen ausgewählten Monatsbereich extrahiert wurden.

> [!TIP]
> Sie können das Downloadsymbol in der oberen rechten Ecke eines Widgets verwenden, um die Daten herunterzuladen. Sie können Feedback zu jedem der Widgets geben, indem Sie auf das Symbol „Daumen nach oben“ oder „Daumen nach unten“ klicken.

## <a name="next-steps"></a>Nächste Schritte

- Eine Übersicht über die im kommerziellen Marketplace verfügbaren Analyseberichte finden Sie unter [Zugreifen auf Analysen für den kommerziellen Marketplace in Partner Center](./partner-center-portal/analytics.md).
- Informationen zu Diagrammen, Trends und Werten von aggregierten Daten, mit deren Hilfe Marketplace-Aktivitäten für Ihr Angebot zusammengefasst werden, finden Sie unter [Dashboard „Zusammenfassung“ in Analysen für den kommerziellen Marketplace](./summary-dashboard.md).
- Informationen zu Ihren Aufträgen in einem grafischen und herunterladbaren Format finden Sie unter [Dashboard „Aufträge“ in Analysen für den kommerziellen Marketplace](orders-dashboard.md).
- Informationen zu Metriken zur Nutzung und zur getakteten Abrechnung für VM-Angebote finden Sie unter [Dashboard „Nutzung“ in Analysen für den kommerziellen Marketplace](./usage-dashboard.md).
- Eine Liste Ihrer Downloadanforderungen der letzten 30 Tagen finden Sie unter [Dashboard „Downloads“ in Analysen für den kommerziellen Marketplace](./partner-center-portal/downloads-dashboard.md).
- Eine konsolidierte Ansicht des Kundenfeedbacks für Angebote im Azure Marketplace und in AppSource finden Sie unter [Analyse-Dashboard „Bewertungen und Rezensionen“ in Partner Center](./partner-center-portal/ratings-reviews.md).
- Häufig gestellte Fragen zu Analysen für den kommerziellen Marketplace und ein umfassendes Wörterbuch mit Datenbegriffen finden Sie unter [Analysen für den kommerziellen Marketplace: Häufig gestellte Fragen](./analytics-faq.md).