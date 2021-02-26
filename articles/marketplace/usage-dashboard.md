---
title: Dashboard „Nutzung“ in Analysen für den kommerziellen Marketplace | Azure Marketplace
description: Erfahren Sie, wie Sie für Angebote, die im Azure Marketplace veröffentlicht wurden, auf alle Metriken zur Nutzung und zur getakteten Abrechnung zugreifen.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 11/09/2020
author: sayantanroy83
ms.author: sroy
ms.openlocfilehash: 9f798751065d4889569f4b798ac864342f3e54ef
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98934400"
---
# <a name="usage-dashboard-in-commercial-marketplace-analytics"></a>Dashboard „Nutzung“ in Analysen für den kommerziellen Marketplace

Dieser Artikel enthält Informationen zum Dashboard „Nutzung“ im Partner Center. In diesem Dashboard werden alle VM-Angebote mit Metriken zur normalisierten Nutzung, zur tatsächlichen Nutzung und zur Nutzung nach getakteter Abrechnung auf drei separaten Registerkarten angezeigt: Normalisierte Nutzung nach VM-Größe, Tatsächliche Nutzung nach VM-Größe und Nutzung nach getakteter Abrechnung.

Um auf das Dashboard „Nutzung“ im Partner Center zuzugreifen, wählen Sie unter **Kommerzieller Marketplace** die Optionen **[Analysieren](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)**  > **Nutzung** aus.

>[!NOTE]
> Ausführliche Definitionen der Analyseterminologie finden Sie unter [Analysen für den kommerziellen Marketplace: Häufig gestellte Fragen und Terminologie](./analytics-faq.md).

## <a name="usage-dashboard"></a>Dashboard „Nutzung“

Im Dashboard **Nutzung** im Menü **Analysieren** werden die aktuellen Aufträge für all Ihre SaaS (Software-as-a-Service)-Angebote angezeigt. Sie können grafische Darstellungen folgender Elemente anzeigen:

- Nutzungstrend
- Normalisierte Nutzung nach Angeboten
- Normalisierte Nutzung nach anderen Dimensionen: VM-Größe, Vertriebskanäle und Angebotstypen
- Nutzung nach Geografie
- Tabelle mit detaillierten Nutzungsdaten
- Filter für die Seite „Aufträge“

> [!NOTE]
> Die maximale Latenz zwischen der Generierung von Nutzungsereignissen und der Berichterstattung im Partner Center beträgt 48 Stunden.

## <a name="elements-of-the-usage-dashboard"></a>Elemente des Dashboards „Nutzung“

In den folgenden Abschnitten wird beschrieben, wie das Dashboard „Nutzung“ verwendet wird und wie die Daten gelesen werden.

### <a name="month-range"></a>Monatsbereich

Oben rechts auf jeder Seite finden Sie eine Monatsbereichsauswahl. Passen Sie die Ausgabe der Diagramme auf der Seite **Nutzung** an, indem Sie einen Monatsbereich basierend auf den letzten 6 oder 12 Monaten oder einen benutzerdefinierten Monatsbereich mit einer maximalen Dauer von 12 Monaten auswählen. Der standardmäßige Monatsbereich (Berechnungszeitraum) beträgt sechs Monate.

:::image type="content" source="./media/usage-dashboard/month-filter.png" alt-text="Veranschaulicht die Monatsfilter im Dashboard „Nutzung“":::

### <a name="usage-trend"></a>Nutzungstrend

In diesem Abschnitt finden Sie die gesamten Nutzungsstunden und einen Trend für alle Angebote, die von Ihren Kunden während des ausgewählten Berechnungszeitraums verbraucht wurden. Metriken und Wachstumstrends werden durch ein Liniendiagramm dargestellt. Sie können den Wert für jeden Monat anzeigen, indem Sie auf die Linie im Diagramm zeigen. Der unter den Nutzungsmetriken im Widget angegebene Prozentwert stellt den prozentualen Zuwachs bzw. Rückgang im ausgewählten Berechnungszeitraum dar.

Die Nutzungsstunden können auf zwei Weisen dargestellt werden: als normalisierte VM-Nutzung und als tatsächliche VM-Nutzung.

- Normalisierte Nutzungsstunden werden als Nutzungsstunden definiert, die normalisiert wurden, um die Anzahl der VM-Kerne ([Anzahl der VM-Kerne] ·x [Stunden der Rohdatennutzung]) zu berücksichtigen. Für VMs, die als „SHAREDCORE“ festgelegt wurden, wird der Multiplikator 1/6 (oder 0,1666) für [Anzahl der VM-Kerne] verwendet.
- Die tatsächlichen Nutzungsstunden werden als die Zeitspanne (in Stunden) definiert, in der VMs ausgeführt wurden.

[![Veranschaulicht die Daten für die normalisierte Nutzung und die tatsächliche Nutzung im Dashboard „Nutzung“](./media/usage-dashboard/normalized-usage.png)](./media/usage-dashboard/normalized-usage.png#lightbox)

### <a name="normalized-usage-by-offers"></a>Normalisierte Nutzung nach Angeboten

In diesem Abschnitt sind die gesamten Nutzungsstunden und ein Trend für Ihre nutzungsbasierten Angebote im Azure Marketplace angegeben. Das Diagramm für die normalisierte Nutzung nach Angeboten wird unten beschrieben.

- Im gestapelten Säulendiagramm **Normalisierte Nutzung nach Angeboten** sind die Stunden für die normalisierte Nutzung entsprechend dem ausgewählten Berechnungszeitraum für die fünf Topangebote aufgeschlüsselt. Die fünf Topangebote werden in einem Diagramm angezeigt, während die übrigen unter der Kategorie für sonstige Angebote gruppiert sind.
- Im gestapelten Säulendiagramm wird der Wachstumstrend für den ausgewählten Datumsbereich nach Monaten angezeigt. In den Monatsspalten sind die Nutzungsstunden aus den Angeboten mit den meisten Nutzungsstunden für den jeweiligen Monat dargestellt. Im Liniendiagramm wird der prozentuale Wachstumstrend auf der sekundären Y-Achse dargestellt.
- Sie können bestimmte Angebote in der Legende auswählen, um nur diese Angebote im Diagramm anzuzeigen.

:::image type="content" source="./media/usage-dashboard/normalized-usage-offers.png" alt-text="Veranschaulicht die Angebotsdaten für die normalisierte Nutzung im Dashboard „Nutzung“":::

Sie können ein beliebiges Angebot und maximal drei SKUs dieses Angebots auswählen, um den Nutzungstrend für das Angebot und die ausgewählten SKUs nach Monaten anzuzeigen.

:::image type="content" source="./media/usage-dashboard/normalized-usage-offers-sku.png" alt-text="Veranschaulicht die Angebots- und SKU-Daten für die normalisierte Nutzung im Dashboard „Nutzung“":::

### <a name="orders-by-offers-and-skus"></a>Aufträge nach Angeboten und SKUs

Im Diagramm **Aufträge nach Angeboten und SKUs** werden die Measures und Trends aller Angebote angezeigt. Beachten Sie Folgendes:

- Die Topangebote werden im Diagramm angezeigt, während die übrigen unter der Kategorie für sonstige Angebote gruppiert sind.
- Sie können bestimmte Angebote in der Legende auswählen, um nur diese Angebote im Diagramm anzuzeigen.
- Wenn Sie mit der Maus auf ein Segment im Diagramm zeigen, wird die Anzahl der Bestellungen und der Prozentsatz dieses Angebots im Vergleich zur Gesamtanzahl der Bestellungen für alle Ihre Angebote angezeigt.
- Der **Trend „Aufträge nach Angeboten“** zeigt Wachstumstrends nach Monaten an. Die Spalte „Monat“ gibt die Anzahl der Bestellungen nach Angebotsname an. Im Liniendiagramm ist der prozentuale Wachstumstrend auf der z-Achse dargestellt.

:::image type="content" source="./media/usage-dashboard/orders-by-offers-and-skus.png" alt-text="Veranschaulicht das Diagramm „Aufträge nach Angeboten und SKUs“ im Dashboard „Nutzung“":::

Sie können ein beliebiges Angebot und maximal drei SKUs dieses Angebots auswählen, um den Trend für das Angebot, die SKUs und die Arbeitsplätze nach Monaten anzuzeigen.

:::image type="content" source="./media/usage-dashboard/orders-by-offers-and-skus-2.png" alt-text="Veranschaulicht das Diagramm „Aufträge nach Angeboten und SKUs“ im Dashboard „Nutzung“. Die Trends für das Angebot, die SKUs und die Arbeitsplätze werden angezeigt.":::

#### <a name="normalized-usage-by-other-dimensions-vm-size-sales-channels-and-offer-type"></a>Normalisierte Nutzung nach anderen Dimensionen: VM-Größe, Vertriebskanäle und Angebotstyp

Es gibt drei Registerkarten für die Dimensionen: „VM-Größe“, „Vertriebskanal“ und „Angebotstyp“. Sie können die Nutzungsmetriken und die monatlichen Trends für jede dieser Dimensionen anzeigen.

:::image type="content" source="./media/usage-dashboard/normalized-usage-other-dimensions.png" alt-text="Veranschaulicht das Diagramm „Normalisierte Nutzung nach anderen Dimensionen“ im Dashboard „Nutzung“":::

### <a name="usage-by-geography"></a>Nutzung nach Geografie

Im Wärmebild wird für den ausgewählten Berechnungszeitraum die gesamte Nutzung für die Geography-Dimension angezeigt. Die Helligkeit der Farbe auf der Karte entspricht einer niedrigen bis hohen Anzahl von Kunden. Wählen Sie einen Datensatz in der Tabelle aus, um ein Land/eine Region zu vergrößern.

:::image type="content" source="./media/usage-dashboard/normalized-usage-country.png" alt-text="Veranschaulicht das Diagramm für die normalisierte Nutzung nach dem Land im Dashboard „Nutzung“":::

Beachten Sie Folgendes:

- Sie können die Karte verschieben, um den genauen Standort anzuzeigen.
- Sie können an einen bestimmten Ort zoomen.
- Das Wärmebild verfügt über ein zusätzliches Raster, um Details zur Anzahl der Kunden oder Aufträge und zu den Stunden für die normalisierte Nutzung an einem bestimmten Ort anzuzeigen.
- Sie können ein Land oder eine Region im Raster suchen und auswählen, um an den Ort in der Karte zu zoomen. Um wieder zur ursprünglichen Ansicht zurückzukehren, wählen Sie in der Karte die Schaltfläche **Startseite** aus.

### <a name="usage-details-table"></a>Tabelle „Nutzungsdetails“

In der Tabelle **Nutzungsdetails** wird eine nummerierte Liste der 1.000 Top-Verwendungseinträge nach Nutzung sortiert angezeigt. Beachten Sie Folgendes:

- Jede Spalte im Raster ist sortierbar.
- Die Daten können in eine TSV- oder CSV-Datei extrahiert werden, wenn die Anzahl der Einträge unter 1.000 liegt.
- Liegt die Anzahl der Einträge über 1.000, werden die exportierten Daten asynchron auf einer Downloadseite abgelegt, die für die nächsten 30 Tage verfügbar ist.
- Wenden Sie Filter auf **detaillierte Nutzungsdaten** an, um nur die Daten anzuzeigen, die für Sie von Interesse sind. Filtern Sie Daten nach Land/Region, Vertriebskanal, Marketplace-Lizenztyp, Nutzungstyp, Angebotsname, Angebotstyp, kostenlosen Testversionen, Marketplace-Abonnement-ID, Kunden-ID und Firmenname.

_**Tabelle 1: Wörterbuch mit Datenbegriffen**_

| Spaltenname | Attributname | Definition |
| ------------ | ------------- | ------------- |
| Marketplace Subscription Id | Marketplace-Abonnement-ID | Der eindeutige Bezeichner (ID), der mit dem Azure-Abonnement verknüpft ist, über das der Kunde Ihr Angebot im kommerziellen Marketplace erworben hat. Früher war ID die Azure-Abonnement-GUID. |
| MonthStartDate | Monatliches Startdatum | Das monatliche Startdatum steht für den Monat des Kaufs. |
| Angebotstyp | Angebotstyp | Der Typ des Angebots im kommerziellen Marketplace |
| Azure-Lizenztyp | Azure-Lizenztyp | Die Art der Lizenzvereinbarung, über die Kunden Azure erwerben. Wird auch als „Kanal“ bezeichnet. Mögliche Werte sind:<ui><li>Cloud Solution Provider</li><li>Enterprise</li><li>Enterprise über Handelspartner</li><li>Nutzungsbasierte Bezahlung</li></ul> |
| Marketplace-Lizenztyp | Marketplace-Lizenztyp | Die Abrechnungsmethode für das Angebot im kommerziellen Marketplace. Mögliche Werte sind:<ul><li>Abrechnung über Azure</li><li>BYOL (Bring Your Own License)</li><li>Kostenlos</li><li>Microsoft als Handelspartner</li></ul> |
| SKU | SKU | Der dem Angebot zugeordnete Plan |
| Land des Kunden | Land/Region des Kunden | Der vom Kunden angegebene Name für das Land bzw. die Region. Das Land/die Region kann sich von dem im Azure-Abonnement eines Kunden angegebenen Land bzw. der angegebenen Region unterscheiden. |
| Vorschau-SKU | Vorschau-SKU | Der Wert gibt an, ob Sie die SKU als „Vorschau“ gekennzeichnet haben. Der Wert ist „Ja“, wenn Sie die SKU entsprechend gekennzeichnet haben und nur von Ihnen autorisierte Azure-Abonnements dieses Image bereitstellen und nutzen können. Der Wert ist „Nein“, wenn die SKU nicht als „Vorschau“ definiert wurde. |
| SKU-Abrechnungstyp | SKU-Abrechnungstyp | Der Abrechnungstyp, der den einzelnen SKUs im Angebot zugeordnet ist. Mögliche Werte sind:<ul><li>Kostenlos</li><li>Kostenpflichtig</li></ul> |
| IsInternal | Als veraltet markiert | Als veraltet markiert |
| Größe des virtuellen Computers | VM-Größe | Bei VM-basierten Angebotstypen gibt diese Entität die Größe des virtuellen Computers an, der der SKU des Angebots zugeordnet ist. |
| Cloudinstanzname | Cloudinstanzname | Die Microsoft Cloud, in der die Bereitstellung eines virtuellen Computers erfolgt ist. |
| ServicePlanName | Als veraltet markiert | Veraltet (dieselbe Definition wie „SKU“) |
| Angebotsname | Angebotsname | Der Name des Angebots im kommerziellen Marketplace. |
| DeploymentMethod | Als veraltet markiert | Veraltet (dieselbe Definition wie „Angebotstyp“)
 |
| Firmenname des Kunden | Firmenname des Kunden | Der vom Kunden angegebene Firmenname. Der Name kann sich von der Angabe im Azure-Abonnement eines Kunden unterscheiden. |
| Usage Date | Usage Date | Das Datum der Generierung von Nutzungsereignissen für verwendungsbasierte Ressourcen |
| IsMultisolution | Entspricht mehreren Lösungen | Gibt an, ob der Angebotstyp mehrere Lösungen umfasst. |
| Neuer Kunde | Als veraltet markiert | Als veraltet markiert |
| Kerngröße | Kerngröße | Die Anzahl der Kerne, die dem VM-basierten Angebot zugeordnet sind. |
| Nutzungstyp | Nutzungstyp | Gibt an, ob das dem Angebot zugeordnete Nutzungsereignis einem der folgenden Typen entspricht:<ul><li>Normalisierte Nutzung</li><li>Tatsächliche Nutzung</li><li>Nutzungsbezogene Gebühren</li></ul> |
| Enddatum der Testversion | Enddatum der Testversion | Das Datum, an dem der Testzeitraum für die Bestellung endet oder beendet ist. |
| Währung des Kunden (Customer Currency, CC) | Währung des Kunden | Die vom Kunden für die Transaktion im kommerziellen Marketplace verwendete Währung |
| Preis (CC) | Preis | Der Einzelpreis der SKU, der in der Kundenwährung angezeigt wird |
| Auszahlungswährung (PC) | Auszahlungswährung | Der Herausgeber wird für die Nutzungsereignisse, die der Ressource zugeordnet sind, in der vom Herausgeber konfigurierten Währung bezahlt. |
| Geschätzter Preis (PC) | Geschätzter Preis | Der Einzelpreis der SKU in der vom Herausgeber konfigurierten Währung |
| Nutzungsreferenz | Nutzungsreferenz | Eine verkettete GUID, die verwendet wird, um den Nutzungsbericht (in Analysen für den kommerziellen Marketplace) mit dem Bericht für Auszahlungstransaktionen zu verbinden. Die Nutzungsreferenz ist mit „OrderId“ und „LineItemId“ im Bericht für Auszahlungstransaktionen verbunden. |
| Usage Unit | Nutzungseinheit | Die der SKU zugeordnete Verbrauchseinheit |
| Customer Id | Customer ID | Der eindeutige, einem Kunden zugewiesene Bezeichner. Ein Kunde kann keine oder mehrere Azure Marketplace-Abonnements besitzen. |
| Billing Account Id | ID des Abrechnungskontos | Der Bezeichner des Kontos, für das die Abrechnung generiert wird. Ordnen Sie die **ID des Abrechnungskontos** zu **customerID** zu, um Ihren Auszahlungstransaktionsbericht mit den Kunden-, Bestellungs- und Verbrauchsberichten zu verbinden. |
| Verwendungsmenge | Verwendungsmenge | Die gesamten Nutzungseinheiten, die von der Ressource verbraucht wurden, die vom Kunden bereitgestellt wird.<br>Diese basieren auf dem Element „Nutzungstyp“. Wenn der Nutzungstyp z. B. die normalisierte Nutzung ist, dann entspricht die Verwendungsmenge der normalisierten Nutzung. |
| NormalizedUsage | Normalisierter Verbrauch | Die gesamten Einheiten für die normalisierte Nutzung, die von der Ressource verbraucht wurden, die vom Kunden bereitgestellt wird.<br>Normalisierte Nutzungsstunden werden als Nutzungsstunden definiert, die normalisiert wurden, um die Anzahl der VM-Kerne ([Anzahl der VM-Kerne] ·x [Stunden der Rohdatennutzung]) zu berücksichtigen. Für VMs, die als „SHAREDCORE“ festgelegt wurden, wird der Multiplikator 1/6 (oder 0,1666) für [Anzahl der VM-Kerne] verwendet. |
| MeteredUsage | Gemessene Nutzung | Die gesamten Nutzungseinheiten, die von den Verbrauchseinheiten beansprucht werden, die mit dem vom Kunden bereitgestellten Angebot konfiguriert wurden. |
| RawUsage | Rohnutzung | Die gesamten Einheiten für die tatsächliche Nutzung, die von der Ressource verbraucht wurden, die vom Kunden bereitgestellt wird.<br>Die Stunden für die tatsächliche Nutzung werden als die Zeitspanne (in Nutzungseinheiten) definiert, in der VMs ausgeführt wurden. |
| Geschätzte erweiterte Gebühren (CC) | Geschätzte erweiterte Gebühren in der Kundenwährung | Gibt die mit der Nutzung verbundenen Gebühren an. Die Spalte entspricht dem rechnerischen Produkt aus Preis (CC) und Verwendungsmenge. |
| Geschätzte erweiterte Gebühren (PC) | Geschätzte erweiterte Gebühren in der Auszahlungswährung | Gibt die mit der Nutzung verbundenen Gebühren an. Die Spalte entspricht dem rechnerischen Produkt aus geschätztem Preis (PC) und Verwendungsmenge. |
||||

### <a name="usage-page-filters"></a>Filter für die Seite „Nutzung“

Filter für die Seite **Nutzung** werden auf der Ebene der Seite „Aufträge“ angewendet. Sie können einen oder mehrere Filter auswählen, um das Diagramm nach den gewünschten Anzeigekriterien und den gewünschten Daten im Raster/Export für Nutzungs-/Auftragsdaten zu rendern. Filter werden auf die Daten angewendet, die für den in der rechten oberen Ecke der Seite „Nutzung“ von Ihnen ausgewählten Monatsbereich extrahiert wurden.

Die Widgets und der Exportbericht für die tatsächliche VM-Nutzung sind abgesehen von den folgenden Unterschieden mit der normalisierten VM-Nutzung vergleichbar:

- Normalisierte Nutzungsstunden werden als Nutzungsstunden definiert, die normalisiert wurden, um die Anzahl der VM-Kerne ([Anzahl der VM-Kerne] ·x [Stunden der Rohdatennutzung]) zu berücksichtigen. Für VMs, die als „SHAREDCORE“ festgelegt wurden, wird der Multiplikator 1/6 (oder 0,1666) für [Anzahl der VM-Kerne] verwendet.
- Die Stunden für die tatsächliche Nutzung werden als die Zeitspanne (in Nutzungseinheiten) definiert, in der VMs ausgeführt wurden.

### <a name="metered-billing-usage"></a>Nutzung nach getakteter Abrechnung

Auf der Registerkarte **Gemessene Nutzung** werden Nutzungsinformationen für Angebotstypen angezeigt, wobei die Nutzung per Verbrauchseinheitsdimension gemessen wird. Derzeit wird die Überschreitung des SaaS-Angebotstyps dargestellt. Die Registerkarte enthält grafische Darstellungen von Überschreitungstrends für die SaaS-Nutzung nach getakteter Abrechnung:

- **Überschreitungstrend nach Verbrauchseinheitsdimension**: Zeigt den monatlichen Überschreitungstrend für die ausgewählte Verbrauchseinheitsdimension eines Angebots an. Die X-Achse stellt den Monat und die Y-Achse die Verwendungsmenge der Überschreitung dar. Die Maßeinheit der benutzerdefinierten Verbrauchseinheit wird ebenfalls auf der Y-Achse angezeigt.

    :::image type="content" source="./media/usage-dashboard/metered-usage.png" alt-text="Veranschaulicht das Diagramm für die normalisierte VM-Nutzung im Dashboard „Nutzung“":::

- **Überschreitungstrend nach SKU**: Stellt den Trend der Verwendungsmenge der ausgewählten Verbrauchseinheitsdimension nach SKUs/Plänen dar. Es werden die fünf Top-Pläne mit der höchsten Nutzungsmenge für das ausgewählte Angebot angezeigt.

- **Überschreitungstrend nach Kunden**: Die Kundenbestenliste ist eine gestapelte Liste der Kunden mit den meisten Nutzungsstunden. Diese werden in einer _Bestenliste_ angezeigt und nach der höchsten Nutzung der benutzerdefinierten Verbrauchseinheit bewertet. Wählen Sie einen Kunden in der Bestenliste aus, um den Überschreitungstrend der Nutzung einer ausgewählten Verbrauchseinheitsdimension anzuzeigen.

    :::image type="content" source="./media/usage-dashboard/metered-usage-by-customers.png" alt-text="Veranschaulicht das Diagramm für die gemessene Nutzung nach Kunden im Dashboard „Nutzung“":::

Wenn Sie über mehrere Angebote verfügen, bei denen benutzerdefinierte Verbrauchseinheiten verwendet werden, werden im Nutzungsbericht für die getaktete Abrechnung Nutzungsinformationen für alle Angebote entsprechend ihren Dimensionen für benutzerdefinierte Verbrauchseinheiten angezeigt.

> [!TIP]
> Sie können das Downloadsymbol in der oberen rechten Ecke eines Widgets verwenden, um die Daten herunterzuladen. Sie können Feedback zu jedem der Widgets geben, indem Sie auf das Symbol „Daumen nach oben“ oder „Daumen nach unten“ klicken.

## <a name="next-steps"></a>Nächste Schritte

- Eine Übersicht über die im kommerziellen Marketplace verfügbaren Analyseberichte finden Sie unter [Zugreifen auf Analysen für den kommerziellen Marketplace in Partner Center](./partner-center-portal/analytics.md).
- Informationen zu Diagrammen, Trends und Werten von aggregierten Daten, mit deren Hilfe Marketplace-Aktivitäten für Ihr Angebot zusammengefasst werden, finden Sie unter [Dashboard „Zusammenfassung“ in Analysen für den kommerziellen Marketplace](./summary-dashboard.md).
- Informationen zu Ihren Aufträgen in einem grafischen und herunterladbaren Format finden Sie unter [Dashboard „Aufträge“ in Analysen für den kommerziellen Marketplace](./orders-dashboard.md).
- Informationen zu Metriken zur Nutzung und zur getakteten Abrechnung für VM-Angebote finden Sie unter [Dashboard „Nutzung“ in Analysen für den kommerziellen Marketplace](usage-dashboard.md).
- Eine Liste Ihrer Downloadanforderungen der letzten 30 Tagen finden Sie unter [Dashboard „Downloads“ in Analysen für den kommerziellen Marketplace](./partner-center-portal/downloads-dashboard.md).
- Eine konsolidierte Ansicht des Kundenfeedbacks für Angebote im Azure Marketplace und in Microsoft AppSource finden Sie unter [Analyse-Dashboard „Bewertungen und Rezensionen“ in Partner Center](./partner-center-portal/ratings-reviews.md).
- Häufig gestellte Fragen zu Analysen für den kommerziellen Marketplace und ein umfassendes Wörterbuch mit Datenbegriffen finden Sie unter [Terminologie und häufig gestellte Fragen zu Analysen für den kommerziellen Marketplace](./analytics-faq.md).
