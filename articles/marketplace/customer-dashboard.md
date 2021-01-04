---
title: Dashboard „Kunden“ in Analysen für den kommerziellen Microsoft-Marketplace im Partner Center, im Azure Marketplace und in Microsoft AppSource
description: Erfahren Sie, wie Sie mithilfe des Dashboards „Kunden“ in Analysen für den kommerziellen Marketplace auf Informationen zu Ihren Kunden zugreifen, einschließlich Wachstumstrends.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 11/09/2020
author: sayantanroy83
ms.author: sroy
ms.openlocfilehash: 878fa181ff09d547ef6594a238e9c1d883cc89b7
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96460476"
---
# <a name="customers-dashboard-in-commercial-marketplace-analytics"></a>Dashboard „Kunden“ in Analysen für den kommerziellen Marketplace

Dieser Artikel enthält Informationen zum Dashboard „Kunden“ in Partner Center. Dieses Dashboard zeigt Informationen zu Ihren Kunden, einschließlich Wachstumstrends, in einem grafischen und herunterladbaren Format an.

Um auf das Dashboard „Kunden“ in Partner Center zuzugreifen, wählen Sie unter **Kommerzieller Marketplace** die Option **[Analysieren](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)**  > **Kunden** aus.

>[!NOTE]
> Ausführliche Definitionen der Analyseterminologie finden Sie unter [Analysen für den kommerziellen Marketplace: Häufig gestellte Fragen und Terminologie](./analytics-faq.md).

## <a name="customers-dashboard"></a>Dashboard „Kunden“

Das Dashboard „Kunden“ zeigt Daten zu Kunden an, die Ihre Angebote erworben haben. Sie können grafische Darstellungen folgender Elemente anzeigen:

- Trend für aktive und abgewanderte Kunden
- Trend für Kundenwachstum einschließlich vorhandener, neuer und abgewanderter Kunden
- Kunden nach Bestellungen und Nutzung
- Perzentil für Kunden 
- Kundentyp nach Aufträgen und Nutzung
- Kunden nach Geographie
- Tabelle der Kundendetails
- Filter auf der Seite „Kunden“

> [!NOTE]
> Die maximale Latenz zwischen der Kundengewinnung und der Berichterstattung im Partner Center beträgt 48 Stunden.

## <a name="elements-of-the-customers-dashboard"></a>Elemente des Dashboards „Kunden“

In den folgenden Abschnitten wird beschrieben, wie das Dashboard „Kunden“ verwendet wird und wie die Daten gelesen werden.

### <a name="month-range"></a>Monatsbereich

Oben rechts auf jeder Seite finden Sie eine Monatsbereichsauswahl. Passen Sie die Ausgabe der Diagramme auf der Seite **Kunden** an, indem Sie einen Monatsbereich basierend auf den letzten 6 oder 12 Monaten oder einen benutzerdefinierten Monatsbereich mit einer maximalen Dauer von 12 Monaten auswählen. Der standardmäßige Monatsbereich (Berechnungszeitraum) beträgt sechs Monate.

:::image type="content" source="./media/customer-dashboard/month-range-filters.png" alt-text="Veranschaulicht die Monatsfilter auf der Seite „Kunden“.":::

> [!NOTE]
> Alle Metriken in den Visualisierungswidgets und Exportberichten berücksichtigen den vom Benutzer ausgewählten Berechnungszeitraum.

### <a name="active-and-churned-customers-trend"></a>Trend für aktive und abgewanderte Kunden

In diesem Abschnitt finden Sie den Kundenwachstumstrend für den ausgewählten Berechnungszeitraum. Metriken und Wachstumstrends werden durch ein Liniendiagramm dargestellt und zeigen den Wert für jeden Monat an, wenn Sie den Cursor über die Linie des Diagramms bewegen. Der unter **Aktive Kunden** im Widget angegebene Prozentwert stellt die Stärke des Wachstums während des ausgewählten Berechnungszeitraums dar. Der folgende Screenshot zeigt z. B. ein Wachstum von 0,92 % für den ausgewählten Berechnungszeitraum.

[![Veranschaulicht das Widget „Kunden“ auf der Seite „Kunden“.](./media/customer-dashboard/customers-widget.png)](./media/customer-dashboard/customers-widget.png#lightbox)

Es gibt drei _Kundentypen_: Neukunden, Bestandskunden und abgewanderte Kunden.

- Ein Neukunde hat mindestens eines Ihrer Angebote innerhalb des ausgewählten Monats zum ersten Mal erworben.
- Ein Bestandskunde hat mindestens eines Ihrer Angebote vor dem ausgewählten Monat erworben.
- Ein abgewanderter Kunde hat alle zuvor erteilten Bestellungen storniert. Abgewanderte Kunden werden im Trend-Widget auf der negativen Achse dargestellt.

### <a name="customer-growth-trend-including-existing-new-and-churned-customers"></a>Trend für Kundenwachstum einschließlich vorhandener, neuer und abgewanderter Kunden

In diesem Abschnitt finden Sie Trend und Anzahl für alle Kunden einschließlich Neukunden, Bestandskunden und abgewanderter Kunden mit dem Wachstumstrend auf Monatsbasis.

- Das Liniendiagramm stellt den Prozentsatz des Kundenwachstums insgesamt dar.
- Die Spalte „Monat“ stellt die Anzahl der Kunden dar, aufgeteilt in Neukunden, Bestandskunden und abgewanderte Kunden.
- Die Abwanderung wird auf der X-Achse in negativer Richtung angezeigt.
- Sie können einzelne Elemente der Legende auswählen, um detailliertere Ansichten anzuzeigen. Wählen Sie beispielsweise „Neukunden“ aus der Legende aus, um nur Informationen zu Neukunden anzuzeigen.
- Wenn Sie auf eine Spalte im Diagramm zeigen, werden nur Details für diesen Monat angezeigt.

[![Veranschaulicht das Kundentrendwidget auf der Seite „Kunden“.](./media/customer-dashboard/customers-trend.png)](./media/customer-dashboard/customers-trend.png#lightbox)

### <a name="customers-by-ordersusage"></a>Kunden nach Aufträgen/Nutzung

Das Diagramm **Kunden nach Aufträgen/Nutzung** enthält drei Registerkarten: „Aufträge“, „Normalisierte Nutzung“ und „Tatsächliche Nutzung“. Wählen Sie die Registerkarte **Aufträge** aus, um Auftragsdetails anzuzeigen.

:::image type="content" source="./media/customer-dashboard/customers-by-orders-usage.png" alt-text="Veranschaulicht die Registerkarte „Aufträge“ des Widgets „Kunden nach Aufträgen/Nutzung“ auf der Seite „Kunden“.":::

Beachten Sie Folgendes:

- Das Leaderboard zeigt Details der Kunden nach Anzahl von Aufträgen an. Nach Auswahl eines Kunden werden die Kundendetails in den nebeneinanderliegenden Abschnitten „Details“, „Aufträge nach SKUs“ und „SKUs nach Arbeitsplätzen“ angezeigt.
- Die Details zum Kundenprofil werden in diesem Bereich angezeigt, sofern Herausgeber mit einer Besitzerrolle angemeldet sind. Wenn Herausgeber mit einer Rolle als Mitwirkender angemeldet sind, sind die Details in diesem Abschnitt nicht verfügbar.
- Im Ringdiagramm **Aufträge nach SKUs** wird die Aufschlüsselung der für Pläne erworbenen Aufträge angezeigt. Die obersten fünf Pläne mit der höchsten Anzahl von Aufträgen werden angezeigt, während die restlichen Aufträge unter **Alle restlichen** gruppiert sind.
- Im Ringdiagramm **SKUs nach Arbeitsplätzen** wird die Aufschlüsselung der für Pläne bestellten Arbeitsplätze angezeigt. Die obersten fünf Pläne mit der höchsten Anzahl von Arbeitsplätzen werden angezeigt, während die restlichen Aufträge unter **Alle restlichen** gruppiert sind.

Sie können auch die Registerkarte **Normalisierte Nutzung** oder **Tatsächliche Nutzung** auswählen, um die Nutzungsdetails anzuzeigen.

- Das Leaderboard zeigt Details der Kunden nach Anzahl der Nutzungsstunden an. Nach Auswahl eines Kunden werden die Kundendetails in den nebeneinanderliegenden Abschnitten „Details“, „Normalisierte Nutzung nach Angeboten“ und „Normalisierte Nutzung nach VM-Größe(n)“ angezeigt.
- Die Details zum Kundenprofil werden in diesem Bereich angezeigt, sofern Herausgeber mit einer Besitzerrolle angemeldet sind. Wenn Herausgeber mit einer Rolle als Mitwirkender angemeldet sind, sind die Details in diesem Abschnitt nicht verfügbar.
- Im Ringdiagramm **Normalisierte Nutzung nach Angeboten** wird die Aufschlüsselung der von den Angeboten beanspruchten Nutzung angezeigt. Die obersten fünf Pläne mit der höchsten Nutzung werden angezeigt, während die restlichen Angebote unter **Alle restlichen** gruppiert sind.
- Im Ringdiagramm **Normalisierte Nutzung nach VM-Größe(n)** wird die Aufschlüsselung der von den verschiedenen VM-Größen beanspruchten Nutzung angezeigt. Die obersten fünf VM-Größen mit der höchsten normalisierten Nutzung werden angezeigt, während die restliche Nutzung unter **Alle restlichen** gruppiert ist.

### <a name="top-customers-percentile"></a>Perzentil der besten Kunden

Im Diagramm **Perzentil der besten Kunden** werden drei Registerkarten angezeigt: „Aufträge“, „Normalisierte Nutzung“ und „Tatsächliche Nutzung“. Das _Perzentil der besten Kunden_ wird entlang der X-Achse angezeigt und anhand der Anzahl von Aufträgen bestimmt. Auf der y-Achse wird die Anzahl der Bestellungen des Kunden angezeigt. Auf der sekundären y-Achse (Liniendiagramm) wird der kumulierte prozentuale Anteil an der Gesamtanzahl der Bestellungen angezeigt. Sie können Details anzeigen, indem Sie den Cursor über Punkte entlang des Liniendiagramms bewegen.

[![Veranschaulicht die Registerkarte „Aufträge“ des Widgets „Perzentil der besten Kunden“ auf der Seite „Kunden“.](./media/customer-dashboard/top-customer-percentile.png)](./media/customer-dashboard/top-customer-percentile.png#lightbox)

### <a name="customer-type-by-orders-and-usage"></a>Kundentyp nach Aufträgen und Nutzung

Im Diagramm **Bestellungen/Nutzung nach Kundentyp** werden die Anzahl von Bestellungen, die normalisierte Nutzung und die unformatierten Nutzungszeiten aufgeteilt nach Neukunden und Bestandskunden angezeigt. Dies basiert auf der Auswahl der Bestellungen, der normalisierten Nutzung bzw. der unformatierten Nutzung im Diagramm.

Im Diagramm wird Folgendes angezeigt:

- Ein Neukunde weist mindestens einen erstmaligen Erwerb eines Ihrer Angebote bzw. erstmalige Nutzung innerhalb desselben Kalendermonats (y-Achse) auf.
- Ein Bestandskunde hat bereits vor dem Berichtskalendermonat (auf der y-Achse) eines Ihrer Angebote erworben oder genutzt.

[![Veranschaulicht die Registerkarte „Aufträge“ des Widgets „Aufträge nach Kundentyp“ auf der Seite „Kunden“.](./media/customer-dashboard/orders-by-customer-type.png)](./media/customer-dashboard/orders-by-customer-type.png#lightbox)

### <a name="customers-by-geography"></a>Kunden nach Geographie

Für den ausgewählten Berechnungszeitraum zeigt das Wärmebild die Gesamtzahl der Kunden und den Prozentsatz der neu hinzugefügten Kunden für den jeweiligen geografischen Bereich an. Die Helligkeit der Farbe auf der Karte entspricht einer niedrigen bis hohen Anzahl von Kunden. Wählen Sie einen Datensatz in der Tabelle aus, um ein Land oder eine Region zu vergrößern.

[![Veranschaulicht die Registerkarte „Aufträge“ des Widgets „Aufträge nach Geografie“ auf der Seite „Kunden“.](./media/customer-dashboard/customers-by-geography.png)](./media/customer-dashboard/customers-by-geography.png#lightbox)

Beachten Sie Folgendes:

- Sie können die Karte verschieben, um den genauen Standort anzuzeigen.
- Sie können an einen bestimmten Ort zoomen.
- Das Wärmebild verfügt über ein zusätzliches Raster, um Details zur Anzahl der Kunden oder Aufträge und zu den normalisierten Nutzungsstunden an einem bestimmten Ort anzuzeigen.
- Sie können ein Land oder eine Region im Raster suchen und auswählen, um an den Ort in der Karte zu zoomen. Um wieder zur ursprünglichen Ansicht zurückzukehren, wählen Sie die Schaltfläche **Home** in der Karte aus.

### <a name="customer-details-table"></a>Tabelle der Kundendetails

Die Tabelle **Kundendetails** zeigt eine nummerierte Liste der 1.000 besten Kunden an, sortiert nach dem Datum ihrer ersten Bestellung eines Ihrer Angebote. Sie können einen Abschnitt erweitern, indem Sie im Detailsmenüband das Erweiterungssymbol auswählen.

Beachten Sie Folgendes:

- Persönliche Kundendaten sind nur dann verfügbar, wenn der Kunde seine Einwilligung erteilt hat. Sie können diese Daten nur einsehen, wenn Sie sich mit der Berechtigungsebene einer Besitzerrolle angemeldet haben.
- Jede Spalte im Raster ist sortierbar.
- Die Daten können in eine CSV- oder TSV-Datei extrahiert werden, wenn die Anzahl weniger als 1.000 Einträge beträgt.
- Liegt die Anzahl der Datensätze über 1.000, werden die exportierten Daten für die nächsten 30 Tage asynchron auf einer Downloadseite abgelegt.
- Wenden Sie Filter auf die Tabelle an, um nur die Daten anzuzeigen, die für Sie von Interesse sind. Filtern Sie Daten nach Firmenname, Kunden-ID, Marketplace-Abonnement-ID, Azure-Lizenztyp, Erwerbsdatum, Verlustdatum, E-Mail-Adresse des Kunden, Land/Region/Bundesland/Kanton/Stadt/Postleitzahl des Kunden, Sprache des Kunden usw.
- Wenn ein Angebot von einem geschützten Kunden erworben wird, werden die Informationen in **Customer Detailed Data** (Detaillierte Kundendaten) maskiert (************).
- Kundendimensionsdetails wie Firmenname, Kundenname und E-Mail-Adresse des Kunden befinden sich auf der Ebene der Unternehmens-ID, nicht auf der Azure Marketplace- oder Microsoft AppSource-Transaktionsebene.

_**Tabelle 1: Wörterbuch mit Datenbegriffen**_

| Spaltenname | Attributname | Definition |
| ------------ | ------------- | ------------- |
| Marketplace Subscription Id | Marketplace-Abonnement-ID | Der eindeutige Bezeichner (ID), der mit dem Azure-Abonnement verknüpft ist, über das der Kunde Ihr Angebot im kommerziellen Marketplace erworben hat. Früher war ID die Azure-Abonnement-GUID. |
| DateAcquired | Erwerbsdatum | Das erste Datum, an dem der Kunde ein von Ihnen veröffentlichtes Angebot erworben hat. |
| DateLost | Verlustdatum | Das letzte Datum, an dem der Kunde das letzte aller zuvor erworbenen Angebote storniert hat. |
| Anbietername | Anbietername | Der Name des Anbieters, der an der Beziehung zwischen Microsoft und dem Kunden beteiligt ist. Beim Lizenztyp „Enterprise über Handelspartner“ ist dies der Handelspartner. Wenn ein Cloud Solution Provider (CSP) beteiligt ist, wird hier der CSP angegeben. |
| Anbieter-E-Mail | Anbieter-E-Mail | Die E-Mail-Adresse des Anbieters, der an der Beziehung zwischen Microsoft und dem Kunden beteiligt ist. Beim Lizenztyp „Enterprise über Handelspartner“ ist dies der Handelspartner. Wenn ein Cloud Solution Provider (CSP) beteiligt ist, wird hier der CSP angegeben. |
| FirstName | Vorname des Kunden | Der vom Kunden angegebene Vorname. Der Name kann sich von dem im Azure-Abonnement eines Kunden angegebenen Namen unterscheiden. |
| LastName | Nachname des Kunden | Der vom Kunden angegebene Nachname. Der Name kann sich von dem im Azure-Abonnement eines Kunden angegebenen Namen unterscheiden. |
| Email | E-Mail-Adresse des Kunden | Die vom Endkunden angegebene E-Mail-Adresse. Die E-Mail-Adresse kann sich von der im Azure-Abonnement eines Kunden angegebenen E-Mail-Adresse unterscheiden. |
| Firmenname des Kunden | Firmenname des Kunden | Der vom Kunden angegebene Firmenname. Der Name kann sich von der Angabe im Azure-Abonnement eines Kunden unterscheiden. |
| CustomerCity | Kundenort | Der Name des vom Kunden angegebenen Orts. Der Ort kann sich von der Angabe im Azure-Abonnement eines Kunden unterscheiden. |
| Postleitzahl des Kunden | Postleitzahl des Kunden | Die vom Kunden angegebene Postleitzahl. Die Postleitzahl kann sich von der im Azure-Abonnement eines Kunden angegebenen Postleitzahl unterscheiden. |
| CustomerCommunicationCulture | Kommunikationssprache des Kunden | Die vom Kunden für die Kommunikation bevorzugte Sprache. |
| CustomerCountryRegion | Land/Region des Kunden | Der vom Kunden angegebene Name für das Land bzw. die Region. Das Land/die Region kann sich von dem im Azure-Abonnement eines Kunden angegebenen Land bzw. der angegebenen Region unterscheiden. |
| AzureLicenseType | Azure-Lizenztyp | Die Art der Lizenzvereinbarung, über die Kunden Azure erwerben. Wird auch als _Kanal_ bezeichnet. Mögliche Werte sind:<ul><li>Cloud Solution Provider</li><li>Enterprise</li><li>Enterprise über Handelspartner</li><li>Nutzungsbasierte Bezahlung</li></ul> |
| PromotionalCustomers | Zustimmung zur Kontaktaufnahme zu Werbezwecken | Der Wert gibt an, ob der Kunde proaktiv der Kontaktaufnahme zu Werbezwecken durch die Herausgeber zugestimmt hat. Derzeit wird diese Option für Kunden nicht angezeigt. Daher ist allgemein „Nein“ angegeben. Sobald diese Funktion bereitgestellt wird, wird eine entsprechende Aktualisierung vorgenommen. |
| CustomerState | Bundesland/Kanton des Kunden | Das vom Kunden als Wohnsitz angegebene Bundesland oder der angegebene Kanton. Das Bundesland bzw. der Kanton kann sich von der Angabe im Azure-Abonnement eines Kunden unterscheiden. |
| CommerceRootCustomer | Handelsstammkunde | Eine Abrechnungskonto-ID kann mehreren Kunden-IDs zugeordnet werden.<br>Eine Kombination aus Abrechnungskonto-ID und Kunden-ID kann mehreren Abonnements für den kommerziellen Marketplace zugeordnet werden.<br>Der Handelsstammkunde ist der Name des Kunden, der dem Abonnement zugeordnet ist. |
| Customer Id | Customer ID | Der eindeutige, einem Kunden zugewiesene Bezeichner. Ein Kunde kann keine oder mehrere Azure Marketplace-Abonnements besitzen. |
| Billing Account Id | ID des Abrechnungskontos | Der Bezeichner des Kontos, für das die Abrechnung generiert wird. Ordnen Sie die **ID des Abrechnungskontos** zu **customerID** zu, um Ihren Auszahlungstransaktionsbericht mit den Kunden-, Bestellungs- und Verbrauchsberichten zu verbinden. |
||||

### <a name="customers-page-filters"></a>Filter auf der Seite „Kunden“

Die Filter auf der Seite Kunden werden auf der Ebene der Seite „Kunden“ angewandt. Sie können mehrere Filter auswählen, um das Diagramm nach den gewünschten Anzeigekriterien und den gewünschten Daten im Raster/Export „Daten zu detaillierten Aufträgen“ zu rendern. Filter werden auf die Daten angewandt, die für den Monatsbereich extrahiert werden, den Sie in der rechten oberen Ecke der Seite „Kunden“ ausgewählt haben.

> [!TIP]
> Sie können das Downloadsymbol in der oberen rechten Ecke eines Widgets verwenden, um die Daten herunterzuladen. Sie können Feedback zu jedem der Widgets geben, indem Sie auf das Symbol „Daumen nach oben“ oder „Daumen nach unten“ klicken.

## <a name="next-steps"></a>Nächste Schritte

- Eine Übersicht über die im kommerziellen Marketplace verfügbaren Analyseberichte finden Sie unter [Zugreifen auf Analysen für den kommerziellen Marketplace in Partner Center](./partner-center-portal/analytics.md).
- Informationen zu Diagrammen, Trends und Werten von aggregierten Daten, mit deren Hilfe Marketplace-Aktivitäten für Ihr Angebot zusammengefasst werden, finden Sie unter [Dashboard „Zusammenfassung“ in Analysen für den kommerziellen Marketplace](./summary-dashboard.md).
- Informationen zu Ihren Aufträgen in einem grafischen und herunterladbaren Format finden Sie unter [Dashboard „Aufträge“ in Analysen für den kommerziellen Marketplace](./orders-dashboard.md).
- Informationen zu Metriken zur Nutzung und zur getakteten Abrechnung für VM-Angebote finden Sie unter [Dashboard „Nutzung“ in Analysen für den kommerziellen Marketplace](./usage-dashboard.md).
- Eine Liste Ihrer Downloadanforderungen der letzten 30 Tagen finden Sie unter [Dashboard „Downloads“ in Analysen für den kommerziellen Marketplace](./partner-center-portal/downloads-dashboard.md).
- Eine konsolidierte Ansicht des Kundenfeedbacks für Angebote im Azure Marketplace und in Microsoft AppSource finden Sie unter [Analyse-Dashboard „Bewertungen und Rezensionen“ in Partner Center](./partner-center-portal/ratings-reviews.md).
- Häufig gestellte Fragen zu Analysen für den kommerziellen Marketplace und ein umfassendes Wörterbuch mit Datenbegriffen finden Sie unter [Terminologie und häufig gestellte Fragen zu Analysen für den kommerziellen Marketplace](./analytics-faq.md).